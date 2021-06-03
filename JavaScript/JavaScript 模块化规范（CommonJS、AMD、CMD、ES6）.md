---
title: JavaScript 模块化规范（CommonJS、AMD、CMD、ES6）
date: 2021-6-3 13:07:25
categories:
  - JavaScript
---

## 介绍



模块化与函数式编程相似，前者是将复杂的程序拆分成块（文件）；后者是将复杂的程序封装成函数，都是为了达到复用的效果。块的内部数据是私有的，只有导出接口才能与其它模块通信。



最早，我们是这么写代码的：



```js
function foo () {
    // ... 
}

function bar () {
    // ... 
}
```



这样很容易导致全局命名冲突。那定义一个对象，不放到全局中不就好了吗，这就是 Namespace 模式。



```js
var MYAPP =  {
  foo: function () {},
  bar: function () {}
}

MYAPP.foo()
```



这样全局变量虽然少了，但本质了是个对象，是可以操作修改的，这样很不安全。



函数是 JavaScript 唯一的 LocalScope，函数外部是修改不了函数内部代码的。所以相对来说是安全的。这就是 IIFE 模式。



```js
// 利用闭包
var Module = (function () {
  var _private = 'safe now'
  var foo = function () {
    console.log(_private) 
  }
  
  // 暴露接口
  return {
    foo: foo 
  }
})()
```



开发中往往会依赖第三方库，这时候就要引入依赖。例如依赖于 jQuery 时，就要将 `jQuery` 引入。



```js
var Module = (function ($) {
  // 使用 jQuery
  var _$body = $("body")
  var foo = function () {
    console.log(_$body)
  }
  
  return {
    foo: foo
  }
  // 引入 jQuery
})(jQuery)

Module.foo()
```



这就是模块模式，也是现代模块实现的基石。



模块化可以降低单个文件的复杂度，降低偶合度，使文件更好维护。当文件分离后可以按需加载，提高了复用性。并且每个模块都是一个独立的作用域，避免了命名冲突。



但是这样就引发了一个问题：文件分离导致需要发送更多的 HTTP 请求，并且使模块与模块之间的依赖变得模糊。



模块化规范就可以通过依赖关系来合并文件，现在有主流的 CommonJS、AMD、CMD、ES6 四种规范。



## CommonJS



CommonJS 规范中，每个文件都可以当作一个模块。在服务器端（Node.js），模块的加载是运行时同步加载的；在浏览器端，没有 `require` 方法，模块需要使用 Browserify 工具编译打包处理。



使用 `require` 方法来加载模块，使用 `exports` 接口对象来导出模块中的成员。



### 加载（require）



加载模块后，会执行模块中的代码，并得到模块中使用 `exports` 导出的接口对象。



```js
// 加载第三方模块
var unique = require('uniq')

// 加载自定义模块
var myModule = require('./src')
```



第三方模块会自动去 `node_modules` 目录下查找。



### 导出（exports）



只有将模块中的成员导出，才能被其它模块访问。



```js
// 1.导出单个成员
module.exports = 'hello'; 
// 后者会覆盖前者 
module.exports = function add(x,y) { 
  return x+y; 
}

// 2.导出多个成员
exports.b = 'hello'; 
exports.c = function(){ 
  console.log('bbb') 
};

// 还可以通过另一种方式导出多个成员
module.exports = { 
  foo: 'hello', 
  add:function(){ 
    return x+y; 
  } 
};
```



初始 `module.exports` 是一个空对象。



```js
module = { 
  exports = { 
    } 
}
```



即可以通过 `module.exports` 也可以通过 `exports` 导出，因为 `exports` 是 `module.exports` 的一个引用。



```js
console.log(exports === module.exports);    // true 
exports.foo = 'bar'; 
//等价于 
module.exports.foo = 'bar';
```



当给 `exports` 重新赋值后，`exports != module.exports`，可以通过 `exports = module.exports` 用来重新建立关系。



模块最终 `return` 的是 `module.exports`，无论 `exports `中的成员是什么都没用。



### 编译（browserify）



