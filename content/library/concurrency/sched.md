+++
title = "sched --- 事件调度器"
date = 2024-11-15T12:21:05+08:00
weight = 60
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/sched.html](https://docs.python.org/zh-cn/3.13/library/sched.html)
>
> 收录该文档的时间：`2024-11-15T12:21:05+08:00`

# `sched` --- 事件调度器

**源码：** [Lib/sched.py](https://github.com/python/cpython/tree/3.13/Lib/sched.py)

------

[`sched`]({{< ref "/library/concurrency/sched#module-sched" >}}) 模块定义了一个实现通用事件调度程序的类：

## *class* sched.**scheduler**(*timefunc=time.monotonic*, *delayfunc=time.sleep*)

[`scheduler`]({{< ref "/library/concurrency/sched#sched.scheduler" >}}) 类定义了一个调度事件的通用接口。 它需要两个函数来实际处理“外部世界” —— *timefunc* 应当不带参数地调用，并返回一个数字（“时间”，可以为任意单位）。 *delayfunc* 函数应当带一个参数调用，与 *timefunc* 的输出相兼容，并且应当延迟其所指定的时间单位。 每个事件运行后还将调用 *delayfunc* 并传入参数 `0` 以允许其他线程有机会在多线程应用中运行。

> 在 3.3 版本发生变更: *timefunc* 和 *delayfunc* 参数是可选的。

> 在 3.3 版本发生变更: [`scheduler`]({{< ref "/library/concurrency/sched#sched.scheduler" >}}) 类可以安全的在多线程环境中使用。

​	示例:



``` python
>>> import sched, time
>>> s = sched.scheduler(time.time, time.sleep)
>>> def print_time(a='default'):
...     print("From print_time", time.time(), a)
...
>>> def print_some_times():
...     print(time.time())
...     s.enter(10, 1, print_time)
...     s.enter(5, 2, print_time, argument=('positional',))
...     # 虽然具有更高的优先级，'keyword' 将在 'positional' 之后运行因为 enter() 是相对的
...     s.enter(5, 1, print_time, kwargs={'a': 'keyword'})
...     s.enterabs(1_650_000_000, 10, print_time, argument=("first enterabs",))
...     s.enterabs(1_650_000_000, 5, print_time, argument=("second enterabs",))
...     s.run()
...     print(time.time())
...
>>> print_some_times()
1652342830.3640375
From print_time 1652342830.3642538 second enterabs
From print_time 1652342830.3643398 first enterabs
From print_time 1652342835.3694863 positional
From print_time 1652342835.3696074 keyword
From print_time 1652342840.369612 default
1652342840.3697174
```



## 调度器对象

[`scheduler`]({{< ref "/library/concurrency/sched#sched.scheduler" >}}) 实例拥有以下方法和属性：

## scheduler.**enterabs**(*time*, *priority*, *action*, *argument=()*, *kwargs={}*)

​	安排一个新事件。 *time* 参数应该有一个数字类型兼容的返回值，与传递给构造函数的 *timefunc* 函数的返回值兼容。 计划在相同 *time* 的事件将按其 *priority* 的顺序执行。 数字越小表示优先级越高。

​	执行事件意为执行 `action(*argument, **kwargs)`。 *argument* 是包含有 *action* 的位置参数的序列。 *kwargs* 是包含 *action* 的关键字参数的字典。

​	返回值是一个事件，可用于以后取消事件（ 参见 [`cancel()`]({{< ref "/library/concurrency/sched#sched.scheduler.cancel" >}}) ）。

> 在 3.3 版本发生变更: *argument* 参数是可选的。

> 在 3.3 版本发生变更: 添加了 *kwargs* 形参。

## scheduler.**enter**(*delay*, *priority*, *action*, *argument=()*, *kwargs={}*)

​	安排延后 *delay* 时间单位的事件。 除了时间是相对的，其他参数、效果和返回值与 [`enterabs()`]({{< ref "/library/concurrency/sched#sched.scheduler.enterabs" >}}) 相同。

> 在 3.3 版本发生变更: *argument* 参数是可选的。

> 在 3.3 版本发生变更: 添加了 *kwargs* 形参。

## scheduler.**cancel**(*event*)

​	从队列中删除事件。 如果 *event* 不是当前队列中的事件，则此方法将引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

## scheduler.**empty**()

​	如果事件队列为空则返回 `True`。

## scheduler.**run**(*blocking=True*)

​	运行所有计划事件。 此方法将等待（使用传递给构造器的 *delayfunc* 函数）进行下一个事件，然后执行它，依此类推直到没有更多的计划事件。

​	如果 *blocking* 为false，则执行最快到期（如果有）的预定事件，然后在调度程序中返回下一个预定调用的截止时间（如果有）。

*action* 或 *delayfunc* 都可以引发异常。 在任何一种情况下，调度程序都将保持一致状态并传播异常。 如果 *action* 引发异常，则在将来调用 [`run()`]({{< ref "/library/concurrency/sched#sched.scheduler.run" >}}) 时不会尝试该事件。

​	如果一系列事件的运行时间大于下一个事件发生前的可用时间，那么调度程序只会保持落后。 没有事件会被丢弃；调用代码负责取消不再相关的事件。

> 在 3.3 版本发生变更: 添加了 *blocking* 形参。

## scheduler.**queue**

​	只读属性，按照计划运行的顺序返回即将发生的事件列表。 每个事件都显示为 [named tuple]({{< ref "/glossary/idx#term-named-tuple" >}}) ，包含以下字段：time、priority、action、argument、kwargs。
