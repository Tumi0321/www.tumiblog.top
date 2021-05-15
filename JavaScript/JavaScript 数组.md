---
title: JavaScript 数组
date: 2021-2-26
tags:
  - JS 数组
  - JS 方法
categories:
  - JavaScript
---

## Array 类型



除了 Object 之外，Array 类型恐怕是 ECMAScript 中最常用的类型了。而且，ECMAScript 中的数组与其他多数语言中的数组有着相当大的区别。虽然 ECMAScript 数组与其他语言中的数组都是数据的有序列表，但与其他语言不同的是，ECMAScript 数组的每一项可以保存任何类型的数据。也就是说，可以用数组的第一个位置来保存字符串，用第二位置来保存数值，用第三个位置来保存对象，以此类推。而且，ECMAScript 数组的大小是可以动态调整的，即可以随着数据的添加自动增长以容纳新增数据。



## 创建数组



创建数组的基本方式有两种



- 使用  Array 构造函数 



```js
var colors = new Array(); // 空数组

var colors = new Array(20); // length 值为 20 的数组

var colors = new Array("red", "blue", "green"); // ["red", "blue", "green"]

// 也可以省略 new 关键字
var colors = Array(3);             // 创建一个包含3 项的数组
var names = Array("Greg");         // ["Greg"]
```



- 使用数组字面量表示法



```js
var colors = ["red", "blue", "green"]; // // ["red", "blue", "green"]
var names = [];                        // 空数组
```



*注：数组最多可以包含 4 294 967 295 个项，这几乎已经能够满足任何编程需求了。如 果想添加的项数超过这个上限值，就会发生异常。而创建一个初始大小与这个上限值 接近的数组，则可能会导致运行时间超长的脚本错误。



## 读取数组



在读取和设置数组的值时，要使用方括号并提供相应值的基于  0 的数字索引，如下所示：



```js
var colors = ["red", "blue", "green"];  // 定义一个字符串数组
alert(colors[0]);                       // 显示第一项
colors[2] = "black";                    // 修改第三项
colors[3] = "brown";                    // 新增第四项
```



这个例子中的 `colors[3]` 会自动增加到该索引值加 1 的长度（就这个例子而言，索引是 3，因此数组长度就是 4）



数组的项数保存在其 length 属性中，这个属性始终会返回 0 或更大的值，如下面这个例子所示： 



```js
var colors = ["red", "blue", "green"];     // 创建一个包含3 个字符串的数组
var names = [];                            // 创建一个空数组

alert(colors.length);            // 3
alert(names.length);             // 0 
```



数组的  length 属性它不是只读的。因此，通过设置这个属性，可以从数组的末尾移除项或向数组中添加新项。请看下面的例子：



```js
var colors = ["red", "blue", "green"];     // 创建一个包含3个字符串的数组
colors.length = 2;                // 删除了最后一项 
alert(colors[2]);                 //undefined

var colors = ["black", "white", "pink"];     // 创建一个包含3个字符串的数组
colors.length = 4;                // 新增了一项
alert(colors[3]);                 // undefined
```



利用  length 属性也可以方便地在数组末尾添加新项，如下所示：



```js
var colors = ["red", "blue", "green"];     // 创建一个包含3个字符串的数组
colors[colors.length] = "black";           //（在位置3）添加一种颜色
colors[colors.length] = "brown";           //（在位置4）再添加一种颜色
```



当把一个值放在超出当前数组大小的位置上时，数组就会重新计算其长度值，即长度值 等于最后一项的索引加 1，如下面的例子所示：



```js
var colors = ["red", "blue", "green"];     // 创建一个包含3 个字符串的数组
colors[99] = "black";                      //（在位置99）添加一种颜色
alert(colors.length); // 100
```



在这个例子中位置 3 到位置  98 实际上都是不存在的，所以访问它们都将返回  undefined



## 检测数组



对于一个网页， 或者一个全局作用域而言，使用  instanceof 操作符就能得到满意的结果：



```js
if (value instanceof Array){
  //对数组执行某些操作
}
```



instanceof 操作符的问题在于，它假定只有一个全局执行环境。如果网页中包含多个框架，那实 际上就存在两个以上不同的全局执行环境，从而存在两个以上不同版本的 Array 构造函数。如果你从一个框架向另一个框架传入一个数组，那么传入的数组与在第二个框架中原生创建的数组分别具有各自不同的构造函数



为了解决这个问题，ECMAScript 5 新增了 `Array.isArray()` 方法。这个方法的目的是最终确定某 个值到底是不是数组，而不管它是在哪个全局执行环境中创建的



```js
if (Array.isArray(value)){
  //对数组执行某些操作
} 
```



## 转换字符串



所有对象都具有 `toLocaleString()`、`toString()` 方法



调用数组的 `toString()` 方法和 `toLocaleString()` 方法会返回由数组中每个值的字符串形式拼接而成的一个以逗号分隔的字符串



