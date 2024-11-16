+++
title = "Python 开发模式"
date = 2024-11-15T21:03:03+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/devmode.html](https://docs.python.org/zh-cn/3.13/library/devmode.html)
>
> 收录该文档的时间：`2024-11-15T21:03:03+08:00`

# Python 开发模式

> Added in version 3.7.
>

​	开发模式下的 Python 加入了额外的运行时检查，由于开销太大，并非默认启用的。如果代码能够正确执行，默认的调试级别足矣，不应再提高了；仅当觉察到问题时再提升警告触发的级别。

​	使用 [`-X dev`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-X) 命令行参数或将环境变量 [`PYTHONDEVMODE`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONDEVMODE) 置为 `1` ，可以启用开发模式。

​	另请参考 [Python debug build](https://docs.python.org/zh-cn/3.13/using/configure.html#debug-build) 。

## Python 开发模式的效果

​	启用 Python 开发模式后的效果，与以下命令类似，不过还有下面的额外效果：

```
PYTHONMALLOC=debug PYTHONASYNCIODEBUG=1 python -W default -X faulthandler
```

​	Python 开发模式的效果：

- 加入 `default` [warning filter](https://docs.python.org/zh-cn/3.13/library/warnings.html#describing-warning-filters) 。下述警告信息将会显示出来：

  - [`DeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#DeprecationWarning)
  - [`ImportWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ImportWarning)
  - [`PendingDeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#PendingDeprecationWarning)
  - [`ResourceWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ResourceWarning)

  通常上述警告是由默认的 [warning filters](https://docs.python.org/zh-cn/3.13/library/warnings.html#describing-warning-filters) 负责处理的。

  效果类似于采用了 [`-W default`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-W) 命令行参数。

  使用命令行参数 [`-W error`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-W) 或将环境变量 [`PYTHONWARNINGS`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONWARNINGS) 设为 `error`，可将警告视为错误。

- 在内存分配程序中安装调试钩子，用以查看：

  - 缓冲区下溢
  - 缓冲区上溢
  - 内存分配 API 冲突
  - 不安全的 GIL 调用

  参见 C 函数 [`PyMem_SetupDebugHooks()`](https://docs.python.org/zh-cn/3.13/c-api/memory.html#c.PyMem_SetupDebugHooks) 。

  效果如同将环境变量 [`PYTHONMALLOC`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONMALLOC) 设为 `debug`。

  若要启用 Python 开发模式，却又不要在内存分配程序中安装调试钩子，请将 环境变量 [`PYTHONMALLOC`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONMALLOC) 设为 `default`。

- 在 Python 启动时调用 [`faulthandler.enable()`](https://docs.python.org/zh-cn/3.13/library/faulthandler.html#faulthandler.enable) 来为 [`SIGSEGV`](https://docs.python.org/zh-cn/3.13/library/signal.html#signal.SIGSEGV), [`SIGFPE`](https://docs.python.org/zh-cn/3.13/library/signal.html#signal.SIGFPE), [`SIGABRT`](https://docs.python.org/zh-cn/3.13/library/signal.html#signal.SIGABRT), [`SIGBUS`](https://docs.python.org/zh-cn/3.13/library/signal.html#signal.SIGBUS) 和 [`SIGILL`](https://docs.python.org/zh-cn/3.13/library/signal.html#signal.SIGILL) 信号安装处理器以便在程序崩溃时转储 Python 回溯信息。

  其行为如同使用了 [`-X faulthandler`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-X) 命令行选项或将 [`PYTHONFAULTHANDLER`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONFAULTHANDLER) 环境变量设为 `1`。

- 启用 [asyncio debug mode](https://docs.python.org/zh-cn/3.13/library/asyncio-dev.html#asyncio-debug-mode)。比如 [`asyncio`](https://docs.python.org/zh-cn/3.13/library/asyncio.html#module-asyncio) 会检查没有等待的协程并记录下来。

  效果如同将环境变量 [`PYTHONASYNCIODEBUG`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONASYNCIODEBUG) 设为 `1`。

- 检查字符串编码和解码函数的 *encoding* 和 *errors* 参数。例如： [`open()`](https://docs.python.org/zh-cn/3.13/library/functions.html#open) 、 [`str.encode()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str.encode) 和 [`bytes.decode()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes.decode)。

  为了获得最佳性能，默认只会在第一次编码/解码错误时才会检查 *errors* 参数，有时 *encoding* 参数为空字符串时还会被忽略。

- [`io.IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 的析构函数会记录 `close()` 触发的异常。

- 将 [`sys.flags`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.flags) 的 [`dev_mode`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.flags.dev_mode) 属性设为 `True`。

​	Python 开发模式下，默认不会启用 [`tracemalloc`](https://docs.python.org/zh-cn/3.13/library/tracemalloc.html#module-tracemalloc) 模块，因为其性能和内存开销太大。启用 [`tracemalloc`](https://docs.python.org/zh-cn/3.13/library/tracemalloc.html#module-tracemalloc) 模块后，能够提供有关错误来源的一些额外信息。例如，[`ResourceWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ResourceWarning) 记录了资源分配的跟踪信息，而缓冲区溢出错误记录了内存块分配的跟踪信息。

​	Python 开发模式不会阻止命令行参数 [`-O`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-O) 删除 [`assert`](https://docs.python.org/zh-cn/3.13/reference/simple_stmts.html#assert) 语句，也不会阻止将 [`__debug__`](https://docs.python.org/zh-cn/3.13/library/constants.html#debug__) 设为 `False`。

​	Python 开发模式只能在 Python 启动时启用。其参数值可从 [`sys.flags.dev_mode`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.flags) 读取。

*在 3.8 版本发生变更:* 现在， [`io.IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 的析构函数会记录 `close()` 触发的异常。

*在 3.9 版本发生变更:* 现在，字符串编码和解码操作时会检查 *encoding* 和 *errors* 参数。

## ResourceWarning 示例

​	以下示例将统计由命令行指定的文本文件的行数：

```
import sys

def main():
    fp = open(sys.argv[1])
    nlines = len(fp.readlines())
    print(nlines)
    # 文件将隐式地关闭

if __name__ == "__main__":
    main()
```

​	上述代码没有显式关闭文件。默认情况下，Python 不会触发任何警告。下面用 README.txt 文件测试下，有 269 行：

```
$ python script.py README.txt
269
```

​	启用 Python 开发模式后，则会显示一条 [`ResourceWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ResourceWarning) 警告：

```
$ python -X dev script.py README.txt
269
script.py:10: ResourceWarning: unclosed file <_io.TextIOWrapper name='README.rst' mode='r' encoding='UTF-8'>
  main()
ResourceWarning: Enable tracemalloc to get the object allocation traceback
```

​	启用 [`tracemalloc`](https://docs.python.org/zh-cn/3.13/library/tracemalloc.html#module-tracemalloc) 后，则还会显示打开文件的那行代码：

```
$ python -X dev -X tracemalloc=5 script.py README.rst
269
script.py:10: ResourceWarning: unclosed file <_io.TextIOWrapper name='README.rst' mode='r' encoding='UTF-8'>
  main()
Object allocated at (most recent call last):
  File "script.py", lineno 10
    main()
  File "script.py", lineno 4
    fp = open(sys.argv[1])
```

​	修正方案就是显式关闭文件。下面用上下文管理器作为示例：

```
def main():
    # 在退出 with 语句块时显式地关闭文件
    with open(sys.argv[1]) as fp:
        nlines = len(fp.readlines())
    print(nlines)
```

​	未能显式关闭资源，会让资源打开时长远超预期；在退出 Python 时可能会导致严重问题。这在 CPython 中比较糟糕，但在 PyPy 中会更糟。显式关闭资源能让应用程序更加稳定可靠。

## 文件描述符错误示例

​	显示自身的第一行代码：

```
import os

def main():
    fp = open(__file__)
    firstline = fp.readline()
    print(firstline.rstrip())
    os.close(fp.fileno())
    # 文件被隐式地关闭

main()
```

​	默认情况下，Python 不会触发任何警告：

```
$ python script.py
import os
```

​	在 Python 开发模式下，会在析构文件对象时显示 [`ResourceWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ResourceWarning) 并记录 “Bad file descriptor” 错误。

```
$ python -X dev script.py
import os
script.py:10: ResourceWarning: unclosed file <_io.TextIOWrapper name='script.py' mode='r' encoding='UTF-8'>
  main()
ResourceWarning: Enable tracemalloc to get the object allocation traceback
Exception ignored in: <_io.TextIOWrapper name='script.py' mode='r' encoding='UTF-8'>
Traceback (most recent call last):
  File "script.py", line 10, in <module>
    main()
OSError: [Errno 9] Bad file descriptor
```

`os.close(fp.fileno())` 会关闭文件描述符。当文件对象析构函数试图再次关闭文件描述符时会失败，并触发 `Bad file descriptor` 错误。每个文件描述符只允许关闭一次。在最坏的情况下，关闭两次会导致程序崩溃（示例可参见 [bpo-18748](https://bugs.python.org/issue?@action=redirect&bpo=18748) ）。

​	修正方案是删除 `os.close(fp.fileno())` 这一行，或者打开文件时带上 `closefd=False` 参数。
