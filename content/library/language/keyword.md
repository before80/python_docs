+++
title = "keyword --- 检验 Python 关键字"
date = 2024-11-15T21:23:55+08:00
weight = 30
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/keyword.html](https://docs.python.org/zh-cn/3.13/library/keyword.html)
>
> 收录该文档的时间：`2024-11-15T21:23:55+08:00`

# `keyword` --- 检验 Python 关键字

**源码：** [Lib/keyword.py](https://github.com/python/cpython/tree/3.13/Lib/keyword.py)

------

​	此模块使 Python 程序可以确定某个字符串是否为 [关键字](https://docs.python.org/zh-cn/3.13/reference/lexical_analysis.html#keywords) 或 [软关键字](https://docs.python.org/zh-cn/3.13/reference/lexical_analysis.html#soft-keywords)。

## keyword.**iskeyword**(*s*)

​	如果 *s* 是一个 Python [关键字](https://docs.python.org/zh-cn/3.13/reference/lexical_analysis.html#keywords) 则返回 `True`。

## keyword.**kwlist**

​	包含解释器定义的所有 [关键字](https://docs.python.org/zh-cn/3.13/reference/lexical_analysis.html#keywords) 的序列。 如果所定义的任何关键字仅在特定 [`__future__`](https://docs.python.org/zh-cn/3.13/library/__future__.html#module-__future__) 语句生效时被激活，它们也将被包含在内。

## keyword.**issoftkeyword**(*s*)

​	如果 *s* 是一个 Python [软关键字](https://docs.python.org/zh-cn/3.13/reference/lexical_analysis.html#soft-keywords) 则返回 `True`。

*Added in version 3.9.*

## keyword.**softkwlist**

​	包含为解释器定义的所有 [软关键字](https://docs.python.org/zh-cn/3.13/reference/lexical_analysis.html#soft-keywords) 的序列。 如果有任何被定义的软关键字是仅在当特定 [`__future__`](https://docs.python.org/zh-cn/3.13/library/__future__.html#module-__future__) 语句生效时才会被激活的，它们同样会被包括在内。

*Added in version 3.9.*
