---
layout: page
title: Visualization of sentiment and narrative in Star Trek scripts
description: Boldly going where no sentiment analysis has gone before
img: /assets/img/startrek/startrek.gif
importance: 1
category: work   
---

As I was playing around with NLP and sentiment analysis, I remembered watching this wonderful clip of Stephen Fry talking about Star Trek years ago, well before I had even watched a single episode.

<div>

<iframe width="800" height="600" src="https://www.youtube.com/embed/mlpklo4VLak" title="Stephen Fry on Star Trek" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe> 

</div>

I must admit I was never a hardcore “Trekkie” but I did remember having a blast binging The Original Series during COVID.

Stephen Fry’s proposition that Kirk balances out Spock’s logic and McCoy’s emotion seemed like something that was worth testing using data. My first thought was to analyse the scripts of the episode using NLP.

## What I used

-   Python in Visual Studio for data cleaning and analysis
-   NLTK for sentiment analysis
-   Plotly for interactive graphs

## Data source

Thankfully I didn't have to manually transcribe the episodes. Someone had already created a json file on Kaggle (which seems to have disappeared from there now (but you can retrieve it at my [Github repository](https://github.com/surajkarak/star_trek)).

## Data cleaning and exploration

First, I read the json file, filtered for The Original Series and extracted the episode numbers and the script for each episode. There were 80 episodes (including the first one which was a pilot, which – interesting bit of trivia by the way – did not feature Kirk at all, as he was added in the second episode).

Cleaning the text in each episode. This involved removing:

-   the text at the beginning of each episode with the episode name and some special characters.

-   the text at the end of each episode

-   cases when of "\[OC\]", when a character is speaking off-camera.

-   scene descriptions (which are surrounded by \[\] and () parentheses)

