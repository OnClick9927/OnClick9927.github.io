---
title: 6-IFramework-模块-协程模块
author: 灵
comment: 'on'
tags:
  - IFramework
category:
  - 文档
  - IFramework
date: 2023-07-24 14:11:38
---
# 协程
更好地利用线程资源

模块特点:
* 支持多种等待时间延迟执行
* 支持多种等待判断
* 支持快速结束某个协程
* 支持异步等待

使用示例:
``` csharp
//tip:环境的Update需要定时执行

//获取协程模块
coroutineModule = Framework.GetEnv(EnvironmentType.Ev0).modules.Coroutine;

//开启协程WaitExample
coroutineModule.StartCoroutine(WaitExample());

IEnumerator WaitExample()
{
    Log.L("协程WaitExample开始");

    yield return new WaitForFrame();
    Log.L("一帧过去了");

    yield return new WaitForFrames(20);
    Log.L("20帧过去了");

    yield return new WaitForHours(0.0004);
    Log.L("0.0004小时过去了");
}

```