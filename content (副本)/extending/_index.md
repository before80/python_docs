+++
title = "扩展和嵌入 Python 解释器"
linkTitle="扩展和嵌入 Python 解释器"
date = 2024-11-14T22:23:58+08:00
type = "docs"
description = ""
isCJKLanguage = true
draft = false
[menu.main]
    weight = 90

+++

> 原文：[https://docs.python.org/zh-cn/3.13/extending/index.html](https://docs.python.org/zh-cn/3.13/extending/index.html)
>
> 收录该文档的时间：`2024-11-14T22:23:58+08:00`

# 扩展和嵌入 Python 解释器

本文档描述了如何使用 C 或 C++ 编写模块以使用新模块来扩展 Python 解释器的功能。 这些模块不仅可以定义新的函数，还可以定义新的对象类型及其方法。 该文档还描述了如何将 Python 解释器嵌入到另一个应用程序中，以用作扩展语言。 最后，它展示了如何编译和链接扩展模块，以便它们可以动态地（在运行时）加载到解释器中，如果底层操作系统支持此特性的话。

本文档假设你具备有关 Python 的基本知识。有关该语言的非正式介绍，请参阅 [Python 教程]({{< ref "/tutorial#tutorial-index" >}}) 。 [Python 语言参考手册]({{< ref "/reference#reference-index" >}}) 给出了更正式的语言定义。 [Python 标准库]({{< ref "/library#library-index" >}}) 包含现有的对象类型、函数和模块（内置和用 Python 编写）的文档，使语言具有广泛的应用范围。

关于整个 Python/C API 的详细介绍，请参阅独立的 [Python/C API 参考手册]({{< ref "/capi#c-api-index" >}}) 。

## 推荐的第三方工具

本指南仅介绍了作为此 CPython 版本的一部分提供的创建扩展的基本工具。 第三方工具如 [Cython](https://cython.org/), [cffi](https://cffi.readthedocs.io/), [SWIG](https://www.swig.org/) 和 [Numba](https://numba.pydata.org/) 提供了更简单或更复杂的方式来为 Python 创建 C 和 C++ 扩展。

参见

- [Python Packaging User Guide: Binary Extensions](https://packaging.python.org/guides/packaging-binary-extensions/)

  “ Python Packaging User Guide ”不仅涵盖了几个简化二进制扩展创建的可用工具，还讨论了为什么首先创建扩展模块的各种原因。

## 不使用第三方工具创建扩展

本指南的这一部分包括在没有第三方工具帮助的情况下创建 C 和 C ++ 扩展。它主要用于这些工具的创建者，而不是建议你创建自己的 C 扩展的方法。

- 1. 使用 C 或 C++ 扩展 Python

  - [1.1. 一个简单的例子]({{< ref "/extending/extending#a-simple-example" >}})
  - [1.2. 关于错误和异常]({{< ref "/extending/extending#intermezzo-errors-and-exceptions" >}})
  - [1.3. 回到例子]({{< ref "/extending/extending#back-to-the-example" >}})
  - [1.4. 模块方法表和初始化函数]({{< ref "/extending/extending#the-module-s-method-table-and-initialization-function" >}})
  - [1.5. 编译和链接]({{< ref "/extending/extending#compilation-and-linkage" >}})
  - [1.6. 在C中调用Python函数]({{< ref "/extending/extending#calling-python-functions-from-c" >}})
  - [1.7. 提取扩展函数的参数]({{< ref "/extending/extending#extracting-parameters-in-extension-functions" >}})
  - [1.8. 给扩展函数的关键字参数]({{< ref "/extending/extending#keyword-parameters-for-extension-functions" >}})
  - [1.9. 构造任意值]({{< ref "/extending/extending#building-arbitrary-values" >}})
  - [1.10. 引用计数]({{< ref "/extending/extending#reference-counts" >}})
  - [1.11. 在C++中编写扩展]({{< ref "/extending/extending#writing-extensions-in-c" >}})
  - [1.12. 给扩展模块提供C API]({{< ref "/extending/extending#providing-a-c-api-for-an-extension-module" >}})

- 2. 自定义扩展类型：教程

  - [2.1. 基础]({{< ref "/extending/newtypes_tutorial#the-basics" >}})
  - [2.2. 向基本示例添加数据和方法]({{< ref "/extending/newtypes_tutorial#adding-data-and-methods-to-the-basic-example" >}})
  - [2.3. 提供对于数据属性的更精细控制]({{< ref "/extending/newtypes_tutorial#providing-finer-control-over-data-attributes" >}})
  - [2.4. 支持循环垃圾回收]({{< ref "/extending/newtypes_tutorial#supporting-cyclic-garbage-collection" >}})
  - [2.5. 子类化其他类型]({{< ref "/extending/newtypes_tutorial#subclassing-other-types" >}})

- 3. 定义扩展类型：已分类主题

  - [3.1. 终结和内存释放]({{< ref "/extending/newtypes#finalization-and-de-allocation" >}})
  - [3.2. 对象展示]({{< ref "/extending/newtypes#object-presentation" >}})
  - [3.3. 属性管理]({{< ref "/extending/newtypes#attribute-management" >}})
  - [3.4. 对象比较]({{< ref "/extending/newtypes#object-comparison" >}})
  - [3.5. 抽象协议支持]({{< ref "/extending/newtypes#abstract-protocol-support" >}})
  - [3.6. 弱引用支持]({{< ref "/extending/newtypes#weak-reference-support" >}})
  - [3.7. 更多建议]({{< ref "/extending/newtypes#more-suggestions" >}})

- 4. 构建C/C++扩展

  - [4.1. 使用 setuptools 构建 C 和 C++ 扩展]({{< ref "/extending/building#building-c-and-c-extensions-with-setuptools" >}})

- 5. 在 Windows 上构建 C 和 C++ 扩展

  - [5.1. 菜谱式说明]({{< ref "/extending/windows#a-cookbook-approach" >}})
  - [5.2. Unix 和 Windows 之间的差异]({{< ref "/extending/windows#differences-between-unix-and-windows" >}})
  - [5.3. DLL 的实际使用]({{< ref "/extending/windows#using-dlls-in-practice" >}})

## 在更大的应用程序中嵌入 CPython 运行时

有时，不是要创建在 Python 解释器中作为主应用程序运行的扩展，而是希望将 CPython 运行时嵌入到更大的应用程序中。 本节介绍了成功完成此操作所涉及的一些细节。

- 1. 在其它应用程序嵌入 Python

  - [1.1. 高层次的嵌入]({{< ref "/extending/embedding#very-high-level-embedding" >}})
  - [1.2. 突破高层次嵌入的限制：概述]({{< ref "/extending/embedding#beyond-very-high-level-embedding-an-overview" >}})
  - [1.3. 只做嵌入]({{< ref "/extending/embedding#pure-embedding" >}})
  - [1.4. 对嵌入 Python 功能进行扩展]({{< ref "/extending/embedding#extending-embedded-python" >}})
  - [1.5. 在 C++ 中嵌入 Python]({{< ref "/extending/embedding#embedding-python-in-c" >}})
  - [1.6. 在类 Unix 系统中编译和链接]({{< ref "/extending/embedding#compiling-and-linking-under-unix-like-systems" >}})
