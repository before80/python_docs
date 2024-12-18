+++
title = "tkinter.font --- Tkinter 字体包装器"
date = 2024-11-15T20:56:44+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/tkinter.font.html](https://docs.python.org/zh-cn/3.13/library/tkinter.font.html)
>
> 收录该文档的时间：`2024-11-15T20:56:44+08:00`

# `tkinter.font` --- Tkinter 字体包装器

**源代码:** [Lib/tkinter/font.py](https://github.com/python/cpython/tree/3.13/Lib/tkinter/font.py)

------

[`tkinter.font`]({{< ref "/library/tk/tkinter_font#module-tkinter.font" >}}) 模块提供用于创建和使用命名字体的 [`Font`]({{< ref "/library/tk/tkinter_font#tkinter.font.Font" >}}) 类。

​	不同的字体粗细和倾斜是：

## tkinter.font.**NORMAL**

## tkinter.font.**BOLD**

## tkinter.font.**ITALIC**

## tkinter.font.**ROMAN**

## *class* tkinter.font.**Font**(*root=None*, *font=None*, *name=None*, *exists=False*, ***options*)

[`Font`]({{< ref "/library/tk/tkinter_font#tkinter.font.Font" >}}) 类表示命名字体。*Font* 实例具有唯一的名称，可以通过其族、大小和样式配置进行指定。命名字体是 Tk 将字体创建和标识为单个对象的方法，而不是通过每次出现时的属性来指定字体。

> ​	参数：
>
> > *font* - 字体指示符元组 (family, size, options)
> >
> > *name* - 唯一的字体名
> >
> > *exists* - 指向现有命名字体（如果有）
>
> ​	其他关键字选项（如果指定了 *font*，则忽略）：
>
> > *family* - 字体系列，例如 Courier，Times
> >
> > *size* - 字体大小
> >
> > 如果 *size* 为正数，则解释为以磅为单位的大小。
> >
> > 如果 *size* 是负数，则将其绝对值
> >
> > 解释为以像素为单位的大小。
> >
> > *weight* - 字体强调 (NORMAL, BOLD)（普通，加粗）
> >
> > *slant* - ROMAN, ITALIC（正体，斜体）
> >
> > *underline* - 字体下划线（0 - 无下划线，1 - 有下划线）
> >
> > *overstrike* - 字体删除线（0 - 无删除线，1 - 有删除线）

## **actual**(*option=None*, *displayof=None*)

​	返回字体的属性。

## **cget**(*option*)

​	检索字体的某一个属性值。

## **config**(***options*)

​	修改字体的某一个属性值。

## **copy**()

​	返回当前字体的新实例。

## **measure**(*text*, *displayof=None*)

​	返回以当前字体格式化时文本将在指定显示上占用的空间量。如果未指定显示，则假定为主应用程序窗口。

## **metrics**(**options*, ***kw*)

​	返回特定字体的数据。选项包括：

## *ascent* - 基线和最高点之间的距离

​	（在该字体中的一个字符可以占用的空间中）

## *descent* - 基线和最低点之间的距离

​	（在该字体中的一个字符可以占用的空间中）

## *linespace* - 所需最小垂直间距（在两个

​	该字体的字符间，使得这两个字符在垂直方向上不重叠）。

*fixed* - 如果该字体宽度被固定则为1，否则为0。

## tkinter.font.**families**(*root=None*, *displayof=None*)

​	返回不同的字体系列。

## tkinter.font.**names**(*root=None*)

​	返回定义字体的名字。

## tkinter.font.**nametofont**(*name*, *root=None*)

​	返回一个 [`Font`]({{< ref "/library/tk/tkinter_font#tkinter.font.Font" >}}) 类，代表一个 tk 命名的字体。

> 在 3.10 版本发生变更: 增加了 *root* 形参。
