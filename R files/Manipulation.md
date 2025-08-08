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


```r
df_cleaned$vote_variable<-ifelse(df_cleaned$vote==1, 1, 0)
tabulate(df_cleaned$vote_variable)
```

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
