---
title: WooTween-Tween
author: yy
comment: 'on'
tags:
  - WooTween
  - Tween
category:
  - 文档
  - WooTween
date: 2023-06-23 02:30:57

---
# 开始
* 我们需要实现开始、重新开始、结束，回去
* 所以必须有四个方法
* Run、ReStart、Rewind、Complete
* 因为要可以设置转换器，所以必须有一个引用
* 因为需要可以循环动画，所以必须要有一个方向、循环类型
## 呼之欲出的结果
``` csharp
///环境
public enum TweenDirection
{
    Forward,
    Back
}
public enum LoopType
{
    ReStart,
    PingPong
}
public abstract class Tween : TweenObject, ITween
{
    private TweenDirection _direction = TweenDirection.Forward;
    public LoopType loopType { get; set; }
    public abstract IPercentConverter converter { get; set; }
    public abstract void Run();
    public abstract void ReStart();
    public abstract void Rewind(float duration,bool snap=false);
    public abstract void Complete(bool invoke);
}
```
# 再次开始
* 动画的变化有可能是从一个值到另一个值
* 比如0-1
* 也有可能需要变化一组数值
* 比如dotween 经典的 dopath
* 所以我们有两种tween,SingleTween,ArrayTween
# 凉鞋专用
* 为什么要有 WaitToRun 这个方法
* 因为需要支持链式编程，所以tween都是等到下一帧才会真的开始跑
* 对于为什么要有 UnbindTweenValue 这个方法
* TweenValue 其实是一直跑的，直到他自己跑结束，会被驱动统一回收
* tween想要中断被当前 TweenValue 控制数值，只要把 TweenValue 的plugin制空即可
* 还想继续跑，那就重新分配一个即可
* 这个就是我之前困扰的bug

# 更方便的书写
* 众所周知，dotween最出名的就是链式编程
* 我们也不能少

``` csharp
public static ISingleTween<T> AllocateSingleTween<T>(EnvironmentType env)
{
    if (env != EnvironmentType.Editor)
        TweenSingleton.Initialized();
    return TweenObject.Allocate<SingleTween<T>>(env);
}
public static ITween<T> DoGoto<T>(T start, T end, float duration, Func<T> getter, Action<T> setter, bool snap, EnvironmentType env= EnvironmentType.RT)
{
#if UNITY_EDITOR
    if (!UnityEditor.EditorApplication.isPlaying && !Application.isPlaying)
    {
        env = EnvironmentType.Editor;
    }
#endif
    var tween = AllocateSingleTween<T>(env);
    tween.Config(start, end, duration, getter, setter, snap);
    (tween as Tween).WaitToRun();
    return tween;
}

public static ITween<Vector3> DoMove(this Transform target, Vector3 end, float duration, bool snap = false)
{
    return DoGoto(target.position, end, duration, () => { return target.position; },
            (value) => {
                target.position = value;
            }, snap
        );
}
```
