# UCI HAR Dataset Code Book

## Data Source
Human Activity Recognition database built from the recordings of 30 subjects performing activities of daily living (ADL) while carrying a waist-mounted smartphone with embedded inertial sensors

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones


## Variables of fulldata_avg.txt dataset:

*subject_id: 1-30
*activity: WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS, SITTING, STANDING, LAYING
*features: Average of 66 features based on measurements on the mean and standard deviation for each measurement.


## Features Info:

The features selected for this database come from the accelerometer and gyroscope 3-axial raw signals tAcc-XYZ and tGyro-XYZ. These time domain signals (prefix 't' to denote time) were captured at a constant rate of 50 Hz. Then they were filtered using a median filter and a 3rd order low pass Butterworth filter with a corner frequency of 20 Hz to remove noise. Similarly, the acceleration signal was then separated into body and gravity acceleration signals (tBodyAcc-XYZ and tGravityAcc-XYZ) using another low pass Butterworth filter with a corner frequency of 0.3 Hz. 

Subsequently, the body linear acceleration and angular velocity were derived in time to obtain Jerk signals (tBodyAccJerk-XYZ and tBodyGyroJerk-XYZ). Also the magnitude of these three-dimensional signals were calculated using the Euclidean norm (tBodyAccMag, tGravityAccMag, tBodyAccJerkMag, tBodyGyroMag, tBodyGyroJerkMag). 

Finally a Fast Fourier Transform (FFT) was applied to some of these signals producing fBodyAcc-XYZ, fBodyAccJerk-XYZ, fBodyGyro-XYZ, fBodyAccJerkMag, fBodyGyroMag, fBodyGyroJerkMag. (Note the 'f' to indicate frequency domain signals). 

These signals were used to estimate variables of the feature vector for each pattern:  
'-XYZ' is used to denote 3-axial signals in the X, Y and Z directions.

* tBodyAcc-XYZ
* tGravityAcc-XYZ
* tBodyAccJerk-XYZ
* tBodyGyro-XYZ
* tBodyGyroJerk-XYZ
* tBodyAccMag
* tGravityAccMag
* tBodyAccJerkMag
* tBodyGyroMag
* tBodyGyroJerkMag
* fBodyAcc-XYZ
* fBodyAccJerk-XYZ
* fBodyGyro-XYZ
* fBodyAccMag
* fBodyAccJerkMag
* fBodyGyroMag
* fBodyGyroJerkMag

The set of variables that were estimated from these signals are: 

* mean(): Mean value
* std(): Standard deviation

## Transformations

The following steps were performed on the original UCI HAR train and test datasets through the run_analysis.R script:

1. Download and unzip dataset
2. Read activity and feature reference data
3. Read and merge x_train, y_train, subject_train datasets
4. Read and merge x_test, y_test, subject_test datasets
5. Merge test and train datasets
6. Remove duplicate columns
7. Add descriptive activity and feature labels
8. Extract measurements based on mean() and std()
9. Summarise data by averaging measurements for each observation and actitvity
10. Save tidy dataset as fulldata_avg.txt

## R Script for generating the fulldata_avg.txt file:

```{r}
##Load libraries
library(readtext)
library(dplyr)
library(magrittr)

##Download & unzip data
url<- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(url, "./UCI_HAR_Dataset.zip")
unzip("./UCI_HAR_Dataset.zip")

##Read Refernce Data
activity_ref<- read.table("./UCI HAR Dataset/activity_labels.txt")
colnames(activity_ref)<- c("id","activity")

features_ref<- read.table("./UCI HAR Dataset/features.txt")
colnames(features_ref)<- c("id","feature")


## Read & merge Train datasets
x_train<- read.table("./UCI HAR Dataset/train/X_train.txt")

y_train<- read.table("./UCI HAR Dataset/train/Y_train.txt")

subject_train<- read.table("./UCI HAR Dataset/train/subject_train.txt")

train<- cbind(subject_train, y_train, x_train)


## Read & merge Test datasets
x_test<- read.table("./UCI HAR Dataset/test/X_test.txt")

y_test<- read.table("./UCI HAR Dataset/test/Y_test.txt")

subject_test<- read.table("./UCI HAR Dataset/test/subject_test.txt")

test<- cbind(subject_test, y_test, x_test)


## Merge Train and Test datasets
fulldata<- rbind(train, test)

##Add column names
colnames(fulldata)<- c("subject_id","activity_id", as.character(features[,2]))

##Remove duplicate columns
fulldata <- fulldata[ , !duplicated(colnames(fulldata))]

##Add activity names and extract mean and std columns only
fulldata <- inner_join(fulldata, activity_ref, by = c("activity_id" = "id")) %>% 
    select(subject_id, activity, contains('mean()'), contains('std()'))

#Create summary dataset with the average of each variable for each activity and each subject
fulldata_avg<- fulldata %>%
    group_by(subject_id, activity) %>%
    summarise_all(c(mean = "mean"))

##Save summary dataset as txt file
write.table(fulldata_avg, "./UCI_HAR_avg.txt", row.names = FALSE)

```



