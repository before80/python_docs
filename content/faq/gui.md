+++
title = "图形用户界面（GUI）常见问题"
date = 2024-11-14T22:34:43+08:00
weight = 60
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/faq/gui.html](https://docs.python.org/zh-cn/3.13/faq/gui.html)
>
> 收录该文档的时间：`2024-11-14T22:34:43+08:00`

# [图形用户界面（GUI）常见问题]({{< ref "/faq/gui#id2" >}})

​	目录

- [图形用户界面（GUI）常见问题]({{< ref "/faq/gui#graphic-user-interface-faq" >}})
  - [图形界面常见问题]({{< ref "/faq/gui#general-gui-questions" >}})
  - [Python 有哪些 GUI 工具包？]({{< ref "/faq/gui#what-gui-toolkits-exist-for-python" >}})
  - [有关Tkinter的问题]({{< ref "/faq/gui#tkinter-questions" >}})
    - [我怎样“冻结”Tkinter程序？]({{< ref "/faq/gui#how-do-i-freeze-tkinter-applications" >}})
    - [在等待 I/O 操作时能够处理 Tk 事件吗？]({{< ref "/faq/gui#can-i-have-tk-events-handled-while-waiting-for-i-o" >}})
    - [在Tkinter中键绑定不工作：为什么？]({{< ref "/faq/gui#i-can-t-get-key-bindings-to-work-in-tkinter-why" >}})

## [图形界面常见问题]({{< ref "/faq/gui#id3" >}})

## [Python 有哪些 GUI 工具包？]({{< ref "/faq/gui#id4" >}})

​	Python 的标准构建包括一个指向 Tcl/Tk 部件集的面向对象的接口，称为 [tkinter]({{< ref "/library/tk#tkinter" >}}) 。 这可能是最容易安装（因为它包含在大多数 Python 的 [二进制发行版](https://www.python.org/downloads/) 中）和使用的。关于 Tk 的更多信息，包括指向源代码的信息，见 [Tcl/Tk 主页](https://www.tcl.tk/) 。 Tcl/Tk 可以完全移植到 macOS 、 Windows 和 Unix 平台。

​	存在多种选项，具体取决于你的目标平台。 Python Wiki 上提供了一个 [跨平台](https://wiki.python.org/moin/GuiProgramming#Cross-Platform_Frameworks) 和 [平台专属](https://wiki.python.org/moin/GuiProgramming#Platform-specific_Frameworks) 的 GUI 框架列表。

## [有关Tkinter的问题]({{< ref "/faq/gui#id5" >}})

### [我怎样“冻结”Tkinter程序？]({{< ref "/faq/gui#id6" >}})

​	Freeze （意为 “冻结”）是一个用来创建独立应用程序的工具。 当 “冻结” Tkinter 程序时，程序并不是真的能够独立运行，因为程序仍然需要 Tcl 和 Tk 库。

​	一种解决方案是将应用程序与 Tcl 和 Tk 库同一起发布，并在运行时使用 `TCL_LIBRARY` 和 `TK_LIBRARY` 环境变量指向它们的位置。

​	各种第三方冻结库例如 py2exe 和 cx_Freeze 都能够处理 Tkinter 应用程序的内置对象。

### [在等待 I/O 操作时能够处理 Tk 事件吗？]({{< ref "/faq/gui#id7" >}})

​	在 Windows 以外的平台上，你甚至不需要使用线程！ 但您必须稍微调整一下你的 I/O 代码。 Tk 有与 Xt 的 `XtAddInput()` 对应的调用，它允许你注册一个回调函数，当可以对一个文件描述符进行 I/O 操作时，该函数将从 Tk 的主循环中被调用。 参见 [文件处理程序]({{< ref "/library/tk/tkinter#tkinter-file-handlers" >}})。

### [在Tkinter中键绑定不工作：为什么？]({{< ref "/faq/gui#id8" >}})

​	一个经常听到的抱怨是：已经通过 `bind()` 方法 [绑定]({{< ref "/library/tk/tkinter#bindings-and-events" >}}) 到事件的事件处理器在对应的键被按下时并没有被处理。

​	最常见的原因是，那个绑定的控件没有“键盘焦点”。请在 Tk 文档中查找 focus 指令。通常一个控件要获得“键盘焦点”，需要点击那个控件（而不是标签；请查看 takefocus 选项）。
