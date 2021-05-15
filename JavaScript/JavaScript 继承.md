---
title: JavaScript 继承
date: 2021-3-23
tags:
  - JS 基础
categories:
  - JavaScript
---

JS 中继承可以按照是否使用 `object` 函数（在下文中会提到），将继承分成两部分（`Object.create` 是ES5新增的方法，用来规范化这个函数）



其中，原型链继承和原生式继承有一样的优缺点，构造函数继承与寄生式继承也相互对应。寄生组合继承基于 `Object.create`, 同时优化了组合继承，成为了完美的继承方式。ES6 `Class Extends` 的结果与寄生组合继承基本一致，但是实现方案又略有不同



![2021/03/23/TOIMG24e9f0323093322N.png](https://picturebed.tumiblog.top/2021/03/23/TOIMG24e9f0323093322N.png)



## 原型链继承



**优点：**



- 可以继承父类原型属性/方法
- 父类方法可以复用



**缺点：**



- 父类的引用属性会被所有子类实例共享
- 子类构建实例时不能向父类传递参数



只要把子类的 `prototype` 设置为父类的实例，就完成了继承



```js
// 父类
function Person() {}

// 子类
function Student(){}

// 继承
Student.prototype = new Person()
```



继承的属性中，子类属性会覆盖父类属性，因为原型链是逐级查找的



```js
// 父类
function Person(name,age) {
  this.name = name || 'unknow'
  this.age = age || 0
}

// 子类
function Student(name){
  this.name = name
  this.score = 80
}

// 继承
Student.prototype = new Person()

var stu = new Student('lucy')

console.log(stu.name)  // lucy    --子类覆盖父类的属性
console.log(stu.age)   // 0       --父类的属性
console.log(stu.score) // 80      --子类自己的属性
```



父类方法可以进行复用



```js
// 父类
function Person() {
  this.say = function() {};
}

// 子类
function Student() {}

// 继承
Student.prototype = new Person()

var stu1 = new Student();
var stu2= new Student();

console.log(stu1.say === stu2.say); // true
```



当为子类原型添加方法时，需写在继承之后，否则会被覆盖



```js
// 父类
function Person() {}

// 为父类新曾一个方法
Person.prototype.say = function() {
  console.log('I am a person')
}

// 子类
function Student() {}

// 继承 注意：继承必须要写在子类方法定义的前面
Student.prototype = new Person()

// 为子类新增一个方法(在继承之后,否则会被覆盖)
Student.prototype.study = function () {
  console.log('I am studing')
}
```



但是，原型链继承有一个缺点：就是属性如果是引用类型的话，会共享引用类型



```js
// 父类
function Person() {
  this.hobbies = ['music','reading']
}

// 子类
function Student(){}

// 继承
Student.prototype = new Person()

var stu1 = new Student()
var stu2 = new Student()

stu1.hobbies.push('basketball')

console.log(stu1.hobbies)   // music,reading,basketball
console.log(stu2.hobbies)   // music,reading,basketball
```



可以看到，当我们改变 stu1 的引用类型的属性时，stu2 对应的属性也会跟着更改。原因是因为引用类型是保存在堆内存中的，也就是父类中保存的是一个指针



之所以说子类构建实例时不能向父类传递参数，因为这样就失去了面向对象编程的意义，示例：



```js
// 父类
function Parent(name){ this.name=name; }

// 子类
function Child(){}

// 继承
Child.prototype = new Parent("zs")

var person1 = new Child()
var person2 = new Child()

console.log(person1.name) // zs
console.log(person1.name) // zs
```



此时，Child 类就拥有了 name=“zs” 属性，而 Children 类的实例对象只能被迫接受这个 name 属性



## 构造函数继承



**优点：**



- 父类的引用属性不会被共享
- 子类构建实例时可以向父类传递参数



**缺点：**



- 只能继承父类的实例属性和方法，不能继承原型属性/方法
- 父类的方法不能复用，子类实例的方法每次都是单独创建的



通过改变 this 的指向，将父类构造函数的内容复制给子类构造函数



```js
// 父类
function Person() {
  this.hobbies = ['music','reading']
}

// 子类
function Student(){
  Person.call(this)
}

var stu1 = new Student()
var stu2 = new Student()

stu1.hobbies.push('basketball')
console.log(stu1.hobbies)   // music,reading,basketball
console.log(stu2.hobbies)   // music,reading
```



这样，我们就解决了引用类型被所有实例共享的问题了



但这就导致了一个很矛盾的问题：函数也是引用类型，也就是说，每个实例里面的函数，虽然功能一样，但是却不是同一个函数，就相当于我们每实例化一个子类，就复制了一遍函数代码



```js
// 父类
function Person() {
  this.say = function() {}
}

// 子类
function Student(){
  Person.call(this)
}

var stu1 = new Student()
var stu2 = new Student()
console.log(stu1.say === stu2.say)   // false
```



另一个好处就是可以给父类传参



```js
// 父类
function Person(name) {
  this.name = name
}

// 子类
function Student(name){
  Person.call(this, name)
}

var stu1 = new Student('lucy')
var stu2 = new Student('lili')

console.log(stu1.name)   // lucy
console.log(stu2.name)   // lili
```



## 组合继承



**优点：**



- 可以继承父类原型属性/方法
- 父类的引用属性不会被共享
- 子类构建实例时可以向父类传递参数



**缺点：**



调用了两次父类的构造函数，第一次给子类的原型添加了父类的属性，第二次又给子类的构造函数添加了父类的属性，从而覆盖了子类原型中的同名参数。这种被覆盖的情况造成了性能上的浪费



组合继承，就是各取上面两种继承的长处，普通属性使用构造函数继承，函数使用原型链继承



```js
// 父类
function Person(name) {
  this.hobbies = ['music','reading'];
  this.name = name
}

// 父类函数
Person.prototype.say = function() {console.log('I am a person')}

// 子类
function Student(name){
    Person.call(this, name)        // 构造函数继承(继承属性)，第二次调用
}
// 继承
Student.prototype = new Person()   // 原型链继承(继承方法)，第一次调用

// 实例化
var stu1 = new Student('lucy')
var stu2 = new Student('lili')

stu1.hobbies.push('basketball')
console.log(stu1.hobbies)        // music,reading,basketball
console.log(stu2.hobbies)        // music,reading

console.log(stu1.say == stu2.say)     // true

console.log(stu1.name)   // lucy
console.log(stu2.name)   // lili
```



第一次调用 `Person()` 时，给子类的原型添加了父元素的属性，第二次调用 `Person()` 时给子类构造函数添加了父元素的属性。此时子类的原型和构造函数都拥有父类的属性，而方法在父类的原型中。通过原型链的关系覆盖了子类原型中的同名属性，而要想使方法可以复用，必须定义在父类的原型中，而不能定义在父类中



## 原型式继承



**优点：** 父类方法可以复用



**缺点：**



- 父类的引用属性会被所有子类实例共享
- 子类构建实例时不能向父类传递参数



原型式继承的方法本质上是对参数对象的一个浅复制，原型式继承跟原型链继承很像，两者因为都是基于prototype 继承的，所以也有一些相同的特性



```js
function inheritObject (proto) {
  function F () {}
  F.prototype = proto
  return new F()
}

var person = {
  name: "Nicholas",
  friends: ["Shelby", "Court", "Van"]
};

var anotherPerson = inheritObject(person);
```



ECMAScript 5 通过新增 `Object.create()` 方法规范化了原型式继承。这个方法接收两个参数：一 个用作新对象原型的对象和（可选的）一个为新对象定义额外属性的对象。在传入一个参数的情况下，`Object.create()` 与 `inheritObject()` 方法的行为相同



所以上文中代码可以转变为



```js
var person = {
  name: "Nicholas",
  friends: ["Shelby", "Court", "Van"]
};

var anotherPerson = Object.create(person);
```



## 寄生式继承



寄生式继承其实就是在原型式继承的基础上做了一些增强



```js
function inheritAndStrengthen(proto){
  function F () {}
  F.prototype = proto
  let f = new F()
  f.say = function() { 
    console.log('I am a person')
  }
  return f
}
```



跟原型式继承比，差别就是在实例对象 f 返回之前，给 f 添加函数。但是这样在实例对象上添加的引用属性，跟构造函数模式一样, 实例对象的引用类型属性无法共享



## 寄生组合继承



上面有提到组合继承父类构造函数里的代码会执行两遍，可以使用寄生组合继承来解决这个问题



```js
function inheritPrototype (subType, superType) {
  var prototype = Object.create(superType.prototype) // 目的是为了继承父类原型的方法
  prototype.constructor = subType // 修正原型的构造函数（原本构造函数是 F()）
  subType.prototype = prototype // 目的是为了与父类原型关联起来
}

function SuperType (name) {
  this.name = name
  this.colors = ["red", "blue", "green"]
}

SuperType.prototype.sayName = function () {
  alert(this.name)
}

function SubType (name, age) {
  SuperType.call(this, name)
  this.age = age
}
// 核心：因为是对父类原型的复制，所以不包含父类的构造函数，也就不会调用两次父类的构造函数造成浪费
inheritPrototype(SubType, SuperType)

SubType.prototype.sayAge = function () {
  alert(this.age)
}

var test = new SubType("zs", 20)
```



可以参考以下的原型链

![ ](https://picturebed.tumiblog.top/2021/03/23/TOIMGdd7c60323093352N.jpg)



寄生组合式继承是引用类型最理想的继承范式



## ES6 Class extends



ES6 继承的结果和寄生组合继承相似，本质上，ES6 继承是一种语法糖。但是，寄生组合继承是先创建子类实例 this 对象，然后再对其增强；而 ES6 先将父类实例对象的属性和方法，加到 this 上面（所以必须先调用 super 方法），然后再用子类的构造函数修改 this



```js
class Person {
  constructor(name) {
    this.name = name
  }
  // 原型方法
  // 即 Person.prototype.getName = function() { }
  getName() {
    console.log('Person:', this.name)
  }
}

class Gamer extends Person {
  constructor(name, age) {
    // 子类中存在构造函数，则需要在使用“this”之前首先调用 super()
    super(name) // 类似于call的继承：在这里super相当于把 Person 的constructor 给执行了，并且让方法中的 this 是 Gamer 的实例，super 当中传递的实参都是在给 Person 的 constructor 传递
    this.age = age
  }
}

const asuna = new Gamer('Asuna', 20)
asuna.getName() // 成功访问到父类的方法
```



## 参考文章



- [JS中的继承(上)](https://segmentfault.com/a/1190000014476341)
- [JS中的继承(下)](https://github.com/noahlam/articles/blob/master/JS中的继承(下).md)
- [一篇文章理解JS继承——原型链/构造函数/组合/原型式/寄生式/寄生组合/Class extends](https://segmentfault.com/a/1190000015727237)