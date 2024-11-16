+++
title = "select --- 等待 I/O 完成"
date = 2024-11-15T12:30:27+08:00
weight = 30
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/select.html](https://docs.python.org/zh-cn/3.13/library/select.html)
>
> 收录该文档的时间：`2024-11-15T12:30:27+08:00`

# `select` --- 等待 I/O 完成

------

​	该模块提供了对 `select()` 和 `poll()` 函数的访问，这在大多数操作系统上都是可用的，`devpoll()` 在 Solaris 及其衍生系统上可用，`epoll()` 在 Linux 2.5+ 上可用，而 `kqueue()` 在大多数 BSD 上可用。 注意在 Windows 上，它仅适用于套接字；在其他操作系统上，它还适用于其他文件类型（特别是在 Unix 上，它还适用于管道）。 它不能被用在常规文件上确定一个文件自其最后一次被读取后大小是否有增长。

​	备注

 

[`selectors`]({{< ref "/library/ipc/selectors#module-selectors" >}}) 模块是在 [`select`]({{< ref "/library/ipc/select#module-select" >}}) 模块原型的基础上进行高级且高效的 I/O 复用。推荐用户改用 [`selectors`]({{< ref "/library/ipc/selectors#module-selectors" >}}) 模块，除非用户希望对 OS 级的函数原型进行精确控制。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

​	此模块在 WebAssembly 平台上无效或不可用。 请参阅 [WebAssembly 平台]({{< ref "/library/intro#wasm-availability" >}}) 了解详情。

​	该模块定义以下内容：

## *exception* select.**error**

​	一个被弃用的 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 的别名。

*在 3.3 版本发生变更:* 根据 [**PEP 3151**](https://peps.python.org/pep-3151/)，这个类是 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 的别名。

## select.**devpoll**()

​	（仅支持 Solaris 及其衍生版本）返回一个 `/dev/poll` 轮询对象，请参阅下方 [/dev/poll 轮询对象]({{< ref "/library/ipc/select#devpoll-objects" >}}) 获取 devpoll 对象所支持的方法。

`devpoll()` 对象与实例化时允许的文件描述符数量相关联。 如果你的程序减少该值，`devpoll()` 将会失败。 如果你的程序增加该值，`devpoll()` 可能会返回不完整的活动文件描述符列表。

​	新的文件描述符是 [不可继承的]({{< ref "/library/allos/os#fd-inheritance" >}})。

> Added in version 3.3.
>

*在 3.4 版本发生变更:* 新的文件描述符现在是不可继承的。

## select.**epoll**(*sizehint=-1*, *flags=0*)

​	（仅支持 Linux 2.5.44 或更高版本）返回一个 edge poll 对象，该对象可作为 I/O 事件的边缘触发或水平触发接口。

*sizehint* 通知 epoll 预计要注册的事件数量。 该值必须为正数，或为 `-1` 以使用默认值。 它仅在 `epoll_create1()` 不可用的旧系统上会被使用，在其他情况下它没有任何作用（尽管仍会检查其值）。

*flags* 已经弃用且完全被忽略。但是，如果提供该值，则它必须是 `0` 或 `select.EPOLL_CLOEXEC`，否则会抛出 `OSError` 异常。

​	请参阅下方 [边缘触发和水平触发的轮询 (epoll) 对象]({{< ref "/library/ipc/select#epoll-objects" >}}) 获取 epoll 对象所支持的方法。

`epoll` 对象支持上下文管理器：当在 [`with`]({{< ref "/reference/compound_stmts#with" >}}) 语句中使用时，新建的文件描述符会在运行至语句块结束时自动关闭。

​	新的文件描述符是 [不可继承的]({{< ref "/library/allos/os#fd-inheritance" >}})。

*在 3.3 版本发生变更:* 增加了 *flags* 参数。

*在 3.4 版本发生变更:* 增加了对 [`with`]({{< ref "/reference/compound_stmts#with" >}}) 语句的支持。新的文件描述符现在是不可继承的。

*自 3.4 版本弃用:* *flags* 参数。现在默认采用 `select.EPOLL_CLOEXEC` 标志。使用 [`os.set_inheritable()`]({{< ref "/library/allos/os#os.set_inheritable" >}}) 来让文件描述符可继承。

## select.**poll**()

​	（部分操作系统不支持）返回一个 poll 对象，该对象支持注册和注销文件描述符，支持对描述符进行轮询以获取 I/O 事件。请参阅下方 [Poll 对象]({{< ref "/library/ipc/select#poll-objects" >}}) 获取 poll 对象所支持的方法。

## select.**kqueue**()

​	（仅支持 BSD）返回一个内核队列对象，请参阅下方 [Kqueue 对象]({{< ref "/library/ipc/select#kqueue-objects" >}}) 获取 kqueue 对象所支持的方法。

​	新的文件描述符是 [不可继承的]({{< ref "/library/allos/os#fd-inheritance" >}})。

*在 3.4 版本发生变更:* 新的文件描述符现在是不可继承的。

## select.**kevent**(*ident*, *filter=KQ_FILTER_READ*, *flags=KQ_EV_ADD*, *fflags=0*, *data=0*, *udata=0*)

​	（仅支持 BSD）返回一个内核事件对象，请参阅下方 [Kevent 对象]({{< ref "/library/ipc/select#kevent-objects" >}}) 获取 kevent 对象所支持的方法。

## select.**select**(*rlist*, *wlist*, *xlist*[, *timeout*])

​	这是一个明白直观的 Unix `select()` 系统调用接口。 前三个参数是产生“可等待对象”的可迭代对象：可以是代表文件描述符的整数，或是带有名为 [`fileno()`]({{< ref "/library/allos/io#io.IOBase.fileno" >}}) 的返回这样的整数的无形参方法的对象:

- *rlist*：等待，直到可以开始读取
- *wlist*：等待，直到可以开始写入
- *xlist*：等待“异常情况”（请参阅当前系统的手册，以获取哪些情况称为异常情况）

​	允许空的可迭代对象，但是否接受三个空的可迭代对象则取决于具体平台。 （已知在 Unix 上可行但在 Windows 上不可行。） 可选的 *timeout* 参数以一个浮点数表示超时秒数。 当省略 *timeout* 参数时该函数将阻塞直到至少有一个文件描述符准备就绪。 超时值为零表示执行轮询且永不阻塞。

​	返回值是三个列表，包含已就绪对象，返回的三个列表是前三个参数的子集。当超时时间已到且没有文件描述符就绪时，返回三个空列表。

​	可迭代对象中可接受的对象类型有 Python [文件对象]({{< ref "/glossary/idx#term-file-object" >}}) (例如 `sys.stdin` 以及 [`open()`]({{< ref "/library/functions#open" >}}) 或 [`os.popen()`]({{< ref "/library/allos/os#os.popen" >}}) 所返回的对象)，由 [`socket.socket()`]({{< ref "/library/ipc/socket#socket.socket" >}}) 返回的套接字对象等。 你也可以自定义一个 *wrapper* 类，只要它具有适当的 [`fileno()`]({{< ref "/library/allos/io#io.IOBase.fileno" >}}) 方法（该方法要确实返回一个文件描述符，而不能只是一个随机整数）。

​	备注

 

​	在 Windows 上不接受文件对象，但可以接受套接字。 在 Windows 上，底层的 `select()` 函数由 WinSock 库提供，且不会处理不是源自 WinSock 的文件描述符。

*在 3.5 版本发生变更:* 现在，当本函数被信号中断时，重试超时将从头开始计时，不会抛出 [`InterruptedError`]({{< ref "/library/exceptions#InterruptedError" >}}) 异常。除非信号处理程序抛出异常（相关原理请参阅 [**PEP 475**](https://peps.python.org/pep-0475/)）。

## select.**PIPE_BUF**

​	当 [`select()`]({{< ref "/library/ipc/select#select.select" >}})、`poll()` 或本模块中的其他接口报告管道已准备就绪可以写入时，可以在不阻塞该管道的情况下的最小字节数。 它这不适用于其他文件型对象，例如如套接字。

​	POSIX 上须保证该值不小于 512。

[Availability]({{< ref "/library/intro#availability" >}}): Unix

> Added in version 3.2.
>



## `/dev/poll` 轮询对象

​	Solaris 及其衍生版本具有 `/dev/poll`。 而 `select()` 为 *O*(*最高文件描述符*) 并且 `poll()` 为 *O*(*文件描述符数量*), `/dev/poll` 为 *O*(*活动的文件描述符*)。

`/dev/poll` 的行为非常接近标准 `poll()` 对象。

## devpoll.**close**()

​	关闭轮询对象的文件描述符。

> Added in version 3.4.
>

## devpoll.**closed**

​	如果轮询对象已关闭，则返回 `True`。

> Added in version 3.4.
>

## devpoll.**fileno**()

​	返回轮询对象的文件描述符对应的数字。

> Added in version 3.4.
>

## devpoll.**register**(*fd*[, *eventmask*])

​	在轮询对象中注册文件描述符。这样，将来调用 [`poll()`]({{< ref "/library/ipc/select#select.poll" >}}) 方法时将检查文件描述符是否有未处理的 I/O 事件。*fd* 可以是整数，也可以是带有 [`fileno()`]({{< ref "/library/allos/io#io.IOBase.fileno" >}}) 方法的对象（该方法返回一个整数）。文件对象已经实现了 `fileno()`，因此它们也可以用作参数。

*eventmask* 是可选的位掩码，用于描述要检查的事件类型。 这些常量与 `poll()` 对象的相同。 默认值是常量 `POLLIN`、`POLLPRI` 和 `POLLOUT` 的组合。

​	警告

 

​	注册已注册的文件描述符不会报错，但结果是未定义的。 适当的做法是先注销或修改它。 这是与:c:func:!poll 的一个重要区别。

## devpoll.**modify**(*fd*[, *eventmask*])

​	此方法先执行 [`unregister()`]({{< ref "/library/ipc/select#select.devpoll.unregister" >}}) 后执行 [`register()`]({{< ref "/library/ipc/select#select.devpoll.register" >}})。直接执行此操作效率（稍微）高一些。

## devpoll.**unregister**(*fd*)

​	删除轮询对象正在跟踪的某个文件描述符。与 [`register()`]({{< ref "/library/ipc/select#select.devpoll.register" >}}) 方法类似，*fd* 可以是整数，也可以是带有 [`fileno()`]({{< ref "/library/allos/io#io.IOBase.fileno" >}}) 方法的对象（该方法返回一个整数）。

​	尝试删除从未注册过的文件描述符将被安全地忽略。

## devpoll.**poll**([*timeout*])

​	轮询已注册的文件描述符的集合，并返回一个列表，列表可能为空，也可能有多个 `(fd, event)` 2元组，其中包含了要报告事件或错误的描述符。 *fd* 是文件描述符，*event* 是一个位掩码，表示该描述符所报告的事件 --- `POLLIN` 表示等待输入，`POLLOUT` 表示该描述符可以写入，依此类推。 空列表表示调用超时，没有任何文件描述符报告事件。 如果指定了 *timeout*，它将指定系统等待事件时，等待多长时间后返回（以毫秒为单位）。如果 *timeout* 被省略、为 -1 或为 [`None`]({{< ref "/library/constants#None" >}})，则本调用将阻塞，直到轮询对象发生事件为止。

*在 3.5 版本发生变更:* 现在，当本函数被信号中断时，重试超时将从头开始计时，不会抛出 [`InterruptedError`]({{< ref "/library/exceptions#InterruptedError" >}}) 异常。除非信号处理程序抛出异常（相关原理请参阅 [**PEP 475**](https://peps.python.org/pep-0475/)）。



## 边缘触发和水平触发的轮询 (epoll) 对象

> https://linux.die.net/man/4/epoll
>
> *eventmask*
>
> | 常量             | 含意                                                         |
> | :--------------- | :----------------------------------------------------------- |
> | `EPOLLIN`        | 可读                                                         |
> | `EPOLLOUT`       | 可写                                                         |
> | `EPOLLPRI`       | 紧急数据读取                                                 |
> | `EPOLLERR`       | 在关联的文件描述符上有错误情况发生                           |
> | `EPOLLHUP`       | 关联的文件描述符已挂起                                       |
> | `EPOLLET`        | 设置触发方式为边缘触发，默认为水平触发                       |
> | `EPOLLONESHOT`   | 设置 one-shot 模式。触发一次事件后，该描述符会在轮询对象内部被禁用。 |
> | `EPOLLEXCLUSIVE` | 当已关联的描述符发生事件时，仅唤醒一个 epoll 对象。默认（如果未设置此标志）是唤醒所有轮询该描述符的 epoll 对象。 |
> | `EPOLLRDHUP`     | 流套接字的对侧关闭了连接或关闭了写入到一半的连接。           |
> | `EPOLLRDNORM`    | 等同于 `EPOLLIN`                                             |
> | `EPOLLRDBAND`    | 可以读取优先数据带。                                         |
> | `EPOLLWRNORM`    | 等同于 `EPOLLOUT`                                            |
> | `EPOLLWRBAND`    | 可以写入优先级数据。                                         |
> | `EPOLLMSG`       | 忽略                                                         |
>
> *Added in version 3.6:* 增加了 `EPOLLEXCLUSIVE`。仅支持 Linux Kernel 4.5 或更高版本。

## epoll.**close**()

​	关闭用于控制 epoll 对象的文件描述符。

## epoll.**closed**

​	如果 epoll 对象已关闭，则返回 `True`。

## epoll.**fileno**()

​	返回文件描述符对应的数字，该描述符用于控制 epoll 对象。

## epoll.**fromfd**(*fd*)

​	根据给定的文件描述符创建 epoll 对象。

## epoll.**register**(*fd*[, *eventmask*])

​	在 epoll 对象中注册一个文件描述符。

## epoll.**modify**(*fd*, *eventmask*)

​	修改一个已注册的文件描述符。

## epoll.**unregister**(*fd*)

​	从 epoll 对象中删除一个已注册的文件描述符。

*在 3.9 版本发生变更:* 此方法不会再忽略 [`EBADF`]({{< ref "/library/allos/errno#errno.EBADF" >}}) 错误。

## epoll.**poll**(*timeout=None*, *maxevents=-1*)

​	等待事件发生，timeout 是浮点数，单位为秒。

*在 3.5 版本发生变更:* 现在，当本函数被信号中断时，重试超时将从头开始计时，不会抛出 [`InterruptedError`]({{< ref "/library/exceptions#InterruptedError" >}}) 异常。除非信号处理程序抛出异常（相关原理请参阅 [**PEP 475**](https://peps.python.org/pep-0475/)）。



## Poll 对象

​	大多数 Unix 系统都支持 `poll()` 系统调用，它为网络服务器提供了更好的可伸缩性，可以同时为大量客户端提供服务。 `poll()` 的可伸缩性更好是因为该系统只须列出要关注的文件描述符，而 `select()` 则会构建一个位映射表，打开这个要关注的描述符所对应的比特位，然后再次线性扫描整个位映射表。 `select()` 的复杂度为 *O*(*最高文件描述符*)，而 `poll()` 则为 *O*(*文件描述符的数量*)。

## poll.**register**(*fd*[, *eventmask*])

​	在轮询对象中注册文件描述符。这样，将来调用 [`poll()`]({{< ref "/library/ipc/select#select.poll" >}}) 方法时将检查文件描述符是否有未处理的 I/O 事件。*fd* 可以是整数，也可以是带有 [`fileno()`]({{< ref "/library/allos/io#io.IOBase.fileno" >}}) 方法的对象（该方法返回一个整数）。文件对象已经实现了 `fileno()`，因此它们也可以用作参数。

*eventmask* 是可选的位掩码，用于指定要检查的事件类型，它可以是常量 `POLLIN`、`POLLPRI` 和 `POLLOUT` 的组合，如下表所述。如果未指定本参数，默认将会检查所有 3 种类型的事件。

| 常量        | 含意                                               |
| :---------- | :------------------------------------------------- |
| `POLLIN`    | 有要读取的数据                                     |
| `POLLPRI`   | 有紧急数据需要读取                                 |
| `POLLOUT`   | 准备输出：写不会阻塞                               |
| `POLLERR`   | 某种错误条件                                       |
| `POLLHUP`   | 挂起                                               |
| `POLLRDHUP` | 流套接字的对侧关闭了连接，或关闭了写入到一半的连接 |
| `POLLNVAL`  | 无效的请求：描述符未打开                           |

​	注册已注册过的文件描述符不会报错，且等同于只注册一次该描述符。

## poll.**modify**(*fd*, *eventmask*)

​	修改一个已注册的文件描述符，等同于 `register(fd, eventmask)`。尝试修改未注册的文件描述符会抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 异常，错误码为 `ENOENT`。

## poll.**unregister**(*fd*)

​	删除轮询对象正在跟踪的某个文件描述符。与 [`register()`]({{< ref "/library/ipc/select#select.poll.register" >}}) 方法类似，*fd* 可以是整数，也可以是带有 [`fileno()`]({{< ref "/library/allos/io#io.IOBase.fileno" >}}) 方法的对象（该方法返回一个整数）。

​	尝试删除从未注册过的文件描述符会抛出 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}}) 异常。

## poll.**poll**([*timeout*])

​	轮询已注册的文件描述符的集合，并返回一个列表，列表可能为空，也可能有多个 `(fd, event)` 2元组，其中包含了要报告事件或错误的描述符。 *fd* 是文件描述符，*event* 是一个位掩码，表示该描述符所报告的事件 --- `POLLIN` 表示等待输入，`POLLOUT` 表示该描述符可以写入，依此类推。 空列表表示调用超时，没有任何文件描述符报告事件。 如果指定了 *timeout*，它将指定系统等待事件时，等待多长时间后返回（以毫秒为单位）。如果 *timeout* 被省略、为 -1 或为 [`None`]({{< ref "/library/constants#None" >}})，则本调用将阻塞，直到轮询对象发生事件为止。

*在 3.5 版本发生变更:* 现在，当本函数被信号中断时，重试超时将从头开始计时，不会抛出 [`InterruptedError`]({{< ref "/library/exceptions#InterruptedError" >}}) 异常。除非信号处理程序抛出异常（相关原理请参阅 [**PEP 475**](https://peps.python.org/pep-0475/)）。



## Kqueue 对象

## kqueue.**close**()

​	关闭用于控制 kqueue 对象的文件描述符。

## kqueue.**closed**

​	如果 kqueue 对象已关闭，则返回 `True`。

## kqueue.**fileno**()

​	返回文件描述符对应的数字，该描述符用于控制 epoll 对象。

## kqueue.**fromfd**(*fd*)

​	根据给定的文件描述符创建 kqueue 对象。

## kqueue.**control**(*changelist*, *max_events*[, *timeout*]) → eventlist

​	Kevent 的低级接口

- changelist 必须是一个可迭代对象，迭代出 kevent 对象，否则置为 `None`。
- max_events 必须是 0 或一个正整数。
- timeout 单位为秒（一般为浮点数），默认为 `None`，即永不超时。

*在 3.5 版本发生变更:* 现在，当本函数被信号中断时，重试超时将从头开始计时，不会抛出 [`InterruptedError`]({{< ref "/library/exceptions#InterruptedError" >}}) 异常。除非信号处理程序抛出异常（相关原理请参阅 [**PEP 475**](https://peps.python.org/pep-0475/)）。



## Kevent 对象

https://man.freebsd.org/cgi/man.cgi?query=kqueue&sektion=2

## kevent.**ident**

​	用于区分事件的标识值。其解释取决于筛选器，但该值通常是文件描述符。在构造函数中，该标识值可以是整数或带有 [`fileno()`]({{< ref "/library/allos/io#io.IOBase.fileno" >}}) 方法的对象。kevent 在内部存储整数。

## kevent.**filter**

​	内核筛选器的名称。

| 常量               | 含意                                              |
| :----------------- | :------------------------------------------------ |
| `KQ_FILTER_READ`   | 获取描述符，并在有数据可读时返回                  |
| `KQ_FILTER_WRITE`  | 获取描述符，并在有数据可写时返回                  |
| `KQ_FILTER_AIO`    | AIO 请求                                          |
| `KQ_FILTER_VNODE`  | 当在 *fflag* 中监视的一个或多个请求事件发生时返回 |
| `KQ_FILTER_PROC`   | 监视进程ID上的事件                                |
| `KQ_FILTER_NETDEV` | 观察网络设备上的事件 [在 macOS 上不可用]          |
| `KQ_FILTER_SIGNAL` | 每当监视的信号传递到进程时返回                    |
| `KQ_FILTER_TIMER`  | 建立一个任意的计时器                              |

## kevent.**flags**

​	筛选器操作。

| 常量             | 含意                      |
| :--------------- | :------------------------ |
| `KQ_EV_ADD`      | 添加或修改事件            |
| `KQ_EV_DELETE`   | 从队列中删除事件          |
| `KQ_EV_ENABLE`   | Permitscontrol() 返回事件 |
| `KQ_EV_DISABLE`  | 禁用事件                  |
| `KQ_EV_ONESHOT`  | 在第一次发生后删除事件    |
| `KQ_EV_CLEAR`    | 检索事件后重置状态        |
| `KQ_EV_SYSFLAGS` | 内部事件                  |
| `KQ_EV_FLAG1`    | 内部事件                  |
| `KQ_EV_EOF`      | 筛选特定EOF条件           |
| `KQ_EV_ERROR`    | 请参阅返回值              |

## kevent.**fflags**

​	筛选特定标志。

`KQ_FILTER_READ` 和 `KQ_FILTER_WRITE` 筛选标志：

| 常量            | 含意                 |
| :-------------- | :------------------- |
| `KQ_NOTE_LOWAT` | 套接字缓冲区的低水线 |

`KQ_FILTER_VNODE` 筛选标志：

| 常量             | 含意                     |
| :--------------- | :----------------------- |
| `KQ_NOTE_DELETE` | 已调用 *unlink()*        |
| `KQ_NOTE_WRITE`  | 发生写入                 |
| `KQ_NOTE_EXTEND` | 文件已扩展               |
| `KQ_NOTE_ATTRIB` | 属性已更改               |
| `KQ_NOTE_LINK`   | 链接计数已更改           |
| `KQ_NOTE_RENAME` | 文件已重命名             |
| `KQ_NOTE_REVOKE` | 对文件的访问权限已被撤销 |

`KQ_FILTER_PROC` filter flags:

| 常量                | 含意                           |
| :------------------ | :----------------------------- |
| `KQ_NOTE_EXIT`      | 进程已退出                     |
| `KQ_NOTE_FORK`      | 该进程调用了 *fork()*          |
| `KQ_NOTE_EXEC`      | 进程已执行新进程               |
| `KQ_NOTE_PCTRLMASK` | 内部筛选器标志                 |
| `KQ_NOTE_PDATAMASK` | 内部筛选器标志                 |
| `KQ_NOTE_TRACK`     | 跨 *fork()* 执行进程           |
| `KQ_NOTE_CHILD`     | 在 *NOTE_TRACK* 的子进程上返回 |
| `KQ_NOTE_TRACKERR`  | 无法附加到子对象               |

`KQ_FILTER_NETDEV` 过滤器旗标 (在 macOS 上不可用):

| 常量               | 含意         |
| :----------------- | :----------- |
| `KQ_NOTE_LINKUP`   | 链接已建立   |
| `KQ_NOTE_LINKDOWN` | 链接已断开   |
| `KQ_NOTE_LINKINV`  | 链接状态无效 |

## kevent.**data**

​	筛选特定数据。

## kevent.**udata**

​	用户自定义值。
