# Data frames



*Data frame* is R's name for tabular data. We generally want each row in a data frame to represent a unit of observation, and each column to contain a different type of information about the units of observation. Tabular data in this form is called ["tidy data"](http://vita.had.co.nz/papers/tidy-data.html).

Today we will be using a collection of modern packages collectively known as the [Tidyverse](https://www.tidyverse.org/). R and its predecessor S have a history dating back to 1976. The Tidyverse fixes some dubious design decisions baked into "base R", including having its own slightly improved form of data frame, which is called a *tibble*. Sticking to the Tidyverse where possible is generally safer, Tidyverse packages are more willing to generate errors rather than ignore problems.


## Setting up

Our first step is to download the files we need and to install the Tidyverse. This is the one step where we ask you to copy and paste some code:



If using RStudio Cloud, you might need to switch to R version 3.5.3 to successfully install Tidyverse. Use the drop-down in the top right corner of the page.

People also sometimes have problems installing all the packages in Tidyverse on Windows machines. If you run into problems you may have more success installing individual packages.


```r
install.packages(c("dplyr","readr","tidyr","ggplot2"))
```

We need to load the `tidyverse` package in order to use it.


```r
library(tidyverse)

# OR
library(dplyr)
library(readr)
library(tidyr)
library(ggplot2)
```

The `tidyverse` package loads various other packages, setting up a modern R environment. In this section we will be using functions from the `dplyr`, `readr` and `tidyr` packages.

R is a language with mini-languages within it that solve specific problem domains. `dplyr` is such a mini-language, a set of "verbs" (functions) that work well together. `dplyr`, with the help of `tidyr` for some more complex operations, provides a way to perform most manipulations on a data frame that you might need.


## Loading data

We will use the `read_tsv` function from `readr` to load a data set. (See also `read.csv` in base R.) TSV stands for Tab Separated Values, and is a text format used to store tabular data. The first few lines of the file we are loading are shown below. Conventionally the first line contains column headings.

```
th_sampleid    disease                             age_at_dx
TH03_0010_S01  acute leukemia of ambiguous lineage        11
TH03_0010_S02  acute leukemia of ambiguous lineage        11
THR33_1000_S01 medulloblastoma                             7
THR33_1001_S01 medulloblastoma                             5
THR33_1002_S01 medulloblastoma                             5
THR33_1003_S01 medulloblastoma                             3
```


```r
sample_metadata <- read_tsv("clinical_TumorCompendium_v10_PolyA_2019-07-25_clean.tsv")
```

```
## Parsed with column specification:
## cols(
##   sample = col_character(),
##   disease = col_character(),
##   age_at_dx = col_double(),
##   pedaya = col_character(),
##   gender = col_character()
## )
```

```r
# downloaded from "https://xena.treehouse.gi.ucsc.edu/download/clinical_TumorCompendium_v10_PolyA_2019-07-25.tsv"
```

`read_tsv` has guessed the type of data each column holds:

* `<chr>` - character strings
* `<dbl>` - numerical values. Technically these are "doubles", which is a way of storing numbers with 15 digits precision.

We will also encounter:

* `<lgl>` - logical values, `TRUE` or `FALSE`.
* `<int>` - integers, a fancy name for whole numbers.
* `<fct>` - factors, categorical data. We will get to this shortly.


You can also see this data frame referring to itself as "a tibble". This is the Tidyverse's improved form of data frame. Tibbles present themselves more conveniently than base R data frames. Base R data frames don't show the type of each column, and output every row when you try to view them.


### Tip {- .tip}

A data frame can also be created from vectors, with the `tibble` function. (See also `data.frame` in base R.) For example:


```r
tibble(foo=c(10,20,30), bar=c("a","b","c"))
```

```
## # A tibble: 3 x 2
##     foo bar  
##   <dbl> <chr>
## 1    10 a    
## 2    20 b    
## 3    30 c
```

The argument names become column names in the data frame.

### Tip {- .tip}

The *path* to the file on our server is `"download/clinical_TumorCompendium_v10_PolyA_2019-07-25.tsv"`. This says, starting from your working directory, look in the directory `download` for the file `clinical_TumorCompendium_v10_PolyA_2019-07-25.tsv`. The steps in the path are separated by `/`. Your working directory is shown at the top of the console pane. The path needed might be different on your own computer, depending where you downloaded the file.

One way to work out the correct path is to find the file in the file browser pane, click on it and select "Import Dataset...".

<img src="figures/import.png" width="50%" style="display: block; margin: auto auto auto 0;" />


## Exploring

The `View` function gives us a spreadsheet-like view of the data frame.

```
View(sample_metadata)
```

`print` with the `n` argument can be used to show more than the first 10 rows on the console.


```r
print(sample_metadata, n=200)
```

We can extract details of the data frame with further functions:


```r
nrow(sample_metadata)
```

```
## [1] 11610
```

```r
ncol(sample_metadata)
```

```
## [1] 5
```

```r
colnames(sample_metadata)
```

```
## [1] "sample"    "disease"   "age_at_dx" "pedaya"    "gender"
```

```r
summary(sample_metadata)
```

```
##     sample            disease            age_at_dx       pedaya         
##  Length:11610       Length:11610       Min.   : 0.0   Length:11610      
##  Class :character   Class :character   1st Qu.:39.0   Class :character  
##  Mode  :character   Mode  :character   Median :57.0   Mode  :character  
##                                        Mean   :50.7                     
##                                        3rd Qu.:68.0                     
##                                        Max.   :90.0                     
##     gender         
##  Length:11610      
##  Class :character  
##  Mode  :character  
##                    
##                    
## 
```


## Indexing data frames

Data frames can be subset using `[row,column]` syntax.


```r
sample_metadata[4,2]
```

```
## # A tibble: 1 x 1
##   disease        
##   <chr>          
## 1 medulloblastoma
```

Note that while this is a single value, it is still wrapped in a data frame. (This is a behaviour specific to Tidyverse data frames.) More on this in a moment.

Columns can be given by name.


```r
sample_metadata[4,"disease"]
```

```
## # A tibble: 1 x 1
##   disease        
##   <chr>          
## 1 medulloblastoma
```

The column or row may be omitted, thereby retrieving the entire row or column.


```r
sample_metadata[4,]
```

```
## # A tibble: 1 x 5
##   sample         disease         age_at_dx pedaya              gender
##   <chr>          <chr>               <dbl> <chr>               <chr> 
## 1 THR33_1001_S01 medulloblastoma         5 Yes, age < 30 years male
```

```r
sample_metadata[,"disease"]
```

```
## # A tibble: 11,610 x 1
##    disease                            
##    <chr>                              
##  1 acute leukemia of ambiguous lineage
##  2 acute leukemia of ambiguous lineage
##  3 medulloblastoma                    
##  4 medulloblastoma                    
##  5 medulloblastoma                    
##  6 medulloblastoma                    
##  7 medulloblastoma                    
##  8 medulloblastoma                    
##  9 medulloblastoma                    
## 10 medulloblastoma                    
## # … with 11,600 more rows
```

Multiple rows or columns may be retrieved using a vector.


```r
rows_wanted <- c(1,3,5)
sample_metadata[rows_wanted,]
```

```
## # A tibble: 3 x 5
##   sample       disease                     age_at_dx pedaya          gender
##   <chr>        <chr>                           <dbl> <chr>           <chr> 
## 1 TH03_0010_S… acute leukemia of ambiguou…        11 Yes, age < 30 … female
## 2 THR33_1000_… medulloblastoma                     7 Yes, age < 30 … female
## 3 THR33_1002_… medulloblastoma                     5 Yes, age < 30 … female
```

Vector indexing can also be written on a single line.


```r
sample_metadata[c(1,3,5),]
```

```
## # A tibble: 3 x 5
##   sample       disease                     age_at_dx pedaya          gender
##   <chr>        <chr>                           <dbl> <chr>           <chr> 
## 1 TH03_0010_S… acute leukemia of ambiguou…        11 Yes, age < 30 … female
## 2 THR33_1000_… medulloblastoma                     7 Yes, age < 30 … female
## 3 THR33_1002_… medulloblastoma                     5 Yes, age < 30 … female
```

```r
sample_metadata[1:7,]
```

```
## # A tibble: 7 x 5
##   sample       disease                     age_at_dx pedaya          gender
##   <chr>        <chr>                           <dbl> <chr>           <chr> 
## 1 TH03_0010_S… acute leukemia of ambiguou…        11 Yes, age < 30 … female
## 2 TH03_0010_S… acute leukemia of ambiguou…        11 Yes, age < 30 … female
## 3 THR33_1000_… medulloblastoma                     7 Yes, age < 30 … female
## 4 THR33_1001_… medulloblastoma                     5 Yes, age < 30 … male  
## 5 THR33_1002_… medulloblastoma                     5 Yes, age < 30 … female
## 6 THR33_1003_… medulloblastoma                     3 Yes, age < 30 … female
## 7 THR33_1004_… medulloblastoma                    26 Yes, age < 30 … male
```


## Columns are vectors

Ok, so how do we actually get data out of a data frame?

Under the hood, a data frame is a list of column vectors. We can use `$` to retrieve columns. Occasionally it is also useful to use `[[ ]]` to retrieve columns, for example if the column name we want is stored in a variable.


```r
head( sample_metadata$disease )
```

```
## [1] "acute leukemia of ambiguous lineage"
## [2] "acute leukemia of ambiguous lineage"
## [3] "medulloblastoma"                    
## [4] "medulloblastoma"                    
## [5] "medulloblastoma"                    
## [6] "medulloblastoma"
```

```r
head( sample_metadata[["disease"]] )
```

```
## [1] "acute leukemia of ambiguous lineage"
## [2] "acute leukemia of ambiguous lineage"
## [3] "medulloblastoma"                    
## [4] "medulloblastoma"                    
## [5] "medulloblastoma"                    
## [6] "medulloblastoma"
```

To get the "disease" value of the 4th row as above, but unwrapped, we can use:


```r
sample_metadata$disease[4]
```

```
## [1] "medulloblastoma"
```


## Logical indexing

A method of indexing that we haven't discussed yet is logical indexing. Instead of specifying the row number or numbers that we want, we can give a logical vector which is `TRUE` for the rows we want and `FALSE` otherwise. This can also be used with vectors.

We will first do this in a slightly verbose way in order to understand it, then learn a more concise way to do this using the `dplyr` package.


```r
is_glioma <- sample_metadata$disease == "glioma"

head(is_glioma)
```

```
## [1] FALSE FALSE FALSE FALSE FALSE FALSE
```

```r
sum(is_glioma)
```

```
## [1] 702
```

`sum` treats TRUE as 1 and FALSE as 0, so it tells us the number of TRUE elements in the vector.

We can use this logical vector to get the glioma samples from `sample_metadata`:


```r
sample_metadata[is_glioma,]
```

```
## # A tibble: 702 x 5
##    sample         disease age_at_dx pedaya              gender      
##    <chr>          <chr>       <dbl> <chr>               <chr>       
##  1 TH03_0117_S01  glioma       0.9  Yes, age < 30 years not reported
##  2 TH06_1175_S01  glioma      17    Yes, age < 30 years female      
##  3 TH38_1311_S02  glioma      13    Yes, age < 30 years male        
##  4 TH38_1346_S02  glioma      11    Yes, age < 30 years female      
##  5 TH34_1445_S02  glioma       7    Yes, age < 30 years female      
##  6 THR24_1924_S01 glioma       5.33 Yes, age < 30 years female      
##  7 THR24_1925_S01 glioma      15.0  Yes, age < 30 years female      
##  8 THR24_1925_S02 glioma      15.0  Yes, age < 30 years female      
##  9 THR24_1926_S01 glioma      13.1  Yes, age < 30 years male        
## 10 THR24_1926_S02 glioma      13.1  Yes, age < 30 years male        
## # … with 692 more rows
```

Comparison operators available are:

* `x == y ` -- "equal to"
* `x != y ` -- "not equal to"
* `x < y  ` -- "less than"
* `x > y  ` -- "greater than"
* `x <= y ` -- "less than or equal to"
* `x >= y ` -- "greater than or equal to"

More complicated conditions can be constructed using logical operators:

* `a & b ` -- "and", TRUE only if both `a` and `b` are TRUE.
* `a | b ` -- "or", TRUE if either `a` or `b` or both are TRUE.
* `! a   ` -- "not" , TRUE if `a` is FALSE, and FALSE if `a` is TRUE.

The `age_at_dx` column of `sample_metadata` tells the age at which the person was diagnosed. We could see which gliomas occured in people over 65 with:


```r
senior_gliomas <- is_glioma & sample_metadata$age_at_dx >65

sample_metadata[senior_gliomas,]
```

```
## # A tibble: 33 x 5
##    sample          disease age_at_dx pedaya gender
##    <chr>           <chr>       <dbl> <chr>  <chr> 
##  1 TCGA-CS-4941-01 glioma         67 No     male  
##  2 TCGA-DB-A64L-01 glioma         67 No     female
##  3 TCGA-DH-A669-01 glioma         70 No     male  
##  4 TCGA-DH-A669-02 glioma         70 No     male  
##  5 TCGA-DU-6393-01 glioma         66 No     male  
##  6 TCGA-DU-6400-01 glioma         66 No     female
##  7 TCGA-DU-7012-01 glioma         74 No     female
##  8 TCGA-DU-7292-01 glioma         69 No     male  
##  9 TCGA-DU-7306-01 glioma         67 No     male  
## 10 TCGA-DU-8167-01 glioma         69 No     female
## # … with 23 more rows
```

`senior_gliomas` seems like it should be kept within our `sample_metadata` data frame for future use. We can add it as a new column of the data frame with:


```r
sample_metadata$senior_glioma <- senior_gliomas

sample_metadata
```

```
## # A tibble: 11,610 x 6
##    sample     disease            age_at_dx pedaya      gender senior_glioma
##    <chr>      <chr>                  <dbl> <chr>       <chr>  <lgl>        
##  1 TH03_0010… acute leukemia of…        11 Yes, age <… female FALSE        
##  2 TH03_0010… acute leukemia of…        11 Yes, age <… female FALSE        
##  3 THR33_100… medulloblastoma            7 Yes, age <… female FALSE        
##  4 THR33_100… medulloblastoma            5 Yes, age <… male   FALSE        
##  5 THR33_100… medulloblastoma            5 Yes, age <… female FALSE        
##  6 THR33_100… medulloblastoma            3 Yes, age <… female FALSE        
##  7 THR33_100… medulloblastoma           26 Yes, age <… male   FALSE        
##  8 THR33_100… medulloblastoma           10 Yes, age <… male   FALSE        
##  9 THR33_100… medulloblastoma            3 Yes, age <… male   FALSE        
## 10 THR33_100… medulloblastoma           27 Yes, age <… male   FALSE        
## # … with 11,600 more rows
```


### Challenge: logical indexing {- .challenge}


1. Which samples are gliomas from seniors (over 65)?

2. Which samples are in neither glioma nor from seniors?

2. How many samples are from seniors?


### A `dplyr` shorthand

The above method is a little laborious. We have to keep mentioning the name of the data frame, and there is a lot of punctuation to keep track of. `dplyr` provides a slightly magical function called `filter` which lets us write more concisely. For example:


```r
filter(sample_metadata, sample_metadata$age_at_dx >65 & disease == "glioma")
```

```
## # A tibble: 33 x 6
##    sample          disease age_at_dx pedaya gender senior_glioma
##    <chr>           <chr>       <dbl> <chr>  <chr>  <lgl>        
##  1 TCGA-CS-4941-01 glioma         67 No     male   TRUE         
##  2 TCGA-DB-A64L-01 glioma         67 No     female TRUE         
##  3 TCGA-DH-A669-01 glioma         70 No     male   TRUE         
##  4 TCGA-DH-A669-02 glioma         70 No     male   TRUE         
##  5 TCGA-DU-6393-01 glioma         66 No     male   TRUE         
##  6 TCGA-DU-6400-01 glioma         66 No     female TRUE         
##  7 TCGA-DU-7012-01 glioma         74 No     female TRUE         
##  8 TCGA-DU-7292-01 glioma         69 No     male   TRUE         
##  9 TCGA-DU-7306-01 glioma         67 No     male   TRUE         
## 10 TCGA-DU-8167-01 glioma         69 No     female TRUE         
## # … with 23 more rows
```

In the second argument, we are able to refer to columns of the data frame as though they were variables. The code is beautiful, but also opaque. It's important to understand that under the hood we are creating and combining logical vectors.



## Factors

The `count` function from `dplyr` can help us understand the contents of some of the columns in `sample_metadata`. `count` is also *magical*, we can refer to columns of the data frame directly in the arguments to `count`.


```r
count(sample_metadata, pedaya)
```

```
## # A tibble: 3 x 2
##   pedaya                  n
##   <chr>               <int>
## 1 No                   9408
## 2 Unknown                 1
## 3 Yes, age < 30 years  2201
```

```r
count(sample_metadata, senior_glioma)
```

```
## # A tibble: 2 x 2
##   senior_glioma     n
##   <lgl>         <int>
## 1 FALSE         11577
## 2 TRUE             33
```

One annoyance here is that the different categories in `pedaya` aren't in a sensible order. This comes up quite often, for example when sorting or plotting categorical data. R's solution is a further type of vector called a *factor* (think a factor of an experimental design). A factor holds categorical data, and has an associated ordered set of *levels*. It is otherwise quite similar to a character vector.

Any sort of vector can be converted to a factor using the `factor` function. This function defaults to placing the levels in alphabetical order, but takes a `levels` argument that can override this.


```r
head( factor(sample_metadata$pedaya, levels=c("Yes, age < 30 years","No","Unknown")) )
```

```
## [1] Yes, age < 30 years Yes, age < 30 years Yes, age < 30 years
## [4] Yes, age < 30 years Yes, age < 30 years Yes, age < 30 years
## Levels: Yes, age < 30 years No Unknown
```

We should modify the `pedaya` column of the `sample_metadata` table in order to use this:


```r
sample_metadata$pedaya <- factor(sample_metadata$pedaya,
                                 c("Yes, age < 30 years","No","Unknown"))
```

`count` now produces the desired order of output:


```r
count(sample_metadata, pedaya)
```

```
## # A tibble: 3 x 2
##   pedaya                  n
##   <fct>               <int>
## 1 Yes, age < 30 years  2201
## 2 No                   9408
## 3 Unknown                 1
```


We can count two categorical columns at once.


```r
count(sample_metadata, pedaya, senior_glioma)
```

```
## # A tibble: 4 x 3
##   pedaya              senior_glioma     n
##   <fct>               <lgl>         <int>
## 1 Yes, age < 30 years FALSE          2201
## 2 No                  FALSE          9375
## 3 No                  TRUE             33
## 4 Unknown             FALSE             1
```


### Challenge: counting {- .challenge}

Investigate how many acute myeloid leukemia samples come from adult or pediatric individuals using `count`


## Sorting

Data frames can be sorted using the `arrange` function in `dplyr`.


```r
arrange(sample_metadata, age_at_dx)
```

```
## # A tibble: 11,610 x 6
##    sample     disease            age_at_dx pedaya      gender senior_glioma
##    <chr>      <chr>                  <dbl> <fct>       <chr>  <lgl>        
##  1 THR08_016… acute lymphoblast…         0 Yes, age <… unkno… FALSE        
##  2 THR08_016… acute lymphoblast…         0 Yes, age <… unkno… FALSE        
##  3 TH06_0613… atypical teratoid…         0 Yes, age <… male   FALSE        
##  4 TH06_0613… atypical teratoid…         0 Yes, age <… male   FALSE        
##  5 TH06_0627… glioblastoma mult…         0 Yes, age <… female FALSE        
##  6 THR29_074… rhabdomyosarcoma           0 Yes, age <… female FALSE        
##  7 THR29_074… embryonal rhabdom…         0 Yes, age <… male   FALSE        
##  8 THR29_075… alveolar rhabdomy…         0 Yes, age <… male   FALSE        
##  9 THR29_075… alveolar rhabdomy…         0 Yes, age <… male   FALSE        
## 10 THR29_078… embryonal rhabdom…         0 Yes, age <… female FALSE        
## # … with 11,600 more rows
```

Numeric columns are sorted in numeric order. Character columns will be sorted in alphabetical order. Factor columns are sorted in order of their levels. The `desc` helper function can be used to sort in descending order.


```r
arrange(sample_metadata, desc(disease))
```

```
## # A tibble: 11,610 x 6
##    sample          disease    age_at_dx pedaya         gender senior_glioma
##    <chr>           <chr>          <dbl> <fct>          <chr>  <lgl>        
##  1 TH03_0012_S01   wilms tum…     18    Yes, age < 30… male   FALSE        
##  2 TH03_0012_S02   wilms tum…     18    Yes, age < 30… male   FALSE        
##  3 TH03_0144_S01   wilms tum…      6    Yes, age < 30… male   FALSE        
##  4 TH27_2189_S01   wilms tum…      5    Yes, age < 30… female FALSE        
##  5 TH06_0632_S01   wilms tum…      4    Yes, age < 30… female FALSE        
##  6 TARGET-50-CAAA… wilms tum…      6.15 Yes, age < 30… female FALSE        
##  7 TARGET-50-CAAA… wilms tum…      4.68 Yes, age < 30… female FALSE        
##  8 TARGET-50-CAAA… wilms tum…      1.1  Yes, age < 30… female FALSE        
##  9 TARGET-50-CAAA… wilms tum…      4.38 Yes, age < 30… female FALSE        
## 10 TARGET-50-CAAA… wilms tum…      7.53 Yes, age < 30… female FALSE        
## # … with 11,600 more rows
```


## Joining data frames

Let's move on to a larger data set. These are some gene expression values. 


```r
expression_values <- read_tsv("expression_data_for_MAP2K1_HRAS_v9_.tsv") %>%
  gather(sample, expression, -Gene)
expression_values
```

```
## # A tibble: 22,908 x 3
##    Gene   sample         expression
##    <chr>  <chr>               <dbl>
##  1 HRAS   THR15_0330_S01       5.77
##  2 MAP2K1 THR15_0330_S01       4.53
##  3 HRAS   THR29_0776_S01       6.99
##  4 MAP2K1 THR29_0776_S01       4.51
##  5 HRAS   THR11_0247_S01       6.10
##  6 MAP2K1 THR11_0247_S01       5.75
##  7 HRAS   THR08_0162_S01       4.48
##  8 MAP2K1 THR08_0162_S01       6.42
##  9 HRAS   TH27_1241_S01        6.41
## 10 MAP2K1 TH27_1241_S01        5.39
## # … with 22,898 more rows
```

### Quiz {.challenge -}

What is the unit of observation in this new data frame?

### {-}

It would be useful to have general information about samples from `sample_metadata` available as columns when we use this data frame. `expression_values` and `sample_metadata` share a column called `sample` which can be used to match rows from one to the other. 


```r
expression_by_disease <- left_join(expression_values, sample_metadata, by=c("sample"))
expression_by_disease
```

```
## # A tibble: 22,908 x 8
##    Gene   sample  expression disease  age_at_dx pedaya gender senior_glioma
##    <chr>  <chr>        <dbl> <chr>        <dbl> <fct>  <chr>  <lgl>        
##  1 HRAS   THR15_…       5.77 <NA>          NA   <NA>   <NA>   NA           
##  2 MAP2K1 THR15_…       4.53 <NA>          NA   <NA>   <NA>   NA           
##  3 HRAS   THR29_…       6.99 rhabdom…      20   Yes, … male   FALSE        
##  4 MAP2K1 THR29_…       4.51 rhabdom…      20   Yes, … male   FALSE        
##  5 HRAS   THR11_…       6.10 glioma        13   Yes, … male   FALSE        
##  6 MAP2K1 THR11_…       5.75 glioma        13   Yes, … male   FALSE        
##  7 HRAS   THR08_…       4.48 acute l…       0.6 Yes, … unkno… FALSE        
##  8 MAP2K1 THR08_…       6.42 acute l…       0.6 Yes, … unkno… FALSE        
##  9 HRAS   TH27_1…       6.41 <NA>          NA   <NA>   <NA>   NA           
## 10 MAP2K1 TH27_1…       5.39 <NA>          NA   <NA>   <NA>   NA           
## # … with 22,898 more rows
```

The "left" in "left join" refers to how rows that can't be paired up are handled. `left_join` keeps all rows from the first data frame but not the second. This is a good default when the intent is to attaching some extra information to a data frame. `inner_join` discard all rows that can't be paired up. `full_join` keeps all rows from both data frames. 


## Further reading

We've covered the fundamentals of dplyr and data frames, but there is much more to learn. Notably, we haven't covered the use of the pipe `%>%` to chain `dplyr` verbs together. The ["R for Data Science" book](http://r4ds.had.co.nz/) is an excellent source to learn more. The Monash Data Fluency ["Programming and Tidy data analysis in R" course](https://monashdatafluency.github.io/r-progtidy/) also covers this. 











