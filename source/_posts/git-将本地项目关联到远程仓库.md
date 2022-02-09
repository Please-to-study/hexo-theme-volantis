---
title: git 将本地项目关联到远程仓库
seo_title: git 将本地项目关联到远程仓库
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
date: 2021-10-28 20:26:28
updated: 2021-10-28 20:26:28
categories:
- 实践中的问题
- Git
keywords: git 关联远程仓库
description: git 将本地项目与远程仓库相关联的方法
headimg: https://cdn.pkubailu.cn/img/git学习.png
thumbnail:
tags:
- git关联远程仓库
---

{% note info::git 将本地项目与远程仓库相关联的方法 %}

## 1.首先在项目目录下初始化本地仓库

```
$ git init
```

## 2.添加所有文件( . 表示所有)

```
$ git add .
```

## 3.提交所有文件到本地仓库

```
$ git commit -m "备注信息"
```

## 4.连接到远程仓库

```
$ git remote add origin 你的远程仓库地址
```

## 5.将项目推送到远程仓库

```
$ git push -u origin master
```
