---
title: Forecasting
description: ""
---

## Training and Test data

```yaml
type: NormalExercise
key: 319dc6dc9f
xp: 100
```

One of the main purposes of time series analysis is to predict future values of a time series. 
If we are looking for a model aiming to predict the future we would like to choose the model with the "best" forecasting performance (i.e. "best" out-of-sample fit).
Compare this to Chapter 3 where we chose a model based on its in-sample fit. 

In order to evaluate our forecast of future values, we would have to wait until those were observed. 
In practice, we therefore wind back and pretend that only data until a specific date in the past are available. 
Then the remaining data can be used to evaluate the forecast. 

We call all values up to this specific date the training data because we use those to "train" (i.e. estimate) our model.
All later observed data form the test set because we use those to test the forecasting performance.

`@instructions`
- Create a training data set containing all observations of `con_supply2010` until the end of `2017` and assign it to the variable `train`.
- Create a test data set containing the remaining data and assign it to the variable `test`.

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
con_supply     <- getSymbols("IPB54100N", src = "FRED", auto.assign = FALSE)
con_supply_ts  <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1), end = c(2018,12))

```

`@sample_code`
```{r}
# The training data 


# The test data 


```

`@solution`
```{r}
# The training data 
train <- window(con_supply2010, end = c(2017,12))

# The test data 
test <- window(con_supply2010, start = c(2018,1))

```

`@sct`
```{r}
ex() %>% check_object("train") %>% check_equal()
ex() %>% check_object("test") %>% check_equal()
success_msg("Great!")
```

---

## Estimate a  Model for Trend and Seasonality

```yaml
type: NormalExercise
key: 1f04a8aad5
xp: 100
```

Before we are able to estimate some ARMA model and provide forecasts for it we have to account for trend and seasonality in our data.

For simplicity let us again assume the classical additive decomposition model with a linear time trend and a constant seasonality cycle for our data.
Making this assumption crucially simplifies things in a forecasting setting as it implies that both components behave deterministically in the future.
So we are not only able to estimate and remove these components for our train data, but are also able to easily forecast these components and remove them for our test data.

In this exercise you will first estimate trend and seasonality and remove it from the train data. The next exercise then shows you how to precisely handle these components to extract the random component from the test data.

`@instructions`
- Estimate trend and seasonality of `train` using `tslm()` and assign it to `seas_mod`.
- Save the estimated random component as `train_random`.

`@hint`
- This exercise is conceptually equal to exercises from Chapter 2, so it might help to recapitulate them.

`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
con_supply     <- getSymbols("IPB54100N", src = "FRED", auto.assign = FALSE)
con_supply_ts  <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1), end = c(2018,12))
train <- window(con_supply2010, end = c(2017,12)) 
test <- window(con_supply2010, start = c(2018,1))
```

`@sample_code`
```{r}
# Estimate trend and seasonality


# Save the estimated random component


```

`@solution`
```{r}
# Estimate trend and seasonality
seas_mod <- tslm(train ~ trend + season)

# Save the estimated random component
train_random <- residuals(seas_mod)

```

`@sct`
```{r}
ex() %>% check_object("seas_mod") %>% check_equal()
ex() %>% check_object("train_random") %>% check_equal()
success_msg("Great!")
```

---

## Forecast the Seasonal Model

```yaml
type: NormalExercise
key: af875b0bad
xp: 100
```

In this Exercise, we want to extract the random component of our time Series. 

You will use that data later to evaluate our model predictions.

`train`, `test` and `seas_mod` are already available in your working environment.

`@instructions`
- Calculate the random component of the train dataset and assign it to `train_rndm`.

- Predict the seasonal component of the test data and assign it to `test_seas`. You can use `forecast()` for this and access the prediction with `$mean`.

- Calculate the random component of the test data and assign it to `test_rndm`.

- Create `con_supply_random` a time series that combines `train_rndm` and `test_rndm` you can use `ts()` for that.

`@hint`
- Remember that the random component is that part of our seasonal model that can't be explained by seasonality or trend.

- When forecasting you have to specify how many steps you want to predict. In our example, the steps should be equal to the number of observations in the test data.

- Remember that you just need the deterministic part and the time series itself to calculate the random part.

- The time series should start at the same point as our train data and it should have the same frequency.

`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
con_supply     <- getSymbols("IPB54100N", src = "FRED", auto.assign = FALSE)
con_supply_ts  <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1), end = c(2018,12))
train <- window(con_supply2010, end = c(2017,12)) 
test <- window(con_supply2010, start = c(2018,1))
seas_mod <- tslm(train ~ trend + season)
train_random <- residuals(seas_mod)

```

`@sample_code`
```{r}
# Detrend and Deseasonalize training data


# Forecast Season + Trend for the test data


# Detrend and Deseasonalize test data


# Merge train and test together without trend and season
con_supply_random <- ts(c(    ,    ), start= start(   ), frequency= frequency(   ))
```

`@solution`
```{r}
# Forecast Season + Trend for the test data
test_seas <- forecast(seas_mod, h = length(test))$mean

