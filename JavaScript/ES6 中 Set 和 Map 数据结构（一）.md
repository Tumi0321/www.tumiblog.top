---
title: ES6 中 Set 和 Map 数据结构（一）
date: 2021-5-19
tags:
  - ES6
categories:
  - JavaScript
---

## Set



ES6 提供了新的数据结构 Set（集合）。它类似于数组，但成员的值都是唯一的，集合实现了 iterator 接口，所以可以使用『扩展运算符』和『for…of…』进行遍历。



### 基本使用



`Set` 本身是一个构造函数，用来生成 `Set` 数据结构。`Set` 函数可以接受一个数组（或者具有 iterable 接口的其他数据结构，参见《ES6 中的迭代器（Iterator）》）作为参数，用来初始化。因为成员是唯一的，所以会自动去重。



```JS
// 创建一个空集合 
let s = new Set()

// 创建一个非空集合
let s1 = new Set([1,2,3,1,2,3])

[...s1]  // [1,2,3]
```



以上代码可以证明 Set 结构不会添加重复的值。



### Set 中的特殊值



 Set 加入值的时候，不会发生类型转换，所以 `5` 和 `"5"` 是两个不同的值。Set 内部判断两个值是否不同，使用的算法叫做“Same-value-zero equality”，它类似于精确相等运算符（`===`），主要区别是精确相等运算符认为 `NaN` 不等于自身。



Set 对象存储的值总是唯一的，所以有几个特殊值需要特殊对待：



- +0 与 -0 在存储判断唯一性的时候是恒等的，所以不能重复
- undefined 与 undefined 是恒等的，所以不能重复
- NaN 与 NaN 是不恒等的，但是在 Set 中也只能存一个



### Set 的属性和方法



#### 实例属性



- `Set.prototype.constructor`：构造函数，默认就是 `Set` 函数。
- `Set.prototype.size`：返回 `Set` 实例的成员总数



#### 操作方法



- `Set.prototype.add(value)`：增加一个新成员，返回当前集合
- `Set.prototype.delete(value)`：删除元素，返回 boolean 值，表示删除是否成功
- `Set.prototype.has(value)`：返回 boolean 值，表示集合中是否包含某个元素
- `Set.prototype.clear()`：清除所有成员，没有返回值



```JS
// 创建一个 Set 实例
let s1 = new Set([1,2,3,1,2,3])

s1  // {1, 2, 3}

// 返回集合的元素个数 
s1.size  // 3

// 添加新元素
s1.add(4)  // {1, 2, 3, 4}

// 删除元素
s1.delete(1)  // true

// 检测是否存在某个值 
s1.has(2)     // true

// 清空集合
s1.clear()  // undefined
```



#### 遍历方法



Set 结构的实例有四个遍历方法，可以用于遍历成员。



- `Set.prototype.keys()`：返回键名的遍历器
- `Set.prototype.values()`：返回键值的遍历器
- `Set.prototype.entries()`：返回键值对的遍历器
- `Set.prototype.forEach()`：使用回调函数遍历每个成员



需要特别指出的是，`Set` 的遍历顺序就是插入顺序。这个特性有时非常有用，比如使用 Set 保存一个回调函数列表，调用时就能保证按照添加顺序调用，而普通对象遍历顺序是不确定的（参见《JavaScript 对象遍历输出错乱问题》）。



**（1）`keys()`，`values()`，`entries()`**



`keys` 方法、`values` 方法、`entries` 方法返回的都是遍历器对象（详见《ES6 中的迭代器（Iterator）》一章）。由于 Set 结构没有键名，只有键值（或者说键名和键值是同一个值），所以 `keys` 方法和 `values` 方法的行为完全一致。



```JS
let set = new Set(['red', 'green', 'blue']);

for (let item of set.keys()) {
  console.log(item);
}
// red
// green
// blue

for (let item of set.values()) {
  console.log(item);
}
// red
// green
// blue

for (let item of set.entries()) {
  console.log(item);
}
// ["red", "red"]
// ["green", "green"]
// ["blue", "blue"]
```



