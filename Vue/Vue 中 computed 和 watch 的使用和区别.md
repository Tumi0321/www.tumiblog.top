---
title: Vue 中 computed 和 watch 的使用和区别
date: 2021-3-29
tags:
  - Vue 基础
categories:
  - Vue
---

## computed



computed 是计算属性，它会根据你所依赖的数据动态显示新的计算结果，可以直接使用函数名当做属性引用



```js
var app = new Vue({
  el: "#app",
  data: {
    firstName: "Lebron",
    lastName: "James",
  },
  computed: {
    fullName: function () {
      return this.firstName + " " + this.lastName
    },
  },
  template: `
    <div>
      <h2>{{ fullName }}</h2>
    </div>`,
});
```



computed 是一个简写属性（完整有 `set` 和 `get` 两个方法）



```js
var vm = new Vue({
  data: { a: 1 },
  computed: {
    // 仅读取
    aDouble: function () {
      return this.a * 2
    },
    // 读取和设置
    aPlus: {
      get: function () {
        return this.a + 1
      },
      set: function (v) {
        this.a = v - 1
      }
    }
  }
})
vm.aPlus        // => 2
vm.aPlus = 3
vm.a            // => 2
vm.aDouble      // => 4
```



相比于 watch，计算属性拥有数据缓存机制（methods 也没有）



```js
var app = new Vue({
  el: '#app',
  data: { a: 1 },
  methods: {
    getA: function () {
      console.log('methods 调用多少次就会输出多少次');
      return this.a
    }
  },
  computed: {
    aPlus: function () {
      console.log('不管调用多少次 computeds 只会输出一次');
      return this.a + 1
    }
  }
})
```



在 Vue 的 template 模板内是可以写一些简单的 js 表达式的，但是在模版中放入太多声明式的逻辑会让模板本身过重，尤其当在页面中使用大量复杂的逻辑表达式处理数据时，会对页面的可维护性造成很大的影响，而 computed 的设计初衷也正是用于解决此类问题。



## watch



watch 是一个对象，键是 data 中要监听的数据，值是对应的回调函数，或者包含选项的对象。当依赖的 data 的数据变化，执行回调，在回调中会传入 `newVal` 和 `oldVal` 两个参数。Vue 实例将会在实例化时调用 `$watch()`，遍历 `watch` 对象的每一个属性。



```js
var app = new Vue({
  el: "#app",
  data: {
    firstName: "Foo",
    lastName: "Bar",
    fullName: "Foo Bar",
  },
  watch: {
    firstName: function (newVal, oldVal) {
      console.log("firstName 发生了变化：" + oldVal + "——>" + newVal);
      this.fullName = newVal + " " + this.lastName
    },
    lastName: function (newVal, oldVal) {
      console.log("lastName 发生了变化：" + oldVal + "——>" + newVal);
      this.fullName = this.firstName + " " + newVal
    },
  },
  template: `
    <div>
      <h2>{{ fullName }}</h2>
    </div>`,
});
```



监听复杂类型可以使用对象的方式，回调函数名必须为 `handler` 



如果不设置 `deep` 属性，将监听不到 `obj.a` 的改变，不过 `obj` 的属性依旧可以监听



```js
var app = new Vue({
  el: "#app",
  data: {
    obj: {
     a: "a" 
    }
  },
  watch: {
    obj: {
      handler: function (newVal, oldVal) {、
        console.log("obj 发生了变化");
        console.log(newVal);
        console.log(oldVal);
        deep: true, // 该属性设定在任何被侦听的对象的 property 改变时都要执行 handler 的回调，不论其被嵌套多深
      },
  },
});
```



打印的结果，发现 `oldVal` 和 `newVal` 值是一样的，因为它们的引用指向同一个对象/数组。Vue 不会保留变更之前值的副本，所以深度监听虽然可以监听到对象的变化，但是无法监听到具体对象里面那个属性的变化



可以直接对用 `对象.属性` 的方法监听单个属性的变化



```js
var app = new Vue({
  el: "#app",
  data: {
    obj: {
     a: "a" 
    }
  },
  watch: {
    obj: {
      handler: function (newVal, oldVal) {、
        console.log("obj 发生了变化");
        console.log(newVal);
        console.log(oldVal);
        deep: true,
      },
    "obj.a": {
      handler: function (newVal, oldVal) {
        console.log("obj.a 变了");
        console.log(newVal);
        console.log(oldVal);
      },
      immediate: true, // 该属性设定该回调将会在侦听开始之后被立即调用
    },
  },
});
```



**注意：** 不应该使用箭头函数来定义 watcher 函数，因为箭头函数没有 this，它的 this 会继承它的父级函数，但是它的父级函数是 window，导致箭头函数的 this 指向 window，而不是 Vue 实例



`vm.$watch()` 的用法和 `watch` 回调类似





```js
vm.$watch("n", function(newVal, oldVal){
  console.log("n 变了");
},{deep: true, immediate: true})
```



## 区别



计算属性：



- 支持缓存，只有依赖数据发生改变，才会重新进行计算
- 不支持异步，当 computed 内有异步操作时无效，无法监听数据的变化
- 默认深度依赖



侦听属性：



- 不支持缓存，数据变，直接会触发相应的操作
- watch 支持异步
- 默认浅度观测



## 参考文章



- [面试题： Vue中的 computed 和 watch的区别](https://juejin.cn/post/6844903807592169486)
- [官方 API 文档（vm.$watch）](https://cn.vuejs.org/v2/api/#vm-watch)
- [Vue 里的 computed 和 watch 的区别](https://zhuanlan.zhihu.com/p/99894379?from_voters_page=true)
- [Vue的computed和watch的细节全面分析](https://segmentfault.com/a/1190000012948175)