---
layout:     post
title:      "git rebase vs git merge"
subtitle:   ""
date:       2017-12-21
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

## git merge

- 记录下合并动作 很多时候这种合并动作是垃圾信息
- 不会修改原 commit ID
- 冲突只解决一次
- 分支看着不大整洁，但是能看出合并的先后顺序
- 记录了真实的 commit 情况，包括每个分支的详情

## git rebase

- 改变当前分支 branch out 的位置
- 得到更简洁的项目历史
- 每个 commit 都需要解决冲突
- 修改所有 commit ID