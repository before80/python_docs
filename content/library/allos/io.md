+++
title = "io --- 处理流的核心工具"
date = 2024-11-15T12:09:25+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/io.html](https://docs.python.org/zh-cn/3.13/library/io.html)
>
> 收录该文档的时间：`2024-11-15T12:09:25+08:00`

# `io` --- 处理流的核心工具

**源代码:** [Lib/io.py](https://github.com/python/cpython/tree/3.13/Lib/io.py)

------



## 概述

[`io`](https://docs.python.org/zh-cn/3.13/library/io.html#module-io) 模块提供了 Python 用于处理各种 I/O 类型的主要工具。三种主要的 I/O类型分别为: *文本 I/O*, *二进制 I/O* 和 *原始 I/O*。这些是泛型类型，有很多种后端存储可以用在他们上面。一个隶属于任何这些类型的具体对象被称作 [file object](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-object)。 其他同类的术语还有 *流* 和 *类文件对象*。

​	独立于其类别，每个具体流对象也将具有各种功能：它可以是只读，只写或读写。它还可以允许任意随机访问（向前或向后寻找任何位置），或仅允许顺序访问（例如在套接字或管道的情况下）。

​	所有流对提供给它们的数据类型都很敏感。 例如将 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 对象提供给二进制流的 `write()` 方法将引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。 将 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 对象提供给文本流的 `write()` 方法也是如此。

*在 3.3 版本发生变更:* 由于 [`IOError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#IOError) 现在是 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 的别名，因此用于引发 [`IOError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#IOError) 的操作现在会引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 。

### 文本 I/O

​	文本I/O预期并生成 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 对象。这意味着，无论何时后台存储是由字节组成的（例如在文件的情况下），数据的编码和解码都是透明的，并且可以选择转换特定于平台的换行符。

​	创建文本流的最简单方式是通过 [`open()`](https://docs.python.org/zh-cn/3.13/library/functions.html#open)，并可选择指定编码格式:

```
f = open("myfile.txt", "r", encoding="utf-8")
```

​	内存中文本流也可以作为 [`StringIO`](https://docs.python.org/zh-cn/3.13/library/io.html#io.StringIO) 对象使用：

```
f = io.StringIO("some initial text data")
```

[`TextIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOBase) 的文档中详细描述了文本流的API

### 二进制 I/O

​	二进制I/O（也称为缓冲I/O）预期 [bytes-like objects](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) 并生成 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 对象。不执行编码、解码或换行转换。这种类型的流可以用于所有类型的非文本数据，并且还可以在需要手动控制文本数据的处理时使用。

​	创建二进制流的最简单方式是通过 [`open()`](https://docs.python.org/zh-cn/3.13/library/functions.html#open) 并在模式字符串中使用 `'b'`:

```
f = open("myfile.jpg", "rb")
```

​	内存中二进制流也可以作为 [`BytesIO`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BytesIO) 对象使用：

```
f = io.BytesIO(b"some initial binary data: \x00\x01")
```

[`BufferedIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase) 的文档中详细描述了二进制流 API。

​	其他库模块可以提供额外的方式来创建文本或二进制流。参见 [`socket.socket.makefile()`](https://docs.python.org/zh-cn/3.13/library/socket.html#socket.socket.makefile) 的示例。

### 原始 I/O

​	原始 I/O（也称为 *非缓冲 I/O*）通常用作二进制和文本流的低级构建块。用户代码直接操作原始流的用法非常罕见。不过，可以通过在禁用缓冲的情况下以二进制模式打开文件来创建原始流：

```
f = open("myfile.jpg", "rb", buffering=0)
```

[`RawIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase) 的文档中详细描述了原始流的API



## 文本编码格式

[`TextIOWrapper`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOWrapper) 和 [`open()`](https://docs.python.org/zh-cn/3.13/library/functions.html#open) 的默认编码格式取决于语言区域的设置 ([`locale.getencoding()`](https://docs.python.org/zh-cn/3.13/library/locale.html#locale.getencoding))。

​	但是，很多开发者在打开以 UTF-8 编码的文本文件 (例如 JSON, TOML, Markdown 等等...) 时会忘记指定编码格式，因为大多数 Unix 平台默认使用 UTF-8 语言区域。 这会导致各种错误因为大多数 Windows 用户的语言区域编码格式并不是 UTF-8。 例如:

```
# 当文件中有非 ASCII 字符时可能无法在 Windows 下使用。
with open("README.md") as f:
    long_description = f.read()
```

​	为此，强烈建议你在打开文本文件时显式地指定编码格式。 如果你想要使用 UTF-8，就传入 `encoding="utf-8"`。 要使用当前语言区域的编码格式，`encoding="locale"` 自 Python 3.10 开始已被支持。

​	参见

## [Python UTF-8 模式](https://docs.python.org/zh-cn/3.13/library/os.html#utf8-mode)

​	Python UTF-8 模式可以用来将默认编码格式由语言区域所确定的编码格式改为 UTF-8。

## [**PEP 686**](https://peps.python.org/pep-0686/)

​	Python 3.15 将把 [Python UTF-8 模式](https://docs.python.org/zh-cn/3.13/library/os.html#utf8-mode) 设为默认值。



### 选择性的 EncodingWarning

*Added in version 3.10:* 请参阅 [**PEP 597**](https://peps.python.org/pep-0597/) 了解详情。

​	要找出哪里使用了默认语言区域的编码格式，你可以启用 [`-X warn_default_encoding`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-X) 命令行选项或设置 [`PYTHONWARNDEFAULTENCODING`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONWARNDEFAULTENCODING) 环境变量，这将在使用默认编码格式时发出 [`EncodingWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#EncodingWarning)。

​	如果你提供了使用 [`open()`](https://docs.python.org/zh-cn/3.13/library/functions.html#open) 或 [`TextIOWrapper`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOWrapper) 的 API 并将 `encoding=None` 作为形参传入，你可以使用 [`text_encoding()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.text_encoding) 以便 API 的调用方在没有传入 `encoding` 的时候将发出 [`EncodingWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#EncodingWarning)。 但是，对于新的 API 请考虑默认就使用 UTF-8 (即 `encoding="utf-8"`)。

## 高阶模块接口

## io.**DEFAULT_BUFFER_SIZE**

​	包含模块缓冲 I/O 类使用的默认缓冲区大小的 int。 在可能的情况下 [`open()`](https://docs.python.org/zh-cn/3.13/library/functions.html#open) 将使用文件的 blksize（由 [`os.stat()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.stat) 获得）。

## io.**open**(*file*, *mode='r'*, *buffering=-1*, *encoding=None*, *errors=None*, *newline=None*, *closefd=True*, *opener=None*)

​	这是内置的 [`open()`](https://docs.python.org/zh-cn/3.13/library/functions.html#open) 函数的别名。

​	此函数将引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `open` 并附带参数 *path*, *mode* 和 *flags*。 *mode* 和 *flags* 参数可能在原始调用的基础上修改或推断得到。

## io.**open_code**(*path*)

​	以 `'rb'` 模式打开提供的文件。如果目的是将文件内容做为可执行代码，则应使用此函数。

*path* 应当为 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 类型并且是一个绝对路径。

​	该函数的行为可通过先期调用 [`PyFile_SetOpenCodeHook()`](https://docs.python.org/zh-cn/3.13/c-api/file.html#c.PyFile_SetOpenCodeHook) 来重写。 不过，假如 *path* 为 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 类型并且是一个绝对路径，`open_code(path)` 的行为应当总是与 `open(path, 'rb')` 一致。 重写行为的目的是为了给文件附加额外的验证或预处理。

*Added in version 3.8.*

## io.**text_encoding**(*encoding*, *stacklevel=2*, */*)

​	这是一个针对使用 [`open()`](https://docs.python.org/zh-cn/3.13/library/functions.html#open) 或 [`TextIOWrapper`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOWrapper) 的可调用对象的辅助函数并且具有 `encoding=None` 形参。

​	如果 *encoding* 不为 `None` 则将其返回。 还其他情况下，它将根据是否启用了 [UTF-8 模式](https://docs.python.org/zh-cn/3.13/library/os.html#utf8-mode) 返回 `"locale"` 或 `"utf-8"`。

​	如果 [`sys.flags.warn_default_encoding`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.flags) 为真值且 *encoding* 为 `None` 则此函数会发出 [`EncodingWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#EncodingWarning)。 *stacklevel* 指明警告在哪里发出。 例如:

```
def read_text(path, encoding=None):
    encoding = io.text_encoding(encoding)  # stacklevel=2
    with open(path, encoding) as f:
        return f.read()
```

​	在这个例子中，将为 `read_text()` 的调用方发出 [`EncodingWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#EncodingWarning)。

​	请参阅 [文本编码格式](https://docs.python.org/zh-cn/3.13/library/io.html#io-text-encoding) 了解更多信息。

*Added in version 3.10.*

*在 3.11 版本发生变更:* 当启用 UTF-8 模式且 *encoding* 为 `None` 时 [`text_encoding()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.text_encoding) 将返回 "utf-8"。

## *exception* io.**BlockingIOError**

​	这是内置的 [`BlockingIOError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BlockingIOError) 异常的兼容性别名。

## *exception* io.**UnsupportedOperation**

​	在流上调用不支持的操作时引发的继承 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 和 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError) 的异常。

​	参见

## [`sys`](https://docs.python.org/zh-cn/3.13/library/sys.html#module-sys)

​	包含标准IO流: [`sys.stdin`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stdin), [`sys.stdout`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stdout) 和 [`sys.stderr`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stderr) 。

## 类的层次结构

​	I/O 流被安排为按类的层次结构实现。 首先是 [抽象基类](https://docs.python.org/zh-cn/3.13/glossary.html#term-abstract-base-class) (ABC)，用于指定流的各种类别，然后是提供标准流实现的具体类。

​	备注

 

​	这些抽象基类还提供了一些方法的默认实现，以帮助实现具体的流类。 例如，[`BufferedIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase) 提供了 `readinto()` 和 `readline()` 的未优化实现。

​	I/O层次结构的顶部是抽象基类 [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 。它定义了流的基本接口。但是请注意，对流的读取和写入之间没有分离。如果实现不支持指定的操作，则会引发 [`UnsupportedOperation`](https://docs.python.org/zh-cn/3.13/library/io.html#io.UnsupportedOperation) 。

​	抽象基类 [`RawIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase) 是 [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 的子类。它负责将字节读取和写入流中。 [`RawIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase) 的子类 [`FileIO`](https://docs.python.org/zh-cn/3.13/library/io.html#io.FileIO) 提供计算机文件系统中文件的接口。

​	抽象基类 [`BufferedIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase) 扩展了 [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase)。 它能处理原始二进制流 ([`RawIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase)) 上的缓冲。 它的子类 [`BufferedWriter`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedWriter), [`BufferedReader`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedReader) 和 [`BufferedRWPair`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedRWPair) 分别缓冲可写、可读以及同时可读写的原始二进制流。 [`BufferedRandom`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedRandom) 提供了带缓冲的可随机访问接口。 [`BufferedIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase) 的另一子类 [`BytesIO`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BytesIO) 是内存中字节流。

​	抽象基类 [`TextIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOBase) 继承了 [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 。它处理可表示文本的流，并处理字符串的编码和解码。类 [`TextIOWrapper`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOWrapper) 继承了 [`TextIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOBase) ，是原始缓冲流（ [`BufferedIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase) ）的缓冲文本接口。最后， [`StringIO`](https://docs.python.org/zh-cn/3.13/library/io.html#io.StringIO) 是文本的内存流。

​	参数名不是规范的一部分，只有 [`open()`](https://docs.python.org/zh-cn/3.13/library/functions.html#open) 的参数才用作关键字参数。

​	下表总结了抽象基类提供的 [`io`](https://docs.python.org/zh-cn/3.13/library/io.html#module-io) 模块：

| 抽象基类                                                     | 继承                                                         | 抽象方法                                 | Mixin方法和属性                                              |
| :----------------------------------------------------------- | :----------------------------------------------------------- | :--------------------------------------- | :----------------------------------------------------------- |
| [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) |                                                              | `fileno`, `seek`, 和 `truncate`          | `close`, `closed`, `__enter__`, `__exit__`, `flush`, `isatty`, `__iter__`, `__next__`, `readable`, `readline`, `readlines`, `seekable`, `tell`, `writable` 和 `writelines` |
| [`RawIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase) | [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) | `readinto` 和 `write`                    | 继承 [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 方法, `read`, 和 `readall` |
| [`BufferedIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase) | [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) | `detach`, `read`, `read1`, 和 `write`    | 继承 [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 方法, `readinto`, 和 `readinto1` |
| [`TextIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOBase) | [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) | `detach`, `read`, `readline`, 和 `write` | 继承 [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 方法, `encoding`, `errors`, 和 `newlines` |

### I/O 基类

## *class* io.**IOBase**

​	所有 I/O 类的抽象基类。

​	此类为许多方法提供了空的抽象实现，派生类可以有选择地重写。默认实现代表一个无法读取、写入或查找的文件。

​	尽管 [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 并未声明 `read()` 或 `write()`，因为它们的签名会有所不同，但是实现和客户端应该将这些方法视为接口的一部分。 此外，当调用不支持的操作时实现可能会引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError) (或 [`UnsupportedOperation`](https://docs.python.org/zh-cn/3.13/library/io.html#io.UnsupportedOperation))。

​	从文件读取或写入文件的二进制数据的基本类型为 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 。其他 [bytes-like objects](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) 也可以作为方法参数。文本I/O类使用 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 数据。

​	请注意，在关闭的流上调用任何方法（甚至查询）都是未定义的（undefined）。在这种情况下，实现可能会引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError) 。

[`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) （及其子类）支持迭代器协议，这意味着可以迭代 [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 对象以产生流中的行。根据流是二进制流（产生字节）还是文本流（产生字符串），行的定义略有不同。请参见下面的 [`readline()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.readline) 。

[`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 也是一个上下文管理器，因此支持 [`with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#with) 语句。 在这个示例中，*file* 将在 `with` 语句块执行完成之后被关闭 --- 即使是发生了异常:

```
with open('spam.txt', 'w') as file:
    file.write('Spam and eggs!')
```

[`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 提供以下数据属性和方法：

## **close**()

​	刷新并关闭此流。如果文件已经关闭，则此方法无效。文件关闭后，对文件的任何操作（例如读取或写入）都会引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError) 。

​	为方便起见，允许多次调用此方法。但是，只有第一个调用才会生效。

## **closed**

​	如果流已关闭，则返回 True。

## **fileno**()

​	返回流的底层文件描述符（整数）---如果存在。如果 IO 对象不使用文件描述符，则会引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 。

## **flush**()

​	刷新流的写入缓冲区（如果适用）。这对只读和非阻塞流不起作用。

## **isatty**()

​	如果流是交互式的（即连接到终端/tty设备），则返回 `True` 。

## **readable**()

​	如果可以读取流则返回 `True`。 如果返回 `False`，则 `read()` 将引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError)。

## **readline**(*size=-1*, */*)

​	从流中读取并返回一行。如果指定了 *size*，将至多读取 *size* 个字节。

​	对于二进制文件行结束符总是 `b'\n'`；对于文本文件，可以用将 *newline* 参数传给 [`open()`](https://docs.python.org/zh-cn/3.13/library/functions.html#open) 的方式来选择要识别的行结束符。

## **readlines**(*hint=-1*, */*)

​	从流中读取并返回包含多行的列表。可以指定 *hint* 来控制要读取的行数：如果（以字节/字符数表示的）所有行的总大小超出了 *hint* 则将不会读取更多的行。

`0` 或更小的 *hint* 值以及 `None`，会被视为没有 hint。

​	请注意，在不调用 `file.readlines()` 的情况下使用 `for line in file: ...` 来遍历文件对象已经成为可能。

## **seek**(*offset*, *whence=os.SEEK_SET*, */*)

​	将流位置修改到给定的字节 *offset*，它将相对于 *whence* 所指明的位置进行解析，并返回新的绝对位置。 *whence* 的可用值有:

- [`os.SEEK_SET`](https://docs.python.org/zh-cn/3.13/library/os.html#os.SEEK_SET) 或 `0` -- 流的开头（默认值；*offset* 应为零或正值
- [`os.SEEK_CUR`](https://docs.python.org/zh-cn/3.13/library/os.html#os.SEEK_CUR) 或 `1` -- 当前流位置；*offset* 可以为负值
- [`os.SEEK_END`](https://docs.python.org/zh-cn/3.13/library/os.html#os.SEEK_END) 或 `2` -- 流的末尾；*offset* 通常为负值

*Added in version 3.1:* `SEEK_*` 常量。

*Added in version 3.3:* 某些操作系统还可支持其他的值，如 [`os.SEEK_HOLE`](https://docs.python.org/zh-cn/3.13/library/os.html#os.SEEK_HOLE) 或 [`os.SEEK_DATA`](https://docs.python.org/zh-cn/3.13/library/os.html#os.SEEK_DATA)。 特定文件的可用值还会取决于它是以文本还是二进制模式打开。

## **seekable**()

​	如果流支持随机访问则返回 `True`。 如为 `False`，则 [`seek()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.seek), [`tell()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.tell) 和 [`truncate()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.truncate) 将引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError)。

## **tell**()

​	返回当前流的位置。

## **truncate**(*size=None*, */*)

​	将流的大小调整为给定的 *size* 个字节（如果未指定 *size* 则调整至当前位置）。 当前的流位置不变。 这个调整操作可扩展或减小当前文件大小。 在扩展的情况下，新文件区域的内容取决于具体平台（在大多数系统上，额外的字节会填充为零）。 返回新的文件大小。

*在 3.5 版本发生变更:* 现在Windows在扩展时将文件填充为零。

## **writable**()

​	如果流支持写入则返回 `True`。 如为 `False`，则 `write()` 和 [`truncate()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.truncate) 将引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError)。

## **writelines**(*lines*, */*)

​	将行列表写入到流。 不会添加行分隔符，因此通常所提供的每一行都带有末尾行分隔符。

## **__del__**()

​	为对象销毁进行准备。 [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 提供了此方法的默认实现，该实现会调用实例的 [`close()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.close) 方法。

## *class* io.**RawIOBase**

​	原始二进制流的基类。 它继承自 [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase)。

​	原始二进制流通常会提供对下层 OS 设备或 API 的低层级访问，而不是尝试将其封装到高层级的基元中（此功能是在更高层级的缓冲二进制流和文本流中实现的，将在下文中描述）。

[`RawIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase) 在 [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 的现有成员以外还提供了下列方法:

## **read**(*size=-1*, */*)

​	从对象中读取 *size* 个字节并将其返回。 作为一个便捷选项，如果 *size* 未指定或为 -1，则返回所有字节直到 EOF。 在其他情况下，仅会执行一次系统调用。 如果操作系统调用返回字节数少于 *size* 则此方法也可能返回少于 *size* 个字节。

​	如果返回 0 个字节而 *size* 不为零 0，这表明到达文件末尾。 如果处于非阻塞模式并且没有更多字节可用，则返回 `None`。

​	默认实现会转至 [`readall()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase.readall) 和 [`readinto()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase.readinto)。

## **readall**()

​	从流中读取并返回所有字节直到 EOF，如有必要将对流执行多次调用。

## **readinto**(*b*, */*)

​	将字节数据读入预先分配的可写 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) *b*，并返回所读取的字节数。 例如，*b* 可以是一个 [`bytearray`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytearray)。 如果对象处理非阻塞模式并且没有更多字节可用，则返回 `None`。

## **write**(*b*, */*)

​	将给定的 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) *b* 写入到下层的原始流，并返回所写入的字节数。 这可以少于 *b* 的总字节数，具体取决于下层原始流的设定，特别是如果它处于非阻塞模式的话。 如果原始流设为非阻塞并且不能真正向其写入单个字节时则返回 `None`。 调用者可以在此方法返回后释放或改变 *b*，因此该实现应该仅在方法调用期间访问 *b*。

## *class* io.**BufferedIOBase**

​	支持某种缓冲的二进制流的基类。 它继承自 [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase)。

​	与 [`RawIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase) 的主要差别在于 [`read()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase.read), [`readinto()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase.readinto) 和 [`write()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase.write) 等方法将（分别）尝试按照要求读取尽可能多的输入或是耗尽所有给定的输出，其代价是可能会执行一次以上的系统调用。

​	除此之外，那些方法还可能引发 [`BlockingIOError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BlockingIOError)，如果下层的原始数据流处于非阻塞模式并且无法接受或给出足够数据的话；不同于对应的 [`RawIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase) 方法，它们将永远不会返回 `None`。

​	并且，[`read()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase.read) 方法也没有转向 [`readinto()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase.readinto) 的默认实现。

​	典型的 [`BufferedIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase) 实现不应当继承自 [`RawIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase) 实现，而要包装一个该实现，正如 [`BufferedWriter`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedWriter) 和 [`BufferedReader`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedReader) 所做的那样。

[`BufferedIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase) 在 [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 的现有成员以外还提供或重写了下列数据属性和方法:

## **raw**

​	由 [`BufferedIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase) 处理的下层原始流 ([`RawIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase) 的实例)。 它不是 [`BufferedIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase) API 的组成部分并且不存在于某些实现中。

## **detach**()

​	从缓冲区分离出下层原始流并将其返回。

​	在原始流被分离之后，缓冲区将处于不可用的状态。

​	某些缓冲区例如 [`BytesIO`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BytesIO) 并无可从此方法返回的单独原始流的概念。 它们将会引发 [`UnsupportedOperation`](https://docs.python.org/zh-cn/3.13/library/io.html#io.UnsupportedOperation)。

*Added in version 3.1.*

## **read**(*size=-1*, */*)

​	读取并返回最多 *size* 个字节。 如果此参数被省略、为 `None` 或为负值，则读取并返回所有数据直到 EOF。 如果流已经到达 EOF 则返回一个空的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 对象。

​	如果此参数为正值，并且下层原始流不可交互，则可能发起多个原始读取以满足字节计数（直至先遇到 EOF）。 但对于可交互原始流，则将至多发起一个原始读取，并且简短的结果并不意味着已到达 EOF。

[`BlockingIOError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BlockingIOError) 会在下层原始流不处于阻塞模式，并且当前没有可用数据时被引发。

## **read1**(*size=-1*, */*)

​	通过至多一次对下层流的 [`read()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase.read) (或 [`readinto()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase.readinto)) 方法的调用读取并返回至多 *size* 个字节。 这适用于在 [`BufferedIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase) 对象之上实现你自己的缓冲区的情况。

​	如果 *size* 为 `-1` (默认值)，则返回任意数量的字节（多于零字节，除非已到达 EOF）。

## **readinto**(*b*, */*)

​	将字节数据读入预先分配的可写 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) *b* 并返回所读取的字节数。 例如，*b* 可以是一个 [`bytearray`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytearray)。

​	类似于 [`read()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase.read)，可能对下层原始流发起多次读取，除非后者为交互式。

[`BlockingIOError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BlockingIOError) 会在下层原始流不处于阻塞模式，并且当前没有可用数据时被引发。

## **readinto1**(*b*, */*)

​	将字节数据读入预先分配的可写 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) *b*，其中至多使用一次对下层原始流 [`read()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase.read) (或 [`readinto()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase.readinto)) 方法的调用。 返回所读取的字节数。

[`BlockingIOError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BlockingIOError) 会在下层原始流不处于阻塞模式，并且当前没有可用数据时被引发。

*Added in version 3.5.*

## **write**(*b*, */*)

​	写入给定的 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) *b*，并返回写入的字节数 (总是等于 *b* 的字节长度，因为如果写入失败则会引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError))。 根据具体实现的不同，这些字节可能被实际写入下层流，或是出于运行效率和冗余等考虑而暂存于缓冲区。

​	当处于非阻塞模式时，如果需要将数据写入原始流但它无法在不阻塞的情况下接受所有数据则将引发 [`BlockingIOError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BlockingIOError)。

​	调用者可能会在此方法返回后释放或改变 *b*，因此该实现应当仅在方法调用期间访问 *b*。

### 原始文件 I/O

## *class* io.**FileIO**(*name*, *mode='r'*, *closefd=True*, *opener=None*)

​	代码一个包含字节数据的 OS 层级文件的原始二进制流。 它继承自 [`RawIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase)。

*name* 可以是以下两项之一：

- 代表将被打开的文件路径的字符串或 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 对象。 在此情况下 closefd 必须为 `True` (默认值) 否则将会引发异常。
- 代表一个现有 OS 层级文件描述符的号码的整数，作为结果的 [`FileIO`](https://docs.python.org/zh-cn/3.13/library/io.html#io.FileIO) 对象将可访问该文件。 当 FileIO 对象被关闭时此 fd 也将被关闭，除非 *closefd* 设为 `False`。

*mode* 可以为 `'r'`, `'w'`, `'x'` 或 `'a'` 分别表示读取（默认模式）、写入、独占新建或添加。 如果以写入或添加模式打开的文件不存在将自动新建；当以写入模式打开时文件将先清空。 以新建模式打开时如果文件已存在则将引发 [`FileExistsError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#FileExistsError)。 以新建模式打开文件也意味着要写入，因此该模式的行为与 `'w'` 类似。 在模式中附带 `'+'` 将允许同时读取和写入。

​	该类的 [`read()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase.read) (当附带为正值的参数调用时), [`readinto()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase.readinto) 和 [`write()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase.write) 方法将只执行一次系统调用。

​	可以通过传入一个可调用对象作为 *opener* 来使用自定义文件打开器。 然后通过调用 *opener* 并传入 (*name*, *flags*) 来获取文件对象所对应的下层文件描述符。 *opener* 必须返回一个打开文件描述符（传入 [`os.open`](https://docs.python.org/zh-cn/3.13/library/os.html#os.open) 作为 *opener* 的结果在功能上将与传入 `None` 类似）。

​	新创建的文件是 [不可继承的](https://docs.python.org/zh-cn/3.13/library/os.html#fd-inheritance)。

​	有关 opener 参数的示例，请参见内置函数 [`open()`](https://docs.python.org/zh-cn/3.13/library/functions.html#open) 。

*在 3.3 版本发生变更:* 增加了 *opener* 参数。增加了 `'x'` 模式。

*在 3.4 版本发生变更:* 文件现在禁止继承。

[`FileIO`](https://docs.python.org/zh-cn/3.13/library/io.html#io.FileIO) 在继承自 [`RawIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase) 和 [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 的现有成员以外还提供了以下数据属性和方法:

## **mode**

​	构造函数中给定的模式。

## **name**

​	文件名。当构造函数中没有给定名称时，这是文件的文件描述符。

### 缓冲流

​	相比原始 I/O，缓冲 I/O 流提供了针对 I/O 设备的更高层级接口。

## *class* io.**BytesIO**(*initial_bytes=b''*)

​	一个使用内存字节缓冲区的二进制流。 它继承自 [`BufferedIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase)。 当 [`close()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.close) 方法被调用时缓冲区将被丢弃。

​	可选参数 *initial_bytes* 是一个包含初始数据的 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object)。

[`BytesIO`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BytesIO) 在继承自 [`BufferedIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase) 和 [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 的成员以外还提供或重写了下列方法:

## **getbuffer**()

​	返回一个对应于缓冲区内容的可读写视图而不必拷贝其数据。 此外，改变视图将透明地更新缓冲区内容:

\>>>

```
>>> b = io.BytesIO(b"abcdef")
>>> view = b.getbuffer()
>>> view[2:4] = b"56"
>>> b.getvalue()
b'ab56ef'
```

​	备注

 

​	只要视图保持存在，[`BytesIO`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BytesIO) 对象就无法被改变大小或关闭。

*Added in version 3.2.*

## **getvalue**()

​	返回包含整个缓冲区内容的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)。

## **read1**(*size=-1*, */*)

​	在 [`BytesIO`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BytesIO) 中，这与 [`read()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase.read) 相同。

*在 3.7 版本发生变更:* *size* 参数现在是可选的。

## **readinto1**(*b*, */*)

​	在 [`BytesIO`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BytesIO) 中，这与 [`readinto()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase.readinto) 相同。

*Added in version 3.5.*

## *class* io.**BufferedReader**(*raw*, *buffer_size=DEFAULT_BUFFER_SIZE*)

​	一个提供对可读、不可定位的 [`RawIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase) 原始二进制流的高层级访问的缓冲二进制流。 它继承自 [`BufferedIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase)。

​	当从此对象读取数据时，可能会从下层原始流请求更大量的数据，并存放到内部缓冲区中。 接下来可以在后续读取时直接返回缓冲数据。

​	根据给定的可读 *raw* 流和 *buffer_size* 创建 [`BufferedReader`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedReader) 的构造器。 如果省略 *buffer_size*，则会使用 [`DEFAULT_BUFFER_SIZE`](https://docs.python.org/zh-cn/3.13/library/io.html#io.DEFAULT_BUFFER_SIZE)。

[`BufferedReader`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedReader) 在继承自 [`BufferedIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase) 和 [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 的成员以外还提供或重写了下列方法:

## **peek**(*size=0*, */*)

​	从流返回字节数据而不前移位置。 完成此调用将至多读取一次原始流。 返回的字节数量可能少于或多于请求的数量。

## **read**(*size=-1*, */*)

​	读取并返回 *size* 个字节，如果 *size* 未给定或为负值，则读取至 EOF 或是在非阻塞模式下读取调用将会阻塞。

## **read1**(*size=-1*, */*)

​	在原始流上通过单次调用读取并返回至多 *size* 个字节。 如果至少缓冲了一个字节，则只返回缓冲的字节。 在其他情况下，将执行一次原始流读取。

*在 3.7 版本发生变更:* *size* 参数现在是可选的。

## *class* io.**BufferedWriter**(*raw*, *buffer_size=DEFAULT_BUFFER_SIZE*)

​	一个提供对可写、不可定位的 [`RawIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase) 原始二进制流的高层级访问的缓冲二进制流。 它继承自 [`BufferedIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase)。

​	当写入到此对象时，数据通常会被放入到内部缓冲区中。 缓冲区将在满足某些条件的情况下被写到下层的 [`RawIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase) 对象，包括:

- 当缓冲区对于所有挂起数据而言太小时；
- 当 [`flush()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedWriter.flush) 被调用时；
- 当 [`seek()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.seek) 被请求时（针对 [`BufferedRandom`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedRandom) 对象）；
- 当 [`BufferedWriter`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedWriter) 对象被关闭或销毁时。

​	该构造器会为给定的可写 *raw* 流创建一个 [`BufferedWriter`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedWriter)。 如果未给定 *buffer_size*，则使用默认的 [`DEFAULT_BUFFER_SIZE`](https://docs.python.org/zh-cn/3.13/library/io.html#io.DEFAULT_BUFFER_SIZE)。

[`BufferedWriter`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedWriter) 在继承自 [`BufferedIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase) 和 [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 的成员以外还提供或重写了下列方法:

## **flush**()

​	将缓冲区中保存的字节数据强制放入原始流。 如果原始流发生阻塞则应当引发 [`BlockingIOError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BlockingIOError)。

## **write**(*b*, */*)

​	写入 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) *b* 并返回写入的字节数。 当处于非阻塞模式时，如果缓冲区需要被写入但原始流发生阻塞则将引发 [`BlockingIOError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BlockingIOError)。

## *class* io.**BufferedRandom**(*raw*, *buffer_size=DEFAULT_BUFFER_SIZE*)

​	一个提供对不可定位的 [`RawIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase) 原始二进制流的高层级访问的缓冲二进制流。 它继承自 [`BufferedReader`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedReader) 和 [`BufferedWriter`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedWriter)。

​	该构造器会为在第一个参数中给定的可查找原始流创建一个读取器和写入器。 如果省略 *buffer_size* 则使用默认的 [`DEFAULT_BUFFER_SIZE`](https://docs.python.org/zh-cn/3.13/library/io.html#io.DEFAULT_BUFFER_SIZE)。

[`BufferedRandom`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedRandom) 能做到 [`BufferedReader`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedReader) 或 [`BufferedWriter`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedWriter) 所能做的任何事。 此外，还会确保实现 [`seek()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.seek) 和 [`tell()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.tell)。

## *class* io.**BufferedRWPair**(*reader*, *writer*, *buffer_size=DEFAULT_BUFFER_SIZE*, */*)

​	一个提供对两个不可定位的 [`RawIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase) 原始二进制流的高层级访问的缓冲二进制流 --- 一个可读，另一个可写。 它继承自 [`BufferedIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase)。

*reader* 和 *writer* 分别是可读和可写的 [`RawIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase) 对象。 如果省略 *buffer_size* 则使用默认的 [`DEFAULT_BUFFER_SIZE`](https://docs.python.org/zh-cn/3.13/library/io.html#io.DEFAULT_BUFFER_SIZE)。

[`BufferedRWPair`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedRWPair) 实现了 [`BufferedIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase) 的所有方法，但 [`detach()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase.detach) 除外，调用该方法将引发 [`UnsupportedOperation`](https://docs.python.org/zh-cn/3.13/library/io.html#io.UnsupportedOperation)。

​	警告

 

[`BufferedRWPair`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedRWPair) 不会尝试同步访问其下层的原始流。 你不应当将传给它与读取器和写入器相同的对象；而要改用 [`BufferedRandom`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedRandom)。

### 文本 I/O

## *class* io.**TextIOBase**

​	文本流的基类。 该类提供了基于字符和行的流 I/O 的接口。 它继承自 [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase)。

[`TextIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOBase) 在来自 [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 的成员以外还提供或重写了以下数据属性和方法:

## **encoding**

​	用于将流的字节串解码为字符串以及将字符串编码为字节串的编码格式名称。

## **errors**

​	解码器或编码器的错误设置。

## **newlines**

​	一个字符串、字符串元组或者 `None`，表示目前已经转写的新行。 根据具体实现和初始构造器旗标的不同，此属性或许会不可用。

## **buffer**

​	由 [`TextIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOBase) 处理的下层二进制缓冲区（为一个 [`BufferedIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase) 的实例）。 它不是 [`TextIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOBase) API 的组成部分并且不存在于某些实现中。

## **detach**()

​	从 [`TextIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOBase) 分离出下层二进制缓冲区并将其返回。

​	在下层缓冲区被分离后，[`TextIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOBase) 将处于不可用的状态。

​	某些 [`TextIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOBase) 的实现，例如 [`StringIO`](https://docs.python.org/zh-cn/3.13/library/io.html#io.StringIO) 可能并无下层缓冲区的概念，因此调用此方法将引发 [`UnsupportedOperation`](https://docs.python.org/zh-cn/3.13/library/io.html#io.UnsupportedOperation)。

*Added in version 3.1.*

## **read**(*size=-1*, */*)

​	从流中读取至多 *size* 个字符并以单个 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 的形式返回。 如果 *size* 为负值或 `None`，则读取至 EOF。

## **readline**(*size=-1*, */*)

​	读取至换行符或 EOF 并返回单个 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)。 如果流已经到达 EOF，则将返回一个空字符串。, an empty string is returned.

​	如果指定了 *size* ，最多将读取 *size* 个字符。

## **seek**(*offset*, *whence=SEEK_SET*, */*)

​	将流位置改为给定的 *offset*。 具体行为取决于 *whence* 形参。 *whence* 的默认值为 `SEEK_SET`。

- `SEEK_SET` 或 `0`: 从流的起始位置开始查找（默认值）；*offset* 必须为 [`TextIOBase.tell()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOBase.tell) 所返回的数值或为零。 任何其他 *offset* 值都将导致未定义的行为。
- `SEEK_CUR` 或 `1`: "查找" 到当前位置；*offset* 必须为零，表示无操作（所有其他值均不受支持）。
- `SEEK_END` 或 `2`: 查找到流的末尾；*offset* 必须为零（所有其他值均不受支持）。

​	以不透明数字形式返回新的绝对位置。

*Added in version 3.1:* `SEEK_*` 常量。

## **tell**()

​	以不透明数字形式返回当前流的位置。 该数字通常并不代表下层二进制存储中对应的字节数。

## **write**(*s*, */*)

​	将字符串 *s* 写入到流并返回写入的字符数。

## *class* io.**TextIOWrapper**(*buffer*, *encoding=None*, *errors=None*, *newline=None*, *line_buffering=False*, *write_through=False*)

​	一个提供对 [`BufferedIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase) 缓冲二进制流的高层级访问的缓冲文本流。 它继承自 [`TextIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOBase)。

*encoding* 给出将被用于编码或解码流的编码格式。 该参数值默认为 [`locale.getencoding()`](https://docs.python.org/zh-cn/3.13/library/locale.html#locale.getencoding)。 可以使用 `encoding="locale"` 来显式地指定当前语言区域的编码格式。 详情参见 [文本编码格式](https://docs.python.org/zh-cn/3.13/library/io.html#io-text-encoding)。

*errors* 是一个可选的字符串，它指明编码格式和编码格式错误的处理方式。 传入 `'strict'` 将在出现编码格式错误时引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError) (默认值 `None` 具有相同的效果)，传入 `'ignore'` 将忽略错误。 (请注意忽略编码格式错误会导致数据丢失。) `'replace'` 会在出现错误数据时插入一个替换标记 (例如 `'?'`)。 `'backslashreplace'` 将把错误数据替换为一个反斜杠转义序列。 在写入时，还可以使用 `'xmlcharrefreplace'` (替换为适当的 XML 字符引用) 或 `'namereplace'` (替换为 `\N{...}` 转义序列)。 任何其他通过 [`codecs.register_error()`](https://docs.python.org/zh-cn/3.13/library/codecs.html#codecs.register_error) 注册的错误处理方式名称也可以被接受。

*newline* 控制行结束符处理方式。 它可以为 `None`, `''`, `'\n'`, `'\r'` 和 `'\r\n'`。 其工作原理如下:

- 当从流读取输入时，如果 *newline* 为 `None`，则将启用 [universal newlines](https://docs.python.org/zh-cn/3.13/glossary.html#term-universal-newlines) 模式。 输入中的行结束符可以为 `'\n'`, `'\r'` 或 `'\r\n'`，在返回给调用者之前它们会被统一转写为 `'\n'`。 如果 *newline* 为 `''`，也会启用通用换行模式，但行结束符会不加转写即返回给调用者。 如果 *newline* 具有任何其他合法的值，则输入行将仅由给定的字符串结束，并且行结束符会不加转写即返回给调用者。
- 将输出写入流时，如果 *newline* 为 `None`，则写入的任何 `'\n'` 字符都将转换为系统默认行分隔符 [`os.linesep`](https://docs.python.org/zh-cn/3.13/library/os.html#os.linesep)。如果 *newline* 是 `''` 或 `'\n'`，则不进行翻译。如果 *newline* 是任何其他合法值，则写入的任何 `'\n'` 字符将被转换为给定的字符串。

​	如果 *line_buffering* 为 `True`，则当一个写入调用包含换行或回车符时将会应用 [`flush()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.flush)。

​	如果 *write_through* 为 `True`，则对 [`write()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedIOBase.write) 的调用会确保不被缓冲：在 [`TextIOWrapper`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOWrapper) 对象上写入的任何数据会立即交给其下层的 *buffer* 来处理。

*在 3.3 版本发生变更:* 已添加 *write_through* 参数

*在 3.3 版本发生变更:* 默认的 *encoding* 现在将为 `locale.getpreferredencoding(False)` 而非 `locale.getpreferredencoding()`。 不要使用 [`locale.setlocale()`](https://docs.python.org/zh-cn/3.13/library/locale.html#locale.setlocale) 来临时改变区域编码格式，要使用当前区域编码格式而不是用户的首选编码格式。

*在 3.10 版本发生变更:* *encoding* 参数现在支持 `"locale"` 作为编码格式名称。

[`TextIOWrapper`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOWrapper) 在继承自 [`TextIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOBase) 和 [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 的现有成员以外还提供了以下数据属性和方法:

## **line_buffering**

​	是否启用行缓冲。

## **write_through**

​	写入是否要立即传给下层的二进制缓冲。

*Added in version 3.7.*

## **reconfigure**(***, *encoding=None*, *errors=None*, *newline=None*, *line_buffering=None*, *write_through=None*)

​	使用 *encoding*, *errors*, *newline*, *line_buffering* 和 *write_through* 的新设置来重新配置此文本流。

​	未指定的形参将保留当前设定，例外情况是当指定了 *encoding* 但未指定 *errors* 时将会使用 `errors='strict'`。

​	如果已经有数据从流中被读取则将无法再改变编码格式或行结束符。 另一方面，在写入数据之后再改变编码格式则是可以的。

​	此方法会在设置新的形参之前执行隐式的流刷新。

*Added in version 3.7.*

*在 3.11 版本发生变更:* 此方法支持 `encoding="locale"` 选项。

## **seek**(*cookie*, *whence=os.SEEK_SET*, */*)

​	设置流位置。 以 [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int) 的形式返回新的流位置。

​	支持四种操作，由下列参数组合给出：

- `seek(0, SEEK_SET)`: 回退到流的开头。
- `seek(cookie, SEEK_SET)`: 恢复之前的位置；*cookie* **必须是** 由 [`tell()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOWrapper.tell) 返回的数字。
- `seek(0, SEEK_END)`: 快进到流的末尾。
- `seek(0, SEEK_CUR)`: 保持当前流位置不变。

​	任何其他参数组合均无效，并可能引发异常。

​	参见

 

[`os.SEEK_SET`](https://docs.python.org/zh-cn/3.13/library/os.html#os.SEEK_SET), [`os.SEEK_CUR`](https://docs.python.org/zh-cn/3.13/library/os.html#os.SEEK_CUR) 和 [`os.SEEK_END`](https://docs.python.org/zh-cn/3.13/library/os.html#os.SEEK_END)。

## **tell**()

​	以不透明数字的形式返回流位置。 `tell()` 的返回值可以作为 [`seek()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOWrapper.seek) 的输入，以恢复之前的流位置。

## *class* io.**StringIO**(*initial_value=''*, *newline='\n'*)

​	一个使用内存文本缓冲区的文本流。 它继承自 [`TextIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOBase)。

​	当 [`close()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.close) 方法被调用时将会丢弃文本缓冲区。

​	缓冲区的初始值可通过提供 *initial_value* 来设置。 如果启用了换行符转写，换行符将以与 [`write()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOBase.write) 相同的方式进行编码。 流将被定位到缓冲区的起点，这模拟了以 `w+` 模式打开一个现有文件的操作，使其准备好从头开始立即写入或是将要覆盖初始值的写入。 要模拟以 `a+` 模式打开一个文件准备好追加内容，请使用 `f.seek(0, io.SEEK_END)` 来将流重新定位到缓冲区的末尾。

*newline* 参数的规则与 [`TextIOWrapper`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOWrapper) 所用的一致，不同之处在于当将输出写入到流时，如果 *newline* 为 `None`，则在所有平台上换行符都会被写入为 `\n`。

[`StringIO`](https://docs.python.org/zh-cn/3.13/library/io.html#io.StringIO) 在继承自 [`TextIOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOBase) 和 [`IOBase`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase) 的现有成员以外还提供了以下方法:

## **getvalue**()

​	返回一个 包含缓冲区全部内容的 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)。 换行符会以与 [`read()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOBase.read) 相同的方式被编码，但是流位置不会改变。

​	用法示例：

```
import io

output = io.StringIO()
output.write('First line.\n')
print('Second line.', file=output)

# 提取文件内容 -- 这将为
# 'First line.\nSecond line.\n'
contents = output.getvalue()

# 关闭对象并丢弃内存缓冲区 --
# 现在 .getvalue() 将引发一个异常。
output.close()
```

## *class* io.**IncrementalNewlineDecoder**

​	用于在 [universal newlines](https://docs.python.org/zh-cn/3.13/glossary.html#term-universal-newlines) 模式下解码换行符的辅助编解码器。 它继承自 [`codecs.IncrementalDecoder`](https://docs.python.org/zh-cn/3.13/library/codecs.html#codecs.IncrementalDecoder)。

## 性能

​	本节讨论所提供的具体 I/O 实现的性能。

### 二进制 I/O

​	即使在用户请求单个字节时，也只读取和写入大块数据。通过该方法，缓冲 I/O 隐藏了操作系统调用和执行无缓冲 I/O 例程时的任何低效性。增益取决于操作系统和执行的 I/O 类型。例如，在某些现代操作系统上（例如 Linux），无缓冲磁盘 I/O 可以与缓冲 I/O 一样快。但最重要的是，无论平台和支持设备如何，缓冲 I/O 都能提供可预测的性能。因此，对于二进制数据，应首选使用缓冲的 I/O 而不是未缓冲的 I/O 。

### 文本 I/O

​	二进制存储（如文件）上的文本 I/O 比同一存储上的二进制 I/O 慢得多，因为它需要使用字符编解码器在 unicode 和二进制数据之间进行转换。 在处理大量文本数据（如大型日志文件）时这种情况会非常明显。 此外，由于使用了重构算法因而 [`tell()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOBase.tell) 和 [`seek()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOBase.seek) 的速度都相当慢。

[`StringIO`](https://docs.python.org/zh-cn/3.13/library/io.html#io.StringIO) 是原生的内存 Unicode 容器，速度与 [`BytesIO`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BytesIO) 相似。

### 多线程

[`FileIO`](https://docs.python.org/zh-cn/3.13/library/io.html#io.FileIO) 对象在它们封装的操作系统调用 (如 Unix 下的 *[read(2)](https://manpages.debian.org/read(2))*) 是线程安全的情况下也是线程安全的。

​	二进制缓冲对象（例如 [`BufferedReader`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedReader), [`BufferedWriter`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedWriter), [`BufferedRandom`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedRandom) 和 [`BufferedRWPair`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedRWPair)）使用锁来保护其内部结构；因此，可以安全地一次从多个线程中调用它们。

[`TextIOWrapper`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOWrapper) 对象不再是线程安全的。

### 可重入性

​	二进制缓冲对象（ [`BufferedReader`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedReader) ， [`BufferedWriter`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedWriter) ， [`BufferedRandom`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedRandom) 和 [`BufferedRWPair`](https://docs.python.org/zh-cn/3.13/library/io.html#io.BufferedRWPair) 的实例）不是可重入的。虽然在正常情况下不会发生可重入调用，但仍可能会在 [`signal`](https://docs.python.org/zh-cn/3.13/library/signal.html#module-signal) 处理程序执行 I/O 时产生。如果线程尝试重入已经访问的缓冲对象，则会引发 [`RuntimeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#RuntimeError) 。注意，这并不禁止其他线程进入缓冲对象。

​	上面的代码将显式地扩展到文本文件，因为 [`open()`](https://docs.python.org/zh-cn/3.13/library/functions.html#open) 函数将把缓冲的对象包装在 [`TextIOWrapper`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOWrapper) 中。 这包括标准流因而也会影响内置的 [`print()`](https://docs.python.org/zh-cn/3.13/library/functions.html#print) 函数。
