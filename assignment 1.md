---
title: "Reproducible Research - Assignment 1"
author: "Jeffrey Lou"
date: "17/02/2020"
output: html_document
keep_md: true
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## Loading and preprocessing the data

Load the relevant packages, then process and transform data for analysis

```{r}
library(knitr)
library(ggplot2)
data<-read.csv('activity.csv')
data<-transform(data, date= as.Date(date))
```

## What is mean total number of steps taken per day?

First, aggregate the table for total steps per day.  Also remove NA's

```{r}
steps_per_day <- aggregate(steps ~ date, data, sum, na.rm = TRUE)
```

Create the plot (see Figure 1)
```{r}
hist(steps_per_day$steps, main = "Figure 1: Total Steps per day", xlab = "Steps", ylim = c(0,40), labels = TRUE)
```

The average number of steps taken each day was around 10,766 steps.
```{r}
mean(steps_per_day$steps)
```


The median number of steps taken each day was 10,765 steps.
```{r}
median(steps_per_day$steps)
```

## What is the average daily activity pattern?

Aggreate the time series data and remove NA's

```{r}
mean_steps_interval <- aggregate(steps ~ interval, data, mean, na.rm = TRUE)
```

Use the aggregate data to create the time series plot (see Figure 2)
```{r}
plot(mean_steps_interval$interval, mean_steps_interval$steps, type = "l", main = "Figure 2: Average number of steps per 5-min interval", xlab = "Interval", ylab = "Average Steps")
```

To find our the 'peak' 5-minute interval which contains the maximum number of steps (on average across all days)
```{r}
max_interval <- mean_steps_interval$interval[which(mean_steps_interval$steps == max(mean_steps_interval$steps))]
max_interval
```

## Imputing missing values

First, calculate and report the total number of missing values in the dataset. 2,304 missing values were found.

```{r}
sum(is.na(data))
```

Then replace all the missing values with the mean of the 5-minute interval.

```{r}
imputed_data <- data
for (i in 1:length(imputed_data$steps)) {
  if (is.na(imputed_data$steps[i])) {
    imputed_data$steps[i] <- mean_steps_interval$steps[mean_steps_interval$interval == imputed_data$interval[i]]
  }
}
```

Aggregate the 'revised' dataset and ensure there is no NA values

```{r}
imp_steps_per_day <- aggregate(steps ~ date, imputed_data, sum, na.rm = TRUE)
sum(is.na(imp_steps_per_day$steps))
```

Create the plot with the imputted data (see Figure 3)
```{r}
hist(imp_steps_per_day$steps, main = "Figure 3: Total Steps per day (based on imputted data)", xlab = "Steps", ylim = c(0,40), labels = TRUE)
```

## Are there differences in activity patterns between weekdays and weekends?

Add a new factor variable to the dataset, which is either weekdays or weekends, then create aggregate for plot

```{r}
imputed_data$date <- as.Date(imputed_data$date)
imputed_data$wkdy <- "weekday"
imputed_data$wkdy[weekdays(imputed_data$date) == "Saturday" | weekdays(imputed_data$date) == "Sunday"] <- "weekend"
imputed_data$wkdy <- as.factor(imputed_data$wkdy)
imputed_data_interval <- aggregate(steps ~ interval + wkdy, imputed_data, mean, na.rm = TRUE)
```

Create the panel plot (see Figure 4)

```{r}
g <- ggplot(imputed_data_interval, aes(interval, steps))
g + facet_grid(wkdy ~ .) + geom_line() + ggtitle("Figure 4: Average number of steps per 5-min interval")
```

The answer to the question is: yes, there are differences in activity patterns between weekdays and weekends.  Figure 4 shows that the step activity trends are different, depending on whether the day falls into a weekend or not.  There could be a variety of reasons to explain the differences, such as working people having more opportunities to exercise out of working hours.
