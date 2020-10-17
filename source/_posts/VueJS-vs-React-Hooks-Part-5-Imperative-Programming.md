---
title: 'VueJS vs React Hooks: Part 5: Imperative Programming'
date: 2020-06-15 09:11:33
tags: vuejs, react
---

In both VueJS & React world, [declarative programming](https://en.wikipedia.org/wiki/Declarative_programming) is preferable way for managing parent-child communication and interaction against [imperative programming](https://en.wikipedia.org/wiki/Imperative_programming).

Image we have a form, with an input and a submit button. Button is a child component which will be in disabled status unless input is filled in with an email address. So in declarative way, we will pass a prop to button like:

{% codeblock lang:html %}

<form>
  <input type="email" />
  <Button disabled="disabled">Submit</Button>
</form>

{% endcodeblock %}

In the form component, it does not care how children are implementing disable/enable. It only **declares** the status that button component should be in.

On contrary to this approach, comes imperative programming, which will specifically calling children’s API being exposed to its parent:

{% codeblock lang:javascript %}

if (isEmailValid) {
  button.setEnable(true) 
} else {
  button.setEnable(false)
}

{% endcodeblock %}

Above being said, both framework allows their users to calling sub components in imperative way. One reason is that some 3rd party libraries will expose APIs which components need to call imperatively.

# VueJS

{% codeblock lang:html %}

<Parent>
  <Child ref="button" />
</Parent>

{% endcodeblock %}

It provides [$refs](https://vuejs.org/v2/guide/components-edge-cases.html#Accessing-Child-Component-Instances-amp-Child-Elements) to both DOM and child components so that parent can access directly by call:

{% codeblock lang:javascript %}

this.$refs.button.setEnable()

{% endcodeblock %}

It even provides two special internal properties $parent and $children to refer to the parent and children components respectively. However this needs to be used with precautions as VueJS does not guarantee rendering order in children so if you write some code like

{% codeblock lang:javascript %}

this.$children[0].setEnable

{% endcodeblock %}

It will be likely to fail sometime later and it is difficult to debug.

# React Hooks

For React Hooks, it needs to use a built-in hooks called [useImperativeHandle](https://reactjs.org/docs/hooks-reference.html#useimperativehandle):

{% codeblock lang:javascript %}

const Button = forwardRef((props, ref) => {
  let [disabled, setDisabled] = useState(true)
  useImperativeHandle(ref, () => ({
    setEnable(flag) {
      setDisabled(!flag)
    }
  }));
  return <button disabled={disabled}>Hi</h1>;
});
const Parent = () => {
  const buttonRef = useRef();
   return (
    <form>
      <input type="email" onChange={buttonRef.current.setEnable()}/>
      <Button ref={buttonRef} />
    </form>
  );
};

{% endcodeblock %}

In combination with useRef API we introduced before (which can also be referred to both component and DOM element), we can also call child component methods in imperative way

At first glance, it might look like React is not intuitive against VueJS in this specific design. But React can designate which APIs to be exposed to parent instead of exposing entire child component instance. So to use imperative programming in VueJS way, it’s up to developer’s responsibility to make sure it is safe, reasonable and will not mess up child component internal state and model.
