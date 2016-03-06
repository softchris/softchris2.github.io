---
layout: post
category : angular
tagline: "angular 2 basics"
tags : [tutorial, angular2, javascript, typescript]
---
{% include JB/setup %}


# angular 2
## supported languages

- es5
- es6
- typescript
- Dart


## Bootstrapping angular 2

	index.html
	app/main.ts
	app/movie.component.ts

We need a bunch of sript tags to make everything work:

	<!-- IE required polyfills, in this exact order -->
  	<script src="https://cdnjs.cloudflare.com/ajax/libs/es6-shim/0.34.1/es6-shim.js"></script>
  	<script src="https://cdnjs.cloudflare.com/ajax/libs/systemjs/0.19.20/system-polyfills.js"></script>
  	<script src="https://npmcdn.com/angular2/es6/dev/src/testing/shims_for_IE.js"></script>

  	<!-- Angular polyfill required everywhere -->
  	<script src="https://code.angularjs.org/2.0.0-beta.8/angular2-polyfills.js"></script>

  	<script src="https://code.angularjs.org/tools/system.js"></script>
    <script src="https://code.angularjs.org/tools/typescript.js"></script>
  	<script src="https://code.angularjs.org/2.0.0-beta.8/Rx.js"></script>
  	<script src="https://code.angularjs.org/2.0.0-beta.8/angular2.dev.js"></script>
  	<script src="https://code.angularjs.org/2.0.0-beta.8/router.dev.js"></script>
  	<script src="https://code.angularjs.org/2.0.0-beta.8/http.dev.js"></script>

And calling an entry point

	<script>
      System.config({
        transpiler: 'typescript', 
        typescriptOptions: { emitDecoratorMetadata: true }, 
        packages: {
          'api': {defaultExtension: 'ts'}, 
          'app': {defaultExtension: 'ts'} 
        } 
      });
    System.import('app/main')
          .then(null, console.error.bind(console));
    </script>

System.import('app/main') means we are looking for a main.ts under /app


And lastly we need to call a component that is the entry point to our application
	 	

	<body>
  		<movie>Loading Demo ...</movie>
	</body>	

Let's look at main.ts more in detail

	 import { bootstrap } from 'angular2/platform/browser';
  	 import { MovieComponent } from './movie.component';

     bootstrap(MovieComponent)
       .then(success => console.log(`Bootstrap success`))
       .catch(error => console.log(error));
	
Importing bootstrap using the module loading from ES6

	 1) import { bootstrap } from 'angular2/platform/browser';
Loading our starting component

	 2) import { MovieComponent } from './movie.component';

And finally the bootstrapping itself

	3) bootstrap(MovieComponent)
       .then(success => console.log(`Bootstrap success`))
       .catch(error => console.log(error));
OK so at this point we have bootstrapped the app with a component, movie

	import { Component } from 'angular2/core';

	@Component({
	  selector: 'movie',
	  templateUrl: 'movie.html' 
	})
	export class MovieComponent {
	  movie:Movie = new Movie('Lord of the rings');
	  color = 'blue';
	  
	  hi(){
	    alert('hi');
	  }
	}

	// movie.html
    <h3>{{movie.getName()}}</h3>
    <h3 [innerText]="movie.getName()"></h3>
    <div [style.color]="color">{{movie.getName()}}</div>
    <div>
      <button (click)="hi()">Say hi</button>
    </div>
	  
	
Importing Component is needed to create any component, look at it as a directive in angular 1 for now.

	import { Component } from 'angular2/core';
Next thing to happen is that we annotate a class

	@annotation
	class SomeClass {

	}

And give it some extra properties. In this case angular gives component properties like what tagname, template etc..

	@Component({
	  selector: 'movie',
	  templateUrl: 'movie.html' 
	})
	export class MovieComponent {
	  movie:Movie = new Movie('Lord of the rings');
	  color = 'blue';
	  
	  hi(){
	    alert('hi');
	  }
	}

	// movie.html
    <h3>{{movie.getName()}}</h3>
    <h3 [innerText]="movie.getName()"></h3>
    <div [style.color]="color">{{movie.getName()}}</div>
    <div>
      <button (click)="hi()">Say hi</button>
    </div>
	  

Oh another piece of interest is the

	export class MovieComponent
This is ES6 way of creating something public in the file so that when you import this file as we do in main.ts it would get this definition

	import { MovieComponent } from './movie.component'


Angular 1 would say

	function Movie(name){
		this.name = name;
	}

	Movie.prototype.getName = function(){
		return this.name;
	}

	angular
		.module('app')
		.directive('movie', function(){
			template : '<h3>{{ movie.getName() }}</h3> <button ng-click="hi()"></button>',
			controller : function(){
				$scope.movie = new Movie('Top Gun');
				$scope.sayHi = function(){
					alert('hi')
				}
			}
		});