```js
var colors = ["red", "blue", "green"];   // 创建一个包含3 个字符串的数组
console.log(colors.toString());          // red,blue,green
console.log(colors.toLocaleString());    // red,blue,green
```



使用 `join()` 方法可以通过不同的分隔符来构建这个字符串



```js
var colors = ["red", "green", "blue"];
console.log(colors.join(","));   //red,green,blue
console.log(colors.join("||"));  //red||green||blue
```



如果不给 `join()` 方法传入任何值，或者给它传入 undefined，则使用逗号作为分隔符。IE7 及更早版本会错误的使用字符串 "undefined" 作为分隔符



```js
var colors = ["red", "green", "blue"];
console.log(colors.join());            // red,green,blue
console.log(colors.join(undefined));   // red,green,blue
```



如果数组中的某一项的值是 `null` 或者 `undefined`，那么该值在 `join()`、`toLocaleString()`、`toString()`  方法返回的结果中以空字符串表示



## 末尾插入项（push）



`push()` 方法可以接收任意数量的参数，把它们逐个添加到数组末尾，并返回修改后数组的长度



```js
var colors = ["red"];
var count = colors.push("green", "blue");     // 推入两项
console.log(colors);                          // ["red", "green", "blue"]
console.log(count);                           // 2
```



## 末尾删除项（pop）



`pop()` 方法则从数组末尾移除最后一项，减少数组的 length 值，然后返回移除的项





```js
var colors = ["red", "green", "blue"];
var item = colors.pop();                     // 删除最后一项
console.log(item);                           // "blue"
console.log(colors.length);                  // 2
```



`arr.pop()` 的与 `arr.length--` 的效果一样，但 `arr.length--` 没有返回值



## 头部插入项（unshift）



`unshift()` 方法能在数组前端添加任意个项并返回新数组的长度



```js
var colors = ["red"];
var count = colors.unshift("green", "blue");    // 插入两项
console.log(colors);                            // ["red", "green", "blue"]
console.log(count);                             // 3
```



## 头部删除项（shift）



`shift()` 方法能够移除数组中的第一个项并返回该项，同时将数组长度减 1



```js
var colors = ["red", "green", "blue"];
var item = colors.shift();                   // 删除第一项
console.log(colors);                         // ["green", "blue"];
console.log(item);                           // "red"
```



## 升序排序（sort）



`sort()` 方法默认按升序排列数组项，`sort()` 方法会调用每个数组项的 `toString()` 转型方法，然后比较得到的字符串。即使数组中的每一项都是数值，`sort()` 方法比较的也是字符串，该方法会影响原数组



```js
var values = [0, 1, 5, 10, 15];
values.sort();
console.log(values);                 // 0,1,10,15,5
```



因此 `sort()` 方法可以接收一个比较函数作为参数，以便我们指定哪个值位于哪个值的前面。比较函数接收两个参数，并根据函数的返回值来决定元素的顺序。如果返回一个大于 0 的值，则元素会交换位置，如果返回一个小于 0 的值，则元素位置不变，如果返回一个等于 0 的值，也不交换位置。示例：



```js
function compare(value1, value2) {
  if (value1 < value2) {
    return -1;
  } else if (value1 > value2) {
    return 1;
  } else {
    return 0;
  }
}
var values = [0,1,10,15,5];
values.sort(compare);
console.log(values);      // 0,1,5,10,15
```



可以使用一个更简单的比较函数。这个函数只要用第二个值减第一个值即可。



```js
// 降序,如果是升序改变 return 值就行
function compare(value1, value2){
     return value2 - value1;
}
```



## 反转排序（reverse）



`reverse()` 方法会反转数组项的顺序，该方法会直接修改原数组



```js
var values = [1, 2, 3, 4, 5];
values.reverse();
console.log(values);  // 5,4,3,2,1
```



## 合并数组（concat）



`concat()` 方法可以基于当前数组中的所有项创建一个新数组。这个方法可以连接两个或多个数组，并将新的数组返回，该方法不会对原数组产生影响。示例：



```js
var colors = ["red", "green", "blue"];
var colors2 = colors.concat("yellow", ["black", "brown"]);
console.log(colors);        // ["red", "green", "blue"];
console.log(colors2);       // ["red", "green", "blue", "yellow", "black", "brown"]
```



`concat()` 方法不传入参数时可以用来复制数组



## 截取数组（slice）



`slice()` 方法可以接受一或两个参数，即要返回项的起始和结束位置。在只有一个参数的情况下，`slice()` 方法返回从该参数指定位置开始到当前数组末尾的所有项。如果有两个参数，该方法返回起始和结束位置之间的项，但不包括结束位置的项，该方法不会改变原数组。示例：



