---

permalink: "/michelegubello_Introduction/"
title: "R for data analysis"
layout: page
---


# Introduction

**Welcome!**

This page is an introduction to R for users who have some basic experience with STATA. You'll find a few files here designed to provide simple tools for data analysis, similar to those used in quantitative social science research.

The goal is to share small code snippets that can help you generate various outputs quickly and easily. In R, there are often many different ways to achieve the same result. The scripts provided here may not always be the most efficient, but they aim to be clear, intuitive and practical. I’ll continue to improve and update them whenever I have some free time.

For all the examples, I will import the ESS 8 dataset, which you can find in the <a href="https://ess.sikt.no/en/">European Social Survey website</a>. The dataset format is in STATA, so I will import it through the following command:

```r
library(haven)
df <- read_dta("C:/[directory]/ESS8e02_3.dta") 
```


This is an ongoing project; please check back periodically for new content or updates.

1) [How to create a STATA-style summary statistics table and export it to Excel or LaTeX](https://gubellom.github.io/descriptive_stats/)
   
2) [How to Replicate STATA tabulate command: one-way and two-way tables](https://gubellom.github.io/tabulate/)

3) [How to create a function in R, and why should you do it? ](https://gubellom.github.io/functions/)

4) [Data cleaning (still incomplete) ](https://gubellom.github.io/datacleaning/)


