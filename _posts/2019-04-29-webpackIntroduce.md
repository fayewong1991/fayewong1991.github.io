---
layout:     post
title:      "webpack 简介与安装配置"
subtitle:   ""
date:       2019-04-29
author:     "Faye"
tags:
    - webpack
---

### 1.前言
现今的很多网页其实可以看做是功能丰富的应用，它们拥有着复杂的JavaScript代码和一大堆依赖包。为了简化开发的复杂度，前端社区涌现出了很多好的实践方法

- 模块化，让我们可以把复杂的程序细化为小的文件;
- 类似于TypeScript这种在JavaScript基础上拓展的开发语言：使我们能够实现目前版本的JavaScript不能直接使用的特性，并且之后还能能装换为JavaScript文件使浏览器可以识别；
- Scss，less等CSS预处理器
- ...

这些改进确实大大的提高了我们的开发效率，但是利用它们开发的文件往往需要进行额外的处理才能让浏览器识别,而手动处理又是非常繁琐的，这就为WebPack类的工具的出现提供了需求。
#### 1.1 什么是webpack
原文是：
>webpack is a module bundler.
webpack takes modules with dependencies and generates static assets representing those modules.

翻译一下：webpack是一个模块打包工具，处理模块之间的依赖同时生成对应模块的静态资源。
#### 1.2 它有啥用
![](http://img.blog.csdn.net/20160723105504240)

图中已经很清楚的反应了几个信息：

- webpack把项目中所有的静态文件都看作一个模块
- 模快之间存在着一些列的依赖
- 多页面的静态资源生成(打包之后生成多个静态文件，涉及到代码拆分)


### 2. 安装与基本配置
当我们[安装好webpack](http://webpackdoc.com/install.html)以后，有两种方式可以使用

1. 在终端使用

  最基础的命令
  ```
  webpack {entry file/入口文件} {destination for bundled file/存放bundle文件的地方}
  ```  
  只需要指定一个入口文件，webpack将自动识别项目所依赖的其它文件，不过需要注意的是如果你的webpack没有进行全局安装，那么当你在终端中使用此命令时，需要额外指定其在node_modules中的地址，继续上面的例子，在终端中属于如下命令
  ```
 //webpack非全局安装的情况
  node_modules/.bin/webpack {entry file/入口文件} {destination for bundled file/存放bundle文件的地方}
  ```
2. 创建config文件
Webpack拥有很多其它的比较高级的功能（比如说本文后面会介绍的loaders和plugins），尽管这些功能其实都可以通过命令行模式实现，但是正如已经提到的，这样不太方便且容易出错的，一个更好的办法是定义一个配置文件(webpack.config.js，执行webpack命令的时候，默认会执行这个文件)，这个配置文件其实也是一个简单的JavaScript模块，可以把所有的与构建相关的信息放在里面。
```
module.export = {
    entry : 'app.js',
    output : {
        path : 'assets/',
        filename : '[name].bundle.js'
    },
    module : {
        loaders : [
            // 使用babel-loader解析js或者jsx模块
            { test : /\.js\.jsx$/, loader : 'babel' },
            // 使用css-loader解析css模块
            { test : /\.css$/, loader : 'style!css' },
            // or another way
            { test : /\.css$/, loader : ['style', 'css'] }
        ]
    }
};
```
  - entry对应需要打包的入口js文件，
  - output对应输出的目录以及文件名，
  - module中的loaders对应解析各个模块时需要的加载器
  



