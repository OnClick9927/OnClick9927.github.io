---
title: WooAsset-更新与初始化
author: 灵
tags:
  - WooAsset
category:
  - 文档
  - WooAsset
date: 2023-06-27 15:58:38
---
# 更新
``` csharp
Assets.SetAssetsSetting(new LocalSetting());
//拉取远端版本信息
var op = await Assets.VersionCheck();
//远端的所有版本数据，内容是buildGroup
var versions = op.versions;
//远端和本地进行版本比较
//可选参数 versionIndex 和远端的第几个版本比较，不传入就是最后一个版本
//可选参数 tags 只比较包含标签的组，不传入就是所有组
var compare = await op.Compare(op.versions.Count);
//下载所有需要更新的资源
for (int i = 0; i < compare.add.Count; i++)
    await Assets.DownLoadBundle(compare.add[i].name);
for (int i = 0; i < compare.change.Count; i++)
    await Assets.DownLoadBundle(compare.change[i].name);
```
# 初始化
``` csharp
Assets.SetAssetsSetting(new LocalSetting());
//初始化
//可选参数 version 初始化哪一个版本，不传就是本地版本，本地没有就是远端最新版本
//可选参数 again 再一次初始化（使用场景，热更新界面也热更）
//可选参数 tags 只初始化包含标签的组，不传入就是所有组
await Assets.InitAsync();
```
