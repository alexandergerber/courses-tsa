---
title: 'GARCH Models'
description: ""
---

## Downloading and Preparing Financial Data

```yaml
type: NormalExercise
key: 2ff3d10f68
xp: 100
```



`@instructions`


`@hint`


`@pre_exercise_code`
```{r}
library(forecast)
library(quantmod)
```

`@sample_code`
```{r}
# Load and plot the closing prices of Apple



# Compute and plot the log differences 



# Split the return series into a train and test series



```

`@solution`
```{r}
# Load and plot the closing prices of Apple
apple <- getSymbols("AAPL", auto.assign = FALSE)$AAPL.Close
autoplot(apple)

# Compute and plot the log differences 
log_returns <- diff(log(apple))
autoplot(log_returns)

# Split the return series into a train and test series
train <- ts(window(log_returns, start = "2015-01-01", end = "2018-12-31"))
test <- ts(window(log_returns, start = "2019-01-01"))

```

`@sct`
```{r}

```

---

## Dependence Structure within the Return Series

```yaml
type: NormalExercise
key: 28573c3590
xp: 100
```



`@instructions`


`@hint`


`@pre_exercise_code`
```{r}
library(forecast)
library(quantmod)
apple <- getSymbols('AAPL', auto.assign = F)$AAPL.Close
log_returns <- diff(log(apple))
train <- ts(window(log_returns, start = "2015-01-01", end = "2018-12-31"))
```

`@sample_code`
```{r}
# Estimate the ACF of the original return series


# Estimate the ACF of the squared return series


```

`@solution`
```{r}
# Estimate the ACF of the original return series
ggAcf(train)

# Estimate the ACF of the squared return series
ggAcf(train^2)

```

`@sct`
```{r}

```

---

## Fitting (G)ARCH Models

```yaml
type: NormalExercise
key: decd27689a
xp: 100
```



`@instructions`


`@hint`


`@pre_exercise_code`
```{r}
library(forecast)
library(quantmod)
library(fGarch)
apple <- getSymbols('AAPL', auto.assign = F)$AAPL.Close
log_returns <- diff(log(apple))
train <- ts(window(log_returns, start = "2015-01-01", end = "2018-12-31"))
```

`@sample_code`
```{r}

```

`@solution`
```{r}
# Fit an ARCH(4) to the return series
arch4 <- garchFit(~garch(4, 0), data = train, include.mean = FALSE, trace = FALSE)

# Fit a GARCH(1, 2) to the return series
garch12 <- garchFit(~garch(1, 2), data = train, include.mean = FALSE, trace = FALSE)

```

`@sct`
```{r}

```

---

## Model Selection

```yaml
type: NormalExercise
key: 71c752792e
xp: 100
```



`@instructions`


`@hint`


`@pre_exercise_code`
```{r}
library(forecast)
library(quantmod)
library(fGarch)
apple <- getSymbols('AAPL', auto.assign = F)$AAPL.Close
log_returns <- diff(log(apple))
train <- ts(window(log_returns, start = "2015-01-01", end = "2018-12-31"))
```

`@sample_code`
```{r}
# Expand a grid of possible model orders


# Create an empty list `garch_models` and vector `aic`



# Write a for() loop that iterates over the rows of your grid and estimates the models
for(___ in ___){
  garch_formula <- formula(paste("~garch(", ___, ",", ___, ")"))     
  
  
}

# Select the GARCH model with the lowest AIC


```

`@solution`
```{r}
# Expand a grid of possible model orders
grid <- expand.grid(p = 1:3, q = 1:3)

# Create an empty list `garch_models` and vector `aic`
garch_models <- list()
aic <- numeric()

# Write a for() loop that iterates over the rows of your grid and estimates the models
for(i in 1:nrow(grid)){
  garch_formula <- formula(paste("~garch(", grid[i, 1], ",", grid[i, 2] , ")"))     
  garch_models[[i]] <- garchFit(garch_formula, data = train, include.mean = F, trace = F)
  aic[i] <- garch_models[[i]]@fit$ics[1]
}

# Select the GARCH model with the lowest AIC
best_model <- garch_models[[which.min(aic)]]

```

