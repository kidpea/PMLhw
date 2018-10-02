---
title: "Weight Lifting Exercises Dataset analysis"
author: "Kidpea LAU"
date: "2018骞<b4>10鏈<88>1鏃<a5>"
output:
  
  html_document: 
    fig_caption: yes
    keep_md: yes
    toc: yes
  pdf_document: default
editor_options: 
  chunk_output_type: console
---
#Overview
The data is about Six young health participants were asked to perform one set of 10 repetitions of the Unilateral Dumbbell Biceps Curl in five different fashions: 
-exactly according to the specification (Class A)
-throwing the elbows to the front (Class B)
-lifting the dumbbell only halfway (Class C)
-lowering the dumbbell only halfway (Class D)
-throwing the hips to the front (Class E).

In the next , we will analysis the data and try to  find a predictor to classify the fashions. Then use the predictor to see if it s working well in the new data.

#DATA PREPROCESSING
###loading the TRAINING data into R

```
## Loading required package: lattice
```

```
## Loading required package: ggplot2
```

```
## Rattle: A free graphical interface for data science with R.
## XXXX 5.2.0 Copyright (c) 2006-2018 Togaware Pty Ltd.
## 键入'rattle()'去轻摇、晃动、翻滚你的数据。
```

```r
if(!file.exists("./trainingdata.csv")) {
  download.file("https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv",
                destfile = "./trainingdata.csv")
}

trainData<-read.csv("./trainingdata.csv")
```
###CLEAN THE DATA :

after cleaning the data, The data looks like  this :

```r
str(train)
```

```
## 'data.frame':	19622 obs. of  53 variables:
##  $ roll_belt           : num  1.41 1.41 1.42 1.48 1.48 1.45 1.42 1.42 1.43 1.45 ...
##  $ pitch_belt          : num  8.07 8.07 8.07 8.05 8.07 8.06 8.09 8.13 8.16 8.17 ...
##  $ yaw_belt            : num  -94.4 -94.4 -94.4 -94.4 -94.4 -94.4 -94.4 -94.4 -94.4 -94.4 ...
##  $ total_accel_belt    : int  3 3 3 3 3 3 3 3 3 3 ...
##  $ gyros_belt_x        : num  0 0.02 0 0.02 0.02 0.02 0.02 0.02 0.02 0.03 ...
##  $ gyros_belt_y        : num  0 0 0 0 0.02 0 0 0 0 0 ...
##  $ gyros_belt_z        : num  -0.02 -0.02 -0.02 -0.03 -0.02 -0.02 -0.02 -0.02 -0.02 0 ...
##  $ accel_belt_x        : int  -21 -22 -20 -22 -21 -21 -22 -22 -20 -21 ...
##  $ accel_belt_y        : int  4 4 5 3 2 4 3 4 2 4 ...
##  $ accel_belt_z        : int  22 22 23 21 24 21 21 21 24 22 ...
##  $ magnet_belt_x       : int  -3 -7 -2 -6 -6 0 -4 -2 1 -3 ...
##  $ magnet_belt_y       : int  599 608 600 604 600 603 599 603 602 609 ...
##  $ magnet_belt_z       : int  -313 -311 -305 -310 -302 -312 -311 -313 -312 -308 ...
##  $ roll_arm            : num  -128 -128 -128 -128 -128 -128 -128 -128 -128 -128 ...
##  $ pitch_arm           : num  22.5 22.5 22.5 22.1 22.1 22 21.9 21.8 21.7 21.6 ...
##  $ yaw_arm             : num  -161 -161 -161 -161 -161 -161 -161 -161 -161 -161 ...
##  $ total_accel_arm     : int  34 34 34 34 34 34 34 34 34 34 ...
##  $ gyros_arm_x         : num  0 0.02 0.02 0.02 0 0.02 0 0.02 0.02 0.02 ...
##  $ gyros_arm_y         : num  0 -0.02 -0.02 -0.03 -0.03 -0.03 -0.03 -0.02 -0.03 -0.03 ...
##  $ gyros_arm_z         : num  -0.02 -0.02 -0.02 0.02 0 0 0 0 -0.02 -0.02 ...
##  $ accel_arm_x         : int  -288 -290 -289 -289 -289 -289 -289 -289 -288 -288 ...
##  $ accel_arm_y         : int  109 110 110 111 111 111 111 111 109 110 ...
##  $ accel_arm_z         : int  -123 -125 -126 -123 -123 -122 -125 -124 -122 -124 ...
##  $ magnet_arm_x        : int  -368 -369 -368 -372 -374 -369 -373 -372 -369 -376 ...
##  $ magnet_arm_y        : int  337 337 344 344 337 342 336 338 341 334 ...
##  $ magnet_arm_z        : int  516 513 513 512 506 513 509 510 518 516 ...
##  $ roll_dumbbell       : num  13.1 13.1 12.9 13.4 13.4 ...
##  $ pitch_dumbbell      : num  -70.5 -70.6 -70.3 -70.4 -70.4 ...
##  $ yaw_dumbbell        : num  -84.9 -84.7 -85.1 -84.9 -84.9 ...
##  $ total_accel_dumbbell: int  37 37 37 37 37 37 37 37 37 37 ...
##  $ gyros_dumbbell_x    : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ gyros_dumbbell_y    : num  -0.02 -0.02 -0.02 -0.02 -0.02 -0.02 -0.02 -0.02 -0.02 -0.02 ...
##  $ gyros_dumbbell_z    : num  0 0 0 -0.02 0 0 0 0 0 0 ...
##  $ accel_dumbbell_x    : int  -234 -233 -232 -232 -233 -234 -232 -234 -232 -235 ...
##  $ accel_dumbbell_y    : int  47 47 46 48 48 48 47 46 47 48 ...
##  $ accel_dumbbell_z    : int  -271 -269 -270 -269 -270 -269 -270 -272 -269 -270 ...
##  $ magnet_dumbbell_x   : int  -559 -555 -561 -552 -554 -558 -551 -555 -549 -558 ...
##  $ magnet_dumbbell_y   : int  293 296 298 303 292 294 295 300 292 291 ...
##  $ magnet_dumbbell_z   : num  -65 -64 -63 -60 -68 -66 -70 -74 -65 -69 ...
##  $ roll_forearm        : num  28.4 28.3 28.3 28.1 28 27.9 27.9 27.8 27.7 27.7 ...
##  $ pitch_forearm       : num  -63.9 -63.9 -63.9 -63.9 -63.9 -63.9 -63.9 -63.8 -63.8 -63.8 ...
##  $ yaw_forearm         : num  -153 -153 -152 -152 -152 -152 -152 -152 -152 -152 ...
##  $ total_accel_forearm : int  36 36 36 36 36 36 36 36 36 36 ...
##  $ gyros_forearm_x     : num  0.03 0.02 0.03 0.02 0.02 0.02 0.02 0.02 0.03 0.02 ...
##  $ gyros_forearm_y     : num  0 0 -0.02 -0.02 0 -0.02 0 -0.02 0 0 ...
##  $ gyros_forearm_z     : num  -0.02 -0.02 0 0 -0.02 -0.03 -0.02 0 -0.02 -0.02 ...
##  $ accel_forearm_x     : int  192 192 196 189 189 193 195 193 193 190 ...
##  $ accel_forearm_y     : int  203 203 204 206 206 203 205 205 204 205 ...
##  $ accel_forearm_z     : int  -215 -216 -213 -214 -214 -215 -215 -213 -214 -215 ...
##  $ magnet_forearm_x    : int  -17 -18 -18 -16 -17 -9 -18 -9 -16 -22 ...
##  $ magnet_forearm_y    : num  654 661 658 658 655 660 659 660 653 656 ...
##  $ magnet_forearm_z    : num  476 473 469 469 473 478 470 474 476 473 ...
##  $ classe              : Factor w/ 5 levels "A","B","C","D",..: 1 1 1 1 1 1 1 1 1 1 ...
```

