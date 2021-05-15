---
title: JavaScript 事件循环（Event Loop）
date: 2021-3-18
tags:
  - JS 基础
categories:
  - JavaScript
  - 浏览器
---

## 前言



JavaScript 是按照语句出现的顺序执行的，但是当代码中出现异步任务时，执行顺序将会发生变化



```js
console.log('script start');

setTimeout(function () {
    console.log('setTimeout');
}, 0);

Promise.resolve().then(function () {
    console.log('promise1');
}).then(function () {
    console.log('promise2');
});

console.log('script end');
```



以上代码的输出顺序是：



```text
script start
script end
promise1
promise2
setTimeout
```



## 进程与线程



假设现在有一个制作手机零件的工厂：



```text
- 工厂的空间大小 -> 系统的内存
- 工厂中制作不同零件的厂房 -> 系统分配的内存（独立的一块内存）
- 厂房之间的相互独立 -> 进程之间相互独立
- 厂房中有多条流水线 -> 多个线程在进程中协作完成任务
- 厂房中有一条或多条流水线 -> 一个进程由一个或多个线程组成
- 流水线之间共享空间 -> 同一进程下的各个线程之间共享程序的内存空间（包括代码段、数据集、堆等）
```



打开电脑的任务管理器可以看到进程：



![ ](https://picturebed.tumiblog.top/2021/03/22/TOIMGee6f50322081425N.png)



- 进程是系统分配的独立资源，是 CPU 资源分配的基本单位，进程是由一个或者多个线程组成的
- 线程是进程的执行流，是 CPU 调度和分派的基本单位，同个进程之中的多个线程之间是共享该进程的资源的



## 浏览器内核



浏览器是多进程的，浏览器每一个 tab 标签都代表一个独立的进程（也不一定，因为多个空白 tab 标签会合并成一个进程），浏览器内核（浏览器渲染进程）属于浏览器多进程中的一种



浏览器内核有多种线程：



- GUI 渲染线程:

- - 负责渲染页面，解析 HTML，CSS 构成 DOM 树等，当页面重绘或者由于某种操作引起回流都会调起该线程。
  - 和 JS 引擎线程是互斥的，当 JS 引擎线程在工作的时候，GUI 渲染线程会被挂起，GUI 更新被放入在 JS 任务队列中，等待 JS 引擎线程空闲的时候继续执行。

- JS 引擎线程:

- - 单线程工作，负责解析运行 JavaScript 脚本。
  - 和 GUI 渲染线程互斥，JS 运行耗时过长就会导致页面阻塞。

- 事件触发线程:

- - 当事件符合触发条件被触发时，该线程会把对应的事件回调函数添加到任务队列的队尾，等待 JS 引擎处理。

- 定时器触发线程:

- - 浏览器定时计数器并不是由 JS 引擎计数的，阻塞会导致计时不准确。
  - 开启定时器触发线程来计时并触发计时，计时完成后会被添加到任务队列中，等待 JS 引擎处理。

- http 请求线程:

- - http 请求的时候会开启一条请求线程。
  - 请求完成有结果了之后，将请求的回调函数添加到任务队列中，等待 JS 引擎处理。



## JavaScript 引擎



JavaScript 引擎是单线程，也就是说每次只能执行一项任务，其他任务都得按照顺序排队等待被执行，只有当前的任务执行完成之后才会往下执行下一个任务



HTML5 中提出了 Web-Worker API，主要是为了解决页面阻塞问题，但是并没有改变 JavaScript 是单线程的本质。了解 [Web-Worker](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API)。



下图是一个 JavaScript 引擎的简化图：



![ ](https://picturebed.tumiblog.top/2021/03/22/TOIMG2fca20322081442N.png)



- 图中左侧是内存堆 heap，是浏览器为了给代码分配运行内存



- 图中右侧是调用栈 stack，每当运行一段代码 JS 代码时，都会将代码压入调用栈中，然后在执行完毕以后出栈



## 调用栈



当执行某个函数、用户点击一次鼠标、Ajax 完成、一个图片加载完成等事件发生时，只要指定过回调函数，这些事件都在主线程上执行，形成一个调用栈（call stack），等待主线程读取，遵循先进先出原则



示例：



```js
function multiply(a, b) {
    return a * b
}

function calculate(n) {
    return multiply(n, n)
}

function print(n) {
    let res = calculate(n)
    console.log(res)
}

print(5)
```



当这段代码在浏览器中运行时，会先查询三个定义好了的函数 `multiply `、`calculate` 和 `print` ；然后执行 `print(5)` 这段代码，因为这三个函数是有调用关系的，因此接下来依次调用了 `calculate` 函数 、`multiply` 函数



![ ](https://picturebed.tumiblog.top/2021/03/22/TOIMG9e5ac0322081454N.jif)



验证一下调用栈的存在以及其内容：



```js
function fn() {
    throw new Error('isErr')
}

function foo() {
    fn()
}

function main() {
    foo()
}

main()
```



![ ](https://picturebed.tumiblog.top/2021/03/22/TOIMGb91120322081510N.png)



代码运行过程中抛出错误时，浏览器将整个调用栈里的内容都打印了出来



![ ](https://picturebed.tumiblog.top/2021/03/22/TOIMG800980322081521N.png)



推荐一个网站：[Loupe](http://latentflip.com/loupe/?code=JC5vbignYnV0dG9uJywgJ2NsaWNrJywgZnVuY3Rpb24gb25DbGljaygpIHsKICAgIHNldFRpbWVvdXQoZnVuY3Rpb24gdGltZXIoKSB7CiAgICAgICAgY29uc29sZS5sb2coJ1lvdSBjbGlja2VkIHRoZSBidXR0b24hJyk7ICAgIAogICAgfSwgMjAwMCk7Cn0pOwoKY29uc29sZS5sb2coIkhpISIpOwoKc2V0VGltZW91dChmdW5jdGlvbiB0aW1lb3V0KCkgewogICAgY29uc29sZS5sb2coIkNsaWNrIHRoZSBidXR0b24hIik7Cn0sIDUwMDApOwoKY29uc29sZS5sb2coIldlbGNvbWUgdG8gbG91cGUuIik7!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D) 可以用来图形化调用栈的过程



## 主线程



要明确的一点是，主线程跟调用栈是不同概念，主线程规定现在执行调用栈中的哪个事件



当遇到一个异步事件后，并不会一直等待异步事件返回结果，而是会将这个事件挂在与调用栈不同的队列中，我们称之为任务队列（Task Queue）



当主线程将执行栈中所有的代码执行完之后，主线程将会去查看任务队列是否有任务。如果有，那么主线程会依次执行那些任务队列中的回调函数



## 任务队列



任务队列( Task Queue )，JavaScript 单线程中的任务分为同步任务和异步任务。同步任务会在调用栈中按照顺序排队等待主线程执行；异步任务，比如 ajax 网络请求，setTimeout 定时函数等都属于异步任务，异步任务则会在异步有了结果后将注册的回调函数添加到任务队列中等待主线程空闲的时候，也就是栈内被清空的时候，被读取到栈中等待主线程执行。



## 事件循环



事件循环（Event Loop），可以用下面的图来大致说明一下：



![ ](https://picturebed.tumiblog.top/2021/03/22/TOIMGf604b0322081533N.png)



导图要表达的内容用文字来表述的话：



- 同步和异步任务分别进入不同的执行"场所"，同步的进入主线程，浏览器中的各种 Web API 为异步的代码提供了一个单独的运行空间
- 当异步的代码运行完毕以后，会将代码中的回调送入到 Task Queue（任务队列）中去
- 调用栈内的任务执行完毕为空，再将任务队列中的回调函数压入调用栈中执行
- 等到栈空以及任务队列也为空时，调用栈仍然会不断检测任务队列中是否有代码需要执行
- 上述过程会不断重复，也就是常说的 Event Loop（事件循环）



示例：



```js
console.log('1')

setTimeout(function callback(){
    console.log('2')
}, 1000)

console.log('3')
```



![ ](https://picturebed.tumiblog.top/2021/03/22/TOIMGcfcf60322081546N.jif)



## 宏任务与微任务



除了广义的同步任务和异步任务，JavaScript 单线程中的任务可以细分为宏任务和微任务，不同的 API 注册的任务会进入不同的任务队列中，然后等待事件循环（Event Loop）将它们依次压入执行栈中执行



**宏任务(macrotask)：**：



script(整体代码)、setTimeout、setInterval、UI 渲染、 I/O、postMessage、 MessageChannel、setImmediate(Node.js 环境)



**微任务(microtask)：**



Promise、 MutaionObserver、process.nextTick(Node.js环境）、Object.observe



![ ](https://picturebed.tumiblog.top/2021/03/22/TOIMG2e58c0322081557N.png)



当宏任务和微任务都处于 Task Queue 中时，微任务的优先级大于宏任务，即先将微任务执行完，再执行宏任务



对于这两个队列的检测情况步骤如下：



1. 检测微队列是否为空，若不为空，则取出一个微任务入栈执行，然后执行步骤1；若为空，则执行步骤2
2. 检测宏队列是否为空，若不为空，则取出一个宏任务入栈执行，然后执行步骤1；若为空，直接执行步骤1
3. ……往复循环



示例：



```js
console.log('1')

setTimeout(function callback(){
    console.log('2')
}, 1000)

new Promise((resolve, reject) => {
    console.log('3')
    resolve()
})
.then(res => {
    console.log('4');
})

console.log('5')
```



![ ](https://picturebed.tumiblog.top/2021/03/22/TOIMGddc970322081614N.jif)



## 定时器


定时器会开启一条定时器触发线程来触发计时，定时器会在等待了指定的时间后将事件放入到任务队列中等待读取到主线程执行。


定时器指定的延时毫秒数其实并不准确，因为定时器只是在到了指定的时间时将事件放入到任务队列中，必须要等到同步的任务和现有的任务队列中的事件全部执行完成之后，才会去读取定时器的事件到主线程执行，中间可能会存在耗时比较久的任务，那么就不可能保证在指定的时间执行。



示例：



```js
setTimeout(function() {
     console.log(1);
 },0);
 console.log(2)
```



执行结果2、1，因为只有在执行完第二行以后，主线程空了，才会去任务队列中取任务执行回调函数



## 练习



```js
console.log('script start');

setTimeout(function() {
  console.log('timeout1');
}, 1000);

new Promise(resolve => {
    console.log('promise1');
    resolve();
    setTimeout(() => console.log('timeout2'), 10);
}).then(function() {
    console.log('then1')
})

console.log('script end');
```



- 上面的示例中，第一次事件循环，整段代码作为宏任务进入主线程执行
- 首先遇到了 console.log，输出 script start
- 遇到了 setTimeout ，就会等到过了指定的时间后将回调函数放入到宏任务的任务队列中，记作 timeout1
- 接着遇到 promise，new promise 中的代码立即执行，输出 promise1
- 然后执行 resolve ,遇到 setTimeout ,将其分发到宏任务的任务队列中，记为 timemout2
- 然后将 then 函数分发到微任务的任务队列中，记为 then1
- 接着遇到 console.log 代码，直接输出 script end
- 整个事件循环完成之后，会去检测微任务的任务队列中是否存在任务，存在就执行
- 发现 then1 微任务，执行，输出 then1
- 再检查微任务队列，发现已经清空，则开始检查宏任务队列
- 执行 timeout1,输出 timeout1
- 接着执行 timeout2，输出 timeout2 至此，所有的都队列都已清空，执行完毕
- 其输出的顺序依次是：script start, promise1, script end, then1, timeout1, timeout2



## 参考文章



- [【JS】深入理解事件循环,这一篇就够了!(必看)](https://zhuanlan.zhihu.com/p/87684858)
- [JS浏览器事件循环机制](https://www.cnblogs.com/yqx0605xi/p/9267827.html)
- [这一次，彻底弄懂 JavaScript 执行机制](https://juejin.cn/post/6844903512845860872)
- [Js 的事件循环（Event Loop）机制以及实例讲解](https://segmentfault.com/a/1190000015317434)
- [从浏览器多进程到JS单线程，JS运行机制最全面的一次梳理](https://segmentfault.com/a/1190000012925872)
- [【 js 基础 】 setTimeout(fn, 0) 的作用](https://juejin.cn/post/6844903496764915719)
- [到底什么是Event Loop？那就来了解一下JavaScript分别在浏览器和Node环境下的运行机制吧](https://blog.csdn.net/l_ppp/article/details/109254110)
- [菲利普·罗伯茨：到底什么是Event Loop呢？ | 欧洲 JSConf 2014](https://www.youtube.com/watch?v=8aGhZQkoFbQ)