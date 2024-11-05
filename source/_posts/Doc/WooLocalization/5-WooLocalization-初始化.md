---
title: WooLocalization-初始化
author: yy
comment: 'on'
tags:
  - WooLocalization
category:
  - 文档
  - WooLocalization
date: 2024-10-26 20:59:43
---



# 初始化
* 本地化初始化时必须加的几句代码

``` csharp
//是为了平台适配
Localization.SetRecorder(this);
//是为了有起手语言
Localization.SetDefaultLocalizationType(type);
//是为了有数据
Localization.SetContext(data);
```