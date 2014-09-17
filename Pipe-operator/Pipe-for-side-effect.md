

# Pipe for side effect

In a pipeline, one may be interested not only in the final outcome but sometimes also in intermediate results. In other words, as the operator pipes the value forward for streamlined processing, one may also want to save the intermediate result for further use, visualize the  intermediate values for better understanding, or print some message to indicate the progress.

However, if we directly call `plot()` or `cat()` in the middle of a pipeline, the pipeline will be broken because they return `NULL` or some other values undesired for further piping. 

For example, we draw a scatter plot and saves its returned value to `z`.


```r
z <- plot(mtcars$mpg)
```

<img src="figure/simple-plot.png" title="plot of chunk simple-plot" alt="plot of chunk simple-plot" style="display: block; margin: auto;" />

```r
z
```

```
# NULL
```

It can be clearly seen that `plot()` does two things: 

1. Draw a scatter image of `mtcars$mpg`.
2. Return `NULL` value.

The first thing is often considered as *side-effect* because it is an influence the function has on the outside environment: it changes what to show in the graphics area. However, when we are building a pipeline and want to plot with some intermediate results, we only want the side effect and have to avoid the returned value, in this case, `NULL`, which is not the desired data we would like to further process.

## Syntax for side effect

`%>>%` provides a syntax to supports side effect piping. The syntax is called **one-sided formula** that starts with `~` to indicate that the right-hand  side expression will be evaluated only for its side-effect, its value will be ignored, and the input value will be returned instead.

For example, after subsetting `mtcars` by lower and upper quantile, if we want to see how many rows are left, we can use a one-sided formula to indicate side-effect.


```r
mtcars %>>%
  subset(mpg >= quantile(mpg, 0.05) & mpg <= quantile(mpg, 0.95),
    c(mpg, cyl, wt)) %>>%
  (~ cat("rows:",nrow(.),"\n")) %>>%   # cat() returns NULL
  summary
```

```
# rows: 28
```

```
#       mpg            cyl             wt      
#  Min.   :13.3   Min.   :4.00   Min.   :1.51  
#  1st Qu.:15.7   1st Qu.:4.00   1st Qu.:2.73  
#  Median :19.2   Median :6.00   Median :3.33  
#  Mean   :19.9   Mean   :6.21   Mean   :3.15  
#  3rd Qu.:21.8   3rd Qu.:8.00   3rd Qu.:3.57  
#  Max.   :30.4   Max.   :8.00   Max.   :5.34
```

To explain in detail, the syntax `(~ f(.))` tells the operator that `f(.)` on the right of `~` should only be evaluated for its side effect with `.` representing the input value, and the input value, i.e. the filtered `mtcars` should continue in the pipeline and be passed to `summary()`.

If `cat()` is directly called here, the code will not only fail because `%>>%` tries to pass the filtered data (a `data.frame`, which is essentially a `list`) to `cat()` and get rejected, but also become less readable because the data processing steps and side-effect steps are mixed to make it harder to distinguish really important lines.


```r
mtcars %>>%
  subset(mpg >= quantile(mpg, 0.05) & mpg <= quantile(mpg, 0.95),
    c(mpg, cyl, wt)) %>>%
  cat("rows:",nrow(.),"\n") %>>%   # cat() returns NULL
  summary
```

```
# Error: argument 1 (type 'list') cannot be handled by 'cat'
```

If you look back at the correct code with the side effect syntax, you may find it more readable because you can easily distinguish side effect expressions with other lines.

The design of `~ expression` syntax for side effect also makes one-sided formula **a branch** of the mainstream pipeline. Note that the design always emphasizes the mainstream pipeline and makes it easy to find out branch pipelines.

Another example is plotting in the middle of a pipeline as side effect.


```r
mtcars %>>%
  subset(mpg >= quantile(mpg, 0.05) & mpg <= quantile(mpg, 0.95)) %>>%
  (~ plot(mpg ~ wt, data = .)) %>>%    # plot() returns NULL
  lm(formula = mpg ~ wt) %>>%
  summary()
```

<img src="figure/side-effect-graphics.png" title="plot of chunk side-effect-graphics" alt="plot of chunk side-effect-graphics" style="display: block; margin: auto;" />

```
# 
# Call:
# lm(formula = mpg ~ wt, data = .)
# 
# Residuals:
#    Min     1Q Median     3Q    Max 
# -3.959 -1.793 -0.222  1.578  5.607 
# 
# Coefficients:
#             Estimate Std. Error t value Pr(>|t|)    
# (Intercept)   35.306      1.985   17.79  4.5e-16 ***
# wt            -4.904      0.611   -8.03  1.7e-08 ***
# ---
# Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
# 
# Residual standard error: 2.56 on 26 degrees of freedom
# Multiple R-squared:  0.713,	Adjusted R-squared:  0.702 
# F-statistic: 64.5 on 1 and 26 DF,  p-value: 1.65e-08
```

With `~`, side-effect operations can be easily distinguished from mainstream pipeline, and the input value of each line can be easily found by skipping the lines that start with `(~ ` while you are looking back.

## Lambda expression

If you feel ambiguous when using `.` in the side-effect expression, you can write a formula after `~`. The expression can be either an expression using `.` or a formula (lambda expression) using user-defined symbol. Therefore the first example can be rewritten like


