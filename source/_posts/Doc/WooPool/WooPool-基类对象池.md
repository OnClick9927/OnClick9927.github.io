---
title: WooPool-基类对象池
author: 叶子三分青
comment: on

tags:
  - Pool 
  - 基类对象池
category:
  - 文档
  - WooPool
date: 2023-06-07 16:23:00
---

## 定义

设置一个基类，可以从这个对象池中拿出继承这个基类的对象

```csharp
public abstract class BaseTypePool<T> : PoolUnit
```

#### 补充说明

基类对象池的本质是一个Key为 类型，Value为 对应类型对象池 的字典。获取对象时，根据类型找到对应的对象池，并从对应的对象池中获取对象。



## 方法

注：下面的`Object`类型是泛型类型，为了区分基类的泛型`T`

| 方法名                                          | 可见性级别        | 说明                               |
| ----------------------------------------------- | ----------------- | ---------------------------------- |
| SetPool&lt;Object&gt;(ObjectPool&lt;Object&gt;) | public            | 将对象池放入基类对象池中           |
| SetPool(Type, IObjectPool)                      | public            | 将对应类型的对象池放入基类对象池中 |
| GetPool&lt;Object&gt;()                         | public            | 根据类型获取对象池                 |
| GetPool(Type)                                   | public            | 根据类型获取对象池                 |
| CreatePool(Type)                                | protected virtual | 创建对象池                         |
| Get&lt;Object&gt;(IPoolArgs)                    | public            | 从对象池中获取对应类型的对象       |
| Get(Type,IPoolArgs)                             | public            | 从对象池中获取对应类型的对象       |
| Set&lt;Object&gt;(Object,IPoolArgs)             | public            | 将对应类型的对象放回对象池         |
| Set(Type,T,IPoolArgs)                           | public            | 将对应类型的对象放回对象池         |
| GetPoolCount&lt;Object&gt;()                    | public            | 获取对应类型的对象池中对象的数量   |
| GetPoolCount(Type)                              | public            | 获取对应类型的对象池中对象的数量   |
| Dispose()                                       | public            | 销毁基类对象池                     |

