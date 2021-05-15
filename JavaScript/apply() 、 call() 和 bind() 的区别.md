---
title: apply() 、 call() 和 bind() 的区别
date: 2021-2-26
tags:
  - JS 方法
categories:
  - JavaScript
---

ECAMScript 3 给 Function 的原型定义了三个方法，`Function.prototype.call`、`Function.prototype.apply ` 和 `Function.prototype.bind()` 



它们的作用一样，都能改变 this 的指向并传入参数。区别就是 `call()` 方法接受的是参数列表，而 `apply()` 方法接受的是一个参数数组， `bind()` 方法接受的参数和 `call()` 一样，但是返回的是一个新函数



## 使用



- `apply()` 和 `call()` 第一个必选参数指定了函数体内 this 对象的指向



如果这个函数处于非严格模式下，则指定为 null 或 undefined 时会自动替换为指向全局对象



```js
// 传入 null
var func = function(a, b, c) {
  console.log(this); // window
};
func.apply(null);

// 传入 undefined
var func = function(a, b, c) {
  console.log(this); // window
};
func.apply(null);
```



如果在严格模式下，传入 null 则为 null，传入 undefined 则为 undefined



```js
// 传入 null
var func = function(a, b, c) {
  console.log(this); // null
};
func.apply(null);

// 传入 undefined
var func = function(a, b, c) {
  console.log(this); // undefined
};
func.apply(undefined);
```



- `apply()` 与 `call()` 不同的是第二个可选参数，这个参数将作为参数传给函数



`apply()` 接受的参数数组或类数组对象



```js
var func = function(a, b, c) {
  console.log(a, b, c); // 1 2 3
};
func.apply(null, [1, 2, 3]);
```



`call()` 接受的是参数列表



```js
var func = function(a, b, c) {
  console.log(a, b, c); // 1 2 3
};
func.call(null, 1, 2, 3);
```



- `bind()` 和 `call()` 用法一样，但返回一个原函数的拷贝，并拥有指定的 `this` 值和初始参数



```js
var func = function(a, b, c) {
  console.log(a, b, c);
};
var newFunc = func.bind(null, 1, 2, 3);
newFunc(); // 1 2 3
```



## 用途



- 改变 this 的指向



```js
var obj1 = {
  name: "sven",
};

var obj2 = {
  name: "anne",
};

window.name = "window";

var getName = function() {
  console.log(this.name);
};

getName(); // window
getName.call(obj1); // sven
getName.call(obj2); // anne
```



- 调用父构造函数，类似继承



```js
function Product(name, price) {
  this.name = name;
  this.price = price;
}

// category 属性是在各自的构造函数中定义的
function Food(name, price) {
  Product.call(this, name, price);
  this.category = 'food';
}

function Toy(name, price) {
  Product.call(this, name, price);
  this.category = 'toy';
}

// 使用 Food 和 Toy 构造函数创建的对象实例都会拥有在 Product 构造函数中添加的 name 属性和 price 属性
var cheese = new Food('feta', 5);
var fun = new Toy('robot', 40);
```



- 将数组各项添加到另一个数组



```js
var array = ['a', 'b'];
var elements = [0, 1, 2];
array.push.apply(array, elements);
console.info(array); // ["a", "b", 0, 1, 2]
```



- 对于一些需要写循环以便历数组各项的需求，我们可以用 `apply()` 完成以避免循环



```js
/* 找出数组中最大/小的数字 */
var numbers = [5, 6, 2, 3, 7];

var max = Math.max.apply(null, numbers); /* 基本等同于 Math.max(numbers[0], ...) 或 Math.max(5, 6, ..) */
var min = Math.min.apply(null, numbers);
```



## 参考文章



- [apply、call的区别和用途](http://www.yaya12.com/study/call-apply.html#_3、有时候我们使用-call-或者-apply-的目的不在于指-定-this-指向-而是另有用途-比如借用其他对象的方法。那么我们可以传入-null-来代替某个具体的对象)
- [Function.prototype.apply()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)
- [Function.prototype.call()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call)
- [Function.prototype.bind()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)
- [javascript中call()、apply()、bind()的用法终于理解](https://www.cnblogs.com/Shd-Study/p/6560808.html)