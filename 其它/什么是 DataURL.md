---
title: 什么是 DataURL
date: 2020-8-10
tags:
  - DataURL
categories:
  - 其它
---

## 概述

**Data URL**，即前缀为 `data:` 协议的 URL，其允许内容创建者向文档中嵌入小文件。

传统的 url 指定的是一个远程服务器上的资源，而 DataURL 则是通过字符串来表示数据

## 语法

Data URLs 由四个部分组成：前缀(`data:`)、指示数据类型的 MIME 类型、如果非文本则为可选的 `base64` 标记、数据本身：

> data:[<mediatype>][;base64],<data>

`mediatype` 是个 MIME 类型的字符串，例如 `image/jpeg` 表示 JPEG 图像文件。如果被省略，则默认值为 `text/plain;charset=US-ASCII`

如果数据是文本类型，你可以直接将文本嵌入 (根据文档类型，使用合适的实体字符或转义字符)。如果是二进制数据，你可以将数据进行 base64 编码之后再进行嵌入。

## 使用

**Data URL** 可以代替传统 URL 将图片嵌入到网页中

两种方式的显示效果没有任何区别

传统的形式：

```html
<img src="image.jpeg" />
```

使用 Data RUL：

```html
<img src="data:image/jpg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/2wBDAAYEBQ....." />
```

### 优点和缺点

**优点：**

- 可以减少 http 请求。
- 可以在访问外部资源受限时使用
- 避免了图片重新上传，清理缓存的问题
- ....

使用`img` 标签的 `src` 属性指定一个远程服务器上的资源时，当网页加载时，每个资源都需要向服务器发送一次拉取请求，如果网页里嵌入过多的外部资源，会大大影响网页的加载速度。当使用 **Data URL** 将图片以字符串的形式嵌入网页时，只需要通过本地解析加载图片，提高了网页的加载速度，分担了服务器的压力。

**缺点：**

- 不能在客户端进行缓存。
- 多次使用时需要重复加载
- Base64 编码之后相对原文件较大
- 渲染时需要 `base64` 解码，需要消耗 `cpu` 资源。
- ....

由于 Base64 编码的数据大约比原始数据大 33％，也就是 Data URL 形式的图片会比二进制格式的图片体积大 1/3。并且浏览器进行解析的时候多消耗 53% 左右的 CPU 资源。Data URL 形式的图片不会被浏览器缓存，这意味着每次访问这样页面时都被下载一次。这是一个使用效率方面的问题——尤其当这个图片被整个网站大量使用的时候。

### 合理使用

1. 只需要将 **Data URL** 嵌入 `css` 文件中就可以解决缓存问题
2. 能使用 sprite 的地方尽量使用 sprite
3. 在图片较小的情况下使用
4. 根据实际情况，合理使用。

## 扩展文章

- [Data URI&MHTML: 用还是不用？](https://www.99css.com/492/)
- [Data URI 的利弊](https://www.cssforest.org/2010/10/16/Data-URI的利弊.html)
- [维基百科](https://en.wikipedia.org/wiki/Data_URI_scheme)
