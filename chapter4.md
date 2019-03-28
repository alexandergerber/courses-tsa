---
title: 'ARMA Models'
description: ""
---

## Fitting an ARMA model

```yaml
type: NormalExercise
key: 0fd6ee0f58
xp: 100
```

After we removed trend and seasonality the time series of construction supplies appears to be stationary.
Hence, we can start to use ARMA models to analyse the short term dependencies. For this we will continue to work with 
the residuals of the linear trend + seasonality model `con_supply2010_random1`.  

The function `arima()` is the standard tool to fit ARMA(p,q) Models in R. 
The syntax is as follows

```
arima(time_series, order = c(p,0,q))
```

`@instructions`
- Fit an ARMA(1,1) model to `con_supply2010_random1`and save the fitted model as `arma11`
- Extract the fitted values of the fitted model using the function `fitted()` 
- Extract the residuals of the fitted using the function `residuals()`

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
con_supply <- getSymbols("IPB54100N", src = "FRED", auto.assign = FALSE)
con_supply_ts  <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1))
seasonal_model <- tslm(con_supply2010 ~ trend + season)
con_supply2010_random1 <- residuals(seasonal_model)

```

`@sample_code`
```{r}

```

`@solution`
```{r}

```

`@sct`
```{r}
success_msg("Great!")
```

---

## Simulate an ARMA Time Series

```yaml
type: NormalExercise
key: 8fec5a03b9
xp: 100
```

Before we continue to work on the construction supply data we will use R to learn more about the properties of ARMA time series. 
This will help us to decide which ARMA model we should use (i.e how to choose $p$ and $q$). 

We start by simulating data from an ARMA process. R provides the function `arima.sim(model, n)` which can be used to do that.
The parameter `model` expects a list of the form 
``
list(ar = c(phi1, phi2, ...), ma = c(theta1, theta2, ...))
``
and `n` is the length of the time series that will be generated. The function returns an object of class `ts`.

Example: 
If we want to simulate $1000$ observations from the ARMA(1,2) process
$$y _t = 0.4 y _{t-1} +  \epsilon _t + 0.6\epsilon _{t-1} + 0.3\epsilon _{t-2} $$
we can do this with
```
arima.sim(model = list(ar =c(0.6), ma = c(0.6, 0.3)), n = 1000)
```

`@instructions`
- Simulate data from the process 
$$y _t = 0.9 y _{t-1} +  \epsilon _t,\quad t = 1,...,1000$$ and save the result as `ar1` 
- Simulate data from the process 
 $$y _t = 0.5 y _{t-1} + 0.3 y _{t-2} +  \epsilon _t,\quad t = 1,...,1000$$ and save the result as `ar2`
- Simulate data from the process 
$$y _t = \epsilon _t + 0.6\epsilon _{t-1} , \quad t = 1,...,1000$$ and save the result as `ma1` 
- Simulate data from the process 
 $$y _t = \epsilon _t + 0.6\epsilon _{t-1} + 0.3\epsilon _{t-2}, \quad t = 1,...,1000$$ and save the result as `ma2`

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
success_msg("Great!")
```

---

## Plot the simulated ARMA data

```yaml
type: NormalExercise
key: 79e8ae9ec7
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
success_msg("Great!")
```

---

## Selecting the model order based on ACF and PACF

```yaml
type: NormalExercise
key: 5d66faf365
xp: 100
```

We now know how to fit a model but how do we choose $p$ and $q$?
Box and Jenkins proposed the following: 
- choose an AR(p) process if the PACF has a cutoff after the p-th lag and the ACF decays slowly to zero 
- choose a MA(q) process if the ACF has a cutoff after the q-th lag and the PACF decays slowly to zero
- if PACF and ACF decay slowly to zero a mixed model should be choosen

Since we are working with estimates of the ACF and PACF a cutoff means values which are close to zero (i.e. within the blue significance band).

`@instructions`
- Plot the ACF and the PACF of `con_supply2010_random1`
- Fit the model which is suggested by the approach of Box and Jenkins

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
con_supply <- getSymbols("IPB54100N", src = "FRED", auto.assign = FALSE)
con_supply_ts  <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1))
seasonal_model <- tslm(con_supply2010 ~ trend + season)
con_supply2010_random1 <- residuals(seasonal_model)
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

## Select Model Order Based on Information Criteria

```yaml
type: NormalExercise
key: 292300d10c
xp: 100
```

Another approach too choose the model order is using Information Criteria such as the Akaike Information Criterion (AIC).
The idea is to fit a variety of plausible candidate models and then choose the model which provides the "best" fit without overfitting the data.

If we base our decision on information criteria such as AIC we would choose among all candidate models the one with the lowest value for that criteria. 
The AIC can be computed by passing the fitted model to the function `AIC()`.

`@instructions`


`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
con_supply <- getSymbols("IPB54100N", src = "FRED", auto.assign = FALSE)
con_supply_ts  <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1))
seasonal_model <- tslm(con_supply2010 ~ trend + season)
con_supply2010_random1 <- residuals(seasonal_model)
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

## Model Diagnostics with ACF and PACF

```yaml
type: NormalExercise
key: e29c07d14d
xp: 100
```



`@instructions`


`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
con_supply <- getSymbols("IPB54100N", src = "FRED", auto.assign = FALSE)
con_supply_ts  <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1))
seasonal_model <- tslm(con_supply2010 ~ trend + season)
con_supply2010_random1 <- residuals(seasonal_model)
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

## Ljung-Box Test

```yaml
type: NormalExercise
key: 006d98c393
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

## Shortcut Forecast Package

```yaml
type: NormalExercise
key: a7a4e557dc
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
