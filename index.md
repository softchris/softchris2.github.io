---
layout: page
title: Christoffer Noring - blog
tagline: Never stop learning
---
{% include JB/setup %}


# Latest posts

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





