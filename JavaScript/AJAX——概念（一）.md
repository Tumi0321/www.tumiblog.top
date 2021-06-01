---
title: AJAX——概念（一）
date: 2021-5-25
tags:
  - AJAX
categories:
  - JavaScript
---

## AJAX 简介



AJAX（Asynchronous JavaScritpt and XML），异步的 JavaScript 和 XML。AJAX 不是一种新的编程语言，是使用 XMLHttpRequest 对象与服务器通信的一种技术。AJAX 最主要的特性就是可以在不刷新页面的情况下与服务器通信（异步），交换信息或更新页面。



## XML 简介



XML（Extensible Markup Language） ，指可扩展标记语言，被设计用来传输和存储数据。



比如说我有一个学生数据：



```
name = “张三” ; age = 18 ; gender = “男” ;
```



用 XML 表示：



```xml
<student>
    <name>张三</name>
    <age>18</age>
    <gender>男</gender>
</student>
```



XML 和 HTML 类似，不同的是 HTML 中都是预定义标签，用来向网页中呈现数据的；而 XML 中没有预定义标签，通过自定义标签表示一些数据，用来传输和存储。



最开始 AJAX 进行数据交互时，所使用的格式是 XML，现在已经被 JSON 取代了。



```json
{"name":"张三","age":18,"gender":"男"}
```



JSON 格式更加简洁，并且提供了强大的 API，灵活度远胜于 XML。



## AJAX 优缺点



### AJAX 的优点



- 在不重新加载页面的情况下与服务器进行通信。
- 允许你接受并使用从服务器发来的数据更新部分页面内容。



### AJAX 的缺点



- 没有浏览历史，不能回退
- 存在跨域问题（同源）
- 数据是异步请求的，对 SEO 不友好



## HTTP 协议



超文本传输协议（Hypertext Transfer Protocol，HTTP）是一个简单的请求-响应协议，规定了浏览器和万维服务器之间互相通信的规则，[MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Overview) 中有对其概念的解释。



### 请求交互过程



![2021/05/25/TOIMG9fc680525074336N.png](https://picturebed.tumiblog.top/2021/05/25/TOIMG9fc680525074336N.png)



1. 前后应用从浏览器端向服务器发送 HTTP 请求(请求报文)
2. 服务器接收到请求后, 调度服务器应用处理请求, 向浏览器端返回 HTTP 响应（响应报文）
3. 浏览器端接收到响应, 解析显示响应体/调用监视回调



### 查看 HTTP 请求



在浏览器中可以查看 http 请求信息，以下以 Chrome 为例。



按 F12 可以打开开发者工具界面，可以查看发送的 HTTP 请求的相关信息。



![2021/05/25/TOIMG3d5aa0525074404N.png](https://picturebed.tumiblog.top/2021/05/25/TOIMG3d5aa0525074404N.png)



### HTTP 请求报文



请求报文包括了四部分，请求行、请求头、空行、请求体。



- **请求行**



请求行包括了三部分，请求类型（GET、POST...）、URL 路径、HTTP 协议的版本（HTTP/1.1）。



```
GET /product_detail?id=2 HTTP/1.1
```



![2021/05/25/TOIMGe81ce0525074431N.png](https://picturebed.tumiblog.top/2021/05/25/TOIMGe81ce0525074431N.png)



- **请求头**



```
Host: baidu.com
Cookie: name=baidu
Content-type: applicatiion/x-www-form-urlencoded
User-Agent: Chrome 83
...
```



![2021/05/25/TOIMG027a60525074456N.png](https://picturebed.tumiblog.top/2021/05/25/TOIMG027a60525074456N.png)



- **空行**



空行是固定的，必须得有。



- **请求体**



请求体内容可以有也可以没有，如果是 GET 请求，请求体则为空，如果是 POST 请求，那么请求体可以不为空。



```
username=admin&password=admin
```



![2021/05/25/TOIMG9c8890525074531N.png](https://picturebed.tumiblog.top/2021/05/25/TOIMG9c8890525074531N.png)



### HTTP 响应报文



响应报文和请求报文一样，也包括四部分，响应行、响应头、空行、响应体。



- **响应行**



响应行包括三部分，HTTP 版本、响应状态码（200）、响应状态字符串（OK）。



```
HTTP/1.1 200 OK
```



![2021/05/25/TOIMG2a59e0525074554N.png](https://picturebed.tumiblog.top/2021/05/25/TOIMG2a59e0525074554N.png)





- **响应头**



格式和请求头一样。



```
Content-type: text/html;charset=utf-8
Content-length: 2048
Content-encoding: gzip
...
```



![2021/05/25/TOIMG7dc8a0525074615N.png](https://picturebed.tumiblog.top/2021/05/25/TOIMG7dc8a0525074615N.png)



- **空行**



- **响应体**



响应体可以是 html 文档，也可以是其它类型的。



![2021/05/25/TOIMGd60400525074636N.png](https://picturebed.tumiblog.top/2021/05/25/TOIMGd60400525074636N.png)



### 常见的响应状态码



`200 OK` 请求成功。一般用于GET 与POST 请求。

`201 Created` 已创建。成功请求并创建了新的资源。

`401 Unauthorized` 未授权/请求要求用户的身份认证。

`404 Not Found` 服务器无法根据客户端的请求找到资源。

`500 Internal Server Error` 服务器内部错误，无法完成请求。



更多状态码可以参考[《HTTP 状态码》](https://tumiblog.top/blogs/%E6%B5%8F%E8%A7%88%E5%99%A8/HTTP%20%E7%8A%B6%E6%80%81%E7%A0%81.html#%E5%B8%B8%E8%A7%81%E7%8A%B6%E6%80%81%E7%A0%81)



### 响应头信息



| **响应头**        | **描述**                                           | **示例**                                        |
| ----------------- | -------------------------------------------------- | ----------------------------------------------- |
| Content-Length    | 请求的内容长度                                     | Content-Length: 348                             |
| Content-Type      | 表示文档属于什么 MIME 类型                         | Content-Type: application/x-www-form-urlencoded |
| Connection        | 表示是否需要持久连接。（HTTP 1.1默认进行持久连接） | Connection: close                               |
| Content-Encoding  | web服务器支持的返回内容压缩编码类型。              | Content-Encoding: gzip                          |
| Date              | 请求发送的日期和时间                               | Date: Tue, 15 Nov 2010 08:12:31 GMT             |
| ETag              | 请求变量的实体标签的当前值                         | ETag: “737060cd8c284d8af7ad3082f209582d”        |
| Server            | web 服务器软件名称                                 | Server: Apache/1.3.27 (Unix) (Red-Hat/Linux)    |
| Transfer-Encoding | 文件传输编码                                       | Transfer-Encoding:chunked                       |
| Vary              | 告诉下游代理是使用缓存响应还是从原始服务器请求     | Vary: *                                         |



详细可以参考 [w3c 官网](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)，或者[菜鸟教程](https://www.runoob.com/http/http-header-fields.html)中文文档