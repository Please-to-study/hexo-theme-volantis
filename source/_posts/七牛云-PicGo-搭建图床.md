---
title: 七牛云 + PicGo 搭建图床
seo_title: 七牛云 + PicGo 搭建图床
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
date: 2021-10-29 15:43:13
updated: 2021-10-29 15:43:13
categories:
- 教程
- 图床
keywords: 七牛云 PicGo 搭建图床
description: 使用七牛云 + PicGo 搭建图床，实现图片便捷上传
headimg: https://cdn.pkubailu.cn/img/imagebed.png
thumbnail:
tags:
- 图床
---

{% note quote:: 赶快搭建起自己的图床，实现行云流水的markdown笔记吧 %}

## 背景
无论大家使用Typora等markdown笔记软件记笔记还是搭建自己的个人博客，其实都离不开图床，markdown对于图片只会记录它的链接。
所以一旦笔记文件与本地图片的相对位置发生改变，图片就会裂开了，如果我们在笔记中记录的是图片的网络地址就不会发生这样的事了。

## 1.创建七牛云图床
1.创建[七牛云账号](https://sso.qiniu.com/)
2.进行实名认证
3.创建对象存储空间
![](https://cdn.pkubailu.cn/img/qiniucreate.png)
存储空间名字自己命名一个即可。存储区域选择离自己所在地较近的就行，访问控制选择公开空间，毕竟wiki也说了要带有外链服务以供分享，填写完后点击确定创建即可。
创建完成后主页如下所示。
![](https://cdn.pkubailu.cn/img/qiniucname.png)
点击图中的绑定域名，绑定自己的域名，七牛云提供的免费域名只有30天，这里建议绑定自己的域名。
![](https://cdn.pkubailu.cn/img/qiniucnameconf.png)
如果勾选了https选项，会要求你上传ssl证书,这里你可以购买七牛云的证书，也可以上传自有证书。
![](https://cdn.pkubailu.cn/img/qiniucertificate.png)
如果你选择上传自有证书的话，一定要注意下面这个地方，我当初上传时浪费了不少时间。
![](https://cdn.pkubailu.cn/img/uploadcertificate.png)
添加成功后七牛云会提供CNAME地址,并会有配置CNAME的教程，按照教程配置去域名提供商配置即可。
![](https://cdn.pkubailu.cn/img/cnameconf.png)
添加解析记录后回到主页，点击内容管理，修改外链默认域名并保存。
![](https://cdn.pkubailu.cn/img/qiniucontentconfig.png)

## 2.下载并配置PicGo
1.下载地址： https://github.com/Molunerfinn/PicGo/releases ，还可以使用 Homebrew 来安装 PicGo: brew install picgo --cask。
2.下载安装后打开，在图床设置中对七牛图床进行设置，设置图如下
存储区域对照表

|  存储区域   | 地域简称  |
|  :----:  | :----:  |
| 华东  | z0 |
| 华北  | z1 |
| 华南  | z2 |
| 北美  | na0 |
| 东南亚  | as0 |

![](https://cdn.pkubailu.cn/img/qiniuimagebedconfig.png)
两个Key作为连接凭证，可以在七牛云的个人面板-秘钥管理中查看，复制过来即可。设定访问网址必须添加http://或https://，不然无效。输入完后点击确定并设为默认图床。

## 总结
至此，配置的整个流程就算结束了，操作起来并不复杂，还是比较简单的，但是用起来就非常爽了，心动不如行动！
