---
title: ES6 中的 Symbol 类型
date: 2021-5-8
tags:
  - ES6
categories:
  - JavaScript
---

## 概述



ES5 的对象属性名都是字符串，缺点是当你使用了一个他人提供的对象，但又想为这个对象添加新的方法（mixin 模式），新方法的名字就有可能与现有方法产生冲突。ES6 引入 `Symbol` 的原因就是为了保证每个属性的名字都是独一无二的。



ES6 数据类型除了 `Number`、`String`、`Boolean`、`Object`、`null` 和 `undefined` ，还新增了 `Symbol`。



`Symbol` 的特性：



- Symbol 的值是唯一的，用来解决命名冲突的问题
- Symbol 值不能与其他数据进行运算
- Symbol  定义的对象属性不能使用 `for…in`、 `for...of` 循环遍历，但是可以通过 `Object.getOwnPropertySymbols()` 和 `Reflect.ownKeys()` 取到对象的键名
- Symbol 有 11 个内置的值，指向语言内部使用的方法



## Symbol 基本使用



Symbol 值是通过 `Symbol` 函数生成。不能用 `new` 命令，因为 `Symbol` 是原始数据类型，不是对象，它是一种类似于字符串的数据类型。



Symbol 函数可以接受一个字符串作为参数，为新创建的 `Symbol` 提供描述，用来显示在控制台或者转为字符串的时候使用，仅仅是为了便于区分。



```js
// 创建 Symbol
let sy = Symbol("KK");
console.log(sy);   // Symbol(KK)
typeof(sy);        // "symbol"
```



`Symbol` 函数的参数只是表示对当前 Symbol 值的描述，因此相同参数的 `Symbol` 函数的返回值是不相等的。



```js
// 没有参数的情况
let s1 = Symbol()
let s2 = Symbol()

s1 === s2 // false

// 有参数的情况
let s1 = Symbol('foo')
let s2 = Symbol('foo')

s1 === s2 // false
```



Symbol 值不能与其他类型的值进行运算，会报错。



```js
let sym = Symbol('My symbol');

"your symbol is " + sym
// TypeError: can't convert symbol to string
`your symbol is ${sym}`
// TypeError: can't convert symbol to string
```



但是，Symbol 值可以显式转为字符串。



```js
let sym = Symbol('My symbol')
String(sym)     // 'Symbol(My symbol)'
sym.toString()  // 'Symbol(My symbol)'
```



另外，Symbol 值也可以转为布尔值，但是不能转为数值。



```js
let sym = Symbol()
Boolean(sym) // true

!sym  // false

if (sym) {
  // ...
}

Number(sym) // TypeError
sym + 2 // TypeError
```



## Symbol.prototype.description



当你需要读取 Symbol 描述时需要显式转为字符串，上面的用法不是很方便。ES2019 提供了一个实例属性 `description`，直接返回 Symbol 的描述。



```js
const sym = Symbol('foo')

sym.description // "foo"
```



## Symbol.for()



除了 Symbol 函数生成 Symbol 值外，还可以通过 `Symbol.for()` ，它接受一个字符串作为参数，首先会在全局搜索被登记的 Symbol 中是否有该参数作为名称的 Symbol 值，如果有即返回该 Symbol 值，若没有则新建并返回一个以该字符串参数为名称的 Symbol 值，并登记在全局环境中供搜索。



```js
let yellow = Symbol("Yellow")
let yellow1 = Symbol.for("Yellow")
yellow === yellow1      // false

let yellow2 = Symbol.for("Yellow")
yellow1 === yellow2     // true
```



`Symbol.for()` 与 `Symbol()` 这两种写法，都会生成新的 Symbol。它们的区别是，前者会被登记在全局环境中供搜索，后者不会。



## Symbol.keyFor()



`Symbol.keyFor()` 返回一个已登记的 Symbol 类型值的 key ，用来检测该字符串参数作为名称的 Symbol 值是否已被登记。



```js
let s1 = Symbol.for("foo")
Symbol.keyFor(s1) // "foo"

let s2 = Symbol("foo")
Symbol.keyFor(s2) // undefined
```



上面代码中，变量 `s2` 属于未登记的 Symbol 值，所以返回 `undefined`。



## 应用场景



### 作为属性名



由于每一个 Symbol 的值都是不相等的，所以 Symbol 作为对象的属性名，可以保证属性不重名



```js
let sy = Symbol()

// 写法1
let syObject = {
  [sy] (arg) { }
}

// 或者
let syObject = {
  [Symbol()] (arg) { }
}

// 写法2
let syObject = {}
obj[sy] = "zs"

// 写法3
let syObject = {}
Object.defineProperty(syObject, sy, { value: "kk" })
```



