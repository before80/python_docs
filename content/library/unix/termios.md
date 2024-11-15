+++
title = "termios --- POSIX 风格的 tty 控制"
date = 2024-11-15T21:30:54+08:00
weight = 30
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/termios.html](https://docs.python.org/zh-cn/3.13/library/termios.html)
>
> 收录该文档的时间：`2024-11-15T21:30:54+08:00`

# `termios` --- POSIX 风格的 tty 控制

------

​	此模块提供了针对tty I/O 控制的 POSIX 调用的接口。 有关此类调用的完整描述，请参阅 *[termios(3)](https://manpages.debian.org/termios(3))* Unix 指南页。 它仅在当安装时配置了支持 POSIX *termios* 风格的 tty I/O 控制的 Unix 版本上可用。

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): Unix.

​	此模块中的所有函数均接受一个文件描述符 *fd* 作为第一个参数。 这可以是一个整数形式的文件描述符，例如 `sys.stdin.fileno()` 所返回的对象，或是一个 [file object](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-object)，例如 `sys.stdin` 本身。

​	这个模块还定义了与此处所提供的函数一起使用的所有必要的常量；这些常量与它们在 C 中的对应常量同名。 请参考你的系统文档了解有关如何使用这些终端控制接口的更多信息。

​	这个模块定义了以下函数：

## termios.**tcgetattr**(*fd*)

​	对于文件描述符 *fd* 返回一个包含 tty 属性的列表，形式如下: `[iflag, oflag, cflag, lflag, ispeed, ospeed, cc]`，其中 *cc* 为一个包含 tty 特殊字符的列表（每一项都是长度为 1 的字符串，索引号为 `VMIN` 和 `VTIME` 的项除外，这些字段如有定义则应为整数）。 对旗标和速度以及 *cc* 数组中索引的解读必须使用在 [`termios`](https://docs.python.org/zh-cn/3.13/library/termios.html#module-termios) 模块中定义的符号常量来完成。

## termios.**tcsetattr**(*fd*, *when*, *attributes*)

​	根据 *attributes* 为文件描述符 *fd* 设置 tty 的属性，它是一个类似于 [`tcgetattr()`](https://docs.python.org/zh-cn/3.13/library/termios.html#termios.tcgetattr) 的返回值的列表。 *when* 参数决定这些属性在何时被更改：

## termios.**TCSANOW**

​	立即更改属性。

## termios.**TCSADRAIN**

​	传输完所有队列输出后再更改属性。

## termios.**TCSAFLUSH**

​	传输完所有队列输出并丢弃所有队列输入后再更改属性。

## termios.**tcsendbreak**(*fd*, *duration*)

​	在文件描述符 *fd* 上发送一个中断。 *duration* 为零表示发送时长为 0.25--0.5 秒的中断；*duration* 非零值的含义取决于具体系统。

## termios.**tcdrain**(*fd*)

​	进入等待状态直到写入文件描述符 *fd* 的所有输出都传送完毕。

## termios.**tcflush**(*fd*, *queue*)

​	在文件描述符 *fd* 上丢弃队列数据。 *queue* 选择器指定哪个队列: `TCIFLUSH` 表示输入队列，`TCOFLUSH` 表示输出队列，或 `TCIOFLUSH` 表示两个队列同时。

## termios.**tcflow**(*fd*, *action*)

​	在文件描述符 *fd* 上挂起一战恢复输入或输出。 *action* 参数可以为 `TCOOFF` 表示挂起输出，`TCOON` 表示重启输出，`TCIOFF` 表示挂起输入，或 `TCION` 表示重启输入。

## termios.**tcgetwinsize**(*fd*)

​	返回一个包含文件描述符 *fd* 的 tty 窗口大小的元组 `(ws_row, ws_col)`。 需要 `termios.TIOCGWINSZ` 或 `termios.TIOCGSIZE`。

*Added in version 3.11.*

## termios.**tcsetwinsize**(*fd*, *winsize*)

​	将文件描述符 *fd* 的 tty 窗口大小设置为 *winsize*，这是一个包含两项的元组 `(ws_row, ws_col)`，如 [`tcgetwinsize()`](https://docs.python.org/zh-cn/3.13/library/termios.html#termios.tcgetwinsize) 所返回的一样。 要求至少定义了 (`termios.TIOCGWINSZ`, `termios.TIOCSWINSZ`); (`termios.TIOCGSIZE`, `termios.TIOCSSIZE`) 对之一。

*Added in version 3.11.*

​	参见

## 模块 [`tty`](https://docs.python.org/zh-cn/3.13/library/tty.html#module-tty)

​	针对常用终端控制操作的便捷函数。



## 示例

​	这个函数可提示输入密码并且关闭回显。 请注意其采取的技巧是使用一个单独的 [`tcgetattr()`](https://docs.python.org/zh-cn/3.13/library/termios.html#termios.tcgetattr) 调用和一个 [`try`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#try) ... [`finally`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#finally) 语句来确保旧的 tty 属性无论在何种情况下都会被原样保存:

```
def getpass(prompt="Password: "):
    import termios, sys
    fd = sys.stdin.fileno()
    old = termios.tcgetattr(fd)
    new = termios.tcgetattr(fd)
    new[3] = new[3] & ~termios.ECHO          # lflags
    try:
        termios.tcsetattr(fd, termios.TCSADRAIN, new)
        passwd = input(prompt)
    finally:
        termios.tcsetattr(fd, termios.TCSADRAIN, old)
    return passwd
```
