---
title: 'Import and Visualize Time Series Data'
description: ""
---

## Importing Data

```yaml
type: NormalExercise
key: 7e8b138317
xp: 100
```

Before we can start to analyse a time series we have to load data into R. A nice and easy way to do that is provided 
by the `quantmod` package. The package contains the function `getSymbols()` which can be used to import data from Yahoo Finance (`src = yahoo`) 
and the Federal Reserve Bank of St. Louis (`src = FRED`). 

To import for example DAX data from Yahoo Finance we have to find the symbol Yahoo uses for the DAX. You can find the symbol by using 
a search engine (search e.g. for Yahoo Finance DAX). Then we can pass this symbol as a string to `getSymbols(.,src = "yahoo", auto.assign = FALSE)` (replace the dot by the symbol) and assign the result to a variable using `<-`.

`@instructions`
- Verify that the Yahoo Finance symbol for the DAX is `GDAXI`. 
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
# Import the Data
DAX <- getSymbols("^GDAXI", auto.assign = FALSE)
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

The data from the previous exercise are still loaded. You can check this by using `ls()` which will list all variables in the working environment. Follow the instructions to find out more about the data.

`@instructions`
- Use `head()` to get a numerical overview
- Use `class()` to find out which kind of object you are working with.

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
DAX <- getSymbols("^GDAXI", auto.assign = FALSE)
```

`@sample_code`
```{r}
# Print out the first rows  

# Find out the class of the downloaded object

```

`@solution`
```{r}
# Print out the first rows  
head(DAX)
# Find out the class of the downloaded object
class(DAX)
```

`@sct`
```{r}
ex() %>% check_function("plot") %>% check_arg("x") %>% check_equal()
ex() %>% check_function("class") %>% check_arg("x") %>% check_equal()
success_msg("Great!")
```

---

## Subset the downloaded data

```yaml
type: NormalExercise
key: 62f191b4b5
xp: 100
```

You found out that the dataset consists of more than one time series.  Each time series is contained in 
one column. We can extract one column by either using a numerical index, the column name or the `$` notation (notice that this works exactly as for data frames).

`@instructions`
- Use `colnames()` to find the name of the time series of daily closing prices.
- Use one of the subsetting methods to extract the closing prices and assign the result to a new variable called `close_daily`. 
- Plot the single time series `close_daily`.

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
colnames(DAX)
# Extract the closing prices 
close_daily <- DAX[ ,"GDAXI.Close"]
# Plot the closing prices
plot(close_daily)

```

`@sct`
```{r}
ex() %>% check_function("colnames") %>% check_arg("x") %>% check_equal()
ex() %>% check_object("close") %>% check_equal()
success_msg("Great!")
```

---

## Irregular vs. Regular Time Series

```yaml
type: NormalExercise
key: 335ac38015
xp: 100
```

We also learned that `DAX` is of class `xts`, which is an elaborate way of dealing with time series in R. 
For our purposes it is enough to know that `xts` can handle irregular time series, which means time series with non-constant time increments. This is useful for stock data since we do not have observations for night hours and for e.g. weekends and holidays.
However, the aim of this course is to work with regular time series. We can use the function `to.monthly()` to convert our series of daily data to monthly data. Since we get for each month of the year one observation, we can treat the newly generated series as a regular time series.

`@instructions`
- Convert `DAX` to a monthly series and assign it to the variable `DAX_monthly`.
- Create a variable `close_monthly` with the monthly closing prices. 
- Plot `close_monthly`.

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
DAX <- getSymbols("^GDAXI",auto.assign = FALSE)
```

`@sample_code`
```{r}

```

`@solution`
```{r}
# convert to monthly
DAX_monthly <- to.monthly(DAX)
# extract closing prices
close_monthly <- DAX_monthly$DAX.Close
# plot 
plot(close_monthly)
```

`@sct`
```{r}
ex() %>% check_function("to.monthly") %>% check_arg("x") %>% check_equal()
ex() %>% check_object("closeMonthly") %>% check_equal()
ex() %>% check_function("plot") %<% check_arg("x") %>% check_equal()
success_msg("Great!")
```

---

## Class ts

```yaml
type: NormalExercise
key: eb87762070
xp: 100
```

For the remainder of the course we will work with time series of class `ts`. This is a class for only regular time series. 
Most of the methods we are using in this course require time series in this format. We can convert an `xts` object to an object of class `ts`
by using the function `ts()`. Unfortunatly the time indices are not converted and we have to add them manually. For this it is enough to 
specify the start date and the so called frequency, which is the number of observation per "cycle", in our case per year. 

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
- Use e.g. `head(close_monthly)` to find the starting date.

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

Use this function as follows: 
- To extract a time period with a specific start and end date use
`window(TimeSeries, start = c(year, month), end = c(year, month) )`
- To get a subset from the start of the series until a specific end date use 
`window(TimeSeries end = c(year, month) )`
- To get a subset from a specific start date until the end of the series use
`window(TimeSeries start = c(year, month) )`

`@instructions`
Split the series of monthly closing prices (`close`) in two parts; 
one from January 2007 until December 2012 and one with the remaining data. 

Assign the first part to the variable `close1` and the second part to the variable `close2`.

Plot the two series next to each other.

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


# plot the 2 time series
par(mfrow = c(1,2)) # this puts the follwing plots next to each other

```

`@solution`
```{r}
# Split the series in two parts
close1 <- window(close, start = c(2007, 1), end = c(2012, 12) )
close2 <- window(close, start = c(2013, 1))
# plot the 2 time series
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

In this course we will use some of the functionality the `forecast` package provides. 
We start with a simple one, the `autoplot()` function. For a single time series it makes not a big difference whether we use `plot()` or `autoplot()`, however, `autoplot()` makes it easier if we want to add additional information to the plot as we will see later.

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

# plot the closing prices using autoplot()

```

`@solution`
```{r}
# Load the forecast package
library(forecast)
# plot the closing prices using autoplot()
autoplot(close)
```

`@sct`
```{r}
ex() %>% check_function("library") %>% check_arg("package") %>% check_equal()
ex() %>% check_function("autoplot") %>% check_arg("object") %>% check_equal()
success_msg("Great!")
```

---

## lm() for time series

```yaml
type: NormalExercise
key: df995c8906
xp: 100
```

One way to model e.g. a time trend in time series data is to use a linear model. The `forecast` package includes the function`tslm()` which makes it easy to do so. 
To fit a linear model of the form 

$$y _t = t + u _t$$

to the data we can use `tslm(y ~ trend)`. 
After the model is fitted, we can add the result to the plot using `autolayer()`. The syntax is as follows
```
autoplot(data) + autolayer(fitted(model))
```

`@instructions`
Fit a linear trend model to the closing data `close`.
Plot the data again using `autoplot()` but add this time the linear trend estimate.

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
DAX <- getSymbols("^GDAXI",auto.assign = FALSE)
daxMonthly <- to.monthly(DAX)
closeMonthly <- daxMonthly$DAX.Close
close <- ts(closeMonthly, start = c(2007, 1), frequency = 12)
```

`@sample_code`
```{r}

```

`@solution`
```{r}
# Fit the linear Model for the trend
trendModel<- tslm(close ~ trend)
# Plot the closing data together with the data fitted by the trend Model 
autoplot(close) + autolayer(fitted(trendModel))
```

`@sct`
```{r}
ex() %>% check_function("tslm") %>% check_arg("formula") %>% check_equal()
ex() %>% check_function("autoplot") %>% check_arg("object") %>% check_equal()
ex() %>% check_function("autolayer") %>% check_arg("object") %>% check_equal()
success_msg("Great!")
```
