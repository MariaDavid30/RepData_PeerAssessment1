---
title: "Reproducible Research(Project 1)"
author: "Maria David"
date: "9/19/2020"
output: html_document
---

**Loading the data**

```{r,echo=TRUE}
#Using read.csv to read the data
activity <- read.csv("F:/online_courses/activity.csv")
```

**Understanding the Data **

```{r,echo=TRUE}
names(activity)
head(activity)
str(activity)
dim(activity)
```

**What is mean total number of steps taken per day?**

```{r,echo=TRUE}
#Using aggregate function to find the total no. of steps taken each day
stepseachday <- aggregate(steps ~ date, activity, sum, na.rm=TRUE)

#Histogram of the total number of steps taken each day
hist(stepseachday$steps,main="Histogram of the total number of steps taken each day",xlab = "Steps taken each day",border = "blue",col="orange",breaks=10)
```

Mean and median number of steps taken each day

```{r,echo=TRUE}
#mean
meanstep<-mean(stepseachday$steps)
meanstep

```
```{r,echo=TRUE}
#median
medianstep<-median(stepseachday$steps)
medianstep

```

**What is the average daily activity pattern?**

```{r,echo=TRUE}
#Using aggregate to average steps and interval 
avgsteps <- aggregate(steps ~ interval, activity, mean, na.rm=TRUE)
head(avgsteps)
```

Time series plot of the 5-minute interval and the average number of steps taken

```{r,echo=TRUE}
library(ggplot2)
g<-ggplot(avgsteps,aes(interval,steps))
g+geom_line(col="chocolate",size=1)+
  ggtitle("Plot of 5 minute interval and Average number of steps taken")+
  xlab("Time")+
  ylab("Average Steps taken")
  
```

Find Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps

```{r,echo=TRUE}
library(dplyr)
#Using the filter function to find which interval had max no.of steps 
avgsteps%>%filter(steps==max(avgsteps$steps))
```

**Imputing missing values**

Calculate and report the total number of missing values in the dataset 

```{r,echo=TRUE}
#Using sum fuction to find the total number of missing values
sum(is.na(activity))
```
Devise a strategy for filling in all of the missing values in the dataset.

```{r,echo=TRUE}
#Creating a column 'filling' to store the filled values
#Here NA value's are replaced with the mean values of the intervals
activity$filling <- ifelse(is.na(activity$steps), round(avgsteps$steps[match(activity$interval, avgsteps$interval)],0), activity$steps)
```

Replace the original dataset with missing data filled.
```{r,echo=TRUE}
#The new dataset with filled missing value is stored in'Newactivity'
Newactivity<-data.frame(steps=activity$filling,interval=activity$interval,date=activity$date)
head(Newactivity,n=10)
```
Histogram of the New dataset

```{r,echo=TRUE}
#Using aggregate function to find the total no. of steps taken each day
Newstepseachday <- aggregate(steps ~ date, Newactivity, sum, na.rm=TRUE)
#Histogram of the total number of steps taken each day
hist(Newstepseachday$steps,main="Histogram of the total number of steps taken each day (New dataset)",xlab = "Steps taken each day",border = "blue",col="orange",breaks=10)
```
Mean and median number of steps taken each day(New dataset)

```{r,echo=TRUE}
#mean
meanstep2<-mean(Newstepseachday$steps)
meanstep2

```
```{r,echo=TRUE}
#median
medianstep2<-median(Newstepseachday$steps)
medianstep2

```

On filling the missing values,the mean did not differ from the original  ,but the median changed by 1.19 % from the original values. 

**Are there differences in activity patterns between weekdays and weekends?**

Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```{r,echo=TRUE}
#Converting character to date format to use the function weekdays
Newactivity$date <- as.Date(Newactivity$date, format = "%Y-%m-%d")
Newactivity$weekday <- weekdays(Newactivity$date)
Newactivity$week <- ifelse(Newactivity$weekday=='Saturday' | Newactivity$weekday=='Sunday', 'weekend','weekday')
head(Newactivity)
```

Make a panel plot containing a time series plot of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).

```{r,echo=TRUE}
#Averaging the no.of steps according to weekday and weekend days.
Weekavg <- aggregate(steps~interval+week,data=Newactivity,mean,na.rm=TRUE)
#line plot
g<-ggplot(Weekavg,aes(interval,steps))
g+geom_line(col="darkgreen",size=1)+
  ggtitle("Time series plot of 5min interval and steps")+
  xlab("Time")+
  ylab("Avg no.of Steps taken")+
  facet_grid(week~.)
  

```
