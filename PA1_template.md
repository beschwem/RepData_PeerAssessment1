# Reproducible Research: Peer Assessment 1
========================================================



## Loading and preprocessing the data

```r
setwd("~/")
activity = read.csv("activity.csv")
activity2 = na.omit(activity)
```


## What is the mean total number of steps taken per day?

Histogram of the total number of steps each day

```r
library(plyr)
# Sum the number of steps per day
stepsday = ddply(activity2, "date", summarize, steps = sum(steps))
# Plot the histogram
hist(stepsday$steps, xlab = "Steps/Day", main = "")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2.png) 


Mean and Median Steps per day

```r
mean(stepsday$steps)
```

```
## [1] 10766
```

```r
median(stepsday$steps)
```

```
## [1] 10765
```


## What is the average daily activity pattern?

Time series plot of the 5 minute intervals and average number of steps per day, averaged across all days.

```r
# Make a data frame with the mean steps over the intervals
stepsday = ddply(activity2, "interval", summarize, steps = mean(steps))
# Time series plot of intervals and mean steps per day
plot(stepsday$interval, stepsday$steps, type = "l", xlab = "Interval", ylab = "Mean Steps/Day", 
    main = "")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4.png) 


Which interval has the maximum number of steps?

```r
stepsday$interval[max(stepsday$steps)]
```

```
## [1] 1705
```


## Imputing missing values

Calculate and report the total number of rows with NAs

```r
nrow(activity[!complete.cases(activity), ])
```

```
## [1] 2304
```


Create a new dataset with missing values filled in.

```r
library(mice)
```

```
## Loading required package: Rcpp
## mice 2.21 2014-02-05
```

```r
# Imputed values using the mice package
activity2 = mice(activity, printFlag = F)
```


Histogram of the total number of steps each day and calculate the mean and median.

```r
# Make a data frame with the sum of all steps for each day
stepsday = ddply(complete(activity2), "date", summarize, steps = sum(steps))
# Make the histogram
hist(stepsday$steps, xlab = "Steps per Day", main = "")
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8.png) 

```r
mean(stepsday$steps)
```

```
## [1] 11131
```

```r
median(stepsday$steps)
```

```
## [1] 11352
```

 
## Are there differences in activity patterns between weekdays and weekends?

Create a new factor variable in the dataset with two levels weekday and weekend.

```r
activity2 = complete(activity2)
activity2$date = as.Date(activity2$date)
activity2$date = weekdays(activity2$date)
activity2$weekend = ifelse(activity2$date == "Sunday" | activity2$date == "Saturday", 
    "Weekend", "Weekday")
```


Time series, panel plot of 5 minute interval and average number of steps taken averaged across weekends and weekdays.

```r
# Make a data frame with the average steps per Weekday and Weekend day
stepsday = aggregate(activity2$steps, by = list(activity2$interval, activity2$weekend), 
    FUN = mean)
library(ggplot2)
# Plot the average steps weekend vs weekday
qplot(Group.1, x, data = stepsday, facets = . ~ Group.2, xlab = "Interval", 
    ylab = "Mean Steps/Day", geom = c("line", "smooth"), method = "", se = FALSE)
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10.png) 