`@sct`
```{r}

```

---

## Diagnostic Checking

```yaml
type: NormalExercise
key: cfd91968ef
xp: 100
```



`@instructions`


`@hint`


`@pre_exercise_code`
```{r}
library(forecast)
library(quantmod)
library(fGarch)
apple <- getSymbols('AAPL', auto.assign = F)$AAPL.Close
log_returns <- diff(log(apple))
train <- ts(window(log_returns, start = "2015-01-01", end = "2018-12-31"))
best_model <- garchFit(~garch(1, 1), data = train, include.mean = FALSE, trace = FALSE)
```

`@sample_code`
```{r}
# Compute and plot the standardized residuals



# Estimate the ACF of the standardized residuals 


# Estimate the ACF of the squared standardized residuals 


# Check the results of the Ljung-Box test


```

`@solution`
```{r}
# Compute and plot the standardized residuals
e <- train/best_model@sigma.t
autoplot(e)

# Estimate the ACF of the standardized residuals 
ggAcf(e)

# Estimate the ACF of the squared standardized residuals 
ggAcf(e^2)

# Check the results of the Ljung-Box test
summary(best_model)

```

`@sct`
```{r}

```

---

## Forecasting

```yaml
type: NormalExercise
key: bd56a9007f
xp: 100
```



`@instructions`


`@hint`


`@pre_exercise_code`
```{r}
library(forecast)
library(quantmod)
library(fGarch)
apple <- getSymbols('AAPL', auto.assign = F)$AAPL.Close
log_returns <- diff(log(apple))
train <- ts(window(log_returns, start = "2015-01-01", end = "2018-12-31"))
test <- ts(window(log_returns, start = "2019-01-01"))
```

`@sample_code`
```{r}
# Create an empty vector `forecast_sigma`


# Write a for() loop to statically forecast the sigmas within the rolling window scheme






```

`@solution`
```{r}
# Create an empty vector `forecast_sigma`
forecasts_sigma <- numeric()

# Write a for() loop to statically forecast the sigmas within the rolling window scheme
for(h in seq_along(test)){
  garch11 <- garchFit(formula = ~garch(1, 1), data = log_returns[(1 + h) : (length(train) + h - 1)], 
                          cond.dist = "norm", include.mean = FALSE, trace = FALSE)
  forecasts_sigma[h] <- predict(garch11, 1)$standardDeviation
}

```

`@sct`
```{r}

```

---

## Conditional Value at Risk

```yaml
type: NormalExercise
key: 49bd4f0b54
xp: 100
```



`@instructions`


`@hint`


`@pre_exercise_code`
```{r}
library(forecast)
library(quantmod)
library(fGarch)
apple <- getSymbols('AAPL', auto.assign = F)$AAPL.Close
log_returns <- diff(log(apple))
train <- ts(window(log_returns, start = "2015-01-01", end = "2018-12-31"))
test <- ts(window(log_returns, start = "2019-01-01"))
forecasts_sigma <- numeric()
for(h in seq_along(test)){
  garch11 <- garchFit(formula = ~garch(1, 1), data = log_returns[(1 + h) : (length(train) + h - 1)], 
                          cond.dist = "norm", include.mean = FALSE, trace = FALSE)
  forecasts_sigma[h] <- predict(garch11, 1)$standardDeviation
}
```

`@sample_code`
```{r}
# Forecast the CVaRs based on the forecasted sigmas


# Plot the test series together with the forecasted CVaRs


# Compute the share of actual returns being less than the forecasted CVaR


```

`@solution`
```{r}
# Forecast the CVaRs based on the forecasted sigmas
CVaR <- ts(forecasts_sigma * qnorm(0.05))

# Plot the test series together with the forecasted CVaRs
autoplot(test) + autolayer(CVaR)

# Compute the share of actual returns being less than the forecasted CVaR
mean(CVaR > test)

```

`@sct`
```{r}

```
