+++
title = "bdb --- 调试器框架"
date = 2024-11-15T21:06:32+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/bdb.html](https://docs.python.org/zh-cn/3.13/library/bdb.html)
>
> 收录该文档的时间：`2024-11-15T21:06:32+08:00`

# `bdb` --- 调试器框架

**源代码:** [Lib/bdb.py](https://github.com/python/cpython/tree/3.13/Lib/bdb.py)

------

[`bdb`]({{< ref "/library/debug/bdb#module-bdb" >}}) 模块处理基本的调试器函数，例如设置中断点或通过调试器来管理执行。

​	定义了以下异常：

## *exception* bdb.**BdbQuit**

​	由 [`Bdb`]({{< ref "/library/debug/bdb#bdb.Bdb" >}}) 类引发用于退出调试器的异常。

[`bdb`]({{< ref "/library/debug/bdb#module-bdb" >}}) 模块还定义了两个类:

## *class* bdb.**Breakpoint**(*self*, *file*, *line*, *temporary=False*, *cond=None*, *funcname=None*)

​	这个类实现了临时性中断点、忽略计数、禁用与（重新）启用，以及条件设置等。

​	中断点通过一个名为 [`bpbynumber`]({{< ref "/library/debug/bdb#bdb.Breakpoint.bpbynumber" >}}) 的列表基于数字并通过 [`bplist`]({{< ref "/library/debug/bdb#bdb.Breakpoint.bplist" >}}) 基于 `(file, line)` 对进行索引。 前者指向一个 [`Breakpoint`]({{< ref "/library/debug/bdb#bdb.Breakpoint" >}}) 类的单独实例。 后者指向一个由这种实例组成的列表，因为在每一行中可能存在多个中断点。

​	当创建一个中断点时，它所关联的 [`文件名`]({{< ref "/library/debug/bdb#bdb.Breakpoint.file" >}}) 应当为规范形式。 如果定义了 [`funcname`]({{< ref "/library/debug/bdb#bdb.Breakpoint.funcname" >}})，则当该函数的第一行被执行时将增加一次中断点 [`命中`]({{< ref "/library/debug/bdb#bdb.Breakpoint.hits" >}}) 次数。 [`有条件的`]({{< ref "/library/debug/bdb#bdb.Breakpoint.cond" >}}) 中断点将总是会计入 [`命中`]({{< ref "/library/debug/bdb#bdb.Breakpoint.hits" >}}) 次数。

[`Breakpoint`]({{< ref "/library/debug/bdb#bdb.Breakpoint" >}}) 的实例具有下列方法:

## **deleteMe**()

​	从关联到文件/行的列表中删除此中断点。 如果它是该位置上的最后一个中断点，还将删除相应的文件/行条目。

## **enable**()

​	将此中断点标记为启用。

## **disable**()

​	将此中断点标记为禁用。

## **bpformat**()

​	返回一个带有关于此中断点的所有信息的，格式良好的字符串:

- 中断点编号 。
- 临时状态（删除或保留）。
- 文件/行位置。
- 中断条件
- 要忽略的次数。
- 命中的次数。

> Added in version 3.2.
>

## **bpprint**(*out=None*)

​	将 [`bpformat()`]({{< ref "/library/debug/bdb#bdb.Breakpoint.bpformat" >}}) 的输出打印到文件 *out*，或者如果为 `None` 则打印到标准输出。, to standard output.

[`Breakpoint`]({{< ref "/library/debug/bdb#bdb.Breakpoint" >}}) 实例具有以下属性:

## **file**

[`Breakpoint`]({{< ref "/library/debug/bdb#bdb.Breakpoint" >}}) 的文件名。

## **line**

[`Breakpoint`]({{< ref "/library/debug/bdb#bdb.Breakpoint" >}}) 在 [`file`]({{< ref "/library/debug/bdb#bdb.Breakpoint.file" >}}) 中的行号。

## **temporary**

​	如果位于 (file, line) 的 [`Breakpoint`]({{< ref "/library/debug/bdb#bdb.Breakpoint" >}}) 是临时性的则返回 `True`。

## **cond**

​	在 (file, line) 上对 [`Breakpoint`]({{< ref "/library/debug/bdb#bdb.Breakpoint" >}}) 求值的条件。

## **funcname**

​	用于定义在进入函数时一个 [`Breakpoint`]({{< ref "/library/debug/bdb#bdb.Breakpoint" >}}) 是否命中的函数的名称。

## **enabled**

​	如果 [`Breakpoint`]({{< ref "/library/debug/bdb#bdb.Breakpoint" >}}) 被启用则返回 `True`。

## **bpbynumber**

​	一个 [`Breakpoint`]({{< ref "/library/debug/bdb#bdb.Breakpoint" >}}) 单独实例的数字索引。

## **bplist**

​	以 ([`file`]({{< ref "/library/debug/bdb#bdb.Breakpoint.file" >}}), [`line`]({{< ref "/library/debug/bdb#bdb.Breakpoint.line" >}})) 元组作为索引的 [`Breakpoint`]({{< ref "/library/debug/bdb#bdb.Breakpoint" >}}) 实例的字典。

## **ignore**

​	忽略一个 [`Breakpoint`]({{< ref "/library/debug/bdb#bdb.Breakpoint" >}}) 的次数。

## **hits**

​	命中一个 [`Breakpoint`]({{< ref "/library/debug/bdb#bdb.Breakpoint" >}}) 的次数统计。

## *class* bdb.**Bdb**(*skip=None*)

[`Bdb`]({{< ref "/library/debug/bdb#bdb.Bdb" >}}) 类是作为通用的 Python 调试器基类。

​	这个类负责追踪工具的细节；所派生的类应当实现用户交互。 标准调试器类 ([`pdb.Pdb`]({{< ref "/library/debug/pdb#pdb.Pdb" >}})) 就是一个例子。

​	如果给出了 *skip* 参数，它必须是一个包含 glob 风格的模块名称模式的可迭代对象。 调试器将不会步进到来自与这些模式相匹配的模块的帧。 一个帧是否会被视为来自特定的模块是由帧的 `__name__` 全局变量来确定的。

> 在 3.1 版本发生变更: 增加了 *skip* 形参。

[`Bdb`]({{< ref "/library/debug/bdb#bdb.Bdb" >}}) 的以下方法通常不需要被重写。

## **canonic**(*filename*)

​	返回 *filename* 的规范形式。

​	对于真实的文件名称，此规范形式是一个依赖于具体操作系统的，[`大小写规范的`]({{< ref "/library/filesys/os_path#os.path.normcase" >}}) [`绝对路径`]({{< ref "/library/filesys/os_path#os.path.abspath" >}})。 在交互模式下生成的带有尖括号的 *filename*，如 `"<stdin>"`，会被不加修改地返回。

## **reset**()

​	将 `botframe`, `stopframe`, `returnframe` 和 [`quitting`]({{< ref "/library/debug/bdb#bdb.Bdb.set_quit" >}}) 属性设为准备开始调试的值。

## **trace_dispatch**(*frame*, *event*, *arg*)

​	此函数被安装为被调试帧的追踪函数。 它的返回值是新的追踪函数（在大多数情况下就是它自身）。

​	默认实现会决定如何分派帧，这取决于即将被执行的事件的类型（作为字符串传入）。 *event* 可以是下列值之一:

- `"line"`: 一个新的代码行即将被执行。
- `"call"`: 一个函数即将被调用，或者进入了另一个代码块。
- `"return"`: 一个函数或其他代码块即将返回。
- `"exception"`: 一个异常已发生。
- `"c_call"`: 一个 C 函数即将被调用。
- `"c_return"`: 一个 C 函数已返回。
- `"c_exception"`: 一个 C 函数引发了异常。

​	对于 Python 事件，调用了专门的函数（见下文）。 对于 C 事件，不执行任何操作。

*arg* 形参取决于之前的事件。

​	请参阅 [`sys.settrace()`]({{< ref "/library/python/sys#sys.settrace" >}}) 的文档了解追踪函数的更多信息。 对于代码和帧对象的详情，请参考 [标准类型层级结构]({{< ref "/reference/datamodel#types" >}})。

## **dispatch_line**(*frame*)

​	如果调试器应该在当前行上停止，则发起调用 [`user_line()`]({{< ref "/library/debug/bdb#bdb.Bdb.user_line" >}}) 方法（该方法应当在子类中被重写）。 如果设置了 [`quitting`]({{< ref "/library/debug/bdb#bdb.Bdb.set_quit" >}}) 旗标（可通过 [`user_line()`]({{< ref "/library/debug/bdb#bdb.Bdb.user_line" >}}) 来设置）则将引发 [`BdbQuit`]({{< ref "/library/debug/bdb#bdb.BdbQuit" >}}) 异常。 返回一个对 [`trace_dispatch()`]({{< ref "/library/debug/bdb#bdb.Bdb.trace_dispatch" >}}) 方法的引用以便在该作用域内进一步地追踪。

## **dispatch_call**(*frame*, *arg*)

​	如果调试器应该在此函数调用上停止，则发起调用 [`user_call()`]({{< ref "/library/debug/bdb#bdb.Bdb.user_call" >}}) 方法（该方法应当在子类中被重写）。 如果设置了 [`quitting`]({{< ref "/library/debug/bdb#bdb.Bdb.set_quit" >}}) 旗标（可通过 [`user_call()`]({{< ref "/library/debug/bdb#bdb.Bdb.user_call" >}}) 来设置）则将引发 [`BdbQuit`]({{< ref "/library/debug/bdb#bdb.BdbQuit" >}}) 异常。 返回一个对 [`trace_dispatch()`]({{< ref "/library/debug/bdb#bdb.Bdb.trace_dispatch" >}}) 方法的引用以便在该作用域内进一步地追踪。

## **dispatch_return**(*frame*, *arg*)

​	如果调试器应当在此函数调用上停止，则发起调用 [`user_return()`]({{< ref "/library/debug/bdb#bdb.Bdb.user_return" >}}) 方法（该方法应当在子类中被重写）。 如果设置了 [`quitting`]({{< ref "/library/debug/bdb#bdb.Bdb.set_quit" >}}) 旗标（可通过 [`user_return()`]({{< ref "/library/debug/bdb#bdb.Bdb.user_return" >}}) 来设置）则将引发 [`BdbQuit`]({{< ref "/library/debug/bdb#bdb.BdbQuit" >}}) 异常。 返回一个对 [`trace_dispatch()`]({{< ref "/library/debug/bdb#bdb.Bdb.trace_dispatch" >}}) 方法的引用以便在该作用域内进一步地追踪。

## **dispatch_exception**(*frame*, *arg*)

​	如果调试器应当在此异常上停止,则发起调用 [`user_exception()`]({{< ref "/library/debug/bdb#bdb.Bdb.user_exception" >}}) 方法（该方法应当在子类中被重写）。 如果设置了 [`quitting`]({{< ref "/library/debug/bdb#bdb.Bdb.set_quit" >}}) 旗标（可通过 [`user_exception()`]({{< ref "/library/debug/bdb#bdb.Bdb.user_exception" >}}) 设置）则将引发 [`BdbQuit`]({{< ref "/library/debug/bdb#bdb.BdbQuit" >}}) 异常。 返回一个对 [`trace_dispatch()`]({{< ref "/library/debug/bdb#bdb.Bdb.trace_dispatch" >}}) 方法的引用以便在该作用域内进一步地追踪。

​	通常情况下派生的类不会重写下列方法，但是如果想要重新定义停止和中断点的定义则可能会重写它们。

## **is_skipped_line**(*module_name*)

​	如果 *module_name* 匹配到任何跳过模式则返回 `True`。

## **stop_here**(*frame*)

​	如果 *frame* 在栈的起始帧之下则返回 `True`。

## **break_here**(*frame*)

​	如果该行有生效的中断点则返回 `True`。

​	检测某行或某函数是否存在中断点且处于生效状态。 基于来自 [`effective()`]({{< ref "/library/debug/bdb#bdb.effective" >}}) 的信息删除临时中断点。

## **break_anywhere**(*frame*)

​	如果存在任何针对 *frame* 的文件名的中断点则返回 `True`。

​	派生的类应当重写这些方法以获取调试器操作的控制权。

## **user_call**(*frame*, *argument_list*)

​	如果中断可能在被调用的函数内停止则会从 [`dispatch_call()`]({{< ref "/library/debug/bdb#bdb.Bdb.dispatch_call" >}}) 来调用。

*argument_list* 已不再使用并将始终为 `None`。 该参数被保留用于向下兼容。

## **user_line**(*frame*)

​	当 [`stop_here()`]({{< ref "/library/debug/bdb#bdb.Bdb.stop_here" >}}) 或 [`break_here()`]({{< ref "/library/debug/bdb#bdb.Bdb.break_here" >}}) 返回 `True` 时则会从 [`dispatch_line()`]({{< ref "/library/debug/bdb#bdb.Bdb.dispatch_line" >}}) 来调用。

## **user_return**(*frame*, *return_value*)

​	当 [`stop_here()`]({{< ref "/library/debug/bdb#bdb.Bdb.stop_here" >}}) 返回 `True` 时则会从 [`dispatch_return()`]({{< ref "/library/debug/bdb#bdb.Bdb.dispatch_return" >}}) 来调用。

## **user_exception**(*frame*, *exc_info*)

​	当 [`stop_here()`]({{< ref "/library/debug/bdb#bdb.Bdb.stop_here" >}}) 返回 `True` 时则会从 [`dispatch_exception()`]({{< ref "/library/debug/bdb#bdb.Bdb.dispatch_exception" >}}) 来调用。

## **do_clear**(*arg*)

​	处理当一个中断点属于临时性中断点时是否必须要移除它。

​	此方法必须由派生类来实现。

​	派生类与客户端可以调用以下方法来影响步进状态。

## **set_step**()

​	在一行代码之后停止。

## **set_next**(*frame*)

​	在给定的帧以内或以下的下一行停止。

## **set_return**(*frame*)

​	当从给定的帧返回时停止。

## **set_until**(*frame*, *lineno=None*)

​	在 *lineno* 行大于当前所到达的行或者在从当前帧返回时停止。

## **set_trace**([*frame*])

​	从 *frame* 开始调试。 如果未指定 *frame*，则从调用者的帧开始调试。

> 在 3.13 版本发生变更: [`set_trace()`]({{< ref "/library/debug/bdb#bdb.set_trace" >}}) 将立即进入调试器，而不是在下一行要执行的代码上进入。

## **set_continue**()

​	仅在中断点上或是当结束时停止。 如果不存在中断点，则将系统追踪函数设为 `None`。

## **set_quit**()

​	将 `quitting` 属性设为 `True`。 这将在对某个 `dispatch_*()` 方法的下一次调用中引发 [`BdbQuit`]({{< ref "/library/debug/bdb#bdb.BdbQuit" >}})。

​	派生的类和客户端可以调用下列方法来操纵中断点。 如果出现错误则这些方法将返回一个包含错误消息的字符串，或者如果一切正常则返回 `None`。

## **set_break**(*filename*, *lineno*, *temporary=False*, *cond=None*, *funcname=None*)

​	设置一个新的中断点。 如果对于作为参数传入的 *filename* 不存在 *lineno*，则返回一条错误消息。 *filename* 应为规范的形式，如在 [`canonic()`]({{< ref "/library/debug/bdb#bdb.Bdb.canonic" >}}) 方法中描述的。

## **clear_break**(*filename*, *lineno*)

​	删除位于 *filename* 和 *lineno* 的中断点。 如果未设置过中断点，则返回一条错误消息。

## **clear_bpbynumber**(*arg*)

​	删除 [`Breakpoint.bpbynumber`]({{< ref "/library/debug/bdb#bdb.Breakpoint.bpbynumber" >}}) 中索引号为 *arg* 的中断点。 如果 *arg* 不是数字或超出范围，则返回一条错误消息。

## **clear_all_file_breaks**(*filename*)

​	删除位于 *filename* 的所有中断点。 如果未设置过中断点，则返回一条错误消息。

## **clear_all_breaks**()

​	删除所有现存的中断点。 如果未设置过中断点，则返回一条错误消息。

## **get_bpbynumber**(*arg*)

​	返回由指定数字所指明的中断点。 如果 *arg* 是一个字符串，它将被转换为一个数字。 如果 *arg* 不是一个表示数字的字符串，如果给定的中断点不存在或者已被删除，则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

> Added in version 3.2.
>

## **get_break**(*filename*, *lineno*)

​	如果存在针对 *filename* 中 *lineno* 的中断点则返回 `True`。

## **get_breaks**(*filename*, *lineno*)

​	返回 *filename* 中在 *lineno* 上的所有中断点，或者如果未设置任何中断点则返回一个空列表。

## **get_file_breaks**(*filename*)

​	返回 *filename* 中的所有中断点，或者如果未设置任何中断点则返回一个空列表。

## **get_all_breaks**()

​	返回已设置的所有中断点。

​	派生类与客户端可以调用以下方法来获取一个代表栈回溯信息的数组结构。

## **get_stack**(*f*, *t*)

​	返回一个栈回溯中 (frame, lineno) 元组的列表，及一个大小值。

​	最近调用的帧将排在列表的末尾。 大小值即调试器被发起调用所在帧之下的帧数量。

## **format_stack_entry**(*frame_lineno*, *lprefix=': '*)

​	返回一个字符串，其内容为有关以 `(frame, lineno)` 元组表示的特定栈条目的信息。 返回的字符串包含:

- 包含该帧的规范文件名。
- 函数名称或 `"<lambda>"`。
- 输入参数。
- 返回值。
- 代码的行（如果存在）。

​	以下两个方法可由客户端调用以使用一个调试器来调试一条以字符串形式给出的 [statement]({{< ref "/glossary/idx#term-statement" >}})。

## **run**(*cmd*, *globals=None*, *locals=None*)

​	调试一条通过 [`exec()`]({{< ref "/library/functions#exec" >}}) 函数执行的语句。 *globals* 默认为 `__main__.__dict__`，*locals* 默认为 *globals*。

## **runeval**(*expr*, *globals=None*, *locals=None*)

​	调试一条通过 [`eval()`]({{< ref "/library/functions#eval" >}}) 函数执行的表达式。 *globals* 和 *locals* 的含义与在 [`run()`]({{< ref "/library/debug/bdb#bdb.Bdb.run" >}}) 中的相同。

## **runctx**(*cmd*, *globals*, *locals*)

​	为了保证向下兼容性。 调用 [`run()`]({{< ref "/library/debug/bdb#bdb.Bdb.run" >}}) 方法。

## **runcall**(*func*, */*, **args*, ***kwds*)

​	调试一个单独的函数调用，并返回其结果。

​	最后，这个模块定义了以下函数:

## bdb.**checkfuncname**(*b*, *frame*)

​	如果应当在此中断则返回 `True`，具体取决于 [`Breakpoint`]({{< ref "/library/debug/bdb#bdb.Breakpoint" >}}) *b* 的设置方式。

​	如果是通过行号设置的，它将检查 [`b.line`]({{< ref "/library/debug/bdb#bdb.Breakpoint.line" >}}) 是否与 *frame* 中的行一致。 如果中断点是通过 [`函数名称`]({{< ref "/library/debug/bdb#bdb.Breakpoint.funcname" >}}) 设置的，则必须检查是否位于正确的 *帧* (正确的函数) 以及是否位于其中第一个可执行的行。

## bdb.**effective**(*file*, *line*, *frame*)

​	返回 `(active breakpoint, delete temporary flag)` 或 `(None, None)` 作为目标中断点。

*激活的中断点* 是 [`bplist`]({{< ref "/library/debug/bdb#bdb.Breakpoint.bplist" >}}) 中对应 ([`file`]({{< ref "/library/debug/bdb#bdb.Breakpoint.file" >}}), [`line`]({{< ref "/library/debug/bdb#bdb.Breakpoint.line" >}})) (它必须存在) 的第一个 [`已启用的`]({{< ref "/library/debug/bdb#bdb.Breakpoint.enabled" >}}) 条目，对应的 [`checkfuncname()`]({{< ref "/library/debug/bdb#bdb.checkfuncname" >}}) 为真值，并且没有为假值的 [`cond`]({{< ref "/library/debug/bdb#bdb.Breakpoint.cond" >}}) 或为正值的 [`ignore`]({{< ref "/library/debug/bdb#bdb.Breakpoint.ignore" >}}) 计数。 *flag* 表示临时中断点应当被删除，它仅在 [`cond`]({{< ref "/library/debug/bdb#bdb.Breakpoint.cond" >}}) 无法被求值时 (在此情况下，[`ignore`]({{< ref "/library/debug/bdb#bdb.Breakpoint.ignore" >}}) 计数将被忽略) 才会为 `False`。

​	如果不存在这样的条目，则返回 `(None, None)`。

## bdb.**set_trace**()

​	使用一个来自调用方的帧的 [`Bdb`]({{< ref "/library/debug/bdb#bdb.Bdb" >}}) 实例开始调试。
