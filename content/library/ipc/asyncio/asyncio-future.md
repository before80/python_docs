+++
title = "Futures"
date = 2024-11-15T12:30:27+08:00
weight = 90
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/asyncio-future.html](https://docs.python.org/zh-cn/3.13/library/asyncio-future.html)
>
> 收录该文档的时间：`2024-11-15T12:30:27+08:00`

# Futures

**源代码:** [Lib/asyncio/futures.py](https://github.com/python/cpython/tree/3.13/Lib/asyncio/futures.py), [Lib/asyncio/base_futures.py](https://github.com/python/cpython/tree/3.13/Lib/asyncio/base_futures.py)

------

*Future* 对象用来链接 **底层回调式代码** 和高层异步/等待式代码。

## Future 函数

## asyncio.**isfuture**(*obj*)

​	如果 *obj* 为下面任意对象，返回 `True`：

- 一个 [`asyncio.Future`](https://docs.python.org/zh-cn/3.13/library/asyncio-future.html#asyncio.Future) 类的实例，
- 一个 [`asyncio.Task`](https://docs.python.org/zh-cn/3.13/library/asyncio-task.html#asyncio.Task) 类的实例，
- 带有 `_asyncio_future_blocking` 属性的类似 Future 的对象。

> Added in version 3.5.
>

## asyncio.**ensure_future**(*obj*, ***, *loop=None*)

​	返回：

- *obj* 参数会是保持原样，如果 *obj* 是 [`Future`](https://docs.python.org/zh-cn/3.13/library/asyncio-future.html#asyncio.Future)、 [`Task`](https://docs.python.org/zh-cn/3.13/library/asyncio-task.html#asyncio.Task) 或 类似 Future 的对象( [`isfuture()`](https://docs.python.org/zh-cn/3.13/library/asyncio-future.html#asyncio.isfuture) 用于测试。)
- 封装了 *obj* 的 [`Task`](https://docs.python.org/zh-cn/3.13/library/asyncio-task.html#asyncio.Task) 对象，如果 *obj* 是一个协程 (使用 [`iscoroutine()`](https://docs.python.org/zh-cn/3.13/library/asyncio-task.html#asyncio.iscoroutine) 进行检测)；在此情况下该协程将通过 `ensure_future()` 加入执行计划。
- 等待 *obj* 的 [`Task`](https://docs.python.org/zh-cn/3.13/library/asyncio-task.html#asyncio.Task) 对象，如果 *obj* 是一个可等待对象( [`inspect.isawaitable()`](https://docs.python.org/zh-cn/3.13/library/inspect.html#inspect.isawaitable) 用于测试)

​	如果 *obj* 不是上述对象会引发一个 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError) 异常。

​	重要

 

​	查看 [`create_task()`](https://docs.python.org/zh-cn/3.13/library/asyncio-task.html#asyncio.create_task) 函数，它是创建新任务的首选途径。

​	保存一个指向此函数的结果的引用，以避免任务在执行期间消失。

*在 3.5.1 版本发生变更:* 这个函数接受任意 [awaitable](https://docs.python.org/zh-cn/3.13/glossary.html#term-awaitable) 对象。

*自 3.10 版本弃用:* 如果 *obj* 不是 Future 类对象同时未指定 *loop* 并且没有正在运行的事件循环则会发出弃用警告。

## asyncio.**wrap_future**(*future*, ***, *loop=None*)

​	将一个 [`concurrent.futures.Future`](https://docs.python.org/zh-cn/3.13/library/concurrent.futures.html#concurrent.futures.Future) 对象封装到 [`asyncio.Future`](https://docs.python.org/zh-cn/3.13/library/asyncio-future.html#asyncio.Future) 对象中。

*自 3.10 版本弃用:* 如果 *future* 不是 Future 类对象同时未指定 *loop* 并且没有正在运行的事件循环则会发出弃用警告。

## Future 对象

## *class* asyncio.**Future**(***, *loop=None*)

​	一个 Future 代表一个异步运算的最终结果。线程不安全。

​	Future 是一个 [awaitable](https://docs.python.org/zh-cn/3.13/glossary.html#term-awaitable) 对象。 协程可以等待 Future 对象直到它们有结果或设置了异常，或者直到它们被取消。 一个 Future 可被等待多次并且结果相同。

​	通常 Future 用于支持底层回调式代码(例如在协议实现中使用asyncio [transports](https://docs.python.org/zh-cn/3.13/library/asyncio-protocol.html#asyncio-transports-protocols)) 与高层异步/等待式代码交互。

​	经验告诉我们永远不要面向用户的接口暴露 Future 对象，同时建议使用 [`loop.create_future()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.loop.create_future) 来创建 Future 对象。这种方法可以让 Future 对象使用其它的事件循环实现，它可以注入自己的优化实现。

*在 3.7 版本发生变更:* 加入对 [`contextvars`](https://docs.python.org/zh-cn/3.13/library/contextvars.html#module-contextvars) 模块的支持。

*自 3.10 版本弃用:* 如果未指定 *loop* 并且没有正在运行的事件循环则会发出弃用警告。

## **result**()

​	返回 Future 的结果。

​	如果 Future 状态为 *完成* ，并由 [`set_result()`](https://docs.python.org/zh-cn/3.13/library/asyncio-future.html#asyncio.Future.set_result) 方法设置一个结果，则返回这个结果。

​	如果 Future 状态为 *完成* ，并由 [`set_exception()`](https://docs.python.org/zh-cn/3.13/library/asyncio-future.html#asyncio.Future.set_exception) 方法设置一个异常，那么这个方法会引发异常。

​	如果 Future 已 *取消*，方法会引发一个 [`CancelledError`](https://docs.python.org/zh-cn/3.13/library/asyncio-exceptions.html#asyncio.CancelledError) 异常。

​	如果 Future 的结果还不可用，此方法会引发一个 [`InvalidStateError`](https://docs.python.org/zh-cn/3.13/library/asyncio-exceptions.html#asyncio.InvalidStateError) 异常。

## **set_result**(*result*)

​	将 Future 标记为 *完成* 并设置结果。

​	如果 Future 已经 *完成* 则抛出一个 [`InvalidStateError`](https://docs.python.org/zh-cn/3.13/library/asyncio-exceptions.html#asyncio.InvalidStateError) 错误。

## **set_exception**(*exception*)

​	将 Future 标记为 *完成* 并设置一个异常。

​	如果 Future 已经 *完成* 则抛出一个 [`InvalidStateError`](https://docs.python.org/zh-cn/3.13/library/asyncio-exceptions.html#asyncio.InvalidStateError) 错误。

## **done**()

​	如果 Future 为已 *完成* 则返回 `True` 。

​	如果 Future 为 *取消* 或调用 [`set_result()`](https://docs.python.org/zh-cn/3.13/library/asyncio-future.html#asyncio.Future.set_result) 设置了结果或调用 [`set_exception()`](https://docs.python.org/zh-cn/3.13/library/asyncio-future.html#asyncio.Future.set_exception) 设置了异常，那么它就是 *完成* 。

## **cancelled**()

​	如果 Future 已 *取消* 则返回 `True`

​	这个方法通常在设置结果或异常前用来检查 Future 是否已 *取消* 。

```
if not fut.cancelled():
    fut.set_result(42)
```

## **add_done_callback**(*callback*, ***, *context=None*)

​	添加一个在 Future *完成* 时运行的回调函数。

​	调用 *callback* 时，Future 对象是它的唯一参数。

​	如果调用这个方法时 Future 已经 *完成*，回调函数会被 [`loop.call_soon()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.loop.call_soon) 调度。

​	可选键值类的参数 *context* 允许 *callback* 运行在一个指定的自定义 [`contextvars.Context`](https://docs.python.org/zh-cn/3.13/library/contextvars.html#contextvars.Context) 对象中。如果没有提供 *context* ，则使用当前上下文。

​	可以用 [`functools.partial()`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.partial) 给回调函数传递参数，例如:

```
# 当 "fut" 已完成时则调用 'print("Future:", fut)'。
fut.add_done_callback(
    functools.partial(print, "Future:"))
```

*在 3.7 版本发生变更:* 加入键值类形参 *context*。请参阅 [**PEP 567**](https://peps.python.org/pep-0567/) 查看更多细节。

## **remove_done_callback**(*callback*)

​	从回调列表中移除 *callback* 。

​	返回被移除的回调函数的数量，通常为1，除非一个回调函数被添加多次。

## **cancel**(*msg=None*)

​	取消 Future 并调度回调函数。

​	如果 Future 已经 *完成* 或 *取消* ，返回 `False` 。否则将 Future 状态改为 *取消* 并在调度回调函数后返回 `True` 。

*在 3.9 版本发生变更:* 增加了 *msg* 形参。

## **exception**()

​	返回 Future 已设置的异常。

​	只有 Future 在 *完成* 时才返回异常（或者 `None` ，如果没有设置异常）。

​	如果 Future 已 *取消*，方法会引发一个 [`CancelledError`](https://docs.python.org/zh-cn/3.13/library/asyncio-exceptions.html#asyncio.CancelledError) 异常。

​	如果 Future 还没 *完成* ，这个方法会引发一个 [`InvalidStateError`](https://docs.python.org/zh-cn/3.13/library/asyncio-exceptions.html#asyncio.InvalidStateError) 异常。

## **get_loop**()

​	返回 Future 对象已绑定的事件循环。

> Added in version 3.7.
>

​	这个例子创建一个 Future 对象，创建和调度一个异步任务去设置 Future 结果，然后等待其结果:

```
async def set_after(fut, delay, value):
    # 休眠 *delay* 秒。
    await asyncio.sleep(delay)

    # 将 *value* 设为 Future 对象 *fut* 的结果。
    fut.set_result(value)

async def main():
    # 获取当前事件循环。
    loop = asyncio.get_running_loop()

    # 新建一个 Future 对象。
    fut = loop.create_future()

    # 在一个并行的任务中运行 "set_after()" 协程。
    # 在这里我们使用低层级的 "loop.create_task()" API
    # 因为我们手头已经拥有一个对事件循环的引用。
    # 在其他情况下我们可以使用 "asyncio.create_task()"。
    loop.create_task(
        set_after(fut, 1, '... world'))

    print('hello ...')

    # 等待直到 *fut* 得出结果 (1 秒) 并打印它。
    print(await fut)

asyncio.run(main())
```

​	重要

 

​	该 Future 对象是为了模仿 [`concurrent.futures.Future`](https://docs.python.org/zh-cn/3.13/library/concurrent.futures.html#concurrent.futures.Future) 类。主要差异包含：

- 与 asyncio 的 Future 不同，[`concurrent.futures.Future`](https://docs.python.org/zh-cn/3.13/library/concurrent.futures.html#concurrent.futures.Future) 实例不是可等待对象。
- [`asyncio.Future.result()`](https://docs.python.org/zh-cn/3.13/library/asyncio-future.html#asyncio.Future.result) 和 [`asyncio.Future.exception()`](https://docs.python.org/zh-cn/3.13/library/asyncio-future.html#asyncio.Future.exception) 不接受 *timeout* 参数。
- Future 没有 *完成* 时 [`asyncio.Future.result()`](https://docs.python.org/zh-cn/3.13/library/asyncio-future.html#asyncio.Future.result) 和 [`asyncio.Future.exception()`](https://docs.python.org/zh-cn/3.13/library/asyncio-future.html#asyncio.Future.exception) 抛出一个 [`InvalidStateError`](https://docs.python.org/zh-cn/3.13/library/asyncio-exceptions.html#asyncio.InvalidStateError) 异常。
- 使用 [`asyncio.Future.add_done_callback()`](https://docs.python.org/zh-cn/3.13/library/asyncio-future.html#asyncio.Future.add_done_callback) 注册的回调函数不会立即调用，而是被 [`loop.call_soon()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.loop.call_soon) 调度。
- asyncio Future 不能兼容 [`concurrent.futures.wait()`](https://docs.python.org/zh-cn/3.13/library/concurrent.futures.html#concurrent.futures.wait) 和 [`concurrent.futures.as_completed()`](https://docs.python.org/zh-cn/3.13/library/concurrent.futures.html#concurrent.futures.as_completed) 函数。
- [`asyncio.Future.cancel()`](https://docs.python.org/zh-cn/3.13/library/asyncio-future.html#asyncio.Future.cancel) 接受一个可选的 `msg` 参数，但 [`concurrent.futures.Future.cancel()`](https://docs.python.org/zh-cn/3.13/library/concurrent.futures.html#concurrent.futures.Future.cancel) 无此参数。
