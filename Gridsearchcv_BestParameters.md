# Setting up a gridsearchcv to look for the best parameter inputs for the neural network
For this step, the grid will be running through various combinations of learning rate, batch size and epochs to minimize loss and define the best parameters for the Qlim prediction model. For the code below, sklearn was used to search and evaluate each combination. Externally, a log file was created, ranking every interaction by mean test scores, which were defined by R² values. 

```markdown
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

X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.2)

n_features = X_train.shape[1]

################## Defining ann

def make_regression_ann(Optimizer_trial, learning_rate):
    model = Sequential()
    initializer = tf.compat.v1.keras.initializers.he_normal(seed=1800)
    model.add(Dense(15, input_dim=6, input_shape=(n_features,), kernel_initializer=initializer, activation='relu', activity_regularizer=l1(0.00001)))
    model.add(Dense(15, activation='relu', kernel_initializer=initializer))
    model.add(Dense(16, activation='relu', kernel_initializer=initializer))
    model.add(Dense(16, activation='relu', kernel_initializer=initializer))
    model.add(Dense(15, activation='relu', kernel_initializer=initializer))
    model.add(Dense(15, activation='relu', kernel_initializer=initializer))
    model.add(Dense(1))

    opt = tf.keras.optimizers.Adam(learning_rate=learning_rate, beta_1=0.9, beta_2=0.999, epsilon=1e-07)
    model.compile(loss='mean_squared_logarithmic_error', optimizer=opt, metrics=['mse', 'mae', 'mape'])
    return model

##################### Importing modules for gridsearch

from sklearn.model_selection import GridSearchCV
from keras.wrappers.scikit_learn import KerasRegressor

#########################

#print("Best: %f using %s" % (GridSearchResults.best_score_,GridSearchResults.best_params_))
#means = GridSearchResults.cv_results_['mean_test_score']
#stds = GridSearchResults.cv_results_['std_test_score']
#params = GridSearchResults.cv_results_['params']
#for mean, stdev, param in zip(means, stds, params):
#    print("%f (%f) with: %r" % (mean, stdev, param))
######################## PRINTING RESULTS TO A LOG FILE

import sys

old_stdout = sys.stdout
log_file = open("cv.log","w")
sys.stdout = log_file

######################## Defining testing parameters to try
learning_rate = [0.005091, 0.001, 0.01, 0.006, 0.007]

Parameter_Trials={'batch_size':[1500, 1750, 2000, 2500, 3000, 3500, 4000],
                  'epochs':[2500, 3000, 3500, 4000, 4500, 5000, 7500, 8000, 9000],
                  'Optimizer_trial': ['adam'],
                  'learning_rate': learning_rate}

######################## Creating the regression ANN model

RegModel=KerasRegressor(make_regression_ann, verbose=10)

######################## Defining a custom function to calculate accuracy

from sklearn.metrics import make_scorer
def Accuracy_Score(orig,pred):
    R2 = sklearn.metrics.r2_score(orig,pred)
    print('#'*70,'R2:', R2)
    return(R2)

custom_Scoring=make_scorer(Accuracy_Score, greater_is_better=True)

######################## Creating the Grid search space
######################## Setting up gridsearch and cross-validating with kfold

kfold = KFold(n_splits=5, random_state=1200, shuffle=True)
grid_search=GridSearchCV(estimator=RegModel,
                         param_grid=Parameter_Trials,
                         scoring=custom_Scoring,
                         n_jobs=1,
                         cv=kfold,
                         verbose=10,
                         return_train_score=True)

######################## Running Grid Search for different paramenters

GridSearchResults=grid_search.fit(X_train, Y_train, verbose=1)

######################## Printing results and organizing them

print('### Printing Best parameters ###\n')

import tabulate

####################### Dataframing parameters

#print('Parameters by case\n')
#dataset = GridSearchResults.cv_results_['params']
#header = dataset[0].keys()
#rows =  [x.values() for x in dataset]
#print (tabulate.tabulate(rows, header))

######################## Dataframing ranking parameters

print('\nParameter rankings\n')
result_summary = pd.DataFrame(GridSearchResults.cv_results_)
result_summary = result_summary.sort_values(by=["rank_test_score"])
summary=result_summary[["params", "rank_test_score", "mean_test_score", "mean_train_score", "std_test_score"]]

print (tabulate.tabulate(summary, headers='keys'))

######################## Dataframing the best result 

print('\nBest case\n')

bestcase = pd.DataFrame({'Batch size': GridSearchResults.best_params_["batch_size"], 
                         'Epochs': GridSearchResults.best_params_["epochs"],
                         'Learning Rate': GridSearchResults.best_params_["learning_rate"],
                         "Optimizer_trial": GridSearchResults.best_params_["Optimizer_trial"]}, index=[1])
print(tabulate.tabulate(bestcase, headers=["n", "batch size", "epochs", "optimizer"]))

######################## Closing log

print('\nFinished! Check log file.')

sys.stdout = old_stdout
log_file.close()
```
