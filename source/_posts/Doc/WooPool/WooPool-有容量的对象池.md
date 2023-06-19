---
title: WooPool-有容量的对象池
author: 叶子三分青
comment: on

tags:
  - Pool
category:
  - 文档
  - WooPool
date: 2023-06-03 16:00:00
---

## 定义

限制容量大小的对象池

```csharp
public abstract class CapacityPool<T>: ObjectPool<T>
```



## 构造函数

```csharp
protected CapacityPool(int capacity) : base() { this._capacity = capacity; }
```

#### 参数

| 参数名   | 类型 | 说明             |
| -------- | ---- | ---------------- |
| capacity | int  | 对象池的最大容量 |



## 属性

| 属性名 |   说明                      |
| ------ | -------------------------- |
| capacity | 对象池的最大容量，创建后可重新修改 |



## 使用示例

```csharp
//用于示例的类型
public class Obj_A { }

//定义一个存放Obj_A对象的对象池
public class CapacityPoolA : CapacityPool<Obj_A>
{
    public CapacityPoolA(int capacity) : base(capacity)
    {

    }

    protected override Obj_A CreateNew(IPoolArgs arg)
    {
        return new Obj_A();
    }
}

//限制大小对象池的示例
void CapacityPoolExample()
{
    //创建对象池，设置大小为1
    var pool = new CapacityPoolA(1);

    //放入一个对象，不会报错
    Console.WriteLine("回收第一个对象，结果为：" + pool.Set(new Obj_A()));

    //再放入一个对象，会发现存放失败
    Console.WriteLine("回收第二个对象，结果为：" + pool.Set(new Obj_A()));
}
```

