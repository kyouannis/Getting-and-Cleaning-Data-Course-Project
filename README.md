# Getting-and-Cleaning-Data-Course-Project
##The run_analysis.R script downloads the UCI HAR Dataset and does the following:

# 1. Merges the training and the test sets to create one data set.
# 2. Extracts only the measurements on the mean and standard deviation for each measurement.
# 3. Uses descriptive activity names to name the activities in the data set
# 4. Appropriately labels the data set with descriptive variable names.
# 5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

# Refer to CodeBook.md for more information on data and variables

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



