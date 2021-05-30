---
title: 'Why we need $set & $delete in VueJS 2.0'
date: 2021-05-30 13:11:41
tags: vuejs
---

In VueJS, data is reactive and mutatable -- You do not need to call API to change the data like ReactJS does:

```javascript
this.setState({count: 1})
```

Whereas in VueJS you just have to do it in a more natural way:

```javascript
this.count = 1
```

But this convenience comes at a price: To do this **magic** thing, VueJS has to intercept your setter/getter in object but due to some limitation of Javascript core, it can not detect changes in the following:

1. If you directly mutate array member, like array[1] = 'text'
2. If you do not declare the data at initialization phrase. That's why [data option should be a function returning object](https://v3.vuejs.org/guide/data-methods.html#data-properties)

To the first one, VueJS actually overrides some native array methods like push/pop/splice etc. Calling this methods will notify the watcher system so that UI will get update automatically. For the second one, however, it is possible that we are not able to know all the data available to our component at the time of initialization. To tackle this genuine scenario, VueJS offers set & delete. 

These two APIs are both offered at [global level](https://vuejs.org/v2/api/#Vue-set) and [instance level](https://vuejs.org/v2/api/#vm-set). You can go to official documentation for the usage. It is simple

```javascript
Vue.set(this, 'count', 1) //equivalent to this.count = 1
Vue.set(this.array, 1, 'text') //equivalent to this.array.splice(1, 1, 'text')
```

You can also check this Codepen for detail: https://codepen.io/frankdai/pen/VwppqVP Try to switch the JS code between the commented line and see the effect. 

Above being said, in VueJS 3.0, its reactivity system moves from [Object.defineProperty to Proxy](https://stackoverflow.com/questions/62358094/javascript-proxy-set-vs-defineproperty) as Javascript evolves with new bulit-in APIs. [So set and delete are dropped in 3.0](https://v3.vuejs.org/guide/migration/introduction.html#removed-apis). It is recommend update from 2.x given your application has not much technical debt or you are building a brand new VueJS app. 


