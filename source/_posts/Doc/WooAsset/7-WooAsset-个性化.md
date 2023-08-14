---
title: WooAsset-个性化
author: 灵
tags:
  - WooAsset
category:
  - 文档
  - WooAsset
date: 2023-06-27 15:58:38
---
## 打包个性化
``` csharp
 //个性化打包流程
public interface IAssetBuild
{
    //自定义版本规则
    string GetVersion(string settingVersion, AssetTaskContext context);
    //自定义 资源 Tag （可以按照tag加载一组资源）
    IReadOnlyList<string> GetTags(EditorAssetData info);
    // 自定义资源分组
    void Create(AssetTagCollection tags,List<EditorAssetData> assets, List<BundleGroup> result);
    //自定义打包结束之后需要执行的任务
    //比如输出一些Log、上传文件到服务器等
    List<AssetTask> GetPipelineFinishTasks(AssetTaskContext context);
    //更具一个路径返回资源类型
    AssetType GetAssetType(string path);
    //是否忽略这个路径
    bool IsIgnorePath(string path);
}
```
## 加载个性化
``` csharp
///写一个 class 继承于 AssetsSetting
public abstract class AssetsSetting
{
  ///自定义Bundle下载器
  public virtual BundleDownloader GetBundleDownloader(string url,string bundleName) {}
  ///下载的路径
  protected virtual string GetBaseUrl() {}
  ///自定义 bundle 去哪里下载
  public virtual string GetUrlByBundleName(string buildTarget, string bundleName){}
  ///自定义 版本文件 去哪里下载
  public virtual string GetVersionUrl(string buildTarget){}
  /// 文件比对方式
  public virtual FileCompareType GetFileCheckType() {}
  /// 下载等待
  public virtual int GetWebRequestTimeout() {}
  ///资源加密方式
  public virtual IAssetStreamEncrypt GetEncrypt() {}
  ///是否自动卸载
  public virtual bool GetAutoUnloadBundle() {}
  /// 如果本地没有是否需要保存文件
  public virtual bool GetSaveBundlesWhenPlaying(){}
  /// 自定义的资源生命周期管理，bundle和asset均可以
  public virtual IAssetLife GetAssetLife(){}
  ///一帧内最多多少毫秒在加载资源
  public virtual long GetLoadingMaxTimeSlice(){}
}
```
## 资源加密
``` csharp
//个性化加密
public interface IAssetStreamEncrypt
{
    byte[] Encode(string bundleName, byte[] buffer);
    byte[] Decode(string bundleName, byte[] buffer);
}
```
