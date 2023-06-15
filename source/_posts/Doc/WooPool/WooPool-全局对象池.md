---
title: WooPool-全局对象池
author: 叶子三分青
comment: on

tags:
  - Pool 
  - 数组对象池
category:
  - 文档
  - WooPool
date: 2023-06-08 16:23:00


---

## 定义

全局的对象池，不用考虑创建对象池，不用考虑对象的类型，即拿即用，用完归还。

* 全局对象池操作使用PoolEx类
* ***注意回收时要自己初始化数据！！！***

```csharp
public static class PoolEx
```



## 方法

| 方法名                 | 可见性级别 | 说明                             |
| ---------------------- | ---------- | -------------------------------- |
| GetGlbalPoolCount&lt;T&gt;() | public     | 从对象池中获取对应类型的数组对象 |
| SetGlbalPool&lt;T&gt;(ObjectPool&lt;T&gt;) | public     | 将数组对象放回对象池             |
| GlobalAllocate&lt;T&gt;(IPoolArgs)         | public     | 全局分配对应类型的对象           |
| GlobalAllocate(Type,IPoolArgs) | public | 全局分配对应类型的对象 |
| GlobalRecyle&lt;T&gt;(T,IPoolArgs) | public | 全局回收 |
| GlobalAllocateArray&lt;T&gt;(int) | public | 获取对应类型和长度的数组对象 |



## 使用例子

```csharp
//全局对象池示例
void GlobalPoolExample() 
{
    //全局获取一个对象
    var human = PoolEx.GlobalAllocate<Human>();
    //全局回收一个对象
    PoolEx.GlobalRecyle(human);

    //从全局对象池中获取一个长度为10的数组对象
    var arr = PoolEx.GlobalAllocateArray<Human>(10);
    //Human类的定义在第一个示例
    arr[0] = new Human(33, "吉良吉影");
    arr[0].Say();
    //回收掉这个数组对象
    PoolEx.GlobalRecyle(arr);
    //重新从全局对象池中获取长度为10的数组对象
    arr = PoolEx.GlobalAllocateArray<Human>(10);
    arr[0].Say();
    PoolEx.GlobalRecyle(arr);
    //可以发现是和原先同样的对象
    //因此回收的时候需要注意初始化数据！！！！
}

//示例所需的类
public class Human
{
    public readonly int age;
    public readonly string name;

    public Human(int age, string name)
    {
        this.age = age;
        this.name = name;
    }
    public Human() 
    {
        age= 0;
        name = "无名";
    }

    public void Say()
    {
        Console.WriteLine($"我的名字叫{name},{age}岁");
    }
}
```

