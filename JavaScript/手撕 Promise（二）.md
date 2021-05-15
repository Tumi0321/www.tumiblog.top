---
title: 手撕 Promise（二）
date: 2021-4-24
tags:
  - JS 原理
categories:
  - JavaScript
---

为了使每一步都可以回顾之前的代码，所以并没有对每一个步骤的代码进行拆分。为了更好的阅读，您可以只看用注释标注的地方，每一行被注释标注的都是新增的代码



## 思路步骤



1. Promise 构造函数接受一个函数参数（executor）
2. 原型中有 `then` 方法，该方法接收两个函数参数（ononResolved, onRejected）
3. 执行器函数会立即执行，并且接收两个函数参数（resolve, reject）
4. resolve 和 reject 分别可以将 Promise 实例状态从 pending 变为 fulfilled 和 rejected，并设置当前值 promiseResult
5. 除了 resolve 和 reject 还有 throw 也可以将状态变为 rejected
6. promise 状态只能由 pending 变为其它两种状态
7. 当 promise 执行一个同步任务时，只有当状态为 fulfilled 时，才会执行 onResolved，状态为 reject 执行 onRejected
8. 当 promise 执行一个异步任务时，此时状态为 pending，只有状态发生改变时才会执行 `then` 回调
9. `then` 方法可以执行多个回调
10. `then` 方法可以返回一个基本类型数据或者 Promise 实例，或者抛出一个错误
11. 当不给 `then` 方法传递第一个参数时，值会传递给下一个 `then` 
12. `catch` 方法只是 `then` 没有第二个参数，默认给第二个参数一个函数并抛出错误
13. `resolve` 方法返回一个 promise
14. `reject` 方法永远返回一个状态为 rejected 的 promise
15. `all` 方法的实现
16. `race` 方法的实现
17. ……



## 初始结构搭建



```js
// Promise 构造函数接受一个函数参数
function Promise (executor) {

}

// then 方法接受两个函数参数
Promise.prototype.then = function (onResolved, onRejected) {

}
```



## resolve 与 reject



`resolve` 和 `reject` 都可以改变 Promise 的状态和结果值 。由于 `resolve` 和 `reject` 是直接调用的，`this` 是指向 window，所以需要通过 `_this` 中保存的当前 Promise 实例的 `this` 来改变实例中的属性



```js
function Promise (executor) {
  // 用于保存状态
  this.promiseState = 'pending'
  // 用于保存结果值
  this.promiseResult = null
  // 用来保存当前实例的 this
  const _this = this
  
  // 定义 resove 和 reject 函数
  function resolve (data) {
    // 1.修改对象的状态（promiseState）
    _this.promiseState = 'fulfilled'
    // 2.设置对象的结果
    _this.promiseResult = data
  }
  
  function reject (err) {
    // 1.修改对象的状态（promiseState）
    _this.promiseState = 'rejected'
    // 2.设置对象的结果
    _this.promiseResult = err
  }
  
    // 同步调用执行器函数，并且执行器接收两个函数作为参数
  executor(resolve, reject)
}

Promise.prototype.then = function (onResolved, onRejected) {

}
```



## throw 改变状态



Promise 有三种方式可以改变状态，除去上面两种，通过 throw 抛出异常也能将状态变为 rejected



```js
function Promise (executor) {
  this.promiseState = 'pending'
  this.promiseResult = null
  const _this = this
  
  function resolve (data) {
    _this.promiseState = 'fulfilled'
    _this.promiseResult = data
  }
  
  function reject (err) {
    _this.promiseState = 'rejected'
    _this.promiseResult = err
  }
  
    // 通过 try catch 来捕获 throw 抛出的错误
  try {
    executor(resolve, reject)
  } catch (error) {
    _this.promiseState = 'rejected'
    _this.promiseResult = error
  }
}

Promise.prototype.then = function (onResolved, onRejected) {

}
```



## 状态一旦改变就不会变



