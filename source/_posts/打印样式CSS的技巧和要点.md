---
title: 打印样式CSS的技巧和要点
date: 2017-07-09 22:07:50
categories: CSS
tags: CSS
---
不经过任何处理而直接打印网站上的页面会得到一个不理想的效果。

我们WEB开发人员可以简单采用几个要点来使之达到较为合适的效果：
- 使用响应式布局设置打印的效果
- 在合适的时候打印背景图片和颜色
- 添加显示的网址或页面链接，以供参考
- 使用css filter 提高打印的图形效果
<!--more-->
### 针对打印的样式，而不是屏幕显示样式
首先，我们需要使用媒体查询（media query）针对打印样式设置。
```css
@media print {
 // write some styles
}
```
重新针对打印写CSS样式是没有必要的，我们只需要针对差异设置打印的样式覆盖掉之前的默认样式。

大多数的浏览器会自动根据打印更改颜色，以节省打印原料，但是我们还是尽可能的手工设置一下。

为了达到最佳效果，使颜色清晰明了，我们至少需要包含一下基本的打印样式。
```css
@media print { body { color: #000; background: #fff; } }
```
对于打印，大多数情况下我们不需要打印整个页面，只需要打印一个简洁的能够突出需要信息的页面，那么我们将不相关的部分隐藏掉（如：导航条、背景图片）。
```css
/* Default styles */

h1 {
   color: #fff;
   background: url(banner.jpg);
}

@media print {
   h1 {
      color: #000;
      background: none;
   }

   nav, aside {
      display: none;
   }
}
```
在编写打印样式表的时候，你要注意要使用厘米(cm),英寸(inch)或者磅(pt)作为单位而不是屏幕像素单位，实际的单位对打印非常有用。

为了保证打印样式有用，写CSS样式使打印的内容距离纸张边缘，看起来更好，需要使用 @page 这个语法：
```css
@media print {
   h1 {
      color: #000;
      background: none;
   }

   nav, aside {
      display: none;
   }

   body, article {
      width: 100%;
      margin: 0;
      padding: 0;
   }

   @page {
      margin: 2cm;
   }
}
```
为了保证不被跨页打印，如一个标题和内容在页面底部被分开：
```css
 h2, h3 { page-break-after: avoid; }
```
另一种情况是要防止图片过宽而超出纸张边缘：
```css
img {
   max-width: 100% !important;
}
```
第三个要点是确保 articles 文章标签的内容，在新的一页开始：
```css
article {
   page-break-before: always;
}
```
最后，还要注意列表和图片不被分开在不同的页：
```css
ul, img {
   page-break-inside: avoid;
}
```
### 背景图片和颜色
对于一些网站，颜色和背景图还是非常必要需要遵循的。如果用户是在 webkit 内核浏览器上打印的话，我们可以强制打印机打印屏幕上所看到的颜色（即强制在打印页面上出现任何的背景图和颜色），一般来说彩色打印机可以做到这点，我们需要一个单独的媒体查询：
```css
@media print and (color) {
   * {
      -webkit-print-color-adjust: exact;
      print-color-adjust: exact;
   }
}
```
遗憾的是，这不能马上应用于firefox opera 和IE.
>[传送门~](http://blog.jobbole.com/44662/)

