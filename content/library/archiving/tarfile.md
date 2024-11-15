+++
title = "tarfile --- 读写 tar 归档文件"
date = 2024-11-15T12:00:20+08:00
weight = 50
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/tarfile.html](https://docs.python.org/zh-cn/3.13/library/tarfile.html)
>
> 收录该文档的时间：`2024-11-15T12:00:20+08:00`

# `tarfile` --- 读写 tar 归档文件

**源代码:** [Lib/tarfile.py](https://github.com/python/cpython/tree/3.13/Lib/tarfile.py)

------

[`tarfile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#module-tarfile) 模块可以用来读写 tar 归档，包括使用 gzip, bz2 和 lzma 压缩的归档。 请使用 [`zipfile`](https://docs.python.org/zh-cn/3.13/library/zipfile.html#module-zipfile) 模块来读写 `.zip` 文件，或者使用 [shutil](https://docs.python.org/zh-cn/3.13/library/shutil.html#archiving-operations) 的高层级函数。

​	一些事实和数字:

- 读写 [`gzip`](https://docs.python.org/zh-cn/3.13/library/gzip.html#module-gzip), [`bz2`](https://docs.python.org/zh-cn/3.13/library/bz2.html#module-bz2) 和 [`lzma`](https://docs.python.org/zh-cn/3.13/library/lzma.html#module-lzma) 解压的归档要求相应的模块可用。
- 支持读取 / 写入 POSIX.1-1988 (ustar) 格式。
- 对 GNU tar 格式的读/写支持，包括 *longname* 和 *longlink* 扩展，对所有种类 *sparse* 扩展的只读支持，包括 sparse 文件的恢复。
- 对 POSIX.1-2001 (pax) 格式的读/写支持。
- 处理目录、正常文件、硬链接、符号链接、fifo 管道、字符设备和块设备，并且能够获取和恢复文件信息例如时间戳、访问权限和所有者等。

*在 3.3 版本发生变更:* 添加了对 [`lzma`](https://docs.python.org/zh-cn/3.13/library/lzma.html#module-lzma) 压缩的支持。

*在 3.12 版本发生变更:* 归档文件使用 [过滤器](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile-extraction-filter) 来提取，这将可以限制令人惊讶/危险的特性，或确认它们符合预期并且归档文档受到完全信任。 在默认情况下，归档文档将受到完全信任，但此默认选项已被弃用并计划在 Python 3.14 中改变。

## tarfile.**open**(*name=None*, *mode='r'*, *fileobj=None*, *bufsize=10240*, ***kwargs*)

​	针对路径名 *name* 返回 [`TarFile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile) 对象。 有关 [`TarFile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile) 对象以及所允许的关键字参数的详细信息请参阅 [TarFile 对象](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile-objects)。

*mode* 必须是 `'filemode[:compression]'` 形式的字符串，其默认值为 `'r'`。 以下是模式组合的完整列表:

| 模式            | action                                                       |
| :-------------- | :----------------------------------------------------------- |
| `'r' or 'r:*'`  | 打开和读取使用透明压缩（推荐）。                             |
| `'r:'`          | 打开和读取不使用压缩。                                       |
| `'r:gz'`        | 打开和读取使用gzip 压缩。                                    |
| `'r:bz2'`       | 打开和读取使用bzip2 压缩。                                   |
| `'r:xz'`        | 打开和读取使用lzma 压缩。                                    |
| `'x'` 或 `'x:'` | 单独创建一个 tarfile 而不带压缩。 如果它已经存在则会引发 [`FileExistsError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#FileExistsError) 异常。 |
| `'x:gz'`        | 使用 gzip 压缩创建一个 tarfile。 如果它已经存在则会引发 [`FileExistsError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#FileExistsError) 异常。 |
| `'x:bz2'`       | 使用 bzip2 压缩创建一个 tarfile。 如果它已经存在则会引发 [`FileExistsError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#FileExistsError) 异常。 |
| `'x:xz'`        | 使用 lzma 压缩创建一个 tarfile。 如果它已经存在则会引发 [`FileExistsError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#FileExistsError) 异常。 |
| `'a' or 'a:'`   | 打开以便在没有压缩的情况下追加。如果文件不存在，则创建该文件。 |
| `'w' or 'w:'`   | 打开用于未压缩的写入。                                       |
| `'w:gz'`        | 打开用于 gzip 压缩的写入。                                   |
| `'w:bz2'`       | 打开用于 bzip2 压缩的写入。                                  |
| `'w:xz'`        | 打开用于 lzma 压缩的写入。                                   |

​	请注意 `'a:gz'`, `'a:bz2'` 或 `'a:xz'` 是不可能的组合。 如果 *mode* 不适用于打开特定（压缩的）文件用于读取，则会引发 [`ReadError`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.ReadError)。 请使用 *mode* `'r'` 来避免这种情况。 如果某种压缩方法不受支持，则会引发 [`CompressionError`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.CompressionError)。

​	如果指定了 *fileobj*，它会被用作对应于 *name* 的以二进制模式打开的 [file object](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-object) 的替代。 它会被设定为处在位置 0。

​	对于 `'w:gz'`, `'x:gz'`, `'w|gz'`, `'w:bz2'`, `'x:bz2'`, `'w|bz2'` 等模式，[`tarfile.open()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.open) 接受关键字参数 *compresslevel* (默认值为 `9`) 用于指定文件的压缩等级。

​	对于 `'w:xz'` 和 `'x:xz'` 模式，[`tarfile.open()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.open) 接受关键字参数 *preset* 来指定文件的压缩等级。

​	针对特殊的目的，还存在第二种 *mode* 格式: `'filemode|[compression]'`。 [`tarfile.open()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.open) 将返回一个将其数据作为数据块流来处理的 [`TarFile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile) 对象。 对此文件将不能执行随机查找。 如果给定了 *fileobj*，它可以是任何具有 [`read()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase.read) 或 [`write()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase.write) 方法（由 *mode* 确定）的对象。 *bufsize* 指定块大小，默认为 `20 * 512` 字节。 可与此格式组合使用的有 `sys.stdin.buffer`、套接字 [file object](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-object) 或磁盘设备等。 但是，这样的 [`TarFile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile) 对象存在不允许随机访问的限制，参见 [例子](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tar-examples)。 当前可用的模式有：

| 模式      | 动作                                          |
| :-------- | :-------------------------------------------- |
| `'r|*'`   | 打开 tar 块的 *流* 以进行透明压缩读取。       |
| `'r|'`    | 打开一个未压缩的 tar 块的 *stream* 用于读取。 |
| `'r|gz'`  | 打开一个 gzip 压缩的 *stream* 用于读取。      |
| `'r|bz2'` | 打开一个 bzip2 压缩的 *stream* 用于读取。     |
| `'r|xz'`  | 打开一个 lzma 压缩 *stream* 用于读取。        |
| `'w|'`    | 打开一个未压缩的 *stream* 用于写入。          |
| `'w|gz'`  | 打开一个 gzip 压缩的 *stream* 用于写入。      |
| `'w|bz2'` | 打开一个 bzip2 压缩的 *stream* 用于写入。     |
| `'w|xz'`  | 打开一个 lzma 压缩的 *stream* 用于写入。      |

*在 3.5 版本发生变更:* 添加了 `'x'` (单独创建) 模式。

*在 3.6 版本发生变更:* *name* 形参接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

*在 3.12 版本发生变更:* *compresslevel* 关键字参数也适用于流式数据。

## *class* tarfile.**TarFile**

​	用于读取和写入 tar 归档的类。 请不要直接使用这个类：而要使用 [`tarfile.open()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.open)。 参见 [TarFile 对象](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile-objects)。

## tarfile.**is_tarfile**(*name*)

​	如果 *name* 是一个 [`tarfile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#module-tarfile) 能读取的 tar 归档文件则返回 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)。 *name* 可以为 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)，文件或文件型对象。

*在 3.9 版本发生变更:* 支持文件或类文件对象。

[`tarfile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#module-tarfile) 模块定义了以下异常:

## *exception* tarfile.**TarError**

​	所有 [`tarfile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#module-tarfile) 异常的基类。

## *exception* tarfile.**ReadError**

​	当一个不能被 [`tarfile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#module-tarfile) 模块处理或者因某种原因而无效的 tar 归档被打开时将被引发。

## *exception* tarfile.**CompressionError**

​	当一个压缩方法不受支持或者当数据无法被正确解码时将被引发。

## *exception* tarfile.**StreamError**

​	当达到流式 [`TarFile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile) 对象的典型限制时将被引发。

## *exception* tarfile.**ExtractError**

​	当使用 [`TarFile.extract()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extract) 时针对 *non-fatal* 所引发的异常，但是仅限 [`TarFile.errorlevel`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.errorlevel)`== 2`。

## *exception* tarfile.**HeaderError**

​	如果获取的缓冲区无效则会由 [`TarInfo.frombuf()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo.frombuf) 引发的异常。

## *exception* tarfile.**FilterError**

​	被过滤器 [拒绝](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile-extraction-refuse) 的成员的基类。

## **tarinfo**

​	关于过滤器拒绝提取的成员的信息，为 [TarInfo](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarinfo-objects) 类型。

## *exception* tarfile.**AbsolutePathError**

​	在拒绝提取具有绝对路径的成员时引发。

## *exception* tarfile.**OutsideDestinationError**

​	在拒绝提取目标目录以外的成员时引发。

## *exception* tarfile.**SpecialFileError**

​	在拒绝提取特殊文件（例如设备或管道）时引发。

## *exception* tarfile.**AbsoluteLinkError**

​	在拒绝提取具有绝对路径的符号链接时引发。

## *exception* tarfile.**LinkOutsideDestinationError**

​	在拒绝提取指向目标目录以外的符号链接时引发。

​	以下常量在模块层级上可用:

## tarfile.**ENCODING**

​	默认的字符编码格式：在 Windows 上为 `'utf-8'`，其他系统上则为 [`sys.getfilesystemencoding()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.getfilesystemencoding) 所返回的值。

## tarfile.**REGTYPE**

## tarfile.**AREGTYPE**

​	常规文件 [`type`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo.type)。

## tarfile.**LNKTYPE**

​	（tar 文件中的）链接 [`type`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo.type)。

## tarfile.**SYMTYPE**

​	符号链接 [`type`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo.type)。

## tarfile.**CHRTYPE**

​	字符特殊设备 [`type`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo.type)。

## tarfile.**BLKTYPE**

​	块特殊设备 [`type`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo.type)。

## tarfile.**DIRTYPE**

​	目录 [`type`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo.type)。

## tarfile.**FIFOTYPE**

​	FIFO 特殊设备 [`type`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo.type)。

## tarfile.**CONTTYPE**

​	连续文件 [`type`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo.type)。

## tarfile.**GNUTYPE_LONGNAME**

​	GNU tar 长名称 [`type`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo.type)。

## tarfile.**GNUTYPE_LONGLINK**

​	GNU tar 长链接 [`type`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo.type)。

## tarfile.**GNUTYPE_SPARSE**

​	A GNU tar 离散文件 [`type`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo.type)。

​	以下常量各自定义了一个 [`tarfile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#module-tarfile) 模块能够创建的 tar 归档格式。 相关细节请参阅 [受支持的 tar 格式](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tar-formats) 小节。

## tarfile.**USTAR_FORMAT**

​	POSIX.1-1988 (ustar) 格式。

## tarfile.**GNU_FORMAT**

​	GNU tar 格式。

## tarfile.**PAX_FORMAT**

​	POSIX.1-2001 (pax) 格式。

## tarfile.**DEFAULT_FORMAT**

​	用于创建归档的默认格式。 目前为 [`PAX_FORMAT`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.PAX_FORMAT)。

*在 3.8 版本发生变更:* 新归档的默认格式已更改为 [`PAX_FORMAT`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.PAX_FORMAT) 而不再是 [`GNU_FORMAT`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.GNU_FORMAT)。

​	参见

## 模块 [`zipfile`](https://docs.python.org/zh-cn/3.13/library/zipfile.html#module-zipfile)

[`zipfile`](https://docs.python.org/zh-cn/3.13/library/zipfile.html#module-zipfile) 标准模块的文档。

## [归档操作](https://docs.python.org/zh-cn/3.13/library/shutil.html#archiving-operations)

​	标准 [`shutil`](https://docs.python.org/zh-cn/3.13/library/shutil.html#module-shutil) 模块所提供的高层级归档工具的文档。

## [GNU tar manual, Basic Tar Format](https://www.gnu.org/software/tar/manual/html_node/Standard.html)

​	针对 tar 归档文件的文档，包含 GNU tar 扩展。



## TarFile 对象

[`TarFile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile) 对象提供了一个 tar 归档的接口。 一个 tar 归档就是数据块的序列。 一个归档成员（被保存文件）是由一个标头块加多个数据块组成的。 一个文件可以在一个 tar 归档中多次被保存。 每个归档成员都由一个 [`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 对象来代表，详情参见 [TarInfo 对象](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarinfo-objects)。

[`TarFile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile) 对象可在 [`with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#with) 语句中作为上下文管理器使用。 当语句块结束时它将自动被关闭。 请注意在发生异常事件时被打开用于写入的归档将不会被终结；只有内部使用的文件对象将被关闭。 相关用例请参见 [例子](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tar-examples)。

*Added in version 3.2:* 添加了对上下文管理器协议的支持。

## *class* tarfile.**TarFile**(*name=None*, *mode='r'*, *fileobj=None*, *format=DEFAULT_FORMAT*, *tarinfo=TarInfo*, *dereference=False*, *ignore_zeros=False*, *encoding=ENCODING*, *errors='surrogateescape'*, *pax_headers=None*, *debug=0*, *errorlevel=1*, *stream=False*)

​	下列所有参数都是可选项并且也可作为实例属性来访问。

*name* 是归档的路径名。 *name* 可以是一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。 如果给定了 *fileobj* 则它可以被省略。 在此情况下，如果对象存在 `name` 属性则将使用它。

*mode* 可以为 `'r'` 表示从现有归档读取，`'a'` 表示将数据追加到现有文件，`'w'` 表示创建新文件覆盖现有文件，或者 `'x'` 表示仅在文件不存在时创建新文件。

​	如果给定了 *fileobj*，它会被用于读取或写入数据。 如果可以被确定，则 *mode* 会被 *fileobj* 的模式所覆盖。 *fileobj* 的使用将从位置 0 开始。

​	备注

 

​	当 [`TarFile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile) 被关闭时，*fileobj* 不会被关闭。

*format* 控制用于写入的归档格式。 它必须为在模块层级定义的常量 [`USTAR_FORMAT`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.USTAR_FORMAT), [`GNU_FORMAT`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.GNU_FORMAT) 或 [`PAX_FORMAT`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.PAX_FORMAT) 中的一个。 当读取时，格式将被自动检测，即使单个归档中存在不同的格式。

*tarinfo* 参数可以被用来将默认的 [`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 类替换为另一个。

​	如果 *dereference* 为 [`False`](https://docs.python.org/zh-cn/3.13/library/constants.html#False)，则会将符号链接和硬链接添加到归档中。 如果为 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)，则会将目标文件的内容添加到归档中。 在不支持符号链接的系统上参数将不起作用。

​	如果 *ignore_zeros* 为 [`False`](https://docs.python.org/zh-cn/3.13/library/constants.html#False)，则会将空的数据块当作归档的末尾来处理。 如果为 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)，则会跳过空的（和无效的）数据块并尝试获取尽可能多的成员。 此参数仅适用于读取拼接的或损坏的归档。

*debug* 可设为从 `0` (无调试消息) 到 `3` (全部调试消息)。 消息会被写入到 `sys.stderr`。

*errorlevel* 控制如何处理解压错误，参见 [`相应的属性`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.errorlevel)。

*encoding* 和 *errors* 参数定义了读取或写入归档所使用的字符编码格式以及要如何处理转换错误。 默认设置将适用于大多数用户。 要深入了解详情可参阅 [Unicode 问题](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tar-unicode) 小节。

​	可选的 *pax_headers* 参数是字符串的字典，如果 *format* 为 [`PAX_FORMAT`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.PAX_FORMAT) 它将被作为 pax 全局标头被添加。

​	如果 *stream* 被设为 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True) 则在读取时有关归档中文件的归档信息不会被缓存，以节省内存消耗。

*在 3.2 版本发生变更:* 使用 `'surrogateescape'` 作为 *errors* 参数的默认值。

*在 3.5 版本发生变更:* 添加了 `'x'` (单独创建) 模式。

*在 3.6 版本发生变更:* *name* 形参接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

*在 3.13 版本发生变更:* 增加了 *stream* 形参。

## *classmethod* TarFile.**open**(*...*)

​	作为替代的构造器。 [`tarfile.open()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.open) 函数实际上是这个类方法的快捷方式。

## TarFile.**getmember**(*name*)

​	返回成员 *name* 的 [`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 对象。 如果 *name* 在归档中找不到，则会引发 [`KeyError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#KeyError)。

​	备注

 

​	如果一个成员在归档中出现超过一次，它的最后一次出现会被视为是最新的版本。

## TarFile.**getmembers**()

​	以 [`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 对象列表的形式返回归档的成员。 列表的顺序与归档中成员的顺序一致。

## TarFile.**getnames**()

​	以名称列表的形式返回成员。 它的顺序与 [`getmembers()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.getmembers) 所返回列表的顺序一致。

## TarFile.**list**(*verbose=True*, ***, *members=None*)

​	将内容清单打印到 `sys.stdout`。 如果 *verbose* 为 [`False`](https://docs.python.org/zh-cn/3.13/library/constants.html#False)，则将只打印成员名称。 如果为 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)，则输出将类似于 **ls -l** 的输出效果。 如果给定了可选的 *members*，它必须为 [`getmembers()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.getmembers) 所返回的列表的一个子集。

*在 3.5 版本发生变更:* 添加了 *members* 形参。

## TarFile.**next**()

​	当 [`TarFile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile) 被打开用于读取时，以 [`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 对象的形式返回归档的下一个成员。 如果不再有可用对象则返回 [`None`](https://docs.python.org/zh-cn/3.13/library/constants.html#None)。

## TarFile.**extractall**(*path='.'*, *members=None*, ***, *numeric_owner=False*, *filter=None*)

​	将归档中的所有成员提取到当前工作目录或 *path* 目录。 如果给定了可选的 *members*，则它必须为 [`getmembers()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.getmembers) 所返回的列表的一个子集。 字典信息例如所有者、修改时间和权限会在所有成员提取完毕后被设置。 这样做是为了避免两个问题：目录的修改时间会在每当在其中创建文件时被重置。 并且如果目录的权限不允许写入，提取文件到目录的操作将失败。

​	如果 *numeric_owner* 为 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)，则将使用来自 tarfile 的 uid 和 gid 数值来设置被提取文件的所有者/用户组。 在其他情况下，则会使用来自 tarfile 的名称值。

*filter* 参数指明在提取之前要如何修改或拒绝 `members`。 请参阅 [解压缩过滤器](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile-extraction-filter) 了解详情。 建议应根据你需要支持的 *tar* 特征显式地设置该参数。

​	警告

 

​	绝不要未经预先检验就从不可靠的源中提取归档文件。 这样有可能在 *path* 之外创建文件，例如某些成员具有以 `"/"` 开始的绝对路径文件名或带有两个点号 `".."` 的文件名。

​	设置 `filter='data'` 来防止最危险的安全问题，并请参阅 [解压缩过滤器](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile-extraction-filter) 一节了解详情。section for details.

*在 3.5 版本发生变更:* 添加了 *numeric_owner* 形参。

*在 3.6 版本发生变更:* *path* 形参接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

*在 3.12 版本发生变更:* 添加了 *filter* 形参。

## TarFile.**extract**(*member*, *path=''*, *set_attrs=True*, ***, *numeric_owner=False*, *filter=None*)

​	从归档中提取出一个成员放入当前工作目录，将使用其完整名称。 成员的文件信息会尽可能精确地被提取。 *member* 可以是一个文件名或 [`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 对象。 你可以使用 *path* 指定一个不同的目录。 *path* 可以是一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。 将会设置文件属性 (owner, mtime, mode) 除非 *set_attrs* 为假值。

*numeric_owner* 和 *filter* 参数与 [`extractall()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extractall) 中的相同。

​	备注

 

[`extract()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extract) 方法不会处理某些提取问题。 在大多数情况下你应当考虑使用 [`extractall()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extractall) 方法。

​	警告

 

​	查看 [`extractall()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extractall) 的警告信息。

​	设置 `filter='data'` 来防止最危险的安全问题，并请参阅 [解压缩过滤器](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile-extraction-filter) 一节了解详情。section for details.

*在 3.2 版本发生变更:* 添加了 *set_attrs* 形参。

*在 3.5 版本发生变更:* 添加了 *numeric_owner* 形参。

*在 3.6 版本发生变更:* *path* 形参接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

*在 3.12 版本发生变更:* 添加了 *filter* 形参。

## TarFile.**extractfile**(*member*)

​	将归档中的一个成员提取为文件对象。 *member* 可以是一个文件名或 [`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 对象。 如果 *member* 是一个常规文件或链接，则会返回一个 [`io.BufferedReader`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedReader) 对象。 对于所有其他现有成员，则都将返回 [`None`](https://docs.python.org/zh-cn/3.13/library/constants.html#None)。 如果 *member* 未在归档中出现，则会引发 [`KeyError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#KeyError)。

*在 3.3 版本发生变更:* 返回一个 [`io.BufferedReader`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedReader) 对象。

*在 3.13 版本发生变更:* 返回的 [`io.BufferedReader`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedReader) 对象具有 `mode` 属性并且总是会等于 `'rb'`。

## TarFile.**errorlevel***: [int](https://docs.python.org/zh-cn/3.13/library/functions.html#int)*

​	如果 *errorlevel* 为 `0`，则在使用 [`TarFile.extract()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extract) 和 [`TarFile.extractall()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extractall) 时错误会被忽略。 不过，当 *debug* 大于 0 时它们将会作为错误消息在调试输出中出现。 如果 *errorlevel\*为 ``1`` (默认值)，则所有 \*fatal* 错误都会作为 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 或 [`FilterError`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.FilterError) 异常被引发。 如果为 `2`，则所有 *non-fatal* 错误也会作为 [`TarError`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarError) 异常被引发。

​	某些异常，如参数类型错误或数据损坏导致的异常，总是会被触发。

​	自定义 [提取过滤器](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile-extraction-filter) 应针对 *fatal* 错误引发 [`FilterError`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.FilterError)，针对 *non-fatal* 错误引发 [`ExtractError`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.ExtractError)。

​	请注意，当出现异常时，存档可能会被部分提取。用需要户负责进行清理。

## TarFile.**extraction_filter**

*Added in version 3.12.*

​	被用作 [`extract()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extract) 和 [`extractall()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extractall) 的 *filter* 参数的默认值的 [提取过滤器](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile-extraction-filter)。

​	该属性可以为 `None` 或是一个可调用对象。 与 [`extract()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extract) 的 *filter* 参数不同，该属性不允许使用字符串名称。

​	如果 `extraction_filter` 为 `None` (默认值)，则不带 *filter* 参数调用提取方法将引发 `DeprecationWarning`，并回退至 [`fully_trusted`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.fully_trusted_filter) 过滤器，其危险行为与之前版本的 Python 一致。

​	在 Python 3.14+ 中，保持 `extraction_filter=None` 将导致提取方法默认使用 [`data`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.data_filter) 过滤器。

​	The attribute may be set on instances or overridden in subclasses. It also is possible to set it on the `TarFile` class itself to set a global default, although, since it affects all uses of *tarfile*, it is best practice to only do so in top-level applications or [`site configuration`](https://docs.python.org/zh-cn/3.13/library/site.html#module-site). To set a global default this way, a filter function needs to be wrapped in [`staticmethod()`](https://docs.python.org/zh-cn/3.13/library/functions.html#staticmethod) to prevent injection of a `self` argument.

## TarFile.**add**(*name*, *arcname=None*, *recursive=True*, ***, *filter=None*)

​	将文件 *name* 添加到归档。 *name* 可以为任意类型的文件（目录、fifo、符号链接等等）。 如果给出 *arcname* 则它将为归档中的文件指定一个替代名称。 默认情况下会递归地添加目录。 这可以通过将 *recursive* 设为 [`False`](https://docs.python.org/zh-cn/3.13/library/constants.html#False) 来避免。 递归操作会按排序顺序添加条目。 如果给定了 *filter*，它应当为一个接受 [`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 对象并返回已修改 [`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 对象的函数。 如果它返回 [`None`](https://docs.python.org/zh-cn/3.13/library/constants.html#None) 则 [`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 对象将从归档中被排除。 具体示例参见 [例子](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tar-examples)。

*在 3.2 版本发生变更:* 添加了 *filter* 形参。

*在 3.7 版本发生变更:* 递归操作按排序顺序添加条目。

## TarFile.**addfile**(*tarinfo*, *fileobj=None*)

​	将 [`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 对象 *tarinfo* 添加到归档中。 如果 *tarinfo* 代表一个大小不为零的常规文件，则 *fileobj* 参数应为一个 [binary file](https://docs.python.org/zh-cn/3.13/glossary.html#term-binary-file)，且会从中读取 `tarinfo.size` 个字节并添加到归档中。 你可以直接创建 [`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 对象，或者也可以使用 [`gettarinfo()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.gettarinfo)。

*在 3.13 版本发生变更:* 对于大小不为零的常规文件必须给出 *fileobj*。

## TarFile.**gettarinfo**(*name=None*, *arcname=None*, *fileobj=None*)

​	基于 [`os.stat()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.stat) 的结果或者现有文件的相同数据创建一个 [`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo)。 文件或者是命名为 *name*，或者是使用文件描述符指定为一个 [file object](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-object) *fileobj*。 *name* 可以是一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。 如果给定了 *arcname*，则它将为归档中的文件指定一个替代名称，在其他情况下，名称将从 *fileobj* 的 [`name`](https://docs.python.org/zh-cn/3.13/library/io.html#io.FileIO.name) 属性或 *name* 参数获取。 名称应当是一个文本字符串。

​	你可以在使用 [`addfile()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.addfile) 添加 [`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 的某些属性之前修改它们。 如果文件对象不是从文件开头进行定位的普通文件对象，[`size`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo.size) 之类的属性就可能需要修改。 例如 [`GzipFile`](https://docs.python.org/zh-cn/3.13/library/gzip.html#gzip.GzipFile) 之类的文件就属于这种情况。 [`name`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo.name) 也可以被修改，在这种情况下 *arcname* 可以是一个占位字符串。

*在 3.6 版本发生变更:* *name* 形参接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## TarFile.**close**()

​	关闭 [`TarFile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile)。 在写入模式下，会向归档添加两个表示结束的零数据块。

## TarFile.**pax_headers***: [dict](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#dict)*

​	一个包含 pax 全局标头的键值对的字典。



## TarInfo 对象

[`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 对象代表 [`TarFile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile) 中的一个文件。 除了会存储所有必要的文件属性（例如文件类型、大小、时间、权限、所有者等），它还提供了一些确定文件类型的有用方法。 此对象 *并不* 包含文件数据本身。

[`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 对象可通过 [`TarFile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile) 的方法 [`getmember()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.getmember), [`getmembers()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.getmembers) 和 [`gettarinfo()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.gettarinfo) 返回。

​	修改 [`getmember()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.getmember) 或 [`getmembers()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.getmembers) 返回的对象会影响在上的所有后续操作。 对于不想要这样的场景，你可以使用 [`copy.copy()`](https://docs.python.org/zh-cn/3.13/library/copy.html#module-copy) 或调用 [`replace()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo.replace) 方法一次性创建修改后的副本。

​	部分属性可以设为 `None` 以表示一些元数据未被使用或未知。 不同的 [`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 方法会以不同的方式处理 `None`:

- [`extract()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extract) 或 [`extractall()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extractall) 方法会忽略相应的元数据，让其保持默认设置。
- [`addfile()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.addfile) 将会失败。
- [`list()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.list) 将打印一个占位字符串。

## *class* tarfile.**TarInfo**(*name=''*)

​	创建一个 [`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 对象。

## *classmethod* TarInfo.**frombuf**(*buf*, *encoding*, *errors*)

​	基于字符串缓冲区 *buf* 创建并返回一个 [`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 对象。

​	如果缓冲区无效则会引发 [`HeaderError`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.HeaderError)。

## *classmethod* TarInfo.**fromtarfile**(*tarfile*)

​	从 [`TarFile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile) 对象 *tarfile* 读取下一个成员并将其作为 [`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 对象返回。

## TarInfo.**tobuf**(*format=DEFAULT_FORMAT*, *encoding=ENCODING*, *errors='surrogateescape'*)

​	基于 [`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 对象创建一个字符串缓冲区。 有关参数的信息请参见 [`TarFile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile) 类的构造器。

*在 3.2 版本发生变更:* 使用 `'surrogateescape'` 作为 *errors* 参数的默认值。

`TarInfo` 对象具有以下公有数据属性:

## TarInfo.**name***: [str](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)*

​	归档成员的名称。

## TarInfo.**size***: [int](https://docs.python.org/zh-cn/3.13/library/functions.html#int)*

​	以字节表示的大小。

## TarInfo.**mtime***: [int](https://docs.python.org/zh-cn/3.13/library/functions.html#int) | [float](https://docs.python.org/zh-cn/3.13/library/functions.html#float)*

​	以 [Unix 纪元](https://docs.python.org/zh-cn/3.13/library/time.html#epoch) 秒数表示的最近修改时间，与 [`os.stat_result.st_mtime`](https://docs.python.org/zh-cn/3.13/library/os.html#os.stat_result.st_mtime) 相同。

*在 3.12 版本发生变更:* 对于 [`extract()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extract) 和 [`extractall()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extractall) 可设为 `None`，以使解压缩操作跳过应用此属性。

## TarInfo.**mode***: [int](https://docs.python.org/zh-cn/3.13/library/functions.html#int)*

​	权限比特位，与 [`os.chmod()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.chmod) 相同。

*在 3.12 版本发生变更:* 对于 [`extract()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extract) 和 [`extractall()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extractall) 可设为 `None`，以使解压缩操作跳过应用此属性。

## TarInfo.**type**

​	文件类型。 *type* 通常为以下常量之一: [`REGTYPE`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.REGTYPE), [`AREGTYPE`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.AREGTYPE), [`LNKTYPE`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.LNKTYPE), [`SYMTYPE`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.SYMTYPE), [`DIRTYPE`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.DIRTYPE), [`FIFOTYPE`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.FIFOTYPE), [`CONTTYPE`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.CONTTYPE), [`CHRTYPE`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.CHRTYPE), [`BLKTYPE`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.BLKTYPE), [`GNUTYPE_SPARSE`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.GNUTYPE_SPARSE)。 要更方便地确定一个 [`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 对象的类型，请使用下述的 `is*()` 方法。

## TarInfo.**linkname***: [str](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)*

​	目标文件名的名称，该属性仅在类型为 [`LNKTYPE`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.LNKTYPE) 和 [`SYMTYPE`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.SYMTYPE) 的 [`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 对象中存在。

​	对于符号链接 (`SYMTYPE`)，*linkname* 是相对于包含链接的目录的。 对于硬链接 (`LNKTYPE`)，*linkname* 则是相对于存档根目录的。

## TarInfo.**uid***: [int](https://docs.python.org/zh-cn/3.13/library/functions.html#int)*

​	最初保存该成员的用户的用户 ID。

*在 3.12 版本发生变更:* 对于 [`extract()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extract) 和 [`extractall()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extractall) 可设为 `None`，以使解压缩操作跳过应用此属性。

## TarInfo.**gid***: [int](https://docs.python.org/zh-cn/3.13/library/functions.html#int)*

​	最初保存该成员的用户的分组 ID。

*在 3.12 版本发生变更:* 对于 [`extract()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extract) 和 [`extractall()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extractall) 可设为 `None`，以使解压缩操作跳过应用此属性。

## TarInfo.**uname***: [str](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)*

​	用户名。

*在 3.12 版本发生变更:* 对于 [`extract()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extract) 和 [`extractall()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extractall) 可设为 `None`，以使解压缩操作跳过应用此属性。

## TarInfo.**gname***: [str](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)*

​	分组名。

*在 3.12 版本发生变更:* 对于 [`extract()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extract) 和 [`extractall()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extractall) 可设为 `None`，以使解压缩操作跳过应用此属性。

## TarInfo.**chksum***: [int](https://docs.python.org/zh-cn/3.13/library/functions.html#int)*

​	标头校验和。

## TarInfo.**devmajor***: [int](https://docs.python.org/zh-cn/3.13/library/functions.html#int)*

​	设备主编号。

## TarInfo.**devminor***: [int](https://docs.python.org/zh-cn/3.13/library/functions.html#int)*

​	设备次编号。

## TarInfo.**offset***: [int](https://docs.python.org/zh-cn/3.13/library/functions.html#int)*

​	tar 标头从这里开始。

## TarInfo.**offset_data***: [int](https://docs.python.org/zh-cn/3.13/library/functions.html#int)*

​	文件的数据从这里开始。

## TarInfo.**sparse**

​	离散的成员信息。

## TarInfo.**pax_headers***: [dict](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#dict)*

​	一个包含所关联的 pax 扩展标头的键值对的字典。

## TarInfo.**replace**(*name=...*, *mtime=...*, *mode=...*, *linkname=...*, *uid=...*, *gid=...*, *uname=...*, *gname=...*, *deep=True*)

*Added in version 3.12.*

​	返回修改了给定属性的 `TarInfo` 对象的 *新* 副本。 例如，要返回组名设为 `'staff'` 的 `TarInfo`，请使用:

```
new_tarinfo = old_tarinfo.replace(gname='staff')
```

​	在默认情况下，将执行深拷贝。 如果 *deep* 为假值，则执行浅拷贝，即 `pax_headers` 及任何自定义属性都与原始 `TarInfo` 对象共享。

[`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 对象还提供了一些便捷查询方法:

## TarInfo.**isfile**()

​	如果 [`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 对象为普通文件则返回 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)。

## TarInfo.**isreg**()

​	与 [`isfile()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo.isfile) 相同。

## TarInfo.**isdir**()

​	如果为目录则返回 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)。

## TarInfo.**issym**()

​	如果为符号链接则返回 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)。

## TarInfo.**islnk**()

​	如果为硬链接则返回 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)。

## TarInfo.**ischr**()

​	如果为字符设备则返回 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)。

## TarInfo.**isblk**()

​	如果为块设备则返回 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)。

## TarInfo.**isfifo**()

​	如果为 FIFO 则返回 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)。.

## TarInfo.**isdev**()

​	如果为字符设备、块设备或 FIFO 之一则返回 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)。



## 解压缩过滤器

*Added in version 3.12.*

*tar* 格式的设计旨在捕捉类 UNIX 文件系统的所有细节，这使其功能非常强大。 不幸的是，这些特性也使得很容易创建在解压缩时产生意想不到的 -- 甚至可能是恶意的 -- 影响的 tar 文件。 举例来说，解压缩 tar 文件时可以通过各种方式覆盖任意文件（例如通过使用绝对路径、`..` 路径组件或影响后续成员的符号链接等）。

​	在大多数情况下，并不需要全部的功能。 因此，*tarfile* 支持提取过滤器：一种限制功能的机制，从而避免一些安全问题。

​	参见

## [**PEP 706**](https://peps.python.org/pep-0706/)

​	包含设计背后进一步的动机和理由。

[`TarFile.extract()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extract) 或 [`extractall()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extractall) 的 *filter* 参数可以是：

- 字符串 `'fully_trusted'`：尊重归档文件中指定的所有元数据。 如果用户完全信任该归档，或实现了自己的复杂验证则应使用此过滤器。

- 字符串 `'tar'`: 尊重大多数 *tar* 专属的特性（即类 UNIX 文件系统的功能），但阻止极有可能令人惊讶的或恶意的功能。 详情参见 [`tar_filter()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.tar_filter)。

- 字符串 `'data'`：忽略或阻止大多数类 UNIX 文件系统专属的特性。 用于提取跨平台数据归档文件。 详情参见 [`data_filter()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.data_filter)。

- `None` (默认): 使用 [`TarFile.extraction_filter`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extraction_filter)。

  如果这也为 `None` (默认值)，则引发 `DeprecationWarning`，并回退为 `'fully_trusted'` 过滤器，其危险行为与之前版本的 Python 一致。

  在 Python 3.14 中，`'data'` 过滤器将变成默认选项。 也可以提前切换，参见 [`TarFile.extraction_filter`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extraction_filter)。

- 该可调用对象将针每个被提取的成员执行调用并附带一个 [TarInfo](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarinfo-objects) 来描述该成员以及被提取归档文件的目标路径（即供所有成员使用的相同路径）:

  ```
  filter(member: TarInfo, path: str, /) -> TarInfo | None
  ```

  该可调用对象会在提取每个成员之前被调用，因此它能够将磁盘的当前状态考虑在内。 它可以：

  - 返回一个 [`TarInfo`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarInfo) 对象，该对象将被用来代替归档文件中的元数据，或者
  - 返回 `None`，在这种情况下该成员将被跳过，或者
  - 根据 [`errorlevel`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.errorlevel) 的值引发一个异常以中止操作或跳过成员。 请注意当提取操作中止时，[`extractall()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extractall) 可能会保留部分已提取的归档文件。 它不会尝试执行清理。

### 默认的命名过滤器

​	预定义的命名过滤器可作为函数使用，因此它们可在自定义过滤器中被重用：

## tarfile.**fully_trusted_filter**(*member*, *path*)

​	不加修改地返回 *member*。

​	实现 `'fully_trusted'` 过滤器。

## tarfile.**tar_filter**(*member*, *path*)

​	实现 `'tar'` 过滤器。

- 从文件名中去除开头的斜杠 (`/` 和 [`os.sep`](https://docs.python.org/zh-cn/3.13/library/os.html#os.sep))。
- [拒绝](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile-extraction-refuse) 提取具有绝对路径的文件（针对名称在去除斜杠后仍为绝对路径的情况，例如 Windows 上 `C:/foo` 这样的路径）。 这会引发 [`AbsolutePathError`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.AbsolutePathError)。
- [拒绝](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile-extraction-refuse) 提取具有位于目标以外的绝对路径（跟随符号链接之后）的文件。这会引发 [`OutsideDestinationError`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.OutsideDestinationError)。
- 清空高模式位 (setuid, setgid, sticky) 和 group/other 写入位 ([`S_IWGRP`](https://docs.python.org/zh-cn/3.13/library/stat.html#stat.S_IWGRP) | [`S_IWOTH`](https://docs.python.org/zh-cn/3.13/library/stat.html#stat.S_IWOTH))。

​	返回修改后的 `TarInfo` 成员。

## tarfile.**data_filter**(*member*, *path*)

​	实现 `'data'` 过滤器。 在 `tar_filter` 的所具有的功能之外：

- [拒绝](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile-extraction-refuse) 提取链接到绝对路径的链接（不论是硬链接还是软链接），或链接到目标之外的链接。

  这会引发 [`AbsoluteLinkError`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.AbsoluteLinkError) 或 [`LinkOutsideDestinationError`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.LinkOutsideDestinationError)。

  请注意即使在不支持符号链接的平台上此类文件也会被拒绝。

- [拒绝](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile-extraction-refuse) 提取设备文件（包括管道）。 这会引发 [`SpecialFileError`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.SpecialFileError)。

- 用于常规文件，包括硬链接：

  - 设置所有者读写权限 ([`S_IRUSR`](https://docs.python.org/zh-cn/3.13/library/stat.html#stat.S_IRUSR) | [`S_IWUSR`](https://docs.python.org/zh-cn/3.13/library/stat.html#stat.S_IWUSR))。
  - 如果所有者没有 group 和 other 可执行权限 ([`S_IXGRP`](https://docs.python.org/zh-cn/3.13/library/stat.html#stat.S_IXGRP) | [`S_IXOTH`](https://docs.python.org/zh-cn/3.13/library/stat.html#stat.S_IXOTH)) 则移除它 ([`S_IXUSR`](https://docs.python.org/zh-cn/3.13/library/stat.html#stat.S_IXUSR))。

- 对于其他文件（目录），将 `mode` 设为 `None`，以便提取方法跳过应用权限位。

- 将用户和组信息 (`uid`, `gid`, `uname`, `gname`) 设为 `None`，以使得提取方法跳过对它的设置。

​	返回修改后的 `TarInfo` 成员。



### 过滤器错误

​	当过滤器拒绝提取文件时，它将引发一个适当的异常，即 [`FilterError`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.FilterError) 的子类。 如果 [`TarFile.errorlevel`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.errorlevel) 为 1 或更大的值则提取将中止。 如果 `errorlevel=0` 则会记录错误并跳过该成员，但提取仍会继续。

### 进一步核验的提示

​	即使 `filter='data'`，*tarfile* 也不适合在没有事先检查的情况下提取不受信任的文件。 除其他问题外，预定义的过滤器不能防止拒绝服务攻击。 用户应当进行额外的检查。

​	以下是一份不完整的考虑事项列表：

- 提取到 [`新的临时目录`](https://docs.python.org/zh-cn/3.13/library/tempfile.html#tempfile.mkdtemp) 以避免滥用已存在的链接等问题，并使得提取失败后更容易清理。
- 在处理不受信任的数据时，使用外部（例如操作系统层级）的磁盘、内存和 CPU 使用限制。
- 根据允许字符列表检查文件名（来过滤控制字符、易混淆字符、外来路径分隔符等）。
- 检查文件名是否有预期的扩展名（不鼓励使用在“点击”时会被执行的文件，或像 Windows 特殊设备名称这样没有扩展名的文件）。
- 限制提取文件的数量、提取数据的总大小、文件名长度（包括符号链接长度）以及单个文件的大小。
- 检查在不区分大小写的文件系统上会被屏蔽的文件。

​	还需要注意：

- Tar 文件可能包含同一文件的多个版本。 较晚的版本会覆盖任何较早的版本。 这一功能对于更新磁带归档来说至关重要，但也可能被恶意滥用。
- *tarfile* 无法为“实时”数据的问题提供保护，例如在提取（或归档）过程中攻击者对目标（或源）目录进行了改动。

### 支持较早的 Python 版本

​	提取过滤器是在 Python 3.12 中增加的，但可能会作为安全更新向下移植到较老的版本。 要检查该特性是否可用，请使用 `hasattr(tarfile, 'data_filter')` 而不是检查 Python 版本。

​	下面的例子演示了如何支持带有和没有有该功能的 Python 版本。 请注意设置 `extraction_filter` 会影响任何后续的操作。

- 完全受信任的归档:

  ```
  my_tarfile.extraction_filter = (lambda member, path: member)
  my_tarfile.extractall()
  ```

- 如果可用则使用 `'data'` 过滤器；如果此特性不可用，则恢复为 Python 3.11 的行为 (`'fully_trusted'`):

  ```
  my_tarfile.extraction_filter = getattr(tarfile, 'data_filter',
                                         (lambda member, path: member))
  my_tarfile.extractall()
  ```

- 使用 `'data'` 过滤器；如果不可用则 *fail*:

  ```
  my_tarfile.extractall(filter=tarfile.data_filter)
  ```

  或者:

  ```
  my_tarfile.extraction_filter = tarfile.data_filter
  my_tarfile.extractall()
  ```

- 使用 `'data'` 过滤器；如果不可用则 *warn*:

  ```
  if hasattr(tarfile, 'data_filter'):
      my_tarfile.extractall(filter='data')
  else:
      # remove this when no longer needed
      warn_the_user('Extracting may be unsafe; consider updating Python')
      my_tarfile.extractall()
  ```

### 有状态的提取过滤器示例

*tarfile* 的提取方法接受一个简单的 *filter* 可调用对象，而自定义过滤器则可以是具有内部状态的更复杂对象。 将其写成为下文管理器可能会很有用处，即以这样的方式使用:

```
with StatefulFilter() as filter_func:
    tar.extractall(path, filter=filter_func)
```

​	例如，这种过滤器可以写成:

```
class StatefulFilter:
    def __init__(self):
        self.file_count = 0

    def __enter__(self):
        return self

    def __call__(self, member, path):
        self.file_count += 1
        return member

    def __exit__(self, *exc_info):
        print(f'{self.file_count} files extracted')
```



## 命令行接口

*Added in version 3.4.*

[`tarfile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#module-tarfile) 模块提供了简单的命令行接口以便与 tar 归档进行交互。

​	如果你想要创建一个新的 tar 归档，请在 [`-c`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#cmdoption-tarfile-c) 选项后指定其名称然后列出应当被包含的文件名:

```
$ python -m tarfile -c monty.tar  spam.txt eggs.txt
```

​	传入一个字典也是可接受的:

```
$ python -m tarfile -c monty.tar life-of-brian_1979/
```

​	如果你想要将一个 tar 归档提取到指定的目录，请使用 [`-e`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#cmdoption-tarfile-e) 选项:

```
$ python -m tarfile -e monty.tar
```

​	你也可以通过传入目录名称将一个 tar 归档提取到不同的目录:

```
$ python -m tarfile -e monty.tar  other-dir/
```

​	要获取一个 tar 归档中文件的列表，请使用 [`-l`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#cmdoption-tarfile-l) 选项:

```
$ python -m tarfile -l monty.tar
```

### 命令行选项

## **-l** <tarfile>

## **--list** <tarfile>

​	列出一个 tarfile 中的文件名。

## **-c** <tarfile> <source1> ... <sourceN>

## **--create** <tarfile> <source1> ... <sourceN>

​	基于源文件创建 tarfile。

## **-e** <tarfile> [<output_dir>]

## **--extract** <tarfile> [<output_dir>]

​	如果未指定 *output_dir* 则会将 tarfile 提取到当前目录。

## **-t** <tarfile>

## **--test** <tarfile>

​	检测 tarfile 是否有效。

## **-v**, **--verbose**

​	更详细地输出结果。

## **--filter** <filtername>

​	为 `--extract` 指定 *filter*。 详情参见 [解压缩过滤器](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile-extraction-filter)。 只接受字符串名称 (包括 `fully_trusted`, `tar` 和 `data`)。



## 例子

​	如何将整个 tar 归档提取到当前工作目录:

```
import tarfile
tar = tarfile.open("sample.tar.gz")
tar.extractall(filter='data')
tar.close()
```

​	如何通过 [`TarFile.extractall()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.extractall) 使用生成器函数而非列表来提取一个 tar 归档的子集:

```
import os
import tarfile

def py_files(members):
    for tarinfo in members:
        if os.path.splitext(tarinfo.name)[1] == ".py":
            yield tarinfo

tar = tarfile.open("sample.tar.gz")
tar.extractall(members=py_files(tar))
tar.close()
```

​	如何基于一个文件名列表创建未压缩的 tar 归档:

```
import tarfile
tar = tarfile.open("sample.tar", "w")
for name in ["foo", "bar", "quux"]:
    tar.add(name)
tar.close()
```

​	使用 [`with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#with) 语句的同一个示例:

```
import tarfile
with tarfile.open("sample.tar", "w") as tar:
    for name in ["foo", "bar", "quux"]:
        tar.add(name)
```

​	如何读取一个 gzip 压缩的 tar 归档并显示一些成员信息:

```
import tarfile
tar = tarfile.open("sample.tar.gz", "r:gz")
for tarinfo in tar:
    print(tarinfo.name, "is", tarinfo.size, "bytes in size and is ", end="")
    if tarinfo.isreg():
        print("a regular file.")
    elif tarinfo.isdir():
        print("a directory.")
    else:
        print("something else.")
tar.close()
```

​	如何创建一个归档并使用 [`TarFile.add()`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile.add) 中的 *filter* 形参来重置用户信息:

```
import tarfile
def reset(tarinfo):
    tarinfo.uid = tarinfo.gid = 0
    tarinfo.uname = tarinfo.gname = "root"
    return tarinfo
tar = tarfile.open("sample.tar.gz", "w:gz")
tar.add("foo", filter=reset)
tar.close()
```



## 受支持的 tar 格式

​	通过 [`tarfile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#module-tarfile) 模块可以创建三种 tar 格式:

- The POSIX.1-1988 ustar 格式 ([`USTAR_FORMAT`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.USTAR_FORMAT))。 它支持最多 256 个字符的文件名长度和最多 100 个字符的链接名长度。 文件大小上限为 8 GiB。 这是一种老旧但广受支持的格式。

- GNU tar 格式 ([`GNU_FORMAT`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.GNU_FORMAT))。 它支持长文件名和链接名、大于 8 GiB 的文件以及稀疏文件。 它是 GNU/Linux 系统上的事实标准。 [`tarfile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#module-tarfile) 完全支持针对长名称的 GNU tar 扩展，稀疏文件支持则限制为只读。

- POSIX.1-2001 pax 格式 ([`PAX_FORMAT`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.PAX_FORMAT))。 它是几乎无限制的最灵活格式。 它支持长文件名和链接名，大文件以及使用可移植方式存储路径名。 现代的 tar 实现，包括 GNU tar, bsdtar/libarchive 和 star，都完全支持扩展的 *pax* 特性；某些老旧或不再维护的库可能不支持，但应当会将 *pax* 归档视为广受支持的 *ustar* 格式。 它是当前新建归档的默认格式。

  它扩展了现有的 *ustar* 格式，包括用于无法以其他方式存储的附加标头。 存在两种形式的 pax 标头：扩展标头只影响后续的文件标头，全局标头则适用于完整归档并会影响所有后续的文件。 为了便于移植，在 pax 标头中的所有数据均以 *UTF-8* 编码。

​	还有一些 tar 格式的其他变种，它们可以被读取但不能被创建:

- 古老的 V7 格式。 这是来自 Unix 第七版的第一个 tar 格式，它只存储常规文件和目录。 名称长度不能超过 100 个字符，并且没有用户/分组名信息。 某些归档在带有非 ASCII 字符字段的情况下会产生计算错误的标头校验和。
- SunOS tar 扩展格式。 此格式是 POSIX.1-2001 pax 格式的一个变种，但并不保持兼容。



## Unicode 问题

​	最初 tar 格式被设计用来在磁带机上生成备份，主要关注于保存文件系统信息。 现在 tar 归档通常用于文件分发和在网络上交换归档。 最初格式（它是所有其他格式的基础）的一个问题是它没有支持不同字符编码格式的概念。 例如，一个在 *UTF-8* 系统上创建的普通 tar 归档如果包含非 *ASCII* 字符则将无法在 *Latin-1* 系统上被正确读取。 文本元数据（例如文件名，链接名，用户/分组名）将变为损坏状态。 不幸的是，没有什么办法能够自动检测一个归档的编码格式。 pax 格式被设计用来解决这个问题。 它使用通用字符编码格式 *UTF-8* 来存储非 ASCII 元数据。

​	在 [`tarfile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#module-tarfile) 中字符转换的细节由 [`TarFile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.TarFile) 类的 *encoding* 和 *errors* 关键字参数控制。

*encoding* 定义了用于归档中元数据的字符编码格式。 默认值为 [`sys.getfilesystemencoding()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.getfilesystemencoding) 或是回退选项 `'ascii'`。 根据归档是被读取还是被写入，元数据必须被解码或编码。 如果没有正确设置 *encoding*，转换可能会失败。

*errors* 参数定义了不能被转换的字符将如何处理。 可能的取值在 [错误处理方案](https://docs.python.org/zh-cn/3.13/library/codecs.html#error-handlers) 小节列出。 默认方案为 `'surrogateescape'`，它也被 Python 用于文件系统调用，参见 [文件名，命令行参数，以及环境变量。](https://docs.python.org/zh-cn/3.13/library/os.html#os-filenames)。

​	对于 [`PAX_FORMAT`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#tarfile.PAX_FORMAT) 归档（默认格式），*encoding* 通常是不必要的，因为所有元数据都使用 *UTF-8* 来存储。 *encoding* 仅在解码二进制 pax 标头或存储带有替代字符的字符串等少数场景下会被使用。
