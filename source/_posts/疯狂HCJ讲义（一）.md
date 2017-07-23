---
title: 疯狂HCJ讲义（一）
date: 2017-06-15 22:04:22
categories: HTML
tags: HTML
---
## HTML5的常用元素与属性
### HTML5新增通用属性
#### contentEditable属性
HTML5为大部分元素都增加了contentEditable属性,如果将该属性设置为true，浏览器会允许开发者直接在页面编辑该元素。
contentEditable属性具有可继承的特点，如果一个HTML元素的父元素是“可编辑”状态，那么它也是默认可编辑的。
<!--more-->
```HTML
<div contentEditable="true" 
style="width:500px;height:300px;border:1px solid #000"></div>
<p ondbclick="this.contentEditable=true">双击编辑内容</p>
```
#### designMode属性
designMode属性相当于全局的contentEditable属性，如果把整个页面的designMode属性设置为on时，该页面所有支持contentEditable属性的元素都变成可编辑状态。
在JavaScript代码中只能修改整个HTML页面的designMode属性。
```js
document.designMode = "on";
```
#### hidden属性
HTML5所有元素提供了hidden属性，这个hidden属性支持true,false属性，如果把某一元素的hidden设置成true，就意味着浏览器不显示该组件，也不会保留该组件所占用的空间。
_提示：hidden属性可以代替CSS样式的display=none。_
```HTML
<body>
<div id="target" hidden="true">这是文字</div>
<button 
onclick="var target=document.getElementById('target');
target.hidden=!target.hidden;">显示隐藏</button>
```
#### spellcheck属性
HTML为input,textarea等元素增加了spellcheck属性，支持true,false属性，如果设置spellcheck="true",浏览器会负责对用户输入的文本内容执行输入检查，如果检查不通过，浏览器会对拼错的单词进行提示。
```HTML
<textarea spellcheck="true" rows="3" cols="40" ></textarea>
```
注：目前支持spellcheck属性的浏览器有chrome,Safari,Opera.Internet Explorer和Firefox暂不支持。
### HTML5新增常用元素
`<article>`侧重于表达一篇独立的，完整的文章；
`<section>`侧重于对页面内容进行分块；
`<nav>`专门用于定义页面上的导航栏
`<aside>`主要用来渲染成侧边栏
`<footer>`主要用于为<article.../>元素定义‘脚注’部分，包括该文章的版权信息，作者的授权信息等。
`<figure>`用于表示一块独立的图片区域，可包含多个<img>元素所代表的图片，还可包含一个`<figcaption>`作为定义图片区域的标题。
### HTML5头部和元信息
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
`<meta/>`用于定义页面元信息，也就是一些name-content对，该元素除了可以指定id属性外，还有以下三个属性：
`http-equiv`: 指定元信息的名称，该属性指定的名称具有特殊意义，它可以向浏览器传回一些有用的信息，帮助浏览器正确的处理网页内容。它支持的几个值有以下：
*Refresh*: 指浏览器多长时间自动刷新页面，如：
```HTML
<!--设置2秒后自动刷新指定网站-->
<meta http-equiv="Refresh" content="2,URL=http://www.baidu.com"/>
```
*Expires*: 指定网页的过期时间，一旦网页过期，必须重新从服务器下载，如：
```html
<meta http-equiv="Expires" content="Sat Sep 27 16:12:33 CST 2008">
```
`name`: 指定元信息的名称，该名称可以随意指定
`content`: 指定元信息值

