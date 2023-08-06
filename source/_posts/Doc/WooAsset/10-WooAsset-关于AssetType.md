---
title: WooAsset-关于AssetType
author: 灵
tags:
  - WooAsset
category:
  - 文档
  - WooAsset
date: 2023-06-27 15:59:38
---

``` csharp
public enum AssetType
{
    None,
    Meta,
    CS,
    Directory,
    Texture,
    Shader,
    ShaderVariant,
    VideoClip,
    AudioClip,
    Scene,
    Material,
    Prefab,
    Font,
    Animation,
    AnimationClip,
    AnimatorController,
    SpriteAtlas,
    ScriptObject,
    Model,
    TextAsset,
    Raw,
    RawObject,
    RawCopyFile,
}
```
# 简述AssetType
AssetType 本质上就是这个资源是什么类型

其中 不会打入Bundle的有
* Meta
* CS
* Directory
* SpriteAtlas
* RawObject
* RawCopyFile

众所周知的 Meta/CS/Directory就不说了

SpriteAtlas 不打进去的原因是：防止冗余

RawObject/RawCopyFile 本身不会进入Bundle，代替他们进去的是 Raw

## RawObject的用处
* unity无法识别的文件(比如 .lua)
* 需要特殊处理的文件（比如 .dll）
* 可以直接拿到 RawObject.bytes 属性 进行加载



## RawCopyFile的用处
* unity无法识别的文件(比如 .lua)
* 需要特殊处理的文件（比如 .dll）
* 可以直接拿到 RawObject.bytes 属性 进行加载

* 一些插件不支持 直接从 bytes 加载，只能使用路径



# 打包与加载
## 打包
如何 设置 资源的类型？

重写 IAssetBuild.GetAssetType

## 加载
如何确保本地有 只能用路径加载的资源？

在 Assets.InitAsync() 之后 执行 Assets.UnzipRawFile()即可

如何知道运行时候 RawCopyFile 实际的文件路径？

调用 Assets.GetRawFileToDlcPath(string srcPath) 即可获取到目标路径



