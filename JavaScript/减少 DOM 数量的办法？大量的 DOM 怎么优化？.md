---
title: 减少 DOM 数量的办法？大量的 DOM 怎么优化？
date: 2021-2-3
tags:
  - JS 优化
categories:
  - JavaScript
---

## 前提



当有大量的 DOM 时，访问 dom 和 重绘、重排都会消耗性能。



**重绘和重排是什么**



重绘是指一些样式的修改，元素的位置和大小都没有改变； 重排是指元素的位置或尺寸发生了变化，浏览器需要重新计算渲染树，而新的渲染树建立后，浏览器会重新绘制受影响的元素。



**浏览器渲染页面**



当我们的浏览器接收到从服务器响应的页面之后便开始逐行渲染，遇到 css 的时候会异步的去计算属性值，再继续向下解析 dom 解析完毕之后形成一颗 DOM 树，将异步计算好的样式（样式盒子）与 DOM 树相结合便成为了一个 Render 树，再由浏览器绘制在页面上。DOM 树与 Render树的区别在于：样式为 `display:none;` 的节点会在 DOM 树中而不在渲染树中。浏览器绘制了之后便开始解析 js 文件，根据 js 来确定是否重绘和重排。



**引起重绘和重排的原因**



产生重绘的因素：



- 改变visibility、outline、背景色等样式属性，并没有改变元素大小、位置等。浏览器会根据元素的新属性重新绘制。



产生重排的因素：



- 内容改变
- 文本改变或图片尺寸改变
- DOM元素的几何属性的变化

- - 例如改变 DOM 元素的宽高值时，原渲染树中的相关节点会失效，浏览器会根据变化后的 DOM 重新排建渲染树中的相关节点。如果父节点的几何属性变化时，还会使其子节点及后续兄弟节点重新计算位置等，造成一系列的重排。

- DOM 树的结构变化

- - 添加 DOM 节点、修改 DOM 节点位置及删除某个节点都是对 DOM 树的更改，会造成页面的重排。浏览器布局是从上到下的过程，修改当前元素不会对其前边已经遍历过的元素造成影响，但是如果在所有的节点前添加一个新的元素，则后续的所有元素都要进行重排。

- 获取某些属性

- - 除了渲染树的直接变化，当获取一些属性值时，浏览器为取得正确的值也会发生重排，这些属性包括：offsetTop、offsetLeft、 offsetWidth、offsetHeight、scrollTop、scrollLeft、scrollWidth、scrollHeight、 clientTop、clientLeft、clientWidth、clientHeight、getComputedStyle()。

- 浏览器窗口尺寸改变

- - 窗口尺寸的改变会影响整个网页内元素的尺寸的改变，即 DOM 元素的集合属性变化，因此会造成重排。

- 滚动条的出现（会触发整个页面的重排）



js 是单线程的，重绘和重排会阻塞用户的操作以及影响网页的性能，当一个页面发生了多次重绘和重排比如写一个定时器每500ms 改变页面元素的宽高，那么这个页面可能会变得越来越卡顿，我们要尽可能的减少重绘和重排。那么我们对于 DOM 的优化也是基于这个开始。



## 减少 DOM 数量的方法



1. 可以使用伪元素、阴影、CSS 实现的内容尽量不要使用 DOM 实现
2. 按需加载，减少不必要的渲染
3. 结构合理，语义化标签



## 大量 DOM 时的优化



### 1. 缓存 DOM 对象



在循环遍历时，不必要的 dom 节点先获取到，那么在循环里就可以直接引用，而不必去重新查询

```javascript
let rootElem = document.querySelector('#app');
let childList = rootElem.child; // 假设全是dom节点
for(let i = 0; i < childList.len; j++){
   ...
}
```



### 2. 缓存 NodeList



使用 `getElementByClassName()` 获取到的是一个 NodeList 类数组



NodeList 对象是动态的，每一次访问都会运行一次基于文档的查询。使用 `querySelectorAll()` 获取到的是静态 NodeList



