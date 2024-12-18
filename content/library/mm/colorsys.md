+++
title = "colorsys --- 颜色系统间的转换"
date = 2024-11-15T20:46:14+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/colorsys.html](https://docs.python.org/zh-cn/3.13/library/colorsys.html)
>
> 收录该文档的时间：`2024-11-15T20:46:14+08:00`

# `colorsys` --- 颜色系统间的转换

**源代码：** [Lib/colorsys.py](https://github.com/python/cpython/tree/3.13/Lib/colorsys.py)

------

[`colorsys`]({{< ref "/library/mm/colorsys#module-colorsys" >}}) 模块定义了计算机显示器所用的 RGB (Red Green Blue) 色彩空间与三种其他色彩坐标系统 YIQ, HLS (Hue Lightness Saturation) 和 HSV (Hue Saturation Value) 表示的颜色值之间的双向转换。 所有这些色彩空间的坐标都使用浮点数值来表示。 在 YIQ 空间中，Y 坐标取值为 0 和 1 之间，而 I 和 Q 坐标均可以为正数或负数。 在所有其他空间中，坐标取值均为 0 和 1 之间。

​参见
 

​	有关色彩空间的更多信息可访问 https://poynton.ca/ColorFAQ.html 和 https://www.cambridgeincolour.com/tutorials/color-spaces.htm。

[`colorsys`]({{< ref "/library/mm/colorsys#module-colorsys" >}}) 模块定义了如下函数：

## colorsys.**rgb_to_yiq**(*r*, *g*, *b*)

​	把颜色从RGB值转为YIQ值。

## colorsys.**yiq_to_rgb**(*y*, *i*, *q*)

​	把颜色从YIQ值转为RGB值。

## colorsys.**rgb_to_hls**(*r*, *g*, *b*)

​	把颜色从RGB值转为HLS值。

## colorsys.**hls_to_rgb**(*h*, *l*, *s*)

​	把颜色从HLS值转为RGB值。

## colorsys.**rgb_to_hsv**(*r*, *g*, *b*)

​	把颜色从RGB值转为HSV值。

## colorsys.**hsv_to_rgb**(*h*, *s*, *v*)

​	把颜色从HSV值转为RGB值。

​	示例:



``` python
>>> import colorsys
>>> colorsys.rgb_to_hsv(0.2, 0.4, 0.4)
(0.5, 0.5, 0.4)
>>> colorsys.hsv_to_rgb(0.5, 0.5, 0.4)
(0.2, 0.4, 0.4)
```
