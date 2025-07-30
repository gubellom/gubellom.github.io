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
