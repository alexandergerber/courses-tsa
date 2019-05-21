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
So we are not only able to estimate and remove these components for our training data, but are also able to easily forecast these components and remove them from our test data.

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
- Use `seas_mod` to predict the seasonal component of the test data and assign the point forecast to `pred_seas`.
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
pred_seas <- forecast(seas_mod, h = length(test))$mean

# Plot your forecast
autoplot(test, series = "test") + autolayer(pred_seas)
```

`@sct`
```{r}
ex() %>% check_object("pred_seas") %>% check_equal()
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
Here the estimation was only based on the training data set.

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
test  <- window(con_supply2010, start = c(2018,1))
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
For this reason ARMA-Models are usually used to perform 1-step-ahead forecasts. 
What we want to do here is to produce 1-step-ahead forecasts for the entire test sample. This can be done as follows: 

1. Estimate the model coefficients on the training data (all data up to time $T$)
2. Use the estimated model and all Information available up to time $T$ to predict $y_{T+1}$
3. Use the estimated model and all Information available up to time $T+1$ to predict $y_{T+2}$
4. Continue until you have for every observation in the test data a prediction. 

You accomplish that with the function Arima:
```
Arima(data, model = estimated_model)
```
As data you should provide the complete detrended and deseasonalized data set (not only the test data) because otherwise there would be especially for the first couple of forecasts nothing to condition on (i.e. in the AR(1) case  $y_{T+1} = c + \phi y _{T} + \epsilon _t$ you need $y _T$ which is the last observation of the training data).

`@instructions`
- Fit an ARMA(1,1) model to the training data
- Use the estimated model to compute 1-step-ahead forecasts for the entire test set (`all_random`)
- Extract only the predictions for the test data by using `window()`

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
fitted_test <- window(fitted_all, start = c(2018,1))




```

`@sct`
```{r}
ex() %>% check_object("arma11") %>% check_equal()
ex() %>% check_object("fitted_all") %>% check_equal()
ex() %>% check_object("fitted_test") %>% check_equal()
success_msg("Great!")
```

---

## Model Selection Based on Static Forecast 1

```yaml
type: NormalExercise
key: 9d347a23b0
xp: 100
```

We now know how to perform 1-step-ahead forecasts on the test data. This can be used to select the model with the best forecast performance. 

1. Estimate a variety of models.
2. Make 1-step-ahead predictions for the test period for every model
3. Compute a performance measure e.g. the MSE for every model
4. Select the model with the best performance

To efficiently perform the first step we can use a loop that iterates over a matrix which contains the the model orders p and q of the considered ARMA models. 
This matrix can be created using the `expand.grid()` function.

Here is a small example:
`expand.grid(p = 0:1,q = 3:4)`
This will give you the following matrix.

|  | p | q |
|---|------|------|
| 1 | 0 | 3 |
| 2 | 1 | 3 |
| 3 | 0 | 4 |
| 4 | 1 | 4 |

`@instructions`
- Expand a grid of possible model orders p and q. Consider here only orders from 0 to 2 for both, the MA and the AR component. Assign the result to `grid`.
- Create an empty list `models` as placeholder for the for loop. You can do that with `list()`.
- Write a for loop that iterates over the rows of your grid, estimates the models bast on `train_random` and writes the results into the list `models`.

`@hint`
- If `i` is your loop variable and you created the order matrix as above then `grid[i, "p"]` contains the order for the AR part and `grid[i, "q"]` contains the order of the MA part.

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


# Create an empty list `mods` where the models can be stored


# Write a for loop that iterates over the rows of your grid and estimates the models


```

`@solution`
```{r}
# Expand a grid of possible model orders
grid <- expand.grid(0:2,0:2)

# Create an empty list `mods` where the models can be stored

models <-  list()

for(i in 1:nrow(grid)){
  models[[i]] <- Arima(train_random, order = c(grid[i,1],0,grid[i,2]))
}
```

`@sct`
```{r}
ex() %>% check_object("grid") %>% check_equal()
ex() %>% check_function("expand.grid") 
ex() %>% check_object("models") %>% check_equal()
ex() %>% check_function("Arima") %>% check_arg("order") %>% check_equal()
success_msg("Awesome!")

```

---

## Model Selection Based on Static Forecast 2

```yaml
type: NormalExercise
key: dad13b9fc0
xp: 100
```

Recall the 4 steps the last exercise:

1. Estimate a variety of models.
2. Make 1-step-ahead predictions for the test period for every model
3. Compute a performance measure e.g. the MSE for every model
4. Select the model with the best performance

