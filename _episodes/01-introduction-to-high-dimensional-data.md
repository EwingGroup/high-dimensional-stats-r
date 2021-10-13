---
# Please do not edit this file directly; it is auto generated.
# Instead, please edit 01-introduction-to-high-dimensional-data.md in _episodes_rmd/
title: "Introduction to high-dimensional data"
author: "GS Robertson"
teaching: 60
exercises: 20
questions:
- What are high-dimensional data and what do these data look like in the
  biosciences?
- What are the challenges when analysing high-dimensional data?
- What statistical methods are suitable for analysing these data?
- How can Bioconductor be used to explore high-dimensional data in the
  biosciences?
objectives:
- Explore examples of high-dimensional data in the biosciences.
- Appreciate challenges involved in analysing high-dimensional data.
- Explore different statistical methods used for analysing high-dimensional data.
- Work with example data created from biological studies.
keypoints:
- High-dimensional data are data in which the number of features, $p$, are close
  to or larger than the number of observations, $n$.
- These data are becoming more common in the biological sciences due to
  increases in data storage capabilities and computing power.
- Standard statistical methods, such as linear regression, run into difficulties
  when analysing high-dimensional data.
- In this workshop, we will explore statistical methods used for analysing
  high-dimensional data using datasets available on Bioconductor.
math: yes
---





# What are high-dimensional data? 

*High-dimensional data* are defined as data in which the number of features
in the data, $p$, are equal or larger than the number of observations (or data
points), $n$. Unlike *low-dimensional data* in which the number of observations,
$n$, far outnumbers the number of features, $p$, high-dimensional data require
consideration of potential problems that comes from having a large number of
features.

High-dimensional data have become more common in many scientific fields as new
automated data collection techniques have been developed. More and more datsets
have a large number of features (or variables) and some have as many features as
there are rows in the dataset. Datasets in which $p$>=$n$ are becoming more
common. Such datasets pose a challenge for data analysis as standard methods of
analysis, such as linear regression, are no longer appropriate.

High-dimensional datasets are common in the biological sciences. Subjects like
genomics and medical sciences often use both large (in terms of $n$) and wide
(in terms of $p$) datasets that can be difficult to analyse or visualise using
standard statistical tools. An example of high-dimensional data in biological
sciences may include data collected from hospital patients recording symptoms,
blood test results, behaviours, and general health, resulting in datasets with
large numbers of features. Researchers often want to relate these features to
specific patient outcomes (e.g. survival, length of time spent in hospital).
An example of what high-dimensional data might look like in a biomedical study
is shown in Figure 1. 

<img src="../fig/intro-table.png" title="plot of chunk table-intro" alt="plot of chunk table-intro" style="display: block; margin: auto;" />



> ## Challenge 1 
> 
> Descriptions of three research questions and their datasets are given below.
> Which of these are considered to have high-dimensional data?
> 
> 1. Predicting patient blood pressure using cholesterol level in blood, age,
>    and BMI measurements collected from 100 patients.
> 2. Predicting patient blood pressure using cholesterol level in blood, age,
>    and BMI as well as information from 200,000 single nucleotide polymorphisms
>    from 100 patients.
> 3. Predicting length of time patients spend in hospital with pneumonia.
>    infection using measurements on age, BMI, length of time with symptoms,
>    number of symptoms, and percentage of neutrophils in blood using data
>    from 200 patients.
> 4. Predicting probability of a patient's cancer progressing using gene
>    expression data as well as data associated with general patient health
>    (age, weight, BMI, blood pressure) and cancer growth (tumour size,
>    localised spread, blood test results).
> 
> > ## Solution
> > 
> > 2 and 4
> {: .solution}
{: .challenge}


Now that we have an idea of what high-dimensional data look like we can think
about the challenges we face in analysing them.


# Challenges in dealing with high-dimensional data 

Most classical statistical methods are set up for use on low-dimensional data
(i.e. data where the number of observations $n$ is much larger than the number
of features $p$). This is because low-dimensional data were much more common in
the past when data collection was more difficult and time consuming. In recent
years advances in information technology have allowed large amounts of data to
be collected and stored with relative ease. This has allowed large numbers of
features to be collected, meaning that datasets in which $p$ matches or exceeds
$n$ are common (collecting observations is often more difficult or expensive
than collecting many features from a single observation).

