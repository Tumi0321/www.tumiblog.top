---
title: 手写 apply、bind、call
date: 2021-4-26
tags:
  - JS 原理
categories:
  - JavaScript
---

## apply



```js
Function.prototype.myCall = function (context, args = []) {
  // 判断调用该方法的对象是否是函数
  if (typeof this !== "function") {
    throw new TypeError(`It's must be a function`);
  }
  // context 不存在，或者传递的是 null undefined，则默认为 window
  context = context || window
  // 指定唯一属性，防止 delete 删除错误
  const fn = Symbol(); 
  // this 是调用 myCall 的函数
  context[fn] = this; 
   // 调用 context 的 fn
  const result = context[fn](...args);
   // 删除掉 context 新增的属性
  delete context[fn];
   // 将结果返回
  return result;
};
```



## bind



`bind()` 会返回一个新函数



```js
Function.prototype.myBind = function (context, ...args) {
  const fn = this
  if (typeof fn !== 'function') {
    throw new TypeError('It must be a function')
  }
  context = context || window
  return function (...otherArgs) {
    return fn.apply(context, [...args, ...otherArgs])
  }
}
```



## call



`apply()` 第二个参数接收的是一个数组， `call()` 接收的是一个参数列表



```js
Function.prototype.myCall = function (context, ...args) {
  // 判断调用该方法的对象是否是函数
  if (typeof this !== "function") {
    throw new TypeError(`It's must be a function`);
  }
  // context 不存在，或者传递的是 null undefined，则默认为 window
  context = context || window
  // 指定唯一属性，防止 delete 删除错误
  const fn = Symbol(); 
  // this 是调用 myCall 的函数
  context[fn] = this; 
   // 调用 context 的 fn
  const result = context[fn](...args);
   // 删除掉 context 新增的属性
  delete context[fn];
   // 将结果返回
  return result;
};
```