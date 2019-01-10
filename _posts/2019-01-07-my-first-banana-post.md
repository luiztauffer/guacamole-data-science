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
  teaser: /assets/images/bananas.jpg
  og_image: /assets/images/bananas.jpg
            
---
Here I try to develop my first post. I'm gonna just add up a bunch of text, just to check things like justification, font style, font size, colors and all that stuff. Hopefully won't spend too much time doing it... who knows. But for now, just a bunch of text will do, also nice to just go writting a bunch of nonsense crap to relieve the tension, you know. blablablablabla whatever Im feeling hungry already, is it enough for a first paragraph maybe?? I say so, now a second on. 

A second paragraph to test ident at the beginning, for style purposes and because I want. Maybe a bit more of text for that and noooww I think its ok, back to css.

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
    <a href="{{ site.url }}{{ site.baseurl }}/assets/images/banana1.jpg"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/banana1.jpg"></a>
    <a href="{{ site.url }}{{ site.baseurl }}/assets/images/banana2.jpg"><img src="{{ site.url }}{{ site.baseurl }}/assets/images/banana2.jpg"></a>
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
