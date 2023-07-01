---
title: WooTween-转换器
author: yy
comment: 'on'
tags:
  - WooTween
category:
  - 文档
  - WooTween
date: 2023-06-23 02:06:02

---
# 开始
## 这是个什么东西
在dotween 里面有一个Ease的概念

我们要实现的就是这个

那么这个东西很简单

只需要实现从一个值到另一个值的映射即可

## 呼之欲出的结果
``` csharp
///环境
public interface IPercentConverter
{
    float Convert(float percent, float time, float duration);
    void Recyle();
}
```