```js
function Promise (executor) {
  this.promiseState = 'pending'
  this.promiseResult = null
  const _this = this
  
  function resolve (data) {
    // 判断状态，只能由 pending 变为其它两种状态
    if (_this.promiseState !== 'pending') return
    _this.promiseState = 'fulfilled'
    _this.promiseResult = data
  }
  
  function reject (err) {
    // 判断状态
    if (_this.promiseState !== 'pending') return
    _this.promiseState = 'rejected'
    _this.promiseResult = err
  }

  try {
    executor(resolve, reject)
  } catch (error) {
    _this.promiseState = 'rejected'
    _this.promiseResult = error
  }
}

Promise.prototype.then = function (onResolved, onRejected) {

}
```



## 同步 then 方法执行回调



在同步任务中，如果调用 `resolve` 和 `reject` 状态会立即改变为 `fulfilled` 和 `rejected` ，

在 `then` 方法中进行判断



```js
function Promise (executor) {
  this.promiseState = 'pending'
  this.promiseResult = null
  const _this = this
  
  function resolve (data) {
    if (_this.promiseState !== 'pending') return
    _this.promiseState = 'fulfilled'
    _this.promiseResult = data
  }
  
  function reject (err) {
    if (_this.promiseState !== 'pending') return
    _this.promiseState = 'rejected'
    _this.promiseResult = err
  }

  try {
    executor(resolve, reject)
  } catch (error) {
    _this.promiseState = 'rejected'
    _this.promiseResult = error
  }
}

Promise.prototype.then = function (onResolved, onRejected) {
  // 只有当状态成功时才会执行 onResolved
  if (this.promiseState === 'fulfilled') {
   // 传递过来的回调中有形参，而 this.promiseResult 中保存着这个参数
    onResolved(this.promiseResult)
  }
  // 只有当状态失败时才会执行 onRejected
  if (this.promiseState === 'rejected') {
    onRejected(this.promiseResult)
  }
}
```



## 异步 then 方法执行回调



通过 `setTimeout` 来模拟一个异步任务



```js
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("OK")
  }, 1000)
})

p.then(
  (value) => {
    console.log(value)
  },
  (reason) => {
    console.warn(reason)
  }
)
```



以上示例中，1s 之后才会执行 `resolve` 改变状态，此时 `promiseState` 的状态为 pending，所以 `then` 中的回调不会执行，需要添加一个对 pending 状态的判断



```js
Promise.prototype.then = function (onResolved, onRejected) {
  // 判断 pending 状态
  if (this.promiseState === 'pending') {
    
  }
  if (this.promiseState === 'fulfilled') {
    onResolved(this.promiseResult)
  }
  if (this.promiseState === 'rejected') {
    onRejected(this.promiseResult)
  }
}
```



而 `then` 的回调只有当状态发生改变时才会触发，也就是必须在 `resolve` 和 `reject` 方法中触发。但是 Promise 实例中不可能拿到 `then` 中回调，此时可能通过往 Promise 中添加属性来保存这个回调



```js
function Promise (executor) {
  this.promiseState = 'pending'
  this.promiseResult = null
  const _this = this
  // 保存 then 中的回调函数
  this.callback = {}
  
  function resolve (data) {
    if (_this.promiseState !== 'pending') return
    _this.promiseState = 'fulfilled'
    _this.promiseResult = data
    // 执行 callback 中的 onResolved
    if (_this.callback.onResolved) {
      _this.callback.onResolved(data)
    }
  }
  
  function reject (err) {
    if (_this.promiseState !== 'pending') return
    _this.promiseState = 'rejected'
    _this.promiseResult = err
    // 执行 callback 中的 onRejected
    if (_this.callback.onRejected) {
      _this.callback.onRejected(err)
    }
  }

  try {
    executor(resolve, reject)
  } catch (error) {
    _this.promiseState = 'rejected'
    _this.promiseResult = error
  }
}

Promise.prototype.then = function (onResolved, onRejected) {
  if (this.promiseState === 'pending') {
    // 将回调函数保存在实例中
    this.callback = {
      onResolved,
      onRejected
    }
  }
  if (this.promiseState === 'fulfilled') {
    onResolved(this.promiseResult)
  }
  if (this.promiseState === 'rejected') {
    onRejected(this.promiseResult)
  }
}
```



## then 方法执行多个回调



此时你会发现，当调用多个 `then` 方法时，后面的回调会将前面的回调给覆盖



