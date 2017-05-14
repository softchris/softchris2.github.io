---
layout: page
title: Christoffer Noring - blog
tagline: A technical blog with mostly javascript related stuff but also .net
---
{% include JB/setup %}

*I a migrating all my blog posts to this blog, because as far as I have seen it has the best support for
writing in markdown..*

## About me
I am a developer since 10 years roughly.. I love to code and spread knowledge.
I love javascript and its accompanying frameworks such as grunt, gulp, angular, react etc..

Google Developer Expert,  [Google Developer Expert](https://developers.google.com/experts/people/christoffer-noring)
Telerik Developer Expert [Telerik Developer Expert](http://developer.telerik.com/community/developer-experts/)
I am currently writing a book on RxJS found at angular.io/resources

## Public speaking
I have done numerous public speaking at Tech conferences. If you want to hire me as a technical speaker contact me on Twitter @chris_noring or [Linked In](https://www.linkedin.com/in/christoffer-noring-3257061/)

Here is a complete listing of my speaking engagements:
[Speaking engagements](http://lanyrd.com/profile/chris_noring/)

## Organizer
Currently I organize 5 meetup groups for over 3000 developers.. In truth co organizers and the people who attend makes me better. It is a privilege to stand in front of you each and everytime and present something inspiring.

I am also one of the organizer for Angular Conference ngVikings, [ngVikings](https://ngvikings.org/)


## Latest posts

<div class="posts col-md-10">

  {% for post in site.posts %}
  <div style="border-bottom:solid 1px lightgray"> 
    <h3>  
      <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a>
    </h3>  
    <div style="color:grey; font-size: smaller">{{ post.date | date_to_string }}</div>   
    {{ post.content | strip_html | truncatewords:75}}<br>
    <a href="{{ post.url }}">Read more...</a><br><br>

  </div>
  {% endfor %}
</div>
<div class="col-md-2">
  {% assign tags = site.tags | sort %}
  {% for tag in tags %}
  <span class="site-tag">
      <a href="/tags.html#{{ tag | first | slugify }}-ref"
          style="font-size: {{ tag | last | size  |  times: 4 | plus: 80  }}%">
              {{ tag[0] | replace:'-', ' ' }} ({{ tag | last | size }})
      </a>
  </span>
  {% endfor %}


</div> 





