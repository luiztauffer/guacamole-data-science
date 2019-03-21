---
layout: single_post
title:  "The Cool Mammals Project"
excerpt: "Part 3: Web Application"
date:   2019-03-20
classes: wide
author_profile: false

categories: 
    - projects
tags: 
    - deployment
    - web application
tools:    
    - Django
    - Ngrok
  
  
sidebar:
  - title: "The Cool Mammals Project"
    nav: sidebar-cool-mammals

---  


Previously, we learned how to use TensorFlow Object Detection API to [identify cool mammals]({{ site.url }}{{ site.baseurl }}//posts/2019-03-14-identifying-cool-mammals/) in images and then how to [data mine]({{ site.url }}{{ site.baseurl }}//posts/2019-03-16-mammals-data-mining/) the web and produce nice visualizations with geographical information about those species. But a local machine is too small for the greatness of our algorithms and they deserve to win the world (wide web)! In this part 3, we're gonna build a web application that reads an user's photo, detects which cool mammals are present and prints back some nice and informative graphics.

If you say the words "web development and python", you're basically saying [Django](https://www.djangoproject.com/). If you're good with python, but not so much well acquainted with web development (`HTML`, `CSS` and `JavaScript`), then Django is right for you (us)!


## Getting started with Django
To install Django, just run `pip install django` and verify the installation with `django-admin --version`. Now let's create a Django project with `django-admin startproject your_project_name`. This should create a folder with your project's name and some files that contitute the initial basis for our application.



Creating an app
$ python manage.py startapp identify_mammal

To run a server
$ python manage.py runserver



