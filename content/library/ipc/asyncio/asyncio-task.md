+++
title = "协程与任务"
date = 2024-11-15T12:30:27+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/asyncio-task.html](https://docs.python.org/zh-cn/3.13/library/asyncio-task.html)
>
> 收录该文档的时间：`2024-11-15T12:30:27+08:00`

# 协程与任务

​	本节将简述用于协程与任务的高层级 API。

- [协程]({{< ref "/library/ipc/asyncio/asyncio-task#coroutines" >}})
- [可等待对象]({{< ref "/library/ipc/asyncio/asyncio-task#awaitables" >}})
- [创建任务]({{< ref "/library/ipc/asyncio/asyncio-task#creating-tasks" >}})
- [任务取消]({{< ref "/library/ipc/asyncio/asyncio-task#task-cancellation" >}})
- [任务组]({{< ref "/library/ipc/asyncio/asyncio-task#task-groups" >}})
- [休眠]({{< ref "/library/ipc/asyncio/asyncio-task#sleeping" >}})
- [并发运行任务]({{< ref "/library/ipc/asyncio/asyncio-task#running-tasks-concurrently" >}})
- [主动任务工厂]({{< ref "/library/ipc/asyncio/asyncio-task#eager-task-factory" >}})
- [屏蔽取消操作]({{< ref "/library/ipc/asyncio/asyncio-task#shielding-from-cancellation" >}})
- [超时]({{< ref "/library/ipc/asyncio/asyncio-task#timeouts" >}})
- [简单等待]({{< ref "/library/ipc/asyncio/asyncio-task#waiting-primitives" >}})
- [在线程中运行]({{< ref "/library/ipc/asyncio/asyncio-task#running-in-threads" >}})
- [跨线程调度]({{< ref "/library/ipc/asyncio/asyncio-task#scheduling-from-other-threads" >}})
- [内省]({{< ref "/library/ipc/asyncio/asyncio-task#introspection" >}})
- [Task 对象]({{< ref "/library/ipc/asyncio/asyncio-task#task-object" >}})



## [协程]({{< ref "/library/ipc/asyncio/asyncio-task#id3" >}})

*源码：* [Lib/asyncio/coroutines.py](https://github.com/python/cpython/tree/3.13/Lib/asyncio/coroutines.py)

------

​	通过 async/await 语法来声明 [协程]({{< ref "/glossary/idx#term-coroutine" >}}) 是编写 asyncio 应用的推荐方式。 例如，以下代码段会打印 "hello"，等待 1 秒，再打印 "world":



``` python
>>> import asyncio

>>> async def main():
...     print('hello')
...     await asyncio.sleep(1)
...     print('world')

>>> asyncio.run(main())
hello
world
```

​	注意：简单地调用一个协程并不会使其被调度执行



``` python
>>> main()
<coroutine object main at 0x1053bb7c8>
```

​	要实际运行一个协程，asyncio 提供了以下几种机制:

- [`asyncio.run()`]({{< ref "/library/ipc/asyncio/asyncio-runner#asyncio.run" >}}) 函数用来运行最高层级的入口点 "main()" 函数 (参见上面的示例。)

- 对协程执行 await。以下代码段会在等待 1 秒后打印 "hello"，然后 *再次* 等待 2 秒后打印 "world":

  ```
  import asyncio
  import time
  
  async def say_after(delay, what):
      await asyncio.sleep(delay)
      print(what)
  
  async def main():
      print(f"started at {time.strftime('%X')}")
  
      await say_after(1, 'hello')
      await say_after(2, 'world')
  
      print(f"finished at {time.strftime('%X')}")
  
  asyncio.run(main())
  ```

  预期的输出:

  ```
  started at 17:13:52
  hello
  world
  finished at 17:13:55
  ```

- [`asyncio.create_task()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.create_task" >}}) 函数用来并发运行作为 asyncio [`任务`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}}) 的多个协程。

  让我们修改以上示例，*并发* 运行两个 `say_after` 协程:

  ```
  async def main():
      task1 = asyncio.create_task(
          say_after(1, 'hello'))
  
      task2 = asyncio.create_task(
          say_after(2, 'world'))
  
      print(f"started at {time.strftime('%X')}")
  
      # 等待直到两个任务都完成
      # （会花费约 2 秒钟。）
      await task1
      await task2
  
      print(f"finished at {time.strftime('%X')}")
  ```

  注意，预期的输出显示代码段的运行时间比之前快了 1 秒:

  ```
  started at 17:14:32
  hello
  world
  finished at 17:14:34
  ```

- [`asyncio.TaskGroup`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.TaskGroup" >}}) 类提供了 [`create_task()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.create_task" >}}) 的更现代化的替代。 使用此 API，之前的例子将变为:

  ```
  async def main():
      async with asyncio.TaskGroup() as tg:
          task1 = tg.create_task(
              say_after(1, 'hello'))
  
          task2 = tg.create_task(
              say_after(2, 'world'))
  
          print(f"started at {time.strftime('%X')}")
  
      # 当存在上下文管理器时 await 是隐式执行的。
  
      print(f"finished at {time.strftime('%X')}")
  ```

  用时和输出结果应当与之前的版本相同。

  *Added in version 3.11:* [`asyncio.TaskGroup`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.TaskGroup" >}})。



## [可等待对象]({{< ref "/library/ipc/asyncio/asyncio-task#id4" >}})

​	如果一个对象可以在 [`await`]({{< ref "/reference/expressions#await" >}}) 语句中使用，那么它就是 **可等待** 对象。许多 asyncio API 都被设计为接受可等待对象。

*可等待* 对象有三种主要类型: **协程**, **任务** 和 **Future**.

​	协程

​	Python 协程属于 *可等待* 对象，因此可以在其他协程中被等待:

```
import asyncio

async def nested():
    return 42

async def main():
    # Nothing happens if we just call "nested()".
    # A coroutine object is created but not awaited,
    # so it *won't run at all*.
    nested()  # will raise a "RuntimeWarning".

    # Let's do it differently now and await it:
    print(await nested())  # will print "42".

asyncio.run(main())
```

​	重要

 

​	在本文档中 "协程" 可用来表示两个紧密关联的概念:

- *协程函数*: 定义形式为 [`async def`]({{< ref "/reference/compound_stmts#async-def" >}}) 的函数;
- *协程对象*: 调用 *协程函数* 所返回的对象。

​	任务

*任务* 被用来“并行的”调度协程

​	当一个协程通过 [`asyncio.create_task()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.create_task" >}}) 等函数被封装为一个 *任务*，该协程会被自动调度执行:

```
import asyncio

async def nested():
    return 42

async def main():
    # Schedule nested() to run soon concurrently
    # with "main()".
    task = asyncio.create_task(nested())

    # "task" can now be used to cancel "nested()", or
    # can simply be awaited to wait until it is complete:
    await task

asyncio.run(main())
```

​	Futures

[`Future`]({{< ref "/library/ipc/asyncio/asyncio-future#asyncio.Future" >}}) 是一种特殊的 **低层级** 可等待对象，表示一个异步操作的 **最终结果**。

​	当一个 Future 对象 *被等待*，这意味着协程将保持等待直到该 Future 对象在其他地方操作完毕。

​	在 asyncio 中需要 Future 对象以便允许通过 async/await 使用基于回调的代码。

​	通常情况下 **没有必要** 在应用层级的代码中创建 Future 对象。

​	Future 对象有时会由库和某些 asyncio API 暴露给用户，用作可等待对象:

```
async def main():
    await function_that_returns_a_future_object()

    # this is also valid:
    await asyncio.gather(
        function_that_returns_a_future_object(),
        some_python_coroutine()
    )
```

​	一个很好的返回对象的低层级函数的示例是 [`loop.run_in_executor()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.run_in_executor" >}})。

## [创建任务]({{< ref "/library/ipc/asyncio/asyncio-task#id5" >}})

**源码：** [Lib/asyncio/tasks.py](https://github.com/python/cpython/tree/3.13/Lib/asyncio/tasks.py)

------

## asyncio.**create_task**(*coro*, ***, *name=None*, *context=None*)

​	将 *coro* [协程]({{< ref "/library/ipc/asyncio/asyncio-task#coroutine" >}}) 封装为一个 [`Task`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}}) 并调度其执行。返回 Task 对象。

*name* 不为 `None`，它将使用 [`Task.set_name()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.set_name" >}}) 来设为任务的名称。

​	可选的 *context* 参数允许指定自定义的 [`contextvars.Context`]({{< ref "/library/concurrency/contextvars#contextvars.Context" >}}) 供 *coro* 运行。 当未提供 *context* 时将创建当前上下文的副本。

​	该任务会在 [`get_running_loop()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.get_running_loop" >}}) 返回的循环中执行，如果当前线程没有在运行的循环则会引发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。

​备注
 

[`asyncio.TaskGroup.create_task()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.TaskGroup.create_task" >}}) 是一个平衡了结构化并发的新选择；它允许等待一组相关任务并具有极强的安全保证。

​	重要

 

​	保存一个指向此函数的结果的引用，以避免任务在执行过程中消失。 事件循环将只保留对任务的弱引用。 未在其他地方被引用的任务可能在任何时候被作为垃圾回收，即使是在它被完成之前。 如果需要可靠的“发射后不用管”后台任务，请将它们放到一个多项集中:

```
background_tasks = set()

for i in range(10):
    task = asyncio.create_task(some_coro(param=i))

    # Add task to the set. This creates a strong reference.
    background_tasks.add(task)

    # To prevent keeping references to finished tasks forever,
    # make each task remove its own reference from the set after
    # completion:
    task.add_done_callback(background_tasks.discard)
```

> Added in version 3.7.
>

> 在 3.8 版本发生变更: 增加了 *name* 形参。

> 在 3.11 版本发生变更: 增加了 *context* 形参。

## [任务取消]({{< ref "/library/ipc/asyncio/asyncio-task#id6" >}})

​	任务可以便捷和安全地取消。 当任务被取消时，[`asyncio.CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}}) 将在遇到机会时在任务中被引发。

​	推荐协程使用 `try/finally` 代码块来可靠地执行清理逻辑。 对于 [`asyncio.CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}}) 被显式捕获的情况，它通常应当在清理完成时被传播。 [`asyncio.CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}}) 会直接子类化 [`BaseException`]({{< ref "/library/exceptions#BaseException" >}}) 因此大多数代码都不需要关心这一点。

​	启用结构化并发的 asyncio 组件，如 [`asyncio.TaskGroup`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.TaskGroup" >}}) 和 [`asyncio.timeout()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.timeout" >}})，在内部是使用撤销操作来实现的因而在协程屏蔽了 [`asyncio.CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}}) 时可能无法正常工作。 类似地，用户代码通常也不应调用 [`uncancel`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.uncancel" >}})。 但是，在确实想要屏蔽 [`asyncio.CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}}) 的情况下，则还有必要调用 `uncancel()` 来完全移除撤销状态。



