---
layout: post
category : angular
tagline: "routing + data fetching"
tags : [tutorial, angular2, javascript]
---
{% include JB/setup %}

# Angular 2 - routing + data

this is an old article that will be updated in a couple of days. This is based on the RC, a lot has been changed, check back in a couple of days.

This blogpost is based on you having read the first part [Angular 2 basics part I](http://softchris.github.io/angular/2016/03/06/angular2-basics)



## Bindings

	<img [src]="image.src"></div>

	<div [innerText]="title" ></div>

OR

	<div>
		{% raw %} 
		{{ title }}
		{% endraw %} 
	</div>

## Components
So we learned about how to create components, 

	@Component({
		selector : 'app',
		template : `
			<h1>
			{% raw %}
			{{ title }}
			{% endraw %}
			</h1>`
	})
	export class App {
		title = 'chris';
	}


how to place components in components
	
	@Component({
		selector : 'app',
		template : `
			<h1>
				{% raw %}
				{{ title }}
				{% endraw %}
			</h1>
			<other></other>
		`,
		directives : [ OtherComponent ]
	})
	export class App {
		title = 'chris';
	}

## Services + injection

how to create services, make them injectable 

	@Injectable()
	class Service {
		getData() {

		}
	}

	

and inject them

	@Component({
		selector : 'app',
		template : `
			<h1>
			{% raw %}
			{{ title }}
			{% endraw %}
			</h1>
			<other></other>
		`,
		providers : [ Service ]
	})
	export class App {

		constructor(service:Service) {
			this.service = service;
		}
		title = 'chris';
	}

We also learned about bootstrapping

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

And point out a starter main.ts that pointed out a MainComponent

	import { bootstrap } from 'angular2/platform/browser';
    import { MainComponent } from './main.component';
  
    bootstrap(MainComponent)
    .then(success => console.log(`Bootstrap success`))
    .catch(error => console.log(error));

And main component looking like this :

	@Component({
    	selector: 'main-component',
    	template: `
    	<menu></menu>
    	<movies></movies>
    	`,
    	directives : [ MoviesComponent, MenuComponent ]
    })
	export class MainComponent {
 
	}

BUT we can see we are pointing out 

	<movies></movies> 

when we want to point out something like

	<div ng-view></div> 

That we did in Angular 1. So how to do that in angular 2?




## More on bindings

	<button [attr.some-prop]="prop">ok</button>

If I want to bind to an attribute property that isnt supported by the element

	<div [class.selected]="selected">some text</div>

selected class is added if property selected is true

## Inputs
So this probably changed most from angular 1 to angular 2. Life was sweet, all was simple. This was our reality 

	<input ng-model="obj" />

Well in angular 2 there are two ways to deal with it.


1) local template variable

	<input #in />
	{% raw %}
	{{ in.value }}
	{% endraw %}

	<button (click)="save(in.value)"></button>


WAIT WHAT.. Ok so we reasoned that most inputs are done in a form context ending with us pressing a button. Sounds fair right?

So what we did was to create local template variable  \#in. In refers to the actual input and thus it knows what *in.value* is.

We then refer to it in our save call like 

	save( in.value )


So we lost our hash \#. 

However we don't see the interpolation binding happening updating, until we press save button
	
	{% raw %}
	{{ in.value }}
	{% endraw %}

So let's fix that

	<input #in (keyup)="0" />

And now it updates when it should..

2) update model on event

	<input type="text" 
             [value]="movie.name"
             (input)="movie.name=$event.target.value" />

So on the input event we assign movie.name its new value. This changes it for real just like a a banana in a box binding would.

3) Same ol double binding - *banana in a box*

	<input [(ngModel)]="prop" />

This one is obviously less verbose than 2)

## Pipes aka filters

Usage
 
	{% raw %}
	{{ movie.name | uppercase }}
	{% endraw %}

Built in  

	uppercase, lowercase, date:'medium', date : 'yMMMd', currency, number ,percent etc.

### Lets build a pipe

A pipe uses the annotation *pipe*
Let's build a simple pipe, a pipe that takes a text and makes it italic:

	import { Pipe, PipeTransform } from 'angular2/core';

	@Pipe({ name : 'cursive' })
	export class CursivePipe implements PipeTransform {
	    transform(value:string, args : any[]) {
	        return `<i>${ value }</i>`;
	    }
	}

And use would be

	import { CursivePipe } from './cursive.pipe';

	@Component({
		selector : 'movie',
		template : `<div [innerHTML]= "movie.description | cursive" ></div>`,
		pipes : [ CursivePipe ]
	})
	export class MovieComponent {
		
	}

