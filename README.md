# Tidy-Data-Project-Week-4
This Repo contains the syntax and explanation about the Project in the Getting and Cleaning Data.



#Below is the full syntax for the 5 steps of this assignment.
#It consists in 5 steps
#Step 1: Getting files for test and train
#you can get the full files information, the labels for each database and the creation of new variables in order to match both data for test and train. This steo also considers the Step 4 and do the right labeling of of the columns contained in both files.

#Step 2: According to the column names of the both datasets, identifies those variable names that contains "mean" or "std", so the column index can be identified and then we can  extract only those columns we are interested

#Step 3: Convert into factors the "Activity", so it can be used when grouping is requested

#Step 4: It is done and explained in the Step 5.

#Step 5. Makes the grouping by Activity and Subject, so it can be summarized by means scores. Also crean te txt file requested in the assignment.

#Getting and Cleaning Data Course Project
#Task: 1 and Task 4
#Merges the training and the test sets to create one data set
#Appropriately labels the data set with descriptive variable names

#Getting the files for test and train

library(dplyr)
library(stringr)

id_subject_test<-read.table(".\\UCI HAR Dataset/test/subject_test.txt")
scores_X_test<-read.table(".\\UCI HAR Dataset/test/X_test.txt")
activity_y_test<-read.table(".\\UCI HAR Dataset/test/y_test.txt")
features<-read.table(".\\UCI HAR Dataset/features.txt")

#naming the columns for id_subject_text
colnames(id_subject_test)<-("ID_Subject")
names_paste<-1:561
new_features<-sub("[(]","", features$V2)
new_features<-sub("[)]","", new_features)
new_features<-sub("-","_", new_features)
new_features<-sub("-","_", new_features)
colnames(scores_X_test)<-paste(new_features, names_paste,sep = "_")
colnames(activity_y_test)<-"Activity"

#adding a new variable that contains the kind of subject: train
data_type_test<-as.data.frame(rep("Test",2947))
colnames(data_type_test)<-"Data_Type"

#merging test data bases
data_test<-cbind(data_type_test,id_subject_test,scores_X_test,activity_y_test)
id_subject_train<-read.table(".\\UCI HAR Dataset/test/subject_test.txt")
scores_X_train<-read.table(".\\UCI HAR Dataset/test/X_test.txt")
activity_y_train<-read.table(".\\UCI HAR Dataset/test/y_test.txt")

colnames(id_subject_train)<-("ID_Subject")
colnames(scores_X_train)<-paste(new_features, names_paste,sep = "_")
colnames(activity_y_train)<-"Activity"

#adding a new variable that contains the kind of subject: train
data_type_train<-as.data.frame(rep("Train",2947))
colnames(data_type_train)<-"Data_Type"

#merging test data bases
data_train<-cbind(data_type_train,id_subject_train,scores_X_train,activity_y_train)

#binding both data bases: train and test

full_test_train<-rbind(data_test,data_train)
col_names_full_test_train<-colnames(full_test_train)
col_names_full_test_train<-sub(" ","_",col_names_full_test_train)

#Task: 2
#Extracts only the measurements on the mean and standard deviation for each measurement.



keep_mean_cols<-as.data.frame(grep("[Mm]ean",col_names_full_test_train, value=FALSE))
keep_std_cols<-as.data.frame(grep("std",col_names_full_test_train, value=FALSE))

colnames(keep_mean_cols)<-("Col_Mean")
colnames(keep_std_cols)<-("Col_Std")
consecutive<-as.data.frame(1:564)

keep_mean_cols<-as.matrix(keep_mean_cols)
keep_std_cols<-as.matrix(keep_std_cols)

only_means<-select(full_test_train, 1,2, keep_mean_cols,564)
only_std<-select(full_test_train, keep_std_cols)

data_mean_std<-cbind(only_means,only_std)

#Task: 3
#Uses descriptive activity names to name the activities in the data set

activity_labels<-read.table(".\\UCI HAR Dataset/activity_labels.txt")
colnames(activity_labels)<-c("Value","Label")
.
data_mean_std$Activity<-factor(data_mean_std$Activity, levels = activity_labels$Value, labels=activity_labels$Label)

#Task: 4
#The right labeling is already done in the step 1

#Task: 5
#From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

group_means<-group_by(data_mean_std, Activity,ID_Subject)

vars<-colnames(group_means)


means_by_group<-summarise_each(group_means,funs(mean))
          
write.table(means_by_group,file=".\\UCI HAR Dataset/Tidy Data for Cleaning Data Week 4.txt",row.names=FALSE)




