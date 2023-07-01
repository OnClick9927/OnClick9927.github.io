---
title: WooTween-驱动器
author: yy
comment: 'on'
tags:
  - WooTween
category:
  - 文档
  - WooTween
date: 2023-06-23 02:05:04

---
## 开始
* 驱动器需要逐帧运行，所以需要update
* 因为需要分为编辑器和运行时，所以要有一个环境的概念
* 但是驱动总得有一个指挥他运行的东西
  * 运行时：单例
  * 编辑器：绑定到编辑器的update
### 呼之欲出的结果
``` csharp
///环境
public enum EnvironmentType
{
    RT,
    Editor
}
public class TweenDrive : System.IDisposable
{
    //各自的指挥，调用这个方法即可
    public void Update()
    {
    }
    private static Dictionary<EnvironmentType, TweenDrive> dic = new Dictionary<EnvironmentType, TweenDrive>();

    public static TweenDrive GetDrive(EnvironmentType env)
    {
        if (!dic.ContainsKey(env))
        {
            dic.Add(env, new TweenDrive());
        }
        return dic[env];
    }
}
```