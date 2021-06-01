---
title: AJAX——原生 Ajax（二）
date: 2021-5-25
tags:
  - AJAX
categories:
  - JavaScript
---

## XHR 简介



`XMLHttpRequest`（XHR）对象用于与服务器交互。通过 XMLHttpRequest 可以在不刷新页面的情况下请求特定 URL，获取数据。这允许网页在不影响用户操作的情况下，更新页面的局部内容。`XMLHttpRequest` 在 [AJAX](https://developer.mozilla.org/zh-CN/docs/Glossary/AJAX) 编程中被大量使用。



## 基本使用



通过 `XMLHttpRequest` 构造函数可以初始化一个 XMLHttpRequest 实例对象。



```js
// 1.创建实例
const xhr = new XMLHttpRequest()

// 2.初始化一个请求（请求类型和 URL）
xhr.open('GET', 'http://127.0.0.1:8000/server')

// 3.发送请求
xhr.send()

// 4.绑定事件，处理服务器端返回结果
xhr.onreadystateChange = function () {
  // 判断当前服务端返回了所有结果
    if (xhr.readState === 4) {
    // 判断响应成功时（2 开头的状态都是成功的）
     if (xhr.status >= 200 && xhr.status < 300) {
       // 处理结果
     } else {
        // ...
     }
  }
}
```



## 属性



| **属性**           | **描述**                                                     |
| ------------------ | ------------------------------------------------------------ |
| onreadystatechange | 接受一个回调函数作为值，当 readystate 属性发生改变时调用函数。 |
| readyState         | 存有 XMLHttpRequest 的状态。从 0 到 4 发生变化。             |
| status             | 返回请求的响应状态（例如，"200"、"404"）。                   |
| statusText         | 返回响应状态字符串（例如，"OK"、"Not Found"）。              |
| response           | 返回整个响应体，具体是哪种类型取决于 `responseType` 属性。   |
| responseText       | 只能返回"text"类型的响应。                                   |
| responseType       | 返回响应数据的类型。它允许我们手动设置返回数据的类型。如果我们将它设置为一个空字符串，它将使用默认的"text"类型。 |
| timeout            | 表示该请求的最大请求时间（毫秒），若超出该时间，请求会自动终止。 |
| ontimeout          | 接受一个回调函数作为值，当请求超时时调用函数。               |
| onerror            | 接受一个回调函数作为值，当请求遭遇错误时调用函数。           |



## 方法



| **方法**                       | **描述**                                                     |
| ------------------------------ | ------------------------------------------------------------ |
| open(method,url,async)         | 规定请求的类型、URL 以及是否异步处理请求。 method：请求的类型；GET 或 POSTurl：文件在服务器上的位置async：true（异步）或 false（同步） |
| send(string)                   | 将请求发送到服务器。 string：设置请求体，仅用于 POST 请求    |
| setRequestHeader(header,value) | 向请求添加 HTTP 头。 header: 规定头的名称value: 规定头的值   |
| getAllResponseHeaders          | 以字符串的形式返回所有响应头数据，如果没有收到响应，则返回 null。 |
| abort                          | 如果请求已被发出，则立刻中止请求。并将请求的 status 置为 0。 |



## 事件



| **事件** | **描述**                                                     |
| -------- | ------------------------------------------------------------ |
| error    | 当 request 遭遇错误时触发，也可以使用 `onerror` 属性。       |
| timeout  | 在预设时间内没有接收到响应时触发，也可以使用 `ontimeout` 属性。 |



示例：



```js
const xhr = new XMLHttpRequest();

xhr.addEventListener('error', handleEvent);
xhr.addEventListener('timeout', handleEvent);
```



## `readyState` 状态码



| **值** | **状态**         | **描述**                                            |
| ------ | ---------------- | --------------------------------------------------- |
| 0      | UNSENT           | 代理被创建，但尚未调用 `open()` 方法。              |
| 1      | OPENED           | `open()` 方法已经被调用。                           |
| 2      | HEADERS_RECEIVED | `send()` 方法已经被调用，并且头部和状态已经可获得。 |
| 3      | LOADING          | 下载中； `responseText` 属性已经包含部分数据。      |
| 4      | DONE             | 下载操作已完成。                                    |



## GET 设置请求参数



例如 `https://www.baidu.com/s?wd=HTTP`，问号（?）后面的就是请求参数。可以通过 `open` 方法设置。



```js
const xhr = new XMLHttpRequest()

// 设置请求参数
xhr.open('GET', 'http://127.0.0.1:8000/server?a=100&b=300')
```



![2021/05/25/TOIMG4824d0525075719N.png](https://picturebed.tumiblog.top/2021/05/25/TOIMG4824d0525075719N.png)



## POST 设置请求体





POST 请求体需要在 `send` 方法中设置。



```js
const xhr = new XMLHttpRequest()
xhr.open('POST', 'http://127.0.0.1:8000/server')

// 设置请求体
xhr.send('a=100&b=200')
```



请求体可以是任意格式，只要服务器能够处理。



![2021/05/25/TOIMG653b00525075914N.png](https://picturebed.tumiblog.top/2021/05/25/TOIMG653b00525075914N.png)



## 设置请求头信息



通过 `setRequestHeader` 方法设置请求头信息，该方法接收两个参数，第一个参数是属性的名称，第二个参数是属性的值。必须在 `open()` 之后、`send()` 之前调用。



```js
const xhr = new XMLHttpRequest()
xhr.open('GET', 'http://127.0.0.1:8000/server')
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded')
xhr.send()
```



![2021/05/25/TOIMGe81610525075941N.png](https://picturebed.tumiblog.top/2021/05/25/TOIMGe81610525075941N.png)



注意：自定义一些 header 属性进行跨域请求时，可能会遇到"**not allowed by Access-Control-Allow-Headers in preflight response**"，你可能需要在你的服务端设置"Access-Control-Allow-Headers"。



## 服务端响应 JSON 数据



node 的 `send` 方法只能发送字符串或者 buffer 类型的数据，在实际应用中，服务器响应的绝大多数情况都是 JSON 格式的数据，所以要对响应的数据做一个处理。



第一种方式可以使用 `JSON.parse` 方法进行手动转换



```js
const xhr = new XMLHttpRequest()

xhr.open("POST", "http://localhost:8000/json-server")
xhr.send()

xhr.onreadystatechange = function () {
  if (xhr.readyState === 4) {
    if (xhr.status >= 200 && xhr.status < 300) {
        // 将 JSON 转换成对象
        let data = JSON.parse(xhr.response)
        console.log(data) // {name: "zs"}
    }
  }
}
```



第二种方法可以通过 `XMLHttpRequest.responseType` 属性设置响应类型，如果值为 `'json'` 将会自动进行转换。



```js
const xhr = new XMLHttpRequest()

// 设置响应类型
xhr.responseType = 'json'
xhr.open("POST", "http://localhost:8000/json-server")
xhr.send()

xhr.onreadystatechange = function () {
  if (xhr.readyState === 4) {
    if (xhr.status >= 200 && xhr.status < 300) {
        console.log(xhr.response) // {name: "zs"}
    }
  }
}
```



## IE 缓存问题



IE 浏览器会对同一个 ajax 请求结果做一个缓存，这样会导致重新请求时，浏览器依然使用的是缓存内容。



既然是对同一个 ajax 请求才会缓存，那就发送不同的请求就能解决这个问题了：



```js
const xhr = new XMLHttpRequest()

xhr.open("GET", "http://localhost:8000/ie?t=" + Date.now())
xhr.send()
```



在请求 URL 后面加上一个动态参数就可以了。



## 请求超时与异常处理



我们不能保证服务端永远能够及时快速响应请求，为了给用户友好地反馈信息，需要设置请求超时的处理和请求异常时的处理。



```js
const xhr = new XMLHttpRequest()

// 超时设置 2s
xhr.timeout = 2000
// 超时回调
xhr.ontimeout = function () {
  alert("网络异常，请稍后重试！")
}
// 网络异常回调
xhr.onerror = function () {
  alert("你的网络似乎出了一些问题！")
}

xhr.open("GET", "http://localhost:8000/delay")
xhr.send()
```



请求的状态为 canceled 时，表示请求已经取消了，状态码为 0。



![2021/05/25/TOIMGef46a0525080100N.png](https://picturebed.tumiblog.top/2021/05/25/TOIMGef46a0525080100N.png)



## 取消请求



在请求的过程中，当结果还没有响应时，我们可以使用 `abort` 方法手动取消这个请求。



```js
let flag = false
const xhr = new XMLHttpRequest()

xhr.open("GET", "http://localhost:8000/delay")
xhr.send()

if (!flag) {
  // 取消请求
    xhr.abort() 
}
```



## 请求重复问题



如果用户频繁去发送相同请求，服务器的压力会很大。我们可以在用户发送请求的时候，判断是否发送过相同的请求，如果有，则可以取消请求并重新发送一个新的请求。



```js
// 用来存放 XMLHttpRequest 实例
let xhr = null
// 标识变量，是否发送请求
let isSending = false

btn.addEventListener("click", function () {
    // 如果已经发送了则取消请求
  if (isSending) xhr.abort()
  
  // 当实例被创建时，表示正在发送请求
  xhr = new XMLHttpRequest()
    // 此时更改标识变量
  isSending = true
  xhr.open("GET", "http://localhost:8000/delay")
  xhr.send()

  xhr.onreadystatechange = function () {
    if (xhr.readyState === 4) {
      // 当请求操作完成之后，还原标识变量
      isSending = false
    }
  }
})
```