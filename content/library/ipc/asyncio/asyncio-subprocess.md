+++
title = "子进程集"
date = 2024-11-15T12:30:27+08:00
weight = 50
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html)
>
> 收录该文档的时间：`2024-11-15T12:30:27+08:00`

# 子进程集

**源代码:** [Lib/asyncio/subprocess.py](https://github.com/python/cpython/tree/3.13/Lib/asyncio/subprocess.py), [Lib/asyncio/base_subprocess.py](https://github.com/python/cpython/tree/3.13/Lib/asyncio/base_subprocess.py)

------

​	本节介绍了用于创建和管理子进程的高层级 async/await asyncio API。

​	下面的例子演示了如何用 asyncio 运行一个 shell 命令并获取其结果:

```
import asyncio

async def run(cmd):
    proc = await asyncio.create_subprocess_shell(
        cmd,
        stdout=asyncio.subprocess.PIPE,
        stderr=asyncio.subprocess.PIPE)

    stdout, stderr = await proc.communicate()

    print(f'[{cmd!r} exited with {proc.returncode}]')
    if stdout:
        print(f'[stdout]\n{stdout.decode()}')
    if stderr:
        print(f'[stderr]\n{stderr.decode()}')

asyncio.run(run('ls /zzz'))
```

​	将打印:

```
['ls /zzz' exited with 1]
[stderr]
ls: /zzz: No such file or directory
```

​	由于所有 asyncio 子进程函数都是异步的并且 asyncio 提供了许多工具用来配合这些函数使用，因此并行地执行和监视多个子进程十分容易。 要修改上面的例子来同时运行多个命令确实是非常简单的:

```
async def main():
    await asyncio.gather(
        run('ls /zzz'),
        run('sleep 1; echo "hello"'))

asyncio.run(main())
```

​	另请参阅 [Examples](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html#examples) 小节。

## 创建子进程

## *coroutine* asyncio.**create_subprocess_exec**(*program*, **args*, *stdin=None*, *stdout=None*, *stderr=None*, *limit=None*, ***kwds*)

​	创建一个子进程。

*limit* 参数为 `Process.stdout` 和 `Process.stderr` 设置 [`StreamReader`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.StreamReader) 包装器的缓冲区上限（如果将 [`subprocess.PIPE`](https://docs.python.org/zh-cn/3.13/library/subprocess.html#subprocess.PIPE) 传给 *stdout* 和 *stderr* 参数）。

​	返回一个 [`Process`](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html#asyncio.subprocess.Process) 实例。

​	有关其他形参的说明请查阅 [`loop.subprocess_exec()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.loop.subprocess_exec) 的文档。

*在 3.10 版本发生变更:* 移除了 *loop* 形参。

## *coroutine* asyncio.**create_subprocess_shell**(*cmd*, *stdin=None*, *stdout=None*, *stderr=None*, *limit=None*, ***kwds*)

​	运行 *cmd* shell 命令。

*limit* 参数为 `Process.stdout` 和 `Process.stderr` 设置 [`StreamReader`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.StreamReader) 包装器的缓冲区上限（如果将 [`subprocess.PIPE`](https://docs.python.org/zh-cn/3.13/library/subprocess.html#subprocess.PIPE) 传给 *stdout* 和 *stderr* 参数）。

​	返回一个 [`Process`](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html#asyncio.subprocess.Process) 实例。

​	有关其他形参的说明请查阅 [`loop.subprocess_shell()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.loop.subprocess_shell) 的文档。

​	重要

 

​	应用程序要负责确保正确地转义所有空白字符和特殊字符以防止 [shell 注入](https://en.wikipedia.org/wiki/Shell_injection#Shell_injection) 漏洞。 [`shlex.quote()`](https://docs.python.org/zh-cn/3.13/library/shlex.html#shlex.quote) 函数可以被用来正确地转义字符串中可以被用来构造 shell 命令的空白字符和特殊 shell 字符。

*在 3.10 版本发生变更:* 移除了 *loop* 形参。

​	备注

 

​	如果使用了 [`ProactorEventLoop`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.ProactorEventLoop) 则子进程将在 Windows 中可用。 详情参见 [Windows 上的子进程支持](https://docs.python.org/zh-cn/3.13/library/asyncio-platforms.html#asyncio-windows-subprocess)。

​	参见

 

​	asyncio 还有下列 *低层级* API 可配合子进程使用: [`loop.subprocess_exec()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.loop.subprocess_exec), [`loop.subprocess_shell()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.loop.subprocess_shell), [`loop.connect_read_pipe()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.loop.connect_read_pipe), [`loop.connect_write_pipe()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.loop.connect_write_pipe) 以及 [子进程传输](https://docs.python.org/zh-cn/3.13/library/asyncio-protocol.html#asyncio-subprocess-transports) 和 [子进程协议](https://docs.python.org/zh-cn/3.13/library/asyncio-protocol.html#asyncio-subprocess-protocols)。

## 常量

## asyncio.subprocess.**PIPE**

​	可以被传递给 *stdin*, *stdout* 或 *stderr* 形参。

​	如果 *PIPE* 被传递给 *stdin* 参数，则 [`Process.stdin`](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html#asyncio.subprocess.Process.stdin) 属性将会指向一个 `StreamWriter` 实例。

​	如果 *PIPE* 被传递给 *stdout* 或 *stderr* 参数，则 [`Process.stdout`](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html#asyncio.subprocess.Process.stdout) 和 [`Process.stderr`](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html#asyncio.subprocess.Process.stderr) 属性将会指向 `StreamReader` 实例。

## asyncio.subprocess.**STDOUT**

​	可以用作 *stderr* 参数的特殊值，表示标准错误应当被重定向到标准输出。

## asyncio.subprocess.**DEVNULL**

​	可以用作 *stdin*, *stdout* 或 *stderr* 参数来处理创建函数的特殊值。 它表示将为相应的子进程流使用特殊文件 [`os.devnull`](https://docs.python.org/zh-cn/3.13/library/os.html#os.devnull)。

## 与子进程交互

[`create_subprocess_exec()`](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html#asyncio.create_subprocess_exec) 和 [`create_subprocess_shell()`](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html#asyncio.create_subprocess_shell) 函数都返回 *Process* 类的实例。 *Process* 是一个高层级包装器，它允许与子进程通信并监视其完成情况。

## *class* asyncio.subprocess.**Process**

​	一个用于包装 `create_subprocess_exec()` and `create_subprocess_shell()` 函数创建的 OS 进程的对象。

​	这个类被设计为具有与 [`subprocess.Popen`](https://docs.python.org/zh-cn/3.13/library/subprocess.html#subprocess.Popen) 类相似的 API，但两者有一些重要的差异:

- 不同于 Popen，Process 实例没有与 [`poll()`](https://docs.python.org/zh-cn/3.13/library/subprocess.html#subprocess.Popen.poll) 方法等价的方法；
- [`communicate()`](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html#asyncio.subprocess.Process.communicate) 和 [`wait()`](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html#asyncio.subprocess.Process.wait) 方法没有 *timeout* 形参：请使用 [`wait_for()`](https://docs.python.org/zh-cn/3.13/library/asyncio-task.html#asyncio.wait_for) 函数；
- [`Process.wait()`](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html#asyncio.subprocess.Process.wait) 方法是异步的，而 [`subprocess.Popen.wait()`](https://docs.python.org/zh-cn/3.13/library/subprocess.html#subprocess.Popen.wait) 方法则被实现为阻塞型忙循环；
- *universal_newlines* 形参不被支持。

​	这个类不是线程安全的（[not thread safe](https://docs.python.org/zh-cn/3.13/library/asyncio-dev.html#asyncio-multithreading)）。

​	请参阅 [子进程和线程](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html#asyncio-subprocess-threads) 部分。

## *coroutine* **wait**()

​	等待子进程终结。

​	设置并返回 [`returncode`](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html#asyncio.subprocess.Process.returncode) 属性。

​	备注

 

​	当使用 `stdout=PIPE` 或 `stderr=PIPE` 并且子进程产生了足以阻塞 OS 管道缓冲区等待接收更多的数据的输出时，此方法会发生死锁。 当使用管道时请使用 [`communicate()`](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html#asyncio.subprocess.Process.communicate) 方法来避免这种情况。

## *coroutine* **communicate**(*input=None*)

​	与进程交互:

1. 发送数据到 *stdin* (如果 *input* 不为 `None`)；
2. 关闭 *stdin*;
3. 从 *stdout* 和 *stderr* 读取数据，直至到达 EOF；
4. 等待进程终结。

​	可选的 *input* 参数为将被发送到子进程的数据 ([`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 对象)。

​	返回一个元组 `(stdout_data, stderr_data)`。

​	如果在将 *input* 写入到 *stdin* 时引发了 [`BrokenPipeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BrokenPipeError) 或 [`ConnectionResetError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ConnectionResetError) 异常，异常会被忽略。 此条件会在进程先于所有数据被写入到 *stdin* 之前退出时发生。

​	如果想要将数据发送到进程的 *stdin*，则创建进程时必须使用 `stdin=PIPE`。 类似地，要在结果元组中获得任何不为 `None` 的值，则创建进程时必须使用 `stdout=PIPE` 和/或 `stderr=PIPE` 参数。

​	注意，数据读取在内存中是带缓冲的，因此如果数据量过大或不受则不要使用此方法。

*在 3.12 版本发生变更:* *stdin* 在 input=None 时也会被关闭。

## **send_signal**(*signal*)

​	将信号 *signal* 发送给子进程。

​	备注

 

​	在 Windows 上，[`SIGTERM`](https://docs.python.org/zh-cn/3.13/library/signal.html#signal.SIGTERM) 是 [`terminate()`](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html#asyncio.subprocess.Process.terminate) 的别名。 `CTRL_C_EVENT` 和 `CTRL_BREAK_EVENT` 可被发送给启动时带有 *creationflags* 形参且其中包括 `CREATE_NEW_PROCESS_GROUP` 的进程。

## **terminate**()

​	停止子进程。

​	在 POSIX 系统上此方法会发送 [`SIGTERM`](https://docs.python.org/zh-cn/3.13/library/signal.html#signal.SIGTERM) 给子进程。

​	在 Windows 上会调用 Win32 API 函数 `TerminateProcess()` 来停止子进程。

## **kill**()

​	杀掉子进程。

​	在 POSIX 系统中此方法会发送 `SIGKILL` 给子进程。

​	在 Windows 上此方法是 [`terminate()`](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html#asyncio.subprocess.Process.terminate) 的别名。

## **stdin**

​	标准输入流 (`StreamWriter`) 或者如果进程创建时设置了 `stdin=None` 则为 `None`。

## **stdout**

​	标准输出流 (`StreamReader`) 或者如果进程创建时设置了 `stdout=None` 则为 `None`。

## **stderr**

​	标准错误流 (`StreamReader`) 或者如果进程创建时设置了 `stderr=None` 则为 `None`。

​	警告

 

​	使用 [`communicate()`](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html#asyncio.subprocess.Process.communicate) 方法而非 [`process.stdin.write()`](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html#asyncio.subprocess.Process.stdin), [`await process.stdout.read()`](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html#asyncio.subprocess.Process.stdout) 或 [`await process.stderr.read()`](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html#asyncio.subprocess.Process.stderr)。 这可以避免由于流暂停读取或写入并阻塞子进程而导致的死锁。

## **pid**

​	进程标识号（PID）。

​	注意对于由Note that for processes created by the `create_subprocess_shell()` 函数所创建的进程，这个属性将是所生成的 shell 的 PID。

## **returncode**

​	当进程退出时返回其代号。

`None` 值表示进程尚未终止。

​	一个负值 `-N` 表示子进程被信号 `N` 中断 (仅 POSIX).



### 子进程和线程

​	标准 asyncio 事件循环默认支持从不同线程中运行子进程。

​	在 Windows 上子进程（默认）只由 [`ProactorEventLoop`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.ProactorEventLoop) 提供，[`SelectorEventLoop`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.SelectorEventLoop) 没有子进程支持。

​	在 UNIX 上会使用 *child watchers* 来让子进程结束等待，详情请参阅 [进程监视器](https://docs.python.org/zh-cn/3.13/library/asyncio-policy.html#asyncio-watchers)。

*在 3.8 版本发生变更:* UNIX 对于从不同线程中无限制地生成子进程会切换为使用 [`ThreadedChildWatcher`](https://docs.python.org/zh-cn/3.13/library/asyncio-policy.html#asyncio.ThreadedChildWatcher)。

​	使用 *不活动的* 当前子监视器生成子进程将引发 [`RuntimeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#RuntimeError)。

​	请注意其他的事件循环实现可能有其本身的限制；请查看它们各自的文档。

​	参见

 

[asyncio 中的并发和多线程](https://docs.python.org/zh-cn/3.13/library/asyncio-dev.html#asyncio-multithreading) 章节。

### 例子

​	一个使用 [`Process`](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html#asyncio.subprocess.Process) 类来控制子进程并用 [`StreamReader`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.StreamReader) 类来从其标准输出读取信息的示例。

​	这个子进程是由 [`create_subprocess_exec()`](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html#asyncio.create_subprocess_exec) 函数创建的:

```
import asyncio
import sys

async def get_date():
    code = 'import datetime; print(datetime.datetime.now())'

    # 创建子进程；重定向标准输出
    # 至一个管道中。
    proc = await asyncio.create_subprocess_exec(
        sys.executable, '-c', code,
        stdout=asyncio.subprocess.PIPE)

    # 读取一行输出。
    data = await proc.stdout.readline()
    line = data.decode('ascii').rstrip()

    # 等待子进程退出。
    await proc.wait()
    return line

date = asyncio.run(get_date())
print(f"Current date: {date}")
```

​	另请参阅使用低层级 API 编写的 [相同示例](https://docs.python.org/zh-cn/3.13/library/asyncio-protocol.html#asyncio-example-subprocess-proto)。
