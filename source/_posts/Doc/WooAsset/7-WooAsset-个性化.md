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
public abstract class IAssetBuild
{
//是否记录这个路径
bool GetIsRecord(string path) ;
//自定义 资源 Tag （可以按照tag加载一组资源）
List<string> GetAssetTags(string path) ;
//自定义版本规则
string GetVersion(string settingVersion, AssetTaskContext context);

//更具一个路径返回资源类型（覆盖）
AssetType CoverAssetType(string path, AssetType type) ;

// 自定义资源分组
void Create(List<EditorAssetData> assets, List<EditorBundleData> result, EditorPackageData pkg);

//自定义加密
IAssetStreamEncrypt GetBundleEncrypt(EditorPackageData pkg, EditorBundleData data, IAssetStreamEncrypt en) ;
int GetEncryptCode(IAssetStreamEncrypt en);
IAssetStreamEncrypt GetEncryptByCode(int code)

}





```
## 加载个性化
``` csharp
///写一个 class 继承于 AssetsSetting
public abstract class AssetsSetting
{
  ///是否需要拷贝内置资源
  public virtual bool NeedCopyStreamBundles() {}
  ///一帧内最多多少毫秒在加载资源
  public virtual long GetLoadingMaxTimeSlice(){}

  ///下载的路径
  //举例 ：https://xxx.xxx.xx
  //http://127.0.0.1:8080
  // Application.StreamingPath 
  protected virtual string GetBaseUrl() {}
  ///自定义 bundle 去哪里下载
public virtual string GetUrlByBundleName(string buildTarget, string bundleName) => $"{GetBaseUrl()}/{buildTarget}/{bundleName}";
 public virtual string GetUrlByBundleName(string buildTarget, string version, string bundleName) => $"{GetBaseUrl()}/{buildTarget}/{version}/{bundleName}";

  /// 如果本地没有是否需要保存文件
  // GetBundleAlwaysFromWebRequest 返回 true 该选项不起效
  public virtual bool GetSaveBundlesWhenPlaying(){}
  //永远从远端下载
   public virtual bool GetBundleAlwaysFromWebRequest() {}

  /// 下载超时
  public virtual int GetWebRequestTimeout() {}
  ///下载重试次数
  public virtual int GetWebRequestRetryCount() {  }



//是否开启模糊加载
//Assets.loadAsset("Assets/a.png")
//开启后 Assets.loadAsset("Assets/a")
//注意 开启之后，同一个文件夹下不可以出现同名文件
   public virtual string GetFuzzySearch(){}
  ///是否自动卸载 AB
  //如果未开启，需要在合适的时机手动调用   Assets.UnloadBundles
  public virtual bool GetAutoUnloadBundle() {}


  ///资源加密方式
  public virtual IAssetStreamEncrypt GetEncrypt(int code) {}


  /// 自定义的资源生命周期管理，bundle和asset均可以
  public virtual IAssetLife GetAssetLife(){}

}
```
## 资源加密
``` csharp
//个性化加密

    public interface IAssetEncrypt
    {
        byte[] Encode(string bundleName, byte[] buffer);
        byte[] Decode(string bundleName, byte[] buffer);
        byte[] Decode(string bundleName, byte[] buffer, int offset, int length);


    }

```