Datasets with large numbers of features are difficult to visualise. When
exploring low-dimensional datasets, it is possible to plot the response variable
against each of the limited number of explanatory variables to get an idea which
of these are important predictors of the response. With high-dimensional data
the large number of explanatory variables makes doing this difficult. In some
high-dimensional datasets it can also be difficult to identify a single response
variable, making standard data exploration and analysis techniques less useful.

Let's have a look at a simple dataset with lots of features to understand some
of the challenges we are facing when working with high-dimensional data.


> ## Challenge 2 
> 
> Load the `Prostate` dataset from the `lasso2` package and examine the column
> names. 
>
> Examine the dataset (in which each row represents a single patient) and plot
> relationships between the variables using the `pairs` function. Why does it
> become more difficult to plot relationships between pairs of variables with
> increasing numbers of variables? Discuss in groups.
> 
> > ## Solution
> > 
> > 
> > ~~~
> > library(lasso2)  #load lasso2 package
> > data(Prostate)   #load the Prostate dataset
> > ~~~
> > {: .language-r}
> > 
> > 
> > ~~~
> > View(Prostate)   #view the dataset
> > ~~~
> > {: .language-r}
> > 
> > 
> > ~~~
> > names(Prostate)  #examine column names
> > ~~~
> > {: .language-r}
> > 
> > 
> > 
> > ~~~
> > [1] "lcavol"  "lweight" "age"     "lbph"    "svi"     "lcp"     "gleason"
> > [8] "pgg45"   "lpsa"   
> > ~~~
> > {: .output}
> > 
> > 
> > 
> > ~~~
> > pairs(Prostate)  #plot each pair of variables against each other
> > ~~~
> > {: .language-r}
> > 
> > <img src="../fig/rmd-01-pairs-prostate-1.png" title="plot of chunk pairs-prostate" alt="plot of chunk pairs-prostate" width="432" style="display: block; margin: auto;" />
> > The `pairs` function plots relationships between each of the variables in
> > the `Prostate` dataset. This is possible for datasets with smaller numbers
> > of variables, but for datasets in which $p$ is larger it becomes difficult
> > (and time consuming) to visualise relationships between all variables in the
> > dataset. Even where visualisation is possible, fitting models to datasets
> > with large numbers of variables is difficult due to the potential for
> > overfitting and difficulties in identifying a response variable.
> > 
> {: .solution}
{: .challenge}

Imagine we are carrying out least squares regression on a dataset with 25
observations. Fitting a best fit line through these data produces a plot shown
in Figure 2a.

However, imagine a situation in which the ratio of observations to features in
a dataset is almost equal. In that situation the effective number of
observations per features is low. The result of fitting a best fit line through
few observations can be seen in Figure 2b.

<img src="../fig/intro-scatterplot.png" title="plot of chunk intro-figure" alt="plot of chunk intro-figure" style="display: block; margin: auto;" />

In the first situation, the least squares regression lines does not fit the data
perfectly and there is some error around the regression line. But when there are
only two observations the regression line will fit through the points exactly,
resulting in overfitting of the data. This suggests that carrying out least
squares regression on a dataset with few data points per feature would result
in difficulties in applying the resulting model to further datsets. This is a
common problem when using regression on high-dimensional datasets.

Another problem in carrying out regression on high-dimensional data is dealing
with correlations between explanatory variables. The large numbers of features
in these datasets makes high correlations between variables more likely.


