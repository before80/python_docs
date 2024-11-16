+++
title = "文件和目录访问"
date = 2024-11-14T22:00:25+08:00
weight = 100
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/filesys.html](https://docs.python.org/zh-cn/3.13/library/filesys.html)
>
> 收录该文档的时间：`2024-11-14T22:00:25+08:00`

# 文件和目录访问

​	本章中描述的模块处理磁盘文件和目录。 例如，有一些模块用于读取文件的属性，以可移植的方式操作路径以及创建临时文件。 

​	本章的完整模块列表如下：

> 参见
>
> - 模块 [`os`]({{< ref "/library/allos/os#module-os" >}})
>
>   操作系统接口，包括处理比 Python [文件对象]({{< ref "/glossary/idx#term-file-object" >}}) 更低级别文件的功能。
>
> - 模块 [`io`]({{< ref "/library/allos/io#module-io" >}})
>
>   Python的内置 I/O 库，包括抽象类和一些具体的类，如文件 I/O 。
>
> - 内置函数 [`open()`]({{< ref "/library/functions#open" >}})
>
>   使用 Python 打开文件进行读写的标准方法。