```js
let p = new Promise((resolve, reject) => {
  resolve("ok");
});

p.then((value) => {
  console.log(value);  // 该回调并不会执行
},(reason) => {
  console.warn(reason);
});

p.then((value) => {
  alert(value);  // 此时只会弹出 ok
},(reason) => {
  alert(reason);
});
```



这并不符合 Promise 的特性。可以通过 `then` 方法中直接保存回调改为 `push` 来解决这个问题



```js
function Promise (executor) {
  this.promiseState = 'pending'
  this.promiseResult = null
  const _this = this
  // 将存放回调的对象改为数组
  this.callback = []
  
  function resolve (data) {
    if (_this.promiseState !== 'pending') return
    _this.promiseState = 'fulfilled'
    _this.promiseResult = data
    // 执行每一个回调中的 onResolved
    _this.callback.forEach(item => {
      item.onResolved(data)
    })
  }
  
  function reject (err) {
    if (_this.promiseState !== 'pending') return
    _this.promiseState = 'rejected'
    _this.promiseResult = err
    // 执行每一个回调中的 onRejected
    _this.callback.forEach(item => {
      item.onRejected(err)
    })
  }

  try {
    executor(resolve, reject)
  } catch (error) {
    _this.promiseState = 'rejected'
    _this.promiseResult = error
  }
}

Promise.prototype.then = function (onResolved, onRejected) {
  if (this.promiseState === 'pending') {
    // 将回调 push 进数组中
    this.callback.push({
      onResolved,
      onRejected
    })
  }
  if (this.promiseState === 'fulfilled') {
    onResolved(this.promiseResult)
  }
  if (this.promiseState === 'rejected') {
    onRejected(this.promiseResult)
  }
}
```



## 同步 then 方法的返回结果



`then` 方法会返回一个 Promise 实例。在同步任务执行之后，如果 `then` 方法返回的是原始类型的数据时，状态为 fulfilled，并且 promiseResult 是返回的那个值；如果返回的是一个 Promise 实例时，状态由返回的那个实例决定，值由实例传过来的值决定；如果抛出的是一个 `throw` 则状态是 rejected



```js
function Promise (executor) {
  this.promiseState = 'pending'
  this.promiseResult = null
  const _this = this
  this.callback = []

  function resolve (data) {
    if (_this.promiseState !== 'pending') return
    _this.promiseState = 'fulfilled'
    _this.promiseResult = data
    _this.callback.forEach(item => {
      item.onResolved(data)
    })
  }

  function reject (err) {
    if (_this.promiseState !== 'pending') return
    _this.promiseState = 'rejected'
    _this.promiseResult = err
    _this.callback.forEach(item => {
      item.onRejected(err)
    })
  }

  try {
    executor(resolve, reject)
  } catch (error) {
    _this.promiseState = 'rejected'
    _this.promiseResult = error
  }
}

Promise.prototype.then = function (onResolved, onRejected) {
  // 返回 promise
  return new Promise((resolve, reject) => {
    if (this.promiseState === 'pending') {
      this.callback.push({
        onResolved,
        onRejected
      })
    }
    if (this.promiseState === 'fulfilled') {
      try {
        // 保存回调的结果
        let result = onResolved(this.promiseResult)
        // 判断回调返回的是什么
        if (result instanceof Promise) {
          // 如果返回的是一个 Promise，状态和结果都由 result 决定
          result.then(value => {
            resolve(value)
          }, reason => {
            reject(reason)
          })
        } else {
          // 如果返回 Promise 以外的，状态都为 fulfilled
          resolve(result)
        }
      } catch (error) {
        reject(error)
      }
    }
    // 写法和上面一致
    if (this.promiseState === 'rejected') {
      try {
        let result = onRejected(this.promiseResult)
        if (result instanceof Promise) {
          result.then(value => {
            resolve(value)
          }, reason => {
            reject(reason)
          })
        } else {
          resolve(result)
        }
      } catch (error) {
        reject(error)
      }
    }
  })
}
```



## 异步 then 方法的返回结果



上面“异步 then 方法执行回调”有提到，会将回调保存在实例中，然后根据 `resolve` 或者 `reject` 来进行相应的调用。但是要对回调返回的结果做一个判断时，就不能将回调直接保存，应当保存一个函数，通过这个函数来进行回调函数的一个调用和判断



