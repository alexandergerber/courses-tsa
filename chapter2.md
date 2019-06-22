---
title: 'Trend and Seasonality'
description: ""
---

## Download Data with Seasonality

```yaml
type: NormalExercise
key: 11ee1fa50d
xp: 100
```

Many economical time series exhibit some form of deterministic trend and seasonality. Our aim is to either estimate and subtract or eliminate both components from the time series. If the remainder turns out to be stationary we can use e.g. ARMA models to learn more about the underlying short term dependencies.     

We start by downloading a time series with a strong seasonal pattern.

`@instructions`
- Load the packages `quantmod` and `forecast`. Those will be loaded for the rest of the course. 
- Download the time series of construction supplies (not seasonally adjusted) from `FRED` and assign it to the variable `con_supply`.
- Visualize the data using `autoplot()`.

`@hint`
The Symbol is `IPB54100N`.

`@pre_exercise_code`
```{r}

```

`@sample_code`
```{r}
# Load packages



# Load the construction supply data


# Plot the data

```

`@solution`
```{r}
# Load packages
library(quantmod)
library(forecast)

# Load the construction supply data
con_supply <- getSymbols("IPB54100N", src = "FRED", auto.assign = FALSE)

# Plot the data
autoplot(con_supply)
```

`@sct`
```{r}
ex() %>% check_library("quantmod")
ex() %>% check_library("forecast")
ex() %>% check_object("con_supply") %>% check_equal()
ex() %>% check_function("autoplot") %>% check_arg("object") %>% check_equal()
success_msg("Great!")
```

---

## The Additive Decomposition Model

```yaml
type: NormalExercise
key: 7c986018c3
xp: 100
```

The most basic model dealing with a trend and a seasonal pattern is the additive decomposition model 

$$Y _t = m _t + s_t + X _t$$

where 

- $m _t$ is the trend component,
- $s _t$ is the seasonal component,
- $X _t$ is the random component.

We start by assuming that the seasonal pattern is simply repeating without any change, i.e. $s _t = s _{t+d}$, where $d$ is the length of one cycle.

Furthermore, we assume a linear time trend. 

In practice it is unlikely to find a time series for which these assumptions hold. However, for some parts of a time series 
this might be true.

`@instructions`
- Produce a **monthly** time series of class `ts` from `con_supply` Assign the result to the variable `con_supply_ts`. Make sure the time indices are correctly specified.  
- Subset the series so that it starts at January 2010 and ends in April 2019. Assign the result to the variable `con_supply2010`.
- Plot `con_supply2010` to see whether the above stated assumptions approximately hold (use `autoplot()` for this).

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


# Subset the series from Jan. 2010 until the end


# Plot con_supply2010

```

`@solution`
```{r}
# Produce monthly time series of class ts
con_supply_ts <- ts(con_supply, start = c(1947, 1), frequency = 12)

# Subset the series from Jan. 2010 until the end
con_supply2010 <- window(con_supply_ts, start = c(2010, 1), end = c(2019,4))

# Plot con_supply2010
autoplot(con_supply2010)

```

`@sct`
```{r}
ex() %>% check_function("ts") %>% {
  check_arg(., "data") %>% check_equal()
  check_arg(., "start") %>% check_equal()
  check_arg(., "frequency") %>% check_equal()
}
ex() %>% check_object("con_supply_ts") %>% check_equal()
ex() %>% check_object("con_supply2010") %>% check_equal()
ex() %>% check_function("autoplot") %>% check_arg("object") %>% check_equal()
success_msg("Great!")

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

OLS can be used to estimate the model coefficents. Note that $u _t$ contains the seasonal and the stochastic component.  The trend estimate is
$$\widehat{m} _t = \widehat{\beta _0} + \widehat{\beta _1} t.$$

In R the above model can be estimated by `tslm(ts ~ trend)`. 
The function `tslm()` extends the standard `lm()` function by providing additional functionality for time series data. 

A good way to check if everything worked is to plot the fitted values of the model together with the original series. 
The series of fitted values can be extracted by `fitted(model)`. 
With `residuals(model)` you can also access the residuals which will be needed later on.

