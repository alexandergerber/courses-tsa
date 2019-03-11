---
title: 'Trend and Seasonality'
description: ""
---

## Download the data

```yaml
type: NormalExercise
key: 7c986018c3
xp: 100
```

Many economic time series exhibit a trend and a seasonal pattern. 
The most basic model dealing with both is the additive decomposition model 

$$Y _t = m _t + s_t + X _t$$

where 

- $m _t$ is the trend component
- $s _t$ is the seasonal component
- $X _t$ is the random component

`@instructions`
- load the `quantmod` package
- download the time series of construction supplies (not seasonally adjusted) from `FRED` and assign it to the variable `conSupply`.
- visualize the Data (if you want to use `autoplot()` make sure to load the required package)

`@hint`


`@pre_exercise_code`
```{r}

```

`@sample_code`
```{r}
# Load the quantmod package

# Import the Data

# Inspect the downloaded data
```

`@solution`
```{r}
# Load the quantmod package
library(quantmod)
# Import the Data
conSupply <- getSymbols("IPB54100N", src = "FRED", auto.assign = FALSE)
conSupply2010 <- window(conSupply, start = c(2012, 1))
# Inspect the downloaded data
plot(conSupply)
```

`@sct`
```{r}

```

---

## Insert exercise title here

```yaml
type: NormalExercise
key: 14a226810d
xp: 100
```

An easy case for the previous introduces seasonal model is one where the seasonal pattern does not change over time and the trend is a linear function of time, i.e
$$m _t = c + \beta \times t.$$ 

In practice it is unlikely to find a time series which has a constant seasonal pattern and a linear trend. However, for some parts 
this might be true. 

An easy way to fit this model is provided by `tslm()` using the formula `y ~ trend + season`. 
To model the seasonal component for each but one season a dummy variable is created and added to the linear regression model.

`@instructions`
- Produce from `conSupply` a **monthly** time series of class `ts` beginning at January 2010 until the end of the downloaded series. Assign the result to the variable `conSupply2010`. 
- Fit a model with linear trend and constant seasonal component to the data using `tslm()` and assign the result to the variable `seasonalModel`. 
- Inspect the result

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
conSupply <- getSymbols("IPB54100N", src = "FRED", auto.assign = FALSE)
```

`@sample_code`
```{r}

```

`@solution`
```{r}

```

`@sct`
```{r}

```

---

## Plot fitted model

```yaml
type: NormalExercise
key: bf4d7783d0
xp: 100
```

In order to check whether our model was suitable to capture the trend and seasonal component a first step is to plot the fitted values together with the original series.

`@instructions`
- Use `autoplot()` to plot `conSupply2010` and add the fitted values of `seasonalModel`

`@hint`


`@pre_exercise_code`
```{r}

```

`@sample_code`
```{r}

```

`@solution`
```{r}

```

`@sct`
```{r}

```

---

## Residuals

```yaml
type: NormalExercise
key: a3fdc4f2ad
xp: 100
```



`@instructions`


`@hint`


`@pre_exercise_code`
```{r}

```

`@sample_code`
```{r}

```

`@solution`
```{r}

```

`@sct`
```{r}

```
