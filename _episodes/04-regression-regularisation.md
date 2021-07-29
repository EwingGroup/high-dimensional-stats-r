---
# Please do not edit this file directly; it is auto generated.
# Instead, please edit 04-regression-regularisation.md in _episodes_rmd/
title: "Regularised regression with many features"
teaching: 0
exercises: 0
questions:
- "Can we fit a model that accounts for and selects many features?"
- "How does regularisation work?"
- "What are some considerations for a regularised model?"
objectives:
- "Understand the benefits of regularised models."
- "Understand how different types of regularisation work."
- "Perform and critically analyse penalised regression."
keypoints:
- "Regularisation is a way to avoid the problems of stepwise
  or iterative model building processes."
- "Modelling features together can help to identify a subset of features
    that contribute to the outcome."
math: yes
---





~~~
library("minfi")
library("here")

methylation <- readRDS(here("data/methylation.rds"))

age <- methylation$Age
methyl_mat <- t(assay(methylation))
~~~
{: .language-r}

In the previous episode we covered variable selection using stepwise/best subset
selection.
These have issues with respect to computation time and efficiency.

In low noise settings and with few or strong relationships, stepwise/subset
works well. However that's often not what we're faced with in biomedicine.


# Ridge regression

When we fit a linear model, we're minimising the RSS.

$$
    \sum_{i=1}^N y_i - X\beta
$$

The idea of regularisation is to add another condition to this to control
the size of the coefficients that come out.

One idea is to control the squared sum of the coefficients, $\beta$.
This is also sometimes called the $L^2$ norm. This is defined as

$$
    |\beta|_2 = \sqrt{\sum_{j=1}^p \beta_j^2}
$$

To control this, we specify that the solution for the equation above
also has to have an $L^2$ norm smaller than a certain amount. Or, equivalently,
we try to minimise a function that includes our $L^2$ norm scaled by a 
factor that is usually written $\lambda$.

$$
    \sum_{i=1}^N y_i - X\beta + \lambda|\beta|_2
$$

> # Exercise
> 
> Run `shinystats::ridgeApp()` and play with the parameters
> 
> Questions:
> 
> > ## Solution
> > 
> {: .solution}
{: .challenge}

Now we can fit a model using ridge regression.


~~~
library("glmnet")
ridge <- glmnet(methyl_mat, age, alpha = 0)
~~~
{: .language-r}

# LASSO regression

LASSO is another type of regularisation. In this case we use the $L^1$ norm,
or the sum of the absolute values of the coefficients.

$$
    |\beta|_1 = \sqrt{\sum_{j=1}^p \beta_j}
$$

This tends to produce


~~~
Error in viridis(40, option = "A", direction = 1): could not find function "viridis"
~~~
{: .error}

<img src="../fig/rmd-04-unnamed-chunk-4-1.png" width="432" style="display: block; margin: auto;" />



~~~
lasso <- cv.glmnet(methyl_mat[, -1], age, alpha = 1)
~~~
{: .language-r}


~~~
## Challenge 5:
## one of these...? probably lasso
elastic <- cv.glmnet(methyl_mat[, -1], age, alpha = 0.5, intercept = FALSE)
~~~
{: .language-r}


> ## Other types of outcomes
> 
> You may have noticed that `glmnet` is written as `glm`, not `lm`.
> This means we can actually model a variety of different outcomes
> using this regularisation approach. For example, we can model binary
> variables using logistic regression, as shown below.
> 
> In fact, `glmnet` is somewhat cheeky as it also allows you to model
> survival using Cox proportional hazards models, which aren't GLMs, strictly
> speaking.
> 
> 
> ~~~
> smoking <- as.numeric(factor(methylation$smoker)) - 1
> # binary outcome
> smoking
> ~~~
> {: .language-r}
> 
> 
> 
> ~~~
>  [1] 0 0 0 0 0 0 1 0 1 0 0 0 1 0 0 0 0 0 1 1 0 0 1 0 0 1 0 0 0 0 0 0 0 0 0 0 0
> ~~~
> {: .output}
> 
> 
> 
> ~~~
> fit <- cv.glmnet(x = methyl_mat, y = smoking, family = "binomial")
> ~~~
> {: .language-r}
> 
> 
> 
> ~~~
> Warning in lognet(xd, is.sparse, ix, jx, y, weights, offset, alpha, nobs, : one
> multinomial or binomial class has fewer than 8 observations; dangerous ground
> Warning in lognet(xd, is.sparse, ix, jx, y, weights, offset, alpha, nobs, : one
> multinomial or binomial class has fewer than 8 observations; dangerous ground
> Warning in lognet(xd, is.sparse, ix, jx, y, weights, offset, alpha, nobs, : one
> multinomial or binomial class has fewer than 8 observations; dangerous ground
> Warning in lognet(xd, is.sparse, ix, jx, y, weights, offset, alpha, nobs, : one
> multinomial or binomial class has fewer than 8 observations; dangerous ground
> Warning in lognet(xd, is.sparse, ix, jx, y, weights, offset, alpha, nobs, : one
> multinomial or binomial class has fewer than 8 observations; dangerous ground
> Warning in lognet(xd, is.sparse, ix, jx, y, weights, offset, alpha, nobs, : one
> multinomial or binomial class has fewer than 8 observations; dangerous ground
> Warning in lognet(xd, is.sparse, ix, jx, y, weights, offset, alpha, nobs, : one
> multinomial or binomial class has fewer than 8 observations; dangerous ground
> Warning in lognet(xd, is.sparse, ix, jx, y, weights, offset, alpha, nobs, : one
> multinomial or binomial class has fewer than 8 observations; dangerous ground
> Warning in lognet(xd, is.sparse, ix, jx, y, weights, offset, alpha, nobs, : one
> multinomial or binomial class has fewer than 8 observations; dangerous ground
> Warning in lognet(xd, is.sparse, ix, jx, y, weights, offset, alpha, nobs, : one
> multinomial or binomial class has fewer than 8 observations; dangerous ground
> Warning in lognet(xd, is.sparse, ix, jx, y, weights, offset, alpha, nobs, : one
> multinomial or binomial class has fewer than 8 observations; dangerous ground
> ~~~
> {: .warning}
> 
> 
> 
> ~~~
> coef <- coef(fit, s = fit$lambda.1se)
> coef[coef[, 1] != 0, 1]
> ~~~
> {: .language-r}
> 
> 
> 
> ~~~
> [1] -1.455287
> ~~~
> {: .output}
> 
> 
> 
> ~~~
> plot(smoking, methyl_mat[, names(which.max(coef[-1]))])
> ~~~
> {: .language-r}
> 
> 
> 
> ~~~
> Error in xy.coords(x, y, xlabel, ylabel, log): 'x' and 'y' lengths differ
> ~~~
> {: .error}
{: .callout}


Figure taken from [Hastie et al. (2020)](https://doi.org/10.1214/19-STS733).


~~~
knitr::include_graphics("../fig/bs_fs_lasso.png")
~~~
{: .language-r}

<img src="../fig/bs_fs_lasso.png" style="display: block; margin: auto;" />


> ## Selecting hyperparameters
> 
> There are various methods to select the "best"
> value for $\lambda$. One idea is to split
> the data into $K$ chunks. We then use $K-1$ of
> these as the training set, and the remaining $1$ chunk
> as the test set. Repeating this process for each of the
> $K$ chunks produces more variability.
> 
> <img src="../fig/cross_validation.svg" style="display: block; margin: auto;" />
>
> To be really rigorous, we could even repeat this *cross-validation*
> process a number of times! This is termed "repeated cross-validation".
{: .callout}


{% include links.md %}