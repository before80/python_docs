+++
title = "multiprocessing --- 基于进程的并行"
date = 2024-11-15T12:21:05+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/multiprocessing.html](https://docs.python.org/zh-cn/3.13/library/multiprocessing.html)
>
> 收录该文档的时间：`2024-11-15T12:21:05+08:00`

# `multiprocessing` --- 基于进程的并行

**源代码** [Lib/multiprocessing/](https://github.com/python/cpython/tree/3.13/Lib/multiprocessing/)

------

[Availability]({{< ref "/library/intro#availability" >}}): not Android, not iOS, not WASI.

​	此模块在 [移动平台]({{< ref "/library/intro#mobile-availability" >}}) 或 [WebAssembly 平台]({{< ref "/library/intro#wasm-availability" >}}) 上不受支持。

## 概述

[`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 是一个支持使用与 [`threading`]({{< ref "/library/concurrency/threading#module-threading" >}}) 模块类似的 API 来产生进程的包。 [`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 包同时提供了本地和远程并发操作，通过使用子进程而非线程有效地绕过了 [全局解释器锁]({{< ref "/glossary/idx#term-global-interpreter-lock" >}})。 因此，[`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 模块允许程序员充分利用给定机器上的多个处理器。 它在 POSIX 和 Windows 上均可运行。

[`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 模块还引入了在 [`threading`]({{< ref "/library/concurrency/threading#module-threading" >}}) 模块中没有的API。一个主要的例子就是 [`Pool`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool" >}}) 对象，它提供了一种快捷的方法，赋予函数并行化处理一系列输入值的能力，可以将输入数据分配给不同进程处理（数据并行）。下面的例子演示了在模块中定义此类函数的常见做法，以便子进程可以成功导入该模块。这个数据并行的基本例子使用了 [`Pool`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool" >}}) ，

```
from multiprocessing import Pool

def f(x):
    return x*x

if __name__ == '__main__':
    with Pool(5) as p:
        print(p.map(f, [1, 2, 3]))
```

​	将在标准输出中打印

```
[1, 4, 9]
```

​参见
 

[`concurrent.futures.ProcessPoolExecutor`]({{< ref "/library/concurrency/future/concurrent_futures#concurrent.futures.ProcessPoolExecutor" >}}) 提供了一个更高层级的接口用来将任务推送到后台进程而不会阻塞调用方进程的执行。 与直接使用 [`Pool`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool" >}}) 接口相比，[`concurrent.futures`]({{< ref "/library/concurrency/future/concurrent_futures#module-concurrent.futures" >}}) API 能更好地允许将工作单元发往无需等待结果的下层进程池。

### `Process` 类

​	在 [`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 中，通过创建一个 [`Process`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process" >}}) 对象然后调用它的 `start()` 方法来生成进程。 [`Process`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process" >}}) 和 [`threading.Thread`]({{< ref "/library/concurrency/threading#threading.Thread" >}}) API 相同。 一个简单的多进程程序示例是:

```
from multiprocessing import Process

def f(name):
    print('hello', name)

if __name__ == '__main__':
    p = Process(target=f, args=('bob',))
    p.start()
    p.join()
```

​	要显示所涉及的各个进程ID，这是一个扩展示例:

```
from multiprocessing import Process
import os

def info(title):
    print(title)
    print('module name:', __name__)
    print('parent process:', os.getppid())
    print('process id:', os.getpid())

def f(name):
    info('function f')
    print('hello', name)

if __name__ == '__main__':
    info('main line')
    p = Process(target=f, args=('bob',))
    p.start()
    p.join()
```

​	关于为什么 `if __name__ == '__main__'` 部分是必需的解释，请参见 [编程指导]({{< ref "/library/concurrency/multiprocessing#multiprocessing-programming" >}})。



### 上下文和启动方法

​	根据不同的平台， [`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 支持三种启动进程的方法。这些 *启动方法* 有

> ## *spawn*
>
> ​	父进程会启动一个新的 Python 解释器进程。 子进程将只继承那些运行进程对象的 [`run()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.run" >}}) 方法所必须的资源。 特别地，来自父进程的非必需文件描述符和句柄将不会被继承。 使用此方法启动进程相比使用 *fork* 或 *forkserver* 要慢上许多。
>
> ​	在 POSIX 和 Windows 平台上可用。 默认在 Windows 和 macOS 上。
>
> ## *fork*
>
> ​	父进程使用 [`os.fork()`]({{< ref "/library/allos/os#os.fork" >}}) 来产生 Python 解释器分叉。子进程在开始时实际上与父进程相同。父进程的所有资源都由子进程继承。请注意，安全分叉多线程进程是棘手的。
>
> ​	在 POSIX 系统上可用。 目前在除 macOS 之外的 POSIX 上为默认值。
>
> ​备注>
>  
>
> ​	在 Python 3.14 上默认的启动方法将不再为 *fork*。 需要 *fork* 的代码应当显式地通过 [`get_context()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.get_context" >}}) 或 [`set_start_method()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.set_start_method" >}}) 来指定。
>
> > 在 3.12 版本发生变更: 如果 Python 能够检测到你的进程有多个线程，那么在该启动方法内部调用 [`os.fork()`]({{< ref "/library/allos/os#os.fork" >}}) 函数将引发 [`DeprecationWarning`]({{< ref "/library/exceptions#DeprecationWarning" >}})。 请使用其他启动方法。 进一步的解释参见 [`os.fork()`]({{< ref "/library/allos/os#os.fork" >}}) 文档。
>
> ## *forkserver*
>
> ​	当程序启动并选择 *forkserver* 启动方法时，将产生一个服务器进程。 从那时起，每当需要一个新进程时，父进程就会连接到该服务器并请求它分叉一个新进程。 分叉服务器进程是单线程的，除非因系统库或预加载导入的附带影响改变了这一点，因此使用 [`os.fork()`]({{< ref "/library/allos/os#os.fork" >}}) 通常是安全的。 没有不必要的资源被继承。
>
> ​	在支持通过 Unix 管道传递文件描述符的 POSIX 平台上可用，例如 Linux。

> 在 3.4 版本发生变更: 在所有 POSIX 平台上添加了 *spawn*，并为某些 POSIX 平台添加了 *forkserver*。 在 Windows 上子进程将不再继承父进程的所有可继承句柄。

> 在 3.8 版本发生变更: 在 macOS 上，现在 *spawn* 启动方法是默认值。 由于 *fork* 启动方法可能因 macOS 系统库也许会启动线程导致子进程崩溃因而应当被视为是不安全的。 参见 [bpo-33725](https://bugs.python.org/issue?@action=redirect&bpo=33725)。

​	在 POSIX 上使用 *spawn* 或 *forkserver* 启动方法将同时启动一个 *资源追踪器* 进程，负责追踪当前程序的进程产生的已取消连接的命名系统资源（如命名信号量或 [`SharedMemory`]({{< ref "/library/concurrency/multiprocessing_shared_memory#multiprocessing.shared_memory.SharedMemory" >}}) 对象）。 当所有进程退出后资源追踪器会负责取消链接任何仍然被追踪的对象。 在通常情况下应该是没有此种对象的，但是如果一个子进程是被某个信号杀掉的则可能存在一些“泄露”的资源。 （泄露的信号量或共享的内存段不会被自动取消链接直到下一次重启。 对于这两种对象来说会有问题因为系统只允许有限数量的命名信号量，而共享的内存段在主内存中占用一些空间。）

​	要选择一个启动方法，你应该在主模块的 `if __name__ == '__main__'` 子句中调用 [`set_start_method()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.set_start_method" >}}) 。例如：

```
import multiprocessing as mp

def foo(q):
    q.put('hello')

if __name__ == '__main__':
    mp.set_start_method('spawn')
    q = mp.Queue()
    p = mp.Process(target=foo, args=(q,))
    p.start()
    print(q.get())
    p.join()
```

​	在程序中 [`set_start_method()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.set_start_method" >}}) 不应该被多次调用。

​	或者，你可以使用 [`get_context()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.get_context" >}}) 来获取上下文对象。上下文对象与 multiprocessing 模块具有相同的API，并允许在同一程序中使用多种启动方法。:

```
import multiprocessing as mp

def foo(q):
    q.put('hello')

if __name__ == '__main__':
    ctx = mp.get_context('spawn')
    q = ctx.Queue()
    p = ctx.Process(target=foo, args=(q,))
    p.start()
    print(q.get())
    p.join()
```

​	请注意，对象在不同上下文创建的进程间可能并不兼容。 特别是，使用 *fork* 上下文创建的锁不能传递给使用 *spawn* 或 *forkserver* 启动方法启动的进程。

​	想要使用特定启动方法的库应该使用 [`get_context()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.get_context" >}}) 以避免干扰库用户的选择。

​	警告

 

`'spawn'` 和 `'forkserver'` 启动方法在 POSIX 系统上通常不能与“已冻结”可执行程序一同使用（例如由 **PyInstaller** 和 **cx_Freeze** 等软件包产生的二进制文件）。 如果代码没有使用线程则可以使用 `'fork'` 启动方法。

### 在进程之间交换对象

[`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 支持进程之间的两种通信通道：

**队列**

> [`Queue`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Queue" >}}) 类是一个近似 [`queue.Queue`]({{< ref "/library/concurrency/queue#queue.Queue" >}}) 的克隆。 例如:
>
> ```
> from multiprocessing import Process, Queue
> 
> def f(q):
>     q.put([42, None, 'hello'])
> 
> if __name__ == '__main__':
>     q = Queue()
>     p = Process(target=f, args=(q,))
>     p.start()
>     print(q.get())    # 打印 "[42, None, 'hello']"
>     p.join()
> ```
>
> ​	队列是线程和进程安全的。 任何放入 [`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 队列的对象都将被序列化。

**管道**

> [`Pipe()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Pipe" >}}) 函数返回一个由管道连接的连接对象，默认情况下是双工（双向）。例如:
>
> ```
> from multiprocessing import Process, Pipe
> 
> def f(conn):
>     conn.send([42, None, 'hello'])
>     conn.close()
> 
> if __name__ == '__main__':
>     parent_conn, child_conn = Pipe()
>     p = Process(target=f, args=(child_conn,))
>     p.start()
>     print(parent_conn.recv())   # 打印 "[42, None, 'hello']"
>     p.join()
> ```
>
> ​	返回的两个连接对象 [`Pipe()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Pipe" >}}) 表示管道的两端。每个连接对象都有 `send()` 和 `recv()` 方法（相互之间的）。请注意，如果两个进程（或线程）同时尝试读取或写入管道的 *同一* 端，则管道中的数据可能会损坏。当然，在不同进程中同时使用管道的不同端的情况下不存在损坏的风险。
>
> `send()` 方法将序列化对象而 `recv()` 将重新创建该对象。

### 进程间同步

[`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 包含来自 [`threading`]({{< ref "/library/concurrency/threading#module-threading" >}}) 的所有同步原语的等价物。例如，可以使用锁来确保一次只有一个进程打印到标准输出:

```
from multiprocessing import Process, Lock

def f(l, i):
    l.acquire()
    try:
        print('hello world', i)
    finally:
        l.release()

if __name__ == '__main__':
    lock = Lock()

    for num in range(10):
        Process(target=f, args=(lock, num)).start()
```

​	不使用锁的情况下，来自于多进程的输出很容易产生混淆。

### 进程间共享状态

​	如上所述，在进行并发编程时，通常最好尽量避免使用共享状态。使用多个进程时尤其如此。

​	但是，如果你真的需要使用一些共享数据，那么 [`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 提供了两种方法。

**共享内存**

> ​	可以使用 [`Value`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Value" >}}) 或 [`Array`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Array" >}}) 将数据存储在共享内存映射中。例如，以下代码:
>
> ```
> from multiprocessing import Process, Value, Array
> 
> def f(n, a):
>     n.value = 3.1415927
>     for i in range(len(a)):
>         a[i] = -a[i]
> 
> if __name__ == '__main__':
>     num = Value('d', 0.0)
>     arr = Array('i', range(10))
> 
>     p = Process(target=f, args=(num, arr))
>     p.start()
>     p.join()
> 
>     print(num.value)
>     print(arr[:])
> ```
>
> ​	将打印
>
> ```
> 3.1415927
> [0, -1, -2, -3, -4, -5, -6, -7, -8, -9]
> ```
>
> ​	创建 `num` 和 `arr` 时使用的 `'d'` 和 `'i'` 参数是 [`array`]({{< ref "/library/datatypes/array#module-array" >}}) 模块使用的类型的 typecode ： `'d'` 表示双精度浮点数， `'i'` 表示有符号整数。这些共享对象将是进程和线程安全的。
>
> ​	为了更灵活地使用共享内存，可以使用 [`multiprocessing.sharedctypes`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing.sharedctypes" >}}) 模块，该模块支持创建从共享内存分配的任意ctypes对象。

**服务进程**

> ​	由 [`Manager()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Manager" >}}) 返回的管理器对象控制一个服务进程，该进程保存Python对象并允许其他进程使用代理操作它们。
>
> [`Manager()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Manager" >}}) 返回的管理器支持类型： [`list`]({{< ref "/library/stdtypes#list" >}}) 、 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 、 [`Namespace`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.managers.Namespace" >}}) 、 [`Lock`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Lock" >}}) 、 [`RLock`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.RLock" >}}) 、 [`Semaphore`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Semaphore" >}}) 、 [`BoundedSemaphore`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.BoundedSemaphore" >}}) 、 [`Condition`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Condition" >}}) 、 [`Event`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Event" >}}) 、 [`Barrier`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Barrier" >}}) 、 [`Queue`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Queue" >}}) 、 [`Value`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Value" >}}) 和 [`Array`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Array" >}}) 。例如
>
> ```
> from multiprocessing import Process, Manager
> 
> def f(d, l):
>     d[1] = '1'
>     d['2'] = 2
>     d[0.25] = None
>     l.reverse()
> 
> if __name__ == '__main__':
>     with Manager() as manager:
>         d = manager.dict()
>         l = manager.list(range(10))
> 
>         p = Process(target=f, args=(d, l))
>         p.start()
>         p.join()
> 
>         print(d)
>         print(l)
> ```
>
> ​	将打印
>
> ```
> {0.25: None, 1: '1', '2': 2}
> [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]
> ```
>
> ​	使用服务进程的管理器比使用共享内存对象更灵活，因为它们可以支持任意对象类型。此外，单个管理器可以通过网络由不同计算机上的进程共享。但是，它们比使用共享内存慢。

### 使用工作进程

[`Pool`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool" >}}) 类表示一个工作进程池。它具有允许以几种不同方式将任务分配到工作进程的方法。

​	例如：

```
from multiprocessing import Pool, TimeoutError
import time
import os

def f(x):
    return x*x

if __name__ == '__main__':
    # start 4 worker processes
    with Pool(processes=4) as pool:

        # print "[0, 1, 4,..., 81]"
        print(pool.map(f, range(10)))

        # print same numbers in arbitrary order
        for i in pool.imap_unordered(f, range(10)):
            print(i)

        # evaluate "f(20)" asynchronously
        res = pool.apply_async(f, (20,))      # runs in *only* one process
        print(res.get(timeout=1))             # prints "400"

        # evaluate "os.getpid()" asynchronously
        res = pool.apply_async(os.getpid, ()) # runs in *only* one process
        print(res.get(timeout=1))             # prints the PID of that process

        # launching multiple evaluations asynchronously *may* use more processes
        multiple_results = [pool.apply_async(os.getpid, ()) for i in range(4)]
        print([res.get(timeout=1) for res in multiple_results])

        # make a single worker sleep for 10 seconds
        res = pool.apply_async(time.sleep, (10,))
        try:
            print(res.get(timeout=1))
        except TimeoutError:
            print("We lacked patience and got a multiprocessing.TimeoutError")

        print("For the moment, the pool remains available for more work")

    # exiting the 'with'-block has stopped the pool
    print("Now the pool is closed and no longer available")
```

​	请注意，进程池的方法只能由创建它的进程使用。

​备注
 

​	这个包中的功能要求子进程可以导入 `__main__` 模块。虽然这在 [编程指导]({{< ref "/library/concurrency/multiprocessing#multiprocessing-programming" >}}) 中有描述，但还是需要提前说明一下。这意味着一些示例在交互式解释器中不起作用，比如 [`multiprocessing.pool.Pool`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool" >}}) 示例。例如:



``` python
>>> from multiprocessing import Pool
>>> p = Pool(5)
>>> def f(x):
...     return x*x
...
>>> with p:
...     p.map(f, [1,2,3])
Process PoolWorker-1:
Process PoolWorker-2:
Process PoolWorker-3:
Traceback (most recent call last):
Traceback (most recent call last):
Traceback (most recent call last):
AttributeError: Can't get attribute 'f' on <module '__main__' (<class '_frozen_importlib.BuiltinImporter'>)>
AttributeError: Can't get attribute 'f' on <module '__main__' (<class '_frozen_importlib.BuiltinImporter'>)>
AttributeError: Can't get attribute 'f' on <module '__main__' (<class '_frozen_importlib.BuiltinImporter'>)>
```

​	（如果尝试执行上面的代码，它会以一种半随机的方式将三个完整的堆栈内容交替输出，然后你只能以某种方式停止父进程。)

## 参考

[`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 包主要复制了 [`threading`]({{< ref "/library/concurrency/threading#module-threading" >}}) 模块的API。

### `Process` 和异常

## *class* multiprocessing.**Process**(*group=None*, *target=None*, *name=None*, *args=()*, *kwargs={}*, ***, *daemon=None*)

​	进程对象表示在单独进程中运行的活动。 [`Process`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process" >}}) 类拥有和 [`threading.Thread`]({{< ref "/library/concurrency/threading#threading.Thread" >}}) 等价的大部分方法。

​	The constructor should always be called with keyword arguments. *group* should always be `None`; it exists solely for compatibility with [`threading.Thread`]({{< ref "/library/concurrency/threading#threading.Thread" >}}). *target* is the callable object to be invoked by the [`run()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.run" >}}) method. It defaults to `None`, meaning nothing is called. *name* is the process name (see [`name`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.name" >}}) for more details). *args* is the argument tuple for the target invocation. *kwargs* is a dictionary of keyword arguments for the target invocation. If provided, the keyword-only *daemon* argument sets the process [`daemon`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.daemon" >}}) flag to `True` or `False`. If `None` (the default), this flag will be inherited from the creating process.

​	在默认情况下，不会将任何参数传递给 *target*。 *args* 参数默认值为 `()`，可被用来指定要传递给 *target* 的参数列表或元组。

​	如果子类重写构造函数，它必须确保它在对进程执行任何其他操作之前调用基类构造函数（ `Process.__init__()` ）。

> 在 3.3 版本发生变更: 增加了 *daemon* 形参。

## **run**()

​	表示进程活动的方法。

​	你可以在子类中重写此方法。标准 [`run()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.run" >}}) 方法调用传递给对象构造函数的可调用对象作为目标参数（如果有），分别从 *args* 和 *kwargs* 参数中获取顺序和关键字参数。

​	使用列表或元组作为传给 [`Process`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process" >}}) 的 *args* 参数可以达成同样的效果。

​	示例：



``` python
>>> from multiprocessing import Process
>>> p = Process(target=print, args=[1])
>>> p.run()
1
>>> p = Process(target=print, args=(1,))
>>> p.run()
1
```

## **start**()

​	启动进程活动。

​	这个方法每个进程对象最多只能调用一次。它会将对象的 [`run()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.run" >}}) 方法安排在一个单独的进程中调用。

## **join**([*timeout*])

​	如果可选参数 *timeout* 是 `None` （默认值），则该方法将阻塞，直到调用 [`join()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.join" >}}) 方法的进程终止。如果 *timeout* 是一个正数，它最多会阻塞 *timeout* 秒。请注意，如果进程终止或方法超时，则该方法返回 `None` 。检查进程的 [`exitcode`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.exitcode" >}}) 以确定它是否终止。

​	一个进程可以被 join 多次。

​	进程无法join自身，因为这会导致死锁。尝试在启动进程之前join进程是错误的。

## **name**

​	进程的名称。该名称是一个字符串，仅用于识别目的。它没有语义。可以为多个进程指定相同的名称。

​	初始名称由构造器设定。 如果没有为构造器提供显式名称，则会构造一个形式为 'Process-N1:N2:...:Nk' 的名称，其中每个 Nk 是其父亲的第 N 个孩子。

## **is_alive**()

​	返回进程是否还活着。

​	粗略地说，从 [`start()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.start" >}}) 方法返回到子进程终止之前，进程对象仍处于活动状态。

## **daemon**

​	进程的守护标志，一个布尔值。这必须在 [`start()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.start" >}}) 被调用之前设置。

​	初始值继承自创建进程。

​	当进程退出时，它会尝试终止其所有守护进程子进程。

​	请注意，不允许在守护进程中创建子进程。这是因为当守护进程由于父进程退出而中断时，其子进程会变成孤儿进程。 另外，这些 **不是** Unix 守护进程或服务，它们是正常进程，如果非守护进程已经退出，它们将被终止（并且不被合并）。

​	除了 [`threading.Thread`]({{< ref "/library/concurrency/threading#threading.Thread" >}}) API ，[`Process`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process" >}}) 对象还支持以下属性和方法：

## **pid**

​	返回进程ID。在生成该进程之前，这将是 `None` 。

## **exitcode**

​	子进程的退出代码。如果该进程尚未终止则为 `None` 。

​	如果子进程的 [`run()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.run" >}}) 方法正常返回，退出代码将是 0 。 如果它通过 [`sys.exit()`]({{< ref "/library/python/sys#sys.exit" >}}) 终止，并有一个整数参数 *N* ，退出代码将是 *N* 。

​	如果子进程由于在 [`run()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.run" >}}) 内的未捕获异常而终止，退出代码将是 1 。 如果它是由信号 *N* 终止的，退出代码将是负值 *-N* 。

## **authkey**

​	进程的身份验证密钥（字节字符串）。

​	当 [`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 初始化时，主进程使用 [`os.urandom()`]({{< ref "/library/allos/os#os.urandom" >}}) 分配一个随机字符串。

​	当创建 [`Process`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process" >}}) 对象时，它将继承其父进程的身份验证密钥，尽管可以通过将 [`authkey`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.authkey" >}}) 设置为另一个字节字符串来更改。

​	参见 [认证密码]({{< ref "/library/concurrency/multiprocessing#multiprocessing-auth-keys" >}}) 。

## **sentinel**

​	系统对象的数字句柄，当进程结束时将变为 "ready" 。

​	You can use this value if you want to wait on several events at once using [`multiprocessing.connection.wait()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.connection.wait" >}}). Otherwise calling [`join()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.join" >}}) is simpler.

​	在 Windows 上，这是一个可以与 `WaitForSingleObject` 和 `WaitForMultipleObjects` API 调用族一起使用的 OS 句柄。 在 POSIX 上，这是一个可以与来自 [`select`]({{< ref "/library/ipc/select#module-select" >}}) 模块的原语一起使用的文件描述符。

> Added in version 3.3.
>

## **terminate**()

​	终结进程。 在 POSIX 上这是使用 [`SIGTERM`]({{< ref "/library/ipc/signal#signal.SIGTERM" >}}) 信号来完成的；在 Windows 上则会使用 `TerminateProcess()`。 请注意 exit 处理器和 finally 子句等将不会被执行。

​	请注意，进程的后代进程将不会被终止 —— 它们将简单地变成孤立的。

​	警告

 

​	如果在关联进程使用管道或队列时使用此方法，则管道或队列可能会损坏，并可能无法被其他进程使用。类似地，如果进程已获得锁或信号量等，则终止它可能导致其他进程死锁。

## **kill**()

​	Same as [`terminate()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.terminate" >}}) but using the `SIGKILL` signal on POSIX.

> Added in version 3.7.
>

## **close**()

​	关闭 [`Process`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process" >}}) 对象，释放与之关联的所有资源。如果底层进程仍在运行，则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 。一旦 [`close()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.close" >}}) 成功返回， [`Process`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process" >}}) 对象的大多数其他方法和属性将引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 。

> Added in version 3.7.
>

​	注意 [`start()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.start" >}}) 、 [`join()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.join" >}}) 、 [`is_alive()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.is_alive" >}}) 、 [`terminate()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.terminate" >}}) 和 [`exitcode`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.exitcode" >}}) 方法只能由创建进程对象的进程调用。

[`Process`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process" >}}) 一些方法的示例用法：



``` python
>>> import multiprocessing, time, signal
>>> mp_context = multiprocessing.get_context('spawn')
>>> p = mp_context.Process(target=time.sleep, args=(1000,))
>>> print(p, p.is_alive())
<...Process ... initial> False
>>> p.start()
>>> print(p, p.is_alive())
<...Process ... started> True
>>> p.terminate()
>>> time.sleep(0.1)
>>> print(p, p.is_alive())
<...Process ... stopped exitcode=-SIGTERM> False
>>> p.exitcode == -signal.SIGTERM
True
```

## *exception* multiprocessing.**ProcessError**

​	所有 [`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 异常的基类。

## *exception* multiprocessing.**BufferTooShort**

​	Exception raised by `Connection.recv_bytes_into()` when the supplied buffer object is too small for the message read.

​	如果 `e` 是一个 [`BufferTooShort`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.BufferTooShort" >}}) 实例，那么 `e.args[0]` 将把消息作为字节字符串给出。

## *exception* multiprocessing.**AuthenticationError**

​	出现身份验证错误时引发。

## *exception* multiprocessing.**TimeoutError**

​	有超时的方法超时时引发。

### 管道和队列

​	使用多进程时，一般使用消息机制实现进程间通信，尽可能避免使用同步原语，例如锁。

​	消息机制包含： [`Pipe()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Pipe" >}}) (可以用于在两个进程间传递消息)，以及队列(能够在多个生产者和消费者之间通信)。

[`Queue`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Queue" >}}), [`SimpleQueue`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.SimpleQueue" >}}) 以及 [`JoinableQueue`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.JoinableQueue" >}}) 都是多生产者，多消费者，并且实现了 FIFO 的队列类型，其表现与标准库中的 [`queue.Queue`]({{< ref "/library/concurrency/queue#queue.Queue" >}}) 类相似。 不同之处在于 [`Queue`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Queue" >}}) 缺少标准库的 [`queue.Queue`]({{< ref "/library/concurrency/queue#queue.Queue" >}}) 从 Python 2.5 开始引入的 [`task_done()`]({{< ref "/library/concurrency/queue#queue.Queue.task_done" >}}) 和 [`join()`]({{< ref "/library/concurrency/queue#queue.Queue.join" >}}) 方法。

​	如果你使用了 [`JoinableQueue`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.JoinableQueue" >}}) ，那么你 **必须** 对每个已经移出队列的任务调用 [`JoinableQueue.task_done()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.JoinableQueue.task_done" >}})。 不然的话用于统计未完成任务的信号量最终会溢出并抛出异常。

​	与其他 Python 队列实现的区别之一，在于 [`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 队列会使用 [`pickle`]({{< ref "/library/persistence/pickle#module-pickle" >}}) 来序列化所有被放入的对象。 由获取方法所返回的对象是重新创建的对象，它不会与原始对象共享内存。

​	另外还可以通过使用一个管理器对象创建一个共享队列，详见 [管理器]({{< ref "/library/concurrency/multiprocessing#multiprocessing-managers" >}}) 。

​备注
 

[`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 使用了普通的 [`queue.Empty`]({{< ref "/library/concurrency/queue#queue.Empty" >}}) 和 [`queue.Full`]({{< ref "/library/concurrency/queue#queue.Full" >}}) 异常去表示超时。 你需要从 [`queue`]({{< ref "/library/concurrency/queue#module-queue" >}}) 中导入它们，因为它们并不在 [`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 的命名空间中。

​备注
 

​	当一个对象被放入一个队列中时，这个对象首先会被一个后台线程用 pickle 序列化，并将序列化后的数据通过一个底层管道的管道传递到队列中。 这种做法会有点让人惊讶，但一般不会出现什么问题。 如果它们确实妨碍了你，你可以使用一个由管理器 [manager]({{< ref "/library/concurrency/multiprocessing#multiprocessing-managers" >}}) 创建的队列替换它。

1. 将一个对象放入一个空队列后，可能需要极小的延迟，队列的方法 [`empty()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Queue.empty" >}}) 才会返回 [`False`]({{< ref "/library/constants#False" >}}) 。而 [`get_nowait()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Queue.get_nowait" >}}) 可以不抛出 [`queue.Empty`]({{< ref "/library/concurrency/queue#queue.Empty" >}}) 直接返回。
2. 如果有多个进程同时将对象放入队列，那么在队列的另一端接受到的对象可能是无序的。但是由同一个进程放入的多个对象的顺序在另一端输出时总是一样的。

​	警告

 

​	如果一个进程在尝试使用 [`Queue`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Queue" >}}) 期间被 [`Process.terminate()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.terminate" >}}) 或 [`os.kill()`]({{< ref "/library/allos/os#os.kill" >}}) 调用终止了，那么队列中的数据很可能被破坏。 这可能导致其他进程在尝试使用该队列时发生异常。

​	警告

 

​	正如刚才提到的，如果一个子进程将一些对象放进队列中 (并且它没有用 [`JoinableQueue.cancel_join_thread`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Queue.cancel_join_thread" >}}) 方法)，那么这个进程在所有缓冲区的对象被刷新进管道之前，是不会终止的。

​	这意味着，除非你确定所有放入队列中的对象都已经被消费了，否则如果你试图等待这个进程，你可能会陷入死锁中。相似地，如果该子进程不是后台进程，那么父进程可能在试图等待所有非后台进程退出时挂起。

​	注意用管理器创建的队列不存在这个问题，详见 [编程指导]({{< ref "/library/concurrency/multiprocessing#multiprocessing-programming" >}}) 。

​	该 [例子]({{< ref "/library/concurrency/multiprocessing#multiprocessing-examples" >}}) 展示了如何使用队列实现进程间通信。

## multiprocessing.**Pipe**([*duplex*])

​	返回一对 [`Connection`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.connection.Connection" >}}) 对象 `(conn1, conn2)` ， 分别表示管道的两端。

​	如果 *duplex* 被置为 `True` (默认值)，那么该管道是双向的。如果 *duplex* 被置为 `False` ，那么该管道是单向的，即 `conn1` 只能用于接收消息，而 `conn2` 仅能用于发送消息。

`send()` 方法将使用 [`pickle`]({{< ref "/library/persistence/pickle#module-pickle" >}}) 来序列化对象而 `recv()` 将重新创建该对象。

## *class* multiprocessing.**Queue**([*maxsize*])

​	返回一个使用一个管道和少量锁和信号量实现的共享队列实例。当一个进程将一个对象放进队列中时，一个写入线程会启动并将对象从缓冲区写入管道中。

​	一旦超时，将抛出标准库 [`queue`]({{< ref "/library/concurrency/queue#module-queue" >}}) 模块中常见的异常 [`queue.Empty`]({{< ref "/library/concurrency/queue#queue.Empty" >}}) 和 [`queue.Full`]({{< ref "/library/concurrency/queue#queue.Full" >}})。

​	除了 [`task_done()`]({{< ref "/library/concurrency/queue#queue.Queue.task_done" >}}) 和 [`join()`]({{< ref "/library/concurrency/queue#queue.Queue.join" >}}) 之外，[`Queue`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Queue" >}}) 实现了标准库类 [`queue.Queue`]({{< ref "/library/concurrency/queue#queue.Queue" >}}) 中所有的方法。

## **qsize**()

​	返回队列的大致长度。由于多线程或者多进程的上下文，这个数字是不可靠的。

​	请注意这可能会在未实现 `sem_getvalue()` 的平台如 macOS 上引发 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}})。

## **empty**()

​	如果队列是空的，返回 `True` ，反之返回 `False` 。 由于多线程或多进程的环境，该状态是不可靠的。

​	在已关闭的队列上可能会引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}})。 （但不保证如此）

## **full**()

​	如果队列是满的，返回 `True` ，反之返回 `False` 。 由于多线程或多进程的环境，该状态是不可靠的。

## **put**(*obj*[, *block*[, *timeout*]])

​	将 obj 放入队列。如果可选参数 *block* 是 `True` (默认值) 而且 *timeout* 是 `None` (默认值), 将会阻塞当前进程，直到有空的缓冲槽。如果 *timeout* 是正数，将会在阻塞了最多 *timeout* 秒之后还是没有可用的缓冲槽时抛出 [`queue.Full`]({{< ref "/library/concurrency/queue#queue.Full" >}}) 异常。反之 (*block* 是 `False` 时)，仅当有可用缓冲槽时才放入对象，否则抛出 [`queue.Full`]({{< ref "/library/concurrency/queue#queue.Full" >}}) 异常 (在这种情形下 *timeout* 参数会被忽略)。

> 在 3.8 版本发生变更: 如果队列已经关闭，会抛出 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 而不是 [`AssertionError`]({{< ref "/library/exceptions#AssertionError" >}}) 。

## **put_nowait**(*obj*)

​	相当于 `put(obj, False)`。

## **get**([*block*[, *timeout*]])

​	从队列中取出并返回对象。如果可选参数 *block* 是 `True` (默认值) 而且 *timeout* 是 `None` (默认值), 将会阻塞当前进程，直到队列中出现可用的对象。如果 *timeout* 是正数，将会在阻塞了最多 *timeout* 秒之后还是没有可用的对象时抛出 [`queue.Empty`]({{< ref "/library/concurrency/queue#queue.Empty" >}}) 异常。反之 (*block* 是 `False` 时)，仅当有可用对象能够取出时返回，否则抛出 [`queue.Empty`]({{< ref "/library/concurrency/queue#queue.Empty" >}}) 异常 (在这种情形下 *timeout* 参数会被忽略)。

> 在 3.8 版本发生变更: 如果队列已经关闭，会抛出 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 而不是 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 。

## **get_nowait**()

​	相当于 `get(False)` 。

[`multiprocessing.Queue`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Queue" >}}) 类有一些在 [`queue.Queue`]({{< ref "/library/concurrency/queue#queue.Queue" >}}) 类中没有出现的方法。这些方法在大多数情形下并不是必须的。

## **close**()

​	指示当前进程将不会再往队列中放入对象。一旦所有缓冲区中的数据被写入管道之后，后台的线程会退出。这个方法在队列被gc回收时会自动调用。

## **join_thread**()

​	等待后台线程。这个方法仅在调用了 [`close()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Queue.close" >}}) 方法之后可用。这会阻塞当前进程，直到后台线程退出，确保所有缓冲区中的数据都被写入管道中。

​	默认情况下，如果一个不是队列创建者的进程试图退出，它会尝试等待这个队列的后台线程。这个进程可以使用 [`cancel_join_thread()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Queue.cancel_join_thread" >}}) 让 [`join_thread()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Queue.join_thread" >}}) 方法什么都不做直接跳过。

## **cancel_join_thread**()

​	防止 [`join_thread()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Queue.join_thread" >}}) 方法阻塞当前进程。具体而言，这防止进程退出时自动等待后台线程退出。详见 [`join_thread()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Queue.join_thread" >}})。

​	这个方法更好的名字可能是 `allow_exit_without_flush()`。 这可能会导致已排入队列的数据丢失，几乎可以肯定你将不需要用到这个方法。 实际上它仅适用于当你需要当前进程立即退出而不必等待将已排入的队列更新到下层管道，并且你不担心丢失数据的时候。

​备注
 

​	该类的功能依赖于宿主操作系统具有可用的共享信号量实现。否则该类将被禁用，任何试图实例化一个 [`Queue`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Queue" >}}) 对象的操作都会抛出 [`ImportError`]({{< ref "/library/exceptions#ImportError" >}}) 异常，更多信息详见 [bpo-3770](https://bugs.python.org/issue?@action=redirect&bpo=3770) 。后续说明的任何专用队列对象亦如此。

## *class* multiprocessing.**SimpleQueue**

​	这是一个简化的 [`Queue`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Queue" >}}) 类的实现，很像带锁的 [`Pipe`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Pipe" >}}) 。

## **close**()

​	关闭队列：释放内部资源。

​	队列在被关闭后就不可再被使用。 例如不可再调用 [`get()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.SimpleQueue.get" >}}), [`put()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.SimpleQueue.put" >}}) 和 [`empty()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.SimpleQueue.empty" >}}) 等方法。

> Added in version 3.9.
>

## **empty**()

​	如果队列为空返回 `True` ，否则返回 `False` 。

​	如果 SimpleQueue 已关闭则总是会引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}})。

## **get**()

​	从队列中移出并返回一个对象。

## **put**(*item*)

​	将 *item* 放入队列。

## *class* multiprocessing.**JoinableQueue**([*maxsize*])

[`JoinableQueue`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.JoinableQueue" >}}) 类是 [`Queue`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Queue" >}}) 的子类，额外添加了 [`task_done()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.JoinableQueue.task_done" >}}) 和 [`join()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.JoinableQueue.join" >}}) 方法。

## **task_done**()

​	指出之前进入队列的任务已经完成。由队列的消费者进程使用。对于每次调用 [`get()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Queue.get" >}}) 获取的任务，执行完成后调用 [`task_done()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.JoinableQueue.task_done" >}}) 告诉队列该任务已经处理完成。

​	如果 [`join()`]({{< ref "/library/concurrency/queue#queue.Queue.join" >}}) 方法正在阻塞之中，该方法会在所有对象都被处理完的时候返回 (即对之前使用 [`put()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Queue.put" >}}) 放进队列中的所有对象都已经返回了对应的 [`task_done()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.JoinableQueue.task_done" >}}) ) 。

​	如果被调用的次数多于放入队列中的项目数量，将引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 异常 。

## **join**()

​	阻塞至队列中所有的元素都被接收和处理完毕。

​	当条目添加到队列的时候，未完成任务的计数就会增加。每当消费者进程调用 [`task_done()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.JoinableQueue.task_done" >}}) 表示这个条目已经被回收，该条目所有工作已经完成，未完成计数就会减少。当未完成计数降到零的时候， `join()` 阻塞被解除。

### 杂项

## multiprocessing.**active_children**()

​	返回当前进程存活的子进程的列表。

​	调用该方法有“等待”已经结束的进程的副作用。

## multiprocessing.**cpu_count**()

​	返回系统的CPU数量。

​	该数值不等于当前进程可使用的 CPU 数量。 可用的 CPU 数量可以通过 [`os.process_cpu_count()`]({{< ref "/library/allos/os#os.process_cpu_count" >}}) (或 `len(os.sched_getaffinity(0))`) 获得。

​	当 CPU 的数量无法确定时，会引发 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}}) 。

​参见
 

[`os.cpu_count()`]({{< ref "/library/allos/os#os.cpu_count" >}}) [`os.process_cpu_count()`]({{< ref "/library/allos/os#os.process_cpu_count" >}})

> 在 3.13 版本发生变更: 返回值也可使用 [`-X cpu_count`]({{< ref "/using/cmdline#cmdoption-X" >}}) 旗标或 [`PYTHON_CPU_COUNT`]({{< ref "/using/cmdline#envvar-PYTHON_CPU_COUNT" >}}) 来覆盖因为这只是一个针对 [`os`]({{< ref "/library/allos/os#module-os" >}}) cpu count API 的包装器。

## multiprocessing.**current_process**()

​	返回与当前进程相对应的 [`Process`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process" >}}) 对象。

​	和 [`threading.current_thread()`]({{< ref "/library/concurrency/threading#threading.current_thread" >}}) 相同。

## multiprocessing.**parent_process**()

​	返回父进程 [`Process`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process" >}}) 对象，和父进程调用 [`current_process()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.current_process" >}}) 返回的对象一样。如果一个进程已经是主进程， `parent_process` 会返回 `None`.

> Added in version 3.8.
>

## multiprocessing.**freeze_support**()

​	为使用了 [`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 的程序，提供冻结以产生 Windows 可执行文件的支持。(在 **py2exe**, **PyInstaller** 和 **cx_Freeze** 上测试通过)

​	需要在 main 模块的 `if __name__ == '__main__'` 该行之后马上调用该函数。例如：

```
from multiprocessing import Process, freeze_support

def f():
    print('hello world!')

if __name__ == '__main__':
    freeze_support()
    Process(target=f).start()
```

​	如果没有调用 `freeze_support()` 在尝试运行被冻结的可执行文件时会抛出 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}}) 异常。

​	对 `freeze_support()` 的调用在非 Windows 平台上是无效的。如果该模块在 Windows 平台的 Python 解释器中正常运行 (该程序没有被冻结)， 调用 `freeze_support()` 也是无效的。

## multiprocessing.**get_all_start_methods**()

​	返回由受支持的启动方法组成的列表，其中第一项将为默认值。 可用的启动方法有 `'fork'`, `'spawn'` 和 `'forkserver'`。 并非所有的平台都支持所有的方法。 参见 [上下文和启动方法]({{< ref "/library/concurrency/multiprocessing#multiprocessing-start-methods" >}})。

> Added in version 3.4.
>

## multiprocessing.**get_context**(*method=None*)

​	返回一个 Context 对象。该对象具有和 [`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 模块相同的API。

​	如果 *method* 为 `None` 则将返回默认的上下文。 否则 *method* 应为 `'fork'`, `'spawn'`, `'forkserver'`。 如果指定的启动方法不可用则将引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。 参见 [上下文和启动方法]({{< ref "/library/concurrency/multiprocessing#multiprocessing-start-methods" >}})。

> Added in version 3.4.
>

## multiprocessing.**get_start_method**(*allow_none=False*)

​	返回启动进程时使用的启动方法名。

​	如果启动方法已经固定，并且 *allow_none* 被设置成 False ，那么启动方法将被固定为默认的启动方法，并且返回其方法名。如果启动方法没有设定，并且 *allow_none* 被设置成 True ，那么将返回 `None` 。

​	返回值可以为 `'fork'`, `'spawn'`, `'forkserver'` 或 `None`。 参见 [上下文和启动方法]({{< ref "/library/concurrency/multiprocessing#multiprocessing-start-methods" >}})。

> Added in version 3.4.
>

> 在 3.8 版本发生变更: 对于 macOS，*spawn* 启动方式是默认方式。 因为 *fork* 可能导致subprocess崩溃，被认为是不安全的，查看 [bpo-33725](https://bugs.python.org/issue?@action=redirect&bpo=33725) 。

## multiprocessing.**set_executable**(*executable*)

​	设置在启动子进程时使用的 Python 解释器路径。 ( 默认使用 [`sys.executable`]({{< ref "/library/python/sys#sys.executable" >}}) ) 嵌入式编程人员可能需要这样做：

```
set_executable(os.path.join(sys.exec_prefix, 'pythonw.exe'))
```

​	以使他们可以创建子进程。

> 在 3.4 版本发生变更: 当使用 `'spawn'` 启动方法时在 POSIX 上受到支持。

> 在 3.11 版本发生变更: 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## multiprocessing.**set_forkserver_preload**(*module_names*)

​	为 forkserver 主进程设置一个可尝试导入的模块名称列表以使得它们已导入的状态被分叉进程所继承。 当执行操作时引发的任何 [`ImportError`]({{< ref "/library/exceptions#ImportError" >}}) 会被静默地忽略。 这可被用作一种性能增强措施以避免在每个进程中的重复操作。

​	要让此方法发挥作用，它必须在 forkserver 进程执行之前被调用（在创建 `Pool` 或启动 [`Process`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process" >}}) 之前）。

​	仅在使用 `'forkserver'` 启动方法时是有意义的。 参见 [上下文和启动方法]({{< ref "/library/concurrency/multiprocessing#multiprocessing-start-methods" >}})。

> Added in version 3.4.
>

## multiprocessing.**set_start_method**(*method*, *force=False*)

​	设置应当被用于启动子进程的方法。 *method* 方法可以为 `'fork'`, `'spawn'` 或 `'forkserver'`。 如果启动方法已经设置且 *force* 不为 `True` 则会引发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。 如果 *method* 为 `None` 而 *force* 为 `True` 则启动方法会被设为 `None`。 如果 *method* 为 `None` 而 *force* 为 `False` 则上下文会被设为默认的上下文。

​	注意这最多只能调用一次，并且需要藏在 main 模块中，由 `if __name__ == '__main__'` 保护着。

​	参见 [上下文和启动方法]({{< ref "/library/concurrency/multiprocessing#multiprocessing-start-methods" >}})。

> Added in version 3.4.
>

​备注
 

[`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 并没有包含类似 [`threading.active_count()`]({{< ref "/library/concurrency/threading#threading.active_count" >}}) , [`threading.enumerate()`]({{< ref "/library/concurrency/threading#threading.enumerate" >}}) , [`threading.settrace()`]({{< ref "/library/concurrency/threading#threading.settrace" >}}) , [`threading.setprofile()`]({{< ref "/library/concurrency/threading#threading.setprofile" >}}), [`threading.Timer`]({{< ref "/library/concurrency/threading#threading.Timer" >}}) , 或者 [`threading.local`]({{< ref "/library/concurrency/threading#threading.local" >}}) 的方法和类。

### 连接对象（Connection）

​	Connection 对象允许收发可以序列化的对象或字符串。它们可以看作面向消息的连接套接字。

​	通常使用 [`Pipe`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Pipe" >}}) 创建 Connection 对象。详见 ： [监听器及客户端]({{< ref "/library/concurrency/multiprocessing#multiprocessing-listeners-clients" >}}).

## *class* multiprocessing.connection.**Connection**

## **send**(*obj*)

​	将一个对象发送到连接的另一端，可以用 [`recv()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.connection.Connection.recv" >}}) 读取。

​	发送的对象必须是可以序列化的，过大的对象 ( 接近 32MiB+ ，这个值取决于操作系统 ) 有可能引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 异常。

## **recv**()

​	返回一个由另一端使用 [`send()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.connection.Connection.send" >}}) 发送的对象。该方法会一直阻塞直到接收到对象。 如果对端关闭了连接或者没有东西可接收，将抛出 [`EOFError`]({{< ref "/library/exceptions#EOFError" >}}) 异常。

## **fileno**()

​	返回由连接对象使用的描述符或者句柄。

## **close**()

​	关闭连接对象。

​	当连接对象被垃圾回收时会自动调用。

## **poll**([*timeout*])

​	返回连接对象中是否有可以读取的数据。

​	如果未指定 *timeout* ，此方法会马上返回。如果 *timeout* 是一个数字，则指定了最大阻塞的秒数。如果 *timeout* 是 `None`，那么将一直等待，不会超时。

​	注意通过使用 [`multiprocessing.connection.wait()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.connection.wait" >}}) 可以一次轮询多个连接对象。

## **send_bytes**(*buffer*[, *offset*[, *size*]])

​	从一个 [bytes-like object]({{< ref "/glossary/idx#term-bytes-like-object" >}}) 对象中取出字节数组并作为一条完整消息发送。

​	如果由 *offset* 给定了在 *buffer* 中读取数据的位置。 如果给定了 *size* ，那么将会从缓冲区中读取多个字节。 过大的缓冲区 ( 接近 32MiB+ ，此值依赖于操作系统 ) 有可能引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 异常。

## **recv_bytes**([*maxlength*])

​	以字符串形式返回一条从连接对象另一端发送过来的字节数据。此方法在接收到数据前将一直阻塞。 如果连接对象被对端关闭或者没有数据可读取，将抛出 [`EOFError`]({{< ref "/library/exceptions#EOFError" >}}) 异常。

​	如果给定了 *maxlength* 并且消息长于 *maxlength* 那么将抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 并且该连接对象将不再可读。

> 在 3.3 版本发生变更: 曾经该函数抛出 [`IOError`]({{< ref "/library/exceptions#IOError" >}}) ，现在这是 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 的别名。

## **recv_bytes_into**(*buffer*[, *offset*])

​	将一条完整的字节数据消息读入 *buffer* 中并返回消息的字节数。 此方法在接收到数据前将一直阻塞。 如果连接对象被对端关闭或者没有数据可读取，将抛出 [`EOFError`]({{< ref "/library/exceptions#EOFError" >}}) 异常。

*buffer* must be a writable [bytes-like object]({{< ref "/glossary/idx#term-bytes-like-object" >}}). If *offset* is given then the message will be written into the buffer from that position. Offset must be a non-negative integer less than the length of *buffer* (in bytes).

​	如果缓冲区太小，则将引发 `BufferTooShort` 异常，并且完整的消息将会存放在异常实例 `e` 的 `e.args[0]` 中。

> 在 3.3 版本发生变更: 现在连接对象自身可以通过 [`Connection.send()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.connection.Connection.send" >}}) 和 [`Connection.recv()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.connection.Connection.recv" >}}) 在进程之间传递。

​	连接对象现在支持上下文管理协议 -- 参见 [上下文管理器类型]({{< ref "/library/stdtypes#typecontextmanager" >}})。 [`__enter__()`]({{< ref "/library/stdtypes#contextmanager.__enter__" >}}) 返回连接对象，而 [`__exit__()`]({{< ref "/library/stdtypes#contextmanager.__exit__" >}}) 将调用 [`close()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.connection.Connection.close" >}})。

​	例如:



``` python
>>> from multiprocessing import Pipe
>>> a, b = Pipe()
>>> a.send([1, 'hello', None])
>>> b.recv()
[1, 'hello', None]
>>> b.send_bytes(b'thank you')
>>> a.recv_bytes()
b'thank you'
>>> import array
>>> arr1 = array.array('i', range(5))
>>> arr2 = array.array('i', [0] * 10)
>>> a.send_bytes(arr1)
>>> count = b.recv_bytes_into(arr2)
>>> assert count == len(arr1) * arr1.itemsize
>>> arr2
array('i', [0, 1, 2, 3, 4, 0, 0, 0, 0, 0])
```

​	警告

 

[`Connection.recv()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.connection.Connection.recv" >}}) 方法会自动解封它收到的数据，除非你能够信任发送消息的进程，否则此处可能有安全风险。

​	因此， 除非连接对象是由 `Pipe()` 产生的，否则你应该仅在使用了某种认证手段之后才使用 [`recv()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.connection.Connection.recv" >}}) 和 [`send()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.connection.Connection.send" >}}) 方法。 参考 [认证密码]({{< ref "/library/concurrency/multiprocessing#multiprocessing-auth-keys" >}})。

​	警告

 

​	如果一个进程在试图读写管道时被终止了，那么管道中的数据很可能是不完整的，因为此时可能无法确定消息的边界。

### 同步原语

​	通常来说同步原语在多进程环境中并不像它们在多线程环境中那么必要。参考 [`threading`]({{< ref "/library/concurrency/threading#module-threading" >}}) 模块的文档。

​	注意可以使用管理器对象创建同步原语，参考 [管理器]({{< ref "/library/concurrency/multiprocessing#multiprocessing-managers" >}}) 。

## *class* multiprocessing.**Barrier**(*parties*[, *action*[, *timeout*]])

​	类似 [`threading.Barrier`]({{< ref "/library/concurrency/threading#threading.Barrier" >}}) 的栅栏对象。

> Added in version 3.3.
>

## *class* multiprocessing.**BoundedSemaphore**([*value*])

​	非常类似 [`threading.BoundedSemaphore`]({{< ref "/library/concurrency/threading#threading.BoundedSemaphore" >}}) 的有界信号量对象。

​	一个小小的不同在于，它的 `acquire` 方法的第一个参数名是和 [`Lock.acquire()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Lock.acquire" >}}) 一样的 *block* 。

​备注
 

​	在 macOS 平台上， 该对象于 [`Semaphore`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Semaphore" >}}) 不同在于 `sem_getvalue()` 方法并没有在该平台上实现。

## *class* multiprocessing.**Condition**([*lock*])

​	条件变量： [`threading.Condition`]({{< ref "/library/concurrency/threading#threading.Condition" >}}) 的别名。

​	指定的 *lock* 参数应该是 [`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 模块中的 [`Lock`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Lock" >}}) 或者 [`RLock`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.RLock" >}}) 对象。

> 在 3.3 版本发生变更: 新增了 [`wait_for()`]({{< ref "/library/concurrency/threading#threading.Condition.wait_for" >}}) 方法。

## *class* multiprocessing.**Event**

​	A clone of [`threading.Event`]({{< ref "/library/concurrency/threading#threading.Event" >}}).

## *class* multiprocessing.**Lock**

​	原始锁（非递归锁）对象，类似于 [`threading.Lock`]({{< ref "/library/concurrency/threading#threading.Lock" >}}) 。一旦一个进程或者线程拿到了锁，后续的任何其他进程或线程的其他请求都会被阻塞直到锁被释放。任何进程或线程都可以释放锁。除非另有说明，否则 [`multiprocessing.Lock`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Lock" >}}) 用于进程或者线程的概念和行为都和 [`threading.Lock`]({{< ref "/library/concurrency/threading#threading.Lock" >}}) 一致。

​	注意 [`Lock`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Lock" >}}) 实际上是一个工厂函数。它返回由默认上下文初始化的 `multiprocessing.synchronize.Lock` 对象。

[`Lock`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Lock" >}}) supports the [context manager]({{< ref "/glossary/idx#term-context-manager" >}}) protocol and thus may be used in [`with`]({{< ref "/reference/compound_stmts#with" >}}) statements.

## **acquire**(*block=True*, *timeout=None*)

​	可以阻塞或非阻塞地获得锁。

​	如果 *block* 参数被设为 `True` ( 默认值 ) ， 对该方法的调用在锁处于释放状态之前都会阻塞，然后将锁设置为锁住状态并返回 `True` 。需要注意的是第一个参数名与 [`threading.Lock.acquire()`]({{< ref "/library/concurrency/threading#threading.Lock.acquire" >}}) 的不同。

​	如果 *block* 参数被设置成 `False` ，方法的调用将不会阻塞。 如果锁当前处于锁住状态，将返回 `False` ； 否则将锁设置成锁住状态，并返回 `True` 。

​	当 *timeout* 是一个正浮点数时，会在等待锁的过程中最多阻塞等待 *timeout* 秒，当 *timeout* 是负数时，效果和 *timeout* 为0时一样，当 *timeout* 是 `None` （默认值）时，等待时间是无限长。需要注意的是，对于 *timeout* 参数是负数和 `None` 的情况, 其行为与 [`threading.Lock.acquire()`]({{< ref "/library/concurrency/threading#threading.Lock.acquire" >}}) 是不一样的。当 *block* 参数 为 `False` 时， *timeout* 并没有实际用处，会直接忽略。否则，函数会在拿到锁后返回 `True` 或者 超时没拿到锁后返回 `False` 。

## **release**()

​	释放锁，可以在任何进程、线程使用，并不限于锁的拥有者。

​	当尝试释放一个没有被持有的锁时，会抛出 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 异常，除此之外其行为与 [`threading.Lock.release()`]({{< ref "/library/concurrency/threading#threading.Lock.release" >}}) 一样。

## *class* multiprocessing.**RLock**

​	递归锁对象: 类似于 [`threading.RLock`]({{< ref "/library/concurrency/threading#threading.RLock" >}}) 。递归锁必须由持有线程、进程亲自释放。如果某个进程或者线程拿到了递归锁，这个进程或者线程可以再次拿到这个锁而不需要等待。但是这个进程或者线程的拿锁操作和释放锁操作的次数必须相同。

​	注意 [`RLock`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.RLock" >}}) 是一个工厂函数，调用后返回一个使用默认 context 初始化的 `multiprocessing.synchronize.RLock` 实例。

[`RLock`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.RLock" >}}) 支持 [context manager]({{< ref "/glossary/idx#term-context-manager" >}}) 协议，因此可在 [`with`]({{< ref "/reference/compound_stmts#with" >}}) 语句内使用。

## **acquire**(*block=True*, *timeout=None*)

​	可以阻塞或非阻塞地获得锁。

​	当 *block* 参数设置为 `True` 时，会一直阻塞直到锁处于空闲状态（没有被任何进程、线程拥有），除非当前进程或线程已经拥有了这把锁。然后当前进程/线程会持有这把锁（在锁没有其他持有者的情况下），锁内的递归等级加一，并返回 `True` . 注意， 这个函数第一个参数的行为和 [`threading.RLock.acquire()`]({{< ref "/library/concurrency/threading#threading.RLock.acquire" >}}) 的实现有几个不同点，包括参数名本身。

​	当 *block* 参数是 `False` , 将不会阻塞，如果此时锁被其他进程或者线程持有，当前进程、线程获取锁操作失败，锁的递归等级也不会改变，函数返回 `False` , 如果当前锁已经处于释放状态，则当前进程、线程则会拿到锁，并且锁内的递归等级加一，函数返回 `True` 。

*timeout* 参数的使用方法及行为与 [`Lock.acquire()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Lock.acquire" >}}) 一样。但是要注意 *timeout* 的其中一些行为和 [`threading.RLock.acquire()`]({{< ref "/library/concurrency/threading#threading.RLock.acquire" >}}) 中实现的行为是不同的。

## **release**()

​	释放锁，使锁内的递归等级减一。如果释放后锁内的递归等级降低为0，则会重置锁的状态为释放状态（即没有被任何进程、线程持有），重置后如果有有其他进程和线程在等待这把锁，他们中的一个会获得这个锁而继续运行。如果释放后锁内的递归等级还没到达0，则这个锁仍将保持未释放状态且当前进程和线程仍然是持有者。

​	只有当前进程或线程是锁的持有者时，才允许调用这个方法。如果当前进程或线程不是这个锁的拥有者，或者这个锁处于已释放的状态(即没有任何拥有者)，调用这个方法会抛出 [`AssertionError`]({{< ref "/library/exceptions#AssertionError" >}}) 异常。注意这里抛出的异常类型和 [`threading.RLock.release()`]({{< ref "/library/concurrency/threading#threading.RLock.release" >}}) 中实现的行为不一样。

## *class* multiprocessing.**Semaphore**([*value*])

​	一种信号量对象: 类似于 [`threading.Semaphore`]({{< ref "/library/concurrency/threading#threading.Semaphore" >}}).

​	一个小小的不同在于，它的 `acquire` 方法的第一个参数名是和 [`Lock.acquire()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Lock.acquire" >}}) 一样的 *block* 。

​备注
 

​	在 macOS 上，不支持 `sem_timedwait` ，所以，调用 `acquire()` 时如果使用 timeout 参数，会通过循环sleep来模拟这个函数的行为。

​备注
 

​	这个包的某些功能依赖于宿主机系统的共享信号量的实现，如果系统没有这个特性， `multiprocessing.synchronize` 会被禁用，尝试导入这个模块会引发 [`ImportError`]({{< ref "/library/exceptions#ImportError" >}}) 异常，详细信息请查看 [bpo-3770](https://bugs.python.org/issue?@action=redirect&bpo=3770) 。

### 共享 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 对象

​	在共享内存上创建可被子进程继承的共享对象时是可行的。

## multiprocessing.**Value**(*typecode_or_type*, **args*, *lock=True*)

​	返回一个从共享内存上创建的 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 对象。默认情况下返回的对象实际上是经过了同步器包装过的。可以通过 [`Value`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Value" >}}) 的 *value* 属性访问这个对象本身。

*typecode_or_type* 指明了返回的对象类型: 它可能是一个 ctypes 类型或者 [`array`]({{< ref "/library/datatypes/array#module-array" >}}) 模块中每个类型对应的单字符长度的字符串。 **args* 会透传给这个类的构造函数。

​	如果 *lock* 参数是 `True` （默认值）, 将会新建一个递归锁用于同步对于此值的访问操作。 如果 *lock* 是 [`Lock`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Lock" >}}) 或者 [`RLock`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.RLock" >}}) 对象，那么这个传入的锁将会用于同步对这个值的访问操作，如果 *lock* 是 `False` , 那么对这个对象的访问将没有锁保护，也就是说这个变量不是进程安全的。

​	诸如 `+=` 这类的操作会引发独立的读操作和写操作，也就是说这类操作符并不具有原子性。所以，如果你想让递增共享变量的操作具有原子性，仅仅以这样的方式并不能达到要求:

```
counter.value += 1
```

​	共享对象内部关联的锁是递归锁(默认情况下就是)的情况下， 你可以采用这种方式

```
with counter.get_lock():
    counter.value += 1
```

​	注意 *lock* 只能是命名参数。

## multiprocessing.**Array**(*typecode_or_type*, *size_or_initializer*, ***, *lock=True*)

​	从共享内存中申请并返回一个具有ctypes类型的数组对象。默认情况下返回值实际上是被同步器包装过的数组对象。

*typecode_or_type* 指明了返回的数组中的元素类型: 它可能是一个 ctypes 类型或者 [`array`]({{< ref "/library/datatypes/array#module-array" >}}) 模块中每个类型对应的单字符长度的字符串。 如果 *size_or_initializer* 是一个整数，那就会当做数组的长度，并且整个数组的内存会初始化为0。否则，如果 *size_or_initializer* 会被当成一个序列用于初始化数组中的每一个元素，并且会根据元素个数自动判断数组的长度。

​	如果 *lock* 为 `True` (默认值) 则将创建一个新的锁对象用于同步对值的访问。 如果 *lock* 为一个 [`Lock`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Lock" >}}) 或 [`RLock`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.RLock" >}}) 对象则该对象将被用于同步对值的访问。 如果 *lock* 为 `False` 则对返回对象的访问将不会自动得到锁的保护，也就是说它不是“进程安全的”。

​	请注意 *lock* 是一个仅限关键字参数。

​	请注意 [`ctypes.c_char`]({{< ref "/library/allos/ctypes#ctypes.c_char" >}}) 的数组具有 *value* 和 *raw* 属性，允许被用来保存和提取字符串。



#### [`multiprocessing.sharedctypes`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing.sharedctypes" >}}) 模块

[`multiprocessing.sharedctypes`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing.sharedctypes" >}}) 模块提供了一些函数，用于分配来自共享内存的、可被子进程继承的 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 对象。

​备注
 

​	虽然可以将指针存储在共享内存中，但请记住它所引用的是特定进程地址空间中的位置。 而且，指针很可能在第二个进程的上下文中无效，尝试从第二个进程对指针进行解引用可能会导致崩溃。

## multiprocessing.sharedctypes.**RawArray**(*typecode_or_type*, *size_or_initializer*)

​	从共享内存中申请并返回一个 ctypes 数组。

*typecode_or_type* 指明了返回的数组中的元素类型: 它可能是一个 ctypes 类型或者 [`array`]({{< ref "/library/datatypes/array#module-array" >}}) 模块中使用的类型字符。 如果 *size_or_initializer* 是一个整数，那就会当做数组的长度，并且整个数组的内存会初始化为0。否则，如果 *size_or_initializer* 会被当成一个序列用于初始化数组中的每一个元素，并且会根据元素个数自动判断数组的长度。

​	注意对元素的访问、赋值操作可能是非原子操作 - 使用 [`Array()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.sharedctypes.Array" >}}) , 从而借助其中的锁保证操作的原子性。

## multiprocessing.sharedctypes.**RawValue**(*typecode_or_type*, **args*)

​	从共享内存中申请并返回一个 ctypes 对象。

*typecode_or_type* 指明了返回的对象类型: 它可能是一个 ctypes 类型或者 [`array`]({{< ref "/library/datatypes/array#module-array" >}}) 模块中每个类型对应的单字符长度的字符串。 **args* 会透传给这个类的构造函数。

​	注意对 value 的访问、赋值操作可能是非原子操作 - 使用 [`Value()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.sharedctypes.Value" >}}) ，从而借助其中的锁保证操作的原子性。

​	请注意 [`ctypes.c_char`]({{< ref "/library/allos/ctypes#ctypes.c_char" >}}) 的数组具有 *value* 和 *raw* 属性，允许被用来保存和提取字符串 - 请查看 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 文档。

## multiprocessing.sharedctypes.**Array**(*typecode_or_type*, *size_or_initializer*, ***, *lock=True*)

​	返回一个纯 ctypes 数组, 或者在此之上经过同步器包装过的进程安全的对象，这取决于 *lock* 参数的值，除此之外，和 [`RawArray()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.sharedctypes.RawArray" >}}) 一样。

​	如果 *lock* 为 `True` (默认值) 则将创建一个新的锁对象用于同步对值的访问。 如果 *lock* 为一个 [`Lock`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Lock" >}}) 或 [`RLock`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.RLock" >}}) 对象则该对象将被用于同步对值的访问。 如果 *lock* 为 `False` 则对所返回对象的访问将不会自动得到锁的保护，也就是说它将不是“进程安全的”。

​	注意 *lock* 只能是命名参数。

## multiprocessing.sharedctypes.**Value**(*typecode_or_type*, **args*, *lock=True*)

​	返回一个纯 ctypes 数组, 或者在此之上经过同步器包装过的进程安全的对象，这取决于 *lock* 参数的值，除此之外，和 `RawArray()` 一样。

​	如果 *lock* 为 `True` (默认值) 则将创建一个新的锁对象用于同步对值的访问。 如果 *lock* 为一个 [`Lock`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Lock" >}}) 或 [`RLock`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.RLock" >}}) 对象则该对象将被用于同步对值的访问。 如果 *lock* 为 `False` 则对所返回对象的访问将不会自动得到锁的保护，也就是说它将不是“进程安全的”。

​	注意 *lock* 只能是命名参数。

## multiprocessing.sharedctypes.**copy**(*obj*)

​	从共享内存中申请一片空间将 ctypes 对象 *obj* 过来，然后返回一个新的 ctypes 对象。

## multiprocessing.sharedctypes.**synchronized**(*obj*[, *lock*])

​	将一个 ctypes 对象包装为进程安全的对象并返回，使用 *lock* 同步对于它的操作。如果 *lock* 是 `None` (默认值) ，则会自动创建一个 [`multiprocessing.RLock`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.RLock" >}}) 对象。

​	同步器包装后的对象会在原有对象基础上额外增加两个方法: `get_obj()` 返回被包装的对象， `get_lock()` 返回内部用于同步的锁。

​	需要注意的是，访问包装后的ctypes对象会比直接访问原来的纯 ctypes 对象慢得多。

> 在 3.5 版本发生变更: 同步器包装后的对象支持 [context manager]({{< ref "/glossary/idx#term-context-manager" >}}) 协议。

​	下面的表格对比了创建普通ctypes对象和基于共享内存上创建共享ctypes对象的语法。（表格中的 `MyStruct` 是 [`ctypes.Structure`]({{< ref "/library/allos/ctypes#ctypes.Structure" >}}) 的子类）

| ctypes               | 使用类型的共享ctypes       | 使用 typecode 的共享 ctypes |
| :------------------- | :------------------------- | :-------------------------- |
| c_double(2.4)        | RawValue(c_double, 2.4)    | RawValue('d', 2.4)          |
| MyStruct(4, 6)       | RawValue(MyStruct, 4, 6)   |                             |
| (c_short * 7)()      | RawArray(c_short, 7)       | RawArray('h', 7)            |
| (c_int * 3)(9, 2, 8) | RawArray(c_int, (9, 2, 8)) | RawArray('i', (9, 2, 8))    |

​	下面是一个在子进程中修改多个ctypes对象的例子。

```
from multiprocessing import Process, Lock
from multiprocessing.sharedctypes import Value, Array
from ctypes import Structure, c_double

class Point(Structure):
    _fields_ = [('x', c_double), ('y', c_double)]

def modify(n, x, s, A):
    n.value **= 2
    x.value **= 2
    s.value = s.value.upper()
    for a in A:
        a.x **= 2
        a.y **= 2

if __name__ == '__main__':
    lock = Lock()

    n = Value('i', 7)
    x = Value(c_double, 1.0/3.0, lock=False)
    s = Array('c', b'hello world', lock=lock)
    A = Array(Point, [(1.875,-6.25), (-5.75,2.0), (2.375,9.5)], lock=lock)

    p = Process(target=modify, args=(n, x, s, A))
    p.start()
    p.join()

    print(n.value)
    print(x.value)
    print(s.value)
    print([(a.x, a.y) for a in A])
```

​	输出如下

```
49
0.1111111111111111
HELLO WORLD
[(3.515625, 39.0625), (33.0625, 4.0), (5.640625, 90.25)]
```



### 管理器

​	管理器提供了一种创建共享数据的方法，从而可以在不同进程中共享，甚至可以通过网络跨机器共享数据。管理器维护一个用于管理 *共享对象* 的服务。其他进程可以通过代理访问这些共享对象。

## multiprocessing.**Manager**()

​	返回一个已启动的 [`SyncManager`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.managers.SyncManager" >}}) 管理器对象，这个对象可以用于在不同进程中共享数据。返回的管理器对象对应了一个已经启动的子进程，并且拥有一系列方法可以用于创建共享对象、返回对应的代理。

​	当管理器被垃圾回收或者父进程退出时，管理器进程会立即退出。管理器类定义在 [`multiprocessing.managers`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing.managers" >}}) 模块:

## *class* multiprocessing.managers.**BaseManager**(*address=None*, *authkey=None*, *serializer='pickle'*, *ctx=None*, ***, *shutdown_timeout=1.0*)

​	创建一个 BaseManager 对象。

​	一旦创建，应该及时调用 [`start()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.managers.BaseManager.start" >}}) 或者 `get_server().serve_forever()` 以确保管理器对象对应的管理进程已经启动。

*address* 是管理器服务进程监听的地址。如果 *address* 是 `None` ,则允许和任意主机的请求建立连接。

*authkey* 是认证标识，用于检查连接服务进程的请求合法性。如果 *authkey* 是 `None`, 则会使用 `current_process().authkey` , 否则，就使用 *authkey* , 需要保证它必须是 byte 类型的字符串。

*serializer* 必须为 `'pickle'` (使用 [`pickle`]({{< ref "/library/persistence/pickle#module-pickle" >}}) 序列化) 或 `'xmlrpclib'` (使用 [`xmlrpc.client`]({{< ref "/library/internet/xmlrpc_client#module-xmlrpc.client" >}}) 序列化)。

*ctx* 是一个上下文对象，或者为 `None` (使用当前上下文)。 参见 `get_context()` 函数。

*shutdown_timeout* 是用于等待直到 [`shutdown()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.managers.BaseManager.shutdown" >}}) 方法中的管理器所使用的进程结束的超时秒数。 如果关闭超时，进程将被终结。 如果终结进程的操作也超时，进程将被杀掉。

> 在 3.11 版本发生变更: 添加了 *shutdown_timeout* 形参。

## **start**([*initializer*[, *initargs*]])

​	为管理器开启一个子进程，如果 *initializer* 不是 `None` , 子进程在启动时将会调用 `initializer(*initargs)` 。

## **get_server**()

​	返回一个 `Server` 对象，它是管理器在后台控制的真实的服务。 `Server` 对象拥有 `serve_forever()` 方法。



``` python
>>> from multiprocessing.managers import BaseManager
>>> manager = BaseManager(address=('', 50000), authkey=b'abc')
>>> server = manager.get_server()
>>> server.serve_forever()
```

`Server` 额外拥有一个 [`address`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.managers.BaseManager.address" >}}) 属性。

## **connect**()

​	将本地管理器对象连接到一个远程管理器进程:



``` python
>>> from multiprocessing.managers import BaseManager
>>> m = BaseManager(address=('127.0.0.1', 50000), authkey=b'abc')
>>> m.connect()
```

## **shutdown**()

​	停止管理器的进程。这个方法只能用于已经使用 [`start()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.managers.BaseManager.start" >}}) 启动的服务进程。

​	它可以被多次调用。

## **register**(*typeid*[, *callable*[, *proxytype*[, *exposed*[, *method_to_typeid*[, *create_method*]]]]])

​	一个 classmethod，可以将一个类型或者可调用对象注册到管理器类。

*typeid* 是一种 "类型标识符"，用于唯一表示某种共享对象类型，必须是一个字符串。

*callable* 是一个用来为此类型标识符创建对象的可调用对象。如果一个管理器实例将使用 [`connect()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.managers.BaseManager.connect" >}}) 方法连接到服务器，或者 *create_method* 参数为 `False`，那么这里可留下 `None`。

*proxytype* 是 [`BaseProxy`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.managers.BaseProxy" >}}) 的子类，可以根据 *typeid* 为共享对象创建一个代理，如果是 `None` , 则会自动创建一个代理类。

*exposed* 是一个函数名组成的序列，用来指明只有这些方法可以使用 [`BaseProxy._callmethod()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.managers.BaseProxy._callmethod" >}}) 代理。(如果 *exposed* 是 `None`, 则会在 `proxytype._exposed_` 存在的情况下转而使用它) 当暴露的方法列表没有指定的时候，共享对象的所有 “公共方法” 都会被代理。（这里的“公共方法”是指所有拥有 [`__call__()`]({{< ref "/reference/datamodel#object.__call__" >}}) 方法并且不是以 `'_'` 开头的属性）

*method_to_typeid* 是一个映射，用来指定那些应该返回代理对象的暴露方法所返回的类型。（如果 *method_to_typeid* 是 `None`, 则 `proxytype._method_to_typeid_` 会在存在的情况下被使用）如果方法名称不在这个映射中或者映射是 `None` ,则方法返回的对象会是一个值拷贝。

*create_method* 指明，是否要创建一个以 *typeid* 命名并返回一个代理对象的方法，这个函数会被服务进程用于创建共享对象，默认为 `True` 。

[`BaseManager`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.managers.BaseManager" >}}) 实例也有一个只读属性。

## **address**

​	管理器所用的地址。

> 在 3.3 版本发生变更: 管理器对象支持上下文管理协议 - 查看 [上下文管理器类型]({{< ref "/library/stdtypes#typecontextmanager" >}}) 。[`__enter__()`]({{< ref "/library/stdtypes#contextmanager.__enter__" >}}) 启动服务进程（如果它还没有启动）并且返回管理器对象， [`__exit__()`]({{< ref "/library/stdtypes#contextmanager.__exit__" >}}) 会调用 [`shutdown()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.managers.BaseManager.shutdown" >}}) 。

​	在之前的版本中，如果管理器服务进程没有启动， [`__enter__()`]({{< ref "/library/stdtypes#contextmanager.__enter__" >}}) 不会负责启动它。

## *class* multiprocessing.managers.**SyncManager**

[`BaseManager`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.managers.BaseManager" >}}) 的子类，可用于进程的同步。这个类型的对象使用 [`multiprocessing.Manager()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Manager" >}}) 创建。

​	它拥有一系列方法，可以为大部分常用数据类型创建并返回 [代理对象]({{< ref "/library/concurrency/multiprocessing#multiprocessing-proxy-objects" >}}) 代理，用于进程间同步。甚至包括共享列表和字典。

## **Barrier**(*parties*[, *action*[, *timeout*]])

​	创建一个共享的 [`threading.Barrier`]({{< ref "/library/concurrency/threading#threading.Barrier" >}}) 对象并返回它的代理。

> Added in version 3.3.
>

## **BoundedSemaphore**([*value*])

​	创建一个共享的 [`threading.BoundedSemaphore`]({{< ref "/library/concurrency/threading#threading.BoundedSemaphore" >}}) 对象并返回它的代理。

## **Condition**([*lock*])

​	创建一个共享的 [`threading.Condition`]({{< ref "/library/concurrency/threading#threading.Condition" >}}) 对象并返回它的代理。

​	如果提供了 *lock* 参数，那它必须是 [`threading.Lock`]({{< ref "/library/concurrency/threading#threading.Lock" >}}) 或 [`threading.RLock`]({{< ref "/library/concurrency/threading#threading.RLock" >}}) 的代理对象。

> 在 3.3 版本发生变更: 新增了 [`wait_for()`]({{< ref "/library/concurrency/threading#threading.Condition.wait_for" >}}) 方法。

## **Event**()

​	创建一个共享的 [`threading.Event`]({{< ref "/library/concurrency/threading#threading.Event" >}}) 对象并返回它的代理。

## **Lock**()

​	创建一个共享的 [`threading.Lock`]({{< ref "/library/concurrency/threading#threading.Lock" >}}) 对象并返回它的代理。

## **Namespace**()

​	创建一个共享的 [`Namespace`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.managers.Namespace" >}}) 对象并返回它的代理。

## **Queue**([*maxsize*])

​	创建一个共享的 [`queue.Queue`]({{< ref "/library/concurrency/queue#queue.Queue" >}}) 对象并返回它的代理。

## **RLock**()

​	创建一个共享的 [`threading.RLock`]({{< ref "/library/concurrency/threading#threading.RLock" >}}) 对象并返回它的代理。

## **Semaphore**([*value*])

​	创建一个共享的 [`threading.Semaphore`]({{< ref "/library/concurrency/threading#threading.Semaphore" >}}) 对象并返回它的代理。

## **Array**(*typecode*, *sequence*)

​	创建一个数组并返回它的代理。

## **Value**(*typecode*, *value*)

​	创建一个具有可写 `value` 属性的对象并返回它的代理。

## **dict**()

## **dict**(*mapping*)

## **dict**(*sequence*)

​	创建一个共享的 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 对象并返回它的代理。

## **list**()

## **list**(*sequence*)

​	创建一个共享的 [`list`]({{< ref "/library/stdtypes#list" >}}) 对象并返回它的代理。

> 在 3.6 版本发生变更: 共享对象能够嵌套。例如, 共享的容器对象如共享列表，可以包含另一个共享对象，他们全都会在 [`SyncManager`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.managers.SyncManager" >}}) 中进行管理和同步。

## *class* multiprocessing.managers.**Namespace**

​	一个可以注册到 [`SyncManager`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.managers.SyncManager" >}}) 的类型。

​	命名空间对象没有公共方法，但是拥有可写的属性。直接print会显示所有属性的值。

​	值得一提的是，当对命名空间对象使用代理的时候，访问所有名称以 `'_'` 开头的属性都只是代理器上的属性，而不是命名空间对象的属性。



``` python
>>> mp_context = multiprocessing.get_context('spawn')
>>> manager = mp_context.Manager()
>>> Global = manager.Namespace()
>>> Global.x = 10
>>> Global.y = 'hello'
>>> Global._z = 12.3    # 这是该代理的一个属性
>>> print(Global)
Namespace(x=10, y='hello')
```

#### 自定义管理器

​	要创建一个自定义的管理器，需要新建一个 [`BaseManager`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.managers.BaseManager" >}}) 的子类，然后使用这个管理器类上的 [`register()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.managers.BaseManager.register" >}}) 类方法将新类型或者可调用方法注册上去。例如:

```
from multiprocessing.managers import BaseManager

class MathsClass:
    def add(self, x, y):
        return x + y
    def mul(self, x, y):
        return x * y

class MyManager(BaseManager):
    pass

MyManager.register('Maths', MathsClass)

if __name__ == '__main__':
    with MyManager() as manager:
        maths = manager.Maths()
        print(maths.add(4, 3))         # 打印 7
        print(maths.mul(7, 8))         # 打印 56
```

#### 使用远程管理器

​	可以将管理器服务运行在一台机器上，然后使用客户端从其他机器上访问。(假设它们的防火墙允许)

​	运行下面的代码可以启动一个服务，此付包含了一个共享队列，允许远程客户端访问:



``` python
>>> from multiprocessing.managers import BaseManager
>>> from queue import Queue
>>> queue = Queue()
>>> class QueueManager(BaseManager): pass
>>> QueueManager.register('get_queue', callable=lambda:queue)
>>> m = QueueManager(address=('', 50000), authkey=b'abracadabra')
>>> s = m.get_server()
>>> s.serve_forever()
```

​	远程客户端可以通过下面的方式访问服务:



``` python
>>> from multiprocessing.managers import BaseManager
>>> class QueueManager(BaseManager): pass
>>> QueueManager.register('get_queue')
>>> m = QueueManager(address=('foo.bar.org', 50000), authkey=b'abracadabra')
>>> m.connect()
>>> queue = m.get_queue()
>>> queue.put('hello')
```

​	也可以通过下面的方式:



``` python
>>> from multiprocessing.managers import BaseManager
>>> class QueueManager(BaseManager): pass
>>> QueueManager.register('get_queue')
>>> m = QueueManager(address=('foo.bar.org', 50000), authkey=b'abracadabra')
>>> m.connect()
>>> queue = m.get_queue()
>>> queue.get()
'hello'
```

​	本地进程也可以访问这个队列，利用上面的客户端代码通过远程方式访问:



``` python
>>> from multiprocessing import Process, Queue
>>> from multiprocessing.managers import BaseManager
>>> class Worker(Process):
...     def __init__(self, q):
...         self.q = q
...         super().__init__()
...     def run(self):
...         self.q.put('local hello')
...
>>> queue = Queue()
>>> w = Worker(queue)
>>> w.start()
>>> class QueueManager(BaseManager): pass
...
>>> QueueManager.register('get_queue', callable=lambda: queue)
>>> m = QueueManager(address=('', 50000), authkey=b'abracadabra')
>>> s = m.get_server()
>>> s.serve_forever()
```



### 代理对象

​	代理是一个 *指向* 其他共享对象的对象，这个对象(很可能)在另外一个进程中。共享对象也可以说是代理 *指涉* 的对象。多个代理对象可能指向同一个指涉对象。

​	代理对象代理了指涉对象的一系列方法调用(虽然并不是指涉对象的每个方法都有必要被代理)。通过这种方式，代理的使用方法可以和它的指涉对象一样:



``` python
>>> mp_context = multiprocessing.get_context('spawn')
>>> manager = mp_context.Manager()
>>> l = manager.list([i*i for i in range(10)])
>>> print(l)
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
>>> print(repr(l))
<ListProxy object, typeid 'list' at 0x...>
>>> l[4]
16
>>> l[2:5]
[4, 9, 16]
```

​	注意，对代理使用 [`str()`]({{< ref "/library/stdtypes#str" >}}) 函数会返回指涉对象的字符串表示，但是 [`repr()`]({{< ref "/library/functions#repr" >}}) 却会返回代理本身的内部字符串表示。

​	被代理的对象很重要的一点是必须可以被序列化，这样才能允许他们在进程间传递。因此，指涉对象可以包含 [代理对象]({{< ref "/library/concurrency/multiprocessing#multiprocessing-proxy-objects" >}}) 。这允许管理器中列表、字典或者其他 [代理对象]({{< ref "/library/concurrency/multiprocessing#multiprocessing-proxy-objects" >}}) 对象之间的嵌套。



``` python
>>> a = manager.list()
>>> b = manager.list()
>>> a.append(b)         # referent of a now contains referent of b
>>> print(a, b)
[<ListProxy object, typeid 'list' at ...>] []
>>> b.append('hello')
>>> print(a[0], b)
['hello'] ['hello']
```

​	类似地，字典和列表代理也可以相互嵌套:



``` python
>>> l_outer = manager.list([ manager.dict() for i in range(2) ])
>>> d_first_inner = l_outer[0]
>>> d_first_inner['a'] = 1
>>> d_first_inner['b'] = 2
>>> l_outer[1]['c'] = 3
>>> l_outer[1]['z'] = 26
>>> print(l_outer[0])
{'a': 1, 'b': 2}
>>> print(l_outer[1])
{'c': 3, 'z': 26}
```

​	如果指涉对象包含了普通 [`list`]({{< ref "/library/stdtypes#list" >}}) 或 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 对象，对这些内部可变对象的修改不会通过管理器传播，因为代理无法得知被包含的值什么时候被修改了。但是把存放在容器代理中的值本身是会通过管理器传播的（会触发代理对象中的 `__setitem__` ）从而有效修改这些对象，所以可以把修改过的值重新赋值给容器代理:

```
# create a list proxy and append a mutable object (a dictionary)
lproxy = manager.list()
lproxy.append({})
# now mutate the dictionary
d = lproxy[0]
d['a'] = 1
d['b'] = 2
# at this point, the changes to d are not yet synced, but by
# updating the dictionary, the proxy is notified of the change
lproxy[0] = d
```

​	在大多是使用情形下，这种实现方式并不比嵌套 [代理对象]({{< ref "/library/concurrency/multiprocessing#multiprocessing-proxy-objects" >}}) 方便，但是依然演示了对于同步的一种控制级别。

​备注
 

[`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 中的代理类并没有提供任何对于代理值比较的支持。所以，我们会得到如下结果:



``` python
>>> manager.list([1,2,3]) == [1,2,3]
False
```

​	当需要比较值的时候，应该替换为使用指涉对象的拷贝。

## *class* multiprocessing.managers.**BaseProxy**

​	代理对象是 [`BaseProxy`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.managers.BaseProxy" >}}) 派生类的实例。

## **_callmethod**(*methodname*[, *args*[, *kwds*]])

​	调用指涉对象的方法并返回结果。

​	如果 `proxy` 是一个代理且其指涉的是 `obj` , 那么下面的表达式:

```
proxy._callmethod(methodname, args, kwds)
```

​	相当于求取以下表达式的值:

```
getattr(obj, methodname)(*args, **kwds)
```

​	于管理器进程。

​	返回结果会是一个值拷贝或者一个新的共享对象的代理 - 见函数 [`BaseManager.register()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.managers.BaseManager.register" >}}) 中关于参数 *method_to_typeid* 的文档。

​	如果这个调用熬出了异常，则这个异常会被 [`_callmethod()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.managers.BaseProxy._callmethod" >}}) 透传出来。如果是管理器进程本身抛出的一些其他异常，则会被 [`_callmethod()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.managers.BaseProxy._callmethod" >}}) 转换为 `RemoteError` 异常重新抛出。

​	特别注意，如果 *methodname* 没有 *暴露* 出来，将会引发一个异常。

[`_callmethod()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.managers.BaseProxy._callmethod" >}}) 的一个使用示例:



``` python
>>> l = manager.list(range(10))
>>> l._callmethod('__len__')
10
>>> l._callmethod('__getitem__', (slice(2, 7),)) # 等价于 l[2:7]
[2, 3, 4, 5, 6]
>>> l._callmethod('__getitem__', (20,))          # 等价于 l[20]
Traceback (most recent call last):
...
IndexError: list index out of range
```

## **_getvalue**()

​	返回指涉对象的一份拷贝。

​	如果指涉对象无法序列化，则会抛出一个异常。

## `__repr__`()

​	返回代理对象的内部字符串表示。

## `__str__`()

​	返回指涉对象的内部字符串表示。

#### 清理

​	代理对象使用了一个弱引用回调函数，当它被垃圾回收时，会将自己从拥有此指涉对象的管理器上反注册，

​	当共享对象没有被任何代理器引用时，会被管理器进程删除。



### 进程池

​	可以创建一个进程池，它将使用 [`Pool`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool" >}}) 类执行提交给它的任务。

## *class* multiprocessing.pool.**Pool**([*processes*[, *initializer*[, *initargs*[, *maxtasksperchild*[, *context*]]]]])

​	一个进程池对象，它控制可以提交作业的工作进程池。它支持带有超时和回调的异步结果，以及一个并行的 map 实现。

*processes* 是要使用的工作进程数量。 如果 *processes* 为 `None` 则使用 [`os.process_cpu_count()`]({{< ref "/library/allos/os#os.process_cpu_count" >}}) 所返回的数值。

​	如果 *initializer* 不为 `None`，则每个工作进程将会在启动时调用 `initializer(*initargs)`。

*maxtasksperchild* 是一个工作进程在它退出或被一个新的工作进程代替之前能完成的任务数量，为了释放未使用的资源。默认的 *maxtasksperchild* 是 `None`，意味着工作进程寿与池齐。

*context* 可被用于指定启动的工作进程的上下文。通常一个进程池是使用函数 `multiprocessing.Pool()` 或者一个上下文对象的 [`Pool()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool" >}}) 方法创建的。在这两种情况下， *context* 都是适当设置的。

​	注意，进程池对象的方法只有创建它的进程能够调用。

​	警告

 

[`multiprocessing.pool`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing.pool" >}}) 对象具有需要正确管理的内部资源 （像任何其他资源一样），具体方式是将进程池用作上下文管理器，或者手动调用 [`close()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.close" >}}) 和 [`terminate()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.terminate" >}})。 未做此类操作将导致进程在终结阶段挂起。

​	请注意依赖垃圾回收器来销毁进程池是 **不正确的** 做法，因为 CPython 并不保证进程池终结器会被调用（请参阅 [`object.__del__()`]({{< ref "/reference/datamodel#object.__del__" >}}) 来了解详情）。

> 在 3.2 版本发生变更: 增加了 *maxtasksperchild* 形参。

> 在 3.4 版本发生变更: 增加了 *context* 形参。

> 在 3.13 版本发生变更: 在默认情况下 *processes* 将使用 [`os.process_cpu_count()`]({{< ref "/library/allos/os#os.process_cpu_count" >}})，而不是 [`os.cpu_count()`]({{< ref "/library/allos/os#os.cpu_count" >}})。

​备注
 

​	通常来说，[`Pool`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool" >}}) 中的 Worker 进程的生命周期和进程池的工作队列一样长。一些其他系统中（如 Apache, mod_wsgi 等）也可以发现另一种模式，他们会让工作进程在完成一些任务后退出，清理、释放资源，然后启动一个新的进程代替旧的工作进程。 [`Pool`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool" >}}) 的 *maxtasksperchild* 参数给用户提供了这种能力。

## **apply**(*func*[, *args*[, *kwds*]])

​	使用 *args* 参数以及 *kwds* 命名参数调用 *func* , 它会返回结果前阻塞。这种情况下，[`apply_async()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.apply_async" >}}) 更适合并行化工作。另外 *func* 只会在一个进程池中的一个工作进程中执行。

## **apply_async**(*func*[, *args*[, *kwds*[, *callback*[, *error_callback*]]]])

[`apply()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.apply" >}}) 方法的一个变种，返回一个 [`AsyncResult`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.AsyncResult" >}}) 对象。

​	如果指定了 *callback* , 它必须是一个接受单个参数的可调用对象。当执行成功时， *callback* 会被用于处理执行后的返回结果，否则，调用 *error_callback* 。

​	如果指定了 *error_callback* , 它必须是一个接受单个参数的可调用对象。当目标函数执行失败时， 会将抛出的异常对象作为参数传递给 *error_callback* 执行。

​	回调函数应该立即执行完成，否则会阻塞负责处理结果的线程。

## **map**(*func*, *iterable*[, *chunksize*])

​	内置 [`map()`]({{< ref "/library/functions#map" >}}) 函数的并行版本 (但它只支持一个 *iterable* 参数，对于多个可迭代对象请参阅 [`starmap()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.starmap" >}}))。 它会保持阻塞直到获得结果。

​	这个方法会将可迭代对象分割为许多块，然后提交给进程池。 可以将 *chunksize* 设置为一个正整数来指定每个块的（近似）大小。

​	注意对于很长的迭代对象，可能消耗很多内存。可以考虑使用 [`imap()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.imap" >}}) 或 [`imap_unordered()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.imap_unordered" >}}) 并且显式指定 *chunksize* 以提升效率。

## **map_async**(*func*, *iterable*[, *chunksize*[, *callback*[, *error_callback*]]])

[`map()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.map" >}}) 方法的一个变种，返回一个 [`AsyncResult`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.AsyncResult" >}}) 对象。

​	如果指定了 *callback* , 它必须是一个接受单个参数的可调用对象。当执行成功时， *callback* 会被用于处理执行后的返回结果，否则，调用 *error_callback* 。

​	如果指定了 *error_callback* , 它必须是一个接受单个参数的可调用对象。当目标函数执行失败时， 会将抛出的异常对象作为参数传递给 *error_callback* 执行。

​	回调函数应该立即执行完成，否则会阻塞负责处理结果的线程。

## **imap**(*func*, *iterable*[, *chunksize*])

[`map()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.map" >}}) 的延迟执行版本。

*chunksize* 参数的作用和 [`map()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.map" >}}) 方法的一样。对于很长的迭代器，给 *chunksize* 设置一个很大的值会比默认值 `1` **极大** 地加快执行速度。

​	同样，如果 *chunksize* 是 `1` , 那么 [`imap()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.imap" >}}) 方法所返回的迭代器的 `next()` 方法拥有一个可选的 *timeout* 参数： 如果无法在 *timeout* 秒内执行得到结果，则 `next(timeout)` 会抛出 [`multiprocessing.TimeoutError`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.TimeoutError" >}}) 异常。

## **imap_unordered**(*func*, *iterable*[, *chunksize*])

​	和 [`imap()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.imap" >}}) 相同，只不过通过迭代器返回的结果是任意的。（当进程池中只有一个工作进程的时候，返回结果的顺序才能认为是"有序"的）

## **starmap**(*func*, *iterable*[, *chunksize*])

​	和 [`map()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.map" >}}) 类似，不过 *iterable* 中的每一项会被解包再作为函数参数。

​	比如可迭代对象 `[(1,2), (3, 4)]` 会转化为等价于 `[func(1,2), func(3,4)]` 的调用。

> Added in version 3.3.
>

## **starmap_async**(*func*, *iterable*[, *chunksize*[, *callback*[, *error_callback*]]])

​	相当于 [`starmap()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.starmap" >}}) 与 [`map_async()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.map_async" >}}) 的结合，迭代 *iterable* 的每一项，解包作为 *func* 的参数并执行，返回用于获取结果的对象。

> Added in version 3.3.
>

## **close**()

​	阻止后续任务提交到进程池，当所有任务执行完成后，工作进程会退出。

## **terminate**()

​	不必等待未完成的任务，立即停止工作进程。当进程池对象被垃圾回收时，会立即调用 [`terminate()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.terminate" >}})。

## **join**()

​	等待工作进程结束。调用 [`join()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.join" >}}) 前必须先调用 [`close()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.close" >}}) 或者 [`terminate()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.terminate" >}}) 。

> 在 3.3 版本发生变更: 进程池对象现在支持上下文管理器协议 - 参见 [上下文管理器类型]({{< ref "/library/stdtypes#typecontextmanager" >}}) 。[`__enter__()`]({{< ref "/library/stdtypes#contextmanager.__enter__" >}}) 返回进程池对象, [`__exit__()`]({{< ref "/library/stdtypes#contextmanager.__exit__" >}}) 会调用 [`terminate()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.terminate" >}}) 。

## *class* multiprocessing.pool.**AsyncResult**

[`Pool.apply_async()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.apply_async" >}}) 和 [`Pool.map_async()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.map_async" >}}) 返回对象所属的类。

## **get**([*timeout*])

​	用于获取执行结果。如果 *timeout* 不是 `None` 并且在 *timeout* 秒内仍然没有执行完得到结果，则抛出 [`multiprocessing.TimeoutError`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.TimeoutError" >}}) 异常。如果远程调用发生异常，这个异常会通过 [`get()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.AsyncResult.get" >}}) 重新抛出。

## **wait**([*timeout*])

​	阻塞，直到返回结果，或者 *timeout* 秒后超时。

## **ready**()

​	返回执行状态，是否已经完成。

## **successful**()

​	判断调用是否已经完成并且未引发异常。 如果还未获得结果则将引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

> 在 3.7 版本发生变更: 如果没有执行完，会抛出 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 异常而不是 [`AssertionError`]({{< ref "/library/exceptions#AssertionError" >}}) 。

​	下面的例子演示了进程池的用法:

```
from multiprocessing import Pool
import time

def f(x):
    return x*x

if __name__ == '__main__':
    with Pool(processes=4) as pool:         # start 4 worker processes
        result = pool.apply_async(f, (10,)) # evaluate "f(10)" asynchronously in a single process
        print(result.get(timeout=1))        # prints "100" unless your computer is *very* slow

        print(pool.map(f, range(10)))       # prints "[0, 1, 4,..., 81]"

        it = pool.imap(f, range(10))
        print(next(it))                     # prints "0"
        print(next(it))                     # prints "1"
        print(it.next(timeout=1))           # prints "4" unless your computer is *very* slow

        result = pool.apply_async(time.sleep, (10,))
        print(result.get(timeout=1))        # raises multiprocessing.TimeoutError
```



### 监听器及客户端

​	通常情况下，进程间通过队列或者 [`Pipe()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Pipe" >}}) 返回的 [`Connection`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.connection.Connection" >}}) 传递消息。

​	不过，[`multiprocessing.connection`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing.connection" >}}) 模块其实提供了一些更灵活的特性。最基础的用法是通过它抽象出来的高级API来操作socket或者Windows命名管道。也提供一些高级用法，如通过 [`hmac`]({{< ref "/library/crypto/hmac#module-hmac" >}}) 模块来支持 *摘要认证*，以及同时监听多个管道连接。

## multiprocessing.connection.**deliver_challenge**(*connection*, *authkey*)

​	发送一个随机生成的消息到另一端，并等待回复。

​	如果收到的回复与使用 *authkey* 作为键生成的信息摘要匹配成功，就会发送一个欢迎信息给管道另一端。否则抛出 [`AuthenticationError`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.AuthenticationError" >}}) 异常。

## multiprocessing.connection.**answer_challenge**(*connection*, *authkey*)

​	接收一条信息，使用 *authkey* 作为键计算信息摘要，然后将摘要发送回去。

​	如果没有收到欢迎消息，就抛出 [`AuthenticationError`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.AuthenticationError" >}}) 异常。

## multiprocessing.connection.**Client**(*address*[, *family*[, *authkey*]])

​	尝试使用 *address* 地址上的监听器建立一个连接，返回 [`Connection`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.connection.Connection" >}}) 。

​	连接的类型取决于 *family* 参数，但是通常可以省略，因为可以通过 *address* 的格式推导出来。(查看 [地址格式]({{< ref "/library/concurrency/multiprocessing#multiprocessing-address-formats" >}}) )

​	如果给出了 *authkey* 并且不为 `None`，则它应为一个字节串并且会被用作基于 HMAC 认证的密钥。 如果 *authkey* 为 `None` 则不会执行认证。 如果认证失败则会引发 [`AuthenticationError`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.AuthenticationError" >}})。 参见 [认证密码]({{< ref "/library/concurrency/multiprocessing#multiprocessing-auth-keys" >}})。

## *class* multiprocessing.connection.**Listener**([*address*[, *family*[, *backlog*[, *authkey*]]]])

​	可以监听连接请求，是对于绑定套接字或者 Windows 命名管道的封装。

*address* 是监听器对象中的绑定套接字或命名管道使用的地址。

​备注
 

​	如果使用 '0.0.0.0' 作为监听地址，那么在Windows上这个地址无法建立连接。想要建立一个可连接的端点，应该使用 '127.0.0.1' 。

*family* 是套接字(或者命名管道)使用的类型。它可以是以下一种: `'AF_INET'` ( TCP 套接字类型), `'AF_UNIX'` ( Unix 域套接字) 或者 `'AF_PIPE'` ( Windows 命名管道)。其中只有第一个保证各平台可用。如果 *family* 是 `None` ,那么 family 会根据 *address* 的格式自动推导出来。如果 *address* 也是 `None` , 则取默认值。默认值为可用类型中速度最快的。见 [地址格式]({{< ref "/library/concurrency/multiprocessing#multiprocessing-address-formats" >}}) 。注意，如果 *family* 是 `'AF_UNIX'` 而address是 `None` ,套接字会在一个 [`tempfile.mkstemp()`]({{< ref "/library/filesys/tempfile#tempfile.mkstemp" >}}) 创建的私有临时目录中创建。

​	如果监听器对象使用了套接字，*backlog* (默认值为1) 会在套接字绑定后传递给它的 [`listen()`]({{< ref "/library/ipc/socket#socket.socket.listen" >}}) 方法。

​	如果给出了 *authkey* 并且不为 `None`，则它应为一个字节串并且会被用作基于 HMAC 认证的密钥。 如果 *authkey* 为 `None` 则不会执行认证。 如果认证失败则会引发 [`AuthenticationError`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.AuthenticationError" >}})。 参见 [认证密码]({{< ref "/library/concurrency/multiprocessing#multiprocessing-auth-keys" >}})。

## **accept**()

​	接受一个连接并返回一个 [`Connection`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.connection.Connection" >}}) 对象，其连接到的监听器对象已绑定套接字或者命名管道。如果已经尝试过认证并且失败了，则会抛出 [`AuthenticationError`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.AuthenticationError" >}}) 异常。

## **close**()

​	关闭监听器对象上的绑定套接字或者命名管道。此函数会在监听器被垃圾回收后自动调用。不过仍然建议显式调用函数关闭。

​	监听器对象拥有下列只读属性:

## **address**

​	监听器对象使用的地址。

## **last_accepted**

​	最后一个连接所使用的地址。如果没有的话就是 `None` 。

> 在 3.3 版本发生变更: 监听器对象现在支持了上下文管理协议 - 见 [上下文管理器类型]({{< ref "/library/stdtypes#typecontextmanager" >}}) 。 [`__enter__()`]({{< ref "/library/stdtypes#contextmanager.__enter__" >}}) 返回一个监听器对象, [`__exit__()`]({{< ref "/library/stdtypes#contextmanager.__exit__" >}}) 会调用 [`close()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.connection.Listener.close" >}}) 。

## multiprocessing.connection.**wait**(*object_list*, *timeout=None*)

​	一直等待直到 *object_list* 中某个对象处于就绪状态。返回 *object_list* 中处于就绪状态的对象。如果 *timeout* 是一个浮点型，该方法会最多阻塞这么多秒。如果 *timeout* 是 `None` ，则会允许阻塞的事件没有限制。timeout为负数的情况下和为0的情况相同。

​	对于 POSIX 和 Windows，满足下列条件的对象可以出现在 *object_list* 中

- 可读的 [`Connection`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.connection.Connection" >}}) 对象；
- 一个已连接并且可读的 [`socket.socket`]({{< ref "/library/ipc/socket#socket.socket" >}}) 对象；或者
- [`Process`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process" >}}) 对象中的 [`sentinel`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.sentinel" >}}) 属性。

​	当一个连接或者套接字对象拥有有效的数据可被读取的时候，或者另一端关闭后，这个对象就处于就绪状态。

**POSIX**: `wait(object_list, timeout)` 和 `select.select(object_list, [], [], timeout)` 几乎相同。 差别在于，如果 [`select.select()`]({{< ref "/library/ipc/select#select.select" >}}) 被信号中断，它会引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 并附带错误号 `EINTR`，而 [`wait()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.connection.wait" >}}) 则不会。

**Windows**: *object_list* 中的条目必须是一个可等待的整数句柄 (根据 Win32 函数 `WaitForMultipleObjects()` 文档所使用的定义) 或者一个具有 [`fileno()`]({{< ref "/library/allos/io#io.IOBase.fileno" >}}) 方法的对象，该方法返回一个套接字句柄或管道句柄。 （注意管道句柄和套接字句柄 **不是** 可等待的句柄。）

> Added in version 3.3.
>

**示例**

​	下面的服务代码创建了一个使用 `'secret password'` 作为认证密码的监听器。它会等待连接然后发送一些数据给客户端:

```
from multiprocessing.connection import Listener
from array import array

address = ('localhost', 6000)     # family is deduced to be 'AF_INET'

with Listener(address, authkey=b'secret password') as listener:
    with listener.accept() as conn:
        print('connection accepted from', listener.last_accepted)

        conn.send([2.25, None, 'junk', float])

        conn.send_bytes(b'hello')

        conn.send_bytes(array('i', [42, 1729]))
```

​	下面的代码连接到服务然后从服务器上j接收一些数据:

```
from multiprocessing.connection import Client
from array import array

address = ('localhost', 6000)

with Client(address, authkey=b'secret password') as conn:
    print(conn.recv())                  # => [2.25, None, 'junk', float]

    print(conn.recv_bytes())            # => 'hello'

    arr = array('i', [0, 0, 0, 0, 0])
    print(conn.recv_bytes_into(arr))    # => 8
    print(arr)                          # => array('i', [42, 1729, 0, 0, 0])
```

​	下面的代码使用了 [`wait()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.connection.wait" >}}) ，以便在同时等待多个进程发来消息。

```
from multiprocessing import Process, Pipe, current_process
from multiprocessing.connection import wait

def foo(w):
    for i in range(10):
        w.send((i, current_process().name))
    w.close()

if __name__ == '__main__':
    readers = []

    for i in range(4):
        r, w = Pipe(duplex=False)
        readers.append(r)
        p = Process(target=foo, args=(w,))
        p.start()
        # We close the writable end of the pipe now to be sure that
        # p is the only process which owns a handle for it.  This
        # ensures that when p closes its handle for the writable end,
        # wait() will promptly report the readable end as being ready.
        w.close()

    while readers:
        for r in wait(readers):
            try:
                msg = r.recv()
            except EOFError:
                readers.remove(r)
            else:
                print(msg)
```



#### 地址格式

- `'AF_INET'` 地址是 `(hostname, port)` 形式的元组类型，其中 *hostname* 是一个字符串，*port* 是整数。
- `'AF_UNIX'` 地址是文件系统上文件名的字符串。
- `'AF_PIPE'` 地址是一个 `r'\\.\pipe\*PipeName*'` 形式的字符串。 要使用 [`Client()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.connection.Client" >}}) 来连接到远程计算机上一个名为 *ServerName* 的命名管道则应当改用 `r'\\*ServerName*\pipe\*PipeName*'` 形式的地址。

​	注意，使用两个反斜线开头的字符串默认被当做 `'AF_PIPE'` 地址而不是 `'AF_UNIX'` 地址。



### 认证密码

​	当使用 [`Connection.recv`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.connection.Connection.recv" >}}) 接收数据时，数据会自动被反序列化。不幸的是，对于一个不可信的数据源发来的数据，反序列化是存在安全风险的。所以 [`Listener`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.connection.Listener" >}}) 和 [`Client()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.connection.Client" >}}) 之间使用 [`hmac`]({{< ref "/library/crypto/hmac#module-hmac" >}}) 模块进行摘要认证。

​	认证密钥是一个 byte 类型的字符串，可以认为是和密码一样的东西，连接建立好后，双方都会要求另一方证明知道认证密钥。（这个证明过程不会通过连接发送密钥）

​	如果要求认证但是没有指定认证密钥，则会使用 `current_process().authkey` 的返回值 (参见 [`Process`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process" >}}))。 这个值将被当前进程所创建的任何 [`Process`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process" >}}) 对象自动继承。 这意味着 (在默认情况下) 一个包含多进程的程序中的所有进程会在相互间建立连接的时候共享单个认证密钥。

[`os.urandom()`]({{< ref "/library/allos/os#os.urandom" >}}) 也可以用来生成合适的认证密钥。

### 日志记录

​	当前模块也提供了一些对 logging 的支持。注意， [`logging`]({{< ref "/library/allos/logging#module-logging" >}}) 模块本身并没有使用进程间共享的锁，所以来自于多个进程的日志可能（具体取决于使用的日志 handler 类型）相互覆盖或者混杂。

## multiprocessing.**get_logger**()

​	返回 [`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 使用的 logger，必要的话会创建一个新的。

​	当首次创建时日志记录器级别为 [`logging.NOTSET`]({{< ref "/library/allos/logging#logging.NOTSET" >}}) 并且没有默认处理器。 发送到这个日志记录器的消息默认将不会传播到根日志记录器。

​	注意在 Windows 上，子进程只会继承父进程 logger 的日志级别 - 对于logger的其他自定义项不会继承。

## multiprocessing.**log_to_stderr**(*level=None*)

​	此函数会调用 [`get_logger()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.get_logger" >}}) 但是会在返回的 logger 上增加一个 handler，将所有输出都使用 `'[%(levelname)s/%(processName)s] %(message)s'` 的格式发送到 [`sys.stderr`]({{< ref "/library/python/sys#sys.stderr" >}}) 。你可以通过传递一个 `level` 参数来修改记录器的 `levelname` 。

​	下面是一个在交互式解释器中打开日志功能的例子:



``` python
>>> import multiprocessing, logging
>>> logger = multiprocessing.log_to_stderr()
>>> logger.setLevel(logging.INFO)
>>> logger.warning('doomed')
[WARNING/MainProcess] doomed
>>> m = multiprocessing.Manager()
[INFO/SyncManager-...] child process calling self.run()
[INFO/SyncManager-...] created temp directory /.../pymp-...
[INFO/SyncManager-...] manager serving at '/.../listener-...'
>>> del m
[INFO/MainProcess] sending shutdown message to manager
[INFO/SyncManager-...] manager exiting with exitcode 0
```

​	要查看日志等级的完整列表，见 [`logging`]({{< ref "/library/allos/logging#module-logging" >}}) 模块。



### [`multiprocessing.dummy`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing.dummy" >}}) 模块

[`multiprocessing.dummy`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing.dummy" >}}) 复制了 [`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 的 API，不过是在 [`threading`]({{< ref "/library/concurrency/threading#module-threading" >}}) 模块之上包装了一层。

​	特别地，[`multiprocessing.dummy`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing.dummy" >}}) 所提供的 `Pool` 函数会返回一个 [`ThreadPool`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.ThreadPool" >}}) 的实例，该类是 [`Pool`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool" >}}) 的子类，它支持所有相同的方法调用但会使用一个工作线程池而非工作进程池。

## *class* multiprocessing.pool.**ThreadPool**([*processes*[, *initializer*[, *initargs*]]])

​	一个线程池对象，用来控制可向其提交任务的工作线程池。 [`ThreadPool`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.ThreadPool" >}}) 实例与 [`Pool`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool" >}}) 实例是完全接口兼容的，并且它们的资源也必须被正确地管理，或者是将线程池作为上下文管理器来使用，或者是通过手动调用 [`close()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.close" >}}) 和 [`terminate()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool.terminate" >}})。

*processes* 是要使用的工作线程数量。 如果 *processes* 为 `None` 则使用 [`os.process_cpu_count()`]({{< ref "/library/allos/os#os.process_cpu_count" >}}) 所返回的数值。

​	如果 *initializer* 不为 `None`，则每个工作进程将会在启动时调用 `initializer(*initargs)`。

​	不同于 [`Pool`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool" >}})，*maxtasksperchild* 和 *context* 不可被提供。

​备注
 

[`ThreadPool`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.ThreadPool" >}}) 具有与 [`Pool`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool" >}}) 相同的接口，它围绕一个进程池进行设计并且先于 [`concurrent.futures`]({{< ref "/library/concurrency/future/concurrent_futures#module-concurrent.futures" >}}) 模块的引入。 因此，它继承了一些对于基于线程的池来说没有意义的操作，并且它具有自己的用于表示异步任务状态的类型 [`AsyncResult`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.AsyncResult" >}})，该类型不为任何其他库所知。

​	用户通常应该倾向于使用 [`concurrent.futures.ThreadPoolExecutor`]({{< ref "/library/concurrency/future/concurrent_futures#concurrent.futures.ThreadPoolExecutor" >}})，它拥有从一开始就围绕线程进行设计的更简单接口，并且返回与许多其他库相兼容的 [`concurrent.futures.Future`]({{< ref "/library/concurrency/future/concurrent_futures#concurrent.futures.Future" >}}) 实例，包括 [`asyncio`]({{< ref "/library/ipc/asyncio#module-asyncio" >}}) 库。



## 编程指导

​	使用 [`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 时，应遵循一些指导原则和习惯用法。

### 所有start方法

​	下面这些适用于所有start方法。

​	避免共享状态

> ​	应该尽可能避免在进程间传递大量数据，越少越好。
>
> ​	最好坚持使用队列或者管道进行进程间通信，而不是底层的同步原语。

​	可序列化

> ​	保证所代理的方法的参数是可以序列化的。

​	代理的线程安全性

> ​	不要在多线程中同时使用一个代理对象，除非你用锁保护它。
>
> ​	（而在不同进程中使用 *相同* 的代理对象却没有问题。）

​	使用 Join 避免僵尸进程

> ​	在 POSIX 上当一个进程结束但没有被合并则它将变成僵尸进程。 这样的进程应该不会很多因为每次启动新进程（或 [`active_children()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.active_children" >}}) 被调用）时所有尚未被合并的已完成进程都将被合并。 而且调用一个已结束进程的 [`Process.is_alive`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.is_alive" >}}) 也会合并这个进程。 虽然如此但显式地合并你所启动的所有进程仍然是个好习惯。

​	继承优于序列化、反序列化

> ​	当使用 *spawn* 或者 *forkserver* 的启动方式时，[`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 中的许多类型都必须是可序列化的，这样子进程才能使用它们。但是通常我们都应该避免使用管道和队列发送共享对象到另外一个进程，而是重新组织代码，对于其他进程创建出来的共享对象，让那些需要访问这些对象的子进程可以直接将这些对象从父进程继承过来。

​	避免杀死进程

> ​	通过 [`Process.terminate`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.terminate" >}}) 停止一个进程很容易导致这个进程正在使用的共享资源（如锁、信号量、管道和队列）损坏或者变得不可用，无法在其他进程中继续使用。
>
> ​	所以，最好只对那些从来不使用共享资源的进程调用 [`Process.terminate`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.terminate" >}}) 。

​	Join 使用队列的进程

> ​	记住，往队列放入数据的进程会一直等待直到队列中所有项被"feeder" 线程传给底层管道。（子进程可以调用队列的 [`Queue.cancel_join_thread`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Queue.cancel_join_thread" >}}) 方法禁止这种行为）
>
> ​	这意味着，任何使用队列的时候，你都要确保在进程join之前，所有存放到队列中的项将会被其他进程、线程完全消费。否则不能保证这个写过队列的进程可以正常终止。记住非精灵进程会自动 join 。
>
> ​	下面是一个会导致死锁的例子:
>
> ```
> from multiprocessing import Process, Queue
> 
> def f(q):
>     q.put('X' * 1000000)
> 
> if __name__ == '__main__':
>     queue = Queue()
>     p = Process(target=f, args=(queue,))
>     p.start()
>     p.join()                    # 这将死锁
>     obj = queue.get()
> ```
>
> ​	交换最后两行可以修复这个问题（或者直接删掉 `p.join()`）。

​	显式传递资源给子进程

> ​	在 POSIX 上使用 *fork* 启动方法，子进程将能够访问使用全局资源在父进程中创建的共享资源。 但是，更好的做法是将对象作为子进程构造器的参数来传入。
>
> ​	除了（部分原因）让代码兼容 Windows 以及其他的进程启动方式外，这种形式还保证了在子进程生命期这个对象是不会被父进程垃圾回收的。如果父进程中的某些对象被垃圾回收会导致资源释放，这就变得很重要。
>
> ​	所以对于实例：
>
> ```
> from multiprocessing import Process, Lock
> 
> def f():
>     ... 使用锁进行一些操作 ...
> 
> if __name__ == '__main__':
>     lock = Lock()
>     for i in range(10):
>         Process(target=f).start()
> ```
>
> ​	应当重写成这样：
>
> ```
> from multiprocessing import Process, Lock
> 
> def f(l):
>     ... 使用 "l" 进行一些操作 ...
> 
> if __name__ == '__main__':
>     lock = Lock()
>     for i in range(10):
>         Process(target=f, args=(lock,)).start()
> ```

​	谨防将 [`sys.stdin`]({{< ref "/library/python/sys#sys.stdin" >}}) 数据替换为 “类似文件的对象”

> [`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 原本会无条件地这样调用:
>
> ```
> os.close(sys.stdin.fileno())
> ```
>
> ​	在 `multiprocessing.Process._bootstrap()` 方法中 —— 这会导致与"进程中的进程"相关的一些问题。这已经被修改成了:
>
> ```
> sys.stdin.close()
> sys.stdin = open(os.open(os.devnull, os.O_RDONLY), closefd=False)
> ```
>
> ​	Which solves the fundamental issue of processes colliding with each other resulting in a bad file descriptor error, but introduces a potential danger to applications which replace [`sys.stdin()`]({{< ref "/library/python/sys#sys.stdin" >}}) with a "file-like object" with output buffering. This danger is that if multiple processes call [`close()`]({{< ref "/library/allos/io#io.IOBase.close" >}}) on this file-like object, it could result in the same data being flushed to the object multiple times, resulting in corruption.
>
> ​	如果你写入文件型对象并实现了自己的缓存，可以在每次追加缓存数据时记录当前进程id，从而将其变成 fork 安全的，当发现进程id变化后舍弃之前的缓存，例如:
>
> ```
> @property
> def cache(self):
>     pid = os.getpid()
>     if pid != self._pid:
>         self._pid = pid
>         self._cache = []
>     return self._cache
> ```
>
> ​	需要更多信息，请查看 [bpo-5155](https://bugs.python.org/issue?@action=redirect&bpo=5155), [bpo-5313](https://bugs.python.org/issue?@action=redirect&bpo=5313) 以及 [bpo-5331](https://bugs.python.org/issue?@action=redirect&bpo=5331)

### *spawn* 和 *forkserver* 启动方式

​	还有一些没有被应用到 *fork* 启动方法的额外限制。

​	更依赖序列化

> `Process.__init__()` 的所有参数都必须可序列化。同样的，当你继承 [`Process`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process" >}}) 时，需要保证当调用 [`Process.start`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.start" >}}) 方法时，实例可以被序列化。

​	全局变量

> ​	记住，如果子进程中的代码尝试访问一个全局变量，它所看到的值（如果有）可能和父进程中执行 [`Process.start`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.Process.start" >}}) 那一刻的值不一样。
>
> ​	当全局变量只是模块级别的常量时，是不会有问题的。

​	安全导入主模块

> ​	确保新的 Python 解释器可以安全地导入主模块，而不会导致意想不到的副作用（如启动新进程）。
>
> ​	例如，使用 *spawn* 或 *forkserver* 启动方式执行下面的模块，会引发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}}) 异常而失败。
>
> ```
> from multiprocessing import Process
> 
> def foo():
>     print('hello')
> 
> p = Process(target=foo)
> p.start()
> ```
>
> ​	应该通过下面的方法使用 `if __name__ == '__main__':` ，从而保护程序"入口点":
>
> ```
> from multiprocessing import Process, freeze_support, set_start_method
> 
> def foo():
>     print('hello')
> 
> if __name__ == '__main__':
>     freeze_support()
>     set_start_method('spawn')
>     p = Process(target=foo)
>     p.start()
> ```
>
> ​	（如果程序将正常运行而不是冻结，则可以省略 `freeze_support()` 行）
>
> ​	这允许新启动的 Python 解释器安全导入模块然后运行模块中的 `foo()` 函数。
>
> ​	如果主模块中创建了进程池或者管理器，这个规则也适用。



## 例子

​	创建和使用自定义管理器、代理的示例:

```
from multiprocessing import freeze_support
from multiprocessing.managers import BaseManager, BaseProxy
import operator

##

class Foo:
    def f(self):
        print('you called Foo.f()')
    def g(self):
        print('you called Foo.g()')
    def _h(self):
        print('you called Foo._h()')

# A simple generator function
def baz():
    for i in range(10):
        yield i*i

# Proxy type for generator objects
class GeneratorProxy(BaseProxy):
    _exposed_ = ['__next__']
    def __iter__(self):
        return self
    def __next__(self):
        return self._callmethod('__next__')

# Function to return the operator module
def get_operator_module():
    return operator

##

class MyManager(BaseManager):
    pass

# register the Foo class; make `f()` and `g()` accessible via proxy
MyManager.register('Foo1', Foo)

# register the Foo class; make `g()` and `_h()` accessible via proxy
MyManager.register('Foo2', Foo, exposed=('g', '_h'))

# register the generator function baz; use `GeneratorProxy` to make proxies
MyManager.register('baz', baz, proxytype=GeneratorProxy)

# register get_operator_module(); make public functions accessible via proxy
MyManager.register('operator', get_operator_module)

##

def test():
    manager = MyManager()
    manager.start()

    print('-' * 20)

    f1 = manager.Foo1()
    f1.f()
    f1.g()
    assert not hasattr(f1, '_h')
    assert sorted(f1._exposed_) == sorted(['f', 'g'])

    print('-' * 20)

    f2 = manager.Foo2()
    f2.g()
    f2._h()
    assert not hasattr(f2, 'f')
    assert sorted(f2._exposed_) == sorted(['g', '_h'])

    print('-' * 20)

    it = manager.baz()
    for i in it:
        print('<%d>' % i, end=' ')
    print()

    print('-' * 20)

    op = manager.operator()
    print('op.add(23, 45) =', op.add(23, 45))
    print('op.pow(2, 94) =', op.pow(2, 94))
    print('op._exposed_ =', op._exposed_)

##

if __name__ == '__main__':
    freeze_support()
    test()
```

​	使用 [`Pool`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.pool.Pool" >}}):

```
import multiprocessing
import time
import random
import sys

#
# Functions used by test code
#

def calculate(func, args):
    result = func(*args)
    return '%s says that %s%s = %s' % (
        multiprocessing.current_process().name,
        func.__name__, args, result
        )

def calculatestar(args):
    return calculate(*args)

def mul(a, b):
    time.sleep(0.5 * random.random())
    return a * b

def plus(a, b):
    time.sleep(0.5 * random.random())
    return a + b

def f(x):
    return 1.0 / (x - 5.0)

def pow3(x):
    return x ** 3

def noop(x):
    pass

#
# Test code
#

def test():
    PROCESSES = 4
    print('Creating pool with %d processes\n' % PROCESSES)

    with multiprocessing.Pool(PROCESSES) as pool:
        #
        # Tests
        #

        TASKS = [(mul, (i, 7)) for i in range(10)] + \
                [(plus, (i, 8)) for i in range(10)]

        results = [pool.apply_async(calculate, t) for t in TASKS]
        imap_it = pool.imap(calculatestar, TASKS)
        imap_unordered_it = pool.imap_unordered(calculatestar, TASKS)

        print('Ordered results using pool.apply_async():')
        for r in results:
            print('\t', r.get())
        print()

        print('Ordered results using pool.imap():')
        for x in imap_it:
            print('\t', x)
        print()

        print('Unordered results using pool.imap_unordered():')
        for x in imap_unordered_it:
            print('\t', x)
        print()

        print('Ordered results using pool.map() --- will block till complete:')
        for x in pool.map(calculatestar, TASKS):
            print('\t', x)
        print()

        #
        # Test error handling
        #

        print('Testing error handling:')

        try:
            print(pool.apply(f, (5,)))
        except ZeroDivisionError:
            print('\tGot ZeroDivisionError as expected from pool.apply()')
        else:
            raise AssertionError('expected ZeroDivisionError')

        try:
            print(pool.map(f, list(range(10))))
        except ZeroDivisionError:
            print('\tGot ZeroDivisionError as expected from pool.map()')
        else:
            raise AssertionError('expected ZeroDivisionError')

        try:
            print(list(pool.imap(f, list(range(10)))))
        except ZeroDivisionError:
            print('\tGot ZeroDivisionError as expected from list(pool.imap())')
        else:
            raise AssertionError('expected ZeroDivisionError')

        it = pool.imap(f, list(range(10)))
        for i in range(10):
            try:
                x = next(it)
            except ZeroDivisionError:
                if i == 5:
                    pass
            except StopIteration:
                break
            else:
                if i == 5:
                    raise AssertionError('expected ZeroDivisionError')

        assert i == 9
        print('\tGot ZeroDivisionError as expected from IMapIterator.next()')
        print()

        #
        # Testing timeouts
        #

        print('Testing ApplyResult.get() with timeout:', end=' ')
        res = pool.apply_async(calculate, TASKS[0])
        while 1:
            sys.stdout.flush()
            try:
                sys.stdout.write('\n\t%s' % res.get(0.02))
                break
            except multiprocessing.TimeoutError:
                sys.stdout.write('.')
        print()
        print()

        print('Testing IMapIterator.next() with timeout:', end=' ')
        it = pool.imap(calculatestar, TASKS)
        while 1:
            sys.stdout.flush()
            try:
                sys.stdout.write('\n\t%s' % it.next(0.02))
            except StopIteration:
                break
            except multiprocessing.TimeoutError:
                sys.stdout.write('.')
        print()
        print()


if __name__ == '__main__':
    multiprocessing.freeze_support()
    test()
```

​	一个演示如何使用队列来向一组工作进程提供任务并收集结果的例子：

```
import time
import random

from multiprocessing import Process, Queue, current_process, freeze_support

#
# Function run by worker processes
#

def worker(input, output):
    for func, args in iter(input.get, 'STOP'):
        result = calculate(func, args)
        output.put(result)

#
# Function used to calculate result
#

def calculate(func, args):
    result = func(*args)
    return '%s says that %s%s = %s' % \
        (current_process().name, func.__name__, args, result)

#
# Functions referenced by tasks
#

def mul(a, b):
    time.sleep(0.5*random.random())
    return a * b

def plus(a, b):
    time.sleep(0.5*random.random())
    return a + b

#
#
#

def test():
    NUMBER_OF_PROCESSES = 4
    TASKS1 = [(mul, (i, 7)) for i in range(20)]
    TASKS2 = [(plus, (i, 8)) for i in range(10)]

    # Create queues
    task_queue = Queue()
    done_queue = Queue()

    # Submit tasks
    for task in TASKS1:
        task_queue.put(task)

    # Start worker processes
    for i in range(NUMBER_OF_PROCESSES):
        Process(target=worker, args=(task_queue, done_queue)).start()

    # Get and print results
    print('Unordered results:')
    for i in range(len(TASKS1)):
        print('\t', done_queue.get())

    # Add more tasks using `put()`
    for task in TASKS2:
        task_queue.put(task)

    # Get and print some more results
    for i in range(len(TASKS2)):
        print('\t', done_queue.get())

    # Tell child processes to stop
    for i in range(NUMBER_OF_PROCESSES):
        task_queue.put('STOP')


if __name__ == '__main__':
    freeze_support()
    test()
```
