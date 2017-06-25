---
title: 原生JavaScript之DOM操作
date: 2017-06-11 21:46:40
categories: JavaScript
tags: JavaScript
---
## 直接获取元素
```js
document.getElementById(selector); // 获取指定id的元素
document.getElementsByClassName(selector);// 获取指定class的元素的集合
document.getElementsByTagName(selector); // 获取指定标签名的元素集合
document.getElementsByName(selector); // 获取带有指定名称（name属性）的元素集合
```
## 获取父子兄弟元素
```js
el.parentNode; // 获取直接父元素
el.offsetParent; // 用于计算定位的父元素(position属性为relative、absolute或fixed的最近的父元素),若不存在这样的父元素，则为body，但是在低版本IE中可能为html
el.childNodes; // 所有子节点的集合，包含文本节点(换行或空白)
el.children; // 所有子元素的集合，不包含文本节点
el.firstChild;
el.lastChild;
el.nextSibling;
el.previousSibling; // 以上四个获取的节点，都包含文本节点
el.firstElementChild;
el.lastElementChild;
el.nextElementSibling;
el.previousElementSibling; // 以上四个获取的节点，不包含文本节点，但是在低版本IE里不兼容
```
## “增删改”元素
```js
let element = document.createElement(TagName); // 根据标签名创建标签
let textnode = document.createTextNode(content); // 根据内容创建文本节点
el.appendChild(element); // 将element标签添加为el的子节点，并且位于最后
el.insertBefore(element, child); // 将element标签添加为el的子节点，并且位于child之前，child参数为空则与appendChild方法效果一致
el.removeChild(element); // 删除el的子节点
el.replaceChild(new, old); // 用new替换old，new可以为新创建的节点也可以为已存在的节点
el.hasChildNode(); // 判断节点是否包含子节点，包含返回true，不包含返回false.
el.cloneChild(true/false); // 默认为false，只复制当前节点，为true时它的所有子节点也一起复制。它也是存在内存中，也需要向页面里添加。
``` 
## 操作元素属性
```js
let attr = document.createAttribute(name); // 创建一个属性节点
attr.value = "value"; // 为属性节点添加属性值
el.setAttributeNode(attr); // 将一个属性节点添加到el上
el.setAttribute(name, value); // 设置el上的属性，若此属性不存在则添加
el.getAttribute(name); // 获取el上的属性
el.removeAttribute(name); // 删除el上的属性
el.id;  // 元素的id名
el.className; // 元素的class名
```
## 获取元素的高宽
```js
el.style.width/height; // 获取元素content的宽高，但是若未设置style会得到空，这个留待后面再说
el.clientWidth/clientHeight; // 获取元素可视区的宽高，即为content+padding  元素相对于视窗可见部分的left和top，
el.offsetWidth/offsetHeight; // 获取元素占位宽高，即为content+padding+border   相当于绝对定位的位置left和top
el.scrollWidth/scrollHeight; // 与clientWidth类似，不过如果有滚轮会包括滚轮的宽度  // 滚轮的left和top
// 注意，margin与以上都无关
let W = document.body.clientWidth || window.innerWidth || document.documentElement.clientWidth; // 获取整个浏览器的可视区宽度，由于IE与firefox的实现有差异，所以需要兼容性写法
```
##文档碎片化处理
js操作dom是一个很耗性能的过程，在某些情况下，不得不进行dom循环操作，我们每次对dom的操作都会触发"重排"，这严重影响到能耗，一般通常采取的做法是尽可能的减少dom操作来减少"重排"。
面对循环操作dom的过程，我们选择使用文档碎片（creatDocumentFragment），将需要添加到dom中的内容都添加到文档碎片中，然后一次性的将文档碎片添加到dom树，这样就可以有效的减少操作dom的次数，减少重排的发生。
```html
<ul id="list"></ul>
<script>
    window.onload = function(){
        var oFragment = document.createDocumentFragment();
        var ul = document.getElementById("list");
        var oLi = null;
        for(var i= 0;i<10;i++){
            oLi = document.createElement("li");
            oLi.appendChild(document.createTextNode('item'+(i)));
            oFragment.appendChild(oLi);
        }
        ul.appendChild(oFragment);
    }
</script>
```