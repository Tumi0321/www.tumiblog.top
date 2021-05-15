---
title: 理解 cookie、 session、localStorage 和 sessionStorage
date: 2021-4-1
tags:
  - 浏览器存储
categories:
  - 浏览器
---

随着 Html5 的出现，我们有了多种选择可以在客户端浏览器上缓存或存储信息。以前，我们只有 cookie，它具有很大的局限性，并且 cookie 的存储大小很小。但是现在我们也使用 localStorage 和 sesstionStorage。



## LocalStorage



`localStorage` 是在客户端计算机上存储数据的一种方法。它允许在 web 浏览器中保存键/值对，并存储没有过期日期的数据。`localStorage` 只能通过 JavaScript 和 HTML5 访问。但是，用户可以清除浏览器数据/缓存以清除所有 `localStorage` 数据。



**优点：**



- 存储没有过期日期的数据
- 存储限制约为 5MB
- 数据永远不会传输到服务器



**缺点：**



- 明文，因此设计上不安全
- 限于字符串数据，因此需要序列化
- 有同源策略限制
- 只能在客户端读取



**作用域**：在同一个浏览器内，同源文档之间共享 `localStorage` 数据，可以互相读取、覆盖。



**使用：**



```js
// 保存数据到 localStorage
localStorage.setItem('key', 'value');

// 从 localStorage 获取数据
let data = localStorage.getItem('key');

// 从 localStorage 删除保存的数据
// 在删除的时候如果 key 值错误，不会报错
localStorage.removeItem('key');

// 从 localStorage 删除所有保存的数据
localStorage.clear();
```





## SessionStorage



- 仅为会话存储数据，这意味着数据将一直存储到浏览器(或选项卡)关闭为止
- 数据永远不会传输到服务器
- 只能在客户端读取
- 存储限制约为 5-10MB
- 用相同的 URL 打开多个选项卡/窗口会为每个选项卡/窗口创建 `sessionStorage`
- 有同源策略限制



**作用域**：`sessionStorage` 与 `localStorage` 一样需要同一浏览器同源文档这一条件。不仅如此，`sessionStorage` 的作用域还被限定在了窗口中，也就是说，只有同一浏览器、同一窗口的同源文档才能共享数据。但是如果是一个窗口中，有两个同源的 `iframe` 元素的话，这两个 `iframe` 的 `sessionStorage` 是可以互通的



**使用：**



```js
// 保存数据到 sessionStorage
sessionStorage.setItem('key', 'value');

// 从 sessionStorage 获取数据
let data = sessionStorage.getItem('key');

// 从 sessionStorage 删除保存的数据
// 在删除的时候如果 key 值错误，不会报错
sessionStorage.removeItem('key');

// 从 sessionStorage 删除所有保存的数据
sessionStorage.clear();
```



但当要存储的数据是一个对象或是数组的时候，直接存储是不行的，需要将对象序列化



存储数据前：傅用 `JSON.stringify` 将对象转换成字符串

获取数据后：使用用 `JSON.parse` 将字符串转换成对象



```js
var user = {
  username: 'zs',
  password: '123456'
}

user = JSON.stringify(user)
sessionStorage.setItem('user', user)
var account = sessionStorage.getItem('user')
account = JSON.parse(account)
```



## Cookie



由于 HTTP 是一种无状态的协议，服务器单从网络连接上是无法知道客户身份的。这时候服务器就需要给客户端颁发一个 cookie，用来确认用户的身份。



原理：web 服务器通过在 http 响应消息头增加 `Set-Cookie` 响应头字段将 Cookie 信息发送给浏览器，浏览器则通过在 http 请求消息中增加 Cookie 请求头字段将 Cookie 回传给 web 服务器。



- 主要用于服务器端读取(也可以在客户端读取)，单个大小必须小于 4KB
- 一般存储必须与后续 XHR 请求一起发送回服务器的数据，每次都会携带在 HTTP 头中，如果使用 cookie 保存过多数据会带来性能问题
- 原生 API 不如 storage 友好，需要自己封装函数
- 它的过期时间取决于类型，过期时间可以从服务器端或客户端设置。如果在浏览器端生成 Cookie，默认是关闭浏览器后失效
- 可以通过将该 cookie 的 `httpOnly` 标志设置为 `true` 来确保 cookie 的安全。这将阻止客户端访问该 cookie
- 有同源策略限制



**使用：**



服务端向客户端发送的 cookie



```
Set-Cookie: <cookie-name>=<cookie-value> (name可选)
Set-Cookie: <cookie-name>=<cookie-value>;(可选参数1);(可选参数2)
```



客户端设置 cookie



```js
document.cookie = "<cookie-name>=<cookie-value>;(可选参数1);(可选参数2)"
```



