---
title: ''
output: html_document
---

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:

```r
library(lattice) 
```


```r
#Extract data from a file
data_set <- read.csv("activity.csv", header=T, sep=",")
```

## What is the mean total number of steps taken per day?

1. Make a histogram of the total number of steps taken each day


```r
steps.date <- aggregate(steps ~ date, data_set , FUN = sum)
hist(steps.date$steps, col ='blue', xlab = "Steps in a day",main = "Steps taken for given day",breaks = 20)
```

https://cloud.githubusercontent.com/assets/8277475/3943262/32f2e9b0-2594-11e4-8ded-8aa52500af60.png

2. Calculate and report the **mean** and **median** total number of
   steps taken per day


```r
mean.steps <- mean(steps.date$steps, na.rm = T)
mean.steps
```

```
## [1] 10766
```

```r
median.steps <- median(steps.date$steps, na.rm = T)
median.steps
```

```
## [1] 10765
```

1. Make a time series plot (i.e. `type = "l"`) of the 5-minute
   interval (x-axis) and the average number of steps taken, averaged
   across all days (y-axis)


```r
steps.interval <- aggregate(steps ~ interval, data_set , FUN = mean)
plot(steps.interval, type = "l" ,col ="blue")
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5.png) 

2. Which 5-minute interval, on average across all the days in the
   dataset, contains the maximum number of steps?


```r
steps.interval <- steps.interval$interval[which.max(steps.interval$steps)]
```

## Imputing missing values

1. Calculate and report the total number of missing values in the
   dataset (i.e. the total number of rows with `NA`s)


```r
sum.na <- sum(is.na(data_set))
sum.na
```

```
## [1] 2304
```

2. Devise a strategy for filling in all of the missing values in the
   dataset. The strategy does not need to be sophisticated. For
   example, you could use the mean/median for that day, or the mean
   for that 5-minute interval, etc.

I will use the means for the 5-minute intervals as fillers for missing
values.

3. Create a new dataset that is equal to the original dataset but with
   the missing data filled in.


```r
new_data_set <- data_set
new_data_set$steps[is.na(new_data_set$steps)] <- mean(new_data_set$steps, na.rm = T)
```

4. Make a histogram of the total number of steps taken each day and
   Calculate and report the **mean** and **median** total number of
   steps taken per day. Do these values differ from the estimates from
   the first part of the assignment? What is the impact of imputing
   missing data on the estimates of the total daily number of steps?


```r
new.steps.date <- aggregate(steps ~ date, new_data_set , FUN = sum)
mean(new.steps.date$steps, na.rm = T)
```

```
## [1] 10766
```

```r
median(new.steps.date$steps, na.rm = T)
```

```
## [1] 10766
```

```r
hist(new.steps.date$steps, col ='cyan', xlab = "Steps in a day",main = "Steps taken for given day",breaks = 20)
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9.png) 

## Are there differences in activity patterns between weekdays and weekends?

1. Create a new factor variable in the dataset with two levels --
   "weekday" and "weekend" indicating whether a given date is a
   weekday or weekend day.


```r
new_data_set$day <- weekdays(as.Date(new_data_set$date))
new_data_set$day_type <- ifelse(new_data_set$day == "Saturday" | new_data_set$day == "Sunday", "Weekend", "Weekday")
```

2. Make a panel plot containing a time series plot (i.e. `type = "l"`)
   of the 5-minute interval (x-axis) and the average number of steps
   taken, averaged across all weekday days or weekend days
   (y-axis).


```r
mean.steps <- aggregate(new_data_set$steps, by = list(new_data_set$interval, new_data_set$day_type), mean)
names(mean.steps ) <- c("interval", "day_type", "steps")

xyplot(steps ~ interval | day_type, mean.steps, type = "l", layout = c(1, 2), xlab = "Interval", ylab = "Number of steps")
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11.png) 



