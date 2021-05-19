---
title: ES6 中 Set 和 Map 数据结构（二）
date: 2021-5-19
tags:
  - ES6
categories:
  - JavaScript
---

## Map 对象



传统对象（Object）只能用字符串当作键，这给它的使用带来了很大的限制。ES6 提供了 Map 数据结构。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。Map 也实现了 iterator 接口，所以可以使用『扩展运算符』和『for…of…』进行遍历。



### Maps 和 Objects 的区别



- 一个对象的键只能是字符串或者 Symbol，但一个 Map 的键可以是任意值。
- Map 中的键值是有序的（FIFO 原则，先入先出），而添加到对象中的键则不是。
- Map 的 size 属性保存着成员的个数，而对象只能手动计算。
- Map 实现了 iterator 接口，可以被迭代，而普通对象不能。
- Map是一个 hash 结构，针对于存在大量增删操作的场景，使用 Map 更合适。



### 基本使用



通过 `Map()` 函数生成，`Map` 函数可以接受一个数组作为参数，该数组的成员是一个个表示键值对的数组。



```js
// 创建一个空集合
let m = new Map();

// 创建一个非空集合
let m1 = new Map([
    ['name', '张三'],
  ['title', 'Author']
])
```



`Map` 构造函数接受数组作为参数，实际上执行的是下面的算法。



```js
const items = [
  ['name', '张三'],
  ['title', 'Author']
];

const map = new Map();

items.forEach(
  ([key, value]) => map.set(key, value)
);
```



事实上，不仅仅是数组，任何具有 Iterator 接口、且每个成员都是一个双元素的数组的数据结构（详见《ES6 中的迭代器（Iterator）》一章）都可以当作 `Map` 构造函数的参数。这就是说，`Set` 和 `Map` 都可以用来生成新的 Map。



```js
const set = new Set([
  ['foo', 1],
  ['bar', 2]
]);

const m1 = new Map(set);
const m2 = new Map([['baz', 3]]);
const m3 = new Map(m2);
```



### Map 的属性和方法



#### 实例属性



- **Map.prototype.size**



返回 `Map` 实例的成员总数



```js
const map = new Map();
map.set('foo', true);
map.set('bar', false);

map.size // 2
```



#### 操作方法



- **Map.prototype.set(key, value)**



设置键名 `key` 对应的键值为 `value`，然后返回整个 Map 结构。如果 `key` 已经有值，则键值会被更新，否则就新生成该键



```js
const m = new Map();

m.set('edition', 6)        // 键是字符串
m.set(262, 'standard')     // 键是数值
m.set(undefined, 'nah')    // 键是 undefined
```



`set` 方法返回的是当前的 `Map` 对象，因此可以采用链式写法。



```js
let map = new Map()
  .set(1, 'a')
  .set(2, 'b')
  .set(3, 'c');
```



- **Map.prototype.get(key)**



`get` 方法读取 `key` 对应的键值，如果找不到 `key`，返回 undefined。



```js
const m = new Map();

const hello = function() {console.log('hello');};
m.set(hello, 'Hello ES6!') // 键是函数

m.get(hello)  // Hello ES6!
```



注意，只有对同一个对象的引用，Map 结构才将其视为同一个键。这一点要非常小心。



```js
const map = new Map();

const k1 = ['a'];
const k2 = ['a'];

map
.set(k1, 111)
.set(k2, 222);

map.get(k1)     // 111
map.get(['a'])  // undefined
map.get(k2)     // 222
```



由上可知，Map 的键实际上是跟内存地址绑定的，只要内存地址不一样，就视为两个键。这就解决了同名属性碰撞（clash）的问题，我们扩展别人的库的时候，如果使用对象作为键名，就不用担心自己的属性与原作者的属性同名。



如果 Map 的键是一个简单类型的值（数字、字符串、布尔值），则只要两个值严格相等，Map 将其视为一个键，比如 `0` 和 `-0` 就是一个键，布尔值 `true` 和字符串 `true` 则是两个不同的键。另外，`undefined` 和 `null` 也是两个不同的键。虽然 `NaN` 不严格相等于自身，但 Map 将其视为同一个键。



