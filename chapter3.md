---
title: 'Trend and Seasonality'
description: ""
---

## Download data with seasonality

```yaml
type: NormalExercise
key: 11ee1fa50d
xp: 100
```

Many economical time series exhibit some form of deterministic trend and seasonality. Our aim is to either estimate and substract or eliminate both components from the time series. If the remainder turns out to be stationary we can use time series models such as ARMA models to learn more about the underlying short term dependencies.     

We start by downloading a time series with a strong seasonal pattern.

`@instructions`
- load the `quantmod` package
- download the time series of construction supplies (not seasonally adjusted) from `FRED` and assign it to the variable `con_supply`.
- visualize the Data (if you want to use `autoplot()` make sure to load the required package)

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

## The additive decomposition model

```yaml
type: NormalExercise
key: 7c986018c3
xp: 100
```

The most basic model dealing with a trend and a seasonal pattern is the additive decomposition model 

$$Y _t = m _t + s_t + X _t$$

where 

- $m _t$ is the trend component
- $s _t$ is the seasonal component
- $X _t$ is the random component

We start by assuming that the seasonal pattern is simply repeating every year without any change, i.e. $s _t = s _{t+d}$, where $d$ is the length of one cycle.

Furthermore we assume a linear time trend. 

In practice it is unlikely to find a time series for which these assumptions hold. However, for some parts of a time series 
this might be true.

`@instructions`
- Produce from `con_supply` a **monthly** time series of class `ts` beginning at January 2010 until the end of the downloaded series. Assign the result to the variable `con_supply2010`.
- Plot `con_supply2010` to make sure if the above stated assumptions approximately hold.

`@hint`


`@pre_exercise_code`
```{r}

```

`@sample_code`
```{r}
# Load the quantmod package

# Import the Data

# Produce monthly time series of class ts

# Inspect the downloaded data

```

`@solution`
```{r}
# Load the quantmod package
library(quantmod)
library(forecast)
# Import the Data
con_supply_xts <- getSymbols("IPB54100N", src = "FRED", auto.assign = FALSE)
# Produce monthly time series of class ts
con_supply <- ts(con_supply_xts, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply, start = c(2012, 1))
#
autoplot(con_supply2010)
# Inspect the downloaded data
seasonal_model <- tslm(con_supply2010 ~ trend + season)

```

`@sct`
```{r}

```

---

## Linear Trend Model

```yaml
type: NormalExercise
key: a50a1017d3
xp: 100
```

The first first step is to deal with the trend component $m _t$. 
Since we assume a linear trend we can estimate the trend using the model 

$$y _t = \beta _0 + \beta_1 t + u _t.$$

We can use OLS to estimate the coefficents. Note that $u _t$ contains the seasonal and the stochastic component.  

In R the above model can be estimated by `tslm(ts ~ trend)`.
A good way to check if everything worked is to plot the fitted values of the model together with the original. 
This can be done by
```
autoplot(ts) + autolayer(fitted(model))
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

---

## Moving Average Trend

```yaml
type: NormalExercise
key: 142f2066a9
xp: 100
```

The assumption of an linear trend is often unrealistic. To account for changes in the trend behaviour of a time series the trend can be estimated by an accordingly specified moving average filter. 

For a cycle with and odd length such as weekly data we can use the filter
$$\hat{m}_ t = \frac{1}{2q +1} \sum _{j = -q}^q y _{t-j}, \qquad t = q + 1, \ldots, T-q$$
where we choose $q$ such that $d = 2q$. 

In case of an even cycle length one should use 
$$\hat{m}_ t = (0.5 y _{t-q} + y _{t-q+1} + \ldots +  y _{t+q-1} +  y _{t+q})$$
where $d = 2q +1$.

If the `frequency` attribute 
of the time series is set correctly the function `decompose(y)` automatically decides which of the 2 version should be used.
The trend estimate can be extracted using  `decompose(y)["trend"]`.

`@instructions`
- Estimate the trend using a moving average filter and assign the result to `trend_ma`
- Produce a plot with the original data and the moving average trend.

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

## Trend Elimination Using Differences

```yaml
type: NormalExercise
key: e9d18ca8ce
xp: 100
```

In contrast to the two methods before we now will eliminate the trend instead of estimating it. 
This can be done by computing the first differences $\Delta y _t = y _t - y _{t-1}$. If the data contains a linear time trend then the 
trend will be removed in the transformed series  $\Delta y _t$.  

In R we can use the function `diff(y, lag = 1)` to get $\Delta y _t$.

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

---

## Detect Saisonality

```yaml
type: NormalExercise
key: 2113d183ec
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

---

## Linear Model for trend and seasonality

```yaml
type: NormalExercise
key: 31eb5aabf6
xp: 100
```

If we assume a linear time trend and a constant seasonal pattern we can use `tslm(y ~ trend + season)` 
to estimate $m _t$ and $s _t$ . This will run an OLS regression where for each but one month a dummy variable was automatically introduced.

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

---

## Plot fitted model

```yaml
type: NormalExercise
key: bf4d7783d0
xp: 100
```

In order to check whether our model was suitable to capture  trend and seasonal component a first step is to plot the fitted values together with the original series.

`@instructions`
- Use `autoplot()` to plot `con_supply2010` and add the fitted values of `seasonal_model`

`@hint`


`@pre_exercise_code`
```{r}
# Load the quantmod package
library(quantmod)
library(forecast)
# Import the Data
con_supply_xts <- getSymbols("IPB54100N", src = "FRED", auto.assign = FALSE)
# Produce monthly time series of class ts
con_supply <- ts(con_supply_xts, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply, start = c(2012, 1))
# Inspect the downloaded data
seasonal_model <- tslm(con_supply2010 ~ trend + season)
plot(con_supply2010)
```

`@sample_code`
```{r}

```

`@solution`
```{r}
autoplot(con_supply2010) + autolayer(fitted(seasonal_model))
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
