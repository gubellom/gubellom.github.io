---
layout: page
title: "Michele Gubello"
permalink: "/"
---

<!-- <div style="display: flex; align-items: center;">
  <div style="flex: 1;">
    <img src="https://github.com/gubellom/gubellom.github.io/blob/master/profile.JPG?raw=true" alt="My Photo" style="width:200px; border-radius:50%;">
  </div>
  <div style="flex: 2; margin-left: 20px;">
    <p> -->

    
# How To Replicate STATA Tabulate Command?

A particularly useful command in STATA is `tabulate`, which is commonly used to create frequency tables or cross-tabulations between two categorical variables. It provides a simple way to understand how values are distributed across categories, and is especially helpful in exploring relationships in survey or categorical data.

In R, there is no direct equivalent to `tabulate`, but we can replicate its functionality using a combination of base R functions. Below is an example of how to generate a STATA-style frequency table for the variable `polintr` (interest in politics) using R.

Let's start by importing the necessary packages.

```r
pacman::p_load(
  haven,     # Import Stata Dataset
  dplyr
)
```

I import the ESS 8 dataset, which you can find in the <a href="https://ess.sikt.no/en/">ESS website</a>.

```r
df <- read_dta("C:/[directory]/ESS8e02_3.dta") 

```


```r

# Generate Tabulate for Variable "polintr"

labels <- attr(df$polintr, "labels")
response_labels <- names(labels)
polintr_factor <- factor(as_factor(df$polintr), levels = response_labels)
freq_table <- table(polintr_factor, useNA = "ifany")
percent_table <- round(prop.table(freq_table) * 100, 2)

# Cumulative percentage
cum_percent <- round(cumsum(percent_table), 2)

# Combine into a data frame
stat_table <- data.frame(
  Response = names(freq_table),
  Frequency = as.vector(freq_table),
  Percentage = as.vector(percent_table),
  Cumulative = as.vector(cum_percent)
)

# Combine into a data frame
total_row <- data.frame(
  Response = "Total",
  Frequency = sum(stat_table$Frequency),
  Percentage = sum(stat_table$Percentage),
  Cumulative = NA_real_,  # Numeric NA to match column type
  stringsAsFactors = FALSE
)

stat_table_final <- rbind(stat_table, total_row)

print(stat_table_final)


```

               Response Frequency Percentage Cumulative
1       Very interested      5415      12.20      12.20
2      Quite interested     15539      35.01      47.21
3     Hardly interested     15248      34.35      81.56
4 Not at all interested      8088      18.22      99.78
5               Refusal        36       0.08      99.86
6            Don't know        57       0.13      99.99
7             No answer         4       0.01     100.00
8                 Total     44387     100.00         NA

```


```
