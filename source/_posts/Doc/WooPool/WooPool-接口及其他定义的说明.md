---
title: WooPool-接口及其他定义的说明 
author: 叶子三分青
comment: on

tags:
  - Pool 
  - 接口介绍
category:
  - 文档
  - WooPool
date: 2023-06-09 09:43:20
---

## IObjectPool

定义了对象池的**对象数量**、**对象类型**和**回收方法**

```csharp
public interface IObjectPool:IDisposable
```

#### 属性

| 属性名 | 说明                               |
| ------ | ---------------------------------- |
| count  | 获取当前对象池里对象的个数         |
| type   | 获取当前对象池中对应对象的对象类型 |

#### 方法

| 方法名                       | 说明                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| Set(object,IPoolArgs)| 将对象回收，也就是将对象放回到对象池中<br />回收成功时返回`true`，失败时返回`false` |



## IPoolObject

定义了对象池对象的生命周期的三个阶段

```csharp
public interface IPoolObject : IDisposable
```

#### 方法

| 方法名       | 说明                         |
| ------------ | ---------------------------- |
| OnAllocate() | 在对象被创建时调用           |
| OnGet()      | 在对象从对象池中被取出时调用 |
| OnSet()      | 在对象被回收回对象池时调用   |



## IPoolArgs

此接口用于抽象构造的参数

```csharp
public interface IPoolArgs
```



## PoolUnit

定义对象池的销毁状态和销毁方法

```csharp
public abstract class PoolUnit : IDisposable
```

#### 属性

| 属性名   | 说明                 |
| -------- | -------------------- |
| disposed | 当前对象池是否被销毁 |

### 方法

| 方法名      | 说明                 |
| ----------- | -------------------- |
| OnDispose() | 在对象池被销毁时调用 |
| Dispose()   | 销毁对象池           |
