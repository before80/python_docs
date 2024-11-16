+++
title = "平台支持"
date = 2024-11-15T12:30:27+08:00
weight = 120
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/asyncio-platforms.html](https://docs.python.org/zh-cn/3.13/library/asyncio-platforms.html)
>
> 收录该文档的时间：`2024-11-15T12:30:27+08:00`

# 平台支持

[`asyncio`]({{< ref "/library/ipc/asyncio#module-asyncio" >}}) 模块被设计为可移植的,但由于平台的底层架构和功能，一些平台存在细微的差异和限制。

## 所有平台

- [`loop.add_reader()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.add_reader" >}}) 和 [`loop.add_writer()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.add_writer" >}}) 不能用来监视文件I/O。

## Windows

**源代码:** [Lib/asyncio/proactor_events.py](https://github.com/python/cpython/tree/3.13/Lib/asyncio/proactor_events.py), [Lib/asyncio/windows_events.py](https://github.com/python/cpython/tree/3.13/Lib/asyncio/windows_events.py), [Lib/asyncio/windows_utils.py](https://github.com/python/cpython/tree/3.13/Lib/asyncio/windows_utils.py)

------

> 在 3.8 版本发生变更: 在 Windows 上，[`ProactorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.ProactorEventLoop" >}}) 现在是默认的事件循环。

​	Windows上的所有事件循环都不支持以下方法:

- 不支持 [`loop.create_unix_connection()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_unix_connection" >}}) 和 [`loop.create_unix_server()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_unix_server" >}})。 [`socket.AF_UNIX`]({{< ref "/library/ipc/socket#socket.AF_UNIX" >}}) 套接字族是 Unix 专属的。
- 不支持 [`loop.add_signal_handler()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.add_signal_handler" >}}) 和 [`loop.remove_signal_handler()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.remove_signal_handler" >}}) 。

[`SelectorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.SelectorEventLoop" >}}) 有下列限制:

- [`SelectSelector`]({{< ref "/library/ipc/selectors#selectors.SelectSelector" >}}) 只被用于等待套接字事件：它支持套接字且最多支持512个套接字。
- [`loop.add_reader()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.add_reader" >}}) 和 [`loop.add_writer()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.add_writer" >}}) 只接受套接字处理回调函数(如管道、文件描述符等都不支持)。
- 因为不支持管道，所以 [`loop.connect_read_pipe()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.connect_read_pipe" >}}) 和 [`loop.connect_write_pipe()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.connect_write_pipe" >}}) 方法没有实现。
- 不支持 [Subprocesses]({{< ref "/library/ipc/asyncio/asyncio-subprocess#asyncio-subprocess" >}}) ，也就是 [`loop.subprocess_exec()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.subprocess_exec" >}}) 和 [`loop.subprocess_shell()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.subprocess_shell" >}}) 方法没有实现。

[`ProactorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.ProactorEventLoop" >}}) 有下列限制:

- 不支持 [`loop.add_reader()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.add_reader" >}}) 和 [`loop.add_writer()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.add_writer" >}}) 方法。

​	通常 Windows 上单调时钟的分辨率约为 15.6 毫秒。 最佳分辨率是 0.5 毫秒。 分辨率依赖于具体的硬件 ([HPET](https://en.wikipedia.org/wiki/High_Precision_Event_Timer) 的可用性) 和 Windows 的设置。



### Windows的子进程支持

​	在 Windows 上，默认的事件循环 [`ProactorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.ProactorEventLoop" >}}) 支持子进程，而 [`SelectorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.SelectorEventLoop" >}}) 则不支持。

​	也不支持 [`policy.set_child_watcher()`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.AbstractEventLoopPolicy.set_child_watcher" >}}) 函数，[`ProactorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.ProactorEventLoop" >}}) 有不同的机制来监视子进程。

## macOS

​	完整支持流行的macOS版本。

​	macOS <= 10.8

​	在 macOS 10.6, 10.7 和 10.8 上，默认的事件循环使用 [`selectors.KqueueSelector`]({{< ref "/library/ipc/selectors#selectors.KqueueSelector" >}})，在这些版本上它并不支持字符设备。 可以手工配置 [`SelectorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.SelectorEventLoop" >}}) 来使用 [`SelectSelector`]({{< ref "/library/ipc/selectors#selectors.SelectSelector" >}}) 或 [`PollSelector`]({{< ref "/library/ipc/selectors#selectors.PollSelector" >}}) 以在这些较老版本的 macOS 上支持字符设备。 例如:

```
import asyncio
import selectors

selector = selectors.SelectSelector()
loop = asyncio.SelectorEventLoop(selector)
asyncio.set_event_loop(loop)
```
