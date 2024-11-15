+++
title = "fileinput --- 迭代来自多个输入流的行"
date = 2024-11-15T11:53:09+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/fileinput.html](https://docs.python.org/zh-cn/3.13/library/fileinput.html)
>
> 收录该文档的时间：`2024-11-15T11:53:09+08:00`

# `fileinput` --- 迭代来自多个输入流的行

**源代码:** [Lib/fileinput.py](https://github.com/python/cpython/tree/3.13/Lib/fileinput.py)

------

​	此模块实现了一个辅助类和一些函数用来快速编写访问标准输入或文件列表的循环。 如果你只想要读写一个文件请参阅 [`open()`](https://docs.python.org/zh-cn/3.13/library/functions.html#open)。

​	典型用法为:

```
import fileinput
for line in fileinput.input(encoding="utf-8"):
    process(line)
```

​	此程序会迭代 `sys.argv[1:]` 中列出的所有文件内的行，如果列表为空则会使用 `sys.stdin`。 如果有一个文件名为 `'-'`，它也会被替换为 `sys.stdin` 并且可选参数 *mode* 和 *openhook* 会被忽略。 要指定替代文件列表，请将其作为第一个参数传给 [`input()`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.input)。 也允许使用单个文件。

​	所有文件都默认以文本模式打开，但你可以通过在调用 [`input()`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.input) 或 [`FileInput`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.FileInput) 时指定 *mode* 形参来覆盖此行为。 如果在打开或读取文件时发生了 I/O 错误，将会引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError)。

*在 3.3 版本发生变更:* 原来会引发 [`IOError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#IOError)；现在它是 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 的别名。

​	如果 `sys.stdin` 被使用超过一次，则第二次之后的使用将不返回任何行，除非是被交互式的使用，或都是被显式地重置 (例如使用 `sys.stdin.seek(0)`)。

​	空文件打开后将立即被关闭；它们在文件列表中会被注意到的唯一情况只有当最后打开的文件为空的时候。

​	反回的行不会对换行符做任何处理，这意味着文件中的最后一行可能不带换行符。

​	你可以通过将 *openhook* 形参传给 [`fileinput.input()`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.input) 或 [`FileInput()`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.FileInput) 来提供一个打开钩子以控制文件的打开方式。 此钩子必须为一个函数，它接受两个参数，*filename* 和 *mode*，并返回一个以相应模式打开的文件型对象。 如果指定了 *encoding* 和/或 *errors*，它们将作为额外的关键字参数被传给这个钩子。 此模块提供了一个 [`hook_compressed()`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.hook_compressed) 来支持压缩文件。

​	以下函数是此模块的初始接口：

## fileinput.**input**(*files=None*, *inplace=False*, *backup=''*, ***, *mode='r'*, *openhook=None*, *encoding=None*, *errors=None*)

​	创建一个 [`FileInput`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.FileInput) 类的实例。 该实例将被用作此模块中函数的全局状态，并且还将在迭代期间被返回使用。 此函数的形参将被继续传递给 [`FileInput`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.FileInput) 类的构造器。

[`FileInput`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.FileInput) 实例可以在 [`with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#with) 语句中被用作上下文管理器。 在这个例子中，*input* 在 `with` 语句结束后将会被关闭，即使发生了异常也是如此:

```
with fileinput.input(files=('spam.txt', 'eggs.txt'), encoding="utf-8") as f:
    for line in f:
        process(line)
```

*在 3.2 版本发生变更:* 可以被用作上下文管理器。

*在 3.8 版本发生变更:* 关键字形参 *mode* 和 *openhook* 现在是仅限关键字形参。

*在 3.10 版本发生变更:* 增加了仅限关键字形参 *encoding* 和 *errors*。

​	下列函数会使用 [`fileinput.input()`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.input) 所创建的全局状态；如果没有活动的状态，则会引发 [`RuntimeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#RuntimeError)。

## fileinput.**filename**()

​	返回当前被读取的文件名。 在第一行被读取之前，返回 `None`。

## fileinput.**fileno**()

​	返回以整数表示的当前文件“文件描述符”。 当未打开文件时（处在第一行和文件之间），返回 `-1`。

## fileinput.**lineno**()

​	返回已被读取的累计行号。 在第一行被读取之前，返回 `0`。 在最后一个文件的最后一行被读取之后，返回该行的行号。

## fileinput.**filelineno**()

​	返回当前文件中的行号。 在第一行被读取之前，返回 `0`。 在最后一个文件的最后一行被读取之后，返回此文件中该行的行号。

## fileinput.**isfirstline**()

​	如果刚读取的行是其所在文件的第一行则返回 `True`，否则返回 `False`。

## fileinput.**isstdin**()

​	如果最后读取的行来自 `sys.stdin` 则返回 `True`，否则返回 `False`。

## fileinput.**nextfile**()

​	关闭当前文件以使下次迭代将从下一个文件（如果存在）读取第一行；不是从该文件读取的行将不会被计入累计行数。 直到下一个文件的第一行被读取之后文件名才会改变。 在第一行被读取之前，此函数将不会生效；它不能被用来跳过第一个文件。 在最后一个文件的最后一行被读取之后，此函数将不再生效。

## fileinput.**close**()

​	关闭序列。

​	此模块所提供的实现了序列行为的类同样也可用于子类化：

## *class* fileinput.**FileInput**(*files=None*, *inplace=False*, *backup=''*, ***, *mode='r'*, *openhook=None*, *encoding=None*, *errors=None*)

​	类 [`FileInput`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.FileInput) 是具体的实现；它的方法 [`filename()`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.filename), [`fileno()`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.fileno), [`lineno()`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.lineno), [`filelineno()`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.filelineno), [`isfirstline()`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.isfirstline), [`isstdin()`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.isstdin), [`nextfile()`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.nextfile) 和 [`close()`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.close) 对应于此模块具有相同名称的函数。 此外它还是一个 [iterable](https://docs.python.org/zh-cn/3.13/glossary.html#term-iterable) 并且具有可返回下一个输入行的 [`readline()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOBase.readline) 方法。 此序列必须以严格的序列顺序来访问；随机访问和 [`readline()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOBase.readline) 不可被混用。

​	通过 *mode* 你可以指定要传给 [`open()`](https://docs.python.org/zh-cn/3.13/library/functions.html#open) 的文件模式。 它必须为 `'r'` 和 `'rb'` 中的一个。

*openhook* 如果给出则必须为一个函数，它接受两个参数 *filename* 和 *mode*，并相应地返回一个打开的文件型对象。 你不能同时使用 *inplace* 和 *openhook*。

​	你可以指定 *encoding* 和 *errors* 来将其传给 [`open()`](https://docs.python.org/zh-cn/3.13/library/functions.html#open) 或 *openhook*。

[`FileInput`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.FileInput) 实例可以在 [`with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#with) 语句中被用作上下文管理器。 在这个例子中，*input* 在 `with` 语句结束后将会被关闭，即使发生了异常也是如此:

```
with FileInput(files=('spam.txt', 'eggs.txt')) as input:
    process(input)
```

*在 3.2 版本发生变更:* 可以被用作上下文管理器。

*在 3.8 版本发生变更:* 关键字形参 *mode* 和 *openhook* 现在是仅限关键字形参。

*在 3.10 版本发生变更:* 增加了仅限关键字形参 *encoding* 和 *errors*。

*在 3.11 版本发生变更:* `'rU'` 和 `'U'` 模式以及 `__getitem__()` 方法已被移除。

**可选的原地过滤:** 如果传递了关键字参数 `inplace=True` 给 [`fileinput.input()`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.input) 或 [`FileInput`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.FileInput) 构造器，则文件会被移至备份文件并将标准输出定向到输入文件（如果已存在与备份文件同名的文件，它将被静默地替换）。 这使得编写一个能够原地重写其输入文件的过滤器成为可能。 如果给出了 *backup* 形参 (通常形式为 `backup='.<some extension>'`)，它将指定备份文件的扩展名，并且备份文件会被保留；默认情况下扩展名为 `'.bak'` 并且它会在输出文件关闭时被删除。 在读取标准输入时原地过滤会被禁用。

​	此模块提供了以下两种打开文件钩子：

## fileinput.**hook_compressed**(*filename*, *mode*, ***, *encoding=None*, *errors=None*)

​	使用 [`gzip`](https://docs.python.org/zh-cn/3.13/library/gzip.html#module-gzip) 和 [`bz2`](https://docs.python.org/zh-cn/3.13/library/bz2.html#module-bz2) 模块透明地打开 gzip 和 bzip2 压缩的文件（通过扩展名 `'.gz'` 和 `'.bz2'` 来识别）。 如果文件扩展名不是 `'.gz'` 或 `'.bz2'`，文件会以正常方式打开（即使用 [`open()`](https://docs.python.org/zh-cn/3.13/library/functions.html#open) 并且不带任何解压操作）。

*encoding* 和 *errors* 值会被传给 [`io.TextIOWrapper`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOWrapper) 用于压缩文件以及打开普通文件。

​	用法示例: `fi = fileinput.FileInput(openhook=fileinput.hook_compressed, encoding="utf-8")`

*在 3.10 版本发生变更:* 增加了仅限关键字形参 *encoding* 和 *errors*。

## fileinput.**hook_encoded**(*encoding*, *errors=None*)

​	返回一个通过 [`open()`](https://docs.python.org/zh-cn/3.13/library/functions.html#open) 打开每个文件的钩子，使用给定的 *encoding* 和 *errors* 来读取文件。

​	使用示例: `fi = fileinput.FileInput(openhook=fileinput.hook_encoded("utf-8", "surrogateescape"))`

*在 3.6 版本发生变更:* 添加了可选的 *errors* 形参。

*自 3.10 版本弃用:* 此函数已被弃用，因为 [`fileinput.input()`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.input) 和 [`FileInput`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#fileinput.FileInput) 现在有了 *encoding* 和 *errors* 形参。
