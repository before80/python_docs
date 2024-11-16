+++
title = "os --- 多种操作系统接口"
date = 2024-11-15T12:09:25+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/os.html](https://docs.python.org/zh-cn/3.13/library/os.html)
>
> 收录该文档的时间：`2024-11-15T12:09:25+08:00`

# `os` --- 多种操作系统接口

**源代码：** [Lib/os.py](https://github.com/python/cpython/tree/3.13/Lib/os.py)

------

​	本模块提供了一种使用与操作系统相关的功能的便捷式途径。 如果你只是想读写一个文件，请参阅 [`open()`]({{< ref "/library/functions#open" >}})，如果你想操作文件路径，请参阅 [`os.path`]({{< ref "/library/filesys/os_path#module-os.path" >}}) 模块，如果你想读取通过命令行给出的所有文件中的所有行，请参阅 [`fileinput`]({{< ref "/library/filesys/fileinput#module-fileinput" >}}) 模块。 为了创建临时文件和目录，请参阅 [`tempfile`]({{< ref "/library/filesys/tempfile#module-tempfile" >}}) 模块，对于高级文件和目录处理，请参阅 [`shutil`]({{< ref "/library/filesys/shutil#module-shutil" >}}) 模块。

​	关于这些函数的适用性的说明：

- Python中所有依赖于操作系统的内置模块的设计都是这样，只要不同的操作系统某一相同的功能可用，它就使用相同的接口。例如，函数 `os.stat(path)` 以相同的格式返回关于 *path* 的状态信息（该格式源于 POSIX 接口）。
- 特定于某一操作系统的扩展通过操作 [`os`]({{< ref "/library/allos/os#module-os" >}}) 模块也是可用的，但是使用它们当然是对可移植性的一种威胁。
- 所有接受路径或文件名的函数都同时支持字节串和字符串对象，并在返回路径或文件名时使用相应类型的对象作为结果。
- 在 VxWorks 系统上，os.popen, os.fork, os.execv 和 os.spawn*p* 都未支持。
- 在 WebAssembly 平台、Android 和 iOS 上，[`os`]({{< ref "/library/allos/os#module-os" >}}) 模块的很大一部分都不可用或具有不同的行为。 与进程相关联的 API (例如 [`fork()`]({{< ref "/library/allos/os#os.fork" >}}), [`execve()`]({{< ref "/library/allos/os#os.execve" >}})) 和资源 (例如 [`nice()`]({{< ref "/library/allos/os#os.nice" >}})) 都不可用。 其他诸如 [`getuid()`]({{< ref "/library/allos/os#os.getuid" >}}) 和 [`getpid()`]({{< ref "/library/allos/os#os.getpid" >}}) 等则为模拟或空盒。 WebAssembly 平台还缺少对信号 (例如 [`kill()`]({{< ref "/library/allos/os#os.kill" >}}), [`wait()`]({{< ref "/library/allos/os#os.wait" >}})) 的支持。

​	备注

 

​	如果使用无效或无法访问的文件名与路径，或者其他类型正确但操作系统不接受的参数，此模块的所有函数都抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) （或者它的子类）。

## *exception* os.**error**

​	内建的 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 异常的一个别名。

## os.**name**

​	导入的依赖特定操作系统的模块的名称。以下名称目前已注册: `'posix'`, `'nt'`, `'java'`.

​	参见

 

[`sys.platform`]({{< ref "/library/python/sys#sys.platform" >}}) 具有更细的粒度。 [`os.uname()`]({{< ref "/library/allos/os#os.uname" >}}) 将给出基于不同系统的版本信息。

[`platform`]({{< ref "/library/allos/platform#module-platform" >}}) 模块对系统的标识有更详细的检查。



## 文件名，命令行参数，以及环境变量。

​	在 Python 中，使用字符串类型表示文件名、命令行参数和环境变量。 在某些系统上，在将这些字符串传递给操作系统之前，必须将这些字符串解码为字节。 Python 使用 [filesystem encoding and error handler]({{< ref "/glossary/idx#term-filesystem-encoding-and-error-handler" >}}) 来执行此转换（请参阅 [`sys.getfilesystemencoding()`]({{< ref "/library/python/sys#sys.getfilesystemencoding" >}}) ）。

[filesystem encoding and error handler]({{< ref "/glossary/idx#term-filesystem-encoding-and-error-handler" >}}) 是在 Python 启动时通过 [`PyConfig_Read()`]({{< ref "/c_api/init_config#c.PyConfig_Read" >}}) 函数来配置的：请参阅 [`PyConfig`]({{< ref "/c_api/init_config#c.PyConfig" >}}) 的 [`filesystem_encoding`]({{< ref "/c_api/init_config#c.PyConfig.filesystem_encoding" >}}) 和 [`filesystem_errors`]({{< ref "/c_api/init_config#c.PyConfig.filesystem_errors" >}}) 等成员。

*在 3.1 版本发生变更:* 在某些系统上，使用文件系统编码格式进行转换可能会失败。 在这种情况下，Python 会使用 [surrogateescape 编码错误处理器]({{< ref "/library/binary/codecs#surrogateescape" >}})，这意味着不可解码的字节在解码时会被 Unicode 字符 U+DC*xx* 替换，并且这些字节在编码时又会再次被转换为原始字节。

[文件系统编码器]({{< ref "/glossary/idx#term-filesystem-encoding-and-error-handler" >}}) 必须保证能成功解码所有 128 以内的字节。如果不能保证，API 函数可能触发 [`UnicodeError`]({{< ref "/library/exceptions#UnicodeError" >}}) 。

​	另请参见 [locale encoding]({{< ref "/glossary/idx#term-locale-encoding" >}})。



## Python UTF-8 模式

> Added in version 3.7:* 有关更多详细信息，请参阅 [**PEP 540*
>](https://peps.python.org/pep-0540/) 。

​	Python UTF-8 模式会忽略 [locale encoding]({{< ref "/glossary/idx#term-locale-encoding" >}}) 并强制使用 UTF-8 编码。

- 用 UTF-8 作为 [文件系统编码]({{< ref "/glossary/idx#term-filesystem-encoding-and-error-handler" >}})。
- [`sys.getfilesystemencoding()`]({{< ref "/library/python/sys#sys.getfilesystemencoding" >}}) 返回 `'utf-8'`。
- [`locale.getpreferredencoding()`]({{< ref "/library/i18n/locale#locale.getpreferredencoding" >}}) 返回 `'utf-8'` (*do_setlocale* 参数不起作用)。
- [`sys.stdin`]({{< ref "/library/python/sys#sys.stdin" >}}), [`sys.stdout`]({{< ref "/library/python/sys#sys.stdout" >}}) 和 [`sys.stderr`]({{< ref "/library/python/sys#sys.stderr" >}}) 都将 UTF-8 用作它们的文本编码，并且为 [`sys.stdin`]({{< ref "/library/python/sys#sys.stdin" >}}) 和 [`sys.stdout`]({{< ref "/library/python/sys#sys.stdout" >}}) 启用 `surrogateescape` [错误处理器]({{< ref "/library/binary/codecs#error-handlers" >}}) ([`sys.stderr`]({{< ref "/library/python/sys#sys.stderr" >}}) 会继续使用 `backslashreplace` 如同在默认的局部感知模式下一样)
- 在 Unix 上，[`os.device_encoding()`]({{< ref "/library/allos/os#os.device_encoding" >}}) 返回 `'utf-8'` 而不是设备的编码格式。

​	请注意 UTF-8 模式下的标准流设置可以被 [`PYTHONIOENCODING`]({{< ref "/using/cmdline#envvar-PYTHONIOENCODING" >}}) 所覆盖（在默认的区域感知模式下也同样如此）。

​	作为低层级 API 发生改变的结果，其他高层级 API 也会表现出不同的默认行为：

- 命令行参数，环境变量和文件名会使用 UTF-8 编码来解码为文本。
- [`os.fsdecode()`]({{< ref "/library/allos/os#os.fsdecode" >}}) 和 [`os.fsencode()`]({{< ref "/library/allos/os#os.fsencode" >}}) 使用 UTF-8 编码格式。
- [`open()`]({{< ref "/library/functions#open" >}}), [`io.open()`]({{< ref "/library/allos/io#io.open" >}}) 和 [`codecs.open()`]({{< ref "/library/binary/codecs#codecs.open" >}}) 默认会使用 UTF-8 编码格式。 但是，它们默认仍将使用 strict 错误处理器因此试图在文本模式下打开二进制文件可能会引发异常而不是产生无意义的数据。

​	如果在 Python 启动时 LC_CTYPE 区域设为 `C` 或 `POSIX` ，则启用 [Python UTF-8 模式]({{< ref "/library/allos/os#utf8-mode" >}}) (参见 [`PyConfig_Read()`]({{< ref "/c_api/init_config#c.PyConfig_Read" >}}) 函数)。

​	它可以通过命令行选项 [`-X utf8`]({{< ref "/using/cmdline#cmdoption-X" >}}) 和环境变量 [`PYTHONUTF8`]({{< ref "/using/cmdline#envvar-PYTHONUTF8" >}})，来启用或禁用。

​	如果没有设置 [`PYTHONUTF8`]({{< ref "/using/cmdline#envvar-PYTHONUTF8" >}}) 环境变量，那么解释器默认使用当前的地区设置，*除非* 当前地区识别为基于 ASCII 的传统地区（如 [`PYTHONCOERCECLOCALE`]({{< ref "/using/cmdline#envvar-PYTHONCOERCECLOCALE" >}}) 所述），并且 locale coercion 被禁用或失败。在这种传统地区，除非显式指明不要如此，解释器将默认启用 UTF-8 模式。

​	Python UTF-8 模式只能在 Python 启动时启用。其值可以从 [`sys.flags.utf8_mode`]({{< ref "/library/python/sys#sys.flags" >}}) 读取。

​	另请参阅 [在 Windows 中的 UTF-8 模式]({{< ref "/using/windows#win-utf8-mode" >}}) 和 [filesystem encoding and error handler]({{< ref "/glossary/idx#term-filesystem-encoding-and-error-handler" >}})。

​	参见

[**PEP 686**](https://peps.python.org/pep-0686/)

​	Python 3.15 将把 [Python UTF-8 模式]({{< ref "/library/allos/os#utf8-mode" >}}) 设为默认值。



## 进程参数

​	这些函数和数据项提供了操作当前进程和用户的信息。

## os.**ctermid**()

​	返回与进程控制终端对应的文件名。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**environ**

​	一个 [mapping]({{< ref "/glossary/idx#term-mapping" >}}) 对象，其中键值是代表进程环境的字符串。 例如，`environ['HOME']` 是你的主目录（在某些平台上）的路径名，相当于 C 中的 `getenv("HOME")`。

​	这个映射是在第一次导入 [`os`]({{< ref "/library/allos/os#module-os" >}}) 模块时捕获的，通常作为 Python 启动时处理 `site.py` 的一部分。除了通过直接修改 [`os.environ`]({{< ref "/library/allos/os#os.environ" >}}) 之外，在此之后对环境所做的更改不会反映在 [`os.environ`]({{< ref "/library/allos/os#os.environ" >}}) 中。

​	该映射除了可以用于查询环境外，还能用于修改环境。当该映射被修改时，将自动调用 [`putenv()`]({{< ref "/library/allos/os#os.putenv" >}})。

​	在Unix系统上，键和值会使用 [`sys.getfilesystemencoding()`]({{< ref "/library/python/sys#sys.getfilesystemencoding" >}}) 和 `'surrogateescape'` 的错误处理。如果你想使用其他的编码，使用 [`environb`]({{< ref "/library/allos/os#os.environb" >}})。

​	在 Windows 上，这些键会被转换为大写形式。 这也会在获取、设备或删除条目时被应用。 例如，`environ['monty'] = 'python'` 会将键 `'MONTY'` 映射到值 `'python'`。

​	备注

 

​	直接调用 [`putenv()`]({{< ref "/library/allos/os#os.putenv" >}}) 并不会影响 [`os.environ`]({{< ref "/library/allos/os#os.environ" >}}) ，所以推荐直接修改 [`os.environ`]({{< ref "/library/allos/os#os.environ" >}}) 。

​	备注

 

​	在某些平台上，包括 FreeBSD 和 macOS 等，设置 `environ` 可能会导致内存泄漏。 请参阅有关 `putenv()` 的系统文档。

​	可以删除映射中的元素来删除对应的环境变量。当从 [`os.environ`]({{< ref "/library/allos/os#os.environ" >}}) 删除元素时，以及调用 `pop()` 或 `clear()` 之一时，将自动调用 [`unsetenv()`]({{< ref "/library/allos/os#os.unsetenv" >}})。

*在 3.9 版本发生变更:* 已更新并支持了 [**PEP 584**](https://peps.python.org/pep-0584/) 的合并 (`|`) 和更新 (`|=`) 运算符。

## os.**environb**

[`environ`]({{< ref "/library/allos/os#os.environ" >}}) 的字节版本：一个 [mapping]({{< ref "/glossary/idx#term-mapping" >}}) 对象，其中键值都是 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 对象，代表进程环境。 [`environ`]({{< ref "/library/allos/os#os.environ" >}}) 和 [`environb`]({{< ref "/library/allos/os#os.environb" >}}) 是同步的（修改 [`environb`]({{< ref "/library/allos/os#os.environb" >}}) 会更新 [`environ`]({{< ref "/library/allos/os#os.environ" >}})，反之亦然）。

[`environb`]({{< ref "/library/allos/os#os.environb" >}}) 仅在 [`supports_bytes_environ`]({{< ref "/library/allos/os#os.supports_bytes_environ" >}}) 为 `True` 时可用。

> Added in version 3.2.
>

*在 3.9 版本发生变更:* 已更新并支持了 [**PEP 584**](https://peps.python.org/pep-0584/) 的合并 (`|`) 和更新 (`|=`) 运算符。

## os.**chdir**(*path*)

## os.**fchdir**(*fd*)

## os.**getcwd**()

​	以上函数请参阅 [文件和目录]({{< ref "/library/allos/os#os-file-dir" >}}) 。

## os.**fsencode**(*filename*)

​	将 [类似路径形式的]({{< ref "/glossary/idx#term-path-like-object" >}}) *filename* 编码为 [filesystem encoding and error handler]({{< ref "/glossary/idx#term-filesystem-encoding-and-error-handler" >}})；原样返回 [`bytes`]({{< ref "/library/stdtypes#bytes" >}})。

[`fsdecode()`]({{< ref "/library/allos/os#os.fsdecode" >}}) 是此函数的逆向函数。

> Added in version 3.2.
>

*在 3.6 版本发生变更:* 增加对实现了 [`os.PathLike`]({{< ref "/library/allos/os#os.PathLike" >}}) 接口的对象的支持。

## os.**fsdecode**(*filename*)

​	根据 [filesystem encoding and error handler]({{< ref "/glossary/idx#term-filesystem-encoding-and-error-handler" >}}) 来解码 [类似路径形式的]({{< ref "/glossary/idx#term-path-like-object" >}}) *filename*；原样返回 [`str`]({{< ref "/library/stdtypes#str" >}})。

[`fsencode()`]({{< ref "/library/allos/os#os.fsencode" >}}) 是此函数的逆向函数。

> Added in version 3.2.
>

*在 3.6 版本发生变更:* 增加对实现了 [`os.PathLike`]({{< ref "/library/allos/os#os.PathLike" >}}) 接口的对象的支持。

## os.**fspath**(*path*)

​	返回路径的文件系统表示。

​	如果传入的是 [`str`]({{< ref "/library/stdtypes#str" >}}) 或 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 类型的字符串，将原样返回。否则 [`__fspath__()`]({{< ref "/library/allos/os#os.PathLike.__fspath__" >}}) 将被调用，如果得到的是一个 [`str`]({{< ref "/library/stdtypes#str" >}}) 或 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 类型的对象，那就返回这个值。其他所有情况则会抛出 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}}) 异常。

> Added in version 3.6.
>

## *class* os.**PathLike**

​	某些对象用于表示文件系统中的路径（如 [`pathlib.PurePath`]({{< ref "/library/filesys/pathlib#pathlib.PurePath" >}}) 对象），本类是这些对象的 [抽象基类]({{< ref "/glossary/idx#term-abstract-base-class" >}})。

> Added in version 3.6.
>

## *abstractmethod* **__fspath__**()

​	返回当前对象的文件系统表示。

​	这个方法只应该返回一个 [`str`]({{< ref "/library/stdtypes#str" >}}) 字符串或 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 字节串，请优先选择 [`str`]({{< ref "/library/stdtypes#str" >}}) 字符串。

## os.**getenv**(*key*, *default=None*)

​	如果环境变量 *key* 存在则将其值作为字符串返回，如果不存在则返回 *default*。 *key* 是一个字符串。 请注意由于 [`getenv()`]({{< ref "/library/allos/os#os.getenv" >}}) 使用了 [`os.environ`]({{< ref "/library/allos/os#os.environ" >}})，因此 [`getenv()`]({{< ref "/library/allos/os#os.getenv" >}}) 的映射同样也会在导入时被捕获，并且该函数可能无法反映未来的环境变化。

​	在Unix系统上，键和值会使用 [`sys.getfilesystemencoding()`]({{< ref "/library/python/sys#sys.getfilesystemencoding" >}}) 和 `'surrogateescape'` 错误处理进行解码。如果你想使用其他的编码，使用 [`os.getenvb()`]({{< ref "/library/allos/os#os.getenvb" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows.

## os.**getenvb**(*key*, *default=None*)

​	如果环境变量 *key* 存在则将其值作为字节串返回，如果不存在则返回 *default*。 *key* 必须为字节串。 请注意由于 [`getenvb()`]({{< ref "/library/allos/os#os.getenvb" >}}) 使用了 [`os.environb`]({{< ref "/library/allos/os#os.environb" >}})，因此 [`getenvb()`]({{< ref "/library/allos/os#os.getenvb" >}}) 的映射同样也会在导入时被捕获，并且该函数可能无法反映未来的环境变化。

[`getenvb()`]({{< ref "/library/allos/os#os.getenvb" >}}) 仅在 [`supports_bytes_environ`]({{< ref "/library/allos/os#os.supports_bytes_environ" >}}) 为 `True` 时可用。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

> Added in version 3.2.
>

## os.**get_exec_path**(*env=None*)

​	返回将用于搜索可执行文件的目录列表，与在外壳程序中启动一个进程时相似。指定的 *env* 应为用于搜索 PATH 的环境变量字典。默认情况下，当 *env* 为 `None` 时，将会使用 [`environ`]({{< ref "/library/allos/os#os.environ" >}}) 。

> Added in version 3.2.
>

## os.**getegid**()

​	返回当前进程的有效组ID。对应当前进程执行文件的 "set id" 位。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**geteuid**()

​	返回当前进程的有效用户ID。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**getgid**()

​	返回当前进程的实际组ID。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

​	该函数在 WASI 上为空代码段，请参阅 [WebAssembly 平台]({{< ref "/library/intro#wasm-availability" >}}) 了解详情。

## os.**getgrouplist**(*user*, *group*, */*)

​	返回 *user* 所属的组 ID 列表。 如果 *group* 不在该列表中，它将被包括在内；通常，*group* 将会被指定为来自 *user* 的密码记录文件的组 ID 字段，因为在其他情况下该组 ID 有可能会被略去。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

> Added in version 3.3.
>

## os.**getgroups**()

​	返回当前进程关联的附加组ID列表

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

​	备注

 

​	在 macOS 上，[`getgroups()`]({{< ref "/library/allos/os#os.getgroups" >}}) 的行为与其他 Unix 平台有所不同。 如果 Python 解释器是以 `10.5` 或更早版本作为部署目标的，则 [`getgroups()`]({{< ref "/library/allos/os#os.getgroups" >}}) 会返回与当前用户进程相关联的有效组 ID 列表；该列表受限于系统预定义的条目数量，通常为 16，并且在适当的权限下还可通过调用 [`setgroups()`]({{< ref "/library/allos/os#os.setgroups" >}}) 来修改。 如果所用的部署目标版本大于 `10.5`，则 [`getgroups()`]({{< ref "/library/allos/os#os.getgroups" >}}) 会返回与进程的有效用户 ID 相关联的当前组访问列表；组访问列表可能会在进程的生命周期之内发生改变，它不会受对 [`setgroups()`]({{< ref "/library/allos/os#os.setgroups" >}}) 的调用影响，且其长度也不会被限制为 16。 部署目标值 `MACOSX_DEPLOYMENT_TARGET` 可以通过 [`sysconfig.get_config_var()`]({{< ref "/library/python/sysconfig#sysconfig.get_config_var" >}}) 来获取。

## os.**getlogin**()

​	返回通过控制终端进程进行登录的用户名。在多数情况下，使用 [`getpass.getuser()`]({{< ref "/library/allos/getpass#getpass.getuser" >}}) 会更有效，因为后者会通过检查环境变量 `LOGNAME` 或 `USERNAME` 来查找用户，再由 `pwd.getpwuid(os.getuid())[0]` 来获取当前用户 ID 的登录名。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows, not WASI.

## os.**getpgid**(*pid*)

​	根据进程id *pid* 返回进程的组 ID 列表。如果 *pid* 为 0，则返回当前进程的进程组 ID 列表

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**getpgrp**()

​	返回当时进程组的ID

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**getpid**()

​	返回当前进程ID

​	该函数在 WASI 上为空代码段，请参阅 [WebAssembly 平台]({{< ref "/library/intro#wasm-availability" >}}) 了解详情。

## os.**getppid**()

​	返回父进程ID。当父进程已经结束，在Unix中返回的ID是初始进程(1)中的一个，在Windows中仍然是同一个进程ID，该进程ID有可能已经被进行进程所占用。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows, not WASI.

*在 3.2 版本发生变更:* 添加WIndows的支持。

## os.**getpriority**(*which*, *who*)

​	获取程序调度优先级。*which* 参数值可以是 [`PRIO_PROCESS`]({{< ref "/library/allos/os#os.PRIO_PROCESS" >}})，[`PRIO_PGRP`]({{< ref "/library/allos/os#os.PRIO_PGRP" >}})，或 [`PRIO_USER`]({{< ref "/library/allos/os#os.PRIO_USER" >}}) 中的一个，*who* 是相对于 *which* ([`PRIO_PROCESS`]({{< ref "/library/allos/os#os.PRIO_PROCESS" >}}) 的进程标识符，[`PRIO_PGRP`]({{< ref "/library/allos/os#os.PRIO_PGRP" >}}) 的进程组标识符和 [`PRIO_USER`]({{< ref "/library/allos/os#os.PRIO_USER" >}}) 的用户ID)。当 *who* 为 0 时（分别）表示调用的进程，调用进程的进程组或调用进程所属的真实用户 ID。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

> Added in version 3.3.
>

## os.**PRIO_PROCESS**

## os.**PRIO_PGRP**

## os.**PRIO_USER**

​	函数 [`getpriority()`]({{< ref "/library/allos/os#os.getpriority" >}}) 和 [`setpriority()`]({{< ref "/library/allos/os#os.setpriority" >}}) 的参数。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

> Added in version 3.3.
>

## os.**PRIO_DARWIN_THREAD**

## os.**PRIO_DARWIN_PROCESS**

## os.**PRIO_DARWIN_BG**

## os.**PRIO_DARWIN_NONUI**

​	函数 [`getpriority()`]({{< ref "/library/allos/os#os.getpriority" >}}) 和 [`setpriority()`]({{< ref "/library/allos/os#os.setpriority" >}}) 的参数。

[Availability]({{< ref "/library/intro#availability" >}}): macOS

> Added in version 3.12.
>

## os.**getresuid**()

​	返回一个由 (ruid, euid, suid) 所组成的元组，分别表示当前进程的真实用户ID，有效用户ID和暂存用户ID。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

> Added in version 3.2.
>

## os.**getresgid**()

​	返回一个由 (rgid, egid, sgid) 所组成的元组，分别表示当前进程的真实组ID，有效组ID和暂存组ID。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

> Added in version 3.2.
>

## os.**getuid**()

​	返回当前进程的真实用户ID。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

​	该函数在 WASI 上为空代码段，请参阅 [WebAssembly 平台]({{< ref "/library/intro#wasm-availability" >}}) 了解详情。

## os.**initgroups**(*username*, *gid*, */*)

​	调用系统 initgroups()，使用指定用户所在的所有值来初始化组访问列表，包括指定的组ID。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android.

> Added in version 3.2.
>

## os.**putenv**(*key*, *value*, */*)

​	将名为 *key* 的环境变量值设置为 *value*。该变量名修改会影响由 [`os.system()`]({{< ref "/library/allos/os#os.system" >}})， [`popen()`]({{< ref "/library/allos/os#os.popen" >}}) ，[`fork()`]({{< ref "/library/allos/os#os.fork" >}}) 和 [`execv()`]({{< ref "/library/allos/os#os.execv" >}}) 发起的子进程。

​	对 [`os.environ`]({{< ref "/library/allos/os#os.environ" >}}) 中的项目的赋值会自动转化为对 [`putenv()`]({{< ref "/library/allos/os#os.putenv" >}}) 的相应调用；然而，对 [`putenv()`]({{< ref "/library/allos/os#os.putenv" >}}) 的调用并不更新 [`os.environ`]({{< ref "/library/allos/os#os.environ" >}}) ，所以实际上最好是赋值到 [`os.environ`]({{< ref "/library/allos/os#os.environ" >}}) 的项目。这也适用于 [`getenv()`]({{< ref "/library/allos/os#os.getenv" >}}) 和 [`getenvb()`]({{< ref "/library/allos/os#os.getenvb" >}}) ，它们分别使用 [`os.environ`]({{< ref "/library/allos/os#os.environ" >}}) 和 [`os.environb`]({{< ref "/library/allos/os#os.environb" >}}) 在它们的实现中。

​	备注

 

​	在某些平台上，包括 FreeBSD 和 macOS 等，设置 `environ` 可能会导致内存泄漏。 请参阅有关 `putenv()` 的系统文档。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.putenv` 并附带参数 `key`, `value`。

*在 3.9 版本发生变更:* 该函数现在总是可用。

## os.**setegid**(*egid*, */*)

​	设置当前进程的有效组ID。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android.

## os.**seteuid**(*euid*, */*)

​	设置当前进程的有效用户ID。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android.

## os.**setgid**(*gid*, */*)

​	设置当前进程的组ID。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android.

## os.**setgroups**(*groups*, */*)

​	将 *group* 参数值设置为与当进程相关联的附加组ID列表。*group* 参数必须为一个序列，每个元素应为每个组的数字ID。该操作通常只适用于超级用户。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

​	备注

 

​	在 macOS 中，*groups* 的长度不能超过系统定义的最大有效组 ID 数量，通常为 16。 对于未返回与调用 setgroups() 产生的相同组列表的情况，请参阅 [`getgroups()`]({{< ref "/library/allos/os#os.getgroups" >}}) 的文档。

## os.**setns**(*fd*, *nstype=0*)

​	将当前线程与 Linux 命名空间重新关联。 详情参见 *[setns(2)](https://manpages.debian.org/setns(2))* 和 *[namespaces(7)](https://manpages.debian.org/namespaces(7))* 手册页面。

​	如果 *fd* 是指向一个 `/proc/*pid*/ns/` 链接，`setns()` 会将调用线程与该链接所关联的命名空间重新关联起来，并且 *nstype* 可以设为某个 [CLONE_NEW* 常量]({{< ref "/library/allos/os#os-unshare-clone-flags" >}}) 以便对操作施加约束 (`0` 表示没有任何约束)。

​	自 Linux 5.8 起，*fd* 可以是通过 [`pidfd_open()`]({{< ref "/library/allos/os#os.pidfd_open" >}}) 获取的 PID 文件描述符。在这种情况下，`setns()` 会将调用线程重新关联到与 *fd* 引用的线程相同的一个或多个命名空间。位掩码 *nstype* 通常会结合一个或多个 [CLONE_NEW* 常量]({{< ref "/library/allos/os#os-unshare-clone-flags" >}}) ，例如 `setns(fd, os.CLONE_NEWUTS | os.CLONE_NEWPID)`，其施加的任何约束限制仍然保留，调用者在未指定的命名空间中的成员资格保持不变。

*fd* 可以是任何带有 [`fileno()`]({{< ref "/library/allos/io#io.IOBase.fileno" >}}) 方法的对象，或是一个原始文件描述符。

​	此示例将线程与 `init` 进程的网络命名空间进行了重新关联:

```
fd = os.open("/proc/1/ns/net", os.O_RDONLY)
os.setns(fd, os.CLONE_NEWNET)
os.close(fd)
```

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 3.0 with glibc >= 2.14.

> Added in version 3.12.
>

​	参见

 

[`unshare()`]({{< ref "/library/allos/os#os.unshare" >}}) 函数。

## os.**setpgrp**()

​	在系统调用 `setpgrp()` 和 `setpgrp(0, 0)` 中择一调用，具体取决于何种实现版本可用（如果任一实现存在的话）。请参阅 Unix 手册以了解语义。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**setpgid**(*pid*, *pgrp*, */*)

​	使用系统调用 `setpgid()` 将 *pid* 对应进程的组 ID 设置为 *pgrp*。请参阅 Unix 手册以了解语义。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**setpriority**(*which*, *who*, *priority*)

​	设置程序调度优先级。 *which* 的值为 [`PRIO_PROCESS`]({{< ref "/library/allos/os#os.PRIO_PROCESS" >}}), [`PRIO_PGRP`]({{< ref "/library/allos/os#os.PRIO_PGRP" >}}) 或 [`PRIO_USER`]({{< ref "/library/allos/os#os.PRIO_USER" >}}) 之一，而 *who* 会相对于 *which* ([`PRIO_PROCESS`]({{< ref "/library/allos/os#os.PRIO_PROCESS" >}}) 的进程标识符, [`PRIO_PGRP`]({{< ref "/library/allos/os#os.PRIO_PGRP" >}}) 的进程组标识符和 [`PRIO_USER`]({{< ref "/library/allos/os#os.PRIO_USER" >}}) 的用户 ID) 被解析。 *who* 值为零 (分别) 表示调用进程，调用进程的进程组或调用进程的真实用户 ID。 *priority* 是范围在 -20 至 19 的值。 默认优先级为 0；较小的优先级数值会更优先被调度。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

> Added in version 3.3.
>

## os.**setregid**(*rgid*, *egid*, */*)

​	设置当前进程的真实和有效组ID。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android.

## os.**setresgid**(*rgid*, *egid*, *sgid*, */*)

​	设置当前进程的真实，有效和暂存组ID。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android.

> Added in version 3.2.
>

## os.**setresuid**(*ruid*, *euid*, *suid*, */*)

​	设置当前进程的真实，有效和暂存用户ID。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android.

> Added in version 3.2.
>

## os.**setreuid**(*ruid*, *euid*, */*)

​	设置当前进程的真实和有效用户ID。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android.

## os.**getsid**(*pid*, */*)

​	调用系统调用 `getsid()`。 其语义请参见 Unix 手册。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**setsid**()

​	调用系统调用 `setsid()`。 其语义请参见 Unix 手册。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**setuid**(*uid*, */*)

​	设置当前进程的用户ID。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android.

## os.**strerror**(*code*, */*)

​	根据 *code* 中的错误码返回错误消息。如果 `strerror()` 返回 `NULL` ，说明给出的是未知错误码，则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

## os.**supports_bytes_environ**

​	如果操作系统上原生环境类型是字节型则为 `True` (例如在 Windows 上为 `False`)。

> Added in version 3.2.
>

## os.**umask**(*mask*, */*)

​	设定当前数值掩码并返回之前的掩码。

​	该函数在 WASI 上为空代码段，请参阅 [WebAssembly 平台]({{< ref "/library/intro#wasm-availability" >}}) 了解详情。

## os.**uname**()

​	返回当前操作系统的识别信息。返回值是一个有5个属性的对象：

- `sysname` - 操作系统名
- `nodename` - 机器在网络上的名称（需要先设定）
- `release` - 操作系统发行信息
- `version` - 操作系统版本信息
- `machine` - 硬件标识符

​	为了向后兼容，该对象也是可迭代的，像是一个按照 `sysname`，`nodename`，`release`，`version`，和 `machine` 顺序组成的元组。

​	有些系统会将 `nodename` 截短为 8 个字符或截短至前缀部分；获取主机名的一个更好方式是 [`socket.gethostname()`]({{< ref "/library/ipc/socket#socket.gethostname" >}}) 或甚至可以用 `socket.gethostbyaddr(socket.gethostname())`。

​	在 macOS, iOS 和 Android 上，这将返回 *内核* 名称和版本 (即在 macOS 和 iOS 上为 `'Darwin'`; 在 Android 上为 `'Linux'`)。 [`platform.uname()`]({{< ref "/library/allos/platform#platform.uname" >}}) 可被用来在 iOS 和 Android 上获取面向用户的操作系统名称和版本。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

*在 3.3 版本发生变更:* 返回结果的类型由元组变成一个类似元组的对象，同时具有命名的属性。

## os.**unsetenv**(*key*, */*)

​	取消设置（删除）名为 *key* 的环境变量。变量名的改变会影响由 [`os.system()`]({{< ref "/library/allos/os#os.system" >}})，[`popen()`]({{< ref "/library/allos/os#os.popen" >}})，[`fork()`]({{< ref "/library/allos/os#os.fork" >}}) 和 [`execv()`]({{< ref "/library/allos/os#os.execv" >}}) 触发的子进程。

​	删除 [`os.environ`]({{< ref "/library/allos/os#os.environ" >}}) 中的项目会自动转化为对 [`unsetenv()`]({{< ref "/library/allos/os#os.unsetenv" >}}) 的相应调用；然而，对 [`unsetenv()`]({{< ref "/library/allos/os#os.unsetenv" >}}) 的调用并不更新 [`os.environ`]({{< ref "/library/allos/os#os.environ" >}}) ，所以实际上最好是删除 [`os.environ`]({{< ref "/library/allos/os#os.environ" >}}) 的项目。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.unsetenv` 并附带参数 `key`。

*在 3.9 版本发生变更:* 该函数现在总是可用，并且在 Windows 上也可用。

## os.**unshare**(*flags*)

​	拆分进程执行上下文的部分内容，并将其移入新创建的命名空间中。 请参阅 *[unshare(2)](https://manpages.debian.org/unshare(2))* 手册页了解详情。 *flags* 参数是一个位掩码，它组合了零个或多个 [CLONE_* 常量]({{< ref "/library/allos/os#os-unshare-clone-flags" >}})，用于指定执行上下文中的哪些部分应从现有关联中解除共享并移动到新的命名空间。 如果 *flags* 参数为 `0`，则不会对调用方进程的执行上下文进行任何更改。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.16.

> Added in version 3.12.
>

​	参见

 

[`setns()`]({{< ref "/library/allos/os#os.setns" >}}) 函数。

[`unshare()`]({{< ref "/library/allos/os#os.unshare" >}}) 函数的旗标，如果实现支持。 访问 Linux 手册中的 *[unshare(2)](https://manpages.debian.org/unshare(2))* 以获取关于实际影响和可用性的信息。

## os.**CLONE_FILES**

## os.**CLONE_FS**

## os.**CLONE_NEWCGROUP**

## os.**CLONE_NEWIPC**

## os.**CLONE_NEWNET**

## os.**CLONE_NEWNS**

## os.**CLONE_NEWPID**

## os.**CLONE_NEWTIME**

## os.**CLONE_NEWUSER**

## os.**CLONE_NEWUTS**

## os.**CLONE_SIGHAND**

## os.**CLONE_SYSVSEM**

## os.**CLONE_THREAD**

## os.**CLONE_VM**



## 创建文件对象

​	这些函数创建新的 [file objects]({{< ref "/glossary/idx#term-file-object" >}}) 。（参见 [`open()`]({{< ref "/library/allos/os#os.open" >}}) 以获取打开文件描述符的相关信息。）

## os.**fdopen**(*fd*, **args*, ***kwargs*)

​	返回打开文件描述符 *fd* 对应文件的对象。类似内建 [`open()`]({{< ref "/library/functions#open" >}}) 函数，二者接受同样的参数。不同之处在于 [`fdopen()`]({{< ref "/library/allos/os#os.fdopen" >}}) 第一个参数应该为整数。



## 文件描述符操作

​	这些函数对文件描述符所引用的 I/O 流进行操作。

​	文件描述符是一些小的整数，对应于当前进程所打开的文件。例如，标准输入的文件描述符通常是0，标准输出是1，标准错误是2。之后被进程打开的文件的文件描述符会被依次指定为3，4，5等。“文件描述符”这个词有点误导性，在 Unix 平台中套接字和管道也被文件描述符所引用。

​	当需要时，可以用 [`fileno()`]({{< ref "/library/allos/io#io.IOBase.fileno" >}}) 可以获得 [file object]({{< ref "/glossary/idx#term-file-object" >}}) 所对应的文件描述符。需要注意的是，直接使用文件描述符会绕过文件对象的方法，会忽略如数据内部缓冲等情况。

## os.**close**(*fd*)

​	关闭文件描述符 *fd*。

​	备注

 

​	该功能适用于低级 I/O 操作，必须用于 [`os.open()`]({{< ref "/library/allos/os#os.open" >}}) 或 [`pipe()`]({{< ref "/library/allos/os#os.pipe" >}}) 返回的文件描述符。若要关闭由内建函数 [`open()`]({{< ref "/library/functions#open" >}})、[`popen()`]({{< ref "/library/allos/os#os.popen" >}}) 或 [`fdopen()`]({{< ref "/library/allos/os#os.fdopen" >}}) 返回的 "文件对象"，则应使用其相应的 [`close()`]({{< ref "/library/allos/io#io.IOBase.close" >}}) 方法。

## os.**closerange**(*fd_low*, *fd_high*, */*)

​	关闭从 *fd_low* （包括）到 *fd_high* （排除）间的文件描述符，并忽略错误。类似（但快于）:

```
for fd in range(fd_low, fd_high):
    try:
        os.close(fd)
    except OSError:
        pass
```

## os.**copy_file_range**(*src*, *dst*, *count*, *offset_src=None*, *offset_dst=None*)

​	从文件描述符 *src* 自偏移量 *offset_src* 起的位置拷贝 *count* 个字节到文件描述符 *dst* 自偏移量 *offset_dst* 起的位置。 如果 *offset_src* 为 `None`，则从当前位置读取 *src*；相应地 *offset_dst* 也是如此。

​	在早于 5.3 版的 Linux 内核中，*src* 和 *dst* 指向的文件必须位于相同的文件系统中，否则会引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 并将 [`errno`]({{< ref "/library/exceptions#OSError.errno" >}}) 设为 [`errno.EXDEV`]({{< ref "/library/allos/errno#errno.EXDEV" >}})。

​	执行这种拷贝无需付出将数据从内核传输到用户空间再返回内核的额外耗费。 此外，某些文件系统还可以实现进一步的优化，例如使用引用链接（即两个或多个共享指向相同写入时复制磁盘块的指针的 inode；支持的文件系统包括 btrfs 和 XFS）和服务器端拷贝（对于 NFS）。

​	此函数在两个文件描述符之间拷贝字节数据。 文本选项，如编码格式和行结束符等将被忽略。

​	返回值是复制的字节的数目。这可能低于需求的数目。

​	备注

 

​	在 Linux 上，[`os.copy_file_range()`]({{< ref "/library/allos/os#os.copy_file_range" >}}) 不应被用于从特殊的文件系统如 procfs 和 sysfs 复制特定范围的伪文件。 因为已知的 Linux 内核问题它将总是不复制任何字节并返回 0 就像文件是空的一样。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 4.5 with glibc >= 2.27.

> Added in version 3.8.
>

## os.**device_encoding**(*fd*)

​	如果连接到终端，则返回一个与 *fd* 关联的设备描述字符，否则返回 [`None`]({{< ref "/library/constants#None" >}})。

​	在 Unix 上，如果启用了 [Python UTF-8 模式]({{< ref "/library/allos/os#utf8-mode" >}})，则返回 `'UTF-8'` 而不是设备的编码格式。

*在 3.10 版本发生变更:* 在 Unix 上，该函数现在实现了 Python UTF-8 模式。

## os.**dup**(*fd*, */*)

​	返回一个文件描述符 *fd* 的副本。该文件描述符的副本是 [不可继承的]({{< ref "/library/allos/os#fd-inheritance" >}})。

​	在 Windows 中，当复制一个标准流（0: stdin, 1: stdout, 2: stderr）时，新的文件描述符是 [可继承的]({{< ref "/library/allos/os#fd-inheritance" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

*在 3.4 版本发生变更:* 新的文件描述符现在是不可继承的。

## os.**dup2**(*fd*, *fd2*, *inheritable=True*)

​	把文件描述符 *fd* 复制为 *fd2*，必要时先关闭后者。返回 *fd2*。新的文件描述符默认是 [可继承的]({{< ref "/library/allos/os#fd-inheritance" >}})，除非在 *inheritable* 为 `False` 时，是不可继承的。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

*在 3.4 版本发生变更:* 添加可选参数 *inheritable*。

*在 3.7 版本发生变更:* 成功时返回 *fd2*，以过去的版本中，总是返回 `None`。

## os.**fchmod**(*fd*, *mode*)

​	将 *fd* 指定文件的权限状态修改为 *mode*。可以参考 [`chmod()`]({{< ref "/library/allos/os#os.chmod" >}}) 中列出 *mode* 的可用值。从Python 3.3开始，这相当于 `os.chmod(fd, mode)`。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.chmod` 并附带参数 `path`, `mode`, `dir_fd`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows.

​	此函数在 WASI 是受限的，请参阅 [WebAssembly 平台]({{< ref "/library/intro#wasm-availability" >}}) 了解详情。

*在 3.13 版本发生变更:* 增加 Windows 上的支持。

## os.**fchown**(*fd*, *uid*, *gid*)

​	分别将 *fd* 指定文件的所有者和组 ID 修改为 *uid* 和 *gid* 的值。若不想变更其中的某个 ID，可将相应值设为 -1。参考 [`chown()`]({{< ref "/library/allos/os#os.chown" >}})。从 Python 3.3 开始，这相当于 `os.chown(fd, uid, gid)`。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.chown` 并附带参数 `path`, `uid`, `gid`, `dir_fd`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

​	此函数在 WASI 是受限的，请参阅 [WebAssembly 平台]({{< ref "/library/intro#wasm-availability" >}}) 了解详情。

## os.**fdatasync**(*fd*)

​	强制将文件描述符 *fd* 指定文件写入磁盘。不强制更新元数据。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

​	备注

 

​	该功能在 MacOS 中不可用。

## os.**fpathconf**(*fd*, *name*, */*)

​	返回与打开的文件有关的系统配置信息。*name* 指定要查找的配置名称，它可以是字符串，是一个系统已定义的名称，这些名称定义在不同标准（POSIX.1，Unix 95，Unix 98 等）中。一些平台还定义了额外的其他名称。当前操作系统已定义的名称在 `pathconf_names` 字典中给出。对于未包含在该映射中的配置名称，也可以传递一个整数作为 *name*。

​	如果 *name* 是一个字符串且不是已定义的名称，将抛出 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 异常。如果当前系统不支持 *name* 指定的配置名称，即使该名称存在于 `pathconf_names`，也会抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 异常，错误码为 [`errno.EINVAL`]({{< ref "/library/allos/errno#errno.EINVAL" >}})。

​	从 Python 3.3 起，此功能等价于 `os.pathconf(fd, name)`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

## os.**fstat**(*fd*)

​	获取文件描述符 *fd* 的状态. 返回一个 [`stat_result`]({{< ref "/library/allos/os#os.stat_result" >}}) 对象。

​	从 Python 3.3 起，此功能等价于 `os.stat(fd)`。

​	参见

 

[`stat()`]({{< ref "/library/allos/os#os.stat" >}}) 函数。

## os.**fstatvfs**(*fd*, */*)

​	返回文件系统的信息，该文件系统是文件描述符 *fd* 指向的文件所在的文件系统，与 [`statvfs()`]({{< ref "/library/allos/os#os.statvfs" >}}) 一样。从 Python 3.3 开始，它等效于 `os.statvfs(fd)`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

## os.**fsync**(*fd*)

​	强制将文件描述符 *fd* 指向的文件写入磁盘。 在 Unix 上，这将调用原生 `fsync()` 函数；在 Windows 上，则是 MS `_commit()` 函数。

​	如果要写入的是缓冲区内的 Python [文件对象]({{< ref "/glossary/idx#term-file-object" >}}) *f*，请先执行 `f.flush()`，然后执行 `os.fsync(f.fileno())`，以确保与 *f* 关联的所有内部缓冲区都写入磁盘。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows.

## os.**ftruncate**(*fd*, *length*, */*)

​	截断文件描述符 *fd* 指向的文件，以使其最大为 *length* 字节。从 Python 3.3 开始，它等效于 `os.truncate(fd, length)`。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.truncate` 并附带参数 `fd`, `length`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows.

*在 3.5 版本发生变更:* 添加了 Windows 支持

## os.**get_blocking**(*fd*, */*)

​	获取文件描述符的阻塞模式：如果设置了 [`O_NONBLOCK`]({{< ref "/library/allos/os#os.O_NONBLOCK" >}}) 标志位，返回 `False`，如果该标志位被清除，返回 `True`。

​	参见 [`set_blocking()`]({{< ref "/library/allos/os#os.set_blocking" >}}) 和 [`socket.socket.setblocking()`]({{< ref "/library/ipc/socket#socket.socket.setblocking" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows.

​	此函数在 WASI 是受限的，请参阅 [WebAssembly 平台]({{< ref "/library/intro#wasm-availability" >}}) 了解详情。

​	在 Windows 上，此函数仅限于管道。

> Added in version 3.5.
>

*在 3.12 版本发生变更:* 增加了在Windows上对于管道的支持。

## os.**grantpt**(*fd*, */*)

​	允许访问与文件描述符 *fd* 所指向的主伪终端设备相关联的从伪终端设备。 文件描述符 *fd* 在失败时不会被关闭。

​	调用 C 标准库函数 `grantpt()`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

> Added in version 3.13.
>

## os.**isatty**(*fd*, */*)

​	如果文件描述符 *fd* 打开且已连接至 tty 设备（或类 tty 设备），返回 `True`，否则返回 `False`。

## os.**lockf**(*fd*, *cmd*, *len*, */*)

​	在打开的文件描述符上，使用、测试或删除 POSIX 锁。*fd* 是一个打开的文件描述符。*cmd* 指定要进行的操作，它们是 [`F_LOCK`]({{< ref "/library/allos/os#os.F_LOCK" >}})、[`F_TLOCK`]({{< ref "/library/allos/os#os.F_TLOCK" >}})、[`F_ULOCK`]({{< ref "/library/allos/os#os.F_ULOCK" >}}) 或 [`F_TEST`]({{< ref "/library/allos/os#os.F_TEST" >}}) 中的一个。*len* 指定哪部分文件需要锁定。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.lockf` 并附带参数 `fd`, `cmd`, `len`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

> Added in version 3.3.
>

## os.**F_LOCK**

## os.**F_TLOCK**

## os.**F_ULOCK**

## os.**F_TEST**

​	标志位，用于指定 [`lockf()`]({{< ref "/library/allos/os#os.lockf" >}}) 进行哪一种操作。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

> Added in version 3.3.
>

## os.**login_tty**(*fd*, */*)

​	准备 tty 设置 fd 为新登录会话的文件描述符。 设置调用方进程为会话主进程；设置该 tty 为主控 tty，调用方进程使用其 stdin, stdout 和 stderr；关闭 fd。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

> Added in version 3.11.
>

## os.**lseek**(*fd*, *pos*, *whence*, */*)

​	将文件描述符 *fd* 的当前位置设为位置 *pos*，经 *whence* 修正，并返回相对于文件开头的以字节为单位的新位置。 *whence* 的有效值为：

- [`SEEK_SET`]({{< ref "/library/allos/os#os.SEEK_SET" >}}) 或 `0` -- 相对于文件开头设置 *pos*
- [`SEEK_CUR`]({{< ref "/library/allos/os#os.SEEK_CUR" >}}) 或 `1` -- 相对于当前文件位置设置 *pos*
- [`SEEK_END`]({{< ref "/library/allos/os#os.SEEK_END" >}}) 或 `2` -- 相对于文件末尾设置 *pos*
- [`SEEK_HOLE`]({{< ref "/library/allos/os#os.SEEK_HOLE" >}}) -- 将 *pos* 设置为相对于 *pos* 的下一个数据位置
- [`SEEK_DATA`]({{< ref "/library/allos/os#os.SEEK_DATA" >}}) -- 将 *pos* 设为相对于 *pos* 的下一个数据空位

*在 3.3 版本发生变更:* 增加对 `SEEK_HOLE` 和 `SEEK_DATA` 的支持。

## os.**SEEK_SET**

## os.**SEEK_CUR**

## os.**SEEK_END**

​	传给 [`lseek()`]({{< ref "/library/allos/os#os.lseek" >}}) 函数和 [文件型对象]({{< ref "/glossary/idx#term-file-object" >}}) 上 [`seek()`]({{< ref "/library/allos/io#io.IOBase.seek" >}}) 方法的形参，用于调整文件位置指示器。

## [`SEEK_SET`]({{< ref "/library/allos/os#os.SEEK_SET" >}})

​	相对于文件的开头调整文件位置。

## [`SEEK_CUR`]({{< ref "/library/allos/os#os.SEEK_CUR" >}})

​	相对于当前文件位置调整文件位置。

## [`SEEK_END`]({{< ref "/library/allos/os#os.SEEK_END" >}})

​	相对于文件的末尾调整文件位置。

​	它们的值分别为 0, 1 和 2。

## os.**SEEK_HOLE**

## os.**SEEK_DATA**

​	传给 [`lseek()`]({{< ref "/library/allos/os#os.lseek" >}}) 函数和 [文件型对象]({{< ref "/glossary/idx#term-file-object" >}}) 上 [`seek()`]({{< ref "/library/allos/io#io.IOBase.seek" >}}) 方法的形参，用于查找文件数据和稀疏分配的文件中的空洞。

## `SEEK_DATA`

​	相对于查找位置调整到下一个包含数据的位置的文件偏移量。

## `SEEK_HOLE`

​	相对于查找位置调整到下一个包含空洞的位置的文件偏移量。 空洞被定义为零值的序列。

​	备注

 

​	这些操作只对支持它们的文件系统具有意义。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 3.1, macOS, Unix

> Added in version 3.3.
>

## os.**open**(*path*, *flags*, *mode=0o777*, ***, *dir_fd=None*)

​	打开文件 *path*，根据 *flags* 设置各种标志位，并根据 *mode* 设置其权限状态。当计算 *mode* 时，会首先根据当前 umask 值将部分权限去除。本方法返回新文件的描述符。新的文件描述符是 [不可继承]({{< ref "/library/allos/os#fd-inheritance" >}}) 的。

​	有关 flag 和 mode 取值的说明，请参见 C 运行时文档。标志位常量（如 [`O_RDONLY`]({{< ref "/library/allos/os#os.O_RDONLY" >}}) 和 [`O_WRONLY`]({{< ref "/library/allos/os#os.O_WRONLY" >}})）在 [`os`]({{< ref "/library/allos/os#module-os" >}}) 模块中定义。特别地，在 Windows 上需要添加 [`O_BINARY`]({{< ref "/library/allos/os#os.O_BINARY" >}}) 才能以二进制模式打开文件。

​	本函数带有 *dir_fd* 参数，支持 [基于目录描述符的相对路径]({{< ref "/library/allos/os#dir-fd" >}})。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `open` 并附带参数 `path`, `mode`, `flags`。

*在 3.4 版本发生变更:* 新的文件描述符现在是不可继承的。

​	备注

 

​	本函数适用于底层的 I/O。常规用途请使用内置函数 [`open()`]({{< ref "/library/functions#open" >}})，该函数的 `read()` 和 `write()` 方法（及其他方法）会返回 [文件对象]({{< ref "/glossary/idx#term-file-object" >}})。要将文件描述符包装在文件对象中，请使用 [`fdopen()`]({{< ref "/library/allos/os#os.fdopen" >}})。

*在 3.3 版本发生变更:* 添加了 *dir_fd* 参数。

*在 3.5 版本发生变更:* 如果系统调用被中断，但信号处理程序没有触发异常，此函数现在会重试系统调用，而不是触发 [`InterruptedError`]({{< ref "/library/exceptions#InterruptedError" >}}) 异常 (原因详见 [**PEP 475**](https://peps.python.org/pep-0475/))。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

​	以下常量是 [`open()`]({{< ref "/library/allos/os#os.open" >}}) 函数 *flags* 参数的选项。可以用按位或运算符 `|` 将它们组合使用。部分常量并非在所有平台上都可用。有关其可用性和用法的说明，请参阅 *[open(2)](https://manpages.debian.org/open(2))* 手册（Unix 上）或 [MSDN](https://msdn.microsoft.com/en-us/library/z0kc8e3z.aspx) （Windows 上）。

## os.**O_RDONLY**

## os.**O_WRONLY**

## os.**O_RDWR**

## os.**O_APPEND**

## os.**O_CREAT**

## os.**O_EXCL**

## os.**O_TRUNC**

​	上述常量在 Unix 和 Windows 上均可用。

## os.**O_DSYNC**

## os.**O_RSYNC**

## os.**O_SYNC**

## os.**O_NDELAY**

## os.**O_NONBLOCK**

## os.**O_NOCTTY**

## os.**O_CLOEXEC**

​	这个常数仅在 Unix 系统中可用。

*在 3.3 版本发生变更:* 增加 [`O_CLOEXEC`]({{< ref "/library/allos/os#os.O_CLOEXEC" >}}) 常量。

## os.**O_BINARY**

## os.**O_NOINHERIT**

## os.**O_SHORT_LIVED**

## os.**O_TEMPORARY**

## os.**O_RANDOM**

## os.**O_SEQUENTIAL**

## os.**O_TEXT**

​	这个常数仅在 Windows 系统中可用。

## os.**O_EVTONLY**

## os.**O_FSYNC**

## os.**O_SYMLINK**

## os.**O_NOFOLLOW_ANY**

​	以上常量仅适用于 macOS。

*在 3.10 版本发生变更:* 加入 [`O_EVTONLY`]({{< ref "/library/allos/os#os.O_EVTONLY" >}}) 、 [`O_FSYNC`]({{< ref "/library/allos/os#os.O_FSYNC" >}}) 、 [`O_SYMLINK`]({{< ref "/library/allos/os#os.O_SYMLINK" >}}) 和 [`O_NOFOLLOW_ANY`]({{< ref "/library/allos/os#os.O_NOFOLLOW_ANY" >}}) 常量。

## os.**O_ASYNC**

## os.**O_DIRECT**

## os.**O_DIRECTORY**

## os.**O_NOFOLLOW**

## os.**O_NOATIME**

## os.**O_PATH**

## os.**O_TMPFILE**

## os.**O_SHLOCK**

## os.**O_EXLOCK**

​	上述常量是扩展常量，如果 C 库未定义它们，则不存在。

*在 3.4 版本发生变更:* 在支持的系统上增加 [`O_PATH`]({{< ref "/library/allos/os#os.O_PATH" >}})。增加 [`O_TMPFILE`]({{< ref "/library/allos/os#os.O_TMPFILE" >}})，仅在 Linux Kernel 3.11 或更高版本可用。

## os.**openpty**()

​	打开一对新的伪终端，返回一对文件描述符 `（主，从）`，分别为 pty 和 tty。新的文件描述符是 [不可继承]({{< ref "/library/allos/os#fd-inheritance" >}}) 的。对于（稍微）轻量一些的方法，请使用 [`pty`]({{< ref "/library/unix/pty#module-pty" >}}) 模块。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

*在 3.4 版本发生变更:* 新的文件描述符不再可继承。

## os.**pipe**()

​	创建一个管道，返回一对分别用于读取和写入的文件描述符 `(r, w)`。新的文件描述符是 [不可继承]({{< ref "/library/allos/os#fd-inheritance" >}}) 的。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows.

*在 3.4 版本发生变更:* 新的文件描述符不再可继承。

## os.**pipe2**(*flags*, */*)

​	创建带有 *flags* 标志位的管道。可通过对以下一个或多个值进行“或”运算来构造这些 *flags*：[`O_NONBLOCK`]({{< ref "/library/allos/os#os.O_NONBLOCK" >}})、[`O_CLOEXEC`]({{< ref "/library/allos/os#os.O_CLOEXEC" >}})。返回一对分别用于读取和写入的文件描述符 `(r, w)`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

> Added in version 3.3.
>

## os.**posix_fallocate**(*fd*, *offset*, *len*, */*)

​	确保为 *fd* 指向的文件分配了足够的磁盘空间，该空间从偏移量 *offset* 开始，到 *len* 字节为止。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

> Added in version 3.3.
>

## os.**posix_fadvise**(*fd*, *offset*, *len*, *advice*, */*)

​	声明即将以特定模式访问数据，使内核可以提前进行优化。数据范围是从 *fd* 所指向文件的 *offset* 开始，持续 *len* 个字节。*advice* 的取值是如下之一：[`POSIX_FADV_NORMAL`]({{< ref "/library/allos/os#os.POSIX_FADV_NORMAL" >}}), [`POSIX_FADV_SEQUENTIAL`]({{< ref "/library/allos/os#os.POSIX_FADV_SEQUENTIAL" >}}), [`POSIX_FADV_RANDOM`]({{< ref "/library/allos/os#os.POSIX_FADV_RANDOM" >}}), [`POSIX_FADV_NOREUSE`]({{< ref "/library/allos/os#os.POSIX_FADV_NOREUSE" >}}), [`POSIX_FADV_WILLNEED`]({{< ref "/library/allos/os#os.POSIX_FADV_WILLNEED" >}}) 或 [`POSIX_FADV_DONTNEED`]({{< ref "/library/allos/os#os.POSIX_FADV_DONTNEED" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

> Added in version 3.3.
>

## os.**POSIX_FADV_NORMAL**

## os.**POSIX_FADV_SEQUENTIAL**

## os.**POSIX_FADV_RANDOM**

## os.**POSIX_FADV_NOREUSE**

## os.**POSIX_FADV_WILLNEED**

## os.**POSIX_FADV_DONTNEED**

​	用于 [`posix_fadvise()`]({{< ref "/library/allos/os#os.posix_fadvise" >}}) 的 *advice* 参数的标志位，指定可能使用的访问模式。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

> Added in version 3.3.
>

## os.**pread**(*fd*, *n*, *offset*, */*)

​	从文件描述符 *fd* 所指向文件的偏移位置 *offset* 开始，读取至多 *n* 个字节，而保持文件偏移量不变。

​	返回所读取字节的字节串 (bytestring)。如果到达了 *fd* 指向的文件末尾，则返回空字节对象。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

> Added in version 3.3.
>

## os.**posix_openpt**(*oflag*, */*)

​	打开并返回一个代表主要伪终端设备的文件描述符。

​	调用 C 标准库函数 `posix_openpt()`。 *oflag* 参数用于设置文件状态旗标和文件访问模式，如你所用系统的 `posix_openpt()` 帮助页中所指明的那样。

​	返回的文件描述符是 [非不可继承的]({{< ref "/library/allos/os#fd-inheritance" >}})。 如果所在系统上 [`O_CLOEXEC`]({{< ref "/library/allos/os#os.O_CLOEXEC" >}}) 值是可用的，它将被添加到 *oflag*。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

> Added in version 3.13.
>

## os.**preadv**(*fd*, *buffers*, *offset*, *flags=0*, */*)

​	从文件描述符 *fd* 所指向文件的偏移位置 *offset* 开始，将数据读取至可变 [字节类对象]({{< ref "/glossary/idx#term-bytes-like-object" >}}) 缓冲区 *buffers* 中，保持文件偏移量不变。将数据依次存放到每个缓冲区中，填满一个后继续存放到序列中的下一个缓冲区，来保存其余数据。

​	flags 参数可以由零个或多个标志位进行按位或运算来得到：

- [`RWF_HIPRI`]({{< ref "/library/allos/os#os.RWF_HIPRI" >}})
- [`RWF_NOWAIT`]({{< ref "/library/allos/os#os.RWF_NOWAIT" >}})

​	返回实际读取的字节总数，该总数可以小于所有对象的总容量。

​	操作系统可能对允许使用的缓冲区数量有限制（使用 [`sysconf()`]({{< ref "/library/allos/os#os.sysconf" >}}) 获取 `'SC_IOV_MAX'` 值）。

​	本方法结合了 [`os.readv()`]({{< ref "/library/allos/os#os.readv" >}}) 和 [`os.pread()`]({{< ref "/library/allos/os#os.pread" >}}) 的功能。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.30, FreeBSD >= 6.0, OpenBSD >= 2.7, AIX >= 7.1.

​	使用旗标需要 Linux >= 4.6。

> Added in version 3.7.
>

## os.**RWF_NOWAIT**

​	不要等待无法立即获得的数据。如果指定了此标志，那么当需要从后备存储器中读取数据，或等待文件锁时，系统调用将立即返回。

​	如果成功读取了数据，将返回读取的字节数。 如果未读取到数据，将返回 `-1` 并将 errno 设为 [`errno.EAGAIN`]({{< ref "/library/allos/errno#errno.EAGAIN" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 4.14.

> Added in version 3.7.
>

## os.**RWF_HIPRI**

​	高优先级读/写。允许基于块的文件系统对设备进行轮询，这样可以降低延迟，但可能会占用更多资源。

​	目前在 Linux 上，此功能仅在使用 [`O_DIRECT`]({{< ref "/library/allos/os#os.O_DIRECT" >}}) 标志打开的文件描述符上可用。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 4.6.

> Added in version 3.7.
>

## os.**ptsname**(*fd*, */*)

​	返回与文件描述符 *fd* 所指向的主伪终端设备相关联的从伪终端设备。 文件描述符 *fd* 在失败时不会被关闭。

​	如果可重入 C 标准库函数 `ptsname_r()` 可用则调用它；在其他情况下，则调用 C 标准库函数 `ptsname()`，该函数不保证线程安全。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

> Added in version 3.13.
>

## os.**pwrite**(*fd*, *str*, *offset*, */*)

​	将 *str* 中的字节串 (bytestring) 写入文件描述符 *fd* 的偏移位置 *offset* 处，保持文件偏移量不变。

​	返回实际写入的字节数。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

> Added in version 3.3.
>

## os.**pwritev**(*fd*, *buffers*, *offset*, *flags=0*, */*)

​	将 *buffers* 内容写入文件描述符 *fd* 的偏移位置 *offset* 处，保持文件偏移位置不变。 *buffers* 必须是由 [字节型对象]({{< ref "/glossary/idx#term-bytes-like-object" >}}) 组成的序列。 缓冲区以数组顺序处理。 先写入第一个缓冲区的全部内容再写入第二个，依此类推。

​	flags 参数可以由零个或多个标志位进行按位或运算来得到：

- [`RWF_DSYNC`]({{< ref "/library/allos/os#os.RWF_DSYNC" >}})
- [`RWF_SYNC`]({{< ref "/library/allos/os#os.RWF_SYNC" >}})
- [`RWF_APPEND`]({{< ref "/library/allos/os#os.RWF_APPEND" >}})

​	返回实际写入的字节总数。

​	操作系统可能对允许使用的缓冲区数量有限制（使用 [`sysconf()`]({{< ref "/library/allos/os#os.sysconf" >}}) 获取 `'SC_IOV_MAX'` 值）。

​	本方法结合了 [`os.writev()`]({{< ref "/library/allos/os#os.writev" >}}) 和 [`os.pwrite()`]({{< ref "/library/allos/os#os.pwrite" >}}) 的功能。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.30, FreeBSD >= 6.0, OpenBSD >= 2.7, AIX >= 7.1.

​	使用旗标需要 Linux >= 4.6。

> Added in version 3.7.
>

## os.**RWF_DSYNC**

​	提供预写功能，等效于带 [`O_DSYNC`]({{< ref "/library/allos/os#os.O_DSYNC" >}}) 标志的 [`os.open()`]({{< ref "/library/allos/os#os.open" >}}) 。本标志只作用于通过系统调用写入的数据。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 4.7.

> Added in version 3.7.
>

## os.**RWF_SYNC**

​	提供预写功能，等效于带 [`O_SYNC`]({{< ref "/library/allos/os#os.O_SYNC" >}}) 标志的 [`os.open()`]({{< ref "/library/allos/os#os.open" >}}) 。本标志只作用于通过系统调用写入的数据。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 4.7.

> Added in version 3.7.
>

## os.**RWF_APPEND**

​	提供预写功能，等效于带 [`O_APPEND`]({{< ref "/library/allos/os#os.O_APPEND" >}}) 标志的 [`os.open()`]({{< ref "/library/allos/os#os.open" >}}) 。本标志只对 [`os.pwritev()`]({{< ref "/library/allos/os#os.pwritev" >}}) 有意义，只作用于通过系统调用写入的数据。参数 *offset* 对写入操作无效；数据总是会添加到文件的末尾。但如果 *offset* 参数为 `-1`，则会刷新当前文件的 *offset* 。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 4.16.

> Added in version 3.10.
>

## os.**read**(*fd*, *n*, */*)

​	从文件描述符 *fd* 中读取至多 *n* 个字节。

​	返回所读取字节的字节串 (bytestring)。如果到达了 *fd* 指向的文件末尾，则返回空字节对象。

​	备注

 

​	该功能适用于低级 I/O 操作，必须用于 [`os.open()`]({{< ref "/library/allos/os#os.open" >}}) 或 [`pipe()`]({{< ref "/library/allos/os#os.pipe" >}}) 返回的文件描述符。若要读取由内建函数 [`open()`]({{< ref "/library/functions#open" >}})、[`popen()`]({{< ref "/library/allos/os#os.popen" >}})、[`fdopen()`]({{< ref "/library/allos/os#os.fdopen" >}}) 或 [`sys.stdin`]({{< ref "/library/python/sys#sys.stdin" >}}) 返回的 "文件对象"，则应使用其相应的 `read()` 或 `readline()` 方法。

*在 3.5 版本发生变更:* 如果系统调用被中断，但信号处理程序没有触发异常，此函数现在会重试系统调用，而不是触发 [`InterruptedError`]({{< ref "/library/exceptions#InterruptedError" >}}) 异常 (原因详见 [**PEP 475**](https://peps.python.org/pep-0475/))。

## os.**sendfile**(*out_fd*, *in_fd*, *offset*, *count*)

## os.**sendfile**(*out_fd*, *in_fd*, *offset*, *count*, *headers=()*, *trailers=()*, *flags=0*)

​	将文件描述符 *in_fd* 中的 *count* 字节复制到文件描述符 *out_fd* 的偏移位置 *offset* 处。返回复制的字节数，如果到达 EOF，返回 `0`。

​	定义了 [`sendfile()`]({{< ref "/library/allos/os#os.sendfile" >}}) 的所有平台均支持第一种函数用法。

​	在 Linux 上，将 *offset* 设置为 `None`，则从 *in_fd* 的当前位置开始读取，并更新 *in_fd* 的位置。

​	第二种情况可以被用于 macOS 和 FreeBSD，其中 *headers* 和 *trailers* 是任意的缓冲区序列，它们会在写入来自 *in_fd* 的数据之前被写入。 它的返回内容与第一种情况相同。

​	在 macOS 和 FreeBSD 上，传入 `0` 值作为 *count* 将指定持续发送直至到达 *in_fd* 的末尾。

​	所有平台都支持将套接字作为 *out_fd* 文件描述符，有些平台也支持其他类型（如常规文件或管道）。

​	跨平台应用程序不应使用 *headers*、*trailers* 和 *flags* 参数。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

​	备注

 

​	有关 [`sendfile()`]({{< ref "/library/allos/os#os.sendfile" >}}) 的高级封装，参见 [`socket.socket.sendfile()`]({{< ref "/library/ipc/socket#socket.socket.sendfile" >}})。

> Added in version 3.3.
>

*在 3.9 版本发生变更:* *out* 和 *in* 参数被重命名为 *out_fd* 和 *in_fd*。

## os.**SF_NODISKIO**

## os.**SF_MNOWAIT**

## os.**SF_SYNC**

[`sendfile()`]({{< ref "/library/allos/os#os.sendfile" >}}) 函数的参数（假设当前实现支持这些参数）。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

> Added in version 3.3.
>

## os.**SF_NOCACHE**

​	传给 [`sendfile()`]({{< ref "/library/allos/os#os.sendfile" >}}) 函数的形参，如果具体实现支持的话。 数据不会缓存在虚拟内存中并将随即被释放。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

> Added in version 3.11.
>

## os.**set_blocking**(*fd*, *blocking*, */*)

​	设置指定文件描述符的阻塞模式：如果 blocking 为 `False`，则为该描述符设置 [`O_NONBLOCK`]({{< ref "/library/allos/os#os.O_NONBLOCK" >}}) 标志位，反之则清除该标志位。

​	参见 [`get_blocking()`]({{< ref "/library/allos/os#os.get_blocking" >}}) 和 [`socket.socket.setblocking()`]({{< ref "/library/ipc/socket#socket.socket.setblocking" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows.

​	此函数在 WASI 是受限的，请参阅 [WebAssembly 平台]({{< ref "/library/intro#wasm-availability" >}}) 了解详情。

​	在 Windows 上，此函数仅限于管道。

> Added in version 3.5.
>

*在 3.12 版本发生变更:* 增加了在Windows上对于管道的支持。

## os.**splice**(*src*, *dst*, *count*, *offset_src=None*, *offset_dst=None*)

​	从文件描述符 *src* 偏移量 *offset_src* 开始，将 *count* 个字节传输到文件描述符 *dst*。 至少有一个文件描述符必须指向一个管道。 如果 *offset_src* 为 `None`，则 *src* 将从当前位置开始读取；相应地 *offset_dst* 也是如此。 与指向管道的文件描述符相关的偏移量必须为 `None`。 *src* 和 *dst* 指向的文件必须位于相同文件系统中，否则会引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 并将 [`errno`]({{< ref "/library/exceptions#OSError.errno" >}}) 设为 [`errno.EXDEV`]({{< ref "/library/allos/errno#errno.EXDEV" >}})。

​	此复制的完成没有额外的从内核到用户空间再回到内核的数据转移花费。另外，一些文件系统可能实现额外的优化。完成复制就如同打开两个二进制文件一样。

​	调用成功后，返回拼接到管道的字节数或从管道拼接出来的字节数。返回值为 0 意味着输入结束。如果 *src* 指向一个管道，则意味着没有数据需要传输，而且由于没有写入程序连到管道的写入端，所以将不会阻塞。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.17 with glibc >= 2.5

> Added in version 3.10.
>

## os.**SPLICE_F_MOVE**

## os.**SPLICE_F_NONBLOCK**

## os.**SPLICE_F_MORE**

> Added in version 3.10.
>

## os.**readv**(*fd*, *buffers*, */*)

​	从文件描述符 *fd* 将数据读取至多个可变的 [字节类对象]({{< ref "/glossary/idx#term-bytes-like-object" >}}) 缓冲区 *buffers* 中。将数据依次存放到每个缓冲区中，填满一个后继续存放到序列中的下一个缓冲区，来保存其余数据。

​	返回实际读取的字节总数，该总数可以小于所有对象的总容量。

​	操作系统可能对允许使用的缓冲区数量有限制（使用 [`sysconf()`]({{< ref "/library/allos/os#os.sysconf" >}}) 获取 `'SC_IOV_MAX'` 值）。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

> Added in version 3.3.
>

## os.**tcgetpgrp**(*fd*, */*)

​	返回与 *fd* 指定的终端相关联的进程组（*fd* 是由 [`os.open()`]({{< ref "/library/allos/os#os.open" >}}) 返回的已打开的文件描述符）。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**tcsetpgrp**(*fd*, *pg*, */*)

​	设置与 *fd* 指定的终端相关联的进程组为 *pg\*（*fd* 是由 [`os.open()`]({{< ref "/library/allos/os#os.open" >}}) 返回的已打开的文件描述符）。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**ttyname**(*fd*, */*)

​	返回一个字符串，该字符串表示与文件描述符 *fd* 关联的终端。如果 *fd* 没有与终端关联，则抛出异常。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

## os.**unlockpt**(*fd*, */*)

​	解锁与文件描述符 *fd* 所指向的主伪终端设备相关联的从伪终端设备。 文件描述符 *fd* 在失败时不会被关闭。

​	调用 C 标准库函数 `unlockpt()`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

> Added in version 3.13.
>

## os.**write**(*fd*, *str*, */*)

​	将 *str* 中的字节串 (bytestring) 写入文件描述符 *fd*。

​	返回实际写入的字节数。

​	备注

 

​	该功能适用于低级 I/O 操作，必须用于 [`os.open()`]({{< ref "/library/allos/os#os.open" >}}) 或 [`pipe()`]({{< ref "/library/allos/os#os.pipe" >}}) 返回的文件描述符。若要写入由内建函数 [`open()`]({{< ref "/library/functions#open" >}})、[`popen()`]({{< ref "/library/allos/os#os.popen" >}})、[`fdopen()`]({{< ref "/library/allos/os#os.fdopen" >}})、[`sys.stdout`]({{< ref "/library/python/sys#sys.stdout" >}}) 或 [`sys.stderr`]({{< ref "/library/python/sys#sys.stderr" >}}) 返回的 "文件对象"，则应使用其相应的 `write()` 方法。

*在 3.5 版本发生变更:* 如果系统调用被中断，但信号处理程序没有触发异常，此函数现在会重试系统调用，而不是触发 [`InterruptedError`]({{< ref "/library/exceptions#InterruptedError" >}}) 异常 (原因详见 [**PEP 475**](https://peps.python.org/pep-0475/))。

## os.**writev**(*fd*, *buffers*, */*)

​	将缓冲区 *buffers* 的内容写入文件描述符 *fd*。缓冲区 *buffers* 必须是由 [字节类对象]({{< ref "/glossary/idx#term-bytes-like-object" >}}) 组成的序列。缓冲区以数组顺序处理。先写入第一个缓冲区的全部内容，再写入第二个缓冲区，照此继续。

​	返回实际写入的字节总数。

​	操作系统可能对允许使用的缓冲区数量有限制（使用 [`sysconf()`]({{< ref "/library/allos/os#os.sysconf" >}}) 获取 `'SC_IOV_MAX'` 值）。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

> Added in version 3.3.
>



### 查询终端的尺寸

> Added in version 3.3.
>

## os.**get_terminal_size**(*fd=STDOUT_FILENO*, */*)

​	返回终端窗口的尺寸，格式为 `(columns, lines)`，它是类型为 [`terminal_size`]({{< ref "/library/allos/os#os.terminal_size" >}}) 的元组。

​	可选参数 `fd` （默认为 `STDOUT_FILENO` 或标准输出）指定应查询的文件描述符。

​	如果文件描述符未连接到终端，则抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 异常。

[`shutil.get_terminal_size()`]({{< ref "/library/filesys/shutil#shutil.get_terminal_size" >}}) 是供常规使用的高阶函数，`os.get_terminal_size` 是其底层的实现。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows.

## *class* os.**terminal_size**

​	元组的子类，存储终端窗口尺寸 `(columns, lines)`。

## **columns**

​	终端窗口的宽度，单位为字符。

## **lines**

​	终端窗口的高度，单位为字符。



### 文件描述符的继承

> Added in version 3.4.
>

​	每个文件描述符都有一个 "inheritable"（可继承）标志位，该标志位控制了文件描述符是否可以由子进程继承。从 Python 3.4 开始，由 Python 创建的文件描述符默认是不可继承的。

​	在 UNIX 上，执行新程序时，不可继承的文件描述符在子进程中是关闭的，其他文件描述符将被继承。

​	在 Windows 上，不可继承的句柄和文件描述符在子进程中是关闭的，但标准流（文件描述符 0、1 和 2 即标准输入、标准输出和标准错误）是始终继承的。如果使用 [`spawn*`]({{< ref "/library/allos/os#os.spawnl" >}}) 函数，所有可继承的句柄和文件描述符都将被继承。如果使用 [`subprocess`]({{< ref "/library/concurrency/subprocess#module-subprocess" >}}) 模块，将关闭除标准流以外的所有文件描述符，并且仅当 *close_fds* 参数为 `False` 时才继承可继承的句柄。

​	在 WebAssembly 平台上，文件描述符无法被修改。

## os.**get_inheritable**(*fd*, */*)

​	获取指定文件描述符的“可继承”标志位（为布尔值）。

## os.**set_inheritable**(*fd*, *inheritable*, */*)

​	设置指定文件描述符的“可继承”标志位。

## os.**get_handle_inheritable**(*handle*, */*)

​	获取指定句柄的“可继承”标志位（为布尔值）。

[Availability]({{< ref "/library/intro#availability" >}}): Windows.

## os.**set_handle_inheritable**(*handle*, *inheritable*, */*)

​	设置指定句柄的“可继承”标志位。

[Availability]({{< ref "/library/intro#availability" >}}): Windows.



## 文件和目录

​	在某些 Unix 平台上，许多函数支持以下一项或多项功能：

- **指定文件描述符为参数：** 通常在 [`os`]({{< ref "/library/allos/os#module-os" >}}) 模块中提供给函数的 *path* 参数必须是表示文件路径的字符串，但是，某些函数现在可以接受其 *path* 参数为打开文件描述符，该函数将对描述符指向的文件进行操作。（对于 POSIX 系统，Python 将调用以 `f` 开头的函数变体（如调用 `fchdir` 而不是 `chdir`）。）

  可以用 [`os.supports_fd`]({{< ref "/library/allos/os#os.supports_fd" >}}) 检查某个函数在你的平台上是否支持将 *path* 参数指定为文件描述符。如果不支持，使用该功能将抛出 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}}) 异常。

  如果该函数还支持 *dir_fd* 或 *follow_symlinks* 参数，那么用文件描述符作为 *path* 后就不能再指定上述参数了。

- **基于目录描述符的相对路径：** 如果 *dir_fd* 不是 `None`，它就应该是一个指向目录的文件描述符，这时待操作的 path 应该是相对路径，相对路径是相对于前述目录的。如果 path 是绝对路径，则 *dir_fd* 将被忽略。（对于 POSIX 系统，Python 将调用该函数的变体，变体以 `at` 结尾，可能以 `f` 开头（如调用 `faccessat` 而不是 `access`）。

  可以用 [`os.supports_dir_fd`]({{< ref "/library/allos/os#os.supports_dir_fd" >}}) 检查某个函数在你的平台上是否支持 *dir_fd*。如果不支持，使用该功能将抛出 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}}) 异常。

- **不跟踪符号链接：** 如果 *follow_symlinks* 为 `False`，并且待操作路径的最后一个元素是符号链接，则该函数将在符号链接本身而不是链接所指向的文件上操作。（对于 POSIX 系统，Python 将调用该函数的 `l...` 变体。）

  可以用 [`os.supports_follow_symlinks`]({{< ref "/library/allos/os#os.supports_follow_symlinks" >}}) 检查某个函数在你的平台上是否支持 *follow_symlinks*。如果不支持，使用该功能将抛出 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}}) 异常。

## os.**access**(*path*, *mode*, ***, *dir_fd=None*, *effective_ids=False*, *follow_symlinks=True*)

​	使用 实际用户ID/用户组ID 测试对 *path* 的访问。请注意，大多数测试操作将使用 有效用户ID/用户组ID，因此可以在 suid/sgid 环境中运用此例程，来测试调用用户是否具有对 *path* 的指定访问权限。*mode* 为 [`F_OK`]({{< ref "/library/allos/os#os.F_OK" >}}) 时用于测试 *path* 是否存在，也可以对 [`R_OK`]({{< ref "/library/allos/os#os.R_OK" >}})、[`W_OK`]({{< ref "/library/allos/os#os.W_OK" >}}) 和 [`X_OK`]({{< ref "/library/allos/os#os.X_OK" >}}) 中的一个或多个进行“或”运算来测试指定权限。允许访问则返回 [`True`]({{< ref "/library/constants#True" >}})，否则返回 [`False`]({{< ref "/library/constants#False" >}})。更多信息请参见 Unix 手册页 *[access(2)](https://manpages.debian.org/access(2))*。

​	本函数支持指定 [基于目录描述符的相对路径]({{< ref "/library/allos/os#dir-fd" >}}) 和 [不跟踪符号链接]({{< ref "/library/allos/os#follow-symlinks" >}})。

​	如果 *effective_ids* 为 `True`，[`access()`]({{< ref "/library/allos/os#os.access" >}}) 将使用 有效用户ID/用户组ID 而非 实际用户ID/用户组ID 进行访问检查。您的平台可能不支持 *effective_ids*，您可以使用 [`os.supports_effective_ids`]({{< ref "/library/allos/os#os.supports_effective_ids" >}}) 检查它是否可用。如果不可用，使用它时会抛出 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}}) 异常。

​	备注

 

​	使用 [`access()`]({{< ref "/library/allos/os#os.access" >}}) 来检查用户是否具有某项权限（如打开文件的权限），然后再使用 [`open()`]({{< ref "/library/functions#open" >}}) 打开文件，这样做存在一个安全漏洞，因为用户可能会在检查和打开文件之间的时间里做其他操作。推荐使用 [EAFP]({{< ref "/glossary/idx#term-EAFP" >}}) 技术。如:

```
if os.access("myfile", os.R_OK):
    with open("myfile") as fp:
        return fp.read()
return "some default data"
```

​	最好写成:

```
try:
    fp = open("myfile")
except PermissionError:
    return "some default data"
else:
    with fp:
        return fp.read()
```

​	备注

 

​	即使 [`access()`]({{< ref "/library/allos/os#os.access" >}}) 指示 I/O 操作会成功，但实际操作仍可能失败，尤其是对网络文件系统的操作，其权限语义可能超出常规的 POSIX 权限位模型。

*在 3.3 版本发生变更:* 添加 *dir_fd*、*effective_ids* 和 *follow_symlinks* 参数。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## os.**F_OK**

## os.**R_OK**

## os.**W_OK**

## os.**X_OK**

​	作为 [`access()`]({{< ref "/library/allos/os#os.access" >}}) 的 *mode* 参数的可选值，分别测试 *path* 的存在性、可读性、可写性和可执行性。

## os.**chdir**(*path*)

​	将当前工作目录更改为 *path*。

​	本函数支持 [指定文件描述符为参数]({{< ref "/library/allos/os#path-fd" >}})。其中，描述符必须指向打开的目录，不能是打开的文件。

​	本函数可以抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 及其子类的异常，如 [`FileNotFoundError`]({{< ref "/library/exceptions#FileNotFoundError" >}})、[`PermissionError`]({{< ref "/library/exceptions#PermissionError" >}}) 和 [`NotADirectoryError`]({{< ref "/library/exceptions#NotADirectoryError" >}}) 异常。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.chdir` 并附带参数 `path`。

*在 3.3 版本发生变更:* 在某些平台上新增支持将 *path* 参数指定为文件描述符。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## os.**chflags**(*path*, *flags*, ***, *follow_symlinks=True*)

​	将 *path* 的 flags 设置为其他由数字表示的 *flags*。*flags* 可以用以下值按位或组合起来（以下值在 [`stat`]({{< ref "/library/filesys/stat#module-stat" >}}) 模块中定义）：

- [`stat.UF_NODUMP`]({{< ref "/library/filesys/stat#stat.UF_NODUMP" >}})
- [`stat.UF_IMMUTABLE`]({{< ref "/library/filesys/stat#stat.UF_IMMUTABLE" >}})
- [`stat.UF_APPEND`]({{< ref "/library/filesys/stat#stat.UF_APPEND" >}})
- [`stat.UF_OPAQUE`]({{< ref "/library/filesys/stat#stat.UF_OPAQUE" >}})
- [`stat.UF_NOUNLINK`]({{< ref "/library/filesys/stat#stat.UF_NOUNLINK" >}})
- [`stat.UF_COMPRESSED`]({{< ref "/library/filesys/stat#stat.UF_COMPRESSED" >}})
- [`stat.UF_HIDDEN`]({{< ref "/library/filesys/stat#stat.UF_HIDDEN" >}})
- [`stat.SF_ARCHIVED`]({{< ref "/library/filesys/stat#stat.SF_ARCHIVED" >}})
- [`stat.SF_IMMUTABLE`]({{< ref "/library/filesys/stat#stat.SF_IMMUTABLE" >}})
- [`stat.SF_APPEND`]({{< ref "/library/filesys/stat#stat.SF_APPEND" >}})
- [`stat.SF_NOUNLINK`]({{< ref "/library/filesys/stat#stat.SF_NOUNLINK" >}})
- [`stat.SF_SNAPSHOT`]({{< ref "/library/filesys/stat#stat.SF_SNAPSHOT" >}})

​	本函数支持 [不跟踪符号链接]({{< ref "/library/allos/os#follow-symlinks" >}})。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.chflags` 并附带参数 `path`, `flags`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

*在 3.3 版本发生变更:* 增加了 *follow_symlinks* 形参。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## os.**chmod**(*path*, *mode*, ***, *dir_fd=None*, *follow_symlinks=True*)

​	将 *path* 的 mode 更改为其他由数字表示的 *mode*。*mode* 可以用以下值之一，也可以将它们按位或组合起来（以下值在 [`stat`]({{< ref "/library/filesys/stat#module-stat" >}}) 模块中定义）：

- [`stat.S_ISUID`]({{< ref "/library/filesys/stat#stat.S_ISUID" >}})
- [`stat.S_ISGID`]({{< ref "/library/filesys/stat#stat.S_ISGID" >}})
- [`stat.S_ENFMT`]({{< ref "/library/filesys/stat#stat.S_ENFMT" >}})
- [`stat.S_ISVTX`]({{< ref "/library/filesys/stat#stat.S_ISVTX" >}})
- [`stat.S_IREAD`]({{< ref "/library/filesys/stat#stat.S_IREAD" >}})
- [`stat.S_IWRITE`]({{< ref "/library/filesys/stat#stat.S_IWRITE" >}})
- [`stat.S_IEXEC`]({{< ref "/library/filesys/stat#stat.S_IEXEC" >}})
- [`stat.S_IRWXU`]({{< ref "/library/filesys/stat#stat.S_IRWXU" >}})
- [`stat.S_IRUSR`]({{< ref "/library/filesys/stat#stat.S_IRUSR" >}})
- [`stat.S_IWUSR`]({{< ref "/library/filesys/stat#stat.S_IWUSR" >}})
- [`stat.S_IXUSR`]({{< ref "/library/filesys/stat#stat.S_IXUSR" >}})
- [`stat.S_IRWXG`]({{< ref "/library/filesys/stat#stat.S_IRWXG" >}})
- [`stat.S_IRGRP`]({{< ref "/library/filesys/stat#stat.S_IRGRP" >}})
- [`stat.S_IWGRP`]({{< ref "/library/filesys/stat#stat.S_IWGRP" >}})
- [`stat.S_IXGRP`]({{< ref "/library/filesys/stat#stat.S_IXGRP" >}})
- [`stat.S_IRWXO`]({{< ref "/library/filesys/stat#stat.S_IRWXO" >}})
- [`stat.S_IROTH`]({{< ref "/library/filesys/stat#stat.S_IROTH" >}})
- [`stat.S_IWOTH`]({{< ref "/library/filesys/stat#stat.S_IWOTH" >}})
- [`stat.S_IXOTH`]({{< ref "/library/filesys/stat#stat.S_IXOTH" >}})

​	本函数支持 [指定文件描述符]({{< ref "/library/allos/os#path-fd" >}})、[指定基于目录描述符的相对路径]({{< ref "/library/allos/os#dir-fd" >}}) 和 [不跟踪符号链接]({{< ref "/library/allos/os#follow-symlinks" >}})。

​	备注

 

​	尽管 Windows 支持 [`chmod()`]({{< ref "/library/allos/os#os.chmod" >}})，但你只能用它设置文件的只读旗标（通过 `stat.S_IWRITE` 和 `stat.S_IREAD` 常量或对应的整数值）。 所有其他旗标位都会被忽略。 在 Windows 上 *follow_symlinks* 的默认值为 `False`。

​	此函数在 WASI 是受限的，请参阅 [WebAssembly 平台]({{< ref "/library/intro#wasm-availability" >}}) 了解详情。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.chmod` 并附带参数 `path`, `mode`, `dir_fd`。

*在 3.3 版本发生变更:* 添加了指定 *path* 为文件描述符的支持，以及 *dir_fd* 和 *follow_symlinks* 参数。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

*在 3.13 版本发生变更:* 在 Windows 上增加了对文件描述符和 *follow_symlinks* 参数的支持。

## os.**chown**(*path*, *uid*, *gid*, ***, *dir_fd=None*, *follow_symlinks=True*)

​	将 *path* 的用户和组 ID 分别修改为数字形式的 *uid* 和 *gid*。若要使其中某个 ID 保持不变，请将其置为 -1。

​	本函数支持 [指定文件描述符]({{< ref "/library/allos/os#path-fd" >}})、[指定基于目录描述符的相对路径]({{< ref "/library/allos/os#dir-fd" >}}) 和 [不跟踪符号链接]({{< ref "/library/allos/os#follow-symlinks" >}})。

​	参见更高阶的函数 [`shutil.chown()`]({{< ref "/library/filesys/shutil#shutil.chown" >}})，除了数字 ID 之外，它也接受名称。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.chown` 并附带参数 `path`, `uid`, `gid`, `dir_fd`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

​	此函数在 WASI 是受限的，请参阅 [WebAssembly 平台]({{< ref "/library/intro#wasm-availability" >}}) 了解详情。

*在 3.3 版本发生变更:* 添加了指定 *path* 为文件描述符的支持，以及 *dir_fd* 和 *follow_symlinks* 参数。

*在 3.6 版本发生变更:* 支持 [类路径对象]({{< ref "/glossary/idx#term-path-like-object" >}})。

## os.**chroot**(*path*)

​	将当前进程的根目录更改为 *path*。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android.

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## os.**fchdir**(*fd*)

​	将当前工作目录更改为文件描述符 *fd* 指向的目录。fd 必须指向打开的目录而非文件。从 Python 3.3 开始，它等效于 `os.chdir(fd)`。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.chdir` 并附带参数 `path`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

## os.**getcwd**()

​	返回表示当前工作目录的字符串。

## os.**getcwdb**()

​	返回表示当前工作目录的字节串 (bytestring)。

*在 3.8 版本发生变更:* 在 Windows 上，本函数现在会使用 UTF-8 编码格式而不是 ANSI 代码页：请参看 [**PEP 529**](https://peps.python.org/pep-0529/) 了解具体原因。 该函数在 Windows 上不再被弃用。

## os.**lchflags**(*path*, *flags*)

​	将 *path* 的 flags 设置为其他由数字表示的 *flags*，与 [`chflags()`]({{< ref "/library/allos/os#os.chflags" >}}) 类似，但不跟踪符号链接。从 Python 3.3 开始，它等效于 `os.chflags(path, flags, follow_symlinks=False)`。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.chflags` 并附带参数 `path`, `flags`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## os.**lchmod**(*path*, *mode*)

​	将 *path* 的权限状态修改为 *mode*。如果 path 是符号链接，则影响符号链接本身而非链接目标。可以参考 [`chmod()`]({{< ref "/library/allos/os#os.chmod" >}}) 中列出 *mode* 的可用值。从 Python 3.3 开始，它等效于 `os.chmod(path, mode, follow_symlinks=False)`。

`lchmod()` 不是 POSIX 的一部分，但 Unix 实现如果支持修改符号链接的模式则可能包含它。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.chmod` 并附带参数 `path`, `mode`, `dir_fd`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows, not Linux, FreeBSD >= 1.3, NetBSD >= 1.3, not OpenBSD

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

*在 3.13 版本发生变更:* 增加 Windows 上的支持。

## os.**lchown**(*path*, *uid*, *gid*)

​	将 *path* 的用户和组 ID 分别修改为数字形式的 *uid* 和 *gid*，本函数不跟踪符号链接。从 Python 3.3 开始，它等效于 `os.chown(path, uid, gid, follow_symlinks=False)`。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.chown` 并附带参数 `path`, `uid`, `gid`, `dir_fd`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## os.**link**(*src*, *dst*, ***, *src_dir_fd=None*, *dst_dir_fd=None*, *follow_symlinks=True*)

​	创建一个指向 *src* 的硬链接，名为 *dst*。

​	本函数支持将 *src_dir_fd* 和 *dst_dir_fd* 中的一个或两个指定为 [基于目录描述符的相对路径]({{< ref "/library/allos/os#dir-fd" >}})，支持 [不跟踪符号链接]({{< ref "/library/allos/os#follow-symlinks" >}})。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.link` 并附带参数 `src`、`dst`、`src_dir_fd`、`dst_dir_fd`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows.

*在 3.2 版本发生变更:* 添加了对 Windows 的支持。

*在 3.3 版本发生变更:* 增加了 *src_dir_fd*, *dst_dir_fd* 和 *follow_symlinks* 形参。

*在 3.6 版本发生变更:* 接受一个 [类路径对象]({{< ref "/glossary/idx#term-path-like-object" >}}) 作为 *src* 和 *dst*。

## os.**listdir**(*path='.'*)

​	返回一个包含由 *path* 指定目录中条目名称组成的列表。 该列表按任意顺序排列，并且不包括特殊条目 `'.'` 和 `'..'`，即使它们存在于目录中。 如果有文件在调用此函数期间在被移除或添加到目录中，是否要包括该文件的名称并没有规定。

*path* 可以是 [类路径对象]({{< ref "/glossary/idx#term-path-like-object" >}})。如果 *path* 是（直接传入或通过 [`PathLike`]({{< ref "/library/allos/os#os.PathLike" >}}) 接口间接传入） `bytes` 类型，则返回的文件名也将是 `bytes` 类型，其他情况下是 `str` 类型。

​	本函数也支持 [指定文件描述符为参数]({{< ref "/library/allos/os#path-fd" >}})，其中描述符必须指向目录。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.listdir` 并附带参数 `path`。

​	备注

 

​	要将 `str` 类型的文件名编码为 `bytes`，请使用 [`fsencode()`]({{< ref "/library/allos/os#os.fsencode" >}})。

​	参见

 

[`scandir()`]({{< ref "/library/allos/os#os.scandir" >}}) 函数返回目录内文件名的同时，也返回文件属性信息，它在某些具体情况下能提供更好的性能。

*在 3.2 版本发生变更:* *path* 变为可选参数。

*在 3.3 版本发生变更:* 新增支持将 *path* 参数指定为打开的文件描述符。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## os.**listdrives**()

​	返回一个包括Windows系统上驱动名称的列表。

​	驱动器名称通常的形式如 `'C:\\'`。 并非每个驱动器名都会关联到特定的卷，有些驱动器名可能出于各种原因而无法访问，包括权限、网络连接或介质丢失等。 本函数不会测试可访问性。

​	如果在收集驱动器名时发生错误则可能引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}})。

​	引发一个不带参数的 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.listdrives`。

[Availability]({{< ref "/library/intro#availability" >}}): Windows

> Added in version 3.12.
>

## os.**listmounts**(*volume*)

​	返回一个包含 Windows 系统上指向卷的加载点的列表。

*volume* 必须表示为 GUID 路径，如 [`os.listvolumes()`]({{< ref "/library/allos/os#os.listvolumes" >}}) 所返回的值。 卷可能被挂载到多个位置也可能根本未挂载。 在后一种情况下，该列表将为空。 此函数不会返回没有关联到卷的挂载点。

​	此函数返回的挂载点将为绝对路径，并可能比驱动器名称更长。

​	如果卷未被识别或者如果在获取路径时发生错误则会引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}})。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.listmounts` 并附带参数 `volume`。

[Availability]({{< ref "/library/intro#availability" >}}): Windows

> Added in version 3.12.
>

## os.**listvolumes**()

​	返回一个包含系统中的卷的列表。

​	卷通常被表示为一个 GUID 路径如 `\\?\Volume{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}\`。 文件通常可通过 GUID 路径来访问，如果权限允许的话。 但是，用户往往并不熟悉这种路径，所以此函数的推荐用法是使用 [`os.listmounts()`]({{< ref "/library/allos/os#os.listmounts" >}}) 来获取加载点。

​	如果在收集卷时发生错误则可能引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}})。

​	引发一个不带参数的 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.listvolumes`。

[Availability]({{< ref "/library/intro#availability" >}}): Windows

> Added in version 3.12.
>

## os.**lstat**(*path*, ***, *dir_fd=None*)

​	在给定的路径上执行 `lstat()` 系统调用的等价物。 类似于 [`stat()`]({{< ref "/library/allos/os#os.stat" >}})，但不会跟随符号链接。 返回一个 [`stat_result`]({{< ref "/library/allos/os#os.stat_result" >}}) 对象。

​	在不支持符号链接的平台上，本函数是 [`stat()`]({{< ref "/library/allos/os#os.stat" >}}) 的别名。

​	从 Python 3.3 起，此功能等价于 `os.stat(path, dir_fd=dir_fd, follow_symlinks=False)`。

​	本函数支持 [基于目录描述符的相对路径]({{< ref "/library/allos/os#dir-fd" >}})。

​	参见

 

[`stat()`]({{< ref "/library/allos/os#os.stat" >}}) 函数。

*在 3.2 版本发生变更:* 添加对 Windows 6.0 (Vista) 符号链接的支持。

*在 3.3 版本发生变更:* 添加了 *dir_fd* 参数。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

*在 3.8 版本发生变更:* 目前在 Windows 上，遇到表示另一个路径的重解析点（即名称代理，包括符号链接和目录结点），本函数将打开它。其他种类的重解析点由 [`stat()`]({{< ref "/library/allos/os#os.stat" >}}) 交由操作系统解析。

## os.**mkdir**(*path*, *mode=0o777*, ***, *dir_fd=None*)

​	创建一个名为 *path* 的目录，应用以数字表示的权限模式 *mode*。

​	如果目录已经存在， [`FileExistsError`]({{< ref "/library/exceptions#FileExistsError" >}}) 会被提出。如果路径中的父目录不存在，则会引发 [`FileNotFoundError`]({{< ref "/library/exceptions#FileNotFoundError" >}}) 。

​	某些系统会忽略 *mode*。如果没有忽略它，那么将首先从它中减去当前的 umask 值。如果除最后 9 位（即 *mode* 八进制的最后 3 位）之外，还设置了其他位，则其他位的含义取决于各个平台。在某些平台上，它们会被忽略，应显式调用 [`chmod()`]({{< ref "/library/allos/os#os.chmod" >}}) 进行设置。

​	在 Windows 系统中，*mode* 值 `0o700` 被专门用于对新目录实施访问控制，使其只有当前用户和管理员才能访问。 其他值的 *mode* 将被忽略。

​	本函数支持 [基于目录描述符的相对路径]({{< ref "/library/allos/os#dir-fd" >}})。

​	如果需要创建临时目录，请参阅 [`tempfile`]({{< ref "/library/filesys/tempfile#module-tempfile" >}}) 模块中的 [`tempfile.mkdtemp()`]({{< ref "/library/filesys/tempfile#tempfile.mkdtemp" >}}) 函数。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.mkdir` 并附带参数 `path`, `mode`, `dir_fd`。

*在 3.3 版本发生变更:* 添加了 *dir_fd* 参数。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

*在 3.13 版本发生变更:* Windows系统现在使用*mode*值``0o700``

## os.**makedirs**(*name*, *mode=0o777*, *exist_ok=False*)

​	递归目录创建函数。与 [`mkdir()`]({{< ref "/library/allos/os#os.mkdir" >}}) 类似，但会自动创建到达最后一级目录所需要的中间目录。

*mode* 形参会被传递给 [`mkdir()`]({{< ref "/library/allos/os#os.mkdir" >}}) 用来创建最后一级目录；请参阅 [mkdir() 的说明]({{< ref "/library/allos/os#mkdir-modebits" >}}) 了解其解读方式。 要设置任何新建父目录的权限你可以在发起调用 [`makedirs()`]({{< ref "/library/allos/os#os.makedirs" >}}) 之前设置掩码。 现有父目录的文件权限不会被更改。

​	如果 *exist_ok* 为 `False` (默认值)，则如果目标目录已存在将会引发 [`FileExistsError`]({{< ref "/library/exceptions#FileExistsError" >}})。

​	备注

 

​	如果要创建的路径元素包含 [`pardir`]({{< ref "/library/allos/os#os.pardir" >}}) (如 UNIX 系统中的 "..") [`makedirs()`]({{< ref "/library/allos/os#os.makedirs" >}}) 将无法明确目标。

​	本函数能正确处理 UNC 路径。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.mkdir` 并附带参数 `path`, `mode`, `dir_fd`。

*在 3.2 版本发生变更:* 增加了 *exist_ok* 形参。

*在 3.4.1 版本发生变更:* 在 Python 3.4.1 以前，如果 *exist_ok* 为 `True`，且目录已存在，且 *mode* 与现有目录的权限不匹配，[`makedirs()`]({{< ref "/library/allos/os#os.makedirs" >}}) 仍会抛出错误。由于无法安全地实现此行为，因此在 Python 3.4.1 中将该行为删除。请参阅 [bpo-21082](https://bugs.python.org/issue?@action=redirect&bpo=21082)。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

*在 3.7 版本发生变更:* *mode* 参数不会再影响新创建的中间目录的文件权限位。

## os.**mkfifo**(*path*, *mode=0o666*, ***, *dir_fd=None*)

​	创建一个名为 *path* 的 FIFO（命名管道，一种先进先出队列），具有以数字表示的权限状态 *mode*。将从 mode 中首先减去当前的 umask 值。

​	本函数支持 [基于目录描述符的相对路径]({{< ref "/library/allos/os#dir-fd" >}})。

​	FIFO 是可以像常规文件一样访问的管道。FIFO 如果没有被删除（如使用 [`os.unlink()`]({{< ref "/library/allos/os#os.unlink" >}})），会一直存在。通常，FIFO 用作“客户端”和“服务器”进程之间的汇合点：服务器打开 FIFO 进行读取，而客户端打开 FIFO 进行写入。请注意，[`mkfifo()`]({{< ref "/library/allos/os#os.mkfifo" >}}) 不会打开 FIFO --- 它只是创建汇合点。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

*在 3.3 版本发生变更:* 添加了 *dir_fd* 参数。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## os.**mknod**(*path*, *mode=0o600*, *device=0*, ***, *dir_fd=None*)

​	创建一个名为 *path* 的文件系统节点（文件，设备专用文件或命名管道）。*mode* 指定权限和节点类型，方法是将权限与下列节点类型 `stat.S_IFREG`、`stat.S_IFCHR`、`stat.S_IFBLK` 和 `stat.S_IFIFO` 之一（按位或）组合（这些常量可以在 [`stat`]({{< ref "/library/filesys/stat#module-stat" >}}) 模块中找到）。对于 `stat.S_IFCHR` 和 `stat.S_IFBLK`，*device* 参数指定了新创建的设备专用文件（可能会用到 [`os.makedev()`]({{< ref "/library/allos/os#os.makedev" >}})），否则该参数将被忽略。

​	本函数支持 [基于目录描述符的相对路径]({{< ref "/library/allos/os#dir-fd" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

*在 3.3 版本发生变更:* 添加了 *dir_fd* 参数。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## os.**major**(*device*, */*)

​	根据原始设备编号提取设备主编号（通常为来自 `stat` 的 `st_dev` 或 `st_rdev` 字段）。

## os.**minor**(*device*, */*)

​	根据原始设备编号提取设备次编号（通常为来自 `stat` 的 `st_dev` 或 `st_rdev` 字段）。

## os.**makedev**(*major*, *minor*, */*)

​	将主设备号和次设备号组合成原始设备号。

## os.**pathconf**(*path*, *name*)

​	返回所给名称的文件有关的系统配置信息。*name* 指定要查找的配置名称，它可以是字符串，是一个系统已定义的名称，这些名称定义在不同标准（POSIX.1，Unix 95，Unix 98 等）中。一些平台还定义了额外的其他名称。当前操作系统已定义的名称在 `pathconf_names` 字典中给出。对于未包含在该映射中的配置名称，也可以传递一个整数作为 *name*。

​	如果 *name* 是一个字符串且不是已定义的名称，将抛出 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 异常。如果当前系统不支持 *name* 指定的配置名称，即使该名称存在于 `pathconf_names`，也会抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 异常，错误码为 [`errno.EINVAL`]({{< ref "/library/allos/errno#errno.EINVAL" >}})。

​	本函数支持 [指定文件描述符为参数]({{< ref "/library/allos/os#path-fd" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## os.**pathconf_names**

​	字典，表示映射关系，为 [`pathconf()`]({{< ref "/library/allos/os#os.pathconf" >}}) 和 [`fpathconf()`]({{< ref "/library/allos/os#os.fpathconf" >}}) 可接受名称与操作系统为这些名称定义的整数值之间的映射。这可用于判断系统已定义了哪些名称。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

## os.**readlink**(*path*, ***, *dir_fd=None*)

​	返回一个字符串，为符号链接指向的实际路径。其结果可以是绝对或相对路径。如果是相对路径，则可用 `os.path.join(os.path.dirname(path), result)` 转换为绝对路径。

​	如果 *path* 是字符串对象（直接传入或通过 [`PathLike`]({{< ref "/library/allos/os#os.PathLike" >}}) 接口间接传入），则结果也将是字符串对象，且此类调用可能会引发 UnicodeDecodeError。如果 *path* 是字节对象（直接传入或间接传入），则结果将会是字节对象。

​	本函数支持 [基于目录描述符的相对路径]({{< ref "/library/allos/os#dir-fd" >}})。

​	当尝试解析的路径可能含有链接时，请改用 [`realpath()`]({{< ref "/library/filesys/os_path#os.path.realpath" >}}) 以正确处理递归和平台差异。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows.

*在 3.2 版本发生变更:* 添加对 Windows 6.0 (Vista) 符号链接的支持。

*在 3.3 版本发生变更:* 添加了 *dir_fd* 参数。

*在 3.6 版本发生变更:* 在 Unix 上可以接受一个 [类路径对象]({{< ref "/glossary/idx#term-path-like-object" >}})。

*在 3.8 版本发生变更:* 在 Windows 上接受 [类路径对象]({{< ref "/glossary/idx#term-path-like-object" >}}) 和字节对象。

​	增加了对目录链接的支持，且返回值改为了“替换路径”的形式（通常带有 `\\?\` 前缀），而不是先前那样返回可选的 "print name" 字段。

## os.**remove**(*path*, ***, *dir_fd=None*)

​	移除（删除）文件 *path*。 如果 *path* 是目录，则会引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}})。 请使用 [`rmdir()`]({{< ref "/library/allos/os#os.rmdir" >}}) 来移除目录。 如果文件不存在，则会引发 [`FileNotFoundError`]({{< ref "/library/exceptions#FileNotFoundError" >}})。

​	本函数支持 [基于目录描述符的相对路径]({{< ref "/library/allos/os#dir-fd" >}})。

​	在 Windows 上，尝试删除正在使用的文件会抛出异常。而在 Unix 上，虽然该文件的条目会被删除，但分配给文件的存储空间仍然不可用，直到原始文件不再使用为止。

​	本函数在语义上与 [`unlink()`]({{< ref "/library/allos/os#os.unlink" >}}) 相同。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.remove` 并附带参数 `path`, `dir_fd`。

*在 3.3 版本发生变更:* 添加了 *dir_fd* 参数。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## os.**removedirs**(*name*)

​	递归删除目录。工作方式类似于 [`rmdir()`]({{< ref "/library/allos/os#os.rmdir" >}})，不同之处在于，如果成功删除了末尾一级目录，[`removedirs()`]({{< ref "/library/allos/os#os.removedirs" >}}) 会尝试依次删除 *path* 中提到的每个父目录，直到抛出错误为止（但该错误会被忽略，因为这通常表示父目录不是空目录）。例如，`os.removedirs('foo/bar/baz')` 将首先删除目录 `'foo/bar/baz'`，然后如果 `'foo/bar'` 和 `'foo'` 为空，则继续删除它们。如果无法成功删除末尾一级目录，则抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 异常。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.remove` 并附带参数 `path`, `dir_fd`。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## os.**rename**(*src*, *dst*, ***, *src_dir_fd=None*, *dst_dir_fd=None*)

​	将文件或目录 *src* 重命名为 *dst*。如果 *dst* 已存在，则下列情况下将会操作失败，并抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 的子类：

​	在 Windows 上，如果 *dst* 存在则总是会引发 [`FileExistsError`]({{< ref "/library/exceptions#FileExistsError" >}})。 如果 *src* 和 *dst* 是在不同的文件系统上则此操作可能会失败。 请使用 [`shutil.move()`]({{< ref "/library/filesys/shutil#shutil.move" >}}) 以支持移动到不同的文件系统。

​	在 Unix 上，如果 *src* 是文件而 *dst* 是目录，将抛出 [`IsADirectoryError`]({{< ref "/library/exceptions#IsADirectoryError" >}}) 异常，反之则抛出 [`NotADirectoryError`]({{< ref "/library/exceptions#NotADirectoryError" >}}) 异常。如果两者都是目录且 *dst* 为空，则 *dst* 将被静默替换。如果 *dst* 是非空目录，则抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 异常。如果两者都是文件，则在用户具有权限的情况下，将对 *dst* 进行静默替换。如果 *src* 和 *dst* 在不同的文件系统上，则本操作在某些 Unix 分支上可能会失败。如果成功，重命名操作将是一个原子操作（这是 POSIX 的要求）。

​	本函数支持将 *src_dir_fd* 和 *dst_dir_fd* 中的一个或两个指定为 [基于目录描述符的相对路径]({{< ref "/library/allos/os#dir-fd" >}})。

​	如果需要在不同平台上都能替换目标，请使用 [`replace()`]({{< ref "/library/allos/os#os.replace" >}})。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.rename` 并附带参数 `src`, `dst`, `src_dir_fd`, `dst_dir_fd`。

*在 3.3 版本发生变更:* 增加了 *src_dir_fd* 和 *dst_dir_fd* 形参。

*在 3.6 版本发生变更:* 接受一个 [类路径对象]({{< ref "/glossary/idx#term-path-like-object" >}}) 作为 *src* 和 *dst*。

## os.**renames**(*old*, *new*)

​	递归重命名目录或文件。工作方式类似 [`rename()`]({{< ref "/library/allos/os#os.rename" >}})，除了会首先创建新路径所需的中间目录。重命名后，将调用 [`removedirs()`]({{< ref "/library/allos/os#os.removedirs" >}}) 删除旧路径中不需要的目录。

​	备注

 

​	如果用户没有权限删除末级的目录或文件，则本函数可能会无法建立新的目录结构。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.rename` 并附带参数 `src`, `dst`, `src_dir_fd`, `dst_dir_fd`。

*在 3.6 版本发生变更:* 接受一个 [类路径对象]({{< ref "/glossary/idx#term-path-like-object" >}}) 作为 *old* 和 *new*。

## os.**replace**(*src*, *dst*, ***, *src_dir_fd=None*, *dst_dir_fd=None*)

​	将文件或目录 *src* 重命名为 *dst*。如果 *dst* 是非空目录，将抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 异常。如果 *dst* 已存在且为文件，则在用户具有权限的情况下，将对其进行静默替换。如果 *src* 和 *dst* 在不同的文件系统上，本操作可能会失败。如果成功，重命名操作将是一个原子操作（这是 POSIX 的要求）。

​	本函数支持将 *src_dir_fd* 和 *dst_dir_fd* 中的一个或两个指定为 [基于目录描述符的相对路径]({{< ref "/library/allos/os#dir-fd" >}})。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.rename` 并附带参数 `src`, `dst`, `src_dir_fd`, `dst_dir_fd`。

> Added in version 3.3.
>

*在 3.6 版本发生变更:* 接受一个 [类路径对象]({{< ref "/glossary/idx#term-path-like-object" >}}) 作为 *src* 和 *dst*。

## os.**rmdir**(*path*, ***, *dir_fd=None*)

​	移除（删除）目录 *path*。 如果目录不存在或不为空，则会分别引发 [`FileNotFoundError`]({{< ref "/library/exceptions#FileNotFoundError" >}}) 或 [`OSError`]({{< ref "/library/exceptions#OSError" >}})。 要移除整个目录树，可以使用 [`shutil.rmtree()`]({{< ref "/library/filesys/shutil#shutil.rmtree" >}})。

​	本函数支持 [基于目录描述符的相对路径]({{< ref "/library/allos/os#dir-fd" >}})。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.rmdir` 并附带参数 `path`, `dir_fd`。

*在 3.3 版本发生变更:* 添加了 *dir_fd* 参数。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## os.**scandir**(*path='.'*)

​	返回一个 [`os.DirEntry`]({{< ref "/library/allos/os#os.DirEntry" >}}) 对象的迭代器，它们对应于由 *path* 指定目录中的条目。 这些条目会以任意顺序生成，并且不包括特殊条目 `'.'` 和 `'..'`。 如果有文件在迭代器创建之后在目录中被移除或添加，是否要包括该文件对应的条目并没有规定。

​	如果需要文件类型或文件属性信息，使用 [`scandir()`]({{< ref "/library/allos/os#os.scandir" >}}) 代替 [`listdir()`]({{< ref "/library/allos/os#os.listdir" >}}) 可以大大提高这部分代码的性能，因为如果操作系统在扫描目录时返回的是 [`os.DirEntry`]({{< ref "/library/allos/os#os.DirEntry" >}}) 对象，则该对象包含了这些信息。所有 [`os.DirEntry`]({{< ref "/library/allos/os#os.DirEntry" >}}) 的方法都可能执行一次系统调用，但是 [`is_dir()`]({{< ref "/library/allos/os#os.DirEntry.is_dir" >}}) 和 [`is_file()`]({{< ref "/library/allos/os#os.DirEntry.is_file" >}}) 通常只在有符号链接时才执行一次系统调用。[`os.DirEntry.stat()`]({{< ref "/library/allos/os#os.DirEntry.stat" >}}) 在 Unix 上始终需要一次系统调用，而在 Windows 上只在有符号链接时才需要。

*path* 可以是 [类路径对象]({{< ref "/glossary/idx#term-path-like-object" >}})。如果 *path* 是（直接传入或通过 [`PathLike`]({{< ref "/library/allos/os#os.PathLike" >}}) 接口间接传入的） `bytes` 类型，那么每个 [`os.DirEntry`]({{< ref "/library/allos/os#os.DirEntry" >}}) 的 [`name`]({{< ref "/library/allos/os#os.DirEntry.name" >}}) 和 [`path`]({{< ref "/library/allos/os#os.DirEntry.path" >}}) 属性将是 `bytes` 类型，其他情况下是 `str` 类型。

​	本函数也支持 [指定文件描述符为参数]({{< ref "/library/allos/os#path-fd" >}})，其中描述符必须指向目录。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.scandir` 并附带参数 `path`。

[`scandir()`]({{< ref "/library/allos/os#os.scandir" >}}) 迭代器支持 [上下文管理]({{< ref "/glossary/idx#term-context-manager" >}}) 协议，并具有以下方法：

## scandir.**close**()

​	关闭迭代器并释放占用的资源。

​	当迭代器迭代完毕，或垃圾回收，或迭代过程出错时，将自动调用本方法。但仍建议显式调用它或使用 [`with`]({{< ref "/reference/compound_stmts#with" >}}) 语句。

> Added in version 3.6.
>

​	下面的例子演示了 [`scandir()`]({{< ref "/library/allos/os#os.scandir" >}}) 的简单用法，用来显示给定 *path* 中所有不以 `'.'` 开头的文件（不包括目录）。`entry.is_file()` 通常不会增加一次额外的系统调用:

```
with os.scandir(path) as it:
    for entry in it:
        if not entry.name.startswith('.') and entry.is_file():
            print(entry.name)
```

​	备注

 

​	在基于 Unix 的系统上，[`scandir()`]({{< ref "/library/allos/os#os.scandir" >}}) 使用系统的 [opendir()](https://pubs.opengroup.org/onlinepubs/009695399/functions/opendir.html) 和 [readdir()](https://pubs.opengroup.org/onlinepubs/009695399/functions/readdir_r.html) 函数。 在 Windows 上，它使用 Win32 [FindFirstFileW](https://msdn.microsoft.com/en-us/library/windows/desktop/aa364418(v=vs.85).aspx) 和 [FindNextFileW](https://msdn.microsoft.com/en-us/library/windows/desktop/aa364428(v=vs.85).aspx) 函数。

> Added in version 3.5.
>

*在 3.6 版本发生变更:* 增加了对 [context manager]({{< ref "/glossary/idx#term-context-manager" >}}) 协议和 [`close()`]({{< ref "/library/allos/os#os.scandir.close" >}}) 方法的支持。 如果 [`scandir()`]({{< ref "/library/allos/os#os.scandir" >}}) 迭代器没有耗尽也没有被显式地关闭则将在其析构器中发出 [`ResourceWarning`]({{< ref "/library/exceptions#ResourceWarning" >}})。

​	本函数接受一个 [类路径对象]({{< ref "/glossary/idx#term-path-like-object" >}})。

*在 3.7 版本发生变更:* 在 Unix 上新增支持 [指定文件描述符为参数]({{< ref "/library/allos/os#path-fd" >}})。

## *class* os.**DirEntry**

​	由 [`scandir()`]({{< ref "/library/allos/os#os.scandir" >}}) 生成的对象，用于显示目录内某个条目的文件路径和其他文件属性。

[`scandir()`]({{< ref "/library/allos/os#os.scandir" >}}) 将在不进行额外系统调用的情况下，提供尽可能多的此类信息。每次进行 `stat()` 或 `lstat()` 系统调用时，`os.DirEntry` 对象会将结果缓存下来。

`os.DirEntry` 实例不适合存储在长期存在的数据结构中，如果你知道文件元数据已更改，或者自调用 [`scandir()`]({{< ref "/library/allos/os#os.scandir" >}}) 以来已经经过了很长时间，请调用 `os.stat(entry.path)` 来获取最新信息。

​	因为 `os.DirEntry` 方法可以进行系统调用，所以它也可能抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 异常。如需精确定位错误，可以逐个调用 `os.DirEntry` 中的方法来捕获 [`OSError`]({{< ref "/library/exceptions#OSError" >}})，并适当处理。

​	为了能直接用作 [类路径对象]({{< ref "/glossary/idx#term-path-like-object" >}})，`os.DirEntry` 实现了 [`PathLike`]({{< ref "/library/allos/os#os.PathLike" >}}) 接口。

`os.DirEntry` 实例所包含的属性和方法如下：

## **name**

​	本条目的基本文件名，是根据 [`scandir()`]({{< ref "/library/allos/os#os.scandir" >}}) 的 *path* 参数得出的相对路径。

​	如果 [`scandir()`]({{< ref "/library/allos/os#os.scandir" >}}) 的 *path* 参数是 `bytes` 类型，则 [`name`]({{< ref "/library/allos/os#os.name" >}}) 属性也是 `bytes` 类型，否则为 `str`。使用 [`fsdecode()`]({{< ref "/library/allos/os#os.fsdecode" >}}) 解码 byte 类型的文件名。

## **path**

​	本条目的完整路径：等效于 `os.path.join(scandir_path, entry.name)`，其中 *scandir_path* 就是 [`scandir()`]({{< ref "/library/allos/os#os.scandir" >}}) 的 *path* 参数。仅当 [`scandir()`]({{< ref "/library/allos/os#os.scandir" >}}) 的 *path* 参数为绝对路径时，本路径才是绝对路径。如果 [`scandir()`]({{< ref "/library/allos/os#os.scandir" >}}) 的 *path* 参数是 [文件描述符]({{< ref "/library/allos/os#path-fd" >}})，则 [`path`]({{< ref "/library/filesys/os_path#module-os.path" >}}) 属性与上述 [`name`]({{< ref "/library/allos/os#os.name" >}}) 属性相同。

​	如果 [`scandir()`]({{< ref "/library/allos/os#os.scandir" >}}) 的 *path* 参数是 `bytes` 类型，则 [`path`]({{< ref "/library/filesys/os_path#module-os.path" >}}) 属性也是 `bytes` 类型，否则为 `str`。使用 [`fsdecode()`]({{< ref "/library/allos/os#os.fsdecode" >}}) 解码 byte 类型的文件名。

## **inode**()

​	返回本条目的索引节点号 (inode number)。

​	这一结果是缓存在 `os.DirEntry` 对象中的，请调用 `os.stat(entry.path, follow_symlinks=False).st_ino` 来获取最新信息。

​	一开始没有缓存时，在 Windows 上需要一次系统调用，但在 Unix 上不需要。

## **is_dir**(***, *follow_symlinks=True*)

​	如果本条目是目录，或是指向目录的符号链接，则返回 `True`。如果本条目是文件，或指向任何其他类型的文件，或该目录不再存在，则返回 `False`。

​	如果 *follow_symlinks* 是 `False`，那么仅当本条目为目录时返回 `True` （不跟踪符号链接），如果本条目是任何类型的文件，或该文件不再存在，则返回 `False`。

​	这一结果是缓存在 `os.DirEntry` 对象中的，且 *follow_symlinks* 为 `True` 和 `False` 时的缓存是分开的。请调用 [`os.stat()`]({{< ref "/library/allos/os#os.stat" >}}) 和 [`stat.S_ISDIR()`]({{< ref "/library/filesys/stat#stat.S_ISDIR" >}}) 来获取最新信息。

​	一开始没有缓存时，大多数情况下不需要系统调用。特别是对于非符号链接，Windows 和 Unix 都不需要系统调用，除非某些 Unix 文件系统（如网络文件系统）返回了 `dirent.d_type == DT_UNKNOWN`。如果本条目是符号链接，则需要一次系统调用来跟踪它（除非 *follow_symlinks* 为 `False`）。

​	本方法可能抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 异常，如 [`PermissionError`]({{< ref "/library/exceptions#PermissionError" >}}) 异常，但 [`FileNotFoundError`]({{< ref "/library/exceptions#FileNotFoundError" >}}) 异常会被内部捕获且不会抛出。

## **is_file**(***, *follow_symlinks=True*)

​	如果本条目是文件，或是指向文件的符号链接，则返回 `True`。如果本条目是目录，或指向目录，或指向其他非文件条目，或该文件不再存在，则返回 `False`。

​	如果 *follow_symlinks* 是 `False`，那么仅当本条目为文件时返回 `True` （不跟踪符号链接），如果本条目是目录或其他非文件条目，或该文件不再存在，则返回 `False`。

​	这一结果是缓存在 `os.DirEntry` 对象中的。缓存、系统调用、异常抛出都与 [`is_dir()`]({{< ref "/library/allos/os#os.DirEntry.is_dir" >}}) 一致。

## **is_symlink**()

​	如果本条目是符号链接（即使是断开的链接），返回 `True`。如果是目录或任何类型的文件，或本条目不再存在，返回 `False`。

​	这一结果是缓存在 `os.DirEntry` 对象中的，请调用 [`os.path.islink()`]({{< ref "/library/filesys/os_path#os.path.islink" >}}) 来获取最新信息。

​	一开始没有缓存时，大多数情况下不需要系统调用。其实 Windows 和 Unix 都不需要系统调用，除非某些 Unix 文件系统（如网络文件系统）返回了 `dirent.d_type == DT_UNKNOWN`。

​	本方法可能抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 异常，如 [`PermissionError`]({{< ref "/library/exceptions#PermissionError" >}}) 异常，但 [`FileNotFoundError`]({{< ref "/library/exceptions#FileNotFoundError" >}}) 异常会被内部捕获且不会抛出。

## **is_junction**()

​	如果本条目是接合点（即使已断开）则返回 `True`；如果条目指向常规目录、任何种类的文件、符号链接或者已不存在则返回 `False`。

​	结果是缓存在 `os.DirEntry` 对象中的。 调用 [`os.path.isjunction()`]({{< ref "/library/filesys/os_path#os.path.isjunction" >}}) 来获取更新信息。

> Added in version 3.12.
>

## **stat**(***, *follow_symlinks=True*)

​	返回本条目对应的 [`stat_result`]({{< ref "/library/allos/os#os.stat_result" >}}) 对象。本方法默认会跟踪符号链接，要获取符号链接本身的 stat，请添加 `follow_symlinks=False` 参数。

​	在 Unix 上，本方法需要一次系统调用。在 Windows 上，仅在 *follow_symlinks* 为 `True` 且该条目是一个重解析点（如符号链接或目录结点）时，才需要一次系统调用。

​	在 Windows 上，[`stat_result`]({{< ref "/library/allos/os#os.stat_result" >}}) 的 `st_ino`、`st_dev` 和 `st_nlink` 属性总是为零。请调用 [`os.stat()`]({{< ref "/library/allos/os#os.stat" >}}) 以获得这些属性。

​	这一结果是缓存在 `os.DirEntry` 对象中的，且 *follow_symlinks* 为 `True` 和 `False` 时的缓存是分开的。请调用 [`os.stat()`]({{< ref "/library/allos/os#os.stat" >}}) 来获取最新信息。

​	请注意 `os.DirEntry` 和 [`pathlib.Path`]({{< ref "/library/filesys/pathlib#pathlib.Path" >}}) 的几个属性和方法之间存在很好的对应关系。 具体来说，`name` 属性具有相同的含义，`is_dir()`, `is_file()`, `is_symlink()`, `is_junction()` 和 `stat()` 方法也是如此。

> Added in version 3.5.
>

*在 3.6 版本发生变更:* 添加了对 [`PathLike`]({{< ref "/library/allos/os#os.PathLike" >}}) 接口的支持。在 Windows 上添加了对 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 类型路径的支持。

*在 3.12 版本发生变更:* 统计结果的 `st_ctime` 属性在 Windows 上已被弃用。 文件创建时间可通过 `st_birthtime` 来访问，在未来 `st_ctime` 可能会改为返回零或元数据的修改时间，如果可用的话。

## os.**stat**(*path*, ***, *dir_fd=None*, *follow_symlinks=True*)

​	获取文件或文件描述符的状态。在所给路径上执行等效于 `stat()` 系统调用的操作。*path* 可以是字符串类型，或（直接传入或通过 [`PathLike`]({{< ref "/library/allos/os#os.PathLike" >}}) 接口间接传入的） bytes 类型，或打开的文件描述符。返回一个 [`stat_result`]({{< ref "/library/allos/os#os.stat_result" >}}) 对象。

​	本函数默认会跟踪符号链接，要获取符号链接本身的 stat，请添加 `follow_symlinks=False` 参数，或使用 [`lstat()`]({{< ref "/library/allos/os#os.lstat" >}})。

​	本函数支持 [指定文件描述符为参数]({{< ref "/library/allos/os#path-fd" >}}) 和 [不跟踪符号链接]({{< ref "/library/allos/os#follow-symlinks" >}})。

​	在 Windows 上，传入 `follow_symlinks=False` 将禁用所有名称代理重解析点，其中包括符号链接和目录结点。其他类型的重解析点将直接打开，比如不像链接的或系统无法跟踪的重解析点。当多个链接形成一个链时，本方法可能会返回原始链接的 stat，无法完整遍历到非链接的对象。在这种情况下，要获取最终路径的 stat，请使用 [`os.path.realpath()`]({{< ref "/library/filesys/os_path#os.path.realpath" >}}) 函数尽可能地解析路径，并在解析结果上调用 [`lstat()`]({{< ref "/library/allos/os#os.lstat" >}})。这不适用于空链接或交接点，否则会抛出异常。

​	示例:



``` python
>>> import os
>>> statinfo = os.stat('somefile.txt')
>>> statinfo
os.stat_result(st_mode=33188, st_ino=7876932, st_dev=234881026,
st_nlink=1, st_uid=501, st_gid=501, st_size=264, st_atime=1297230295,
st_mtime=1297230027, st_ctime=1297230027)
>>> statinfo.st_size
264
```

​	参见

 

[`fstat()`]({{< ref "/library/allos/os#os.fstat" >}}) 和 [`lstat()`]({{< ref "/library/allos/os#os.lstat" >}}) 函数。

*在 3.3 版本发生变更:* 增加了 *dir_fd* 和 *follow_symlinks* 形参，用于指定一个文件描述符而不是路径。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

*在 3.8 版本发生变更:* 在 Windows 上，本方法将跟踪系统能解析的所有重解析点，并且传入 `follow_symlinks=False` 会停止跟踪所有名称代理重解析点。现在，如果操作系统遇到无法跟踪的重解析点，*stat* 将返回原始路径的信息，就像已指定 `follow_symlinks=False` 一样，而不会抛出异常。

## *class* os.**stat_result**

​	对象的属性大致对应于 `stat` 结构体的成员。 它将被用作 [`os.stat()`]({{< ref "/library/allos/os#os.stat" >}}), [`os.fstat()`]({{< ref "/library/allos/os#os.fstat" >}}) 和 [`os.lstat()`]({{< ref "/library/allos/os#os.lstat" >}}) 的输出结果。

​	属性：

## **st_mode**

​	文件模式：包括文件类型和文件模式位（即权限位）。

## **st_ino**

​	与平台有关，但如果不为零，则根据 `st_dev` 值唯一地标识文件。通常：

- 在 Unix 上该值表示索引节点号 (inode number)。
- 在 Windows 上该值表示 [文件索引号](https://msdn.microsoft.com/en-us/library/aa363788) 。

## **st_dev**

​	该文件所在设备的标识符。

## **st_nlink**

​	硬链接的数量。

## **st_uid**

​	文件所有者的用户 ID。

## **st_gid**

​	文件所有者的用户组 ID。

## **st_size**

​	文件大小（以字节为单位），文件可以是常规文件或符号链接。符号链接的大小是它包含的路径的长度，不包括末尾的空字节。

​	时间戳：

## **st_atime**

​	最近的访问时间，以秒为单位。

## **st_mtime**

​	最近的修改时间，以秒为单位。

## **st_ctime**

​	以秒数表示的元数据最近更改的时间。

*在 3.12 版本发生变更:* `st_ctime` 在 Windows 上已被弃用。 请使用 `st_birthtime` 获取文件创建时间。 在未来，`st_ctime` 将包含最近的元数据修改时间，与其他平台一样。

## **st_atime_ns**

​	最近的访问时间，以纳秒表示，为整数。

> Added in version 3.3.
>

## **st_mtime_ns**

​	最近的修改时间，以纳秒表示，为整数。

> Added in version 3.3.
>

## **st_ctime_ns**

​	最近的元数据修改时间，表示为一个以纳秒为单位的整数。

> Added in version 3.3.
>

*在 3.12 版本发生变更:* `st_ctime_ns` 在 Windows 上已被弃用。 请使用 `st_birthtime_ns` 获取文件创建时间。 在未来，`st_ctime` 将包含最近的元数据修改时间，与其他平台一样。

## **st_birthtime**

​	以秒为单位的文件创建时间。 该属性并不总是可用的，并可能引发 [`AttributeError`]({{< ref "/library/exceptions#AttributeError" >}})。

*在 3.12 版本发生变更:* 目前 `st_birthtime` 已在 Windows 上可用。

## **st_birthtime_ns**

​	表示为一个以纳秒为单位的整数的文件创建时间。 该属性并不总是可用，并可能引发 [`AttributeError`]({{< ref "/library/exceptions#AttributeError" >}})。

> Added in version 3.12.
>

​	备注

 

[`st_atime`]({{< ref "/library/allos/os#os.stat_result.st_atime" >}}), [`st_mtime`]({{< ref "/library/allos/os#os.stat_result.st_mtime" >}}), [`st_ctime`]({{< ref "/library/allos/os#os.stat_result.st_ctime" >}}) 和 [`st_birthtime`]({{< ref "/library/allos/os#os.stat_result.st_birthtime" >}}) 等属性的确切含义和精度依赖于操作系统和文件系统。 例如，在使用 FAT32 文件系统的 Windows 系统上，[`st_mtime`]({{< ref "/library/allos/os#os.stat_result.st_mtime" >}}) 的精度为 2 秒，而 [`st_atime`]({{< ref "/library/allos/os#os.stat_result.st_atime" >}}) 的精度只有 1 天。 请参阅你的操作系统文档了解详情。

​	类似地，尽管 [`st_atime_ns`]({{< ref "/library/allos/os#os.stat_result.st_atime_ns" >}}), [`st_mtime_ns`]({{< ref "/library/allos/os#os.stat_result.st_mtime_ns" >}}), [`st_ctime_ns`]({{< ref "/library/allos/os#os.stat_result.st_ctime_ns" >}}) 和 [`st_birthtime_ns`]({{< ref "/library/allos/os#os.stat_result.st_birthtime_ns" >}}) 始终以纳秒表示，但许多系统并不提供纳秒级精度。 在确实提供纳秒级精度的系统上，用于存储 [`st_atime`]({{< ref "/library/allos/os#os.stat_result.st_atime" >}}), [`st_mtime`]({{< ref "/library/allos/os#os.stat_result.st_mtime" >}}), [`st_ctime`]({{< ref "/library/allos/os#os.stat_result.st_ctime" >}}) 和 [`st_birthtime`]({{< ref "/library/allos/os#os.stat_result.st_birthtime" >}}) 的浮点数对象无法保留所有精度，因此不是完全准确的。 如果你需要准确的时间戳你应始终使用 [`st_atime_ns`]({{< ref "/library/allos/os#os.stat_result.st_atime_ns" >}}), [`st_mtime_ns`]({{< ref "/library/allos/os#os.stat_result.st_mtime_ns" >}}), [`st_ctime_ns`]({{< ref "/library/allos/os#os.stat_result.st_ctime_ns" >}}) 和 [`st_birthtime_ns`]({{< ref "/library/allos/os#os.stat_result.st_birthtime_ns" >}})。

​	在某些 Unix 系统上（如 Linux 上），以下属性可能也可用：

## **st_blocks**

​	为文件分配的字节块数，每块 512 字节。文件是稀疏文件时，它可能小于 [`st_size`]({{< ref "/library/allos/os#os.stat_result.st_size" >}})/512。

## **st_blksize**

​	“首选的” 块大小，用于提高文件系统 I/O 效率。写入文件时块大小太小可能会导致读取-修改-重写效率低下。

## **st_rdev**

​	设备类型（如果是 inode 设备）。

## **st_flags**

​	用户定义的文件标志位。

​	在其他 Unix 系统上（如 FreeBSD 上），以下属性可能可用（但仅当 root 使用它们时才被填充）：

## **st_gen**

​	文件生成号。

​	在 Solaris 及其衍生版本上，以下属性可能也可用：

## **st_fstype**

​	文件所在文件系统的类型的唯一标识，为字符串。

​	在 macOS 系统上，以下属性可能也可用：

## **st_rsize**

​	文件的实际大小。

## **st_creator**

​	文件的创建者。

## **st_type**

​	文件类型。

​	在 Windows 系统上，以下属性也可用：

## **st_file_attributes**

​	Windows 文件属性：由 `GetFileInformationByHandle()` 返回的 `BY_HANDLE_FILE_INFORMATION` 结构体的 `dwFileAttributes` 成员。 参见 [`stat`]({{< ref "/library/filesys/stat#module-stat" >}}) 模块中的 `FILE_ATTRIBUTE_* <stat.FILE_ATTRIBUTE_ARCHIVE>` 常量。

> Added in version 3.5.
>

## **st_reparse_tag**

​	当 [`st_file_attributes`]({{< ref "/library/allos/os#os.stat_result.st_file_attributes" >}}) 存在 [`FILE_ATTRIBUTE_REPARSE_POINT`]({{< ref "/library/filesys/stat#stat.FILE_ATTRIBUTE_REPARSE_POINT" >}}) 集合时，本字段将包含标识重解析点的类型的标签。 请参阅 [`stat`]({{< ref "/library/filesys/stat#module-stat" >}}) 模块中的 [`IO_REPARSE_TAG_*`]({{< ref "/library/filesys/stat#stat.IO_REPARSE_TAG_SYMLINK" >}}) 常量。

​	标准模块 [`stat`]({{< ref "/library/filesys/stat#module-stat" >}}) 定义了一些可用于从 `stat` 结构体中提取信息的函数和常量。 （在 Windows 上，某些项填充了虚拟值。）

​	为了向下兼容，[`stat_result`]({{< ref "/library/allos/os#os.stat_result" >}}) 实例还可以作为至少包含 10 个整数的元组来访问以提供 `stat` 结构体中最重要（且可移植）的成员，其顺序为 [`st_mode`]({{< ref "/library/allos/os#os.stat_result.st_mode" >}}), [`st_ino`]({{< ref "/library/allos/os#os.stat_result.st_ino" >}}), [`st_dev`]({{< ref "/library/allos/os#os.stat_result.st_dev" >}}), [`st_nlink`]({{< ref "/library/allos/os#os.stat_result.st_nlink" >}}), [`st_uid`]({{< ref "/library/allos/os#os.stat_result.st_uid" >}}), [`st_gid`]({{< ref "/library/allos/os#os.stat_result.st_gid" >}}), [`st_size`]({{< ref "/library/allos/os#os.stat_result.st_size" >}}), [`st_atime`]({{< ref "/library/allos/os#os.stat_result.st_atime" >}}), [`st_mtime`]({{< ref "/library/allos/os#os.stat_result.st_mtime" >}}), [`st_ctime`]({{< ref "/library/allos/os#os.stat_result.st_ctime" >}})。 某些实现还可能在末尾添加更多条目。 为了与旧版 Python 兼容，以元组形式访问 [`stat_result`]({{< ref "/library/allos/os#os.stat_result" >}}) 将始终返回整数。

*在 3.5 版本发生变更:* 在 Windows 上，如果可用，会返回文件索引作为 [`st_ino`]({{< ref "/library/allos/os#os.stat_result.st_ino" >}}) 的值。

*在 3.7 版本发生变更:* 在 Solaris 及其衍生版本上添加了 [`st_fstype`]({{< ref "/library/allos/os#os.stat_result.st_fstype" >}}) 成员。

*在 3.8 版本发生变更:* 在 Windows 上添加了 [`st_reparse_tag`]({{< ref "/library/allos/os#os.stat_result.st_reparse_tag" >}}) 成员。

*在 3.8 版本发生变更:* 在 Windows 上，[`st_mode`]({{< ref "/library/allos/os#os.stat_result.st_mode" >}}) 成员现在可以根据需要将特殊文件标识为 `S_IFCHR`、`S_IFIFO` 或 `S_IFBLK`。

*在 3.12 版本发生变更:* 在 Windows 上，[`st_ctime`]({{< ref "/library/allos/os#os.stat_result.st_ctime" >}}) 已被弃用。 最终，它将包含元数据的最后修改时间，以与其他平台保持一致，但目前仍包含创建时间。 请使用 [`st_birthtime`]({{< ref "/library/allos/os#os.stat_result.st_birthtime" >}}) 来获取创建时间。

​	在 Windows 上，现在 [`st_ino`]({{< ref "/library/allos/os#os.stat_result.st_ino" >}}) 最多可为 128 比特位，具体取决于文件系统。 在之前它不会超过 64 比特位，更长的文件标识符会被强制缩减。

​	在 Windows 上，[`st_rdev`]({{< ref "/library/allos/os#os.stat_result.st_rdev" >}}) 将不再返回值。 在之前它将包含与 [`st_dev`]({{< ref "/library/allos/os#os.stat_result.st_dev" >}}) 相同的值，这是不正确的。

​	在 Windows 上增加了 [`st_birthtime`]({{< ref "/library/allos/os#os.stat_result.st_birthtime" >}}) 成员。

## os.**statvfs**(*path*)

​	在给定的路径上执行 `statvfs()` 系统调用。 返回值是一个对象，其属性描述了所给路径上的文件系统，并且与 `statvfs` 结构体的成员相对应，即: `f_bsize`, `f_frsize`, `f_blocks`, `f_bfree`, `f_bavail`, `f_files`, `f_ffree`, `f_favail`, `f_flag`, `f_namemax`, `f_fsid`。

​	为 `f_flag` 属性位定义了两个模块级常量：如果存在 `ST_RDONLY` 位，则文件系统以只读挂载；如果存在 `ST_NOSUID` 位，则文件系统禁用或不支持 setuid/setgid 位。

​	为基于 GNU/glibc 的系统还定义了额外的模块级常量。它们是 `ST_NODEV` （禁止访问设备专用文件），`ST_NOEXEC` （禁止执行程序），`ST_SYNCHRONOUS` （写入后立即同步），`ST_MANDLOCK` （允许文件系统上的强制锁定），`ST_WRITE` （写入文件/目录/符号链接），`ST_APPEND` （仅追加文件），`ST_IMMUTABLE` （不可变文件），`ST_NOATIME` （不更新访问时间），`ST_NODIRATIME` （不更新目录访问时间），`ST_RELATIME` （相对于 mtime/ctime 更新访问时间）。

​	本函数支持 [指定文件描述符为参数]({{< ref "/library/allos/os#path-fd" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

*在 3.2 版本发生变更:* 添加了 `ST_RDONLY` 和 `ST_NOSUID` 常量。

*在 3.3 版本发生变更:* 新增支持将 *path* 参数指定为打开的文件描述符。

*在 3.4 版本发生变更:* 添加了 `ST_NODEV`、`ST_NOEXEC`、`ST_SYNCHRONOUS`、`ST_MANDLOCK`、`ST_WRITE`、`ST_APPEND`、`ST_IMMUTABLE`、`ST_NOATIME`、`ST_NODIRATIME` 和 `ST_RELATIME` 常量。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

*在 3.7 版本发生变更:* 增加了 `f_fsid` 属性。

## os.**supports_dir_fd**

​	一个 [`set`]({{< ref "/library/stdtypes#set" >}}) 对象，指示 [`os`]({{< ref "/library/allos/os#module-os" >}}) 模块中的哪些函数接受一个打开的文件描述符作为 *dir_fd* 参数。不同平台提供的功能不同，且 Python 用于实现 *dir_fd* 参数的底层函数并非在 Python 支持的所有平台上都可用。考虑到一致性，支持 *dir_fd* 的函数始终允许指定描述符，但如果在底层不支持时调用了该函数，则会抛出异常。（在所有平台上始终支持将 *dir_fd* 指定为 `None`。）

​	要检查某个函数是否接受打开的文件描述符作为 *dir_fd* 参数，请在 `supports_dir_fd` 前使用 `in` 运算符。例如，如果 [`os.stat()`]({{< ref "/library/allos/os#os.stat" >}}) 在当前平台上接受打开的文件描述符作为 *dir_fd* 参数，则此表达式的计算结果为 `True`:

```
os.stat in os.supports_dir_fd
```

​	目前 *dir_fd* 参数仅在 Unix 平台上有效，在 Windows 上均无效。

> Added in version 3.3.
>

## os.**supports_effective_ids**

​	一个 [`set`]({{< ref "/library/stdtypes#set" >}}) 对象，指示 [`os.access()`]({{< ref "/library/allos/os#os.access" >}}) 是否允许在当前平台上将其 *effective_ids* 参数指定为 `True`。（所有平台都支持将 *effective_ids* 指定为 `False`。）如果当前平台支持，则集合将包含 [`os.access()`]({{< ref "/library/allos/os#os.access" >}})，否则集合为空。

​	如果当前平台上的 [`os.access()`]({{< ref "/library/allos/os#os.access" >}}) 支持 `effective_ids=True`，则此表达式的计算结果为 `True`:

```
os.access in os.supports_effective_ids
```

​	目前仅 Unix 平台支持 *effective_ids*，Windows 不支持。

> Added in version 3.3.
>

## os.**supports_fd**

​	一个 [`set`]({{< ref "/library/stdtypes#set" >}}) 对象，指示在当前平台上 [`os`]({{< ref "/library/allos/os#module-os" >}}) 模块中的哪些函数接受一个打开的文件描述符作为 *path* 参数。不同平台提供的功能不同，且 Python 所使用到的底层函数（用于实现接受描述符作为 *path*）并非在 Python 支持的所有平台上都可用。

​	要判断某个函数是否接受打开的文件描述符作为 *path* 参数，请在 `supports_fd` 前使用 `in` 运算符。例如，如果 [`os.chdir()`]({{< ref "/library/allos/os#os.chdir" >}}) 在当前平台上接受打开的文件描述符作为 *path* 参数，则此表达式的计算结果为 `True`:

```
os.chdir in os.supports_fd
```

> Added in version 3.3.
>

## os.**supports_follow_symlinks**

​	一个 [`set`]({{< ref "/library/stdtypes#set" >}}) 对象，指示在当前平台上 [`os`]({{< ref "/library/allos/os#module-os" >}}) 模块中的哪些函数的 *follow_symlinks* 参数可指定为 `False`。不同平台提供的功能不同，且 Python 用于实现 *follow_symlinks* 的底层函数并非在 Python 支持的所有平台上都可用。考虑到一致性，支持 *follow_symlinks* 的函数始终允许将其指定为 `False`，但如果在底层不支持时调用了该函数，则会抛出异常。（在所有平台上始终支持将 *follow_symlinks* 指定为 `True`。）

​	要检查某个函数的 *follow_symlinks* 参数是否可以指定为 `False`，请在 `supports_follow_symlinks` 前使用 `in` 运算符。例如，如果在当前平台上调用 [`os.stat()`]({{< ref "/library/allos/os#os.stat" >}}) 时可以指定 `follow_symlinks=False`，则此表达式的计算结果为 `True`:

```
os.stat in os.supports_follow_symlinks
```

> Added in version 3.3.
>

## os.**symlink**(*src*, *dst*, *target_is_directory=False*, ***, *dir_fd=None*)

​	创建一个指向 *src* 的符号链接，名为 *dst*。

​	在 Windows 上，符号链接可以表示文件或目录两种类型，并且不会动态改变类型。如果目标存在，则新建链接的类型将与目标一致。否则，如果 *target_is_directory* 为 `True`，则符号链接将创建为目录链接，为 `False` （默认）将创建为文件链接。在非 Windows 平台上，*target_is_directory* 被忽略。

​	本函数支持 [基于目录描述符的相对路径]({{< ref "/library/allos/os#dir-fd" >}})。

​	备注

 

​	在 Windows 10 或更高版本上，如果启用了开发人员模式，非特权帐户可以创建符号链接。如果开发人员模式不可用/未启用，则需要 *SeCreateSymbolicLinkPrivilege* 权限，或者该进程必须以管理员身份运行。

​	当本函数由非特权账户调用时，抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 异常。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.symlink` 并附带参数 `src`, `dst`, `dir_fd`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows.

​	此函数在 WASI 是受限的，请参阅 [WebAssembly 平台]({{< ref "/library/intro#wasm-availability" >}}) 了解详情。

*在 3.2 版本发生变更:* 添加对 Windows 6.0 (Vista) 符号链接的支持。

*在 3.3 版本发生变更:* 增加了 *dir_fd* 形参，现在将在非 Windows 平台上允许 *target_is_directory*。

*在 3.6 版本发生变更:* 接受一个 [类路径对象]({{< ref "/glossary/idx#term-path-like-object" >}}) 作为 *src* 和 *dst*。

*在 3.8 版本发生变更:* 针对启用了开发人员模式的 Windows，添加了非特权账户创建符号链接的支持。

## os.**sync**()

​	强制将所有内容写入磁盘。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

> Added in version 3.3.
>

## os.**truncate**(*path*, *length*)

​	截断 *path* 对应的文件，以使其最大为 *length* 字节。

​	本函数支持 [指定文件描述符为参数]({{< ref "/library/allos/os#path-fd" >}})。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.truncate` 并附带参数 `path`, `length`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows.

> Added in version 3.3.
>

*在 3.5 版本发生变更:* 添加了 Windows 支持

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## os.**unlink**(*path*, ***, *dir_fd=None*)

​	移除（删除）文件 *path*。该函数在语义上与 [`remove()`]({{< ref "/library/allos/os#os.remove" >}}) 相同，`unlink` 是其传统的 Unix 名称。请参阅 [`remove()`]({{< ref "/library/allos/os#os.remove" >}}) 的文档以获取更多信息。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.remove` 并附带参数 `path`, `dir_fd`。

*在 3.3 版本发生变更:* 添加了 *dir_fd* 参数。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## os.**utime**(*path*, *times=None*, ***, [*ns*, ]*dir_fd=None*, *follow_symlinks=True*)

​	设置文件 *path* 的访问时间和修改时间。

[`utime()`]({{< ref "/library/allos/os#os.utime" >}}) 有 *times* 和 *ns* 两个可选参数，它们指定了设置给 *path* 的时间，用法如下：

- 如果指定 *ns*，它必须是一个 `(atime_ns, mtime_ns)` 形式的二元组，其中每个成员都是一个表示纳秒的整数。
- 如果 *times* 不为 `None`，则它必须是 `(atime, mtime)` 形式的二元组，其中每个成员都是一个表示秒的 int 或 float。
- 如果 *times* 为 `None` 且未指定 *ns*，则相当于指定 `ns=(atime_ns, mtime_ns)`，其中两个时间均为当前时间。

​	同时为 *times* 和 *ns* 指定元组会出错。

​	请注意你在此处设置的确切时间可能不会被后续的 [`stat()`]({{< ref "/library/allos/os#os.stat" >}}) 调用所返回，具体取决于你的操作系统记录访问和修改时间的分辨率；请参阅 [`stat()`]({{< ref "/library/allos/os#os.stat" >}})。 保留准确时间的最佳方式是使用来自于将 *ns* 形参设为 [`utime()`]({{< ref "/library/allos/os#os.utime" >}}) 的 [`os.stat()`]({{< ref "/library/allos/os#os.stat" >}}) 结果对象的 *st_atime_ns* 和 *st_mtime_ns* 字段。

​	本函数支持 [指定文件描述符]({{< ref "/library/allos/os#path-fd" >}})、[指定基于目录描述符的相对路径]({{< ref "/library/allos/os#dir-fd" >}}) 和 [不跟踪符号链接]({{< ref "/library/allos/os#follow-symlinks" >}})。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.utime` 并附带参数 `path`, `times`, `ns`, `dir_fd`。

*在 3.3 版本发生变更:* 新增支持将 *path* 参数指定为打开的文件描述符，以及支持 *dir_fd*、*follow_symlinks* 和 *ns* 参数。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## os.**walk**(*top*, *topdown=True*, *onerror=None*, *followlinks=False*)

​	生成目录树中的文件名，方式是按上->下或下->上顺序浏览目录树。对于以 *top* 为根的目录树中的每个目录（包括 *top* 本身），它都会生成一个三元组 `(dirpath, dirnames, filenames)`。

*dirpath* 是一个字符串，表示目录的路径。 *dirnames* 是由 *dirpath* 中的子目录名称组成的列表 (包括指向目录的符号链接，不包括 `'.'` 和 `'..'`)。 *filenames* 是由 *dirpath* 中非目录文件名称组成的列表。 请注意列表中的名称不包含路径部分。 要获取 *dirpath* 中文件或目录的完整路径 (以 *top* 打头，请执行 `os.path.join(dirpath, name)`。 列表是否排序取决于具体文件系统。 如果有文件在列表生成期间被移除或添加到 *dirpath*，是否要包括该文件的名称并没有规定。

​	如果可选参数 *topdown* 为 `True` 或未指定，则在所有子目录的三元组之前生成父目录的三元组（目录是自上而下生成的）。如果 *topdown* 为 `False`，则在所有子目录的三元组生成之后再生成父目录的三元组（目录是自下而上生成的）。无论 *topdown* 为何值，在生成目录及其子目录的元组之前，都将检索全部子目录列表。

​	当 *topdown* 为 `True` 时，调用者可以就地修改 *dirnames* 列表（也许用到了 [`del`]({{< ref "/reference/simple_stmts#del" >}}) 或切片），而 [`walk()`]({{< ref "/library/allos/os#os.walk" >}}) 将仅仅递归到仍保留在 *dirnames* 中的子目录内。这可用于减少搜索、加入特定的访问顺序，甚至可在继续 [`walk()`]({{< ref "/library/allos/os#os.walk" >}}) 之前告知 [`walk()`]({{< ref "/library/allos/os#os.walk" >}}) 由调用者新建或重命名的目录的信息。当 *topdown* 为 `False` 时，修改 *dirnames* 对 walk 的行为没有影响，因为在自下而上模式中，*dirnames* 中的目录是在 *dirpath* 本身之前生成的。

​	默认将忽略 [`scandir()`]({{< ref "/library/allos/os#os.scandir" >}}) 调用中的错误。如果指定了可选参数 *onerror*，它应该是一个函数。出错时它会被调用，参数是一个 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 实例。它可以报告错误然后继续遍历，或者抛出异常然后中止遍历。注意，可以从异常对象的 `filename` 属性中获取出错的文件名。

[`walk()`]({{< ref "/library/allos/os#os.walk" >}}) 默认不会递归进指向目录的符号链接。可以在支持符号链接的系统上将 *followlinks* 设置为 `True`，以访问符号链接指向的目录。

​	备注

 

​	注意，如果链接指向自身的父目录，则将 *followlinks* 设置为 `True` 可能导致无限递归。[`walk()`]({{< ref "/library/allos/os#os.walk" >}}) 不会记录它已经访问过的目录。

​	备注

 

​	如果传入的是相对路径，请不要在恢复 [`walk()`]({{< ref "/library/allos/os#os.walk" >}}) 之间更改当前工作目录。[`walk()`]({{< ref "/library/allos/os#os.walk" >}}) 不会更改当前目录，并假定其调用者也不会更改当前目录。

​	下面的示例遍历起始目录内所有子目录，打印每个目录内的文件占用的字节数，CVS 子目录不会被遍历:

```
import os
from os.path import join, getsize
for root, dirs, files in os.walk('python/Lib/email'):
    print(root, "consumes", end=" ")
    print(sum(getsize(join(root, name)) for name in files), end=" ")
    print("bytes in", len(files), "non-directory files")
    if 'CVS' in dirs:
        dirs.remove('CVS')  # don't visit CVS directories
```

​	在下一个示例（[`shutil.rmtree()`]({{< ref "/library/filesys/shutil#shutil.rmtree" >}}) 的简单实现）中，必须使树自下而上遍历，因为 [`rmdir()`]({{< ref "/library/allos/os#os.rmdir" >}}) 只允许在目录为空时删除目录:

```
# Delete everything reachable from the directory named in "top",
# assuming there are no symbolic links.
# CAUTION:  This is dangerous!  For example, if top == '/', it
# could delete all your disk files.
import os
for root, dirs, files in os.walk(top, topdown=False):
    for name in files:
        os.remove(os.path.join(root, name))
    for name in dirs:
        os.rmdir(os.path.join(root, name))
os.rmdir(top)
```

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.walk` 并附带参数 `top`, `topdown`, `onerror`, `followlinks`。

*在 3.5 版本发生变更:* 现在，本函数调用的是 [`os.scandir()`]({{< ref "/library/allos/os#os.scandir" >}}) 而不是 [`os.listdir()`]({{< ref "/library/allos/os#os.listdir" >}})，从而减少了调用 [`os.stat()`]({{< ref "/library/allos/os#os.stat" >}}) 的次数而变得更快。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## os.**fwalk**(*top='.'*, *topdown=True*, *onerror=None*, ***, *follow_symlinks=False*, *dir_fd=None*)

​	本方法的行为与 [`walk()`]({{< ref "/library/allos/os#os.walk" >}}) 完全一样，除了它产生的是 4 元组 `(dirpath, dirnames, filenames, dirfd)`，并且它支持 `dir_fd`。

*dirpath*、*dirnames* 和 *filenames* 与 [`walk()`]({{< ref "/library/allos/os#os.walk" >}}) 输出的相同，*dirfd* 是指向目录 *dirpath* 的文件描述符。

​	本函数始终支持 [基于目录描述符的相对路径]({{< ref "/library/allos/os#dir-fd" >}}) 和 [不跟踪符号链接]({{< ref "/library/allos/os#follow-symlinks" >}})。但是请注意，与其他函数不同，[`fwalk()`]({{< ref "/library/allos/os#os.fwalk" >}}) 的 *follow_symlinks* 的默认值为 `False`。

​	备注

 

​	由于 [`fwalk()`]({{< ref "/library/allos/os#os.fwalk" >}}) 会生成文件描述符，而它们仅在下一个迭代步骤前有效，因此如果要将描述符保留更久，则应复制它们（比如使用 [`dup()`]({{< ref "/library/allos/os#os.dup" >}})）。

​	下面的示例遍历起始目录内所有子目录，打印每个目录内的文件占用的字节数，CVS 子目录不会被遍历:

```
import os
for root, dirs, files, rootfd in os.fwalk('python/Lib/email'):
    print(root, "consumes", end="")
    print(sum([os.stat(name, dir_fd=rootfd).st_size for name in files]),
          end="")
    print("bytes in", len(files), "non-directory files")
    if 'CVS' in dirs:
        dirs.remove('CVS')  # 不访问 CVS 目录
```

​	在下一个示例中，必须使树自下而上遍历，因为 [`rmdir()`]({{< ref "/library/allos/os#os.rmdir" >}}) 只允许在目录为空时删除目录:

```
# 删除可从 "top" 指定的目录进入的所有东西，
# 假定其中没有符号链接。
# 注意：这很危险！举例来说，如果 top == '/'，
# 它会删除你所有的磁盘文件。
import os
for root, dirs, files, rootfd in os.fwalk(top, topdown=False):
    for name in files:
        os.unlink(name, dir_fd=rootfd)
    for name in dirs:
        os.rmdir(name, dir_fd=rootfd)
```

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.fwalk` 并附带参数 `top`, `topdown`, `onerror`, `follow_symlinks`, `dir_fd`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

> Added in version 3.3.
>

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

*在 3.7 版本发生变更:* 添加了对 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 类型路径的支持。

## os.**memfd_create**(*name*[, *flags=os.MFD_CLOEXEC*])

​	创建一个匿名文件，返回指向该文件的文件描述符。*flags* 必须是系统上可用的 `os.MFD_*` 常量之一（或将它们按位“或”组合起来）。新文件描述符默认是 [不可继承的]({{< ref "/library/allos/os#fd-inheritance" >}})。

*name* 提供的名称会被用作文件名，并且 `/proc/self/fd/` 目录中相应符号链接的目标将显示为该名称。显示的名称始终以 `memfd:` 为前缀，并且仅用于调试目的。名称不会影响文件描述符的行为，因此多个文件可以有相同的名称，不会有副作用。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 3.17 with glibc >= 2.27.

> Added in version 3.8.
>

## os.**MFD_CLOEXEC**

## os.**MFD_ALLOW_SEALING**

## os.**MFD_HUGETLB**

## os.**MFD_HUGE_SHIFT**

## os.**MFD_HUGE_MASK**

## os.**MFD_HUGE_64KB**

## os.**MFD_HUGE_512KB**

## os.**MFD_HUGE_1MB**

## os.**MFD_HUGE_2MB**

## os.**MFD_HUGE_8MB**

## os.**MFD_HUGE_16MB**

## os.**MFD_HUGE_32MB**

## os.**MFD_HUGE_256MB**

## os.**MFD_HUGE_512MB**

## os.**MFD_HUGE_1GB**

## os.**MFD_HUGE_2GB**

## os.**MFD_HUGE_16GB**

​	以上标志位可以传递给 [`memfd_create()`]({{< ref "/library/allos/os#os.memfd_create" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 3.17 with glibc >= 2.27

`MFD_HUGE*` 旗标仅从 Linux 4.14 开始可用。

> Added in version 3.8.
>

## os.**eventfd**(*initval*[, *flags=os.EFD_CLOEXEC*])

​	创建并返回一个事件文件描述符。此文件描述符支持缓冲区大小为 8 的原生 [`read()`]({{< ref "/library/allos/os#os.read" >}}) 和 [`write()`]({{< ref "/library/allos/os#os.write" >}}) 操作、[`select()`]({{< ref "/library/ipc/select#select.select" >}}) 、[`poll()`]({{< ref "/library/ipc/select#select.poll" >}}) 等类似操作。更多信息请参阅 man 文档 *[eventfd(2)](https://manpages.debian.org/eventfd(2))*。默认情况下，新的文件描述符是 [non-inheritable]({{< ref "/library/allos/os#fd-inheritance" >}})。

*initval* 是事件计数哭喊的初始值。 该初始值必须是一个 32 位无符号整数。 请注意虽然事件计数器是一个最大值为 264-2 的无符号 64 位整数但初始值仍被限制为 32 位无符号整数。

*flags* 可由 [`EFD_CLOEXEC`]({{< ref "/library/allos/os#os.EFD_CLOEXEC" >}}) 、 [`EFD_NONBLOCK`]({{< ref "/library/allos/os#os.EFD_NONBLOCK" >}}) 和 [`EFD_SEMAPHORE`]({{< ref "/library/allos/os#os.EFD_SEMAPHORE" >}}) 组合而成。

​	如果设置了 [`EFD_SEMAPHORE`]({{< ref "/library/allos/os#os.EFD_SEMAPHORE" >}})，并且事件计数器非零，那么 [`eventfd_read()`]({{< ref "/library/allos/os#os.eventfd_read" >}}) 将返回 1 并将计数器递减 1。

​	如果未设置 [`EFD_SEMAPHORE`]({{< ref "/library/allos/os#os.EFD_SEMAPHORE" >}})，并且事件计数器非零，那么 [`eventfd_read()`]({{< ref "/library/allos/os#os.eventfd_read" >}}) 返回当前的事件计数器值，并将计数器重置为零。

​	如果事件计数器为 0，并且未设置 [`EFD_NONBLOCK`]({{< ref "/library/allos/os#os.EFD_NONBLOCK" >}})，那么 [`eventfd_read()`]({{< ref "/library/allos/os#os.eventfd_read" >}}) 会阻塞。

[`eventfd_write()`]({{< ref "/library/allos/os#os.eventfd_write" >}}) 会递增事件计数器。如果写操作会让计数器的增量大于264-2，则写入会被阻止。

​	示例:

```
import os

# 取值从 '1' 开始的信号量
fd = os.eventfd(1, os.EFD_SEMAPHORE | os.EFC_CLOEXEC)
try:
    # 获取信号量
    v = os.eventfd_read(fd)
    try:
        do_work()
    finally:
        # 释放信号量
        os.eventfd_write(fd, v)
finally:
    os.close(fd)
```

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.27 with glibc >= 2.8

> Added in version 3.10.
>

## os.**eventfd_read**(*fd*)

​	从一个 [`eventfd()`]({{< ref "/library/allos/os#os.eventfd" >}}) 文件描述符中读取数据，并返回一个 64 位无符号整数。该函数不会校验 *fd* 是否为 [`eventfd()`]({{< ref "/library/allos/os#os.eventfd" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.27

> Added in version 3.10.
>

## os.**eventfd_write**(*fd*, *value*)

​	向一个 [`eventfd()`]({{< ref "/library/allos/os#os.eventfd" >}}) 文件描述符加入数据。*value* 必须是一个 64 位无符号整数。本函数不会校验 *fd* 是否为 [`eventfd()`]({{< ref "/library/allos/os#os.eventfd" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.27

> Added in version 3.10.
>

## os.**EFD_CLOEXEC**

​	为新的 [`eventfd()`]({{< ref "/library/allos/os#os.eventfd" >}}) 文件描述符设置 close-on-exec 标志。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.27

> Added in version 3.10.
>

## os.**EFD_NONBLOCK**

​	为新的 [`eventfd()`]({{< ref "/library/allos/os#os.eventfd" >}}) 文件描述符设置 [`O_NONBLOCK`]({{< ref "/library/allos/os#os.O_NONBLOCK" >}}) 状态标志。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.27

> Added in version 3.10.
>

## os.**EFD_SEMAPHORE**

​	为从 [`eventfd()`]({{< ref "/library/allos/os#os.eventfd" >}}) 文件描述符进行读取提供类似信号量的语法。 在读取时内部计数器将递减一。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.30

> Added in version 3.10.
>



### 计时器文件描述符

> Added in version 3.13.
>

​	这些函数提供了对 Linux 的 *计时器文件描述符* API 的支持。 当然，它们仅在 Linux 上可用。

## os.**timerfd_create**(*clockid*, */*, ***, *flags=0*)

​	创建并返回一个计时器文件描述符 (*timerfd*)。

​	由 [`timerfd_create()`]({{< ref "/library/allos/os#os.timerfd_create" >}}) 返回的文件描述符可支持：

- [`read()`]({{< ref "/library/allos/os#os.read" >}})
- [`select()`]({{< ref "/library/ipc/select#select.select" >}})
- [`poll()`]({{< ref "/library/ipc/select#select.poll" >}})

​	文件描述符的 [`read()`]({{< ref "/library/allos/os#os.read" >}}) 方法被调用时可附带大小为 8 的缓冲区。 如果计时器已到期一次或多次，[`read()`]({{< ref "/library/allos/os#os.read" >}}) 将返回以主机端序表示的到期次数，它可通过 `int.from_bytes(x, byteorder=sys.byteorder)` 转换为 [`int`]({{< ref "/library/functions#int" >}})。

[`select()`]({{< ref "/library/ipc/select#select.select" >}}) 和 [`poll()`]({{< ref "/library/ipc/select#select.poll" >}}) 可被用于等待直至计时器到期并且文件描述符为可读状态。

*clockid* 必须是一个有效的 [时钟 ID]({{< ref "/library/allos/time#time-clock-id-constants" >}})，如 [`time`]({{< ref "/library/allos/time#module-time" >}}) 模块中所定义的：

- [`time.CLOCK_REALTIME`]({{< ref "/library/allos/time#time.CLOCK_REALTIME" >}})
- [`time.CLOCK_MONOTONIC`]({{< ref "/library/allos/time#time.CLOCK_MONOTONIC" >}})
- [`time.CLOCK_BOOTTIME`]({{< ref "/library/allos/time#time.CLOCK_BOOTTIME" >}}) (自 Linux 3.15 起用于 timerfd_create)

​	如果 *clockid* 为 [`time.CLOCK_REALTIME`]({{< ref "/library/allos/time#time.CLOCK_REALTIME" >}})，则将使用一个可设置的系统级实时时钟。 如果系统时钟发生改变，计时器设置将需要被更新。 要在系统时钟发生改变时取消计时器，请参阅 [`TFD_TIMER_CANCEL_ON_SET`]({{< ref "/library/allos/os#os.TFD_TIMER_CANCEL_ON_SET" >}})。

​	如果 *clockid* 为 [`time.CLOCK_MONOTONIC`]({{< ref "/library/allos/time#time.CLOCK_MONOTONIC" >}})，将使用一个不可设置的单调递增时钟。 即使系统时钟发生改变，计时器设置也不会受影响。

​	如果 *clockid* 为 [`time.CLOCK_BOOTTIME`]({{< ref "/library/allos/time#time.CLOCK_BOOTTIME" >}})，将与 [`time.CLOCK_MONOTONIC`]({{< ref "/library/allos/time#time.CLOCK_MONOTONIC" >}}) 相似，不同之处在于它还包括任何系统挂起的时间。

​	这个文件描述符的行为可以通过指定 *flags* 值来改变。 可以使用以下任意变量，可以使用按位 OR (`|` 运算符) 来进行组合：

- [`TFD_NONBLOCK`]({{< ref "/library/allos/os#os.TFD_NONBLOCK" >}})
- [`TFD_CLOEXEC`]({{< ref "/library/allos/os#os.TFD_CLOEXEC" >}})

​	如果未将 [`TFD_NONBLOCK`]({{< ref "/library/allos/os#os.TFD_NONBLOCK" >}}) 设为一个旗标，[`read()`]({{< ref "/library/allos/os#os.read" >}}) 将会阻塞直至计时器到期。 如果它被设为旗标，则 [`read()`]({{< ref "/library/allos/os#os.read" >}}) 不会阻塞，但是如果自从上次对 read 的调用以来尚未有一次到期，[`read()`]({{< ref "/library/allos/os#os.read" >}}) 将引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 并将 `errno` 设为 [`errno.EAGAIN`]({{< ref "/library/allos/errno#errno.EAGAIN" >}})。

[`TFD_CLOEXEC`]({{< ref "/library/allos/os#os.TFD_CLOEXEC" >}}) 总是会由 Python 自动设置。

​	该文件描述符在其不再需要时必须通过 [`os.close()`]({{< ref "/library/allos/os#os.close" >}}) 来关闭，否则文件描述符将被泄漏。

​	参见

 

*[timerfd_create(2)](https://manpages.debian.org/timerfd_create(2))* 帮助页。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.27 with glibc >= 2.8

> Added in version 3.13.
>

## os.**timerfd_settime**(*fd*, */*, ***, *flags=flags*, *initial=0.0*, *interval=0.0*)

​	修改一个计时器文件描述符的内部计时器。 此函数将操作与 [`timerfd_settime_ns()`]({{< ref "/library/allos/os#os.timerfd_settime_ns" >}}) 相同的间隔计时器。

*fd* 必须是一个有效的计时器文件描述符。

​	该计时器的行为可以通过指定 *flags* 值来改变。 可以使用以下任意变量，可以使用按位 OR (`|` 运算符) 来进行组合：

- [`TFD_TIMER_ABSTIME`]({{< ref "/library/allos/os#os.TFD_TIMER_ABSTIME" >}})
- [`TFD_TIMER_CANCEL_ON_SET`]({{< ref "/library/allos/os#os.TFD_TIMER_CANCEL_ON_SET" >}})

​	该计时器可通过将 *initial* 设为零 (`0`) 来禁用。 如果 *initial* 大于零，计时器将被启用。 如果 *initial* 小于零，它将引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 异常并将 `errno` 设为 [`errno.EINVAL`]({{< ref "/library/allos/errno#errno.EINVAL" >}})

​	在默认情况下计时器将在经过 *initial* 秒后启动。 （如果 *initial* 为零，计时器将立即启动。）

​	但是，如果设置了 [`TFD_TIMER_ABSTIME`]({{< ref "/library/allos/os#os.TFD_TIMER_ABSTIME" >}}) 旗标，计时器将在计时器时钟 (由 [`timerfd_create()`]({{< ref "/library/allos/os#os.timerfd_create" >}}) 中的 *clockid* 设置) 达到 *initial* 秒时启动。

​	计时器的间隔时间是通过 *interval* [`float`]({{< ref "/library/functions#float" >}}) 设置的。 如果 *interval* 为零，计时器将只在初始到期时启动一次。 如果 *interval* 大于零，计时器将从上一次到期后每经过 *interval* 秒启动一次。 如果 *interval* 小于零，它将引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 并将 `errno` 设为 [`errno.EINVAL`]({{< ref "/library/allos/errno#errno.EINVAL" >}})

​	如果 [`TFD_TIMER_CANCEL_ON_SET`]({{< ref "/library/allos/os#os.TFD_TIMER_CANCEL_ON_SET" >}}) 旗标与 [`TFD_TIMER_ABSTIME`]({{< ref "/library/allos/os#os.TFD_TIMER_ABSTIME" >}}) 一起被设置并且用于此计时器的时钟为 [`time.CLOCK_REALTIME`]({{< ref "/library/allos/time#time.CLOCK_REALTIME" >}})，则当实时计时器发生不连续改变时计时器将被标记为可取消的。 对描述符的读取将被取消并设置 ECANCELED 错误。

​	Linux 将以 UTC 来管理系统时钟。 夏令时调整是仅通过修改时差完成的而不会导致不连续的系统时钟改变。

​	下列事件会导致不连续的系统时钟变化：

- `settimeofday`
- `clock_settime`
- 通过 `date` 命令设置系统日期和时间

​	根据此函数执行之前的计时器状态，返回一个二元组 (`next_expiration`, `interval`)。

​	参见

 

*[timerfd_create(2)](https://manpages.debian.org/timerfd_create(2))*, *[timerfd_settime(2)](https://manpages.debian.org/timerfd_settime(2))*, *[settimeofday(2)](https://manpages.debian.org/settimeofday(2))*, *[clock_settime(2)](https://manpages.debian.org/clock_settime(2))* 以及 *[date(1)](https://manpages.debian.org/date(1))*。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.27 with glibc >= 2.8

> Added in version 3.13.
>

## os.**timerfd_settime_ns**(*fd*, */*, ***, *flags=0*, *initial=0*, *interval=0*)

​	与 [`timerfd_settime()`]({{< ref "/library/allos/os#os.timerfd_settime" >}}) 类似，但使用纳秒形式的时间。 此函数将操作与 [`timerfd_settime()`]({{< ref "/library/allos/os#os.timerfd_settime" >}}) 相同的间隔计时器。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.27 with glibc >= 2.8

> Added in version 3.13.
>

## os.**timerfd_gettime**(*fd*, */*)

​	返回一个浮点数形式的二元组 (`next_expiration`, `interval`)。

`next_expiration` 表示距离定时器下一次启动的相对时间，无论是否设置了 [`TFD_TIMER_ABSTIME`]({{< ref "/library/allos/os#os.TFD_TIMER_ABSTIME" >}}) 旗标。

`interval` 表示计时器的间隔。 如果为零，该计时器将只启动一次，即在经过了 `next_expiration` 秒之后。

​	参见

 

*[timerfd_gettime(2)](https://manpages.debian.org/timerfd_gettime(2))*

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.27 with glibc >= 2.8

> Added in version 3.13.
>

## os.**timerfd_gettime_ns**(*fd*, */*)

​	与 [`timerfd_gettime()`]({{< ref "/library/allos/os#os.timerfd_gettime" >}}) 类似，但返回以纳秒为单位的时间。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.27 with glibc >= 2.8

> Added in version 3.13.
>

## os.**TFD_NONBLOCK**

​	一个用于 [`timerfd_create()`]({{< ref "/library/allos/os#os.timerfd_create" >}}) 函数的旗标，它将为新的计时器文件描述符设置 [`O_NONBLOCK`]({{< ref "/library/allos/os#os.O_NONBLOCK" >}}) 状态旗标。 如果未将 [`TFD_NONBLOCK`]({{< ref "/library/allos/os#os.TFD_NONBLOCK" >}}) 设为旗标，[`read()`]({{< ref "/library/allos/os#os.read" >}}) 将会阻塞。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.27 with glibc >= 2.8

> Added in version 3.13.
>

## os.**TFD_CLOEXEC**

​	一个用于 [`timerfd_create()`]({{< ref "/library/allos/os#os.timerfd_create" >}}) 函数的旗标，如果将 [`TFD_CLOEXEC`]({{< ref "/library/allos/os#os.TFD_CLOEXEC" >}}) 设为旗标，将为新的文件描述符设置 close-on-exec 旗标。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.27 with glibc >= 2.8

> Added in version 3.13.
>

## os.**TFD_TIMER_ABSTIME**

​	一个用于 [`timerfd_settime()`]({{< ref "/library/allos/os#os.timerfd_settime" >}}) 和 [`timerfd_settime_ns()`]({{< ref "/library/allos/os#os.timerfd_settime_ns" >}}) 函数的旗标。 如果设置了此旗标，*initial* 将被解读为计时器时钟上的一个绝对数值（即自 Unix 纪元开始的 UTC 秒数或纳秒数）。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.27 with glibc >= 2.8

> Added in version 3.13.
>

## os.**TFD_TIMER_CANCEL_ON_SET**

​	一个配合 [`TFD_TIMER_ABSTIME`]({{< ref "/library/allos/os#os.TFD_TIMER_ABSTIME" >}}) 用于 [`timerfd_settime()`]({{< ref "/library/allos/os#os.timerfd_settime" >}}) 和 [`timerfd_settime_ns()`]({{< ref "/library/allos/os#os.timerfd_settime_ns" >}}) 函数的旗标。 当下层时钟发生不连续改变时计时器将被取消。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.27 with glibc >= 2.8

> Added in version 3.13.
>

### Linux 扩展属性

> Added in version 3.3.
>

​	这些函数仅在 Linux 上可用。

## os.**getxattr**(*path*, *attribute*, ***, *follow_symlinks=True*)

​	返回 *path* 的扩展文件系统属性 *attribute* 的值。*attribute* 可以是 bytes 或 str （直接传入或通过 [`PathLike`]({{< ref "/library/allos/os#os.PathLike" >}}) 接口间接传入）。如果是 str，则使用文件系统编码来编码字符串。

​	本函数支持 [指定文件描述符为参数]({{< ref "/library/allos/os#path-fd" >}}) 和 [不跟踪符号链接]({{< ref "/library/allos/os#follow-symlinks" >}})。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.getxattr` 并附带参数 `path`, `attribute`。

*在 3.6 版本发生变更:* 接受一个 [类路径对象]({{< ref "/glossary/idx#term-path-like-object" >}}) 作为 *path* 和 *attribute*。

## os.**listxattr**(*path=None*, ***, *follow_symlinks=True*)

​	返回一个列表，包含 *path* 的所有扩展文件系统属性。列表中的属性都表示为字符串，它们是根据文件系统编码解码出来的。如果 *path* 为 `None`，则 [`listxattr()`]({{< ref "/library/allos/os#os.listxattr" >}}) 将检查当前目录。

​	本函数支持 [指定文件描述符为参数]({{< ref "/library/allos/os#path-fd" >}}) 和 [不跟踪符号链接]({{< ref "/library/allos/os#follow-symlinks" >}})。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.listxattr` 并附带参数 `path`。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## os.**removexattr**(*path*, *attribute*, ***, *follow_symlinks=True*)

​	移除 *path* 中的扩展文件系统属性 *attribute*。 *attribute* 应为字节串或字符串类型（通过 [`PathLike`]({{< ref "/library/allos/os#os.PathLike" >}}) 接口直接或间接得到）。 若为字符串类型，则用 [filesystem encoding and error handler]({{< ref "/glossary/idx#term-filesystem-encoding-and-error-handler" >}}) 进行编码。

​	本函数支持 [指定文件描述符为参数]({{< ref "/library/allos/os#path-fd" >}}) 和 [不跟踪符号链接]({{< ref "/library/allos/os#follow-symlinks" >}})。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.removexattr` 并附带参数 `path`, `attribute`。

*在 3.6 版本发生变更:* 接受一个 [类路径对象]({{< ref "/glossary/idx#term-path-like-object" >}}) 作为 *path* 和 *attribute*。

## os.**setxattr**(*path*, *attribute*, *value*, *flags=0*, ***, *follow_symlinks=True*)

​	将 *path* 的文件系统扩展属性 *attribute* 设为 *value*。 *attribute* 必须是一个字节串或字符串，不含 NUL（通过 [`PathLike`]({{< ref "/library/allos/os#os.PathLike" >}}) 接口直接或间接得到）。 若为字符串，将用 [filesystem encoding and error handler]({{< ref "/glossary/idx#term-filesystem-encoding-and-error-handler" >}}) 进行编码。 *flags* 可以是 [`XATTR_REPLACE`]({{< ref "/library/allos/os#os.XATTR_REPLACE" >}}) 或 [`XATTR_CREATE`]({{< ref "/library/allos/os#os.XATTR_CREATE" >}})。 如果给出 [`XATTR_REPLACE`]({{< ref "/library/allos/os#os.XATTR_REPLACE" >}}) 而属性不存在，则会触发 `ENODATA`。 如果给出了 [`XATTR_CREATE`]({{< ref "/library/allos/os#os.XATTR_CREATE" >}}) 而属性已存在，则不会创建属性并将触发 `EEXISTS`。

​	本函数支持 [指定文件描述符为参数]({{< ref "/library/allos/os#path-fd" >}}) 和 [不跟踪符号链接]({{< ref "/library/allos/os#follow-symlinks" >}})。

​	备注

 

​	Linux kernel 2.6.39 以下版本的一个 bug 导致在某些文件系统上，flags 参数会被忽略。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.setxattr` 并附带参数 `path`, `attribute`, `value`, `flags`。

*在 3.6 版本发生变更:* 接受一个 [类路径对象]({{< ref "/glossary/idx#term-path-like-object" >}}) 作为 *path* 和 *attribute*。

## os.**XATTR_SIZE_MAX**

​	一条扩展属性的值的最大大小。在当前的 Linux 上是 64 KiB。

## os.**XATTR_CREATE**

​	这是 [`setxattr()`]({{< ref "/library/allos/os#os.setxattr" >}}) 的 flags 参数的可取值，它表示该操作必须创建一个属性。

## os.**XATTR_REPLACE**

​	这是 [`setxattr()`]({{< ref "/library/allos/os#os.setxattr" >}}) 的 flags 参数的可取值，它表示该操作必须替换现有属性。



## 进程管理

​	下列函数可用于创建和管理进程。

​	所有 [`exec*`]({{< ref "/library/allos/os#os.execl" >}}) 函数都接受一个参数列表，用来给新程序加载到它的进程中。在所有情况下，传递给新程序的第一个参数是程序本身的名称，而不是用户在命令行上输入的参数。对于 C 程序员来说，这就是传递给 `main()` 函数的 `argv[0]`。例如，`os.execv('/bin/echo', ['foo', 'bar'])` 只会在标准输出上打印 `bar`，而 `foo` 会被忽略。

## os.**abort**()

​	发送 `SIGABRT` 信号到当前进程。在 Unix 上，默认行为是生成一个核心转储。在 Windows 上，该进程立即返回退出代码 `3`。请注意，使用 [`signal.signal()`]({{< ref "/library/ipc/signal#signal.signal" >}}) 可以为 `SIGABRT` 注册 Python 信号处理程序，而调用本函数将不会调用按前述方法注册的程序。

## os.**add_dll_directory**(*path*)

​	将路径添加到 DLL 搜索路径。

​	当需要解析扩展模块的依赖时（扩展模块本身通过 [`sys.path`]({{< ref "/library/python/sys#sys.path" >}}) 解析），会使用该搜索路径，[`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 也会使用该搜索路径。

​	要移除目录，可以在返回的对象上调用 **close()**，也可以在 [`with`]({{< ref "/reference/compound_stmts#with" >}}) 语句内使用本方法。

​	参阅 [Microsoft 文档](https://msdn.microsoft.com/44228cf2-6306-466c-8f16-f513cd3ba8b5) 获取如何加载 DLL 的信息。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.add_dll_directory` 并附带参数 `path`。

[Availability]({{< ref "/library/intro#availability" >}}): Windows.

> Added in version 3.8:
> 早期版本的 CPython 解析 DLL 时用的是当前进程的默认行为。这会导致不一致，比如不是每次都会去搜索 `PATH` 和当前工作目录，且系统函数（如 `AddDllDirectory` ）失效。

​	在 3.8 中，DLL 的两种主要加载方式现在可以显式覆盖进程的行为，以确保一致性。请参阅 [移植说明](https://docs.python.org/zh-cn/3.13/whatsnew/3.8.html#bpo-36085-whatsnew) 了解如何更新你的库。

## os.**execl**(*path*, *arg0*, *arg1*, *...*)

## os.**execle**(*path*, *arg0*, *arg1*, *...*, *env*)

## os.**execlp**(*file*, *arg0*, *arg1*, *...*)

## os.**execlpe**(*file*, *arg0*, *arg1*, *...*, *env*)

## os.**execv**(*path*, *args*)

## os.**execve**(*path*, *args*, *env*)

## os.**execvp**(*file*, *args*)

## os.**execvpe**(*file*, *args*, *env*)

​	这些函数都将执行一个新程序，以替换当前进程。它们没有返回值。在 Unix 上，新程序会加载到当前进程中，且进程号与调用者相同。过程中的错误会被报告为 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 异常。

​	当前进程会被立即替换。打开的文件对象和描述符都不会刷新，因此如果这些文件上可能缓冲了数据，则应在调用 [`exec*`]({{< ref "/library/allos/os#os.execl" >}}) 函数之前使用 `sys.stdout.flush()` 或 [`os.fsync()`]({{< ref "/library/allos/os#os.fsync" >}}) 刷新它们。

[`exec*`]({{< ref "/library/allos/os#os.execl" >}}) 函数的 "l" 和 "v" 变体的不同在于命令行参数的传递方式。 如果在编写代码时形参数量是固定的，则 "l" 变体可能是最方便的；单个形参简单地作为传给 `execl*()` 函数的额外形参即可。 当形参数量可变时则 "v" 变体更为好用，参数将以列表或元组的形式作为 *args* 形参传入。 在这两种情况下，传给子进程的参数应当以要运行的命令名称开头，但这不是强制性的。

​	在结尾位置包括 "p" 的变体形式 ([`execlp()`]({{< ref "/library/allos/os#os.execlp" >}}), [`execlpe()`]({{< ref "/library/allos/os#os.execlpe" >}}), [`execvp()`]({{< ref "/library/allos/os#os.execvp" >}}) 和 [`execvpe()`]({{< ref "/library/allos/os#os.execvpe" >}})) 将使用 `PATH` 环境变量来定位程序 *file*。 当环境被替换时 (使用某个 [`exec*e`]({{< ref "/library/allos/os#os.execl" >}}) 变体形式，将在下一段中讨论)，将使用新环境作为 `PATH` 变量的来源。 其他的变体形式 [`execl()`]({{< ref "/library/allos/os#os.execl" >}}), [`execle()`]({{< ref "/library/allos/os#os.execle" >}}), [`execv()`]({{< ref "/library/allos/os#os.execv" >}}) 和 [`execve()`]({{< ref "/library/allos/os#os.execve" >}}) 将不使用 `PATH` 变量来定位可执行程序；*path* 必须包含正确的绝对或相对路径。 相对路径必须包括至少一个斜杠，即使是在 Windows 上，因为简单名称将不会被解析。

​	对于 [`execle()`]({{< ref "/library/allos/os#os.execle" >}})、[`execlpe()`]({{< ref "/library/allos/os#os.execlpe" >}})、[`execve()`]({{< ref "/library/allos/os#os.execve" >}}) 和 [`execvpe()`]({{< ref "/library/allos/os#os.execvpe" >}}) （都以 "e" 结尾），*env* 参数是一个映射，用于定义新进程的环境变量（代替当前进程的环境变量）。而函数 [`execl()`]({{< ref "/library/allos/os#os.execl" >}})、[`execlp()`]({{< ref "/library/allos/os#os.execlp" >}})、[`execv()`]({{< ref "/library/allos/os#os.execv" >}}) 和 [`execvp()`]({{< ref "/library/allos/os#os.execvp" >}}) 会将当前进程的环境变量过继给新进程。

​	某些平台上的 [`execve()`]({{< ref "/library/allos/os#os.execve" >}}) 可以将 *path* 指定为打开的文件描述符。当前平台可能不支持此功能，可以使用 [`os.supports_fd`]({{< ref "/library/allos/os#os.supports_fd" >}}) 检查它是否支持。如果不可用，则使用它会抛出 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}}) 异常。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.exec` 并附带参数 `path`, `args`, `env`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows, not WASI, not Android, not iOS.

*在 3.3 版本发生变更:* 新增支持将 [`execve()`]({{< ref "/library/allos/os#os.execve" >}}) 的 *path* 参数指定为打开的文件描述符。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## os.**_exit**(*n*)

​	以状态码 *n* 退出进程，不会调用清理处理程序，不会刷新 stdio，等等。

​	备注

 

​	退出的标准方式是使用 [`sys.exit(n)`]({{< ref "/library/python/sys#sys.exit" >}})。 `_exit()` 通常只应在 [`fork()`]({{< ref "/library/allos/os#os.fork" >}}) 所生成的子进程中使用。

​	以下是已定义的退出代码，可以用于 [`_exit()`]({{< ref "/library/allos/os#os._exit" >}})，尽管它们不是必需的。这些退出代码通常用于 Python 编写的系统程序，例如邮件服务器的外部命令传递程序。

​	备注

 

​	其中部分退出代码在部分 Unix 平台上可能不可用，因为平台间存在差异。如果底层平台定义了这些常量，那上层也会定义。

## os.**EX_OK**

​	表示没有发生错误的退出码。 在某些平台上可能会从 `EXIT_SUCCESS` 定义的值中选取。 通常其值为零。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows.

## os.**EX_USAGE**

​	退出代码，表示命令使用不正确，如给出的参数数量有误。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**EX_DATAERR**

​	退出代码，表示输入数据不正确。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**EX_NOINPUT**

​	退出代码，表示某个输入文件不存在或不可读。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**EX_NOUSER**

​	退出代码，表示指定的用户不存在。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**EX_NOHOST**

​	退出代码，表示指定的主机不存在。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**EX_UNAVAILABLE**

​	退出代码，表示所需的服务不可用。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**EX_SOFTWARE**

​	退出代码，表示检测到内部软件错误。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**EX_OSERR**

​	退出代码，表示检测到操作系统错误，例如无法 fork 或创建管道。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**EX_OSFILE**

​	退出代码，表示某些系统文件不存在、无法打开或发生其他错误。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**EX_CANTCREAT**

​	退出代码，表示无法创建用户指定的输出文件。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**EX_IOERR**

​	退出代码，表示对某些文件进行读写时发生错误。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**EX_TEMPFAIL**

​	退出代码，表示发生了暂时性故障。它可能并非意味着真正的错误，例如在可重试的情况下无法建立网络连接。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**EX_PROTOCOL**

​	退出代码，表示协议交换是非法的、无效的或无法解读的。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**EX_NOPERM**

​	退出代码，表示没有足够的权限执行该操作（但不适用于文件系统问题）。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**EX_CONFIG**

​	退出代码，表示发生某种配置错误。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**EX_NOTFOUND**

​	退出代码，表示的内容类似于“找不到条目”。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**fork**()

​	Fork 出一个子进程。在子进程中返回 `0`，在父进程中返回子进程的进程号。如果发生错误，则抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 异常。

​	注意，当从线程中使用 `fork()` 时，某些平台（包括 FreeBSD <= 6.3 和 Cygwin）存在已知问题。

​	引发一个不带参数的 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.fork`。

​	警告

 

​	如果你在调用``fork()`` 的应用程序中使用 TLS 套接字，请参阅 [`ssl`]({{< ref "/library/ipc/ssl#module-ssl" >}}) 文档中的警告信息。

​	警告

 

​	在 macOS 上将此函数与高层级的系统 API 混用是不安全的，包括 [`urllib.request`]({{< ref "/library/internet/urllib/urllib_request#module-urllib.request" >}})。

*在 3.8 版本发生变更:* 不再支持在子解释器中调用 `fork()` （将抛出 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}}) 异常）。

*在 3.12 版本发生变更:* 如果 Python 能够检测到你的进程有多个线程，则 [`os.fork()`]({{< ref "/library/allos/os#os.fork" >}}) 现在会引发 [`DeprecationWarning`]({{< ref "/library/exceptions#DeprecationWarning" >}})。

​	在可以检测时，我们选择将此显示为警告，以便更好地告知开发人员 POSIX 平台明确指出不支持的设计问题。 在 POSIX 平台上即使在 *看起来* 可行的代码中，将线程与 [`os.fork()`]({{< ref "/library/allos/os#os.fork" >}}) 混用也是不安全的。 当父进程中存在线程时 CPython 运行时本身总是会在子进程中执行不安全的 API 调用 (如 `malloc` 和 `free`)。

​	使用 macOS 的用户或使用 glibc 中可找到的典型实现以外的 libc 或 malloc 实现的用户在运行此类代码时更容易发生死锁现象。

​	请参阅 [有关 fork 与线程不兼容的讨论](https://discuss.python.org/t/33555) 了解我们为何向开发者公开这个长期存在的平台不兼容性问题的技术细节。

[Availability]({{< ref "/library/intro#availability" >}}): POSIX, not WASI, not Android, not iOS.

## os.**forkpty**()

​	Fork 出一个子进程，使用新的伪终端作为子进程的控制终端。返回一对 `(pid, fd)`，其中 *pid* 在子进程中为 `0`，这是父进程中新子进程的进程号，而 *fd* 是伪终端主设备的文件描述符。对于更便于移植的方法，请使用 [`pty`]({{< ref "/library/unix/pty#module-pty" >}}) 模块。如果发生错误，则抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 异常。

​	引发一个不带参数的 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.forkpty`。

​	警告

 

​	在 macOS 上将此函数与高层级的系统 API 混用是不安全的，包括 [`urllib.request`]({{< ref "/library/internet/urllib/urllib_request#module-urllib.request" >}})。

*在 3.8 版本发生变更:* 不再支持在子解释器中调用 `forkpty()` （将抛出 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}}) 异常）。

*在 3.12 版本发生变更:* 现在如果 Python 能够检测到你的进程有多个线程，此函数将引发 [`DeprecationWarning`]({{< ref "/library/exceptions#DeprecationWarning" >}})。 请参阅有关 [`os.fork()`]({{< ref "/library/allos/os#os.fork" >}}) 的更详细解释。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

## os.**kill**(*pid*, *sig*, */*)

​	将信号 *sig* 发送至进程 *pid*。特定平台上可用的信号常量定义在 [`signal`]({{< ref "/library/ipc/signal#module-signal" >}}) 模块中。

​	Windows: The [`signal.CTRL_C_EVENT`]({{< ref "/library/ipc/signal#signal.CTRL_C_EVENT" >}}) and [`signal.CTRL_BREAK_EVENT`]({{< ref "/library/ipc/signal#signal.CTRL_BREAK_EVENT" >}}) signals are special signals which can only be sent to console processes which share a common console window, e.g., some subprocesses. Any other value for *sig* will cause the process to be unconditionally killed by the TerminateProcess API, and the exit code will be set to *sig*.

​	另请参阅 [`signal.pthread_kill()`]({{< ref "/library/ipc/signal#signal.pthread_kill" >}})。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.kill` 并附带参数 `pid`, `sig`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows, not WASI, not iOS.

*在 3.2 版本发生变更:* 添加了对 Windows 的支持。

## os.**killpg**(*pgid*, *sig*, */*)

​	将信号 *sig* 发送给进程组 *pgid*。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.killpg` 并附带参数 `pgid`, `sig`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not iOS.

## os.**nice**(*increment*, */*)

​	将进程的优先级（nice 值）增加 *increment*，返回新的 nice 值。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

## os.**pidfd_open**(*pid*, *flags=0*)

​	返回一个指向设置了 *flags* 的进程 *pid* 的文件描述符。 该描述符可用于执行无需竞争和信号的进程管理。

​	更多详细信息请参阅 *[pidfd_open(2)](https://manpages.debian.org/pidfd_open(2))* 手册页。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 5.3, Android >= [`build-time`]({{< ref "/library/python/sys#sys.getandroidapilevel" >}}) API level 31

> Added in version 3.9.
>

## os.**PIDFD_NONBLOCK**

​	该旗标表示文件描述符将是非阻塞的。 如果文件描述符所引用的进程尚未终止，那么尝试使用 *[waitid(2)](https://manpages.debian.org/waitid(2))* 等待文件描述符将立即返回错误 [`EAGAIN`]({{< ref "/library/allos/errno#errno.EAGAIN" >}}) 而不是阻塞。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 5.10

> Added in version 3.12.
>

## os.**plock**(*op*, */*)

​	将程序段锁定到内存中。*op* 的值（定义在 `<sys/lock.h>` 中）决定了哪些段被锁定。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not iOS.

## os.**popen**(*cmd*, *mode='r'*, *buffering=-1*)

​	打开一个通往或接受命令 *cmd* 的管道。 返回值是连接到该管道的已打开文件对象，它可读取还是可写入取决于其 *mode* 是 `'r'` (默认) 还是 `'w'`。 *buffering* 参数与内置 [`open()`]({{< ref "/library/functions#open" >}}) 函数相应的参数含义相同。 返回的文件对象只能读写文本字符串而不是字节串。

​	如果子进程成功退出，则 `close` 方法返回 [`None`]({{< ref "/library/constants#None" >}})。如果发生错误，则返回子进程的返回码。在 POSIX 系统上，如果返回码为正，则它就是进程返回值左移一个字节后的值。如果返回码为负，则进程是被信号终止的，返回码取反后就是该信号。（例如，如果子进程被终止，则返回值可能是 `- signal.SIGKILL`。）在 Windows 系统上，返回值包含子进程的返回码（有符号整数）。

​	在 Unix 上，[`waitstatus_to_exitcode()`]({{< ref "/library/allos/os#os.waitstatus_to_exitcode" >}}) 可以将 `close` 方法的返回值（即退出状态，不能是 `None`）转换为退出码。在 Windows 上，`close` 方法的结果直接就是退出码（或 `None` ）。

​	本方法是使用 [`subprocess.Popen`]({{< ref "/library/concurrency/subprocess#subprocess.Popen" >}}) 实现的，如需更强大的方法来管理和沟通子进程，请参阅该类的文档。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI, not Android, not iOS.

​	备注

 

[Python UTF-8 模型]({{< ref "/library/allos/os#utf8-mode" >}}) 影响 *cmd* 和管道内容所使用的编码格式。

[`popen()`]({{< ref "/library/allos/os#os.popen" >}}) 是针对 [`subprocess.Popen`]({{< ref "/library/concurrency/subprocess#subprocess.Popen" >}}) 的简单包装器。 请使用 [`subprocess.Popen`]({{< ref "/library/concurrency/subprocess#subprocess.Popen" >}}) 或 [`subprocess.run()`]({{< ref "/library/concurrency/subprocess#subprocess.run" >}}) 来控制编码格式等选项。

## os.**posix_spawn**(*path*, *argv*, *env*, ***, *file_actions=None*, *setpgroup=None*, *resetids=False*, *setsid=False*, *setsigmask=()*, *setsigdef=()*, *scheduler=None*)

​	包装 `posix_spawn()` C 库 API 以供 Python 使用。

​	大多数用户应使用 [`subprocess.run()`]({{< ref "/library/concurrency/subprocess#subprocess.run" >}}) 代替 [`posix_spawn()`]({{< ref "/library/allos/os#os.posix_spawn" >}})。

​	位置参数 *path*, *args* 和 *env* 与 [`execve()`]({{< ref "/library/allos/os#os.execve" >}}) 类似。 *env* 允许为 `None`，在此情况下将使用当前进程的环境。

*path* 形参是可执行文件的路径，*path* 中应当包含目录。 使用 [`posix_spawnp()`]({{< ref "/library/allos/os#os.posix_spawnp" >}}) 可传入不带目录的可执行文件。

*file_actions* 参数可以是由元组组成的序列，序列描述了对子进程中指定文件描述符采取的操作，这些操作会在 C 库实现的 `fork()` 和 `exec()` 步骤间完成。每个元组的第一个元素必须是下面列出的三个类型指示符之一，用于描述元组剩余的元素：

## os.**POSIX_SPAWN_OPEN**

​	(`os.POSIX_SPAWN_OPEN`, *fd*, *path*, *flags*, *mode*)

​	执行 `os.dup2(os.open(path, flags, mode), fd)`。

## os.**POSIX_SPAWN_CLOSE**

​	(`os.POSIX_SPAWN_CLOSE`, *fd*)

​	执行 `os.close(fd)`。

## os.**POSIX_SPAWN_DUP2**

​	(`os.POSIX_SPAWN_DUP2`, *fd*, *new_fd*)

​	执行 `os.dup2(fd, new_fd)`。

## os.**POSIX_SPAWN_CLOSEFROM**

​	(`os.POSIX_SPAWN_CLOSEFROM`, *fd*)

​	执行 `os.closerange(fd, INF)` 。

​	这些元组对应于 C 库 `posix_spawn_file_actions_addopen()`, `posix_spawn_file_actions_addclose()`, `posix_spawn_file_actions_adddup2()` 和 `posix_spawn_file_actions_addclosefrom_np()` API 调用，用于为 `posix_spawn()` 调用本身做准备。

*setpgroup* 参数将把子进程的进程组设置为指定值。 如果指定值为 0，则子进程的进程组 ID 将与其进程 ID 相同。 如果未设置 *setpgroup* 的值，则子进程将继承父进程的进程组 ID。 本参数对应于 C 库的 `POSIX_SPAWN_SETPGROUP` 旗标。

​	如果 *resetids* 参数为 `True` 则它会将子进程的有效 UID 和 GID 重置为父进程的实际 UID 和 GID。 如果该参数为 `False`，则子进程会保留父进程的有效 UID 和 GID。 无论哪种情况，如果在可执行文件上启用了设置用户 ID 和设置组 ID 权限位，它们的效果将覆盖有效 UID 和 GID 的设置。 本参数对应于 C 库的 `POSIX_SPAWN_RESETIDS` 旗标。

​	如果 *setsid* 参数为 `True`，它将为 `posix_spawn` 新建一个会话 ID。 *setsid* 需要 `POSIX_SPAWN_SETSID` 或 `POSIX_SPAWN_SETSID_NP` 旗标。 否则，将会引发 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}})。

*setsigmask* 参数会将信号掩码设置为指定的信号集合。 如果未使用该参数，则子进程将继承父进程的信号掩码。 本参数对应于 C 库的 `POSIX_SPAWN_SETSIGMASK` 旗标。

*sigdef* 参数会将集合中所有信号的操作全部重置为默认。 本参数对应于 C 库的 `POSIX_SPAWN_SETSIGDEF` 旗标。

*scheduler* 参数必须是一个元组，其中包含（可选的）调度器策略以及携带了调度器参数的 [`sched_param`]({{< ref "/library/allos/os#os.sched_param" >}}) 实例。 在调度器策略所在位置的值为 `None` 表示未提供该值。 本参数是 C 库的 `POSIX_SPAWN_SETSCHEDPARAM` 和 `POSIX_SPAWN_SETSCHEDULER` 旗标的组合。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.posix_spawn` 并附带参数 `path`, `argv`, `env`。

> Added in version 3.8.
>

*在 3.13 版本发生变更:* *env* 形参可接受 `None`。 `os.POSIX_SPAWN_CLOSEFROM` 在具有 `posix_spawn_file_actions_addclosefrom_np()` 的平台上可用。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

## os.**posix_spawnp**(*path*, *argv*, *env*, ***, *file_actions=None*, *setpgroup=None*, *resetids=False*, *setsid=False*, *setsigmask=()*, *setsigdef=()*, *scheduler=None*)

​	包装 `posix_spawnp()` C 库 API 以供 Python 使用。

​	与 [`posix_spawn()`]({{< ref "/library/allos/os#os.posix_spawn" >}}) 相似，但是系统会在 `PATH` 环境变量指定的目录列表中搜索可执行文件 *executable* （与 `execvp(3)` 相同）。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.posix_spawn` 并附带参数 `path`, `argv`, `env`。

> Added in version 3.8.
>

[Availability]({{< ref "/library/intro#availability" >}}): POSIX, not WASI, not Android, not iOS.

​	参见 [`posix_spawn()`]({{< ref "/library/allos/os#os.posix_spawn" >}}) 文档。

## os.**register_at_fork**(***, *before=None*, *after_in_parent=None*, *after_in_child=None*)

​	注册可调用对象，在使用 [`os.fork()`]({{< ref "/library/allos/os#os.fork" >}}) 或类似的进程克隆 API 派生新的子进程时，这些对象会运行。参数是可选的，且为仅关键字 (Keyword-only) 参数。每个参数指定一个不同的调用点。

- *before* 是一个函数，在 fork 子进程前调用。
- *after_in_parent* 是一个函数，在 fork 子进程后从父进程调用。
- *after_in_child* 是一个函数，从子进程中调用。

​	只有希望控制权回到 Python 解释器时，才进行这些调用。典型的 [`子进程`]({{< ref "/library/concurrency/subprocess#module-subprocess" >}}) 启动时不会触发它们，因为子进程不会重新进入解释器。

​	在注册的函数中，用于 fork 前运行的函数将按与注册相反的顺序调用。用于 fork 后（从父进程或子进程）运行的函数按注册顺序调用。

​	注意，第三方 C 代码的 `fork()` 调用可能不会调用这些函数，除非它显式调用了 [`PyOS_BeforeFork()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PyOS_BeforeFork)、[`PyOS_AfterFork_Parent()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PyOS_AfterFork_Parent) 和 [`PyOS_AfterFork_Child()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PyOS_AfterFork_Child)。

​	函数注册后无法注销。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

> Added in version 3.7.
>

## os.**spawnl**(*mode*, *path*, *...*)

## os.**spawnle**(*mode*, *path*, *...*, *env*)

## os.**spawnlp**(*mode*, *file*, *...*)

## os.**spawnlpe**(*mode*, *file*, *...*, *env*)

## os.**spawnv**(*mode*, *path*, *args*)

## os.**spawnve**(*mode*, *path*, *args*, *env*)

## os.**spawnvp**(*mode*, *file*, *args*)

## os.**spawnvpe**(*mode*, *file*, *args*, *env*)

​	在新进程中执行程序 *path*。

​	（注意，[`subprocess`]({{< ref "/library/concurrency/subprocess#module-subprocess" >}}) 模块提供了更强大的工具来生成新进程并跟踪执行结果，使用该模块比使用这些函数更好。尤其应当检查 [使用 subprocess 模块替换旧函数]({{< ref "/library/concurrency/subprocess#subprocess-replacements" >}}) 部分。）

*mode* 为 [`P_NOWAIT`]({{< ref "/library/allos/os#os.P_NOWAIT" >}}) 时，本函数返回新进程的进程号。*mode* 为 [`P_WAIT`]({{< ref "/library/allos/os#os.P_WAIT" >}}) 时，如果进程正常退出，返回退出代码，如果被终止，返回 `-signal`，其中 *signal* 是终止进程的信号。在 Windows 上，进程号实际上是进程句柄，因此可以与 [`waitpid()`]({{< ref "/library/allos/os#os.waitpid" >}}) 函数一起使用。

​	注意在 VxWorks 上，新进程被终止时，本函数不会返回 `-signal`，而是会抛出 OSError 异常。

[`spawn*`]({{< ref "/library/allos/os#os.spawnl" >}}) 函数的 "l" 和 "v" 变体的不同在于命令行参数的传递方式。 如果在编写代码时形参数量是固定的，则 "l" 变体可能是最方便的；单个形参简单地作为传给 `spawnl*()` 函数的额外形参即可。 当形参数量可变时 "v" 变体更为好用，参数将以列表或元组的形式作为 *args* 形参传入。 在这两种情况下，传给子进程的参数应当以要运行的命令名称开头。

​	结尾包含第二个 "p" 的变体（[`spawnlp()`]({{< ref "/library/allos/os#os.spawnlp" >}})、[`spawnlpe()`]({{< ref "/library/allos/os#os.spawnlpe" >}})、[`spawnvp()`]({{< ref "/library/allos/os#os.spawnvp" >}}) 和 [`spawnvpe()`]({{< ref "/library/allos/os#os.spawnvpe" >}})）将使用 `PATH` 环境变量来查找程序 *file*。当环境被替换时（使用下一段讨论的 [`spawn*e`]({{< ref "/library/allos/os#os.spawnl" >}}) 变体之一），`PATH` 变量将来自于新环境。其他变体 [`spawnl()`]({{< ref "/library/allos/os#os.spawnl" >}})、[`spawnle()`]({{< ref "/library/allos/os#os.spawnle" >}})、[`spawnv()`]({{< ref "/library/allos/os#os.spawnv" >}}) 和 [`spawnve()`]({{< ref "/library/allos/os#os.spawnve" >}}) 不使用 `PATH` 变量来查找程序，因此 *path* 必须包含正确的绝对或相对路径。

​	对于 [`spawnle()`]({{< ref "/library/allos/os#os.spawnle" >}})、[`spawnlpe()`]({{< ref "/library/allos/os#os.spawnlpe" >}})、[`spawnve()`]({{< ref "/library/allos/os#os.spawnve" >}}) 和 [`spawnvpe()`]({{< ref "/library/allos/os#os.spawnvpe" >}}) （都以 "e" 结尾），*env* 参数是一个映射，用于定义新进程的环境变量（代替当前进程的环境变量）。而函数 [`spawnl()`]({{< ref "/library/allos/os#os.spawnl" >}})、[`spawnlp()`]({{< ref "/library/allos/os#os.spawnlp" >}})、[`spawnv()`]({{< ref "/library/allos/os#os.spawnv" >}}) 和 [`spawnvp()`]({{< ref "/library/allos/os#os.spawnvp" >}}) 会将当前进程的环境变量过继给新进程。注意，*env* 字典中的键和值必须是字符串。无效的键或值将导致函数出错，返回值为 `127`。

​	例如，以下对 [`spawnlp()`]({{< ref "/library/allos/os#os.spawnlp" >}}) 和 [`spawnvpe()`]({{< ref "/library/allos/os#os.spawnvpe" >}}) 的调用是等效的:

```
import os
os.spawnlp(os.P_WAIT, 'cp', 'cp', 'index.html', '/dev/null')

L = ['cp', 'index.html', '/dev/null']
os.spawnvpe(os.P_WAIT, 'cp', L, os.environ)
```

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.spawn` 并附带参数 `mode`, `path`, `args`, `env`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows, not WASI, not Android, not iOS.

[`spawnlp()`]({{< ref "/library/allos/os#os.spawnlp" >}}), [`spawnlpe()`]({{< ref "/library/allos/os#os.spawnlpe" >}}), [`spawnvp()`]({{< ref "/library/allos/os#os.spawnvp" >}}) 和 [`spawnvpe()`]({{< ref "/library/allos/os#os.spawnvpe" >}}) 在 Windows 上不可用。 [`spawnle()`]({{< ref "/library/allos/os#os.spawnle" >}}) 和 [`spawnve()`]({{< ref "/library/allos/os#os.spawnve" >}}) 在 Windows 上不是线程安全的；我们建议你用 [`subprocess`]({{< ref "/library/concurrency/subprocess#module-subprocess" >}}) 模块来代替。

*在 3.6 版本发生变更:* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## os.**P_NOWAIT**

## os.**P_NOWAITO**

​	传给 [`spawn*`]({{< ref "/library/allos/os#os.spawnl" >}}) 函数族的 *mode* 形参可能的值。 如果给出这些值中的某一个，则 [`spawn*`]({{< ref "/library/allos/os#os.spawnl" >}}) 函数将在新进程创建后立即返回，并将进程 ID 作为返回值。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows.

## os.**P_WAIT**

​	传给 [`spawn*`]({{< ref "/library/allos/os#os.spawnl" >}}) 函数族的 *mode* 形参可能的值。 如果作为 *mode* 给出，则 [`spawn*`]({{< ref "/library/allos/os#os.spawnl" >}}) 函数将在新进程运行完毕后才返回，并在进程运行成功时返回退出码，或者如果进程被信号杀掉则返回 `-signal`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows.

## os.**P_DETACH**

## os.**P_OVERLAY**

[`spawn*`]({{< ref "/library/allos/os#os.spawnl" >}}) 系列函数的 *mode* 参数的可取值。它们比上面列出的值可移植性差。[`P_DETACH`]({{< ref "/library/allos/os#os.P_DETACH" >}}) 与 [`P_NOWAIT`]({{< ref "/library/allos/os#os.P_NOWAIT" >}}) 相似，但是新进程会与父进程的控制台脱离。使用 [`P_OVERLAY`]({{< ref "/library/allos/os#os.P_OVERLAY" >}}) 则会替换当前进程，[`spawn*`]({{< ref "/library/allos/os#os.spawnl" >}}) 函数将不会返回。

[Availability]({{< ref "/library/intro#availability" >}}): Windows.

## os.**startfile**(*path*[, *operation*][, *arguments*][, *cwd*][, *show_cmd*])

​	使用已关联的应用程序打开文件。

​	当未指定 *operation* 时，这类似于在 Windows 资源管理器中双击文件，或在交互式命令行中将文件名作为 **start** 命令的参数：通过扩展名所关联的应用程序（如果有的话）来打开文件。

​	当指定其他 *operation* 时，它必须是一个对该文件执行操作的“命令动词”。 Microsoft 文档中记录的常用动词有 `'open'`, `'print'` 和 `'edit'` (用于文件) 以及 `'explore'` 和 `'find'` (用于目录)。

​	在启动某个应用程序时，*arguments* 将作为一个字符串传入。若是打开某个文档，此参数可能没什么效果。

​	默认工作目录是继承而来的，但可以通过 *cwd* 参数进行覆盖。且应为绝对路径。相对路径 *path* 将据此参数进行解析。

​	使用 *show_cmd* 覆盖默认的窗口样式。 这是否生效则取决于被启动的应用程序。 其值应为 Win32 `ShellExecute()` 函数所支持的整数形式。

​	在关联的应用程序启动后，[`startfile()`]({{< ref "/library/allos/os#os.startfile" >}}) 就会立即返回。 没有提供等待应用程序关闭的选项，也没有办法获得应用程序的退出状态。 *path* 形参是基于当前目录或 *cwd* 的相对路径。 如果要使用绝对路径，请确保第一个字符不为斜杠 (`'/'`) 。 请用 [`pathlib`]({{< ref "/library/filesys/pathlib#module-pathlib" >}}) 或 [`os.path.normpath()`]({{< ref "/library/filesys/os_path#os.path.normpath" >}}) 函数来保证路径已按照 Win32 的要求进行了正确的编码。

​	为了减少解释器的启动开销，Win32 `ShellExecute()` 函数将不会被解析直到该函数首次被调用。 如果该函数无法被解析，则将引发 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}}) 异常。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.startfile` 并附带参数 `path`, `operation`。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.startfile/2` 并附带参数 `path`, `operation`, `arguments`, `cwd`, `show_cmd`。

[Availability]({{< ref "/library/intro#availability" >}}): Windows.

*在 3.10 版本发生变更:* 加入了 *arguments*、*cwd* 和 *show_cmd* 参数，以及 `os.startfile/2` 审计事件。

## os.**system**(*command*)

​	在子外壳程序中执行此命令（一个字符串）。 这是通过调用标准 C 函数 `system()` 来实现的，并受到同样的限制。 对 [`sys.stdin`]({{< ref "/library/python/sys#sys.stdin" >}}) 的更改等不会反映在所执行命令的环境中。 如果 *command* 生成了任何输出，它将被发送到解释器的标准输出流。 C 标准没有指明这个 C 函数返回值的含义，因此这个 Python 函数的返回值取决于具体系统。

​	在 Unix 上，返回值为进程的退出状态，以针对 [`wait()`]({{< ref "/library/allos/os#os.wait" >}}) 而指定的格式进行编码。

​	在 Windows 上，返回值是运行 *command* 后系统 Shell 返回的值。该 Shell 由 Windows 环境变量 `COMSPEC`: 给出：通常是 **cmd.exe**，它会返回命令的退出状态。在使用非原生 Shell 的系统上，请查阅 Shell 的文档。

[`subprocess`]({{< ref "/library/concurrency/subprocess#module-subprocess" >}}) 模块提供了更强大的工具来生成新进程并跟踪执行结果，使用该模块比使用本函数更好。参阅 [`subprocess`]({{< ref "/library/concurrency/subprocess#module-subprocess" >}}) 文档中的 [使用 subprocess 模块替换旧函数]({{< ref "/library/concurrency/subprocess#subprocess-replacements" >}}) 部分以获取有用的帮助。

​	在 Unix 上，[`waitstatus_to_exitcode()`]({{< ref "/library/allos/os#os.waitstatus_to_exitcode" >}}) 可以将返回值（即退出状态）转换为退出码。在 Windows 上，返回值就是退出码。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `os.system` 并附带参数 `command`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows, not WASI, not Android, not iOS.

## os.**times**()

​	返回当前的全局进程时间。返回值是一个有 5 个属性的对象：

- `user` - 用户时间
- `system` - 系统时间
- `children_user` - 所有子进程的用户时间
- `children_system` - 所有子进程的系统时间
- `elapsed` - 从过去的固定时间点起，经过的真实时间

​	为了向后兼容，该对象的行为也类似于五元组，按照 `user`，`system`，`children_user`，`children_system` 和 `elapsed` 顺序组成。

​	在 Unix 上请参阅 Unix 手册页 *[times(2)](https://manpages.debian.org/times(2))* 和 [times(3)](https://man.freebsd.org/cgi/man.cgi?time(3)) 而在 Windows 上请参阅 [GetProcessTimes MSDN](https://docs.microsoft.com/windows/win32/api/processthreadsapi/nf-processthreadsapi-getprocesstimes)。 在 Windows 上，只有 `user` 和 `system` 是已知的；其他属性均为零。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows.

*在 3.3 版本发生变更:* 返回结果的类型由元组变成一个类似元组的对象，同时具有命名的属性。

## os.**wait**()

​	等待子进程执行完毕，返回一个元组，包含其 pid 和退出状态指示：一个 16 位数字，其低字节是终止该进程的信号编号，高字节是退出状态码（信号编号为零的情况下），如果生成了核心文件，则低字节的高位会置位。

​	如果不存在可被等待的子进程，则将引发 [`ChildProcessError`]({{< ref "/library/exceptions#ChildProcessError" >}})。

​	可以使用 [`waitstatus_to_exitcode()`]({{< ref "/library/allos/os#os.waitstatus_to_exitcode" >}}) 来将退出状态转换为退出码。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

​	参见

 

​	下面列出的其他 `wait*()` 函数可被用于等待特定子进程完成并具有更多的选项。 [`waitpid()`]({{< ref "/library/allos/os#os.waitpid" >}}) 是其中唯一在 Windows 上也可用的。

## os.**waitid**(*idtype*, *id*, *options*, */*)

​	等待一个子进程完成。

*idtype* 可以为 [`P_PID`]({{< ref "/library/allos/os#os.P_PID" >}}), [`P_PGID`]({{< ref "/library/allos/os#os.P_PGID" >}}), [`P_ALL`]({{< ref "/library/allos/os#os.P_ALL" >}}) 或 (在 Linux 上) [`P_PIDFD`]({{< ref "/library/allos/os#os.P_PIDFD" >}})。 对于 *id* 的解读由它来决定；请参阅它们各自的说明。

*options* 是多个旗标的 OR 组合。 要求至少有 [`WEXITED`]({{< ref "/library/allos/os#os.WEXITED" >}}), [`WSTOPPED`]({{< ref "/library/allos/os#os.WSTOPPED" >}}) 或 [`WCONTINUED`]({{< ref "/library/allos/os#os.WCONTINUED" >}}) 中的一个；[`WNOHANG`]({{< ref "/library/allos/os#os.WNOHANG" >}}) 和 [`WNOWAIT`]({{< ref "/library/allos/os#os.WNOWAIT" >}}) 是附加的可选旗标。

​	返回值是一个代表 `siginfo_t` 结构体所包含数据的对象，具有以下属性:

- `si_pid` (进程 ID)
- `si_uid` (子进程的实际用户 ID)
- `si_signo` (始终为 [`SIGCHLD`]({{< ref "/library/ipc/signal#signal.SIGCHLD" >}}))
- `si_status` (退出状态或信号编号，具体取决于 `si_code`)
- `si_code` (可能的值参见 [`CLD_EXITED`]({{< ref "/library/allos/os#os.CLD_EXITED" >}}))

​	如果指定了 [`WNOHANG`]({{< ref "/library/allos/os#os.WNOHANG" >}}) 而在所请求的状态中没有匹配的子进程，则将返回 `None`。 在其他情况下，如果没有匹配的子进程可被等待，则将引发 [`ChildProcessError`]({{< ref "/library/exceptions#ChildProcessError" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

> Added in version 3.3.
>

*在 3.13 版本发生变更:* 现在该函数在 macOS 同样可用。

## os.**waitpid**(*pid*, *options*, */*)

​	本函数的细节在 Unix 和 Windows 上有不同之处。

​	在 Unix 上：等待进程号为 *pid* 的子进程执行完毕，返回一个元组，内含其进程 ID 和退出状态指示（编码与 [`wait()`]({{< ref "/library/allos/os#os.wait" >}}) 相同）。调用的语义受整数 *options* 的影响，常规操作下该值应为 `0`。

​	如果 *pid* 大于 `0`，则 [`waitpid()`]({{< ref "/library/allos/os#os.waitpid" >}}) 会获取该指定进程的状态信息。如果 *pid* 为 `0`，则获取当前进程所在进程组中的所有子进程的状态。如果 *pid* 为 `-1`，则获取当前进程的子进程状态。如果 *pid* 小于 `-1`，则获取进程组 `-pid` （ *pid* 的绝对值）中所有进程的状态。

*options* 是多个旗标的 OR 组合。 如果它包含了 [`WNOHANG`]({{< ref "/library/allos/os#os.WNOHANG" >}}) 并且在所请求的状态中没有匹配的子进程，则将返回 `(0, 0)`。 在其他情况下，如果没有匹配的子进程可以被等待，则将引发 [`ChildProcessError`]({{< ref "/library/exceptions#ChildProcessError" >}})。 其他的可用选项还有 [`WUNTRACED`]({{< ref "/library/allos/os#os.WUNTRACED" >}}) 和 [`WCONTINUED`]({{< ref "/library/allos/os#os.WCONTINUED" >}})。

​	在 Windows 上：等待句柄为 *pid* 的进程执行完毕，返回一个元组，内含 *pid* 以及左移 8 位后的退出状态码（移位简化了跨平台使用本函数）。小于或等于 `0` 的 *pid* 在 Windows 上没有特殊含义，且会抛出异常。整数值 *options* 无效。*pid* 可以指向任何 ID 已知的进程，不一定是子进程。调用 [`spawn*`]({{< ref "/library/allos/os#os.spawnl" >}}) 函数时传入 [`P_NOWAIT`]({{< ref "/library/allos/os#os.P_NOWAIT" >}}) 将返回合适的进程句柄。

​	可以使用 [`waitstatus_to_exitcode()`]({{< ref "/library/allos/os#os.waitstatus_to_exitcode" >}}) 来将退出状态转换为退出码。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows, not WASI, not Android, not iOS.

*在 3.5 版本发生变更:* 如果系统调用被中断，但信号处理程序没有触发异常，此函数现在会重试系统调用，而不是触发 [`InterruptedError`]({{< ref "/library/exceptions#InterruptedError" >}}) 异常 (原因详见 [**PEP 475**](https://peps.python.org/pep-0475/))。

## os.**wait3**(*options*)

​	与 [`waitpid()`]({{< ref "/library/allos/os#os.waitpid" >}}) 类似，区别在于没有给出进程 id 参数并且返回一个 3 元组，其中包括子进程的 id、退出状态指示以及资源使用信息。 请参阅 [`resource.getrusage()`]({{< ref "/library/unix/resource#resource.getrusage" >}}) 了解有关资源使用信息的详情。 *options* 参数与提供给 [`waitpid()`]({{< ref "/library/allos/os#os.waitpid" >}}) 和 [`wait4()`]({{< ref "/library/allos/os#os.wait4" >}}) 的相同。

​	可以使用 [`waitstatus_to_exitcode()`]({{< ref "/library/allos/os#os.waitstatus_to_exitcode" >}}) 来将退出状态转换为退出码。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

## os.**wait4**(*pid*, *options*)

​	与 [`waitpid()`]({{< ref "/library/allos/os#os.waitpid" >}}) 类似，区别在于它是返回一个 3 元组，其中包括子进程的 id、退出状态指示以及资源使用信息。 请参阅 [`resource.getrusage()`]({{< ref "/library/unix/resource#resource.getrusage" >}}) 了解有关资源使用信息的详情。 [`wait4()`]({{< ref "/library/allos/os#os.wait4" >}}) 的参数与提供给 [`waitpid()`]({{< ref "/library/allos/os#os.waitpid" >}}) 的相同。

​	可以使用 [`waitstatus_to_exitcode()`]({{< ref "/library/allos/os#os.waitstatus_to_exitcode" >}}) 来将退出状态转换为退出码。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

## os.**P_PID**

## os.**P_PGID**

## os.**P_ALL**

## os.**P_PIDFD**

​	这些是 [`waitid()`]({{< ref "/library/allos/os#os.waitid" >}}) 中 *idtype* 可取的值。 它们会影响 *id* 的解读方式:

- `P_PID` - 等待 PID 为 *id* 的子进程。
- `P_PGID` - 等待进程组 ID 为 *id* 的任何子进程。
- `P_ALL` - 等待任何子进程；*id* 会被忽略。
- `P_PIDFD` - 等待以文件描述符 *id* 作为标识的子进程（使用a process file descriptor created with [`pidfd_open()`]({{< ref "/library/allos/os#os.pidfd_open" >}}) 创建的进程文件描述符）。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

​	备注

 

`P_PIDFD` 仅在 Linux >= 5.4 时可用。

> Added in version 3.3.
>

> Added in version 3.9:
> `P_PIDFD` 常量。

## os.**WCONTINUED**

​	如果子进程自它们上次被报告之后从作业控制停止位置继续执行，则 [`waitpid()`]({{< ref "/library/allos/os#os.waitpid" >}}), [`wait3()`]({{< ref "/library/allos/os#os.wait3" >}}), [`wait4()`]({{< ref "/library/allos/os#os.wait4" >}}) 和 [`waitid()`]({{< ref "/library/allos/os#os.waitid" >}}) 的这个 *选项* 旗标将导致子进程被报告。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

## os.**WEXITED**

[`waitid()`]({{< ref "/library/allos/os#os.waitid" >}}) 的这个 *选项* 旗标将导致已终结的子进程被报告。

​	其他 `wait*` 函数总是会报告已终结的子进程，所以此选项对它们不可用。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

> Added in version 3.3.
>

## os.**WSTOPPED**

​	这个 [`waitid()`]({{< ref "/library/allos/os#os.waitid" >}}) 的 *选项* 旗标将导致被信号发送所停止的子进程被报告。

​	这个选项对于其他 `wait*` 函数不可用。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

> Added in version 3.3.
>

## os.**WUNTRACED**

​	如果子进程自它们上次被停止之后再次被停止但它们的当前状态还未被报告，则 [`waitpid()`]({{< ref "/library/allos/os#os.waitpid" >}}), [`wait3()`]({{< ref "/library/allos/os#os.wait3" >}}) 和 [`wait4()`]({{< ref "/library/allos/os#os.wait4" >}}) 的这个 *选项* 旗标也将导致子进程被报告。

​	这个选项对于 [`waitid()`]({{< ref "/library/allos/os#os.waitid" >}}) 不可用。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

## os.**WNOHANG**

​	如果没有任何子进程状态是立即可用的，则这个 *选项* 旗标将导致 [`waitpid()`]({{< ref "/library/allos/os#os.waitpid" >}}), [`wait3()`]({{< ref "/library/allos/os#os.wait3" >}}), [`wait4()`]({{< ref "/library/allos/os#os.wait4" >}}) 和 [`waitid()`]({{< ref "/library/allos/os#os.waitid" >}}) 立即返回。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

## os.**WNOWAIT**

​	这个 *选项* 旗标将导致 [`waitid()`]({{< ref "/library/allos/os#os.waitid" >}}) 以可等待的状态离开子进程，这样后续的 `wait*()` 调用可被用来再次获取子进程状态信息。

​	这个选项对于其他 `wait*` 函数不可用。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

## os.**CLD_EXITED**

## os.**CLD_KILLED**

## os.**CLD_DUMPED**

## os.**CLD_TRAPPED**

## os.**CLD_STOPPED**

## os.**CLD_CONTINUED**

​	这些是由 [`waitid()`]({{< ref "/library/allos/os#os.waitid" >}}) 所返回的结果中 `si_code` 可能的取值。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

> Added in version 3.3.
>

*在 3.9 版本发生变更:* 添加了 [`CLD_KILLED`]({{< ref "/library/allos/os#os.CLD_KILLED" >}}) 和 [`CLD_STOPPED`]({{< ref "/library/allos/os#os.CLD_STOPPED" >}}) 值。

## os.**waitstatus_to_exitcode**(*status*)

​	将等待状态转换为退出码。

​	在 Unix 上：

- 如果进程正常退出（当 `WIFEXITED(status)` 为真值），则返回进程退出状态 (返回 `WEXITSTATUS(status)`): 结果值大于等于 0。
- 如果进程被信号终止（当 `WIFSIGNALED(status)` 为真值），则返回 `-signum` 其中 *signum* 为导致进程终止的信号数值 (返回 `-WTERMSIG(status)`): 结果值小于 0。
- 否则将抛出 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 异常。

​	在 Windows 上，返回 *status* 右移 8 位的结果。

​	在 Unix 上，如果进程正被追踪或 [`waitpid()`]({{< ref "/library/allos/os#os.waitpid" >}}) 附带 [`WUNTRACED`]({{< ref "/library/allos/os#os.WUNTRACED" >}}) 选项被调用，则调用者必须先检查 `WIFSTOPPED(status)` 是否为真值。 如果 `WIFSTOPPED(status)` 为真值则此函数不可被调用。

​	参见

 

[`WIFEXITED()`]({{< ref "/library/allos/os#os.WIFEXITED" >}}), [`WEXITSTATUS()`]({{< ref "/library/allos/os#os.WEXITSTATUS" >}}), [`WIFSIGNALED()`]({{< ref "/library/allos/os#os.WIFSIGNALED" >}}), [`WTERMSIG()`]({{< ref "/library/allos/os#os.WTERMSIG" >}}), [`WIFSTOPPED()`]({{< ref "/library/allos/os#os.WIFSTOPPED" >}}), [`WSTOPSIG()`]({{< ref "/library/allos/os#os.WSTOPSIG" >}}) 函数。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows, not WASI, not Android, not iOS.

> Added in version 3.9.
>

​	下列函数采用进程状态码作为参数，状态码由 [`system()`]({{< ref "/library/allos/os#os.system" >}})、[`wait()`]({{< ref "/library/allos/os#os.wait" >}}) 或 [`waitpid()`]({{< ref "/library/allos/os#os.waitpid" >}}) 返回。它们可用于确定进程上发生的操作。

## os.**WCOREDUMP**(*status*, */*)

​	如果为该进程生成了核心转储，返回 `True`，否则返回 `False`。

​	此函数应当仅在 [`WIFSIGNALED()`]({{< ref "/library/allos/os#os.WIFSIGNALED" >}}) 为真值时使用。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

## os.**WIFCONTINUED**(*status*)

​	如果一个已停止的子进程通过传送 [`SIGCONT`]({{< ref "/library/ipc/signal#signal.SIGCONT" >}}) 获得恢复（如果该进程是从任务控制停止后再继续的）则返回 `True`，否则返回 `False`。

​	参见 [`WCONTINUED`]({{< ref "/library/allos/os#os.WCONTINUED" >}}) 选项。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

## os.**WIFSTOPPED**(*status*)

​	如果进程是通过传送一个信号来停止的则返回 `True`，否则返回 `False`。

[`WIFSTOPPED()`]({{< ref "/library/allos/os#os.WIFSTOPPED" >}}) 只有在当 [`waitpid()`]({{< ref "/library/allos/os#os.waitpid" >}}) 调用是通过使用 [`WUNTRACED`]({{< ref "/library/allos/os#os.WUNTRACED" >}}) 选项来完成或者当该进程正被追踪时 (参见 *[ptrace(2)](https://manpages.debian.org/ptrace(2))*) 才返回 `True`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

## os.**WIFSIGNALED**(*status*)

​	如果进程是通过一个信号来终止的则返回 `True` ，否则返回 `False`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

## os.**WIFEXITED**(*status*)

​	如果进程正常终止退出则返回 `True`，也就是说通过调用 `exit()` 或 `_exit()`，或者通过从 `main()` 返回；在其他情况下则返回 `False`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

## os.**WEXITSTATUS**(*status*)

​	返回进程退出状态。

​	此函数应当仅在 [`WIFEXITED()`]({{< ref "/library/allos/os#os.WIFEXITED" >}}) 为真值时使用。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

## os.**WSTOPSIG**(*status*)

​	返回导致进程停止的信号。

​	此函数应当仅在 [`WIFSTOPPED()`]({{< ref "/library/allos/os#os.WIFSTOPPED" >}}) 为真值时使用。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

## os.**WTERMSIG**(*status*)

​	返回导致进程终止的信号的编号。

​	此函数应当仅在 [`WIFSIGNALED()`]({{< ref "/library/allos/os#os.WIFSIGNALED" >}}) 为真值时使用。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

## 调度器接口

​	这些函数控制操作系统如何为进程分配 CPU 时间。 它们仅在某些 Unix 平台上可用。 更多细节信息请查阅你所用 Unix 的指南页面。

> Added in version 3.3.
>

​	以下调度策略如果被操作系统支持就会对外公开。

## os.**SCHED_OTHER**

​	默认调度策略。

## os.**SCHED_BATCH**

​	用于 CPU 密集型进程的调度策略，它会尽量为计算机中的其余任务保留交互性。

## os.**SCHED_IDLE**

​	用于极低优先级的后台任务的调度策略。

## os.**SCHED_SPORADIC**

​	用于偶发型服务程序的调度策略。

## os.**SCHED_FIFO**

​	先进先出的调度策略。

## os.**SCHED_RR**

​	循环式的调度策略。

## os.**SCHED_RESET_ON_FORK**

​	此旗标可与任何其他调度策略进行 OR 运算。 当带有此旗标的进程设置分叉时，其子进程的调度策略和优先级会被重置为默认值。

## *class* os.**sched_param**(*sched_priority*)

​	这个类表示在 [`sched_setparam()`]({{< ref "/library/allos/os#os.sched_setparam" >}}), [`sched_setscheduler()`]({{< ref "/library/allos/os#os.sched_setscheduler" >}}) 和 [`sched_getparam()`]({{< ref "/library/allos/os#os.sched_getparam" >}}) 中使用的可修改调度形参。 它属于不可变对象。

​	目前它只有一个可能的形参：

## **sched_priority**

​	一个调度策略的调度优先级。

## os.**sched_get_priority_min**(*policy*)

​	获取 *policy* 的最低优先级数值。 *policy* 是以上调度策略常量之一。

## os.**sched_get_priority_max**(*policy*)

​	获取 *policy* 的最高优先级数值。 *policy* 是以上调度策略常量之一。

## os.**sched_setscheduler**(*pid*, *policy*, *param*, */*)

​	设置 PID 为 *pid* 的进程的调度策略。*pid* 为 0 指的是调用本方法的进程。*policy* 是以上调度策略常量之一。*param* 是一个 [`sched_param`]({{< ref "/library/allos/os#os.sched_param" >}}) 实例。

## os.**sched_getscheduler**(*pid*, */*)

​	返回 PID 为 *pid* 的进程的调度策略。*pid* 为 0 指的是调用本方法的进程。返回的结果是以上调度策略常量之一。

## os.**sched_setparam**(*pid*, *param*, */*)

​	设置 PID 为 *pid* 的进程的调度参数。 *pid* 为 0 表示调用方过程。 *param* 是一个 [`sched_param`]({{< ref "/library/allos/os#os.sched_param" >}}) 实例。

## os.**sched_getparam**(*pid*, */*)

​	返回 PID 为 *pid* 的进程的调度参数为一个 [`sched_param`]({{< ref "/library/allos/os#os.sched_param" >}}) 实例。*pid* 为 0 指的是调用本方法的进程。

## os.**sched_rr_get_interval**(*pid*, */*)

​	返回 PID 为 *pid* 的进程在时间片轮转调度下的时间片长度（单位为秒）。*pid* 为 0 指的是调用本方法的进程。

## os.**sched_yield**()

​	自愿放弃 CPU。

## os.**sched_setaffinity**(*pid*, *mask*, */*)

​	将 PID 为 *pid* 的进程（为零则为当前进程）限制到一组 CPU 上。*mask* 是整数的可迭代对象，表示应将进程限制在其中的一组 CPU。

## os.**sched_getaffinity**(*pid*, */*)

​	返回 PID 为 *pid* 的进程被限制到的那一组 CPU。

​	如果 *pid* 为零，则返回当前进程的调用方线程被限制到的那一组 CPU。

​	另请参阅 [`process_cpu_count()`]({{< ref "/library/allos/os#os.process_cpu_count" >}}) 函数。



## 其他系统信息

## os.**confstr**(*name*, */*)

​	返回字符串格式的系统配置信息。*name* 指定要查找的配置名称，它可以是字符串，是一个系统已定义的名称，这些名称定义在不同标准（POSIX，Unix 95，Unix 98 等）中。一些平台还定义了额外的其他名称。当前操作系统已定义的名称在 `confstr_names` 字典的键中给出。对于未包含在该映射中的配置名称，也可以传递一个整数作为 *name*。

​	如果 *name* 指定的配置值未定义，返回 `None`。

​	如果 *name* 是一个字符串且不是已定义的名称，将抛出 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 异常。如果当前系统不支持 *name* 指定的配置名称，即使该名称存在于 `confstr_names`，也会抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 异常，错误码为 [`errno.EINVAL`]({{< ref "/library/allos/errno#errno.EINVAL" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

## os.**confstr_names**

​	字典，表示映射关系，为 [`confstr()`]({{< ref "/library/allos/os#os.confstr" >}}) 可接受名称与操作系统为这些名称定义的整数值之间的映射。这可用于判断系统已定义了哪些名称。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

## os.**cpu_count**()

​	返回 **系统** 中逻辑 CPU 的数量。 如果无法确定则返回 `None`。

[`process_cpu_count()`]({{< ref "/library/allos/os#os.process_cpu_count" >}}) 函数可被用于获取 **当前进程** 的调用方线程可以使用的逻辑 CPU 数量。

> Added in version 3.4.
>

*在 3.13 版本发生变更:* 如果给出了 [`-X cpu_count`]({{< ref "/using/cmdline#cmdoption-X" >}}) 或设置了 [`PYTHON_CPU_COUNT`]({{< ref "/using/cmdline#envvar-PYTHON_CPU_COUNT" >}})，则 [`cpu_count()`]({{< ref "/library/allos/os#os.cpu_count" >}}) 将返回被覆盖的值 *n*。

## os.**getloadavg**()

​	返回系统运行队列中最近 1、5 和 15 分钟内的平均进程数。无法获得平均负载则抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 异常。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

## os.**process_cpu_count**()

​	获取 **当前进程** 的调用方线程可以使用的逻辑 CPU 数量。 如果无法确定则返回 `None`。 根据实际 CPU 的关联性它可能会小于 [`cpu_count()`]({{< ref "/library/allos/os#os.cpu_count" >}})。

[`cpu_count()`]({{< ref "/library/allos/os#os.cpu_count" >}}) 函数可被用于获取 **系统** 中逻辑 CPU 的数量。

​	如果给出了 [`-X cpu_count`]({{< ref "/using/cmdline#cmdoption-X" >}}) 或设置了 [`PYTHON_CPU_COUNT`]({{< ref "/using/cmdline#envvar-PYTHON_CPU_COUNT" >}})，则 [`process_cpu_count()`]({{< ref "/library/allos/os#os.process_cpu_count" >}}) 将返回被覆盖的值 *n*。

​	See also the [`sched_getaffinity()`]({{< ref "/library/allos/os#os.sched_getaffinity" >}}) function.

> Added in version 3.13.
>

## os.**sysconf**(*name*, */*)

​	返回整数格式的系统配置信息。如果 *name* 指定的配置值未定义，返回 `-1`。对 [`confstr()`]({{< ref "/library/allos/os#os.confstr" >}}) 的 *name* 参数的注释在此处也适用。当前已知的配置名称在 `sysconf_names` 字典中提供。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

## os.**sysconf_names**

​	字典，表示映射关系，为 [`sysconf()`]({{< ref "/library/allos/os#os.sysconf" >}}) 可接受名称与操作系统为这些名称定义的整数值之间的映射。这可用于判断系统已定义了哪些名称。

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

*在 3.11 版本发生变更:* 添加 `'SC_MINSIGSTKSZ'` 名称。

​	以下数据值用于支持对路径本身的操作。所有平台都有定义。

​	对路径的高级操作在 [`os.path`]({{< ref "/library/filesys/os_path#module-os.path" >}}) 模块中定义。

## os.**curdir**

​	操作系统用来表示当前目录的常量字符串。在 Windows 和 POSIX 上是 `'.'`。在 [`os.path`]({{< ref "/library/filesys/os_path#module-os.path" >}}) 中也可用。

## os.**pardir**

​	操作系统用来表示父目录的常量字符串。在 Windows 和 POSIX 上是 `'..'`。在 [`os.path`]({{< ref "/library/filesys/os_path#module-os.path" >}}) 中也可用。



## os.**sep**

​	操作系统用来分隔路径不同部分的字符。在 POSIX 上是 `'/'`，在 Windows 上是是 `'\\'`。注意，仅了解它不足以能解析或连接路径，请使用 [`os.path.split()`]({{< ref "/library/filesys/os_path#os.path.split" >}}) 和 [`os.path.join()`]({{< ref "/library/filesys/os_path#os.path.join" >}})，但它有时是有用的。在 [`os.path`]({{< ref "/library/filesys/os_path#module-os.path" >}}) 中也可用。

## os.**altsep**

​	操作系统用来分隔路径不同部分的替代字符。如果仅存在一个分隔符，则为 `None`。在 `sep` 是反斜杠的 Windows 系统上，该值被设为 `'/'`。在 [`os.path`]({{< ref "/library/filesys/os_path#module-os.path" >}}) 中也可用。

## os.**extsep**

​	分隔基本文件名与扩展名的字符，如 `os.py` 中的 `'.'`。在 [`os.path`]({{< ref "/library/filesys/os_path#module-os.path" >}}) 中也可用。

## os.**pathsep**

​	操作系统通常用于分隔搜索路径（如 `PATH`）中不同部分的字符，如 POSIX 上是 `':'`，Windows 上是 `';'`。在 [`os.path`]({{< ref "/library/filesys/os_path#module-os.path" >}}) 中也可用。

## os.**defpath**

​	在环境变量没有 `'PATH'` 键的情况下，[`exec*p*`]({{< ref "/library/allos/os#os.execl" >}}) and [`spawn*p*`]({{< ref "/library/allos/os#os.spawnl" >}}) 使用的默认搜索路径。在 [`os.path`]({{< ref "/library/filesys/os_path#module-os.path" >}}) 中也可用。

## os.**linesep**

​	当前平台用于分隔（或终止）行的字符串。它可以是单个字符，如 POSIX 上是 `'\n'`，也可以是多个字符，如 Windows 上是 `'\r\n'`。在写入以文本模式（默认模式）打开的文件时，请不要使用 *os.linesep* 作为行终止符，请在所有平台上都使用一个 `'\n'` 代替。

## os.**devnull**

​	空设备的文件路径。如 POSIX 上为 `'/dev/null'`，Windows 上为 `'nul'`。在 [`os.path`]({{< ref "/library/filesys/os_path#module-os.path" >}}) 中也可用。

## os.**RTLD_LAZY**

## os.**RTLD_NOW**

## os.**RTLD_GLOBAL**

## os.**RTLD_LOCAL**

## os.**RTLD_NODELETE**

## os.**RTLD_NOLOAD**

## os.**RTLD_DEEPBIND**

[`setdlopenflags()`]({{< ref "/library/python/sys#sys.setdlopenflags" >}}) 和 [`getdlopenflags()`]({{< ref "/library/python/sys#sys.getdlopenflags" >}}) 函数所使用的标志。请参阅 Unix 手册页 *[dlopen(3)](https://manpages.debian.org/dlopen(3))* 获取不同标志的含义。

> Added in version 3.3.
>

## 随机数

## os.**getrandom**(*size*, *flags=0*)

​	获得最多为 *size* 的随机字节。本函数返回的字节数可能少于请求的字节数。

​	这些字节可用于为用户空间的随机数生成器提供种子，或用于加密目的。

`getrandom()` 依赖于从设备驱动程序和其他环境噪声源收集的熵。不必要地读取大量数据将对使用 `/dev/random` 和 `/dev/urandom` 设备的其他用户产生负面影响。

​	flags 参数是一个位掩码，它可以包含零个或多个下列值的或运算结果: [`os.GRND_RANDOM`]({{< ref "/library/allos/os#os.GRND_RANDOM" >}}) 和 [`GRND_NONBLOCK`]({{< ref "/library/allos/os#os.GRND_NONBLOCK" >}})。

​	另请参阅 [Linux getrandom() 手册页](https://man7.org/linux/man-pages/man2/getrandom.2.html)。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 3.17.

> Added in version 3.6.
>

## os.**urandom**(*size*, */*)

​	返回大小为 *size* 的字节串，它是适合加密使用的随机字节。

​	本函数从系统指定的随机源获取随机字节。对于加密应用程序，返回的数据应有足够的不可预测性，尽管其确切的品质取决于操作系统的实现。

​	在 Linux 上，如果 `getrandom()` 系统调用可用，它将以阻塞模式使用：阻塞直到系统的 urandom 熵池初始化完毕（内核收集了 128 位熵）。原理请参阅 [**PEP 524**](https://peps.python.org/pep-0524/)。在 Linux 上，[`getrandom()`]({{< ref "/library/allos/os#os.getrandom" >}}) 可以以非阻塞模式（使用 [`GRND_NONBLOCK`]({{< ref "/library/allos/os#os.GRND_NONBLOCK" >}}) 标志）获取随机字节，或者轮询直到系统的 urandom 熵池初始化完毕。

​	在类 Unix 系统上，随机字节是从 `/dev/urandom` 设备读取的。如果 `/dev/urandom` 设备不可用或不可读，则抛出 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}}) 异常。

​	在 Windows 上，它将使用 `BCryptGenRandom()`。

​	参见

 

[`secrets`]({{< ref "/library/crypto/secrets#module-secrets" >}}) 模块提供了更高级的功能。所在平台会提供随机数生成器，有关其易于使用的接口，请参阅 [`random.SystemRandom`]({{< ref "/library/numeric/random#random.SystemRandom" >}})。

*在 3.5 版本发生变更:* 在 Linux 3.17 和更高版本上，现在使用 `getrandom()` 系统调用（如果可用）。在 OpenBSD 5.6 和更高版本上，现在使用 `getentropy()` C 函数。这些函数避免了使用内部文件描述符。

*在 3.5.2 版本发生变更:* 在 Linux 上，如果 `getrandom()` 系统调用阻塞（urandom 熵池尚未初始化完毕），则退回一步读取 `/dev/urandom`。

*在 3.6 版本发生变更:* 在 Linux 上，`getrandom()` 现在以阻塞模式使用，以提高安全性。

*在 3.11 版本发生变更:* 在 Windows 上，将使用 `BCryptGenRandom()` 而不是已被弃用的 `CryptGenRandom()`。

## os.**GRND_NONBLOCK**

​	默认情况下，从 `/dev/random` 读取时，如果没有可用的随机字节，则 [`getrandom()`]({{< ref "/library/allos/os#os.getrandom" >}}) 会阻塞；从 `/dev/urandom` 读取时，如果熵池尚未初始化，则会阻塞。

​	如果设置了 [`GRND_NONBLOCK`]({{< ref "/library/allos/os#os.GRND_NONBLOCK" >}}) 标志，则这些情况下 [`getrandom()`]({{< ref "/library/allos/os#os.getrandom" >}}) 不会阻塞，而是立即抛出 [`BlockingIOError`]({{< ref "/library/exceptions#BlockingIOError" >}}) 异常。

> Added in version 3.6.
>

## os.**GRND_RANDOM**

​	如果设置了此标志位，那么将从 `/dev/random` 池而不是 `/dev/urandom` 池中提取随机字节。

> Added in version 3.6.
>
