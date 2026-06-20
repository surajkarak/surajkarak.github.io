---
layout: post
title: Why you can’t vibe-code your way through a data science project
date: 2025-11-12 17:12:28
description: Knowing what to build, trust, and deploy is the real challenge.
tags: data-science
categories: data-science
thumbnail: /assets/post_imgs/vibe-coding-data-science.png
featured: false
related_posts: false

---
 

I’ve been reading about the rise of AI agents, and the trend of vibe coding raising concerns that certain technical jobs are going to be automated away. For sure, routine tasks that can be automated, will be automated. 

In my own line of work of data science too, there is discussion around how much can be done through pure vibe coding. There are even AI agents being released, capable of generating entire notebooks for standard data science tasks. And yet, I find this to be the wrong way of thinking about the data science practice altogether. For various reasons:

## 1. Data science work is fundamentally ambiguous

The very idea behind vibe based coding work is that you get things done through natural language. When the goals, expected outcomes and end product is clear, such as the case of simply building and app or software product, this might work. 

But data science as a practice is by its very nature ambiguous. Half the work involves understanding the problem, [examining the data available](https://surajkarak.github.io/blog/understanding-data-science-projects/), figuring out what can be measured, modelled or predicted and how, setting up experiments and communicating results. This requires a lot of attention to detail, sound domain knowledge, deep understanding of business context and collaboration across teams and disciplines. 

## 2. The “workflow” is often circular, never linear

It’s never as as simple as creating a pipeline to do extraction → cleaning → preprocessing → EDA → modelling → visualisation → deployment, as most intro to data science coursework and tutorials would have you believe. Very often you need to stop at a stage, go back and redo the entire workflow again to test out different scenarios.


<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/post_imgs/data-science-workflow.png" title="Data science workflow" alt="Comparison of expected data science workflow (linear) and reality (non-linear)" class="img-fluid rounded z-depth-1" %}
    </div>
</div>


Take [exploratory data analysis](https://surajkarak.github.io/blog/importance-of-eda-data-science/) for example. You will have to check the distribution of various parameters, plot correlations and visualise time series plots before proceeding to modelling. And very often, you will have to relook at features selected or engineered for modelling, and therefore return to EDA again. Sometimes you will even have to engineer new features and retrain + re-evaluate the model. Trying to automate this is not only tricky, but could result in a lot of suboptimal performance.

## 3. Human judgement drives decisions

Despite being heavily quantitative, there is an aspect of data science that relies on human judgement. What are the best features to focus on? Should they be engineered or are they already captured? What type of model best suits a use case? And why can’t another model be preferred if it performs better in evaluation? Someone has to make a call when it comes to these questions, based on experience and domain knowledge. 

Purely relying on a metric or logic to try and automate this may give you better performance but it may lack interpretability or scalability, for example. Then there is the question of the actual business KPIs and how they map to the in-model metrics - again something that the data scientist has to discuss with stakeholders.

## 4. The “science” part of data science 

Most people forget the science part of data science. The scientific method is central to data science work - forming hypotheses, testing assumptions, gathering evidence, and updating beliefs. Data science borrows heavily from that mindset. 

Intuition, critical evaluation at every step, self-correction and such is also necessary. We are increasingly seeing this being offloaded on to LLMs through prompts and users become increasingly reliant on the generated outputs. 

This is not to say that prompting LLMs is to be avoided completely. Writing prompts is a good way to structure your own thinking, just like when you’re rubber ducking a problem. Sure, if you’re in a hurry and want to look up something quickly, then it's fine - just prompt whatever.

But if you’re working through a complex problem, being as clear as possible in your prompt makes a huge difference. It doesn’t have to be grammatically perfect, although that helps in other ways. But stating clear goals, providing context, constraints to follow, and caveats go a long way.

For example, "I want A “but” the with the constraint B or I want X “while also” satisfying conditions Y and Z".

And carefully reviewing the response with a critical eye:

❓ Does it make sense, given what I know already?

❓ Is it confirming my biases in anyway?

❓ Is it agreeing too quickly to my idea?

❓ How much of this response could be influenced by previous questions I may have asked?

Does anything seem too perfect? If it’s too good to be true, it should ring alarm bells. And this is where the “scientist” brain should kick in and where a scientific method has to be adopted to critically evaluate and iterate as needed.

## 5. Communication is overlooked but critical

You can automate a lot of the data science workflow - maybe even the entire process. But when faced with questions from stakeholders - the what of a model, the why of the decision to choose a specific model and the ramifications on latency, costs and complexity - you will need to be prepared with domain knowledge. This is also where soft skills like communication become incredibly valuable. One can easily generate reports, dashboards and visualizations in single-shot prompts but having a knack of how to adapt your communication style to meet the needs of different levels of stakeholders and their styles can make a big difference.

