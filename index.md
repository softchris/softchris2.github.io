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

Currently I organize 4 meetup groups for over 1100 developers.. In truth co organizers and the people who attend makes me better. It is a privilege to stand in front of you each and everytime and present something inspiring.

    
## Latest posts

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>





