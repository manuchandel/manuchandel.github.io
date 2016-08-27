---
layout: post
title: New Entity Discovery
author: Manu Chandel
category: project
excerpt: New Entity Discovery is a text analysis project which discovers new popular person entity from news headlines along with related nouns. 
---

### Problem statement

Analysing news headlines, find person names which are trending and do not have wikipedia page of their own. Find related nouns associated with these person entities. 

This is [GIT](https://github.com/manuchandel/NewEntityDiscovery) repository for source code.<br>
The final output for the program is [here]({% post_url 2016-05-02-NewEntityDiscoveryOutput %}).<br>
For detailed description check out original [project report](/assets/files/NewEntityDiscoveryReport.pdf)


### Problem understanding and challenges

Problem statement was very simple and clear in its nature. We needed a tool to extract person names from text and then a wikipedia knowledge base to query whether the person has its own wikipedia page or not. Once the person name is found we can extract most frequent nouns from the text where this name appears.
There were many challenges faced.

First challenge was to collect names of the people who has their own wikipedia page. These people were famous personalities from vaious social and political fields. Second challenge was more severe as these personalities needed disambiguation. If in a text there are two people, one famous and other with a famous name it is almost impossible to say who is new entity here without the knowledge of the context.

e.g. Flipkart owner Sachin meets cricket legend Sachin.  
In this example suppose Flipkart owner Sachin Bansal has no wikipedia page of his own then there will be confusion betweem Sachin Bansal and Sachin Tendulkar.

Also sometimes wikipedia page contains information about a person in a different format then what that person is generally referred to.

e.g. Wikipedia page has entry of Lal Krishna Advani but often we see L.K Advani appearing in news headlines. Same is the case with Mahendra Singh Dhoni, we often see M.S Dhoni in news.

These ambiguations can be avoided by taking a small assumption. We assume that news articles report only complete names of the people who are trending. If there is a news headline where we see only acronyms or alias of the famous people then our algorithm is not able to distinguish between them.

e.g. Sanju Baba is commonly refered to Mr. Sanjay Dutt. But our algorithm has a limitation where it is not able to distnguish between Sanju Baba and Sanjay Dutt.

### Sub Problem One

Our first subproblem was to collect names of all the people who have wikipedia page of their own. There is a already a blog post as to how I did this. Please go through it [here]({% post_url 2016-03-16-WikiHumanScraper%})

### Sub Problem Two

Second subproblem was to identify person names in a text. To identify person names, Stanford NER 3-class classifier was used. A dictionery of words with their resepective tag frequencies was made. This ensured greater accuracy while tagging a headline with NER Tagger. 

Text is divided into tokens and NER Tagger tags each token separately. After tagging, words with the person name tag were clumped together to form whole name. e.g. words like `Narendra` and `Modi` were tagged separately as named entities. These words were combined together to form `Narendra Modi`.

Combining of tagged entities was based on the proximity distance of each entity with another. After tagging all named entities, each entity was queried in database. If the entity did not existed in database then, it was declared as `new entity` after considering its frequency of occurence and importance.

### Sub Problem Three

Last subproblem was of finding related nouns to these entities. Using `Natural Language Processing Toolkit` of python each sentence was tagged into `Parts of Speech` tags. Nouns occuring in the text where `discovered entity` occured were recorded. If the noun occured more than half of times in relation to `discovered entity` then it was considered related to that entity.

### Output

Our system was tested on news headlines from January 2016 to March 2016. In this time period it indentified many new entities correctly. The output table is [here]({% post_url 2016-05-02-NewEntityDiscoveryOutput %})

for e.g. Kanhaiya Kumar was identitifed as a new entity on 12th Feburary 2016 after the incident at JNU. Same is the case with Omar Khalid.

Many entities which were old were indentified as new ones due to ambiguation.

for e.g. Sanju Baba was tagged as new entity. It was on the same day when he was released from Yervada Jail.
