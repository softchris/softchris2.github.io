---
layout: post
category : angular
tagline: "angular 2 custom components"
tags : [tutorial, angular2, javascript, typescript]
---
{% include JB/setup %}

Before you read this article I suggest you have a look at 
[Angular 2 Basics](http://softchris.github.io/angular/2017/07/21/angular2-basics) first. It will tell you how to get started with your first project using @angular-cli and also some basics around components, modules and bindings.

Reading time 5 min.

In this post we will learn

- How to create a custom component
- How to work with inputs and ouputs
- Some nice extensions for VS Code which will make component writing a bit more fun

# Scaffolding a project
From the last article we know how to install @angular-cli. Let's scaffold a project to get us started

```
ng new <project>
```

After that we enter the newly created directory

```
cd <project>
```

and then we serve the application

```
ng serve
```

the application can be found at localhost:4200

# Create a component
let's create component let's call it hero.component.ts:

**hero.component.ts**
```
import { Component } from '@angular/core';

@Component({
    selector : 'hero',
    template : `
    My name is 
    {% raw %}
    {{name}}
    {% endraw %}
    `
})
export class HeroComponent{
  name:string;
  constructor() {
    this.name = 'hercules';  
  }
}
```
The Component decorator is imported from @angular/core and we pass it an object literal as it decorated our class. The object literal has bunch of properties we can give it but let's start with the ones we have given values, `selector` and `template`. 

The `selector` is the name of the component. When we later use it we use it like so:

```
<hero></hero>
```

The template lets us specify the HTML inline and not in a separate file, it is a good idea at this point to use the backtick `` as it will allow us to specify the template on several rows, which will increase readability.

A tip here is to install the extension Angular2-inline which will highlight the HTML code when you use the `template` over `templateUrl`. 

## Register with the module
So far so good, let us tell the module about this new component so we can use it:

**app.module.ts**

```
import { NgModule } from '@angular/core';
import { HeroComponent } from './hero.component';

@NgModule({
  declarations : [ AppComponent, HeroComponent ]
})
```

That's all we need to do to make the module happy. Now let us actually use this component

## Simple usage

**app.component.ts**

```
import { Component } from '@angular/core';

@Component({
  selector : 'app-root',
  template : `
    <hero></hero>
  ` 
})
export class AppComponent {

}
```

This should output:

```
My name is Hercules
```

# Working with input, using the Input decorator
There are two types of components when thinking about data. Container components and presentational components. The former is where the data originates from and it also contains actions to manipulate data. The latter are purely presentational components that we just pass data that is meant to be rendered in a nice way. We will have a look at the latter, presentational components.

We need to get our data from somewhere and we keep building on our existing component hero we need it to have an input. Which means the markup for said component will need to look something like this:

```
<hero [item]="hero"></hero>
```

As we put this component in the template for app.component.ts this is where the data must come from. So that file must look like this:

**app.component.ts**
```
import { Component } from '@angular/core';

@Component({
  selector : 'app-root',
  template : `
    <hero [item]="hero"></hero>
  ` 
})
export class AppComponent {
  hero:Hero = new Hero('Hercules');
}

class Hero {
  constructor(public name:string) {}
}
```

For this to be possible we also need to update the hero.component.ts file so that we can recive an input, so let's update it the following way:

**hero.component.ts**

```
import { Component } from '@angular/core';
import { Input } from '@angular/core';

@Component({
    selector : 'hero',
    template : `
    My name is 
    {% raw %}
    {{item.name}}
    {% endraw %}
    `
})
export class HeroComponent{
  @Input() item;
}
```

As you can see we cleaned up this component but we added two rows if importance. The first one being:

```
import { Input } from '@angular/core';
```
this imports the Input decorator. 
The second row of importance is where we define our property inside of the class:

```
@Input() item;
```
This ensures we properly map the html attribute to a field in our class. 


# Working with output, introducing the EventEmitter and the Output decorator

Ok, so we covered input into our component. What if we want to communicate back to the parent component our component resides in? We do that with the output decorator. Imagine the following parent component:

**app.component.ts**
```
import { Component } from '@angular/core';

@Component({
  selector : 'app-root',
  template : `
    <hero [item]="hero" (save)="notify()"></hero>
  ` 
})
export class AppComponent {
  hero:Hero = new Hero('Hercules');

  notify(){
    console.log('child invoked me')
  }
}
```

Note the additions we did to the markup:

```
<hero [item]="hero" (save)="notify()"></hero>
```

We added the save event, (save) and make it point to our notify method notify(). At this point we need to update our hero component and wire up the save event that now exist, let's do that:

**hero.component.ts**
```
import { Component, Output, Input, EventEmitter } from '@angular/core';

@Component({
    selector : 'hero',
    template : `
    My name is 
    {% raw %}
    {{item.name}}
    {% endraw %}
    <button (click)="save.emit()">Send message to parent</button>
    `
})
export class HeroComponent{
  @Input() item;
  @Output() save = new EventEmitter<any>();
}
```

Three things have been added here, the import:

```
import { Output, EventEmitter } from '@angular/core';
```

creating the save variable of type EventEmitter and decorating it with the @Output decorator:

```
@Output() save = new EventEmitter<any>();
```

and lastly let's wire up the HTML and a button that invokes save action:

```
<button (click)="save.emit()">Send message to parent</button>
```

Note especially that we invoke the .emit() method on the save field when the button is pushed. This will trigger the method on the parent component and output the following to the console:

```
'child invoked me'
```

## Send a parameter with the output
So far we have triggered a method, declared in the parent component, from the HeroComponent. We did that by using the @Output decorator and an EventEmitter. However there are more things we can do. We can send data from our HeroComponent back to our parent component. Let's update our parent component to the following:

**app.component.ts**
```
import { Component } from '@angular/core';

@Component({
  selector : 'app-root',
  template : `
    <hero [item]="hero" (save)="notify($event)"></hero>
  ` 
})
export class AppComponent {
  hero:Hero = new Hero('Hercules');

  notify(hero:Hero){
    console.log('child invoked me')
  }
}
``` 

What we did was to update the following row in the template:

```
<hero [item]="hero" (save)="notify($event)"></hero>
```

The notify method now takes an argument $event. We also updated the method signature for the notify method to take an argument:

```
notify(hero:Hero){
  console.log('child invoked me')
}
```

Let's now make some changes on the HeroComponent:

**hero.component.ts**
```
import { Component, Output, Input, EventEmitter } from '@angular/core';

@Component({
    selector : 'hero',
    template : `
    My name is 
    {% raw %}
    {{item.name}}
    {% endraw %}
    <button (click)="save.emit(hero)">Send message to parent</button>
    `
})
export class HeroComponent{
  @Input() item;
  @Output() save = new EventEmitter<any>();
}
```

We changed the following row:

```
<button (click)="save.emit(hero)">Send message to parent</button>
```

save.emit() now has an argument in it, the components hero field. When sent this way, through the emit method, the value sent will be bound to the $event on the parent component side. That's it thats all you need to know in most cases to work with input and output in Angular

# Add snippets extension, be lazy
We will do one thing before concluding this post. There is a snippet for VS Code we can install that will make writing components a less tedious activity. The extension is called 'Angular v4 TypeScript Snippets' and is written by John Papa. By installing this you will have access to over 30+ snippets. For components there are two snippets of interest:

- a-component
- a-component-root

Using a-component will give you the following component code for free:

```
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'selector-name',
  templateUrl: 'name.component.html'
})

export class NameComponent implements OnInit {
  constructor() { }

  ngOnInit() { }
}
```

Using a-component-root will give the following:

```
import { Component } from '@angular/core';

@Component({
  selector: 'prefix-app',
  template: `
    <router-outlet></router-outlet>`
})
export class AppComponent { }
```

The difference being the <router-outlet> tag added to the template, which we use to tell Angular where our routed content should be added.

Hope you enjoyed this post, stay tuned for posts.