-   some additional characters (non-breaking spaces, strange multiple instances of '\\n' and ':'

-   lines starting with "Captain's log" since it is not clear who spoke them (most of the time it’s Captain Kirk but there are also occasionally others stepping in. These may have some sentiment but for simplicity I am only looking at lines spoken as dialogue in the script.

Next I group lines by character. To do this I create a function that takes in an episode’s text as input, split lines at '\\n’ and to get the character’s lines, I look for all caps names of characters (this can also have an apostrophe or a space in the name) followed by a colon. The output is a dictionary where the key is the character name, and the value is an array of lines spoken by that character. (Regex is a pain but fun to work out as well after a lot of trial and error 🙂).


```python
def group_by_character(episode_text):
    if isinstance(episode_text, str):
        lines_by_character = {}
        # Each spoken line separated by a newline
        split_lines = episode_text.split('\n')

        for line in split_lines:
            # Name is in all caps (can have an apostrophe, or a space), followed by a colon
            name = re.search("([A-Z' ]+)(?=:+)", line)
            # Spoken words following the colon
            words = re.search("(?<=:)(.*)", line)
            if name is not None:
                name = name.group(0).strip()
                words = words.group(0).strip()
                if name in lines_by_character.keys():
                    lines_by_character[name].append(words)
                else:
                    lines_by_character[name]=[words]
        return lines_by_character
    else:
        return {}  # Return an empty dictionary if it's not a string
```

I apply this function to the column of cleaned lines and store it in a dictionary.


## How many words and lines are spoken by each character

Functions that calculate the word count and lines count by each character is used on the dictionaries to generate a bar plot.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/startrek/wordsandlines.png" title="words and lines" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

As expected, the 3 main characters – Kirk, Spock and McCoy have the most lines, followed by Scott, Sulu Uhura and Chekov.

## Sentiment analysis - Does Kirk balance out Spock’s logic and McCoy’s emotion?

I decide to focus on the 3 main characters. For sentiment, I use a simple NLTK SentimentIntensityAnalyzer.

For each episode, I calculate the overall polarity_scores for the lines by each, and also calculate the word counts for each. Polarity scores range from -1 to 1, with -1 being highly negative (words like “awful”, “terrible” and 1 being positive (”excellent”, “great”). Although they don’t capture the sense of logic or emotional intensity accurately, I thought it would be worth checking.

Taking an average of the scores for each across all episodes:

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/startrek/avgsentiment.png" title="average of sentiments" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

Spock has very low sentiment, even negative. This makes sense. But strangely, Kirk’s scores are much higher than the other two. This seems to go against the idea that McCoy is the emotional one and Kirk balances the two (in which case his score would have been in between the other two).

But this could also be because sentiment captures positive aspects in the language used. And both Spock and McCoy tend to speak of dangers and warnings, while Kirk is usually the one who is optimistic and confident.


## How do the sentiments vary across episodes?

For some reason, I also felt like tracking how each of their sentiment changes across the seasons. But I also wanted to weight the sentiment scores by the word counts for that character per each episode so that I could get an idea of how positive each character was in an episode in proportion to the number of words they spoke. For this I simply normalized the word count for each character per episode and multiplied their sentiment for that episode with this value.


```python
max_word_count = char_data['word count'].max()
char_data['normalized_word_count'] = char_data['word count'] / max_word_count

# Calculate weighted sentiment score per episode
char_data['weighted_sentiment'] = char_data['sent_per_episode'] * char_data['normalized_word_count']
```


The weighted sentiment scores is plotted in an interactive graph as below:

<iframe src="/assets/img/startrek/sentperepisode.html" width="900" height="800"></iframe>

Spock can be seen to be more negative, although slightly more balanced than the other two. His lowest comes in The Galileo Seven, the 16th episode of the 1st season, which I remember being one of his best ones. It is the one where he crashes with his team on a planet populated by aggressive giants and has to lead his team out. He has to make some quick moves to escape the planet's gravity at the end and interestingly, refuses to admit that his final actions were motivated by emotion than logic.

McCoy’s most positive episode is the The Tholian Web,

Interestingly, it looks like Kirk is also the one who fluctuates between the highest of positives to the lowest of negatives throughout. His highest weighted sentiment score comes in the episodes "A Piece of the Action" and “The Immunity Syndrome" (the 17th and 18th episodes of the 2nd season respectively). A Piece of the Action is the one in which they visit the planet with Earth-like 1920s gangster culture, with Tommy guns and where Kirk teaches them the fictitious game of “fizzbin” to distract the guards who captured his team. In The Immunity Syndrome, the team encounters an energy-draining, space-dwelling organism and has to send a member to pilot a shuttlecraft into the gelatinous mass of the creature and conduct analysis. His lowest sentiments comes in the episodes “The Deadly Years”, where the crew experiences rapid aging after being exposed to radiation, “Obsession” in which Kirk becomes obsessed with hunting down a deadly creature that killed members of his previous crew and “Wolf in the Fold”, where the crew becomes embroiled in a murder investigation on a pleasure planet when a series of killings occur under mysterious circumstances.

## How does the overall sentiment during an episode narrative?

One interesting but obvious aspect that I noticed when I watched the original series was how it usually followed a standard narrative progression template. The crew finds something to investigate, either on a planet or in outer space, have to send a team to collect data, more dangers are observed, the usual character interactions and bickering ensue between Spock-Kirk-McCoy, a major event peaks the tension followed by climax and eventual resolution. This follows the standard narrative arc that all stories tend to have. I wanted to explore how the sentiment changes through an episode, and if it follows a similar trajectory.

For this, I calculated the sentiment for each line in an episode and added it to a cumulative sentiment score for that episode. (This was an aesthetic choice as just tracking the sentiment values per line through the episode just showed a graph that was varying between -1 and 1. Instead, a cumulative one would allow you me to see how sentiment builds and drops as the narrative progresses). I also drilled down into each line to find the top 3 positive and negative words in the lines to let us get an idea of what may have caused the sentiment to rise or fall in that line. Also added a color code to help spot the drops more visually.

I couldn’t do this for all episodes so I just choose from the first few.

<iframe src="/assets/img/startrek/sentep0.html" width="900" height="800"></iframe>

<iframe src="/assets/img/startrek/sentep2.html" width="900" height="800"></iframe>

<iframe src="/assets/img/startrek/sentep3.html" width="900" height="800"></iframe>

<iframe src="/assets/img/startrek/sentep4.html" width="900" height="800"></iframe>


The result was not what I expected. There does not seem to be a common pattern. Episodes rise with positive sentiment sometimes while at other times it begins with negative sentiment, while in some cases it can just be fluctuating from the outset. What is common though is that at some point in the narrative the positive sentiment keeps building and after a drop towards the end, it rises back up again.