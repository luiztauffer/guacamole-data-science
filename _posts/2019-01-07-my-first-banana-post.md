---
layout: single
date:   2019-01-07
categories: bananas

title:  "My own Bananas post"
excerpt: "This post will make you go **BANANAS**"

header:
  overlay_image: /assets/images/bananas.jpg
  overlay_filter: 0.5
  caption: "Photo credit: [**chuttersnap**](https://unsplash.com/photos/zQWuVlP_bNI)"

image:
  feature: /assets/images/bananas.jpg
  thumb: /assets/images/bananas.jpg 
  
docs:
  - title: Luiz Tauffer
    image: /assets/images/bio-photo.jpg
    text: "I am ..."
  - title: "Another Title"
    text: "More text here."  
    text: "Stockholm, SE" 
    children:
          - title: "GitHub"
            url: "https://github.com/luiztauffer"
          - title: "Linkedin"
            icon: "fab fa-fw fa-linkedin"
            url: "https://www.linkedin.com/in/luiz-tauffer"
            
---
Here I try to develop my first post.

# This is a first Section
And for that, a large bananas picture.

<figure>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/banana1.jpg" alt="" width="500" />
    <figcaption>Some bananas.</figcaption>
</figure>

And now, we create a subsection.

## A subsection
This subsection will get two pictures, like this: 
<figure class="half">
    <a href="/assets/images/banana1.jpg"><img src="/assets/images/banana1.jpg"></a>
    <a href="/assets/images/banana2.jpg"><img src="/assets/images/banana2.jpg"></a>
    <figcaption>More bananas is better than less bananas.</figcaption>
</figure>


<div class="notice--success">
  <h4>Hooray!!</h4>
  <p>We made it. Banana blog post.</p>
</div>


I can put in some Python code:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}
