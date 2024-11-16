+++
title = "异常"
date = 2024-11-15T12:30:27+08:00
weight = 70
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/asyncio-exceptions.html](https://docs.python.org/zh-cn/3.13/library/asyncio-exceptions.html)
>
> 收录该文档的时间：`2024-11-15T12:30:27+08:00`

# 异常

**源代码:** [Lib/asyncio/exceptions.py](https://github.com/python/cpython/tree/3.13/Lib/asyncio/exceptions.py)

------

## *exception* asyncio.**TimeoutError**

[`TimeoutError`]({{< ref "/library/exceptions#TimeoutError" >}}) 的一个已被弃用的别名，会在操作超出了给定的时限时引发。

> 在 3.11 版本发生变更: 这个类是 [`TimeoutError`]({{< ref "/library/exceptions#TimeoutError" >}}) 的别名。

## *exception* asyncio.**CancelledError**

​	该操作已被取消。

​	取消asyncio任务时，可以捕获此异常以执行自定义操作。在几乎所有情况下，都必须重新引发异常。

> 在 3.8 版本发生变更: [`CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}}) 现在是 [`BaseException`]({{< ref "/library/exceptions#BaseException" >}}) 的子类而不是 [`Exception`]({{< ref "/library/exceptions#Exception" >}}) 的子类。

## *exception* asyncio.**InvalidStateError**

[`Task`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}}) 或 [`Future`]({{< ref "/library/ipc/asyncio/asyncio-future#asyncio.Future" >}}) 的内部状态无效。

​	在为已设置结果值的未来对象设置结果值等情况下，可以引发此问题。

## *exception* asyncio.**SendfileNotAvailableError**

​	"sendfile" 系统调用不适用于给定的套接字或文件类型。

​	子类 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}}) 。

## *exception* asyncio.**IncompleteReadError**

​	请求的读取操作未完全完成。

​	由 [asyncio stream APIs]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio-streams" >}}) 提出

​	此异常是 [`EOFError`]({{< ref "/library/exceptions#EOFError" >}}) 的子类。

## **expected**

​	预期字节的总数（ [`int`]({{< ref "/library/functions#int" >}}) ）。

## **partial**

​	到达流结束之前读取的 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 字符串。

## *exception* asyncio.**LimitOverrunError**

​	在查找分隔符时达到缓冲区大小限制。

​	由 [asyncio stream APIs]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio-streams" >}}) 提出

## **consumed**

​	要消耗的字节总数。
