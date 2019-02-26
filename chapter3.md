---
title: 'Trend and Seasonality'
description: ""
---

## Insert exercise title here

```yaml
type: NormalExercise
key: 7c986018c3
xp: 100
```



`@instructions`
- load the quantmod package
- download monthly German unemployment rate (not seasonally adjusted) from `FRED`
- Inspect the downloaded data

`@hint`


`@pre_exercise_code`
```{r}

```

`@sample_code`
```{r}

```

`@solution`
```{r}
# Load the quantmod package
library(quantmod)
# Import the Data
unemp <- getSymbols("LMUNRRTTDEM156N", src = "FRED", auto.assign = FALSE)
# Inspect the downloaded data
```

`@sct`
```{r}

```