Let's build one more, one that uses an argument

	import { Pipe, PipeTransform } from 'angular2/core';

	@Pipe({ name : 'power' })
	export class PowerPipe implements PipeTransform {
	    transform(value:string, args : any[]) {
	        let base:number = parseInt(value);
	        let exp = args.length > 0 ? parseInt( args[ 0 ] ) : 1;
	        
	        return Math.pow(base,exp);
	    }
	}	

And usage:

	import { PowerPipe } from './power.pipe';

	@Component({
		selector : 'calculator',
		template : `
		{% raw %}
		{{ item.number | power:2 }}
		{% endraw %}
		`,
		pipes : [ PowerPipe ]
	})
	export class MovieComponent {
		item:number = 10;
	}

Which will output 100 of course.

## lifecycle, init

Remember how you were always torn in angular 1 on how to handle initialization of a controller?  

wether to create an init method ? 

	angular.module('app').controller('ctrl', function(productService) {
		
		function init() {
			$scope.items = productService.getItems();
		}

		init();
	})

or active method

	angular.module('app').controller('ctrl', function(productService) {
		
		function activate() {
			$scope.items = productService.getItems();
		}

		activate();
	}) 

or put it in a resolve and inject it?	

	$routeProvider.when('/products', {
		resolve : {
			products : function(productService){
				return productService.getItems();
			}
		}
	})

	angular.module('app').controller('ctrl', function(products) {
		
		function activate() {
			$scope.items = products;
		}

		activate();
	}) 
	
Angular2 gives us a life cycle hook for this, its more of an interface..


	@Component({
		selector : 'test'
	})
	export class TestComponent implements OnInit {
		private service:ProductService;

		constructor( service:ProductService ) {
			this.service = service; 
		}

		ngOnInit() {
			this.items = service.getItems();
		}
	}

ngOnInit is automatically called after the constructor

## http

Ok so how do we do stuff like we knew how to do like fetching data from a route, like $http ?

We have been given two different flavours the known way with promises but also a new kid on the block Rxjs and Observers.  

### How to make it work

#### setup top most component

In your top most component, in my case main.component.ts import the following:

	import { HTTP_PROVIDERS } from 'angular2/http';

And inject it as a provider

	@Component({
	  selector: 'main-component',
	  template: `
	    <menu></menu>
	    <movies></movies>
	    <jedis></jedis>
	  `,
	  providers : [ 
	      HTTP_PROVIDERS, 
	      JediService 
	  ],
	  directives : [ 
	      MoviesComponent, 
	      MenuComponent, 
	      JedisComponent 
	  ]
	})
	export class MainComponent {
	 
	}		

