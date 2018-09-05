---
title: CSS3之animation
date: 2018-08-17 16:43:59
categories: CSS
tags: CSS
---
首先，CSS Animation需要指定动画一个周期持续的时间，以及动画效果的名称。

```css
div:hover {
  animation: 1s 1s rainbow linear 3 forwards normal;
}
```

<!-- more -->
用keyframes关键字，定义rainbow效果。

```css
@keyframes rainbow {
  0% { background: #c00; }
  50% { background: orange; }
  100% { background: yellowgreen; }
}
```

上面的animation属性是简写，可以拆分成多个单独的属性

```css
div:hover {
  animation-name: rainbow;  // 效果名称
  animation-duration: 1s; // 执行一次所需是时间
  animation-timing-function: linear; // 动画的速度曲线, linear是匀速，还有ease, ease-in加速,ease-out减速，cubic-bezier(http://cubic-bezier.com) 自定义速度模式等
  animation-delay: 1s; // 延时多久执行动画
  animation-fill-mode:forwards; // 动画保持在结束状态, forwards是停在结束时，backwards 是停在开始时，none 回到动画没开始状态
  animation-direction: normal; // 动画播放的方向, 有alternate（start-end -> end-start -> start-end）、reverse(end-start -> end-start)
  animation-iteration-count: 3; // 动画执行次数 还有infinite
}
```

```css
div {
  animation: rainbow 1s linear infinite;
  animation-play-state: paused; // 没有鼠标没有悬停时，动画状态是暂停；
}

div:hover {
  animation-play-state: running; // 一旦悬停，动画状态改为继续播放。
}
```

举个栗子：实现动画的播放，暂停和重新开始
![效果图](http://oonulpk6h.bkt.clouddn.com/animation.gif)

html部分

```html
<div id="box" class="box"></div>
<p id="text"></p>
<div class="control">
  <button id="play" value="播放">播放</button>
  <button id="pause" value="暂停">暂停</button>
  <button id="restart" value="重新开始">重新开始</button>
</div>
```

css部分

```js
@keyframes mymove {
  0% {
    margin-left: 0px;
  }
  50% {
    margin-left: 400px;
  }
  100% {
    margin-left: 0px;
  }
}
@keyframes mymove1 {
  0% {
    margin-left: 0px;
  }
  50% {
    margin-left: 400px;
  }
  100% {
    margin-left: 0px;
  }
}
.box {
  margin: 50px 0;
  width: 100px;
  height: 100px;
  background-color: #5578a2;
}
.play {
  animation: mymove 5s infinite ease;
}
.restart {
  animation: mymove1 5s infinite ease;
}
.pause {
  animation-play-state: paused;
}
```

js部分

```js
var play = document.getElementById('play'),
    pause = document.getElementById('pause'),
    restart = document.getElementById('restart'),
    text = document.getElementById('text'),
    box = document.getElementById('box');
// 暂停
pause.addEventListener('click', function() {
  if (box.classList.contains('play')) {
    box.className = 'pause play box';
  } else {
    box.className = 'pause restart box';
  }
  text.innerHTML = this.value;
});
// 播放
play.addEventListener('click', function() {
  if (box.classList.contains('play')) {
    box.className = 'play box';
  } else {
    box.className = 'restart box';
  }
  text.innerHTML = this.value;
});
// 重新开始
restart.addEventListener('click', function() {
  if (box.classList.contains('play')) {
    box.className = 'restart box';
  } else {
    box.className = 'play box';
  }
  text.innerHTML = this.value;
});
```

参考资料
[CSS3 animation 属性妙用](https://aotu.io/notes/2016/11/28/css3-animation-properties/index.html)