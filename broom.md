---
title: "Broom"
teaching: 30
exercises: 20
source: Rmd
editor_options: 
  markdown: 
    wrap: sentence
---



::::::::::::::::::::::::::::::::::::::: objectives

- To be able to present model outcomes using Broom

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- How can I present model outputs in an easier to read way?

::::::::::::::::::::::::::::::::::::::::::::::::::

## Data


``` r
# We will need these libraries and this data later.
library(ggplot2)
library(tidyverse)
library(lmtest)
library(sandwich)
library(broom)

hgtwgt_survey <- read.csv("data/hgt_wgt.csv")
amrData <- read.csv("data/dig_health_hub_amr_v2.csv")
```

## Broom

The 'broom' package offers an alternative way of presenting the output of statistical analysis.
It centers around three S3 methods, each of which take common objects produced by R statistical functions (lm, t.test, nls, etc) and convert them into a tibble.

These are:

-   tidy: constructs a tibble that summarizes the model’s statistical findings. This includes coefficients and p-values for each term in a regression, per-cluster information in clustering applications, or per-test information for multtest functions.
-   augment: add columns to the original data that was modeled. This includes predictions, residuals, and cluster assignments.
-   glance: construct a concise one-row summary of the model. This typically contains values such as R\^2, adjusted R\^2, and residual standard error that are computed once for the entire model.

Let's revisit the linear model exploring the relationship between height and weight:


``` r
reg_weight_height <- lm(weight.kg. ~ height.cm., data = hgtwgt_survey)

summary(reg_weight_height)
```

``` output

Call:
lm(formula = weight.kg. ~ height.cm., data = hgtwgt_survey)

Residuals:
    Min      1Q  Median      3Q     Max 
-53.257 -11.782  -0.578  12.686  55.936 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept) 26.37137    9.11826   2.892  0.00392 ** 
height.cm.   0.30844    0.05373   5.740 1.32e-08 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 18.44 on 843 degrees of freedom
  (155 observations deleted due to missingness)
Multiple R-squared:  0.03762,	Adjusted R-squared:  0.03648 
F-statistic: 32.95 on 1 and 843 DF,  p-value: 1.318e-08
```

There is a lot of useful information, but it not available in a way so that you can combine it with other models or do further analysis.
We can convert this to tabular data using the 'tidy' function.


``` r
tidy(reg_weight_height)
```

``` output
# A tibble: 2 × 5
  term        estimate std.error statistic      p.value
  <chr>          <dbl>     <dbl>     <dbl>        <dbl>
1 (Intercept)   26.4      9.12        2.89 0.00392     
2 height.cm.     0.308    0.0537      5.74 0.0000000132
```

The row names have been moved into a column called term, and the column names are simple and consistent (and can be accessed using \$).

Information about the model can be explored with 'augment'.
The function augments the original data with information from the model, such as the fitted values and residuals for each of the original points in the regression.


``` r
augment(reg_weight_height)
```

``` output
# A tibble: 845 × 9
   .rownames weight.kg. height.cm. .fitted  .resid    .hat .sigma    .cooksd
   <chr>          <dbl>      <dbl>   <dbl>   <dbl>   <dbl>  <dbl>      <dbl>
 1 1               84.4       186.    83.7   0.669 0.00353   18.5 0.00000234
 2 2               51.5       161.    75.9 -24.4   0.00181   18.4 0.00159   
 3 3               85.0       151.    72.8  12.1   0.00414   18.4 0.000904  
 4 4               72.0       175.    80.4  -8.42  0.00149   18.5 0.000156  
 5 5               70.9       170.    78.8  -7.94  0.00119   18.5 0.000110  
 6 7               48.7       156.    74.3 -25.6   0.00279   18.4 0.00271   
 7 8               82.3       168.    78.1   4.24  0.00121   18.5 0.0000321 
 8 9               68.0       171.    79.0 -10.9   0.00120   18.5 0.000212  
 9 11              88.4       156.    74.5  13.9   0.00265   18.4 0.000751  
10 13              71.0       168.    78.2  -7.24  0.00120   18.5 0.0000923 
# ℹ 835 more rows
# ℹ 1 more variable: .std.resid <dbl>
```

Some of the data presented by 'augment' will be discussed in the supplemental episode Linear Regression Diagnostics.

Summary statistics are computed for the entire regression, such as R\^2 and the F-statistic can be accessed with the 'glance' function:


``` r
glance(reg_weight_height)
```

