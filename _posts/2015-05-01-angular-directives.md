---
layout: post
category : angular
tagline: "the most powerful construct"
tags : [tutorial, angular, javascript]
---
{% include JB/setup %}

# Directives

## First directive

Declaration

	angular.module('app').directive('testDir',function(){
		return {
			restrict : 'E',
			replace : true,
			transclude : true,
			template : '<h1>test</h1>'
		}
	})

Usage

	<test-dir></test-dir>

Casing matters: testDir becomes **test-dir** in view

### Restrict

What can it be applied to:

- Comments
- Elements, E
- Attributes, A
- CSS

### Replace

Determines wether the custom element is being replaced with view content or just added to it

**false**

	<test-dir>
		 <h1>test</h1> // view content
	</test-dir>

**true**

	<h1>test</h1> // view content
	
### template, templateUrl

template : 
Points to inline defined html like so:

	template : '<h1>test</h1>'

templateUrl : 
points to a view file like so:

	templateUrl : 'views/view.html'

view.html
	
	<h1>test</h1>
	some text

GOTCHA:
with templateUrl/template you need to have only **1 root element**

**VALID**
	
	<div>
		<h1>test</h1>
		<div>
			content
		</div>
	</div>

**NOT VALID**	

	<h1>test</h1>
	<div>
		content
	</div>

### transclude

This is a property that governs what happens with content defined inside of your custom element.

Example

	<custom-element>
	I want this to show too
	</custome-element>	
	 
To show *that content* it needs to be specified within the template, or templateUrl like so:

	template : '<h1>test,<span ng-transclude></span></h1>'	

This will render

	<h1>test I want this to show too</h1>

That is **test** is concatenated with **I want this to show too**

## Second directive - lets play with scope

So this far we have only played with static data which of course can be interesting but the real value lies in 

1) the directive having its own scope 

or 

2) displaying the value from a parent controller scope 

or 

3) interacting with its parent controller

Lets first imagine a good scenario where you want to have a directive in the first place. *A user page*. We imagine it shows something like

- user metadata like name, description, member since etc..
- all purchases made
- social functions like all users you are friends with

With angulars directives you can create your own custom elements so we can have a view looking like this

	<div ng-controller="userController">	
		<user-metadata></user-metadata>
		<user-purchases></user-purchases>
		<user-friends></user-friends>
	<div>
Thats a nice looking view right, so clean?

Let's start building it:
We assume that the user controller already fetched us the user with all the data we need and that the user data is placed on the userController scope like so:

	scope.user = { 
		'name' : 'carl', 
		'created' : '2011-01-01',
		'surname' : 'barks', 
		'purchases' : [], 
		'friends' : []  
	}
	
### Inherited scope

Now we can start talking about scope, i.e state and the fact that a directive default gets the  scope of its controller. That is, our yet to exist user-metadata will have access to the userController's scope.

#### Building user-metadata

##### js file + with template included

	angular
		.module('app')
		.directive('userMetadata',function(){
			return {
				inherited : false, // default, we get the controllers scope
				restrict : 'E',
				replace : true,
				template : '<div> <h1>{{user.name}}</h1><i>{{user.created | date: 'shortDate'}}</i> </div>'
			}
		})

#### Building user-purchases

##### js file

	angular
		.module('app')
		.directive('userPurchases',function(){
			return {
				inherited : false, // default, we get the controllers scope
				restrict : 'E',
				replace : true,
				templateUrl : 'user-purchase-view.html'
			}
		})
##### view file

user-purchase-view.html

	<div>
		<table>
			<tr ng-repeat="item in user.purchases" >
				<td>
					{{item.name}}
				</td>
				<td>
					{{item.price}}
				</td>
				<td>
					{{item.quantity}}
				</td>
			</tr>
		</table>
	</div>

#### Building user-friends

##### js file

	angular
		.module('app')
		.directive('userFriends',function(){
			return {
				inherited : false, // default, we get the controllers scope
				restrict : 'E',
				replace : true,
				templateUrl : 'user-friends-view.html'
			}
		})

##### view file

user-friends-view.html

	<div>
		<table>
			<tr ng-repeat="item in user.friends" >
				<td>
					{{item.name}}
				</td>
				<td>
					{{item.age}}
				</td>
			</tr>
		</table>
	</div>

### Inherited scope = true

At some point you want more standalone directives for each of them to hold their own data. For example let's say you want to create *notes* or *blog posts*. You want these to be unique. Maybe they have some common data like **who** created them that could be interesting to inherit but other than that you want their content to be NOT SHARED. This is exactly when you should use **inherited=true**, some parts are shared and some are your own data.

#### Building a notes directive

##### js file

	angular
		.module('app')
		.directive('myNote',function(){
			return {
				inherited : true, // default, we get the controllers scope
				restrict : 'E',
				replace : true,
				templateUrl : 'my-note-view.html',
				controller : function($scope){
					$scope.title = 'default title';
					$scope.content = 'default content';
					$scope.id = 0;  // not set before it is being saved
					$scope.save = function(){
						//mockup code for persisting
						if($scope.id === 0){
							$http.post(baseUrl + '/notes/',
							{ 
								  title : $scope.title, 
								  content : $scope.content,
								  user : $scope.user.name // belongs to controller
							})
							.then(function(result){
								$scope.id = result.data.user.id;  // we send the created user back
							});
						}else{
							$http.put(baseUrl + '/notes/',
							{
								  id : $scope.id, 
								  title : $scope.title, 
								  content : $scope.content,
								  user : $scope.user.name // belongs to controller
							});
						}
						
					}
				}
			}
		})

