# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data
First time to load some stuff

```r
library(ggplot2)
library(plyr)
```

Load the data and take a sneak-peek into it

```r
x <- read.csv('activity.csv')
head(x)
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
Let's first generate histogram for steps:

```r
hist(x[!is.na(x$steps),1], breaks=40, ylab="frequency", xlab="steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

Then mean:

```r
mean(x[!is.na(x$steps),1])
```

```
## [1] 37.3826
```

and finally median:

```r
median(x[!is.na(x$steps),1])
```

```
## [1] 0
```

## What is the average daily activity pattern?
Now let's cut down x to interval==5, calculate averages, then plot:

```r
y <- x[!is.na(x$steps),]
z <- ddply(y[,c(1,3)], .(interval), summarize, mean=mean(steps))
plot(z$interval,z$mean,type="l",ylab="average of steps",xlab="interval")
```

![](PA1_template_files/figure-html/unnamed-chunk-6-1.png) 

And as we can see the greatest average number of steps is in interval:

```r
z$interval[z$mean == max(z$mean)]
```

```
## [1] 835
```


## Imputing missing values
There is a number of steps values missing:

```r
sum(as.numeric(is.na(x$steps)))
```

```
## [1] 2304
```

As I can use average for that particular interval as fill-in I will do just that (as I have it already):

```r
x2 <- x
for (i in 1:length(x2[,1])) {
  if (is.na(x2$steps[i]))
    x2$steps[i] <- z$mean[z$interval == x2$interval[i]]
}
```

Now let's repeat with new dataset histogram:

```r
hist(x2[,1], breaks=40, ylab="frequency", xlab="steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-10-1.png) 

Then mean:

```r
mean(x2[!is.na(x2$steps),1])
```

```
## [1] 37.3826
```

and finally median:

```r
median(x2[!is.na(x2$steps),1])
```

```
## [1] 0
```

So the graph does differ a little bit, but neither median nor mean does - in case of a mean it looks weird a bit.


## Are there differences in activity patterns between weekdays and weekends?
Now let's add weekdays information:

```r
x2$wkd <- weekdays(as.Date.factor(x2$date))
x2$wkd[x2$wkd == "Sunday" | x2$wkd == "Saturday"] <- "weekend"
x2$wkd[x2$wkd != "weekend"] <- "weekday"
```

And so the unnecessary panel plot of itnervals happens here:

```r
par(mfrow=c(1,2)) 
z2we <- ddply(x2[x2$wkd=="weekend",c(1,3)], .(interval), summarize, mean=mean(steps))
z2wd <- ddply(x2[x2$wkd=="weekday",c(1,3)], .(interval), summarize, mean=mean(steps))
plot(z2we$interval,z2we$mean,main="Weekend",type="l",ylab="average of steps",xlab="interval")
plot(z2wd$interval,z2wd$mean,main="Weekday",type="l",ylab="average of steps",xlab="interval")
```

![](PA1_template_files/figure-html/unnamed-chunk-14-1.png) 
