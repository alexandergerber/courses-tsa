---
title: 'Arma Models'
description: 'Insert the chapter description here'
---

## Simulate from MA(q) processes

```yaml
type: NormalExercise
key: 99a66ed4c2
lang: r
xp: 100
skills: 1
```

In the lecture you learnt about Moving Average (MA) processes of the form 

$$Y _t = \mu + \sum _{j = 0} ^q \theta _j \epsilon _{t-j}.$$

The function `arima.sim()` can be used to simulate time series from an MA process. 
In order to generate 100 data points from the $MA(2)$ process 

$$Y _t = 2 + \epsilon _{t} + 0.6 \epsilon _{t-1} - 1.5 \epsilon _{t-2} $$

we can use: `arima.sim(model = list(ma = c(0.6, -1.5)), mean = 2, n = 100)`.

`@instructions`
- Generate 1000 obs. from a Gausian White Noise process. Use `arima.sim()` for this and assign the result to the variable `wn`.
- Generate 1000 obs. form the process $$Y _t = 7 + \epsilon _{t} - 1.5 \epsilon _{t-1} $$ and call them `ma1`.
- Generate 1000 obs. from the process $$Y _t = \epsilon _{t} + \epsilon _{t-1} + \epsilon _{t-2} + \epsilon _{t-3}$$ and call them `ma3`.
- Plot `wn`, `ma_1` and `ma_5` and examine the characteristics of the time series.

`@hint`
- To simulate a White Noise process you can pass an empty list (`list()` produces an empty list) to the `model` argument  .

`@pre_exercise_code`
```{r}

```

`@sample_code`
```{r}
#Set the seed
set.seed(123)
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

---

## Variance of WN and MA process

```yaml
type: MultipleChoiceExercise
key: 762e8728f1
lang: r
xp: 50
skills: 1
```

What can you say about the variances of the processes $Y _t$ and $Z _t$? 

$$ Y _t = \epsilon _t, \qquad \epsilon _t \sim (0, \sigma^2) $$
$$ Z _t = \epsilon _t + 0.9 \epsilon _{t-1},  \qquad \epsilon _t \sim (0, \sigma^2) $$

`@possible_answers`
- The variance of $Y_t$ is larger because a white noise process is more wiggly than an $MA$ process.
- The variance of $Y_t$ is larger because $Z _t$ is a smoothed version of $Y _t$.
- The variance of $Z _t$ is larger because $$Var(Z _t) = \sigma^2 (1 + \theta^2) $$.

`@hint`


`@pre_exercise_code`
```{r}

```

`@sct`
```{r}
test_mc(correct = 3)
success_msg("Great Work!")
```

---

## Simulate form an AR(p) processes

```yaml
type: NormalExercise
key: 6599cb6b79
lang: r
xp: 100
skills: 1
```

Now we want to use `arima.sim()` to simulate a time series from an $AR$ process. 
Consider the process:
$$Y_t = 0.4 Y _{t-1} + 0.2 Y _{t-2} + \epsilon _t.$$

We can simulate one possible path of this process with 100 observation by typing
`arima.sim(ar = c(0.4, 0.2), n = 100)`.

`@instructions`
- Set the seed to 123
- Simulate 1000 observations of the process $$Y_ t = 0.9 Y_ {t-1} + \epsilon_ t$$ and save the result to the variable `ar_1`. 
- Compare it to the realizations of the White Noise and the $MA(5)$ process from the previous question by plotting the 3 time series.

`@hint`
Check the help page for more information.

`@pre_exercise_code`
```{r}

