
#Practical Machine Learning Project
###Introduction
#####The data for this project comes form 6 participants in a study who perform weightlifting exercises with accelerometers on their bodies and on the weights they are lifting.  The goal of the study was to be able to calssify the motions of the bodies and weights and identify each of the participants.  A training data set was created with the study subjects identities and a testing data was created without the subjects identified.  
The R script below uses analyzes the trianng data with random forest and is able to determone the study participants on the testing set.




>library(caret)

>ptrain <- read.csv("pml-training.csv")

ptest <- read.csv("pml-testing.csv")

#####Because I want to be able to estimate the out-of-sample error, I randomly split the full training data (ptrain) into a smaller training set (ptrain1) and a validation set (ptrain2):
This sets my home directory and loads the training and testing files into data frames

>library(caret)
setwd("C:/Users/Mike/Documents/testdir/ML")
training <- read.csv("pml-training.csv")
testing <- read.csv("pml-testing.csv")

# This breaks the training data into two partitions
set.seed(12345)
setTrain <- createDataPartition(y=training$classe, p=0.7, list=F)
training1 <- training[setTrain, ]
training2 <- training[-setTrain, ]

#This detects and removes variables which are close to zero 
zvar <- nearZeroVar(training1)
training1 <- training1[, -zvar]
training2 <- training2[, -zvar]

#This removes variables which have over 90% NA values

bigNA <- sapply(training1, function(x) mean(is.na(x))) > 0.90
training1 <- training1[, bigNA==F]
training2 <- training2[, bigNA==F]

# This removes the first five variables which have very little to do with prediction values
training1 <- training1[, -(1:5)]
training2 <- training2[, -(1:5)]

#This is using a Random Forest model to check on the accuracy. 
#Train is used on training1 with 3-fold cross validation.

weights <- trainControl(method="cv", number=3, verboseIter=TRUE)
dim(training1)
# weight model on training1
weight <- train(classe ~., data=training1, method="rf", trControl=weights)

weight$finalModel
#plot the output to see the impact the variables used in trianing1
plot(varImp(weight))

#Display the confusion matrix to measure performance of the 
#Random Forest on training1 versus training2

results <- predict(weight, newdata=training2)
confusionMatrix(training2$classe, results)

#remove the near zero variables from the training and testing sets
zvar <- nearZeroVar(training)
training <- training[, -zvar]
testing <- testing[, -zvar]

# remove variables that are over 90% NA's
bigNA <- sapply(training, function(x) mean(is.na(x))) > 0.90
training <- training[, bigNA==FALSE]
testing <- testing[, bigNA==FALSE]

# This removes the first five variables which have very little to do with prediction values
training <- training[, -(1:5)]
testing <- testing[, -(1:5)]

#This is using a Random Forest model to check on the accuracy. 
#Train is used on training with 3-fold cross validation.
weights <- trainControl(method="cv", number=3, verboseIter=TRUE)
weight <- train(classe ~ ., data=training, method="rf", trControl=weights)

#Shows the resulting accuracy
weight$finalModel

  results <- predict(weight, newdata=testing)

# convert predictions to character vector
results <- as.character(results)
plot(varImp(weight))

# create function to write predictions to files
pml_write_files <- function(x) {
  n <- length(x)
  for(i in 1:n) {
    filename <- paste0("problem_id_", i, ".txt")
    write.table(x[i], file=filename, quote=F, row.names=F, col.names=F)
  }
}

pml_write_files(results)
