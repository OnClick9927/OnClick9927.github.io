---
title: UGUI合批-优化
author: 灵
comment: 'on'
tags:
  - UGUI
category:
  - Unity
  - UGUI
date: 2023-07-07 10:00:30
---
# 触发合批计算（rebuild）
* 动态创建UI
* UI材质变化
* UI 网格（Mesh）变化
* UI 位置变化
* UI 颜色变化


# 合批规则
```
// materialId
image.material.GetInstanceID()
// textureId
image.mainTexture.GetInstanceID()
```
基本条件：材质球(Shader)和贴图要完全相同。

* 1、是把所有Canvas给找出来
* 2、除掉不必渲染的Canvas(透明度为0，长宽为0，在* RectMask2D控件下，且在RectMask2D的区域外)
* 3、计算Canvas下各UI控件的深度值Depth(Image的属* 性里面也有个depth，两者不是同一个东西)
* 4、按照Depth值从小到大排序
* 5、Depth排完之后，Depth相同的元素再按material * ID从小到大排序
* 6、material ID排完之后，material ID相同的元素再* 按texture ID从小到大排序
* 7、textrure ID排完之后，textrure ID相同的元素最* 后再按在Hierarchy深度遍历的顺序排序
* 8、得到 VisiableList
* 9、遍历 VisiableList，只要前后的元素的材质球和贴图相同，就可以合批

## Depth的计算规则
* 0、按照 hierarchy 的顺序，深度遍历所有UI元素
* 1、选中一个UI元素(CurrentUI)
* 2、不渲染，Depth = -1
* 3、渲染（相交：mesh 相交）
  * 3.1、CurrentUI 与前面没有相交，则Depth = 0
  * 3.2、CurrentUI 前面只有一个(LowerUI)与其相交
    * 3.2.1、CurrentUI与LowerUI可以合批 CurrentUI.Depth = LowerUI.Depth
    * 3.2.2、CurrentUI与LowerUI不可以合批 CurrentUI.Depth = LowerUI.Depth + 1
  * 3.3、CurrentUI与前面有 N个UI相交
    * 3.3.1、 重复3.2.1计算出 N 个Depth值
    * 3.3.2、 CurrentUI.Depth = Max(N个Depth值)


# 合批的优化
* 减少计算难度
  * 减小 Hierarchy 深度/复杂度：深度遍历，计算次数
* 减少计算次数
  * 动静分离
  * 避免频繁删除/增加UI对象，UI层次结构变化会引起Canvas的更新(rebuild)
  * Text如果可以用图片代替就用图片代替
  * Text避免或者降低赋值的操作
  * 显示隐藏
   * 对于单个UI可以设置GetComponent< CanvasRenderer >().cull来控制
   * 而多个UI可以通过父物体挂上CanvasGroup，修改Alpha值来控制
* 尽可能不打断合批
 * 使用图集
 * 尽量不要使用Mask（其内部使用了模板缓冲，至少会造成增加2个Draw Call）
 * 动态图集，一个界面的可以放一起
 * 保证UI顺序结构合理
 * 减少 UI 不同图集或材质的倾轧
# 其他优化 
* Raycast
* 减少填充率
  * 不需要绘制的图片（只接受点击）
  * Image的 fillcenter 属性
* 避免使用LayOut
* 避免使用outLine和shadow，用TextMesh Pro代替
* 避免使用BestFit
* RichText会造成三角面增加
* Screen Space 和 World Space 中的Camera必须要指定
