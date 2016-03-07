---
layout: post
category : angular
tagline: "angular heart browserify"
tags : [tutorial, angular, javascript, browserify, nodejs]
---
{% include JB/setup %}

# Browserify heart angular


## The problem

You probably have a project structure looking something like this.

**Project structure**

	app.js
	/infrastructure
		/login
		/notification
		/interceptors
	/features
		/user
		/products
		/projects
		/...
	/vendor
	/resources

And more importantly an **index.html** file looking like this	

	<body>
		...
		<script src="infrasctructure/login/login.js"></script>
		.
		.
		.
		<script src="features/login/login.js"></script>
		.
		.
		.
		
		// vendor
		<script src="bower_components/angular/angular.min.js"></script>
		.
		.
		.
		100 or so script tags..
	</body>
	
index.html

You think this is **OK** because this is how you think it MUST be in development stage and once you create a deploy there will be just a few files because you :

- concatenated, 
- minified 
- uglified 

it into a few neat files

It really doesn't, there is no reason why you can't have it make look good even in development stage.

Myself I was looking into a way to get rid of the script tags from index.html and found that there was such a thing as an import tag which would let me split up my script tags into several files. One for each topic. Which would lead to a structure looking like this

	<script rel='import' src='features.html'></script>
	<script rel='import' src='infrastructure.html'></script>
	<script rel='import' src='vendor.html'></script>

Example features.html

	<script src="feature1"></script>
	<script src="feature2"></script>
	<script src="feature3"></script>
	<script src="feature4"></script>
	<script src="feature5"></script>
	<script src="feature6"></script>

So this is not the perfect solution to the problem but at least there is some organization among your script tags  which makes it easier to maintain.

One major problem though. The browser vendors doesn't support this feature fully. Chrome does. Firefox has it disabled. It used to work for IE with a polyfill but currently it doesn't. So what do? The answer currently is to use Browserify.

## The solution

### Browserify, what is it

Browserify is meant to be several things. 

- A bundling tool for your javascript files
- A module system like requirejs 
- It let's you use node modules in the browser !

It uses the CommonJs

### CommonJs

CommonJs is a standard meant for serverside javascript where each javascript file is meant to be a module. When I say meant for serverside it is the fact that file access server side are fast whereas in the browser you run into problem with too many file accesses made and thats why you usually create a bundled file.

The standard specifies variables such as *require*, *exports* and *module*

	var aModule = require('aModule');

aModules.js

	function doStuff(){

	}

	module.exports = doStuff; 

### Browserify that project

So we said that browserify will help us create order and remove script tags but how...

Let's first look at what a normal angular project might look like when adding a construct like a factory

Example **userModel.js**

	angular
		.module('app')
		.factory('User', function(){
			function User(){}

			User.prototype.doSomething = function(){}

			return User;
		}) 

Browserify it in this case means we require our module. And the module in this case is the anonymous callback associated with 'User'. So we create an isolated module looking like this:

	 	function User (){
			function User(){}

			User.prototyp.doSomething = function(){}

			return User;
		}

		module.exports = User;

And the first file we turn into this:

	angular
		.module('app')
		.factory('User', require('./User'));

So if we go back to our enterprise project with user being a feature it is likely that it consist of a lot of models, controllers, filter, directives etc. It is likely it looked something like this


	features/user
				userModel.js
				userController.js
				userDirective1.js
				userDirective2.js
				userDirective3.js
				userFilter.js

You already saw how we could redo our userModel.js and create a module file and keep the file where it is registered as a factory. 

But **wait** you say won't this create two times as many files if I am do redo all of the js files above?

I failed to mention one important thing. We will need to make sure we only have one file where we do all registrations to the angular module, like so:

	angular
		.module('app')
		.factory('userModel',require('./userModel'))
		.controller('userController',require('./userController'))
		.directive('userDirective1',require('./userDirective1'))
		.directive('userDirective2',require('./userDirective2'))
		.directive('userDirective3',require('./userDirective3'))
		.filter('userFilter',require('./userFilter'))			

And because this is CommonJs we are smart about this, we place it in a file called index.js 
You will soon see why.

In every project there is a bootstrap file that starts it all, it is usually called **app.js**. In angular this is usually filled with application wide configuration like so:

	module('app',['module1','module2'...]) 	
		.config(function($routeProvider){

		})
		.constant('baseUrl','http://mydomain/')
		.value('val','some value')
		.
		.
		.

Anything that is added to the core module **app** is referred to in its respective file.
Basically what we had before with userModel, userController etc, that is we refer to core module and it the construction we want like so

	angular
		.module('app')
		.factory('UserModel'...) 

But we changed stuff. We created an index.js and placed all our wireup in there so the user feature looks like

	features/user
				index.js
				userModel.js
				userController.js
				userDirective1.js
				userDirective2.js
				userDirective3.js
				userFilter.js

So for something 'user' to be accessible for the rest of the application we need to make angular aware of what is in there so we need to add a line to app.js 
			
	module('app',['module1','module2'...])
	
	require('./features/user')  // this automatically looks after user/index.js

So this way angular is made aware of anything in the user feature.

## Gotcha

- Yes we need to rethink how we declare and register our constructs with angular
- Yes we need to create an extra index.js for all registrations belonging to a feature

But we also gain something. Our index.html is a lot cleaner

index.html

	<body>
		<script src="angular.js"></script>
		<script src="bundle.js"></script>
	</body>

