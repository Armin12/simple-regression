---
title: 'Relationship between variables and MPG'
output:
  html_document:
    keep_md: yes
  pdf_document: default
  word_document: default
---
Author: Armin Najarpour Foroushani

### Overview and Objectives
We are interested in exploring the relationship between different variables and miles per gallon (MPG) (outcome). Particularly the following two questions:
1) Which one is better for MPG: automatic or manual?
2) MPG difference between automatic and manual transmissions

These questions are answered using mtcars dataset in R.

### Analysis

mpg and am columns show miles per gallon and transmission (0 = automatic, 1 = manual).

A linear regression model was fitted with transmission as two level factor predictor and MPG as the outcome. 

ggplot was used to visualize the data and the fitted lines.

```r
fit = lm(mpg ~ factor(am), data = mtcars)
summary(fit)$coef
```

```
##              Estimate Std. Error   t value     Pr(>|t|)
## (Intercept) 17.147368   1.124603 15.247492 1.133983e-15
## factor(am)1  7.244939   1.764422  4.106127 2.850207e-04
```

![](Simple-Regression_files/figure-html/Regression_and_Visualization-1.png)<!-- -->

We compared mean MPG for manual and automatic transmissions.

```r
cat("automatic: ", mean(mtcars$mpg[mtcars$am == 0]))
```

```
## automatic:  17.14737
```

```r
cat("manual: ", mean(mtcars$mpg[mtcars$am == 1]))
```

```
## manual:  24.39231
```

Intercept for the automatic cars is 17.15 and for the manual is 24.4, and their mean values are 17.15 and 24.4.

t-test was applied to quantify the difference between automatic and manual transmissions:


```r
t.test(mtcars$mpg[mtcars$am==0],mtcars$mpg[mtcars$am==1],paired = FALSE,alternative = "two.sided",var.equal = FALSE)$p.value
```

```
## [1] 0.001373638
```

Difference between the automatic and manual cars is significant with 95% confidence interval (-11.3,-3.2).

Residual plot:

```r
dfbetas(fit)[1:4, 2]
```

```
##      Mazda RX4  Mazda RX4 Wag     Datsun 710 Hornet 4 Drive 
##    -0.15890518    -0.15890518    -0.07408166    -0.13342859
```

```r
er <- resid(fit)
yhat <- predict(fit)
max(abs(er -(mtcars$mpg - yhat)))
```

```
## [1] 4.840572e-14
```

Merc 450SL has very low dfbeta slope while Toyota Corolla, Ford Pantera L, and Maserati Bora have high slope. They can be removed from the analysis.

Predicted values are the mean of each group and the residuals are the difference between each value and its mean.

Dependence of MPG on the other variables:

```r
summary(lm(mpg ~ ., data = mtcars))$coef[, 1]
```

```
## (Intercept)         cyl        disp          hp        drat          wt 
## 12.30337416 -0.11144048  0.01333524 -0.02148212  0.78711097 -3.71530393 
##        qsec          vs          am        gear        carb 
##  0.82104075  0.31776281  2.52022689  0.65541302 -0.19941925
```

```r
t.test(mtcars$mpg,mtcars$am,paired = FALSE,alternative = "two.sided",var.equal = FALSE)$p.value
```

```
## [1] 2.151228e-18
```

```r
t.test(mtcars$mpg,mtcars$wt,paired = FALSE,alternative = "two.sided",var.equal = FALSE)$p.value
```

```
## [1] 1.027903e-16
```

So, transmission and weight are the most important predictors of MPG.

### Conclusion and summary

Overall automatic transmission is better than manual transmission for MPG. However, weight plays an important role too. So, only using transmission biases the model.
