+++
title = "signal --- 设置异步事件处理器"
date = 2024-11-15T12:30:27+08:00
weight = 50
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/signal.html](https://docs.python.org/zh-cn/3.13/library/signal.html)
>
> 收录该文档的时间：`2024-11-15T12:30:27+08:00`

# `signal` --- 设置异步事件处理器

**源代码:** [Lib/signal.py](https://github.com/python/cpython/tree/3.13/Lib/signal.py)

------

​	该模块提供了在 Python 中使用信号处理程序的机制。

## 一般规则

[`signal.signal()`]({{< ref "/library/ipc/signal#signal.signal" >}}) 函数允许定义在接收到信号时执行的自定义处理程序。少量的默认处理程序已经设置： [`SIGPIPE`]({{< ref "/library/ipc/signal#signal.SIGPIPE" >}}) 被忽略（因此管道和套接字上的写入错误可以报告为普通的 Python 异常）以及如果父进程没有更改 [`SIGINT`]({{< ref "/library/ipc/signal#signal.SIGINT" >}}) ，则其会被翻译成 [`KeyboardInterrupt`]({{< ref "/library/exceptions#KeyboardInterrupt" >}}) 异常。

​	一旦设置，特定信号的处理程序将保持安装，直到它被显式重置（ Python 模拟 BSD 样式接口而不管底层实现），但 [`SIGCHLD`]({{< ref "/library/ipc/signal#signal.SIGCHLD" >}}) 的处理程序除外，它遵循底层实现。

​	在 WebAssembly 平台上，信号是模拟实现的因而其行为有所不同。 某些函数和信号在这些平台上将不可用。

### 执行 Python 信号处理程序

​	Python 信号处理程序不会在低级（ C ）信号处理程序中执行。相反，低级信号处理程序设置一个标志，告诉 [virtual machine]({{< ref "/glossary/idx#term-virtual-machine" >}}) 稍后执行相应的 Python 信号处理程序（例如在下一个 [bytecode]({{< ref "/glossary/idx#term-bytecode" >}}) 指令）。这会导致：

- 捕获同步错误是没有意义的，例如 [`SIGFPE`]({{< ref "/library/ipc/signal#signal.SIGFPE" >}}) 或 [`SIGSEGV`]({{< ref "/library/ipc/signal#signal.SIGSEGV" >}}) ，它们是由 C 代码中的无效操作引起的。Python 将从信号处理程序返回到 C 代码，这可能会再次引发相同的信号，导致 Python 显然的挂起。 从Python 3.3开始，你可以使用 [`faulthandler`]({{< ref "/library/debug/faulthandler#module-faulthandler" >}}) 模块来报告同步错误。
- 纯 C 中实现的长时间运行的计算（例如在大量文本上的正则表达式匹配）可以在任意时间内不间断地运行，而不管接收到任何信号。计算完成后将调用 Python 信号处理程序。
- 如果处理器引发了异常，它将在主线程中“凭空”被引发。 请参阅 [下面的注释]({{< ref "/library/ipc/signal#handlers-and-exceptions" >}}) 讨论相关细节。



### 信号与线程

​	Python 信号处理程序总是会在主 Python 主解释器的主线程中执行，即使信号是在另一个线程中接收的。 这意味着信号不能被用作线程间通信的手段。 你可以改用 [`threading`]({{< ref "/library/concurrency/threading#module-threading" >}}) 模块中的同步原语。

​	此外，只有主解释器的主线程才被允许设置新的信号处理程序。

## 模块内容

*在 3.5 版本发生变更:* 下面列出的信号 (SIG*), 处理器 ([`SIG_DFL`]({{< ref "/library/ipc/signal#signal.SIG_DFL" >}}), [`SIG_IGN`]({{< ref "/library/ipc/signal#signal.SIG_IGN" >}})) 和信号掩码 ([`SIG_BLOCK`]({{< ref "/library/ipc/signal#signal.SIG_BLOCK" >}}), [`SIG_UNBLOCK`]({{< ref "/library/ipc/signal#signal.SIG_UNBLOCK" >}}), [`SIG_SETMASK`]({{< ref "/library/ipc/signal#signal.SIG_SETMASK" >}})) 相关的常量会被转成 [`enums`]({{< ref "/library/datatypes/enum#enum.IntEnum" >}}) (分别为 [`Signals`]({{< ref "/library/ipc/signal#signal.Signals" >}}), [`Handlers`]({{< ref "/library/ipc/signal#signal.Handlers" >}}) 和 [`Sigmasks`]({{< ref "/library/ipc/signal#signal.Sigmasks" >}}))。 [`getsignal()`]({{< ref "/library/ipc/signal#signal.getsignal" >}}), [`pthread_sigmask()`]({{< ref "/library/ipc/signal#signal.pthread_sigmask" >}}), [`sigpending()`]({{< ref "/library/ipc/signal#signal.sigpending" >}}) 和 [`sigwait()`]({{< ref "/library/ipc/signal#signal.sigwait" >}}) 函数将以 [`Signals`]({{< ref "/library/ipc/signal#signal.Signals" >}}) 对象形式返回人类可读的 [`enums`]({{< ref "/library/datatypes/enum#enum.IntEnum" >}})。

​	signal 模块定义了三个枚举:

## *class* signal.**Signals**

[`enum.IntEnum`]({{< ref "/library/datatypes/enum#enum.IntEnum" >}}) 是 SIG* 常量和 CTRL_* 常量的多项集。

> Added in version 3.5.
>

## *class* signal.**Handlers**

[`enum.IntEnum`]({{< ref "/library/datatypes/enum#enum.IntEnum" >}}) 是常量 [`SIG_DFL`]({{< ref "/library/ipc/signal#signal.SIG_DFL" >}}) 和 [`SIG_IGN`]({{< ref "/library/ipc/signal#signal.SIG_IGN" >}}) 的多项集。

> Added in version 3.5.
>

## *class* signal.**Sigmasks**

[`enum.IntEnum`]({{< ref "/library/datatypes/enum#enum.IntEnum" >}}) 是常量 [`SIG_BLOCK`]({{< ref "/library/ipc/signal#signal.SIG_BLOCK" >}}), [`SIG_UNBLOCK`]({{< ref "/library/ipc/signal#signal.SIG_UNBLOCK" >}}) 和 [`SIG_SETMASK`]({{< ref "/library/ipc/signal#signal.SIG_SETMASK" >}}) 的多项集。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

​	请参阅手册页面 *[sigprocmask(2)](https://manpages.debian.org/sigprocmask(2))* 和 *[pthread_sigmask(3)](https://manpages.debian.org/pthread_sigmask(3))* 了解更多信息。

> Added in version 3.5.
>

​	在 [`signal`]({{< ref "/library/ipc/signal#module-signal" >}}) 模块中定义的变量是：

## signal.**SIG_DFL**

​	这是两种标准信号处理选项之一；它只会执行信号的默认函数。 例如，在大多数系统上，对于 `SIGQUIT` 的默认操作是转储核心并退出，而对于 [`SIGCHLD`]({{< ref "/library/ipc/signal#signal.SIGCHLD" >}}) 的默认操作是简单地忽略它。

## signal.**SIG_IGN**

​	这是另一个标准信号处理程序，它将简单地忽略给定的信号。

## signal.**SIGABRT**

​	来自 *[abort(3)](https://manpages.debian.org/abort(3))* 的中止信号。

## signal.**SIGALRM**

​	来自 *[alarm(2)](https://manpages.debian.org/alarm(2))* 的计时器信号。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

## signal.**SIGBREAK**

​	来自键盘的中断 (CTRL + BREAK)。

[Availability]({{< ref "/library/intro#availability" >}}): Windows.

## signal.**SIGBUS**

​	总线错误 (非法的内存访问)。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

## signal.**SIGCHLD**

​	子进程被停止或终结。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

## signal.**SIGCLD**

[`SIGCHLD`]({{< ref "/library/ipc/signal#signal.SIGCHLD" >}}) 的别名。

[Availability]({{< ref "/library/intro#availability" >}}): not macOS.

## signal.**SIGCONT**

​	如果进程当前已停止则继续执行它

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

## signal.**SIGFPE**

​	浮点异常。 例如除以零。

​	参见

 

​	当除法或求余运算的第二个参数为零时会引发 [`ZeroDivisionError`]({{< ref "/library/exceptions#ZeroDivisionError" >}}) 。

## signal.**SIGHUP**

​	在控制终端上检测到挂起或控制进程的终止。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

## signal.**SIGILL**

​	非法指令。

## signal.**SIGINT**

​	来自键盘的中断 (CTRL + C)。

​	默认的动作是引发 [`KeyboardInterrupt`]({{< ref "/library/exceptions#KeyboardInterrupt" >}})。

## signal.**SIGKILL**

​	终止信号。

​	它不能被捕获、阻塞或忽略。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

## signal.**SIGPIPE**

​	损坏的管道：写入到没有读取器的管道。

​	默认的动作是忽略此信号。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

## signal.**SIGSEGV**

​	段错误：无效的内存引用。

## signal.**SIGSTKFLT**

> ​	协处理器上的栈错误。 Linux 内核不会引发此信号：它只能在用户空间中被引发。

[Availability]({{< ref "/library/intro#availability" >}}): Linux.

​	在信号可用的架构上。 参见手册页面 *[signal(7)](https://manpages.debian.org/signal(7))* 了解更多信息。

> Added in version 3.11.
>

## signal.**SIGTERM**

​	终结信号。

## signal.**SIGUSR1**

​	用户自定义信号 1。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

## signal.**SIGUSR2**

​	用户自定义信号 2。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

## signal.**SIGWINCH**

​	窗口调整大小信号。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

## **SIG\***

​	所有信号编号都是符号化定义的。 例如，挂起信号被定义为 [`signal.SIGHUP`]({{< ref "/library/ipc/signal#signal.SIGHUP" >}})；变量的名称与 C 程序中使用的名称相同，具体见 `<signal.h>`。 '`signal()`' 的 Unix 手册页面列出了现有的信号 (在某些系统上这是 *[signal(2)](https://manpages.debian.org/signal(2))*，在其他系统中此列表则是在 *[signal(7)](https://manpages.debian.org/signal(7))* 中)。 请注意并非所有系统都会定义相同的信号名称集；只有系统所定义的名称才会由此模块来定义。

## signal.**CTRL_C_EVENT**

​	对应于 Ctrl+C 击键事件的信号。此信号只能用于 [`os.kill()`]({{< ref "/library/allos/os#os.kill" >}}) 。

[Availability]({{< ref "/library/intro#availability" >}}): Windows.

> Added in version 3.2.
>

## signal.**CTRL_BREAK_EVENT**

​	对应于 Ctrl+Break 击键事件的信号。此信号只能用于 [`os.kill()`]({{< ref "/library/allos/os#os.kill" >}}) 。

[Availability]({{< ref "/library/intro#availability" >}}): Windows.

> Added in version 3.2.
>

## signal.**NSIG**

​	比最高的信号编号值多一。 请使用 [`valid_signals()`]({{< ref "/library/ipc/signal#signal.valid_signals" >}}) 来获取有效的信号编号。

## signal.**ITIMER_REAL**

​	实时递减间隔计时器，并在到期时发送 [`SIGALRM`]({{< ref "/library/ipc/signal#signal.SIGALRM" >}}) 。

## signal.**ITIMER_VIRTUAL**

​	仅在进程执行时递减间隔计时器，并在到期时发送 SIGVTALRM 。

## signal.**ITIMER_PROF**

​	当进程执行时以及当系统替进程执行时都会减小间隔计时器。 这个计时器与 ITIMER_VIRTUAL 相配结，通常被用于分析应用程序在用户和内核空间中花费的时间。 SIGPROF 会在超期时被发送。

## signal.**SIG_BLOCK**

[`pthread_sigmask()`]({{< ref "/library/ipc/signal#signal.pthread_sigmask" >}}) 的 *how* 形参的一个可能的值，表明信号将会被阻塞。

> Added in version 3.3.
>

## signal.**SIG_UNBLOCK**

[`pthread_sigmask()`]({{< ref "/library/ipc/signal#signal.pthread_sigmask" >}}) 的 *how* 形参的是个可能的值，表明信号将被解除阻塞。

> Added in version 3.3.
>

## signal.**SIG_SETMASK**

[`pthread_sigmask()`]({{< ref "/library/ipc/signal#signal.pthread_sigmask" >}}) 的 *how* 形参的一个可能的值，表明信号掩码将要被替换。

> Added in version 3.3.
>

[`signal`]({{< ref "/library/ipc/signal#module-signal" >}}) 模块定义了一个异常:

## *exception* signal.**ItimerError**

​	作为来自下层 [`setitimer()`]({{< ref "/library/ipc/signal#signal.setitimer" >}}) 或 [`getitimer()`]({{< ref "/library/ipc/signal#signal.getitimer" >}}) 实现错误的信号被引发。 如果将无效的定时器或负的时间值传给 [`setitimer()`]({{< ref "/library/ipc/signal#signal.setitimer" >}}) 就导致这个错误。 此错误是 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 的子类型。

> Added in version 3.3:
> 此错误是 [`IOError`]({{< ref "/library/exceptions#IOError" >}}) 的子类型，现在则是 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 的别名。

[`signal`]({{< ref "/library/ipc/signal#module-signal" >}}) 模块定义了以下函数:

## signal.**alarm**(*time*)

​	如果 *time* 值非零，则此函数将要求将一个 [`SIGALRM`]({{< ref "/library/ipc/signal#signal.SIGALRM" >}}) 信号在 *time* 秒内发往进程。 任何在之前排入计划的警报都会被取消（在任何时刻都只能有一个警报被排入计划）。 后续的返回值将是任何之前设置的警报被传入之前的秒数。 如果 *time* 值为零，则不会将任何警报排入计划，并且任何已排入计划的警报都会被取消。 如果返回值为零，则目前没有任何警报被排入计划。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

​	请参阅手册页面 *[alarm(2)](https://manpages.debian.org/alarm(2))* 了解更多信息。

## signal.**getsignal**(*signalnum*)

​	返回当前用于信号 *signalnum* 的信号处理程序。 返回值可以是一个 Python 可调用对象，或是特殊值 [`signal.SIG_IGN`]({{< ref "/library/ipc/signal#signal.SIG_IGN" >}}), [`signal.SIG_DFL`]({{< ref "/library/ipc/signal#signal.SIG_DFL" >}}) 或 [`None`]({{< ref "/library/constants#None" >}}) 之一。 在这里，[`signal.SIG_IGN`]({{< ref "/library/ipc/signal#signal.SIG_IGN" >}}) 表示信号在之前被忽略，[`signal.SIG_DFL`]({{< ref "/library/ipc/signal#signal.SIG_DFL" >}}) 表示之前在使用默认的信号处理方式，而 `None` 表示之前的信号处理程序未由 Python 安装。

## signal.**strsignal**(*signalnum*)

​	返回信号 *signalnum* 的描述信息，例如 "Interrupt" 对应 [`SIGINT`]({{< ref "/library/ipc/signal#signal.SIGINT" >}})。 如果 *signalnum* 没有描述信息则返回 [`None`]({{< ref "/library/constants#None" >}})。 如果 *signalnum* 无效则引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

> Added in version 3.8.
>

## signal.**valid_signals**()

​	返回本平台上的有效信号编号集。 这可能会少于 `range(1, NSIG)`，如果某些信号被系统保留作为内部使用的话。

> Added in version 3.8.
>

## signal.**pause**()

​	使进程休眠直至接收到一个信号；然后将会调用适当的处理程序。 返回空值。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

​	请参阅手册页面 *[signal(2)](https://manpages.debian.org/signal(2))* 了解更多信息。

​	另请参阅 [`sigwait()`]({{< ref "/library/ipc/signal#signal.sigwait" >}}), [`sigwaitinfo()`]({{< ref "/library/ipc/signal#signal.sigwaitinfo" >}}), [`sigtimedwait()`]({{< ref "/library/ipc/signal#signal.sigtimedwait" >}}) 和 [`sigpending()`]({{< ref "/library/ipc/signal#signal.sigpending" >}})。

## signal.**raise_signal**(*signum*)

​	向调用方进程发送一个信号。 返回空值。

> Added in version 3.8.
>

## signal.**pidfd_send_signal**(*pidfd*, *sig*, *siginfo=None*, *flags=0*)

​	发送信号 *sig* 到文件描述符 *pidfd* 所指向的进程。 Python 目前不支持 *siginfo* 形参；它必须为 `None`。 提供 *flags* 参数是为了将来扩展；当前未定义旗标值。

​	更多信息请参阅 *[pidfd_send_signal(2)](https://manpages.debian.org/pidfd_send_signal(2))* 手册页面。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 5.1, Android >= [`build-time`]({{< ref "/library/python/sys#sys.getandroidapilevel" >}}) API level 31

> Added in version 3.9.
>

## signal.**pthread_kill**(*thread_id*, *signalnum*)

​	将信号 *signalnum* 发送至与调用者在同一进程中另一线程 *thread_id*。 目标线程可被用于执行任何代码（Python或其它）。 但是，如果目标线程是在执行 Python 解释器，则 Python 信号处理程序将 [由主解释器的主线程来执行]({{< ref "/library/ipc/signal#signals-and-threads" >}})。 因此，将信号发送给特定 Python 线程的唯一作用在于强制让一个正在运行的系统调用失败并抛出 [`InterruptedError`]({{< ref "/library/exceptions#InterruptedError" >}})。

​	使用 [`threading.Thread`]({{< ref "/library/concurrency/threading#threading.Thread" >}}) 对象的 [`threading.get_ident()`]({{< ref "/library/concurrency/threading#threading.get_ident" >}}) 或 [`ident`]({{< ref "/library/concurrency/threading#threading.Thread.ident" >}}) 属性为 *thread_id* 获取合适的值。

​	如果 *signalnum* 为 0，则不会发送信号，但仍然会执行错误检测；这可被用来检测目标线程是否仍在运行。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `signal.pthread_kill` 并附带参数 `thread_id`, `signalnum`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

​	请参阅手册页面 *[pthread_kill(3)](https://manpages.debian.org/pthread_kill(3))* 了解更多信息。

​	另请参阅 [`os.kill()`]({{< ref "/library/allos/os#os.kill" >}})。

> Added in version 3.3.
>

## signal.**pthread_sigmask**(*how*, *mask*)

​	获取和/或修改调用方线程的信号掩码。 信号掩码是一组传送过程目前为调用者而阻塞的信号集。 返回旧的信号掩码作为一组信号。

​	该调用的行为取决于 *how* 的值，具体见下。

- [`SIG_BLOCK`]({{< ref "/library/ipc/signal#signal.SIG_BLOCK" >}}): 被阻塞信号集是当前集与 *mask* 参数的并集。
- [`SIG_UNBLOCK`]({{< ref "/library/ipc/signal#signal.SIG_UNBLOCK" >}}): *mask* 中的信号会从当前已阻塞信号集中被移除。 允许尝试取消对一个非阻塞信号的阻塞。
- [`SIG_SETMASK`]({{< ref "/library/ipc/signal#signal.SIG_SETMASK" >}}): 已阻塞信号集会被设为 *mask* 参数的值。

*mask* 是一个信号编号集合 (例如 {[`signal.SIGINT`]({{< ref "/library/ipc/signal#signal.SIGINT" >}}), [`signal.SIGTERM`]({{< ref "/library/ipc/signal#signal.SIGTERM" >}})})。 请使用 [`valid_signals()`]({{< ref "/library/ipc/signal#signal.valid_signals" >}}) 表示包含所有信号的完全掩码。

​	例如，`signal.pthread_sigmask(signal.SIG_BLOCK, [])` 会读取调用方线程的信号掩码。

[`SIGKILL`]({{< ref "/library/ipc/signal#signal.SIGKILL" >}}) 和 `SIGSTOP` 不能被阻塞。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

​	请参阅手册页面 *[sigprocmask(2)](https://manpages.debian.org/sigprocmask(2))* 和 *[pthread_sigmask(3)](https://manpages.debian.org/pthread_sigmask(3))* 了解更多信息。

​	另请参阅 [`pause()`]({{< ref "/library/ipc/signal#signal.pause" >}}), [`sigpending()`]({{< ref "/library/ipc/signal#signal.sigpending" >}}) 和 [`sigwait()`]({{< ref "/library/ipc/signal#signal.sigwait" >}})。

> Added in version 3.3.
>

## signal.**setitimer**(*which*, *seconds*, *interval=0.0*)

​	设置由 *which* 指明的给定间隔计时器 ([`signal.ITIMER_REAL`]({{< ref "/library/ipc/signal#signal.ITIMER_REAL" >}}), [`signal.ITIMER_VIRTUAL`]({{< ref "/library/ipc/signal#signal.ITIMER_VIRTUAL" >}}) 或 [`signal.ITIMER_PROF`]({{< ref "/library/ipc/signal#signal.ITIMER_PROF" >}}) 之一) 在 *seconds* 秒 (接受浮点数值，为与 [`alarm()`]({{< ref "/library/ipc/signal#signal.alarm" >}}) 之差) 之后开始并在每 *interval* 秒间隔时 (如果 *interval* 不为零) 启动。 由 *which* 指明的间隔计时器可通过将 *seconds* 设为零来清空。

​	当一个间隔计时器启动时，会有信号发送至进程。 所发送的具体信号取决于所使用的计时器；[`signal.ITIMER_REAL`]({{< ref "/library/ipc/signal#signal.ITIMER_REAL" >}}) 将发送 [`SIGALRM`]({{< ref "/library/ipc/signal#signal.SIGALRM" >}}), [`signal.ITIMER_VIRTUAL`]({{< ref "/library/ipc/signal#signal.ITIMER_VIRTUAL" >}}) 将发送 `SIGVTALRM`, 而 [`signal.ITIMER_PROF`]({{< ref "/library/ipc/signal#signal.ITIMER_PROF" >}}) 将发送 `SIGPROF`.

​	原有的值会以元组: (delay, interval) 的形式被返回。

​	尝试传入无效的计时器将导致 [`ItimerError`]({{< ref "/library/ipc/signal#signal.ItimerError" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

## signal.**getitimer**(*which*)

​	返回由 *which* 指明的给定间隔计时器当前的值。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

## signal.**set_wakeup_fd**(*fd*, ***, *warn_on_full_buffer=True*)

​	将唤醒文件描述符设为 *fd*。 当接收到信号时，会将信号编号以单个字节的形式写入 fd。 这可被其它库用来唤醒一次 poll 或 select 调用，以允许该信号被完全地处理。

​	原有的唤醒 fd 会被返回（或者如果未启用文件描述符唤醒则返回 -1）。 如果 *fd* 为 -1，文件描述符唤醒会被禁用。 如果不为 -1，则 *fd* 必须为非阻塞型。 需要由库来负责在重新调用 poll 或 select 之前从 *fd* 移除任何字节数据。

​	当启用线程用时，此函数只能从 [主解释器的主线程]({{< ref "/library/ipc/signal#signals-and-threads" >}}) 被调用；尝试从另一线程调用它将导致 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 异常被引发。

​	使用此函数有两种通常的方式。 在两种方式下，当有信号到达时你都是用 fd 来唤醒，但之后它们在确定达到的一个或多个信号 *which* 时存在差异。

​	在第一种方式下，我们从 fd 的缓冲区读取数据，这些字节值会给你信号编号。 这种方式很简单，但在少数情况下会发生问题：通常 fd 将有缓冲区空间大小限制，如果信号到达得太多且太快，缓冲区可能会爆满，有些信号可能丢失。 如果你使用此方式，则你应当设置 `warn_on_full_buffer=True`，当信号丢失时这至少能将警告消息打印到 stderr。

​	在第二种方式下，我们 *只会* 将唤醒 fd 用于唤醒，而忽略实际的字节值。 在此情况下，我们所关心的只有 fd 的缓冲区为空还是不为空；爆满的缓冲区完全不会导致问题。 如果你使用此方式，则你应当设置 `warn_on_full_buffer=False`，这样你的用户就不会被虚假的警告消息所迷惑。

*在 3.5 版本发生变更:* 在 Windows 上，此函数现在也支持套接字处理。

*在 3.7 版本发生变更:* 添加了 `warn_on_full_buffer` 形参。

## signal.**siginterrupt**(*signalnum*, *flag*)

​	更改系统调用重启行为：如果 *flag* 为 [`False`]({{< ref "/library/constants#False" >}})，系统调用将在被信号 *signalnum* 中断时重启，否则系统调用将被中断。 返回空值。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

​	请参阅手册页面 *[siginterrupt(3)](https://manpages.debian.org/siginterrupt(3))* 了解更多信息。

​	请注意使用 [`signal()`]({{< ref "/library/ipc/signal#module-signal" >}}) 安装信号处理器将会通过隐式地调用 `siginterrupt()` 并为给定信号的 *flag* 设置真值来将重启行为重置为可中断的。

## signal.**signal**(*signalnum*, *handler*)

​	将信号 *signalnum* 的处理程序设为函数 *handler*。 *handler* 可以为接受两个参数（见下）的 Python 可调用对象，或者为特殊值 [`signal.SIG_IGN`]({{< ref "/library/ipc/signal#signal.SIG_IGN" >}}) 或 [`signal.SIG_DFL`]({{< ref "/library/ipc/signal#signal.SIG_DFL" >}}) 之一。 之前的信号处理程序将被返回（参见上文 [`getsignal()`]({{< ref "/library/ipc/signal#signal.getsignal" >}}) 的描述）。 （更多信息请参阅 Unix 手册页面 *[signal(2)](https://manpages.debian.org/signal(2))*。）

​	当启用线程用时，此函数只能从 [主解释器的主线程]({{< ref "/library/ipc/signal#signals-and-threads" >}}) 被调用；尝试从另一线程调用它将导致 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 异常被引发。

*handler* 将附带两个参数调用：信号编号和当前堆栈帧 (`None` 或一个帧对象；有关帧对象的描述请参阅 [类型层级结构描述]({{< ref "/reference/datamodel#frame-objects" >}}) 或者参阅 [`inspect`]({{< ref "/library/python/inspect#module-inspect" >}}) 模块中的属性描述)。

​	在 Windows 上，[`signal()`]({{< ref "/library/ipc/signal#module-signal" >}}) 调用只能附带 [`SIGABRT`]({{< ref "/library/ipc/signal#signal.SIGABRT" >}}), [`SIGFPE`]({{< ref "/library/ipc/signal#signal.SIGFPE" >}}), [`SIGILL`]({{< ref "/library/ipc/signal#signal.SIGILL" >}}), [`SIGINT`]({{< ref "/library/ipc/signal#signal.SIGINT" >}}), [`SIGSEGV`]({{< ref "/library/ipc/signal#signal.SIGSEGV" >}}), [`SIGTERM`]({{< ref "/library/ipc/signal#signal.SIGTERM" >}}) 或 [`SIGBREAK`]({{< ref "/library/ipc/signal#signal.SIGBREAK" >}})。 任何其他值都将引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。 请注意不是所有系统都定义了同样的信号名称集合；如果一个信号名称未被定义为 `SIG*` 模块层级常量则将引发 [`AttributeError`]({{< ref "/library/exceptions#AttributeError" >}})。

## signal.**sigpending**()

​	检查正在等待传送给调用方线程的信号集合（即在阻塞期间被引发的信号）。 返回正在等待的信号集合。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

​	请参阅手册页面 *[sigpending(2)](https://manpages.debian.org/sigpending(2))* 了解更多信息。

​	另请参阅 [`pause()`]({{< ref "/library/ipc/signal#signal.pause" >}}), [`pthread_sigmask()`]({{< ref "/library/ipc/signal#signal.pthread_sigmask" >}}) 和 [`sigwait()`]({{< ref "/library/ipc/signal#signal.sigwait" >}})。

> Added in version 3.3.
>

## signal.**sigwait**(*sigset*)

​	挂起调用方线程的执行直到信号集合 *sigset* 中指定的信号之一被传送。 此函数会接受该信号（将其从等待信号列表中移除），并返回信号编号。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

​	请参阅手册页面 *[sigwait(3)](https://manpages.debian.org/sigwait(3))* 了解更多信息。

​	另请参阅 [`pause()`]({{< ref "/library/ipc/signal#signal.pause" >}}), [`pthread_sigmask()`]({{< ref "/library/ipc/signal#signal.pthread_sigmask" >}}), [`sigpending()`]({{< ref "/library/ipc/signal#signal.sigpending" >}}), [`sigwaitinfo()`]({{< ref "/library/ipc/signal#signal.sigwaitinfo" >}}) 和 [`sigtimedwait()`]({{< ref "/library/ipc/signal#signal.sigtimedwait" >}})。

> Added in version 3.3.
>

## signal.**sigwaitinfo**(*sigset*)

​	挂起调用方线程的执行直到信号集合 *sigset* 中指定的信号之一被传送。 此函数会接受该信号并将其从等待信号列表中移除。 如果 *sigset* 中的信号之一已经在等待调用方线程，此函数将立即返回并附带有关该信号的信息。 被传送信号的信号处理程序不会被调用。 如果该函数被某个不在 *sigset* 中的信号中断则会引发 [`InterruptedError`]({{< ref "/library/exceptions#InterruptedError" >}})。

​	返回值是一个代表 `siginfo_t` 结构体所包含数据的对象，具体为: `si_signo`, `si_code`, `si_errno`, `si_pid`, `si_uid`, `si_status`, `si_band`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

​	请参阅手册页面 *[sigwaitinfo(2)](https://manpages.debian.org/sigwaitinfo(2))* 了解更多信息。

​	另请参阅 [`pause()`]({{< ref "/library/ipc/signal#signal.pause" >}}), [`sigwait()`]({{< ref "/library/ipc/signal#signal.sigwait" >}}) 和 [`sigtimedwait()`]({{< ref "/library/ipc/signal#signal.sigtimedwait" >}})。

> Added in version 3.3.
>

*在 3.5 版本发生变更:* 当被某个 不在 *sigset* 中的信号中断时本函数将进行重试并且信号处理程序不会引发异常（请参阅 [**PEP 475**](https://peps.python.org/pep-0475/) 了解其理由）。

## signal.**sigtimedwait**(*sigset*, *timeout*)

​	与 [`sigwaitinfo()`]({{< ref "/library/ipc/signal#signal.sigwaitinfo" >}}) 类似，但会接受一个额外的 *timeout* 参数来指定超时限制。 如果将 *timeout* 指定为 `0`，则会执行轮询。 如果发生超时则返回 [`None`]({{< ref "/library/constants#None" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

​	请参阅手册页面 *[sigtimedwait(2)](https://manpages.debian.org/sigtimedwait(2))* 了解更多信息。

​	另请参阅 [`pause()`]({{< ref "/library/ipc/signal#signal.pause" >}}), [`sigwait()`]({{< ref "/library/ipc/signal#signal.sigwait" >}}) 和 [`sigwaitinfo()`]({{< ref "/library/ipc/signal#signal.sigwaitinfo" >}})。

> Added in version 3.3.
>

*在 3.5 版本发生变更:* 现在当此函数被某个不在 *sigset* 中的信号中断时将以计算出的 *timeout* 进行重试并且信号处理程序不会引发异常（请参阅 [**PEP 475**](https://peps.python.org/pep-0475/) 了解其理由）。



## 例子

​	这是一个最小示例程序。 它使用 [`alarm()`]({{< ref "/library/ipc/signal#signal.alarm" >}}) 函数来限制等待打开一个文件所花费的时间；这在文件为无法开启的串行设备时会很有用处，此情况通常会导致 [`os.open()`]({{< ref "/library/allos/os#os.open" >}}) 无限期地挂起。 解决办法是在打开文件之前设置 5 秒钟的 alarm；如果操作耗时过长，将会发送 alarm 信号，并且处理程序会引发一个异常。

```
import signal, os

def handler(signum, frame):
    signame = signal.Signals(signum).name
    print(f'Signal handler called with signal {signame} ({signum})')
    raise OSError("Couldn't open device!")

# 设置信号处理器及 5 秒警报
signal.signal(signal.SIGALRM, handler)
signal.alarm(5)

# open() 可能会无限挂起
fd = os.open('/dev/ttyS0', os.O_RDWR)

signal.alarm(0)          # 禁用警报
```

## 对于 SIGPIPE 的说明

​	将你的程序用管道输出到工具例如 *[head(1)](https://manpages.debian.org/head(1))* 将会导致 [`SIGPIPE`]({{< ref "/library/ipc/signal#signal.SIGPIPE" >}}) 信号在其标准输出的接收方提前关闭时被发送到你的进程。 这将引发一个异常例如 `BrokenPipeError: [Errno 32] Broken pipe`。 要处理这种情况，请对你的入口点进行包装以捕获此异常，如下所示:

```
import os
import sys

def main():
    try:
        # 模拟大量输出（你的代码将替换此循环）
        for x in range(10000):
            print("y")
        # 在此刷新输出以在此 try 代码块内部时
        # 强制让 SIGPIPE 被触发。
        sys.stdout.flush()
    except BrokenPipeError:
        # Python 在退出时刷新标准流；将剩余的输出
        # 重定向到 devnull 以避免关闭时引发新的 BrokenPipeError
        devnull = os.open(os.devnull, os.O_WRONLY)
        os.dup2(devnull, sys.stdout.fileno())
        sys.exit(1)  # Python 退出时在 EPIPE 上输出错误码 1

if __name__ == '__main__':
    main()
```

​	请不要将 [`SIGPIPE`]({{< ref "/library/ipc/signal#signal.SIGPIPE" >}}) 的处置方式设为 [`SIG_DFL`]({{< ref "/library/ipc/signal#signal.SIG_DFL" >}}) 以避免 [`BrokenPipeError`]({{< ref "/library/exceptions#BrokenPipeError" >}})。 这样做还会在你的程序所写入的任何套接字连接中断时导致你的程序异常退出。



## 有关信号处理器和异常的注释

​	如果一个信号处理器引发了异常，该异常将被传播到主线程并可能在任何 [bytecode]({{< ref "/glossary/idx#term-bytecode" >}}) 指令之后被引发，在执行期间的任何时候都可能出现 [`KeyboardInterrupt`]({{< ref "/library/exceptions#KeyboardInterrupt" >}})。 大多数 Python 代码，包括标准库的代码都不能对此进行健壮性处理，因此 [`KeyboardInterrupt`]({{< ref "/library/exceptions#KeyboardInterrupt" >}}) (或由信号处理器所导致的任何其他异常) 可能会在极少数情况下使程序处于非预期的状态。

​	为了展示这个问题，请考虑以下代码:

```
class SpamContext:
    def __init__(self):
        self.lock = threading.Lock()

    def __enter__(self):
        # 如果 KeyboardInterrupt 在这里发生，将保持一切正常
        self.lock.acquire()
        # 如果 KeyboardInterrupt 在这里发生，__exit__ 将不会被调用
        ...
        # KeyboardInterrupt 可能会在该函数返回之前发生

    def __exit__(self, exc_type, exc_val, exc_tb):
        ...
        self.lock.release()
```

​	对于许多程序，特别是那些在遇到 [`KeyboardInterrupt`]({{< ref "/library/exceptions#KeyboardInterrupt" >}}) 只需直接退出的程序来说，这不是个问题，但是高复杂度或要求高可靠性的应用程序则应当避免由于信号处理器引发异常。 他们还应当避免将捕获 [`KeyboardInterrupt`]({{< ref "/library/exceptions#KeyboardInterrupt" >}}) 作为程序关闭的优雅方式。 相反地，他们应当安装自己的 [`SIGINT`]({{< ref "/library/ipc/signal#signal.SIGINT" >}}) 处理器。 下面是一个避免了 [`KeyboardInterrupt`]({{< ref "/library/exceptions#KeyboardInterrupt" >}}) 的 HTTP 服务器示例:

```
import signal
import socket
from selectors import DefaultSelector, EVENT_READ
from http.server import HTTPServer, SimpleHTTPRequestHandler

interrupt_read, interrupt_write = socket.socketpair()

def handler(signum, frame):
    print('Signal handler called with signal', signum)
    interrupt_write.send(b'\0')
signal.signal(signal.SIGINT, handler)

def serve_forever(httpd):
    sel = DefaultSelector()
    sel.register(interrupt_read, EVENT_READ)
    sel.register(httpd, EVENT_READ)

    while True:
        for key, _ in sel.select():
            if key.fileobj == interrupt_read:
                interrupt_read.recv(1)
                return
            if key.fileobj == httpd:
                httpd.handle_request()

print("Serving on port 8000")
httpd = HTTPServer(('', 8000), SimpleHTTPRequestHandler)
serve_forever(httpd)
print("Shutdown...")
```
