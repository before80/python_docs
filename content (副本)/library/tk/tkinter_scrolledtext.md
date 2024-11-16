+++
title = "tkinter.scrolledtext --- 流动文本控件"
date = 2024-11-15T20:56:44+08:00
weight = 50
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/tkinter.scrolledtext.html](https://docs.python.org/zh-cn/3.13/library/tkinter.scrolledtext.html)
>
> 收录该文档的时间：`2024-11-15T20:56:44+08:00`

# `tkinter.scrolledtext` --- 流动文本控件

**源代码：** [Lib/tkinter/scrolledtext.py](https://github.com/python/cpython/tree/3.13/Lib/tkinter/scrolledtext.py)

------

[`tkinter.scrolledtext`]({{< ref "/library/tk/tkinter_scrolledtext#module-tkinter.scrolledtext" >}}) 模块提供了一个同名的类，实现了带有垂直滚动条并被配置为可以“正常运作”的文本控件 。 使用 [`ScrolledText`]({{< ref "/library/tk/tkinter_scrolledtext#tkinter.scrolledtext.ScrolledText" >}}) 类会比直接配置一个文本控件附加滚动条要简单得多。

​	文本控件与滚动条打包在一个 `Frame` 中， `Grid` 方法和 `Pack` 方法的布局管理器从 `Frame` 对象中获得。这允许 [`ScrolledText`]({{< ref "/library/tk/tkinter_scrolledtext#tkinter.scrolledtext.ScrolledText" >}}) 控件可以直接用于实现大多数正常的布局管理行为。

​	如果需要更具体的控制，可以使用以下属性：

## *class* tkinter.scrolledtext.**ScrolledText**(*master=None*, ***kw*)

## **frame**

​	围绕文本和滚动条控件的框架。

## **vbar**

​	滚动条控件。
