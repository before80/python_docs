+++
title = "`getopt` --- C 风格的命令行选项解析器"
date = 2024-11-14T22:00:25+08:00
weight = 350
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/getopt.html](https://docs.python.org/zh-cn/3.13/library/getopt.html)
>
> 收录该文档的时间：`2024-11-14T22:00:25+08:00`

# `getopt` --- C 风格的命令行选项解析器

**源代码:** [Lib/getopt.py](https://github.com/python/cpython/tree/3.13/Lib/getopt.py)

*自 3.13 版本弃用:* [`getopt`](https://docs.python.org/zh-cn/3.13/library/getopt.html#module-getopt) 模块已被 [soft deprecated](https://docs.python.org/zh-cn/3.13/glossary.html#term-soft-deprecated) 并且将不再继续开发；开发将转至 [`argparse`](https://docs.python.org/zh-cn/3.13/library/argparse.html#module-argparse) 模块进行。

​	备注

 

[`getopt`](https://docs.python.org/zh-cn/3.13/library/getopt.html#module-getopt) 模块是一个命令行选项解析器，其 API 的设计会将 C `getopt()` 函数的用户感到熟悉。 不熟悉 C `getopt()` 函数或者希望编写更少代码并获得更完善帮助和错误消息的用户应当考虑改用 [`argparse`](https://docs.python.org/zh-cn/3.13/library/argparse.html#module-argparse) 模块。

------

​	此模块可协助脚本解析 `sys.argv` 中的命令行参数。 它支持与 Unix `getopt()` 函数相同的惯例 (包括形式为 '`-`' 和 '`--`' 的参数的特殊含义)。 也可以通过可选的第三个参数来使用类似于 GNU 软件所支持形式的长选项。

​	此模块提供了两个函数和一个异常:

## getopt.**getopt**(*args*, *shortopts*, *longopts=[]*)

​	解析命令行选项与形参列表。 *args* 是要解析的参数列表，不包含最开头的对正在运行的程序的引用。 通常，这意味着 `sys.argv[1:]`。 *shortopts* 是脚本要识别的选项字母，带有要求后缀一个冒号 (`':'`；即与 Unix `getopt()` 所用的格式相同) 的选项。

​	备注

 

​	与 GNU `getopt()` 不同，在非选项参数之后，所有后续参数都会被视为非选项。 这类似于非 GNU Unix 系统的运作方式。

​	如果指定了 *longopts*，则必须为一个由应当被支持的长选项名称组成的列表。 开头的 `'--'` 字符不应被包括在选项名称中。 要求参数的长选项后应当带一个等号 (`'='`)。 可选参数不被支持。 如果想仅接受长选项，则 *shortopts* 应为一个空字符串。 命令行中的长选项只要提供了恰好能匹配可接受选项之一的选项名称前缀即可被识别。 举例来说，如果 *longopts* 为 `['foo', 'frob']`，则选项 `--fo` 将匹配为 `--foo`，但 `--f` 将不能得到唯一匹配，因此将引发 [`GetoptError`](https://docs.python.org/zh-cn/3.13/library/getopt.html#getopt.GetoptError)。

​	返回值由两个元素组成：第一个是 `(option, value)` 对的列表；第二个是在去除该选项列表后余下的程序参数列表（这也就是 *args* 的尾部切片）。每个被返回的选项与值对的第一个元素是选项，短选项前缀一个连字符 (例如 `'-x'`)，长选项则前缀两个连字符 (例如 `'--long-option'`)，第二个元素是选项参数，如果选项不带参数则为空字符串。 列表中选项的排列顺序与它们被解析的顺序相同，因此允许多次出现。 长选项与短选项可以混用。

## getopt.**gnu_getopt**(*args*, *shortopts*, *longopts=[]*)

​	此函数与 [`getopt()`](https://docs.python.org/zh-cn/3.13/library/getopt.html#module-getopt) 类似，区别在于它默认使用 GNU 风格的扫描模式。 这意味着选项和非选项参数可能会混在一起。 [`getopt()`](https://docs.python.org/zh-cn/3.13/library/getopt.html#module-getopt) 函数将在遇到非选项参数时立即停止处理选项。

​	如果选项字符串的第一个字符为 `'+'`，或者如果设置了环境变量 `POSIXLY_CORRECT`，则选项处理会在遇到非选项参数时立即停止。

## *exception* getopt.**GetoptError**

​	当参数列表中出现不可识别的选项或当一个需要参数的选项未带参数时将引发此异常。 此异常的参数是一个指明错误原因的字符串。 对于长选项，将一个参数传给不需要参数的选项也将导致此异常被引发。 `msg` 和 `opt` 属性将给出错误消息和关联的选项；如果没有关联到此异常的特定选项，则 `opt` 将为空字符串。

## *exception* getopt.**error**

[`GetoptError`](https://docs.python.org/zh-cn/3.13/library/getopt.html#getopt.GetoptError) 的别名；用于向后兼容。

​	一个仅使用 Unix 风格选项的例子:

\>>>

```
>>> import getopt
>>> args = '-a -b -cfoo -d bar a1 a2'.split()
>>> args
['-a', '-b', '-cfoo', '-d', 'bar', 'a1', 'a2']
>>> optlist, args = getopt.getopt(args, 'abc:d:')
>>> optlist
[('-a', ''), ('-b', ''), ('-c', 'foo'), ('-d', 'bar')]
>>> args
['a1', 'a2']
```

​	使用长选项名也同样容易:

\>>>

```
>>> s = '--condition=foo --testing --output-file abc.def -x a1 a2'
>>> args = s.split()
>>> args
['--condition=foo', '--testing', '--output-file', 'abc.def', '-x', 'a1', 'a2']
>>> optlist, args = getopt.getopt(args, 'x', [
...     'condition=', 'output-file=', 'testing'])
>>> optlist
[('--condition', 'foo'), ('--testing', ''), ('--output-file', 'abc.def'), ('-x', '')]
>>> args
['a1', 'a2']
```

​	In a script, typical usage is something like this:

```
import getopt, sys

def main():
    try:
        opts, args = getopt.getopt(sys.argv[1:], "ho:v", ["help", "output="])
    except getopt.GetoptError as err:
        # 打印帮助信息并退出：
        print(err)  # 将打印 "option -a not recognized" 这样的内容
        usage()
        sys.exit(2)
    output = None
    verbose = False
    for o, a in opts:
        if o == "-v":
            verbose = True
        elif o in ("-h", "--help"):
            usage()
            sys.exit()
        elif o in ("-o", "--output"):
            output = a
        else:
            assert False, "unhandled option"
    # ...

if __name__ == "__main__":
    main()
```

​	Note that an equivalent command line interface could be produced with less code and more informative help and error messages by using the [`argparse`](https://docs.python.org/zh-cn/3.13/library/argparse.html#module-argparse) module:

```
import argparse

if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    parser.add_argument('-o', '--output')
    parser.add_argument('-v', dest='verbose', action='store_true')
    args = parser.parse_args()
    # ... 使用 args.output ...
    # ... 使用 args.verbose ...
```

> 参见
>
> 模块 [`argparse`](https://docs.python.org/zh-cn/3.13/library/argparse.html#module-argparse)
>
> ​	替代的命令行选项和参数解析库。
