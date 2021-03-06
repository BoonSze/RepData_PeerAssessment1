---
title: "PA1_template"
output: html_document
---

This documents the steps and code for Peer Assignment 1 of the Reproducible Research course.
The graphs can be viewed in the html document.

Load the data


```r
activity <- read.csv("data/activity.csv")
```


###Part 1.  What is the mean total number of steps taken per day
1.  Calculate total number of steps taken each day

```r
temp <- tapply(activity$steps, activity$date, sum, na.rm=TRUE)
df1 <- data.frame(dimnames(temp), as.vector(temp))
colnames(df1) <- c("date", "steps")
```

2.  Make a histogram of the total number of steps taken each day

```r
library(ggplot2)
qplot(df1$steps, geom="histogram", main="Histogram for Total Steps", xlab="Steps", fill=I("blue"), col=I("green"))
```

```
## stat_bin: binwidth defaulted to range/30. Use 'binwidth = x' to adjust this.
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png) 

3.  Calculate and report the mean and median of the total number of steps taken per day

```r
mean(df1$steps)
```

```
## [1] 9354.23
```

```r
median(df1$steps)
```

```
## [1] 10395
```

###Part 2.  What is the average daily activity pattern?
1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
temp <- tapply(activity$steps, activity$interval, mean, na.rm=TRUE)
df2 <- data.frame(dimnames(temp), as.vector(temp))
colnames(df2) <- c("interval", "steps")

g <- ggplot(df2, aes(interval, steps, group=1))
g + geom_line(colour="blue")
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png) 

2.  Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
df2[which.max(df2$steps),]
```

```
##     interval    steps
## 104      835 206.1698
```

###Part 3.  Inputing missing values
1.  Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
sum(is.na(activity$steps))
```

```
## [1] 2304
```

2.  Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

*Answer:* I will use the mean for that 5 min interval, and this information can be obtained from Part 2 of this assignment.

3.  Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
activityNew <- activity
for (i in 1:17568) {
  if (is.na(activityNew$steps[i])) {
    interval <- activityNew$interval[i]
    activityNew$steps[i] <- df2$steps[which(df2$interval == interval)]    
  }
}
```

4.  Make a histogram of the total number of steps taken each day and 

```r
temp <- tapply(activityNew$steps, activityNew$date, sum)
df3 <- data.frame(dimnames(temp), as.vector(temp))
colnames(df3) <- c("date", "steps")

qplot(df3$steps, geom="histogram", main="Histogram for Total Steps without NA", xlab="Steps", fill=I("blue"), col=I("green"))
```

```
## stat_bin: binwidth defaulted to range/30. Use 'binwidth = x' to adjust this.
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9-1.png) 

### Part 4.  Calculate and report the mean and median total number of steps taken per day. 

```r
mean(df3$steps)
```

```
## [1] 10766.19
```

```r
median(df3$steps)
```

```
## [1] 10766.19
```


Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

*Answer: *Yes, the mean and median have increased.

The mean and median now have the same values.

Are there differences in activity patterns between weekdays and weekends?

1.  Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```r
activityNew$weekdays <- ifelse(weekdays(as.Date(activityNew$date)) %in% c('Saturday','Sunday'), "weekend", "weekday")
```

2.  Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

```r
weekdayDf <- subset(activityNew, weekdays == "weekday")
weekendDf <- subset(activityNew, weekdays == "weekend")

temp <- tapply(weekdayDf$steps, weekdayDf$interval, mean)
df4 <- data.frame(dimnames(temp), as.vector(temp))
df4$weekdays <- c(rep("weekday"))
colnames(df4) <- c("interval", "steps", "weekdays")

temp <- tapply(weekendDf$steps, weekendDf$interval, mean)
df5 <- data.frame(dimnames(temp), as.vector(temp))
df5$weekdays <- c(rep("weekend"))
colnames(df5) <- c("interval", "steps", "weekdays")

df6 <- rbind(df4, df5)

g <- ggplot(df6, aes(interval, steps, group = 1))
g + geom_line(colour="blue") + facet_wrap(~weekdays, nrow=2)
```

![plot of chunk unnamed-chunk-12](figure/unnamed-chunk-12-1.png) 
