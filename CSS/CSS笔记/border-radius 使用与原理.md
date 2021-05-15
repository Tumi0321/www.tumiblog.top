---
title: border-radius 使用与原理
date: 2021-2-18
tags:
  - CSS 属性
categories:
  - CSS
---

## 使用



`border-radius` 是一个简写属性，它包含 `border-top-left-radius`（左上圆角半径）, `border-top-right-radius`（右上圆角半径）, `border-bottom-right-radius` （右下圆角半径）,`border-bottom-left-radius`（左下圆角半径）。



每一个属性都有两个值，第一个值是水平半径，第二个是垂直半径。如果省略第二个值，它是从第一个复制。以下以 `border-top-left-radius` 举例：



当设置一个值时：



```css
.wrap {
  width: 300px;
  height: 150px;
  border-top-left-radius: 50px;
}
```

![2021/02/18/TOIMGc18320218030531N.png](https://picturebed.tumiblog.top/2021/02/18/TOIMGc18320218030531N.png)



当设置两个值时：



```css
.wrap {
  width: 300px;
  height: 150px;
  border-top-left-radius: 50px 100px;
}
```

![2021/02/18/TOIMG48fbc0218030618N.png](https://picturebed.tumiblog.top/2021/02/18/TOIMG48fbc0218030618N.png)

其它三个属性同理，当设置一个值时确定一圆，当设置两个值时确定一个椭圆。



所以 `border-radius` 作为简写属性有四个值，每个值都是指定四个角的半径。每个半径的值的顺序是：左上角，右上角，右下角，左下角。一个值时，四个角的半径相等；两个值时，处于对角线的半径相等；三个值时，右上角和左下角的半径相等。



一个值时：



```css
.wrap {
  width: 300px;
  height: 150px;
  border-radius: 10px;
}
```

![2021/02/18/TOIMG103530218030652N.png](https://picturebed.tumiblog.top/2021/02/18/TOIMG103530218030652N.png)



两个值时：



```css
.wrap {
  width: 300px;
  height: 150px;
  border-radius: 10px 15px;
}c
```

![2021/02/18/TOIMG00bb50218030724N.png](https://picturebed.tumiblog.top/2021/02/18/TOIMG00bb50218030724N.png)



三个值时：



```css
.wrap {
  width: 300px;
  height: 150px;
  border-radius: 10px 15px 20px;
}
```

![2021/02/18/TOIMG925600218030747N.png](https://picturebed.tumiblog.top/2021/02/18/TOIMG925600218030747N.png)



四个值时：



```css
.wrap {
  width: 300px;
  height: 150px;
  border-radius: 10px 15px 20px 25px;
}
```

![2021/02/18/TOIMGc46d00218030813N.png](https://picturebed.tumiblog.top/2021/02/18/TOIMGc46d00218030813N.png)



上面有提到，还能指定水平方向和垂直方向的半径大小，如果使用简写属性 `border-radius` 的话要使用 “/”分割。



```css
.wrap {
  width: 300px;
  height: 150px;
  border-radius: 20px / 30px;
}
```

![2021/02/18/TOIMGfe5990218030836N.png](https://picturebed.tumiblog.top/2021/02/18/TOIMGfe5990218030836N.png)



如果使用 “/”分割的话，那么 `border-radius` 能指定八个值，前面四个后面四个。前面代表四个角的各个水平半径，后面代表四个角的各个垂直半径，值的顺序与上面 `border-radius` 顺序相同。



```css
.wrap {
  width: 300px;
  height: 150px;
  /**
   * 左上为 10px 15px 的圆
   * 右上为 15px 20px 的圆
   * 右下为 20px 25px 的圆
   * 左下为 25px 30px 的圆
  */
  border-radius: 10px 15px 20px 25px / 15px 20px 25px 30px;
}
```

![2021/02/18/TOIMG2cffa0218030915N.png](https://picturebed.tumiblog.top/2021/02/18/TOIMG2cffa0218030915N.png)



## 原理



个人认为对于这个 radius 属性，其实是原有的盒子跟这“看不见”的圆或者椭圆相互结合后的结果，所以我是这么理解这个渲染过程：



```css
.wrap { 
  width: 300px;
  height: 200px;
  background: #F8D575;
  border-radius: 50px;
}
```



先渲染盒子模型，这里为 300*200px



![2021/02/18/TOIMGae0cc0218030941N.png](https://picturebed.tumiblog.top/2021/02/18/TOIMGae0cc0218030941N.png)



再渲染 `border-radius` 属性，这里使用了 `border-radius: 50px`



![2021/02/18/TOIMGfc0220218031009N.png](https://picturebed.tumiblog.top/2021/02/18/TOIMGfc0220218031009N.png)



接着盒子最外边框与圆形的交集应该是下面这部分



![2021/02/18/TOIMGd59140218031034N.gif](https://picturebed.tumiblog.top/2021/02/18/TOIMGd59140218031034N.gif)



浏览器保留盒子内部结构的同时，只保留盒子与“看不见”的圆形相互交集部分，故四个90°的角被圆角所覆盖。



![2021/02/18/TOIMG9d49f0218031053N.gif](https://picturebed.tumiblog.top/2021/02/18/TOIMG9d49f0218031053N.gif)



但是盒子模型依然起作用，内容部分依然不会改变



![2021/02/18/TOIMG94a170218031114N.png](https://picturebed.tumiblog.top/2021/02/18/TOIMG94a170218031114N.png)



## 画圆



通常我们用 CSS 实现一个圆形：先画一个方形，然后将它的 `border-radius` 设置成 50%。



这是一个 `150px x 150px` 大小的方形，将它的四个角的半径都设置成 50%。如果 `border-radius` 的值是百分比的话，就是相对于 border box 的宽度和高度的百分比。在我们的例子中，盒子的宽高都是 150px，所以 50% 对应的就是 75px。



![2021/02/18/TOIMG04a3f0218031139N.png](https://picturebed.tumiblog.top/2021/02/18/TOIMG04a3f0218031139N.png)



但是你会发现使用 100% 也可以实现一个圆形。



如果左上角的圆角半径被设置成了 100%，那么圆角就会从这个方形左下角跨到右上角，相当于把圆角半径设置成为 150px（也就是方形的大小）。如果同时把右上角的圆角半径也设置成为 100%，则两个相邻圆角合起来就有 200%。这种情况自然是不允许出现的，所以浏览器就会重新就算，匀出空间给右边的圆角，同时等比缩放两个圆角的半径直到它们可以刚好符合这个方形，所以半径就变成了 50%。



![2021/02/18/TOIMGdfaf30218031203N.png](https://picturebed.tumiblog.top/2021/02/18/TOIMGdfaf30218031203N.png)



同样的，浏览器会对其他相邻的圆角应用相同的计算，即使将 `border-radius` 设置为150px，浏览器还是会按照 75px 画圆角，75px 是浏览器所允许的这个方形能够拥有的最大的圆角半径。



使用 `border-raidius` 画半圆：



[演示 demo](https://codepen.io/tumi0321/pen/jOVwbqm)

```css
 /* 上半圆 */
.top {
  border-radius: 50% / 100% 100% 0 0;
}
/* 下半圆 */
.bottom {
  border-radius: 50% / 0 0 100% 100%;
}
/* 右半圆 */
.right {
  border-radius: 0 100% 100% 0 / 50%;
}
/* 左半圆 */
.left {
  border-radius: 100% 0 0 100% / 50%;
}
```

![2021/02/18/TOIMGb11840218031244N.png](https://picturebed.tumiblog.top/2021/02/18/TOIMGb11840218031244N.png)

## 参考文章



- [css3：border-radius圆角边框详解](http://www.ferecord.com/css3border-radius-yuan-jiao-bian-kuang-xiang-jie.html)
- [border-radius 原理分析](https://www.cnblogs.com/xianshenglu/p/8034101.html)
- [Border-radius 50% vs 100%](https://zhuanlan.zhihu.com/p/20128284)
- [Fluent 2014: Lea Verou, "The Humble Border-Radius"](https://www.youtube.com/watch?v=JSaMl2OKjfQ)