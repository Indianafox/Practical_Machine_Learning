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

The original source of this data comes from this original source: http://groupware.les.inf.puc-rio.br/har. 

### The Code:

### load libraries and read in raw data
```
library(caret)
library(gbm)
url <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv"
training_Raw <- read.csv(url)
url2 <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv"
testing_Raw <- read.csv(url2)
```

### Partition training_raw dataframe into training and testing dataframes
```
inTrain <-createDataPartition(training$classe, p=0.75)[[1]]
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
RF_CM
```
Confusion Matrix and Statistics

          Reference
Prediction    A    B    C    D    E
         A 1393    7    0    0    0
         B    2  942    5    0    0
         C    0    0  846    8    0
         D    0    0    4  795    0
         E    0    0    0    1  901

Overall Statistics
                                         
               Accuracy : 0.9945         
                 95% CI : (0.992, 0.9964)
    No Information Rate : 0.2845         
    P-Value [Acc > NIR] : < 2.2e-16      
                                         
                  Kappa : 0.993          
 Mcnemar's Test P-Value : NA             

Statistics by Class:

                     Class: A Class: B Class: C Class: D Class: E
Sensitivity            0.9986   0.9926   0.9895   0.9888   1.0000
Specificity            0.9980   0.9982   0.9980   0.9990   0.9998
Pos Pred Value         0.9950   0.9926   0.9906   0.9950   0.9989
Neg Pred Value         0.9994   0.9982   0.9978   0.9978   1.0000
Prevalence             0.2845   0.1935   0.1743   0.1639   0.1837
Detection Rate         0.2841   0.1921   0.1725   0.1621   0.1837
Detection Prevalence   0.2855   0.1935   0.1741   0.1629   0.1839
Balanced Accuracy      0.9983   0.9954   0.9937   0.9939   0.9999
