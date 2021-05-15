---
title: ul 内部除最后一个 li 以外设置样式
date: 2021-2-4
tags:
  - CSS 实现
categories:
  - CSS
---

## 代码实现



- last-child / last-of-type



[css 关键字 unset](http://localhost:8080/blogs/CSS/CSS%E7%AC%94%E8%AE%B0/%E5%85%B3%E9%94%AE%E5%AD%97%20initial%E3%80%81inherit%20%E5%92%8C%20unset.html)

```css
ul li {
  color: orange;
}

ul li:last-child {
  color: unset;
}

/* 或者 */

ul li:last-of-type {
  color: unset;
}
```



- nth-last-child / nth-last-of-type



```css
ul li {
  color: orange;
}

ul li:nth-last-child(n) {
  color: unset;
}

/* 或者 */

ul li:nth-last-of-type(n) {
  color: unset;
}
```



- not(以上四种方式)



```css
ul li:not(:last-child) {
  color: orange
}

/* 或者 */

ul li:not(nth-last-child(1)) {
  color: orange
}

...
```



## last-child 与 last-of-type 的区别





`:last-child` 选择器用来匹配父元素中最后一个子元素



必须是最后一个子元素，同时必须是指定的元素，否则样式将不生效



```html
<style> 
p:last-child
{
  background:#ff0000;
}
</style>

<body>

<p>The first paragraph.</p>
<p>The second paragraph.</p>
<p>The third paragraph.</p>
<p>The fourth paragraph.</p>
```

![ ](http://picturebed.tumiblog.top/2021/02/13/TOIMGa03e90213120945N.png)



`last-of-type` 选择器匹配父元素中特定类型的最后一个子元素。



不必是最后一个子元素，只需要是指定的特定类型中的最后一个子元素



```html
<style> 
p:last-of-type
{
  background:#ff0000;
}
</style>

<body>

<p>The first paragraph.</p>
<p>The second paragraph.</p>
<p>The third paragraph.</p>
<p>The fourth paragraph.</p>
<h2>This is a ending</h2>
```

![ ](http://picturebed.tumiblog.top/2021/02/13/TOIMGc5d9e0213120957N.png)



同样 `nth-last-child` 和 `nth-last-of-type` 也是一样的效果



相同的还有 `first-child` 、 `first-of-type` 、 `nth-child` 、 `nth-of-type` 、 `only-of-type` 、 `only-child` 



简单理解就是有 type 字段的是先定义元素，再找位置；没有 type 字段的是先定义位置，再找元素