```js
function Promise (executor) {
  this.promiseState = 'pending'
  this.promiseResult = null
  const _this = this
  this.callback = []

  function resolve (data) {
    if (_this.promiseState !== 'pending') return
    _this.promiseState = 'fulfilled'
    _this.promiseResult = data
    _this.callback.forEach(item => {
      item.onResolved(data)
    })
  }

  function reject (err) {
    if (_this.promiseState !== 'pending') return
    _this.promiseState = 'rejected'
    _this.promiseResult = err
    _this.callback.forEach(item => {
      item.onRejected(err)
    })
  }

  try {
    executor(resolve, reject)
  } catch (error) {
    _this.promiseState = 'rejected'
    _this.promiseResult = error
  }
}

Promise.prototype.then = function (onResolved, onRejected) {
  const _this = this
  return new Promise((resolve, reject) => {
    if (this.promiseState === 'pending') {
      this.callback.push({
        onResolved: function () {
          // 捕获异常
          try {
            // 保存回调的结果
            let result = onResolved(_this.promiseResult)
            // 判断回调返回的是什么
            if (result instanceof Promise) {
              // 如果返回的是一个 Promise，状态和结果都由 result 决定
              result.then(value => {
                resolve(value)
              }, reason => {
                reject(reason)
              })
            } else {
              // 如果返回 Promise 以外的，状态都为 fulfilled
              resolve(result)
            }
          } catch (error) {
            reject(error)
          }
        },
        // 以下和 onResolved 一致，只是将调用的方法改为了 onRejected
        onRejected: function () {
          try {
            let result = onRejected(_this.promiseResult)
            if (result instanceof Promise) {
              result.then(value => {
                resolve(value)
              }, reason => {
                reject(reason)
              })
            } else {
              resolve(result)
            }
          } catch (error) {
            reject(error)
          }
        }
      })
    }
    if (this.promiseState === 'fulfilled') {
      try {
        let result = onResolved(this.promiseResult)
        if (result instanceof Promise) {
          result.then(value => {
            resolve(value)
          }, reason => {
            reject(reason)
          })
        } else {
          resolve(result)
        }
      } catch (error) {
        reject(error)
      }
    }
    if (this.promiseState === 'rejected') {
      onRejected(this.promiseResult)
    }
  })
}
```



## 封装重复代码



可以发现以上的代码多次对 `then` 返回的值做了一个判断，可以将重复的代码进行一个封装，方便维护



```js
function Promise (executor) {
  this.promiseState = 'pending'
  this.promiseResult = null
  const _this = this
  this.callback = []

  function resolve (data) {
    if (_this.promiseState !== 'pending') return
    _this.promiseState = 'fulfilled'
    _this.promiseResult = data
    _this.callback.forEach(item => {
      item.onResolved(data)
    })
  }

  function reject (err) {
    if (_this.promiseState !== 'pending') return
    _this.promiseState = 'rejected'
    _this.promiseResult = err
    _this.callback.forEach(item => {
      item.onRejected(err)
    })
  }

  try {
    executor(resolve, reject)
  } catch (error) {
    _this.promiseState = 'rejected'
    _this.promiseResult = error
  }
}

Promise.prototype.then = function (onResolved, onRejected) {
  const _this = this
  return new Promise((resolve, reject) => {
    // 将重复性的代码进行封装
    function callback (type) {
      try {
        // 保存回调的结果
        let result = type(_this.promiseResult)
        // 判断回调返回的是什么
        if (result instanceof Promise) {
          // 如果返回的是一个 Promise，状态和结果都由 result 决定
          result.then(value => {
            resolve(value)
          }, reason => {
            reject(reason)
          })
        } else {
          // 如果返回 Promise 以外的，状态都为 fulfilled
          resolve(result)
        }
      } catch (error) {
        reject(error)
      }
    }
    if (this.promiseState === 'pending') {
      this.callback.push({
        onResolved: function () {
          callback(onResolved)
        },
        onRejected: function () {
          callback(onRejected)
        }
      })
    }
    if (this.promiseState === 'fulfilled') {
      callback(onResolved)
    }
    if (this.promiseState === 'rejected') {
      callback(onRejected)
    }
  })
}
```



## then 方法值传递



`then` 方法还有一个特性就是值的传递，当不给 `then` 传递第一个参数时，值会传递给下一个 `then` 。原理是给第一个参数一个默认函数，并将值返回



