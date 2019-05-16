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
- Note: `train` and `test` will be for the rest of this chapter in your working environment.

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

Before we can forecast we need to repeat the steps from the previous two chapters on the training data:

- deal with trend and seasonality 
- fit an ARMA model to the residuals. 

For simplicity let us again assume the classical additive decomposition model with a linear time trend and a constant seasonality cycle for our data.
Making this assumption crucially simplifies things in a forecasting setting as it implies that both components behave deterministically in the future.
So we are not only able to estimate and remove these components for our training data, but are also able to easily forecast these components and remove them for our test data.

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

Now we have estimated a model that handles trend and seasonality for the training data. 
Our next aim is to use this model to predict the trend and the seasonal component of the test data. 

For this task the `forecast` package provides the function `forecast()`. 
This function can be used as follows: 

```
forecast(fitted_model, h = number_of_steps_to_forecast)
```
This results in a point forecast but also provides prediction intervals. 
For now we are only interested in the former which can be accessed by `$mean`.

`@instructions`
- Use `seas_mod` to predict the seasonal component of the test data and assign the point forecast to `test_seas`.
- Plot the test data together with your trend and seasonality forecast

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
# Forecast Season + Trend for the test data


# Plot your forecast

```

`@solution`
```{r}
# Forecast Season + Trend for the test data
test_seas <- forecast(seas_mod, h = length(test))$mean

# Plot your forecast

```

`@sct`
```{r}
ex() %>% check_object("test_seas") %>% check_equal()
ex() %>% check_function("forecast") %>% check_arg("h") %>% check_equal()
success_msg("You Rock!")


```

---

## Prepare the Test Data

```yaml
type: NormalExercise
key: 7cd3b028f5
xp: 100
```

For later use we also want to detrend and deseasonalize the test data. 
Furthermore, we need the cleaned training and test data combined in one data set. 

Note that this is not equivalent to what we have done in chapter 3 because there we used all data for the estimation.
Here the estimation was only based on a part of the complete data set.

`@instructions`
- Compute the random component of the test data and assign it to `test_random`.
- Create `all_random` a time series that combines `train_random` and `test_random`.

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
```

`@sample_code`
```{r}
# Detrend and Deseasonalize test data


# Merge train and test together without trend and season
con_supply_random <- ts(c(    ,    ), start= start(   ), frequency= frequency(   ))
```

`@solution`
```{r}
# Detrend and Deseasonalize test data
test_random <- test - test_seas

# Merge train and test together without trend and season
all_random <- ts(c(train_random, test_random), start=start(train_random), frequency=frequency(test_random))
```

`@sct`
```{r}
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
all_random <- ts(c(train_random, test_random), start=start(train_random), frequency=frequency(test_random))

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
ex() %>% check_function("autoplot") %>% check_arg("object") %>% check_equal()
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
all_random <- ts(c(train_random, test_random), start=start(train_random), frequency=frequency(test_random))

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
arma11 <- Arima(train_random, order = c(1,0,1))

# 1-step-ahead predictions
fitted_all <- fitted(Arima(all_random, model = arma11))

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

Forecasting is also a great way to do model selection. It is pretty straightforward:

1. Estimate a variety of models.
2. Predict the values for the test period.
3. Compute a performance measure e.g. the MSE.
4. Evaluate the performance measure.

You are asked to perform the first step in this exercise. Its the most challenging one. For estimating a variety of models we can use a `for()` loop.

Furthermore, we need to gather all possible model orders in a matrix in order to use them in our for loop. This matrix can be easily created using the `expand.grid()` function.

Here is a small example:
`expand.grid(0:1,3:4)`
This will give you the following matrix.

|  | Var1 | Var2 |
|---|------|------|
| 1 | 0 | 3 |
| 2 | 1 | 3 |
| 3 | 0 | 4 |
| 4 | 1 | 4 |


`train_random` is already loaded in your working environment.

Now it's time to practise!

`@instructions`
- Expand a grid of possible model orders. Consider orders from 0 to 2 for both, MA and AR, components.

- Write a for loop that iterates over the rows of your grid and estimates the models

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
all_random <- ts(c(train_random, test_random), start=start(train_random), frequency=frequency(test_random))
```

`@sample_code`
```{r}
# Expand a grid of possible model orders
grid <- expand.grid(0:2,0:2)

# Create an empty list `mods` where the models can be stored

mods <-  list()

# Write a for loop that iterates over the rows of your grid and estimates the models

for(i in 1:nrow(grid)){
  mods[i] <- Arima(train_random, order = c(grid[i,1],0,grid[i,2]))
}
```

`@solution`
```{r}
# Expand a grid of possible model orders
grid <- expand.grid(0:2,0:2)

# Create an empty list `mods` where the models can be stored

mods <-  list()

for(i in 1:nrow(grid)){
  mods[i] <- Arima(train_random, order = c(grid[i,1],0,grid[i,2]))
}
```

`@sct`
```{r}
success_msg("Great!")
```
