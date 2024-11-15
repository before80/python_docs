+++
title = "doctest --- 测试交互式的 Python 示例"
date = 2024-11-15T21:03:03+08:00
weight = 30
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/doctest.html](https://docs.python.org/zh-cn/3.13/library/doctest.html)
>
> 收录该文档的时间：`2024-11-15T21:03:03+08:00`

# `doctest` --- 测试交互式的 Python 示例

**源代码：** [Lib/doctest.py](https://github.com/python/cpython/tree/3.13/Lib/doctest.py)

------

[`doctest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#module-doctest) 模块寻找像Python交互式代码的文本，然后执行这些代码来确保它们的确就像展示的那样正确运行，有许多方法来使用doctest：

- 通过验证所有交互式示例仍然按照记录的方式工作，以此来检查模块的文档字符串是否是最新的。
- 通过验证来自一个测试文件或一个测试对象的交互式示例按预期工作，来进行回归测试。
- 为一个包写指导性的文档，用输入输出的例子来说明。 取决于是强调例子还是说明性的文字，这有一种 "文本测试 "或 "可执行文档 "的风格。

​	下面是一个小却完整的示例模块:

```
"""
This is the "example" module.

The example module supplies one function, factorial().  For example,

>>> factorial(5)
120
"""

def factorial(n):
    """Return the factorial of n, an exact integer >= 0.

    >>> [factorial(n) for n in range(6)]
    [1, 1, 2, 6, 24, 120]
    >>> factorial(30)
    265252859812191058636308480000000
    >>> factorial(-1)
    Traceback (most recent call last):
        ...
    ValueError: n must be >= 0

    Factorials of floats are OK, but the float must be an exact integer:
    >>> factorial(30.1)
    Traceback (most recent call last):
        ...
    ValueError: n must be exact integer
    >>> factorial(30.0)
    265252859812191058636308480000000

    It must also not be ridiculously large:
    >>> factorial(1e100)
    Traceback (most recent call last):
        ...
    OverflowError: n too large
    """

    import math
    if not n >= 0:
        raise ValueError("n must be >= 0")
    if math.floor(n) != n:
        raise ValueError("n must be exact integer")
    if n+1 == n:  # catch a value like 1e300
        raise OverflowError("n too large")
    result = 1
    factor = 2
    while factor <= n:
        result *= factor
        factor += 1
    return result


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

​	如果你直接在命令行里运行 `example.py` ， [`doctest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#module-doctest) 将发挥它的作用。

```
$ python example.py
$
```

​	没有输出！ 这很正常，这意味着所有的例子都成功了。 把 `-v` 传给脚本，[`doctest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#module-doctest) 会打印出它所尝试的详细日志，并在最后打印出一个总结。

```
$ python example.py -v
Trying:
    factorial(5)
Expecting:
    120
ok
Trying:
    [factorial(n) for n in range(6)]
Expecting:
    [1, 1, 2, 6, 24, 120]
ok
```

​	以此类推，最终以：

```
Trying:
    factorial(1e100)
Expecting:
    Traceback (most recent call last):
        ...
    OverflowError: n too large
ok
2 items passed all tests:
   1 test in __main__
   6 tests in __main__.factorial
7 tests in 2 items.
7 passed.
Test passed.
$
```

​	这就是关于高效使用 [`doctest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#module-doctest) 你所需要知道的一切！ 开始上手吧。 下面的小节提供了完整细节。 请注意在标准 Python 测试套件和库中有许多 doctest 的例子。 特别有用的例子可以在标准测试文件 `Lib/test/test_doctest/test_doctest.py` 中找到。



## 简单用法：检查Docstrings中的示例

​	开始使用 doctest 的最简单方式（但不一定是你今后沿用的方式）是这样结束每个模块 `M`:

```
if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

`doctest` 会随后检查模块 `M` 中的文档字符串。

​	以脚本形式运行该模块会使文档中的例子得到执行和验证:

```
python M.py
```

​	这不会显示任何东西，除非一个例子失败了，在这种情况下，失败的例子和失败的原因会被打印到stdout，最后一行的输出是 `***Test Failed*** N failures.`，其中 *N* 是失败的例子的数量。

​	用 `-v` 来运行它来切换，而不是:

```
python M.py -v
```

​	并将所有尝试过的例子的详细报告打印到标准输出，最后还有各种总结。

​	你可以通过向 [`testmod()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testmod) 传递 `verbose=True` 来强制执行 verbose 模式，或者通过传递 `verbose=False` 来禁止它。 在这两种情况下，`sys.argv` 都不会被 [`testmod()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testmod) 检查（所以传递 `-v` 或不传递都没有影响）。

​	还有一个命令行快捷方式用于运行 [`testmod()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testmod)。 你可以指示Python解释器直接从标准库中运行doctest模块，并在命令行中传递模块名称:

```
python -m doctest -v example.py
```

​	这将导入 `example.py` 作为一个独立的模块，并对其运行 [`testmod()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testmod)。 注意，如果该文件是一个包的一部分，并且从该包中导入了其他子模块，这可能无法正确工作。

​	关于 [`testmod()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testmod) 的更多信息，请参见 [基本API](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest-basic-api) 部分。



## 简单的用法：检查文本文件中的例子

​	doctest 的另一个简单应用是测试文本文件中的交互式例子。 这可以用 [`testfile()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testfile) 函数来完成:

```
import doctest
doctest.testfile("example.txt")
```

​	这个简短的脚本执行并验证文件 `example.txt` 中包含的任何交互式 Python 示例。该文件的内容被当作一个巨大的文档串来处理；该文件不需要包含一个Python程序！例如，也许 `example.txt` 包含以下内容:

```
The ``example`` module
======================

Using ``factorial``
-------------------

This is an example text file in reStructuredText format.  First import
``factorial`` from the ``example`` module:

    >>> from example import factorial

Now use it:

    >>> factorial(6)
    120
```

​	运行 `doctest.testfile("example.txt")`，然后发现这个文档中的错误:

```
File "./example.txt", line 14, in example.txt
Failed example:
    factorial(6)
Expected:
    120
Got:
    720
```

​	与 [`testmod()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testmod) 一样， [`testfile()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testfile) 不会显示任何东西，除非一个例子失败。 如果一个例子失败了，那么失败的例子和失败的原因将被打印到stdout，使用的格式与 [`testmod()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testmod) 相同。

​	默认情况下，[`testfile()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testfile) 在调用模块的目录中寻找文件。参见章节 [基本API](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest-basic-api)，了解可用于告诉它在其他位置寻找文件的可选参数的描述。

​	像 [`testmod()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testmod) 一样，[`testfile()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testfile) 的详细程度可以通过命令行 `-v` 切换或可选的关键字参数 *verbose* 来设置。

​	还有一个命令行快捷方式用于运行 [`testfile()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testfile)。 你可以指示Python解释器直接从标准库中运行doctest模块，并在命令行中传递文件名:

```
python -m doctest -v example.txt
```

​	因为文件名没有以 `.py` 结尾，[`doctest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#module-doctest) 推断它必须用 [`testfile()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testfile) 运行，而不是 [`testmod()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testmod)。

​	关于 [`testfile()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testfile) 的更多信息，请参见 [基本API](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest-basic-api) 一节。



## 它是如何工作的

​	这一节详细研究了doctest的工作原理：它查看哪些文档串，它如何找到交互式的用例，它使用什么执行环境，它如何处理异常，以及如何用选项标志来控制其行为。这是你写doctest例子所需要知道的信息；关于在这些例子上实际运行doctest的信息，请看下面的章节。



### 哪些文件串被检查了？

​	模块的文档串以及所有函数、类和方法的文档串都将被搜索。 导入模块的对象不被搜索。

​	此外，有时你会希望测试成为模块的一部分但不是帮助文本的一部分，这就要求测试不包括在文档字符串中。 在此情况下 doctest 会查找一个名为 `__test__` 的模块级变量并用它来定位其他测试。 如果 `M.__test__` 存在，则它必须是一个字典，其中每个条目都是将（字符串）名称映射到函数对象、类对象或字符串。 从 `M.__test__` 找到的函数和类对象的文档字符串将会被搜索，字符串会被当作文档字符串来处理。 在输出中，`M.__test__` 中的键 `K` 将作为名称 `M.__test__.K` 出现。

​	例如，将这段代码放在 `example.py` 的开头:

```
__test__ = {
    'numbers': """
>>> factorial(6)
720

>>> [factorial(n) for n in range(6)]
[1, 1, 2, 6, 24, 120]
"""
}
```

`example.__test__["numbers"]` 的值将被视为一个文档字符串并且其中的所有测试将被运行。 重要的注意事项是该值可以被映射到一个函数、类对象或模块；如果是这样的话，`doctest` 会递归地搜索它们的文档字符串，然后扫描其中的测试。

​	任何发现的类都会以类似的方式进行递归搜索，以测试其包含的方法和嵌套类中的文档串。



### 文档串的例子是如何被识别的？

​	在大多数情况下，对交互式控制台会话的复制和粘贴功能工作得很好，但是 doctest 并不试图对任何特定的 Python shell 进行精确的模拟。



```
>>> # comments are ignored
>>> x = 12
>>> x
12
>>> if x == 13:
...     print("yes")
... else:
...     print("no")
...     print("NO")
...     print("NO!!!")
...
no
NO
NO!!!
>>>
```

​	任何预期的输出必须紧随包含代码的最后 `'>>> '` 或 `'... '` 行，预期的输出（如果有的话）延伸到下一 `'>>> '` 行或全空白行。

​	fine输出：

- 预期输出不能包含一个全白的行，因为这样的行被认为是预期输出的结束信号。 如果预期的输出包含一个空行，在你的测试例子中，在每一个预期有空行的地方加上 `<BLANKLINE>`。

- 所有硬制表符都被扩展为空格，使用 8 列的制表符。由测试代码生成的输出中的制表符不会被修改。 因为样本输出中的任何硬制表符都会被扩展，这意味着如果代码输出包括硬制表符，文档测试通过的唯一方法是 [`NORMALIZE_WHITESPACE`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.NORMALIZE_WHITESPACE) 选项或者 [指令](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest-directives) 是有效的。 另外，测试可以被重写，以捕获输出并将其与预期值进行比较，作为测试的一部分。这种对源码中标签的处理是通过试错得出的，并被证明是最不容易出错的处理方式。通过编写一个自定义的 [`DocTestParser`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestParser) 类，可以使用一个不同的算法来处理标签。

- 向stdout的输出被捕获，但不向stderr输出（异常回溯通过不同的方式被捕获）。

- 如果你在交互式会话中通过反斜线续行，或出于任何其他原因使用反斜线，你应该使用原始文件串，它将完全保留你输入的反斜线:

  

  ```
  >>> def f(x):
  ...     r'''Backslashes in a raw docstring: m\n'''
  ...
  >>> print(f.__doc__)
  Backslashes in a raw docstring: m\n
  ```

  否则，反斜杠将被解释为字符串的一部分。例如，上面的 `\n` 会被解释为一个换行符。 另外，你可以在doctest版本中把每个反斜杠加倍（而不使用原始字符串）:

  

  ```
  >>> def f(x):
  ...     '''Backslashes in a raw docstring: m\\n'''
  ...
  >>> print(f.__doc__)
  Backslashes in a raw docstring: m\n
  ```

- 起始列并不重要:

  

  ```
  >>> assert "Easy!"
        >>> import math
            >>> math.floor(1.9)
            1
  ```

  并从预期的输出中剥离出与开始该例子的初始 `'>>> '` 行中出现的同样多的前导空白字符。



### 什么是执行上下文？

​	默认情况下，每次 [`doctest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#module-doctest) 找到要测试的文档字符串时，它都会使用 `M` 的全局变量的一个 *浅拷贝*，这样运行测试就不会改变模块真正的全局变量，并且 `M` 中的一个测试也不会留下任何痕迹从而意外地让另一个测试通过。 这意味着示例可以自由地使用 `M` 中任何在最高层级上定义的名称，以及正在运行的文档字符串中之前定义的名称。 示例将无法看到在其他文档字符串中定义的名称。

​	你可以通过将 `globs=your_dict` 传递给 [`testmod()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testmod) 或 [`testfile()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testfile) 来强制使用你自己的dict作为执行环境。



### 异常如何处理？

​	没问题，只要回溯是这个例子产生的唯一输出：只要粘贴回溯即可。[[1\]](https://docs.python.org/zh-cn/3.13/library/doctest.html#id2) 由于回溯所包含的细节可能会迅速变化（例如，确切的文件路径和行号），这是doctest努力使其接受的内容具有灵活性的一种情况。

​	简单实例:



```
>>> [1, 2, 3].remove(42)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: list.remove(x): x not in list
```

​	如果 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError) 被触发，该测试就会成功，`list.remove(x): x not in list` 的细节如图所示。

​	异常的预期输出必须以回溯头开始，可以是以下两行中的任何一行，缩进程度与例子中的第一行相同:

```
Traceback (most recent call last):
Traceback (innermost last):
```

​	回溯头的后面是一个可选的回溯堆栈，其内容被doctest忽略。 回溯堆栈通常是省略的，或者从交互式会话中逐字复制的。

​	回溯堆栈的后面是最有用的部分：包含异常类型和细节的一行（几行）。 这通常是回溯的最后一行，但如果异常有多行细节，则可以延伸到多行:



```
>>> raise ValueError('multi\n    line\ndetail')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: multi
    line
detail
```

​	最后三行（以 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError) 开头）将与异常的类型和细节进行比较，其余的被忽略。

​	最佳实践是省略回溯栈，除非它为这个例子增加了重要的文档价值。 因此，最后一个例子可能更好，因为:



```
>>> raise ValueError('multi\n    line\ndetail')
Traceback (most recent call last):
    ...
ValueError: multi
    line
detail
```

​	请注意，回溯的处理方式非常特别。 特别是，在重写的例子中，`...` 的使用与 doctest 的 [`ELLIPSIS`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.ELLIPSIS) 选项无关。 该例子中的省略号可以不写，也可以是三个（或三百个）逗号或数字，或者是一个缩进的 Monty Python 短剧的剧本。

​	有些细节你应该读一遍，但不需要记住：

- Doctest 不能猜测你的预期输出是来自异常回溯还是来自普通打印。 因此，例如，一个期望 `ValueError: 42 is prime` 的用例将通过测试，无论 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError) 是真的被触发，或者该用例只是打印了该回溯文本。 在实践中，普通输出很少以回溯标题行开始，所以这不会产生真正的问题。
- 回溯堆栈的每一行（如果有的话）必须比例子的第一行缩进， *或者* 以一个非字母数字的字符开始。回溯头之后的第一行缩进程度相同，并且以字母数字开始，被认为是异常细节的开始。当然，这对真正的回溯来说是正确的事情。
- 当 [`IGNORE_EXCEPTION_DETAIL`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.IGNORE_EXCEPTION_DETAIL) doctest 选项被指定时，最左边的冒号后面的所有内容以及异常名称中的任何模块信息都被忽略。
- 交互式 shell 省略了一些 [`SyntaxError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#SyntaxError) 的回溯头行。但 doctest 使用回溯头行来区分异常和非异常。所以在罕见的情况下，如果你需要测试一个省略了回溯头的 [`SyntaxError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#SyntaxError)，你将需要手动添加回溯头行到你的测试用例中。

- 对于某些异常，Python 会使用 `^` 标记和波浪号来显示错误位置:

  

  ```
  >>> 1 + None
    File "<stdin>", line 1
      1 + None
      ~~^~~~~~
  TypeError: unsupported operand type(s) for +: 'int' and 'NoneType'
  ```

  由于显示错误位置的行在异常类型和细节之前，它们不被doctest检查。 例如，下面的测试会通过，尽管它把 `^` 标记放在了错误的位置:

  

  ```
  >>> 1 + None
    File "<stdin>", line 1
      1 + None
      ^~~~~~~~
  TypeError: unsupported operand type(s) for +: 'int' and 'NoneType'
  ```



### 选项标记

​	一系列选项旗标控制着 doctest 的各方面行为。 旗标的符号名称以模块常量的形式提供，可以一起 [bitwise ORed](https://docs.python.org/zh-cn/3.13/reference/expressions.html#bitwise) 并传递给各种函数。 这些名称也可以在 [doctest directives](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest-directives) 中使用，并且可以通过 `-o` 选项传递给 doctest 命令行接口。

*Added in version 3.4:* 命令行选项 `-o` 。

​	第一组选项定义了测试语义，控制doctest如何决定实际输出是否与用例的预期输出相匹配方面的问题。

## doctest.**DONT_ACCEPT_TRUE_FOR_1**

​	默认情况下，如果一个预期的输出块只包含 `1`，那么实际的输出块只包含 `1` 或只包含 `True` 就被认为是匹配的，同样，`0` 与 `False` 也是如此。 当 [`DONT_ACCEPT_TRUE_FOR_1`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DONT_ACCEPT_TRUE_FOR_1) 被指定时，两种替换都不允许。 默认行为是为了适应 Python 将许多函数的返回类型从整数改为布尔值；期望 "小整数" 输出的测试在这些情况下仍然有效。 这个选项可能会消失，但不会在几年内消失。

## doctest.**DONT_ACCEPT_BLANKLINE**

​	默认情况下，如果一个预期输出块包含一个只包含字符串 `<BLANKLINE>` 的行，那么该行将与实际输出中的一个空行相匹配。 因为一个真正的空行是对预期输出的限定，这是传达预期空行的唯一方法。 当 [`DONT_ACCEPT_BLANKLINE`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DONT_ACCEPT_BLANKLINE) 被指定时，这种替换是不允许的。

## doctest.**NORMALIZE_WHITESPACE**

​	当指定时，所有的空白序列（空白和换行）都被视为相等。预期输出中的任何空白序列将与实际输出中的任何空白序列匹配。默认情况下，空白必须完全匹配。 [`NORMALIZE_WHITESPACE`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.NORMALIZE_WHITESPACE) 在预期输出非常长的一行，而你想把它包在源代码的多行中时特别有用。

## doctest.**ELLIPSIS**

​	当指定时，预期输出中的省略号（ `...` ）可以匹配实际输出中的任何子串。这包括跨行的子串和空子串，所以最好保持简单的用法。复杂的用法会导致与 `.*` 在正则表达式中容易出现的 "oops, it matched too much!"相同的意外情况。

## doctest.**IGNORE_EXCEPTION_DETAIL**

​	当被指定时，只要有预期类型的异常被引发 doctests 就会预期异常测试通过，即使细节（消息和完整限定名称）并不匹配。

​	举例来说，一个预期 `ValueError: 42` 的用例在实际引发的异常为 `ValueError: 3*14` 将会通过，但是如果是引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError) 则将会失败。 它也将忽略任何包括在异常类前面的完整限定名称，该名称在所使用的不同 Python 版本和代码/库中可能会不同。 因此，以下三种形式对于指定的旗标均有效:



```
>>> raise Exception('message')
Traceback (most recent call last):
Exception: message

>>> raise Exception('message')
Traceback (most recent call last):
builtins.Exception: message

>>> raise Exception('message')
Traceback (most recent call last):
__main__.Exception: message
```

​	请注意 [`ELLIPSIS`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.ELLIPSIS) 也可以被用来忽略异常消息中的细节，但这样的测试仍然可能根据特定模块名称是否存在或是否完全匹配而失败。

*在 3.2 版本发生变更:* [`IGNORE_EXCEPTION_DETAIL`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.IGNORE_EXCEPTION_DETAIL) 现在也忽略了与包含被测异常的模块有关的任何信息。

## doctest.**SKIP**

​	当指定时，完全不运行这个用例。 这在doctest用例既是文档又是测试案例的情况下很有用，一个例子应该包括在文档中，但不应该被检查。例如，这个例子的输出可能是随机的；或者这个例子可能依赖于测试驱动程序所不能使用的资源。

​	SKIP标志也可用于临时 "注释" 用例。

## doctest.**COMPARISON_FLAGS**

​	一个比特或运算将上述所有的比较标志放在一起。

​	第二组选项控制测试失败的报告方式：

## doctest.**REPORT_UDIFF**

​	当指定时，涉及多行预期和实际输出的故障将使用统一的差异来显示。

## doctest.**REPORT_CDIFF**

​	当指定时，涉及多行预期和实际输出的故障将使用上下文差异来显示。

## doctest.**REPORT_NDIFF**

​	当指定时，差异由 `difflib.Differ` 来计算，使用与流行的:file:ndiff.py`工具相同的算法。这是唯一一种标记行内和行间差异的方法。 例如，如果一行预期输出包含数字 ``1` ，而实际输出包含字母 `l` ，那么就会插入一行，用圆点标记不匹配的列位置。

## doctest.**REPORT_ONLY_FIRST_FAILURE**

​	当指定时，在每个 doctest 中显示第一个失败的用例，但隐藏所有其余用例的输出。 这将防止 doctest 报告由于先前的失败而中断的正确用例；但也可能隐藏独立于第一个失败的不正确用例。 当 [`REPORT_ONLY_FIRST_FAILURE`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.REPORT_ONLY_FIRST_FAILURE) 被指定时，其余的用例仍然被运行，并且仍然计入报告的失败总数；只是输出被隐藏了。

## doctest.**FAIL_FAST**

​	当指定时，在第一个失败的用例后退出，不尝试运行其余的用例。因此，报告的失败次数最多为1。这个标志在调试时可能很有用，因为第一个失败后的用例甚至不会产生调试输出。

​	doctest 命令行接受选项 `-f` 作为 `-o FAIL_FAST` 的简洁形式。

*Added in version 3.4.*

## doctest.**REPORTING_FLAGS**

​	一个比特或操作将上述所有的报告标志组合在一起。

​	还有一种方法可以注册新的选项标志名称，不过这并不有用，除非你打算通过子类来扩展 [`doctest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#module-doctest) 内部。

## doctest.**register_optionflag**(*name*)

​	用给定的名称创建一个新的选项标志，并返回新标志的整数值。 [`register_optionflag()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.register_optionflag) 可以在继承 [`OutputChecker`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.OutputChecker) 或 [`DocTestRunner`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner) 时使用，以创建子类支持的新选项。 [`register_optionflag()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.register_optionflag) 应始终使用以下方式调用:

```
MY_FLAG = register_optionflag('MY_FLAG')
```



### 指令

​	Doctest指令可以用来修改单个例子的 [option flags](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest-options) 。 Doctest指令是在一个用例的源代码后面的特殊Python注释。

```
directive             ::=  "#" "doctest:" directive_options
directive_options     ::=  directive_option ("," directive_option)*
directive_option      ::=  on_or_off directive_option_name
on_or_off             ::=  "+" | "-"
directive_option_name ::=  "DONT_ACCEPT_BLANKLINE" | "NORMALIZE_WHITESPACE" | ...
```

`+` 或 `-` 与指令选项名称之间不允许有空格。 指令选项名称可以是上面解释的任何一个选项标志名称。

​	一个用例的 doctest 指令可以修改 doctest 对该用例的行为。 使用 `+` 来启用指定的行为，或者使用 `-` 来禁用它。

​	例如，这个测试将会通过:



```
>>> print(list(range(20)))  # doctest: +NORMALIZE_WHITESPACE
[0,   1,  2,  3,  4,  5,  6,  7,  8,  9,
10,  11, 12, 13, 14, 15, 16, 17, 18, 19]
```

​	如果没有这个指令则它将失败，因为实际的输出在只有个位数的列表元素前没有两个空格，也因为实际的输出是单行的。 这个测试也会通过，并且也需要一个指令来完成:



```
>>> print(list(range(20)))  # doctest: +ELLIPSIS
[0, 1, ..., 18, 19]
```

​	在单个物理行中可以使用多条指令，以逗号分隔:



```
>>> print(list(range(20)))  # doctest: +ELLIPSIS, +NORMALIZE_WHITESPACE
[0,    1, ...,   18,    19]
```

​	如果在单个用例中使用了多条指令注释，则它们会被合并:



```
>>> print(list(range(20)))  # doctest: +ELLIPSIS
...                         # doctest: +NORMALIZE_WHITESPACE
[0,    1, ...,   18,    19]
```

​	正如前面的例子所示，你可以在你的用例中添加只包含指令的行 `...`。 当一个用例太长以至于不能方便地放到同一行时这将会很有用:



```
>>> print(list(range(5)) + list(range(10, 20)) + list(range(30, 40)))
... # doctest: +ELLIPSIS
[0, ..., 4, 10, ..., 19, 30, ..., 39]
```

​	请注意，由于所有的选项都是默认禁用的，而指令只适用于它们出现的用例，所以启用选项 (通过指令中的 `+`) 通常是唯一有意义的选择。 然而，选项标志也可以被传递给运行测试的函数，建立不同的默认值。 在这种情况下，通过指令中的 `-` 来禁用一个选项可能是有用的。



### 警告

[`doctest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#module-doctest) 是严格地要求在预期输出中完全匹配。 如果哪怕只有一个字符不匹配，测试就会失败。 这可能会让你吃惊几次，在你确切地了解到 Python 对输出的保证和不保证之前。 例如，当打印一个集合时，Python 不保证元素以任何特定的顺序被打印出来，所以像:



```
>>> foo()
{"spam", "eggs"}
```

​	是不可靠的！一个变通方法是用:



```
>>> foo() == {"spam", "eggs"}
True
```

​	来取代。另一个是使用



```
>>> d = sorted(foo())
>>> d
['eggs', 'spam']
```

​	还有其他的问题，但你会明白的。

​	另一个不好的做法是打印嵌入了对象地址的值，例如



```
>>> id(1.0)  # certain to fail some of the time  
7948648
>>> class C: pass
>>> C()  # the default repr() for instances embeds an address   
<C object at 0x00AC18F0>
```

[`ELLIPSIS`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.ELLIPSIS) 指令为之前的例子提供了一个不错的方案:



```
>>> C()  # doctest: +ELLIPSIS
<C object at 0x...>
```

​	浮点数在不同的平台上也会有小的输出变化，因为Python在浮点数的格式化上依赖于平台的C库，而C库在这个问题上的质量差异很大。:



```
>>> 1./7  # risky
0.14285714285714285
>>> print(1./7) # safer
0.142857142857
>>> print(round(1./7, 6)) # much safer
0.142857
```

​	形式 `I/2.**J` 的数字在所有的平台上都是安全的，我经常设计一些测试的用例来产生该形式的数:



```
>>> 3./4  # utterly safe
0.75
```

​	简单的分数也更容易让人理解，这也使得文件更加完善。



## 基本API

​	函数 [`testmod()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testmod) 和 [`testfile()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testfile) 为 doctest 提供了一个简单的接口，应该足以满足大多数基本用途。关于这两个函数的不太正式的介绍，请参见 [简单用法：检查Docstrings中的示例](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest-simple-testmod) 和 [简单的用法：检查文本文件中的例子](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest-simple-testfile) 部分。

## doctest.**testfile**(*filename*, *module_relative=True*, *name=None*, *package=None*, *globs=None*, *verbose=None*, *report=True*, *optionflags=0*, *extraglobs=None*, *raise_on_error=False*, *parser=DocTestParser()*, *encoding=None*)

​	除了*filename*，所有的参数都是可选的，而且应该以关键字的形式指定。

​	测试名为 *filename* 的文件中的用例。 返回 `(failure_count, test_count)`。

​	可选参数 *module_relative* 指定了文件名的解释方式。

- 如果 *module_relative* 是 `True` (默认)，那么 *filename* 指定一个独立于操作系统的模块相对路径。 默认情况下，这个路径是相对于调用模块的目录的；但是如果指定了 *package* 参数，那么它就是相对于该包的。 为了保证操作系统的独立性， *filename* 应该使用字符来分隔路径段，并且不能是一个绝对路径 (即不能以 `/` 开始)。
- 如果 *module_relative* 是 `False`，那么 *filename* 指定了一个操作系统特定的路径。路径可以是绝对的，也可以是相对的；相对路径是相对于当前工作目录而言的。

​	可选参数 *name* 给出了测试的名称；默认情况下，或者如果是 `None`，那么使用 `os.path.basename(filename)`。

​	可选参数 *package* 是一个 Python 包或一个 Python 包的名字，其目录应被用作模块相关文件名的基础目录。 如果没有指定包，那么调用模块的目录将作为模块相关文件名的基础目录。如果 *module_relative* 是 `False`，那么指定 *package* 是错误的。

​	可选参数 *globs* 给出了一个在执行示例时用作全局变量的dict。 这个dict的一个新的浅层副本将为doctest创建，因此它的用例将从一个干净的地方开始。默认情况下，或者如果为 `None`，使用一个新的空dict。

​	可选参数 *extraglobs* 给出了一个合并到用于执行用例全局变量中的dict。 这就像 [`dict.update()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#dict.update) 一样：如果 *globs* 和 *extraglobs* 有一个共同的键，那么 *extraglobs* 中的相关值会出现在合并的dict中。 默认情况下，或者为 `None` ，则不使用额外的全局变量。这是一个高级功能，允许对 doctest 进行参数化。例如，可以为一个基类写一个测试，使用该类的通用名称，然后通过传递一个 *extraglobs* dict，将通用名称映射到要测试的子类，从而重复用于测试任何数量的子类。

​	可选的参数 *verbose* 如果为真值会打印很多东西，如果为假值则只打印失败信息；默认情况下，或者为 `None`，只有当 `'-v'` 在 `sys.argv` 中时才为真值。

​	可选参数 *report* 为True时，在结尾处打印一个总结，否则在结尾处什么都不打印。 在verbose模式下，总结是详细的，否则总结是非常简短的（事实上，如果所有的测试都通过了，总结就是空的）。

​	可选参数 *optionflags* （默认值为0）是选项标志的 [bitwise OR](https://docs.python.org/zh-cn/3.13/reference/expressions.html#bitwise) 。参见章节 [选项标记](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest-options) 。

​	可选参数 *raise_on_error* 默认为False。 如果是True，在一个用例中第一次出现失败或意外的异常时，会触发一个异常。这允许对失败进行事后调试。默认行为是继续运行例子。

​	可选参数 *parser* 指定一个 [`DocTestParser`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestParser) （或子类），它应该被用来从文件中提取测试。它默认为一个普通的解析器（即 `DocTestParser()`）。

​	可选参数 *encoding* 指定了一个编码，应该用来将文件转换为unicode。

## doctest.**testmod**(*m=None*, *name=None*, *globs=None*, *verbose=None*, *report=True*, *optionflags=0*, *extraglobs=None*, *raise_on_error=False*, *exclude_empty=False*)

​	所有的参数都是可选的，除了 *m* 之外，都应该以关键字的形式指定。

​	测试从模块 *m* （或模块 [`__main__`](https://docs.python.org/zh-cn/3.13/library/__main__.html#module-__main__) ，如果 *m* 没有被提供或为 `None` ）可达到的函数和类的文档串中的用例，从 `m.__doc__` 开始。

​	还会测试从 dict `m.__test__` 可达到的用例，如果存在的话。 `m.__test__` 将名称（字符串）映射到函数、类和字符串；将在函数和类的文档字符串中搜索用例；字符串将被直接搜索，就像它们是文档字符串一样。

​	只搜索附属于模块 *m* 中的对象的文档串。

​	返回 `(failure_count, test_count)` 。

​	可选参数 *name* 给出了模块的名称；默认情况下，或者如果为 `None` ，则为 `m.__name__` 。

​	可选参数 *exclude_empty* 默认为假值。 如果为真值，则未找到任何 doctests 的对象将被排除在考虑范围之外。 默认情况下将做向下兼容处理，因而仍然使用 [`doctest.master.summarize`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner.summarize) 来配合 [`testmod()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testmod) 的代码会继续得到没有测试的对象的输出。 转给较新的 [`DocTestFinder`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestFinder) 构造器的 *exclude_empty* 参数默认为真值。

​	可选参数 *extraglobs* 、 *verbose* 、 *report* 、 *optionflags* 、 *raise_on_error* 和 *globs* 与上述函数 [`testfile()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testfile) 的参数相同，只是 *globs* 默认为 `m.__dict__` 。

## doctest.**run_docstring_examples**(*f*, *globs*, *verbose=False*, *name='NoName'*, *compileflags=None*, *optionflags=0*)

​	与对象 *f* 相关的测试用例；例如， *f* 可以是一个字符串、一个模块、一个函数或一个类对象。

​	dict 参数 *globs* 的浅层拷贝被用于执行环境。

​	可选参数 *name* 在失败信息中使用，默认为 `"NoName"` 。

​	如果可选参数 *verbose* 为真，即使没有失败也会产生输出。 默认情况下，只有在用例失败的情况下才会产生输出。

​	可选参数 *compileflags* 给出了Python编译器在运行例子时应该使用的标志集。默认情况下，或者如果为 `None` ，标志是根据 *globs* 中发现的未来特征集推导出来的。

​	可选参数 *optionflags* 的作用与上述 [`testfile()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testfile) 函数中的相同。



## Unittest API

​	随着你带有文档测试的模块不断增加，你会希望以系统性地方式运行所有的文档测试。 [`doctest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#module-doctest) 提供了两个函数可用来根据模块和包含文档测试的文本文件创建 [`unittest`](https://docs.python.org/zh-cn/3.13/library/unittest.html#module-unittest) 测试套件。 要与 [`unittest`](https://docs.python.org/zh-cn/3.13/library/unittest.html#module-unittest) 测试发现功能实现集成，请在你的测试模块中包括一个 [load_tests](https://docs.python.org/zh-cn/3.13/library/unittest.html#load-tests-protocol) 函数:

```
import unittest
import doctest
import my_module_with_doctests

def load_tests(loader, tests, ignore):
    tests.addTests(doctest.DocTestSuite(my_module_with_doctests))
    return tests
```

​	有两个主要函数用于从文本文件和带doctest的模块中创建 [`unittest.TestSuite`](https://docs.python.org/zh-cn/3.13/library/unittest.html#unittest.TestSuite) 实例。

## doctest.**DocFileSuite**(**paths*, *module_relative=True*, *package=None*, *setUp=None*, *tearDown=None*, *globs=None*, *optionflags=0*, *parser=DocTestParser()*, *encoding=None*)

​	将一个或多个文本文件中的doctest测试转换为一个 [`unittest.TestSuite`](https://docs.python.org/zh-cn/3.13/library/unittest.html#unittest.TestSuite) 。

​	返回的 [`unittest.TestSuite`](https://docs.python.org/zh-cn/3.13/library/unittest.html#unittest.TestSuite) 将由 unittest 框架运行并运行每个文件中的交互式示例。 如果任何文件中的示例运行失败，则合成的单元测试就将失败，并引发一个 [`failureException`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.failureException) 异常显示包含该测试的文件名以及（有时为近似的）行号。 如果某个文件中的示例被跳过，则合成的单元测试也会被标记为跳过。

​	传递一个或多个要检查的文本文件的路径（作为字符串）。

​	选项可以作为关键字参数提供：

​	可选参数 *module_relative* 指定了 *paths* 中的文件名应该如何解释。

- 如果 *module_relative* 是 `True` （默认值），那么 *paths* 中的每个文件名都指定了一个独立于操作系统的模块相对路径。 默认情况下，这个路径是相对于调用模块的目录的；但是如果指定了 *package* 参数，那么它就是相对于该包的。 为了保证操作系统的独立性，每个文件名都应该使用字符来分隔路径段，并且不能是绝对路径（即不能以 `/` 开始）。
- 如果 *module_relative* 是 `False` ，那么 *paths* 中的每个文件名都指定了一个操作系统特定的路径。 路径可以是绝对的，也可以是相对的；相对路径是关于当前工作目录的解析。

​	可选参数 *package* 是一个Python包或一个Python包的名字，其目录应该被用作 *paths* 中模块相关文件名的基本目录。 如果没有指定包，那么调用模块的目录将作为模块相关文件名的基础目录。 如果 *module_relative* 是 `False` ，那么指定 *package* 是错误的。

​	可选的参数 *setUp* 为测试套件指定了一个设置函数。在运行每个文件中的测试之前，它被调用。 *setUp* 函数将被传递给一个 [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 对象。 setUp函数可以通过测试的 *globs* 属性访问测试的全局变量。

​	可选的参数 *tearDown* 指定了测试套件的卸载函数。 在运行每个文件中的测试后，它会被调用。 *tearDown* 函数将被传递一个 [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 对象。 setUp函数可以通过测试的 *globs* 属性访问测试的全局变量。

​	可选参数 *globs* 是一个包含测试的初始全局变量的字典。 这个字典的一个新副本为每个测试创建。 默认情况下， *globs* 是一个新的空字典。

​	可选参数 *optionflags* 为测试指定默认的doctest选项，通过将各个选项的标志连接在一起创建。 参见章节 [选项标记](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest-options) 。参见下面的函数 [`set_unittest_reportflags()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.set_unittest_reportflags) ，以了解设置报告选项的更好方法。

​	可选参数 *parser* 指定一个 [`DocTestParser`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestParser) （或子类），它应该被用来从文件中提取测试。它默认为一个普通的解析器（即 `DocTestParser()`）。

​	可选参数 *encoding* 指定了一个编码，应该用来将文件转换为unicode。

​	该全局 `__file__` 被添加到提供给用 [`DocFileSuite()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocFileSuite) 从文本文件加载的doctest的全局变量中。

## doctest.**DocTestSuite**(*module=None*, *globs=None*, *extraglobs=None*, *test_finder=None*, *setUp=None*, *tearDown=None*, *optionflags=0*, *checker=None*)

​	将一个模块的doctest测试转换为 [`unittest.TestSuite`](https://docs.python.org/zh-cn/3.13/library/unittest.html#unittest.TestSuite) 。

​	返回的 [`unittest.TestSuite`](https://docs.python.org/zh-cn/3.13/library/unittest.html#unittest.TestSuite) 将由 unittest 框架运行并运行模块中的每个文档测试。 如果有任何文档测试运行失败，则合成的单元测试就将失败，并引发一个 [`failureException`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.failureException) 异常显示包含该测试的文件名以及（有时为近似的）行号。 如果文档字符串中的所有示例都被跳过，则合成的单元测试也会被标记为跳过。

​	可选参数 *module* 提供了要测试的模块。 它可以是一个模块对象或一个（可能是带点的）模块名称。 如果没有指定，则使用调用此函数的模块。

​	可选参数 *globs* 是一个包含测试的初始全局变量的字典。 这个字典的一个新副本为每个测试创建。 默认情况下， *globs* 是一个新的空字典。

​	可选参数 *extraglobs* 指定了一组额外的全局变量，这些变量被合并到 *globs* 中。 默认情况下，不使用额外的全局变量。

​	可选参数 *test_finder* 是 [`DocTestFinder`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestFinder) 对象（或一个可替换的对象），用于从模块中提取测试。

​	可选参数 *setUp* 、 *tearDown* 和 *optionflags* 与上述函数 [`DocFileSuite()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocFileSuite) 相同。

​	这个函数使用与 [`testmod()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testmod) 相同的搜索技术。

*在 3.5 版本发生变更:* 如果 *module* 不包含任何文件串，则 [`DocTestSuite()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestSuite) 返回一个空的 [`unittest.TestSuite`](https://docs.python.org/zh-cn/3.13/library/unittest.html#unittest.TestSuite)，而不是触发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

## *exception* doctest.**failureException**

​	当已被 [`DocFileSuite()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocFileSuite) 或 [`DocTestSuite()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestSuite) 转换为单元测试的文档测试失败时，此异常将被引发并显示包含测试的文件名及行号（有时为估计值）。

​	在内部，[`DocTestSuite()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestSuite) 将根据 `doctest.DocTestCase` 实例创建 [`unittest.TestSuite`](https://docs.python.org/zh-cn/3.13/library/unittest.html#unittest.TestSuite)，而 `DocTestCase` 是 [`unittest.TestCase`](https://docs.python.org/zh-cn/3.13/library/unittest.html#unittest.TestCase) 的子类。 `DocTestCase` 没有记入本文档（它属于内部细节），但研究其代码可以回答有关 [`unittest`](https://docs.python.org/zh-cn/3.13/library/unittest.html#module-unittest) 集成的准确细节的问题。

​	类似地，[`DocFileSuite()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocFileSuite) 将根据 `doctest.DocFileCase` 实例创建 [`unittest.TestSuite`](https://docs.python.org/zh-cn/3.13/library/unittest.html#unittest.TestSuite)，而 `DocFileCase` 是 `DocTestCase` 的子类。

​	所以这两种创建 [`unittest.TestSuite`](https://docs.python.org/zh-cn/3.13/library/unittest.html#unittest.TestSuite) 的方式都会运行 `DocTestCase` 的实例。 这一点很重要，因为有一个微妙的原因：当你自己运行 [`doctest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#module-doctest) 函数时，你可以直接控制使用中的 [`doctest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#module-doctest) 选项，具体是通过传递选项旗标给 [`doctest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#module-doctest) 函数。 然而，如果你正在编写一个 [`unittest`](https://docs.python.org/zh-cn/3.13/library/unittest.html#module-unittest) 框架，则最终要由 [`unittest`](https://docs.python.org/zh-cn/3.13/library/unittest.html#module-unittest) 来控制测试的运行时间和方式。 框架作者通常希望控制 [`doctest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#module-doctest) 的报告选项（例如，可能由命令行选项指定），但没有办法通过 [`unittest`](https://docs.python.org/zh-cn/3.13/library/unittest.html#module-unittest) 向 [`doctest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#module-doctest) 测试运行方传递选项。

​	出于这个原因， [`doctest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#module-doctest) 也支持一个概念，即 [`doctest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#module-doctest) 报告特定于 [`unittest`](https://docs.python.org/zh-cn/3.13/library/unittest.html#module-unittest) 支持的标志，通过这个函数：

## doctest.**set_unittest_reportflags**(*flags*)

​	设置要使用的 [`doctest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#module-doctest) 报告标志。

​	参数 *flags* 是选项标志的 [bitwise OR](https://docs.python.org/zh-cn/3.13/reference/expressions.html#bitwise) 。 参见章节 [选项标记](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest-options) 。 只有 "报告标志" 可以被使用。

​	这是模块全局的设置，并会影响 [`unittest`](https://docs.python.org/zh-cn/3.13/library/unittest.html#module-unittest) 模块今后运行的所有文档测试: `DocTestCase` 的 `runTest()` 方法会查看 `DocTestCase` 实例构建时为测试用例指定的选项旗标。 如果没有指定报告旗标志（这是典型和预期的情况），则 `doctest` 的 [`unittest`](https://docs.python.org/zh-cn/3.13/library/unittest.html#module-unittest) 报告旗标志将通过 [按位或运算](https://docs.python.org/zh-cn/3.13/reference/expressions.html#bitwise) 合并选项旗标志中，这样增强的选项标志将传递给为运行文档测试而创建的 [`DocTestRunner`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner) 实例。 如果在构建 `DocTestCase` 实例 时指定了任何报告旗标志，则 `doctest` 的 [`unittest`](https://docs.python.org/zh-cn/3.13/library/unittest.html#module-unittest) 报告旗标将被忽略。

[`unittest`](https://docs.python.org/zh-cn/3.13/library/unittest.html#module-unittest) 报告标志的值在调用该函数之前是有效的，由该函数返回。



## 高级 API

​	基本 API 是一个简单的封装，旨在使 doctest 易于使用。它相当灵活，应该能满足大多数用户的需求；但是，如果你需要对测试进行更精细的控制，或者希望扩展 doctest 的功能，那么你应该使用高级 API 。

​	高级API围绕着两个容器类，用于存储从 doctest 案例中提取的交互式用例:

- [`Example`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.Example): 一个单一的 Python [statement](https://docs.python.org/zh-cn/3.13/glossary.html#term-statement) ，与它的预期输出配对。
- [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest): 一组 [`Example`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.Example)s 的集合，通常从一个文档字符串或文本文件中提取。

​	定义了额外的处理类来寻找、解析和运行，并检查 doctest 的用例。

- [`DocTestFinder`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestFinder) : 查找给定模块中的所有文档串，并使用 [`DocTestParser`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestParser) 从每个包含交互式用例的文档串中创建一个 [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 。
- [`DocTestParser`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestParser) : 从一个字符串（如一个对象的文档串）创建一个 [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 对象。
- [`DocTestRunner`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner) : 执行 [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 中的用例，并使用 [`OutputChecker`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.OutputChecker) 来验证其输出。
- [`OutputChecker`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.OutputChecker) : 将一个测试用例的实际输出与预期输出进行比较，并决定它们是否匹配。

​	这些处理类之间的关系总结在下图中:

```
                            list of:
+------+                   +---------+
|module| --DocTestFinder-> | DocTest | --DocTestRunner-> results
+------+    |        ^     +---------+     |       ^    (printed)
            |        |     | Example |     |       |
            v        |     |   ...   |     v       |
           DocTestParser   | Example |   OutputChecker
                           +---------+
```



### DocTest 对象

## *class* doctest.**DocTest**(*examples*, *globs*, *name*, *filename*, *lineno*, *docstring*)

​	应该在单一命名空间中运行的doctest用例的集合。构造函数参数被用来初始化相同名称的属性。

[`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 定义了以下属性。 它们由构造函数初始化，不应该被直接修改。

## **examples**

​	一个 [`Example`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.Example) 对象的列表，它编码了应该由该测试运行的单个交互式 Python 用例。

## **globs**

​	例子应该运行的命名空间（又称 globals ）。这是一个将名字映射到数值的字典。例子对名字空间的任何改变（比如绑定新的变量）将在测试运行后反映在 [`globs`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest.globs) 中。

## **name**

​	识别 [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 的字符串名称。 通常情况下，这是从测试中提取的对象或文件的名称。

## **filename**

​	这个 [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 被提取的文件名；或者为 `None`，如果文件名未知，或者 [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 没有从文件中提取。

## **lineno**

[`filename`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest.filename) 中的行号，这个 [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 开始的地方，或者行号不可用时为 `None`。 这个行号相对于文件的开头来说是零的。

## **docstring**

​	从测试中提取的字符串，或者如果字符串不可用，或者为 `None` ，如果测试没有从字符串中提取。



### Example 对象

## *class* doctest.**Example**(*source*, *want*, *exc_msg=None*, *lineno=0*, *indent=0*, *options=None*)

​	单个交互式用例，由一个 Python 语句及其预期输出组成。 构造函数参数被用来初始化相同名称的属性。

[`Example`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.Example) 定义了以下属性。 它们由构造函数初始化，不应该被直接修改。

## **source**

​	一个包含该用例源码的字符串。 源码由一个 Python 语句组成，并且总是以换行结束；构造函数在必要时添加一个换行。

## **want**

​	运行这个用例的源码的预期输出（可以是 stdout ，也可以是异常情况下的回溯）。 [`want`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.Example.want) 以一个换行符结束，除非没有预期的输出，在这种情况下它是一个空字符串。 构造函数在必要时添加一个换行。

## **exc_msg**

​	用例产生的异常信息，如果这个例子被期望产生一个异常；或者为 `None` ，如果它不被期望产生一个异常。 这个异常信息与 [`traceback.format_exception_only()`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.format_exception_only) 的返回值进行比较。 [`exc_msg`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.Example.exc_msg) 以换行结束，除非是 `None` 。

## **lineno**

​	包含本例的字符串中的行号，即本例的开始。 这个行号相对于包含字符串的开头来说是以零开始的。

## **indent**

​	用例在包含字符串中的缩进，即在用例的第一个提示前有多少个空格字符。

## **options**

​	一个将选项旗标映射到 `True` 或 `False` 的字典，用于覆盖这个例子的默认选项。 任何不包含在这个字典中的选项旗标都将保持其默认值（由 [`DocTestRunner`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner) 的 [optionflags](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest-options) 指定）。 默认情况下，将不设置任何选项。



### DocTestFinder 对象

## *class* doctest.**DocTestFinder**(*verbose=False*, *parser=DocTestParser()*, *recurse=True*, *exclude_empty=True*)

​	一个处理类，用于从一个给定的对象的 docstring 和其包含的对象的 docstring 中提取与之相关的 [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 。 [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 可以从模块、类、函数、方法、静态方法、类方法和属性中提取。

​	可选的参数 *verbose* 可以用来显示查找器搜索到的对象。 它的默认值是 `False` （无输出）。

​	可选的参数 *parser* 指定了 [`DocTestParser`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestParser) 对象（或一个可替换的对象），用于从文档串中提取 doctest 。

​	如果可选的参数 *recurse* 是 False ，那么 [`DocTestFinder.find()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestFinder.find) 将只检查给定的对象，而不是任何包含的对象。

​	如果可选参数 *exclude_empty* 为 False ，那么 [`DocTestFinder.find()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestFinder.find) 将包括对文档字符串为空的对象的测试。

[`DocTestFinder`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestFinder) 定义了以下方法：

## **find**(*obj[, name][, module][, globs][, extraglobs]*)

​	返回 [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 的列表，该列表由 *obj* 的文档串或其包含的任何对象的文档串定义。

​	可选参数 *name* 指定了对象的名称；这个名称将被用来为返回的 [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 构建名称。 如果没有指定 *name* ，则使用 `obj.__name__` 。

​	可选参数 *module* 是包含给定对象的模块。如果没有指定模块或者是 `None` ，那么测试查找器将试图自动确定正确的模块。 该对象被使用的模块：

- 作为一个默认的命名空间，如果没有指定 *globs* 。
- 为了防止 DocTestFinder 从其他模块导入的对象中提取 DocTest 。 (包含有除 *module* 以外的模块的对象会被忽略)。
- 找到包含该对象的文件名。
- 找到该对象在其文件中的行号。

​	如果 *module* 是 `False` ，将不会试图找到这个模块。 这是不明确的，主要用于测试 doctest 本身：如果 *module* 是 `False` ，或者是 `None` 但不能自动找到，那么所有对象都被认为属于（不存在的）模块，所以所有包含的对象将（递归地）被搜索到 doctest 。

​	每个 [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 的 globals 是由 *globs* 和 *extraglobs* 组合而成的（ *extraglobs* 的绑定覆盖 *globs* 的绑定）。 为每个 [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 创建一个新的 globals 字典的浅层拷贝。如果没有指定 *globs* ，那么它默认为模块的 *__dict__* ，如果指定了或者为 `{}` ，则除外。 如果没有指定 *extraglobs* ，那么它默认为 `{}` 。



### DocTestParser 对象

## *class* doctest.**DocTestParser**

​	一个处理类，用于从一个字符串中提取交互式的用例，并使用它们来创建一个 [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 对象。

[`DocTestParser`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestParser) 定义了以下方法：

## **get_doctest**(*string*, *globs*, *name*, *filename*, *lineno*)

​	从给定的字符串中提取所有的测试用例，并将它们收集到一个 [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 对象中。

*globs* 、 *name* 、 *filename* 和 *lineno* 是新的 [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 对象的属性。 更多信息请参见 [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 的文档。

## **get_examples**(*string*, *name='<string>'*)

​	从给定的字符串中提取所有的测试用例，并以 [`Example`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.Example) 对象列表的形式返回。 行数以 0 为基数。 可选参数 *name* 用于识别这个字符串的名称，只用于错误信息。

## **parse**(*string*, *name='<string>'*)

​	将给定的字符串分成用例和中间的文本，并以 [`Example`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.Example) 和字符串交替的列表形式返回。 [`Example`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.Example) 的行号以 0 为基数。 可选参数 *name* 用于识别这个字符串的名称，只用于错误信息。

### TestResults 对象

## *class* doctest.**TestResults**(*failed*, *attempted*)

## **failed**

​	失败的测试数量。

## **attempted**

​	执行的测试数量。

## **skipped**

​	跳过的测试数量。

*Added in version 3.13.*



### DocTestRunner 对象

## *class* doctest.**DocTestRunner**(*checker=None*, *verbose=None*, *optionflags=0*)

​	一个处理类，用于执行和验证 [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 中的交互式用例。

​	预期输出和实际输出之间的比较是由一个 [`OutputChecker`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.OutputChecker) 完成的。 这种比较可以用一些选项标志来定制；更多信息请看 [选项标记](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest-options) 部分。 如果选项标志不够，那么也可以通过向构造函数传递 [`OutputChecker`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.OutputChecker) 的子类来定制比较。

​	测试运行器的显示输出可以通过两种方式来控制。 首先，一个输出函数可以被传递给 [`run()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner.run)；这个函数将被调用并传入要显示的字符串。 默认使用 `sys.stdout.write`。 如果捕获输出是不够的，那么也可以通过子类化 DocTestRunner，并重写 [`report_start()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner.report_start), [`report_success()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner.report_success), [`report_unexpected_exception()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner.report_unexpected_exception) 和 [`report_failure()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner.report_failure) 等方法来定制显示输出。

​	可选的关键字参数 *checker* 指定了 [`OutputChecker`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.OutputChecker) 对象（或其相似替换），它应该被用来比较预期输出和 doctest 用例的实际输出。

​	可选的关键字参数 *verbose* 控制 [`DocTestRunner`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner) 的详细程度。 如果 *verbose* 是 `True` ，那么每个用例的信息都会被打印出来，当它正在运行时。 如果 *verbose* 是 `False` ，则只打印失败的信息。 当 *verbose* 没有指定，或者为 `None` ，如果使用了命令行开关 `-v` ，则使用verbose输出。

​	可选的关键字参数 *optionflags* 可以用来控制测试运行器如何比较预期输出和实际输出，以及如何显示失败。更多信息，请参见章节 [选项标记](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest-options) 。

​	测试运行将累积统计数据。 已尝试、已失败和已跳过的示例的聚合计数也可通过 [`tries`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner.tries), [`failures`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner.failures) 和 [`skips`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner.skips) 属性来获取。 [`run()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner.run) 和 [`summarize()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner.summarize) 方法将返回一个 [`TestResults`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.TestResults) 实例。

[`DocTestRunner`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner) 定义了以下方法：

## **report_start**(*out*, *test*, *example*)

​	报告测试运行器即将处理给定的用例。提供这个方法是为了让 [`DocTestRunner`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner) 的子类能够定制他们的输出；它不应该被直接调用。

*example* 是即将被处理的用。 *test* 是 *包含用例* 的测试。 *out* 是传递给 [`DocTestRunner.run()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner.run) 的输出函数。

## **report_success**(*out*, *test*, *example*, *got*)

​	报告给定的用例运行成功。 提供这个方法是为了让 [`DocTestRunner`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner) 的子类能够定制他们的输出；它不应该被直接调用。

*example* 是即将被处理的用例。 *got* 是这个例子的实际输出。 *test* 是包含 *example* 的测试。 *out* 是传递给 [`DocTestRunner.run()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner.run) 的输出函数。

## **report_failure**(*out*, *test*, *example*, *got*)

​	报告给定的用例运行失败。 提供这个方法是为了让 [`DocTestRunner`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner) 的子类能够定制他们的输出；它不应该被直接调用。

*example* 是即将被处理的用例。 *got* 是这个例子的实际输出。 *test* 是包含 *example* 的测试。 *out* 是传递给 [`DocTestRunner.run()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner.run) 的输出函数。

## **report_unexpected_exception**(*out*, *test*, *example*, *exc_info*)

​	报告给定的用例触发了一个异常。 提供这个方法是为了让 [`DocTestRunner`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner) 的子类能够定制他们的输出；它不应该被直接调用。

*example* 是将要被处理的用例。 *exc_info* 是一个元组，包含关于异常的信息（如由 [`sys.exc_info()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.exc_info) 返回）。 *test* 是包含 *example* 的测试。 *out* 是传递给 [`DocTestRunner.run()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner.run) 的输出函数。

## **run**(*test*, *compileflags=None*, *out=None*, *clear_globs=True*)

​	运行 *test* (一个 [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 对象) 中的示例，并使用写入函数 *out* 显示结果。 返回一个 [`TestResults`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.TestResults) 实例。

​	这些用例都是在命名空间 `test.globs` 中运行的。 如果 *clear_globs* 为 True （默认），那么这个命名空间将在测试运行后被清除，以帮助进行垃圾回收。如果你想在测试完成后检查命名空间，那么使用 *clear_globs=False* 。

*compileflags* 给出了 Python 编译器在运行例子时应该使用的标志集。如果没有指定，那么它将默认为适用于 *globs* 的 future-import 标志集。

​	每个例子的输出都使用The output of each example is checked using the [`DocTestRunner`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner) 的输出检查器进行检查，并且结果将由 `DocTestRunner.report_*()` 方法来格式化。

## **summarize**(*verbose=None*)

​	打印这个 DocTestRunner 运行过的所有测试用例的概要，并返回一个 [`TestResults`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.TestResults) 实例。

​	可选的 *verbose* 参数控制摘要的详细程度。 如果没有指定 verbose ，那么将使用 [`DocTestRunner`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner) 的 verbose 。

[`DocTestParser`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestParser) 具有下列属性：

## **tries**

​	尝试的示例数量。

## **failures**

​	失败的示例数量。

## **skips**

​	跳过的示例数量。

*Added in version 3.13.*



### OutputChecker 对象

## *class* doctest.**OutputChecker**

​	一个用于检查测试用例的实际输出是否与预期输出相匹配的类。 [`OutputChecker`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.OutputChecker) 定义了两个方法： [`check_output()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.OutputChecker.check_output) ，比较给定的一对输出，如果它们匹配则返回 `True` ； [`output_difference()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.OutputChecker.output_difference) ，返回一个描述两个输出之间差异的字符串。

[`OutputChecker`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.OutputChecker) 定义了以下方法：

## **check_output**(*want*, *got*, *optionflags*)

​	如果一个用例的实际输出（ *got* ）与预期输出（ *want* ）匹配，则返回 `True` 。 如果这些字符串是相同的，总是被认为是匹配的；但是根据测试运行器使用的选项标志，也可能有几种非精确的匹配类型。 参见章节 [选项标记](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest-options) 了解更多关于选项标志的信息。

## **output_difference**(*example*, *got*, *optionflags*)

​	返回一个字符串，描述给定用例（ *example* ）的预期输出和实际输出（ *got* ）之间的差异。 *optionflags* 是用于比较 *want* 和 *got* 的选项标志集。



## 调试

​	Doctest 提供了几种调试 doctest 用例的机制：

- 有几个函数将测试转换为可执行的 Python 程序，这些程序可以在 Python 调试器， [`pdb`](https://docs.python.org/zh-cn/3.13/library/pdb.html#module-pdb) 下运行。

- [`DebugRunner`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DebugRunner) 类是 [`DocTestRunner`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner) 的一个子类，它为第一个失败的用例触发一个异常，包含关于这个用例的信息。这些信息可以用来对这个用例进行事后调试。

- 由 [`DocTestSuite()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestSuite) 生成的 [`unittest`](https://docs.python.org/zh-cn/3.13/library/unittest.html#module-unittest) 用例支持由 [`unittest.TestCase`](https://docs.python.org/zh-cn/3.13/library/unittest.html#unittest.TestCase) 定义的 [`debug()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.debug) 方法，。

- 你可以在 doctest 的用例中加入对 [`pdb.set_trace()`](https://docs.python.org/zh-cn/3.13/library/pdb.html#pdb.set_trace) 的调用，当这一行被执行时，你会进入 Python 调试器。 然后你可以检查变量的当前值，等等。 例如，假设 `a.py` 只包含这个模块 docstring

  ```
  """
  >>> def f(x):
  ...     g(x*2)
  >>> def g(x):
  ...     print(x+3)
  ...     import pdb; pdb.set_trace()
  >>> f(3)
  9
  """
  ```

  那么一个交互式Python会话可能是这样的:

  

  ```
  >>> import a, doctest
  >>> doctest.testmod(a)
  --Return--
  > <doctest a[1]>(3)g()->None
  -> import pdb; pdb.set_trace()
  (Pdb) list
    1     def g(x):
    2         print(x+3)
    3  ->     import pdb; pdb.set_trace()
  [EOF]
  (Pdb) p x
  6
  (Pdb) step
  --Return--
  > <doctest a[0]>(2)f()->None
  -> g(x*2)
  (Pdb) list
    1     def f(x):
    2  ->     g(x*2)
  [EOF]
  (Pdb) p x
  3
  (Pdb) step
  --Return--
  > <doctest a[2]>(1)?()->None
  -> f(3)
  (Pdb) cont
  (0, 3)
  >>>
  ```

​	将测试转换为 Python 代码的函数，并可能在调试器下运行合成的代码:

## doctest.**script_from_examples**(*s*)

​	将带有用例的文本转换为脚本。

​	参数 *s* 是一个包含测试用例的字符串。 该字符串被转换为 Python 脚本，其中 *s* 中的 doctest 用例被转换为常规代码，其他的都被转换为 Python 注释。 生成的脚本将以字符串的形式返回。例如，

```
import doctest
print(doctest.script_from_examples(r"""
    Set x and y to 1 and 2.
    >>> x, y = 1, 2

    Print their sum:
    >>> print(x+y)
    3
"""))
```

​	显示:

```
# Set x and y to 1 and 2.
x, y = 1, 2
#
# Print their sum:
print(x+y)
# Expected:
## 3
```

​	这个函数在内部被其他函数使用（见下文），但当你想把一个交互式 Python 会话转化为 Python 脚本时，也会很有用。

## doctest.**testsource**(*module*, *name*)

​	将一个对象的 doctest 转换为一个脚本。

​	参数 *module* 是一个模块对象，或是一个带点号的模块名称，其中包含文档测试需要的对象。 参数 *name* 是文档测试需要的对象（在模块中）的名称。 结果是一个字符串，包含该对象的文档字符串转换成的 Python 脚本，如上面 [`script_from_examples()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.script_from_examples) 所描述的。 举例来说，如果模块 `a.py` 包含一个最高层级的函数 `f()`，那么

```
import a, doctest
print(doctest.testsource(a, "a.f"))
```

​	打印函数 `f()` 的文档字符串的脚本版本，将文档测试转换为代码，而将其余内容放在注释中。

## doctest.**debug**(*module*, *name*, *pm=False*)

​	对一个对象的 doctest 进行调试。

*module* 和 *name* 参数与上面函数 [`testsource()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testsource) 的参数相同。 被命名对象的文本串的合成 Python 脚本被写入一个临时文件，然后该文件在 Python 调试器 [`pdb`](https://docs.python.org/zh-cn/3.13/library/pdb.html#module-pdb) 的控制下运行。

`module.__dict__` 的一个浅层拷贝被用于本地和全局的执行环境。

​	可选参数 *pm* 控制是否使用事后调试。 如果 *pm* 为 True ，则直接运行脚本文件，只有当脚本通过引发一个未处理的异常而终止时，调试器才会介入。如果是这样，就会通过 [`pdb.post_mortem()`](https://docs.python.org/zh-cn/3.13/library/pdb.html#pdb.post_mortem) 调用事后调试，并传递未处理异常的跟踪对象。如果没有指定 *pm* ，或者是 False ，脚本将从一开始就在调试器下运行，通过传递一个适当的 [`exec()`](https://docs.python.org/zh-cn/3.13/library/functions.html#exec) 调用给 [`pdb.run()`](https://docs.python.org/zh-cn/3.13/library/pdb.html#pdb.run) 。

## doctest.**debug_src**(*src*, *pm=False*, *globs=None*)

​	在一个字符串中调试 doctest 。

​	这就像上面的函数 [`debug()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.debug) ，只是通过 *src* 参数，直接指定一个包含测试用例的字符串。

​	可选参数 *pm* 的含义与上述函数 [`debug()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.debug) 的含义相同。

​	可选的参数 *globs* 给出了一个字典，作为本地和全局的执行环境。 如果没有指定，或者为 `None` ，则使用一个空的字典。如果指定，则使用字典的浅层拷贝。

[`DebugRunner`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DebugRunner) 类，以及它可能触发的特殊异常，是测试框架作者最感兴趣的，在此仅作简要介绍。请看源代码，特别是 [`DebugRunner`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DebugRunner) 的文档串（这是一个测试！）以了解更多细节。

## *class* doctest.**DebugRunner**(*checker=None*, *verbose=None*, *optionflags=0*)

[`DocTestRunner`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner) 的一个子类，一旦遇到失败，就会触发一个异常。 如果一个意外的异常发生，就会引发一个 [`UnexpectedException`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.UnexpectedException) 异常，包含测试、用例和原始异常。 如果输出不匹配，那么就会引发一个 [`DocTestFailure`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestFailure) 异常，包含测试、用例和实际输出。

​	关于构造函数参数和方法的信息，请参见 [`DocTestRunner`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner) 部分的文档 [高级 API](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest-advanced-api) 。

[`DebugRunner`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DebugRunner) 实例可能会触发两种异常。

## *exception* doctest.**DocTestFailure**(*test*, *example*, *got*)

[`DocTestRunner`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner) 触发的异常，表示一个 doctest 用例的实际输出与预期输出不一致。构造函数参数被用来初始化相同名称的属性。

[`DocTestFailure`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestFailure) 定义了以下属性:

## DocTestFailure.**test**

​	当该用例失败时正在运行的 [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 对象。

## DocTestFailure.**example**

​	失败的 [`Example`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.Example) 。

## DocTestFailure.**got**

​	用例的实际输出。

## *exception* doctest.**UnexpectedException**(*test*, *example*, *exc_info*)

​	一个由 [`DocTestRunner`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTestRunner) 触发的异常，以提示一个 doctest 用例引发了一个意外的异常。 构造函数参数被用来初始化相同名称的属性。

[`UnexpectedException`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.UnexpectedException) 定义了以下属性:

## UnexpectedException.**test**

​	当该用例失败时正在运行的 [`DocTest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocTest) 对象。

## UnexpectedException.**example**

​	失败的 [`Example`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.Example) 。

## UnexpectedException.**exc_info**

​	一个包含意外异常信息的元组，由 `sys.ex_info()` 返回。



## 肥皂盒

​	正如介绍中提到的， [`doctest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#module-doctest) 已经发展到有三个主要用途:

1. 检查 docstring 中的用例。
2. 回归测试。
3. 可执行的文档/文字测试。

​	这些用途有不同的要求，区分它们是很重要的。特别是，用晦涩难懂的测试用例来填充你的文档字符串会使文档变得很糟糕。

​	在编写文档串时，要谨慎地选择文档串的用例。这是一门需要学习的艺术——一开始可能并不自然。用例应该为文档增加真正的价值。 一个好的用例往往可以抵得上许多文字。如果用心去做，这些用例对你的用户来说是非常有价值的，而且随着时间的推移和事情的变化，收集这些用例所花费的时间也会得到很多倍的回报。 我仍然惊讶于我的 [`doctest`](https://docs.python.org/zh-cn/3.13/library/doctest.html#module-doctest) 用例在一个“无害”的变化后停止工作。

​	Doctest 也是回归测试的一个很好的工具，特别是如果你不吝啬解释的文字。 通过交错的文本和用例，可以更容易地跟踪真正的测试，以及为什么。当测试失败时，好的文本可以使你更容易弄清问题所在，以及如何解决。 的确，你可以在基于代码的测试中写大量的注释，但很少有程序员这样做。许多人发现，使用 doctest 方法反而能使测试更加清晰。 也许这只是因为 doctest 使写散文比写代码容易一些，而在代码中写注释则有点困难。 我认为它比这更深入：当写一个基于 doctest 的测试时，自然的态度是你想解释你的软件的细微之处，并用例子来说明它们。这反过来又自然地导致了测试文件从最简单的功能开始，然后逻辑地发展到复杂和边缘案例。 一个连贯的叙述是结果，而不是一个孤立的函数集合，似乎是随机的测试孤立的功能位。 这是一种不同的态度，产生不同的结果，模糊了测试和解释之间的区别。

​	回归测试最好限制在专用对象或文件中。 有几种组织测试的选择：

- 编写包含测试案例的文本文件作为交互式例子，并使用 [`testfile()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.testfile) 或 [`DocFileSuite()`](https://docs.python.org/zh-cn/3.13/library/doctest.html#doctest.DocFileSuite) 来测试这些文件。 建议这样做，尽管对于新的项目来说是最容易做到的，从一开始就设计成使用 doctest 。
- 定义命名为 `_regrtest_topic` 的函数，由单个文档串组成，包含命名主题的测试用例。 这些函数可以包含在与模块相同的文件中，或分离出来成为一个单独的测试文件。
- 定义一个从回归测试主题到包含测试用例的文档串的 `__test__` 字典映射。

​	当你把你的测试放在一个模块中时，这个模块本身就可以成为测试运行器。 当一个测试失败时，你可以安排你的测试运行器只重新运行失败的测试，同时调试问题。 下面是这样一个测试运行器的最小例子:

```
if __name__ == '__main__':
    import doctest
    flags = doctest.REPORT_NDIFF|doctest.FAIL_FAST
    if len(sys.argv) > 1:
        name = sys.argv[1]
        if name in globals():
            obj = globals()[name]
        else:
            obj = __test__[name]
        doctest.run_docstring_examples(obj, globals(), name=name,
                                       optionflags=flags)
    else:
        fail, total = doctest.testmod(optionflags=flags)
        print(f"{fail} failures out of {total} tests")
```

​	备注

[[1](https://docs.python.org/zh-cn/3.13/library/doctest.html#id1)]

​	不支持同时包含预期输出和异常的用例。试图猜测一个在哪里结束，另一个在哪里开始，太容易出错了，而且这也会使测试变得混乱。
