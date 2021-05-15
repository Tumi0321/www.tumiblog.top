---
title: JS 事件冒泡、捕获与事件委托
date: 2021-3-31
tags:
  - JS 基础
categories:
  - JavaScript
---

## 概述



**事件冒泡（Event Bubbling）**：可以形象地比喻为把一颗石头投入水中，泡泡会一直从水底冒出水面。也就是说，事件会从最内层的元素开始发生，一直向上传播，直到 windown 对象。



**事件捕获（Event Capturing）**：与事件冒泡相反，事件会从最外层开始发生，直到最具体的元素。



**事件委托（Event Delegation）**：通过事件冒泡/捕获，将多个事件处理函数减为一个。





![2021/03/31/TOIMGc66460331023159N.png](https://picturebed.tumiblog.top/2021/03/31/TOIMGc66460331023159N.png)



## 事件



JavaScript 和 HTML之 间的交互是通过事件实现的。事件，就是文档或浏览器窗口发生的一些特定的交互瞬间。可以使用监听器（或事件处理程序）来预定事件，以便事件发生时执行相应的代码。通俗的说，这种模型其实就是一个观察者模式（事件是对象主题，而这一个个的监听器就是一个个观察者）。



## 事件流



事件流描述的就是从页面中接收事件的顺序。而 IE 和 Netscape 提出了完全相反的事件流概念。IE 事件流是事件冒泡，而 Netscape 的事件流就是事件捕获。



“DOM2级事件”规定事件流包括三个阶段，事件捕获阶段、处于目标阶段和事件冒泡阶段。首先发生的事件捕获，为截获事件提供了机会。然后是实际的目标接收了事件。最后一个阶段是冒泡阶段，可以在这个阶段对事件做出响应。



## addEventListener



“DOM2级事件”中规定的事件流同时支持了事件捕获阶段和事件冒泡阶段，而作为开发者，我们可以选择事件处理函数在哪一个阶段被调用。



`addEventListener` 方法用来为一个特定的元素绑定一个事件处理函数



`addEventListener` 有三个参数：



- 第一个参数是需要绑定的[事件类型](https://developer.mozilla.org/zh-CN/docs/Web/Events)
- 第二个参数是触发事件后要执行的函数
- 三个参数默认值是 `false`，表示在事件冒泡的阶段调用事件处理函数，如果参数为 `true`，则表示在事件捕获阶段调用处理函数。请看[例子](http://www.w3schools.com/jsref/tryit.asp?filename=tryjsref_element_addeventlistener_capture)



## 事件冒泡



IE 的事件流叫做事件冒泡。即事件开始时由最具体的元素（文档中嵌套层次最深的那个节点）接收，然后逐级向上传播到较为不具体的节点（文档）。所有现代浏览器都支持事件冒泡，并且会将事件一直冒泡到 `window` 对象。



三个 div 标签呈嵌套关系，假使三个元素都注册了相同的事件



```html
<div id="div1">
  <div id="div2">
    <div id="div3">click</div>
  </div>
</div>
```



三个元素间的触发顺序是 div3->div2->div1



![2021/03/31/TOIMGd590a0331023235N.png](https://picturebed.tumiblog.top/2021/03/31/TOIMGd590a0331023235N.png)



[示例 demo](https://codepen.io/tumi0321/pen/zYNoeox)



## 事件捕获



事件捕获的思想是不太具体的节点应该更早的接收到事件，而在最具体的节点应该最后接收到事件。事件捕获的用以在于事件到达预定目标之前捕获它。IE9+、Safari、Chrome、Opera 和 Firefox 支持，且从 `window` 开始捕获（尽管 DOM2 级事件规范要求从 `document`）。由于老版本浏览器不支持，所以很少有人使用事件捕获。



针对刚才的例子，三个元素间的触发顺序应该是 div1->div2->div3



![2021/03/31/TOIMG6ea6e0331023302N.png](https://picturebed.tumiblog.top/2021/03/31/TOIMG6ea6e0331023302N.png)



[示例 demo](https://codepen.io/tumi0321/pen/LYxbqyb)



## 事件委托



事件委托又叫事件代理，比如上面的代码，我们想要在点击每个 `div` 标签时，弹出对应的 `id`。常规做法是遍历每个 `div`，然后在每个 `div` 上绑定一个点击事件，这种做法在元素较少的时候可以使用，如果元素过多，那就会导致性能降低。这时候就可以使用事件委托。



[示例 demo](https://codepen.io/tumi0321/pen/gOgLqZd)



```js
var div1 = document.getElementById("div1")

div1.addEventListener("click",function (e) {
  var e = e || window.event
  if (e.target.nodeName.toLowerCase() == "div") {
    alert(e.target.id)
  }
},false
)
```



使用事件代理的好处不仅在于将多个事件处理函数减为一个，而且对于不同的元素可以有不同的处理方法。假如上述列表元素当中添加了其他的元素（如：a、span等），我们不必再一次循环给每一个元素绑定事件，直接修改事件代理的事件处理函数即可。



对于事件代理来说，在事件捕获或者事件冒泡阶段处理并没有明显的优劣之分，但是由于事件冒泡的事件流模型被所有主流的浏览器兼容，从兼容性角度来说建议使用事件冒泡模型。



## IE 浏览器兼容



IE 浏览器对 `addEventListener` 兼容性并不算太好，只有 IE9 以上可以使用。



![2021/03/31/TOIMGa45550331023353N.png](https://picturebed.tumiblog.top/2021/03/31/TOIMGa45550331023353N.png)



要兼容旧版本的 IE 浏览器，可以使用 IE 的 `attachEvent` 函数



> object.attachEvent(event, function)



两个参数与 `addEventListener` 相似，分别是事件和处理函数，默认是事件冒泡阶段调用处理函数，要注意的是，写事件名时候要加上"on"前缀（"onload"、"onclick"等）



## 阻止冒泡/捕获



**阻止事件冒泡：**



- 给子级加 `event.stopPropagation()`
- 事件处理函数中返回 `false`
- 阻止默认事件：`event.preventDefault()`





但是这两种方式是有区别的：`return false` 不仅阻止了事件往上冒泡，而且阻止了事件本身。`event.stopPropagation()` 则只阻止事件往上冒泡，不阻止事件本身。



**阻止事件捕获：**



- 通过设置 `addEventListener` 的第三个参数可以决定事件是否在捕获阶段触发
- `stopPropagation()` 方法既可以阻止事件冒泡，也可以阻止事件捕获，也可以阻止处于目标阶段
- 使用 DOM3 级新增事件 `stopImmediatePropagation()` 方法来阻止事件捕获，另外此方法还可以阻止事件冒泡



## 参考文章



- [理解事件捕获和事件冒泡](https://blog.csdn.net/sinat_27088253/article/details/51051115)
- [浅谈事件冒泡与事件捕获](https://segmentfault.com/a/1190000000749838)
- [你真的理解事件冒泡和事件捕获吗？](https://segmentfault.com/a/1190000012729080)
- [浅谈事件冒泡与事件捕获](https://zhuanlan.zhihu.com/p/100831300)
- [事件冒泡和事件捕获](https://yxyuxuan.github.io/2020/02/17/事件冒泡和事件捕获/)