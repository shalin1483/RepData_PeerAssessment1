# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data
Show any code that is needed to 
1. Load the data (i.e. read.csv())
2. Process/transform the data (if necessary) into a format suitable for your analysis


```r
setwd("D:/Users/Shalin/R/Coursera/ReproducibleResearch/Assignment1/RepData_PeerAssessment1")
unzip("activity.zip")
dat <- read.csv("activity.csv")
dat$date <- as.Date(dat$date)
head(dat)
```

```
##   steps       date interval
## 1    NA 2012-10-01        0
## 2    NA 2012-10-01        5
## 3    NA 2012-10-01       10
## 4    NA 2012-10-01       15
## 5    NA 2012-10-01       20
## 6    NA 2012-10-01       25
```

## What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in the dataset.

```r
dat.completecases <- na.omit(dat)
```

1. Make a histogram of the total number of steps taken each day.

```r
library(ggplot2)
steps.by.day <- aggregate(steps ~ date, data=dat.completecases, FUN=sum)
ggplot(steps.by.day, aes(x=steps)) + geom_histogram(binwidth=2500, colour="black", fill="skyblue")
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

2. Calculate and report the mean and median total number of steps taken per day.

```r
mean(steps.by.day$steps)
```

```
## [1] 10766.19
```

```r
median(steps.by.day$steps)
```

```
## [1] 10765
```


## What is the average daily activity pattern?

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis).


```r
steps.by.interval <- aggregate(steps ~ interval, data=dat.completecases,FUN=mean)
ggplot(steps.by.interval, aes(x=interval, y=steps)) + geom_line(stat="identity")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png) 

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
steps.by.interval$interval[which.max(steps.by.interval$steps)]
```

```
## [1] 835
```
## Imputing missing values
Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.
1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs).

```r
sum(is.na(dat))
```

```
## [1] 2304
```

```r
sum(is.na(dat$steps))
```

```
## [1] 2304
```
2.Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
dat.new <- dat
dat.new$steps[is.na(dat.new$steps)] <- mean(dat$steps,na.rm=TRUE)
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
steps.by.day.new <- aggregate(steps ~ date, data=dat.new, FUN=sum)
ggplot(steps.by.day.new, aes(x=steps)) + geom_histogram(binwidth=2500, colour="black", fill="skyblue")
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png) 

```r
mean(steps.by.day.new$steps)
```

```
## [1] 10766.19
```

```r
median(steps.by.day.new$steps)
```

```
## [1] 10766.19
```

Mean remained same are imputing data while median increased slightly.

## Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```r
dat.new$day[weekdays(as.Date(dat.new$date)) %in% c("Sunday","Saturday")] <- "weekend"
dat.new$day[!(weekdays(as.Date(dat.new$date)) %in% c("Sunday","Saturday"))] <- "weekday"
dat.new[, "day"] <- as.factor(dat.new[, "day"])
```
2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
steps.new <- aggregate(steps ~ interval + day, data=dat.new, FUN=mean)
ggplot(steps.new, aes(x=interval, y=steps, group=1)) + geom_line() + facet_wrap(~ day, ncol=1)
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png) 
