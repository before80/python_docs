+++
title = "tkinter.dnd --- 拖放操作支持"
date = 2024-11-15T20:56:44+08:00
weight = 60
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/tkinter.dnd.html](https://docs.python.org/zh-cn/3.13/library/tkinter.dnd.html)
>
> 收录该文档的时间：`2024-11-15T20:56:44+08:00`

# `tkinter.dnd` --- 拖放操作支持

**源代码:** [Lib/tkinter/dnd.py](https://github.com/python/cpython/tree/3.13/Lib/tkinter/dnd.py)

------

​备注
 

​	此模块是实验性的且在为 Tk DND 所替代后将被弃用。

[`tkinter.dnd`]({{< ref "/library/tk/tkinter_dnd#module-tkinter.dnd" >}}) 模块为单个应用内部的对象提供了在同一窗口中或多个窗口间的拖放操作支持。 要将对象设为可拖放，你必须为其创建启动拖放进程的事件绑定。 通常，你要将 ButtonPress 事件绑定到你所编写的回调函数 (参见 [绑定和事件]({{< ref "/library/tk/tkinter#bindings-and-events" >}}))。 该函数应当调用 [`dnd_start()`]({{< ref "/library/tk/tkinter_dnd#tkinter.dnd.dnd_start" >}})，其中 'source' 为要拖动的对象，而 'event' 为发起调用的事件（你的回调函数的参数）。

​	目标对象的选择方式如下:

1. 从顶至底地在鼠标之下的区域中搜索目标控件

> - 目标控件应当具有一个指向可调用对象的 *dnd_accept* 属性
> - 如果 *dnd_accept* 不存在或者返回 `None`，则将转至父控件中搜索
> - 如果目标控件未找到，则目标对象为 `None`

1. 调用 *<old_target>.dnd_leave(source, event)*
2. 调用 *<new_target>.dnd_enter(source, event)*
3. 调用 *<target>.dnd_commit(source, event)* 来通知释放
4. 调用 *<source>.dnd_end(target, event)* 来表明拖放的结束

## *class* tkinter.dnd.**DndHandler**(*source*, *event*)

*DndHandler* 类处理拖放事件，在事件控件的根对象上跟踪 Motion 和 ButtonRelease 事件。

## **cancel**(*event=None*)

​	取消拖放进程。

## **finish**(*event*, *commit=0*)

​	执行结束播放函数。

## **on_motion**(*event*)

​	在执行拖动期间为目标对象检查鼠标之下的区域。

## **on_release**(*event*)

​	当释放模式被触发时表明拖动的结束。

## tkinter.dnd.**dnd_start**(*source*, *event*)

​	用于拖放进程的工厂函数。

​参见
 

[绑定和事件]({{< ref "/library/tk/tkinter#bindings-and-events" >}})