```js
function Promise (executor) {
  this.promiseState = 'pending'
  this.promiseResult = null
  const _this = this
  this.callback = []

  function resolve (data) {
    if (_this.promiseState !== 'pending') return
    _this.promiseState = 'fulfilled'
    _this.promiseResult = data
    _this.callback.forEach(item => {
      item.onResolved(data)
    })
  }

  function reject (err) {
    if (_this.promiseState !== 'pending') return
    _this.promiseState = 'rejected'
    _this.promiseResult = err
    _this.callback.forEach(item => {
      item.onRejected(err)
    })
  }

  try {
    executor(resolve, reject)
  } catch (error) {
    _this.promiseState = 'rejected'
    _this.promiseResult = error
  }
}

Promise.prototype.then = function (onResolved, onRejected) {
  const _this = this
  // 判断回调函数参数
  if (typeof onResolved !== 'function') {
    // 将值返回
    onResolved = value => value
  }
  
  return new Promise((resolve, reject) => {
    function callback (type) {
      try {
        let result = type(_this.promiseResult)
        if (result instanceof Promise) {
          result.then(value => {
            resolve(value)
          }, reason => {
            reject(reason)
          })
        } else {
          resolve(result)
        }
      } catch (error) {
        reject(error)
      }
    }
    if (this.promiseState === 'pending') {
      this.callback.push({
        onResolved: function () {
          callback(onResolved)
        },
        onRejected: function () {
          callback(onRejected)
        }
      })
    }
    if (this.promiseState === 'fulfilled') {
      callback(onResolved)
    }
    if (this.promiseState === 'rejected') {
      callback(onRejected)
    }
  })
}
```



## catch 方法的实现



`catch` 方法特性是异常穿透



异常穿透就是不给 `then` 函数传递第二个参数，最后所有错误都由 `catch` 来处理。原理是给第二个参数一个默认的函数，并将错误抛出。这样错误能一直往后传递，最后由 `catch` 处理



```js
function Promise (executor) {
  this.promiseState = 'pending'
  this.promiseResult = null
  const _this = this
  this.callback = []

  function resolve (data) {
    if (_this.promiseState !== 'pending') return
    _this.promiseState = 'fulfilled'
    _this.promiseResult = data
    _this.callback.forEach(item => {
      item.onResolved(data)
    })
  }

  function reject (err) {
    if (_this.promiseState !== 'pending') return
    _this.promiseState = 'rejected'
    _this.promiseResult = err
    _this.callback.forEach(item => {
      item.onRejected(err)
    })
  }

  try {
    executor(resolve, reject)
  } catch (error) {
    _this.promiseState = 'rejected'
    _this.promiseResult = error
  }
}

Promise.prototype.then = function (onResolved, onRejected) {
  const _this = this
  if (typeof onResolved !== 'function') {
    onResolved = value => value
  }
  // 判断回调函数参数
  if (typeof onRejected !== 'function') {
    // 将错误抛出
    onRejected = reason => {
      throw reason
    }
  }
  
  return new Promise((resolve, reject) => {
    function callback (type) {
      try {
        let result = type(_this.promiseResult)
        if (result instanceof Promise) {
          result.then(value => {
            resolve(value)
          }, reason => {
            reject(reason)
          })
        } else {
          resolve(result)
        }
      } catch (error) {
        reject(error)
      }
    }
    if (this.promiseState === 'pending') {
      this.callback.push({
        onResolved: function () {
          callback(onResolved)
        },
        onRejected: function () {
          callback(onRejected)
        }
      })
    }
    if (this.promiseState === 'fulfilled') {
      callback(onResolved)
    }
    if (this.promiseState === 'rejected') {
      callback(onRejected)
    }
  })
}

// 添加 catch 方法
Promise.prototype.catch = function (onRejected) {
  return this.then(undefined, onRejected)
}
```



## resolve 方法的实现



`Promise.resolve` 方法不是属性实例对象的，当传入一个原始类型时，返回 fulfilled 状态的 Promise 实例，传入一个 Promise 实例时，由这个实例决定返回实例的状态



