

# Dot function

As we have mentioned earlier, not all functions are pipe-friendly. While `%>>%` accepts `{}` and `()` on the right side, `Pipe` object provides `.()` to perform exactly the same set of features with `x %>>% (...)` which is powerful enough to meet a wide variety of demands in piping.

## Pipe to dot

If you supply an ordinary function call in `.()` it will evaluate it with `.` being the piped value.


```r
Pipe(mtcars)$
  .(lm(mpg ~ cyl + wt, data = .))
```

```
# $value : lm 
# ------
# 
# Call:
# lm(formula = mpg ~ cyl + wt, data = .)
# 
# Coefficients:
# (Intercept)          cyl           wt  
#       39.69        -1.51        -3.19
```

## Pipe by formula

To avoid ambiguity, one can write a formula in `.()` to define the symbol representing `mtcars`.


```r
Pipe(mtcars)$
  .(df ~ lm(mpg ~ cyl + wt, data = df))
```

```
# $value : lm 
# ------
# 
# Call:
# lm(formula = mpg ~ cyl + wt, data = df)
# 
# Coefficients:
# (Intercept)          cyl           wt  
#       39.69        -1.51        -3.19
```

## Pipe for side effect

The syntax of one-sided formula is also interpreted as side effect in `.()`.


```r
Pipe(mtcars)$
  subset(mpg >= quantile(mpg, 0.05) & mpg <= quantile(mpg, 0.95),
    c(mpg, cyl, wt))$
  .(~ cat("rows:",nrow(.),"\n"))$
  lm(formula = mpg ~ .)$
  summary()$
  coef()
```

```
# rows: 28
```

```
# $value : matrix 
# ------
#             Estimate Std. Error t value  Pr(>|t|)
# (Intercept)   36.631     1.6127  22.713 3.299e-18
# cyl           -1.418     0.3533  -4.014 4.783e-04
# wt            -2.528     0.7658  -3.301 2.895e-03
```

The question mark works too.


```r
Pipe(mtcars)$
  subset(vs == 1, c(mpg, cyl, wt))$
  .(? nrow(.))$
  .(? data ~ ncol(data))$
  summary()
```

```
# ? nrow(.)
# [1] 14
# ? data ~ ncol(data)
# [1] 3
```

```
# $value : table 
# ------
#       mpg            cyl             wt      
#  Min.   :17.8   Min.   :4.00   Min.   :1.51  
#  1st Qu.:21.4   1st Qu.:4.00   1st Qu.:2.00  
#  Median :22.8   Median :4.00   Median :2.62  
#  Mean   :24.6   Mean   :4.57   Mean   :2.61  
#  3rd Qu.:29.6   3rd Qu.:5.50   3rd Qu.:3.21  
#  Max.   :33.9   Max.   :6.00   Max.   :3.46
```

The debugging function `browser()` works exactly the same.

```r
mtcars %>>% 
  subset(vs == 1, c(mpg, cyl, wt)) %>>%
  (~ browser()) %>>%
  lm(formula = mpg ~ cyl + wt) %>>%
  summary
```

## Pipe with assignment

As we have previously mentioned that assignment may be the most important side effect so it deserves a syntax. One-sided formula with a symbol on the right indicates the input value should be assigned to that symbol. The syntax works in `.()` too.


```r
pmodel <- Pipe(mtcars)$
  subset(select = c(mpg, wt, cyl))$
  .(~ sub_mtcars)$ # assign subsetted mtcars to sub_mtcars
  lm(formula = mpg ~ wt + cyl)

summary(sub_mtcars)
```

```
#       mpg             wt            cyl      
#  Min.   :10.4   Min.   :1.51   Min.   :4.00  
#  1st Qu.:15.4   1st Qu.:2.58   1st Qu.:4.00  
#  Median :19.2   Median :3.33   Median :6.00  
#  Mean   :20.1   Mean   :3.22   Mean   :6.19  
#  3rd Qu.:22.8   3rd Qu.:3.61   3rd Qu.:8.00  
#  Max.   :33.9   Max.   :5.42   Max.   :8.00
```

Note that `=` in function argument will be interpreted as argument selection, therefore `=` cannot be used to assign value in `.()` function.

The way to perform side-effect assignment is to use `<-` or `->` introduced by pipeR v0.5 with `~`, like the following:


```r
Pipe(mtcars)$
  lm(formula = mpg ~ wt + cyl)$
  .(~ summary(.) -> summ)$
  coef()
```

```
# $value : numeric 
# ------
# (Intercept)          wt         cyl 
#      39.686      -3.191      -1.508
```

```r
summ$r.squared
```

```
# [1] 0.8302
```

If `~` in is removed, the assignment will be done and the value of `summary()` will go on piping.


```r
Pipe(mtcars)$
  lm(formula = mpg ~ wt + cyl)$
  .(summary(.) -> summ)$
  coef()
```

```
# $value : matrix 
# ------
#             Estimate Std. Error t value  Pr(>|t|)
# (Intercept)   39.686     1.7150  23.141 3.043e-20
# wt            -3.191     0.7569  -4.216 2.220e-04
# cyl           -1.508     0.4147  -3.636 1.064e-03
```

```r
summ$r.squared
```

```
# [1] 0.8302
```

Note that the output of two `Pipe`s are different. The `Pipe` with `~ summary(.)` results in a numeric vector of the coeffecients of the linear model because the summary is only a side effect while the `Pipe` without `~` results in a comprehensive matrix showing the summary of the linear coefficients because `coef()` works on the summary.

## Extract element

Element extraction also works in `.()` if a symbol name is supplied.


```r
Pipe(mtcars)$
  lm(formula = mpg ~ wt + cyl)$
  summary()$
  .(r.squared)
```

```
# $value : numeric 
# ------
# [1] 0.8302
```
