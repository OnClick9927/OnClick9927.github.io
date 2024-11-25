---
title: 2-WooLocalization-Localization Data文件使用
author: yy
comment: 'on'
tags:
  - WooLocalization
category:
  - 文档
  - WooLocalization
date: 2024-10-26 20:59:43
---
# WooLocalization-Localization Data 是什么
在Unity中存储多语言数据，通常涉及到创建和管理多语言映射表，这些表用于建立不同资源之间的对应关系，确保一个key对应多个语言的资源。
以下是对这一过程的详细描述


# 创建方法
右键Project窗口 - Creat - Localization Data

# 界面功能介绍
* Clear Data
  * 一键清除所有数据
* Read From Asset
  * 从Assets文件夹中读取数据
* Read From CSV / Write to CSV 
  * 从本地路径中读写CSV文件
* LanType 
  * 添加所需要的语言
* Key
  * 用于在代码中引用对应文本的标识符
* VAL 
  * 用于在所需语言中显示的实际文本

* 右键data文件 可以删除所选 删除语言类型 一键翻译（下一篇会详细讲解）

![](../../../Pic/Doc/WooLocalization/LocalizationData文件.png)