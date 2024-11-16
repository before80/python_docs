+++
title = "Python 3.13 有什么新变化"
date = 2024-11-14T22:31:40+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文：[https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html](https://docs.python.org/zh-cn/3.13/whatsnew/3.13.html)
>
> 收录该文档的时间：`2024-11-14T22:01:54+08:00`

# Python 3.13 有什么新变化

- 编者:

  ​	Adam Turner 和 Thomas Wouters

​	本文介绍了 Python 3.13 相比 3.12 增加的新我。 Python 3.13 已于 2024 年 10 月 7 日发布。 要获取详细信息，可参阅 [更新日志](https://docs.python.org/zh-cn/3.13/whatsnew/changelog.html#changelog)。

​	参见 **PEP 719**](https://peps.python.org/pep-0719/) -- Python 3.13 发布计划

## 摘要 -- 发布重点

​	Python 3.13 是 Python 编程语言的最新稳定发布版，包含多项针对语言、实现和标准库的改变。 最大的变化包括一个新的 [交互式解释器]({{< ref "/whatsnew/idx#whatsnew313-better-interactive-interpreter" >}})，以及对于在 [自由线程模式]({{< ref "/whatsnew/idx#whatsnew313-free-threaded-cpython" >}}) ([**PEP 703**](https://peps.python.org/pep-0703/)) 下运行和 [即时编译器]({{< ref "/whatsnew/idx#whatsnew313-jit-compiler" >}}) ([**PEP 744**](https://peps.python.org/pep-0744/)) 的实验性支持。

​	错误消息继续得到改进，回溯信息现在默认使用彩色高亮显示。 [`locals()`]({{< ref "/library/functions#locals" >}}) 内置函数现在对于修改所返回的映射具有 [更细化的语法]({{< ref "/whatsnew/idx#whatsnew313-locals-semantics" >}})，并且类型形参现在支持设置默认值。

​	针对标准库的改变包括移除已弃用的 API 和模块，以及用户友好度和正确性方面的常规提升。 一些旧式标准库模块自 Python 3.11 起被弃用 ([**PEP 594**](https://peps.python.org/pep-0594/)) 之后现在 [已被移除]({{< ref "/whatsnew/idx#whatsnew313-pep594" >}})。

​	本文并不试图提供所有新特性的完整规范说明，而是提供一个方便的概览。 要了解完整细节请参阅相应文档，如 [标准库参数]({{< ref "/library#library-index" >}}) 和 [语言参考]({{< ref "/reference#reference-index" >}})。 要了解某项改变的完整实现和设计理念，请参阅相应新特性的 PEP；但请注意一旦某项特性已完全实现则相应 PEP 通常不会再继续更新。 请参阅 [迁移到 Python 3.13]({{< ref "/whatsnew/idx#porting-to-python-3-13" >}}) 了解如何从较早 Python 进行升级的指导。

------

​	解释器的改进：

- 大幅改进的 [交互式解释器]({{< ref "/whatsnew/idx#whatsnew313-better-interactive-interpreter" >}}) 和 [改进的错误消息]({{< ref "/whatsnew/idx#whatsnew313-improved-error-messages" >}})。
- [**PEP 667**](https://peps.python.org/pep-0667/): 现在 [`locals()`]({{< ref "/library/functions#locals" >}}) 内置函数在修改被返回的映射时具有 [已定义语义]({{< ref "/whatsnew/idx#whatsnew313-locals-semantics" >}})。 Python 调试器及类似的工具现在即使在并发代码执行期间也能更可靠地在已优化的作用域中更新局部变量。
- [**PEP 703**](https://peps.python.org/pep-0703/): CPython 3.13 具有对在运行时禁用 [global interpreter lock]({{< ref "/glossary/idx#term-global-interpreter-lock" >}}) 的实验性支持。 请参阅 [自由线程 CPython]({{< ref "/whatsnew/idx#whatsnew313-free-threaded-cpython" >}}) 了解详情。
- [**PEP 744**](https://peps.python.org/pep-0744/): 增加了一个基本的 [JIT 编译器]({{< ref "/whatsnew/idx#whatsnew313-jit-compiler" >}})。 目前默认是禁用的（但以后可能启用）。 能够小幅提升性能 -- 我们预计在接下来的几个发布版中不断改进它。
- 在新的 [交互式解释器]({{< ref "/whatsnew/idx#whatsnew313-better-interactive-interpreter" >}}) 中，以及 [回溯信息]({{< ref "/whatsnew/idx#whatsnew313-improved-error-messages" >}}) 和 [文档测试]({{< ref "/whatsnew/idx#whatsnew313-doctest" >}}) 输出中的颜色支持。 这可以通过 [`PYTHON_COLORS`]({{< ref "/using/cmdline#envvar-PYTHON_COLORS" >}}) and [`NO_COLOR`](https://no-color.org/) 环境变量来禁用。

​	对 Python 数据模型的改进：

- [`__static_attributes__`]({{< ref "/reference/datamodel#type.__static_attributes__" >}}) 保存了可在一个类体的任何函数中通过 `self.X` 来访问的属性名称。
- [`__firstlineno__`]({{< ref "/reference/datamodel#type.__firstlineno__" >}}) 记录了一个类定义的首行的行号。

​	标准库中的重大改进：

- 新增了 [`PythonFinalizationError`]({{< ref "/library/exceptions#PythonFinalizationError" >}}) 异常，当操作在 [最终化]({{< ref "/glossary/idx#term-interpreter-shutdown" >}}) 期间被阻塞时将被引发。
- 现在 [`argparse`]({{< ref "/library/allos/argparse#module-argparse" >}}) 模块可支持弃用命令行选项、位置参数和子命令。
- 新增的函数 [`base64.z85encode()`]({{< ref "/library/netdata/base64#base64.z85encode" >}}) 和 [`base64.z85decode()`]({{< ref "/library/netdata/base64#base64.z85decode" >}}) 支持对 [Z85 数据](https://rfc.zeromq.org/spec/32/) 进行编码和解码。
- 现在 [`copy`]({{< ref "/library/datatypes/copy#module-copy" >}}) 模块有一个 [`copy.replace()`]({{< ref "/library/datatypes/copy#copy.replace" >}}) 函数，支持许多内置类型和任何定义了 [`__replace__()`]({{< ref "/library/datatypes/copy#object.__replace__" >}}) 方法的类。
- 新的 [`dbm.sqlite3`]({{< ref "/library/persistence/dbm#module-dbm.sqlite3" >}}) 模块现在是默认的 [`dbm`]({{< ref "/library/persistence/dbm#module-dbm" >}}) 后端。
- [`os`]({{< ref "/library/allos/os#module-os" >}}) 模块增加了 [一套新函数]({{< ref "/library/allos/os#os-timerfd" >}}) 用于处理 Linux 的定时器通知文件描述符。
- 现在 [`random`]({{< ref "/library/numeric/random#module-random" >}}) 模块提供了一个 [命令行界面]({{< ref "/library/numeric/random#random-cli" >}})。

​	安全改进：

- [`ssl.create_default_context()`]({{< ref "/library/ipc/ssl#ssl.create_default_context" >}}) 设置了 [`ssl.VERIFY_X509_PARTIAL_CHAIN`]({{< ref "/library/ipc/ssl#ssl.VERIFY_X509_PARTIAL_CHAIN" >}}) 和 [`ssl.VERIFY_X509_STRICT`]({{< ref "/library/ipc/ssl#ssl.VERIFY_X509_STRICT" >}}) 作为默认的旗标。

​	C API 的改进：

- 现在 [`Py_mod_gil`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.Py_mod_gil) 槽位被用来指明一个扩展模块支持在禁用 [GIL]({{< ref "/glossary/idx#term-GIL" >}}) 的情况下运行。
- 增加了 [PyTime C API](https://docs.python.org/zh-cn/3.13/c-api/time.html)，提供了对系统时钟的访问。
- [`PyMutex`]({{< ref "/c_api/init#c.PyMutex" >}}) 是新增的轻量级互斥锁，只占用一个字节。
- 新增了 [一套函数]({{< ref "/c_api/monitoring#c-api-monitoring" >}}) 用于在 C API 中生成 [**PEP 669**](https://peps.python.org/pep-0669/) 监控事件。

​	新的类型标注特性：

- [**PEP 696**](https://peps.python.org/pep-0696/): 类型形参 ([`typing.TypeVar`]({{< ref "/library/development/typing#typing.TypeVar" >}}), [`typing.ParamSpec`]({{< ref "/library/development/typing#typing.ParamSpec" >}}) 和 [`typing.TypeVarTuple`]({{< ref "/library/development/typing#typing.TypeVarTuple" >}})) 现在可支持默认值。
- [**PEP 702**](https://peps.python.org/pep-0702/): 新的 [`warnings.deprecated()`]({{< ref "/library/python/warnings#warnings.deprecated" >}}) 装饰器在类型系统和运行时中增加了对标记为弃用的支持。
- [**PEP 705**](https://peps.python.org/pep-0705/): [`typing.ReadOnly`]({{< ref "/library/development/typing#typing.ReadOnly" >}}) 可被用来将 [`typing.TypedDict`]({{< ref "/library/development/typing#typing.TypedDict" >}}) 的项标记为对类型检查器只读。
- [**PEP 742**](https://peps.python.org/pep-0742/): [`typing.TypeIs`]({{< ref "/library/development/typing#typing.TypeIs" >}}) 提供了更直观的类型细化行为，作为对 [`typing.TypeGuard`]({{< ref "/library/development/typing#typing.TypeGuard" >}}) 的替代。

​	平台支持：

- [**PEP 730**](https://peps.python.org/pep-0730/): 现在 Apple 的 iOS 是 [官方支持的平台]({{< ref "/whatsnew/idx#whatsnew313-platform-support" >}})，处于 [**第 3 层级**](https://peps.python.org/pep-0011/#tier-3)。
- [**PEP 738**](https://peps.python.org/pep-0738/): 现在 Android 是 [官方支持的平台]({{< ref "/whatsnew/idx#whatsnew313-platform-support" >}})，处于 [**第 3 层级**](https://peps.python.org/pep-0011/#tier-3)。
- 现在 `wasm32-wasi` 作为 [**第 2 层级**](https://peps.python.org/pep-0011/#tier-2) 的平台受到支持。
- `wasm32-emscripten` 不再是受到官方支持的平台。

​	重要的移除：

- [PEP 594]({{< ref "/whatsnew/idx#whatsnew313-pep594" >}}): 剩余的 19 个“死电池”（老旧 stdlib 模块）已从标准库中移除: `aifc`, `audioop`, `cgi`, `cgitb`, `chunk`, `crypt`, `imghdr`, `mailcap`, `msilib`, `nis`, `nntplib`, `ossaudiodev`, `pipes`, `sndhdr`, `spwd`, `sunau`, `telnetlib`, `uu` 和 `xdrlib`。
- 移除了 **2to3** 工具和 `lib2to3` 模块（在 Python 3.11 中已被弃用）。
- 移除了 `tkinter.tix` 模块（在 Python 3.6 中已被弃用）。
- 移除了 `locale.resetlocale()` 函数。
- 移除了 `typing.io` 和 `typing.re` 命名空间。
- 移除了链式的 [`classmethod`]({{< ref "/library/functions#classmethod" >}}) 描述器。

​	发布计划的变化：

[**PEP 602**](https://peps.python.org/pep-0602/) ("Annual Release Cycle for Python") 已被更新为将新发布版的完整支持 ('bugfix') 期扩展至两年。 这个更新的政策意味着：

- Python 3.9--3.12 有一年半的完整支持，另加三年半的安全修正。
- Python 3.13 及以后的版本有两年的完整支持，另加三年的安全修正。

## 新的特性



### 更好的交互式解释器

​	Python 现在默认会使用新的 [interactive]({{< ref "/glossary/idx#term-interactive" >}}) shell，它基于来自 [PyPy 项目](https://pypy.org/) 的代码。 当使用从交互式终端启动 [REPL]({{< ref "/glossary/idx#term-REPL" >}}) 时，下列新特性将受到支持：

- 多行编辑并保留历史记录。
- 对 REPL 专属的命令如 help, exit 和 quit 的直接支持，无需以函数形式调用它们。
- 提示和回溯 [默认启用彩色显示]({{< ref "/using/cmdline#using-on-controlling-color" >}})。
- 使用 F1 浏览交互式帮助并带有单独的命令历史。
- 使用 F2 浏览去除了输出以及 [>>>]({{< ref "/glossary/idx#term-0" >}}) 和 [...]({{< ref "/glossary/idx#term-..." >}}) 提示符的历史。
- 使用 F3 进入“粘贴模式”以更方便地粘贴大段代码（再次按 F3 返回常规提示符）。

​	要禁用新的交互式 shell，可设置 [`PYTHON_BASIC_REPL`]({{< ref "/using/cmdline#envvar-PYTHON_BASIC_REPL" >}}) 环境变量。 有关交互模式的详情，请参见 [交互模式]({{< ref "/tutorial/appendix#tut-interac" >}})。

​	（由 Pablo Galindo Salgado, Łukasz Langa 和 Lysandros Nikolaou 在 [gh-111201](https://github.com/python/cpython/issues/111201) 基于来自 PyPy 项目的代码贡献。 Windows 支持由 Dino Viehland 和 Anthony Shaw 贡献。）



### 改进的错误消息

- 在终端里显示回溯时解释器现在会默认使用彩色。 此特性可通过新的 [`PYTHON_COLORS`]({{< ref "/using/cmdline#envvar-PYTHON_COLORS" >}}) 环境变量以及传统的 [`NO_COLOR`](https://no-color.org/) 和 [`FORCE_COLOR`](https://force-color.org/) 环境变量来 [进行控制]({{< ref "/using/cmdline#using-on-controlling-color" >}})。 （由 Pablo Galindo Salgado 在 [gh-112730](https://github.com/python/cpython/issues/112730) 中贡献。）

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

​	现在 CPython 具有对运行于禁用 [global interpreter lock]({{< ref "/glossary/idx#term-global-interpreter-lock" >}}) (GIL) 的自由线程模式的实验性支持。 这是一个实验性的特性因而默认是不启用的。 自由线程模式需要一个不同的可执行程序，通常名为 `python3.13t` 或 `python3.13t.exe`。 标记为 *free-threaded* 的预构建二进制文件可作为官方 [Windows]({{< ref "/using/windows#install-freethreaded-windows" >}}) 和 [macOS]({{< ref "/using/mac#install-freethreaded-macos" >}}) 安装器的一部分被安装，或者可以附带 [`--disable-gil`]({{< ref "/using/configure#cmdoption-disable-gil" >}}) 选项使用源代码来构建 CPython。

​	自由线程模式的执行允许在可用的 CPU 核心上并行地运行线程从而充分利用可用的处理能力。 虽然并非所有软件都能自动从中受益，但在设计时将线程纳入考虑的程序在多核心硬件上运行速度会更快。 **自由线程模式是实验性的** 并且处于不断改进的过程中：预计会出现一些程序错误并且在单线程场景下出现明显的性能损失。 可以选择使用环境变量 [`PYTHON_GIL`]({{< ref "/using/cmdline#envvar-PYTHON_GIL" >}}) 或命令行选项 [`-X gil=1`]({{< ref "/using/cmdline#cmdoption-X" >}}) 让 CPython 的自由线程构建版支持在运行时启用 GIL。

​	为了检查当前解释器是否支持自由线程，[`python -VV`]({{< ref "/using/cmdline#cmdoption-V" >}}) 和 [`sys.version`]({{< ref "/library/python/sys#sys.version" >}}) 将包含 "experimental free-theading build" 字样。 可以使用新增的 `sys._is_gil_enabled()` 函数来检查正在运行的线程是否确实禁用了 GIL。

​	C-API 扩展模块需要针对自由线程构建版专门进行构建。 支持在禁用 [GIL]({{< ref "/glossary/idx#term-GIL" >}}) 的情况下运行的扩展应当使用 [`Py_mod_gil`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.Py_mod_gil) 槽位。 使用单阶段初始化的扩展应当使用 [`PyUnstable_Module_SetGIL()`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.PyUnstable_Module_SetGIL) 来指明它们是支支持在禁用 GIL 的情况下运行。 导入不使用这些机制的 C 扩展将导致 GIL 被启用，除非通过 [`PYTHON_GIL`]({{< ref "/using/cmdline#envvar-PYTHON_GIL" >}}) 环境变量或 [`-X gil=0`]({{< ref "/using/cmdline#cmdoption-X" >}}) 选项显式地禁用 GIL。 需要 pip 24.1 或更新的版本才能在自由线程构建版中安装带有 C 扩展的软件包。

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



### 针对 [`locals()`]({{< ref "/library/functions#locals" >}}) 的已定义修改语义

​	在历史上，改变 [`locals()`]({{< ref "/library/functions#locals" >}}) 的返回值的预期结果是留给具体的 Python 实现来定义的。 从 Python 3.13 开始，[**PEP 667**](https://peps.python.org/pep-0667/) 标准化了 CPython 对于大多数代码执行作用域的历史行为，但也将 [已优化作用域]({{< ref "/glossary/idx#term-optimized-scope" >}}) (函数、生成器、协程、推导式和生成器表达式) 修改为显式地返回当前已赋值的局部变量的独立快照，包括局部引用的在闭包中捕获的非局部变量。

​	在已优化作用域中对 [`locals()`]({{< ref "/library/functions#locals" >}}) 语义的这项修改也会影响隐式地以 `locals()` 为目标的代码执行函数的默认行为，如果没有提供显式命名空间的话（例如 [`exec()`]({{< ref "/library/functions#exec" >}}) 和 [`eval()`]({{< ref "/library/functions#eval" >}}) 等）。 在之前的版本中，在调用代码执行函数后是否可以通过调用 `locals()` 访问更改情况取决于具体的实现。 具体到 CPython 而言，此类代码通常会按预期工作，但有时可能会在基于其他代码（包括调试器和代码执行跟踪工具）的已优化作用域中失败，因为代码有可能重置该作用域中的共享快照。 现在，代码在已优化作用域中将始终针对局部变量的独立快照运行，因为在后续调用 `locals()` 时将永远看不到更改。 要访问在这些情况下所做的更改，现在必须将一个显式命名空间引用传递给相关的函数。 或者，也可以更新受影响的代码以使用更高层级的代码执行 API 返回结果代码命名空间（例如，当执行磁盘上的 Python 文件时使用 [`runpy.run_path()`]({{< ref "/library/modules/runpy#runpy.run_path" >}}) 函数）。

​	为确保调试器和类似工具能可靠地更新受到此变化影响的作用域中的局部变量，现在 [`FrameType.f_locals`]({{< ref "/reference/datamodel#frame.f_locals" >}}) 将返回一个针对此种作用域中的帧的局部变量和在局部引用的非局部变量的直通写入代理对象，而不是返回一个非持续更新的具有规定义的运行时语义的共享 `dict` 实例。

​	请参阅 [**PEP 667**](https://peps.python.org/pep-0667/) 了解详情，包括相关的 C API 更改和弃用。 下文还针对受影响的 [Python API]({{< ref "/whatsnew/idx#pep667-porting-notes-py" >}}) 和 [C API]({{< ref "/whatsnew/idx#pep667-porting-notes-c" >}}) 提供了移植说明。

​	（PEP 和实现由 Mark Shannon 和 Tian Gao 在 [gh-74929](https://github.com/python/cpython/issues/74929) 中贡献。 文档更新由 Guido van Rossum 和 Alyssa Coghlan 提供。）



### 对移动平台的支持

[**PEP 730**](https://peps.python.org/pep-0730/): iOS 现在是 [**PEP 11**](https://peps.python.org/pep-0011/) 所支持的平台，包括第 3 层级的 `arm64-apple-ios` 和 `arm64-apple-ios-simulator` 等目标（分别为2013 年后的 iPhone 和 iPad 设备以及运行于 Apple silicon 硬件的 Xcode iOS 模拟器）。 `x86_64-apple-ios-simulator` （运行于较旧的 `x86_64` 硬件的 Xcode iOS 模拟器）不是第 3 层级的受支持平台，但也将尽可能地支持。 （PEP 撰写及实现由 Russell Keith-Magee 在 [gh-114099](https://github.com/python/cpython/issues/114099) 中贡献。）.)

[**PEP 738**](https://peps.python.org/pep-0738/): Android 现在是 [**PEP 11**](https://peps.python.org/pep-0011/) 所支持的平台，包括位于第 3 层级的 `aarch64-linux-android` 和 `x86_64-linux-android` 等目标。 32 位的目标 `arm-linux-androideabi` 和 `i686-linux-android` 不是第 3 层级的受支持平台，但也将尽可能地支持。 （PEP 撰写及实现由 Malcolm Smith 在 [gh-116622](https://github.com/python/cpython/issues/116622) 中贡献。）

​	参见

 

[**PEP 730**](https://peps.python.org/pep-0730/), [**PEP 738**](https://peps.python.org/pep-0738/)

## 其他语言特性修改

- 编译器现在将从文档字符串的每一行去除共有的前导空格。 这会减少 [字节码缓存]({{< ref "/glossary/idx#term-bytecode" >}}) 的大小（例如 `.pyc` 文件），例如在 SQLAlchemy 2.0 的 `sqlalchemy.orm.session` 中文件大小将减少约 5%。 这项改变将影响各种使用了文档字符串的工具，如 [`doctest`]({{< ref "/library/development/doctest#module-doctest" >}})。

  

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

- 类作用域内的 [标注作用域]({{< ref "/reference/executionmodel#annotation-scopes" >}}) 现在可以包含 lambda 和推导式。 位于类作用域内的推导式不会内联到其父作用域中。

  ```
  class C[T]:
      type Alias = lambda: T
  ```

  （由 Jelle Zijlstra 在 [gh-109118](https://github.com/python/cpython/issues/109118) 和 [gh-118160](https://github.com/python/cpython/issues/118160) 中贡献。）

- [future 语句]({{< ref "/reference/simple_stmts#future" >}}) 不再会被 [`__future__`]({{< ref "/library/python/__future__#module-__future__" >}}) 模块的相对导入触发，意味着 `from .__future__ import ...` 形式的语句现在只是标准的相对导入，而不会激活任何特殊特性。 （由 Jeremiah Gabriel Pascual 在 [gh-118216](https://github.com/python/cpython/issues/118216) 中贡献。）

- 现在 [`global`]({{< ref "/reference/simple_stmts#global" >}}) 声明当其被用于 [`else`]({{< ref "/reference/compound_stmts#else" >}}) 代码块中时也将被允许在 [`except`]({{< ref "/reference/compound_stmts#except" >}}) 代码块中使用。 在之前版本中这会错误地引发 [`SyntaxError`]({{< ref "/library/exceptions#SyntaxError" >}})。 （由 Irit Katriel 在 [gh-111123](https://github.com/python/cpython/issues/111123) 中贡献。）

- 增加了新的环境变量 [`PYTHON_FROZEN_MODULES`]({{< ref "/using/cmdline#envvar-PYTHON_FROZEN_MODULES" >}})，它确定冻结模块是否会被导入机制所忽略，等价于 [`-X frozen_modules`]({{< ref "/using/cmdline#cmdoption-X" >}}) 命令行选项。 （由 Yilei Yang 在 [gh-111374](https://github.com/python/cpython/issues/111374) 中贡献。）

- 通过新的环境变量 [`PYTHON_PERF_JIT_SUPPORT`]({{< ref "/using/cmdline#envvar-PYTHON_PERF_JIT_SUPPORT" >}}) 和命令行选项 [`-X perf_jit`]({{< ref "/using/cmdline#cmdoption-X" >}}) 添加无需 [帧指针](https://en.wikipedia.org/wiki/Call_stack) 即可工作的 [对 perf 性能分析器的支持]({{< ref "/howto/perf_profiling#perf-profiling" >}})。 （由 Pablo Galindo 在 [gh-118518](https://github.com/python/cpython/issues/118518) 中贡献。）

- 可通过新的 [`PYTHON_HISTORY`]({{< ref "/using/cmdline#envvar-PYTHON_HISTORY" >}}) 环境变量来更改 `.python_history` 文件的位置。 （由 Levi Sabah, Zackery Spytz 和 Hugo van Kemenade 在 [gh-73965](https://github.com/python/cpython/issues/73965) 中贡献。）

- 类新增了一个 [`__static_attributes__`]({{< ref "/reference/datamodel#type.__static_attributes__" >}}) 属性。 这由编译器以类属性名称的元组来填充，这些名称是从类体中的任何函数通过 `self.<name>` 来赋值的。 （由 Irit Katriel 在 [gh-115775](https://github.com/python/cpython/issues/115775) 中贡献。）.)

- 编译器现在会在类上创建一个 `__firstlineno__` 属性，其值为类定义第一行的行号。 （由 Serhiy Storchaka 在 [gh-118465](https://github.com/python/cpython/issues/118465) 中贡献。）

- 现在 [`exec()`]({{< ref "/library/functions#exec" >}}) 和 [`eval()`]({{< ref "/library/functions#eval" >}}) 内置函数接受以关键字形式传入的 *globals* 和 *locals* 参数。 （由 Raphael Gaschignard 在 [gh-105879](https://github.com/python/cpython/issues/105879) 中贡献。）

- 现在 [`compile()`]({{< ref "/library/functions#compile" >}}) 内置函数接受一个新的旗标 `ast.PyCF_OPTIMIZED_AST`，它类似于 `ast.PyCF_ONLY_AST` 但区别在于返回的 AST 是根据 *optimize* 参数的值进行优化的。 （由 Irit Katriel 在 [gh-108113](https://github.com/python/cpython/issues/108113) 中贡献。）

- 在 [`property`]({{< ref "/library/functions#property" >}}) 对象上增加了 [`__name__`]({{< ref "/library/functions#property.__name__" >}}) 属性。 （由 Eugene Toder 在 [gh-101860](https://github.com/python/cpython/issues/101860) 中贡献。）

- 增加了新的异常 [`PythonFinalizationError`]({{< ref "/library/exceptions#PythonFinalizationError" >}})，它派生自 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})，用于当操作在 [最终化]({{< ref "/glossary/idx#term-interpreter-shutdown" >}}) 期间被阻塞时发出信号。 下列可调用对象现在将引发 `PythonFinalizationError`，而不是 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}}):

  - [`_thread.start_new_thread()`]({{< ref "/library/concurrency/_thread#thread.start_new_thread" >}})
  - [`os.fork()`]({{< ref "/library/allos/os#os.fork" >}})
  - [`os.forkpty()`]({{< ref "/library/allos/os#os.forkpty" >}})
  - [`subprocess.Popen`]({{< ref "/library/concurrency/subprocess#subprocess.Popen" >}})

  （由 Victor Stinner 在 [gh-114570](https://github.com/python/cpython/issues/114570) 中贡献。）

- 允许 [`str.replace()`]({{< ref "/library/stdtypes#str.replace" >}}) 的 *count* 参数为关键字参数。 （由 Hugo van Kemenade 在 [gh-106487](https://github.com/python/cpython/issues/106487) 中贡献。）

- 现在许多函数会对将布尔值作为文件描述符参数发出警告。这可以帮助尽早发现一些错误。（由 Serhiy Storchaka 在 [gh-82626](https://github.com/python/cpython/issues/82626) 中贡献。）

- 为 [`bz2`]({{< ref "/library/archiving/bz2#module-bz2" >}}), [`lzma`]({{< ref "/library/archiving/lzma#module-lzma" >}}), [`tarfile`]({{< ref "/library/archiving/tarfile#module-tarfile" >}}) 和 [`zipfile`]({{< ref "/library/archiving/zipfile#module-zipfile" >}}) 等模块中的已压缩和已归档文件型对象添加了 `name` 和 `mode` 属性。 （由 Serhiy Storchaka 在 [gh-115961](https://github.com/python/cpython/issues/115961) 中贡献。）

## 新增模块

- [`dbm.sqlite3`]({{< ref "/library/persistence/dbm#module-dbm.sqlite3" >}}): 针对 [`dbm`]({{< ref "/library/persistence/dbm#module-dbm" >}}) 的 SQLite 后端。 （由 Raymond Hettinger 和 Erlend E. Aasland 在 [gh-100414](https://github.com/python/cpython/issues/100414) 中贡献。）

## 改进的模块

### argparse

- 为 [`add_argument()`]({{< ref "/library/allos/argparse#argparse.ArgumentParser.add_argument" >}}) 和 `add_parser()` 方法添加了 *deprecated* 形参，以允许弃用命令行选项、位置参数和子命令。 （由 Serhiy Storchaka 在 [gh-83648](https://github.com/python/cpython/issues/83648) 中贡献。）

### array

- 增加了 `'w'` 类型码 (`Py_UCS4`) 表示 Unicode 字符。 它应被用来代替已弃用的 `'u'` 类型码。 （由 Inada Naoki 在 [gh-80480](https://github.com/python/cpython/issues/80480) 中贡献。）
- 通过实现 [`clear()`]({{< ref "/library/datatypes/array#array.array.clear" >}}) 方法将 [`array.array`]({{< ref "/library/datatypes/array#array.array" >}}) 注册为 [`MutableSequence`]({{< ref "/library/datatypes/collections_abc#collections.abc.MutableSequence" >}})。 （由 Mike Zimin 在 [gh-114894](https://github.com/python/cpython/issues/114894) 中贡献。）

### ast

- 现在 [`ast`]({{< ref "/library/language/ast#module-ast" >}}) 模块中节点类型的构造器对其接受的参数要求更为严格，并在参数被省略时有更易理解的行为。

  如果在构造实例时某个 AST 节点上的可选字段没有被作为参数包括在内，则该字段现在将被设为 `None`。 类似地，如果某个列表字段被省略，则该字段现在将被设为空列表，而如果某个 `expr_context` 字段被省略，则它将默认为 [`Load()`]({{< ref "/library/language/ast#ast.Load" >}})。 （之前，在所有情况下，新构造的 AST 节点实例上的相应属性都将缺失。）

  在所有其他情况下，当需要的参数被省略时，节点构造器将发出 [`DeprecationWarning`]({{< ref "/library/exceptions#DeprecationWarning" >}})。 这在 Python 3.15 中将会引发异常。 类似地，将关键字参数传入一个未映射到 AST 节点上的字段的构造器的做法现在已被弃用，并且在 Python 3.15 中将会引发异常。

  这些更改将不会应用于用户自定义的 [`ast.AST`]({{< ref "/library/language/ast#ast.AST" >}}) 子类，除非该类选择通过设置 [`AST._field_types`]({{< ref "/library/language/ast#ast.AST._field_types" >}}) 映射的方式加入新的行为。

  （由 Jelle Zijlstra 在 [gh-105858](https://github.com/python/cpython/issues/105858), [gh-117486](https://github.com/python/cpython/issues/117486) 和 [gh-118851](https://github.com/python/cpython/issues/118851) 中贡献。）

- 现在 [`ast.parse()`]({{< ref "/library/language/ast#ast.parse" >}}) 接受一个可选参数 *optimize*，它会被传递给 [`compile()`]({{< ref "/library/functions#compile" >}})。 这使得获取已优化的 AST 成为可能。 （由 Irit Katriel 在 [gh-108113](https://github.com/python/cpython/issues/108113) 中贡献。）

### asyncio

- 现在 [`asyncio.as_completed()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.as_completed" >}}) 将返回一个即是 [asynchronous iterator]({{< ref "/glossary/idx#term-asynchronous-iterator" >}}) 又是基本的产生 [可等待对象]({{< ref "/glossary/idx#term-awaitable" >}}) 的 [iterator]({{< ref "/glossary/idx#term-iterator" >}}) 的对象。 由异常迭代产生的可等待对象包括被传入的原始 Task 或 Future 对象，使得将结果与正在完成的任务相关联更为容易。 （由 Justin Arthur 在 [gh-77714](https://github.com/python/cpython/issues/77714) 中贡献。）

- 现在当服务器被关闭时 [`asyncio.loop.create_unix_server()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_unix_server" >}}) 会自动移除 Unix 套接字。 （由 Pierre Ossman 在 [gh-111246](https://github.com/python/cpython/issues/111246) 中贡献。）

- 现在当附带一个空字节串对象调用时 [`DatagramTransport.sendto()`]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio.DatagramTransport.sendto" >}}) 将发送零长度的数据报。 现在当计算缓冲区大小时传输控制流还会将数据报标头纳入考量。 （由 Jamie Phan 在 [gh-115199](https://github.com/python/cpython/issues/115199) 中贡献。）

- 增加了 [`Queue.shutdown`]({{< ref "/library/ipc/asyncio/asyncio-queue#asyncio.Queue.shutdown" >}}) 和 [`QueueShutDown`]({{< ref "/library/ipc/asyncio/asyncio-queue#asyncio.QueueShutDown" >}}) 用于管理队列终结。 （由 Laurie Opperman 和 Yves Duprat 在 [gh-104228](https://github.com/python/cpython/issues/104228) 中贡献。）

- 增加了 [`Server.close_clients()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.Server.close_clients" >}}) 和 [`Server.abort_clients()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.Server.abort_clients" >}}) 方法，它们会以更强制的方式关闭 asyncio 服务器。 （由 Pierre Ossman 在 [gh-113538](https://github.com/python/cpython/issues/113538) 中贡献。）

- 在 [`StreamReader.readuntil()`]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio.StreamReader.readuntil" >}}) 中接受一个由分隔符组成的元组，当遇到其中之一时就会停止。 （由 Bruce Merry 在 [gh-81322](https://github.com/python/cpython/issues/81322) 中贡献。）

- 改进了 [`TaskGroup`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.TaskGroup" >}}) 在外部的取消操作与内部的取消操作发生冲突时的行为。 例如，当嵌套两个任务分组并且两者同时在某个子任务中遇到异常时，外层的任务分组有可能被挂起，因为其内部的取消操作已由内层的任务分组进行处理。

  对于任务分组在外部被取消时同时必须引发 [`ExceptionGroup`]({{< ref "/library/exceptions#ExceptionGroup" >}}) 的情况，现在它将调用父任务的 [`cancel()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.cancel" >}}) 方法。 这样可以确保 [`CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions.#asyncio.CancelledError" >}}) 会在下一次 [`await`]({{< ref "/reference/expressions#await" >}}) 时被引发，因此取消操作不会丢失。, so the cancellation is not lost.

  这些更改的一个附加好处是现在任务组会保留取消操作计数 ([`cancelling()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.cancelling" >}}))。

  为了处理某些边界情况，现在 [`uncancel()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.uncancel" >}}) 可以在取消操作计数达到零时重置未写入文档的 `_must_cancel` 旗标。

  （受到由 Arthur Tacca 在 [gh-116720](https://github.com/python/cpython/issues/116720) 中报告的问题的启发。）

- 当在一个未激活的 [`TaskGroup`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.TaskGroup" >}}) 上调用 [`TaskGroup.create_task()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.TaskGroup.create_task" >}}) 时，给定的协程将被关闭 (这将防止引发有关给定的协程从未被等待的 [`RuntimeWarning`]({{< ref "/library/exceptions#RuntimeWarning" >}}))。 （由 Arthur Tacca 和 Jason Zhang 在 [gh-115957](https://github.com/python/cpython/issues/115957) 中贡献。）

### base64

- 增加了 [`z85encode()`]({{< ref "/library/netdata/base64#base64.z85encode" >}}) 和 [`z85decode()`]({{< ref "/library/netdata/base64#base64.z85decode" >}}) 函数用于将 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 编码为 [Z85 data](https://rfc.zeromq.org/spec/32/) 和将 Z85 编码的数据解码为 `bytes`。 （由 Matan Perelman 在 [gh-75299](https://github.com/python/cpython/issues/75299) 中贡献。）

### compileall

- 工作线程和进程的默认数据现在是使用 [`os.process_cpu_count()`]({{< ref "/library/allos/os#os.process_cpu_count" >}}) 而不是 [`os.cpu_count()`]({{< ref "/library/allos/os#os.cpu_count" >}}) 来选择的。 （由 Victor Stinner 在 [gh-109649](https://github.com/python/cpython/issues/109649) 中贡献。）

### concurrent.futures

- 工作线程和进程的默认数据现在是使用 [`os.process_cpu_count()`]({{< ref "/library/allos/os#os.process_cpu_count" >}}) 而不是 [`os.cpu_count()`]({{< ref "/library/allos/os#os.cpu_count" >}}) 来选择的。 （由 Victor Stinner 在 [gh-109649](https://github.com/python/cpython/issues/109649) 中贡献。）

### configparser

- 现在 [`ConfigParser`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser" >}}) 具有对未命名节的支持，这将允许使用最高层级的键值对。 此特性可通过新增的 *allow_unnamed_section* 形参来启用。 （由 Pedro Sousa Lacerda 在 [gh-66449](https://github.com/python/cpython/issues/66449) 中贡献。）

### copy

- 新增的 [`replace()`]({{< ref "/library/datatypes/copy#copy.replace" >}}) 函数和 [`replace 协议`]({{< ref "/library/datatypes/copy#object.__replace__" >}}) 使得创建经修改的对象副本更为简单。 这在操作不可变对象时特别有用。 以下类型将支持 [`replace()`]({{< ref "/library/datatypes/copy#copy.replace" >}}) 函数并实现了 replace 协议：

  - [`collections.namedtuple()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.namedtuple)
  - [`dataclasses.dataclass`]({{< ref "/library/python/dataclasses#dataclasses.dataclass" >}})
  - [`datetime.datetime`]({{< ref "/library/datatypes/datetime#datetime.datetime" >}}), [`datetime.date`]({{< ref "/library/datatypes/datetime#datetime.date" >}}), [`datetime.time`]({{< ref "/library/datatypes/datetime#datetime.time" >}})
  - [`inspect.Signature`]({{< ref "/library/python/inspect#inspect.Signature" >}}), [`inspect.Parameter`]({{< ref "/library/python/inspect#inspect.Parameter" >}})
  - [`types.SimpleNamespace`]({{< ref "/library/datatypes/types#types.SimpleNamespace" >}})
  - [代码对象]({{< ref "/reference/datamodel#code-objects" >}})

  任何用户自定义类也可以通过定义 [`__replace__()`]({{< ref "/library/datatypes/copy#object.__replace__" >}}) 方法来支持 [`copy.replace()`]({{< ref "/library/datatypes/copy#copy.replace" >}})。 （由 Serhiy Storchaka 在 [gh-108751](https://github.com/python/cpython/issues/108751) 中贡献。）

### ctypes

- 作为必要的内部重构的一个后果，内部元类的初始化现在将发生于 `__init__` 中而不是 `__new__` 中。 这会影响子类化这些内部元类以提供自定义初始化的项目。 一般而言：

  - 调用 `super().__new__` 之后在 `__new__` 中完成的自定义逻辑应当移至 `__init__`。
  - 要创建一个类，需调用相应的元类，而不仅是该元类的 `__new__` 方法。

  请参阅 [gh-124520](https://github.com/python/cpython/issues/124520) 了解相关讨论和对某些受影响项目的修改的链接。

- [`ctypes.Structure`]({{< ref "/library/allos/ctypes#ctypes.Structure" >}}) objects have a new [`_align_`]({{< ref "/library/allos/ctypes#ctypes.Structure._align_" >}}) attribute which allows the alignment of the structure being packed to/from memory to be specified explicitly. (Contributed by Matt Sanderson in [gh-112433](https://github.com/python/cpython/issues/112433))

### dbm

- 增加 [`dbm.sqlite3`]({{< ref "/library/persistence/dbm#module-dbm.sqlite3" >}})，一个实现了 SQLite 后端的新模块，并将其设为默认的 `dbm` 后端。 （由 Raymond Hettinger 和 Erlend E. Aasland 在 [gh-100414](https://github.com/python/cpython/issues/100414) 中贡献。）
- 允许通过新增的 [`gdbm.clear()`]({{< ref "/library/persistence/dbm#dbm.gnu.gdbm.clear" >}}) 和 [`ndbm.clear()`]({{< ref "/library/persistence/dbm#dbm.ndbm.ndbm.clear" >}}) 方法移除数据库中的所有条目。 （由 Donghee Na 在 [gh-107122](https://github.com/python/cpython/issues/107122) 中贡献。）

### dis

- 将 [`dis`]({{< ref "/library/language/dis#module-dis" >}}) 模块的函数的输出修改为显示跳转目标和异常处理器的逻辑标签，而不是偏移量。 可以使用新的 [`-O`]({{< ref "/library/language/dis#cmdoption-dis-O" >}}) 命令行选项或 *show_offsets* 参数来添加偏移量。 （由 Irit Katriel 在 [gh-112137](https://github.com/python/cpython/issues/112137) 中贡献。）
- [`get_instructions()`]({{< ref "/library/language/dis#dis.get_instructions" >}}) 不再将缓存条目表示为单独的指令。 作为替代，它会将它们作为 [`Instruction`]({{< ref "/library/language/dis#dis.Instruction" >}}) 的组成部分返回，放在新的 *cache_info* 字段中。 传给 [`get_instructions()`]({{< ref "/library/language/dis#dis.get_instructions" >}}) 的 *show_caches* 参数已被弃用并且不再有任何效果。 （由 Irit Katriel 在 [gh-112962](https://github.com/python/cpython/issues/112962) 中贡献。）



### doctest

- 现在 [`doctest`]({{< ref "/library/development/doctest#module-doctest" >}}) 输出默认是彩色的。 此特性可通过新增的 [`PYTHON_COLORS`]({{< ref "/using/cmdline#envvar-PYTHON_COLORS" >}}) 环境变量和传统的 [`NO_COLOR`](https://no-color.org/) 和 [`FORCE_COLOR`](https://force-color.org/) 环境变量来控制。 另请参阅 [控制颜色]({{< ref "/using/cmdline#using-on-controlling-color" >}})。 （由 Hugo van Kemenade 在 [gh-117225](https://github.com/python/cpython/issues/117225) 中贡献。）
- 现在 [`DocTestRunner.run()`]({{< ref "/library/development/doctest#doctest.DocTestRunner.run" >}}) 方法会统计已跳过测试的数量。 增加了 [`DocTestRunner.skips`]({{< ref "/library/development/doctest#doctest.DocTestRunner.skips" >}}) 和 [`TestResults.skipped`]({{< ref "/library/development/doctest#doctest.TestResults.skipped" >}}) 属性。 （由 Victor Stinner 在 [gh-108794](https://github.com/python/cpython/issues/108794) 中贡献。）

### email

- 现在带有嵌入的换行符的标头在输出时会加上引号。 现在 [`generator`]({{< ref "/library/netdata/email/email_generator#module-email.generator" >}}) 会拒绝序列化（写入）不正确地折叠或分隔的标头，例如将被解析为多个标头或与相邻数据合并的标头等。 如果你需要禁用此安全特性，请设置 [`verify_generated_headers`]({{< ref "/library/netdata/email/email_policy#email.policy.Policy.verify_generated_headers" >}})。 （由 Bas Bloemsaat 和 Petr Viktorin 在 [gh-121650](https://github.com/python/cpython/issues/121650) 中贡献。）
- 现在 [`getaddresses()`]({{< ref "/library/netdata/email/email_utils#email.utils.getaddresses" >}}) 和 [`parseaddr()`]({{< ref "/library/netdata/email/email_utils#email.utils.parseaddr" >}}) 会在更多遇到无效 email 地址的情况下返回 `('', '')` 对非可能不准确的值。 这两个函数新增了可选的 *strict* 形参 (默认为 `True`)。 要获取旧版本的行为 (接受错误格式的输入)，请使用 `strict=False`。 `getattr(email.utils, 'supports_strict_parsing', False)` 可被用于检查 *strict* 形参是否可用。 （由 Thomas Dwyer 和 Victor Stinner 针对 [gh-102988](https://github.com/python/cpython/issues/102988) 贡献以改进 [**CVE 2023-27043**](https://www.cve.org/CVERecord?id=CVE-2023-27043) 修正。）

### fractions

- 现在 [`Fraction`]({{< ref "/library/numeric/fractions#fractions.Fraction" >}}) 对象支持用于填充、对齐、正负号处理、最小宽度和分组的标准 [格式说明迷你语言]({{< ref "/library/text/string#formatspec" >}}) 规则。 （由 Mark Dickinson 在 [gh-111320](https://github.com/python/cpython/issues/111320) 中贡献。）

### glob

- 增加了 [`translate()`]({{< ref "/library/filesys/glob#glob.translate" >}})，这是个用来将具有 shell 风格通配符的路径说明转换为正则表达式的函数。 （由 Barney Gale 在 [gh-72904](https://github.com/python/cpython/issues/72904) 中贡献。）

### importlib

- 现在 [`importlib.resources`]({{< ref "/library/modules/importlib_resources#module-importlib.resources" >}}) 中的下列函数允许访问资源目录（或树），并使用多个位置参数（现在文本读取函数中的 *encoding* 和 *errors* 参数是仅限关键字参数）：

  - [`is_resource()`]({{< ref "/library/modules/importlib_resources#importlib.resources.is_resource" >}})
  - [`open_binary()`]({{< ref "/library/modules/importlib_resources#importlib.resources.open_binary" >}})
  - [`open_text()`]({{< ref "/library/modules/importlib_resources#importlib.resources.open_text" >}})
  - [`path()`]({{< ref "/library/modules/importlib_resources#importlib.resources.path" >}})
  - [`read_binary()`]({{< ref "/library/modules/importlib_resources#importlib.resources.read_binary" >}})
  - [`read_text()`]({{< ref "/library/modules/importlib_resources#importlib.resources.read_text" >}})

  These functions are no longer deprecated and are not scheduled for removal. (Contributed by Petr Viktorin in [gh-116608](https://github.com/python/cpython/issues/116608).)

- [`contents()`]({{< ref "/library/modules/importlib_resources#importlib.resources.contents" >}}) remains deprecated in favor of the fully-featured [`Traversable`]({{< ref "/library/modules/importlib_resources_abc#importlib.resources.abc.Traversable" >}}) API. However, there is now no plan to remove it. (Contributed by Petr Viktorin in [gh-116608](https://github.com/python/cpython/issues/116608).)

### io

- 现在 [`IOBase`]({{< ref "/library/allos/io#io.IOBase" >}}) 最终化器会使用 [`sys.unraisablehook`]({{< ref "/library/python/sys#sys.unraisablehook" >}}) 来将由 [`close()`]({{< ref "/library/allos/io#io.IOBase.close" >}}) 方法引发的错误写入日志。 在之前版本中，错误在默认情况下会被静默地忽略，而只有在 [Python 开发模式]({{< ref "/library/development/devmode#devmode" >}}) 或在使用 [Python 调试构建版]({{< ref "/using/configure#debug-build" >}}) 时才会被写入日志。 （由 Victor Stinner 在 [gh-62948](https://github.com/python/cpython/issues/62948) 中贡献。）

### ipaddress

- 增加了 [`IPv4Address.ipv6_mapped`]({{< ref "/library/internet/ipaddress#ipaddress.IPv4Address.ipv6_mapped" >}}) 特征属性，它将返回映射 IPv4 的 IPv6 地址。 （由 Charles Machalow 在 [gh-109466](https://github.com/python/cpython/issues/109466) 中贡献。）
- 修正了 [`IPv4Address`]({{< ref "/library/internet/ipaddress#ipaddress.IPv4Address" >}}), [`IPv6Address`]({{< ref "/library/internet/ipaddress#ipaddress.IPv6Address" >}}), [`IPv4Network`]({{< ref "/library/internet/ipaddress#ipaddress.IPv4Network" >}}) 和 [`IPv6Network`]({{< ref "/library/internet/ipaddress#ipaddress.IPv6Network" >}}) 中 `is_global` 和 `is_private` 的行为。 （由 Jakub Stasiak 在 [gh-113171](https://github.com/python/cpython/issues/113171) 中贡献。）

### itertools

- [`batched()`]({{< ref "/library/functional/itertools#itertools.batched" >}}) 新增了 *strict* 形参，它会在最后一批次数据小于指定批准大小时引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。 （由 Raymond Hettinger 在 [gh-113202](https://github.com/python/cpython/issues/113202) 中贡献。）

### marshal

- 在模块函数中增加了 *allow_code* 形参。 传入 `allow_code=False` 将防止在 Python 各版本间不兼容的代码对象的序列化和反序列化。 （由 Serhiy Storchaka 在 [gh-113626](https://github.com/python/cpython/issues/113626) 中贡献。）

### math

- 新增函数 [`fma()`]({{< ref "/library/numeric/math#math.fma" >}}) 可执行合并的乘法-加法运算。 此函数只需一轮操作即可计算 `x * y + z`，从而避免了任何中间步骤导致的精度损失。 它包装了 C99 所提供的 `fma()` 函数，并且遵从针对特殊情况的 IEEE 754 "fusedMultiplyAdd" 运算规范。 （由 Mark Dickinson 和 Victor Stinner 在 [gh-73468](https://github.com/python/cpython/issues/73468) 中贡献。）

### mimetypes

- 增加了 [`guess_file_type()`]({{< ref "/library/netdata/mimetypes#mimetypes.guess_file_type" >}}) 函数用于根据文件系统路径来猜测 MIME 类型。 在 [`guess_type()`]({{< ref "/library/netdata/mimetypes#mimetypes.guess_type" >}}) 中使用路径的做法现在已是 [soft deprecated]({{< ref "/glossary/idx#term-soft-deprecated" >}})。 （由 Serhiy Storchaka 在 [gh-66543](https://github.com/python/cpython/issues/66543) 中贡献。）

### mmap

- 现在 [`mmap`]({{< ref "/library/ipc/mmap#mmap.mmap" >}}) 在 Windows 上当被映射的内存由于文件系统错误或访问限制而不可访问时将获得保护以避免崩溃。 （由 Jannis Weigend 在 [gh-118209](https://github.com/python/cpython/issues/118209) 中贡献。）
- [`mmap`]({{< ref "/library/ipc/mmap#mmap.mmap" >}}) 具有新的 [`seekable()`]({{< ref "/library/ipc/mmap#mmap.mmap.seekable" >}}) 方法将在需要可定位的文件型对象时被使用。 现在 [`seek()`]({{< ref "/library/ipc/mmap#mmap.mmap.seek" >}}) 方法将返回一个新的绝对位置。 （由 Donghee Na 和 Sylvie Liberman 在 [gh-111835](https://github.com/python/cpython/issues/111835) 中贡献。）
- [`mmap`]({{< ref "/library/ipc/mmap#mmap.mmap" >}}) 新增了 UNIX 专属的 *trackfd* 形参用来控制文件描述符的复制；如为假值，则由 *fileno* 指定的文件描述符将不会被复制。 （由 Zackery Spytz 和 Petr Viktorin 在 [gh-78502](https://github.com/python/cpython/issues/78502) 中贡献。）

### multiprocessing

- 工作线程和进程的默认数据现在是使用 [`os.process_cpu_count()`]({{< ref "/library/allos/os#os.process_cpu_count" >}}) 而不是 [`os.cpu_count()`]({{< ref "/library/allos/os#os.cpu_count" >}}) 来选择的。 （由 Victor Stinner 在 [gh-109649](https://github.com/python/cpython/issues/109649) 中贡献。）

### os

- 增加了 [`process_cpu_count()`]({{< ref "/library/allos/os#os.process_cpu_count" >}}) 函数用于获取当前进程的调用方线程可以使用的逻辑 CPU 核心数量。 （由 Victor Stinner 在 [gh-109649](https://github.com/python/cpython/issues/109649) 中贡献。）
- [`cpu_count()`]({{< ref "/library/allos/os#os.cpu_count" >}}) 和 [`process_cpu_count()`]({{< ref "/library/allos/os#os.process_cpu_count" >}}) 可通过新的环境变量 [`PYTHON_CPU_COUNT`]({{< ref "/using/cmdline#envvar-PYTHON_CPU_COUNT" >}}) 或新的命令行选项 [`-X cpu_count`]({{< ref "/using/cmdline#cmdoption-X" >}}) 来覆盖。 此选项对于需要在不修改应用程序代码或容器本身的情况下限制一个容器系统的 CPU 资源的用户会很有用处。 （由 Donghee Na 在 [gh-109595](https://github.com/python/cpython/issues/109595) 中贡献。）
- 通过 [`timerfd_create()`]({{< ref "/library/allos/os#os.timerfd_create" >}}), [`timerfd_settime()`]({{< ref "/library/allos/os#os.timerfd_settime" >}}), [`timerfd_settime_ns()`]({{< ref "/library/allos/os#os.timerfd_settime_ns" >}}), [`timerfd_gettime()`]({{< ref "/library/allos/os#os.timerfd_gettime" >}}), [`timerfd_gettime_ns()`]({{< ref "/library/allos/os#os.timerfd_gettime_ns" >}}), [`TFD_NONBLOCK`]({{< ref "/library/allos/os#os.TFD_NONBLOCK" >}}), [`TFD_CLOEXEC`]({{< ref "/library/allos/os#os.TFD_CLOEXEC" >}}), [`TFD_TIMER_ABSTIME`]({{< ref "/library/allos/os#os.TFD_TIMER_ABSTIME" >}}) 和 [`TFD_TIMER_CANCEL_ON_SET`]({{< ref "/library/allos/os#os.TFD_TIMER_CANCEL_ON_SET" >}}) 增加了针对 Linux 的 *[计算器文件描述符](https://manpages.debian.org/timerfd_create(2))* 的 [低层级接口]({{< ref "/library/allos/os#os-timerfd" >}})。 （由 Masaru Tsuchiyama 在 [gh-108277](https://github.com/python/cpython/issues/108277) 中贡献。）
- 在 Windows 上现在同时增加了对 [`lchmod()`]({{< ref "/library/allos/os#os.lchmod" >}}) 和 [`chmod()`]({{< ref "/library/allos/os#os.chmod" >}}) 的 *follow_symlinks* 参数的支持。 请注意在 Windows 上 `lchmod()` 中的 *follow_symlinks* 的默认值为 `False`。 （由 Serhiy Storchaka 在 [gh-59616](https://github.com/python/cpython/issues/59616) 中贡献。）
- 在 Windows 上现在同时增加了 [`fchmod()`]({{< ref "/library/allos/os#os.fchmod" >}}) 和对 [`chmod()`]({{< ref "/library/allos/os#os.chmod" >}}) 中的文件描述符的支持。 （由 Serhiy Storchaka 在 [gh-113191](https://github.com/python/cpython/issues/113191) 中贡献。）
- 在 Windows 上，[`mkdir()`]({{< ref "/library/allos/os#os.mkdir" >}}) 和 [`makedirs()`]({{< ref "/library/allos/os#os.makedirs" >}}) 现在支持传入 *mode* 值 `0o700` 以对新目录应用访问控制。 这会隐式地影响 [`tempfile.mkdtemp()`]({{< ref "/library/filesys/tempfile#tempfile.mkdtemp" >}}) 并可缓解 [**CVE 2024-4030**](https://www.cve.org/CVERecord?id=CVE-2024-4030)。 其他的 *mode* 值仍然会被忽略。 （由 Steve Dower 在 [gh-118486](https://github.com/python/cpython/issues/118486) 中贡献。）
- 现在 [`posix_spawn()`]({{< ref "/library/allos/os#os.posix_spawn" >}}) 可接受 `None` 作为 *env* 参数，这将让新产生的进程使用当前进程的环境。 （由 Jakub Kulik 在 [gh-113119](https://github.com/python/cpython/issues/113119) 中贡献。）.)
- 在支持 `posix_spawn_file_actions_addclosefrom_np()` 的平台上 [`posix_spawn()`]({{< ref "/library/allos/os#os.posix_spawn" >}}) 现在可以在 *file_actions* 形参中使用 [`POSIX_SPAWN_CLOSEFROM`]({{< ref "/library/allos/os#os.POSIX_SPAWN_CLOSEFROM" >}}) 属性。 （由 Jakub Kulik 在 [gh-113117](https://github.com/python/cpython/issues/113117) 中贡献。）.)

### os.path

- 增加了 [`isreserved()`]({{< ref "/library/filesys/os_path#os.path.isreserved" >}}) 用于检查一个路径在当前系统中是否为保留路径。 此函数仅在 Windows 上可用。 （由 Barney Gale 在 [gh-88569](https://github.com/python/cpython/issues/88569) 中贡献。）
- 在 Windows 上，[`isabs()`]({{< ref "/library/filesys/os_path#os.path.isabs" >}}) 将不再把以恰好一个斜杠 (`\` 或 `/`) 开头的路径视为绝对路径。 （由 Barney Gale 和 Jon Foster 在 [gh-44626](https://github.com/python/cpython/issues/44626) 中贡献。）
- 现在即使文件不可访问 [`realpath()`]({{< ref "/library/filesys/os_path#os.path.realpath" >}}) 也能够解析 MS-DOS 风格的文件名。 （由 Moonsik Park 在 [gh-82367](https://github.com/python/cpython/issues/82367) 中贡献。）

### pathlib

- 增加了 [`UnsupportedOperation`]({{< ref "/library/filesys/pathlib#pathlib.UnsupportedOperation" >}})，它会在一个路径操作不受支持时代替 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}}) 被引发。 （由 Barney Gale 在 [gh-89812](https://github.com/python/cpython/issues/89812) 中贡献。）
- 新增了一个用于根据 'file' URI (`file:///`) 来创建 [`Path`]({{< ref "/library/filesys/pathlib#pathlib.Path" >}}) 对象的构造器 [`Path.from_uri()`]({{< ref "/library/filesys/pathlib#pathlib.Path.from_uri" >}})。 （由 Barney Gale 在 [gh-107465](https://github.com/python/cpython/issues/107465) 中贡献。）
- 增加了 [`PurePath.full_match()`]({{< ref "/library/filesys/pathlib#pathlib.PurePath.full_match" >}}) 用于匹配带有 shell 风格通配符的路径，包括递归通配符 "`**`"。 （由 Barney Gale 在 [gh-73435](https://github.com/python/cpython/issues/73435) 中贡献。）
- 增加了 [`PurePath.parser`]({{< ref "/library/filesys/pathlib#pathlib.PurePath.parser" >}}) 类属性以存储用于低层级路径解析与合并的 [`os.path`]({{< ref "/library/filesys/os_path#module-os.path" >}}) 实现。 这可以是 `posixpath` 或 `ntpath`。
- 为 [`Path.glob()`]({{< ref "/library/filesys/pathlib#pathlib.Path.glob" >}}) 和 [`rglob()`]({{< ref "/library/filesys/pathlib#pathlib.Path.rglob" >}}) 增加了 *recurse_symlinks* 仅限关键字参数。 （由 Barney Gale 在 [gh-77609](https://github.com/python/cpython/issues/77609) 中贡献。）
- 现在当给出以 "`**`" 结束的模式时 [`Path.glob()`]({{< ref "/library/filesys/pathlib#pathlib.Path.glob" >}}) 和 [`rglob()`]({{< ref "/library/filesys/pathlib#pathlib.Path.rglob" >}}) 将返回文件和目录。 在之前版本中，仅会返回目录。 （由 Barney Gale 在 [gh-70303](https://github.com/python/cpython/issues/70303) 中贡献。）.)
- 为 [`Path.is_file`]({{< ref "/library/filesys/pathlib#pathlib.Path.is_file" >}}), [`Path.is_dir`]({{< ref "/library/filesys/pathlib#pathlib.Path.is_dir" >}}), [`Path.owner()`]({{< ref "/library/filesys/pathlib#pathlib.Path.owner" >}}) 和 [`Path.group()`]({{< ref "/library/filesys/pathlib#pathlib.Path.group" >}}) 增加了 *follow_symlinks* 仅限关键字参数。 （由 Barney Gale 在 [gh-105793](https://github.com/python/cpython/issues/105793) 中，以及 Kamil Turek 在 [gh-107962](https://github.com/python/cpython/issues/107962) 中贡献。）

### pdb

- 现在 [`breakpoint()`]({{< ref "/library/functions#breakpoint" >}}) 和 [`set_trace()`]({{< ref "/library/debug/pdb#pdb.set_trace" >}}) 会立即进入调试器而不是在被执行代码的下一行进入。 这一更改可防止当 `breakpoint()` 位于上下文末尾 时调试器在上下文以外被中断。 （由 Tian Gao 在 [gh-118579](https://github.com/python/cpython/issues/118579) 中贡献。）
- 当设置了 [`sys.flags.safe_path`]({{< ref "/library/python/sys#sys.flags.safe_path" >}}) 时 `sys.path[0]` 将不会再被替换为被调试脚本的目录。 （由 Tian Gao 和 Christian Walther 在 [gh-111762](https://github.com/python/cpython/issues/111762) 中贡献。）
- 现在支持将 [`zipapp`]({{< ref "/library/distribution/zipapp#module-zipapp" >}}) 作为调试目标。 （由 Tian Gao 在 [gh-118501](https://github.com/python/cpython/issues/118501) 中贡献。）
- 添加了在 [`pm()`]({{< ref "/library/debug/pdb#pdb.pm" >}}) 中进行事后调试期间使用 Pdb 新增的 [`exceptions [exc_number\]`]({{< ref "/library/debug/pdb#pdbcommand-exceptions" >}}) 命令在串连的异常之间移动的能力。 （由 Matthias Bussonnier 在 [gh-106676](https://github.com/python/cpython/issues/106676) 中贡献。）
- 以一条 pdb 命令打头的表达式和语句现在会被正确地标识并执行。 （由 Tian Gao 在 [gh-108464](https://github.com/python/cpython/issues/108464) 中贡献。）

### queue

- 增加了 [`Queue.shutdown`]({{< ref "/library/concurrency/queue#queue.Queue.shutdown" >}}) 和 [`ShutDown`]({{< ref "/library/concurrency/queue#queue.ShutDown" >}}) 用于管理队列的终结。 （由 Laurie Opperman 和 Yves Duprat 在 [gh-104750](https://github.com/python/cpython/issues/104750) 中贡献。）

### random

- 增加了一个 [命令行接口]({{< ref "/library/numeric/random#random-cli" >}})。 （由 Hugo van Kemenade 在 [gh-118131](https://github.com/python/cpython/issues/118131) 中贡献。）

### re

- 将 `re.error` 重命名为 [`PatternError`]({{< ref "/library/text/re#re.PatternError" >}}) 以改善准确性。 `re.error` 仍被保留用于向下兼容。

### shutil

- 在 [`chown()`]({{< ref "/library/filesys/shutil#shutil.chown" >}}) 中增加了对 *dir_fd* 和 *follow_symlinks* 关键字参数的支持。 （由 Berker Peksag 和 Tahia K 在 [gh-62308](https://github.com/python/cpython/issues/62308) 中贡献。）

### site

- 现在 `.pth` 文件将先使用 UTF-8 来解码，如果 UTF-8 解码失败再使用 [locale encoding]({{< ref "/glossary/idx#term-locale-encoding" >}})。 （由 Inada Naoki 在 [gh-117802](https://github.com/python/cpython/issues/117802) 中贡献。）

### sqlite3

- 现在当一个 [`Connection`]({{< ref "/library/persistence/sqlite3#sqlite3.Connection" >}}) 对象未被显式地 [`关闭`]({{< ref "/library/persistence/sqlite3#sqlite3.Connection.close" >}}) 时将发出 [`ResourceWarning`]({{< ref "/library/exceptions#ResourceWarning" >}})。 （由 Erlend E. Aasland 在 [gh-105539](https://github.com/python/cpython/issues/105539) 中贡献。）
- 为 [`Connection.iterdump()`]({{< ref "/library/persistence/sqlite3#sqlite3.Connection.iterdump" >}}) 增加了 *filter* 仅限关键字形参用于过滤要转储的数据库对象。 （由 Mariusz Felisiak 在 [gh-91602](https://github.com/python/cpython/issues/91602) 中贡献。）

### ssl

- 现在 [`create_default_context()`]({{< ref "/library/ipc/ssl#ssl.create_default_context" >}}) API 将在其默认旗标中包括 [`VERIFY_X509_PARTIAL_CHAIN`]({{< ref "/library/ipc/ssl#ssl.VERIFY_X509_PARTIAL_CHAIN" >}}) 和 [`VERIFY_X509_STRICT`]({{< ref "/library/ipc/ssl#ssl.VERIFY_X509_STRICT" >}})。

  ​	备注

   

  [`VERIFY_X509_STRICT`]({{< ref "/library/ipc/ssl#ssl.VERIFY_X509_STRICT" >}}) 可能会拒绝下层 OpenSSL 实现本来会接受的 [**RFC 5280**](https://datatracker.ietf.org/doc/html/rfc5280.html) 以前的证书或格式错误的证书。 虽然不建议禁用此功能，但你可以使用以下方式禁用它：

  ```
  import ssl
  
  ctx = ssl.create_default_context()
  ctx.verify_flags &= ~ssl.VERIFY_X509_STRICT
  ```

  （由 William Woodruff 在 [gh-112389](https://github.com/python/cpython/issues/112389) 贡献。）

### statistics

- 增加了用于核密度估计的 [`kde()`]({{< ref "/library/numeric/statistics#statistics.kde" >}})。 这使得根据固定数量的离散样本估计连续概率密度函数成为可能。 （由 Raymond Hettinger 在 [gh-115863](https://github.com/python/cpython/issues/115863) 中贡献。）
- 增加了 [`kde_random()`]({{< ref "/library/numeric/statistics#statistics.kde_random" >}}) 用来从 [`kde()`]({{< ref "/library/numeric/statistics#statistics.kde" >}}) 创建的估计概率密度函数进行取样。 （由 Hettinger 在 [gh-115863](https://github.com/python/cpython/issues/115863) 中贡献。）



### subprocess

- 现在 [`subprocess`]({{< ref "/library/concurrency/subprocess#module-subprocess" >}}) 模块会在更多场合下使用 [`posix_spawn()`]({{< ref "/library/allos/os#os.posix_spawn" >}}) 函数。

  需要注意的是，当 *close_fds* 为 `True` 时（默认值），则将在 C 库提供了 `posix_spawn_file_actions_addclosefrom_np()` 时使用 [`posix_spawn()`]({{< ref "/library/allos/os#os.posix_spawn" >}})，这包括近期的 Linux, FreeBSD 和 Solaris 版本。 在 Linux，其性能应当与现有的 Linux `vfork()` 基础代码类似。

  如果你需要强制 [`subprocess`]({{< ref "/library/concurrency/subprocess#module-subprocess" >}}) 绝不使用 [`posix_spawn()`]({{< ref "/library/allos/os#os.posix_spawn" >}}) 可以将私有的控制节点 `subprocess._USE_POSIX_SPAWN` 设为 `False`。 如果你这样设置的话请在 [issue tracker](https://docs.python.org/zh-cn/3.13/bugs.html#using-the-tracker) 中报告你的理由和平台相关的细节以便我们能够为大家改进 API 的选择逻辑。 （由 Jakub Kulik 在 [gh-113117](https://github.com/python/cpython/issues/113117) 中贡献。）

### sys

- 增加了 [`_is_interned()`]({{< ref "/library/python/sys#sys._is_interned" >}}) 函数用于检测字符串是否被内部化。 此函数不保证在所有的 Python 实现中均存在。 （由 Serhiy Storchaka 在 [gh-78573](https://github.com/python/cpython/issues/78573) 中贡献。）

### tempfile

- 在 Windows 上，[`tempfile.mkdtemp()`]({{< ref "/library/filesys/tempfile#tempfile.mkdtemp" >}}) 所使用的默认模式 `0o700` 由于 [`os.mkdir()`]({{< ref "/library/allos/os#os.mkdir" >}}) 的更改现在将限制对新目录的访问。 这是对 [**CVE 2024-4030**](https://www.cve.org/CVERecord?id=CVE-2024-4030) 的缓解措施。 （由 Steve Dower 在 [gh-118486](https://github.com/python/cpython/issues/118486) 中贡献。）

### time

- 在 Windows 上，[`monotonic()`]({{< ref "/library/allos/time#time.monotonic" >}}) 现在将使用精度为 1 微秒的 `QueryPerformanceCounter()` 时钟，而不是精度只有 15.6 毫秒的 `GetTickCount64()` 时钟。 （由 Victor Stinner 在 [gh-88494](https://github.com/python/cpython/issues/88494) 中贡献。）
- 在 Windows 上，[`time()`]({{< ref "/library/allos/time#time.time" >}}) 现在将使用精度为 1 微秒的 `GetSystemTimePreciseAsFileTime()` 时钟，代替精度为 15.6 毫秒的 `GetSystemTimeAsFileTime()` 时钟。 （由 Victor Stinner 在 [gh-63207](https://github.com/python/cpython/issues/63207) 中贡献。）

### tkinter

- 增加了 [`tkinter`]({{< ref "/library/tk/tkinter#module-tkinter" >}}) 控件方法: `tk_busy_hold()`, `tk_busy_configure()`, `tk_busy_cget()`, `tk_busy_forget()`, `tk_busy_current()` 和 `tk_busy_status()`。 （由 Miguel, klappnase 和 Serhiy Storchaka 在 [gh-72684](https://github.com/python/cpython/issues/72684) 中贡献。）
- 现在 [`tkinter`]({{< ref "/library/tk/tkinter#module-tkinter" >}}) 控件 `wm_attributes()` 接受不带负号前缀的属性名称来获取窗口属性，例如 `w.wm_attributes('alpha')` 并允许指定属性和值以关键字参数形式来设置，例如 `w.wm_attributes(alpha=0.5)`。 （由 Serhiy Storchaka 在 [gh-43457](https://github.com/python/cpython/issues/43457) 中贡献。）
- 通过使用新的可选关键字形参 *return_python_dict*，现在 `wm_attributes()` 可将属性作为 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 返回。 （由 Serhiy Storchaka 在 [gh-43457](https://github.com/python/cpython/issues/43457) 中贡献。）
- 现在当使用新的可选仅限关键字形参 *return_ints* 时 `Text.count()` 可以返回一个简单的 [`int`]({{< ref "/library/functions#int" >}})。 在其他情况下，将以 1 个元素的元组形式返回单个计数值或者 `None`。 （由 Serhiy Storchaka 在 [gh-97928](https://github.com/python/cpython/issues/97928) 中贡献。）
- 在 [`tkinter.ttk.Style`]({{< ref "/library/tk/tkinter_ttk#tkinter.ttk.Style" >}}) 的 [`element_create()`]({{< ref "/library/tk/tkinter_ttk#tkinter.ttk.Style.element_create" >}}) 方法中增加了对 "vsapi" 元素类型的支持。 （由 Serhiy Storchaka 在 [gh-68166](https://github.com/python/cpython/issues/68166) 中贡献。）
- 为 Tkinter 的控件增加了 `after_info()` 方法。 （由 Cheryl Sabella 在 [gh-77020](https://github.com/python/cpython/issues/77020) 中贡献。）
- 为 `PhotoImage` 新增 `copy_replace()` 方法用于将一个图像的某个区域拷贝到另一个图像，可能带有像素缩放、子采样，或两者皆有。 （由 Serhiy Storchaka 在 [gh-118225](https://github.com/python/cpython/issues/118225) 中贡献。）
- 为 `PhotoImage` 的方法 `copy()`, `zoom()` 和 `subsample()` 增加了 *from_coords* 形参。 为 `PhotoImage` 的方法 `copy()` 增加了 *zoom* 和 *subsample* 形参。 （由 Serhiy Storchaka 在 [gh-118225](https://github.com/python/cpython/issues/118225) 中贡献。）
- 增加了 `PhotoImage` 方法 `read()` 用于从文件读取图像以及 `data()` 用于获取图像数据。 为 `write()` 方法增加了 *background* 和 *grayscale* 形参。 （由 Serhiy Storchaka 在 [gh-118271](https://github.com/python/cpython/issues/118271) 中贡献。）.)

### 回溯

- 为 [`TracebackException`]({{< ref "/library/python/traceback#traceback.TracebackException" >}}) 增加了 [`exc_type_str`]({{< ref "/library/python/traceback#traceback.TracebackException.exc_type_str" >}}) 属性，它用于保存 *exc_type* 字符串表示。 弃用了 [`exc_type`]({{< ref "/library/python/traceback#traceback.TracebackException.exc_type" >}}) 属性，它用于保存类型对象本身。 增加了 *save_exc_type* 形参 (默认值为 `True`) 用于指明 `exc_type` 是否应当被保存。 （由 Irit Katriel 在 [gh-112332](https://github.com/python/cpython/issues/112332) 中贡献。）
- 为 [`TracebackException.format_exception_only()`]({{< ref "/library/python/traceback#traceback.TracebackException.format_exception_only" >}}) 增加了新的 *show_group* 仅限关键字形参用于（递归地）格式化 [`BaseExceptionGroup`]({{< ref "/library/exceptions#BaseExceptionGroup" >}}) 实例中嵌套的异常。 （由 Irit Katriel 在 [gh-105292](https://github.com/python/cpython/issues/105292) 中贡献。）

### types

- 现在 [`SimpleNamespace`]({{< ref "/library/datatypes/types#types.SimpleNamespace" >}}) 可以接受单个位置参数来初始化命名空间的各个参数值。 该参数必须为映射或键值对的可迭代对象。 （由 Serhiy Storchaka 在 [gh-108191](https://github.com/python/cpython/issues/108191) 中贡献。）

### typing

- [**PEP 705**](https://peps.python.org/pep-0705/): 增加 [`ReadOnly`]({{< ref "/library/development/typing#typing.ReadOnly" >}})，一个针对类型检查器的特殊类型结构，用于将 [`TypedDict`]({{< ref "/library/development/typing#typing.TypedDict" >}}) 的项标记为只读。
- [**PEP 742**](https://peps.python.org/pep-0742/): 增加 [`TypeIs`]({{< ref "/library/development/typing#typing.TypeIs" >}})，一个可被用于指示类型检查器如何细化类型的类型结构。
- 增加 [`NoDefault`]({{< ref "/library/development/typing#typing.NoDefault" >}})，一个用于代表 [`typing`]({{< ref "/library/development/typing#module-typing" >}}) 模块中某些形参的默认值的哨兵对象。 （由 Jelle Zijlstra 在 [gh-116126](https://github.com/python/cpython/issues/116126) 中贡献。）
- 增加 [`get_protocol_members()`]({{< ref "/library/development/typing#typing.get_protocol_members" >}}) 用于返回定义一个 [`typing.Protocol`]({{< ref "/library/development/typing#typing.Protocol" >}}) 的成员的集合。 （由 Jelle Zijlstra 在 [gh-104873](https://github.com/python/cpython/issues/104873) 中贡献。）
- 增加 [`is_protocol()`]({{< ref "/library/development/typing#typing.is_protocol" >}}) 用于检查一个类是否属于 [`Protocol`]({{< ref "/library/development/typing#typing.Protocol" >}})。 （由 Jelle Zijlstra 在 [gh-104873](https://github.com/python/cpython/issues/104873) 中贡献。）
- 现在 [`ClassVar`]({{< ref "/library/development/typing#typing.ClassVar" >}}) 可以被嵌套在 [`Final`]({{< ref "/library/development/typing#typing.Final" >}}) 中，反之亦然。 （由 Mehdi Drissi 在 [gh-89547](https://github.com/python/cpython/issues/89547) 中贡献。）

### unicodedata

- 将 Unicode 数据库更新到 [15.1.0 版](https://www.unicode.org/versions/Unicode15.1.0/)。 （由 James Gerity 在 [gh-109559](https://github.com/python/cpython/issues/109559) 中贡献。）

### venv

- 增加了对在虚拟环境目录中添加源码控制管理 (SCM) 忽略文件的支持。 在默认情况下，Git 已受到支持。 此特性是以可被扩展为支持其他 SCM 的通过 API 选择启用 ([`EnvBuilder`]({{< ref "/library/distribution/venv#venv.EnvBuilder" >}}) 和 [`create()`]({{< ref "/library/distribution/venv#venv.create" >}}))，并通过 CLI 使用 `--without-scm-ignore-files` 选择禁用的方式实现的。 （由 Brett Cannon 在 [gh-108125](https://github.com/python/cpython/issues/108125) 中贡献。）

### warnings

- [**PEP 702**](https://peps.python.org/pep-0702/): 新的 [`warnings.deprecated()`]({{< ref "/library/python/warnings#warnings.deprecated" >}}) 装饰器提供了一种将弃用消息传送给 [static type checker]({{< ref "/glossary/idx#term-static-type-checker" >}}) 并在使用已弃用的类和函数时发出警告的方式。 当被装饰的函数或类在运行时被使用时也可以发出 [`DeprecationWarning`]({{< ref "/library/exceptions#DeprecationWarning" >}})。 （由 Jelle Zijlstra 在 [gh-104003](https://github.com/python/cpython/issues/104003) 中贡献。）

### xml

- 通过添加五个新方法来允许控制 Expat >=2.6.0 重解析延迟 ([**CVE 2023-52425**](https://www.cve.org/CVERecord?id=CVE-2023-52425))：

  - [`xml.etree.ElementTree.XMLParser.flush()`]({{< ref "/library/markup/xml_etree_elementtree#xml.etree.ElementTree.XMLParser.flush" >}})
  - [`xml.etree.ElementTree.XMLPullParser.flush()`]({{< ref "/library/markup/xml_etree_elementtree#xml.etree.ElementTree.XMLPullParser.flush" >}})
  - [`xml.parsers.expat.xmlparser.GetReparseDeferralEnabled()`]({{< ref "/library/markup/pyexpat#xml.parsers.expat.xmlparser.GetReparseDeferralEnabled" >}})
  - [`xml.parsers.expat.xmlparser.SetReparseDeferralEnabled()`]({{< ref "/library/markup/pyexpat#xml.parsers.expat.xmlparser.SetReparseDeferralEnabled" >}})
  - `xml.sax.expatreader.ExpatParser.flush()`

  （由 Sebastian Pipping 在 [gh-115623](https://github.com/python/cpython/issues/115623) 中贡献。）

- 为 [`iterparse()`]({{< ref "/library/markup/xml_etree_elementtree#xml.etree.ElementTree.iterparse" >}}) 所返回的迭代器增加了 `close()` 方法用于执行显式的清理。 （由 Serhiy Storchaka 在 [gh-69893](https://github.com/python/cpython/issues/69893) 中贡献。）

### zipimport

- 增加了对 [ZIP64](https://en.wikipedia.org/wiki/Zip_(file_format)#ZIP64) 格式的文件的支持。 大家都喜欢更庞大的数据，对吧？ （由 Tim Hatch 在 [gh-94146](https://github.com/python/cpython/issues/94146) 中贡献。）.)

## 性能优化

- 一些标准库模块的导入时间得到了显著改善。 例如，[`typing`]({{< ref "/library/development/typing#module-typing" >}}) 模块的导入时间通过移除对 [`re`]({{< ref "/library/text/re#module-re" >}}) 和 [`contextlib`]({{< ref "/library/python/contextlib#module-contextlib" >}}) 的依赖而减少了大约三分之一。 其他获得导入时间加速的模块包括 [`email.utils`]({{< ref "/library/netdata/email/email_utils#module-email.utils" >}}), [`enum`]({{< ref "/library/datatypes/enum#module-enum" >}}), [`functools`]({{< ref "/library/functional/functools#module-functools" >}}), [`importlib.metadata`]({{< ref "/library/modules/importlib_metadata#module-importlib.metadata" >}}) 和 [`threading`]({{< ref "/library/concurrency/threading#module-threading" >}})。 （由 Alex Waygood, Shantanu Jain, Adam Turner, Daniel Hollas 等人在 [gh-109653](https://github.com/python/cpython/issues/109653) 中贡献。）
- 现在对于大量输入 [`textwrap.indent()`]({{< ref "/library/text/textwrap#textwrap.indent" >}}) 相比之前可提速大约 30%。 （由 Inada Naoki 在 [gh-107369](https://github.com/python/cpython/issues/107369) 中贡献。）
- 现在 [`subprocess`]({{< ref "/library/concurrency/subprocess#module-subprocess" >}}) 模块会在更多场合下使用 [`posix_spawn()`]({{< ref "/library/allos/os#os.posix_spawn" >}}) 函数，包括在许多现代系统平台上当 *close_fds* 为 `True` (默认值) 的时候。 当在 FreeBSD 和 Solaris 上启动进程时这应该能提供显著的性能提升。 请参阅上面的 [subprocess]({{< ref "/whatsnew/idx#whatsnew313-subprocess" >}}) 小节了解详情。 （由 Jakub Kulik 在 [gh-113117](https://github.com/python/cpython/issues/113117) 中贡献。）

## 被移除的模块与 API



### PEP 594: 从标准库中移除“死电池”

[**PEP 594**](https://peps.python.org/pep-0594/) 提议从标准库移除 19 个模块，它们因其古旧、过时或不安全的状态而被非正式地称呼为‘死电池’。 下列所有模块在 Python 3.11 中被弃用，现在已被移除：

- `aifc`

- `audioop`

- `chunk`

- `cgi` 和 `cgitb`

  - 对于 `GET` 和 `HEAD` 请求 `cgi.FieldStorage` 通常可以用 [`urllib.parse.parse_qsl()`]({{< ref "/library/internet/urllib/urllib_parse#urllib.parse.parse_qsl" >}}) 来替换，而对于 `POST` 和 `PUT` 请求则可以用 [`email.message`]({{< ref "/library/netdata/email/email_message#module-email.message" >}}) 模块或 [multipart](https://pypi.org/project/multipart/) 库。

  - `cgi.parse()` 可被替换为在想要的查询字符串上直接调用 [`urllib.parse.parse_qs()`]({{< ref "/library/internet/urllib/urllib_parse#urllib.parse.parse_qs" >}})，除非输入为 `multipart/form-data`，它应当如下文针对 `cgi.parse_multipart()` 所描述的那样被替换。

  - `cgi.parse_header()` 可被 [`email`]({{< ref "/library/netdata/email#module-email" >}}) 包中的功能所替换，它实现了相同的 MIME RFC。 例如，使用 [`email.message.EmailMessage`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage" >}}):

    ```
    from email.message import EmailMessage
    
    msg = EmailMessage()
    msg['content-type'] = 'application/json; charset="utf8"'
    main, params = msg.get_content_type(), msg['content-type'].params
    ```

  - `cgi.parse_multipart()` can be replaced with the functionality in the [`email`]({{< ref "/library/netdata/email#module-email" >}}) package, which implements the same MIME RFCs, or with the [multipart](https://pypi.org/project/multipart/) library. For example, the [`email.message.EmailMessage`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage" >}}) and [`email.message.Message`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message" >}}) classes.

- `crypt` and the private `_crypt` extension. The [`hashlib`]({{< ref "/library/crypto/hashlib#module-hashlib" >}}) module may be an appropriate replacement when simply hashing a value is required. Otherwise, various third-party libraries on PyPI are available:

  - [bcrypt](https://pypi.org/project/bcrypt/): 用于软件和服务器的现代密码哈希算法。
  - [passlib](https://pypi.org/project/passlib/): 支持超过 over 30 种方案的综合密码哈希算法框架。
  - [argon2-cffi](https://pypi.org/project/argon2-cffi/): 安全的 Argon2 密码哈希算法。
  - [legacycrypt](https://pypi.org/project/legacycrypt/): 针对 POSIX 加密库调用和相关功能的 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 包装器。
  - [crypt_r](https://pypi.org/project/crypt_r/): `crypt` 模块的分叉，针对 *[crypt_r(3)](https://manpages.debian.org/crypt_r(3))* 库调用和相关功能和包装器。

- `imghdr`: The [filetype](https://pypi.org/project/filetype/), [puremagic](https://pypi.org/project/puremagic/), or [python-magic](https://pypi.org/project/python-magic/) libraries should be used as replacements. For example, the `puremagic.what()` function can be used to replace the `imghdr.what()` function for all file formats that were supported by `imghdr`.

- `mailcap`: 改用 [`mimetypes`]({{< ref "/library/netdata/mimetypes#module-mimetypes" >}}) 模块。

- `msilib`

- `nis`

- `nntplib`: 改用 PyPI 上的 [pynntp](https://pypi.org/project/pynntp/) 库。

- `ossaudiodev`: 对于音频回放，改用 PyPI 上的 [pygame](https://pypi.org/project/pygame/) 库。

- `pipes`: Use the [`subprocess`]({{< ref "/library/concurrency/subprocess#module-subprocess" >}}) module instead. Use [`shlex.quote()`]({{< ref "/library/frameworks/shlex#shlex.quote" >}}) to replace the undocumented `pipes.quote` function.

- `sndhdr`: 应当使用 [filetype](https://pypi.org/project/filetype/), [puremagic](https://pypi.org/project/puremagic/) 或 [python-magic](https://pypi.org/project/python-magic/) 库作为替代。

- `spwd`: 改用 PyPI 上的 [python-pam](https://pypi.org/project/python-pam/) 库。

- `sunau`

- `telnetlib`，改用 PyPI 上的 [telnetlib3](https://pypi.org/project/telnetlib3/) 或 [Exscript](https://pypi.org/project/Exscript/) 库。

- `uu`: 改用 the [`base64`]({{< ref "/library/netdata/base64#module-base64" >}}) 模块，作为一款现代化的替代。

- `xdrlib`

​	（由 Victor Stinner 和 Zachary Ware 在 [gh-104773](https://github.com/python/cpython/issues/104773) 和 [gh-104780](https://github.com/python/cpython/issues/104780) 中贡献。）

### 2to3

- 移除 **2to3** 程序和 `lib2to3` 模块，此前已在 Python 3.11 中被弃用。 （由 Victor Stinner 在 [gh-104780](https://github.com/python/cpython/issues/104780) 中贡献。）

### builtins

- Remove support for chained [`classmethod`]({{< ref "/library/functions#classmethod" >}}) descriptors (introduced in [gh-63272](https://github.com/python/cpython/issues/63272)). These can no longer be used to wrap other descriptors, such as [`property`]({{< ref "/library/functions#property" >}}). The core design of this feature was flawed and led to several problems. To "pass-through" a [`classmethod`]({{< ref "/library/functions#classmethod" >}}), consider using the `__wrapped__` attribute that was added in Python 3.10. (Contributed by Raymond Hettinger in [gh-89519](https://github.com/python/cpython/issues/89519).)
- Raise a [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}}) when calling [`frame.clear()`]({{< ref "/reference/datamodel#frame.clear" >}}) on a suspended frame (as has always been the case for an executing frame). (Contributed by Irit Katriel in [gh-79932](https://github.com/python/cpython/issues/79932).)

### configparser

- Remove the undocumented `LegacyInterpolation` class, deprecated in the docstring since Python 3.2, and at runtime since Python 3.11. (Contributed by Hugo van Kemenade in [gh-104886](https://github.com/python/cpython/issues/104886).)

### importlib.metadata

- Remove deprecated subscript ([`__getitem__()`]({{< ref "/reference/datamodel#object.__getitem__" >}})) access for [EntryPoint]({{< ref "/library/modules/importlib_metadata#entry-points" >}}) objects. (Contributed by Jason R. Coombs in [gh-113175](https://github.com/python/cpython/issues/113175).)

### locale

- Remove the `locale.resetlocale()` function, deprecated in Python 3.11. Use `locale.setlocale(locale.LC_ALL, "")` instead. (Contributed by Victor Stinner in [gh-104783](https://github.com/python/cpython/issues/104783).)

### opcode

- Move `opcode.ENABLE_SPECIALIZATION` to `_opcode.ENABLE_SPECIALIZATION`. This field was added in 3.12, it was never documented, and is not intended for external use. (Contributed by Irit Katriel in [gh-105481](https://github.com/python/cpython/issues/105481).)
- Remove `opcode.is_pseudo()`, `opcode.MIN_PSEUDO_OPCODE`, and `opcode.MAX_PSEUDO_OPCODE`, which were added in Python 3.12, but were neither documented nor exposed through [`dis`]({{< ref "/library/language/dis#module-dis" >}}), and were not intended to be used externally. (Contributed by Irit Katriel in [gh-105481](https://github.com/python/cpython/issues/105481).)

### pathlib

- 移除了使用 [`Path`]({{< ref "/library/filesys/pathlib#pathlib.Path" >}}) 对象作为上下文管理器的能力。 此功能自 Python 3.9 起已被弃用并设为空操作。 （由 Barney Gale 在 [gh-83863](https://github.com/python/cpython/issues/83863) 中贡献。）

### re

- 移除了未被写入文档、已被弃用且已不能工作的 `re.template()` 函数和 `re.TEMPLATE` / `re.T` 旗标。 （由 Serhiy Storchaka 和 Nikita Sobolev 在 [gh-105687](https://github.com/python/cpython/issues/105687) 中贡献。）

### tkinter.tix

- 移除了 `tkinter.tix` 模块，它在 Python 3.6 中已被弃用。 该模块所包装的第三方库 Tix 已不再维护。 （由 Zachary Ware 在 [gh-75552](https://github.com/python/cpython/issues/75552) 中贡献。）.)

### turtle

- Remove the `RawTurtle.settiltangle()` method, deprecated in the documentation since Python 3.1 and at runtime since Python 3.11. (Contributed by Hugo van Kemenade in [gh-104876](https://github.com/python/cpython/issues/104876).)

### typing

- Remove the `typing.io` and `typing.re` namespaces, deprecated since Python 3.8. The items in those namespaces can be imported directly from the [`typing`]({{< ref "/library/development/typing#module-typing" >}}) module. (Contributed by Sebastian Rittau in [gh-92871](https://github.com/python/cpython/issues/92871).)
- 移除了创建 [`TypedDict`]({{< ref "/library/development/typing#typing.TypedDict" >}}) 类型的关键字参数方法，它在 Python 3.11 中已被弃用。 （由 Tomas Roun 在 [gh-104786](https://github.com/python/cpython/issues/104786) 中贡献。）

### unittest

- 移除了下列 [`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 函数，它们在 Python 3.11 中已被弃用：

  - `unittest.findTestCases()`
  - `unittest.makeSuite()`
  - `unittest.getTestCaseNames()`

  请改用 [`TestLoader`]({{< ref "/library/development/unittest#unittest.TestLoader" >}}) 方法:

  - [`loadTestsFromModule()`]({{< ref "/library/development/unittest#unittest.TestLoader.loadTestsFromModule" >}})
  - [`loadTestsFromTestCase()`]({{< ref "/library/development/unittest#unittest.TestLoader.loadTestsFromTestCase" >}})
  - [`getTestCaseNames()`]({{< ref "/library/development/unittest#unittest.TestLoader.getTestCaseNames" >}})

  （由 Hugo van Kemenade 在 [gh-104835](https://github.com/python/cpython/issues/104835) 中贡献。）

- Remove the untested and undocumented `TestProgram.usageExit()` method, deprecated in Python 3.11. (Contributed by Hugo van Kemenade in [gh-104992](https://github.com/python/cpython/issues/104992).)

### urllib

- Remove the *cafile*, *capath*, and *cadefault* parameters of the [`urllib.request.urlopen()`]({{< ref "/library/internet/urllib/urllib_request#urllib.request.urlopen" >}}) function, deprecated in Python 3.6. Use the *context* parameter instead with an [`SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) instance. The [`ssl.SSLContext.load_cert_chain()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.load_cert_chain" >}}) function can be used to load specific certificates, or let [`ssl.create_default_context()`]({{< ref "/library/ipc/ssl#ssl.create_default_context" >}}) select the operating system's trusted certificate authority (CA) certificates. (Contributed by Victor Stinner in [gh-105382](https://github.com/python/cpython/issues/105382).)

### webbrowser

- Remove the untested and undocumented `MacOSX` class, deprecated in Python 3.11. Use the `MacOSXOSAScript` class (introduced in Python 3.2) instead. (Contributed by Hugo van Kemenade in [gh-104804](https://github.com/python/cpython/issues/104804).)
- Remove the deprecated `MacOSXOSAScript._name` attribute. Use the [`MacOSXOSAScript.name`]({{< ref "/library/internet/webbrowser#webbrowser.controller.name" >}}) attribute instead. (Contributed by Nikita Sobolev in [gh-105546](https://github.com/python/cpython/issues/105546).)

## 新的弃用

- [用户自定义函数]({{< ref "/reference/datamodel#user-defined-funcs" >}}):
  - Deprecate assignment to a function's [`__code__`]({{< ref "/reference/datamodel#function.__code__" >}}) attribute, where the new code object's type does not match the function's type. The different types are: plain function, generator, async generator, and coroutine. (Contributed by Irit Katriel in [gh-81137](https://github.com/python/cpython/issues/81137).)
- [`array`]({{< ref "/library/datatypes/array#module-array" >}}):
  - Deprecate the `'u'` format code (`wchar_t`) at runtime. This format code has been deprecated in documentation since Python 3.3, and will be removed in Python 3.16. Use the `'w'` format code ([`Py_UCS4`](https://docs.python.org/zh-cn/3.13/c-api/unicode.html#c.Py_UCS4)) for Unicode characters instead. (Contributed by Hugo van Kemenade in [gh-80480](https://github.com/python/cpython/issues/80480).)
- [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}):
  - Deprecate the undocumented `SetPointerType()` function, to be removed in Python 3.15. (Contributed by Victor Stinner in [gh-105733](https://github.com/python/cpython/issues/105733).)
  - [Soft-deprecate]({{< ref "/glossary/idx#term-soft-deprecated" >}}) the [`ARRAY()`]({{< ref "/library/allos/ctypes#ctypes.ARRAY" >}}) function in favour of `type * length` multiplication. (Contributed by Victor Stinner in [gh-105733](https://github.com/python/cpython/issues/105733).)
- [`decimal`]({{< ref "/library/numeric/decimal#module-decimal" >}}):
  - Deprecate the non-standard and undocumented [`Decimal`]({{< ref "/library/numeric/decimal#decimal.Decimal" >}}) format specifier `'N'`, which is only supported in the `decimal` module's C implementation. (Contributed by Serhiy Storchaka in [gh-89902](https://github.com/python/cpython/issues/89902).)
- [`dis`]({{< ref "/library/language/dis#module-dis" >}}):
  - Deprecate the `HAVE_ARGUMENT` separator. Check membership in [`hasarg`]({{< ref "/library/language/dis#dis.hasarg" >}}) instead. (Contributed by Irit Katriel in [gh-109319](https://github.com/python/cpython/issues/109319).)
- [`getopt`]({{< ref "/library/superseded/getopt#module-getopt" >}}) and [`optparse`]({{< ref "/library/superseded/optparse#module-optparse" >}}):
  - Both modules are now [soft deprecated]({{< ref "/glossary/idx#term-soft-deprecated" >}}), with [`argparse`]({{< ref "/library/allos/argparse#module-argparse" >}}) preferred for new projects. This is a new soft-deprecation for the `getopt` module, whereas the `optparse` module was already *de facto* soft deprecated. (Contributed by Victor Stinner in [gh-106535](https://github.com/python/cpython/issues/106535).)
- [`gettext`]({{< ref "/library/i18n/gettext#module-gettext" >}}):
  - Deprecate non-integer numbers as arguments to functions and methods that consider plural forms in the `gettext` module, even if no translation was found. (Contributed by Serhiy Storchaka in [gh-88434](https://github.com/python/cpython/issues/88434).)
- [`glob`]({{< ref "/library/filesys/glob#module-glob" >}}):
  - Deprecate the undocumented `glob0()` and `glob1()` functions. Use [`glob()`]({{< ref "/library/filesys/glob#glob.glob" >}}) and pass a [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}}) specifying the root directory to the *root_dir* parameter instead. (Contributed by Barney Gale in [gh-117337](https://github.com/python/cpython/issues/117337).)
- [`http.server`]({{< ref "/library/internet/http_server#module-http.server" >}}):
  - Deprecate [`CGIHTTPRequestHandler`]({{< ref "/library/internet/http_server#http.server.CGIHTTPRequestHandler" >}}), to be removed in Python 3.15. Process-based CGI HTTP servers have been out of favor for a very long time. This code was outdated, unmaintained, and rarely used. It has a high potential for both security and functionality bugs. (Contributed by Gregory P. Smith in [gh-109096](https://github.com/python/cpython/issues/109096).)
  - Deprecate the `--cgi` flag to the **python -m http.server** command-line interface, to be removed in Python 3.15. (Contributed by Gregory P. Smith in [gh-109096](https://github.com/python/cpython/issues/109096).)
- [`mimetypes`]({{< ref "/library/netdata/mimetypes#module-mimetypes" >}}):
  - [Soft-deprecate]({{< ref "/glossary/idx#term-soft-deprecated" >}}) file path arguments to [`guess_type()`]({{< ref "/library/netdata/mimetypes#mimetypes.guess_type" >}}), use [`guess_file_type()`]({{< ref "/library/netdata/mimetypes#mimetypes.guess_file_type" >}}) instead. (Contributed by Serhiy Storchaka in [gh-66543](https://github.com/python/cpython/issues/66543).)
- [`re`]({{< ref "/library/text/re#module-re" >}}):
  - Deprecate passing the optional *maxsplit*, *count*, or *flags* arguments as positional arguments to the module-level [`split()`]({{< ref "/library/text/re#re.split" >}}), [`sub()`]({{< ref "/library/text/re#re.sub" >}}), and [`subn()`]({{< ref "/library/text/re#re.subn" >}}) functions. These parameters will become [keyword-only]({{< ref "/glossary/idx#keyword-only-parameter" >}}) in a future version of Python. (Contributed by Serhiy Storchaka in [gh-56166](https://github.com/python/cpython/issues/56166).)
- [`pathlib`]({{< ref "/library/filesys/pathlib#module-pathlib" >}}):
  - Deprecate [`PurePath.is_reserved()`]({{< ref "/library/filesys/pathlib#pathlib.PurePath.is_reserved" >}}), to be removed in Python 3.15. Use [`os.path.isreserved()`]({{< ref "/library/filesys/os_path#os.path.isreserved" >}}) to detect reserved paths on Windows. (Contributed by Barney Gale in [gh-88569](https://github.com/python/cpython/issues/88569).)
- [`platform`]({{< ref "/library/allos/platform#module-platform" >}}):
  - 弃用了 [`java_ver()`]({{< ref "/library/allos/platform#platform.java_ver" >}})，并将在 Python 3.15 中移除。 此函数仅对 Jython 支持有用，具有令人困惑的 API，并且大部分未经测试。 （由 Nikita Sobolev 在 [gh-116349](https://github.com/python/cpython/issues/116349) 中贡献。）.)
- [`pydoc`]({{< ref "/library/development/pydoc#module-pydoc" >}}):
  - 弃用未写入文档的 `ispackage()` 函数。 （由 Zackery Spytz 在 [gh-64020](https://github.com/python/cpython/issues/64020) 中贡献。）
- [`sqlite3`]({{< ref "/library/persistence/sqlite3#module-sqlite3" >}}):
  - Deprecate passing more than one positional argument to the [`connect()`]({{< ref "/library/persistence/sqlite3#sqlite3.connect" >}}) function and the [`Connection`]({{< ref "/library/persistence/sqlite3#sqlite3.Connection" >}}) constructor. The remaining parameters will become keyword-only in Python 3.15. (Contributed by Erlend E. Aasland in [gh-107948](https://github.com/python/cpython/issues/107948).)
  - Deprecate passing name, number of arguments, and the callable as keyword arguments for [`Connection.create_function()`]({{< ref "/library/persistence/sqlite3#sqlite3.Connection.create_function" >}}) and [`Connection.create_aggregate()`]({{< ref "/library/persistence/sqlite3#sqlite3.Connection.create_aggregate" >}}) These parameters will become positional-only in Python 3.15. (Contributed by Erlend E. Aasland in [gh-108278](https://github.com/python/cpython/issues/108278).)
  - Deprecate passing the callback callable by keyword for the [`set_authorizer()`]({{< ref "/library/persistence/sqlite3#sqlite3.Connection.set_authorizer" >}}), [`set_progress_handler()`]({{< ref "/library/persistence/sqlite3#sqlite3.Connection.set_progress_handler" >}}), and [`set_trace_callback()`]({{< ref "/library/persistence/sqlite3#sqlite3.Connection.set_trace_callback" >}}) [`Connection`]({{< ref "/library/persistence/sqlite3#sqlite3.Connection" >}}) methods. The callback callables will become positional-only in Python 3.15. (Contributed by Erlend E. Aasland in [gh-108278](https://github.com/python/cpython/issues/108278).)
- [`sys`]({{< ref "/library/python/sys#module-sys" >}}):
  - Deprecate the [`_enablelegacywindowsfsencoding()`]({{< ref "/library/python/sys#sys._enablelegacywindowsfsencoding" >}}) function, to be removed in Python 3.16. Use the [`PYTHONLEGACYWINDOWSFSENCODING`]({{< ref "/using/cmdline#envvar-PYTHONLEGACYWINDOWSFSENCODING" >}}) environment variable instead. (Contributed by Inada Naoki in [gh-73427](https://github.com/python/cpython/issues/73427).)
- [`tarfile`]({{< ref "/library/archiving/tarfile#module-tarfile" >}}):
  - 弃用了未写入文档也未被使用的 `TarFile.tarfile` 属性，并将在 Python 3.16 中移除。 （在 [gh-115256](https://github.com/python/cpython/issues/115256) 中贡献。）
- [`traceback`]({{< ref "/library/python/traceback#module-traceback" >}}):
  - 已弃用 [`TracebackException.exc_type`]({{< ref "/library/python/traceback#traceback.TracebackException.exc_type" >}}) 属性。 请改用 [`TracebackException.exc_type_str`]({{< ref "/library/python/traceback#traceback.TracebackException.exc_type_str" >}})。 （由 Irit Katriel 在 [gh-112332](https://github.com/python/cpython/issues/112332) 中贡献。）
- [`typing`]({{< ref "/library/development/typing#module-typing" >}}):
  - Deprecate the undocumented keyword argument syntax for creating [`NamedTuple`]({{< ref "/library/development/typing#typing.NamedTuple" >}}) classes (e.g. `Point = NamedTuple("Point", x=int, y=int)`), to be removed in Python 3.15. Use the class-based syntax or the functional syntax instead. (Contributed by Alex Waygood in [gh-105566](https://github.com/python/cpython/issues/105566).)
  - Deprecate omitting the *fields* parameter when creating a [`NamedTuple`]({{< ref "/library/development/typing#typing.NamedTuple" >}}) or [`typing.TypedDict`]({{< ref "/library/development/typing#typing.TypedDict" >}}) class, and deprecate passing `None` to the *fields* parameter of both types. Python 3.15 will require a valid sequence for the *fields* parameter. To create a NamedTuple class with zero fields, use `class NT(NamedTuple): pass` or `NT = NamedTuple("NT", ())`. To create a TypedDict class with zero fields, use `class TD(TypedDict): pass` or `TD = TypedDict("TD", {})`. (Contributed by Alex Waygood in [gh-105566](https://github.com/python/cpython/issues/105566) and [gh-105570](https://github.com/python/cpython/issues/105570).)
  - Deprecate the [`typing.no_type_check_decorator()`]({{< ref "/library/development/typing#typing.no_type_check_decorator" >}}) decorator function, to be removed in in Python 3.15. After eight years in the [`typing`]({{< ref "/library/development/typing#module-typing" >}}) module, it has yet to be supported by any major type checker. (Contributed by Alex Waygood in [gh-106309](https://github.com/python/cpython/issues/106309).)
  - Deprecate [`typing.AnyStr`]({{< ref "/library/development/typing#typing.AnyStr" >}}). In Python 3.16, it will be removed from `typing.__all__`, and a [`DeprecationWarning`]({{< ref "/library/exceptions#DeprecationWarning" >}}) will be emitted at runtime when it is imported or accessed. It will be removed entirely in Python 3.18. Use the new [type parameter syntax]({{< ref "/reference/compound_stmts#type-params" >}}) instead. (Contributed by Michael The in [gh-107116](https://github.com/python/cpython/issues/107116).)
- [`wave`]({{< ref "/library/mm/wave#module-wave" >}}):
  - Deprecate the [`getmark()`]({{< ref "/library/mm/wave#wave.Wave_read.getmark" >}}), `setmark()`, and [`getmarkers()`]({{< ref "/library/mm/wave#wave.Wave_read.getmarkers" >}}) methods of the [`Wave_read`]({{< ref "/library/mm/wave#wave.Wave_read" >}}) and [`Wave_write`]({{< ref "/library/mm/wave#wave.Wave_write" >}}) classes, to be removed in Python 3.15. (Contributed by Victor Stinner in [gh-105096](https://github.com/python/cpython/issues/105096).)

### 计划在 Python 3.14 中移除

- [`argparse`]({{< ref "/library/allos/argparse#module-argparse" >}}): `argparse.BooleanOptionalAction` 的 *type*, *choices* 和 *metavar* 形参已被弃用并将在 3.14 中移除。 （由 Nikita Sobolev 在 [gh-92248](https://github.com/python/cpython/issues/92248) 中贡献。）

- [`ast`]({{< ref "/library/language/ast#module-ast" >}}): 以下特性自 Python 3.8 起已在文档中声明弃用，现在当运行时如果它们被访问或使用时将发出 [`DeprecationWarning`]({{< ref "/library/exceptions#DeprecationWarning" >}})，并将在 Python 3.14 中移除：

  - `ast.Num`
  - `ast.Str`
  - `ast.Bytes`
  - `ast.NameConstant`
  - `ast.Ellipsis`

  请改用 [`ast.Constant`]({{< ref "/library/language/ast#ast.Constant" >}})。 （由 Serhiy Storchaka 在 [gh-90953](https://github.com/python/cpython/issues/90953) 中贡献。）

- [`asyncio`]({{< ref "/library/ipc/asyncio#module-asyncio" >}}):

  - 子监视器类 [`MultiLoopChildWatcher`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.MultiLoopChildWatcher" >}}), [`FastChildWatcher`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.FastChildWatcher" >}}), [`AbstractChildWatcher`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.AbstractChildWatcher" >}}) 和 [`SafeChildWatcher`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.SafeChildWatcher" >}}) 已被弃用并将在 Python 3.14 中移除。 （由 Kumar Aditya 在 [gh-94597](https://github.com/python/cpython/issues/94597) 中贡献。）
  - [`asyncio.set_child_watcher()`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.set_child_watcher" >}})、[`asyncio.get_child_watcher()`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.get_child_watcher" >}})、[`asyncio.AbstractEventLoopPolicy.set_child_watcher()`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.AbstractEventLoopPolicy.set_child_watcher" >}}) 和 [`asyncio.AbstractEventLoopPolicy.get_child_watcher()`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.AbstractEventLoopPolicy.get_child_watcher" >}}) 已弃用，并将在 Python 3.14 中移除。（由 Kumar Aditya 在 [gh-94597](https://github.com/python/cpython/issues/94597) 中贡献。）
  - 现在默认事件循环策略的 [`get_event_loop()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.get_event_loop" >}}) 方法在当前事件循环未设置并决定创建一个时将发出 [`DeprecationWarning`]({{< ref "/library/exceptions#DeprecationWarning" >}})。 （由 Serhiy Storchaka 和 Guido van Rossum 在 [gh-100160](https://github.com/python/cpython/issues/100160) 中贡献。）

- [`collections.abc`]({{< ref "/library/datatypes/collections_abc#module-collections.abc" >}}): 已弃用 [`ByteString`]({{< ref "/library/datatypes/collections_abc#collections.abc.ByteString" >}})。 推荐改用 `Sequence` 或 [`Buffer`]({{< ref "/library/datatypes/collections_abc#collections.abc.Buffer" >}})。 用于类型标注时，则推荐并集运算符，如 `bytes | bytearray`，或 [`collections.abc.Buffer`]({{< ref "/library/datatypes/collections_abc#collections.abc.Buffer" >}})。 （由 Shantanu Jain 在 [gh-91896](https://github.com/python/cpython/issues/91896) 中贡献。）

- [`email`]({{< ref "/library/netdata/email#module-email" >}}): 已弃用 [`email.utils.localtime()`]({{< ref "/library/netdata/email/email_utils#email.utils.localtime" >}}) 中的 *isdst* 形参。 （由 Alan Williams 在 [gh-72346](https://github.com/python/cpython/issues/72346) 中贡献。）

- [`importlib.abc`]({{< ref "/library/modules/importlib#module-importlib.abc" >}}) 中已弃用的类：

  - `importlib.abc.ResourceReader`
  - `importlib.abc.Traversable`
  - `importlib.abc.TraversableResources`

  使用 [`importlib.resources.abc`]({{< ref "/library/modules/importlib_resources_abc#module-importlib.resources.abc" >}}) 类代替:

  - [`importlib.resources.abc.Traversable`]({{< ref "/library/modules/importlib_resources_abc#importlib.resources.abc.Traversable" >}})
  - [`importlib.resources.abc.TraversableResources`]({{< ref "/library/modules/importlib_resources_abc#importlib.resources.abc.TraversableResources" >}})

  （由 Jason R. Coombs 和 Hugo van Kemenade 在 [gh-93963](https://github.com/python/cpython/issues/93963) 中贡献。）

- [`itertools`]({{< ref "/library/functional/itertools#module-itertools" >}}) 具有对 copy, deepcopy 和 pickle 等操作的未写入文档的、低效的、历史上充满问题的且不稳定的支持。 这将在 3.14 中移除以显著减少代码量和维护负担。 （由 Raymond Hettinger 在 [gh-101588](https://github.com/python/cpython/issues/101588) 中贡献。）

- [`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}): 默认的启动方法在目前默认使用 `'fork'` 的 Linux, BSD 和其他非 macOS POSIX 平台上将改为更安全的方法 ([gh-84559](https://github.com/python/cpython/issues/84559))。 为此添加运行时警告将带来糟糕的体验因为大部分代码并不会关心这个问题。 当你的代码 *需要* `'fork'` 时请使用 [`get_context()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.get_context" >}}) 或 [`set_start_method()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.set_start_method" >}}) API 来显式地指明。 参见 [上下文和启动方法]({{< ref "/library/concurrency/multiprocessing#multiprocessing-start-methods" >}})。

- [`pathlib`]({{< ref "/library/filesys/pathlib#module-pathlib" >}}): [`is_relative_to()`]({{< ref "/library/filesys/pathlib#pathlib.PurePath.is_relative_to" >}}) 和 [`relative_to()`]({{< ref "/library/filesys/pathlib#pathlib.PurePath.relative_to" >}}): 传入额外参数的做法已被弃用。

- [`pkgutil`]({{< ref "/library/modules/pkgutil#module-pkgutil" >}}): 现在 [`find_loader()`]({{< ref "/library/modules/pkgutil#pkgutil.find_loader" >}}) 和 [`get_loader()`]({{< ref "/library/modules/pkgutil#pkgutil.get_loader" >}}) 将引发 [`DeprecationWarning`]({{< ref "/library/exceptions#DeprecationWarning" >}})；请改用 [`importlib.util.find_spec()`]({{< ref "/library/modules/importlib#importlib.util.find_spec" >}})。 （由 Nikita Sobolev 在 [gh-97850](https://github.com/python/cpython/issues/97850) 中贡献。）

- [`pty`]({{< ref "/library/unix/pty#module-pty" >}}):

  - `master_open()`: 使用 [`pty.openpty()`]({{< ref "/library/unix/pty#pty.openpty" >}})。
  - `slave_open()`: 使用 [`pty.openpty()`]({{< ref "/library/unix/pty#pty.openpty" >}})。

- [`sqlite3`]({{< ref "/library/persistence/sqlite3#module-sqlite3" >}}):

  - [`version`]({{< ref "/library/persistence/sqlite3#sqlite3.version" >}}) 和 [`version_info`]({{< ref "/library/persistence/sqlite3#sqlite3.version_info" >}})。
  - 如果使用了 [命名占位符]({{< ref "/library/persistence/sqlite3#sqlite3-placeholders" >}}) 且 *parameters* 是一个序列而不是 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 则选择 [`execute()`]({{< ref "/library/persistence/sqlite3#sqlite3.Cursor.execute" >}}) 和 [`executemany()`]({{< ref "/library/persistence/sqlite3#sqlite3.Cursor.executemany" >}})。

- [`typing`]({{< ref "/library/development/typing#module-typing" >}}): [`ByteString`]({{< ref "/library/development/typing#typing.ByteString" >}}) 自 Python 3.9 起已被弃用，现在当被使用时将会发出 [`DeprecationWarning`]({{< ref "/library/exceptions#DeprecationWarning" >}})。

- [`urllib`]({{< ref "/library/internet/urllib#module-urllib" >}}): `urllib.parse.Quoter` 已被弃用：它不应被作为公有 API。 （由 Gregory P. Smith 在 [gh-88168](https://github.com/python/cpython/issues/88168) 中贡献。）

### Python 3.15 中的待移除功能

- 导入系统：
  - 当设置 [`__spec__.cached`]({{< ref "/library/modules/importlib#importlib.machinery.ModuleSpec.cached" >}}) 失败时在模块上设置 [`__cached__`]({{< ref "/reference/datamodel#module.__cached__" >}}) 的做法已被弃用。 在 Python 3.15 中，`__cached__` 将不会再被导入系统或标准库纳入考虑。 ([gh-97879](https://github.com/python/cpython/issues/97879))
  - 当设备 [`__spec__.parent`]({{< ref "/library/modules/importlib#importlib.machinery.ModuleSpec.parent" >}}) 失败时在模块上设置 [`__package__`]({{< ref "/reference/datamodel#module.__package__" >}}) 的做法已被弃用。 在 Python 3.15 中，`__package__` 将不会再被导入系统或标准库纳入考虑。 ([gh-97879](https://github.com/python/cpython/issues/97879))
- [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}):
  - 未写入文档的 `ctypes.SetPointerType()` 函数自 Python 3.13 起已被弃用。
- [`http.server`]({{< ref "/library/internet/http_server#module-http.server" >}}):
  - 过时且很少被使用的 [`CGIHTTPRequestHandler`]({{< ref "/library/internet/http_server#http.server.CGIHTTPRequestHandler" >}}) 自 Python 3.13 起已被弃用。 不存在直接的替代品。 对于建立带有请求处理器的 Web 服务程序来说 *任何东西* 都比 CGI 要好。
  - 用于 **python -m http.server** 命令行界面的 `--cgi` 旗标自 Python 3.13 起已被弃用。
- [`locale`]({{< ref "/library/i18n/locale#module-locale" >}}):
  - [`getdefaultlocale()`]({{< ref "/library/i18n/locale#locale.getdefaultlocale" >}}) 函数自 Python 3.11 起已被弃用。 最初计划在 Python 3.13 中移除它 ([gh-90817](https://github.com/python/cpython/issues/90817))，但已被推迟至 Python 3.15。 请改用 [`getlocale()`]({{< ref "/library/i18n/locale#locale.getlocale" >}}), [`setlocale()`]({{< ref "/library/i18n/locale#locale.setlocale" >}}) 和 [`getencoding()`]({{< ref "/library/i18n/locale#locale.getencoding" >}})。 （由 Hugo van Kemenade 在 [gh-111187](https://github.com/python/cpython/issues/111187) 中贡献。）
- [`pathlib`]({{< ref "/library/filesys/pathlib#module-pathlib" >}}):
  - [`PurePath.is_reserved()`]({{< ref "/library/filesys/pathlib#pathlib.PurePath.is_reserved" >}}) 自 Python 3.13 起已被弃用。 请使用 [`os.path.isreserved()`]({{< ref "/library/filesys/os_path#os.path.isreserved" >}}) 来检测 Windows 上的保留路径。
- [`platform`]({{< ref "/library/allos/platform#module-platform" >}}):
  - [`java_ver()`]({{< ref "/library/allos/platform#platform.java_ver" >}}) 自 Python 3.13 起已被弃用。 此函数仅对 Jython 支持有用，具有令人困惑的 API，并且大部分未经测试。
- [`threading`]({{< ref "/library/concurrency/threading#module-threading" >}}):
  - 在 Python 3.15 中 [`RLock()`]({{< ref "/library/concurrency/threading#threading.RLock" >}}) 将不再接受参数。 传入参数的做法自 Python 3.14 起已被弃用，因为 Python 版本不接受任何参数，而 C 版本允许任意数量的位置或关键字参数，但会忽略所有参数。
- [`types`]({{< ref "/library/datatypes/types#module-types" >}}):
  - [`types.CodeType`]({{< ref "/library/datatypes/types#types.CodeType" >}}): Accessing [`co_lnotab`]({{< ref "/reference/datamodel#codeobject.co_lnotab" >}}) was deprecated in [**PEP 626**](https://peps.python.org/pep-0626/) since 3.10 and was planned to be removed in 3.12, but it only got a proper [`DeprecationWarning`]({{< ref "/library/exceptions#DeprecationWarning" >}}) in 3.12. May be removed in 3.15. (Contributed by Nikita Sobolev in [gh-101866](https://github.com/python/cpython/issues/101866).)
- [`typing`]({{< ref "/library/development/typing#module-typing" >}}):
  - 未写入文档的用于创建 [`NamedTuple`]({{< ref "/library/development/typing#typing.NamedTuple" >}}) 类的关键字参数语法 (例如 `Point = NamedTuple("Point", x=int, y=int)`) 自 Python 3.13 起已被弃用。 请改用基于类的语法或函数语法。
  - [`typing.no_type_check_decorator()`]({{< ref "/library/development/typing#typing.no_type_check_decorator" >}}) 装饰器自 Python 3.13 起已被弃用。 存在于 [`typing`]({{< ref "/library/development/typing#module-typing" >}}) 模块八年之后，它仍未被任何主要类型检查器所支持。
- [`wave`]({{< ref "/library/mm/wave#module-wave" >}}):
  - [`Wave_read`]({{< ref "/library/mm/wave#wave.Wave_read" >}}) 和 [`Wave_write`]({{< ref "/library/mm/wave#wave.Wave_write" >}}) 类的 [`getmark()`]({{< ref "/library/mm/wave#wave.Wave_read.getmark" >}}), `setmark()` 和 [`getmarkers()`]({{< ref "/library/mm/wave#wave.Wave_read.getmarkers" >}}) 方法自 Python 3.13 起已被弃用。

### Pending removal in Python 3.16

- 导入系统：
  - Setting [`__loader__`]({{< ref "/reference/datamodel#module.__loader__" >}}) on a module while failing to set [`__spec__.loader`]({{< ref "/library/modules/importlib#importlib.machinery.ModuleSpec.loader" >}}) is deprecated. In Python 3.16, `__loader__` will cease to be set or taken into consideration by the import system or the standard library.
- [`array`]({{< ref "/library/datatypes/array#module-array" >}}):
  - `'u'` 格式代码 (`wchar_t`) 自 Python 3.3 起已在文档中弃用并自 Python 3.13 起在运行时弃用。 对于 Unicode 字符请改用 `'w'` 格式代码 ([`Py_UCS4`](https://docs.python.org/zh-cn/3.13/c-api/unicode.html#c.Py_UCS4))。
- [`asyncio`]({{< ref "/library/ipc/asyncio#module-asyncio" >}}):
  - `asyncio.iscoroutinefunction()` is deprecated and will be removed in Python 3.16, use [`inspect.iscoroutinefunction()`]({{< ref "/library/python/inspect#inspect.iscoroutinefunction" >}}) instead. (Contributed by Jiahao Li and Kumar Aditya in [gh-122875](https://github.com/python/cpython/issues/122875).)
- [`builtins`]({{< ref "/library/python/builtins#module-builtins" >}}):
  - 对布尔类型 `~True` 或 `~False` 执行按位取反的操作自 Python 3.12 起已被弃用，因为它会产生奇怪和不直观的结果 (`-2` and `-1`)。 请改用 `not x` 来对布尔值执行逻辑否操作。 对于需要对下层整数执行按位取反操作的少数场合，请显式地将其转换为 `int` (`~int(x)`)。
- [`shutil`]({{< ref "/library/filesys/shutil#module-shutil" >}}):
  - `ExecError` 异常自 Python 3.14 起已被弃用。 它自 Python 3.4 起就未被 `shutil` 中的任何函数所使用，现在是 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}}) 的一个别名。
- [`symtable`]({{< ref "/library/language/symtable#module-symtable" >}}):
  - [`Class.get_methods`]({{< ref "/library/language/symtable#symtable.Class.get_methods" >}}) 方法自 Python 3.14 起被弃用。
- [`sys`]({{< ref "/library/python/sys#module-sys" >}}):
  - [`_enablelegacywindowsfsencoding()`]({{< ref "/library/python/sys#sys._enablelegacywindowsfsencoding" >}}) 函数自 Python 3.13 起被弃用。 请改用 [`PYTHONLEGACYWINDOWSFSENCODING`]({{< ref "/using/cmdline#envvar-PYTHONLEGACYWINDOWSFSENCODING" >}}) 环境变量。
- [`tarfile`]({{< ref "/library/archiving/tarfile#module-tarfile" >}}):
  - 未写入文档也未被使用的 `TarFile.tarfile` 属性自 Python 3.13 起被弃用。

### 计划在未来版本中移除

​	以下API将会被移除，尽管具体时间还未确定。

- [`argparse`]({{< ref "/library/allos/argparse#module-argparse" >}}): 嵌套参数分组和嵌套互斥分组的做法已被弃用。
- [`array`]({{< ref "/library/datatypes/array#module-array" >}}) 的 `'u'` 格式代码 ([gh-57281](https://github.com/python/cpython/issues/57281))
- [`builtins`]({{< ref "/library/python/builtins#module-builtins" >}}):
  - `bool(NotImplemented)`。
  - 生成器: `throw(type, exc, tb)` 和 `athrow(type, exc, tb)` 签名已被弃用：请改用 `throw(exc)` 和 `athrow(exc)`，即单参数签名。
  - 目前 Python 接受数字类字面值后面紧跟关键字的写法，例如 `0in x`, `1or x`, `0if 1else 2`。 它允许像 `[0x1for x in y]` 这样令人困惑且有歧义的表达式 (它可以被解读为 `[0x1 for x in y]` 或者 `[0x1f or x in y]`)。 如果数字类字面值后面紧跟关键字 [`and`]({{< ref "/reference/expressions#and" >}}), [`else`]({{< ref "/reference/compound_stmts#else" >}}), [`for`]({{< ref "/reference/compound_stmts#for" >}}), [`if`]({{< ref "/reference/compound_stmts#if" >}}), [`in`]({{< ref "/reference/expressions#in" >}}), [`is`]({{< ref "/reference/expressions#is" >}}) 和 [`or`]({{< ref "/reference/expressions#or" >}}) 中的一个将会引发语法警告。 在未来的版本中它将改为语法错误。 ([gh-87999](https://github.com/python/cpython/issues/87999))
  - 对 `__index__()` 和 `__int__()` 方法返回非 int 类型的支持：将要求这些方法必须返回 [`int`]({{< ref "/library/functions#int" >}}) 的子类的实例。
  - 对 `__float__()` 方法返回 [`float`]({{< ref "/library/functions#float" >}}) 的子类的支持：将要求这些方法必须返回 [`float`]({{< ref "/library/functions#float" >}}) 的实例。
  - 对 `__complex__()` 方法返回 [`complex`]({{< ref "/library/functions#complex" >}}) 的子类的支持：将要求这些方法必须返回 [`complex`]({{< ref "/library/functions#complex" >}}) 的实例。
  - 将 `int()` 委托给 `__trunc__()` 方法。
  - 传入一个复数作为 [`complex()`]({{< ref "/library/functions#complex" >}}) 构造器中的 *real* 或 *imag* 参数的做法现在已被弃用；它应当仅作为单个位置参数被传入。 （由 Serhiy Storchaka 在 [gh-109218](https://github.com/python/cpython/issues/109218) 中贡献。）.)
- [`calendar`]({{< ref "/library/datatypes/calendar#module-calendar" >}}): `calendar.January` 和 `calendar.February` 常量已被弃用并由 [`calendar.JANUARY`]({{< ref "/library/datatypes/calendar#calendar.JANUARY" >}}) 和 [`calendar.FEBRUARY`]({{< ref "/library/datatypes/calendar#calendar.FEBRUARY" >}}) 替代。 （由 Prince Roshan 在 [gh-103636](https://github.com/python/cpython/issues/103636) 中贡献。）
- [`codeobject.co_lnotab`]({{< ref "/reference/datamodel#codeobject.co_lnotab" >}}): 改用 [`codeobject.co_lines()`]({{< ref "/reference/datamodel#codeobject.co_lines" >}}) 方法。
- [`datetime`]({{< ref "/library/datatypes/datetime#module-datetime" >}}):
  - [`utcnow()`]({{< ref "/library/datatypes/datetime#datetime.datetime.utcnow" >}}): 使用 `datetime.datetime.now(tz=datetime.UTC)`。
  - [`utcfromtimestamp()`]({{< ref "/library/datatypes/datetime#datetime.datetime.utcfromtimestamp" >}}): 使用 `datetime.datetime.fromtimestamp(timestamp, tz=datetime.UTC)`。
- [`gettext`]({{< ref "/library/i18n/gettext#module-gettext" >}}): 复数值必须是一个整数。
- [`importlib`]({{< ref "/library/modules/importlib#module-importlib" >}}):
  - `load_module()` 方法：改用 `exec_module()`。
  - [`cache_from_source()`]({{< ref "/library/modules/importlib#importlib.util.cache_from_source" >}}) *debug_override* 形参已被弃用：改用 *optimization* 形能耐。
- [`importlib.metadata`]({{< ref "/library/modules/importlib_metadata#module-importlib.metadata" >}}):
  - `EntryPoints` 元组接口。
  - 返回值中隐式的 `None`。
- [`logging`]({{< ref "/library/allos/logging#module-logging" >}}): `warn()` 方法自 Python 3.3 起已被弃用，请改用 [`warning()`]({{< ref "/library/allos/logging#logging.warning" >}})。
- [`mailbox`]({{< ref "/library/netdata/mailbox#module-mailbox" >}}): 对 StringIO 输入和文本模式的使用已被弃用，改用 BytesIO 和二进制模式。
- [`os`]({{< ref "/library/allos/os#module-os" >}}): 在多线程的进程中调用 [`os.register_at_fork()`]({{< ref "/library/allos/os#os.register_at_fork" >}})。
- `pydoc.ErrorDuringImport`: 使用元组值作为 *exc_info* 形参的做法已被弃用，应使用异常实例。
- [`re`]({{< ref "/library/text/re#module-re" >}}): 现在对于正则表达式中的数字分组引用和分组名称将应用更严格的规则。 现在只接受 ASCII 数字序列作为数字引用。 字节串模式和替换字符串中的分组名称现在只能包含 ASCII 字母和数字以及下划线。 （由 Serhiy Storchaka 在 [gh-91760](https://github.com/python/cpython/issues/91760) 中贡献。）
- `sre_compile`, `sre_constants` 和 `sre_parse` 模块。
- [`shutil`]({{< ref "/library/filesys/shutil#module-shutil" >}}): [`rmtree()`]({{< ref "/library/filesys/shutil#shutil.rmtree" >}}) 的 *onerror* 形参在 Python 3.12 中已被弃用；请改用 *onexc* 形参。
- [`ssl`]({{< ref "/library/ipc/ssl#module-ssl" >}}) 选项和协议：
  - [`ssl.SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 不带 protocol 参数的做法已被弃用。
  - [`ssl.SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}): [`set_npn_protocols()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.set_npn_protocols" >}}) 和 `selected_npn_protocol()` 已被弃用：请改用 ALPN.
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
- [`sysconfig.is_python_build()`]({{< ref "/library/python/sysconfig#sysconfig.is_python_build" >}}) *check_home* 形参已被弃用并会被忽略。
- [`threading`]({{< ref "/library/concurrency/threading#module-threading" >}}) 的方法：
  - `threading.Condition.notifyAll()`: 使用 [`notify_all()`]({{< ref "/library/concurrency/threading#threading.Condition.notify_all" >}})。
  - `threading.Event.isSet()`: 使用 [`is_set()`]({{< ref "/library/concurrency/threading#threading.Event.is_set" >}})。
  - `threading.Thread.isDaemon()`, [`threading.Thread.setDaemon()`]({{< ref "/library/concurrency/threading#threading.Thread.setDaemon" >}}): 使用 [`threading.Thread.daemon`]({{< ref "/library/concurrency/threading#threading.Thread.daemon" >}}) 属性。
  - `threading.Thread.getName()`, [`threading.Thread.setName()`]({{< ref "/library/concurrency/threading#threading.Thread.setName" >}}): 使用 [`threading.Thread.name`]({{< ref "/library/concurrency/threading#threading.Thread.name" >}}) 属性。
  - `threading.currentThread()`: 使用 [`threading.current_thread()`]({{< ref "/library/concurrency/threading#threading.current_thread" >}})。
  - `threading.activeCount()`: 使用 [`threading.active_count()`]({{< ref "/library/concurrency/threading#threading.active_count" >}})。
- [`typing.Text`]({{< ref "/library/development/typing#typing.Text" >}}) ([gh-92332](https://github.com/python/cpython/issues/92332))。
- [`unittest.IsolatedAsyncioTestCase`]({{< ref "/library/development/unittest#unittest.IsolatedAsyncioTestCase" >}}): 从测试用例返回不为 `None` 的值的做法已被弃用。
- [`urllib.parse`]({{< ref "/library/internet/urllib/urllib_parse#module-urllib.parse" >}}) 函数已被弃用：改用 [`urlparse()`]({{< ref "/library/internet/urllib/urllib_parse#urllib.parse.urlparse" >}})
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
- [`urllib.request`]({{< ref "/library/internet/urllib/urllib_request#module-urllib.request" >}}): 发起请求的 [`URLopener`]({{< ref "/library/internet/urllib/urllib_request#urllib.request.URLopener" >}}) 和 [`FancyURLopener`]({{< ref "/library/internet/urllib/urllib_request#urllib.request.FancyURLopener" >}}) 方式已被弃用。 改用更新 [`urlopen()`]({{< ref "/library/internet/urllib/urllib_request#urllib.request.urlopen" >}}) 函数和方法。
- [`wsgiref`]({{< ref "/library/internet/wsgiref#module-wsgiref" >}}): `SimpleHandler.stdout.write()` 不应执行部分写入。
- [`xml.etree.ElementTree`]({{< ref "/library/markup/xml_etree_elementtree#module-xml.etree.ElementTree" >}}): 对 [`Element`]({{< ref "/library/markup/xml_etree_elementtree#xml.etree.ElementTree.Element" >}}) 的真值测试已被弃用。 在未来的发布版中它将始终返回 `True`。 建议改用显式的 `len(elem)` 或 `elem is not None` 测试。
- [`zipimport.zipimporter.load_module()`]({{< ref "/library/modules/zipimport#zipimport.zipimporter.load_module" >}}) 已被弃用：请改用 [`exec_module()`]({{< ref "/library/modules/zipimport#zipimport.zipimporter.exec_module" >}})。

## CPython 字节码的变化

- The oparg of [`YIELD_VALUE`]({{< ref "/library/language/dis#opcode-YIELD_VALUE" >}}) is now `1` if the yield is part of a yield-from or await, and `0` otherwise. The oparg of [`RESUME`]({{< ref "/library/language/dis#opcode-RESUME" >}}) was changed to add a bit indicating if the except-depth is 1, which is needed to optimize closing of generators. (Contributed by Irit Katriel in [gh-111354](https://github.com/python/cpython/issues/111354).)

## C API 的变化

### 新的特性

- 增加 [PyMonitoring C API]({{< ref "/c_api/monitoring#c-api-monitoring" >}}) 用于生成 [**PEP 669**](https://peps.python.org/pep-0669/) 监控事件：

  - [`PyMonitoringState`]({{< ref "/c_api/monitoring#c.PyMonitoringState" >}})
  - [`PyMonitoring_FirePyStartEvent()`]({{< ref "/c_api/monitoring#c.PyMonitoring_FirePyStartEvent" >}})
  - [`PyMonitoring_FirePyResumeEvent()`]({{< ref "/c_api/monitoring#c.PyMonitoring_FirePyResumeEvent" >}})
  - [`PyMonitoring_FirePyReturnEvent()`]({{< ref "/c_api/monitoring#c.PyMonitoring_FirePyReturnEvent" >}})
  - [`PyMonitoring_FirePyYieldEvent()`]({{< ref "/c_api/monitoring#c.PyMonitoring_FirePyYieldEvent" >}})
  - [`PyMonitoring_FireCallEvent()`]({{< ref "/c_api/monitoring#c.PyMonitoring_FireCallEvent" >}})
  - [`PyMonitoring_FireLineEvent()`]({{< ref "/c_api/monitoring#c.PyMonitoring_FireLineEvent" >}})
  - [`PyMonitoring_FireJumpEvent()`]({{< ref "/c_api/monitoring#c.PyMonitoring_FireJumpEvent" >}})
  - [`PyMonitoring_FireBranchEvent()`]({{< ref "/c_api/monitoring#c.PyMonitoring_FireBranchEvent" >}})
  - [`PyMonitoring_FireCReturnEvent()`]({{< ref "/c_api/monitoring#c.PyMonitoring_FireCReturnEvent" >}})
  - [`PyMonitoring_FirePyThrowEvent()`]({{< ref "/c_api/monitoring#c.PyMonitoring_FirePyThrowEvent" >}})
  - [`PyMonitoring_FireRaiseEvent()`]({{< ref "/c_api/monitoring#c.PyMonitoring_FireRaiseEvent" >}})
  - [`PyMonitoring_FireCRaiseEvent()`]({{< ref "/c_api/monitoring#c.PyMonitoring_FireCRaiseEvent" >}})
  - [`PyMonitoring_FireReraiseEvent()`]({{< ref "/c_api/monitoring#c.PyMonitoring_FireReraiseEvent" >}})
  - [`PyMonitoring_FireExceptionHandledEvent()`]({{< ref "/c_api/monitoring#c.PyMonitoring_FireExceptionHandledEvent" >}})
  - [`PyMonitoring_FirePyUnwindEvent()`]({{< ref "/c_api/monitoring#c.PyMonitoring_FirePyUnwindEvent" >}})
  - [`PyMonitoring_FireStopIterationEvent()`]({{< ref "/c_api/monitoring#c.PyMonitoring_FireStopIterationEvent" >}})
  - [`PyMonitoring_EnterScope()`]({{< ref "/c_api/monitoring#c.PyMonitoring_EnterScope" >}})
  - [`PyMonitoring_ExitScope()`]({{< ref "/c_api/monitoring#c.PyMonitoring_ExitScope" >}})

  （由 Irit Katriel 在 [gh-111997](https://github.com/python/cpython/issues/111997) 中贡献。）

- 增加了 [`PyMutex`]({{< ref "/c_api/init#c.PyMutex" >}})，它是占用一个字节的轻量级互斥锁，以及新的 [`PyMutex_Lock()`]({{< ref "/c_api/init#c.PyMutex_Lock" >}}) 和 [`PyMutex_Unlock()`]({{< ref "/c_api/init#c.PyMutex_Unlock" >}}) 函数。 如果操作需要阻塞则 `PyMutex_Lock()` 将释放 (当前持有的) [GIL]({{< ref "/glossary/idx#term-GIL" >}})。 （由 Sam Gross 在 [gh-108724](https://github.com/python/cpython/issues/108724) 中贡献。）

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

- Add the [`PyDict_GetItemRef()`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDict_GetItemRef) and [`PyDict_GetItemStringRef()`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDict_GetItemStringRef) functions, which behave similarly to [`PyDict_GetItemWithError()`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDict_GetItemWithError), but return a [strong reference]({{< ref "/glossary/idx#term-strong-reference" >}}) instead of a [borrowed reference]({{< ref "/glossary/idx#term-borrowed-reference" >}}). Moreover, these functions return `-1` on error, removing the need to check `PyErr_Occurred()`. (Contributed by Victor Stinner in [gh-106004](https://github.com/python/cpython/issues/106004).)

- Add the [`PyDict_SetDefaultRef()`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDict_SetDefaultRef) function, which behaves similarly to [`PyDict_SetDefault()`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDict_SetDefault), but returns a [strong reference]({{< ref "/glossary/idx#term-strong-reference" >}}) instead of a [borrowed reference]({{< ref "/glossary/idx#term-borrowed-reference" >}}). This function returns `-1` on error, `0` on insertion, and `1` if the key was already present in the dictionary. (Contributed by Sam Gross in [gh-112066](https://github.com/python/cpython/issues/112066).)

- Add the [`PyDict_Pop()`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDict_Pop) and [`PyDict_PopString()`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDict_PopString) functions to remove a key from a dictionary and optionally return the removed value. This is similar to [`dict.pop()`]({{< ref "/library/stdtypes#dict.pop" >}}), though there is no default value, and [`KeyError`]({{< ref "/library/exceptions#KeyError" >}}) is not raised for missing keys. (Contributed by Stefan Behnel and Victor Stinner in [gh-111262](https://github.com/python/cpython/issues/111262).)

- Add the [`PyMapping_GetOptionalItem()`](https://docs.python.org/zh-cn/3.13/c-api/mapping.html#c.PyMapping_GetOptionalItem) and [`PyMapping_GetOptionalItemString()`](https://docs.python.org/zh-cn/3.13/c-api/mapping.html#c.PyMapping_GetOptionalItemString) functions as alternatives to [`PyObject_GetItem()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_GetItem) and [`PyMapping_GetItemString()`](https://docs.python.org/zh-cn/3.13/c-api/mapping.html#c.PyMapping_GetItemString) respectively. The new functions do not raise [`KeyError`]({{< ref "/library/exceptions#KeyError" >}}) if the requested key is missing from the mapping. These variants are more convenient and faster if a missing key should not be treated as a failure. (Contributed by Serhiy Storchaka in [gh-106307](https://github.com/python/cpython/issues/106307).)

- Add the [`PyObject_GetOptionalAttr()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_GetOptionalAttr) and [`PyObject_GetOptionalAttrString()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_GetOptionalAttrString) functions as alternatives to [`PyObject_GetAttr()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_GetAttr) and [`PyObject_GetAttrString()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_GetAttrString) respectively. The new functions do not raise [`AttributeError`]({{< ref "/library/exceptions#AttributeError" >}}) if the requested attribute is not found on the object. These variants are more convenient and faster if the missing attribute should not be treated as a failure. (Contributed by Serhiy Storchaka in [gh-106521](https://github.com/python/cpython/issues/106521).)

- Add the [`PyErr_FormatUnraisable()`]({{< ref "/c_api/exceptions#c.PyErr_FormatUnraisable" >}}) function as an extension to [`PyErr_WriteUnraisable()`]({{< ref "/c_api/exceptions#c.PyErr_WriteUnraisable" >}}) that allows customizing the warning message. (Contributed by Serhiy Storchaka in [gh-108082](https://github.com/python/cpython/issues/108082).)

- Add new functions that return a [strong reference]({{< ref "/glossary/idx#term-strong-reference" >}}) instead of a [borrowed reference]({{< ref "/glossary/idx#term-borrowed-reference" >}}) for frame locals, globals, and builtins, as part of [PEP 667]({{< ref "/whatsnew/idx#whatsnew313-locals-semantics" >}}):

  - [`PyEval_GetFrameBuiltins()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetFrameBuiltins) replaces [`PyEval_GetBuiltins()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetBuiltins)
  - [`PyEval_GetFrameGlobals()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetFrameGlobals) replaces [`PyEval_GetGlobals()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetGlobals)
  - [`PyEval_GetFrameLocals()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetFrameLocals) replaces [`PyEval_GetLocals()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetLocals)

  (Contributed by Mark Shannon and Tian Gao in [gh-74929](https://github.com/python/cpython/issues/74929).)

- Add the [`Py_GetConstant()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.Py_GetConstant) and [`Py_GetConstantBorrowed()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.Py_GetConstantBorrowed) functions to get [strong]({{< ref "/glossary/idx#term-strong-reference" >}}) or [borrowed]({{< ref "/glossary/idx#term-borrowed-reference" >}}) references to constants. For example, `Py_GetConstant(Py_CONSTANT_ZERO)` returns a strong reference to the constant zero. (Contributed by Victor Stinner in [gh-115754](https://github.com/python/cpython/issues/115754).)

- Add the [`PyImport_AddModuleRef()`](https://docs.python.org/zh-cn/3.13/c-api/import.html#c.PyImport_AddModuleRef) function as a replacement for [`PyImport_AddModule()`](https://docs.python.org/zh-cn/3.13/c-api/import.html#c.PyImport_AddModule) that returns a [strong reference]({{< ref "/glossary/idx#term-strong-reference" >}}) instead of a [borrowed reference]({{< ref "/glossary/idx#term-borrowed-reference" >}}). (Contributed by Victor Stinner in [gh-105922](https://github.com/python/cpython/issues/105922).)

- Add the [`Py_IsFinalizing()`]({{< ref "/c_api/init#c.Py_IsFinalizing" >}}) function to check whether the main Python interpreter is [shutting down]({{< ref "/glossary/idx#term-interpreter-shutdown" >}}). (Contributed by Victor Stinner in [gh-108014](https://github.com/python/cpython/issues/108014).)

- Add the [`PyList_GetItemRef()`](https://docs.python.org/zh-cn/3.13/c-api/list.html#c.PyList_GetItemRef) function as a replacement for [`PyList_GetItem()`](https://docs.python.org/zh-cn/3.13/c-api/list.html#c.PyList_GetItem) that returns a [strong reference]({{< ref "/glossary/idx#term-strong-reference" >}}) instead of a [borrowed reference]({{< ref "/glossary/idx#term-borrowed-reference" >}}). (Contributed by Sam Gross in [gh-114329](https://github.com/python/cpython/issues/114329).)

- Add the [`PyList_Extend()`](https://docs.python.org/zh-cn/3.13/c-api/list.html#c.PyList_Extend) and [`PyList_Clear()`](https://docs.python.org/zh-cn/3.13/c-api/list.html#c.PyList_Clear) functions, mirroring the Python `list.extend()` and `list.clear()` methods. (Contributed by Victor Stinner in [gh-111138](https://github.com/python/cpython/issues/111138).)

- Add the [`PyLong_AsInt()`](https://docs.python.org/zh-cn/3.13/c-api/long.html#c.PyLong_AsInt) function. It behaves similarly to [`PyLong_AsLong()`](https://docs.python.org/zh-cn/3.13/c-api/long.html#c.PyLong_AsLong), but stores the result in a C int instead of a C long. (Contributed by Victor Stinner in [gh-108014](https://github.com/python/cpython/issues/108014).)

- Add the [`PyLong_AsNativeBytes()`](https://docs.python.org/zh-cn/3.13/c-api/long.html#c.PyLong_AsNativeBytes), [`PyLong_FromNativeBytes()`](https://docs.python.org/zh-cn/3.13/c-api/long.html#c.PyLong_FromNativeBytes), and [`PyLong_FromUnsignedNativeBytes()`](https://docs.python.org/zh-cn/3.13/c-api/long.html#c.PyLong_FromUnsignedNativeBytes) functions to simplify converting between native integer types and Python [`int`]({{< ref "/library/functions#int" >}}) objects. (Contributed by Steve Dower in [gh-111140](https://github.com/python/cpython/issues/111140).)

- Add [`PyModule_Add()`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.PyModule_Add) function, which is similar to [`PyModule_AddObjectRef()`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.PyModule_AddObjectRef) and [`PyModule_AddObject()`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.PyModule_AddObject), but always steals a reference to the value. (Contributed by Serhiy Storchaka in [gh-86493](https://github.com/python/cpython/issues/86493).)

- Add the [`PyObject_GenericHash()`](https://docs.python.org/zh-cn/3.13/c-api/hash.html#c.PyObject_GenericHash) function that implements the default hashing function of a Python object. (Contributed by Serhiy Storchaka in [gh-113024](https://github.com/python/cpython/issues/113024).)

- Add the [`Py_HashPointer()`](https://docs.python.org/zh-cn/3.13/c-api/hash.html#c.Py_HashPointer) function to hash a raw pointer. (Contributed by Victor Stinner in [gh-111545](https://github.com/python/cpython/issues/111545).)

- Add the [`PyObject_VisitManagedDict()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_VisitManagedDict) and [`PyObject_ClearManagedDict()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_ClearManagedDict) functions. which must be called by the traverse and clear functions of a type using the [`Py_TPFLAGS_MANAGED_DICT`](https://docs.python.org/zh-cn/3.13/c-api/typeobj.html#c.Py_TPFLAGS_MANAGED_DICT) flag. The [pythoncapi-compat project](https://github.com/python/pythoncapi-compat/) can be used to use these functions with Python 3.11 and 3.12. (Contributed by Victor Stinner in [gh-107073](https://github.com/python/cpython/issues/107073).)

- Add the [`PyRefTracer_SetTracer()`]({{< ref "/c_api/init#c.PyRefTracer_SetTracer" >}}) and [`PyRefTracer_GetTracer()`]({{< ref "/c_api/init#c.PyRefTracer_GetTracer" >}}) functions, which enable tracking object creation and destruction in the same way that the [`tracemalloc`]({{< ref "/library/debug/tracemalloc#module-tracemalloc" >}}) module does. (Contributed by Pablo Galindo in [gh-93502](https://github.com/python/cpython/issues/93502).)

- Add the [`PySys_AuditTuple()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PySys_AuditTuple) function as an alternative to [`PySys_Audit()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PySys_Audit) that takes event arguments as a Python [`tuple`]({{< ref "/library/stdtypes#tuple" >}}) object. (Contributed by Victor Stinner in [gh-85283](https://github.com/python/cpython/issues/85283).)

- Add the [`PyThreadState_GetUnchecked()`]({{< ref "/c_api/init#c.PyThreadState_GetUnchecked" >}}) function as an alternative to [`PyThreadState_Get()`]({{< ref "/c_api/init#c.PyThreadState_Get" >}}) that doesn't kill the process with a fatal error if it is `NULL`. The caller is responsible for checking if the result is `NULL`. (Contributed by Victor Stinner in [gh-108867](https://github.com/python/cpython/issues/108867).)

- Add the [`PyType_GetFullyQualifiedName()`](https://docs.python.org/zh-cn/3.13/c-api/type.html#c.PyType_GetFullyQualifiedName) function to get the type's fully qualified name. The module name is prepended if [`type.__module__`]({{< ref "/reference/datamodel#type.__module__" >}}) is a string and is not equal to either `'builtins'` or `'__main__'`. (Contributed by Victor Stinner in [gh-111696](https://github.com/python/cpython/issues/111696).)

- Add the [`PyType_GetModuleName()`](https://docs.python.org/zh-cn/3.13/c-api/type.html#c.PyType_GetModuleName) function to get the type's module name. This is equivalent to getting the [`type.__module__`]({{< ref "/reference/datamodel#type.__module__" >}}) attribute. (Contributed by Eric Snow and Victor Stinner in [gh-111696](https://github.com/python/cpython/issues/111696).)

- Add the [`PyUnicode_EqualToUTF8AndSize()`](https://docs.python.org/zh-cn/3.13/c-api/unicode.html#c.PyUnicode_EqualToUTF8AndSize) and [`PyUnicode_EqualToUTF8()`](https://docs.python.org/zh-cn/3.13/c-api/unicode.html#c.PyUnicode_EqualToUTF8) functions to compare a Unicode object with a const char* UTF-8 encoded string and `1` if they are equal or `0` otherwise. These functions do not raise exceptions. (Contributed by Serhiy Storchaka in [gh-110289](https://github.com/python/cpython/issues/110289).)

- 增加 [`PyWeakref_GetRef()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GetRef) 函数作为 [`PyWeakref_GetObject()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GetObject) 的替代，它将返回一个 [strong reference]({{< ref "/glossary/idx#term-strong-reference" >}}) 或是在引用对象不再存活时返回 `NULL`。 （由 Victor Stinner 在 [gh-105927](https://github.com/python/cpython/issues/105927) 中贡献。）

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

- The [`PyDict_GetItem()`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDict_GetItem), [`PyDict_GetItemString()`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDict_GetItemString), [`PyMapping_HasKey()`](https://docs.python.org/zh-cn/3.13/c-api/mapping.html#c.PyMapping_HasKey), [`PyMapping_HasKeyString()`](https://docs.python.org/zh-cn/3.13/c-api/mapping.html#c.PyMapping_HasKeyString), [`PyObject_HasAttr()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_HasAttr), [`PyObject_HasAttrString()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_HasAttrString), and [`PySys_GetObject()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PySys_GetObject) functions, each of which clears all errors which occurred when calling them now reports these errors using [`sys.unraisablehook()`]({{< ref "/library/python/sys#sys.unraisablehook" >}}). You may replace them with other functions as recommended in the documentation. (Contributed by Serhiy Storchaka in [gh-106672](https://github.com/python/cpython/issues/106672).)

- Add support for the `%T`, `%#T`, `%N` and `%#N` formats to [`PyUnicode_FromFormat()`](https://docs.python.org/zh-cn/3.13/c-api/unicode.html#c.PyUnicode_FromFormat):

  - `%T`: Get the fully qualified name of an object type
  - `%#T`: As above, but use a colon as the separator
  - `%N`: Get the fully qualified name of a type
  - `%#N`: As above, but use a colon as the separator

  See [**PEP 737**](https://peps.python.org/pep-0737/) for more information. (Contributed by Victor Stinner in [gh-111696](https://github.com/python/cpython/issues/111696).)

- You no longer have to define the `PY_SSIZE_T_CLEAN` macro before including `Python.h` when using `#` formats in [format codes](https://docs.python.org/zh-cn/3.13/c-api/arg.html#arg-parsing-string-and-buffers). APIs accepting the format codes always use `Py_ssize_t` for `#` formats. (Contributed by Inada Naoki in [gh-104922](https://github.com/python/cpython/issues/104922).)

- 如果 Python 是使用 [调试模式]({{< ref "/using/configure#debug-build" >}}) 或 [`附带断言`]({{< ref "/using/configure#cmdoption-with-assertions" >}}) 构建的，[`PyTuple_SET_ITEM()`](https://docs.python.org/zh-cn/3.13/c-api/tuple.html#c.PyTuple_SET_ITEM) 和 [`PyList_SET_ITEM()`](https://docs.python.org/zh-cn/3.13/c-api/list.html#c.PyList_SET_ITEM) 现在将通过一个断言来检查 index 参数。 （由 Victor Stinner 在 [gh-106168](https://github.com/python/cpython/issues/106168) 中贡献。）

### 受限 C API 的改变

- 下列函数现在被包括在受限 C API 中：

  - [`PyMem_RawMalloc()`]({{< ref "/c_api/memory#c.PyMem_RawMalloc" >}})
  - [`PyMem_RawCalloc()`]({{< ref "/c_api/memory#c.PyMem_RawCalloc" >}})
  - [`PyMem_RawRealloc()`]({{< ref "/c_api/memory#c.PyMem_RawRealloc" >}})
  - [`PyMem_RawFree()`]({{< ref "/c_api/memory#c.PyMem_RawFree" >}})
  - [`PySys_Audit()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PySys_Audit)
  - [`PySys_AuditTuple()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PySys_AuditTuple)
  - [`PyType_GetModuleByDef()`](https://docs.python.org/zh-cn/3.13/c-api/type.html#c.PyType_GetModuleByDef)

  （由 Victor Stinner 在 [gh-85283](https://github.com/python/cpython/issues/85283), [gh-85283](https://github.com/python/cpython/issues/85283) 和 [gh-116936](https://github.com/python/cpython/issues/116936) 中贡献。）

- 使用 [`--with-trace-refs`]({{< ref "/using/configure#cmdoption-with-trace-refs" >}}) (跟踪引用) 构建的 Python 现在支持 [受限 API]({{< ref "/c_api/stable#limited-c-api" >}})。 （由 Victor Stinner 在 [gh-108634](https://github.com/python/cpython/issues/108634) 中贡献。）

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

     

    ​	In [`PyObject_Call()`](https://docs.python.org/zh-cn/3.13/c-api/call.html#c.PyObject_Call), positional arguments must be a [`tuple`]({{< ref "/library/stdtypes#tuple" >}}) and must not be `NULL`, and keyword arguments must be a [`dict`]({{< ref "/library/stdtypes#dict" >}}) or `NULL`, whereas the removed functions checked argument types and accepted `NULL` positional and keyword arguments. To replace `PyEval_CallObjectWithKeywords(func, NULL, kwargs)` with [`PyObject_Call()`](https://docs.python.org/zh-cn/3.13/c-api/call.html#c.PyObject_Call), pass an empty tuple as positional arguments using [`PyTuple_New(0)`](https://docs.python.org/zh-cn/3.13/c-api/tuple.html#c.PyTuple_New).

  - `PyEval_CallFunction()`: Use [`PyObject_CallFunction()`](https://docs.python.org/zh-cn/3.13/c-api/call.html#c.PyObject_CallFunction) instead.

  - `PyEval_CallMethod()`: Use [`PyObject_CallMethod()`](https://docs.python.org/zh-cn/3.13/c-api/call.html#c.PyObject_CallMethod) instead.

  - `PyCFunction_Call()`: Use [`PyObject_Call()`](https://docs.python.org/zh-cn/3.13/c-api/call.html#c.PyObject_Call) instead.

  (Contributed by Victor Stinner in [gh-105107](https://github.com/python/cpython/issues/105107).)

- Remove the following old functions to configure the Python initialization, deprecated in Python 3.11:

  - `PySys_AddWarnOptionUnicode()`: Use [`PyConfig.warnoptions`]({{< ref "/c_api/init_config#c.PyConfig.warnoptions" >}}) instead.
  - `PySys_AddWarnOption()`: Use [`PyConfig.warnoptions`]({{< ref "/c_api/init_config#c.PyConfig.warnoptions" >}}) instead.
  - `PySys_AddXOption()`: Use [`PyConfig.xoptions`]({{< ref "/c_api/init_config#c.PyConfig.xoptions" >}}) instead.
  - `PySys_HasWarnOptions()`: Use [`PyConfig.xoptions`]({{< ref "/c_api/init_config#c.PyConfig.xoptions" >}}) instead.
  - `PySys_SetPath()`: Set [`PyConfig.module_search_paths`]({{< ref "/c_api/init_config#c.PyConfig.module_search_paths" >}}) instead.
  - `Py_SetPath()`: Set [`PyConfig.module_search_paths`]({{< ref "/c_api/init_config#c.PyConfig.module_search_paths" >}}) instead.
  - `Py_SetStandardStreamEncoding()`: Set [`PyConfig.stdio_encoding`]({{< ref "/c_api/init_config#c.PyConfig.stdio_encoding" >}}) instead, and set also maybe [`PyConfig.legacy_windows_stdio`]({{< ref "/c_api/init_config#c.PyConfig.legacy_windows_stdio" >}}) (on Windows).
  - `_Py_SetProgramFullPath()`: Set [`PyConfig.executable`]({{< ref "/c_api/init_config#c.PyConfig.executable" >}}) instead.

  Use the new [`PyConfig`]({{< ref "/c_api/init_config#c.PyConfig" >}}) API of the [Python Initialization Configuration]({{< ref "/c_api/init_config#init-config" >}}) instead ([**PEP 587**](https://peps.python.org/pep-0587/)), added to Python 3.8. (Contributed by Victor Stinner in [gh-105145](https://github.com/python/cpython/issues/105145).)

- Remove `PyEval_AcquireLock()` and `PyEval_ReleaseLock()` functions, deprecated in Python 3.2. They didn't update the current thread state. They can be replaced with:

  - [`PyEval_SaveThread()`]({{< ref "/c_api/init#c.PyEval_SaveThread" >}}) and [`PyEval_RestoreThread()`]({{< ref "/c_api/init#c.PyEval_RestoreThread" >}});
  - low-level [`PyEval_AcquireThread()`]({{< ref "/c_api/init#c.PyEval_AcquireThread" >}}) and [`PyEval_RestoreThread()`]({{< ref "/c_api/init#c.PyEval_RestoreThread" >}});
  - or [`PyGILState_Ensure()`]({{< ref "/c_api/init#c.PyGILState_Ensure" >}}) and [`PyGILState_Release()`]({{< ref "/c_api/init#c.PyGILState_Release" >}}).

  (Contributed by Victor Stinner in [gh-105182](https://github.com/python/cpython/issues/105182).)

- 删除了在 Python 3.9 弃用的:c:func:!PyEval_ThreadsInitialized 函数。自Python 3.7 起，`Py_Initialize()` 总是创建全局解释器锁 ：调用:c:func:!PyEval_InitThreads 不做任何事情，而:c:func:!PyEval_ThreadsInitialized 总返回非零值。(由 Victor Stinner 在:gh:105182 中贡献。）

- Remove the `_PyInterpreterState_Get()` alias to [`PyInterpreterState_Get()`]({{< ref "/c_api/init#c.PyInterpreterState_Get" >}}) which was kept for backward compatibility with Python 3.8. The [pythoncapi-compat project](https://github.com/python/pythoncapi-compat/) can be used to get [`PyInterpreterState_Get()`]({{< ref "/c_api/init#c.PyInterpreterState_Get" >}}) on Python 3.8 and older. (Contributed by Victor Stinner in [gh-106320](https://github.com/python/cpython/issues/106320).)

- Remove the private `_PyObject_FastCall()` function: use `PyObject_Vectorcall()` which is available since Python 3.8 ([**PEP 590**](https://peps.python.org/pep-0590/)). (Contributed by Victor Stinner in [gh-106023](https://github.com/python/cpython/issues/106023).)

- Remove the `cpython/pytime.h` header file, which only contained private functions. (Contributed by Victor Stinner in [gh-106316](https://github.com/python/cpython/issues/106316).)

- Remove the undocumented `PY_TIMEOUT_MAX` constant from the limited C API. (Contributed by Victor Stinner in [gh-110014](https://github.com/python/cpython/issues/110014).)

- Remove the old trashcan macros `Py_TRASHCAN_SAFE_BEGIN` and `Py_TRASHCAN_SAFE_END`. Replace both with the new macros `Py_TRASHCAN_BEGIN` and `Py_TRASHCAN_END`. (Contributed by Irit Katriel in [gh-105111](https://github.com/python/cpython/issues/105111).)

### 已弃用的 C API

- 已弃用旧的 Python 初始化函数：

  - [`PySys_ResetWarnOptions()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PySys_ResetWarnOptions): 改为清除 [`sys.warnoptions`]({{< ref "/library/python/sys#sys.warnoptions" >}}) 和 `warnings.filters`。
  - [`Py_GetExecPrefix()`]({{< ref "/c_api/init#c.Py_GetExecPrefix" >}}): 改为获取 [`sys.exec_prefix`]({{< ref "/library/python/sys#sys.exec_prefix" >}})。
  - [`Py_GetPath()`]({{< ref "/c_api/init#c.Py_GetPath" >}}): 改为获取 [`sys.path`]({{< ref "/library/python/sys#sys.path" >}})。
  - [`Py_GetPrefix()`]({{< ref "/c_api/init#c.Py_GetPrefix" >}}): 改为获取 [`sys.prefix`]({{< ref "/library/python/sys#sys.prefix" >}})。
  - [`Py_GetProgramFullPath()`]({{< ref "/c_api/init#c.Py_GetProgramFullPath" >}}): 改为获取 [`sys.executable`]({{< ref "/library/python/sys#sys.executable" >}})。
  - [`Py_GetProgramName()`]({{< ref "/c_api/init#c.Py_GetProgramName" >}}): 改为获取 [`sys.executable`]({{< ref "/library/python/sys#sys.executable" >}})。
  - [`Py_GetPythonHome()`]({{< ref "/c_api/init#c.Py_GetPythonHome" >}}): 改为获取 [`PyConfig.home`]({{< ref "/c_api/init_config#c.PyConfig.home" >}}) 或 [`PYTHONHOME`]({{< ref "/using/cmdline#envvar-PYTHONHOME" >}}) 环境变量。

  （由 Victor Stinner 在 [gh-105145](https://github.com/python/cpython/issues/105145) 中贡献。）

- [Soft deprecate]({{< ref "/glossary/idx#term-soft-deprecated" >}}) the [`PyEval_GetBuiltins()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetBuiltins), [`PyEval_GetGlobals()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetGlobals), and [`PyEval_GetLocals()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetLocals) functions, which return a [borrowed reference]({{< ref "/glossary/idx#term-borrowed-reference" >}}). (Soft deprecated as part of [**PEP 667**](https://peps.python.org/pep-0667/).)

- Deprecate the [`PyImport_ImportModuleNoBlock()`](https://docs.python.org/zh-cn/3.13/c-api/import.html#c.PyImport_ImportModuleNoBlock) function, which is just an alias to [`PyImport_ImportModule()`](https://docs.python.org/zh-cn/3.13/c-api/import.html#c.PyImport_ImportModule) since Python 3.3. (Contributed by Victor Stinner in [gh-105396](https://github.com/python/cpython/issues/105396).)

- [Soft deprecate]({{< ref "/glossary/idx#term-soft-deprecated" >}}) the [`PyModule_AddObject()`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.PyModule_AddObject) function. It should be replaced with [`PyModule_Add()`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.PyModule_Add) or [`PyModule_AddObjectRef()`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.PyModule_AddObjectRef). (Contributed by Serhiy Storchaka in [gh-86493](https://github.com/python/cpython/issues/86493).)

- Deprecate the old `Py_UNICODE` and `PY_UNICODE_TYPE` types and the `Py_UNICODE_WIDE` define. Use the `wchar_t` type directly instead. Since Python 3.3, `Py_UNICODE` and `PY_UNICODE_TYPE` are just aliases to `wchar_t`. (Contributed by Victor Stinner in [gh-105156](https://github.com/python/cpython/issues/105156).)

- Deprecate the [`PyWeakref_GetObject()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GetObject) and [`PyWeakref_GET_OBJECT()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GET_OBJECT) functions, which return a [borrowed reference]({{< ref "/glossary/idx#term-borrowed-reference" >}}). Replace them with the new [`PyWeakref_GetRef()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GetRef) function, which returns a [strong reference]({{< ref "/glossary/idx#term-strong-reference" >}}). The [pythoncapi-compat project](https://github.com/python/pythoncapi-compat/) can be used to get [`PyWeakref_GetRef()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GetRef) on Python 3.12 and older. (Contributed by Victor Stinner in [gh-105927](https://github.com/python/cpython/issues/105927).)

#### 计划在 Python 3.14 中移除

- [`PyDictObject`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDictObject) 中的 `ma_version_tag` 字段用于扩展模块 ( [**PEP 699**](https://peps.python.org/pep-0699/) ; [gh-101193](https://github.com/python/cpython/issues/101193) )。

- 创建 [`immutable types`](https://docs.python.org/zh-cn/3.13/c-api/typeobj.html#c.Py_TPFLAGS_IMMUTABLETYPE) 的可变基础 ( [gh-95388](https://github.com/python/cpython/issues/95388) )。

- 用于配置 Python 的初始化的函数，在 Python 3.11 中已弃用：

  - `PySys_SetArgvEx()`: 改为设置 [`PyConfig.argv`]({{< ref "/c_api/init_config#c.PyConfig.argv" >}})。
  - `PySys_SetArgv()`: 改为设置 [`PyConfig.argv`]({{< ref "/c_api/init_config#c.PyConfig.argv" >}})。
  - `Py_SetProgramName()`: 改为设置 [`PyConfig.program_name`]({{< ref "/c_api/init_config#c.PyConfig.program_name" >}})。
  - `Py_SetPythonHome()`: 改为设置 [`PyConfig.home`]({{< ref "/c_api/init_config#c.PyConfig.home" >}})。

  [`Py_InitializeFromConfig()`]({{< ref "/c_api/init#c.Py_InitializeFromConfig" >}}) API 应与 [`PyConfig`]({{< ref "/c_api/init_config#c.PyConfig" >}}) 一起使用。

- 全局配置变量：

  - [`Py_DebugFlag`]({{< ref "/c_api/init#c.Py_DebugFlag" >}}): 改用 [`PyConfig.parser_debug`]({{< ref "/c_api/init_config#c.PyConfig.parser_debug" >}})。
  - [`Py_VerboseFlag`]({{< ref "/c_api/init#c.Py_VerboseFlag" >}}): 改用 [`PyConfig.verbose`]({{< ref "/c_api/init_config#c.PyConfig.verbose" >}})。
  - [`Py_QuietFlag`]({{< ref "/c_api/init#c.Py_QuietFlag" >}}): 改用 [`PyConfig.quiet`]({{< ref "/c_api/init_config#c.PyConfig.quiet" >}})。
  - [`Py_InteractiveFlag`]({{< ref "/c_api/init#c.Py_InteractiveFlag" >}}): 改用 [`PyConfig.interactive`]({{< ref "/c_api/init_config#c.PyConfig.interactive" >}})。
  - [`Py_InspectFlag`]({{< ref "/c_api/init#c.Py_InspectFlag" >}}): 改用 [`PyConfig.inspect`]({{< ref "/c_api/init_config#c.PyConfig.inspect" >}})。
  - [`Py_OptimizeFlag`]({{< ref "/c_api/init#c.Py_OptimizeFlag" >}}): 改用 [`PyConfig.optimization_level`]({{< ref "/c_api/init_config#c.PyConfig.optimization_level" >}})。
  - [`Py_NoSiteFlag`]({{< ref "/c_api/init#c.Py_NoSiteFlag" >}}): 改用 [`PyConfig.site_import`]({{< ref "/c_api/init_config#c.PyConfig.site_import" >}})。
  - [`Py_BytesWarningFlag`]({{< ref "/c_api/init#c.Py_BytesWarningFlag" >}}): 改用 [`PyConfig.bytes_warning`]({{< ref "/c_api/init_config#c.PyConfig.bytes_warning" >}})。
  - [`Py_FrozenFlag`]({{< ref "/c_api/init#c.Py_FrozenFlag" >}}): 改用 [`PyConfig.pathconfig_warnings`]({{< ref "/c_api/init_config#c.PyConfig.pathconfig_warnings" >}})。
  - [`Py_IgnoreEnvironmentFlag`]({{< ref "/c_api/init#c.Py_IgnoreEnvironmentFlag" >}}): 改用 [`PyConfig.use_environment`]({{< ref "/c_api/init_config#c.PyConfig.use_environment" >}})。
  - [`Py_DontWriteBytecodeFlag`]({{< ref "/c_api/init#c.Py_DontWriteBytecodeFlag" >}}): 改用 [`PyConfig.write_bytecode`]({{< ref "/c_api/init_config#c.PyConfig.write_bytecode" >}})。
  - [`Py_NoUserSiteDirectory`]({{< ref "/c_api/init#c.Py_NoUserSiteDirectory" >}}): 改用 [`PyConfig.user_site_directory`]({{< ref "/c_api/init_config#c.PyConfig.user_site_directory" >}})。
  - [`Py_UnbufferedStdioFlag`]({{< ref "/c_api/init#c.Py_UnbufferedStdioFlag" >}}): 改用 [`PyConfig.buffered_stdio`]({{< ref "/c_api/init_config#c.PyConfig.buffered_stdio" >}})。
  - [`Py_HashRandomizationFlag`]({{< ref "/c_api/init#c.Py_HashRandomizationFlag" >}}): 改用 [`PyConfig.use_hash_seed`]({{< ref "/c_api/init_config#c.PyConfig.use_hash_seed" >}}) 和 [`PyConfig.hash_seed`]({{< ref "/c_api/init_config#c.PyConfig.hash_seed" >}})。
  - [`Py_IsolatedFlag`]({{< ref "/c_api/init#c.Py_IsolatedFlag" >}}): 改用 [`PyConfig.isolated`]({{< ref "/c_api/init_config#c.PyConfig.isolated" >}})。
  - [`Py_LegacyWindowsFSEncodingFlag`]({{< ref "/c_api/init#c.Py_LegacyWindowsFSEncodingFlag" >}}): 改用 [`PyPreConfig.legacy_windows_fs_encoding`]({{< ref "/c_api/init_config#c.PyPreConfig.legacy_windows_fs_encoding" >}})。
  - [`Py_LegacyWindowsStdioFlag`]({{< ref "/c_api/init#c.Py_LegacyWindowsStdioFlag" >}}): 改用 [`PyConfig.legacy_windows_stdio`]({{< ref "/c_api/init_config#c.PyConfig.legacy_windows_stdio" >}})。
  - `Py_FileSystemDefaultEncoding`: 改用 [`PyConfig.filesystem_encoding`]({{< ref "/c_api/init_config#c.PyConfig.filesystem_encoding" >}})。
  - `Py_HasFileSystemDefaultEncoding`: 改用 [`PyConfig.filesystem_encoding`]({{< ref "/c_api/init_config#c.PyConfig.filesystem_encoding" >}})。
  - `Py_FileSystemDefaultEncodeErrors`: 改用 [`PyConfig.filesystem_errors`]({{< ref "/c_api/init_config#c.PyConfig.filesystem_errors" >}})。
  - `Py_UTF8Mode`: 改用 [`PyPreConfig.utf8_mode`]({{< ref "/c_api/init_config#c.PyPreConfig.utf8_mode" >}})。 (参见 [`Py_PreInitialize()`]({{< ref "/c_api/init_config#c.Py_PreInitialize" >}}))

  [`Py_InitializeFromConfig()`]({{< ref "/c_api/init#c.Py_InitializeFromConfig" >}}) API 应与 [`PyConfig`]({{< ref "/c_api/init_config#c.PyConfig" >}}) 一起使用。

#### Python 3.15 中的待移除功能

- 捆绑的 `libmpdecimal` 副本。
- The [`PyImport_ImportModuleNoBlock()`](https://docs.python.org/zh-cn/3.13/c-api/import.html#c.PyImport_ImportModuleNoBlock): 改用 [`PyImport_ImportModule()`](https://docs.python.org/zh-cn/3.13/c-api/import.html#c.PyImport_ImportModule)。
- [`PyWeakref_GetObject()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GetObject) 和 [`PyWeakref_GET_OBJECT()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GET_OBJECT): 改用 [`PyWeakref_GetRef()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GetRef)。
- [`Py_UNICODE`](https://docs.python.org/zh-cn/3.13/c-api/unicode.html#c.Py_UNICODE) 类型和 `Py_UNICODE_WIDE` 宏：改用 `wchar_t`。
- Python 初始化函数
  - [`PySys_ResetWarnOptions()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PySys_ResetWarnOptions): 改为清除 [`sys.warnoptions`]({{< ref "/library/python/sys#sys.warnoptions" >}}) 和 `warnings.filters`。
  - [`Py_GetExecPrefix()`]({{< ref "/c_api/init#c.Py_GetExecPrefix" >}}): Get [`sys.base_exec_prefix`]({{< ref "/library/python/sys#sys.base_exec_prefix" >}}) and [`sys.exec_prefix`]({{< ref "/library/python/sys#sys.exec_prefix" >}}) instead.
  - [`Py_GetPath()`]({{< ref "/c_api/init#c.Py_GetPath" >}}): 改为获取 [`sys.path`]({{< ref "/library/python/sys#sys.path" >}})。
  - [`Py_GetPrefix()`]({{< ref "/c_api/init#c.Py_GetPrefix" >}}): Get [`sys.base_prefix`]({{< ref "/library/python/sys#sys.base_prefix" >}}) and [`sys.prefix`]({{< ref "/library/python/sys#sys.prefix" >}}) instead.
  - [`Py_GetProgramFullPath()`]({{< ref "/c_api/init#c.Py_GetProgramFullPath" >}}): 改为获取 [`sys.executable`]({{< ref "/library/python/sys#sys.executable" >}})。
  - [`Py_GetProgramName()`]({{< ref "/c_api/init#c.Py_GetProgramName" >}}): 改为获取 [`sys.executable`]({{< ref "/library/python/sys#sys.executable" >}})。
  - [`Py_GetPythonHome()`]({{< ref "/c_api/init#c.Py_GetPythonHome" >}}): 改为获取 [`PyConfig.home`]({{< ref "/c_api/init_config#c.PyConfig.home" >}}) 或 [`PYTHONHOME`]({{< ref "/using/cmdline#envvar-PYTHONHOME" >}}) 环境变量。

#### 计划在未来版本中移除

​	以下 API 已被弃用，将被移除，但目前尚未确定移除日期。

- [`Py_TPFLAGS_HAVE_FINALIZE`](https://docs.python.org/zh-cn/3.13/c-api/typeobj.html#c.Py_TPFLAGS_HAVE_FINALIZE): 自 Python 3.8 起不再需要。
- [`PyErr_Fetch()`]({{< ref "/c_api/exceptions#c.PyErr_Fetch" >}}): 改用 [`PyErr_GetRaisedException()`]({{< ref "/c_api/exceptions#c.PyErr_GetRaisedException" >}})。
- [`PyErr_NormalizeException()`]({{< ref "/c_api/exceptions#c.PyErr_NormalizeException" >}}): 改用 [`PyErr_GetRaisedException()`]({{< ref "/c_api/exceptions#c.PyErr_GetRaisedException" >}})。
- [`PyErr_Restore()`]({{< ref "/c_api/exceptions#c.PyErr_Restore" >}}): 改用 [`PyErr_SetRaisedException()`]({{< ref "/c_api/exceptions#c.PyErr_SetRaisedException" >}})。
- [`PyModule_GetFilename()`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.PyModule_GetFilename): 改用 [`PyModule_GetFilenameObject()`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.PyModule_GetFilenameObject)。
- [`PyOS_AfterFork()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PyOS_AfterFork): 改用 [`PyOS_AfterFork_Child()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PyOS_AfterFork_Child)。
- [`PySlice_GetIndicesEx()`](https://docs.python.org/zh-cn/3.13/c-api/slice.html#c.PySlice_GetIndicesEx): 改用 [`PySlice_Unpack()`](https://docs.python.org/zh-cn/3.13/c-api/slice.html#c.PySlice_Unpack) and [`PySlice_AdjustIndices()`](https://docs.python.org/zh-cn/3.13/c-api/slice.html#c.PySlice_AdjustIndices)。
- `PyUnicode_AsDecodedObject()`: 改用 [`PyCodec_Decode()`](https://docs.python.org/zh-cn/3.13/c-api/codec.html#c.PyCodec_Decode)。
- `PyUnicode_AsDecodedUnicode()`: 改用 [`PyCodec_Decode()`](https://docs.python.org/zh-cn/3.13/c-api/codec.html#c.PyCodec_Decode)。
- `PyUnicode_AsEncodedObject()`: 改用 [`PyCodec_Encode()`](https://docs.python.org/zh-cn/3.13/c-api/codec.html#c.PyCodec_Encode)。
- `PyUnicode_AsEncodedUnicode()`: 改用 [`PyCodec_Encode()`](https://docs.python.org/zh-cn/3.13/c-api/codec.html#c.PyCodec_Encode)。
- [`PyUnicode_READY()`](https://docs.python.org/zh-cn/3.13/c-api/unicode.html#c.PyUnicode_READY): 自 Python 3.12 起不再需要
- `PyErr_Display()`: 改用 [`PyErr_DisplayException()`]({{< ref "/c_api/exceptions#c.PyErr_DisplayException" >}})。
- `_PyErr_ChainExceptions()`: 改用 `_PyErr_ChainExceptions1()`。
- `PyBytesObject.ob_shash` 成员：改为调用 [`PyObject_Hash()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_Hash)。
- `PyDictObject.ma_version_tag` 成员。
- 线程本地存储 (TLS) API：
  - [`PyThread_create_key()`]({{< ref "/c_api/init#c.PyThread_create_key" >}}): 改用 [`PyThread_tss_alloc()`]({{< ref "/c_api/init#c.PyThread_tss_alloc" >}})。
  - [`PyThread_delete_key()`]({{< ref "/c_api/init#c.PyThread_delete_key" >}}): 改用 [`PyThread_tss_free()`]({{< ref "/c_api/init#c.PyThread_tss_free" >}})。
  - [`PyThread_set_key_value()`]({{< ref "/c_api/init#c.PyThread_set_key_value" >}}): 改用 [`PyThread_tss_set()`]({{< ref "/c_api/init#c.PyThread_tss_set" >}})。
  - [`PyThread_get_key_value()`]({{< ref "/c_api/init#c.PyThread_get_key_value" >}}): 改用 [`PyThread_tss_get()`]({{< ref "/c_api/init#c.PyThread_tss_get" >}})。
  - [`PyThread_delete_key_value()`]({{< ref "/c_api/init#c.PyThread_delete_key_value" >}}): 改用 [`PyThread_tss_delete()`]({{< ref "/c_api/init#c.PyThread_tss_delete" >}})。
  - [`PyThread_ReInitTLS()`]({{< ref "/c_api/init#c.PyThread_ReInitTLS" >}}): 自 Python 3.7 起不再需要。

## 构建变化

- `arm64-apple-ios` and `arm64-apple-ios-simulator` are both now [**PEP 11**](https://peps.python.org/pep-0011/) tier 3 platforms. ([PEP 730]({{< ref "/whatsnew/idx#whatsnew313-platform-support" >}}) written and implementation contributed by Russell Keith-Magee in [gh-114099](https://github.com/python/cpython/issues/114099).)
- `aarch64-linux-android` and `x86_64-linux-android` are both now [**PEP 11**](https://peps.python.org/pep-0011/) tier 3 platforms. ([PEP 738]({{< ref "/whatsnew/idx#whatsnew313-platform-support" >}}) written and implementation contributed by Malcolm Smith in [gh-116622](https://github.com/python/cpython/issues/116622).)
- 现在 `wasm32-wasi` 是 [**PEP 11**](https://peps.python.org/pep-0011/) 第 2 层级的平台。 （由 Brett Cannon 在 [gh-115192](https://github.com/python/cpython/issues/115192) 中贡献。）
- `wasm32-emscripten` 不再是 [**PEP 11**](https://peps.python.org/pep-0011/) 的受支持平台。 （由 Brett Cannon 在 [gh-115192](https://github.com/python/cpython/issues/115192) 中贡献。）
- 现在构建 CPython 需要带有 C11 atomic 库支持的编译器、GCC 内置 atomic 函数或 MSVC 互锁内生函数。
- Autoconf 2.71 and aclocal 1.16.5 are now required to regenerate the `configure` script. (Contributed by Christian Heimes in [gh-89886](https://github.com/python/cpython/issues/89886) and by Victor Stinner in [gh-112090](https://github.com/python/cpython/issues/112090).)
- 需要 SQLite 3.15.2 或更新的版本才能构建 [`sqlite3`]({{< ref "/library/persistence/sqlite3#module-sqlite3" >}}) 扩展模块。 （由 Erlend Aasland 在 [gh-105875](https://github.com/python/cpython/issues/105875) 中贡献。）
- CPython now bundles the [mimalloc library](https://github.com/microsoft/mimalloc/) by default. It is licensed under the MIT license; see [mimalloc license](https://docs.python.org/zh-cn/3.13/license.html#mimalloc-license). The bundled mimalloc has custom changes, see [gh-113141](https://github.com/python/cpython/issues/113141) for details. (Contributed by Dino Viehland in [gh-109914](https://github.com/python/cpython/issues/109914).)
- 现在 `configure` 选项 [`--with-system-libmpdec`]({{< ref "/using/configure#cmdoption-with-system-libmpdec" >}}) 默认为 `yes`。 捆绑的 `libmpdecimal` 副本将在 Python 3.15 中被移除。
- 使用 `configure` [`--with-trace-refs`]({{< ref "/using/configure#cmdoption-with-trace-refs" >}}) (跟踪引用) 构建的 Python 现在与 Python 发布构建版和 [调试构建版]({{< ref "/using/configure#debug-build" >}}) 是 ABI 兼容的。 （由 Victor Stinner 在 [gh-108634](https://github.com/python/cpython/issues/108634) 中贡献。）
- On POSIX systems, the pkg-config (`.pc`) filenames now include the ABI flags. For example, the free-threaded build generates `python-3.13t.pc` and the debug build generates `python-3.13d.pc`.
- 现在 `errno`, `fcntl`, `grp`, `md5`, `pwd`, `resource`, `termios`, `winsound`, `_ctypes_test`, `_multiprocessing.posixshmem`, `_scproxy`, `_stat`, `_statistics`, `_testconsole`, `_testimportmultiple` 和 `_uuid` C 扩展是使用 [受限 C API]({{< ref "/c_api/stable#limited-c-api" >}}) 构建的。 （由 Victor Stinner 在 [gh-85283](https://github.com/python/cpython/issues/85283) 中贡献。）

## Porting to Python 3.13

​	本节列出了先前描述的更改以及可能需要更改代码的其他错误修正.

### Python API 的变化

- [PEP 667]({{< ref "/whatsnew/idx#whatsnew313-locals-semantics" >}}) introduces several changes to the semantics of [`locals()`]({{< ref "/library/functions#locals" >}}) and [`f_locals`]({{< ref "/reference/datamodel#frame.f_locals" >}}):

  - Calling [`locals()`]({{< ref "/library/functions#locals" >}}) in an [optimized scope]({{< ref "/glossary/idx#term-optimized-scope" >}}) now produces an independent snapshot on each call, and hence no longer implicitly updates previously returned references. Obtaining the legacy CPython behavior now requires explicit calls to update the initially returned dictionary with the results of subsequent calls to `locals()`. Code execution functions that implicitly target `locals()` (such as `exec` and `eval`) must be passed an explicit namespace to access their results in an optimized scope. (Changed as part of [**PEP 667**](https://peps.python.org/pep-0667/).)
  - Calling [`locals()`]({{< ref "/library/functions#locals" >}}) from a comprehension at module or class scope (including via `exec` or `eval`) once more behaves as if the comprehension were running as an independent nested function (i.e. the local variables from the containing scope are not included). In Python 3.12, this had changed to include the local variables from the containing scope when implementing [**PEP 709**](https://peps.python.org/pep-0709/). (Changed as part of [**PEP 667**](https://peps.python.org/pep-0667/).)
  - Accessing [`FrameType.f_locals`]({{< ref "/reference/datamodel#frame.f_locals" >}}) in an [optimized scope]({{< ref "/glossary/idx#term-optimized-scope" >}}) now returns a write-through proxy rather than a snapshot that gets updated at ill-specified times. If a snapshot is desired, it must be created explicitly with `dict` or the proxy's `.copy()` method. (Changed as part of [**PEP 667**](https://peps.python.org/pep-0667/).)

- [`functools.partial`]({{< ref "/library/functional/functools#functools.partial" >}}) now emits a [`FutureWarning`]({{< ref "/library/exceptions#FutureWarning" >}}) when used as a method. The behavior will change in future Python versions. Wrap it in [`staticmethod()`]({{< ref "/library/functions#staticmethod" >}}) if you want to preserve the old behavior. (Contributed by Serhiy Storchaka in [gh-121027](https://github.com/python/cpython/issues/121027).)

- An [`OSError`]({{< ref "/library/exceptions#OSError" >}}) is now raised by [`getpass.getuser()`]({{< ref "/library/allos/getpass#getpass.getuser" >}}) for any failure to retrieve a username, instead of [`ImportError`]({{< ref "/library/exceptions#ImportError" >}}) on non-Unix platforms or [`KeyError`]({{< ref "/library/exceptions#KeyError" >}}) on Unix platforms where the password database is empty.

- The value of the `mode` attribute of [`gzip.GzipFile`]({{< ref "/library/archiving/gzip#gzip.GzipFile" >}}) is now a string (`'rb'` or `'wb'`) instead of an integer (`1` or `2`). The value of the `mode` attribute of the readable file-like object returned by [`zipfile.ZipFile.open()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.open" >}}) is now `'rb'` instead of `'r'`. (Contributed by Serhiy Storchaka in [gh-115961](https://github.com/python/cpython/issues/115961).)

- [`mailbox.Maildir`]({{< ref "/library/netdata/mailbox#mailbox.Maildir" >}}) now ignores files with a leading dot (`.`). (Contributed by Zackery Spytz in [gh-65559](https://github.com/python/cpython/issues/65559).)

- [`pathlib.Path.glob()`]({{< ref "/library/filesys/pathlib#pathlib.Path.glob" >}}) and [`rglob()`]({{< ref "/library/filesys/pathlib#pathlib.Path.rglob" >}}) now return both files and directories if a pattern that ends with "`**`" is given, rather than directories only. Add a trailing slash to keep the previous behavior and only match directories.

- The [`threading`]({{< ref "/library/concurrency/threading#module-threading" >}}) module now expects the `_thread` module to have an `_is_main_interpreter()` function. This function takes no arguments and returns `True` if the current interpreter is the main interpreter.

  Any library or application that provides a custom `_thread` module must provide `_is_main_interpreter()`, just like the module's other "private" attributes. ([gh-112826](https://github.com/python/cpython/issues/112826).)

### C API 的变化

- `Python.h` no longer includes the `<ieeefp.h>` standard header. It was included for the `finite()` function which is now provided by the `<math.h>` header. It should now be included explicitly if needed. Remove also the `HAVE_IEEEFP_H` macro. (Contributed by Victor Stinner in [gh-108765](https://github.com/python/cpython/issues/108765).)

- `Python.h` no longer includes these standard header files: `<time.h>`, `<sys/select.h>` and `<sys/time.h>`. If needed, they should now be included explicitly. For example, `<time.h>` provides the `clock()` and `gmtime()` functions, `<sys/select.h>` provides the `select()` function, and `<sys/time.h>` provides the `futimes()`, `gettimeofday()` and `setitimer()` functions. (Contributed by Victor Stinner in [gh-108765](https://github.com/python/cpython/issues/108765).)

- On Windows, `Python.h` no longer includes the `<stddef.h>` standard header file. If needed, it should now be included explicitly. For example, it provides `offsetof()` function, and `size_t` and `ptrdiff_t` types. Including `<stddef.h>` explicitly was already needed by all other platforms, the `HAVE_STDDEF_H` macro is only defined on Windows. (Contributed by Victor Stinner in [gh-108765](https://github.com/python/cpython/issues/108765).)

- If the [`Py_LIMITED_API`]({{< ref "/c_api/stable#c.Py_LIMITED_API" >}}) macro is defined, `Py_BUILD_CORE`, `Py_BUILD_CORE_BUILTIN` and `Py_BUILD_CORE_MODULE` macros are now undefined by `<Python.h>`. (Contributed by Victor Stinner in [gh-85283](https://github.com/python/cpython/issues/85283).)

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

- [PEP 667]({{< ref "/whatsnew/idx#whatsnew313-locals-semantics" >}}) introduces several changes to frame-related functions:
  - The effects of mutating the dictionary returned from [`PyEval_GetLocals()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetLocals) in an [optimized scope]({{< ref "/glossary/idx#term-optimized-scope" >}}) have changed. New dict entries added this way will now *only* be visible to subsequent [`PyEval_GetLocals()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetLocals) calls in that frame, as [`PyFrame_GetLocals()`](https://docs.python.org/zh-cn/3.13/c-api/frame.html#c.PyFrame_GetLocals), [`locals()`]({{< ref "/library/functions#locals" >}}), and [`FrameType.f_locals`]({{< ref "/reference/datamodel#frame.f_locals" >}}) no longer access the same underlying cached dictionary. Changes made to entries for actual variable names and names added via the write-through proxy interfaces will be overwritten on subsequent calls to [`PyEval_GetLocals()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetLocals) in that frame. The recommended code update depends on how the function was being used, so refer to the deprecation notice on the function for details.
  - Calling [`PyFrame_GetLocals()`](https://docs.python.org/zh-cn/3.13/c-api/frame.html#c.PyFrame_GetLocals) in an [optimized scope]({{< ref "/glossary/idx#term-optimized-scope" >}}) now returns a write-through proxy rather than a snapshot that gets updated at ill-specified times. If a snapshot is desired, it must be created explicitly (e.g. with [`PyDict_Copy()`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDict_Copy)), or by calling the new [`PyEval_GetFrameLocals()`](https://docs.python.org/zh-cn/3.13/c-api/reflection.html#c.PyEval_GetFrameLocals) API.
  - `PyFrame_FastToLocals()` and `PyFrame_FastToLocalsWithError()` no longer have any effect. Calling these functions has been redundant since Python 3.11, when [`PyFrame_GetLocals()`](https://docs.python.org/zh-cn/3.13/c-api/frame.html#c.PyFrame_GetLocals) was first introduced.
  - `PyFrame_LocalsToFast()` no longer has any effect. Calling this function is redundant now that [`PyFrame_GetLocals()`](https://docs.python.org/zh-cn/3.13/c-api/frame.html#c.PyFrame_GetLocals) returns a write-through proxy for [optimized scopes]({{< ref "/glossary/idx#term-optimized-scope" >}}).

## 回归测试的变化

- 现在使用 `configure` [`--with-pydebug`]({{< ref "/using/configure#cmdoption-with-pydebug" >}}) 编译的 Python 将支持 [`-X presite=package.module`]({{< ref "/using/cmdline#cmdoption-X" >}}) 命令行选项。 如果被使用，它指明一个模块应当在解释器生命周期开始时，即 `site.py` 被执行之前被导入。 （由 Łukasz Langa 在 [gh-110769](https://github.com/python/cpython/issues/110769) 中贡献。）

## Notable changes in 3.13.1

### sys

- The previously undocumented special function [`sys.getobjects()`]({{< ref "/library/python/sys#sys.getobjects" >}}), which only exists in specialized builds of Python, may now return objects from other interpreters than the one it's called in.
