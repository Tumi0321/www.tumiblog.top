---
title: Object.defineProperty() 介绍及使用
date: 2021-3-27
tags:
  - JS 方法
categories:
  - JavaScript
---

> 转载修改自：https://blog.csdn.net/yexudengzhidao/article/details/100830878



对象是由多个键/值对组成的无序的集合。对象中每个属性可以是任意类型的值。



定义对象可以使用构造函数或字面量的形式：



```js
var obj = new Object;     // obj = {}
obj.name = "张三";        // 添加描述
obj.say = function(){};   // 添加行为
```



除了以上添加属性的方式，还可以使用 `Object.defineProperty` 定义新属性或修改原有的属性。



## Object.defineProperty()



**语法：**



```js
Object.defineProperty(obj, prop, descriptor)
```



**参数说明：**



- obj：必需。要定义属性的对象
- prop：必需。需定义或修改的属性的名称
- descriptor：必需。要定义或修改的属性描述符



**返回值：**



被传递给函数的对象，即第一个参数obj



## descriptor 参数



针对属性，我们可以给这个属性设置一些特性，比如是否只读不可以写；是否可以被 `for…in` 或`Object.keys()` 遍历。



对象里目前存在的属性描述符有两种主要形式：数据描述符和存取描述符。当修改或定义对象的某个属性的时候，给这个属性添加一些特性。



### 数据描述符



```js
var obj = {
  test:"hello"
}

// 对象已有的属性添加特性描述
Object.defineProperty(obj,"test",{
  configurable:true | false,
  enumerable:true | false,
  value:任意类型的值,
  writable:true | false
});

// 对象新添加的属性的特性描述
Object.defineProperty(obj,"newKey",{
  configurable:true | false,
  enumerable:true | false,
  value:任意类型的值,
  writable:true | false
});
```



数据描述中的属性都是可选的，来看一下设置每一个属性的作用。



- **value**



属性对应的值，可以使任意类型的值，默认为 undefined



```js
var obj = {}

// 第一种情况：不设置 value 属性
Object.defineProperty(obj, "newKey", {

})
console.log(obj.newKey)  // undefined

// 第二种情况：设置 value 属性
Object.defineProperty(obj, "newKey", {
  value: "hello"
})
console.log(obj.newKey)  // hello
```



- **writable**



属性的值是否可以被重写。设置为 `true` 可以被重写；设置为 `false`，不能被重写。默认为 `false`。



```js
var obj = {}

// 第一种情况：writable 设置为 false，不能重写
Object.defineProperty(obj, "newKey", {
  value: "hello",
  writable: false
})

// 更改 newKey的值
obj.newKey = "change value"
console.log(obj.newKey)  // hello

// 第二种情况：writable 设置为 true，可以重写
Object.defineProperty(obj, "newKey", {
  value: "hello",
  writable: true
})

// 更改newKey的值
obj.newKey = "change value"
console.log(obj.newKey)  // change value
```



- **enumerable**



此属性是否可以被枚举（使用 `for…in` 或 `Object.keys()`）。设置为 `true` 可以被枚举；设置为 `false`，不能被枚举。默认为 `false`。



```js
var obj = {}

// 第一种情况：enumerable 设置为 false，不能被枚举
Object.defineProperty(obj, "newKey", {
  value: "hello",
  writable: false,
  enumerable: false
})

// 枚举对象的属性
for (var attr in obj) {
  console.log(attr)  // console 无效
}

// 第二种情况：enumerable 设置为 true，可以被枚举
Object.defineProperty(obj, "newKey", {
  value: "hello",
  writable: false,
  enumerable: true
})

// 枚举对象的属性
for (var attr in obj) {
  console.log(attr)  // newKey
}
```



- **configurable**



是否可以删除目标属性或是否可以再次修改属性的特性。设置为 `true` 可以被删除或可以重新设置特性；设置为 `false`，不能被可以被删除或不可以重新设置特性。默认为 `false`。



目标属性是否可以使用 delete 删除：



```js
// -----------------测试目标属性是否能被删除------------------------
var obj = {}

// 第一种情况：configurable 设置为 false，不能被删除
Object.defineProperty(obj, "newKey", {
  value: "hello",
  configurable: false,
})

// 删除属性
delete obj.newKey
console.log(obj.newKey)  // hello

// 第二种情况：configurable 设置为 true，可以被删除
Object.defineProperty(obj, "newKey", {
  value: "hello",
  configurable: true,
})

delete obj.newKey
console.log(obj.newKey)  // undefined
```



目标属性是否可以再次设置特性：



```js
// -----------------测试目标属性特性是否能被改变------------------------
var obj = {}

// 第一种情况：configurable 设置为 false，属性特性不能被改变
Object.defineProperty(obj, "newKey", {
  value: "hello",
  writable: false,
  configurable: false,
})

// 报错：Cannot redefine property: newKey
Object.defineProperty(obj, "newKey", {
  value: "world",
  writable: true,
})

// 第二种情况：configurable 设置为 true，属性特性能被改变
Object.defineProperty(obj, "newKey", {
  value: "hello",
  writable: false,
  configurable: true,
})

Object.defineProperty(obj, "newKey", {
  value: "world",
  writable: true,
})

console.log(obj.newKey)       // world
obj.newKey = "change value";
console.log(obj.newKey)       // change value
```



提示：一旦使用 `Object.defineProperty` 给对象添加属性，那么如果不设置属性的特性，那么 `configurable`、`enumerable`、`writable` 这些值都为默认的 `false`



### 存取描述符



存取描述符是由 `getter` 函数和 `setter` 函数所描述的属性。一个描述符只能是这两者其中之一；不能同时是两者



```js
var obj = {}
var initValue = 'hello'

Object.defineProperty(obj, "newKey", {
  get: function () {
    console.log("触发 get 方法");
    return initValue
  },
  set: function (value) {
    console.log("触发 set 方法");
    initValue = value
  }
})

// 获取值
console.log(obj.newKey)

// 设置值
obj.newKey = 'change value'

console.log(obj.newKey)
```



打印顺序：



```text
触发 get 方法
hello
触发 set 方法
触发 get 方法
change value
```