set the data into training and valid to improve the preditor:

```r
inTrain <- createDataPartition(train$classe, p=0.7, list=FALSE)
training <- train[inTrain,]
pmlvalid <- train[-inTrain,]
```
let see the density about different ways of the  data:

```r
p1<-qplot(total_accel_belt,colour=classe,data=training,geom="density")
p2<-qplot(total_accel_arm,colour=classe,data=training,geom="density")
p3<-qplot(total_accel_dumbbell,colour=classe,data=training,geom="density")
p4<-qplot(total_accel_forearm,colour=classe,data=training,geom="density")
```
![](PMLproject_files/figure-html/unnamed-chunk-7-1.png)<!-- -->
#TREE Model
I will use trees to build the predictor,and check it out with fancy:

```r
set.seed(111)
treemodel <- rpart(classe ~ ., data=training, method="class")
fancyRpartPlot(treemodel, sub="")
```

```
## Warning: labs do not fit even at cex 0.15, there may be some overplotting
```

![](PMLproject_files/figure-html/unnamed-chunk-8-1.png)<!-- -->
Using confusion Matrix to test results:

```r
treePred<-predict(treemodel,pmlvalid,type = "class")
treeconf<-confusionMatrix(treePred,pmlvalid$classe)
treeAccuracy<-round(treeconf$overall['Accuracy'],4)
print(treeconf)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 1497  195   13   51   18
##          B   56  664  100   77  109
##          C   41  154  799   83   81
##          D   63   79   73  662   91
##          E   17   47   41   91  783
## 
## Overall Statistics
##                                           
##                Accuracy : 0.7485          
##                  95% CI : (0.7372, 0.7596)
##     No Information Rate : 0.2845          
##     P-Value [Acc > NIR] : < 2.2e-16       
##                                           
##                   Kappa : 0.6814          
##  Mcnemar's Test P-Value : < 2.2e-16       
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.8943   0.5830   0.7788   0.6867   0.7237
## Specificity            0.9342   0.9279   0.9261   0.9378   0.9592
## Pos Pred Value         0.8439   0.6600   0.6900   0.6839   0.7998
## Neg Pred Value         0.9569   0.9026   0.9520   0.9386   0.9391
## Prevalence             0.2845   0.1935   0.1743   0.1638   0.1839
## Detection Rate         0.2544   0.1128   0.1358   0.1125   0.1331
## Detection Prevalence   0.3014   0.1709   0.1968   0.1645   0.1664
## Balanced Accuracy      0.9142   0.7555   0.8524   0.8123   0.8414
```

