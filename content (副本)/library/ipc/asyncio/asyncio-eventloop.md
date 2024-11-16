+++
title = "事件循环"
date = 2024-11-15T12:30:27+08:00
weight = 80
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html)
>
> 收录该文档的时间：`2024-11-15T12:30:27+08:00`

# 事件循环

**源代码:** [Lib/asyncio/events.py](https://github.com/python/cpython/tree/3.13/Lib/asyncio/events.py), [Lib/asyncio/base_events.py](https://github.com/python/cpython/tree/3.13/Lib/asyncio/base_events.py)

------

​	前言

​	事件循环是每个 asyncio 应用的核心。 事件循环会运行异步任务和回调，执行网络 IO 操作，以及运行子进程。

​	应用开发者通常应当使用高层级的 asyncio 函数，例如 [`asyncio.run()`]({{< ref "/library/ipc/asyncio/asyncio-runner.#asyncio.run" >}})，应当很少有必要引用循环对象或调用其方法。 本节所针对的主要是低层级代码、库和框架的编写者，他们需要更细致地控制事件循环行为。

​	获取事件循环

​	以下低层级函数可被用于获取、设置或创建事件循环:

## asyncio.**get_running_loop**()

​	返回当前 OS 线程中正在运行的事件循环。

​	如果没有正在运行的事件循环则会引发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。

​	此函数只能由协程或回调来调用。

> Added in version 3.7.
>

## asyncio.**get_event_loop**()

​	获取当前事件循环。

​	当在协程或回调中被调用时（例如通过 call_soon 或类似 API 加入计划任务），此函数将始终返回正在运行的事件循环。

​	如果没有设置正在运行的事件循环，此函数将返回 `get_event_loop_policy().get_event_loop()` 调用的结果。

​	由于此函数具有相当复杂的行为（特别是在使用了自定义事件循环策略的时候），更推荐在协程和回调中使用 [`get_running_loop()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.get_running_loop" >}}) 函数而非 [`get_event_loop()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.get_event_loop" >}})。

​	如上文所说，请考虑使用高层级的 [`asyncio.run()`]({{< ref "/library/ipc/asyncio/asyncio-runner.#asyncio.run" >}}) 函数，而不是使用这些低层级的函数来手动创建和关闭事件循环。

*自 3.12 版本弃用:* 如果没有当前事件循环则会发出弃用警告。 在未来的 Python 发布版中这将被改为错误。

## asyncio.**set_event_loop**(*loop*)

​	将 *loop* 设为当前 OS 线程的当前事件循环。

## asyncio.**new_event_loop**()

​	创建并返回一个新的事件循环对象。

​	请注意 [`get_event_loop()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.get_event_loop" >}}), [`set_event_loop()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.set_event_loop" >}}) 以及 [`new_event_loop()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.new_event_loop" >}}) 函数的行为可以通过 [设置自定义事件循环策略]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio-policies" >}}) 来改变。

​	目录

​	本文档包含下列小节:

- [事件循环方法集]({{< ref "/library/ipc/asyncio/asyncio-eventloop#event-loop-methods" >}}) 章节是事件循环APIs的参考文档；
- [回调处理]({{< ref "/library/ipc/asyncio/asyncio-eventloop#callback-handles" >}}) 章节是从调度方法 例如 [`loop.call_soon()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.call_soon" >}}) 和 [`loop.call_later()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.call_later" >}}) 中返回 [`Handle`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.Handle" >}}) 和 [`TimerHandle`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.TimerHandle" >}}) 实例的文档。
- [Server Objects]({{< ref "/library/ipc/asyncio/asyncio-eventloop#server-objects" >}}) 章节记录了从事件循环方法返回的类型，比如 [`loop.create_server()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_server" >}}) ；
- [Event Loop Implementations]({{< ref "/library/ipc/asyncio/asyncio-eventloop#event-loop-implementations" >}}) 章节记录了 [`SelectorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.SelectorEventLoop" >}}) 和 [`ProactorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.ProactorEventLoop" >}}) 类；
- [Examples]({{< ref "/library/ipc/asyncio/asyncio-eventloop#examples" >}}) 章节展示了如何使用某些事件循环API。



## 事件循环方法集

​	事件循环有下列 **低级** APIs：

- [运行和停止循环]({{< ref "/library/ipc/asyncio/asyncio-eventloop#running-and-stopping-the-loop" >}})
- [安排回调]({{< ref "/library/ipc/asyncio/asyncio-eventloop#scheduling-callbacks" >}})
- [调度延迟回调]({{< ref "/library/ipc/asyncio/asyncio-eventloop#scheduling-delayed-callbacks" >}})
- [创建 Future 和 Task]({{< ref "/library/ipc/asyncio/asyncio-eventloop#creating-futures-and-tasks" >}})
- [打开网络连接]({{< ref "/library/ipc/asyncio/asyncio-eventloop#opening-network-connections" >}})
- [创建网络服务]({{< ref "/library/ipc/asyncio/asyncio-eventloop#creating-network-servers" >}})
- [传输文件]({{< ref "/library/ipc/asyncio/asyncio-eventloop#transferring-files" >}})
- [TLS 升级]({{< ref "/library/ipc/asyncio/asyncio-eventloop#tls-upgrade" >}})
- [监控文件描述符]({{< ref "/library/ipc/asyncio/asyncio-eventloop#watching-file-descriptors" >}})
- [直接使用 socket 对象]({{< ref "/library/ipc/asyncio/asyncio-eventloop#working-with-socket-objects-directly" >}})
- [DNS]({{< ref "/library/ipc/asyncio/asyncio-eventloop#dns" >}})
- [使用管道]({{< ref "/library/ipc/asyncio/asyncio-eventloop#working-with-pipes" >}})
- [Unix 信号]({{< ref "/library/ipc/asyncio/asyncio-eventloop#unix-signals" >}})
- [在线程或者进程池中执行代码。]({{< ref "/library/ipc/asyncio/asyncio-eventloop#executing-code-in-thread-or-process-pools" >}})
- [错误处理API]({{< ref "/library/ipc/asyncio/asyncio-eventloop#error-handling-api" >}})
- [开启调试模式]({{< ref "/library/ipc/asyncio/asyncio-eventloop#enabling-debug-mode" >}})
- [运行子进程]({{< ref "/library/ipc/asyncio/asyncio-eventloop#running-subprocesses" >}})

### [运行和停止循环]({{< ref "/library/ipc/asyncio/asyncio-eventloop#id2" >}})

## loop.**run_until_complete**(*future*)

​	运行直到 *future* ( [`Future`]({{< ref "/library/ipc/asyncio/asyncio-future#asyncio.Future" >}}) 的实例 ) 被完成。

​	如果参数是 [coroutine object]({{< ref "/library/ipc/asyncio/asyncio-task#coroutine" >}}) ，将被隐式调度为 [`asyncio.Task`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}}) 来运行。

​	返回 Future 的结果 或者引发相关异常。

## loop.**run_forever**()

​	运行事件循环直到 [`stop()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.stop" >}}) 被调用。

​	如果 [`stop()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.stop" >}}) 在调用 [`run_forever()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.run_forever" >}}) 之前被调用，循环将轮询一次 I/O 选择器并设置超时为零，再运行所有已加入计划任务的回调来响应 I/O 事件（以及已加入计划任务的事件），然后退出。

​	如果 [`stop()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.stop" >}}) 在 [`run_forever()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.run_forever" >}}) 运行期间被调用，循环将运行当前批次的回调然后退出。 请注意在此情况下由回调加入计划任务的新回调将不会运行；它们将会在下次 [`run_forever()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.run_forever" >}}) 或 [`run_until_complete()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.run_until_complete" >}}) 被调用时运行。

## loop.**stop**()

​	停止事件循环。

## loop.**is_running**()

​	返回 `True` 如果事件循环当前正在运行。

## loop.**is_closed**()

​	如果事件循环已经被关闭，返回 `True` 。

## loop.**close**()

​	关闭事件循环。

​	当这个函数被调用的时候，循环必须处于非运行状态。pending状态的回调将被丢弃。

​	此方法清除所有的队列并立即关闭执行器，不会等待执行器完成。

​	这个方法是幂等的和不可逆的。事件循环关闭后，不应调用其他方法。

## *coroutine* loop.**shutdown_asyncgens**()

​	安装所有当前打开的 [asynchronous generator]({{< ref "/glossary/idx#term-asynchronous-generator" >}}) 对象通过 [`aclose()`]({{< ref "/reference/expressions#agen.aclose" >}}) 调用来关闭。 在调用此方法后，如果有新的异步生成器被迭代则事件循环将会发出警告。 这应当被用来可靠地最终化所有已加入计划任务的异步生成器。

​	请注意当使用 [`asyncio.run()`]({{< ref "/library/ipc/asyncio/asyncio-runner.#asyncio.run" >}}) 时不必调用此函数。

​	示例:

```
try:
    loop.run_forever()
finally:
    loop.run_until_complete(loop.shutdown_asyncgens())
    loop.close()
```

> Added in version 3.6.
>

## *coroutine* loop.**shutdown_default_executor**(*timeout=None*)

​	安排默认执行器的关闭并等待它合并 [`ThreadPoolExecutor`]({{< ref "/library/concurrency/future/concurrent_futures#concurrent.futures.ThreadPoolExecutor" >}}) 中的所有线程。 一旦此方法被调用，将默认执行器与 [`loop.run_in_executor()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.run_in_executor" >}}) 一起使用将引发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。

*timeout* 形参指定提供给执行器结束合并的时间限制（为 [`float`]({{< ref "/library/functions#float" >}}) 形式的秒数）。 在默认情况下，该值为 `None`，即允许执行器无时间限制地执行。

​	如果达到了 *timeout*，将会发出 [`RuntimeWarning`]({{< ref "/library/exceptions#RuntimeWarning" >}}) 并且默认执行器将会终结而不等待其线程结束合并。

​	备注

 

​	当使用 [`asyncio.run()`]({{< ref "/library/ipc/asyncio/asyncio-runner.#asyncio.run" >}}) 不要调用此方法，因为它会自动处理默认执行器的关闭。

> Added in version 3.9.
>

*在 3.12 版本发生变更:* 增加了 *timeout* 形参。

### [安排回调]({{< ref "/library/ipc/asyncio/asyncio-eventloop#id3" >}})

## loop.**call_soon**(*callback*, **args*, *context=None*)

​	安排 *callback* [callback]({{< ref "/glossary/idx#term-callback" >}}) 在事件循环的下一次迭代时附带 *args* 参数被调用。

​	返回一个 [`asyncio.Handle`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.Handle" >}}) 的实例，可在此后被用来取消回调。

​	回调按其注册顺序被调用。每个回调仅被调用一次。

​	可选的仅限关键字参数 *context* 指定一个自定义的 [`contextvars.Context`]({{< ref "/library/concurrency/contextvars#contextvars.Context" >}}) 供 *callback* 在其中运行。 当未提供 *context* 时回调将使用当前上下文。

​	与 [`call_soon_threadsafe()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.call_soon_threadsafe" >}}) 不同，此方法不是线程安全的。, this method is not thread-safe.

## loop.**call_soon_threadsafe**(*callback*, **args*, *context=None*)

[`call_soon()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.call_soon" >}}) 的线程安全版。 当从另一个线程安排回调时，*必须* 使用此函数，因为 [`call_soon()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.call_soon" >}}) 不是线程安全的。

