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
- Import the DAX data by passing the symbol as a string to `getSymbols()`.
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
getSymbols("^GDAXI")
# Check out the name 
ls()
```

`@sct`
```{r}
ex() %>% check_library("quantmod")
success_msg("Great!")
```

---

## Insert exercise title here

```yaml
type: TabExercise
key: d11102fe29
xp: 100
```

Before we can start to analyse a time series we need to load the desired data into R. A nice and easy way to do that is provided 
by the `quantmod` package. The package contains the function `getSymbols()` which can be used to import data from various sources such 
as Yahoo and Google Finance and the FRED (Federal Reserve Bank of St. Louis). 

To import for example DAX data from Yahoo Finance we need to find the symbol Yahoo uses for the DAX. You can find the symbol by using 
a search engine (search e.g. for Yahoo Finance DAX).

`@pre_exercise_code`
```{r}

```

***

```yaml
type: NormalExercise
key: c996807822
xp: 50
```

`@instructions`
- Verify that the Yahoo Finance symbol for the DAX is ^GDAXI. 
- Load the `quantmod` package.
- Import the DAX data by passing the symbol as a string to `getSymbols()`.
- Check under which name the imported data was saved using `ls()`.

`@hint`


`@sample_code`
```{r}
# Load the quantmod package

# Import the Data

# Check out the name 

```

`@solution`
```{r}
# Load the quantmod package
library(quantmod)
# Import the Data
getSymbols("^GDAXI")
# Check out the name 
ls()
```

`@sct`
```{r}
ex() %>% check_library("quantmod")
success_msg("Great!")
```

***

```yaml
type: NormalExercise
key: 1a483598e4
xp: 50
```

`@instructions`
- Use the plot function to visualize the data

`@hint`


`@sample_code`
```{r}
# Plot the data
```

`@solution`
```{r}
# Plot the data
plot(1:10)
```

`@sct`
```{r}
ex() %>% check_function("plot") %>% check_arg("x") %>% check_equal()
success_msg("Great!")
```