### Refactoring
We don't want an app that only bootstraps a movie so first thing we do is to change in the boostrapping in main.ts. Let's change it to this:

	import { bootstrap } from 'angular2/platform/browser';
    import { MainComponent } from './main.component';

    bootstrap(MainComponent)
    .then(success => console.log(`Bootstrap success`))
    .catch(error => console.log(error));
I.e we bootstrap a MainComponent instead. Let's see what that one looks like:

	import { Component } from 'angular2/core';
	import { MovieComponent } from './movie.component';
	
	@Component({
	  selector: 'main-component',
	  template: `
	    <movie></movie>
	  `,
	  directives : [ MovieComponent ]
	})
	export class MainComponent {
	 
	}
We see here that it points to a <movie> -tag in its template but that we also introduced a *directives* property on the component.. The reason for doing so is that if we want to use other components inside of your template you need you need to make angular aware of it by doing the two following steps:

		import { MovieComponent } from './movie.component';

		directives : [ MovieComponent ]

More on that in the next chapter.
### Summary of bootstrapping
So obviously if we want an entry point for an application we should maybe call it something like 

	<main-component></main-component> 
and not 

	<movie></movie>

We learnt anyway that calling bootstrap with our component in main.ts was what made everything work..

We learned about a concept called component which we will learn more about and that an annotation made it into a directive like construct

## Building a real app
Ok so we looked at basic bootstrapping and creating an app with one component in it..

### Built in directives
But what about all the nice built in directives from angular 1 like ng-repeat, ng-if, ng-show. They are still there right?

#### ng-repeat
This one turned into *ngFor with a little difference

You used to type

	<div ng-repeat="item in items"></div>
Now its

	<div *ngFor="#item in items"></div>
So no more dashes, we added a *\** char in the beginning and we added a *\#* to the item, and lastly we do *of* instead of in.
#### ng-if
This one turned into \*ngIf and works exactly the same as before.

#### ng-style, ng-src ng-href, ng-class
In Angular 2 these disappear and becomes

	<div [class] = "condition ? 'class1' : 'class2'" 
	<div [style.visibility] = "condition ? 'class1' : 'class2'"

	<img [src]="path"

	<a [href]="link"

#### Summary
40 + directives disappears
### binding

	- interpolation
	- one way binding
	- event binding
	- two way binding

#### interpolation
Angular 1 

	{{ vm.movie.title }}
Angular 2 

	{{ movie.title }}
We lost the the vm ( this )

#### One way binding
Angular 1

	<div ng-bind="prop"></div>
Angular 2

	<div [innerText]="prop"></div>

Other example

	<div [style.color]="color"></div>

Essentially whats within brackets is the element attribute and whats on the other side of = is from our component.
#### event binding
Angular 1

	<div ng-click="method()"></div>
	<div ng-blur="blur()"></div>
	<div ng-change="change()"></div>

Angular 2

	<div (click)="method()"></div>
	<div (blur)="blur()"></div>
	<div (change)="change()"></div>

#### two way binding
Angular 1

	<input ng-model="vm.movie.name" />

Angular 2

	<input [(ngModel)]="movie.name" />
Also called *banana in a box*

### What about factories, services, providers, constant??

The short answer is class. 

In angular 1 a service is created and consumed like so:

	angular
		.module('app')
		.service('productService', function(){
			this.getData = function(){
				return [];
			}
		})
	
	angular
		.module('app')
		.controller('productController', ProductController );

	ProductController.$inject = ['productService'];

	function ProductController(productService){
			this.products = productService.getData();
		}


And in Angular2 this becomes:

	import { Injectable } from 'angular2/core';

	@Injectable()
	export class ProductService {
		constructor() {

		}

		getData() {

		}
	}

And consume it like so:

	import { Component } from 'angular2/core';
	import { ProductService } from './products/product.service'
	
	@Component({
		selector : 'test',
		template : '',
		providers : [ ProductService ],
		
	})
	class SomeOtherService {
		private service:ProductService;

		constructor(service:ProductService) {
			this.service = service;	
			this.products = this.service.getData();	
		}
	}
So 2 things to keep track of **providers** property in the component and the injection point in the constructor.. As simple as that and we got a service to play with.
	
	providers : [ ProductService ]

	constructor(service:ProductService)

