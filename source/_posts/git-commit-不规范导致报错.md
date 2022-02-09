---
title: git commit 不规范导致报错
seo_title: git commit 不规范导致报错
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
date: 2021-10-28 20:39:37
updated: 2021-10-28 20:39:37
categories:
- 实践中的问题
- Git
keywords: git commit 规范
description: 平常要养成符合git commit 规范的好习惯，不然就会像我一样报不规范错误了
headimg: https://cdn.pkubailu.cn/img/git学习.png
thumbnail:
tags:
- git commit 规范
---

{% note warning::要养成git commit 规范的良好习惯！ %}

## 某次提交(commit)代码的时候，突然报了一个有意思的错误，之前并没有遇到过，懵了一下。

{% span red::× %}  subject may not be empty [subject-empty] <br>
{% span red::× %}  type may not be empty [type-empty]

![](https://cdn.pkubailu.cn/img/gitcommitRules.png)

参考网上的一些资料，最后发现报这个错误有2种可能的情况
1. 下方图片红色框中冒号后面少一个空格
2. 下方图片红色框中是中文冒号要改为英文
![](https://cdn.pkubailu.cn/img/gitcommitRules2.png)
