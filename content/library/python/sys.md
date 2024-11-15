+++
title = "sys --- 系统相关的形参和函数"
date = 2024-11-15T21:12:39+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/sys.html](https://docs.python.org/zh-cn/3.13/library/sys.html)
>
> 收录该文档的时间：`2024-11-15T21:12:39+08:00`

# `sys` --- 系统相关的形参和函数

------

​	该模块提供了一些变量和函数。这些变量可能被解释器使用，也可能由解释器提供。这些函数会影响解释器。本模块总是可用的。

## sys.**abiflags**

​	在POSIX系统上，以标准的 `configure` 脚本构建的 Python 中，这个变量会包含 [**PEP 3149**](https://peps.python.org/pep-3149/) 中定义的ABI标签。

*Added in version 3.2.*

*在 3.8 版本发生变更:* 默认的 flags 变为了空字符串（用于 pymalloc 的 `m` 旗标已经移除）

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): Unix.

## sys.**addaudithook**(*hook*)

​	将可调用的对象 *hook* 附加到当前（子）解释器的活动的审计钩子列表中。

​	当通过 [`sys.audit()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.audit) 函数引发审计事件时，每个钩子将按照其被加入的先后顺序被调用，调用时会传入事件名称和参数元组。 由 [`PySys_AddAuditHook()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PySys_AddAuditHook) 添加的原生钩子会先被调用，然后是当前（子）解释器中添加的钩子。 接下来这些钩子会记录事件，引发异常来中止操作，或是完全终止进程。

​	请注意审计钩子主要是用于收集有关内部或在其他情况下不可观察操作的信息，可能是通过 Python 或者用 Python 编写的库。 它们不适合用于实现“沙盒”。 特别重要的一点是，恶意代码可以轻易地禁用或绕过使用此函数添加的钩子。 至少，在初始化运行时之前必须使用 C API [`PySys_AddAuditHook()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PySys_AddAuditHook) 来添加任何安全敏感的钩子，并且应当完全删除或密切监视任何允许任意修改内存的模块 (如 [`ctypes`](https://docs.python.org/zh-cn/3.13/library/ctypes.html#module-ctypes))。

​	调用 [`sys.addaudithook()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.addaudithook) 时它自身将引发一个名为 `sys.addaudithook` 的审计事件且不附带参数。 如果任何现有的钩子引发了派生自 [`RuntimeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#RuntimeError) 的异常，则新的钩子不会被添加并且该异常会被抑制。 其结果就是，调用者无法确保他们的钩子已经被添加，除非他们控制了全部现有的钩子。

​	请参阅 [审计事件表](https://docs.python.org/zh-cn/3.13/library/audit_events.html#audit-events) 以获取由 CPython 引发的所有事件，并参阅 [**PEP 578**](https://peps.python.org/pep-0578/) 了解最初的设计讨论。

*Added in version 3.8.*

*在 3.8.1 版本发生变更:* 派生自 [`Exception`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#Exception) （而非 [`RuntimeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#RuntimeError) ）的异常不会被抑制。

**CPython 实现细节：** 启用跟踪时（参阅 [`settrace()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.settrace) ），仅当可调用对象（钩子）的 `__cantrace__` 成员设置为 true 时，才会跟踪该钩子。否则，跟踪功能将跳过该钩子。

## sys.**argv**

​	一个列表，其中包含了被传递给 Python 脚本的命令行参数。 `argv[0]` 为脚本的名称（是否是完整的路径名取决于操作系统）。如果是通过 Python 解释器的命令行参数 [`-c`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-c) 来执行的， `argv[0]` 会被设置成字符串 `'-c'` 。如果没有脚本名被传递给 Python 解释器， `argv[0]` 为空字符串。

​	为了遍历标准输入，或者通过命令行传递的文件列表，参照 [`fileinput`](https://docs.python.org/zh-cn/3.13/library/fileinput.html#module-fileinput) 模块

​	另请参阅 [`sys.orig_argv`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.orig_argv)。

​	备注

 

​	在 Unix 上，系统传递的命令行参数是字节类型的。Python 使用文件系统编码和 "surrogateescape" 错误处理方案对它们进行解码。当需要原始字节时，可以通过 `[os.fsencode(arg) for arg in sys.argv]` 来获取。

## sys.**audit**(*event*, **args*)

​	引发一个审计事件并触发任何激活的审计钩子。 *event* 是一个用于标识事件的字符串，*args* 会包含有关事件的更多信息的可选参数。 特定事件的参数的数量和类型会被视为是公有的稳定 API 且不应当在版本之间进行修改。

​	例如，有一个审计事件的名称为 `os.chdir`。 此事件具有一个名为 *path* 的参数，该参数将包含所请求的新工作目录。

[`sys.audit()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.audit) 将调用现有的审计钩子，传入事件名称和参数，并将重新引发来自任何钩子的第一个异常。 通常来说，如果有一个异常被引发，则它不应当被处理且其进程应当被尽可能快地终止。 这将允许钩子实现来决定对特定事件要如何反应：它们可以只是将事件写入日志或是通过引发异常来中止操作。

​	钩子程序由 [`sys.addaudithook()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.addaudithook) 或 [`PySys_AddAuditHook()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PySys_AddAuditHook) 函数添加。

​	与本函数相等效的原生函数是 [`PySys_Audit()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PySys_Audit)，应尽量使用原生函数。

​	参阅 [审计事件表](https://docs.python.org/zh-cn/3.13/library/audit_events.html#audit-events) 以获取 CPython 定义的所有审计事件。

*Added in version 3.8.*

## sys.**base_exec_prefix**

​	在 `site.py` 运行之前， Python 启动的时候被设置为跟 [`exec_prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.exec_prefix) 同样的值。如果不是运行在 [虚拟环境](https://docs.python.org/zh-cn/3.13/library/venv.html#venv-def) 中，两个值会保持相同；如果 `site.py` 发现处于一个虚拟环境中， [`prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.prefix) 和 [`exec_prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.exec_prefix) 将会指向虚拟环境。然而 [`base_prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.base_prefix) 和 [`base_exec_prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.base_exec_prefix) 将仍然会指向基础的 Python 环境（用来创建虚拟环境的 Python 环境）

*Added in version 3.3.*

## sys.**base_prefix**

​	在 `site.py` 运行之前， Python 启动的时候被设置为跟 [`prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.prefix) 同样的值。如果不是运行在 [虚拟环境](https://docs.python.org/zh-cn/3.13/library/venv.html#venv-def) 中, 两个值会保持相同；如果 `site.py` 发现处于一个虚拟环境中， [`prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.prefix) 和 [`exec_prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.exec_prefix) 将会指向虚拟环境。然而 [`base_prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.base_prefix) 和 [`base_exec_prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.base_exec_prefix) 将仍然会指向基础的 Python 环境（用来创建虚拟环境的 Python 环境）

*Added in version 3.3.*

## sys.**byteorder**

​	本地字节顺序的指示符。在大端序（最高有效位优先）操作系统上值为 `'big'` ，在小端序（最低有效位优先）操作系统上为 `'little'` 。

## sys.**builtin_module_names**

​	一个包含所有被编译进 Python 解释器的模块的名称的字符串元组。 （此信息无法通过任何其他办法获取 --- `modules.keys()` 仅会列出导入的模块。）

​	另请参阅 [`sys.stdlib_module_names`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stdlib_module_names) 列表。

## sys.**call_tracing**(*func*, *args*)

​	当启用跟踪时，调用 `func(*args)`。 跟踪状态将被保存，并在以后恢复。 这被设计为由调试器从某个检查点执行调用，以便递归地调试或分析某些其他代码。

​	在调用由 [`settrace()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.settrace) 或 [`setprofile()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.setprofile) 设置的跟踪函数时跟踪将暂停以避免无限递归。 `call_tracing()` 会启用跟踪函数的显式递归。

## sys.**copyright**

​	一个字符串，包含了 Python 解释器有关的版权信息

## sys.**_clear_type_cache**()

​	清除内部的类型缓存。类型缓存是为了加速查找方法和属性的。在调试引用泄漏的时候调用这个函数 *只会* 清除不必要的引用。

​	这个函数应该只在内部为了一些特定的目的使用。

*自 3.13 版本弃用:* 改用更一般化的 [`_clear_internal_caches()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys._clear_internal_caches) 函数。

## sys.**_clear_internal_caches**()

​	清空所有内部性能相关的缓存。 此函数的使用 *仅限于* 释放不再需要的引用和寻找泄漏的内存块时。

*Added in version 3.13.*

## sys.**_current_frames**()

​	返回一个字典，存放着每个线程的标识符与（调用本函数时）该线程栈顶的帧（当前活动的帧）之间的映射。注意 [`traceback`](https://docs.python.org/zh-cn/3.13/library/traceback.html#module-traceback) 模块中的函数可以在给定某一帧的情况下构建调用堆栈。

​	这对于调试死锁最有用：本函数不需要死锁线程的配合，并且只要这些线程的调用栈保持死锁，它们就是冻结的。在调用本代码来检查栈顶的帧的那一刻，非死锁线程返回的帧可能与该线程当前活动的帧没有任何关系。

​	这个函数应该只在内部为了一些特定的目的使用。

​	引发一个不带参数的 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `sys._current_frames`。

## sys.**_current_exceptions**()

​	返回一个字典，存放着每个线程的标识与调用此函数时该线程当前活动帧的栈顶异常之间的映射。 如果某个线程当前未在处理异常，它将不被包括在结果字典中。

​	这对于静态性能分析来说最为有用。

​	这个函数应该只在内部为了一些特定的目的使用。

​	引发一个不带参数的 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `sys._current_exceptions`。

*在 3.12 版本发生变更:* 现在字典中的每个值都是单独的异常实例，而不是如 `sys.exc_info()` 所返回的 3 元组。

## sys.**breakpointhook**()

​	本钩子函数由内建函数 [`breakpoint()`](https://docs.python.org/zh-cn/3.13/library/functions.html#breakpoint) 调用。默认情况下，它将进入 [`pdb`](https://docs.python.org/zh-cn/3.13/library/pdb.html#module-pdb) 调试器，但可以将其改为任何其他函数，以选择使用哪个调试器。

​	该函数的特征取决于其调用的函数。例如，默认绑定（即 `pdb.set_trace()` ）不要求提供参数，但可以将绑定换成要求提供附加参数（位置参数/关键字参数）的函数。内建函数 `breakpoint()` 直接将其 `*args` 和 `**kws` 传入。`breakpointhooks()` 返回的所有内容都会从 `breakpoint()` 返回。

​	默认的实现首先会查询环境变量 [`PYTHONBREAKPOINT`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONBREAKPOINT)。如果将该变量设置为 `"0"`，则本函数立即返回，表示在断点处无操作。如果未设置该环境变量或将其设置为空字符串，则调用 `pdb.set_trace()`。否则，此变量应指定要运行的函数，指定函数时应使用 Python 的点导入命名法，如 `package.subpackage.module.function`。这种情况下将导入 `package.subpackage.module`，且导入的模块必须有一个名为 `function()` 的可调用对象。该可调用对象会运行，`*args` 和 `**kws` 会传入，且无论 `function()` 返回什么，`sys.breakpointhook()` 都将返回到內建函数 [`breakpoint()`](https://docs.python.org/zh-cn/3.13/library/functions.html#breakpoint)。

​	请注意，如果在导入 [`PYTHONBREAKPOINT`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONBREAKPOINT) 指定的可调用对象时出错，则将报告一个 [`RuntimeWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#RuntimeWarning) 并忽略断点。

​	另请注意，如果以编程方式覆盖 `sys.breakpointhook()`，则 *不会* 查询 [`PYTHONBREAKPOINT`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONBREAKPOINT)。

*Added in version 3.7.*

## sys.**_debugmallocstats**()

​	将有关 CPython 内存分配器状态的底层的信息打印至 stderr。

​	如果 Python 是 [以调试模式编译的](https://docs.python.org/zh-cn/3.13/using/configure.html#debug-build) ([`使用 --with-pydebug 配置选项`](https://docs.python.org/zh-cn/3.13/using/configure.html#cmdoption-with-pydebug))，它还会执行某些高开销的内部一致性检查。

*Added in version 3.3.*

**CPython 实现细节：** 本函数仅限 CPython。此处没有定义确切的输出格式，且可能会更改。

## sys.**dllhandle**

​	指向 Python DLL 句柄的整数。

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): Windows.

## sys.**displayhook**(*value*)

​	如果 *value* 不是 `None`，则本函数会将 `repr(value)` 打印至 `sys.stdout`，并将 *value* 保存在 `builtins._` 中。如果 `repr(value)` 无法用 `sys.stdout.errors` 错误处理方案（可能为 `'strict'` ）编码为 `sys.stdout.encoding`，则用 `'backslashreplace'` 错误处理方案将其编码为 `sys.stdout.encoding`。

​	在交互式 Python 会话中运行 [expression](https://docs.python.org/zh-cn/3.13/glossary.html#term-expression) 产生结果后，将在结果上调用 `sys.displayhook`。若要自定义这些 value 的显示，可以将 `sys.displayhook` 指定为另一个单参数函数。

​	伪代码:

```
def displayhook(value):
    if value is None:
        return
    # 将 '_' 设为 None 以避免继续递归
    builtins._ = None
    text = repr(value)
    try:
        sys.stdout.write(text)
    except UnicodeEncodeError:
        bytes = text.encode(sys.stdout.encoding, 'backslashreplace')
        if hasattr(sys.stdout, 'buffer'):
            sys.stdout.buffer.write(bytes)
        else:
            text = bytes.decode(sys.stdout.encoding, 'strict')
            sys.stdout.write(text)
    sys.stdout.write("\n")
    builtins._ = value
```

*在 3.2 版本发生变更:* 在发生 [`UnicodeEncodeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#UnicodeEncodeError) 时使用 `'backslashreplace'` 错误处理方案。

## sys.**dont_write_bytecode**

​	如果该值为 true，则 Python 在导入源码模块时将不会尝试写入 `.pyc` 文件。该值会被初始化为 `True` 或 `False`，依据是 [`-B`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-B) 命令行选项和 [`PYTHONDONTWRITEBYTECODE`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONDONTWRITEBYTECODE) 环境变量，可以自行设置该值，来控制是否生成字节码文件。

## sys.**_emscripten_info**

​	这个 [named tuple](https://docs.python.org/zh-cn/3.13/glossary.html#term-named-tuple) 保存了 *wasm32-emscripten* 平台中环境的相关信息。 该命名元组处于暂定状态并可能在将来被更改。

## _emscripten_info.**emscripten_version**

​	以整数元组 (major, minor, micro) 表示的 Emscripten 版本，例如 `(3, 1, 8)`。

## _emscripten_info.**runtime**

​	运行时字符串，例如 browser user agent, `'Node.js v14.18.2'` 或 `'UNKNOWN'`。

## _emscripten_info.**pthreads**

​	如果 Python 编译附带了 Emscripten pthreads 支持则为 `True`。

## _emscripten_info.**shared_memory**

​	如果 Python 编译附带了共享内存支持则为 `True`。

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): Emscripten.

*Added in version 3.11.*

## sys.**pycache_prefix**

​	如果设置了该值 (不能为 `None`)，Python 会将字节码缓存文件 `.pyc` 写入到以该值指定的目录为根的并行目录树中（并从中读取），而不是在源代码树的 `__pycache__` 目录下读写。 源代码树中所有的 `__pycache__` 目录都将被忽略并且新的 `.pyc` 文件将被写入到 pycache 前缀指定的位置。 因此如果你使用 [`compileall`](https://docs.python.org/zh-cn/3.13/library/compileall.html#module-compileall) 作为预编译步骤，你必须确保使用与在运行时相同的 pycache 前缀（如果有的话）来运行它。

​	相对路径将解释为相对于当前工作目录。

​	该值的初值设置，依据 [`-X`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-X) `pycache_prefix=PATH` 命令行选项或 [`PYTHONPYCACHEPREFIX`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONPYCACHEPREFIX) 环境变量的值（命令行优先）。如果两者均未设置，则为 `None`。

*Added in version 3.8.*

## sys.**excepthook**(*type*, *value*, *traceback*)

​	本函数会将所给的回溯和异常输出到 `sys.stderr` 中。

​	当有 [`SystemExit`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#SystemExit) 以外的异常被引发且未被捕获时，解释器会调用 `sys.excepthook` 并附带三个参数：异常类、异常实例和回溯对象。 在交互会话中这将发生在控制返回提示符之前；在 Python 程序中这将发生在程序退出之前。 这种最高层级异常的处理可以通过为 `sys.excepthook` 指定另一个三参数函数来实现自定义。

​	当发生未捕获的异常时，引发一个审计事件 `sys.excepthook`，附带参数 `hook`, `type`, `value`, `traceback`。如果没有设置钩子，`hook` 可能为 `None`。如果某个钩子抛出了派生自 [`RuntimeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#RuntimeError) 的异常，则将禁止对该钩子的调用。否则，审计钩子的异常将被报告为无法抛出，并将调用 `sys.excepthook`。

​	参见

 

[`sys.unraisablehook()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.unraisablehook) 函数处理无法抛出的异常，[`threading.excepthook()`](https://docs.python.org/zh-cn/3.13/library/threading.html#threading.excepthook) 函数处理 [`threading.Thread.run()`](https://docs.python.org/zh-cn/3.13/library/threading.html#threading.Thread.run) 抛出的异常。

## sys.**__breakpointhook__**

## sys.**__displayhook__**

## sys.**__excepthook__**

## sys.**__unraisablehook__**

​	程序开始时，这些对象存有 `breakpointhook`、`displayhook`、`excepthook` 和 `unraisablehook` 的初始值。保存它们是为了可以在 `breakpointhook`、`displayhook` 和 `excepthook`、`unraisablehook` 被破坏或被替换时恢复它们。

*Added in version 3.7:* __breakpointhook__

*Added in version 3.8:* __unraisablehook__

## sys.**exception**()

​	当此函数在某个异常处理器执行过程中（如 `except` 或 `except*` 子句）被调用时，将返回被该处理器所捕获的异常实例。 当有多个异常处理器彼此嵌套时，只有最内层处理器所处理的异常可以被访问到。

​	如果没有任何异常处理器在执行，此函数将返回 `None`。

*Added in version 3.11.*

## sys.**exc_info**()

​	此函数返回被处理异常的旧式表示形式。 如果异常 `e` 当前已被处理 (因此 [`exception()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.exception) 将会返回 `e`)，则 [`exc_info()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.exc_info) 将返回元组 `(type(e), e, e.__traceback__)`。 也就是说，包含该异常类型 ([`BaseException`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BaseException) 的子类) 的元组，异常本身，以及一个通常封装了异常最后发生位置上调用栈的 [回溯对象](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#traceback-objects)。

​	如果堆栈上的任何地方都没有处理异常，则此函数将返回一个包含三个 `None` 的元组。

*在 3.11 版本发生变更:* `type` 和 `traceback` 字段现在是派生自 `value` (异常实例)，因此当一个异常在处理期间被修改时，其变化会在后续对 [`exc_info()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.exc_info) 的调用结果中反映出来。

## sys.**exec_prefix**

​	一个字符串，提供特定域的目录前缀，该目录中安装了与平台相关的 Python 文件，默认也是 `'/usr/local'`。该目录前缀可以在构建时使用 **configure** 脚本的 `--exec-prefix` 参数进行设置。具体而言，所有配置文件（如 `pyconfig.h` 头文件）都安装在目录 `*exec_prefix*/lib/python*X.Y*/config` 中，共享库模块安装在 `*exec_prefix*/lib/python*X.Y*/lib-dynload` 中，其中 *X.Y* 是 Python 的版本号，如 `3.2`。

​	备注

 

​	如果在一个 [虚拟环境](https://docs.python.org/zh-cn/3.13/library/venv.html#venv-def) 中，那么该值将在 `site.py` 中被修改，指向虚拟环境。Python 安装位置仍然可以用 [`base_exec_prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.base_exec_prefix) 来获取。

## sys.**executable**

​	一个字符串，提供 Python 解释器的可执行二进制文件的绝对路径，仅在部分系统中此值有意义。如果 Python 无法获取其可执行文件的真实路径，则 [`sys.executable`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.executable) 将为空字符串或 `None`。

## sys.**exit**([*arg*])

​	引发一个 [`SystemExit`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#SystemExit) 异常，表示打算退出解释器。

​	可选参数 *arg* 可以是表示退出状态的整数（默认为 0），也可以是其他类型的对象。如果它是整数，则 shell 等将 0 视为“成功终止”，非零值视为“异常终止”。大多数系统要求该值的范围是 0--127，否则会产生不确定的结果。某些系统为退出代码约定了特定的含义，但通常尚不完善；Unix 程序通常用 2 表示命令行语法错误，用 1 表示所有其他类型的错误。传入其他类型的对象，如果传入 `None` 等同于传入 0，如果传入其他对象则将其打印至 [`stderr`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stderr)，且退出代码为 1。特别地，`sys.exit("some error message")` 可以在发生错误时快速退出程序。

​	由于 [`exit()`](https://docs.python.org/zh-cn/3.13/library/constants.html#exit) 最终 "只" 引发了一个异常，它只在从主线程调用时退出进程，而异常不会被拦截。 [`try`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#try) 语句的 finally 子句所指定的清理动作会被遵守，并且有可能在外层拦截退出的尝试。

*在 3.6 版本发生变更:* 在 Python 解释器捕获 [`SystemExit`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#SystemExit) 后，如果在清理中发生错误（如清除标准流中的缓冲数据时出错），则退出状态码将变为 120。

## sys.**flags**

[具名元组](https://docs.python.org/zh-cn/3.13/glossary.html#term-named-tuple) *flags* 含有命令行标志的状态。这些属性是只读的。

| flags.**debug**                 | [`-d`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-d) |
| ------------------------------- | ------------------------------------------------------------ |
| flags.**inspect**               | [`-i`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-i) |
| flags.**interactive**           | [`-i`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-i) |
| flags.**isolated**              | [`-I`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-I) |
| flags.**optimize**              | [`-O`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-O) 或 [`-OO`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-OO) |
| flags.**dont_write_bytecode**   | [`-B`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-B) |
| flags.**no_user_site**          | [`-s`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-s) |
| flags.**no_site**               | [`-S`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-S) |
| flags.**ignore_environment**    | [`-E`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-E) |
| flags.**verbose**               | [`-v`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-v) |
| flags.**bytes_warning**         | [`-b`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-b) |
| flags.**quiet**                 | [`-q`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-q) |
| flags.**hash_randomization**    | [`-R`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-R) |
| flags.**dev_mode**              | [`-X dev`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-X) ([Python 开发模式](https://docs.python.org/zh-cn/3.13/library/devmode.html#devmode)) |
| flags.**utf8_mode**             | [`-X utf8`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-X) |
| flags.**safe_path**             | [`-P`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-P) |
| flags.**int_max_str_digits**    | [`-X int_max_str_digits`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-X) ([integer string conversion length limitation](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#int-max-str-digits)) |
| flags.**warn_default_encoding** | [`-X warn_default_encoding`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-X) |

*在 3.2 版本发生变更:* 为新的 [`-q`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-q) 标志添加了 `quiet` 属性

*Added in version 3.2.3:* `hash_randomization` 属性

*在 3.3 版本发生变更:* 删除了过时的 `division_warning` 属性

*在 3.4 版本发生变更:* 为 [`-I`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-I) `isolated` 标志添加了 `isolated` 属性。

*在 3.7 版本发生变更:* 为新的 [Python 开发模式](https://docs.python.org/zh-cn/3.13/library/devmode.html#devmode) 添加了 `dev_mode` 属性，为新的 [`-X`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-X) `utf8` 标志添加了 `utf8_mode` 属性。

*在 3.10 版本发生变更:* 为 [`-X`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-X) `warn_default_encoding` 旗标添加了 `warn_default_encoding` 属性。

*在 3.11 版本发生变更:* 添加了用于 [`-P`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-P) 选项的 `safe_path` 属性。

*在 3.11 版本发生变更:* 增加了 `int_max_str_digits` 属性。

## sys.**float_info**

​	一个 [具名元组](https://docs.python.org/zh-cn/3.13/glossary.html#term-named-tuple)，存有浮点型的相关信息。它包含的是关于精度和内部表示的底层信息。这些值与标准头文件 `float.h` 中为 C 语言定义的各种浮点常量对应，详情请参阅 1999 ISO/IEC C 标准 [[C99\]](https://docs.python.org/zh-cn/3.13/library/sys.html#c99) 的 5.2.4.2.2 节，'Characteristics of floating types（浮点型的特性）'。

| attribute -- 属性         | float.h 宏       | 说明                                                         |
| :------------------------ | :--------------- | :----------------------------------------------------------- |
| float_info.**epsilon**    | `DBL_EPSILON`    | 1.0 与可表示为浮点数的大于 1.0 的最小值之间的差。另请参阅 [`math.ulp()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.ulp)。 |
| float_info.**dig**        | `DBL_DIG`        | 浮点数可以真实表示的十进制数的最大位数；见下文。             |
| float_info.**mant_dig**   | `DBL_MANT_DIG`   | 浮点数精度：以 `radix` 为基数浮点数的有效位数。              |
| float_info.**max**        | `DBL_MAX`        | 可表示的最大正有限浮点数。                                   |
| float_info.**max_exp**    | `DBL_MAX_EXP`    | 使得 `radix**(e-1)` 是可表示的有限浮点数的最大整数 *e*。     |
| float_info.**max_10_exp** | `DBL_MAX_10_EXP` | 使得 `10**e` 在可表示的有限浮点数范围内的最大整数 *e*。      |
| float_info.**min**        | `DBL_MIN`        | 可表示的最小正 *规范化* 浮点数。使用 [`math.ulp(0.0)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.ulp) 获取可表示的最小正 *非规格化* 浮点数 |
| float_info.**min_exp**    | `DBL_MIN_EXP`    | 使得 `radix**(e-1)` 是规范化浮点数的最小整数 *e*。           |
| float_info.**min_10_exp** | `DBL_MIN_10_EXP` | 使得 `10**e` 是归范化浮点数的最小整数 *e*。                  |
| float_info.**radix**      | `FLT_RADIX`      | 指数表示法中采用的基数。                                     |
| float_info.**rounds**     | `FLT_ROUNDS`     | 一个代表浮点运算舍入模式的整数。 它反映了解释器启动时系统 `FLT_ROUNDS` 宏的值：`-1`: 不确定`0`: 向零值`1`: 向最近值`2`: 向正无穷`3`: 向负无穷`FLT_ROUNDS` 的所有其他值被用于代表具体实现所定义的舍入行为。 |

​	属性 [`sys.float_info.dig`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.float_info.dig) 需要进一步的解释。 如果 `s` 是表示十进制数的字符串，且最多有 `sys.float_info.dig` 位有效数字，那么将 `s` 转换为浮点数再转换回来将恢复为一个表示相同十进制值的字符串:

```
>>> import sys
>>> sys.float_info.dig
15
>>> s = '3.14159265358979'    # 有 15 个有效位的十进制小数字节串
>>> format(float(s), '.15g')  # 转换为浮点数再转换回来 -> 相同的值
'3.14159265358979'
```

​	但是对于超过 [`sys.float_info.dig`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.float_info.dig) 位有效数字的字符串，转换前后并非总是相同:

```
>>> s = '9876543211234567'    # 16 个有效位就太多了！
>>> format(float(s), '.16g')  # 转换将改变原值
'9876543211234568'
```

## sys.**float_repr_style**

​	一个字符串，反映 [`repr()`](https://docs.python.org/zh-cn/3.13/library/functions.html#repr) 函数在浮点数上的行为。如果该字符串是 `'short'`，那么对于（非无穷的）浮点数 `x`，`repr(x)` 将会生成一个短字符串，满足 `float(repr(x)) == x` 的特性。这是 Python 3.1 及更高版本中的常见行为。否则 `float_repr_style` 的值将是 `'legacy'`，此时 `repr(x)` 的行为方式将与 Python 3.1 之前的版本相同。

*Added in version 3.1.*

## sys.**getallocatedblocks**()

​	返回解释器当前已分配的内存块数，无论它们的大小如何。 此函数主要用于跟踪和调试内存泄漏。 因为解释器有内部缓存，所以不同调用的结果会有变化；你可能需要调用 [`_clear_internal_caches()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys._clear_internal_caches) 和 [`gc.collect()`](https://docs.python.org/zh-cn/3.13/library/gc.html#gc.collect) 来获得更可预测的结果。

​	如果一个 Python 构建或实现无法合理地计算此信息，则允许 [`getallocatedblocks()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.getallocatedblocks) 返回 0。

*Added in version 3.4.*

## sys.**getunicodeinternedsize**()

​	返回已被处置的 unicode 对象数量。

*Added in version 3.12.*

## sys.**getandroidapilevel**()

​	以一个整数的形式返回 Android 的构建时级别。 这代表此 Python 构建版可运行的最小 Android 版本。 对于运行时版本信息，请查看 [`platform.android_ver()`](https://docs.python.org/zh-cn/3.13/library/platform.html#platform.android_ver)。

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): Android.

*Added in version 3.7.*

## sys.**getdefaultencoding**()

​	返回当前 Unicode 实现所使用的默认字符串编码格式名称。

## sys.**getdlopenflags**()

​	返回用于 `dlopen()` 调用的旗标的当前值。 旗标值的符号名称可在 [`os`](https://docs.python.org/zh-cn/3.13/library/os.html#module-os) 模块中找到 (`RTLD_*xxx*` 常量，例如 [`os.RTLD_LAZY`](https://docs.python.org/zh-cn/3.13/library/os.html#os.RTLD_LAZY))。

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): Unix.

## sys.**getfilesystemencoding**()

​	获取 [文件系统编码格式](https://docs.python.org/zh-cn/3.13/glossary.html#term-filesystem-encoding-and-error-handler): 该编码格式与 [文件系统错误处理器](https://docs.python.org/zh-cn/3.13/glossary.html#term-filesystem-encoding-and-error-handler) 一起使用以便在 Unicode 文件名和字节文件名之间进行转换。 文件系统错误处理器是从 [`getfilesystemencodeerrors()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.getfilesystemencodeerrors) 返回的。

​	为获得最佳兼容性，在任何时候都应使用 str 来表示文件名，尽管使用 bytes 来表示文件名也是受支持的。 接受还返回文件名的函数应当支持 str 或 bytes 并在内部将其转换为系统首选的表示形式。

​	应使用 [`os.fsencode()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.fsencode) 和 [`os.fsdecode()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.fsdecode) 来保证所采用的编码和错误处理方案都是正确的。

[filesystem encoding and error handler](https://docs.python.org/zh-cn/3.13/glossary.html#term-filesystem-encoding-and-error-handler) 是在 Python 启动时通过 [`PyConfig_Read()`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig_Read) 函数来配置的：请参阅 [`PyConfig`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig) 的 [`filesystem_encoding`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.filesystem_encoding) 和 [`filesystem_errors`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.filesystem_errors) 等成员。

*在 3.2 版本发生变更:* [`getfilesystemencoding()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.getfilesystemencoding) 的结果将不再有可能是 `None`。

*在 3.6 版本发生变更:* Windows 不再保证会返回 `'mbcs'`。详情请参阅 [**PEP 529**](https://peps.python.org/pep-0529/) 和 [`_enablelegacywindowsfsencoding()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys._enablelegacywindowsfsencoding)。

*在 3.7 版本发生变更:* 返回 `'utf-8'`，如果启用了 [Python UTF-8 模式](https://docs.python.org/zh-cn/3.13/library/os.html#utf8-mode) 的话。

## sys.**getfilesystemencodeerrors**()

​	获取 [文件系统错误处理器](https://docs.python.org/zh-cn/3.13/glossary.html#term-filesystem-encoding-and-error-handler): 该错误处理器与 [文件系统编码格式](https://docs.python.org/zh-cn/3.13/glossary.html#term-filesystem-encoding-and-error-handler) 一起使用以便在 Unicode 文件名和字节文件名之间进程转换。 文件系统编码格式是由 [`getfilesystemencoding()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.getfilesystemencoding) 来返回的。

​	应使用 [`os.fsencode()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.fsencode) 和 [`os.fsdecode()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.fsdecode) 来保证所采用的编码和错误处理方案都是正确的。

[filesystem encoding and error handler](https://docs.python.org/zh-cn/3.13/glossary.html#term-filesystem-encoding-and-error-handler) 是在 Python 启动时通过 [`PyConfig_Read()`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig_Read) 函数来配置的：请参阅 [`PyConfig`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig) 的 [`filesystem_encoding`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.filesystem_encoding) 和 [`filesystem_errors`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.filesystem_errors) 等成员。

*Added in version 3.6.*

## sys.**get_int_max_str_digits**()

​	返回 [整数字符串转换长度限制](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#int-max-str-digits) 的当前值。 另请参阅 [`set_int_max_str_digits()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.set_int_max_str_digits)。

*Added in version 3.11.*

## sys.**getrefcount**(*object*)

​	返回 *object* 的引用计数。返回的计数通常比预期的多一，因为它包括了作为 [`getrefcount()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.getrefcount) 参数的这一次（临时）引用。

​	请注意返回的值可能并不真正反映实际持有的对象引用数。 例如，有些对象属于 [immortal](https://docs.python.org/zh-cn/3.13/glossary.html#term-immortal) 对象并具有并不反映实际引用数的非常高的 refcount 值。 因此，除了 0 或 1 这两个值，不要依赖返回值的准确性。

*在 3.12 版本发生变更:* 永生对象具有与对象的实际引用次数不相符的非常大的引用计数。

## sys.**getrecursionlimit**()

​	返回当前的递归限制值，即 Python 解释器堆栈的最大深度。此限制可防止无限递归导致的 C 堆栈溢出和 Python 崩溃。该值可以通过 [`setrecursionlimit()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.setrecursionlimit) 设置。

## sys.**getsizeof**(*object*[, *default*])

​	返回对象的大小（以字节为单位）。该对象可以是任何类型。所有内建对象返回的结果都是正确的，但对于第三方扩展不一定正确，因为这与具体实现有关。

​	只计算直接分配给对象的内存消耗，不计算它所引用的对象的内存消耗。

​	对象不提供计算大小的方法时，如果传入过 *default* 则返回它，否则抛出 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError) 异常。

​	如果对象由垃圾回收器管理，则 [`getsizeof()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.getsizeof) 将调用对象的 `__sizeof__` 方法，并在上层添加额外的垃圾回收器。

​	请参阅 [recursive sizeof recipe](https://code.activestate.com/recipes/577504-compute-memory-footprint-of-an-object-and-its-cont/) 获取一个递归地使用 [`getsizeof()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.getsizeof) 来找出各个容器及其全部内容大小的示例。

## sys.**getswitchinterval**()

​	返回解释器的“线程切换间隔时间”，请参阅 [`setswitchinterval()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.setswitchinterval)。

*Added in version 3.2.*

## sys.**_getframe**([*depth*])

​	返回来自调用栈的一个帧对象。如果传入可选整数 *depth*，则返回从栈顶往下相应调用层数的帧对象。如果该数比调用栈更深，则抛出 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。*depth* 的默认值是 0，返回调用栈顶部的帧。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `sys._getframe` 并附带参数 `frame`。

**CPython 实现细节：** 这个函数应该只在内部为了一些特定的目的使用。不保证它在所有 Python 实现中都存在。

## sys.**_getframemodulename**([*depth*])

​	从调用栈返回一个模块的名称。 如果给出了可选的整数 *depth*，则返回从栈顶往下相应调用层数的模块。 如果该数值比调用栈更深，或者如果该模块不可被标识，则返回 `None`。 *depth* 的默认值为零，即返回位于调用栈顶端的模块。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `sys._getframemodulename` 并附带参数 `depth`。

**CPython 实现细节：** 这个函数应该只在内部为了一些特定的目的使用。不保证它在所有 Python 实现中都存在。

## sys.**getobjects**(*limit*[, *type*])

​	This function only exists if CPython was built using the specialized configure option [`--with-trace-refs`](https://docs.python.org/zh-cn/3.13/using/configure.html#cmdoption-with-trace-refs). It is intended only for debugging garbage-collection issues.

​	Return a list of up to *limit* dynamically allocated Python objects. If *type* is given, only objects of that exact type (not subtypes) are included.

​	Objects from the list are not safe to use. Specifically, the result will include objects from all interpreters that share their object allocator state (that is, ones created with [`PyInterpreterConfig.use_main_obmalloc`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.PyInterpreterConfig.use_main_obmalloc) set to 1 or using [`Py_NewInterpreter()`](https://docs.python.org/zh-cn/3.13/c-api/init.html#c.Py_NewInterpreter), and the [main interpreter](https://docs.python.org/zh-cn/3.13/c-api/init.html#sub-interpreter-support)). Mixing objects from different interpreters may lead to crashes or other unexpected behavior.

**CPython 实现细节：** This function should be used for specialized purposes only. It is not guaranteed to exist in all implementations of Python.

*在 3.13.0 (unreleased) 版本发生变更:* The result may include objects from other interpreters.

## sys.**getprofile**()

​	返回由 [`setprofile()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.setprofile) 设置的性能分析函数。

## sys.**gettrace**()

​	返回由 [`settrace()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.settrace) 设置的跟踪函数。

**CPython 实现细节：** [`gettrace()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.gettrace) 函数仅用于实现调试器，性能分析器，打包工具等。它的行为是实现平台的一部分，而不是语言定义的一部分，因此并非在所有 Python 实现中都可用。

## sys.**getwindowsversion**()

​	返回一个具名元组，描述当前正在运行的 Windows 版本。元素名称包括 *major*, *minor*, *build*, *platform*, *service_pack*, *service_pack_minor*, *service_pack_major*, *suite_mask*, *product_type* 和 *platform_version*。*service_pack* 包含一个字符串，*platform_version* 包含一个三元组，其他所有值都是整数。元素也可以通过名称来访问，所以 `sys.getwindowsversion()[0]` 与 `sys.getwindowsversion().major` 是等效的。为保持与旧版本的兼容性，只有前 5 个元素可以用索引检索。

*platform* 将为 `2` (VER_PLATFORM_WIN32_NT)。

*product_type* 可能是以下值之一：

| 常量                           | 含意                           |
| :----------------------------- | :----------------------------- |
| `1` (VER_NT_WORKSTATION)       | 系统是工作站。                 |
| `2` (VER_NT_DOMAIN_CONTROLLER) | 系统是域控制器。               |
| `3` (VER_NT_SERVER)            | 系统是服务器，但不是域控制器。 |

​	该函数包装了 Win32 `GetVersionEx()` 函数；有关这些字段的更多信息请参阅 `OSVERSIONINFOEX()` 的 Microsoft 文档。

*platform_version* 返回当前操作系统的主要版本、次要版本和编译版本号，而不是为该进程所模拟的版本。 它旨在用于日志记录而非特性检测。

​	备注

 

*platform_version* 会从 kernel32.dll 获取版本号，这个版本可能与 OS 版本不同。 请使用 [`platform`](https://docs.python.org/zh-cn/3.13/library/platform.html#module-platform) 模块来获取准确的 OS 版本号。

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): Windows.

*在 3.2 版本发生变更:* 更改为具名元组，添加 *service_pack_minor*, *service_pack_major*, *suite_mask* 和 *product_type*。

*在 3.6 版本发生变更:* 添加了 *platform_version*

## sys.**get_asyncgen_hooks**()

​	返回一个 *asyncgen_hooks* 对象，该对象类似于 `(firstiter, finalizer)` 形式的 [`namedtuple`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.namedtuple)，其中 *firstiter* 和 *finalizer* 应为 `None` 或是一个接受 [asynchronous generator iterator](https://docs.python.org/zh-cn/3.13/glossary.html#term-asynchronous-generator-iterator) 作为参数的函数，并被用来在事件循环中调度异步生成器的最终化。

*Added in version 3.6:* 详情请参阅 [**PEP 525**](https://peps.python.org/pep-0525/)。

​	备注

 

​	本函数已添加至暂定软件包（详情请参阅 [**PEP 411**](https://peps.python.org/pep-0411/) ）。

## sys.**get_coroutine_origin_tracking_depth**()

​	获取由 [`set_coroutine_origin_tracking_depth()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.set_coroutine_origin_tracking_depth) 设置的协程来源的追踪深度。

*Added in version 3.7.*

​	备注

 

​	本函数已添加至暂定软件包（详情请参阅 [**PEP 411**](https://peps.python.org/pep-0411/) ）。仅将其用于调试目的。

## sys.**hash_info**

​	一个 [具名元组](https://docs.python.org/zh-cn/3.13/glossary.html#term-named-tuple)，给出数字类型的哈希的实现参数。关于数字类型的哈希的详情请参阅 [数字类型的哈希运算](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#numeric-hash)。

## hash_info.**width**

​	用于哈希值的位宽度

## hash_info.**modulus**

​	用于数字哈案方案的质数模数 P

## hash_info.**inf**

​	为正无穷大返回的哈希值

## hash_info.**nan**

​	（该属性已不再被使用）

## hash_info.**imag**

​	用于复数虚部的乘数

## hash_info.**algorithm**

​	对字符串、字节串和内存视图进行哈希的算法名称

## hash_info.**hash_bits**

​	哈希算法的内部输出大小

## hash_info.**seed_bits**

​	哈希算法种子密钥的大小

*Added in version 3.2.*

*在 3.4 版本发生变更:* 添加了 *algorithm*, *hash_bits* 和 *seed_bits*

## sys.**hexversion**

​	编码为单个整数的版本号。该整数会确保每个版本都自增，其中适当包括了未发布版本。举例来说，要测试 Python 解释器的版本不低于 1.5.2，请使用:

```
if sys.hexversion >= 0x010502F0:
    # 使用某些高级特性
    ...
else:
    # 使用替代实现或警告用户
    ...
```

​	之所以称它为 `hexversion`，是因为只有将它传入内置函数 [`hex()`](https://docs.python.org/zh-cn/3.13/library/functions.html#hex) 后，其结果才看起来有意义。也可以使用 [具名元组](https://docs.python.org/zh-cn/3.13/glossary.html#term-named-tuple) [`sys.version_info`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.version_info)，它对相同信息有着更人性化的编码。

​	关于 `hexversion` 的更多信息可以在 [API 和 ABI 版本管理](https://docs.python.org/zh-cn/3.13/c-api/apiabiversion.html#apiabiversion) 中找到。

## sys.**implementation**

​	一个对象，该对象包含当前运行的 Python 解释器的实现信息。所有 Python 实现中都必须存在下列属性。

*name* 是当前实现的标识符，如 `'cpython'`。实际的字符串由 Python 实现定义，但保证是小写字母。

*version* 是一个具名元组，格式与 [`sys.version_info`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.version_info) 相同。它表示 Python *实现* 的版本。 另一个（由 `sys.version_info` 表示）是当前解释器遵循的相应 Python *语言* 的版本，两者具有不同的含义。 例如，对于 PyPy 1.8，`sys.implementation.version` 可能是 `sys.version_info(1, 8, 0, 'final', 0)`，而 `sys.version_info` 则是 `sys.version_info(2, 7, 2, 'final', 0)`。对于 CPython 而言两个值是相同的，因为它是参考实现。

*hexversion* 是十六进制的实现版本，类似于 [`sys.hexversion`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.hexversion)。

*cache_tag* 是导入机制使用的标记，用于已缓存模块的文件名。按照惯例，它将由实现的名称和版本组成，如 `'cpython-33'`。但如果合适，Python 实现可以使用其他值。如果 `cache_tag` 被置为 `None`，表示模块缓存已禁用。

[`sys.implementation`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.implementation) 可能包含相应 Python 实现的其他属性。这些非标准属性必须以下划线开头，此处不详细阐述。无论其内容如何，[`sys.implementation`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.implementation) 在解释器运行期间或不同实现版本之间都不会更改。（但是不同 Python 语言版本间可能会不同。）详情请参阅 [**PEP 421**](https://peps.python.org/pep-0421/)。

*Added in version 3.3.*

​	备注

 

​	新的必要属性的添加必须经过常规的 PEP 过程。详情请参阅 [**PEP 421**](https://peps.python.org/pep-0421/)。

## sys.**int_info**

​	一个 [具名元组](https://docs.python.org/zh-cn/3.13/glossary.html#term-named-tuple)，包含 Python 内部整数表示形式的信息。这些属性是只读的。

## int_info.**bits_per_digit**

​	每个数位占用的比特位数。 Python 整数在内部以 `2**int_info.bits_per_digit` 为基数存储。

## int_info.**sizeof_digit**

​	用于表示一个数位的 C 类型的以字节为单位的大小。

## int_info.**default_max_str_digits**

[`sys.get_int_max_str_digits()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.get_int_max_str_digits) 在未被显式配置时所使用的默认值。

## int_info.**str_digits_check_threshold**

[`sys.set_int_max_str_digits()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.set_int_max_str_digits), [`PYTHONINTMAXSTRDIGITS`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONINTMAXSTRDIGITS) 或 [`-X int_max_str_digits`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-X) 的最小非零值。

*Added in version 3.1.*

*在 3.11 版本发生变更:* 添加了 [`default_max_str_digits`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.int_info.default_max_str_digits) 和 [`str_digits_check_threshold`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.int_info.str_digits_check_threshold)。

## sys.**__interactivehook__**

​	当本属性存在，则以 [交互模式](https://docs.python.org/zh-cn/3.13/tutorial/interpreter.html#tut-interactive) 启动解释器时，将自动（不带参数地）调用本属性的值。该过程是在读取 [`PYTHONSTARTUP`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONSTARTUP) 文件之后完成的，所以可以在该文件中设置这一钩子。[`site`](https://docs.python.org/zh-cn/3.13/library/site.html#module-site) 模块 [设置了这一属性](https://docs.python.org/zh-cn/3.13/library/site.html#rlcompleter-config)。

​	如果在启动时调用了钩子，则引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `cpython.run_interactivehook`，附带参数为 hook 对象。

*Added in version 3.4.*

## sys.**intern**(*string*)

​	将 *string* 插入 "interned" （驻留）字符串表，返回被插入的字符串 -- 它是 *string* 本身或副本。驻留字符串对提高字典查找的性能很有用 -- 如果字典中的键已驻留，且所查找的键也已驻留，则键（取散列后）的比较可以用指针代替字符串来比较。通常，Python 程序使用到的名称会被自动驻留，且用于保存模块、类或实例属性的字典的键也已驻留。

​	驻留字符串不属于 [immortal](https://docs.python.org/zh-cn/3.13/glossary.html#term-immortal) 对象；你必须保留一个对 [`intern()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.intern) 返回值的引用才能发挥其优势。

## sys.**_is_gil_enabled**()

​	如果 [GIL](https://docs.python.org/zh-cn/3.13/glossary.html#term-GIL) 已启用则返回 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True) 而如果已禁用则返回 [`False`](https://docs.python.org/zh-cn/3.13/library/constants.html#False)。

*Added in version 3.13.*

## sys.**is_finalizing**()

​	如果主 Python 解释器 [正在关闭](https://docs.python.org/zh-cn/3.13/glossary.html#term-interpreter-shutdown) 则返回 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)。 在其他情况下返回 [`False`](https://docs.python.org/zh-cn/3.13/library/constants.html#False)。

​	另请参阅 [`PythonFinalizationError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#PythonFinalizationError) 异常。

*Added in version 3.5.*

## sys.**last_exc**

​	该变量并非总是会被定义；当有未处理的异常时它将被设为相应的异常实例并且解释器将打印异常消息和栈回溯。它的预期用途是允许交互用户导入调试器模块并进行事后调试而不必重新运行导致了错误的命令。 （典型用法是执行 `import pdb; pdb.pm()` 来进入事后调试器；请参阅 [`pdb`](https://docs.python.org/zh-cn/3.13/library/pdb.html#module-pdb) 了解详情。）

*Added in version 3.12.*

## sys.**_is_interned**(*string*)

​	如果给定的字符串为“驻留字符串”则返回 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)，在其他情况下返回 [`False`](https://docs.python.org/zh-cn/3.13/library/constants.html#False)。

*Added in version 3.13.*

**CPython 实现细节：** 不保证存在于所有的 Python 实现。

## sys.**last_type**

## sys.**last_value**

## sys.**last_traceback**

​	这三个变量已被弃用；请改用 [`sys.last_exc`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.last_exc)。 它们将保存 `sys.last_exc` 的旧表示形式，如上面 [`exc_info()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.exc_info) 所返回的。

## sys.**maxsize**

​	一个整数，表示 [`Py_ssize_t`](https://docs.python.org/zh-cn/3.13/c-api/intro.html#c.Py_ssize_t) 类型的变量可以取到的最大值。在 32 位平台上通常为 `2**31 - 1`，在 64 位平台上通常为 `2**63 - 1`。

## sys.**maxunicode**

​	一个整数，表示最大的 Unicode 码点值，如 `1114111` （十六进制为 `0x10FFFF` ）。

*在 3.3 版本发生变更:* 在 [**PEP 393**](https://peps.python.org/pep-0393/) 之前，`sys.maxunicode` 曾是 `0xFFFF` 或 `0x10FFFF`，具体取决于配置选项，该选项指定将 Unicode 字符存储为 UCS-2 还是 UCS-4。

## sys.**meta_path**

​	一个由 [meta path finder](https://docs.python.org/zh-cn/3.13/glossary.html#term-meta-path-finder) 对象组成的列表，这些对象的 [`find_spec()`](https://docs.python.org/zh-cn/3.13/library/importlib.html#importlib.abc.MetaPathFinder.find_spec) 方法将会被调用以确定其中的某个对象能否找到要导入的模块。 在默认情况下，它将存放实现了 Python 默认导入语法的条目。 调用 [`find_spec()`](https://docs.python.org/zh-cn/3.13/library/importlib.html#importlib.abc.MetaPathFinder.find_spec) 方法至少要附带待导入模块的绝对名称。 如果待导入模块包含在一个包中，则父包的 [`__path__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#module.__path__) 属性将作为第二个参数被传入。 此方法将返回一个 [module spec](https://docs.python.org/zh-cn/3.13/glossary.html#term-module-spec)，或者如果找不到模块则返回 `None`。

​	参见

## [`importlib.abc.MetaPathFinder`](https://docs.python.org/zh-cn/3.13/library/importlib.html#importlib.abc.MetaPathFinder)

​	抽象基类，定义了 [`meta_path`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.meta_path) 内的查找器对象的接口。

## [`importlib.machinery.ModuleSpec`](https://docs.python.org/zh-cn/3.13/library/importlib.html#importlib.machinery.ModuleSpec)

[`find_spec()`](https://docs.python.org/zh-cn/3.13/library/importlib.html#importlib.abc.MetaPathFinder.find_spec) 返回的实例所对应的具体类。

*在 3.4 版本发生变更:* [模块规格说明](https://docs.python.org/zh-cn/3.13/glossary.html#term-module-spec) 是在 Python 3.4 中根据 [**PEP 451**](https://peps.python.org/pep-0451/) 引入的。

*在 3.12 版本发生变更:* 移除了当 [`meta_path`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.meta_path) 条目没有 [`find_spec()`](https://docs.python.org/zh-cn/3.13/library/importlib.html#importlib.abc.MetaPathFinder.find_spec) 方法时查找 `find_module()` 方法的回调。

## sys.**modules**

​	这是一个字典，它将模块名称映射到已经被加载的模块。 这可以被操纵来强制重新加载模块和其他技巧。然而，替换这个字典不一定会像预期的那样工作，从字典中删除重要的项目可能会导致 Python 出错。 如果你想对这个全局字典进行迭代，一定要使用 `sys.modules.copy()` 或 `tuple(sys.modules)` 来避免异常，因为它的大小在迭代过程中可能会因为其他线程中的代码或活动的副作用而改变。

## sys.**orig_argv**

​	传给 Python 可执行文件的原始命令行参数列表。

[`sys.orig_argv`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.orig_argv) 中的元素是传给 Python 解释器的参数，而 [`sys.argv`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.argv) 中的元素则是传给用户程序的参数。 解释器本身所使用的参数将出现在 [`sys.orig_argv`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.orig_argv) 中而不会出现在 [`sys.argv`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.argv) 中。

*Added in version 3.10.*

## sys.**path**

​	一个由字符串组成的列表，用于指定模块的搜索路径。初始化自环境变量 [`PYTHONPATH`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONPATH)，再加上一条与安装有关的默认路径。

​	在默认情况下，如在程序启动时被初始化的时候，会有潜在的不安全路径被添加到 [`sys.path`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.path) 的开头 (在作为的 [`PYTHONPATH`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONPATH) 结果被插入的条目 *之前* 位置):

- `python -m module` 命令行：添加当前工作目录。
- `python script.py` 命令行：添加脚本的目录。 如果是一个符号链接，则会解析符号链接。
- `python -c code` 和 `python` (REPL) 命令行：添加一个空字符串，这表示当前工作目录。

​	如果不想添加这个具有潜在不安全性的路径，请使用 [`-P`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-P) 命令行选项或 [`PYTHONSAFEPATH`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONSAFEPATH) 环境变量。

​	程序可以出于自己的目的随意修改此列表。 应当只将字符串添加到 [`sys.path`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.path) 中；所有其他数据类型都将在导入期间被忽略。

​	参见

- [`site`](https://docs.python.org/zh-cn/3.13/library/site.html#module-site) 模块，该模块描述了如何使用 .pth 文件来扩展 [`sys.path`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.path)。

## sys.**path_hooks**

​	一个由可调用对象组成的列表，这些对象接受一个路径作为参数，并尝试为该路径创建一个 [查找器](https://docs.python.org/zh-cn/3.13/glossary.html#term-finder)。如果成功创建查找器，则可调用对象将返回它，否则将引发 [`ImportError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ImportError) 异常。

​	本特性最早在 [**PEP 302**](https://peps.python.org/pep-0302/) 中被提及。

## sys.**path_importer_cache**

​	一个字典，作为 [查找器](https://docs.python.org/zh-cn/3.13/glossary.html#term-finder) 对象的缓存。key 是传入 [`sys.path_hooks`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.path_hooks) 的路径，value 是相应已找到的查找器。如果路径是有效的文件系统路径，但在 [`sys.path_hooks`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.path_hooks) 中未找到查找器，则存入 `None`。

​	本特性最早在 [**PEP 302**](https://peps.python.org/pep-0302/) 中被提及。

## sys.**platform**

​	一个包含平台标识的字符串。 已知的值有：

| 系统           | `平台` 值      |
| :------------- | :------------- |
| AIX            | `'aix'`        |
| Android        | `'android'`    |
| Emscripten     | `'emscripten'` |
| iOS            | `'ios'`        |
| Linux          | `'linux'`      |
| macOS          | `'darwin'`     |
| Windows        | `'win32'`      |
| Windows/Cygwin | `'cygwin'`     |
| WASI           | `'wasi'`       |

​	对于未在表中列出的 Unix 系统，该值是 `uname -s` 所返回的小写形式 OS 名称，并附加 `uname -r` 所返回的版本号的第一部分，例如 `'sunos5'` 或 `'freebsd8'`，*对应 Python 被构建的时间*。 除非你想要检测特定的系统版本，否则建议使用以下惯例:

```
if sys.platform.startswith('freebsd'):
    # 在此添加 FreeBSD 专属的代码...
```

*在 3.3 版本发生变更:* 在 Linux 上，[`sys.platform`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.platform) 将不再包含主版本号。 它将始终为 `'linux'`，而不是 `'linux2'` 或 `'linux3'`。

*在 3.8 版本发生变更:* 在 AIX 上，[`sys.platform`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.platform) 将不再包含主版本号。 它将始终为 `'aix'`，而不是 `'aix5'` 或 `'aix7'`。

*在 3.13 版本发生变更:* 在 Android 上，[`sys.platform`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.platform) 现在将返回 `'android'` 而不是 `'linux'`。

​	参见

 

[`os.name`](https://docs.python.org/zh-cn/3.13/library/os.html#os.name) 具有更粗的粒度。 [`os.uname()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.uname) 将给出依赖于具体系统的版本信息。

[`platform`](https://docs.python.org/zh-cn/3.13/library/platform.html#module-platform) 模块对系统的标识有更详细的检查。

## sys.**platlibdir**

​	平台专用库目录。用于构建标准库的路径和已安装扩展模块的路径。

​	在大多数平台上，它等同于 `"lib"` 。在 Fedora 和 SuSE 上，它等同于给出了以下 `sys.path` 路径的 64 位平台上的 `"lib64"` （其中 `X.Y` 是 Python 的 `major.minor` 版本）。

- `/usr/lib64/pythonX.Y/`：标准库（如 [`os`](https://docs.python.org/zh-cn/3.13/library/os.html#module-os) 模块的 `os.py` ）
- `/usr/lib64/pythonX.Y/lib-dynload/`：标准库的 C 扩展模块（如 [`errno`](https://docs.python.org/zh-cn/3.13/library/errno.html#module-errno) 模块，确切的文件名取决于平台）
- `/usr/lib/pythonX.Y/site-packages/` (请使用 `lib`, 而非 [`sys.platlibdir`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.platlibdir)): 第三方模块
- `/usr/lib64/pythonX.Y/site-packages/`: 第三方包的 C 扩展模块

*Added in version 3.9.*

## sys.**prefix**

​	一个指定用于安装与平台无关的 Python 文件的站点专属目录前缀的字符串；在 Unix 上，默认为 `/usr/local`。 这可以在构建时通过将 [`--prefix`](https://docs.python.org/zh-cn/3.13/using/configure.html#cmdoption-prefix) 参数传入 **configure** 脚本来设置。 请参阅 [安装路径](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#installation-paths) 了解衍生的路径。

​	备注

 

​	如果在一个 [虚拟环境](https://docs.python.org/zh-cn/3.13/library/venv.html#venv-def) 中，那么该值将在 `site.py` 中被修改，指向虚拟环境。Python 安装位置仍然可以用 [`base_prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.base_prefix) 来获取。

## sys.**ps1**

## sys.**ps2**

​	字符串，指定解释器的首要和次要提示符。仅当解释器处于交互模式时，它们才有定义。这种情况下，它们的初值为 `'>>> '` 和 `'... '`。如果赋给其中某个变量的是非字符串对象，则每次解释器准备读取新的交互式命令时，都会重新运行该对象的 [`str()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)，这可以用来实现动态的提示符。

## sys.**setdlopenflags**(*n*)

​	设置解释器在调用 `dlopen()` 时使用的旗标，例如当解释器加载扩展模块的时候。 首先，如果以 `sys.setdlopenflags(0)` 的形式调用的话这将在导入模块时启用符号的惰性求值。 要在扩展模块之间共享符号，请以 `sys.setdlopenflags(os.RTLD_GLOBAL)` 的形式调用。 旗标志值的符号名称可以在 [`os`](https://docs.python.org/zh-cn/3.13/library/os.html#module-os) 模块中找到 (`RTLD_*xxx*` 常量，例如 [`os.RTLD_LAZY`](https://docs.python.org/zh-cn/3.13/library/os.html#os.RTLD_LAZY))。

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): Unix.

## sys.**set_int_max_str_digits**(*maxdigits*)

​	设置解释器所使用的 [整数字符串转换长度限制](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#int-max-str-digits)。 另请参阅 [`get_int_max_str_digits()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.get_int_max_str_digits)。

*Added in version 3.11.*

## sys.**setprofile**(*profilefunc*)

​	设置系统的性能分析函数，该函数使得在 Python 中能够实现一个 Python 源代码性能分析器。关于 Python Profiler 的更多信息请参阅 [Python 性能分析器](https://docs.python.org/zh-cn/3.13/library/profile.html#profile) 章节。性能分析函数的调用方式类似于系统的跟踪函数（参阅 [`settrace()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.settrace) ），但它是通过不同的事件调用的，例如，不是每执行一行代码就调用它一次（仅在调用某函数和从某函数返回时才会调用性能分析函数，但即使某函数发生异常也会算作返回事件）。该函数是特定于单个线程的，但是性能分析器无法得知线程之间的上下文切换，因此在存在多个线程的情况下使用它是没有意义的。另外，因为它的返回值不会被用到，所以可以简单地返回 `None`。性能分析函数中的错误将导致其自身被解除设置。

​	备注

 

`setprofile()` 使用与 [`settrace()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.settrace) 相同的跟踪机制。 要在跟踪函数内部使用 `setprofile()` 来跟踪调用（例如在调试器断点内），请参阅 [`call_tracing()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.call_tracing)。

​	性能分析函数应接收三个参数：*frame*、*event* 和 *arg*。*frame* 是当前的堆栈帧。*event* 是一个字符串：`'call'`、`'return'`、`'c_call'`、`'c_return'` 或 `'c_exception'`。*arg* 取决于事件类型。

​	这些事件具有以下含义：

## `'call'`

​	表示调用了某个函数（或进入了其他的代码块）。性能分析函数将被调用，*arg* 为 `None`。

## `'return'`

​	表示某个函数（或别的代码块）即将返回。性能分析函数将被调用，*arg* 是即将返回的值，如果此次返回事件是由于抛出异常，*arg* 为 `None`。

## `'c_call'`

​	表示即将调用某个 C 函数。它可能是扩展函数或是内建函数。*arg* 是 C 函数对象。

## `'c_return'`

​	表示返回了某个 C 函数。*arg* 是 C 函数对象。

## `'c_exception'`

​	表示某个 C 函数抛出了异常。*arg* 是 C 函数对象。

​	引发一个不带参数的 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `sys.setprofile`。

## sys.**setrecursionlimit**(*limit*)

​	将 Python 解释器堆栈的最大深度设置为 *limit*。此限制可防止无限递归导致的 C 堆栈溢出和 Python 崩溃。

​	不同平台所允许的最高限值不同。当用户有需要深度递归的程序且平台支持更高的限值，可能就需要调高限值。进行该操作需要谨慎，因为过高的限值可能会导致崩溃。

​	如果新的限值低于当前的递归深度，将抛出 [`RecursionError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#RecursionError) 异常。

*在 3.5.1 版本发生变更:* 如果新的限值低于当前的递归深度，现在将抛出 [`RecursionError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#RecursionError) 异常。

## sys.**setswitchinterval**(*interval*)

​	设置解释器的线程切换间隔时间（单位为秒）。该浮点数决定了“时间片”的理想持续时间，时间片将分配给同时运行的 Python 线程。请注意，实际值可能更高，尤其是使用了运行时间长的内部函数或方法时。同时，在时间间隔末尾调度哪个线程是操作系统的决定。解释器没有自己的调度程序。

*Added in version 3.2.*

## sys.**settrace**(*tracefunc*)

​	设置系统的跟踪函数，使得用户在 Python 中就可以实现 Python 源代码调试器。该函数是特定于单个线程的，所以要让调试器支持多线程，必须为正在调试的每个线程都用 [`settrace()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.settrace) 注册一个跟踪函数，或使用 [`threading.settrace()`](https://docs.python.org/zh-cn/3.13/library/threading.html#threading.settrace)。

​	跟踪函数应接收三个参数：*frame*、*event* 和 *arg*。*frame* 是当前的堆栈帧。*event* 是一个字符串：`'call'`、`'line'`、`'return'`、`'exception'` 或 `'opcode'`。*arg* 取决于事件类型。

​	每次进入 trace 函数的新的局部作用范围，都会调用 trace 函数（ *event* 会被设置为 `'call'` ），它应该返回一个引用，指向即将用在新作用范围上的局部跟踪函数；如果不需要跟踪当前的作用范围，则返回 `None`。

​	本地跟踪函数应返回对自身的引用，或对另一个函数的引用然后将其用作本作用域的局部跟踪函数。

​	如果跟踪函数出错，则该跟踪函数将被取消设置，类似于调用 `settrace(None)`。

​	备注

 

​	在调用跟踪函数（例如由 `settrace()` 设置的函数）时将禁用跟踪。 有关递归跟踪请参阅 [`call_tracing()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.call_tracing)。

​	这些事件具有以下含义：

## `'call'`

​	表示调用了某个函数（或进入了其他的代码块）。全局跟踪函数将被调用，*arg* 为 `None`。返回值将指定局部跟踪函数。

## `'line'`

​	解释器即将执行一个新的代码行或重新执行一个循环的条件。 局部跟踪函数将被调用；*arg* 为 `None`；其返回值将指定新的局部跟踪函数。 请参阅 `Objects/lnotab_notes.txt` 查看有关其工作原理的详细说明。 可以通过在某个 [帧](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame-objects) 上把 [`f_trace_lines`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame.f_trace_lines) 设为 [`False`](https://docs.python.org/zh-cn/3.13/library/constants.html#False) 来禁用相应帧的每行触发事件。

## `'return'`

​	表示某个函数（或别的代码块）即将返回。局部跟踪函数将被调用，*arg* 是即将返回的值，如果此次返回事件是由于抛出异常，*arg* 为 `None`。跟踪函数的返回值将被忽略。

## `'exception'`

​	表示发生了某个异常。局部跟踪函数将被调用，*arg* 是一个 `(exception, value, traceback)` 元组，返回值将指定新的局部跟踪函数。

## `'opcode'`

​	解释器即将执行一个新的操作码（请参阅 [`dis`](https://docs.python.org/zh-cn/3.13/library/dis.html#module-dis) 了解有关操作码的详情）。 局部跟踪函数将被调用；*arg* 为 `None`；共返回值将指定新的局部跟踪函数。 在默认情况下不会发出每个操作码触发事件：必须通过在某个 [帧](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame-objects) 上把 [`f_trace_opcodes`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame.f_trace_opcodes) 设为 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True) 来显式地发出请求。

​	注意，由于异常是在链式调用中传播的，所以每一级都会产生一个 `'exception'` 事件。

​	更细微的用法是，可以显式地通过赋值 `frame.f_trace = tracefunc` 来设置跟踪函数，而不是用现有跟踪函数的返回值去间接设置它。当前帧上的跟踪函数必须激活，而 [`settrace()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.settrace) 还没有做这件事。注意，为了使上述设置起效，必须使用 [`settrace()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.settrace) 来安装全局跟踪函数才能启用运行时跟踪机制，但是它不必与上述是同一个跟踪函数（它可以是一个开销很低的跟踪函数，只返回 `None`，即在各个帧上立即将其自身禁用）。

​	关于代码对象和帧对象的更多信息请参考 [标准类型层级结构](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#types)。

​	引发一个不带参数的 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `sys.settrace`。

**CPython 实现细节：** [`settrace()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.settrace) 函数仅用于实现调试器，性能分析器，打包工具等。它的行为是实现平台的一部分，而不是语言定义的一部分，因此并非在所有 Python 实现中都可用。

*在 3.7 版本发生变更:* 添加了 `'opcode'` 事件类型；为帧添加了 [`f_trace_lines`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame.f_trace_lines) 和 [`f_trace_opcodes`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame.f_trace_opcodes) 属性

## sys.**set_asyncgen_hooks**(*[firstiter] [, finalizer]*)

​	接受两个可选的关键字参数，要求它们是可调用对象，且接受一个 [异步生成器迭代器](https://docs.python.org/zh-cn/3.13/glossary.html#term-asynchronous-generator-iterator) 作为参数。*firstiter* 对象将在异步生成器第一次迭代时调用。*finalizer* 将在异步生成器即将被销毁时调用。

​	引发一个不带参数的 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `sys.set_asyncgen_hooks_firstiter`。

​	引发一个不带参数的 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `sys.set_asyncgen_hooks_finalizer`。

​	之所以会引发两个审计事件，是因为底层的 API 由两个调用组成，每个调用都须要引发自己的事件。

*Added in version 3.6:* 更多详情请参阅 [**PEP 525**](https://peps.python.org/pep-0525/)，*finalizer* 方法的参考示例可参阅 [Lib/asyncio/base_events.py](https://github.com/python/cpython/tree/3.13/Lib/asyncio/base_events.py) 中 `asyncio.Loop.shutdown_asyncgens` 的实现。

​	备注

 

​	本函数已添加至暂定软件包（详情请参阅 [**PEP 411**](https://peps.python.org/pep-0411/) ）。

## sys.**set_coroutine_origin_tracking_depth**(*depth*)

​	允许启用或禁用协程溯源。 当启用时，协程对象上的 `cr_origin` 属性将包含一个由 (文件名, 行号, 函数名) 元组组成的元组，它描述了协程对象自创建以来的回溯信息，最近的调用在最上面。 当禁用时，`cr_origin` 将为 `None`。

​	要启用，请向 *depth* 传递一个大于零的值，它指定了有多少帧将被捕获信息。要禁用，请将 *depth* 置为零。

​	该设置是特定于单个线程的。

*Added in version 3.7.*

​	备注

 

​	本函数已添加至暂定软件包（详情请参阅 [**PEP 411**](https://peps.python.org/pep-0411/) ）。仅将其用于调试目的。

## sys.**activate_stack_trampoline**(*backend*, */*)

​	激活栈性能分析器 trampoline *backend*。 唯一受支持的后端是 `"perf"`。

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): Linux.

*Added in version 3.12.*

​	参见

- [Python 对 Linux perf 性能分析器的支持](https://docs.python.org/zh-cn/3.13/howto/perf_profiling.html#perf-profiling)
- [https://perf.wiki.kernel.org](https://perf.wiki.kernel.org/)

## sys.**deactivate_stack_trampoline**()

​	取消激活当前的栈性能分析器 trampoline 后端。

​	如果没有激活的栈性能分析器，此函数将没有任何效果。

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): Linux.

*Added in version 3.12.*

## sys.**is_stack_trampoline_active**()

​	如果激活了栈性能分析器 trampoline 则返回 `True`。

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): Linux.

*Added in version 3.12.*

## sys.**_enablelegacywindowsfsencoding**()

​	将 [filesystem encoding and error handler](https://docs.python.org/zh-cn/3.13/glossary.html#term-filesystem-encoding-and-error-handler) 分别修改为 'mbcs' 和 'replace'，以便与 3.6 之前版本的 Python 保持一致。

​	这等同于在启动 Python 前先定义好 [`PYTHONLEGACYWINDOWSFSENCODING`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONLEGACYWINDOWSFSENCODING) 环境变量。

​	另请参阅 [`sys.getfilesystemencoding()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.getfilesystemencoding) 和 [`sys.getfilesystemencodeerrors()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.getfilesystemencodeerrors)。

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): Windows.

​	备注

 

​	在 Python 启动后改变文件系统编码格式是有风险的因为旧的文件系统编码格式或由旧的文件系统编码格式所编码的路径可能已被缓存。 请改用 [`PYTHONLEGACYWINDOWSFSENCODING`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONLEGACYWINDOWSFSENCODING)。

*Added in version 3.6:* 更多详情请参阅 [**PEP 529**](https://peps.python.org/pep-0529/)。

*Deprecated since version 3.13, will be removed in version 3.16:* 应改用 [`PYTHONLEGACYWINDOWSFSENCODING`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONLEGACYWINDOWSFSENCODING)。

## sys.**stdin**

## sys.**stdout**

## sys.**stderr**

​	解释器用于标准输入、标准输出和标准错误的 [文件对象](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-object)：

- `stdin` 用于所有交互式输入（包括对 [`input()`](https://docs.python.org/zh-cn/3.13/library/functions.html#input) 的调用）；
- `stdout` 用于 [`print()`](https://docs.python.org/zh-cn/3.13/library/functions.html#print) 和 [expression](https://docs.python.org/zh-cn/3.13/glossary.html#term-expression) 语句的输出，以及用于 [`input()`](https://docs.python.org/zh-cn/3.13/library/functions.html#input) 的提示符；
- 解释器自身的提示符和它的错误消息都发往 `stderr`。

​	这些流都是常规 [文本文件](https://docs.python.org/zh-cn/3.13/glossary.html#term-text-file)，与 [`open()`](https://docs.python.org/zh-cn/3.13/library/functions.html#open) 函数返回的对象一致。它们的参数选择如下：

- 编码格式和错误处理器是由 [`PyConfig.stdio_encoding`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.stdio_encoding) 和 [`PyConfig.stdio_errors`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.stdio_errors) 来初始化的。

  在 Windows 上，控制台设备使用 UTF-8。 非字符设备如磁盘文件和管道使用系统语言区域编码格式（例如 ANSI 代码页）。 非控制台字符设备如 NUL（例如当 `isatty()` 返回 `True` 时）会在启动时分别让 stdin 和 stdout/stderr 使用控制台输入和输出代码页。 如果进程初始化时没有被附加到控制台则会使用默认的系统 [locale encoding](https://docs.python.org/zh-cn/3.13/glossary.html#term-locale-encoding)。

  要重写控制台的特殊行为，可以在启动 Python 前设置 PYTHONLEGACYWINDOWSSTDIO 环境变量。此时，控制台代码页将用于其他字符设备。

  在所有平台上，都可以通过在 Python 启动前设置 [`PYTHONIOENCODING`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONIOENCODING) 环境变量来重写字符编码，或通过新的 [`-X`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-X) `utf8` 命令行选项和 [`PYTHONUTF8`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONUTF8) 环境变量来设置。但是，对 Windows 控制台来说，上述方法仅在设置了 [`PYTHONLEGACYWINDOWSSTDIO`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONLEGACYWINDOWSSTDIO) 后才起效。

- 交互模式下，`stdout` 流是行缓冲的。其他情况下，它像常规文本文件一样是块缓冲的。两种情况下的 `stderr` 流都是行缓冲的。要使得两个流都变成无缓冲，可以传入 [`-u`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-u) 命令行选项或设置 [`PYTHONUNBUFFERED`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONUNBUFFERED) 环境变量。

*在 3.9 版本发生变更:* 非交互模式下，`stderr` 现在是行缓冲的，而不是全缓冲的。

​	备注

 

​	要从标准流写入或读取二进制数据，请使用底层二进制 [`buffer`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOBase.buffer) 对象。例如，要将字节写入 [`stdout`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stdout)，请使用 `sys.stdout.buffer.write(b'abc')`。

​	但是，如果你正在编写一个库（并且不能控制其代码执行所在的上下文），请注意标准流可能会被不支持 `buffer` 属性的文件型对象如 [`io.StringIO`](https://docs.python.org/zh-cn/3.13/library/io.html#io.StringIO) 所取代。

## sys.**__stdin__**

## sys.**__stdout__**

## sys.**__stderr__**

​	程序开始时，这些对象存有 `stdin`、`stderr` 和 `stdout` 的初始值。它们在程序结束前都可以使用，且在需要向实际的标准流打印内容时很有用，无论 `sys.std*` 对象是否已重定向。

​	如果实际文件已经被覆盖成一个损坏的对象了，那它也可用于将实际文件还原成能正常工作的文件对象。但是，本过程的最佳方法应该是，在原来的流被替换之前就显式地保存它，并使用这一保存的对象来还原。

​	备注

 

​	某些情况下的 `stdin`、`stdout` 和 `stderr` 以及初始值 `__stdin__`、`__stdout__` 和 `__stderr__` 可以是 `None`。通常发生在未连接到控制台的 Windows GUI app 中，以及在用 **pythonw** 启动的 Python app 中。

## sys.**stdlib_module_names**

​	一个包含标准库模组名称字符串的冻结集合。

​	它在所有平台上都保持一致。 在某些平台上不可用的模块和在 Python 编译时被禁用的模块也会被列出。 所有种类的模块都会被列出：纯 Python 模块、内置模块、冻结模块和扩展模块等。 测试模块则会被排除掉。

​	对于包来说，仅会列出主包：子包和子模块不会被列出。 例如，`email` 包会被列出，但 `email.mime` 子包和 `email.message` 子模块不会被列出。

​	另请参阅 [`sys.builtin_module_names`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.builtin_module_names) 列表。

*Added in version 3.10.*

## sys.**thread_info**

​	一个 [具名元组](https://docs.python.org/zh-cn/3.13/glossary.html#term-named-tuple)，包含线程实现的信息。

## thread_info.**name**

​	线程实现的名称：

- `"nt"`: Windows 线程
- `"pthread"`: POSIX 线程
- `"pthread-stubs"`: 转存 POSIX 线程（在不支持线程的 WebAssembly 平台上）
- `"solaris"`: Solaris 线程

## thread_info.**lock**

​	锁实现的名称：

- `"semaphore"`: 锁使用一个寄存器
- `"mutex+cond"`: 锁使用互斥和条件变量
- `None` 如果此信息未知

## thread_info.**version**

​	线程库的名称和版本。 它是一个字符串，如果此信息未知则为 `None`。

*Added in version 3.3.*

## sys.**tracebacklimit**

​	当该变量值设置为整数，在发生未处理的异常时，它将决定打印的回溯信息的最大层级数。默认为 `1000`。当将其设置为 `0` 或小于 0，将关闭所有回溯信息，并且只打印异常类型和异常值。

## sys.**unraisablehook**(*unraisable*, */*)

​	处理一个无法抛出的异常。

​	它会在发生了一个异常但 Python 没有办法处理时被调用。例如，当一个析构器引发了异常，或在垃圾回收 ([`gc.collect()`](https://docs.python.org/zh-cn/3.13/library/gc.html#gc.collect)) 期间引发了异常。

*unraisable* 参数具有以下属性:

- `exc_type`: 异常类型。
- `exc_value`: 异常值，可以为 `None`。
- `exc_traceback`: 异常回溯，可以为 `None`。
- `err_msg`: 错误消息，可以为 `None`。
- `object`: 导致异常的对象，可以为 `None`。

​	默认的钩子会将 `err_msg` 和 `object` 格式化为: `f'{err_msg}: {object!r}'`；如果 `err_msg` 为 `None` 则会使用 "Exception ignored in" 错误消息。

​	要改变无法抛出的异常的处理过程，可以重写 [`sys.unraisablehook()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.unraisablehook)。

​	参见

 

[`excepthook()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.excepthook) 处理未捕获的异常。

​	警告

 

​	使用自定义钩子存储 `exc_value` 可能会创建引用循环。 当该异常不再需要时应当显式地清空以打破引用循环。

​	使用自定义钩子存储 `object` 可能会在它被设为正在终结的对象时将其复活。 为避免对象复活应当避免在自定义钩子完成后存储 `object`。

​	当发生无法处理的异常时将引发一个审计事件 `sys.unraisablehook`，附带参数 *hook*、*unraisable*。 其中 *unraisable* 对象与传递给钩子的对象相同。 如果没有设置钩子，那么 *hook* 可以为 `None`。

*Added in version 3.8.*

## sys.**version**

​	一个包含 Python 解释器版本号加编译版本号以及所用编译器等额外信息的字符串。 此字符串会在交互式解释器启动时显示。 请不要从中提取版本信息，而应当使用 [`version_info`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.version_info) 以及 [`platform`](https://docs.python.org/zh-cn/3.13/library/platform.html#module-platform) 模块所提供的函数。

## sys.**api_version**

​	这个解释器的 C API 版本。当你在调试 Python及期扩展模板的版本冲突这个功能非常有用。

## sys.**version_info**

​	一个包含版本号五部分的元组: *major*, *minor*, *micro*, *releaselevel* 和 *serial*。 除 *releaselevel* 外的所有值均为整数；发布级别值则为 `'alpha'`, `'beta'`, `'candidate'` 或 `'final'`。 对应于 Python 版本 2.0 的 `version_info` 值为 `(2, 0, 0, 'final', 0)`。 这些部分也可按名称访问，因此 `sys.version_info[0]` 就等价于 `sys.version_info.major`，依此类推。

*在 3.1 版本发生变更:* 增加了以名称表示的各部分属性。

## sys.**warnoptions**

​	这是警告框架的一个实现细节；请不要修改此值。 有关警告框架的更多信息请参阅 [`warnings`](https://docs.python.org/zh-cn/3.13/library/warnings.html#module-warnings) 模块。

## sys.**winver**

​	用于在 Windows 平台上作为注册表键的版本号。这在 Python DLL 中被存储为 1000 号字符串资源。 其值通常是正在运行的 Python 解释器的主要和次要版本号。 它在 [`sys`](https://docs.python.org/zh-cn/3.13/library/sys.html#module-sys) 模块中提供是为了信息展示目的；修改此值不会影响 Python 所使用的注册表键。

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): Windows.

## sys.**monitoring**

​	包含用于注册回调和控制监控事件的函数和常量的命名空间。 详情参见 [`sys.monitoring`](https://docs.python.org/zh-cn/3.13/library/sys.monitoring.html#module-sys.monitoring)。

## sys.**_xoptions**

​	一个字典，包含通过 [`-X`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-X) 命令行选项传递的旗标，这些旗标专属于各种具体实现。选项名称将会映射到对应的值（如果显式指定）或者 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)。例如:

```
$ ./python -Xa=b -Xc
Python 3.2a3+ (py3k, Oct 16 2010, 20:14:50)
[GCC 4.4.3] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import sys
>>> sys._xoptions
{'a': 'b', 'c': True}
```

**CPython 实现细节：** 这是 CPython 专属的访问通过 [`-X`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-X) 传递的选项的方式。 其他实现可能会通过其他方式导出它们，或者完全不导出。

*Added in version 3.2.*

​	引用

[[C99](https://docs.python.org/zh-cn/3.13/library/sys.html#id1)]

​	ISO/IEC 9899:1999. "Programming languages -- C." 该标准的公开草案可从 https://www.open-std.org/jtc1/sc22/wg14/www/docs/n1256.pdf 获得。
