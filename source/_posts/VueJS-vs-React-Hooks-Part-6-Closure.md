---
title: 'VueJS vs React Hooks: Part 6: Closure'
date: 2020-06-15 19:22:09
tags: vuejs, react
---

In every Javascript 101 class, [closure](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-closure-b2f0d2152b36) is always an indispensable part. I wonder how many beginners will stumble into this tiny piece of code:

```javascript
for (var i > 0; i < array.length; i++) {
  button.onclick = function(){alert(i)}
}
```

But we are not revisiting this intrigue yet important concept in Javascript today. In terms of closure in this article, we are referring the way we store a local variable in our components for later use, like registering a resize function and call removeEventListener for cleanup before component being destroyed.

In VueJS or React class-based component, this is easy like a breeze, since we always have this reference in component, which can be accessed in all lifecycle events like [mounted/beforeDestroy](https://vuejs.org/images/lifecycle.png) (VueJS) and [componentWillMount/componentWillUnmount](https://reactjs.org/docs/react-component.html) (React)

{% iframe https://gist.github.com/frankdai/d406db6be3a0241448fd2d792d5dd900.js %}

But in functional programming like React Hooks, we don’t have this and how can we achieve the same thing?

Let’s say we have a demo which show an input. Upon rendering, user has 30 seconds countdown before input changes to disabled status. Any typing will resolve the countdown.

For this, you might instinctively say, let’s just name a variable inside function body and see how it goes:

{% iframe https://gist.github.com/frankdai/fda1cdf0a1f964bf78296de6a0c96285.js %}


It looks good but actually it won’t work. The problem is that when user type anything, the entire function will re-run and timeout will be a brand new variable again with initial value of undefined. So this line will never run:

if (timeout) { doSomething() }

Here comes a special hook called useRef. In the very first part of this series we have already introduced this hook for accessing DOM element inside component. But this can also be used for closure purpose. Here is the explanation from [official doc](https://reactjs.org/docs/hooks-reference.html#useref):

> useRef returns a mutable ref object whose .current property is initialized to the passed argument (initialValue). The returned object will persist for the full lifetime of the component.

Here is the correct demo which utilize useRef for store the timeout variable

{% iframe https://codesandbox.io/embed/billowing-sea-9rrqf?fontsize=14&hidenavigation=1&theme=dark %}
