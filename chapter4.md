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

The function `arima()` is the standard tool to fit an ARMA(p,q) Models in R. 
The syntax is as follows

```
arima(time_series, order = c(p,0,q))
```

`@instructions`
- Fit an ARMA(1,1) model to `con_supply2010_random1`and save the fitted model as `arma11`
- Plot the original series together with the fitted values of the ARMA(1,1) model.

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

## Selecting the model order based on ACF and PACF

```yaml
type: NormalExercise
key: 5d66faf365
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

## Select Model Order Based on Information Criteria

```yaml
type: NormalExercise
key: 292300d10c
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
