# Practical-Machine-Learning
---
title: "Sensor Prediction"
author: "Valentino Avon"
date: "02 febbraio 2016"
output: html_document
---

Thanks to Groupware, who provided the data for the course. <http://groupware.les.inf.puc-rio.br/har>.
I load the dataset and the submit set.
```{r}
dataset <- read.csv("pml-training.csv")
submit <- read.csv("pml-testing.csv")
```
I loaded the caret package and the RWeka package.

```{r}
library(caret)
library(RWeka)
```


Then i partitioned the dataset in three part: training set, validation set and test set.
I randomly shuffled the records to break the order of the instances.
```{r}
# create validation set
set.seed(1234)
trainingIndex <- createDataPartition(dataset$classe)[[1]]
training <- dataset[trainingIndex, ]
dataset <- dataset[-trainingIndex, ]
validationIndex <- createDataPartition(dataset$classe)[[1]]
validation <- dataset[validationIndex,]
testing <-  dataset[-validationIndex,]

#randomly shuffle the record (because they are ordered)
training <- training[sample(nrow(training)), ]
validation <- validation[sample(nrow(validation)), ]
testing <- testing[sample(nrow(testing)), ]
```

The dataset has a lot of NA value in some columns. Therefore, I removed these columns because they was noisy. I also removed the X attribute, which produces wrong results making predictions bad.

```{r}
# get the column without NA value
features <- training[ , colSums(is.na(training)) == 0]
# remove X attribute, it alters the results!:
features <-features[, -1]
```
Then I try to use the simpliest model that I know: the one rule algorithm, in order to get a baseline on the accuracy.
I trained it with the selected features and predict the results on the validation set.
```{r}
baseline <- RWeka::OneR(classe ~ ., data = features) #0.2829 with na
prediction <- predict(baseline, validation, type = "class")
confusionMatrix(prediction, validation$classe)
```
I got an accuracy of 100%, this is perfect.
To verify that it was not a strange result I predicted the values of the testing set.
```{r}
confusionMatrix(predict(baseline, testing, type = "class"), testing$classe)
```
Here I got an accuray of 99.96%, which is very good. 
A simple model is sufficient to capture the essence of data and to make prediction, this is great. Finally, I predicted the value of the submit set to accomplish the assignment:
```{r}
prediction <- predict(baseline, submit, type = "class")
```
Submitting this forecasts on the coursera web page, I got 20/20.

