---
layout: post
category : angular
tagline: "angular animate"
tags : [tutorial, angular, javascript, animation]
---
{% include JB/setup %}

# Angular animate
So animation... It is built into angular, well angular animate that is. And it is pretty easy to use

Why animate stuff in the first place? Well it looks nice and its more fun to develop. But remember, less is more..

## Installation

	bower install angular
	bower install angular-animate
### Bootstrap

index.html

	<html>
		<body ng-app="app">
			<script src="bower_components/angular/angular.min.js"></script>
			<script src="bower_components/angular-animate/angular-animate.min.js"></script>

			<script src="app.js"></script>
		</body>
	</html

app.js

	(function(){

		angular.module('app',[
			ngAnimate
		])
	})();

	

## What can I animate
	- ngClass, add + remove
	- ngShow, add + remove
	- ngHide, add + remove
	- ngInclude, enter  leave
	- ngRepeat enter + leave + move 
	- ngView, enter + leave
	- ngIf,  
	- ngSwitch
	- probably something more I forgot about

### properties
A lot of properties can be changed from one value to the next. By category it roughly looks like this:

- color
- position
- size
- text


## Your first animation
Let's try animating *adding a class* through ngClass

	<div ng-class="{ aclass : property }"></div>

In this case *property* is *true* so that **aclass** appears. We need to think of the animation as happening in steps.

- Transition stage
- Active stage
- End stage

### Transition stage
This defines what animation to use, what properties to animate, for how long etc..

	.aclass-add{  
		transition : color 1s linear;
	}
This means we will animate **color** property , for **1 second**, using a **linear algorithm** 

### Active stage
This happens after the transition stage.

This class trigers the animation

	.aclass-add-active{

	}
This active class isn't needed when adding or removing something but is needed when dealing with enter/leave so it's a good rule of thumb to include it.

### End stage
Here we setup what the final result will look like:

	.aclass{
		color : blue;
	}

### Example
You click on a button that toggles the visibility of a an element.. This is typically something you would do to dismiss a message:

	.dismissed-message{
		opacity : 0;
	}

	.dismissed-message-add{
		transition: opacity 250ms linear;
	}



	transition : opacity 250ms linear;

#### The animation
A quick word on the transition:

Let's break it down:

	**opacity** is the property to animate
	**250ms** is the time it should take
	**linear** is the easing function that should handle the animation

Because you can animate more than one property in animation you can change the property to animate from a specific keyword to **all**. Like so:

	 transition : all 250ms linear;


## More examples
So I just showed that you can animate adding of a class.. However you can animate a lot of other stuff.. Let's look at how to animate a route change..
### route change
We will use the keywords enter/leave instead of add/remove as with the ngClass. Cause that is what will happen, content will enter the div and leave the div. 

index.html

	<div class="page" ng-view >
	     Everything is rendered here
	</div>

app.css

	// define the animation
	.page.ng-enter{
		opacity: 0.2;
		top: 600px;
		transition: all 350ms ease;
	}

	// trigger the animation, define end result
	.page.ng-enter-active{
		opacity: 1.0;
		top: 0;
	}

	// how page should be rendered normally
	.page{
		background: white;
		text-align: center;
		position: relative;
		min-height: 200px;
		box-shadow: 0 10px 6px -6px #777;
	}
In this case we get a content that comes flying from the bottom to the top..

### ng-repeat
Let's try to animate the adding and removing of items in a list.. As with ngView the keywords to use are enter/leave... 

#### Adding
First let's add some code to add an item to a list:

index.html

	<input ng-model="newitem" />
	<button ng-click="add()">add</button>
	<div class="item" ng-repeat="item in items">
	{{ item }}
	</div>	
Then let's setup the animations:

app.css
	
	// define animation 
	.item.ng-enter{
		opacity: 0.2;
		left: -400px;
		transition: all 250ms linear;
	}

	//trigger the animation and define end stage
	.item.ng-enter-active{
		opacity: 1.0;
		left: 0;
	}
#### Removing
Let's add some code so we can remove the item from the list

	<input ng-model="newitem" />
	<button ng-click="add()">add</button>
	<div class="item" ng-repeat="item in items" ng-click="remove($index)">
	{{ item }}
	</div>
As you an see we just added an ngClick to with a callback to a remove method..

And the css then, it looks pretty much like the **enter** css.

app.css

	// define animation
	.item.ng-leave{
		opacity: 1.0;
		left: 0px;
		transition: all 300ms ease;
	}

	//trigger the animation and define end stage
	.item.ng-leave-active{
		opacity: 0.0;
		left: 1200px;
		background-color: white;
	}

## Concluding
So this was just a short post to get you started and say it isn't all that hard to get started with animation in angular. Also the full source code to this blog post is available at : 

	https://github.com/softchris/angular-animate-examples

## Further reading
To deepdive please have a look at the following

### Official documentation

	http://www.nganimate.org/

### Deep diving
Have a look at the excellent course "Animating AngularJS Applications" by Kevin Weeks on Pluralsight

	