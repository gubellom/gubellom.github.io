---
layout: page
title: "Merging in R"
permalink: "/merging/"
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

# How to merge and append datasets in R?

One common task when working with multiple datasets is merging or appending them into a single dataset. This becomes particularly useful, for instance, when working with individual-level data and wanting to introduce regional or national variables into the analysis.

Henceforth, I show how to:

- Merge two datasets in a one-to-one format (e.g., individual-to-individual observations).
- Merge two datasets in a one-to-many format (e.g., individual-to-country-level observations).
- Append two datasets.

In this analysis, I will use three datasets:

1) The usual ESS8 (from <a href="https://ess.sikt.no/en/">European Social Survey website</a>) as primary dataset, labelled as `df`.
2) A dataset with average age by country, labelled as `df_avg`.
3) A dataset with individual-level values for the variable "social trust", rescaled from [0,10] to a [0,1] scale. The dataset is labeled as `df_trust`.
4) A dataset with a sample of people from the country "Austria", labelled as `df_append_austria`.

All datasets and R code generating them can be found in this <a href="https://github.com/gubellom/r_dataset_exercises/tree/main">Github directory</a>.

# Merge one-to-one.

One can merge two datasets - let's say `Dataset_1` and `Dataset_2`- in a one-to-one format when the same observation appears in both datasets only once. For instance, assume you have two datasets. The two datasets share the same observations to which it has been assigned a unique identifier number, but different variables. In one dataset you have information about age, and in the other information about education. You can merge the two datasets to have one unique dataset showing the age and education level of each observation.