## [任务组]({{< ref "/library/ipc/asyncio/asyncio-task#id7" >}})

​	任务组合并了一套用于等待分组中所有任务完成的方便可靠方式的任务创建 API。

## *class* asyncio.**TaskGroup**

​	持有一个任务分组的 [异步上下文管理器]({{< ref "/reference/datamodel#async-context-managers" >}})。 可以使用 [`create_task()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.create_task" >}}) 将任务添加到分组中。 当该上下文管理器退出时所有任务都将被等待。

> Added in version 3.11.
>

## **create_task**(*coro*, ***, *name=None*, *context=None*)

​	在该任务组中创建一个任务。 签名与 [`asyncio.create_task()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.create_task" >}}) 的签名相匹配。 如果该任务组未激活（例如尚未进入、已经结束或在关闭过程中），我们将关闭所给出的 `coro`。

> 在 3.13 版本发生变更: 如果任务组未激活则关闭所给出的协程。

​	示例:

```
async def main():
    async with asyncio.TaskGroup() as tg:
        task1 = tg.create_task(some_coro(...))
        task2 = tg.create_task(another_coro(...))
    print(f"Both tasks have completed now: {task1.result()}, {task2.result()}")
```

`async with` 语句将等待分组中的所有任务结束。 在等待期间，仍可将新任务添加到分组中 (例如，通过将 `tg` 传入某个协程并在该协程中调用 `tg.create_task()`)。 一旦最后的任务完成并退出 `async with` 代码块，将无法再向分组添加新任务。

​	当首次有任何属于分组的任务因 [`asyncio.CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}}) 以外的异常而失败时，分组中的剩余任务将被取消。 在此之后将无法添加更多任务到该分组中。 在这种情况下，如果 `async with` 语句体仍然为激活状态（即 [`__aexit__()`]({{< ref "/reference/datamodel#object.__aexit__" >}}) 尚未被调用），则直接包含 `async with` 语句的任务也会被取消。 结果 [`asyncio.CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}}) 将中断一个 `await`，但它将不会跳出包含的 `async with` 语句。