``` output
# A tibble: 1 × 12
  r.squared adj.r.squared sigma statistic      p.value    df logLik   AIC   BIC
      <dbl>         <dbl> <dbl>     <dbl>        <dbl> <dbl>  <dbl> <dbl> <dbl>
1    0.0376        0.0365  18.4      33.0 0.0000000132     1 -3661. 7328. 7342.
# ℹ 3 more variables: deviance <dbl>, df.residual <int>, nobs <int>
```

### Generalised linear models

We can also use the 'broom' functions to present data from Generalised linear and non-linear models.
For example, if we wanted to explore height in relation to gender.


``` r
# create a Generalised Linear Model
glmheightgender <- glm(gender.M. ~ height.cm., hgtwgt_survey, family = "binomial")
summary(glmheightgender)
```

``` output

Call:
glm(formula = gender.M. ~ height.cm., family = "binomial", data = hgtwgt_survey)

Coefficients:
             Estimate Std. Error z value Pr(>|z|)
(Intercept) -0.296726   0.958811  -0.309    0.757
height.cm.   0.002548   0.005646   0.451    0.652

(Dispersion parameter for binomial family taken to be 1)

    Null deviance: 1271.2  on 919  degrees of freedom
Residual deviance: 1271.0  on 918  degrees of freedom
  (80 observations deleted due to missingness)
AIC: 1275

Number of Fisher Scoring iterations: 3
```

Use of 'tidy':


``` r
tidy(glmheightgender)
```

``` output
# A tibble: 2 × 5
  term        estimate std.error statistic p.value
  <chr>          <dbl>     <dbl>     <dbl>   <dbl>
1 (Intercept) -0.297     0.959      -0.309   0.757
2 height.cm.   0.00255   0.00565     0.451   0.652
```

Use of 'augment':


``` r
augment(glmheightgender)
```

``` output
# A tibble: 920 × 9
   .rownames gender.M. height.cm. .fitted .resid    .hat .sigma  .cooksd
   <chr>         <int>      <dbl>   <dbl>  <dbl>   <dbl>  <dbl>    <dbl>
 1 1                 1       186.  0.177    1.10 0.00324   1.18 0.00136 
 2 2                 0       161.  0.113   -1.23 0.00169   1.18 0.000950
 3 3                 0       151.  0.0871  -1.21 0.00390   1.18 0.00214 
 4 4                 1       175.  0.150    1.11 0.00136   1.18 0.000588
 5 5                 1       170.  0.137    1.12 0.00109   1.18 0.000477
 6 7                 0       156.  0.0996  -1.22 0.00262   1.18 0.00146 
 7 8                 0       168.  0.130   -1.23 0.00111   1.18 0.000636
 8 9                 0       171.  0.138   -1.24 0.00110   1.18 0.000632
 9 11                1       156.  0.101    1.13 0.00248   1.18 0.00113 
10 13                0       168.  0.132   -1.23 0.00110   1.18 0.000630
# ℹ 910 more rows
# ℹ 1 more variable: .std.resid <dbl>
```

Use of 'glance':


``` r
glance(glmheightgender)
```

``` output
# A tibble: 1 × 8
  null.deviance df.null logLik   AIC   BIC deviance df.residual  nobs
          <dbl>   <int>  <dbl> <dbl> <dbl>    <dbl>       <int> <int>
1         1271.     919  -636. 1275. 1285.    1271.         918   920
```

You will notice that the statistics computed by 'glance' are different for glm objects than for lm (e.g. deviance rather than R\^2).

### Hypothesis testing

The tidy function can also be applied a range of hypotheses tests, such as built-in functions like t.test, cor.test, and wilcox.test.

### t-test


``` r
tt <- t.test(height.cm. ~ gender.M., hgtwgt_survey)
tidy(tt)
```

``` output
# A tibble: 1 × 10
  estimate estimate1 estimate2 statistic p.value parameter conf.low conf.high
     <dbl>     <dbl>     <dbl>     <dbl>   <dbl>     <dbl>    <dbl>     <dbl>
1   -0.349      169.      170.    -0.451   0.652      905.    -1.87      1.17
# ℹ 2 more variables: method <chr>, alternative <chr>
```

Some cases might have fewer columns (for example, no confidence interval).

Wilcox test:


``` r
wt <- wilcox.test(height.cm. ~ gender.M., hgtwgt_survey)
tidy(wt)
```

``` output
# A tibble: 1 × 4
  statistic p.value method                                           alternative
      <dbl>   <dbl> <chr>                                            <chr>      
1    103132   0.586 Wilcoxon rank sum test with continuity correcti… two.sided  
```

