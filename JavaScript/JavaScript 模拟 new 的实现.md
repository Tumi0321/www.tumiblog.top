---
title: JavaScript 模拟 new 的实现
date: 2021-3-15
tags:
  - JS 原理
categories:
  - JavaScript
---

::: tip
[前端面试题——自己实现new](https://zhuanlan.zhihu.com/p/84605717)
:::



在红宝书中有提到过 new 操作符经历的四个步骤：



- 创建一个新对象
- 将构造函数的作用域赋给新对象（因此this就指向了这个新对象）
- 执行构造函数中的代码（为这个新对象添加属性）
-  返回新对象



由于无法模拟 JS 关键字，我们只能创建一个函数 `myNew` 来模拟



> myNew(constructor，[arguments])



这个函数当中，第一个参数是构造函数，第二个参数是构造函数中的参数



首先创建一个空对象：



```js
function myNew() {
    var obj = new Object(); 
}
```



第二步，将构造函数的作用域赋给新对象，就是给这个新对象构造原型链，链接到构造函数的原型对象上，从而新对象就可以访问构造函数中的属性和方法



构造函数就是传入的第一个参数，可以使用 `arguments` 来获取，由于`arguments` 是类数组，我们不能直接使用 `shift` 方法，我们可以使用 `call` 来调用 Array 原型上的 `shift` 方法来获取



```js
function myNew() {
  var obj = new Object();
  var constr = Array.prototype.shift.call(arguments);
}
```



之后，把 obj 的原型指向构造函数的原型对象上：



```js
function myNew() {
  var obj = new Object();
  var constr = Array.prototype.shift.call(arguments);
  obj.__proto__ = constr.prototype;
}
```



此时会发现，我们模拟 new，但是在代码当中又使用到了 new 这个关键字，可以使用 `Object.create()` 来代替创建对象，并且省略了原型指向步骤（不了解的可以阅读 《JavaScript 继承》中的“原型式继承”）



```js
function myNew() {
  var constr = Array.prototype.shift.call(arguments);
  var obj = Object.create(constr.prototype);
}
```



第三步，执行构造函数中的代码（为这个新对象添加属性）



```js
function myNew() {
  var constr = Array.prototype.shift.call(arguments);
  var obj = Object.create(constr.prototype);
  constr.apply(obj, arguments);
}
```



第四步，如果构造函数有返回值，则返回；否则，就默认返回新对象



首先，要获取这个返回值：



```js
function myNew() {
  var constr = Array.prototype.shift.call(arguments);
  var obj = Object.create(constr.prototype);
  var result = constr.apply(obj, arguments);
}
```





但是，new 这个关键字，并不是所有返回值都原封不动地返回的。如果返回的是 undefined，null 以及基本类型的时候，都会返回新的对象；而只有返回对象的时候，才会返回构造函数的返回值



因此，我们要判断 result 是不是 object 类型，如果是 object 类型，那么就返回 result，否则，返回 obj



```js
function myNew() {
  var constr = Array.prototype.shift.call(arguments);
  var obj = Object.create(constr.prototype);
  var result = constr.apply(obj, arguments);
  return result instanceof Object? result : obj;
}
```



测试：



```js
function Person(name) {
  this.name = name;
  this.sayName = function() {
    console.log(this.name)
  }
}

var person1 = new Person('Nicholas'); 
var person2 = myNew(Person, 'Nicholas');

console.log(person1.name) // Nicholas
console.log(person2.name) // Nicholas

person1.sayName(); // Nicholas
person2.sayName(); // Nicholas

console.log(person1.__proto__ === person2.__proto__);   // true
```