`@instructions`
- Fit a linear trend model to `con_supply2010`and save it as `trend_model`.
- Plot the original series together with the estimated linear trend (use `autoplot()` and `autolayer()` for this).

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
download.file("https://assets.datacamp.com/production/repositories/4944/datasets/6ad23686e37c771ff807b4d6060bb0ec9a2f543e/con_supply.rda", destfile = "con_supply.rda")
load("con_supply.rda")
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
trend_model <- tslm(con_supply2010 ~ trend)

# Plot the series together with the estimated trend 
autoplot(con_supply2010, series = "Construction Supplies") + autolayer(fitted(trend_model), series = "Trend Estimate")
```

`@sct`
```{r}
ex() %>% check_object("trend_model") %>% check_equal()

ex() %>% check_or(
   check_function(.,"autoplot") %>% check_arg("object") %>% check_equal(),
   override_solution(.,'trend_model <- tslm(con_supply2010 ~ trend); autoplot(fitted(trend_model)) + autolayer(con_supply2010)') %>% 
   check_function("autoplot") %>% check_arg("object") %>% check_equal()
)

ex() %>% check_or(
   check_function(.,"autolayer") %>% check_arg("object") %>% check_equal(),
   override_solution(.,'trend_model <- tslm(con_supply2010 ~ trend); autoplot(fitted(trend_model)) + autolayer(con_supply2010)') %>% 
   check_function("autolayer") %>% check_arg("object") %>% check_equal()
)
success_msg("Great!")
```

---

## Moving Average Trend

```yaml
type: NormalExercise
key: 142f2066a9
xp: 100
```

The assumption of a linear trend is often not realistic. To account for changes in the trend behavior of a time series the trend can be estimated by an accordingly specified moving average filter. 

For a cycle with odd length (e.g. weekly data) we can use the filter
$$\widehat{m}_ t = \frac{1}{2q +1} \sum _{j = -q}^q y _{t-j}, \qquad t = q + 1, \ldots, T-q,$$
where we choose $q$ such that $d = 2q + 1$. 

In case of an even cycle length one should use 
$$\widehat{m}_ t = (0.5 y _{t-q} + y _{t-q+1} + \ldots +  y _{t+q-1} +  0.5 y _{t+q}),$$
where $d = 2q$.

If the `frequency` attribute 
of the time series is set correctly the function `decompose(y)` automatically decides which of the 2 versions should be used.
The trend estimate can be extracted by `decompose(y)$trend`.

`@instructions`
- Estimate the trend of `con_supply2010` using a moving average filter and assign the result to `trend_ma`.
- Produce a plot with the original data and the moving average trend (use `autoplot()` and `autolayer()` for this).

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
download.file("https://assets.datacamp.com/production/repositories/4944/datasets/6ad23686e37c771ff807b4d6060bb0ec9a2f543e/con_supply.rda", destfile = "con_supply.rda")
load("con_supply.rda")
con_supply_ts <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1))
```

`@sample_code`
```{r}
# Use decompose() to estimate the trend via an moving average filter


# Plot the series together with the estimated trend 

```

`@solution`
```{r}
# Use decompose() to estimate the trend with a moving average filter
trend_ma <- decompose(con_supply2010)$trend

# Plot the series together with the estimated trend 
autoplot(con_supply2010, series = "Construction Supplies") + autolayer(trend_ma, series = "Trend Estimate")
```

`@sct`
```{r}
ex() %>% check_object("trend_ma") %>% check_equal()
ex() %>% check_function("autoplot") %>% check_arg("object") %>% check_equal()
ex() %>% check_function("autolayer") %>% check_arg("object") %>% check_equal()
success_msg("Great! Can you guess why there are 12 rows missing?")
```

---

## Trend Elimination Using Differences

```yaml
type: NormalExercise
key: e9d18ca8ce
xp: 100
```

In contrast to the two methods mentioned before we will now eliminate the trend instead of estimating it. 
This can be done by computing the first differences $\Delta y _t = y _t - y _{t-1}$. If the data contains a linear time trend then the 
trend in the transformed series  $\Delta y _t$ is eliminated.  

In R we can use the function `diff(y, lag = 1)` to compute $\Delta y _t$.

`@instructions`
- Compute first differences to remove the time trend of `con_supply2010`. Assign the result to `x_diff`.
- Plot the differenced series (use `autoplot()` for this).

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
download.file("https://assets.datacamp.com/production/repositories/4944/datasets/6ad23686e37c771ff807b4d6060bb0ec9a2f543e/con_supply.rda", destfile = "con_supply.rda")
load("con_supply.rda")
con_supply_ts <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1))
```

`@sample_code`
```{r}
# Compute first differences


