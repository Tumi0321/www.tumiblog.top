---
title: JavaScript 实现深浅拷贝
date: 2021-4-26
tags:
  - JS 原理
categories:
  - JavaScript
---

所谓深浅拷贝，浅拷贝的意思就是，你只是复制了对象数据的引用，并没有把内存里的值另外复制一份，那么深拷贝就是把值完整地复制一份新的值。



在之前的文章[《基本类型和引用类型的区别》](https://tumiblog.top/blogs/JavaScript/基本类型和引用类型的区别.html)中我有讲过，JS 中的数据类型分为两种，基本数据类型和引用数据类型，基本数据类型是保存在栈的数据结构中的,是按值访问，所以不存在深浅拷贝问题。



而比如对象，数组，函数，正则，时间对象这些都是引用数据类型，是保存在堆中的。所以，引用数据类型的复制，是内存地址的传递，并没有拷贝出一份新的数据。



区别：操作拷贝之后的数据不会影响到原数据的值，就是深拷贝，反之则为浅拷贝。



## 浅拷贝



浅拷贝只是复制基本类型的数据或者指向某个对象的指针，而不是复制整个对象，如果复制的是一个引用的话，修改该对象依旧会影响原对象



以下是实现浅拷贝的几种常见方法：



- **Object.assign()**



```js
var obj = { a: {a: "www", b: 39} };
var initalObj = Object.assign({}, obj);
initalObj.a.a = "yyy";
console.log(obj.a.a); // yyy
```



**注意**： 当 object 只有单层属性的时候，是深拷贝



- **Array.prototype.concat()**



```js
let arr = [1, 3, { username: 'www' }];
let arr2 = arr.concat();    
arr2[2].username = 'yyy';
console.log(arr[2].username); // yyy
```



- **Array.prototype.slice()**



```js
let arr = [1, 3, { username: 'www' }];
let arr3 = arr.slice();
arr3[2].username = 'yyy'
console.log(arr[2].username);  // yyy
```



## 深拷贝



深拷贝就完整复制数据的值（而非引用），目的在于避免拷贝后数据对原数据产生影响。



目前深拷贝的实现方法主要有递归复制，JSON 正反序列化：



- `JSON.parse()` 和 `JSON.stringify()`



```js
function deepCopy(origin) {
  return JSON.parse(JSON.stringify(arr));
}
```



- 递归复制



```js
function deepCopy(origin) {
  if (!origin && typeof origin !== 'object') {
    throw new Error('error arguments');
  }
  
  const targetObj = Array.isArray(obj) ? [] : {};
  
  for (let keys in origin) {
    if (origin.hasOwnProperty(keys)) {
      if (origin[keys] && typeof origin[keys] === "object") {
        // 如果是引用数据类型，会递归调用
        targetObj[key] = deepCopy(origin[key]);
      } else {
        // 如果不是，就直接赋值
        targetObj[keys] = origin[keys];
      }
    }
  }
  return targetObj;
}
```



## 参考文章



- [JS专题之深浅拷贝](https://www.imooc.com/article/277442?block_id=tuijian_wz)
- [手写JS之深浅拷贝](https://juejin.cn/post/6951242993978310687)