```javascript
// 优化前
var lis = document.getElementsByTagName('li');

for(var i = 0; i < lis.length; i++) {
  // do something...  
}

// 优化后，将 length的值缓存起来就不会每次都去查询 length 的值，或者使用querySelectorAll
var lis = document.getElementsByTagName('li');

for(var i = 0, len = lis.length; i < len; i++) {
  // do something...  
}
```



而且由于 NodeList 是动态变化的，所以如果不缓存可能会引起死循环，比如一边添加元素，一边获取 NodeList 的 length：

```javascript
var divs = document.getElementsByTagName("div");
var i=0;

while(i < divs.length){
  document.body.appendChild(document.createElement("div"));
  i++;
}
```



### 3. 避免不必要的循环



优化前的代码访问了 10 次元素，而优化后的代码只访问了一次。

```javascript
// 优化前
for(var i = 0; i < 10; i++) {
    document.getElementById('ele').innerHTML += 'a'; 
} 
// 优化后 
var str = ''; 
for(var i = 0; i < 10; i++) {
    str += 'a'; 
}
document.getElementById('ele').innerHTML = str;
```



### 4. 事件委托



js 中的事件函数都是对象，如果事件函数过多会占用大量内存，而且绑定事件的 DOM 元素越多会增加访问 dom 的次数，对页面的交互就绪时间也会有延迟。所以诞生了事件委托，事件委托是利用了事件冒泡，只指定一个事件处理程序就可以管理某一类型的所有事件。

```javascript
// 事件委托前
var lis = document.getElementsByTagName('li');
for(var i = 0; i < lis.length; i++) {
   lis[i].onclick = function() {
      console.log(this.innerHTML);
   };  
}    
// 事件委托后
var ul = document.getElementsByTagName('ul')[0];
ul.onclick = function(event) {
   console.log(event.target.innerHTML);
};
```

事件委托前我们访问了 `lis.length` 次 li，而采用事件委托之后我们只访问了一次 ul。



### 5. 减少重排重绘



**5.1 改变一个 dom 节点的多个样式**



我们想改变一个 div 元素的宽度和高度，通常做法可以是这样：

```javascript
var div = document.getElementById('div1');
div.style.width = '220px';
div.style.height = '300px';
```

以上操作改变了元素的两个属性，访问了三次 dom，触发两次重排与两次重绘。我们可以在 css 里写一个 class，达到一次操作多个样式：

```css
.change {
    width: 220px;
    height: 300px;
}

document.getElementById('div').className = 'change';
```



**5.2 批量修改 dom 节点样式**



如果我们要改变一个 dom 集合的样式呢？ 如果遍历集合给每个节点加一个 className，虽然可行但访问了多次 dom 节点。“前提”提到过，如果一个节点的 `display` 属性为 `none` 那么这个节点不会存在于 render 树中，意味着对这个节点的操作也不会影响 render 树进而不会引起重绘和重排，基于这个思路我们可以实现优化：



- 将待修改的集合的父元素 `display: none;`
- 之后遍历修改集合节点
- 将集合父元素 `display: block;`

```javascript
// 假设增加的class为.change
var lis = document.getElementsByTagName('li');  
var ul = document.getElementsByTagName('ul')[0];
ul.style.display = 'none';
for(var i = 0; i < lis.length; i++) {
    lis[i].className = 'change';  
}
ul.style.display = 'block';
```



**5.3. 文档片段**



通过 [`document.createDocumentFragment()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/createDocumentFragment) 方法创建文档碎片节点，创建是一个虚拟的节点对象。利用这个特点可以将我们需要修改的 dom 一并修改完并保存到文档碎片中，然后一次性替换真实的 dom 节点。这样只会触发一次回流：

```javascript
var element  = document.getElementById('ul');=
var fragment = document.createDocumentFragment();
var browsers = ['Firefox', 'Chrome', 'Opera','Safari', 'Internet Explorer'];

browsers.forEach(function(browser) {
    var li = document.createElement('li');
    li.textContent = browser;
    fragment.appendChild(li);
});

element.appendChild(fragment);
```

如果需要对元素进行复杂的操作（删减、添加子节点），那我们应当先将元素从页面中移除，然后再对其进行操作，或者将其复制一个（cloneNode()），在内存中进行操作后再替换原来的节点。