# Detrend and Deseasonalize test data
test_random <- test - test_seas

# Merge train and test together without trend and season
all_random <- ts(c(train_rndm, test_rndm), start=start(train_random), frequency=frequency(test_random))

```

`@sct`
```{r}
ex() %>% check_function("residuals", index  = 1)
ex() %>% check_object("test_seas") %>% check_equal()
ex() %>% check_function("forecast", index  = 1) %>% check_arg("h") %>% check_equal()
ex() %>% check_object("test_random") %>% check_equal()
ex() %>% check_object("all_random") %>% check_equal()
success_msg("You Rock!")


```

---

## Dynamic and Static Forecasts for ARMA Models 1

```yaml
type: NormalExercise
key: 05ad85824e
xp: 100
```

We distinguish 2 types of forecasts: 

- dynamic forecasts predict multiple steps into the future (i.e. n-step-ahead)
- static forecasts predict only one step into the future (i.e. 1-step-ahead)

Lets start with an example of a dynamic forecast for an ARMA-model.

`@instructions`
- Fit an AR(1)-model to `train_random` and save the resulting object as `ar1_mod`. 
- Use `forecast()` to predict the values for the next year of the random component and save the result as `ar1_dyn_pred`.
- Use `autoplot()` to visualize the predicted values. What do you observe?

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
con_supply     <- getSymbols("IPB54100N", src = "FRED", auto.assign = FALSE)
con_supply_ts  <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1), end = c(2018,12))
train <- window(con_supply2010, end = c(2017,12)) 
test <- window(con_supply2010, start = c(2018,1))
seas_mod <- tslm(train ~ trend + season)
train_random <- residuals(seas_mod)
test_seas <- forecast(seas_mod, h = length(test))$mean
test_random <- test - test_seas
all_random <- ts(c(train_rndm, test_rndm), start=start(train_random), frequency=frequency(test_random))

```

`@sample_code`
```{r}
# Fit the AR(1) model 


# Forecast the Values for the next year


# Plot the forecast

```

`@solution`
```{r}
# Fit the AR(1) model 
ar1_mod <- Arima(train_random, order = c(1,0,0))

# Forecast the Values for the next year
ar1_dyn_pred <- forecast(ar1_mod, h = 24)

# Plot the Forecast
autoplot(ar1_dyn_pred)

```

`@sct`
```{r}
ex() %>% check_object("ar1_mod") %>% check_equal()
ex() %>% check_object("ar1_dyn_pred") %>% check_equal()
ex() %>% check_function("autoplot") %>% check_argument("x") %>% check_equal()
success_msg("Great!")
```

---

## Dynamic and Static Forecasts for ARMA Models 2

```yaml
type: NormalExercise
key: 785d9a6669
xp: 100
```

You could see in the exercise before that the dynamic forecast of an AR-Model converges very fast to its mean and hence provides not much value for predictions which are more than a couple of steps ahead of the present. For MA-Models there is even less dependence over time. 
For this reason ARMA-Models are usually used to perform (or improve) 1-step-ahead forecasts. 
What we want to do here is to produce 1-step-ahead forecasts for the entire test sample. This can be done as follows: 

1. Estimate the model coefficients on the training data (all data up to time $T$)
2. Use the estimated model and all Information available up to time $T$ to predict $y_{T+1}$
3. Use the estimated model and all Information available up to time $T+1$ to predict $y_{T+2}$
4. Continue until you have for every observation in the test data a prediction. 

With the function `Arima()` it is easy to accomplish that:
```
Arima(data, model = estimated_model)
```

`@instructions`
- Fit an ARMA(1,1) model to the training data
- Use the estimated model to compute 1-step-ahead forecasts for the entire test set

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
con_supply     <- getSymbols("IPB54100N", src = "FRED", auto.assign = FALSE)
con_supply_ts  <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1), end = c(2018,12))
train <- window(con_supply2010, end = c(2017,12)) 
test <- window(con_supply2010, start = c(2018,1))
seas_mod <- tslm(train ~ trend + season)
train_random <- residuals(seas_mod)
test_seas <- forecast(seas_mod, h = length(test))$mean
test_random <- test - test_seas
all_random <- ts(c(train_rndm, test_rndm), start=start(train_random), frequency=frequency(test_random))

```

`@sample_code`
```{r}
# Fit ARMA(1,1) model


# 1-step-ahead predictions


# Extract test set predictions

```

`@solution`
```{r}
# Fit ARMA(1,1) model
arma11 <- Arima(train_random, model = c(1,0,1))

# 1-step-ahead predictions
fitted_all <- fitted(Arima(all_random, arma11))

# Extract test set predictions
fitted_test <- fitted_all[-(1:length(train))]

```

`@sct`
```{r}
ex() %>% check_object("arma11") %>% check_equal()
ex() %>% check_object("fitted_all") %>% check_equal()
ex() %>% check_object("fitted_test") %>% check_equal()
success_msg("Great!")
```

---

## Model Selection Based on Static Forecast

```yaml
type: NormalExercise
key: 9d347a23b0
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
