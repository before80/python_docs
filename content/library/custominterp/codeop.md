+++
title = "codeop --- 编译 Python 代码"
date = 2024-11-15T21:17:35+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/codeop.html](https://docs.python.org/zh-cn/3.13/library/codeop.html)
>
> 收录该文档的时间：`2024-11-15T21:17:35+08:00`

# `codeop` --- 编译 Python 代码

**源代码：** [Lib/codeop.py](https://github.com/python/cpython/tree/3.13/Lib/codeop.py)

------

[`codeop`]({{< ref "/library/custominterp/codeop#module-codeop" >}}) 模块提供了可以模拟Python读取-执行-打印循环的实用程序，就像在 [`code`]({{< ref "/library/custominterp/code#module-code" >}}) 模块中一样。因此，您可能不希望直接使用该模块；如果你想在程序中包含这样一个循环，你可能需要使用 [`code`]({{< ref "/library/custominterp/code#module-code" >}}) 模块。

​	这个任务有两个部分:

1. 能够判断一行输入是否完成了一条 Python 语句：简而言之，就是告诉我们接下来是要打印 '`>>>`' 还是 '`...`'。
2. 记住用户已输入了哪些 future 语句，这样后续的输入可以在这些语句生效的状态下被编译。

[`codeop`]({{< ref "/library/custominterp/codeop#module-codeop" >}}) 模块提供了分别以及同时执行这两个部分的方式。

​	只执行前一部分：

## codeop.**compile_command**(*source*, *filename='<input>'*, *symbol='single'*)

​	尝试编译 *source*，这应当是一个 Python 代码字符串并且在 *source* 是有效的 Python 代码时返回一个对象对象。 在此情况下，代码对象的 filename 属性将为 *filename*，其默认值为 `'<input>'`。 如果 *source* 不是 *not* 有效的 Python 代码，而是有效的 Python 代码的一个前缀时将返回 `None`。

​	如果 *source* 存在问题，将引发异常。 如果存在无效的 Python 语法将引发 [`SyntaxError`]({{< ref "/library/exceptions#SyntaxError" >}})，而如果存在无效的字面值则将引发 [`OverflowError`]({{< ref "/library/exceptions#OverflowError" >}}) 或 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

*symbol* 参数确定 *source* 是作为一条语句 (`'single'`，为默认值)，作为一个 [statement]({{< ref "/glossary/idx#term-statement" >}}) 的序列 (`'exec'`) 还是作为一个 [expression]({{< ref "/glossary/idx#term-expression" >}}) (`'eval'`) 来进行编译。 任何其他值都将导致引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

​备注
 

​	解析器有可能（但很不常见）会在到达源码结尾之前停止解析并成功输出结果；在这种情况下，末尾的符号可能会被忽略而不是引发错误。 例如，一个反斜杠加两个换行符之后可以跟随任何无意义的符号。 一旦解析器 API 得到改进将修正这个问题。

## *class* codeop.**Compile**

​	该类的实例具有 [`__call__()`]({{< ref "/reference/datamodel#object.__call__" >}}) 方法，其签名与内置函数 [`compile()`]({{< ref "/library/functions#compile" >}}) 相似，区别在于如果该实例编译了包含 [`__future__`]({{< ref "/library/python/__future__#module-__future__" >}}) 语句的程序文本，则该实例会‘记住’并编译后续所有的所有包含该语句的程序文本。

## *class* codeop.**CommandCompiler**

​	该类的实例具有 [`__call__()`]({{< ref "/reference/datamodel#object.__call__" >}}) 方法，其签名与 [`compile_command()`]({{< ref "/library/custominterp/codeop#codeop.compile_command" >}}) 相似；区别在如果该实例编译了包含 [`__future__`]({{< ref "/library/python/__future__#module-__future__" >}}) 语句的程序文本，则该实例会‘记住’并编译后续所有的包含该语句的程序文本。
