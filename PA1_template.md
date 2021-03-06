# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data


```r
act <- read.csv("activity.csv")
```

## What is mean total number of steps taken per day?

Make a histogram of the total number of steps taken each day

```r
ds <- aggregate(steps~date,data=act,sum,na.rm=TRUE)
```

```r
hist(ds$steps)
```

![](PA1_template_files/figure-html/histogram-1.png)<!-- -->
Calculate and report the mean and median total number of steps taken per day

```r
mean(ds$steps)
```

```
## [1] 10766.19
```

```r
median(ds$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
is <- aggregate(steps~interval,data=act,mean,na.rm=TRUE)
with(is,plot(interval,steps,type="l"))
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png)<!-- -->
Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
hsi <- subset(is,steps==max(is$steps))
hsi[1,1]
```

```
## [1] 835
```

## Imputing missing values

Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
sum(is.na(act$steps))
```

```
## [1] 2304
```
Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.  
Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
act2 <- act
for(i in 1:nrow(act2)){
    if(is.na(act2$steps[i])==TRUE){
        act2$steps[i] <- is$steps[which(is$interval == act2$interval[i])]
    }
}
```
Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
ds2 <- aggregate(steps~date,data=act2,sum,na.rm=TRUE)
with(ds2,hist(steps))
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

```r
mean(ds2$steps)
```

```
## [1] 10766.19
```

```r
median(ds2$steps)   
```

```
## [1] 10766.19
```

## Are there differences in activity patterns between weekdays and weekends?

Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```r
act2$date <- as.Date(act2$date)
act2$day <- weekdays(act2$date)
for(i in 1:nrow(act2)){
    if(act2$day[i]=="lunes" | act2$day[i]=="martes" | act2$day[i]=="miércoles" | act2$day[i]=="jueves" | act2$day[i]=="viernes"){
        act2$wd_we[i] <- "weekday"
    }
    else{
        act2$wd_we[i] <- "weekend"
    }
}
```
Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).

```r
wd <- subset(act2,wd_we=="weekday")
we <- subset(act2,wd_we=="weekend")
iswd <- aggregate(steps~interval,data=wd,mean,na.rm=TRUE)
iswe <- aggregate(steps~interval,data=we,mean,na.rm=TRUE)
par(mfrow=c(2,1))
with(iswd,plot(interval,steps,type="l",main = "weekday"))
with(iswe,plot(interval,steps,type="l",main = "weekend"))
```

![](PA1_template_files/figure-html/unnamed-chunk-10-1.png)<!-- -->
