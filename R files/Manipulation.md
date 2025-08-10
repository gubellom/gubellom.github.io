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



```r
#Generate categorical variable
tabulate(df_cleaned$eisced)
attr(df_cleaned$eisced, "labels")
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