```js
let map = new Map();

map.set(-0, 123);
map.get(+0) // 123

map.set(true, 1);
map.set('true', 2);
map.get(true) // 1

map.set(undefined, 3);
map.set(null, 4);
map.get(undefined) // 3

map.set(NaN, 123);
map.get(NaN) // 123
```



- **Map.prototype.has(key)**



`has` 方法返回一个布尔值，表示某个键是否在当前 Map 对象之中。



```js
const m = new Map();

m.set('edition', 6);
m.set(262, 'standard');
m.set(undefined, 'nah');

m.has('edition')     // true
m.has('years')       // false
m.has(262)           // true
m.has(undefined)     // true
```



- **Map.prototype.delete(key)**



`delete` 方法删除某个键，返回 `true`。如果删除失败，返回 `false`。





```js
const m = new Map();
m.set(undefined, 'nah');
m.has(undefined)     // true

m.delete(undefined)
m.has(undefined)     // false
```



- **Map.prototype.clear()**



`clear` 方法清除所有成员，没有返回值。



```js
let map = new Map();
map.set('foo', true);
map.set('bar', false);

map.size // 2
map.clear()
map.size // 0
```



#### 遍历方法



Map 结构原生提供三个遍历器生成函数和一个遍历方法：



- `Map.prototype.keys()`：返回键名的遍历器。
- `Map.prototype.values()`：返回键值的遍历器。
- `Map.prototype.entries()`：返回所有成员的遍历器。
- `Map.prototype.forEach()`：遍历 Map 的所有成员。



需要特别注意的是，Map 与 Set  的遍历顺序都是插入顺序。



```js
const map = new Map([
  ['F', 'no'],
  ['T',  'yes'],
]);

for (let key of map.keys()) {
  console.log(key);
}
// "F"
// "T"

for (let value of map.values()) {
  console.log(value);
}
// "no"
// "yes"

for (let item of map.entries()) {
  console.log(item[0], item[1]);
}
// "F" "no"
// "T" "yes"

// 或者
for (let [key, value] of map.entries()) {
  console.log(key, value);
}
// "F" "no"
// "T" "yes"

// 等同于使用 map.entries()
for (let [key, value] of map) {
  console.log(key, value);
}
// "F" "no"
// "T" "yes"
```



上面代码最后的那个例子，表示 Map 结构的默认遍历器接口（`Symbol.iterator`属性），就是 `entries`方法。



```js
map[Symbol.iterator] === map.entries  // true
```



Map 结构转为数组结构，比较快速的方法是使用扩展运算符（`...`）。



```js
const map = new Map([
  [1, 'one'],
  [2, 'two'],
  [3, 'three'],
]);

[...map.keys()]
// [1, 2, 3]
[...map.values()]
// ['one', 'two', 'three']
[...map.entries()]
// [[1,'one'], [2, 'two'], [3, 'three']]
[...map]
// [[1,'one'], [2, 'two'], [3, 'three']]
```



结合数组的 `map` 方法、`filter` 方法，可以实现 Map 的遍历和过滤（Map 本身没有 `map` 和 `filter` 方法）。



```js
const map0 = new Map()
  .set(1, 'a')
  .set(2, 'b')
  .set(3, 'c');

const map1 = new Map(
  [...map0].filter(([k, v]) => k < 3)
);
// 产生 Map 结构 {1 => 'a', 2 => 'b'}
const map2 = new Map(
  [...map0].map(([k, v]) => [k * 2, '_' + v])
    );
// 产生 Map 结构 {2 => '_a', 4 => '_b', 6 => '_c'}
```



此外，Map 还有一个 `forEach` 方法，与数组的 `forEach` 方法类似，也可以实现遍历。



```js
map.forEach(function(value, key, map) {
  console.log("Key: %s, Value: %s", key, value);
});
```



`forEach` 方法还可以接受第二个参数，用来绑定 `this`。



```js
const reporter = {
  report: function(key, value) {
    console.log("Key: %s, Value: %s", key, value);
  }
};
map.forEach(function(value, key, map) {
  this.report(key, value);
}, reporter);
```



