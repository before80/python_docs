+++
title = "mmap --- 内存映射文件支持"
date = 2024-11-15T12:30:27+08:00
weight = 60
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/mmap.html](https://docs.python.org/zh-cn/3.13/library/mmap.html)
>
> 收录该文档的时间：`2024-11-15T12:30:27+08:00`

# `mmap` --- 内存映射文件支持

------

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): not WASI.

​	此模块在 WebAssembly 平台上无效或不可用。 请参阅 [WebAssembly 平台](https://docs.python.org/zh-cn/3.13/library/intro.html#wasm-availability) 了解详情。

​	内存映射文件对象的行为既像 [`bytearray`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytearray) 又像 [文件对象](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-object)。 你可以在大部分接受 [`bytearray`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytearray) 的地方使用 mmap 对象；例如，你可以使用 [`re`](https://docs.python.org/zh-cn/3.13/library/re.html#module-re) 模块来搜索一个内存映射文件。 你也可以通过执行 `obj[index] = 97` 来修改单个字节，或者通过对切片赋值来修改一个子序列: `obj[i1:i2] = b'...'`。 你还可以在文件的当前位置开始读取和写入数据，并使用 `seek()` 前往另一个位置。

​	内存映射文件是由 [`mmap`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.mmap) 构造器创建的，它在 Unix 和在 Windows 上会有所不同。 无论在哪种情况下你都必须为一个打开用于更新的文件提供文件描述符。 如果你想要映射一个已有的 Python 文件对象，请使用其 [`fileno()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.fileno) 方法来为 *fileno* 形参获取正确的值。 否则，你可以使用 [`os.open()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.open) 函数来打开这个文件，它会直接返回一个文件描述符（结束时仍然需要关闭该文件）。

​	备注

 

​	如果要为可写的缓冲文件创建内存映射，则应当首先 [`flush()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.flush) 该文件。 这确保了对缓冲区的本地修改在内存映射中可用。

​	对于 Unix 和 Windows 版本的构造函数，可以将 *access* 指定为可选的关键字参数。 *access* 接受以下四个值之一： `ACCESS_READ` ， `ACCESS_WRITE` 或 `ACCESS_COPY` 分别指定只读，直写或写时复制内存，或 `ACCESS_DEFAULT` 推迟到 *prot* 。 *access* 可以在 Unix 和 Windows 上使用。如果未指定 *access* ，则 Windows mmap 返回直写映射。这三种访问类型的初始内存值均取自指定的文件。向 `ACCESS_READ` 内存映射赋值会引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError) 异常。 向 `ACCESS_WRITE` 内存映射赋值会影响内存和底层的文件。 向 `ACCESS_COPY` 内存映射赋值会影响内存，但不会更新底层的文件。

*在 3.7 版本发生变更:* 添加了 `ACCESS_DEFAULT` 常量。

​	要映射匿名内存，应将 -1 作为 fileno 和 length 一起传递。

## *class* mmap.**mmap**(*fileno*, *length*, *tagname=None*, *access=ACCESS_DEFAULT*, *offset=0*)

**（ Windows 版本）** 映射被文件句柄 *fileno* 指定的文件的 *length* 个字节，并创建一个 mmap 对象。如果 *length* 大于当前文件大小，则文件将扩展为包含 *length* 个字节。如果 *length* 为 `0`，则映射的最大长度为当前文件大小。如果文件为空， Windows 会引发异常（你无法在Windows上创建空映射）。

​	如果指定了 *tagname* 并且不为 `None`，则将是一个为映射提供标签名称的字符串。 Windows 允许对同一文件设置许多不同的映射。 如果指定一个现有标签的名称，则将打开该标签，否则将创建一个具有该名称的新标签。 如果此形参被省略或为 `None`，则创建的映射将不带名称。 避免使用 *tagname* 形参将有助于使你的代码在 Unix 和 Windows 之间可移植。

*offset* 可以被指定为非负整数偏移量。 mmap 引用将相对于从文件开头的偏移。 *offset* 默认为0。 *offeset* 必须是 `ALLOCATIONGRANULARITY` 的倍数。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `mmap.__new__` 并附带参数 `fileno`, `length`, `access`, `offset`。

## *class* mmap.**mmap**(*fileno*, *length*, *flags=MAP_SHARED*, *prot=PROT_WRITE | PROT_READ*, *access=ACCESS_DEFAULT*, *offset=0*, ***, *trackfd=True*)

**(Unix 版本)** 映射文件描述符 *fileno* 指定的文件的 *length* 个字节，并返回一个 mmap 对象。如果 *length* 为 `0` ，则当调用 [`mmap`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.mmap) 时，映射的最大长度将为文件的当前大小。

*flags* 指明映射的性质。 [`MAP_PRIVATE`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.MAP_PRIVATE) 会创建私有的写入时拷贝映射，因此对 mmap 对象内容的修改将为该进程所私有。 而 [`MAP_SHARED`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.MAP_SHARED) 会创建与其他映射同一文件区域的进程所共享的映射。 默认值为 [`MAP_SHARED`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.MAP_SHARED)。 某些系统还具有额外的可用旗标，完整列表会在 [MAP_* 常量](https://docs.python.org/zh-cn/3.13/library/mmap.html#map-constants) 中指明。

​	如果指明了 *prot*，它将给出所需的内存保护方式；最有用的两个值是 `PROT_READ` 和 `PROT_WRITE`，分别指明页面为可读或可写。 *prot* 默认为 `PROT_READ | PROT_WRITE`。

​	可以指定 *access* 作为替代 *flags* 和 *prot* 的可选关键字形参。 同时指定 *flags*, *prot* 和 *access* 将导致错误。 请参阅上文中 *access* 的描述了解有关如何使用此形参的信息。

*offset* 可以被指定为非负整数偏移量。 mmap 引用将相对于从文件开头的偏移。 *offset* 默认为 0。 *offset* 必须是 `ALLOCATIONGRANULARITY` 的倍数，它在 Unix 系统上等价于 `PAGESIZE`。

​	如果 *trackfd* 为 `False`，则由 *fileno* 指定的文件描述符将不会被复制，而结果 `mmap` 对象将不会被关联到映射的下层文件。 这意味着 [`size()`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.mmap.size) 和 [`resize()`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.mmap.resize) 方法将会失败。 此模式适用于限制打开文件描述符的数量。

​	为了确保已创建内存映射的有效性，描述符 *fileno* 所指定的文件在 macOS 上会与物理后备存储进行内部自动同步。

*在 3.13 版本发生变更:* 增加了 *trackfd* 形参。

​	这个例子演示了使用 [`mmap`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.mmap) 的简单方式:

```
import mmap

# 写入一个简单的示例文件
with open("hello.txt", "wb") as f:
    f.write(b"Hello Python!\n")

with open("hello.txt", "r+b") as f:
    # 对文件进行内存映射，大小为 0 表示整个文件
    mm = mmap.mmap(f.fileno(), 0)
    # 通过标准文件方法读取内容
    print(mm.readline())  # prints b"Hello Python!\n"
    # 通过切片标记方式读取内容
    print(mm[:5])  # 打印 b"Hello"
    # 使用切片标记方式更新内容；
    # 请注意新内容必须为相同的大小
    mm[6:] = b" world!\n"
    # ... 并使用标准文件方法再次读取
    mm.seek(0)
    print(mm.readline())  # prints b"Hello  world!\n"
    # 关闭映射
    mm.close()
```

[`mmap`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.mmap) 也可以在 [`with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#with) 语句中被用作上下文管理器:

```
import mmap

with mmap.mmap(-1, 13) as mm:
    mm.write(b"Hello world!")
```

*Added in version 3.2:* 上下文管理器支持。

​	下面的例子演示了如何创建一个匿名映射并在父进程和子进程之间交换数据。:

```
import mmap
import os

mm = mmap.mmap(-1, 13)
mm.write(b"Hello world!")

pid = os.fork()

if pid == 0:  # In a child process
    mm.seek(0)
    print(mm.readline())

    mm.close()
```

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `mmap.__new__` 并附带参数 `fileno`, `length`, `access`, `offset`。

​	映射内存的文件对象支持以下方法:

## **close**()

​	关闭 mmap。 后续调用该对象的其他方法将导致引发 ValueError 异常。 此方法将不会关闭打开的文件。

## **closed**

​	如果文件已关闭则返回 `True`。

*Added in version 3.2.*

## **find**(*sub*[, *start*[, *end*]])

​	返回子序列 *sub* 在对象内被找到的最小索引号，使得 *sub* 被包含在 [*start*, *end*] 范围中。 可选参数 *start* 和 *end* 会被解读为切片表示法。 如果未找到则返回 `-1`。

*在 3.5 版本发生变更:* 现在接受可写的 [字节类对象](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object)。

## **flush**([*offset*[, *size*]])

​	将对文件的内存副本的修改刷新至磁盘。 如果不使用此调用则无法保证在对象被销毁前将修改写回存储。 如果指定了 *offset* 和 *size*，则只将对指定范围内字节的修改刷新至磁盘；在其他情况下，映射的全部范围都会被刷新。 *offset* 必须为 `PAGESIZE` 或 `ALLOCATIONGRANULARITY` 的倍数。

​	返回 `None` 以表示成功。 当调用失败时将引发异常。

*在 3.8 版本发生变更:* 在之前版本中，成功时将返回非零值；在 Windows 下当发生错误时将返回零。 在 Unix 下 成功时将返回零值；当发生错误时将引发异常。

## **madvise**(*option*[, *start*[, *length*]])

​	将有关内存区域的建议 *option* 发送至内核，从 *start* 开始扩展 *length* 个字节。 *option* 必须为系统中可用的 [MADV_* 常量](https://docs.python.org/zh-cn/3.13/library/mmap.html#madvise-constants) 之一。 如果省略 *start* 和 *length*，则会包含整个映射。 在某些系统中（包括 Linux），*start* 必须为 `PAGESIZE` 的倍数。

​	可用性: 具有 `madvise()` 系统调用的系统。

*Added in version 3.8.*

## **move**(*dest*, *src*, *count*)

​	将从偏移量 *src* 开始的 *count* 个字节拷贝到目标索引号 *dest*。 如果 mmap 创建时设置了 `ACCESS_READ`，则调用 move 将引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError) 异常。

## **read**([*n*])

​	返回一个 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)，其中包含从当前文件位置开始的至多 *n* 个字节。 如果参数省略，为 `None` 或负数，则返回从当前文件位置开始直至映射结尾的所有字节。 文件位置会被更新为返回字节数据之后的位置。

*在 3.3 版本发生变更:* 参数可被省略或为 `None`。

## **read_byte**()

​	将当前文件位置上的一个字节以整数形式返回，并让文件位置前进 1。

## **readline**()

​	返回一个单独的行，从当前文件位置开始直到下一个换行符。 文件位置会被更新为返回字节数据之后的位置。

## **resize**(*newsize*)

​	改变映射和下层文件的大小，如果存在的话。

​	改变具有 `ACCESS_READ` 或 `ACCESS_COPY` *访问权限* 的已创建映射的大小，将引发一个 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError) 异常。 改变 *trackfd* 被设为 `False` 的已创建映射的大小，将引发一个 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError) 异常。