```js
Promise.resolve = function (argument) {
  return new Promise((resolve, reject) => {
    // 判断类型
    if (argument instanceof Promise) {
      argument.then(value => {
        resolve(value)
      }, reason => {
        reject(reason)
      })
    } else {
      resolve(argument)
    }
  })
}
```



## reject 方法的实现



`Promise.reject` 方法不管传入什么都返回一个 rejected 状态的 Promise 实例



```js
Promise.reject = function (reason) {
  return new Promise((resolve, reject) => {
    reject(reason)
  })
}
```



## all 方法的实现



`Promise.all` 接收一个 `Promise` 实例组成的数组，当所有实例的状态都为 fulfilled 时，返回一个状态为 fulfilled 状态的 Promise 实例，结果为返回值组成的数组；如果其中一个 实例状态不为 fulfilled，则返回一个 rejected 状态的 Promise 实例，结果为第一个失败的 Promise 的值



```js
Promise.all = function (promises) {
  return new Promise((resolve, reject) => {
    // 用于存放成功的 promise
    let count = 0
    // 用于存放成功 promise 的值
    let arr = []
    for (let  i = 0; i < promises.length; i++) {
      // 如果是 promise 就必定有 then 方法
      promises[i].then(value => {
        count++
        count[i] = value
        // 如果 promise 成功的数量与数组长度相同时，则表示全部成功了
        if (count == promises.length) {
          resolve(arr)
        }
      }, reason => {
        reject(reason)
      })
    }
  })
}
```



## race 方法的实现



`Promise.all` 接收一个 `Promise` 实例组成的数组，返回一个 Promise 实例，状态由数组中最先改变状态的 promise 决定，结果也是为最先改变状态的结果



```js
Promise.race = function (promises) {
  return new Promise((resolve, reject) => {
    for (let i = 0; i < promises.length; i++) {
      promises[i].then(value => {
        // 修改返回对象的状态为 成功
        resolve(value)
      }, reason => {
        // 修改返回对象的状态为 失败
        reject(reason)
      })
    }
  })
}
```



## then 方法回调的异步执行



`then` 方法中的回调是异步执行的，示例：



```js
let p1 = new Promise((resolve, reject) => {
    resolve('OK')
  console.log(111)
})

p1.then(value => {
    console.log(222)
})

console.log(333)

// 打印结果为：111 222 333
```



在 `then` 方法执行回调时使用 `setTimeout` 可以变为异步执行



```js
function Promise (executor) {
  this.promiseState = 'pending'
  this.promiseResult = null
  const _this = this
  this.callback = []

  function resolve (data) {
    if (_this.promiseState !== 'pending') return
    _this.promiseState = 'fulfilled'
    _this.promiseResult = data
    // 转换为异步操作
    setTimeout(() => {
      _this.callback.forEach(item => {
        item.onResolved(data)
      })
    })
  }

  function reject (err) {
    if (_this.promiseState !== 'pending') return
    _this.promiseState = 'rejected'
    _this.promiseResult = err
    // 转换为异步操作
    setTimeout(() => {
      _this.callback.forEach(item => {
        item.onRejected(err)
      })
    });
  }

  try {
    executor(resolve, reject)
  } catch (error) {
    _this.promiseState = 'rejected'
    _this.promiseResult = error
  }
}

Promise.prototype.then = function (onResolved, onRejected) {
  const _this = this
  if (typeof onResolved !== 'function') {
    onResolved = value => value
  }
  if (typeof onRejected !== 'function') {
    onRejected = reason => {
      throw reason
    }
  }
  
  return new Promise((resolve, reject) => {
    function callback (type) {
      try {
        let result = type(_this.promiseResult)
        if (result instanceof Promise) {
          result.then(value => {
            resolve(value)
          }, reason => {
            reject(reason)
          })
        } else {
          resolve(result)
        }
      } catch (error) {
        reject(error)
      }
    }
    if (this.promiseState === 'pending') {
      this.callback.push({
        onResolved: function () {
          callback(onResolved)
        },
        onRejected: function () {
          callback(onRejected)
        }
      })
    }
    if (this.promiseState === 'fulfilled') {
      // 转换为异步操作
      setTimeout(() => {
        callback(onResolved) 
      });
    }
    if (this.promiseState === 'rejected') {
      // 转换为异步操作
      setTimeout(() => {
        callback(onRejected)
      });
    }
  })
}

Promise.prototype.catch = function (onRejected) {
  return this.then(undefined, onRejected)
}
```



