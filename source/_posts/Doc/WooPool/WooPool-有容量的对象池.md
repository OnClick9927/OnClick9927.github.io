---
title: WooPool-有容量的对象池
author: 叶子三分青
comment: on

tags:
  - Pool 
  - 有容量的对象池
category:
  - 文档
  - WooPool
date: 2023-06-03 16:00:00
---

## 定义

限制容量大小的对象池

```csharp
public abstract class CapicityPool<T>: ObjectPool<T>
```



## 构造函数

```csharp
protected CapicityPool(int capacity) : base() { this._capacity = capacity; }
```

#### 参数

| 参数名   | 类型 | 说明             |
| -------- | ---- | ---------------- |
| capacity | int  | 对象池的最大容量 |



## 属性

| 属性名 |   说明                      |
| ------ | -------------------------- |
| capacity | 对象池的最大容量，创建后可重新修改 |
