---
title: 'VueJS vs React Hooks: Part 7: Render Child Component With Data'
date: 2020-12-13 14:14:25
tags: react, vuejs
---

In React or VueJS, we often author components that are considered as layout components, which sets the generic layout UI like header/sidebar/footer etc so that they can be reused in different places. Each time a layout component is called, it does not care about what to be inserted into its children. In VueJS, we have [Slots API](https://vuejs.org/v2/guide/components-slots.html) and in React we have [props.children](https://reactjs.org/docs/composition-vs-inheritance.html) to achieve that. 

However, in certain scenarios we want to pass some data from parent into its children, even the parent does not know what kind of component will be of its children, given the fact that it is a generic layout component. How can we do so? 

Let's make a demo: The parent component will start counting how many seconds you have stayed in this web page (yep, that's exactly what you saw in [reactjs.org](https://reactjs.org/)) and child component will consume this count and render it.

## VueJS

We will resort what is referred as [Scoped Slots](https://vuejs.org/v2/guide/components-slots.html#Scoped-Slots)

```javascript

let TimeOnPage = {
    template: `<div> 
                 <slot v-bind:second="second" />
               </div>`
    data () {
        return {second: 0}
    },
    mounted () {
        setInterval(()=>this.second++,  1000)
    }           
}

let Child = {
    template: `<time-on-page> 
                 <template v-slot="slotProps">
                   {{slotProps.second}}
                 </template>
               </time-on-page>`,
    components: {TimeOnPage}    
}

```

In the parent TimeOnPage we can use [v-bind](https://vuejs.org/v2/api/#v-bind) to bind an object to slot with structure like

```javascript
slotProps = {
    second: 0 //0 is reactive in child component this.second
}

```
 so in child components can access this object. It is a common practice to name this object to slotProps in child v-slot directive but you can actually name it to whatever you would like. 

## React Hooks

With React Hooks we can easily share state between components by using customized hooks:

```javascript
const useTimeOnPage = () => {
  const [second, setSecond] = useState(0);
  useEffect(() => {
    setTimeout(() => {
      setSecond(second + 1);
    }, 1000);
  }, [second]);
  return second;
}
const Demo = () => {
  const second = useTimeOnPage();
  return <div>second</div>
}
```

However this is not exactly same as our topic, since we want to keep the data in the parent component and share in its immediate children. Here we can use a technique that is often referred as [Render Props](https://reactjs.org/docs/render-props.html):

```javascript
const Child = (props) => {
  return <div>{props.second}s</div>;
};

const TimeOnPage = (props) => {
  const [second, setSecond] = useState(0);
  useEffect(() => {
    setTimeout(() => {
      setSecond(second + 1);
    }, 1000);
  }, [second]);
  return <div>{props.render(second)}</div>;
};

<TimeOnPage render={(second) => <Child second={second} />
```

In this example, TimeOnPage will accept a function as props (The prop name can be called anything but usually we will name it as ***render***) and pass its data as function arguments. The function will return a component which can access these arguments in this closure. 


