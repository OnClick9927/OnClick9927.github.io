---
title: WooTween-TweenValue
author: yy
comment: 'on'
tags:
  - WooTween
category:
  - 文档
  - WooTween
date: 2023-06-23 02:07:46

---
# 开始
* 这是一个单程车，
* 所以他的功能就是在曲线转换器的作用下
* 从一个值跑到另一个值即可
* 那么他肯定有一个 开始跑（run）和update
* run 很简单
* update ，在那里运行呢？
* 还记得我们的好司机吗？
* 对了，就是他带着我们的 TweenValue 跑
* 我们需要具有曲线的运行跑完这个单程
* 所以我们TweenValue得有一个 转换器
* 我们变化的值，也不可能是一种
* 有可能是 float、int、Vector3等等
* 所以 TweenValue 是一个泛型

## 呼之欲出的结果
``` csharp
///环境
public abstract class TweenValue : TweenObject
{
    ///提供统一的获取接口
    public static TweenValue<T> Get<T>(EnvironmentType envType) where T : struct {}

    private IPercentConverter _converter = EaseCoverter.Default;
    private float _time;
    public abstract float duration { get; }
    public static float deltaTime = 0.02f;
    protected abstract void MoveNext();
    public void Run()
    {
        TweenDrive container = TweenDrive.GetDrive(envType);
        container.Subscribe(this);
    }
    public void Update()
    {
        _time += deltaTime ;

        if (_time >= duration)
        {
            OnCompelete();
        }
        else
        {
            MoveNext();
        }
    }
    protected virtual void OnCompelete(){}

}

public abstract class TweenValue<T> : TweenValue where T : struct
{
    ///对Tween传过来的设置打个包
    private IPlugin<T> _plugin;
    private T _current;

    protected T pluginValue { get { return _plugin.getter.Invoke(); } }
    ///设置当前的值
    protected void SetCurrent(T value)
    {
        if (_plugin != null)
        {
            if (_plugin.snap)
                _current = Snap(value);
            else
                _current = value;
            if (_plugin.setter != null)
            {
                _plugin.setter(_current);
            }
        }
    }
    public T end { get { return _plugin.end; } }
    public T start { get { return _plugin.start; } }
    public override float duration { get { return _plugin != null ? _plugin.duration : 0; } }
}
```
## 结果
基础的结构已经有了，接下来就是各种类型（float，int，Vector3）各自实现即可