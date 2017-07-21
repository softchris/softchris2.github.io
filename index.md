---
layout: page
title: Christoffer Noring - blog
tagline: Never stop learning
---
{% include JB/setup %}

## About me
I am a developer since 10 years roughly.. I love to code and spread knowledge.
I love javascript and its accompanying eco system such as grunt, gulp, webpack  angular, react, vue etc..

Google Developer Expert,  [Google Developer Expert](https://developers.google.com/experts/people/christoffer-noring)
Telerik Developer Expert [Telerik Developer Expert](http://developer.telerik.com/community/developer-experts/)

I am currently writing a book on RxJS found at angular.io/resources RxJS Ultimate

## Public speaking
I have done numerous public speaking at Tech conferences. If you want to hire me as a technical speaker contact me on Twitter @chris_noring or [Linked In](https://www.linkedin.com/in/christoffer-noring-3257061/)

Here is a complete listing of my speaking engagements:
[Speaking engagements](http://lanyrd.com/profile/chris_noring/)

Here is a talk from ngVikings conference
[RxJS - everything is a stream](https://opbeat.com/community/posts/rxjs-everything-is-a-stream-by-christoffer-noring/)

### Upcoming talks in 2017
#### September

[Frontend Connect Warsaw, Poland 22-23 Sep](http://frontend-con.io/#speakers)
, Talk title: GraphQl vs REST 

[Nativescript Developer day, Rotterdam, Netherlands 29 Sep]() ,Talk title : TBD


#### October
[Angular Mix, Orlando 10-12 Oct](https://angularmix.com/#!/speakers/Christoffer%20Noring), Talk title(s): HOW SPAS GOT THEIR GROOVE BACK STARRING ANGULAR AND RXJS 

[International Javascript Conference, Munich 23-27 Oct](https://javascript-conference.com/programm-de/), Talk title(s): React + Redux  & RxJS 

#### November
[Oredev, Malmo 6-10 Nov](http://oredev.org/2017/sessions), Talk title(s): RxJS 

[DevReach, Sofia 13-14 Nov](http://devreach.com/), Talk title(s): TBD 



## Organizer
Currently I organize 6 meetup groups for over 3000 developers.. In truth co organizers and the people who attend makes me better. It is a privilege to stand in front of you each and everytime and present something inspiring.

I live in London nowadays so it's mostly the meetups here that I have time to actively manage.

Here are my groups:

[London Javascript](https://www.meetup.com/London-Javascript/)

[Nativescript London](https://www.meetup.com/NativeScript-London/)

[Frontend Gbg](https://www.meetup.com/Frontend-Gbg/)

[Software Craftsmanship Goteborg](https://www.meetup.com/Software-Craftsmanship-Goteborg/)

[Angular Gothenburg](https://www.meetup.com/AngularJS-Gothenburg/)

[Angular Stockholm](https://www.meetup.com/ngStockholm/)


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