上面代码中，`forEach` 方法的回调函数的 `this`，就指向 `reporter`。



### 类型转换



#### Map <=> Array



前面已经提过，Map 转为数组最方便的方法，就是使用扩展运算符（`...`）。



```js
const myMap = new Map()
  .set(true, 7)
  .set({foo: 3}, ['abc']);
[...myMap]
// [ [ true, 7 ], [ { foo: 3 }, [ 'abc' ] ] ]
```



将数组传入 Map 构造函数，就可以转为 Map。



```js
new Map([
  [true, 7],
  [{foo: 3}, ['abc']]
])
// Map {
//   true => 7,
//   Object {foo: 3} => ['abc']
// }
```



#### Map <=> Object



如果所有 Map 的键都是字符串，它可以无损地转为对象。



```js
function strMapToObj(strMap) {
  let obj = Object.create(null);
  for (let [k,v] of strMap) {
    obj[k] = v;
  }
  return obj;
}

const myMap = new Map()
  .set('yes', true)
  .set('no', false);
strMapToObj(myMap)
// { yes: true, no: false }
```



如果有非字符串的键名，那么这个键名会被转成字符串，再作为对象的键名。



对象转为 Map 可以通过 `Object.entries()`。



```js
let obj = {"a":1, "b":2};
let map = new Map(Object.entries(obj));
```



此外，也可以自己实现一个转换函数。



```js
function objToStrMap(obj) {
  let strMap = new Map();
  for (let k of Object.keys(obj)) {
    strMap.set(k, obj[k]);
  }
  return strMap;
}

objToStrMap({yes: true, no: false})
// Map {"yes" => true, "no" => false}
```



#### Map <=> JSON



Map 转为 JSON 要区分两种情况。一种情况是，Map 的键名都是字符串，这时可以选择转为对象 JSON。



```js
function strMapToJson(strMap) {
  return JSON.stringify(strMapToObj(strMap));
}

let myMap = new Map().set('yes', true).set('no', false);
strMapToJson(myMap)
// '{"yes":true,"no":false}'
```



另一种情况是，Map 的键名有非字符串，这时可以选择转为数组 JSON。



```js
function mapToArrayJson(map) {
  return JSON.stringify([...map]);
}
let myMap = new Map().set(true, 7).set({foo: 3}, ['abc']);
mapToArrayJson(myMap)
// '[[true,7],[{"foo":3},["abc"]]]'
```



JSON 转为 Map，正常情况下，所有键名都是字符串。



```js
function jsonToStrMap(jsonStr) {
  return objToStrMap(JSON.parse(jsonStr));
}

jsonToStrMap('{"yes": true, "no": false}')
// Map {'yes' => true, 'no' => false}
```



但是，有一种特殊情况，整个 JSON 就是一个数组，且每个数组成员本身，又是一个有两个成员的数组。这时，它可以一一对应地转为 Map。这往往是 Map 转为数组 JSON 的逆操作。



```js
function jsonToMap(jsonStr) {
  return new Map(JSON.parse(jsonStr));
}

jsonToMap('[[true,7],[{"foo":3},["abc"]]]')
// Map {true => 7, Object {foo: 3} => ['abc']}
```



## WeakMap



### 含义



`WeakMap` 结构与 `Map` 结构类似，也是用于生成键值对的集合。



```js
// WeakMap 可以使用 set 方法添加成员
const wm1 = new WeakMap();
const key = {foo: 1};
wm1.set(key, 2);
wm1.get(key) // 2

// WeakMap 也可以接受一个数组，
// 作为构造函数的参数
const k1 = [1, 2, 3];
const k2 = [4, 5, 6];
const wm2 = new WeakMap([[k1, 'foo'], [k2, 'bar']]);
wm2.get(k2) // "bar"
```



`WeakMap` 与 `Map` 的区别有两点。



首先，`WeakMap` 只接受对象作为键名（`null` 除外），不接受其他类型的值作为键名。



