---
layout: post
title: All Things Altair
subtitle: Exploring Visualizations
cover-img: /assets/img/altair_cover.png
thumbnail-img: /assets/img/altair_thumbnail.png
share-img: /assets/img/altair_cover.png
tags: [labs]
author: Joshua Borut
---

I began my search for an interesting visualization on the *New York Times* interactive graphics webpage. There, I found a graph that caught my attention...

# Graph 1: Summer Temperatures

![NYT Graph](/assets/img/nyt_graph.png)

This graph compares climate during summers in the Northern Hemisphere between 1951 and 1980 to that from the recent years of 2013 to 2023. I found it interesting because it captures the upward trend in temperature in an easily understood manner. Moreover, it is timely considering how climate change impacted the rampant spread of fires in Los Angeles. 

The graph's only __mark__ is area, while the __channels__ are color, height, and position. The graph bins the __quantitative__ variable of temperature into categories ranging from "extremely cold" to "extremely hot." However, this binning seems to be at a detriment to the graph as the viewer has little information as to what qualifies as "hot" or "cold."

I sought to replicate this graph, but with generated data. Even with __Vega Altair's__ documentation, I struggled making many of the channels. For instance, I tried inserting vertical lines but kept getting errors as the stacked nature of the graph limited the methods I could use. In the end, I generated the following graph:

<head>
  <!-- Import Vega & Vega-Lite (does not have to be from CDN) -->
  <script src="https://cdn.jsdelivr.net/npm/vega@5"></script>
  <script src="https://cdn.jsdelivr.net/npm/vega-lite@5"></script>
  <!-- Import vega-embed -->
  <script src="https://cdn.jsdelivr.net/npm/vega-embed@6"></script>
</head>
<body>

<div id="vis"></div>

<script type="text/javascript">
  var spec = "https://raw.githubusercontent.com/joshborut/joshborut.github.io/403e2f7420c868b2c7139827920eb95f7c6a5e9c/assets/graphs/summer_temps.json";
  vegaEmbed('#vis', spec).then(function(result) {
    // Access the Vega view instance (https://vega.github.io/vega/docs/api/view/) as result.view
  }).catch(console.error);
</script>
</body>

# Graph 2: Immigration

This graph also comes from the *New York Times*, but it instead focuses on immigration. I took interest in this graph because of the recent rise in anti-immigraiton sentiment following President Donald Trump's inaugeration. The graph's __marks__ are lines and area while the __channels__ are height, position, and color. It compares the __quantitative__ variable of percent to the __temporal__ variable of time. The graph reveals a steep dip in immigration from Europe and an increase in immigration from the Americas begining in around 1970. This makes sense considering the passage of the *Hart Celler Act* that overhauled the immigration system in 1965.