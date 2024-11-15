+++
title = "posix --- 最常见的 POSIX 系统调用"
date = 2024-11-15T21:30:54+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/posix.html](https://docs.python.org/zh-cn/3.13/library/posix.html)
>
> 收录该文档的时间：`2024-11-15T21:30:54+08:00`

# `posix` --- 最常见的 POSIX 系统调用

------

​	此模块提供了对基于 C 标准和 POSIX 标准（一种稍加修改的 Unix 接口）进行标准化的系统功能的访问。

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): Unix.

**请勿直接导入此模块。** 而应导入 [`os`](https://docs.python.org/zh-cn/3.13/library/os.html#module-os) 模块，它提供了此接口的 *可移植* 版本。 在 Unix 上，[`os`](https://docs.python.org/zh-cn/3.13/library/os.html#module-os) 模块提供了 [`posix`](https://docs.python.org/zh-cn/3.13/library/posix.html#module-posix) 接口的一个超集。 在非 Unix 操作系统上 [`posix`](https://docs.python.org/zh-cn/3.13/library/posix.html#module-posix) 模块将不可用，但会通过 [`os`](https://docs.python.org/zh-cn/3.13/library/os.html#module-os) 接口提供它的一个可用子集。 一旦导入了 [`os`](https://docs.python.org/zh-cn/3.13/library/os.html#module-os)，用它替代 [`posix`](https://docs.python.org/zh-cn/3.13/library/posix.html#module-posix) 时就 *没有* 性能惩罚。 此外，[`os`](https://docs.python.org/zh-cn/3.13/library/os.html#module-os) 还提供了一些附加功能，例如在 `os.environ` 中的某个条目被修改时会自动调用 [`putenv()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.putenv)。

​	错误将作为异常被报告；对于类型错误会给出普通异常，而系统调用所报告的异常则会引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError)。



## 大文件支持

​	某些操作系统（包括 AIX 和 Solaris）可对 int 和 long 为 32 位值的 C 编程模型提供大于 2 GiB 文件的支持。 这在通常情况下是以将相关数据的大小和偏移量类型定义为 64 位值的方式来实现的。 这样的文件有时被称为 *大文件*。

​	Python 中的大文件支持会在 `off_t` 的大小超过 long 且 long long 的大小至少与 `off_t` 一样时被启用。 要启用此模式可能必须在启用特定编译旗标的情况下配置和编译 Python。 例如，在 Solaris 2.6 和 2.7 中你需要执行这样的操作:

```
CFLAGS="`getconf LFS_CFLAGS`" OPT="-g -O2 $CFLAGS" \
        ./configure
```

​	在支持大文件的 Linux 系统中，可以这样做:

```
CFLAGS='-D_LARGEFILE64_SOURCE -D_FILE_OFFSET_BITS=64' OPT="-g -O2 $CFLAGS" \
        ./configure
```



## 重要的模块内容

​	除了 [`os`](https://docs.python.org/zh-cn/3.13/library/os.html#module-os) 模块文档已说明的许多函数，[`posix`](https://docs.python.org/zh-cn/3.13/library/posix.html#module-posix) 还定义了下列数据项:

## posix.**environ**

​	一个表示解释器启动时间点的字符串环境的字典。 键和值的类型在Unix 上为 bytes 而在 Windows 上为 str。 例如，`environ[b'HOME']` (Windows 上的 `environ['HOME']`) 是你的家目录的路径名，等价于 C 中的 `getenv("HOME")`。

​	修改此字典不会影响由 [`execv()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.execv), [`popen()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.popen) 或 [`system()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.system) 所传入的字符串环境；如果你需要修改环境，请将 `environ` 传给 [`execve()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.execve) 或者为 [`system()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.system) 或 [`popen()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.popen) 的命令字符串添加变量赋值和 export 语句。

*在 3.2 版本发生变更:* 在 Unix 上，键和值为 bytes 类型。

​	备注

 

[`os`](https://docs.python.org/zh-cn/3.13/library/os.html#module-os) 模块提供了对 `environ` 的替代实现，它会在被修改时更新环境。 还要注意更新 [`os.environ`](https://docs.python.org/zh-cn/3.13/library/os.html#os.environ) 将导致此字典失效。 推荐使用这个 [`os`](https://docs.python.org/zh-cn/3.13/library/os.html#module-os) 模块版本而不是直接访问 [`posix`](https://docs.python.org/zh-cn/3.13/library/posix.html#module-posix) 模块。
