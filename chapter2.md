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
close <- DAX[ ,"GDAXI.Close"]
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

---

## Insert exercise title here

```yaml
type: NormalExercise
key: 335ac38015
xp: 100
```

Recall the things you learned in order to import and inspect the Dow Jones data.

`@instructions`
- Import the Dow Jones Data from Yahoo Finance
- Assign the traded volume to a new variable called `vol`
- Visualize `vol` using `autoplot()`

`@hint`
Recall that you imported data from Yahoo using `getSymbols(.,src = "yahoo", auto.assign = FALSE, return.class = "ts")`

`@pre_exercise_code`
```{r}

```

`@sample_code`
```{r}

```

`@solution`
```{r}
DOW <- getSymbols("^DJI",auto.assign = FALSE, return.class = "ts")

colnames(DOW)
```

`@sct`
```{r}

```