##### view file

	<div>
		<p>
			<input type="text" ng-model="title" placeholder="title" />
		</p>
		<p>
			<input type="text" ng-model="content" placeholder="title" />
		</p>
		<p>
			<button ng-click="save()" >Save</button>
		</p>
	</div>

#### Final code

So we have a directive that can save a note and the best part is we can have as many as we want, like so:

	<div ng-controller="userController">
		<my-note> <br/>
		<my-note> <br/>
		<my-note> <br/>
		<my-note> <br/>
		<my-note> <br/>
		<my-note> <br/>
		...
	</div>

### Isolated scope

So of course there is a third mode, **isolated mode**. This is the most common mode to use, why?
Well because with the first two you are dependant on placing your directives in a controller environment that actually has the data you mean to render. If it doesn't then your directives won't work as intended.

You can easily see you are dealing with an isolated scope by the fact the scope is a property in the directive declaration

	angular
		.module('app')
		.directive('isolatedDirective',function(){
			return {
				restrict : 'E',
				replace : true,
				templateUrl : 'view.html'
				scope : {
					products : '='
				}
			}
		})

Note this addition:

		scope : {
					products : '='
				}
The fact that the property is there tells us we are dealing with an isolated scope but it also tells us something else, there is a **products** property and an equal sign **=**

The equal sign means that the surrounding controller passes us data from one of its properties.
More on that later. BUT the major point being made here is that completely isolated means that properties from the controller doesn't *leak* to the directive unless we explicitly WANT it to.

There are three ways in which we can voluntarily receive data from the outside:

- by property, =
- by value, @
- by function callback, &

As mentioned before the equal sign **=** means we are passing the data from a scope and it is binded to our directive. Binded in the sense that if it changes the directive will rerender.

Looking at our directive again we start thinking of a use case where an isolated scope is good and come to think of a calender browser. A directive that lets you navigate through days. Imagine we have a booking application and you want to see which guests have been booked which day. Your probably will have a view looking like this:

	<div ng-controller="bookingController">
		<day-browser></day-browser>
		<bookings></bookings>
	</div>   

From a functionality standpoint you got two directives doing two completely different things but you want some way of notifiying the other controller. This is not an ideal use case but it will have to do as a demo.

#### Scenario

Day-browser directive should be able to navigate back and forth in time and bookings directive should get new content based on what day it is.

##### day-browser directive

js file

	angular
		.module('app')
		.directive('dayBrowser',function(){
			replace : true,
			scope : {
				update : '&'
			}
			templateUrl :'dayBrowser.html',
			controller: function($scope){
				$scope.currentDay = new Date();
				$scope.increment = function(){
					$scope.currentDay.addDays(1);
					update({ day : $scope.currentDay});
				}
	
				$scope.decrement = function(){
					$scope.currentDay.addDays(-1);
					update({ day : $scope.currentDay});
				}
			}
		})

view

	<div>
		<button ng-click="increment()">+</button>
			<input type="date" ng-model="currentDay" />
		<button ng-click="decrement()">-</button>
	</div>


##### bookings directive

js file

	angular
		.module('app')
		.directive('booking',function(){
			replace : true,
			scope : {
				bookedRooms : '='
			},
			templateUrl :'bookings.html'
		})

view

	<table>
		<tr ng-repeat="booking in bookedRooms">
			<td>
				{{booking.name}}
			</td>
			<td>
				{{booking.date}}
			</td>
		</tr>
	</table>

bookings controller

	angular
		.module('app')
		.controller('bookingsController',function($scope){
			$scope.update = function(day){
				$http.get(baseUrl + '/bookings/'+ day)
				.then(function(result){
					$scope.bookings = result.data; 
				});
			}
		})

#### Complete view code

	<div ng-controller="bookingController">
		<day-browser update="update(day)" ></day-browser>
		<bookings booked-rooms="bookings" ></bookings>
	</div> 

GOTCHA: 
passing callbacks is somewhat tricky. To invoke your callback, given that it has arguments means that you need to do the following:

- declare the arguments when you declare the directive html
- invoke callback with object literal

Example

Declare

	<my-directive update="update(a,b,c)"

Invoke from directive

	controller : function($scope){
		$scope.update({ a : 1, b: "test", c: true })
	}

There are more than one way to do this but if you forget the declaration it won't pass your arguments.


This is only intended to demonstrate how you can communicate with an isolated scope. Probably in a real application you would use a broadcast for these kind of events, such as changing a day but ultimately as always it depends on the situation :)

There are many more things that could be said about directives but I hope I got you curious. In an upcoming post I will talk more on *link* and *compile* and also how you can *wrap* other javascript code in a directive.
	