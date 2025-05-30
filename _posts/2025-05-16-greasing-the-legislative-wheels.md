---
layout: post
title: Greasing the Legislative Wheels
subtitle:  Lobbying in Congress
tags: [Final project]
author: Joshua Borut
---

# Introduction

> "THIS IS A GREAT TIME TO BUY!!!"
>
> \- President Donald Trump

Bar
<head>
  <!-- Import Vega & Vega-Lite (does not have to be from CDN) -->
  <script src="https://cdn.jsdelivr.net/npm/vega@5"></script>
  <script src="https://cdn.jsdelivr.net/npm/vega-lite@5"></script>
  <!-- Import vega-embed -->
  <script src="https://cdn.jsdelivr.net/npm/vega-embed@6"></script>
</head>

<div id="vis1"></div>

<script type="text/javascript">
  var spec = "https://raw.githubusercontent.com/joshborut/joshborut.github.io/5a4aa4fc8dfd7c61fa3ea20157e6bb4669ea012e/assets/graphs/bar_plot.json";
  vegaEmbed('#vis1', spec).then(function(result) {
    // Access the Vega view instance (https://vega.github.io/vega/docs/api/view/) as result.view
  }).catch(console.error);
</script>

Republican
<div id="vis2"></div>

<script type="text/javascript">
  var spec = "https://raw.githubusercontent.com/joshborut/joshborut.github.io/5a4aa4fc8dfd7c61fa3ea20157e6bb4669ea012e/assets/graphs/R_plot.json";
  vegaEmbed('#vis2', spec).then(function(result) {
    // Access the Vega view instance (https://vega.github.io/vega/docs/api/view/) as result.view
  }).catch(console.error);
</script>

Democrat
<div id="vis3"></div>

<script type="text/javascript">
  var spec = "https://raw.githubusercontent.com/joshborut/joshborut.github.io/f5152c227e3be2e7c110df72b5b0ea0e126b5147/assets/graphs/D_plot.json";
  vegaEmbed('#vis3', spec).then(function(result) {
    // Access the Vega view instance (https://vega.github.io/vega/docs/api/view/) as result.view
  }).catch(console.error);
</script>

Independent
<div id="vis4"></div>

<script type="text/javascript">
  var spec = "https://raw.githubusercontent.com/joshborut/joshborut.github.io/5a4aa4fc8dfd7c61fa3ea20157e6bb4669ea012e/assets/graphs/I_plot.json";
  vegaEmbed('#vis4', spec).then(function(result) {
    // Access the Vega view instance (https://vega.github.io/vega/docs/api/view/) as result.view
  }).catch(console.error);
</script>

Third-Party
<div id="vis5"></div>

<script type="text/javascript">
  var spec = "https://raw.githubusercontent.com/joshborut/joshborut.github.io/5a4aa4fc8dfd7c61fa3ea20157e6bb4669ea012e/assets/graphs/T_plot.json";
  vegaEmbed('#vis5', spec).then(function(result) {
    // Access the Vega view instance (https://vega.github.io/vega/docs/api/view/) as result.view
  }).catch(console.error);
</script>

Libertarian
<div id="vis6"></div>

<script type="text/javascript">
  var spec = "https://raw.githubusercontent.com/joshborut/joshborut.github.io/5a4aa4fc8dfd7c61fa3ea20157e6bb4669ea012e/assets/graphs/L_plot.json";
  vegaEmbed('#vis6', spec).then(function(result) {
    // Access the Vega view instance (https://vega.github.io/vega/docs/api/view/) as result.view
  }).catch(console.error);
</script>

Soon after offering this advice to supporters on Truth Social on April 9, President Trump placed a 90-day pause on many of his most impactful tariffs, causing the S&P 500 to soar by 9.5%. This dramatic increase–the most since World War II–triggered accusations of market manipulation and insider trading. The nature of this event brought me to question the economic ethics of other politicians. Specifically, I wanted to see how Congress members who are less likely to be monitored and scrutinized make economic decisions and whether their success coincides with policy change.

I quickly found that the Stop Trading on Congressional Knowledge Act, known better as the [STOCK Act](https://campaignlegal.org/update/congressional-stock-trading-and-stock-act), was the key to sourcing this data. The policy, made in 2012 in an effort to combat inside trading, stipulates that Congress people must disclose their expenditures within 30 days of making an investment. While the act was intended to harshen punishments for insider trading, a lack of enforcement has led to a severe underreporting of stock purchases. This unaccounted data complicates drawing conclusions for my purposes. Furthermore, the myriad of factors that influence portfolio success make it difficult to discern between insider knowledge and strategic investment. 

Instead, I turned to lobbying and donation data to see which industries target politicians across party lines. Though perhaps not as sensational as pointing out market manipulation and other forms of financial malpractice, identifying which industries target specific politicians reveals where policy change may be more likely to occur to stimulate market growth, thereafter becoming a vulnerable site for financial exploitation. 

# Data

## Sourcing 

I sourced my data from __OpenSecrets__, a nonprofit committed to publicizing data pertaining to elections, politics, and lobbying. Although the site charges for tailored data, I registered to create a bulk account that enables me to download large quantities of data under a Creative Commons license. Given the variety of ways in which government data is inaccessibly stored, OpenSecrets’ database is held back by its lack of complete coverage.

## Processing

__OpenSecrets__ makes it quite easy to download data. Once a bulk data account is approved, the user can simply download a `CSV` or similar file format to use. However, the formatting of said file is quite convoluted and requires heavy reliance on the provided key.

## Visualization

* Geographical map where R/D is discerned by color and on hover it displays the top three largest lobbying and donating industries
- Bar chart to describe nationally what groups contribute most to Congress members

# Case Studies
- Look at one D and one R Congress people to compare contribution sources

# Results
- Describe data

# Conclusion
- Describe how voters can deploy this knowledge effectively