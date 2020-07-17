---
layout:     post
title:      "webpack 简介与安装配置"
subtitle:   ""
date:       2019-04-29
author:     "Faye"
tags:
    - webpack
---

## 3. webapck常用到的各点拆分
### 3.1 entry相关
webpack的多入口配置

上例的简单配置中，只有一个入口文件，那么如果对应于一个页面需要加载多个打包文件或者多个页面想同时引入对应的打包文件的时候，应该怎么做？
entry : {
    app1 : './app1.js',
    app2 : './app2.js'
}
可以生成生成了两个入口文件

### 3.2 output相关
#### 3.2.1 output.chunkFilename
各个文件除了主模块以外，还可能生成许多额外附加的块，比如在模块中采用代码分割就会出现这样的情况。其中chunkFilename中包含以下的文件生成规则：
[id] 会被对应块的id替换.
[name] 会被对应块的name替换（或者被id替换，如果这个块没有name）.
[hash] 会被文件hash替换.
[chunkhash] 会被块文件hash替换.
例如，在output中如下设置：
output : {
    path : './assets/',
    filename : '[name].[hash].bundle.js',
    chunkFilename: "chunk/[chunkhash].chunk.js"
}

####3.2.2 打包为库

当我们希望把代码作为库发布的时候，就会涉及需要如何访问依赖库。常见的有CMD、AMD和window/global全局的方式访问。

怎么理解呢？我们先通过官网说的那个jquery的案例来理解。

有时我们希望我们通过script引入的库，如用CDN的方式引入的jquery，我们在使用时，依旧用require的方式来使用，但是却不希望webpack将它又编译进文件中。

    <script src="http://code.jquery.com/jquery-1.12.0.min.js"></script>
    
jquery的使用如下

```
// 我们不想这么用
// const $ = window.jQuery

// 而是这么用
const $ = require("jquery")
$("#content").html("<h1>hello world</h1>")
```

这时，我们便需要配置externals

```
module.exports = {
  ...
  output: {
    ...
    libraryTarget: "umd"
  },
  externals: {
    jquery: "jQuery"
  },
  ...
}
```

我们可以看看编译后的文件

```
({
  0: function(...) {
    var jQuery = require(1);
    /* ... */
  },
  1: function(...) {
    // 很明显这里是把window.jQuery赋值给了module.exports
    // 因此我们便可以使用require来引入了。
    module.exports = jQuery;
  },
  /* ... */
});
```

因此，根据不同的场景选择不同的方式，就会产出不同编译后的文件。
如果是自执行文件，就不需要使用library和libraryTarget了。  

#### 3.2.2.1 output.library
这个配置作为库发布的时候会用到，配置的名字即为**库的名字**，通常可以搭配libraryTarget进行使用。例如给webpack.config.js加上这样的配置：
output : {
    // ...
    library : 'testLibrary'
    // ...
}
那么实际上生成出来的main.bundle.js中会默认带上以下代码：
```
var testLibrary = (//....以前的打包生成的代码);
// 这样在直接引入这个库的时候，就可以直接使用`testLibrary`这个变量
```

#### 3.2.2.2 output.libraryTarget
规定了以哪一种方式输出你的库，比如：amd/cmd/或者直接变量，具体包括如下
"var" - 以直接变量输出(默认library方式) var Library = xxx (default)
"this" - 通过设置this的属性输出 this["Library"] = xxx
"commonjs" - 通过设置exports的属性输出 exports["Library"] = xxx
"commonjs2" - 通过设置module.exports的属性输出 module.exports = xxx
"amd" - 以amd方式输出
"umd" - 结合commonjs2/amd/root
例如以umd方式输出，如图：
![](http://img.blog.csdn.net/20160723142325583)

### 3.3 module相关
Loaders是webpack中最让人激动人心的功能之一了。通过使用不同的loader，**webpack通过调用外部的脚本或工具可以对各种各样的格式的文件进行处理**，比如说分析JSON文件并把它转换为JavaScript文件，或者说把下一代的JS文件（ES6，ES7)转换为现代浏览器可以识别的JS文件。或者说对React的开发而言，合适的Loaders可以把React的JSX文件转换为JS文件。
#### 3.3.1loader中基本配置  
Loaders需要单独安装并且需要在webpack.config.js下的modules关键字下进行配置，Loaders的配置选项包括以下几方面：

- test：一个匹配loaders所处理的文件的拓展名的正则表达式（必须）
- loader：loader的名称（必须）
- include/exclude:手动添加必须处理的文件（文件夹）或屏蔽不需要处理的文件（文件夹）（可选）；
- query：为loaders提供额外的设置选项（可选）

如在main.js中读取json的值，需要先安装json-loader
    npm install --save-dev json-loader
在config文件中加入：  
```
loaders: [
  {
    test: /\.json$/,
    loader: "json"
  }
]
```  
#### 3.3.2 loader中!代表的含义
>require("!style!css!less!bootstrap/less/bootstrap.less");
// => the file "bootstrap.less" in the folder "less" in the "bootstrap"
// module (that is installed from github to "node_modules") is
// transformed by the "less-loader". The result is transformed by the
// "css-loader" and then by the "style-loader".
// If configuration has some transforms bound to the file, they will not be applied.

代表加载器的流式调用，例如：
    
    { test : /\.css\.less$/, loader : 'style!css!less' }
      
就代表了先使用less加载器来解释less文件，然后使用css加载器来解析less解析后的文件，依次类推

#### 3.3.3 loaders中的include与exclude
include表示必须要包含的文件或者目录，而exclude的表示需要排除的目录
比如我们在配置中一般要排除node_modules目录，就可以这样写
{ 
    test : /\.js$/, 
    loader : 'babel',
    exclude : nodeModuleDir 
}
官方建议：优先采用include，并且include最好是文件目录

