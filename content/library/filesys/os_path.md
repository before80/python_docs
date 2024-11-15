+++
title = "os.path --- 常用的路径操作"
date = 2024-11-15T11:53:09+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/os.path.html](https://docs.python.org/zh-cn/3.13/library/os.path.html)
>
> 收录该文档的时间：`2024-11-15T11:53:09+08:00`

# `os.path` --- 常用的路径操作

**源代码:** [Lib/genericpath.py](https://github.com/python/cpython/tree/3.13/Lib/genericpath.py), [Lib/posixpath.py](https://github.com/python/cpython/tree/3.13/Lib/posixpath.py) (用于 POSIX) 和 [Lib/ntpath.py](https://github.com/python/cpython/tree/3.13/Lib/ntpath.py) (用于 Windows)。

------

​	此模块实现了一些有用的路径名称相关函数。 要读取或写入文件请参见 [`open()`](https://docs.python.org/zh-cn/3.13/library/functions.html#open)，对于访问文件系统请参阅 [`os`](https://docs.python.org/zh-cn/3.13/library/os.html#module-os) 模块。 传给 path 形参的可以是字符串、字节串或者任何实现了 [`os.PathLike`](https://docs.python.org/zh-cn/3.13/library/os.html#os.PathLike) 协议的对象。

​	与 Unix 不同，Python 不会执行任何 *自动* 路径扩展。 当应用程序需要类似 shell 的路径扩展时，可以显式地发起调用 [`expanduser()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.expanduser) 和 [`expandvars()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.expandvars) 这样的函数。 （另请参阅 [`glob`](https://docs.python.org/zh-cn/3.13/library/glob.html#module-glob) 模块。）

​	参见

 

[`pathlib`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#module-pathlib) 模块提供高级路径对象。

​	备注

 

​	所有这些函数都仅接受字节或字符串对象作为其参数。如果返回路径或文件名，则结果是相同类型的对象。

​	备注

 

​	由于不同的操作系统具有不同的路径名称约定，因此标准库中有此模块的几个版本。[`os.path`](https://docs.python.org/zh-cn/3.13/library/os.path.html#module-os.path) 模块始终是适合 Python 运行的操作系统的路径模块，因此可用于本地路径。但是，如果操作的路径 *总是* 以一种不同的格式显示，那么也可以分别导入和使用各个模块。它们都具有相同的接口：

- `posixpath` 用于Unix 样式的路径
- `ntpath` 用于 Windows 路径

*在 3.8 版本发生变更:* [`exists()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.exists)、[`lexists()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.lexists)、[`isdir()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.isdir)、[`isfile()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.isfile)、[`islink()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.islink) 和 [`ismount()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.ismount) 现在遇到系统层面上不可表示的字符或字节的路径时，会返回 `False`，而不是抛出异常。

## os.path.**abspath**(*path*)

​	返回路径 *path* 的绝对路径（标准化的）。在大多数平台上，这等同于用 `normpath(join(os.getcwd(), path))` 的方式调用 [`normpath()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.normpath) 函数。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## os.path.**basename**(*path*)

​	返回路径 *path* 的基本名称。这是将 *path* 传入函数 [`split()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.split) 之后，返回的一对值中的第二个元素。请注意，此函数的结果与Unix **basename** 程序不同。**basename** 在 `'/foo/bar/'` 上返回 `'bar'`，而 [`basename()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.basename) 函数返回一个空字符串 (`''`)。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## os.path.**commonpath**(*paths*)

​	返回可迭代对象 *paths* 中每个路径名称的最长共同子路径。 如果 *paths* 同时包含绝对和相对路径名称，如果 *paths* 位于不同驱动器，或者如果 *paths* 为空则会引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。 不同于 [`commonprefix()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.commonprefix)，此函数将返回一个有效的路径。

*Added in version 3.5.*

*在 3.6 版本发生变更:* 接受一个 [类路径对象](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object) 序列。

*在 3.13 版本发生变更:* 现在可以传入任意可迭代对象，而不只是序列。

## os.path.**commonprefix**(*list*)

​	接受包含多个路径的 *列表*，返回所有路径的最长公共前缀（逐字符比较）。如果 *列表* 为空，则返回空字符串 (`''`)。

​	备注

 

​	此函数是逐字符比较，因此可能返回无效路径。要获取有效路径，参见 [`commonpath()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.commonpath)。

\>>>

```
>>> os.path.commonprefix(['/usr/lib', '/usr/local/lib'])
'/usr/l'

>>> os.path.commonpath(['/usr/lib', '/usr/local/lib'])
'/usr'
```

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## os.path.**dirname**(*path*)

​	返回路径 *path* 的目录名称。这是将 *path* 传入函数 [`split()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.split) 之后，返回的一对值中的第一个元素。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## os.path.**exists**(*path*)

​	如果 *path* 指向一个已存在的路径或已打开的文件描述符，返回 `True`。对于失效的符号链接，返回 `False`。在某些平台上，如果使用 [`os.stat()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.stat) 查询到目标文件没有执行权限，即使 *path* 确实存在，本函数也可能返回 `False`。

*在 3.3 版本发生变更:* *path* 现在可以是一个整数：如果该整数是一个已打开的文件描述符，返回 `True`，否则返回 `False`。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## os.path.**lexists**(*path*)

​	如果 *path* 指向一个已存在的路径，包括失效的符号链接则返回 `True`。 在缺少 [`os.lstat()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.lstat) 的平台上就等价于 [`exists()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.exists)。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## os.path.**expanduser**(*path*)

​	在 Unix 和 Windows 上，将参数中开头部分的 `~` 或 `~user` 替换为当前 *用户* 的家目录并返回。

​	在 Unix 上，开头的 `~` 会被环境变量 `HOME` 代替，如果变量未设置，则通过内置模块 [`pwd`](https://docs.python.org/zh-cn/3.13/library/pwd.html#module-pwd) 在 password 目录中查找当前用户的主目录。以 `~user` 开头则直接在 password 目录中查找。

​	在 Windows 上，如果 `USERPROFILE` 已设置将会被使用，否则 `HOMEPATH` 和 `HOMEDRIVE` 将被组合起来使用。 初始的 `~user` 会通过检查当前用户的家目录中匹配 `USERNAME` 的最后一部分目录名并执行替换来处理。

​	如果展开路径失败，或者路径不是以波浪号开头，则路径将保持不变。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

*在 3.8 版本发生变更:* Windows 不再使用 `HOME`。

## os.path.**expandvars**(*path*)

​	输入带有环境变量的路径作为参数，返回展开变量以后的路径。`$name` 或 `${name}` 形式的子字符串被环境变量 *name* 的值替换。格式错误的变量名称和对不存在变量的引用保持不变。

​	在 Windows 上，除了 `$name` 和 `${name}` 外，还可以展开 `%name%`。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## os.path.**getatime**(*path*)

​	返回 *path* 的最后访问时间。返回值是一个浮点数，为纪元秒数（参见 [`time`](https://docs.python.org/zh-cn/3.13/library/time.html#module-time) 模块）。如果该文件不存在或不可访问，则抛出 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 异常。

## os.path.**getmtime**(*path*)

​	返回 *path* 的最后修改时间。返回值是一个浮点数，为纪元秒数（参见 [`time`](https://docs.python.org/zh-cn/3.13/library/time.html#module-time) 模块）。如果该文件不存在或不可访问，则抛出 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 异常。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## os.path.**getctime**(*path*)

​	返回 *path* 在系统中的 ctime，在有些系统（比如 Unix）上，它是元数据的最后修改时间，其他系统（比如 Windows）上，它是 *path* 的创建时间。返回值是一个数，为纪元秒数（参见 [`time`](https://docs.python.org/zh-cn/3.13/library/time.html#module-time) 模块）。如果该文件不存在或不可访问，则抛出 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 异常。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## os.path.**getsize**(*path*)

​	返回 *path* 的大小，以字节为单位。如果该文件不存在或不可访问，则抛出 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 异常。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## os.path.**isabs**(*path*)

​	如果 *path* 为绝对路径则返回 `True`。 在 Unix 上，意味着它是以斜杠打头的，在 Windows 上它是以两个（反）斜杠，或是由驱动器号、冒号和（反斜杠）连在一起打头的。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

*在 3.13 版本发生变更:* 在 Windows 上，如果给定的路径是以一个单独（反）斜杠打头则返回 `False`。

## os.path.**isfile**(*path*)

​	如果 *path* 是 [`现有的`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.exists) 常规文件，则返回 `True`。本方法会跟踪符号链接，因此，对于同一路径，[`islink()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.islink) 和 [`isfile()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.isfile) 都可能为 `True`。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## os.path.**isdir**(*path*)

​	如果 *path* 是 [`现有的`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.exists) 目录，则返回 `True`。本方法会跟踪符号链接，因此，对于同一路径，[`islink()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.islink) 和 [`isdir()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.isdir) 都可能为 `True`。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## os.path.**isjunction**(*path*)

​	Return `True` 如果 *path* 指向的 [`现有`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.lexists) 目录条目是一个连接点。 则当连接点在当前平台不受支持时将总是返回 `False`。

*Added in version 3.12.*

## os.path.**islink**(*path*)

​	如果 *path* 指向的 [`现有`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.exists) 目录条目是一个符号链接，则返回 `True`。如果 Python 运行时不支持符号链接，则总是返回 `False`。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## os.path.**ismount**(*path*)

​	如果路径 *path* 是 *挂载点* （文件系统中挂载其他文件系统的点），则返回 `True`。在 POSIX 上，该函数检查 *path* 的父目录 `*path*/..` 是否在与 *path* 不同的设备上，或者 `*path*/..` 和 *path* 是否指向同一设备上的同一 inode（这一检测挂载点的方法适用于所有 Unix 和 POSIX 变体）。本方法不能可靠地检测同一文件系统上的绑定挂载 (bind mount)。在 Windows 上，盘符和共享 UNC 始终是挂载点，对于任何其他路径，将调用 `GetVolumePathName` 来查看它是否与输入的路径不同。

*在 3.4 版本发生变更:* 增加了在 Windows 上检测非根挂载点的支持。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## os.path.**isdevdrive**(*path*)

​	如果路径名 *path* 位于一个 Windows Dev 驱动器则返回 `True`。 Dev Drive 针对开发者场景进行了优化，并为读写文件提供更快的性能。 推荐用于源代码、临时构建目录、包缓存以及其他的 IO 密集型操作。

​	对于无效的路径可能引发错误，例如，没有可识别的驱动器的路径，但在不支持 Dev 驱动器的平台上将返回 `False`。 请参阅 [Windows 文档](https://learn.microsoft.com/windows/dev-drive/) 了解有关启用并创建 Dev 驱动器的信息。

*Added in version 3.12.*

*在 3.13 版本发生变更:* 现在此函数在所有平台上可用，并且在不支持 Dev 驱动器的平台上将总是返回 `False`。

## os.path.**isreserved**(*path*)

​	如果 *path* 为当前系统的保留路径名则返回 `True`。

​	在 Windows 上，保留文件名包括以一个空格或点号结尾的；包含冒号的 (即文件流如 "name:stream")，通配符 (即 `'*?"<>'`)，管道或 ASCII 控制符；以及 DOS 设备名称如 "NUL", "CON", "CONIN$", "CONOUT$", "AUX", "PRN", "COM1" 和 "LPT1"。

​	备注

 

​	此函数在多数 Windows 系统上使用相似的保留路径规则。 这些规则会随着时间的推移在不同的 Windows 发布版中发生改变。 此函数可能会随着规则的更改广泛可用在未来的 Python 发布版中被更新。

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): Windows.

*Added in version 3.13.*

## os.path.**join**(*path*, **paths*)

​	智能地合并一个或多个路径部分。 返回值将是 *path* 和所有 **paths* 成员的拼接，其中每个非空部分后面都紧跟一个目录分隔符，最后一个除外。 也就是说，如果最后一个部分为空或是以一个分隔符结束则结果将仅以一个分隔符结束。 如果某个部分为绝对路径（在 Windows 上需要同时有驱动器号和根路径符号），则之前的所有部分会被忽略并从该绝对路径部分开始拼接。

​	在 Windows，当遇到绝对路径部分 (如 `r'\foo'`) 时驱动器号将不会被重置。 如果某个部分位于不同驱动器或为绝对路径，则之前的所有部分会被忽略并且该驱动器号会被重置。 请注意由于每个驱动器都有一个当前目录，因此 `os.path.join("c:", "foo")` 是代表驱动器 `C:` 上当前路径的相对路径 (`c:foo`)，而不是 `c:\foo`。

*在 3.6 版本发生变更:* 接受一个 [类路径对象](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object) 用于 *path* 和 *paths* 。

## os.path.**normcase**(*path*)

​	规范路径的大小写。在 Windows 上，将路径中的所有字符都转换为小写，并将正斜杠转换为反斜杠。在其他操作系统上返回原路径。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## os.path.**normpath**(*path*)

​	通过折叠多余的分隔符和对上级目录的引用来标准化路径名，所以 `A//B`、`A/B/`、`A/./B` 和 `A/foo/../B` 都会转换成 `A/B`。这个字符串操作可能会改变带有符号链接的路径的含义。在 Windows 上，本方法将正斜杠转换为反斜杠。要规范大小写，请使用 [`normcase()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.normcase)。

​	备注

 

​	在 POSIX 系统上，根据 [IEEE Std 1003.1 2013 Edition; 4.13 Pathname Resolution](https://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap04.html#tag_04_13)，如果一个路径名称以两个斜杠开始，则开始字符之后的第一个部分将以具体实现所定义的方式来解读，但是超过两个开始字符则将被视为单个字符。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## os.path.**realpath**(*path*, ***, *strict=False*)

​	返回指定文件名的规范路径，去除在路径中出现的任何符号链接（如果操作系统支持的话）。 在 Windows 上，此函数也会将 MS-DOS (或称 8.3) 风格的名称如 `C:\\PROGRA~1` 解析为 `C:\\Program Files`。

​	如果一个路径不存在或是遇到了符号链接循环，并且 *strict* 为 `True`，则会引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError)。 如果 *strict* 为 `False` 则这些错误将被忽略，因此结果可能缺失或无法访问。

​	备注

 

​	这个函数会模拟操作系统生成规范路径的过程，Windows 与 UNIX 的这个过程在处理链接和后续路径组成部分的交互方式上有所差异。

​	操作系统 API 会根据需要来规范化路径，因此通常不需要调用此函数。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

*在 3.8 版本发生变更:* 在 Windows 上现在可以正确解析符号链接和交接点 (junction point)。

*在 3.10 版本发生变更:* 增加了 *strict* 形参。

## os.path.**relpath**(*path*, *start=os.curdir*)

​	返回从当前目录或可选的 *start* 目录至 *path* 的相对文件路径。 这只是一个路径计算：不会访问文件系统来确认 *path* 或 *start* 是否存在或其性质。 在 Windows 上，当 *path* 和 *start* 位于不同驱动器时将引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

*start* 默认为 [`os.curdir`](https://docs.python.org/zh-cn/3.13/library/os.html#os.curdir)。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## os.path.**samefile**(*path1*, *path2*)

​	如果两个路径都指向相同的文件或目录，则返回 `True`。这由设备号和 inode 号确定，在任一路径上调用 [`os.stat()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.stat) 失败则抛出异常。

*在 3.2 版本发生变更:* 添加了对 Windows 的支持。

*在 3.4 版本发生变更:* Windows现在使用与其他所有平台相同的实现。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## os.path.**sameopenfile**(*fp1*, *fp2*)

​	如果文件描述符 *fp1* 和 *fp2* 指向相同文件，则返回 `True`。

*在 3.2 版本发生变更:* 添加了对 Windows 的支持。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## os.path.**samestat**(*stat1*, *stat2*)

​	如果 stat 元组 *stat1* 和 *stat2* 指向相同文件，则返回 `True`。这些 stat 元组可能是由 [`os.fstat()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.fstat)、[`os.lstat()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.lstat) 或 [`os.stat()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.stat) 返回的。本函数实现了 [`samefile()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.samefile) 和 [`sameopenfile()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.sameopenfile) 底层所使用的比较过程。

*在 3.4 版本发生变更:* 添加了对 Windows 的支持。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## os.path.**split**(*path*)

​	将路径 *path* 拆分为一对，即 `(head, tail)`，其中，*tail* 是路径的最后一部分，而 *head* 里是除最后部分外的所有内容。*tail* 部分不会包含斜杠，如果 *path* 以斜杠结尾，则 *tail* 将为空。如果 *path* 中没有斜杠，*head* 将为空。如果 *path* 为空，则 *head* 和 *tail* 均为空。*head* 末尾的斜杠会被去掉，除非它是根目录（即它仅包含一个或多个斜杠）。在所有情况下，`join(head, tail)` 指向的位置都与 *path* 相同（但字符串可能不同）。另请参见函数 [`dirname()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.dirname) 和 [`basename()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.basename)。

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## os.path.**splitdrive**(*path*)

​	将路径 *path* 拆分为一对，即 `(drive, tail)`，其中 *drive* 是挂载点或空字符串。在没有驱动器概念的系统上，*drive* 将始终为空字符串。在所有情况下，`drive + tail` 都与 *path* 相同。

​	在 Windows 上，本方法将路径拆分为驱动器/UNC 根节点和相对路径。

​	如果路径 path 包含盘符，则 drive 将包含冒号之前的所有内容包括冒号本身:

\>>>

```
>>> splitdrive("c:/dir")
("c:", "/dir")
```

​	如果路径包含 UNC 路径，则 drive 将包含主机名和 share:

\>>>

```
>>> splitdrive("//host/computer/dir")
("//host/computer", "/dir")
```

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## os.path.**splitroot**(*path*)

​	将路径名 *path* 拆分为一个 3 元组 `(drive, root, tail)` 其中 *drive* 是设置名或挂载点，*root* 是表示 drive 之后的分隔符的字符串，而 *tail* 则为 root 之后的所有内容。 这此条目均可以为空字符串。 在所有情况下，`drive + root + tail` 都与 *path* 相同。

​	在 POSIX 系统上，*drive* 将总是为空。 *root* 可能为空（如果 *path* 是相对路径）、单个正斜杠（如果 *path* 是绝对路径）、或两个正斜杠（由基于 [IEEE Std 1003.1-2017; 4.13 Pathname Resolution](https://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap04.html#tag_04_13) 的具体实现来定义。） 例如:

\>>>

```
>>> splitroot('/home/sam')
('', '/', 'home/sam')
>>> splitroot('//home/sam')
('', '//', 'home/sam')
>>> splitroot('///home/sam')
('', '/', '//home/sam')
```

​	在 Windows 上，*drive* 可能为空、以字母表示的驱动器名称、UNC share 或是设备名称。 *root* 可能为空、单个正斜杠，或单个反斜杠。 例如:

\>>>

```
>>> splitroot('C:/Users/Sam')
('C:', '/', 'Users/Sam')
>>> splitroot('//Server/Share/Users/Sam')
('//Server/Share', '/', 'Users/Sam')
```

*Added in version 3.12.*

## os.path.**splitext**(*path*)

​	将路径名称 *path* 拆分为 `(root, ext)` 对使得 `root + ext == path`，并且扩展名 *ext* 为空或以句点打头并最多只包含一个句点。

​	如果路径 path 不包含扩展名，则 *ext* 将为 `''`:

\>>>

```
>>> splitext('bar')
('bar', '')
```

​	如果路径 path 包含扩展名，则 *ext* 将被设为该扩展名，包括打头的句点。 请注意在其之前的句点将被忽略:

\>>>

```
>>> splitext('foo.bar.exe')
('foo.bar', '.exe')
>>> splitext('/foo/bar.exe')
('/foo/bar', '.exe')
```

​	path 中最后一部分如果以点号开头则会被视为 root 的一部分:

\>>>

```
>>> splitext('.cshrc')
('.cshrc', '')
>>> splitext('/foo/....jpg')
('/foo/....jpg', '')
```

*在 3.6 版本发生变更:* 接受一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

## os.path.**supports_unicode_filenames**

​	如果（在文件系统限制下）允许将任意 Unicode 字符串用作文件名，则为 `True`。
