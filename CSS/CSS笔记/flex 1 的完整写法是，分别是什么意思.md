---
title: flex:1 的完整写法是？分别是什么意思？
date: 2021-2-6
tags:
  - CSS 属性
  - flex
categories:
  - CSS
---



**`flex`** 规定了弹性元素如何伸长或缩短以适应 flex 容器中的可用空间。这是一个简写属性，用来设置 `flex-grow`，`flex-shrink` 与 `flex-basis`



## 取值



**单值语法**: 值必须为以下其中之一:



- 一个无单位**数(`<number>`)**: 它会被当作 `flex-grow` 的值（省略时默认为 1，初始值 0）
- 一个有效的**宽度(`width`)**值: 它会被当作  `flex-basis` 的值（省略时默认为 0，初始值 auto）
- 关键字 `none`，`auto` 或 `initial`.



**双值语法**: 第一个值必须为一个无单位数，并且它会被当作 `flex-grow` 的值。第二个值必须为以下之一：

- 一个无单位数：它会被当作 `flex-shrink` 的值（省略时默认为 1，初始值 1）
- 一个有效的宽度值: 它会被当作 `flex-basis` 的值



**三值语法:**



- 第一个值必须为一个非负无单位数字，并且它会被当作 `flex-grow` 的值
- 第二个值必须为一个非负无单位数字，并且它会被当作 `flex-shrink` 的值
- 第三个值必须为一个有效的宽度值， 并且它会被当作 `flex-basis` 的值



## flex: 1



`flex: 1` 的完整写法是（flex: 非负无单位数）



```css
flex-grow: 1;
flex-shrink: 1;
flex-basis: 0%;
```



## flex: 宽度值



```css
.item{
  flex: 0%;
}
/* 等价于 */
.item{
  flex-grow: 1;
  flex-shrink: 1;
  flex-basis: 0%;
}

.item2{
  flex: 10px;
}
/* 等价于 */
.item2{
  flex-grow: 1;
  flex-shrink: 1;
  flex-basis: 10px;
}
```



## flex 关键字



### flex: none



```css
flex-grow: 0;
flex-shrink: 0;
flex-basis: auto;
```



### flex: auto



```css
flex-grow: 1;
flex-shrink: 1;
flex-basis: auto;
```



### flex: initial



```css
flex-grow: 0;
flex-shrink: 1;
flex-basis: auto;
```



## flex: 双值



```css
.item{
  flex: 2 10px;
}
/* 等价于 */
.item{
  flex-grow: 2;
  flex-shrink: 1;
  flex-basis: 10px;
}

.item{
  flex: 2 3;
}
/* 等价于 */
.item{
  flex-grow: 2;
  flex-shrink: 3;
  flex-basis: 0%;
}
```