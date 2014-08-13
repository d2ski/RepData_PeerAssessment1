# Reproducible Research: Peer Assessment 1

## Loading and preprocessing the data

```r
data <- read.csv(unz("activity.zip", "activity.csv"), colClasses = c("numeric",                                                               "character", "numeric"))

##data$date <- strptime(data$date, format = "%Y-%m-%d", tz = "GMT")
```


```
## 
## Attaching package: 'dplyr'
## 
## Следующие объекты скрыты from 'package:stats':
## 
##     filter, lag
## 
## Следующие объекты скрыты from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

## What is mean total number of steps taken per day?


```r
data1 <- data %.%
    group_by(date) %.%
    summarise(total = sum(steps))
hist(data1$total, main = "Histogram of the total number of steps per day",
     xlab = "total number of steps taken each day")
```

![plot of chunk stepshist](figure/stepshist.png) 


```r
mean(data1$total, na.rm = T) -> tstepsmean
print(tstepsmean)
```

```
## [1] 10766
```
The mean total number of steps taken per day is 1.0766 &times; 10<sup>4</sup>.

```r
median(data1$total, na.rm = T) -> tstepsmedian
print(tstepsmedian)
```

```
## [1] 10765
```
The median total number of steps taken per day is 1.0765 &times; 10<sup>4</sup>.  



## What is the average daily activity pattern?
### Make a time series plot of the 5-minute interval and the average number of steps


```r
data2 <- data %.%
    group_by(interval) %.%
    summarise(mean = mean(steps, na.rm = T))
plot(data2$interval, data2$mean, type = "l", main = "Time Series Plot per
     5-minute interval", xlab = "5-minute intervals", ylab = "Average number of
     steps taken")
```

![plot of chunk timeseries](figure/timeseries.png) 

### Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
data2[which.max(data2$mean), "interval"]
```

```
## [1] 835
```

## Imputing missing values

### Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
sum(is.na(data$steps))
```

```
## [1] 2304
```

### Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

The strategy will be to fill the missing values with the mean for that 5-minute interval from `data2` data frame.

### Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
new.data <- data
for (value in 1:length(data$steps)) {
    
    if (is.na(data[value, "steps"])) {
        new.data[value, "steps"] <- data2[value, "mean"]
    }
}
```
### Make a histogram of the total number of steps taken each day


```r
new.data1 <- new.data %.%
    group_by(date) %.%
    summarise(total = sum(steps))
hist(new.data1$total, main = "Histogram of the total number of steps per day",
     xlab = "total number of steps taken each day")
```

![plot of chunk newstepshist](figure/newstepshist.png) 

### Calculate and report the mean and median total number of steps taken per day

```r
mean(new.data1$total) -> new.tstepsmean
print(new.tstepsmean)
```

```
## [1] NA
```
The mean total number of steps taken per day is NA.

```r
median(new.data1$total) -> new.tstepsmedian
print(new.tstepsmedian)
```

```
## [1] NA
```
The median total number of steps taken per day is NA.  

## Are there differences in activity patterns between weekdays and weekends?
