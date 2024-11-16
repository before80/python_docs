+++
title = "netrc --- netrc 文件处理"
date = 2024-11-15T12:03:23+08:00
weight = 30
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/netrc.html](https://docs.python.org/zh-cn/3.13/library/netrc.html)
>
> 收录该文档的时间：`2024-11-15T12:03:23+08:00`

# `netrc` --- netrc 文件处理

**源代码:** [Lib/netrc.py](https://github.com/python/cpython/tree/3.13/Lib/netrc.py)

------

[`netrc`]({{< ref "/library/fileformats/netrc#netrc.netrc" >}}) 类解析并封装了 Unix 的 **ftp** 程序和其他 FTP 客户端所使用的 netrc 文件格式。

## *class* netrc.**netrc**([*file*])

[`netrc`]({{< ref "/library/fileformats/netrc#netrc.netrc" >}}) 的实例或其子类的实例会被用来封装来自 netrc 文件的数据。 如果有初始化参数，它将指明要解析的文件。 如果未给出参数，则位于用户家目录的 `.netrc` 文件 -- 即 [`os.path.expanduser()`]({{< ref "/library/filesys/os_path#os.path.expanduser" >}}) 所确定的文件 -- 将会被读取。 在其他情况下，则将引发 [`FileNotFoundError`]({{< ref "/library/exceptions#FileNotFoundError" >}}) 异常。 解析错误将引发 [`NetrcParseError`]({{< ref "/library/fileformats/netrc#netrc.NetrcParseError" >}}) 并附带诊断信息，包括文件名、行号以及终止令牌。 如果在 POSIX 系统上未指明参数，则当 `.netrc` 文件中有密码时，如果文件归属或权限不安全（归属的用户不是运行进程的用户，或者可供任何其他用户读取或写入）将引发 [`NetrcParseError`]({{< ref "/library/fileformats/netrc#netrc.NetrcParseError" >}})。 这实现了与 ftp 和其他使用 `.netrc` 的程序同等的安全行为。

*在 3.4 版本发生变更:* 添加了 POSIX 权限检查。

*在 3.7 版本发生变更:* 当未将 *file* 作为参数传入时会使用 [`os.path.expanduser()`]({{< ref "/library/filesys/os_path#os.path.expanduser" >}}) 来查找 `.netrc` 文件的位置。

*在 3.10 版本发生变更:* [`netrc`]({{< ref "/library/fileformats/netrc#module-netrc" >}}) 会在使用语言区域专属的编码格式之前先尝试 UTF-8 编码格式。 netrc 文件中的条目不再需要包括所有凭据。 缺失的凭据值默认将为空字符串。 所有的凭据及其值现在可以包含任意字符，如空格和非 ASCII 字符。 如果登录名为 anonymous，它将不会触发安全检查。

## *exception* netrc.**NetrcParseError**

​	当源代码文本中出现语法错误时由 [`netrc`]({{< ref "/library/fileformats/netrc#netrc.netrc" >}}) 类所引发的异常。 该异常的实例提供了三个有用的属性:

## **msg**

​	错误的解释性文本。

## **filename**

​	源代码文件名。

## **lineno**

​	发现错误所在的行号。



## netrc 对象

[`netrc`]({{< ref "/library/fileformats/netrc#netrc.netrc" >}}) 实例具有下列方法:

## netrc.**authenticators**(*host*)

​	针对 *host* 的身份验证者返回一个 3 元组 `(login, account, password)`。 如果 netrc 文件不包含针对给定主机的条目，则返回关联到 'default' 条目的元组。 如果匹配的主机或默认条目均不可用，则返回 `None`。

## netrc.**__repr__**()

​	将类数据以 netrc 文件的格式转储为一个字符串。 （这会丢弃注释并可能重排条目顺序。）

[`netrc`]({{< ref "/library/fileformats/netrc#netrc.netrc" >}}) 的实例具有一些公共实例变量:

## netrc.**hosts**

​	将主机名映射到 `(login, account, password)` 元组的字典。 如果存在 'default' 条目，则会表示为使用该名称的伪主机。

## netrc.**macros**

​	将宏名称映射到字符串列表的字典。
