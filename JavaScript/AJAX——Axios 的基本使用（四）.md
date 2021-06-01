---
title: AJAX——Axios 的基本使用（四）
date: 2021-6-1
tags:
  - AJAX
  - Axios
categories:
  - JavaScript
---

Axios 也是对 AJAX 的一种实现，是一个基于 Promise 的 HTTP 库，可以用在浏览器和 Node.js 中使用。



## 特性



- 从浏览器中创建 [XMLHttpRequests](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)
- 从 node.js 创建 [http](http://nodejs.org/api/http.html) 请求
- 支持 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) API
- 拦截请求和响应
- 转换请求数据和响应数据
- 取消请求
- 自动转换 JSON 数据
- 客户端支持防御 [XSRF](http://en.wikipedia.org/wiki/Cross-site_request_forgery)



## 安装



使用 npm:



```bash
$ npm install axios
```



使用 bower:



```bash
$ bower install axios
```



使用 cdn:



```html
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
```



## 基本使用



执行 `GET` 请求



```js
// 为给定 ID 的 user 创建请求
axios.get('/user?ID=12345')
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });

// 上面的请求也可以这样做
axios.get('/user', {
  params: {
    ID: 12345
  }
})
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```



执行 `POST` 请求



```js
axios.post('/user', {
    firstName: 'Fred',
    lastName: 'Flintstone'
  })
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```



执行多个并发请求



```js
function getUserAccount() {
  return axios.get('/user/12345');
}
function getUserPermissions() {
  return axios.get('/user/12345/permissions');
}
axios.all([getUserAccount(), getUserPermissions()])
  .then(axios.spread(function (acct, perms) {
    // 两个请求现在都执行完成
  }));
```



## axios API



> axios(config)



```js
// 发送 POST 请求
axios({
  method: 'post',
  url: '/user/12345',
  data: {
    firstName: 'Fred',
    lastName: 'Flintstone'
  }
});
```



获取远端图片



```js
axios({
  method:'get',
  url:'http://bit.ly/2mTM3nY',
  responseType:'stream'
})
  .then(function(response) {
  response.data.pipe(fs.createWriteStream('ada_lovelace.jpg'))
});
```



> axios(url[,config])



```js
// 发送 GET 请求（默认的方法）
axios('/user/12345');
```



## 请求方法别名



为方便起见，为所有支持的请求方法提供了别名，以取代 axios API 的复杂写法。



- **axios.request(config)**
- **axios.get(url[, config])**
- **axios.delete(url[, config])**
- **axios.head(url[, config])**
- **axios.options(url[, config])**
- **axios.post(url[, data[, config]])**
- **axios.put(url[, data[, config]])**
- **axios.patch(url[, data[, config]])**



在使用别名方法时， `url`、`method`、`data` 这些属性都不必在配置中指定。



## 请求配置



这些是创建请求时可以用的配置选项。只有 `url` 是必需的。如果没有指定 `method`，请求将默认使用 `get` 方法。



```json
{
   // url 是用于请求的服务器 URL
  url: '/user',

  // method 是创建请求时使用的方法
  method: 'get', // default

  // baseURL 将自动加在 url 前面，除非 url 是一个绝对 URL。
  // 它可以通过设置一个 baseURL 便于为 axios 实例的方法传递相对 URL
  baseURL: 'https://some-domain.com/api/',

  // transformRequest 允许在向服务器发送前，修改请求数据
  // 只能用在 'PUT', 'POST' 和 'PATCH' 这几个请求方法
  // 后面数组中的函数必须返回一个字符串，或 ArrayBuffer，或 Stream
  transformRequest: [function (data, headers) {
    // 对 data 进行任意转换处理
    return data;
  }],

  // transformResponse 在传递给 then/catch 前，允许修改响应数据
  transformResponse: [function (data) {
    // 对 data 进行任意转换处理
    return data;
  }],

  // headers 是即将被发送的自定义请求头
  headers: {'X-Requested-With': 'XMLHttpRequest'},

  // params 是即将与请求一起发送的 URL 参数
  // 必须是一个无格式对象(plain object)或 URLSearchParams 对象
  params: {
    ID: 12345
  },

   // paramsSerializer 是一个负责 params 序列化的函数
  // (e.g. https://www.npmjs.com/package/qs, http://api.jquery.com/jquery.param/)
  paramsSerializer: function(params) {
    return Qs.stringify(params, {arrayFormat: 'brackets'})
  },

  // data 是作为请求主体被发送的数据
  // 只适用于这些请求方法 'PUT', 'POST', 和 'PATCH'
  // 在没有设置 transformRequest 时，必须是以下类型之一：
  // - string, plain object, ArrayBuffer, ArrayBufferView, URLSearchParams
  // - 浏览器专属：FormData, File, Blob
  // - Node 专属： Stream
  data: {
    firstName: 'Fred'
  },

  // timeout 指定请求超时的毫秒数(0 表示无超时时间)
  // 如果请求话费了超过 `timeout` 的时间，请求将被中断
  timeout: 1000,

  // withCredentials 表示跨域请求时是否需要使用凭证
  withCredentials: false, // default

  // adapter 允许自定义处理请求，以使测试更轻松
  // 返回一个 promise 并应用一个有效的响应 (查阅 [response docs](#response-api)).
  adapter: function (config) {
    /* ... */
  },

  // auth 表示应该使用 HTTP 基础验证，并提供凭据
  // 这将设置一个 `Authorization` 头，覆写掉现有的任意使用 `headers` 设置的自定义 `Authorization`头
  auth: {
    username: 'janedoe',
    password: 's00pers3cret'
  },

  // responseType 表示服务器响应的数据类型
  // 可以是 'arraybuffer', 'blob', 'document', 'json', 'text', 'stream'
  responseType: 'json', // default

  // responseEncoding 指示用于解码响应的编码
  // 注意:忽略'stream'或客户端请求的' responseType '
  responseEncoding: 'utf8', // default

   // xsrfCookieName 是用作 xsrf token 的值的 cookie 的名称
  xsrfCookieName: 'XSRF-TOKEN', // default

  // xsrfHeaderName 是携带 XSRF 令牌值的 HTTP 头的名称
  xsrfHeaderName: 'X-XSRF-TOKEN', // default

   // onUploadProgress 允许为上传处理进度事件
  onUploadProgress: function (progressEvent) {
    // Do whatever you want with the native progress event
  },

  // onDownloadProgress 允许为下载处理进度事件
  onDownloadProgress: function (progressEvent) {
    // 对原生进度事件的处理
  },

   // maxContentLength 定义允许的响应内容的最大尺寸
  maxContentLength: 2000,

  // validateStatus 定义对于给定的 HTTP 响应状态码是 resolve 或 reject  promise 。
  // 如果 validateStatus 返回 `true` (或者设置为 `null` 或 `undefined`)
  // promise 将被 resolve; 否则，promise 将被 rejecte
  validateStatus: function (status) {
    return status >= 200 && status < 300; // default
  },

  // maxRedirects 定义在 node.js 中 follow 的最大重定向数目
  // 如果设置为0，将不会 follow 任何重定向
  maxRedirects: 5, // default

  // socketPath 定义了node.js中使用的 UNIX
  // 例如 '/var/run/docker.sock' 向 docker 进程发送请求
  // 只能指定 socketPath 或 proxy
  // 如果两者都指定，则使用 socketPath
  socketPath: null, // default

  // httpAgent 和 httpsAgent 分别在 node.js 中用于定义在执行 http 和 https 时使用的自定义代理。
  // 允许像这样配置选项：
  // keepAlive 默认没有启用
  httpAgent: new http.Agent({ keepAlive: true }),
  httpsAgent: new https.Agent({ keepAlive: true }),

  // proxy 定义代理服务器的主机名称和端口
  // auth 表示 HTTP 基础验证应当用于连接代理，并提供凭据
  // 这将会设置一个 Proxy-Authorization 头，覆写掉已有的通过使用 header 设置的自定义 Proxy-Authorization 头。
  proxy: {
    host: '127.0.0.1',
    port: 9000,
    auth: {
      username: 'mikeymike',
      password: 'rapunz3l'
    }
  },

  // cancelToken 指定用于取消请求的 cancel token
  // （查看后面的 Cancellation 这节了解更多）
  cancelToken: new CancelToken(function (cancel) {
  })
}
```



## 创建实例



创建一个 axios 实例，每个实例都有自己的配置，实例本质上是一个函数。



> axios.create([config])



```js
const instance = axios.create({
  baseURL: 'https://some-domain.com/api/',
  timeout: 1000,
  headers: {'X-Custom-Header': 'foobar'}
});
```



axios 实例中没有取消请求和批量发送请求的方法



## 配置默认值



你可以指定将被用在各个请求的配置默认值



### 全局默认值



```js
axios.defaults.baseURL = 'https://api.example.com';
axios.defaults.headers.common['Authorization'] = AUTH_TOKEN;
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';
```



### 实例默认值



```js
// Set config defaults when creating the instance
const instance = axios.create({
  baseURL: 'https://api.example.com'
});

// Alter defaults after instance has been created
instance.defaults.headers.common['Authorization'] = AUTH_TOKEN;
```



## 拦截器



在请求或响应被 `then` 或 `catch` 处理前拦截它们。



```js
// 添加请求拦截器
axios.interceptors.request.use(function (config) {
    // 在发送请求之前做些什么
    return config;
  }, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
  });

// 添加响应拦截器
axios.interceptors.response.use(function (response) {
    // 对响应数据做点什么
    return response;
  }, function (error) {
    // 对响应错误做点什么
    return Promise.reject(error);
  });
```



如果你想在稍后移除拦截器，可以这样：



```js
const myInterceptor = axios.interceptors.request.use(function () {/*...*/});
axios.interceptors.request.eject(myInterceptor);
```



可以为自定义 axios 实例添加拦截器



```js
const instance = axios.create();
instance.interceptors.request.use(function () {/*...*/});
```



如果你指定了多个请求或响应拦截器



```js
// 添加第一个请求拦截器
axios.interceptors.request.use(function (config) {
    console.log("request interceptor1");
    return config;
  }, function (error) {
    return Promise.reject(error);
  });

// 添加第二个请求拦截器
axios.interceptors.request.use(function (config) {
    console.log("request interceptor2");
    return config;
  }, function (error) {
    return Promise.reject(error);
  });

// 添加第一个响应拦截器
axios.interceptors.response.use(function (response) {
    console.log("response interceptor1");
    return response;
  }, function (error) {
    return Promise.reject(error);
  });

// 添加第二个响应拦截器
axios.interceptors.response.use(function (response) {
    console.log("response interceptor2");
    return response;
  }, function (error) {
    return Promise.reject(error);
  });

// output：
// request interceptor2
// request interceptor1
// response interceptor1
// response interceptor1
```



请求拦截器后添加先执行，响应拦截器后添加后执行。



## 中断请求



使用 cancel token 取消请求



可以使用 `CancelToken.source` 工厂方法创建 cancel token，像这样：



```js
const CancelToken = axios.CancelToken;
const source = CancelToken.source();

axios.get('/user/12345', {
  cancelToken: source.token
}).catch(function(thrown) {
  // 判断是否是中断请求抛出的错误
  if (axios.isCancel(thrown)) {
    console.log('Request canceled', thrown.message);
  } else {
     // 处理错误
  }
});

axios.post('/user/12345', {
  name: 'new name'
}, {
  cancelToken: source.token
})

// 取消请求（message 参数是可选的）
source.cancel('Operation canceled by the user.');
```



还可以通过传递一个 executor 函数到 `CancelToken` 的构造函数来创建 cancel token： 



```js
const CancelToken = axios.CancelToken;
let cancel;

axios.get('/user/12345', {
  cancelToken: new CancelToken(function executor(c) {
    // executor 函数接收一个 cancel 函数作为参数
    cancel = c;
  })
});

// cancel the request
cancel();
```