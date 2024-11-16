+++
title = "自定义 Python 解释器"
date = 2024-11-14T22:00:25+08:00
weight = 290
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/custominterp.html](https://docs.python.org/zh-cn/3.13/library/custominterp.html)
>
> 收录该文档的时间：`2024-11-14T22:00:25+08:00`

# 自定义 Python 解释器

​	本章中描述的模块允许编写类似于 Python 的交互式解释器的接口。 如果你想要一个支持附加某些特殊功能到 Python 语言的 Python 解释器，你应当看一下 [`code`]({{< ref "/library/custominterp/code#module-code" >}}) 模块。 ([`codeop`]({{< ref "/library/custominterp/codeop#module-codeop" >}}) 模块是低层级的，用于支持编译可能不完整的 Python 代码块。

​	本章描述的完整模块列表如下：
