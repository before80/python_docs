+++
title = "pty --- 伪终端工具"
date = 2024-11-15T21:30:54+08:00
weight = 50
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/pty.html](https://docs.python.org/zh-cn/3.13/library/pty.html)
>
> 收录该文档的时间：`2024-11-15T21:30:54+08:00`

# `pty` --- 伪终端工具

**源代码:** [Lib/pty.py](https://github.com/python/cpython/tree/3.13/Lib/pty.py)

------

[`pty`]({{< ref "/library/unix/pty#module-pty" >}}) 模块定义了一些处理“伪终端”概念的操作：启动另一个进程并能以程序方式在其控制终端中进行读写。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

​	伪终端处理高度依赖于具体平台。 此代码主要针对 Linux, FreeBSD 和 macOS 进行了测试（它应当也能在其他 POSIX 平台上工作，但是未经充分测试）。

[`pty`]({{< ref "/library/unix/pty#module-pty" >}}) 模块定义了下列函数:

## pty.**fork**()

​	分叉。 将子进程的控制终端连接到一个伪终端。 返回值为 `(pid, fd)`。 请注意子进程获得 *pid* 0 而 *fd* 为 *invalid*。 父进程返回值为子进程的 *pid* 而 *fd* 为一个连接到子进程的控制终端（并同时连接到子进程的标准输入和输出）的文件描述符。

​	警告

 

​	在 macOS 上将此函数与高层级的系统 API 混用是不安全的，包括 [`urllib.request`]({{< ref "/library/internet/urllib/urllib_request#module-urllib.request" >}})。

## pty.**openpty**()

​	打开一个新的伪终端对，如果可能将使用 [`os.openpty()`]({{< ref "/library/allos/os#os.openpty" >}})，或是针对通用 Unix 系统的模拟代码。 返回一个文件描述符对 `(master, slave)`，分别表示主从两端。

## pty.**spawn**(*argv*[, *master_read*[, *stdin_read*]])

​	生成一个进程，并将其控制终端连接到当前进程的标准 io。 这常被用来应对坚持要从控制终端读取数据的程序。 在 pty 背后生成的进程预期最后将被终止，而且当它被终止时 *spawn* 将会返回。

​	将当前进程的 STDIN 拷贝到子进程并将从子进程接收的数据拷贝到当前进程的 STDOUT 的循环。 如果当前进程的 STDIN 关闭则它不会向子进程发信号。

*master_read* 和 *stdin_read* 函数会被传入一个文件描述符供它们读取内容，并且它们总是应当返回一个字节串。 为了强制 spawn 在子进程退出之前返回，应当返回一个空字节数组来提示文件的结束。

​	两个函数的默认实现在每次函数被调用时将读取并返回至多 1024 个字节。 会向 *master_read* 回调传入伪终端的主文件描述符以从子进程读取输出，而向 *stdin_read* 传入文件描述符 0 以从父进程的标准输入读取数据。

​	从两个回调返回空字节串会被解读为文件结束 (EOF) 条件，在此之后回调将不再被调用。 如果 *stdin_read* 发出 EOF 信号则控制终端就不能再与父进程或子进程进行通信。 除非子进程将不带任何输入就退出，否则随后 *spawn* 将一直循环下去。 如果 *master_read* 发出 EOF 信号则会有相同的行为结果（至少是在 Linux 上）。

​	从子进程中的 [`os.waitpid()`]({{< ref "/library/allos/os#os.waitpid" >}}) 返回退出状态值。

[`os.waitstatus_to_exitcode()`]({{< ref "/library/allos/os#os.waitstatus_to_exitcode" >}}) 可被用来将退出状态转换为退出码。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `pty.spawn` 并附带参数 `argv`。

> 在 3.4 版本发生变更: [`spawn()`]({{< ref "/library/unix/pty#pty.spawn" >}}) 现在会从子进程的 [`os.waitpid()`]({{< ref "/library/allos/os#os.waitpid" >}}) 返回状态值。

## 示例

​	以下程序的作用类似于 Unix 命令 *[script(1)](https://manpages.debian.org/script(1))*，它使用一个伪终端来记录一个 "typescript" 里终端进程的所有输入和输出:

```
import argparse
import os
import pty
import sys
import time

parser = argparse.ArgumentParser()
parser.add_argument('-a', dest='append', action='store_true')
parser.add_argument('-p', dest='use_python', action='store_true')
parser.add_argument('filename', nargs='?', default='typescript')
options = parser.parse_args()

shell = sys.executable if options.use_python else os.environ.get('SHELL', 'sh')
filename = options.filename
mode = 'ab' if options.append else 'wb'

with open(filename, mode) as script:
    def read(fd):
        data = os.read(fd, 1024)
        script.write(data)
        return data

    print('Script started, file is', filename)
    script.write(('Script started on %s\n' % time.asctime()).encode())

    pty.spawn(shell, read)

    script.write(('Script done on %s\n' % time.asctime()).encode())
    print('Script done, file is', filename)
```
