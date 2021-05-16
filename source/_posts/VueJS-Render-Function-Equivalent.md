---
title: 'VueJS render function equivalent to template'
date: 2021-05-15 18:44:22
tags: vuejs, render, template
---

# render() vs. template

All VueJS developers are familiar with the template option. But sometimes [render function](https://vuejs.org/v2/guide/render-function.html) comes more handy and flexible compared with template. However, not all the power build-in syntaxes offered by template are available in render function. So we will walk through some of the most common interpolate between the two ways of rendering UI in VueJS:

## .sync modifier

Similar to React, VueJS props flow in top-to-bottom direction, or [one-way data flow](https://www.geeksforgeeks.org/unidirectional-data-flow). You are not allowed to mutate props in child components directly. However VueJS does offer .sync modifier so that child component can emit event to let parent component to mutate the prop. 

```javascript
this.$emit('update:name', newName)

<parent>
    <child :name.sync="myName"></child>
</parent>
```

From [official document](https://vuejs.org/v2/guide/components-custom-events.html), we can easily know .sync modifier is just shorthand version of a combination of props (**name**) and event listener (**@update:name**). So we can adapt to our render function like this:

```javascript

createElement('div', {
  props: {                                  
    "name": this.myName
  },
  on: {                                 
    "update:name": ($event) => {
      this.myName = $event;
    }
  }
})

```

## v-model

Just like .sync modifier, v-model is another syntax sugar for to mimic the behavior of two-way data binding. Though most of the time developers use it for the input/select elements but it can also be applied to component level. It consists of two key things here: a prop named **'value'** and an event named **'input'**. So basically we can translate v-model into options object as following:

```javascript
return createElement(MyInput, { 
    props: { value: this.message }, 
    on: { input: (value) => { this.message = value } }
})
```

From this simple piece of code we can know that a component can also use v-model to pass a prop and allow its child to emit an **input** event with payload to mutate the prop named **value**. 

## .native modifier

If parent listens to a child component event, by default it expects the event should be emitted from child using $emit methods. 

```javascript
<child @click="onChildComponentClick" />
```

The onChildComponentClick callback will be triggered when child component emits an event named 'click'.

However you might think of a click event as in browser sense. To achieve this, you can use **@click.native** to tell VueJS you want to register a native event listener on the root element of child component. 

In official example, VueJS told us how to do this in render function as well:

```javascript
 on: {
    click: this.clickHandler
  },
  // For components only. Allows you to listen to
  // native events, rather than events emitted from
  // the component using `vm.$emit`.
  nativeOn: {
    click: this.nativeClickHandler
},
```

## Scoped Slots

Although in template we only use <slot> but in component instance, we have two properties: $slots and $scopedSlots. So if we want to pass data to scoped slots in render function, we need to call $scopedSlots instead:

```javascript
return createElement('div', [
    this.$scopedSlots.default({
        name: this.myName
    })
])
```

this.$scopedSlots.default is a function which accepts a single argument and passes it to its slot as data. 

## v-bind

When we have to pass a lot of props to child component, it is very annoying to list all of them in the template,  especially if there is an intermediate component whose sole purpose is to render some stateless UI and pass whatever the props from its parent to its children. 

In template, we can easily achieve that by utilizing v-bind directives:

```html
<child v-bind="options"></child>
```

With [ES6 object spread syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax), we can adapt this to render function with a single line of code:

```javascript
createElement('div', {
    props: {
        ...this.options
    }
})
```

# Conclusion

In fact, all the markup in template will be parsed into render function for generating Virtual DOM. If you have any input, you are more than welcomed to post your comment. 



