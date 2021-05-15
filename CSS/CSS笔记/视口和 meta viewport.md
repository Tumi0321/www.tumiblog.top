---
title: 视口和 meta viewport
date: 2021-2-13
tags:
  - CSS 基础
categories:
  - CSS
---

::: warning 

如果对一些基本概念不清楚的可以看上一篇[文章](http://tumiblog.top/blogs/CSS/CSS笔记/物理像素、逻辑像素和%20CSS%20像素的关系.html)

:::

## 视口



视口（viewport）代表当前可见的计算机图形区域。在 Web 浏览器术语中，通常与浏览器窗口相同，但不包括浏览器的 UI， 菜单栏等——即指你正在浏览的文档的那一部分。



一般我们所说的视口共包括三种：布局视口、视觉视口和理想视口。



### 布局视口



布局视口（layout viewport）：当我们以百分比来指定一个元素的大小时，它的计算值是由这个元素的包含块计算而来的。当这个元素是最顶级的元素时，它就是基于布局视口来计算的。



所以，布局视口是网页布局的基准窗口，在 PC 浏览器上，布局视口就等于当前浏览器的窗口大小（不包括 borders 、margins、滚动条）。



在移动端，布局视口被赋予一个默认值，大部分为 980px，这保证 PC 的网页可以在手机浏览器上呈现，但是非常小，用户可以手动对网页进行放大。



我们可以通过调用 `document.documentElement.clientWidth / clientHeight` 来获取布局视口大小。



![ ](http://picturebed.tumiblog.top/2021/02/13/TOIMG60ba80213115755N.jpg)



### 视觉视口



视觉视口（visual viewport）：用户通过屏幕真实看到的区域。



视觉视口默认等于当前浏览器的窗口大小（包括滚动条宽度）。



当用户对浏览器进行缩放时，不会改变布局视口的大小，所以页面布局是不变的，但是缩放会改变视觉视口的大小。



例如：用户将浏览器窗口放大了 200%，这时浏览器窗口中的 CSS 像素会随着视觉视口的放大而放大，这时一个 CSS 像素会跨越更多的物理像素。



所以，布局视口会限制你的 CSS 布局而视觉视口决定用户具体能看到什么。



我们可以通过调用 `window.innerWidth / innerHeight` 来获取视觉视口大小。



![ ](http://picturebed.tumiblog.top/2021/02/13/TOIMG220610213115809N.jpg)



### 理想视口



布局视口在移动端展示的效果并不是一个理想的效果，所以理想视口（ideal viewport）就诞生了：网站页面在移动端展示的理想大小。



如下图，在浏览器调试移动端时页面上给定的像素大小就是理想视口大小，它的单位正是设备独立像素。



![ ](http://picturebed.tumiblog.top/2021/02/13/TOIMGed8920213114556N.png)



可以通过 [Viewport Sizes](https://viewportsizes.com/) 查看主流设备的理想视口



页面的缩放系数 = 理想视口宽度 / 视觉视口宽度更为准确（缩放是相对于理想视口进行缩放）



所以，当页面缩放比例为 100% 时，CSS像素 = 设备独立像素，理想视口 = 视觉视口。



## Meta viewport



`<meta>` 元素表示那些不能由其它 HTML 元相关元素之一表示的任何元数据信息，它可以告诉浏览器如何解析页面。



我们可以借助 `<meta>` 元素的 `viewport` 来帮助我们设置视口、缩放等，从而让移动端得到更好的展示效果。



```html
<meta name="viewport" content="width=device-width; initial-scale=1; maximum-scale=1; minimum-scale=1; user-scalable=no;">
```



上面是 viewport 的配置，我们来看看它们的具体含义：



- width：设置 layout viewport 的宽度，为一个正整数，使用字符串”width-device”表示为设备宽度（缩放为 100% 时的逻辑像素宽度）。
- height：和 width 相对应，指定高度。
- initial-scale：用于指定页面的初始缩放比例，也即是当页面第一次加载的时候缩放比例（相对于理想视口进行缩放）。
- maximum-scale：用于指定用户能够放大的最大比例。
- minimum-scale：用于指定用户能够缩小的最大比例。
- user-scalable：控制用户是否可以手动缩放，yes 或 no。



为了在移动端让页面获得更好的显示效果，我们必须让布局视口、视觉视口都尽可能等于理想视口。



`device-width` 就等于理想视口的宽度，所以设置 `width=device-width` 就相当于让布局视口等于理想视口。但要注意的是，在 iphone 和 ipad 上，无论是竖屏还是横屏，宽度都是竖屏时理想视口的宽度。



![ ](http://picturebed.tumiblog.top/2021/02/13/TOIMG1377f0213120010N.png)



由于 `initial-scale = 理想视口宽度 / 视觉视口宽度`，所以我们设置 `initial-scale=1;` 就相当于让视觉视口等于理想视口。但是这次轮到 IE 无论是竖屏还是横屏都把宽度设为竖屏时理想视口的宽度了。


![ ](http://picturebed.tumiblog.top/2021/02/13/TOIMGf43b80213120022N.png)



如果 width 和 initial-scale=1 冲突时：

```html
<meta name="viewport" content="width=400, initial-scale=1">
```

浏览器会取他们两个中较大的那个值，例如当理想视口宽度为 320 时，取的是 400



为了解决 iphone、IE 和 ipad 的问题，可以同时设置两个属性来达到效果

```html
<meta name="viewport" content="width=device-width, initial-scale=1">
```

这时，1 个 CSS 像素就等于 1 个设备独立像素，而且我们也是基于理想视口来进行布局的，所以呈现出来的页面布局在各种设备上都能大致相似。



## 缩放



上面提到 width 可以决定布局视口的宽度，实际上它并不是布局视口的唯一决定性因素，设置 `initial-scale` 也有可能影响到布局视口，因为布局视口宽度取的是 width 和视觉视口宽度的最大值。



例如：若手机的理想视口宽度为 400px，设置 `width=device-width`，`initial-scale=2`，此时 `视觉视口宽度 = 理想视口宽度 / initial-scale` 即 200px，布局视口取两者最大值即 400px。



若设置 `width=device-width`，`initial-scale=0.5`，此时 `视觉视口宽度 = 理想视口宽度 / initial-scale` 即 800px，布局视口取两者最大值即 800px。



我们可以发现在 iphone 上不设置任何 viewport meta，却不会出现滚动条。但前面说了，各浏览器默认的 layout viewport 宽度一般都是 980。其实在 iphone 中会自动计算 initial-scale 这个值，以保证当前 layout viewport 的宽度在缩放后就是视觉视口的宽度，也就是说不会出现横向滚动条。当你指定了 initial-scale 的值后，这个默认值就不起作用了。



例如此时 layout viewport 的宽度为 980px，但我们可以看到浏览器并没有出现横向滚动条，浏览器默认的把页面缩小了。根据上面的公式，当前缩放值 = 理想视口宽度 / 视觉视口宽度，我们可以得出：当前缩放值 = 320 / 980



## 获取浏览器大小



浏览器为我们提供的获取窗口大小的`API`有很多，下面我们再来对比一下：



![ ](http://picturebed.tumiblog.top/2021/02/13/TOIMGcce7f0213120049N.jpg)



- `window.innerHeight`：获取浏览器视觉视口高度（包括垂直滚动条）。
- `window.outerHeight`：获取浏览器窗口外部的高度。表示整个浏览器窗口的高度，包括侧边栏、窗口镶边和调正窗口大小的边框。
- `window.screen.Height`：获取获屏幕取理想视口高度，这个数值是固定的，`设备的分辨率/设备像素比`
- `window.screen.availHeight`：浏览器窗口可用的高度。
- `document.documentElement.clientHeight`：获取浏览器布局视口高度，包括内边距，但不包括垂直滚动条、边框和外边距。
- `document.documentElement.offsetHeight`：包括内边距、滚动条、边框和外边距。
- `document.documentElement.scrollHeight`：在不使用滚动条的情况下适合视口中的所有内容所需的最小宽度。测量方式与 `clientHeight` 相同：它包含元素的内边距，但不包括边框，外边距或垂直滚动条。