---
title: AJAX——jQuery 中的 Ajax（三）
date: 2021-5-26
tags:
  - AJAX
categories:
  - JavaScript
---

## GET 请求



通过 `$.get()` 方法发送请求，以取代复杂的 `$.ajax()` 方法。请求成功时可调用回调函数。如果需要在出错时执行函数，请使用 `$.ajax()`。



`get()` 接受四个参数：



- `url`：必需，规定需要请求的 URL。
- `data`：可选，规定连同请求发送到服务器的数据。
- `success(response,status,xhr)`：可选。规定请求成功时的回调函数。

- - response - 包含来自请求的结果数据
  - status - 包含请求的状态（"success"、"notmodified"、"error"、"timeout"、"parsererror"）
  - xhr - 包含 XMLHttpRequest 对象

- `dataType`：可选。规定预计的服务器响应的数据类型。默认地，jQuery 将智能判断。

- - "json" - 以 JSON 运行响应，并以 JavaScript 对象返回
  - "jsonp" - 使用 JSONP 加载一个 JSON 块，将添加一个 "?callback=?" 到 URL 来规定回调



示例：



```js
$.get("http://localhost:8000/jquery-server",{ a: 100, b: 200 },function (response, status, xhr) {
  console.log(`Hello,${response.name}`)
}, 'json')
```



jQuery 1.12 中  `$.get()` 和 `$.post()` 都支持对象参数，具体的参数可以参考 `$.ajax()`。



```
$.get({
  url: "/example"
});
```



## POST 请求



通过 `$.post()` 方法发送  POST 请求，以取代复杂的 `$.ajax()` 方法。请求成功时可调用回调函数。如果需要在出错时执行函数，请使用 `$.ajax()`。



`$.post()` 方法与 `$.get()` 方法接受的参数一样。



```js
$.post("http://localhost:8000/jquery-server",
    $("#testform").serialize(),
  function (response, status, xhr) {
    console.log(response)
}, 'json')
```



上述代码中，`serialize()` 方法可以将表单数据序列化。



## $.ajax() 方法



通用方法 `ajax`  接受一个键值对集合（对象）作为参数，所有选项（键）都是可选的。



```js
$.ajax({
    url: 'http://loacalhost:8000/jquery-server',
  // 参数
  data: {a:100, b:200},
  // 请求类型
  type: 'GET',
  // 响应类型
  dataType: 'json',
  // 成功的回调
  success: function (data) {
    ... 
  },
  // 超时时间
  timeout: 2000,
  // 失败的回调
  error: function () {
    ... 
  }
  // 头信息
  headers: {
    'Content-Type': 'application/x-www-form-urlencoded'
  }
})
```



更多选项可以查看[官方文档](https://api.jquery.com/jQuery.ajax/)或者[中文文档](https://jquery.cuishifeng.cn/jQuery.Ajax.html)



## $.getJSON() 方法



`$.getJSON()` 用于向服务器发送 GET 请求，获取 JSON 格式数据。是 `$.get()` 方法第四个参数为 "json" 的简写方式。



```js
getJSON: function( url, data, callback ) {
  return jQuery.get( url, data, callback, "json" );
}
```