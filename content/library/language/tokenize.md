+++
title = "tokenize --- Python 源代码的分词器"
date = 2024-11-15T21:23:55+08:00
weight = 40
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/tokenize.html](https://docs.python.org/zh-cn/3.13/library/tokenize.html)
>
> 收录该文档的时间：`2024-11-15T21:23:55+08:00`

# `tokenize` --- Python 源代码的分词器

**源码：** [Lib/tokenize.py](https://github.com/python/cpython/tree/3.13/Lib/tokenize.py)

------

[`tokenize`]({{< ref "/library/language/tokenize#module-tokenize" >}}) 模块为 Python 源代码提供了一个词法扫描器，用 Python 实现。该模块中的扫描器也将注释作为标记返回，这使得它对于实现“漂亮的输出器”非常有用，包括用于屏幕显示的着色器。

​	为了简化标记流的处理，所有的 [运算符]({{< ref "/reference/lexical_analysis#operators" >}}) 和 [定界符]({{< ref "/reference/lexical_analysis#delimiters" >}}) 以及 [`Ellipsis`]({{< ref "/library/constants#Ellipsis" >}}) 返回时都会打上通用的 [`OP`]({{< ref "/library/language/token#token.OP" >}}) 标记。 可以通过 [`tokenize.tokenize()`]({{< ref "/library/language/tokenize#tokenize.tokenize" >}}) 返回的 [named tuple]({{< ref "/glossary/idx#term-named-tuple" >}}) 对象的 `exact_type` 属性来获得确切的标记类型。

​	警告

 

​	请注意本模块中的函数被设计为仅能解析符合语法的 Python 代码（当使用 [`ast.parse()`]({{< ref "/library/language/ast#ast.parse" >}}) 解析代码时不会引发异常）。 在提供无效的 Python 代码时本模块中函数的行为是 **未定义** 的并可能在任何时候发生改变。

## 对输入进行解析标记

​	主要的入口是一个 [generator]({{< ref "/glossary/idx#term-generator" >}}):

## tokenize.**tokenize**(*readline*)

​	生成器 [`tokenize()`]({{< ref "/library/language/tokenize#tokenize.tokenize" >}}) 需要一个 *readline* 参数，这个参数必须是一个可调用对象，且能提供与文件对象的 [`io.IOBase.readline()`]({{< ref "/library/allos/io#io.IOBase.readline" >}}) 方法相同的接口。每次调用这个函数都要 返回字节类型输入的一行数据。

​	生成器产生 5 个具有这些成员的元组：令牌类型；令牌字符串；指定令牌在源中开始的行和列的 2 元组 `(srow, scol)` ；指定令牌在源中结束的行和列的 2 元组 `(erow, ecol)` ；以及发现令牌的行。所传递的行（最后一个元组项）是 *实际的* 行。 5 个元组以 [named tuple]({{< ref "/glossary/idx#term-named-tuple" >}}) 的形式返回，字段名是： `type string start end line` 。

​	返回的 [named tuple]({{< ref "/glossary/idx#term-named-tuple" >}}) 有一个额外的属性，名为 `exact_type` ，包含了 [`OP`]({{< ref "/library/language/token#token.OP" >}}) 标记的确切操作符类型。 对于所有其他标记类型， `exact_type` 等于命名元组的 `type` 字段。

> 在 3.1 版本发生变更: 增加了对 named tuple 的支持。

> 在 3.3 版本发生变更: 添加了对 `exact_type` 的支持。

​	根据 [**PEP 263**](https://peps.python.org/pep-0263/) ，[`tokenize()`]({{< ref "/library/language/tokenize#tokenize.tokenize" >}}) 通过寻找 UTF-8 BOM 或编码 cookie 来确定文件的源编码。

## tokenize.**generate_tokens**(*readline*)

​	对读取 unicode 字符串而不是字节的源进行标记。

​	和 [`tokenize()`]({{< ref "/library/language/tokenize#tokenize.tokenize" >}}) 一样， *readline* 参数是一个返回单行输入的可调用参数。然而， [`generate_tokens()`]({{< ref "/library/language/tokenize#tokenize.generate_tokens" >}}) 希望 *readline* 返回一个 str 对象而不是字节。

​	其结果是一个产生具名元组的的迭代器，与 [`tokenize()`]({{< ref "/library/language/tokenize#tokenize.tokenize" >}}) 完全一样。 它不会产生 [`ENCODING`]({{< ref "/library/language/token#token.ENCODING" >}}) 标记。

​	所有来自 [`token`]({{< ref "/library/language/token#module-token" >}}) 模块的常量也可从 [`tokenize`]({{< ref "/library/language/tokenize#module-tokenize" >}}) 导出。

​	提供了另一个函数来逆转标记化过程。这对于创建对脚本进行标记、修改标记流并写回修改后脚本的工具很有用。

## tokenize.**untokenize**(*iterable*)

​	将令牌转换为 Python 源代码。 *iterable* 必须返回至少有两个元素的序列，即令牌类型和令牌字符串。任何额外的序列元素都会被忽略。

​	重构的脚本以单个字符串的形式返回。 结果被保证为标记回与输入相匹配，因此转换是无损的，并保证来回操作。 该保证只适用于标记类型和标记字符串，因为标记之间的间距（列位置）可能会改变。

​	它返回字节，使用 [`ENCODING`]({{< ref "/library/language/token#token.ENCODING" >}}) 标记进行编码，这是由 [`tokenize()`]({{< ref "/library/language/tokenize#tokenize.tokenize" >}}) 输出的第一个标记序列。如果输入中没有编码令牌，它将返回一个字符串。

[`tokenize()`]({{< ref "/library/language/tokenize#tokenize.tokenize" >}}) 需要检测它所标记源文件的编码。它用来做这件事的函数是可用的：

## tokenize.**detect_encoding**(*readline*)

[`detect_encoding()`]({{< ref "/library/language/tokenize#tokenize.detect_encoding" >}}) 函数用于检测解码 Python 源文件时应使用的编码。它需要一个参数， readline ，与 [`tokenize()`]({{< ref "/library/language/tokenize#tokenize.tokenize" >}}) 生成器的使用方式相同。

​	它最多调用 readline 两次，并返回所使用的编码（作为一个字符串）和它所读入的任何行（不是从字节解码的）的 list 。

​	它从 UTF-8 BOM 或编码 cookie 的存在中检测编码格式，如 [**PEP 263**](https://peps.python.org/pep-0263/) 所指明的。 如果 BOM 和 cookie 都存在，但不一致，将会引发 [`SyntaxError`]({{< ref "/library/exceptions#SyntaxError" >}})。 请注意，如果找到 BOM ，将返回 `'utf-8-sig'` 作为编码格式。

​	如果没有指定编码，那么将返回默认的 `'utf-8'` 编码.

​	使用 [`open()`]({{< ref "/library/language/tokenize#tokenize.open" >}}) 来打开 Python 源文件：它使用 [`detect_encoding()`]({{< ref "/library/language/tokenize#tokenize.detect_encoding" >}}) 来检测文件编码。

## tokenize.**open**(*filename*)

​	使用由 [`detect_encoding()`]({{< ref "/library/language/tokenize#tokenize.detect_encoding" >}}) 检测到的编码，以只读模式打开一个文件。

> Added in version 3.2.
>

## *exception* tokenize.**TokenError**

​	当文件中任何地方没有完成 docstring 或可能被分割成几行的表达式时触发，例如:

```
"""Beginning of
docstring
```

​	或者:

```
[1,
 2,
 3
```



## 命令行用法

> Added in version 3.3.
>

[`tokenize`]({{< ref "/library/language/tokenize#module-tokenize" >}}) 模块可以作为一个脚本从命令行执行。这很简单:

```
python -m tokenize [-e] [filename.py]
```

​	可以接受以下选项：

## **-h**, `--help`

​	显示此帮助信息并退出

## **-e**, `--exact`

​	使用确切的类型显示令牌名称

​	如果 `filename.py` 被指定，其内容会被标记到 stdout 。否则，标记化将在 stdin 上执行。

## 例子

​	脚本改写器的例子，它将 float 文本转换为 Decimal 对象:

```
from tokenize import tokenize, untokenize, NUMBER, STRING, NAME, OP
from io import BytesIO

def decistmt(s):
    """在一个语句字符串中用 Decimal 替代浮点数。

    >>> from decimal import Decimal
    >>> s = 'print(+21.3e-5*-.1234/81.7)'
    >>> decistmt(s)
    "print (+Decimal ('21.3e-5')*-Decimal ('.1234')/Decimal ('81.7'))"

    指数的格式继承自平台的 C 库。
    已知用例有 "e-007" (Windows) 和 "e-07" (非 Windows)。
    由于我们只显示 12 个数位，且第 13 位的值不到 5，因此
    输出的其余部分应当是依赖于具体平台的。

    >>> exec(s)  #doctest: +ELLIPSIS
    -3.21716034272e-0...7

    使用 Decimal 进行计算的输出应当在所有平台上保持一致。

    >>> exec(decistmt(s))
    -3.217160342717258261933904529E-7
    """
    result = []
    g = tokenize(BytesIO(s.encode('utf-8')).readline)  # tokenize the string
    for toknum, tokval, _, _, _ in g:
        if toknum == NUMBER and '.' in tokval:  # 替换 NUMBER 形符
            result.extend([
                (NAME, 'Decimal'),
                (OP, '('),
                (STRING, repr(tokval)),
                (OP, ')')
            ])
        else:
            result.append((toknum, tokval))
    return untokenize(result).decode('utf-8')
```

​	从命令行进行标记化的例子。 脚本:

```
def say_hello():
    print("Hello, World!")

say_hello()
```

​	将被标记为以下输出，其中第一列是发现标记的行 / 列坐标范围，第二列是标记的名称，最后一列是标记的值（如果有）。

```
$ python -m tokenize hello.py
0,0-0,0:            ENCODING       'utf-8'
1,0-1,3:            NAME           'def'
1,4-1,13:           NAME           'say_hello'
1,13-1,14:          OP             '('
1,14-1,15:          OP             ')'
1,15-1,16:          OP             ':'
1,16-1,17:          NEWLINE        '\n'
2,0-2,4:            INDENT         '    '
2,4-2,9:            NAME           'print'
2,9-2,10:           OP             '('
2,10-2,25:          STRING         '"Hello, World!"'
2,25-2,26:          OP             ')'
2,26-2,27:          NEWLINE        '\n'
3,0-3,1:            NL             '\n'
4,0-4,0:            DEDENT         ''
4,0-4,9:            NAME           'say_hello'
4,9-4,10:           OP             '('
4,10-4,11:          OP             ')'
4,11-4,12:          NEWLINE        '\n'
5,0-5,0:            ENDMARKER      ''
```

​	可以使用 [`-e`]({{< ref "/library/language/tokenize#cmdoption-tokenize-e" >}}) 选项来显示确切的标记类型名称。

```
$ python -m tokenize -e hello.py
0,0-0,0:            ENCODING       'utf-8'
1,0-1,3:            NAME           'def'
1,4-1,13:           NAME           'say_hello'
1,13-1,14:          LPAR           '('
1,14-1,15:          RPAR           ')'
1,15-1,16:          COLON          ':'
1,16-1,17:          NEWLINE        '\n'
2,0-2,4:            INDENT         '    '
2,4-2,9:            NAME           'print'
2,9-2,10:           LPAR           '('
2,10-2,25:          STRING         '"Hello, World!"'
2,25-2,26:          RPAR           ')'
2,26-2,27:          NEWLINE        '\n'
3,0-3,1:            NL             '\n'
4,0-4,0:            DEDENT         ''
4,0-4,9:            NAME           'say_hello'
4,9-4,10:           LPAR           '('
4,10-4,11:          RPAR           ')'
4,11-4,12:          NEWLINE        '\n'
5,0-5,0:            ENDMARKER      ''
```

​	以编程方式对文件进行标记的例子，用 [`generate_tokens()`]({{< ref "/library/language/tokenize#tokenize.generate_tokens" >}}) 读取 unicode 字符串而不是字节:

```
import tokenize

with tokenize.open('hello.py') as f:
    tokens = tokenize.generate_tokens(f.readline)
    for token in tokens:
        print(token)
```

​	或者通过 [`tokenize()`]({{< ref "/library/language/tokenize#tokenize.tokenize" >}}) 直接读取字节数据:

```
import tokenize

with open('hello.py', 'rb') as f:
    tokens = tokenize.tokenize(f.readline)
    for token in tokens:
        print(token)
```
