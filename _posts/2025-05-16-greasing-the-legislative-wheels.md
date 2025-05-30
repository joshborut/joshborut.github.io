---
layout: post
title: Greasing the Legislative Wheels
subtitle:  The Role of PACs in Congressional Elections
cover-img: /assets/img/congress_cover.png
thumbnail-img: /assets/img/congress_thumbnail.png
share-img: /assets/img/congress_cover.png
tags: [Final project]
author: Joshua Borut
---

# Introduction

> "THIS IS A GREAT TIME TO BUY!!!"
>
> \- President Donald Trump

Soon after offering this advice to supporters on Truth Social on April 9, President Trump placed a 90-day pause on many of his most impactful tariffs, causing the __S&P 500__ to soar by 9.5%. This dramatic increase–the most since World War II–triggered accusations of market manipulation and insider trading. The nature of this event brought me to question the economic ethics of other politicians. Specifically, I wanted to see how Congress members who are less likely to be monitored and scrutinized make economic decisions and whether their success coincides with policy change.

I quickly found that the Stop Trading on Congressional Knowledge Act, known better as the [STOCK Act](https://campaignlegal.org/update/congressional-stock-trading-and-stock-act), was the key to sourcing this data. The policy, made in 2012 in an effort to combat inside trading, stipulates that Congress people must disclose their expenditures within 30 days of making an investment. While the act was intended to harshen punishments for insider trading, a lack of enforcement has led to a severe underreporting of stock purchases. This unaccounted data complicates drawing conclusions for my purposes. Furthermore, the myriad of factors that influence portfolio success make it difficult to discern between insider knowledge and strategic investment. I also found scientific literature covering the impact of the __STOCK Act__, so I figured that it would be good to explore something new ([source](https://www.sciencedirect.com/science/article/abs/pii/S1059056024005835)).

Instead, I turned to lobbying and donation data to see which industries target politicians across party lines. Though perhaps not as sensational as pointing out market manipulation and other forms of financial malpractice, identifying which industries target specific politicians reveals where policy change may be more likely to occur to stimulate market growth, thereafter becoming a vulnerable site for financial exploitation. I chose to use data from 2022 that covers funding from political action committees (PACs) for congressional campaigns. Although I had access to data covering donations from individuals, I figured that PACs would better reflect the leverage large groups hold over Congress.

# PACs 

After locating my data, I had to figure out what a PAC actually was…

PACs are defined as private groups that funnel contributions from constituents, all of whom must share some association with an organization or cause, to a candidate. However, these groups must legally be unaffiliated with said candidates. PACs came about in 2010 after the Supreme Court ruled in *Citizens United v. FECs* that corporations could finance politicians in an organized manner ([source](https://campaignlegal.org/update/super-pac-deals-are-bad-deal-democracy)). Despite the continued existence of legislative barriers that prevent PACs from coordinating with candidates, loopholes and lax restrictions enable PACs to persist nonetheless ([source](https://campaignlegal.org/update/super-pac-deals-are-bad-deal-democracy)). As such, PACs are a powerful source of funding for all forms of political candidates, oftentimes allocating sums of money in the millions. Consequently, it is important to monitor how PACs behave to see if their actions in any way influence the political undertakings of an elected individual.

## Examples of PACs

__Never Back Down Inc__ is an example of a so-called “super PAC” that has notably supported conservative Florida Governor Rob DeSantis, who is strongly anti-choice, pro-wall, and against “woke ideology” ([source](https://www.bbc.com/news/world-us-canada-65464667)). The group reportedly spent over $40 million dollars in advertising on DeSantis and has been one of his main funding sources ([source](https://www.cbsnews.com/news/never-back-down-desantis-super-pac-backs-out-2-5-million-2024-tv-advertising/)). Sarah Ghosh, a director at __Campaign Legal Center__, asserted that “when a super PAC like __Never Back Down__ illegally coordinates its election spending with a candidate’s campaign, the super PAC effectively becomes an arm of the campaign” ([source](https://www.politico.com/news/2023/12/18/complaint-desantis-campaign-never-back-down-00132325)). PACs like these accused of malpractice stand against the egalitarian ideals of the United States’ electoral system.

# Data

## Sourcing

I sourced my data from __[OpenSecrets](https://www.opensecrets.org/)__, a nonprofit committed to publicizing data pertaining to elections, politics, and lobbying. Although the site charges for tailored data, I registered to create a bulk account that enables me to download large quantities of data under a *Creative Commons* license. Given the variety of ways in which government data is inaccessibly stored, __OpenSecrets’__ database is held back by its lack of complete coverage.

## Processing

OpenSecrets makes it quite easy to download data. Once a bulk data account is approved, the user can simply download a `CSV` or similar file format to use. However, the formatting of said file is quite convoluted and requires heavy reliance on the provided key. After locating my text files, the largest of which occupied a whopping __15.6 GB__, I used parsing libraries to automate the process of substituting identifier codes for readable text. Specifically, OpenSecrets utilizes unique identifier codes, called `CID` codes, that correspond to individual politicians and `CRP` codes that correspond to specific industries. This made the process of cleaning my data rather laborious (the processing time took up to half an hour due to the sheer size of the dataset), but eventually I yielded a `CSV` with __4,545,798__ data points in the following form:

| name | party | state | industry | amount | donor |
| :--: | :---: | :---: | :------: | :----: | :---: |
| Aarika Rhodes (D) | D | CA | Party/Non-contribs | 75 | PAC |
| Aarika Rhodes (D) | D | CA | Party/Non-contribs | 458 | PAC |

Here, each row is an individual donation. Additionally, the `donor` column is not crucial for the purposes of this article but I created it nonetheless in case I ever want to expand the dataset to cover __individual donations__. 

# Making Lots of Plots

Before plotting the data, we can do some rudimentary operations on the set to get a sense of its contents. Namely, we can can use __Panda’s__ `.describe()` function:

| Count | Mean | Std | Min | 25% | 50% | 75% | Max |
| :---: | :--: | :-: | :-: | :-: | :-: | :-: | :-: |
| 7.576330e+05 |  3.697573e+03 | 5.222328e+04 | -6.000000e+05 | -8.000000e+00 | 2.400000e+01 |  1.000000e+03 | 6881347e+06|

We can see that, of the dataset’s 757,633 rows, the largest donation was 6,881,347 while the smallest was strangely negative at -600,000. Luckily, __OpenSecret’s__ “Data Dictionary” clarifies that negative amounts represent refunds from the PAC to the original donor. We have quite a large standard deviation as well--around 3,698 to be specific, meaning that the variability of our dataset is incredibly high. This makes it harder to draw conclusions in some ways, but also means that its important to donations on an individual basis.

I decided to analyze two relationships: (1) the sources of funding for specific candidates and (2) the sourcing of funding across parties on a state-by-state basis. The former is meant to enable readers to investigate individuals who still may be politically active or retrospectively observe their economic motivations while the latter is meant to show where specific industries are most heavily invested, as well as which parties specific sectors tend to side with. 

I created a bar chart where the vertical axis is the total amount of money provided to a candidate from a specific industry while the horizontal axis represents the respective industry. Utilizing the interactive aspect of __Altair__, I added a `binding_select` dropdown to choose a specific candidate. I also added __ticks__ representing the __mean total contributions__ for a specific industry. To make this graph work, I actually had to use `alt.data_transformers.disable_max_rows()` to circumvent __Altair’s__ limit of 5,000 data points because of the size of my dataset. Moreover, I had to manipulate the column names to fix a time-consuming bug; due to the nature of __Altair__, 'name' cannot be provided as a field in a selector functon. Once I got past these barriers, I had my completed graph.

Next, I made a choropleth representing the total amount contributed by PACs per industry in each state for both liberal and conservative candidates. Although the dataset included Independent, Third-Party, and Libertarian candidates, I mainly focused on the Democratic and Republican Parties as I assumed that they would provide the starkest difference in terms of results considering that they are on opposite ends of the political spectrum. I implemented interactive features for the chart such that the user is able to select individual states and look at the total contributions across industries for that given state. Furthermore, I implemented a legend so the user can modify which industries are represented, enabling more granular analysis. Lastly, I added __ticks__ just as I did in the first graph to show the __mean total contributions__ in a specific state.

# Plots

<head>
  <!-- Import Vega & Vega-Lite (does not have to be from CDN) -->
  <script src="https://cdn.jsdelivr.net/npm/vega@5"></script>
  <script src="https://cdn.jsdelivr.net/npm/vega-lite@5"></script>
  <!-- Import vega-embed -->
  <script src="https://cdn.jsdelivr.net/npm/vega-embed@6"></script>
</head>

## Candidates

<div id="vis1"></div>

<script type="text/javascript">
  var spec = "https://raw.githubusercontent.com/joshborut/joshborut.github.io/5a4aa4fc8dfd7c61fa3ea20157e6bb4669ea012e/assets/graphs/bar_plot.json";
  vegaEmbed('#vis1', spec).then(function(result) {
    // Access the Vega view instance (https://vega.github.io/vega/docs/api/view/) as result.view
  }).catch(console.error);
</script> 

## Republican  
<div id="vis2"></div>

<script type="text/javascript">
  var spec = "https://raw.githubusercontent.com/joshborut/joshborut.github.io/5a4aa4fc8dfd7c61fa3ea20157e6bb4669ea012e/assets/graphs/R_plot.json";
  vegaEmbed('#vis2', spec).then(function(result) {
    // Access the Vega view instance (https://vega.github.io/vega/docs/api/view/) as result.view
  }).catch(console.error);
</script>

## Democrat  
<div id="vis3"></div>

<script type="text/javascript">
  var spec = "https://raw.githubusercontent.com/joshborut/joshborut.github.io/f5152c227e3be2e7c110df72b5b0ea0e126b5147/assets/graphs/D_plot.json";
  vegaEmbed('#vis3', spec).then(function(result) {
    // Access the Vega view instance (https://vega.github.io/vega/docs/api/view/) as result.view
  }).catch(console.error);
</script>

## Independent  

<div id="vis4"></div>

<script type="text/javascript">
  var spec = "https://raw.githubusercontent.com/joshborut/joshborut.github.io/5a4aa4fc8dfd7c61fa3ea20157e6bb4669ea012e/assets/graphs/I_plot.json";
  vegaEmbed('#vis4', spec).then(function(result) {
    // Access the Vega view instance (https://vega.github.io/vega/docs/api/view/) as result.view
  }).catch(console.error);
</script>

## Third-Party  
<div id="vis5"></div>

<script type="text/javascript">
  var spec = "https://raw.githubusercontent.com/joshborut/joshborut.github.io/5a4aa4fc8dfd7c61fa3ea20157e6bb4669ea012e/assets/graphs/T_plot.json";
  vegaEmbed('#vis5', spec).then(function(result) {
    // Access the Vega view instance (https://vega.github.io/vega/docs/api/view/) as result.view
  }).catch(console.error);
</script>

## Libertarian  
<div id="vis6"></div>

<script type="text/javascript">
  var spec = "https://raw.githubusercontent.com/joshborut/joshborut.github.io/5a4aa4fc8dfd7c61fa3ea20157e6bb4669ea012e/assets/graphs/L_plot.json";
  vegaEmbed('#vis6', spec).then(function(result) {
    // Access the Vega view instance (https://vega.github.io/vega/docs/api/view/) as result.view
  }).catch(console.error);
</script>

# Analysis  

<div id="vis7"></div>

<script type="text/javascript">
  var spec = "https://raw.githubusercontent.com/joshborut/joshborut.github.io/5a4aa4fc8dfd7c61fa3ea20157e6bb4669ea012e/assets/graphs/bar_plot.json";
  vegaEmbed('#vis7', spec).then(function(result) {
    // Access the Vega view instance (https://vega.github.io/vega/docs/api/view/) as result.view
  }).catch(console.error);
</script>

Now equipped with this plot, we can isolate candidates to see how the influence of PACs may have manifested in their political carriers.

### Case Study: JD Vance

Vice President JD Vance formerly served as the Senator of Ohio from 2023 to 2025. JD Vance is in many ways in-line with modern Republicans; he is anti-abortion, isolationist, and anti-LGBTQ rights ([source](https://www.cbsnews.com/news/jd-vance-views-policy-record-2024/)). 


![JD Vance Plot 1](/assets/img/jd_vance_plot.png)

Our plot reveals that Vance recieved over $2.5 million in funding from ideologist PACs alone. Using the filter feature, we can remove the __ideology__ and __party__ categories to take a closer look at more "under the radar" donors.

![JD Vance Plot 2](/assets/img/jd_vance_filtered_plot.png)

We see that, other than __lawyers and lobbyists__ (this source of data is not particularly helpful as lobbying is vague), most of Vance's funding came from the __energy and natural resource__ sector. After some research, I found that Vance opposes clean energy plans that pose a threat to conventional, high-emmision sources of energy like natural gas ([source](https://climatepower.us/research-polling/jd-vance-threatens-our-clean-energy-boom/)). Considering that he recieved over $200,000 from energy PACS--far more than the average candidate--and his alignment with energy companies, it is important to be aware as a voter of his potential bias. This is especially important given his current influence as the vice president. 

![Ohio Plot](/assets/img/ohio_plot.png)

When we reference Ohio as a whole, we also see that __energy__ makes up a disproportionate amount of Vance's funding relative to other Ohio candidates. Additionally, we observe that Ohio received $88,576,765 in funding, indicating that energy companies target even less impactful states.

Analyzing how JD Vance interacted with his PACs serves as an example of how this article can be leveraged by voters to uncover the driving forces behind candidates. While this is not neccessarily the most scientific approach, it is still informative and allows constituents to make politcally conscious decisions. 

# Takeaways

I found that this project served as a good refresher of all the skills we learned this year in terms of graphing with __Altair__ and using statistical analysis. I also found that I struggled the most, but also learned the most, with this real-world dataset given that it wasn’t as simple as clicking the download button on __Kaggle__; instead, I had acquire the data through outreach where I defended my project, grapple with the unique naming conventions of __OpenSecrets__, and 

While the data I used is from prior election cycles, I hope that it will serve as an informative tool for voters looking to evaluate where candidates source their money from. Some candidates' names are recognizable and still active on the United States’ political stage, but the ones that aren’t still serve a purpose in this project. They not only provide insight into which PACs may reach out to a constituent, but also reveal which candidates certain industries tend to favor. Regardless of the impact of this small, somewhat scientific study, it still stands as a meaningful contribution in pursuit of political transparency in the United States.

### Additional references

* https://medium.com/data-analytics-at-nesta/vega-altair-interactive-maps-d9c3bd77af50

* https://medium.com/@dr.junghoonson/using-altair-to-building-dynamic-interactive-choropleth-maps-8e7e8b051227

* https://altair-viz.github.io/gallery/us_incomebrackets_by_state_facet.html