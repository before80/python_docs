+++
title = "zipfile --- 操作 ZIP 归档文件"
date = 2024-11-15T12:00:20+08:00
weight = 40
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/zipfile.html](https://docs.python.org/zh-cn/3.13/library/zipfile.html)
>
> 收录该文档的时间：`2024-11-15T12:00:20+08:00`

# `zipfile` --- 操作 ZIP 归档文件

**源代码:** [Lib/zipfile/](https://github.com/python/cpython/tree/3.13/Lib/zipfile/)

------

​	ZIP 文件格式是一个常用的归档与压缩标准。 这个模块提供了创建、读取、写入、添加及列出 ZIP 文件的工具。 任何对此模块的进阶使用都将需要理解此格式，其定义参见 [PKZIP 应用程序笔记](https://pkware.cachefly.net/webdocs/casestudies/APPNOTE.TXT)。

​	此模块目前不能处理分卷 ZIP 文件。它可以处理使用 ZIP64 扩展（超过 4 GB 的 ZIP 文件）的 ZIP 文件。它支持解密 ZIP 归档中的加密文件，但是目前不能创建一个加密的文件。解密非常慢，因为它是使用原生 Python 而不是 C 实现的。

​	这个模块定义了以下内容：

## *exception* zipfile.**BadZipFile**

​	为损坏的 ZIP 文件抛出的错误。

> Added in version 3.2.
>

## *exception* zipfile.**BadZipfile**

[`BadZipFile`]({{< ref "/library/archiving/zipfile#zipfile.BadZipFile" >}}) 的别名，与旧版本 Python 保持兼容性。

*自 3.2 版本弃用.*

## *exception* zipfile.**LargeZipFile**

​	当 ZIP 文件需要 ZIP64 功能但是未启用时会抛出此错误。

## *class* zipfile.**ZipFile**

​	用于读写 ZIP 文件的类。 欲了解构造函数的描述，参阅段落 [ZipFile 对象]({{< ref "/library/archiving/zipfile#zipfile-objects" >}})。

## *class* zipfile.**Path**

​	实现了 [`pathlib.Path`]({{< ref "/library/filesys/pathlib#pathlib.Path" >}}) 所提供接口的一个子集的类，包括完整的 [`importlib.resources.abc.Traversable`]({{< ref "/library/modules/importlib_resources_abc#importlib.resources.abc.Traversable" >}}) 接口。

> Added in version 3.8.
>

## *class* zipfile.**PyZipFile**

​	用于创建包含 Python 库的 ZIP 归档的类。

## *class* zipfile.**ZipInfo**(*filename='NoName'*, *date_time=(1980, 1, 1, 0, 0, 0)*)

​	用于表示档案内一个成员信息的类。 此类的实例会由 [`ZipFile`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile" >}}) 对象的 [`getinfo()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.getinfo" >}}) 和 [`infolist()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.infolist" >}}) 方法返回。 大多数 [`zipfile`]({{< ref "/library/archiving/zipfile#module-zipfile" >}}) 模块的用户都不必创建它们，只需使用此模块所创建的实例。 *filename* 应当是档案成员的全名，*date_time* 应当是包含六个字段的描述最近修改时间的元组；这些字段的描述请参阅 [ZipInfo 对象]({{< ref "/library/archiving/zipfile#zipinfo-objects" >}})。

> 在 3.13 版本发生变更: 增加了公有的 `compress_level` 属性来暴露之前被保护 `_compresslevel`。 较旧的被保护名称可继续作为保持向下兼容性的特征属性使用。

## zipfile.**is_zipfile**(*filename*)

​	根据文件的 Magic Number，如果 *filename* 是一个有效的 ZIP 文件则返回 `True`，否则返回 `False`。 *filename* 也可能是一个文件或类文件对象。

> 在 3.1 版本发生变更: 支持文件或类文件对象。

## zipfile.**ZIP_STORED**

​	未被压缩的归档成员的数字常数。

## zipfile.**ZIP_DEFLATED**

​	常用的 ZIP 压缩方法的数字常数。需要 [`zlib`]({{< ref "/library/archiving/zlib#module-zlib" >}}) 模块。

## zipfile.**ZIP_BZIP2**

​	BZIP2 压缩方法的数字常数。需要 [`bz2`]({{< ref "/library/archiving/bz2#module-bz2" >}}) 模块。

> Added in version 3.3.
>

## zipfile.**ZIP_LZMA**

​	LZMA 压缩方法的数字常数。需要 [`lzma`]({{< ref "/library/archiving/lzma#module-lzma" >}}) 模块。

> Added in version 3.3.
>

​备注
 

​	ZIP 文件格式规范包括自 2001 年以来对 bzip2 压缩的支持，以及自 2006 年以来对 LZMA 压缩的支持。但是，一些工具（包括较旧的 Python 版本）不支持这些压缩方法，并且可能拒绝完全处理 ZIP 文件，或者无法提取单个文件。

​参见
## [PKZIP 应用程序笔记](https://pkware.cachefly.net/webdocs/casestudies/APPNOTE.TXT)

​	Phil Katz 编写的 ZIP 文件格式文档，此格式和使用的算法的创建者。

## [Info-ZIP 主页](https://infozip.sourceforge.net/)

​	有关 Info-ZIP 项目的 ZIP 存档程序和开发库的信息。



## ZipFile 对象

## *class* zipfile.**ZipFile**(*file*, *mode='r'*, *compression=ZIP_STORED*, *allowZip64=True*, *compresslevel=None*, ***, *strict_timestamps=True*, *metadata_encoding=None*)

​	打开一个 ZIP 文件，*file* 为一个指向文件的路径（字符串），一个类文件对象或者一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

​	形参 *mode* 应当为 `'r'` 来读取一个存在的文件，`'w'` 来截断并写入新的文件， `'a'` 来添加到一个存在的文件，或者 `'x'` 来仅新建并写入新的文件。如果 *mode* 为 `'x'` 并且 *file* 指向已经存在的文件，则抛出 [`FileExistsError`]({{< ref "/library/exceptions#FileExistsError" >}})。如果 *mode* 为 `'a'` 且 *file* 为已存在的文件，则格外的文件将被加入。如果 *file* 不指向 ZIP 文件，之后一个新的 ZIP 归档将被追加为此文件。这是为了将 ZIP 归档添加到另一个文件（例如 `python.exe`）。如果 *mode* 为 `'a'` 并且文件不存在， 则会新建。如果 *mode* 为 `'r'` 或 `'a'`， 则文件应当可定位。

*compression* 是在写入归档时要使用的 ZIP 压缩方法，应为 [`ZIP_STORED`]({{< ref "/library/archiving/zipfile#zipfile.ZIP_STORED" >}}), [`ZIP_DEFLATED`]({{< ref "/library/archiving/zipfile#zipfile.ZIP_DEFLATED" >}}), [`ZIP_BZIP2`]({{< ref "/library/archiving/zipfile#zipfile.ZIP_BZIP2" >}}) 或 [`ZIP_LZMA`]({{< ref "/library/archiving/zipfile#zipfile.ZIP_LZMA" >}})；不可识别的值将导致引发 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}})。 如果指定了 [`ZIP_DEFLATED`]({{< ref "/library/archiving/zipfile#zipfile.ZIP_DEFLATED" >}}), [`ZIP_BZIP2`]({{< ref "/library/archiving/zipfile#zipfile.ZIP_BZIP2" >}}) 或 [`ZIP_LZMA`]({{< ref "/library/archiving/zipfile#zipfile.ZIP_LZMA" >}}) 但相应的模块 ([`zlib`]({{< ref "/library/archiving/zlib#module-zlib" >}}), [`bz2`]({{< ref "/library/archiving/bz2#module-bz2" >}}) 或 [`lzma`]({{< ref "/library/archiving/lzma#module-lzma" >}})) 不可用，则会引发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。 默认值为 [`ZIP_STORED`]({{< ref "/library/archiving/zipfile#zipfile.ZIP_STORED" >}})。

​	如果 *allowZip64* 为 `True` (默认值) 则当 zipfile 大于 4 GiB 时 zipfile 将创建使用 ZIP64 扩展的 ZIP 文件。 如果该参数为 `false` 则当 ZIP 文件需要 ZIP64 扩展时 [`zipfile`]({{< ref "/library/archiving/zipfile#module-zipfile" >}}) 将引发异常。

*compresslevel* 形参控制在将文件写入归档时要使用的压缩等级。 当使用 [`ZIP_STORED`]({{< ref "/library/archiving/zipfile#zipfile.ZIP_STORED" >}}) 或 [`ZIP_LZMA`]({{< ref "/library/archiving/zipfile#zipfile.ZIP_LZMA" >}}) 时无压缩效果。 当使用 [`ZIP_DEFLATED`]({{< ref "/library/archiving/zipfile#zipfile.ZIP_DEFLATED" >}}) 时接受整数 `0` 至 `9` (更多信息参见 [`zlib`]({{< ref "/library/archiving/zlib#zlib.compressobj" >}}))。 当使用 [`ZIP_BZIP2`]({{< ref "/library/archiving/zipfile#zipfile.ZIP_BZIP2" >}}) 时接受整数 `1` 至 `9` (更多信息参见 [`bz2`]({{< ref "/library/archiving/bz2#bz2.BZ2File" >}}))。

*strict_timestamps* 参数在设为 `False` 时允许压缩早于 1980-01-01 的文件，代价时会将时间戳设为 1980-01-01。 类似的行为也会对晚于 2107-12-31 的文件发生，时间戳也会被设为该上限值。

​	当 mode 为 `'r'` 时，可以将 *metadata_encoding* 设为某个编解码器的名称，它将被用来解码元数据如成员名称和 ZIP 注释等等。

​	如果创建文件时使用 `'w'`, `'x'` 或 `'a'` 模式并且未向归档添加任何文件就执行了 [`closed`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.close" >}})，则会将适当的空归档 ZIP 结构写入文件。

​	ZipFile 也是一个上下文管理器，因此支持 [`with`]({{< ref "/reference/compound_stmts#with" >}}) 语句。 在这个示例中，*myzip* 将在 `with` 语句块执行完成之后被关闭 --- 即使是发生了异常:

```
with ZipFile('spam.zip', 'w') as myzip:
    myzip.write('eggs.txt')
```

​备注
 

*metadata_encoding* 是用于 ZipFile 的实例级设置。 目前无法在成员层级上设置此选项。

​	该属性是对旧式实现的变通处理，它产生的归档文件名会使用当前语言区域编码格式或代码页（主要是在 Windows 上）。 根据 .ZIP 标准，元数据的编码格式可以通过归档文件标头中的一个旗标指定为 IBM 代码页（默认）或 UTF-8。 该旗标优先于 *metadata_encoding*，后者是一个 Python 专属的扩展。

> 在 3.2 版本发生变更: 添加了将 [`ZipFile`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile" >}}) 用作上下文管理器的功能。

> 在 3.3 版本发生变更: 添加了对 [`bzip2`]({{< ref "/library/archiving/bz2#module-bz2" >}}) 和 [`lzma`]({{< ref "/library/archiving/lzma#module-lzma" >}}) 压缩的支持。

> 在 3.4 版本发生变更: 默认启用 ZIP64 扩展。

> 在 3.5 版本发生变更: 添加了对不可查找数据流的支持。 并添加了对 `'x'` 模式的支持。

> 在 3.6 版本发生变更: 在此之前，对于不可识别的压缩值将引发普通的 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。

*在 3.6.2 版本发生变更:* *file* 形参接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

> 在 3.7 版本发生变更: 添加了 *compresslevel* 形参。

> 在 3.8 版本发生变更: *strict_timestamps* 仅限关键字形参。

> 在 3.11 版本发生变更: 增加了对指定成员名称编码格式的支持以便在 ZIP 文件的目录和文件标头中读取元数据。

## ZipFile.**close**()

​	关闭归档文件。 你必须在退出程序之前调用 [`close()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.close" >}}) 否则将不会写入关键记录数据。

## ZipFile.**getinfo**(*name*)

​	返回一个 [`ZipInfo`]({{< ref "/library/archiving/zipfile#zipfile.ZipInfo" >}}) 对象，其中包含有关归档成员 *name* 的信息。 针对一个目前并不包含于归档中的名称调用 [`getinfo()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.getinfo" >}}) 将会引发 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}})。

## ZipFile.**infolist**()

​	返回一个列表，其中包含每个归档成员的 [`ZipInfo`]({{< ref "/library/archiving/zipfile#zipfile.ZipInfo" >}}) 对象。 如果是打开一个现有归档则这些对象的排列顺序与它们对应条目在磁盘上的实际 ZIP 文件中的顺序一致。

## ZipFile.**namelist**()

​	返回按名称排序的归档成员列表。

## ZipFile.**open**(*name*, *mode='r'*, *pwd=None*, ***, *force_zip64=False*)

​	以二进制文件型对象的形式访问一个归档成员。 *name* 可以是归档内某个文件的名称或是某个 [`ZipInfo`]({{< ref "/library/archiving/zipfile#zipfile.ZipInfo" >}}) 对象。 如果包括了 *mode* 形参，则它必须为 `'r'` (默认值) 或 `'w'`。 *pwd* 是用于解密 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 对象形式的已加密 ZIP 文件的密码。

[`open()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.open" >}}) 也是一个上下文管理器，因此支持 [`with`]({{< ref "/reference/compound_stmts#with" >}}) 语句:

```
with ZipFile('spam.zip') as myzip:
    with myzip.open('eggs.txt') as myfile:
        print(myfile.read())
```

​	如果 *mode* 为 `'r'` 则文件型对象 (`ZipExtFile`) 将为只读并且提供下列方法: [`read()`]({{< ref "/library/allos/io#io.BufferedIOBase.read" >}}), [`readline()`]({{< ref "/library/allos/io#io.IOBase.readline" >}}), [`readlines()`]({{< ref "/library/allos/io#io.IOBase.readlines" >}}), [`seek()`]({{< ref "/library/allos/io#io.IOBase.seek" >}}), [`tell()`]({{< ref "/library/allos/io#io.IOBase.tell" >}}), [`__iter__()`]({{< ref "/library/stdtypes#container.__iter__" >}}), [`__next__()`]({{< ref "/library/stdtypes#iterator.__next__" >}})。 这些对象可独立于 ZipFile 进行操作。

​	如果 `mode='w'` 则返回一个可写入的文件句柄，它将支持 [`write()`]({{< ref "/library/allos/io#io.BufferedIOBase.write" >}}) 方法。 当一个可写入的文件句柄被打开时，尝试读写 ZIP 文件中的其他文件将会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

​	在两种情况下该文件型对象还具有属性 `name`，它等价于归档内文件的名称，以及 `mode`，它根据输入模式的不同可能为 `'rb'` 或 `'wb'`。

​	当写入一个文件时，如果文件大小不能预先确定但是可能超过 2 GiB，可传入 `force_zip64=True` 以确保标头格式能够支持超大文件。 如果文件大小可以预先确定，则在构造 [`ZipInfo`]({{< ref "/library/archiving/zipfile#zipfile.ZipInfo" >}}) 对象时应设置 [`file_size`]({{< ref "/library/archiving/zipfile#zipfile.ZipInfo.file_size" >}})，并将其用作 *name* 形参。

​备注
 

[`open()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.open" >}}), [`read()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.read" >}}) 和 [`extract()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.extract" >}}) 方法可接受文件名或 [`ZipInfo`]({{< ref "/library/archiving/zipfile#zipfile.ZipInfo" >}}) 对象。 当尝试读取一个包含重复名称成员的 ZIP 文件时你将发现此功能很有好处。

> 在 3.6 版本发生变更: 移除了对 `mode='U'` 的支持。 请使用 [`io.TextIOWrapper`]({{< ref "/library/allos/io#io.TextIOWrapper" >}}) 以在 [universal newlines]({{< ref "/glossary/idx#term-universal-newlines" >}}) 模式中读取已压缩的文本文件。

> 在 3.6 版本发生变更: 现在 [`ZipFile.open()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.open" >}}) 可以被用来配合 `mode='w'` 选项将文件写入归档。

> 在 3.6 版本发生变更: 在已关闭的 ZipFile 上调用 [`open()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.open" >}}) 将引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。 在之前的版本中则会引发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。

> 在 3.13 版本发生变更: 为可写文件型对象增加了属性 `name` 和 `mode`。 可读文件型对象的 `mode` 属性值由 `'r'` 改为 `'rb'`。

## ZipFile.**extract**(*member*, *path=None*, *pwd=None*)

​	从归档中提取一个成员放入当前工作目录；*member* 必须是一个成员的完整名称或 [`ZipInfo`]({{< ref "/library/archiving/zipfile#zipfile.ZipInfo" >}}) 对象。 成员的文件信息会尽可能精确地被提取。 *path* 指定一个要放入的不同目录。 *member* 可以是一个文件名或 [`ZipInfo`]({{< ref "/library/archiving/zipfile#zipfile.ZipInfo" >}}) 对象。 *pwd* 是 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 对象形式的用于解密已加密文件的密码。

​	返回所创建的经正规化的路径（对应于目录或新文件）。

​备注
 

​	如果一个成员文件名为绝对路径，则将去掉驱动器/UNC共享点和前导的（反）斜杠，例如: `///foo/bar` 在 Unix 上将变为 `foo/bar`，而 `C:\foo\bar` 在 Windows 上将变为 `foo\bar`。 并且一个成员文件名中的所有 `".."` 都将被移除，例如: `../../foo../../ba..r` 将变为 `foo../ba..r`。 在 Windows 上非法字符 (`:`, `<`, `>`, `|`, `"`, `?`, and `*`) 会被替换为下划线 (`_`)。

> 在 3.6 版本发生变更: 在已关闭的 ZipFile 上调用 [`extract()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.extract" >}}) 将引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。 在之前的版本中则将引发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。

*在 3.6.2 版本发生变更:* *path* 形参接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## ZipFile.**extractall**(*path=None*, *members=None*, *pwd=None*)

​	从归档中提取出所有成员放入当前工作目录。 *path* 指定一个要放入的不同目录。 *members* 为可选项且必须为 [`namelist()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.namelist" >}}) 所返回列表的一个子集。 *pwd* 是 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 对象形式的用于解密已加密文件的密码。

​	警告

 

​	绝不要未经预先检验就从不可靠的源中提取归档文件。 这样有可能在 *path* 之外创建文件，例如某些成员具有以 `"/"` 开始的文件名或带有两个点号 `".."` 的文件名。 此模块会尝试防止这种情况。 参见 [`extract()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.extract" >}}) 的注释。

> 在 3.6 版本发生变更: 在已关闭的 ZipFile 上调用 [`extractall()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.extractall" >}}) 将引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。 在之前的版本中则将引发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。

*在 3.6.2 版本发生变更:* *path* 形参接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## ZipFile.**printdir**()

​	将归档的目录表打印到 `sys.stdout`。

## ZipFile.**setpassword**(*pwd*)

​	将 *pwd* (一个 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 对象) 设为用于提取已加密文件的默认密码。

## ZipFile.**read**(*name*, *pwd=None*)

​	返回归档中文件 *name* 的字节数据。 *name* 是归档中文件的名称，或是一个 [`ZipInfo`]({{< ref "/library/archiving/zipfile#zipfile.ZipInfo" >}}) 对象。 归档必须以读取或追加模式打开。 *pwd* 为 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 对象形式的用于解密已加密文件的密码，并且如果指定了该参数则它将覆盖通过 [`setpassword()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.setpassword" >}}) 设置的默认密码。 在使用 [`ZIP_STORED`]({{< ref "/library/archiving/zipfile#zipfile.ZIP_STORED" >}}), [`ZIP_DEFLATED`]({{< ref "/library/archiving/zipfile#zipfile.ZIP_DEFLATED" >}}), [`ZIP_BZIP2`]({{< ref "/library/archiving/zipfile#zipfile.ZIP_BZIP2" >}}) or [`ZIP_LZMA`]({{< ref "/library/archiving/zipfile#zipfile.ZIP_LZMA" >}}) 以外的压缩方法的 ZipFile 上调用 [`read()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.read" >}}) 将引发 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}})。 如果相应的压缩模块不可用也会引发错误。

> 在 3.6 版本发生变更: 在已关闭的 ZipFile 上调用 [`read()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.read" >}}) 将引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。 在之前的版本中则会引发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。

## ZipFile.**testzip**()

​	读取归档中的所有文件并检查它们的 CRC 和文件头。 返回第一个已损坏文件的名称，在其他情况下则返回 `None`。

> 在 3.6 版本发生变更: 在已关闭的 ZipFile 上调用 [`testzip()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.testzip" >}}) 将引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。 在之前的版本中则将引发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。

## ZipFile.**write**(*filename*, *arcname=None*, *compress_type=None*, *compresslevel=None*)

​	将名为 *filename* 的文件写入归档，给予的归档名为 *arcname* (默认情况下将与 *filename* 一致，但是不带驱动器盘符并会移除开头的路径分隔符)。 *compress_type* 如果给出，它将覆盖作为构造器 *compression* 形参对于新条目所给出的值。 类似地，*compresslevel* 如果给出也将覆盖构造器。 归档必须使用 `'w'`, `'x'` 或 `'a'` 模式打开。

​备注
 

​	ZIP 文件标准在历史上并未指定元数据编码格式，但是强烈建议使用 CP437（原始 IBM PC 编码格式）来实现互操作性。 最近的版本允许（仅）使用 UTF-8。 在这个模块中，如果成员名称包含任何非 ASCII 字符则将自动使用 UTF-8 来写入它们。 不可能用 ASCII 或 UTF-8 以外的任何其他编码格式来写入成员名称。

​备注
 

​	归档名称应当是基于归档根目录的相对路径，也就是说，它们不应以路径分隔符开头。

​备注
 

​	如果 `arcname` (或 `filename`，如果 `arcname` 未给出) 包含一个空字节，则归档中该文件的名称将在空字节位置被截断。

​备注
 

​	文件名开头有一个斜杠可能导致存档文件无法在 Windows 系统上的某些 zip 程序中打开。

> 在 3.6 版本发生变更: 在使用 `'r'` 模式创建的 ZipFile 或已关闭的 ZipFile 上调用 [`write()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.write" >}}) 将引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。 在之前的版本中则会引发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。

## ZipFile.**writestr**(*zinfo_or_arcname*, *data*, *compress_type=None*, *compresslevel=None*)

​	将一个文件写入归档。 内容为 *data*，它可以是一个 [`str`]({{< ref "/library/stdtypes#str" >}}) 或 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 的实例；如果是 [`str`]({{< ref "/library/stdtypes#str" >}})，则会先使用 UTF-8 进行编码。 *zinfo_or_arcname* 可以是它在归档中将被给予的名称，或者是 [`ZipInfo`]({{< ref "/library/archiving/zipfile#zipfile.ZipInfo" >}}) 的实例。 如果它是一个实例，则至少必须给定文件名、日期和时间。 如果它是一个名称，则日期和时间会被设为当前日期和时间。 归档必须以 `'w'`, `'x'` 或 `'a'` 模式打开。

​	如果给定了 *compress_type*，它将会覆盖作为新条目构造器的 *compression* 形参或在 *zinfo_or_arcname* (如果是一个 [`ZipInfo`]({{< ref "/library/archiving/zipfile#zipfile.ZipInfo" >}}) 实例) 中所给出的值。 类似地，如果给定了 *compresslevel*，它将会覆盖构造器。

​备注
 

​	当传入一个 [`ZipInfo`]({{< ref "/library/archiving/zipfile#zipfile.ZipInfo" >}}) 实例作为 *zinfo_or_arcname* 形参时，所使用的压缩方法将为在给定的 [`ZipInfo`]({{< ref "/library/archiving/zipfile#zipfile.ZipInfo" >}}) 实例的 *compress_type* 成员中指定的方法。 默认情况下，[`ZipInfo`]({{< ref "/library/archiving/zipfile#zipfile.ZipInfo" >}}) 构造器将将此成员设为 [`ZIP_STORED`]({{< ref "/library/archiving/zipfile#zipfile.ZIP_STORED" >}})。

> 在 3.2 版本发生变更: *compress_type* 参数。

> 在 3.6 版本发生变更: 在使用 `'r'` 模式创建的 ZipFile 或已关闭的 ZipFile 上调用 [`writestr()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.writestr" >}}) 将引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。 在之前的版本中则会引发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。

## ZipFile.**mkdir**(*zinfo_or_directory*, *mode=511*)

​	在归档文件内创建一个目录。 如果 *zinfo_or_directory* 是一个字符串，则会在归档文件中以 *mode* 参数指定的模式创建目录。 但是，如果 *zinfo_or_directory* 是一个 [`ZipInfo`]({{< ref "/library/archiving/zipfile#zipfile.ZipInfo" >}}) 实例则 *mode* 参数将被忽略。

​	归档文件必须以 `'w'`, `'x'` 或 `'a'` 模式打开。

> Added in version 3.11.
>

​	以下数据属性也是可用的:

## ZipFile.**filename**

​	ZIP 文件的名称。

## ZipFile.**debug**

​	要使用的调试输出等级。 这可以设为从 `0` (默认无输出) 到 `3` (最多输出) 的值。 调试信息会被写入 `sys.stdout`。

## ZipFile.**comment**

​	关联到 ZIP 文件的 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 对象形式的说明。 如果将说明赋给以 `'w'`, `'x'` 或 `'a'` 模式创建的 [`ZipFile`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile" >}}) 实例，它的长度不应超过 65535 字节。 超过此长度的说明将被截断。



## Path 对象

## *class* zipfile.**Path**(*root*, *at=''*)

​	根据 `root` zipfile (它可以是一个 [`ZipFile`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile" >}}) 实例或适合传给 [`ZipFile`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile" >}}) 构造器的 `file`) 构造一个 Path 对象。

`at` 指定此 Path 在 zipfile 中的位置，例如 'dir/file.txt', 'dir/' 或 ''。 默认为空字符串，即指定跟目录。

​	Path 对象会公开 [`pathlib.Path`]({{< ref "/library/filesys/pathlib#pathlib.Path" >}}) 对象的下列特性:

​	Path 对象可以使用 `/` 运算符或 `joinpath` 来进行遍历。

## Path.**name**

​	最终的路径组成部分。

## Path.**open**(*mode='r'*, ***, *pwd*, ****)

​	在当前路径上发起调用 [`ZipFile.open()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.open" >}})。 允许通过支持的模式打开用于读取或写入文本或二进制数据: 'r', 'w', 'rb', 'wb'。 当以文本模式打开时位置和关键字参数会被传给 [`io.TextIOWrapper`]({{< ref "/library/allos/io#io.TextIOWrapper" >}})，在其他情况下则会被忽略。 `pwd` 是要传给 [`ZipFile.open()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.open" >}}) 的 `pwd` 形参。

> 在 3.9 版本发生变更: 增加了对以文本和二进制模式打开的支持。 现在默认为文本模式。

*在 3.11.2 版本发生变更:* `encoding` 形参可以作为位置参数来提供而不会引起 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。 这种情况在 3.9 中是会发生的。 需要与未打补丁的 3.10 和 3.11 版保持兼容的代码必须将所有 [`io.TextIOWrapper`]({{< ref "/library/allos/io#io.TextIOWrapper" >}}) 参数，包括 `encoding` 作为关键字参数传入。

## Path.**iterdir**()

​	枚举当前目录的子目录。

## Path.**is_dir**()

​	如果当前上下文引用了一个目录则返回 `True`。

## Path.**is_file**()

​	如果当前上下文引用了一个文件则返回 `True`。

## Path.**is_symlink**()

​	如果当前上下文引用了一个符号链接则返回 `True`。

> Added in version 3.12.
>

> 在 3.13 版本发生变更: 在之前版本中，`is_symlink` 将无条件地返回 `False`。

## Path.**exists**()

​	如果当前上下文引用了 zip 文件内的一个文件或目录则返回 `True`。

## Path.**suffix**

​	最终组件末尾的以点号分隔的部分，如果存在的话。 这通常被称为文件扩展名。

> Added in version 3.11:
> 添加了 [`Path.suffix`]({{< ref "/library/archiving/zipfile#zipfile.Path.suffix" >}}) 特征属性。

## Path.**stem**

​	路径的末尾部分，不带文件后缀。

> Added in version 3.11:
> 添加了 [`Path.stem`]({{< ref "/library/archiving/zipfile#zipfile.Path.stem" >}}) 特征属性。

## Path.**suffixes**

​	由路径后缀组成的列表，通常被称为文件扩展名。

> Added in version 3.11:
> 添加了 [`Path.suffixes`]({{< ref "/library/archiving/zipfile#zipfile.Path.suffixes" >}}) 特征属性。

## Path.**read_text**(***, ****)

​	读取当前文件为 unicode 文本。 位置和关键字参数会被传递给 [`io.TextIOWrapper`]({{< ref "/library/allos/io#io.TextIOWrapper" >}}) (`buffer` 除外，它将由上下文确定)。

*在 3.11.2 版本发生变更:* `encoding` 形参可以作为位置参数来提供而不会引起 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。 这种情况在 3.9 中是会发生的。 需要与未打补丁的 3.10 和 3.11 版保持兼容的代码必须将所有 [`io.TextIOWrapper`]({{< ref "/library/allos/io#io.TextIOWrapper" >}}) 参数，包括 `encoding` 作为关键字参数传入。

## Path.**read_bytes**()

​	读取当前文件为字节串。

## Path.**joinpath**(**other*)

​	返回一个新的 Path 对象，其中合并了每个 *other* 参数。 以下代码是等价的:



``` python
>>> Path(...).joinpath('child').joinpath('grandchild')
>>> Path(...).joinpath('child', 'grandchild')
>>> Path(...) / 'child' / 'grandchild'
```

> 在 3.10 版本发生变更: 在 3.10 之前，`joinpath` 未被写入文档并且只接受一个形参。

[zipp](https://pypi.org/project/zipp/) 项目向较旧版本的 Python 提供了最新路径对象功能的向下移植。 为尽早应用这些改变请使用 `zipp.Path` 来替代 `zipfile.Path`。



## PyZipFile 对象

[`PyZipFile`]({{< ref "/library/archiving/zipfile#zipfile.PyZipFile" >}}) 构造器接受与 [`ZipFile`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile" >}}) 构造器相同的形参，以及一个额外的形参 *optimize*。

## *class* zipfile.**PyZipFile**(*file*, *mode='r'*, *compression=ZIP_STORED*, *allowZip64=True*, *optimize=-1*)

> 在 3.2 版本发生变更: 增加了 *optimize* 形参。

> 在 3.4 版本发生变更: 默认启用 ZIP64 扩展。

​	实例在 [`ZipFile`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile" >}}) 对象所具有的方法以外还附加了一个方法:

## **writepy**(*pathname*, *basename=''*, *filterfunc=None*)

​	查找 `*.py` 文件并将相应的文件添加到归档。

​	如果 [`PyZipFile`]({{< ref "/library/archiving/zipfile#zipfile.PyZipFile" >}}) 的 *optimize* 形参未给定或为 `-1`，则相应的文件为 `*.pyc` 文件，并在必要时进行编译。

​	如果 [`PyZipFile`]({{< ref "/library/archiving/zipfile#zipfile.PyZipFile" >}}) 的 *optimize* 形参为 `0`, `1` 或 `2`，则限具有相应优化级别 (参见 [`compile()`]({{< ref "/library/functions#compile" >}})) 的文件会被添加到归档，并在必要时进行编译。

​	如果 *pathname* 是文件，则文件名必须以 `.py` 为后缀，并且只有 (相应的 `*.pyc`) 文件会被添加到最高层级（不带路径信息）。 如果 *pathname* 不是以 `.py` 为后缀的文件，则将引发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。 如果它是目录，并且该目录不是一个包目录，则所有的 `*.pyc` 文件会被添加到最高层级。 如果目录是一个包目录，则所有的 `*.pyc` 会被添加到包名所表示的文件路径下，并且如果有任何子目录为包目录，则会以排好的顺序递归地添加这些目录。

*basename* 仅限在内部使用。

​	如果给定 *filterfunc*，则它必须是一个接受单个字符串参数的函数。 在将其添加到归档之前它将被传入每个路径（包括每个单独的完整路径）。 如果 *filterfunc* 返回假值，则路径将不会被添加，而如果它是一个目录则其内容将被忽略。 例如，如果我们的测试文件全都位于 `test` 目录或以字符串 `test_` 打头，则我们可以使用一个 *filterfunc* 来排除它们:



``` python
>>> zf = PyZipFile('myprog.zip')
>>> def notests(s):
...     fn = os.path.basename(s)
...     return (not (fn == 'test' or fn.startswith('test_')))
...
>>> zf.writepy('myprog', filterfunc=notests)
```

[`writepy()`]({{< ref "/library/archiving/zipfile#zipfile.PyZipFile.writepy" >}}) 方法会产生带有这样一些文件名的归档:

```
string.pyc                   # 最高层级名称
test/__init__.pyc            # 包目录
test/testall.pyc             # 模块 test.testall
test/bogus/__init__.pyc      # 子包目录
test/bogus/myfile.pyc        # 子模块 test.bogus.myfile
```

> 在 3.4 版本发生变更: 增加了 *filterfunc* 形参。

*在 3.6.2 版本发生变更:* *pathname* 形参接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

> 在 3.7 版本发生变更: 递归排序目录条目。



## ZipInfo 对象

[`ZipInfo`]({{< ref "/library/archiving/zipfile#zipfile.ZipInfo" >}}) 类的实例会通过 [`getinfo()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.getinfo" >}}) 和 [`ZipFile`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile" >}}) 对象的 [`infolist()`]({{< ref "/library/archiving/zipfile#zipfile.ZipFile.infolist" >}}) 方法返回。 每个对象将存储关于 ZIP 归档的一个成员的信息。

​	有一个类方法可以为文件系统文件创建 [`ZipInfo`]({{< ref "/library/archiving/zipfile#zipfile.ZipInfo" >}}) 实例:

## *classmethod* ZipInfo.**from_file**(*filename*, *arcname=None*, ***, *strict_timestamps=True*)

​	为文件系统中的文件构造一个 [`ZipInfo`]({{< ref "/library/archiving/zipfile#zipfile.ZipInfo" >}}) 实例，并准备将其添加到一个 zip 文件。

*filename* 应为文件系统中某个文件或目录的路径。

​	如果指定了 *arcname*，它会被用作归档中的名称。 如果未指定 *arcname*，则所用名称与 *filename* 相同，但将去除任何驱动器盘符和打头的路径分隔符。

*strict_timestamps* 参数在设为 `False` 时允许压缩早于 1980-01-01 的文件，代价时会将时间戳设为 1980-01-01。 类似的行为也会对晚于 2107-12-31 的文件发生，时间戳也会被设为该上限值。

> Added in version 3.6.
>

*在 3.6.2 版本发生变更:* *filename* 形参接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

> 在 3.8 版本发生变更: 增加了 *strict_timestamps* 仅限关键字形参。

​	实例具有下列方法和属性:

## ZipInfo.**is_dir**()

​	如果此归档成员是一个目录则返回 `True`。

​	这会使用条目的名称：目录应当总是以 `/` 结尾。

> Added in version 3.6.
>

## ZipInfo.**filename**

​	归档中的文件名称。

## ZipInfo.**date_time**

​	上次修改存档成员的时间和日期。这是六个值的元组：

| 索引 | 值                      |
| :--- | :---------------------- |
| `0`  | Year (>= 1980)          |
| `1`  | 月（1为基数）           |
| `2`  | 月份中的日期（1为基数） |
| `3`  | 小时（0为基数）         |
| `4`  | 分钟（0为基数）         |
| `5`  | 秒（0为基数）           |

​备注
 

​	ZIP文件格式不支持1980年以前的时间戳。

## ZipInfo.**compress_type**

​	归档成员的压缩类型。

## ZipInfo.**comment**

[`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 对象形式的单个归档成员的注释。

## ZipInfo.**extra**

​	扩展字段数据。 [PKZIP Application Note](https://pkware.cachefly.net/webdocs/casestudies/APPNOTE.TXT) 包含一些保存于该 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 对象中的内部结构的注释。

## ZipInfo.**create_system**

​	创建 ZIP 归档所用的系统。

## ZipInfo.**create_version**

​	创建 ZIP 归档所用的 PKZIP 版本。

## ZipInfo.**extract_version**

​	需要用来提取归档的 PKZIP 版本。

## ZipInfo.**reserved**

​	必须为零。

## ZipInfo.**flag_bits**

​	ZIP 标志位。

## ZipInfo.**volume**

​	文件头的分卷号。

## ZipInfo.**internal_attr**

​	内部属性。

## ZipInfo.**external_attr**

​	外部文件属性。

## ZipInfo.**header_offset**

​	文件头的字节偏移量。

## ZipInfo.**CRC**

​	未压缩文件的 CRC-32。

## ZipInfo.**compress_size**

​	已压缩数据的大小。

## ZipInfo.**file_size**

​	未压缩文件的大小。



## 命令行接口

[`zipfile`]({{< ref "/library/archiving/zipfile#module-zipfile" >}}) 模块提供了简单的命令行接口用于与 ZIP 归档的交互。

​	如果你想要创建一个新的 ZIP 归档，请在 [`-c`]({{< ref "/library/archiving/zipfile#cmdoption-zipfile-c" >}}) 选项后指定其名称然后列出应当被包含的文件名:

```
$ python -m zipfile -c monty.zip spam.txt eggs.txt
```

​	传入一个目录也是可接受的:

```
$ python -m zipfile -c monty.zip life-of-brian_1979/
```

​	如果你想要将一个 ZIP 归档提取到指定的目录，请使用 [`-e`]({{< ref "/library/archiving/zipfile#cmdoption-zipfile-e" >}}) 选项:

```
$ python -m zipfile -e monty.zip target-dir/
```

​	要获取一个 ZIP 归档中的文件列表，请使用 [`-l`]({{< ref "/library/archiving/zipfile#cmdoption-zipfile-l" >}}) 选项:

```
$ python -m zipfile -l monty.zip
```

### 命令行选项

## **-l** <zipfile>

## `--list` <zipfile>

​	列出一个 zipfile 中的文件名。

## **-c** <zipfile> <source1> ... <sourceN>

## `--create` <zipfile> <source1> ... <sourceN>

​	基于源文件创建 zipfile。

## **-e** <zipfile> <output_dir>

## `--extract` <zipfile> <output_dir>

​	将 zipfile 提取到目标目录中。

## **-t** <zipfile>

## `--test` <zipfile>

​	检测 zipfile 是否有效。

## `--metadata-encoding` <encoding>

​	为 [`-l`]({{< ref "/library/archiving/zipfile#cmdoption-zipfile-l" >}}), [`-e`]({{< ref "/library/archiving/zipfile#cmdoption-zipfile-e" >}}) 和 [`-t`]({{< ref "/library/archiving/zipfile#cmdoption-zipfile-t" >}}) 指定成员名称的编码格式。

> Added in version 3.11.
>

## 解压缩的障碍

​	zipfile 模块的提取操作可能会由于下面列出的障碍而失败。

### 由于文件本身

​	解压缩可能由于不正确的密码 / CRC 校验和 / ZIP 格式或不受支持的压缩 / 解密方法而失败。

### 文件系统限制

​	超出特定文件系统上的限制可能会导致解压缩失败。 例如目录条目所允许的字符、文件名的长度、路径名的长度、单个文件的大小以及文件的数量等等。



### 资源限制

​	缺乏内存或磁盘空间将会导致解压缩失败。 例如，作用于 zipfile 库的解压缩炸弹 (即 [ZIP bomb](https://en.wikipedia.org/wiki/Zip_bomb)) 就可能造成磁盘空间耗尽。

### 中断

​	在解压缩期间中断执行，例如按下 ctrl-C 或杀死解压缩进程可能会导致归档文件的解压缩不完整。

### 提取的默认行为

​	不了解提取的默认行为可能导致不符合期望的解压缩结果。 例如，当提取相同归档两次时，它会不经询问地覆盖文件。
