Project-getting-and-Cleaning-Data
=================================
library(reshape2)
setwd("../")
setwd("Project Data Cleaning/UCI HAR Dataset")
#### Read all activities and their names and label the aproppriate columns 
df_activity<-read.csv("activity_labels.txt", sep=" ",header=F)
colnames(df_activity)<-c("activity_id","activity_name")
## Read the dataframe's column names
df_feat<-read.csv("features.txt", sep=" ",header=F)
df_feat_variable<-df_feat[,2]
## Read the test data and label the dataframe's columns
df_X_test<-read.csv(".//test//X_test.txt",sep="",header=F)
colnames(df_X_test)<-df_feat_variable
## Read the training data and label the dataframe's columns
df_X_train<-read.csv(".//train//X_train.txt",sep="",header=F)
##getting rid of unwanted rows
df_X_train<-df_X_train[-7253:-7255,]
colnames(df_X_train)<-df_feat_variable

## Read the ids of the test subjects and label the the dataframe's columns
df_subject_test<-read.csv(".//test//subject_test.txt",sep="",header=F)
colnames(df_subject_test)<-"subject_id"

## Read the activity id's of the test data and label the the dataframe's columns
test_activity_id<-read.csv(".//test//y_test.txt",sep="",header=F)
colnames(test_activity_id)<-"activity_id"

## Read the ids of the train subjects and label the the dataframe's columns
df_subject_train<-read.csv(".//train//subject_train.txt",sep="",header=F)
colnames(df_subject_train)<-"subject_id"

## Read the activity id's of the train data and label the the dataframe's columns
train_activity_id<-read.csv(".//train//y_train.txt",sep="",header=F)
colnames(train_activity_id)<-"activity_id"

##merge now Xand Y test and Train Data
test_data<-cbind(df_subject_test,test_activity_id,df_X_test)
train_data<-cbind(df_subject_train,train_activity_id,df_X_train)

all_data<-rbind(test_data,train_data)
##Keep only columns refering to mean() or std() values

mean_col_idx <- grep("mean",names(all_data),ignore.case=TRUE) 
mean_col_names <- names(all_data)[mean_col_idx]

std_col_idx <- grep("std",names(all_data),ignore.case=TRUE) 
std_col_names <- names(all_data)[std_col_idx] 


meanstddata <-all_data[,c("subject_id","activity_id",mean_col_names,std_col_names)]

##Merge the activities datase with the mean/std values datase  
##to get one dataset with descriptive activity names 
descrnames <- merge(df_activity,meanstddata,by.x="activity_id",by.y="activity_id",all=TRUE) 


##Melt the dataset with the descriptive activity names for better handling 
data_melt <- melt(descrnames,id=c("activity_id","activity_name","subject_id")) 

##Cast the melted dataset according to  the average of each variable  
 ##for each activity and each subjec 
mean_data <- dcast(data_melt,activity_id + activity_name + subject_id ~ variable,mean) 
        

## Create a file with the new tidy dataset 
write.table(mean_data,"./tidy_movement_data.txt") 




project 
