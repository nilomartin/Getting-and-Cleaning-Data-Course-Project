# Getting-and-Cleaning-Data-Course-Project
Repository for the Getting and Cleaning Data Course project.

The file run_analysis.R can be run as long as the Samsung data is in your working directory.

The run_analysis.R script works according to the following steps.

1-Loading packages:

library(data.table)
library(dplyr)

2-Load data info into R:

featuresPath<-'C:\\Users\\niloa\\Documents\\machine_learning\\Curso3_coursera\\project\\UCI HAR Dataset\\features.txt'
activityPath<-'C:\\Users\\niloa\\Documents\\machine_learning\\Curso3_coursera\\project\\UCI HAR Dataset\\activity_labels.txt'
features<-fread(featuresPath)
activity<-fread(activityPath,col.names = c('id','aactivity'))
featuresNames<-as.character(features$V2)

3-Load train data into R:

xtrainPath<-'C:\\Users\\niloa\\Documents\\machine_learning\\Curso3_coursera\\project\\UCI HAR Dataset\\train\\X_train.txt'
ytrainPath<-'C:\\Users\\niloa\\Documents\\machine_learning\\Curso3_coursera\\project\\UCI HAR Dataset\\train\\Y_train.txt'
trainSubjectPath<-'C:\\Users\\niloa\\Documents\\machine_learning\\Curso3_coursera\\project\\UCI HAR Dataset\\train\\subject_train.txt'
xtrain<-fread(xtrainPath)
ytrain<-fread(ytrainPath)
subjectTrain<-fread(trainSubjectPath)
trainData<-cbind(subjectTrain,ytrain,xtrain)

3-Load test data into R:

xtestPath<-'C:\\Users\\niloa\\Documents\\machine_learning\\Curso3_coursera\\project\\UCI HAR Dataset\\test\\X_test.txt'
ytestPath<-'C:\\Users\\niloa\\Documents\\machine_learning\\Curso3_coursera\\project\\UCI HAR Dataset\\test\\Y_test.txt'
testSubjectPath<-'C:\\Users\\niloa\\Documents\\machine_learning\\Curso3_coursera\\project\\UCI HAR Dataset\\test\\subject_test.txt'
xtest<-fread(xtestPath)
ytest<-fread(ytestPath)
subjectTest<-fread(testSubjectPath)
testData<-cbind(subjectTest,ytest,xtest)

4- Merging train and test data into variable "projectData":

projectData<-rbind(trainData,testData)
projectData<-data.table(projectData)
setnames(projectData,c('subject','activity',featuresNames))

5- Filter mean and std columns using grep() function:

columns<-grep("subject|activity|mean[\'('\')']|std[\'('\')'])", names(projectData),value = T)
projectData<-select(projectData,c(columns))

6- Changing activities numbers to activity labels:

projectData$activity<-activity[projectData$activity,2]

7- Appropriated variables naming with gsub() function:

names(projectData)<-gsub("Acc", "Accelerometer", names(projectData))
names(projectData)<-gsub("Gyro", "Gyroscope", names(projectData))
names(projectData)<-gsub("BodyBody", "Body", names(projectData))
names(projectData)<-gsub("Mag", "Magnitude", names(projectData))
names(projectData)<-gsub("^t", "Time", names(projectData))
names(projectData)<-gsub("^f", "Frequency", names(projectData))
names(projectData)<-gsub("tBody", "TimeBody", names(projectData))
names(projectData)<-gsub("-mean()", "Mean", names(projectData), ignore.case = TRUE)
names(projectData)<-gsub("-std()", "STD", names(projectData), ignore.case = TRUE)
names(projectData)<-gsub("-freq()", "Frequency", names(projectData), ignore.case = TRUE)
names(projectData)<-gsub("angle", "Angle", names(projectData))
names(projectData)<-gsub("gravity", "Gravity", names(projectData))

8-Subset the data with group_by() function and writing into txt file using wtrite.table():

projectDataSubset<-group_by(projectData,subject,activity)
finalSubset<-summarise_all(projectDataSubset,mean)
write.table(finalSubset, 'finalSubset.txt', row.name=FALSE)
