---
title: WooTween-对象池
author: yy
comment: 'on'
tags:
  - WooTween
  - 对象池
category:
  - 文档
  - WooTween
date: 2023-06-23 02:01:45

---
## 如何实现一个对象池
* 有最基本的存取能力
* 最好是可以通用
* 有一定的能力
### 基础的存取能力
* 存 Set
* 取 Get ,如果不够 CreateNew
* 内部需要维护一个可以保存对象的集合
### 通用/扩展能力
* 使用泛型
* 方法可以重写
### 结果
``` csharp
public abstract ObjectPool<T>
{
    protected Queue<T> pool ;
    public T Get(){
        T t;
            if (pool.Count > 0)
                t = pool.Dequeue();
            else
            {
                t = CreateNew();
                OnCreate(t);
            }
            OnGet(t);
            return t;
    }
    protected abstract T CreateNew();
    protected virtual void OnGet(T t) { }
    ///在被创建时候个性化的设置
    protected virtual void OnCreate(T t) { }
    public virtual bool Set(T t)
    {
        if (!pool.Contains(t))
        {
            if (OnSet(t))
            pool.Enqueue(t);
            return true;
        }
        else
        return false;
    }
    ///为了实现可以控制数量的对象池
    protected virtual bool OnSet(T t)
    {
        return true;
    }
}
```
## 更加容易使用的对象池
### 分析
* 上边的对象池已经可以实现对象的存取
* 但是每一个类型都得写一个子类
* C#可以反射创建对象，但是需要类型
### 结果
``` csharp
public abstract ActivatorCreatePool<T>: ObjectPool<T>
{
    ///反射创建对象
    protected abstract T CreateNew()
    {
        Type type = typeof(T);
        return (T)Activator.CreateInstance(type);
    }
}
```
## 基类对象池
### 分析
* 现在我们可以各种类型都使用同一个对象池
* 但是我们获取对象的时候，我们需要拿到具体的对象池
* 如果我们可以问一个对象池要各种对象该有多好
    * 例子：
    * 假设我们想看动物
    * 看鲨鱼-》海洋馆
    * 看熊猫-》熊猫馆
    * 就像上面，我们需要知道具体的展馆在哪里才行
    * 但是大部分情况，她们都是在动物园里面的
    * 动物园的路边是有指路牌的
    * 因此我们的对象池也可以以做成动物园这种
* 由此而言，我们有一个基类的概念，不能去动物园看飞机、坦克
* 我们需要一个对象池集合、并且需要指路牌
* 指路牌很简单、Type即可
* 所以用一个字典 Dictionary<Type,Pool?>
* 但是我们的对象池是泛型，后面这个类型咋写？
* 所以我们需要把之前额对象池再次抽象一次
### 结果
``` csharp
///更抽象
public interface IObjectPool:IDisposable {
    /// 回收，解决不能使用泛型问题
    void Set(object obj);
}
public abstract class BaseTypePool<T>
{

    private Dictionary<Type, IObjectPool> _poolMap;
    /// 获取内部对象池
    public ObjectPool<Object> GetPool<Object>() where Object : T
    {
        Type type = typeof(Object);
        var pool = GetPool(type);
        return pool as ObjectPool<Object>;
    }

    /// 获取内部对象池
    public IObjectPool GetPool(Type type)
    {
        IObjectPool pool;
        if (!_poolMap.TryGetValue(type, out pool))
        {
            var pooType = typeof(ActivatorCreatePool<>).MakeGenericType(type);
            pool = Activator.CreateInstance(pooType, null) as IObjectPool;
            _poolMap.Add(type, pool);
        }
        return pool;
    }


    /// 获取数据
    public Object Get<Object>() where Object : T
    {
        var pool = GetPool<Object>();
        Object t = pool.Get();
        return t;
    }

    /// 回收数据
    public void Set<Object>(Object t) where Object : T
    {
        Type type = t.GetType();
        var pool = GetPool(type);
        pool.Set(t);
    }

}

```
## 全局扩展
### 分析
* 现在我们已经实现了只要去动物园就可以观赏各种动物
* 很爽，但是呢我们必须知道动物园在哪里
* 还有就是，我们可能还想看飞机、坦克，并不是动物园
  * 关于飞机坦克，还有动物，
  * 我们可以抽象一点，他们都是一个东西
  * 所以我们可以搞一个东西池子
  * 也就是说，我们池子的 T 是 object
  * 关于知道动物园在哪里
  * 反正他是个东西，问东西池子要即可
  * 所以我们实现一个全局的池子就好了
  * 如何全局？C#正是个好语言-》静态扩展

### 结果
``` csharp
public static class PoolEx
{

    private class GlobalPool : BaseTypePool<object> { }
    static private GlobalPool _globalPool = new GlobalPool();
    /// 全局分配
    public static T GlobalAllocate<T>()where T: class
    {
        return _globalPool.Get<T>();
    }
    /// 全局回收
    public static void GlobalRecyle<T>(this T t)where T :class
    {
        _globalPool.Set(t);
    }
}
```

### 总结一下
目前对象池，可以获取任何对象了 
