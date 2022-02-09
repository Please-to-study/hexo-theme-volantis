---
title: git撤销已推送到远端仓库的提交信息
seo_title: git撤销已推送到远端仓库的提交信息
toc: true
indent: true
comments: true
archive: true
cover: true
mathjax: false
pin: false
top_meta: true
bottom_meta: true
sidebar:
  - toc
icons: []
date: 2021-10-28 19:47:18
updated: 2021-10-28 19:47:18
categories:
- 实践中的问题
- Git
keywords: git撤销 推送(push) 远端仓库 提交(commit)信息
description: 当将错误代码推送到远端仓库时，该如何回退呢？
headimg: https://cdn.pkubailu.cn/img/git学习.png
thumbnail:
tags:
- git分支
---

{% note info::git撤销已推送(push)到远端仓库的提交(commit)信息的方法 %}

## 1.首先通过 git log 查看提交信息，以获得需要回退的版本号

```
$ git log
```

## 2.回退指定版本号

通过git reset -soft <版本号> 重置至指定版本的提交，达到撤销提交的目的。
```
$ git reset -soft <版本号>
## 参数 soft 指的是: 保留当前工作区，以便重新提交。
## 还可以选择参数hard，会撤销相应工作区的修改！！
```

## 3.通过git log 确认是否成功撤销

```
$ git log
```

## 4.修改远程仓库

```
## 通过 git push origin master -force 强制提交当前版本号，以达到撤销版本号的目的
$ git push origin master -force
```


## 5.修改代码，重新提交和推送

执行完第4步，本地和远程仓库便已经回退到指定的版本号了，可以继续修改代码进行开发了。
