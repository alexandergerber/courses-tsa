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

Financial time series' often exhibit a dependence structure previously introduced model classes (e.g. ARMA models) fail to capture.
But before being able to elaborate more on this, we first need to download and prepare some financial time series. 

For this chapter we will work with closing prices from the tech firm Apple. Similarly to Chapter 1 we will make use of the function `getSymbols()` from the `quantmod` package to load the data.

`@instructions`
- Import the Apple closing prices by passing the relevant symbol (google it, if you don't know it!) to `getSymbols("your_symbol", src = "yahoo", auto.assign = FALSE)[, "your_symbol.Close"]` and assign the result to `apple`. Then plot `apple`. Does the series look stationary?
- Compute the log returns of `apple` and restrict the observation period (2015-01-01 - 2019-06-01). Assign the result to `log_returns` and plot it. Does the return series look stationary?
- Split the return series into a test (2015-01-01 - 2018-12-31) and train (2019-01-01 - 2019-06-01) data set and save the results as objects of class `ts()` in `train` and `test`, respectively.

`@hint`
- The Yahoo Finance symbol for Apple is `AAPL`. Thus the closing prices can be obtained using `[, "AAPL.Close"]`.
- Log returns are defined as $r\_t=\log\left(\frac{S\_t}{S\_{t-1}}\right)=\log(S\_t)-\log(S\_{t-1})$, where $S_t$ denotes the closing price of Apple at time $t$.

`@pre_exercise_code`
```{r}
library(forecast)
library(quantmod)
```

`@sample_code`
```{r}
# Import and plot the closing prices of Apple



# Compute and plot the log returns



# Split the return series into a train and test series



```

`@solution`
```{r}
# Load and plot the closing prices of Apple
apple <- getSymbols("AAPL", src = "yahoo", auto.assign = FALSE)[, "AAPL.Close"]
autoplot(apple)

# Compute and plot the log returns
log_returns <- window(diff(log(apple)), start = "2015-01-01", end = "2019-06-01")
autoplot(log_returns)

# Split the return series into a train and test series
train <- ts(window(log_returns, end = "2018-12-31"))
test <- ts(window(log_returns, start = "2019-01-01"))

```

`@sct`
```{r}
ex() %>% check_object("apple") %>% check_equal()
ex() %>% check_function("autoplot", index = 1) %>% check_arg("object") %>% check_equal()
ex() %>% check_object("log_returns") %>% check_equal()
ex() %>% check_function("autoplot", index = 2) %>% check_arg("object") %>% check_equal()
ex() %>% check_object("train") %>% check_equal()
ex() %>% check_object("test") %>% check_equal()
success_msg("Correct! The original series does not look stationary, however the return series does.")
```

---

## Detecting Dependence Structure within the Return Series

```yaml
type: NormalExercise
key: 28573c3590
xp: 100
```

The series of log returns seems to randomly fluctuate around zero indicating that little to no autocorrelation is prevalent. 
However when considering the squared log returns we do indeed spot a dependence structure. In particular, there seems to be a clustering in that large squared log returns tend to occur consecutively.

Such volatility clustering (also known as conditional heteroscedasticity) can be captured with so-called (Generalized) AutoRegressive Conditional Heteroscedasticity models, short (G)ARCH models.

So in order to check whether a (G)ARCH model is an appropriate choice we should investigate the autocorrelation structure of the raw and squared return series.

`@instructions`
- Plot the ACF estimate of the raw `train` return series.
- Plot the ACF estimate of the squared `train` return series.

`@hint`


`@pre_exercise_code`
```{r}
library(forecast)
library(quantmod)
apple <- getSymbols('AAPL', auto.assign = F)$AAPL.Close
log_returns <- window(diff(log(apple)), start = "2015-01-01")
train <- ts(window(log_returns, end = "2018-12-31"))
```

`@sample_code`
```{r}
# Estimate the ACF of the raw train return series


# Estimate the ACF of the squared train return series


```

`@solution`
```{r}
# Estimate the ACF of the raw train return series
ggAcf(train)

# Estimate the ACF of the squared train return series
ggAcf(train^2)

```

`@sct`
```{r}
ex() %>% check_function("ggAcf", index = 1) %>% check_arg("x") %>% check_equal()
ex() %>% check_function("ggAcf", index = 2) %>% check_arg("x") %>% check_equal()
success_msg("Correct! It looks like a (G)ARCH model fits well here.")
```

---

## Fitting (G)ARCH Models

```yaml
type: NormalExercise
key: decd27689a
xp: 100
```

After finding evidence for a serial structure in the squared log returns, a model from the (G)ARCH class seems to be the right choice.

Fitting GARCH(p, q) models in R can be done by the function `garchFit()` from the `fGarch` package.
The syntax is as follows:

```
garchFit(~garch(p, q), data = time_series, include.mean = FALSE, trace = FALSE).
```

Setting `include.mean = FALSE` fixes the mean at zero during the optimization process which, when working with financial time series', is a reasonable assumption (why?).
With `trace = FALSE` we suppress any output printed out during the optimization process.

`@instructions`
- Load the `fGarch` package. It will be loaded for the rest of the course. 
- Fit an ARCH(4) to `train` and assign the resulting model object to `arch4`.
- Fit a GARCH(1, 2) to `train` and assign the resulting model object to `garch12`.

`@hint`
- The ARCH(p) model arises as a special case of the GARCH(p, q) model.

`@pre_exercise_code`
```{r}
library(forecast)
library(quantmod)
apple <- getSymbols('AAPL', auto.assign = F)$AAPL.Close
log_returns <- window(diff(log(apple)), start = "2015-01-01")
train <- ts(window(log_returns, end = "2018-12-31"))
```

`@sample_code`
```{r}
# Load the fGarch package


# Fit an ARCH(4) to the return series


# Fit a GARCH(1, 2) to the return series


```

`@solution`
```{r}
# Load the fGarch package
library(fGarch)

# Fit an ARCH(4) to the return series
arch4 <- garchFit(~garch(4, 0), data = train, include.mean = FALSE, trace = FALSE)

# Fit a GARCH(1, 2) to the return series
garch12 <- garchFit(~garch(1, 2), data = train, include.mean = FALSE, trace = FALSE)

```

`@sct`
```{r}
ex() %>% check_library("fGarch")
ex() %>% check_object("arch4") %>% check_equal()
ex() %>% check_object("garch12") %>% check_equal()
success_msg("Correct!")
```

---

## Model Selection

```yaml
type: NormalExercise
key: 71c752792e
xp: 100
```

To help us decide which GARCH(p,q) model to choose among a set of candidate models we can make use of information criteria again, e.g AIC or BIC.
Since there is no function which automates this process, such as `auto.arima()` for ARMA models, we have to code a solution on our own. 

We use a similar approach to the model selection step in chapter 4 where we used the $\widehat{MSFE}\;$ to select a model. 

1. Construct a grid of all considered model orders
2. Loop over the grid to fit the models  
4. Compute the value of the information criterion for every model
3. Choose the model with the lowest value for the information criterion 

There is one difficulty: `garchFit()` requires a formula of the form `~garch(p,q)` to set the model order.
If we want to switch `p` and `q` in every round of the loop we need to manipulate the formula. With the function 
`paste()` character strings can be glued together like this
```
a <- 42
paste("a = ", a)`
```
Furthermore, we can use the function `formula()` to convert a character string to a `formula` (e.g. `formula("y ~ x")`). 
If we combine both we can dynamically create    
new formulas within the loop as follows

```
formula(paste("~garch(",p,",",q,")"))
```

Finally, you can extract a vector with values for several information criteria from an object returned by `garchFit`
with `garch_model@fit$ics`.

`@instructions`
- Create a grid of possible model orders containing all combinations of $p = \{1,2,3\}$ and $q = \{1,2,3\}$ and assign it to `grid`. 
- Create as placeholders the list `garch_models` and the vector `aic`. The length of both should equal the number of rows in `grid`.
- Loop over the grid to fit all models and save each model as an entry in `garch_models` and the value of the AIC as an entry in `aic`.
- Save the best model according to the AIC as `best_model`.

`@hint`
- Use `expand.grid()` to produce `grid`. 
- Use double brackets (`[[]]`) if you work with a list.

`@pre_exercise_code`
```{r}
library(forecast)
library(quantmod)
library(fGarch)
apple <- getSymbols('AAPL', auto.assign = F)$AAPL.Close
log_returns <- window(diff(log(apple)), start = "2015-01-01")
train <- ts(window(log_returns, end = "2018-12-31"))
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
  garch_models[[i]] <- garchFit(garch_formula, data = train, include.mean = FALSE, trace = FALSE)
  aic[i] <- garch_models[[i]]@fit$ics[1]
}

