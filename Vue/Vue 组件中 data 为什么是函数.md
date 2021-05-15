---
title: Vue 组件中 data 为什么是函数
date: 2021-3-29
tags:
  - Vue 基础
categories:
  - Vue
---

官方文档有说明组件是可复用的 `Vue` 实例



![2021/03/29/TOIMGc86880329043109N.png](https://picturebed.tumiblog.top/2021/03/29/TOIMGc86880329043109N.png)



在创建 Vue 实例的时候会调用 `_init` 方法



![2021/03/29/TOIMGd4f360329043135N.png](https://picturebed.tumiblog.top/2021/03/29/TOIMGd4f360329043135N.png)



需要知道的是，注册组件的本质其实就是建立一个组件构造器的引用。使用组件才是真正创建一个组件实例





这个 `_init` 方法会将 `options` 传入，做一系列的初始化，包括对 `data` 的初始化，我们可以找到对应的处理方法



![2021/03/29/TOIMG0e44b0329043157N.png](https://picturebed.tumiblog.top/2021/03/29/TOIMG0e44b0329043157N.png)



如果 `data` 是对象时，返回的就是本身，如果 `data` 是一个函数时，将会调用 `getData(data, vm)`。 `getData` 内部会将 `data` 作为函数执行，并将返回的值重新赋值给 `data`



而我们知道 Object 是引用数据类型，进行赋值操作只是引用的赋值，修改属性会相互影响。而 JavaScript 中函数有函数作用域，返回的对象都是独立的不会相互影响



每次组件实例化时，并不是传入新的 options，而是同一份 options。例如平时经常写的单文件组件，对于同个组件，所有该组件的实例，在实例化时用的都是 `export {}` 的对象。



所以 Vue 组件 data 是一个函数可以保证组件相互的独立性，实现组件复用



![2021/03/29/TOIMGd26150329043226N.png](https://picturebed.tumiblog.top/2021/03/29/TOIMGd26150329043226N.png)



对于 `let app = new Vue({})` 实例化，传入的 `data` 只给一个实例使用，传对象或者函数都没问题



## 参考文章



- [data 必须是一个函数](https://cn.vuejs.org/v2/guide/components.html#data-必须是一个函数)
- [Vue 组件 data 为什么必须是个函数，而 Vue 的根实例却没有此限制？](https://www.zhihu.com/question/384454093)
- [Vue组件data选项为什么必须是个函数而Vue的根实例则没有此限制？](https://segmentfault.com/a/1190000021680253)