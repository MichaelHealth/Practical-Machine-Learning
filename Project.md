
Practical Machine Learning Project
Introduction
The data for this project comes form 6 participants in a study who perform weightlifting exercises with accelerometers on the 


For this project, we are given data from accelerometers on the belt, forearm, arm, and dumbell of 6 research study participants. Our training data consists of accelerometer data and a label identifying the quality of the activity the participant was doing. Our testing data consists of accelerometer data without the identifying label. Our goal is to predict the labels for the test set observations.
The data for this project come from this source: http://groupware.les.inf.puc-rio.br/har.

This is the code I used to make the calculation


Data Preparation
I load the caret package, and read in the training and testing data:

library(caret)
## Loading required package: lattice
## Loading required package: ggplot2
ptrain <- read.csv("pml-training.csv")
ptest <- read.csv("pml-testing.csv")
Because I want to be able to estimate the out-of-sample error, I randomly split the full training data (ptrain) into a smaller training set (ptrain1) and a validation set (ptrain2):