应用场景:  遇到唯一性的场景时使用 Symbol



**注意**：Symbol 作为对象属性名时不能用点（.）运算符，要用方括号。因为点运算符后面跟的是字符串。



```js
let syObject = {};
syObject[sy] = "kk";
 
syObject[sy];  // "kk"
syObject.sy;   // undefined
```



### 定义常量



ES5 中用字符串定义常量不能保证常量是独特的，这样会引起一些问题：



```js
const COLOR_RED = "red"
const MY_RED = "red"

function getComplement (color) {
  switch (color) {
    case COLOR_RED:
      return "COLOR_RED"
    case MY_RED:
      return "MY_RED"
    default:
      throw new Error('Undefined color')
  }
}

try {
  let colorName1 = getComplement(COLOR_RED)
  let colorName2 = getComplement(MY_RED)
  console.log(colorName1)  // COLOR_RED
  console.log(colorName2)  // COLOR_RED
} catch (e) {
  console.log(e)
}
```



上述代码中，`COLOR_RED` 与 `MY_RED` 的值是相同的，所以返回的永远是 `"COLOR_RED"`。



但是使用 Symbol 定义常量，这样就可以保证这一组常量的值都不相等。用 Symbol 来修改上面的例子。



```js
const COLOR_RED = Symbol()
const MY_RED = Symbol()

function getComplement (color) {
  switch (color) {
    case COLOR_RED:
      return "COLOR_RED"
    case MY_RED:
      return "MY_RED"
    default:
      throw new Error('Undefined color')
  }
}

try {
  let colorName1 = getComplement(COLOR_RED)
  let colorName2 = getComplement(MY_RED)
  console.log(colorName1)  // COLOR_RED
  console.log(colorName2)  // MY_RED
} catch (e) {
  console.log(e)
}
```



这样就可以保证上面的 `switch` 语句会按设计的方式工作。



### 消除魔术字符串



魔术字符串指的是，在代码之中多次出现、与代码形成强耦合的某一个具体的字符串或者数值。风格良好的代码，应该尽量消除魔术字符串，改由含义清晰的变量代替。



```js
function getMonth (month) {
  if (month == "May") {
    return true
  } else {
    return false
  }
}

getMonth("May")
```



上面代码中“May”就是魔术字符串，多次出现，如果出现的次数足够多，不利于将来的修改和维护。常用的消除魔术字符串的方法，就是把它写成一个变量。



```js
const mayMonth = "May"

function getMonth (month) {
  if (month == mayMonth) {
    return true
  } else {
    return false
  }
}

getMonth(mayMonth)
```



如果仔细分析，可以发现 `mayMonth` 等于哪个值并不重要，只要确保不会跟其他常量的值冲突即可。因此，这里就很适合改用 Symbol 值。



```js
const mayMonth = Symbol()
```



上面代码中，除了将 `mayMonth` 的值设为一个 Symbol，其他地方都不用修改。



## 属性名的遍历



Symbol 值作为属性名时，该属性是公有属性不是私有属性，可以在类的外部访问。但是不会出现在 `for...in`、`for...of` 的循环中，也不会被 `Object.keys()`、`Object.getOwnPropertyNames()`、`JSON.stringify()` 返回。如果要读取到一个对象的 Symbol 属性，可以通过 `Object.getOwnPropertySymbols()` 和 `Reflect.ownKeys()` 取到。



### Object.getOwnPropertySymbols()



`Object.getOwnPropertySymbols()` 方法，可以获取指定对象的所有 Symbol 属性名。该方法返回一个数组，成员是当前对象的所有用作属性名的 Symbol 值。



```js
let sy = Symbol("sy")

let obj = {}
obj[sy] = "kk"
obj.name = 'zs'

for (let i in obj) {
  console.log(i) // 无输出
}

Object.keys(obj)                                  // ["name"]
Object.getOwnPropertyNames(obj)                   // ["name"]
Object.getOwnPropertySymbols(obj)                 // [Symbol(sy)]
```



### Reflect.ownKeys() 



另一个新的 API，`Reflect.ownKeys()` 方法可以返回所有类型的键名，包括常规键名和 Symbol 键名。



```js
let obj = {
  [Symbol('my_key')]: 1,
  enum: 2,
  nonEnum: 3
}

Reflect.ownKeys(obj)  //  ["enum", "nonEnum", Symbol(my_key)]
```



