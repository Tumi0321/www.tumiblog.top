---
title: CSS3 中的 position:sticky
date: 2021-2-18
tags:
  - CSS 属性
categories:
  - CSS
---

## 介绍



`position: sticky` 又称为粘性定位，是一个结合了 `position: relative` 和 `position: fixed` 两种定位于一体的特殊定位，适用于一些特殊场景。



粘性定位的元素是依赖于用户的滚动，元素先按照普通文档流定位元素定位（relative），在跨越特定阈值之后为固定定位（fixed）。



这个特定阈值指的是 top, right, bottom 或 left 之一，换言之，指定 top, right, bottom 或 left 四个阈值其中之一，才可使粘性定位生效。否则其行为与相对定位相同。



值得注意的是 `position: sticky` 的兼容性并不乐观（[CANIUSE](https://caniuse.com/#feat=css-sticky)）



![2021/02/23/TOIMG1f5090223123847N.png](https://picturebed.tumiblog.top/2021/02/23/TOIMG1f5090223123847N.png)



## 示例



[demo 示例](https://codepen.io/tumi0321/pen/poNrwvE)

```css
.sticky-box {
  position: -webkit-sticky; /* Safari */
  position: sticky;
  height: 60px;
  margin-bottom: 30px;
  background: #ff7300;
  top: 0px;
}
```

![2021/02/23/TOIMG241e60223123909N.gif](https://picturebed.tumiblog.top/2021/02/23/TOIMG241e60223123909N.gif)



因为设定的阈值是 `top:0` ，这个值表示当元素距离页面视口（Viewport，也就是 fixed 定位的参照）顶部距离大于 0px 时，元素以 relative 定位表现，而当元素距离页面视口小于 0px 时，元素表现为 fixed 定位，也就会固定在顶部。



## 规则和特性



`position:sticky` 的生效是有一定的限制的，总结如下：



- 必须指定 top, right, bottom 或 left 四个阈值其中之一，才可使粘性定位生效。否则其行为与相对定位相同。
- 如果 top 和 bottom 同时设置时，top 生效的优先级高，left 和 right 同时设置时，left 的优先级高。
- 设定为 `position:sticky` 元素的任意父节点的 overflow 属性必须是 visible，否则 `position:sticky` 不会生效。

- - 如果 `position:sticky` 元素的任意父节点定位设置为 `overflow:hidden`，则父容器无法进行滚动，所以 `position:sticky` 元素也不会有滚动然后固定的情况。
  - 如果 `position:sticky` 元素的任意父节点定位设置为 position:relative | absolute | fixed，则元素相对父元素进行定位，而不会相对 viewprot 定位。

- 父元素的高度不能低于 sticky 元素的高度
- sticky 不会触发 BFC
- z-index 无效

## 参考文章



- [使用 position:sticky 实现粘性布局](https://www.cnblogs.com/coco1s/p/6402723.html)
- [杀了个回马枪，还是说说position:sticky吧](https://www.zhangxinxu.com/wordpress/2018/12/css-position-sticky/)
- [MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/position)
- [深入理解css中position属性及z-index属性](https://www.cnblogs.com/zhuzhenwei918/p/6112034.html)