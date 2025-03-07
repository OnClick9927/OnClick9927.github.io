---
title: WooAsset-一些工具
author: 灵
tags:
  - WooAsset
category:
  - 文档
  - WooAsset
date: 2023-06-27 15:58:38
---
# 收集资源预览（Option/Tools页签）
![Alt text](../../../Pic/Doc/WooAsset/option_tool.png)
## AssetMode
* Normal  正式的模式
* Rude    粗鲁的，纯粹的编辑器加载，不需要任何配置
* AssetDatabase  与 Normal 差不多，但是 不需要打包，是编辑器模拟
## Simulator
* EditorSimulatorPath  (编辑器状态下，Bundle存储的位置)
* ServerDirectory  (打包后，资源所在目录)（上传到CDN拷贝词目录即可）
* Enable Server （是否开启本地资源模拟服务）
* Server Port  （模拟服务绑定的端口）

## Shader Variant 防止 着色器丢失 
* Shader Variant Input Directory （需要收集SHader 变体文件夹）
* Shader Variant output Directory （shader 变体输出文件夹）
* （如何执行）点击Tools/WooAsset/Help/ShaderVariant
## Sprite Atlas  打图集 
* 设置好图集相关设置
* 点击Tools/WooAsset/Help/SpriteAtlas
## AssetTags 资源有哪些标签
## Record Ignore 不需要记录到资源清单的资源 配置


## 收集资源预览（Assets页签）
![Alt text](../../../Pic/Doc/WooAsset/资源收集情况.png)
* 如果不是打包，会收集所有的buildGroup
* 可以方便的看到究竟打包了什么内容
* 双击资源可以定位
* 支持快速搜索
* 可以查看资源依赖情况
## Asset Tags
* 给相应的资源加标记的记录
* 具体操作（在Assets页签，选中资源-》右键）
* 需要先在窗口  （Option/Tools页签/AssetTags） 中 预设tag
## Record Ignore
* 规定一些资源不记录到资源清单内
* 属于优化选项，可以减小资源清单文件的大小
  * 哪些资源适合？满足以下条件
  * 1、未在AssetReference中使用
  * 2、不需要用代码指定路径加载的资源->即其他资源依赖的资源，属于运行时被动加载的
  * 例子
    * 一个Scene 内有 N 个预制体
    * 只需要标记场景文件，场景依赖的其他都是可以忽略的（包括预制体、材质、图片、shader、fbx 等）
    * 这样一个场景就可以节约很大一部分的清单文件的占用
* 具体操作（在Assets页签，选中资源-》右键）




## 构建 Bundle 预览（Bundles页签）
![Alt text](../../../Pic/Doc/WooAsset/打包预览.png)
* 如果不是打包，会收集所有的buildGroup
* 可以预览所有的assetBundle
* 可以查看每一个资源的依赖
* 可以查看每一个bundle的依赖
* 双击资源可以定位
* 支持快速搜索

## 资源加载情况（AssetsLife页签）
![Alt text](../../../Pic/Doc/WooAsset/资源加载情况.png)
* 资源加载实践
* 当前有多少引用
* 资源的标签
* 资源所属的 bundle
* 支持快速搜索