由于以 Symbol 值作为键名，不会被常规方法遍历得到。我们可以利用这个特性，为对象定义一些非私有的、但又希望只用于内部的方法。



```js
let size = Symbol('size')
class Collection {
  constructor() {
    this[size] = 0
  }
  add (item) {
    this[this[size]] = item
    this[size]++
  }
  static sizeOf (instance) {
    return instance[size]
  }
}
let x = new Collection()
Collection.sizeOf(x) // 0
x.add('foo')
Collection.sizeOf(x) // 1
Object.keys(x) // ['0']
Object.getOwnPropertyNames(x) // ['0']
Object.getOwnPropertySymbols(x) // [Symbol(size)]
```



上面代码中，对象 `x` 的 `size` 属性是一个 Symbol 值，所以 `Object.keys(x)`、`Object.getOwnPropertyNames(x)` 都无法获取它。这就造成了一种非私有的内部方法的效果。



## 模块的 Singleton 模式



Singleton 模式指的是调用一个类，任何时候返回的都是同一个实例。



对于 Node 来说，模块文件可以看成是一个类。怎么保证每次执行这个模块文件，返回的都是同一个实例呢？



很容易想到，可以把实例放到顶层对象 `global`。



```js
// mod.js
function A() {
  this.foo = 'hello';
}
if (!global._foo) {
  global._foo = new A();
}
module.exports = global._foo;
```



然后，加载上面的 `mod.js`。



```js
const a = require('./mod.js');
console.log(a.foo);
```



上面代码中，变量 `a` 任何时候加载的都是 `A` 的同一个实例。



但是，这里有一个问题，全局变量 `global._foo` 是可写的，任何文件都可以修改。



```js
global._foo = { foo: 'world' };
const a = require('./mod.js');
console.log(a.foo);
```



上面的代码，会使得加载 `mod.js` 的脚本都失真。



为了防止这种情况出现，我们就可以使用 Symbol。



```js
// mod.js
const FOO_KEY = Symbol.for('foo');
function A() {
  this.foo = 'hello';
}
if (!global[FOO_KEY]) {
  global[FOO_KEY] = new A();
}
module.exports = global[FOO_KEY];
```



上面代码中，可以保证 `global[FOO_KEY]` 不会被无意间覆盖，但还是可以被改写。



```js
global[Symbol.for('foo')] = { foo: 'world' };
const a = require('./mod.js');
```



如果键名使用 `Symbol` 方法生成，那么外部将无法引用这个值，当然也就无法改写。



```js
// mod.js
const FOO_KEY = Symbol('foo');
// 后面代码相同 ……
```



上面代码将导致其他脚本都无法引用 `FOO_KEY`。但这样也有一个问题，就是如果多次执行这个脚本，每次得到的 `FOO_KEY` 都是不一样的。虽然 Node 会将脚本的执行结果缓存，一般情况下，不会多次执行同一个脚本，但是用户可以手动清除缓存，所以也不是绝对可靠。



## 内置的 Symbol 值



除了定义自己使用的 Symbol 值以外，ES6 还提供了 11 个内置的 Symbol 值，指向语言内部使用的方法。可以称这些方法为魔术方法，因为它们会在特定的场景下自动执行。



| **方法**                  | **描述**                                                     |
| ------------------------- | ------------------------------------------------------------ |
| Symbol.hasInstance        | 当其他对象使用 instanceof 运算符，判断是否为该对象的实例时，会调用这个方法 |
| Symbol.isConcatSpreadable | 对象的 Symbol.isConcatSpreadable 属性等于的是一个 布尔值，表示该对象用于 Array.prototype.concat() 时， 是否可以展开。 |
| Symbol.species            | 创建衍生对象时，会使用该属性                                 |
| Symbol.match              | 当执行 str.match(myObject) 时，如果该属性存在，会调用它，返回该方法的返回值。 |
| Symbol.replace            | 当该对象被 str.replace(myObject) 方法调用时，会返回该方法的返回值。 |
| Symbol.search             | 当该对象被 str. search(myObject) 方法调用时，会返回该方法的返回值。 |
| Symbol.split              | 当该对象被 str.split(myObject) 方法调用时，会返回该方法的返回值。 |
| Symbol.iterator           | 对象进行 for...of 循环时，会调用 Symbol.iterator 方法，返回该对象的默认遍历器 |
| Symbol.toPrimitive        | 该对象被转为原始类型的值时，会调用这个方法，返回该对象对应的原始类型值。 |
| Symbol. toStringTag       | 在该对象上面调用 toString 方法时，返回该方法的返回值         |
| Symbol. unscopables       | 该对象指定了使用 with 关键字时，哪些属性会被 with 环境排除。 |