**可选参数：**



- `Expires=<date>`：cookie 的最长有效时间，若不设置则 cookie 生命期与会话期相同
- `Max-Age=<non-zero-digit>`：cookie 生成后失效的秒数
- `Domain=<domain-value>`：指定 cookie 可以送达的主机域名，若一级域名设置了则二级域名也能获取。
- `Path=<path-value>`：指定一个URL，例如指定path=/docs，则”/docs”、”/docs/Web/“、”/docs/Web/Http”均满足匹配条件
- `Secure`：必须在请求使用 SSL 或 HTTPS 协议的时候 cookie 才回被发送到服务器
- `HttpOnly`：客户端无法更改 Cookie，客户端设置 cookie 时不能使用这个参数，一般是服务器端使用



**可选前缀：**



- `__Secure-`：以 `__Secure-` 为前缀的 cookie，必须与 secure 属性一同设置，同时必须应用于安全页面（即使用 HTTPS）
- `__Host-`：以 `__Host-` 为前缀的 cookie，必须与 secure 属性一同设置，同时必须应用于安全页面（即使用 HTTPS）。必须不能设置 domian 属性（这样可以防止二级域名获取一级域名的 cookie），path 属性的值必须为”/“



**示例：**



```
Set-Cookie: __Secure-ID=123; Secure; Domain=example.com
Set-Cookie: __Host-ID=123; Secure; Path=/

document.cookie = "__Secure-KMKNKK=1234;Sercure"
document.cookie = "__Host-KMKNKK=1234;Sercure;path=/"
```



## Session



Session 是在无状态的 HTTP 协议下，服务端记录用户状态时用于标识具体用户的机制。它是在服务端保存的用来跟踪用户的状态的数据结构，可以保存在文件、数据库或者集群中。当服务器收到请求需要创建 `session` 对象时，首先会检查客户端请求中是否包含 `sessionid`。如果有，则服务器将根据该 `id` 返回对应 `session` 对象。如果客户端请求中没有 `sessionid`，服务器会创建新的 `session` 对象，并把 `sessionid` 在本次响应中返回给客户端。



在浏览器关闭后这次的 `session` 就消失了，下次打开就不再拥有这个 `session`。其实并不是 `session` 消失了，而是 `sessionid` 变了，服务器端可能还是存着你上次的 `sessionid` 及其 `session` 信息，只是他们是无主状态。大多数的应用都是用 Cookie 来实现 Session 跟踪的，第一次创建 `session` 的时候，服务端会在 HTTP 协议中告诉客户端，需要在 `cookie` 里面记录一个 `sessionid`，以后每次请求把这个会话 `id` 发送到服务器



如果用户禁用 Cookie，则要使用 URL 重写，可以通过 `response.encodeURL(url)` 进行实现；API对 `encodeURL` 的结束为，当浏览器支持 cookie 时，url 不做任何处理；当浏览器不支持 cookie 的时候，将会重写 URL 将 `sessionid` 拼接到访问地址后。





**与 Cookie 的关系与区别：**



- Session 是在服务端保存的一个数据结构，用来跟踪用户的状态，这个数据可以保存在集群、数据库、文件中，Cookie 是客户端保存用户信息的一种机制，用来记录用户的一些信息，也是实现 Session 的一种方式
- Cookie 的安全性一般，他人可通过分析存放在本地的 Cookie 并进行 Cookie 欺骗。在安全性第一的前提下，选择 Session 更优。重要交互信息比如权限等就要放在 Session 中，一般的信息记录放 Cookie 就好了
- 单个 Cookie 保存的数据不能超过 4K，很多浏览器都限制一个站点最多保存 20 个 Cookie。 Session 大小没有限制
- 当访问增多时，Session 会较大地占用服务器的性能。考虑到减轻服务器性能方面，应当适时使用 Cookie
- Session 的运行依赖 Session ID，而 Session ID 是存在  Cookie 中的。也就是说，如果浏览器禁用了 Cookie，Session也会失效



![2021/04/01/TOIMG2e7b00401022424N.png](https://picturebed.tumiblog.top/2021/04/01/TOIMG2e7b00401022424N.png)



## 参考文章



- [Local Storage vs Session Storage vs Cookie](https://krishankantsinghal.medium.com/local-storage-vs-session-storage-vs-cookie-22655ff75a8)
- [细说localStorage, sessionStorage, Cookie, Session](https://juejin.cn/post/6844903587764502536#heading-5)
- [HTML5 Web 存储（localStorage和sessionStorage）](https://blog.csdn.net/sleepwalker_1992/article/details/82832123)
- [一张图看懂cookie、 session、localStorage和sessionStorage 之间的区别和使用](https://blog.csdn.net/weixin_43522687/article/details/87694897)