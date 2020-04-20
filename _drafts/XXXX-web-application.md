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


Previously, we learned how to use TensorFlow Object Detection API to [identify cool mammals]({{ site.url }}{{ site.baseurl }}//posts/2019-03-14-identifying-cool-mammals/) in images and then how to [data mine]({{ site.url }}{{ site.baseurl }}//posts/2019-03-16-mammals-data-mining/) the web and produce nice visualizations with geographical information about those species. But a Jupyter notebook is too small for the greatness of our algorithms and they deserve to win the world (wide web)! In this part 3, we're gonna build a web application that reads an user's photo, detects which cool mammals are present and prints back some nice and informative graphics.

If you say the words "web development and python", you're basically saying [Django](https://www.djangoproject.com/) ([Flask](http://flask.pocoo.org/) has recently [caught up](https://www.jetbrains.com/research/python-developers-survey-2018/), though). If you're good with python, but not so much well acquainted with web development, then Django is right for you (us)!


## Getting started with Django
To install Django, just run `pip install django` and verify the installation with `django-admin --version`. Now let's create a Django project with `django-admin startproject your_project_name`. This should create a directory with your project's name and some files that constitute the initial basis for our website. One of the great features of Django is the standalone web server for testing your application with real time updates. To run a server is as simple as `python manage.py runserver` inside your project's main directory. Now open your browser, navigate to `localhost:8000` and, if everything was correctly set up, you should see the success rocket:

<figure>
    <a href="{{ site.url }}{{ site.baseurl }}/assets/images/cool_mammals_project/part3/create_project.PNG"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/cool_mammals_project/part3/create_project.PNG"></a>
    <figcaption>Skyrocketing to a Django success!</figcaption>
</figure>

Inside your project's main directory, create a new directory `media`, where we will store all our project images. Django breaks down all functionalities of the project (the website) into apps (e.g. forums, users profiles, blog). This might sound unnecessarily confusing but it's actually quite neat, imagine that your apps will be independents blocks that could be copied and pasted multiples times in multiple projects! So the first thing we should do inside the project is to create an app with `python manage.py startapp mammals_detection`. Inside the newly created directory `mammals_detection`, let's create a new directory `templates` where we will store our `HTML` templates. After all that, the initial skeleton of our web app should be looking like that:

```
your_project_name/
├── mammals_detection/
    ├── migrations/
        ├── __init__.py
    ├── __init__.py
    ├── admin.py
    ├── apps.py
    ├── models.py
    ├── tests.py
    ├── views.py
├── media/    
├── your_project_name/
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    ├── wsgi.py
├── manage.py
```

The main files inside our `mammals_detection` app we should be concerned, for a start, are:
`views.py` - that is where we define the main functionalities of the app.
`urls.py` - lists the url paths associated with each function defined at `views.py`.



