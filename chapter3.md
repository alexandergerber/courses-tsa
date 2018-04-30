---
title       : Arma Models
description : Insert the chapter description here

---
## Simulate from a MA(q) processes

```yaml
type: NormalExercise
key: 99a66ed4c2
lang: r
xp: 100
skills: 1
```
In the lecture you learnt about Moving Average (MA) processes of the form 

$$Y _t = \mu + \sum _{j = 0} ^q \theta _j \epsilon _{t-j}.$$

The function `arima.sim()` can be used to simulate time series from an underlying MA process. 
In order to generate 100 data points from the $MA(2)$ process 

$$Y _t = 2 + \epsilon _{t} + 0.6 \epsilon _{t-1} - 1.5 \epsilon _{t-2} $$

we would type: `arima.sim(model = list(ma = c(0.6, -1.5)), mean = 2, n = 100)`.

Set the seed to 123.


`@instructions`
- Generate 1000 obs. from a Gausian White Noise process. Use `arima.sim()` for this and assign the result to the variable `wn`.
- Generate 1000 obs. form the process $$Y _t = 7 + \epsilon _{t} - 1.5 \epsilon _{t-1} $$ and call them `ma1`.
- Generate 1000 obs. from the process $$Y _t = \epsilon _{t} + \epsilon _{t-1} + \epsilon _{t-2} + \epsilon _{t-3}$$ and call them `ma3`.
- Plot `wn`, `ma_1` and `ma_5` and examine the characteristics of the time series.  
`@hint`
- To simulate a White Noise process you can pass an empty list (`list()` produces an empty list) to the `model` argument  .


`@pre_exercise_code`

`@sample_code`
```{r}
#Set the seed

#Simulate the time series

#Plot all three time series
par(mfrow = c(3,1))
```

`@solution`
```{r}
#Set the seed
set.seed(123)
#Simulate the time series
wn    <- arima.sim(model = list(), n = 1000 )
ma_1  <- arima.sim(model = list(ma = c(1)), n = 1000 )
ma_3  <- arima.sim(model = list(ma = c(1,1,1) ), n = 1000 )
#Plot all three time series
par(mfrow = c(3,1))
plot(wn)
plot(ma_1)
plot(ma_3)
```


`@sct`
```{r}
ex() %>% check_object("wn") %>% check_equal()
ex() %>% check_object("ma_1") %>% check_equal()
ex() %>% check_object("ma_3") %>% check_equal()
ex() %>% check_function('plot', index = 1) %>% check_arg("x") %>% check_equal()
ex() %>% check_function('plot', index = 2) %>% check_arg("x") %>% check_equal()
ex() %>% check_function('plot', index = 3) %>% check_arg("x") %>% check_equal()
success_msg("Great Work!")
```
