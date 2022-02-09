---
title: 使用 Nginx 搭建图片服务器
seo_title: 使用 Nginx 搭建图片服务器
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
date: 2021-10-29 13:29:05
updated: 2021-10-29 13:29:05
categories:
- 教程
- Nginx图片服务器
keywords: Nginx 图片服务器
description: 使用nginx搭建图片服务器
headimg: https://cdn.pkubailu.cn/img/nginxImage.png
thumbnail:
tags:
- Nginx
- 图片服务器
---

{% note info::搭建图片服务器，直接可以访问网络图片 %}

## 1.在linux服务器上安装nginx

安装nginx的方法很多，大家可以自行google一下
安装完成之后执行以下命令,可以看到nginx的运行信息
```
ps -ef | grep nginx
```
![](https://cdn.pkubailu.cn/img/nginxinfo.png)

## 2.测试是否能成功访问
若出现下图所示就表示能够成功访问
![](https://cdn.pkubailu.cn/img/nginxtest.png)

## 3.找到图片文件夹路径
在服务器上新建一个image文件夹用来存放图片，或者找到你要存放图片文件夹的路径。
例如我将图片存放在image文件夹下，路径如下
![](https://cdn.pkubailu.cn/img/imagepath.png)

## 4.配置nginx的nginx.conf文件
该文件的位置可以通过以下命令来找到
```
ps -ef | grep nginx
## 第一行就会显示nginx.conf文件的路径，打开该文件进行编辑
```

## 5.配置nginx的nginx.conf文件
![](https://cdn.pkubailu.cn/img/nginxconf.png)
完整的nginx.conf文件代码
```
http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;
    keepalive_timeout  65;

    server {
        listen       80;
        server_name  localhost;

		location /images/ {
		   root /opt/myapp/;
		   autoindex on;
		}

        location / {
            root   html;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

    }
}
## 解释：
##   1)root则是将images映射到/opt/ormis/images/
##   2)autoindex on便是打开浏览功能。
```

## 6.保存nginx.conf配置，使用下面命令刷新配置
```
## 进入到/usr/local/nginx/sbin目录下 执行

nginx -t
## nginx: the configuration file /www/server/nginx/conf/nginx.conf syntax is ok
## nginx: configuration file /www/server/nginx/conf/nginx.conf test is successful
## 显示以上信息表明配置文件正确

## 执行刷新配置命令
nginx -s reload
```

## 7.浏览器直接访问图片
浏览器输入http://xxx.xxx.xxx.xxx/images/***.png即可访问到图片
