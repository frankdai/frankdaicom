---
title: 'VueJS vs React Hooks:Part3: Props Validation'
date: 2020-05-28 09:44:10
tags: vuejs, react
---

Props are the key for components being reused in different places. It comprises of flags and parameters to mandate the component rendering into different results. Naturally checking of props is a must-have feature for component, especially for 3rd party UI library.

Normally we want to perform these kinds of check on props

1. Required props are not missing

2. Providing default values if optional props are not provided

3. Props type is expected

Let’s go through them one by one:

## Required & Optional Props

### VueJS

VueJS offer a built-in mechanism for checking required props

{% codeblock lang:javascript %}

props: {
  firstName: {
   required: false
  }, 
  lastName: {
   required: true
  }
}

{% endcodeblock %}


So when you call this component without lastName property, VueJS will throw an error. IDE like VS Code and WebStorm will also warn you.

### React

In React Hooks, things are even easier as component is just function:

{% codeblock lang:javascript %}

function renderName({firstName, lastName}) {
  if (!lastName) throw new Error('lastName required');
  return (<div>{'hello' + firstName + lastName}</div>)
}

{% endcodeblock %}

You can also use type-interfaced pre-process like Typescript to detect this even before runtime:

{% codeblock lang:javascript %}

function renderName(firstName?:string, lastName:string) {
  
}

{% endcodeblock %}

## Default Values

For optional props, providing default value can be handy for writing the component so data input can be expected. Let’s say I am have an user prop with data structure like

{% codeblock lang:javascript %}

{
  firstName: '', 
  lastName: ''
}

{% endcodeblock %}

So I can provide the user prop as a skeleton object representing this data structure. So instead of wring expression like

{% codeblock lang:javascript %}

if (this.user && this.user.firstName)

{% endcodeblock %}

We can safely to use this.user.firstName for the check.

It can be also very helpful in providing boolean type of prop. Say if the user name component has a flag called showFullName to control whether show first name or full name. If the component caller has not passed it, this flag will be undefined. Without default value, we can turn this flag into true when it is not there.

### VueJS

{% codeblock lang:javascript %}

props: {
  firstName: {
   default: 'John'
  }, 
  showFullName: {
   default: true
  }
}

{% endcodeblock %}

An important notation is that, similar to its data property, if the default value is an object (or array and function, as they are object in Javascript world) instead of literal values, you have to use a function to return an object. This is for [reactivity](https://vuejs.org/v2/guide/reactivity.html):

{% codeblock lang:javascript %}

props: {
  user: {
   default: ()=>{firstName: '', lastName: ''}
  }
}

{% endcodeblock %}

### React

As for React Hooks, you can process this in your function body in old fashion way:

{% codeblock lang:javascript %}

function renderName({firstName, lastName}) {
  firstName = firstName || 'John'
  lastName = lastName || 'Doe'
}

{% endcodeblock %}

Or just use ES6 or Typescript for more elegant approach

{% codeblock lang:javascript %}

function renderName(firstName = 'John', lastName = 'Doe') {
 
}

{% endcodeblock %}

But this can be tricky if you are using object as props to hold all the values. A detailed explanation [here](https://medium.com/@matanbobi/react-defaultprops-is-dying-whos-the-contender-443c19d9e7f1).

## Type Validation

Checking type of prop can be achieved a variety of ways in VueJS, as some built-in type are there with additional power of customized validator functions

{% codeblock lang:javascript %}

props: {
  firstName: {
   type: String
  }, 
  showFullName: {
   type: Boolean
  }
}

{% endcodeblock %}

As stated above, React Hooks functional component can do this kind of type check simply in function body or via ES6/Typescript. You can also use [props-type package](https://reactjs.org/docs/typechecking-with-proptypes.html) provided by React team which is available for both class-based and functional component in React.

## Conclusion

As indicated in above examples, functional programming is more intuitive than class-based component in terms of props type validation, as it is just Javascript expression without any learning curve at all. However, class-based examples are still handy as it provided default validators for most of your common needs.

