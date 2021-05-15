---
title: 改变 placeholder 的字体颜色大小
date: 2021-3-31
tags:
  - CSS 属性
categories:
  - CSS
---

```css
input::-webkit-input-placeholder { 
  /* WebKit browsers */ 
  font-size:14px;
  color: red;
} 
input::-moz-placeholder { 
  /* Mozilla Firefox 19+ */ 
  font-size:14px;
  color: red;
} 
input:-ms-input-placeholder { 
  /* Internet Explorer 10+ */ 
  font-size:14px;
  color: red;
}
```