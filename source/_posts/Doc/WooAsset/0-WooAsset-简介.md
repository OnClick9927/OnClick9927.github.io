---
title: WooAsset-简介
author: 灵
comment: 'on'
tags:
  - WooAsset
category:
  - 文档
  - WooAsset
date: 2023-05-19 13:37:15
---

# [地址](https://github.com/OnClick9927/WooAsset)
# 介绍
WooAsset可以满足以下任何需求：

* 我想发布一个不包含任何游戏资源的安装包，然后玩家边玩边下载。
* 我想发布一个可以保证前期体验的安装包，然后玩家自己选择下载关卡内容。
* 我想发布一个保证300MB以下内容的安装包，然后进入游戏之前把剩余内容下载完毕。
* 我想发布一个偏单机的游戏安装包，在网络畅通的时候，支持正常更新。在没有网络的时候，支持游玩老版本。
* 我想发布一个MOD游戏安装包，玩家可以把自己制作的MOD内容上传到服务器，其它玩家可以下载游玩。
* 我们在制作一个超大体量的项目，有上百GB的资源内容，每次构建都花费大量时间，是否可以分工程构建？
## 系统特点

* Editor、Runtime均支持同步、异步、委托、携程
* 支持各个模式切换（无需修改代码）
* 支持自定义资源加密
* 支持分布式构建(支持mod)
* 支持 shader 变体收集
* 支持 构建 spriteAtlas
* 支持打包/加载Unity无法识别的文件
* 支持打包流程自定义（自定义分包、版本规划、结束流程、自定义标签）
* 包含本地资源服务器
* 支持快速版本切换，回退
* 支持资源模糊搜索
* 支持自定义下载器
* 支持子资源加载
* 支持资源懒卸载/内存大小控制
* 支持WebGL
* 支持加载Resources文件夹内容

## 支持的模式
各个模式切换无需修改代码
 * 0 编辑器模拟加载         (纯粹编辑器模拟)
 * 1 纯粹的C#空包，   （注意：本地不会有任何ab）
 * 2 发布 正常流程包       （比模式1 多一个版本检查）
 * 3 发布 游戏前期的体验包  (把资源拷贝到stream)
 * 4 边玩边下载
 * 5 先准备一部分，后续靠下载
### 模式区别
<table>
<tr><th></th><th>模式/流程</th><th>编辑器模拟</th><th>纯C#空包</th><th>正常流程</th><th>前期的体验</th><th>边玩边下载</th><th>先准备一部分</th></tr>
<tr ><td rowspan="5">打包</td></tr>
<tr><th>选择FastMode        </th><th>✔</th><th> </th><th> </th><th> </th><th> </th><th> </th></tr>
<tr><th>打包资源            </th><th>  </th><th>✔</th><th>✔</th><th>✔</th><th>✔</th><th>✔</th></tr>
<tr><th>拷贝资源到stream     </th><th>  </th><th> </th><th> </th><th>✔</th><th> </th><th>按tag拷贝</th></tr>
<tr><th>上传资源到服务器     </th><th>  </th><th>✔</th><th>✔</th><th> </th><th>✔</th><th>✔</th></tr>
<tr ><td rowspan="7">加载</td></tr>
<tr><th>设置AssetsSetting   </th><th>✔</th><th>✔</th><th>✔</th><th>✔</th><th>✔</th><th>✔</th></tr>
<tr><th>拷贝资源到沙盒路径   </th><th> </th><th> </th><th> </th><th>✔</th><th> </th><th>✔</th></tr>
<tr><th>版本检查            </th><th> </th><th> </th><th>✔</th><th> </th><th>可选</th><th>可选</th></tr>
<tr><th>下载变化资源        </th><th> </th><th> </th><th>✔</th><th> </th><th> </th><th>可选</th></tr>
<tr><th>初始化              </th><th>✔</th><th>✔</th><th>✔</th><th>✔</th><th>✔</th><th>✔</th></tr>
<tr><th>正常加载            </th><th>✔</th><th>✔</th><th>✔</th><th>✔</th><th>✔</th><th>✔</th></tr>
</table>


