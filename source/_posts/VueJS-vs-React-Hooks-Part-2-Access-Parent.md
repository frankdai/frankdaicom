---
title: 'VueJS vs React Hooks: Part 2: Access Parent'
date: 2020-05-12 13:14:22
tags: vuejs
---

In functional programming terms, an UI component is just about input and output. The input is the data being passed to the component hierarchically down and output is HTML being rendered. But as every developer knows, requirements are constantly changing, causing never-ending flow of adding new props to our component.
So let say we have nested structure like

{% codeblock lang:javascript %}

<Main>
  <UserList>
     <UserListItem></UserListItem>
  </UserList>
</Main>

{% endcodeblock %}


Now in Main component, we need to add a new prop called type to control what to be displayed: first name, last name or full name. As UserListItem will govern the detail rendering of each user, we have to pass down the newly added prop through intermediate component. i.e. UserList which does not need this prop at all.

{% codeblock lang:javascript %}

function UserListItem(props) {
  return (props.type === 'first'?firstName:lastName)
}
<Main>
  <UserList type={type} >
     <UserListItem type={type}></UserListItem>
  </UserList>
</Main>

{% endcodeblock %}

Notice the type={type} will have to repeat again and again as long there are more components in the middle between Main and UserListItem. In large app we might have much deep nested structure then above example. Changing props and passing them all the way down is painstaking. To solve this, both React and VueJS offer ways to access parent context, immediately or not.
In React Hooks, we use [useContext](https://reactjs.org/docs/hooks-reference.html#usecontext) which is just syntax sugar for [React.createContext](https://reactjs.org/docs/context.html#reactcreatecontext) in class-style component:

{% iframe https://codesandbox.io/embed/bold-hamilton-uldfx?fontsize=14&hidenavigation=1&theme=dark %}

In VueJS, we have an (interestingly) same terminology called [Provide/Inject](https://vuejs.org/v2/api/#provide-inject) options:

{% iframe https://codesandbox.io/embed/boring-night-vmcbh?fontsize=14&hidenavigation=1&theme=dark%}

One common requirement from both React & VueJS is the context being provided at topmost level should be object instead of literal values.
As convenient it might looks like, we need to limit the usage of this pattern, as this might cause data flow difficult to understand and maintain. Use state management system and Redux or VueX if your app is sharing data between different components at many different components if necessary.
