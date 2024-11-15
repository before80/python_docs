+++
title = "tty --- 终端控制函数"
date = 2024-11-15T21:30:54+08:00
weight = 40
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/tty.html](https://docs.python.org/zh-cn/3.13/library/tty.html)
>
> 收录该文档的时间：`2024-11-15T21:30:54+08:00`

# `tty` --- 终端控制函数

**Source code:** [Lib/tty.py](https://github.com/python/cpython/tree/3.13/Lib/tty.py)

------

[`tty`](https://docs.python.org/zh-cn/3.13/library/tty.html#module-tty) 模块定义了将 tty 放入 cbreak 和 raw 模式的函数。

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): Unix.

​	因为它需要 [`termios`](https://docs.python.org/zh-cn/3.13/library/termios.html#module-termios) 模块，所以只能在 Unix 上运行。

[`tty`](https://docs.python.org/zh-cn/3.13/library/tty.html#module-tty) 模块定义了以下函数：

## tty.**cfmakeraw**(*mode*)

​	操作 tty 属性列表 *mode*，它是一个与 [`termios.tcgetattr()`](https://docs.python.org/zh-cn/3.13/library/termios.html#termios.tcgetattr) 的返回值类似的列表，将其转换为原始模式 tty 的属性列表。

*Added in version 3.12.*

## tty.**cfmakecbreak**(*mode*)

​	操作 tty 属性列表 *mode*，它是一个与 [`termios.tcgetattr()`](https://docs.python.org/zh-cn/3.13/library/termios.html#termios.tcgetattr) 的返回值类似的列表，将其转换为 cbreak 模式的 tty 的属性列表。

​	这将清除 *mode* 中的 `ECHO` 和 `ICANON` 本地模式旗标并将最小输入设为 1 字节且无延迟。

*Added in version 3.12.*

*在 3.12.2 版本发生变更:* `ICRNL` 旗标将不再被清除。 这与 Linux 和 macOS 的 `stty cbreak` 行为以及 [`setcbreak()`](https://docs.python.org/zh-cn/3.13/library/tty.html#tty.setcbreak) 在历史上所做的相匹配。

## tty.**setraw**(*fd*, *when=termios.TCSAFLUSH*)

​	将文件描述符 *fd* 的模式改为 raw。 如果 *when* 被省略，它将默认为 [`termios.TCSAFLUSH`](https://docs.python.org/zh-cn/3.13/library/termios.html#termios.TCSAFLUSH)，并将被传给 [`termios.tcsetattr()`](https://docs.python.org/zh-cn/3.13/library/termios.html#termios.tcsetattr)。 [`termios.tcgetattr()`](https://docs.python.org/zh-cn/3.13/library/termios.html#termios.tcgetattr) 的返回值在将 *fd* 设为 raw 模式前会被保存；该值将被返回。

*在 3.12 版本发生变更:* 现在返回值就是原本的 tty 属性，而不是 `None`。

## tty.**setcbreak**(*fd*, *when=termios.TCSAFLUSH*)

​	将文件描述符 *fd* 的模式改为 cbreak。 如果 *when* 被省略，它将默认为 [`termios.TCSAFLUSH`](https://docs.python.org/zh-cn/3.13/library/termios.html#termios.TCSAFLUSH)，并将被传给 [`termios.tcsetattr()`](https://docs.python.org/zh-cn/3.13/library/termios.html#termios.tcsetattr)。 [`termios.tcgetattr()`](https://docs.python.org/zh-cn/3.13/library/termios.html#termios.tcgetattr) 的返回值在将 *fd* 设为 cbreak 模式前会被保存；该值将被返回。

​	这将清除 `ECHO` 和 `ICANON` 本地模式旗标并将最小输入设为 1 字节且无延迟。

*在 3.12 版本发生变更:* 现在返回值就是原本的 tty 属性，而不是 `None`。

*在 3.12.2 版本发生变更:* `ICRNL` 旗标将不再被清除。 这恢复了 Python 3.11 及更早版本的行为并与 Linux, macOS 和BSD 在它们的 `stty(1)` 指南页对于 cbreak 模式的描述相匹配。

> 参见
>
> 模块 [`termios`](https://docs.python.org/zh-cn/3.13/library/termios.html#module-termios)
>
> ​	低级终端控制接口。
