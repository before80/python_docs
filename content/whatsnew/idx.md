+++
title = "Python 3.13 有什么新变化"
date = 2024-11-14T22:31:40+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文: [https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html)
>
> 收录该文档的时间：`2024-11-14T22:01:54+08:00`

# Python 3.13 有什么新变化

- 编者:

  ​	Adam Turner 和 Thomas Wouters

​	本文介绍了 Python 3.13 相比 3.12 增加的新我。 Python 3.13 已于 2024 年 10 月 7 日发布。 要获取详细信息，可参阅 [更新日志](https://docs.python.org/zh-cn/3.13/whatsnew/changelog.html#changelog)。

​	参见 **PEP 719**](https://peps.python.org/pep-0719/) -- Python 3.13 发布计划

## 摘要 -- 发布重点

​	Python 3.13 是 Python 编程语言的最新稳定发布版，包含多项针对语言、实现和标准库的改变。 最大的变化包括一个新的 [交互式解释器](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#whatsnew313-better-interactive-interpreter)，以及对于在 [自由线程模式](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#whatsnew313-free-threaded-cpython) ([**PEP 703**](https://peps.python.org/pep-0703/)) 下运行和 [即时编译器](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#whatsnew313-jit-compiler) ([**PEP 744**](https://peps.python.org/pep-0744/)) 的实验性支持。

​	错误消息继续得到改进，回溯信息现在默认使用彩色高亮显示。 [`locals()`](https://docs.python.org/zh-cn/3.13/library/functions.html#locals) 内置函数现在对于修改所返回的映射具有 [更细化的语法](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#whatsnew313-locals-semantics)，并且类型形参现在支持设置默认值。

​	针对标准库的改变包括移除已弃用的 API 和模块，以及用户友好度和正确性方面的常规提升。 一些旧式标准库模块自 Python 3.11 起被弃用 ([**PEP 594**](https://peps.python.org/pep-0594/)) 之后现在 [已被移除](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#whatsnew313-pep594)。

​	本文并不试图提供所有新特性的完整规范说明，而是提供一个方便的概览。 要了解完整细节请参阅相应文档，如 [标准库参数](https://docs.python.org/zh-cn/3.13/library/index.html#library-index) 和 [语言参考](https://docs.python.org/zh-cn/3.13/reference/index.html#reference-index)。 要了解某项改变的完整实现和设计理念，请参阅相应新特性的 PEP；但请注意一旦某项特性已完全实现则相应 PEP 通常不会再继续更新。 请参阅 [迁移到 Python 3.13](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#porting-to-python-3-13) 了解如何从较早 Python 进行升级的指导。

------

​	解释器的改进：

- 大幅改进的 [交互式解释器](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#whatsnew313-better-interactive-interpreter) 和 [改进的错误消息](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#whatsnew313-improved-error-messages)。
- [**PEP 667**](https://peps.python.org/pep-0667/): 现在 [`locals()`](https://docs.python.org/zh-cn/3.13/library/functions.html#locals) 内置函数在修改被返回的映射时具有 [已定义语义](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#whatsnew313-locals-semantics)。 Python 调试器及类似的工具现在即使在并发代码执行期间也能更可靠地在已优化的作用域中更新局部变量。
- [**PEP 703**](https://peps.python.org/pep-0703/): CPython 3.13 具有对在运行时禁用 [global interpreter lock](https://docs.python.org/zh-cn/3.13/glossary.html#term-global-interpreter-lock) 的实验性支持。 请参阅 [自由线程 CPython](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#whatsnew313-free-threaded-cpython) 了解详情。
- [**PEP 744**](https://peps.python.org/pep-0744/): 增加了一个基本的 [JIT 编译器](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#whatsnew313-jit-compiler)。 目前默认是禁用的（但以后可能启用）。 能够小幅提升性能 -- 我们预计在接下来的几个发布版中不断改进它。
- 在新的 [交互式解释器](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#whatsnew313-better-interactive-interpreter) 中，以及 [回溯信息](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#whatsnew313-improved-error-messages) 和 [文档测试](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#whatsnew313-doctest) 输出中的颜色支持。 这可以通过 [`PYTHON_COLORS`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHON_COLORS) and [`NO_COLOR`](https://no-color.org/) 环境变量来禁用。

​	对 Python 数据模型的改进：

- [`__static_attributes__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#type.__static_attributes__) 保存了可在一个类体的任何函数中通过 `self.X` 来访问的属性名称。
- [`__firstlineno__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#type.__firstlineno__) 记录了一个类定义的首行的行号。

​	标准库中的重大改进：

- 新增了 [`PythonFinalizationError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#PythonFinalizationError) 异常，当操作在 [最终化](https://docs.python.org/zh-cn/3.13/glossary.html#term-interpreter-shutdown) 期间被阻塞时将被引发。
- 现在 [`argparse`](https://docs.python.org/zh-cn/3.13/library/argparse.html#module-argparse) 模块可支持弃用命令行选项、位置参数和子命令。
- 新增的函数 [`base64.z85encode()`](https://docs.python.org/zh-cn/3.13/library/base64.html#base64.z85encode) 和 [`base64.z85decode()`](https://docs.python.org/zh-cn/3.13/library/base64.html#base64.z85decode) 支持对 [Z85 数据](https://rfc.zeromq.org/spec/32/) 进行编码和解码。
- 现在 [`copy`](https://docs.python.org/zh-cn/3.13/library/copy.html#module-copy) 模块有一个 [`copy.replace()`](https://docs.python.org/zh-cn/3.13/library/copy.html#copy.replace) 函数，支持许多内置类型和任何定义了 [`__replace__()`](https://docs.python.org/zh-cn/3.13/library/copy.html#object.__replace__) 方法的类。
- 新的 [`dbm.sqlite3`](https://docs.python.org/zh-cn/3.13/library/dbm.html#module-dbm.sqlite3) 模块现在是默认的 [`dbm`](https://docs.python.org/zh-cn/3.13/library/dbm.html#module-dbm) 后端。
- [`os`](https://docs.python.org/zh-cn/3.13/library/os.html#module-os) 模块增加了 [一套新函数](https://docs.python.org/zh-cn/3.13/library/os.html#os-timerfd) 用于处理 Linux 的定时器通知文件描述符。
- 现在 [`random`](https://docs.python.org/zh-cn/3.13/library/random.html#module-random) 模块提供了一个 [命令行界面](https://docs.python.org/zh-cn/3.13/library/random.html#random-cli)。

​	安全改进：

- [`ssl.create_default_context()`](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl.create_default_context) 设置了 [`ssl.VERIFY_X509_PARTIAL_CHAIN`](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl.VERIFY_X509_PARTIAL_CHAIN) 和 [`ssl.VERIFY_X509_STRICT`](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl.VERIFY_X509_STRICT) 作为默认的旗标。

​	C API 的改进：

- 现在 [`Py_mod_gil`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.Py_mod_gil) 槽位被用来指明一个扩展模块支持在禁用 [GIL](https://docs.python.org/zh-cn/3.13/glossary.html#term-GIL) 的情况下运行。
- 增加了 [PyTime C API](https://docs.python.org/zh-cn/3.13/c-api/time.html)，提供了对系统时钟的访问。
- [`PyMutex`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyMutex) 是新增的轻量级互斥锁，只占用一个字节。
- 新增了 [一套函数](https://docs.python.org/zh-cn/3.13/c-api/monitoring.html#c-api-monitoring) 用于在 C API 中生成 [**PEP 669**](https://peps.python.org/pep-0669/) 监控事件。

​	新的类型标注特性：

- [**PEP 696**](https://peps.python.org/pep-0696/): 类型形参 ([`typing.TypeVar`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.TypeVar), [`typing.ParamSpec`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.ParamSpec) 和 [`typing.TypeVarTuple`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.TypeVarTuple)) 现在可支持默认值。
- [**PEP 702**](https://peps.python.org/pep-0702/): 新的 [`warnings.deprecated()`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.deprecated) 装饰器在类型系统和运行时中增加了对标记为弃用的支持。
- [**PEP 705**](https://peps.python.org/pep-0705/): [`typing.ReadOnly`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.ReadOnly) 可被用来将 [`typing.TypedDict`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.TypedDict) 的项标记为对类型检查器只读。
- [**PEP 742**](https://peps.python.org/pep-0742/): [`typing.TypeIs`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.TypeIs) 提供了更直观的类型细化行为，作为对 [`typing.TypeGuard`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.TypeGuard) 的替代。

​	平台支持：

- [**PEP 730**](https://peps.python.org/pep-0730/): 现在 Apple 的 iOS 是 [官方支持的平台](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#whatsnew313-platform-support)，处于 [**第 3 层级**](https://peps.python.org/pep-0011/#tier-3)。
- [**PEP 738**](https://peps.python.org/pep-0738/): 现在 Android 是 [官方支持的平台](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#whatsnew313-platform-support)，处于 [**第 3 层级**](https://peps.python.org/pep-0011/#tier-3)。
- 现在 `wasm32-wasi` 作为 [**第 2 层级**](https://peps.python.org/pep-0011/#tier-2) 的平台受到支持。
- `wasm32-emscripten` 不再是受到官方支持的平台。

​	重要的移除：

- [PEP 594](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#whatsnew313-pep594): 剩余的 19 个“死电池”（老旧 stdlib 模块）已从标准库中移除: `aifc`, `audioop`, `cgi`, `cgitb`, `chunk`, `crypt`, `imghdr`, `mailcap`, `msilib`, `nis`, `nntplib`, `ossaudiodev`, `pipes`, `sndhdr`, `spwd`, `sunau`, `telnetlib`, `uu` 和 `xdrlib`。
- 移除了 **2to3** 工具和 `lib2to3` 模块（在 Python 3.11 中已被弃用）。
- 移除了 `tkinter.tix` 模块（在 Python 3.6 中已被弃用）。
- 移除了 `locale.resetlocale()` 函数。
- 移除了 `typing.io` 和 `typing.re` 命名空间。
- 移除了链式的 [`classmethod`](https://docs.python.org/zh-cn/3.13/library/functions.html#classmethod) 描述器。

​	发布计划的变化：

[**PEP 602**](https://peps.python.org/pep-0602/) ("Annual Release Cycle for Python") 已被更新为将新发布版的完整支持 ('bugfix') 期扩展至两年。 这个更新的政策意味着：

- Python 3.9--3.12 有一年半的完整支持，另加三年半的安全修正。
- Python 3.13 及以后的版本有两年的完整支持，另加三年的安全修正。

## 新的特性



### 更好的交互式解释器

​	Python 现在默认会使用新的 [interactive](https://docs.python.org/zh-cn/3.13/glossary.html#term-interactive) shell，它基于来自 [PyPy 项目](https://pypy.org/) 的代码。 当使用从交互式终端启动 [REPL](https://docs.python.org/zh-cn/3.13/glossary.html#term-REPL) 时，下列新特性将受到支持：

- 多行编辑并保留历史记录。
- 对 REPL 专属的命令如 help, exit 和 quit 的直接支持，无需以函数形式调用它们。
- 提示和回溯 [默认启用彩色显示](https://docs.python.org/zh-cn/3.13/using/cmdline.html#using-on-controlling-color)。
- 使用 F1 浏览交互式帮助并带有单独的命令历史。
- 使用 F2 浏览去除了输出以及 [>>>](https://docs.python.org/zh-cn/3.13/glossary.html#term-0) 和 [...](https://docs.python.org/zh-cn/3.13/glossary.html#term-...) 提示符的历史。
- 使用 F3 进入“粘贴模式”以更方便地粘贴大段代码（再次按 F3 返回常规提示符）。

​	要禁用新的交互式 shell，可设置 [`PYTHON_BASIC_REPL`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHON_BASIC_REPL) 环境变量。 有关交互模式的详情，请参见 [交互模式](https://docs.python.org/zh-cn/3.13/tutorial/appendix.html#tut-interac)。

​	（由 Pablo Galindo Salgado, Łukasz Langa 和 Lysandros Nikolaou 在 [gh-111201](https://github.com/python/cpython/issues/111201) 基于来自 PyPy 项目的代码贡献。 Windows 支持由 Dino Viehland 和 Anthony Shaw 贡献。）



### 改进的错误消息

- 在终端里显示回溯时解释器现在会默认使用彩色。 此特性可通过新的 [`PYTHON_COLORS`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHON_COLORS) 环境变量以及传统的 [`NO_COLOR`](https://no-color.org/) 和 [`FORCE_COLOR`](https://force-color.org/) 环境变量来 [进行控制](https://docs.python.org/zh-cn/3.13/using/cmdline.html#using-on-controlling-color)。 （由 Pablo Galindo Salgado 在 [gh-112730](https://github.com/python/cpython/issues/112730) 中贡献。）

- 一个常见错误是撰写的脚本和标准库中的某个模块重名。现在出现此类错误时会显示一条更有用的错误信息：

  ```
  $ python random.py
  Traceback (most recent call last):
    File "/home/me/random.py", line 1, in <module>
      import random
    File "/home/me/random.py", line 3, in <module>
      print(random.randint(5))
            ^^^^^^^^^^^^^^
  AttributeError: module 'random' has no attribute 'randint' (consider renaming '/home/me/random.py' since it has the same name as the standard library module named 'random' and prevents importing that standard library module)
  ```

  类似地，如果一个脚本具有与它尝试导入的第三方模块相同的名称并因此导致错误，我们也会显示一条更有帮助的错误消息：

  ```
  $ python numpy.py
  Traceback (most recent call last):
    File "/home/me/numpy.py", line 1, in <module>
      import numpy as np
    File "/home/me/numpy.py", line 3, in <module>
      np.array([1, 2, 3])
      ^^^^^^^^
  AttributeError: module 'numpy' has no attribute 'array' (consider renaming '/home/me/numpy.py' if it has the same name as a library you intended to import)
  ```

  (由 Shantanu Jain 在 [gh-95754](https://github.com/python/cpython/issues/95754) 中贡献）。

- 现在当向一个函数传入不正确的关键字参数时错误消息会尝试提示正确的关键字参数。

  \>>>

  ```
  >>> "Better error messages!".split(max_split=1)
  Traceback (most recent call last):
    File "<python-input-0>", line 1, in <module>
      "Better error messages!".split(max_split=1)
      ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^
  TypeError: split() got an unexpected keyword argument 'max_split'. Did you mean 'maxsplit'?
  ```

  （由 Pablo Galindo Salgado 和 Shantanu Jain 在 [gh-107944](https://github.com/python/cpython/issues/107944) 中贡献。）



### 自由线程的 CPython

​	现在 CPython 具有对运行于禁用 [global interpreter lock](https://docs.python.org/zh-cn/3.13/glossary.html#term-global-interpreter-lock) (GIL) 的自由线程模式的实验性支持。 这是一个实验性的特性因而默认是不启用的。 自由线程模式需要一个不同的可执行程序，通常名为 `python3.13t` 或 `python3.13t.exe`。 标记为 *free-threaded* 的预构建二进制文件可作为官方 [Windows](https://docs.python.org/zh-cn/3.13/using/windows.html#install-freethreaded-windows) 和 [macOS](https://docs.python.org/zh-cn/3.13/using/mac.html#install-freethreaded-macos) 安装器的一部分被安装，或者可以附带 [`--disable-gil`](https://docs.python.org/zh-cn/3.13/using/configure.html#cmdoption-disable-gil) 选项使用源代码来构建 CPython。

​	自由线程模式的执行允许在可用的 CPU 核心上并行地运行线程从而充分利用可用的处理能力。 虽然并非所有软件都能自动从中受益，但在设计时将线程纳入考虑的程序在多核心硬件上运行速度会更快。 **自由线程模式是实验性的** 并且处于不断改进的过程中：预计会出现一些程序错误并且在单线程场景下出现明显的性能损失。 可以选择使用环境变量 [`PYTHON_GIL`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHON_GIL) 或命令行选项 [`-X gil=1`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-X) 让 CPython 的自由线程构建版支持在运行时启用 GIL。

​	为了检查当前解释器是否支持自由线程，[`python -VV`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-V) 和 [`sys.version`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.version) 将包含 "experimental free-theading build" 字样。 可以使用新增的 `sys._is_gil_enabled()` 函数来检查正在运行的线程是否确实禁用了 GIL。

​	C-API 扩展模块需要针对自由线程构建版专门进行构建。 支持在禁用 [GIL](https://docs.python.org/zh-cn/3.13/glossary.html#term-GIL) 的情况下运行的扩展应当使用 [`Py_mod_gil`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.Py_mod_gil) 槽位。 使用单阶段初始化的扩展应当使用 [`PyUnstable_Module_SetGIL()`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.PyUnstable_Module_SetGIL) 来指明它们是支支持在禁用 GIL 的情况下运行。 导入不使用这些机制的 C 扩展将导致 GIL 被启用，除非通过 [`PYTHON_GIL`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHON_GIL) 环境变量或 [`-X gil=0`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-X) 选项显式地禁用 GIL。 需要 pip 24.1 或更新的版本才能在自由线程构建版中安装带有 C 扩展的软件包。

​	这项工作成为可能要感谢许多个人和组织，包括针对 Python 和第三方项目测试并启用自由线程支持的庞大的贡献者社区。 重要的贡献者包括：Sam Gross, Ken Jin, Donghee Na, Itamar Oren, Matt Page, Brett Simmers, Dino Viehland, Carl Meyer, Nathan Goldbaum, Ralf Gommers, Lysandros Nikolaou 及其他许多人。 有许多贡献者受雇于 Meta，该公司提供了大量的工程资源来支持此项目。

​	参见

 

[**PEP 703**](https://peps.python.org/pep-0703/) "Making the Global Interpreter Lock Optional in CPython" 中包含了有关此项工作的理念和信息。

[Porting Extension Modules to Support Free-Threading](https://py-free-threading.github.io/porting/): 一份由社区维护的针对扩展开发者的移植指南。



### 实验性的即时 (JIT) 编译器

​	当 CPython 使用 `--enable-experimental-jit` 选项进行配置和构建时，会添加一个即时（JIT）编译器以加快某些 Python 程序的运行速度。 在 Windows 上，可使用 `PCbuild/build.bat --experimental-jit` 启用 JIT 或使用 `--experimental-jit-interpreter` 启用第 2 层级解释器。 构建要求和进一步的支持信息 [包含在](https://github.com/python/cpython/blob/main/Tools/jit/README.md) `Tools/jit/README.md` 中。

`--enable-experimental-jit` 选项接受这些（可选）值，如果不带可选值地预设 `--enable-experimental-jit` 则默认为 `yes`。

- `no`: 禁用整个第 2 层级和 JIT 管线。
- `yes`: 启用 JIT。 要在运行时禁用 JIT，则传入环境变量 `PYTHON_JIT=0`。
- `yes-off`: 构建 JIT 但默认禁用它。 要在运行时启用 JIT，则传入环境变量 `PYTHON_JIT=1`。
- `interpreter`: 启用第 2 层级解释器但是禁用 JIT。 可以在运行时传入 `PYTHON_JIT=0` 来禁用该解释器。

​	其内部架构大致如下：

- 我们将从特化的 *第 1 层级字节码* 开始。 请参阅 [3.11 有什么新变化](https://docs.python.org/zh-cn/3.13/whatsnew/3.11.html#whatsnew311-pep659) 了解详情。
- 当第 1 层级字节码达到足够热度，它将被翻译为新的纯内部的中间表示形式 (IR)，称为 *第 2 层级 IR*，有时也称为微操作码 ("uops")。
- 第 2 层级 IR 使用与第 1 层级相同的基于栈的虚拟机，但其指令格式更适合被翻译为机器码。
- 在第 2 层级 IR 被解释或翻译为机器码之前，我们会预先应用一些优化通路。
- 虽然第 2 层级解释器存在，但它主要用于对优化管线的先前阶段进行调试。可通过为 Python 配置 `--enable-experimental-jit=interpreter` 选项启用第 2 层级解释器。
- 启用 JIT 时，经优化的第 2 层级 IR 将被翻译为机器码后再执行。
- 这个机器码翻译过程使用了名为 *拷贝并打补丁* 的技巧。 它没有运行时依赖，但增加了构建时对 LLVM 的依赖。

​	参见

 

[**PEP 744**](https://peps.python.org/pep-0744/)

​	（JIT 来自 Brandt Bucher 且受到 Haoran Xu 和 Fredrik Kjolstad 论文的启发。第 2 层级 IR 来自 Mark Shannon 和 Guido van Rossum。第 2 层级解释器来自 Ken Jin。）



### 针对 [`locals()`](https://docs.python.org/zh-cn/3.13/library/functions.html#locals) 的已定义修改语义

​	在历史上，改变 [`locals()`](https://docs.python.org/zh-cn/3.13/library/functions.html#locals) 的返回值的预期结果是留给具体的 Python 实现来定义的。 从 Python 3.13 开始，[**PEP 667**](https://peps.python.org/pep-0667/) 标准化了 CPython 对于大多数代码执行作用域的历史行为，但也将 [已优化作用域](https://docs.python.org/zh-cn/3.13/glossary.html#term-optimized-scope) (函数、生成器、协程、推导式和生成器表达式) 修改为显式地返回当前已赋值的局部变量的独立快照，包括局部引用的在闭包中捕获的非局部变量。

​	在已优化作用域中对 [`locals()`](https://docs.python.org/zh-cn/3.13/library/functions.html#locals) 语义的这项修改也会影响隐式地以 `locals()` 为目标的代码执行函数的默认行为，如果没有提供显式命名空间的话（例如 [`exec()`](https://docs.python.org/zh-cn/3.13/library/functions.html#exec) 和 [`eval()`](https://docs.python.org/zh-cn/3.13/library/functions.html#eval) 等）。 在之前的版本中，在调用代码执行函数后是否可以通过调用 `locals()` 访问更改情况取决于具体的实现。 具体到 CPython 而言，此类代码通常会按预期工作，但有时可能会在基于其他代码（包括调试器和代码执行跟踪工具）的已优化作用域中失败，因为代码有可能重置该作用域中的共享快照。 现在，代码在已优化作用域中将始终针对局部变量的独立快照运行，因为在后续调用 `locals()` 时将永远看不到更改。 要访问在这些情况下所做的更改，现在必须将一个显式命名空间引用传递给相关的函数。 或者，也可以更新受影响的代码以使用更高层级的代码执行 API 返回结果代码命名空间（例如，当执行磁盘上的 Python 文件时使用 [`runpy.run_path()`](https://docs.python.org/zh-cn/3.13/library/runpy.html#runpy.run_path) 函数）。

​	为确保调试器和类似工具能可靠地更新受到此变化影响的作用域中的局部变量，现在 [`FrameType.f_locals`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame.f_locals) 将返回一个针对此种作用域中的帧的局部变量和在局部引用的非局部变量的直通写入代理对象，而不是返回一个非持续更新的具有规定义的运行时语义的共享 `dict` 实例。

​	请参阅 [**PEP 667**](https://peps.python.org/pep-0667/) 了解详情，包括相关的 C API 更改和弃用。 下文还针对受影响的 [Python API](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#pep667-porting-notes-py) 和 [C API](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#pep667-porting-notes-c) 提供了移植说明。

​	（PEP 和实现由 Mark Shannon 和 Tian Gao 在 [gh-74929](https://github.com/python/cpython/issues/74929) 中贡献。 文档更新由 Guido van Rossum 和 Alyssa Coghlan 提供。）



### 对移动平台的支持

[**PEP 730**](https://peps.python.org/pep-0730/): iOS 现在是 [**PEP 11**](https://peps.python.org/pep-0011/) 所支持的平台，包括第 3 层级的 `arm64-apple-ios` 和 `arm64-apple-ios-simulator` 等目标（分别为2013 年后的 iPhone 和 iPad 设备以及运行于 Apple silicon 硬件的 Xcode iOS 模拟器）。 `x86_64-apple-ios-simulator` （运行于较旧的 `x86_64` 硬件的 Xcode iOS 模拟器）不是第 3 层级的受支持平台，但也将尽可能地支持。 （PEP 撰写及实现由 Russell Keith-Magee 在 [gh-114099](https://github.com/python/cpython/issues/114099) 中贡献。）.)

[**PEP 738**](https://peps.python.org/pep-0738/): Android 现在是 [**PEP 11**](https://peps.python.org/pep-0011/) 所支持的平台，包括位于第 3 层级的 `aarch64-linux-android` 和 `x86_64-linux-android` 等目标。 32 位的目标 `arm-linux-androideabi` 和 `i686-linux-android` 不是第 3 层级的受支持平台，但也将尽可能地支持。 （PEP 撰写及实现由 Malcolm Smith 在 [gh-116622](https://github.com/python/cpython/issues/116622) 中贡献。）

​	参见

 

[**PEP 730**](https://peps.python.org/pep-0730/), [**PEP 738**](https://peps.python.org/pep-0738/)

## 其他语言特性修改

- 编译器现在将从文档字符串的每一行去除共有的前导空格。 这会减少 [字节码缓存](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytecode) 的大小（例如 `.pyc` 文件），例如在 SQLAlchemy 2.0 的 `sqlalchemy.orm.session` 中文件大小将减少约 5%。 这项改变将影响各种使用了文档字符串的工具，如 [`doctest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#module-doctest)。

  \>>>

  ```
  >>> def spam():
  ...     """
  ...         This is a docstring with
  ...           leading whitespace.
  ...
  ...         It even has multiple paragraphs!
  ...     """
  ...
  >>> spam.__doc__
  '\nThis is a docstring with\n  leading whitespace.\n\nIt even has multiple paragraphs!\n'
  ```

  （由 Inada Naoki 在 [gh-81283](https://github.com/python/cpython/issues/81283) 中贡献。）

- 类作用域内的 [标注作用域](https://docs.python.org/zh-cn/3.13/reference/executionmodel.html#annotation-scopes) 现在可以包含 lambda 和推导式。 位于类作用域内的推导式不会内联到其父作用域中。

  ```
  class C[T]:
      type Alias = lambda: T
  ```

  （由 Jelle Zijlstra 在 [gh-109118](https://github.com/python/cpython/issues/109118) 和 [gh-118160](https://github.com/python/cpython/issues/118160) 中贡献。）

- [future 语句](https://docs.python.org/zh-cn/3.13/reference/simple_stmts.html#future) 不再会被 [`__future__`](https://docs.python.org/zh-cn/3.13/library/__future__.html#module-__future__) 模块的相对导入触发，意味着 `from .__future__ import ...` 形式的语句现在只是标准的相对导入，而不会激活任何特殊特性。 （由 Jeremiah Gabriel Pascual 在 [gh-118216](https://github.com/python/cpython/issues/118216) 中贡献。）

- 现在 [`global`](https://docs.python.org/zh-cn/3.13/reference/simple_stmts.html#global) 声明当其被用于 [`else`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#else) 代码块中时也将被允许在 [`except`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#except) 代码块中使用。 在之前版本中这会错误地引发 [`SyntaxError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#SyntaxError)。 （由 Irit Katriel 在 [gh-111123](https://github.com/python/cpython/issues/111123) 中贡献。）

- 增加了新的环境变量 [`PYTHON_FROZEN_MODULES`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHON_FROZEN_MODULES)，它确定冻结模块是否会被导入机制所忽略，等价于 [`-X frozen_modules`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-X) 命令行选项。 （由 Yilei Yang 在 [gh-111374](https://github.com/python/cpython/issues/111374) 中贡献。）

- 通过新的环境变量 [`PYTHON_PERF_JIT_SUPPORT`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHON_PERF_JIT_SUPPORT) 和命令行选项 [`-X perf_jit`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-X) 添加无需 [帧指针](https://en.wikipedia.org/wiki/Call_stack) 即可工作的 [对 perf 性能分析器的支持](https://docs.python.org/zh-cn/3.13/howto/perf_profiling.html#perf-profiling)。 （由 Pablo Galindo 在 [gh-118518](https://github.com/python/cpython/issues/118518) 中贡献。）

- 可通过新的 [`PYTHON_HISTORY`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHON_HISTORY) 环境变量来更改 `.python_history` 文件的位置。 （由 Levi Sabah, Zackery Spytz 和 Hugo van Kemenade 在 [gh-73965](https://github.com/python/cpython/issues/73965) 中贡献。）

- 类新增了一个 [`__static_attributes__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#type.__static_attributes__) 属性。 这由编译器以类属性名称的元组来填充，这些名称是从类体中的任何函数通过 `self.<name>` 来赋值的。 （由 Irit Katriel 在 [gh-115775](https://github.com/python/cpython/issues/115775) 中贡献。）.)

- 编译器现在会在类上创建一个 `__firstlineno__` 属性，其值为类定义第一行的行号。 （由 Serhiy Storchaka 在 [gh-118465](https://github.com/python/cpython/issues/118465) 中贡献。）

- 现在 [`exec()`](https://docs.python.org/zh-cn/3.13/library/functions.html#exec) 和 [`eval()`](https://docs.python.org/zh-cn/3.13/library/functions.html#eval) 内置函数接受以关键字形式传入的 *globals* 和 *locals* 参数。 （由 Raphael Gaschignard 在 [gh-105879](https://github.com/python/cpython/issues/105879) 中贡献。）

- 现在 [`compile()`](https://docs.python.org/zh-cn/3.13/library/functions.html#compile) 内置函数接受一个新的旗标 `ast.PyCF_OPTIMIZED_AST`，它类似于 `ast.PyCF_ONLY_AST` 但区别在于返回的 AST 是根据 *optimize* 参数的值进行优化的。 （由 Irit Katriel 在 [gh-108113](https://github.com/python/cpython/issues/108113) 中贡献。）

- 在 [`property`](https://docs.python.org/zh-cn/3.13/library/functions.html#property) 对象上增加了 [`__name__`](https://docs.python.org/zh-cn/3.13/library/functions.html#property.__name__) 属性。 （由 Eugene Toder 在 [gh-101860](https://github.com/python/cpython/issues/101860) 中贡献。）

- 增加了新的异常 [`PythonFinalizationError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#PythonFinalizationError)，它派生自 [`RuntimeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#RuntimeError)，用于当操作在 [最终化](https://docs.python.org/zh-cn/3.13/glossary.html#term-interpreter-shutdown) 期间被阻塞时发出信号。 下列可调用对象现在将引发 `PythonFinalizationError`，而不是 [`RuntimeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#RuntimeError):

  - [`_thread.start_new_thread()`](https://docs.python.org/zh-cn/3.13/library/_thread.html#thread.start_new_thread)
  - [`os.fork()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.fork)
  - [`os.forkpty()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.forkpty)
  - [`subprocess.Popen`](https://docs.python.org/zh-cn/3.13/library/subprocess.html#subprocess.Popen)

  （由 Victor Stinner 在 [gh-114570](https://github.com/python/cpython/issues/114570) 中贡献。）

- 允许 [`str.replace()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str.replace) 的 *count* 参数为关键字参数。 （由 Hugo van Kemenade 在 [gh-106487](https://github.com/python/cpython/issues/106487) 中贡献。）

- 现在许多函数会对将布尔值作为文件描述符参数发出警告。这可以帮助尽早发现一些错误。（由 Serhiy Storchaka 在 [gh-82626](https://github.com/python/cpython/issues/82626) 中贡献。）

- 为 [`bz2`](https://docs.python.org/zh-cn/3.13/library/bz2.html#module-bz2), [`lzma`](https://docs.python.org/zh-cn/3.13/library/lzma.html#module-lzma), [`tarfile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#module-tarfile) 和 [`zipfile`](https://docs.python.org/zh-cn/3.13/library/zipfile.html#module-zipfile) 等模块中的已压缩和已归档文件型对象添加了 `name` 和 `mode` 属性。 （由 Serhiy Storchaka 在 [gh-115961](https://github.com/python/cpython/issues/115961) 中贡献。）

## 新增模块

- [`dbm.sqlite3`](https://docs.python.org/zh-cn/3.13/library/dbm.html#module-dbm.sqlite3): 针对 [`dbm`](https://docs.python.org/zh-cn/3.13/library/dbm.html#module-dbm) 的 SQLite 后端。 （由 Raymond Hettinger 和 Erlend E. Aasland 在 [gh-100414](https://github.com/python/cpython/issues/100414) 中贡献。）

## 改进的模块

### argparse

- 为 [`add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument) 和 `add_parser()` 方法添加了 *deprecated* 形参，以允许弃用命令行选项、位置参数和子命令。 （由 Serhiy Storchaka 在 [gh-83648](https://github.com/python/cpython/issues/83648) 中贡献。）

### array

- 增加了 `'w'` 类型码 (`Py_UCS4`) 表示 Unicode 字符。 它应被用来代替已弃用的 `'u'` 类型码。 （由 Inada Naoki 在 [gh-80480](https://github.com/python/cpython/issues/80480) 中贡献。）
- 通过实现 [`clear()`](https://docs.python.org/zh-cn/3.13/library/array.html#array.array.clear) 方法将 [`array.array`](https://docs.python.org/zh-cn/3.13/library/array.html#array.array) 注册为 [`MutableSequence`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.MutableSequence)。 （由 Mike Zimin 在 [gh-114894](https://github.com/python/cpython/issues/114894) 中贡献。）

### ast

- 现在 [`ast`](https://docs.python.org/zh-cn/3.13/library/ast.html#module-ast) 模块中节点类型的构造器对其接受的参数要求更为严格，并在参数被省略时有更易理解的行为。

  如果在构造实例时某个 AST 节点上的可选字段没有被作为参数包括在内，则该字段现在将被设为 `None`。 类似地，如果某个列表字段被省略，则该字段现在将被设为空列表，而如果某个 `expr_context` 字段被省略，则它将默认为 [`Load()`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Load)。 （之前，在所有情况下，新构造的 AST 节点实例上的相应属性都将缺失。）

  在所有其他情况下，当需要的参数被省略时，节点构造器将发出 [`DeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#DeprecationWarning)。 这在 Python 3.15 中将会引发异常。 类似地，将关键字参数传入一个未映射到 AST 节点上的字段的构造器的做法现在已被弃用，并且在 Python 3.15 中将会引发异常。

  这些更改将不会应用于用户自定义的 [`ast.AST`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST) 子类，除非该类选择通过设置 [`AST._field_types`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST._field_types) 映射的方式加入新的行为。

  （由 Jelle Zijlstra 在 [gh-105858](https://github.com/python/cpython/issues/105858), [gh-117486](https://github.com/python/cpython/issues/117486) 和 [gh-118851](https://github.com/python/cpython/issues/118851) 中贡献。）

- 现在 [`ast.parse()`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.parse) 接受一个可选参数 *optimize*，它会被传递给 [`compile()`](https://docs.python.org/zh-cn/3.13/library/functions.html#compile)。 这使得获取已优化的 AST 成为可能。 （由 Irit Katriel 在 [gh-108113](https://github.com/python/cpython/issues/108113) 中贡献。）

### asyncio

- 现在 [`asyncio.as_completed()`](https://docs.python.org/zh-cn/3.13/library/asyncio-task.html#asyncio.as_completed) 将返回一个即是 [asynchronous iterator](https://docs.python.org/zh-cn/3.13/glossary.html#term-asynchronous-iterator) 又是基本的产生 [可等待对象](https://docs.python.org/zh-cn/3.13/glossary.html#term-awaitable) 的 [iterator](https://docs.python.org/zh-cn/3.13/glossary.html#term-iterator) 的对象。 由异常迭代产生的可等待对象包括被传入的原始 Task 或 Future 对象，使得将结果与正在完成的任务相关联更为容易。 （由 Justin Arthur 在 [gh-77714](https://github.com/python/cpython/issues/77714) 中贡献。）

- 现在当服务器被关闭时 [`asyncio.loop.create_unix_server()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.loop.create_unix_server) 会自动移除 Unix 套接字。 （由 Pierre Ossman 在 [gh-111246](https://github.com/python/cpython/issues/111246) 中贡献。）

- 现在当附带一个空字节串对象调用时 [`DatagramTransport.sendto()`](https://docs.python.org/zh-cn/3.13/library/asyncio-protocol.html#asyncio.DatagramTransport.sendto) 将发送零长度的数据报。 现在当计算缓冲区大小时传输控制流还会将数据报标头纳入考量。 （由 Jamie Phan 在 [gh-115199](https://github.com/python/cpython/issues/115199) 中贡献。）

- 增加了 [`Queue.shutdown`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.shutdown) 和 [`QueueShutDown`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.QueueShutDown) 用于管理队列终结。 （由 Laurie Opperman 和 Yves Duprat 在 [gh-104228](https://github.com/python/cpython/issues/104228) 中贡献。）

- 增加了 [`Server.close_clients()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.Server.close_clients) 和 [`Server.abort_clients()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.Server.abort_clients) 方法，它们会以更强制的方式关闭 asyncio 服务器。 （由 Pierre Ossman 在 [gh-113538](https://github.com/python/cpython/issues/113538) 中贡献。）

- 在 [`StreamReader.readuntil()`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.StreamReader.readuntil) 中接受一个由分隔符组成的元组，当遇到其中之一时就会停止。 （由 Bruce Merry 在 [gh-81322](https://github.com/python/cpython/issues/81322) 中贡献。）

- 改进了 [`TaskGroup`](https://docs.python.org/zh-cn/3.13/library/asyncio-task.html#asyncio.TaskGroup) 在外部的取消操作与内部的取消操作发生冲突时的行为。 例如，当嵌套两个任务分组并且两者同时在某个子任务中遇到异常时，外层的任务分组有可能被挂起，因为其内部的取消操作已由内层的任务分组进行处理。

  对于任务分组在外部被取消时同时必须引发 [`ExceptionGroup`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ExceptionGroup) 的情况，现在它将调用父任务的 [`cancel()`](https://docs.python.org/zh-cn/3.13/library/asyncio-task.html#asyncio.Task.cancel) 方法。 这样可以确保 [`CancelledError`](https://docs.python.org/zh-cn/3.13/library/asyncio-exceptions.html#asyncio.CancelledError) 会在下一次 [`await`](https://docs.python.org/zh-cn/3.13/reference/expressions.html#await) 时被引发，因此取消操作不会丢失。, so the cancellation is not lost.

  这些更改的一个附加好处是现在任务组会保留取消操作计数 ([`cancelling()`](https://docs.python.org/zh-cn/3.13/library/asyncio-task.html#asyncio.Task.cancelling))。

  为了处理某些边界情况，现在 [`uncancel()`](https://docs.python.org/zh-cn/3.13/library/asyncio-task.html#asyncio.Task.uncancel) 可以在取消操作计数达到零时重置未写入文档的 `_must_cancel` 旗标。

  （受到由 Arthur Tacca 在 [gh-116720](https://github.com/python/cpython/issues/116720) 中报告的问题的启发。）

- 当在一个未激活的 [`TaskGroup`](https://docs.python.org/zh-cn/3.13/library/asyncio-task.html#asyncio.TaskGroup) 上调用 [`TaskGroup.create_task()`](https://docs.python.org/zh-cn/3.13/library/asyncio-task.html#asyncio.TaskGroup.create_task) 时，给定的协程将被关闭 (这将防止引发有关给定的协程从未被等待的 [`RuntimeWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#RuntimeWarning))。 （由 Arthur Tacca 和 Jason Zhang 在 [gh-115957](https://github.com/python/cpython/issues/115957) 中贡献。）

### base64

- 增加了 [`z85encode()`](https://docs.python.org/zh-cn/3.13/library/base64.html#base64.z85encode) 和 [`z85decode()`](https://docs.python.org/zh-cn/3.13/library/base64.html#base64.z85decode) 函数用于将 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 编码为 [Z85 data](https://rfc.zeromq.org/spec/32/) 和将 Z85 编码的数据解码为 `bytes`。 （由 Matan Perelman 在 [gh-75299](https://github.com/python/cpython/issues/75299) 中贡献。）

### compileall

- 工作线程和进程的默认数据现在是使用 [`os.process_cpu_count()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.process_cpu_count) 而不是 [`os.cpu_count()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.cpu_count) 来选择的。 （由 Victor Stinner 在 [gh-109649](https://github.com/python/cpython/issues/109649) 中贡献。）

### concurrent.futures

- 工作线程和进程的默认数据现在是使用 [`os.process_cpu_count()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.process_cpu_count) 而不是 [`os.cpu_count()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.cpu_count) 来选择的。 （由 Victor Stinner 在 [gh-109649](https://github.com/python/cpython/issues/109649) 中贡献。）

### configparser

- 现在 [`ConfigParser`](https://docs.python.org/zh-cn/3.13/library/configparser.html#configparser.ConfigParser) 具有对未命名节的支持，这将允许使用最高层级的键值对。 此特性可通过新增的 *allow_unnamed_section* 形参来启用。 （由 Pedro Sousa Lacerda 在 [gh-66449](https://github.com/python/cpython/issues/66449) 中贡献。）

### copy

- 新增的 [`replace()`](https://docs.python.org/zh-cn/3.13/library/copy.html#copy.replace) 函数和 [`replace 协议`](https://docs.python.org/zh-cn/3.13/library/copy.html#object.__replace__) 使得创建经修改的对象副本更为简单。 这在操作不可变对象时特别有用。 以下类型将支持 [`replace()`](https://docs.python.org/zh-cn/3.13/library/copy.html#copy.replace) 函数并实现了 replace 协议：

  - [`collections.namedtuple()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.namedtuple)
  - [`dataclasses.dataclass`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.dataclass)
  - [`datetime.datetime`](https://docs.python.org/zh-cn/3.13/library/datetime.html#datetime.datetime), [`datetime.date`](https://docs.python.org/zh-cn/3.13/library/datetime.html#datetime.date), [`datetime.time`](https://docs.python.org/zh-cn/3.13/library/datetime.html#datetime.time)
  - [`inspect.Signature`](https://docs.python.org/zh-cn/3.13/library/inspect.html#inspect.Signature), [`inspect.Parameter`](https://docs.python.org/zh-cn/3.13/library/inspect.html#inspect.Parameter)
  - [`types.SimpleNamespace`](https://docs.python.org/zh-cn/3.13/library/types.html#types.SimpleNamespace)
  - [代码对象](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#code-objects)

  任何用户自定义类也可以通过定义 [`__replace__()`](https://docs.python.org/zh-cn/3.13/library/copy.html#object.__replace__) 方法来支持 [`copy.replace()`](https://docs.python.org/zh-cn/3.13/library/copy.html#copy.replace)。 （由 Serhiy Storchaka 在 [gh-108751](https://github.com/python/cpython/issues/108751) 中贡献。）

### ctypes

- 作为必要的内部重构的一个后果，内部元类的初始化现在将发生于 `__init__` 中而不是 `__new__` 中。 这会影响子类化这些内部元类以提供自定义初始化的项目。 一般而言：

  - 调用 `super().__new__` 之后在 `__new__` 中完成的自定义逻辑应当移至 `__init__`。
  - 要创建一个类，需调用相应的元类，而不仅是该元类的 `__new__` 方法。

  请参阅 [gh-124520](https://github.com/python/cpython/issues/124520) 了解相关讨论和对某些受影响项目的修改的链接。

- [`ctypes.Structure`](https://docs.python.org/zh-cn/3.13/library/ctypes.html#ctypes.Structure) objects have a new [`_align_`](https://docs.python.org/zh-cn/3.13/library/ctypes.html#ctypes.Structure._align_) attribute which allows the alignment of the structure being packed to/from memory to be specified explicitly. (Contributed by Matt Sanderson in [gh-112433](https://github.com/python/cpython/issues/112433))

### dbm

- 增加 [`dbm.sqlite3`](https://docs.python.org/zh-cn/3.13/library/dbm.html#module-dbm.sqlite3)，一个实现了 SQLite 后端的新模块，并将其设为默认的 `dbm` 后端。 （由 Raymond Hettinger 和 Erlend E. Aasland 在 [gh-100414](https://github.com/python/cpython/issues/100414) 中贡献。）
- 允许通过新增的 [`gdbm.clear()`](https://docs.python.org/zh-cn/3.13/library/dbm.html#dbm.gnu.gdbm.clear) 和 [`ndbm.clear()`](https://docs.python.org/zh-cn/3.13/library/dbm.html#dbm.ndbm.ndbm.clear) 方法移除数据库中的所有条目。 （由 Donghee Na 在 [gh-107122](https://github.com/python/cpython/issues/107122) 中贡献。）

### dis

- 将 [`dis`](https://docs.python.org/zh-cn/3.13/library/dis.html#module-dis) 模块的函数的输出修改为显示跳转目标和异常处理器的逻辑标签，而不是偏移量。 可以使用新的 [`-O`](https://docs.python.org/zh-cn/3.13/library/dis.html#cmdoption-dis-O) 命令行选项或 *show_offsets* 参数来添加偏移量。 （由 Irit Katriel 在 [gh-112137](https://github.com/python/cpython/issues/112137) 中贡献。）
- [`get_instructions()`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.get_instructions) 不再将缓存条目表示为单独的指令。 作为替代，它会将它们作为 [`Instruction`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.Instruction) 的组成部分返回，放在新的 *cache_info* 字段中。 传给 [`get_instructions()`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.get_instructions) 的 *show_caches* 参数已被弃用并且不再有任何效果。 （由 Irit Katriel 在 [gh-112962](https://github.com/python/cpython/issues/112962) 中贡献。）



### doctest

- 现在 [`doctest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#module-doctest) 输出默认是彩色的。 此特性可通过新增的 [`PYTHON_COLORS`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHON_COLORS) 环境变量和传统的 [`NO_COLOR`](https://no-color.org/) 和 [`FORCE_COLOR`](https://force-color.org/) 环境变量来控制。 另请参阅 [控制颜色](https://docs.python.org/zh-cn/3.13/using/cmdline.html#using-on-controlling-color)。 （由 Hugo van Kemenade 在 [gh-117225](https://github.com/python/cpython/issues/117225) 中贡献。）
- 现在 [`DocTestRunner.run()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner.run) 方法会统计已跳过测试的数量。 增加了 [`DocTestRunner.skips`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner.skips) 和 [`TestResults.skipped`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.TestResults.skipped) 属性。 （由 Victor Stinner 在 [gh-108794](https://github.com/python/cpython/issues/108794) 中贡献。）

### email

- 现在带有嵌入的换行符的标头在输出时会加上引号。 现在 [`generator`](https://docs.python.org/zh-cn/3.13/library/email.generator.html#module-email.generator) 会拒绝序列化（写入）不正确地折叠或分隔的标头，例如将被解析为多个标头或与相邻数据合并的标头等。 如果你需要禁用此安全特性，请设置 [`verify_generated_headers`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.verify_generated_headers)。 （由 Bas Bloemsaat 和 Petr Viktorin 在 [gh-121650](https://github.com/python/cpython/issues/121650) 中贡献。）
- 现在 [`getaddresses()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.getaddresses) 和 [`parseaddr()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.parseaddr) 会在更多遇到无效 email 地址的情况下返回 `('', '')` 对非可能不准确的值。 这两个函数新增了可选的 *strict* 形参 (默认为 `True`)。 要获取旧版本的行为 (接受错误格式的输入)，请使用 `strict=False`。 `getattr(email.utils, 'supports_strict_parsing', False)` 可被用于检查 *strict* 形参是否可用。 （由 Thomas Dwyer 和 Victor Stinner 针对 [gh-102988](https://github.com/python/cpython/issues/102988) 贡献以改进 [**CVE 2023-27043**](https://www.cve.org/CVERecord?id=CVE-2023-27043) 修正。）

### fractions

- 现在 [`Fraction`](https://docs.python.org/zh-cn/3.13/library/fractions.html#fractions.Fraction) 对象支持用于填充、对齐、正负号处理、最小宽度和分组的标准 [格式说明迷你语言](https://docs.python.org/zh-cn/3.13/library/string.html#formatspec) 规则。 （由 Mark Dickinson 在 [gh-111320](https://github.com/python/cpython/issues/111320) 中贡献。）

### glob

- 增加了 [`translate()`](https://docs.python.org/zh-cn/3.13/library/glob.html#glob.translate)，这是个用来将具有 shell 风格通配符的路径说明转换为正则表达式的函数。 （由 Barney Gale 在 [gh-72904](https://github.com/python/cpython/issues/72904) 中贡献。）

### importlib

- 现在 [`importlib.resources`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html#module-importlib.resources) 中的下列函数允许访问资源目录（或树），并使用多个位置参数（现在文本读取函数中的 *encoding* 和 *errors* 参数是仅限关键字参数）：

  - [`is_resource()`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html#importlib.resources.is_resource)
  - [`open_binary()`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html#importlib.resources.open_binary)
  - [`open_text()`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html#importlib.resources.open_text)
  - [`path()`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html#importlib.resources.path)
  - [`read_binary()`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html#importlib.resources.read_binary)
  - [`read_text()`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html#importlib.resources.read_text)

  These functions are no longer deprecated and are not scheduled for removal. (Contributed by Petr Viktorin in [gh-116608](https://github.com/python/cpython/issues/116608).)

- [`contents()`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html#importlib.resources.contents) remains deprecated in favor of the fully-featured [`Traversable`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.abc.html#importlib.resources.abc.Traversable) API. However, there is now no plan to remove it. (Contributed by Petr Viktorin in [gh-116608](https://github.com/python/cpython/issues/116608).)

### io

- 现在 [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 最终化器会使用 [`sys.unraisablehook`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.unraisablehook) 来将由 [`close()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.close) 方法引发的错误写入日志。 在之前版本中，错误在默认情况下会被静默地忽略，而只有在 [Python 开发模式](https://docs.python.org/zh-cn/3.13/library/devmode.html#devmode) 或在使用 [Python 调试构建版](https://docs.python.org/zh-cn/3.13/using/configure.html#debug-build) 时才会被写入日志。 （由 Victor Stinner 在 [gh-62948](https://github.com/python/cpython/issues/62948) 中贡献。）

### ipaddress

- 增加了 [`IPv4Address.ipv6_mapped`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Address.ipv6_mapped) 特征属性，它将返回映射 IPv4 的 IPv6 地址。 （由 Charles Machalow 在 [gh-109466](https://github.com/python/cpython/issues/109466) 中贡献。）
- 修正了 [`IPv4Address`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Address), [`IPv6Address`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv6Address), [`IPv4Network`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Network) 和 [`IPv6Network`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv6Network) 中 `is_global` 和 `is_private` 的行为。 （由 Jakub Stasiak 在 [gh-113171](https://github.com/python/cpython/issues/113171) 中贡献。）

### itertools

- [`batched()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.batched) 新增了 *strict* 形参，它会在最后一批次数据小于指定批准大小时引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。 （由 Raymond Hettinger 在 [gh-113202](https://github.com/python/cpython/issues/113202) 中贡献。）

### marshal

- 在模块函数中增加了 *allow_code* 形参。 传入 `allow_code=False` 将防止在 Python 各版本间不兼容的代码对象的序列化和反序列化。 （由 Serhiy Storchaka 在 [gh-113626](https://github.com/python/cpython/issues/113626) 中贡献。）

### math

- 新增函数 [`fma()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.fma) 可执行合并的乘法-加法运算。 此函数只需一轮操作即可计算 `x * y + z`，从而避免了任何中间步骤导致的精度损失。 它包装了 C99 所提供的 `fma()` 函数，并且遵从针对特殊情况的 IEEE 754 "fusedMultiplyAdd" 运算规范。 （由 Mark Dickinson 和 Victor Stinner 在 [gh-73468](https://github.com/python/cpython/issues/73468) 中贡献。）

### mimetypes

- 增加了 [`guess_file_type()`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.guess_file_type) 函数用于根据文件系统路径来猜测 MIME 类型。 在 [`guess_type()`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.guess_type) 中使用路径的做法现在已是 [soft deprecated](https://docs.python.org/zh-cn/3.13/glossary.html#term-soft-deprecated)。 （由 Serhiy Storchaka 在 [gh-66543](https://github.com/python/cpython/issues/66543) 中贡献。）

### mmap

- 现在 [`mmap`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.mmap) 在 Windows 上当被映射的内存由于文件系统错误或访问限制而不可访问时将获得保护以避免崩溃。 （由 Jannis Weigend 在 [gh-118209](https://github.com/python/cpython/issues/118209) 中贡献。）
- [`mmap`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.mmap) 具有新的 [`seekable()`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.mmap.seekable) 方法将在需要可定位的文件型对象时被使用。 现在 [`seek()`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.mmap.seek) 方法将返回一个新的绝对位置。 （由 Donghee Na 和 Sylvie Liberman 在 [gh-111835](https://github.com/python/cpython/issues/111835) 中贡献。）
- [`mmap`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.mmap) 新增了 UNIX 专属的 *trackfd* 形参用来控制文件描述符的复制；如为假值，则由 *fileno* 指定的文件描述符将不会被复制。 （由 Zackery Spytz 和 Petr Viktorin 在 [gh-78502](https://github.com/python/cpython/issues/78502) 中贡献。）

### multiprocessing

- 工作线程和进程的默认数据现在是使用 [`os.process_cpu_count()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.process_cpu_count) 而不是 [`os.cpu_count()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.cpu_count) 来选择的。 （由 Victor Stinner 在 [gh-109649](https://github.com/python/cpython/issues/109649) 中贡献。）

### os

- 增加了 [`process_cpu_count()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.process_cpu_count) 函数用于获取当前进程的调用方线程可以使用的逻辑 CPU 核心数量。 （由 Victor Stinner 在 [gh-109649](https://github.com/python/cpython/issues/109649) 中贡献。）
- [`cpu_count()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.cpu_count) 和 [`process_cpu_count()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.process_cpu_count) 可通过新的环境变量 [`PYTHON_CPU_COUNT`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHON_CPU_COUNT) 或新的命令行选项 [`-X cpu_count`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-X) 来覆盖。 此选项对于需要在不修改应用程序代码或容器本身的情况下限制一个容器系统的 CPU 资源的用户会很有用处。 （由 Donghee Na 在 [gh-109595](https://github.com/python/cpython/issues/109595) 中贡献。）
- 通过 [`timerfd_create()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.timerfd_create), [`timerfd_settime()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.timerfd_settime), [`timerfd_settime_ns()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.timerfd_settime_ns), [`timerfd_gettime()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.timerfd_gettime), [`timerfd_gettime_ns()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.timerfd_gettime_ns), [`TFD_NONBLOCK`](https://docs.python.org/zh-cn/3.13/library/os.html#os.TFD_NONBLOCK), [`TFD_CLOEXEC`](https://docs.python.org/zh-cn/3.13/library/os.html#os.TFD_CLOEXEC), [`TFD_TIMER_ABSTIME`](https://docs.python.org/zh-cn/3.13/library/os.html#os.TFD_TIMER_ABSTIME) 和 [`TFD_TIMER_CANCEL_ON_SET`](https://docs.python.org/zh-cn/3.13/library/os.html#os.TFD_TIMER_CANCEL_ON_SET) 增加了针对 Linux 的 *[计算器文件描述符](https://manpages.debian.org/timerfd_create(2))* 的 [低层级接口](https://docs.python.org/zh-cn/3.13/library/os.html#os-timerfd)。 （由 Masaru Tsuchiyama 在 [gh-108277](https://github.com/python/cpython/issues/108277) 中贡献。）
- 在 Windows 上现在同时增加了对 [`lchmod()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.lchmod) 和 [`chmod()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.chmod) 的 *follow_symlinks* 参数的支持。 请注意在 Windows 上 `lchmod()` 中的 *follow_symlinks* 的默认值为 `False`。 （由 Serhiy Storchaka 在 [gh-59616](https://github.com/python/cpython/issues/59616) 中贡献。）
- 在 Windows 上现在同时增加了 [`fchmod()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.fchmod) 和对 [`chmod()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.chmod) 中的文件描述符的支持。 （由 Serhiy Storchaka 在 [gh-113191](https://github.com/python/cpython/issues/113191) 中贡献。）
- 在 Windows 上，[`mkdir()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.mkdir) 和 [`makedirs()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.makedirs) 现在支持传入 *mode* 值 `0o700` 以对新目录应用访问控制。 这会隐式地影响 [`tempfile.mkdtemp()`](https://docs.python.org/zh-cn/3.13/library/tempfile.html#tempfile.mkdtemp) 并可缓解 [**CVE 2024-4030**](https://www.cve.org/CVERecord?id=CVE-2024-4030)。 其他的 *mode* 值仍然会被忽略。 （由 Steve Dower 在 [gh-118486](https://github.com/python/cpython/issues/118486) 中贡献。）
- 现在 [`posix_spawn()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.posix_spawn) 可接受 `None` 作为 *env* 参数，这将让新产生的进程使用当前进程的环境。 （由 Jakub Kulik 在 [gh-113119](https://github.com/python/cpython/issues/113119) 中贡献。）.)
- 在支持 `posix_spawn_file_actions_addclosefrom_np()` 的平台上 [`posix_spawn()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.posix_spawn) 现在可以在 *file_actions* 形参中使用 [`POSIX_SPAWN_CLOSEFROM`](https://docs.python.org/zh-cn/3.13/library/os.html#os.POSIX_SPAWN_CLOSEFROM) 属性。 （由 Jakub Kulik 在 [gh-113117](https://github.com/python/cpython/issues/113117) 中贡献。）.)

### os.path

- 增加了 [`isreserved()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.isreserved) 用于检查一个路径在当前系统中是否为保留路径。 此函数仅在 Windows 上可用。 （由 Barney Gale 在 [gh-88569](https://github.com/python/cpython/issues/88569) 中贡献。）
- 在 Windows 上，[`isabs()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.isabs) 将不再把以恰好一个斜杠 (`\` 或 `/`) 开头的路径视为绝对路径。 （由 Barney Gale 和 Jon Foster 在 [gh-44626](https://github.com/python/cpython/issues/44626) 中贡献。）
- 现在即使文件不可访问 [`realpath()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.realpath) 也能够解析 MS-DOS 风格的文件名。 （由 Moonsik Park 在 [gh-82367](https://github.com/python/cpython/issues/82367) 中贡献。）

### pathlib

- 增加了 [`UnsupportedOperation`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.UnsupportedOperation)，它会在一个路径操作不受支持时代替 [`NotImplementedError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#NotImplementedError) 被引发。 （由 Barney Gale 在 [gh-89812](https://github.com/python/cpython/issues/89812) 中贡献。）
- 新增了一个用于根据 'file' URI (`file:///`) 来创建 [`Path`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.Path) 对象的构造器 [`Path.from_uri()`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.Path.from_uri)。 （由 Barney Gale 在 [gh-107465](https://github.com/python/cpython/issues/107465) 中贡献。）
- 增加了 [`PurePath.full_match()`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.PurePath.full_match) 用于匹配带有 shell 风格通配符的路径，包括递归通配符 "`**`"。 （由 Barney Gale 在 [gh-73435](https://github.com/python/cpython/issues/73435) 中贡献。）
- 增加了 [`PurePath.parser`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.PurePath.parser) 类属性以存储用于低层级路径解析与合并的 [`os.path`](https://docs.python.org/zh-cn/3.13/library/os.path.html#module-os.path) 实现。 这可以是 `posixpath` 或 `ntpath`。
- 为 [`Path.glob()`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.Path.glob) 和 [`rglob()`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.Path.rglob) 增加了 *recurse_symlinks* 仅限关键字参数。 （由 Barney Gale 在 [gh-77609](https://github.com/python/cpython/issues/77609) 中贡献。）
- 现在当给出以 "`**`" 结束的模式时 [`Path.glob()`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.Path.glob) 和 [`rglob()`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.Path.rglob) 将返回文件和目录。 在之前版本中，仅会返回目录。 （由 Barney Gale 在 [gh-70303](https://github.com/python/cpython/issues/70303) 中贡献。）.)
- 为 [`Path.is_file`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.Path.is_file), [`Path.is_dir`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.Path.is_dir), [`Path.owner()`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.Path.owner) 和 [`Path.group()`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.Path.group) 增加了 *follow_symlinks* 仅限关键字参数。 （由 Barney Gale 在 [gh-105793](https://github.com/python/cpython/issues/105793) 中，以及 Kamil Turek 在 [gh-107962](https://github.com/python/cpython/issues/107962) 中贡献。）

### pdb

- 现在 [`breakpoint()`](https://docs.python.org/zh-cn/3.13/library/functions.html#breakpoint) 和 [`set_trace()`](https://docs.python.org/zh-cn/3.13/library/pdb.html#pdb.set_trace) 会立即进入调试器而不是在被执行代码的下一行进入。 这一更改可防止当 `breakpoint()` 位于上下文末尾 时调试器在上下文以外被中断。 （由 Tian Gao 在 [gh-118579](https://github.com/python/cpython/issues/118579) 中贡献。）
- 当设置了 [`sys.flags.safe_path`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.flags.safe_path) 时 `sys.path[0]` 将不会再被替换为被调试脚本的目录。 （由 Tian Gao 和 Christian Walther 在 [gh-111762](https://github.com/python/cpython/issues/111762) 中贡献。）
- 现在支持将 [`zipapp`](https://docs.python.org/zh-cn/3.13/library/zipapp.html#module-zipapp) 作为调试目标。 （由 Tian Gao 在 [gh-118501](https://github.com/python/cpython/issues/118501) 中贡献。）
- 添加了在 [`pm()`](https://docs.python.org/zh-cn/3.13/library/pdb.html#pdb.pm) 中进行事后调试期间使用 Pdb 新增的 [`exceptions [exc_number\]`](https://docs.python.org/zh-cn/3.13/library/pdb.html#pdbcommand-exceptions) 命令在串连的异常之间移动的能力。 （由 Matthias Bussonnier 在 [gh-106676](https://github.com/python/cpython/issues/106676) 中贡献。）
- 以一条 pdb 命令打头的表达式和语句现在会被正确地标识并执行。 （由 Tian Gao 在 [gh-108464](https://github.com/python/cpython/issues/108464) 中贡献。）

### queue

- 增加了 [`Queue.shutdown`](https://docs.python.org/zh-cn/3.13/library/queue.html#queue.Queue.shutdown) 和 [`ShutDown`](https://docs.python.org/zh-cn/3.13/library/queue.html#queue.ShutDown) 用于管理队列的终结。 （由 Laurie Opperman 和 Yves Duprat 在 [gh-104750](https://github.com/python/cpython/issues/104750) 中贡献。）

### random

- 增加了一个 [命令行接口](https://docs.python.org/zh-cn/3.13/library/random.html#random-cli)。 （由 Hugo van Kemenade 在 [gh-118131](https://github.com/python/cpython/issues/118131) 中贡献。）

### re

- 将 `re.error` 重命名为 [`PatternError`](https://docs.python.org/zh-cn/3.13/library/re.html#re.PatternError) 以改善准确性。 `re.error` 仍被保留用于向下兼容。

### shutil

- 在 [`chown()`](https://docs.python.org/zh-cn/3.13/library/shutil.html#shutil.chown) 中增加了对 *dir_fd* 和 *follow_symlinks* 关键字参数的支持。 （由 Berker Peksag 和 Tahia K 在 [gh-62308](https://github.com/python/cpython/issues/62308) 中贡献。）

### site

- 现在 `.pth` 文件将先使用 UTF-8 来解码，如果 UTF-8 解码失败再使用 [locale encoding](https://docs.python.org/zh-cn/3.13/glossary.html#term-locale-encoding)。 （由 Inada Naoki 在 [gh-117802](https://github.com/python/cpython/issues/117802) 中贡献。）

### sqlite3

- 现在当一个 [`Connection`](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#sqlite3.Connection) 对象未被显式地 [`关闭`](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#sqlite3.Connection.close) 时将发出 [`ResourceWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ResourceWarning)。 （由 Erlend E. Aasland 在 [gh-105539](https://github.com/python/cpython/issues/105539) 中贡献。）
- 为 [`Connection.iterdump()`](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#sqlite3.Connection.iterdump) 增加了 *filter* 仅限关键字形参用于过滤要转储的数据库对象。 （由 Mariusz Felisiak 在 [gh-91602](https://github.com/python/cpython/issues/91602) 中贡献。）

### ssl

- 现在 [`create_default_context()`](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl.create_default_context) API 将在其默认旗标中包括 [`VERIFY_X509_PARTIAL_CHAIN`](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl.VERIFY_X509_PARTIAL_CHAIN) 和 [`VERIFY_X509_STRICT`](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl.VERIFY_X509_STRICT)。

  ​	备注

   

  [`VERIFY_X509_STRICT`](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl.VERIFY_X509_STRICT) 可能会拒绝下层 OpenSSL 实现本来会接受的 [**RFC 5280**](https://datatracker.ietf.org/doc/html/rfc5280.html) 以前的证书或格式错误的证书。 虽然不建议禁用此功能，但你可以使用以下方式禁用它：

  ```
  import ssl
  
  ctx = ssl.create_default_context()
  ctx.verify_flags &= ~ssl.VERIFY_X509_STRICT
  ```

  （由 William Woodruff 在 [gh-112389](https://github.com/python/cpython/issues/112389) 贡献。）

### statistics

- 增加了用于核密度估计的 [`kde()`](https://docs.python.org/zh-cn/3.13/library/statistics.html#statistics.kde)。 这使得根据固定数量的离散样本估计连续概率密度函数成为可能。 （由 Raymond Hettinger 在 [gh-115863](https://github.com/python/cpython/issues/115863) 中贡献。）
- 增加了 [`kde_random()`](https://docs.python.org/zh-cn/3.13/library/statistics.html#statistics.kde_random) 用来从 [`kde()`](https://docs.python.org/zh-cn/3.13/library/statistics.html#statistics.kde) 创建的估计概率密度函数进行取样。 （由 Hettinger 在 [gh-115863](https://github.com/python/cpython/issues/115863) 中贡献。）



### subprocess

- 现在 [`subprocess`](https://docs.python.org/zh-cn/3.13/library/subprocess.html#module-subprocess) 模块会在更多场合下使用 [`posix_spawn()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.posix_spawn) 函数。

  需要注意的是，当 *close_fds* 为 `True` 时（默认值），则将在 C 库提供了 `posix_spawn_file_actions_addclosefrom_np()` 时使用 [`posix_spawn()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.posix_spawn)，这包括近期的 Linux, FreeBSD 和 Solaris 版本。 在 Linux，其性能应当与现有的 Linux `vfork()` 基础代码类似。

  如果你需要强制 [`subprocess`](https://docs.python.org/zh-cn/3.13/library/subprocess.html#module-subprocess) 绝不使用 [`posix_spawn()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.posix_spawn) 可以将私有的控制节点 `subprocess._USE_POSIX_SPAWN` 设为 `False`。 如果你这样设置的话请在 [issue tracker](https://docs.python.org/zh-cn/3.13/bugs.html#using-the-tracker) 中报告你的理由和平台相关的细节以便我们能够为大家改进 API 的选择逻辑。 （由 Jakub Kulik 在 [gh-113117](https://github.com/python/cpython/issues/113117) 中贡献。）

### sys

- 增加了 [`_is_interned()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys._is_interned) 函数用于检测字符串是否被内部化。 此函数不保证在所有的 Python 实现中均存在。 （由 Serhiy Storchaka 在 [gh-78573](https://github.com/python/cpython/issues/78573) 中贡献。）

### tempfile

- 在 Windows 上，[`tempfile.mkdtemp()`](https://docs.python.org/zh-cn/3.13/library/tempfile.html#tempfile.mkdtemp) 所使用的默认模式 `0o700` 由于 [`os.mkdir()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.mkdir) 的更改现在将限制对新目录的访问。 这是对 [**CVE 2024-4030**](https://www.cve.org/CVERecord?id=CVE-2024-4030) 的缓解措施。 （由 Steve Dower 在 [gh-118486](https://github.com/python/cpython/issues/118486) 中贡献。）

### time

- 在 Windows 上，[`monotonic()`](https://docs.python.org/zh-cn/3.13/library/time.html#time.monotonic) 现在将使用精度为 1 微秒的 `QueryPerformanceCounter()` 时钟，而不是精度只有 15.6 毫秒的 `GetTickCount64()` 时钟。 （由 Victor Stinner 在 [gh-88494](https://github.com/python/cpython/issues/88494) 中贡献。）
- 在 Windows 上，[`time()`](https://docs.python.org/zh-cn/3.13/library/time.html#time.time) 现在将使用精度为 1 微秒的 `GetSystemTimePreciseAsFileTime()` 时钟，代替精度为 15.6 毫秒的 `GetSystemTimeAsFileTime()` 时钟。 （由 Victor Stinner 在 [gh-63207](https://github.com/python/cpython/issues/63207) 中贡献。）

### tkinter

- 增加了 [`tkinter`](https://docs.python.org/zh-cn/3.13/library/tkinter.html#module-tkinter) 控件方法: `tk_busy_hold()`, `tk_busy_configure()`, `tk_busy_cget()`, `tk_busy_forget()`, `tk_busy_current()` 和 `tk_busy_status()`。 （由 Miguel, klappnase 和 Serhiy Storchaka 在 [gh-72684](https://github.com/python/cpython/issues/72684) 中贡献。）
- 现在 [`tkinter`](https://docs.python.org/zh-cn/3.13/library/tkinter.html#module-tkinter) 控件 `wm_attributes()` 接受不带负号前缀的属性名称来获取窗口属性，例如 `w.wm_attributes('alpha')` 并允许指定属性和值以关键字参数形式来设置，例如 `w.wm_attributes(alpha=0.5)`。 （由 Serhiy Storchaka 在 [gh-43457](https://github.com/python/cpython/issues/43457) 中贡献。）
- 通过使用新的可选关键字形参 *return_python_dict*，现在 `wm_attributes()` 可将属性作为 [`dict`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#dict) 返回。 （由 Serhiy Storchaka 在 [gh-43457](https://github.com/python/cpython/issues/43457) 中贡献。）
- 现在当使用新的可选仅限关键字形参 *return_ints* 时 `Text.count()` 可以返回一个简单的 [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int)。 在其他情况下，将以 1 个元素的元组形式返回单个计数值或者 `None`。 （由 Serhiy Storchaka 在 [gh-97928](https://github.com/python/cpython/issues/97928) 中贡献。）
- 在 [`tkinter.ttk.Style`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Style) 的 [`element_create()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Style.element_create) 方法中增加了对 "vsapi" 元素类型的支持。 （由 Serhiy Storchaka 在 [gh-68166](https://github.com/python/cpython/issues/68166) 中贡献。）
- 为 Tkinter 的控件增加了 `after_info()` 方法。 （由 Cheryl Sabella 在 [gh-77020](https://github.com/python/cpython/issues/77020) 中贡献。）
- 为 `PhotoImage` 新增 `copy_replace()` 方法用于将一个图像的某个区域拷贝到另一个图像，可能带有像素缩放、子采样，或两者皆有。 （由 Serhiy Storchaka 在 [gh-118225](https://github.com/python/cpython/issues/118225) 中贡献。）
- 为 `PhotoImage` 的方法 `copy()`, `zoom()` 和 `subsample()` 增加了 *from_coords* 形参。 为 `PhotoImage` 的方法 `copy()` 增加了 *zoom* 和 *subsample* 形参。 （由 Serhiy Storchaka 在 [gh-118225](https://github.com/python/cpython/issues/118225) 中贡献。）
- 增加了 `PhotoImage` 方法 `read()` 用于从文件读取图像以及 `data()` 用于获取图像数据。 为 `write()` 方法增加了 *background* 和 *grayscale* 形参。 （由 Serhiy Storchaka 在 [gh-118271](https://github.com/python/cpython/issues/118271) 中贡献。）.)

### 回溯

- 为 [`TracebackException`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.TracebackException) 增加了 [`exc_type_str`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.TracebackException.exc_type_str) 属性，它用于保存 *exc_type* 字符串表示。 弃用了 [`exc_type`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.TracebackException.exc_type) 属性，它用于保存类型对象本身。 增加了 *save_exc_type* 形参 (默认值为 `True`) 用于指明 `exc_type` 是否应当被保存。 （由 Irit Katriel 在 [gh-112332](https://github.com/python/cpython/issues/112332) 中贡献。）
- 为 [`TracebackException.format_exception_only()`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.TracebackException.format_exception_only) 增加了新的 *show_group* 仅限关键字形参用于（递归地）格式化 [`BaseExceptionGroup`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BaseExceptionGroup) 实例中嵌套的异常。 （由 Irit Katriel 在 [gh-105292](https://github.com/python/cpython/issues/105292) 中贡献。）

### types

- 现在 [`SimpleNamespace`](https://docs.python.org/zh-cn/3.13/library/types.html#types.SimpleNamespace) 可以接受单个位置参数来初始化命名空间的各个参数值。 该参数必须为映射或键值对的可迭代对象。 （由 Serhiy Storchaka 在 [gh-108191](https://github.com/python/cpython/issues/108191) 中贡献。）

### typing

- [**PEP 705**](https://peps.python.org/pep-0705/): 增加 [`ReadOnly`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.ReadOnly)，一个针对类型检查器的特殊类型结构，用于将 [`TypedDict`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.TypedDict) 的项标记为只读。
- [**PEP 742**](https://peps.python.org/pep-0742/): 增加 [`TypeIs`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.TypeIs)，一个可被用于指示类型检查器如何细化类型的类型结构。
- 增加 [`NoDefault`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.NoDefault)，一个用于代表 [`typing`](https://docs.python.org/zh-cn/3.13/library/typing.html#module-typing) 模块中某些形参的默认值的哨兵对象。 （由 Jelle Zijlstra 在 [gh-116126](https://github.com/python/cpython/issues/116126) 中贡献。）
- 增加 [`get_protocol_members()`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.get_protocol_members) 用于返回定义一个 [`typing.Protocol`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.Protocol) 的成员的集合。 （由 Jelle Zijlstra 在 [gh-104873](https://github.com/python/cpython/issues/104873) 中贡献。）
- 增加 [`is_protocol()`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.is_protocol) 用于检查一个类是否属于 [`Protocol`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.Protocol)。 （由 Jelle Zijlstra 在 [gh-104873](https://github.com/python/cpython/issues/104873) 中贡献。）
- 现在 [`ClassVar`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.ClassVar) 可以被嵌套在 [`Final`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.Final) 中，反之亦然。 （由 Mehdi Drissi 在 [gh-89547](https://github.com/python/cpython/issues/89547) 中贡献。）

### unicodedata

- 将 Unicode 数据库更新到 [15.1.0 版](https://www.unicode.org/versions/Unicode15.1.0/)。 （由 James Gerity 在 [gh-109559](https://github.com/python/cpython/issues/109559) 中贡献。）

### venv

- 增加了对在虚拟环境目录中添加源码控制管理 (SCM) 忽略文件的支持。 在默认情况下，Git 已受到支持。 此特性是以可被扩展为支持其他 SCM 的通过 API 选择启用 ([`EnvBuilder`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder) 和 [`create()`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.create))，并通过 CLI 使用 `--without-scm-ignore-files` 选择禁用的方式实现的。 （由 Brett Cannon 在 [gh-108125](https://github.com/python/cpython/issues/108125) 中贡献。）

### warnings

- [**PEP 702**](https://peps.python.org/pep-0702/): 新的 [`warnings.deprecated()`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.deprecated) 装饰器提供了一种将弃用消息传送给 [static type checker](https://docs.python.org/zh-cn/3.13/glossary.html#term-static-type-checker) 并在使用已弃用的类和函数时发出警告的方式。 当被装饰的函数或类在运行时被使用时也可以发出 [`DeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#DeprecationWarning)。 （由 Jelle Zijlstra 在 [gh-104003](https://github.com/python/cpython/issues/104003) 中贡献。）

### xml

- 通过添加五个新方法来允许控制 Expat >=2.6.0 重解析延迟 ([**CVE 2023-52425**](https://www.cve.org/CVERecord?id=CVE-2023-52425))：

  - [`xml.etree.ElementTree.XMLParser.flush()`](https://docs.python.org/zh-cn/3.13/library/xml.etree.elementtree.html#xml.etree.ElementTree.XMLParser.flush)
  - [`xml.etree.ElementTree.XMLPullParser.flush()`](https://docs.python.org/zh-cn/3.13/library/xml.etree.elementtree.html#xml.etree.ElementTree.XMLPullParser.flush)
  - [`xml.parsers.expat.xmlparser.GetReparseDeferralEnabled()`](https://docs.python.org/zh-cn/3.13/library/pyexpat.html#xml.parsers.expat.xmlparser.GetReparseDeferralEnabled)
  - [`xml.parsers.expat.xmlparser.SetReparseDeferralEnabled()`](https://docs.python.org/zh-cn/3.13/library/pyexpat.html#xml.parsers.expat.xmlparser.SetReparseDeferralEnabled)
  - `xml.sax.expatreader.ExpatParser.flush()`

  （由 Sebastian Pipping 在 [gh-115623](https://github.com/python/cpython/issues/115623) 中贡献。）

- 为 [`iterparse()`](https://docs.python.org/zh-cn/3.13/library/xml.etree.elementtree.html#xml.etree.ElementTree.iterparse) 所返回的迭代器增加了 `close()` 方法用于执行显式的清理。 （由 Serhiy Storchaka 在 [gh-69893](https://github.com/python/cpython/issues/69893) 中贡献。）

### zipimport

- 增加了对 [ZIP64](https://en.wikipedia.org/wiki/Zip_(file_format)#ZIP64) 格式的文件的支持。 大家都喜欢更庞大的数据，对吧？ （由 Tim Hatch 在 [gh-94146](https://github.com/python/cpython/issues/94146) 中贡献。）.)

## 性能优化

- 一些标准库模块的导入时间得到了显著改善。 例如，[`typing`](https://docs.python.org/zh-cn/3.13/library/typing.html#module-typing) 模块的导入时间通过移除对 [`re`](https://docs.python.org/zh-cn/3.13/library/re.html#module-re) 和 [`contextlib`](https://docs.python.org/zh-cn/3.13/library/contextlib.html#module-contextlib) 的依赖而减少了大约三分之一。 其他获得导入时间加速的模块包括 [`email.utils`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#module-email.utils), [`enum`](https://docs.python.org/zh-cn/3.13/library/enum.html#module-enum), [`functools`](https://docs.python.org/zh-cn/3.13/library/functools.html#module-functools), [`importlib.metadata`](https://docs.python.org/zh-cn/3.13/library/importlib.metadata.html#module-importlib.metadata) 和 [`threading`](https://docs.python.org/zh-cn/3.13/library/threading.html#module-threading)。 （由 Alex Waygood, Shantanu Jain, Adam Turner, Daniel Hollas 等人在 [gh-109653](https://github.com/python/cpython/issues/109653) 中贡献。）
- 现在对于大量输入 [`textwrap.indent()`](https://docs.python.org/zh-cn/3.13/library/textwrap.html#textwrap.indent) 相比之前可提速大约 30%。 （由 Inada Naoki 在 [gh-107369](https://github.com/python/cpython/issues/107369) 中贡献。）
- 现在 [`subprocess`](https://docs.python.org/zh-cn/3.13/library/subprocess.html#module-subprocess) 模块会在更多场合下使用 [`posix_spawn()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.posix_spawn) 函数，包括在许多现代系统平台上当 *close_fds* 为 `True` (默认值) 的时候。 当在 FreeBSD 和 Solaris 上启动进程时这应该能提供显著的性能提升。 请参阅上面的 [subprocess](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#whatsnew313-subprocess) 小节了解详情。 （由 Jakub Kulik 在 [gh-113117](https://github.com/python/cpython/issues/113117) 中贡献。）

## 被移除的模块与 API



### PEP 594: 从标准库中移除“死电池”

[**PEP 594**](https://peps.python.org/pep-0594/) 提议从标准库移除 19 个模块，它们因其古旧、过时或不安全的状态而被非正式地称呼为‘死电池’。 下列所有模块在 Python 3.11 中被弃用，现在已被移除：

- `aifc`

- `audioop`

- `chunk`

- `cgi` 和 `cgitb`

  - 对于 `GET` 和 `HEAD` 请求 `cgi.FieldStorage` 通常可以用 [`urllib.parse.parse_qsl()`](https://docs.python.org/zh-cn/3.13/library/urllib.parse.html#urllib.parse.parse_qsl) 来替换，而对于 `POST` 和 `PUT` 请求则可以用 [`email.message`](https://docs.python.org/zh-cn/3.13/library/email.message.html#module-email.message) 模块或 [multipart](https://pypi.org/project/multipart/) 库。

  - `cgi.parse()` 可被替换为在想要的查询字符串上直接调用 [`urllib.parse.parse_qs()`](https://docs.python.org/zh-cn/3.13/library/urllib.parse.html#urllib.parse.parse_qs)，除非输入为 `multipart/form-data`，它应当如下文针对 `cgi.parse_multipart()` 所描述的那样被替换。

  - `cgi.parse_header()` 可被 [`email`](https://docs.python.org/zh-cn/3.13/library/email.html#module-email) 包中的功能所替换，它实现了相同的 MIME RFC。 例如，使用 [`email.message.EmailMessage`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage):

    ```
    from email.message import EmailMessage
    
    msg = EmailMessage()
    msg['content-type'] = 'application/json; charset="utf8"'
    main, params = msg.get_content_type(), msg['content-type'].params
    ```

  - `cgi.parse_multipart()` can be replaced with the functionality in the [`email`](https://docs.python.org/zh-cn/3.13/library/email.html#module-email) package, which implements the same MIME RFCs, or with the [multipart](https://pypi.org/project/multipart/) library. For example, the [`email.message.EmailMessage`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage) and [`email.message.Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) classes.

- `crypt` and the private `_crypt` extension. The [`hashlib`](https://docs.python.org/zh-cn/3.13/library/hashlib.html#module-hashlib) module may be an appropriate replacement when simply hashing a value is required. Otherwise, various third-party libraries on PyPI are available:

  - [bcrypt](https://pypi.org/project/bcrypt/): 用于软件和服务器的现代密码哈希算法。
  - [passlib](https://pypi.org/project/passlib/): 支持超过 over 30 种方案的综合密码哈希算法框架。
  - [argon2-cffi](https://pypi.org/project/argon2-cffi/): 安全的 Argon2 密码哈希算法。
  - [legacycrypt](https://pypi.org/project/legacycrypt/): 针对 POSIX 加密库调用和相关功能的 [`ctypes`](https://docs.python.org/zh-cn/3.13/library/ctypes.html#module-ctypes) 包装器。
  - [crypt_r](https://pypi.org/project/crypt_r/): `crypt` 模块的分叉，针对 *[crypt_r(3)](https://manpages.debian.org/crypt_r(3))* 库调用和相关功能和包装器。

- `imghdr`: The [filetype](https://pypi.org/project/filetype/), [puremagic](https://pypi.org/project/puremagic/), or [python-magic](https://pypi.org/project/python-magic/) libraries should be used as replacements. For example, the `puremagic.what()` function can be used to replace the `imghdr.what()` function for all file formats that were supported by `imghdr`.

- `mailcap`: 改用 [`mimetypes`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#module-mimetypes) 模块。

- `msilib`

- `nis`

- `nntplib`: 改用 PyPI 上的 [pynntp](https://pypi.org/project/pynntp/) 库。

- `ossaudiodev`: 对于音频回放，改用 PyPI 上的 [pygame](https://pypi.org/project/pygame/) 库。

- `pipes`: Use the [`subprocess`](https://docs.python.org/zh-cn/3.13/library/subprocess.html#module-subprocess) module instead. Use [`shlex.quote()`](https://docs.python.org/zh-cn/3.13/library/shlex.html#shlex.quote) to replace the undocumented `pipes.quote` function.

- `sndhdr`: 应当使用 [filetype](https://pypi.org/project/filetype/), [puremagic](https://pypi.org/project/puremagic/) 或 [python-magic](https://pypi.org/project/python-magic/) 库作为替代。

- `spwd`: 改用 PyPI 上的 [python-pam](https://pypi.org/project/python-pam/) 库。

- `sunau`

- `telnetlib`，改用 PyPI 上的 [telnetlib3](https://pypi.org/project/telnetlib3/) 或 [Exscript](https://pypi.org/project/Exscript/) 库。

- `uu`: 改用 the [`base64`](https://docs.python.org/zh-cn/3.13/library/base64.html#module-base64) 模块，作为一款现代化的替代。

- `xdrlib`

​	（由 Victor Stinner 和 Zachary Ware 在 [gh-104773](https://github.com/python/cpython/issues/104773) 和 [gh-104780](https://github.com/python/cpython/issues/104780) 中贡献。）

### 2to3

- 移除 **2to3** 程序和 `lib2to3` 模块，此前已在 Python 3.11 中被弃用。 （由 Victor Stinner 在 [gh-104780](https://github.com/python/cpython/issues/104780) 中贡献。）

### builtins

- Remove support for chained [`classmethod`](https://docs.python.org/zh-cn/3.13/library/functions.html#classmethod) descriptors (introduced in [gh-63272](https://github.com/python/cpython/issues/63272)). These can no longer be used to wrap other descriptors, such as [`property`](https://docs.python.org/zh-cn/3.13/library/functions.html#property). The core design of this feature was flawed and led to several problems. To "pass-through" a [`classmethod`](https://docs.python.org/zh-cn/3.13/library/functions.html#classmethod), consider using the `__wrapped__` attribute that was added in Python 3.10. (Contributed by Raymond Hettinger in [gh-89519](https://github.com/python/cpython/issues/89519).)
- Raise a [`RuntimeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#RuntimeError) when calling [`frame.clear()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame.clear) on a suspended frame (as has always been the case for an executing frame). (Contributed by Irit Katriel in [gh-79932](https://github.com/python/cpython/issues/79932).)

### configparser

- Remove the undocumented `LegacyInterpolation` class, deprecated in the docstring since Python 3.2, and at runtime since Python 3.11. (Contributed by Hugo van Kemenade in [gh-104886](https://github.com/python/cpython/issues/104886).)

### importlib.metadata

- Remove deprecated subscript ([`__getitem__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__getitem__)) access for [EntryPoint](https://docs.python.org/zh-cn/3.13/library/importlib.metadata.html#entry-points) objects. (Contributed by Jason R. Coombs in [gh-113175](https://github.com/python/cpython/issues/113175).)

### locale

- Remove the `locale.resetlocale()` function, deprecated in Python 3.11. Use `locale.setlocale(locale.LC_ALL, "")` instead. (Contributed by Victor Stinner in [gh-104783](https://github.com/python/cpython/issues/104783).)

### opcode

- Move `opcode.ENABLE_SPECIALIZATION` to `_opcode.ENABLE_SPECIALIZATION`. This field was added in 3.12, it was never documented, and is not intended for external use. (Contributed by Irit Katriel in [gh-105481](https://github.com/python/cpython/issues/105481).)
- Remove `opcode.is_pseudo()`, `opcode.MIN_PSEUDO_OPCODE`, and `opcode.MAX_PSEUDO_OPCODE`, which were added in Python 3.12, but were neither documented nor exposed through [`dis`](https://docs.python.org/zh-cn/3.13/library/dis.html#module-dis), and were not intended to be used externally. (Contributed by Irit Katriel in [gh-105481](https://github.com/python/cpython/issues/105481).)

### pathlib

- 移除了使用 [`Path`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.Path) 对象作为上下文管理器的能力。 此功能自 Python 3.9 起已被弃用并设为空操作。 （由 Barney Gale 在 [gh-83863](https://github.com/python/cpython/issues/83863) 中贡献。）

### re

- 移除了未被写入文档、已被弃用且已不能工作的 `re.template()` 函数和 `re.TEMPLATE` / `re.T` 旗标。 （由 Serhiy Storchaka 和 Nikita Sobolev 在 [gh-105687](https://github.com/python/cpython/issues/105687) 中贡献。）

### tkinter.tix

- 移除了 `tkinter.tix` 模块，它在 Python 3.6 中已被弃用。 该模块所包装的第三方库 Tix 已不再维护。 （由 Zachary Ware 在 [gh-75552](https://github.com/python/cpython/issues/75552) 中贡献。）.)

### turtle

- Remove the `RawTurtle.settiltangle()` method, deprecated in the documentation since Python 3.1 and at runtime since Python 3.11. (Contributed by Hugo van Kemenade in [gh-104876](https://github.com/python/cpython/issues/104876).)

### typing

- Remove the `typing.io` and `typing.re` namespaces, deprecated since Python 3.8. The items in those namespaces can be imported directly from the [`typing`](https://docs.python.org/zh-cn/3.13/library/typing.html#module-typing) module. (Contributed by Sebastian Rittau in [gh-92871](https://github.com/python/cpython/issues/92871).)
- 移除了创建 [`TypedDict`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.TypedDict) 类型的关键字参数方法，它在 Python 3.11 中已被弃用。 （由 Tomas Roun 在 [gh-104786](https://github.com/python/cpython/issues/104786) 中贡献。）

### unittest

- 移除了下列 [`unittest`](https://docs.python.org/zh-cn/3.13/library/unittest.html#module-unittest) 函数，它们在 Python 3.11 中已被弃用：

  - `unittest.findTestCases()`
  - `unittest.makeSuite()`
  - `unittest.getTestCaseNames()`

  请改用 [`TestLoader`](https://docs.python.org/zh-cn/3.13/library/unittest.html#unittest.TestLoader) 方法:

  - [`loadTestsFromModule()`](https://docs.python.org/zh-cn/3.13/library/unittest.html#unittest.TestLoader.loadTestsFromModule)
  - [`loadTestsFromTestCase()`](https://docs.python.org/zh-cn/3.13/library/unittest.html#unittest.TestLoader.loadTestsFromTestCase)
  - [`getTestCaseNames()`](https://docs.python.org/zh-cn/3.13/library/unittest.html#unittest.TestLoader.getTestCaseNames)

  （由 Hugo van Kemenade 在 [gh-104835](https://github.com/python/cpython/issues/104835) 中贡献。）

- Remove the untested and undocumented `TestProgram.usageExit()` method, deprecated in Python 3.11. (Contributed by Hugo van Kemenade in [gh-104992](https://github.com/python/cpython/issues/104992).)

### urllib

- Remove the *cafile*, *capath*, and *cadefault* parameters of the [`urllib.request.urlopen()`](https://docs.python.org/zh-cn/3.13/library/urllib.request.html#urllib.request.urlopen) function, deprecated in Python 3.6. Use the *context* parameter instead with an [`SSLContext`](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl.SSLContext) instance. The [`ssl.SSLContext.load_cert_chain()`](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl.SSLContext.load_cert_chain) function can be used to load specific certificates, or let [`ssl.create_default_context()`](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl.create_default_context) select the operating system's trusted certificate authority (CA) certificates. (Contributed by Victor Stinner in [gh-105382](https://github.com/python/cpython/issues/105382).)

### webbrowser

- Remove the untested and undocumented `MacOSX` class, deprecated in Python 3.11. Use the `MacOSXOSAScript` class (introduced in Python 3.2) instead. (Contributed by Hugo van Kemenade in [gh-104804](https://github.com/python/cpython/issues/104804).)
- Remove the deprecated `MacOSXOSAScript._name` attribute. Use the [`MacOSXOSAScript.name`](https://docs.python.org/zh-cn/3.13/library/webbrowser.html#webbrowser.controller.name) attribute instead. (Contributed by Nikita Sobolev in [gh-105546](https://github.com/python/cpython/issues/105546).)

## 新的弃用

- [用户自定义函数](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#user-defined-funcs):
  - Deprecate assignment to a function's [`__code__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#function.__code__) attribute, where the new code object's type does not match the function's type. The different types are: plain function, generator, async generator, and coroutine. (Contributed by Irit Katriel in [gh-81137](https://github.com/python/cpython/issues/81137).)
- [`array`](https://docs.python.org/zh-cn/3.13/library/array.html#module-array):
  - Deprecate the `'u'` format code (`wchar_t`) at runtime. This format code has been deprecated in documentation since Python 3.3, and will be removed in Python 3.16. Use the `'w'` format code ([`Py_UCS4`](https://docs.python.org/zh-cn/3.13/c-api/unicode.html#c.Py_UCS4)) for Unicode characters instead. (Contributed by Hugo van Kemenade in [gh-80480](https://github.com/python/cpython/issues/80480).)
- [`ctypes`](https://docs.python.org/zh-cn/3.13/library/ctypes.html#module-ctypes):
  - Deprecate the undocumented `SetPointerType()` function, to be removed in Python 3.15. (Contributed by Victor Stinner in [gh-105733](https://github.com/python/cpython/issues/105733).)
  - [Soft-deprecate](https://docs.python.org/zh-cn/3.13/glossary.html#term-soft-deprecated) the [`ARRAY()`](https://docs.python.org/zh-cn/3.13/library/ctypes.html#ctypes.ARRAY) function in favour of `type * length` multiplication. (Contributed by Victor Stinner in [gh-105733](https://github.com/python/cpython/issues/105733).)
- [`decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#module-decimal):
  - Deprecate the non-standard and undocumented [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) format specifier `'N'`, which is only supported in the `decimal` module's C implementation. (Contributed by Serhiy Storchaka in [gh-89902](https://github.com/python/cpython/issues/89902).)
- [`dis`](https://docs.python.org/zh-cn/3.13/library/dis.html#module-dis):
  - Deprecate the `HAVE_ARGUMENT` separator. Check membership in [`hasarg`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.hasarg) instead. (Contributed by Irit Katriel in [gh-109319](https://github.com/python/cpython/issues/109319).)
- [`getopt`](https://docs.python.org/zh-cn/3.13/library/getopt.html#module-getopt) and [`optparse`](https://docs.python.org/zh-cn/3.13/library/optparse.html#module-optparse):
  - Both modules are now [soft deprecated](https://docs.python.org/zh-cn/3.13/glossary.html#term-soft-deprecated), with [`argparse`](https://docs.python.org/zh-cn/3.13/library/argparse.html#module-argparse) preferred for new projects. This is a new soft-deprecation for the `getopt` module, whereas the `optparse` module was already *de facto* soft deprecated. (Contributed by Victor Stinner in [gh-106535](https://github.com/python/cpython/issues/106535).)
- [`gettext`](https://docs.python.org/zh-cn/3.13/library/gettext.html#module-gettext):
  - Deprecate non-integer numbers as arguments to functions and methods that consider plural forms in the `gettext` module, even if no translation was found. (Contributed by Serhiy Storchaka in [gh-88434](https://github.com/python/cpython/issues/88434).)
- [`glob`](https://docs.python.org/zh-cn/3.13/library/glob.html#module-glob):
  - Deprecate the undocumented `glob0()` and `glob1()` functions. Use [`glob()`](https://docs.python.org/zh-cn/3.13/library/glob.html#glob.glob) and pass a [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object) specifying the root directory to the *root_dir* parameter instead. (Contributed by Barney Gale in [gh-117337](https://github.com/python/cpython/issues/117337).)
- [`http.server`](https://docs.python.org/zh-cn/3.13/library/http.server.html#module-http.server):
  - Deprecate [`CGIHTTPRequestHandler`](https://docs.python.org/zh-cn/3.13/library/http.server.html#http.server.CGIHTTPRequestHandler), to be removed in Python 3.15. Process-based CGI HTTP servers have been out of favor for a very long time. This code was outdated, unmaintained, and rarely used. It has a high potential for both security and functionality bugs. (Contributed by Gregory P. Smith in [gh-109096](https://github.com/python/cpython/issues/109096).)
  - Deprecate the `--cgi` flag to the **python -m http.server** command-line interface, to be removed in Python 3.15. (Contributed by Gregory P. Smith in [gh-109096](https://github.com/python/cpython/issues/109096).)
- [`mimetypes`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#module-mimetypes):
  - [Soft-deprecate](https://docs.python.org/zh-cn/3.13/glossary.html#term-soft-deprecated) file path arguments to [`guess_type()`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.guess_type), use [`guess_file_type()`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.guess_file_type) instead. (Contributed by Serhiy Storchaka in [gh-66543](https://github.com/python/cpython/issues/66543).)
- [`re`](https://docs.python.org/zh-cn/3.13/library/re.html#module-re):
  - Deprecate passing the optional *maxsplit*, *count*, or *flags* arguments as positional arguments to the module-level [`split()`](https://docs.python.org/zh-cn/3.13/library/re.html#re.split), [`sub()`](https://docs.python.org/zh-cn/3.13/library/re.html#re.sub), and [`subn()`](https://docs.python.org/zh-cn/3.13/library/re.html#re.subn) functions. These parameters will become [keyword-only](https://docs.python.org/zh-cn/3.13/glossary.html#keyword-only-parameter) in a future version of Python. (Contributed by Serhiy Storchaka in [gh-56166](https://github.com/python/cpython/issues/56166).)
- [`pathlib`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#module-pathlib):
  - Deprecate [`PurePath.is_reserved()`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.PurePath.is_reserved), to be removed in Python 3.15. Use [`os.path.isreserved()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.isreserved) to detect reserved paths on Windows. (Contributed by Barney Gale in [gh-88569](https://github.com/python/cpython/issues/88569).)
- [`platform`](https://docs.python.org/zh-cn/3.13/library/platform.html#module-platform):
  - 弃用了 [`java_ver()`](https://docs.python.org/zh-cn/3.13/library/platform.html#platform.java_ver)，并将在 Python 3.15 中移除。 此函数仅对 Jython 支持有用，具有令人困惑的 API，并且大部分未经测试。 （由 Nikita Sobolev 在 [gh-116349](https://github.com/python/cpython/issues/116349) 中贡献。）.)
- [`pydoc`](https://docs.python.org/zh-cn/3.13/library/pydoc.html#module-pydoc):
  - 弃用未写入文档的 `ispackage()` 函数。 （由 Zackery Spytz 在 [gh-64020](https://github.com/python/cpython/issues/64020) 中贡献。）
- [`sqlite3`](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#module-sqlite3):
  - Deprecate passing more than one positional argument to the [`connect()`](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#sqlite3.connect) function and the [`Connection`](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#sqlite3.Connection) constructor. The remaining parameters will become keyword-only in Python 3.15. (Contributed by Erlend E. Aasland in [gh-107948](https://github.com/python/cpython/issues/107948).)
  - Deprecate passing name, number of arguments, and the callable as keyword arguments for [`Connection.create_function()`](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#sqlite3.Connection.create_function) and [`Connection.create_aggregate()`](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#sqlite3.Connection.create_aggregate) These parameters will become positional-only in Python 3.15. (Contributed by Erlend E. Aasland in [gh-108278](https://github.com/python/cpython/issues/108278).)
  - Deprecate passing the callback callable by keyword for the [`set_authorizer()`](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#sqlite3.Connection.set_authorizer), [`set_progress_handler()`](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#sqlite3.Connection.set_progress_handler), and [`set_trace_callback()`](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#sqlite3.Connection.set_trace_callback) [`Connection`](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#sqlite3.Connection) methods. The callback callables will become positional-only in Python 3.15. (Contributed by Erlend E. Aasland in [gh-108278](https://github.com/python/cpython/issues/108278).)
- [`sys`](https://docs.python.org/zh-cn/3.13/library/sys.html#module-sys):
  - Deprecate the [`_enablelegacywindowsfsencoding()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys._enablelegacywindowsfsencoding) function, to be removed in Python 3.16. Use the [`PYTHONLEGACYWINDOWSFSENCODING`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONLEGACYWINDOWSFSENCODING) environment variable instead. (Contributed by Inada Naoki in [gh-73427](https://github.com/python/cpython/issues/73427).)
- [`tarfile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#module-tarfile):
  - 弃用了未写入文档也未被使用的 `TarFile.tarfile` 属性，并将在 Python 3.16 中移除。 （在 [gh-115256](https://github.com/python/cpython/issues/115256) 中贡献。）
- [`traceback`](https://docs.python.org/zh-cn/3.13/library/traceback.html#module-traceback):
  - 已弃用 [`TracebackException.exc_type`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.TracebackException.exc_type) 属性。 请改用 [`TracebackException.exc_type_str`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.TracebackException.exc_type_str)。 （由 Irit Katriel 在 [gh-112332](https://github.com/python/cpython/issues/112332) 中贡献。）
- [`typing`](https://docs.python.org/zh-cn/3.13/library/typing.html#module-typing):
  - Deprecate the undocumented keyword argument syntax for creating [`NamedTuple`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.NamedTuple) classes (e.g. `Point = NamedTuple("Point", x=int, y=int)`), to be removed in Python 3.15. Use the class-based syntax or the functional syntax instead. (Contributed by Alex Waygood in [gh-105566](https://github.com/python/cpython/issues/105566).)
  - Deprecate omitting the *fields* parameter when creating a [`NamedTuple`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.NamedTuple) or [`typing.TypedDict`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.TypedDict) class, and deprecate passing `None` to the *fields* parameter of both types. Python 3.15 will require a valid sequence for the *fields* parameter. To create a NamedTuple class with zero fields, use `class NT(NamedTuple): pass` or `NT = NamedTuple("NT", ())`. To create a TypedDict class with zero fields, use `class TD(TypedDict): pass` or `TD = TypedDict("TD", {})`. (Contributed by Alex Waygood in [gh-105566](https://github.com/python/cpython/issues/105566) and [gh-105570](https://github.com/python/cpython/issues/105570).)
  - Deprecate the [`typing.no_type_check_decorator()`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.no_type_check_decorator) decorator function, to be removed in in Python 3.15. After eight years in the [`typing`](https://docs.python.org/zh-cn/3.13/library/typing.html#module-typing) module, it has yet to be supported by any major type checker. (Contributed by Alex Waygood in [gh-106309](https://github.com/python/cpython/issues/106309).)
  - Deprecate [`typing.AnyStr`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.AnyStr). In Python 3.16, it will be removed from `typing.__all__`, and a [`DeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#DeprecationWarning) will be emitted at runtime when it is imported or accessed. It will be removed entirely in Python 3.18. Use the new [type parameter syntax](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#type-params) instead. (Contributed by Michael The in [gh-107116](https://github.com/python/cpython/issues/107116).)
- [`wave`](https://docs.python.org/zh-cn/3.13/library/wave.html#module-wave):
  - Deprecate the [`getmark()`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_read.getmark), `setmark()`, and [`getmarkers()`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_read.getmarkers) methods of the [`Wave_read`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_read) and [`Wave_write`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_write) classes, to be removed in Python 3.15. (Contributed by Victor Stinner in [gh-105096](https://github.com/python/cpython/issues/105096).)

### 计划在 Python 3.14 中移除

- [`argparse`](https://docs.python.org/zh-cn/3.13/library/argparse.html#module-argparse): `argparse.BooleanOptionalAction` 的 *type*, *choices* 和 *metavar* 形参已被弃用并将在 3.14 中移除。 （由 Nikita Sobolev 在 [gh-92248](https://github.com/python/cpython/issues/92248) 中贡献。）

- [`ast`](https://docs.python.org/zh-cn/3.13/library/ast.html#module-ast): 以下特性自 Python 3.8 起已在文档中声明弃用，现在当运行时如果它们被访问或使用时将发出 [`DeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#DeprecationWarning)，并将在 Python 3.14 中移除：

  - `ast.Num`
  - `ast.Str`
  - `ast.Bytes`
  - `ast.NameConstant`
  - `ast.Ellipsis`

  请改用 [`ast.Constant`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Constant)。 （由 Serhiy Storchaka 在 [gh-90953](https://github.com/python/cpython/issues/90953) 中贡献。）

- [`asyncio`](https://docs.python.org/zh-cn/3.13/library/asyncio.html#module-asyncio):

  - 子监视器类 [`MultiLoopChildWatcher`](https://docs.python.org/zh-cn/3.13/library/asyncio-policy.html#asyncio.MultiLoopChildWatcher), [`FastChildWatcher`](https://docs.python.org/zh-cn/3.13/library/asyncio-policy.html#asyncio.FastChildWatcher), [`AbstractChildWatcher`](https://docs.python.org/zh-cn/3.13/library/asyncio-policy.html#asyncio.AbstractChildWatcher) 和 [`SafeChildWatcher`](https://docs.python.org/zh-cn/3.13/library/asyncio-policy.html#asyncio.SafeChildWatcher) 已被弃用并将在 Python 3.14 中移除。 （由 Kumar Aditya 在 [gh-94597](https://github.com/python/cpython/issues/94597) 中贡献。）
  - [`asyncio.set_child_watcher()`](https://docs.python.org/zh-cn/3.13/library/asyncio-policy.html#asyncio.set_child_watcher)、[`asyncio.get_child_watcher()`](https://docs.python.org/zh-cn/3.13/library/asyncio-policy.html#asyncio.get_child_watcher)、[`asyncio.AbstractEventLoopPolicy.set_child_watcher()`](https://docs.python.org/zh-cn/3.13/library/asyncio-policy.html#asyncio.AbstractEventLoopPolicy.set_child_watcher) 和 [`asyncio.AbstractEventLoopPolicy.get_child_watcher()`](https://docs.python.org/zh-cn/3.13/library/asyncio-policy.html#asyncio.AbstractEventLoopPolicy.get_child_watcher) 已弃用，并将在 Python 3.14 中移除。（由 Kumar Aditya 在 [gh-94597](https://github.com/python/cpython/issues/94597) 中贡献。）
  - 现在默认事件循环策略的 [`get_event_loop()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.get_event_loop) 方法在当前事件循环未设置并决定创建一个时将发出 [`DeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#DeprecationWarning)。 （由 Serhiy Storchaka 和 Guido van Rossum 在 [gh-100160](https://github.com/python/cpython/issues/100160) 中贡献。）

- [`collections.abc`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#module-collections.abc): 已弃用 [`ByteString`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.ByteString)。 推荐改用 `Sequence` 或 [`Buffer`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Buffer)。 用于类型标注时，则推荐并集运算符，如 `bytes | bytearray`，或 [`collections.abc.Buffer`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Buffer)。 （由 Shantanu Jain 在 [gh-91896](https://github.com/python/cpython/issues/91896) 中贡献。）

- [`email`](https://docs.python.org/zh-cn/3.13/library/email.html#module-email): 已弃用 [`email.utils.localtime()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.localtime) 中的 *isdst* 形参。 （由 Alan Williams 在 [gh-72346](https://github.com/python/cpython/issues/72346) 中贡献。）

- [`importlib.abc`](https://docs.python.org/zh-cn/3.13/library/importlib.html#module-importlib.abc) 中已弃用的类：

  - `importlib.abc.ResourceReader`
  - `importlib.abc.Traversable`
  - `importlib.abc.TraversableResources`

  使用 [`importlib.resources.abc`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.abc.html#module-importlib.resources.abc) 类代替:

  - [`importlib.resources.abc.Traversable`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.abc.html#importlib.resources.abc.Traversable)
  - [`importlib.resources.abc.TraversableResources`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.abc.html#importlib.resources.abc.TraversableResources)

  （由 Jason R. Coombs 和 Hugo van Kemenade 在 [gh-93963](https://github.com/python/cpython/issues/93963) 中贡献。）

- [`itertools`](https://docs.python.org/zh-cn/3.13/library/itertools.html#module-itertools) 具有对 copy, deepcopy 和 pickle 等操作的未写入文档的、低效的、历史上充满问题的且不稳定的支持。 这将在 3.14 中移除以显著减少代码量和维护负担。 （由 Raymond Hettinger 在 [gh-101588](https://github.com/python/cpython/issues/101588) 中贡献。）

- [`multiprocessing`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.html#module-multiprocessing): 默认的启动方法在目前默认使用 `'fork'` 的 Linux, BSD 和其他非 macOS POSIX 平台上将改为更安全的方法 ([gh-84559](https://github.com/python/cpython/issues/84559))。 为此添加运行时警告将带来糟糕的体验因为大部分代码并不会关心这个问题。 当你的代码 *需要* `'fork'` 时请使用 [`get_context()`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.html#multiprocessing.get_context) 或 [`set_start_method()`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.html#multiprocessing.set_start_method) API 来显式地指明。 参见 [上下文和启动方法](https://docs.python.org/zh-cn/3.13/library/multiprocessing.html#multiprocessing-start-methods)。

- [`pathlib`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#module-pathlib): [`is_relative_to()`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.PurePath.is_relative_to) 和 [`relative_to()`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.PurePath.relative_to): 传入额外参数的做法已被弃用。

- [`pkgutil`](https://docs.python.org/zh-cn/3.13/library/pkgutil.html#module-pkgutil): 现在 [`find_loader()`](https://docs.python.org/zh-cn/3.13/library/pkgutil.html#pkgutil.find_loader) 和 [`get_loader()`](https://docs.python.org/zh-cn/3.13/library/pkgutil.html#pkgutil.get_loader) 将引发 [`DeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#DeprecationWarning)；请改用 [`importlib.util.find_spec()`](https://docs.python.org/zh-cn/3.13/library/importlib.html#importlib.util.find_spec)。 （由 Nikita Sobolev 在 [gh-97850](https://github.com/python/cpython/issues/97850) 中贡献。）

- [`pty`](https://docs.python.org/zh-cn/3.13/library/pty.html#module-pty):

  - `master_open()`: 使用 [`pty.openpty()`](https://docs.python.org/zh-cn/3.13/library/pty.html#pty.openpty)。
  - `slave_open()`: 使用 [`pty.openpty()`](https://docs.python.org/zh-cn/3.13/library/pty.html#pty.openpty)。

- [`sqlite3`](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#module-sqlite3):

  - [`version`](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#sqlite3.version) 和 [`version_info`](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#sqlite3.version_info)。
  - 如果使用了 [命名占位符](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#sqlite3-placeholders) 且 *parameters* 是一个序列而不是 [`dict`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#dict) 则选择 [`execute()`](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#sqlite3.Cursor.execute) 和 [`executemany()`](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#sqlite3.Cursor.executemany)。

- [`typing`](https://docs.python.org/zh-cn/3.13/library/typing.html#module-typing): [`ByteString`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.ByteString) 自 Python 3.9 起已被弃用，现在当被使用时将会发出 [`DeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#DeprecationWarning)。

- [`urllib`](https://docs.python.org/zh-cn/3.13/library/urllib.html#module-urllib): `urllib.parse.Quoter` 已被弃用：它不应被作为公有 API。 （由 Gregory P. Smith 在 [gh-88168](https://github.com/python/cpython/issues/88168) 中贡献。）

### Python 3.15 中的待移除功能

- 导入系统：
  - 当设置 [`__spec__.cached`](https://docs.python.org/zh-cn/3.13/library/importlib.html#importlib.machinery.ModuleSpec.cached) 失败时在模块上设置 [`__cached__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#module.__cached__) 的做法已被弃用。 在 Python 3.15 中，`__cached__` 将不会再被导入系统或标准库纳入考虑。 ([gh-97879](https://github.com/python/cpython/issues/97879))
  - 当设备 [`__spec__.parent`](https://docs.python.org/zh-cn/3.13/library/importlib.html#importlib.machinery.ModuleSpec.parent) 失败时在模块上设置 [`__package__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#module.__package__) 的做法已被弃用。 在 Python 3.15 中，`__package__` 将不会再被导入系统或标准库纳入考虑。 ([gh-97879](https://github.com/python/cpython/issues/97879))
- [`ctypes`](https://docs.python.org/zh-cn/3.13/library/ctypes.html#module-ctypes):
  - 未写入文档的 `ctypes.SetPointerType()` 函数自 Python 3.13 起已被弃用。
- [`http.server`](https://docs.python.org/zh-cn/3.13/library/http.server.html#module-http.server):
  - 过时且很少被使用的 [`CGIHTTPRequestHandler`](https://docs.python.org/zh-cn/3.13/library/http.server.html#http.server.CGIHTTPRequestHandler) 自 Python 3.13 起已被弃用。 不存在直接的替代品。 对于建立带有请求处理器的 Web 服务程序来说 *任何东西* 都比 CGI 要好。
  - 用于 **python -m http.server** 命令行界面的 `--cgi` 旗标自 Python 3.13 起已被弃用。
- [`locale`](https://docs.python.org/zh-cn/3.13/library/locale.html#module-locale):
  - [`getdefaultlocale()`](https://docs.python.org/zh-cn/3.13/library/locale.html#locale.getdefaultlocale) 函数自 Python 3.11 起已被弃用。 最初计划在 Python 3.13 中移除它 ([gh-90817](https://github.com/python/cpython/issues/90817))，但已被推迟至 Python 3.15。 请改用 [`getlocale()`](https://docs.python.org/zh-cn/3.13/library/locale.html#locale.getlocale), [`setlocale()`](https://docs.python.org/zh-cn/3.13/library/locale.html#locale.setlocale) 和 [`getencoding()`](https://docs.python.org/zh-cn/3.13/library/locale.html#locale.getencoding)。 （由 Hugo van Kemenade 在 [gh-111187](https://github.com/python/cpython/issues/111187) 中贡献。）
- [`pathlib`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#module-pathlib):
  - [`PurePath.is_reserved()`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.PurePath.is_reserved) 自 Python 3.13 起已被弃用。 请使用 [`os.path.isreserved()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.isreserved) 来检测 Windows 上的保留路径。
- [`platform`](https://docs.python.org/zh-cn/3.13/library/platform.html#module-platform):
  - [`java_ver()`](https://docs.python.org/zh-cn/3.13/library/platform.html#platform.java_ver) 自 Python 3.13 起已被弃用。 此函数仅对 Jython 支持有用，具有令人困惑的 API，并且大部分未经测试。
- [`threading`](https://docs.python.org/zh-cn/3.13/library/threading.html#module-threading):
  - 在 Python 3.15 中 [`RLock()`](https://docs.python.org/zh-cn/3.13/library/threading.html#threading.RLock) 将不再接受参数。 传入参数的做法自 Python 3.14 起已被弃用，因为 Python 版本不接受任何参数，而 C 版本允许任意数量的位置或关键字参数，但会忽略所有参数。
- [`types`](https://docs.python.org/zh-cn/3.13/library/types.html#module-types):
  - [`types.CodeType`](https://docs.python.org/zh-cn/3.13/library/types.html#types.CodeType): Accessing [`co_lnotab`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#codeobject.co_lnotab) was deprecated in [**PEP 626**](https://peps.python.org/pep-0626/) since 3.10 and was planned to be removed in 3.12, but it only got a proper [`DeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#DeprecationWarning) in 3.12. May be removed in 3.15. (Contributed by Nikita Sobolev in [gh-101866](https://github.com/python/cpython/issues/101866).)
- [`typing`](https://docs.python.org/zh-cn/3.13/library/typing.html#module-typing):
  - 未写入文档的用于创建 [`NamedTuple`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.NamedTuple) 类的关键字参数语法 (例如 `Point = NamedTuple("Point", x=int, y=int)`) 自 Python 3.13 起已被弃用。 请改用基于类的语法或函数语法。
  - [`typing.no_type_check_decorator()`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.no_type_check_decorator) 装饰器自 Python 3.13 起已被弃用。 存在于 [`typing`](https://docs.python.org/zh-cn/3.13/library/typing.html#module-typing) 模块八年之后，它仍未被任何主要类型检查器所支持。
- [`wave`](https://docs.python.org/zh-cn/3.13/library/wave.html#module-wave):
  - [`Wave_read`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_read) 和 [`Wave_write`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_write) 类的 [`getmark()`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_read.getmark), `setmark()` 和 [`getmarkers()`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_read.getmarkers) 方法自 Python 3.13 起已被弃用。

### Pending removal in Python 3.16

- 导入系统：
  - Setting [`__loader__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#module.__loader__) on a module while failing to set [`__spec__.loader`](https://docs.python.org/zh-cn/3.13/library/importlib.html#importlib.machinery.ModuleSpec.loader) is deprecated. In Python 3.16, `__loader__` will cease to be set or taken into consideration by the import system or the standard library.
- [`array`](https://docs.python.org/zh-cn/3.13/library/array.html#module-array):
  - `'u'` 格式代码 (`wchar_t`) 自 Python 3.3 起已在文档中弃用并自 Python 3.13 起在运行时弃用。 对于 Unicode 字符请改用 `'w'` 格式代码 ([`Py_UCS4`](https://docs.python.org/zh-cn/3.13/c-api/unicode.html#c.Py_UCS4))。
- [`asyncio`](https://docs.python.org/zh-cn/3.13/library/asyncio.html#module-asyncio):
  - `asyncio.iscoroutinefunction()` is deprecated and will be removed in Python 3.16, use [`inspect.iscoroutinefunction()`](https://docs.python.org/zh-cn/3.13/library/inspect.html#inspect.iscoroutinefunction) instead. (Contributed by Jiahao Li and Kumar Aditya in [gh-122875](https://github.com/python/cpython/issues/122875).)
- [`builtins`](https://docs.python.org/zh-cn/3.13/library/builtins.html#module-builtins):
  - 对布尔类型 `~True` 或 `~False` 执行按位取反的操作自 Python 3.12 起已被弃用，因为它会产生奇怪和不直观的结果 (`-2` and `-1`)。 请改用 `not x` 来对布尔值执行逻辑否操作。 对于需要对下层整数执行按位取反操作的少数场合，请显式地将其转换为 `int` (`~int(x)`)。
- [`shutil`](https://docs.python.org/zh-cn/3.13/library/shutil.html#module-shutil):
  - `ExecError` 异常自 Python 3.14 起已被弃用。 它自 Python 3.4 起就未被 `shutil` 中的任何函数所使用，现在是 [`RuntimeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#RuntimeError) 的一个别名。
- [`symtable`](https://docs.python.org/zh-cn/3.13/library/symtable.html#module-symtable):
  - [`Class.get_methods`](https://docs.python.org/zh-cn/3.13/library/symtable.html#symtable.Class.get_methods) 方法自 Python 3.14 起被弃用。
- [`sys`](https://docs.python.org/zh-cn/3.13/library/sys.html#module-sys):
  - [`_enablelegacywindowsfsencoding()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys._enablelegacywindowsfsencoding) 函数自 Python 3.13 起被弃用。 请改用 [`PYTHONLEGACYWINDOWSFSENCODING`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONLEGACYWINDOWSFSENCODING) 环境变量。
- [`tarfile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#module-tarfile):
  - 未写入文档也未被使用的 `TarFile.tarfile` 属性自 Python 3.13 起被弃用。

### 计划在未来版本中移除

​	以下API将会被移除，尽管具体时间还未确定。

- [`argparse`](https://docs.python.org/zh-cn/3.13/library/argparse.html#module-argparse): 嵌套参数分组和嵌套互斥分组的做法已被弃用。
- [`array`](https://docs.python.org/zh-cn/3.13/library/array.html#module-array) 的 `'u'` 格式代码 ([gh-57281](https://github.com/python/cpython/issues/57281))
- [`builtins`](https://docs.python.org/zh-cn/3.13/library/builtins.html#module-builtins):
  - `bool(NotImplemented)`。
  - 生成器: `throw(type, exc, tb)` 和 `athrow(type, exc, tb)` 签名已被弃用：请改用 `throw(exc)` 和 `athrow(exc)`，即单参数签名。
  - 目前 Python 接受数字类字面值后面紧跟关键字的写法，例如 `0in x`, `1or x`, `0if 1else 2`。 它允许像 `[0x1for x in y]` 这样令人困惑且有歧义的表达式 (它可以被解读为 `[0x1 for x in y]` 或者 `[0x1f or x in y]`)。 如果数字类字面值后面紧跟关键字 [`and`](https://docs.python.org/zh-cn/3.13/reference/expressions.html#and), [`else`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#else), [`for`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#for), [`if`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#if), [`in`](https://docs.python.org/zh-cn/3.13/reference/expressions.html#in), [`is`](https://docs.python.org/zh-cn/3.13/reference/expressions.html#is) 和 [`or`](https://docs.python.org/zh-cn/3.13/reference/expressions.html#or) 中的一个将会引发语法警告。 在未来的版本中它将改为语法错误。 ([gh-87999](https://github.com/python/cpython/issues/87999))
  - 对 `__index__()` 和 `__int__()` 方法返回非 int 类型的支持：将要求这些方法必须返回 [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int) 的子类的实例。
  - 对 `__float__()` 方法返回 [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float) 的子类的支持：将要求这些方法必须返回 [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float) 的实例。
  - 对 `__complex__()` 方法返回 [`complex`](https://docs.python.org/zh-cn/3.13/library/functions.html#complex) 的子类的支持：将要求这些方法必须返回 [`complex`](https://docs.python.org/zh-cn/3.13/library/functions.html#complex) 的实例。
  - 将 `int()` 委托给 `__trunc__()` 方法。
  - 传入一个复数作为 [`complex()`](https://docs.python.org/zh-cn/3.13/library/functions.html#complex) 构造器中的 *real* 或 *imag* 参数的做法现在已被弃用；它应当仅作为单个位置参数被传入。 （由 Serhiy Storchaka 在 [gh-109218](https://github.com/python/cpython/issues/109218) 中贡献。）.)
- [`calendar`](https://docs.python.org/zh-cn/3.13/library/calendar.html#module-calendar): `calendar.January` 和 `calendar.February` 常量已被弃用并由 [`calendar.JANUARY`](https://docs.python.org/zh-cn/3.13/library/calendar.html#calendar.JANUARY) 和 [`calendar.FEBRUARY`](https://docs.python.org/zh-cn/3.13/library/calendar.html#calendar.FEBRUARY) 替代。 （由 Prince Roshan 在 [gh-103636](https://github.com/python/cpython/issues/103636) 中贡献。）
- [`codeobject.co_lnotab`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#codeobject.co_lnotab): 改用 [`codeobject.co_lines()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#codeobject.co_lines) 方法。
- [`datetime`](https://docs.python.org/zh-cn/3.13/library/datetime.html#module-datetime):
  - [`utcnow()`](https://docs.python.org/zh-cn/3.13/library/datetime.html#datetime.datetime.utcnow): 使用 `datetime.datetime.now(tz=datetime.UTC)`。
  - [`utcfromtimestamp()`](https://docs.python.org/zh-cn/3.13/library/datetime.html#datetime.datetime.utcfromtimestamp): 使用 `datetime.datetime.fromtimestamp(timestamp, tz=datetime.UTC)`。
- [`gettext`](https://docs.python.org/zh-cn/3.13/library/gettext.html#module-gettext): 复数值必须是一个整数。
- [`importlib`](https://docs.python.org/zh-cn/3.13/library/importlib.html#module-importlib):
  - `load_module()` 方法：改用 `exec_module()`。
  - [`cache_from_source()`](https://docs.python.org/zh-cn/3.13/library/importlib.html#importlib.util.cache_from_source) *debug_override* 形参已被弃用：改用 *optimization* 形能耐。
- [`importlib.metadata`](https://docs.python.org/zh-cn/3.13/library/importlib.metadata.html#module-importlib.metadata):
  - `EntryPoints` 元组接口。
  - 返回值中隐式的 `None`。
- [`logging`](https://docs.python.org/zh-cn/3.13/library/logging.html#module-logging): `warn()` 方法自 Python 3.3 起已被弃用，请改用 [`warning()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.warning)。
- [`mailbox`](https://docs.python.org/zh-cn/3.13/library/mailbox.html#module-mailbox): 对 StringIO 输入和文本模式的使用已被弃用，改用 BytesIO 和二进制模式。
- [`os`](https://docs.python.org/zh-cn/3.13/library/os.html#module-os): 在多线程的进程中调用 [`os.register_at_fork()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.register_at_fork)。
- `pydoc.ErrorDuringImport`: 使用元组值作为 *exc_info* 形参的做法已被弃用，应使用异常实例。
- [`re`](https://docs.python.org/zh-cn/3.13/library/re.html#module-re): 现在对于正则表达式中的数字分组引用和分组名称将应用更严格的规则。 现在只接受 ASCII 数字序列作为数字引用。 字节串模式和替换字符串中的分组名称现在只能包含 ASCII 字母和数字以及下划线。 （由 Serhiy Storchaka 在 [gh-91760](https://github.com/python/cpython/issues/91760) 中贡献。）
- `sre_compile`, `sre_constants` 和 `sre_parse` 模块。
- [`shutil`](https://docs.python.org/zh-cn/3.13/library/shutil.html#module-shutil): [`rmtree()`](https://docs.python.org/zh-cn/3.13/library/shutil.html#shutil.rmtree) 的 *onerror* 形参在 Python 3.12 中已被弃用；请改用 *onexc* 形参。
- [`ssl`](https://docs.python.org/zh-cn/3.13/library/ssl.html#module-ssl) 选项和协议：
  - [`ssl.SSLContext`](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl.SSLContext) 不带 protocol 参数的做法已被弃用。
  - [`ssl.SSLContext`](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl.SSLContext): [`set_npn_protocols()`](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl.SSLContext.set_npn_protocols) 和 `selected_npn_protocol()` 已被弃用：请改用 ALPN.
  - `ssl.OP_NO_SSL*` 选项
  - `ssl.OP_NO_TLS*` 选项
  - `ssl.PROTOCOL_SSLv3`
  - `ssl.PROTOCOL_TLS`
  - `ssl.PROTOCOL_TLSv1`
  - `ssl.PROTOCOL_TLSv1_1`
  - `ssl.PROTOCOL_TLSv1_2`
  - `ssl.TLSVersion.SSLv3`
  - `ssl.TLSVersion.TLSv1`
  - `ssl.TLSVersion.TLSv1_1`
- [`sysconfig.is_python_build()`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#sysconfig.is_python_build) *check_home* 形参已被弃用并会被忽略。
- [`threading`](https://docs.python.org/zh-cn/3.13/library/threading.html#module-threading) 的方法：
  - `threading.Condition.notifyAll()`: 使用 [`notify_all()`](https://docs.python.org/zh-cn/3.13/library/threading.html#threading.Condition.notify_all)。
  - `threading.Event.isSet()`: 使用 [`is_set()`](https://docs.python.org/zh-cn/3.13/library/threading.html#threading.Event.is_set)。
  - `threading.Thread.isDaemon()`, [`threading.Thread.setDaemon()`](https://docs.python.org/zh-cn/3.13/library/threading.html#threading.Thread.setDaemon): 使用 [`threading.Thread.daemon`](https://docs.python.org/zh-cn/3.13/library/threading.html#threading.Thread.daemon) 属性。
  - `threading.Thread.getName()`, [`threading.Thread.setName()`](https://docs.python.org/zh-cn/3.13/library/threading.html#threading.Thread.setName): 使用 [`threading.Thread.name`](https://docs.python.org/zh-cn/3.13/library/threading.html#threading.Thread.name) 属性。
  - `threading.currentThread()`: 使用 [`threading.current_thread()`](https://docs.python.org/zh-cn/3.13/library/threading.html#threading.current_thread)。
  - `threading.activeCount()`: 使用 [`threading.active_count()`](https://docs.python.org/zh-cn/3.13/library/threading.html#threading.active_count)。
- [`typing.Text`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.Text) ([gh-92332](https://github.com/python/cpython/issues/92332))。
- [`unittest.IsolatedAsyncioTestCase`](https://docs.python.org/zh-cn/3.13/library/unittest.html#unittest.IsolatedAsyncioTestCase): 从测试用例返回不为 `None` 的值的做法已被弃用。
- [`urllib.parse`](https://docs.python.org/zh-cn/3.13/library/urllib.parse.html#module-urllib.parse) 函数已被弃用：改用 [`urlparse()`](https://docs.python.org/zh-cn/3.13/library/urllib.parse.html#urllib.parse.urlparse)
  - `splitattr()`
  - `splithost()`
  - `splitnport()`
  - `splitpasswd()`
  - `splitport()`
  - `splitquery()`
  - `splittag()`
  - `splittype()`
  - `splituser()`
  - `splitvalue()`
  - `to_bytes()`
- [`urllib.request`](https://docs.python.org/zh-cn/3.13/library/urllib.request.html#module-urllib.request): 发起请求的 [`URLopener`](https://docs.python.org/zh-cn/3.13/library/urllib.request.html#urllib.request.URLopener) 和 [`FancyURLopener`](https://docs.python.org/zh-cn/3.13/library/urllib.request.html#urllib.request.FancyURLopener) 方式已被弃用。 改用更新 [`urlopen()`](https://docs.python.org/zh-cn/3.13/library/urllib.request.html#urllib.request.urlopen) 函数和方法。
- [`wsgiref`](https://docs.python.org/zh-cn/3.13/library/wsgiref.html#module-wsgiref): `SimpleHandler.stdout.write()` 不应执行部分写入。
- [`xml.etree.ElementTree`](https://docs.python.org/zh-cn/3.13/library/xml.etree.elementtree.html#module-xml.etree.ElementTree): 对 [`Element`](https://docs.python.org/zh-cn/3.13/library/xml.etree.elementtree.html#xml.etree.ElementTree.Element) 的真值测试已被弃用。 在未来的发布版中它将始终返回 `True`。 建议改用显式的 `len(elem)` 或 `elem is not None` 测试。
- [`zipimport.zipimporter.load_module()`](https://docs.python.org/zh-cn/3.13/library/zipimport.html#zipimport.zipimporter.load_module) 已被弃用：请改用 [`exec_module()`](https://docs.python.org/zh-cn/3.13/library/zipimport.html#zipimport.zipimporter.exec_module)。

## CPython 字节码的变化

- The oparg of [`YIELD_VALUE`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-YIELD_VALUE) is now `1` if the yield is part of a yield-from or await, and `0` otherwise. The oparg of [`RESUME`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-RESUME) was changed to add a bit indicating if the except-depth is 1, which is needed to optimize closing of generators. (Contributed by Irit Katriel in [gh-111354](https://github.com/python/cpython/issues/111354).)

## C API 的变化

### 新的特性

- 增加 [PyMonitoring C API](https://docs.python.org/zh-cn/3.13/c-api/monitoring.html#c-api-monitoring) 用于生成 [**PEP 669**](https://peps.python.org/pep-0669/) 监控事件：

  - [`PyMonitoringState`](https://docs.python.org/zh-cn/3.13/c-api/monitoring.html#c.PyMonitoringState)
  - [`PyMonitoring_FirePyStartEvent()`](https://docs.python.org/zh-cn/3.13/c-api/monitoring.html#c.PyMonitoring_FirePyStartEvent)
  - [`PyMonitoring_FirePyResumeEvent()`](https://docs.python.org/zh-cn/3.13/c-api/monitoring.html#c.PyMonitoring_FirePyResumeEvent)
  - [`PyMonitoring_FirePyReturnEvent()`](https://docs.python.org/zh-cn/3.13/c-api/monitoring.html#c.PyMonitoring_FirePyReturnEvent)
  - [`PyMonitoring_FirePyYieldEvent()`](https://docs.python.org/zh-cn/3.13/c-api/monitoring.html#c.PyMonitoring_FirePyYieldEvent)
  - [`PyMonitoring_FireCallEvent()`](https://docs.python.org/zh-cn/3.13/c-api/monitoring.html#c.PyMonitoring_FireCallEvent)
  - [`PyMonitoring_FireLineEvent()`](https://docs.python.org/zh-cn/3.13/c-api/monitoring.html#c.PyMonitoring_FireLineEvent)
  - [`PyMonitoring_FireJumpEvent()`](https://docs.python.org/zh-cn/3.13/c-api/monitoring.html#c.PyMonitoring_FireJumpEvent)
  - [`PyMonitoring_FireBranchEvent()`](https://docs.python.org/zh-cn/3.13/c-api/monitoring.html#c.PyMonitoring_FireBranchEvent)
  - [`PyMonitoring_FireCReturnEvent()`](https://docs.python.org/zh-cn/3.13/c-api/monitoring.html#c.PyMonitoring_FireCReturnEvent)
  - [`PyMonitoring_FirePyThrowEvent()`](https://docs.python.org/zh-cn/3.13/c-api/monitoring.html#c.PyMonitoring_FirePyThrowEvent)
  - [`PyMonitoring_FireRaiseEvent()`](https://docs.python.org/zh-cn/3.13/c-api/monitoring.html#c.PyMonitoring_FireRaiseEvent)
  - [`PyMonitoring_FireCRaiseEvent()`](https://docs.python.org/zh-cn/3.13/c-api/monitoring.html#c.PyMonitoring_FireCRaiseEvent)
  - [`PyMonitoring_FireReraiseEvent()`](https://docs.python.org/zh-cn/3.13/c-api/monitoring.html#c.PyMonitoring_FireReraiseEvent)
  - [`PyMonitoring_FireExceptionHandledEvent()`](https://docs.python.org/zh-cn/3.13/c-api/monitoring.html#c.PyMonitoring_FireExceptionHandledEvent)
  - [`PyMonitoring_FirePyUnwindEvent()`](https://docs.python.org/zh-cn/3.13/c-api/monitoring.html#c.PyMonitoring_FirePyUnwindEvent)
  - [`PyMonitoring_FireStopIterationEvent()`](https://docs.python.org/zh-cn/3.13/c-api/monitoring.html#c.PyMonitoring_FireStopIterationEvent)
  - [`PyMonitoring_EnterScope()`](https://docs.python.org/zh-cn/3.13/c-api/monitoring.html#c.PyMonitoring_EnterScope)
  - [`PyMonitoring_ExitScope()`](https://docs.python.org/zh-cn/3.13/c-api/monitoring.html#c.PyMonitoring_ExitScope)

  （由 Irit Katriel 在 [gh-111997](https://github.com/python/cpython/issues/111997) 中贡献。）

- 增加了 [`PyMutex`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyMutex)，它是占用一个字节的轻量级互斥锁，以及新的 [`PyMutex_Lock()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyMutex_Lock) 和 [`PyMutex_Unlock()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyMutex_Unlock) 函数。 如果操作需要阻塞则 `PyMutex_Lock()` 将释放 (当前持有的) [GIL](https://docs.python.org/zh-cn/3.13/glossary.html#term-GIL)。 （由 Sam Gross 在 [gh-108724](https://github.com/python/cpython/issues/108724) 中贡献。）

- 增加了 [PyTime C API](https://docs.python.org/zh-cn/3.13/c-api/time.html#c-api-time) 以提供对系统时钟的访问：

  - [`PyTime_t`](https://docs.python.org/zh-cn/3.13/c-api/time.html#c.PyTime_t)。
  - [`PyTime_MIN`](https://docs.python.org/zh-cn/3.13/c-api/time.html#c.PyTime_MIN) 和 [`PyTime_MAX`](https://docs.python.org/zh-cn/3.13/c-api/time.html#c.PyTime_MAX)。
  - [`PyTime_AsSecondsDouble()`](https://docs.python.org/zh-cn/3.13/c-api/time.html#c.PyTime_AsSecondsDouble)。
  - [`PyTime_Monotonic()`](https://docs.python.org/zh-cn/3.13/c-api/time.html#c.PyTime_Monotonic)。
  - [`PyTime_MonotonicRaw()`](https://docs.python.org/zh-cn/3.13/c-api/time.html#c.PyTime_MonotonicRaw)。
  - [`PyTime_PerfCounter()`](https://docs.python.org/zh-cn/3.13/c-api/time.html#c.PyTime_PerfCounter)。
  - [`PyTime_PerfCounterRaw()`](https://docs.python.org/zh-cn/3.13/c-api/time.html#c.PyTime_PerfCounterRaw)。
  - [`PyTime_Time()`](https://docs.python.org/zh-cn/3.13/c-api/time.html#c.PyTime_Time)。
  - [`PyTime_TimeRaw()`](https://docs.python.org/zh-cn/3.13/c-api/time.html#c.PyTime_TimeRaw)。

  （由 Victor Stinner 和 Petr Viktorin 在 [gh-110850](https://github.com/python/cpython/issues/110850) 中贡献。）

- Add the [`PyDict_ContainsString()`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDict_ContainsString) function with the same behavior as [`PyDict_Contains()`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDict_Contains), but *key* is specified as a const char* UTF-8 encoded bytes string, rather than a [PyObject](https://docs.python.org/zh-cn/3.13/c-api/structures.html#c.PyObject)*. (Contributed by Victor Stinner in [gh-108314](https://github.com/python/cpython/issues/108314).)

- Add the [`PyDict_GetItemRef()`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDict_GetItemRef) and [`PyDict_GetItemStringRef()`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDict_GetItemStringRef) functions, which behave similarly to [`PyDict_GetItemWithError()`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDict_GetItemWithError), but return a [strong reference](https://docs.python.org/zh-cn/3.13/glossary.html#term-strong-reference) instead of a [borrowed reference](https://docs.python.org/zh-cn/3.13/glossary.html#term-borrowed-reference). Moreover, these functions return `-1` on error, removing the need to check `PyErr_Occurred()`. (Contributed by Victor Stinner in [gh-106004](https://github.com/python/cpython/issues/106004).)

- Add the [`PyDict_SetDefaultRef()`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDict_SetDefaultRef) function, which behaves similarly to [`PyDict_SetDefault()`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDict_SetDefault), but returns a [strong reference](https://docs.python.org/zh-cn/3.13/glossary.html#term-strong-reference) instead of a [borrowed reference](https://docs.python.org/zh-cn/3.13/glossary.html#term-borrowed-reference). This function returns `-1` on error, `0` on insertion, and `1` if the key was already present in the dictionary. (Contributed by Sam Gross in [gh-112066](https://github.com/python/cpython/issues/112066).)

- Add the [`PyDict_Pop()`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDict_Pop) and [`PyDict_PopString()`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDict_PopString) functions to remove a key from a dictionary and optionally return the removed value. This is similar to [`dict.pop()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#dict.pop), though there is no default value, and [`KeyError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#KeyError) is not raised for missing keys. (Contributed by Stefan Behnel and Victor Stinner in [gh-111262](https://github.com/python/cpython/issues/111262).)

- Add the [`PyMapping_GetOptionalItem()`](https://docs.python.org/zh-cn/3.13/c-api/mapping.html#c.PyMapping_GetOptionalItem) and [`PyMapping_GetOptionalItemString()`](https://docs.python.org/zh-cn/3.13/c-api/mapping.html#c.PyMapping_GetOptionalItemString) functions as alternatives to [`PyObject_GetItem()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_GetItem) and [`PyMapping_GetItemString()`](https://docs.python.org/zh-cn/3.13/c-api/mapping.html#c.PyMapping_GetItemString) respectively. The new functions do not raise [`KeyError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#KeyError) if the requested key is missing from the mapping. These variants are more convenient and faster if a missing key should not be treated as a failure. (Contributed by Serhiy Storchaka in [gh-106307](https://github.com/python/cpython/issues/106307).)

- Add the [`PyObject_GetOptionalAttr()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_GetOptionalAttr) and [`PyObject_GetOptionalAttrString()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_GetOptionalAttrString) functions as alternatives to [`PyObject_GetAttr()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_GetAttr) and [`PyObject_GetAttrString()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_GetAttrString) respectively. The new functions do not raise [`AttributeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#AttributeError) if the requested attribute is not found on the object. These variants are more convenient and faster if the missing attribute should not be treated as a failure. (Contributed by Serhiy Storchaka in [gh-106521](https://github.com/python/cpython/issues/106521).)

- Add the [`PyErr_FormatUnraisable()`](https://docs.python.org/zh-cn/3.13/c-api/exceptions.html#c.PyErr_FormatUnraisable) function as an extension to [`PyErr_WriteUnraisable()`](https://docs.python.org/zh-cn/3.13/c-api/exceptions.html#c.PyErr_WriteUnraisable) that allows customizing the warning message. (Contributed by Serhiy Storchaka in [gh-108082](https://github.com/python/cpython/issues/108082).)

- Add new functions that return a [strong reference](https://docs.python.org/zh-cn/3.13/glossary.html#term-strong-reference) instead of a [borrowed reference](https://docs.python.org/zh-cn/3.13/glossary.html#term-borrowed-reference) for frame locals, globals, and builtins, as part of [PEP 667](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#whatsnew313-locals-semantics):

  - [`PyEval_GetFrameBuiltins()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetFrameBuiltins) replaces [`PyEval_GetBuiltins()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetBuiltins)
  - [`PyEval_GetFrameGlobals()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetFrameGlobals) replaces [`PyEval_GetGlobals()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetGlobals)
  - [`PyEval_GetFrameLocals()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetFrameLocals) replaces [`PyEval_GetLocals()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetLocals)

  (Contributed by Mark Shannon and Tian Gao in [gh-74929](https://github.com/python/cpython/issues/74929).)

- Add the [`Py_GetConstant()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.Py_GetConstant) and [`Py_GetConstantBorrowed()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.Py_GetConstantBorrowed) functions to get [strong](https://docs.python.org/zh-cn/3.13/glossary.html#term-strong-reference) or [borrowed](https://docs.python.org/zh-cn/3.13/glossary.html#term-borrowed-reference) references to constants. For example, `Py_GetConstant(Py_CONSTANT_ZERO)` returns a strong reference to the constant zero. (Contributed by Victor Stinner in [gh-115754](https://github.com/python/cpython/issues/115754).)

- Add the [`PyImport_AddModuleRef()`](https://docs.python.org/zh-cn/3.13/c-api/import.html#c.PyImport_AddModuleRef) function as a replacement for [`PyImport_AddModule()`](https://docs.python.org/zh-cn/3.13/c-api/import.html#c.PyImport_AddModule) that returns a [strong reference](https://docs.python.org/zh-cn/3.13/glossary.html#term-strong-reference) instead of a [borrowed reference](https://docs.python.org/zh-cn/3.13/glossary.html#term-borrowed-reference). (Contributed by Victor Stinner in [gh-105922](https://github.com/python/cpython/issues/105922).)

- Add the [`Py_IsFinalizing()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_IsFinalizing) function to check whether the main Python interpreter is [shutting down](https://docs.python.org/zh-cn/3.13/glossary.html#term-interpreter-shutdown). (Contributed by Victor Stinner in [gh-108014](https://github.com/python/cpython/issues/108014).)

- Add the [`PyList_GetItemRef()`](https://docs.python.org/zh-cn/3.13/c-api/list.html#c.PyList_GetItemRef) function as a replacement for [`PyList_GetItem()`](https://docs.python.org/zh-cn/3.13/c-api/list.html#c.PyList_GetItem) that returns a [strong reference](https://docs.python.org/zh-cn/3.13/glossary.html#term-strong-reference) instead of a [borrowed reference](https://docs.python.org/zh-cn/3.13/glossary.html#term-borrowed-reference). (Contributed by Sam Gross in [gh-114329](https://github.com/python/cpython/issues/114329).)

- Add the [`PyList_Extend()`](https://docs.python.org/zh-cn/3.13/c-api/list.html#c.PyList_Extend) and [`PyList_Clear()`](https://docs.python.org/zh-cn/3.13/c-api/list.html#c.PyList_Clear) functions, mirroring the Python `list.extend()` and `list.clear()` methods. (Contributed by Victor Stinner in [gh-111138](https://github.com/python/cpython/issues/111138).)

- Add the [`PyLong_AsInt()`](https://docs.python.org/zh-cn/3.13/c-api/long.html#c.PyLong_AsInt) function. It behaves similarly to [`PyLong_AsLong()`](https://docs.python.org/zh-cn/3.13/c-api/long.html#c.PyLong_AsLong), but stores the result in a C int instead of a C long. (Contributed by Victor Stinner in [gh-108014](https://github.com/python/cpython/issues/108014).)

- Add the [`PyLong_AsNativeBytes()`](https://docs.python.org/zh-cn/3.13/c-api/long.html#c.PyLong_AsNativeBytes), [`PyLong_FromNativeBytes()`](https://docs.python.org/zh-cn/3.13/c-api/long.html#c.PyLong_FromNativeBytes), and [`PyLong_FromUnsignedNativeBytes()`](https://docs.python.org/zh-cn/3.13/c-api/long.html#c.PyLong_FromUnsignedNativeBytes) functions to simplify converting between native integer types and Python [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int) objects. (Contributed by Steve Dower in [gh-111140](https://github.com/python/cpython/issues/111140).)

- Add [`PyModule_Add()`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.PyModule_Add) function, which is similar to [`PyModule_AddObjectRef()`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.PyModule_AddObjectRef) and [`PyModule_AddObject()`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.PyModule_AddObject), but always steals a reference to the value. (Contributed by Serhiy Storchaka in [gh-86493](https://github.com/python/cpython/issues/86493).)

- Add the [`PyObject_GenericHash()`](https://docs.python.org/zh-cn/3.13/c-api/hash.html#c.PyObject_GenericHash) function that implements the default hashing function of a Python object. (Contributed by Serhiy Storchaka in [gh-113024](https://github.com/python/cpython/issues/113024).)

- Add the [`Py_HashPointer()`](https://docs.python.org/zh-cn/3.13/c-api/hash.html#c.Py_HashPointer) function to hash a raw pointer. (Contributed by Victor Stinner in [gh-111545](https://github.com/python/cpython/issues/111545).)

- Add the [`PyObject_VisitManagedDict()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_VisitManagedDict) and [`PyObject_ClearManagedDict()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_ClearManagedDict) functions. which must be called by the traverse and clear functions of a type using the [`Py_TPFLAGS_MANAGED_DICT`](https://docs.python.org/zh-cn/3.13/c-api/typeobj.html#c.Py_TPFLAGS_MANAGED_DICT) flag. The [pythoncapi-compat project](https://github.com/python/pythoncapi-compat/) can be used to use these functions with Python 3.11 and 3.12. (Contributed by Victor Stinner in [gh-107073](https://github.com/python/cpython/issues/107073).)

- Add the [`PyRefTracer_SetTracer()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyRefTracer_SetTracer) and [`PyRefTracer_GetTracer()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyRefTracer_GetTracer) functions, which enable tracking object creation and destruction in the same way that the [`tracemalloc`](https://docs.python.org/zh-cn/3.13/library/tracemalloc.html#module-tracemalloc) module does. (Contributed by Pablo Galindo in [gh-93502](https://github.com/python/cpython/issues/93502).)

- Add the [`PySys_AuditTuple()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PySys_AuditTuple) function as an alternative to [`PySys_Audit()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PySys_Audit) that takes event arguments as a Python [`tuple`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#tuple) object. (Contributed by Victor Stinner in [gh-85283](https://github.com/python/cpython/issues/85283).)

- Add the [`PyThreadState_GetUnchecked()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThreadState_GetUnchecked) function as an alternative to [`PyThreadState_Get()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThreadState_Get) that doesn't kill the process with a fatal error if it is `NULL`. The caller is responsible for checking if the result is `NULL`. (Contributed by Victor Stinner in [gh-108867](https://github.com/python/cpython/issues/108867).)

- Add the [`PyType_GetFullyQualifiedName()`](https://docs.python.org/zh-cn/3.13/c-api/type.html#c.PyType_GetFullyQualifiedName) function to get the type's fully qualified name. The module name is prepended if [`type.__module__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#type.__module__) is a string and is not equal to either `'builtins'` or `'__main__'`. (Contributed by Victor Stinner in [gh-111696](https://github.com/python/cpython/issues/111696).)

- Add the [`PyType_GetModuleName()`](https://docs.python.org/zh-cn/3.13/c-api/type.html#c.PyType_GetModuleName) function to get the type's module name. This is equivalent to getting the [`type.__module__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#type.__module__) attribute. (Contributed by Eric Snow and Victor Stinner in [gh-111696](https://github.com/python/cpython/issues/111696).)

- Add the [`PyUnicode_EqualToUTF8AndSize()`](https://docs.python.org/zh-cn/3.13/c-api/unicode.html#c.PyUnicode_EqualToUTF8AndSize) and [`PyUnicode_EqualToUTF8()`](https://docs.python.org/zh-cn/3.13/c-api/unicode.html#c.PyUnicode_EqualToUTF8) functions to compare a Unicode object with a const char* UTF-8 encoded string and `1` if they are equal or `0` otherwise. These functions do not raise exceptions. (Contributed by Serhiy Storchaka in [gh-110289](https://github.com/python/cpython/issues/110289).)

- 增加 [`PyWeakref_GetRef()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GetRef) 函数作为 [`PyWeakref_GetObject()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GetObject) 的替代，它将返回一个 [strong reference](https://docs.python.org/zh-cn/3.13/glossary.html#term-strong-reference) 或是在引用对象不再存活时返回 `NULL`。 （由 Victor Stinner 在 [gh-105927](https://github.com/python/cpython/issues/105927) 中贡献。）

- 增加了静默地忽略错误的函数的已修正变体形式：

  - [`PyObject_HasAttrWithError()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_HasAttrWithError) 替代 [`PyObject_HasAttr()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_HasAttr)。
  - [`PyObject_HasAttrStringWithError()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_HasAttrStringWithError) 替代 [`PyObject_HasAttrString()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_HasAttrString)。
  - [`PyMapping_HasKeyWithError()`](https://docs.python.org/zh-cn/3.13/c-api/mapping.html#c.PyMapping_HasKeyWithError) 替代 [`PyMapping_HasKey()`](https://docs.python.org/zh-cn/3.13/c-api/mapping.html#c.PyMapping_HasKey)。
  - [`PyMapping_HasKeyStringWithError()`](https://docs.python.org/zh-cn/3.13/c-api/mapping.html#c.PyMapping_HasKeyStringWithError) 替代 [`PyMapping_HasKeyString()`](https://docs.python.org/zh-cn/3.13/c-api/mapping.html#c.PyMapping_HasKeyString)。

  这些新函数将返回 `-1` 表示错误而返回标准的 `1` 表示真值以及 `0` 表示假值。

  （由 Serhiy Storchaka 在 [gh-108511](https://github.com/python/cpython/issues/108511) 中贡献。）

### 被改变的 C API

- The *keywords* parameter of [`PyArg_ParseTupleAndKeywords()`](https://docs.python.org/zh-cn/3.13/c-api/arg.html#c.PyArg_ParseTupleAndKeywords) and [`PyArg_VaParseTupleAndKeywords()`](https://docs.python.org/zh-cn/3.13/c-api/arg.html#c.PyArg_VaParseTupleAndKeywords) now has type char *const* in C and const char *const* in C++, instead of char**. In C++, this makes these functions compatible with arguments of type const char *const*, const char**, or char *const* without an explicit type cast. In C, the functions only support arguments of type char *const*. This can be overridden with the [`PY_CXX_CONST`](https://docs.python.org/zh-cn/3.13/c-api/arg.html#c.PY_CXX_CONST) macro. (Contributed by Serhiy Storchaka in [gh-65210](https://github.com/python/cpython/issues/65210).)

- [`PyArg_ParseTupleAndKeywords()`](https://docs.python.org/zh-cn/3.13/c-api/arg.html#c.PyArg_ParseTupleAndKeywords) now supports non-ASCII keyword parameter names. (Contributed by Serhiy Storchaka in [gh-110815](https://github.com/python/cpython/issues/110815).)

- The `PyCode_GetFirstFree()` function is now unstable API and is now named [`PyUnstable_Code_GetFirstFree()`](https://docs.python.org/zh-cn/3.13/c-api/code.html#c.PyUnstable_Code_GetFirstFree). (Contributed by Bogdan Romanyuk in [gh-115781](https://github.com/python/cpython/issues/115781).)

- The [`PyDict_GetItem()`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDict_GetItem), [`PyDict_GetItemString()`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDict_GetItemString), [`PyMapping_HasKey()`](https://docs.python.org/zh-cn/3.13/c-api/mapping.html#c.PyMapping_HasKey), [`PyMapping_HasKeyString()`](https://docs.python.org/zh-cn/3.13/c-api/mapping.html#c.PyMapping_HasKeyString), [`PyObject_HasAttr()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_HasAttr), [`PyObject_HasAttrString()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_HasAttrString), and [`PySys_GetObject()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PySys_GetObject) functions, each of which clears all errors which occurred when calling them now reports these errors using [`sys.unraisablehook()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.unraisablehook). You may replace them with other functions as recommended in the documentation. (Contributed by Serhiy Storchaka in [gh-106672](https://github.com/python/cpython/issues/106672).)

- Add support for the `%T`, `%#T`, `%N` and `%#N` formats to [`PyUnicode_FromFormat()`](https://docs.python.org/zh-cn/3.13/c-api/unicode.html#c.PyUnicode_FromFormat):

  - `%T`: Get the fully qualified name of an object type
  - `%#T`: As above, but use a colon as the separator
  - `%N`: Get the fully qualified name of a type
  - `%#N`: As above, but use a colon as the separator

  See [**PEP 737**](https://peps.python.org/pep-0737/) for more information. (Contributed by Victor Stinner in [gh-111696](https://github.com/python/cpython/issues/111696).)

- You no longer have to define the `PY_SSIZE_T_CLEAN` macro before including `Python.h` when using `#` formats in [format codes](https://docs.python.org/zh-cn/3.13/c-api/arg.html#arg-parsing-string-and-buffers). APIs accepting the format codes always use `Py_ssize_t` for `#` formats. (Contributed by Inada Naoki in [gh-104922](https://github.com/python/cpython/issues/104922).)

- 如果 Python 是使用 [调试模式](https://docs.python.org/zh-cn/3.13/using/configure.html#debug-build) 或 [`附带断言`](https://docs.python.org/zh-cn/3.13/using/configure.html#cmdoption-with-assertions) 构建的，[`PyTuple_SET_ITEM()`](https://docs.python.org/zh-cn/3.13/c-api/tuple.html#c.PyTuple_SET_ITEM) 和 [`PyList_SET_ITEM()`](https://docs.python.org/zh-cn/3.13/c-api/list.html#c.PyList_SET_ITEM) 现在将通过一个断言来检查 index 参数。 （由 Victor Stinner 在 [gh-106168](https://github.com/python/cpython/issues/106168) 中贡献。）

### 受限 C API 的改变

- 下列函数现在被包括在受限 C API 中：

  - [`PyMem_RawMalloc()`](https://docs.python.org/zh-cn/3.13/c-api/memory.html#c.PyMem_RawMalloc)
  - [`PyMem_RawCalloc()`](https://docs.python.org/zh-cn/3.13/c-api/memory.html#c.PyMem_RawCalloc)
  - [`PyMem_RawRealloc()`](https://docs.python.org/zh-cn/3.13/c-api/memory.html#c.PyMem_RawRealloc)
  - [`PyMem_RawFree()`](https://docs.python.org/zh-cn/3.13/c-api/memory.html#c.PyMem_RawFree)
  - [`PySys_Audit()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PySys_Audit)
  - [`PySys_AuditTuple()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PySys_AuditTuple)
  - [`PyType_GetModuleByDef()`](https://docs.python.org/zh-cn/3.13/c-api/type.html#c.PyType_GetModuleByDef)

  （由 Victor Stinner 在 [gh-85283](https://github.com/python/cpython/issues/85283), [gh-85283](https://github.com/python/cpython/issues/85283) 和 [gh-116936](https://github.com/python/cpython/issues/116936) 中贡献。）

- 使用 [`--with-trace-refs`](https://docs.python.org/zh-cn/3.13/using/configure.html#cmdoption-with-trace-refs) (跟踪引用) 构建的 Python 现在支持 [受限 API](https://docs.python.org/zh-cn/3.13/c-api/stable.html#limited-c-api)。 （由 Victor Stinner 在 [gh-108634](https://github.com/python/cpython/issues/108634) 中贡献。）

### Removed C APIs

- Remove several functions, macros, variables, etc with names prefixed by `_Py` or `_PY` (which are considered private). If your project is affected by one of these removals and you believe that the removed API should remain available, please [open a new issue](https://docs.python.org/zh-cn/3.13/bugs.html#using-the-tracker) to request a public C API and add `cc: @vstinner` to the issue to notify Victor Stinner. (Contributed by Victor Stinner in [gh-106320](https://github.com/python/cpython/issues/106320).)

- Remove old buffer protocols deprecated in Python 3.0. Use [缓冲协议](https://docs.python.org/zh-cn/3.13/c-api/buffer.html#bufferobjects) instead.

  - `PyObject_CheckReadBuffer()`: Use [`PyObject_CheckBuffer()`](https://docs.python.org/zh-cn/3.13/c-api/buffer.html#c.PyObject_CheckBuffer) to test whether the object supports the buffer protocol. Note that [`PyObject_CheckBuffer()`](https://docs.python.org/zh-cn/3.13/c-api/buffer.html#c.PyObject_CheckBuffer) doesn't guarantee that [`PyObject_GetBuffer()`](https://docs.python.org/zh-cn/3.13/c-api/buffer.html#c.PyObject_GetBuffer) will succeed. To test if the object is actually readable, see the next example of [`PyObject_GetBuffer()`](https://docs.python.org/zh-cn/3.13/c-api/buffer.html#c.PyObject_GetBuffer).

  - `PyObject_AsCharBuffer()`, `PyObject_AsReadBuffer()`: Use [`PyObject_GetBuffer()`](https://docs.python.org/zh-cn/3.13/c-api/buffer.html#c.PyObject_GetBuffer) and [`PyBuffer_Release()`](https://docs.python.org/zh-cn/3.13/c-api/buffer.html#c.PyBuffer_Release) instead:

    ```
    Py_buffer view;
    if (PyObject_GetBuffer(obj, &view, PyBUF_SIMPLE) < 0) {
        return NULL;
    }
    // Use `view.buf` and `view.len` to read from the buffer.
    // You may need to cast buf as `(const char*)view.buf`.
    PyBuffer_Release(&view);
    ```

  - `PyObject_AsWriteBuffer()`: Use [`PyObject_GetBuffer()`](https://docs.python.org/zh-cn/3.13/c-api/buffer.html#c.PyObject_GetBuffer) and [`PyBuffer_Release()`](https://docs.python.org/zh-cn/3.13/c-api/buffer.html#c.PyBuffer_Release) instead:

    ```
    Py_buffer view;
    if (PyObject_GetBuffer(obj, &view, PyBUF_WRITABLE) < 0) {
        return NULL;
    }
    // Use `view.buf` and `view.len` to write to the buffer.
    PyBuffer_Release(&view);
    ```

  (Contributed by Inada Naoki in [gh-85275](https://github.com/python/cpython/issues/85275).)

- 删除了在 Python 3.9 中弃用的各种函数：

  - `PyEval_CallObject()`, `PyEval_CallObjectWithKeywords()`: Use [`PyObject_CallNoArgs()`](https://docs.python.org/zh-cn/3.13/c-api/call.html#c.PyObject_CallNoArgs) or [`PyObject_Call()`](https://docs.python.org/zh-cn/3.13/c-api/call.html#c.PyObject_Call) instead.

    ​	警告

     

    ​	In [`PyObject_Call()`](https://docs.python.org/zh-cn/3.13/c-api/call.html#c.PyObject_Call), positional arguments must be a [`tuple`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#tuple) and must not be `NULL`, and keyword arguments must be a [`dict`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#dict) or `NULL`, whereas the removed functions checked argument types and accepted `NULL` positional and keyword arguments. To replace `PyEval_CallObjectWithKeywords(func, NULL, kwargs)` with [`PyObject_Call()`](https://docs.python.org/zh-cn/3.13/c-api/call.html#c.PyObject_Call), pass an empty tuple as positional arguments using [`PyTuple_New(0)`](https://docs.python.org/zh-cn/3.13/c-api/tuple.html#c.PyTuple_New).

  - `PyEval_CallFunction()`: Use [`PyObject_CallFunction()`](https://docs.python.org/zh-cn/3.13/c-api/call.html#c.PyObject_CallFunction) instead.

  - `PyEval_CallMethod()`: Use [`PyObject_CallMethod()`](https://docs.python.org/zh-cn/3.13/c-api/call.html#c.PyObject_CallMethod) instead.

  - `PyCFunction_Call()`: Use [`PyObject_Call()`](https://docs.python.org/zh-cn/3.13/c-api/call.html#c.PyObject_Call) instead.

  (Contributed by Victor Stinner in [gh-105107](https://github.com/python/cpython/issues/105107).)

- Remove the following old functions to configure the Python initialization, deprecated in Python 3.11:

  - `PySys_AddWarnOptionUnicode()`: Use [`PyConfig.warnoptions`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.warnoptions) instead.
  - `PySys_AddWarnOption()`: Use [`PyConfig.warnoptions`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.warnoptions) instead.
  - `PySys_AddXOption()`: Use [`PyConfig.xoptions`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.xoptions) instead.
  - `PySys_HasWarnOptions()`: Use [`PyConfig.xoptions`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.xoptions) instead.
  - `PySys_SetPath()`: Set [`PyConfig.module_search_paths`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.module_search_paths) instead.
  - `Py_SetPath()`: Set [`PyConfig.module_search_paths`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.module_search_paths) instead.
  - `Py_SetStandardStreamEncoding()`: Set [`PyConfig.stdio_encoding`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.stdio_encoding) instead, and set also maybe [`PyConfig.legacy_windows_stdio`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.legacy_windows_stdio) (on Windows).
  - `_Py_SetProgramFullPath()`: Set [`PyConfig.executable`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.executable) instead.

  Use the new [`PyConfig`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig) API of the [Python Initialization Configuration](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#init-config) instead ([**PEP 587**](https://peps.python.org/pep-0587/)), added to Python 3.8. (Contributed by Victor Stinner in [gh-105145](https://github.com/python/cpython/issues/105145).)

- Remove `PyEval_AcquireLock()` and `PyEval_ReleaseLock()` functions, deprecated in Python 3.2. They didn't update the current thread state. They can be replaced with:

  - [`PyEval_SaveThread()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyEval_SaveThread) and [`PyEval_RestoreThread()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyEval_RestoreThread);
  - low-level [`PyEval_AcquireThread()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyEval_AcquireThread) and [`PyEval_RestoreThread()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyEval_RestoreThread);
  - or [`PyGILState_Ensure()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyGILState_Ensure) and [`PyGILState_Release()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyGILState_Release).

  (Contributed by Victor Stinner in [gh-105182](https://github.com/python/cpython/issues/105182).)

- 删除了在 Python 3.9 弃用的:c:func:!PyEval_ThreadsInitialized 函数。自Python 3.7 起，`Py_Initialize()` 总是创建全局解释器锁 ：调用:c:func:!PyEval_InitThreads 不做任何事情，而:c:func:!PyEval_ThreadsInitialized 总返回非零值。(由 Victor Stinner 在:gh:105182 中贡献。）

- Remove the `_PyInterpreterState_Get()` alias to [`PyInterpreterState_Get()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyInterpreterState_Get) which was kept for backward compatibility with Python 3.8. The [pythoncapi-compat project](https://github.com/python/pythoncapi-compat/) can be used to get [`PyInterpreterState_Get()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyInterpreterState_Get) on Python 3.8 and older. (Contributed by Victor Stinner in [gh-106320](https://github.com/python/cpython/issues/106320).)

- Remove the private `_PyObject_FastCall()` function: use `PyObject_Vectorcall()` which is available since Python 3.8 ([**PEP 590**](https://peps.python.org/pep-0590/)). (Contributed by Victor Stinner in [gh-106023](https://github.com/python/cpython/issues/106023).)

- Remove the `cpython/pytime.h` header file, which only contained private functions. (Contributed by Victor Stinner in [gh-106316](https://github.com/python/cpython/issues/106316).)

- Remove the undocumented `PY_TIMEOUT_MAX` constant from the limited C API. (Contributed by Victor Stinner in [gh-110014](https://github.com/python/cpython/issues/110014).)

- Remove the old trashcan macros `Py_TRASHCAN_SAFE_BEGIN` and `Py_TRASHCAN_SAFE_END`. Replace both with the new macros `Py_TRASHCAN_BEGIN` and `Py_TRASHCAN_END`. (Contributed by Irit Katriel in [gh-105111](https://github.com/python/cpython/issues/105111).)

### 已弃用的 C API

- 已弃用旧的 Python 初始化函数：

  - [`PySys_ResetWarnOptions()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PySys_ResetWarnOptions): 改为清除 [`sys.warnoptions`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.warnoptions) 和 `warnings.filters`。
  - [`Py_GetExecPrefix()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_GetExecPrefix): 改为获取 [`sys.exec_prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.exec_prefix)。
  - [`Py_GetPath()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_GetPath): 改为获取 [`sys.path`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.path)。
  - [`Py_GetPrefix()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_GetPrefix): 改为获取 [`sys.prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.prefix)。
  - [`Py_GetProgramFullPath()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_GetProgramFullPath): 改为获取 [`sys.executable`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.executable)。
  - [`Py_GetProgramName()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_GetProgramName): 改为获取 [`sys.executable`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.executable)。
  - [`Py_GetPythonHome()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_GetPythonHome): 改为获取 [`PyConfig.home`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.home) 或 [`PYTHONHOME`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONHOME) 环境变量。

  （由 Victor Stinner 在 [gh-105145](https://github.com/python/cpython/issues/105145) 中贡献。）

- [Soft deprecate](https://docs.python.org/zh-cn/3.13/glossary.html#term-soft-deprecated) the [`PyEval_GetBuiltins()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetBuiltins), [`PyEval_GetGlobals()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetGlobals), and [`PyEval_GetLocals()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetLocals) functions, which return a [borrowed reference](https://docs.python.org/zh-cn/3.13/glossary.html#term-borrowed-reference). (Soft deprecated as part of [**PEP 667**](https://peps.python.org/pep-0667/).)

- Deprecate the [`PyImport_ImportModuleNoBlock()`](https://docs.python.org/zh-cn/3.13/c-api/import.html#c.PyImport_ImportModuleNoBlock) function, which is just an alias to [`PyImport_ImportModule()`](https://docs.python.org/zh-cn/3.13/c-api/import.html#c.PyImport_ImportModule) since Python 3.3. (Contributed by Victor Stinner in [gh-105396](https://github.com/python/cpython/issues/105396).)

- [Soft deprecate](https://docs.python.org/zh-cn/3.13/glossary.html#term-soft-deprecated) the [`PyModule_AddObject()`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.PyModule_AddObject) function. It should be replaced with [`PyModule_Add()`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.PyModule_Add) or [`PyModule_AddObjectRef()`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.PyModule_AddObjectRef). (Contributed by Serhiy Storchaka in [gh-86493](https://github.com/python/cpython/issues/86493).)

- Deprecate the old `Py_UNICODE` and `PY_UNICODE_TYPE` types and the `Py_UNICODE_WIDE` define. Use the `wchar_t` type directly instead. Since Python 3.3, `Py_UNICODE` and `PY_UNICODE_TYPE` are just aliases to `wchar_t`. (Contributed by Victor Stinner in [gh-105156](https://github.com/python/cpython/issues/105156).)

- Deprecate the [`PyWeakref_GetObject()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GetObject) and [`PyWeakref_GET_OBJECT()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GET_OBJECT) functions, which return a [borrowed reference](https://docs.python.org/zh-cn/3.13/glossary.html#term-borrowed-reference). Replace them with the new [`PyWeakref_GetRef()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GetRef) function, which returns a [strong reference](https://docs.python.org/zh-cn/3.13/glossary.html#term-strong-reference). The [pythoncapi-compat project](https://github.com/python/pythoncapi-compat/) can be used to get [`PyWeakref_GetRef()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GetRef) on Python 3.12 and older. (Contributed by Victor Stinner in [gh-105927](https://github.com/python/cpython/issues/105927).)

#### 计划在 Python 3.14 中移除

- [`PyDictObject`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDictObject) 中的 `ma_version_tag` 字段用于扩展模块 ( [**PEP 699**](https://peps.python.org/pep-0699/) ; [gh-101193](https://github.com/python/cpython/issues/101193) )。

- 创建 [`immutable types`](https://docs.python.org/zh-cn/3.13/c-api/typeobj.html#c.Py_TPFLAGS_IMMUTABLETYPE) 的可变基础 ( [gh-95388](https://github.com/python/cpython/issues/95388) )。

- 用于配置 Python 的初始化的函数，在 Python 3.11 中已弃用：

  - `PySys_SetArgvEx()`: 改为设置 [`PyConfig.argv`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.argv)。
  - `PySys_SetArgv()`: 改为设置 [`PyConfig.argv`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.argv)。
  - `Py_SetProgramName()`: 改为设置 [`PyConfig.program_name`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.program_name)。
  - `Py_SetPythonHome()`: 改为设置 [`PyConfig.home`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.home)。

  [`Py_InitializeFromConfig()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_InitializeFromConfig) API 应与 [`PyConfig`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig) 一起使用。

- 全局配置变量：

  - [`Py_DebugFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_DebugFlag): 改用 [`PyConfig.parser_debug`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.parser_debug)。
  - [`Py_VerboseFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_VerboseFlag): 改用 [`PyConfig.verbose`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.verbose)。
  - [`Py_QuietFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_QuietFlag): 改用 [`PyConfig.quiet`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.quiet)。
  - [`Py_InteractiveFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_InteractiveFlag): 改用 [`PyConfig.interactive`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.interactive)。
  - [`Py_InspectFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_InspectFlag): 改用 [`PyConfig.inspect`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.inspect)。
  - [`Py_OptimizeFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_OptimizeFlag): 改用 [`PyConfig.optimization_level`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.optimization_level)。
  - [`Py_NoSiteFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_NoSiteFlag): 改用 [`PyConfig.site_import`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.site_import)。
  - [`Py_BytesWarningFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_BytesWarningFlag): 改用 [`PyConfig.bytes_warning`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.bytes_warning)。
  - [`Py_FrozenFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_FrozenFlag): 改用 [`PyConfig.pathconfig_warnings`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.pathconfig_warnings)。
  - [`Py_IgnoreEnvironmentFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_IgnoreEnvironmentFlag): 改用 [`PyConfig.use_environment`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.use_environment)。
  - [`Py_DontWriteBytecodeFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_DontWriteBytecodeFlag): 改用 [`PyConfig.write_bytecode`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.write_bytecode)。
  - [`Py_NoUserSiteDirectory`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_NoUserSiteDirectory): 改用 [`PyConfig.user_site_directory`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.user_site_directory)。
  - [`Py_UnbufferedStdioFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_UnbufferedStdioFlag): 改用 [`PyConfig.buffered_stdio`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.buffered_stdio)。
  - [`Py_HashRandomizationFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_HashRandomizationFlag): 改用 [`PyConfig.use_hash_seed`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.use_hash_seed) 和 [`PyConfig.hash_seed`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.hash_seed)。
  - [`Py_IsolatedFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_IsolatedFlag): 改用 [`PyConfig.isolated`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.isolated)。
  - [`Py_LegacyWindowsFSEncodingFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_LegacyWindowsFSEncodingFlag): 改用 [`PyPreConfig.legacy_windows_fs_encoding`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyPreConfig.legacy_windows_fs_encoding)。
  - [`Py_LegacyWindowsStdioFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_LegacyWindowsStdioFlag): 改用 [`PyConfig.legacy_windows_stdio`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.legacy_windows_stdio)。
  - `Py_FileSystemDefaultEncoding`: 改用 [`PyConfig.filesystem_encoding`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.filesystem_encoding)。
  - `Py_HasFileSystemDefaultEncoding`: 改用 [`PyConfig.filesystem_encoding`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.filesystem_encoding)。
  - `Py_FileSystemDefaultEncodeErrors`: 改用 [`PyConfig.filesystem_errors`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.filesystem_errors)。
  - `Py_UTF8Mode`: 改用 [`PyPreConfig.utf8_mode`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyPreConfig.utf8_mode)。 (参见 [`Py_PreInitialize()`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.Py_PreInitialize))

  [`Py_InitializeFromConfig()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_InitializeFromConfig) API 应与 [`PyConfig`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig) 一起使用。

#### Python 3.15 中的待移除功能

- 捆绑的 `libmpdecimal` 副本。
- The [`PyImport_ImportModuleNoBlock()`](https://docs.python.org/zh-cn/3.13/c-api/import.html#c.PyImport_ImportModuleNoBlock): 改用 [`PyImport_ImportModule()`](https://docs.python.org/zh-cn/3.13/c-api/import.html#c.PyImport_ImportModule)。
- [`PyWeakref_GetObject()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GetObject) 和 [`PyWeakref_GET_OBJECT()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GET_OBJECT): 改用 [`PyWeakref_GetRef()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GetRef)。
- [`Py_UNICODE`](https://docs.python.org/zh-cn/3.13/c-api/unicode.html#c.Py_UNICODE) 类型和 `Py_UNICODE_WIDE` 宏：改用 `wchar_t`。
- Python 初始化函数
  - [`PySys_ResetWarnOptions()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PySys_ResetWarnOptions): 改为清除 [`sys.warnoptions`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.warnoptions) 和 `warnings.filters`。
  - [`Py_GetExecPrefix()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_GetExecPrefix): Get [`sys.base_exec_prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.base_exec_prefix) and [`sys.exec_prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.exec_prefix) instead.
  - [`Py_GetPath()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_GetPath): 改为获取 [`sys.path`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.path)。
  - [`Py_GetPrefix()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_GetPrefix): Get [`sys.base_prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.base_prefix) and [`sys.prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.prefix) instead.
  - [`Py_GetProgramFullPath()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_GetProgramFullPath): 改为获取 [`sys.executable`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.executable)。
  - [`Py_GetProgramName()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_GetProgramName): 改为获取 [`sys.executable`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.executable)。
  - [`Py_GetPythonHome()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_GetPythonHome): 改为获取 [`PyConfig.home`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.home) 或 [`PYTHONHOME`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONHOME) 环境变量。

#### 计划在未来版本中移除

​	以下 API 已被弃用，将被移除，但目前尚未确定移除日期。

- [`Py_TPFLAGS_HAVE_FINALIZE`](https://docs.python.org/zh-cn/3.13/c-api/typeobj.html#c.Py_TPFLAGS_HAVE_FINALIZE): 自 Python 3.8 起不再需要。
- [`PyErr_Fetch()`](https://docs.python.org/zh-cn/3.13/c-api/exceptions.html#c.PyErr_Fetch): 改用 [`PyErr_GetRaisedException()`](https://docs.python.org/zh-cn/3.13/c-api/exceptions.html#c.PyErr_GetRaisedException)。
- [`PyErr_NormalizeException()`](https://docs.python.org/zh-cn/3.13/c-api/exceptions.html#c.PyErr_NormalizeException): 改用 [`PyErr_GetRaisedException()`](https://docs.python.org/zh-cn/3.13/c-api/exceptions.html#c.PyErr_GetRaisedException)。
- [`PyErr_Restore()`](https://docs.python.org/zh-cn/3.13/c-api/exceptions.html#c.PyErr_Restore): 改用 [`PyErr_SetRaisedException()`](https://docs.python.org/zh-cn/3.13/c-api/exceptions.html#c.PyErr_SetRaisedException)。
- [`PyModule_GetFilename()`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.PyModule_GetFilename): 改用 [`PyModule_GetFilenameObject()`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.PyModule_GetFilenameObject)。
- [`PyOS_AfterFork()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PyOS_AfterFork): 改用 [`PyOS_AfterFork_Child()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PyOS_AfterFork_Child)。
- [`PySlice_GetIndicesEx()`](https://docs.python.org/zh-cn/3.13/c-api/slice.html#c.PySlice_GetIndicesEx): 改用 [`PySlice_Unpack()`](https://docs.python.org/zh-cn/3.13/c-api/slice.html#c.PySlice_Unpack) and [`PySlice_AdjustIndices()`](https://docs.python.org/zh-cn/3.13/c-api/slice.html#c.PySlice_AdjustIndices)。
- `PyUnicode_AsDecodedObject()`: 改用 [`PyCodec_Decode()`](https://docs.python.org/zh-cn/3.13/c-api/codec.html#c.PyCodec_Decode)。
- `PyUnicode_AsDecodedUnicode()`: 改用 [`PyCodec_Decode()`](https://docs.python.org/zh-cn/3.13/c-api/codec.html#c.PyCodec_Decode)。
- `PyUnicode_AsEncodedObject()`: 改用 [`PyCodec_Encode()`](https://docs.python.org/zh-cn/3.13/c-api/codec.html#c.PyCodec_Encode)。
- `PyUnicode_AsEncodedUnicode()`: 改用 [`PyCodec_Encode()`](https://docs.python.org/zh-cn/3.13/c-api/codec.html#c.PyCodec_Encode)。
- [`PyUnicode_READY()`](https://docs.python.org/zh-cn/3.13/c-api/unicode.html#c.PyUnicode_READY): 自 Python 3.12 起不再需要
- `PyErr_Display()`: 改用 [`PyErr_DisplayException()`](https://docs.python.org/zh-cn/3.13/c-api/exceptions.html#c.PyErr_DisplayException)。
- `_PyErr_ChainExceptions()`: 改用 `_PyErr_ChainExceptions1()`。
- `PyBytesObject.ob_shash` 成员：改为调用 [`PyObject_Hash()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_Hash)。
- `PyDictObject.ma_version_tag` 成员。
- 线程本地存储 (TLS) API：
  - [`PyThread_create_key()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThread_create_key): 改用 [`PyThread_tss_alloc()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThread_tss_alloc)。
  - [`PyThread_delete_key()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThread_delete_key): 改用 [`PyThread_tss_free()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThread_tss_free)。
  - [`PyThread_set_key_value()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThread_set_key_value): 改用 [`PyThread_tss_set()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThread_tss_set)。
  - [`PyThread_get_key_value()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThread_get_key_value): 改用 [`PyThread_tss_get()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThread_tss_get)。
  - [`PyThread_delete_key_value()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThread_delete_key_value): 改用 [`PyThread_tss_delete()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThread_tss_delete)。
  - [`PyThread_ReInitTLS()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThread_ReInitTLS): 自 Python 3.7 起不再需要。

## 构建变化

- `arm64-apple-ios` and `arm64-apple-ios-simulator` are both now [**PEP 11**](https://peps.python.org/pep-0011/) tier 3 platforms. ([PEP 730](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#whatsnew313-platform-support) written and implementation contributed by Russell Keith-Magee in [gh-114099](https://github.com/python/cpython/issues/114099).)
- `aarch64-linux-android` and `x86_64-linux-android` are both now [**PEP 11**](https://peps.python.org/pep-0011/) tier 3 platforms. ([PEP 738](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#whatsnew313-platform-support) written and implementation contributed by Malcolm Smith in [gh-116622](https://github.com/python/cpython/issues/116622).)
- 现在 `wasm32-wasi` 是 [**PEP 11**](https://peps.python.org/pep-0011/) 第 2 层级的平台。 （由 Brett Cannon 在 [gh-115192](https://github.com/python/cpython/issues/115192) 中贡献。）
- `wasm32-emscripten` 不再是 [**PEP 11**](https://peps.python.org/pep-0011/) 的受支持平台。 （由 Brett Cannon 在 [gh-115192](https://github.com/python/cpython/issues/115192) 中贡献。）
- 现在构建 CPython 需要带有 C11 atomic 库支持的编译器、GCC 内置 atomic 函数或 MSVC 互锁内生函数。
- Autoconf 2.71 and aclocal 1.16.5 are now required to regenerate the `configure` script. (Contributed by Christian Heimes in [gh-89886](https://github.com/python/cpython/issues/89886) and by Victor Stinner in [gh-112090](https://github.com/python/cpython/issues/112090).)
- 需要 SQLite 3.15.2 或更新的版本才能构建 [`sqlite3`](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#module-sqlite3) 扩展模块。 （由 Erlend Aasland 在 [gh-105875](https://github.com/python/cpython/issues/105875) 中贡献。）
- CPython now bundles the [mimalloc library](https://github.com/microsoft/mimalloc/) by default. It is licensed under the MIT license; see [mimalloc license](https://docs.python.org/zh-cn/3.13/license.html#mimalloc-license). The bundled mimalloc has custom changes, see [gh-113141](https://github.com/python/cpython/issues/113141) for details. (Contributed by Dino Viehland in [gh-109914](https://github.com/python/cpython/issues/109914).)
- 现在 `configure` 选项 [`--with-system-libmpdec`](https://docs.python.org/zh-cn/3.13/using/configure.html#cmdoption-with-system-libmpdec) 默认为 `yes`。 捆绑的 `libmpdecimal` 副本将在 Python 3.15 中被移除。
- 使用 `configure` [`--with-trace-refs`](https://docs.python.org/zh-cn/3.13/using/configure.html#cmdoption-with-trace-refs) (跟踪引用) 构建的 Python 现在与 Python 发布构建版和 [调试构建版](https://docs.python.org/zh-cn/3.13/using/configure.html#debug-build) 是 ABI 兼容的。 （由 Victor Stinner 在 [gh-108634](https://github.com/python/cpython/issues/108634) 中贡献。）
- On POSIX systems, the pkg-config (`.pc`) filenames now include the ABI flags. For example, the free-threaded build generates `python-3.13t.pc` and the debug build generates `python-3.13d.pc`.
- 现在 `errno`, `fcntl`, `grp`, `md5`, `pwd`, `resource`, `termios`, `winsound`, `_ctypes_test`, `_multiprocessing.posixshmem`, `_scproxy`, `_stat`, `_statistics`, `_testconsole`, `_testimportmultiple` 和 `_uuid` C 扩展是使用 [受限 C API](https://docs.python.org/zh-cn/3.13/c-api/stable.html#limited-c-api) 构建的。 （由 Victor Stinner 在 [gh-85283](https://github.com/python/cpython/issues/85283) 中贡献。）

## Porting to Python 3.13

​	本节列出了先前描述的更改以及可能需要更改代码的其他错误修正.

### Python API 的变化

- [PEP 667](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#whatsnew313-locals-semantics) introduces several changes to the semantics of [`locals()`](https://docs.python.org/zh-cn/3.13/library/functions.html#locals) and [`f_locals`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame.f_locals):

  - Calling [`locals()`](https://docs.python.org/zh-cn/3.13/library/functions.html#locals) in an [optimized scope](https://docs.python.org/zh-cn/3.13/glossary.html#term-optimized-scope) now produces an independent snapshot on each call, and hence no longer implicitly updates previously returned references. Obtaining the legacy CPython behavior now requires explicit calls to update the initially returned dictionary with the results of subsequent calls to `locals()`. Code execution functions that implicitly target `locals()` (such as `exec` and `eval`) must be passed an explicit namespace to access their results in an optimized scope. (Changed as part of [**PEP 667**](https://peps.python.org/pep-0667/).)
  - Calling [`locals()`](https://docs.python.org/zh-cn/3.13/library/functions.html#locals) from a comprehension at module or class scope (including via `exec` or `eval`) once more behaves as if the comprehension were running as an independent nested function (i.e. the local variables from the containing scope are not included). In Python 3.12, this had changed to include the local variables from the containing scope when implementing [**PEP 709**](https://peps.python.org/pep-0709/). (Changed as part of [**PEP 667**](https://peps.python.org/pep-0667/).)
  - Accessing [`FrameType.f_locals`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame.f_locals) in an [optimized scope](https://docs.python.org/zh-cn/3.13/glossary.html#term-optimized-scope) now returns a write-through proxy rather than a snapshot that gets updated at ill-specified times. If a snapshot is desired, it must be created explicitly with `dict` or the proxy's `.copy()` method. (Changed as part of [**PEP 667**](https://peps.python.org/pep-0667/).)

- [`functools.partial`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.partial) now emits a [`FutureWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#FutureWarning) when used as a method. The behavior will change in future Python versions. Wrap it in [`staticmethod()`](https://docs.python.org/zh-cn/3.13/library/functions.html#staticmethod) if you want to preserve the old behavior. (Contributed by Serhiy Storchaka in [gh-121027](https://github.com/python/cpython/issues/121027).)

- An [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) is now raised by [`getpass.getuser()`](https://docs.python.org/zh-cn/3.13/library/getpass.html#getpass.getuser) for any failure to retrieve a username, instead of [`ImportError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ImportError) on non-Unix platforms or [`KeyError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#KeyError) on Unix platforms where the password database is empty.

- The value of the `mode` attribute of [`gzip.GzipFile`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.GzipFile) is now a string (`'rb'` or `'wb'`) instead of an integer (`1` or `2`). The value of the `mode` attribute of the readable file-like object returned by [`zipfile.ZipFile.open()`](https://docs.python.org/zh-cn/3.13/library/zipfile.html#zipfile.ZipFile.open) is now `'rb'` instead of `'r'`. (Contributed by Serhiy Storchaka in [gh-115961](https://github.com/python/cpython/issues/115961).)

- [`mailbox.Maildir`](https://docs.python.org/zh-cn/3.13/library/mailbox.html#mailbox.Maildir) now ignores files with a leading dot (`.`). (Contributed by Zackery Spytz in [gh-65559](https://github.com/python/cpython/issues/65559).)

- [`pathlib.Path.glob()`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.Path.glob) and [`rglob()`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.Path.rglob) now return both files and directories if a pattern that ends with "`**`" is given, rather than directories only. Add a trailing slash to keep the previous behavior and only match directories.

- The [`threading`](https://docs.python.org/zh-cn/3.13/library/threading.html#module-threading) module now expects the `_thread` module to have an `_is_main_interpreter()` function. This function takes no arguments and returns `True` if the current interpreter is the main interpreter.

  Any library or application that provides a custom `_thread` module must provide `_is_main_interpreter()`, just like the module's other "private" attributes. ([gh-112826](https://github.com/python/cpython/issues/112826).)

### C API 的变化

- `Python.h` no longer includes the `<ieeefp.h>` standard header. It was included for the `finite()` function which is now provided by the `<math.h>` header. It should now be included explicitly if needed. Remove also the `HAVE_IEEEFP_H` macro. (Contributed by Victor Stinner in [gh-108765](https://github.com/python/cpython/issues/108765).)

- `Python.h` no longer includes these standard header files: `<time.h>`, `<sys/select.h>` and `<sys/time.h>`. If needed, they should now be included explicitly. For example, `<time.h>` provides the `clock()` and `gmtime()` functions, `<sys/select.h>` provides the `select()` function, and `<sys/time.h>` provides the `futimes()`, `gettimeofday()` and `setitimer()` functions. (Contributed by Victor Stinner in [gh-108765](https://github.com/python/cpython/issues/108765).)

- On Windows, `Python.h` no longer includes the `<stddef.h>` standard header file. If needed, it should now be included explicitly. For example, it provides `offsetof()` function, and `size_t` and `ptrdiff_t` types. Including `<stddef.h>` explicitly was already needed by all other platforms, the `HAVE_STDDEF_H` macro is only defined on Windows. (Contributed by Victor Stinner in [gh-108765](https://github.com/python/cpython/issues/108765).)

- If the [`Py_LIMITED_API`](https://docs.python.org/zh-cn/3.13/c-api/stable.html#c.Py_LIMITED_API) macro is defined, `Py_BUILD_CORE`, `Py_BUILD_CORE_BUILTIN` and `Py_BUILD_CORE_MODULE` macros are now undefined by `<Python.h>`. (Contributed by Victor Stinner in [gh-85283](https://github.com/python/cpython/issues/85283).)

- The old trashcan macros `Py_TRASHCAN_SAFE_BEGIN` and `Py_TRASHCAN_SAFE_END` were removed. They should be replaced by the new macros `Py_TRASHCAN_BEGIN` and `Py_TRASHCAN_END`.

  A `tp_dealloc` function that has the old macros, such as:

  ```
  static void
  mytype_dealloc(mytype *p)
  {
      PyObject_GC_UnTrack(p);
      Py_TRASHCAN_SAFE_BEGIN(p);
      ...
      Py_TRASHCAN_SAFE_END
  }
  ```

  应当按照以下方式迁移到新版宏:

  ```
  static void
  mytype_dealloc(mytype *p)
  {
      PyObject_GC_UnTrack(p);
      Py_TRASHCAN_BEGIN(p, mytype_dealloc)
      ...
      Py_TRASHCAN_END
  }
  ```

  Note that `Py_TRASHCAN_BEGIN` has a second argument which should be the deallocation function it is in. The new macros were added in Python 3.8 and the old macros were deprecated in Python 3.11. (Contributed by Irit Katriel in [gh-105111](https://github.com/python/cpython/issues/105111).)

- [PEP 667](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html#whatsnew313-locals-semantics) introduces several changes to frame-related functions:
  - The effects of mutating the dictionary returned from [`PyEval_GetLocals()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetLocals) in an [optimized scope](https://docs.python.org/zh-cn/3.13/glossary.html#term-optimized-scope) have changed. New dict entries added this way will now *only* be visible to subsequent [`PyEval_GetLocals()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetLocals) calls in that frame, as [`PyFrame_GetLocals()`](https://docs.python.org/zh-cn/3.13/c-api/frame.html#c.PyFrame_GetLocals), [`locals()`](https://docs.python.org/zh-cn/3.13/library/functions.html#locals), and [`FrameType.f_locals`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame.f_locals) no longer access the same underlying cached dictionary. Changes made to entries for actual variable names and names added via the write-through proxy interfaces will be overwritten on subsequent calls to [`PyEval_GetLocals()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetLocals) in that frame. The recommended code update depends on how the function was being used, so refer to the deprecation notice on the function for details.
  - Calling [`PyFrame_GetLocals()`](https://docs.python.org/zh-cn/3.13/c-api/frame.html#c.PyFrame_GetLocals) in an [optimized scope](https://docs.python.org/zh-cn/3.13/glossary.html#term-optimized-scope) now returns a write-through proxy rather than a snapshot that gets updated at ill-specified times. If a snapshot is desired, it must be created explicitly (e.g. with [`PyDict_Copy()`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDict_Copy)), or by calling the new [`PyEval_GetFrameLocals()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetFrameLocals) API.
  - `PyFrame_FastToLocals()` and `PyFrame_FastToLocalsWithError()` no longer have any effect. Calling these functions has been redundant since Python 3.11, when [`PyFrame_GetLocals()`](https://docs.python.org/zh-cn/3.13/c-api/frame.html#c.PyFrame_GetLocals) was first introduced.
  - `PyFrame_LocalsToFast()` no longer has any effect. Calling this function is redundant now that [`PyFrame_GetLocals()`](https://docs.python.org/zh-cn/3.13/c-api/frame.html#c.PyFrame_GetLocals) returns a write-through proxy for [optimized scopes](https://docs.python.org/zh-cn/3.13/glossary.html#term-optimized-scope).

## 回归测试的变化

- 现在使用 `configure` [`--with-pydebug`](https://docs.python.org/zh-cn/3.13/using/configure.html#cmdoption-with-pydebug) 编译的 Python 将支持 [`-X presite=package.module`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-X) 命令行选项。 如果被使用，它指明一个模块应当在解释器生命周期开始时，即 `site.py` 被执行之前被导入。 （由 Łukasz Langa 在 [gh-110769](https://github.com/python/cpython/issues/110769) 中贡献。）

## Notable changes in 3.13.1

### sys

- The previously undocumented special function [`sys.getobjects()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.getobjects), which only exists in specialized builds of Python, may now return objects from other interpreters than the one it's called in.
