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
Compare this to  chapter 3 where we chose a model based on its in-sample fit. 

In order to evaluate our forecast of future values we would have to wait until those were observed. 
In practice we therefore wind back and pretend that only data until a specific date in the past are available. 
Then the remaining data can be used to evaluate the forecast. 

We call all values up to this specific date the training data because we use those to "train" (i.e. estimate) our model.
All later observed data form the test set because we use those to test the forecasting performance. 

`@instructions`
- Create a training data set containing all observations of `con_supply2010` until the end of `2017` and assign it to the variable `train`
- Create a test data set containing the remaining data and assign it to the variable `test`

`@hint`


`@pre_exercise_code`
```{r}
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
