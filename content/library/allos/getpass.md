+++
title = "getpass --- 可移植的密码输入"
date = 2024-11-15T12:09:25+08:00
weight = 70
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/getpass.html](https://docs.python.org/zh-cn/3.13/library/getpass.html)
>
> 收录该文档的时间：`2024-11-15T12:09:25+08:00`

# `getpass` --- 可移植的密码输入

**源代码:** [Lib/getpass.py](https://github.com/python/cpython/tree/3.13/Lib/getpass.py)

------

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): not WASI.

​	此模块在 WebAssembly 平台上无效或不可用。 请参阅 [WebAssembly 平台](https://docs.python.org/zh-cn/3.13/library/intro.html#wasm-availability) 了解详情。

[`getpass`](https://docs.python.org/zh-cn/3.13/library/getpass.html#module-getpass) 模块提供了两个函数：

## getpass.**getpass**(*prompt='Password: '*, *stream=None*)

​	提示用户输入一个密码且不会回显。 用户会看到字符串 *prompt* 作为提示，其默认值为 `'Password: '`。 在 Unix 上，如有必要提示会使用替换错误句柄写入到文件型对象 *stream*。 *stream* 默认指向控制终端 (`/dev/tty`)，如果不可用则指向 `sys.stderr` (此参数在 Windows 上会被忽略)。

​	如果回显自由输入不可用则 getpass() 将回退为打印一条警告消息到 *stream* 并且从 `sys.stdin` 读取同时发出 [`GetPassWarning`](https://docs.python.org/zh-cn/3.13/library/getpass.html#getpass.GetPassWarning)。

​	备注

 

​	如果你从 IDLE 内部调用 getpass，输入可能是在你启动 IDLE 的终端中而非在 IDLE 窗口本身中完成。

## *exception* getpass.**GetPassWarning**

​	一个当密码输入可能被回显时发出的 [`UserWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#UserWarning) 子类。

## getpass.**getuser**()

​	返回用户的“登录名称”。

​	此函数会按顺序检查环境变量 `LOGNAME`, `USER`, `LNAME` 和 `USERNAME`，并返回其中第一个被设为非空字符串的值。 如果全都未设置，则在支持 [`pwd`](https://docs.python.org/zh-cn/3.13/library/pwd.html#module-pwd) 模块的系统上将返回来自密码数据库的登录名，在其他情况下，将会引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError)。

​	通常情况下，此函数应优先于 [`os.getlogin()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.getlogin)。

*在 3.13 版本发生变更:* 在之前版本中，会引发包括 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 在内的多种异常。
