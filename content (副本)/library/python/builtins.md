+++
title = "builtins --- 内置对象"
date = 2024-11-15T21:12:39+08:00
weight = 30
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/builtins.html](https://docs.python.org/zh-cn/3.13/library/builtins.html)
>
> 收录该文档的时间：`2024-11-15T21:12:39+08:00`

# `builtins` --- 内置对象

------

​	This module provides direct access to all 'built-in' identifiers of Python; for example, `builtins.open` is the full name for the built-in function [`open()`]({{< ref "/library/functions#open" >}}).

​	大多数应用程序通常不会显式访问此模块，但在提供与内置值同名的对象的模块中可能很有用，但其中还需要内置该名称。例如，在一个想要实现 [`open()`]({{< ref "/library/functions#open" >}}) 函数的模块中，它包装了内置的 [`open()`]({{< ref "/library/functions#open" >}}) ，这个模块可以直接使用:

```
import builtins

def open(path):
    f = builtins.open(path, 'r')
    return UpperCaser(f)

class UpperCaser:
    '''文件的包装类，将读取的内容转换为大写输出'''

    def __init__(self, f):
        self._f = f

    def read(self, count=-1):
        return self._f.read(count).upper()

    # ..
```

​	作为一个实现细节，大多数模块都将名称 `__builtins__` 作为其全局变量的一部分提供。 `__builtins__` 的值通常是这个模块或者这个模块的值 [`__dict__`]({{< ref "/reference/datamodel#object.__dict__" >}}) 属性。由于这是一个实现细节，因此 Python 的替代实现可能不会使用它。

​	参见

- [内置常量]({{< ref "/library/constants#built-in-consts" >}})
- [内置异常]({{< ref "/library/exceptions#bltin-exceptions" >}})
- [内置函数]({{< ref "/library/functions#built-in-funcs" >}})
- [内置类型]({{< ref "/library/stdtypes#bltin-types" >}})
