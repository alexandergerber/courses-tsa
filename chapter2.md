---
title: 'Import and Visualize Time Series Data'
description: ""
---

## Import Data via `quantmod()`

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
- Verify that the Yahoo Finance symbol for the DAX is ^GDAXI
- Import the DAX data by passing the symbol as a string to `getSymbols()`
- Check under which name the imported data was saved using `ls()`

`@hint`
- If you want to pass something as a string you have to use ""

`@pre_exercise_code`
```{r}
library(quatmod)
# Import the Data


# Check out the name 

```

`@sample_code`
```{r}

```

`@solution`
```{r}
library(quatmod)
# Import the Data
getSymbols("^GDAXI")

# Check out the name 
ls()
```

`@sct`
```{r}
test_error()
success_msg("Great!")
```
