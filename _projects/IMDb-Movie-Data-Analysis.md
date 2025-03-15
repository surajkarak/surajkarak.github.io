---
layout: page
title: What does IMDb data say about the Golden Age of cinema?
description: Analyzing movie ratings using IMDb data
img: assets/img/imdbdata/buster-keaton-smile.gif
importance: 5
category: fun
---

This project analyses the IMDb movie ratings, and looks at trends, patterns, and factors that influence the reception of a film. By analysing the IMDb dataset, we explore movie ratings across genres, time periods, and key metrics such as weighted ratings.

## Tech used 

- R and Quarto in RStudio

## Data set

For this analysis I used the <a href="https://developer.imdb.com/non-commercial-datasets/"> IMDb Non-commercial dataset </a>, a repository of movie-related information sourced from IMDb. The main datasets that we will use are the following:

- name.basics, which comprises data about the names of artists (directors, actors etc.) along with their details such as birthYear and titles they are associated with
- title.basics includes data on the actual titles of shows - movies, TV series, documentaries etc. - along with their details
- title.crew includes data on how each title is connected to the director and writers
- title.ratings is the most important dataset in this, and contains the average rating for each title and number of votes that each title has received

**Note**: If you are trying to replicate this project, the dataset files are too big in size and reading them can take too long. To improve the speed of reading large files, R provides some packages for this purpose like data.table and readr, which optimize the reading efficiently. These packages are often faster than base R functions for reading large datasets. You might also want to avoid reading these large files every time RStudio is launched. When a large file is read into RStudio and then RStudio is closed, the file will not automatically remain loaded in the R environment the next time it is opened. You can explicitly ask it save the files to an RData or other file format. To avoid the time it takes to read the large file each time RStudio is opened, the dataframes can be saved to a binary RData file using the saveRDS() function and loaded again using readRDS() in subsequent sessions.

## Top rated movies 
To get the top rated movies, I joined the ratings and title_basics and filter by movies (while also ignoring adult films) to get the names of the movies along with their ratings. But these include numerous films with averageRating a full 10 out of 10, but with less than 10 votes. These may be either really obscure films that no one saw, or more likely, student films and shorts that were not serious productions which somehow managed to find their way into the database. So I put a threshold on the number of votes and also looked at a better metric.

Weighted Rating is a metric that takes into account both the average rating and the number of votes to provide a more balanced measure of a movie’s quality. It aims to reduce the impact of a small number of high ratings or low ratings on the overall ranking. The concept is that a movie with a higher number of votes should be given more weight than a movie with fewer votes. In simple terms, it values movies that have a higher average rating and a higher number of votes more than those with just a high average rating.

One common formula for calculating Weighted Rating is the IMDb Weighted Rating Formula:


$$ \text{Weighted Rating (WR)} = \left( \frac{v}{v + m} \right) \times R + \left( \frac{m}{v + m} \right) \times C $$


Where:

WR = Weighted Rating

R = Average Rating of the movie

v = Number of votes for the movie

m = Minimum number of votes required to be listed in the rankings

C = Mean rating across the entire dataset

Using the weightedRating, applying a threshold of minimum 10,000 votes and normalizing the votes to get a color scale:

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/imdbdata/topmovies.png" title="toprated movies" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

This reveals what is more common knowledge about the IMDb ratings, as it shows the top 10 rated films being similar to the ones at the IMDb Top 10 list. Interestingly, The Shawshank Redemption and The Dark Knight has a higher vote count, which makes sense since these are also wildly popular films that were seen by people during release in theatres and also on DVD and home viewing later.

## The 1920s-1940s: Golden Age of Cinema?

Exploring the weightedRating averaged for all films in a year, it looks like the decade 1920s-1930s was the best.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/imdbdata/ratingovertime.png" title="rating averaged per year" class="img-fluid rounded z-depth-1" %}
    </div>
</div>


## Silent cinema, German expressionism are all-time classics

Diving into the movies in the peak period of 1920s.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/imdbdata/top1920movies.png" title="top 1920s movies" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

The top ranked films from the 1920s (1915 to 1930) do bring out some all-time classics. A common pattern in this list involves silent cinema (which is expected for films of that era), Charlie Chaplin and Buster Keaton (pioneers in physical comedy) as well as gems of German expressionist cinema, namely Metropolis, Sunrise and The Cabinet of Dr. Cagliari.

## Nolan, Tarantino, Miyazaki among directors who make highly rated films

I also looked at the filmography of directors and the average ratings of their movies. For this, I had to merge the ratings dataframe I worked with until now, with the name dataframe.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/imdbdata/topdirectors.png" title="top 1920s movies" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

## Drama, comedy and action genres are the most popular

I also exploreed whether particular film genres exhibit a tendency to receive higher average ratings. In the IMDb dataset, the genres are not mutually exclusive, i.e., the same film could have been tagged “drama” and “history” in the dataset, separated by a comma. So I first separated these into different columns and then grouped by genre to get summary statistics.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/imdbdata/ratingpergenre.png" title="top 1920s movies" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

It seems like News, Film-Noir, Documentary and War have a higher rating on average. But these don’t have the most films in the genre. So although there are only few films in these, they are generally well rated.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/imdbdata/moviespergenre.png" title="top 1920s movies" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

Biography, Animation and Drama are some of the top ranked genres with more films (in excess of 400).

## The best rated films are around 100-120 minutes long

Lastly, I also looked at whether film duration has a measurable impact on audience ratings. For this I simply plotted the relationship between the runtime (in minutes) to the weighted Rating of each film.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/imdbdata/runtimevsrating.png" title="top 1920s movies" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

There is no clear correlation between runtime and weightedRating. But we do see that the majority of highly rated films are in the range of 100 to 200 minutes. And also noticeable is the fact that there are some outliers with runtimes of over 300 minutes (even up to 550 minutes). These outliers are usually higher rated (over 7.5) and the ratings don’t tend to decrease the longer the film gets. However, keep in mind that there was a 10,000 vote limit for this threshold, meaning that this insight is only applicable for those films which had more than this threshold of votes. And also worth considering is the behavior and traits of the users who do manage to watch the entire length of these high-runtime films. It is reasonably to expect that these users may have liked the film or found it engaging enough to sit through the entire runtime, which makes it more likely for them to give the film a higher rating. In contrast, those who did not watch the entire length of the film may have not bothered voting a rating at all.

