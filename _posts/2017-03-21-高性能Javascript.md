---
layout:     post
title:      "高性能JavaScript 阅读笔记"
subtitle:   ""
date:       2017-03-21
author:     "Faye"
tags:
    - Javascript
---

## 第一章. 加载和运行

为什么JavaScript性能那么重要，因为大多数浏览器使用单线程处理UI和JavaScript运行，因此JavaScript运行了多长时间，那么在浏览器空闲下来响应用户输入之前的等待时间就有多长。

### 脚本位置

早期浏览器遇到<script>和<link>标签，会阻塞页面解析，依次下载完外部文件，再进行解析，进行下一步；现代的浏览器，可以允许并行下载script，但是依然会阻塞图片等资源的下载。所以推荐把js尽量靠近body标签底部位置。

浏览器在遇到<body>之前，不会渲染页面的任何部分。

### 成组脚本

每个HTTP请求都会产生额外的性能负担，因此下载一个100KB的文件比4个25KB的文件要快。所以减少引用外部脚本文件的数量。

### 非阻塞脚本

动态创建<script>标签。


## 第二章. 数据访问

### 4种数据存放方式：

1. literal values 字面量：string, number, null, undefined, boolean, array, object, regular expressions, function

2. variables变量: var创建

3. array items数组项

4. object members对象成员：

前两者的访问速度较快。

### 管理作用域

作用域与性能的关系：作用域过深，查找的时间就更长。

**把多次使用的dom节点保存在局部变量中**

### 改变作用域链

with和catch语句都会临时改变运行期上下文作用域链。新对象会放在作用域链的前端，则局部变量会被推入第二个作用域对象链中。反而影响了局部变量访问的效率。**建议不适用with，使用catch时，可以考虑把异常对象传给专业函数进行处理，这样，catch中没有局部变量访问，也就不会影响代码性能。**

###闭包、作用域和内存

闭包的作用域不被注销，且处于作用域链的前端，需要使用外部变量的时候

location.href 快于window.location.href



### DOM操作

不要用length属性作为循环判断的条件，因为每一次都要去访问一次length，效率很低，所以可以缓存在一个变量中，使用这个变量作为循环判断的条件

对DOM进行循环时，可以把集合的当前元素存在局部变量，这样读取局部变量效率更高

```
//slow
function collectionGlobal() {
    var coll = document.getElementsByTagName_r('div'), 
        len = coll.length,
        name = '';
    for (var count = 0; count < len; count++) {
        name = document.getElementsByTagName_r('div')[count].nodeName; 
        name = document.getElementsByTagName_r('div')[count].nodeType; 
        name = document.getElementsByTagName_r('div')[count].tagName;
    }

    return name; 

};
```



```

//fast
function collectionLocal() {
    var coll = document.getElementsByTagName_r('div'), len = coll.length,
        name = '';

    for (var count = 0; count < len; count++) {

         name = coll[count].nodeName;

         name = coll[count].nodeType; 

         name = coll[count].tagName;

    }

    return name;

};
```



```

//fastest
function collectionNodesLocal() {
    var coll = document.getElementsByTagName_r('div'), len = coll.length,
        name = '',
        el = null;

    for (var count = 0; count < len; count++) {

        el = coll[count]; 

        name = el.nodeName; 
        name = el.nodeType;
        name = el.tagName;
    }

    return name; 

};
```