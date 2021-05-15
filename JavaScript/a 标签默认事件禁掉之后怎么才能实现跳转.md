---
title: a 标签默认事件禁掉之后怎么才能实现跳转
date: 2021-1-27
tags:
  - JS 实现
categories:
  - JavaScript
---

- 通过 `location.href` 实现跳转



```javascript
let domArr = document.getElementsByTagName('a')
[...domArr].forEach(item => {
  item.addEventListener('click', (e) => {
    if (e && e.preventDefault) {
      //阻止默认浏览器动作(W3C)
      e.preventDefault();
    } else {
      //IE中阻止函数器默认动作的方式
      window.event.returnValue = false;
      return false;
    }
    // 通过 location.href 进行跳转
    location.href = item.href;
  })
})
```

- 通过 location 对象方法， `assign()` 、 `assign()` 
- 通过 window 对象方法 `open()` 