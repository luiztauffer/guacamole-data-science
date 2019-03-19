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
This is a prototype project with the objective of summarizing, in a small space, the integrated use of diverse Machine Learning (ML) and Data Science (DS) methods. Good DS is usually seen as preceding good ML, which in its turn would be the end-point of anything we might need. However, this relationship could be more fruitful (in both practical and theoretical terms) if understood, instead, as an active cycle, with ML models also informing the construction and the interpretation of meaningful datasets. I try to illustrate what I mean with that with this cool mammals project.

A first catalyst of this enterprise is my amazement with the abundance of high-quality, freely-available data, algorithms and documentation out there, which empower us to put so many ideas into practice! In nearly every DS or ML piece of expertise, from data gathering, manupulation and visualization to image and language processing, the cutting-edge technologies are made promptly accessible to anyone with interest in them. 

In the spirit of contributing to this evergrowing, beautiful culture, and as a deliberate effort of self organization, I aim to describe the evolution of the project and to catalog it in the currently proper mediums: data gathering and code writting (stored on GitHub), deploying models for inference and informative visualization (as a web application) and the documentation of each project part in an instructional format (blog posts).

In a nutshell, this project will cover:

1. **Object detection:** Train a model to detect pre-defined mammal species in images and use it for inference in new images;
2. **Data Mining:** Unsupervised algorithm to form a corpus-based knowledge representation, Named Entity Recognition for identification of countries names and data visualization with clean and informative graphics;
3. **Web application:** Organizes and deploys all functionalities in an end-user web application.

**Important:** This project serves as a proof of concept for the idea of integrating recurrently ML and DS methods in a single application, and hopefully it will motivate independent pratictioners to implement their own ideas. The resources in it should not be used commercially, but only for educational purposes.
{: .notice--primary}


## 1. Object Detection
I start describing how to make use of advanced image processing models for a personal project. The chosen library is the [Tensorflow Object Detection API](https://github.com/tensorflow/models/tree/master/research/object_detection), from which I repurpose a pre-trained SSD Inception model to identify specific mammal species (a technique known as [transfer learning](https://en.wikipedia.org/wiki/Transfer_learning)).

For each mammal species I collected 100 photos for training and 10 for testing. The model trained on this limited dataset yields good results most of the times, but the number of examples proved to be too small for finer detection, specially for classes that look very similar, such as wolf and fox.

List of mammal species: bat, capybara, elephant, fox, giraffe, hedgehog, hippo, kangaroo, koala, lion, panda, rhino, sloth,  tiger, wolf, zebra.


## 2. Data Mining
Once the model identified which mammals are present in the photo, how can I bring up extra useful information to the user of my system? I propose a solution that is unsupervised and scalable: I collect the text in a large number of web pages related to a specific mammal and estimate the countries where it is most likely to be found by counting the frequency of occurrence of countries names in those pages. First, I used [MarioVilas' googlesearch](https://github.com/MarioVilas/googlesearch) to get me hundreds of google results to entries such as "giraffe animal countries". Next, I scrape this huge set of links with [Scrapy](https://scrapy.org)
This was an interesting implementation of the [wisdom of the crowds](https://en.wikipedia.org/wiki/Wisdom_of_the_crowd) principle applied to web pages, but not without shortcomings. The first limitation is intrinsic to the world wide web: a disproportionally large chunk of the whole web content is produced in the United States, making it likely that the name "United States" will appear more often than others by chance. Making the search exclusively in english reinforces this bias. Also, because of zoos, certain countries might have their names appearing regularly together with some species, even if those species are not natural from those countries.

Now I need to find countries names in this ocean of texts I just extracted. The simplest way to do so would be to sweep the whole article strings looking for exact matches of each country's name. Simple and efficient... but boring! Instead, I decided to use the super cool [SpaCy](https://spacy.io) library available for python, it has ready to use NER functions and works really fast.

I used [Plotly](https://plot.ly) to generate the beautiful bar plot with the frequencies of occurrence for each country. What's better than a nice graphic, right? I'll tell you what: a **map**! To create this choropleth map with the cute mammal icons, I used the [Folium](https://github.com/python-visualization/folium) library for python. Both Plotly and Folium make it easy to create gorgeous visual objects and export directly to html.


## 3. Web application



## 4. References and resources
1. [Tensorflow Object Detection API](https://github.com/tensorflow/models/tree/master/research/object_detection)
2. [Transfer learning](https://en.wikipedia.org/wiki/Transfer_learning)