# Reproducible Research: Peer Assessment 1

------


## Loading and preprocessing the data

1. Load the data (i.e. read.csv())


```r
unzip("activity.zip")  # unzip the file
activity <- read.csv("activity.csv")  # read activity data
```

2. Process/transform the data (if necessary) into a format suitable for your analysis

```
No need.
```


## What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in the dataset.

1. Make a histogram of the total number of steps taken each day


```r
steps_by_date <- aggregate(steps ~ date, data = activity, FUN = sum)
barplot(steps_by_date$steps, names.arg=steps_by_date$date, xlab="Date", ylab="Number of steps")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2.png) 

2. Calculate and report the mean and median total number of steps taken per day


```r
mean(steps_by_date$steps)
```

```
## [1] 10766
```

```r
median(steps_by_date$steps)
```

```
## [1] 10765
```


## What is the average daily activity pattern?

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
steps_by_interval <- aggregate(steps ~ interval, data = activity, FUN = mean)
plot(steps_by_interval, type="l",xlab="Interval", ylab="Number of steps")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4.png) 

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
steps_by_interval$interval[which.max(steps_by_interval$steps)]
```

```
## [1] 835
```


## Imputing missing values

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
sum(is.na(activity))
```

```
## [1] 2304
```

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

Using the mean for that 5-minute interval in this case.


```r
na_indexes <- is.na(activity$steps)
activity_merged <- merge(activity, steps_by_interval, by="interval")
activity$steps[na_indexes] <- activity_merged$steps.y[na_indexes]
```

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
steps_by_date <- aggregate(steps ~ date, data = activity, FUN = sum)
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
barplot(steps_by_date$steps, names.arg=steps_by_date$date, xlab="Date", ylab="Number of steps")
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9.png) 

```r
mean(steps_by_date$steps)
```

```
## [1] 10890
```

```r
median(steps_by_date$steps)
```

```
## [1] 11015
```

```
The mean and median differ, but the impact seems to be low.
```

## Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.


```r
Sys.setlocale("LC_TIME", "en_US") # force locale to get the weekdays in English
```

```
## [1] "en_US"
```

```r
activity$weekday_type <- as.factor(ifelse(weekdays(as.Date(activity$date)) %in% c("Saturday", "Sunday"),"weekend","weekday"))
```

2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was creating using simulated data:


```r
steps_weekday_type <- aggregate(steps ~ interval + weekday_type, data=activity, FUN=mean)

library("lattice")
xyplot(steps_weekday_type$steps ~ steps_weekday_type$interval | steps_weekday_type$weekday_type, type = "l", layout = c(1,2),xlab="Interval", ylab="Number of steps")
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11.png) 
