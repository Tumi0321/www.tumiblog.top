---
title: Vue 中 v-if 和 v-show 的区别
date: 2021-3-28
tags:
  - Vue 指令
categories:
  - Vue
---

## v-if



`v-if` 指令用于条件性地渲染一块内容。这块内容只会在指令的表达式返回 truthy 值的时候被渲染



`v-if` 是动态的向 DOM 树内添加或者删除 DOM 元素来控制元素的显隐，有更高的切换消耗



`v-if` 切换有一个局部编译/卸载的过程，切换过程中适当地销毁和重建内部的事件监听和子组件



`v-if` 是惰性的，如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块



`v-if` 可以和 `v-else` 、 `v-else-if` 一起使用，判断更复杂的条件



如果想切换多个元素，可以把一个 `<template>` 元素当做不可见的包裹元素，并在上面使用 `v-if`。最终的渲染结果将不包含 `<template>` 元素



## v-show



`v-show` 也是用于条件性地渲染一块内容



`v-show` 在任何条件下（不管首次条件是否为真）都会被编译，具有更高的初始渲染消耗



`v-show` 是通过切换 DOM 元素的 `display` 样式属性控制显隐，`block` 为显示，`none` 为隐藏



```
v-show` 不支持 `<template>` 元素，也不支持 `v-else
```



## 使用场景



[官方文档](https://cn.vuejs.org/v2/guide/conditional.html#v-if-vs-v-show)中有描述：



![2021/03/28/TOIMGf95e40328121829N.png](https://picturebed.tumiblog.top/2021/03/28/TOIMGf95e40328121829N.png)



`v-if` 判断是否加载，可以减轻服务器的压力，在需要时加载，但有更高的切换开销;`v-show` 改变 DOM 元素的 CSS 的 `dispaly` 属性，可以使客户端操作更加流畅，但有更高的初始渲染开销。如果需要非常频繁地切换，则使用 `v-show` 较好；如果在运行时条件很少改变，则使用 `v-if` 较好。



`v-else` 和 `<temlplate>` 的使用就需要根据使用场景自行判断了