## HTML5表单相关元素和属性
### HTML原有的表单及表单控件
#### 表单元素
`<form.../>`元素用于生成输入表单，该元素不会生成可视化部分，在HTML5以前表单控件都必须放在`<form.../>`之内。其特有属性有：
*action*:指定当点击确定提交表单时，该表单被提交到那个地址。
*method*:指定提交表单时的请求类型。可以是get/post,默认是get.
*target*: 指定使用哪种方式打开目标URL.
*name*: 指定表单的唯一名称，建议和id属性值保持一致。
*enctype*: 指定对表单内容进行编码所使用的字符集。有三个值:
application/x-www-form-urlencoded:默认的编码方式。只处理表单控件的value值，采用这种编码方式的表单会将value值处理成URL编码方式。
multipart/for-data:这种编码方式会以二进制流的形式处理表单数据，用于上传文件 
text/plain:主要适用于直接通过表单发送邮件的方式
#### 使用input元素
1.单行文本框：指定`<input.../>`type属性为text
2.密码输入框：指定`<input.../>`type属性为password
3.隐藏域：指定`<input.../>`type属性为hidden
4.单选框：指定`<input.../>`type属性为radio
5.复选框：指定`<input.../>`type属性为checkbox
6.图像域：指定`<input.../>`type属性为image,可以指定width和height属性
7.文本上传域：指定`<input.../>`type属性为file
>input元素有以下属性：
checked:设置单选框和复选框初始状态是否处于选中状态，
disabled:设置首次加载时禁用此元素。该元素无法获得输入，无法选中及响应状态。
maxlength: 该属性值是一个数字，指定文本框中允许输入的最大字符数。
readonly: 指定文本框内的值不允许用户修改
src: 指定图像域所显示的图像URL.只有type=image时可指定该属性

### HTML5新增的属性和元素
#### HTML5为表单控件新增的属性
>`form`属性：用于定义该表单控件所属的表单，该属性值应该是它所属表单的id。通过为表单控件添加指定的form属性，可以让表单控件定义在`<form.../>`外，从而提高灵活性。

```html
<body>
    <form id="addForm" action="add">
        物品名：<input type="text" name="name">
        <input type="submit" value="添加">
    </form>
    物品描述：<textarea name="desc" form="addForm"></textarea>
</body>
```
当提交该表单时，该多行文本域也会生成对应的请求参数。
>formaction属性
当页面中的一个表单，包含两个以上的提交按钮，但是程序需要提交到不同的action，这是可以为不同的提交按钮指定对应的formaction,可以动态的让表单提交到不同的URL,省去用js来实现。

```html
<form method="post">
    用户名：<input type="text" name="name"><br/>
    密码：<input type="password" name="name"><br/>
    <input type="submit" value="注册" formaction="regist">
    <input type="submit" value="登录" formaction="login">
</form>
```
>autofocus属性
当为某个表单控件添加该属性后，浏览器打开该页面时该组件会自动获得焦点。整个页面最多只能有一个表单控件可以获得该属性。<br>

>list属性
相当于文本框和下拉菜单组合的组件，既允许用户输入，也允许用户通过下拉菜单进行选择。list属性的值应该是一个`<datalist.../>`组建的id。意思是list属性必须和`<datalist.../>`元素一起使用。
`<datalist.../>`元素相当于一个看不见得`<select.../>`元素，用于生成一个隐藏的下拉菜单。当双击指定了list属性的文本框时，该文本框会显示`<datalist.../>`生成的下拉菜单。

```html
<form>
    请输入图书：<input type="text" name="name" list="books">
    <input type="submit" value="购买">
</form>
<datalist id="books">
    <option value="java">疯狂java讲义</option>  
    <option value="Android">疯狂Android讲义</option>  
</datalist>        
```
注：目前只有Firefox ,Opera最新版支持该属性。

#### 功能丰富的input元素
HTML5为`<input.../>`元素的tyoe属性新增了如下几种类型
`color`： 让`<input.../>`元素生成一个元素选择器。
`date`：让`<input.../>`元素生成一个日期选择器。
`time`：让`<input.../>`元素生成一个时间选择器。
`datetime`：让`<input.../>`元素生成一个UTC日期，时间选择器。
`week`：让`<input.../>`元素生成一个用户选择第几周的文本框。
`month`：让`<input.../>`元素生成一个月份选择器。
`email`：让`<input.../>`元素生成一个元素选择器,可指定multiple属性
`tel`：让`<input.../>`元素生成一个只允许输入手机号文本框。
`number`：让`<input.../>`元素生成一个只能输入数字的文本框。
`range`：让`<input.../>`元素生成一个拖动条。可指定min,max,step属性。
