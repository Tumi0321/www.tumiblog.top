---
title: CSS 实现单行、多行文本溢出显示
date: 2021-3-30
tags:
  - CSS 实现
categories:
  - CSS
---

## 单行溢出



使用 `text-overflow: ellipsis` 属性和 `white-space: nowrap` 属性，前提是必须设置宽度



```css
.box {
  width: 300px;
  overflow: hidden;
  text-overflow:ellipsis; /* 显示省略符号来代表被修剪的文本 */
  white-space: nowrap; /* 文本不会换行 */
}
```



效果：



![2021/03/30/TOIMG50caa0330090509N.png](https://picturebed.tumiblog.top/2021/03/30/TOIMG50caa0330090509N.png)



## 多行溢出



**方法一**



```css
.box {
  width: 300px;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 3;
  overflow: hidden; 
}
```



效果：



![2021/03/30/TOIMG190840330090550N.png](https://picturebed.tumiblog.top/2021/03/30/TOIMG190840330090550N.png)



因使用了 WebKit 的 CSS 扩展属性，该方法适用于 WebKit 浏览器及移动端



`-webkit-line-clamp` 用来限制在一个块元素显示的文本的行数。 为了实现该效果，它需要组合其他的 WebKit 属性（[MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/-webkit-line-clamp)）：



- `display: -webkit-box` 必须结合的属性 ，将对象作为弹性伸缩盒子模型显示 
- `-webkit-box-orient` 必须结合的属性 ，设置或检索伸缩盒对象的子元素的排列方式 



**方法二**



```css
.box {
  width: 300px;
  position: relative;
  line-height: 20px;
  max-height: 40px;
  overflow: hidden;
}

.box:after {
  content: "...";
  position: absolute;
  bottom: 0;
  right: 20px;
  padding-left: 10px;
  background: -webkit-linear-gradient(left, transparent, #fff 55%);
  background: -o-linear-gradient(right, transparent, #fff 55%);
  background: -moz-linear-gradient(right, transparent, #fff 55%);
  background: linear-gradient(to right, transparent, #fff 55%);
}
```





效果：

![2021/03/30/TOIMG2c7ee0330090622N.png](https://picturebed.tumiblog.top/2021/03/30/TOIMG2c7ee0330090622N.png)



该方法适用范围广，但文字未超出行的情况下也会出现省略号，可结合 js 优化该方法



注：



- 将 `height` 设置为 `line-height` 的整数倍，防止超出的文字露出
- 给 `p::after` 添加渐变背景可避免文字只显示一半
- 由于 ie6-7 不显示 `content` 内容，所以要添加标签兼容 ie6-7（如：`<span>…<span/>`）；兼容 ie8 需要将 `::after` 替换成 `:after`