​	一旦所有任务被完成，如果有任何任务因 [`asyncio.CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}}) 以外的异常而失败，这些异常会被组合在 [`ExceptionGroup`]({{< ref "/library/exceptions#ExceptionGroup" >}}) 或 [`BaseExceptionGroup`]({{< ref "/library/exceptions#BaseExceptionGroup" >}}) 中（选择其中较适合的一个；参见其文档）并将随后引发。

​	两个基础异常会被特别对待：如果有任何任务因 [`KeyboardInterrupt`]({{< ref "/library/exceptions#KeyboardInterrupt" >}}) 或 [`SystemExit`]({{< ref "/library/exceptions#SystemExit" >}}) 而失败，任务分组仍然会取消剩余的任务并等待它们，但随后初始 [`KeyboardInterrupt`]({{< ref "/library/exceptions#KeyboardInterrupt" >}}) 或 [`SystemExit`]({{< ref "/library/exceptions#SystemExit" >}}) 而不是 [`ExceptionGroup`]({{< ref "/library/exceptions#ExceptionGroup" >}}) 或 [`BaseExceptionGroup`]({{< ref "/library/exceptions#BaseExceptionGroup" >}}) 会被重新引发。

​	如果 `async with` 语句体因异常而退出（这样将调用 [`__aexit__()`]({{< ref "/reference/datamodel#object.__aexit__" >}}) 并附带一个异常），此种情况会与有任务失败时一样对待：剩余任务将被取消然后被等待，而非取消类异常会被加入到一个异常分组并被引发。 传入到 [`__aexit__()`]({{< ref "/reference/datamodel#object.__aexit__" >}}) 的异常，除了 [`asyncio.CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}}) 以外，也都会被包括在该异常分组中。 同样的特殊对待也适用于上一段所说的 [`KeyboardInterrupt`]({{< ref "/library/exceptions#KeyboardInterrupt" >}}) 和 [`SystemExit`]({{< ref "/library/exceptions#SystemExit" >}})。

​	对于任务组应当注意不要将用于“唤醒”其 [`__aexit__()`]({{< ref "/reference/datamodel#object.__aexit__" >}}) 的内部取消请求与其他地方对其运行的任务提出的取消请求相混淆。 具体来说，当一个任务组在语法上嵌套于另一个任务组中，而两个任务组的某个子任务同时发生异常时，内层的任务组将处理其异常，然后外层的任务组将收到另一个取消请求并处理它自己的异常。

​	对于任务组在外部被取消同时必须引发 [`ExceptionGroup`]({{< ref "/library/exceptions#ExceptionGroup" >}}) 的情况，它将调用父任务的 [`cancel()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.cancel" >}}) 方法。 这样可以确保 [`asyncio.CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}}) 会在下一次 [`await`]({{< ref "/reference/expressions#await" >}}) 时被引发，因此取消操作不会丢失。

​	任务组将保留 [`asyncio.Task.cancelling()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.cancelling" >}}) 所报告的取消次数。

> 在 3.13 版本发生变更: 改进了同时处理内部和外部取消操作以及正确保留取消计数的功能。

### 终结一个任务组

​	While terminating a task group is not natively supported by the standard library, termination can be achieved by adding an exception-raising task to the task group and ignoring the raised exception:

```
import asyncio
from asyncio import TaskGroup

class TerminateTaskGroup(Exception):
    """Exception raised to terminate a task group."""

async def force_terminate_task_group():
    """Used to force termination of a task group."""
    raise TerminateTaskGroup()

async def job(task_id, sleep_time):
    print(f'Task {task_id}: start')
    await asyncio.sleep(sleep_time)
    print(f'Task {task_id}: done')

async def main():
    try:
        async with TaskGroup() as group:
            # spawn some tasks
            group.create_task(job(1, 0.5))
            group.create_task(job(2, 1.5))
            # sleep for 1 second
            await asyncio.sleep(1)
            # add an exception-raising task to force the group to terminate
            group.create_task(force_terminate_task_group())
    except* TerminateTaskGroup:
        pass

asyncio.run(main())
```

​	期待的输出:

```
Task 1: start
Task 2: start
Task 1: done
```

## [休眠]({{< ref "/library/ipc/asyncio/asyncio-task#id8" >}})

## *coroutine* asyncio.**sleep**(*delay*, *result=None*)

​	阻塞 *delay* 指定的秒数。

​	如果指定了 *result*，则当协程完成时将其返回给调用者。

`sleep()` 总是会挂起当前任务，以允许其他任务运行。

​	将 delay 设为 0 将提供一个经优化的路径以允许其他任务运行。 这可供长期间运行的函数使用以避免在函数调用的全过程中阻塞事件循环。

​	以下协程示例运行 5 秒，每秒显示一次当前日期:

```
import asyncio
import datetime

async def display_date():
    loop = asyncio.get_running_loop()
    end_time = loop.time() + 5.0
    while True:
        print(datetime.datetime.now())
        if (loop.time() + 1.0) >= end_time:
            break
        await asyncio.sleep(1)

asyncio.run(display_date())
```

> 在 3.10 版本发生变更: 移除了 *loop* 形参。

> 在 3.13 版本发生变更: 如果 *delay* 不为 [`nan`]({{< ref "/library/numeric/math#math.nan" >}}) 则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

## [并发运行任务]({{< ref "/library/ipc/asyncio/asyncio-task#id9" >}})

## *awaitable* asyncio.**gather**(**aws*, *return_exceptions=False*)

*并发* 运行 *aws* 序列中的 [可等待对象]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio-awaitables" >}})。

​	如果 *aws* 中的某个可等待对象为协程，它将自动被作为一个任务调度。

​	如果所有可等待对象都成功完成，结果将是一个由所有返回值聚合而成的列表。结果值的顺序与 *aws* 中可等待对象的顺序一致。

​	如果 *return_exceptions* 为 `False` (默认)，所引发的首个异常会立即传播给等待 `gather()` 的任务。*aws* 序列中的其他可等待对象 **不会被取消** 并将继续运行。

​	如果 *return_exceptions* 为 `True`，异常会和成功的结果一样处理，并聚合至结果列表。

​	如果 `gather()` *被取消*，所有被提交 (尚未完成) 的可等待对象也会 *被取消*。

​	如果 *aws* 序列中的任一 Task 或 Future 对象 *被取消*，它将被当作引发了 [`CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}}) 一样处理 -- 在此情况下 `gather()` 调用 **不会** 被取消。这是为了防止一个已提交的 Task/Future 被取消导致其他 Tasks/Future 也被取消。

