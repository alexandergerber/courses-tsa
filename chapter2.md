---
title       : Seasonality
description : Insert the chapter description here
---
## The AirPassanger time series

```yaml
type: MultipleChoiceExercise
key: 536201f151
lang: r
xp: 100
skills: 1
```
With your knowledge about stationarity and how a stationary process should look like reconsider the `AirPassanger` time series. 
Which of the following statements are true?


<ol>
  <li>Looks like a random walk.</li>
  <li>The time series has a time trend. </li>
  <li>It could be white noise. </li>
  <li>The time series exhibits a seasonal pattern. </li>
  <li>The seasonal pattern gets stronger with increasing $t$.</li>
  <li>The underlying process seems to be perfectly stationary.</li>
</ol>

You need to find all true statements. 

`@instructions`
- The statements 1, 3, 6 are true. 
- The statements 2 and 5 are true.
- Only statements 6 is true.
- The statements 2, 4, and 5 are true.  
- All statements but 6 are true. 

`@hint`

`@pre_exercise_code`
```{r}
plot(AirPassengers)
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

---
## Trend and Seasonality I

```yaml
type: NormalExercise
key: bff8b8fa63
lang: r
xp: 100
skills: 1
```
We found out that the `AirPassanger` time series is non-stationary because it exhibits a deterministic trend and seasonal pattern.

The function `decompose()` can be used to decompose a time series according to the model: 

$$Y _t = s _t + m _t + X _t,$$

where $s _t$ is the seasonal component, $m _t$ is the trend component and $X _t$ is the random component. 
`decompose()` uses moving average filters to estimate $s _t$ and $m _t$. 

In order to use `decompose()` you need an object of type `ts` with `frequency > 1`. When working with real data
make sure to check this condition. 

`@instructions`
- Apply `decompose()` to `AirPassangers` and assign the results to `AP_decomp`.
- Plot `AP_decomp` and inspect the output. 
- Extract only the seasonal component form `AP_decomp` and save it as `AP_seasonal `. `?decompose` is helpful here. Look for the caption "Value". This section describes the output object of a function.  
`@hint`
Use the `$` sign get access to the seasonal component.

`@pre_exercise_code`
```{r}

```

`@sample_code`
```{r}
#Decompose the AirPassangers time series


#Visualize the decomposed time series


#Extract the seasonal component


#Plot only the seasonal component

```

`@solution`
```{r}
#Decompose the AirPassangers time series
AP_decomp <- decompose(AirPassengers)

#Visualize the decomposed time series
plot(AP_decomp)

#Extract the seasonal component
AP_seasonal <- AP_decomp$seasonal

#Plot only the seasonal component
plot(AP_seasonal)
```

`@sct`
```{r}
ex() %>% check_object("AP_decomp") %>% check_equal()
ex() %>% check_function("plot", index = 1) %>% check_arg("x") %>% check_equal()
ex() %>% check_object("AP_seasonal") %>% check_equal()
ex() %>% check_function("plot", index = 2) %>% check_arg("x") %>% check_equal()
success_msg("Great Work!")
```

---
## Trend and Seasonality II

```yaml
type: MultipleChoiceExercise
key: 8b84fb632b
lang: r
xp: 50
skills: 1
```
Reconsider the seasonal decompositon provided by `decompose()`. 
What do you think about the result?


`@instructions`

- It looks good. The random component moves arround $0$. We can start working with ARMA models. 
- The random model still has a distinct pattern and does not appear to be stationary. We have to reconsider our approach.  

`@hint`

`@pre_exercise_code`
```{r}
#Decompose the AirPassangers time series
AP_decomp <- decompose(AirPassengers)

#Visualize the decomposed time series
plot(AP_decomp)
```

`@sct`
```{r}
test_mc(2)
success_msg("Great Work!")
```

---
## Trend and Seasonality III

```yaml
type: NormalExercise
key: f4b96cb6cb
lang: r
xp: 100
skills: 1
```
Why did it not work as expected? 

As already stated decompose assumes a model of the form:

$$Y _t = s _t + m _t + X _t.$$

However, we found earlier that the seasonal pattern becomes stronger with increasing level of the time series. 
The simple model assumed by `decompose()` considers the seasonal pattern $s_t$ to be constant over time.

One way to improve our results is to take logarithms.  

`@instructions`
- Take the logarithm of the time series and assign it to `AP_logs`.
- Plot `AP_logs`. Can you see what changed?
- `decompose()` `AP_log` and assign the result to `AP_log_decomp`. Plot `AP_log_decomp`.
- What do you think about the result?
`@hint`

`@pre_exercise_code`
```{r}

```

`@sample_code`
```{r}
# Take the logarithm 

 
# Plot


# Decompose and plot


```

`@solution`
```{r}
# Take the logarithm 
AP_log <- log(AirPassengers)
 
# Plot
plot(AP_log)

# Decompose and plot
AP_log_decomp <- decompose(AP_log)
plot(AP_log_decomp)
```

`@sct`
```{r}
ex() %>% check_object("AP_log") %>% check_equal()
ex() %>% check_function("plot", index = 1) %>% check_arg("x") %>% check_equal()
ex() %>% check_object("AP_log_decomp") %>% check_equal()
ex() %>% check_function("plot", index = 2) %>% check_arg("x") %>% check_equal()
success_msg("Great Work!")
```







---
## Aggregate

```yaml
type: NormalExercise
key: c3b3bf1811
lang: r
xp: 100
skills: 1
```
The `AirPassanger` series has both an upward sloping trend and a seasonal pattern. 
We can extract the trend by considering the monthly average of each year. 

To achieve this the function `aggregate()` can be used. This function takes two inputs: a time series and 
a function. The function is applied to all values of each year (time unit).
So if we have a time series with monthly data beginning in January 2000 and ending in December 2001
the function will be applied to the $12$ values of the year $2000$ and the $12$ values of the year 2001. 
By default `aggregate()` computes the sum of all values belonging to one time unit. 
In our case we are however interested in the average for which we can use `mean()`. 

The function call you need should be of the form `aggregate(TimeSeries, FUN = "functionName")`. Do not forget 
the `" "` arround the name of the function you want to apply. 



`@instructions`
- Compute the monthly average of each year with `aggregate()`. Assign the result to the variable `AP_avg`. 
- Produce a plot of the original time series and add a red line with the values of `AP_avg` to the plot. You can use 
  the function `lines()` where you set the argument `col` to `"red"`. If you get the error: "Error: plot.new has not been called yet" you
  have to run the call to `plot()` and `lines()` in one block. 



`@hint`

`@pre_exercise_code`
```{r}

```

`@sample_code`
```{r}
plot(AirPassengers)
#Compute monthly average per year


#Add the average to the plot

```

`@solution`
```{r}
#Compute monthly average per year
AP_avg <- aggregate(AirPassengers, FUN =  "mean")

#Add the average to the plot
plot(AirPassengers)
lines(AP_avg, col = "red")
```

`@sct`
```{r}
ex() %>% check_object("AP_avg") %>% check_equal()
ex() %>% check_function("plot") %>% check_arg("x") %>% check_equal()
fun_lines <- ex() %>% check_function("lines")
fun_lines %>% check_arg("x") %>% check_equal()
fun_lines %>% check_arg("col") %>% check_equal()
success_msg("Great Work!")
```
