---
title: CSS 样式优先级
date: 2021-2-16
tags:
  - CSS 基础
categories:
  - CSS
---

所谓 CSS 样式优先级，即是指 CSS 样式在浏览器中被解析的先后顺序。影响优先级的可能来自周围的标签，也可能来自其自身。



## CSS 样式继承



CSS 的继承特性指的是应用在一个标签上的那些 CSS 属性被传到其子标签上。看下面的 HTML 结构：



```html
<div>
  <p></p>
</div>
```



如果 `<div>` 设置了属性 `color: red`，则这个属性将被 `<p>` 继承，即 `<p>` 也拥有属性 `color: red`。



由上可见，当网页比较复杂， HTML 结构嵌套较深时，一个标签的样式将深受其祖先标签样式的影响。



**CSS 优先规则1：** 最近的祖先样式比其他祖先样式优先级高。



```html
<!-- 类名为 son 的 div 的 color 为 blue -->
<div style="color: red">
  <div style="color: blue">
      <div class="son"></div>
  </div>
</div>
```

如果我们把一个标签从祖先那里继承来的而自身没有的属性叫做"祖先样式"，那么"直接样式"就是一个标签直接拥有的属性。



**CSS 优先规则2：** "直接样式"比"祖先样式"优先级高。



```html
<!-- 类名为 son 的 div 的 color 为 blue -->
<div style="color: red">
  <div class="son" style="color: blue"></div>
</div>
```



## 选择器优先级



CSS 中有 7 种基础选择器



- ID 选择器， 如 `#id{}`
- 类选择器， 如 `.class{}`
- 属性选择器， 如 `a[href="segmentfault.com"]{}`
- 伪类选择器， 如 `:hover{}`
- 伪元素选择器， 如 `::before{}`
- 标签选择器， 如 `span{}`
- 通配选择器， 如 `*{}`



**CSS 优先规则3：** 内联样式 > ID 选择器 > 类选择器 = 属性选择器 = 伪类选择器 > 标签选择器 = 伪元素选择器



```html
<!-- 最终的 color 为 black，因为内联样式比其他选择器的优先级高。-->
<div class="content-class" id="content-id" style="color: black"></div>

<style>
#content-id {
  color: red;
}
.content-class {
  color: blue;
}
div {
  color: grey;
}
</style>
```



所有 CSS 的选择符由上述 7 种基础的选择器或者组合而成，组合的方式有 3 种：



- 后代选择符：`.father .child{}`
- 子选择符：`.father > .child{}`
- 相邻选择符：`.bro1 + .bro2{}`



当一个标签同时被多个选择符选中，我们便需要确定这些选择符的优先级。



**CSS 优先规则4：** 计算选择符中 ID 选择器的个数（a），计算选择符中类选择器、属性选择器以及伪类选择器的个数之和（b），计算选择符中标签选择器和伪元素选择器的个数之和（c）。按 a、b、c 的顺序依次比较大小，大的则优先级高，相等则比较下一个。若最后两个的选择符中 a、b、c 都相等，则按照"就近原则"来判断。



```
<!-- <span> 的 color 为 red -->
<div id="con-id">
  <span class="con-span"></span>
</div>

<style>
#con-id span {
  color: red;
}
div .con-span {
  color: blue;
}
</style>
```



如果外部样式表和内部样式表中的样式发生冲突，仍可以使用以上规则进行判断



```html
<!-- <div> 的背景色为 blue -->
<link rel="stylesheet" type="text/css" href="style-link.css">
<style type="text/css">
@import url(style-import.css); 
div {
    background: blue;
}
</style>

<div></div>

// style-link.css
div {
    background: lime;
}

// style-import.css
div {
    background: grey;
}
```



CSS 还提供了一种可以完全忽略以上规则的方法，当你一定、必须确保某一个特定的属性要显示时，可以使用 `!important` 



**CSS 优先规则5：** 属性后插有 `!important` 的属性拥有最高优先级。若同时插有 `!important`，则再利用规则 3、4 判断优先级。



```html
<!-- <p> 的 background 为 red -->
<div class="father">
    <p class="son"></p>
</div>

<style>
p {
  background: red !important;
}
.father .son {
  background: blue;
}
</style>
```



应尽量避免使用 `!important` ，因为这破坏了样式表中的固有的级联规则使得调试找 bug 变得更加困难了。



## 错误的说法



在学习过程中，你可能发现给选择器加权值的说法，即 ID 选择器权值为 100，类选择器权值为 10，标签选择器权值为 1，当一个选择器由多个 ID 选择器、类选择器或标签选择器组成时，则将所有权值相加，然后再比较权值。这种说法其实是有问题的。比如一个由 11 个类选择器组成的选择器和一个由 1 个 ID 选择器组成的选择器指向同一个标签，按理说 110 > 100，应该应用前者的样式，然而事实是应用后者的样式。错误的原因是：**权重的进制是并不是十进制，CSS 权重进制在 IE6 为 256，后来扩大到了 65536，现代浏览器则采用更大的数量。**还是拿刚刚的例子说明。11 个类选择器组成的选择器的总权值为 110，但因为 11 个均为类选择器，所以其实总权值最多不能超过 100， 你可以理解为 99.99，所以最终应用后者样式。



![2021/02/16/TOIMGd88590216014936N.png](http://picturebed.tumiblog.top/2021/02/16/TOIMGd88590216014936N.png)



## 参考文章



- [CSS 样式优先级](https://www.runoob.com/w3cnote/css-style-priority.html)
- [CSS（层叠样式表）- 优先级](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Specificity)
- [css的优先级和权重](https://www.cnblogs.com/cnblogs-jcy/p/8574177.html)