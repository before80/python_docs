+++
title = "multiprocessing.shared_memory --- 可跨进程直接访问的共享内存"
date = 2024-11-15T12:21:05+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html)
>
> 收录该文档的时间：`2024-11-15T12:21:05+08:00`

# `multiprocessing.shared_memory` --- 可跨进程直接访问的共享内存

**源代码:** [Lib/multiprocessing/shared_memory.py](https://github.com/python/cpython/tree/3.13/Lib/multiprocessing/shared_memory.py)

> Added in version 3.8.
>

------

​	该模块提供了一个 [`SharedMemory`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.shared_memory.SharedMemory) 类，用于分配和管理多核或对称多处理器（SMP）机器上进程间的共享内存。 为了协助进行不同进程间共享内存的生命周期管理，在 [`multiprocessing.managers`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.html#module-multiprocessing.managers) 模块中还提供了一个 [`BaseManager`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.html#multiprocessing.managers.BaseManager) 的子类 [`SharedMemoryManager`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.managers.SharedMemoryManager)。

​	在本模块中，共享内存是指“POSIX 风格”的共享内存块（虽然它并不一定被显式地以这种风格实现）而不是指“分布式共享内存”。 这种风格的共享内存允许不同进程读写一块共同的（或共享的）易失性内存区域。 进程在传统上被限制为只能访问它们自己的进程内存空间而共享内存则允许跨进程共享数据，从而避免通过进程间发送消息的形式传递数据。 相比通过磁盘或套接字或者其他需要序列化/反序列化以及数据拷贝的共享形式，直接通过内存共享数据可提供显著的性能提升。

## *class* multiprocessing.shared_memory.**SharedMemory**(*name=None*, *create=False*, *size=0*, ***, *track=True*)

​	创建一个 `SharedMemory` 类的实例用来新建一个共享内存块或关联到一个已存在的共享内存块。 每个共享内存块都被赋予一个独有的名称。 通过这种方式，进程可以创建一个具有特定名称的共享内存块然后别的进程可以使用相同的名称关联到相同的共享内存块。

​	作为一种跨进程共享数据的方式，共享内存块的寿命可以超过创建它的原始进程。 当一个进程不再需要访问一个可能仍被其他进程所需要的的共享内存块时，应当调用 [`close()`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.shared_memory.SharedMemory.close) 方法。 当一个共享内存块不再被任何进程所需要时，则应当调用 [`unlink()`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.shared_memory.SharedMemory.unlink) 方法以确保执行适当的清理操作。

## 参数:

- **name** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) *|* *None*) -- 被请求的共享内存的独有名称，以字符串形式指定。 当创建新的共享内存块时，如果提供 `None` 作为名称（默认值），将随机生成一个新名称。
- **create** ([*bool*](https://docs.python.org/zh-cn/3.13/library/functions.html#bool)) -- 控制是要创建新的共享内存块 (`True`) 还是关联到已有的共享内存块 (`False`)。
- **size** ([*int*](https://docs.python.org/zh-cn/3.13/library/functions.html#int)) -- 当创建新的共享内存块时所请求的字节数。 由于某些平台会选择根据平台的内存页大小来分配内存块，因此共享内存块的实际大小可能会大于等于所请求的大小。 当关联到已有的共享内存块时，*size* 形参将被忽略。
- **track** ([*bool*](https://docs.python.org/zh-cn/3.13/library/functions.html#bool)) -- 当为 `True` 时，将在 OS 不会自动为共享内存块注册资源跟踪器进程的平台上执行注册操作。 资源跟踪器会确保共享内存的正确清理，即使全部其他具有该内存访问权限的进程均未执行清理即退出。 使用 [`multiprocessing`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.html#module-multiprocessing) 的工具创建的具有共同上级的 Python 进程将共享一个资源跟踪器进程，并且共享内存段的生命周期将在这些进程中自动进行管理。 以任何其他方式创建的 Python 进程在启用 *track* 的情况下访问共享内存时将获得它们自己的资源跟踪器。 这将导致共享内存会被第一个终结的进程的资源跟踪器删除。 为避免此问题，[`subprocess`](https://docs.python.org/zh-cn/3.13/library/subprocess.html#module-subprocess) 或独立 Python 进程的使用者在已经有另一个进程执行跟踪记录时应当将 *track* 设为 `False`。在 Windows 上 *track* 将被忽略，因为该系统有自己的跟踪机制并会在所有指向特定共享内存的句柄被关闭时自动删除它。

*在 3.13 版本发生变更:* 增加了 *track* 形参。

## **close**()

​	关闭该实例指向共享内存的文件描述符/句柄。 一旦不再需要从该实例指向共享内存块的访问权限 [`close()`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.shared_memory.SharedMemory.close) 就应当被调用。 根据具体的操作系统，即使所有指向下层内存的句柄都已被关闭，内存都有可能被释放也有可能不被释放。 要确保正确的清理，请使用 [`unlink()`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.shared_memory.SharedMemory.unlink) 方法。

## **unlink**()

​	删除下层的共享内存块。 此方法在每个共享内存块上应当只被调用一次，无论指向它的句柄数量有多少。 [`unlink()`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.shared_memory.SharedMemory.unlink) 和 [`close()`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.shared_memory.SharedMemory.close) 可以按任意顺序调用，但在can be called in any order, but trying to access data inside a shared memory block after [`unlink()`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.shared_memory.SharedMemory.unlink) 之后再试图访问共享内存块中的数据将导致内存访问错误，其种类取决于具体的系统平台。

​	此方法在 Windows 上无效，在该系统上删除共享内存块的唯一方式是关闭所有的句柄。

## **buf**

​	共享内存块内容的 memoryview 。

## **name**

​	共享内存块的唯一标识，只读属性。

## **size**

​	共享内存块的字节大小，只读属性。

​	以下示例展示了 [`SharedMemory`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.shared_memory.SharedMemory) 底层的用法:



``` python
>>> from multiprocessing import shared_memory
>>> shm_a = shared_memory.SharedMemory(create=True, size=10)
>>> type(shm_a.buf)
<class 'memoryview'>
>>> buffer = shm_a.buf
>>> len(buffer)
10
>>> buffer[:4] = bytearray([22, 33, 44, 55])  # 一次修改多个字节
>>> buffer[4] = 100                           # 一次修改单个字节
>>> # 关联到现有的共享内存块
>>> shm_b = shared_memory.SharedMemory(shm_a.name)
>>> import array
>>> array.array('b', shm_b.buf[:5])  # 将数据拷贝到一个新的 array.array
array('b', [22, 33, 44, 55, 100])
>>> shm_b.buf[:5] = b'howdy'  # 通过 shm_b 使用字节串来修改
>>> bytes(shm_a.buf[:5])      # 通过 shm_a 来访问
b'howdy'
>>> shm_b.close()   # 关闭每个 SharedMemory 实例
>>> shm_a.close()
>>> shm_a.unlink()  # 仅调用一次 unlink 来释放共享内存
```

​	下面的例子展示了 [`SharedMemory`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.shared_memory.SharedMemory) 类配合 [NumPy 数组](https://numpy.org/) 的实际应用，从两个独立的 Python shell 访问相同的 `numpy.ndarray`:



``` python
>>> # 在第一个 Python 交互式 shell 中
>>> import numpy as np
>>> a = np.array([1, 1, 2, 3, 5, 8])  # 从一个现有的 NumPy 数组开始
>>> from multiprocessing import shared_memory
>>> shm = shared_memory.SharedMemory(create=True, size=a.nbytes)
>>> # Now create a NumPy array backed by shared memory
>>> b = np.ndarray(a.shape, dtype=a.dtype, buffer=shm.buf)
>>> b[:] = a[:]  # Copy the original data into shared memory
>>> b
array([1, 1, 2, 3, 5, 8])
>>> type(b)
<class 'numpy.ndarray'>
>>> type(a)
<class 'numpy.ndarray'>
>>> shm.name  # 我们没有指定名称因此会自动为我们选择一个
'psm_21467_46075'

>>> # 在同一个 shell 中或同一台机器上新的 Python shell 中
>>> import numpy as np
>>> from multiprocessing import shared_memory
>>> # 关联到现有的共享内存块
>>> existing_shm = shared_memory.SharedMemory(name='psm_21467_46075')
>>> # 请注意在这个例子中 a.shape 为 (6,) 而 a.dtype 为 np.int64
>>> c = np.ndarray((6,), dtype=np.int64, buffer=existing_shm.buf)
>>> c
array([1, 1, 2, 3, 5, 8])
>>> c[-1] = 888
>>> c
array([  1,   1,   2,   3,   5, 888])

>>> # 回到第一个 Python 交互式 shell，b 将反映出此变化
>>> b
array([  1,   1,   2,   3,   5, 888])

>>> # 在第二个 Python shell 中执行清理
>>> del c  # 不是必须的；只是强调该数组已不再使用
>>> existing_shm.close()

>>> # 在第一个 Python shell 中执行清理
>>> del b  # 不是必须的；只是强制该数组已不再使用
>>> shm.close()
>>> shm.unlink()  # 最后清理并释放共享内存块
```

## *class* multiprocessing.managers.**SharedMemoryManager**([*address*[, *authkey*]])

[`multiprocessing.managers.BaseManager`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.html#multiprocessing.managers.BaseManager) 的子类，可被用于跨进程的共享内存块管理。

​	在 `SharedMemoryManager` 实例上调用 [`start()`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.html#multiprocessing.managers.BaseManager.start) 方法会导致启动一个新进程。 这个新进程的唯一目的就是管理所有通过它创建的共享内存块的生命周期。 想要释放该进程所管理的全部共享内存块，可以在实例上调用 [`shutdown()`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.html#multiprocessing.managers.BaseManager.shutdown)。 这会触发执行该进程所管理的所有 [`SharedMemory`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.managers.SharedMemoryManager.SharedMemory) 对象上的 [`unlink()`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.shared_memory.SharedMemory.unlink) 调用，然后停止该进程本身。 通过 `SharedMemoryManager` 创建 `SharedMemory` 实例，我们可以避免手动跟踪并触发共享内存资源的释放。

​	这个类提供了创建和返回 [`SharedMemory`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.managers.SharedMemoryManager.SharedMemory) 实例的方法，以及以共享内存为基础创建一个列表类对象 ([`ShareableList`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.managers.SharedMemoryManager.ShareableList)) 的方法。

​	请参阅 [`BaseManager`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.html#multiprocessing.managers.BaseManager) 查看有关被继承的可选输入参数 *address* 和 *authkey* 以及如何使用它们来从其他进程连接已有的optional input arguments and how they may be used to connect to an existing `SharedMemoryManager` 服务的说明。

## **SharedMemory**(*size*)

​	新建并返回一个具有指定的 *size* 个字节的 [`SharedMemory`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.managers.SharedMemoryManager.SharedMemory) 对象。

## **ShareableList**(*sequence*)

​	新建并返回一个 [`ShareableList`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.managers.SharedMemoryManager.ShareableList) 对象，使用从 *sequence* 输入的值来初始化。

​	下面的例子展示了 [`SharedMemoryManager`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.managers.SharedMemoryManager) 的基本机制：



``` python
>>> from multiprocessing.managers import SharedMemoryManager
>>> smm = SharedMemoryManager()
>>> smm.start()  # 启动管理共享内存块的进程
>>> sl = smm.ShareableList(range(4))
>>> sl
ShareableList([0, 1, 2, 3], name='psm_6572_7512')
>>> raw_shm = smm.SharedMemory(size=128)
>>> another_sl = smm.ShareableList('alpha')
>>> another_sl
ShareableList(['a', 'l', 'p', 'h', 'a'], name='psm_6572_12221')
>>> smm.shutdown()  # 在 sl, raw_shm 和 another_sl 上调用 unlink()
```

​	下面的例子展示了使用 [`SharedMemoryManager`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.managers.SharedMemoryManager) 对象的一种更方便的方式，通过 [`with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#with) 语句来确保所有共享内存块在它们不再被需要时得到释放：



``` python
>>> with SharedMemoryManager() as smm:
...     sl = smm.ShareableList(range(2000))
...     # 将工作分给两个进程，将部分结果存储在 sl 中
...     p1 = Process(target=do_work, args=(sl, 0, 1000))
...     p2 = Process(target=do_work, args=(sl, 1000, 2000))
...     p1.start()
...     p2.start()  # 用 multiprocessing.Pool 可能会更高效
...     p1.join()
...     p2.join()   # 等等两个进程中的所有工作完成
...     total_result = sum(sl)  # 现在合并 sl 中的部分结果
```

​	当在 [`with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#with) 语句中使用 [`SharedMemoryManager`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.managers.SharedMemoryManager) 对象时，使用这个管理器创建的共享内存块会在 `with` 语句代码块结束执行时全部被释放。

## *class* multiprocessing.shared_memory.**ShareableList**(*sequence=None*, ***, *name=None*)

​	提供一个可变的列表型对象，其中存储的所有值都是存储在一个共享内存块中。 这会将可存储的值限制为下列内置数据类型：

- [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int) (有符号 64 位)
- [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float)
- [`bool`](https://docs.python.org/zh-cn/3.13/library/functions.html#bool)
- [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) (当使用 UTF-8 编码时每个小于 10M 字节)
- [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) (每个小于 10M 字节)
- `None`

​	它与内置 [`list`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#list) 类型的显著区别还在于这些列表无法改变其总长度（即没有 `append()`, `insert()` 等）并且不支持通过切片动态地创建新的 `ShareableList`。

*sequence* 会被用来填充已有值的新 `ShareableList`。 设为 `None` 则会基于唯一的共享内存名称联系到现有的 `ShareableList`。

*name* 是所请求的共享内存的唯一名称，与 [`SharedMemory`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.shared_memory.SharedMemory) 的定义中描述的一致。 当关联到现有的 `ShareableList` 时，将指明其共享内存块的唯一名称并将 *sequence* 设为 `None`。

​	备注

 

[`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 和 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 值存在一个已知问题。 如果它们以 `\x00` 空字节或字符结尾，那么当按索引号从 `ShareableList` 提取这些值时它们可能会被 *静默地去除*。 这种 `.rstrip(b'\x00')` 行为并认为是一个程序错误并可能在未来被修复。 参见 [gh-106939](https://github.com/python/cpython/issues/106939)。

​	对于某些应用来说在右侧截去尾部空值会造成问题，要绕过此问题可以在存储这样的值时总是无条件地在其末尾附加一个额外的非 0 字节并在获取时无条件地移除它:



``` python
>>> from multiprocessing import shared_memory
>>> nul_bug_demo = shared_memory.ShareableList(['?\x00', b'\x03\x02\x01\x00\x00\x00'])
>>> nul_bug_demo[0]
'?'
>>> nul_bug_demo[1]
b'\x03\x02\x01'
>>> nul_bug_demo.shm.unlink()
>>> padded = shared_memory.ShareableList(['?\x00\x07', b'\x03\x02\x01\x00\x00\x00\x07'])
>>> padded[0][:-1]
'?\x00'
>>> padded[1][:-1]
b'\x03\x02\x01\x00\x00\x00'
>>> padded.shm.unlink()
```

## **count**(*value*)

​	返回 *value* 出现的次数。

## **index**(*value*)

​	返回 *value* 首次出现的索引位置。 如果 *value* 不存在则会引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

## **format**

​	包含由所有当前存储值所使用的 [`struct`](https://docs.python.org/zh-cn/3.13/library/struct.html#module-struct) 打包格式的只读属性。

## **shm**

​	存储了值的 [`SharedMemory`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.shared_memory.SharedMemory) 实例。

​	下面的例子演示了 [`ShareableList`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.shared_memory.ShareableList) 实例的基本用法:



``` python
>>> from multiprocessing import shared_memory
>>> a = shared_memory.ShareableList(['howdy', b'HoWdY', -273.154, 100, None, True, 42])
>>> [ type(entry) for entry in a ]
[<class 'str'>, <class 'bytes'>, <class 'float'>, <class 'int'>, <class 'NoneType'>, <class 'bool'>, <class 'int'>]
>>> a[2]
-273.154
>>> a[2] = -78.5
>>> a[2]
-78.5
>>> a[2] = 'dry ice'  # Changing data types is supported as well
>>> a[2]
'dry ice'
>>> a[2] = 'larger than previously allocated storage space'
Traceback (most recent call last):
  ...
ValueError: exceeds available storage for existing str
>>> a[2]
'dry ice'
>>> len(a)
7
>>> a.index(42)
6
>>> a.count(b'howdy')
0
>>> a.count(b'HoWdY')
1
>>> a.shm.close()
>>> a.shm.unlink()
>>> del a  # Use of a ShareableList after call to unlink() is unsupported
```

​	下面的例子演示了一个、两个或多个进程如何通过提供下层的共享内存块名称来访问同一个 [`ShareableList`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.shared_memory.ShareableList):



``` python
>>> b = shared_memory.ShareableList(range(5))         # In a first process
>>> c = shared_memory.ShareableList(name=b.shm.name)  # In a second process
>>> c
ShareableList([0, 1, 2, 3, 4], name='...')
>>> c[-1] = -999
>>> b[-1]
-999
>>> b.shm.close()
>>> c.shm.close()
>>> c.shm.unlink()
```

​	下面的例子显示 [`ShareableList`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.shared_memory.ShareableList) (以及下层的 [`SharedMemory`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.shared_memory.html#multiprocessing.shared_memory.SharedMemory)) 对象可以在必要时被封存和解封。 请注意，它将仍然为同一个共享对象。 出现这种情况是因为被反序列化的对象具有相同的唯一名称并会使用这个相同的名称附加到现有的对象上（如果对象仍然存活）：



``` python
>>> import pickle
>>> from multiprocessing import shared_memory
>>> sl = shared_memory.ShareableList(range(10))
>>> list(sl)
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```



``` python
>>> deserialized_sl = pickle.loads(pickle.dumps(sl))
>>> list(deserialized_sl)
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```



``` python
>>> sl[0] = -1
>>> deserialized_sl[1] = -2
>>> list(sl)
[-1, -2, 2, 3, 4, 5, 6, 7, 8, 9]
>>> list(deserialized_sl)
[-1, -2, 2, 3, 4, 5, 6, 7, 8, 9]
```



``` python
>>> sl.shm.close()
>>> sl.shm.unlink()
```
