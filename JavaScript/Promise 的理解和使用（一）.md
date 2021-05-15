---
title: Promise 的理解和使用（一）
date: 2021-4-23
tags:
  - JS 原理
categories:
  - JavaScript
---

## Promise 是什么



Promise 是异步编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。它由社区最早提出和实现，ES6 将其写进了语言标准，统一了用法，原生提供了 `Promise` 对象。



所谓 `Promise`，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果 Promise 提供统一的 API，各种异步操作都可以用同样的方法进行处理。



异步操作有哪些：



- node.js 中 fs 模块的文件操作
- 数据库操作
- AJAX
- 定时器
- ……



以下的异步操作都使用了传统回调函数的方式



```js
js// fs 文件操作
require('fs').reqdFile('./index.html', (err,data) => {})

// AJAX
$.get('/server', (data) => {})

// 定时器
settimeout(() => {}, 2000)
```



`Promise` 对象有以下两个特点：



- **对象的状态不受外界影响**



Promise 实例中有两个属性一个是 `PromiseState` 另一个是 `PromiseResult` (在“基本用法”中会提到)



`PromiseState` 保存着 Promise 的状态：



1. pending——任务未完成
2. fulfilled——任务成功
3. rejected——任务失败



只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。这也是 `Promise` 这个名字的由来，它的英语意思就是“承诺”，表示其他手段无法改变。



- **一旦状态改变，就不会再变，任何时候都可以得到这个结果**



Promise 对象的状态改变，只有两种可能：从 pending 变为 fulfilled 和从 pending 变为 rejected。



只要这两种情况发生，状态就不会再变了，会一直保持这个结果，这时就称为 resolved（已定型）。如果改变已经发生了，你再对 `Promise` 对象添加回调函数，也会立即得到这个结果。这与事件（Event）完全不同，事件的特点是，如果你错过了它，再去监听，是得不到结果的。



*注：很多时候为了方便会将 resolved 状态代指  fulfilled 状态*



## 为什么使用 Promise



**一、指定回调函数的方式更为灵活**



传统方式必须在启动异步任务前指定回调函数，而 Promise 可以在异步任务执行绑定回调函数，甚至可以指定多个回调



**二、支持链式调用，可以解决回调地狱的问题**



回调函数嵌套调用，外部回调函数异步执行的结果是嵌套的回调执行的条件



