---
layout: post
category : angular
tagline: "first steps"
tags : [tutorial, angular, javascript]
---
{% include JB/setup %}

# Angular

So angularjs. Its easy to explain all the concepts and make this articles really long but as a developer I want to see code very early and I want information given to me in incremental steps. So if you are like me this should be really fun and hopefully educational.
## Worlds smallest app
	
	<div ng-app>
		{{ 1+2 }}
	</div>

ng-app is a so called angular directive that states, your app starts here. So your app lives inside this div tag.

### Expression {{}}

This is called an expression. Whats in here gets evaluated at runtime. So for example typing **{{ 1+2 }}** gets evaluated to **3**. I will return to expressions but this is all that I will divulge at this point to not make it too confusing.

## A little bigger app

To build something worth mentioning its important to start to understand concepts like SPA, module, routing etc. But lets take this one step at a time

###Angular module

In angular all larger components are made up of modules, angular modules. Even your application is an angular module. Let’s start with one module for now. Here is how to declare one

	angular.module('app',[])

So what happened here, two arguments, a name and []. This creates a module and says it has no dependencies hence the []. Lets add this to existing code so we get

	<div ng-app="app">
		{{ 1+2 }}
	</div>

NOTE, we changed **ng-app** to **ng-app=”app”** Cause now we have an application / module and it is called *app*. There needs to be a root module that gets pointed out by ng-app. So at this point we have a little more even though the whole SPA concept hasn’t been presented yet.
<h2>An app with routing</h2>
So lets put on the big boy pants, lets introduce what actually makes it a SPA, a single page application. We need two things, <strong>ng-view</strong> and <strong>routing</strong>

###ng-view

This is a directive that points out where in your page content should be switched out. Where one page should be substituted for another, hence the name <em>Single Page</em>

Lets add this to existing code

	<body ng-app="app">
		<div ng-view >
		// this content will be replaced
		</div>
	</body>


But this does nothing unless we start configuring our routes.

### Routing

I've been going on and on about routing, please explain..

Well routing is about what happens when the url changes. Our app will start at probably index.html. Angular kidnaps the hash sign **#**

So angular route looks like **http://someDomain/#products** or **http://someDomain/#products/114**

So for angular to make something out of this, we need to configure it in a way so it can handle a certain routing pattern.

####Configuring routing pattern

Lets just fokus on our javascript for a bit. 

    var app= angular.module('app',[])

    app.config(function($routeProvider) {
    	$routeProvider

        // route for index.html, start page
        .when('/', {
            templateUrl : 'partials/start.html',
            controller  : 'startController';
        })

        // route for the products
        .when('/products', {
            templateUrl : 'partials/products.html',
            controller  : 'productsController';
        })

        // route for the product detail page
        .when('/products/:id', {
            templateUrl : 'partials/productDetail.html',
            controller  : 'productDetailController';
        });

        .otherwise('/start');
	}); 


