+++
title = "弃用"
date = 2024-11-14T22:43:43+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文: [https://docs.python.org/zh-cn/3.13/deprecations/index.html](https://docs.python.org/zh-cn/3.13/deprecations/index.html)
>
> 收录该文档的时间：`2024-11-14T22:13:29+08:00`

# 弃用

## 计划在 Python 3.14 中移除

- [`argparse`](https://docs.python.org/zh-cn/3.13/library/argparse.html#module-argparse): `argparse.BooleanOptionalAction` 的 *type*, *choices* 和 *metavar* 形参已被弃用并将在 3.14 中移除。 （由 Nikita Sobolev 在 [gh-92248](https://github.com/python/cpython/issues/92248) 中贡献。）

- [`ast`](https://docs.python.org/zh-cn/3.13/library/ast.html#module-ast): 以下特性自 Python 3.8 起已在文档中声明弃用，现在当运行时如果它们被访问或使用时将发出 [`DeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#DeprecationWarning)，并将在 Python 3.14 中移除：

  - `ast.Num`
  - `ast.Str`
  - `ast.Bytes`
  - `ast.NameConstant`
  - `ast.Ellipsis`

  请改用 [`ast.Constant`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Constant)。 （由 Serhiy Storchaka 在 [gh-90953](https://github.com/python/cpython/issues/90953) 中贡献。）

- [`asyncio`](https://docs.python.org/zh-cn/3.13/library/asyncio.html#module-asyncio):

  - 子监视器类 [`MultiLoopChildWatcher`](https://docs.python.org/zh-cn/3.13/library/asyncio-policy.html#asyncio.MultiLoopChildWatcher), [`FastChildWatcher`](https://docs.python.org/zh-cn/3.13/library/asyncio-policy.html#asyncio.FastChildWatcher), [`AbstractChildWatcher`](https://docs.python.org/zh-cn/3.13/library/asyncio-policy.html#asyncio.AbstractChildWatcher) 和 [`SafeChildWatcher`](https://docs.python.org/zh-cn/3.13/library/asyncio-policy.html#asyncio.SafeChildWatcher) 已被弃用并将在 Python 3.14 中移除。 （由 Kumar Aditya 在 [gh-94597](https://github.com/python/cpython/issues/94597) 中贡献。）
  - [`asyncio.set_child_watcher()`](https://docs.python.org/zh-cn/3.13/library/asyncio-policy.html#asyncio.set_child_watcher)、[`asyncio.get_child_watcher()`](https://docs.python.org/zh-cn/3.13/library/asyncio-policy.html#asyncio.get_child_watcher)、[`asyncio.AbstractEventLoopPolicy.set_child_watcher()`](https://docs.python.org/zh-cn/3.13/library/asyncio-policy.html#asyncio.AbstractEventLoopPolicy.set_child_watcher) 和 [`asyncio.AbstractEventLoopPolicy.get_child_watcher()`](https://docs.python.org/zh-cn/3.13/library/asyncio-policy.html#asyncio.AbstractEventLoopPolicy.get_child_watcher) 已弃用，并将在 Python 3.14 中移除。（由 Kumar Aditya 在 [gh-94597](https://github.com/python/cpython/issues/94597) 中贡献。）
  - 现在默认事件循环策略的 [`get_event_loop()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.get_event_loop) 方法在当前事件循环未设置并决定创建一个时将发出 [`DeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#DeprecationWarning)。 （由 Serhiy Storchaka 和 Guido van Rossum 在 [gh-100160](https://github.com/python/cpython/issues/100160) 中贡献。）

- [`collections.abc`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#module-collections.abc): 已弃用 [`ByteString`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.ByteString)。 推荐改用 `Sequence` 或 [`Buffer`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Buffer)。 用于类型标注时，则推荐并集运算符，如 `bytes | bytearray`，或 [`collections.abc.Buffer`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Buffer)。 （由 Shantanu Jain 在 [gh-91896](https://github.com/python/cpython/issues/91896) 中贡献。）

- [`email`](https://docs.python.org/zh-cn/3.13/library/email.html#module-email): 已弃用 [`email.utils.localtime()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.localtime) 中的 *isdst* 形参。 （由 Alan Williams 在 [gh-72346](https://github.com/python/cpython/issues/72346) 中贡献。）

- [`importlib.abc`](https://docs.python.org/zh-cn/3.13/library/importlib.html#module-importlib.abc) 中已弃用的类：

  - `importlib.abc.ResourceReader`
  - `importlib.abc.Traversable`
  - `importlib.abc.TraversableResources`

  使用 [`importlib.resources.abc`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.abc.html#module-importlib.resources.abc) 类代替:

  - [`importlib.resources.abc.Traversable`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.abc.html#importlib.resources.abc.Traversable)
  - [`importlib.resources.abc.TraversableResources`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.abc.html#importlib.resources.abc.TraversableResources)

  （由 Jason R. Coombs 和 Hugo van Kemenade 在 [gh-93963](https://github.com/python/cpython/issues/93963) 中贡献。）

- [`itertools`](https://docs.python.org/zh-cn/3.13/library/itertools.html#module-itertools) 具有对 copy, deepcopy 和 pickle 等操作的未写入文档的、低效的、历史上充满问题的且不稳定的支持。 这将在 3.14 中移除以显著减少代码量和维护负担。 （由 Raymond Hettinger 在 [gh-101588](https://github.com/python/cpython/issues/101588) 中贡献。）

- [`multiprocessing`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.html#module-multiprocessing): 默认的启动方法在目前默认使用 `'fork'` 的 Linux, BSD 和其他非 macOS POSIX 平台上将改为更安全的方法 ([gh-84559](https://github.com/python/cpython/issues/84559))。 为此添加运行时警告将带来糟糕的体验因为大部分代码并不会关心这个问题。 当你的代码 *需要* `'fork'` 时请使用 [`get_context()`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.html#multiprocessing.get_context) 或 [`set_start_method()`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.html#multiprocessing.set_start_method) API 来显式地指明。 参见 [上下文和启动方法](https://docs.python.org/zh-cn/3.13/library/multiprocessing.html#multiprocessing-start-methods)。

- [`pathlib`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#module-pathlib): [`is_relative_to()`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.PurePath.is_relative_to) 和 [`relative_to()`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.PurePath.relative_to): 传入额外参数的做法已被弃用。

- [`pkgutil`](https://docs.python.org/zh-cn/3.13/library/pkgutil.html#module-pkgutil): 现在 [`find_loader()`](https://docs.python.org/zh-cn/3.13/library/pkgutil.html#pkgutil.find_loader) 和 [`get_loader()`](https://docs.python.org/zh-cn/3.13/library/pkgutil.html#pkgutil.get_loader) 将引发 [`DeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#DeprecationWarning)；请改用 [`importlib.util.find_spec()`](https://docs.python.org/zh-cn/3.13/library/importlib.html#importlib.util.find_spec)。 （由 Nikita Sobolev 在 [gh-97850](https://github.com/python/cpython/issues/97850) 中贡献。）

- [`pty`](https://docs.python.org/zh-cn/3.13/library/pty.html#module-pty):

  - `master_open()`: 使用 [`pty.openpty()`](https://docs.python.org/zh-cn/3.13/library/pty.html#pty.openpty)。
  - `slave_open()`: 使用 [`pty.openpty()`](https://docs.python.org/zh-cn/3.13/library/pty.html#pty.openpty)。

- [`sqlite3`](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#module-sqlite3):

  - [`version`](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#sqlite3.version) 和 [`version_info`](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#sqlite3.version_info)。
  - 如果使用了 [命名占位符](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#sqlite3-placeholders) 且 *parameters* 是一个序列而不是 [`dict`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#dict) 则选择 [`execute()`](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#sqlite3.Cursor.execute) 和 [`executemany()`](https://docs.python.org/zh-cn/3.13/library/sqlite3.html#sqlite3.Cursor.executemany)。

- [`typing`](https://docs.python.org/zh-cn/3.13/library/typing.html#module-typing): [`ByteString`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.ByteString) 自 Python 3.9 起已被弃用，现在当被使用时将会发出 [`DeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#DeprecationWarning)。

- [`urllib`](https://docs.python.org/zh-cn/3.13/library/urllib.html#module-urllib): `urllib.parse.Quoter` 已被弃用：它不应被作为公有 API。 （由 Gregory P. Smith 在 [gh-88168](https://github.com/python/cpython/issues/88168) 中贡献。）

## Python 3.15 中的待移除功能

- 导入系统：
  - 当设置 [`__spec__.cached`](https://docs.python.org/zh-cn/3.13/library/importlib.html#importlib.machinery.ModuleSpec.cached) 失败时在模块上设置 [`__cached__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#module.__cached__) 的做法已被弃用。 在 Python 3.15 中，`__cached__` 将不会再被导入系统或标准库纳入考虑。 ([gh-97879](https://github.com/python/cpython/issues/97879))
  - 当设备 [`__spec__.parent`](https://docs.python.org/zh-cn/3.13/library/importlib.html#importlib.machinery.ModuleSpec.parent) 失败时在模块上设置 [`__package__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#module.__package__) 的做法已被弃用。 在 Python 3.15 中，`__package__` 将不会再被导入系统或标准库纳入考虑。 ([gh-97879](https://github.com/python/cpython/issues/97879))
- [`ctypes`](https://docs.python.org/zh-cn/3.13/library/ctypes.html#module-ctypes):
  - 未写入文档的 `ctypes.SetPointerType()` 函数自 Python 3.13 起已被弃用。
- [`http.server`](https://docs.python.org/zh-cn/3.13/library/http.server.html#module-http.server):
  - 过时且很少被使用的 [`CGIHTTPRequestHandler`](https://docs.python.org/zh-cn/3.13/library/http.server.html#http.server.CGIHTTPRequestHandler) 自 Python 3.13 起已被弃用。 不存在直接的替代品。 对于建立带有请求处理器的 Web 服务程序来说 *任何东西* 都比 CGI 要好。
  - 用于 **python -m http.server** 命令行界面的 `--cgi` 旗标自 Python 3.13 起已被弃用。
- [`locale`](https://docs.python.org/zh-cn/3.13/library/locale.html#module-locale):
  - [`getdefaultlocale()`](https://docs.python.org/zh-cn/3.13/library/locale.html#locale.getdefaultlocale) 函数自 Python 3.11 起已被弃用。 最初计划在 Python 3.13 中移除它 ([gh-90817](https://github.com/python/cpython/issues/90817))，但已被推迟至 Python 3.15。 请改用 [`getlocale()`](https://docs.python.org/zh-cn/3.13/library/locale.html#locale.getlocale), [`setlocale()`](https://docs.python.org/zh-cn/3.13/library/locale.html#locale.setlocale) 和 [`getencoding()`](https://docs.python.org/zh-cn/3.13/library/locale.html#locale.getencoding)。 （由 Hugo van Kemenade 在 [gh-111187](https://github.com/python/cpython/issues/111187) 中贡献。）
- [`pathlib`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#module-pathlib):
  - [`PurePath.is_reserved()`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.PurePath.is_reserved) 自 Python 3.13 起已被弃用。 请使用 [`os.path.isreserved()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.isreserved) 来检测 Windows 上的保留路径。
- [`platform`](https://docs.python.org/zh-cn/3.13/library/platform.html#module-platform):
  - [`java_ver()`](https://docs.python.org/zh-cn/3.13/library/platform.html#platform.java_ver) 自 Python 3.13 起已被弃用。 此函数仅对 Jython 支持有用，具有令人困惑的 API，并且大部分未经测试。
- [`threading`](https://docs.python.org/zh-cn/3.13/library/threading.html#module-threading):
  - 在 Python 3.15 中 [`RLock()`](https://docs.python.org/zh-cn/3.13/library/threading.html#threading.RLock) 将不再接受参数。 传入参数的做法自 Python 3.14 起已被弃用，因为 Python 版本不接受任何参数，而 C 版本允许任意数量的位置或关键字参数，但会忽略所有参数。
- [`types`](https://docs.python.org/zh-cn/3.13/library/types.html#module-types):
  - [`types.CodeType`](https://docs.python.org/zh-cn/3.13/library/types.html#types.CodeType): Accessing [`co_lnotab`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#codeobject.co_lnotab) was deprecated in [**PEP 626**](https://peps.python.org/pep-0626/) since 3.10 and was planned to be removed in 3.12, but it only got a proper [`DeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#DeprecationWarning) in 3.12. May be removed in 3.15. (Contributed by Nikita Sobolev in [gh-101866](https://github.com/python/cpython/issues/101866).)
- [`typing`](https://docs.python.org/zh-cn/3.13/library/typing.html#module-typing):
  - 未写入文档的用于创建 [`NamedTuple`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.NamedTuple) 类的关键字参数语法 (例如 `Point = NamedTuple("Point", x=int, y=int)`) 自 Python 3.13 起已被弃用。 请改用基于类的语法或函数语法。
  - [`typing.no_type_check_decorator()`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.no_type_check_decorator) 装饰器自 Python 3.13 起已被弃用。 存在于 [`typing`](https://docs.python.org/zh-cn/3.13/library/typing.html#module-typing) 模块八年之后，它仍未被任何主要类型检查器所支持。
- [`wave`](https://docs.python.org/zh-cn/3.13/library/wave.html#module-wave):
  - [`Wave_read`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_read) 和 [`Wave_write`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_write) 类的 [`getmark()`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_read.getmark), `setmark()` 和 [`getmarkers()`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_read.getmarkers) 方法自 Python 3.13 起已被弃用。

## Pending removal in Python 3.16

- 导入系统：
  - Setting [`__loader__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#module.__loader__) on a module while failing to set [`__spec__.loader`](https://docs.python.org/zh-cn/3.13/library/importlib.html#importlib.machinery.ModuleSpec.loader) is deprecated. In Python 3.16, `__loader__` will cease to be set or taken into consideration by the import system or the standard library.
- [`array`](https://docs.python.org/zh-cn/3.13/library/array.html#module-array):
  - `'u'` 格式代码 (`wchar_t`) 自 Python 3.3 起已在文档中弃用并自 Python 3.13 起在运行时弃用。 对于 Unicode 字符请改用 `'w'` 格式代码 ([`Py_UCS4`](https://docs.python.org/zh-cn/3.13/c-api/unicode.html#c.Py_UCS4))。
- [`asyncio`](https://docs.python.org/zh-cn/3.13/library/asyncio.html#module-asyncio):
  - `asyncio.iscoroutinefunction()` is deprecated and will be removed in Python 3.16, use [`inspect.iscoroutinefunction()`](https://docs.python.org/zh-cn/3.13/library/inspect.html#inspect.iscoroutinefunction) instead. (Contributed by Jiahao Li and Kumar Aditya in [gh-122875](https://github.com/python/cpython/issues/122875).)
- [`builtins`](https://docs.python.org/zh-cn/3.13/library/builtins.html#module-builtins):
  - 对布尔类型 `~True` 或 `~False` 执行按位取反的操作自 Python 3.12 起已被弃用，因为它会产生奇怪和不直观的结果 (`-2` and `-1`)。 请改用 `not x` 来对布尔值执行逻辑否操作。 对于需要对下层整数执行按位取反操作的少数场合，请显式地将其转换为 `int` (`~int(x)`)。
- [`shutil`](https://docs.python.org/zh-cn/3.13/library/shutil.html#module-shutil):
  - `ExecError` 异常自 Python 3.14 起已被弃用。 它自 Python 3.4 起就未被 `shutil` 中的任何函数所使用，现在是 [`RuntimeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#RuntimeError) 的一个别名。
- [`symtable`](https://docs.python.org/zh-cn/3.13/library/symtable.html#module-symtable):
  - [`Class.get_methods`](https://docs.python.org/zh-cn/3.13/library/symtable.html#symtable.Class.get_methods) 方法自 Python 3.14 起被弃用。
- [`sys`](https://docs.python.org/zh-cn/3.13/library/sys.html#module-sys):
  - [`_enablelegacywindowsfsencoding()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys._enablelegacywindowsfsencoding) 函数自 Python 3.13 起被弃用。 请改用 [`PYTHONLEGACYWINDOWSFSENCODING`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONLEGACYWINDOWSFSENCODING) 环境变量。
- [`tarfile`](https://docs.python.org/zh-cn/3.13/library/tarfile.html#module-tarfile):
  - 未写入文档也未被使用的 `TarFile.tarfile` 属性自 Python 3.13 起被弃用。

## 计划在未来版本中移除

​	以下API将会被移除，尽管具体时间还未确定。

- [`argparse`](https://docs.python.org/zh-cn/3.13/library/argparse.html#module-argparse): 嵌套参数分组和嵌套互斥分组的做法已被弃用。
- [`array`](https://docs.python.org/zh-cn/3.13/library/array.html#module-array) 的 `'u'` 格式代码 ([gh-57281](https://github.com/python/cpython/issues/57281))
- [`builtins`](https://docs.python.org/zh-cn/3.13/library/builtins.html#module-builtins):
  - `bool(NotImplemented)`。
  - 生成器: `throw(type, exc, tb)` 和 `athrow(type, exc, tb)` 签名已被弃用：请改用 `throw(exc)` 和 `athrow(exc)`，即单参数签名。
  - 目前 Python 接受数字类字面值后面紧跟关键字的写法，例如 `0in x`, `1or x`, `0if 1else 2`。 它允许像 `[0x1for x in y]` 这样令人困惑且有歧义的表达式 (它可以被解读为 `[0x1 for x in y]` 或者 `[0x1f or x in y]`)。 如果数字类字面值后面紧跟关键字 [`and`](https://docs.python.org/zh-cn/3.13/reference/expressions.html#and), [`else`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#else), [`for`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#for), [`if`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#if), [`in`](https://docs.python.org/zh-cn/3.13/reference/expressions.html#in), [`is`](https://docs.python.org/zh-cn/3.13/reference/expressions.html#is) 和 [`or`](https://docs.python.org/zh-cn/3.13/reference/expressions.html#or) 中的一个将会引发语法警告。 在未来的版本中它将改为语法错误。 ([gh-87999](https://github.com/python/cpython/issues/87999))
  - 对 `__index__()` 和 `__int__()` 方法返回非 int 类型的支持：将要求这些方法必须返回 [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int) 的子类的实例。
  - 对 `__float__()` 方法返回 [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float) 的子类的支持：将要求这些方法必须返回 [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float) 的实例。
  - 对 `__complex__()` 方法返回 [`complex`](https://docs.python.org/zh-cn/3.13/library/functions.html#complex) 的子类的支持：将要求这些方法必须返回 [`complex`](https://docs.python.org/zh-cn/3.13/library/functions.html#complex) 的实例。
  - 将 `int()` 委托给 `__trunc__()` 方法。
  - 传入一个复数作为 [`complex()`](https://docs.python.org/zh-cn/3.13/library/functions.html#complex) 构造器中的 *real* 或 *imag* 参数的做法现在已被弃用；它应当仅作为单个位置参数被传入。 （由 Serhiy Storchaka 在 [gh-109218](https://github.com/python/cpython/issues/109218) 中贡献。）.)
- [`calendar`](https://docs.python.org/zh-cn/3.13/library/calendar.html#module-calendar): `calendar.January` 和 `calendar.February` 常量已被弃用并由 [`calendar.JANUARY`](https://docs.python.org/zh-cn/3.13/library/calendar.html#calendar.JANUARY) 和 [`calendar.FEBRUARY`](https://docs.python.org/zh-cn/3.13/library/calendar.html#calendar.FEBRUARY) 替代。 （由 Prince Roshan 在 [gh-103636](https://github.com/python/cpython/issues/103636) 中贡献。）
- [`codeobject.co_lnotab`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#codeobject.co_lnotab): 改用 [`codeobject.co_lines()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#codeobject.co_lines) 方法。
- [`datetime`](https://docs.python.org/zh-cn/3.13/library/datetime.html#module-datetime):
  - [`utcnow()`](https://docs.python.org/zh-cn/3.13/library/datetime.html#datetime.datetime.utcnow): 使用 `datetime.datetime.now(tz=datetime.UTC)`。
  - [`utcfromtimestamp()`](https://docs.python.org/zh-cn/3.13/library/datetime.html#datetime.datetime.utcfromtimestamp): 使用 `datetime.datetime.fromtimestamp(timestamp, tz=datetime.UTC)`。
- [`gettext`](https://docs.python.org/zh-cn/3.13/library/gettext.html#module-gettext): 复数值必须是一个整数。
- [`importlib`](https://docs.python.org/zh-cn/3.13/library/importlib.html#module-importlib):
  - `load_module()` 方法：改用 `exec_module()`。
  - [`cache_from_source()`](https://docs.python.org/zh-cn/3.13/library/importlib.html#importlib.util.cache_from_source) *debug_override* 形参已被弃用：改用 *optimization* 形参。
- [`importlib.metadata`](https://docs.python.org/zh-cn/3.13/library/importlib.metadata.html#module-importlib.metadata):
  - `EntryPoints` 元组接口。
  - 返回值中隐式的 `None`。
- [`logging`](https://docs.python.org/zh-cn/3.13/library/logging.html#module-logging): `warn()` 方法自 Python 3.3 起已被弃用，请改用 [`warning()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.warning)。
- [`mailbox`](https://docs.python.org/zh-cn/3.13/library/mailbox.html#module-mailbox): 对 StringIO 输入和文本模式的使用已被弃用，改用 BytesIO 和二进制模式。
- [`os`](https://docs.python.org/zh-cn/3.13/library/os.html#module-os): 在多线程的进程中调用 [`os.register_at_fork()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.register_at_fork)。
- `pydoc.ErrorDuringImport`: 使用元组值作为 *exc_info* 形参的做法已被弃用，应使用异常实例。
- [`re`](https://docs.python.org/zh-cn/3.13/library/re.html#module-re): 现在对于正则表达式中的数字分组引用和分组名称将应用更严格的规则。 现在只接受 ASCII 数字序列作为数字引用。 字节串模式和替换字符串中的分组名称现在只能包含 ASCII 字母和数字以及下划线。 （由 Serhiy Storchaka 在 [gh-91760](https://github.com/python/cpython/issues/91760) 中贡献。）
- `sre_compile`, `sre_constants` 和 `sre_parse` 模块。
- [`shutil`](https://docs.python.org/zh-cn/3.13/library/shutil.html#module-shutil): [`rmtree()`](https://docs.python.org/zh-cn/3.13/library/shutil.html#shutil.rmtree) 的 *onerror* 形参在 Python 3.12 中已被弃用；请改用 *onexc* 形参。
- [`ssl`](https://docs.python.org/zh-cn/3.13/library/ssl.html#module-ssl) 选项和协议：
  - [`ssl.SSLContext`](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl.SSLContext) 不带 protocol 参数的做法已被弃用。
  - [`ssl.SSLContext`](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl.SSLContext): [`set_npn_protocols()`](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl.SSLContext.set_npn_protocols) 和 `selected_npn_protocol()` 已被弃用：请改用 ALPN。
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
- [`sysconfig.is_python_build()`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#sysconfig.is_python_build) *check_home* 形参已被弃用并会被忽略。
- [`threading`](https://docs.python.org/zh-cn/3.13/library/threading.html#module-threading) 的方法：
  - `threading.Condition.notifyAll()`: 使用 [`notify_all()`](https://docs.python.org/zh-cn/3.13/library/threading.html#threading.Condition.notify_all)。
  - `threading.Event.isSet()`: 使用 [`is_set()`](https://docs.python.org/zh-cn/3.13/library/threading.html#threading.Event.is_set)。
  - `threading.Thread.isDaemon()`, [`threading.Thread.setDaemon()`](https://docs.python.org/zh-cn/3.13/library/threading.html#threading.Thread.setDaemon): 使用 [`threading.Thread.daemon`](https://docs.python.org/zh-cn/3.13/library/threading.html#threading.Thread.daemon) 属性。
  - `threading.Thread.getName()`, [`threading.Thread.setName()`](https://docs.python.org/zh-cn/3.13/library/threading.html#threading.Thread.setName): 使用 [`threading.Thread.name`](https://docs.python.org/zh-cn/3.13/library/threading.html#threading.Thread.name) 属性。
  - `threading.currentThread()`: 使用 [`threading.current_thread()`](https://docs.python.org/zh-cn/3.13/library/threading.html#threading.current_thread)。
  - `threading.activeCount()`: 使用 [`threading.active_count()`](https://docs.python.org/zh-cn/3.13/library/threading.html#threading.active_count)。
- [`typing.Text`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.Text) ([gh-92332](https://github.com/python/cpython/issues/92332))。
- [`unittest.IsolatedAsyncioTestCase`](https://docs.python.org/zh-cn/3.13/library/unittest.html#unittest.IsolatedAsyncioTestCase): 从测试用例返回不为 `None` 的值的做法已被弃用。
- [`urllib.parse`](https://docs.python.org/zh-cn/3.13/library/urllib.parse.html#module-urllib.parse) 函数已被弃用：改用 [`urlparse()`](https://docs.python.org/zh-cn/3.13/library/urllib.parse.html#urllib.parse.urlparse)
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
- [`urllib.request`](https://docs.python.org/zh-cn/3.13/library/urllib.request.html#module-urllib.request): 发起请求的 [`URLopener`](https://docs.python.org/zh-cn/3.13/library/urllib.request.html#urllib.request.URLopener) 和 [`FancyURLopener`](https://docs.python.org/zh-cn/3.13/library/urllib.request.html#urllib.request.FancyURLopener) 方式已被弃用。 改用更新 [`urlopen()`](https://docs.python.org/zh-cn/3.13/library/urllib.request.html#urllib.request.urlopen) 函数和方法。
- [`wsgiref`](https://docs.python.org/zh-cn/3.13/library/wsgiref.html#module-wsgiref): `SimpleHandler.stdout.write()` 不应执行部分写入。
- [`xml.etree.ElementTree`](https://docs.python.org/zh-cn/3.13/library/xml.etree.elementtree.html#module-xml.etree.ElementTree): 对 [`Element`](https://docs.python.org/zh-cn/3.13/library/xml.etree.elementtree.html#xml.etree.ElementTree.Element) 的真值测试已被弃用。 在未来的发布版中它将始终返回 `True`。 建议改用显式的 `len(elem)` 或 `elem is not None` 测试。
- [`zipimport.zipimporter.load_module()`](https://docs.python.org/zh-cn/3.13/library/zipimport.html#zipimport.zipimporter.load_module) 已被弃用：请改用 [`exec_module()`](https://docs.python.org/zh-cn/3.13/library/zipimport.html#zipimport.zipimporter.exec_module)。

## C API 的弃用项

### 计划在 Python 3.14 中移除

- [`PyDictObject`](https://docs.python.org/zh-cn/3.13/c-api/dict.html#c.PyDictObject) 中的 `ma_version_tag` 字段用于扩展模块 ( [**PEP 699**](https://peps.python.org/pep-0699/) ; [gh-101193](https://github.com/python/cpython/issues/101193) )。

- 创建 [`immutable types`](https://docs.python.org/zh-cn/3.13/c-api/typeobj.html#c.Py_TPFLAGS_IMMUTABLETYPE) 的可变基础 ( [gh-95388](https://github.com/python/cpython/issues/95388) )。

- 用于配置 Python 的初始化的函数，在 Python 3.11 中已弃用：

  - `PySys_SetArgvEx()`: 改为设置 [`PyConfig.argv`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.argv)。
  - `PySys_SetArgv()`: 改为设置 [`PyConfig.argv`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.argv)。
  - `Py_SetProgramName()`: 改为设置 [`PyConfig.program_name`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.program_name)。
  - `Py_SetPythonHome()`: 改为设置 [`PyConfig.home`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.home)。

  [`Py_InitializeFromConfig()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_InitializeFromConfig) API 应与 [`PyConfig`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig) 一起使用。

- 全局配置变量：

  - [`Py_DebugFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_DebugFlag): 改用 [`PyConfig.parser_debug`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.parser_debug)。
  - [`Py_VerboseFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_VerboseFlag): 改用 [`PyConfig.verbose`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.verbose)。
  - [`Py_QuietFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_QuietFlag): 改用 [`PyConfig.quiet`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.quiet)。
  - [`Py_InteractiveFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_InteractiveFlag): 改用 [`PyConfig.interactive`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.interactive)。
  - [`Py_InspectFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_InspectFlag): 改用 [`PyConfig.inspect`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.inspect)。
  - [`Py_OptimizeFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_OptimizeFlag): 改用 [`PyConfig.optimization_level`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.optimization_level)。
  - [`Py_NoSiteFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_NoSiteFlag): 改用 [`PyConfig.site_import`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.site_import)。
  - [`Py_BytesWarningFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_BytesWarningFlag): 改用 [`PyConfig.bytes_warning`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.bytes_warning)。
  - [`Py_FrozenFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_FrozenFlag): 改用 [`PyConfig.pathconfig_warnings`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.pathconfig_warnings)。
  - [`Py_IgnoreEnvironmentFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_IgnoreEnvironmentFlag): 改用 [`PyConfig.use_environment`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.use_environment)。
  - [`Py_DontWriteBytecodeFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_DontWriteBytecodeFlag): 改用 [`PyConfig.write_bytecode`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.write_bytecode)。
  - [`Py_NoUserSiteDirectory`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_NoUserSiteDirectory): 改用 [`PyConfig.user_site_directory`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.user_site_directory)。
  - [`Py_UnbufferedStdioFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_UnbufferedStdioFlag): 改用 [`PyConfig.buffered_stdio`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.buffered_stdio)。
  - [`Py_HashRandomizationFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_HashRandomizationFlag): 改用 [`PyConfig.use_hash_seed`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.use_hash_seed) 和 [`PyConfig.hash_seed`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.hash_seed)。
  - [`Py_IsolatedFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_IsolatedFlag): 改用 [`PyConfig.isolated`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.isolated)。
  - [`Py_LegacyWindowsFSEncodingFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_LegacyWindowsFSEncodingFlag): 改用 [`PyPreConfig.legacy_windows_fs_encoding`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyPreConfig.legacy_windows_fs_encoding)。
  - [`Py_LegacyWindowsStdioFlag`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_LegacyWindowsStdioFlag): 改用 [`PyConfig.legacy_windows_stdio`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.legacy_windows_stdio)。
  - `Py_FileSystemDefaultEncoding`: 改用 [`PyConfig.filesystem_encoding`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.filesystem_encoding)。
  - `Py_HasFileSystemDefaultEncoding`: 改用 [`PyConfig.filesystem_encoding`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.filesystem_encoding)。
  - `Py_FileSystemDefaultEncodeErrors`: 改用 [`PyConfig.filesystem_errors`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.filesystem_errors)。
  - `Py_UTF8Mode`: 改用 [`PyPreConfig.utf8_mode`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyPreConfig.utf8_mode)。 (参见 [`Py_PreInitialize()`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.Py_PreInitialize))

  [`Py_InitializeFromConfig()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_InitializeFromConfig) API 应与 [`PyConfig`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig) 一起使用。

### Python 3.15 中的待移除功能

- 捆绑的 `libmpdecimal` 副本。
- The [`PyImport_ImportModuleNoBlock()`](https://docs.python.org/zh-cn/3.13/c-api/import.html#c.PyImport_ImportModuleNoBlock): 改用 [`PyImport_ImportModule()`](https://docs.python.org/zh-cn/3.13/c-api/import.html#c.PyImport_ImportModule)。
- [`PyWeakref_GetObject()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GetObject) 和 [`PyWeakref_GET_OBJECT()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GET_OBJECT): 改用 [`PyWeakref_GetRef()`](https://docs.python.org/zh-cn/3.13/c-api/weakref.html#c.PyWeakref_GetRef)。
- [`Py_UNICODE`](https://docs.python.org/zh-cn/3.13/c-api/unicode.html#c.Py_UNICODE) 类型和 `Py_UNICODE_WIDE` 宏：改用 `wchar_t`。
- Python 初始化函数
  - [`PySys_ResetWarnOptions()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PySys_ResetWarnOptions): 改为清除 [`sys.warnoptions`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.warnoptions) 和 `warnings.filters`。
  - [`Py_GetExecPrefix()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_GetExecPrefix): Get [`sys.base_exec_prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.base_exec_prefix) and [`sys.exec_prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.exec_prefix) instead.
  - [`Py_GetPath()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_GetPath): 改为获取 [`sys.path`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.path)。
  - [`Py_GetPrefix()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_GetPrefix): Get [`sys.base_prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.base_prefix) and [`sys.prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.prefix) instead.
  - [`Py_GetProgramFullPath()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_GetProgramFullPath): 改为获取 [`sys.executable`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.executable)。
  - [`Py_GetProgramName()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_GetProgramName): 改为获取 [`sys.executable`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.executable)。
  - [`Py_GetPythonHome()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_GetPythonHome): 改为获取 [`PyConfig.home`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.home) 或 [`PYTHONHOME`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONHOME) 环境变量。

### 计划在未来版本中移除

​	以下 API 已被弃用，将被移除，但目前尚未确定移除日期。

- [`Py_TPFLAGS_HAVE_FINALIZE`](https://docs.python.org/zh-cn/3.13/c-api/typeobj.html#c.Py_TPFLAGS_HAVE_FINALIZE): 自 Python 3.8 起不再需要。
- [`PyErr_Fetch()`](https://docs.python.org/zh-cn/3.13/c-api/exceptions.html#c.PyErr_Fetch): 改用 [`PyErr_GetRaisedException()`](https://docs.python.org/zh-cn/3.13/c-api/exceptions.html#c.PyErr_GetRaisedException)。
- [`PyErr_NormalizeException()`](https://docs.python.org/zh-cn/3.13/c-api/exceptions.html#c.PyErr_NormalizeException): 改用 [`PyErr_GetRaisedException()`](https://docs.python.org/zh-cn/3.13/c-api/exceptions.html#c.PyErr_GetRaisedException)。
- [`PyErr_Restore()`](https://docs.python.org/zh-cn/3.13/c-api/exceptions.html#c.PyErr_Restore): 改用 [`PyErr_SetRaisedException()`](https://docs.python.org/zh-cn/3.13/c-api/exceptions.html#c.PyErr_SetRaisedException)。
- [`PyModule_GetFilename()`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.PyModule_GetFilename): 改用 [`PyModule_GetFilenameObject()`](https://docs.python.org/zh-cn/3.13/c-api/module.html#c.PyModule_GetFilenameObject)。
- [`PyOS_AfterFork()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PyOS_AfterFork): 改用 [`PyOS_AfterFork_Child()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PyOS_AfterFork_Child)。
- [`PySlice_GetIndicesEx()`](https://docs.python.org/zh-cn/3.13/c-api/slice.html#c.PySlice_GetIndicesEx): 改用 [`PySlice_Unpack()`](https://docs.python.org/zh-cn/3.13/c-api/slice.html#c.PySlice_Unpack) and [`PySlice_AdjustIndices()`](https://docs.python.org/zh-cn/3.13/c-api/slice.html#c.PySlice_AdjustIndices)。
- `PyUnicode_AsDecodedObject()`: 改用 [`PyCodec_Decode()`](https://docs.python.org/zh-cn/3.13/c-api/codec.html#c.PyCodec_Decode)。
- `PyUnicode_AsDecodedUnicode()`: 改用 [`PyCodec_Decode()`](https://docs.python.org/zh-cn/3.13/c-api/codec.html#c.PyCodec_Decode)。
- `PyUnicode_AsEncodedObject()`: 改用 [`PyCodec_Encode()`](https://docs.python.org/zh-cn/3.13/c-api/codec.html#c.PyCodec_Encode)。
- `PyUnicode_AsEncodedUnicode()`: 改用 [`PyCodec_Encode()`](https://docs.python.org/zh-cn/3.13/c-api/codec.html#c.PyCodec_Encode)。
- [`PyUnicode_READY()`](https://docs.python.org/zh-cn/3.13/c-api/unicode.html#c.PyUnicode_READY): 自 Python 3.12 起不再需要
- `PyErr_Display()`: 改用 [`PyErr_DisplayException()`](https://docs.python.org/zh-cn/3.13/c-api/exceptions.html#c.PyErr_DisplayException)。
- `_PyErr_ChainExceptions()`: 改用 `_PyErr_ChainExceptions1()`。
- `PyBytesObject.ob_shash` 成员：改为调用 [`PyObject_Hash()`](https://docs.python.org/zh-cn/3.13/c-api/object.html#c.PyObject_Hash)。
- `PyDictObject.ma_version_tag` 成员。
- 线程本地存储 (TLS) API：
  - [`PyThread_create_key()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThread_create_key): 改用 [`PyThread_tss_alloc()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThread_tss_alloc)。
  - [`PyThread_delete_key()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThread_delete_key): 改用 [`PyThread_tss_free()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThread_tss_free)。
  - [`PyThread_set_key_value()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThread_set_key_value): 改用 [`PyThread_tss_set()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThread_tss_set)。
  - [`PyThread_get_key_value()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThread_get_key_value): 改用 [`PyThread_tss_get()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThread_tss_get)。
  - [`PyThread_delete_key_value()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThread_delete_key_value): 改用 [`PyThread_tss_delete()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThread_tss_delete)。
  - [`PyThread_ReInitTLS()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyThread_ReInitTLS): 自 Python 3.7 起不再需要。