# Plot the differenced series


```

`@solution`
```{r}
# Compute first differences 
x_diff <- diff(con_supply2010, lag = 1)

# Plot the differenced series
autoplot(x_diff)

```

`@sct`
```{r}
ex() %>% check_object("x_diff") %>% check_equal()
ex() %>% check_function("autoplot") %>% check_arg("object") %>% check_equal()
success_msg("Great! The differenced series contains no trend anymore.")
```

---

## Detect Saisonality

```yaml
type: NormalExercise
key: 2113d183ec
xp: 100
```

We now know some methods to deal with a time trend. If we visualize the series after the trend is subtracted or eliminated we are left with an estimate of the seasonal and the random component.  

In our case it is easy to see the strong seasonal pattern. Sometimes this might not be the case. 
If it is less obvious the autocorrelation function (ACF) can help to detect it. Use the function `ggAcf()` from the `forecast` package 
to compute and plot the ACF.

`@instructions`
- Remove the trend component by subtracting the trend estimated by the linear trend model (`trend_model`) from the original series and save the result as `con_supply2010_detrended`. 
- Plot the detrended series. Use `autoplot()` for this. 
- Plot the autocorrelation function of the detrended series. Do you see what is special about it?

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
download.file("https://assets.datacamp.com/production/repositories/4944/datasets/6ad23686e37c771ff807b4d6060bb0ec9a2f543e/con_supply.rda", destfile = "con_supply.rda")
load("con_supply.rda")
con_supply_ts <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1))
trend_model <- tslm(con_supply2010 ~ trend)
```

`@sample_code`
```{r}
# Remove the trend


# Plot the detrended series


# Plot the ACF

```

`@solution`
```{r}
# Remove the trend
con_supply2010_detrended <- con_supply2010 - fitted(trend_model)

# Plot the detrended series
autoplot(con_supply2010_detrended)

# Plot the ACF
ggAcf(con_supply2010_detrended)
```

`@sct`
```{r}
ex() %>% check_object("con_supply2010_detrended") %>% check_equal()
ex() %>% check_function("autoplot") %>% check_arg("object") %>% check_equal()
ex() %>% check_function("ggAcf") %>% check_arg("x") %>% check_equal()
success_msg("Great!")
```

---

## Linear Model for Trend and Seasonality

```yaml
type: NormalExercise
key: 31eb5aabf6
xp: 100
```

To deal with the seasonality we can extend the methods used for estimating/eliminating the time trend. 

If we assume a linear time trend and a constant seasonal pattern we can use `tslm(y ~ trend + season)` 
to estimate $m _t$ and $s _t$ . This will run an OLS regression where, besides the regressor for the trend, dummy variables for each but one month are automatically included.

`@instructions`
- Estimate trend and seasonality of `con_supply2010` using a linear model and assign it to `seasonal_model`.
- Plot the original series together with the fitted values of this model (use `autoplot()` and `autolayer()` for this). 
- Save the estimated random component as `con_supply2010_random1`.
- Plot `con_supply2010_random1` (use `autoplot()` for this).

`@hint`
- The residuals of a model can be extracted by `residuals(model)`.

`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
download.file("https://assets.datacamp.com/production/repositories/4944/datasets/6ad23686e37c771ff807b4d6060bb0ec9a2f543e/con_supply.rda", destfile = "con_supply.rda")
load("con_supply.rda")
con_supply_ts  <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1))


```

`@sample_code`
```{r}
# Estimate a model with trend and seasonality and assing it to `seasonal_model`


# Plot the series together with the fitted values


# Save the estimated random component


# Plot the estimated random component


```

`@solution`
```{r}
# Estimate a model with trend and seasonality
seasonal_model <- tslm(con_supply2010 ~ trend + season)

# Plot the series together with the fitted values
autoplot(con_supply2010) + autolayer(fitted(seasonal_model))

# Save the estimated random component
con_supply2010_random1 <- residuals(seasonal_model)

# Plot the estimated random component
autoplot(con_supply2010_random1)

```