```js
const map = new WeakMap();
map.set(1, 2)
// TypeError: 1 is not an object!
map.set(Symbol(), 2)
// TypeError: Invalid value used as weak map key
map.set(null, 2)
// TypeError: Invalid value used as weak map key
```



上面代码中，如果将数值 `1` 和 `Symbol` 值作为 WeakMap 的键名，都会报错。



其次，`WeakMap` 的键名所指向的对象，不计入垃圾回收机制。



`WeakMap` 的设计目的在于，有时我们想在某个对象上面存放一些数据，但是这会形成对于这个对象的引用。请看下面的例子。



```js
const e1 = document.getElementById('foo');
const e2 = document.getElementById('bar');

const arr = [
  [e1, 'foo 元素'],
  [e2, 'bar 元素'],
];
```



上面代码中，`e1` 和 `e2` 是两个对象，我们通过 `arr` 数组对这两个对象添加一些文字说明。这就形成了 `arr` 对 `e1` 和 `e2` 的引用。



一旦不再需要这两个对象，我们就必须手动删除这个引用，否则垃圾回收机制就不会释放 `e1` 和 `e2` 占用的内存。



```js
// 不需要 e1 和 e2 的时候
// 必须手动删除引用
arr [0] = null;
arr [1] = null;
```



上面这样的写法显然很不方便。一旦忘了写，就会造成内存泄露。



WeakMap 就是为了解决这个问题而诞生的，它的键名所引用的对象都是弱引用，即垃圾回收机制不将该引用考虑在内。因此，只要所引用的对象的其他引用都被清除，垃圾回收机制就会释放该对象所占用的内存。也就是说，一旦不再需要，WeakMap 里面的键名对象和所对应的键值对会自动消失，不用手动删除引用。



基本上，如果你要往对象上添加数据，又不想干扰垃圾回收机制，就可以使用 WeakMap。一个典型应用场景是，在网页的 DOM 元素上添加数据，就可以使用 `WeakMap` 结构。当该 DOM 元素被清除，其所对应的 `WeakMap` 记录就会自动被移除。



```js
const wm = new WeakMap();

const element = document.getElementById('example');

wm.set(element, 'some information');
wm.get(element) // "some information"
```



上面代码中，先新建一个 WeakMap 实例。然后，将一个 DOM 节点作为键名存入该实例，并将一些附加信息作为键值，一起存放在 WeakMap 里面。这时，WeakMap 里面对 `element` 的引用就是弱引用，不会被计入垃圾回收机制。



也就是说，上面的 DOM 节点对象除了 WeakMap 的弱引用外，其他位置对该对象的引用一旦消除，该对象占用的内存就会被垃圾回收机制释放。WeakMap 保存的这个键值对，也会自动消失。



总之，`WeakMap` 的专用场合就是，它的键所对应的对象，可能会在将来消失。`WeakMap` 结构有助于防止内存泄漏。



注意，WeakMap 弱引用的只是键名，而不是键值。键值依然是正常引用。



### 语法



WeakMap 与 Map 在 API 上的区别主要是两个，一是没有遍历操作（即没有 `keys()`、`values(`) 和 `entries()` 方法），也没有 `size` 属性。因为没有办法列出所有键名，某个键名是否存在完全不可预测，跟垃圾回收机制是否运行相关。这一刻可以取到键名，下一刻垃圾回收机制突然运行了，这个键名就没了，为了防止出现不确定性，就统一规定不能取到键名。二是无法清空，即不支持 `clear` 方法。因此，WeakMap 只有四个方法可用：`get()`、`set()`、`has()`、`delete()`。



```js
const wm = new WeakMap();

// size、forEach、clear 方法都不存在
wm.size // undefined
wm.forEach // undefined
wm.clear // undefined
```



### 示例



WeakMap 的例子很难演示，因为无法观察它里面的引用会自动消失。此时，其他引用都解除了，已经没有引用指向 WeakMap 的键名了，导致无法证实那个键名是不是存在。



