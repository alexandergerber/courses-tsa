---
title: 'Import and Visualize Time Series Data'
description: ""
free_preview: true
---

## Importing Data

```yaml
type: NormalExercise
key: 7e8b138317
xp: 100
```

Before we can start to analyse a time series we have to load data into R. A nice and easy way to do that is provided 
by the `quantmod` package. The package contains the function `getSymbols()` which can be used to import data from Yahoo Finance (`src = "yahoo"`) 
and the Federal Reserve Bank of St. Louis (`src = "FRED"`). 

For example ,to import DAX data from Yahoo Finance we have to find the symbol Yahoo uses for the DAX. You can find the symbol by using 
a search engine (search e.g. for Yahoo Finance DAX). Then we can pass this symbol as a **string** to `getSymbols(.,src = "yahoo", auto.assign = FALSE)` (replace the dot by the symbol) and assign the result to a variable using `<-`.

`@instructions`
- Verify that the Yahoo Finance symbol for the DAX is `^GDAXI`. 
- Load the `quantmod` package with `library()`.
- Import the DAX data by passing the symbol as a string to `getSymbols(.,auto.assign = FALSE)` and assign the result to the variable `DAX`.

`@hint`
- If you want to pass something as a string you have to use ""

`@pre_exercise_code`
```{r}

```

`@sample_code`
```{r}
# Load the quantmod package

# Import the Data with getSymbols()


```

`@solution`
```{r}
# Load the quantmod package
library(quantmod)
# Import the data
DAX <- getSymbols("^GDAXI", src = "yahoo", auto.assign = FALSE)
```

`@sct`
```{r}
ex() %>% check_library("quantmod")
success_msg("Great!")
```

---

## Inspect the Downloaded Data

```yaml
type: NormalExercise
key: e68207ca67
xp: 100
```

The DAX data from the previous exercise are still loaded. You can check this by using `ls()`, which will list all variables in the working environment. Follow the instructions to find out more about the data.

`@instructions`
- Use `start()` to find out when the time series begins.
- Use `end()` to find out when the time series ends.
- Use `head()` to print out the first few rows.
- Use `tail()` print out the last few rows.

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
DAX <- getSymbols("^GDAXI", auto.assign = FALSE)
```

`@sample_code`
```{r}
# Print out the start date of the time series dataset

# Print out the end date of the time series dataset

# Print out the first few rows  

# Print out the last few rows  


```

`@solution`
```{r}
# Print out the start date of the time series dataset
start(DAX)
# Print out the end date of the time series dataset
end(DAX)
# Print out the first rows  
head(DAX)
# Print out the last rows  
tail(DAX)

```

`@sct`
```{r}
ex() %>% check_function("start") %>% check_arg("x") %>% check_equal()
ex() %>% check_function("end") %>% check_arg("x") %>% check_equal()
ex() %>% check_function("head") %>% check_arg("x") %>% check_equal()
ex() %>% check_function("tail") %>% check_arg("x") %>% check_equal()
success_msg("Great!")
```

---

## Subset the downloaded data

```yaml
type: NormalExercise
key: 62f191b4b5
xp: 100
```

You found out by printing the first few rows that the dataset consists of more than one time series (namely Open, High, Low, Close, Volume and Adjusted).  Each time series is contained in one column. We can extract one column by either using a numerical index, the column name or the `$` notation (notice that this works exactly as with data frames).

`@instructions`
- Use `names()` to find the name of the time series of daily closing prices.
- Use a subsetting methods to extract the closing prices and assign the result to a new variable called `close_daily`. 
- Plot the single time series `close_daily` by calling `plot()`.

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
DAX <- getSymbols("^GDAXI",auto.assign = FALSE)
```

`@sample_code`
```{r}
# Find the column names

# Extract the closing prices 

# Plot the closing prices


```

`@solution`
```{r}
# Find the column names
names(DAX)
# Extract the closing prices 
close_daily <- DAX[ ,"GDAXI.Close"]
# Plot the closing prices
plot(close_daily)

```

`@sct`
```{r}
ex() %>% check_function("names") %>% check_arg("x") %>% check_equal()
ex() %>% check_object("close_daily") %>% check_equal()
success_msg("Great!")
```

---

## Irregular vs. Regular Time Series

```yaml
type: NormalExercise
key: 335ac38015
xp: 100
```