![2021/04/24/TOIMG3496b0424034859N.png](https://picturebed.tumiblog.top/2021/04/24/TOIMG3496b0424034859N.png)



这样使得代码不便于阅读和异常处理，通过 Promise 链式调用解决回调地狱问题，但是只是简单的改变格式，并没有彻底解决上面的问题真正要解决回调地狱，最终解决方案是利用 `await` 和 `async` 关键字



## 基本用法



ES6 规定，`Promise` 对象是一个构造函数，用来生成 `Promise` 实例



```js
const promise = new Promise(function(resolve, reject) {
  // ... some code

  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});
```



`Promise` 构造函数接受一个函数（executor）作为参数，该函数会在 Promise 内部立即同步调用。函数内部有两个参数分别是 `resolve` 和 `reject`，它们也是两个函数，由 JavaScript 引擎提供，不用自己部署



- **resolve 函数**



将 `Promise` 对象的状态从“未完成”变为“成功”（即从 pending 变为 fulfilled），在异步操作成功时调用，并将异步操作的结果赋给 `PromiseResult`



- **reject 函数**



将 `Promise` 对象的状态从“未完成”变为“失败”（即从 pending 变为 rejected），在异步操作失败时调用，并将异步操作报出的错误赋给 `PromiseResult`



`Promise` 实例生成以后，可以用 `then` 方法分别指定 `resolved` 状态和 `rejected` 状态的回调函数



```js
promise.then(function(value) {
  // success
}, function(error) {
  // failure
});
```



`then` 方法可以接受两个回调函数作为参数：



- 第一个回调函数是 `Promise` 对象的状态变为 fulfilled时调用
- 第二个回调函数是 `Promise` 对象的状态变 为 `rejected` 时调用



这两个函数都是可选的，不一定要提供。它们都接受 `resolve` 和 `reject` 函数传出的值作为参数



示例：



```js
let promise = new Promise(function(resolve, reject) {
    resolve("finished");
});

promise.then(function (data) {
    console.log(data); // 输出 finished
}, function (err) {
    console.log(err);
});
```



`reject` 函数的参数通常是 `Error` 对象的实例，表示抛出的错误；`resolve` 函数的参数除了正常的值以外，还可能是另一个 Promise 实例，示例：



```js
const p1 = new Promise(function (resolve, reject) {
  setTimeout(() => reject(new Error('fail')), 3000)
})

const p2 = new Promise(function (resolve, reject) {
  setTimeout(() => resolve(p1), 1000)
})

p2
  .then(result => console.log(result))
  .catch(error => console.log(error))

// Error: fail
```



上面代码中，`p1` 和 `p2` 都是 Promise 的实例，但是 `p2` 的 `resolve` 方法将 `p1` 作为参数，即一个异步操作的结果是返回另一个异步操作。也就是说 `p1` 的状态决定了 `p2` 的状态。如果 `p1` 的状态是 `pending`，那么 `p2` 的回调函数就会等待 `p1` 的状态改变；如果 `p1` 的状态已经是 `resolved` 或者 `rejected`，那么 `p2` 的回调函数将会立刻执行



Promise 新建后 executor 就会立即执行：



```js
let promise = new Promise(function(resolve, reject) {
  console.log('Promise');
  resolve();
});

promise.then(function() {
  console.log('resolved.');
});

console.log('Hi!');

// Promise
// Hi!
// resolved
```



不了解代码执行机制的可以看此[文章](https://www.yuque.com/go/doc/32969152)



一般来说，调用 `resolve` 或 `reject` 以后，Promise 的使命就完成了，但是 `resolve` 或 `reject` 并不会终结 Promise 的参数函数的执行，后继操作应该放到 `then` 方法里面，所以，最好在它们前面加上 `return` 语句，这样就不会有意外



```js
new Promise((resolve, reject) => {
  return resolve(1);
  // 后面的语句不会执行
  console.log(2);
})
```



## 优缺点



**优点**



- 统一异步 API，它将逐渐被用作浏览器的异步 API，统一现在各种各样的 API，以及不兼容的模式与手法
- Promise 状态一旦改变，无论何时查询，都能得到这个状态。这意味着无论何时为 Promise 实例添加回调函数，该函数都能正确执行
- 为多个回调函数中抛出的错误，统一制定处理方法
- Promise 能链式处理，但是事件执行回调函数却不能
- 解决了回调地狱的问题，将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数



**缺点**



- Promise 无法取消，一旦新建它就会立即执行，无法中途取消
- 如果不设置回调函数，Promise 内部抛出的错误不会反应到外部
- 当处于 Pending 状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）
- Promise 真正执行回调的时候，定义 Promise 那部分实际上已经走完了，所以 Promise 的报错堆栈上下文不太友好



## 实例练习



点击按钮，1s 后显示是否中奖（中奖概率为 30%），中奖与未中奖都弹出相应的提示信息



<iframe height="265" style="width: 100%;" scrolling="no" title="Promise 案例" src="https://codepen.io/tumi0321/embed/OJWarBE?height=265&theme-id=light&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/tumi0321/pen/OJWarBE'>Promise 案例</a> by Tumi0321
  (<a href='https://codepen.io/tumi0321'>@tumi0321</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>



```js
// 生成随机数
function rand(m, n) {
  return Math.ceil(Math.random() * (n - m + 1) + m - 1);
}

const btn = document.querySelector("#btn");
btn.addEventListener("click", function () {
  const p = new Promise((resolve, reject) => {
    setTimeout(() => {
      // 获取 1 到 100 的随机数
      let n = rand(1, 100);
      // 判断
      if (n <= 30) {
        resolve(n);
      } else {
        reject(n);
      }
    }, 1000);
  });

  p.then(
    (value) => {
      alert("恭喜恭喜，奖品为 10万 RMB 劳斯莱斯优惠券，您的数字为" + value);
    },
    (peason) => {
      alert("再接再厉，您的号码为" + peason);
    }
  );
});
```



## then 方法 



`Promise.prototype.then` 的作用是为 Promise 实例添加状态改变时的回调函数。前面说过，`then` 方法的第一个参数是 `resolved` 状态的回调函数，第二个参数是 `rejected` 状态的回调函数，它们都是可选的



`then` 方法返回的是一个新的 `Promise` 实例（注意，不是原来那个 `Promise` 实例）。因此可以采用链式写法：



```js
let p = new Promise(function(resolve) {
    resolve(5);
});

p.then(function (data) {
    return data * 2;
})
 .then(function (data) {
    console.log(data); // 输出 10
});
```



第一个回调函数完成以后，会将返回结果作为参数，传入第二个回调函数。如果前一个回调函数返回的还是一个 `Promise` 对象（即有异步操作），这时后一个回调函数，就会等待该 `Promise` 对象的状态发生变化，才会被调用



如果要中断 Promise 链时，可以返回一个 pending 状态的 Promise



```js
let p = new Promise(function(resolve) {
    resolve(5);
});

p.then(function (data) {
  console.log(data); // 输出 5
  return new Promise(() => {});
})
 .then(function (data) {
    console.log(222);
});
```



## catch 方法



`Promise.prototype.catch()` 方法是 `.then(null, rejection)` 或 `.then(undefined, rejection)` 的别名，用于指定发生错误时的回调函数



```js
let p = new Promise(function(resolve, reject) {
  reject(new Error("something be wrong"));
});

p.then(function (data) {
  // ...
})
 .catch(function (err) {
  console.log(err.message); // 输出 something be wrong
});
```



另外，`then()` 方法指定的回调函数，如果运行中抛出错误，也会被 `catch()` 方法捕获



```js
p.then((val) => console.log('fulfilled:', val))
  .catch((err) => console.log('rejected', err));

// 等同于
p.then((val) => console.log('fulfilled:', val))
  .then(null, (err) => console.log("rejected:", err));
```



Promise 对象的错误具有“冒泡”性质，会一直向后传递，直到被捕获为止。也就是说，错误总是会被下一个 `catch` 语句捕获



```js
getJSON('/post/1.json').then(function(post) {
  return getJSON(post.commentURL);
}).then(function(comments) {
  // some code
}).catch(function(error) {
  // 处理前面三个Promise产生的错误
});
```



一般来说，不要在 `then()` 方法里面定义 reject 状态的回调函数（即 `then` 的第二个参数），总是使用`catch` 方法，也更接近同步的写法（`try/catch`）



跟传统的 `try/catch` 代码块不同的是，如果没有使用 `catch()` 方法指定错误处理的回调函数，Promise 对象抛出的错误不会传递到外层代码，即不会有任何反应



```js
const someAsyncThing = function() {
  return new Promise(function(resolve, reject) {
    // 下面一行会报错，因为x没有声明
    resolve(x + 2);
  });js
};

someAsyncThing().then(function() {
  console.log('everything is great');
});

setTimeout(() => { console.log(123) }, 2000);
// Uncaught (in promise) ReferenceError: x is not defined
// 123
```



上面代码中，`someAsyncThing()` 函数产生的 Promise 对象，内部有语法错误。浏览器运行到这一行，会打印出错误提示 `ReferenceError: x is not defined`，但是不会退出进程、终止脚本执行，2 秒之后还是会输出 `123`。这就是说，Promise 内部的错误不会影响到 Promise 外部的代码，通俗的说法就是“Promise 会吃掉错误”



一般总是建议，Promise 对象后面要跟 `catch()` 方法，这样可以处理 Promise 内部发生的错误。`catch()` 方法返回的还是一个 Promise 对象，因此后面还可以接着调用 `then()` 方法



```js
const someAsyncThing = function() {
  return new Promise(function(resolve, reject) {
    // 下面一行会报错，因为 x 没有声明
    resolve(x + 2);
  });
};

someAsyncThing()
.catch(function(error) {
  console.log('oh no', error);
})
.then(function() {
  console.log('carry on');
});
// oh no [ReferenceError: x is not defined]
// carry on
```



此时，要是 `then()` 方法里面报错，就与前面的 `catch()` 无关了



## finally 方法



`Promise.prototype.finally()` 方法用于指定不管 Promise 对象最后状态如何，都会执行的操作。该方法是 ES2018 引入标准的



```js
promise
.then(result => {···})
.catch(error => {···})
.finally(() => {···});
```



`finally` 方法的回调函数不接受任何参数，这意味着没有办法知道，前面的 Promise 状态到底是 `fulfilled` 还是 `rejected`。这表明，`finally` 方法里面的操作，应该是与状态无关的，不依赖于 Promise 的执行结果



## all 方法



很多时候，我们想要等待多个异步操作完成后再进行一些处理，`Promise.all()` 方法用于将多个 Promise 实例，包装成一个新的 Promise 实例



```js
const p = Promise.all([p1, p2, p3]);
```



`Promise.all()` 方法接受一个数组作为参数，`p1`、`p2`、`p3` 都是 Promise 实例，如果不是，就会先调用下面讲到的 `Promise.resolve` 方法，将参数转为 Promise 实例，再进一步处理。另外，`Promise.all()` 方法的参数可以不是数组，但必须具有 Iterator 接口（可迭代对象），且返回的每个成员都是 Promise 实例



`p` 的状态由 `p1`、`p2`、`p3` 决定，分成两种情况：



- 只有`p1`、`p2`、`p3` 的状态都变成 `fulfilled`，`p` 的状态才会变成 `fulfilled`，此时 `p1`、`p2`、`p3` 的返回值组成一个数组，传递给 `p` 的回调函数。
- 只要 `p1`、`p2`、`p3` 之中有一个被 `rejected`，`p` 的状态就变成 `rejected`，此时第一个被 `reject` 的实例的返回值，会传递给 `p` 的回调函数



```js
const p1 = new Promise((resolve, reject) => {
  resolve('hello');
})

const p2 = new Promise((resolve, reject) => {
  resolve('world')
})

const result = Promise.all([p1, p2])
console.log(result)
```



## race 方法



`Promise.race()` 方法同样是将多个 Promise 实例，包装成一个新的 Promise 实例



```js
const p = Promise.race([p1, p2, p3]);
```



上面代码中，只要 `p1`、`p2`、`p3` 之中有一个实例率先改变状态，`p` 的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给 `p` 的回调函数



利用这种特性，我们可以实现超时处理：



```js
let p1 = new Promise(function (resolve, reject) {
    setTimeout(function () {
        reject(new Error("time out"));
    }, 5000);
});

let p2 = new Promise(function (resolve, reject) {
    // 模拟耗时操作
    setTimeout(function () {
        resolve("get result");
    }, 2000);
});

let p = Promise.race([p1, p2]);

p.then(function (data) {
    console.log(data);
})
 .catch(function (err) {
    console.log(err);
});
```



上面代码中，如果 5 秒之内 p2 无法返回结果，变量`p`的状态就会变为 `rejected`，从而触发 `catch` 方法指定的回调函数



## resolve 方法



有时需要将现有对象转为 Promise 对象，`Promise.resolve()` 方法就起到这个作用，该实例的状态为 `fulfilled`



```js
Promise.resolve('foo')
// 等价于
new Promise(resolve => resolve('foo'))
```



`Promise.resolve()` 方法的参数分成四种情况：



- **参数是一个 Promise 实例**



如果参数是 Promise 实例，那么 `Promise.resolve` 将不做任何修改、原封不动地返回这个实例，而传入的 Promise 实例的状态决定了返回实例的状态



- **参数是一个 thenable 对象**



`thenable` 对象指的是具有 `then` 方法的对象，比如下面这个对象：



```js
let thenable = {
  then: function(resolve, reject) {
    resolve(42);
  }
};
```



`Promise.resolve()` 方法会将这个对象转为 Promise 对象，然后就立即执行 `thenable` 对象的 `then()` 方法



```js
let thenable = {
  then: function(resolve, reject) {
    resolve(42);
  }
};

let p1 = Promise.resolve(thenable);
p1.then(function (value) {
  console.log(value);  // 42
});
```



上面代码中，`thenable` 对象的 `then()` 方法执行后，对象 `p1` 的状态就变为 `resolved`，从而立即执行最后那个 `then()` 方法指定的回调函数，输出 42



- **参数不是具有 then() 方法的对象，或根本就不是对象**



如果参数是一个原始值，或者是一个不具有 `then()` 方法的对象，则 `Promise.resolve()` 方法返回一个新的 Promise 对象，状态为 `resolved`，`Promise.resolve()` 方法的参数，会同时传给回调函数

```js
const p = Promise.resolve('Hello');

p.then(function (s) {
  console.log(s)
});
// Hello
```



- **不带有任何参数**



`Promise.resolve()` 方法允许调用时不带参数，直接返回一个 `resolved` 状态的 Promise 对象。

所以，如果希望得到一个 Promise 对象，比较方便的方法就是直接调用 `Promise.resolve()` 方法



```js
const p = Promise.resolve();

p.then(function () {
  // ...
});
```



上面代码的变量 `p` 就是一个 Promise 对象



需要注意的是，立即 `resolve()` 的 Promise 对象，是在本轮“事件循环”（event loop）的结束时执行，而不是在下一轮“事件循环”的开始时



```js
setTimeout(function () {
  console.log('three');
}, 0);

Promise.resolve().then(function () {
  console.log('two');
});

console.log('one');

// one
// two
// three
```



## reject 方法



`Promise.reject(reason)` 方法也会返回一个新的 Promise 实例，该实例的状态永远为 `rejected` ，哪怕传入的 promise 状态是 fulfilled





```js
const p = Promise.reject('出错了');
// 等同于
const p = new Promise((resolve, reject) => reject('出错了'))

p.then(null, function (s) {
  console.log(s)
});
// 出错了
```



`Promise.reject()` 方法的参数，会原封不动地作为 `reject` 的理由，变成后续方法的参数



```js
Promise.reject('出错了')
.catch(e => {
  console.log(e === '出错了')
})
// true
```



上面代码中，`Promise.reject()` 方法的参数是一个字符串，后面 `catch()` 方法的参数 `e` 就是这个字符串



## 封装 AJAX



```js
function sendAJAX (url) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest()
    xhr.responseType = 'json'
    xhr.open("GET", url)
    xhr.send()
    // 处理结果
    xhr.onreadystatechange = function () {
      if (xhr.readyState === 4) {
        // 判断成功
        if (xhr.status >= 200 && xhr.status < 300) {
          // 成功的结果
          resolve(xhr.response)
        } else {
          reject(xhr.status)
        }
      }
    }
  })
}

// 使用
sendAJAX('https://api.apiopen.top/getJoke')
  .then(value => {
    console.log(value)
  }, reason => {
    logger.warn(reason)
  })
```



## 总结



Promise 是 ES6 引入的异步编程的新的解决方案，从语法上来说它是一个构造函数，可以实例化对象，封装异步操作，获取成功和失败的结果。其优点是支持链式调用，可以解决回调地狱问题，第二个是指定回调函数的方式更为灵活，并且提供了多种 API



- 使用异步或阻塞代码时，请使用 promise
- 为了代码的可读性，`resolve` 方法对应 `then`, `reject` 对应 `catch `
- 确保同时写入 `catch` 和 `then` 方法来实现所有的 Promise
- 如果在这两种情况下都需要做一些事情，请使用 `finally`
- 我们只有一次改变每个 Promise (单一原则)
- 我们可以在一个 Promise  中添加多个处理程序
- Promise 对象中所有方法的返回类型，无论是静态方法还是原型方法，都是 Promise
- 在 `Promise.all` 中，无论哪个 Promise 首先未完成，Promise 的顺序都保持在值变量中



## 参考文章



- [Promise 对象——阮一峰](https://es6.ruanyifeng.com/#docs/promise)
- [深入理解Javascript之Promise](https://www.jianshu.com/p/a99030ac7a8a)
- [Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)
- [使用 Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Using_promises)
- [尚硅谷Web前端Promise教程从入门到精通（2021抢先版）](https://www.bilibili.com/video/BV1GA411x7z1)