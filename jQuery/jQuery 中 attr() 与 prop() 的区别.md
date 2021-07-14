---
title: jQuery 中 attr() 与 prop() 的区别
date: 2021-7-14
tags:
  - jQuery 方法
categories:
  - jQuery
---

在使用 jQuery 的 `attr()` 方法时可能会遇到下面的问题：



```html
<input type="checkbox" name="items" value="足球" author="tumi">足球
<input type="checkbox" name="items" value="篮球">篮球
<input type="checkbox" name="items" value="羽毛球">羽毛球
<input type="checkbox" name="items" value="排球">排球
<input type="button" id="btn" value="全选">

<script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.6.0/jquery.js"></script>
<script>
  $(function() {
    $("#btn").click(function () {
      $("input:checkbox[name=items]").attr("checked", true);
    })
  })
  </script>
```



当点击“全选”时能正常执行，而当点击任意选项后，再点击“全选”时就失效了。

![2021/07/14/TOIMG028620714080002N.gif](https://picturebed.tumiblog.top/2021/07/14/TOIMG028620714080002N.gif)

这是因为用户操作的是 property，而 `attr` 方法改变的是 attribute。



## 什么是 property 和 attribute



JS 原生对象的直接属性我们称作为 property。



![2021/07/14/TOIMGf20420714080022N.png](https://picturebed.tumiblog.top/2021/07/14/TOIMGf20420714080022N.png)



html 标签的预定义和自定义属性我们统称为 attribute，存放在节点对象的 attributes 属性中。



![2021/07/14/TOIMGc7f030714080047N.png](https://picturebed.tumiblog.top/2021/07/14/TOIMGc7f030714080047N.png)



每一个预定义的 attribute 都有一个 property 与之对应，而自定义属性并没有。



## property 和 attribute 的同步关系



当属性的值是非布尔类型时，不管改变哪个都会实时同步；



当属性的值是布尔类型时：



1. 改变 property 时，永远不会同步 attribute
2. 当 property 是默认值时，attribute 会同步 property，不是默认值则不会同步



例如以下示例就会同步：



```html
<input type="checkbox">

<script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.6.0/jquery.js"></script>
<script>
  $(function () {
    var input = $("input[type=checkbox]")

    debugger
    input.attr("checked", "checked")
  })
</script>
```



## 浏览器渲染和用户操作的是哪个



只改变 property 可以使复选框勾选，所以浏览器渲染是通过 property。



![2021/07/14/TOIMGebaee0714080108N.gif](https://picturebed.tumiblog.top/2021/07/14/TOIMGebaee0714080108N.gif)



用户操作也只改变 property。



![2021/07/14/TOIMGf7d510714080130N.gif](https://picturebed.tumiblog.top/2021/07/14/TOIMGf7d510714080130N.gif)



所以造成以上情况的原因是因为当用户点击复选框后，改变的是 property。再使用 `attr()` 是改变不了 checked 的。