以下只举例个别方法，详细的可以查看阮一峰老师的[《ECMAScript 6 入门》](https://es6.ruanyifeng.com/?search=rest&x=8&y=12)



### Symbol.hasInstance



对象的 `Symbol.hasInstance` 属性，指向一个内部方法。当其他对象使用 `instanceof` 运算符，判断是否为该对象的实例时，会调用这个方法。比如，`foo instanceof Foo` 在语言内部，实际调用的是 `Foo[Symbol.hasInstance](foo)`。



```js
class MyClass {
  [Symbol.hasInstance] (foo) {
    return foo instanceof Array
  }
}
[1, 2, 3] instanceof new MyClass() // true
```



上面代码中，`MyClass` 是一个类，`new MyClass()` 会返回一个实例。该实例的 `Symbol.hasInstance` 方法，会在进行 `instanceof` 运算时自动调用，判断左侧的运算子是否为 `Array` 的实例。



下面是另一个例子。



```js
class Even {
  static [Symbol.hasInstance] (obj) {
    return Number(obj) % 2 === 0
  }
}

// 等同于
const Even = {
  [Symbol.hasInstance] (obj) {
    return Number(obj) % 2 === 0
  }
}

1 instanceof Even // false
2 instanceof Even // true
12345 instanceof Even // false
```



### Symbol.isConcatSpreadable



对象的 `Symbol.isConcatSpreadable` 属性等于一个布尔值，表示该对象用于 `Array.prototype.concat()` 时，是否可以展开。



```js
let arr1 = ['c', 'd'];
['a', 'b'].concat(arr1, 'e') // ['a', 'b', 'c', 'd', 'e']
arr1[Symbol.isConcatSpreadable] // undefined
let arr2 = ['c', 'd'];
arr2[Symbol.isConcatSpreadable] = false;
['a', 'b'].concat(arr2, 'e') // ['a', 'b', ['c','d'], 'e']
```



上面代码说明，数组的默认行为是可以展开，`Symbol.isConcatSpreadable` 默认等于 `undefined`。该属性等于 `true` 时，也有展开的效果。



类似数组的对象正好相反，默认不展开。它的 `Symbol.isConcatSpreadable` 属性设为 `true`，才可以展开。



```js
let obj = {length: 2, 0: 'c', 1: 'd'};
['a', 'b'].concat(obj, 'e') // ['a', 'b', obj, 'e']
obj[Symbol.isConcatSpreadable] = true;
['a', 'b'].concat(obj, 'e') // ['a', 'b', 'c', 'd', 'e']
```



`Symbol.isConcatSpreadable` 属性也可以定义在类里面。



```js
class A1 extends Array {
  constructor(args) {
    super(args);
    this[Symbol.isConcatSpreadable] = true;
  }
}

class A2 extends Array {
  constructor(args) {
    super(args);
  }
  get [Symbol.isConcatSpreadable] () {
    return false;
  }
}

let a1 = new A1();
a1[0] = 3;
a1[1] = 4;

let a2 = new A2();
a2[0] = 5;
a2[1] = 6;

[1, 2].concat(a1).concat(a2)
// [1, 2, 3, 4, [5, 6]]
```



上面代码中，类 `A1` 是可展开的，类 `A2` 是不可展开的，所以使用 `concat` 时有不一样的结果。



注意，`Symbol.isConcatSpreadable` 的位置差异，`A1` 是定义在实例上，`A2` 是定义在类本身，效果相同。



### Symbol.iterator



对象的 `Symbol.iterator` 属性，指向该对象的默认遍历器方法。



对象进行 `for...of` 循环时，会自动调用 `Symbol.iterator` 方法，详细介绍参见《ES6 中的迭代器（Iterator）》一章。



```js
const obj = {
  name: "zs",
  age: 18,
  [Symbol.iterator]: function () {
    let nextIndex = 0;
    let _this = this;
    return {
      next: function () {
        return nextIndex < Object.keys(obj).length
          ? { value: Object.keys(obj)[nextIndex++], done: false }
          : { valeu: undefined, done: true };
      },
    };
  },
};

// 完成了对一个对象的遍历
for (const v of obj) {
  console.log(v);
}

// output:
// name
// age
```



## 参考文章



- [ES6 Symbol](https://www.runoob.com/w3cnote/es6-symbol.html)
- [ECMAScript 6 入门](https://es6.ruanyifeng.com/?search=rest&x=8&y=12)