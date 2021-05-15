---
title: CSS3 媒体查询
date: 2021-4-16
tags:
  - CSS 基础
categories:
  - CSS
---

媒体查询（Media queries），可以根据媒体类型、特定的特征和设备参数（例如屏幕分辨率和浏览器视窗宽度）来修改网站布局或样式。



每条媒体查询语句都由一个可选的媒体类型和任意数量的媒体特性表达式构成。可以使用多种逻辑操作符合并多条媒体查询语句。



即使表达式的计算结果为  `false` 时带有媒体查询附加到其 `<link>` 标记的样式表仍将下载，但是，除非查询结果变为 `true`，否则其内容将不适用。



## 媒体类型



媒体类型（Media types）描述设备的一般类别。除非使用 `not` 或 `only` 逻辑操作符，媒体类型是可选的，并且会隐式地应用 `all` 类型。



- all，适用于所有设备
- print，适用于在打印预览模式下在屏幕上查看的分页材料和文档。 （有关特定于这些格式的格式问题的信息，请参阅[分页媒体](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Paged_Media)。）
- screen，主要用于屏幕
- speech，主要用于语音合成器



还有一些已经废弃的类型，这里就不阐述了。一般开发中，大多是使用 `all` 和 `screen` 



## 媒体特性



媒体特性（Media features）描述了 [user agent](https://developer.mozilla.org/en-US/docs/Glossary/User_agent)、输出设备，或是浏览环境的具体特征。媒体特性表达式是完全可选的，它负责测试这些特性或特征是否存在、值为多少。每条媒体特性表达式都必须用括号括起来。



以下只列举常用的特性，更详细的可以查看 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Media_Queries/Using_media_queries) 和[菜鸟教程](https://www.runoob.com/cssref/css3-pr-mediaquery.html)



| 名称                                                         | 介绍                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [orientation](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@media/orientation) | 定义视窗高度与宽度的关系（值 `portrait` 代表纵向，`landscape` 代表横向）。 |
| [width](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@media/width) | 视窗（viewport）的宽度，包括纵向滚动条的宽度。这是一个范围特性，也就是说可以使用前缀 `min-width` 和 `max-width` 分别查询最小值和最大值 |



## 基本使用





- 使用标签的形式：



用逗号指定多个设备



```html
<link rel="stylesheet" href="screen.css" media="screen">
<link rel="stylesheet" href="all.css" media="screen,print">
```



- 使用 CSS @import 的形式：



```css
@import url(commom.css);
@import url(screen.css) screen;
@import url(print.css) print;
```



- 在 CSS 样式中使用媒体查询：



```css
@media screen and (max-width:600px) {
    .navbar {
    display: none; 
  }
}
```



## 逻辑操作符



逻辑操作符（logical operators） `not`, `and` 和 `only` 可用于联合构造复杂的媒体查询，可以通过用逗号分隔多个媒体查询，将它们组合为一个规则。



### and



`and` 操作符相当于逻辑与，当每个查询规则都为真时则该条媒体查询为真。



```html
<style media="screen and (min-width:768px)">
    h1 {
    color: red; 
  }
</style>
```



上面示例表示，当视窗宽度大于等于 `768px` 时运用的样式。还可以连接多个操作符来进行更复杂的判断



```html
<style media="screen and (min-width:768px) and (max-width: 1000px)">
    h1 {
    color: blue; 
  }
</style>
```



上面示例表示，当视窗宽度大于等于 `768px` 并小于 `1000px` 时应用的样式



### 逗号



逗号操作符相当于逻辑或，如果列表中的任何查询为 `true`，则整个 media 语句均返回 `true`。



```html
<style media="screen and (min-width:768px),screen and (orientation:landscape)">
    h1 {
    color: blue; 
  }
</style>
```



上面示例表示，移动设备视窗宽度大于等于 `768px` 或者当设备处理横屏时（宽度大于高度）应用的样式



### not



`not` 运算符用于否定媒体查询，如果不满足这个条件则返回 `true`，否则返回 `false`。 如果出现在以逗号分隔的查询列表中，它将仅否定应用了该查询的特定查询。 如果使用 `not` 运算符，则还必须指定媒体类型。



```html
<style>
    @media not screen and (min-width:768px) {
        h1 {
        color: blue 
    }
    }
</style>
```



以上示例表示，当移动设备视窗宽度大于 `768px` 时不应用的样式



### only



`only` 关键字可防止不支持带有媒体功能的媒体查询的旧版浏览器应用给定的样式。它对现代浏览器没有影响。



```html
<style>
    @media only screen and (min-width:768px) {
        h1 {
        color: blue 
    }
    }
</style>
```



## 使用规范



使用媒体查询应当注意条件范围的一个先后顺序，因为 css 的规则是后定义的样式会覆盖之前的



```css
@import url("big.css") only screen and (min-width: 1200px);
@import url("medium.css") only screen and (min-width: 960px);
```



以上样式是不生效的，因为大于 `1200px` 也同样大于 `960px` ，正确写法应该是 `medium.css` 在上面