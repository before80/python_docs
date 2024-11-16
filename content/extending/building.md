+++
title = "4. 构建C/C++扩展"
date = 2024-11-14T22:13:29+08:00
weight = 30
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文: [https://docs.python.org/zh-cn/3.13/extending/building.html](https://docs.python.org/zh-cn/3.13/extending/building.html)
>
> 收录该文档的时间：`2024-11-14T22:13:29+08:00`

# 4. 构建C/C++扩展

​	一个CPython的C扩展是一个共享库(例如一个Linux上的 `.so` ，或者Windows上的 `.pyd` )，其会导出一个 *初始化函数* 。

​	为了可导入，共享库必须在 [`PYTHONPATH`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONPATH) 中列出，且必须按照模块名称命名，并带有正确的扩展名。 当使用 setuptools 时，会自动生成正确的文件名。

​	初始化函数的声明如下：

[PyObject](https://docs.python.org/zh-cn/3.13/c-api/structures.html#c.PyObject) *PyInit_modulename(void) 

​	该函数返回完整初始化过的模块，或一个 [`PyModuleDef`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.PyModuleDef) 实例。 请查看 [初始化 C 模块](https://docs.python.org/zh-cn/3.13/c-api/module.html#initializing-modules) 了解详情。

​	对于仅有ASCII编码的模块名，函数必须是 `PyInit_<modulename>` ，将 `<modulename>` 替换为模块的名字。当使用 [多阶段初始化](https://docs.python.org/zh-cn/3.13/c-api/module.html#multi-phase-initialization) 时，允许使用非ASCII编码的模块名。此时初始化函数的名字是 `PyInitU_<modulename>` ，而 `<modulename>` 需要用Python的 *punycode* 编码，连字号需替换为下划线。在Python里:

```
def initfunc_name(name):
    try:
        suffix = b'_' + name.encode('ascii')
    except UnicodeEncodeError:
        suffix = b'U_' + name.encode('punycode').replace(b'-', b'_')
    return b'PyInit' + suffix
```

​	可以在一个动态库里导出多个模块，通过定义多个初始化函数。而导入他们需要符号链接或自定义导入器，因为缺省时只有对应了文件名的函数才会被发现。查看 *"一个库里的多模块"* 章节，在 [**PEP 489**](https://peps.python.org/pep-0489/) 了解更多细节。



## 4.1. 使用 setuptools 构建 C 和 C++ 扩展

​	Python 3.12 及更新的版本不再包含 distutils。 请参考 https://setuptools.readthedocs.io/en/latest/setuptools.html 上的 `setuptools` 文档来更多地了解如何使用 setuptools 来构建和分发 C/C++ 扩展。
