---
title: 'VueJS vs React Hooks:Part4: Computation'
date: 2020-06-05 11:32:48
tags: vuejs, react
---

# What is Computation?

Computation is a technique we often used in programming to save the computed results of expensive function call, which usually depends on input values. Unless those input values changed, the computed result will stay in the memory unchanged, thus optimizing performance by saving from unless re-computation and re-rendering of UI. That’s why it is called as [Memorization](https://en.wikipedia.org/wiki/Memoization).

A very basic example is to suppose we have a user list and search box. We can add some computation field called displayUsers to calculate based on the user list, which usually hold the complete data you pull from data backend, against whatever keyword use types in search box.

# VueJS: Computed Property

VueJS offer [computed property](https://vuejs.org/v2/guide/computed.html) as a part of its class structure.

{% iframe https://codepen.io/frankdai/embed/dyoBQVq?height=265&theme-id=light&default-tab=js,result %}

Remember, the input value in computed property should be reactive so that VueJS can automatically add watcher and fire the computing functions as soon as any of the dependences change.

# React Hooks: useMemo

React offer [useMemo](https://reactjs.org/docs/hooks-reference.html#usememo) hooks to do such computation

{% iframe https://codesandbox.io/embed/fancy-violet-1bfv4?fontsize=14&hidenavigation=1&theme=dark %}

# Comparison

VueJS computed property is more intuitive: value B is based on value A with some functions. But it is inexplicit in its dependence. You have to look at the call for this.xxxx within function body.

On the other hand, React requires user to declare dependence in their useMemo hook. And since you have to pass state or prop into the dependence, it is unlikely to meet recursive problem.