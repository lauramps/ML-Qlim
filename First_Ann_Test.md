## First ann model - defining the basics

Having the database defined, the ann will be set up by using tensorflow. The data was split into test and train sets, and a Sequential model was created with 4 input layers and one output layer, measuring error by mean squared error. For load bearing capacity, values can range from low to high values, but the database contains examples from the big majority of Qlim ranges - that way, it's expected that future input examples are within the test and train set. 

### Defining the ann

Code used for creating the ann is showed below.

```markdown
################## importing modules
import numpy as np
import pandas as pd
import tensorflow as tf
from numpy import sqrt
import keras
from keras.models import Sequential
from keras.layers import Dense
from sklearn.model_selection import train_test_split
from tensorflow.keras import Sequential

################## pulling and cleaning data from excel file
################## 6 columns for inputs (Diameter, Pile Length, Height of Hammer Fall, Weigth of Hammer, Set/10blows, Young's Modulus)
################## 1 column for output (Pile load bearing capacity)

df = pd.read_excel("C:\\Users\\Laura\\Documents\\Training.xlsx")
training=["D", "L", "H", "WM", "NE", "EP"]
data=df[training]
output=["QM"]
final=df[output]

X = data.values
Y = final.values

################## splitting test and training data

X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.15)
print(X_train.shape, X_test.shape, Y_train.shape, Y_test.shape)

n_features = X_train.shape[1]

################## defining base model

model = Sequential()
model.add(Dense(40, input_shape=(n_features,), kernel_initializer='he_normal', activation='relu'))
model.add(Dense(45, activation='relu', kernel_initializer='he_normal'))
model.add(Dense(45, activation='relu', kernel_initializer='he_normal'))
model.add(Dense(45, activation='relu', kernel_initializer='he_normal'))
model.add(Dense(1))

model.compile(loss='mean_squared_error', optimizer='adam')
model.fit(X_train, Y_train, epochs=500, batch_size=70, verbose=0)

################## defining inputs and targets for prediction tests

inputs = np.concatenate((X_train, X_test), axis=0)
targets = np.concatenate((Y_train, Y_test), axis=0)

print(model.summary())

################## evaluating model

error = model.evaluate(X_test, Y_test, verbose=0)
print('MSE: %.3f, RMSE: %.3f' % (error, sqrt(error)))

################## setting prediction example

row = [0.5, 18.7, 2, 35, 10, 28]
prediction = model.predict([row])
print('Predicted: %.3f' % prediction)
```

Having defined the model, the next step is to test for the best parameters for the network - a gridsearchcv will be utilized. 
