---
title: Introduction to Time Series
description: This is a template chapter.
---

## Discover

```yaml
type: NormalExercise
lang: r
xp: 100
skills: 1
key: cc46134a89
```

The `discoveries` data set contains the numbers of great inventions and scientific discoveries of each year from 1860 to 1959.
It is also one of the many data sets which are available immediately after the installation of `R`.
Type `data()` in the R console on your computer to find others (this does not work in DataCamp). 

Your task is to conduct a rudimentary analysis of this data set. Through this you will learn some of the basics of working with time series in `R`. 

`@instructions`
- Print out the time series `discoveries` and examine the output. 
- Find out of how many observations this time series consists.  Use `length()` to achieve this. 
- Print out the first 6 and the last 7 observations. Use `head()` and `tail()` for this. 
- Visualize the time series using the `plot()` function.  
`@hint`
- You can print something by either using to function `print()` or by just typing the name of the object you want to print. 
- You can always use the `R` `help()` function. For example,`help(head)` or `?head`. 
- When working with time series data it is enough to call `plot()` with the time series object as its only argument to generate a decent plot.  


`@pre_exercise_code`

```{r}

```

`@sample_code`

```{r}
# 1. Print the time series

# 2. Compute the length of the time series

# 3. Printe the first 6 and the last 7 observations 


# 4. Generate a plot
```

`@solution`

```{r}
# 1. Print the time series
print(discoveries)
# 2. Compute the length of the time series
length(discoveries)
# 3. Save the first 6 and the last 7 observations 
head(discoveries)
tail(discoveries, n = 7)
# 4. Generate a plot
plot(discoveries)
```

`@sct`

```{r}
ex() %>% check_output_expr("print(discoveries)", missing_msg  = "Did you print out the time series?")
ex() %>% check_function("length") %>%  check_result() %>% check_equal()
ex() %>% check_function("head") %>%  check_result() %>% check_equal()
ex() %>% check_function("tail") %>%  check_result() %>% check_equal()
ex() %>% check_function("plot") %>% check_arg("x") %>% check_equal()
success_msg("Great Work!")
```

---
## Create a time series

```yaml
type: NormalExercise
key: ff20abba1c
lang: r
xp: 100
skills: 1
```
In the previous excercise a proper time series was already provided. 
Mostly this will not be the case.
Assume we observed for each day of the week how many students were in the university canteen.


| Mon. 	| Tue. 	| We. 	| Th. 	| Fri. 	|
|------	|------	|------	|------	|------	|
| 2500 	| 4000 	| 4500 	| 4250 	| 500 	|

`@instructions`
- Create a simple vector called `vec` containing the numbers from the table. 
- Use the function `ts()` to create a time series object from `vec` and assign the result to `ts_vec`.
- Find out to what kind of class `vec` and `ts_vec` belong to. Use `class()` for this task. 

`@hint`
You can use`c()` to **c**ombine values to a vector. 

`@pre_exercise_code`
```{r}

```

`@sample_code`
```{r}
# Create vec

# Create ts_vec

# Print out the class of vec and ts_vec
```

`@solution`
```{r}
# Create vec
vec    <- c(2500, 4000, 4500, 4250, 500)

# Create ts_vec
ts_vec <- ts(vec)

# Print out the class of vec and ts_vec
class(vec)
class(ts_vec)
```

`@sct`
```{r}
ex() %>% check_object("vec") %>% check_equal()
ex() %>% check_function("class", index = 1) %>% check_arg("x") %>% check_equal()
ex() %>% check_function("class", index = 2) %>% check_arg("x") %>% check_equal()
success_msg("Great Work!")
```

---
## Why using class ts?

```yaml
type: NormalExercise
key: 9e22e2e0c7
lang: r
xp: 100
skills: 1
```
In R each object has a class. Along with the now introduced class `ts` 
you might already know the classes `vector`, `matrix`, `list` 
and `data.frame` from the introduction course. 

Everything you can do with a `ts` object can also be done with a normal vector.
So it is not mandatory to work with `ts` objects for time series analysis in R. 
However, it often makes life easier if we do so. The reason is that several 
functions such as plot behave differently depending on the class of their inputs.

