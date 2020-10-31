---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---

## Loading and preprocessing the data
Reading the unzipped data using read.csv

```r
activity <- read.csv("activity.csv")
```
Calculating the sum of steps per day, removing the NA steps

```r
totalStepsPerDay <- tapply(activity$steps,activity$date,sum,na.rm=TRUE)
hist(totalStepsPerDay,main="Total Steps Per Day Histogram",xlab="steps",col = "red",breaks = seq(0,25000, by=2500),ylim=c(0,20))
```

![](PA1_template_files/figure-html/Sum of Steps Per day-1.png)<!-- -->

## What is mean total number of steps taken per day?
The mean number of steps taken per day

```r
median(totalStepsPerDay)
```

```
## [1] 10395
```
The median number of steps taken per day

```r
mean(totalStepsPerDay)
```

```
## [1] 9354.23
```

## What is the average daily activity pattern?
Once we have NA steps, we must remove to calculate the mean by date.  

```r
averageStepsPerDay <- tapply(activity$steps,activity$date,mean,na.rm=TRUE)
averageStepsPerDay <- averageStepsPerDay[!is.na(averageStepsPerDay)]
plot(as.Date(names(averageStepsPerDay)),averageStepsPerDay,xlab="Date",ylab="Average Steps Per Day",type="l")
```

![](PA1_template_files/figure-html/mean by day-1.png)<!-- -->

Do the same for 5 min inverval.  

```r
averageStepsPerInterval <- tapply(activity$steps,activity$interval,mean,na.rm=TRUE)
plot(unique(activity$interval),averageStepsPerInterval,xlab="5 min Interval",ylab="Average Steps Per 5-Minute Interval",type="l")
```

![](PA1_template_files/figure-html/mean by 5-min interval-1.png)<!-- -->

The interval and value of max step.  

```r
which.max(averageStepsPerInterval)
```

```
## 835 
## 104
```
## Imputing missing values
First I identify the NA and using is.na.

```r
missingIndex<-is.na(activity$steps)
```
Second calculate the average of without the NA values using na.rm=TRUE of mean function

```r
m <-mean(activity$steps,na.rm=TRUE)
```
Third substitute the NA value by the average calculated in second step.

```r
activity$steps[missingIndex] <- m
```

## Are there differences in activity patterns between weekdays and weekends?
First we tried to see if have any diffence just by the mean of the days of week

```r
activity$weekday<-weekdays(as.Date(activity$date))
averageStepbyWeekday <- tapply(activity$steps,activity$weekday,mean)
barplot(averageStepbyWeekday[unique(activity$weekday)],ylim=c(0,50),main="Average Step by Weekday")
```

![](PA1_template_files/figure-html/unnamed-chunk-1-1.png)<!-- -->

Once this isn't possible, we must identify and separate the days of week

```r
week <- unique(activity$weekday)
weekDay <- week[1:5]
weekEnd <- week[6:7]
```

Separate the data by weekdays and calculate the average by interval

```r
activityStepsWeekDay <- activity$steps[activity$weekday %in% weekDay]
activityStepsWeekEnd <- activity$steps[activity$weekday %in% weekEnd]
intervalWeekDay <- activity$interval[activity$weekday %in% weekDay]
intervalWeekEnd <- activity$interval[activity$weekday %in% weekEnd]
averageStepsPerIntervalByWeekDay <- tapply(activityStepsWeekDay,intervalWeekDay ,mean)
averageStepsPerIntervalByWeekEnd <- tapply(activityStepsWeekEnd,intervalWeekEnd,mean)
plot(unique(intervalWeekDay),averageStepsPerIntervalByWeekDay,xlab="5 min Interval",ylab="Average Steps Per 5-Minute Interval",type="l",main="Weekdays")
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

```r
plot(unique(intervalWeekEnd),averageStepsPerIntervalByWeekEnd,xlab="5 min Interval",ylab="Average Steps Per 5-Minute Interval",type="l",main="Weekend")
```

![](PA1_template_files/figure-html/unnamed-chunk-2-2.png)<!-- -->
