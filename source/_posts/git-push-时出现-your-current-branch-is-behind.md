---
title: git push 时出现 your current branch is behind
seo_title: git push 时出现 your current branch is behind
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
date: 2021-10-29 15:07:26
updated: 2021-10-29 15:07:26
categories:
- 实践中的问题
- Git
keywords: git push your current branch is behind
description: git push时出现 hint Updates were rejected because the tip of your current branch is behind
headimg: https://cdn.pkubailu.cn/img/git学习.png
thumbnail:
tags:
- git push
---

{% note warning:: Updates were rejected because the tip of your current branch is behind%}

## 出现问题的原因
某次在将本地仓库的代码push到远程仓库时，由于本地仓库与远程仓库文件不一致，push的时候出现 hint: Updates were rejected because the tip of your current branch is behind 的报错。
```
parkin@wangyan:~/duapp/consignment$ git push
To code.aliyun.com:xxx/xxxxxx.git
 ! [rejected]        wy -> wy (non-fast-forward)
error: failed to push some refs to 'git@code.aliyun.com:xxx/xxxxxx.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.

```
这种情况下，由于我确定我本地仓库的代码是我想要的，所以我采取了强制推送的方法，让远程仓库和本地仓库的代码保持一致。
```
git push -u origin 分支名 -f
```