```js
var colors = ["red", "green", "blue", "yellow", "purple"]; 
var colors2 = colors.slice(1); 
var colors3 = colors.slice(1,4); 
console.log(colors2);   // ["green", "blue", "yellow", "purple"]
console.log(colors3);   // ["green", "blue", "yellow"]
```



参数可以传递一个负值，如果传递一个负值，则从后往前计算



```js
var colors = ["red", "green", "blue", "yellow", "purple"];
var colors2 = colors.slice(-2);
var colors3 = colors.slice(-2, 4);
console.log(colors2);   // ["yellow", "purple"]
console.log(colors3);   // ["yellow"]
```



## 添加/删除或替换项（splice）



`splice()` 方法可以用于删除数组中的指定元素，并向数组中添加新元素



- 第一个参数表示开始位置的索引，包含开始索引
- 第二个参数表示删除的数量
- 第三个参数及以后的参数，可以向数组中开始位置索引前边插入新元素



使用此方法会影响到原数组，并返回删除的元素组成的数组。示例：



```js
var colors = ["red", "green", "blue"];
var removed = colors.splice(0,1); // 删除第一项
console.log(colors);  // ["green", "blue"]
console.log(removed); // ["red"]，返回的数组中只包含一项

removed = colors.splice(1, 0, "yellow", "orange"); // 从位置 1 开始插入两项
console.log(colors);  // ["green", "yellow", "orange", "blue"]
console.log(removed); // 返回的是一个空数组

removed = colors.splice(1, 1, "red", "purple"); // 插入两项，删除一项
console.log(colors);  // ["green", "red", "purple", "orange", "blue"]
console.log(removed); // ["yellow"]，返回的数组中只包含一项
```



## 查找项（indexOf / lastIndexOf）



`indexOf()` 和 `lastIndexOf()`，这两个方法都接收两个参数：



- 第一个参数表示要查找的项
- 第二个参数表示开始查找的位置（可选的），包含当前索引



如果字符串中含有该内容，则会返回其第一次出现的索引，如果没有找到指定的内容，则返回 -1。不同的是 `indexOf()` 是从前往后找，而 `lastIndexOf` 是从后往前找。示例：



```js
var numbers = [1,2,3,4,5,4,3,2,1];
console.log(numbers.indexOf(4));                 // 3
console.log(numbers.lastIndexOf(4));             // 5
console.log(numbers.indexOf(4, 4));              // 5
console.log(numbers.lastIndexOf(4, 4));          // 3
```



## 迭代方法



ECMAScript 5 为数组定义了 5 个迭代方法。传入这些方法中的函数会接收三个参数：数组项的值、该项在数组中的位置和数组对象本身。



- every()：对数组中的每一项运行给定函数，如果该函数对每一项都返回 true，则返回 true
- some()：对数组中的每一项运行给定函数，如果该函数对任一项返回 true，则返回 true
- filter()：对数组中的每一项运行给定函数，返回该函数会返回 true 的项组成的数组
- forEach()：对数组中的每一项运行给定函数。这个方法没有返回值
- map()：对数组中的每一项运行给定函数，返回每次函数调用的结果组成的数组



```js
var numbers = [1,2,3,4,5,4,3,2,1];
var everyResult = numbers.every(function(item, index, array) {
  return (item > 2);
});
console.log(everyResult);    // false

var someResult = numbers.some(function(item, index, array) {
  return (item > 2);
});
console.log(someResult);     // true

var filterResult = numbers.filter(function(item, index, array) {
  return (item > 2);
});
console.log(filterResult);   // [3,4,5,4,3]

// 它只是对数组中的每一项运行传入的函数
numbers.forEach(function(item, index, array) {
  //执行某些操作
})

var mapResult = numbers.map(function(item, index, array) {
  return item * 2;
});
console.log(mapResult);      // [2,4,6,8,10,8,6,4,2]
```



注意：`every` 一个空数组也是返回 true



支持这些迭代方法的浏览器有 IE9+、Firefox 2+、Safari 3+、Opera 9.5+ 和 Chrome



## 归并方法



ECMAScript 5 还新增了两个归并数组的方法：`reduce()` 和 `reduceRight()`。这两个方法都会迭代数组的所有项，然后构建一个最终返回的值。其中，`reduce()` 方法从数组的第一项开始，逐个遍历到最后。而 `reduceRight()` 则从数组的最后一项开始，向前遍历到第一项。示例：



```js
var values = [1,2,3,4,5];
var sum = values.reduce(function(prev, cur, index, array){
  return prev + cur;
});
console.log(sum);      // 15

var sum = values.reduceRight(function(prev, cur, index, array){
  return prev + cur;
});
console.log(sum);      // 15
```



使用 `reduce()` 还是 `reduceRight()`，主要取决于要从哪头开始遍历数组。除此之外，它们完全相同



支持这两个归并函数的浏览器有 IE9+、Firefox 3+、Safari 4+、Opera 10.5 和 Chrome