**在 Windows 上**: 如果存在其他针对相同名称文件的映射则改变映射大小将引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError)。 改变匿名映射（即针对分页文件）的大小将静默地创建一个新映射并将原始数据复制到对应新大小的长度。

*在 3.11 版本发生变更:* 如果在持有另一个映射允许在 Windows 上针对匿名映射改变大小的情况下尝试改变大小则会正确地报告失败

## **rfind**(*sub*[, *start*[, *end*]])

​	返回子序列 *sub* 在对象内被找到的最大索引号，使得 *sub* 被包含在 [*start*, *end*] 范围中。 可选参数 *start* 和 *end* 会被解读为切片表示法。 如果未找到则返回 `-1`。

*在 3.5 版本发生变更:* 现在接受可写的 [字节类对象](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object)。

## **seek**(*pos*[, *whence*])

​	设置文件的当前位置。 *whence* 参数为可选项并且默认为 `os.SEEK_SET` 或 `0` (绝对文件定位)；其他值还有 `os.SEEK_CUR` 或 `1` (相对当前位置查找) 和 `os.SEEK_END` 或 `2` (相对文件末尾查找)。

*在 3.13 版本发生变更:* 返回一个新的绝对位置值而非 `None`。

## **seekable**()

​	返回文件是否支持定位，返回值将始终为 `True`。

