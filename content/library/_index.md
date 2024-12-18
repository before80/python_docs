+++
title = "Python 标准库"
linkTitle="Python 标准库"
date = 2024-11-14T22:23:58+08:00
type = "docs"
description = ""
isCJKLanguage = true
draft = false
[menu.main]
    weight = 30

+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/index.html](https://docs.python.org/zh-cn/3.13/library/index.html)
>
> 收录该文档的时间：`2024-11-14T22:23:58+08:00`

# Python 标准库

​	[Python 语言参考手册]({{< ref "/reference#reference-index" >}}) 描述了 Python 语言的具体语法和语义，这份库参考则介绍了与 Python 一同发行的标准库。它还描述了通常包含在 Python 发行版中的一些可选组件。

​	Python 标准库非常庞大，所提供的组件涉及范围十分广泛，正如以下内容目录所显示的。这个库包含了多个内置模块 (以 C 编写)，Python 程序员必须依靠它们来实现系统级功能，例如文件 I/O，此外还有大量以 Python 编写的模块，提供了日常编程中许多问题的标准解决方案。其中有些模块经过专门设计，通过将特定平台功能抽象化为平台中立的 API 来鼓励和加强 Python 程序的可移植性。

​	Windows 版本的 Python 安装程序通常包含整个标准库，往往还包含许多额外组件。对于类 Unix 操作系统，Python 通常会分成一系列的软件包，因此可能需要使用操作系统所提供的包管理工具来获取部分或全部可选组件。

​	在标准库以外，还存在成千上万并且不断增加的其他组件集（从单独的程序和模块到软件包以及完整的应用程序开发框架），这些组件集可以从`Python 包索引 <[https://pypi.org](https://pypi.org/)>`_ 获取。
