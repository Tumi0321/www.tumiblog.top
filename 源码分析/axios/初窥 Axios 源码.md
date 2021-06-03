---
title: 初窥 Axios 源码
date: 2021-06-01 19:20
tags:
  - Axios
categories:
  - 源码分析

---

## 目录结构



以下只列出了核心的目录或文件。



```
├─/dist/                            # 项目输出目录
├─/lib/                             # 项目源码目录
│  ├─/adapters/                     # 定义请求适配器目录
│  │  ├─http.js                     # 实现 http 适配器(包装 http 包)
│  │  ├─xhr.js                      # 实现 xhr 适配器(包装 xhr 对象)
│  ├─/cancel/                       # 定义取消功能
│  ├─/core/                         # 一些核心功能
│  │  ├─Axios.js                    # axios 的核心主类
│  │  ├─dispatchRequest.js          # 用来调用 http 请求适配器方法发送请求的函数
│  │  ├─InterceptorManager.js       # 拦截器的管理器
│  │  ├─settle.js                   # 根据 http 响应状态，改变 Promise 的状态
│  ├─/helpers/                      # 一些辅助方法
│  ├─axios.js                       # 对外暴露接口
│  ├─defaults.js                    # axios 的默认配置
│  └─utils.js                       # 公用工具
├─package.json                      # 项目信息
├─index.d.ts                        # 配置 TypeScript 的声明文件
└─index.js                          # 入口文件
```



## 运行流程