> ## Challenge 3
> 
> Use the `cor` function to examine correlations between all variables in the
> Prostate dataset. Are some variables highly correlated (i.e. correlation
> coefficients > 0.6)? Carry out a linear regression predicting patient age
> using all variables in the Prostate dataset.
> 
> > ## Solution
> > 
> > 
> > ~~~
> > ## create a correlation matrix of all variables in the Prostate dataset
> > cor(Prostate)
> > ~~~
> > {: .language-r}
> > 
> > 
> > 
> > ~~~
> >            lcavol      lweight       age         lbph         svi          lcp
> > lcavol  1.0000000  0.194128286 0.2249999  0.027349703  0.53884500  0.675310484
> > lweight 0.1941283  1.000000000 0.3075286  0.434934636  0.10877851  0.100237795
> > age     0.2249999  0.307528614 1.0000000  0.350185896  0.11765804  0.127667752
> > lbph    0.0273497  0.434934636 0.3501859  1.000000000 -0.08584324 -0.006999431
> > svi     0.5388450  0.108778505 0.1176580 -0.085843238  1.00000000  0.673111185
> > lcp     0.6753105  0.100237795 0.1276678 -0.006999431  0.67311118  1.000000000
> > gleason 0.4324171 -0.001275658 0.2688916  0.077820447  0.32041222  0.514830063
> > pgg45   0.4336522  0.050846821 0.2761124  0.078460018  0.45764762  0.631528245
> > lpsa    0.7344603  0.354120390 0.1695928  0.179809410  0.56621822  0.548813169
> >              gleason      pgg45      lpsa
> > lcavol   0.432417056 0.43365225 0.7344603
> > lweight -0.001275658 0.05084682 0.3541204
> > age      0.268891599 0.27611245 0.1695928
> > lbph     0.077820447 0.07846002 0.1798094
> > svi      0.320412221 0.45764762 0.5662182
> > lcp      0.514830063 0.63152825 0.5488132
> > gleason  1.000000000 0.75190451 0.3689868
> > pgg45    0.751904512 1.00000000 0.4223159
> > lpsa     0.368986803 0.42231586 1.0000000
> > ~~~
> > {: .output}
> > 
> > 
> > 
> > ~~~
> > ## correlation matrix for variables describing cancer/clinical variables
> > cor(Prostate[, c(1, 2, 4, 6, 9)])
> > ~~~
> > {: .language-r}
> > 
> > 
> > 
> > ~~~
> >            lcavol   lweight         lbph          lcp      lpsa
> > lcavol  1.0000000 0.1941283  0.027349703  0.675310484 0.7344603
> > lweight 0.1941283 1.0000000  0.434934636  0.100237795 0.3541204
> > lbph    0.0273497 0.4349346  1.000000000 -0.006999431 0.1798094
> > lcp     0.6753105 0.1002378 -0.006999431  1.000000000 0.5488132
> > lpsa    0.7344603 0.3541204  0.179809410  0.548813169 1.0000000
> > ~~~
> > {: .output}
> > 
> > 
> > 
> > ~~~
> > ## use linear regression to predict patient age from cancer progression variables
> > model <- lm(
> >     age ~ lcavol + lweight + lbph + lcp + lpsa + svi + gleason + pgg45,
> >     data = Prostate
> > )
> > summary(model)
> > ~~~
> > {: .language-r}
> > 
> > 
> > 
> > ~~~
> > 
> > Call:
> > lm(formula = age ~ lcavol + lweight + lbph + lcp + lpsa + svi + 
> >     gleason + pgg45, data = Prostate)
> > 
> > Residuals:
> >      Min       1Q   Median       3Q      Max 
> > -19.6192  -4.1898   0.1754   4.8268  13.4274 
> > 
> > Coefficients:
> >             Estimate Std. Error t value Pr(>|t|)    
> > (Intercept) 41.82017   11.33028   3.691 0.000387 ***
> > lcavol       2.04919    0.98817   2.074 0.041028 *  
> > lweight      3.31717    1.61968   2.048 0.043536 *  
> > lbph         1.40721    0.53796   2.616 0.010474 *  
> > lcp         -1.35385    0.84781  -1.597 0.113877    
> > lpsa        -1.72700    0.98259  -1.758 0.082293 .  
> > svi          2.18332    2.40451   0.908 0.366353    
> > gleason      1.35628    1.47029   0.922 0.358810    
> > pgg45        0.05856    0.04124   1.420 0.159111    
> > ---
> > Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
> > 
> > Residual standard error: 6.643 on 88 degrees of freedom
> > Multiple R-squared:  0.2701,	Adjusted R-squared:  0.2038 
> > F-statistic: 4.071 on 8 and 88 DF,  p-value: 0.0003699
> > ~~~
> > {: .output}
> > 
> > 
> > 
> > ~~~
> > ## examine model residuals
> > plot(model)
> > ~~~
> > {: .language-r}
> > 
> > <img src="../fig/rmd-01-plot-lm-1.png" title="plot of chunk plot-lm" alt="plot of chunk plot-lm" width="432" style="display: block; margin: auto;" /><img src="../fig/rmd-01-plot-lm-2.png" title="plot of chunk plot-lm" alt="plot of chunk plot-lm" width="432" style="display: block; margin: auto;" /><img src="../fig/rmd-01-plot-lm-3.png" title="plot of chunk plot-lm" alt="plot of chunk plot-lm" width="432" style="display: block; margin: auto;" /><img src="../fig/rmd-01-plot-lm-4.png" title="plot of chunk plot-lm" alt="plot of chunk plot-lm" width="432" style="display: block; margin: auto;" />
> {: .solution}
{: .challenge}

