---
title: 'Deep Dive into VueJS SFC & Scoped CSS'
date: 2021-01-17 18:44:22
tags: vuejs, scoped, css, style, vue-loader
---
# Single File Component (SFC) in VueJS

A majority of VueJS developers should be using .vue file in their day-to-day development. Built by VueJS core team as community plugin, Vue Loader is the de factor build tools which works behind-the-scene and transform the Single File Component (SFC) into format recognized by browser. Its popularity derives mostly from two major offerings by this plugin: 

Firstly, it provides more user-friendly version of writing component markup instead of built-in [template option](https://vuejs.org/v2/api/#template) or [render function](https://vuejs.org/v2/guide/render-function.html), though latter can also takes up format of JSX like React.

Secondly, it also supports Scoped CSS to avoid the painstaking nature of global CSS namespace. So components will not affect each other with their CSS selector. 

With these two distinctive features, you will be able to write logic (Javascript), markup (HTML) and style (CSS) in one single file. And today we will take a deep dive into Scoped SCSS:

# How does it work?

CSS is of global namespace by nature. So in large scaled application we certainly want to avoid name conflicts when components are authored by different developers. So how does Vue Loader manage to keep CSS rules limited to the component itself? 

In simple terms, when Vue Loader compiles each component, it will do a number of following things: 

1. When compiling \<template\> node, it will generate an unique id for each component and add to its HTML attribute like data-v-763db97b. 

2. When compiling \<style\> node, it will add this id to each CSS rule as attribute selector. So essentially if you have this simple CSS rule in .vue file

```CSS
.red {
    color:red
}
```

It will be transformed into 

```CSS
.red[data-v-763db97b] {
    color:red
}
```

Vue Loader allows developer to write plain CSS or use preproceser like SCSS/LESS in the style node. After adding the attribute selector, these style nodes will be handled over to other loaders like style-loader and scss-loaders for next action. Developers can also configure post-css action in Webpack like minification, adding vendor prefix etc. 

Let's go to some of the details for Scoped CSS: 

# How to override a child component

Sometime we use a 3rd party component as child component and we want to override certain style. You write an rule in the parent component but it will not take effect in child, because the two components are of the different data-v-id. In this case, we will have to use /deep/ selector provided by Vue Loader:

```CSS
/deep/ .child-component-class {
    color: red
}
```

With the deep indicator, Vue Loader will transform it into different selector order like:

```CSS
[data-v-763db97b] .child-component-class{
    color:red
}
```

Compared with previous example, the order between class selector and attribute selector are swapped with an extra space between them. So browser will apply this to every child elements with this class name, including grandchildren component. 

# @import directives

If you are going to use SCSS/LESS in style node, Vue Loader will not be able to understand your code dependence. Each component will create its own context during pre-processing compilation. So if you have two components calling @import directives to include a common file for dependence so that you can use variable, mixin or call [@extend](https://sass-lang.com/documentation/at-rules/extend), this file will be included into the final bundle for twice. 

```CSS
/*main.scss*/
.underline {
    text-decoration: underline;
}
```

```SCSS
@import './common/main.scss'
.text {
    @extend .underline; 
}
```

In the final bundle, .underline class will appear twice in company with different attribute id selector. To avoid these duplication, we can move some of the common shared CSS code back to global namespace. It will be almost impossible to make entire app CSS scoped. 

# Root Element of Child Component

Vue Loader will add parent id into the root element of child 

```HTML
//parent component
<div class="root"> 
    <div>Hello</div>
    <child-component />
</div>
//child component
<div class="root">
    <p>World</p>
</div>
```

If parent id is 123456 and child id is 654321, it will be complied to this: 

```HTML
<div class="root" data-v-123456> 
    <div data-v-123456>Hello</div>
    <div class="root" data-v-123456 data-v-654321>
        <p data-v-654321>World</p>
    </div>
</div>
```

So if you write an rule in parent component with .root selector, it will affect child component unintentionally. 

# Runtime Variable

This is not much related to SFC but we discuss this matter here as this is one of the most common requests. Imagine a service that allows its users to enter any hex value as color and change theme for personalization. How can we achieve that without overriding the entire CSS? 

SCSS and LESS do let developers define variables but these are always build time. User's color variable will be saved in server backend and has to be retrieved in runtime via Javascript code. 

So we can use [var function](https://developer.mozilla.org/en-US/docs/Web/CSS/var()) provided by browser. After calling API we will override global variable

```javascript
getColor().then((color)=>{
    var sheet = window.document.styleSheets[0];
    sheet.insertRule(`:root { color: ${color}; }`, sheet.cssRules.length);
})
```

# Conclusion

As above explanation, you will have a better comprehension of how Vue Loader Scoped CSS works and certain limitation with it. In the future we will talk about some alternatives scope CSS solutions like [CSS-in-JS](https://cssinjs.org/) or [CSS modules](https://css-tricks.com/css-modules-part-1-need/).

