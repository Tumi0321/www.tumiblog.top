---
title: HTML5 有哪些新特性？如何处理兼容问题？如何区分 HTML5 和 HTML
date: 2020-9-28
tags:
  - HTML5
categories:
  - HTML
---

## Html5 新特性



- 语义化标签
- 增强型 input（类型、属性）
- 增强型表单（表单元素、表单事件、表单属性）
- 绘图（Canvas、SVG ）
- 全屏接口
- 读取文件
- 拖放对象
- 地理定位
- 音频、视频
- web 存储（sessionStorage、localStorage）
- 应用程序缓存
- 新的技术（web worker、SSE、websocket）



## Html5 兼容问题处理



- 为了能让旧版本的浏览器显示新标签，可以设置创建标签并设置 `display` 属性值为 `block`：

```html
<script>
  document.createElement("header");
  ...
</script>

<style>
header, section, footer, aside, nav, article, figure
{
    display: block;
}
</style>
```

- 使用封闭好的 js 库（[html5shiv.js](https://cdn.static.runoob.com/libs/html5shiv/3.7/html5shiv.min.js)）



## 如何区分 HTML 和 HTML5



**一、文档类型声明**



> [参考文档](https://www.runoob.com/tags/tag-doctype.html)



HTML5：



```html
<!DOCTYPE html>
```



HTML 4.01



```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
"http://www.w3.org/TR/html4/loose.dtd">
```



XHTML 1.0



```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
```



**二、独有特性**



- html 没有语义化标签（以上 HTML5 新特性有说明）
- html 没有音频视频 API（video、audio）
- html 没有绘图功能（Canvas、SVG）
- ……
- “HTML5 新特性”都可以作为区分的参考