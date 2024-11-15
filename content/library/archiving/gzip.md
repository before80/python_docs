+++
title = "gzip --- 对 gzip 文件的支持"
date = 2024-11-15T12:00:20+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/gzip.html](https://docs.python.org/zh-cn/3.13/library/gzip.html)
>
> 收录该文档的时间：`2024-11-15T12:00:20+08:00`

# `gzip` --- 对 **gzip** 文件的支持

**源代码：** [Lib/gzip.py](https://github.com/python/cpython/tree/3.13/Lib/gzip.py)

------

​	此模块提供的简单接口帮助用户压缩和解压缩文件，功能类似于 GNU 应用程序 **gzip** 和 **gunzip**。

​	数据压缩由 [`zlib`](https://docs.python.org/zh-cn/3.13/library/zlib.html#module-zlib) 模块提供。

[`gzip`](https://docs.python.org/zh-cn/3.13/library/gzip.html#module-gzip) 模块提供 [`GzipFile`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.GzipFile) 类和 [`open()`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.open)、[`compress()`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.compress)、[`decompress()`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.decompress) 几个便利的函数。[`GzipFile`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.GzipFile) 类可以读写 **gzip** 格式的文件，还能自动压缩和解压缩数据，这让操作压缩文件如同操作普通的 [file object](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-object) 一样方便。

​	注意，此模块不支持部分可以被 **gzip** 和 **gunzip** 解压的格式，如利用 **compress** 或 **pack** 压缩所得的文件。

​	这个模块定义了以下内容：

## gzip.**open**(*filename*, *mode='rb'*, *compresslevel=9*, *encoding=None*, *errors=None*, *newline=None*)

​	以二进制方式或者文本方式打开一个 gzip 格式的压缩文件，返回一个 [file object](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-object)。

*filename* 参数可以是一个实际的文件名（一个 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 对象或者 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 对象），或者是一个用来读写的已存在的文件对象。

*mode* 参数可以是二进制模式： `'r'`, `'rb'`, `'a'`, `'ab'`, `'w'`, `'wb'`, `'x'` or `'xb'` , 或者是文本模式 `'rt'`, `'at'`, `'wt'`, or `'xt'`。默认值是 `'rb'`。

​	The *compresslevel* argument is an integer from 0 to 9, as for the [`GzipFile`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.GzipFile) constructor.

​	对于二进制模式，这个函数等价于 [`GzipFile`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.GzipFile) 构造器：`GzipFile(filename, mode, compresslevel)`。在这个例子中，*encoding*, *errors* 和 *newline* 三个参数一定不要设置。

​	对于文本模式，将会创建一个 [`GzipFile`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.GzipFile) 对象，并将它封装到一个 [`io.TextIOWrapper`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOWrapper) 实例中， 这个实例默认了指定编码，错误抓获行为和行。

*在 3.3 版本发生变更:* 支持 *filename* 为一个文件对象，支持文本模式和 *encoding*, *errors* 和 *newline* 参数。

*在 3.4 版本发生变更:* 支持 `'x'`, `'xb'` 和 `'xt'` 三种模式。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## *exception* gzip.**BadGzipFile**

​	针对无效 gzip 文件引发的异常。 它继承自 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError)。 针对无效 gzip 文件也可能引发 [`EOFError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#EOFError) 和 [`zlib.error`](https://docs.python.org/zh-cn/3.13/library/zlib.html#zlib.error)。

*Added in version 3.8.*

## *class* gzip.**GzipFile**(*filename=None*, *mode=None*, *compresslevel=9*, *fileobj=None*, *mtime=None*)

[`GzipFile`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.GzipFile) 类的构造器，它模拟了 [file object](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-object) 的大部分方法，但 [`truncate()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.truncate) 方法除外。 *fileobj* 和 *filename* 中至少有一个必须为非空值。

​	新的实例基于 *fileobj*，它可以是一个普通文件，一个 [`io.BytesIO`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BytesIO) 对象，或者任何一个与文件相似的对象。当 *filename* 是一个文件对象时，它的默认值是 `None`。

​	当 *fileobj* 为 `None` 时， *filename* 参数只用于 **gzip** 文件头中，这个文件有可能包含未压缩文件的源文件名。如果文件可以被识别，默认 *fileobj* 的文件名；否则默认为空字符串，在这种情况下文件头将不包含源文件名。

*mode* 参数可以是 `'r'`, `'rb'`, `'a'`, `'ab'`, `'w'`, `'wb'`, `'x'` 或 `'xb'` 中的一个，具体取决于文件将被读取还是被写入。 如果可识别则默认为 *fileobj* 的模式；否则默认为 `'rb'`。 在未来的 Python 发布版中将不再使用 *fileobj* 的模式。 最好总是指定 *mode* 为写入模式。

​	需要注意的是，文件默认使用二进制模式打开。 如果要以文本模式打开一个压缩文件，请使用 [`open()`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.open) 方法 (或者使用 [`io.TextIOWrapper`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOWrapper) 包装 [`GzipFile`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.GzipFile))。

*compresslevel* 参数是一个从 `0` 到 `9` 的整数，用于控制压缩等级；`1` 最快但压缩比例最小，`9` 最慢但压缩比例最大。 `0` 不压缩。默认为 `9`。

​	可选的 *mtime* 参数是 gzip 所请求的时间戳。 该时间为 Unix 格式，即距离 1970-01-01 00:00:00 UTC 的秒数。 如果 *mtime* 被省略或为 `None`，则会使用当前时间。 使用 *mtime* = 0 可生成不依赖于创建时间的压缩流。

​	有关在解压缩时设置的 [`mtime`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.GzipFile.mtime) 属性见下文。

​	调用 [`GzipFile`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.GzipFile) 对象的 `close()` 方法不会关闭 *fileobj*，因为你可能希望增加其它内容到已经缩的数据中。 你还可以传入一个 [`io.BytesIO`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BytesIO) 对象作为 *fileobj* 打开，并使用 [`io.BytesIO`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BytesIO) 对象的 [`getvalue()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BytesIO.getvalue) 方法提取所得到的内存缓冲区数据。

[`GzipFile`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.GzipFile) 支持 [`io.BufferedIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase) 接口，包括迭代和 [`with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#with) 语句。 只有 [`truncate()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.truncate) 方法未被实现。

[`GzipFile`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.GzipFile) 还提供了以下的方法和属性:

## **peek**(*n*)

​	在不移动文件指针的情况下读取 *n* 个未压缩字节。最多只有一个单独的读取流来服务这个方法调用。返回的字节数不一定刚好等于要求的数量。

​	备注

 

​	调用 [`peek()`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.GzipFile.peek) 并没有改变 [`GzipFile`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.GzipFile) 的文件指针，它可能改变潜在文件对象(例如： [`GzipFile`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.GzipFile) 使用 *fileobj* 参数进行初始化)。

*Added in version 3.2.*

## **mode**

`'rb'` 表示可读而 `'wb'` 表示可写。

*在 3.13 版本发生变更:* 在之前版本中该值为整数 `1` 或 `2`。

## **mtime**

​	当解压缩时，该属性将被设为最近读取标头的末尾时间戳。 它是一个整数，保存从 Unix 纪元 (1970 -01-01 00:00:00 UTC) 开始的秒数。 在读取任何标头之前的初始值为 `None`。

## **name**

​	指向磁盘上 gzip 文件的路径，为 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 或 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 对象。 等价于原始输入路径上 [`os.fspath()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.fspath) 的输出，不带其他标准化、解析或扩展。

*在 3.1 版本发生变更:* 支持 [`with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#with) 语句，构造器参数 *mtime* 和 [`mtime`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.GzipFile.mtime) 属性。

*在 3.2 版本发生变更:* 添加了对零填充和不可搜索文件的支持。

*在 3.3 版本发生变更:* 实现 [`io.BufferedIOBase.read1()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase.read1) 方法。

*在 3.4 版本发生变更:* 支持 `'x'` and `'xb'` 两种模式。

*在 3.5 版本发生变更:* 支持写入任意 [bytes-like objects](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object)。[`read()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase.read) 方法可以接受 `None` 为参数。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

*自 3.9 版本弃用:* 打开 [`GzipFile`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.GzipFile) 用于写入而不指定 *mode* 参数的做法已被弃用。

*在 3.12 版本发生变更:* 移除 `filename` 属性，改用 [`name`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.GzipFile.name) 属性。

## gzip.**compress**(*data*, *compresslevel=9*, ***, *mtime=None*)

​	压缩 *data*，返回一个包含压缩数据的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 对象。 *compresslevel* 和 *mtime* 的含义与上文中 [`GzipFile`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.GzipFile) 构造器的相同。

*Added in version 3.2.*

*在 3.8 版本发生变更:* 添加了 *mtime* 形参用于可重复的输出。

*在 3.11 版本发生变更:* 速度的提升是通过一次性压缩所有数据代替流的方式来达成的。 将 *mtime* 设为 `0` 的调用被委托给 [`zlib.compress()`](https://docs.python.org/zh-cn/3.13/library/zlib.html#zlib.compress) 以加快速度。 在此情况下输出可能包含一个 gzip 标头 "OS" 字节值而不是下层 zlib 实现所提供的 255 "unknown"。

*在 3.13 版本发生变更:* 当使用此函数时 gzip 标头 OS 字节会保证如在 3.10 和更早版本中一样被设为 255。

## gzip.**decompress**(*data*)

​	解压缩 *data*，返回一个包含已解压数据的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 对象。 此函数可以解压缩多成员的 gzip 数据（即多个 gzip 块拼接在一起）。 当数据确定只包含一个成员时则 *wbits* 设为 31 的 [`zlib.decompress()`](https://docs.python.org/zh-cn/3.13/library/zlib.html#zlib.decompress) 函数更快一些。

*Added in version 3.2.*

*在 3.11 版本发生变更:* 通过一次性解压缩全部数据而不是通过流方式提高了速度。



## 用法示例

​	读取压缩文件示例：

```
import gzip
with gzip.open('/home/joe/file.txt.gz', 'rb') as f:
    file_content = f.read()
```

​	创建GZIP 文件示例：

```
import gzip
content = b"Lots of content here"
with gzip.open('/home/joe/file.txt.gz', 'wb') as f:
    f.write(content)
```

​	使用 GZIP 压缩已有的文件示例：

```
import gzip
import shutil
with open('/home/joe/file.txt', 'rb') as f_in:
    with gzip.open('/home/joe/file.txt.gz', 'wb') as f_out:
        shutil.copyfileobj(f_in, f_out)
```

​	使用 GZIP 压缩二进制字符串示例：

```
import gzip
s_in = b"Lots of content here"
s_out = gzip.compress(s_in)
```

​	参见

## 模块 [`zlib`](https://docs.python.org/zh-cn/3.13/library/zlib.html#module-zlib)

​	支持 **gzip** 格式所需要的基本压缩模块。



## 命令行界面

[`gzip`](https://docs.python.org/zh-cn/3.13/library/gzip.html#module-gzip) 模块提供了简单的命令行界面用于压缩和解压缩文件。

​	在执行后 [`gzip`](https://docs.python.org/zh-cn/3.13/library/gzip.html#module-gzip) 模块会保留输入文件。

*在 3.8 版本发生变更:* 添加一个带有用法说明的新命令行界面命令。 默认情况下，当你要执行 CLI 时，默认压缩等级为 6。

### 命令行选项

## **file**

​	如果未指定 *file*，则从 [`sys.stdin`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stdin) 读取。

## **--fast**

​	指明最快速的压缩方法（较低压缩率）。

## **--best**

​	指明最慢速的压缩方法（最高压缩率）。

## **-d**, **--decompress**

​	解压缩给定的文件。

## **-h**, **--help**

​	显示帮助消息。
