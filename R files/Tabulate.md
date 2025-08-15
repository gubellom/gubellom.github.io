---
layout: page
title: "Tabulate in R"
permalink: "/tabulate/"
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
<!-- <div style="display: flex; align-items: center;">
  <div style="flex: 1;">
    <img src="https://github.com/gubellom/gubellom.github.io/blob/master/profile.JPG?raw=true" alt="My Photo" style="width:200px; border-radius:50%;">
  </div>
  <div style="flex: 2; margin-left: 20px;">
    <p> -->

    
# How To Replicate STATA Tabulate Command

A particularly useful command in STATA is `tabulate`, which is commonly used to create frequency tables or cross-tabulations between two categorical variables. It provides a simple way to understand how values are distributed across categories, and is especially helpful in exploring relationships in survey or categorical data.

## One-way tabulate
In R, we can replicate its functionality using a combination of different R functions. Below is an example of how to generate a STATA-style frequency table for the variable `polintr` (interest in politics) using R.

Let's start by importing the necessary packages.

```r
pacman::p_load(
  haven,     
  dplyr
)
```
To generate a Tabulate command that includes all the values:

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
  Cumulative = NA_real_, 
  stringsAsFactors = FALSE
)

stat_table_final <- rbind(stat_table, total_row)

# Print tabel with variable label
attr(df$polintr, "label")
print(stat_table_final)
```

<details>
  <summary>[Output]</summary>

  <pre>
[1] "How interested in politics"
    
               Response Frequency Percentage Cumulative
1       Very interested      5415      12.20      12.20
2      Quite interested     15539      35.01      47.21
3     Hardly interested     15248      34.35      81.56
4 Not at all interested      8088      18.22      99.78
5               Refusal        36       0.08      99.86
6            Don't know        57       0.13      99.99
7             No answer         4       0.01     100.00
8                 Total     44387     100.00         NA

</pre>
</details>


To remove missing values and responses such as *Refusal*, *Don't know*, and *No answer*, I filter a subsample of the data to include only the `gndr` and `polintr` variables. I name the resulting dataset `df_oneway`.


```r

#Drop observations that are missings in both variables
df_oneway<-select(df, gndr, polintr)
df_oneway <- df_oneway[complete.cases(df_oneway), ]

# Consider labels
df_oneway$polintr_label<-as_factor(df_oneway$polintr)
df_oneway$gndr_label<-as_factor(df_oneway$gndr)

# Removes unused factor levels (levels with 0 observations)
df_oneway <- df_oneway %>%
  mutate(
    polintr_label = droplevels(polintr_label),
    gndr_label = droplevels(gndr_label)
  )

```

Notice that the command `%>%` must be read as "and then apply to `df_oneway` the function `mutate()`". However, we can also write:

```r
df_oneway <- mutate(df_oneway,
    polintr_label = droplevels(polintr_label),
    gndr_label = droplevels(gndr_label)
  )
```

Then, I replicate the ```tabulate``` code using the dataset ```df_oneway```:

```r
# Generate Tabulate for Variable "polintr"

labels <- attr(df_oneway$polintr, "labels")
response_labels <- names(labels)
polintr_factor <- factor(as_factor(df_oneway$polintr))
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

# Add a final row for total
total_row <- data.frame(
  Response = "Total",
  Frequency = sum(stat_table$Frequency),
  Percentage = sum(stat_table$Percentage),
  Cumulative = NA_real_,  
  stringsAsFactors = FALSE
)

stat_table_final <- rbind(stat_table, total_row)

attr(df_oneway$polintr, "label")
print(stat_table_final)
rm(df_oneway)

```

<details>
  <summary>[Output]</summary>

  <pre>
[1] "How interested in politics"
    
               Response Frequency Percentage Cumulative
1       Very interested      5414      12.23      12.23
2      Quite interested     15535      35.08      47.31
3     Hardly interested     15245      34.43      81.74
4 Not at all interested      8087      18.26     100.00
5                 Total     44281     100.00         NA

</pre>
</details>

## Two-way tabulate
A **two-way tabulation** is a statistical tool used to display the relationship between two categorical variables. Each cell in the table represents the number of observations (or frequency) that fall into the combination of a level from each variable. For example, if we are analysing the variables *gender* (`gndr`) and *interest in politics* (`polintr`), a two-way tabulation will show how many individuals of each gender report different levels of political interest. Before creating such a table, I remove or exclude **missing values** or responses such as *Refusal*, *Don't know*, and *No answer*.


```r

#Drop observations that are missings in both variables
df_twoway<-select(df, gndr, polintr)
df_twoway <- df_twoway[complete.cases(df_twoway), ]

# Consider labels
df_twoway$polintr_label<-as_factor(df_twoway$polintr)
df_twoway$gndr_label<-as_factor(df_twoway$gndr)

# Removes unused factor levels (levels with 0 observations)
df_twoway <- mutate(df_twoway,
    polintr_label = droplevels(polintr_label),
    gndr_label = droplevels(gndr_label)
  )

# Generate the 2-way tabulate
tab_2w <- table(df_twoway$polintr_label, df_twoway$gndr_label, useNA = c("no"))
addmargins(tab_2w)

```

<details>
  <summary>[Output]</summary>

  <pre>
                       Male Female   Sum
  Very interested        3323   2091  5414
  Quite interested       7926   7609 15535
  Hardly interested      6557   8688 15245
  Not at all interested  3184   4903  8087
  Sum                   20990  23291 44281

</pre>
</details>

Go back to the [Introduction webpage ↩ ](https://gubellom.github.io/michelegubello_Introduction/)

 <!--   </p>
  </div>
</div>-->



