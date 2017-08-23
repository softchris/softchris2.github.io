---
layout: post
category : angular
tagline: "Components - input"
tags : [tutorial, kendo, javascript, angular 4]
---
{% include JB/setup %}

Reading time : 10 min 

# My story and the business case for Vendor controls
Kendo controls are built by the company Progress, for some of you known as Telerik. They are a program control vendor that has a long history of building controls for the web and thereby also a long history of making developers successful on the Web. 

My own experience started with Web Forms in .Net, which continued on with Kendo controls for ASP MVC. 
At that point in time we were on a time crunch, like most other IT projects. The customer basically wanted two things:
- A professional and consistent looking UI
- Advanced capabilities especially when it came to Grids such as Sorting, Grouping, Calculations and so on.

Now, when you have such a challenge in front of you, you need to deliver. If you got all the time in the world you might think I am a savvy developer that can create a beautiful looking UI and badass functionality to go with it. Time might not be on your side though and you might not be a front end ninja.

As a developer we all have spikes in different programming related things. We might be:
- Backenders with a little UI experience
- Frontenders, who dreams in CSS
- Jack of all trades, we can do a little of each layer but nothing with spectacular outcome, most importantly, we get the job done.

Regardless of what type you are, you deserve to succeed and trust me the customer really wants you too, as well.

We went with Kendo controls, for the project I mentioned. We were able to deliver and make the customer happy. Thats how you want most client engagements to end.

Back to present day. Nowadays I am an Expert on Nativescript and Angular. For me that means I am doing what I have always have done, which is to help people be successful by writing tutorials or doing public speaking or building repos on github. 

# Skinning your UI for Angular 4.x
As a front end developer you nowadays live in the best of times and the worst of times. There are more UI frameworks that are free than ever but JavaScript is outright scary for a front end beginner with all the tooling, not to mention CSS and Grids vs Flexbox or how to vertically center an image :). 

The thing with free options is that they are great til one or more core developers looses interest. There is no accountability when there is a bug, no service level agreement. 

Worth highlighting is that the free UI frameworks comes with a basic offering in terms of control functionality, number of controls and themes. You might have to pay up though for extensions of the above mentioned.

- Angular Material, this is a great library that is built on the principles of Material Design, but it is a work in progress, it may offer what you want, only you can tell
- Bootstrap is another great project but is only able to offer you so much in terms of functionality. There are a ton of themes created for it, some that cost money and some that are free, it could be a viable option.
- Kendo [Kendo UI for Angular](http://www.telerik.com/kendo-angular-ui). Then we have Kendo, a control package, offering different themes and a ton of controls with advanced functionality. As said before, main reason to go for this one is lack of time but also the fact that the controls do a lot of heavy lifting that might be boring or hard to code. If you are a company and the controls offer what you need, the cost for this is a no brainer.


This is part I of a series of posts on Kendo UI for Angular controls. In this post we will cover the different input controls:
- switch
- numeric textbox
- masked textbox
- slider

The official documention to these components can be found on [Kendo UI Inputs](http://www.telerik.com/kendo-angular-ui/components/inputs/)

# Input controls
There are many reasons why we would want input controls with a specific theme and behaviour. Having a nice theme will give your app that professional look and a nice extra behaviour that might save you a lot of time you would else have had to spend on developing it yourself. So the business case is definitely there, at least for a company.

## switch
A switch is just a a checkbox, which means it is either checked or unchecked. The markup for it looks like:

```
<kendo-switch
  [(ngModel)]="checkBox.checked"
  [disabled]="checkBox.disabled">
</kendo-switch>
```

There are two properties of interest:

- the checked state, a boolean that indicates wether it is checked or not
- disabled, wether or not it is disabled, this is good for when you don't want the user to change this value but you still want to represent it in a nice graphical way.

The backing code looks like:

```
export class Component {
    checkBox = {
        checked: true,
        disabled: false
    }
}
```

The default theme renders the control to look like the following:

![alt text](/assets/img/kendo/switch.png "switch")


## numeric textbox

A numeric textbox is about taking a normal textbox and filter it to only accept numbers and that the number can be incremented/decremented by using the arrows. It's a good control to have, if you don't have access to a keyboard for example and you want to help the user on what data type to fill in.

The markup for it looks like:

```
<kendo-numerictextbox
    [value]="numericalTextBox.value" 
    [min]="0" 
    [max]="100" 
    [autoCorrect]="numericalTextBox.autoCorrect">
</kendo-numerictextbox>
```

There are four properties of interest:
- value, this is the value it currently holds
- min, this is the min value that the value can possibly be
- max, this is the opposite of min and is the upper limit of the value
- autoCorrect, this will ensure to set the value to an allowed value

The backing code looks like:

```
export class Component {
  numericalTextBox = {
    autoCorrect : false,
    value : 5
  };
}
```

The default theme renders the control to look like the following:

![alt text](/assets/img/kendo/numerical.png "numerical")

## masked textbox
A masked textbox is a textbox where you guide the user. You visually guide the user by ensuring they only enter the numbers they are supposed to, while grouping the numbers in a more readable format. A win for both developer and user.

```
<kendo-maskedtextbox 
  [mask]="maskedTextBox.mask" 
  [value="maskedTextBox.value">
</kendo-maskedtextbox>
```

There are two properties of interest:
- mask, a defined pattern that controls what the input looks like
- value, the actual value

The backing code looks like:

```
export class Component {
    maskedTextBox = {
      value : "359884123321",
      mask : "(999) 000-00-00-00"
    };
}
```

The default theme renders the control to look like the following:

![alt text](/assets/img/kendo/masked.png "masked")


## slider
A slider is a very common control. The idea is to represent a value and convey what the min and max number is. Essentially you help the user in the sense that they don't have to use the keyboard. A slider usually has a horisontal and vertical orientation. It also helps with determining the step size, that is how much the value will increase for each step.

```
<kendo-slider
    [fixedTickWidth]="10"
    [vertical]="true"
    [min]="sliderVertical.min"
    [max]="sliderVertical.max"
    [smallStep]="sliderVertical.smallStep"
    [(ngModel)] = "sliderVertical.valueVertical"
></kendo-slider>
```

There are quite a few properties of interest:

- fixedTickWidth, the pixel width of the indicator
- vertical, this is a boolean property, its optional and defaults to horizontal if not set
- min, a numerical value representing the lower bound
- max, a numerical value representing the upper bound
- smallStep, how big the step size is when using the slider
- the value bound to [(ngModel)]

The backing code looks like:

```
export class Component {
  sliderVertical = {
    min: 0,
    max : 100,
    smallStep: 2,
    valueVertical: 40
  };
}

```

The default theme renders the control to look like the following:

![alt text](/assets/img/kendo/slider.png "slider")

# Summarising
We set out to talk about different controls for input. The switch is a very known concept from the mobile world where it is used instead of a checkbox.
The numeric textbox stops our user from entering anything they shouldn't. Any type of guidance on what and how to input values reassures the user. So is also the fact with the third component we covered, the masked textbox. This one is especially useful when inputting phone numbers, card numbers and other type of numbers expecially when the input is lengthy, a great visual guide. The last control we covered was the slider. A very popular control on settings pages but also on dashboards.

Hopefully you will take a crack at the demo repository ( url below ) and see for yourself if this can be of use for your current or future project. 

Hope you had a good read. Stay tuned for more articles on this topic.

# Demo

To see all these controls in action, have a look at the following repository [Kendo input](https://github.com/softchris/kendo-input)


