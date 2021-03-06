# Creating tidy data from Samsung Galaxy SII

### This Readme file was created using knitr package that converts
### .Rmd files to .md and .html file. I learnt about it while taking
### another course in Data Science specialization

## extract mean and standard deviation values from features.txt, since point of interest

```r
namesFeature <- read.table("UCI_HAR_Dataset/features.txt", header=FALSE, col.names=c("ID", "metricName"))
featureDesired <- grep("std|mean", namesFeature$metricName)
```

## read train and test feature sets, and subset only the desired features

```r
featureTrain <- read.table("UCI_HAR_Dataset/train/X_train.txt", header=FALSE, col.names=namesFeature$metricName)
featureDesiredTrain <- featureTrain[,featureDesired]
featureTest <- read.table("UCI_HAR_Dataset/test/X_test.txt", header=FALSE, col.names=namesFeature$metricName)
featureDesiredTest <- featureTest[,featureDesired]
```

## Combine datasets

```r
featureDesiredMerge <- rbind(featureDesiredTrain, featureDesiredTest)
```

## Read and combine the train and test activity codes

```r
trainActivities <- read.table("UCI_HAR_Dataset/train/y_train.txt", header=FALSE)
testActivities <- read.table("UCI_HAR_Dataset/test/y_test.txt", header=FALSE)
mergeActivities <- rbind(trainActivities, testActivities)
```

## extract activity labels and match to activity codes

```r
activityLabels <- read.table("UCI_HAR_Dataset/activity_labels.txt", header=FALSE, col.names=c("ID", "activityName"))
mergeActivities$activity <- factor(mergeActivities$V1, levels = activityLabels$ID, labels = activityLabels$activityName)
```

## merge train and test subject ids

```r
trainSubjectID <- read.table("UCI_HAR_Dataset/train/subject_train.txt", header=FALSE)
testSubjectID <- read.table("UCI_HAR_Dataset/test/subject_test.txt", header=FALSE)
mergeSubjectID <- rbind(trainSubjectID, testSubjectID)
```

## Combine and name subjects and activity names

```r
subjects.and.activities <- cbind(mergeSubjectID, mergeActivities$activity)
colnames(subjects.and.activities) <- c("subject.id", "activity")
```

## Combine with measures of interest for finished desired data frame

```r
activityDF <- cbind(subjects.and.activities, featureDesiredMerge)
```

## From the set produced for analysis, compute and report means of 
## all measures, grouped by subject_id and by activity.

```r
resultDF <- aggregate(activityDF[,3:81], by = list(activityDF$subject.id, activityDF$activity), FUN = mean)
colnames(resultDF)[1:2] <- c("subject.id", "activity")
write.table(resultDF, file="mean_val.txt", row.names = FALSE)
```