上面代码中，`entries` 方法返回的遍历器，同时包括键名和键值，所以每次输出一个数组，它的两个成员完全相等。



Set 结构的实例默认可遍历，它的默认遍历器生成函数就是它的 `values` 方法。



```JS
Set.prototype[Symbol.iterator] === Set.prototype.values  // true
```



这意味着，可以省略 `values` 方法，直接用 `for...of` 循环遍历 Set。



```JS
let set = new Set(['red', 'green', 'blue']);

for (let x of set) {
  console.log(x);
}
// red
// green
// blue
```



**（2）`forEach()`**



Set 结构的实例与数组一样，也拥有 `forEach` 方法，用于对每个成员执行某种操作，没有返回值。



```JS
let set = new Set([1, 4, 9]);
set.forEach((value, key) => console.log(key + ' : ' + value))
// 1 : 1
// 4 : 4
// 9 : 9
```



上面代码说明，`forEach` 方法的参数就是一个处理函数。该函数的参数与数组的 `forEach` 一致，依次为键值、键名、集合本身（上例省略了该参数）。这里需要注意，Set 结构的键名就是键值（两者是同一个值），因此第一个参数与第二个参数的值永远都是一样的。



另外，`forEach` 方法还可以有第二个参数，表示绑定处理函数内部的 `this` 对象。



### Set 应用场景



扩展运算符（`...`）内部使用 `for...of` 循环，所以也可以用于 Set 结构。



```JS
let set = new Set(['red', 'green', 'blue'])

[...set]  // ['red', 'green', 'blue']
```



扩展运算符和 Set 结构相结合，就可以去除数组的重复成员。



```JS
let arr = [1, 2, 3, 4, 5, 4, 3, 2, 1]

[...new Set(arr)]  // [1, 2, 3, 4, 5]
```



`Array.from` 方法可以将 Set 结构转为数组。



```JS
const items = new Set([1, 2, 3, 4, 5])
const array = Array.from(items)
```



这就提供了去除数组重复成员的另一种方法。



```JS
function dedupe (array) {
  return Array.from(new Set(array))
}

dedupe([1, 1, 2, 3]) // [1, 2, 3]
```



而且，数组的 `map` 和 `filter` 方法也可以间接用于 Set 了。



```JS
let set = new Set([1, 2, 3])
set = new Set([...set].map(x => x * 2))
// 返回Set结构：{2, 4, 6}

let set = new Set([1, 2, 3, 4, 5])
set = new Set([...set].filter(x => (x % 2) == 0))
// 返回Set结构：{2, 4}
```



因此使用 Set 可以很容易地实现并集（Union）、交集（Intersect）和差集（Difference）



```JS
let a = [1, 2, 3, 4, 5, 4, 3, 2, 1]
let b = [4, 5, 6, 5, 6]

// 1.并集
let union = [...new Set([...a, ...b])]  // [1, 2, 3, 4, 5, 6]

// 2.交集
let intersect = [...new Set(a)].filter(item => new Set(b).has(item))  // [4, 5]

// 3.(a 相对于 b 的）差集
let difference = [...new Set(a)].filter(item => !(new Set(b).has(item)))  // [1, 2, 3]
```



如果想在遍历操作中，同步改变原来的 Set 结构，目前没有直接的方法，但有两种变通方法。一种是利用原 Set 结构映射出一个新的结构，然后赋值给原来的 Set 结构；另一种是利用 `Array.from` 方法。



```JS
// 方法一
let set = new Set([1, 2, 3]);
set = new Set([...set].map(val => val * 2));
// set 的值是 2, 4, 6

// 方法二
let set = new Set([1, 2, 3]);
set = new Set(Array.from(set, val => val * 2));
// set 的值是 2, 4, 6
```



上面代码提供了两种方法，直接在遍历操作中改变原来的 Set 结构。



## WeakSet



### 含义



WeakSet 结构与 Set 类似，也是不重复的值的集合。但是，它与 Set 有两个区别。



首先，WeakSet 的成员只能是对象，而不能是其他类型的值。



```JS
const ws = new WeakSet();

ws.add(1)
// TypeError: Invalid value used in weak set

ws.add(Symbol())
// TypeError: invalid value used in weak set
```



