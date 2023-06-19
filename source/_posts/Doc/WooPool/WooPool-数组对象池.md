---
title: WooPool-数组对象池
author: 叶子三分青
comment: on

tags:
  - Pool
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



## 使用示例

```csharp
void ArrayPoolExample()
{
    //创建数组对象池，示例使用存放int值的数组
    var pool = new ArrayPool<int>();

    //从数组对象池中拿出一个长度为2的数组
    var objA = pool.Get(new ArrayPoolArg(2));
    Console.WriteLine("取出的数组长度为"+objA.Length);

    //将数组中的第一个元素设置成1
    objA[0] = 1;

    //将数组回收，再拿出一个长度为2的数组
    pool.Set(objA);
    objA = pool.Get(new ArrayPoolArg(2));

    //打印第一个元素
    Console.WriteLine("数组的第一个元素的值为："+ objA[0]);
    //因此需要注意放回数组时 根据自己需求判断是否需要重置一下数组
}
```

