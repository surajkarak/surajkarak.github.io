---
layout: page
title: How politically biased are some of the top subreddits? 
description: Using NLP to detect political bias in some top subreddits
img: /assets/img/RedditBias/Subreddit_Political_Bias.png
importance: 1
category: work  
---

One of the topics I used to follow closely was the polarisation among online communities. Certain communities tend to attract audiences of a specific leaning, in their political or other viewpoints.

So, having analysed the responses from [ChatGPT for political bias](https://surajkarak.github.io/projects/NLP-ChatGPT-Bias/), I decided to see if I could replicate the same thing for reddit communities, or subreddits as they are known.

First, I had to think about which subreddits to analyse and how. There are countless subreddits that could lend themselves nicely to such an analysis (even some harmless ones like r/awww or r/wholesomemes might be interesting to look at) but for this analysis I decided to look at some of the top subreddits by subscribers.

-   worldnews

-   politics

-   worldevents

-   ukpolitics

-   AskReddit

And I could not analyse all the content in these subreddits since the beginning - that would take days or weeks or more to extract and maybe still doable with some hacks. So I decided to look at the latest posts from these subreddits (more specifically, those which show up when you sort by “new” at the top).

## What I used

-   Python in Visual Studio for extraction and analysis
-   Reddit API for extraction of the posts and their metrics
-   SQL for storing the extracted data into and pulling from a database

## Data extraction

To extract the data, I used [Reddit’s API](https://www.reddit.com/dev/api/) after registering, creating an app, creating the credentials and requesting the access token.

In every API call, I would pull the new posts from one subreddit by specifying the endpoint as "https://oauth.reddit.com/r/{subreddit_name}/new".

I had to set a limit of 100 but doing this in 10 loops allowed me to get 1000 posts. After pulling the 1000 posts, I would save this into a SQLite database using SQL queries. This is so that every time All this was defined in a function and I would just call the function by passing the name of the subreddit. Then I queried all the data from the database into a dataframe. I ran the code notebook I didn’t have to execute the API call, and could just work with the stored database.

## Data cleaning and exploration 

In total there were 4795 posts extracted across the 5 subreddits. The data extracted included features such as

-   id, an integer ID for the post

-   title - the content of the post which we will analyse, as string

-   numerical metrics for posts such as upvotes, downvotes, upvote_ratio

The title is what I want to explore and analyse so I apply the basic cleaning steps that are required on this column – changing to lower case, removing stopwords (using STOPWORDS from the NLTK library) and punctuation. I also remove the most frequent words

## Sentiment analysis

First I did a quick sentiment analysis to check how positive or negative each of the subreddits are. For this I used the AFINN and TextBlob Lexicon based analysis. (You can use better techniques including VADER and pretrained models for this but I just wanted a quick check before proceeding to the political bias). I got the sentiment scores which vary from -18 to just under 10. 


<div class="col-sm mt-3 mt-md-0">
    {% include figure.html path="/assets/img/RedditBias/sentiment_score_distribution.png" title="Sentiment Score Distribution" class="img-fluid rounded z-depth-1" %}
</div>


Categorising the scores above 0 to be positive, below 0 to be negative and 0 as neutral, I checked how each subreddit fared for each category.

 
<div class="col-sm mt-3 mt-md-0">
    {% include figure.html path="/assets/img/RedditBias/sentiment_category_distribution.png" title="Sentiment Category Distribution" class="img-fluid rounded z-depth-1" %}
</div>

Interestingly, all the politics-related subreddits were leaning towards negative (not surprising as these subreddits often or even mostly share posts from news outlets, which of course are catered towards grabbing attention and building audience which means they tend to be negative). AskReddit is the only one which has a neutral majority, which makes sense as that subreddit is not exclusively about politics.

## Political bias

To analyse the political bias, I decided to use a labelled training dataset from [PoliticalBias_AllSides_Txt](https://huggingface.co/datasets/valurank/PoliticalBias_AllSides_Txt) at Hugginface. This contains a corpus of articles that has already been labeled with a specific bias — from left, right or center. These are 17,362 articles labeled left, right, or center by the editors of [allsides.com](http://allsides.com/). Articles were manually annotated by news editors who were attempting to select representative articles from the left, right and center of each article topic. In other words, the dataset should generally be balanced — the left/right/center articles cover the same set of topics, and have roughly the same amount of articles in each.

### Training

The dataset comes in 3 folders, Center Data, Left Data and Right Data, each containing text files.

The files in each folder are read and their content extracted into one dataframe df, with two columns: ‘text’, for the content, and ‘bias’ corresponding to the folder in which they came in, as ‘Center’, ‘Right’ or ‘Left’.

When put together, the dataframe has 17362 rows, with no missing values. The dataframe’s rows need to be shuffled first to avoid all rows of the same labels being together. Next, the bias values are encoded as ‘Left’: 0,’Center’: 1 and ,’Right’: 2, to do numerical calculations. Plotting the distribution of the bias values shows the following graph.

<div class="col-sm mt-3 mt-md-0">
    {% include figure.html path="/assets/img/RedditBias/labelled_data_distribution.png" title="Labelled Data Distribution" class="img-fluid rounded z-depth-1" %}
</div>

This shows that the training dataset itself will not be perfectly balanced, as there are more left-biased samples than others.

Next, I went through the process of cleaning these labelled articles – removing unnecessary punctuation and symbols, making all text lowercase and removing stopwords. Then I tagged the article texts to the bias values as per the labels in the corpus in tagged documents. The corpus is split into training and test data.

Then these training and test datasets had to be tokenised and passed to the TaggedDocument() constructor along with the corresponding bias label. The resulting TaggedDocument objects are collected in a list and stored in the train_tagged and test_tagged variables. These TaggedDocument objects are used as input to train the Doc2Vec model, where each document in the dataset is represented by a unique vector.

The Doc2Vec algorithm encodes a whole document of text into a vector are able to represent the theme or overall meaning of a document. It uses the word similarities learned during training to construct a vector that will predict the words in a new document. This process creates a mapping between words and their IDs, which is stored in the model. Once the vocabulary is built, the model can then use it to learn the patterns and relationships between words in the training data.

I fit the training dataset into 3 classification algorithms: Naive Bayes Classifier, Random Forest Classifier and Support Vector Machines, and then the test data is used to make predictions to evaluate the best of the three algorithms.

The accuracies for the three different algorithms are as follows.

|                |      |
|----------------|------|
| Naive Bayes    | 0.83 |
| Random Forest  | 0.77 |
| Support Vector | 0.92 |


Since the Support Vector classifier has the best results on the test data set, I decided to use that predict the biases for the subreddit texts.

### Prediction

Next, I wanted to predict the bias for the subreddit texts using SVC. For this, I first tokenised the cleaned text from the subreddits and vectorised it using the same Doc2Vec model. Passing these vectors to the SVC function, I got the bias values (0, 1 or 2) in a new column ‘Label’. Grouping the labels into categories {0: 'Left', 1: 'Center', 2: 'Right'} and plotting a distribution gave me this:

   
<div class="col-sm mt-3 mt-md-0">
    {% include figure.html path="/assets/img/RedditBias/subreddit_bias_distribution.png" title="Subreddit Political Bias Distribution" class="img-fluid rounded z-depth-1" %}
</div>


As expected, all the subreddits show a left-leaning bias. There are some right-leaning bias but very little neutral. It is important to keep in mind that the reddit community tends to be skewed towards younger, progressive, tech and digitally savvy crowds (although there are subreddits that are almost fully conservative and even extreme right-wing).

Interestingly, the AskReddit community seems to be the one with the most difference between the biases, with almost 90% left bias. I’m not sure why this could be the case, but perhaps building on the earlier point about the audience and users of reddit, it should also be noted that the posts in this subreddit are almost always written by users (user-generated original content) while the other subreddits are almost always posts shared from news articles and websites. The latter therefore could involve content that come with a right-bias (even if the discussion and comments in the corresponding posts in those subreddits may involve left-leaning viewpoints).

#### **Where to go from here and how could we use this information?**

Firstly, we could compare the results with those produced by other models, even pre-trained LLM models. One could also analyse the discussion in the comments section under the subreddit’s posts and layer them on top of the post content itself to get a more holistic view of the bias in that specific thread. This will give us a better idea of what a general user can expect when they land on and spend time with the thread.

There are also other more extreme and potentially more incendiary subreddits that could analyse. One interesting use case I can think of is to have real-time monitor of the bias of a thread (and a continuous one at that, instead of a discrete one) so that visitors and “lurkers” can get an idea of whether they should spend time on it before proceeding. One could also give users and option to view a more “balanced” feed where, depending on the overall bias, more content from other viewpoints are surfaced, so that readers can avoid spiralling into the much-talked about political “echo-chambers”.