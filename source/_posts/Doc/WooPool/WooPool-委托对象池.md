---
title: WooPool-委托对象池
author: 叶子三分青
comment: on

tags:
  - Pool 
  - 委托对象池
category:
  - 文档
  - WooPool
date: 2023-06-04 16:00:00
---

## 定义

创建对象时会调用委托的对象池

```csharp
public abstract class DelegatePool<T> : ObjectPool<T>
```



## 构造函数

```csharp
public DelegatePool(Func<T> create)
```

#### 参数

| 参数名 | 类型          | 说明                       |
| ------ | ------------- | -------------------------- |
| create | Func&lt;T&gt; | 创建对象时所需要调用的委托 |