​	如果在已被关闭的循环上调用则会引发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。 这可能会在主应用程序被关闭时在二级线程上发生。

​	参见 [concurrency and multithreading]({{< ref "/library/ipc/asyncio/asyncio-dev#asyncio-multithreading" >}}) 部分的文档。

*在 3.7 版本发生变更:* 加入键值类形参 *context*。请参阅 [**PEP 567**](https://peps.python.org/pep-0567/) 查看更多细节。

​	备注

 

​	大多数 [`asyncio`]({{< ref "/library/ipc/asyncio#module-asyncio" >}}) 的调度函数不让传递关键字参数。为此，请使用 [`functools.partial()`]({{< ref "/library/functional/functools#functools.partial" >}}) ：

```
# 将把 "print("Hello", flush=True)" 加入计划任务
loop.call_soon(
    functools.partial(print, "Hello", flush=True))
```

​	使用 partial 对象通常比使用lambda更方便，asyncio 在调试和错误消息中能更好的呈现 partial 对象。



### [调度延迟回调]({{< ref "/library/ipc/asyncio/asyncio-eventloop#id4" >}})

​	事件循环提供安排调度函数在将来某个时刻调用的机制。事件循环使用单调时钟来跟踪时间。

## loop.**call_later**(*delay*, *callback*, **args*, *context=None*)

​	安排 *callback* 在给定的 *delay* 秒（可以是 int 或者 float）后被调用。

​	返回一个 [`asyncio.TimerHandle`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.TimerHandle" >}}) 实例，该实例能用于取消回调。

*callback* 只被调用一次。如果两个回调被安排在同样的时间点，执行顺序未限定。

​	可选的位置参数 *args* 在被调用的时候传递给 *callback* 。 如果你想把关键字参数传递给 *callback* ，请使用 [`functools.partial()`]({{< ref "/library/functional/functools#functools.partial" >}}) 。

​	可选键值类的参数 *context* 允许 *callback* 运行在一个指定的自定义 [`contextvars.Context`]({{< ref "/library/concurrency/contextvars#contextvars.Context" >}}) 对象中。如果没有提供 *context* ，则使用当前上下文。

*在 3.7 版本发生变更:* 加入键值类形参 *context*。请参阅 [**PEP 567**](https://peps.python.org/pep-0567/) 查看更多细节。

*在 3.8 版本发生变更:* 在 Python 3.7 和更早版本的默认事件循环实现中， *delay* 不能超过一天。 这在 Python 3.8 中已被修复。

## loop.**call_at**(*when*, *callback*, **args*, *context=None*)

​	安排 *callback* 在给定的绝对时间戳 *when* (int 或 float) 被调用，使用与 [`loop.time()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.time" >}}) 同样的时间参考。

​	本方法的行为和 [`call_later()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.call_later" >}}) 方法相同。

​	返回一个 [`asyncio.TimerHandle`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.TimerHandle" >}}) 实例，该实例能用于取消回调。

*在 3.7 版本发生变更:* 加入键值类形参 *context*。请参阅 [**PEP 567**](https://peps.python.org/pep-0567/) 查看更多细节。

*在 3.8 版本发生变更:* 在 Python 3.7 和更早版本的默认事件循环实现中，*when* 和当前时间相差不能超过一天。 在这 Python 3.8 中已被修复。

## loop.**time**()

​	根据时间循环内部的单调时钟，返回当前时间为一个 [`float`]({{< ref "/library/functions#float" >}}) 值。

​	备注

*在 3.8 版本发生变更:* 在 Python 3.7 和更早版本中超时 (相对的 *delay* 或绝对的 *when*) 不能超过一天。 这在 Python 3.8 中已被修复。

​	参见

 

[`asyncio.sleep()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.sleep" >}}) 函数。

### [创建 Future 和 Task]({{< ref "/library/ipc/asyncio/asyncio-eventloop#id5" >}})

## loop.**create_future**()

​	创建一个附加到事件循环中的 [`asyncio.Future`]({{< ref "/library/ipc/asyncio/asyncio-future#asyncio.Future" >}}) 对象。

​	这是在asyncio中创建Futures的首选方式。这让第三方事件循环可以提供Future 对象的替代实现(更好的性能或者功能)。

> Added in version 3.5.2.
>

## loop.**create_task**(*coro*, ***, *name=None*, *context=None*)

​	将 [协程]({{< ref "/library/ipc/asyncio/asyncio-task#coroutine" >}}) *coro* 的执行加入计划任务。 返回一个 [`Task`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}}) 对象。

​	第三方的事件循环可以使用它们自己的 [`Task`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}}) 子类来满足互操作性。这种情况下结果类型是一个 [`Task`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}}) 的子类。

​	如果提供了 *name* 参数且不为 `None`，它会使用 [`Task.set_name()`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task.set_name" >}}) 来设为任务的名称。

​	可选的 *context* 参数允许指定自定义的 [`contextvars.Context`]({{< ref "/library/concurrency/contextvars#contextvars.Context" >}}) 供 *coro* 运行。 当未提供 *context* 时将创建当前上下文的副本。

*在 3.8 版本发生变更:* 添加了 *name* 参数。

*在 3.11 版本发生变更:* 增加了 *context* 形参。

## loop.**set_task_factory**(*factory*)

​	设置一个任务工厂，它将由 [`loop.create_task()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_task" >}}) 来使用。

​	如果 *factory* 为 `None` 则将设置默认的任务工厂。 在其他情况下，*factory* 必须是一个 *可调用对象* 且其签名要匹配 `(loop, coro, context=None)`，其中 *loop* 是对活动事件循环的引用，而 *coro* 是一个协程对象。 该可调用对象必须返回一个兼容 [`asyncio.Future`]({{< ref "/library/ipc/asyncio/asyncio-future#asyncio.Future" >}}) 的对象。

## loop.**get_task_factory**()

​	返回一个任务工厂，或者如果是使用默认值则返回 `None`。

### [打开网络连接]({{< ref "/library/ipc/asyncio/asyncio-eventloop#id6" >}})

## *coroutine* loop.**create_connection**(*protocol_factory*, *host=None*, *port=None*, ***, *ssl=None*, *family=0*, *proto=0*, *flags=0*, *sock=None*, *local_addr=None*, *server_hostname=None*, *ssl_handshake_timeout=None*, *ssl_shutdown_timeout=None*, *happy_eyeballs_delay=None*, *interleave=None*, *all_errors=False*)

​	打开一个流式传输连接，连接到由 *host* 和 *port* 指定的地址。

​	套接字族可以是 [`AF_INET`]({{< ref "/library/ipc/socket#socket.AF_INET" >}}) 或 [`AF_INET6`]({{< ref "/library/ipc/socket#socket.AF_INET6" >}})，具体取决于 *host* (或 *family* 参数，如果有提供的话)。

​	套接字类型将为 [`SOCK_STREAM`]({{< ref "/library/ipc/socket#socket.SOCK_STREAM" >}})。

*protocol_factory* 必须为一个返回 [asyncio 协议]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio-protocol" >}}) 实现的可调用对象。

​	这个方法会尝试在后台创建连接。当创建成功，返回 `(transport, protocol)` 组合。

​	底层操作的大致的执行顺序是这样的：

1. 创建连接并为其创建一个 [传输]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio-transport" >}})。
2. 不带参数地调用 *protocol_factory* 并预期返回一个 [协议]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio-protocol" >}}) 实例。
3. 协议实例通过调用其 [`connection_made()`]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio.BaseProtocol.connection_made" >}}) 方法与传输进行配对。
4. 成功时返回一个 `(transport, protocol)` 元组。

​	创建的传输是一个具体实现相关的双向流。

​	其他参数：

- *ssl*: 如果给定该参数且不为假值，则会创建一个 SSL/TLS 传输（默认创建一个纯 TCP 传输）。 如果 *ssl* 是一个 [`ssl.SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 对象，则会使用此上下文来创建传输对象；如果 *ssl* 为 [`True`]({{< ref "/library/constants#True" >}})，则会使用从 [`ssl.create_default_context()`]({{< ref "/library/ipc/ssl#ssl.create_default_context" >}}) 返回的默认上下文。

  ​	参见

   

  [SSL/TLS security considerations]({{< ref "/library/ipc/ssl#ssl-security" >}})

- *server_hostname* 设置或覆盖目标服务器的证书将要匹配的主机名。 应当只在 *ssl* 不为 `None` 时传入。 默认情况下会使用 *host* 参数的值。 如果 *host* 为空那就没有默认值，你必须为 *server_hostname* 传入一个值。 如果 *server_hostname* 为空字符串，则主机名匹配会被禁用（这是一个严重的安全风险，使得潜在的中间人攻击成为可能）。

- *family*, *proto*, *flags* 是可选的地址族、协议和标志，它们会被传递给 getaddrinfo() 来对 *host* 进行解析。如果要指定的话，这些都应该是来自于 [`socket`]({{< ref "/library/ipc/socket#module-socket" >}}) 模块的对应常量。

- 如果给出 *happy_eyeballs_delay*，它将为此链接启用 Happy Eyeballs。 该函数应当为一个表示在开始下一个并行尝试之前要等待连接尝试完成的秒数的浮点数。 这也就是在 [**RFC 8305**](https://datatracker.ietf.org/doc/html/rfc8305.html) 中定义的 "连接尝试延迟"。 该 RFC 所推荐的合理默认值为 `0.25` (250 毫秒)。

- *interleave* 控制当主机名解析为多个 IP 地址时的地址重排序。 如果该参数为 `0` 或未指定，则不会进行重排序，这些地址会按 [`getaddrinfo()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.getaddrinfo" >}}) 所返回的顺序进行尝试。 如果指定了一个正整数，这些地址会按地址族交错排列，而指定的整数会被解读为 [**RFC 8305**](https://datatracker.ietf.org/doc/html/rfc8305.html) 所定义的 "首个地址族计数"。 如果 *happy_eyeballs_delay* 未指定则默认值为 `0`，否则为 `1`。

- 如果给出 *sock*，它应当是一个已存在、已连接并将被传输所使用的 [`socket.socket`]({{< ref "/library/ipc/socket#socket.socket" >}}) 对象。 如果给出了 *sock*，则 *host*, *port*, *family*, *proto*, *flags*, *happy_eyeballs_delay*, *interleave* 和 *local_addr* 都不应当被指定。

  ​	备注

   

  *sock* 参数可将套接字的所有权转给所创建的传输。 要关闭该套接字，请调用传输的 [`close()`]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio.BaseTransport.close" >}}) 方法。

- 如果给出 *local_addr*，它应当是一个用来在本地绑定套接字的 `(local_host, local_port)` 元组。 *local_host* 和 *local_port* 会使用 `getaddrinfo()` 来查找，这与 *host* 和 *port* 类似。

- *ssl_handshake_timeout* 是（用于 TLS 连接的）在放弃连接之前要等待 TLS 握手完成的秒数。 如果参数为 `None` 则使用 (默认的) `60.0`。

- *ssl_shutdown_timeout* 是在放弃连接之前要等待 SSL 关闭完成的秒数。 如为 `None` (默认值) 则使用 `30.0`。

- *all_errors* 确定当无法创建连接时要引发何种异常。 在默认情况下，只有一个 `Exception` 会被引发：即只有一个异常或所有错误的消息相同，或者是合并了多个错误消息的单个 `OSError`。 当 `all_errors` 为 `True` 时，将引发一个包含所有异常的 `ExceptionGroup` (即使只有一个异常)。

*在 3.5 版本发生变更:* [`ProactorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.ProactorEventLoop" >}}) 类中添加 SSL/TLS 支持。

*在 3.6 版本发生变更:* 套接字选项 [socket.TCP_NODELAY]({{< ref "/library/ipc/socket#socket-unix-constants" >}}) 默认将为所有 TCP 连接设置。

*在 3.7 版本发生变更:* 添加了 *ssl_handshake_timeout* 参数。

*在 3.8 版本发生变更:* 增加了 *happy_eyeballs_delay* 和 *interleave* 形参。

​	Happy Eyeballs 算法：成功使用双栈主机。 当服务器的 IPv4 路径和协议工作正常，但服务器的 IPv6 路径和协议工作不正常时，双线客户端应用程序相比仅有 IPv4 的客户端会感受到明显的连接延迟。 这是不可接受的因为它会导致双栈客户端糟糕的用户体验。 这篇文档指明了减少这种用户可见延迟的算法要求并提供了具体的算法。

​	详情参见: https://datatracker.ietf.org/doc/html/rfc6555

*在 3.11 版本发生变更:* 添加了 *ssl_shutdown_timeout* 形参。

*在 3.12 版本发生变更:* 添加了 *all_errors*。

​	参见

 

[`open_connection()`]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio.open_connection" >}}) 函数是一个高层级的替代 API。 它返回一对 ([`StreamReader`]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio.StreamReader" >}}), [`StreamWriter`]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio.StreamWriter" >}}))，可在 async/await 代码中直接使用。

## *coroutine* loop.**create_datagram_endpoint**(*protocol_factory*, *local_addr=None*, *remote_addr=None*, ***, *family=0*, *proto=0*, *flags=0*, *reuse_port=None*, *allow_broadcast=None*, *sock=None*)

​	创建一个数据报连接。

​	套接字族可以是 [`AF_INET`]({{< ref "/library/ipc/socket#socket.AF_INET" >}}), [`AF_INET6`]({{< ref "/library/ipc/socket#socket.AF_INET6" >}}) 或 [`AF_UNIX`]({{< ref "/library/ipc/socket#socket.AF_UNIX" >}})，具体取决于 *host* (或 *family* 参数，如果有提供的话)。

​	套接字类型将为 [`SOCK_DGRAM`]({{< ref "/library/ipc/socket#socket.SOCK_DGRAM" >}})。

*protocol_factory* 必须为一个返回 [协议]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio-protocol" >}}) 实现的可调用对象。

​	成功时返回一个 `(transport, protocol)` 元组。

​	其他参数：

- 如果给出 *local_addr*，它应当是一个用来在本地绑定套接字的 `(local_host, local_port)` 元组。 *local_host* 和 *local_port* 是使用 [`getaddrinfo()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.getaddrinfo" >}}) 来查找的。

- *remote_addr*，如果指定的话，就是一个 `(remote_host, remote_port)` 元组，用于同一个远程地址连接。*remote_host* 和 *remote_port* 是使用 [`getaddrinfo()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.getaddrinfo" >}}) 来查找的。

- *family*, *proto*, *flags* 是可选的地址族，协议和标志，其会被传递给 [`getaddrinfo()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.getaddrinfo" >}}) 来完成 *host* 的解析。如果要指定的话，这些都应该是来自于 [`socket`]({{< ref "/library/ipc/socket#module-socket" >}}) 模块的对应常量。

- *reuse_port* 告知内核允许此端点绑定到其他现有端点所绑定的相同端口上，只要它们在创建时都设置了这个旗标。 这个选项在 Windows 和某些 Unix 上将不受支持。 如果 [socket.SO_REUSEPORT]({{< ref "/library/ipc/socket#socket-unix-constants" >}}) 常量未被定义那么该功能就是不受支持的。

- *allow_broadcast* 告知内核允许此端点向广播地址发送消息。

- *sock* 可选择通过指定此值用于使用一个预先存在的，已经处于连接状态的 [`socket.socket`]({{< ref "/library/ipc/socket#socket.socket" >}}) 对象，并将其提供给此传输对象使用。如果指定了这个值， *local_addr* 和 *remote_addr* 就应该被忽略 (必须为 [`None`]({{< ref "/library/constants#None" >}}))。

  ​	备注

   

  *sock* 参数可将套接字的所有权转给所创建的传输。 要关闭该套接字，请调用传输的 [`close()`]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio.BaseTransport.close" >}}) 方法。

​	参见 [UDP echo 客户端协议]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio-udp-echo-client-protocol" >}}) 和 [UDP echo 服务端协议]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio-udp-echo-server-protocol" >}}) 的例子。

*在 3.4.4 版本发生变更:* 添加了 *family*, *proto*, *flags*, *reuse_address*, *reuse_port*, *allow_broadcast* 和 *sock* 等形参。

*在 3.8 版本发生变更:* 添加WIndows的支持。

*在 3.8.1 版本发生变更:* *reuse_address* 形参已不再受支持，因为使用 [socket.SO_REUSEADDR]({{< ref "/library/ipc/socket#socket-unix-constants" >}}) 对于 UDP 会造成显著的安全问题。 显式地传入 `reuse_address=True` 将引发异常。

​	当具有不同 UID 的多个进程将套接字赋给具有 `SO_REUSEADDR` 的相同 UDP 套接字地址时，传入的数据包可能会在套接字间随机分配。

​	对于受支持的平台，可以使用 *reuse_port* 作为类似功能的替代。 通过 *reuse_port*，将会改用 [socket.SO_REUSEPORT]({{< ref "/library/ipc/socket#socket-unix-constants" >}})，它能防止具有不同 UID 的进程将套接字赋给相同的套接字地址。

*在 3.11 版本发生变更:* 自 Python 3.8.1, 3.7.6 和 3.6.10 起被禁用的 *reuse_address* 形参现已被完全移除。

## *coroutine* loop.**create_unix_connection**(*protocol_factory*, *path=None*, ***, *ssl=None*, *sock=None*, *server_hostname=None*, *ssl_handshake_timeout=None*, *ssl_shutdown_timeout=None*)

​	创建Unix 连接

​	套接字族将为 [`AF_UNIX`]({{< ref "/library/ipc/socket#socket.AF_UNIX" >}})；套接字类型将为 [`SOCK_STREAM`]({{< ref "/library/ipc/socket#socket.SOCK_STREAM" >}})。

​	成功时返回一个 `(transport, protocol)` 元组。

*path* 是所要求的 Unix 域套接字的名字，除非指定了 *sock* 形参。 抽象的 Unix 套接字, [`str`]({{< ref "/library/stdtypes#str" >}}), [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 和 [`Path`]({{< ref "/library/filesys/pathlib#pathlib.Path" >}}) 路径都是受支持的。

​	请查看 [`loop.create_connection()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_connection" >}}) 方法的文档了解有关此方法的参数的信息。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

*在 3.7 版本发生变更:* 增加了 *ssl_handshake_timeout* 参数。现在 *path* 参数可以是一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}}) 。

*在 3.11 版本发生变更:* 添加了 *ssl_shutdown_timeout* 形参。

### [创建网络服务]({{< ref "/library/ipc/asyncio/asyncio-eventloop#id7" >}})

## *coroutine* loop.**create_server**(*protocol_factory*, *host=None*, *port=None*, ***, *family=socket.AF_UNSPEC*, *flags=socket.AI_PASSIVE*, *sock=None*, *backlog=100*, *ssl=None*, *reuse_address=None*, *reuse_port=None*, *keep_alive=None*, *ssl_handshake_timeout=None*, *ssl_shutdown_timeout=None*, *start_serving=True*)

​	创建一个 TCP 服务器 (套接字类型 [`SOCK_STREAM`]({{< ref "/library/ipc/socket#socket.SOCK_STREAM" >}})) 在 *host* 地址的 *port* 上进行监听。

​	返回一个 [`Server`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.Server" >}}) 对象。

​	参数：

- *protocol_factory* 必须为一个返回 [协议]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio-protocol" >}}) 实现的可调用对象。

- *host* 形参可被设为几种类型，它确定了服务器所应监听的位置:

  - 如果 *host* 是一个字符串，则 TCP 服务器会被绑定到 *host* 所指明的单一网络接口。
  - 如果 *host* 是一个字符串序列，则 TCP 服务器会被绑定到序列所指明的所有网络接口。
  - 如果 *host* 是一个空字符串或 `None`，则会应用所有接口并将返回包含多个套接字的列表（通常是一个 IPv4 的加一个 IPv6 的）。

- 可以设置 *port* 参数来指定服务器应该监听哪个端口。如果为 `0` 或者 `None` （默认），将选择一个随机的未使用的端口（注意，如果 *host* 解析到多个网络接口，将为每个接口选择一个不同的随机端口）。

- *family* 可被设为 [`socket.AF_INET`]({{< ref "/library/ipc/socket#socket.AF_INET" >}}) 或 [`AF_INET6`]({{< ref "/library/ipc/socket#socket.AF_INET6" >}}) 以强制此套接字使用 IPv4 或 IPv6。 如果未设定，则 *family* 将通过主机名为确定 (默认为 [`AF_UNSPEC`]({{< ref "/library/ipc/socket#socket.AF_UNSPEC" >}}))。

- *flags* 是用于 [`getaddrinfo()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.getaddrinfo" >}}) 的位掩码。

- 可以选择指定 *sock* 以便使用预先存在的套接字对象。 如果指定了此参数，则不可再指定 *host* 和 *port*。

  ​	备注

   

  *sock* 参数可将套接字的所有权转给所创建的服务器。 要关闭该套接字，请调用服务器的 [`close()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.Server.close" >}}) 方法。

- *backlog* 是传递给 [`listen()`]({{< ref "/library/ipc/socket#socket.socket.listen" >}}) 的最大排队连接的数量（默认为100）。

- *ssl* 可被设置为一个 [`SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 实例以在所接受的连接上启用 TLS。

- *reuse_address* 告知内核要重用一个处于 `TIME_WAIT` 状态的本地套接字，而不是等待其自然超时失效。 如果未指定此参数则在 Unix 上将自动设置为 `True`。

- *reuse_port* 告知内核，只要在创建的时候都设置了这个标志，就允许此端点绑定到其它端点列表所绑定的同样的端口上。这个选项在 Windows 上是不支持的。

- *keep_alive* 设为 `True` 将通过启用定期的消息传输来使连接保持活动状态。

*在 3.13 版本发生变更:* 增加了 *keep_alive* 形参。

- *ssl_handshake_timeout* 是（用于 TLS 服务器的）在放弃连接之前要等待 TLS 握手完成的秒数。 如果参数为 (默认值) `None` 则为 `60.0` 秒。
- *ssl_shutdown_timeout* 是在放弃连接之前要等待 SSL 关闭完成的秒数。 如为 `None` (默认值) 则使用 `30.0`。
- *start_serving* 设置成 `True` (默认值) 会导致创建server并立即开始接受连接。设置成 `False` ，用户需要等待 [`Server.start_serving()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.Server.start_serving" >}}) 或者 [`Server.serve_forever()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.Server.serve_forever" >}}) 以使server开始接受连接。

*在 3.5 版本发生变更:* [`ProactorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.ProactorEventLoop" >}}) 类中添加 SSL/TLS 支持。

*在 3.5.1 版本发生变更:* *host* 形参可以是一个字符串的序列。

*在 3.6 版本发生变更:* 增加了 *ssl_handshake_timeout* 和 *start_serving* 形参。 套接字选项 [socket.TCP_NODELAY]({{< ref "/library/ipc/socket#socket-unix-constants" >}}) 会默认为所有 TCP 连接设置。

*在 3.11 版本发生变更:* 添加了 *ssl_shutdown_timeout* 形参。

​	参见

 

[`start_server()`]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio.start_server" >}}) 函数是一个高层级的替代 API，它返回一对 [`StreamReader`]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio.StreamReader" >}}) 和 [`StreamWriter`]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio.StreamWriter" >}})，可在 async/await 代码中使用。

## *coroutine* loop.**create_unix_server**(*protocol_factory*, *path=None*, ***, *sock=None*, *backlog=100*, *ssl=None*, *ssl_handshake_timeout=None*, *ssl_shutdown_timeout=None*, *start_serving=True*, *cleanup_socket=True*)

​	与 [`loop.create_server()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_server" >}}) 类似但是专用于 [`AF_UNIX`]({{< ref "/library/ipc/socket#socket.AF_UNIX" >}}) 套接字族。

*path* 是必要的 Unix 域套接字名称，除非提供了 *sock* 参数。 抽象的 Unix 套接字, [`str`]({{< ref "/library/stdtypes#str" >}}), [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 和 [`Path`]({{< ref "/library/filesys/pathlib#pathlib.Path" >}}) 路径都是受支持的。

​	如果 *cleanup_socket* 为真值那么当服务器关闭时 Unix 套接字将自动从文件系统中被移除，除非套接字在服务器创建之后被替换。

​	请查看 [`loop.create_server()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_server" >}}) 方法的文档了解有关此方法的参数的信息。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

*在 3.7 版本发生变更:* 增加了 *ssl_handshake_timeout* 和 *start_serving* 参数。现在 *path* 参数可以是一个 [`Path`]({{< ref "/library/filesys/pathlib#pathlib.Path" >}}) 对象。

*在 3.11 版本发生变更:* 添加了 *ssl_shutdown_timeout* 形参。

*在 3.13 版本发生变更:* 增加了 *cleanup_socket* 形参。

## *coroutine* loop.**connect_accepted_socket**(*protocol_factory*, *sock*, ***, *ssl=None*, *ssl_handshake_timeout=None*, *ssl_shutdown_timeout=None*)

​	将已被接受的连接包装成一个传输/协议对。

​	此方法可被服务器用来接受 asyncio 以外的连接，但是使用 asyncio 来处理它们。

​	参数：

- *protocol_factory* 必须为一个返回 [协议]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio-protocol" >}}) 实现的可调用对象。

- *sock* 是一个预先存在的套接字对象，它是由 [`socket.accept`]({{< ref "/library/ipc/socket#socket.socket.accept" >}}) 返回的。

  ​	备注

   

  *sock* 参数可将套接字的所有权转给所创建的传输。 要关闭该套接字，请调用传输的 [`close()`]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio.BaseTransport.close" >}}) 方法。

- *ssl* 可被设置为一个 [`SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 以在接受的连接上启用 SSL。

- *ssl_handshake_timeout* 是(为一个SSL连接)在中止连接前，等待SSL握手完成的时间【单位秒】。如果为 `None` (缺省) 则是 `60.0` 秒。

- *ssl_shutdown_timeout* 是在放弃连接之前要等待 SSL 关闭完成的秒数。 如为 `None` (默认值) 则使用 `30.0`。

​	返回一个 `(transport, protocol)` 对。

> Added in version 3.5.3.
>

*在 3.7 版本发生变更:* 添加了 *ssl_handshake_timeout* 参数。

*在 3.11 版本发生变更:* 添加了 *ssl_shutdown_timeout* 形参。

### [传输文件]({{< ref "/library/ipc/asyncio/asyncio-eventloop#id8" >}})

## *coroutine* loop.**sendfile**(*transport*, *file*, *offset=0*, *count=None*, ***, *fallback=True*)

​	将 *file* 通过 *transport* 发送。 返回所发送的字节总数。

​	如果可用的话，该方法将使用高性能的 [`os.sendfile()`]({{< ref "/library/allos/os#os.sendfile" >}})。

*file* 必须是个二进制模式打开的常规文件对象。

*offset* 指明从何处开始读取文件。 如果指定了 *count*，它是要传输的字节总数而不再一直发送文件直至抵达 EOF。 文件位置总是会被更新，即使此方法引发了错误，并可以使用 [`file.tell()`]({{< ref "/library/allos/io#io.IOBase.tell" >}}) 来获取实际发送的字节总数。

*fallback* 设为 `True` 会使得 asyncio 在平台不支持 sendfile 系统调用时手动读取并发送文件（例如 Windows 或 Unix 上的 SSL 套接字）。

​	如果系统不支持 *sendfile* 系统调用且 *fallback* 为 `False` 则会引发 [`SendfileNotAvailableError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions.#asyncio.SendfileNotAvailableError" >}})。

> Added in version 3.7.
>

### [TLS 升级]({{< ref "/library/ipc/asyncio/asyncio-eventloop#id9" >}})

## *coroutine* loop.**start_tls**(*transport*, *protocol*, *sslcontext*, ***, *server_side=False*, *server_hostname=None*, *ssl_handshake_timeout=None*, *ssl_shutdown_timeout=None*)

​	将现有基于传输的连接升级到 TLS。

​	创建一个 TLS 编码器/解码器实例并将其插入到 *transport* 和 *protocol* 之间。 该编码器/解码器同时实现了面向 *transport* 的协议和面向 *protocol* 的传输。

​	返回已创建的双接口实例。 在 *await* 之后，*protocol* 必须使用原始 *transport* 来停止并仅与所返回的对象通信因为编码器会缓存 *protocol* 方的数据并会不定期地与 *transport* 交换额外的 TLS 会话数据包。

​	在某些情况下（例如当传入的 transport 已经关闭）这可能返回 `None`。

​	参数：

- *transport* 和 *protocol* 实例的方法与 [`create_server()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_server" >}}) 和 [`create_connection()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_connection" >}}) 所返回的类似。
- *sslcontext* ：一个已经配置好的 [`SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 实例。
- 当服务端连接已升级时 (如 [`create_server()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_server" >}}) 所创建的对象) *server_side* 会传入 `True`。
- *server_hostname* ：设置或者覆盖目标服务器证书中相对应的主机名。
- *ssl_handshake_timeout* 是（用于 TLS 连接的）在放弃连接之前要等待 TLS 握手完成的秒数。 如果参数为 `None` 则使用 (默认的) `60.0`。
- *ssl_shutdown_timeout* 是在放弃连接之前要等待 SSL 关闭完成的秒数。 如为 `None` (默认值) 则使用 `30.0`。

> Added in version 3.7.
>

*在 3.11 版本发生变更:* 添加了 *ssl_shutdown_timeout* 形参。

### [监控文件描述符]({{< ref "/library/ipc/asyncio/asyncio-eventloop#id10" >}})

## loop.**add_reader**(*fd*, *callback*, **args*)

​	开始监视 *fd* 文件描述符以获取读取的可用性，一旦 *fd* 可用于读取，使用指定的参数调用 *callback* 。

## loop.**remove_reader**(*fd*)

​	停止监视 *fd* 文件描述符的读取可用性。 如果之前正在监视 *fd* 的读取则返回 `True`。

## loop.**add_writer**(*fd*, *callback*, **args*)

​	开始监视 *fd* 文件描述符的写入可用性，一旦 *fd* 可用于写入，使用指定的参数调用 *callback* 。

​	使用 [`functools.partial()`]({{< ref "/library/functional/functools#functools.partial" >}}) [传递关键字参数]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio-pass-keywords" >}}) 给 *callback*.

## loop.**remove_writer**(*fd*)

​	停止监视 *fd* 文件描述符的写入可用性。 如果之前正在监视 *fd* 的写入则返回 `True`。

​	另请查看 [平台支持]({{< ref "/library/ipc/asyncio/asyncio-platforms#asyncio-platform-support" >}}) 一节了解以上方法的某些限制。

### [直接使用 socket 对象]({{< ref "/library/ipc/asyncio/asyncio-eventloop#id11" >}})

​	通常，使用基于传输的 API 的协议实现，例如 [`loop.create_connection()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_connection" >}}) 和 [`loop.create_server()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_server" >}}) 比直接使用套接字的实现更快。 但是，在某些应用场景下性能并不非常重要，直接使用 [`socket`]({{< ref "/library/ipc/socket#socket.socket" >}}) 对象会更方便。

## *coroutine* loop.**sock_recv**(*sock*, *nbytes*)

​	从 *sock* 接收至多 *nbytes*。 [`socket.recv()`]({{< ref "/library/ipc/socket#socket.socket.recv" >}}) 的异步版本。

​	返回接收到的数据【bytes对象类型】。

*sock* 必须是个非阻塞socket。

*在 3.7 版本发生变更:* 虽然这个方法总是被记录为协程方法，但它在 Python 3.7 之前的发行版中会返回一个 [`Future`]({{< ref "/library/ipc/asyncio/asyncio-future#asyncio.Future" >}})。 从 Python 3.7 开始它则是一个 `async def` 方法。

## *coroutine* loop.**sock_recv_into**(*sock*, *buf*)

​	从 *sock* 接收数据放入 *buf* 缓冲区。 模仿了阻塞型的 [`socket.recv_into()`]({{< ref "/library/ipc/socket#socket.socket.recv_into" >}}) 方法。

​	返回写入缓冲区的字节数。

*sock* 必须是个非阻塞socket。

> Added in version 3.7.
>

## *coroutine* loop.**sock_recvfrom**(*sock*, *bufsize*)

​	从 *sock* 接收最大为 *bufsize* 的数据报。 [`socket.recvfrom()`]({{< ref "/library/ipc/socket#socket.socket.recvfrom" >}}) 的异步版本。

​	返回一个 (已接收数据, 远程地址) 元组。

*sock* 必须是个非阻塞socket。

> Added in version 3.11.
>

## *coroutine* loop.**sock_recvfrom_into**(*sock*, *buf*, *nbytes=0*)

​	从 *sock* 接收最大为 *nbytes* 的数据报并放入 *buf*。 [`socket.recvfrom_into()`]({{< ref "/library/ipc/socket#socket.socket.recvfrom_into" >}}) 的异步版本。

​	返回一个 (已接收字节数, 远程地址) 元组。

*sock* 必须是个非阻塞socket。

> Added in version 3.11.
>

## *coroutine* loop.**sock_sendall**(*sock*, *data*)

​	将 *data* 发送到 *sock* 套接字。 [`socket.sendall()`]({{< ref "/library/ipc/socket#socket.socket.sendall" >}}) 的异步版本。

​	此方法会持续发送数据到套接字直至 *data* 中的所有数据发送完毕或是有错误发生。 当成功时会返回 `None`。 当发生错误时，会引发一个异常。 此外，没有办法能确定有多少数据或是否有数据被连接的接收方成功处理。

*sock* 必须是个非阻塞socket。

*在 3.7 版本发生变更:* 虽然这个方法一直被标记为协程方法。但是，Python 3.7 之前，该方法返回 [`Future`]({{< ref "/library/ipc/asyncio/asyncio-future#asyncio.Future" >}}) ，从Python 3.7 开始，这个方法是 `async def` 方法。

## *coroutine* loop.**sock_sendto**(*sock*, *data*, *address*)

​	从 *sock* 向 *address* 发送数据报。 [`socket.sendto()`]({{< ref "/library/ipc/socket#socket.socket.sendto" >}}) 的异步版本。

​	返回已发送的字节数。

*sock* 必须是个非阻塞socket。

> Added in version 3.11.
>

## *coroutine* loop.**sock_connect**(*sock*, *address*)

​	将 *sock* 连接到位于 *address* 的远程套接字。

[`socket.connect()`]({{< ref "/library/ipc/socket#socket.socket.connect" >}}) 的异步版本。

*sock* 必须是个非阻塞socket。

*在 3.5.2 版本发生变更:* `address` 不再需要被解析。 `sock_connect` 将尝试检查 *address* 是否已通过调用 [`socket.inet_pton()`]({{< ref "/library/ipc/socket#socket.inet_pton" >}}) 被解析。 如果没有，则将使用 [`loop.getaddrinfo()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.getaddrinfo" >}}) 来解析 *address*。

​	参见

 

[`loop.create_connection()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_connection" >}}) 和 [`asyncio.open_connection()`]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio.open_connection" >}}) 。

## *coroutine* loop.**sock_accept**(*sock*)

​	接受一个连接。 模仿了阻塞型的 [`socket.accept()`]({{< ref "/library/ipc/socket#socket.socket.accept" >}}) 方法。

​	此 *scoket* 必须绑定到一个地址上并且监听连接。返回值是一个 `(conn, address)` 对，其中 *conn* 是一个 *新\*的套接字对象，用于在此连接上收发数据，\*address* 是连接的另一端的套接字所绑定的地址。

*sock* 必须是个非阻塞socket。

*在 3.7 版本发生变更:* 虽然这个方法一直被标记为协程方法。但是，Python 3.7 之前，该方法返回 [`Future`]({{< ref "/library/ipc/asyncio/asyncio-future#asyncio.Future" >}}) ，从Python 3.7 开始，这个方法是 `async def` 方法。

​	参见

 

[`loop.create_server()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_server" >}}) 和 [`start_server()`]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio.start_server" >}})。

## *coroutine* loop.**sock_sendfile**(*sock*, *file*, *offset=0*, *count=None*, ***, *fallback=True*)

​	在可能的情况下使用高性能的 [`os.sendfile`]({{< ref "/library/allos/os#os.sendfile" >}}) 发送文件。 返回所发送的字节总数。

[`socket.sendfile()`]({{< ref "/library/ipc/socket#socket.socket.sendfile" >}}) 的异步版本。

*sock* 必须为非阻塞型的 [`socket.SOCK_STREAM`]({{< ref "/library/ipc/socket#socket.SOCK_STREAM" >}}) [`socket`]({{< ref "/library/ipc/socket#socket.socket" >}})。

*file* 必须是个用二进制方式打开的常规文件对象。

*offset* 指明从何处开始读取文件。 如果指定了 *count*，它是要传输的字节总数而不再一直发送文件直至抵达 EOF。 文件位置总是会被更新，即使此方法引发了错误，并可以使用 [`file.tell()`]({{< ref "/library/allos/io#io.IOBase.tell" >}}) 来获取实际发送的字节总数。

​	当 *fallback* 被设为 `True` 时，会使用 asyncio 在平台不支持 sendfile 系统调用时手动读取并发送文件（例如 Windows 或 Unix 上的 SSL 套接字）。

​	如果系统不支持 *sendfile* 并且 *fallback* 为 `False` ，引发 [`SendfileNotAvailableError`]({{< ref "/library/ipc/asyncio/asyncio-exceptions.#asyncio.SendfileNotAvailableError" >}}) 异常。

*sock* 必须是个非阻塞socket。

> Added in version 3.7.
>

### [DNS]({{< ref "/library/ipc/asyncio/asyncio-eventloop#id12" >}})

## *coroutine* loop.**getaddrinfo**(*host*, *port*, ***, *family=0*, *type=0*, *proto=0*, *flags=0*)

​	异步版的 [`socket.getaddrinfo()`]({{< ref "/library/ipc/socket#socket.getaddrinfo" >}}) 。

## *coroutine* loop.**getnameinfo**(*sockaddr*, *flags=0*)

​	异步版的 [`socket.getnameinfo()`]({{< ref "/library/ipc/socket#socket.getnameinfo" >}}) 。

​	备注

 

*getaddrinfo* 和 *getnameinfo* 均在内部通过循环的默认线程池执行器使用其同步版本。 当执行器饱和时，这些方法可能会遭遇延迟，对此高层级网络库可能报告为更多的超时。 为缓解此问题，可以考虑使用针对其他用户任务的自定义执行器，或者设置具有更多工作线程的默认执行器。

*在 3.7 版本发生变更:* *getaddrinfo* 和 *getnameinfo* 方法一直被标记返回一个协程，但是Python 3.7之前，实际返回的是 [`asyncio.Future`]({{< ref "/library/ipc/asyncio/asyncio-future#asyncio.Future" >}}) 对象。从Python 3.7 开始，这两个方法是协程。

### [使用管道]({{< ref "/library/ipc/asyncio/asyncio-eventloop#id13" >}})

## *coroutine* loop.**connect_read_pipe**(*protocol_factory*, *pipe*)

​	在事件循环中注册 *pipe* 的读取端。

*protocol_factory* 必须为一个返回 [asyncio 协议]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio-protocol" >}}) 实现的可调用对象。

*pipe* 是个 [类似文件型对象]({{< ref "/glossary/idx#term-file-object" >}}).

​	返回一对 `(transport, protocol)`，其中 *transport* 支持 [`ReadTransport`]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio.ReadTransport" >}}) 接口而 *protocol* 是由 *protocol_factory* 所实例化的对象。

​	使用 [`SelectorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.SelectorEventLoop" >}}) 事件循环， *pipe* 被设置为非阻塞模式。

## *coroutine* loop.**connect_write_pipe**(*protocol_factory*, *pipe*)

​	在事件循环中注册 *pipe* 的写入端。

*protocol_factory* 必须为一个返回 [asyncio 协议]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio-protocol" >}}) 实现的可调用对象。

*pipe* 是个 [类似文件型对象]({{< ref "/glossary/idx#term-file-object" >}}).

​	返回一对 `(transport, protocol)`，其中 *transport* 支持 [`WriteTransport`]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio.WriteTransport" >}}) 接口而 *protocol* 是由 *protocol_factory* 所实例化的对象。

​	使用 [`SelectorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.SelectorEventLoop" >}}) 事件循环， *pipe* 被设置为非阻塞模式。

​	备注

 

​	在 Windows 中 [`SelectorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.SelectorEventLoop" >}}) 不支持上述方法。 对于 Windows 请改用 [`ProactorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.ProactorEventLoop" >}})。

​	参见

 

[`loop.subprocess_exec()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.subprocess_exec" >}}) 和 [`loop.subprocess_shell()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.subprocess_shell" >}}) 方法。

### [Unix 信号]({{< ref "/library/ipc/asyncio/asyncio-eventloop#id14" >}})

## loop.**add_signal_handler**(*signum*, *callback*, **args*)

​	设置 *callback* 作为 *signum* 信号的处理程序。

​	此回调将与该事件循环中其他加入队列的回调和可运行协程一起由 *loop* 发起调用。 不同与使用 [`signal.signal()`]({{< ref "/library/ipc/signal#signal.signal" >}}) 注册的信号处理程序，使用此函数注册的回调可以与事件循环进行交互。

​	如果信号数字非法或者不可捕获，就抛出一个 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 。如果建立处理器的过程中出现问题，会抛出一个 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}}) 。

​	使用 [`functools.partial()`]({{< ref "/library/functional/functools#functools.partial" >}}) [传递关键字参数]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio-pass-keywords" >}}) 给 *callback*.

​	和 [`signal.signal()`]({{< ref "/library/ipc/signal#signal.signal" >}}) 一样，这个函数只能在主线程中调用。

## loop.**remove_signal_handler**(*sig*)

​	移除 *sig* 信号的处理程序。

​	如果信号处理程序被移除则返回 `True`，否则如果给定信号未设置处理程序则返回 `False`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

​	参见

 

[`signal`]({{< ref "/library/ipc/signal#module-signal" >}}) 模块。

### [在线程或者进程池中执行代码。]({{< ref "/library/ipc/asyncio/asyncio-eventloop#id15" >}})

## *awaitable* loop.**run_in_executor**(*executor*, *func*, **args*)

​	安排在指定的执行器中调用 *func* 。

*executor* 参数应当是一个 [`concurrent.futures.Executor`]({{< ref "/library/concurrency/future/concurrent_futures#concurrent.futures.Executor" >}}) 实例。 如果 *executor* 为 `None` 则为使用默认执行器。 默认执行器可通过 [`loop.set_default_executor()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.set_default_executor" >}}) 来设置，在其他情况下，将在有需要时惰性初始化 [`concurrent.futures.ThreadPoolExecutor`]({{< ref "/library/concurrency/future/concurrent_futures#concurrent.futures.ThreadPoolExecutor" >}}) 并由 [`run_in_executor()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.run_in_executor" >}}) 来使用。

​	示例:

```
import asyncio
import concurrent.futures

def blocking_io():
    # 文件操作（如日志记录）会阻塞事件循环：
    # 应在线程池中运行它们。
    with open('/dev/urandom', 'rb') as f:
        return f.read(100)

def cpu_bound():
    # CPU 密集型操作会阻塞事件循环：
    # 通常建议在进程中运行它们。
    return sum(i * i for i in range(10 ** 7))

async def main():
    loop = asyncio.get_running_loop()

    ## Options:

    # 1. 运行默认循环的执行器：
    result = await loop.run_in_executor(
        None, blocking_io)
    print('default thread pool', result)

    # 2. 在自定义的线程池中运行：
    with concurrent.futures.ThreadPoolExecutor() as pool:
        result = await loop.run_in_executor(
            pool, blocking_io)
        print('custom thread pool', result)

    # 3. 在自定义的进程池中运行：
    with concurrent.futures.ProcessPoolExecutor() as pool:
        result = await loop.run_in_executor(
            pool, cpu_bound)
        print('custom process pool', result)

if __name__ == '__main__':
    asyncio.run(main())
```

​	请注意需要为选项 3 设置入口点保护 (`if __name__ == '__main__'`)，这是由于 [`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 的特殊性，它将由 [`ProcessPoolExecutor`]({{< ref "/library/concurrency/future/concurrent_futures#concurrent.futures.ProcessPoolExecutor" >}}) 来使用。 参见 [主模块的安全导入]({{< ref "/library/concurrency/multiprocessing#multiprocessing-safe-main-import" >}})。

​	这个方法返回一个 [`asyncio.Future`]({{< ref "/library/ipc/asyncio/asyncio-future#asyncio.Future" >}}) 对象。

​	使用 [`functools.partial()`]({{< ref "/library/functional/functools#functools.partial" >}}) [传递关键字参数]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio-pass-keywords" >}}) 给 *func* 。

*在 3.5.3 版本发生变更:* [`loop.run_in_executor()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.run_in_executor" >}}) 不会再配置它所创建的线程池执行器的 `max_workers`，而是将其留给线程池执行器 ([`ThreadPoolExecutor`]({{< ref "/library/concurrency/future/concurrent_futures#concurrent.futures.ThreadPoolExecutor" >}})) 来设置默认值。

## loop.**set_default_executor**(*executor*)

​	将 *executor* 设为 [`run_in_executor()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.run_in_executor" >}}) 所使用的默认执行器。 *executor* 必须是 [`ThreadPoolExecutor`]({{< ref "/library/concurrency/future/concurrent_futures#concurrent.futures.ThreadPoolExecutor" >}}) 的实例。

*在 3.11 版本发生变更:* *executor* 必须是 [`ThreadPoolExecutor`]({{< ref "/library/concurrency/future/concurrent_futures#concurrent.futures.ThreadPoolExecutor" >}}) 的实例。

### [错误处理API]({{< ref "/library/ipc/asyncio/asyncio-eventloop#id16" >}})

​	允许自定义事件循环中如何去处理异常。

## loop.**set_exception_handler**(*handler*)

​	将 *handler* 设置为新的事件循环异常处理器。

​	如果 *handler* 为 `None`，将设置默认的异常处理程序。 在其他情况下，*handler* 必须是一个可调用对象且签名匹配 `(loop, context)`，其中 `loop` 是对活动事件循环的引用，而 `context` 是一个包含异常详情的 `dict` (请查看 [`call_exception_handler()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.call_exception_handler" >}}) 文档来获取关于上下文的更多信息)。

​	如果针对一个 [`Task`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}}) 或 [`Handle`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.Handle" >}}) 调用了该异常处理器，它将在相应任务或回调句柄的 [`contextvars.Context`]({{< ref "/library/concurrency/contextvars#contextvars.Context" >}}) 中运行。

*在 3.12 版本发生变更:* 该处理器可能会在发生异常的任务或句柄的 [`Context`]({{< ref "/library/concurrency/contextvars#contextvars.Context" >}}) 中被调用。

## loop.**get_exception_handler**()

​	返回当前的异常处理器，如果没有设置异常处理器，则返回 `None` 。

> Added in version 3.5.2.
>

## loop.**default_exception_handler**(*context*)

​	默认的异常处理器。

​	此方法会在发生异常且未设置异常处理程序时被调用。 此方法也可以由想要具有不同于默认处理程序的行为的自定义异常处理程序来调用。

*context* 参数和 [`call_exception_handler()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.call_exception_handler" >}}) 中的同名参数完全相同。

## loop.**call_exception_handler**(*context*)

​	调用当前事件循环的异常处理器。

*context* 是个包含下列键的 `dict` 对象(未来版本的Python可能会引入新键)：

- 'message': 错误消息；

- 'exception' （可选）: 异常对象；

- 'future' （可选）: [`asyncio.Future`]({{< ref "/library/ipc/asyncio/asyncio-future#asyncio.Future" >}}) 实例；

- 'task' (可选): [`asyncio.Task`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}}) 实例；

- 'handle' （可选）: [`asyncio.Handle`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.Handle" >}}) 实例；

- 'protocol' （可选）: [Protocol]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio-protocol" >}}) 实例；

- 'transport' （可选）: [Transport]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio-transport" >}}) 实例；

- 'socket' (可选): [`socket.socket`]({{< ref "/library/ipc/socket#socket.socket" >}}) 实例；

- ## 'asyncgen' (可选): 异步生成器，它导致了

  ​	这个异常

​	备注

 

​	此方法不应在子类化的事件循环中被重载。 对于自定义的异常处理，请使用 [`set_exception_handler()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.set_exception_handler" >}}) 方法。

### [开启调试模式]({{< ref "/library/ipc/asyncio/asyncio-eventloop#id17" >}})

## loop.**get_debug**()

​	获取事件循环调试模式设置（[`bool`]({{< ref "/library/functions#bool" >}})）。

​	如果环境变量 [`PYTHONASYNCIODEBUG`]({{< ref "/using/cmdline#envvar-PYTHONASYNCIODEBUG" >}}) 是一个非空字符串，就返回 `True` ，否则就返回 `False` 。

## loop.**set_debug**(*enabled: [bool]({{< ref "/library/functions#bool" >}})*)

​	设置事件循环的调试模式。

*在 3.7 版本发生变更:* 现在也可以通过新的 [Python 开发模式]({{< ref "/library/development/devmode#devmode" >}}) 来启用调试模式。

## loop.**slow_callback_duration**

​	该属性可用于设置会被视为“缓慢”的以秒数表示的最短执行时间。 当启用调试模式时，“缓慢”的回调将被记录到日志。

​	默认值为 100 毫秒。

​	参见

 

[debug mode of asyncio]({{< ref "/library/ipc/asyncio/asyncio-dev#asyncio-debug-mode" >}}).

### [运行子进程]({{< ref "/library/ipc/asyncio/asyncio-eventloop#id18" >}})

​	本小节所描述的方法都是低层级的。 在常规 async/await 代码中请考虑改用高层级的 [`asyncio.create_subprocess_shell()`]({{< ref "/library/ipc/asyncio/asyncio-subprocess#asyncio.create_subprocess_shell" >}}) 和 [`asyncio.create_subprocess_exec()`]({{< ref "/library/ipc/asyncio/asyncio-subprocess#asyncio.create_subprocess_exec" >}}) 便捷函数。

​	备注

 

​	在 Windows 中，默认的事件循环 [`ProactorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.ProactorEventLoop" >}}) 支持子进程，而 [`SelectorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.SelectorEventLoop" >}}) 不支持。参见 [Windows 中的子进程支持]({{< ref "/library/ipc/asyncio/asyncio-platforms#asyncio-windows-subprocess" >}}) 。

## *coroutine* loop.**subprocess_exec**(*protocol_factory*, **args*, *stdin=subprocess.PIPE*, *stdout=subprocess.PIPE*, *stderr=subprocess.PIPE*, ***kwargs*)

​	用 *args* 指定的一个或者多个字符串型参数创建一个子进程。

*args* 必须是个由下列形式的字符串组成的列表：

- [`str`]({{< ref "/library/stdtypes#str" >}});
- 或者由 [文件系统编码格式]({{< ref "/library/allos/os#filesystem-encoding" >}}) 编码的 [`bytes`]({{< ref "/library/stdtypes#bytes" >}})。

​	第一个字符串指定可执行程序，其余的字符串指定其参数。 所有字符串参数共同组成了程序的 `argv`。

​	此方法类似于调用标准库 [`subprocess.Popen`]({{< ref "/library/concurrency/subprocess#subprocess.Popen" >}}) 类，设置 `shell=False` 并将字符串列表作为第一个参数传入；但是，[`Popen`]({{< ref "/library/concurrency/subprocess#subprocess.Popen" >}}) 只接受一个单独的字符串列表参数，而 *subprocess_exec* 接受多个字符串参数。

*protocol_factory* 必须为一个返回 [`asyncio.SubprocessProtocol`]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio.SubprocessProtocol" >}}) 类的子类的可调用对象。

​	其他参数：

- *stdin* 可以是以下对象之一:

  - 一个文件型对象
  - 一个现有的文件描述符（一个正整数），例如用 [`os.pipe()`]({{< ref "/library/allos/os#os.pipe" >}}) 创建的文件描述符
  - [`subprocess.PIPE`]({{< ref "/library/concurrency/subprocess#subprocess.PIPE" >}}) 常量（默认），将创建并连接一个新的管道。
  - `None` 值，这将使得子进程继承来自此进程的文件描述符
  - [`subprocess.DEVNULL`]({{< ref "/library/concurrency/subprocess#subprocess.DEVNULL" >}}) 常量，这表示将使用特殊的 [`os.devnull`]({{< ref "/library/allos/os#os.devnull" >}}) 文件

- *stdout* 可以是以下对象之一:

  - 一个文件型对象
  - [`subprocess.PIPE`]({{< ref "/library/concurrency/subprocess#subprocess.PIPE" >}}) 常量（默认），将创建并连接一个新的管道。
  - `None` 值，这将使得子进程继承来自此进程的文件描述符
  - [`subprocess.DEVNULL`]({{< ref "/library/concurrency/subprocess#subprocess.DEVNULL" >}}) 常量，这表示将使用特殊的 [`os.devnull`]({{< ref "/library/allos/os#os.devnull" >}}) 文件

- *stderr* 可以是以下对象之一:

  - 一个文件型对象
  - [`subprocess.PIPE`]({{< ref "/library/concurrency/subprocess#subprocess.PIPE" >}}) 常量（默认），将创建并连接一个新的管道。
  - `None` 值，这将使得子进程继承来自此进程的文件描述符
  - [`subprocess.DEVNULL`]({{< ref "/library/concurrency/subprocess#subprocess.DEVNULL" >}}) 常量，这表示将使用特殊的 [`os.devnull`]({{< ref "/library/allos/os#os.devnull" >}}) 文件
  - [`subprocess.STDOUT`]({{< ref "/library/concurrency/subprocess#subprocess.STDOUT" >}}) 常量，将把标准错误流连接到进程的标准输出流

- 所有其他关键字参数会被不加解释地传给 [`subprocess.Popen`]({{< ref "/library/concurrency/subprocess#subprocess.Popen" >}})，除了 *bufsize*, *universal_newlines*, *shell*, *text*, *encoding* 和 *errors*，它们都不应当被指定。

  `asyncio` 子进程 API 不支持将流解码为文本。 可以使用 [`bytes.decode()`]({{< ref "/library/stdtypes#bytes.decode" >}}) 来将从流返回的字节串转换为文本。

​	如果作为 *stdin*, *stdout* 或 *stderr* 传入的文件型对象是代表一个管道，则该管道的另一端应当用 [`connect_write_pipe()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.connect_write_pipe" >}}) 或 [`connect_read_pipe()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.connect_read_pipe" >}}) 来注册以配合事件循环使用。

​	其他参数的文档，请参阅 [`subprocess.Popen`]({{< ref "/library/concurrency/subprocess#subprocess.Popen" >}}) 类的构造函数。

​	返回一对 `(transport, protocol)`，其中 *transport* 来自 [`asyncio.SubprocessTransport`]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio.SubprocessTransport" >}}) 基类而 *protocol* 是由 *protocol_factory* 所实例化的对象。

## *coroutine* loop.**subprocess_shell**(*protocol_factory*, *cmd*, ***, *stdin=subprocess.PIPE*, *stdout=subprocess.PIPE*, *stderr=subprocess.PIPE*, ***kwargs*)

​	基于 *cmd* 创建一个子进程，该参数可以是一个 [`str`]({{< ref "/library/stdtypes#str" >}}) 或者按 [文件系统编码格式]({{< ref "/library/allos/os#filesystem-encoding" >}}) 编码得到的 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) ，使用平台的 "shell" 语法。

​	这类似与用 `shell=True` 调用标准库的 [`subprocess.Popen`]({{< ref "/library/concurrency/subprocess#subprocess.Popen" >}}) 类。

*protocol_factory* 必须为一个返回 [`SubprocessProtocol`]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio.SubprocessProtocol" >}}) 类的子类的可调用对象。

​	请参阅 [`subprocess_exec()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.subprocess_exec" >}}) 了解有关其余参数的详情。

​	返回一对 `(transport, protocol)`，其中 *transport* 来自 [`SubprocessTransport`]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio.SubprocessTransport" >}}) 基类而 *protocol* 是由 *protocol_factory* 所实例化的对象。

​	备注

 

​	应用程序要负责确保正确地转义所有空白字符和特殊字符以防止 [shell 注入](https://en.wikipedia.org/wiki/Shell_injection#Shell_injection) 漏洞。 [`shlex.quote()`]({{< ref "/library/frameworks/shlex#shlex.quote" >}}) 函数可以被用来正确地转义字符串中可能被用来构造 shell 命令的空白字符和特殊字符。

## 回调处理

## *class* asyncio.**Handle**

​	由 [`loop.call_soon()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.call_soon" >}}), [`loop.call_soon_threadsafe()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.call_soon_threadsafe" >}}) 所返回的回调包装器对象。

## **get_context**()

​	返回关联到该句柄的 [`contextvars.Context`]({{< ref "/library/concurrency/contextvars#contextvars.Context" >}}) 对象。

> Added in version 3.12.
>

## **cancel**()

​	取消回调。 如果此回调已被取消或已被执行，此方法将没有任何效果。

## **cancelled**()

​	如果此回调已被取消则返回 `True`。

> Added in version 3.7.
>

## *class* asyncio.**TimerHandle**

​	由 [`loop.call_later()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.call_later" >}}) 和 [`loop.call_at()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.call_at" >}}) 所返回的回调包装器对象。

​	这个类是 [`Handle`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.Handle" >}}) 的子类。

## **when**()

​	返回加入计划任务的回调时间，以 [`float`]({{< ref "/library/functions#float" >}}) 值表示的秒数。

​	时间值是一个绝对时间戳，使用与 [`loop.time()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.time" >}}) 相同的时间引用。

> Added in version 3.7.
>

## Server 对象

​	Server 对象可使用 [`loop.create_server()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_server" >}}), [`loop.create_unix_server()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_unix_server" >}}), [`start_server()`]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio.start_server" >}}) 和 [`start_unix_server()`]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio.start_unix_server" >}}) 等函数来创建。

​	请不要直接实例化 [`Server`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.Server" >}}) 类。

## *class* asyncio.**Server**

*Server* 对象是异步上下文管理器。当用于 `async with` 语句时，异步上下文管理器可以确保 Server 对象被关闭，并且在 `async with` 语句完成后，不接受新的连接。

```
srv = await loop.create_server(...)

async with srv:
    # 一些代码

# 此时，srv 已关闭并不再接受新的连接。
```

*在 3.7 版本发生变更:* Python3.7 开始，Server 对象是一个异步上下文管理器。

*在 3.11 版本发生变更:* 这个类在 Python 3.9.11, 3.10.3 和 3.11 中作为 `asyncio.Server` 对外公开。

## **close**()

​	停止服务：关闭监听的套接字并且设置 [`sockets`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.Server.sockets" >}}) 属性为 `None` 。

​	用于表示已经连进来的客户端连接会保持打开的状态。

​	服务器是被异步关闭的；使用 [`wait_closed()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.Server.wait_closed" >}}) 协程来等待服务器关闭（将不再有激活的连接）。

## **close_clients**()

​	关闭所有的外来客户端连接。

​	在所有关联的传输上调用 [`close()`]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio.BaseTransport.close" >}})。

​	当关闭服务器时 [`close()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.Server.close" >}}) 应当在 [`close_clients()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.Server.close_clients" >}}) 之前被调用以避免与新的客户端连接发生竞争。

> Added in version 3.13.
>

## **abort_clients**()

​	立即关闭所有当前的外来客户端连接，无需等待挂起的操作完成。

​	在所有关联的传输上调用 [`abort()`]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio.WriteTransport.abort" >}})。

​	当关闭服务器时 [`close()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.Server.close" >}}) 应当在 [`abort_clients()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.Server.abort_clients" >}}) 之前被调用以避免与新的服务器连接发生竞争。

> Added in version 3.13.
>

## **get_loop**()

​	返回与服务器对象相关联的事件循环。

> Added in version 3.7.
>

## *coroutine* **start_serving**()

​	开始接受连接。

​	此方法是幂等的，所以它可在服务器已在运行时被调用。

​	传给 [`loop.create_server()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_server" >}}) 和 [`asyncio.start_server()`]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio.start_server" >}}) 的 *start_serving* 仅限关键字形参允许创建不接受初始连接的 Server 对象。 在此情况下可以使用 `Server.start_serving()` 或 [`Server.serve_forever()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.Server.serve_forever" >}}) 让 Server 对象开始接受连接。

> Added in version 3.7.
>

## *coroutine* **serve_forever**()

​	开始接受连接，直到协程被取消。 `serve_forever` 任务的取消将导致服务器被关闭。

​	如果服务器已经在接受连接了，这个方法可以被调用。每个 *Server* 对象，仅能有一个 `serve_forever` 任务。

​	示例:

```
async def client_connected(reader, writer):
    # 使用 reader/writer 流与客户端通信。 例如：
    await reader.readline()

async def main(host, port):
    srv = await asyncio.start_server(
        client_connected, host, port)
    await srv.serve_forever()

asyncio.run(main('127.0.0.1', 0))
```

> Added in version 3.7.
>

## **is_serving**()

​	如果服务器正在接受新连接的状态，返回 `True` 。

> Added in version 3.7.
>

## *coroutine* **wait_closed**()

​	等待直到 [`close()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.Server.close" >}}) 方法完成且所有活动的连接结束。

## **sockets**

​	由类套接字对象组成的列表 `asyncio.trsock.TransportSocket`，服务器将监听这些对象。

*在 3.7 版本发生变更:* 在 Python 3.7 之前 `Server.sockets` 会直接返回内部的服务器套接字列表。 在 3.7 版则会返回该列表的副本。



## 事件循环实现

​	asyncio 带有两种不同的事件循环实现: [`SelectorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.SelectorEventLoop" >}}) 和 [`ProactorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.ProactorEventLoop" >}})。

​	在默认情况下 asyncio 将被配置为使用 [`EventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.EventLoop" >}})。

## *class* asyncio.**SelectorEventLoop**

​	基于 [`selectors`]({{< ref "/library/ipc/selectors#module-selectors" >}}) 模块的 [`AbstractEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.AbstractEventLoop" >}}) 的子类。

​	使用给定平台中最高效的可用 *selector*。 也可以手动配置要使用的特定 selector:

```
import asyncio
import selectors

class MyPolicy(asyncio.DefaultEventLoopPolicy):
   def new_event_loop(self):
      selector = selectors.SelectSelector()
      return asyncio.SelectorEventLoop(selector)

asyncio.set_event_loop_policy(MyPolicy())
```

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows.

## *class* asyncio.**ProactorEventLoop**

​	使用 "I/O Completion Ports" (IOCP) 的针对 Windows 的 [`AbstractEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.AbstractEventLoop" >}}) 子类。

[Availability]({{< ref "/library/intro#availability" >}}): Windows.

​	参见

 

[MSDN documentation on I/O Completion Ports](https://learn.microsoft.com/windows/win32/fileio/i-o-completion-ports).

## *class* asyncio.**EventLoop**

> ​	在给定平台上可用的最高效的 [`AbstractEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.AbstractEventLoop" >}}) 子类的别名。
>
> ​	它在 Unix 上是 [`SelectorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.SelectorEventLoop" >}}) 的别名而在 Windows 上是 [`ProactorEventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.ProactorEventLoop" >}}) 的别名。

> Added in version 3.13.
>

## *class* asyncio.**AbstractEventLoop**

​	asyncio 兼容事件循环的抽象基类。

[事件循环方法集]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio-event-loop-methods" >}}) 一节列出了 `AbstractEventLoop` 的替代实现应当要定义的所有方法。

## 例子

​	请注意本节中的所有示例都 **有意地** 演示了如何使用低层级的事件循环 API，例如 [`loop.run_forever()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.run_forever" >}}) 和 [`loop.call_soon()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.call_soon" >}})。 现代的 asyncio 应用很少需要以这样的方式编写；请考虑使用高层级的函数例如 [`asyncio.run()`]({{< ref "/library/ipc/asyncio/asyncio-runner.#asyncio.run" >}})。



### call_soon() 的 Hello World 示例。

​	一个使用 [`loop.call_soon()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.call_soon" >}}) 方法来安排回调的示例。 回调会显示 `"Hello World"` 然后停止事件循环:

```
import asyncio

def hello_world(loop):
    """打印 'Hello World' 并停止事件循环的回调"""
    print('Hello World')
    loop.stop()

loop = asyncio.new_event_loop()

# 将对 hello_world() 的调用加入计划任务
loop.call_soon(hello_world, loop)

# 阻塞调用被 loop.stop() 中断
try:
    loop.run_forever()
finally:
    loop.close()
```

​	参见

 

​	一个类似的 [Hello World]({{< ref "/library/ipc/asyncio/asyncio-task#coroutine" >}}) 示例，使用协程和 [`run()`]({{< ref "/library/ipc/asyncio/asyncio-runner.#asyncio.run" >}}) 函数创建。



### 使用 call_later() 来展示当前的日期

​	一个每秒刷新显示当前日期的示例。 回调使用 [`loop.call_later()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.call_later" >}}) 方法在 5 秒后将自身重新加入计划日程，然后停止事件循环:

```
import asyncio
import datetime

def display_date(end_time, loop):
    print(datetime.datetime.now())
    if (loop.time() + 1.0) < end_time:
        loop.call_later(1, display_date, end_time, loop)
    else:
        loop.stop()

loop = asyncio.new_event_loop()

# 将对 display_date() 的第一次调用加入计划任务
end_time = loop.time() + 5.0
loop.call_soon(display_date, end_time, loop)

# 被 loop.stop() 中断的阻塞调用
try:
    loop.run_forever()
finally:
    loop.close()
```

​	参见

 

​	一个类似的 [current date]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio-example-sleep" >}}) 示例，使用协程和 [`run()`]({{< ref "/library/ipc/asyncio/asyncio-runner.#asyncio.run" >}}) 函数创建。



### 监控一个文件描述符的读事件

​	使用 [`loop.add_reader()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.add_reader" >}}) 方法，等到文件描述符收到一些数据，然后关闭事件循环：

```
import asyncio
from socket import socketpair

# 创建一对已连接的文件描述符
rsock, wsock = socketpair()

loop = asyncio.new_event_loop()

def reader():
    data = rsock.recv(100)
    print("Received:", data.decode())

    # 已完成：注销文件描述符
    loop.remove_reader(rsock)

    # 停止事件循环
    loop.stop()

# 为读取事件注册文件描述符
loop.add_reader(rsock, reader)

# 模拟从网络接收数据
loop.call_soon(wsock.send, 'abc'.encode())

try:
    # 运行事件循环
    loop.run_forever()
finally:
    # 已完成。 关闭套接字和事件循环。
    rsock.close()
    wsock.close()
    loop.close()
```

​	参见

- 一个类似的 [示例]({{< ref "/library/ipc/asyncio/asyncio-protocol.#asyncio-example-create-connection" >}})，使用传输、协议和 [`loop.create_connection()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_connection" >}}) 方法创建。
- 另一个类似的 [示例]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio-example-create-connection-streams" >}})，使用了高层级的 [`asyncio.open_connection()`]({{< ref "/library/ipc/asyncio/asyncio-stream#asyncio.open_connection" >}}) 函数和流。



### 为SIGINT和SIGTERM设置信号处理器

​	（这个 `signals` 示例只适用于 Unix。）

​	使用 [`loop.add_signal_handler()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.add_signal_handler" >}}) 方法为信号 [`SIGINT`]({{< ref "/library/ipc/signal#signal.SIGINT" >}}) 和 [`SIGTERM`]({{< ref "/library/ipc/signal#signal.SIGTERM" >}}) 注册处理器:

```
import asyncio
import functools
import os
import signal

def ask_exit(signame, loop):
    print("got signal %s: exit" % signame)
    loop.stop()

async def main():
    loop = asyncio.get_running_loop()

    for signame in {'SIGINT', 'SIGTERM'}:
        loop.add_signal_handler(
            getattr(signal, signame),
            functools.partial(ask_exit, signame, loop))

    await asyncio.sleep(3600)

print("Event loop running for 1 hour, press Ctrl+C to interrupt.")
print(f"pid {os.getpid()}: send SIGINT or SIGTERM to exit.")

asyncio.run(main())
```
