---
title: foreachGC问题验证
author: YueZhenpeng
comment: 'on'

tags:
  - C#
  - foreach
  - GC
category:
  - C#
  - 性能优化

date: 2023-05-19 07:57:04
---

# 一、Foreach究竟会不会产生GC？
很多读者在听一些群内大佬谈话过程中可能会听说**foreach遍历集合会产生GC**，笔者也是这么了解的，所以很多读者可能会和笔者一样在网上看到各种说法，将信将疑。
主要分为这几个立场：

> **1.foreach 会产生GC，在unity里别用，Mono的问题
> 2.foreach产生GC是被遍历的集合有问题，实现的不好，不是foreach的锅
> 3.foreach的GC问题已经修复了，大家可以毫不顾忌的使用**

笔者在搜索了资料的基础上自己亲手实验，试图证明这些结论哪个是正确的，得到的结论是

> 网上的其他回答太过远古，甚至存在莫名的歧视foreach
> 有时候会产生一点点GC，但无需否定，甚至在现在可以忽略不计

**<font color=red>如果不想看实验过程，可以直接翻到文末有结论！！！！</fontcolor>**


# 二、实验过程
首先我们以最常用的**Dictionary**进行讨论，因为我们经常使用**foreach便捷的遍历Dictionary**，难以用for进行
## 1. foreach遍历字典是否存在GC
```csharp
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Profiling;
public class MyGCTest : MonoBehaviour
{
    Dictionary<int,int> dic = new Dictionary<int, int>()
    {
        { 0, 0 },
        { 1, 1 }
    };
    void Update()
    {
        Profiler.BeginSample("ForeachGC");
        foreach (var x in dic){}
        Profiler.EndSample();
    }
}

```
![在这里插入图片描述](https://raw.githubusercontent.com/yueh0607/MyPicueres/main/202305192036507.png)
![在这里插入图片描述](https://raw.githubusercontent.com/yueh0607/MyPicueres/main/202305192037774.png)

**答案显然是存在的**，**但是笔者在不经意间发现，写在Update的foreach**，居然**仅仅在第一次调用时产生GC，以后的循环的foreach均不产生GC**！！

### 2. foreach遍历字典在什么时候产生GC Alloc
根据上一步，笔者产生了以下猜想

> **1.字典内增加一个元素，foreach是否会再次产生GC
> 2.如果我分别遍历多个字典，会不会产生双份的GC**
> 3.如果我遍历几个不同类型的字典呢？

根据以下代码验证，笔者在两个文件中分别监测GC的产生
```csharp
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Profiling;


public class MyGCTest : MonoBehaviour
{
	//先整两个不同的字典
    Dictionary<int, int> dic = new Dictionary<int, int>()
    {
        { 0, 0 }
    };
    Dictionary<int, int> dic2 = new Dictionary<int, int>()
    {
        { 0, 0 },
        { 100,100}
    };
    void Update()
    {
        Profiler.BeginSample("ForeachGC");
        //先遍历一次第一个字典试试
        foreach (var x in dic){}
        //新增一个元素再试试
        if (!dic.ContainsKey(1)) dic.Add(1, 0);
        foreach (var x in dic){}
        //遍历第二个字典
        foreach (var x in dic2) {}
        Profiler.EndSample();
        //此时发现第一帧有0B 的GCAlloc
    }
}
public class MyGCtest2 : MonoBehaviour
{
    Dictionary<int, int> dic2 = new Dictionary<int, int>()
    {
        { 0, 0 },
        { 100,100}
    };
    void Update()
    {
        Profiler.BeginSample("ForeachGC3");
        foreach (var x in dic2) {}
        Profiler.EndSample();
        //第一帧产生96B GCAlloc
    }
}
```
![在这里插入图片描述](https://raw.githubusercontent.com/yueh0607/MyPicueres/main/202305192037743.png)
![结果完全相同，这说明了foreach在同一个方法内，](https://raw.githubusercontent.com/yueh0607/MyPicueres/main/202305192037024.png)
这样我们就能得出

> **1.无论遍历几个字典，遍历几次，元素是否改变，都只产生96B的GCAlloc**
> **2.foreach遍历字典的GCAlloc 全局仅产生一次，与所在文件，方法，类都无关**

但是我们接下来想试试不同类型的字典..

```csharp
Dictionary<int, int> dic2 = new Dictionary<int, int>()
    {
        { 0, 0 },
        { 100,100}
    };

    Dictionary<int, float> dic1 = new Dictionary<int, float>()
    {
        { 0, 0.2f },
        { 100,100.0f}
    };
    void Update()
    {
        Profiler.BeginSample("ForeachGC");
        foreach(var x in dic2)
        {

        }
        foreach (var x in dic1)
        {

        }
        Profiler.EndSample();
    }
```
![在这里插入图片描述](https://raw.githubusercontent.com/yueh0607/MyPicueres/main/202305192037423.png)
GC突然变为192B ，是原来的二倍，显然每个类型的字典都会产生96B

> **1.无论遍历几个字典，遍历几次，元素是否改变，都只产生96B的GCAlloc**
> **2.foreach遍历字典的GCAlloc 全局仅产生一次，与所在文件，方法，类都无关**
> 3. **foreach遍历字典产生GC与字典类型有关**


### 3.foreach 遍历其他Collection呢？

```csharp
	List<int> list = new List<int>() { 0,1,0};
    int[] arr= new int[3] { 0,1,0};
    void Update()
    {
        Profiler.BeginSample("ForeachGC");
        foreach(var x in list)
        {

        }
        foreach(var x in arr) { }
        Profiler.EndSample();
    }
```

![在这里插入图片描述](https://raw.githubusercontent.com/yueh0607/MyPicueres/main/202305192037653.png)

甚至第一次GC都没产生，接下来我们仔细发掘一下原理。

# 三、foreach 为什么在遍历Dictionary时产生GC
foreach本质是对GetEnumerator(),MoveNext()等方法的简化，我们对IEnumerable等接口再熟悉不过了。
> **1.无论遍历几个字典，遍历几次，元素是否改变，都只产生96B的GCAlloc**
> **2.foreach遍历字典的GCAlloc 全局仅产生一次，与所在文件，方法，类都无关**
> 3. **foreach遍历字典产生GC与字典类型有关**

产生这些结论，得出foreach的CG产出和字典类型相关，而与其他的因素无关的结论。我能猜测出GetEnumerator始终返回的是Enumerator的单例，每个字典类型都包含一个实例，所以形成每个字典类型都产生一定GC的现象。

![在这里插入图片描述](https://raw.githubusercontent.com/yueh0607/MyPicueres/main/202305192037701.png)
![在这里插入图片描述](https://raw.githubusercontent.com/yueh0607/MyPicueres/main/202305192037173.png)
我们详细展开分析，发现在GetEnumerator处产生96B，在MoveNext处产生96B
甚至笔者为了探究这一内容，写了第三个Dictionary，发现GetEnumerator处产生144B，在MoveNext处产生144B
笔者得到了以下结论

> **1.每个类型Dictionary<T,K>首次foreach均产生96B 的GCAlloc
> 2.每个96B的GCAlloc分别为 48B的GetEnumerator()和 48B的MoveNext()**
> **3.Dictionary的迭代方式类似于单例，每个类型全局仅加载一次**


# 四、结论

 1. **<font color=red>foreach在遍历System.Collections.Generic内的集合时不会无理由产生不可接受的GC**
 事实上，遍历List和数组时不会创建Enumerator，即一直保持0GC
 2. **<font color=red>foreach在遍历字典时，仅对每个类型字典在首次调用时产生一次GC,以后同类型字典不会再产生GC，与其他因素无关。**
也就是说，你只需要对Dictionary<int,int>使用过foreach，以后再使用同类型的字典foreach就不会产生GC，无论是否为同一实例，元素是否变化，文件是否相同，方法和类是否相同。
3.**<font color=red>对字典Values/Keys单独foreach将产生更多的GC，大概多24B,与上面提到的相近，其他一致。**
