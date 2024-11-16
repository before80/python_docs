+++
title = "py_compile --- 编译 Python 源文件"
date = 2024-11-15T21:23:55+08:00
weight = 70
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/py_compile.html](https://docs.python.org/zh-cn/3.13/library/py_compile.html)
>
> 收录该文档的时间：`2024-11-15T21:23:55+08:00`

# `py_compile` --- 编译 Python 源文件

**源代码:** [Lib/py_compile.py](https://github.com/python/cpython/tree/3.13/Lib/py_compile.py)

------

[`py_compile`]({{< ref "/library/language/py_compile#module-py_compile" >}}) 模块提供了用来从源文件生成字节码的函数和另一个用于当模块源文件作为脚本被调用时的函数。

​	虽然不太常用，但这个函数在安装共享模块时还是很有用的，特别是当一些用户可能没有权限在包含源代码的目录中写字节码缓存文件时。

## *exception* py_compile.**PyCompileError**

​	当编译文件过程中发生错误时，抛出的异常。

## py_compile.**compile**(*file*, *cfile=None*, *dfile=None*, *doraise=False*, *optimize=-1*, *invalidation_mode=PycInvalidationMode.TIMESTAMP*, *quiet=0*)

​	将源文件编译成字节码并写入字节码缓存文件。 源代码将从名为 *file* 的文件中加载。 字节码会被写入到 *cfile*，它默认为 [**PEP 3147**](https://peps.python.org/pep-3147/)/[**PEP 488**](https://peps.python.org/pep-0488/) 路径，以 `.pyc` 结尾。 举例来说，如果 *file* 为 `/foo/bar/baz.py` 则对于 Python 3.2 *cfile* 将默认为 `/foo/bar/__pycache__/baz.cpython-32.pyc`。 如果指定了 *dfile*，则将用它而不是 *file* 作为在异常回溯中获取并显示的源文件的名称。 如果 *doraise* 为真值，则当编译 *file* 遇到错误时将引发 [`PyCompileError`]({{< ref "/library/language/py_compile#py_compile.PyCompileError" >}})。 如果 *doraise* 为（默认的）假值，则会将错误字符串写入到 `sys.stderr`，但不会引发异常。 此函数返回已编译字节码文件的路径，即 *cfile* 所使用的值。

*doraise* 和 *quiet* 参数确定在编译文件时如何处理错误。 如果 *quiet* 为 0 或 1，并且 *doraise* 为假值，则会启用默认行为：写入错误信息到 `sys.stderr`，并且函数将返回 `None` 而非一个路径。 如果 *doraise* 为真值，则将改为引发 [`PyCompileError`]({{< ref "/library/language/py_compile#py_compile.PyCompileError" >}})。 但是如果 *quiet* 为 2，则不会写入消息，并且 *doraise* 也不会有效果。

​	如果 *cfile* 所表示（显式指定或计算得出）的路径为符号链接或非常规文件，则将引发 [`FileExistsError`]({{< ref "/library/exceptions#FileExistsError" >}})。 此行为是用来警告如果允许写入编译后字节码文件到这些路径则导入操作将会把它们转为常规文件。 这是使用文件重命名来将最终编译后字节码文件放置到位以防止并发文件写入问题的导入操作的附带效果。

*optimize* 控制优化级别并会被传给内置的 [`compile()`]({{< ref "/library/functions#compile" >}}) 函数。 默认值 `-1` 表示选择当前解释器的优化级别。

*invalidation_mode* 应当是 [`PycInvalidationMode`]({{< ref "/library/language/py_compile#py_compile.PycInvalidationMode" >}}) 枚举的成员，它控制在运行时如何让已生成的字节码缓存失效。 如果设置了 `SOURCE_DATE_EPOCH` 环境变量则默认值为 [`PycInvalidationMode.CHECKED_HASH`]({{< ref "/library/language/py_compile#py_compile.PycInvalidationMode.CHECKED_HASH" >}})，否则默认值为 [`PycInvalidationMode.TIMESTAMP`]({{< ref "/library/language/py_compile#py_compile.PycInvalidationMode.TIMESTAMP" >}})。

> 在 3.2 版本发生变更: 将 *cfile* 的默认值改成与 [**PEP 3147**](https://peps.python.org/pep-3147/) 兼容。 之前的默认值是 *file* + `'c'` (如果启用优化则为 `'o'`)。 同时也添加了 *optimize* 形参。

> 在 3.4 版本发生变更: 将代码更改为使用 [`importlib`]({{< ref "/library/modules/importlib#module-importlib" >}}) 执行字节码缓存文件写入。 这意味着文件创建/写入的语义现在与 [`importlib`]({{< ref "/library/modules/importlib#module-importlib" >}}) 所做的相匹配，例如权限、写入和移动语义等等。 同时也添加了当 *cfile* 为符号链接或非常规文件时引发 [`FileExistsError`]({{< ref "/library/exceptions#FileExistsError" >}}) 的预警设置。

> 在 3.7 版本发生变更: *invalidation_mode* 形参是根据 [**PEP 552**](https://peps.python.org/pep-0552/) 的描述添加的。 如果设置了 `SOURCE_DATE_EPOCH` 环境变量，*invalidation_mode* 将被强制设为 [`PycInvalidationMode.CHECKED_HASH`]({{< ref "/library/language/py_compile#py_compile.PycInvalidationMode.CHECKED_HASH" >}})。

*在 3.7.2 版本发生变更:* `SOURCE_DATE_EPOCH` 环境变量不会再覆盖 *invalidation_mode* 参数的值，而改为确定其默认值。

> 在 3.8 版本发生变更: 增加了 *quiet* 形参。

## *class* py_compile.**PycInvalidationMode**

​	一个由可用方法组成的枚举，解释器可以用它来确定字节码文件是否与源文件保持同步。 `.pyc` 文件在其标头中指明了所需的失效模式。 请参阅 [已缓存字节码的失效]({{< ref "/reference/import#pyc-invalidation" >}}) 了解有关 Python 在运行时如何让 `.pyc` 文件失效的更多信息。

> Added in version 3.7.
>

## **TIMESTAMP**

`.pyc` 文件包括时间戳和源文件的大小，Python 将在运行时将其与源文件的元数据进行比较以确定 `.pyc` 文件是否需要重新生成。

## **CHECKED_HASH**

`.pyc` 文件包括源文件内容的哈希值，Python 将在运行时将其与源文件内容进行比较以确定 `.pyc` 文件是否需要重新生成。

## **UNCHECKED_HASH**

​	类似于 [`CHECKED_HASH`]({{< ref "/library/language/py_compile#py_compile.PycInvalidationMode.CHECKED_HASH" >}})，`.pyc` 文件包括源文件内容的哈希值。 但是，Python 将在运行时假定 `.pyc` 文件是最新的而完全不会将 `.pyc` 与源文件进行验证。

​	此选项适用于 `.pycs` 由 Python 以外的某个系统例如构建系统来确保最新的情况。



## 命令行接口

​	这个模块可作为脚本发起调用以编译多个源文件。 在 *filenames* 中指定的文件会被编译并将结果字节码以普通方式进行缓存。 这个程序不会搜索目录结构来定位源文件；它只编译显式指定的文件。 如果某个文件无法被编译则退出状态为非零值。

## **<file>** ... <fileN>

## **-**

​	位置参数是要编译的文件。 如果 `-` 是唯一的形参，则文件列表将从标准输入获取。

## **-q**, `--quiet`

​	屏蔽错误输出。

> 在 3.2 版本发生变更: 添加了对 `-` 的支持。

> 在 3.10 版本发生变更: 添加了对 [`-q`]({{< ref "/library/language/py_compile#cmdoption-python-m-py_compile-q" >}}) 的支持。

​参见
## 模块 [`compileall`]({{< ref "/library/language/compileall#module-compileall" >}})

​	编译一个目录树中所有 Python 源文件的工具。