`@instructions`
- Plot `vec` and `ts_vec` one below the other. The `par()` function in the sample code
   will achieve this. Simply put the code for the two plots below. Note the difference. 
`@hint`
- You can increase the size of the plot window to get a better view on the plots you created. 
`@pre_exercise_code`
```{r}

```

`@sample_code`
```{r}
# Generate the time series 
vec    <- c(2500, 4000, 4500, 4250, 500)
ts_vec <- ts(vec)

# Plot both objects
par(mfrow = c(2,1))
```

`@solution`
```{r}
# Generate the time series 
vec    <- c(2500, 4000, 4500, 4250, 500)
ts_vec <- ts(vec)

# Plot both objects
par(mfrow = c(2,1))
plot(vec)
plot(ts_vec)
```

`@sct`
```{r}
ex() %>% check_function("plot", index = 1) %>% check_arg("x") %>% check_equal()
ex() %>% check_function("plot", index = 2) %>% check_arg("x") %>% check_equal()
success_msg("Great Work!")
```

---
## Quarterly Data

```yaml
type: NormalExercise
key: 442bf6e1e7
lang: r
xp: 100
skills: 1
```
You already know some basics about time series objects of class `ts`. Now, we
want to create a time series for which we have to define more than just one argument 
of the function `ts()`.

We want to create a quarterly time series which starts in the 3. quarter of the year
$2000$. This can be done by calling `ts()` with the additional arguments `start` and `frequency`. 
`start` will tell the time series when to start. `frequency` defines how many observations 
occur per unit of time (in our case per year). 

`@instructions`
  -  Create a time series which starts in the 3. quarter of the year 2000 and assign it to `x_ts`. Use the observations contained in the variable `x`.
  -  Print out the time series to the console and produce a simple plot.

`@hint`
If we for example want the series to start
in the 4. quarter 2016 than we would have to type `ts(x, start = c(2016, 4), frequency = 4)`.
We set frequency to `4` because a year consists of `4` quarters. For monthly data we would have
`frequency = 12` and so on. 


`@pre_exercise_code`
```{r}

```

`@sample_code`
```{r}
# Generating sample data
set.seed(123)
x <- sample(0:100, size = 50, replace = TRUE) 
# Create the time series object

# Print the time series 

# Plot the time series 
```

`@solution`
```{r}
# Generating sample data
set.seed(123)
x <- sample(0:100, size = 50, replace = TRUE)  
# Create the time series object
x_ts <- ts(x, start = c(2000, 4), frequency = 4)
# Print the time series 
x_ts
# Plot the time series
plot(x_ts)
```

`@sct`
```{r}
ex() %>% check_object("x_ts") %>% check_equal()
ex() %>% check_output_expr("x_ts")
success_msg("Great Work!")
```

---
## Subsetting a Time Series

```yaml
type: NormalExercise
key: bd53c02b37
lang: r
xp: 100
skills: 1
```
The `AirPassanger` data set contains the number of air passangers in the first years of commercial aviation. 

We are only interested in the first $4$ years. A convinient way of subsetting a
time series is the function `window()`. We provide the original time series as well as
the start and the end date of the desired subset 

One advantage of `window()` is that in contrast to other subsetting techniques an object of class `ts` is returned.  


`@instructions`
- Plot the time series `AirPassangers`. This object is already available in your enviornment. 
- When does the time series start and when does it end? What is the underlying frequency? Use `start()`, `end()` and `frequency()` to find it out. 
- Create a new object called `AP_begin` with only the first 4 years of the original time series. Print out `AP_begin`! 
- Create a new object called `AP_rest` with the remaining years. Print out `AP_rest`!
`@hint`
Use for monthly data the following syntax `window(TimeSeries, start = c(year, month), end = c(year, month) )`.

`@pre_exercise_code`
```{r}

```

`@sample_code`
```{r}
# Plot the AirPassanger time series

# Find out the start and the end date 


# Determine the frequnecy

# Create and print out a vector containing only the first 4 years


# Create and print out a vector containing the remaining series


```