We already completed step 1.

`@instructions`
- Create `predictions`, an empty list for your predictions, and `mse`, an empty vector for the MSE's.

- Compute one step ahead predictions for the whole test data and for each model. Use a for loop for this.

- In the same for loop. Extract the fitted values from your predictions and compute the MSE.

- Save the model with the lowest MSE as `best_model`.

`@hint`
- use `list()` to create an empty list and `c()` to create an empty vector.

- Recall from exercise 6 how to compute one step ahead predictions.

- Recall how the MSE is defined. You need your computed predictions and the test data.

- You can use `which.min()` to evaluate which is the smallest element of a vector.

`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
con_supply     <- getSymbols("IPB54100N", src = "FRED", auto.assign = FALSE)
con_supply_ts  <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1), end = c(2019,4))
train <- window(con_supply2010, end = c(2018,4)) 
test <- window(con_supply2010, start = c(2018,5))
seas_mod <- tslm(train ~ trend + season)
train_random <- residuals(seas_mod)
test_seas <- forecast(seas_mod, h = length(test))$mean
test_random <- test - test_seas
all_random <- ts(c(train_random, test_random), start=start(train_random), frequency=frequency(test_random))
grid <- expand.grid(0:2,0:2)
mods <-  list()
for(i in 1:nrow(grid)){
  mods[[i]] <- Arima(train_random, order = c(grid[i,1],0,grid[i,2]))
}
```

`@sample_code`
```{r}
# Create `predictions` and `mse`


# Write the for loop

# Save the best model

```

`@solution`
```{r}
# Create `predictions` and `mses`
mse <- numeric(length(mods))

# Write the for loop
for(i in 1:length(mods)){
  predictions <- fitted(Arima(all_random, model = mods[[i]]))
  test_prediction <- window(predictions, start = c(2018,5))
  mse[i] <- mean((test_prediction - test_random)^2)
}

# Save the best model
best_model <- mods[[which.min(mse)]]
```

`@sct`
```{r}
ex() %>% check_object("predictions") %>% check_equal()
ex() %>% check_object("mse") %>% check_equal()
ex() %>% check_object("best_model") %>% check_equal()
success_msg("Awesome!")
```

---

## Predict the Future

```yaml
type: NormalExercise
key: a09a6b87b7
xp: 100
```

The MA(1) model provided the best out-of-sample forecast for the random component. Equipped with this knowledge we can make a forecast for the future. 
It would be a waste of information if we only used the training data set for our final model. Hence, we reestimate our final model with all available data. 

We also need to remember that we need to add the seasonal and the random component to get our final forecast.

`@instructions`
- Estimate the MA(1) model using `àll_random` and assign it to `final_arma_model`
- Forecast the random component for the first step into the future and assign it to `forecast_random`
- Forecast the trend and the seasonal component and assign it to `forecast_season`  
- Add the trend and the seasonal component to the forecasted random component and assign the result to `forecast_final`

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
con_supply     <- getSymbols("IPB54100N", src = "FRED", auto.assign = FALSE)
con_supply_ts  <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1), end = c(2019,4))
train <- window(con_supply2010, end = c(2018,4)) 
test <- window(con_supply2010, start = c(2018,5))
seas_mod <- tslm(train ~ trend + season)
train_random <- residuals(seas_mod)
test_seas <- forecast(seas_mod, h = length(test))$mean
test_random <- test - test_seas
all_random <- ts(c(train_random, test_random), start=start(train_random), frequency=frequency(test_random))
```

`@sample_code`
```{r}
# Estimate the final arma model


# Forecast the random component


# Estimate the final arma model


# Forecast trend and seasonality


# Final forecast

```

`@solution`
```{r}
# Estimate the final arma model
final_arma_model <- Arima(all_random, c(0,0,1))

# Forecast the random component
forecast_random <- forecast(final_arma_model, h = 1)$mean

# Estimate the final arma model
seasonal_model <- tslm(con_supply2010 ~ trend + season)

# Forecast trend and seasonality
forecast_season <- forecast(seasonal_model, h = 1)$mean

# Final forecast
forecast_final <- forecast_random + forecast_season
```

`@sct`
```{r}
ex() %>% check_object("final_arma_model") %>% check_equal()
ex() %>% check_object("forecast_random") %>% check_equal()
ex() %>% check_object("seasonal_model") %>% check_equal()
ex() %>% check_object("forecast_season") %>% check_equal()
ex() %>% check_object("forecast_final") %>% check_equal()
success_msg("Unbelievable!")
```
