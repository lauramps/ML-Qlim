# Artificial Neural Network model for Load Bearing Capacity preview 
This project is my thesis for my civil engineering course, projecting a formula for pile load bearing capacity by using machine learning tools. During the course of this project, a database of PDA results was collected and processed, filtering the variables for Diameter, Length, Hammer Fall Height, Hammer Weight, Set and Young's Modulus, returning a value for Qlim (load bearing capacity value measured in kN). 

## First steps
For optimization and commodity, the database was processed on the software JMP Pro, which returns a neural network model and formula for application. I've used the model as a basis for which parameters to aim for. After filtering outliers, 375 input cases were left. 
In Python, tensorflow, keras and sklearn were used to build, test and evaluate the model until it was able to succesfully predict the values for Qlim with minimal error. The present repository contains the data and the codes written to build and run the ann model, along with the results obtained. 

1. [Database](https://github.com/lauramps/ML-Qlim/blob/main/Database.md)
2. [First ann build](https://github.com/lauramps/ML-Qlim/blob/main/First_Ann_Test.md)
3. [Grid search run for best parametrers](https://github.com/lauramps/ML-Qlim/blob/main/Gridsearchcv_BestParameters.md)
4. [Grid search log: Best parameters ranked](https://github.com/lauramps/ML-Qlim/blob/main/BestParams_Log.md)
5. [Running the ann with the best parameters and fine tuning](https://github.com/lauramps/ML-Qlim/blob/main/Ann_Run_BestParameters.md)
6. [Comparing predictions to real results: Final evaluation](https://github.com/lauramps/ML-Qlim/blob/main/RealxPredicted.md)
