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

Many economical time series exhibit some form of deterministic trend and seasonality. Our aim is to either estimate and substract or eliminate both components from the time series. If the remainder turns out to be stationary we can use e.g. ARMA models to learn more about the underlying short term dependencies.     

We start by downloading a time series with a strong seasonal pattern.

`@instructions`
- Load the `quantmod` and the `forecast` package.
- Download the time series of construction supplies (not seasonally adjusted) from `FRED` and assign it to the variable `con_supply`.
- Visualize the Data (if you want to use `autoplot()` make sure to load the required package).

`@hint`


`@pre_exercise_code`
```{r}

```

`@sample_code`
```{r}
# Load Packages


# Load construction supply data

# Plot the data

```

`@solution`
```{r}
# Load Packages
library(quantmod)
library(forecast)
# Load construction supply data
con_supply <- getSymbols("IPB54100N", src = "FRED", auto.assign = FALSE)
# Plot the data
autoplot(con_supply)
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

We start by assuming that the seasonal pattern is simply repeating without any change, i.e. $s _t = s _{t+d}$, where $d$ is the length of one cycle.

Furthermore we assume a linear time trend. 

In practice it is unlikely to find a time series for which these assumptions hold. However, for some parts of a time series 
this might be true.

`@instructions`
- Produce from `con_supply` a **monthly** time series of class `ts` beginning at January 2010 until the end of the downloaded series. Assign the result to the variable `con_supply2010`.
- Plot `con_supply2010` to see whether the above stated assumptions approximately hold.

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
con_supply <- getSymbols("IPB54100N", src = "FRED", auto.assign = FALSE)
```

`@sample_code`
```{r}
# Produce monthly time series of class ts

# Plot con_supply2010
```

`@solution`
```{r}
# Produce monthly time series of class ts
con_supply_ts <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1))
# Plot con_supply2010
autoplot(con_supply2010)

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

We can use OLS to estimate the coefficents. Note that $u _t$ contains the seasonal and the stochastic component.  As trend estimate we get
$\hat{m} _t = \hat{\beta _0} + \hat{\beta _1} t$.

In R the above model can be estimated by `tslm(ts ~ trend)`. A good way to check if everything worked is to plot the fitted values of the model together with the original series. 
This can be done by
```
autoplot(ts) + autolayer(fitted(model))
```

`@instructions`
- Fit a linear trend model to `con_supply2010`and save it as `trend_lm`.
- Plot the original series together with the estimated linear trend.

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
con_supply <- getSymbols("IPB54100N", src = "FRED", auto.assign = FALSE)
con_supply_ts <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1))
```

`@sample_code`
```{r}
# Fit the trend model 

# Plot the series together with the estimated trend 

```

`@solution`
```{r}
# Fit the trend model 
trend_lm <- tslm(con_supply2010 ~ trend)
# Plot the series together with the estimated trend 
autoplot(con_supply2010) + autolayer(fitted(trend_lm))
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

For a cycle with odd length (e.g. weekly data) we can use the filter
$$\hat{m}_ t = \frac{1}{2q +1} \sum _{j = -q}^q y _{t-j}, \qquad t = q + 1, \ldots, T-q$$
where we choose $q$ such that $d = 2q$. 

In case of an even cycle length one should use 
$$\hat{m}_ t = (0.5 y _{t-q} + y _{t-q+1} + \ldots +  y _{t+q-1} +  0.5 y _{t+q})$$
where $d = 2q +1$.

If the `frequency` attribute 
of the time series is set correctly the function `decompose(y)` automatically decides which of the 2 version should be used.
The trend estimate can be extracted by `decompose(y)$trend`.

`@instructions`
- Estimate the trend using a moving average filter and assign the result to `trend_ma`.
- Produce a plot with the original data and the moving average trend.

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
con_supply <- getSymbols("IPB54100N", src = "FRED", auto.assign = FALSE)
con_supply_ts <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1))
```

`@sample_code`
```{r}
# Use Decompose to estimate the trend via an moving average filter

# Plot the series together with the estimated trend 

```

`@solution`
```{r}
# Use Decompose to estimate the trend via an moving average filter
trend_ma <- decompose(con_supply2010)$trend
# Plot the series together with the estimated trend 
autoplot(con_supply2010) + autolayer(trend_ma)
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

In R we can use the function `diff(y, lag = 1)` to compute $\Delta y _t$.

`@instructions`
Use first differences to remove the time trend. Assign the result to `x_diff`.

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

We now know some methods to deal with a time trend. If we visualise the series after the trend is substracted or eliminated we are left with an estimate of the seasonal and the random component.  

In our case it is easy to see the strong seasonal pattern. Sometimes this might not be the case. 
If it is less obvious the autocorrelation function (ACF) can help to detect it. Use the function `ggAcf()` from the `forecast` package 
to compute and plot the ACF.

`@instructions`
- Remove the trend component by substracting the trend estimate `trend_lm` from the original series and save the result as `con_supply2010_detrended`. 
- Plot the detrended series. 
- Plot the autocorrelation function of the detrended series. Do you see what is special about ist?

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

To deal with the seasonality we can simply adjust the methods used for estimating/eliminating the time trend. 

If we assume a linear time trend and a constant seasonal pattern we can use `tslm(y ~ trend + season)` 
to estimate $m _t$ and $s _t$ . This will run an OLS regression where for each but one month a dummy variable is automatically introduced.

`@instructions`
- Estimate trend and seasonality using a linear model.
- Plot the original series together with the fitted values of this model. 
- Save an estimate of the random component as `con_supply2010_random1`.

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

## Estimate Trend and Seasonality using Decompose

```yaml
type: NormalExercise
key: 1862a60e6f
xp: 100
```

The function `decompose()` estimates the seasonal component as simple averages. This approach is numerical identical to the dummy variables approach from the last exercise. If the result of `decompose()` is past to `autoplot()` a nice plot of the seasonal decomposition is produced.

`@instructions`
Use `decompose()` to estimate trend and seasonality. Assign the result `con_supply2010_decomp`.
Plot the seasonal decomposition. 
Extract the random component from `con_supply2010_decomp` an save it as `con_supply2010_random2´.

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

## Remove Trend by Differencing

```yaml
type: NormalExercise
key: 281b640848
xp: 100
```

We can also eliminate a constant seasonal pattern

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
