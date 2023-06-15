---
title: WooPool-Activator构建对象池
author: 叶子三分青
comment: on

tags:
  - Pool 
  - Activator构建对象池
category:
  - 文档
  - WooPool
date: 2023-06-05 09:30:00
---

## 定义

使用Activator自动构建对象的对象池，支持传入对象创建时所需的参数
```csharp
public class ActivatorCreatePool<T> : ObjectPool<T>
```



## 构造函数

```csharp
public ActivatorCreatePool(params object[] args)
```

#### 参数

| 参数名 | 类型     | 说明                     |
| ------ | -------- | ------------------------ |
| `args` | object[] | 用于对象构造时所需的参数 |



## 属性

| 属性名   | 说明                                                       |
| -------- | ---------------------------------------------------------- |
| count    | 获取当前对象池里对象的个数（继承自 ObjectPool&lt;T&gt;）         |
| type     | 获取当前对象池中对应对象的对象类型（继承自 ObjectPool&lt;T&gt;） |
| disposed | 当前对象池是否被销毁（继承自 PoolUnit）                    |



## 关键方法

| 方法名     | 说明                                               |
| ---------- | -------------------------------------------------- |
| Get()      | 从对象池中获取对象（继承自 ObjectPool&lt;T&gt;）         |
| Set(T)     | 将对象回收到对象池中（继承自 ObjectPool&lt;T&gt;）       |
| Clear()    | 清空对象池中的所有对象（继承自 ObjectPool&lt;T&gt;）     |
| Clear(int) | 清除对象池里一定数量的对象（继承自 ObjectPool&lt;T&gt;） |
| Dispose()  | 销毁对象池（继承自 PoolUnit）                      |



## 使用例子

```csharp
/// <summary>
/// 示例将会用到的示例类型，构造函数不含参数
/// </summary>
public class Bottle { }

/// <summary>
/// 示例将会用到的示例类型，构造函数包含age和name两个参数
/// </summary>
public class Human
{
    public readonly int age;
    public readonly string name;

    public Human(int age, string name)
    {
        this.age = age;
        this.name = name;
    }

    public void Say()
    {
		Console.WriteLine($"我的名字叫{name},{age}岁");
    }
}

/// <summary>
/// ActivatorPool的使用示例
/// </summary>
private void ActivatorPoolExample()
{
    //创建一个存放Bottle的对象池
    ActivatorCreatePool<Bottle> pool = new ActivatorCreatePool<Bottle>();
    
    //从对象池中获取一个对象
    var bottle = pool.Get();
    
    //用对象池的Set方法将对象放回对象池
    pool.Set(bottle);
    
	//创建一个能够根据参数创建对象的对象池
    //此处以Human类为例，传入age和name
    ActivatorCreatePool<Human> humanPool = new ActivatorCreatePool<Human>(33, "吉良吉影");
    var human = humanPool.Get();
    //调用对象的Say方法
    human.Say();
    //输出内容为 我的名字叫吉良吉影,33岁
}
```

