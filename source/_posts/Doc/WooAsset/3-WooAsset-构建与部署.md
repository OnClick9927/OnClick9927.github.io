---
title: WooAsset-构建与部署
author: 灵
tags:
  - WooAsset
category:
  - 文档
  - WooAsset
date: 2023-06-27 15:58:38
---
# 构建
* 设置好需要打包的组
* 点击Tools/WooAsset/Build
* 稍等片刻即可完成打包，内容全都在输出目录下
![Alt text](../../../Pic/Doc/WooAsset/firstopen.png)

# 部署
```
CDN
└─android
    ├─v1.0（APP版本）
    ├─v1.1（APP版本）
    └─v2.0（APP版本）
└─iphone
    ├─v1.0（APP版本）
    ├─v1.1（APP版本）
    └─v2.0（APP版本）
```
* 也可以允许下面的目录结构
* 所有的版本文件都在一起
```
CDN
└─android
└─iphone
```
