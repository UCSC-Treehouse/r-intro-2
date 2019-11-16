# Summarizing data



Having loaded and thoroughly explored a data set, we are ready to distill it down to concise conclusions. At its simplest, this involves calculating summary statistics like counts, means, and standard deviations. Beyond this is the fitting of models, and hypothesis testing and confidence interval calculation. R has a huge number of packages devoted to these tasks and this is a large part of its appeal, but is beyond the scope of today.

Loading the data as before, if you have not already done so:


```r
library(tidyverse)

sample_metadata <- read_tsv("selected_metadata.tsv")

expression_values <- read_tsv("one_gene_expression.tsv") 

expression_by_disease <- left_join(expression_values, sample_metadata, by=c("sample")) 
```


## Summary functions

R has a variety of functions for summarizing a vector, including: `sum`, `mean`, `min`, `max`, `median`, `sd`.


```r
mean( c(1,2,3,4) )
```

```
## [1] 2.5
```

We can use these on the expression data.


```r
expression_in_AML <- filter(expression_by_disease, disease == "acute myeloid leukemia")
sum(expression_in_AML$age_at_dx)
```

```
## [1] NA
```

```r
mean(expression_in_AML$age_at_dx)
```

```
## [1] NA
```
## Missing values

Why did `mean` fail? The reason is that `life_exp` contains missing values (`NA`).


```r
expression_in_AML$age_at_dx
```

```
##  [1] 61.00000       NA       NA  4.60000  0.72000  4.90000 21.00000
##  [8] 76.00000       NA  7.82000  0.40000  0.44000       NA 55.00000
## [15] 73.00000 12.91667 11.23000  8.46000 18.96000 47.00000  4.00000
## [22]  4.00000 14.00000
```

R will not ignore these unless we explicitly tell it to with `na.rm=TRUE`.


```r
mean(expression_in_AML$age_at_dx, na.rm=TRUE)
```

```
## [1] 22.39193
```

`NA` is a special value. If we try to calculate with `NA`, the result is `NA`


```r
NA + 1
```

```
## [1] NA
```

`is.na` can be used to detect `NA` values, or `na.omit` can be used to directly remove rows of a data frame containing them.


```r
is.na( c(1,2,NA,3) )
```

```
## [1] FALSE FALSE  TRUE FALSE
```

```r
cleaned <- filter(expression_in_AML, !is.na(age_at_dx))
mean(cleaned$age_at_dx)
```

```
## [1] 22.39193
```

## Grouped summaries

The `summarize` function in `dplyr` allows summary functions to be applied to data frames.


```r
summarize(expression_by_disease, mean_age_at_dx = mean(age_at_dx, na.rm=TRUE))
```

```
## # A tibble: 1 x 1
##   mean_age_at_dx
##            <dbl>
## 1           13.1
```

So far unremarkable, but `summarize` comes into its own when the `group_by` "adjective" is used.


```r
summarize(
    group_by(expression_by_disease, disease), 
    mean_age_at_dx=mean(age_at_dx,na.rm=TRUE))
```

```
## # A tibble: 36 x 2
##    disease                             mean_age_at_dx
##    <chr>                                        <dbl>
##  1 acute leukemia of ambiguous lineage           11  
##  2 acute lymphoblastic leukemia                   6.8
##  3 acute myeloid leukemia                        22.4
##  4 adrenocortical carcinoma                      12  
##  5 alveolar rhabdomyosarcoma                     17  
##  6 atypical teratoid/rhabdoid tumor               1.4
##  7 colon adenocarcinoma                          24  
##  8 desmoplastic small round cell tumor           18.5
##  9 embryonal rhabdomyosarcoma                    16  
## 10 ependymoma                                     6.7
## # … with 26 more rows
```


### {-}

`group_by` can be used to group by multiple columns, much like `count`. We can use this to see how the rest of the world is catching up to OECD nations in terms of life expectancy.


```r
result <- summarize(
    group_by(expression_by_disease, disease, gender), 
    mean_age_at_dx=mean(age_at_dx,na.rm=TRUE))

result
```

```
## # A tibble: 52 x 3
## # Groups:   disease [36]
##    disease                             gender       mean_age_at_dx
##    <chr>                               <chr>                 <dbl>
##  1 acute leukemia of ambiguous lineage female                 11  
##  2 acute lymphoblastic leukemia        female                NaN  
##  3 acute lymphoblastic leukemia        male                    6.8
##  4 acute myeloid leukemia              female                 24.7
##  5 acute myeloid leukemia              male                   18.4
##  6 adrenocortical carcinoma            not reported           12  
##  7 alveolar rhabdomyosarcoma           female                 17  
##  8 atypical teratoid/rhabdoid tumor    male                    1.4
##  9 colon adenocarcinoma                female                 24  
## 10 desmoplastic small round cell tumor male                   18.5
## # … with 42 more rows
```


## t-test

We will finish this section by demonstrating a t-test. The main point of this section is to give a flavour of how statistical tests work in R, rather than the details of what a t-test does.

does age of diagnosis differ in males and females with AML?


```r
expr_male <- filter(expression_in_AML, gender == "male")
expr_female <- filter(expression_in_AML, gender == "female")

t.test(expr_male$age_at_dx, expr_female$age_at_dx)
```

