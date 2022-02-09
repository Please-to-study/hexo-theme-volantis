---
title: 开发新需求-拉取新的git分支
seo_title: 开发新需求-拉取新的git分支
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
date: 2021-10-28 18:17:19
updated: 2021-10-28 18:17:19
categories:
- 实践中的问题
- Git
keywords: 开发新需求 拉取新的git分支
description: 当开发新的需求时，要从原有master分支上check出新的开发分支
headimg: https://cdn.pkubailu.cn/img/git学习.png
thumbnail:
tags:
- git分支
---

{% note info::从master分支拉取新分支（dev）开发新需求 %}

## 1.切换到被copy的分支（master），并从远端拉取最新代码

```
$ git checkout master  ## 这里切换到线上分支，master分支一般为线上分支
$ git pull
```
![](https://cdn.pkubailu.cn/img/gitcheckout1.png)

## 2.从master分支check出新的开发分支

```
$ git checkout -b dev  ## dev表示你新建的分支名
```
![](https://cdn.pkubailu.cn/img/gitcheckout2.png)

## 3.把新建的分支(dev)push到远端仓库

```
$ git push origin dev  ## dev表示你新建的分支名
```
![](https://cdn.pkubailu.cn/img/gitcheckout3.png)

## 4.拉取远端分支

```
$ git pull
## 这个时候会报错，是因为你本地分支还没有与远端分支相关联的原因，git给与了你提示，按照提示操作即可
```
![](https://cdn.pkubailu.cn/img/gitcheckout4.png)

## 5.将本地分支与远端分支相关联

```
$ git branch --set-upstream-to=origin/gitStudy  ## 将<branch>替换为自己的分支
```
![](https://cdn.pkubailu.cn/img/gitcheckout5.png)

## 6.再次拉取验证

```
$ git pull
## 这个时候就成功建立了自己的分支，可以进行新的开发了
```