![2021/06/01/TOIMG40a860601064152N.png](https://picturebed.tumiblog.top/2021/06/01/TOIMG40a860601064152N.png)



## 入口文件



`index.js` 是项目的入口文件，只加载了 axios 对外暴露的接口。



```js
module.exports = require('./lib/axios');
```



实则 `lib/axios.js` 才是整个项目的入口文件，也就是 axios API 接口。



## axios API



观察 `axios.js` 代码可以看到最后导出的是 `axios`，而 `axios` 是 `createInstance` 函数返回的 `instance`。



```js
function createInstance(defaultConfig) {
  // 1.创建一个 Axios 实例
  var context = new Axios(defaultConfig);
  // 2.改变 request 函数的 this 指向，相当于 Axios.prototype.request.apply(context)
  var instance = bind(Axios.prototype.request, context);

  // 3.将 Axios 原型对象上的方法拷贝到 instance 上：request()、get()、post()、put()、delete()
  utils.extend(instance, Axios.prototype, context);

  // 将 Axios 实例对象上的属性拷贝到 instance 上：defaluts 和 interceptors 属性
  utils.extend(instance, context);

  return instance;
}

// 创建要导出的默认实例
var axios = createInstance(defaults);

// 添加 create 方法，用于创建新实例
axios.create = function create(instanceConfig) {
  // 将默认配置与实例配置合并
  return createInstance(mergeConfig(axios.defaults, instanceConfig));
};

// 导出 axios
module.exports.default = axios;
```



`createInstance` 函数首先创建了一个 `Axios` 实例，然后通过 `bind()` 返回一个函数用于执行 `request` 函数。



```js
// bind.js
module.exports = function bind(fn, thisArg) {
  // wrap 这个函数实际上就是我们使用的 axios()
  return function wrap() {
    var args = new Array(arguments.length);
    for (var i = 0; i < args.length; i++) {
      args[i] = arguments[i];
    }
    // thisArg 是 Axios 实例，args 是传给 axios() 的参数
    return fn.apply(thisArg, args);
  };
};
```



然后通过 `utils.extend()` 将 `Axios` 原型上的方法拷贝到 `instance`。



查看 `Axios.js` 能看到原型上添加了哪些方法，以下只展示了别名方法。



```js
// 为支持的请求方法提供别名
utils.forEach(['delete', 'get', 'head', 'options'], function forEachMethodNoData(method) {
  /*eslint func-names:0*/
  Axios.prototype[method] = function(url, config) {
    return this.request(mergeConfig(config || {}, {
      method: method,
      url: url,
      data: (config || {}).data
    }));
  };
});

utils.forEach(['post', 'put', 'patch'], function forEachMethodWithData(method) {
  /*eslint func-names:0*/
  Axios.prototype[method] = function(url, data, config) {
    return this.request(mergeConfig(config || {}, {
      method: method,
      url: url,
      data: data
    }));
  };
});
```



可以看到，这些方法实际上也是调用了 `request` 方法。



再将原型上的属性 `defaults` 和 `interceptors` 拷贝到 `instance`。



```js
// Axios.js
function Axios(instanceConfig) {
  this.defaults = instanceConfig;
  this.interceptors = {
    request: new InterceptorManager(),
    response: new InterceptorManager()
  };
}
```



接着向 `axios` 添加 `create` ，该方法就是我们使用 Axios 创建新实例的方法。原理与 `axios` 一样，都是调用 `createInstance` 函数。但是新创建的 `instance` 是没有 `axios` 后面添加的一些方法: `create()`、`CancelToken()`、`all()`。



```js
// axios.js
// 以下方法是 create 创建的实例所没有的（只截取了部分）
axios.Cancel = require('./cancel/Cancel');
axios.CancelToken = require('./cancel/CancelToken');
axios.isCancel = require('./cancel/isCancel');
```



最后将 `axios` 导出。`axios` 是一个用于执行 `request` 方法的函数，它拥有 `Axios` 上的所有属性和方法，在功能上是 `Axios` 的实例。



## 拦截器管理器



拦截器管理器（InterceptorManager），是对传递给请求或响应拦截器的参数进行管理。



`Axios` 构造函数中就创建了两个拦截管理器（请求拦截和响应拦截）。



```js
function Axios(instanceConfig) {
  this.defaults = instanceConfig;
  this.interceptors = {
    // 创建一个请求拦截管理器
    request: new InterceptorManager(),
    // 创建一个响应拦截管理器
    response: new InterceptorManager()
  };
}
```



拦截器管理器是一个构造函数，只有一个属性 `handlers`，用来存放传递给拦截器的回调函数。



```js
function InterceptorManager() {
  this.handlers = [];
}
```



`InterceptorManager` 原型上有个 `use` 方法，该方法就是我们使用拦截器时所调用的方法。



```js
/**
 * 向堆栈添加一个新的拦截器
 *
 * @param {Function} 处理成功的回调函数
 * @param {Function} 处理失败的回调函数
 *
 * @return {Number} 稍后用于移除拦截器的 ID
 */
InterceptorManager.prototype.use = function use(fulfilled, rejected, options) {
  this.handlers.push({
    fulfilled: fulfilled,
    rejected: rejected,
    synchronous: options ? options.synchronous : false,
    runWhen: options ? options.runWhen : null
  });
  return this.handlers.length - 1;
};
```



`use` 方法返回的是当前拦截器在数组中的下标，用于 `eject` 方法除拦截器。



```js
/**
 * 从堆栈中移除一个拦截器
 *
 * @param {Number} id `usr` 返回的 ID
 */
InterceptorManager.prototype.eject = function eject(id) {
  if (this.handlers[id]) {
    this.handlers[id] = null;
  }
};
```



原型上还添加了一个 `forEach` 方法，用于调用 `request` 函数时遍历拦截器回调函数。



```js
/**
 * 遍历所有注册的拦截器
 *
 * 这个方法会跳过调用 `eject` 成为 `null` 的拦截器
 *
 * @param {Function} fn 为每个拦截器调用的函数
 */
InterceptorManager.prototype.forEach = function forEach(fn) {
  utils.forEach(this.handlers, function forEachHandler(h) {
    if (h !== null) {
      fn(h);
    }
  });
};
```



## request 函数



上面多次提到 `request` 函数，`Axios.prototype.request` 函数是为了将请求拦截器、处理后的请求（dispatchRequest）和响应拦截器通过 promise 链串连起来。不管发送哪种类型的请求，都调用了 `request` 函数。



以下来看看 `request`（Axios.js） 具体做了哪些操作：

首先对配置进行了一些处理。



```js
Axios.prototype.request = function request(config) {
  // 1.合并配置
  config = mergeConfig(this.defaults, config);

  // 2.设置 method，默认为 get
  if (config.method) {
    // 将 method 转换为大写
    config.method = config.method.toLowerCase();
  } else if (this.defaults.method) {
    config.method = this.defaults.method.toLowerCase();
  } else {
    config.method = 'get';
  }
};
```



然后将请求和响应拦截器的成功和回调函数提取成执行链。



```js
// 用于存放请求拦截器的成功和失败回调函数
var requestInterceptorChain = []

this.interceptors.request.forEach(function unshiftRequestInterceptors (interceptor) {
  // 后添加的请求拦截器保存在数组的前面
  requestInterceptorChain.unshift(interceptor.fulfilled, interceptor.rejected)
})

// 用于存放响应拦截器的成功和失败回调函数
var responseInterceptorChain = []
// 后添加的响应拦截器保存在数组的后面
this.interceptors.response.forEach(function pushResponseInterceptors (interceptor) {
  responseInterceptorChain.push(interceptor.fulfilled, interceptor.rejected)
})
```



这就是为什么请求拦截器后添加先执行，响应拦截器后添加后执行。



处理完回调函数后就是执行回调函数了，请求拦截器要在发送请求前执行，所以先执行 `requestInterceptorChain`。



```js
// 拦截后的配置
var newConfig = config
// 执行请求拦截器
while (requestInterceptorChain.length) {
  // 取出第一个请求拦截器成功的回调函数
  var onFulfilled = requestInterceptorChain.shift()
  // 取出第一个请求拦截器失败的回调函数
  var onRejected = requestInterceptorChain.shift()
  try {
    // 这里就是为什么要将配置 return，不然拿不到 newConfig
    newConfig = onFulfilled(newConfig)
  } catch (error) {
    onRejected(error)
    break
  }
}
```



响应拦截器执行完之后就会发送请求了，但是在请求发送前会进行调度，也就是对请求数据进行一个处理。



```js
// 存放 promise
var promise

try {
  promise = dispatchRequest(newConfig)
} catch (error) {
  return Promise.reject(error)
}
```



`dispatchRequest` 函数是一个调度函数，接收配置进行处理，下文会对它进行解析。



发送请求后，服务器响应数据，在我们拿到数据之前会执行响应拦截器。



```js
// 通过 promise 的 then() 串连起所有的响应拦截器
while (responseInterceptorChain.length) {
  promise = promise.then(responseInterceptorChain.shift(), responseInterceptorChain.shift())
}

return promise
```



最后返回 promise，然后就是开发者对响应后的一个数据处理了。



## 调度请求



调度请求函数（dispatchRequest）用于转换请求体或响应体的数据。



```js
/**
 * 使用配置的适配器向服务器发送请求
 *
 * @param {object} config 用于请求的配置
 * @returns {Promise} 成功的 promise
 */
module.exports = function dispatchRequest(config) {
  // 确保 headers 存在
  config.headers = config.headers || {};

  // 转换请求数据
  config.data = transformData.call(
    config,
    config.data,
    config.headers,
    config.transformRequest
  );

  // 扁平化 config 中所有的 headers
  config.headers = utils.merge(
    config.headers.common || {},
    config.headers[config.method] || {},
    config.headers
  );

  // 得到当前环境对应的请求适配器（xhr、http）
  var adapter = config.adapter || defaults.adapter;

  return adapter(config).then(function onAdapterResolution(response) {
    // 转换响应数据
    response.data = transformData.call(
      config,
      response.data,
      response.headers,
      config.transformResponse
    );

    return response;
  }, function onAdapterRejection(reason) {
    if (!isCancel(reason)) {
      throwIfCancellationRequested(config);

      // Transform response data
      if (reason && reason.response) {
        reason.response.data = transformData.call(
          config,
          reason.response.data,
          reason.response.headers,
          config.transformResponse
        );
      }
    }

    return Promise.reject(reason);
  });
};
```



`transformRequest` 主要是对请求前数据做了 json 转换处理。



```js
// default.js
// 请求转换器
transformRequest: [function transformRequest (data, headers) {
  // 如果 data 是对象，指定请求体参数格式为 json，并将参数数据对象转换为 json
  if (utils.isObject(data) || (headers && headers['Content-Type'] === 'application/json')) {
    setContentTypeIfUnset(headers, 'application/json')
    return JSON.stringify(data)
  }
  return data
}],
```



转换之后，获取当前环境的适配器并发送了请求（XHR请求，本篇不涉及服务端请求）。



```js
// default.js
// 获取默认适配器
function getDefaultAdapter() {
  var adapter;
  if (typeof XMLHttpRequest !== 'undefined') {
    // For browsers use XHR adapter
    adapter = require('./adapters/xhr');
  } else if (typeof process !== 'undefined' && Object.prototype.toString.call(process) === '[object process]') {
    // For node use HTTP adapter
    adapter = require('./adapters/http');
  }
  return adapter;
}
```



获取响应数据之后通过 `transformResponse` 对数据进行了 `json` 解析，最后返回一个成功的 promise。



## 请求模块



`/adapters/` 目录下面的模块是在收到响应后处理发送请求和处理返回的 Promise 模块。`http.js` 是普通 HTTP 模块；`xhr.js` 是 ajax 请求模块。



`xhr.js` 模块返回的是一个 promise，主要是创建了 XHR 对象，通过 config 进行了一些初始化配置和事件绑定。构建了 response 的结构。



```js
module.exports = function xhrAdapter(config) {
  // 返回一个 promise
  return new Promise(function dispatchXhrRequest(resolve, reject) {
    // 创建 xhr 对象
    var request = new XMLHttpRequest();
    // 初始化请求
    request.open(config.method.toUpperCase(), buildURL(fullPath, config.params, config.paramsSerializer), true);
    // 设置请求超时
    request.timeout = config.timeout;
    function onloadend() {
      if (!request) {
        return;
      }
      // 准备 response 对象
      var responseHeaders = 'getAllResponseHeaders' in request ? parseHeaders(request.getAllResponseHeaders()) : null;
      var responseData = !responseType || responseType === 'text' ||  responseType === 'json' ?
        request.responseText : request.response;
      var response = {
        data: responseData,
        status: request.status,
        statusText: request.statusText,
        headers: responseHeaders,
        config: config,
        request: request
      };

      // 根据响应状态码来确定请求的 promise 的结果状态（成功/失败）
      settle(resolve, reject, response);

      // 清空请求对象
      request = null;
    }
    // 发送请求，指定请求体数据，可能是 null
    request.send(requestData);
  });
};
```



请求或失败的函数并不是直接调用的，而是通过了 `settle` 函数。



```js
// settle.js
module.exports = function settle(resolve, reject, response) {
  var validateStatus = response.config.validateStatus;
  if (!response.status || !validateStatus || validateStatus(response.status)) {
    resolve(response);
  } else {
    reject(createError(
      'Request failed with status code ' + response.status,
      response.config,
      null,
      response.request,
      response
    ));
  }
};
```



而是否调用 `resolve` 的判断条件是 `validateStatus` 函数。



```js
// 判断响应状态码的合法性：[200, 299]
validateStatus: function validateStatus(status) {
  return status >= 200 && status < 300;
}
```



## 中断请求



可以使用 `CancelToken.source` 工厂方法创建 cancel token。



```js
CancelToken.source = function source() {
  var cancel;
  var token = new CancelToken(function executor(c) {
    cancel = c;
  });
  return {
    token: token,
    cancel: cancel
  };
};
```



创建的 `CancelToken` 实例是用于将来取消请求的 cancelPromise；接收的参数 `c` ，是用来定义错误信息和取消请求的函数。



```js
// CancelToken.js
function CancelToken(executor) {
  if (typeof executor !== 'function') {
    throw new TypeError('executor must be a function.');
  }

  // 为取消请求准备一个 promise 对象，并保存 resolve 函数
  var resolvePromise;
  this.promise = new Promise(function promiseExecutor(resolve) {
    resolvePromise = resolve;
  });

  // 保存当前 token 对象
  var token = this;

  // 立即执行接收的执行器函数，并传入用于取消请求的 cancel 对象
  executor(function cancel(message) {
    // 如果 token 中有 reason 了，说明请求已取消
    if (token.reason) {
      return;
    }

    // 创建错误对象
    token.reason = new Cancel(message);w
    resolvePromise(token.reason);
  });
}
```



而 cancelToken 是一个 cancelPromise，只有调用了 `cancel()` 后才会执行 `then`。



```js
// xhr.js
// 如果配置了 cancelToken
if (config.cancelToken) {
  // 指定用于中断请求的回调函数
  config.cancelToken.promise.then(function onCanceled (cancel) {
    if (!request) {
      return
    }

    // 中断请求
    request.abort()
    // 调用 promise reject
    reject(cancel)
    // Clean up request
    request = null
  })
}
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



原理都是创建实例，使用 `sourse` 方法更加简洁。



## 并发



原理是使用 `Promise.all` 方法。



```js
axios.all = function all(promises) {
  return Promise.all(promises);
};
```



此时再去看 #运行流程 ，对 Axios 的执行过程就有一个大致的了解。