+++
title = "异常"
date = 2024-11-15T12:30:27+08:00
weight = 70
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/asyncio-exceptions.html](https://docs.python.org/zh-cn/3.13/library/asyncio-exceptions.html)
>
> 收录该文档的时间：`2024-11-15T12:30:27+08:00`

# 异常

**源代码:** [Lib/asyncio/exceptions.py](https://github.com/python/cpython/tree/3.13/Lib/asyncio/exceptions.py)

------

## *exception* asyncio.**TimeoutError**

[`TimeoutError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TimeoutError) 的一个已被弃用的别名，会在操作超出了给定的时限时引发。

*在 3.11 版本发生变更:* 这个类是 [`TimeoutError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TimeoutError) 的别名。

## *exception* asyncio.**CancelledError**

​	该操作已被取消。

​	取消asyncio任务时，可以捕获此异常以执行自定义操作。在几乎所有情况下，都必须重新引发异常。

*在 3.8 版本发生变更:* [`CancelledError`](https://docs.python.org/zh-cn/3.13/library/asyncio-exceptions.html#asyncio.CancelledError) 现在是 [`BaseException`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BaseException) 的子类而不是 [`Exception`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#Exception) 的子类。

## *exception* asyncio.**InvalidStateError**

[`Task`](https://docs.python.org/zh-cn/3.13/library/asyncio-task.html#asyncio.Task) 或 [`Future`](https://docs.python.org/zh-cn/3.13/library/asyncio-future.html#asyncio.Future) 的内部状态无效。

​	在为已设置结果值的未来对象设置结果值等情况下，可以引发此问题。

## *exception* asyncio.**SendfileNotAvailableError**

​	"sendfile" 系统调用不适用于给定的套接字或文件类型。

​	子类 [`RuntimeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#RuntimeError) 。

## *exception* asyncio.**IncompleteReadError**

​	请求的读取操作未完全完成。

​	由 [asyncio stream APIs](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio-streams) 提出

​	此异常是 [`EOFError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#EOFError) 的子类。

## **expected**

​	预期字节的总数（ [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int) ）。

## **partial**

​	到达流结束之前读取的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 字符串。

## *exception* asyncio.**LimitOverrunError**

​	在查找分隔符时达到缓冲区大小限制。

​	由 [asyncio stream APIs](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio-streams) 提出

## **consumed**

​	要消耗的字节总数。
