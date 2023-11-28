---
layout: page
title: Discovering Waste Price Determinants 
description: Finding correlations between the price of waste and various potential price determinants.
img: assets/img/KIPA/KIPA.png
importance: 1
category: work
# related_publications: einstein1956investigations, einstein1950meaning
---

This was a group project that we did for the course MDSSB-DSAI-01 DIGITAL TRANSFORMATION as part of our Masters program in Data Science at Constructor University Bremen. The goal was to identify correlations and patterns between waste prices and potential price determinants such as weather, energy, and business cycle factors, and to recommend which were the best ones to consider for future prediction and modeling work.

## The wPreis Dataset

The dataset was provided to us by the professor and teaching assistant. It contained the price of waste as recorded by an anonymous waste recycling company in Bremen from Sep 2020 until Sep 2023. The prices corresponded to the amounts that the company paid for collecting waste from various sources (positive values), or which the company paid for collecting waste (negative values.

There were also 10 unique clusters or collection of Postleitzahl (postal code in Germany) and had 4 unique product categories – 'A2 - geschreddert', 'A1 & A2 - geschreddert', 'A2 & A3 - geschreddert', 'A3 - geschreddert'. There were no null values.

The wPreis for all 3 categories in the same cluster are somewhat correlated, with occasional deviations. This is observed in all clusters more or less. For some clusters, there is only one waste category, \*A1 & A2\* for \[1, 4, 6, 7, 8, 9\].

::: row
```         
<div class="col-sm mt-3 mt-md-0">
    {% include figure.html path="assets/img/KIPA/data.png" title="example image" class="img-fluid rounded z-depth-1" %}
</div>
```
:::

::: caption
```         
- Correlations observed within categories in the same cluster
```
:::

## **Exploring Potential Price Determinants**

We explored a few different variables to see how they correlated with the wPreis values.

### 1. Weather

**First Approach:** Using data extracted from an [open meteo](https://open-meteo.com/) free API. Parameters include temperature at 2 meters, wind speed at 10 meters, precipitation, rain, and snowfall.Accessed using latitude and longitude of cities in each cluster.

**Second Approach:** Data obtained from [Deutscher Wetterdienst (DWD)](https://www.dwd.de/DE/Home/home_node.html) – the German Meteorological Service – they provide weather data dating back to the 1830s Parameters include temperature mean and max.

The coefficients of wPreis with all other weather variables are too small. Hence this may not be a good determinant.

For the Cluster \['40', '41', '42', '44', '45', '46', '47'\], Correlation between wPreis and windspeed: 0.06 and Correlation between wPreis and temperature: 0.03

### 2. Energy 

For energy, we looked at 3 variables – oil, electricity and gas prices.

#### Electricity

We used data from [netztransparenz.de](https://www.netztransparenz.de/EEG/Marktpraemie/Spotmarktpreis) (ct/kWh) which contains electricity prices for the whole of Germany from Jan 2021. This was the best choice we could find on electricity prices.

Although visually it looks like there was some correlation in some parts, the coefficients are not signfiicant. A2 & A3 category alone has 0.52 though.

Checking for correlation with a 1-week lag also does not improve the coefficients much.

#### Oil

For oil, there was no data available on prices per region in Germany (this may not make sense either). So we looked at [Global Oil and Gas Market Prices as a proxy from Yahoo finance](https://finance.yahoo.com/quote/CL%3DF/history?period1=1599436800&period2=1694649600&interval=1wk&filter=history&frequency=1wk&includeAdjustedClose=true), and assumed that relative changes in global prices would have a proportional impact on the same prices in Germany. We specifically tracked the variable \*\*Adjusted Close Price (Adj Close)\*\* which accounts for events such as stock splits and dividend payments.

We found that there was a close correlation from Sep 2020 to around Jan 2022 for both categories. For A3 - geschreddert, corr. roughly continues until Jun 2022, then inverse corr. until Dec 2022 and then low corr. until Sep 2023. For A2 & A3 - geschreddert, it continues from Jan 2022 to around Mar 2022, then inverse corr. until Dec 2022, and similar to the other category, loses the correlation after that.

Correlation coefficients also validate this, with 0.6 for A3 - geschreddert category and 0.62 for A2 & A3 - geschreddert which are above 0.5 and hence significant.

Something remained consistent until Jan 2022. After that things began going in the opposite direction. And from Dec 2022 onwards they become less correlated.

#### Gas

Gas prices collected from Yahoo Finance for the whole of Germany Exploration of correlation with Adjusted Close Price (Adj Close)

### 3. Business Cycle

For exploring correlation of wPreis with the business cycle we looked at data from DAY. DAX (Deutscher Aktien Index) data obtained from Yahoo Finance Calculated the adjusted close price of the weekly average for correlation analysis

### 4. Construction

Data on construction permits number (per land per month) taken from Statistik der Baugenehmigungen (code 31111)

::: {.row .justify-content-sm-center}
```         
<div class="col-sm-8 mt-3 mt-md-0">
    {% include figure.html path="assets/img/6.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
</div>
<div class="col-sm-4 mt-3 mt-md-0">
    {% include figure.html path="assets/img/11.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
</div>
```
:::

::: caption
```         
You can also have artistically styled 2/3 + 1/3 images, like these.
```
:::

The code is simple. Just wrap your images with `<div class="col-sm">` and place them inside `<div class="row">` (read more about the <a href="https://getbootstrap.com/docs/4.4/layout/grid/">Bootstrap Grid</a> system). To make images responsive, add `img-fluid` class to each; for rounded corners and shadows use `rounded` and `z-depth-1` classes. Here's the code for the last row of images above:

{% raw %}

``` html
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