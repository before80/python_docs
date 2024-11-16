+++
title = "fcntl --- fcntl 和 ioctl 系统调用"
date = 2024-11-15T21:30:54+08:00
weight = 60
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/fcntl.html](https://docs.python.org/zh-cn/3.13/library/fcntl.html)
>
> 收录该文档的时间：`2024-11-15T21:30:54+08:00`

# `fcntl` --- `fcntl` 和 `ioctl` 系统调用

------

​	本模块基于文件描述符来执行文件和 I/O 控制。 它是 `fcntl()` 和 `ioctl()` Unix 例程的接口。 请参阅 *[fcntl(2)](https://manpages.debian.org/fcntl(2))* 和 *[ioctl(2)](https://manpages.debian.org/ioctl(2))* Unix 手册页了解详情。

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): Unix, not WASI.

​	本模块的所有函数都接受文件描述符 *fd* 作为第一个参数。可以是一个整数形式的文件描述符，比如 `sys.stdin.fileno()` 的返回结果，或为 [`io.IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 对象，比如 `sys.stdin` 提供一个 [`fileno()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.fileno)，可返回一个真正的文件描述符。

*在 3.3 版本发生变更:* 本模块的操作以前触发的是 [`IOError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#IOError)，现在则会触发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError)。

*在 3.8 版本发生变更:* `fcntl` 模块现在包含 `F_ADD_SEALS`, `F_GET_SEALS` 和 `F_SEAL_*` 常量用于 [`os.memfd_create()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.memfd_create) 文件描述符的封包。

*在 3.9 版本发生变更:* 在 macOS 上，`fcntl` 模块暴露了 `F_GETPATH` 常量，它可从文件描述符获取文件的路径。 在 Linux(>=3.15) 上，`fcntl` 模块暴露了 `F_OFD_GETLK`, `F_OFD_SETLK` 和 `F_OFD_SETLKW` 常量，它们将在处理打开文件描述锁时被使用。

*在 3.10 版本发生变更:* 在 Linux >= 2.6.11 中，`fcntl` 模块暴露了 `F_GETPIPE_SZ` 和 `F_SETPIPE_SZ` 常量，它们分别允许检查和修改管道的大小。

*在 3.11 版本发生变更:* 在 FreeBSD 上，`fcntl` 模块会暴露 `F_DUP2FD` 和 `F_DUP2FD_CLOEXEC` 常量，它们允许复制文件描述符，后者还额外设置了 `FD_CLOEXEC` 旗标。

*在 3.12 版本发生变更:* 在 Linux >= 4.5 上，[`fcntl`](https://docs.python.org/zh-cn/3.13/library/fcntl.html#module-fcntl) 模块将公开 `FICLONE` 和 `FICLONERANGE` 常量，这允许在某些系统上（例如 btrfs, OCFS2, 和 XFS）通过将一个文件引用链接到另一个文件来共享某些数据。 此行为通常被称为“写入时拷贝”。

*在 3.13 版本发生变更:* 在 Linux >= 2.6.32 上，`fcntl` 模块会暴露 `F_GETOWN_EX`, `F_SETOWN_EX`, `F_OWNER_TID`, `F_OWNER_PID`, `F_OWNER_PGRP` 常量，它们允许针对特定线程、进程或进程组的直接 I/O 可用性信号。 在 Linux >= 4.13 上，`fcntl` 模块会暴露 `F_GET_RW_HINT`, `F_SET_RW_HINT`, `F_GET_FILE_RW_HINT`, `F_SET_FILE_RW_HINT` 和 `RWH_WRITE_LIFE_*` 常量，它们允许向内核通知有关在给定 inode 上或通过特定的打开文件描述符写入的相对预计生命期。 在 Linux >= 5.1 和 NetBSD 上，`fcntl` 模块会暴露 `F_SEAL_FUTURE_WRITE` 常量供 `F_ADD_SEALS` 和 `F_GET_SEALS` 操作使用。 在 FreeBSD 上，`fcntl` 模块会暴露 `F_READAHEAD`, `F_ISUNIONSTACK` 和 `F_KINFO` 常量。 在 macOS 和 FreeBSD 上，`fcntl` 模块会暴露 `F_RDAHEAD` 常量。 在 NetBSD 和 AIX 上，`fcntl` 模块会暴露 `F_CLOSEM` 常量。 在 NetBSD 上，`fcntl` 模块会暴露 `F_MAXFD` 常量。 在 macOS 和 NetBSD 上，`fcntl` 模块会暴露 `F_GETNOSIGPIPE` 和 `F_SETNOSIGPIPE` 常量。

​	这个模块定义了以下函数：

## fcntl.**fcntl**(*fd*, *cmd*, *arg=0*)

​	对文件描述符 *fd* 执行 *cmd* 操作（能够提供 [`fileno()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.fileno) 方法的文件对象也可以接受）。 *cmd* 可用的值与操作系统有关，在 [`fcntl`](https://docs.python.org/zh-cn/3.13/library/fcntl.html#module-fcntl) 模块中可作为常量使用，名称与相关 C 语言头文件中的一样。参数 *arg* 可以是整数或 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 对象。若为整数值，则本函数的返回值是 C 语言 `fcntl()` 调用的整数返回值。若为字节串，则其代表一个二进制结构，比如由 [`struct.pack()`](https://docs.python.org/zh-cn/3.13/library/struct.html#struct.pack) 创建的数据。该二进制数据将被复制到一个缓冲区，缓冲区地址传给 C 调用 `fcntl()`。调用成功后的返回值位于缓冲区内，转换为一个 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 对象。返回的对象长度将与 *arg* 参数的长度相同。上限为 1024 字节。如果操作系统在缓冲区中返回的信息大于 1024 字节，很可能导致内存段冲突，或更为不易察觉的数据错误。

​	如果 `fcntl()` 调用失败，将引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError)。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `fcntl.fcntl` 并附带参数 `fd`, `cmd`, `arg`。

## fcntl.**ioctl**(*fd*, *request*, *arg=0*, *mutate_flag=True*)

​	本函数与 [`fcntl()`](https://docs.python.org/zh-cn/3.13/library/fcntl.html#fcntl.fcntl) 函数相同，只是参数的处理更加复杂。

*request* 参数的上限是 32位。[`termios`](https://docs.python.org/zh-cn/3.13/library/termios.html#module-termios) 模块中包含了可用作 *request* 参数其他常量，名称与相关 C 头文件中定义的相同。

​	参数 *arg* 可为整数、支持只读缓冲区接口的对象（如 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) ）或支持读写缓冲区接口的对象（如 [`bytearray`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytearray) ）。

​	除了最后一种情况，其他情况下的行为都与 [`fcntl()`](https://docs.python.org/zh-cn/3.13/library/fcntl.html#fcntl.fcntl) 函数一样。

​	如果传入的是个可变缓冲区，那么行为就由 *mutate_flag* 参数决定。

​	如果 *mutate_flag* 为 False，缓冲区的可变性将被忽略，行为与只读缓冲区一样，只是没有了上述 1024 字节的上限——只要传入的缓冲区能容纳操作系统放入的数据即可。

​	如果 *mutate_flag* 为 True（默认值），那么缓冲区（实际上）会传给底层的 系统调用 [`ioctl()`](https://docs.python.org/zh-cn/3.13/library/fcntl.html#fcntl.ioctl) ，其返回代码则会回传给调用它的 Python，而缓冲区的新数据则反映了 [`ioctl()`](https://docs.python.org/zh-cn/3.13/library/fcntl.html#fcntl.ioctl) 的运行结果。这里做了一点简化，因为若是给出的缓冲区少于 1024 字节，首先会被复制到一个 1024 字节长的静态缓冲区再传给 [`ioctl()`](https://docs.python.org/zh-cn/3.13/library/fcntl.html#fcntl.ioctl) ，然后把结果复制回给出的缓冲区去。

​	如果 `ioctl()` 调用失败，将引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 异常。

​	举个例子：



``` python
>>> import array, fcntl, struct, termios, os
>>> os.getpgrp()
13341
>>> struct.unpack('h', fcntl.ioctl(0, termios.TIOCGPGRP, "  "))[0]
13341
>>> buf = array.array('h', [0])
>>> fcntl.ioctl(0, termios.TIOCGPGRP, buf, 1)
0
>>> buf
array('h', [13341])
```

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `fcntl.ioctl` 并附带参数 `fd`, `request`, `arg`。

## fcntl.**flock**(*fd*, *operation*)

​	在文件描述符 *fd* 上执行加锁操作 *operation* (也接受能提供 [`fileno()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.fileno) 方法的文件对象)。 详见 Unix 手册 *[flock(2)](https://manpages.debian.org/flock(2))*。 (在某些系统中，此函数是用 `fcntl()` 模拟出来的。)

​	如果 `flock()` 调用失败，将引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 异常。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `fcntl.flock` 并附带参数 `fd`, `operation`。

## fcntl.**lockf**(*fd*, *cmd*, *len=0*, *start=0*, *whence=0*)

​	本质上是对 [`fcntl()`](https://docs.python.org/zh-cn/3.13/library/fcntl.html#fcntl.fcntl) 加锁调用的封装。*fd* 是要加解锁的文件描述符（也接受能提供 [`fileno()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.fileno) 方法的文件对象），*cmd* 是以下值之一：

## fcntl.**LOCK_UN**

​	释放一个已存在的锁 。

## fcntl.**LOCK_SH**

​	获取一个共享的锁。

## fcntl.**LOCK_EX**

​	获得一个独占的锁。

## fcntl.**LOCK_NB**

​	与其他三个 `LOCK_*` 常量中的任何一个进行位或操作，使请求不阻塞。

​	如果使用了 `LOCK_NB` ，但无法获取锁 ，则 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 将被引发 ，异常将被 *errno* 属性 设置为 [`EACCES`](https://docs.python.org/zh-cn/3.13/library/errno.html#errno.EACCES) 或 [`EAGAIN`](https://docs.python.org/zh-cn/3.13/library/errno.html#errno.EAGAIN) （取决于操作系统；为便于移植，请检查这两个值）。 至少在某些系统中，只有当文件描述符指向一个已打开供写入的文件时，才能使用:const:!LOCK_EX 。

*len* 是要锁定的字节数，*start* 是自 *whence* 开始锁定的字节偏移量，*whence* 与 [`io.IOBase.seek()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.seek) 的定义一样。

- `0` -- 相对于文件开头 ([`os.SEEK_SET`](https://docs.python.org/zh-cn/3.13/library/os.html#os.SEEK_SET))
- `1` -- 相对于当前缓冲区位置 ([`os.SEEK_CUR`](https://docs.python.org/zh-cn/3.13/library/os.html#os.SEEK_CUR))
- `2` -- 相对于文件末尾 ([`os.SEEK_END`](https://docs.python.org/zh-cn/3.13/library/os.html#os.SEEK_END))

*start* 的默认值为 0，表示从文件起始位置开始。*len* 的默认值是 0，表示加锁至文件末尾。 *whence* 的默认值也是 0。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `fcntl.lockf` 并附带参数 `fd`, `cmd`, `len`, `start`, `whence`。

​	示例（都是运行于符合 SVR4 的系统）：

```
import struct, fcntl, os

f = open(...)
rv = fcntl.fcntl(f, fcntl.F_SETFL, os.O_NDELAY)

lockdata = struct.pack('hhllhh', fcntl.F_WRLCK, 0, 0, 0, 0, 0)
rv = fcntl.fcntl(f, fcntl.F_SETLKW, lockdata)
```

​	注意，在第一个例子中，返回值变量 *rv* 将存有整数；在第二个例子中，该变量中将存有一个 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 对象。*lockdata* 变量的结构布局视系统而定——因此采用 [`flock()`](https://docs.python.org/zh-cn/3.13/library/fcntl.html#fcntl.flock) 调用可能会更好。

> 参见
>
> 模块 [`os`](https://docs.python.org/zh-cn/3.13/library/os.html#module-os)
>
> ​	如果加锁旗标 [`O_SHLOCK`](https://docs.python.org/zh-cn/3.13/library/os.html#os.O_SHLOCK) 和 [`O_EXLOCK`](https://docs.python.org/zh-cn/3.13/library/os.html#os.O_EXLOCK) 存在于 [`os`](https://docs.python.org/zh-cn/3.13/library/os.html#module-os) 模块中（仅 BSD 专属），则 [`os.open()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.open) 函数提供了对 [`lockf()`](https://docs.python.org/zh-cn/3.13/library/fcntl.html#fcntl.lockf) 和 [`flock()`](https://docs.python.org/zh-cn/3.13/library/fcntl.html#fcntl.flock) 函数的替代。