​备注
 

​	一个创建然后并发地运行任务等待它们完成的新选择是 [`asyncio.TaskGroup`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.TaskGroup" >}})。 *TaskGroup* 提供了针对调度嵌套子任务的比 *gather* 更强的安全保证：如果一个任务（或子任务，即由一个任务调度的任务）引发了异常，*TaskGroup* 将取消剩余的已排期任务）。

​	示例:

```
import asyncio

async def factorial(name, number):
    f = 1
    for i in range(2, number + 1):
        print(f"Task {name}: Compute factorial({number}), currently i={i}...")
        await asyncio.sleep(1)
        f *= i
    print(f"Task {name}: factorial({number}) = {f}")
    return f

async def main():
    # 将三个调用 *并发地* 加入计划任务：
    L = await asyncio.gather(
        factorial("A", 2),
        factorial("B", 3),
        factorial("C", 4),
    )
    print(L)

asyncio.run(main())

# 预期的输出：
#
#     Task A: Compute factorial(2), currently i=2...
#     Task B: Compute factorial(3), currently i=2...
#     Task C: Compute factorial(4), currently i=2...
#     Task A: factorial(2) = 2
#     Task B: Compute factorial(3), currently i=3...
#     Task C: Compute factorial(4), currently i=3...
#     Task B: factorial(3) = 6
#     Task C: Compute factorial(4), currently i=4...
#     Task C: factorial(4) = 24
#     [2, 6, 24]
```

​备注
 

​	如果 *return_exceptions* 为假值，则在 gather() 被标记为完成后取消它将不会取消任何已提交的可等待对象。 例如，在将一个异常传播给调用者之后，gather 可被标记为已完成，因此，在从 gather 捕获一个（由可等待对象所引发的）异常之后调用 `gather.cancel()` 将不会取消任何其他可等待对象。

> 在 3.7 版本发生变更: 如果 *gather* 本身被取消，则无论 *return_exceptions* 取值为何，消息都会被传播。

> 在 3.10 版本发生变更: 移除了 *loop* 形参。

> 自 3.10 版本弃用: 如果未提供位置参数或者并非所有位置参数均为 Future 类对象并且没有正在运行的事件循环则会发出弃用警告。



## [主动任务工厂]({{< ref "/library/ipc/asyncio/asyncio-task#id10" >}})

## asyncio.**eager_task_factory**(*loop*, *coro*, ***, *name=None*, *context=None*)

​	用于主动任务执行的任务工厂

​	当使用这个工厂函数时 (通过 [`loop.set_task_factory(asyncio.eager_task_factory)`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.set_task_factory" >}}))，协程将在 [`Task`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}}) 构造期间同步地开始执行。 任务仅会在它们阻塞时被加入事件循环上的计划任务。 这可以达成性能提升因为对同步完成的协程来说可以避免循环调度的开销。

​	此特性会带来好处的一个常见例子是应用了缓存或记忆功能以便在可能的情况避免实际 I/O 的协程。

​备注
 

​	协程是立即执行是一项语言改变。 如果协程返回或引发异常，其任务将不会被加入事件循环上的计划任务。 如果协程执行发生阻塞，其任务将被加入事件循环上的计划任务。 这项改变可能会向现有应用程序引入行为变化。 例如，应用程序的任务执行顺序可能会发生改变。

> Added in version 3.12.
>

## asyncio.**create_eager_task_factory**(*custom_task_constructor*)

​	创建一个主动型任务工厂，类似于 [`eager_task_factory()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.eager_task_factory" >}})，在创建新任务时使用所提供的 *custom_task_constructor* 而不是默认的 [`Task`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}})。

*custom_task_constructor* 必须是一个 *可调用对象*，其签名与 [`Task.__init__`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}}) 的签名相匹配。 该可调用对象必须返回一个兼容 [`asyncio.Task`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}}) 的对象。

​	此函数返回一个 *可调用对象*，将通过 [`loop.set_task_factory(factory)`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.set_task_factory" >}})) 被用作一个事件循环的任务工厂。

> Added in version 3.12.
>

## [屏蔽取消操作]({{< ref "/library/ipc/asyncio/asyncio-task#id11" >}})

## *awaitable* asyncio.**shield**(*aw*)

​	保护一个 [可等待对象]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio-awaitables" >}}) 防止其被 [`取消`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.cancel" >}})。

​	如果 *aw* 是一个协程，它将自动被作为任务调度。

​	以下语句:

```
task = asyncio.create_task(something())
res = await shield(task)
```

​	相当于:

```
res = await something()
```

*不同之处* 在于如果包含它的协程被取消，在 `something()` 中运行的任务不会被取消。从 `something()` 的角度看来，取消操作并没有发生。然而其调用者已被取消，因此 "await" 表达式仍然会引发 [`CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}})。

​	如果通过其他方式取消 `something()` (例如在其内部操作) 则 `shield()` 也会取消。

​	如果希望完全忽略取消操作 (不推荐) 则 `shield()` 函数需要配合一个 try/except 代码段，如下所示:

```
task = asyncio.create_task(something())
try:
    res = await shield(task)
except CancelledError:
    res = None
```

​	重要

 

​	保存一个传给此函数的任务的引用，以避免任务在执行过程中消失。 事件循环将只保留对任务的弱引用。 未在其他地方被引用的任务可能在任何时候被作为垃圾回收，即使是在它被完成之前。

> 在 3.10 版本发生变更: 移除了 *loop* 形参。

> 自 3.10 版本弃用: 如果 *aw* 不是 Future 类对象并且没有正在运行的事件循环则会发出弃用警告。

## [超时]({{< ref "/library/ipc/asyncio/asyncio-task#id12" >}})

## asyncio.**timeout**(*delay*)

​	返回一个可被用于限制等待某个操作所耗费时间的 [异步上下文管理器]({{< ref "/reference/datamodel#async-context-managers" >}})。

*delay* 可以为 `None`，或是一个表示等待秒数的浮点数/整数。 如果 *delay* 为 `None`，将不会应用时间限制；如果当创建上下文管理器时无法确定延时则此设置将很适用。

​	在两种情况下，该上下文管理器都可以在创建之后使用 [`Timeout.reschedule()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Timeout.reschedule" >}}) 来重新安排计划。

​	示例:

```
async def main():
    async with asyncio.timeout(10):
        await long_running_task()
```

​	如果 `long_running_task` 耗费 10 秒以上完成，该上下文管理器将取消当前任务并在内部处理所引发的 [`asyncio.CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}})，将其转化为可被捕获和处理的 [`TimeoutError`]({{< ref "/library/exceptions#TimeoutError" >}})。

