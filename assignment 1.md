---
title: "Reproducible Research - Assignment 1"
author: "JL"
date: "13/02/2020"
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

Create the plot 
```{r}
hist(steps_per_day$steps, main = "Total Steps per day", xlab = "Steps", ylim = c(0,40), labels = TRUE)
```

The mean of total steps taken per day
```{r}
mean(steps_per_day$steps)
```

The median of total steps taken per day
```{r}
median(steps_per_day$steps)
```

## What is the average daily activity pattern?

Aggreate the time series data and remove NA's

```{r}
mean_steps_interval <- aggregate(steps ~ interval, data, mean, na.rm = TRUE)
```

Use the aggregate data to create the time series plot
```{r}
plot(mean_steps_interval$interval, mean_steps_interval$steps, type = "l", main = "Average number of steps per 5-min interval", xlab = "Interval", ylab = "Average Steps")
```

To find our the 'peak' 5-minute interval which contains the maximum number of steps (on average across all days)
```{r}
max_interval <- mean_steps_interval$interval[which(mean_steps_interval$steps == max(mean_steps_interval$steps))]
max_interval
```

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.
