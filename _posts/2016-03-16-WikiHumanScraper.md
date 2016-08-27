---
layout: post
title: WikiHuman Scraper
author: Manu Chandel
category: project
excerpt: A script written in python which scrapes human names and their description from wiki data and stores them into a database.
---

## Introduction

<br>
`WikiHuman Scraper` as I like to call it, is a script written in python which scrapes human names and their description from wiki data and stores them into a database.<br>

I needed names of the people who had their wikipedia page for my `Text Mining` project. I could not find any source which could provide me with precise data. Ofcourse there were wikipedia dumps out there but they were huge in size and most of the information present in these dumps was irrlevant to my project. So i decided to write my own script.

I managed to collect about 72MB of data which had around 1.2 million names with their description.<br>The data collected was stored on a `Apache Server` using `LAMPP`.<br>
Here is the .sql [__FILE__](https://goo.gl/sB0Psh) for data and [__GIT REPOSITORY__](https://github.com/manuchandel/WikiHuman-Scraper) for source code.

Data about people was scraped using this [__SOURCE__](https://goo.gl/MgvSG0)
<br>
<br>

## Environment

<br>
To install python on a recent version of Debian or Ubuntu Linux

```

$ sudo add-apt-repository ppa:fkrull/deadsnakes
$ sudo apt-get update
$ sudo apt-get install python2.7

```
To install beautiful soup

```
$ sudo apt-get install python-bs4
```

<br>

## Working
<br>

* The script makes use of `Beautiful Soup` library. More about beautiful soup [__HERE__](http://goo.gl/Ea64B).<br>

* The initial problem faced was how to keep track of webpages which were already scraped and the one which were left for scraping.This problem was solved by creating a text file named `url`.
* This text file would keep record of the link of the webpage which is to be scraped next. In case where the connection was reset by the host or some other error may occur, scraping can be resumed from the url which was present in `url` text file.
* The `url` text file in a way added resuming capability for scraping. After the error has occured, to resume scraping, simply copy the url from the text file paste it on line 10 of `scraper.py` and run the script.
* To keep track of progress made by script, a variable `i` was used and it would print a number when a page was successfully scraped.
* All the transactions into the database were commited only after one complete webpage was scraped. This avoided incomplete transactions to take place due to error.
