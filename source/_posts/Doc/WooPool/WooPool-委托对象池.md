---
title: WooPool-委托对象池
author: 叶子三分青
comment: on

tags:
  - Pool
category:
  - 文档
  - WooPool
date: 2023-06-04 16:00:00
---

## 定义

使用委托创建对象的对象池，用于在CreateNew()中调用,方便外部动态修改创建对象的方法

```csharp
public abstract class DelegatePool<T> : ObjectPool<T>
```



## 构造函数

```csharp
public DelegatePool(Func<T> create)
```

#### 参数

| 参数名 | 类型          | 说明           |
| ------ | ------------- | -------------- |
| create | Func&lt;T&gt; | 创建对象的委托 |



## 使用示例

```csharp
//用于示例的类型
public class Obj_A { }

//定义一个存放Obj_A对象的委托对象池
public class DelegatePoolA : DelegatePool<Obj_A>
{
    public DelegatePoolA(Func<Obj_A> create) : base(create)
    {
    }
}

//创建Obj_A对象的方法
Obj_A CreateObject()
{
    Console.WriteLine("我创建了一个对象，哈哈哈哈哈哈！");
    return new Obj_A();
}

void DelegatePoolExample()
{
    //创建委托对象池
    var pool = new DelegatePoolA(CreateObject);

    //获取一个对象，内部创建新对象时会调用方法
    var objA = pool.Get();
}
```