​备注
 

[`asyncio.timeout()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.timeout" >}}) 上下文管理器负责将 [`asyncio.CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}}) 转化为 [`TimeoutError`]({{< ref "/library/exceptions#TimeoutError" >}})，这意味着 [`TimeoutError`]({{< ref "/library/exceptions#TimeoutError" >}}) 只能在该上下文管理器 *之外* 被捕获。

​	捕获 [`TimeoutError`]({{< ref "/library/exceptions#TimeoutError" >}}) 的示例:

```
async def main():
    try:
        async with asyncio.timeout(10):
            await long_running_task()
    except TimeoutError:
        print("The long operation timed out, but we've handled it.")

    print("This statement will run regardless.")
```

[`asyncio.timeout()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.timeout" >}}) 所产生的上下文管理器可以被重新调整到不同的终止点并执行检查。

## *class* asyncio.**Timeout**(*when*)

​	一个用于撤销已过期协程的 [异步上下文管理器]({{< ref "/reference/datamodel#async-context-managers" >}})。

`when` 应当是一个指明上下文将要过期的绝对时间，由事件循环的时钟来计时。

- 如果 `when` 为 `None`，则超时将永远不会被触发。
- 如果 `when < loop.time()`，则超时将在事件循环的下一次迭代中被触发。

> ## **when**() → [float]({{< ref "/library/functions#float" >}}) | [None]({{< ref "/library/constants#None" >}})
>
> ​	返回当前终止点，或者如果未设置当前终止点则返回 `None`。
>
> ## **reschedule**(*when: [float]({{< ref "/library/functions#float" >}}) | [None]({{< ref "/library/constants#None" >}})*)
>
> ​	重新安排超时。
>
> ## **expired**() → [bool]({{< ref "/library/functions#bool" >}})
>
> ​	返回上下文管理器是否已超出时限（过期）。

​	示例:

```
async def main():
    try:
        # We do not know the timeout when starting, so we pass ``None``.
        async with asyncio.timeout(None) as cm:
            # We know the timeout now, so we reschedule it.
            new_deadline = get_running_loop().time() + 10
            cm.reschedule(new_deadline)

            await long_running_task()
    except TimeoutError:
        pass

    if cm.expired():
        print("Looks like we haven't finished on time.")
```

​	超时上下文管理器可以被安全地嵌套。

> Added in version 3.11.
>

## asyncio.**timeout_at**(*when*)

​	类似于 [`asyncio.timeout()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.timeout" >}})，不同之处在于 *when* 是停止等待的绝对时间，或者为 `None`。

​	示例:

```
async def main():
    loop = get_running_loop()
    deadline = loop.time() + 20
    try:
        async with asyncio.timeout_at(deadline):
            await long_running_task()
    except TimeoutError:
        print("The long operation timed out, but we've handled it.")

    print("This statement will run regardless.")
```

> Added in version 3.11.
>

## *coroutine* asyncio.**wait_for**(*aw*, *timeout*)

​	等待 *aw* [可等待对象]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio-awaitables" >}}) 完成，指定 timeout 秒数后超时。

​	如果 *aw* 是一个协程，它将自动被作为任务调度。

*timeout* 可以为 `None`，也可以为 float 或 int 型数值表示的等待秒数。如果 *timeout* 为 `None`，则等待直到完成。

​	如果发生超时，将取消任务并引发 [`TimeoutError`]({{< ref "/library/exceptions#TimeoutError" >}})。

​	要避免任务 [`取消`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.cancel" >}})，可以加上 [`shield()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.shield" >}})。

​	此函数将等待直到 Future 确实被取消，所以总等待时间可能超过 *timeout*。 如果在取消期间发生了异常，异常将会被传播。

​	如果等待被取消，则 *aw* 指定的对象也会被取消。

​	示例:

```
async def eternity():
    # Sleep for one hour
    await asyncio.sleep(3600)
    print('yay!')

async def main():
    # Wait for at most 1 second
    try:
        await asyncio.wait_for(eternity(), timeout=1.0)
    except TimeoutError:
        print('timeout!')

asyncio.run(main())

# Expected output:
#
#     timeout!
```

> 在 3.7 版本发生变更: 当 *aw* 由于超时被取消时，`wait_for` 会等待 *aw* 被取消。 在之前版本中，它会立即引发 [`TimeoutError`]({{< ref "/library/exceptions#TimeoutError" >}})。

> 在 3.10 版本发生变更: 移除了 *loop* 形参。

