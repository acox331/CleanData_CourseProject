---
title: "CodeBook"
author: "Alexandria C."
date: '2022-10-18'
output: html_document
---

## **File containing the R code "run_analysis.R" with the following 5 steps:**

0.  Loading necessary package and downloading data set

```{r, echo=TRUE}

library("dplyr")

if(!file.exists("./data")){dir.create("./data")}
fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(fileUrl,destfile="./data/Dataset.zip")


```

# Unzip data set to data directory

```{r, echo=TRUE}
unzip(zipfile="./data/Dataset.zip",exdir="./data")
```

1.  ***Merging the training and the test sets to create one data set.***

1.1 Reading files into R Reading trainings tables into R

```{r , echo=TRUE}
x_train <- read.table("./data/UCI HAR Dataset/train/X_train.txt")
y_train <- read.table("./data/UCI HAR Dataset/train/y_train.txt")
subject_train <- read.table("./data/UCI HAR Dataset/train/subject_train.txt")  
```

1.2 Reading testing tables into R

```{r, echo=TRUE}
x_train <- read.table("./data/UCI HAR Dataset/train/X_train.txt")
y_train <- read.table("./data/UCI HAR Dataset/train/y_train.txt")
subject_train <- read.table("./data/UCI HAR Dataset/train/subject_train.txt")  
```

1.3 Reading feature vector into R

```{r, echo=TRUE}
features <- read.table('./data/UCI HAR Dataset/features.txt')   
```

1.4 Reading activity labels

```{r, echo=TRUE}
activityLabels = read.table('./data/UCI HAR Dataset/activity_labels.txt')  
```

1.5 Assigning column names into R

```{r, echo=TRUE}
colnames(x_train) <- features[,2]
colnames(y_train) <-"activityId"
colnames(subject_train) <- "subjectId"

colnames(x_test) <- features[,2] 
colnames(y_test) <- "activityId"
colnames(subject_test) <- "subjectId"

colnames(activityLabels) <- c('activityId','activityType')
```

1.6 Merging all data into just one data set

```{r, echo=TRUE}
mrg_train <- cbind(y_train, subject_train, x_train)
mrg_test <- cbind(y_test, subject_test, x_test)
setAllInOne <- rbind(mrg_train, mrg_test)   
```

2.  ***Extracting only the measurements on the mean and standard deviation for each measurement***

2.1 Reading column names

```{r step 1, echo=TRUE}
colNames <- colnames(setAllInOne)  
```

2.2 Create vector for defining ID, mean and standard deviation

```{r step 1, echo=TRUE}
mean_and_std <- (grepl("activityId" , colNames) | 
                   grepl("subjectId" , colNames) | 
                   grepl("mean.." , colNames) | 
                   grepl("std.." , colNames) 
)   
```

2.3 Making nessesary subset from setAllInOne

```{r, echo=TRUE}
setForMeanAndStd <- setAllInOne[ , mean_and_std == TRUE]   
```

3.  ***Giving descriptive activity names for the activities in the data set***

```{r, echo=TRUE}
secTidySet <- aggregate(. ~subjectId + activityId, setWithActivityNames, mean)
secTidySet <- secTidySet[order(secTidySet$subjectId, secTidySet$activityId),]  
```

4.  ***Adding appropriate labeling to the data set with descriptive names for variables***

*[Done in previous steps]*

5.  ***Creating a second tidy data set independent from the original, with the average of each variable for each activity and each subject***

5.1 Making second tidy data set

```{r step 1, echo=TRUE}
secTidySet <- aggregate(. ~subjectId + activityId, setWithActivityNames, mean)
secTidySet <- secTidySet[order(secTidySet$subjectId, secTidySet$activityId),]   
```

5.2 Converting second tidy data set into a txt file

```{r step 1, echo=TRUE}
write.table(secTidySet, "secTidySet.txt", row.name=FALSE)   
```

------------------------------------------------------------------------

## **Variables**

**x_train**, **y_train**, **x_test**, **y_test**, **subject_train**, and **subject_test** all contain data from the original data sets.

**x_data**, **y_data**, and **subject_data** merge the original data sets.

**features** contains the correct names for the **x_data** data set.
