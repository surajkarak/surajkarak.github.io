---
layout: post
title: ChatGPT and the problem with personalisation
date: 2025-09-08 14:22:23
description: Not every chat needs to be personalised to your interests.
tags: chat-gpt
categories: genai
thumbnail: /assets/post_imgs/chatgpt-personalisation.jpg
featured: false
related_posts: false

---
 


There has been much commentary about the issues with ChatGPT and other LLM tools. Hallucination is perhaps the most common. Users are also beginning to notice the excessive agreeability as a problem.

But one that I am not seeing talked about enough is that of the level of personalisation. These tools can remember previous conversations and personalise the responses based on this information. The idea is good - to keep responses relevant for you.

Sometimes this is necessary and also incredibly useful. I was recently in Paris and not being a French-speaker, ChatGPT was literally my travel buddy. When I got tired and exhausted from walking around the city, I had asked ChatGPT for some recommendations for something to eat that was cheap and healthy. We then had a brief chat about foods with good protein sources and the benefits of omega-3 in reducing inflammation and boosting mood.

Later on, I was unsure of what to eat at a restaurant - partly due to the language barrier, and partly due to me indecision. Unable to think clearly, I just took a photo of the menu and asked ChatGPT to help me decide.

<div class="col-sm mt-3 mt-md-0">
    {% include figure.html path="/assets/post_imgs/chatgpt-image.png" title="ChatGPT helps me decide lunch" class="img-fluid rounded z-depth-1" %}
</div>

Bingo, perfect response. This was super helpful and in the end, I did not regret what I ate. Done and done.

But having this personalisation always enabled means that new responses may not be ideal when you want answers that are completely independent, and unbiased by context from previous conversations.

There is of course a more general “LLM echo chamber” problem where different LLM tools, are trained on data sources that are biased in different ways. These could continue to push users down into their own bubbles, confirming their biases and accelerating polarisation. We have already seen the degradation of social media and networking platforms – starting off as places for information exchange and connecting with diverse groups of people, and ending up becoming fragmented echo chambers where people of similar viewpoints have their biases confirmed.

But this kind of personalisation is also something that could actually affect day-to-day usage for practical tasks. I have found responses to new questions being subtly influenced by things I had asked it before.

For example, at one point, I was doing a deep dive into media mix modelling and causal inference for a marketing data science project. But later on, when I was brainstorming ideas for fun sideprojects, the response included some suggestions that made use of MMM and causal inference. Not entirely unusable but I was hoping for completely brand new ideas and wondered if the idea suggestions would have been better if I had not discussed these topics separately before.  

Clearly, this level of personalisation is not always desirable. Sometimes you just want a clean start. I am increasingly having to begin prompts with something like “Forget all other conversations we have had before so as to avoid influencing your answer to this question…” more often, when I need an unbiased response.

There is an option to disable this “Memory” in ChatGPT’s Settings but one has to actively do it. I doubt the average user would be aware of this or remember to do it. And disabling it might mean losing the benefits of personalisation when it is actually useful.

Like I have always maintained, you have to be really critical of LLM responses and think carefully before taking action based their output.