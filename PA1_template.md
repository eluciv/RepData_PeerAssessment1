Loading and preprocessing the data
----------------------------------

Show any code that is needed to

1.  Load the data (i.e. read.csv())
2.  Process/transform the data (if necessary) into a format suitable for
    your analysis

<!-- -->

    library(lattice)
    data <- read.csv("./activity.csv")
    data$date <- as.Date(data$date)

What is mean total number of steps taken per day?
-------------------------------------------------

For this part of the assignment, you can ignore the missing values in
the dataset.

1.  Calculate the total number of steps taken per day

<!-- -->

    totalNumOfStepsPerDay <- aggregate(steps ~ date, data = data, sum)

1.  If you do not understand the difference between a histogram and a
    barplot, research the difference between them. Make a histogram of
    the total number of steps taken each day

<!-- -->

    histogram(~steps, data = totalNumOfStepsPerDay, xlab = 'Number of steps per day', main = 'Total number of steps per day')

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-3-1.png)

1.  Calculate and report the mean and median of the total number of
    steps taken per day

<!-- -->

    mean(totalNumOfStepsPerDay$steps, na.rm = TRUE)

    ## [1] 10766.19

    median(totalNumOfStepsPerDay$steps, na.rm = TRUE)

    ## [1] 10765

What is the average daily activity pattern?
-------------------------------------------

1.  Make a time series plot (i.e. type = "l") of the 5-minute interval
    (x-axis) and the average number of steps taken, averaged across all
    days (y-axis)

<!-- -->

    avgPattern <- aggregate(steps ~ interval, data = data, FUN = mean)
    xyplot(steps ~ interval, data = avgPattern, type = "l", main = "Average daily activity pattern", xlab = "Interval", ylab = "Steps")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-6-1.png)

1.  Which 5-minute interval, on average across all the days in the
    dataset, contains the maximum number of steps?

<!-- -->

    maxAvgSteps <- max(avgPattern$steps)
    avgPattern$interval[ which(avgPattern$steps == maxAvgSteps) ]

    ## [1] 835

Imputing missing values
-----------------------

Note that there are a number of days/intervals where there are missing
values (coded as NA). The presence of missing days may introduce bias
into some calculations or summaries of the data.

1.  Calculate and report the total number of missing values in the
    dataset (i.e. the total number of rows with NAs)

<!-- -->

    length(data$steps[which(is.na(data$steps))])

    ## [1] 2304

1.  Devise a strategy for filling in all of the missing values in the
    dataset. The strategy does not need to be sophisticated. For
    example, you could use the mean/median for that day, or the mean for
    that 5-minute interval, etc.
2.  Create a new dataset that is equal to the original dataset but with
    the missing data filled in.

<!-- -->

    enrichedData <- data.frame(date = data$date, interval = data$interval, steps = ifelse(is.na(data$steps), avgPattern$steps, data$steps))

1.  Make a histogram of the total number of steps taken each day and
    Calculate and report the mean and median total number of steps taken
    per day. Do these values differ from the estimates from the first
    part of the assignment? What is the impact of imputing missing data
    on the estimates of the total daily number of steps?

<!-- -->

    totalEnrichedNumOfStepsPerDay <- aggregate(steps ~ date, data = enrichedData, sum)
    histogram(~steps, data = totalEnrichedNumOfStepsPerDay, xlab = 'Number of steps per day', main = 'Total number of steps per day')

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-10-1.png)

    mean(totalEnrichedNumOfStepsPerDay$steps)

    ## [1] 10766.19

    median(totalEnrichedNumOfStepsPerDay$steps)

    ## [1] 10766.19

We can see that now there are more days with number of steps close to
average number of steps per day. The mean and the median values have
almost not changed, but they became even closer to each other.

Are there differences in activity patterns between weekdays and weekends?
-------------------------------------------------------------------------

For this part the weekdays() function may be of some help here. Use the
dataset with the filled-in missing values for this part.

1.  Create a new factor variable in the dataset with two levels -
    "weekday" and "weekend" indicating whether a given date is a weekday
    or weekend day.

<!-- -->

    data$weekday <- factor((weekdays(data$date) %in% c('Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday')), levels = c(FALSE, TRUE), labels = c('Weekend', 'Weekday'))

1.  Make a panel plot containing a time series plot (i.e. type = "l") of
    the 5-minute interval (x-axis) and the average number of steps
    taken, averaged across all weekday days or weekend days (y-axis).
    See the README file in the GitHub repository to see an example of
    what this plot should look like using simulated data.

<!-- -->

    avgPatternByWeekday <- aggregate(steps ~ interval + weekday, data = data, FUN = mean)
    xyplot(steps ~ interval | weekday, data = avgPatternByWeekday, main = "Average daily activity patterns for weekdays and weekends", xlab = "Interval", ylab = "Steps", type = "l", layout = c(1, 2))

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-14-1.png)
