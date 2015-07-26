
#Practical Machine Learning Project
###Introduction
*The data for this project comes form 6 participants in a study who perform weightlifting exercises with accelerometers on their bodies and on the weights they are lifting.  The goal of the study was to be able to classify the motions of the bodies and weights and identify each of the participants.  A training data set was created with the study subjects identities and a testing data was created without the subjects identified.  
The training data is partitioned into training1 and training2 on a 70-30 split.  The traing1 data set is then analyzed and tested againt training2 using randon forest. After seeing if the results are satisfactory the entire trianing set is then analyzed and tested against the testing set.  All of the indiivduals are correctly identified.
The R script below uses analyzes the trianing data with random forest and is able to determone the study participants on the testing set.
It is followed by the command line input and output while the script is run and then by plots of the variable imporatnce from the partiition training1 and the full training set*

```
library(caret)
setwd("C:/Users/Mike/Documents/testdir/ML")
training <- read.csv("pml-training.csv")
testing <- read.csv("pml-testing.csv")
```

*This sets my home directory and loads the training and testing files into data frames*
```
library(caret)
setwd("C:/Users/Mike/Documents/testdir/ML")
training <- read.csv("pml-training.csv")
testing <- read.csv("pml-testing.csv")
```

*This breaks the training data into two partitions*
```

set.seed(12345)
setTrain <- createDataPartition(y=training$classe, p=0.7, list=F)
training1 <- training[setTrain, ]
training2 <- training[-setTrain, ]
```
*This detects and removes variables which are close to zero* 
```
zvar <- nearZeroVar(training1)
training1 <- training1[, -zvar]
training2 <- training2[, -zvar]
```
*This removes variables which have over 90% NA values*
```
bigNA <- sapply(training1, function(x) mean(is.na(x))) > 0.90
training1 <- training1[, bigNA==F]
training2 <- training2[, bigNA==F]

```
*This removes the first five variables which have very little to do with prediction values*
```
>training1 <- training1[, -(1:5)]
training2 <- training2[, -(1:5)]

```
*This is using a Random Forest model to check on the accuracy. 
Train is used on training1 with 3-fold cross validation.*

