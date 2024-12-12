---
title: 6-IFramework-Unity-单例
author: 灵
comment: 'on'
tags:
  - IFramework-Unity
category:
  - 文档
  - IFramework-Unity
date: 2023-07-24 14:54:57
---
``` csharp
public class Test : IFramework.Singleton.Singleton<Test>
{
    internal void Dosth()
    {
       
    }
}
//场景内单例物体路径
[IFramework.Singleton.MonoSingletonPath("xx/xx")]
public class TestMono : IFramework.Singleton.MonoSingleton<TestMono>
{
    void Use()
    {
        Test.instance.Dosth();
        TestMono.instance.Dosth();

    }

    private void Dosth()
    {
        
    }
}
```