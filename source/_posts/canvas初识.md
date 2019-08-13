---
title: canvas初识
date: 2019-07-11 15:31:38
tags: canvas
---
## 什么是Canvas

Canvas是HTML5新增的组件，它就像一块幕布，可以用JavaScript在上面绘制各种图表、动画等。

没有Canvas的年代，绘图只能借助Flash插件实现，页面不得不用JavaScript和Flash进行交互。有了Canvas，我们就再也不需要Flash了，直接使用JavaScript完成绘制。

一个Canvas定义了一个指定尺寸的矩形框，在这个范围内我们可以随意绘制

```html
<canvas id="canvas" width="300" height="200"></canvas>
```

<!--more-->

在使用Canvas前，用canvas.getContext来测试浏览器是否支持Canvas：

```js
var canvas = document.getElementById('canvas')
if (canvas.getContext) {
    console.log('你的浏览器支持Canvas!')
} else {
    console.log('你的浏览器不支持Canvas!')
}
// Internet Explorer 8 以及更早的版本不支持 <canvas> 元素
```

绘制2D图形，使用`getContext('2d')`方法让我们拿到一个CanvasRenderingContext2D对象，所有的绘图操作都需要通过这个对象完成。

```js
const ctx = canvas.getContext('2d')
```

## 绘制线段

```js
ctx.strokeStyle = 'red' // 描绘色
ctx.lineWidth = 2 // 线条宽度
ctx.moveTo(x0,y0)
ctx.lineTo(x1,y1)
// ...
ctx.stroke() // 描绘路径
ctx.fillStyle = '#fff' // 填充色
ctx.fill() // 填充闭合区域

ctx.beginPath()  // 起始一条路径，或重置当前路径 之后的绘制不会对之前的线再绘制一次
ctx.closePath()  // 创建从当前点回到起始点的路径 闭合线路
```

## 绘制矩形

```js
// 绘制矩形
ctx.rect(x,y,width,height) // 创建矩形
ctx.stroke()
ctx.fill()

ctx.fillStyle = '#fff' // 填充色 默认黑色
// 线性渐变色
const my_gradient=ctx.createLinearGradient(x1,y1,x2,y2); // 起始点坐标x1,y1 终点坐标x2,y2 在这两点直线 间颜色渐变
my_gradient.addColorStop(0, color1);
my_gradient.addColorStop(1, color2);
ctx.fillStyle=my_gradient;
ctx.fillRect(x,y,width,height)

// 绘制边框矩形
ctx.strokeStyle = 'red' // 描绘色
ctx.lineWidth = 2 // 线条宽度
ctx.strokeRect(x,y,width,height)

ctx.shadowBlur=20 // 设置阴影
ctx.shadowColor="black"
ctx.shadowOffsetX = value  // 设置或返回阴影距形状的水平距离
ctx.shadowOffsetY = value  // 设置或返回阴影距形状的垂直距离
```

## 绘制圆形

```js
ctx.arc(x,y,r,sAngle,eAngle,counterclockwise);  // 圆中心坐标x, y, 半径， 起始角度，结束角度，是否逆时针绘图

//方法在画布上创建介于两个切线之间的弧/曲线。
ctx.arcTo(x1,y1,x2,y2,r) // 两线相交点坐标x1,y1, 终点坐标x2,y2， 弧的半径 如果当前端点不是弧线起点，arcTo()方法还将添加一条当前端点到弧线起点的直线线段。
ctx.stroke();                // 进行绘制
```

## 文字

```js
// 设置或返回文本内容的当前字体属性
ctx.font = 'bold 12px arial'
ctx.textAlign = 'start|end|center|left|right' // 文本的对齐方式
context.textBaseline="top|hanging|middle|bottom"; //  基线在文字水平方向的位置。
ctx.fillText(text,x,y,maxWidth); // 在画布上绘制“被填充的”文本 文字 位置 x,y, 允许的最大文本宽度
ctx.strokeText() // 在画布上绘制文本（无填充）同上
ctx.measureText().width // 返回包含指定文本宽度的对象 宽度
```

## 图像

```js
ctx.drawImage(img,x,y,width,height); // 图像源DOM 起始位置x,y 设置在画布上的图像宽高 在onload后执行
```

## 点击事件

```js
canvas.addEventListener('click', (ev) => {
    let x
    let y
    if (ev.layerX || ev.layerX === 0) {
      x = ev.layerX
      y = ev.layerY
    } else if (ev.offsetX || ev.offsetX === 0) { // Opera
      x = ev.offsetX
      y = ev.offsetY
    }
    return {
      x,
      y
    }
})
```
