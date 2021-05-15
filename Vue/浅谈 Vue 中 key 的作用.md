---
title: 浅谈 Vue 中 key 的作用
date: 2021-3-28
tags:
  - Vue 基础
categories:
  - Vue
---

::: tip

内容来自 coderwhy 老师的[视频](https://www.bilibili.com/video/BV15741177Eh?p=38)

:::



`key` 的特殊 attribute 主要用在 Vue 的虚拟 DOM 算法，在新旧 Nodes 对比时辨识 VNodes。如果不使用 key，Vue 会使用一种最大限度减少动态元素并且尽可能的尝试就地修改、复用相同类型元素的算法，而使用 key 时，它会基于 key 的变化重新排列元素顺序，并且会移除 key 不存在的元素。有相同父元素的子元素必须有独特的 key，重复的 key 会造成渲染错误。



当你想往一组相同的节点插入新节点时



![2021/03/28/TOIMG0fef10328070318N.png](https://picturebed.tumiblog.top/2021/03/28/TOIMG0fef10328070318N.png)



Diff 算法默认执行起来是这样的：



![2021/03/28/TOIMG8bda10328070342N.png](https://picturebed.tumiblog.top/2021/03/28/TOIMG8bda10328070342N.png)



即把 C 更新成 F，D 更新成 C，E 更新成 D，最后再插入 E，很没有效率



所以我们需要使用 key 来给每个节点做一个唯一标识，Diff 算法就可以正确的识别此节点，找到正确的位置区插入新的节点。



![2021/03/28/TOIMGf86bf0328070412N.png](https://picturebed.tumiblog.top/2021/03/28/TOIMGf86bf0328070412N.png)



所以一句话，**key的作用主要是为了高效的更新虚拟DOM。**