**GOTCHA** adding it to the providers tab made it available. However you don't want to put it in every providers property for every component. Because then it will be transient and not singleton. Put it at highest level component. Example:

	ParentComponent, providers [ Service ] // <= put it here

	ChildComponent // I will know about it
	ChildComponent // I will know about it
	ChildComponent // I will know about it



#### Name collisions - bye bye
In angular 1 we get a name collision if we define two or more productService because it is a global dictionary regardless of module it resides in..
Last defined service wins, so to speak.
 Angular 2 and modules solves this in an elegant way by the imports

	import { ProductService } as service 1 from './products/product.service'
	
	import { ProductService } as service 2 from './feature/product.service'

### Component in depth
Lets recap for a second. We have been playing with a component so far and learned to use certain properties:

	selector : 'some-name'
Here we type the name of the html element.

	template/tempalteUrl
Same function as in angular 1 We give it either a url to a tempalte or the template itself.

	directives : []
If this is a component using other child components we created we need to import those and specify them here.

	providers: []
Lastly providers is an array in which we list services that we mean to use that we can inject into our constructor.. Jus inject them in one place if you mean for them to be singletons and carry a state. Do it as a high up in the hiearchy as possible.

#### Bind data to component
Ok so far we have been talking about component as a kind of directive but being angular 1 devs we remember stuff such as isolated scopes, different type of bindings like @, &, =, <

What about those? 	

Well in angular 2 binding to data was easy for example 

	{{}} 
and 

	<input [value]="model.val" />

And we could also do double binding like so

	<input [(ngModel)]="model.val" />

And binding some data to a component is no different:

	// parent component
	@Component({
		template : '<movie-detail [movie]="movieFromComponent"></movie-detail>',
		directives : [ MovieDetail ]
	})
	export class Movies {
		movie:Movie;
	}
We just assign our movie prop like an html attribute with [] as binding type

	// actual movie detail
	import { Component,Input } from 'angular2/core';
	
	@Component({
		template : '{{ movie.name }}'
	})
	export class MovieDetail {
		@Input() movie:Movie;
	}
And in our movie-detail component we use @Input decorator to inject the data and then display it out on the view. Compare this to doing the following in angular 1

	.directive('movieDetail', function(){
		return {
			scope : {
				movie : '='
			}
		};
	})

So now we know how to bind data but what about callbacks, i.e & ?
In angular 1 we typed

	<comp callback="update(data)"

	.directive('comp', {
		scope : {
			callback : '&'
		},
 		controller : function($scope){
			$scope.click = function(){
				$scope.callback({ data : 'some data' })
			}
		}
	}) 

	.controller('ctrl', function($scope){
		$scope.update = function(data) {
			console.log("data from directive " + data);
		}
	})
In angular 2 we solve this with an output property. Consider the following code:

	<selected-movie 
        [movie]="selectedMovie"
        *ngIf="selectedMovie"
        (changed)="changed($event)" >
    </selected-movie>
I.e assume this component is placed in the template of a parent component that has
 
- selectedMovie
- changed - method declared in its class 


The important row here is 
	
	(changed) = changed($event)
If you DONT type in $event you **wont** be able to pass data from child component to parent component.

	import { Component,Input,Output, EventEmitter } from 'angular2/core';
	import { Movie } from './movie.model';

	@Component({
	    selector : 'selected-movie',
	    templateUrl : 'select-movie.html' 
	})
	export class MovieComponent {
	    @Output() changed:EventEmitter<Movie> = new EventEmitter<Movie>();
	    @Input() movie:Movie;
	    
	    constructor() {
	        
	    }
	    
	    hi() {
	        console.log('saying hi to parent');
	        this.changed.next(this.movie);
	        
	    }
	}

	// selected-movie.html
    <div style="border: solid 2px black; background:grey; padding: 10px; width: 50% ">
        <strong>Selected movie {{ movie.name }}</strong>
    </div>
    <button (click)="hi()" >Say hi to parent</button>
	    
Here the following row is of interest:

	import { Component,Input,Output, EventEmitter } from 'angular2/core';
We imported EventEmitter and Output

The next row of interest is the Output declaration

	@Output() changed:EventEmitter<Movie> = new EventEmitter<Movie>();

And lastly the invoking of the event

	hi() {
        console.log('saying hi to parent');
        this.changed.next(this.movie);
        //this.selected.emit(this.movie);
    }


This was the first part on Angular 2 just to get you started.. Hopefully you found that angular 2 doesn't bite and is even really nice.. Both Sublime and Visual Studio Code has excellent support for typescript.. So build a few angular 2 apps, feel the power and the speed and tune in for next part on routing and http coming really soon..

Look into https://github.com/softchris/angular2-simple to see a full functional angular 2 app demonstrating all mentioned concepts..