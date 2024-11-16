+++
title = "弃用"
date = 2024-11-14T22:43:43+08:00
weight = 3
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文：[https://docs.python.org/zh-cn/3.13/deprecations/index.html](https://docs.python.org/zh-cn/3.13/deprecations/index.html)
>
> 收录该文档的时间：`2024-11-14T22:13:29+08:00`

# 弃用

## 计划在 Python 3.14 中移除

- [`argparse`]({{< ref "/library/allos/argparse#module-argparse" >}}): `argparse.BooleanOptionalAction` 的 *type*, *choices* 和 *metavar* 形参已被弃用并将在 3.14 中移除。 （由 Nikita Sobolev 在 [gh-92248](https://github.com/python/cpython/issues/92248) 中贡献。）

- [`ast`]({{< ref "/library/language/ast#module-ast" >}}): 以下特性自 Python 3.8 起已在文档中声明弃用，现在当运行时如果它们被访问或使用时将发出 [`DeprecationWarning`]({{< ref "/library/exceptions#DeprecationWarning" >}})，并将在 Python 3.14 中移除：

  - `ast.Num`
  - `ast.Str`
  - `ast.Bytes`
  - `ast.NameConstant`
  - `ast.Ellipsis`

  请改用 [`ast.Constant`]({{< ref "/library/language/ast#ast.Constant" >}})。 （由 Serhiy Storchaka 在 [gh-90953](https://github.com/python/cpython/issues/90953) 中贡献。）

- [`asyncio`]({{< ref "/library/ipc/asyncio#module-asyncio" >}}):

  - 子监视器类 [`MultiLoopChildWatcher`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.MultiLoopChildWatcher" >}}), [`FastChildWatcher`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.FastChildWatcher" >}}), [`AbstractChildWatcher`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.AbstractChildWatcher" >}}) 和 [`SafeChildWatcher`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.SafeChildWatcher" >}}) 已被弃用并将在 Python 3.14 中移除。 （由 Kumar Aditya 在 [gh-94597](https://github.com/python/cpython/issues/94597) 中贡献。）
  - [`asyncio.set_child_watcher()`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.set_child_watcher" >}})、[`asyncio.get_child_watcher()`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.get_child_watcher" >}})、[`asyncio.AbstractEventLoopPolicy.set_child_watcher()`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.AbstractEventLoopPolicy.set_child_watcher" >}}) 和 [`asyncio.AbstractEventLoopPolicy.get_child_watcher()`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.AbstractEventLoopPolicy.get_child_watcher" >}}) 已弃用，并将在 Python 3.14 中移除。（由 Kumar Aditya 在 [gh-94597](https://github.com/python/cpython/issues/94597) 中贡献。）
  - 现在默认事件循环策略的 [`get_event_loop()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.get_event_loop" >}}) 方法在当前事件循环未设置并决定创建一个时将发出 [`DeprecationWarning`]({{< ref "/library/exceptions#DeprecationWarning" >}})。 （由 Serhiy Storchaka 和 Guido van Rossum 在 [gh-100160](https://github.com/python/cpython/issues/100160) 中贡献。）

- [`collections.abc`]({{< ref "/library/datatypes/collections_abc#module-collections.abc" >}}): 已弃用 [`ByteString`]({{< ref "/library/datatypes/collections_abc#collections.abc.ByteString" >}})。 推荐改用 `Sequence` 或 [`Buffer`]({{< ref "/library/datatypes/collections_abc#collections.abc.Buffer" >}})。 用于类型标注时，则推荐并集运算符，如 `bytes | bytearray`，或 [`collections.abc.Buffer`]({{< ref "/library/datatypes/collections_abc#collections.abc.Buffer" >}})。 （由 Shantanu Jain 在 [gh-91896](https://github.com/python/cpython/issues/91896) 中贡献。）

- [`email`]({{< ref "/library/netdata/email#module-email" >}}): 已弃用 [`email.utils.localtime()`]({{< ref "/library/netdata/email/email_utils#email.utils.localtime" >}}) 中的 *isdst* 形参。 （由 Alan Williams 在 [gh-72346](https://github.com/python/cpython/issues/72346) 中贡献。）

- [`importlib.abc`]({{< ref "/library/modules/importlib#module-importlib.abc" >}}) 中已弃用的类：

  - `importlib.abc.ResourceReader`
  - `importlib.abc.Traversable`
  - `importlib.abc.TraversableResources`

  使用 [`importlib.resources.abc`]({{< ref "/library/modules/importlib_resources_abc#module-importlib.resources.abc" >}}) 类代替:

  - [`importlib.resources.abc.Traversable`]({{< ref "/library/modules/importlib_resources_abc#importlib.resources.abc.Traversable" >}})
  - [`importlib.resources.abc.TraversableResources`]({{< ref "/library/modules/importlib_resources_abc#importlib.resources.abc.TraversableResources" >}})

  （由 Jason R. Coombs 和 Hugo van Kemenade 在 [gh-93963](https://github.com/python/cpython/issues/93963) 中贡献。）

- [`itertools`]({{< ref "/library/functional/itertools#module-itertools" >}}) 具有对 copy, deepcopy 和 pickle 等操作的未写入文档的、低效的、历史上充满问题的且不稳定的支持。 这将在 3.14 中移除以显著减少代码量和维护负担。 （由 Raymond Hettinger 在 [gh-101588](https://github.com/python/cpython/issues/101588) 中贡献。）

- [`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}): 默认的启动方法在目前默认使用 `'fork'` 的 Linux, BSD 和其他非 macOS POSIX 平台上将改为更安全的方法 ([gh-84559](https://github.com/python/cpython/issues/84559))。 为此添加运行时警告将带来糟糕的体验因为大部分代码并不会关心这个问题。 当你的代码 *需要* `'fork'` 时请使用 [`get_context()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.get_context" >}}) 或 [`set_start_method()`]({{< ref "/library/concurrency/multiprocessing#multiprocessing.set_start_method" >}}) API 来显式地指明。 参见 [上下文和启动方法]({{< ref "/library/concurrency/multiprocessing#multiprocessing-start-methods" >}})。

- [`pathlib`]({{< ref "/library/filesys/pathlib#module-pathlib" >}}): [`is_relative_to()`]({{< ref "/library/filesys/pathlib#pathlib.PurePath.is_relative_to" >}}) 和 [`relative_to()`]({{< ref "/library/filesys/pathlib#pathlib.PurePath.relative_to" >}}): 传入额外参数的做法已被弃用。

- [`pkgutil`]({{< ref "/library/modules/pkgutil#module-pkgutil" >}}): 现在 [`find_loader()`]({{< ref "/library/modules/pkgutil#pkgutil.find_loader" >}}) 和 [`get_loader()`]({{< ref "/library/modules/pkgutil#pkgutil.get_loader" >}}) 将引发 [`DeprecationWarning`]({{< ref "/library/exceptions#DeprecationWarning" >}})；请改用 [`importlib.util.find_spec()`]({{< ref "/library/modules/importlib#importlib.util.find_spec" >}})。 （由 Nikita Sobolev 在 [gh-97850](https://github.com/python/cpython/issues/97850) 中贡献。）

- [`pty`]({{< ref "/library/unix/pty#module-pty" >}}):

  - `master_open()`: 使用 [`pty.openpty()`]({{< ref "/library/unix/pty#pty.openpty" >}})。
  - `slave_open()`: 使用 [`pty.openpty()`]({{< ref "/library/unix/pty#pty.openpty" >}})。

- [`sqlite3`]({{< ref "/library/persistence/sqlite3#module-sqlite3" >}}):

  - [`version`]({{< ref "/library/persistence/sqlite3#sqlite3.version" >}}) 和 [`version_info`]({{< ref "/library/persistence/sqlite3#sqlite3.version_info" >}})。
  - 如果使用了 [命名占位符]({{< ref "/library/persistence/sqlite3#sqlite3-placeholders" >}}) 且 *parameters* 是一个序列而不是 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 则选择 [`execute()`]({{< ref "/library/persistence/sqlite3#sqlite3.Cursor.execute" >}}) 和 [`executemany()`]({{< ref "/library/persistence/sqlite3#sqlite3.Cursor.executemany" >}})。

- [`typing`]({{< ref "/library/development/typing#module-typing" >}}): [`ByteString`]({{< ref "/library/development/typing#typing.ByteString" >}}) 自 Python 3.9 起已被弃用，现在当被使用时将会发出 [`DeprecationWarning`]({{< ref "/library/exceptions#DeprecationWarning" >}})。

- [`urllib`]({{< ref "/library/internet/urllib#module-urllib" >}}): `urllib.parse.Quoter` 已被弃用：它不应被作为公有 API。 （由 Gregory P. Smith 在 [gh-88168](https://github.com/python/cpython/issues/88168) 中贡献。）

## Python 3.15 中的待移除功能

- 导入系统：
  - 当设置 [`__spec__.cached`]({{< ref "/library/modules/importlib#importlib.machinery.ModuleSpec.cached" >}}) 失败时在模块上设置 [`__cached__`]({{< ref "/reference/datamodel#module.__cached__" >}}) 的做法已被弃用。 在 Python 3.15 中，`__cached__` 将不会再被导入系统或标准库纳入考虑。 ([gh-97879](https://github.com/python/cpython/issues/97879))
  - 当设备 [`__spec__.parent`]({{< ref "/library/modules/importlib#importlib.machinery.ModuleSpec.parent" >}}) 失败时在模块上设置 [`__package__`]({{< ref "/reference/datamodel#module.__package__" >}}) 的做法已被弃用。 在 Python 3.15 中，`__package__` 将不会再被导入系统或标准库纳入考虑。 ([gh-97879](https://github.com/python/cpython/issues/97879))
- [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}):
  - 未写入文档的 `ctypes.SetPointerType()` 函数自 Python 3.13 起已被弃用。
- [`http.server`]({{< ref "/library/internet/http_server#module-http.server" >}}):
  - 过时且很少被使用的 [`CGIHTTPRequestHandler`]({{< ref "/library/internet/http_server#http.server.CGIHTTPRequestHandler" >}}) 自 Python 3.13 起已被弃用。 不存在直接的替代品。 对于建立带有请求处理器的 Web 服务程序来说 *任何东西* 都比 CGI 要好。
  - 用于 **python -m http.server** 命令行界面的 `--cgi` 旗标自 Python 3.13 起已被弃用。
- [`locale`]({{< ref "/library/i18n/locale#module-locale" >}}):
  - [`getdefaultlocale()`]({{< ref "/library/i18n/locale#locale.getdefaultlocale" >}}) 函数自 Python 3.11 起已被弃用。 最初计划在 Python 3.13 中移除它 ([gh-90817](https://github.com/python/cpython/issues/90817))，但已被推迟至 Python 3.15。 请改用 [`getlocale()`]({{< ref "/library/i18n/locale#locale.getlocale" >}}), [`setlocale()`]({{< ref "/library/i18n/locale#locale.setlocale" >}}) 和 [`getencoding()`]({{< ref "/library/i18n/locale#locale.getencoding" >}})。 （由 Hugo van Kemenade 在 [gh-111187](https://github.com/python/cpython/issues/111187) 中贡献。）
- [`pathlib`]({{< ref "/library/filesys/pathlib#module-pathlib" >}}):
  - [`PurePath.is_reserved()`]({{< ref "/library/filesys/pathlib#pathlib.PurePath.is_reserved" >}}) 自 Python 3.13 起已被弃用。 请使用 [`os.path.isreserved()`]({{< ref "/library/filesys/os_path#os.path.isreserved" >}}) 来检测 Windows 上的保留路径。
- [`platform`]({{< ref "/library/allos/platform#module-platform" >}}):
  - [`java_ver()`]({{< ref "/library/allos/platform#platform.java_ver" >}}) 自 Python 3.13 起已被弃用。 此函数仅对 Jython 支持有用，具有令人困惑的 API，并且大部分未经测试。
- [`threading`]({{< ref "/library/concurrency/threading#module-threading" >}}):
  - 在 Python 3.15 中 [`RLock()`]({{< ref "/library/concurrency/threading#threading.RLock" >}}) 将不再接受参数。 传入参数的做法自 Python 3.14 起已被弃用，因为 Python 版本不接受任何参数，而 C 版本允许任意数量的位置或关键字参数，但会忽略所有参数。
- [`types`]({{< ref "/library/datatypes/types#module-types" >}}):
  - [`types.CodeType`]({{< ref "/library/datatypes/types#types.CodeType" >}}): Accessing [`co_lnotab`]({{< ref "/reference/datamodel#codeobject.co_lnotab" >}}) was deprecated in [**PEP 626**](https://peps.python.org/pep-0626/) since 3.10 and was planned to be removed in 3.12, but it only got a proper [`DeprecationWarning`]({{< ref "/library/exceptions#DeprecationWarning" >}}) in 3.12. May be removed in 3.15. (Contributed by Nikita Sobolev in [gh-101866](https://github.com/python/cpython/issues/101866).)
- [`typing`]({{< ref "/library/development/typing#module-typing" >}}):
  - 未写入文档的用于创建 [`NamedTuple`]({{< ref "/library/development/typing#typing.NamedTuple" >}}) 类的关键字参数语法 (例如 `Point = NamedTuple("Point", x=int, y=int)`) 自 Python 3.13 起已被弃用。 请改用基于类的语法或函数语法。
  - [`typing.no_type_check_decorator()`]({{< ref "/library/development/typing#typing.no_type_check_decorator" >}}) 装饰器自 Python 3.13 起已被弃用。 存在于 [`typing`]({{< ref "/library/development/typing#module-typing" >}}) 模块八年之后，它仍未被任何主要类型检查器所支持。
- [`wave`]({{< ref "/library/mm/wave#module-wave" >}}):
  - [`Wave_read`]({{< ref "/library/mm/wave#wave.Wave_read" >}}) 和 [`Wave_write`]({{< ref "/library/mm/wave#wave.Wave_write" >}}) 类的 [`getmark()`]({{< ref "/library/mm/wave#wave.Wave_read.getmark" >}}), `setmark()` 和 [`getmarkers()`]({{< ref "/library/mm/wave#wave.Wave_read.getmarkers" >}}) 方法自 Python 3.13 起已被弃用。

## Pending removal in Python 3.16

- 导入系统：
  - Setting [`__loader__`]({{< ref "/reference/datamodel#module.__loader__" >}}) on a module while failing to set [`__spec__.loader`]({{< ref "/library/modules/importlib#importlib.machinery.ModuleSpec.loader" >}}) is deprecated. In Python 3.16, `__loader__` will cease to be set or taken into consideration by the import system or the standard library.
- [`array`]({{< ref "/library/datatypes/array#module-array" >}}):
  - `'u'` 格式代码 (`wchar_t`) 自 Python 3.3 起已在文档中弃用并自 Python 3.13 起在运行时弃用。 对于 Unicode 字符请改用 `'w'` 格式代码 ([`Py_UCS4`](https://docs.python.org/zh-cn/3.13/c-api/unicode.html#c.Py_UCS4))。
- [`asyncio`]({{< ref "/library/ipc/asyncio#module-asyncio" >}}):
  - `asyncio.iscoroutinefunction()` is deprecated and will be removed in Python 3.16, use [`inspect.iscoroutinefunction()`]({{< ref "/library/python/inspect#inspect.iscoroutinefunction" >}}) instead. (Contributed by Jiahao Li and Kumar Aditya in [gh-122875](https://github.com/python/cpython/issues/122875).)
- [`builtins`]({{< ref "/library/python/builtins#module-builtins" >}}):
  - 对布尔类型 `~True` 或 `~False` 执行按位取反的操作自 Python 3.12 起已被弃用，因为它会产生奇怪和不直观的结果 (`-2` and `-1`)。 请改用 `not x` 来对布尔值执行逻辑否操作。 对于需要对下层整数执行按位取反操作的少数场合，请显式地将其转换为 `int` (`~int(x)`)。
- [`shutil`]({{< ref "/library/filesys/shutil#module-shutil" >}}):
  - `ExecError` 异常自 Python 3.14 起已被弃用。 它自 Python 3.4 起就未被 `shutil` 中的任何函数所使用，现在是 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}}) 的一个别名。
- [`symtable`]({{< ref "/library/language/symtable#module-symtable" >}}):
  - [`Class.get_methods`]({{< ref "/library/language/symtable#symtable.Class.get_methods" >}}) 方法自 Python 3.14 起被弃用。
- [`sys`]({{< ref "/library/python/sys#module-sys" >}}):
  - [`_enablelegacywindowsfsencoding()`]({{< ref "/library/python/sys#sys._enablelegacywindowsfsencoding" >}}) 函数自 Python 3.13 起被弃用。 请改用 [`PYTHONLEGACYWINDOWSFSENCODING`]({{< ref "/using/cmdline#envvar-PYTHONLEGACYWINDOWSFSENCODING" >}}) 环境变量。
- [`tarfile`]({{< ref "/library/archiving/tarfile#module-tarfile" >}}):
  - 未写入文档也未被使用的 `TarFile.tarfile` 属性自 Python 3.13 起被弃用。

## 计划在未来版本中移除

​	以下API将会被移除，尽管具体时间还未确定。

- [`argparse`]({{< ref "/library/allos/argparse#module-argparse" >}}): 嵌套参数分组和嵌套互斥分组的做法已被弃用。
- [`array`]({{< ref "/library/datatypes/array#module-array" >}}) 的 `'u'` 格式代码 ([gh-57281](https://github.com/python/cpython/issues/57281))
- [`builtins`]({{< ref "/library/python/builtins#module-builtins" >}}):
  - `bool(NotImplemented)`。
  - 生成器: `throw(type, exc, tb)` 和 `athrow(type, exc, tb)` 签名已被弃用：请改用 `throw(exc)` 和 `athrow(exc)`，即单参数签名。
  - 目前 Python 接受数字类字面值后面紧跟关键字的写法，例如 `0in x`, `1or x`, `0if 1else 2`。 它允许像 `[0x1for x in y]` 这样令人困惑且有歧义的表达式 (它可以被解读为 `[0x1 for x in y]` 或者 `[0x1f or x in y]`)。 如果数字类字面值后面紧跟关键字 [`and`]({{< ref "/reference/expressions#and" >}}), [`else`]({{< ref "/reference/compound_stmts#else" >}}), [`for`]({{< ref "/reference/compound_stmts#for" >}}), [`if`]({{< ref "/reference/compound_stmts#if" >}}), [`in`]({{< ref "/reference/expressions#in" >}}), [`is`]({{< ref "/reference/expressions#is" >}}) 和 [`or`]({{< ref "/reference/expressions#or" >}}) 中的一个将会引发语法警告。 在未来的版本中它将改为语法错误。 ([gh-87999](https://github.com/python/cpython/issues/87999))
  - 对 `__index__()` 和 `__int__()` 方法返回非 int 类型的支持：将要求这些方法必须返回 [`int`]({{< ref "/library/functions#int" >}}) 的子类的实例。
  - 对 `__float__()` 方法返回 [`float`]({{< ref "/library/functions#float" >}}) 的子类的支持：将要求这些方法必须返回 [`float`]({{< ref "/library/functions#float" >}}) 的实例。
  - 对 `__complex__()` 方法返回 [`complex`]({{< ref "/library/functions#complex" >}}) 的子类的支持：将要求这些方法必须返回 [`complex`]({{< ref "/library/functions#complex" >}}) 的实例。
  - 将 `int()` 委托给 `__trunc__()` 方法。
  - 传入一个复数作为 [`complex()`]({{< ref "/library/functions#complex" >}}) 构造器中的 *real* 或 *imag* 参数的做法现在已被弃用；它应当仅作为单个位置参数被传入。 （由 Serhiy Storchaka 在 [gh-109218](https://github.com/python/cpython/issues/109218) 中贡献。）.)
- [`calendar`]({{< ref "/library/datatypes/calendar#module-calendar" >}}): `calendar.January` 和 `calendar.February` 常量已被弃用并由 [`calendar.JANUARY`]({{< ref "/library/datatypes/calendar#calendar.JANUARY" >}}) 和 [`calendar.FEBRUARY`]({{< ref "/library/datatypes/calendar#calendar.FEBRUARY" >}}) 替代。 （由 Prince Roshan 在 [gh-103636](https://github.com/python/cpython/issues/103636) 中贡献。）
- [`codeobject.co_lnotab`]({{< ref "/reference/datamodel#codeobject.co_lnotab" >}}): 改用 [`codeobject.co_lines()`]({{< ref "/reference/datamodel#codeobject.co_lines" >}}) 方法。
- [`datetime`]({{< ref "/library/datatypes/datetime#module-datetime" >}}):
  - [`utcnow()`]({{< ref "/library/datatypes/datetime#datetime.datetime.utcnow" >}}): 使用 `datetime.datetime.now(tz=datetime.UTC)`。
  - [`utcfromtimestamp()`]({{< ref "/library/datatypes/datetime#datetime.datetime.utcfromtimestamp" >}}): 使用 `datetime.datetime.fromtimestamp(timestamp, tz=datetime.UTC)`。
- [`gettext`]({{< ref "/library/i18n/gettext#module-gettext" >}}): 复数值必须是一个整数。
- [`importlib`]({{< ref "/library/modules/importlib#module-importlib" >}}):
  - `load_module()` 方法：改用 `exec_module()`。
  - [`cache_from_source()`]({{< ref "/library/modules/importlib#importlib.util.cache_from_source" >}}) *debug_override* 形参已被弃用：改用 *optimization* 形参。
- [`importlib.metadata`]({{< ref "/library/modules/importlib_metadata#module-importlib.metadata" >}}):
  - `EntryPoints` 元组接口。
  - 返回值中隐式的 `None`。
- [`logging`]({{< ref "/library/allos/logging#module-logging" >}}): `warn()` 方法自 Python 3.3 起已被弃用，请改用 [`warning()`]({{< ref "/library/allos/logging#logging.warning" >}})。
- [`mailbox`]({{< ref "/library/netdata/mailbox#module-mailbox" >}}): 对 StringIO 输入和文本模式的使用已被弃用，改用 BytesIO 和二进制模式。
- [`os`]({{< ref "/library/allos/os#module-os" >}}): 在多线程的进程中调用 [`os.register_at_fork()`]({{< ref "/library/allos/os#os.register_at_fork" >}})。
- `pydoc.ErrorDuringImport`: 使用元组值作为 *exc_info* 形参的做法已被弃用，应使用异常实例。
- [`re`]({{< ref "/library/text/re#module-re" >}}): 现在对于正则表达式中的数字分组引用和分组名称将应用更严格的规则。 现在只接受 ASCII 数字序列作为数字引用。 字节串模式和替换字符串中的分组名称现在只能包含 ASCII 字母和数字以及下划线。 （由 Serhiy Storchaka 在 [gh-91760](https://github.com/python/cpython/issues/91760) 中贡献。）
- `sre_compile`, `sre_constants` 和 `sre_parse` 模块。
- [`shutil`]({{< ref "/library/filesys/shutil#module-shutil" >}}): [`rmtree()`]({{< ref "/library/filesys/shutil#shutil.rmtree" >}}) 的 *onerror* 形参在 Python 3.12 中已被弃用；请改用 *onexc* 形参。
- [`ssl`]({{< ref "/library/ipc/ssl#module-ssl" >}}) 选项和协议：
  - [`ssl.SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 不带 protocol 参数的做法已被弃用。
  - [`ssl.SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}): [`set_npn_protocols()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.set_npn_protocols" >}}) 和 `selected_npn_protocol()` 已被弃用：请改用 ALPN。
  - `ssl.OP_NO_SSL*` 选项
  - `ssl.OP_NO_TLS*` 选项
  - `ssl.PROTOCOL_SSLv3`
  - `ssl.PROTOCOL_TLS`
  - `ssl.PROTOCOL_TLSv1`
  - `ssl.PROTOCOL_TLSv1_1`
  - `ssl.PROTOCOL_TLSv1_2`
  - `ssl.TLSVersion.SSLv3`
  - `ssl.TLSVersion.TLSv1`
  - `ssl.TLSVersion.TLSv1_1`
- [`sysconfig.is_python_build()`]({{< ref "/library/python/sysconfig#sysconfig.is_python_build" >}}) *check_home* 形参已被弃用并会被忽略。
- [`threading`]({{< ref "/library/concurrency/threading#module-threading" >}}) 的方法：
  - `threading.Condition.notifyAll()`: 使用 [`notify_all()`]({{< ref "/library/concurrency/threading#threading.Condition.notify_all" >}})。
  - `threading.Event.isSet()`: 使用 [`is_set()`]({{< ref "/library/concurrency/threading#threading.Event.is_set" >}})。
  - `threading.Thread.isDaemon()`, [`threading.Thread.setDaemon()`]({{< ref "/library/concurrency/threading#threading.Thread.setDaemon" >}}): 使用 [`threading.Thread.daemon`]({{< ref "/library/concurrency/threading#threading.Thread.daemon" >}}) 属性。
  - `threading.Thread.getName()`, [`threading.Thread.setName()`]({{< ref "/library/concurrency/threading#threading.Thread.setName" >}}): 使用 [`threading.Thread.name`]({{< ref "/library/concurrency/threading#threading.Thread.name" >}}) 属性。
  - `threading.currentThread()`: 使用 [`threading.current_thread()`]({{< ref "/library/concurrency/threading#threading.current_thread" >}})。
  - `threading.activeCount()`: 使用 [`threading.active_count()`]({{< ref "/library/concurrency/threading#threading.active_count" >}})。
- [`typing.Text`]({{< ref "/library/development/typing#typing.Text" >}}) ([gh-92332](https://github.com/python/cpython/issues/92332))。
- [`unittest.IsolatedAsyncioTestCase`]({{< ref "/library/development/unittest#unittest.IsolatedAsyncioTestCase" >}}): 从测试用例返回不为 `None` 的值的做法已被弃用。
- [`urllib.parse`]({{< ref "/library/internet/urllib/urllib_parse#module-urllib.parse" >}}) 函数已被弃用：改用 [`urlparse()`]({{< ref "/library/internet/urllib/urllib_parse#urllib.parse.urlparse" >}})
  - `splitattr()`
  - `splithost()`
  - `splitnport()`
  - `splitpasswd()`
  - `splitport()`
  - `splitquery()`
  - `splittag()`
  - `splittype()`
  - `splituser()`
  - `splitvalue()`
  - `to_bytes()`
- [`urllib.request`]({{< ref "/library/internet/urllib/urllib_request#module-urllib.request" >}}): 发起请求的 [`URLopener`]({{< ref "/library/internet/urllib/urllib_request#urllib.request.URLopener" >}}) 和 [`FancyURLopener`]({{< ref "/library/internet/urllib/urllib_request#urllib.request.FancyURLopener" >}}) 方式已被弃用。 改用更新 [`urlopen()`]({{< ref "/library/internet/urllib/urllib_request#urllib.request.urlopen" >}}) 函数和方法。
- [`wsgiref`]({{< ref "/library/internet/wsgiref#module-wsgiref" >}}): `SimpleHandler.stdout.write()` 不应执行部分写入。
- [`xml.etree.ElementTree`]({{< ref "/library/markup/xml_etree_elementtree#module-xml.etree.ElementTree" >}}): 对 [`Element`]({{< ref "/library/markup/xml_etree_elementtree#xml.etree.ElementTree.Element" >}}) 的真值测试已被弃用。 在未来的发布版中它将始终返回 `True`。 建议改用显式的 `len(elem)` 或 `elem is not None` 测试。
- [`zipimport.zipimporter.load_module()`]({{< ref "/library/modules/zipimport#zipimport.zipimporter.load_module" >}}) 已被弃用：请改用 [`exec_module()`]({{< ref "/library/modules/zipimport#zipimport.zipimporter.exec_module" >}})。

## C API 的弃用项

### 计划在 Python 3.14 中移除

- [`PyDictObject`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDictObject) 中的 `ma_version_tag` 字段用于扩展模块 ( [**PEP 699**](https://peps.python.org/pep-0699/) ; [gh-101193](https://github.com/python/cpython/issues/101193) )。

- 创建 [`immutable types`](https://docs.python.org/zh-cn/3.13/c-api/typeobj.html#c.Py_TPFLAGS_IMMUTABLETYPE) 的可变基础 ( [gh-95388](https://github.com/python/cpython/issues/95388) )。

- 用于配置 Python 的初始化的函数，在 Python 3.11 中已弃用：

  - `PySys_SetArgvEx()`: 改为设置 [`PyConfig.argv`]({{< ref "/c_api/init_config#c.PyConfig.argv" >}})。
  - `PySys_SetArgv()`: 改为设置 [`PyConfig.argv`]({{< ref "/c_api/init_config#c.PyConfig.argv" >}})。
  - `Py_SetProgramName()`: 改为设置 [`PyConfig.program_name`]({{< ref "/c_api/init_config#c.PyConfig.program_name" >}})。
  - `Py_SetPythonHome()`: 改为设置 [`PyConfig.home`]({{< ref "/c_api/init_config#c.PyConfig.home" >}})。

  [`Py_InitializeFromConfig()`]({{< ref "/c_api/init#c.Py_InitializeFromConfig" >}}) API 应与 [`PyConfig`]({{< ref "/c_api/init_config#c.PyConfig" >}}) 一起使用。

- 全局配置变量：

  - [`Py_DebugFlag`]({{< ref "/c_api/init#c.Py_DebugFlag" >}}): 改用 [`PyConfig.parser_debug`]({{< ref "/c_api/init_config#c.PyConfig.parser_debug" >}})。
  - [`Py_VerboseFlag`]({{< ref "/c_api/init#c.Py_VerboseFlag" >}}): 改用 [`PyConfig.verbose`]({{< ref "/c_api/init_config#c.PyConfig.verbose" >}})。
  - [`Py_QuietFlag`]({{< ref "/c_api/init#c.Py_QuietFlag" >}}): 改用 [`PyConfig.quiet`]({{< ref "/c_api/init_config#c.PyConfig.quiet" >}})。
  - [`Py_InteractiveFlag`]({{< ref "/c_api/init#c.Py_InteractiveFlag" >}}): 改用 [`PyConfig.interactive`]({{< ref "/c_api/init_config#c.PyConfig.interactive" >}})。
  - [`Py_InspectFlag`]({{< ref "/c_api/init#c.Py_InspectFlag" >}}): 改用 [`PyConfig.inspect`]({{< ref "/c_api/init_config#c.PyConfig.inspect" >}})。
  - [`Py_OptimizeFlag`]({{< ref "/c_api/init#c.Py_OptimizeFlag" >}}): 改用 [`PyConfig.optimization_level`]({{< ref "/c_api/init_config#c.PyConfig.optimization_level" >}})。
  - [`Py_NoSiteFlag`]({{< ref "/c_api/init#c.Py_NoSiteFlag" >}}): 改用 [`PyConfig.site_import`]({{< ref "/c_api/init_config#c.PyConfig.site_import" >}})。
  - [`Py_BytesWarningFlag`]({{< ref "/c_api/init#c.Py_BytesWarningFlag" >}}): 改用 [`PyConfig.bytes_warning`]({{< ref "/c_api/init_config#c.PyConfig.bytes_warning" >}})。
  - [`Py_FrozenFlag`]({{< ref "/c_api/init#c.Py_FrozenFlag" >}}): 改用 [`PyConfig.pathconfig_warnings`]({{< ref "/c_api/init_config#c.PyConfig.pathconfig_warnings" >}})。
  - [`Py_IgnoreEnvironmentFlag`]({{< ref "/c_api/init#c.Py_IgnoreEnvironmentFlag" >}}): 改用 [`PyConfig.use_environment`]({{< ref "/c_api/init_config#c.PyConfig.use_environment" >}})。
  - [`Py_DontWriteBytecodeFlag`]({{< ref "/c_api/init#c.Py_DontWriteBytecodeFlag" >}}): 改用 [`PyConfig.write_bytecode`]({{< ref "/c_api/init_config#c.PyConfig.write_bytecode" >}})。
  - [`Py_NoUserSiteDirectory`]({{< ref "/c_api/init#c.Py_NoUserSiteDirectory" >}}): 改用 [`PyConfig.user_site_directory`]({{< ref "/c_api/init_config#c.PyConfig.user_site_directory" >}})。
  - [`Py_UnbufferedStdioFlag`]({{< ref "/c_api/init#c.Py_UnbufferedStdioFlag" >}}): 改用 [`PyConfig.buffered_stdio`]({{< ref "/c_api/init_config#c.PyConfig.buffered_stdio" >}})。
  - [`Py_HashRandomizationFlag`]({{< ref "/c_api/init#c.Py_HashRandomizationFlag" >}}): 改用 [`PyConfig.use_hash_seed`]({{< ref "/c_api/init_config#c.PyConfig.use_hash_seed" >}}) 和 [`PyConfig.hash_seed`]({{< ref "/c_api/init_config#c.PyConfig.hash_seed" >}})。
  - [`Py_IsolatedFlag`]({{< ref "/c_api/init#c.Py_IsolatedFlag" >}}): 改用 [`PyConfig.isolated`]({{< ref "/c_api/init_config#c.PyConfig.isolated" >}})。
  - [`Py_LegacyWindowsFSEncodingFlag`]({{< ref "/c_api/init#c.Py_LegacyWindowsFSEncodingFlag" >}}): 改用 [`PyPreConfig.legacy_windows_fs_encoding`]({{< ref "/c_api/init_config#c.PyPreConfig.legacy_windows_fs_encoding" >}})。
  - [`Py_LegacyWindowsStdioFlag`]({{< ref "/c_api/init#c.Py_LegacyWindowsStdioFlag" >}}): 改用 [`PyConfig.legacy_windows_stdio`]({{< ref "/c_api/init_config#c.PyConfig.legacy_windows_stdio" >}})。
  - `Py_FileSystemDefaultEncoding`: 改用 [`PyConfig.filesystem_encoding`]({{< ref "/c_api/init_config#c.PyConfig.filesystem_encoding" >}})。
  - `Py_HasFileSystemDefaultEncoding`: 改用 [`PyConfig.filesystem_encoding`]({{< ref "/c_api/init_config#c.PyConfig.filesystem_encoding" >}})。
  - `Py_FileSystemDefaultEncodeErrors`: 改用 [`PyConfig.filesystem_errors`]({{< ref "/c_api/init_config#c.PyConfig.filesystem_errors" >}})。
  - `Py_UTF8Mode`: 改用 [`PyPreConfig.utf8_mode`]({{< ref "/c_api/init_config#c.PyPreConfig.utf8_mode" >}})。 (参见 [`Py_PreInitialize()`]({{< ref "/c_api/init_config#c.Py_PreInitialize" >}}))

  [`Py_InitializeFromConfig()`]({{< ref "/c_api/init#c.Py_InitializeFromConfig" >}}) API 应与 [`PyConfig`]({{< ref "/c_api/init_config#c.PyConfig" >}}) 一起使用。

### Python 3.15 中的待移除功能

- 捆绑的 `libmpdecimal` 副本。
- The [`PyImport_ImportModuleNoBlock()`](https://docs.python.org/zh-cn/3.13/c-api/import.html#c.PyImport_ImportModuleNoBlock): 改用 [`PyImport_ImportModule()`](https://docs.python.org/zh-cn/3.13/c-api/import.html#c.PyImport_ImportModule)。
- [`PyWeakref_GetObject()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GetObject) 和 [`PyWeakref_GET_OBJECT()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GET_OBJECT): 改用 [`PyWeakref_GetRef()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GetRef)。
- [`Py_UNICODE`](https://docs.python.org/zh-cn/3.13/c-api/unicode.html#c.Py_UNICODE) 类型和 `Py_UNICODE_WIDE` 宏：改用 `wchar_t`。
- Python 初始化函数
  - [`PySys_ResetWarnOptions()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PySys_ResetWarnOptions): 改为清除 [`sys.warnoptions`]({{< ref "/library/python/sys#sys.warnoptions" >}}) 和 `warnings.filters`。
  - [`Py_GetExecPrefix()`]({{< ref "/c_api/init#c.Py_GetExecPrefix" >}}): Get [`sys.base_exec_prefix`]({{< ref "/library/python/sys#sys.base_exec_prefix" >}}) and [`sys.exec_prefix`]({{< ref "/library/python/sys#sys.exec_prefix" >}}) instead.
  - [`Py_GetPath()`]({{< ref "/c_api/init#c.Py_GetPath" >}}): 改为获取 [`sys.path`]({{< ref "/library/python/sys#sys.path" >}})。
  - [`Py_GetPrefix()`]({{< ref "/c_api/init#c.Py_GetPrefix" >}}): Get [`sys.base_prefix`]({{< ref "/library/python/sys#sys.base_prefix" >}}) and [`sys.prefix`]({{< ref "/library/python/sys#sys.prefix" >}}) instead.
  - [`Py_GetProgramFullPath()`]({{< ref "/c_api/init#c.Py_GetProgramFullPath" >}}): 改为获取 [`sys.executable`]({{< ref "/library/python/sys#sys.executable" >}})。
  - [`Py_GetProgramName()`]({{< ref "/c_api/init#c.Py_GetProgramName" >}}): 改为获取 [`sys.executable`]({{< ref "/library/python/sys#sys.executable" >}})。
  - [`Py_GetPythonHome()`]({{< ref "/c_api/init#c.Py_GetPythonHome" >}}): 改为获取 [`PyConfig.home`]({{< ref "/c_api/init_config#c.PyConfig.home" >}}) 或 [`PYTHONHOME`]({{< ref "/using/cmdline#envvar-PYTHONHOME" >}}) 环境变量。

### 计划在未来版本中移除

​	以下 API 已被弃用，将被移除，但目前尚未确定移除日期。

- [`Py_TPFLAGS_HAVE_FINALIZE`](https://docs.python.org/zh-cn/3.13/c-api/typeobj.html#c.Py_TPFLAGS_HAVE_FINALIZE): 自 Python 3.8 起不再需要。
- [`PyErr_Fetch()`]({{< ref "/c_api/exceptions#c.PyErr_Fetch" >}}): 改用 [`PyErr_GetRaisedException()`]({{< ref "/c_api/exceptions#c.PyErr_GetRaisedException" >}})。
- [`PyErr_NormalizeException()`]({{< ref "/c_api/exceptions#c.PyErr_NormalizeException" >}}): 改用 [`PyErr_GetRaisedException()`]({{< ref "/c_api/exceptions#c.PyErr_GetRaisedException" >}})。
- [`PyErr_Restore()`]({{< ref "/c_api/exceptions#c.PyErr_Restore" >}}): 改用 [`PyErr_SetRaisedException()`]({{< ref "/c_api/exceptions#c.PyErr_SetRaisedException" >}})。
- [`PyModule_GetFilename()`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.PyModule_GetFilename): 改用 [`PyModule_GetFilenameObject()`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.PyModule_GetFilenameObject)。
- [`PyOS_AfterFork()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PyOS_AfterFork): 改用 [`PyOS_AfterFork_Child()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PyOS_AfterFork_Child)。
- [`PySlice_GetIndicesEx()`](https://docs.python.org/zh-cn/3.13/c-api/slice.html#c.PySlice_GetIndicesEx): 改用 [`PySlice_Unpack()`](https://docs.python.org/zh-cn/3.13/c-api/slice.html#c.PySlice_Unpack) and [`PySlice_AdjustIndices()`](https://docs.python.org/zh-cn/3.13/c-api/slice.html#c.PySlice_AdjustIndices)。
- `PyUnicode_AsDecodedObject()`: 改用 [`PyCodec_Decode()`](https://docs.python.org/zh-cn/3.13/c-api/codec.html#c.PyCodec_Decode)。
- `PyUnicode_AsDecodedUnicode()`: 改用 [`PyCodec_Decode()`](https://docs.python.org/zh-cn/3.13/c-api/codec.html#c.PyCodec_Decode)。
- `PyUnicode_AsEncodedObject()`: 改用 [`PyCodec_Encode()`](https://docs.python.org/zh-cn/3.13/c-api/codec.html#c.PyCodec_Encode)。
- `PyUnicode_AsEncodedUnicode()`: 改用 [`PyCodec_Encode()`](https://docs.python.org/zh-cn/3.13/c-api/codec.html#c.PyCodec_Encode)。
- [`PyUnicode_READY()`](https://docs.python.org/zh-cn/3.13/c-api/unicode.html#c.PyUnicode_READY): 自 Python 3.12 起不再需要
- `PyErr_Display()`: 改用 [`PyErr_DisplayException()`]({{< ref "/c_api/exceptions#c.PyErr_DisplayException" >}})。
- `_PyErr_ChainExceptions()`: 改用 `_PyErr_ChainExceptions1()`。
- `PyBytesObject.ob_shash` 成员：改为调用 [`PyObject_Hash()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_Hash)。
- `PyDictObject.ma_version_tag` 成员。
- 线程本地存储 (TLS) API：
  - [`PyThread_create_key()`]({{< ref "/c_api/init#c.PyThread_create_key" >}}): 改用 [`PyThread_tss_alloc()`]({{< ref "/c_api/init#c.PyThread_tss_alloc" >}})。
  - [`PyThread_delete_key()`]({{< ref "/c_api/init#c.PyThread_delete_key" >}}): 改用 [`PyThread_tss_free()`]({{< ref "/c_api/init#c.PyThread_tss_free" >}})。
  - [`PyThread_set_key_value()`]({{< ref "/c_api/init#c.PyThread_set_key_value" >}}): 改用 [`PyThread_tss_set()`]({{< ref "/c_api/init#c.PyThread_tss_set" >}})。
  - [`PyThread_get_key_value()`]({{< ref "/c_api/init#c.PyThread_get_key_value" >}}): 改用 [`PyThread_tss_get()`]({{< ref "/c_api/init#c.PyThread_tss_get" >}})。
  - [`PyThread_delete_key_value()`]({{< ref "/c_api/init#c.PyThread_delete_key_value" >}}): 改用 [`PyThread_tss_delete()`]({{< ref "/c_api/init#c.PyThread_tss_delete" >}})。
  - [`PyThread_ReInitTLS()`]({{< ref "/c_api/init#c.PyThread_ReInitTLS" >}}): 自 Python 3.7 起不再需要。