# Select the GARCH model with the lowest AIC
best_model <- garch_models[[which.min(aic)]]

```

`@sct`
```{r}
ex() %>% check_object("grid") %>% check_equal()
ex() %>% check_function("expand.grid") 
ex() %>% check_object("garch_models") %>% check_equal()
ex() %>% check_object("aic") %>% check_equal()
ex() %>% check_object("best_model") %>% check_equal()
success_msg("Correct!")
```

---

## Diagnostic Checking

```yaml
type: NormalExercise
key: cfd91968ef
xp: 100
```

As we did for ARMA models we run some model diagnostics to check whether our estimated model is a good approximation of the real process. 

Specifically, we want to check if the standardized residuals ($\hat{\epsilon}_ t = y _t / \hat{\sigma} _t$) as well as the squared standardized residuals ($\hat{\epsilon}_ t^2$)  roughly look like white noise (use `garch_model@sigma.t` to extract the $\hat{\sigma}$ s from a GARCH model object). If that wouldn't be the case there would be some dependence left our model did not capture. 

We can check this by taking a look at the ACF or by performing e.g. a Ljung-Box test. 

If you have a garch model object `garch_model`, then you can run `summary(garch_model)` which will automatically print out p-values
for Ljung-Box tests on $\hat{\epsilon}_ t$ and $\hat{\epsilon}_ t^2$ for different included lag numbers. 

This is an example:  

|   |  | | Statistic | p-Value |
|---|------|------|------|------|
| Ljung-Box Test  | R | Q(10) | 10.6873  |  0.3823935|
| Ljung-Box Test  | R^2 | Q(15) |13.5374 | 0.5608628 |


The first test is performed on $\hat{\epsilon}_ t$ (indicated by only `R`), with 10 lags included (indicated by `Q(10)`).
The second test is on $\hat{\epsilon}_ t^2$ (indicated by `R^2`), with 15 lags included (indicated by `Q(15)`).

In general we are satisfied if all performed test would not reject.

`@instructions`
- Compute and plot the standardized residuals
- Estimate the ACF of the standardized residuals
- Estimate the ACF of the squared standardized residuals
- Check the results of the Ljung-Box test

`@hint`
- The $\hat{\sigma} _t$ s needed to compute the standardized residuals can be extracted from a GARCH model object.

`@pre_exercise_code`
```{r}
library(forecast)
library(quantmod)
library(fGarch)
apple <- getSymbols('AAPL', auto.assign = F)$AAPL.Close
log_returns <- window(diff(log(apple)), start = "2015-01-01")
train <- ts(window(log_returns, end = "2018-12-31"))
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
ex() %>% check_object("e") %>% check_equal()
ex() %>% check_function("autoplot") %>% check_arg("object") %>% check_equal
ex() %>% check_function("ggAcf", index = 1) %>% check_arg("x") %>% check_equal()
ex() %>% check_function("ggAcf", index = 2) %>% check_arg("x") %>% check_equal()
ex() %>% check_function("summary") %>% check_arg("object") %>% check_equal
success_msg("Correct!")
```

---

## Forecasting

```yaml
type: NormalExercise
key: bd56a9007f
xp: 100
```

Since we are interested in the volatility of a process when using GARCH models we also want to forecast volatility. 

If we already have estimated a GARCH model based on data up to time $T$. then the h-step ahead volatility forecast  $\widehat{ \sigma }_ {T+h}$ can be computed with the function `predict()` like this: 
```
predict(garch_model, h)$standardDeviation
```  

Here we want to produce a series of 1-step ahead volatility forecasts for the data in the test set using a rolling window.
For this we fit a model for each forecasting step to a subsample of the data.
If the training data set consists of the observations $\\{y_ 1, \ldots, y_ T\\}$ and the test data set consists of $\\{y_ {T+1}, \ldots, y_ {T+n}\\}$ then we would proceed as follows: 

1. Fit a model to ${y_ {T-m}, \ldots, y_ T}$. Use this model to forecast $y_ {T+1}$.
2. Fit a model to ${y_ {T-m + 1}, \ldots, y_ {T + 1}}$. Use this model to forecast $y_ {T+2}$.

&emsp;&ensp;   Continue until you reach 

&emsp;&ensp; n. Fit a model to ${y_ {T-m + n - 1}, \ldots, y_ {T + n - 1}}$ and use this model to forecast $y_ {T+n}$.

Note that we fit each model on exactly $m$ observations. Therefore $m$ is called the window size.

`@instructions`
- Create an empty numeric vector having the length of the test data (`test`) for the volatility forecasts called `sigma_forecast`.
- Use a rolling window approach as described above. Set the window size equal to the length of the training dataset. This way you should get for each observation in the test dataset a volatility forecast.

`@hint`


`@pre_exercise_code`
```{r}
library(forecast)
library(quantmod)
library(fGarch)
apple <- getSymbols('AAPL', auto.assign = F)$AAPL.Close
log_returns <- window(diff(log(apple)), start = "2015-01-01")
train <- ts(window(log_returns, end = "2018-12-31"))
test <- ts(window(log_returns, start = "2019-01-01"))
```

`@sample_code`
```{r}
# Create an empty vector `forecast_sigma`


