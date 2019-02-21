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

Before we can start to analyse a time series we need to load the desired data into R. A nice and easy way to do that is provided 
by the `quantmod` package. The package contains the function `getSymbols()` which can be used to import data from various sources such 
as Yahoo and Google Finance and the FRED (Federal Reserve Bank of St. Louis). 

To import for example DAX data from Yahoo Finance we need to find the symbol Yahoo uses for the DAX. You can find the symbol by using 
a search engine (search e.g. for Yahoo Finance DAX). Then we can pass this symbol as a string to `getSymbols(., auto.assign = FALSE, return.class = "ts")`
(meaning you should replace the dot by the symbol) and assign the result to a variable using `<-`. 

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
DAX <- getSymbols("^GDAXI", auto.assign = FALSE, return.class = "ts")
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
- Use `plot()` to get a graphical overview

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
getSymbols("^GDAXI", return.class = "ts")
```

`@sample_code`
```{r}
# Print out the first rows  

# Plot the time series

```

`@solution`
```{r}
# Print out the first rows  
head(GDAXI)
# Plot the time series
plot(GDAXI)
```

`@sct`
```{r}
ex() %>% check_function("plot") %>% check_arg("x") %>% check_equal()
ex() %>% check_function("head") %>% check_arg("x") %>% check_equal()
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
Then can subset this column by either using a numerical index or, if provided, the column name (notice that this works exactly as for matrices). 

`@instructions`
- Use the `colnames()` to find the name of the time series of daily closing prices.
- Use the name to extract the closing prices and assign the result to a new variable calles `close`. 
- Plot the single time series `close`.

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
DAX <- getSymbols("^GDAXI",auto.assign = FALSE, return.class = "ts")
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
ex() %>% check_function("close") %>% check_arg("x") %>% check_equal()
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
We start with a simple one, the `autoplot()` function. For a single time series it makes not a big difference whether we use `plot()` or `autoplot()`, however, 
`autoplot()` makes it easier if we want to add additional information to the plot as we will see later. 

`@instructions`
- Load the `forecast` package
- Plot the closing prices using the autoplot function

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
DAX <- getSymbols("^GDAXI",auto.assign = FALSE, return.class = "ts")
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
