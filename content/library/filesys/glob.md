+++
title = "glob --- Unix 风格的路径名模式扩展"
date = 2024-11-15T11:53:09+08:00
weight = 60
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/glob.html](https://docs.python.org/zh-cn/3.13/library/glob.html)
>
> 收录该文档的时间：`2024-11-15T11:53:09+08:00`

# `glob` --- Unix 风格的路径名模式扩展

**源代码:** [Lib/glob.py](https://github.com/python/cpython/tree/3.13/Lib/glob.py)

------

[`glob`](https://docs.python.org/zh-cn/3.13/library/glob.html#module-glob) 模块会按照 Unix shell 所使用的规则找出所有匹配特定模式的路径名称，但返回结果的顺序是不确定的。 波浪号扩展不会生效，但 `*`, `?` 以及用 `[]` 表示的字符范围将被正确地匹配。 这是通过配合使用 [`os.scandir()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.scandir) 和 [`fnmatch.fnmatch()`](https://docs.python.org/zh-cn/3.13/library/fnmatch.html#fnmatch.fnmatch) 函数来实现的，而不是通过实际发起调用子 shell。

​	请注意以点号 (`.`) 打头的文件只能用同样以点号打头的模式来匹配，这不同于 [`fnmatch.fnmatch()`](https://docs.python.org/zh-cn/3.13/library/fnmatch.html#fnmatch.fnmatch) 或 [`pathlib.Path.glob()`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.Path.glob)。 （对于波浪号和 shell 变量扩展，请使用 [`os.path.expanduser()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.expanduser) 和 [`os.path.expandvars()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.expandvars)。）

​	对于字面值匹配，请将原字符用方括号括起来。 例如，`'[?]'` 将匹配字符 `'?'`。

[`glob`](https://docs.python.org/zh-cn/3.13/library/glob.html#module-glob) 模块定义了下列函数：

## glob.**glob**(*pathname*, ***, *root_dir=None*, *dir_fd=None*, *recursive=False*, *include_hidden=False*)

​	返回一个匹配 *pathname* 的可能为空的路径名列表，其中的元素必须为包含路径信息的字符串。 *pathname* 可以是绝对路径 (如 `/usr/src/Python-1.5/Makefile`) 或相对路径 (如 `../../Tools/*/*.gif`)，并可包含 shell 风格的通配符。 无效的符号链接也将包括在结果中 (如像在 shell 中一样)。 结果是否排序取决于具体文件系统。 如果某个符合条件的文件在调用此函数期间被移除或添加，是否包括该文件的路径是没有规定的。

​	如果 *root_dir* 不为 `None`，则它应当是指明要搜索的根目录的 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。 它用在 [`glob()`](https://docs.python.org/zh-cn/3.13/library/glob.html#module-glob) 上与在调用它之前改变当前目录有相同的效果。 如果 *pathname* 为相对路径，结果将包含相对于 *root_dir* 的路径。

​	本函数带有 *dir_fd* 参数，支持 [基于目录描述符的相对路径](https://docs.python.org/zh-cn/3.13/library/os.html#dir-fd)。

​	如果 *recursive* 为真值，则模式 "`**`" 将匹配目录中的任何文件以及零个或多个目录、子目录和符号链接。 如果模式加了一个 [`os.sep`](https://docs.python.org/zh-cn/3.13/library/os.html#os.sep) 或 [`os.altsep`](https://docs.python.org/zh-cn/3.13/library/os.html#os.altsep) 则将不匹配文件。

​	如果 *include_hidden* 为真值，"`**`" 模式将匹配隐藏目录。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `glob.glob` 并附带参数 `pathname`, `recursive`。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `glob.glob/2` 并附带参数 `pathname`, `recursive`, `root_dir`, `dir_fd`。

​	备注

 

​	在一个较大的目录树中使用 "`**`" 模式可能会消耗非常多的时间。

​	备注

 

​	如果 *pathname* 包含多个 "`**`" 模式并且 *recursive* 为真值则此函数可能返回重复的路径名。

*在 3.5 版本发生变更:* 支持使用 "`**`" 的递归 glob。

*在 3.10 版本发生变更:* 添加了 *root_dir* 和 *dir_fd* 形参。

*在 3.11 版本发生变更:* 增加了 *include_hidden* 形参。

## glob.**iglob**(*pathname*, ***, *root_dir=None*, *dir_fd=None*, *recursive=False*, *include_hidden=False*)

​	返回一个 [iterator](https://docs.python.org/zh-cn/3.13/glossary.html#term-iterator)，它会产生与 [`glob()`](https://docs.python.org/zh-cn/3.13/library/glob.html#module-glob) 相同的结果，但不会实际地同时保存它们。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `glob.glob` 并附带参数 `pathname`, `recursive`。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `glob.glob/2` 并附带参数 `pathname`, `recursive`, `root_dir`, `dir_fd`。

​	备注

 

​	如果 *pathname* 包含多个 "`**`" 模式并且 *recursive* 为真值则此函数可能返回重复的路径名。

*在 3.5 版本发生变更:* 支持使用 "`**`" 的递归 glob。

*在 3.10 版本发生变更:* 添加了 *root_dir* 和 *dir_fd* 形参。

*在 3.11 版本发生变更:* 增加了 *include_hidden* 形参。

## glob.**escape**(*pathname*)

​	转义所有特殊字符 (`'?'`, `'*'` 和 `'['`)。 这适用于当你想要匹配可能带有特殊字符的任意字符串字面值的情况。 在 drive/UNC 共享点中的特殊字符不会被转义，例如在 Windows 上 `escape('//?/c:/Quo vadis?.txt')` 将返回 `'//?/c:/Quo vadis[?].txt'`。

> Added in version 3.4.
>

## glob.**translate**(*pathname*, ***, *recursive=False*, *include_hidden=False*, *seps=None*)

​	将给定的路径规格说明转换为一个正则表达式供 [`re.match()`](https://docs.python.org/zh-cn/3.13/library/re.html#re.match) 使用。 路径规格说明可以包含 shell 风格的通配符。

​	例如:



``` python
>>> import glob, re
>>>
>>> regex = glob.translate('**/*.txt', recursive=True, include_hidden=True)
>>> regex
'(?s:(?:.+/)?[^/]*\\.txt)\\Z'
>>> reobj = re.compile(regex)
>>> reobj.match('foo/bar/baz.txt')
<re.Match object; span=(0, 15), match='foo/bar/baz.txt'>
```

​	路径分隔符与部件对该函数是有意义的，这与 [`fnmatch.translate()`](https://docs.python.org/zh-cn/3.13/library/fnmatch.html#fnmatch.translate) 不同。 在默认情况下通配符不会匹配路径分隔符，而 `*` 模式部件将精确匹配一个路径部件。

​	如果 *recursive* 为真值，则模式部件 "`**`" 将匹配任意数量的路径部件。

​	如果 *include_hidden* 为真值，则通配符可以匹配以点号 (`.`) 打头的路径部件。

​	可以向 *seps* 参数提供一个由路径分隔符组成的序列。 如果未给出，则将使用 [`os.sep`](https://docs.python.org/zh-cn/3.13/library/os.html#os.sep) 和 [`altsep`](https://docs.python.org/zh-cn/3.13/library/os.html#os.altsep) (如果可用)。

​	参见

 

[`pathlib.PurePath.full_match()`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.PurePath.full_match) 和 [`pathlib.Path.glob()`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.Path.glob) 方法，它们将调用此函数来实现模式匹配和 glob 操作。

> Added in version 3.13.
>

## 例子

​	考虑一个包含以下文件的目录: `1.gif`, `2.txt`, `card.gif` 以及一个子目录 `sub` 且其中只包含一个文件 `3.txt`。 [`glob()`](https://docs.python.org/zh-cn/3.13/library/glob.html#module-glob) 将产生如下结果。 请注意路径的任何开头部件都将被保留。



``` python
>>> import glob
>>> glob.glob('./[0-9].*')
['./1.gif', './2.txt']
>>> glob.glob('*.gif')
['1.gif', 'card.gif']
>>> glob.glob('?.gif')
['1.gif']
>>> glob.glob('**/*.txt', recursive=True)
['2.txt', 'sub/3.txt']
>>> glob.glob('./**/', recursive=True)
['./', './sub/']
```

​	如果目录包含以 `.` 打头的文件，它们默认将不会被匹配。 例如，考虑一个包含 `card.gif` 和 `.card.gif` 的目录:



``` python
>>> import glob
>>> glob.glob('*.gif')
['card.gif']
>>> glob.glob('.c*')
['.card.gif']
```

​	参见

 

[`fnmatch`](https://docs.python.org/zh-cn/3.13/library/fnmatch.html#module-fnmatch) 模块提供了 shell 风格的文件名（而非路径）扩展。

​	参见

 

[`pathlib`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#module-pathlib) 模块提供高级路径对象。
