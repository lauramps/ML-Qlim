# Running the ann with final defined parameters
## Changes from first setup
For this part, the model ran a few times for the parameters chosen by gridsearchcv, while slowly tuning the parameters and layer sizes until a sactisfactory R² value was reached. 7 input layers and 1 output layer were used, with 10500 epochs and a batch size of 3000. A log file with the information for the interactions and accuracy values was generated at the end.

```
########### Importing modules
import numpy as np
import pandas as pd
import tensorflow as tf
from numpy import sqrt
import sklearn
from sklearn.model_selection import cross_val_score
from sklearn.model_selection import KFold
from sklearn.preprocessing import StandardScaler
from sklearn.pipeline import Pipeline
from sklearn.model_selection import train_test_split
import keras
import keras.optimizers
import keras.initializers
from keras.regularizers import l1
from keras.regularizers import l2
from keras.layers import Dropout
from keras.wrappers.scikit_learn import KerasRegressor
from keras.models import Sequential
from keras.layers import Dense
from tensorflow.keras import Sequential
from tensorflow.keras.layers import Dense

################## Pulling and cleaning data from excel file

df = pd.read_excel("C:\\Users\\Laura\\Documents\\Training.xlsx")
training=["D", "L", "H", "WM", "NE", "EP"]
data=df[training]
output=["QM"]
final=df[output]

X = data.values
Y = final.values

X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.15)

n_features = X_train.shape[1]

################## Defining ann

model = Sequential()
initializer = tf.compat.v1.keras.initializers.he_normal(seed=1800)
model.add(Dense(155, input_dim=6, input_shape=(n_features,), kernel_initializer=initializer, activation='relu', activity_regularizer=l1(0.00001)))
model.add(Dense(155, activation='relu', kernel_initializer=initializer))
model.add(Dense(156, activation='relu', kernel_initializer=initializer))
model.add(Dense(16, activation='relu', kernel_initializer=initializer))
model.add(Dense(15, activation='relu', kernel_initializer=initializer))
model.add(Dense(15, activation='relu', kernel_initializer=initializer))
model.add(Dense(25, activation='relu', kernel_initializer=initializer))
model.add(Dense(1))

opt = tf.keras.optimizers.Adam(learning_rate=0.007, beta_1=0.9, beta_2=0.999, epsilon=1e-08)
model.compile(loss='mean_squared_logarithmic_error', optimizer=opt, metrics=['mse', 'mae', 'mape'])
model.fit(X_train, Y_train, epochs=10500, batch_size=3000, verbose=2)

########################

error = model.evaluate(X_test, Y_test, verbose=0)
prediction = model.predict(X_test)

########################test
for i in range(57):
    print(f"{i+1}. y_true: {Y_test[i]}, y_pred: {prediction[i]}")

print('############## TESTING VALUES')

prediction2=model.predict(X)
for i in range(375):
    print(f"{i+1}. y_true: {Y[i]}, y_pred: {prediction2[i]}")
    

R2 = sklearn.metrics.r2_score(Y, prediction2)
print(R2)

print('MSLE, MSE, MAE and MAPE are', error)

########################log
import sys

old_stdout = sys.stdout
log_file = open("neuraltesting.log","w")
sys.stdout = log_file

for i in range(57):
    print(f"{i+1}. y_true: {Y_test[i]}, y_pred: {prediction[i]}")

print('\n\n############## TESTING VALUES\n\n')

prediction2=model.predict(X)
for i in range(375):
    print(f"{i+1}. y_true: {Y[i]}, y_pred: {prediction2[i]}")
    

R2 = sklearn.metrics.r2_score(Y, prediction2)
print(R2)
print('MSLE, MSE, MAE and MAPE are', error)

sys.stdout = old_stdout
log_file.close()

print('\n\nFinished! Check log file.')
```
