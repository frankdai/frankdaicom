---
title: 'How to render a VueJS component via Constructor'
date: 2020-10-24 21:33:10
tags: vuejs, component, constructor, new
---

# Old-fashion Declarative Rendering

In VueJS, most of time if we want to render a component based on user interaction or certain events, we will do it in a declarative way by using v-if directive: 

```html
<parent>
    <button @click="showDialog = true">Click to show a dialog</button>
    <dialog v-if="showDialog" />
</parent>
```

Now let's make it a bit complex. We will call some API when user click this button. When such API failed, we will show the dialog component which has two buttons, cancel and retry, both of which we need to setup an event listener for user interaction. Then the code become:

```html
<parent>
    <button @click="onClick">Click to show a dialog</button>
    <dialog v-if="showDialog" @confirm="doRetry" @cancel="doCancel" />
</parent>
<script>
    export default {
        methods: {
            onClick ()=>{
                doSomeAPICall().catch(()=>this.showDialog =true)
            },
            doRetry ()=>{doSomeAPICall()},
            doCancel ()=>{}
        }
    }
</script>
```

# Issues

Though above code works perfectly, it can be very cumbersome especially when dialog component is being used in a lot of places. So how can we improve this? 

One solution actually lies in how Unit Testing tools work like [Mount API](https://vue-test-utils.vuejs.org/api/#mount) in Vue Test Utils. So we can actually do this

```javascript
doSomeAPICall().then(()=>
    mount(successDialog)
).catch(()=>
    mount(errorDialog)
)
```

# Vue.extend and the Constructor

Here is how to do it. We can import the Vue Component and use it in this way:

```javascript
import Dialog from './Dialog.vue'
let DialogConstructor = Vue.extend(Dialog)

doSomeAPICall().catch(()=>{
    let dialog = new DialogConstructor
    dialog.$mount()
    document.body.appendChild(dialog.$el) //you can append it anyway you like
})
```

Apparently there are certain advantages over this approach:

1. No more looking back to template for logic reference. Everything reside in the API promise callback.

2. The entire rendering process can be further encapsulated into function for code reuse, e.g you can wrap the cancel/retry into promise call with its own then/catch.

3. You can mount the dialog into everywhere you want instead of parent component. (Though you can also do it inside the component via [el option](https://vuejs.org/v2/api/#el))

A bit more digging into this constructor:

How to pass props to the component: 

```javascript
let dialog = new DialogConstructor({
    propsData: {
        propA: 'foo',
        propB: 'bar'
    }
})
```

How to listen to event from dialog component:

```javascript
dialog.$on('confirm', ()=>{ doSomething() })
```

You can even manipulate child component data directly, though it is not recommended since it can make code hard to read and debug:  

```javascript
dialog.dataA = 'foo'
```

# Conclusion 

Vue.extend API can return an constructor so that rendering a component entirely in JS code can be possible. It can be used in frequently used component for better code reuse. 