```

`@sample_code`
```{r}
#Set the seed
set.seed(123)
#Simulate the time series
wn    <- arima.sim(model = list(), n = 1000 )
ma_5  <- arima.sim(model = list(ma = c(1,1,1,1,1)), n = 1000 )
#Plot all three time series
par(mfrow = c(3,1))
plot(wn)
plot(ma_1)
```

`@solution`
```{r}
#Set the seed
set.seed(123)
#Simulate the time series
wn    <- arima.sim(model = list(), n = 1000 )
ma_5  <- arima.sim(model = list(ma = c(1,1,1,1,1)), n = 1000 )
ar_1  <- arima.sim(model = list(ar = 0.9), n = 1000)
#Plot all three time series
par(mfrow = c(3,1))
plot(wn)
plot(ma_5)
plot(ar_1)
```

`@sct`
```{r}
ex() %>% check_object("ar_1") %>% check_equal()
ex() %>% check_function("plot") %>% check_arg("x") %>% check_equal()
success_msg("Great Work!")
```

---

## Simulate from an ARMA(p,q) process

```yaml
type: NormalExercise
key: 036a7f8256
lang: r
xp: 100
skills: 1
```

To generate a time series from an $ARMA(p,q)$ process we pass both
$AR$ and $MA$ coefficients to `arima.sim()`.

To simulate 200 observations from the process

$$Y _t = 2.7 + 0.5 Y _{t-1} +  0.2 Y _{t-2} + 0.7 \epsilon _{t-1} + \epsilon _t$$

we can type: `arima.sim(model = list(ar = c(0.5, 0.2), ma = 0.7), mean = 2.7, n = 200)`.

When using `arima.sim()` to simulate from a $MA(q)$ or White Noise process the argument mean corresponds to the expectation of the process $\mu$.
In case you want to simulate from an $AR(p)$ or an $ARMA(p,q)$ process `mean` corresponds to the constant $c \neq \mu$.

`@instructions`
- Set the seed to 123
- Simulate 1000 observations of a Gaussian White Noise process and assign the result to `wn`.   
- Simulate one trajectory consisting of 1000 observations of $$Y _t = 4 + 0.7 Y _{t-1} + 0.4 \epsilon _{t-1} + \epsilon _{t}$$ and assign the result to `y`.
- Plot both time series.

`@hint`
Check the help page for more information.

`@pre_exercise_code`
```{r}

```

`@sample_code`
```{r}
#set the seen 

#simulate from gaussian white noise process

#simulate from arma(1,1) process

#plot wn and y 
par(mfrow = c(2,1))

```

`@solution`
```{r}
#set the seed 
set.seed(123)
#simulate from gaussian white noise process
wn  <- arima.sim(model = list(), n = 1000)
#simulate from arma(1,1) process
y <- arima.sim(model = list(ar = 0.7, ma = 0.4), mean = 4, n = 1000)
#plot wn and y 
par(mfrow = c(2,1))
plot(wn)
plot(y)
```

`@sct`
```{r}
ex() %>% check_function("set.seed") %>% check_arg("seed") %>% check_equal()
ex() %>% check_object("wn") %>% check_equal()
ex() %>% check_object("y") %>% check_equal()
ex() %>% check_function("plot", index = 1) %>% check_arg("x") %>% check_equal()
ex() %>% check_function("plot", index = 2) %>% check_arg("x") %>% check_equal()
success_msg("Great Work!")
```

---

## Autocorrelation Function

```yaml
type: NormalExercise
key: 851539fe9d
lang: r
xp: 100
skills: 1
```

The autocorrelation functions of White Noise, $MA$ and $AR$ processes exhibit distinct patterns which can help you to identify the best suited Model.

`@instructions`
Use the functio `acf` to plot the autocorrelation function of all 4 time series.

`@hint`
If the 4 plots do not fit on your display or are too small you can copy and paste the code into the R console on your computer.

`@pre_exercise_code`
```{r}

