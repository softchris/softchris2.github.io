---
layout: post
category : angular
tagline: "Components - input"
tags : [tutorial, kendo, javascript, angular 4]
---
{% include JB/setup %}

Reading time : 10 min

Kendo controls are built by the company Progress, for some of you known as Telerik. They are a program control vendor that has a long history of building controls for the web. My own experience started with Web Forms in .Net, continued on with Kendo controls for ASP MVC, nowadays I am an Expert on Nativescript and Angular. For me that means I am doing what I have always have done which is to help people be successful by writing tutorials or doing public speaking or building repos on github. 

# Skinning your UI for Angular 4.x
There are many ways to give your Angular 4.x applications a nice look:

- Angular material [Angular material](https://material.angular.io/)

- Bootstrap, [Bootstrap](getbootstrap.com)
- Kendo [Kendo UI for Angular](http://www.telerik.com/kendo-angular-ui)

I'm sure there are more vendors and frameworks out there where you can use the power of CSS and Angualr 4.x together but here are some.

This is part I of a series of posts on Kendo UI for Angular controls. In this post we will cover the different input controls:
- switch
- numeric textbox
- masked textbox
- slider

The official documention to these components can be found on [Kendo UI Inputs](http://www.telerik.com/kendo-angular-ui/components/inputs/)

# Input controls
There are many reasons why we would want input controls with a specific theme and behaviour. Having a nice theme will give your app that professional look and a nice extra behaviour might save you a lot of time you would else have had to spend on developing it yourself. So the business case is definitely there, at least for a company.

## switch
A switch is just a a checkbox. The markup for it looks like:

```
<kendo-switch
  [(ngModel)]="checkBox.checked"
  [disabled]="checkBox.disabled">
</kendo-switch>
```

There are two properties of interest:

- the checked state, a boolean
- wether or not it is disabled

The backing code looks like:

```
export class Component {
    checkBox = {
        checked: true,
        disabled: false
    }
}
```

The control with the default theme renders to look like the following:

![alt text](/assets/img/kendo/switch.png "switch")


## numeric textbox

A numeric textbox is about taking a normal textbox. Ensuring it can only take numbers and that that number can be incremented/decremented by pushing on arrows. It's a good control to have if you don't have access to a keyboard for example.
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
- value
- min
- max
- autoCorrect

The backing code looks like:

```
export class Component {
  numericalTextBox = {
    autoCorrect : false,
    value : 5
  };
}
```

The control with the default theme renders to look like the following:

![alt text](/assets/img/kendo/numerical.png "numerical")

## masked textbox
A masked textbox is a textbox where you guide the user. You visually guide the user by ensuring they only enter the numbers they are supposed while grouping the numbers in a more readable format, a win for both developer and user.

```
<kendo-maskedtextbox 
  [mask]="maskedTextBox.mask" 
  [value="maskedTextBox.value">
</kendo-maskedtextbox>
```

There are two properties of interest:
- mask, a defined pattern thst controls what the input looks like
- value

The backing code looks like:

```
export class Component {
    maskedTextBox = {
      value : "359884123321",
      mask : "(999) 000-00-00-00"
    };
}
```

The control with the default theme renders to look like the following:

![alt text](/assets/img/kendo/masked.png "masked")


## slider
A slider is a very common control. The idea is to represent a value and convey what the min and max number is. Essentially you help the user in the sense that they don't have to use they keyboard. A slider usually has a horisontal and vertical orientation.

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
- min, a numerical value
- max, a numerical value
- smallStep, how big the step size is when sliding
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

The control with the default theme renders to look like the following:

![alt text](/assets/img/kendo/slider.png "slider")

# Demo

To see all these controls in action, have a look at the following repository [Kendo input](https://github.com/softchris/kendo-input)