`@solution`
```{r}
# Plot the AirPassanger time series
plot(AirPassengers)
# Find out the start and the end date 
start(AirPassengers)
end(AirPassengers)
# Determine the frequnecy
frequency(AirPassengers)
# Create and print out a vector containing only the first 4 years
AP_begin<- window(AirPassengers, start =  start(AirPassengers), end = c(1952, 12))
AP_begin
# Create and print out a vector containing the remaining series
AP_rest <- window(AirPassengers, start = c(1953, 1), end =  end(AirPassengers))
AP_rest
```

`@sct`
```{r}
ex() %>% check_function("plot") %>% check_arg("x") %>% check_equal()
ex() %>% check_function("start") %>% check_arg("x") %>% check_equal()
ex() %>% check_function("end") %>% check_arg("x") %>% check_equal()
ex() %>% check_function("frequency") %>% check_arg("x") %>% check_equal()
ex() %>% check_function("window", index = 1) %>% check_result() %>% check_equal()
ex() %>% check_function("window", index = 2) %>% check_result() %>% check_equal()
ex() %>% check_object("AP_begin")  %>% check_equal()
ex() %>% check_object("AP_rest")  %>% check_equal()
ex() %>% check_output_expr("AP_begin")
ex() %>% check_output_expr("AP_rest")
success_msg("Great Work!")
```

---
## Sequence of random variables 

```yaml
type: MultipleChoiceExercise
key: ae9260bd7c
lang: r
xp: 50
skills: 1
```
How do we call an ordered sequence of random variables (rv) which are defined on the same probability space?  

`@instructions`
- random walk
- AR-process
- (discrete) stochastic process
- martingale 
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
## Time Series

```yaml
type: MultipleChoiceExercise
key: 6b9cf0fb93
lang: r
xp: 50
skills: 1
```
How can a time series be defined?

`@instructions`
- As a (discrete) stochastic process. 
- As a realization of a (discrete) stochastic process. 
- As a sequence of variables.
- As a process in continues time.

`@hint`

`@pre_exercise_code`
```{r}

```

`@sct`
```{r}
test_mc(correct = 2)
success_msg("Great Work!")
```

---
## (Weak) Stationarity I

```yaml
type: MultipleChoiceExercise
key: 85b7fcbddf
lang: r
xp: 50
skills: 1
```
We say a time series process is weakly stationary if: 

`@instructions`
- The mean is constant over time.
- The autocovariances do not depend on $t$.
- The process stays within a certain interval.
- The first two moments do not change over time.

`@hint`

`@pre_exercise_code`
```{r}

```

`@sct`
```{r}
test_mc(correct = 4)
success_msg("Great Work!")
```


---
## (Weak) Stationarity II

```yaml
type: MultipleChoiceExercise
key: a3b54f025a
lang: r
xp: 50
skills: 1
```
Consider the process from the previous exercise
$$Y_t = 4 + \epsilon _t \text{, } \,\,\,\,\,\, \epsilon _t \sim N(0,t)$$

Is $\{Y_t\}$ stationary?


`@instructions`
- Yes, because the mean does not change over time.
- No, because the mean changes over time. 
- No, because the second moments converge to $0$.
- No, because $\sigma _t \neq \sigma _{t+1}$.
- Yes, because the process is ergodic.
`@hint`

`@pre_exercise_code`
```{r}
set.seed(123)
t <- 1:100
y <- 4 + rnorm(100, sd = t)
plot(y ~ t, type = "l")
```

`@sct`
```{r}
test_mc(correct = 4)
success_msg("Great Work!")
```
---
## Ergodicity

```yaml
type: MultipleChoiceExercise
key: 997640b9d6
lang: r
xp: 100
skills: 1
```
The time series process $\\{Y_t\\}$ is ergodic if 

$$\overline{Y}_T =\frac{1}{T} \sum^T _{t = 1} y _t$$

`@instructions`
- converges to the sample mean.
- converges to $\mu$ as $T \rightarrow 0$.
- converges to $0$ as $T \rightarrow \infty$.
- converges to $E[Y_t]$ as $T \rightarrow \infty$.
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
test_mc(correct = 4)
success_msg("Great Work!")
```