Since the 'tidy' output is already only one row, glance returns the same output:


``` r
# t-test
glance(tt)
```

``` output
# A tibble: 1 × 10
  estimate estimate1 estimate2 statistic p.value parameter conf.low conf.high
     <dbl>     <dbl>     <dbl>     <dbl>   <dbl>     <dbl>    <dbl>     <dbl>
1   -0.349      169.      170.    -0.451   0.652      905.    -1.87      1.17
# ℹ 2 more variables: method <chr>, alternative <chr>
```

``` r
# Wilcox test
glance(wt)
```

``` output
# A tibble: 1 × 4
  statistic p.value method                                           alternative
      <dbl>   <dbl> <chr>                                            <chr>      
1    103132   0.586 Wilcoxon rank sum test with continuity correcti… two.sided  
```

The chisq.test enables us to investigate whether changes in one categorical variable are related to changes in another categorical variable.

Let's explore if there are differences in gender of those tested in each truste.


``` r
#let's organise our data for the test

# Select the columns of interest
selected_data <- amrData %>% select(trst_cd, sex_male)

# Create a contingency table for trust and gender
contingency_table <- table(selected_data$trst_cd, selected_data$sex_male)

# Perform chi-square test
chi_square_test <- chisq.test(contingency_table)
```

``` warning
Warning in chisq.test(contingency_table): Chi-squared approximation may be
incorrect
```

``` r
# View the results
print(chi_square_test)
```

``` output

	Pearson's Chi-squared test

data:  contingency_table
X-squared = 264.8, df = 237, p-value = 0.1037
```


``` r
# we can use tidy on our results
tidy(chi_square_test)
```

``` output
# A tibble: 1 × 4
  statistic p.value parameter method                    
      <dbl>   <dbl>     <int> <chr>                     
1      265.   0.104       237 Pearson's Chi-squared test
```

The 'augment' method is defined only for chi-squared tests, since there is no meaningful sense, for other tests, in which a hypothesis test produces output about each initial data point.


``` r
augment(chi_square_test)
```

``` output
# A tibble: 476 × 9
   Var1  Var2  .observed .prop .row.prop .col.prop .expected   .resid .std.resid
   <fct> <fct>     <int> <dbl>     <dbl>     <dbl>     <dbl>    <dbl>      <dbl>
 1 R0A   0             2 0.002     1       0.00401     0.998  1.00       1.42   
 2 R1A   0             1 0.001     0.143   0.00200     3.49  -1.33      -1.89   
 3 R1C   0             4 0.004     0.571   0.00802     3.49   0.271      0.385  
 4 R1D   0             1 0.001     0.5     0.00200     0.998  0.00200    0.00283
 5 R1E   0             0 0         0       0           2.00  -1.41      -2.00   
 6 R1F   0             2 0.002     1       0.00401     0.998  1.00       1.42   
 7 R1G   0             2 0.002     1       0.00401     0.998  1.00       1.42   
 8 R1H   0             0 0         0       0           1.50  -1.22      -1.73   
 9 R1J   0             4 0.004     0.571   0.00802     3.49   0.271      0.385  
10 R1K   0             2 0.002     0.667   0.00401     1.50   0.411      0.582  
# ℹ 466 more rows
```

There are a number of underlying assumptions of a Chi-Square test, these are:

-   Independence: The Chi-Square test assumes that the observations in the data are independent of each other.
    This means that the outcome of one observation should not influence the outcome of another.

-   Random sampling: The data should be obtained through random sampling to ensure that it is representative of the population from which it was drawn.

-   Expected frequency: The Chi-Square test assumes that the expected frequency count for each cell in the contingency table should be at least 5.
    If this assumption is not met, the test results may not be reliable.

As we have received a warning about the reliability of our test, it is likely that one of these assumptions has not been met, and that this is not a suitable test for this data.

::: challenge
## Challenge 3

Use broom to amend the display of your model outputs.

Which function(s) did you use and why?
:::

### Conventions

There are some conventions that enable consistency across the broom functions, these are: \* The output of the tidy, augment and glance functions is always a tibble.

-   The output never has rownames. This ensures that you can combine it with other tidy outputs without fear of losing information (since rownames in R cannot contain duplicates).
-   Some column names are kept consistent, so that they can be combined across different models and so that you know what to expect (in contrast to asking “is it pval or PValue?” every time).

:::::::::::::::::::::::::::::::::::::::: keypoints

- Broom can be used to create reusable outputs from various analyses, in the form of tibbles.

::::::::::::::::::::::::::::::::::::::::::::::::::
