# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data
Show any code that is needed to 

- load the data (i.e. read.csv())

- Process/transform the data (if necessary) into a format suitable for 
your analysis

```r
setwd("Coursera/Data Science/5. Reproducible Research/RepData_PeerAssessment1")
```

```
## Error: no es posible cambiar el directorio de trabajo
```

```r
unzip(zipfile = "activity.zip", overwrite = TRUE)
activity <- read.csv("activity.csv")
activity$D <- as.Date(activity$date)
```


## What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in the dataset.
Make a histogram of the total number of steps taken each day
Calculate and report the mean and median total number of steps taken per day


```r

total.steps.vector <- tapply(activity$steps, activity$date, sum, na.rm = TRUE)
hist(total.steps.vector)
```

![plot of chunk meansteps](figure/meansteps.png) 

```r

mean.steps = mean(total.steps.vector, na.rm = TRUE)
mean.steps
```

```
## [1] 9354
```

```r
median.steps = median(total.steps.vector, na.rm = TRUE)
median.steps
```

```
## [1] 10395
```


## What is the average daily activity pattern?

Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) 
and the average number of steps taken, averaged across all days (y-axis)
The intervals are not numbered continuosly, but jump every hour (for example
the first hour has intervals from 0 to 55, the second from 100 to 155, etc.).
So you will have 288 intervals per day (which is the number of 5-minute
intervals in one day)

Which 5-minute interval, on average across all the days in the dataset, 
contains the maximum number of steps?


```r

mean.activity.vector <- tapply(activity$steps, activity$interval, mean, na.rm = TRUE)
activity$mean.interval <- mean.activity.vector
interval.vector <- activity$interval[1:288]
plot(interval.vector, mean.activity.vector, type = "l")
```

![plot of chunk averageactivity](figure/averageactivity.png) 

```r
max.steps <- mean.activity.vector[mean.activity.vector == max(mean.activity.vector)]
max.steps
```

```
##   835 
## 206.2
```


## Imputing missing values

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
missing <- is.na(activity$steps)
length(missing[missing == TRUE])
```

```
## [1] 2304
```


2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
I am going to use the mean of the 5 minute interval.


```r

activity$steps2 <- activity$mean.interval
activity$steps2[!is.na(activity$steps)] <- activity$steps[!is.na(activity$steps)]
```


3. Create a new dataset that is equal to the original dataset but with the missing data filled in.



```r

activity2 <- data.frame(activity$D, activity$interval, activity$steps2)
colnames(activity2) <- c("D", "interval", "steps")
```


4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r

total.steps.vector2 <- tapply(activity2$steps, activity2$D, sum, na.rm = TRUE)
hist(total.steps.vector2)
```

![plot of chunk meansteps2](figure/meansteps2.png) 

```r
mean.steps2 = mean(total.steps.vector2, na.rm = TRUE)
mean.steps2
```

```
## [1] 10766
```

```r
median.steps2 = median(total.steps.vector2, na.rm = TRUE)
median.steps2
```

```
## [1] 10766
```


Yes, there are differences:  

1. The mean changes from 9354.2295 to 1.0766 &times; 10<sup>4</sup>
2. The median changes from ` r median.steps` to 1.0766 &times; 10<sup>4</sup>

## Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
activity2$wd <- as.factor(weekdays(activity2$D))
activity2$is.weekend <- activity2$wd == "sábado" | activity2$wd == "domingo"
```


2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was creating using simulated data:


```r

mean.activity.weekday <- tapply(activity2$steps[activity2$is.weekend == FALSE], 
    activity2$interval[activity2$is.weekend == FALSE], mean, na.rm = TRUE)
mean.activity.weekend <- tapply(activity2$steps[activity2$is.weekend == TRUE], 
    activity2$interval[activity2$is.weekend == TRUE], mean, na.rm = TRUE)
# activity$mean.interval<-mean.activity.vector
par(mfrow = c(2, 1), mar = c(4, 4, 2, 1), oma = c(0, 0, 0, 0))

plot(interval.vector, mean.activity.weekday, type = "l", main = "weekdays", 
    ylab = "number of steps", xlab = "")
plot(interval.vector, mean.activity.weekend, type = "l", main = "weekend", ylab = "number of steps", 
    xlab = "Interval")
```

![plot of chunk averagestepsByWeekend](figure/averagestepsByWeekend.png) 