The downloaded `DAX` dataset is of class `xts`, which is an elaborate way of dealing with time series in R. 
For our purposes it is enough to know that `xts` can handle irregular time series, which means time series with non-constant time increments. This is useful for stock data since we do not have observations for night hours and for e.g. weekends and holidays.
However, in this course we will work on regular time series only. We can use the function `to.monthly()` to convert our `xts` series of daily data to monthly data. Since we get one observation for each month of the year, we can treat the newly generated series as a regular time series.

`@instructions`
- Use the function `class()` to make sure that `DAX`  is of class `xts`.
- Convert `DAX` to a monthly series and assign it to the variable `DAX_monthly`.
- Create a variable `close_monthly` with the monthly closing prices. 
- Plot `close_monthly` using the function `plot()`.

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
DAX <- getSymbols("^GDAXI",auto.assign = FALSE)
```

`@sample_code`
```{r}
# Find out the class of the downloaded object

# Convert the DAX data set from daily data to monthly data

# Extract the monthly closing prices

# Plot the monthly cloasing prices

```

`@solution`
```{r}
# Find out the class of the downloaded object
class(DAX)
# Convert the DAX data set from daily data to monthly data
DAX_monthly <- to.monthly(DAX)
# Extract the monthly closing prices
close_monthly <- DAX_monthly$DAX.Close
# Plot the monthly cloasing prices
plot(close_monthly)
```

`@sct`
```{r}
ex() %>% check_function("to.monthly") %>% check_arg("x") %>% check_equal()
ex() %>% check_object("close_monthly") %>% check_equal()
ex() %>% check_function("plot") %>%  check_arg("x") %>% check_equal()
success_msg("Great!")
```

---

## Class ts

```yaml
type: NormalExercise
key: eb87762070
xp: 100
```

For the rest of the course we will work with time series of class `ts`. This is a class for only regular time series. 
Most of the methods we are using in this course require time series in this format. We can convert an `xts` object to an object of class `ts`
by using the function `ts()`. Unfortunately, the time indices are not converted and we have to add them manually. For this it is enough to 
specify the start date and the so called frequency, which is the number of observation per "cycle". 

For example: 
- a monthly time series starting in April 2016

	```
	ts(timeSeriesData, start = c(2016, 4), frequency = 12)
	```
- a quarterly time series starting in the second quarter 2013

	```
	ts(timeSeriesData, start = c(2013, 2), frequency = 4)
	```

`@instructions`
- Convert `close_monthly` to an object of class `ts` and assign it to the variable `close`. Make sure to specify `start` and `frequency` accordingly. 
- Plot `close`.

`@hint`
- Use e.g. `head(close_monthly)` or `start(close_mothly)` to find the starting date.

`@pre_exercise_code`
```{r}
library(quantmod)
DAX <- getSymbols("^GDAXI",auto.assign = FALSE)
DAX_monthly <- to.monthly(DAX)
close_monthly <- DAX_monthly$DAX.Close
```

`@sample_code`
```{r}
# Create the ts object 

# Plot the result

```

`@solution`
```{r}
# Create the ts object 
close <- ts(close_monthly, start = c(2007, 1), frequency = 12)
# Plot the result
plot(close)
```

`@sct`
```{r}
ex() %>% check_function("ts") %>% {
  check_arg(., "data") %>% check_equal()
  check_arg(., "start") %>% check_equal()
  check_arg(., "frequency") %>% check_equal()
}
ex() %>% check_function("plot") %>% check_arg("x") %>% check_equal()
success_msg("Great!")
```

---

## Subsetting an Object of class ts

```yaml
type: NormalExercise
key: 4f95d4c225
xp: 100
```

To subset a time series according to time it is most convenient to work with `window()`. 

For monthly data you can use this function as follows: 
- To extract a time period with a specific start and end date use
`window(TimeSeries, start = c(year, month), end = c(year, month) )`
- To get a subset from the start of the series until a specific end date use 
`window(TimeSeries, end = c(year, month) )`
- To get a subset from a specific start date until the end of the series use
`window(TimeSeries, start = c(year, month) )`

`@instructions`
- Split the series of monthly closing prices (`close`) in two parts; one from January 2007 until December 2012 and one with the remaining data. 
- Assign the first part to the variable `close1` and the second part to the variable `close2`.
- Plot the two series next to each other.

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
DAX <- getSymbols("^GDAXI",auto.assign = FALSE)
DAX_monthly <- to.monthly(DAX)
close_monthly <- DAX_monthly$DAX.Close
close <- ts(close_monthly, start = c(2007, 1), frequency = 12)
```

