## Practical Machine Learning Assignment

### Background
Using devices such as JawboneUp, NikeFuelBand, and Fitbitit is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement - a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in
their behavior, or because they are tech geeks. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it.  
   
In this project, the goal is to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. They were asked to perform barbell lifts correctly and incorrectly (in 4 different ways). More information is available from the website: [http://groupware.les.inf.puc-rio.br/har](http://groupware.les.inf.puc-rio.br/har) (see the section on the Weight Lifting Exercise Dataset).   

### Data Sources
The raw data for this project was obtained from here:

https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv

The test data for the prediction quiz part of this assignment are available here:

https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv

The original source of this data comes this page: http://groupware.les.inf.puc-rio.br/har. 

### The Code:

### load libraries and read in raw data
```
library(caret)
library(gbm)
url <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv"
training_Raw <- read.csv(url)
url2 <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv"
testing_Raw <- read.csv(url2)

set.seed(510)
```

### Partition training_raw dataframe into training and testing dataframes
```
inTrain <-createDataPartition(training_Raw$classe, p=0.75)[[1]]
training <- training_Raw[inTrain,]
testing <- training_Raw[-inTrain,]
```
### Data cleaning
```
# Replace each NA with a zero to assist with removal below of variables with near zero variance
temp <- training
temp[is.na(temp)] <- 0

# Remove variables with near zero variance in observations
NZV_ColsToDel <- nearZeroVar(temp); rm(temp)
training <- training[,-NZV_ColsToDel]
testing <- testing[,-NZV_ColsToDel]

# Also remove variables that have variance but are not sensor readings eg time stamps
ExtraColsToDel <- c(1:6)
training <- training[,-ExtraColsToDel]
testing <- testing[,-ExtraColsToDel]
```
### Commence modelling
```
# Set caret train trControl parameter
fitControl <- trainControl(method="repeatedcv",
                           number=5,
                           repeats=1,
                           verboseIter=TRUE)

# Model using Random Forest method of caret train function
modFit_RF <- train(training$classe ~ .,method="rf", data = training, trControl = fitControl)
PredRF <- predict(modFit_RF,testing)
RF_CM <- confusionMatrix(PredRF,testing$classe)
RF_CM$overall
```
     Accuracy          Kappa  AccuracyLower  AccuracyUpper   AccuracyNull AccuracyPValue  McnemarPValue 
     0.9944943      0.9930350      0.9919995      0.9963687      0.2844617      0.0000000            NaN
```
# Model using rpart method of caret train function
modFit_rpart <- train(training$classe ~ .,method="rpart",data=training, trControl = fitControl)
PredRPART <- predict(modFit_rpart,testing)
RPart_CM <- confusionMatrix(PredRPART,testing$classe)
RPart_CM$overall
```
     Accuracy          Kappa  AccuracyLower  AccuracyUpper   AccuracyNull AccuracyPValue  McnemarPValue 
     0.5034666     3.521546e-01   4.893724e-01   5.175566e-01   2.844617e-01  3.077590e-228         NaN 
```
# Model using gbm method of caret train function
modFit_gbm <- train(training$classe ~ .,method="gbm",data=training, trControl = fitControl)
PredGBM <- predict(modFit_gbm,testing) #
GBM_CM <- confusionMatrix(PredGBM,testing$classe) # Accuracy 0.9623
GBM_CM$overall
```
     Accuracy          Kappa  AccuracyLower  AccuracyUpper   AccuracyNull AccuracyPValue  McnemarPValue 
     0.9622757   9.522868e-01   9.565588e-01   9.674334e-01   2.844617e-01   0.000000e+00   5.312594e-06
  
### Choose best method
The method with the lowest error rate (ie 1-0.9943 = 0.0167 or 1.67%) is Random Forest.

### Use Random Forest method on 20 test cases for automated marking part of assignment
```
Classe <- predict(modFit_RF,test20) # Predict classe
test20 <- cbind(test20,Classe) # Add new column containing predicted classe values
test20 <- test20[,c(53:54)] # Keep only problem_id and classe vars
write.csv(test20,"test20.csv")
```
