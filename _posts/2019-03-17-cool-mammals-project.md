---
layout: single_post
title:  "The Cool Mammals Project"
excerpt: "Introduction"
date:   2019-03-17
classes: wide
author_profile: false

categories: 
    - projects
tags: 
    - machine learning
    - data science
    
header:
  overlay_image: /assets/images/cool_mammals_project/header.jpg
  overlay_filter: 0.4
  caption: "Photo credit: [**Sebastian Molinares**](https://unsplash.com/@damebash)"
  teaser: /assets/images/cool_mammals_project/header.jpg
  og_image: /assets/images/cool_mammals_project/header.jpg
  
  
sidebar:
  - title: "The Cool Mammals Project"
    nav: sidebar-cool-mammals

---  

# The Cool Mammals Project
This is a prototype project with the objective of summarizing, in a small space, the integrated use of diverse Machine Learning (ML) and Data Science (DS) methods. Good DS is usually seen as preceding good ML, which by its turn would be the end-point of anything we might need. However, this relationship could be more fruitful (in both practical and theoretical terms) if understood, instead, as an active cycle, with ML models also informing the construction and the interpretation of meaningful datasets. I try to illustrate what-I-mean-with-that with this cool mammals project.

A first catalyst of this enterprise is my amazement with the abundance of high-quality, freely-available data, algorithms and documentation out there, which empower us to put so many ideas into practice! In nearly every DS or ML piece of expertise, from data gathering, manupulation and visualization to image and language processing, the cutting-edge technologies are made promptly accessible to anyone with interest in them. 

In the spirit of contributing to this evergrowing, beautiful culture, and as a deliberate effort of self organization, I aim to describe the evolution of the project and to catalog it in the currently proper mediums: data gathering and code writting (stored on GitHub), deploying models for inference and informative visualization (as a web application) and the documentation of each project part in an instructional format (blog posts).

In a nutshell, this project will cover:

1. **Object detection in images:** Receives a photo uploaded by the user and detects pre-trained types of mammals in it;
2. **Data Mining:** Scrapes the web to automatically search for useful information about the detected mammals;
3. **Named Entity Recognition:** Automatically identifies countries names strongly associated with the detected mammals;
4. **Data Visualization:** Presents the results and insights in visually appealing ways.
5. **App design and deployment:** Organizes all functionalities in an end-user web application.


## 1. Object Detection in images
I make use of the [Tensorflow Object Detection API](https://github.com/tensorflow/models/tree/master/research/object_detection) and repurpose a pre-trained SSD Inception model a specific mammal species (a technique known as [transfer learning](https://www.youtube.com/watch?v=yofjFQddwHE)).
List of available mammals: hedgehog, lion, wolf, fox, zebra, giraffe, bat, sloth, capybara, elephant, rhino, hippo, tiger, panda, kangaroo and koala.
For each mammal I collected 100 photos for training and 10 for testing. The model trained on this limited dataset yields good results most of the times, but the number of examples proved to be too small for finer detection, specially for classes that look very similar, such as wolf and fox.


## 2. Data Mining
Once the model identified which mammals are present in the photo, how can I bring up extra useful information to the user of my system? Well, for mammals specifically, there are plenty of expert filled databases available online, such as [Map of Life](https://mol.org/), [Mammal Diversity Database](https://mammaldiversity.org) or the [IUCN Red List of Threatened Species](https://www.iucnredlist.org/). But I wanted a more challenging and generalizable solution!
My intutition was: if I collect a large enough number of web pages related to a specific mammal, I can statistically estimate the countries where it is most likely to find it by counting the frequency of occurrence of countries names in those pages. First, I used [MarioVilas' googlesearch](https://github.com/MarioVilas/googlesearch) to get me hundreds of google results to entries such as "giraffe animal countries". Next, I scrape this huge set of links with [Scrapy](https://scrapy.org)
This was an interesting implementation of the [wisdom of the crowds](https://en.wikipedia.org/wiki/Wisdom_of_the_crowd) principle applied to web pages, but not without shortcomings. The first limitation is intrinsic to the world wide web: a disproportionally large chunk of the whole web content is produced in the United States, making it likely that the name "United States" will appear more often than others by chance. Making the search exclusively in english reinforces this bias. Also, because of zoos, certain countries might have their names appearing regularly together with some species, even if those species are not natural from those countries.


## 3. Named Entity Recognition
Now I need to find countries names in this ocean of texts I just extracted. The simplest way to do so would be to sweep the whole article strings looking for exact matches of each country's name. Simple and efficient... but boring! Instead, I decided to use the super cool [SpaCy](https://spacy.io) library available for python, it has ready to use NER functions and works really fast.


## 4. Data Visualization
I used [Plotly](https://plot.ly) to generate the beautiful bar plot with the frequencies of occurrence for each country. What's better than a nice graphic, right? I'll tell you what: a **map**! To create this choropleth map with the cute mammal icons, I used the [Folium](https://github.com/python-visualization/folium) library for python. Both Plotly and Folium make it easy to create gorgeous visual objects and export directly to html.


## 5. Deployment as a web application