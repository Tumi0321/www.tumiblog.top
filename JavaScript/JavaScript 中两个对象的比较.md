---
title: JavaScript 中 {} == !{} 为什么是 true
date: 2021-5-11
tags:
  - JS 基础
categories:
  - JavaScript
---

::: tip

提示：以下会对函数或者逻辑操作符的规则进行较为详细的说明，如果只是为了理解标题，可以只阅读加粗部分，并不影响对文章之后内容的理解。

:::



## Boolean() 函数



在 ECMAScript 中，所有类型的值都能转换为布尔类型（Boolean）。要将一个值转换为其对应的 Boolean 值，可以使用转型函数 Boolean()。



Boolean() 转型遵循下列规则：



- **除 null 外任何对象都转换为 true，null 转换为 false**
- true 还是 true，false 还是 false
- 非 0 数字值转换为 1，0 转换为 false
- NaN 转换为 false
- 非空字符串转换为 true，空字符串转换为 false
- undefined 转换为 false



## 逻辑非



逻辑非操作符由一个叹号（!）表示，可以应用于 ECMAScript 中的任何值。无论这个值是什么数据类型，这个操作符都会返回一个布尔值。逻辑非操作符首先会将它的操作数转换为一个布尔值，然后再对其求反，**而转换布尔值的过程其实就是隐式调用 window 对象下的 Boolean 转型函数**，逻辑非遵循下列规则：



- **除 null 以外的对象返回 false， null 返回 true**
- 操作数是任意非 0 的数值返回 false，0 返回 true
- 操作数是 NaN，返回 true
- 操作数是一个非空字符串，返回 false，空字符串返回 true
- 操作数是 undefined，返回 true



*注：同时使用两个逻辑非操作符，实际上就会模拟 `Boolean()` 转型函数的行为。



明白了 Boolean 转型函数和逻辑非的规则后，就能知道 `![]` 和 `!{}` 都会转换为 `false`



## Number() 函数



Number() 函数可以把对象的值转换为数字。



*注：如果参数是 Date 对象，Number() 返回从 1970 年 1 月 1 日至今的毫秒数。



Number() 转换遵循下列规则：



- **true 转换成 1，false 转换成 0**
- 如果是纯数字的字符串，则直接将其转换为数字
- **如果字符串中有非数字的内容，则转换为 NaN**
- 如果字符串是一个空串或者是一个全是空格的字符串，则转换为 0
- 如果是一个带前缀的进制数字符串时（0x、0b、0o），则转换为十进制，如 Number('0x11')
- null 转换成 0
- undefined 转换成 NaN



## 相等操作符



相等操作符（==），不相等操作符（!=），这两个操作符都会先转换操作数（通常称为强制转型），然后再比较相等性。



转换不同类型时，遵循下列原则 ：



- **如果有一个操作数是布尔值，会隐式调用 window 对象上的 Number 转型函数，true 转换为 1，false 转换为 0 进行比较。**
- **如果一个是对象，另一个操作数不是，则调用对象的 valueOf() 方法，如果返回的是复杂数据类型，则在返回值得基础上再调用 toString() 方法，最后根据以上规则进行比较。**
- **一个是字符串，另一个是数值，会隐式调用 Number() 转型函数，将字符串转换为数值进行比较。**



两个操作符在进行比较时则要遵循以下原则：



- null 和 undefined 是相等的
- 要比较相等性之前，不能将 null 和 undefined 转换成其他任何值。
- 如果一个操作数是 NaN，相等操作符也返回 false，而不相等操作符返回 true。`重要提示：即使两个数都是 NaN，相等操作符也返回 fase，不相等返回 true`。
- 如果两个操作数都是对象，则比较它们是不是同一个对象。如果是返回 true。



## 解析



了解了以上规则后，当对 {} == !{} 运算时是如下步骤：



1. 隐式调用 Boolean 转型函数，对空对象转换成 Boolean 值，再对结果取反。此时比较 [] == false
2. 隐式调用 Number 转型函数，将 false 转换为数值 0，此时比较 [] == 0
3. 调用 valueOf 方法和 toString 方法，此时比较 '[object Object]' == 0
4. 隐式调用 Number 转型函数，此时比较 NaN == 0
5. 因此返回 false



## toString()



这边说一下关于 t`oString` 方法的题外话，在引用类型上调用 `toString` 方法，此时是通过继承 `Object.prototype` 得到的。此时返回值格式为字符串 `"[object Constructor]"`。`Constructor` 指的是构造函数。如果是对象，`Constructor = Object`；如果是数组，`Constructor = Array`。



```js
({}).toString()                                  // "[object Object]"
Object.prototype.toString.call([])               // "[object Array]"
Object.prototype.toString.call(function () {})   // '[object Function]'
Object.prototype.toString.call(new RegExp())     // "[object RegExp]"
Object.prototype.toString.call(new Date())       // "[object Date]"
```



观察上述代码会发现使用了 `Object.prototype.toString`，因为除 Object 类型外，其余引用类型的原型上都重写了 `toSring` 方法，覆盖了对象上的 `toString` 方法。



而在数组原型上的 `toString` 方法在不传递参数的情况下，会将数组以逗号的形式转换为字符串，实现与 `join()` 或者 `join(',')` 相同的效果



```js
[].toString               // '' 空字符串
["a","b","c"].toString()  // "a,b,c"
```



同样的，在函数、正则、日期的原型对象上同样存在 `toString` 方法，覆盖了 `Object.prototype` 上的 `toString` 方法，并且同数组一样，以字符串的形式输出



```js
(function func () {}).toString()       // "function func() {}"
(new RegExp('abc', 'g')).toString()    // "/abc/g"
(new Date()).toString()                // "Tue May 11 2021 20:19:46 GMT+0800 (中国标准时间)"
```



## 参考文章



- [{}==!{}为false是为什么呢？ - 凯斯的回答 - 知乎](https://www.zhihu.com/question/61745873/answer/190977765)
- [Number](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number)
- JavaScript 高级程序设计（第3版）