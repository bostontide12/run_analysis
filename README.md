# run_analysis
fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(fileUrl,destfile="./data/Dataset.zip",method="curl")
unzip(zipfile="./data/Dataset.zip",exdir="./data")
path_files <- file.path("./data" , "UCI HAR Dataset")
files<-list.files(path_files, recursive=TRUE)
## pulled the data set and downloaded, and created a path to it

dataActivityTest  <- read.table(file.path(path_files, "test" , "Y_test.txt" ),header = FALSE)
dataActivityTest  <- read.table(file.path(path_files, "test" , "Y_test.txt" ),header = FALSE)
dataActivityTrain <- read.table(file.path(path_files, "train", "Y_train.txt"),header = FALSE)
dataSubjectTrain <- read.table(file.path(path_files, "train", "subject_train.txt"),header = FALSE)
dataSubjectTest  <- read.table(file.path(path_files, "test" , "subject_test.txt"),header = FALSE)
dataFeaturesTest  <- read.table(file.path(path_files, "test" , "X_test.txt" ),header = FALSE)
dataFeaturesTrain <- read.table(file.path(path_files, "train", "X_train.txt"),header = FALSE)
dataFeaturesTrain <- read.table(file.path(path_files, "train", "X_train.txt"),header = FALSE)
## gave each of the tables a variable name

dataSubject <- rbind(dataSubjectTrain, dataSubjectTest)
dataActivity<- rbind(dataActivityTrain, dataActivityTest)
dataFeatures<- rbind(dataFeaturesTrain, dataFeaturesTest)
## combined each of the individual tables together

names(dataSubject)<-c("subject")
names(dataActivity)<- c("activity")
dataFeaturesNames <- read.table(file.path(path_files, "features.txt"),head=FALSE)
names(dataFeatures)<- dataFeaturesNames$V2
## set names to the tables

dataCombine <- cbind(dataSubject, dataActivity)
Data <- cbind(dataFeatures, dataCombine)
## merged the columns to get the data

subdataFeaturesNames<-dataFeaturesNames$V2[grep("mean\\(\\)|std\\(\\)", dataFeaturesNames$V2)]
selectedNames<-c(as.character(subdataFeaturesNames), "subject", "activity" )
Data<-subset(Data,select=selectedNames)
## Extract only the mean and standard deviation

activityLabels <- read.table(file.path(path_files, "activity_labels.txt"),header = FALSE)
names(Data)<-gsub("^t", "time", names(Data))
names(Data)<-gsub("^f", "frequency", names(Data))
names(Data)<-gsub("Acc", "Accelerometer", names(Data))
names(Data)<-gsub("Gyro", "Gyroscope", names(Data))
names(Data)<-gsub("Mag", "Magnitude", names(Data))
names(Data)<-gsub("BodyBody", "Body", names(Data))
## used descriptive names for each section

Data2<-aggregate(. ~subject + activity, Data, mean)
Data2<-Data2[order(Data2$subject,Data2$activity),]
write.table(Data2, file = "tidydata.txt",row.name=FALSE)
## created second tidy data set
