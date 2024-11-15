+++
title = "argparse --- 用于命令行选项、参数和子命令的解析器"
date = 2024-11-15T12:09:25+08:00
weight = 30
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/argparse.html](https://docs.python.org/zh-cn/3.13/library/argparse.html)
>
> 收录该文档的时间：`2024-11-15T12:09:25+08:00`

# `argparse` --- 用于命令行选项、参数和子命令的解析器

*Added in version 3.2.*

**源代码：** [Lib/argparse.py](https://github.com/python/cpython/tree/3.13/Lib/argparse.py)

------

​	教程

​	此页面包含该 API 的参考信息。有关 Python 命令行解析更细致的介绍，请参阅 [argparse 教程](https://docs.python.org/zh-cn/3.13/howto/argparse.html#argparse-tutorial)。

`argparse` 模块让编写用户友好的命令行接口变得容易。 程序定义它需要哪些参数，`argparse` 将会知道如何从 [`sys.argv`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.argv) 解析它们。 `argparse` 模块还能自动生成帮助和用法消息文本。 该模块还会在用户向程序传入无效参数时发出错误提示。

`argparse` 模块对命令行界面的支持是围绕 [`argparse.ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 实例建立的。 它是一个用于参数规格说明的容器并包含应用于解析器的一组选项:

```
parser = argparse.ArgumentParser(
                    prog='ProgramName',
                    description='What the program does',
                    epilog='Text at the bottom of help')
```

[`ArgumentParser.add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument) 方法将单个参数规格说明关联到解析器。 它支持位置参数，接受各种值的选项，以及各种启用/禁用旗标:

```
parser.add_argument('filename')           # 位置参数
parser.add_argument('-c', '--count')      # 接受一个值的选项
parser.add_argument('-v', '--verbose',
                    action='store_true')  # 启用/禁用旗标
```

[`ArgumentParser.parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args) 方法运行解析器并将提取的数据放入 [`argparse.Namespace`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.Namespace) 对象:

```
args = parser.parse_args()
print(args.filename, args.count, args.verbose)
```

​	备注

 

​	If you're looking for a guide about how to upgrade [`optparse`](https://docs.python.org/zh-cn/3.13/library/optparse.html#module-optparse) code to `argparse`, see [Upgrading Optparse Code](https://docs.python.org/zh-cn/3.13/howto/argparse-optparse.html#upgrading-optparse-code).

## ArgumentParser 对象

## *class* argparse.**ArgumentParser**(*prog=None*, *usage=None*, *description=None*, *epilog=None*, *parents=[]*, *formatter_class=argparse.HelpFormatter*, *prefix_chars='-'*, *fromfile_prefix_chars=None*, *argument_default=None*, *conflict_handler='error'*, *add_help=True*, *allow_abbrev=True*, *exit_on_error=True*)

​	创建一个新的 [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 对象。所有的参数都应当作为关键字参数传入。每个参数在下面都有它更详细的描述，但简而言之，它们是：

- [prog](https://docs.python.org/zh-cn/3.13/library/argparse.html#prog) - 程序的名称 (默认值: `os.path.basename(sys.argv[0])`)
- [usage](https://docs.python.org/zh-cn/3.13/library/argparse.html#usage) - 描述程序用途的字符串（默认值：从添加到解析器的参数生成）
- [description](https://docs.python.org/zh-cn/3.13/library/argparse.html#description) - 要在参数帮助信息之前显示的文本（默认：无文本）
- [epilog](https://docs.python.org/zh-cn/3.13/library/argparse.html#epilog) - 要在参数帮助信息之后显示的文本（默认：无文本）
- [parents](https://docs.python.org/zh-cn/3.13/library/argparse.html#parents) - 一个 [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 对象的列表，它们的参数也应包含在内
- [formatter_class](https://docs.python.org/zh-cn/3.13/library/argparse.html#formatter-class) - 用于自定义帮助文档输出格式的类
- [prefix_chars](https://docs.python.org/zh-cn/3.13/library/argparse.html#prefix-chars) - 可选参数的前缀字符集合（默认值： '-'）
- [fromfile_prefix_chars](https://docs.python.org/zh-cn/3.13/library/argparse.html#fromfile-prefix-chars) - 当需要从文件中读取其他参数时，用于标识文件名的前缀字符集合（默认值： `None`）
- [argument_default](https://docs.python.org/zh-cn/3.13/library/argparse.html#argument-default) - 参数的全局默认值（默认值： `None`）
- [conflict_handler](https://docs.python.org/zh-cn/3.13/library/argparse.html#conflict-handler) - 解决冲突选项的策略（通常是不必要的）
- [add_help](https://docs.python.org/zh-cn/3.13/library/argparse.html#add-help) - 为解析器添加一个 `-h/--help` 选项（默认值： `True`）
- [allow_abbrev](https://docs.python.org/zh-cn/3.13/library/argparse.html#allow-abbrev) - 如果缩写是无歧义的，则允许缩写长选项 （默认值：`True`）
- [exit_on_error](https://docs.python.org/zh-cn/3.13/library/argparse.html#exit-on-error) - Determines whether or not `ArgumentParser` exits with error info when an error occurs. (default: `True`)

*在 3.5 版本发生变更:* 增加了 *allow_abbrev* 参数。

*在 3.8 版本发生变更:* 在之前的版本中，*allow_abbrev* 还会禁用短旗标分组，例如 `-vv` 表示为 `-v -v`。

*在 3.9 版本发生变更:* 添加了 *exit_on_error* 形参。

​	以下部分描述这些参数如何使用。



### prog

​	By default, [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) calculates the name of the program to display in help messages depending on the way the Python interpreter was run:

- The [`base name`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.basename) of `sys.argv[0]` if a file was passed as argument.
- The Python interpreter name followed by `sys.argv[0]` if a directory or a zipfile was passed as argument.
- The Python interpreter name followed by `-m` followed by the module or package name if the [`-m`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-m) option was used.

​	This default is almost always desirable because it will make the help messages match the string that was used to invoke the program on the command line. However, to change this default behavior, another value can be supplied using the `prog=` argument to [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser):

\>>>

```
>>> parser = argparse.ArgumentParser(prog='myprogram')
>>> parser.print_help()
usage: myprogram [-h]

options:
 -h, --help  show this help message and exit
```

​	需要注意的是，无论是从 `sys.argv[0]` 或是从 `prog=` 参数确定的程序名称，都可以在帮助消息里通过 `%(prog)s` 格式说明符来引用。

\>>>

```
>>> parser = argparse.ArgumentParser(prog='myprogram')
>>> parser.add_argument('--foo', help='foo of the %(prog)s program')
>>> parser.print_help()
usage: myprogram [-h] [--foo FOO]

options:
 -h, --help  show this help message and exit
 --foo FOO   foo of the myprogram program
```

### usage

​	By default, [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) calculates the usage message from the arguments it contains. The default message can be overridden with the `usage=` keyword argument:

\>>>

```
>>> parser = argparse.ArgumentParser(prog='PROG', usage='%(prog)s [options]')
>>> parser.add_argument('--foo', nargs='?', help='foo help')
>>> parser.add_argument('bar', nargs='+', help='bar help')
>>> parser.print_help()
usage: PROG [options]

positional arguments:
 bar          bar help

options:
 -h, --help   show this help message and exit
 --foo [FOO]  foo help
```

​	在用法消息中可以使用 `%(prog)s` 格式说明符来填入程序名称。



### description

​	Most calls to the [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) constructor will use the `description=` keyword argument. This argument gives a brief description of what the program does and how it works. In help messages, the description is displayed between the command-line usage string and the help messages for the various arguments.

​	在默认情况下，description 将被换行以便适应给定的空间。如果想改变这种行为，见 [formatter_class](https://docs.python.org/zh-cn/3.13/library/argparse.html#formatter-class) 参数。

### epilog

​	一些程序喜欢在 description 参数后显示额外的对程序的描述。这种文字能够通过给 [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser):: 提供 `epilog=` 参数而被指定。

\>>>

```
>>> parser = argparse.ArgumentParser(
...     description='A foo that bars',
...     epilog="And that's how you'd foo a bar")
>>> parser.print_help()
usage: argparse.py [-h]

A foo that bars

options:
 -h, --help  show this help message and exit

And that's how you'd foo a bar
```

​	和 [description](https://docs.python.org/zh-cn/3.13/library/argparse.html#description) 参数一样，`epilog=` text 在默认情况下会换行，但是这种行为能够被调整通过提供 [formatter_class](https://docs.python.org/zh-cn/3.13/library/argparse.html#formatter-class) 参数给 `ArgumentParse`.

### parents

​	有些时候，少数解析器会使用同一系列参数。 单个解析器能够通过提供 `parents=` 参数给 [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 而使用相同的参数而不是重复这些参数的定义。`parents=` 参数使用 [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 对象的列表，从它们那里收集所有的位置和可选的行为，然后将这写行为加到正在构建的 [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 对象。

\>>>

```
>>> parent_parser = argparse.ArgumentParser(add_help=False)
>>> parent_parser.add_argument('--parent', type=int)

>>> foo_parser = argparse.ArgumentParser(parents=[parent_parser])
>>> foo_parser.add_argument('foo')
>>> foo_parser.parse_args(['--parent', '2', 'XXX'])
Namespace(foo='XXX', parent=2)

>>> bar_parser = argparse.ArgumentParser(parents=[parent_parser])
>>> bar_parser.add_argument('--bar')
>>> bar_parser.parse_args(['--bar', 'YYY'])
Namespace(bar='YYY', parent=None)
```

​	请注意大多数父解析器会指定 `add_help=False` . 否则， `ArgumentParse` 将会看到两个 `-h/--help` 选项（一个在父参数中一个在子参数中）并且产生一个错误。

​	备注

 

​	你在通过 `parents=` 传递解析器之前必须完全初始化它们。 如果你在子解析器之后改变父解析器，这些改变将不会反映在子解析器上。



### formatter_class

[`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 对象允许通过指定备用格式化类来自定义帮助格式。目前，有四种这样的类。

## *class* argparse.**RawDescriptionHelpFormatter**

## *class* argparse.**RawTextHelpFormatter**

## *class* argparse.**ArgumentDefaultsHelpFormatter**

## *class* argparse.**MetavarTypeHelpFormatter**

[`RawDescriptionHelpFormatter`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.RawDescriptionHelpFormatter) 和 [`RawTextHelpFormatter`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.RawTextHelpFormatter) 在正文的描述和展示上给与了更多的控制。[`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 对象会将 [description](https://docs.python.org/zh-cn/3.13/library/argparse.html#description) 和 [epilog](https://docs.python.org/zh-cn/3.13/library/argparse.html#epilog) 的文字在命令行中自动换行。

\>>>

```
>>> parser = argparse.ArgumentParser(
...     prog='PROG',
...     description='''this description
...         was indented weird
...             but that is okay''',
...     epilog='''
...             likewise for this epilog whose whitespace will
...         be cleaned up and whose words will be wrapped
...         across a couple lines''')
>>> parser.print_help()
usage: PROG [-h]

this description was indented weird but that is okay

options:
 -h, --help  show this help message and exit

likewise for this epilog whose whitespace will be cleaned up and whose words
will be wrapped across a couple lines
```

​	传 [`RawDescriptionHelpFormatter`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.RawDescriptionHelpFormatter) 给 `formatter_class=` 表示 [description](https://docs.python.org/zh-cn/3.13/library/argparse.html#description) 和 [epilog](https://docs.python.org/zh-cn/3.13/library/argparse.html#epilog) 已经被正确的格式化了，不能在命令行中被自动换行:

\>>>

```
>>> parser = argparse.ArgumentParser(
...     prog='PROG',
...     formatter_class=argparse.RawDescriptionHelpFormatter,
...     description=textwrap.dedent('''\
...         Please do not mess up this text!
...         --------------------------------
...             I have indented it
...             exactly the way
...             I want it
...         '''))
>>> parser.print_help()
usage: PROG [-h]

Please do not mess up this text!
--------------------------------
   I have indented it
   exactly the way
   I want it

options:
 -h, --help  show this help message and exit
```

[`RawTextHelpFormatter`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.RawTextHelpFormatter) maintains whitespace for all sorts of help text, including argument descriptions. However, multiple newlines are replaced with one. If you wish to preserve multiple blank lines, add spaces between the newlines.

[`ArgumentDefaultsHelpFormatter`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentDefaultsHelpFormatter) 自动添加默认的值的信息到每一个帮助信息的参数中:

\>>>

```
>>> parser = argparse.ArgumentParser(
...     prog='PROG',
...     formatter_class=argparse.ArgumentDefaultsHelpFormatter)
>>> parser.add_argument('--foo', type=int, default=42, help='FOO!')
>>> parser.add_argument('bar', nargs='*', default=[1, 2, 3], help='BAR!')
>>> parser.print_help()
usage: PROG [-h] [--foo FOO] [bar ...]

positional arguments:
 bar         BAR! (default: [1, 2, 3])

options:
 -h, --help  show this help message and exit
 --foo FOO   FOO! (default: 42)
```

[`MetavarTypeHelpFormatter`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.MetavarTypeHelpFormatter) 为它的值在每一个参数中使用 [type](https://docs.python.org/zh-cn/3.13/library/argparse.html#type) 的参数名当作它的显示名（而不是使用通常的格式 [dest](https://docs.python.org/zh-cn/3.13/library/argparse.html#dest) ):

\>>>

```
>>> parser = argparse.ArgumentParser(
...     prog='PROG',
...     formatter_class=argparse.MetavarTypeHelpFormatter)
>>> parser.add_argument('--foo', type=int)
>>> parser.add_argument('bar', type=float)
>>> parser.print_help()
usage: PROG [-h] [--foo int] float

positional arguments:
  float

options:
  -h, --help  show this help message and exit
  --foo int
```

### prefix_chars

​	Most command-line options will use `-` as the prefix, e.g. `-f/--foo`. Parsers that need to support different or additional prefix characters, e.g. for options like `+f` or `/foo`, may specify them using the `prefix_chars=` argument to the [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) constructor:

\>>>

```
>>> parser = argparse.ArgumentParser(prog='PROG', prefix_chars='-+')
>>> parser.add_argument('+f')
>>> parser.add_argument('++bar')
>>> parser.parse_args('+f X ++bar Y'.split())
Namespace(bar='Y', f='X')
```

`prefix_chars=` 参数默认使用 `'-'`。 提供一组不包括 `-` 的字符将导致 `-f/--foo` 选项不被允许。

### fromfile_prefix_chars

​	在某些时候，如在处理一个特别长的参数列表时，把参数列表保存在一个文件中而不是在命令行中打印出来会更有意义。 如果提供 `fromfile_prefix_chars=` 参数给 [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 构造器，则任何以指定字符打头的参数都将被当作文件来处理，并将被它们包含的参数所替代。 举例来说:

\>>>

```
>>> with open('args.txt', 'w', encoding=sys.getfilesystemencoding()) as fp:
...     fp.write('-f\nbar')
...
>>> parser = argparse.ArgumentParser(fromfile_prefix_chars='@')
>>> parser.add_argument('-f')
>>> parser.parse_args(['-f', 'foo', '@args.txt'])
Namespace(f='bar')
```

​	从文件读取的参数在默认情况下必须一个一行（但是可参见 [`convert_arg_line_to_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.convert_arg_line_to_args)）并且它们被视为与命令行上的原始文件引用参数位于同一位置。所以在以上例子中，`['-f', 'foo', '@args.txt']` 的表示和 `['-f', 'foo', '-f', 'bar']` 的表示相同。

[`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 使用 [filesystem encoding and error handler](https://docs.python.org/zh-cn/3.13/glossary.html#term-filesystem-encoding-and-error-handler) 来读取包含参数的文件。

`fromfile_prefix_chars=` 参数默认为 `None`，意味着参数不会被当作文件对待。

*在 3.12 版本发生变更:* [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) changed encoding and errors to read arguments files from default (e.g. [`locale.getpreferredencoding(False)`](https://docs.python.org/zh-cn/3.13/library/locale.html#locale.getpreferredencoding) and `"strict"`) to the [filesystem encoding and error handler](https://docs.python.org/zh-cn/3.13/glossary.html#term-filesystem-encoding-and-error-handler). Arguments file should be encoded in UTF-8 instead of ANSI Codepage on Windows.

### argument_default

​	一般情况下，参数默认会通过设置一个默认到 [`add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument) 或者调用带一组指定键值对的 [`ArgumentParser.set_defaults()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.set_defaults) 方法。但是有些时候，为参数指定一个普遍适用的解析器会更有用。这能够通过传输 `argument_default=` 关键词参数给 [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 来完成。举个栗子，要全局禁止在 [`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args) 中创建属性，我们提供 `argument_default=SUPPRESS`:

\>>>

```
>>> parser = argparse.ArgumentParser(argument_default=argparse.SUPPRESS)
>>> parser.add_argument('--foo')
>>> parser.add_argument('bar', nargs='?')
>>> parser.parse_args(['--foo', '1', 'BAR'])
Namespace(bar='BAR', foo='1')
>>> parser.parse_args([])
Namespace()
```



### allow_abbrev

​	正常情况下，当你向 [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 的 [`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args) 方法传入一个参数列表时，它会 [recognizes abbreviations](https://docs.python.org/zh-cn/3.13/library/argparse.html#prefix-matching)。

​	这个特性可以设置 `allow_abbrev` 为 `False` 来关闭:

\>>>

```
>>> parser = argparse.ArgumentParser(prog='PROG', allow_abbrev=False)
>>> parser.add_argument('--foobar', action='store_true')
>>> parser.add_argument('--foonley', action='store_false')
>>> parser.parse_args(['--foon'])
usage: PROG [-h] [--foobar] [--foonley]
PROG: error: unrecognized arguments: --foon
```

*Added in version 3.5.*

### conflict_handler

[`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 对象不允许在相同选项字符串下有两种行为。默认情况下， [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 对象会产生一个异常如果去创建一个正在使用的选项字符串参数。

\>>>

```
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> parser.add_argument('-f', '--foo', help='old foo help')
>>> parser.add_argument('--foo', help='new foo help')
Traceback (most recent call last):
 ..
ArgumentError: argument --foo: conflicting option string(s): --foo
```

​	有些时候（例如：使用 [parents](https://docs.python.org/zh-cn/3.13/library/argparse.html#parents)），重写旧的有相同选项字符串的参数会更有用。为了产生这种行为， `'resolve'` 值可以提供给 [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 的 `conflict_handler=` 参数:

\>>>

```
>>> parser = argparse.ArgumentParser(prog='PROG', conflict_handler='resolve')
>>> parser.add_argument('-f', '--foo', help='old foo help')
>>> parser.add_argument('--foo', help='new foo help')
>>> parser.print_help()
usage: PROG [-h] [-f FOO] [--foo FOO]

options:
 -h, --help  show this help message and exit
 -f FOO      old foo help
 --foo FOO   new foo help
```

​	注意 [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 对象只能移除一个行为如果它所有的选项字符串都被重写。所以，在上面的例子中，旧的 `-f/--foo` 行为 回合 `-f` 行为保持一样, 因为只有 `--foo` 选项字符串被重写。

### add_help

​	By default, [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) objects add an option which simply displays the parser's help message. If `-h` or `--help` is supplied at the command line, the `ArgumentParser` help will be printed.

​	有时候可能会需要关闭额外的帮助信息。这可以通过在 [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 中设置 `add_help=` 参数为 `False` 来实现。

\>>>

```
>>> parser = argparse.ArgumentParser(prog='PROG', add_help=False)
>>> parser.add_argument('--foo', help='foo help')
>>> parser.print_help()
usage: PROG [--foo FOO]

options:
 --foo FOO  foo help
```

​	帮助选项一般为 `-h/--help`。如果 `prefix_chars=` 被指定并且没有包含 `-` 字符，在这种情况下， `-h` `--help` 不是有效的选项。此时， `prefix_chars` 的第一个字符将用作帮助选项的前缀。

\>>>

```
>>> parser = argparse.ArgumentParser(prog='PROG', prefix_chars='+/')
>>> parser.print_help()
usage: PROG [+h]

options:
  +h, ++help  show this help message and exit
```

### exit_on_error

​	Normally, when you pass an invalid argument list to the [`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args) method of an [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser), it will print a *message* to [`sys.stderr`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stderr) and exit with a status code of 2.

​	如果用户想要手动捕获错误，可通过将 `exit_on_error` 设为 `False` 来启用该特性:

\>>>

```
>>> parser = argparse.ArgumentParser(exit_on_error=False)
>>> parser.add_argument('--integers', type=int)
_StoreAction(option_strings=['--integers'], dest='integers', nargs=None, const=None, default=None, type=<class 'int'>, choices=None, help=None, metavar=None)
>>> try:
...     parser.parse_args('--integers a'.split())
... except argparse.ArgumentError:
...     print('Catching an argumentError')
...
Catching an argumentError
```

*Added in version 3.9.*

## add_argument() 方法

## ArgumentParser.**add_argument**(*name or flags...*, ***[, *action*][, *nargs*][, *const*][, *default*][, *type*][, *choices*][, *required*][, *help*][, *metavar*][, *dest*][, *deprecated*])

​	定义单个的命令行参数应当如何解析。每个形参都在下面有它自己更多的描述，长话短说有：

- [name or flags](https://docs.python.org/zh-cn/3.13/library/argparse.html#name-or-flags) - Either a name or a list of option strings, e.g. `'foo'` or `'-f', '--foo'`.
- [action](https://docs.python.org/zh-cn/3.13/library/argparse.html#action) - 当参数在命令行中出现时使用的动作基本类型。
- [nargs](https://docs.python.org/zh-cn/3.13/library/argparse.html#nargs) - 命令行参数应当消耗的数目。
- [const](https://docs.python.org/zh-cn/3.13/library/argparse.html#const) - 被一些 [action](https://docs.python.org/zh-cn/3.13/library/argparse.html#action) 和 [nargs](https://docs.python.org/zh-cn/3.13/library/argparse.html#nargs) 选择所需求的常数。
- [default](https://docs.python.org/zh-cn/3.13/library/argparse.html#default) - 当参数未在命令行中出现并且也不存在于命名空间对象时所产生的值。
- [type](https://docs.python.org/zh-cn/3.13/library/argparse.html#type) - 命令行参数应当被转换成的类型。
- [choices](https://docs.python.org/zh-cn/3.13/library/argparse.html#choices) - 由允许作为参数的值组成的序列。
- [required](https://docs.python.org/zh-cn/3.13/library/argparse.html#required) - 此命令行选项是否可省略 （仅选项可用）。
- [help](https://docs.python.org/zh-cn/3.13/library/argparse.html#help) - 一个此选项作用的简单描述。
- [metavar](https://docs.python.org/zh-cn/3.13/library/argparse.html#metavar) - 在使用方法消息中使用的参数值示例。
- [dest](https://docs.python.org/zh-cn/3.13/library/argparse.html#dest) - 被添加到 [`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args) 所返回对象上的属性名。
- [deprecated](https://docs.python.org/zh-cn/3.13/library/argparse.html#deprecated) - 参数的使用是否已被弃用。

​	以下部分描述这些参数如何使用。



### name or flags

[`add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument) 方法必须知道是要接收一个可选参数，如 `-f` 或 `--foo`，还是一个位置参数，如由文件名组成的列表。 因此首先传递给 [`add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument) 的参数必须是一组旗标，或一个简单的参数名称。

​	例如，可以这样创建可选参数:

\>>>

```
>>> parser.add_argument('-f', '--foo')
```

​	而位置参数可以这么创建:

\>>>

```
>>> parser.add_argument('bar')
```

​	当 [`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args) 被调用，选项会以 `-` 前缀识别，剩下的参数则会被假定为位置参数:

\>>>

```
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> parser.add_argument('-f', '--foo')
>>> parser.add_argument('bar')
>>> parser.parse_args(['BAR'])
Namespace(bar='BAR', foo=None)
>>> parser.parse_args(['BAR', '--foo', 'FOO'])
Namespace(bar='BAR', foo='FOO')
>>> parser.parse_args(['--foo', 'FOO'])
usage: PROG [-h] [-f FOO] bar
PROG: error: the following arguments are required: bar
```



### action

[`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 对象将命令行参数与动作相关联。这些动作可以做与它们相关联的命令行参数的任何事，尽管大多数动作只是简单的向 [`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args) 返回的对象上添加属性。`action` 命名参数指定了这个命令行参数应当如何处理。供应的动作有：

- `'store'` - 这用于存储参数的值。 这是默认的动作。

- `'store_const'` - 存储由 [const](https://docs.python.org/zh-cn/3.13/library/argparse.html#const) 关键字参数指定的值；请注意 [const](https://docs.python.org/zh-cn/3.13/library/argparse.html#const) 关键字参数默认为 `None`。 `'store_const'` 动作最常被用于指定某类旗标的可选参数。 例如:

  \>>>

  ```
  >>> parser = argparse.ArgumentParser()
  >>> parser.add_argument('--foo', action='store_const', const=42)
  >>> parser.parse_args(['--foo'])
  Namespace(foo=42)
  ```

- `'store_true'` 和 `'store_false'` - 这些是 `'store_const'` 的特例，分别用于存储 `True` 和 `False` 值。 此外，它们还会分别创建默认的 `False` 和 `True` 值:

  \>>>

  ```
  >>> parser = argparse.ArgumentParser()
  >>> parser.add_argument('--foo', action='store_true')
  >>> parser.add_argument('--bar', action='store_false')
  >>> parser.add_argument('--baz', action='store_false')
  >>> parser.parse_args('--foo --bar'.split())
  Namespace(foo=True, bar=False, baz=True)
  ```

- `'append'` - 存储一个列表，并将每个参数值添加到该列表。 它适用于允许可多次指定的选项。 如果默认值非空，则默认的元素将出现在该选项的已解析值中，并将所有来自命令行的值添加在默认值之后。 示例用法:

  \>>>

  ```
  >>> parser = argparse.ArgumentParser()
  >>> parser.add_argument('--foo', action='append')
  >>> parser.parse_args('--foo 1 --foo 2'.split())
  Namespace(foo=['1', '2'])
  ```

- `'append_const'` - 存储一个列表，并将由 [const](https://docs.python.org/zh-cn/3.13/library/argparse.html#const) 关键字参数指定的值添加到列表中；请注意 [const](https://docs.python.org/zh-cn/3.13/library/argparse.html#const) 关键字参数默认为 `None`。 `'append_const'` 动作通常适用于多个参数需要将常量存储到同一列表的场合。 例如:

  \>>>

  ```
  >>> parser = argparse.ArgumentParser()
  >>> parser.add_argument('--str', dest='types', action='append_const', const=str)
  >>> parser.add_argument('--int', dest='types', action='append_const', const=int)
  >>> parser.parse_args('--str --int'.split())
  Namespace(types=[<class 'str'>, <class 'int'>])
  ```

- `'extend'` - This stores a list and appends each item from the multi-value argument list to it. The `'extend'` action is typically used with the [nargs](https://docs.python.org/zh-cn/3.13/library/argparse.html#nargs) keyword argument value `'+'` or `'*'`. Note that when [nargs](https://docs.python.org/zh-cn/3.13/library/argparse.html#nargs) is `None` (the default) or `'?'`, each character of the argument string will be appended to the list. Example usage:

  \>>>

  ```
  >>> parser = argparse.ArgumentParser()
  >>> parser.add_argument("--foo", action="extend", nargs="+", type=str)
  >>> parser.parse_args(["--foo", "f1", "--foo", "f2", "f3", "f4"])
  Namespace(foo=['f1', 'f2', 'f3', 'f4'])
  ```

  *Added in version 3.8.*

- `'count'` - 计算一个关键字参数出现的数目或次数。例如，对于一个增长的详情等级来说有用:

  \>>>

  ```
  >>> parser = argparse.ArgumentParser()
  >>> parser.add_argument('--verbose', '-v', action='count', default=0)
  >>> parser.parse_args(['-vvv'])
  Namespace(verbose=3)
  ```

  请注意，*default* 将为 `None`，除非显式地设为 *0*。

- `'help'` - 打印所有当前解析器中的选项和参数的完整帮助信息，然后退出。默认情况下，一个 help 动作会被自动加入解析器。关于输出是如何创建的，参与 [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser)。

- `'version'` - 期望有一个 `version=` 命名参数在 [`add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument) 调用中，并打印版本信息并在调用后退出:

  \>>>

  ```
  >>> import argparse
  >>> parser = argparse.ArgumentParser(prog='PROG')
  >>> parser.add_argument('--version', action='version', version='%(prog)s 2.0')
  >>> parser.parse_args(['--version'])
  PROG 2.0
  ```

​	Only actions that consume command-line arguments (e.g. `'store'`, `'append'` or `'extend'`) can be used with positional arguments.

## *class* argparse.**BooleanOptionalAction**

​	You may also specify an arbitrary action by passing an [`Action`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.Action) subclass or other object that implements the same interface. The `BooleanOptionalAction` is available in `argparse` and adds support for boolean actions such as `--foo` and `--no-foo`:

\>>>

```
>>> import argparse
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', action=argparse.BooleanOptionalAction)
>>> parser.parse_args(['--no-foo'])
Namespace(foo=False)
```

*Added in version 3.9.*

​	The recommended way to create a custom action is to extend [`Action`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.Action), overriding the `__call__()` method and optionally the `__init__()` and `format_usage()` methods.

​	一个自定义动作的例子:

\>>>

```
>>> class FooAction(argparse.Action):
...     def __init__(self, option_strings, dest, nargs=None, **kwargs):
...         if nargs is not None:
...             raise ValueError("nargs not allowed")
...         super().__init__(option_strings, dest, **kwargs)
...     def __call__(self, parser, namespace, values, option_string=None):
...         print('%r %r %r' % (namespace, values, option_string))
...         setattr(namespace, self.dest, values)
...
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', action=FooAction)
>>> parser.add_argument('bar', action=FooAction)
>>> args = parser.parse_args('1 --foo 2'.split())
Namespace(bar=None, foo=None) '1' None
Namespace(bar='1', foo=None) '2' '--foo'
>>> args
Namespace(bar='1', foo='2')
```

​	更多描述，见 [`Action`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.Action)。



### nargs

[`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) objects usually associate a single command-line argument with a single action to be taken. The `nargs` keyword argument associates a different number of command-line arguments with a single action. See also [指定有歧义的参数](https://docs.python.org/zh-cn/3.13/howto/argparse.html#specifying-ambiguous-arguments). The supported values are:

- `N` （一个整数）。命令行中的 `N` 个参数会被聚集到一个列表中。 例如:

  \>>>

  ```
  >>> parser = argparse.ArgumentParser()
  >>> parser.add_argument('--foo', nargs=2)
  >>> parser.add_argument('bar', nargs=1)
  >>> parser.parse_args('c --foo a b'.split())
  Namespace(bar=['c'], foo=['a', 'b'])
  ```

  注意 `nargs=1` 会产生一个单元素列表。这和默认的元素本身是不同的。

- `'?'`。 如果可能的话，会从命令行中消耗一个参数，并产生一个单独项。 如果当前没有命令行参数，将会产生 [default](https://docs.python.org/zh-cn/3.13/library/argparse.html#default) 值。 注意对于可选参数来说，还有一个额外情况 —— 出现了选项字符串但没有跟随命令行参数，在此情况下将会产生 [const](https://docs.python.org/zh-cn/3.13/library/argparse.html#const) 值。 一些说明这种情况的例子如下:

  \>>>

  ```
  >>> parser = argparse.ArgumentParser()
  >>> parser.add_argument('--foo', nargs='?', const='c', default='d')
  >>> parser.add_argument('bar', nargs='?', default='d')
  >>> parser.parse_args(['XX', '--foo', 'YY'])
  Namespace(bar='XX', foo='YY')
  >>> parser.parse_args(['XX', '--foo'])
  Namespace(bar='XX', foo='c')
  >>> parser.parse_args([])
  Namespace(bar='d', foo='d')
  ```

  `nargs='?'` 的一个更普遍用法是允许可选的输入或输出文件:

  \>>>

  ```
  >>> parser = argparse.ArgumentParser()
  >>> parser.add_argument('infile', nargs='?', type=argparse.FileType('r'),
  ...                     default=sys.stdin)
  >>> parser.add_argument('outfile', nargs='?', type=argparse.FileType('w'),
  ...                     default=sys.stdout)
  >>> parser.parse_args(['input.txt', 'output.txt'])
  Namespace(infile=<_io.TextIOWrapper name='input.txt' encoding='UTF-8'>,
            outfile=<_io.TextIOWrapper name='output.txt' encoding='UTF-8'>)
  >>> parser.parse_args([])
  Namespace(infile=<_io.TextIOWrapper name='<stdin>' encoding='UTF-8'>,
            outfile=<_io.TextIOWrapper name='<stdout>' encoding='UTF-8'>)
  ```

- `'*'`。所有当前命令行参数被聚集到一个列表中。注意通过 `nargs='*'` 来实现多个位置参数通常没有意义，但是多个选项是可能的。例如:

  \>>>

  ```
  >>> parser = argparse.ArgumentParser()
  >>> parser.add_argument('--foo', nargs='*')
  >>> parser.add_argument('--bar', nargs='*')
  >>> parser.add_argument('baz', nargs='*')
  >>> parser.parse_args('a b --foo x y --bar 1 2'.split())
  Namespace(bar=['1', '2'], baz=['a', 'b'], foo=['x', 'y'])
  ```

- `'+'`。和 `'*'` 类似，所有当前命令行参数被聚集到一个列表中。另外，当前没有至少一个命令行参数时会产生一个错误信息。例如:

  \>>>

  ```
  >>> parser = argparse.ArgumentParser(prog='PROG')
  >>> parser.add_argument('foo', nargs='+')
  >>> parser.parse_args(['a', 'b'])
  Namespace(foo=['a', 'b'])
  >>> parser.parse_args([])
  usage: PROG [-h] foo [foo ...]
  PROG: error: the following arguments are required: foo
  ```

​	If the `nargs` keyword argument is not provided, the number of arguments consumed is determined by the [action](https://docs.python.org/zh-cn/3.13/library/argparse.html#action). Generally this means a single command-line argument will be consumed and a single item (not a list) will be produced. Actions that do not consume command-line arguments (e.g. `'store_const'`) set `nargs=0`.



### const

[`add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument) 的 `const` 参数用于保存不从命令行中读取但被各种 [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 动作需求的常数值。最常用的两例为：

- 当 [`add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument) 附带 `action='store_const'` 或 `action='append_const'` 被调用时。 这些动作会把 `const` 值添加到 [`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args) 所返回的对象的属性中。 请查看 [action](https://docs.python.org/zh-cn/3.13/library/argparse.html#action) 的示例描述。 如果未将 `const` 提供给 [`add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument)，它将接收一个 `None` 的默认值。
- 当 [`add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument) 附带选项字符串 (如 `-f` 或 `--foo`) 和 `nargs='?'` 被调用的时候。 这会创建一个可以跟随零到一个命令行参数的选项参数。 当解析该命令行时，如果选项字符串没有跟随任何命令行参数，`const` 的值将被假定为以 `None` 代替。 请参阅 [nargs](https://docs.python.org/zh-cn/3.13/library/argparse.html#nargs) 描述中的示例。

*在 3.11 版本发生变更:* 在默认情况下 `const=None`，包括 `action='append_const'` 或 `action='store_const'` 的时候。



### 默认值

​	所有选项和一些位置参数可能在命令行中被忽略。[`add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument) 的命名参数 `default`，默认值为 `None`，指定了在命令行参数未出现时应当使用的值。对于选项， `default` 值在选项未在命令行中出现时使用:

\>>>

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', default=42)
>>> parser.parse_args(['--foo', '2'])
Namespace(foo='2')
>>> parser.parse_args([])
Namespace(foo=42)
```

​	If the target namespace already has an attribute set, the action *default* will not overwrite it:

\>>>

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', default=42)
>>> parser.parse_args([], namespace=argparse.Namespace(foo=101))
Namespace(foo=101)
```

​	如果 `default` 值是一个字符串，解析器解析此值就像一个命令行参数。特别是，在将属性设置在 [`Namespace`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.Namespace) 的返回值之前，解析器应用任何提供的 [type](https://docs.python.org/zh-cn/3.13/library/argparse.html#type) 转换参数。否则解析器使用原值:

\>>>

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--length', default='10', type=int)
>>> parser.add_argument('--width', default=10.5, type=int)
>>> parser.parse_args()
Namespace(length=10, width=10.5)
```

​	对于 [nargs](https://docs.python.org/zh-cn/3.13/library/argparse.html#nargs) 等于 `?` 或 `*` 的位置参数， `default` 值在没有命令行参数出现时使用。

\>>>

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('foo', nargs='?', default=42)
>>> parser.parse_args(['a'])
Namespace(foo='a')
>>> parser.parse_args([])
Namespace(foo=42)
```

​	For [required](https://docs.python.org/zh-cn/3.13/library/argparse.html#required) arguments, the `default` value is ignored. For example, this applies to positional arguments with [nargs](https://docs.python.org/zh-cn/3.13/library/argparse.html#nargs) values other than `?` or `*`, or optional arguments marked as `required=True`.

​	提供 `default=argparse.SUPPRESS` 导致命令行参数未出现时没有属性被添加:

\>>>

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', default=argparse.SUPPRESS)
>>> parser.parse_args([])
Namespace()
>>> parser.parse_args(['--foo', '1'])
Namespace(foo='1')
```



### type -- 类型

​	默认情况下，解析器会将命令行参数当作简单字符串读入。 然而，命令行字符串经常应当被解读为其他类型，例如 [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float) 或 [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int)。 [`add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument) 的 `type` 关键字允许执行任何必要的类型检查和类型转换。

​	如果 [type](https://docs.python.org/zh-cn/3.13/library/argparse.html#type) 关键字使用了 [default](https://docs.python.org/zh-cn/3.13/library/argparse.html#default) 关键字，则类型转换器仅会在默认值为字符串时被应用。

​	传给 `type` 的参数可以是任何接受单个字符串的可调用对象。 如果函数引发了 [`ArgumentTypeError`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentTypeError), [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError) 或 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)，异常会被捕获并显示经过良好格式化的错误消息。 其他异常类型则不会被处理。

​	普通内置类型和函数可被用作类型转换器:

```
import argparse
import pathlib

parser = argparse.ArgumentParser()
parser.add_argument('count', type=int)
parser.add_argument('distance', type=float)
parser.add_argument('street', type=ascii)
parser.add_argument('code_point', type=ord)
parser.add_argument('dest_file', type=argparse.FileType('w', encoding='latin-1'))
parser.add_argument('datapath', type=pathlib.Path)
```

​	用户自定义的函数也可以被使用:

\>>>

```
>>> def hyphenated(string):
...     return '-'.join([word[:4] for word in string.casefold().split()])
...
>>> parser = argparse.ArgumentParser()
>>> _ = parser.add_argument('short_title', type=hyphenated)
>>> parser.parse_args(['"The Tale of Two Cities"'])
Namespace(short_title='"the-tale-of-two-citi')
```

​	不建议将 [`bool()`](https://docs.python.org/zh-cn/3.13/library/functions.html#bool) 函数用作类型转换器。 它所做的只是将空字符串转为 `False` 而将非空字符串转为 `True`。 这通常不是用户所想要的。

​	通常，`type` 关键字是仅应被用于只会引发上述三种被支持的异常的简单转换的便捷选项。 任何具有更复杂错误处理或资源管理的转换都应当在参数被解析后由下游代码来完成。

​	例如，JSON 或 YAML 转换具有复杂的错误情况，需要能给出比 `type` 关键字所能给出的更好的报告。 [`JSONDecodeError`](https://docs.python.org/zh-cn/3.13/library/json.html#json.JSONDecodeError) 将不会被良好地格式化而 [`FileNotFoundError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#FileNotFoundError) 异常则完全不会被处理。

​	Even [`FileType`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.FileType) has its limitations for use with the `type` keyword. If one argument uses [`FileType`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.FileType) and then a subsequent argument fails, an error is reported but the file is not automatically closed. In this case, it would be better to wait until after the parser has run and then use the [`with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#with)-statement to manage the files.

​	对于简单地检查一组固定值的类型检查器，请考虑改用 [choices](https://docs.python.org/zh-cn/3.13/library/argparse.html#choices) 关键字。



### choices

​	某些命令行参数应当从一组受限的值中选择。 这可以通过将一个序列对象作为 *choices* 关键字参数传给 [`add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument) 来处理。 当执行命令行解析时，参数值将被检查，如果参数不是可接受的值之一就将显示错误消息:

\>>>

```
>>> parser = argparse.ArgumentParser(prog='game.py')
>>> parser.add_argument('move', choices=['rock', 'paper', 'scissors'])
>>> parser.parse_args(['rock'])
Namespace(move='rock')
>>> parser.parse_args(['fire'])
usage: game.py [-h] {rock,paper,scissors}
game.py: error: argument move: invalid choice: 'fire' (choose from 'rock',
'paper', 'scissors')
```

​	Note that inclusion in the *choices* sequence is checked after any [type](https://docs.python.org/zh-cn/3.13/library/argparse.html#type) conversions have been performed, so the type of the objects in the *choices* sequence should match the [type](https://docs.python.org/zh-cn/3.13/library/argparse.html#type) specified.

​	任何序列都可作为 *choices* 值传入，因此 [`list`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#list) 对象、[`tuple`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#tuple) 对象以及自定义序列都是受支持的。

​	不建议使用 [`enum.Enum`](https://docs.python.org/zh-cn/3.13/library/enum.html#enum.Enum)，因为要控制其在用法、帮助和错误消息中的外观是很困难的。

​	已格式化的选项会覆盖默认的 *metavar*，该值一般是派生自 *dest*。 这通常就是你所需要的，因为用户永远不会看到 *dest* 形参。 如果不想要这样的显示（或许因为有很多选择），只需指定一个显式的 [metavar](https://docs.python.org/zh-cn/3.13/library/argparse.html#metavar)。



### required

​	In general, the `argparse` module assumes that flags like `-f` and `--bar` indicate *optional* arguments, which can always be omitted at the command line. To make an option *required*, `True` can be specified for the `required=` keyword argument to [`add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument):

\>>>

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', required=True)
>>> parser.parse_args(['--foo', 'BAR'])
Namespace(foo='BAR')
>>> parser.parse_args([])
usage: [-h] --foo FOO
: error: the following arguments are required: --foo
```

​	如这个例子所示，如果一个选项被标记为 `required`，则当该选项未在命令行中出现时，[`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args) 将会报告一个错误。

​	备注

 

​	必需的选项通常被认为是不适宜的，因为用户会预期 *options* 都是 *可选的*，因此在可能的情况下应当避免使用它们。



### help

​	The `help` value is a string containing a brief description of the argument. When a user requests help (usually by using `-h` or `--help` at the command line), these `help` descriptions will be displayed with each argument.

`help` 字符串可包括各种格式描述符以避免重复使用程序名称或参数 [default](https://docs.python.org/zh-cn/3.13/library/argparse.html#default) 等文本。 有效的描述符包括程序名称 `%(prog)s` 和传给 [`add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument) 的大部分关键字参数，例如 `%(default)s`, `%(type)s` 等等:

\>>>

```
>>> parser = argparse.ArgumentParser(prog='frobble')
>>> parser.add_argument('bar', nargs='?', type=int, default=42,
...                     help='the bar to %(prog)s (default: %(default)s)')
>>> parser.print_help()
usage: frobble [-h] [bar]

positional arguments:
 bar     the bar to frobble (default: 42)

options:
 -h, --help  show this help message and exit
```

​	由于帮助字符串支持 %-formatting，如果你希望在帮助字符串中显示 `%` 字面值，你必须将其转义为 `%%`。

`argparse` supports silencing the help entry for certain options, by setting the `help` value to `argparse.SUPPRESS`:

\>>>

```
>>> parser = argparse.ArgumentParser(prog='frobble')
>>> parser.add_argument('--foo', help=argparse.SUPPRESS)
>>> parser.print_help()
usage: frobble [-h]

options:
  -h, --help  show this help message and exit
```



### metavar

​	When [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) generates help messages, it needs some way to refer to each expected argument. By default, `ArgumentParser` objects use the [dest](https://docs.python.org/zh-cn/3.13/library/argparse.html#dest) value as the "name" of each object. By default, for positional argument actions, the [dest](https://docs.python.org/zh-cn/3.13/library/argparse.html#dest) value is used directly, and for optional argument actions, the [dest](https://docs.python.org/zh-cn/3.13/library/argparse.html#dest) value is uppercased. So, a single positional argument with `dest='bar'` will be referred to as `bar`. A single optional argument `--foo` that should be followed by a single command-line argument will be referred to as `FOO`. An example:

\>>>

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo')
>>> parser.add_argument('bar')
>>> parser.parse_args('X --foo Y'.split())
Namespace(bar='X', foo='Y')
>>> parser.print_help()
usage:  [-h] [--foo FOO] bar

positional arguments:
 bar

options:
 -h, --help  show this help message and exit
 --foo FOO
```

​	可以使用 `metavar` 来指定一个替代名称:

\>>>

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', metavar='YYY')
>>> parser.add_argument('bar', metavar='XXX')
>>> parser.parse_args('X --foo Y'.split())
Namespace(bar='X', foo='Y')
>>> parser.print_help()
usage:  [-h] [--foo YYY] XXX

positional arguments:
 XXX

options:
 -h, --help  show this help message and exit
 --foo YYY
```

​	请注意 `metavar` 仅改变 *显示的* 名称 - [`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args) 对象的属性名称仍然会由 [dest](https://docs.python.org/zh-cn/3.13/library/argparse.html#dest) 值确定。

​	不同的 `nargs` 值可能导致 metavar 被多次使用。 提供一个元组给 `metavar` 即为每个参数指定不同的显示信息:

\>>>

```
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> parser.add_argument('-x', nargs=2)
>>> parser.add_argument('--foo', nargs=2, metavar=('bar', 'baz'))
>>> parser.print_help()
usage: PROG [-h] [-x X X] [--foo bar baz]

options:
 -h, --help     show this help message and exit
 -x X X
 --foo bar baz
```



### dest

​	大多数 [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 动作会添加一些值作为 [`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args) 所返回对象的一个属性。 该属性的名称由 [`add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument) 的 `dest` 关键字参数确定。 对于位置参数动作，`dest` 通常会作为 [`add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument) 的第一个参数提供:

\>>>

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('bar')
>>> parser.parse_args(['XXX'])
Namespace(bar='XXX')
```

​	对于可选参数动作，`dest` 的值通常取自选项字符串。 [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 会通过接受第一个长选项字符串并去掉开头的 `--` 字符串来生成 `dest` 的值。 如果没有提供长选项字符串，则 `dest` 将通过接受第一个短选项字符串并去掉开头的 `-` 字符来获得。 任何内部的 `-` 字符都将被转换为 `_` 字符以确保字符串是有效的属性名称。 下面的例子显示了这种行为:

\>>>

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('-f', '--foo-bar', '--foo')
>>> parser.add_argument('-x', '-y')
>>> parser.parse_args('-f 1 -x 2'.split())
Namespace(foo_bar='1', x='2')
>>> parser.parse_args('--foo 1 -y 2'.split())
Namespace(foo_bar='1', x='2')
```

`dest` 允许提供自定义属性名称:

\>>>

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', dest='bar')
>>> parser.parse_args('--foo XXX'.split())
Namespace(bar='XXX')
```



### deprecated

​	During a project's lifetime, some arguments may need to be removed from the command line. Before removing them, you should inform your users that the arguments are deprecated and will be removed. The `deprecated` keyword argument of [`add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument), which defaults to `False`, specifies if the argument is deprecated and will be removed in the future. For arguments, if `deprecated` is `True`, then a warning will be printed to [`sys.stderr`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stderr) when the argument is used:

\>>>

```
>>> import argparse
>>> parser = argparse.ArgumentParser(prog='snake.py')
>>> parser.add_argument('--legs', default=0, type=int, deprecated=True)
>>> parser.parse_args([])
Namespace(legs=0)
>>> parser.parse_args(['--legs', '4'])  
snake.py: warning: option '--legs' is deprecated
Namespace(legs=4)
```

*Added in version 3.13.*

### Action 类

`Action` classes implement the Action API, a callable which returns a callable which processes arguments from the command-line. Any object which follows this API may be passed as the `action` parameter to [`add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument).

## *class* argparse.**Action**(*option_strings*, *dest*, *nargs=None*, *const=None*, *default=None*, *type=None*, *choices=None*, *required=False*, *help=None*, *metavar=None*)

`Action` objects are used by an [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) to represent the information needed to parse a single argument from one or more strings from the command line. The `Action` class must accept the two positional arguments plus any keyword arguments passed to [`ArgumentParser.add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument) except for the `action` itself.

​	Instances of `Action` (or return value of any callable to the `action` parameter) should have attributes `dest`, `option_strings`, `default`, `type`, `required`, `help`, etc. defined. The easiest way to ensure these attributes are defined is to call `Action.__init__()`.

## **__call__**(*parser*, *namespace*, *values*, *option_string=None*)

`Action` instances should be callable, so subclasses must override the `__call__()` method, which should accept four parameters:

- *parser* - The [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) object which contains this action.
- *namespace* - The [`Namespace`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.Namespace) object that will be returned by [`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args). Most actions add an attribute to this object using [`setattr()`](https://docs.python.org/zh-cn/3.13/library/functions.html#setattr).
- *values* - The associated command-line arguments, with any type conversions applied. Type conversions are specified with the [type](https://docs.python.org/zh-cn/3.13/library/argparse.html#type) keyword argument to [`add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument).
- *option_string* - The option string that was used to invoke this action. The `option_string` argument is optional, and will be absent if the action is associated with a positional argument.

​	The `__call__()` method may perform arbitrary actions, but will typically set attributes on the `namespace` based on `dest` and `values`.

## **format_usage**()

`Action` subclasses can define a `format_usage()` method that takes no argument and return a string which will be used when printing the usage of the program. If such method is not provided, a sensible default will be used.

## parse_args() 方法

## ArgumentParser.**parse_args**(*args=None*, *namespace=None*)

​	将参数字符串转换为对象并将其设为命名空间的属性。 返回带有成员的命名空间。

​	Previous calls to [`add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument) determine exactly what objects are created and how they are assigned. See the documentation for `add_argument()` for details.

- [args](https://docs.python.org/zh-cn/3.13/library/argparse.html#args) - 要解析的字符串列表。 默认值是从 [`sys.argv`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.argv) 获取。
- [namespace](https://docs.python.org/zh-cn/3.13/library/argparse.html#namespace) - 用于获取属性的对象。 默认值是一个新的空 [`Namespace`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.Namespace) 对象。

### 选项值语法

[`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args) 方法支持多种指定选项值的方式（如果它接受选项的话）。 在最简单的情况下，选项和它的值是作为两个单独参数传入的:

\>>>

```
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> parser.add_argument('-x')
>>> parser.add_argument('--foo')
>>> parser.parse_args(['-x', 'X'])
Namespace(foo=None, x='X')
>>> parser.parse_args(['--foo', 'FOO'])
Namespace(foo='FOO', x=None)
```

​	对于长选项（名称长度超过一个字符的选项），选项和值也可以作为单个命令行参数传入，使用 `=` 分隔它们即可:

\>>>

```
>>> parser.parse_args(['--foo=FOO'])
Namespace(foo='FOO', x=None)
```

​	对于短选项（长度只有一个字符的选项），选项和它的值可以拼接在一起:

\>>>

```
>>> parser.parse_args(['-xX'])
Namespace(foo=None, x='X')
```

​	有些短选项可以使用单个 `-` 前缀来进行合并，如果仅有最后一个选项（或没有任何选项）需要值的话:

\>>>

```
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> parser.add_argument('-x', action='store_true')
>>> parser.add_argument('-y', action='store_true')
>>> parser.add_argument('-z')
>>> parser.parse_args(['-xyzZ'])
Namespace(x=True, y=True, z='Z')
```

### 无效的参数

​	在解析命令行时，[`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args) 会检测多种错误，包括有歧义的选项、无效的类型、无效的选项、错误的位置参数个数等等。 当遇到这种错误时，它将退出并打印出错误文本同时附带用法消息:

\>>>

```
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> parser.add_argument('--foo', type=int)
>>> parser.add_argument('bar', nargs='?')

>>> # invalid type
>>> parser.parse_args(['--foo', 'spam'])
usage: PROG [-h] [--foo FOO] [bar]
PROG: error: argument --foo: invalid int value: 'spam'

>>> # invalid option
>>> parser.parse_args(['--bar'])
usage: PROG [-h] [--foo FOO] [bar]
PROG: error: no such option: --bar

>>> # wrong number of arguments
>>> parser.parse_args(['spam', 'badger'])
usage: PROG [-h] [--foo FOO] [bar]
PROG: error: extra arguments found: badger
```

### 包含 `-` 的参数

[`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args) 方法会在用户明显出错时尝试给出错误信息，但某些情况本身就存在歧义。 例如，命令行参数 `-1` 可能是尝试指定一个选项也可能是尝试提供一个位置参数。 [`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args) 方法在此会谨慎行事：位置参数只有在它们看起来像负数并且解析器中没有任何选项看起来像负数时才能以 `-` 打头。:

\>>>

```
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> parser.add_argument('-x')
>>> parser.add_argument('foo', nargs='?')

>>> # no negative number options, so -1 is a positional argument
>>> parser.parse_args(['-x', '-1'])
Namespace(foo=None, x='-1')

>>> # no negative number options, so -1 and -5 are positional arguments
>>> parser.parse_args(['-x', '-1', '-5'])
Namespace(foo='-5', x='-1')

>>> parser = argparse.ArgumentParser(prog='PROG')
>>> parser.add_argument('-1', dest='one')
>>> parser.add_argument('foo', nargs='?')

>>> # negative number options present, so -1 is an option
>>> parser.parse_args(['-1', 'X'])
Namespace(foo=None, one='X')

>>> # negative number options present, so -2 is an option
>>> parser.parse_args(['-2'])
usage: PROG [-h] [-1 ONE] [foo]
PROG: error: no such option: -2

>>> # negative number options present, so both -1s are options
>>> parser.parse_args(['-1', '-1'])
usage: PROG [-h] [-1 ONE] [foo]
PROG: error: argument -1: expected one argument
```

​	如果你有必须以 `-` 打头的位置参数并且看起来不像负数，你可以插入伪参数 `'--'` 以告诉 [`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args) 在那之后的内容是一个位置参数:

\>>>

```
>>> parser.parse_args(['--', '-f'])
Namespace(foo='-f', one=None)
```

​	另请参阅 [针对有歧义参数的 argparse 指引](https://docs.python.org/zh-cn/3.13/howto/argparse.html#specifying-ambiguous-arguments) 来了解更多细节。



### 参数缩写（前缀匹配）

[`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args) 方法 [在默认情况下](https://docs.python.org/zh-cn/3.13/library/argparse.html#allow-abbrev) 允许将长选项缩写为前缀，如果缩写无歧义（即前缀与一个特定选项相匹配）的话:

\>>>

```
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> parser.add_argument('-bacon')
>>> parser.add_argument('-badger')
>>> parser.parse_args('-bac MMM'.split())
Namespace(bacon='MMM', badger=None)
>>> parser.parse_args('-bad WOOD'.split())
Namespace(bacon=None, badger='WOOD')
>>> parser.parse_args('-ba BA'.split())
usage: PROG [-h] [-bacon BACON] [-badger BADGER]
PROG: error: ambiguous option: -ba could match -badger, -bacon
```

​	可产生一个以上选项的参数会引发错误。 此特定可通过将 [allow_abbrev](https://docs.python.org/zh-cn/3.13/library/argparse.html#allow-abbrev) 设为 `False` 来禁用。



### 在 `sys.argv` 以外

​	Sometimes it may be useful to have an [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) parse arguments other than those of [`sys.argv`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.argv). This can be accomplished by passing a list of strings to [`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args). This is useful for testing at the interactive prompt:

\>>>

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument(
...     'integers', metavar='int', type=int, choices=range(10),
...     nargs='+', help='an integer in the range 0..9')
>>> parser.add_argument(
...     '--sum', dest='accumulate', action='store_const', const=sum,
...     default=max, help='sum the integers (default: find the max)')
>>> parser.parse_args(['1', '2', '3', '4'])
Namespace(accumulate=<built-in function max>, integers=[1, 2, 3, 4])
>>> parser.parse_args(['1', '2', '3', '4', '--sum'])
Namespace(accumulate=<built-in function sum>, integers=[1, 2, 3, 4])
```



### 命名空间对象

## *class* argparse.**Namespace**

​	由 [`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args) 默认使用的简单类，可创建一个存放属性的对象并将其返回。

​	这个类被有意做得很简单，只是一个具有可读字符串表示形式的 [`object`](https://docs.python.org/zh-cn/3.13/library/functions.html#object)。 如果你更喜欢类似字典的属性视图，你可以使用标准 Python 中惯常的 [`vars()`](https://docs.python.org/zh-cn/3.13/library/functions.html#vars):

\>>>

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo')
>>> args = parser.parse_args(['--foo', 'BAR'])
>>> vars(args)
{'foo': 'BAR'}
```

​	另一个用处是让 [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 为一个已存在对象而不是为一个新的 [`Namespace`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.Namespace) 对象的属性赋值。 这可以通过指定 `namespace=` 关键字参数来实现:

\>>>

```
>>> class C:
...     pass
...
>>> c = C()
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo')
>>> parser.parse_args(args=['--foo', 'BAR'], namespace=c)
>>> c.foo
'BAR'
```

## 其它实用工具

### 子命令

## ArgumentParser.**add_subparsers**(***[, *title*][, *description*][, *prog*][, *parser_class*][, *action*][, *dest*][, *required*][, *help*][, *metavar*])

​	Many programs split up their functionality into a number of subcommands, for example, the `svn` program can invoke subcommands like `svn checkout`, `svn update`, and `svn commit`. Splitting up functionality this way can be a particularly good idea when a program performs several different functions which require different kinds of command-line arguments. [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) supports the creation of such subcommands with the `add_subparsers()` method. The `add_subparsers()` method is normally called with no arguments and returns a special action object. This object has a single method, `add_parser()`, which takes a command name and any `ArgumentParser` constructor arguments, and returns an `ArgumentParser` object that can be modified as usual.

​	形参的描述

- *title* - title for the sub-parser group in help output; by default "subcommands" if description is provided, otherwise uses title for positional arguments
- *description* - description for the sub-parser group in help output, by default `None`
- *prog* - usage information that will be displayed with sub-command help, by default the name of the program and any positional arguments before the subparser argument
- *parser_class* - class which will be used to create sub-parser instances, by default the class of the current parser (e.g. [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser))
- [action](https://docs.python.org/zh-cn/3.13/library/argparse.html#action) - 当此参数在命令行中出现时要执行动作的基本类型
- [dest](https://docs.python.org/zh-cn/3.13/library/argparse.html#dest) - 将被用于保存子命令名称的属性名；默认为 `None` 即不保存任何值
- [required](https://docs.python.org/zh-cn/3.13/library/argparse.html#required) - 是否必须要提供子命令，默认为 `False` (在 3.7 中新增)
- [help](https://docs.python.org/zh-cn/3.13/library/argparse.html#help) - 在输出帮助中的子解析器分组帮助信息，默认为 `None`
- [metavar](https://docs.python.org/zh-cn/3.13/library/argparse.html#metavar) - string presenting available subcommands in help; by default it is `None` and presents subcommands in form {cmd1, cmd2, ..}

​	一些使用示例:

\>>>

```
>>> # create the top-level parser
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> parser.add_argument('--foo', action='store_true', help='foo help')
>>> subparsers = parser.add_subparsers(help='subcommand help')
>>>
>>> # create the parser for the "a" command
>>> parser_a = subparsers.add_parser('a', help='a help')
>>> parser_a.add_argument('bar', type=int, help='bar help')
>>>
>>> # create the parser for the "b" command
>>> parser_b = subparsers.add_parser('b', help='b help')
>>> parser_b.add_argument('--baz', choices=('X', 'Y', 'Z'), help='baz help')
>>>
>>> # parse some argument lists
>>> parser.parse_args(['a', '12'])
Namespace(bar=12, foo=False)
>>> parser.parse_args(['--foo', 'b', '--baz', 'Z'])
Namespace(baz='Z', foo=True)
```

​	请注意 [`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args) 返回的对象将只包含主解析器和由命令行所选择的子解析器的属性（而没有任何其他子解析器）。 因此在上面的例子中，当指定了 `a` 命令时，将只存在 `foo` 和 `bar` 属性，而当指定了 `b` 命令时，则只存在 `foo` 和 `baz` 属性。

​	类似地，当一个子解析器请求帮助消息时，只有该特定解析器的帮助消息会被打印出来。 帮助消息将不包括父解析器或同级解析器的消息。 （每个子解析器命令一条帮助消息，但是，也可以像上面那样通过将 `help=` 参数传入 `add_parser()` 来给出。）

\>>>

```
>>> parser.parse_args(['--help'])
usage: PROG [-h] [--foo] {a,b} ...

positional arguments:
  {a,b}   subcommand help
    a     a help
    b     b help

options:
  -h, --help  show this help message and exit
  --foo   foo help

>>> parser.parse_args(['a', '--help'])
usage: PROG a [-h] bar

positional arguments:
  bar     bar help

options:
  -h, --help  show this help message and exit

>>> parser.parse_args(['b', '--help'])
usage: PROG b [-h] [--baz {X,Y,Z}]

options:
  -h, --help     show this help message and exit
  --baz {X,Y,Z}  baz help
```

[`add_subparsers()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_subparsers) 方法也支持 `title` 和 `description` 关键字参数。 当两者都存在时，子解析器的命令将出现在输出帮助消息中它们自己的分组内。 例如:

\>>>

```
>>> parser = argparse.ArgumentParser()
>>> subparsers = parser.add_subparsers(title='subcommands',
...                                    description='valid subcommands',
...                                    help='additional help')
>>> subparsers.add_parser('foo')
>>> subparsers.add_parser('bar')
>>> parser.parse_args(['-h'])
usage:  [-h] {foo,bar} ...

options:
  -h, --help  show this help message and exit

subcommands:
  valid subcommands

  {foo,bar}   additional help
```

​	此外，`add_parser()` 还支持附加的 *aliases* 参数，它允许多个字符串指向同一个子解析器。 下面的例子，类似于 `svn`，将别名 `co` 设为 `checkout` 的缩写形式:

\>>>

```
>>> parser = argparse.ArgumentParser()
>>> subparsers = parser.add_subparsers()
>>> checkout = subparsers.add_parser('checkout', aliases=['co'])
>>> checkout.add_argument('foo')
>>> parser.parse_args(['co', 'bar'])
Namespace(foo='bar')
```

`add_parser()` 也支持附加的 *deprecated* 参数，它允许弃用子解析器。

\>>>

```
>>> import argparse
>>> parser = argparse.ArgumentParser(prog='chicken.py')
>>> subparsers = parser.add_subparsers()
>>> run = subparsers.add_parser('run')
>>> fly = subparsers.add_parser('fly', deprecated=True)
>>> parser.parse_args(['fly'])  
chicken.py: warning: command 'fly' is deprecated
Namespace()
```

*Added in version 3.13.*

​	One particularly effective way of handling subcommands is to combine the use of the [`add_subparsers()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_subparsers) method with calls to [`set_defaults()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.set_defaults) so that each subparser knows which Python function it should execute. For example:

\>>>

```
>>> # subcommand functions
>>> def foo(args):
...     print(args.x * args.y)
...
>>> def bar(args):
...     print('((%s))' % args.z)
...
>>> # create the top-level parser
>>> parser = argparse.ArgumentParser()
>>> subparsers = parser.add_subparsers(required=True)
>>>
>>> # create the parser for the "foo" command
>>> parser_foo = subparsers.add_parser('foo')
>>> parser_foo.add_argument('-x', type=int, default=1)
>>> parser_foo.add_argument('y', type=float)
>>> parser_foo.set_defaults(func=foo)
>>>
>>> # create the parser for the "bar" command
>>> parser_bar = subparsers.add_parser('bar')
>>> parser_bar.add_argument('z')
>>> parser_bar.set_defaults(func=bar)
>>>
>>> # parse the args and call whatever function was selected
>>> args = parser.parse_args('foo 1 -x 2'.split())
>>> args.func(args)
2.0
>>>
>>> # parse the args and call whatever function was selected
>>> args = parser.parse_args('bar XYZYX'.split())
>>> args.func(args)
((XYZYX))
```

​	通过这种方式，你可以在参数解析结束后让 [`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args) 执行调用适当函数的任务。 像这样将函数关联到动作通常是你处理每个子解析器的不同动作的最简便方式。 但是，如果有必要检查被发起调用的子解析器的名称，则 [`add_subparsers()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_subparsers) 调用的 `dest` 关键字参数将可实现:

\>>>

```
>>> parser = argparse.ArgumentParser()
>>> subparsers = parser.add_subparsers(dest='subparser_name')
>>> subparser1 = subparsers.add_parser('1')
>>> subparser1.add_argument('-x')
>>> subparser2 = subparsers.add_parser('2')
>>> subparser2.add_argument('y')
>>> parser.parse_args(['2', 'frobble'])
Namespace(subparser_name='2', y='frobble')
```

*在 3.7 版本发生变更:* New *required* keyword-only parameter.

### FileType 对象

## *class* argparse.**FileType**(*mode='r'*, *bufsize=-1*, *encoding=None*, *errors=None*)

[`FileType`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.FileType) 工厂类用于创建可作为 [`ArgumentParser.add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument) 的 type 参数传入的对象。 以 [`FileType`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.FileType) 对象作为其类型的参数将使用命令行参数以所请求模式、缓冲区大小、编码格式和错误处理方式打开文件（请参阅 [`open()`](https://docs.python.org/zh-cn/3.13/library/functions.html#open) 函数了解详情）:

\>>>

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--raw', type=argparse.FileType('wb', 0))
>>> parser.add_argument('out', type=argparse.FileType('w', encoding='UTF-8'))
>>> parser.parse_args(['--raw', 'raw.dat', 'file.txt'])
Namespace(out=<_io.TextIOWrapper name='file.txt' mode='w' encoding='UTF-8'>, raw=<_io.FileIO name='raw.dat' mode='wb'>)
```

​	FileType 对象能理解伪参数 `'-'` 并会自动将其转换为 [`sys.stdin`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stdin) 用于可读的 [`FileType`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.FileType) 对象以及 [`sys.stdout`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stdout) 用于可写的 [`FileType`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.FileType) 对象:

\>>>

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('infile', type=argparse.FileType('r'))
>>> parser.parse_args(['-'])
Namespace(infile=<_io.TextIOWrapper name='<stdin>' encoding='UTF-8'>)
```

*在 3.4 版本发生变更:* 增加了 *encodings* 和 *errors* 形参。

### 参数组

## ArgumentParser.**add_argument_group**(*title=None*, *description=None*, ***[, *argument_default*][, *conflict_handler*])

​	By default, [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) groups command-line arguments into "positional arguments" and "options" when displaying help messages. When there is a better conceptual grouping of arguments than this default one, appropriate groups can be created using the `add_argument_group()` method:

\>>>

```
>>> parser = argparse.ArgumentParser(prog='PROG', add_help=False)
>>> group = parser.add_argument_group('group')
>>> group.add_argument('--foo', help='foo help')
>>> group.add_argument('bar', help='bar help')
>>> parser.print_help()
usage: PROG [--foo FOO] bar

group:
  bar    bar help
  --foo FOO  foo help
```

​	The [`add_argument_group()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument_group) method returns an argument group object which has an [`add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument) method just like a regular [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser). When an argument is added to the group, the parser treats it just like a normal argument, but displays the argument in a separate group for help messages. The `add_argument_group()` method accepts *title* and *description* arguments which can be used to customize this display:

\>>>

```
>>> parser = argparse.ArgumentParser(prog='PROG', add_help=False)
>>> group1 = parser.add_argument_group('group1', 'group1 description')
>>> group1.add_argument('foo', help='foo help')
>>> group2 = parser.add_argument_group('group2', 'group2 description')
>>> group2.add_argument('--bar', help='bar help')
>>> parser.print_help()
usage: PROG [--bar BAR] foo

group1:
  group1 description

  foo    foo help

group2:
  group2 description

  --bar BAR  bar help
```

​	The optional, keyword-only parameters [argument_default](https://docs.python.org/zh-cn/3.13/library/argparse.html#argument-default) and [conflict_handler](https://docs.python.org/zh-cn/3.13/library/argparse.html#conflict-handler) allow for finer-grained control of the behavior of the argument group. These parameters have the same meaning as in the [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) constructor, but apply specifically to the argument group rather than the entire parser.

​	请注意任意不在你的自定义分组中的参数最终都将回到通常的“位置参数”和“可选参数”分组中。

*在 3.11 版本发生变更:* 在参数分组上调用 [`add_argument_group()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument_group) 已被弃用。 此特性从未受到支持并且不保证总能正确工作。此函数出现在 API 中是继承导致的意外并将在未来被移除。

### 互斥

## ArgumentParser.**add_mutually_exclusive_group**(*required=False*)

​	Create a mutually exclusive group. `argparse` will make sure that only one of the arguments in the mutually exclusive group was present on the command line:

\>>>

```
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> group = parser.add_mutually_exclusive_group()
>>> group.add_argument('--foo', action='store_true')
>>> group.add_argument('--bar', action='store_false')
>>> parser.parse_args(['--foo'])
Namespace(bar=True, foo=True)
>>> parser.parse_args(['--bar'])
Namespace(bar=False, foo=False)
>>> parser.parse_args(['--foo', '--bar'])
usage: PROG [-h] [--foo | --bar]
PROG: error: argument --bar: not allowed with argument --foo
```

[`add_mutually_exclusive_group()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_mutually_exclusive_group) 方法也接受一个 *required* 参数，表示在互斥组中至少有一个参数是需要的:

\>>>

```
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> group = parser.add_mutually_exclusive_group(required=True)
>>> group.add_argument('--foo', action='store_true')
>>> group.add_argument('--bar', action='store_false')
>>> parser.parse_args([])
usage: PROG [-h] (--foo | --bar)
PROG: error: one of the arguments --foo --bar is required
```

​	请注意当前互斥的参数组不支持 [`add_argument_group()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument_group) 的 *title* 和 *description* 参数。 但是，互斥的参数数可以被添加到具有 title 和 description 的参数组中。 例如:

\>>>

```
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> group = parser.add_argument_group('Group title', 'Group description')
>>> exclusive_group = group.add_mutually_exclusive_group(required=True)
>>> exclusive_group.add_argument('--foo', help='foo help')
>>> exclusive_group.add_argument('--bar', help='bar help')
>>> parser.print_help()
usage: PROG [-h] (--foo FOO | --bar BAR)

options:
  -h, --help  show this help message and exit

Group title:
  Group description

  --foo FOO   foo help
  --bar BAR   bar help
```

*在 3.11 版本发生变更:* 在互斥的分组上调用 [`add_argument_group()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument_group) 或 [`add_mutually_exclusive_group()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_mutually_exclusive_group) 已被弃用。 这些特性从未受到支持并且不保证总能正确工作。 这些函数出现在 API 中是继承导致的意外并将在未来被移除。

### 解析器默认值

## ArgumentParser.**set_defaults**(***kwargs*)

​	在大多数时候，[`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args) 所返回对象的属性将完全通过检查命令行参数和参数动作来确定。 [`set_defaults()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.set_defaults) 则允许加入一些无须任何命令行检查的额外属性:

\>>>

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('foo', type=int)
>>> parser.set_defaults(bar=42, baz='badger')
>>> parser.parse_args(['736'])
Namespace(bar=42, baz='badger', foo=736)
```

​	请注意解析器层级的默认值总是会覆盖参数层级的默认值:

\>>>

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', default='bar')
>>> parser.set_defaults(foo='spam')
>>> parser.parse_args([])
Namespace(foo='spam')
```

​	解析器层级默认值在需要多解析器时会特别有用。 请参阅 [`add_subparsers()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_subparsers) 方法了解此类型的一个示例。

## ArgumentParser.**get_default**(*dest*)

​	获取一个命名空间属性的默认值，该值是由 [`add_argument()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.add_argument) 或 [`set_defaults()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.set_defaults) 设置的:

\>>>

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', default='badger')
>>> parser.get_default('foo')
'badger'
```

### 打印帮助

​	在大多数典型应用中，[`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args) 将负责任何用法和错误消息的格式化和打印。 但是，也可使用某些其他格式化方法:

## ArgumentParser.**print_usage**(*file=None*)

​	打印一段简短描述，说明应当如何在命令行中发起调用 [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser)。 如果 *file* 为 `None`，则默认使用 [`sys.stdout`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stdout)。

## ArgumentParser.**print_help**(*file=None*)

​	打印一条帮助消息，包括程序用法和通过 [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 注册的相关参数信息。 如果 *file* 为 `None`，则默认使用 [`sys.stdout`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stdout)。

​	还存在这些方法的几个变化形式，它们只返回字符串而不打印消息:

## ArgumentParser.**format_usage**()

​	返回一个包含简短描述的字符串，说明应当如何在命令行中发起调用 [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser)。

## ArgumentParser.**format_help**()

​	反回一个包含帮助消息的字符串，包括程序用法和通过 [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 注册的相关参数信息。

### 部分解析

## ArgumentParser.**parse_known_args**(*args=None*, *namespace=None*)

​	有时一个脚本可能只解析部分命令行参数，而将其余的参数继续传递给另一个脚本或程序。 在这种情况下，[`parse_known_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_known_args) 方法会很有用处。 它的作用方式很类似 [`parse_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_args) 但区别在于当存在额外参数时它不会产生错误。 而是会返回一个由两个条目构成的元组，其中包含带成员的命名空间和剩余参数字符串的列表。

\>>>

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', action='store_true')
>>> parser.add_argument('bar')
>>> parser.parse_known_args(['--foo', '--badger', 'BAR', 'spam'])
(Namespace(bar='BAR', foo=True), ['--badger', 'spam'])
```

​	警告

 

[前缀匹配](https://docs.python.org/zh-cn/3.13/library/argparse.html#prefix-matching) 规则应用于 [`parse_known_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_known_args)。 一个解析器即使在某个选项只是已知选项的前缀时也能读取该选项，而不是将其放入剩余参数列表。

### 自定义文件解析

## ArgumentParser.**convert_arg_line_to_args**(*arg_line*)

​	从文件读取的参数（见 [`ArgumentParser`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser) 的 *fromfile_prefix_chars* 关键字参数）将是一行读取一个参数。 [`convert_arg_line_to_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.convert_arg_line_to_args) 可被重写以使用更复杂的读取方式。

​	此方法接受从参数文件读取的字符串形式的单个参数 *arg_line*。 它返回从该字符串解析出的参数列表。 此方法将在每次按顺序从参数文件读取一行时被调用一次。

​	此方法的一个有用的重写是将每个以空格分隔的单词视为一个参数。 下面的例子演示了如何实现这一点:

```
class MyArgumentParser(argparse.ArgumentParser):
    def convert_arg_line_to_args(self, arg_line):
        return arg_line.split()
```

### 退出方法

## ArgumentParser.**exit**(*status=0*, *message=None*)

​	This method terminates the program, exiting with the specified *status* and, if given, it prints a *message* to [`sys.stderr`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stderr) before that. The user can override this method to handle these steps differently:

```
class ErrorCatchingArgumentParser(argparse.ArgumentParser):
    def exit(self, status=0, message=None):
        if status:
            raise Exception(f'Exiting because of an error: {message}')
        exit(status)
```

## ArgumentParser.**error**(*message*)

​	This method prints a usage message, including the *message*, to [`sys.stderr`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stderr) and terminates the program with a status code of 2.

### 混合解析

## ArgumentParser.**parse_intermixed_args**(*args=None*, *namespace=None*)

## ArgumentParser.**parse_known_intermixed_args**(*args=None*, *namespace=None*)

​	许多 Unix 命令允许用户混用可选参数与位置参数。 [`parse_intermixed_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_intermixed_args) 和 [`parse_known_intermixed_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_known_intermixed_args) 方法均支持这种解析风格。

​	These parsers do not support all the `argparse` features, and will raise exceptions if unsupported features are used. In particular, subparsers, and mutually exclusive groups that include both optionals and positionals are not supported.

​	下面的例子显示了 [`parse_known_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_known_args) 与 [`parse_intermixed_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_intermixed_args) 之间的差异：前者会将 `['2', '3']` 返回为未解析的参数，而后者会将所有位置参数收集至 `rest` 中。

\>>>

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo')
>>> parser.add_argument('cmd')
>>> parser.add_argument('rest', nargs='*', type=int)
>>> parser.parse_known_args('doit 1 --foo bar 2 3'.split())
(Namespace(cmd='doit', foo='bar', rest=[1]), ['2', '3'])
>>> parser.parse_intermixed_args('doit 1 --foo bar 2 3'.split())
Namespace(cmd='doit', foo='bar', rest=[1, 2, 3])
```

[`parse_known_intermixed_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_known_intermixed_args) 返回由两个条目组成的元组，其中包含带成员的命名空间以及剩余参数字符串列表。 当存在任何剩余的未解析参数字符串时 [`parse_intermixed_args()`](https://docs.python.org/zh-cn/3.13/library/argparse.html#argparse.ArgumentParser.parse_intermixed_args) 将引发一个错误。

*Added in version 3.7.*

## 异常

## *exception* argparse.**ArgumentError**

​	来自创建或使用某个参数的消息（可选或位置参数）。

​	此异常的字符串值即异常消息，并附带有导致该异常的参数的相关信息。

## *exception* argparse.**ArgumentTypeError**

​	当从一个命令行字符串到特定类型的转换出现问题时引发。

​	指南与教程

- [argparse 教程](https://docs.python.org/zh-cn/3.13/howto/argparse.html)
- [Upgrading optparse code](https://docs.python.org/zh-cn/3.13/howto/argparse-optparse.html)
