---
title: grid布局
date: 2017-05-03 16:02:52
catefories: CSS
tags:
    - css
    - grid
---
## 简介
&emsp;&emsp;CSS网格布局（又称“网格”），是一种二维网格布局系统。CSS在处理网页布局方面一直做的不是很好。一开始我们用的是table（表格）布局，然后用float(浮动)，position（定位）和inline-block（行内块）布局，但是这些方法本质上是hack，遗漏了很多功能，例如垂直居中。后来出了flexbox(盒子布局)，解决了很多布局问题，但是它仅仅是一维布局，而不是复杂的二维布局，实际上它们（flexbox与grid）能很好的配合使用。Grid布局是第一个专门为解决布局问题而创建的CSS模块,2012年11月06日成立草案。
##初步了解和浏览器支持
&emsp;&emsp;使用Grid布局非常简单，你只需要给容器（container）定义：display:grid，并设置列（grid-template-columns）和 行（grid-template-rows）的大小，然后用grid-column和grid-row定义容器子元素（grid-item项目）的位置。与flexbox布局类似，一开始项目的排列顺序并不重要，可以放置在容器的任何位置，这也使得你非常容易通过媒体查询重新排列你的项目。想象一下，当你定义整个页面的布局时，你只需要几行CSS就可以完成页面重排以便适应各种屏幕宽度，这得有多么神奇！
&emsp;&emsp;目前浏览器还不支持Grid布局，IE10和IE11支持老的语法。如果你想体验Grid布局的强大，推荐使用开通过“体验新功能”的Chrome, Opera 或 Firefox， Chrome:打开浏览器，输入chrome://flags，找到"experimental web platform features"，启用并重启浏览器；Opera：输入opera://flags，与Chrome一样；Firefox:输入layout.css.grid.enabled。   
**浏览器支持情况：**
| Chome | safari | FireFox | Opera | IE | Android/iOS |
| :--- | :----: | ----: | :--- | :----: | ----: |
| 29+ | Not supported | 40+ | 28+  | 10+ | Not supported |

**重要术语**
&emsp;&emsp;在深入了解Grid布局概念之前，我们先了解一些术语。因为这些术语在概念上很相似，如果你不记住Grid定义的含义，会很容易将它们混淆，但是不用担心，这里术语很少。
<1>.网格容器（Grid Container）
元素应用display:grid，它是其所有网格项的父元素。下面例子container就是网格容器。
```html
<div class="container">
  <div class="item item-1"></div>
  <div class="item item-2"></div>
  <div class="item item-3"></div>
</div>
```
<2>.网格项（Grid Item）
网格容器的子元素，下面的item元素是网格项，但sub-item不是。
```html
<div class="container">
  <div class="item"></div> 
  <div class="item">
    <p class="sub-item"></p>
  </div>
  <div class="item"></div>
</div>
```
<3>.网格线（Grid Line）
&emsp;&emsp;组成网格线的分界线。它们可以是列网格线（column grid lines），也可以是行网格线（row grid lines）并且居于行或列的任意一侧，下面黄色线就是列网格线。
<div align="center">
    <img src="/images/gridlines.png" alt="grid line" title="grid line">
</div>

<4>.网格轨道（Grid Track）
&emsp;&emsp;两个相邻的网格线之间为网格轨道。你可以认为它们是网格的列或行，下面在第二个和第三个网格线之间的黄色部分为网格轨道。
<div align="center">
    <img src="/images/gridtrack.png" alt="grid track" title="grid track">
</div>
<5>.网格单元（Grid Cell）
&emsp;&emsp;两个相邻的列网格线和两个相邻的行网格线组成的是网格单元，它是最小的网格单元。下面行网格线1（row grid lines 1）、行网格线2（row grid lines 2）和列网格线1（column grid lines 1）、列网格线2（column grid lines2）组成的黄色区域为网格单元。
<div align="center">
    <img src="/images/gridcell.png" alt="grid cell" title="grid cell">
</div>
<6>.网格区（Grid Area）
&emsp;&emsp;网格区是由任意数量网格单元组成，下面行网格线1（row grid lines 1）、行网格线3（row grid lines 3）和列网格线1（column grid lines 1）、列网格线3（column grid lines3）组成的黄色区域为网格区。
<div align="center">
    <img src="/images/gridarea.png" alt="grid area" title="grid area">
</div>

[点击这里进入原创~](http://www.jianshu.com/p/d183265a8dad#)
