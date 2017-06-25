---
title: 疯狂HCJ讲义（一）
date: 2017-06-15 22:04:22
categories: HTML
tags: HTML
---
## HTML5新增通用属性
### contentEditable属性
HTML5为大部分元素都增加了contentEditable属性,如果将该属性设置为true，浏览器会允许开发者直接在页面编辑该元素。
contentEditable属性具有可继承的特点，如果一个HTML元素的父元素是“可编辑”状态，那么它也是默认可编辑的。
<!--more-->
```HTML
<div contentEditable="true" 
style="width:500px;height:300px;border:1px solid #000"></div>
<p ondbclick="this.contentEditable=true">双击编辑内容</p>
```
### designMode属性
designMode属性相当于全局的contentEditable属性，如果把整个页面的designMode属性设置为on时，该页面所有支持contentEditable属性的元素都变成可编辑状态。
在JavaScript代码中只能修改整个HTML页面的designMode属性。
```js
document.designMode = "on";
```
### hidden属性
HTML5所有元素提供了hidden属性，这个hidden属性支持true,false属性，如果把某一元素的hidden设置成true，就意味着浏览器不显示该组件，也不会保留该组件所占用的空间。
_提示：hidden属性可以代替CSS样式的display=none。_
```HTML
<body>
<div id="target" hidden="true">这是文字</div>
<button 
onclick="var target=document.getElementById('target');
target.hidden=!target.hidden;">显示隐藏</button>
```
### spellcheck属性
HTML为input,textarea等元素增加了spellcheck属性，支持true,false属性，如果设置spellcheck="true",浏览器会负责对用户输入的文本内容执行输入检查，如果检查不通过，浏览器会对拼错的单词进行提示。
```HTML
<textarea spellcheck="true" rows="3" cols="40" ></textarea>
```
注：目前支持spellcheck属性的浏览器有chrome,Safari,Opera.Internet Explorer和Firefox暂不支持。
## HTML5新增常用元素
###文档结构元素
`<article>`侧重于表达一篇独立的，完整的文章；
`<section>`侧重于对页面内容进行分块；
`<nav>`专门用于定义页面上的导航栏
`<aside>`主要用来渲染成侧边栏
`<footer>`主要用于为<article.../>元素定义‘脚注’部分，包括该文章的版权信息，作者的授权信息等。
`<figure>`用于表示一块独立的图片区域，可包含多个<img>元素所代表的图片，还可包含一个`<figcaption>`作为定义图片区域的标题。
## HTML5头部和元信息
`<base/>`必须是空元素，可以使用id作为其唯一标识，还有以下两个属性：
`href`：指定所有链接的基准链接
`target`：指定超链接默认在哪个窗口打开链接，属性值只能是_blank,_self,_parent,_top。
```HTML
<head>
    ...
    <base target="_blank" href="http://www.baidu.com"/>
</head>
<body>
    <a href="index.php">啦啦啦啦</a>
</body> 
```