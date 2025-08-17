---
layout: page
title: "Data Cleaning in R"
permalink: "/datacleaning/"
---

<head>
  <!-- Google tag (gtag.js) -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XSH3BVKG0H"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', 'G-XSH3BVKG0H');
</script>
</head>

# Data cleaning

This page guides you through basic data cleaning methods in R, using clear and practical examples. As you follow along, you will see how each technique works and why it is useful for preparing your data for analysis. You will learn how to:

- **Select variables** from a dataset to work with a subsample of data.
- **Create dummy variables** from categorical responses.  
- **Generate and recode categorical variables**, including grouping values and adding descriptive labels.  
- **Filter data** based on conditions (similar to Stata’s `keep if`).  
- **Create variables conditionally** on other variables' values.  
- **Handle missing values** by removing rows with `NA`s in key fields.  

By the end of this tutorial, you will know how to clean raw data. You will do this using R's `dplyr` library, which you can load into your R session by running the following command:
```r
library(dplyr)
```
## Select a subsample of data
Before starting our data cleaning, we select only a subsample of variables from our dataset. We keep only three variables:

- `vote`: whether the respondent voted  
- `agea`: age of respondent  
- `eisced`: education level (ES-ISCED classification)

and save them into the dataframe `df_cleaned`, using the function `select()`:

```r
df_cleaned<-select(df, vote, agea, eisced)
```

