---
title: ECMAScript——相关介绍（一）
date: 2021-5-3
tags:
  - ECMAScript
categories:
  - JavaScript
---

::: tip

该系列会持续更新记录 ECMAScript 的新特性，目前内容是对[尚硅谷视频](https://www.bilibili.com/video/BV1uK411H7on)的一个总结

对于一些新特性会单独分离出来，下方会提供相应的链接，或直接在博客中搜索即可

:::



## 什么是 ECMA



ECMA（European Computer Manufacturers Association）中文名称为欧洲计算机制造商协会，这个组织的目标是评估、开发和认可电信和计算机标准。1994 年后该组织改名为 Ecma 国际。



## 什么是 ECMAScript



ECMAScript 是由 Ecma 国际通过 ECMA-262 标准化的脚本程序设计语言。



## 什么是 ECMA-262



Ecma 国际制定了许多标准，而 ECMA-262 只是其中的一个，所有标准列表查看



https://www.ecma-international.org/publications-and-standards/standards/



## ECMA-262 历史



ECMA-262（ECMAScript）历史版本查看网址



https://www.ecma-international.org/publications-and-standards/standards/ecma-262/



| 第 1 版  | 1997 年            | 制定了语言的基本语法                                         |
| -------- | ------------------ | ------------------------------------------------------------ |
| 第 2 版  | 1998 年            | 较小改动                                                     |
| 第 3 版  | 1999 年            | 引入正则、异常处理、格 式化输出等。IE 开始支持               |
| 第 4 版  | 2007 年            | 过于激进，未发布                                             |
| 第 5 版  | 2009 年            | 引入严格模式、JSON，扩 展对象、数组、原型、字 符串、日期方法 |
| 第 6 版  | 2015 年            | 模块化、面向对象语法、 Promise、箭头函数、let、 const、数组解构赋值等等 |
| 第 7 版  | 2016 年            | 幂运算符、数组扩展、 Async/await 关键字                      |
| 第 8 版  | 2017 年            | Async/await、字符串扩展                                      |
| 第 9 版  | 2018 年            | 对象解构赋值、正则扩展                                       |
| 第 10 版 | 2019 年            | 扩展对象、数组方法                                           |
| ES.next  | 动态指向下一个版本 |                                                              |



**注**：从  ES6 开始，每年发布一个版本，版本号比年份最后一位大  1



## 谁在维护 ECMA-262 



ECMA 的第 39 号技术专家委员会（Technical Committee 39，简称 TC39）负责制订 ECMAScript 标准。其会员都是公司（其中主要是浏览器厂商，有苹果、谷歌、微软、因特尔等）。TC39 定期召开会议，会议由会员公司的代表与特邀专家出席。



## 兼容性



https://kangax.github.io/compat-table/es6/ 可查看兼容性



![image.png](https://cdn.nlark.com/yuque/0/2021/png/2332117/1619495057150-a9684e82-e9ee-4954-9ccc-fb7e20eceeda.png)