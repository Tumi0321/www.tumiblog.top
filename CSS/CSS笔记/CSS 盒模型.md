---
title: CSS 盒模型
date: 2021-2-8
tags:
  - CSS 基础
categories:
  - CSS
---

盒模型是 CSS 中的一种设计模式，web 页面中的所有元素都可以当做一个盒模型。



盒模型包括：外边距（margin）、边框（border）、内边距（padding）、实际内容（content）四个属性。



CSS 盒模型有 W3C 标准盒模型和 IE 的传统盒模型（怪异盒模型）

![ ](http://picturebed.tumiblog.top/2021/02/13/TOIMG7f2ee0213121043N.png)



## 标准模型



标准盒模型 `box-sizing: content-box` 



w3c 盒子模型的范围包括 marign、border、padding、width/height（width 决定 content 的大小）

![ ](http://picturebed.tumiblog.top/2021/02/13/TOIMG3b53d0213121058N.png)



计算方式：



width/heigth（空间大小）= width/height + padding + border + margin



示例：



```
<style>
.box {
  width: 200px;
  height: 200px;
  border: 20px solid black;
  padding: 50px;
  margin: 50px;
  background-color: pink;
}
</style>

<div class="box"></div>
```

![ ](http://picturebed.tumiblog.top/2021/02/13/TOIMG6ba370213121112N.png)



## 传统模型



传统盒模型 `box-sizing: border-box` 



IE 盒子模型的范围包括 marign、width（width 决定了 content、padding 和 border 的大小）

![ ](http://picturebed.tumiblog.top/2021/02/13/TOIMGb4d2b0213121132N.png)



计算方式：



width/heigth（空间大小）= width/height + margin



示例：



```
<style>
.box {
  width: 200px;
  height: 200px;
  border: 20px solid black;
  padding: 50px;
  margin: 50px;
  background-color: pink;
  box-sizing: border-box;
}
</style>

<div class="box"></div>
```

![ ](http://picturebed.tumiblog.top/2021/02/13/TOIMG0ce510213121146N.png)