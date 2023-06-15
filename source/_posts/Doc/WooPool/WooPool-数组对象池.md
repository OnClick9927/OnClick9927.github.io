---
title: WooPool-数组对象池
author: 叶子三分青
comment: on

tags:
  - Pool 
  - 数组对象池
category:
  - 文档
  - WooPool
date: 2023-06-06 16:23:00

---

## 定义

能拿出数组对象的对象池

```csharp
public class ArrayPool<T> : ObjectPool<T[]>
```



## 参数说明

参数ArrayPoolArg用于传入数组长度

```csharp
public struct ArrayPoolArg : IPoolArgs
```

#### 属性

| 属性名 | 说明     |
| ------ | -------- |
| length | 数组长度 |

#### 构造函数

``` csharp
public ArrayPoolArg(int length)
```

##### 参数

| 参数名 | 类型 | 说明                       |
| ------ | ---- | -------------------------- |
| length | int  | 用于参数构造时传入数组长度 |



## 方法

| 方法名             | 可见性级别 | 说明                             |
| ------------------ | ---------- | -------------------------------- |
| Get(IPoolArgs)     | public     | 从对象池中获取对应类型的数组对象 |
| Set(T[],IPoolArgs) | public     | 将数组对象放回对象池             |
| Dispose()          | public     | 销毁基类对象池                   |