```
weights <- trainControl(method="cv", number=3, verboseIter=TRUE)
dim(training1)
```
*Weight model on training1*
```
weight <- train(classe ~., data=training1, method="rf", trControl=weights)
weight$finalModel
```
*Plot the output to see the impact the variables used in trianing1*
```
plot(varImp(weight))
```
*Display the confusion matrix to measure performance of the 
Random Forest on training1 versus training2*
```
results <- predict(weight, newdata=training2)
confusionMatrix(training2$classe, results)
```
*Remove the near zero variables from the training and testing sets*
```
zvar <- nearZeroVar(training)
training <- training[, -zvar]
testing <- testing[, -zvar]
```
*Remove variables that are over 90% NA's*
```
bigNA <- sapply(training, function(x) mean(is.na(x))) > 0.90
training <- training[, bigNA==FALSE]
testing <- testing[, bigNA==FALSE]
```
*This removes the first five variables which have very little to do with prediction values*
```
training <- training[, -(1:5)]
testing <- testing[, -(1:5)]
```
*This is using a Random Forest model to check on the accuracy. 
Train is used on training with 3-fold cross validation.*
```
weights <- trainControl(method="cv", number=3, verboseIter=TRUE)
weight <- train(classe ~ ., data=training, method="rf", trControl=weights)
```
*Shows the resulting accuracy*
```
weight$finalModel
results <- predict(weight, newdata=testing)
```
*convert predictions to character vector and plots the impact the variable had on the results*
```
results <- as.character(results)
plot(varImp(weight))
```
*This is the function which writes the predicitons to a directory so they can be entered*
```
pml_write_files <- function(x) {
  n <- length(x)
  for(i in 1:n) {
    filename <- paste0("problem_id_", i, ".txt")
    write.table(x[i], file=filename, quote=F, row.names=F, col.names=F)
  }
}

>pml_write_files(results)
```
*This is the command line while the scriot is run*
```
> library(caret)
Loading required package: lattice
Loading required package: ggplot2
Warning messages:
1: package ‘caret’ was built under R version 3.1.3 
2: package ‘ggplot2’ was built under R version 3.1.3 
> #This sets my hone direcotry and loads the training and testing files into data frames
> setwd("C:/Users/Mike/Documents/testdir/ML")
> training <- read.csv("pml-training.csv")
> testing <- read.csv("pml-testing.csv")
> # This breaks the training data into two partitions
> set.seed(12345)
> setTrain <- createDataPartition(y=training$classe, p=0.7, list=F)
> training1 <- training[setTrain, ]
> training2 <- training[-setTrain, ]
> #This detects and removes variables which are close to zero
> zvar <- nearZeroVar(training1)
> training1 <- training1[, -zvar]
> training2 <- training2[, -zvar]
> #This removes variables which have over 90% NA values
> bigNA <- sapply(training1, function(x) mean(is.na(x))) > 0.90
> training1 <- training1[, bigNA==F]
> training2 <- training2[, bigNA==F]
> # This removes the first five variables which have very little to do with prediction values
> training1 <- training1[, -(1:5)]
> training2 <- training2[, -(1:5)]
> #This is using a Random Forest model to check on the accuracy.
> #Train is used on training1 with 3-fold cross validation.
> weights <- trainControl(method="cv", number=3, verboseIter=TRUE)
> dim(training1)
[1] 13737    54
> # weight model on training1
> weight <- train(classe ~., data=training1, method="rf", trControl=weights)
Loading required package: randomForest
randomForest 4.6-10
Type rfNews() to see new features/changes/bug fixes.
+ Fold1: mtry= 2 
- Fold1: mtry= 2 
+ Fold1: mtry=27 
- Fold1: mtry=27 
+ Fold1: mtry=53 
- Fold1: mtry=53 
+ Fold2: mtry= 2 
- Fold2: mtry= 2 
+ Fold2: mtry=27 
- Fold2: mtry=27 
+ Fold2: mtry=53 
- Fold2: mtry=53 
+ Fold3: mtry= 2 
- Fold3: mtry= 2 
+ Fold3: mtry=27 
- Fold3: mtry=27 
+ Fold3: mtry=53 
- Fold3: mtry=53 
Aggregating results
Selecting tuning parameters
Fitting mtry = 27 on full training set
Warning message:
package ‘randomForest’ was built under R version 3.1.3 
> weight$finalModel

Call:
 randomForest(x = x, y = y, mtry = param$mtry) 
               Type of random forest: classification
                     Number of trees: 500
No. of variables tried at each split: 27

        OOB estimate of  error rate: 0.19%
Confusion matrix:
     A    B    C    D    E  class.error
A 3904    1    0    0    1 0.0005120328
B    5 2651    1    1    0 0.0026335591
C    0    6 2389    1    0 0.0029215359
D    0    0    7 2245    0 0.0031083481
E    0    0    0    3 2522 0.0011881188
> plot(varImp(weight))
> 
> 
> results <- predict(weight, newdata=training2)
> confusionMatrix(training2$classe, results)
Confusion Matrix and Statistics

          Reference
Prediction    A    B    C    D    E
         A 1674    0    0    0    0
         B    5 1133    1    0    0
         C    0    3 1023    0    0
         D    0    0    7  957    0
         E    0    0    0    3 1079

Overall Statistics
                                         
               Accuracy : 0.9968         
                 95% CI : (0.995, 0.9981)
    No Information Rate : 0.2853         
    P-Value [Acc > NIR] : < 2.2e-16      
                                         
                  Kappa : 0.9959         
 Mcnemar's Test P-Value : NA             

Statistics by Class:

                     Class: A Class: B Class: C Class: D Class: E
Sensitivity            0.9970   0.9974   0.9922   0.9969   1.0000
Specificity            1.0000   0.9987   0.9994   0.9986   0.9994
Pos Pred Value         1.0000   0.9947   0.9971   0.9927   0.9972
Neg Pred Value         0.9988   0.9994   0.9984   0.9994   1.0000
Prevalence             0.2853   0.1930   0.1752   0.1631   0.1833
Detection Rate         0.2845   0.1925   0.1738   0.1626   0.1833
Detection Prevalence   0.2845   0.1935   0.1743   0.1638   0.1839
Balanced Accuracy      0.9985   0.9980   0.9958   0.9977   0.9997
> #remove the near zero variables from the training and testing sets
> zvar <- nearZeroVar(training)
> training <- training[, -zvar]
> testing <- testing[, -zvar]
> # remove variables that are over 90% NA's
> bigNA <- sapply(training, function(x) mean(is.na(x))) > 0.90
> training <- training[, bigNA==FALSE]
> testing <- testing[, bigNA==FALSE]
> # This removes the first five variables which have very little to do with prediction values
> training <- training[, -(1:5)]
> testing <- testing[, -(1:5)]
> #This is using a Random Forest model to check on the accuracy.
> #Train is used on training with 3-fold cross validation.
> weights <- trainControl(method="cv", number=3, verboseIter=TRUE)
> weight <- train(classe ~ ., data=training, method="rf", trControl=weights)
+ Fold1: mtry= 2 
- Fold1: mtry= 2 
+ Fold1: mtry=27 
- Fold1: mtry=27 
+ Fold1: mtry=53 
- Fold1: mtry=53 
+ Fold2: mtry= 2 
- Fold2: mtry= 2 
+ Fold2: mtry=27 
- Fold2: mtry=27 
+ Fold2: mtry=53 
- Fold2: mtry=53 
+ Fold3: mtry= 2 
- Fold3: mtry= 2 
+ Fold3: mtry=27 
- Fold3: mtry=27 
+ Fold3: mtry=53 
- Fold3: mtry=53 
Aggregating results
Selecting tuning parameters
Fitting mtry = 27 on full training set
> #Shows the resulting accuracy
> weight$finalModel

Call:
 randomForest(x = x, y = y, mtry = param$mtry) 
               Type of random forest: classification
                     Number of trees: 500
No. of variables tried at each split: 27

        OOB estimate of  error rate: 0.14%
Confusion matrix:
     A    B    C    D    E  class.error
A 5578    1    0    0    1 0.0003584229
B    4 3790    2    1    0 0.0018435607
C    0    5 3416    1    0 0.0017533606
D    0    0    7 3208    1 0.0024875622
E    0    0    0    4 3603 0.0011089548
> results <- predict(weight, newdata=testing)
> # convert predictions to character vector
> results <- as.character(results)
> plot(varImp(weight))
> pml_write_files <- function(x) {
+ n <- length(x)
+ for(i in 1:n) {
+ filename <- paste0("problem_id_", i, ".txt")
+ write.table(x[i], file=filename, quote=F, row.names=F, col.names=F)
+ }
+ }
> pml_write_files(results)
```
*This plot shows the impact of each of the variables in calcualting the prediction using Training1*
![alt tag](https://cloud.githubusercontent.com/assets/10700001/8892358/ad569a0c-3322-11e5-8a88-2988c1fdce00.png)

*This shows the impact of each of the variables in calcualting the prediction using Training*
![alt tag](https://cloud.githubusercontent.com/assets/10700001/8892360/d4ffb016-3322-11e5-97de-02401bf56c40.png)

