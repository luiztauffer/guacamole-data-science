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
  overlay_image: /assets/images/data_mining_mammals/map_sloth.jpg
  overlay_filter: 0.4
  teaser: /assets/images/data_mining_mammals/map_sloth.jpg
  og_image: /assets/images/data_mining_mammals/map_sloth.jpg
  
  
sidebar:
  - title: "The Cool Mammals Project"
    url: /posts/2019-03-17-cool-mammals-project
    text: "A showcase project."
    nav: sidebar-cool-mammals

---  

# The Cool Mammals Project

This is a prototype project with the objective of summarizing, in a small space, the integrated use of diverse Machine Learning (ML) and Data Science (DS) methods. Good DS is usually seen as preceding good ML, that would be end-point of anything we might need. I try to illustrate how this relationship can be more fruitful if understood, instead, a an active cycle, with ML models also informing the formulation of meaningful datasets.

I exemplify how the abundance of amazing, freely-available resouces in data and algorithms, allows us to put these ideas into practice, by building up a project with cool mammals. 



Beyond the theoretical  and implementational
I consider this to be a deliberately focused effort (for me) to document several processes of a project's lifetime: data gathering and code writting (stored on GitHub), deploying models for inference and informative visualization (as a web application) and the documentation of the project steps in an instructional format (blog posts).

In a nutshell:

1. **Image Object Detection:** Receives a photo uploaded by the user and detects pre-trained types of mammals in it;
2. **Data Mining:** Scrapes the web to automatically search for useful information about the detected mammals;
3. **Named Entity Recognition:** Automatically identifies countries names strongly associated with the detected mammals;
4. **Data Visualization:** Presents the results and insights in visually appealing ways.




## Image Object Detection
I make use of the [Tensorflow Object Detection API](https://github.com/tensorflow/models/tree/master/research/object_detection) and repurpose a pre-trained SSD Inception model a specific mammal species (a technique known as [transfer learning](https://www.youtube.com/watch?v=yofjFQddwHE)).
List of available mammals: hedgehog, lion, wolf, fox, zebra, giraffe, bat, sloth, capybara, elephant, rhino, hippo, tiger, panda, kangaroo and koala.
For each mammal I collected 100 photos for training and 10 for testing. The model trained on this limited dataset yields good results most of the times, but the number of examples proved to be too small for finer detection, specially for classes that look very similar, such as wolf and fox.

<br>
<h4>Data Mining</h4>
<p>Once the model identified which mammals are present in the photo, how can I bring up extra useful information to the user of my system? Well, for mammals specifically, there are plenty of expert filled databases available online, such as <a href="https://mol.org/">Map of Life</a> and <a href="https://mammaldiversity.org//">Mammal Diversity Database</a>. But I wanted a more challenging and generalizable solution! </p>
<p>My intutition was: if I collect a large enough number of web pages related to a specific mammal, I can statistically estimate the countries where it is most likely to find it by counting the frequency of occurrence of countries names in those pages. First, I used <a href="https://github.com/MarioVilas/googlesearch">MarioVilas' googlesearch</a> to get me hundreds of google results to entries such as "giraffe animal countries". Next, I scrape this huge set of links with <a href="https://scrapy.org/">Scrapy</a>.</p>
<p>This was an interesting implementation of the <a href="https://en.wikipedia.org/wiki/Wisdom_of_the_crowd">wisdom of the crowd</a> principle applied to web pages, but not without shortcomings. The first limitation is intrinsic to the world wide web: a disproportionally large chunk of the whole web content is produced in the United States, making it likely that the name "United States" will appear more often than others by chance. Making the search exclusively in english reinforces this bias. Also, because of zoos, certain countries might have their names appearing regularly together with some species, even if those species are not natural from those countries.</p>


<br>
<h4>Named Entity Recognition</h4>
<p>Now I need to find countries names in this ocean of texts I just extracted. The simplest way to do so would be to sweep the whole article strings looking for exact matches of each country's name. Simple and efficient... but boring! Instead, I decided to use the super cool <a href="https://spacy.io/">SpaCy</a> library available for python, it has ready to use NER functions and works really fast.</p>

<br>
<h4>Data Visualization</h4>
<p>I used <a href="https://plot.ly/">Plotly</a> to generate the beautiful bar plot with the frequencies of occurrence for each country. What's better than a nice graphic, right? I'll tell you what: a <b>map</b>! To create this choropleth map with the cute mammal icons, I used the <a href="https://github.com/python-visualization/folium">Folium</a> library for python. Both Plotly and Folium make it easy to create gorgeous visual objects and export directly to html.</p>