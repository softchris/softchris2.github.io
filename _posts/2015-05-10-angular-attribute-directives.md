---
layout: post
category : angular
tagline: "angular heart browserify"
tags : [tutorial, angular, javascript, browserify, nodejs]
---
{% include JB/setup %}

## Attribute Directives

So far I have been talking about element directives and the three different modes through which you can pass data. Attribute directives is built on the same principles of course but their application is different. Attribute directives are *decorator* directives and therefore we will look at situations where 'elements' or even other 'element directives' can be decorated..

The first thing before starting out is the feeling that you have a piece of logic that belongs elsewhere..

### Example I - The expander

You have probably seen this example a lot of times before but I thought we would warm up with this.

#### What should it do

In short it should help us expand an element. The way this is normally achieved is by clicking a header and the body would either collapse or expand depending on previous mode.

We assume we have the following html that is in need of a decorating *expander directive*

	<div>
		<div class="header">
			Some title text
		</div
		<div class="body">
			Some body text that will be collapsed or expanded
		</div>
	</div>
So we need two things to accomplish this

1. a scope property that will determine wether to show the body 
2. a place to put our directive, either ON the parent element or ON the header

Let's change the html to the following

	<div expander>
		<div class="header">
			Some title text
		</div
		<div class="body" ng-show="show">
			Some body text that will be collapsed or expanded
		</div>
	</div>

Two things were added

	<div expander> , new attribute, our directive
And

	<div class="body" ng-show="show">  // ng-show and scope property 'show'
			Some body text that will be collapsed or expanded
	</div>	

#### Our directive

	app.directive('expander', function(){
		return {
			link : function(scope,element, attributes){
				scope.show = false;
				
				var headerElement= element.find('.header');
				headerElement.on('click',function(){
					scope.$apply(function(){
						var oldValue = scope.show;
						scope.show = !oldValue;
					});
				});
			}
		}
	});

So this is it. These few lines of code will make it work but let's break it down, we used some new concepts here.

	link : function(scope, element, attributes){}

So the link phase of a directive is where you hook up events and also enables you to talk to the element. That is exactly what we want to do, we want to know when someone clicked our header element so we can alter the **show** state. 

So what the code does line by line is

Find the header element

	var headerElement= element.find('.header');

But wait isn't this? Yes IT IS, our element is a jquery element and can do fun stuff like
 
	element.val()
	element.html()
	element.css('prop','value')
	element.find('expression') 		

With that knowledge next line make sense, we listen to an event:

	headerElement.on('click',function(){});

BUT, something interesting is happening inside the callback, we call **scope.$apply**, so why?

Well it's because listening to this kind of click event is outside of angulars world and affecting a scope variable value means we need to tell angular we want to play, so therefore we call scope.$apply

	scope.$apply(function(){
		var oldValue = scope.show;
		scope.show = !oldValue;
	});

We will see more of this in the upcoming example. 
 
### Example II - The validator

One of the first things you tend to write when creating a directive is your own validation control. Especially if you feel that min/ max or ng-pattern won't be enough because your client or employer have a very specific way of validating their email, postal address or whatever it may be. And as a developer you sometimes like that you will have to build your own thing, right? :)

However for this example I will build a relatively generic pattern validator that you could easily customize to fit your needs...

So before starting out I will try to imagine how I would want to use it. 

I imagine it will be used something like this :

	<input type="text" pattern-validator="^[a-zA-Z]+$" pattern-description="Only letters" />

What do we have here, two new attributes to this input element

	pattern-validator="^[a-zA-Z]+$"

And

	pattern-description="Only letters"

So one of them is the pattern to validate against and the other is some help text meant for the user..

#### Let's build

	app.directive('patternValidator',function(errorSrv){
		return {
			link : function(scope,element, attr){
				var pattern = attr['patternValidator'];
				var regexp = new RegExp(pattern);
				
				var defaultColor = element.css('background');
				
				element.on('blur',function(){
					if (!regexp.test(element.val())){
						element.css('background','red');
						element.css('border-color', 'red');
					}else{
						element.css('background',defaultColor);
						element.css('border-color', '');
					}
					
				});
			}
		}
	});

So what is happening here first line of interest is this :

	var pattern = attr['patternValidator'];

We can talk to attributes on the same element that the directive is applied to

Example

	<div my-directive attr1="test" attr2="test" attr3="test" attr4="test" />

attr1..4 is accessible in the **attr** dictionary

Next interesting line is:

	var defaultColor = element.css('background');

But we are not surprised at this point because, as stated earlier, element is a juery element and can call jquery methods

Next line of interest :

	element.on('blur',function(){});

Here we can see that we listen to the **blur** event. I.e when the element looses focus which is oftentimes when you want your element to run validation.

#### Summarizing

This is a very simple validation directive that only css wise affects the element it is applied to. Usually however we want a validation to do more than just marking your element red. You want a validation text to be displayed so the user can correct the element. So how to do that?

#### Validation element - take two

