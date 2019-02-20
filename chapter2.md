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
a search engine (search e.g. for Yahoo Finance DAX).

`@instructions`
- Verify that the Yahoo Finance symbol for the DAX is ^GDAXI. 
- Load the `quantmod` package.
- Import the DAX data by passing the symbol as a string to `getSymbols(., return.class = "ts")` (meaning you should replace the dot by the symbol). 
- Check under which name the imported data was saved using `ls()`.

`@hint`
- If you want to pass something as a string you have to use ""

`@pre_exercise_code`
```{r}

```

`@sample_code`
```{r}
# Load the quantmod package

# Import the Data with getSymbols()

# Check out the name 

```

`@solution`
```{r}
# Load the quantmod package
library(quantmod)
# Import the Data
getSymbols("^GDAXI", return.class = "ts")
# Check out the name 
ls()
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

The imported data consist of more than just one time series. 
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
