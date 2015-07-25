# Practical-Machine-Learning
This is for the Data Science Specialization from Coursera
Below is the code I used when creating the model, estimating the out-of-sample error, and making predictions. I also include a description of each step of the process.

Data Preparation
I load the caret package, and read in the training and testing data:

library(caret)
## Loading required package: lattice
## Loading required package: ggplot2
ptrain <- read.csv("pml-training.csv")
ptest <- read.csv("pml-testing.csv")
Because I want to be able to estimate the out-of-sample error, I randomly split the full training data (ptrain) into a smaller training set (ptrain1) and a validation set (ptrain2):

set.seed(10)
inTrain <- createDataPartition(y=ptrain$classe, p=0.7, list=F)
ptrain1 <- ptrain[inTrain, ]
ptrain2 <- ptrain[-inTrain, ]
I am now going to reduce the number of features by removing variables with nearly zero variance, variables that are almost always NA, and variables that don’t make intuitive sense for prediction. Note that I decide which ones to remove by analyzing ptrain1, and perform the identica