> 在 3.11 版本发生变更: 引发 [`TimeoutError`]({{< ref "/library/exceptions#TimeoutError" >}}) 而不是 [`asyncio.TimeoutError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.TimeoutError" >}})。

## [简单等待]({{< ref "/library/ipc/asyncio/asyncio-task#id13" >}})

## *coroutine* asyncio.**wait**(*aws*, ***, *timeout=None*, *return_when=ALL_COMPLETED*)

​	并发地运行 *aws* 可迭代对象中的 [`Future`]({{< ref "/library/ipc/asyncio/asyncio-future#asyncio.Future" >}}) 和 [`Task`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}}) 实例并进入阻塞状态直到满足 *return_when* 所指定的条件。

*aws* 可迭代对象必须不为空。

​	返回两个 Task/Future 集合: `(done, pending)`。

​	用法：

```
done, pending = await asyncio.wait(aws)
```

​	如指定 *timeout* (float 或 int 类型) 则它将被用于控制返回之前等待的最长秒数。

​	请注意此函数不会引发 [`TimeoutError`]({{< ref "/library/exceptions#TimeoutError" >}})。 当超时发生时尚未完成的 Future 或 Task 会在设定的秒数后被直接返回。

*return_when* 指定此函数应在何时返回。它必须为以下常数之一:

| 常量                        | 描述                                                         |
| :-------------------------- | :----------------------------------------------------------- |
| asyncio.**FIRST_COMPLETED** | 函数将在任意可等待对象结束或取消时返回。                     |
| asyncio.**FIRST_EXCEPTION** | 该函数将在任何 future 对象通过引发异常而结束时返回。 如果没有任何 future 对象引发引发那么它将等价于 [`ALL_COMPLETED`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.ALL_COMPLETED" >}})。 |
| asyncio.**ALL_COMPLETED**   | 函数将在所有可等待对象结束或取消时返回。                     |

​	与 [`wait_for()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.wait_for" >}}) 不同，`wait()` 在超时发生时不会取消可等待对象。

> 在 3.10 版本发生变更: 移除了 *loop* 形参。

> 在 3.11 版本发生变更: 直接向 `wait()` 传入协程对象的方式已被弃用。

> 在 3.12 版本发生变更: 增加了对产生任务的生成器的支持。

## asyncio.**as_completed**(*aws*, ***, *timeout=None*)

​	并发地运行 *aws* 可迭代对象中的 [可等待对象]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio-awaitables" >}})。 返回的对象可以被迭代以获取可等待对象结束时的结果。

​	由 `as_completed()` 返回的对象可作为 [asynchronous iterator]({{< ref "/glossary/idx#term-asynchronous-iterator" >}}) 或普通的 [iterator]({{< ref "/glossary/idx#term-iterator" >}}) 被迭代。 当使用异步迭代时，原来提供的可等待对象如果为 Task 或 Future 对象则会被产出。 这样可以更容易地将之前加入计划的任务与其结果进行对应。 例如:

```
ipv4_connect = create_task(open_connection("127.0.0.1", 80))
ipv6_connect = create_task(open_connection("::1", 80))
tasks = [ipv4_connect, ipv6_connect]

async for earliest_connect in as_completed(tasks):
    # earliest_connect is done. The result can be obtained by
    # awaiting it or calling earliest_connect.result()
    reader, writer = await earliest_connect

    if earliest_connect is ipv6_connect:
        print("IPv6 connection established.")
    else:
        print("IPv4 connection established.")
```

​	在异步迭代期间，将为不属于 Task 或 Future 对象的可等待对象产出隐式创建的任务。

​	当被用作普通的迭代器时，每次迭代将产出一个返回结果的新协程或是引发下一个完成的等待对象对应的异常。 此模式将与 Python 3.13 之前的版本保持兼容:

```
ipv4_connect = create_task(open_connection("127.0.0.1", 80))
ipv6_connect = create_task(open_connection("::1", 80))
tasks = [ipv4_connect, ipv6_connect]

for next_connect in as_completed(tasks):
    # next_connect is not one of the original task objects. It must be
    # awaited to obtain the result value or raise the exception of the
    # awaitable that finishes next.
    reader, writer = await next_connect
```

​	如果在所有可等待对象完成之前达到超时限制则会引发 [`TimeoutError`]({{< ref "/library/exceptions#TimeoutError" >}})。 这将在异步迭代期间由 `async for` 循环引发或是在普通迭代期间由所产出的协程引发。

> 在 3.10 版本发生变更: 移除了 *loop* 形参。

> 自 3.10 版本弃用: 如果 *aws* 可迭代对象中的可等待对象不全为 Future 类对象并且没有正在运行的事件循环则会发出弃用警告。

> 在 3.12 版本发生变更: 增加了对产生任务的生成器的支持。

> 在 3.13 版本发生变更: 该结果现在可被用作 [asynchronous iterator]({{< ref "/glossary/idx#term-asynchronous-iterator" >}}) 或是普通的 [iterator]({{< ref "/glossary/idx#term-iterator" >}}) (在之前它只是普通的迭代器)。

## [在线程中运行]({{< ref "/library/ipc/asyncio/asyncio-task#id14" >}})

## *coroutine* asyncio.**to_thread**(*func*, */*, **args*, ***kwargs*)

​	在不同的线程中异步地运行函数 *func*。

​	向此函数提供的任何 *args 和 **kwargs 会被直接传给 *func*。 并且，当前 [`contextvars.Context`]({{< ref "/library/concurrency/contextvars#contextvars.Context" >}}) 会被传播，允许在不同的线程中访问来自事件循环的上下文变量。

​	返回一个可被等待以获取 *func* 的最终结果的协程。

​	这个协程函数主要是用于执行在其他情况下会阻塞事件循环的 IO 密集型函数/方法。 例如:

```
def blocking_io():
    print(f"start blocking_io at {time.strftime('%X')}")
    # Note that time.sleep() can be replaced with any blocking
    # IO-bound operation, such as file operations.
    time.sleep(1)
    print(f"blocking_io complete at {time.strftime('%X')}")

async def main():
    print(f"started main at {time.strftime('%X')}")

    await asyncio.gather(
        asyncio.to_thread(blocking_io),
        asyncio.sleep(1))

    print(f"finished main at {time.strftime('%X')}")


asyncio.run(main())

# Expected output:
#
# started main at 19:50:53
# start blocking_io at 19:50:53
# blocking_io complete at 19:50:54
# finished main at 19:50:54
```

​	在任何协程中直接调用 `blocking_io()` 将会在调用期间阻塞事件循环，导致额外的 1 秒运行时间。 但是，通过改用 `asyncio.to_thread()`，我们可以在单独的线程中运行它从而不会阻塞事件循环。

​备注
 

​	由于 [GIL]({{< ref "/glossary/idx#term-GIL" >}}) 的存在，`asyncio.to_thread()` 通常只能被用来将 IO 密集型函数变为非阻塞的。 但是，对于会释放 GIL 的扩展模块或无此限制的替代性 Python 实现来说，`asyncio.to_thread()` 也可被用于 CPU 密集型函数。

> Added in version 3.9.
>

## [跨线程调度]({{< ref "/library/ipc/asyncio/asyncio-task#id15" >}})

## asyncio.**run_coroutine_threadsafe**(*coro*, *loop*)

​	向指定事件循环提交一个协程。（线程安全）

​	返回一个 [`concurrent.futures.Future`]({{< ref "/library/concurrency/future/concurrent_futures#concurrent.futures.Future" >}}) 以等待来自其他 OS 线程的结果。

​	此函数应该从另一个 OS 线程中调用，而非事件循环运行所在线程。示例:

```
# Create a coroutine
coro = asyncio.sleep(1, result=3)

# Submit the coroutine to a given loop
future = asyncio.run_coroutine_threadsafe(coro, loop)

# Wait for the result with an optional timeout argument
assert future.result(timeout) == 3
```

​	如果在协程内产生了异常，将会通知返回的 Future 对象。它也可被用来取消事件循环中的任务:

```
try:
    result = future.result(timeout)
except TimeoutError:
    print('The coroutine took too long, cancelling the task...')
    future.cancel()
except Exception as exc:
    print(f'The coroutine raised an exception: {exc!r}')
else:
    print(f'The coroutine returned: {result!r}')
```

​	参见 [concurrency and multithreading]({{< ref "/library/ipc/asyncio/asyncio-dev#asyncio-multithreading" >}}) 部分的文档。

​	不同于其他 asyncio 函数，此函数要求显式地传入 *loop* 参数。

> Added in version 3.5.1.
>

## [内省]({{< ref "/library/ipc/asyncio/asyncio-task#id16" >}})

## asyncio.**current_task**(*loop=None*)

​	返回当前运行的 [`Task`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}}) 实例，如果没有正在运行的任务则返回 `None`。

