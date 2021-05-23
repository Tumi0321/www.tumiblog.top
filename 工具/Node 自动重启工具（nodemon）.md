---
title: Node 自动重启工具（nodemon）
date: 2021-5-23
tags:
  - Node.js
categories:
  - 工具
---

当运行一个 node.js 文件时，每次修改文件都需要重启服务，这降低了我们的开发效率。而 nodemon 就是为了解决该问题的一个工具，它会在检测到目录中的文件发生变化时自动重启服务。



## 安装



```bash
# 全局安装
npm install -g nodemon

# 开发环境安装
npm install --save-dev nodemon
```



## 使用



安装之后就可以使用 nodemon 来代替 node 命令来启动应用程序。可以传递通常会传递给你应用程序的所有参数。



```bash
nodemon [your node app]

# 例如
nodemon ./server.js
```



如果应用程序需要收受一个主机或者商品作为参数，可以这样启动它：



```bash
nodemon ./server.js localhost 8080
```



同样可以运行 debug 模式



```bash
nodemon --inspect  ./server.js
```



查看帮助可以显示更多选项



```bash
nodemon -h 

# 或者
nodemon -help
```



## 手动重启



在运行 nodemon 的同时，如果需要手动重启应用程序，不必停止并重启 nodemon，只需要输入 `rs` 并回车，nodemon 将自动重启进程。



## 配置文件



nodemon 支持本地和全局配置文件，通常以 nodemon.json 命名。它们的执行优先级：命令行>本地配置>全局配置。



配置文件可以使用任何命令行参数作为 JSON 键值，以下为[官方示例](https://github.com/remy/nodemon/blob/master/doc/sample-nodemon.md)：



```json
{
  "restartable": "rs",
  "ignore": [
    ".git",
    "node_modules/**/node_modules"
  ],
  "verbose": true,
  "execMap": {
    "js": "node --harmony"
  },
  "events": {
    "restart": "osascript -e 'display notification \"App restarted due to:\n'$FILENAME'\" with title \"nodemon\"'"
  },
  "watch": [
    "test/fixtures/",
    "test/samples/"
  ],
  "env": {
    "NODE_ENV": "development"
  },
  "ext": "js,json"
}
```



- **restartable**



手动重启服务的命令，默认为 rs ，可以使用任意字符串代替。除了字符串值外，还可以设置 false 值，意思是当 nodemon 影响了你自己的终端命令时，设置为 false 则不会在 nodemon 运行期间监听 rs 的重启命令。



- **ignore**



当内容被修改时，忽略的文件后缀名或者文件夹，文件路径相对于 nodemon.json 所在的路径。



nodemon 会默认忽略一些文件，默认忽略的文件有



```
.git,
node_modules,
bower_components,
.sass-cache
```



如果这些文件想要加入监控，需要重写默认忽略参数字段 `ignoreRoot`，并在 `watch` 字段中将 node_modules 文件路径加入监控



```json
{
    "ignoreRoot": [".git", "bower_components", ".sass-cache"],
    "watch": [
    "test/fixtures/",
    "test/samples/"
  ],
}
```



- **verbose**



`true` 表示输出详细启动与重启信息，如下图：



![2021/05/23/TOIMGa61030523112218N.png](https://picturebed.tumiblog.top/2021/05/23/TOIMGa61030523112218N.png)



`false` 表示不输出这些运行信息，如下图：



![2021/05/23/TOIMG3a1ec0523112240N.png](https://picturebed.tumiblog.top/2021/05/23/TOIMG3a1ec0523112240N.png)



- **execMap**



运行服务的后缀名和对应的运行命令，"js": "node --harmony" 表示用 `nodemon` 代替 `node  --harmony` 运行 js 后缀文件；"" 指没有后缀名的文件。默认的 [defaults.js](https://github.com/remy/nodemon/blob/master/lib/config/defaults.js) 配置文件会识别一些文件：



```json
{
  execMap: {
    py: 'python',
    rb: 'ruby',
    ts: 'ts-node'
  }
}
```



- **events**



表示 nodemon 运行到某些状态时的一些触发事件，总共有五个状态：



1. start：子进程（即监控的应用）启动
2. crash：子进程崩溃，不会触发 exit
3. exit：子进程完全退出，不是非正常的崩溃
4. restart：子进程重启
5. config：update - nodemon 的 config 文件改变



状态后面可以带标准输入输出语句，比如 mac 系统下设置： "start": "echo 'app start'"，那么启动应用时会输出 app start 信息，其他类似命令如 `ls`，`ps` 等等标准命令都可以在这里定义。除此之外，也可以写 js 来监控。



- **watch**



监控的文件夹路径或者文件路径，`'*.*'` 代表监控所有。



- **env**



运行环境 development 是开发环境，production 是生产环境。



- **ext**



监控指定后缀名的文件，多个用逗号隔开。默认监控的后缀文件：.js, .coffee, .litcoffee, .json。但是对于没有文件后缀的文件，暂时找不到怎么用 nodemon 去监控，就算在 watch 中包含了，nodemon 也会忽略掉。



注：关于监控以及忽略文件修改有个顺序的问题，或者说优先级，首先 nodemon 会先读取 watch 里面需要监控的文件或文件路径，再从文件中选择监控 ext 中指定的后缀名，最后去掉从 ignore 中指定的忽略文件或文件路径。



- **legacy-watch**



nodemon 使用 [Chokidar](https://www.npmjs.com/package/chokidar) 作为底层监控系统，但是如果监控失效，或者提示没有需要监控的文件时，就需要使用轮询模式（polling mode），即设置 legacy-watch 为 true，也可以在命令行中指定：



```bash
nodemon --legacy-watch 

# 或者
nodemon -L 
```



不想在项目中单独维护 nodemon.json 配置文件，可在 package.json 中配置



```json
{
  "name": "nodemon",
  "homepage": "http://nodemon.io",
  "...": "... other standard package.json values",
  "nodemonConfig": {
    "ignore": ["test/*", "docs/*"],
    "delay": "2500"
  }
}
```



## 延迟重启





延迟重启可以避免在短时间内频繁重启服务



```bash
# 指定秒
nodemon -delay10 main.js

# 指定毫秒作为浮点数
nodemon --delay 2.5 server.js

# 指定毫秒为单位
nodemon --delay 2500ms server.js
```



## 参考文档



- [nodemon README.md](https://github.com/remy/nodemon)
- [nodemon default.js](https://github.com/remy/nodemon/blob/master/lib/config/defaults.js)
- [nodemon 基本配置与使用](https://www.cnblogs.com/JuFoFu/p/5140302.html)