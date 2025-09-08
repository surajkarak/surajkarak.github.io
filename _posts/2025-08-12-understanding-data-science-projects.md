---
layout: post
title: Understanding data is half the work
date: 2025-08-12 17:16:32
description: It's critical to spend enough time on this first key step in every dat science task
tags: data-science
categories: data-science
thumbnail: /assets/post_imgs/understanding-data-science-projects.jpg
featured: false
related_posts: false

---
 

It might sound obvious but in almost all my data science projects, the most crucial step has been to understand the data. The additional value of this step in downstream tasks is self-evident but one cannot overstate how much you stand to lose by ignoring this step. 

So often, I have made the mistake of taking this step lightly, merely skimming through the schema of dataframes or tables I am working with, thinking to myself “Yeah I get it, let’s move on to the modelling, ML and visualization (meaty stuff)”, only to regret it later. I have almost always had to go back and spend a lot more time understanding the features, their data types and what they stand for in the business context, in order to resolve issues with the downstream tasks. 

Some simple examples from my work:

1. Grouping across time ranges

In one project, I had assigned a simple data preprocessing task to a junior data analyst. The task was to aggregate weather data for a number of cities across time and ready for time series analysis. Trusting the work, we proceeded to continue the analysis, only to find later that the data was not grouped per country. It might sound trivial but these are the kinds of errors that can happen if the focus is on getting the code for data transformations without understanding what the data looks like.  

2. Overlapping week numbers at the end of the year 

Another simple instance was the “overlapping” of week numbers in another time series project. This was my own fault when I converted the date-time values of a time series into week numbers. I realised later that since the last week (span of 7 days in this context) of a year overlapped with day of the new year, which led to some inconsistency in the numbering of weeks. Not always unavoidable but spotting it as early as possible could lead to a lot of time saved in the analysis and modeling. 

3. Understanding data (Protein Data Bank or PDB files) from a new domain 

Yet another example was in my work with the molecular biology research group at my university. In this particular case, I really had to spend a lot of time understanding the data and what it meant as I was not an expert in the domain. At first I tried to power through the code and let the results “show me” what I was doing. But this did not work as I hit a number of roadblocks in the code, and the only solution was to go back to the data, discuss with the research team and understand what I was dealing with in depth. 

4. Merging adgroup names from 2 different sources

In my work with rebuy, one project required combining names of adgroups from Google Ads performance data and Campaign names from Amplitude, tracked through UTM campaigns. The UTM conventions for tracking was done by a team different from the one that set up the Google ads, which meant that the names were not matching. To address this, I had to sit with the stakeholder and try to discuss with the teams who were responsible for setting up the two different conventions, understand what each represented and why they were set up that way. 

These experiences reinforced my belief that one should not really take the first step lightly. And that sufficient time must be set aside to dig deep into the data being analysed and explored before going into modelling. If you are working with a client, take the time to sit with the stakeholders to understand the business, context and data.