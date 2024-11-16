+++
title = "高层级 API 索引"
date = 2024-11-15T12:30:27+08:00
weight = 140
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/asyncio-api-index.html](https://docs.python.org/zh-cn/3.13/library/asyncio-api-index.html)
>
> 收录该文档的时间：`2024-11-15T12:30:27+08:00`

# 高层级 API 索引

​	这个页面列举了所有能用于 async/wait 的高层级asyncio API 集。

## 任务

​	运行异步程序，创建Task对象，等待多件事运行超时的公共集。

| [`run()`]({{< ref "/library/ipc/asyncio/asyncio-runner#asyncio.run" >}}) | 创建事件循环，运行一个协程，关闭事件循环。                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [`Runner`]({{< ref "/library/ipc/asyncio/asyncio-runner#asyncio.Runner" >}}) | 一个能够简化多次异步函数调用操作的上下文管理器。             |
| [`Task`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}}) | Task对象                                                     |
| [`TaskGroup`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.TaskGroup" >}}) | 持有一组任务的上下文管理器。 它提供了一种等待分组中所有任务完成的方便可靠的方式。 |
| [`create_task()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.create_task" >}}) | 启动一个异步 Task，然后将其返回。                            |
| [`current_task()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.current_task" >}}) | 返回当前Task对象                                             |
| [`all_tasks()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.all_tasks" >}}) | 返回一个事件循环的所有尚未完成的任务。                       |
| `await` [`sleep()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.sleep" >}}) | 休眠几秒。                                                   |
| `await` [`gather()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.gather" >}}) | 并发执行所有事件的调度和等待。                               |
| `await` [`wait_for()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.wait_for" >}}) | 有超时控制的运行。                                           |
| `await` [`shield()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.shield" >}}) | 屏蔽取消操作                                                 |
| `await` [`wait()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.wait" >}}) | 完成情况的监控器                                             |
| [`timeout()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.timeout" >}}) | 设置超时运行。 在 `wait_for` 不适合的情况下会很有用。        |
| [`to_thread()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.to_thread" >}}) | 在不同的 OS 线程中异步地运行一个函数。                       |
| [`run_coroutine_threadsafe()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.run_coroutine_threadsafe" >}}) | 从其他OS线程中调度一个协程。                                 |
| `for in` [`as_completed()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.as_completed" >}}) | 用 `for` 循环监控完成情况。                                  |

​	例子

- [使用 asyncio.gather() 并行运行]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio-example-gather" >}}).
- [使用 asyncio.wait_for() 强制超时]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio-example-waitfor" >}}).
- [撤销协程]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio-example-task-cancel" >}}).
- [asyncio.sleep() 的用法]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio-example-sleep" >}}).
- 请主要参阅 [协程与任务文档]({{< ref "/library/ipc/asyncio/asyncio-task#coroutine" >}}).

## 队列集

​	队列集被用于多个异步Task对象的运行调度，实现连接池以及发布/订阅模式。

| [`Queue`]({{< ref "/library/ipc/asyncio/asyncio-queue#asyncio.Queue" >}}) | 先进先出队列   |
| ------------------------------------------------------------ | -------------- |
| [`PriorityQueue`]({{< ref "/library/ipc/asyncio/asyncio-queue#asyncio.PriorityQueue" >}}) | 优先级队列。   |
| [`LifoQueue`]({{< ref "/library/ipc/asyncio/asyncio-queue#asyncio.LifoQueue" >}}) | 后进先出队列。 |

​	例子

- [使用 asyncio.Queue 在多个并发任务间分配工作量]({{< ref "/library/ipc/asyncio/asyncio-queue#asyncio-example-queue-dist" >}}).
- 请参阅 [队列集文档]({{< ref "/library/ipc/asyncio/asyncio-queue#asyncio-queues" >}}).

## 子进程集

​	用于生成子进程和运行shell命令的工具包。

| `await` [`create_subprocess_exec()`]({{< ref "/library/ipc/asyncio/asyncio-subprocess#asyncio.create_subprocess_exec" >}}) | 创建一个子进程。    |
| ------------------------------------------------------------ | ------------------- |
| `await` [`create_subprocess_shell()`]({{< ref "/library/ipc/asyncio/asyncio-subprocess#asyncio.create_subprocess_shell" >}}) | 运行一个shell命令。 |

​	例子

- [执行一个shell命令]({{< ref "/library/ipc/asyncio/asyncio-subprocess#asyncio-example-subprocess-shell" >}}).
- 请参阅 [子进程 APIs]({{< ref "/library/ipc/asyncio/asyncio-subprocess#asyncio-subprocess" >}}) 相关文档.

## 流

​	用于网络IO处理的高级API集。

| `await` [`open_connection()`]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio.open_connection" >}}) | 建立一个TCP连接。                   |
| ------------------------------------------------------------ | ----------------------------------- |
| `await` [`open_unix_connection()`]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio.open_unix_connection" >}}) | 建立一个Unix socket连接。           |
| `await` [`start_server()`]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio.start_server" >}}) | 启动TCP服务。                       |
| `await` [`start_unix_server()`]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio.start_unix_server" >}}) | 启动一个 Unix 套接字服务。          |
| [`StreamReader`]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio.StreamReader" >}}) | 接收网络数据的高级async/await对象。 |
| [`StreamWriter`]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio.StreamWriter" >}}) | 发送网络数据的高级async/await对象。 |

​	例子

- [TCP 客户端样例]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio-example-stream" >}}).
- 请参阅 [streams APIs]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio-streams" >}}) 文档。

## 同步

​	能被用于Task对象集的，类似线程的同步基元组件。

| [`Lock`]({{< ref "/library/ipc/asyncio/asyncio-sync#asyncio.Lock" >}}) | 互斥锁。            |
| ------------------------------------------------------------ | ------------------- |
| [`Event`]({{< ref "/library/ipc/asyncio/asyncio-sync#asyncio.Event" >}}) | 事件对象。          |
| [`Condition`]({{< ref "/library/ipc/asyncio/asyncio-sync#asyncio.Condition" >}}) | 条件对象            |
| [`Semaphore`]({{< ref "/library/ipc/asyncio/asyncio-sync#asyncio.Semaphore" >}}) | 信号量              |
| [`BoundedSemaphore`]({{< ref "/library/ipc/asyncio/asyncio-sync#asyncio.BoundedSemaphore" >}}) | 有界的信号量。      |
| [`Barrier`]({{< ref "/library/ipc/asyncio/asyncio-sync#asyncio.Barrier" >}}) | 一个 Barrier 对象。 |

​	例子

- [asyncio.Event 的用法]({{< ref "/library/ipc/asyncio/asyncio-sync#asyncio-example-sync-event" >}}).
- [使用 asyncio.Barrier]({{< ref "/library/ipc/asyncio/asyncio-sync#asyncio-example-barrier" >}})。
- 请参阅asyncio文档 [synchronization primitives]({{< ref "/library/ipc/asyncio/asyncio-sync#asyncio-sync" >}}).

## 异常

| [`asyncio.CancelledError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio.CancelledError" >}}) | 当一个Task对象被取消的时候被引发。请参阅 [`Task.cancel()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.cancel" >}})。 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [`asyncio.BrokenBarrierError`]({{< ref "/library/ipc/asyncio/asyncio-sync#asyncio.BrokenBarrierError" >}}) | 当一个 Barrier 对象被破坏时引发。 另请参阅 [`Barrier.wait()`]({{< ref "/library/ipc/asyncio/asyncio-sync#asyncio.Barrier.wait" >}})。 |

​	例子

- [在取消请求发生的运行代码中如何处理CancelledError异常]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio-example-task-cancel" >}}).
- 请参阅完整的 [asyncio 专用异常]({{< ref "/library/ipc/asyncio/asyncio-exceptions#asyncio-exceptions" >}}) 列表.
