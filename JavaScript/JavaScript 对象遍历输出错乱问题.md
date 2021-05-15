---
title: JavaScript 对象遍历输出错乱问题
date: 2021-5-14
tags:
  - JS 基础
categories:
  - JavaScript
---

::: tip

本文转载自https://www.cnblogs.com/everlose/p/12501222.html，并做了一些修改

:::



## 原因



当执行以下代码时，会发现输出的顺序与结构的顺序不一致



```js
let obj = {
  '3': "ccc",
  '2': "bbb",
  '1': "aaa",
};

for (const key in obj) {
  console.log(key)
}  

// output:
// 1 2 3
```



查阅 [ECMA-262 3rd edition](http://www.ecma-international.org/publications/files/ECMA-ST-ARCH/ECMA-262, 3rd edition, December 1999.pdf)，就说到 ES3 标准的对象是无序集合，也就是插入时是什么顺序，遍历时就是什么顺序



> An object is a member of the type Object. It is an unordered collection of properties each of which contains a primitive value, object, or function. A function stored in a property of an object is called a method.



而查阅 [ECMA-262 5.1 edition](https://262.ecma-international.org/5.1/#sec-4.2)，会发现对于对象的描述发生了变化



> an object is a member of the remaining built-in type Object; and a function is a callable object. A function that is associated with an object via a property is a method.



而 Chrome、Opera 等新版浏览器 JS 引擎遵循的是新版 ECMA-262 5th 规范，因此，遍历对象属性时遍历书序并非属性构建顺序。而 IE6、7、 8 的 JS 解析引擎遵循的是较老的 ECMA-262 3rd 规范，属性遍历顺序由属性构建的顺序决定。



而采用了新版规范的浏览器会遵循一个规律：



它们会先提取对象所有 key 的 `parseFloat` 值为非负整数的属性，然后根据数字顺序对属性排序首先遍历出来（省略后面的小数不比较），然后按照对象定义的顺序遍历余下的字符串键，最后遍历 Symbol 键，示例：



```js
let obj = {
  '1': '111',
  'b': 'bbb',
  'a': 'aaa',
  '2': '222'

}

for (const key in obj) {
  console.log(key)
}  
// output:
// 1 2 b a
```



上述代码中，浏览器首先会把 `'1'` 和 `'2'` 这种能被 `parseFloat` 转化为正整数的提到前面并且按照升序乱序，而 `'b'` 和 `'a'` 没法转为整数那就排在后面并按照构建时的顺序排序。



## 解决



数组是有序的，使用数组来进行遍历。或者可以将对象的 key 按照顺序存入数组中，遍历时通过数组来拿对象的值。



```js
var obj = {
  '1': "111",
  'b': "bbb",
  'a': "aaa",
  '2': "222"
}

var arr = ["1", "b", "a", "2"]

arr.forEach(function (el) {
  console.log(obj[el])
})
```



还可以通过 `Object.keys()` 提取所有的属性按照你想要的排序方法排序好之后再遍历读取出对象的属性值。



```js
var obj = {
  name: "coder",
  age: 1024,
  address: "segmentfault"
}

var objKeys = Object.keys(obj)
objKeys = objKeys.sort()  //这里写所需要的规则

objKeys.forEach(function (el) {
  console.log(obj[el])
})
```



## 补充



翻阅李兵老师的[图解 Google V8](https://time.geekbang.org/column/intro/296)的第三节 " V8 采用了哪些策略提升了对象属性的访问速度？有解释到 V8 里的对象维护着两个属性，会把数字放入线性的 elements 属性中，并按照顺序存放，非数字的属性放入 properties 中，不会排序（它可能是线性结构，取决于属性数量的多少）。遍历属性时先遍历 elements 而后在遍历 properties



![2021/05/15/TOIMG717250515064607N.jpg](https://picturebed.tumiblog.top/2021/05/15/TOIMG717250515064607N.jpg)