---
layout: page
title: "Michele Gubello"
permalink: "/functions/"
---

# Why create functions in R?

Creating functions in R is helpful because it makes your code easier to understand, reuse, and modify. When carrying out the same task repeatedly, a function lets you write the code once and reuse it whenever needed. This saves time and helps to reduce errors. Functions also allow you to keep your code clear and organised by shortening it and dividing it into smaller sections. Giving each function a meaningful name makes it easier to understand your code. If you need to modify your code, you can modify the function itself once, rather than searching for multiple lines of code and making changes to each of them. 

To create a function, the first step you need to follow is to open the R edit file. An R edit file is a text file (ending in `.R`) where you write and save your R code so you can edit, reuse, and run it later. You can open you edit file by simply writing this prompt on Rstudio:

```r
file.edit("~/.Rprofile")
```
If you cannot find your R file, you can check the path in your laptop by writing the following line:

```r
normalizePath("~/.Rprofile")
```

Once your Edit file is open, you can write your function. In this example, I am going to write a function for the `tabulate` command that I have presented in [How to Replicate STATA tabulate command: one-way and two-way tables](https://gubellom.github.io/tabulate/).


```r
### TABULATE COMMAND if labels are associated to values:: tab_labels
tab_labels <- function(var) {
  # Extract labels if they exist
  labels <- attr(var, "labels")
  
  # Get label names (if available), otherwise fallback to values
  response_labels <- if (!is.null(labels)) names(labels) else sort(unique(var))
  
  # Convert to factor using haven::as_factor
  factor_var <- factor(haven::as_factor(var), levels = response_labels)
  
  # Frequency table
  freq_table <- table(factor_var, useNA = "ifany")
  percent_table <- round(prop.table(freq_table) * 100, 2)
  cum_percent <- round(cumsum(percent_table), 2)
  
  # Combine into data frame
  stat_table <- data.frame(
    Response = names(freq_table),
    Frequency = as.vector(freq_table),
    Percentage = as.vector(percent_table),
    Cumulative = as.vector(cum_percent),
    stringsAsFactors = FALSE
  )
  
  # Add total row
  total_row <- data.frame(
    Response = "Total",
    Frequency = sum(stat_table$Frequency),
    Percentage = sum(stat_table$Percentage),
    Cumulative = NA_real_,
    stringsAsFactors = FALSE
  )
  
  # Final result
  stat_table_final <- rbind(stat_table, total_row)
  return(stat_table_final)
}
```