The correlation matrix shows high correlation between some pairs of variables
(e.g. between `lcavol` and `lpsa` and between `gleason` and `pgg45`). Including
correlated variables in the same regression model can lead to problems in fitting
a regression and interpreting the output. Some clinical variables
(i.e. `lcavol`, `lweight`, `lbph`, `lcp`, `lpsa`) show high correlation between
pairs of variables (e.g. between `lcavol` and `lpsa`). To allow variables to be
included in the same model despite high levels of correlation we can use
dimensionality reduction methods to collapse multiple variables into a single
new variable (we will explore this dataset further in the dimensionality
reduction lesson). We can also use modifications to linear regression like
regularisation, which we will discuss in the lesson on high-dimensional
regression.


# What statistical methods are used to analyse high-dimensional data? 

As we found out in the above challenges, carrying out linear regression on
datasets with large numbers of features is difficult due to: high correlation
between variables; difficulty in identifying clear a response variable; risk
of overfitting. These problems are common to many high-dimensional datasets,
for example, those using genomics data with multiple genes, or species
composition data in an environment where relative abundance of different species
within a community is of interest. For such datasets, other statistical methods
may be used to examine whether groups of observations show similar features
and whether these groups may relate to other features in the data (e.g.
phenotype in genetics data). While straight-forward linear regression cannot
be used in datasets with many features, high-dimensional regression methods
are available with methods to deal with overfitting and fitting models including
many explanatory variables.

In situations where the response variable is difficult to identify or where
explanatory variables are highly correlated, dimensionality reduction may be
used to create fewer variables that represent variation in the original dataset.
Various dimensionality reduction methods are available, including principal
component analysis (PCA), factor analysis, and multidimensional scaling, which
are used to address different types of research questions. Dimensionality
reduction methods such as PCA can also be used to visualise data in fewer
number of dimensions, making patterns and clusters within the data easier to
visualise. Exploring data via clustering is a good way of understanding
relationships within observations in complex datasets.

Statistical methods (such as hierarchical clustering and k-means clustering)
are often used to identify clusters within complex datasets. However, simply
identifying clusters visually may not be enough - we also need to determine
whether such clusters are 'real' or simply apparent interpretations of noise
within the data.

Let's create some random data and show how we can create clusters by changing
parameters.


~~~
set.seed(80)     

## create random data from a normal distribution and store as a matrix
x <- matrix(rnorm(200, mean = 0, sd = 1), 100, 2)

plot(x, pch = 19)
~~~
{: .language-r}

<img src="../fig/rmd-01-plot-random-1.png" title="plot of chunk plot-random" alt="plot of chunk plot-random" width="432" style="display: block; margin: auto;" />

~~~
## create three groups for each row of x
selected <- sample(1:3, 100, replace = TRUE)

## plot x and colour by selected
plot(x, col = selected, pch = 19)
~~~
{: .language-r}

<img src="../fig/rmd-01-plot-random-2.png" title="plot of chunk plot-random" alt="plot of chunk plot-random" width="432" style="display: block; margin: auto;" />

~~~
#note there are no clusters in these data

## create random data representing mean of each of the three groups
xsel <- matrix(rnorm(6, mean = 0, sd = 1), 3, 2)
#Note how increasing the value of sd makes clusters clearer

## add values of x to xsel for each of three defined groups
xgroups <- x + xsel[selected, ]
## plot xgroups and colour by each of the three groups
plot(xgroups, col = selected, pch = 19)
~~~
{: .language-r}

<img src="../fig/rmd-01-plot-random-3.png" title="plot of chunk plot-random" alt="plot of chunk plot-random" width="432" style="display: block; margin: auto;" />

> ## Challenge 4
> 
> Change the value of `sd` in the above example. What happens to the data when
> `sd` is increased?
> 
> > ## Solution
> > 
> > When `sd = 1` in above example clusters in randomly generated data are not
> > obvious. Increasing the value of `sd` makes clusters clearer. Sometimes it
> > is possible to convince ourselves that there are clusters in the data just
> > by colouring the data points by their respective groups! Formal cluster
> > analysis and validation is necessary to determine whether visual clusters
> > in data are 'real'.
> > 
> {: .solution}
{: .challenge}


