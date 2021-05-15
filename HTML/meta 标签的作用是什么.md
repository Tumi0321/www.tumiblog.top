---
title: meta 标签的作用是什么
date: 2021-1-27
tags:
  - HTML 标签
categories:
  - HTML
---

::: warning
注：以下内容并不完整
:::

## 元数据



用于描述数据的数据，用来构建 HTML 文档的基本结构。它们本身不是文档内容，但提供了关于后面文档内容的信息



如 title、base、meta 都是元数据元素



## meta 标签



`<meta>` 标签提供了 HTML 文档的元数据，比如针对搜索引擎和更新频度的描述和关键词。元数据不会显示在客户端，但是会被浏览器解析。



元数据通常以 名称/值 对出现（如果没有提供 name 属性，那么名称/值对中的名称会采用 http-equiv 属性的值）。



每个 meta 标签只能用于一种用途，如果想定义多个文档信息，则需要在 head 标签中添加多个 meta 标签



meta 标签包含五个属性：



- charset（HTML5 新增属性）
- content
- http-equiv
- name
- scheme（HTML5 不支持）



## charset



定义文档的字符编码。

```html
<!-- HTML5 推荐方式-->
<meta charset="utf-8">
<!-- 旧的HTML --> 
<meta http-equiv=" content-Type" content=" text/html ; charset=utf-8">
```

理论上可以使用任何字符的编码，但并不是所有的浏览器都能够理解它们。



## content



能够给 http-equiv 或 name 属性提供一个值。



## http-equiv



把 content 属性关联到 HTTP 头部。



http-equive 属性在 HTML4 中有很多值，在 HTML5 中只有 refresh、default-style、content-type 可用。



- **content-type 规定文档的字符编码**

```html
<meta http- equiv=" content -type" content=" text/html ;charset=utf-8">
```

- **default-style 规定要使用的首选样式表**

```html
<meta http- equiv="default-style" content="the document's preferred stylesheet">
```

**注释：**上面 content 属性的值必须匹配同一文档中的一个 link 元素上的 title 属性的值，或者必须匹配同一文档中的一个 style 元素上的 title 属性的值。



- **refresh 定义文档自动刷新的时间间隔**

```html
<meta http-equiv="refresh" content="3;URL=https://www.baidu.com" />
```

**注释：**content 的第一个值的单位为秒，当后面不跟 URL 时，则是刷新页面



## name



把 content 属性关联到一个名称，作为 名/值 中的名。



name 用来表示元数据的类型，表示当 meta  标签的具体作用。content 属性用来提供值。

```html
<head>
<title>示例</title>
<meta name= ”keywords”content=" 描述网站内容的关键词，以逗号隔开，用于SEO搜索">
<meta name=" application name" content=" 当前页所属Web应用系统的名称">
<meta name=" descr iption" content=" 当前页的说明">
<meta name=" author" content="当前 页的作者名">
<meta name=" copyright" content= "版权信息">
<meta name="renderer" content="renderer是为双核浏览器准备的，用于指定双核浏览器默认以何种方式渲染页面">
<meta name="viewreport" content="它提供有关视口初始大小的提示，仅供移动设备使用">
</head>
```

## 常用 meta 属性



```html
  <!-- 声明文档使用的字符编码 -->
  <meta charset='utf-8'>
  <!-- 优先使用 IE 最新版本和 Chrome -->
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1"/>
  <!-- 页面描述 -->
  <meta name="description" content="不超过150个字符"/>
  <!-- 页面关键词 -->
  <meta name="keywords" content=""/>
  <!-- 网页作者 -->
  <meta name="author" content="name, email@gmail.com"/>
  <!-- 搜索引擎抓取 -->
  <meta name="robots" content="index,follow"/>
  <!-- 为移动设备添加 viewport -->
  <meta name="viewport" content="initial-scale=1, maximum-scale=3, minimum-scale=1, user-scalable=no">
  <!-- `width=device-width` 会导致 iPhone 5 添加到主屏后以 WebApp 全屏模式打开页面时出现黑边 http://bigc.at/ios-webapp-viewport-meta.orz -->

  <!-- iOS 设备 begin -->
  <meta name="apple-mobile-web-app-title" content="标题">
  <!-- 添加到主屏后的标题（iOS 6 新增） -->
  <meta name="apple-mobile-web-app-capable" content="yes"/>
  <!-- 是否启用 WebApp 全屏模式，删除苹果默认的工具栏和菜单栏 -->

  <meta name="apple-itunes-app" content="app-id=myAppStoreID, affiliate-data=myAffiliateData, app-argument=myURL">
  <!-- 添加智能 App 广告条 Smart App Banner（iOS 6+ Safari） -->
  <meta name="apple-mobile-web-app-status-bar-style" content="black"/>
  <!-- 设置苹果工具栏颜色 -->
  <meta name="format-detection" content="telphone=no, email=no"/>
  <!-- 忽略页面中的数字识别为电话，忽略email识别 -->
  <!-- 启用360浏览器的极速模式(webkit) -->
  <meta name="renderer" content="webkit">
  <!-- 避免IE使用兼容模式 -->
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <!-- 不让百度转码 -->
  <meta http-equiv="Cache-Control" content="no-siteapp" />
  <!-- 针对手持设备优化，主要是针对一些老的不识别viewport的浏览器，比如黑莓 -->
  <meta name="HandheldFriendly" content="true">
  <!-- 微软的老式浏览器 -->
  <meta name="MobileOptimized" content="320">
  <!-- uc强制竖屏 -->
  <meta name="screen-orientation" content="portrait">
  <!-- QQ强制竖屏 -->
  <meta name="x5-orientation" content="portrait">
  <!-- UC强制全屏 -->
  <meta name="full-screen" content="yes">
  <!-- QQ强制全屏 -->
  <meta name="x5-fullscreen" content="true">
  <!-- UC应用模式 -->
  <meta name="browsermode" content="application">
  <!-- QQ应用模式 -->
  <meta name="x5-page-mode" content="app">
  <!-- windows phone 点击无高光 -->
  <meta name="msapplication-tap-highlight" content="no">
    <!-- iOS 设备 end -->
  <meta name="msapplication-TileColor" content="#000"/>
  <!-- Windows 8 磁贴颜色 -->
  <meta name="msapplication-TileImage" content="icon.png"/>
  <!-- Windows 8 磁贴图标 -->

  <link rel="alternate" type="application/rss+xml" title="RSS" href="/rss.xml"/>
  <!-- 添加 RSS 订阅 -->
  <link rel="shortcut icon" type="image/ico" href="/favicon.ico"/>
  <!-- 添加 favicon icon -->

  <!-- sns 社交标签 begin -->
  <!-- 参考微博API -->
  <meta property="og:type" content="类型" />
  <meta property="og:url" content="URL地址" />
  <meta property="og:title" content="标题" />
  <meta property="og:image" content="图片" />
  <meta property="og:description" content="描述" />
  <!-- sns 社交标签 end -->
```