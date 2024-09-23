---
title: Git
author: 灵
comment: 'on'
tags:
  - GIT
category:
  - 工具
  - GIT

date: 2024-09-23 13:31:10
---

## 删除本地远程TAG
``` shell
1、git tag -d $(git tag -l)      //删除本地tag
2、git fetch                 //拉取远程tag
3、git push origin --delete $(git tag -l)        //删除远程tag
4、git tag -d $(git tag -l)      //删除本地tag
```