---
title: 'VueJS vs React Hooks: Part 1: Access DOM'
date: 2020-05-30 15:34:56
tags: [vuejs, react]
categories: coding
---

Both VueJS and ReactJS recommend developers to use their framework imperatively, which means developers ought to take care of business logic purely and let the framework do the dirty work: updating, removing and inserting DOM element based on data in View Model side, hence the [MVVM](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/enterprise-application-patterns/mvvm) patterns.

However, there are certain scenarios where the developers must get his hands dirty and manipulate the DOM, like playing an audio/video element, managing focus and scrolling a long list etc. The tricky thing is that both frameworks are actually updating UI asynchronously, due to performance concerns and its internal mechanism for dependency collection. So in below code:

<!-- more -->

{% codeblock lang:javascript %}

let template = `<p>{{ someState }}</p>`

let state = 'A';

let element = document.querySelector('p');

state = 'B';

console.log(element.textContent);

{% endcodeblock %}

Unlike what you might think, the console.log will output ‘A’.

To simply put, when you try to mutate the state within the component, it will try to collect all the changes made to data model and call for update only once and it is done asynchronously. That’s why you will get ‘A’ in above example. To solve this, both frameworks provide API for developers to access DOM after UI actually got updated.

Here we will use a quick demo for grocery list for demonstration. You can type whatever you need in the text input and once you hit the add button, it will empty the input and add a new item to bottom of the list. After the list updated, it automatically re-focus on the input, making easier for users to continue inputing more items.

## VueJS

VueJS addresses this issue by providing [$nextTick](https://vuejs.org/v2/api/#vm-nextTick) API. You can either call the global Vue.$nextTick and component-level this.$nextTick.

{% codeblock lang:javascript %}

let state = 'A';
let element = document.querySelector('p');
state = 'B';
//callback style
this.$nextTick(()=>console.log(p.textContent))
//or you can use promise style
Vue.$nextTick().then(()=>console.log(p.textContent))

{% endcodeblock %}

{% iframe https://codesandbox.io/embed/stoic-khayyam-qt1kq?fontsize=14&hidenavigation=1&theme=dark %}

## React Hooks

ReactJS provide a functional API called [useEffect](https://reactjs.org/docs/hooks-effect.html). As the name suggests, it will be used to perform side-effect of data changing. It corresponds to class-component componentDidUpdate lifecycle callback. In addition, we also have [useRef](https://reactjs.org/docs/hooks-reference.html#useref) API, which pointing to specific DOM element inside the component.

{% codeblock lang:javascript %}

const inputEl = useRef(null);
useEffect(()=>{ 
    inputEl.current.focus();
})
<input ref={inputEl} />

{% endcodeblock %}

{% iframe https://codesandbox.io/s/recursing-chatterjee-fj3zq?fontsize=14&hidenavigation=1&theme=dark %}







