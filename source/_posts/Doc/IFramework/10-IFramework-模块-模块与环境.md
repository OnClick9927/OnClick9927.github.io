---
title: 10-IFramework-模块-模块与环境
author: 灵
comment: 'on'
tags:
  - IFramework
category:
  - 文档
  - IFramework
date: 2023-07-24 14:11:38
---
# 总结
* 从文档  记录模块 到 自定义模块 ，我们获取任意一个模块的代码都是如下
* 为什么这么做：
  * 写例子方便，环境是对模块的包装
  * 少写一些重复代码
* 为什么要总结：
  * 模块可以拿出来单独使用
``` csharp
//获取模块容器
IModules moduleContainer= Framework.GetEnv(EnvironmentType.Ev0).modules;
//创建模块实例
moduleContainer.CreateModule<MyModule>();
```


# 单独使用模块
* 自定义一个脚本用于存储模块实例
* 提供外部可以访问的获取模块方法
* 下面是 自定义模块 的写法
``` csharp
Modules modules = new Modules();
modules.GetModule<MyModule>();
```
# 环境包含内容
* 对模块实例的持有
* 一个计时器
* 生命周期绑定
* 生命周期等待（处理多线程）