## Generate Dummy Variable
A dummy variable is a binary indicator that takes a value of 1 or 0. Here, we create a dummy variable, `vote_variable`, which takes the value 1 if the respondent reports voting in the last elections and 0 otherwise. Before we begin, let us check the structure of the variable `vote` using the `tabulate()` command that we created in [How to create a function in R, and why should you do it?](https://gubellom.github.io/functions/):


```r
tabulate(df_cleaned$vote)
```
<details>
  <summary>[Output]</summary>
  <pre>
              Response Frequency Percentage Cumulative
1                  Yes     30815      69.42      69.42
2                   No      9417      21.22      90.64
3 Not eligible to vote      3687       8.31      98.95
4              Refusal       250       0.56      99.51
5           Don't know       209       0.47      99.98
6            No answer         9       0.02     100.00
7                Total     44387     100.00         NA
  </pre>
</details>

We then check the values assigned to each label using the command `attr()`. Alternatively, one can use the command `table()`, which provides the value and the number of observations per value.

```r
attr(df_cleaned$vote, "labels")
```

<details>
  <summary>[Output]</summary>
  <pre>
                 Yes                   No Not eligible to vote              Refusal 
                   1                    2                    3                   NA 
          Don't know            No answer 
                  NA                   NA
  </pre>
</details>

We create a dummy variable, `vote_variable`, which takes the value 1 if a respondent voted in the last election, and 0 if the respondent did not vote or was not eligible to vote. NA values remain as NA values even in the new dummy variable.

```r
df_cleaned$vote_variable<-ifelse(df_cleaned$vote==1, 1, 0)
tabulate(df_cleaned$vote_variable)
```
<details>
  <summary>[Output]</summary>
  <pre>
  Response Frequency Percentage Cumulative
1        0     13104      29.52      29.52
2        1     30815      69.42      98.94
3       NA       468       1.05      99.99
4    Total     44387      99.99         NA
  </pre>
</details>

## Generate a categorical variable
Another important coding practice is generating a new categorical variable from an existing one. This is useful when you want to group a few categories because they have only a few observations, or when you want to simplify the categorical variable by creating broader categories.

We choose the variable `eisced`, which gives us the ISCED educational levels of each respondent. As before, we check the structure of the variable using the commands `tabulate()` and `attr()`.

```r
tabulate(df_cleaned$eisced)
```
<details>
  <summary>[Output]</summary>
  <pre>
                                              Response Frequency Percentage Cumulative
1               ES-ISCED I , less than lower secondary      3861       8.70       8.70
2                         ES-ISCED II, lower secondary      7388      16.64      25.34
3            ES-ISCED IIIb, lower tier upper secondary      7163      16.14      41.48
4            ES-ISCED IIIa, upper tier upper secondary      8720      19.65      61.13
5         ES-ISCED IV, advanced vocational, sub-degree      6265      14.11      75.24
6      ES-ISCED V1, lower tertiary education, BA level      4760      10.72      85.96
7  ES-ISCED V2, higher tertiary education, >= MA level      6013      13.55      99.51
8                                                Other        88       0.20      99.71
9                                              Refusal        82       0.18      99.89
10                                          Don't know        39       0.09      99.98
11                                           No answer         8       0.02     100.00
12                                               Total     44387     100.00         NA
  </pre>
</details>


```r
attr(df_cleaned$eisced, "labels")
```
<details>
  <summary>[Output]</summary>
  <pre>
            Not possible to harmonise into ES-ISCED 
                                                  0 
             ES-ISCED I , less than lower secondary 
                                                  1 
                       ES-ISCED II, lower secondary 
                                                  2 
          ES-ISCED IIIb, lower tier upper secondary 
                                                  3 
          ES-ISCED IIIa, upper tier upper secondary 
                                                  4 
       ES-ISCED IV, advanced vocational, sub-degree 
                                                  5 
    ES-ISCED V1, lower tertiary education, BA level 
                                                  6 
ES-ISCED V2, higher tertiary education, >= MA level 
                                                  7 
                                              Other 
                                                 55 
                                            Refusal 
                                                 NA 
                                         Don't know 
                                                 NA 
                                          No answer 
                                                 NA 
  </pre>
</details>

Notice that while the category "*Not possible to harmonise into ES-ISCED*" exists, it has 0 observations and therefore does not appear in the output of `tabulate()` or `table()`. We can verify the number of observations in each category using the `sum()` command, escluding the missing values (i.e., `na.rm = TRUE`):

```r
sum(df_cleaned$eisced == 0, na.rm = TRUE)
```
<details>
  <summary>[Output]</summary>
  <pre>
[1] 0
  </pre>
</details>

We simplify our `eisced` variable by grouping it into three categories: (1) Lower education (ES-ISCED I, ES-ISCED II), (2) Middle education (ES-ISCED IIIb, ES-ISCED IIIa), and (3) Higher education (ES-ISCED IV, ES-ISCED V1, ES-ISCED V2). To do this, we use a *nested* `ifelse` function. An `ifelse` statement is a function that assigns a specific value if a condition is true, and another value if it is false. It is called *nested* because multiple `ifelse` functions are combined within one another. Notice that `%in%` can be read as "*belongs to the vector*". Essentially, we can read the first line of code as: "*If the value in `df_cleaned$eisced` is 1 or 2, assign value 1 to `df_cleaned$education`*". The other lines follow a similar logic.

```r
# Use nested ifelse
df_cleaned$education <- ifelse(df_cleaned$eisced %in% c(1, 2), 1,     # If the value in df_cleaned$eisced takes value 1 or 2, assign value 1 to df_cleaned$education
                        ifelse(df_cleaned$eisced %in% c(3, 4), 2,
                        ifelse(df_cleaned$eisced %in% c(5, 6, 7), 3, NA)))
tabulate(df_cleaned$education)
```
<details>
  <summary>[Output]</summary>
  <pre>
  Response Frequency Percentage Cumulative
1        1     11249      25.34      25.34
2        2     15883      35.78      61.12
3        3     17038      38.39      99.51
4       NA       217       0.49     100.00
5    Total     44387     100.00         NA
  </pre>
</details>

We can now relabel the values associated with each response. We choose three simple labels: *Education: lower*, *Education: middle* and *Education: higher*. 
  
```r
df_cleaned <- df_cleaned %>%
  mutate(education = case_when(
    education == 1 ~ "Education: lower",
    education == 2 ~ "Education: middle",
    education == 3 ~ "Education: higher",
    TRUE ~ NA_character_
  ))
tabulate(df_cleaned$education)
```
<details>
  <summary>[Output]</summary>
  <pre>
           Response Frequency Percentage Cumulative
1 Education: higher     17038      38.39      38.39
2 Education: middle     15883      35.78      74.17
3  Education: lower     11249      25.34      99.51
4                NA       217       0.49     100.00
5             Total     44387     100.00         NA
  </pre>
</details>

The above code applies the `mutate()` function to modify the values in `education`. Precisely, `df_cleaned %>% mutate(education = ...)` can be read as *Take the dataset `df_cleaned` and then (`%>%`) mutate the variable `education`)*. Then, we use `case_when()`. This function modifies a variable (or creates a new variable) based on multiple conditions (e.g., *If `education` is 1, it assigns the label "Education: lower"*):

```r
case_when(
condition1 ~ value1,
condition2 ~ value2,
...,
TRUE ~ default_value  
)
```
Finally, `TRUE ~ default_value` is the equivalent of an `else` statement. Namely, if none of the above conditions are met, the code assigns NA of type character.


