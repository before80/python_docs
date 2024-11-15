+++
title = "warnings --- 警告信息控制"
date = 2024-11-15T21:12:39+08:00
weight = 50
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/warnings.html](https://docs.python.org/zh-cn/3.13/library/warnings.html)
>
> 收录该文档的时间：`2024-11-15T21:12:39+08:00`

# `warnings` --- 警告信息控制

**源代码：** [Lib/warnings.py](https://github.com/python/cpython/tree/3.13/Lib/warnings.py)

------

​	通常以下情况会引发警告：提醒用户注意程序中的某些情况，而这些情况（通常）还不值得触发异常并终止程序。例如，当程序用到了某个过时的模块时，就可能需要发出一条警告。

​	Python 程序员可调用本模块中定义的 [`warn()`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.warn) 函数来发布警告。（C 语言程序员则用 [`PyErr_WarnEx()`](https://docs.python.org/zh-cn/3.13/c-api/exceptions.html#c.PyErr_WarnEx) ； 详见 [异常处理](https://docs.python.org/zh-cn/3.13/c-api/exceptions.html#exceptionhandling) ）。

​	警告信息通常会写入 [`sys.stderr`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stderr) ，但可以灵活改变，从忽略所有警告到变成异常都可以。警告的处理方式可以依据 [警告类型](https://docs.python.org/zh-cn/3.13/library/warnings.html#warning-categories) 、警告信息的文本和发出警告的源位置而进行变化。同一源位置重复出现的警告通常会被抑制。

​	控制警告信息有两个阶段：首先，每次引发警告时，决定信息是否要发出；然后，如果要发出信息，就用可由用户设置的钩子进行格式化并打印输出。

[警告过滤器](https://docs.python.org/zh-cn/3.13/library/warnings.html#warning-filter) 控制着是否发出警告信息，也即一系列的匹配规则和动作。调用 [`filterwarnings()`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.filterwarnings) 可将规则加入过滤器，调用 [`resetwarnings()`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.resetwarnings) 则可重置为默认状态。

​	警告信息的打印输出是通过调用 [`showwarning()`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.showwarning) 完成的，该函数可被重写；默认的实现代码是调用 [`formatwarning()`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.formatwarning) 进行格式化，自己编写的代码也可以调用此格式化函数。

​	参见

 

​	利用 [`logging.captureWarnings()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.captureWarnings) 可以采用标准的日志架构处理所有警告。



## 警告类别

​	警告的类别由一些内置的异常表示。这种分类有助于对警告信息进行分组过滤。

​	虽然在技术上警告类别属于 [内置异常](https://docs.python.org/zh-cn/3.13/library/exceptions.html#warning-categories-as-exceptions)，但也只是在此记录一下而已，因为在概念上他们属于警告机制的一部分。

​	通过对某个标准的警告类别进行派生，用户代码可以定义其他的警告类别。 警告类别必须是 [`Warning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#Warning) 类的子类。

​	目前已定义了以下警告类别的类：

| 类                                                           | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`Warning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#Warning) | 这是所有警告类别的基类。它是 [`Exception`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#Exception) 的子类。 |
| [`UserWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#UserWarning) | The default category for [`warn()`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.warn). |
| [`DeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#DeprecationWarning) | 已废弃特性警告的基类，这些警告是为其他 Python 开发者准备的（默认会忽略，除非在 `__main__` 中用代码触发）。 |
| [`SyntaxWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#SyntaxWarning) | 用于警告可疑语法的基类。                                     |
| [`RuntimeWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#RuntimeWarning) | 用于警告可疑运行时特性的基类。                               |
| [`FutureWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#FutureWarning) | 用于警告已废弃特性的基类，这些警告是为 Python 应用程序的最终用户准备的。 |
| [`PendingDeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#PendingDeprecationWarning) | 用于警告即将废弃功能的基类（默认忽略）。                     |
| [`ImportWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ImportWarning) | 导入模块时触发的警告的基类（默认忽略）。                     |
| [`UnicodeWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#UnicodeWarning) | 用于 Unicode 相关警告的基类。                                |
| [`BytesWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BytesWarning) | [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 和 [`bytearray`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytearray) 相关警告的基类。 |
| [`ResourceWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ResourceWarning) | 资源使用相关警告的基础类别（默认会被忽略）。ignored by default). |

*在 3.7 版本发生变更:* 以前 [`DeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#DeprecationWarning) 和 [`FutureWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#FutureWarning) 是根据某个功能是否完全删除或改变其行为来区分的。现在是根据受众和默认警告过滤器的处理方式来区分的。



## 警告过滤器

​	警告过滤器控制着警告是否被忽略、显示或转为错误（触发异常）。

​	从概念上讲，警告过滤器维护着一个经过排序的过滤器类别列表；任何具体的警告都会依次与列表中的每种过滤器进行匹配，直到找到一个匹配项；过滤器决定了匹配项的处理方式。每个列表项均为 ( *action* , *message* , *category* , *module* , *lineno* ) 格式的元组，其中：

- *action* 是以下字符串之一：

  | 值          | 处置                                                   |
  | :---------- | :----------------------------------------------------- |
  | `"default"` | 为发出警告的每个位置（模块+行号）打印第一个匹配警告    |
  | `"error"`   | 将匹配警告转换为异常                                   |
  | `"ignore"`  | 从不打印匹配的警告                                     |
  | `"always"`  | 总是打印匹配的警告                                     |
  | `"module"`  | 为发出警告的每个模块打印第一次匹配警告（无论行号如何） |
  | `"once"`    | 无论位置如何，仅打印第一次出现的匹配警告               |

- *message* 是一个包含警告消息的开头需要匹配的正则表达式的字符串，对大小写不敏感。 在 [`-W`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-W) 和 [`PYTHONWARNINGS`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONWARNINGS) 中，*message* 是警告消息的开头需要包含的字符串字面值（对大小写不敏感），将忽略 *message* 开头和末尾的任何空格。

- *category* 是警告类别的类（[`Warning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#Warning) 的子类），警告类别必须是其子类，才能匹配。

- *module* 是一个包含完整限定模块名称的开头需要匹配的正则表达式的字符串，对大小写敏感。 在 [`-W`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-W) 和 [`PYTHONWARNINGS`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONWARNINGS) 中，*module* 是完整限定模块名称需要与之相等的字符串字面值（对大小写敏感），将忽略 *module* 开头和末尾的任何空格。

- *lineno* 是个整数，发生警告的行号必须与之匹配，或为 `0` 表示与所有行号匹配。

​	由于 [`Warning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#Warning) 类是由内置类 [`Exception`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#Exception) 派生出来的，要把某个警告变成错误，只要触发 `category(message)` 即可。

​	如果警告不匹配所有已注册的过滤器，那就会应用 “default” 动作（正如其名）。



### Repeated Warning Suppression Criteria

​	The filters that suppress repeated warnings apply the following criteria to determine if a warning is considered a repeat:

- `"default"`: A warning is considered a repeat only if the (*message*, *category*, *module*, *lineno*) are all the same.
- `"module"`: A warning is considered a repeat if the (*message*, *category*, *module*) are the same, ignoring the line number.
- `"once"`: A warning is considered a repeat if the (*message*, *category*) are the same, ignoring the module and line number.



### 警告过滤器的介绍

​	警告过滤器由传给 Python 解释器的命令行 [`-W`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-W) 选项和 [`PYTHONWARNINGS`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONWARNINGS) 环境变量初始化。解释器在 `sys.warningoptions` 中保存了所有给出的参数，但不作解释；[`warnings`](https://docs.python.org/zh-cn/3.13/library/warnings.html#module-warnings) 模块在第一次导入时会解析这些参数（无效的选项被忽略，并会先向 [`sys.stderr`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stderr) 打印一条信息）。

​	每个警告过滤器的设定格式为冒号分隔的字段序列：

```
action:message:category:module:line
```

​	这些字段的含义在 [警告过滤器](https://docs.python.org/zh-cn/3.13/library/warnings.html#warning-filter) 中描述。当一行中列出多个过滤器时（如 [`PYTHONWARNINGS`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONWARNINGS)），过滤器间用逗号隔开，后面的优先于前面的（因为是从左到右应用的，最近应用的过滤器优先于前面的）。

​	常用的警告过滤器适用于所有的警告、特定类别的警告、由特定模块和包引发的警告。下面是一些例子：

```
default                      # 显示所有警告（即使是默认被忽略的）
ignore                       # 忽略所有警告
error                        # 将所有警告转换为错误
error::ResourceWarning       # 将 ResourceWarning 消息视为错误
default::DeprecationWarning  # 显示 DeprecationWarning 消息
ignore,default:::mymodule    # 只报告由 "mymodule" 触发的警告
error:::mymodule             # 将 "mymodule" 中的警告转换为错误
```



### 默认警告过滤器

​	Python 默认安装了几个警告过滤器，可以通过 [`-W`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-W) 命令行参数、 [`PYTHONWARNINGS`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONWARNINGS) 环境变量及调用 [`filterwarnings()`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.filterwarnings) 进行覆盖。

​	在常规发布的版本中，默认的警告过滤器包括（按优先顺序排列）：

```
default::DeprecationWarning:__main__
ignore::DeprecationWarning
ignore::PendingDeprecationWarning
ignore::ImportWarning
ignore::ResourceWarning
```

​	在 [调试版本](https://docs.python.org/zh-cn/3.13/using/configure.html#debug-build) 中，默认警告过滤器的列表是空的。

*在 3.2 版本发生变更:* 除了 [`PendingDeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#PendingDeprecationWarning) 之外，[`DeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#DeprecationWarning) 现在默认会被忽略。

*在 3.7 版本发生变更:* [`DeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#DeprecationWarning) 在被 `__main__` 中的代码直接触发时，默认会再次显示。

*在 3.7 版本发生变更:* 如果指定两次 [`-b`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-b)，则 [`BytesWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BytesWarning) 不再出现在默认的过滤器列表中，而是通过 `sys.warningoptions` 进行配置。



### 重写默认的过滤器

​	Python 应用程序的开发人员可能希望在默认情况下向用户隐藏 *所有* Python级别的警告，而只在运行测试或其他调试时显示这些警告。用于向解释器传递过滤器配置的 `sys.warningoptions` 属性可以作为一个标记，表示是否应该禁用警告：

```
import sys

if not sys.warnoptions:
    import warnings
    warnings.simplefilter("ignore")
```

​	建议 Python 代码测试的开发者使用如下代码，以确保被测代码默认显示 *所有* 警告：

```
import sys

if not sys.warnoptions:
    import os, warnings
    warnings.simplefilter("default") # 更改此进程中的过滤器
    os.environ["PYTHONWARNINGS"] = "default" # 也会影响子进程
```

​	最后，建议在 `__main__` 以外的命名空间运行用户代码的交互式开发者，请确保 [`DeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#DeprecationWarning) 在默认情况下是可见的，可采用如下代码（这里 `user_ns` 是用于执行交互式输入代码的模块）：

```
import warnings
warnings.filterwarnings("default", category=DeprecationWarning,
                                   module=user_ns.get("__name__"))
```



## 暂时禁止警告

​	如果明知正在使用会引起警告的代码，比如某个废弃函数，但不想看到警告（即便警告已经通过命令行作了显式配置），那么可以使用 [`catch_warnings`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.catch_warnings) 上下文管理器来抑制警告。

```
import warnings

def fxn():
    warnings.warn("deprecated", DeprecationWarning)

with warnings.catch_warnings():
    warnings.simplefilter("ignore")
    fxn()
```

​	在上下文管理器中，所有的警告将被简单地忽略。这样就能使用已知的过时代码而又不必看到警告，同时也不会限制警告其他可能不知过时的代码。注意：只能保证在单线程应用程序中生效。如果两个以上的线程同时使用 [`catch_warnings`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.catch_warnings) 上下文管理器，行为不可预知。



## 测试警告

​	要测试由代码引发的警告，请采用 [`catch_warnings`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.catch_warnings) 上下文管理器。有了它，就可以临时改变警告过滤器以方便测试。例如，以下代码可捕获所有的警告以便查看：

```
import warnings

def fxn():
    warnings.warn("deprecated", DeprecationWarning)

with warnings.catch_warnings(record=True) as w:
    # 使得所有警告总是会被触发。
    warnings.simplefilter("always")
    # 触发一个警告。
    fxn()
    # 执行一些查验
    assert len(w) == 1
    assert issubclass(w[-1].category, DeprecationWarning)
    assert "deprecated" in str(w[-1].message)
```

​	也可以用 `error` 取代 `always` ，让所有的警告都成为异常。需要注意的是，如果某条警告已经因为 `once` / `default` 规则而被引发，那么无论设置什么过滤器，该条警告都不会再出现，除非该警告有关的注册数据被清除。

​	一旦上下文管理器退出，警告过滤器将恢复到刚进此上下文时的状态。这样在多次测试时可防止意外改变警告过滤器，从而导致不确定的测试结果。模块中的 [`showwarning()`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.showwarning) 函数也被恢复到初始值。注意：这只能在单线程应用程序中得到保证。如果两个以上的线程同时使用 [`catch_warnings`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.catch_warnings) 上下文管理器，行为未定义。

​	当测试多项操作会引发同类警告时，重点是要确保每次操作都会触发新的警告（比如，将警告设置为异常并检查操作是否触发异常，检查每次操作后警告列表的长度是否有增加，否则就在每次新操作前将以前的警告列表项删除）。



## 为新版本的依赖关系更新代码

​	在默认情况下，主要针对 Python 开发者（而不是 Python 应用程序的最终用户）的警告类别，会被忽略。

​	值得注意的是，这个“默认忽略”的列表包含 [`DeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#DeprecationWarning) （适用于每个模块，除了 `__main__`），这意味着开发人员应该确保在测试代码时应将通常忽略的警告显示出来，以便未来破坏性 API 变化时及时收到通知（无论是在标准库还是第三方包）。

​	理想情况下，代码会有一个合适的测试套件，在运行测试时会隐含地启用所有警告（由 [`unittest`](https://docs.python.org/zh-cn/3.13/library/unittest.html#module-unittest) 模块提供的测试运行程序就是如此）。

​	在不太理想的情况下，可以通过向 Python 解释器传入 [`-Wd`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-W) (这是 `-W default` 的简写) 或设置环境变量 `PYTHONWARNINGS=default` 来检查应用程序是否用到了已弃用的接口。 这样可以启用对所有警告的默认处理操作，包括那些默认忽略的警告。 要改变遇到警告后执行的动作，可以改变传给 [`-W`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-W) 的参数 (例如 `-W error`)。 请参阅 [`-W`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-W) 旗标来了解更多的细节。



## 可用的函数

## warnings.**warn**(*message*, *category=None*, *stacklevel=1*, *source=None*, ***, *skip_file_prefixes=()*)

​	引发警告、忽略或者触发异常。 如果给出 *category* 参数，则必须是 [警告类别类](https://docs.python.org/zh-cn/3.13/library/warnings.html#warning-categories) ；默认为 [`UserWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#UserWarning)。 或者 *message* 可为 [`Warning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#Warning) 的实例，这时 *category* 将被忽略，转而采用 `message.__class__`。 在这种情况下，错误信息文本将是 `str(message)`。 如果某条警告被 [警告过滤器](https://docs.python.org/zh-cn/3.13/library/warnings.html#warning-filter) 改成了错误，本函数将触发一条异常。 参数 *stacklevel* 可供 Python 包装函数使用，比如:

```
def deprecated_api(message):
    warnings.warn(message, DeprecationWarning, stacklevel=2)
```

​	这会让警告指向 `deprecated_api` 的调用者，而不是 `deprecated_api` 本身的来源（因为后者会破坏警告消息的目的）。

*skip_file_prefixes* 关键字参数可被用来指明在栈层级计数时哪些栈帧要被忽略。 当常数 *stacklevel* 不能适应所有调用路径或在其他情况下难以维护如果你希望警告总是在一个包以外的调用位置上出现这将会很有用处。 如果提供，则它必须是一个字符串元组。 当提供了 prefixes 前缀时，stacklevel 会被隐式地覆盖为 `max(2, stacklevel)`。 要使得一个警告被归因至当前包以外的调用方你可以这样写:

```
# example/lower.py
_warn_skips = (os.path.dirname(__file__),)

def one_way(r_luxury_yacht=None, t_wobbler_mangrove=None):
    if r_luxury_yacht:
        warnings.warn("Please migrate to t_wobbler_mangrove=.",
                      skip_file_prefixes=_warn_skips)

# example/higher.py
from . import lower

def another_way(**kw):
    lower.one_way(**kw)
```

​	这将使得警告同时指向 `example.lower.one_way()` 和来自 `example` 包以外的调用代码的 `package.higher.another_way()` 调用位置。

*source* 是发出 [`ResourceWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ResourceWarning) 的被销毁对象。

*在 3.6 版本发生变更:* 加入 *source* 参数。

*在 3.12 版本发生变更:* 增加了 *skip_file_prefixes*。

## warnings.**warn_explicit**(*message*, *category*, *filename*, *lineno*, *module=None*, *registry=None*, *module_globals=None*, *source=None*)

​	这是 [`warn()`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.warn) 函数的底层接口，显式传入消息、类别、文件名和行号，以及可选的模块名和注册表（应为模块的 `__warningregistry__` 字典）。 模块名称默认为去除了 `.py` 的文件名；如果未传递注册表，警告就不会被抑制。 *message* 必须是个字符串，*category* 是 [`Warning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#Warning) 的子类；或者*message* 可为 [`Warning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#Warning) 的实例，且 *category* 将被忽略。

*module_globals* 应为发出警告的代码所用的全局命名空间。（该参数用于从 zip 文件或其他非文件系统导入模块时显式源码）。

*source* 是发出 [`ResourceWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ResourceWarning) 的被销毁对象。

*在 3.6 版本发生变更:* 加入 *source* 参数。

## warnings.**showwarning**(*message*, *category*, *filename*, *lineno*, *file=None*, *line=None*)

​	将警告信息写入文件。默认的实现代码是调用 `formatwarning(message, category, filename, lineno, line)` 并将结果字符串写入 *file* ，默认文件为 [`sys.stderr`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stderr)。通过将任何可调用对象赋给 `warnings.showwarning` 可替换掉该函数。*line* 是要包含在警告信息中的一行源代码；如果未提供 *line*，[`showwarning()`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.showwarning) 将尝试读取由*filename* 和 *lineno* 指定的行。

## warnings.**formatwarning**(*message*, *category*, *filename*, *lineno*, *line=None*)

​	以标准方式格式化一条警告信息。将返回一个字符串，可能包含内嵌的换行符，并以换行符结束。如果未提供 *line*，[`formatwarning()`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.formatwarning) 将尝试读取由 *filename* 和 *lineno* 指定的行。

## warnings.**filterwarnings**(*action*, *message=''*, *category=Warning*, *module=''*, *lineno=0*, *append=False*)

​	在 [警告过滤器种类](https://docs.python.org/zh-cn/3.13/library/warnings.html#warning-filter) 列表中插入一条数据项。默认情况下，该数据项将被插到前面；如果 *append* 为 True，则会插到后面。这里会检查参数的类型，编译 *message* 和 *module* 正则表达式，并将他们作为一个元组插入警告过滤器的列表中。如果两者都与某种警告匹配，那么靠近列表前面的数据项就会覆盖后面的项。省略的参数默认匹配任意值。

## warnings.**simplefilter**(*action*, *category=Warning*, *lineno=0*, *append=False*)

​	在 [警告过滤器种类](https://docs.python.org/zh-cn/3.13/library/warnings.html#warning-filter) 列表中插入一条简单数据项。函数参数的含义与 [`filterwarnings()`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.filterwarnings) 相同，但不需要正则表达式，因为插入的过滤器总是匹配任何模块中的任何信息，只要类别和行号匹配即可。

## warnings.**resetwarnings**()

​	重置警告过滤器。这将丢弃之前对 [`filterwarnings()`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.filterwarnings) 的所有调用，包括 [`-W`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-W) 命令行选项和对 [`simplefilter()`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.simplefilter) 的调用效果。

## @warnings.**deprecated**(*msg*, ***, *category=DeprecationWarning*, *stacklevel=1*)

​	指明某个类、函数或重载已被弃用的装饰器。

​	当此装饰器被应用于某个对象时，在运行时当该对象被使用时将会发出弃用警告。 [静态类型检查器](https://docs.python.org/zh-cn/3.13/glossary.html#term-static-type-checker) 也会生成已弃用对象的使用情况诊断报告。

​	用法：

```
from warnings import deprecated
from typing import overload

@deprecated("Use B instead")
class A:
    pass

@deprecated("Use g instead")
def f():
    pass

@overload
@deprecated("int support is deprecated")
def g(x: int) -> int: ...
@overload
def g(x: str) -> int: ...
```

​	在运行时当已弃用对象被使用时将发出由 *category* 所指明的警告。 对于函数，这会在执行调用时发生；对于类，则会在实例化或创建子类时发生。 如果 *category* 为 `None`，则不会在运行时发出警告。 *stacklevel* 确定要在哪里发出警告。 如为 `1` (默认值)，警告将在已弃用对象的直接调用方那里发出；如为更高的值，它将在栈的更高层级上发出。 静态类型检查器的行为不会受到 *category* 和 *stacklevel* 参数的影响。

​	传给该装饰器的弃用消息保存在被装饰对象的 `__deprecated__` 属性中。 如果应用于重载，该装饰器必须位于 [`@overload`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.overload) 装饰器之后以使该属性存在于 [`typing.get_overloads()`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.get_overloads) 所返回的重载之中。

*Added in version 3.13:* 参见 [**PEP 702**](https://peps.python.org/pep-0702/)。

## 可用的上下文管理器

## *class* warnings.**catch_warnings**(***, *record=False*, *module=None*, *action=None*, *category=Warning*, *lineno=0*, *append=False*)

​	该上下文管理器会复制警告过滤器和 [`showwarning()`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.showwarning) 函数，并在退出时恢复。 如果 *record* 参数是 [`False`](https://docs.python.org/zh-cn/3.13/library/constants.html#False) (默认)，则在进入时会返回 [`None`](https://docs.python.org/zh-cn/3.13/library/constants.html#None)。 如果 *record* 为 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)，则返回一个列表，列表由自定义 [`showwarning()`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.showwarning) 函数所用对象逐步填充（该函数还会抑制 `sys.stdout` 的输出）。 列表中每个对象的属性与 [`showwarning()`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.showwarning) 的参数名称相同。

*module* 参数代表一个模块，当导入 [`warnings`](https://docs.python.org/zh-cn/3.13/library/warnings.html#module-warnings) 时，将被用于代替返回的模块，其过滤器将被保护。该参数主要是为了测试 [`warnings`](https://docs.python.org/zh-cn/3.13/library/warnings.html#module-warnings) 模块自身。

​	如果 *action* 参数不为 `None`，则剩余的参数会被传递给 [`simplefilter()`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.simplefilter) 就如同它在进入上下文时被立即调用一样。

​	请参阅 [警告过滤器](https://docs.python.org/zh-cn/3.13/library/warnings.html#warning-filter) 了解 *category* 和 *lineno* 形参的含义。

​	备注

 

[`catch_warnings`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.catch_warnings) 管理器的工作方式，是替换并随后恢复模块的 [`showwarning()`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.showwarning) 函数和内部的过滤器种类列表。这意味着上下文管理器将会修改全局状态，因此不是线程安全的。

*在 3.11 版本发生变更:* 增加了 *action*, *category*, *lineno* 和 *append* 形参。
