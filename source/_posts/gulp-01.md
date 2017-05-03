---
title: gulp知识点
date: 2017-04-27 15:09:29
categories: gulp
tags: gulp
---
## gulp安装
1. **安装node.js**
    1. 说明：gulp是基于nodejs，理所当然需要安装nodejs 
2. **全局安装gulp**
    1. 说明：全局安装gulp目的是为了通过她执行gulp任务；
    2. 安装：命令提示符执行npm install gulp -g；
    3. 查看是否正确安装：命令提示符执行gulp -v，出现版本号即为正确安装。
<!--more-->
3. **新建package.json文件**
    1. 说明：package.json是基于nodejs项目必不可少的配置文件，它是存放在项目根目录的普通json文件；
    2. 执行命令提示符执行npm init进行手动安装
4. **安装本地gulp及其插件**
    1. 执行命令行npm install gulp --save-dev安装本地gulp
    2. 安装gulp插件：以gulp—sass为例
 执行命令npm install gulp-sass即可安装gulp-sass插件，安装完成后即可在node_modules文件夹下查看到新安装的插件
5. **新建gulpfile.js文件（重要）**
    1. 说明：gulpfile.js是gulp项目的配置文件，是位于项目根目录的普通js文件（其实将gulpfile.js放入其他文件夹下亦可）
    2. gulpfile.js文件用法（以gulp-sass为例）
      + 导入工具包 require('node_modules里对应模块')
        ```js
        var gulp = require("gulp");
        var gulp-sass = require("gulp-sass");
        ```
      + 定义一个testLess任务(自定义)
        ```js
        gulp.task('testLess',function(){
        gulp.src('src/less/index.less')     //该任务针对的文件
            .pipe(less())      //该任务调用的模块
            .pipe(gulp.dest('src/css'));     //将会在src/css下生成index.css
            });

        gulp.task("default",["watch"],function(){ //定义默认任务 并让gulp监视文件变化自动执行
            gulp.watch("sass/*.scss",["sass"]);       
        })
        ```
## gulp常用插件
1. **gulp-uglify(JS压缩)**
*安装：npm install --save-dev gulp-uglify*
```js
var gulp = require('gulp'),
var rename= require('gulp-rename')
var uglify= require("gulp-uglify");

gulp.task('rename',function() {
    gulp.src('src/**/*.js')
    .pipe(uglify())//压缩
    .pipe(rename('index.min.js'))    
    .pipe(gulp.dest('build/js'));
});

gulp.task('default',['rename']);
uglify= require("gulp-uglify");
```
2. **gulp-minify-html（html压缩）**
*安装：npm install --save-dev gulp-minify-html*
```js
var gulp = require('gulp'),
var minifyHtml= require("gulp-minify-html");

gulp.task('minify-html',function() {
    gulp.src('src/**/*.html')//要压缩的html文件
    .pipe(minifyHtml())//压缩
    .pipe(gulp.dest('build'));
});

gulp.task('default',['minify-html']);
```
3. **gulp-concat (js文件合并)**
*安装：npm install --save-dev gulp-concat*
```js
var gulp = require('gulp'),
concat= require("gulp-concat");

gulp.task('concat',function() {
       gulp.src('src/**/*.js')  //要合并的文件
       .pipe(concat('index.js'))//合并匹配到的js文件并命名为"index.js"
      .pipe(gulp.dest('build/js'));
});

gulp.task('default',['concat']);
```
4. **gulp-less**
*安装：npm install –save-dev  gulp-less*
```js
var gulp = require('gulp'),
var less= require("gulp-less");

gulp.task('compile-less',function() {
    gulp.src('src/less/*.less')
    .pipe(less())
    .pipe(gulp.dest('build/css'));
});

gulp.task('default',['compile-less']);
```
5. **gulp-sass**
*安装：npm install –save-dev  gulp-sass*
```js
var gulp = require('gulp'),
var sass= require("gulp-sass");

gulp.task('compile-sass',function() {
    gulp.src('src/sass/*.sass')
    .pipe(sass())
    .pipe(gulp.dest('build/css'));
});

gulp.task('default',['compile-sass']);
```
6. **gulp-imagemin（图片压缩）**
*安装：npm install –save-dev  gulp-imagemin*
```js
var gulp = require('gulp');
var imagemin = require('gulp-imagemin');

gulp.task('uglify-imagemin',function() {returngulp.src('src/images/*')
       .pipe(imagemin())
       .pipe(gulp.dest('build/images'));
});

gulp.task('default',['uglify-imagemin']);
```