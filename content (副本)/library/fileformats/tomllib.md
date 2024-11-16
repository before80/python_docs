+++
title = "tomllib --- 解析 TOML 文件"
date = 2024-11-15T12:03:23+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/tomllib.html](https://docs.python.org/zh-cn/3.13/library/tomllib.html)
>
> 收录该文档的时间：`2024-11-15T12:03:23+08:00`

# `tomllib` --- 解析 TOML 文件

> Added in version 3.11.
>

**源代码：** [Lib/tomllib](https://github.com/python/cpython/tree/3.13/Lib/tomllib)

------

​	此模块提供了一个解析 TOML 1.0.0 (Tom's Obvious Minimal Language, [https://toml.io](https://toml.io/en/)) 的接口。 该模块不支持写入 TOML。

​	参见

 

[Tomli-W 包](https://pypi.org/project/tomli-w/) 是一个 TOML 写入器，它可以与此模块一起使用，提供了与标准库用户熟悉的 [`marshal`]({{< ref "/library/persistence/marshal#module-marshal" >}}) 和 [`pickle`]({{< ref "/library/persistence/pickle#module-pickle" >}}) 模块类似的写入 API。

​	参见

 

[TOML Kit 包](https://pypi.org/project/tomlkit/) 一个是兼具读取和写入功能的保留样式的 TOML 库。 它是用于编辑现有 TOML 文件的本模块的推荐替代品。

​	这个模块定义了以下函数：

## tomllib.**load**(*fp*, */*, ***, *parse_float=float*)

​	读取一个 TOML 文件。第一个参数应该是一个可读的二进制文件对象。返回 [`dict`]({{< ref "/library/stdtypes#dict" >}})。使用 [转换表]({{< ref "/library/fileformats/tomllib#toml-to-py-table" >}}) 将 TOML 类型转换为 Python。

​	对每个要解析的 TOML 浮点数字符串调用 *parse_float*。默认情况下，这相当于 `float(num_str)`。这可以用于为 TOML 浮点数使用另一种数据类型或解析器（例如：[`decimal.Decimal`]({{< ref "/library/numeric/decimal#decimal.Decimal" >}})）。可调用对象不能返回 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 或 [`list`]({{< ref "/library/stdtypes#list" >}})，否则将引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

​	对无效的 TOML 文档将引发 [`TOMLDecodeError`]({{< ref "/library/fileformats/tomllib#tomllib.TOMLDecodeError" >}})。

## tomllib.**loads**(*s*, */*, ***, *parse_float=float*)

​	从 [`str`]({{< ref "/library/stdtypes#str" >}}) 对象中加载 TOML。返回 [`dict`]({{< ref "/library/stdtypes#dict" >}})。使用 [转换表]({{< ref "/library/fileformats/tomllib#toml-to-py-table" >}}) 将 TOML 类型转换为 Python类型。参数 *parse_float* 与 [`load()`]({{< ref "/library/fileformats/tomllib#tomllib.load" >}}) 中的意义相同。

​	对无效的 TOML 文档将引发 [`TOMLDecodeError`]({{< ref "/library/fileformats/tomllib#tomllib.TOMLDecodeError" >}})。

​	有以下几种异常：

## *exception* tomllib.**TOMLDecodeError**

[`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 的子类

## 例子

​	解析 TOML 文件:

```
import tomllib

with open("pyproject.toml", "rb") as f:
    data = tomllib.load(f)
```

​	解析 TOML 字符串:

```
import tomllib

toml_str = """
python-version = "3.11.0"
python-implementation = "CPython"
"""

data = tomllib.loads(toml_str)
```

## 转换表

| TOML             | Python                                                       |
| :--------------- | :----------------------------------------------------------- |
| TOML 文档        | dict                                                         |
| string           | str                                                          |
| integer          | int                                                          |
| float            | float（可用 *parse_float* 配置）                             |
| boolean          | bool                                                         |
| offset date-time | datetime.datetime（`tzinfo` 属性设置为 `datetime.timezone` 的实例） |
| local date-time  | datetime.datetime （`tzinfo` 属性设置为 `None`）             |
| local date       | datetime.date                                                |
| local time       | datetime.time                                                |
| array            | list                                                         |
| table            | dict                                                         |
| 内联表           | dict                                                         |
| 表数组           | 字典列表                                                     |
