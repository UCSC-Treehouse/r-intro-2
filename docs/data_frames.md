# Data frames



*Data frame* is R's name for tabular data. We generally want each row in a data frame to represent a unit of observation, and each column to contain a different type of information about the units of observation. Tabular data in this form is called ["tidy data"](http://vita.had.co.nz/papers/tidy-data.html).

Today we will be using a collection of modern packages collectively known as the [Tidyverse](https://www.tidyverse.org/). R and its predecessor S have a history dating back to 1976. The Tidyverse fixes some dubious design decisions baked into "base R", including having its own slightly improved form of data frame, which is called a *tibble*. Sticking to the Tidyverse where possible is generally safer, Tidyverse packages are more willing to generate errors rather than ignore problems.


## Setting up

Our first step is to download the files we need and to install the Tidyverse. This is the one step where we ask you to copy and paste some code:


```r
# Download files for this workshop
download.file(
  "https://monashdatafluency.github.io/r-intro-2/r-intro-2-files.zip",
  destfile="r-intro-2-files.zip")
unzip("r-intro-2-files.zip")

# Install Tidyverse
install.packages("tidyverse")
```

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

We will use the `read_csv` function from `readr` to load a data set. (See also `read.csv` in base R.) CSV stands for Comma Separated Values, and is a text format used to store tabular data. The first few lines of the file we are loading are shown below. Conventionally the first line contains column headings.

```
name,region,oecd,g77,lat,long,income2017
Afghanistan,asia,FALSE,TRUE,33,66,low
Albania,europe,FALSE,FALSE,41,20,upper_mid
Algeria,africa,FALSE,TRUE,28,3,upper_mid
Andorra,europe,FALSE,FALSE,42.50779,1.52109,high
Angola,africa,FALSE,TRUE,-12.5,18.5,lower_mid
```


```r
sample_metadata <- read_tsv("https://xena.treehouse.gi.ucsc.edu/download/clinical_TumorCompendium_v10_PolyA_2019-07-25.tsv")
```

```
## Parsed with column specification:
## cols(
##   th_sampleid = col_character(),
##   disease = col_character(),
##   age_at_dx = col_double(),
##   pedaya = col_character(),
##   gender = col_character(),
##   site_id = col_character(),
##   site_donor_id = col_character(),
##   site_sampleid = col_character()
## )
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
## [1] 12236
```

```r
ncol(sample_metadata)
```

```
## [1] 8
```

```r
colnames(sample_metadata)
```

```
## [1] "th_sampleid"   "disease"       "age_at_dx"     "pedaya"       
## [5] "gender"        "site_id"       "site_donor_id" "site_sampleid"
```

```r
summary(sample_metadata)
```

```
##  th_sampleid          disease            age_at_dx       pedaya         
##  Length:12236       Length:12236       Min.   : 0.0   Length:12236      
##  Class :character   Class :character   1st Qu.:39.0   Class :character  
##  Mode  :character   Mode  :character   Median :57.0   Mode  :character  
##                                        Mean   :50.7                     
##                                        3rd Qu.:68.0                     
##                                        Max.   :90.0                     
##                                        NA's   :624                      
##     gender            site_id          site_donor_id     
##  Length:12236       Length:12236       Length:12236      
##  Class :character   Class :character   Class :character  
##  Mode  :character   Mode  :character   Mode  :character  
##                                                          
##                                                          
##                                                          
##                                                          
##  site_sampleid     
##  Length:12236      
##  Class :character  
##  Mode  :character  
##                    
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
## # A tibble: 1 x 8
##   th_sampleid disease age_at_dx pedaya gender site_id site_donor_id
##   <chr>       <chr>       <dbl> <chr>  <chr>  <chr>   <chr>        
## 1 THR33_1001… medull…         5 Yes, … male   THR33   N/A          
## # … with 1 more variable: site_sampleid <chr>
```

```r
sample_metadata[,"disease"]
```

```
## # A tibble: 12,236 x 1
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
## # … with 12,226 more rows
```

Multiple rows or columns may be retrieved using a vector.


```r
rows_wanted <- c(1,3,5)
sample_metadata[rows_wanted,]
```

```
## # A tibble: 3 x 8
##   th_sampleid disease age_at_dx pedaya gender site_id site_donor_id
##   <chr>       <chr>       <dbl> <chr>  <chr>  <chr>   <chr>        
## 1 TH03_0010_… acute …        11 Yes, … female TH03    N/A          
## 2 THR33_1000… medull…         7 Yes, … female THR33   N/A          
## 3 THR33_1002… medull…         5 Yes, … female THR33   N/A          
## # … with 1 more variable: site_sampleid <chr>
```

Vector indexing can also be written on a single line.


```r
sample_metadata[c(1,3,5),]
```

```
## # A tibble: 3 x 8
##   th_sampleid disease age_at_dx pedaya gender site_id site_donor_id
##   <chr>       <chr>       <dbl> <chr>  <chr>  <chr>   <chr>        
## 1 TH03_0010_… acute …        11 Yes, … female TH03    N/A          
## 2 THR33_1000… medull…         7 Yes, … female THR33   N/A          
## 3 THR33_1002… medull…         5 Yes, … female THR33   N/A          
## # … with 1 more variable: site_sampleid <chr>
```

```r
sample_metadata[1:7,]
```

```
## # A tibble: 7 x 8
##   th_sampleid disease age_at_dx pedaya gender site_id site_donor_id
##   <chr>       <chr>       <dbl> <chr>  <chr>  <chr>   <chr>        
## 1 TH03_0010_… acute …        11 Yes, … female TH03    N/A          
## 2 TH03_0010_… acute …        11 Yes, … female TH03    N/A          
## 3 THR33_1000… medull…         7 Yes, … female THR33   N/A          
## 4 THR33_1001… medull…         5 Yes, … male   THR33   N/A          
## 5 THR33_1002… medull…         5 Yes, … female THR33   N/A          
## 6 THR33_1003… medull…         3 Yes, … female THR33   N/A          
## 7 THR33_1004… medull…        26 Yes, … male   THR33   N/A          
## # … with 1 more variable: site_sampleid <chr>
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

Southern countries have latitude less than zero.


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
## [1] NA
```

`sum` treats TRUE as 1 and FALSE as 0, so it tells us the number of TRUE elements in the vector.

We can use this logical vector to get the southern countries from `sample_metadata`:


```r
sample_metadata[is_glioma,]
```

```
## # A tibble: 756 x 8
##    th_sampleid disease age_at_dx pedaya gender site_id site_donor_id
##    <chr>       <chr>       <dbl> <chr>  <chr>  <chr>   <chr>        
##  1 TH03_0117_… glioma       0.9  Yes, … not r… TH03    N/A          
##  2 TH06_1175_… glioma      17    Yes, … female TH06    N/A          
##  3 TH27_1241_… glioma      NA    Unkno… unkno… TH27    N/A          
##  4 TH38_1311_… glioma      13    Yes, … male   TH38    N/A          
##  5 TH38_1346_… glioma      11    Yes, … female TH38    N/A          
##  6 TH34_1445_… glioma       7    Yes, … female TH34    N/A          
##  7 <NA>        <NA>        NA    <NA>   <NA>   <NA>    <NA>         
##  8 THR24_1924… glioma       5.33 Yes, … female THR24   SJHGG001     
##  9 THR24_1925… glioma      15.0  Yes, … female THR24   SJHGG002     
## 10 THR24_1925… glioma      15.0  Yes, … female THR24   SJHGG002     
## # … with 746 more rows, and 1 more variable: site_sampleid <chr>
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

The `blash` column of `sample_metadata` tells which countries are in the Organisation for Economic Co-operation and Development, and the `g77` column tells which countries are in the Group of 77 (an alliance of developing nations). We could see which OECD countries are in the southern hemisphere with:


```r
senior_gliomas <- is_glioma & sample_metadata$age_at_dx >65

sample_metadata[senior_gliomas,]
```

```
## # A tibble: 86 x 8
##    th_sampleid disease age_at_dx pedaya gender site_id site_donor_id
##    <chr>       <chr>       <dbl> <chr>  <chr>  <chr>   <chr>        
##  1 <NA>        <NA>           NA <NA>   <NA>   <NA>    <NA>         
##  2 <NA>        <NA>           NA <NA>   <NA>   <NA>    <NA>         
##  3 <NA>        <NA>           NA <NA>   <NA>   <NA>    <NA>         
##  4 <NA>        <NA>           NA <NA>   <NA>   <NA>    <NA>         
##  5 <NA>        <NA>           NA <NA>   <NA>   <NA>    <NA>         
##  6 <NA>        <NA>           NA <NA>   <NA>   <NA>    <NA>         
##  7 <NA>        <NA>           NA <NA>   <NA>   <NA>    <NA>         
##  8 <NA>        <NA>           NA <NA>   <NA>   <NA>    <NA>         
##  9 <NA>        <NA>           NA <NA>   <NA>   <NA>    <NA>         
## 10 <NA>        <NA>           NA <NA>   <NA>   <NA>    <NA>         
## # … with 76 more rows, and 1 more variable: site_sampleid <chr>
```

`senior_gliomas` seems like it should be kept within our `sample_metadata` data frame for future use. We can add it as a new column of the data frame with:


```r
sample_metadata$senior_glioma <- senior_gliomas

sample_metadata
```

```
## # A tibble: 12,236 x 9
##    th_sampleid disease age_at_dx pedaya gender site_id site_donor_id
##    <chr>       <chr>       <dbl> <chr>  <chr>  <chr>   <chr>        
##  1 TH03_0010_… acute …        11 Yes, … female TH03    N/A          
##  2 TH03_0010_… acute …        11 Yes, … female TH03    N/A          
##  3 THR33_1000… medull…         7 Yes, … female THR33   N/A          
##  4 THR33_1001… medull…         5 Yes, … male   THR33   N/A          
##  5 THR33_1002… medull…         5 Yes, … female THR33   N/A          
##  6 THR33_1003… medull…         3 Yes, … female THR33   N/A          
##  7 THR33_1004… medull…        26 Yes, … male   THR33   N/A          
##  8 THR33_1005… medull…        10 Yes, … male   THR33   N/A          
##  9 THR33_1006… medull…         3 Yes, … male   THR33   N/A          
## 10 THR33_1007… medull…        27 Yes, … male   THR33   N/A          
## # … with 12,226 more rows, and 2 more variables: site_sampleid <chr>,
## #   senior_glioma <lgl>
```


### Challenge: logical indexing {- .challenge}


1. Which country is in both the OECD and the G77?

2. Which countries are in neither the OECD nor the G77?

2. Which countries are in the Americas? These have longitudes between -150 and -40. 


### A `dplyr` shorthand

The above method is a little laborious. We have to keep mentioning the name of the data frame, and there is a lot of punctuation to keep track of. `dplyr` provides a slightly magical function called `filter` which lets us write more concisely. For example:


```r
filter(sample_metadata, sample_metadata$age_at_dx >65 & disease == "glioma")
```

```
## # A tibble: 33 x 9
##    th_sampleid disease age_at_dx pedaya gender site_id site_donor_id
##    <chr>       <chr>       <dbl> <chr>  <chr>  <chr>   <chr>        
##  1 TCGA-CS-49… glioma         67 No     male   TCGA    TCGA-CS-4941 
##  2 TCGA-DB-A6… glioma         67 No     female TCGA    TCGA-DB-A64L 
##  3 TCGA-DH-A6… glioma         70 No     male   TCGA    TCGA-DH-A669 
##  4 TCGA-DH-A6… glioma         70 No     male   TCGA    TCGA-DH-A669 
##  5 TCGA-DU-63… glioma         66 No     male   TCGA    TCGA-DU-6393 
##  6 TCGA-DU-64… glioma         66 No     female TCGA    TCGA-DU-6400 
##  7 TCGA-DU-70… glioma         74 No     female TCGA    TCGA-DU-7012 
##  8 TCGA-DU-72… glioma         69 No     male   TCGA    TCGA-DU-7292 
##  9 TCGA-DU-73… glioma         67 No     male   TCGA    TCGA-DU-7306 
## 10 TCGA-DU-81… glioma         69 No     female TCGA    TCGA-DU-8167 
## # … with 23 more rows, and 2 more variables: site_sampleid <chr>,
## #   senior_glioma <lgl>
```

In the second argument, we are able to refer to columns of the data frame as though they were variables. The code is beautiful, but also opaque. It's important to understand that under the hood we are creating and combining logical vectors.



## Factors

The `count` function from `dplyr` can help us understand the contents of some of the columns in `geo`. `count` is also *magical*, we can refer to columns of the data frame directly in the arguments to `count`.


```r
count(sample_metadata, disease)
```

```
## # A tibble: 103 x 2
##    disease                                 n
##    <chr>                               <int>
##  1 acinar cell carcinoma                   1
##  2 acute leukemia                          7
##  3 acute leukemia of ambiguous lineage     2
##  4 acute lymphoblastic leukemia          549
##  5 acute megakaryoblastic leukemia        92
##  6 acute myeloid leukemia                463
##  7 acute undifferentiated leukemia         1
##  8 adrenocortical adenoma                  1
##  9 adrenocortical cancer                  78
## 10 adrenocortical carcinoma               18
## # … with 93 more rows
```

```r
count(sample_metadata, pedaya)
```

```
## # A tibble: 4 x 2
##   pedaya                  n
##   <chr>               <int>
## 1 No                   9426
## 2 Unknown                82
## 3 Yes, age < 30 years  2702
## 4 <NA>                   26
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

We should modify the `income2017` column of the `geo` table in order to use this:


```r
sample_metadata$pedaya <- factor(sample_metadata$pedaya,
                                 c("Yes, age < 30 years","No","Unknown"))
```

`count` now produces the desired order of output:


```r
count(sample_metadata, pedaya)
```

```
## Warning: Factor `pedaya` contains implicit NA, consider using
## `forcats::fct_explicit_na`
```

```
## # A tibble: 4 x 2
##   pedaya                  n
##   <fct>               <int>
## 1 Yes, age < 30 years  2702
## 2 No                   9426
## 3 Unknown                82
## 4 <NA>                   26
```


We can count two categorical columns at once.


```r
count(sample_metadata, pedaya, disease)
```

```
## Warning: Factor `pedaya` contains implicit NA, consider using
## `forcats::fct_explicit_na`
```

```
## # A tibble: 163 x 3
##    pedaya              disease                                 n
##    <fct>               <chr>                               <int>
##  1 Yes, age < 30 years acinar cell carcinoma                   1
##  2 Yes, age < 30 years acute leukemia                          1
##  3 Yes, age < 30 years acute leukemia of ambiguous lineage     2
##  4 Yes, age < 30 years acute lymphoblastic leukemia          549
##  5 Yes, age < 30 years acute megakaryoblastic leukemia        92
##  6 Yes, age < 30 years acute myeloid leukemia                303
##  7 Yes, age < 30 years acute undifferentiated leukemia         1
##  8 Yes, age < 30 years adrenocortical adenoma                  1
##  9 Yes, age < 30 years adrenocortical cancer                  15
## 10 Yes, age < 30 years adrenocortical carcinoma               18
## # … with 153 more rows
```


### Challenge: counting {- .challenge}

Investigate how many OECD and non-OECD nations come from the northern and southern hemispheres using `count`

1. Using `count`.
2. By making a mosaic plot.

Remember you may need to convert columns to factors for `plot` to work, and that a `southern` column could be added to `geo` with:

HCB: consider adding leukemia as the example with a grep


```r
# geo$southern <- geo$lat < 0
```


## Sorting

Data frames can be sorted using the `arrange` function in `dplyr`.


```r
arrange(sample_metadata, age_at_dx)
```

```
## # A tibble: 12,236 x 9
##    th_sampleid disease age_at_dx pedaya gender site_id site_donor_id
##    <chr>       <chr>       <dbl> <fct>  <chr>  <chr>   <chr>        
##  1 THR08_0168… acute …         0 Yes, … unkno… THR08   SJINF009     
##  2 THR08_0169… acute …         0 Yes, … unkno… THR08   SJINF010     
##  3 TH06_0613_… atypic…         0 Yes, … male   TH06    N/A          
##  4 TH06_0613_… atypic…         0 Yes, … male   TH06    N/A          
##  5 TH06_0627_… gliobl…         0 Yes, … female TH06    N/A          
##  6 THR29_0744… rhabdo…         0 Yes, … female THR29   RMS_2049     
##  7 THR29_0748… embryo…         0 Yes, … male   THR29   RMS_2053     
##  8 THR29_0753… alveol…         0 Yes, … male   THR29   RMS_2061     
##  9 THR29_0754… alveol…         0 Yes, … male   THR29   RMS_2063     
## 10 THR29_0782… embryo…         0 Yes, … female THR29   RMS_212      
## # … with 12,226 more rows, and 2 more variables: site_sampleid <chr>,
## #   senior_glioma <lgl>
```

Numeric columns are sorted in numeric order. Character columns will be sorted in alphabetical order. Factor columns are sorted in order of their levels. The `desc` helper function can be used to sort in descending order.


```r
arrange(sample_metadata, desc(disease))
```

```
## # A tibble: 12,236 x 9
##    th_sampleid disease age_at_dx pedaya gender site_id site_donor_id
##    <chr>       <chr>       <dbl> <fct>  <chr>  <chr>   <chr>        
##  1 TH03_0012_… wilms …     18    Yes, … male   TH03    N/A          
##  2 TH03_0012_… wilms …     18    Yes, … male   TH03    N/A          
##  3 TH27_1405_… wilms …     NA    <NA>   male   TH27    N/A          
##  4 TH03_0144_… wilms …      6    Yes, … male   TH03    N/A          
##  5 TH27_2189_… wilms …      5    Yes, … female TH27    N/A          
##  6 TH06_0632_… wilms …      4    Yes, … female TH06    N/A          
##  7 THR31_0876… wilms …     NA    Yes, … male   THR31   N/A          
##  8 TARGET-50-… wilms …      6.15 Yes, … female TARGET  TARGET-50-CA…
##  9 TARGET-50-… wilms …      4.68 Yes, … female TARGET  TARGET-50-CA…
## 10 TARGET-50-… wilms …      1.1  Yes, … female TARGET  TARGET-50-CA…
## # … with 12,226 more rows, and 2 more variables: site_sampleid <chr>,
## #   senior_glioma <lgl>
```


## Joining data frames

Let's move on to a larger data set. This is from the [Gapminder](https://www.gapminder.org) project and contains information about countries over time.


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

It would be useful to have general information about countries from `geo` available as columns when we use this data frame. `gap` and `geo` share a column called `name` which can be used to match rows from one to the other. 


```r
expression_by_disease <- left_join(expression_values, sample_metadata, by=c("sample"="th_sampleid"))
expression_by_disease
```

```
## # A tibble: 22,908 x 11
##    Gene  sample expression disease age_at_dx pedaya gender site_id
##    <chr> <chr>       <dbl> <chr>       <dbl> <fct>  <chr>  <chr>  
##  1 HRAS  THR15…       5.77 osteos…      NA   Yes, … female THR15  
##  2 MAP2… THR15…       4.53 osteos…      NA   Yes, … female THR15  
##  3 HRAS  THR29…       6.99 rhabdo…      20   Yes, … male   THR29  
##  4 MAP2… THR29…       4.51 rhabdo…      20   Yes, … male   THR29  
##  5 HRAS  THR11…       6.10 glioma       13   Yes, … male   THR11  
##  6 MAP2… THR11…       5.75 glioma       13   Yes, … male   THR11  
##  7 HRAS  THR08…       4.48 acute …       0.6 Yes, … unkno… THR08  
##  8 MAP2… THR08…       6.42 acute …       0.6 Yes, … unkno… THR08  
##  9 HRAS  TH27_…       6.41 glioma       NA   Unkno… unkno… TH27   
## 10 MAP2… TH27_…       5.39 glioma       NA   Unkno… unkno… TH27   
## # … with 22,898 more rows, and 3 more variables: site_donor_id <chr>,
## #   site_sampleid <chr>, senior_glioma <lgl>
```

The "left" in "left join" refers to how rows that can't be paired up are handled. `left_join` keeps all rows from the first data frame but not the second. This is a good default when the intent is to attaching some extra information to a data frame. `inner_join` discard all rows that can't be paired up. `full_join` keeps all rows from both data frames. 


## Further reading

We've covered the fundamentals of dplyr and data frames, but there is much more to learn. Notably, we haven't covered the use of the pipe `%>%` to chain `dplyr` verbs together. The ["R for Data Science" book](http://r4ds.had.co.nz/) is an excellent source to learn more. The Monash Data Fluency ["Programming and Tidy data analysis in R" course](https://monashdatafluency.github.io/r-progtidy/) also covers this. 











