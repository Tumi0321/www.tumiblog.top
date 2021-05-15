---
title: class 实现 Promise（三）
date: 2021-4-24
tags:
  - JS 原理
categories:
  - JavaScript
---

```js
class Promise {
  // 构造方法
  constructor(executor) {
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

  // then 方法
  then (onResolved, onRejected) {
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

  // catch 方法
  catch (onRejected) {
    return this.then(undefined, onRejected)
  }

  // resolve 方法
  static resolve (argument) {
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

  // reject 方法
  static reject (reason) {
    return new Promise((resolve, reject) => {
      reject(reason)
    })
  }

  // all 方法
  static all (promises) {
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

  // race 方法
  static race (promises) {
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
}
```