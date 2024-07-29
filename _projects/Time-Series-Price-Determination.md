---
layout: page
title: Discovering waste price determinants 
description: Finding correlations between the price of waste and various potential price determinants.
img: assets/img/KIPA/KIPA.png
importance: 6
category: work
# related_publications: einstein1956investigations, einstein1950meaning
---

This was a group project that we did for the course MDSSB-DSAI-01 DIGITAL TRANSFORMATION as part of our Masters program in Data Science at Constructor University Bremen. The goal was to identify correlations and patterns between waste prices and potential price determinants such as weather, energy, and business cycle factors, and to recommend which were the best ones to consider for future prediction and modeling work.

## Tech used

- Python in Visual Studio
- Time series analysis

## The wPreis Dataset

The dataset was provided to us by the professor and teaching assistant. It contained the price of waste as recorded by an anonymous waste recycling company in Bremen from Sep 2020 until Sep 2023. The prices corresponded to the amounts that the company paid for collecting waste from various sources (positive values), or which the company paid for collecting waste (negative values.

There were also 10 unique clusters or collection of Postleitzahl (postal code in Germany) and had 4 unique product categories – 'A2 - geschreddert', 'A1 & A2 - geschreddert', 'A2 & A3 - geschreddert', 'A3 - geschreddert'. There were no null values.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/KIPA/data.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

The wPreis for all 3 categories in the same cluster are somewhat correlated, with occasional deviations. This is observed in all clusters more or less. For some clusters, there is only one waste category, \*A1 & A2\* for \[1, 4, 6, 7, 8, 9\].

## **Exploring Potential Price Determinants**

We explored a few different variables to see how they correlated with the wPreis values.

### 1. Weather

**First Approach:** Using data extracted from an [open meteo](https://open-meteo.com/) free API. Parameters include temperature at 2 meters, wind speed at 10 meters, precipitation, rain, and snowfall.Accessed using latitude and longitude of cities in each cluster.

**Second Approach:** Data obtained from [Deutscher Wetterdienst (DWD)](https://www.dwd.de/DE/Home/home_node.html) – the German Meteorological Service – they provide weather data dating back to the 1830s Parameters include temperature mean and max.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/KIPA/weather.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

The coefficients of wPreis with all other weather variables are too small. Hence this may not be a good determinant.

For the Cluster \['40', '41', '42', '44', '45', '46', '47'\], Correlation between wPreis and windspeed: 0.06 and Correlation between wPreis and temperature: 0.03

### 2. Energy

For energy, we looked at 3 variables – oil, electricity and gas prices.

#### Electricity

We used data from [netztransparenz.de](https://www.netztransparenz.de/EEG/Marktpraemie/Spotmarktpreis) (ct/kWh) which contains electricity prices for the whole of Germany from Jan 2021. This was the best choice we could find on electricity prices.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/KIPA/electricity.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

Although visually it looks like there was some correlation in some parts, the coefficients are not signfiicant. A2 & A3 category alone has 0.52 though.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/KIPA/elec_correlation.png" title="example image" class="img-fluid rounded z-depth-1" width="1294" height="450" %}
    </div>
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/KIPA/elec_correlation_lag.png" title="electricity correlation with 1 week lag" class="img-fluid rounded z-depth-1" width="1294" height="450" %}
    </div>
</div>

<div class="caption">
    Checking for correlation with a 1-week lag also does not improve the coefficients much.
</div>



#### Oil

For oil, there was no data available on prices per region in Germany (this may not make sense either). So we looked at [Global Oil Market Prices as a proxy from Yahoo finance](https://finance.yahoo.com/quote/CL%3DF/history?period1=1599436800&period2=1694649600&interval=1wk&filter=history&frequency=1wk&includeAdjustedClose=true), and assumed that relative changes in global prices would have a proportional impact on the same prices in Germany. We specifically tracked the variable Adjusted Close Price (Adj Close) which accounts for events such as stock splits and dividend payments.

We found that there was a close correlation from Sep 2020 to around Jan 2022 for both categories. For A3 - geschreddert, corr. roughly continues until Jun 2022, then inverse corr. until Dec 2022 and then low corr. until Sep 2023. For A2 & A3 - geschreddert, it continues from Jan 2022 to around Mar 2022, then inverse corr. until Dec 2022, and similar to the other category, loses the correlation after that.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/KIPA/oil.png" title="example image" class="img-fluid rounded z-depth-1" width="1294" height="450" %}
    </div>
</div>


Correlation coefficients also validate this, with 0.6 for A3 - geschreddert category and 0.62 for A2 & A3 - geschreddert which are above 0.5 and hence significant.


<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/KIPA/oil_correlation.png" title="electricity correlation with 1 week lag" class="img-fluid rounded z-depth-1" width="1294" height="450" %}
    </div>
</div>


Something remained consistent until Jan 2022. After that things began going in the opposite direction. And from Dec 2022 onwards they become less correlated.



#### Gas

Similar to oil, we used data the [Global Gas prices from Yahoo Finance](https://finance.yahoo.com/quote/NG%3DF/history?p=NG%3DF) for the whole of Germany and explored the correlation of wPreis with Adjusted Close Price (Adj Close).


<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/KIPA/gas.png" title="example image" class="img-fluid rounded z-depth-1" width="1294" height="450" %}
    </div>
</div>


<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/KIPA/gas_correlation.png" title="electricity correlation with 1 week lag" class="img-fluid rounded z-depth-1" width="1294" height="450" %}
    </div>
</div>


Unlike oil, for gas, the correlation is lower. The best we can say is that the general trend is rising for both from Jan 2021 until around Dec 2022 and after that the wPreis continues to rise while gas prices rise sharply and fall sharply. This is validated by lower coefficients (around 0.4).



### 3. Business Cycle

For exploring correlation of wPreis with the business cycle we used the [**DAX** - the **Deutscher Aktien Index**](https://finance.yahoo.com/quote/DAX/history?period1=1599955200&period2=1694563200&interval=1d&filter=history&frequency=1d&includeAdjustedClose=true&guccounter=1&guce_referrer=aHR0cHM6Ly93d3cuZ29vZ2xlLmNvbS8&guce_referrer_sig=AQAAALsVKvxUJU7SSHyDzboI1z8iQ-95y7S1toJIg2VLrZbrf37W4faU3xh85tMCeYeiNYfRBnbCjvToNimKt0kiy7mOCnb35Hq6HH9lGpYzfe5sgc8ApkLXnaSE2sDCdicidvgkiGkwhak_cly_pc1KzGCnm-XtgAPsc8XwTIPFq7Ew) or the **GER40** : a stock index that represents 40 of the largest and most liquid German companies that trade on the Frankfurt Exchange. From the dataset, we extract the adjusted close price of the weekly average of the GER40.


<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/KIPA/dax.png" title="example image" class="img-fluid rounded z-depth-1" width="1294" height="450" %}
    </div>
</div>


<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/KIPA/dax_correlation.png" title="electricity correlation with 1 week lag" class="img-fluid rounded z-depth-1" width="1294" height="450" %}
    </div>
</div>

There isn't any significant difference between the normal wPreis and the lagged wPries, they both range somewhat between **-0.45 to -0.63**


### 4. Construction

We also tried to explore whether there was any correlation between construction permits in Germany and the wPreis. Data on construction permits number (per land per month) was taken from [Statistik der Baugenehmigungen (code 31111)](https://www-genesis.destatis.de/). This variable was taken because it was the only one available with monthly frequency and per land.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/KIPA/construction.png" title="electricity correlation with 1 week lag" class="img-fluid rounded z-depth-1" width="1294" height="450" %}
    </div>
</div>

The main insight is that the pattern of correlations is the same. The correlation between actual waste prices and number of permits is large and negative. We assume it has something to do with the waste offer: usually the timespan between getting a construction permit and starting the construction itself is forced to be as short as possible =\> construction works start as soon as the permit is obtained =\> the waste is produced immediately =\> there is more waste offered on the market =\> the price decreases.


## **Conclusions**

Based on this analysis, we find that

-   Weather determinants show very low correlation with wPreis (which makes sense somewhat) and can be ignored.

-   Energy determinants show different variations with oil showing a somewhat significant correlation of around 0.6 (that too overall including ‘black swan’ events like the Russia-Ukraine situation. Gas shows lesser correlation than oil and so does electricity.

-   Business Cycle (DAX) shows some significant inverse correlation while construction permits show high inverse correlation for some clusters.