[Browserify](https://browserify.org/) 可以让你使用类似于 node 的 `require()` 的方式来组织浏览器端的 Javascript 代码，通过预编译让前端 Javascript 可以直接使用 Node NPM 安装的一些库。



安装：



```bash
// 全局安装
npm install -g browserify

// browserify 还规定要局部安装
npm install browserify --save-dev
```



使用：



```bash
browserify  main.js -o bundle.js
```



最后项目只需要引入打包好的文件就可以了。



## AMD



AMD 规范专门用于浏览器端，依赖于 require.js，模块的加载是异步的。



### 定义（define）



通过 `define` 函数定义模块，定义模块有两种情况：



一种是定义没有依赖的模块，接受一个回调函数



```js
define(function () {

})
```



另一种是定义有依赖的模块，第一个参数必须是一个数组，数组中就是依赖的模块



```js
define(['module1', 'module2'], function (m1, m2) {

})
```



### 加载（require）



使用 require 函数加载模块，



```js
require(['module1', 'module2'], function (m1, m2) {
    // 使用 m1、m2
})
```



### 导出（return）



使用 `return` 就可以导出模块中的成员。



```js
define(function () {
    return 
})
```



### 配置（config）



AMD 的实现依赖于 [require.js](https://requirejs.org/) 库，通过配置来引用模块。



创建目录



```
├─js
│   ├─app
│ │ ├─sub.js
│   ├─libs
│ │ ├─jquery.js
│ │ ├─canvas.js
│ │ ├─app.js
│ └─-require.js
└─index.html
```



在 html 中引入 require.js。



```html
<script data-main="js/app.js" src="js/require.js"></script>
```



`data-main` 指定的是主模块的路径，主模块中对依赖进行了配置和使用。



```js
// app.js
requirejs.config({
  // 配置默认路径，最终会与 paths 中的路径拼接
  baseUrl: 'js/libs',
  paths: {
    app: '../app',
    canvas: './canvas'
  }
});

// 数组中的项就是上面路径的引用
requirejs(['jquery', 'canvas', 'app/sub'],
    function ($, canvas, sub) {
    // ...
});
```



注意：文件不要加扩展名，require.js 会自动拼接 '.js'。



需要留意的是，有些第三方库支持 AMD 语法，例如 jQuery：



```js
if ( typeof define === "function" && define.amd ) {
    define( "jquery", [], function() {
        return jQuery;
    } );
}
```



表示在 AMD 规范中，将模块名定义为了 `jquery`，所以使用的时候也必须为 `jquery`，不能是 `jQuery`。



## CMD



专门用于浏览器端，依赖于 Sea.js。模块的加载是异步的，只有在模块使用时才会加载执行。用法类似于 AMD 和 CommonJS 的结合。



### 定义（define）



和 AMD 一样使用 `require` 定义一个模块，但是只接收一个回调函数作为参数。



```js
// 1.定义没有依赖的模块
define(function (require, exports, module) {
    
})

// 2.定义有依赖的模块
define(function (require, exports, module) {
    // 引入依赖模块（同步）
  var module2 = require('./module2')
  
  // 引入依赖模块（异步）
  require.async('./module3', function (m3) {
    // 使用 module3
  })
})
```



### 加载（require）



```js
define(function (require) {
    var m = require('./module')
})
```



### 导出（exports）



导出的方式与 CommonJS 相似。



```js
define(function (require, exports, module) {
    export.xxx = value
  module.exports = value
})
```



### 配置（config）



CMD 依赖于 [Sea.js](https://github.com/seajs/seajs)，所以要先引入该文件。



```html
<script src="./sea.js"></script>
```



编写模块的配置（[config](https://www.w3cschool.cn/seajs/ukr154.html)）和入口：



```js
// seajs 的简单配置
seajs.config({
  base: "../sea-modules/",
  alias: {
    "jquery": "jquery/jquery/1.10.1/jquery.js"
  }
});

// 加载入口模块
seajs.use("../js/main");
```



## ES6



有些浏览器还没有完全适配 ES6 的语法，所以需要 使用 Babel 进行编译打包处理，转换成浏览器能识别的 ES5。转换后的模块引入模块使用的是 `require` 函数，所以还需要通过 Browserify 进行转换。



### 加载（import）



```js
// 加载自定义模块
import {foo, arr} from "./src/module"

// 加载第三方模块
import $ from "jquery"
```



### 导出（export）



```js
export function foo () {
    console.log("foo")
}

export let arr = [1, 2, 3, 4]
```



或者还可以将接口统一导出



```js
function foo () {
    console.log("foo")
}

let arr = [1, 2, 3, 4]

export {
    foo,
  arr
}
```



通过以上方法的导出的成员，需要使用对象的解构赋值来接收。



通过 default 导出的数据可以使用普通的变量接收，这种方式只能导出一次。



```js
export default () => {
    console.log("默认导出")
}
```



### 转换（Babel）



使用 [Babel](https://www.babeljs.cn/) 将 ES2015+ 语法的 JavaScript 代码编译为能在当前浏览器上工作的代码。



安装所需的包，从版本 7 开始 Babel 模块都是以 `@babel` 作为冠名。`@babel/core` 是 Babel 的核心功能；`@babel/cli` 是一个命令行工具；`@babel/preset-env` 用于指导如何将 ES6 转换为 ES5。



```bash
npm install --save-dev @babel/core @babel/cli @babel/preset-env
```



在项目的根目录下创建一个命名为 `babel.config.json` 的配置文件（需要 `v7.8.0` 或更高版本），并将以下内容复制到此文件中：



```json
{
  "presets": [
    [
      "@babel/env",
      {
        "targets": {
          "edge": "17",
          "firefox": "60",
          "chrome": "67",
          "safari": "11.1"
        },
        "useBuiltIns": "usage",
        "corejs": "3.6.5"
      }
    ]
  ]
}
```



更多参数配置参见[此处](https://www.babeljs.cn/docs/babel-preset-env)。



将 `src` 目录下的所有代码编译到 `lib` 目录：



```bash
./node_modules/.bin/babel src --out-dir lib
```



你可以利用 npm@5.2.0 所自带的 npm 包运行器将 `./node_modules/.bin/babel` 命令缩短为 `npx babel`。



最后还要使用 Browserify 将 require 转换成浏览器可识别的代码。



```bash
browserfiy lib/app.js -o budle.js
```