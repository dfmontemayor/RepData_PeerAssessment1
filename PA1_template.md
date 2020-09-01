---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---

#### *Daniel Montemayor*
#### *August 31st, 2020*

## Introduction

It is now possible to collect a large amount of data about personal movement using activity monitoring devices such as a Fitbit, Nike Fuelband, or Jawbone Up. These type of devices are part of the “quantified self” movement – a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. But these data remain under-utilized both because the raw data are hard to obtain and there is a lack of statistical methods and software for processing and interpreting the data.

This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.

## Data

The data for this assignment can be downloaded from the course web site:

Dataset: Activity monitoring data [52K]
The variables included in this dataset are:

steps: Number of steps taking in a 5-minute interval (missing values are coded as \color{red}{\verb|NA|}NA)
date: The date on which the measurement was taken in YYYY-MM-DD format
interval: Identifier for the 5-minute interval in which measurement was taken
The dataset is stored in a comma-separated-value (CSV) file and there are a total of 17,568 observations in this dataset.

## Assignment

This assignment will be described in multiple parts. You will need to write a report that answers the questions detailed below. Ultimately, you will need to complete the entire assignment in a single R markdown document that can be processed by knitr and be transformed into an HTML file.

Throughout your report make sure you always include the code that you used to generate the output you present. When writing code chunks in the R markdown document, always use \color{red}{\verb|echo = TRUE|}echo = TRUE so that someone else will be able to read the code. This assignment will be evaluated via peer assessment so it is essential that your peer evaluators be able to review the code for your analysis.

For the plotting aspects of this assignment, feel free to use any plotting system in R (i.e., base, lattice, ggplot2)

Fork/clone the GitHub repository created for this assignment. You will submit this assignment by pushing your completed files into your forked repository on GitHub. The assignment submission will consist of the URL to your GitHub repository and the SHA-1 commit ID for your repository state.

NOTE: The GitHub repository also contains the dataset for the assignment so you do not have to download the data separately.

## Loading the data

The zip file was automatically downloaded to the disk when the repository was forked. First, we unzip the data and read it:


```r
if(!file.exists("activity.csv")) {
    unzip(zipfile = "activity.zip")
}
data <- read.csv("activity.csv")
head(data)
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

## Questions

### What is mean total number of steps taken per day?

1. Calculate the total number of steps taken per day

```r
total_steps <- aggregate(steps ~ date, data = data, FUN = sum)
head(total_steps)
```

```
##         date steps
## 1 2012-10-02   126
## 2 2012-10-03 11352
## 3 2012-10-04 12116
## 4 2012-10-05 13294
## 5 2012-10-06 15420
## 6 2012-10-07 11015
```

2. Make a histogram of the total number of steps taken each day

```r
hist(total_steps$steps, xlab = "Number of steps", main = "Total number of steps taken each day")
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

3. Calculate and report the mean and median of the total number of steps taken per day

```r
mean_steps1 <- mean(total_steps$steps, na.rm = TRUE)
paste("Mean of total number of steps taken per day =", mean_steps1)
```

```
## [1] "Mean of total number of steps taken per day = 10766.1886792453"
```

```r
median_steps1 <- median(total_steps$steps, na.rm = TRUE)
paste("Median of total number of steps taken per day =", median_steps1)
```

```
## [1] "Median of total number of steps taken per day = 10765"
```

### What is the average daily activity pattern?

1. Make a time series plot (i.e. \color{red}{\verb|type = "l"|}type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
ave_steps <- aggregate(steps ~ interval, data = data, FUN = mean, na.rm = TRUE)
plot(ave_steps$interval, ave_steps$steps, type = "l", xlab = "Intervals", ylab = "Average steps", main = "Average daily activity pattern")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
max_steps <- ave_steps[which.max(ave_steps$steps),]
paste("5-minute interval with maximum number of steps =", max_steps[,1])
```

```
## [1] "5-minute interval with maximum number of steps = 835"
```

### Imputing missing values
Note that there are a number of days/intervals where there are missing values (coded as \color{red}{\verb|NA|}NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with \color{red}{\verb|NA|}NAs)

```r
na_values <- is.na(data$steps)
paste("Number of missing values in dataset =", sum(na_values))
```

```
## [1] "Number of missing values in dataset = 2304"
```

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
- The strategy can be to replace the NA value with the mean for the that interval.

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
replace_na <- transform(data, steps = ifelse(is.na(data$steps),
                                             ave_steps$steps[match(data$interval,
                                                             ave_steps$interval)],
                                             data$steps))
```

4. Make a histogram of the total number of steps taken each day and calculate and report the mean and median total number of steps taken per day.

```r
total_steps_replaced_na <- aggregate(steps ~ date, replace_na, FUN = sum)
hist(total_steps_replaced_na$steps, xlab = "Number of steps", main = "Total number of steps taken each day")
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

```r
mean_steps2 <- mean(total_steps_replaced_na$steps, na.rm = TRUE)
paste("Mean of total number of steps taken per day =", mean_steps2)
```

```
## [1] "Mean of total number of steps taken per day = 10766.1886792453"
```

```r
median_steps2 <- median(total_steps_replaced_na$steps, na.rm = TRUE)
paste("Median of total number of steps taken per day =", median_steps2)
```

```
## [1] "Median of total number of steps taken per day = 10766.1886792453"
```

Do these values differ from the estimates from the first part of the assignment?

```r
paste("Mean difference =", mean_steps2 - mean_steps1)
```

```
## [1] "Mean difference = 0"
```

```r
paste("Median difference =", median_steps2 - median_steps1)
```

```
## [1] "Median difference = 1.1886792452824"
```

What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
paste("Impact of imputing missing data =", sum(total_steps_replaced_na$steps) - sum(total_steps$steps))
```

```
## [1] "Impact of imputing missing data = 86129.5094339623"
```

### Are there differences in activity patterns between weekdays and weekends?
For this part the \color{red}{\verb|weekdays()|}weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```r
type <- function(date) {
        day <- weekdays(date)
        if(day %in% c("Monday","Tuesday","Wednesday","Thursday","Friday"))
              return("weekday")
        else if(day %in% c("Saturday", "Sunday"))
              return("weekend")
        else
              stop("Invalid date")
}
replace_na$date <- as.Date(replace_na$date)
replace_na$day <- sapply(replace_na$date, FUN = type)
```

2. Make a panel plot containing a time series plot (i.e. \color{red}{\verb|type = "l"|}type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

```r
weekday <- grep("weekday", replace_na$day)
weekday_df <- replace_na[weekday,]
weekend_df <- replace_na[-weekday,]
ave_steps_weekday <- aggregate(steps ~ interval, data = weekday_df, FUN = mean, rm.na = TRUE)
ave_steps_weekend <- aggregate(steps ~ interval, data = weekend_df, FUN = mean, rm.na = TRUE)
plot(ave_steps_weekday$interval, ave_steps_weekday$steps, type = "l", xlab = "Intervals", ylab = "Average steps", main = "Average activity pattern on weekdays")
```

![](PA1_template_files/figure-html/unnamed-chunk-13-1.png)<!-- -->

```r
plot(ave_steps_weekend$interval, ave_steps_weekend$steps, type = "l", xlab = "Intervals", ylab = "Average steps", main = "Average activity pattern on weekends")
```

![](PA1_template_files/figure-html/unnamed-chunk-13-2.png)<!-- -->
