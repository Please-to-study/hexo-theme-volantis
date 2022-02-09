---
title: 一台电脑同时配置github与gitlab
seo_title: 一台电脑同时配置github与gitlab
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
date: 2021-10-28 21:20:09
updated: 2021-10-28 21:20:09
categories:
- 教程
- 同时配置github与gitlab
keywords: github gitlab 同时使用
description: 公司的电脑上默认配置了gitlab，但是我们在github上处理开源项目的时候也要用到github，这个时候就要在一台电脑上同时配置github和gitlab了。
headimg: https://cdn.pkubailu.cn/img/githubAndgitlab.webp
thumbnail:
tags:
- github
- gitlab
---

{% note info::想要在一台电脑上同时玩转github和gitlab的小伙伴请仔细阅读本文喔！ %}

公司的电脑上默认都会配置好gitlab，而我们在日常摸鱼的时候又难免用到github，这个时候一直手动切换账户便会很繁琐，所以我们就需要在电脑上同时配置好二者，让其在拉取和上传的时候自动识别。<br>
首先安装好git，当前git无论是哪个账户都可以，下面开始正式配置。<br>

## 0.准备工作
1. 学校或公司给的Gitlab账号的邮箱地址
2. 学校或公司给的Gitlab账号的账号名称
3. 你自己的Github账号的的邮箱地址

## 1.生成SSH密钥并配置
进入.ssh文件夹，如果没有就创建一个
```
cd ~/.ssh
```

1.这里我们先配置Gitlab，写入命令
```
ssh-keygen -t rsa -C "Gitlab账号的邮箱地址"
```
此时文件夹中生成了对应的Gitlab密钥的私钥id_rsa和公钥id_rsa_pub。

2.配置Gitlab公钥id_rsa_pub中的内容到学校或公司的Gitlab上,步骤如下：<br>
打开Gitlab -> 点击自己的头像 -> 下拉菜单有个Settings点进去 -> 点击左侧菜单中的SSH Keys -> 点击绿色按钮Add Key -> 用记事本打开公钥文件，全选复制 -> Title是对密钥的描述（可以随便起名字，但是建议有标识性一些，不然时间久了不知道这个秘钥对应着哪里）；复制内容粘贴到多行文本框 -> 确定
完了你就能看到下方显示的Your SSH keys，配置成功！

3.接下来我们配置Github，写入命令
```
ssh-keygen -t rsa -C 'github登录的邮箱' -f ~/.ssh/id_rsa_github
```
同样在文件夹中生成了Github私钥id_github_rsa和公钥id_github_rsa.pub

4.配置Github公钥github_rsa.pub中的内容到自己的Github上
过程和上述Gitlab公钥配置一样。

## 2.配置config文件
两边公钥都分别配置成功后，在.ssh文件夹中，创建一个config文件，没有后缀，添加如下配置：
```
Host github
    AddKeysToAgent yes
    HostName github.com
    User **@qq.com ## 你github的邮箱地址
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa_github
Host gitlab
    HostName http://git.corp.***.com  ## 填公司或学校的gitlab的Host
    User **@qq.com  ## 填公司或学校的邮箱地址
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa
```
保存文件

## 3.测试连接
```
## 执行命令
ssh -T git@github.com
```
如下图所示则表示连接成功
![](https://cdn.pkubailu.cn/img/github.png)

至此，你的电脑上github和gitlab便同时配置好啦！