Here

	providers : [ 
	      HTTP_PROVIDERS

#### Build a service that is injectable 

After that build a service that fetches data

	import { Injectable } from 'angular2/core';
	import { Http, Response } from 'angular2/http';
	import { Observable } from 'rxjs/Observable';
	import 'rxjs/add/observable/throw';
	import 'rxjs/add/operator/map';
	import 'rxjs/add/operator/catch';
	
	import { Jedi } from './jedi.model'; 
	
	
	@Injectable()
	export class JediService {
	    http:Http;
	    
	    constructor(http:Http) {
	        this.http = http;
	    }
	    
	    getJedis() {
	        return this.http.get('jedi.json')
	            .map((response: Response) => <Jedi> response.json())
	            .catch(this.handleError);
	            
	    }
	    
	    handleError(error:Response) {
	        return Observable.throw(error.status || "Server error");
	    }
	}

##### Imports

	import { Observable } from 'rxjs/Observable';
	import 'rxjs/add/observable/throw';
	import 'rxjs/add/operator/map';
	import 'rxjs/add/operator/catch';

I want to highlight this part because I spent some time getting them right

In the following version ( beta.3 ) 

	<script src="https://code.angularjs.org/2.0.0-beta.3/Rx.js"></script>

It was enough to just import import rxjs/Rx to get Observable, throw, map, catch which is great but in beta.8 that I am using 

	<script src="https://code.angularjs.org/2.0.0-beta.8/Rx.js"></script>

they made it harder hence the many imports on the top of this header..



Let's break it down

Make service injectable, so we can put as a provider

	import { Injectable } from 'angular2/core';

Let's focus on our fetch method for a second

	getJedis() {
	        return this.http.get('jedi.json')
	            .map((response: Response) => <Jedi> response.json())
	            .catch(this.handleError);
	            
	    }

We see here that we call *http* and we call our callback on success, but WAIT it's not called *.then()* its called *.map()*. And we use something cool like an arrow function =>

So instead of

	$http.get(url).then( function( response ) {

	})

We do

	http.get(url).map(function(response) {  })

Or in the more typescripty version we add an arrow function and get

	http.get(url).map( (response:Response) = > {  } )

But things can still go wrong, we still need to handle it ?

As you can see we are doing 

	return this.http.get('jedi.json')
	            .map((response: Response) => <Jedi> response.json())
	            .catch(this.handleError);

the .catch() handles if we get an error like not finding the url

We handle that by doing 

	handleError(error:Response) {
        return Observable.throw(error.status || "Server error");
    }

In essence we are rethrowing our error for the one calling getJedis() to handle

### So how to consume this service ?

#### Create consuming component

Let's create a component and of course start it all by importing our service like so :

	import { JediService } from './jedis.service';

And define a component

	@Component({
    selector : 'jedis',
    template : `
    <div style="border: solid 1px lightgray; padding: 10px; border-radius:5px">
        <div *ngFor="#jedi of jedis">
			{% raw %}	
            {{ jedi.name }}
			{% endraw %}
        </div>
		{% raw %}	
        {{ errorMessage }}
		{% endraw %}
    </div>
    `     
	})
	export class JedisComponent {
	    service:JediService;
	    jedis:Observable<Jedi[]>;
	    errorMessage:string
	    
	    constructor (service:JediService) {
	        this.service = service;
	        
	    }
	}	

#### Connecting fetched data to template

OK so we have a component injecting our service, then what ?

We actually have two ways to handle this

1)  subscribe to the results and assign it to a value in our class

2) use the async pipe and let the data resolve itself ( like angular-resource does for us in angular1 )


1) 

	constructor() {
		this.service.getJedis()
	            .subscribe(
	                jedis => this.jedis = jedis,
	                error => this.errorMessage = <any>error
	            )
	}

And in the template

	<div *ngFor="#jedi of jedis">
		{% raw %}
        {{ jedi.name }}
		{% endraw %}
    </div>

2) 

	this.jedis = service.getJedis();

And in the template

	<div *ngFor="#jedi of jedis | async">
		{% raw %}
        {{ jedi.name }}
		{% endraw %}
    </div>

NOTICE the difference **| async**

### Using good ol promises

	getJedisAsPromise () {
        return this.http.get('jedi.json')
            .map((response: Response) => <Jedi> response.json())
            .toPromise();     
    }

We can see here that after .map() we call toPromise() which turns it into a promise

In the component we can handle this in two ways, either :

	this.promiseJedis = service.getJedisAsPromise();

Which means our template looks like this

	<div *ngFor="#jedi of promiseJedis | async">
			{% raw %}
            {{ jedi.name }}
			{% endraw %}
    </div>

Or we do the classic .then() behaviour with

	this.promiseJedis = service.getJedisAsPromise()
		.then ( response => promiseJedis = response.data )
		catch( err => console.log(err) );

If doing this version then no async pipe

	<div *ngFor="#jedi of promiseJedis">
			{% raw %}
            {{ jedi.name }}
			{% endraw %}
    </div>


## Routing

No SPA would be complete without some form of routing

So we need the correct script

	<script src="https://code.angularjs.org/2.0.0-beta.8/router.dev.js"></script>

On index.html we need to set the base route

	<base href="/" />

Decorate a class with @RouteConfig([])

And start specifying routes like so

	@RouteConfig([
		{
			path : '/',
			name : 'Start',
			component : StartComponent,
			useAsDefault : true
		},
		{
			path : '/Products',
			name : 'Producs',
			component : ProductsComponent
		}
	])

To use the routing inside a component we add 

	ROUTER_DIRECTIVES  
	ROUTER_PROVIDERS 

and the element 

	<router-outlet></router-outlet> 

to the template.

	@Component({
		selector : 'some-component',
		template : `<router-outlet></router-outlet>`,
		directives : [ ROUTER_DIRECTIVES ],
		providers : [ ROUTER_PROVIDERS ]
	})

With the accompanying import

	import { RouteConfig, ROUTER_DIRECTIVES, ROUTER_PROVIDERS } from 'angular2/router';
	

router-outlet is like ng-view in angular 1 as you probably guessed.

