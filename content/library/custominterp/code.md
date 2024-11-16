+++
title = "code --- 解释器基类"
date = 2024-11-15T21:17:35+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/code.html](https://docs.python.org/zh-cn/3.13/library/code.html)
>
> 收录该文档的时间：`2024-11-15T21:17:35+08:00`

# `code` --- 解释器基类

**源代码：** [Lib/code.py](https://github.com/python/cpython/tree/3.13/Lib/code.py)

------

`code` 模块提供了在 Python 中实现 read-eval-print 循环的功能。它包含两个类和一些快捷功能，可用于构建提供交互式解释器的应用程序。

## *class* code.**InteractiveInterpreter**(*locals=None*)

​	这个类会处理解析和解释器状态（用户的命名空间）；它不会处理输入缓冲、提示或输入文件命名（文件名总是显式地传入）。 可选的 *locals* 参数指定一个映射作为代码执行所在的命名空间；它默认是一个新创建的字典，该字典中的键 `'__name__'` 设为 `'__console__'` 而键 `'__doc__'` 设为 `None`。

## *class* code.**InteractiveConsole**(*locals=None*, *filename='<console>'*, *local_exit=False*)

​	高度模仿交互式 Python 解释器的行为。 这个类基于 [`InteractiveInterpreter`]({{< ref "/library/custominterp/code#code.InteractiveInterpreter" >}}) 构建并使用熟悉的 `sys.ps1` 和 `sys.ps2` 来增加提示，以及输入缓冲功能。 如果 *local_exit* 为真值，控制台中的 `exit()` 和 `quit()` 将不会引发 [`SystemExit`]({{< ref "/library/exceptions#SystemExit" >}})，而是返回到调用方代码。

> 在 3.13 版本发生变更: 增加发 *local_exit* 形参。

## code.**interact**(*banner=None*, *readfunc=None*, *local=None*, *exitmsg=None*, *local_exit=False*)

​	运行一个读取-求值-打印循环的便捷函数。 这会创建一个新的 [`InteractiveConsole`]({{< ref "/library/custominterp/code#code.InteractiveConsole" >}}) 实例并设置 *readfunc* 作为 [`InteractiveConsole.raw_input()`]({{< ref "/library/custominterp/code#code.InteractiveConsole.raw_input" >}}) 方法，如果有提供的话。 如果提供了 *local*，它将被传给 [`InteractiveConsole`]({{< ref "/library/custominterp/code#code.InteractiveConsole" >}}) 构造器以用作解释器循环的默认命名空间。 如果提供了 *local_exit*，它将被传给 [`InteractiveConsole`]({{< ref "/library/custominterp/code#code.InteractiveConsole" >}}) 构造器。 实例的 [`interact()`]({{< ref "/library/custominterp/code#code.InteractiveConsole.interact" >}}) 方法将随后运行并传入 *banner* 和 *exitmsg* 以作为标题和退出消息，如果有提供的话。 控制台对象在使用后将被丢弃。

> 在 3.6 版本发生变更: 加入 *exitmsg* 参数。

> 在 3.13 版本发生变更: 增加发 *local_exit* 形参。

## code.**compile_command**(*source*, *filename='<input>'*, *symbol='single'*)

​	这个函数主要用来模拟 Python 解释器的主循环（即 read-eval-print 循环）。难点的部分是当用户输入不完整命令时，判断能否通过之后的输入来完成（要么成为完整的命令，要么语法错误）。该函数 *几乎* 和实际的解释器主循环的判断是相同的。

*source* 是源字符串；*filename* 是可选的用作读取源的文件名，默认为 `'<input>'`；*symbol* 是可选的语法开启符号，应为 `'single'` (默认), `'eval'` 或 `'exec'`。

​	如果命令完整且有效则返回一个代码对象 (等价于 `compile(source, filename, symbol)`)；如果命令不完整则返回 `None`；如果命令完整但包含语法错误则会引发 [`SyntaxError`]({{< ref "/library/exceptions#SyntaxError" >}}) 或 [`OverflowError`]({{< ref "/library/exceptions#OverflowError" >}}) 而如果命令包含无效字面值则将引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。



## 交互解释器对象

## InteractiveInterpreter.**runsource**(*source*, *filename='<input>'*, *symbol='single'*)

​	在解释器中编译并运行一段源码。 所用参数与 [`compile_command()`]({{< ref "/library/custominterp/code#code.compile_command" >}}) 一样；*filename* 的默认值为 `'<input>'`，*symbol* 则为 `'single'`。 可能发生以下情况之一：

- 输入不正确；[`compile_command()`]({{< ref "/library/custominterp/code#code.compile_command" >}}) 引发了一个异常 ([`SyntaxError`]({{< ref "/library/exceptions#SyntaxError" >}}) 或 [`OverflowError`]({{< ref "/library/exceptions#OverflowError" >}}))。 将通过调用 [`showsyntaxerror()`]({{< ref "/library/custominterp/code#code.InteractiveInterpreter.showsyntaxerror" >}}) 方法打印语法回溯信息。 [`runsource()`]({{< ref "/library/custominterp/code#code.InteractiveInterpreter.runsource" >}}) 返回 `False`。
- 输入不完整，需要更多输入；函数 [`compile_command()`]({{< ref "/library/custominterp/code#code.compile_command" >}}) 返回 `None` 。方法 [`runsource()`]({{< ref "/library/custominterp/code#code.InteractiveInterpreter.runsource" >}}) 返回 `True` 。
- 输入完整；[`compile_command()`]({{< ref "/library/custominterp/code#code.compile_command" >}}) 返回了一个代码对象。 将通过调用 [`runcode()`]({{< ref "/library/custominterp/code#code.InteractiveInterpreter.runcode" >}}) 执行代码（该方法也会处理运行时异常，[`SystemExit`]({{< ref "/library/exceptions#SystemExit" >}}) 除外）。 [`runsource()`]({{< ref "/library/custominterp/code#code.InteractiveInterpreter.runsource" >}}) 返回 `False`。

​	该返回值用于决定使用 `sys.ps1` 还是 `sys.ps2` 来作为下一行的输入提示符。

## InteractiveInterpreter.**runcode**(*code*)

​	执行一个代码对象。当发生异常时，调用 [`showtraceback()`]({{< ref "/library/custominterp/code#code.InteractiveInterpreter.showtraceback" >}}) 来显示回溯。除 [`SystemExit`]({{< ref "/library/exceptions#SystemExit" >}}) （允许传播）以外的所有异常都会被捕获。

​	有关 [`KeyboardInterrupt`]({{< ref "/library/exceptions#KeyboardInterrupt" >}}) 的说明，该异常可能发生于此代码的其他位置，并且并不总能被捕获。 调用者应当准备好处理它。

## InteractiveInterpreter.**showsyntaxerror**(*filename=None*)

​	显示刚发生的语法错误。 这不会显示堆栈回溯因为语法错误并无此种信息。 如果给出了 *filename*，它会被放入异常来替代 Python 解析器所提供的默认文件名，因为它在从一个字符串读取时总是会使用 `'<string>'`。 输出将由 [`write()`]({{< ref "/library/custominterp/code#code.InteractiveInterpreter.write" >}}) 方法来写入。

## InteractiveInterpreter.**showtraceback**()

​	显示刚发生的异常。 我们移除了第一个堆栈条目因为它从属于解释器对象的实现。 输出将由 [`write()`]({{< ref "/library/custominterp/code#code.InteractiveInterpreter.write" >}}) 方法来写入。

> 在 3.5 版本发生变更: 将显示完整的链式回溯，而不只是主回溯。

## InteractiveInterpreter.**write**(*data*)

​	将一个字符串写入到标准错误流 (`sys.stderr`)。 所有派生类都应重写此方法以提供必要的正确输出处理。



## 交互式控制台对象

[`InteractiveConsole`]({{< ref "/library/custominterp/code#code.InteractiveConsole" >}}) 类是 [`InteractiveInterpreter`]({{< ref "/library/custominterp/code#code.InteractiveInterpreter" >}}) 的子类，因此它提供了解释器对象的所有方法，还有以下的额外方法。

## InteractiveConsole.**interact**(*banner=None*, *exitmsg=None*)

​	近似地模拟交互式 Python 终端。 可选的 *banner* 参数指定要在第一次交互前打印的条幅；默认情况下会类似于标准 Python 解释器所打印的内容，并附上外加圆括号的终端对象类名（这样就不会与真正的解释器混淆 —— 因为确实太像了！）

​	可选的 *exitmsg* 参数指定要在退出时打印的退出消息。 传入空字符串可以屏蔽退出消息。 如果 *exitmsg* 未给出或为 `None`，则将打印默认消息。

> 在 3.4 版本发生变更: 要禁止打印任何条幅消息，请传递一个空字符串。

> 在 3.6 版本发生变更: 退出时打印退出消息。

## InteractiveConsole.**push**(*line*)

​	将一行源代码文本推入解释器。 行内容不应带有末尾换行符；它可以有内部换行符。 行内容会被添加到一缓冲区然后调用解释器的 [`runsource()`]({{< ref "/library/custominterp/code#code.InteractiveInterpreter.runsource" >}}) 方法并附带缓冲区内容的拼接结果作为源文本。 如果提示命令已执行或不合法，缓冲区将被重置；在其他情况下，则命令结束，缓冲区将在添加行后保持原样。 如果需要更多的输入则返回值为 `True`，如果行已按某种方式被处理则返回值为 `False` (这与 `runsource()` 相同)。

## InteractiveConsole.**resetbuffer**()

​	从输入缓冲区中删除所有未处理的内容。

## InteractiveConsole.**raw_input**(*prompt=''*)

​	输出提示并读取一行。返回的行不包含末尾的换行符。当用户输入 EOF 键序列时，会引发 [`EOFError`]({{< ref "/library/exceptions#EOFError" >}}) 异常。默认实现是从 `sys.stdin` 读取；子类可以用其他实现代替。
