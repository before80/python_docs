+++
title = "队列集"
date = 2024-11-15T12:30:27+08:00
weight = 60
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html)
>
> 收录该文档的时间：`2024-11-15T12:30:27+08:00`

# 队列集

**源代码:** [Lib/asyncio/queues.py](https://github.com/python/cpython/tree/3.13/Lib/asyncio/queues.py)

------

​	asyncio 队列被设计成与 [`queue`](https://docs.python.org/zh-cn/3.13/library/queue.html#module-queue) 模块类似。尽管 asyncio队列不是线程安全的，但是他们是被设计专用于 async/await 代码。

​	注意asyncio 的队列没有 *timeout* 形参；请使用 [`asyncio.wait_for()`](https://docs.python.org/zh-cn/3.13/library/asyncio-task.html#asyncio.wait_for) 函数为队列添加超时操作。

​	参见下面的 [Examples](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#examples) 部分。

## Queue

## *class* asyncio.**Queue**(*maxsize=0*)

​	先进，先出（FIFO）队列

​	如果 *maxsize* 小于等于零，则队列尺寸是无限的。如果是大于 `0` 的整数，则当队列达到 *maxsize* 时， `await put()` 将阻塞至某个元素被 [`get()`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.get) 取出。

​	不像标准库中的并发型 [`queue`](https://docs.python.org/zh-cn/3.13/library/queue.html#module-queue) ，队列的尺寸一直是已知的，可以通过调用 [`qsize()`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.qsize) 方法返回。

*在 3.10 版本发生变更:* 移除了 *loop* 形参。

​	这个类不是线程安全的（[not thread safe](https://docs.python.org/zh-cn/3.13/library/asyncio-dev.html#asyncio-multithreading)）。

## **maxsize**

​	队列中可存放的元素数量。

## **empty**()

​	如果队列为空返回 `True` ，否则返回 `False` 。

## **full**()

​	如果有 [`maxsize`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.maxsize) 个条目在队列中，则返回 `True` 。

​	如果队列用 `maxsize=0` (默认值) 初始化，则 [`full()`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.full) 永远不会返回 `True`。

## *coroutine* **get**()

​	从队列中删除并返回一个元素。如果队列为空，则等待，直到队列中有元素。

​	如果队列已被关闭并且为空，或者如果队列已被立即关闭则会引发 [`QueueShutDown`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.QueueShutDown)。

## **get_nowait**()

​	立即返回一个队列中的元素，如果队列内有值，否则引发异常 [`QueueEmpty`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.QueueEmpty) 。

## *coroutine* **join**()

​	阻塞至队列中所有的元素都被接收和处理完毕。

​	当条目添加到队列的时候，未完成任务的计数就会增加。每当消费协程调用 [`task_done()`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.task_done) 表示这个条目已经被回收，该条目所有工作已经完成，未完成计数就会减少。当未完成计数降到零的时候， [`join()`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.join) 阻塞被解除。

## *coroutine* **put**(*item*)

​	添加一个元素进队列。如果队列满了，在添加元素之前，会一直等待空闲插槽可用。

​	如果队列已被关闭则会引发 [`QueueShutDown`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.QueueShutDown)。

## **put_nowait**(*item*)

​	不阻塞的放一个元素入队列。

​	如果没有立即可用的空闲槽，引发 [`QueueFull`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.QueueFull) 异常。

## **qsize**()

​	返回队列用的元素数量。

## **shutdown**(*immediate=False*)

​	关闭队列，让 [`get()`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.get) 和 [`put()`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.put) 引发 [`QueueShutDown`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.QueueShutDown)。

​	在默认情况下，在已关闭的队列上执行 [`get()`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.get) 只会在队列为空时引发异常。 将 *immediate* 设为真值以改为让 [`get()`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.get) 立即引发异常。

​	所有 [`put()`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.put) 和 [`get()`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.get) 被阻塞的调用方将被撤销阻塞。 如果 *immediate* 为真值，一个任务将对队列中每个剩余的项标记为已完成，它可能撤销对 [`join()`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.join) 的调用方的阻塞。

> Added in version 3.13.
>

## **task_done**()

​	表明前面排队的任务已经完成，即get出来的元素相关操作已经完成。

​	由队列使用者控制。每个 [`get()`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.get) 用于获取一个任务，任务最后调用 [`task_done()`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.task_done) 告诉队列，这个任务已经完成。

​	如果 [`join()`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.join) 当前正在阻塞，在所有条目都被处理后，将解除阻塞(意味着每个 [`put()`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.put) 进队列的条目的 [`task_done()`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.task_done) 都被收到)。

`shutdown(immediate=True)` 将为队列中每个剩余的项调用 [`task_done()`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.task_done)。

​	如果被调用的次数多于放入队列中的项目数量，将引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError) 。

## 优先级队列

## *class* asyncio.**PriorityQueue**

[`Queue`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue) 的变体；按优先级顺序取出条目 (最小的先取出)。

​	条目通常是 `(priority_number, data)` 形式的元组。

## 后进先出队列

## *class* asyncio.**LifoQueue**

[`Queue`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue) 的变体，先取出最近添加的条目（后进，先出）。

## 异常

## *exception* asyncio.**QueueEmpty**

​	当队列为空的时候，调用 [`get_nowait()`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.get_nowait) 方法而引发这个异常。

## *exception* asyncio.**QueueFull**

​	当队列中条目数量已经达到它的 *maxsize* 的时候，调用 [`put_nowait()`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.put_nowait) 方法而引发的异常。

## *exception* asyncio.**QueueShutDown**

​	当在已被关闭的列队上调用 [`put()`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.put) 或 [`get()`](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio.Queue.get) 时引发的异常。

> Added in version 3.13.
>

## 例子

​	队列能被用于多个的并发任务的工作量分配：

```
import asyncio
import random
import time


async def worker(name, queue):
    while True:
        # 从队列获取一个“工作项”。
        sleep_for = await queue.get()

        # 休眠 "sleep_for" 秒。
        await asyncio.sleep(sleep_for)

        # 通知队列“工作项”已被处理。
        queue.task_done()

        print(f'{name} has slept for {sleep_for:.2f} seconds')


async def main():
    # 创建一个用于存储我们的“工作项”的队列。
    queue = asyncio.Queue()

    # 生成随机时段并将它们放入队列。
    total_sleep_time = 0
    for _ in range(20):
        sleep_for = random.uniform(0.05, 1.0)
        total_sleep_time += sleep_for
        queue.put_nowait(sleep_for)

    # 创建三个工作任务来并发地处理队列。
    tasks = []
    for i in range(3):
        task = asyncio.create_task(worker(f'worker-{i}', queue))
        tasks.append(task)

    # 等待直到队列处理完毕。
    started_at = time.monotonic()
    await queue.join()
    total_slept_for = time.monotonic() - started_at

    # 取消我们的工作任务。
    for task in tasks:
        task.cancel()
    # 等待直到所有工作任务都被取消。
    await asyncio.gather(*tasks, return_exceptions=True)

    print('====')
    print(f'3 workers slept in parallel for {total_slept_for:.2f} seconds')
    print(f'total expected sleep time: {total_sleep_time:.2f} seconds')


asyncio.run(main())
```
