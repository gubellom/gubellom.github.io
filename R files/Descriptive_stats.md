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
    
# How to Create a STATA-Style Summary Statistics Table and Export It to Excel or LaTeX

This page presents a quick way to replicate a STATA-style summary statistics table in R. STATA's ```summarize``` command with the ```detail``` option provides key descriptive statistics such as the mean, standard deviation, minimum, maximum, number of observations and many others. Using R, we can achieve a similar output. Below is a minimal working example that summarizes key variables in a dataset in a format that closely resembles STATA's output. Let's start by importing the necessary packages.

```r
pacman::p_load(
  haven,     # Import Stata Dataset
  dplyr,     
  writexl,  #Export in Excel
  tibble,   # To convert row names to columns
  xtable    #Export in Latex
)
```
Import your dataset and select a subsample using the ```select()``` function. In this example, I import ESS Round 8 in ```.dta``` format, which is the STATA version. You can find the publicly available data on the <a href="https://ess.sikt.no/en/">ESS website</a>.
```r
df <- read_dta("C:/[directory]/ESS8e02_3.dta") 

#Select a subsample of variables
df_cleaned<-select(df, vote, polintr, ipstrgv)
```

A basic way to generate summary statistics is using the function ```summary()```, which provide summary statistics in a wide format if more than one variable is considered:

```r
summary_stats <- summary(df_cleaned)
print(summary_stats)
```
 <!-- 
```
     vote          polintr         ipstrgv     
 Min.   :1.000   Min.   :1.000   Min.   :1.000  
 1st Qu.:1.000   1st Qu.:2.000   1st Qu.:1.000  
 Median :1.000   Median :3.000   Median :2.000  
 Mean   :1.382   Mean   :2.587   Mean   :2.342  
 3rd Qu.:2.000   3rd Qu.:3.000   3rd Qu.:3.000  
 Max.   :3.000   Max.   :4.000   Max.   :6.000  
 NA's   :468     NA's   :97      NA's   :1002
```
--->
A better way to display the table is in long format, which allows you to select which summary statistics to include. In this example, I show the total number of valid (non-missing) observations, the mean, standard deviation, minimum, and maximum values. I also round all values to two decimal places if they have decimals.

```r
# Extract variable labels
var_labels <- sapply(df_cleaned, function(x) {
              lbl <- attr(x, "label")
              if (is.null(lbl)) NA else lbl })
# Replace variable names with labels where available. Create a copy in which you change the names
df_cleaned_var <-as.data.frame(df_cleaned)
names(df_cleaned_var) <- ifelse(is.na(var_labels), names(df_cleaned_var), var_labels)
# Transpose and make it long
summary_df <- descr(df_cleaned_var, stats = c("n.valid", "mean", "sd", "min", "max"), transpose = TRUE)
# Convert to data frame
summary_df_df <- as.data.frame(summary_df)
# Convert all columns except 'N. Valid' to numeric and round to 2 decimals
numeric_cols <- setdiff(names(summary_df_df), "N. Valid")
summary_df_df[numeric_cols] <- lapply(summary_df_df[numeric_cols], function(x) {
  round(as.numeric(as.character(x)), 2)
})
```

 <!-- 
```
                                                       N.Valid Mean Std.Dev Min Max
How interested in politics                               44290 2.59    0.92   1   4
Important that government is strong and ensures safety   43385 2.34    1.20   1   6
Voted last national election                             43919 1.38    0.64   1   3

```
--->

We can also change the names of the labels if they are too long. For instance, I change the label ''Important that government is strong and ensures safety'' into
''Government ensures safety'' and ''Voted last national election'' into ''Voted last election'':
```r
# Generate new names for the variable we want to change
new_labels <- c("Important that government is strong and ensures safety" = "Government ensures safety",
               "Voted last national election" = "Voted last election")

# Generate new dataframe to not overlap the previous one
summary_new_labels <- as.data.frame(summary_df_df)

# Change row names using the new names assigned in new_names
rownames(summary_new_labels) <- ifelse(rownames(summary_df_df) %in% names(new_labels),
                                  new_labels[rownames(summary_df_df)],
                                  rownames(summary_df_df))
print(summary_new_labels)
```

Finally, we can export the results in LaTeX or Excel by simply convert the "row names" into a column. Henceforth, I will use the data frame "summary_df_df":
```r

# Move rownames into a column called "Variable"
summary_df_export <- rownames_to_column(summary_df_df, var = "Variable")

#Export in Excel
writexl::write_xlsx(summary_df_export, "summary_table.xlsx")

#Export in Latex
latex_code <- xtable(summary_df_export)
sink("summary_table.tex") 
print(latex_code, type = "latex", include.rownames = FALSE)  
sink()  

```

 <!--   </p>
  </div>
</div>-->






