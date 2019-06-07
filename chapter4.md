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
- Create a training data set containing all observations of `con_supply2010` until April 2018 and assign it to the variable `train`.
- Create a test data set containing the remaining data and assign it to the variable `test`.
- Note: `train` and `test` will be for the rest of this chapter in your working environment.

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
# The training data 


# The test data 


```

`@solution`
```{r}
# The training data 
train <- window(con_supply2010, end = c(2018,4))

# The test data 
test <- window(con_supply2010, start = c(2018,5))

```

`@sct`
```{r}
ex() %>% check_object("train") %>% check_equal()
ex() %>% check_object("test") %>% check_equal()
success_msg("You made this look trivial!")
```

---

## Estimate a  Model for Trend and Seasonality

```yaml
type: NormalExercise
key: 1f04a8aad5
xp: 100
```

Before we can forecast we need to repeat the steps from the previous two chapters on the training data:

1. Estimate trend and seasonality 
2. Fit an ARMA model to the residuals. 

For simplicity let us again assume the classical additive decomposition model with a linear time trend and a constant seasonality cycle.
Making this assumption crucially simplifies things in a forecasting setting as it implies that both components behave deterministically in the future.
So we are not only able to estimate and remove these components for our training data, but are also able to easily forecast these components and remove them from our test data.

`@instructions`
- Estimate trend and seasonality of `train` using `tslm()` and assign it to `seasonal_model`.
- Save the estimated random component as `train_random`.

`@hint`
- This exercise is conceptually equal to exercises from Chapter 2, so it might help to recapitulate them.

`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
download.file("https://assets.datacamp.com/production/repositories/4944/datasets/6ad23686e37c771ff807b4d6060bb0ec9a2f543e/con_supply.rda", destfile = "con_supply.rda")
load("con_supply.rda")
con_supply_ts <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1))
train <- window(con_supply2010, end = c(2018,4))
test <- window(con_supply2010, start = c(2018,5))
```

`@sample_code`
```{r}
# Estimate trend and seasonality


# Save the estimated random component


```

`@solution`
```{r}
# Estimate trend and seasonality
seasonal_model <- tslm(train ~ trend + season)

# Save the estimated random component
train_random <- residuals(seasonal_model)

```

`@sct`
```{r}
ex() %>% check_object("seasonal_model") %>% check_equal()
ex() %>% check_object("train_random") %>% check_equal()
success_msg("That was too easy huh?")
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
- Use `seasonal_model` to predict the seasonal component of the test data and assign the point forecasts to `seasonal_forecasts`.
- Plot the test data together with your trend and seasonality forecast.

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
download.file("https://assets.datacamp.com/production/repositories/4944/datasets/6ad23686e37c771ff807b4d6060bb0ec9a2f543e/con_supply.rda", destfile = "con_supply.rda")
load("con_supply.rda")
con_supply_ts <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1))
train <- window(con_supply2010, end = c(2018,4))
test <- window(con_supply2010, start = c(2018,5))
seasonal_model <- tslm(train ~ trend + season)
train_random <- residuals(seasonal_model)

```

`@sample_code`
```{r}
# Forecast season + trend for the test data


# Plot your forecast


```

`@solution`
```{r}
# Forecast season + trend for the test data
seasonal_forecasts <- forecast(seasonal_model, h = length(test))$mean

# Plot your forecast
autoplot(test, series = "test") + autolayer(seasonal_forecasts)

```

`@sct`
```{r}
ex() %>% check_object("seasonal_forecasts") %>% check_equal()
ex() %>% check_function("forecast") %>% check_arg("h") %>% check_equal()
success_msg("Keep grinding!")


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
Here the estimation is only based on the training data.

`@instructions`
- Compute the random component of the test data by subtracting the predicted trend and season from the test data and assign it to `test_random`.
- Create `all_random`, a time series that combines `train_random` and `test_random`.

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
download.file("https://assets.datacamp.com/production/repositories/4944/datasets/6ad23686e37c771ff807b4d6060bb0ec9a2f543e/con_supply.rda", destfile = "con_supply.rda")
load("con_supply.rda")
con_supply_ts <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1))
train <- window(con_supply2010, end = c(2018,4))
test <- window(con_supply2010, start = c(2018,5))
seasonal_model <- tslm(train ~ trend + season)
train_random <- residuals(seasonal_model)
seasonal_forecasts <- forecast(seasonal_model, h = length(test))$mean
```

`@sample_code`
```{r}
# Detrend and deseasonalize the test data


