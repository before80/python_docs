+++
title = "策略"
date = 2024-11-15T12:30:27+08:00
weight = 110
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/asyncio-policy.html](https://docs.python.org/zh-cn/3.13/library/asyncio-policy.html)
>
> 收录该文档的时间：`2024-11-15T12:30:27+08:00`

# 策略

​	事件循环策略是一个用于获取和设置当前 [事件循环]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio-event-loop" >}}) 的全局对象，还可以创建新的事件循环。 默认策略可以可以被 [替换]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio-policy-get-set" >}}) 为 [内置替代策略]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio-policy-builtin" >}}) 以使用不同的事件循环实现，或者替换为可以覆盖这些行为的 [自定义策略]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio-custom-policies" >}})。

[策略对象]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio-policy-objects" >}}) 可为每个 *context* 获取和设置单独的事件循环。 在默认情况下是分线程，不过自定义策略可以按不同的方式定义 *context*。

​	自定义事件循环策略可以控制 [`get_event_loop()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.get_event_loop" >}}), [`set_event_loop()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.set_event_loop" >}}) 和 [`new_event_loop()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.new_event_loop" >}}) 的行为。

​	策略对象应该实现 [`AbstractEventLoopPolicy`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.AbstractEventLoopPolicy" >}}) 抽象基类中定义的API。



## 获取和设置策略

​	可以使用下面函数获取和设置当前进程的策略:

## asyncio.**get_event_loop_policy**()

​	返回当前进程域的策略。

## asyncio.**set_event_loop_policy**(*policy*)

​	将 *policy* 设置为当前进程域策略。

​	如果 *policy* 设为 `None` 将恢复默认策略。



## 策略对象

​	抽象事件循环策略基类定义如下:

## *class* asyncio.**AbstractEventLoopPolicy**

​	异步策略的抽象基类。

## **get_event_loop**()

​	为当前上下文获取事件循环。

​	返回一个实现 [`AbstractEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.AbstractEventLoop" >}}) 接口的事件循环对象。

​	该方法永远不应返回 `None`。

*在 3.6 版本发生变更.*

## **set_event_loop**(*loop*)

​	将当前上下文的事件循环设置为 *loop* 。

## **new_event_loop**()

​	创建并返回一个新的事件循环对象。

​	该方法永远不应返回 `None`。

## **get_child_watcher**()

​	获取子进程监视器对象。

​	返回一个实现 [`AbstractChildWatcher`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.AbstractChildWatcher" >}}) 接口的监视器对象。

​	该函数仅支持Unix。

*自 3.12 版本弃用.*

## **set_child_watcher**(*watcher*)

​	将当前子进程监视器设置为 *watcher* 。

​	该函数仅支持Unix。

*自 3.12 版本弃用.*

​	asyncio附带下列内置策略:

## *class* asyncio.**DefaultEventLoopPolicy**

​	默认的 asyncio 策略。 在 Unix 上使用 [`SelectorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.SelectorEventLoop" >}}) 而在 Windows 上使用 [`ProactorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.ProactorEventLoop" >}})。

​	不需要手动安装默认策略。asyncio已配置成自动使用默认策略。