*Added in version 3.13.*

## **size**()

​	返回文件的长度，该数值可以大于内存映射区域的大小。

## **tell**()

​	返回文件指针的当前位置。

## **write**(*bytes*)

​	将 *bytes* 中的字节数据写入文件指针当前位置的内存并返回写入的字节总数 (一定不小于 `len(bytes)`，因为如果写入失败，将会引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError))。 在字节数据被写入后文件位置将会更新。 如果 mmap 创建时设置了 `ACCESS_READ`，则向其写入将引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError) 异常。

*在 3.5 版本发生变更:* 现在接受可写的 [字节类对象](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object)。

*在 3.6 版本发生变更:* 现在会返回写入的字节总数。

## **write_byte**(*byte*)

​	将整数值 *byte* 写入文件指针当前位置的内存；文件位置前进 `1`。 如果 mmap 创建时设置了 `ACCESS_READ`，则向其写入将引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError) 异常。



## MADV_* 常量

## mmap.**MADV_NORMAL**

## mmap.**MADV_RANDOM**

## mmap.**MADV_SEQUENTIAL**

## mmap.**MADV_WILLNEED**

## mmap.**MADV_DONTNEED**

## mmap.**MADV_REMOVE**

## mmap.**MADV_DONTFORK**

## mmap.**MADV_DOFORK**

