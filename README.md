# Getting-and-Cleaning-Data
Coursera's getting and cleaning data final project

library(dplyr)

#Creating file name

filename<-"Coursera_DataCleaning_Project.zip"

#Checking if zip file exists

if(!file.exists(filename)){
  fileURL<-"https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
  download.file(fileURL,filename,method="curl")  }
  
#Checking if folder exists

if(!file.exists("UCI HAR Dataset")){
  unzip(filename)}

#Assigning data frames. Use all lowercase to avoid issues later

features<-read.table("UCI HAR Dataset/features.txt",col.names=c("n","functions"))

activities<-read.table("UCI HAR Dataset/activity_labels.txt",col.names=c("code","activity"))

subject_test<-read.table("UCI HAR Dataset/test/subject_test.txt",col.names="subject")

x_test<-read.table("UCI HAR Dataset/test/X_test.txt",col.names=features$functions)

y_test<-read.table("UCI HAR Dataset/test/y_test.txt",col.names="code")

subject_train<-read.table("UCI HAR Dataset/train/subject_train.txt",col.names="subject")

x_train<-read.table("UCI HAR Dataset/train/X_train.txt",col.names=features$functions)

y_train<-read.table("UCI HAR Dataset/train/y_train.txt",col.names="code")

#Merging the test and training sets into a single data set. Stick to all lowercase

x<-rbind(x_train,x_test)

y<-rbind(y_train,y_test)

subject<-rbind(subject_train,subject_test)

merge_data<-cbind(subject,x,y)

#Extracting mean and standard deviation for each measurement

tidydata<-merge_data%>%select(subject,code,contains("mean"),contains("std"))

#Gives descriptive activity names to the data set

tidydata$code<-activities[tidydata$code,2]

#Gives descriptive names to variables, again using all lowercase

names(tidydata)

names(tidydata)[2]="activity"

names(tidydata)<-gsub("Acc","accelerometer",names(tidydata))

names(tidydata)<-gsub("Gyro","gyroscope",names(tidydata))

names(tidydata)<-gsub("BodyBody","body",names(tidydata))

names(tidydata)<-gsub("Mag","magnitude",names(tidydata))

names(tidydata)<-gsub("^t","time",names(tidydata))

names(tidydata)<-gsub("^f","frequency",names(tidydata))

names(tidydata)<-gsub("tBody","timebody",names(tidydata))

names(tidydata)<-gsub("-mean()","mean",names(tidydata),ignore.case=TRUE)

names(tidydata)<-gsub("-std()","std",names(tidydata),ignore.case=TRUE)

names(tidydata)<-gsub("-freq()","frequency",names(tidydata),ignore.case=TRUE)

names(tidydata)<-gsub("angle","angle",names(tidydata))

names(tidydata)<-gsub("gravity","gravity",names(tidydata))

#Clean data output

Final<-tidydata%>%
  group_by(subject,activity)%>%
  summarize_all(funs(mean))

#Looking at final data

Final

#Creating txt file from final tidy data

write.table(Final,"Final.txt",row.name=FALSE)
