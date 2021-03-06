title: "Reproducible Research - Course Project 1: Activity monitoring"
author: "Pranav"
date: "29 july 2021"
output: html_document
---

Synopsis
It is now possible to collect a large amount of data about personal movement using activity monitoring devices such as a Fitbit, Nike Fuelband, or Jawbone Up. These type of devices are part of the "quantified self" movement -- a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. But these data remain under-utilized both because the raw data are hard to obtain and there is a lack of statistical methods and software for processing and interpreting the data.

This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.

###Loading and preprocessing the data This part of the code is necessary for the inital loading of the dataset

```{r}
    #Reads the data
    data = read.csv('activity.csv')
    data$date = as.Date(data$date, "%Y-%m-%d")
    #Splits the data per date
    splitDay = split(data, data$date)
```

    
###What is mean total number of steps taken per day?
```{r}
    #Total number of steps/Median per day
    totalSteps = sapply(splitDay, function(x) sum(x$steps))
    medianSteps = sapply(splitDay, function(x) median(x$steps))
    hist(totalSteps,main="Histogram of Frequency of TotalSteps/Day")
```
Total number of steps/day
```{r}
    totalSteps
```
Median number of steps/day
```{r}
    medianSteps
```

###What is the average daily activity pattern? The code initializes the dataset and creates a plot to find the average steps vs time interval
```{r}
    #Preallocate vector
    interval = seq(0,2355,5)
    steps = numeric()
    for(i in 1:472) {
        steps[i] = 0
    }
    avgSteps = data.frame(interval, steps)
    names(avgSteps) = c('interval','avgsteps')
    
    #Adds the mean data in for the 5 minute interval
    for (i in 1:472){
        avgSteps$avgsteps[i] = mean(data$steps[data$interval == interval[i]], na.rm=TRUE)
    }
    
    #Plots the avgsteps vs time interval
    plot(avgSteps, type='l', main='Avg. Steps over 5 minute intervals',xlab='Interval(minutes)',
         ylab='Average Steps')
    
    #Finds the interval with the highest avg
    avgSteps$interval[which.max(avgSteps$avgsteps)]
```

The interval with the highest average is r avgSteps$interval[which.max(avgSteps$avgsteps)]

##Imputing missing values
```{r}
    #Counts number of na values in steps
    numNa = sum(is.na(data$steps))
```

The number of NA values in the dataset is r numNa
All NA values will be replaced with 0

```{r}
    #Replaces NA with 0
    noNa = data
    noNa[is.na(noNa)]=0
    #Splits the data per date
    splitNa = split(noNa, noNa$date)
    #Average steps/median
    totalNa = sapply(splitNa, function(x) sum(x$steps))
    #Plots histogram of the number of total steps per day
    hist(totalNa,main="Histogram of Frequency of TotalSteps/Day", xlab="Total number of Steps")
    #Average/mean across all values
    avgNa = sapply(splitNa, function(x) mean(x$steps))
    medNa = sapply(splitNa, function(x) median(x$steps))
```

The following is the adjusted average values after changing all NA values to 0
```{r}
    avgNa
```

The following is the adjusted median values after chaning all NA values to 0
```{r}
    medNa
```

###Are there differences in activity patterns between weekdays and weekends?

The following creates subsets containing the weekday/weekend values of the dataset
```{r}
    #Shows data of weekday and weekend values
    weekData = data
    weekData[is.na(weekData)]=0    
    weekData$days = weekdays(weekData$date)
    weekData$days = as.factor(ifelse(weekdays(weekData$date) %in% c("Saturday","Sunday"), 
                                      "Weekend", "Weekday")) 
    avgEnd = mean(weekData$steps[weekData$days == 'Weekend'])
    avgDay = mean(weekData$steps[weekData$days == 'Weekday'])
    #Subset weekday and weekend values
    weekdays = subset(weekData, weekData$days == 'Weekday')
    weekends = subset(weekData, weekData$days == 'Weekend')
    
    #Preallocate vector
    weekend = numeric()
    weekday = numeric()
    for(i in 1:472) {
        weekday[i] = 0
        weekend[i] = 0
    }
    avgWeek = data.frame(interval, weekend, weekday)
    names(avgWeek) = c('interval','avgsteps.weekend','avgsteps.weekday')
    
    #Adds the mean data in for the 5 minute interval
    for (i in 1:472){
        avgWeek$avgsteps.weekday[i] = mean(weekdays$steps[weekdays$interval == interval[i]], na.rm=TRUE)
        avgWeek$avgsteps.weekend[i] = mean(weekends$steps[weekends$interval == interval[i]], na.rm=TRUE)
    }
```

The plot is setup to show the differences in the average steps vs time period between weekends and weekdays
```{r}
    #Creates the plots
    par(mfrow=c(2,1))
    plot(avgWeek$interval, avgWeek$avgsteps.weekend, type='l', main ="Weekend vs Avg Steps",
         xlab="Interval(steps)", ylab="Average Steps")
    plot(avgWeek$interval, avgWeek$avgsteps.weekday, main ="Weekday vs Avg Steps", 
         xlab="Interval(steps)", ylab="Average Steps", type='l')
```
?? 2021 GitHub, Inc.
