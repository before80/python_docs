+++
title = "将扩展模块移植到 Python 3"
date = 2024-11-14T22:10:11+08:00
weight = 10070
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/howto/cporting.html](https://docs.python.org/zh-cn/3.13/howto/cporting.html)
>
> 收录该文档的时间：`2024-11-14T22:10:11+08:00`

# 将扩展模块移植到 Python 3

​	对于将扩展模块移植到 Python 3，我们推荐下列资源：

- *Supporting Python 3: An in-depth guide* 中的 [Migrating C extensions](http://python3porting.com/cextensions.html) 这一章，这本书介绍了如何从 Python 2 迁移到 Python 3，包括指导读者如何移植扩展模块。
- *py3c* 项目中的 [Porting guide](https://py3c.readthedocs.io/en/latest/guide.html) 提供了有关支持代码的指导性建议。
- [Cython](https://cython.org/) 和 [CFFI](https://cffi.readthedocs.io/en/latest/) 库提供了对于 Python 的 C API 的抽象。 扩展大都需要进行重写以使用两者中的一个，然后就可以通过库来处理各种 Python 版本和实现之间的差异。