`@sample_code`
```{r}
# Split the series in two parts


# Plot the 2 time series
par(mfrow = c(1,2)) # this puts the follwing plots next to each other

```

`@solution`
```{r}
# Split the series in two parts
close1 <- window(close, start = c(2007, 1), end = c(2012, 12) )
close2 <- window(close, start = c(2013, 1))
# Plot the 2 time series
par(mfrow = c(1,2)) # this puts the follwing plots next to each other
plot(close1)
plot(close2)
```

`@sct`
```{r}
ex() %>% check_object("close1") %>% check_equal()
ex() %>% check_object("close2") %>% check_equal()
ex() %>% check_function("plot", index = 1) %>% check_arg("x") %>% check_equal()
ex() %>% check_function("plot", index = 2) %>% check_arg("x") %>% check_equal()
success_msg("Great!")
```

---

## The Forecast Package

```yaml
type: NormalExercise
key: 20f2562914
xp: 100
```

In this course we will use some of the functionality the `forecast` package provides. The functions of this package require data of class `ts`. 
We start with the `autoplot()` function. For a single time series it does not make a big difference whether we use `plot()` or `autoplot()`, however, `autoplot()` makes it easier if we want to add additional information to the plot as we will see later.

`@instructions`
- Load the `forecast` package
- Plot the closing prices using the `autoplot()` function

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
DAX <- getSymbols("^GDAXI",auto.assign = FALSE)
daxMonthly   <- to.monthly(DAX)
closeMonthly <- daxMonthly$DAX.Close
close <- ts(closeMonthly, start = c(2007, 1), frequency = 12)
```

`@sample_code`
```{r}
# Load the forecast package

# Plot the closing prices using autoplot()

```

`@solution`
```{r}
# Load the forecast package
library(forecast)
# Plot the closing prices using autoplot()
autoplot(close)
```

`@sct`
```{r}
ex() %>% check_function("library") %>% check_arg("package") %>% check_equal()
ex() %>% check_function("autoplot") %>% check_arg("object") %>% check_equal()
success_msg("Great!")
```

---

## Plotting Multiple Time Series in One Plot

```yaml
type: NormalExercise
key: de074c5e7a
xp: 100
```

To plot more than one time series the function `autolayer()` can be used. 
We still initialize the plot using `autoplot()` but then use `autolayer()` to add further information.

The syntax is as follows
```
autoplot(timeseries1) + autolayer(timeseries2) + autolayer(timeseries3) + ...
```

This will automatically produce a legend except for the first series in `autoplot()`. We can add 
this to the legend by specifying the function parameter `series` like this `autoplot(timeseries1, series = "timeseries1")`.

`@instructions`
- Inspect `DAX_monthly` again. Remember that it is of class `xts`. 
- Create the variables `high` and `low` as `ts` objects which contain the highest and lowest monthly prices.  
- Plot the series of the highest monthly prices together with the lowest monthly prices using `autoplot()` and `autolayer`.

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
DAX <- getSymbols("^GDAXI",auto.assign = FALSE)
DAX_monthly   <- to.monthly(DAX)
close_monthly <- DAX_monthly$DAX.Close
close <- ts(close_monthly, start = c(2007, 1), frequency = 12)
```

`@sample_code`
```{r}
# Create high and low


# Plot high and low together

```

`@solution`
```{r}
# Create high and low
high   <- ts(DAX_monthly$DAX.High, start = c(2007, 1), frequency = 12)
low    <- ts(DAX_monthly$DAX.Low, start = c(2007, 1), frequency = 12)
# Plot high and low together
autoplot(high, series = "high") + autolayer(low)
```

`@sct`
```{r}
ex() %>% check_object("high") %>% check_equal()
ex() %>% check_object("low") %>% check_equal()
ex() %>% check_function("autoplot") %>% check_arg("object") %>% check_equal()
ex() %>% check_function("autolayer") %>% check_arg("object") %>% check_equal()
success_msg("Great!")
```