```r
plot(treeconf$table,col=treeconf$byClass,main=paste(
       "DECISION TREE MODEL CONFUSION MATRIX:ACCURACY=",round(treeconf$overall['Accuracy'],4)))
```

![](PMLproject_files/figure-html/unnamed-chunk-9-1.png)<!-- -->
As we can see, From the confusion matrix and the prediction accuracy (72.35%) of the combined model, there is no significant value in the added computational complexity for using the combined model for prediction.

#Random Forest
A random forest model was next applied to the dataset to see if it would lead to an improvement in prediction accuracy. For the Random Forest model, K-fold cross-validation is utilized.

```r
library(randomForest)
```

```
## randomForest 4.6-14
```

```
## Type rfNews() to see new features/changes/bug fixes.
```

```
## 
## Attaching package: 'randomForest'
```

```
## The following object is masked from 'package:rattle':
## 
##     importance
```

```
## The following object is masked from 'package:ggplot2':
## 
##     margin
```

```r
set.seed(222)
RFmodel <- randomForest(classe ~ ., data=training)
RFprediction <- predict(RFmodel, pmlvalid, type = "class")
RFconf <- confusionMatrix(RFprediction, pmlvalid$classe)
```
U can see the tree:

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 1673    7    0    0    0
##          B    1 1132   11    0    0
##          C    0    0 1015   10    0
##          D    0    0    0  952    4
##          E    0    0    0    2 1078
## 
## Overall Statistics
##                                           
##                Accuracy : 0.9941          
##                  95% CI : (0.9917, 0.9959)
##     No Information Rate : 0.2845          
##     P-Value [Acc > NIR] : < 2.2e-16       
##                                           
##                   Kappa : 0.9925          
##  Mcnemar's Test P-Value : NA              
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.9994   0.9939   0.9893   0.9876   0.9963
## Specificity            0.9983   0.9975   0.9979   0.9992   0.9996
## Pos Pred Value         0.9958   0.9895   0.9902   0.9958   0.9981
## Neg Pred Value         0.9998   0.9985   0.9977   0.9976   0.9992
## Prevalence             0.2845   0.1935   0.1743   0.1638   0.1839
## Detection Rate         0.2843   0.1924   0.1725   0.1618   0.1832
## Detection Prevalence   0.2855   0.1944   0.1742   0.1624   0.1835
## Balanced Accuracy      0.9989   0.9957   0.9936   0.9934   0.9979
```
Using confusion Matrix to test results:

```r
RFaccuracy <- round(RFconf$overall['Accuracy'], 4)
plot(RFconf$table, col = RFconf$byClass, main = paste(
       "Random Forest model confusion matrix: Accuracy =", round(RFconf$overall['Accuracy'], 4)))
```

![](PMLproject_files/figure-html/unnamed-chunk-12-1.png)<!-- -->
The random forest model had an overall prediction accuracy of 99.42%. This accuracy was much higher than that found for the simple tree model. For the model calculated using the Random Forest method, the out of sample error rate is 0.58%.

#predict the new testing data:
For Random Forests we use the following formula, which yielded a much better prediction in in-sample:

```r
#test
if(!file.exists("./testingdata.csv")) {
  download.file("https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv",
                destfile = "./testingdata.csv")
}

testData<-read.csv("./testingdata.csv")
library(randomForest)
RFpredictSubmit <- predict(RFmodel, testData, type = "class")
results <- data.frame("Participant"=testData$user_name, "Problem_id"=testData$problem_id, 
                      "Class"=RFpredictSubmit)
print(results)
```

```
##    Participant Problem_id Class
## 1        pedro          1     B
## 2       jeremy          2     A
## 3       jeremy          3     B
## 4       adelmo          4     A
## 5       eurico          5     A
## 6       jeremy          6     E
## 7       jeremy          7     D
## 8       jeremy          8     B
## 9     carlitos          9     A
## 10     charles         10     A
## 11    carlitos         11     B
## 12      jeremy         12     C
## 13      eurico         13     B
## 14      jeremy         14     A
## 15      jeremy         15     E
## 16      eurico         16     E
## 17       pedro         17     A
## 18    carlitos         18     B
## 19       pedro         19     B
## 20      eurico         20     B
```