Let's create links

	<a [routerLink]="['Start']">Start></a>
	<a [routerLink]="['Producs']">Products></a>

	<router-outlet></router-outlet>

So a complete example using all the above can look like this
	
	import { Component } from 'angular2/core';
	import { MoviesComponent } from './features/movies/movies.component';
	import { MenuComponent } from './features/shared/menu.component';
	import { JedisComponent } from './features/jedis/jedis.component';
	
	
	import { HTTP_PROVIDERS } from 'angular2/http';
	
	import { JediService } from './features/jedis/jedis.service';
	
	import { RouteConfig, ROUTER_DIRECTIVES, ROUTER_PROVIDERS } from 'angular2/router';
	
	@RouteConfig([
	    {
	        path : '/',
	        name : 'Movies',
	        component : MoviesComponent,
	        useAsDefault : true
	    },
	    {
	        path : '/Jedis',
	        name : 'Jedis',
	        component : JedisComponent
	    }
	])
	@Component({
	  selector: 'main-component',
	  template: `
	    <nav>
	        <a [routerLink]="['Movies']" >Movies</a>
	        <a [routerLink]="['Jedis']" >Jedis</a>
	    </nav>
	    <router-outlet></router-outlet>
	  `,
	  providers : [ 
	      HTTP_PROVIDERS,
	      JediService,
	      ROUTER_PROVIDERS
	  ],
	  directives : [ 
	      MoviesComponent, 
	      MenuComponent, 
	      JedisComponent,
	      ROUTER_DIRECTIVES 
	  ]
	})
	export class MainComponent {
	 
	}
	
### detail route

So far we set up the route to go to  Movies and Jedis but we are used to better routing than that like clicking an item in a list and get to a detailed item.

We remember doing that in angular 1 like so

	$routeProvider.when('/products/:id', {
		templateUrl : 'productdetail.html',
		controller : 'productDetailController',
		
	})	

And productDetailController injected $routeParams and we could do this

	angular
		.module('app')
		.controller('productDetailController' , function ($routeParams, service) {
			service.getById( $routeParams.id ).then(function (response) {
				$scope.product = response.data;
			})
		})

It's equally simple in angular 2

#### adding route to route dictionary

Add the following route

	{
			path : 'Jedis/:id',
			name : 'JediDetail',
			component : JediComponent
	}

So we now have 

	@RouteConfig([
	    {
	        path : '/',
	        name : 'Movies',
	        component : MoviesComponent,
	        useAsDefault : true
	    },
	    {
	        path : '/Jedis',
	        name : 'Jedis',
	        component : JedisComponent
	    },
 		{
			path : 'Jedis/:id',
			name : 'Jedi',
			component : JediComponent
		}
	])

#### create link on list page to detail page

First off lets go to the list page and create our detail link

	<div *ngFor="#jedi of jedis">
        <a href="" [routerLink] ="['Jedi', { id: jedi.id }]">
			{% raw %}
            {{ jedi.name }}
			{% endraw %}
        </a>
    </div>

We gave it a param, and because we are using [routerLink] structural directive we need to add it to directives in the component

Ok so now it can point out the JediComponent when we type Jedis/11 for example. But how to make it work in that component ?

#### retrieve correct record based on params

Then lets go to our detail page and retrieve our record and display it

1) we need fetch correct record

2) we need to render it in our template

3) create a back link to our list = adding ROUTER_DIRECTIVES


1) 

First import what we need, *RouteParams* for our parameter

	import { ROUTER_DIRECTIVES,RouteParams } from 'angular2/router';

The code needed to retrieve data from backend and filter the results
 
NOTE in a real system we should probably have a getJediByDetail() method that goes against a detail route. 

	constructor (params:RouteParams, service:JediService) {
        let id:Number = params.get("id");
        service.getJedis()
            .subscribe( (jedis:Jedi[]) =>{
                let jedi = <Jedi>jedis.find( j => j.id === id );
                this.jedi = new Jedi(jedi.name, jedi.allegiance, jedi.id);
            });
    }
	
2)

	<div *ngIf="jedi">
			{% raw %}
            {{ jedi.name }}
			{% endraw %}	
    </div>

3)

	<a href="" [routerLink]="['Jedis']">Back to list</a>

Our backlink only need to refer our existing record in our routing dictionary


So that is pretty much it. Congrats if you read so far.. 

You got more bindings, how to fetch data but also how to handle routing..

Next time I will be talking about how data can flow through an application using Observables..


Full code is available at :

[Angular 2 data + routing repo](https://github.com/softchris/angular2-routing-data)