# Write a for() loop forecast the sigmas using a rolling window approach





```

`@solution`
```{r}
# Create an empty vector `sigma_forecast`
sigma_forecast <- numeric(length(test))

# Write a for-loop to forecast the sigmas using a rolling window
for(i in seq_along(test)){
  garch11 <- garchFit(formula = ~garch(1, 1), data = log_returns[(1 + i) : (length(train) + i - 1)],
                      include.mean = FALSE, trace = FALSE)
  sigma_forecast[i] <- predict(garch11, 1)$standardDeviation
}

```

`@sct`
```{r}
ex() %>% check_object("sigma_forecast") %>% check_equal()
success_msg("Correct!")
```

---

## Conditional Value at Risk

```yaml
type: NormalExercise
key: 49bd4f0b54
xp: 100
```

Using the assumption $\epsilon_ t \sim N(0,1)$ we can use the forecasted volatilities from the previous exercise to forecast the conditional Value at Risk (CVaR). 
The CVaR forecast for $T+1$ at level $\alpha$ can be computed as the $\alpha$-quantile of a normal distribution with expectation $0$ and variance $\hat{ \sigma}^2_ {T+1}$.

`@instructions`
- Predict for the test data the one-step-ahead CVaR at level $\alpha = 0.05$. Save the result as an object of class `ts()` called `CVaR`. 
- Plot the test data together with the CVaR.

`@hint`
- Use `qnorm()` to compute quantiles for the normal distribution.

`@pre_exercise_code`
```{r}
library(forecast)
library(quantmod)
library(fGarch)
apple <- getSymbols('AAPL', auto.assign = F)$AAPL.Close
log_returns <- window(diff(log(apple)), start = "2015-01-01")
train <- ts(window(log_returns, end = "2018-12-31"))
test <- ts(window(log_returns, start = "2019-01-01"))
sigma_forecast <- numeric()
for(h in seq_along(test)){
  garch11 <- garchFit(formula = ~garch(1, 1), data = log_returns[(1 + h) : (length(train) + h - 1)], 
                          cond.dist = "norm", include.mean = FALSE, trace = FALSE)
  sigma_forecast[h] <- predict(garch11, 1)$standardDeviation
}
```

`@sample_code`
```{r}
# Forecast the CVaRs based on the forecasted sigmas


# Plot the test series together with the forecasted CVaRs


```

`@solution`
```{r}
# Forecast the CVaRs based on the forecasted sigmas
CVaR <- ts(sigma_forecast * qnorm(0.05))

# Plot the test series together with the forecasted CVaRs
autoplot(test) + autolayer(CVaR)

```

`@sct`
```{r}
ex() %>% check_object("CVaR") %>% check_equal()
sol_alt <- 'CVaR <- ts(forecasts_sigma * qnorm(0.05)); autoplot(CVaR) + autolayer(test)'

ex() %>% check_or(
   check_function(.,"autoplot") %>% check_arg("object") %>% check_equal(),
   override_solution(.,sol_alt) %>% 
   check_function("autoplot") %>% check_arg("object") %>% check_equal()
)

ex() %>% check_or(
   check_function(.,"autolayer") %>% check_arg("object") %>% check_equal(),
   override_solution(.,sol_alt) %>% 
   check_function("autolayer") %>% check_arg("object") %>% check_equal()
)
success_msg("Correct!")
```
