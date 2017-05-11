---
title: vue之入门
date: 2017-05-11 10:07:08
categories: Vue
tags:
    - Vue
    - JavaScript
---
## 安装
(1) 前提是下载安装好git和node,在此不赘述。
```js
// 安装vue-cli
npm install vue-cli -g
```
<!--more-->
(2) 创建一个基于webpack模板的项目
```js
vue init webpack my-project
// 其中webpack是使用模板
// mu-project 是项目名称
```
(3)安装依赖
```js
// 进入项目根目录下
cd my-project
// 安装依赖
npm install
```
(4)运行
```js
// 在命令行下输入
npm run dev
//在浏览器下输入
localhost:8080
```