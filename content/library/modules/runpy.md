+++
title = "runpy --- 查找并执行 Python 模块"
date = 2024-11-15T21:19:20+08:00
weight = 30
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/runpy.html](https://docs.python.org/zh-cn/3.13/library/runpy.html)
>
> 收录该文档的时间：`2024-11-15T21:19:20+08:00`

# `runpy` --- 查找并执行 Python 模块

**源代码：** [Lib/runpy.py](https://github.com/python/cpython/tree/3.13/Lib/runpy.py)

------

[`runpy`](https://docs.python.org/zh-cn/3.13/library/runpy.html#module-runpy) 模块用于找到并运行 Python 的模块，而无需首先导入。主要用于实现 [`-m`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-m) 命令行开关，以允许用 Python 模块命名空间而不是文件系统来定位脚本。

​	请注意，这 *并非* 一个沙盒模块——所有代码都在当前进程中运行，所有副作用（如其他模块对导入操作进行了缓存）在函数返回后都会留存。

​	此外，在 [`runpy`](https://docs.python.org/zh-cn/3.13/library/runpy.html#module-runpy) 函数返回后，任何由已执行代码定义的函数和类都不能保证正确工作。如果某使用场景不能接收此限制，那么选用 [`importlib`](https://docs.python.org/zh-cn/3.13/library/importlib.html#module-importlib) 可能更合适些。

[`runpy`](https://docs.python.org/zh-cn/3.13/library/runpy.html#module-runpy) 模块提供两个函数：

## runpy.**run_module**(*mod_name*, *init_globals=None*, *run_name=None*, *alter_sys=False*)

​	执行给定模块的代码并返回模块的全局 globals 字典作为结果。 首先会使用标准的导入机制来定位该模块的代码（请参阅 [**PEP 302**](https://peps.python.org/pep-0302/) 了解详情）然后在新的模块命令空间中执行。

*mod_name* 参数应当是一个绝对模块名。 如果模块名指向一个包而非普通模块，则会导入这个包然后执行这个包中的 [`__main__`](https://docs.python.org/zh-cn/3.13/library/__main__.html#module-__main__) 子模块再返回模块全局字典。

​	可选的字典参数 *init_globals* 可用来在代码执行前预填充模块的 globals 字典。 *init_globals* 不会被修改。 如果在 *init_globals* 中定义了下面的任何一个特殊全局变量，这些定义都会被 [`run_module()`](https://docs.python.org/zh-cn/3.13/library/runpy.html#runpy.run_module) 覆盖。

​	特殊全局变量 `__name__`, `__spec__`, `__file__`, `__cached__`, `__loader__` and `__package__` 会在模块代码被执行前在 globals 字典中设置。 （请注意这是一个最小化的变量集合 —— 作为解释器的实现细节其他变量有可能被隐式地设置。）

​	若可选参数 `__name__` 不为 [`None`](https://docs.python.org/zh-cn/3.13/library/constants.html#None) 则设为 *run_name*，若此名称的模块是一个包则设为 `mod_name + '.__main__'`，否则设为 *mod_name* 参数。

`__spec__` 将针对 *实际* 导入的模块进行适当的设置 (也就是说，`__spec__.name` 将始终为 *mod_name* 或 `mod_name + '.__main__'`，而不是 *run_name*)。

`__file__` 、`__cached__`、 `__loader__` 和 `__package__` 根据模块规格进行 [常规设置](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#import-mod-attrs)

​	如果给出了参数 *alter_sys* 并且值为 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)，那么 `sys.argv[0]` 将被更新为 `__file__` 的值，`sys.modules[__name__]` 将被更新为临时模块对象。在函数返回前， `sys.argv[0]` 和 `sys.modules[__name__]` 将会复原。

​	请注意对 [`sys`](https://docs.python.org/zh-cn/3.13/library/sys.html#module-sys) 的这种操作不是线程安全的。 其他线程可能会看到部分初始化的模块，以及更改后的参数列表。 建议当从线程中的代码调用此函数时不要使用 `sys` 模块。

​	参见

 

[`-m`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-m) 选项由命令行提供相同功能。

*在 3.1 版本发生变更:* 增加了通过查找 [`__main__`](https://docs.python.org/zh-cn/3.13/library/__main__.html#module-__main__) 子模块来执行包的功能。

*在 3.2 版本发生变更:* 加入了 `__cached__` 全局变量（参见 [**PEP 3147**](https://peps.python.org/pep-3147/) ）。

*在 3.4 版本发生变更:* 充分利用 [**PEP 451**](https://peps.python.org/pep-0451/) 加入的模块规格功能。使得以这种方式运行的模块能够正确设置 `__cached__`，并确保真正的模块名称总是可以通过 `__spec__.name` 的形式访问。

*在 3.12 版本发生变更:* `__cached__`, `__loader__` 和 `__package__` 的设置已被弃用。 替代设置参见 [`ModuleSpec`](https://docs.python.org/zh-cn/3.13/library/importlib.html#importlib.machinery.ModuleSpec)。

## runpy.**run_path**(*path_name*, *init_globals=None*, *run_name=None*)

​	执行位于指定文件系统位置上的代码并返回模块的 globals 字典作为结果。 与提供给 CPython 命令行的脚本名称一样，*file_path* 可以指向一个 Python 源文件、编译后的字节码文件或包含 [`__main__`](https://docs.python.org/zh-cn/3.13/library/__main__.html#module-__main__) 模块的有效 [`sys.path`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.path) 条目（例如一个包含最高层级 `__main__.py` 文件的 zip 文件）。

​	对于简单的脚本而言，只需在新的模块命名空间中执行指定的代码即可。 对于一个有效的 [`sys.path`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.path) 条目（通常是一个 zip 文件或目录），首先会将该条目添加到 `sys.path` 的开头。 然后函数会使用更新后的路径查找并执行 [`__main__`](https://docs.python.org/zh-cn/3.13/library/__main__.html#module-__main__) 模块。 请注意如果在指定的位置上没有 `__main__` 模块那么在发起调用位于 `sys.path` 中其他位置上的现有条目时也不会受到特殊保护。

​	可选的字典参数 *init_globals* 可用来在代码执行前预填充模块的 globals 字典。 *init_globals* 不会被修改。 如果在 *init_globals* 中定义了下面的任何一个特殊全局变量，这些定义都会被 [`run_path()`](https://docs.python.org/zh-cn/3.13/library/runpy.html#runpy.run_path) 覆盖。

​	特殊全局变量 `__name__`, `__spec__`, `__file__`, `__cached__`, `__loader__` and `__package__` 会在模块代码被执行前在 globals 字典中设置。 （请注意这是一个最小化的变量集合 —— 作为解释器的实现细节其他变量有可能被隐式地设置。）

​	如果该可选参数不为 [`None`](https://docs.python.org/zh-cn/3.13/library/constants.html#None)，则 `__name__` 被设为 *run_name*，否则为 `'<run_path>'`。

​	如果 *file_path* 直接指向一个脚本文件（无论是源码还是预编译的字节码），则 `__file__` 将被设为 *file_path*，而 `__spec__`, `__cached__`, `__loader__` 和 `__package__` 都将被设为 [`None`](https://docs.python.org/zh-cn/3.13/library/constants.html#None)。

​	如果 *file_path* 是对一个有效 [`sys.path`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.path) 条目的引用，则 `__spec__` 将针对导入的 [`__main__`](https://docs.python.org/zh-cn/3.13/library/__main__.html#module-__main__) 模块进行相应设置 (也就是说，`__spec__.name` 将始终为 `__main__`)。 `__file__`, `__cached__`, `__loader__` 和 `__package__` 将根据模块规格说明 [正常设置](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#import-mod-attrs)。

[`sys`](https://docs.python.org/zh-cn/3.13/library/sys.html#module-sys) 模块也进行了多项改动。 首先，[`sys.path`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.path) 可能会有如上文所描述的调整，`sys.argv[0]` 会使用 *file_path* 的值进行更新而 `sys.modules[__name__]` 会使用对应于被执行模块的临时模块对象进行更新。 在函数返回之前对 [`sys`](https://docs.python.org/zh-cn/3.13/library/sys.html#module-sys) 中条目的所有修改都会被复原。

​	请注意，与 [`run_module()`](https://docs.python.org/zh-cn/3.13/library/runpy.html#runpy.run_module) 不同，对 [`sys`](https://docs.python.org/zh-cn/3.13/library/sys.html#module-sys) 的修改在本函数中不是可选项，因为这些调整对于允许执行 [`sys.path`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.path) 条目来说是至关重要的。 由于线程安全限制仍然适用，在线程代码中使用该函数应当使用导入锁进行序列化，或是委托给单独的进程。

​	参见

 

[接口选项](https://docs.python.org/zh-cn/3.13/using/cmdline.html#using-on-interface-options) 用于在命令行上实现同等功能（`python path/to/script`）。

> Added in version 3.2.
>

*在 3.4 版本发生变更:* 进行更新以便利用 [**PEP 451**](https://peps.python.org/pep-0451/) 加入的模块规格特性。 这允许在 `__main__` 是从有效的 [`sys.path`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.path) 条目导入而不是直接执行的情况下能够正确地设置 `__cached__`。

*在 3.12 版本发生变更:* `__cached__`, `__loader__` 和 `__package__` 已被弃用。

> 参见
>
> 
>
> - [**PEP 338**](https://peps.python.org/pep-0338/) -- 将模块作为脚本执行
>
>   PEP 由 Nick Coghlan 撰写并实现。
>
> - [**PEP 366**](https://peps.python.org/pep-0366/) ——主模块的显式相对导入
>
>   PEP 由 Nick Coghlan 撰写并实现。
>
> - [**PEP 451**](https://peps.python.org/pep-0451/) —— 导入系统采用的 ModuleSpec 类型
>
>   PEP 由 Eric Snow 撰写并实现。
>
> [命令行与环境](https://docs.python.org/zh-cn/3.13/using/cmdline.html#using-on-general) —— CPython 命令行详解
>
> [`importlib.import_module()`](https://docs.python.org/zh-cn/3.13/library/importlib.html#importlib.import_module) 函数
