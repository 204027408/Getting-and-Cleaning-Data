Getting-and-Cleaning-Data
=========================

Project Course
# Getting and Cleaning Data
# Course Project
# Data downloaded to the working directory from the web page given in the assignment.
# Could not read it directly with R since the libraries were not working in the R version used.
https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip
# 1. Merges the training and the test sets to create one data set:
# Reading the data from the working directory
X_train <- read.table("./UCI HAR Dataset/train/X_train.txt", header = FALSE)
X_test <- read.table("./UCI HAR Dataset/test/X_test.txt", header = FALSE)
y_train <- read.table("./UCI HAR Dataset/train/y_train.txt", header = FALSE)
y_test <- read.table("./UCI HAR Dataset/test/y_test.txt", header = FALSE)
subject_train <- read.table("./UCI HAR Dataset/train/subject_train.txt", header = FALSE)
subject_test <- read.table("./UCI HAR Dataset/test/subject_test.txt", header = FALSE)

# Combine all data rows in one single table for x, y and s.
x <- rbind(X_train, X_test)
y <- rbind(y_train, y_test)
s <- rbind(subject_train, subject_test)

# 2. Extracts only the measurements on the mean and standard deviation for each measurement.
# Read features labels
features <- read.table("./UCI HAR Dataset/features.txt")

# Naming features column
names(features) <- c('feature_id', 'feature_name')

# Mean or standard deviations match
features_ID <- grep("-mean\\(\\)|-std\\(\\)", features$feature_name) 
x <- x[, features_ID] 

# Strings matches 
names(x) <- gsub("\\(|\\)", "", (features[features_ID, 2]))

# 3. Uses descriptive activity names to name the activities in the data set and 
# 4. Appropriately labels the data set with descriptive activity names.
# Reading activities
activities <- read.table("./UCI HAR Dataset/activity_labels.txt")

# naming column of activities 
names(activities) <- c('act_id', 'act_name')
y[, 1] = activities[y[, 1], 2]
names(y) <- "Activity"
names(s) <- "Subject"

# Combining data frame with columns to get the tidy data set
tidy_data_set <- cbind(s, y, x)

# Create file with tidy data set(txt and csv)
write.table (tidy_data_set, file="tidy_data_set", row.name=FALSE)
write.csv (tidy_data_set, file="tidy_data_set.csv")

# 5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
second <- tidy_data_set[, 3:dim(tidy_data_set)[2]] 
avg_tidy_data_set <- aggregate(second,list(tidy_data_set$Subject, tidy_data_set$Activity), mean)
# Column Names for Activity and Subject 
names(avg_tidy_data_set)[1] <- "Subject"
names(avg_tidy_data_set)[2] <- "Activity"

# Create file with averages of tidy data set
write.table(avg_tidy_data_set, file="avg_tidy_data_set", row.name=FALSE)
write.csv(avg_tidy_data_set, file="avg_tidy_data_set.csv")