> 在 3.8 版本发生变更: 在 Windows 上，现在默认会使用 [`ProactorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.ProactorEventLoop" >}})。

> 自 3.12 版本弃用: 现在默认 asyncio 策略的 [`get_event_loop()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.get_event_loop" >}}) 方法将在没有正在运行的事件循环而决定创建一个事件循环时发出 [`DeprecationWarning`]({{< ref "/library/exceptions#DeprecationWarning" >}})。 在未来的某个 Python 发布版中这将改为发出错误。

## *class* asyncio.**WindowsSelectorEventLoopPolicy**

​	一个使用 [`SelectorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.SelectorEventLoop" >}}) 事件循环实现的替代事件循环策略。

[Availability]({{< ref "/library/intro#availability" >}}): Windows.

## *class* asyncio.**WindowsProactorEventLoopPolicy**

​	使用 [`ProactorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.ProactorEventLoop" >}}) 事件循环实现的另一种事件循环策略。

[Availability]({{< ref "/library/intro#availability" >}}): Windows.



## 进程监视器

​	进程监视器允许定制事件循环如何监视Unix子进程。具体来说，事件循环需要知道子进程何时退出。

​	在asyncio中子进程由 [`create_subprocess_exec()`]({{< ref "/library/ipc/asyncio/asyncio-subprocess#asyncio.create_subprocess_exec" >}}) 和 [`loop.subprocess_exec()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.subprocess_exec" >}}) 函数创建。

​	asyncio 定义了 [`AbstractChildWatcher`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.AbstractChildWatcher" >}}) 抽象基类，子监视器必须要实现它，并具有四种不同实现: [`ThreadedChildWatcher`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.ThreadedChildWatcher" >}}) (已配置为默认使用), [`MultiLoopChildWatcher`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.MultiLoopChildWatcher" >}}), [`SafeChildWatcher`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.SafeChildWatcher" >}}) 和 [`FastChildWatcher`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.FastChildWatcher" >}})。

​	请参阅 [子进程和线程]({{< ref "/library/ipc/asyncio/asyncio-subprocess#asyncio-subprocess-threads" >}}) 部分。

​	以下两个函数可用于自定义子进程监视器实现，它将被asyncio事件循环使用:

## asyncio.**get_child_watcher**()

​	返回当前策略的当前子监视器。

*自 3.12 版本弃用.*

## asyncio.**set_child_watcher**(*watcher*)

​	将当前策略的子监视器设置为 *watcher* 。*watcher* 必须实现 [`AbstractChildWatcher`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.AbstractChildWatcher" >}}) 基类定义的方法。

*自 3.12 版本弃用.*

​备注
 

​	第三方事件循环实现可能不支持自定义子监视器。对于这样的事件循环，禁止使用 [`set_child_watcher()`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.set_child_watcher" >}}) 或不起作用。

## *class* asyncio.**AbstractChildWatcher**

## **add_child_handler**(*pid*, *callback*, **args*)

​	注册一个新的子处理回调函数。

​	安排 `callback(pid, returncode, *args)` 在进程的PID与 *pid* 相等时调用。指定另一个同进程的回调函数替换之前的回调处理函数。

​	回调函数 *callback* 必须是线程安全。

## **remove_child_handler**(*pid*)

​	删除进程PID与 *pid* 相等的进程的处理函数。

​	处理函数成功删除时返回 `True` ，没有删除时返回 `False` 。

## **attach_loop**(*loop*)

​	给一个事件循环绑定监视器。

​	如果监视器之前已绑定另一个事件循环，那么在绑定新循环前会先解绑原来的事件循环。

​	注意：循环有可能是 `None` 。

## **is_active**()

​	如果监视器已准备好使用则返回 `True`。

​	使用 *不活动的* 当前子监视器生成子进程将引发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。

> Added in version 3.8.
>

## **close**()

​	关闭监视器。

​	必须调用这个方法以确保相关资源会被清理。

*自 3.12 版本弃用.*

## *class* asyncio.**ThreadedChildWatcher**

​	此实现会为每个生成的子进程启动一具新的等待线程。

​	即使是当 asyncio 事件循环运行在非主 OS 线程上时它也能可靠地工作。

​	当处理大量子进程时不存在显著的开销 (每次子进程结束时为 *O*(1))，但当每个进程启动一个线程时则需要额外的内存。

​	此监视器会默认被使用。

> Added in version 3.8.
>

## *class* asyncio.**MultiLoopChildWatcher**

​	此实现会在实例化时注册一个 `SIGCHLD` 信号处理程序。 这可能会破坏为 `SIGCHLD` 信号安装自定义处理程序的第三方代码。

​	此监视器会在收到 `SIGCHLD` 信号时通过显式地轮询每个进程来避免干扰其他代码生成的进程。

​	该监视器一旦被安装就不会限制从不同线程运行子进程。

​	该解决方案是安全的，但在处理大量进程时会有显著的开销 (每收到一个 `SIGCHLD` 时为 *O*(*n*))。

> Added in version 3.8.
>

*自 3.12 版本弃用.*

## *class* asyncio.**SafeChildWatcher**

​	该实现会使用主线程中的活动事件循环来处理 `SIGCHLD` 信号。 如果主线程没有正在运行的事件循环，则其他线程无法生成子进程 (会引发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}}))。

​	此监视器会在收到 `SIGCHLD` 信号时通过显式地轮询每个进程来避免干扰其他代码生成的进程。

​	该解决方案与 [`MultiLoopChildWatcher`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.MultiLoopChildWatcher" >}}) 一样安全并具有相同的 *O*(*n*) 复杂度，但需要主线程有正在运行的事件循环才能工作。

*自 3.12 版本弃用.*

## *class* asyncio.**FastChildWatcher**

​	这种实现直接调用 `os.waitpid(-1)` 来获取所有已结束的进程，可能会中断其它代码洐生进程并等待它们结束。

​	在处理大量子进程时没有明显的开销 (每次子进程结束时为 *O*(1))。

​	该解决方案需要主线程有正在运行的事件循环才能工作，这与 [`SafeChildWatcher`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.SafeChildWatcher" >}}) 一样。

*自 3.12 版本弃用.*

## *class* asyncio.**PidfdChildWatcher**

​	这个实现会轮询处理文件描述符 (pidfds) 以等待子进程终结。 在某些方面，[`PidfdChildWatcher`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.PidfdChildWatcher" >}}) 是一个“理想的”子进程监视器实现。 它不需要使用信号或线程，不会介入任何在事件循环以外发起的进程，并能随事件循环发起的子进程数量进行线性伸缩。 其主要缺点在于 pidfds 是 Linux 专属的，并且仅在较近版本的核心（5.3+）上可用。

> Added in version 3.9.
>



## 自定义策略

​	要实现一个新的事件循环策略，建议子类化 [`DefaultEventLoopPolicy`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.DefaultEventLoopPolicy" >}}) 并重写需要定制行为的方法，例如:

```
class MyEventLoopPolicy(asyncio.DefaultEventLoopPolicy):

    def get_event_loop(self):
        """获取事件循环。

        这可能为 None 或是一个 EventLoop 的实例。
        """
        loop = super().get_event_loop()
        # 对 loop 执行一些操作 ...
        return loop

asyncio.set_event_loop_policy(MyEventLoopPolicy())
```
