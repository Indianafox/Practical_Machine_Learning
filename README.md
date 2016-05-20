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