```

`@sample_code`
```{r}
#Generate time series 
wn   <-  arima.sim(model = list(), n = 10000)
ma   <-  arima.sim(model = list(ma = 0.4), n = 10000)
ar   <-  arima.sim(model = list(ar = 0.7), n = 10000)
arma <-  arima.sim(model = list(ar = 0.7, ma = 0.4), n = 10000)
#Plot autocorrelation functions
par(mfrow = c(4,1))
```

`@solution`
```{r}
#Generate time series 
wn   <-  arima.sim(model = list(), n = 10000)
ma   <-  arima.sim(model = list(ma = 0.4), n = 10000)
ar   <-  arima.sim(model = list(ar = 0.7), n = 10000)
arma <-  arima.sim(model = list(ar = 0.7, ma = 0.4), n = 10000)
#Plot autocorrelation functions
par(mfrow = c(4,1))
acf(wn)
acf(ma)
acf(ar)
acf(arma)
```

`@sct`
```{r}
ex() %>% check_function("acf", index = 1) %>% check_arg("x") %>% check_equal()
ex() %>% check_function("acf", index = 2) %>% check_arg("x") %>% check_equal()
ex() %>% check_function("acf", index = 3) %>% check_arg("x") %>% check_equal()
ex() %>% check_function("acf", index = 4) %>% check_arg("x") %>% check_equal()
success_msg("Great Work!")
```

---

## Which ACF belongs to which process?

```yaml
type: MultipleChoiceExercise
key: 0e6caf3c01
lang: r
xp: 50
skills: 1
```

On the right you can find plots of 3 autocorrelation functions. 
Decide which process belongs to which ACF.

`@possible_answers`
- **a** was simulated from a White Noise process, **b** from an $AR$ process and **c** from a $MA$ process. 
- **a** was simulated from an $AR$ process and **b** and **c** from a $MA$ process. 
- **a** was simulated from an $AR$ process, **b** from an White Woise process and **c** from a $MA$ process.

`@hint`


`@pre_exercise_code`
```{r}
set.seed(1)
wn <- arima.sim(model = list(), n = 1000)
ar <- arima.sim(model = list(ar = 0.9), n = 1000)
ma <- arima.sim(model = list(ma = 1), n = 1000)

par(mfrow = c(3,1))
acf(ar, main = "a")
acf(wn, main = "b")
acf(ma, main = "c")
```

`@sct`
```{r}
test_mc(correct = 1)
success_msg("Great Work!")
```

---

## Which processes are stationary?

```yaml
type: MultipleChoiceExercise
key: 2c86e47140
lang: r
xp: 50
skills: 1
```

Consider the following processes. 

<ol>
  <li>$ y _t = 1.4 y _{t-1} + \epsilon _t$</li>
  <li>$ y _t = 0.4 y _{t-1} + \epsilon _t$</li>
  <li>$ y _t = 0.4 y _{t-1} + 0.6 y _{t-2} + \epsilon _t$</li>
  <li>$ y _t = y _{t-1} + \epsilon _t$</li>
  <li>$ y _t = \epsilon _t + 0.4 \epsilon _{t-1}$</li>
  <li>$ y _t = \epsilon _t + 0.4 \epsilon _{t-1} + 0.6 \epsilon _{t-2}$</li>
</ol>

`@possible_answers`
- None of the processes is stationary.
- All processes are stationary. 
- The processes $2$, $5$ and $6$ are stationary.
- The processes $1$, $3$ and $4$ are stationary.
- The processes $2$ and $4$ are stationary.

`@hint`


`@pre_exercise_code`
```{r}

```

`@sct`
```{r}
test_mc(correct = 3)
success_msg("Great Work!")
```

---

## An AR(2) process with complex roots

```yaml
type: NormalExercise
key: c83db424fe
lang: r
xp: 100
skills: 1
```

Check whether the process 
$$y _t = 1.5 y _{t-1} - .75 y _{t-2} + \epsilon _t $$ 
is stationary. 
You can use the function `polyroot()` to compute the roots of the characteristic polynomial.

`@instructions`
- Assign the coefficients of the characteristic polynomial to the vector `z`.
- Pass `z` as an argument to `polyroot()` and assign the result to the variable `roots`. 
- Compute the absolute value of the roots (`Mod()` can do this for real as well as complex numbers) and check if they lie outside the unit circle.

`@hint`
How does the characteristic polynomial looks like? Double check if you used the correct signs.

`@pre_exercise_code`
```{r}

```

`@sample_code`
```{r}
#Create vector z containing the coefficients of the characteristic polynomial


#Compute the roots of the characteristic polynomial 


#Compute and print out the absolute value of the roots

```

`@solution`
```{r}
#Create vector z containing the coefficients of the characteristic polynomial
z <- c(1, -1.5, 0.75)

#Compute the roots of the characteristic polynomial 
roots <- polyroot(z)

#Compute and print out the absolute value of the roots
Mod(roots) 

```

`@sct`
```{r}
ex() %>% check_object("z") %>% check_equal()
ex() %>% check_object("roots") %>% check_equal()
ex() %>% check_output_expr("Mod(roots)")
success_msg("Great Work!")
```
