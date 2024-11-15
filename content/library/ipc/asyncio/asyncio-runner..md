+++
title = "运行器"
date = 2024-11-15T12:30:27+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/asyncio-runner.html](https://docs.python.org/zh-cn/3.13/library/asyncio-runner.html)
>
> 收录该文档的时间：`2024-11-15T12:30:27+08:00`

# 运行器

**源代码:** [Lib/asyncio/runners.py](https://github.com/python/cpython/tree/3.13/Lib/asyncio/runners.py)

​	本节将简述用于运行异步代码的高层级异步原语。

​	它们构建于 [事件循环](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio-event-loop) 之上，其目标是简化针对常见通用场景的异步代码的用法。

- [运行 asyncio 程序](https://docs.python.org/zh-cn/3.13/library/asyncio-runner.html#running-an-asyncio-program)
- [运行器上下文管理器](https://docs.python.org/zh-cn/3.13/library/asyncio-runner.html#runner-context-manager)
- [处理键盘中断](https://docs.python.org/zh-cn/3.13/library/asyncio-runner.html#handling-keyboard-interruption)

## [运行 asyncio 程序](https://docs.python.org/zh-cn/3.13/library/asyncio-runner.html#id2)

## asyncio.**run**(*coro*, ***, *debug=None*, *loop_factory=None*)

​	执行 [coroutine](https://docs.python.org/zh-cn/3.13/glossary.html#term-coroutine) *coro* 并返回结果。

​	此函数会运行传入的协程，负责管理 asyncio 事件循环，*终结异步生成器*，并关闭执行器。

​	当有其他 asyncio 事件循环在同一线程中运行时，此函数不能被调用。

​	如果 *debug* 为 `True`，事件循环将运行于调试模式。 `False` 将显式地禁用调试模式。 使用 `None` 将沿用全局 [Debug 模式](https://docs.python.org/zh-cn/3.13/library/asyncio-dev.html#asyncio-debug-mode) 设置。

​	如果 *loop_factory* 不为 `None`，它将被用来创建一个新的事件循环；否则将会使用 [`asyncio.new_event_loop()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.new_event_loop)。 最终该循环将被关闭。 此函数应当被用作 asyncio 程序的主入口点，在理想情况下应当只被调用一次。 建议使用 *loop_factory* 来配置事件循环而不是使用策略。 传入 [`asyncio.EventLoop`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.EventLoop) 将允许不带策略系统地运行 asyncio。

​	执行器的关闭有 5 分钟的超时限制。 如果执行器未在时限之内结束，将发出警告消息并关闭执行器。

​	示例：

```
async def main():
    await asyncio.sleep(1)
    print('hello')

asyncio.run(main())
```

*Added in version 3.7.*

*在 3.9 版本发生变更:* 更新为使用 [`loop.shutdown_default_executor()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.loop.shutdown_default_executor)。

*在 3.10 版本发生变更:* 默认情况下 *debug* 为 `None` 即沿用全局调试模式设置。

*在 3.12 版本发生变更:* 增加了 *loop_factory* 形参。

## [运行器上下文管理器](https://docs.python.org/zh-cn/3.13/library/asyncio-runner.html#id3)

## *class* asyncio.**Runner**(***, *debug=None*, *loop_factory=None*)

​	对在相同上下文中 *多个* 异步函数调用进行简化的上下文管理器。

​	有时多个最高层级异步函数应当在同一个 [事件循环](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio-event-loop) 和 [`contextvars.Context`](https://docs.python.org/zh-cn/3.13/library/contextvars.html#contextvars.Context) 中被调用。

​	如果 *debug* 为 `True`，事件循环将运行于调试模式。 `False` 将显式地禁用调试模式。 使用 `None` 将沿用全局 [Debug 模式](https://docs.python.org/zh-cn/3.13/library/asyncio-dev.html#asyncio-debug-mode) 设置。

*loop_factory* 可被用来重载循环的创建。 *loop_factory* 要负责将所创建的循环设置为当前事件循环。 在默认情况下如果 *loop_factory* 为 `None` 则会使用 [`asyncio.new_event_loop()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.new_event_loop) 并通过 [`asyncio.set_event_loop()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.set_event_loop) 将其设置为当前事件循环。

​	基本上，[`asyncio.run()`](https://docs.python.org/zh-cn/3.13/library/asyncio-runner.html#asyncio.run) 示例可以通过运行器的用法来重写:

```
async def main():
    await asyncio.sleep(1)
    print('hello')

with asyncio.Runner() as runner:
    runner.run(main())
```

*Added in version 3.11.*

## **run**(*coro*, ***, *context=None*)

​	在嵌入的循环中运行一个 [协程](https://docs.python.org/zh-cn/3.13/glossary.html#term-coroutine) *coro*。

​	返回协程的结果或者引发其异常。

​	可选的仅限关键字参数 *context* 允许指定一个自定义 [`contextvars.Context`](https://docs.python.org/zh-cn/3.13/library/contextvars.html#contextvars.Context) 用作 *coro* 运行所在的上下文。 如果为 `None` 则会使用运行器的默认上下文。

​	当有其他 asyncio 事件循环在同一线程中运行时，此函数不能被调用。

## **close**()

​	关闭运行器。

​	最终化异步生成器，停止默认执行器，关闭事件循环并释放嵌入的 [`contextvars.Context`](https://docs.python.org/zh-cn/3.13/library/contextvars.html#contextvars.Context)。

## **get_loop**()

​	返回关联到运行器实例的事件循环。

​	备注

 

[`Runner`](https://docs.python.org/zh-cn/3.13/library/asyncio-runner.html#asyncio.Runner) 会使用惰性初始化策略，它的构造器不会初始化下层的低层级结构体。

​	嵌入的 *loop* 和 *context* 是在进入 [`with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#with) 语句体或者对 [`run()`](https://docs.python.org/zh-cn/3.13/library/asyncio-runner.html#asyncio.run) 或 [`get_loop()`](https://docs.python.org/zh-cn/3.13/library/asyncio-runner.html#asyncio.Runner.get_loop) 的首次调用时被创建的。

## [处理键盘中断](https://docs.python.org/zh-cn/3.13/library/asyncio-runner.html#id4)

*Added in version 3.11.*

​	当 [`signal.SIGINT`](https://docs.python.org/zh-cn/3.13/library/signal.html#signal.SIGINT) 被 Ctrl-C 引发时，默认将在主线程中引发 [`KeyboardInterrupt`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#KeyboardInterrupt)。 但是这并不适用于 [`asyncio`](https://docs.python.org/zh-cn/3.13/library/asyncio.html#module-asyncio) 因为它可以中断异步的内部操作并能挂起要退出的程序。

​	为解决此问题，[`asyncio`](https://docs.python.org/zh-cn/3.13/library/asyncio.html#module-asyncio) 将按以下步骤处理 [`signal.SIGINT`](https://docs.python.org/zh-cn/3.13/library/signal.html#signal.SIGINT):

1. [`asyncio.Runner.run()`](https://docs.python.org/zh-cn/3.13/library/asyncio-runner.html#asyncio.Runner.run) 在任何用户代码被执行之前安装一个自定义的 [`signal.SIGINT`](https://docs.python.org/zh-cn/3.13/library/signal.html#signal.SIGINT) 处理器并在从该函数退出时将其移除。
2. [`Runner`](https://docs.python.org/zh-cn/3.13/library/asyncio-runner.html#asyncio.Runner) 为所传入的协程创建主任务供其执行。creates the main task for the passed coroutine for its execution.
3. 当 [`signal.SIGINT`](https://docs.python.org/zh-cn/3.13/library/signal.html#signal.SIGINT) 被 Ctrl-C 引发时，自定义的信号处理器将通过调用 [`asyncio.Task.cancel()`](https://docs.python.org/zh-cn/3.13/library/asyncio-task.html#asyncio.Task.cancel) 在主任务内部引发 [`asyncio.CancelledError`](https://docs.python.org/zh-cn/3.13/library/asyncio-exceptions.html#asyncio.CancelledError) 来取消主任务。 这将导致 Python 栈回退，`try/except` 和 `try/finally` 代码块可被用于资源清理。 在主任务被取消之后，[`asyncio.Runner.run()`](https://docs.python.org/zh-cn/3.13/library/asyncio-runner.html#asyncio.Runner.run) 将引发 [`KeyboardInterrupt`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#KeyboardInterrupt)。
4. 用户可以编写无法通过 [`asyncio.Task.cancel()`](https://docs.python.org/zh-cn/3.13/library/asyncio-task.html#asyncio.Task.cancel) 来中断的紧密循环，在这种情况下后续的第二次 Ctrl-C 将立即引发 [`KeyboardInterrupt`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#KeyboardInterrupt) 而不会取消主任务。
