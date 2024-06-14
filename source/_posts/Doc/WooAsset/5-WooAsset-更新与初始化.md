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
var op = await Assets.LoadRemoteVersions();
//网络错误/AssetDatabaseMode/RudeMode 会=null
if (op.Versions != null)
{
  //选择版本
    var version = op.Versions.NewestVersion();
  //下载远端版本文件
    var down = await Assets.DownloadVersionData(version);
    //得到版本数据
    var versionData = down.GetVersion();
    //本地和远端比较，第二个参数表示，比较哪些 pkg
    var compare = await Assets.CompareVersion(versionData, versionData.GetAllPkgs());
//下载所有需要更新的资源
    for (int i = 0; i < compare.add.Count; i++)
        await Assets.DownLoadBundle(versionData.version, compare.add[i].bundleName);
    for (int i = 0; i < compare.change.Count; i++)
        await Assets.DownLoadBundle(versionData.version, compare.change[i].bundleName);
}
```
# 初始化
``` csharp
Assets.SetAssetsSetting(new LocalSetting());
//初始化
//可选参数 version 初始化哪一个版本，不传就是本地版本，本地没有就是远端最新版本
//可选参数 again 再一次初始化（使用场景，热更新界面也热更）
//可选参数 getPkgs 始化包选择
await Assets.InitAsync();

```
