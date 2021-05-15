---
title: 关键字 initial、inherit 和 unset
date: 2021-2-4
tags:
  - CSS 属性
categories:
  - CSS
---

## initial



`initial` 关键字用于表示 CSS 属性为它的默认值，可作用于任何 CSS 样式。（IE 不支持该关键字）



可以在 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Reference) 中查看 CSS 属性的默认值

![ ](http://picturebed.tumiblog.top/2021/02/13/TOIMGc94dd0213120900N.png)



## inherit



`inherit` 关键字用于表示 CSS 属性继承自祖辈，不是所有 css 属性都有继承特性（IE7 不支持）



可以在 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Reference) 中查看 CSS 属性是否有继承特性

![ ](http://picturebed.tumiblog.top/2021/02/13/TOIMGb64f20213120914N.png)



**可继承属性：**



- 字体：`visibility` 和 `cursor`
- 内联元素可继承：`letter-spacing`、`word-spacing`、`white-space`、`line-height`、`color`、`font`、 `font-family`、`font-size`、`font-style`、`font-variant`、`font-weight`、`text-decoration`、`text-transform`、`direction`
- 块状元素可继承：`text-indent` 和 `text-align`
- 列表元素可继承：`list-style`、`list-style-type`、`list-style-position`、`list-style-image`
- 表格元素可继承：`border-collapse`

 

## unset



`inherit` 关键字用于表示 CSS 不设置属性，如果该属性默认可继承，则值为 `inherit`，否则值为 `initial` （IE 不支持）