`@sct`
```{r}
ex() %>% check_object("seasonal_model") %>% check_equal()
ex() %>% check_function("autoplot", index = 1) %>% check_arg("object") %>% check_equal()
ex() %>% check_function("autolayer") %>% check_arg("object") %>% check_equal()
ex() %>% check_object("con_supply2010_random1") %>% check_equal()
ex() %>% check_function("autoplot", index = 2) %>% check_arg("object") %>% check_equal()
success_msg("Great!")
```

---

## Estimate Trend and Seasonality Using Decompose

```yaml
type: NormalExercise
key: 1862a60e6f
xp: 100
```

The function `decompose()` estimates the seasonal component by computing simple averages. This approach is numerically identical to the dummy variables approach from the last exercise. If the result of `decompose()` is passed to `autoplot()` a neat plot of the seasonal decomposition is produced.

`@instructions`
- Use `decompose()` to estimate the trend and seasonality of `con_supply2010`. Assign the result to `con_supply2010_decomp`.
- Plot the seasonal decomposition.
- Extract the random component from `con_supply2010_decomp` and save it as `con_supply2010_random2`.
- Plot `con_supply2010_random2` (use `autoplot()` for this as well).

`@hint`
- The random component can be extracted by `decompose(series)$random`.

`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
download.file("https://assets.datacamp.com/production/repositories/4944/datasets/6ad23686e37c771ff807b4d6060bb0ec9a2f543e/con_supply.rda", destfile = "con_supply.rda")
load("con_supply.rda")
con_supply_ts <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1))
```

`@sample_code`
```{r}
# Estimate trend and seasonality using decompose


# Plot the seasonal decomposition


# Extract the random component


# Plot the random component


```

`@solution`
```{r}
# Estimate trend and seasonality using decompose
con_supply2010_decomp <- decompose(con_supply2010)

# Plot the seasonal decomposition
autoplot(con_supply2010_decomp)

# Extract the random component
con_supply2010_random2 <- con_supply2010_decomp$random

# Plot the random component
autoplot(con_supply2010_random2)

```

`@sct`
```{r}
ex() %>% check_object("con_supply2010_decomp") %>% check_equal()
ex() %>% check_function("autoplot", index = 1) %>% check_arg("object") %>% check_equal()
ex() %>% check_object("con_supply2010_random2") %>% check_equal()
ex() %>% check_function("autoplot", index = 2) %>% check_arg("object") %>% check_equal()
success_msg("Great!")
```

---

## Eliminate Trend and Seasonality by Differencing

```yaml
type: NormalExercise
key: 281b640848
xp: 100
```

We can also eliminate a constant seasonal pattern by using seasonal differences. For this we compute differences between  $y _t$ and  $y _{t-d}$ where $d$ is the length of one cycle
$$\Delta_d y _t = y _t - y _{t-d}.$$
In R we can compute $\Delta_d y _t$ by `diff(y, lag = d)`.

If there is a trend and seasonality we can eliminate both by applying 
$$\Delta_d \Delta y _t.$$

`@instructions`
- Compute the first differences of `con_supply2010` to eliminate the trend and assign the result to `first_diff`. 
- Apply the operator for seasonal differences to the first differences to eliminate the seasonality and save the result as `con_supply2010_random3`.
- Plot `con_supply2010_random3` (use autoplot() for this).

`@hint`
- Remember that $d=12$ for the series at hand.

`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
download.file("https://assets.datacamp.com/production/repositories/4944/datasets/6ad23686e37c771ff807b4d6060bb0ec9a2f543e/con_supply.rda", destfile = "con_supply.rda")
load("con_supply.rda")
con_supply_ts <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1))
```

`@sample_code`
```{r}
# First differences 


# Seasonal differences


# Plot con_supply2010_random3


```

`@solution`
```{r}
# First differences 
first_diff <- diff(con_supply2010, lag = 1)

# Seasonal differences
con_supply2010_random3 <- diff(first_diff, lag = frequency(con_supply2010))

# Plot con_supply2010_random3
autoplot(con_supply2010_random3)

```

`@sct`
```{r}
ex() %>% check_object("first_diff") %>% check_equal()
ex() %>% check_object("con_supply2010_random3") %>% check_equal()
ex() %>% check_function("autoplot") %>% check_arg("object") %>% check_equal()
success_msg("Great!")
```