# High-dimensional data in the biosciences

In this workshop, we will look at statistical methods that can be used to
visualise and analyse high-dimensional biological data using packages available
from Bioconductor, open source software for analysing high throughput genomic
data. Bioconductor contains useful packages and example datasets as shown on the
website [https://www.bioconductor.org/](https://www.bioconductor.org/).

Bioconductor packages can be installed and used in `R` using the `BiocManager`
package. Let's install the `minfi` package from Bioconductor (a package for
analysing Illumina Infinium DNA methylation arrays).


~~~
library("minfi")
~~~
{: .language-r}


~~~
browseVignettes("minfi")
~~~
{: .language-r}

We can explore these packages by browsing the vignettes provided in
Bioconductor. Bioconductor has various packages that can be used to load and
examine datasets in `R` that have been made available in Bioconductor, usually
along with an associated paper or package.

Next, we load the `methylation` dataset which represents data collected using
Illumina Infinium methylation arrays which are used to examine methylation
across the human genome. These data include information collected from the
assay as well as associated metadata from individuals from whom samples were
taken.


~~~
library("minfi")
library("here")
library("ComplexHeatmap")

methylation <- readRDS(here("data/methylation.rds"))
head(colData(methylation))
~~~
{: .language-r}



~~~
DataFrame with 6 rows and 14 columns
                    Sample_Well Sample_Name    purity         Sex       Age
                    <character> <character> <integer> <character> <integer>
201868500150_R01C01         A07     PCA0612        94           M        39
201868500150_R03C01         C07   NKpan2510        95           M        49
201868500150_R05C01         E07      WB1148        95           M        20
201868500150_R07C01         G07       B0044        97           M        49
201868500150_R08C01         H07   NKpan1869        95           F        33
201868590193_R02C01         B03   NKpan1850        93           F        21
                    weight_kg  height_m       bmi    bmi_clas Ethnicity_wide
                    <numeric> <numeric> <numeric> <character>    <character>
201868500150_R01C01   88.4505    1.8542   25.7269  Overweight          Mixed
201868500150_R03C01   81.1930    1.6764   28.8911  Overweight  Indo-European
201868500150_R05C01   80.2858    1.7526   26.1381  Overweight  Indo-European
201868500150_R07C01   82.5538    1.7272   27.6727  Overweight  Indo-European
201868500150_R08C01   87.5433    1.7272   29.3452  Overweight  Indo-European
201868590193_R02C01   87.5433    1.6764   31.1507       Obese          Mixed
                       Ethnic_self      smoker       Array       Slide
                       <character> <character> <character>   <numeric>
201868500150_R01C01       Hispanic          No      R01C01 2.01869e+11
201868500150_R03C01      Caucasian          No      R03C01 2.01869e+11
201868500150_R05C01        Persian          No      R05C01 2.01869e+11
201868500150_R07C01      Caucasian          No      R07C01 2.01869e+11
201868500150_R08C01      Caucasian          No      R08C01 2.01869e+11
201868590193_R02C01 Finnish/Creole          No      R02C01 2.01869e+11
~~~
{: .output}



~~~
methyl_mat <- t(assay(methylation))
## calculate correlations between cells in matrix
cor_mat <- cor(methyl_mat)
~~~
{: .language-r}


~~~
View(cor_mat[1:100,])
~~~
{: .language-r}

The `assay` function creates a matrix-like object where rows represent probes
for genes and columns represent samples. We calculate correlations between
features in the `methylation` dataset and examine the first 100 cells of this
matrix. The size of the dataset makes it difficult to examine in full, a
common challenge in analysing high-dimensional genomics data. 


# Further reading

- Buhlman, P. & van de Geer, S. (2011) Statistics for High-Dimensional Data. Springer, London.
- [Buhlman, P., Kalisch, M. & Meier, L. (2014) High-dimensional statistics with a view toward applications in biology. Annual Review of Statistics and Its Application](https://doi.org/10.1146/annurev-statistics-022513-115545).
- Johnstone, I.M. & Titterington, D.M. (2009) Statistical challenges of high-dimensional data. Philosophical Transactions of the Royal Society A 367:4237-4253.
- [Bioconductor ethylation array analysis vignette](https://www.bioconductor.org/packages/release/workflows/vignettes/methylationArrayAnalysis/inst/doc/methylationArrayAnalysis.html).

{% include links.md %}