​	如果 *loop* 为 `None` 则会使用 [`get_running_loop()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.get_running_loop" >}}) 获取当前事件循环。

> Added in version 3.7.
>

## asyncio.**all_tasks**(*loop=None*)

​	返回事件循环所运行的未完成的 [`Task`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}}) 对象的集合。

​	如果 *loop* 为 `None`，则会使用 [`get_running_loop()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.get_running_loop" >}}) 获取当前事件循环。

> Added in version 3.7.
>

## asyncio.**iscoroutine**(*obj*)

​	如果 *obj* 是一个协程对象则返回 `True`。

> Added in version 3.4.
>

## [Task 对象]({{< ref "/library/ipc/asyncio/asyncio-task#id17" >}})

## *class* asyncio.**Task**(*coro*, ***, *loop=None*, *name=None*, *context=None*, *eager_start=False*)

​	一个与 [`Future 类似`]({{< ref "/library/ipc/asyncio/asyncio-future#asyncio.Future" >}}) 的对象，可运行 Python [协程]({{< ref "/library/ipc/asyncio/asyncio-task#coroutine" >}})。非线程安全。

​	Task 对象被用来在事件循环中运行协程。如果一个协程在等待一个 Future 对象，Task 对象会挂起该协程的执行并等待该 Future 对象完成。当该 Future 对象 *完成*，被打包的协程将恢复执行。

​	事件循环使用协同日程调度: 一个事件循环每次运行一个 Task 对象。而一个 Task 对象会等待一个 Future 对象完成，该事件循环会运行其他 Task、回调或执行 IO 操作。

​	使用高层级的 [`asyncio.create_task()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.create_task" >}}) 函数来创建 Task 对象，也可用低层级的 [`loop.create_task()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_task" >}}) 或 [`ensure_future()`]({{< ref "/library/ipc/asyncio/asyncio-future#asyncio.ensure_future" >}}) 函数。不建议手动实例化 Task 对象。

​	要取消一个正在运行的 Task 对象可使用 [`cancel()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.cancel" >}}) 方法。调用此方法将使该 Task 对象抛出一个 [`CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}}) 异常给打包的协程。如果取消期间一个协程正在对 Future 对象执行 await，该 Future 对象也将被取消。

[`cancelled()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.cancelled" >}}) 可被用来检测 Task 对象是否被取消。如果打包的协程没有抑制 [`CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}}) 异常并且确实被取消，该方法将返回 `True`。

[`asyncio.Task`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}}) 从 [`Future`]({{< ref "/library/ipc/asyncio/asyncio-future#asyncio.Future" >}}) 继承了其除 [`Future.set_result()`]({{< ref "/library/ipc/asyncio/asyncio-future#asyncio.Future.set_result" >}}) 和 [`Future.set_exception()`]({{< ref "/library/ipc/asyncio/asyncio-future#asyncio.Future.set_exception" >}}) 以外的所有 API。

​	可选的仅限关键字参数 *context* 允许指定自定义的 [`contextvars.Context`]({{< ref "/library/concurrency/contextvars#contextvars.Context" >}}) 供 *coro* 运行。 如果未提供 *context*，Task 将拷贝当前上下文并随后在拷贝的上下文中运行其协程。

​	可选的仅限关键字参数 *eager_start* 允许在任务创建时主动开始 [`asyncio.Task`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}}) 的执行。 如果设为 `True` 并且事件循环正在运行，任务将立即开始执行协程，直到该协程第一次阻塞。 如果协程未发生阻塞即返回或引发异常，任务将主动结果并将跳过向事件循环添加计划任务。

> 在 3.7 版本发生变更: 加入对 [`contextvars`]({{< ref "/library/concurrency/contextvars#module-contextvars" >}}) 模块的支持。

> 在 3.8 版本发生变更: 增加了 *name* 形参。

> 自 3.10 版本弃用: 如果未指定 *loop* 并且没有正在运行的事件循环则会发出弃用警告。

> 在 3.11 版本发生变更: 增加了 *context* 形参。

> 在 3.12 版本发生变更: 增加了 *eager_start* 形参。

## **done**()

​	如果 Task 对象 *已完成* 则返回 `True`。

​	当 Task 所封包的协程返回一个值、引发一个异常或 Task 本身被取消时，则会被认为 *已完成*。

## **result**()

​	返回 Task 的结果。

​	如果 Task 对象 *已完成*，其封包的协程的结果会被返回 (或者当协程引发异常时，该异常会被重新引发。)