## Keep a subset of the original data that meets certain conditions

Another useful function is `filter()`. This function is the Stata equivalent of `keep if`. It allows us to keep only observations satisfying specific conditions. For instance, assume we want to focus on the working-age population by keeping only respondents between 15 and 64 years old and excluding pensioners and people who are too young to work. First, let's see the summary statistics for the variable `agea`:

```r
summary(df_cleaned$agea)
```

<details>
  <summary>[Output]</summary>
  <pre>
Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
15.00   34.00   49.00   49.14   64.00  100.00     155 
  </pre>
</details>

We can then filter out the values we are not interested in by using our `filter()` function:

```r
df_cleaned <- filter(df_cleaned, agea>=15 & agea<=64)
summary(df_cleaned$agea)
```
<details>
  <summary>[Output]</summary>
  <pre>
Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
15.00   30.00   42.00   41.42   53.00   64.00 
  </pre>
</details>

## Create a variable conditional on the values from another variable

Sometimes we want to keep only the values of a variable `var1` that depend on specific values of another variable `var2`. We can do this by combining the `mutate()` function with the `ifelse()` function. For example, in the example below, we want to see how many people under the age of 18 did not vote because they were classified as *Not eligible to vote*. To do so, we write:

```r
df_cleaned<-mutate(df_cleaned, no_vote_18 =ifelse(vote==3 & agea<18, 1, 0))
tabulate(df_cleaned$no_vote_18)
```
This time, the `tabulate()` function rounds numbers to three decimal places:

<details>
  <summary>[Output]</summary>
  <pre>
  Response Frequency Percentage Cumulative
1        0     32493     96.665     96.665
2        1      1119      3.329     99.994
3       NA         2      0.006    100.000
4    Total     33614    100.000         NA
  </pre>
</details>
  
## Remove all observations with missing values in one variable.

Sometimes, when cleaning our data, we need to drop all missing values. In Stata, this can be done with `drop if missing(var)`. In R, we can use the `filter()` function together with `!is.na()` (meaning *it’s not a missing value*). In this example, we drop all the rows where `vote` is missing to ensure a clean dataset for analysis.


```r
df_cleaned <- filter(df_cleaned, !is.na(vote))
tabulate(df_cleaned$vote)
```
The result is straightforward as the number of observations drop:

<details>
  <summary>[Output]</summary>
  <pre>
              Response Frequency Percentage Cumulative
1                  Yes     22113      66.47      66.47
2                   No      7666      23.04      89.51
3 Not eligible to vote      3490      10.49     100.00
4                Total     33269     100.00         NA
  </pre>
</details>

## Remove all rows with missing values in at least one variable.

We can also drop all missing values in our dataset. I present three options here: Option 1) uses the function `if_all(c(var1,var2, ..., varN))` combined with `filter()`. This option is preferable when you are creating a subsample of data (for instance, including only a few specific variables). Option 2) uses `na.omit()`, which drops a row if there are NA values in any of the variables of the dataset. Option 3) does the same. 

```r 
# Option 1
df_cleaned <- df_cleaned %>%
  filter(if_all(c(vote, agea, eisced), ~ !is.na(.)))

# Option 2
df_cleaned <- na.omit(df_cleaned)

# Option 3
df_cleaned <- df_cleaned[complete.cases(df_cleaned), ]
```
For options 2) and 3), if you want to obtain the same result as option 1), it is enough to select another subdataset from `df_cleaned' (or from the original `df'). For instance, you can select:

```r
df3 <-select(df, vote, agea, eisced)
df_cleaned <- na.omit(df3)
summary(df_cleaned$agea)
```
<details>
  <summary>[Output]</summary>
  <pre>
   # Summary shows no NA values exist 
  Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
  15.00   34.00   49.00   49.13   64.00  100.00 
  </pre>
</details>


Go back to the [Introduction webpage ↩ ](https://gubellom.github.io/michelegubello_Introduction/)









<!---
## Basic operations

We transform age with simple operations:

- Divide by 100 → `age_100`  
- Multiply by 100 → `age_times_100`  


```r
#Divide age by 100
df_cleaned$age_100<-df_cleaned$agea/100
df_cleaned$age_times_100<-df_cleaned$agea*100
```

<details>
  <summary>[Output]</summary>
  <pre>
summary(df_cleaned$age_100)
Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
0.1500  0.3000  0.4300  0.4196  0.5400  0.6500 

summary(df_cleaned$age_times_100)
Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
1500    3000    4300    4196    5400    6500 
  </pre>
</details>
--->