So we established we want to know what we did wrong so we can fix it. One way of approaching this is either create an element next to our input element that displays what is wrong, a description. But the problem with that is the placement of that error text.. What if we for example have several input elements, will it look nice ? I'm sure that it is solvable but it seems in this case it is more natural to simplify by adding a collaborator - an error service.

An error service you say, for what purpose..? Well the idea is that if the directive can speak to the error service and say *what is in my text field doesn't validate*, then some other controller or directive could easily use the same error service and display the errors.

Alright lets do it:

##### The error service

	app.service('errorSrv',function(){
		var me = this;
		var errors = [];
		
		me.getErrors = function(){
			return errors;
		};
		
		me.add = function(error){
			if (errors.indexOf(error) === -1)
				errors.push(error);
		};
		
		me.remove = function(error){
			var index = errors.indexOf(error);
			errors.splice(index,1);
		}
	})

so we created our service and added some helper methods which will allow us to create / delete and retrieve the errors.

###### Use error service in directive

	app.directive('patternValidator',function(errorSrv){
		return {
			link : function(scope,element, attr){
				var pattern = attr['patternValidator'];
				var regexp = new RegExp(pattern);
				
				var id = attr['id'];
				var desc = attr['patternDescription'];
				
				var errorMessage = id + ' not matching pattern: ' + pattern;
				var defaultColor = element.css('background');
				
				element.on('blur',function(){
					scope.$apply(function(){
						if (!regexp.test(element.val())){
							errorSrv.add({ name :  errorMessage, description : desc });
							element.css('background','red');
							element.css('border-color', 'red');
						}else{
							element.css('background',defaultColor);
							element.css('border-color', '');
							errorSrv.remove(errorMessage);
						}
					});
				});
			}
		}
	});

We added 

	var id = attr['id'];
	var desc = attr['patternDescription'];
				
	var errorMessage = id + ' not matching pattern: ' + pattern;

And in the **blur** event callback, we added the following:

IF NOT validating

	errorSrv.add({ name :  errorMessage, description : desc });

IF validating

	errorSrv.remove(errorMessage);

##### Resulting html - display errors

	<input id='testInput' type="text" pattern-description='only letters' pattern-validator="^[a-z]+$" placeholder="only text"></input>
	<br/>	
	<input id='testInputNumbers' type="text" pattern-description='only numbers' pattern-validator="^[0-9]+$" placeholder="only numbers"></input>
		
	<div>
		<ul>
			<li ng-repeat="error in ctrl.errorSrv.getErrors()">
				{{error.name}} <i>{{error.description}}</i>
			</li>
		</ul>
	</div>

Here we have two input elements both utilizing pattern-validator directive and also a list construction showing all the errors in a repeater. 
Which of course means that the controller has a construction like this

	$scope.errorSrv = errorSrv // with errorSrv being dependency injected

##### Full code

HTML

	<div ng-controller="appCtrl as ctrl" 		 
			style="border: solid 1px gray; border-radius:5px">
			
			
		<input id='testInput' type="text" pattern-description='only letters' pattern-validator="^[a-z]+$" placeholder="only text"></input>
		<br/>	
		<input id='testInputNumbers' type="text" pattern-description='only numbers' pattern-validator="^[0-9]+$" placeholder="only numbers"></input>
			
		<div>
			<ul>
				<li ng-repeat="error in ctrl.errorSrv.getErrors()">
					{{error.name}} <i>{{error.description}}</i>
				</li>
			</ul>
		</div>
		<br/>
			
			
			
	</div>

Javascript

app.js 

	app.controller('appCtrl',function(errorSrv){
		var me = this;
		me.title = 'test';
		me.errorSrv = errorSrv;
	});

patternvalidator.js

	app.directive('patternValidator',function(errorSrv){
		return {
			link : function(scope,element, attr){
				var pattern = attr['patternValidator'];
				var regexp = new RegExp(pattern);
				
				var id = attr['id'];
				var desc = attr['patternDescription'];
				
				var errorMessage = id + ' not matching pattern: ' + pattern;
				var defaultColor = element.css('background');
				
				element.on('blur',function(){
					scope.$apply(function(){
						if (!regexp.test(element.val())){
							errorSrv.add({ name :  errorMessage, description : desc });
							element.css('background','red');
							element.css('border-color', 'red');
						}else{
							element.css('background',defaultColor);
							element.css('border-color', '');
							errorSrv.remove(errorMessage);
						}
					});
				});
			}
		}
	});

And as you already figured out maybe its not the responsbility of the controller to display the errors. Maybe it should be in a directive?

	app.directive('errorSummation',function(){
		return {
			replace : true,
			restrict : 'E',
			scope : {},
			controller : function(errorSrv){
				scope.errorSrv = errorSrv;
			},
			templateUrl : 'errorSummation.html'
		}
	});

errorSummation.html

	<ul> 
		<li ng-repeat="error in ctrl.errorSrv.getErrors()"> {{error.name}} <i>	{{error.description}}</i>
		</li>
	</ul>

Hope you had a good reading so far. In the next blog post I will be covering how to wrap external components like graphs, jquery ui etc inside of directives.