## 完整代码



```js
function Promise (executor) {
  // 状态
  this.promiseState = 'pending'
  // 结果值
  this.promiseResult = null
  // 用来保存当前的 this
  const _this = this
  // 保存 then 中的回调函数
  this.callback = []

  // 定义 resove 和 reject 函数
  function resolve (data) {
    // 判断状态
    if (_this.promiseState !== 'pending') return
    // 1.修改对象的状态（promiseState）
    _this.promiseState = 'fulfilled'
    // 2.设置对象的结果
    _this.promiseResult = data
    // 执行 callback 中的函数
    setTimeout(() => {
      _this.callback.forEach(item => {
        item.onResolved(data)
      })
    })
  }

  function reject (err) {
    // 判断状态
    if (_this.promiseState !== 'pending') return
    // 1.修改对象的状态（promiseState）
    _this.promiseState = 'rejected'
    // 2.设置对象的结果
    _this.promiseResult = err
    // 执行 callback 中的函数
    setTimeout(() => {
      _this.callback.forEach(item => {
        item.onRejected(err)
      })
    })
  }

  // 同步调用执行器函数，并且执行器接收两个函数作为参数
  try {
    executor(resolve, reject)
  } catch (error) {
    _this.promiseState = 'rejected'
    _this.promiseResult = error
  }
}

Promise.prototype.then = function (onResolved, onRejected) {
  const _this = this
  // 判断回调函数参数
  if (typeof onResolved !== 'function') {
    onResolved = value => value
  }
  if (typeof onRejected !== 'function') {
    onRejected = reason => {
      throw reason
    }
  }
  // 返回一个 Promise 实例
  return new Promise((resolve, reject) => {
    function callback (type) {
      try {
        // 保存回调的结果
        let result = type(_this.promiseResult)

        // 判断回调返回的是什么
        if (result instanceof Promise) {
          // 如果返回的是一个 Promise，状态和结果都由 result 决定
          result.then(value => {
            resolve(value)
          }, reason => {
            reject(reason)
          })

        } else {
          // 如果返回 Promise 以外的，状态都为 fulfilled
          resolve(result)
        }
      } catch (error) {
        reject(error)
      }
    }
    if (this.promiseState === 'pending') {
      // 将回调压入进数组中
      this.callback.push({
        onResolved: function () {
          callback(onResolved)
        },
        onRejected: function () {
          callback(onRejected)
        }
      })
    }
    if (this.promiseState === 'fulfilled') {
      setTimeout(() => {
        callback(onResolved)
      })
    }
    if (this.promiseState === 'rejected') {
      setTimeout(() => {
        callback(onRejected)
      })
    }
  })
}

// 添加 catch 方法
Promise.prototype.catch = function (onRejected) {
  return this.then(undefined, onRejected)
}

// 添加 resolve 方法
Promise.resolve = function (argument) {
  return new Promise((resolve, reject) => {
    if (argument instanceof Promise) {
      argument.then(value => {
        resolve(value)
      }, reason => {
        reject(reason)
      })
    } else {
      resolve(argument)
    }
  })
}

// 添加 reject 方法
Promise.reject = function (reason) {
  return new Promise((resolve, reject) => {
    reject(reason)
  })
}

// 添加 all 方法
Promise.all = function (promises) {
  return new Promise((resolve, reject) => {
    // 用于存放成功的 promise
    let count = 0
    // 用于存放成功 promise 的值
    let arr = []
    for (let i = 0; i < promises.length; i++) {
      promises[i].then(value => {
        count++
        count[i] = value
        // 如果 promise 成功的数量与数组长度相同时，则表示全部成功了
        if (count == promises.length) {
          resolve(arr)
        }
      }, reason => {
        reject(reason)
      })
    }
  })
}

// 添加 race 方法
Promise.race = function (promises) {
  return new Promise((resolve, reject) => {
    for (let i = 0; i < promises.length; i++) {
      promises[i].then(value => {
        // 修改返回对象的状态为 成功
        resolve(arr)
      }, reason => {
        // 修改返回对象的状态为 失败
        reject(reason)
      })
    }
  })
}
```