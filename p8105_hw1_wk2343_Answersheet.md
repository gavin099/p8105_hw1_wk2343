Homework 01
================
Gavin Ko wk2343
2019/9/13

# Problem 1

## 0\. Library

``` r
library(tidyverse)
```

    ## -- Attaching packages ------------------------------------------------------------ tidyverse 1.2.1 --

    ## v ggplot2 3.2.1     v purrr   0.3.2
    ## v tibble  2.1.3     v dplyr   0.8.3
    ## v tidyr   0.8.3     v stringr 1.4.0
    ## v readr   1.3.1     v forcats 0.4.0

    ## -- Conflicts --------------------------------------------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(knitr)
```

## 1\. Creating data frame

To create the data frame requested, I would like to set “HelloWorld” as
a great start. Unfortunately, that’s gonna make the length 10, rather
than desired 8, so that’s why I put it in this format. For factors, I
simply put 1,2,3 as my categories.

``` r
p1_df = tibble(
  randomsample = rnorm(8),
  logical = randomsample > 0,
  character = c("H","E","L","L","W","O","R","D"),
  factor = factor( c(1,1,2,2,3,3,3,3) )
)
```

## 2\. Calculating means

``` r
#for random sample itself
mean( pull(p1_df, randomsample) )
```

    ## [1] -0.7296151

``` r
#for logical vector
mean( pull(p1_df, logical) )
```

    ## [1] 0.25

``` r
#for character vector length 8
mean( pull(p1_df, character) )
```

    ## Warning in mean.default(pull(p1_df, character)): argument is not numeric or
    ## logical: returning NA

    ## [1] NA

``` r
#for factor vector length 8
mean( pull(p1_df, factor) )
```

    ## Warning in mean.default(pull(p1_df, factor)): argument is not numeric or
    ## logical: returning NA

    ## [1] NA

It turns out that only numerical and logical data works in mean
calculation.

## 3\. As.numeric transformation

``` r
#logical vector
P1_logicaltonumeric = as.numeric( pull(p1_df, logical) ) 
#character vector
P1_charactertonumeric = as.numeric( pull(p1_df, character) ) 
```

    ## Warning: NAs introduced by coercion

``` r
#factor vector
P1_factortonumeric = as.numeric( pull(p1_df, factor) )
```

Mean function is built for arithmetic operations, so typically the iuput
data should be numeric. Logical vectors also works cause it
automatically assign 1 to TRUE and 0 to FALSE.

After numeric transformation, logical vector is transformed to 0
representing false, and 1 representing true.

The result of character vector transformation is still NA, since there’s
no way to set characters as digits.

The result of factor vector is done by transforming all categories
indicator into numbers.

## 4\. Data class transformation

For this part, I merge two requested process together in one line of
code. random sample are all put in the front just for tidyness.

``` r
#converting logical to numeric
p1_df_convert_numeric = pull(p1_df, randomsample) * as.numeric( pull(p1_df, logical) )
p1_df_convert_numeric
```

    ## [1] 0.0000000 0.0000000 0.2620240 0.0000000 0.0000000 0.0000000 0.6426566
    ## [8] 0.0000000

After numeric transformation, the logical vector is transformed to
either 1 or 0. While those \>0 elements in random sample stays the same,
those elements with value \<= 0 will became 0 after multiplication.

``` r
#converting logical to factor
p1_df_convert_factor = pull(p1_df, randomsample) * factor( pull(p1_df, logical) )
```

    ## Warning in Ops.factor(pull(p1_df, randomsample), factor(pull(p1_df,
    ## logical))): '*' not meaningful for factors

``` r
p1_df_convert_factor
```

    ## [1] NA NA NA NA NA NA NA NA

As shown above, it’s not meaningful to multiply numbers to a factor
vector. This leads to a NA result.

``` r
#converting logical to factor, then convert it to numeric
p1_df_convert_numericandfactor = pull(p1_df, randomsample) * as.numeric( factor( pull(p1_df, logical) ) )
p1_df_convert_numericandfactor
```

    ## [1] -2.67484185 -0.64082451  0.52404799 -1.00515052 -0.46826577 -0.04366341
    ## [7]  1.28531316 -1.90885538

The pattern of this transformation follows the rule below:

Step1. logical to factor, devided into two categories “True” and
“False”, rather than boolean True and False.

Step2. factor to numeric, while “False” would be assigned with value 1,
“True” would be assigned with value 2.

Under this pattern, the elements \> 0 will be multiplied by two after
multiplication, while those \<= 0 holds the same value as it was.

# Problem 2

## 1\. Creating data frame

``` r
p2_df = tibble(
  x = rnorm(500),
  y = rnorm(500),
  logicalVec = x + y > 1,
  numericVec = as.numeric(logicalVec),
  factorVec = as.factor(logicalVec)
)
```

The size of the dataset is 5 columns and 500 rows. The mean of x is
0.0202548. The median of x is 0.0345319. The standard deveation of x is
0.9935055. The proportion of cases for which x + y \> 1 is 0.242

## 2\. Scatterplots

First of all, I’ll do the one with logical variables, and save the
consequence to repository.

``` r
#scatter plot with logical vector
ggplot(p2_df, aes(x = x, y = y, col = logicalVec) ) + geom_point()
```

![](p8105_hw1_wk2343_Answersheet_files/figure-gfm/scatterplot%201-1.png)<!-- -->

In this plot, we can see that the different colors are seperated by the
straightline: x + y = 1, while those falls below indicates x + y \<= 1,
which correspond to their logic status.

``` r
#saving plots
ggsave("p2_df_scatterplot_logical.png")
```

    ## Saving 7 x 5 in image

Secondly, I’ll move on to numeric categories.

``` r
#scatter plot with numeric vector
ggplot(p2_df, aes(x = x, y = y, col = numericVec) ) + geom_point()
```

![](p8105_hw1_wk2343_Answersheet_files/figure-gfm/scatterplot%202-1.png)<!-- -->

The two-color seperation remains the same as the previous plot, but the
scaling for numeric factor is on gradiant colors.

Lastly, the factors.

``` r
#scatter plot with factor vector
ggplot(p2_df, aes(x = x, y = y, col = factorVec) ) + geom_point()
```

![](p8105_hw1_wk2343_Answersheet_files/figure-gfm/scatterplot%203-1.png)<!-- -->

The result is literally the same as first plot, the only difference is
that right now, the x + y \> 1 condition is factorized as “TRUE” or
“FALSE”.
