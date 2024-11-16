+++
title = "bz2 --- 对 bzip2 压缩算法的支持"
date = 2024-11-15T12:00:20+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/bz2.html](https://docs.python.org/zh-cn/3.13/library/bz2.html)
>
> 收录该文档的时间：`2024-11-15T12:00:20+08:00`

# `bz2` --- 对 **bzip2** 压缩算法的支持

**源代码：** [Lib/bz2.py](https://github.com/python/cpython/tree/3.13/Lib/bz2.py)

------

​	此模块提供了使用 bzip2 压缩算法压缩和解压数据的一套完整的接口。

[`bz2`]({{< ref "/library/archiving/bz2#module-bz2" >}}) 模块包含：

- 用于读写压缩文件的 [`open()`]({{< ref "/library/archiving/bz2#bz2.open" >}}) 函数和 [`BZ2File`]({{< ref "/library/archiving/bz2#bz2.BZ2File" >}}) 类。
- 用于增量压缩和解压的 [`BZ2Compressor`]({{< ref "/library/archiving/bz2#bz2.BZ2Compressor" >}}) 和 [`BZ2Decompressor`]({{< ref "/library/archiving/bz2#bz2.BZ2Decompressor" >}}) 类。
- 用于一次性压缩和解压的 [`compress()`]({{< ref "/library/archiving/bz2#bz2.compress" >}}) 和 [`decompress()`]({{< ref "/library/archiving/bz2#bz2.decompress" >}}) 函数。

## 文件压缩和解压

## bz2.**open**(*filename*, *mode='rb'*, *compresslevel=9*, *encoding=None*, *errors=None*, *newline=None*)

​	以二进制或文本模式打开 bzip2 压缩文件，返回一个 [file object]({{< ref "/glossary/idx#term-file-object" >}})。

​	和 [`BZ2File`]({{< ref "/library/archiving/bz2#bz2.BZ2File" >}}) 的构造函数类似，*filename* 参数可以是一个实际的文件名（[`str`]({{< ref "/library/stdtypes#str" >}}) 或 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 对象），或是已有的可供读取或写入的文件对象。

*mode* 参数可设为二进制模式的 `'r'`、`'rb'`、`'w'`、`'wb'`、`'x'`、`'xb'`、`'a'` 或 `'ab'`，或者文本模式的 `'rt'`、`'wt'`、`'xt'` 或 `'at'`。默认是 `'rb'`。

*compresslevel* 参数是 1 到 9 的整数，和 [`BZ2File`]({{< ref "/library/archiving/bz2#bz2.BZ2File" >}}) 的构造函数一样。

​	对于二进制模式，这个函数等价于 [`BZ2File`]({{< ref "/library/archiving/bz2#bz2.BZ2File" >}}) 构造器: `BZ2File(filename, mode, compresslevel=compresslevel)`。 在这种情况下，不可提供 *encoding*, *errors* 和 *newline* 参数。

​	对于文本模式，将会创建一个 [`BZ2File`]({{< ref "/library/archiving/bz2#bz2.BZ2File" >}}) 对象，并将它包装到一个 [`io.TextIOWrapper`]({{< ref "/library/allos/io#io.TextIOWrapper" >}}) 实例中，此实例带有指定的编码格式、错误处理行为和行结束符。

> Added in version 3.3.
>

*在 3.4 版本发生变更:* 添加了 `'x'` (单独创建) 模式。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## *class* bz2.**BZ2File**(*filename*, *mode='r'*, ***, *compresslevel=9*)

​	用二进制模式打开 bzip2 压缩文件。

​	如果 *filename* 是一个 [`str`]({{< ref "/library/stdtypes#str" >}}) 或 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 对象，则打开名称对应的文件目录。 否则的话，*filename* 应当是一个 [file object]({{< ref "/glossary/idx#term-file-object" >}})，它将被用来读取或写入压缩数据。

*mode* 参数可以是表示读取的 `'r'` (默认值)，表示覆写的 `'w'`，表示单独创建的 `'x'`，或表示添加的 `'a'`。 这些模式还可分别以 `'rb'`, `'wb'`, `'xb'` 和 `'ab'` 的等价形式给出。

​	如果 *filename* 是一个文件对象（而不是实际的文件名），则 `'w'` 模式并不会截断文件，而是会等价于 `'a'`。

​	如果 *mode* 为 `'w'` 或 `'a'`，则 *compresslevel* 可以是 `1` 到 `9` 之间的整数，用于指定压缩等级: `1` 产生最低压缩率，而 `9` (默认值) 产生最高压缩率。

​	如果 *mode* 为 `'r'`，则输入文件可以为多个压缩流的拼接。

[`BZ2File`]({{< ref "/library/archiving/bz2#bz2.BZ2File" >}}) 提供了 [`io.BufferedIOBase`]({{< ref "/library/allos/io#io.BufferedIOBase" >}}) 所指定的所有成员，但 [`detach()`]({{< ref "/library/allos/io#io.BufferedIOBase.detach" >}}) 和 [`truncate()`]({{< ref "/library/allos/io#io.IOBase.truncate" >}}) 除外。 并支持迭代和 [`with`]({{< ref "/reference/compound_stmts#with" >}}) 语句。

[`BZ2File`]({{< ref "/library/archiving/bz2#bz2.BZ2File" >}}) 还提供了以下方法和属性：

## **peek**([*n*])

​	返回缓冲的数据而不前移文件位置。 至少将返回一个字节的数据（除非为 EOF）。 实际返回的字节数不确定。

​	备注

 

​	虽然调用 [`peek()`]({{< ref "/library/archiving/bz2#bz2.BZ2File.peek" >}}) 不会改变 [`BZ2File`]({{< ref "/library/archiving/bz2#bz2.BZ2File" >}}) 的文件位置，但它可能改变下层文件对象的位置（举例来说如果 [`BZ2File`]({{< ref "/library/archiving/bz2#bz2.BZ2File" >}}) 是通过传入一个文件对象作为 *filename* 的话）。

> Added in version 3.3.
>

## **fileno**()

​	返回底层文件的文件描述符。

> Added in version 3.3.
>

## **readable**()

​	返回文件是否已被打开供读取。

> Added in version 3.3.
>

## **seekable**()

​	返回文件是否支持定位。

> Added in version 3.3.
>

## **writable**()

​	返回文件是否已被打开供写入。

> Added in version 3.3.
>

## **read1**(*size=-1*)

​	读取至多 *size* 个未压缩字节，将会避免多次从下层流读取。 如果 size 为负值则读取至多为缓冲区数据大小。

​	如果文件位置为 EOF 则返回 `b''`。

> Added in version 3.3.
>

## **readinto**(*b*)

​	将字节数据读取到 *b*。

​	返回读取的字节数（0 表示 EOF）。

> Added in version 3.3.
>

## **mode**

`'rb'` 表示可读而 `'wb'` 表示可写。

> Added in version 3.13.
>

## **name**

​	bzip2 文件名。 等价于下层 [file object]({{< ref "/glossary/idx#term-file-object" >}}) 的 [`name`]({{< ref "/library/allos/io#io.FileIO.name" >}}) 属性。

> Added in version 3.13.
>

*在 3.1 版本发生变更:* 添加了对 [`with`]({{< ref "/reference/compound_stmts#with" >}}) 语句的支持。

*在 3.3 版本发生变更:* 添加了对 *filename* 使用 [file object]({{< ref "/glossary/idx#term-file-object" >}}) 而非实际文件名的支持。

​	添加了 `'a'` (append) 模式，以及对读取多数据流文件的支持。

*在 3.4 版本发生变更:* 添加了 `'x'` (单独创建) 模式。

*在 3.5 版本发生变更:* [`read()`]({{< ref "/library/allos/io#io.BufferedIOBase.read" >}}) 方法现在接受 `None` 作为参数。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

*在 3.9 版本发生变更:* *buffering* 形参已被移除。 它自 Python 3.0 起即被忽略并弃用。 请传入一个打开文件对象来控制文件的打开方式。

*compresslevel* 形参成为仅限关键字参数。

*在 3.10 版本发生变更:* 这个类在面对多个同时读取器和写入器时是线程安全的，就如它在 [`gzip`]({{< ref "/library/archiving/gzip#module-gzip" >}}) 和 [`lzma`]({{< ref "/library/archiving/lzma#module-lzma" >}}) 中的等价类所具有的特性一样。

## 增量压缩和解压

## *class* bz2.**BZ2Compressor**(*compresslevel=9*)

​	创建一个新的压缩器对象。 此对象可被用来执行增量数据压缩。 对于一次性压缩，请改用 [`compress()`]({{< ref "/library/archiving/bz2#bz2.compress" >}}) 函数。

​	如果给定 *compresslevel*，它必须为 `1` 至 `9` 之间的整数。 默认值为 `9`。

## **compress**(*data*)

​	向压缩器对象提供数据。 在可能的情况下返回一段已压缩数据，否则返回空字节串。

​	当你已结束向压缩器提供数据时，请调用 [`flush()`]({{< ref "/library/archiving/bz2#bz2.BZ2Compressor.flush" >}}) 方法来完成压缩进程。

## **flush**()

​	结束压缩进程，返回内部缓冲中剩余的压缩完成的数据。

​	调用此方法之后压缩器对象将不可再被使用。

## *class* bz2.**BZ2Decompressor**

​	创建一个新的解压缩器对象。 此对象可被用来执行增量数据解压缩。 对于一次性解压缩，请改用 [`decompress()`]({{< ref "/library/archiving/bz2#bz2.decompress" >}}) 函数。

​	备注

 

​	这个类不会透明地处理包含多个已压缩数据流的输入，这不同于 [`decompress()`]({{< ref "/library/archiving/bz2#bz2.decompress" >}}) 和 [`BZ2File`]({{< ref "/library/archiving/bz2#bz2.BZ2File" >}})。 如果你需要通过 [`BZ2Decompressor`]({{< ref "/library/archiving/bz2#bz2.BZ2Decompressor" >}}) 来解压缩多个数据流输入，你必须为每个数据流都使用新的解压缩器。

## **decompress**(*data*, *max_length=-1*)

​	解压缩 *data* (一个 [bytes-like object]({{< ref "/glossary/idx#term-bytes-like-object" >}}))，返回字节串形式的解压缩数据。 某些 *data* 可以在内部被缓冲，以便用于后续的 [`decompress()`]({{< ref "/library/archiving/bz2#bz2.decompress" >}}) 调用。 返回的数据应当与之前任何 [`decompress()`]({{< ref "/library/archiving/bz2#bz2.decompress" >}}) 调用的输出进行拼接。

​	如果 *max_length* 为非负数，将返回至多 *max_length* 个字节的解压缩数据。 如果达到此限制并且可以产生后续输出，则 [`needs_input`]({{< ref "/library/archiving/bz2#bz2.BZ2Decompressor.needs_input" >}}) 属性将被设为 `False`。 在这种情况下，下一次 [`decompress()`]({{< ref "/library/archiving/bz2#bz2.BZ2Decompressor.decompress" >}}) 调用提供的 *data* 可以为 `b''` 以获取更多的输出。

​	如果所有输入数据都已被解压缩并返回（或是因为它少于 *max_length* 个字节，或是因为 *max_length* 为负数），则 [`needs_input`]({{< ref "/library/archiving/bz2#bz2.BZ2Decompressor.needs_input" >}}) 属性将被设为 `True`。

​	在到达数据流末尾之后再尝试解压缩数据会引发 [`EOFError`]({{< ref "/library/exceptions#EOFError" >}})。 在数据流末尾之后获取的任何数据都会被忽略并存储至 [`unused_data`]({{< ref "/library/archiving/bz2#bz2.BZ2Decompressor.unused_data" >}}) 属性。

*在 3.5 版本发生变更:* 添加了 *max_length* 形参。

## **eof**

​	若达到了数据流的末尾标记则为 `True`。

> Added in version 3.3.
>

## **unused_data**

​	在压缩数据流的末尾之后获取的数据。

​	如果在达到数据流末尾之前访问此属性，其值将为 `b''`。

## **needs_input**

​	如果在要求新的未解压缩输入之前 [`decompress()`]({{< ref "/library/archiving/bz2#bz2.BZ2Decompressor.decompress" >}}) 方法可以提供更多的解压缩数据则为 `False`。

> Added in version 3.5.
>

## 一次性压缩或解压缩

## bz2.**compress**(*data*, *compresslevel=9*)

​	压缩 *data*，此参数为一个 [字节类对象]({{< ref "/glossary/idx#term-bytes-like-object" >}})。

​	如果给定 *compresslevel*，它必须为 `1` 至 `9` 之间的整数。 默认值为 `9`。

​	对于增量压缩，请改用 [`BZ2Compressor`]({{< ref "/library/archiving/bz2#bz2.BZ2Compressor" >}})。

## bz2.**decompress**(*data*)

​	解压缩 *data*，此参数为一个 [字节类对象]({{< ref "/glossary/idx#term-bytes-like-object" >}})。

​	如果 *data* 是多个压缩数据流的拼接，则解压缩所有数据流。

​	对于增量解压缩，请改用 [`BZ2Decompressor`]({{< ref "/library/archiving/bz2#bz2.BZ2Decompressor" >}})。

*在 3.3 版本发生变更:* 支持了多数据流的输入。



## 用法示例

​	以下是 [`bz2`]({{< ref "/library/archiving/bz2#module-bz2" >}}) 模块典型用法的一些示例。

​	使用 [`compress()`]({{< ref "/library/archiving/bz2#bz2.compress" >}}) 和 [`decompress()`]({{< ref "/library/archiving/bz2#bz2.decompress" >}}) 来显示往复式的压缩：



``` python
>>> import bz2
>>> data = b"""\
... Donec rhoncus quis sapien sit amet molestie. Fusce scelerisque vel augue
... nec ullamcorper. Nam rutrum pretium placerat. Aliquam vel tristique lorem,
... sit amet cursus ante. In interdum laoreet mi, sit amet ultrices purus
... pulvinar a. Nam gravida euismod magna, non varius justo tincidunt feugiat.
... Aliquam pharetra lacus non risus vehicula rutrum. Maecenas aliquam leo
... felis. Pellentesque semper nunc sit amet nibh ullamcorper, ac elementum
... dolor luctus. Curabitur lacinia mi ornare consectetur vestibulum."""
>>> c = bz2.compress(data)
>>> len(data) / len(c)  # Data compression ratio
1.513595166163142
>>> d = bz2.decompress(c)
>>> data == d  # Check equality to original object after round-trip
True
```

​	使用 [`BZ2Compressor`]({{< ref "/library/archiving/bz2#bz2.BZ2Compressor" >}}) 进行增量压缩：



``` python
>>> import bz2
>>> def gen_data(chunks=10, chunksize=1000):
...     """Yield incremental blocks of chunksize bytes."""
...     for _ in range(chunks):
...         yield b"z" * chunksize
...
>>> comp = bz2.BZ2Compressor()
>>> out = b""
>>> for chunk in gen_data():
...     # Provide data to the compressor object
...     out = out + comp.compress(chunk)
...
>>> # Finish the compression process.  Call this once you have
>>> # finished providing data to the compressor.
>>> out = out + comp.flush()
```

​	上面的示例使用了一个相当 "非随机" 的数据流（即 `b"z"` 块的数据流）。 随机数据的压缩率通常很差，而有序、重复的数据通常会产生很高的压缩率。

​	用二进制模式写入和读取 bzip2 压缩文件：



``` python
>>> import bz2
>>> data = b"""\
... Donec rhoncus quis sapien sit amet molestie. Fusce scelerisque vel augue
... nec ullamcorper. Nam rutrum pretium placerat. Aliquam vel tristique lorem,
... sit amet cursus ante. In interdum laoreet mi, sit amet ultrices purus
... pulvinar a. Nam gravida euismod magna, non varius justo tincidunt feugiat.
... Aliquam pharetra lacus non risus vehicula rutrum. Maecenas aliquam leo
... felis. Pellentesque semper nunc sit amet nibh ullamcorper, ac elementum
... dolor luctus. Curabitur lacinia mi ornare consectetur vestibulum."""
>>> with bz2.open("myfile.bz2", "wb") as f:
...     # Write compressed data to file
...     unused = f.write(data)
...
>>> with bz2.open("myfile.bz2", "rb") as f:
...     # Decompress data from file
...     content = f.read()
...
>>> content == data  # Check equality to original object after round-trip
True
```