上面代码试图向 WeakSet 添加一个数值和 `Symbol` 值，结果报错，因为 WeakSet 只能放置对象。



其次，WeakSet 中的对象都是弱引用，即垃圾回收机制不考虑 WeakSet 对该对象的引用，也就是说，如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于 WeakSet 之中。



这是因为垃圾回收机制根据对象的可达性（reachability）来判断回收，如果对象还能被访问到，垃圾回收机制就不会释放这块内存。结束使用该值之后，有时会忘记取消引用，导致内存无法释放，进而可能会引发内存泄漏。WeakSet 里面的引用，都不计入垃圾回收机制，所以就不存在这个问题。因此，WeakSet 适合临时存放一组对象，以及存放跟对象绑定的信息。只要这些对象在外部消失，它在 WeakSet 里面的引用就会自动消失。



由于上面这个特点，WeakSet 的成员是不适合引用的，因为它会随时消失。另外，由于 WeakSet 内部有多少个成员，取决于垃圾回收机制有没有运行，运行前后很可能成员个数是不一样的，而垃圾回收机制何时运行是不可预测的，因此 ES6 规定 WeakSet 不可遍历。



这些特点同样适用于本章后面要介绍的 WeakMap 结构。



### 语法



WeakSet 是一个构造函数，可以使用 `new` 命令，创建 WeakSet 数据结构。



```JS
const ws = new WeakSet();
```



作为构造函数，WeakSet 可以接受一个具有 Iterable 接口的对象作为参数。该对象的所有成员，都会自动成为 WeakSet 实例对象的成员。



```JS
const a = [[1, 2], [3, 4]];
const ws = new WeakSet(a);
// WeakSet {[1, 2], [3, 4]}
```



上面代码中，`a` 是一个数组，它有两个成员，也都是数组。将 `a` 作为 WeakSet 构造函数的参数，`a` 的成员会自动成为 WeakSet 的成员。



注意，是 `a` 数组的成员成为 WeakSet 的成员，而不是 `a` 数组本身。这意味着，数组的成员只能是对象。



```JS
const b = [3, 4];
const ws = new WeakSet(b);
// Uncaught TypeError: Invalid value used in weak set(…)
```



上面代码中，数组 `b` 的成员不是对象，加入 WeakSet 就会报错。



### 方法



WeakSet 结构有以下三个方法：



- `WeakSet.prototype.add(value`)：向 WeakSet 实例添加一个新成员。
- `WeakSet.prototype.delete(value)`：清除 WeakSet 实例的指定成员。
- `WeakSet.prototype.has(value)`：返回一个布尔值，表示某个值是否在 WeakSet 实例之中。



下面是一个例子



```JS
const ws = new WeakSet();
const obj = {};
const foo = {};
ws.add(window);
ws.add(obj);
ws.has(window); // true
ws.has(foo);    // false
ws.delete(window);
ws.has(window);    // false
```



WeakSet 不能遍历，所以没有 `size` 属性。



```JS
ws.size // undefined
ws.forEach // undefined
ws.forEach(function(item){ console.log('WeakSet has ' + item)})
// TypeError: undefined is not a function
```



上面代码试图获取 `size` 和 `forEach` 属性，结果都不能成功。



WeakSet 不能遍历，是因为成员都是弱引用，随时可能消失，遍历机制无法保证成员的存在，很可能刚刚遍历结束，成员就取不到了。WeakSet 的一个用处，是储存 DOM 节点，而不用担心这些节点从文档移除时，会引发内存泄漏。



下面是 WeakSet 的另一个例子：



```JS
const foos = new WeakSet()
class Foo {
  constructor() {
    foos.add(this)
  }
  method () {
    if (!foos.has(this)) {
      throw new TypeError('Foo.prototype.method 只能在Foo的实例上调用！');
    }
  }
}
```



上面代码保证了 `Foo` 的实例方法，只能在 `Foo` 的实例上调用。这里使用 WeakSet 的好处是，`foos` 对实例的引用，不会被计入内存回收机制，所以删除实例的时候，不用考虑 `foos`，也不会出现内存泄漏。