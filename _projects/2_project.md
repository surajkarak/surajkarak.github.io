---
layout: page
title: Audience segmentation using unsupervised learning
description: kMeans clustering of the web visitors to Google’s Merchandise Store  
img: assets/img/AudSeg/Audseg.png
importance: 2
category: work
giscus_comments: false
---

Marketers do adopt some standard segmentation and clustering techniques to identify and group customers based on their purchase behaviour. But this project explores how a similar clustering approach can be used to segment audiences at the top stage of the funnel, i.e., those that are in the awareness stage and may not have converted yet. Brands spend a lot of time and resources trying to understand their audience before they convert to a customer. Identifying the different groups or clusters of audience characteristics at this stage will help marketers device appropriate strategies, campaigns and tactics to ensure that they target those visitors with the best chance of converting and building loyalty with them further down the line.

In this project, we performed clustering of the web visitors to Google’s Merchandise Store using kMeans clustering, using their web analytics data. The clustering stage is preceded by the standard steps of data science, including cleaning, exploration, feature selectiona and preprocessing.

**Data Collection **
The data used for this project are from select files used for the Google Customer Revenue Prediction Competition on Kaggle.

The first dataset involves 903653 observations of visits to Google’s Merchandise Store for a period of a year between the 1st of August 2016 to 1st of August 2017.

The second is another dataset related to this same Google store data, that includes more numerical variables like Sessions, Average Session Duration, Bounce Rate, Transcations and Goal Conversion Rate. This was not part of the original Kaggle competition but it came to be as a result of a “leak” in the competition dataset which sparked some discussion in their forum after which the competition was shut down. https://www.kaggle.com/c/ga-customer-revenue-prediction/discussion/68235#401950

The data has been provided by a Kaggle user in corresponding csv files here https://www.kaggle.com/datasets/satian/exported-google-analytics-data?select=Train_external_data_2.csv. We can use this to marge into the first dataset when needed.

**Data Exploration **

Some observations from exploring the data:

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/Exploration.png" title="Exploration" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

From these graphs, we can see that:

Channels: Google search is the most popular source of visits, followed by Social channels and Direct. Paid search and ads are not a major source.
Continents: The Americas receive a greater number of visits compared to any other continent, nearly twice as many as Asia and Europe combined.
Countries: When considering individual countries, the USA and India attract the highest number of visitors, followed by the UK.
Browser: Chrome is the most commonly used browser, with Safari being the second most popular choice.
Device: Most visitors come to the website on a desktop, but there are also a lot of visitors on mobile and a few from tablets as well
OS: Most visitors are Windows users, then Apple Mac users. Then comes Android users on mobile, followed by iOS users. This may just be useful to get an idea of visitors’ “lifestyle choices,” such as their preferred operating systems (for example, Mac users versus PC users).

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/countries.png" title="Countries" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The US, India, UK, Canada and Vietnam are the top 5 countries sending traffic.
</div>

It is also useful to explore how the visits change over time, throughout the year and on a weekly basis to see if there are any patterns we can take advantage of in the eventual clustering.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/time.png" title="Time" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
As we can see here, the store experienced a higher number of visits during the period from October to January. This can be attributed to the start of the holiday season, as people engage in extensive shopping for themselves or to purchase gifts for others.

Unexpectedly, the number of visits is lower on weekends compared to weekdays.

The timeframe from approximately 11 am to 11 pm appears to be when the store receives the highest number of visitors. However, it is important to note that this observation may not provide a valuable insight due to visitors coming from different time zones. To check how different the visitStartTimes can be in different countries, we can also drill down to the distribution of visits per hour of the day for some of the top countries by visits.


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
