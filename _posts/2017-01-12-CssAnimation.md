---
layout:     post
title:      "CSS3动画"
subtitle:   ""
date:       2017-01-12
author:     "Faye"
tags:
    - css
---

# CSS 雪碧图

## 引言

雪碧图被运用在众多使用了很多小图标的网站上。相对于把每张小图标以.png格式文件的形式引用到页面上，使用雪碧图只需要引用一张图片，对内存和带宽更加友好。 

## 实现

假设我们通过.toolbtn的类，为应用该类的各元素提供一张背景图片：

```html
<style> 
.toolbtn {
  background:url(myfile.png); 
  display:inline-block; 
  height:20px; 
  width:20px; 
} 
</style>
```

背景位置，可以通过在background的url()直接定义X,Y轴的值，或者通过background-position属性来添加。例如：

```html
<style>
  #btn1 {background-position: -20px 0px}
  #btn2 {background-position: -40px 0px}
</style>
```

id=btn1的元素背景左移20px，id=btn2的元素背景左移40px（假设这两个元素的都添加了toolbtn类，应用了上面样式定义的图片效果）

类似的，你也可以使用下面的方式添加hover的状态：

```css
#btn:hover {
  background-position: [pixels shifted right]px [pixels shifted down]px;
}

```