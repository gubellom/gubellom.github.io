---
layout: page
title: ""
permalink: "/datacleaning/"
---

# Data cleaning tools.

```r
df_cleaned<-select(df, vote, polintr, ipstrgv, agea, eisced)
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

## Generate Dummy Variable

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

```r
# Use nested ifelse
df_cleaned$education <- ifelse(df_cleaned$eisced %in% c(1, 2), 1,
                        ifelse(df_cleaned$eisced %in% c(3, 4), 2,
                        ifelse(df_cleaned$eisced %in% c(5, 6, 7), 3, NA)))
tabulate(df_cleaned$education)

#We also relabel education
df_cleaned <- df_cleaned %>%
  mutate(education_label = case_when(
    education == 1 ~ "Education: lower",
    education == 2 ~ "Education: middle",
    education == 3 ~ "Education: higher",
    TRUE ~ NA_character_
  ))
tabulate(df_cleaned$education)

```
