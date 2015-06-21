Practical Machine learning project
========================================================



```r
library(randomForest)
```

```
## Warning: package 'randomForest' was built under R version 3.1.3
```

```
## randomForest 4.6-10
## Type rfNews() to see new features/changes/bug fixes.
```

```r
library(caret)
```

```
## Warning: package 'caret' was built under R version 3.1.3
```

```
## Loading required package: lattice
## Loading required package: ggplot2
```

### Supply source URL's, fill NULL values with "NA", create train and test sets:


```r
trainUrl <- "http://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv"
testUrl <- "http://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv"

testing <- read.csv(url(testUrl), na.strings=c("NA","#DIV/0!",""))
training <- read.csv(url(trainUrl), na.strings=c("NA","#DIV/0!",""))

inTrain <- createDataPartition(y=training$classe, p=0.6, list=FALSE)
myTraining <- training[inTrain, ]
myTesting <- training[-inTrain, ]
```

### Remove columns with more than 50% NA values:


```r
myTestingNoNA <- myTesting[, colSums(is.na(myTesting)) < nrow(myTesting) * 0.5]
myTrainingNoNA <- myTraining[, colSums(is.na(myTraining)) < nrow(myTraining) * 0.5]
#also do this for the supplied testset!
testingNoNA <- testing[, colSums(is.na(testing)) < nrow(testing) * 0.5]
```

### Remove first 7 columns:


```r
myTrainingNoNA <- myTrainingNoNA[,8:length(colnames(myTrainingNoNA))]
myTestingNoNA <- myTestingNoNA[,8:length(colnames(myTestingNoNA))]
testingNoNA <- testingNoNA[,8:length(colnames(testingNoNA))]
```

### Build model:


```r
modFit <- randomForest(classe ~. , data=myTrainingNoNA)
```

### Test on own created testset:


```r
predictions <- predict(modFit, myTestingNoNA, type = "class")
confusionMatrix(predictions, myTestingNoNA$classe)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 2230   10    0    0    0
##          B    1 1508    6    0    0
##          C    0    0 1362   16    2
##          D    0    0    0 1269    5
##          E    1    0    0    1 1435
## 
## Overall Statistics
##                                           
##                Accuracy : 0.9946          
##                  95% CI : (0.9928, 0.9961)
##     No Information Rate : 0.2845          
##     P-Value [Acc > NIR] : < 2.2e-16       
##                                           
##                   Kappa : 0.9932          
##  Mcnemar's Test P-Value : NA              
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.9991   0.9934   0.9956   0.9868   0.9951
## Specificity            0.9982   0.9989   0.9972   0.9992   0.9997
## Pos Pred Value         0.9955   0.9954   0.9870   0.9961   0.9986
## Neg Pred Value         0.9996   0.9984   0.9991   0.9974   0.9989
## Prevalence             0.2845   0.1935   0.1744   0.1639   0.1838
## Detection Rate         0.2842   0.1922   0.1736   0.1617   0.1829
## Detection Prevalence   0.2855   0.1931   0.1759   0.1624   0.1832
## Balanced Accuracy      0.9987   0.9962   0.9964   0.9930   0.9974
```

### YES, 99%+ accuracy!

### Test on supplied testset:


```r
predictions <- predict(modFit, testingNoNA, type = "class")
print(predictions)
```

```
##  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 
##  B  A  B  A  A  E  D  B  A  A  B  C  B  A  E  E  A  B  B  B 
## Levels: A B C D E
```


