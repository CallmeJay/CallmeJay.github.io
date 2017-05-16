---
title: canvas
date: 2017-05-12 09:45:25
categories: Canvas
tags: canvas
---
## 初识
首先，先获取canvas对象：
`var canvas = document.getElementById("canvas");`
context:一直觉得这个翻译成“上下文”真够蛋疼的，context是一个封装了很多绘图功能的对象，获取这个对象的方法是:
`var context = canvas.getContext("2d");` 
canvas元素绘制图像的时候有两种方法，分别是:
`context.fill(); // 填充` 
`context.stroke(); // 绘制边框`
<!--more-->
style:在进行图像绘制之前，需要先设置好绘制的样式
`context.fillStyle  //填充的样式`
`context.strokeStyle  //  边框的样式`

`context.lineWidth //边框的宽度`



[html5 canvas 详细使用教程](http://blog.csdn.net/clh604/article/details/8536059)