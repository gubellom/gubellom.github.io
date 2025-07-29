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
    
Let's add an R code:


Let's import the necessary packages.
```
pacman::p_load(
  haven,
  skimr,        # get overview of data
  tidyverse,    # data management + ggplot2 graphics 
  gtsummary,    # summary statistics and tests
  rstatix,      # summary statistics and statistical tests
  janitor,      # adding totals and percents to tables
  scales,       # easily convert proportions to percents  
  flextable,    # converting tables to pretty images
  dplyr,
  summarytools
)
```


Import your dataset and select a subsample with the function  ```select()```.  In my case, I import the ESS 8 and I use the ```.dta``` version of it, which is the version in STATA. 
```
df <- read_dta("C:/[directory]/ESS8e02_3.dta") 

#Select a subsample of variables
df_cleaned<-select(df, vote, polintr, ipstrgv)
```

A basic way to generate summary statistics is using the function ```summary()```:

```
summary_stats <- summary(df_cleaned)
print(summary_stats)
```

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


 <!--   </p>
  </div>
</div>-->