贺师俊老师[提示](https://github.com/ruanyf/es6tutorial/issues/362#issuecomment-292109104)，如果引用所指向的值占用特别多的内存，就可以通过 Node 的 `process.memoryUsage` 方法（返回描述 Node.js 进程的内存使用情况的对象）看出来。根据这个思路，网友[vtxf](https://github.com/ruanyf/es6tutorial/issues/362#issuecomment-292451925)补充了下面的例子。



首先，打开 Node 命令行。



```bash
$ node --expose-gc
```



上面代码中，`--expose-gc` 参数表示允许手动执行垃圾回收机制。



然后，执行下面的代码。



```js
// 手动执行一次垃圾回收，保证获取的内存使用状态准确
> global.gc();
undefined

// 查看内存占用的初始状态，heapUsed 为 4M 左右
> process.memoryUsage();
{ rss: 21106688,
  heapTotal: 7376896,
  heapUsed: 4153936,
  external: 9059 }

> let wm = new WeakMap();
undefined

// 新建一个变量 key，指向一个 5*1024*1024 的数组
> let key = new Array(5 * 1024 * 1024);
undefined

// 设置 WeakMap 实例的键名，也指向 key 数组
// 这时，key 数组实际被引用了两次，
// 变量 key 引用一次，WeakMap 的键名引用了第二次
// 但是，WeakMap 是弱引用，对于引擎来说，引用计数还是1
> wm.set(key, 1);
WeakMap {}

> global.gc();
undefined

// 这时内存占用 heapUsed 增加到 45M 了
> process.memoryUsage();
{ rss: 67538944,
  heapTotal: 7376896,
  heapUsed: 45782816,
  external: 8945 }

// 清除变量 key 对数组的引用，
// 但没有手动清除 WeakMap 实例的键名对数组的引用
> key = null;
null

// 再次执行垃圾回收
> global.gc();
undefined

// 内存占用 heapUsed 变回 4M 左右，
// 可以看到 WeakMap 的键名引用没有阻止 gc 对内存的回收
> process.memoryUsage();
{ rss: 20639744,
  heapTotal: 8425472,
  heapUsed: 3979792,
  external: 8956 }
```



上面代码中，只要外部的引用消失，WeakMap 内部的引用，就会自动被垃圾回收清除。由此可见，有了 WeakMap 的帮助，解决内存泄漏就会简单很多。



Chrome 浏览器的 Dev Tools 的 Memory 面板，有一个垃圾桶的按钮，可以强制垃圾回收（garbage collect）。这个按钮也能用来观察 WeakMap 里面的引用是否消失。



### 用途



前文说过，WeakMap 应用的典型场合就是 DOM 节点作为键名。下面是一个例子。



```js
let myWeakmap = new WeakMap();

myWeakmap.set(
  document.getElementById('logo'),
  {timesClicked: 0})
;

document.getElementById('logo').addEventListener('click', function() {
  let logoData = myWeakmap.get(document.getElementById('logo'));
  logoData.timesClicked++;
}, false);
```



上面代码中，`document.getElementById('logo')` 是一个 DOM 节点，每当发生 `click` 事件，就更新一下状态。我们将这个状态作为键值放在 WeakMap 里，对应的键名就是这个节点对象。一旦这个 DOM 节点删除，该状态就会自动消失，不存在内存泄漏风险。



WeakMap 的另一个用处是部署私有属性。



```js
const _counter = new WeakMap();
const _action = new WeakMap();

class Countdown {
  constructor(counter, action) {
    _counter.set(this, counter);
    _action.set(this, action);
  }
  dec() {
    let counter = _counter.get(this);
    if (counter < 1) return;
    counter--;
    _counter.set(this, counter);
    if (counter === 0) {
      _action.get(this)();
    }
  }
}

const c = new Countdown(2, () => console.log('DONE'));

c.dec()
c.dec()
// DONE
```



上面代码中，`Countdown` 类的两个内部属性 `_counter` 和 `_action`，是实例的弱引用，所以如果删除实例，它们也就随之消失，不会造成内存泄漏。



## 参考文章



- [ES6 Map 与 Set](https://www.runoob.com/w3cnote/es6-map-set.html)
- [Set 和 Map 数据结构](https://es6.ruanyifeng.com/?search=rest&x=8&y=12#docs/set-map)