## mmap.**MADV_HWPOISON**

## mmap.**MADV_MERGEABLE**

## mmap.**MADV_UNMERGEABLE**

## mmap.**MADV_SOFT_OFFLINE**

## mmap.**MADV_HUGEPAGE**

## mmap.**MADV_NOHUGEPAGE**

## mmap.**MADV_DONTDUMP**

## mmap.**MADV_DODUMP**

## mmap.**MADV_FREE**

## mmap.**MADV_NOSYNC**

## mmap.**MADV_AUTOSYNC**

## mmap.**MADV_NOCORE**

## mmap.**MADV_CORE**

## mmap.**MADV_PROTECT**

## mmap.**MADV_FREE_REUSABLE**

## mmap.**MADV_FREE_REUSE**

​	这些选项可被传给 [`mmap.madvise()`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.mmap.madvise)。 不是每个选项都存在于每个系统中。

​	可用性: 具有 madvise() 系统调用的系统。

*Added in version 3.8.*



## MAP_* 常量

## mmap.**MAP_SHARED**

## mmap.**MAP_PRIVATE**

## mmap.**MAP_32BIT**

## mmap.**MAP_ALIGNED_SUPER**

## mmap.**MAP_ANON**

## mmap.**MAP_ANONYMOUS**

## mmap.**MAP_CONCEAL**

## mmap.**MAP_DENYWRITE**

## mmap.**MAP_EXECUTABLE**

## mmap.**MAP_HASSEMAPHORE**

## mmap.**MAP_JIT**

## mmap.**MAP_NOCACHE**

## mmap.**MAP_NOEXTEND**

## mmap.**MAP_NORESERVE**

## mmap.**MAP_POPULATE**

## mmap.**MAP_RESILIENT_CODESIGN**

## mmap.**MAP_RESILIENT_MEDIA**

## mmap.**MAP_STACK**

## mmap.**MAP_TPRO**

## mmap.**MAP_TRANSLATED_ALLOW_EXECUTE**

## mmap.**MAP_UNIX03**

​	以下是可被传给 [`mmap.mmap()`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.mmap) 的各种旗标。 [`MAP_ALIGNED_SUPER`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.MAP_ALIGNED_SUPER) 仅在 FreeBSD 上可用而 [`MAP_CONCEAL`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.MAP_CONCEAL) 仅在 OpenBSD 上可用。 请注意某些选项在某些系统上可能不存在。

*在 3.10 版本发生变更:* 增加了 [`MAP_POPULATE`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.MAP_POPULATE) 常量。

*Added in version 3.11:* 增加了 [`MAP_STACK`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.MAP_STACK) 常量。

*Added in version 3.12:* 增加了 [`MAP_ALIGNED_SUPER`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.MAP_ALIGNED_SUPER) 和 [`MAP_CONCEAL`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.MAP_CONCEAL) 常量。

*Added in version 3.13:* 增加了 [`MAP_32BIT`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.MAP_32BIT), [`MAP_HASSEMAPHORE`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.MAP_HASSEMAPHORE), [`MAP_JIT`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.MAP_JIT), [`MAP_NOCACHE`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.MAP_NOCACHE), [`MAP_NOEXTEND`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.MAP_NOEXTEND), [`MAP_NORESERVE`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.MAP_NORESERVE), [`MAP_RESILIENT_CODESIGN`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.MAP_RESILIENT_CODESIGN), [`MAP_RESILIENT_MEDIA`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.MAP_RESILIENT_MEDIA), [`MAP_TPRO`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.MAP_TPRO), [`MAP_TRANSLATED_ALLOW_EXECUTE`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.MAP_TRANSLATED_ALLOW_EXECUTE) 和 [`MAP_UNIX03`](https://docs.python.org/zh-cn/3.13/library/mmap.html#mmap.MAP_UNIX03) 等常量。
