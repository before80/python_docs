+++
title = "数据持久化"
date = 2024-11-14T22:00:25+08:00
weight = 110
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/persistence.html](https://docs.python.org/zh-cn/3.13/library/persistence.html)
>
> 收录该文档的时间：`2024-11-14T22:00:25+08:00`

# 数据持久化

​	本章中描述的模块支持在磁盘上以持久形式存储 Python 数据。 [`pickle`](https://docs.python.org/zh-cn/3.13/library/pickle.html#module-pickle) 和 [`marshal`](https://docs.python.org/zh-cn/3.13/library/marshal.html#module-marshal) 模块可以将许多 Python 数据类型转换为字节流，然后从字节中重新创建对象。 各种与 DBM 相关的模块支持一系列基于散列的文件格式，这些格式存储字符串到其他字符串的映射。

​	本章中描述的模块列表是：
