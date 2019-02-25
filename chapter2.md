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

Before we can start to analyse a time series we need to load the data into R. A nice and easy way to do that is provided 
by the `quantmod` package. The package contains the function `getSymbols()` which can be used to import data from Yahoo Finance (`src = yahoo`) 
and the Federal Reserve Bank of St. Louis (`src = FRED`). 

To import for example data from Yahoo Finance we need to find the symbol Yahoo uses for the DAX. You can find the symbol by using 
a search engine (search e.g. for Yahoo Finance DAX). Then we can pass this symbol as a string to `getSymbols(.,src = "yahoo", auto.assign = FALSE)` (replace the dot by the symbol) and assign the result to a variable using `<-`.

`@instructions`
- Verify that the Yahoo Finance symbol for the DAX is ^GDAXI. 
- Load the `quantmod` package.
- Import the DAX data by passing the symbol as a string to `getSymbols(.,auto.assign = FALSE, return.class = "ts")` and assign the result to the variable `DAX`.

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

The data from the previous exercise are still loaded. You can check this by using `ls()`. 
Follow the instructions to find out more about the data.

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
one column. If we want to work on only one time series we need to find the corresponding column.
Then can subset this column by either using a numerical index, the column name or the `$` notation (notice that this works exactly as with data frames).

`@instructions`
- Use the `colnames()` to find the name of the time series of daily closing prices.
- Use one of the subsetting methods to extract the closing prices and assign the result to a new variable called `close`. 
- Plot the single time series `close`.

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
close <- DAX[ ,"GDAXI.Close"]
# Plot the closing prices
plot(close)

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
For our porpuses it is enough to know, that `xts` can handle irregular time series, which means time series with non-constant time increments. This is useful for stock data since we do not have observations for night hours and for e.g. weekends and holidays.
However, the aim of this course is to work with regular time series. We can use the function `to.monthly()` to convert our series of daily data to monthly data. Since we get for each month of the year one observation, we can treat the newly generated series as a regular time series. 

`@instructions`
- Convert `DAX` to a monthly series and assign it to the variable `daxMonthly`.
- Create a variable `closeMonthly` with the monthly closing prices. 
- Plot `closeMonthly`.

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
daxMonthly <- to.monthly(DAX)
# extract closing prices
closeMonthly <- daxMonthly$DAX.Close
# plot 
plot(closeMonthly)
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
Some of the methods we are using in this course require time series of this class. We can convert an `xts` object to an object of class `ts`
by using the function `ts()`. Unfortunatly the time indices are not converted and we have to add them manually. For this it is enough to 
specify the start date and the so called frequency, which is the number of observation per "cycle", in our per year.  

`@instructions`
Convert `closeMonthly` to an object of class `ts` and assign it to the variable `close`. Make sure to specify `start` and `frequency` accordingly. 
Plot `close`.

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
DAX <- getSymbols("^GDAXI",auto.assign = FALSE)
daxMonthly <- to.monthly(DAX)
closeMonthly <- daxMonthly$DAX.Close
```

`@sample_code`
```{r}

```

`@solution`
```{r}
close <- ts(closeMonthly, start = c(2007, 1), frequency = 12)
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
- Plot the closing prices using the autoplot function

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
DAX <- getSymbols("^GDAXI",auto.assign = FALSE)
daxMonthly <- to.monthly(DAX)
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
ex() %>% check_function("library") %>% check_arg("x") %>% check_equal()
ex() %>% check_function("autoplot") %>% check_arg("x") %>% check_equal()
success_msg("Great!")
```
