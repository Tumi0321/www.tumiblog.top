---
title: link 和 @import 的区别
date: 2021-2-6
tags:
  - CSS 基础
categories:
  - CSS
---

## link



这里的 link 方式指的是使用 `<link>` 标签在 HTML 头部中引入外部的 CSS 文件

```html
<head> 
  <link rel="stylesheet" type="text/css" href="style.css">
</head>
```

这是最常见的引入  CSS 的方式。使用这种方式，CSS 代码只存在于单独的 CSS 文件中，所以具有良好的可维护性。并且 CSS 文件会在第一次加载时引入，后面切换页面时只需要加载 HTML 文件即可



## @import



导入方式指的是使用 CSS 规则引入外部 CSS 文件

```html
<style>
  @import url( style.css);
</style>
```



## 区别



- link 是 XHTML 标签，除了加载 CSS 外，还可以定义 RSS、rel 连接属性；@import 属于 CSS 范畴，只能加载 CSS
- link 引入 CSS 时，在页面载入同时加载；@import 需要页面网页完全载入以后加载。所以会出现一开始没有 css 样式，闪烁一下出现样式后的页面（网速慢的情况下）
- link 是 XHTML 标签，无兼容问题；@import 是在 CSS2.1 提出，低版本的浏览器不支持
- link 支持使用 javascript 控制  DOM 去改变样式；而 @import 不支持





最近看到一句话“link方式的样式的权重 高于 @import 的权重”



以下只是测试之后的结论（可能存在争议）



1. 当 `@import` 与内联样式一起时，内联样式 > 导入样式
2. 除了第一种情况，其它时候以”CSS 样式优先级“为参考