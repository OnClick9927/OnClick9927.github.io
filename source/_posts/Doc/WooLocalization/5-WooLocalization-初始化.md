---
title: 5-WooLocalization-初始化
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
* 本地化初始化时必须加的几句代码 否则会报错

``` csharp
//脚本需要先继承ILocalizationPrefRecorder接口
//然后实现这两个方法
public LocalizationPref Read()
{
  return null;
}

public void Write(LocalizationPref pref)
{
}

//此代码写在初始化方法里
//是为了平台适配
Localization.SetRecorder(this);
//是为了有起手语言
Localization.SetDefaultLocalizationType(type);
//是为了有数据
Localization.SetContext(data);
```