---
title: WooPool-基本定义
author: 叶子三分青
comment: on

tags:
  - Pool 
  - 基本定义
category:
  - 文档
  - WooPool
date: 2023-06-02 15:08:00
---

## 定义

对象池的基本定义，包括基本属性、基本方法、生命周期

```csharp
public abstract class ObjectPool<T> : PoolUnit, IDisposable, IObjectPool
```



##  属性

| 属性名 | 可见性级别 | 说明                               |
| ------ | ---------- | ---------------------------------- |
| count  | public     | 获取当前对象池里对象的个数         |
| type   | public     | 获取当前对象池中对应对象的对象类型 |
| pool   | protected  | 对象池的数据容器，默认是队列       |
| para   | protected  | 锁                                 |



## 关键方法

| 方法名                | 可见性级别         | 说明                                                      |
| --------------------- | ------------------ | --------------------------------------------------------- |
| Get(IPoolArgs)        | public virtual     | 从对象池中获取对象                                        |
| Set(object,IPoolArgs) | public             | 将对象回收到对象池中，回收成功时返回true，失败时返回false |
| Set(T,IPoolArgs)      | public virtual     | 将对象回收到对象池中，回收成功时返回true，失败时返回false |
| RealSet(T,IPoolArgs)  | protected          | 对象回收的实际方法，上面两个只是用于调用的                |
| Clear(IPoolArgs)      | public             | 清空对象池中的所有对象                                    |
| Clear(int,IPoolArgs)  | public             | 清除对象池里一定数量的对象                                |
| CreateNew(IPoolArgs ) | protected abstract | 创建对象的方法，需要子类实现                              |
| Dispose()             | public             | 销毁对象池（继承自 PoolUnit）                             |



## 生命周期

需要在对应周期增加操作时可以重写对应的方法

| 生命周期名             | 说明                                      |
| ---------------------- | ----------------------------------------- |
| OnCreate(T, IPoolArgs) | 对象池创建对象时调用                      |
| OnGet(T, IPoolArgs)    | 对象池中的对象被获取时调用                |
| OnSet(T, IPoolArgs)    | 对象被回收时调用，返回false时表示不能回收 |
| OnClear(T, IPoolArgs)  | 对象池中的对象被清除时调用                |
| OnDispose()            | 对象池被销毁时调用                        |