# Merge train and test together without trend and season
con_supply_random <- ts(c(    ,    ), start = start(   ), frequency = frequency(   ))

```

`@solution`
```{r}
# Detrend and deseasonalize the test data
test_random <- test - seasonal_forecasts

# Merge train and test together without trend and season
all_random <- ts(c(train_random, test_random), start = start(train_random), frequency = frequency(test_random))

```

`@sct`
```{r}
ex() %>% check_object("test_random") %>% check_equal()
ex() %>% check_object("all_random") %>% check_equal()
success_msg("You rock!")
```

---

## Dynamic and Static Forecasts for ARMA Models 1

```yaml
type: NormalExercise
key: 05ad85824e
xp: 100
```

We distinguish between two types of forecasts: 

- dynamic forecasts predict multiple steps into the future (i.e. n-step-ahead)
- static forecasts predict only one step into the future (i.e. 1-step-ahead)

Lets start with an example of a dynamic forecast for an ARMA model.

`@instructions`
- Fit an AR(1)-model to `train_random` and save the resulting object as `ar1_model`. 
- Use `forecast()` to predict the values for the next year of the random component and save the result as `ar1_dynamic_forecasts`.
- Use `autoplot()` to visualize the predicted values. What do you observe?

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
download.file("https://assets.datacamp.com/production/repositories/4944/datasets/6ad23686e37c771ff807b4d6060bb0ec9a2f543e/con_supply.rda", destfile = "con_supply.rda")
load("con_supply.rda")
con_supply_ts <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1))
train <- window(con_supply2010, end = c(2018,4))
test <- window(con_supply2010, start = c(2018,5))
seasonal_model <- tslm(train ~ trend + season)
train_random <- residuals(seasonal_model)
seasonal_forecasts <- forecast(seasonal_model, h = length(test))$mean
test_random <- test - seasonal_forecasts
all_random <- ts(c(train_random, test_random), start = start(train_random), frequency = frequency(test_random))

```

`@sample_code`
```{r}
# Fit the AR(1) model 


# Forecast the values for the next year


# Plot the forecast


```

`@solution`
```{r}
# Fit the AR(1) model 
ar1_model <- Arima(train_random, order = c(1, 0, 0))

# Forecast the values for the next year
ar1_dynamic_forecasts <- forecast(ar1_model, h = 24)

# Plot the forecast
autoplot(ar1_dynamic_forecasts)

```

`@sct`
```{r}
ex() %>% check_object("ar1_model") %>% check_equal()
ex() %>% check_object("ar1_dynamic_forecasts") %>% check_equal()
ex() %>% check_function("autoplot") %>% check_arg("object") %>% check_equal()
success_msg("Like a boss!")
```

---

## Dynamic and Static Forecasts for ARMA Models 2

```yaml
type: NormalExercise
key: 785d9a6669
xp: 100
```

You could see in the previous exercise that the dynamic forecast of an AR model converges very fast to its mean and hence provides not much value for predictions which are more than a couple of steps ahead of the present. This is true for all members of the ARMA(p,q) family. 
For this reason ARMA models are usually used to perform 1-step-ahead forecasts. 
What we want to do here is to produce 1-step-ahead forecasts for the entire test sample. 
This can be done as follows: 

1. Estimate the model coefficients on the training data (all data up to time $T$)
2. Use the estimated model and all information available up to time $T$ to predict $y_{T+1}$
3. Use the estimated model and all information available up to time $T+1$ to predict $y_{T+2}$
4. Continue until you have for every observation in the test data a prediction. 

This can be accomplished by using the function `Arima()` from the `forecast` package: 
```
Arima(data, model = estimated_model)
```
You should provide the complete detrended and deseasonalized data set (not only the test data) because otherwise there would be nothing to condition on, especially for the first couple of forecasts (e.g. in the AR(1) case  $y_{T+1} = c + \phi y _{T} + \epsilon _t$ you need $y _T$ which is the last observation of the training data).

`@instructions`
- Fit an ARMA(1,1) model to the training data.
- Use the estimated model to compute 1-step-ahead predictions for the entire data set (`all_random`). You can access those with `fitted()`. Assign the result to `one_step_ahead_all`.
- Extract only the predictions for the test data using `window()`. Assign the result to `one_step_ahead_test`.

`@hint`


