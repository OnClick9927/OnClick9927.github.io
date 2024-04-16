---
title: WooAsset-运行时扩展功能
author: 灵
tags:
  - WooAsset
category:
  - 文档
  - WooAsset
date: 2023-06-27 15:58:38
---
## 资源模糊搜索
``` csharp
public class AssetsSearch
    {

///以下是交集
        public static IReadOnlyList<string> IntersectNameAndTag(string assetName, params string[] tags);
        public static IReadOnlyList<string> IntersectTag(params string[] tags);

        public static IReadOnlyList<string> IntersectTypeAndNameAndTag(AssetType type, string assetName, params string[] tags);
        public static IReadOnlyList<string> IntersectTypeAndTag(AssetType type, params string[] tags);

///以下是并集

        public static IReadOnlyList<string> Union(params string[] nameOrTags);
        public static IReadOnlyList<string> UnionTag(params string[] tags);
        public static IReadOnlyList<string> UnionName(params string[] names);
        public static IReadOnlyList<string> UnionNameAndTag(string assetName, params string[] tags);
        public static IReadOnlyList<string> UnionTypeAndNameAndTag(AssetType type, string assetName, params string[] tags);
        public static IReadOnlyList<string> UnionTypeAndTag(AssetType type, params string[] tags);

        public static IReadOnlyList<string> AssetPathByType(AssetType type);

    }
```
## 资源组加载
``` csharp
//准备一组资源
string[] groups ;
var assets= await Assets.PrepareAssets(groups)
///加载对应的资源方法一
string path;
var asset = assets.FindAsset(path)
///加载对应的资源方法二
var asset = Assets.LoadAssetAsync(path)

///把整组资源全都卸载了
assets.Release();

///配合资源模糊搜索一起使用
//使用场景，进入战斗场景之前把战斗需要的资源全加载
```
## 方便的资源卸载
### 基础方式
* 场景
* 一个ui界面上面有一个image
* 运行时候需要不停的替换image的sprite
* 界面关闭的时候需要把image的sprite卸载
``` csharp
///所有的设置图片都走这个方法
public static async void SetSprite(Image image, string path)
{
    var asset = await Assets.LoadAssetAsync(path);
    if (asset.isErr) return;
    image.sprite = asset.GetAsset<Sprite>();
    Assets.AddBridge(new GameObjectBridge(image.gameObject,asset));
}
///在合适的时候调用一次即可（比如：切换场景时候）
public static void ReleaseUselessBridges()
{
    Assets.ReleaseUselessBridges();
}
```
其他类型的资源/组件，可以 继承 AssetBridge< T > 自行实现即可

### 更加方便的方式（有风险）
``` csharp
public class LocalSetting : AssetsSetting
{
    public override IAssetLife GetAssetLife()
    {
      /// 参数是缓存的内存大小，超过这个数字会自动卸载最早的资源
        return new LRULife(1024 * 50);
    }
}
Assets.SetAssetsSetting(new LocalSetting());

```
* 可以实现 bundle 不自动卸载，到达一定大小在开始卸载
* 如果内存不足时候，最早被使用的资源会被优先卸载
* 内存设置的别太小，容易出现资源丢失