```
## 
## 	Welch Two Sample t-test
## 
## data:  expr_male$age_at_dx and expr_female$age_at_dx
## t = -0.52009, df = 13.552, p-value = 0.6114
## alternative hypothesis: true difference in means is not equal to 0
## 95 percent confidence interval:
##  -32.81913  20.04079
## sample estimates:
## mean of x mean of y 
##  18.35667  24.74583
```

Statistical routines often have many ways to tweak the details of their operation. These are specified by further arguments to the function call, to override the default behaviour. By default, `t.test` performs an unpaired t-test. If this was a paired data, we could specify `paired=TRUE` to `t.test` to perform a paired sample t-test and gain some statistical power. Check this by looking at the help page with `?t.test`.

When performing a statistical test, it's good practice to visualize the data to make sure there is nothing funny going on.


```r
ggplot(expression_in_AML, aes(x=gender, y=age_at_dx)) + 
  geom_boxplot()
```

```
## Warning: Removed 4 rows containing non-finite values (stat_boxplot).
```

<img src="summarizing_files/figure-html/unnamed-chunk-13-1.png" width="336" style="display: block; margin: auto;" />

```r
ggplot(expression_in_AML, aes(x=gender, y=age_at_dx)) + 
  geom_jitter()
```

```
## Warning: Removed 4 rows containing missing values (geom_point).
```

<img src="summarizing_files/figure-html/unnamed-chunk-13-2.png" width="336" style="display: block; margin: auto;" />

This is a visual confirmation of the t-test result. If there were no difference between the years then points would lie approximately evenly above and below the diagonal line, which is clearly not the case. However the outlier may warrant investigation.


# Thinking in R

The result of a t-test is actually a value we can manipulate further. Two functions help us here. `class` gives the "public face" of a value, and `typeof` gives its underlying type, the way R thinks of it internally. For example numbers are "numeric" and have some representation in computer memory, either "integer" for whole numbers only, or "double" which can hold fractional numbers (stored in memory in a base-2 version of scientific notation).


```r
class(42)
```

```
## [1] "numeric"
```

```r
typeof(42)
```

```
## [1] "double"
```

Let's look at the result of a t-test:


```r
result <- t.test(expr_male$age_at_dx, expr_female$age_at_dx)

class(result)
```

```
## [1] "htest"
```

```r
typeof(result)
```

```
## [1] "list"
```

```r
names(result)
```

```
##  [1] "statistic"   "parameter"   "p.value"     "conf.int"    "estimate"   
##  [6] "null.value"  "stderr"      "alternative" "method"      "data.name"
```

```r
result$p.value
```

```
## [1] 0.6113911
```

In R, a t-test is just another function returning just another type of data, so it can also be a building block. The value it returns is a special type of vector called a "list", but with a public face that presents itself nicely. This is a common pattern in R. Besides printing to the console nicely, this public face may alter the behaviour of generic functions such as `plot` and `summary`.

Similarly a data frame is a list of vectors that is able to present itself nicely.

## Lists

Lists are vectors that can hold anything as elements (even other lists!). It's possible to create lists with the `list` function. This becomes especially useful once you get into the programming side of R. For example writing your own function that needs to return multiple values, it could do so in the form of a list.


```r
mylist <- list(hello=c("Hello","world"), numbers=c(1,2,3,4))
mylist
```

```
## $hello
## [1] "Hello" "world"
## 
## $numbers
## [1] 1 2 3 4
```

```r
class(mylist)
```

```
## [1] "list"
```

```r
typeof(mylist)
```

```
## [1] "list"
```

```r
names(mylist)
```

```
## [1] "hello"   "numbers"
```

Accessing lists can be done by name with `$` or by position with `[[ ]]`. 


```r
mylist$hello
```

```
## [1] "Hello" "world"
```

```r
mylist[[2]]
```

```
## [1] 1 2 3 4
```


## Other types not covered here

Matrices are another tabular data type. These come up when doing more mathematical tasks in R. They are also commonly used in bioinformatics, for example to represent RNA-Seq count data. A matrix, as compared to a data frame:

* contains only one type of data, usually numeric (rather than different types in different columns).
* commonly has `rownames` as well as `colnames`. (Base R data frames can have `rownames` too, but it is easier to have any unique identifier as a normal column instead.)
* has individual cells as the unit of observation (rather than rows).

Matrices can be created using `as.matrix` from a data frame, `matrix` from a single vector, or using `rbind` or `cbind` with several vectors.

You may also encounter "S4 objects", especially if you use [Bioconductor](http://bioconductor.org/) packages. The syntax for using these is different again, and uses `@` to access elements.


## Programming

Once you have a useful data analysis, you may want to do it again with different data. You may have some task that needs to be done many times over. This is where programming comes in:

* Writing your own [functions](http://r4ds.had.co.nz/functions.html).
* [For-loops](http://r4ds.had.co.nz/iteration.html) to do things multiple times.
* [If-statements](http://r4ds.had.co.nz/functions.html#conditional-execution) to make decisions.

The ["R for Data Science" book](http://r4ds.had.co.nz/) is an excellent source to learn more. Monash Data Fluency ["Programming and Tidy data analysis in R" course](https://monashdatafluency.github.io/r-progtidy/) also covers this.









