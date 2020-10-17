---
title: 'VueJS: How to listen to child component lifecycle event'
date: 2020-06-15 19:22:09
tags: vuejs
---

VueJS is using similar structure like EventEmitter for child-to-parent communication with its [$emit](https://vuejs.org/v2/api/#vm-emit) API. When parent component wants to know certain [lifecycle](https://vuejs.org/images/lifecycle.png) events that child component is in, we can simply trigger an event in related hook:

```javascript

const Child = {
  mounted () {
    this.$emit('child-mounted')
  }
}

<parent @child-mounted="doSomething"><child /></parent>

```

With that callback, we can do numerous things like managing auto-focus behavior or getting child component layout sizing (width, height etc.).

However, if the child component is from 3rd party library which does not provide such callback, we can still tap into its lifecycle in this way:

```javascript
<parent @hook:mounted="doSomething">
  <child />
</parent>
```

Strangely, this is not listed in any of the official document. If you are interested, check the [source code](https://github.com/vuejs/vue/blob/dev/src/core/instance/lifecycle.js)

So literally it is just syntax-sugar and will trigger event in this special hook: namespace.