But wait there is more. Each and every CommonJs module we created like userModel.js and userController.js etc are completely independent of angular. And that means they are a lot easier to test.

## Creating your app with browserify - step by step

So far I've been trying to convince you why browserify might be worth trying for your angular application. But lets look how to really put it into action

### Setup and install

Install browserify

	npm install -g browserify
### Create your first browserify project

Let's create something simple that isn't angular just yet, just to try out browserify

	index.html
	app.js
	math.js
#### The page

The target is to create one bundled file and refer to this in index.html like so

index.html

	<body>
		<script src="bundle.js"></script>
	</body>

#### Math module

Lets create a module we can refer to when we need it.

math.js

	function add(lhs, rhs){
		return lhs+ rhs;
	}

	module.exports.add = add;
	
#### Bootstrapper

app.js

	var math = require('./math');
	console.log(math.add(1,2));

#### Bundle it

	browserify app.js -o bundle.js

As first command to browserify we give it **app.js**. Cause this is where everything starts.
The second command is the output **bundle.js**

#### Run app

If you created all the files and performed the browserify command and run index.html in a browser
you should have the console stating **3**

But wait you just showed me a way to create a bundled mess of concatenated files, how is this easy to deal with while coding?  The answer is to call browserify differently during code and during the creation of a deploy. When coding you still want all your files split up right? So this is ho

	browserify app.s -o bundle.js -d

We added -d at the end to preserve the file structure. And yes to retain that info the bundle.js is twice as big but that's ok because it is dev mode.

Of course browserify doesn't solve everything you still need to uglify minfy and all the rest, what it helps you with is:

	- concatenation
	- use node modules
	- use CommonJs (require)

### Browserify + angular

So now you know a little more how browserify works. It bundles files obviously. But there is one more thing you should know and that is you can use node modules in the browser. Hence the name BROWSERIFY. 

But that means that our angular that we usually get from bower is a bad candidate, well NO because angular can be had even as a node module.

So create a brand new project looking like this

	index.html
	package.json
	app.js
	user/
		userController.js
		userInfo.js
		index.js

Then run 

	npm install angular
To get angular as a node module

Then implement the files in the following way

app.js

	require('angular/angular')

	angular.module('app',[]);

	require('./user')

Worth noting here is that angular is imported from an angular directory in node_modules and it is added to the windows object like so:

	windows['angular'] = angular;

So thats why you just do

	require('angular/angular')

And don't catch any reference to it.

index.html

	<html>
		<body ng-app='app'>
			<div ng-controller="userController">
			
				<user-info></user-info>
			</div>
			<script src="bundle.js"></script>
		</body>
	</html>
The thing worth noting here is the reference to bundle.js. A file we havn't created yet as we havn't run browserify yet.

user/index.js

	angular
		.module('app')
		.controller('userController',require('./userController'))
		.directive('userInfo',require('./userInfo'));

So in  user/index.js we setup all our components. Note how we require the userController and userInfo as node modules.  

user/userController.js

	function UserController($scope){
		$scope.test = 'browserified';	
		$scope.user = {
			name : 'pelle'
		};
	}

	module.exports = UserController;

user/userInfo.js

	function UserInfo(){
		return {
			template : '<h1>{{user.name}}</h1>'
		}
	}

	module.exports = UserInfo;

Both userController and userInfo are created as node modules and are exposed as public functions by doing:

	module.exports = functionName;

### Testing

So we realized we needed to install angular as a node module. We needed to rethink project structure but we created our components as separate modules which actually makes testing become a breeze. No amount of angular needs to be involved. You just need a testing framework that can test node modules.

#### Unit testing with node unit

So for example you can unit test you functions with the excellent node unit, https://github.com/caolan/nodeunit

A spec file would look like this:

	exports.testUserModel = function(test){
		var productSrvMock = { 
			get : function(){
				console.log('typing mock from productSrv');
		}};
	
		var UserModel = require('../../features/user/user')(productSrvMock);
		var user = new UserModel({ name : 'pelle' });
    	
    	test.ok(user.title === 'pelle', "should set title to Pelle");
    	test.done();
	};

Worth nothing is the require to get a hold of your service/ factory / controller whatever you need to test. 

As you can see no 

**Lengthy** karma.config.js file specifying where all your files are, any angular dependencies. 

**No** module + service import like this:


	beforeEach(module('moduleName'));

	beforeEach(inject(function(_service1_, _service2_) {
    	service1 = _service1_;
    	service2 = _service2_;
    	
  	}));

Just straight on require, and you are good to go. And mocking couldn't be simpler. For example assume that a productService looks like the following:

	function productService(Product, Category, pricingService){

	}

Here you have several dependencies. And you probably have the above dependencies in different angular modules which means you need to create boilerplate code looking like this:

	beforeEach(module('services'));
	beforeEach(module('models'));

	$provide.service('pricingService', function() {
      // perform mocking behaviour
    });

	beforeEach(inject(function(_productService_) {
				

    	productService = _productService_;
    	
    	
  	}));

Same scenario would look like

	exports.testProductService = function(test){
		var pricingServiceMock = { 
			get : function(){
				console.log('typing mock from productSrv');
		}};
	
		var Product = require('./models/product');
		var Category = require('./models/category');
		var productService = require('./services/productService')(Product,Category, pricingServiceMock);
		
    	
    	test.ok(user.title === 'pelle', "should set title to Pelle");
    	test.done();
	}; 

As you can see, so much simpler and cleaner and no talking to angular.

Thats all folks.. now go browserify :)
