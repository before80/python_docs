+++
title = "dis --- Python 字节码反汇编器"
date = 2024-11-15T21:23:55+08:00
weight = 90
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/dis.html](https://docs.python.org/zh-cn/3.13/library/dis.html)
>
> 收录该文档的时间：`2024-11-15T21:23:55+08:00`

# `dis` --- Python 字节码反汇编器

**源代码:** [Lib/dis.py](https://github.com/python/cpython/tree/3.13/Lib/dis.py)

------

[`dis`](https://docs.python.org/zh-cn/3.13/library/dis.html#module-dis) 模块通过反汇编支持CPython的 [bytecode](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytecode) 分析。该模块作为输入的 CPython 字节码在文件 `Include/opcode.h` 中定义，并由编译器和解释器使用。

**CPython 实现细节：** 字节码是 CPython 解释器的实现细节。不保证不会在Python版本之间添加、删除或更改字节码。不应考虑将此模块的跨 Python VM 或 Python 版本的使用。

*在 3.6 版本发生变更:* 每条指令使用2个字节。以前字节数因指令而异。

*在 3.10 版本发生变更:* 跳转、异常处理和循环指令的参数现在将为指令偏移量而不是字节偏移量。

*在 3.11 版本发生变更:* 有些指令带有一个或多个内联缓存条目，它们是采用 [`CACHE`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-CACHE) 指令的形式。 这些指令默认是隐藏的，但可以通过将 `show_caches=True` 传给任何 [`dis`](https://docs.python.org/zh-cn/3.13/library/dis.html#module-dis) 工具对象来显示。 此外，解释器现在会适配字节码以使其能针对不同的运行时条件实现专门化。 适配的字节码可通过传入 `adaptive=True` 来显示。

*在 3.12 版本发生变更:* 跳转的参数是目标指令相对于紧接在跳转指令的 [`CACHE`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-CACHE) 条目之后的指令的偏移量。

​	因此，[`CACHE`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-CACHE) 指令的存在对前向跳转是透明的但在处理后向跳转时则需要将其纳入考虑。

*在 3.13 版本发生变更:* 对于跳转目标和异常处理器输出将显示逻辑标签而不是指令偏移量。 增加了 `-O` 命令行选项和 `show_offsets` 参数。

​	示例：给定函数 `myfunc()`:

```
def myfunc(alist):
    return len(alist)
```

​	可以使用以下命令显示 `myfunc()` 的反汇编：



``` python
>>> dis.dis(myfunc)
  2           RESUME                   0

  3           LOAD_GLOBAL              1 (len + NULL)
              LOAD_FAST                0 (alist)
              CALL                     1
              RETURN_VALUE
```

​	("2" 是行号)。



## 命令行接口

[`dis`](https://docs.python.org/zh-cn/3.13/library/dis.html#module-dis) 模块可以在命令行下作为一个脚本来发起调用：

```
python -m dis [-h] [-C] [-O] [infile]
```

​	可以接受以下选项：

## **-h**, **--help**

​	显示用法并退出。

## **-C**, **--show-caches**

​	显示内联缓存。

## **-O**, **--show-offsets**

​	显示指令偏移量。

​	如果指定了 `infile`，其反汇编代码将被写入到标准输出。 在其他情况下，反汇编将在从标准输入接收的已编译源代码上进行。

## 字节码分析

> Added in version 3.4.
>

​	字节码分析 API 允许将 Python 代码片段包装在 [`Bytecode`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.Bytecode) 对象中，以便轻松访问已编译代码的详细信息。

## *class* dis.**Bytecode**(*x*, ***, *first_line=None*, *current_offset=None*, *show_caches=False*, *adaptive=False*, *show_offsets=False*)

​	分析的字节码对应于函数、生成器、异步生成器、协程、方法、源代码字符串或代码对象（由 [`compile()`](https://docs.python.org/zh-cn/3.13/library/functions.html#compile) 返回）。

​	这是下面列出的许多函数的便利包装，最值得注意的是 [`get_instructions()`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.get_instructions) ，迭代于 [`Bytecode`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.Bytecode) 的实例产生字节码操作 [`Instruction`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.Instruction) 的实例。

​	如果 *first_line* 不是 `None` ，则表示应该为反汇编代码中的第一个源代码行报告的行号。否则，源行信息（如果有的话）直接来自反汇编的代码对象。

​	如果 *current_offset* 不是 `None` ，它指的就是汇编代码中的指令偏移量。设置它意味着 [`dis()`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.Bytecode.dis) 将针对指定的操作码显示“当前指令”标记。

​	如果 *show_caches* 为 `True`，[`dis()`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.Bytecode.dis) 将显示解释器用来专门化字节码的内联缓存条目。

​	如果 *adaptive* 为 `True`，[`dis()`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.Bytecode.dis) 将显示可能不同于原始字节码的专门化字节码。

​	若 *show_offsets* 是 `True`，[`dis()`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.Bytecode.dis) 的输出将会显示指令偏移量。

## *classmethod* **from_traceback**(*tb*, ***, *show_caches=False*)

​	从给定回溯构造一个 [`Bytecode`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.Bytecode) 实例，将设置 *current_offset* 为异常负责的指令。

## **codeobj**

​	已编译的代码对象。

## **first_line**

​	代码对象的第一个源代码行（如果可用）

## **dis**()

​	返回字节码操作的格式化视图（与 [`dis.dis()`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.dis) 打印相同，但作为多行字符串返回）。

## **info**()

​	返回带有关于代码对象的详细信息的格式化多行字符串，如 [`code_info()`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.code_info) 。

*在 3.7 版本发生变更:* 现在可以处理协程和异步生成器对象。

*在 3.11 版本发生变更:* 增加了 *show_caches* 和 *adaptive* 形参。

​	示例:



``` python
>>> bytecode = dis.Bytecode(myfunc)
>>> for instr in bytecode:
...     print(instr.opname)
...
RESUME
LOAD_GLOBAL
LOAD_FAST
CALL
RETURN_VALUE
```

## 分析函数

[`dis`](https://docs.python.org/zh-cn/3.13/library/dis.html#module-dis) 模块还定义了以下分析函数，它们将输入直接转换为所需的输出。如果只执行单个操作，它们可能很有用，因此中间分析对象没用：

## dis.**code_info**(*x*)

​	返回格式化的多行字符串，其包含详细代码对象信息的用于被提供的函数、生成器、异步生成器、协程、方法、源代码字符串或代码对象。

​	请注意，代码信息字符串的确切内容是高度依赖于实现的，它们可能会在Python VM或Python版本中任意更改。

> Added in version 3.2.
>

*在 3.7 版本发生变更:* 现在可以处理协程和异步生成器对象。

## dis.**show_code**(*x*, ***, *file=None*)

​	将提供的函数、方法。源代码字符串或代码对象的详细代码对象信息打印到 *file* （如果未指定 *file* ，则为 `sys.stdout` ）。

​	这是 `print(code_info(x), file=file)` 的便捷简写，用于在解释器提示符下进行交互式探索。

> Added in version 3.2.
>

*在 3.4 版本发生变更:* 添加 *file* 形参。

## dis.**dis**(*x=None*, ***, *file=None*, *depth=None*, *show_caches=False*, *adaptive=False*)

​	反汇编 *x* 对象。 *x* 可以表示模块、类、方法、函数、生成器、异步生成器、协程、代码对象、源代码字符串或原始字节码的字节序列。 对于模块，它会反汇编所有函数。 对于一个类，它会反汇编所有方法（包括类方法和静态方法）。 对于代码对象或原始字节码序列，它会为每条字节码指令打印一行。 它还会递归地反汇编嵌套代码对象。 这些对象包括生成器表达式、嵌套函数、嵌套类的语句体以及用于 [标注作用域](https://docs.python.org/zh-cn/3.13/reference/executionmodel.html#annotation-scopes) 的代码对象。 在反汇编之前，首先使用 [`compile()`](https://docs.python.org/zh-cn/3.13/library/functions.html#compile) 内置函数将字符串编译为代码对象。 如果未提供任何对象，则该函数将反汇编最后一次回溯。

​	如果提供的话，反汇编将作为文本写入提供的 *file* 参数，否则写入 `sys.stdout` 。

​	递归的最大深度受 *depth* 限制，除非它是 `None` 。 `depth=0` 表示没有递归。

​	如果 *show_caches* 为 `True`，此函数将显示解释器用来专门化字节码的内联缓存条目。

​	如果 *adaptive* 为 `True`，此函数将显示可能不同于原始字节码的专门化字节码。

*在 3.4 版本发生变更:* 添加 *file* 形参。

*在 3.7 版本发生变更:* 实现了递归反汇编并添加了 *depth* 参数。

*在 3.7 版本发生变更:* 现在可以处理协程和异步生成器对象。

*在 3.11 版本发生变更:* 增加了 *show_caches* 和 *adaptive* 形参。

## **distb(tb=None, \*, file=None, show_caches=False, adaptive=False,**

## **show_offset=False)**

​	如果没有传递，则使用最后一个回溯来反汇编回溯的堆栈顶部函数。 指示了导致异常的指令。

​	如果提供的话，反汇编将作为文本写入提供的 *file* 参数，否则写入 `sys.stdout` 。

*在 3.4 版本发生变更:* 添加 *file* 形参。

*在 3.11 版本发生变更:* 增加了 *show_caches* 和 *adaptive* 形参。

*在 3.13 版本发生变更:* 添加了*show_offsets*参数。

## dis.**disassemble**(*code*, *lasti=-1*, ***, *file=None*, *show_caches=False*, *adaptive=False*)

## **disco(code, lasti=-1, \*, file=None, show_caches=False, adaptive=False,**

## **show_offsets=False)**

​	反汇编代码对象，如果提供了 *lasti* ，则指示最后一条指令。输出分为以下几列：

1. 行号，用于每行的第一条指令
2. 当前指令，表示为 `-->` ，
3. 一个标记的指令，用 `>>` 表示，
4. 指令的地址，
5. 操作码名称，
6. 操作参数，和
7. 括号中参数的解释。

​	参数解释识别本地和全局变量名称、常量值、分支目标和比较运算符。

​	如果提供的话，反汇编将作为文本写入提供的 *file* 参数，否则写入 `sys.stdout` 。

*在 3.4 版本发生变更:* 添加 *file* 形参。

*在 3.11 版本发生变更:* 增加了 *show_caches* 和 *adaptive* 形参。

*在 3.13 版本发生变更:* 添加了*show_offsets*参数。

## dis.**get_instructions**(*x*, ***, *first_line=None*, *show_caches=False*, *adaptive=False*)

​	在所提供的函数、方法、源代码字符串或代码对象中的指令上返回一个迭代器。

​	迭代器生成一系列 [`Instruction`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.Instruction) ，命名为元组，提供所提供代码中每个操作的详细信息。

​	如果 *first_line* 不是 `None` ，则表示应该为反汇编代码中的第一个源代码行报告的行号。否则，源行信息（如果有的话）直接来自反汇编的代码对象。

​	参数 *adaptive* 和其在 [`dis()`](https://docs.python.org/zh-cn/3.13/library/dis.html#module-dis) 中的工作方式一样。

> Added in version 3.4.
>

*在 3.11 版本发生变更:* 增加了 *show_caches* 和 *adaptive* 形参。

*在 3.13 版本发生变更:* *show_caches* 参数被弃用并且失效。 迭代器保证 [`Instruction`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.Instruction) 实例一定有 *cache_info* 字段（无论 *show_caches* 传入什么），不再生成单独的缓存项。

## dis.**findlinestarts**(*code*)

​	这个生成器函数使用 [代码对象](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#code-objects) *code* 的 [`co_lines()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#codeobject.co_lines) 方法来查找源代码中行开头的偏移量。 它们将作为 `(offset, lineno)` 对被生成。

*在 3.6 版本发生变更:* 行号可能会减少。 以前，他们总是在增加。

*在 3.10 版本发生变更:* 使用 [**PEP 626**](https://peps.python.org/pep-0626/) [`co_lines()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#codeobject.co_lines) 方法而不是 [代码对象](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#code-objects) 的 [`co_firstlineno`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#codeobject.co_firstlineno) 和 [`co_lnotab`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#codeobject.co_lnotab) 属性。

*在 3.13 版本发生变更:* 若字节码不对应任何一行，行号可以是``None``。

## dis.**findlabels**(*code*)

​	检测作为跳转目标的原始编译后字节码字符串 *code* 中的所有偏移量，并返回这些偏移量的列表。

## dis.**stack_effect**(*opcode*, *oparg=None*, ***, *jump=None*)

​	使用参数 *oparg* 计算 *opcode* 的堆栈效果。

​	如果代码有一个跳转目标并且 *jump* 是 `True` ，则 `drag_effect()` 将返回跳转的堆栈效果。如果 *jump* 是 `False` ，它将返回不跳跃的堆栈效果。如果 *jump* 是 `None` （默认值），它将返回两种情况的最大堆栈效果。

> Added in version 3.4.
>

*在 3.8 版本发生变更:* 添加 *jump* 参数。

*在 3.13 版本发生变更:* 如果``oparg`` 被省略（或传入``None`` ），过去如果字节码使用参数，此时会抛出错误，而现在会返回``oparg=0``时的结果 。当``opcode`` 不使用整数 `oparg` 时，传入的``oparg``将被忽略，不会抛出错误。



## Python字节码说明

[`get_instructions()`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.get_instructions) 函数和 [`Bytecode`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.Bytecode) 类提供字节码指令的详细信息的 [`Instruction`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.Instruction) 实例：

## *class* dis.**Instruction**

​	字节码操作的详细信息

## **opcode**

​	操作的数字代码，对应于下面列出的操作码值和 [操作码集合](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-collections) 中的字节码值。

## **opname**

​	人类可读的操作名称

## **baseopcode**

​	如果操作是专用的则为基本操作的数字码；否则等于 [`opcode`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.Instruction.opcode)

## **baseopname**

​	如果操作是专用的则为基本操作的人类易读的名称；否则等于 [`opname`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.opname)

## **arg**

​	操作的数字参数（如果有的话），否则为 `None`

## **oparg**

[`arg`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.Instruction.arg) 的别名

## **argval**

​	已解析的 arg 值（如果有的话），否则为 `None`

## **argrepr**

​	人类可读的操作参数（如果存在）的描述，否则为空字符串。

## **offset**

​	在字节码序列中的起始操作索引

## **start_offset**

​	在字节码序列中的起始索引，包括前面的 `EXTENDED_ARG` 操作（如有），否则和 [`offset`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.Instruction.offset) 相等。

## **cache_offset**

​	在操作后面的缓存条目的起始索引

## **end_offset**

​	操作后面的缓存条目的结束索引

## **starts_line**

​	如果这个操作在源代码行的开始，为``True``，否则为``False``。

## **line_number**

​	操作码对应的源代码行号（如有），否则为``None``

## **is_jump_target**

​	如果其他代码跳到这里，则为 `True` ，否则为 `False`

## **jump_target**

​	跳转的目标的字节码索引，如果有跳转操作，否则为``None``

## **positions**

[`dis.Positions`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.Positions) 对象保存了这条指令所涵盖的起始和结束位置。

> Added in version 3.4.
>

*在 3.11 版本发生变更:* 增加了 `positions` 字段。

*在 3.13 版本发生变更:* 更改了字段``starts_line``。

​	添加了字段``start_offset``、`cache_offset`、`end_offset`、`baseopname`、`baseopcode`、`jump_target`、`oparg`、`line_number``和``cache_info`

## *class* dis.**Positions**

​	考虑到此信息不可用的情况，某些字段可能为 `None`。

## **lineno**

## **end_lineno**

## **col_offset**

## **end_col_offset**

> Added in version 3.11.
>

​	Python编译器当前生成以下字节码指令。

**一般指令**

​	在下文中，我们将把解释器栈称为 `STACK` 并像描述 Python 列表一样描述对它的操作。 栈顶对应于该语言中的 `STACK[-1]`。

## **NOP**

​	无操作代码。 被字节码优化器用作占位符，以及生成行追踪事件。

## **POP_TOP**

​	移除除堆栈顶部的项：

```
STACK.pop()
```

## **END_FOR**

​	移除栈顶。 等价于 `POP_TOP`。用于循环结束时的清理，因此而得名。

> Added in version 3.12.
>

## **END_SEND**

​	实现 `del STACK[-2]`。 用于在生成器退出时进行清理。

> Added in version 3.12.
>

## **COPY**(*i*)

​	将第 *i* 项推入栈顶，并不移除原项：

```
assert i > 0
STACK.append(STACK[-i])
```

> Added in version 3.11.
>

## **SWAP**(*i*)

​	将栈顶的项与栈中第 *i* 项互换：

```
STACK[-i], STACK[-1] = STACK[-1], STACK[-i]
```

> Added in version 3.11.
>

## **CACHE**

​	此操作码不是真正的指令，它被用来为解释器标记额外空间以便在字节码中直接缓存有用的数据。 它会被所有 `dis` 工具自动隐藏，但可以通过 `show_caches=True` 来查看。

​	从逻辑上说，此空间是之前的指令的组成部分。 许多操作码都预期带有固定数量的缓存，并会指示解释器在运行时跳过它们。

​	被填充的缓存看起来可以像是任意的指令，因此在读取或修改包含快取数据的原始自适应字节码时应当非常小心。

> Added in version 3.11.
>

**一元操作**

​	一元操作获取堆栈顶部元素，应用操作，并将结果推回堆栈。

## **UNARY_NEGATIVE**

​	实现 `STACK[-1] = -STACK[-1]` 。

## **UNARY_NOT**

​	实现 `STACK[-1] = not STACK[-1]` 。

*在 3.13 版本发生变更:* 现在，操作对象需要是 [`bool`](https://docs.python.org/zh-cn/3.13/library/functions.html#bool) 类型。

## **UNARY_INVERT**

​	实现 `STACK[-1] = ~STACK[-1]` 。

## **GET_ITER**

​	实现 `STACK[-1] = iter(STACK[-1])` 。

## **GET_YIELD_FROM_ITER**

​	如果 `STACK[-1]` 是一个 [generator iterator](https://docs.python.org/zh-cn/3.13/glossary.html#term-generator-iterator) 或 [coroutine](https://docs.python.org/zh-cn/3.13/glossary.html#term-coroutine) 对象则它将保持原样。 否则，将实现 `STACK[-1] = iter(STACK[-1])`。

> Added in version 3.5.
>

## **TO_BOOL**

​	实现``STACK[-1] = bool(STACK[-1])``。

> Added in version 3.13.
>

**双目和原地操作**

​	双目操作移除栈顶的两项（ `STACK[-1]` 和 `STACK[-2]` ），执行其运算操作，并将结果放回栈中。

​	原地操作类似于双目操作，但当 `STACK[-2]` 支持时，操作将在原地进行。 结果 `STACK[-1]` 可能（但不一定）是原先 `STACK[-2]` 的值。

## **BINARY_OP**(*op*)

​	实现双目和原地操作运算符（取决于 *op* 的值）：

```
rhs = STACK.pop()
lhs = STACK.pop()
STACK.append(lhs op rhs)
```

> Added in version 3.11.
>

## **BINARY_SUBSCR**

​	实现：

```
key = STACK.pop()
container = STACK.pop()
STACK.append(container[key])
```

## **STORE_SUBSCR**

​	实现：

```
key = STACK.pop()
container = STACK.pop()
value = STACK.pop()
container[key] = value
```

## **DELETE_SUBSCR**

​	实现：

```
key = STACK.pop()
container = STACK.pop()
del container[key]
```

## **BINARY_SLICE**

​	实现：

```
end = STACK.pop()
start = STACK.pop()
container = STACK.pop()
STACK.append(container[start:end])
```

> Added in version 3.12.
>

## **STORE_SLICE**

​	实现：

```
end = STACK.pop()
start = STACK.pop()
container = STACK.pop()
values = STACK.pop()
container[start:end] = value
```

> Added in version 3.12.
>

**协程操作码**

## **GET_AWAITABLE**(*where*)

​	实现 `STACK[-1] = get_awaitable(STACK[-1])` 。其中对于 `get_awaitable(o)` ，当 `o` 是一个有 [`CO_ITERABLE_COROUTINE`](https://docs.python.org/zh-cn/3.13/library/inspect.html#inspect.CO_ITERABLE_COROUTINE) 旗标的协程对象或生成器对象时，返回 `o`，否则解析 `o.__await__` 。

> ​	如果 `where` 操作数为非零值，则表示指令所在的位置:
>
> - `1`：在调用 `__aenter__` 之后
> - `2`：在调用 `__aexit__` 之后

> Added in version 3.5.
>

*在 3.11 版本发生变更:* 在之前版本中，该指令没有 oparg。

## **GET_AITER**

​	实现 `STACK[-1] = STACK[-1].__aiter__()`。

> Added in version 3.5.
>

*在 3.7 版本发生变更:* 已经不再支持从 `__aiter__` 返回可等待对象。

## **GET_ANEXT**

​	对堆栈实现 `STACK.append(get_awaitable(STACK[-1].__anext__()))` 。 关于 `get_awaitable` 的细节，见 `GET_AWAITABLE` 。

> Added in version 3.5.
>

## **END_ASYNC_FOR**

​	终结一个 [`async for`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#async-for) 循环。 在等待下一项时处理被引发的异常。 栈包含了 `STACK[-2]` 中的异步可迭代对象和 `STACK[-1]` 中的已引发异常。 两者都将被弹出。 如果异常不是 [`StopAsyncIteration`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#StopAsyncIteration)，它会被重新引发。

> Added in version 3.8.
>

*在 3.11 版本发生变更:* 栈中的异常表示形式现在将由一个而不是三个条目组成。

## **CLEANUP_THROW**

​	处理当前帧中由调用 [`throw()`](https://docs.python.org/zh-cn/3.13/reference/expressions.html#generator.throw) 或 [`close()`](https://docs.python.org/zh-cn/3.13/reference/expressions.html#generator.close) 引发的异常。 如果 `STACK[-1]` 是 [`StopIteration`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#StopIteration) 的实例，则从栈中弹出三个值，并将其成员 `value` 的值推入栈中，否则重新引发 `STACK[-1]` 异常。

> Added in version 3.12.
>

## **BEFORE_ASYNC_WITH**

​	从 `STACK[-1]` 解析 `__aenter__` 和 `__aexit__` 。 将 `__aexit__` 和 `__aenter__()` 的结果推入栈中：

```
STACK.extend((__aexit__, __aenter__())
```

> Added in version 3.5.
>

**其他操作码**

## **SET_ADD**(*i*)

​	实现：

```
item = STACK.pop()
set.add(STACK[-i], item)
```

​	用于实现集合推导式。

## **LIST_APPEND**(*i*)

​	实现：

```
item = STACK.pop()
list.append(STACK[-i], item)
```

​	用于实现列表推导式。

## **MAP_ADD**(*i*)

​	实现：

```
value = STACK.pop()
key = STACK.pop()
dict.__setitem__(STACK[-i], key, value)
```

​	用于实现字典推导式。

> Added in version 3.1.
>

*在 3.8 版本发生变更:* 映射的值为 `STACK[-1]` ，映射的键为 `STACK[-2]` 。之前它们是反过来的。

​	对于所有 [`SET_ADD`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-SET_ADD) 、 [`LIST_APPEND`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-LIST_APPEND) 和 [`MAP_ADD`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-MAP_ADD) 指令，当弹出添加的值或键值对时，容器对象保留在堆栈上，以便它可用于循环的进一步迭代。

## **RETURN_VALUE**

​	返回 `STACK[-1]` 给函数的调用者。

## **RETURN_CONST**(*consti*)

​	返回 `co_consts[consti]` 给函数的调用者。

> Added in version 3.12.
>

## **YIELD_VALUE**

​	从 [generator](https://docs.python.org/zh-cn/3.13/glossary.html#term-generator) 产生 `STACK.pop()`。

*在 3.11 版本发生变更:* oparg 被设为堆栈深度。

*在 3.12 版本发生变更:* oparg 被设为异常块的深度，以确保关闭生成器的效率。

*在 3.13 版本发生变更:* 如果该指令是 yield-from 或 await 的一部分则 oparg 为 `1`，否则为 `0`。

## **SETUP_ANNOTATIONS**

​	检查 `__annotations__` 是否在 `locals()` 中定义，如果没有，它被设置为空 `dict` 。只有在类或模块体静态地包含 [variable annotations](https://docs.python.org/zh-cn/3.13/glossary.html#term-variable-annotation) 时才会发出此操作码。

> Added in version 3.6.
>

## **POP_EXCEPT**

​	从栈中弹出一个值，它将被用来恢复异常状态。

*在 3.11 版本发生变更:* 栈中的异常表示形式现在将由一个而不是三个条目组成。

## **RERAISE**

​	重新引发当前位于栈顶的异常。 如果 oparg 为非零值，则从栈顶额外弹出一个值用来设置当前帧的 [`f_lasti`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame.f_lasti)。

> Added in version 3.9.
>

*在 3.11 版本发生变更:* 栈中的异常表示形式现在将由一个而不是三个条目组成。

## **PUSH_EXC_INFO**

​	从栈中弹出一个值。 将当前异常推入栈顶。 将原先被弹出的值推回栈。 在异常处理器中使用。

> Added in version 3.11.
>

## **CHECK_EXC_MATCH**

​	为 `except` 执行异常匹配。 检测 `STACK[-2]` 是否为匹配 `STACK[-1]` 的异常。 弹出 `STACK[-1]` 并将测试的布尔值结果推入栈。

> Added in version 3.11.
>

## **CHECK_EG_MATCH**

​	为 `except*` 执行异常匹配。 在代表 `STACK[-2]` 的异常组上应用 `split(STACK[-1])`。

​	在匹配的情况下，从栈中弹出两项并推入不匹配的子分组 (如完全匹配则为 `None`) 以及匹配的子分组。 当没有任何匹配时，则弹出一项 (匹配类型) 并推入 `None`。

> Added in version 3.11.
>

## **WITH_EXCEPT_START**

​	调用栈中 4 号位置上的函数并附带代表位于栈顶的异常的参数 (type, val, tb)。 用于在 [`with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#with) 语句内发生异常时实现调用 `context_manager.__exit__(*exc_info())`。

> Added in version 3.9.
>

*在 3.11 版本发生变更:* `__exit__` 函数位于栈的 4 号位而不是 7 号位。 栈中的异常表示形式现在由一项而不是三项组成。

## **LOAD_ASSERTION_ERROR**

​	将 [`AssertionError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#AssertionError) 推入栈顶。 由 [`assert`](https://docs.python.org/zh-cn/3.13/reference/simple_stmts.html#assert) 语句使用。

> Added in version 3.9.
>

## **LOAD_BUILD_CLASS**

​	将 `builtins.__build_class__()` 推入栈中。 之后它将会被调用来构造一个类。

## **BEFORE_WITH**

​	此操作码会在 with 代码块开始之前执行多个操作。 首先，它将从上下文管理器加载 [`__exit__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__exit__) 并将其推入栈顶以供 [`WITH_EXCEPT_START`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-WITH_EXCEPT_START) 后续使用。 然后，将调用 [`__enter__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__enter__)。 最后，将调用 `__enter__()` 方法的结果推入栈顶。

> Added in version 3.11.
>

## **GET_LEN**

​	Perform `STACK.append(len(STACK[-1]))`. Used in [`match`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#match) statements where comparison with structure of pattern is needed.

> Added in version 3.10.
>

## **MATCH_MAPPING**

​	如果 `STACK[-1]` 是 [`collections.abc.Mapping`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Mapping) 的实例（或者更准确地说：如果在它的 [`tp_flags`](https://docs.python.org/zh-cn/3.13/c-api/typeobj.html#c.PyTypeObject.tp_flags) 中设置了 [`Py_TPFLAGS_MAPPING`](https://docs.python.org/zh-cn/3.13/c-api/typeobj.html#c.Py_TPFLAGS_MAPPING) 旗标），则将 `True` 推入栈顶。 否则，推入 `False`。

> Added in version 3.10.
>

## **MATCH_SEQUENCE**

​	如果 `STACK[-1]` 是 [`collections.abc.Sequence`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Sequence) 的实例而 *不是* [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)/[`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)/[`bytearray`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytearray) 的实例（或者更准确地说：如果在它的 [`tp_flags`](https://docs.python.org/zh-cn/3.13/c-api/typeobj.html#c.PyTypeObject.tp_flags) 中设置了 [`Py_TPFLAGS_SEQUENCE`](https://docs.python.org/zh-cn/3.13/c-api/typeobj.html#c.Py_TPFLAGS_SEQUENCE) 旗标），则将 `True` 推入栈顶。 否则 ，推入 `False`。

> Added in version 3.10.
>

## **MATCH_KEYS**

`STACK[-1]` 是一个映射键的元组，而 `STACK[-2]` 是匹配目标。 如果 `STACK[-2]` 包含 `STACK[-1]` 中的所有键，则推入一个包含对应值的 [`tuple`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#tuple)。 在其他情况下，推入 `None`。

> Added in version 3.10.
>

*在 3.11 版本发生变更:* 在之前的版本中，该指令还会推入一个表示成功 (`True`) 或失败 (`False`) 的布尔值。

## **STORE_NAME**(*namei*)

​	实现 `name = STACK.pop()`。 *namei* 是 *name* 在 [代码对象](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#code-objects) 的 [`co_names`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#codeobject.co_names) 属性中的索引。 在可能的情况下编译器会尝试使用 [`STORE_FAST`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-STORE_FAST) 或 [`STORE_GLOBAL`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-STORE_GLOBAL)。

## **DELETE_NAME**(*namei*)

​	实现 `del name`，其中 *namei* 是 [代码对象](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#code-objects) 的 [`co_names`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#codeobject.co_names) 属性的索引。

## **UNPACK_SEQUENCE**(*count*)

​	将 `STACK[-1]` 解包为 *count* 个单独的值，然后自右向左放入栈中。要求有确切的 *count* 值：

```
assert(len(STACK[-1]) == count)
STACK.extend(STACK.pop()[:-count-1:-1])
```

## **UNPACK_EX**(*counts*)

​	实现带星号目标的赋值：将 `STACK[-1]` 中的可迭代对象解包为各个单独的值。 值的总数可以小于可迭代对象的项数：其中会有一个值是存放剩下的项的列表。

​	在列表前后的值的数量被限制在255。

​	列表前的值的数量被编码在操作码的参数之中。 如果列表后有值，则其数量会被用 `EXTENDED_ARG` 编码。 因此参数可以被认为是一个双字节值，其中低位字节代表列表前的值的数量，高位字节代表其后的值的数量。

​	提取出来的值被自右向左放入栈中，也就是说 `a, *b, c = d` 在执行完成之后会被这样储存： `STACK.extend((a, b, c))` 。

## **STORE_ATTR**(*namei*)

​	实现：

```
obj = STACK.pop()
value = STACK.pop()
obj.name = value
```

​	其中 *namei* 是 name 在 [代码对象](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#code-objects) 的 [`co_names`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#codeobject.co_names) 中的索引。

## **DELETE_ATTR**(*namei*)

​	实现：

```
obj = STACK.pop()
del obj.name
```

​	其中 *namei* 是 name 在 [代码对象](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#code-objects) 的 [`co_names`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#codeobject.co_names) 中的索引。

## **STORE_GLOBAL**(*namei*)

​	类似于 [`STORE_NAME`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-STORE_NAME) 但会将 name 存储为全局变量。

## **DELETE_GLOBAL**(*namei*)

​	类似于 [`DELETE_NAME`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-DELETE_NAME) 但会删除一个全局变量。

## **LOAD_CONST**(*consti*)

​	将 `co_consts[consti]` 推入栈顶。

## **LOAD_NAME**(*namei*)

​	将 `co_names[namei]` 关联的值压入栈中。 名称的查找范围包括局部变量，然后是全局变量，然后是内置量。

## **LOAD_LOCALS**

​	将一个局部变量字典的引用压入栈中。 被用于为 [`LOAD_FROM_DICT_OR_DEREF`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-LOAD_FROM_DICT_OR_DEREF) 和 [`LOAD_FROM_DICT_OR_GLOBALS`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-LOAD_FROM_DICT_OR_GLOBALS) 准备命名空间字典。

> Added in version 3.12.
>

## **LOAD_FROM_DICT_OR_GLOBALS**(*i*)

​	从栈中弹出一个映射，在其中查找 `co_names[namei]`。 如果在此没有找到相应的名称，则在全局变量和内置量中查找，类似 [`LOAD_GLOBAL`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-LOAD_GLOBAL) 。 被用于在类定义中的 [标注作用域](https://docs.python.org/zh-cn/3.13/reference/executionmodel.html#annotation-scopes) 中加载全局变量。

> Added in version 3.12.
>

## **BUILD_TUPLE**(*count*)

​	创建一个使用来自栈的 *count* 个条目的元组，并将结果元组推入栈顶:

```
if count == 0:
    value = ()
else:
    value = tuple(STACK[-count:])
    STACK = STACK[:-count]

STACK.append(value)
```

## **BUILD_LIST**(*count*)

​	类似于 [`BUILD_TUPLE`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-BUILD_TUPLE) 但会创建一个列表。

## **BUILD_SET**(*count*)

​	类似于 [`BUILD_TUPLE`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-BUILD_TUPLE) 但会创建一个集合。

## **BUILD_MAP**(*count*)

​	将一个新字典对象推入栈顶。 弹出 `2 * count` 项使得字典包含 *count* 个条目: `{..., STACK[-4]: STACK[-3], STACK[-2]: STACK[-1]}` 。

*在 3.5 版本发生变更:* 字典是根据栈中的项创建而不是创建一个预设大小包含 *count* 项的空字典。

## **BUILD_CONST_KEY_MAP**(*count*)

[`BUILD_MAP`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-BUILD_MAP) 版本专用于常量键。 弹出的栈顶元素包含一个由键构成的元组，然后从 `STACK[-2]` 开始从构建字典的值中弹出 *count* 个值。

> Added in version 3.6.
>

## **BUILD_STRING**(*count*)

​	拼接 *count* 个来自栈的字符串并将结果字符串推入栈顶。

> Added in version 3.6.
>

## **LIST_EXTEND**(*i*)

​	实现：

```
seq = STACK.pop()
list.extend(STACK[-i], seq)
```

​	用于构建列表。

> Added in version 3.9.
>

## **SET_UPDATE**(*i*)

​	实现：

```
seq = STACK.pop()
set.update(STACK[-i], seq)
```

​	用于构建集合。

> Added in version 3.9.
>

## **DICT_UPDATE**(*i*)

​	实现：

```
map = STACK.pop()
dict.update(STACK[-i], map)
```

​	用于构建字典。

> Added in version 3.9.
>

## **DICT_MERGE**(*i*)

​	类似于 [`DICT_UPDATE`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-DICT_UPDATE) 但对于重复的键会引发异常。

> Added in version 3.9.
>

## **LOAD_ATTR**(*namei*)

​	如果 `namei` 的低位未设置，则将 `STACK[-1]` 替换为 `getattr(STACK[-1], co_names[namei>>1])`。

​	如果 `namei` 的低位已设置，则会尝试从 `STACK[-1]` 对象加载名为 `co_names[namei>>1]` 的方法。 `STACK[-1]` 会被弹出。 此字节码会区分两种情况：如果 `STACK[-1]` 具有一个名称正确的方法，字节码会推入未绑定的方法和 `STACK[-1]`。 `STACK[-1]` 将被 [`CALL`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-CALL) 或者 [`CALL_KW`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-CALL_KW) 用作调用未绑定方法时的第一个参数 (`self`)。 否则，将推入 `NULL` 和属性查询所返回的对象。

*在 3.12 版本发生变更:* 如果 `namei` 的低位已置，则会在属性或未绑定方法之前分别将 `NULL` 或 `self` 推入栈。

## **LOAD_SUPER_ATTR**(*namei*)

​	该操作码实现了 [`super()`](https://docs.python.org/zh-cn/3.13/library/functions.html#super)，包括零参数和双参数形式 (例如 `super().method()`, `super().attr` 和 `super(cls, self).method()`, `super(cls, self).attr`)。

​	它从栈弹出三个值（栈顶在前）：

- `self`：当前方法的第一个参数
- `cls`：当前方法被定义的类
- 全局的``super``

​	对应于其参数，它的操作类似于 [`LOAD_ATTR`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-LOAD_ATTR)，区别在于 `namei` 左移了 2 位而不是 1 位。

`namei` 的低位发出尝试加载方法的信号，与 [`LOAD_ATTR`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-LOAD_ATTR) 一样，其结果是推入 `NULL` 和所加载的方法。 当其被取消设置时会将单个值推入栈。

`namei` 的次低比特位如果被设置，表示这是对 [`super()`](https://docs.python.org/zh-cn/3.13/library/functions.html#super) 附带两个参数的调用（未设置则表示附带零个参数）。

> Added in version 3.12.
>

## **COMPARE_OP**(*opname*)

​	执行布尔操作。操作名可以在``cmp_op[opname >> 5]``中找到。如果``opname``的第五低位是1（`opname & 16`），则结果将被强制转换为``bool``。

*在 3.13 版本发生变更:* 现在参数的第五低位表示强制将结果转换为 [`bool`](https://docs.python.org/zh-cn/3.13/library/functions.html#bool)。

## **IS_OP**(*invert*)

​	执行 `is` 比较，或者如果 `invert` 为 1 则执行 `is not`。

> Added in version 3.9.
>

## **CONTAINS_OP**(*invert*)

​	执行 `in` 比较，或者如果 `invert` 为 1 则执行 `not in`。

> Added in version 3.9.
>

## **IMPORT_NAME**(*namei*)

​	导入模块 `co_names[namei]`。 会弹出 `STACK[-1]` 和 `STACK[-2]` 以提供 *fromlist* 和 *level* 参数给 [`__import__()`](https://docs.python.org/zh-cn/3.13/library/functions.html#import__) 。 模块对象会被推入栈顶。 当前命名空间不受影响：对于一条标准 import 语句，会执行后续的 [`STORE_FAST`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-STORE_FAST) 指令来修改命名空间。

## **IMPORT_FROM**(*namei*)

​	从在 `STACK[-1]` 内找到的模块中加载属性 `co_names[namei]`。 结果对象会被推入栈顶，以便由后续的 [`STORE_FAST`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-STORE_FAST) 指令来保存。

## **JUMP_FORWARD**(*delta*)

​	将字节码计数器的值增加 *delta*。

## **JUMP_BACKWARD**(*delta*)

​	将字节码计数器减少 *delta*。 检查中断。

> Added in version 3.11.
>

## **JUMP_BACKWARD_NO_INTERRUPT**(*delta*)

​	将字节码计数器减少 *delta*。 不检查中断。

> Added in version 3.11.
>

## **POP_JUMP_IF_TRUE**(*delta*)

​	如果 `STACK[-1]` 为真值，则将字节码计数器增加 *delta*。 `STACK[-1]` 将被弹出。

*在 3.11 版本发生变更:* 操作符的参数现在是一个相对的差值而不是一个绝对的目标量。 此操作码是一个伪指令，在最终的字节码里被定向的版本（ forward/backward ）取代。

*在 3.12 版本发生变更:* 该操作码现在不再是伪指令。

*在 3.13 版本发生变更:* 现在，操作对象需要是 [`bool`](https://docs.python.org/zh-cn/3.13/library/functions.html#bool) 类型。

## **POP_JUMP_IF_FALSE**(*delta*)

​	如果 `STACK[-1]` 为假值，则将字节码计数器增加 *delta*。 `STACK[-1]` 将被弹出。

*在 3.11 版本发生变更:* 操作符的参数现在是一个相对的差值而不是一个绝对的目标量。 此操作码是一个伪指令，在最终的字节码里被定向的版本（ forward/backward ）取代。

*在 3.12 版本发生变更:* 该操作码现在不再是伪指令。

*在 3.13 版本发生变更:* 现在，操作对象需要是 [`bool`](https://docs.python.org/zh-cn/3.13/library/functions.html#bool) 类型。

## **POP_JUMP_IF_NOT_NONE**(*delta*)

​	如果 `STACK[-1]` 不为 `None` ，则将字节码计数器增加 *delta*。 `STACK[-1]` 将被弹出。

​	此操作码是一个伪指令，在最终的字节码里被定向的版本（ forward/backward ）取代。

> Added in version 3.11.
>

*在 3.12 版本发生变更:* 该操作码现在不再是伪指令。

## **POP_JUMP_IF_NONE**(*delta*)

​	如果 `STACK[-1]` 为 `None` ，则将字节码计数器增加 *delta*。 `STACK[-1]` 将被弹出。

​	此操作码是一个伪指令，在最终的字节码里被定向的版本（ forward/backward ）取代。

> Added in version 3.11.
>

*在 3.12 版本发生变更:* 该操作码现在不再是伪指令。

## **FOR_ITER**(*delta*)

`STACK[-1]` 是一个 [iterator](https://docs.python.org/zh-cn/3.13/glossary.html#term-iterator)。 调用其 [`__next__()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#iterator.__next__) 方法。 如果产生一个新的值则压入栈中 （把迭代器压下去）。 如果迭代器已耗尽，则将字节码计数器增加 *delta* 。

*在 3.12 版本发生变更:* 直到 3.11 ，当迭代器耗尽时，它会被从栈中弹出。

## **LOAD_GLOBAL**(*namei*)

​	将名为 `co_names[namei>>1]` 的全局对象加载到栈顶。

*在 3.11 版本发生变更:* 如果设置了 `namei` 的低比特位，则会在全局变量前将一个 `NULL` 推入栈。

## **LOAD_FAST**(*var_num*)

​	将指向局部对象 `co_varnames[var_num]` 的引用推入栈顶。

*在 3.12 版本发生变更:* 这个操作码目前只用在保证局部变量被初始化的情况下使用。它不能引发 [`UnboundLocalError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#UnboundLocalError) 。

## **LOAD_FAST_CHECK**(*var_num*)

​	将一个指向局部变量 `co_varnames[var_num]` 的引用推入栈中，如果该局部变量未被初始化，引发一个 [`UnboundLocalError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#UnboundLocalError) 。

> Added in version 3.12.
>

## **LOAD_FAST_AND_CLEAR**(*var_num*)

​	将一个指向局部变量 `co_varnames[var_num]` 的引用推入栈中（如果该局部变量未被初始化，则推入一个 `NULL` ）然后将 `co_varnames[var_num]` 设为 `NULL` 。

> Added in version 3.12.
>

## **STORE_FAST**(*var_num*)

​	将 `STACK.pop()` 存放到局部变量 `co_varnames[var_num]` 。

## **DELETE_FAST**(*var_num*)

​	移除局部对象 `co_varnames[var_num]`。

## **MAKE_CELL**(*i*)

​	在槽位 `i` 中创建一个新单元。 如果该槽位为非空则该值将存储到新单元中。

> Added in version 3.11.
>

## **LOAD_DEREF**(*i*)

​	加载包含在 "fast locals" 存储的 `i` 号槽位中的单元。 将一个指向该单元所包含对象的引用推入栈。

*在 3.11 版本发生变更:* `i` 不再是 [`co_varnames`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#codeobject.co_varnames) 的长度的偏移量。

## **LOAD_FROM_DICT_OR_DEREF**(*i*)

​	Pops a mapping off the stack and looks up the name associated with slot `i` of the "fast locals" storage in this mapping. If the name is not found there, loads it from the cell contained in slot `i`, similar to [`LOAD_DEREF`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-LOAD_DEREF). This is used for loading [closure variables](https://docs.python.org/zh-cn/3.13/glossary.html#term-closure-variable) in class bodies (which previously used `LOAD_CLASSDEREF`) and in [annotation scopes](https://docs.python.org/zh-cn/3.13/reference/executionmodel.html#annotation-scopes) within class bodies.

> Added in version 3.12.
>

## **STORE_DEREF**(*i*)

​	将 `STACK.pop()` 存放到 "fast locals" 存储中包含在 `i` 号槽位的单元内。

*在 3.11 版本发生变更:* `i` 不再是 [`co_varnames`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#codeobject.co_varnames) 的长度的偏移量。

## **DELETE_DEREF**(*i*)

​	清空 "fast locals" 存储中包含在 `i` 号槽位的单元。 被用于 [`del`](https://docs.python.org/zh-cn/3.13/reference/simple_stmts.html#del) 语句。

> Added in version 3.2.
>

*在 3.11 版本发生变更:* `i` 不再是 [`co_varnames`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#codeobject.co_varnames) 的长度的偏移量。

## **COPY_FREE_VARS**(*n*)

​	Copies the `n` [free (closure) variables](https://docs.python.org/zh-cn/3.13/glossary.html#term-closure-variable) from the closure into the frame. Removes the need for special code on the caller's side when calling closures.

> Added in version 3.11.
>

## **RAISE_VARARGS**(*argc*)

​	使用 `raise` 语句的 3 种形式之一引发异常，具体形式取决于 *argc* 的值：

- 0: `raise` (重新引发之前的异常)
- 1: `raise STACK[-1]` （在 `STACK[-1]` 上引发异常实例或类型）
- 2: `raise STACK[-2] from STACK[-1]` （在 `STACK[-2]` 上引发异常实例或类型并将 `__cause__` 设为 `STACK[-1]` ）

## **CALL**(*argc*)

​	调用callable对象，以``argc``指定的参数个数。堆栈上是（升序）：

- 可调用对象
- `self``或者``NULL`
- 其余的位置参数

`argc` 是位置参数的数量，不包括 `self`。

`CALL` 将把所有参数和可调用对象弹出栈，附带这些参数调用该可调用对象，并将该可调用对象的返回值推入栈。

> Added in version 3.11.
>

*在 3.13 版本发生变更:* Callable现在总是出现在栈的同一位置。

*在 3.13 版本发生变更:* 有关键字参数的调用现在由 [`CALL_KW`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-CALL_KW) 处理。

## **CALL_KW**(*argc*)

​	调用一个可调用对象并传入由 `argc` 指定数量的参数。 在栈上为（升序）：

- 可调用对象
- `self``或者``NULL`
- 其余的位置参数
- 关键字参数
- 由关键字参数名称组成的 [`tuple`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#tuple)

`argc` 是位置参数和关键字参数的总数，不包括 `self`。 关键字参数名称元组的长度为关键字参数的数量。

`CALL_KW` 会将所有参数、关键字名称和可调用对象从栈中弹出，用这些参数调用可调用对象，并将返回值推入栈。

> Added in version 3.13.
>

## **CALL_FUNCTION_EX**(*flags*)

​	调用一个可调用对象并附带位置参数和关键字参数变量集合。 如果设置了 *flags* 的最低位，则栈顶包含一个由额外关键字参数组成的映射对象。 在调用该可调用对象之前，映射对象和可迭代对象会被分别“解包”并将它们的内容分别作为关键字参数和位置参数传入。 `CALL_FUNCTION_EX` 会中栈中弹出所有参数及可调用对象，附带这些参数调用该可调用对象，并将可调用对象所返回的返回值推入栈顶。

> Added in version 3.6.
>

## **PUSH_NULL**

​	将一个 `NULL` 推入栈。 在调用序列中用来匹配 [`LOAD_METHOD`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-LOAD_METHOD) 针对非方法调用推入栈的 `NULL`。

> Added in version 3.11.
>

## **MAKE_FUNCTION**

​	Pushes a new function object on the stack built from the code object at `STACK[-1]`.

*在 3.10 版本发生变更:* 旗标值 `0x04` 是一个字符串元组而非字典。

*在 3.11 版本发生变更:* 位于 `STACK[-1]` 的限定名称已被移除。

*在 3.13 版本发生变更:* 操作参数曾指示额外的函数属性，现已被移除。 现在使用 [`SET_FUNCTION_ATTRIBUTE`](https://docs.python.org/zh-cn/3.13/library/dis.html#opcode-SET_FUNCTION_ATTRIBUTE)。

## **SET_FUNCTION_ATTRIBUTE**(*flag*)

​	在函数对象上设置一个属性。将``STACK[-1]``的元素作为函数对象，`STACK[-2]``作为要设置的属性值，消耗这两个元素，将函数对象留在``STACK[-1]`。参数flag决定了要设置哪个属性：

- `0x01` 一个默认值的元组，用于按位置排序的仅限位置形参以及位置或关键字形参
- `0x02` 一个仅限关键字形参的默认值的字典
- `0x04` 一个包含形参标注的字符串元组。
- `0x08` 一个包含用于自由变量的单元的元组，生成一个闭包

> Added in version 3.13.
>

## **BUILD_SLICE**(*argc*)

​	将一个切片对象推入栈中， *argc* 必须为2或3。 如果其为2，则实现：

```
end = STACK.pop()
start = STACK.pop()
STACK.append(slice(start, end))
```

​	如果其为3，则实现：

```
step = STACK.pop()
end = STACK.pop()
start = STACK.pop()
STACK.append(slice(start, end, step))
```

​	详见内置函数 [`slice()`](https://docs.python.org/zh-cn/3.13/library/functions.html#slice) 。

## **EXTENDED_ARG**(*ext*)

​	为任意带有大到无法放入默认的单字节的参数的操作码添加前缀。 *ext* 存放一个附加字节作为参数中的高比特位。 对于每个操作码，最多允许三个 `EXTENDED_ARG` 前缀，构成两字节到三字节的参数。

## **CONVERT_VALUE**(*oparg*)

​	将值转化为字符串，以``oparg``指定的方式:

```
value = STACK.pop()
result = func(value)
STACK.append(result)
```

- `oparg == 1`: 在 *value* 上调用 [`str()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)
- `oparg == 2`: 在 *value* 上调用 [`repr()`](https://docs.python.org/zh-cn/3.13/library/functions.html#repr)
- `oparg == 3`: 在 *value* 上调用 [`ascii()`](https://docs.python.org/zh-cn/3.13/library/functions.html#ascii)

​	用于实现格式化的字面值字符串（f-string）。

> Added in version 3.13.
>

## **FORMAT_SIMPLE**

​	格式化栈顶的值:

```
value = STACK.pop()
result = value.__format__("")
STACK.append(result)
```

​	用于实现格式化的字面值字符串（f-string）。

> Added in version 3.13.
>

## **FORMAT_WITH_SPEC**

​	使用给定的格式说明来格式化给定的值:

```
spec = STACK.pop()
value = STACK.pop()
result = value.__format__(spec)
STACK.append(result)
```

​	用于实现格式化的字面值字符串（f-string）。

> Added in version 3.13.
>

## **MATCH_CLASS**(*count*)

`STACK[-1]` 是一个由关键字属性名称组成的元组，`STACK[-2]` 是要匹配的类， 而 `STACK[-3]` 是匹配的目标主题。 *count* 是位置子模式的数量。

​	弹出 `STACK[-1]`、`STACK[-2]` 和 `STACK[-3]`。 如果 `STACK[-3]` 是 `STACK[-2]` 的实例并且具有 *count* 和 `STACK[-1]` 所要求的位置和关键字属性，则推入一个由已提取属性组成的元组。 在其他情况下，则推入 `None`。

> Added in version 3.10.
>

*在 3.11 版本发生变更:* 在之前的版本中，该指令还会推入一个表示成功 (`True`) 或失败 (`False`) 的布尔值。

## **RESUME**(*context*)

​	空操作。 执行内部追踪、调试和优化检查。

`context` 操作数由两部分组成。 最低的两个比特位指明 `RESUME` 在何处发生：

- `0` 在函数的开头。 函数不能是生成器、协程或者异步生成器。
- `1` 在 `yield` 表达式之后
- `2` 在 `yield from` 表达式之后
- `3` 在 `await` 表达式之后

​	如果 RESUME 在 except 深度 `1` 上发生则下一个比特位值为 `1`，否则为 `0`。

> Added in version 3.11.
>

*在 3.13 版本发生变更:* oparg 值已被修改以包括有关 except 深度的信息

## **RETURN_GENERATOR**

​	从当前帧中创建一个生成器，协程，或者异步生成器。 被用作上述可调用对象的代码对象第一个操作码。 清除当前帧，返回新创建的生成器。

> Added in version 3.11.
>

## **SEND**(*delta*)

​	等价于 `STACK[-1] = STACK[-2].send(STACK[-1])` 。 被用于 `yield from` 和 `await` 语句。

​	如果调用引发了 [`StopIteration`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#StopIteration)，则从栈中弹出最上面的值，推入异常的 `value` 属性，并将字节码计数器值递增 *delta*。

> Added in version 3.11.
>

## **HAVE_ARGUMENT**

​	这不是一个真正的操作码。 它是 [0,255] 范围内使用与不使用参数的操作码（分别是 `< HAVE_ARGUMENT` 和 `>= HAVE_ARGUMENT` ）的分界线。

​	如果你的应用程序使用了伪指令或专用指令，请改用 [`hasarg`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.hasarg) 多项集。

*在 3.6 版本发生变更:* 现在每条指令都带有参数，但操作码 `< HAVE_ARGUMENT` 会忽略它。 之前仅限操作码 `>= HAVE_ARGUMENT` 带有参数。

*在 3.12 版本发生变更:* 伪指令被添加到 [`dis`](https://docs.python.org/zh-cn/3.13/library/dis.html#module-dis) 模块中，对于它们来说，“比较 `HAVE_ARGUMENT` 以确定其是否使用参数”不再有效。

*自 3.13 版本弃用:* 使用 [`hasarg`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.hasarg) 来代替。

## **CALL_INTRINSIC_1**

​	调用内联的函数并附带一个参数。 传入 `STACK[-1]` 作为参数并将 `STACK[-1]` 设为结果。 用于实现对性能不敏感的功能。

​	调用哪个内置函数取决于操作数：

| 操作数                          | 描述                                                         |
| :------------------------------ | :----------------------------------------------------------- |
| `INTRINSIC_1_INVALID`           | 无效                                                         |
| `INTRINSIC_PRINT`               | 将参数打印到标准输出。 被用于 REPL 。                        |
| `INTRINSIC_IMPORT_STAR`         | 为指定模块执行 `import *` 。                                 |
| `INTRINSIC_STOPITERATION_ERROR` | 从 `StopIteration` 异常中提取返回值。                        |
| `INTRINSIC_ASYNC_GEN_WRAP`      | 包裹一个异常生成器值                                         |
| `INTRINSIC_UNARY_POSITIVE`      | 执行单目运算符 `+`                                           |
| `INTRINSIC_LIST_TO_TUPLE`       | 将一个列表转换为元组                                         |
| `INTRINSIC_TYPEVAR`             | 创建一个 [`typing.TypeVar`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.TypeVar) |
| `INTRINSIC_PARAMSPEC`           | 创建一个 [`typing.ParamSpec`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.ParamSpec) |
| `INTRINSIC_TYPEVARTUPLE`        | 创建一个 [`typing.TypeVarTuple`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.TypeVarTuple) |
| `INTRINSIC_SUBSCRIPT_GENERIC`   | 返回 [`typing.Generic`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.Generic) 取参数下标。 |
| `INTRINSIC_TYPEALIAS`           | 创建一个 [`typing.TypeAliasType`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.TypeAliasType) ；被用于 [`type`](https://docs.python.org/zh-cn/3.13/reference/simple_stmts.html#type) 语句。 参数是一个由类型别名的名称、类型形参和值组成的元组。 |

> Added in version 3.12.
>

## **CALL_INTRINSIC_2**

​	调用内联的函数并附带两个参数。 用于实现对性能不敏感的功能:

```
arg2 = STACK.pop()
arg1 = STACK.pop()
result = intrinsic2(arg1, arg2)
STACK.append(result)
```

​	调用哪个内置函数取决于操作数：

| 操作数                               | 描述                                                         |
| :----------------------------------- | :----------------------------------------------------------- |
| `INTRINSIC_2_INVALID`                | 无效                                                         |
| `INTRINSIC_PREP_RERAISE_STAR`        | 计算 [`ExceptionGroup`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ExceptionGroup) 以从 `try-except*` 中引发异常。 |
| `INTRINSIC_TYPEVAR_WITH_BOUND`       | 创建一个带范围的 [`typing.TypeVar`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.TypeVar) 。 |
| `INTRINSIC_TYPEVAR_WITH_CONSTRAINTS` | 创建一个带约束的 [`typing.TypeVar`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.TypeVar) 。 |
| `INTRINSIC_SET_FUNCTION_TYPE_PARAMS` | 为一个函数设置 `__type_params__` 属性。                      |

> Added in version 3.12.
>

**伪指令**

​	这些操作码并不出现在 Python 的字节码之中。 它们被编译器所使用，但在生成字节码之前会被替代成真正的操作码。

## **SETUP_FINALLY**(*target*)

​	为下面的代码块设置一个异常处理器。 如果发生异常，值栈的级别将恢复到当前状态并将控制权移交给位于 `target` 的异常处理器。

## **SETUP_CLEANUP**(*target*)

​	与 `SETUP_FINALLY` 类似，但在出现异常的情况下也会将最后一条指令 (`lasti`) 推入栈以便 `RERAISE` 能恢复它。 如果出现异常，栈级别值和帧上的最后一条指令将恢复为其当前状态，控制权将转移到 `target` 上的异常处理器。

## **SETUP_WITH**(*target*)

​	与 `SETUP_CLEANUP` 类似，但在出现异常的情况下会从栈中再弹出一项然后将控制权转移到 `target` 上的异常处理器。

​	该变体形式用于 [`with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#with) 和 [`async with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#async-with) 结构，它们会将上下文管理器的 [`__enter__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__enter__) 或 [`__aenter__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__aenter__) 的返回值推入栈。

## **POP_BLOCK**

​	标记与最后一个 `SETUP_FINALLY`、`SETUP_CLEANUP` 或 `SETUP_WITH` 相关联的代码块的结束。

## **JUMP**

## **JUMP_NO_INTERRUPT**

​	非定向相对跳转指令会被汇编器转换为它们定向版本（ forward/backward ）

## **LOAD_CLOSURE**(*i*)

​	将一个引用推入到 "fast locals" 存储包含在 `i` 号槽位的单元内。

​	请注意在汇编程序中 `LOAD_CLOSURE` 将被替换为 `LOAD_FAST`。

*在 3.13 版本发生变更:* 该操作码现在是一个伪指令。

## **LOAD_METHOD**

​	经优化的非绑定方法查找。 以在 arg 中设置了旗标的 `LOAD_ATTR` 操作码的形式发出。



## 操作码集合

​	提供这些集合用于字节码指令的自动内省：

*在 3.12 版本发生变更:* 现在此集合还包含一些伪指令和工具化指令。 这些操作码的值 `>= MIN_PSEUDO_OPCODE` 和 `>= MIN_INSTRUMENTED_OPCODE`。

## dis.**opname**

​	操作名称的序列，可使用字节码来索引。

## dis.**opmap**

​	映射操作名称到字节码的字典

## dis.**cmp_op**

​	所有比较操作名称的序列。

## dis.**hasarg**

​	所有使用参数的字节码的序列

> Added in version 3.12.
>

## dis.**hasconst**

​	访问常量的字节码序列。

## dis.**hasfree**

​	Sequence of bytecodes that access a [free (closure) variable](https://docs.python.org/zh-cn/3.13/glossary.html#term-closure-variable). 'free' in this context refers to names in the current scope that are referenced by inner scopes or names in outer scopes that are referenced from this scope. It does *not* include references to global or builtin scopes.

## dis.**hasname**

​	按名称访问属性的字节码序列。

## dis.**hasjump**

​	具有一个跳转目标的字节码序列。 所有跳转都是相对的。

> Added in version 3.13.
>

## dis.**haslocal**

​	访问局部变量的字节码序列。

## dis.**hascompare**

​	布尔运算的字节码序列。

## dis.**hasexc**

​	设置一个异常处理器的字节码序列。

> Added in version 3.12.
>

## dis.**hasjrel**

​	具有相对跳转目标的字节码序列。

*自 3.13 版本弃用:* 所有跳转现在都是相对的。 使用 [`hasjump`](https://docs.python.org/zh-cn/3.13/library/dis.html#dis.hasjump)。

## dis.**hasjabs**

​	具有绝对跳转目标的字节码序列。

*自 3.13 版本弃用:* 所有跳转现在都是相对的。 该列表将为空。