```r
mtcars %>>%
  subset(mpg >= quantile(mpg, 0.05) & mpg <= quantile(mpg, 0.95),
    c(mpg, cyl, wt)) %>>%
  (~ filtered ~ cat("rows:",nrow(filtered),"\n")) %>>%   # cat() returns NULL
  summary
```

```
# rows: 28
```

```
#       mpg            cyl             wt      
#  Min.   :13.3   Min.   :4.00   Min.   :1.51  
#  1st Qu.:15.7   1st Qu.:4.00   1st Qu.:2.73  
#  Median :19.2   Median :6.00   Median :3.33  
#  Mean   :19.9   Mean   :6.21   Mean   :3.15  
#  3rd Qu.:21.8   3rd Qu.:8.00   3rd Qu.:3.57  
#  Max.   :30.4   Max.   :8.00   Max.   :5.34
```

## Question mark

An easier way to print the intermediate value is to use `(? expr)` syntax like asking a question. This also supports formula as lambda expression.


```r
mtcars %>>% 
  subset(vs == 1, c(mpg, cyl, wt)) %>>%
  (? nrow(.)) %>>%
  (? data ~ ncol(data)) %>>%
  summary
```

```
# ? nrow(.)
# [1] 14
# ? data ~ ncol(data)
# [1] 3
```

```
#       mpg            cyl             wt      
#  Min.   :17.8   Min.   :4.00   Min.   :1.51  
#  1st Qu.:21.4   1st Qu.:4.00   1st Qu.:2.00  
#  Median :22.8   Median :4.00   Median :2.62  
#  Mean   :24.6   Mean   :4.57   Mean   :2.61  
#  3rd Qu.:29.6   3rd Qu.:5.50   3rd Qu.:3.21  
#  Max.   :33.9   Max.   :6.00   Max.   :3.46
```

## Stopping

Side effects are usually used on purpose not to influence the mainstream pipeline or workflow. But in some cases, such influence can be useful.

An example is stopping. If we call `stopifnot(...)` as side effect in the middle of a pipeline, we are actually trying to ensure a series of conditions. If any of the conditions does not hold, the evaluation will stop.

The following code build a linear model on `mtcars` and before making predictions, we insert a condition to check: if $$R^2 >= 0.5$$ is violated, the pipeline will be terminated immediately.

In this particular case, the required $$R^2$$ is too low to be violated for this linear model.


```r
mtcars %>>% 
  subset(vs == 1, c(mpg, cyl, wt)) %>>%
  lm(formula = mpg ~ cyl + wt) %>>%
  (~ stopifnot(summary(.)$r.squared >= 0.5)) %>>%
  predict(newdata = list(cyl = 4.5, wt  = 3.0))
```

```
#     1 
# 22.34
```

If we adjust the threshold to 0.8, the pipeline will be stopped before making predictions.


```r
mtcars %>>% 
  subset(vs == 1, c(mpg, cyl, wt)) %>>%
  lm(formula = mpg ~ cyl + wt) %>>%
  (~ stopifnot(summary(.)$r.squared >= 0.8)) %>>%
  predict(newdata = list(cyl = 4.5, wt  = 3.0))
```

```
# Error: summary(.)$r.squared >= 0.8 is not TRUE
```

## Debugging

Another way to use side effect is debugging, which is more flexible than violently stopping the pipeline and gives you a chance to see what happens. If you want to go into debugging mode in the middle of a pipeline to inspect the environment interactively, you only have to insert `browser()` as a side-effect expression after the line you want to break.

For example, 

```r
mtcars %>>% 
  subset(vs == 1, c(mpg, cyl, wt)) %>>%
  (~ browser()) %>>%
  lm(formula = mpg ~ cyl + wt) %>>%
  summary
```

The code will pause at `browser()` and go into interactive debugging mode.

```
Called from: eval(expr, envir, enclos)
Browse[1]> head(.)
                mpg cyl    wt
Datsun 710     22.8   4 2.320
Hornet 4 Drive 21.4   6 3.215
Valiant        18.1   6 3.460
Merc 240D      24.4   4 3.190
Merc 230       22.8   4 3.150
Merc 280       19.2   6 3.440
Browse[1]> 
```

In the mode, `.` represents the object being piped, and you can evaluate any expression you are interested in. To continue piping, just hit `<Enter>`.

If you want to distinguish different breakpoints, you can add `cat()` or `message()` ahead to show some message before getting paused.

```r
mtcars %>>% 
  subset(vs == 1, c(mpg, cyl, wt)) %>>%
  (~ message("debug1")) %>>%
  (~ browser()) %>>%
  lm(formula = mpg ~ cyl + wt) %>>%
  summary
```

The code will pause after showing the message `debug1` as following:

```
debug1
Called from: eval(expr, envir, enclos)
Browse[1]> 
```

In this way, you can easily find out which breakpoint is hit.

Since `browser()` supports conditional breakpoint with `expr =` being set, the breakpoint can be smart to examine the value of the expression and then decide whether or not to pause. For example,

```r
mtcars %>>% 
  subset(vs == 1, c(mpg, cyl, wt)) %>>%
  lm(formula = mpg ~ cyl + wt) %>>%
  summary %>>%
  (~ browser(expr = .$r.squared < 0.8))
```

If the $$R^2$$ of the linear model is less than 0.8, the evaluation will pause and wait for browser. You can adjust the threshold at anytime to any value you think indicating a problem.
