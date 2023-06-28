---
title: WooAsset-加载与卸载
author: 灵
tags:
  - WooAsset
category:
  - 文档
  - WooAsset
date: 2023-06-27 15:58:38
---

``` csharp
///正常加载
var asset = await Assets.LoadAssetAsync(path);
///获取要加载的资源
var sp = asset.GetAsset<Sprite>();
///加载场景
var sceneAsset = await Assets.LoadSceneAssetAsync(path);
await sceneAsset.LoadSceneAsync(LoadSceneMode.Additive);
///加载Unity无法识别的资源
var asset = await Assets.LoadAssetAsync(path);
RawObject raw = asset.GetAsset<RawObject>();
Debug.Log(raw.bytes.Length);

///加载子资源
var mainAsset = await Assets.LoadAssetAsync(path);
var sp = mainAsset.GetSubAsset<Sprite>("a_1");

///卸载资源
Assets.Release(asset)
```
