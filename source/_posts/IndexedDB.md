---
title: HTML5本地存储——IndexedDB
date: 2017-04-26 23:15:32
categories: HTML
tags: HTML
---
>IndexedDB 是一种低级API，用于客户端存储大量结构化数据(包括, 文件/ blobs)。该API使用索引来实现对该数据的高性能搜索。


最近有一项业务需求，就是可以离线存储数据，等到有网络信号的时候可以上传表单和图片。所以研究了一下HTML5的IndexedDB。
对于只存储某些字段的需求来说，可以使用Local Storage和 Session Storage来完成。但是一旦存储大量的数据，Local Storage和 Session Storage就远远不能满足需求了。这时，IndexedDB的强大之处就会体现出来了。
<!--more-->
(1).创建或者打开数据库
```
/* 对不同浏览器的indexedDB进行兼容 */
const indexeddb = window.indexedDB || window.webkitIndexedDB || window.mozIndexedDB || window.msIndexedDB;
/* 创建或连接数据库 */
const request = indexeddb.open(name, version);  // name：数据库名，version：数据库版本号
```
因为indexedDB在不同的浏览器上有兼容性，所以我们需要些一个兼容函数来兼容indexedDB。 
************
(2). 连接到数据库的回调函数
```
request.addEventListener('success', function(event){ 
    // 打开或创建数据库成功
}, false);

request.addEventListener('error', function(event){ 
    // 打开或创建数据库失败
}, false);

request.addEventListener('upgradeneeded', function(event){ 
    // 更新数据库时执行
}, false);
```
在连接到数据库后，request会监听三种状态：
    success：打开或创建数据库成功
    error：打开或创建数据库失败
    upgradeneeded：更新数据库
upgradeneeded状态是在indexedDB创建新的数据库时和indexeddb.open(name, version) version（数据库版本号）发生变化时才能监听到此状态。当版本号不发生变化时，不会触发此状态。数据库的ObjectStore的创建、删除等都是在这个监听事件下执行的。
********
(3).创建、删除ObjectStore
在indexedDB中，ObjectStore类似于数据库的表。
```
request.addEventListener('upgradeneeded', function(event){ 
    // 创建数据库实例
    const db = event.target.result;

    // 关闭数据库
    db.close();
    
    // 判断是否有ObjectStore
    db.objectStoreNames.contains(objectStoreName);
    
    // 删除ObjectStore
    db.deleteObjectStore(objectStoreName);
    
}, false);
```
可以用如下方法创建一个ObjectStore
```
request.addEventListener('upgradeneeded', function(event){ 
    // 创建数据库实例
    const db = event.target.result;
    
    // 判断是否有ObjectStore
    if(!db.objectStoreNames.contains(objectStoreName)){
        const store = db.createObjectStore(objectStoreName, {
            keyPath: keyPath  // keyPath 作为ObjectStore的搜索关键字
        });
        
        // 为ObjectStore创造索引
        store.createIndex(name,    // 索引
                          index,   // 键值
                          {
                              unique: unique  // 索引是否唯一
                          });
    }
    
}, false);
```
**********
(4).数据的增删改查
```
request.addEventListener('success', function(event){ 
    // 创建数据库实例
    const db = event.target.result;
    
    // 查找一个ObjectStore
    db.transaction(objectStoreName, wa);
    // wa为'readwrite'时，数据可以读写 
    // wa为'readonly'时，数据只读
    const store = transaction.objectStore(objectStoreName);
}, false);
```
数据库的增删改查
```
// 添加数据，当关键字存在时数据不会添加
store.add(obj);
// 更新数据，当关键字存在时覆盖数据，不存在时会添加数据
store.put(obj);
// 删除数据，删除指定的关键字对应的数据
store.delete(value);
// 清除ObjectStore
store.clear();
// 查找数据，根据关键字查找指定的数据
const g = store.get(value);
g.addEventListener('success', function(event){
    // 异步查找后的回调函数
}, false);
```
按索引查找数据
```
const index = store.index(indexName);
const cursor = index.openCursor(range);

cursor.addEventListener('success', function(event){
    const result = event.target.result;
    if(result){
        result.value       // 数据
        result.continue(); // 迭代，游标下移
    }
}, false);
```
按索引的范围查找数据
```
const index = store.index(indexName);
const cursor = index.openCursor(range);
/**
 * range为null时，查找所有数据
 * range为指定值时，查找索引满足该条件的对应的数据
 * range为IDBKeyRange对象时，根据条件查找满足条件的指定范围的数据
 */

// 大于或大于等于 
range = IDBKeyRange.lowerBound(value, true)   // (value, +∞)，>  value
range = IDBKeyRange.lowerBound(value, false)  // [value, +∞)，>= value
// 小于或小于等于，isOpen：true，开区间；false，闭区间
range = IDBKeyRange.upperBound(value, isOpen)
// 大于或大于等于value1，小于或小于等于value2
IDBKeyRange.bound(value1, value2, isOpen1, isOpen2)
```
>文章转载于[前端网](http://www.qdfuns.com/notes/17100/9c878a83e9f2f6c41518d204990ba635.html),如有侵权，即刻删除。