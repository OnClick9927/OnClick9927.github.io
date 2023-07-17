---
title: 1-WooBind-案例
author: yy
comment: 'on'
tags:
  - WooBind
category:
  - 文档
  - WooBind
date: 2023-07-05 00:53:57
---

# ObservableObject

## Subscribe
* Subscribe函数用于注册数值变化的监听函数。当属性发生变化时，会触发已注册的监听函数。具体使用方式如下：
```csharp
public void Subscribe(string propertyName, Action listener)
``` 
  * propertyName：要监听的属性名称。
  * listener：监听函数，当属性变化时被调用。
```csharp
Subscribe("Name", () =>
{
    Console.WriteLine("属性Name发生了变化！");
});
```

## UnSubscribe
* UnSubscribe函数用于取消注册数值变化的监听函数。如果之前通过Subscribe函数注册了一个或多个监听函数，但后来不再需要监听，则可以通过UnSubscribe函数进行取消注册。具体使用方式如下：
```csharp
public void UnSubscribe(string propertyName, Action listener)
```
* propertyName：要取消监听的属性名称。
* listener：要取消的监听函数。

```csharp 
UnSubscribe("Name", () =>
{
    Console.WriteLine("属性Name发生了变化！");
});
```