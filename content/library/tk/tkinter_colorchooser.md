+++
title = "tkinter.colorchooser --- 颜色选择对话框"
date = 2024-11-15T20:56:44+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/tkinter.colorchooser.html](https://docs.python.org/zh-cn/3.13/library/tkinter.colorchooser.html)
>
> 收录该文档的时间：`2024-11-15T20:56:44+08:00`

# `tkinter.colorchooser` --- 颜色选择对话框

**源代码:** [Lib/tkinter/colorchooser.py](https://github.com/python/cpython/tree/3.13/Lib/tkinter/colorchooser.py)

------

​	[`tkinter.colorchooser`](https://docs.python.org/zh-cn/3.13/library/tkinter.colorchooser.html#module-tkinter.colorchooser) 模块提供了 [`Chooser`](https://docs.python.org/zh-cn/3.13/library/tkinter.colorchooser.html#tkinter.colorchooser.Chooser) 类作为原生颜色选择对话框的接口。 `Chooser` 实现了一个模式颜色选择对话框窗口。 `Chooser` 类继承自 [`Dialog`](https://docs.python.org/zh-cn/3.13/library/dialog.html#tkinter.commondialog.Dialog) 类。

- *class* tkinter.colorchooser.**Chooser**(*master=None*, ***options*)

  

- tkinter.colorchooser.**askcolor**(*color=None*, ***options*)

  创建一个颜色选择对话框。 调用此方法将显示相应窗口，等待用户进行选择，并将选择的颜色 (或 `None`) 返回给调用者。

参见

- 模块 [`tkinter.commondialog`](https://docs.python.org/zh-cn/3.13/library/dialog.html#module-tkinter.commondialog)

  Tkinter 标准对话框模块