Wooah.. Lot's of things at once. Let's break it down

	var app = angular.module('app',[])

	app.config(function($routeProvider) {
		
- So we start be getting a reference to our app by creating a variable **app**

- We call the method **config**
- We give it a function and a reference to **$routeProvider**. This is an angular built in object that can handle the setup of routes.

So next step we start registering routes..

	$routeProvider
	.when('/', {
        templateUrl : 'partials/start.html',
        controller  : 'startController'
	})

Here we state that when url change angular should load controller **startController** and view **start.html**. This matches **http://someDomain/**

Next route

	// route for the products
	.when('/products', {
    	 templateUrl : 'partials/products.html',
     	controller  : 'productsController'
	})

Here we state that when url change to **/products** angular should load controller **productsController** and view **products.html**. This matches **http://someDomain/#products**

Next route is a little bit different it contains a **wildcard**

	.when('/products/:id', {
        templateUrl : 'partials/productDetail.html',
        controller  : 'productDetailController'
	});

This can be seen by the use of **:id**

Here we state that when url change to **/products/<someNumber>** angular should load controller **productDetailController** and view **productDetail.html**.

This mathes both **http://someDomain/#products/11** and
**http://someDomain/#products/99** and so on

Last route

	.otherwise('/start');

This is the if error go to route. Or rather what happened in the url does not match any known route scheme, go here.. In this case it goes to the previously configured start route.

####Create the link

So a lot about routing so far but how to actually navigate to these routes?

	<div>
        <a href="#products">To products</a>
        <a href="#products/11">To a specific product</a>
        <a href="#start">To start</a>
	</div>


	<div ng-view>This content will be switched out</div>

Above I added three links, for example

	<a href="#products">To products</a>

###View + Controller

Ok so you talked about routing view and controllers. There are stuff missing here.. Yes you are right, a view and a controller. Lets start with the controller

####Controller

A controller is something that can react to a UI change but also cause the UI to change. As was seen in the Route chapter a route change means we want to load a controller and a view and present something nice to the user. A controller is configured on an angular module. In this case we only have the one module.

	var app = angular.module('app',[]);
	
	app.controller('startController', function($scope){
    	$scope.test = "test";
	});

So we told our app module / our application it now has a controller called **startController**. A controller in angular is a **javascript function**, go figure ;) More on controllers later. Onto the view..

####The view

lets image first that we have the follwing project structure

	index.html
	angular.js
	partials/start.html

Remember our route for start, it looked like the following:

	$routeProvider
	.when('/', {
        templateUrl : 'partials/start.html',
        controller  : 'startController'
	});

We created the start controller and now we go on to create the file called <strong>start.html</strong>. This should of course contain some suitable html.

###Putting it all together

Lets create the remaining code that is needed for all our routes to work

	var app = angular.module('app',[]);
	app.controller('startController', function($scope){
    	$scope.test = "test";
	});

	app.controller('productsController', function($scope){
    	$scope.test = "test";
	});

	app.controller('productDetailController', function($scope){
    	$scope.test = "test";
	});

And create the corresponding views so your project structure looks like.

	index.html
	angular.js
	partials/start.html
	partials/products.html
	partials/productDetail.html

###Cleanup and resulting project structure

So now we have a little more files but it looks like a mess. Its time to cleanup. first thing is that we want javascript to leave <strong>index.html</strong> and be put in its own file. We create **app.js** and put the following code in there

	var app = angular.module('app',[]);

	app.controller('startController', function($scope){
    	$scope.test = "test";
	});

	app.controller('productsController', function($scope){
    	$scope.test = "test";
	});

	app.controller('productDetailController', function($scope){
    	$scope.test = "test";
	});

So at this point we get the following in index.html:

	
	<div>
		<div>
			<a href="#products">To products</a> 
			<a href="#products/11">To a specific product</a> 
			<a href="#start">To start</a>
		</div>
		
		<div ng-view>This content will be switched out</div>
	</div>

We can do better though, lets move out the controller declarations from app.js into its own file **controllers.js**

	app.controller('startController', function($scope){
    	$scope.test = "test";
	});

	app.controller('productsController', function($scope){
    	$scope.test = "test";
	});

	app.controller('productDetailController', function($scope){
    	$scope.test = "test";
	});

Yes we can do that even better but we let it be for now.
A quick recap on project structure means we have:
	
	angular.js
	app.js
	controllers.js
	partials/start.html
	partials/products.html
	partials/productDetail.html
	index.html

We fix that a little so we get

	js/libs/angular.js
	js/app.js
	js/controllers.js
	partials/start.html
	partials/products.html
	partials/productDetail.html
	index.html

### Controllers

So far the only thing we did with controllers was this

	app.controller('startController', function($scope){
    	$scope.test = "test";
	});

And pointing it out in a router like this

	$routeProvider
    .when('/', {
        templateUrl : 'partials/start.html',
        controller  : 'startController'
    })

But there is more to the story:

#### Using a controller

A controller can be pointed out using either **$routeProvider** or with a directive **ng-controller**. Using it with **ng-controller** is done in the following way:
	
	
	<div ng-controller="aController">
		// everythng in here is in the context of the controller
	</div>


This is very common in index.html for example or parts of your page that you will **NOT** come to by navigation.
So this is a very common way of using them:

	
#### The scope

So we have mentioned the <strong>scope</strong> this far but havn't really explained it in details.
Again with the simple code example:

	app.controller('startController', function($scope){
    	$scope.test = "testing";
	});

What we are saying here is that a controller has property **test** with the value **testing**. Well technically it's the **$scope** who holds the property. But the scope is accessible from the view in the following way:

	// prints 'testing'
	<div>{{test}}</div>
	
#### Alternate notation - no scope

Not everybody uses the scope. Some uses **this**. This is a matter of taste I guess. This is how it works:

In the controller:

	app.controller('startController',function(){
    	this.prop = 'test';
	});

In the view:

	<div ng-controller="startController as vm">
		<div>{{ vm.prop }}</div>
	</div>

This is a very short explanation but it will keep you going :)