​	如果 Task 对象 *被取消*，此方法会引发一个 [`CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}}) 异常。

​	如果 Task 对象的结果还不可用，此方法会引发一个 [`InvalidStateError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.InvalidStateError" >}}) 异常。

## **exception**()

​	返回 Task 对象的异常。

​	如果所封包的协程引发了一个异常，该异常将被返回。如果所封包的协程正常返回则该方法将返回 `None`。

​	如果 Task 对象 *被取消*，此方法会引发一个 [`CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}}) 异常。

​	如果 Task 对象尚未 *完成*，此方法将引发一个 [`InvalidStateError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.InvalidStateError" >}}) 异常。

## **add_done_callback**(*callback*, ***, *context=None*)

​	添加一个回调，将在 Task 对象 *完成* 时被运行。

​	此方法应该仅在低层级的基于回调的代码中使用。

​	要了解更多细节请查看 [`Future.add_done_callback()`]({{< ref "/library/ipc/asyncio/asyncio-future#asyncio.Future.add_done_callback" >}}) 的文档。

## **remove_done_callback**(*callback*)

​	从回调列表中移除 *callback* 。

​	此方法应该仅在低层级的基于回调的代码中使用。

​	要了解更多细节请查看 [`Future.remove_done_callback()`]({{< ref "/library/ipc/asyncio/asyncio-future#asyncio.Future.remove_done_callback" >}}) 的文档。

## **get_stack**(***, *limit=None*)

​	返回此 Task 对象的栈框架列表。

​	如果所封包的协程未完成，这将返回其挂起所在的栈。如果协程已成功完成或被取消，这将返回一个空列表。如果协程被一个异常终止，这将返回回溯框架列表。

​	框架总是从按从旧到新排序。

​	每个被挂起的协程只返回一个栈框架。

​	可选的 *limit* 参数指定返回框架的数量上限；默认返回所有框架。返回列表的顺序要看是返回一个栈还是一个回溯：栈返回最新的框架，回溯返回最旧的框架。(这与 traceback 模块的行为保持一致。)

## **print_stack**(***, *limit=None*, *file=None*)

​	打印此 Task 对象的栈或回溯。

​	此方法产生的输出类似于 traceback 模块通过 [`get_stack()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.get_stack" >}}) 所获取的框架。

*limit* 参数会直接传递给 [`get_stack()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.get_stack" >}})。

*file* 参数是输出所写入的 I/O 流；在默认情况下输出会写入到 [`sys.stdout`]({{< ref "/library/python/sys#sys.stdout" >}})。

## **get_coro**()

​	返回由 [`Task`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}}) 包装的协程对象。

​备注
 

​	这对于已经主动完成的任务将返回 `None`。 参见 [主动任务工厂]({{< ref "/library/ipc/asyncio/asyncio-task#eager-task-factory" >}})。

> Added in version 3.8.
>

> 在 3.12 版本发生变更: 新增加的主动任务执行意味着结果可能为 `None`。

## **get_context**()

​	返回关联到该任务的 [`contextvars.Context`]({{< ref "/library/concurrency/contextvars#contextvars.Context" >}}) 对象。

> Added in version 3.12.
>

## **get_name**()

​	返回 Task 的名称。

​	如果没有一个 Task 名称被显式地赋值，默认的 asyncio Task 实现会在实例化期间生成一个默认名称。

> Added in version 3.8.
>

## **set_name**(*value*)

​	设置 Task 的名称。

*value* 参数可以为任意对象，它随后会被转换为字符串。

​	在默认的 Task 实现中，名称将在任务对象的 [`repr()`]({{< ref "/library/functions#repr" >}}) 输出中可见。

> Added in version 3.8.
>

## **cancel**(*msg=None*)

​	请求取消 Task 对象。

​	这将安排在下一轮事件循环中抛出一个 [`CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}}) 异常给被封包的协程。

​	协程随后将有机会进行清理甚至通过 [`try`]({{< ref "/reference/compound_stmts#try" >}}) ... ... `except CancelledError` ... [`finally`]({{< ref "/reference/compound_stmts#finally" >}}) 代码块抑制异常来拒绝请求。 因此，不同于 [`Future.cancel()`]({{< ref "/library/ipc/asyncio/asyncio-future#asyncio.Future.cancel" >}}), [`Task.cancel()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.cancel" >}}) 不保证 Task 会被取消，虽然完全抑制撤销并不常见也很不建议这样做。 但是如果协程决定要抑制撤销，那么它需要额外调用 [`Task.uncancel()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.uncancel" >}}) 来捕获异常。

> 在 3.9 版本发生变更: 增加了 *msg* 形参。

> 在 3.11 版本发生变更: `msg` 形参将从被取消的任务传播到其等待方。

​	以下示例演示了协程是如何侦听取消请求的:

```
async def cancel_me():
    print('cancel_me(): before sleep')

    try:
        # Wait for 1 hour
        await asyncio.sleep(3600)
    except asyncio.CancelledError:
        print('cancel_me(): cancel sleep')
        raise
    finally:
        print('cancel_me(): after sleep')

async def main():
    # Create a "cancel_me" Task
    task = asyncio.create_task(cancel_me())

    # Wait for 1 second
    await asyncio.sleep(1)

    task.cancel()
    try:
        await task
    except asyncio.CancelledError:
        print("main(): cancel_me is cancelled now")

asyncio.run(main())

# Expected output:
#
#     cancel_me(): before sleep
#     cancel_me(): cancel sleep
#     cancel_me(): after sleep
#     main(): cancel_me is cancelled now
```

## **cancelled**()

​	如果 Task 对象 *被取消* 则返回 `True`。

​	当使用 [`cancel()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.cancel" >}}) 发出取消请求时 Task 会被 *取消*，其封包的协程将传播被抛入的 [`CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}}) 异常。

## **uncancel**()

​	递减对此任务的取消请求计数。

​	返回剩余的取消请求数量。

​	请注意一理被取消的任务执行完成，继续调用 [`uncancel()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.uncancel" >}}) 将是低效的。

> Added in version 3.11.
>

​	此方法是供 asyncio 内部使用而不应被最终用户代码所使用。 特别地，在一个 Task 成功地保持未取消状态的时候使用，这可以允许结构化的并发元素如 [任务组]({{< ref "/library/ipc/asyncio/asyncio-task#taskgroups" >}}) 和 [`asyncio.timeout()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.timeout" >}}) 继续运行，将取消操作隔离在相应的结构化代码块中。 例如:

```
async def make_request_with_timeout():
    try:
        async with asyncio.timeout(1):
            # Structured block affected by the timeout:
            await make_request()
            await make_another_request()
    except TimeoutError:
        log("There was a timeout")
    # Outer code not affected by the timeout:
    await unrelated_code()
```

​	带有 `make_request()` 和 `make_another_request()` 的代码块可能因超时而被取消，而 `unrelated_code()` 应当在超时的情况下继续运行。 这是通过 [`uncancel()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.uncancel" >}}) 实现的。 [`TaskGroup`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.TaskGroup" >}}) 上下文管理器也会以类似的方式来使用 [`uncancel()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.uncancel" >}})。

​	如果最终用户代码出于某种原因通过捕获 [`CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}}) 抑制撤销操作，那么它需要调用此方法来移除撤销状态。

​	当该方法将取消计数递减至零，该方法会检查之前的 [`cancel()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.cancel" >}}) 调用是否已安排将 [`CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}}) 抛出到任务中。 如果尚未抛出，则该安排将被撤销（通过重置内部的 `_must_cancel` 旗标 ）。

> 在 3.13 版本发生变更: 更改为在到达零值时撤回待处理的取消请求。

## **cancelling**()

​	返回对此 Task 的挂起请求次数，即对 [`cancel()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.cancel" >}}) 的调用次数减去 [`uncancel()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.uncancel" >}}) 的调用次数。

​	请注意如果该数字大于零但相应 Task 仍在执行，[`cancelled()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.cancelled" >}}) 仍将返回 `False`。 这是因此该数字可通过调用 [`uncancel()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.uncancel" >}}) 来减少，这会导致任务在取消请求降到零时尚未被取消。

​	此方法是供 asyncio 内部使用而不应被最终用户代码所使用。 请参阅 [`uncancel()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.uncancel" >}}) 了解详情。

> Added in version 3.11.
