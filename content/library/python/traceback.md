+++
title = "traceback --- 打印或读取栈回溯信息"
date = 2024-11-15T21:12:39+08:00
weight = 100
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/traceback.html](https://docs.python.org/zh-cn/3.13/library/traceback.html)
>
> 收录该文档的时间：`2024-11-15T21:12:39+08:00`

# `traceback` --- 打印或读取栈回溯信息

**源代码：** [Lib/traceback.py](https://github.com/python/cpython/tree/3.13/Lib/traceback.py)

------

​	This module provides a standard interface to extract, format and print stack traces of Python programs. It is more flexible than the interpreter's default traceback display, and therefore makes it possible to configure certain aspects of the output. Finally, it contains a utility for capturing enough information about an exception to print it later, without the need to save a reference to the actual exception. Since exceptions can be the roots of large objects graph, this utility can significantly improve memory management.

​	本模块使用 [回溯对象](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#traceback-objects) --- 它们是类型为 [`types.TracebackType`](https://docs.python.org/zh-cn/3.13/library/types.html#types.TracebackType) 的对象，它们将被赋值给 [`BaseException`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BaseException) 实例的 [`__traceback__`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BaseException.__traceback__) 字段。

​	参见

## 模块 [`faulthandler`](https://docs.python.org/zh-cn/3.13/library/faulthandler.html#module-faulthandler)

​	用于在发生错误、超时或用户信号时显式地转储 Python 回溯信息。

## 模块 [`pdb`](https://docs.python.org/zh-cn/3.13/library/pdb.html#module-pdb)

​	用于 Python 程序的交互式源代码调试器。

​	本模块的 API 可分为两部分：

- Module-level functions offering basic functionality, which are useful for interactive inspection of exceptions and tracebacks.
- [`TracebackException`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.TracebackException) class and its helper classes [`StackSummary`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.StackSummary) and [`FrameSummary`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.FrameSummary). These offer both more flexibility in the output generated and the ability to store the information necessary for later formatting without holding references to actual exception and traceback objects.

## 模块级函数

## traceback.**print_tb**(*tb*, *limit=None*, *file=None*)

​	如果 *limit* 为正值则打印来自 [回溯对象](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#traceback-objects) *tb* 的至多 *limit* 个栈回溯条目（从调用方的帧开始）。 否则，打印最后 `abs(limit)` 个条目。 如果 *limit* 被省略或为 `None`，则打印所有条目。 如果 *file* 被省略或为 `None`，则会输出到 [`sys.stderr`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stderr)；在其他情况下它应当是一个打开的 [文件](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-object) 或 [file-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-like-object) 用来接受输出。

​	备注

 

*limit* 形参的含义不同于 [`sys.tracebacklimit`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.tracebacklimit) 的含义。 负的 *limit* 值对应于正的 `sys.tracebacklimit` 值，而正的 *limit* 值的行为无法用 `sys.tracebacklimit` 来达成。

*在 3.5 版本发生变更:* 添加了对负数值 *limit* 的支持

## traceback.**print_exception**(*exc*, */*, [*value*, *tb*, ]*limit=None*, *file=None*, *chain=True*)

​	将来自 [回溯对象](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#traceback-objects) *tb* 的异常信息与栈跟踪条目打印到 *file*。 这与 [`print_tb()`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.print_tb) 相比有以下几方面的区别：

- 如果 *tb* 不为 `None`，它将打印头部 `Traceback (most recent call last):`
- 它将在栈回溯之后打印异常类型和 *value*

- 如果 *type(value)* 为 [`SyntaxError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#SyntaxError) 且 *value* 具有适当的格式，它会打印发生语法错误的行并用一个圆点来指明错误的大致位置。

​	从 Python 3.10 开始，可以不再传递 *value* 和 *tb*，而是传递一个异常对象作为第一个参数。 如果提供了 *value* 和 *tb*，则第一个参数会被忽略以便提供向下兼容性。

​	可选的 *limit* 参数的含义与 [`print_tb()`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.print_tb) 的相同。 如果 *chain* 为真值（默认），则链式异常（异常的 [`__cause__`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BaseException.__cause__) 或 [`__context__`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BaseException.__context__) 属性）也将被打印出来，就像解释器本身在打印未处理的异常时一样。

*在 3.5 版本发生变更:* *etype* 参数会被忽略并根据 *value* 推断出来。

*在 3.10 版本发生变更:* *etype* 形参已被重命名为 *exc* 并且现在是仅限位置形参。

## traceback.**print_exc**(*limit=None*, *file=None*, *chain=True*)

​	这是 `print_exception(sys.exception(), limit, file, chain)` 的快捷操作。

## traceback.**print_last**(*limit=None*, *file=None*, *chain=True*)

​	这是 `print_exception(sys.last_exc, limit, file, chain)` 的一个快捷方式。 通常它将只在异常到达交互提示符之后才会起作用 (参见 [`sys.last_exc`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.last_exc))。

## traceback.**print_stack**(*f=None*, *limit=None*, *file=None*)

​	如果 *limit* 为正数则打印至多 *limit* 个栈跟踪条目（从发起调用点开始）。 在其他情况下，则打印最后 `abs(limit)` 个条目。 如果 *limit* 被省略或为 `None`，则会打印所有条目。 可选的 *f* 参数可被用来指定一个替代 [栈帧](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame-objects) 作为开始位置。 可选的 *file* 参数的含义与 [`print_tb()`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.print_tb) 的相同。

*在 3.5 版本发生变更:* 添加了对负数值 *limit* 的支持

## traceback.**extract_tb**(*tb*, *limit=None*)

​	返回一个 [`StackSummary`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.StackSummary) 对象来代表从 [回溯对象](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#traceback-objects) *tb* 提取的“预处理”栈跟踪条目列表。 它可用作栈跟踪的另一种格式化形式。 可选的 *limit* 参数的含义与 [`print_tb()`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.print_tb) 的相同。 “预处理”栈跟踪条目是一个 [`FrameSummary`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.FrameSummary) 对象，其中包含代表通常针对栈跟踪打印的信息的 [`filename`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.FrameSummary.filename), [`lineno`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.FrameSummary.lineno), [`name`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.FrameSummary.name) 和 [`line`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.FrameSummary.line) 等属性。

## traceback.**extract_stack**(*f=None*, *limit=None*)

​	从当前的 [栈帧](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame-objects) 提取原始回溯。 返回值的格式与 [`extract_tb()`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.extract_tb) 的相同。 可选的 *f* 和 *limit* 参数的含义与 [`print_stack()`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.print_stack) 的相同。

## traceback.**format_list**(*extracted_list*)

​	给定一个由元组或如 [`extract_tb()`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.extract_tb) 或 [`extract_stack()`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.extract_stack) 所返回的 [`FrameSummary`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.FrameSummary) 对象组成的列表，返回一个可打印的字符串列表。 结果列表中的每个字符串都对应于参数列表中具有相同索引号的条目。 每个字符串以一个换行符结束；对于那些源文本行不为 `None` 的条目，字符串也可能包含内部换行符。

## traceback.**format_exception_only**(*exc*, */*, [*value*, ]***, *show_group=False*)

​	使用 [`sys.last_value`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.last_value) 等给出的异常值来格式化回溯的异常部分。 返回值是一个字符串列表，其中每一项都以换行符结束。 该列表包含异常消息，它通常是一个字符串；但是，对于 [`SyntaxError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#SyntaxError) 异常，它将包含多行并且（当打印时）会显示语法错误发生位置的详细信息。 在异常消息之后，该列表还包含了异常的 [`注释`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BaseException.__notes__)。

​	从 Python 3.10 开始，可以不传入 *value*，而是传入一个异常对象作为第一个参数。 如果提供了 *value*，则第一个参数将被忽略以便提供向下兼容性。

​	当 *show_group* 为 `True`，并且异常为 [`BaseExceptionGroup`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BaseExceptionGroup) 的实例时，还会递归地包括嵌套的异常，并根据它们的嵌套深度添加缩进。

*在 3.10 版本发生变更:* *etype* 形参已被重命名为 *exc* 并且现在是仅限位置形参。

*在 3.11 版本发生变更:* 返回的列表现在将包括关联到异常的任何 [`注释`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BaseException.__notes__)。

*在 3.13 版本发生变更:* 增加了 *show_group* 形参。

## traceback.**format_exception**(*exc*, */*, [*value*, *tb*, ]*limit=None*, *chain=True*)

​	格式化一个栈跟踪和异常信息。 参数的含义与传给 [`print_exception()`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.print_exception) 的相应参数相同。 返回值是一个字符串列表，每个字符串都以一个换行符结束且有些还包含内部换行符。 当这些行被拼接并打印时，打印的文本与 [`print_exception()`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.print_exception) 的完全相同。

*在 3.5 版本发生变更:* *etype* 参数会被忽略并根据 *value* 推断出来。

*在 3.10 版本发生变更:* 此函数的行为和签名已被修改以与 [`print_exception()`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.print_exception) 相匹配。

## traceback.**format_exc**(*limit=None*, *chain=True*)

​	这类似于 `print_exc(limit)` 但会返回一个字符串而不是打印到一个文件。

## traceback.**format_tb**(*tb*, *limit=None*)

​	是 `format_list(extract_tb(tb, limit))` 的简写形式。

## traceback.**format_stack**(*f=None*, *limit=None*)

​	是 `format_list(extract_stack(f, limit))` 的简写形式。

## traceback.**clear_frames**(*tb*)

​	通过调用每个 [帧对象](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame-objects) 的 [`clear()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame.clear) 方法来清除 [回溯](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#traceback-objects) *tb* 中所有栈帧的局部变量。

*Added in version 3.4.*

## traceback.**walk_stack**(*f*)

​	从给定的帧开始访问 [`f.f_back`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame.f_back) 之后的栈内容，产生每一个帧和帧对应的行号。 如果 *f* 为 `None`，则会使用当前栈。 这个辅助函数要与 [`StackSummary.extract()`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.StackSummary.extract) 一起使用。

*Added in version 3.5.*

## traceback.**walk_tb**(*tb*)

​	访问 [`tb_next`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#traceback.tb_next) 之后的回溯并产生每一个帧和帧对应的行号。 这个辅助函数要与 [`StackSummary.extract()`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.StackSummary.extract) 一起使用。

*Added in version 3.5.*

## `TracebackException` 对象

*Added in version 3.5.*

`TracebackException` objects are created from actual exceptions to capture data for later printing. They offer a more lightweight method of storing this information by avoiding holding references to [traceback](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#traceback-objects) and [frame](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame-objects) objects In addition, they expose more options to configure the output compared to the module-level functions described above.

## *class* traceback.**TracebackException**(*exc_type*, *exc_value*, *exc_traceback*, ***, *limit=None*, *lookup_lines=True*, *capture_locals=False*, *compact=False*, *max_group_width=15*, *max_group_depth=10*)

​	Capture an exception for later rendering. The meaning of *limit*, *lookup_lines* and *capture_locals* are as for the [`StackSummary`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.StackSummary) class.

​	如果 *compact* 为真值，则只有 `TracebackException` 的 [`format()`](https://docs.python.org/zh-cn/3.13/library/functions.html#format) 方法所需要的数据会被保存在类属性性。 特别地，[`__context__`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.TracebackException.__context__) 字段只有在 [`__cause__`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.TracebackException.__cause__) 为 `None` 且 [`__suppress_context__`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.TracebackException.__suppress_context__) 为假值时才会被计算。

​	请注意当局部变量被捕获时，它们也会被显示在回溯中。

*max_group_width* 和 *max_group_depth* 控制异常组的格式化 (参见 [`BaseExceptionGroup`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BaseExceptionGroup))。 depth 是指分组的嵌套层级，而 width 是指一个异常组的异常数组的大小。 格式化的输出在达到某个限制时将被截断。

*在 3.10 版本发生变更:* 增加了 *compact* 形参。

*在 3.11 版本发生变更:* 添加了 *max_group_width* 和 *max_group_depth* 形参。parameters.

## **__cause__**

​	原始 [`__cause__`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BaseException.__cause__) 的 `TracebackException`。

## **__context__**

​	原始 [`__context__`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BaseException.__context__) 的 `TracebackException`。

## **exceptions**

​	如果 `self` 代表一个 [`ExceptionGroup`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ExceptionGroup)，此字段将保存一个由代表被嵌套异常的 `TracebackException` 实例组成的列表。 否则它将为 `None`。

*Added in version 3.11.*

## **__suppress_context__**

​	来自原始异常的 [`__suppress_context__`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BaseException.__suppress_context__) 值。

## **__notes__**

​	来自原始异常的 [`__notes__`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BaseException.__notes__) 值，或者如果异常没有任何注释则为 `None`。 如果它不为 `None` 则会在异常字符串之后的回溯中进行格式化。

*Added in version 3.11.*

## **stack**

​	代表回溯的 [`StackSummary`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.StackSummary)。

## **exc_type**

​	原始回溯的类。

*自 3.13 版本弃用.*

## **exc_type_str**

​	原始异常类的字符串显示。

*Added in version 3.13.*

## **filename**

​	针对语法错误 —— 错误发生所在的文件名。

## **lineno**

​	针对语法错误 —— 错误发生所在的行号。

## **end_lineno**

​	针对语法错误 —— 错误发生所在的末尾行号。 如不存在则可以为 `None`。

*Added in version 3.10.*

## **text**

​	针对语法错误 —— 错误发生所在的文本。

## **offset**

​	针对语法错误 —— 错误发生所在的文本内部的偏移量。

## **end_offset**

​	针对语法错误 —— 错误发生所在的文本末尾偏移量。 如不存在则可以为 `None`。

*Added in version 3.10.*

## **msg**

​	针对语法错误 —— 编译器错误消息。

## *classmethod* **from_exception**(*exc*, ***, *limit=None*, *lookup_lines=True*, *capture_locals=False*)

​	捕获一个异常以便随后渲染。 *limit*, *lookup_lines* 和 *capture_locals* 的含义与 [`StackSummary`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.StackSummary) 类的相同。

​	请注意当局部变量被捕获时，它们也会被显示在回溯中。

## **print**(***, *file=None*, *chain=True*)

​	将 [`format()`](https://docs.python.org/zh-cn/3.13/library/functions.html#format) 所返回的异常信息打印至 *file* (默认为 `sys.stderr`)。

*Added in version 3.11.*

## **format**(***, *chain=True*)

​	格式化异常。

​	如果 *chain* 不为 `True`，则 [`__cause__`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.TracebackException.__cause__) 和 [`__context__`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.TracebackException.__context__) 将不会被格式化。

​	返回值是一个字符串的生成器，其中每个字符串都以换行符结束并且有些还会包含内部换行符。 [`print_exception()`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.print_exception) 是此方法的一个包装器，它只是将这些行打印到一个文件。

## **format_exception_only**(***, *show_group=False*)

​	格式化回溯的异常部分。

​	返回值是一个字符串的生成器，每个字符串都以一个换行符结束。

​	当 *show_group* 为 `False` 时，生成器会发出异常消息并附带其注释（如果有的话）。 异常消息通常是一个字符串；但是，对于 [`SyntaxError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#SyntaxError) 异常，它将由多行组成并且（当打印时）会显示语法错误发生位置的详细信息。

​	当 *show_group* 为 `True`，并且异常为 [`BaseExceptionGroup`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BaseExceptionGroup) 的实例时，还会递归地包括嵌套的异常，并根据它们的嵌套深度添加缩进。

*在 3.11 版本发生变更:* 异常的 [`注释`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BaseException.__notes__) 现在将被包括在输出中。

*在 3.13 版本发生变更:* 增加了 *show_group* 形参。

## `StackSummary` 对象

*Added in version 3.5.*

`StackSummary` 对象代表一个可被格式化的调用栈。

## *class* traceback.**StackSummary**

## *classmethod* **extract**(*frame_gen*, ***, *limit=None*, *lookup_lines=True*, *capture_locals=False*)

​	根据一个帧生成器（例如由 [`walk_stack()`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.walk_stack) 或 [`walk_tb()`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.walk_tb) 所返回的对象）构造 `StackSummary` 对象。

​	如果提供了 *limit*，则只从 *frame_gen* 提取该参数所指定数量的帧。 如果 *lookup_lines* 为 `False`，则返回的 [`FrameSummary`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.FrameSummary) 对象将不会读入它们的行，这使得创建 `StackSummary` 的开销更低（如果它不会被实际格式化这就很有价值）。 如果 *capture_locals* 为 `True` 则每个 `FrameSummary` 中的局部变量会被捕获为对象表示形式。

*在 3.12 版本发生变更:* 在局部变量的 [`repr()`](https://docs.python.org/zh-cn/3.13/library/functions.html#repr) 上被引发的异常（当 *capture_locals* 为 `True` 时）不会再被传播给调用方。

## *classmethod* **from_list**(*a_list*)

​	从所提供的 [`FrameSummary`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.FrameSummary) 对象列表或旧式的元组列表构造一个 `StackSummary` 对象。 每个元组都应当是以 *文件名*, *行号*, *名称*, *行* 为元素的 4 元组。

## **format**()

​	返回一个可打印的字符串列表。 结果列表中的每个字符串各自对应来自栈的单独的 [帧](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame-objects)。 每个字符串都以一个换行符结束；对于带有源文本行的条目来说，字符串还可能包含内部换行符。

​	对于同一帧与行的长序列，将显示前几个重复项，后面跟一个指明之后的实际重复次数的摘要行。

*在 3.6 版本发生变更:* 重复帧的长序列现在将被缩减。

## **format_frame_summary**(*frame_summary*)

​	返回用于打印栈中涉及的某一个 [帧](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame-objects) 的字符串。 此方法会为每个要用 [`StackSummary.format()`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.StackSummary.format) 来打印的 [`FrameSummary`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.FrameSummary) 对象进行调用。 如果它返回 `None`，该帧将从输出中被省略。

*Added in version 3.11.*

## `FrameSummary` 对象

*Added in version 3.5.*

`FrameSummary` 对象表示 [回溯](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#traceback-objects) 中的某一个 [帧](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame-objects)。

## *class* traceback.**FrameSummary**(*filename*, *lineno*, *name*, *lookup_line=True*, *locals=None*, *line=None*)

​	代表 [回溯](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#traceback-objects) 或栈中被格式化或打印的一个单独 [帧](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame-objects)。 它还可能带有包括在其中的帧局部变量的字符串化版本。 如果 *lookup_line* 为 `False`，则源代码不会被查找直到 `FrameSummary` 的 [`line`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.FrameSummary.line) 属性被访问（这还会在将其转换为 [`tuple`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#tuple) 时发生）。 [`line`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.FrameSummary.line) 可能会被直接提供，并将完全阻止行查找的发生。 *locals* 是一个可选的局部变量映射，如果有提供的话这些变量的表示形式将被存储在概要中以便随后显示。

`FrameSummary` 实例具有以下属性：

## **filename**

​	对应于该帧的源代码的文件名。 等价于访问 [帧对象](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame-objects) *f* 上的 [`f.f_code.co_filename`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#codeobject.co_filename)。

## **lineno**

​	对应于该帧的源代码的行号。

## **name**

​	等价于访问 [帧对象](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame-objects) *f* 上的 [`f.f_code.co_name`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#codeobject.co_name)。

## **line**

​	代表该帧的源代码的字符串，开头和末尾的空白将被去除。 如果源代码不可用，它将为 `None`。



## 使用模块级函数的例子

​	这个简单示例是一个基本的读取-求值-打印循环，类似于（但实用性小于）标准 Python 交互式解释器循环。 对于解释器循环的更完整实现，请参阅 [`code`](https://docs.python.org/zh-cn/3.13/library/code.html#module-code) 模块。

```
import sys, traceback

def run_user_code(envdir):
    source = input(">>> ")
    try:
        exec(source, envdir)
    except Exception:
        print("Exception in user code:")
        print("-"*60)
        traceback.print_exc(file=sys.stdout)
        print("-"*60)

envdir = {}
while True:
    run_user_code(envdir)
```

​	下面的例子演示了打印和格式化异常与回溯的不同方式:

```
import sys, traceback

def lumberjack():
    bright_side_of_life()

def bright_side_of_life():
    return tuple()[0]

try:
    lumberjack()
except IndexError as exc:
    print("*** print_tb:")
    traceback.print_tb(exc.__traceback__, limit=1, file=sys.stdout)
    print("*** print_exception:")
    traceback.print_exception(exc, limit=2, file=sys.stdout)
    print("*** print_exc:")
    traceback.print_exc(limit=2, file=sys.stdout)
    print("*** format_exc, first and last line:")
    formatted_lines = traceback.format_exc().splitlines()
    print(formatted_lines[0])
    print(formatted_lines[-1])
    print("*** format_exception:")
    print(repr(traceback.format_exception(exc)))
    print("*** extract_tb:")
    print(repr(traceback.extract_tb(exc.__traceback__)))
    print("*** format_tb:")
    print(repr(traceback.format_tb(exc.__traceback__)))
    print("*** tb_lineno:", exc.__traceback__.tb_lineno)
```

​	该示例的输出看起来像是这样的:

```
*** print_tb:
  File "<doctest...>", line 10, in <module>
    lumberjack()
    ~~~~~~~~~~^^
*** print_exception:
Traceback (most recent call last):
  File "<doctest...>", line 10, in <module>
    lumberjack()
    ~~~~~~~~~~^^
  File "<doctest...>", line 4, in lumberjack
    bright_side_of_life()
    ~~~~~~~~~~~~~~~~~~~^^
IndexError: tuple index out of range
*** print_exc:
Traceback (most recent call last):
  File "<doctest...>", line 10, in <module>
    lumberjack()
    ~~~~~~~~~~^^
  File "<doctest...>", line 4, in lumberjack
    bright_side_of_life()
    ~~~~~~~~~~~~~~~~~~~^^
IndexError: tuple index out of range
*** format_exc, first and last line:
Traceback (most recent call last):
IndexError: tuple index out of range
*** format_exception:
['Traceback (most recent call last):\n',
 '  File "<doctest default[0]>", line 10, in <module>\n    lumberjack()\n    ~~~~~~~~~~^^\n',
 '  File "<doctest default[0]>", line 4, in lumberjack\n    bright_side_of_life()\n    ~~~~~~~~~~~~~~~~~~~^^\n',
 '  File "<doctest default[0]>", line 7, in bright_side_of_life\n    return tuple()[0]\n           ~~~~~~~^^^\n',
 'IndexError: tuple index out of range\n']
*** extract_tb:
[<FrameSummary file <doctest...>, line 10 in <module>>,
 <FrameSummary file <doctest...>, line 4 in lumberjack>,
 <FrameSummary file <doctest...>, line 7 in bright_side_of_life>]
*** format_tb:
['  File "<doctest default[0]>", line 10, in <module>\n    lumberjack()\n    ~~~~~~~~~~^^\n',
 '  File "<doctest default[0]>", line 4, in lumberjack\n    bright_side_of_life()\n    ~~~~~~~~~~~~~~~~~~~^^\n',
 '  File "<doctest default[0]>", line 7, in bright_side_of_life\n    return tuple()[0]\n           ~~~~~~~^^^\n']
*** tb_lineno: 10
```

​	下面的例子演示了打印和格式化栈的不同方式:

\>>>

```
>>> import traceback
>>> def another_function():
...     lumberstack()
...
>>> def lumberstack():
...     traceback.print_stack()
...     print(repr(traceback.extract_stack()))
...     print(repr(traceback.format_stack()))
...
>>> another_function()
  File "<doctest>", line 10, in <module>
    another_function()
  File "<doctest>", line 3, in another_function
    lumberstack()
  File "<doctest>", line 6, in lumberstack
    traceback.print_stack()
[('<doctest>', 10, '<module>', 'another_function()'),
 ('<doctest>', 3, 'another_function', 'lumberstack()'),
 ('<doctest>', 7, 'lumberstack', 'print(repr(traceback.extract_stack()))')]
['  File "<doctest>", line 10, in <module>\n    another_function()\n',
 '  File "<doctest>", line 3, in another_function\n    lumberstack()\n',
 '  File "<doctest>", line 8, in lumberstack\n    print(repr(traceback.format_stack()))\n']
```

​	最后这个例子演示了最后几个格式化函数:

\>>>

```
>>> import traceback
>>> traceback.format_list([('spam.py', 3, '<module>', 'spam.eggs()'),
...                        ('eggs.py', 42, 'eggs', 'return "bacon"')])
['  File "spam.py", line 3, in <module>\n    spam.eggs()\n',
 '  File "eggs.py", line 42, in eggs\n    return "bacon"\n']
>>> an_error = IndexError('tuple index out of range')
>>> traceback.format_exception_only(an_error)
['IndexError: tuple index out of range\n']
```

## Examples of Using [`TracebackException`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.TracebackException)

​	With the helper class, we have more options:

\>>>

```
>>> import sys
>>> from traceback import TracebackException
>>>
>>> def lumberjack():
...     bright_side_of_life()
...
>>> def bright_side_of_life():
...     t = "bright", "side", "of", "life"
...     return t[5]
...
>>> try:
...     lumberjack()
... except IndexError as e:
...     exc = e
...
>>> try:
...     try:
...         lumberjack()
...     except:
...         1/0
... except Exception as e:
...     chained_exc = e
...
>>> # limit works as with the module-level functions
>>> TracebackException.from_exception(exc, limit=-2).print()
Traceback (most recent call last):
  File "<python-input-1>", line 6, in lumberjack
    bright_side_of_life()
    ~~~~~~~~~~~~~~~~~~~^^
  File "<python-input-1>", line 10, in bright_side_of_life
    return t[5]
           ~^^^
IndexError: tuple index out of range

>>> # capture_locals adds local variables in frames
>>> TracebackException.from_exception(exc, limit=-2, capture_locals=True).print()
Traceback (most recent call last):
  File "<python-input-1>", line 6, in lumberjack
    bright_side_of_life()
    ~~~~~~~~~~~~~~~~~~~^^
  File "<python-input-1>", line 10, in bright_side_of_life
    return t[5]
           ~^^^
    t = ("bright", "side", "of", "life")
IndexError: tuple index out of range

>>> # The *chain* kwarg to print() controls whether chained
>>> # exceptions are displayed
>>> TracebackException.from_exception(chained_exc).print()
Traceback (most recent call last):
  File "<python-input-19>", line 4, in <module>
    lumberjack()
    ~~~~~~~~~~^^
  File "<python-input-8>", line 7, in lumberjack
    bright_side_of_life()
    ~~~~~~~~~~~~~~~~~~~^^
  File "<python-input-8>", line 11, in bright_side_of_life
    return t[5]
           ~^^^
IndexError: tuple index out of range

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "<python-input-19>", line 6, in <module>
    1/0
    ~^~
ZeroDivisionError: division by zero

>>> TracebackException.from_exception(chained_exc).print(chain=False)
Traceback (most recent call last):
  File "<python-input-19>", line 6, in <module>
    1/0
    ~^~
ZeroDivisionError: division by zero
```