`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
download.file("https://assets.datacamp.com/production/repositories/4944/datasets/6ad23686e37c771ff807b4d6060bb0ec9a2f543e/con_supply.rda", destfile = "con_supply.rda")
load("con_supply.rda")
con_supply_ts <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1))
train <- window(con_supply2010, end = c(2018, 4))
test <- window(con_supply2010, start = c(2018, 5))
seasonal_model <- tslm(train ~ trend + season)
train_random <- residuals(seasonal_model)
seasonal_forecasts <- forecast(seasonal_model, h = length(test))$mean
test_random <- test - seasonal_forecasts
all_random <- ts(c(train_random, test_random), start=start(train_random), frequency=frequency(test_random))

```

`@sample_code`
```{r}
# Fit an ARMA(1,1) model


# 1-step-ahead predictions


# Extract test set predictions


```

`@solution`
```{r}
# Fit an ARMA(1,1) model
arma11 <- Arima(train_random, order = c(1, 0, 1))

# 1-step-ahead predictions
one_step_ahead_all <- fitted(Arima(all_random, model = arma11))

# Extract test set predictions
one_step_ahead_test <- window(one_step_ahead_all, start = c(2018, 5))

```

`@sct`
```{r}
ex() %>% check_object("arma11") %>% check_equal()
ex() %>% check_object("one_step_ahead_all") %>% check_equal()
ex() %>% check_object("one_step_ahead_test") %>% check_equal()
success_msg("Yay!")
```

---

## Model Selection Based on Static Forecast 1

```yaml
type: NormalExercise
key: 9d347a23b0
xp: 100
```

We now know how to perform 1-step-ahead forecasts on the test data. This can be used to select the model with the best forecast performance. 
One way is to proceed as follows: 

1. Estimate a variety of candidate models
2. Make 1-step-ahead predictions for the test period for every model
3. Compute a performance measure e.g. the MSE for every model
4. Select the model with the best performance (in terms of the lowest MSE)

To efficiently perform the first step we can use a `for()` loop that iterates over a matrix which contains the model orders p and q of the considered ARMA models. 
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
- Expand a grid of possible model orders p and q. Consider here only orders from 0 to 2 for both the AR and MA component. Assign the result to `grid`.
- Create an empty list `models` as a placeholder for the `for()` loop. You can do that with `list()`.
- Write a `for()` loop that iterates over the rows of your grid, estimates the models based on `train_random` and writes the results into the list `models`.

`@hint`
- If `i` is your loop variable and you created the order matrix as above then `grid[i, "p"]` contains the order for the AR part and `grid[i, "q"]` contains the order of the MA part.

`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
download.file("https://assets.datacamp.com/production/repositories/4944/datasets/6ad23686e37c771ff807b4d6060bb0ec9a2f543e/con_supply.rda", destfile = "con_supply.rda")
load("con_supply.rda")
con_supply_ts <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1))
train <- window(con_supply2010, end = c(2018, 4))
test <- window(con_supply2010, start = c(2018, 5))
seasonal_model <- tslm(train ~ trend + season)
train_random <- residuals(seasonal_model)
seasonal_forecasts <- forecast(seasonal_model, h = length(test))$mean
test_random <- test - seasonal_forecasts
all_random <- ts(c(train_random, test_random), start = start(train_random), frequency = frequency(test_random))
```

`@sample_code`
```{r}
# Expand a grid of possible model orders


# Create an empty list `models` where the models can be stored


# Write a for() loop that iterates over the rows of your grid and estimates the models



```

