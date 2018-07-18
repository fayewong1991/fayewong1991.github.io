---
layout:     post
title:      "git分支管理规范"
subtitle:   ""
date:       2017-11-15
author:     "Faye"
header-img: "img/post-sad.jpg"
tags:
    - GIT
image:
  feature: 
  teaser: mountains-teaser.jpg
  credit: Death to Stock Photo
  creditlink: ""
---

## 分支开发的基本流程
### 第一步：新建分支
每次开发新功能，都应该重新拉一条独立分支，一般我们都建在feature分支下。

```
# 获取主干最新代码
$ git checkout develop
$ git pull

# 新建一个开发分支feature/xxx
$ git checkout -b feature/xxx
```

### 第二步：提交分支commit
分支修改后，就可以提交commit了。

```
$ git status       //用来查看发生变动的文件。
$ git add file     //提交相应的文件夹，文件或者使用'git add .'提交所有的修改
$ git commit --verbose   //verbose参数，会列出 diff 的结果。
```

划重点！
建议大家还是按照一个可视化的git管理软件，如sourcetree，就可以比较清楚的看到改动点。

### 第三步：撰写提交信息
提交commit时，必须给出完整扼要的提交信息。
1. Commit message 的格式
每次提交，Commit message 都包括三个部分：Header，Body 和 Footer。

	```
	<type>(<scope>): <subject>
	// 空一行
	<body>
	// 空一行
	<footer>
	```
2. 常用的type:
  - feat：新功能（feature）
  - fix：修补bug
  - docs：文档（documentation）
  - style： 格式（不影响代码运行的变动）
3. body内容
  - 页面信息：如拼团页
  - 改动点：如修复布局错乱
  - 开发者花名：如@燕七
  
综上，可以得到一个示例：

```
git commit -m "fix: 拼团页修复布局错乱问题 @燕七"  //就可以清楚的知道是谁对哪个页面做了什么修改
```

具体的提交信息可以参考☞http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html 。后续可能会引入conventional-changelog，来辅佐编写commit message，以及便捷的生成changelog。

### 第四步：与主干同步
分支的开发过程中，要经常与主干保持同步。

```
$ git fetch origin
$ git rebase origin/develop
```

可以缩写为：

```
git pull --rebase origin develop 
```

### 第五步：合并commit
个人独立分支开发过程中，经常会有一堆commit，但是合并到dev的时候，为了能够更清晰易管理，就会进行commit合并。那么，怎样才能将多个commit合并呢？这就要用到 git rebase 命令。

```
$ git rebase -i origin/master
```

git rebase命令的i参数表示互动（interactive），这时git会打开一个互动界面，进行下一步操作。
如果是某两个分支的共同辅助祖先，可以在gitlab中，compare一下，找到相应的hash值

```
git rebase -i hash值
```

### 第六步：推送到远程仓库
合并commit后，就可以推送当前分支到远程仓库了。

```
$ git push --force origin myfeature
```

git push命令要加上force参数，因为rebase以后，分支历史改变了，跟远程分支不一定兼容，有可能要强行推送。

### 第七步：提merge request
gitlab和github在这一点，有些轻微的差别。gitlab是使用merge request，而github是使用pull request。

>为了让非核心成员提交的代码被核心成员接纳，非核心成员会向核心成员提出“申请（Request）”去自己的仓库指定分支中“拉取(pull)”最新的修改，这便是 Pull Request 的来源。
>那么 Merge Request 又是什么呢？GitLab 对此的解释是——一样的，没有区别。Merge 只是在强调最后的那个动作“合并（Merge）”。
>GitHub、Bitbucket 和码云（Gitee.com）选择 Pull Request 作为这项功能的名称
>GitLab 和 Gitorious 选择 Merge Request 作为这项功能的名称
其他日常分支处理，可以参考南烛文档☞http://galaxy.meili-inc.com/a/F2E/wxshop-doc/develop/git.html

盗图一张


