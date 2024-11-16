+++
title = "Tk图形用户界面(GUI)"
date = 2024-11-14T22:00:25+08:00
weight = 240
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/tk.html](https://docs.python.org/zh-cn/3.13/library/tk.html)
>
> 收录该文档的时间：`2024-11-14T22:00:25+08:00`

​	Tk/Tcl 早已成为 Python 的一部分。 它提供了一套健壮且独立于平台的窗口工具集，Python 程序员可通过 [`tkinter`]({{< ref "/library/tk/tkinter#module-tkinter" >}}) 包及其扩展 [`tkinter.ttk`]({{< ref "/library/tk/tkinter_ttk#module-tkinter.ttk" >}}) 模块来使用它。

​	[`tkinter`]({{< ref "/library/tk/tkinter#module-tkinter" >}}) 包是使用面向对象方式对 Tcl/Tk 进行的一层薄包装。 使用 [`tkinter`]({{< ref "/library/tk/tkinter#module-tkinter" >}})，你不需要写 Tcl 代码，但你将需要参阅 Tk 文档，有时还需要参阅 Tcl 文档。 [`tkinter`]({{< ref "/library/tk/tkinter#module-tkinter" >}}) 是一组包装器，它将 Tk 的可视化部件实现为相应的 Python 类。

​	[`tkinter`]({{< ref "/library/tk/tkinter#module-tkinter" >}}) 的主要特点是速度很快，并且通常直接附带在 Python 中。 虽然它的官方文档做得不好，但还是有许多可用的资源，包括：在线参考、教程、入门书等等。 [`tkinter`]({{< ref "/library/tk/tkinter#module-tkinter" >}}) 还有众所周知的较过时的外观界面，这在 Tk 8.5 中已得到很大改进。 无论如何，你还可以考虑许多其他的 GUI 库。 Python wiki 例出了一些替代性的 [GUI 框架和工具](https://wiki.python.org/moin/GuiProgramming)。
