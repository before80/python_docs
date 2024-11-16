+++
title = "扩展"
date = 2024-11-15T12:30:27+08:00
weight = 130
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/asyncio-extending.html](https://docs.python.org/zh-cn/3.13/library/asyncio-extending.html)
>
> 收录该文档的时间：`2024-11-15T12:30:27+08:00`

# 扩展

[`asyncio`]({{< ref "/library/ipc/asyncio#module-asyncio" >}}) 扩展的主要方向是编写自定义的 *事件循环* 类。 asyncio 具有可以被用来简化此任务的辅助工具。

​	备注

 

​	第三方应当小心谨慎地重用现有的异步代码，新的 Python 版本可以自由地打破 API 的 *内部* 部分的向下兼容性。

## 编写自定义事件循环

[`asyncio.AbstractEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.AbstractEventLoop" >}}) 声明了大量的方法。 从头开始全部实现它们将是一件烦琐的工作。

​	一个事件循环可以通过从 `asyncio.BaseEventLoop` 继承来自动地获得许多常用方法的实现。

​	相应地，继承者应当实现多个在 `asyncio.BaseEventLoop` 中已声明但未实现的 *私有* 方法。

​	例如，`loop.create_connection()` 会检查参数，解析 DNS 地址，并调用应当由继承方类来实现的 `loop._make_socket_transport()`。 `_make_socket_transport()` 方法未被写入文档并被视为 *内部* API。

## Future 和 Task 私有构造器

[`asyncio.Future`]({{< ref "/library/ipc/asyncio/asyncio-future#asyncio.Future" >}}) 和 [`asyncio.Task`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}}) 不应该被直接实例化，请使用对应的 [`loop.create_future()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_future" >}}), [`loop.create_task()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_task" >}}) 或 [`asyncio.create_task()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.create_task" >}}) 工厂函数。

​	但是，第三方 *事件循环* 可能会 *重用* 内置的 Future 和 Task 实现以自动获得复杂且高度优化的代码。

​	出于这个目的，下面列出了相应的 *私有* 构造器:

## Future.**__init__**(***, *loop=None*)

​	创建一个内置的 Future 实例。

*loop* 是一个可选的事件循环实例。

## Task.**__init__**(*coro*, ***, *loop=None*, *name=None*, *context=None*)

​	创建一个内置的 Task 实例。

*loop* 是一个可选的事件循环实例。 其余参数会在 [`loop.create_task()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_task" >}}) 说明中加以描述。

*在 3.11 版本发生变更:* 添加了 *context* 参数。

## Task 生命周期支持

​	第三方任务实现应当调用下列函数以使任务对 [`asyncio.all_tasks()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.all_tasks" >}}) 和 [`asyncio.current_task()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.current_task" >}}) 可见:

## asyncio.**_register_task**(*task*)

​	注册一个新的 *task* 并由 *asyncio* 管理。

​	调用来自任务构造器的函数。

## asyncio.**_unregister_task**(*task*)

​	从 *asyncio* 内置结构体中注销 *task*。

​	此函数应当在任务将要结束时被调用。

## asyncio.**_enter_task**(*loop*, *task*)

​	将当前任务切换为 *task* 参数。

​	在执行嵌入的 *coroutine* ([`coroutine.send()`]({{< ref "/reference/datamodel#coroutine.send" >}}) 或 [`coroutine.throw()`]({{< ref "/reference/datamodel#coroutine.throw" >}})) 的一部分之前调用此函数。

## asyncio.**_leave_task**(*loop*, *task*)

​	将当前任务从 *task* 切换回 `None`。

​	在 [`coroutine.send()`]({{< ref "/reference/datamodel#coroutine.send" >}}) 或 [`coroutine.throw()`]({{< ref "/reference/datamodel#coroutine.throw" >}}) 执行之后调用此函数。
