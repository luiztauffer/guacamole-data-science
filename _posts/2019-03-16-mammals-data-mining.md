---
layout: single_post
title:  "The Cool Mammals Project"
excerpt: "Part 2: Data Mining"
date:   2019-03-16 
classes: wide
author_profile: false

categories: 
    - projects
tags: 
    - web scraping
    - natural language processing
    - data mining
    - data visualization
    - geospatial
tools:
    - Scrapy
    - SpaCy
    - Plotly
    - Folium
    
header:
  overlay_image: /assets/images/data_mining_mammals/map_sloth.jpg
  overlay_filter: 0.4
  teaser: /assets/images/data_mining_mammals/map_sloth.jpg
  og_image: /assets/images/data_mining_mammals/map_sloth.jpg
  
  
sidebar:
  - title: "The Cool Mammals Project"
    url: /posts/2019-03-17-cool-mammals-project.html
    text: "A showcase project."
    nav: sidebar-cool-mammals

---  
  
In the [part 1]({{ site.url }}{{ site.baseurl }}//posts/2019-03-14-identifying-cool-mammals/) of this project, I described how to train a deep learning model to identify species of mammals present in a photo. This could be an useful functionality, for example, in the cellphone of wildlife enthusiasts. Now, how can we create a more complete application, one that goes beyond tracing boxes around the identified mammals and actually feeds back interesting information to the user?

An obvious first choice would be to rely on existing expert filled databases available online, such as [Map of Life](https://mol.org/), [Mammal Diversity Database](https://mammaldiversity.org) or the [IUCN Red List of Threatened Species](https://www.iucnredlist.org/). This would be a reliable, complete and readily-available solution... but boring! I wanted a more challenging and generalizable solution! What if we wanted to find information not present in these databases? So I decided to experiment with a sort of version of the [wisdom of the crowds](https://en.wikipedia.org/wiki/Wisdom_of_the_crowd) concept applied to internet published content. My intutition was: if I collect a large enough number of web pages related to a specific mammal, I can statistically estimate the countries where it is most likely to be found by counting the frequency of occurrence of countries names in those pages.

This [corpus-based](https://pdfs.semanticscholar.org/274e/dc1371f321628b4d88e8f7bf7756bc39ff9c.pdf) solution will definitely not, for this example, be as reliable as hand-filled, domain expert-based solution. However, the **unsupervised** nature of the method makes it, at least in principle, generalizable to other tasks (e.g. what does this species like to eat?) and scalable to larger numbers of classes (e.g. it gets cheaper than the expert-based solution if we would do it for all known species). Furthermore, it will be an interesting exercise for creative problem solving! Let's go ahead then, this project will involve the following steps:

1. Find relevant webpages and scrape their content
2. Run Named Entity Recognition to find countries names
3. Create appealing visualizations
<br/>


## 1. Web scraping
How to gather so many web pages with information about the mammal species? No one better 
than Mr. know-it-all [Google](https://www.google.com) to give it a hand! But now, how to do it **automatically**, without the need of a sapiens mammal clicking work? I used the handy [MarioVilas' googlesearch](https://github.com/MarioVilas/googlesearch) to get me the first 100 google results to entries such as "giraffe animal countries". Keep in mind that automated querying is against Google's [Terms of Service](https://support.google.com/webmasters/answer/66357) and abusing it might get your IP banned, so be polite! The amount of links we're gathering here is small and we only need to make it once, so keep the limit of one request per minute and you will be (hopefully) safe. 
    
To install the python packages is as easy as `pip install google`. Here's an usage example that will create a `CSV` file with all the page links for every mammal:

```python
from googlesearch import search, get_random_user_agent
import pandas as pd
import time

mammal_list = [ 'hedgehog', 'lion', 'wolf', 'fox', 'zebra', 'giraffe',
                'bat', 'sloth', 'capybara', 'elephant', 'rhino',
                'hippo', 'tiger', 'panda', 'kangaroo', 'koala' ]

df = pd.DataFrame(data=mammal_list, columns=['species'])                                       
df['countries_search'] = ''
df['countries_search'] = df['countries_search'].astype(object)

#Search 100 Google results for each species
for mammal in mammal_list:
    all_urls = []
    results = search(mammal+" animal countries", num=50, stop=200, pause=60., 
                     only_standard=True, user_agent=get_random_user_agent())
    [all_urls.append(i) for i in results]
            
    df.at[df['species']==mammal,'countries_search'] = [all_urls]
    df.to_csv('mammals_websites.csv', index=False)
    time.sleep(60)
```

The next step is to retrieve the text content from each one of these pages. To scrape this large set of links, we'll used [Scrapy](https://scrapy.org/). Scrapy has a bit steepy learning curve, but it really comes at hand for small projects such as this, once you learn the basics of it! [Here's](https://www.youtube.com/playlist?list=PLE50-dh6JzC6dHxpAno-a6W7QpWdAFN20) a nice tutorial for it. To install Scrapy, just run `pip install scrapy`. 

Now we want to create the `spiders`, which are objects that Scrapy will use to navigate through web pages and retrive information from them. The following code creates a spider class `find_countries` that will open the urls in the list `start_urls` relative to the species `mammal`, will extract all text content inside the html `p` tags, remove some of the html content from the text and save the results in `TXT` files:

```python
import os
import scrapy
from scrapy.crawler import CrawlerProcess

class find_countries(scrapy.Spider):
    name = "find_countries_for"
    def __init__(self, mammal, start_urls, *args, **kwargs):
        self.mammal = mammal
        self.start_urls = start_urls
        super(find_countries, self).__init__(*args, **kwargs)

    def parse(self, response):
        paragraphs = response.xpath('//p[.//text()]').extract()
        aux = response.url.replace(".","").replace(":","").replace("/","")+'.txt'
        fname = os.path.join('mammals_raw_txt', self.mammal, aux)
        dirname = os.path.dirname(fname)
        if not os.path.exists(dirname):
            os.makedirs(dirname)
        with open(fname, "w", encoding="utf-8", errors="ignore") as text_file:
            for item in paragraphs:
                item = item.replace("<"," ").replace(">"," ").replace("/"," ")
                text_file.write("%s\n" % item)
    
                        
#dataframe containing websites to search for
df = pd.read_csv('mammals_websites.csv')

mammal_list = ['hedgehog', 'lion', 'wolf', 'fox', 'zebra', 'giraffe', 'bat',
               'sloth', 'capybara', 'elephant', 'rhino', 'hippo', 'tiger',
               'panda', 'kangaroo', 'koala']

process = CrawlerProcess()  

#Iterate over all mammal species
for mammal in mammal_list:
    aux0 = df.loc[df['species']==mammal,'countries_search']
    aux1 = aux0.values.tolist()[0]
    full_list = aux1.strip("[]").replace("'", "").split(", ")
    list_urls = []
    for url in full_list:   #excludes youtube links
        if "youtube" not in url: 
            list_urls.append(url)
    
    #set-up crawler
    process.crawl(find_countries, mammal=mammal, start_urls=list_urls)

process.start()                        
```

This should create one folder per mammal species, with one `TXT` file per web page scraped containing mostly the text content of those pages. Our raw database is now ready to be used offline, with whatever the world wide web has to say about kangaroos and sloths!


## 2. Named Entity Recognition
With the corpus at hand, we move on to extract meaningful information: the whereabouts of those mammals. Now, how to find countries names in this sea of texts? The simplest way to do so would be to sweep the whole article strings looking for exact matches with each existent country's name. Simple and possibly accurate... but boring and possibly slow! Instead, I decided to use the super cool [SpaCy](https://spacy.io) library available for python, it has many ready to use Natural Language Processing (NLP) funtionalities and works really fast.

To install Spacy, just do `pip install -U spacy`. You will also need to download the language [model](https://spacy.io/usage/models) to be used for inference, that's as simples as `python -m spacy download en`. I got the best results with the large model but, if it is excessively large for your application, you can use medium or small models.

The task we need to solve is Named Entity Recognition ([NER](https://en.wikipedia.org/wiki/Named-entity_recognition)), which consists on classification of words into specific categories. SpaCy NER is able to identify a relatively wide span of object classes, amongst which `GPE` (Geopolitical Entity), which encompasses countries, cities and states. That's how the code looks like:

```python
import spacy
import os 
import operator
import pandas as pd

# loads large english model
nlp = spacy.load('en_core_web_lg')

mammal_list = ['hedgehog', 'lion', 'wolf', 'fox', 'zebra', 'giraffe',
               'bat', 'sloth', 'capybara', 'elephant', 'rhino', 'hippo',
               'tiger', 'panda', 'kangaroo', 'koala']

df = pd.DataFrame(data=mammal_list, columns=['species'])    
df['country_counts'] = ''
df['country_counts'] = df['country_counts'].astype(object)
df['number_results'] = 0

for mammal in mammal_list:
    all_files = os.listdir(os.path.join('mammals_raw_txt', mammal)) 
    all_sets = []
    for fname in all_files:   #all scraped pages in .txt files
        file_path = os.path.join('mammals_raw_txt', mammal, fname)
        with open(file_path, 'r', encoding="utf-8", errors="ignore") as myfile:
            data = myfile.read().replace('\n',' ').replace('type',' ').replace('=',' ')
        data = data[0:50000]   #protect from memory error
        doc = nlp(data)
        
        curr_ents = []
        for X in doc.ents:  #find words of a specific entity
            if X.label_ == 'GPE':
                ent_txt = (X.text).lower()
                curr_ents.append(ent_txt)
        
        #Gets unique values in lists
        myset = set(curr_ents)
        all_sets = all_sets + list(myset)
        
    #Counts number of occurrences  
    wrd_cnt = {}    
    for wrd in all_sets:
        if wrd not in wrd_cnt:
            wrd_cnt[wrd] = 1
        else: wrd_cnt[wrd] += 1
    
    #Sorts by number of occurrences    
    sorted_x = sorted(wrd_cnt.items(), key=operator.itemgetter(1), reverse=True)    
    df.at[df['species']==mammal,'country_counts'] = [sorted_x]
    
    #Number of scraped files
    df.at[df['species']==mammal,'number_results'] = len(all_files)
                                
    df.to_csv('mammals_country_counts.csv', index=False)
```

We must have now a single `CSV` file with lists of countries names, sorted by number of occurrences in web pages associated with each mammal species. **Congratulations!** You just used a series of machine learning models to produce a meaningful dataset!


## 3. Visualization
It's now time to explore our dataset visually on a [Jupyter notebook](https://jupyter.org/). To create beautiful and interactive graphics, let's use [Plotly](https://plot.ly). To install it, just run `pip install plotly`. 

In the file `mammals_country_counts.csv` we will find a list of unique `GPE` entity type counts for each mammal species. We can narrow down our scope to names of countries (and exclude regions, cities and other categories) by comparing this list with a list of know country names save in the `'countries_names.csv` file. Open a notebook instance on your working environment and let's set up a function to read the counts file and return the names and values in a clean format:

```python
import numpy as np
import pandas as pd
import ast
import csv

def mammals_countries(mammal):    
    #Load and prepare data
    df = pd.read_csv('mammals_country_counts.csv')
    npages = df.loc[df['species']==mammal]['number_results'].values[0]
    aux = df.loc[df['species']==mammal]['country_counts'].values[0]
    aux = aux.strip('[]')
    aux_all = list(ast.literal_eval(aux))

    #Check if it's a valid country name and counts occurrences
    with open('countries_names.csv', 'r') as f:
        reader = csv.reader(f)
        valid_countries = list(reader)[0]
    country_names = []
    country_counts = []
    for i in aux_all:   
        if i[0].title() in valid_countries:  
            country_names.append(i[0].title())
            country_counts.append(i[1])
    country_names = np.array(country_names)
    country_counts = 100*np.array(country_counts)/npages
    
    return country_names, country_counts
```

Now let's make a function to create a bar plots with the frequencies of occurrence for each country, as a percentage of the number of pages scraped. Set Plotly to work in the notebook mode, call the `mammals_countries()` function to load the data, format the layout and use `iplot(fig)` to print it on the notebook or `plot(fig, filename)` to save the plot as an `HTML` file.

```python
from plotly import tools
import plotly.plotly as py
import plotly.graph_objs as go
from plotly.offline import iplot, init_notebook_mode

init_notebook_mode(connected=True)

def bar_graph_mammals(mammal): 
    #Get countries counts
    country_names, country_counts = mammals_countries(mammal)
    
    #Make Plotly graphics
    trace0 = go.Bar(
        x=country_names,
        y=country_counts,
        marker=dict( color='rgb(158,202,225)',
                     line=dict(color='rgb(8,48,107)', width=1.5) ),
        opacity=0.6 )

    data = [trace0]
    layout = dict( height=370, width=900, xaxis=dict(tickangle=-45), title=mammal+' presence',
                   yaxis=dict( title='Occurrences on web pages [%]' ),
                   paper_bgcolor='rgba(0,0,0,0)',
                   plot_bgcolor='rgba(0,0,0,0)',
                   shapes=[{
                        'type': 'line',
                        'x0': 0,
                        'y0': 10,
                        'x1': len(country_names),
                        'y1': 10,
                        'line': {
                            'color': 'rgb(0, 0, 0)',
                            'width': 1,
                            'dash': 'dot'}}] )

    fig = go.Figure(data=data, layout=layout)
    iplot(fig)
    #plot(fig, filename='media/bar_graph_'+mammal+'.html')

mammal = 'tiger'                
bar_graph_mammals(mammal)                
```

<iframe name="iframe_bargraph" width="950px" height="420px" frameborder="0" scrolling="no" src="{{ site.url }}{{ site.baseurl }}/assets/images/data_mining_mammals/bar_graph_tiger.html">
</iframe>

Since we scraped all the google search results indiscriminately, some spurious results will happen (e.g. tigers in Barbados), but they will most likely have low counts and a simple threshold (~10%) might be enough to get rid of most of this noise.

What's better than a nice graphic, right? I'll tell you what: a **map**! To create this choropleth map with the cute mammal icons, I used the [Folium](https://github.com/python-visualization/folium) library for python. Both Plotly and Folium make it easy to create gorgeous visual objects and export directly to html.



**Tip:** After producing your superb `HTML` graphics and maps, [here's](https://wkhtmltopdf.org/) a great tool to render them as a vector `PDF`, if you need to them as static figures. 


## 4. Limitations, errors and what I learned from them
Having your application indicating that giraffes live in Ecuador can be disappointing, but errors are not always so bad. We can learn form them! Here's a couple of interesting things I learned from the errors of this project:

1. This was an interesting implementation of the [wisdom of the crowds](https://en.wikipedia.org/wiki/Wisdom_of_the_crowd) principle applied to web pages, but not without shortcomings. The first limitation is intrinsic to the world wide web: a disproportionally large chunk of the whole web content is produced in the United States, making it likely that the name "United States" will appear more often than others by chance. Making the search exclusively in english reinforces this bias. Also, because of zoos, certain countries might have their names appearing regularly together with some species, even if those species are not natural from those countries.

2. [Hippos in Colombia](https://www.vox.com/videos/2018/12/4/18125563/hippos-colombia-pablo-escobar), wtf?? Yes, they were brought in the 80s by Pablo Escobar for his personal zoo and now are roaming by the country.


## 5. References and resources
1. [Map of Life](https://mol.org/)
2. [Mammal Diversity Database](https://mammaldiversity.org)
3. [IUCN Red List of Threatened Species](https://www.iucnredlist.org/)
4. [Wisdom of the crowds](https://en.wikipedia.org/wiki/Wisdom_of_the_crowd) 
5. [Corpus-Based Knowledge Representation](https://pdfs.semanticscholar.org/274e/dc1371f321628b4d88e8f7bf7756bc39ff9c.pdf)
6. [Google](https://www.google.com)
7. [MarioVilas' googlesearch](https://github.com/MarioVilas/googlesearch)
8. [Google's Terms of Service](https://support.google.com/webmasters/answer/66357)
9. [HTML to PDF tool](https://wkhtmltopdf.org/)