`@solution`
```{r}
# Expand a grid of possible model orders
grid <- expand.grid(p = 0:2, q = 0:2)

# Create an empty list `models` where the models can be stored
models <-  list()

# Write a for() loop that iterates over the rows of your grid and estimates the models
for(i in 1:nrow(grid)){
  models[[i]] <- Arima(train_random, order = c(grid[i, "p"], 0, grid[i, "q"]))
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

Recall the four model selection steps from the last exercise:

1. Estimate a variety of candidate models
2. Make 1-step-ahead predictions for the test period for every model
3. Compute a performance measure e.g. the MSE for every model
4. Select the model with the best performance

We already completed the first step. Now we can use the estimated models to make predictions on the test set and compare their performance.

`@instructions`
- Create `forecasts`, an empty list for your predictions, and `mse`, an empty vector for the MSEs.

- Use every model in `models` to make 1-step-ahead predictions for the test data. Use a `for()` loop for this.

- In the same `for()` loop: Extract the fitted values from your predictions and compute the MSE.

- Assign the model with the lowest MSE to `best_model`.

`@hint`
- Use `list()` to create an empty list and `c()` to create an empty vector.

- Recall from Exercise 6 how to compute 1-step-ahead predictions.

- Recall how the MSE is defined. You need your computed predictions and the test data.

- You can use `which.min()` to evaluate the location of the smallest element in a vector.

`@pre_exercise_code`
```{r}
library(quantmod)
library(forecast)
download.file("https://assets.datacamp.com/production/repositories/4944/datasets/6ad23686e37c771ff807b4d6060bb0ec9a2f543e/con_supply.rda", destfile = "con_supply.rda")
load("con_supply.rda")
con_supply_ts <- ts(con_supply, start = c(1947, 1), frequency = 12)
con_supply2010 <- window(con_supply_ts, start = c(2010, 1))
train <- window(con_supply2010, end = c(2018, 4))
test <- window(con_supply2010, start = c(2018, 5))
seasonal_model <- tslm(train ~ trend + season)
train_random <- residuals(seasonal_model)
seasonal_forecasts <- forecast(seasonal_model, h = length(test))$mean
test_random <- test - seasonal_forecasts
all_random <- ts(c(train_random, test_random), start = start(train_random), frequency = frequency(test_random))
grid <- expand.grid(p = 0:2, q = 0:2)
models <-  list()
for(i in 1:nrow(grid)){
  models[[i]] <- Arima(train_random, order = c(grid[i, "p"], 0, grid[i, "q"]))
}
```

`@sample_code`
```{r}
# Create `forecasts` and `mse`



# Write the for() loop






# Save the best model


```

`@solution`
```{r}
# Create `forecasts` and `mse`
forecasts <- list()
mse <- numeric(length(models))

# Write the for() loop
for(i in 1:length(models)){
  forecasts[[i]] <- fitted(Arima(all_random, model = models[[i]]))
  forecast_test <- window(forecasts[[i]], start = c(2018, 5))
  mse[i] <- mean((forecast_test - test_random)^2)
}

# Save the best model
best_model <- models[[which.min(mse)]]

```

`@sct`
```{r}
ex() %>% check_object("forecasts") %>% check_equal()
ex() %>% check_object("mse") %>% check_equal()
ex() %>% check_object("best_model") %>% check_equal()
success_msg("Fantastic!")
```

---

## Predict the Future

```yaml
type: NormalExercise
key: a09a6b87b7
xp: 100
```

The MA(1) model provided the best out-of-sample forecast for the random component. Equipped with this knowledge we can make a forecast for the future. 
It would be a waste of information if we only used the training data for our final model. Hence, we reestimate our final model with all available data. 

We also need to remember that we need to add the seasonal and the random component to get our final forecast.

`@instructions`
- Estimate the trend and seasonality of `con_supply2010` and assign it to `seasonal_model`.
- Fit a MA(1) model to the residual random component and assign it to `final_arma_model`.
- Forecast the trend and seasonal component 1-step-ahead and assign it to `season_forecast`.
- Forecast the random component 1-step-ahead and assign it to `random_forecast`.
- Add the forecast of the trend and seasonal component to the forecast of the random component and assign the result to `final_forecast`.

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
# Estimate trend and seasonality


# Estimate the final ARMA model


# Forecast trend and seasonality


# Forecast the random component


# Final forecast


```

`@solution`
```{r}
# Estimate trend and seasonality
seasonal_model <- tslm(con_supply2010 ~ trend + season)

# Estimate the final ARMA model
final_arma_model <- Arima(residuals(seasonal_model), c(0, 0, 1))

# Forecast trend and seasonality
season_forecast <- forecast(seasonal_model, h = 1)$mean

# Forecast the random component
random_forecast <- forecast(final_arma_model, h = 1)$mean

# Final forecast
final_forecast <- season_forecast + random_forecast

```

`@sct`
```{r}
ex() %>% check_object("seasonal_model") %>% check_equal()
ex() %>% check_object("final_arma_model") %>% check_equal()
ex() %>% check_object("season_forecast") %>% check_equal()
ex() %>% check_object("random_forecast") %>% check_equal()
ex() %>% check_object("final_forecast") %>% check_equal()
success_msg("Unbelievable, you finished Chapter 9. Be ARMAzed!")
```
