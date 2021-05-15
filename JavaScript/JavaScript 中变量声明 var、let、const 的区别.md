---
title: JavaScript 中变量声明 var、let、const 的区别
date: 2021-3-24
tags:
  - JS 基础
categories:
  - JavaScript
---

## 概述



JS 中作用域有全局作用域、函数作用域，没有块作用域的概念。ES6 中新增了块级作用域，块作用域由 `{}` 包括，if 语句和 for 语句里面的 `{}` 也属于块作用域



区别：



- var 有变量声明提前，声名的是函数作用域，可以重复声明
- let 没有变量声明提前，声名的是块作用域，不能重复声明
- const 没有变量声明提前，声名的是块作用域。声名时必须初始化，并且不能修改，如果声名的是引用类型，可以修改引用类型的属性。不能重复声明



## var



使用 var 关键字声明的变量会在所有代码执行之前被声明，但是未赋值（声明提前）



```js
console.log(a); // undefined
var a = 1;
console.log(a); // 1

// 相当于
var a;
console.log(a);
a = 1;
```



如果不初始化会输出 undefined，不会报错



定义变量后的值是可以修改的，也可以重复声明



```js
var a = 1;

a = 2;
console.log(a); // 2

var a = 3;
console.log(a); // 3
```



var 定义的变量是函数作用域，可以跨块访问，不能跨函数访问



```js
for (var i = 0; i <= 10; i++) {
  var sum = 0;
  sum += i;
}

console.log(sum); // 10

function test () {
  var a = 1;
  console.log(a);
}

test() // 1
console.log(a); // err: a is not defined
```



## let



let 关键字定义的变量没有声名提前，会报错



```js
console.log(a); // err: Cannot access 'a' before initialization
let a = 1;
```



let 不能重复声明，会报错



```js
let a = 1;
let a = 2;

err: Identifier 'a' has already been declared
```



let 定义的变量，只能在块作用域中访问，不能跨块访问，也不能跨函数访问



```js
{ var a = 1; }

console.log(a); // err: a is not defined

function test () {
  var b = 2;
  console.log(b);
}

test() // 2
console.log(b); // err: b is not defined
```



不过使用 let 关键字并不影响作用域链



```js
{
    let name = '张三'
  function fn () {
    console.log(name) 
  }
  
  fn() // 张三
}
```



## const



const 和 let 一样也没有声明提前



```js
console.log(a);
const a = 1;

err: Cannot access 'a' before initialization
```



const 用来定义常量，使用时必须初始化（即必须赋值）



```js
const a;
// err: Missing initializer in const declaration
```



const 一旦定义无法修改



```js
const b = 3;
b = 4;

// err: Assignment to constant variable.
```



const 和 let 一样也不能重新声明



```js
const a = 1;
const a = 2;

// err: Identifier 'a' has already been declared
```



const 定义的是块级作用域



```js
{ const b = 1 }

console.log(b); // err: b is not defined

function test () {
  const c = 2;
  console.log(c);
}

test() // 2
console.log(c); // err: c is not defined
```



但是当 const 定义的是一个引用对象时，这个引用对象的属性是可以修改的



```js
const person = {
  name : 'zs'
}

person.name = 'ls';
console.log(person.name); // ls
```



因为引用对象是存放在堆内存中的，person 保存的只是这个对象的引用（指针），当修改对象的属性时，并没有改变引用



注意，当不使用关键词定义的是全局变量



```js
{ a = 1; }
console.log(a); // 1

function test() {
  b = 2;
  console.log(b);
}

test(); // 2
console.log(b); // 2
```