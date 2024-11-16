+++
title = "rlcompleter --- 用于 GNU readline 的补全函数"
date = 2024-11-15T11:04:21+08:00
weight = 70
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/rlcompleter.html](https://docs.python.org/zh-cn/3.13/library/rlcompleter.html)
>
> 收录该文档的时间：`2024-11-15T11:04:21+08:00`

# `rlcompleter` --- 用于 GNU readline 的补全函数

**源代码:** [Lib/rlcompleter.py](https://github.com/python/cpython/tree/3.13/Lib/rlcompleter.py)

------

​	`rlcompleter` 模块定义了一个适合被传给 [`readline`](https://docs.python.org/zh-cn/3.13/library/readline.html#module-readline) 模块中 [`set_completer()`](https://docs.python.org/zh-cn/3.13/library/readline.html#readline.set_completer) 的补全函数。

​	当此模块在具有 [`readline`](https://docs.python.org/zh-cn/3.13/library/readline.html#module-readline) 模块的 Unix 平台上被导入时，会自动创建一个 [`Completer`](https://docs.python.org/zh-cn/3.13/library/rlcompleter.html#rlcompleter.Completer) 实例并将其 [`complete()`](https://docs.python.org/zh-cn/3.13/library/rlcompleter.html#rlcompleter.Completer.complete) 方法设为 [readline completer](https://docs.python.org/zh-cn/3.13/library/readline.html#readline-completion)。 该方法提供了对有效的 Python [标识符和关键字](https://docs.python.org/zh-cn/3.13/reference/lexical_analysis.html#identifiers) 的补全功能。

​	示例:



``` python
>>> import rlcompleter
>>> import readline
>>> readline.parse_and_bind("tab: complete")
>>> readline. <TAB PRESSED>
readline.__doc__          readline.get_line_buffer(  readline.read_init_file(
readline.__file__         readline.insert_text(      readline.set_completer(
readline.__name__         readline.parse_and_bind(
>>> readline.
```

`rlcompleter` 模块是为 Python 的 [交互模式](https://docs.python.org/zh-cn/3.13/tutorial/interpreter.html#tut-interactive) 而设计的。 除非 Python 是附带 [`-S`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-S) 选项运行的，这个模块总是会被自动地导入并配置 (参见 [Readline 配置](https://docs.python.org/zh-cn/3.13/library/site.html#rlcompleter-config))。

​	在没有 [`readline`](https://docs.python.org/zh-cn/3.13/library/readline.html#module-readline) 的平台, 此模块定义的 [`Completer`](https://docs.python.org/zh-cn/3.13/library/rlcompleter.html#rlcompleter.Completer) 类仍然可以用于自定义行为.

## *class* rlcompleter.**Completer**

​	Completer 对象具有以下方法：

## **complete**(*text*, *state*)

​	返回针对 *text* 的下一个可能的补全项。

​	当被 [`readline`](https://docs.python.org/zh-cn/3.13/library/readline.html#module-readline) 模块调用时，此方法将被连续调用并附带 `state == 0, 1, 2, ...` 直到该方法返回 `None`。

​	如果指定的 *text* 不包含句点字符 (`'.'`)，它将根据当前 [`__main__`](https://docs.python.org/zh-cn/3.13/library/__main__.html#module-__main__), [`builtins`](https://docs.python.org/zh-cn/3.13/library/builtins.html#module-builtins) 和保留关键字（定义于 [`keyword`](https://docs.python.org/zh-cn/3.13/library/keyword.html#module-keyword) 模块）所定义的名称进行补全。

​	如果为带有点号的名称执行调用，它将尝试尽量求值直到最后一部分为止而产生附带影响（函数不会被求值，但它可以生成对 [`__getattr__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__getattr__) 的调用），并通过 [`dir()`](https://docs.python.org/zh-cn/3.13/library/functions.html#dir) 函数来匹配剩余部分。 在对表达式求值期间引发的任何异常都会被捕获、静默处理并返回 [`None`](https://docs.python.org/zh-cn/3.13/library/constants.html#None)。
