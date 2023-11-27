---
layout: page
title: Discovering Waste Price Determinants 
description: Finding correlations between the price of waste and various potential price determinants.
img: assets/img/KIPA/KIPA.png
importance: 1
category: work
# related_publications: einstein1956investigations, einstein1950meaning
---

The goal of this project is to identify correlations and patterns between waste prices and potential price determinants such as weather, energy, and business cycle factors.

The wPreis Dataset
- Price of waste from around Sep 2020 until Sep 2023
- Could be negative (client received payment for waste) or positive (client paid for waste disposal)
- 10 unique clusters (collection of Postleitzahl) with 4 unique product categories
- No null values

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/KIPA/data.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    - Correlations observed within categories in the same cluster
</div>

**Exploring Potential Price Determinants**

Weather - Fir   st Approach
    Data extracted from an open meteo free API
    Parameters include temperature at 2 meters, wind speed at 10 meters, precipitation, rain, and snowfall
    Accessed using latitude and longitude of cities in each cluster

Weather - Second Approach
    Data obtained from Deutscher Wetterdienst (DWD), the German Meteorological Service
    Provides weather data dating back to the 1830s
    Parameters include temperature mean and max

Energy - Electricity
Data from netztransparenz.de (ct/kWh) for the whole of Germany from Jan 2021

Energy - Oil
    Global Oil and Gas Market Prices from Yahoo Finance used as proxy data
    Adjusted Close Price (Adj Close) considered for analysis

Energy - Gas
    Gas prices collected from Yahoo Finance for the whole of Germany
    Exploration of correlation with Adjusted Close Price (Adj Close)

Business Cycle - DAX
    DAX (Deutscher Aktien Index) data obtained from Yahoo Finance
    Calculated the adjusted close price of the weekly average for correlation analysis

Construction
    Data on construction permits number (per land per month) taken from Statistik der Baugenehmigungen (code 31111)


<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.html path="assets/img/6.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.html path="assets/img/11.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    You can also have artistically styled 2/3 + 1/3 images, like these.
</div>


The code is simple.
Just wrap your images with `<div class="col-sm">` and place them inside `<div class="row">` (read more about the <a href="https://getbootstrap.com/docs/4.4/layout/grid/">Bootstrap Grid</a> system).
To make images responsive, add `img-fluid` class to each; for rounded corners and shadows use `rounded` and `z-depth-1` classes.
Here's the code for the last row of images above:

{% raw %}
```html
<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.html path="assets/img/6.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.html path="assets/img/11.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
```
{% endraw %}
