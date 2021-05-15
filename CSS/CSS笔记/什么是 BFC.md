---
title: 什么是 BFC
date: 2021-2-23
tags:
  - CSS 基础
categories:
  - CSS
---

## 常见定位 



- 普通流 (normal flow)

> 也常被译为标准流或者文档流，是指 HTML 中的文档按照默认的排布 HTML 元素的行为。在普通流中，元素按照其在 HTML 中的先后位置至上而下布局，在这个过程中，行内元素水平排列，直到当行被占满然后换行，块级元素则会被渲染为完整的一个新行。除非另外指定，否则所有元素默认都是普通流定位。



- 脱离文档流



一个元素脱离文档流（out of normal flow）之后，其他的元素在定位的时候会当做没看见它，两者位置重叠都是可以的。



- - 浮动 (float)

> 浮动后的元素会脱离文档流，但文字依然会受影响，其效果与印刷排版中的文本环绕相似。



- - 定位 (absolute、fixed)

> 设置 position 属性为 absolute 或者 fixed 的元素会整体脱离普通流，包括文字



具体可以看该[文章](https://www.zhihu.com/question/24529373/answer/29135021)举出的例子



## 什么是 FC



Formatting context（格式化上下文)）是 W3C CSS2.1 规范中的一个概念。它是页面中的一块渲染区域，并且有一套渲染规则，它决定了其子元素将如何定位，以及和其他元素的关系和相互作用。



## BFC 概念



脱离常规流的元素会创建一个新的块级格式化上下文，BFC 即 Block Formatting Contexts (块级格式化上下文)，是块盒子的布局过程发生的区域，也是浮动元素与其他元素交互的区域。



**具有 BFC 特性的元素可以看作是隔离了的独立容器，容器里面的元素不会在布局上影响到外面的元素，并且 BFC 具有普通容器所没有的一些特性。**



## BFC 特性



- 处于不同 BFC 下的相邻容器上下外边距不会发生折叠（普通流和同一个 BFC 下的容器会发生折叠）
- 计算 BFC 高度时浮动元素也参于计算
- BFC 的区域不会与浮动容器发生重叠
- BFC 内的容器在垂直方向依次排列
- 元素的 margin-left 与其包含块的 border-left 相接触
- BFC 是独立容器，容器内部元素不会影响容器外部元素，反之亦然



## 触发 BFC



只要元素满足下面任一条件即可触发 BFC 特性（[MDN](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context)）：



- 根元素（\<html\>）
- float 值非 none
- overflow 值非 visible（hidden、auto、scroll）
- display 值为 inline-block、table-cell、table-caption、flex、inline-flex
- position 值为 absolute、fixed（除非该值已经被传播到视口）
- 当使用 `overflow: hidden` 给 \<body\> 触发 BFC 时需要 \<html\> 和 \<body\> 同时设置（[参考](https://segmentfault.com/q/1010000002645174)）



## BFC 示例



**1.** **处于不同 BFC 下的容器上下外边距不会发生折叠**



```html
<style>
div{
  width: 50px;
  height: 50px;
  background: lightblue;
  margin: 50x;
}
</style>
<body>
  <div></div>
  <div></div>
</body>
```

![2021/02/23/TOIMG778fc0223121950N.png](https://picturebed.tumiblog.top/2021/02/23/TOIMG778fc0223121950N.png)

因为两个 div 元素都处于普通流中，所以第一个 div 的下边距和第二个 div 的上边距发生了折叠，所以两个盒子之间距离只有 50px，而不是 100px。



这是 CSS 的一种规范，**如果想要避免外边距的折叠，可以将其放在不同的 BFC 容器中。**



```html
<style>
  .container {
    overflow: hidden;
  }

  .container > div {
    width: 50px;
    height: 50px;
    background: lightblue;
    margin: 50px;
  }
</style>
<body>
  <div class="container">
    <div></div>
  </div>
  <div class="container">
    <div></div>
  </div>
</body>
```



这时候，两个盒子边距就变成了 100px

![2021/02/23/TOIMG29e600223122024N.png](https://picturebed.tumiblog.top/2021/02/23/TOIMG29e600223122024N.png)

**2.** **计算 BFC 高度时浮动元素也参于计算（清除浮动）**



浮动的元素会脱离普通文档流



```html
<div style="border: 1px solid #000;">
    <div style="width: 100px;height: 100px;background: #eee;float: left;"></div>
</div>
```



![2021/02/23/TOIMG661db0223122054N.png](https://picturebed.tumiblog.top/2021/02/23/TOIMG661db0223122054N.png)

由于容器内元素浮动，脱离了文档流，所以容器只剩下 2px 的边距高度。如果使触发容器的 BFC，那么容器将会包裹着浮动元素。



```html
<div style="border: 1px solid #000;overflow: hidden">
    <div style="width: 100px;height: 100px;background: #eee;float: left;"></div>
</div>
```



![2021/02/23/TOIMGa02650223122137N.png](https://picturebed.tumiblog.top/2021/02/23/TOIMGa02650223122137N.png)



**3.** **BFC 的区域不会与浮动容器发生重叠**



先来看一个文字环绕效果：



```html
<div style="height: 100px;width: 100px;float: left;background: lightblue">我是一个左浮动的元素</div>
<div style="width: 200px; height: 200px;background: #eee">我是一个没有设置浮动, 
也没有触发 BFC 元素, width: 200px; height:200px; background: #eee;</div>
```



![2021/02/23/TOIMG5b3390223122209N.png](https://picturebed.tumiblog.top/2021/02/23/TOIMG5b3390223122209N.png)

这时候其实第二个元素有部分被浮动元素所覆盖，(但是文本信息不会被浮动元素所覆盖) 如果想避免元素被覆盖，可以触发 BFC 特性，在第二个元素中加入 `overflow: hidden`



![2021/02/23/TOIMG2d9950223122252N.png](https://picturebed.tumiblog.top/2021/02/23/TOIMG2d9950223122252N.png)

这个方法可以用来实现两列自适应布局，效果不错，这时候左边的宽度固定，右边的内容自适应宽度。



## 参考文章



- [css脱离文档流到底是什么意思](https://www.zhihu.com/question/24529373/answer/29135021)
- [In Flow and Out of Flow](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Flow_Layout/在Flow中和Flow之外)
- [10 分钟理解 BFC 原理](https://zhuanlan.zhihu.com/p/25321647)
- [格式化上下文](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context)
- [重提CSS中外边距折叠问题](https://segmentfault.com/q/1010000002645174#)
- [BFC是什么？BFC有什么用？看完全明白](https://www.cnblogs.com/qs-cnblogs/p/12349887.html)