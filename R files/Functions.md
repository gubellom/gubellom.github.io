---
layout: page
title: "Functions in R"
permalink: "/functions/"
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

# How to create a function in R, and why should you do it?

Creating functions in R is helpful because it makes your code easier to understand, reuse, and modify. When carrying out the same task repeatedly, a function lets you write the code once and reuse it whenever needed. This saves time and helps to reduce errors. Functions also allow you to keep your code clear and organised by shortening it and dividing it into smaller sections. Giving each function a meaningful name makes it easier (for you and others) to understand your code. Moreover, if you need to modify your code, you can modify the function itself once, rather than searching for multiple lines of code and making changes to each of them. In other words, functions save you time.

To create a function, the first step is to open your Rprofile file. An Rprofile file is a text file (ending in `.R`) where you write and save your R code, allowing you to edit, reuse, and run it later. You can open your Rprofile file by simply writing this prompt in RStudio:

```r
file.edit("~/.Rprofile")
```
If you cannot find your R file, you can check the path on your laptop by writing the following line:

```r
normalizePath("~/.Rprofile")
```

Once your Rprofile file is open, you can write your function in it. In this example, I am going to write a function for the one-way `tabulate` command that I have presented in [How to Replicate STATA tabulate command: one-way and two-way tables](https://gubellom.github.io/tabulate/).

A function has the following structure:

```r
function_name <- function(variable) {

  # Build your function here
 
 output<- #Save your output here
 return(output)
}
```


In our example, we need to take the `tabulate` command built into the previous GitHub page [How to Replicate STATA tabulate command: one-way and two-way tables](https://gubellom.github.io/tabulate/), and generalise it in a way that it can be applied to any variable in our dataset. To write the function, we need to use a generic `var` label instead of a specific `df_oneway$polintr` command. Then, we can call our `tabulate()` function every time we need it:


```r
### TABULATE COMMAND if labels are associated to values:: tab_labels
tabulate <- function(var) {
  labels <- attr(var, "labels")
  response_labels <- names(labels)
  polintr_factor <- factor(as_factor(var))
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
  
  attr(var, "label")
  return(stat_table_final)
}

```
Then you can use your `tabulate()` command in R:

```r
tabulate(df_oneway$polintr)
```

<details>
  <summary>[Output]</summary>

  <pre>
 
               Response Frequency Percentage Cumulative
1       Very interested      5414      12.23      12.23
2      Quite interested     15535      35.08      47.31
3     Hardly interested     15245      34.43      81.74
4 Not at all interested      8087      18.26     100.00
5                 Total     44281     100.00         NA

</pre>
</details>

<!--
Notice that the library warning below is redundant in our case, as we need the library's `haven' to import a Stata dataset. So, no error message would appear.

```r
# Library warning in the code
  if (!require("haven", quietly = TRUE)) {
  stop("Package 'haven' is required but not installed.")
}
```

# Add library warnings
  if (!require("haven", quietly = TRUE)) {
  stop("Package 'haven' is required but not installed.")
}
--->

Go back to the [Introduction webpage ↩ ](https://gubellom.github.io/michelegubello_Introduction/)
