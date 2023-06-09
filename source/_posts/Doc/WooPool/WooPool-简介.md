---
title: WooPool-简介
author: 叶子三分青
comment: on

tags:
  - Pool
category:
  - 文档
  - WooPool
date: 2023-06-01 15:00:00
---
## 介绍
#### 为什么要使用对象池？

对象在生成和销毁时都是需要一定开销的，如果某个类型频繁地生成对象和销毁对象，会导致开销过大，影响性能。

举个例子：

1. 我们厨房里的碗架（对象池），容纳着很多个碗（对象）。

2. 我们在盛饭的时候，从碗架里拿出一个碗（从对象池中拿出一个对象）。

3. 在吃完饭之后，洗碗（对象的初始化）、放回碗架（将对象回收，放回到对象池中）。

上述例子中，如果不使用对象池，也就意味着碗是一次性的，也就是吃完就把碗摔了，然后要用的时候再花钱买个新的碗，这样的做法很明显是特别烧钱（浪费资源）的。




#### WooPool特点

* 支持传入参数构建对象

* 支持对象池创建对象时调用委托

* 支持限定对象池大小

* 支持从基类对象池中取出子类

* 支持数组对象池

* 支持操作非常简单的全局对象池

* 支持多个生命周期

* 支持自己扩展对象池

  

### 感谢使用WooPool！

### 欢迎加入QQ交流群[782290296](https://jq.qq.com/?_wv=1027&k=sbKbmsTY)，各路大佬给你解惑！
