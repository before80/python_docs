+++
title = "pydoc --- 文档生成器和在线帮助系统"
date = 2024-11-15T21:03:03+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/pydoc.html](https://docs.python.org/zh-cn/3.13/library/pydoc.html)
>
> 收录该文档的时间：`2024-11-15T21:03:03+08:00`

# `pydoc` --- 文档生成器和在线帮助系统

**源代码:** [Lib/pydoc.py](https://github.com/python/cpython/tree/3.13/Lib/pydoc.py)

------

`pydoc` 模块会根据 Python 模块自动生成文档。 生成的文档可在控制台中显示为文本页面，提供给 Web 浏览器或者保存为 HTML 文件。

​	对于模块、类、函数和方法，显示的文档内容取自对象的文档字符串（即 the [`__doc__`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#definition.__doc__) 属性），并会递归地从其带有文档的成员中获取。 如果没有文档字符串，则 `pydoc` 会尝试从源文件中类、函数或方法的定义上方，或者模块最上方的注释行代码块获取描述文本（参见 [`inspect.getcomments()`](https://docs.python.org/zh-cn/3.13/library/inspect.html#inspect.getcomments)。）

​	内置函数 [`help()`](https://docs.python.org/zh-cn/3.13/library/functions.html#help) 会发起调用交互式解释器的在线帮助系统，该系统使用 `pydoc` 在控制台上生成文本形式的文档内容。 同样的文本文档也可以在 Python 解释器以外通过在操作系统的命令提示符中以脚本方式运行 **pydoc** 来查看。 例如，运行

```
python -m pydoc sys
```

​	在终端提示符下将通过 [`sys`](https://docs.python.org/zh-cn/3.13/library/sys.html#module-sys) 模块显示文档内容，其样式类似于 Unix **man** 命令所显示的指南页面。 **pydoc** 的参数可以为函数、模块、包，或带点号的对模块中的类、方法或函数以及包中的模块的引用。 如果传给 **pydoc** 的参数像是一个路径（即包含所在操作系统的路径分隔符，例如 Unix 的正斜杠），并且其指向一个现有的 Python 源文件，则会为该文件生成文档内容。

​	备注

 

​	为了找到对象及其文档的内容，`pydoc` 会导入文档所属的模块。 因而，在此情况下任何模块层级的代码都将被执行。 请使用 `if __name__ == '__main__':` 来确保特定代码仅在文件是作为脚本被发起调用而不是被导入时执行。

​	When printing output to the console, **pydoc** attempts to paginate the output for easier reading. If either the `MANPAGER` or the `PAGER` environment variable is set, **pydoc** will use its value as a pagination program. When both are set, `MANPAGER` is used.

​	在参数前指定 `-w` 旗标将把 HTML 文档写入到当前目录下的一个文件中，而不是在控制台中显示文本。

​	在参数前指定 `-k` 旗标将在全部可用模块的提要行中搜索参数所给定的关键字，具体方式同样类似于 Unix **man** 命令。 模块的提要行就是其文档字符串的第一行。

​	你还可以使用 **pydoc** 在本机上启动一个 HTTP 服务器并向来访的 Web 浏览器展示文档。 **python -m pydoc -p 1234** 将在 1234 端口上启动 HTTP 服务器，允许在你所用的 Web 浏览器上通过 `http://localhost:1234/` 来浏览文档。 指定 `0` 作为端口号将任意选择一个未使用的端口。

**python -m pydoc -n <hostname>** 将启动在给定主机名上监听的服务器。 默认的主机名为 'localhost' 但是如果你希望能从其他机器上搜索该服务器，你可能会想要改变服务器所响应的主机名。 在开发阶段此特性会特别有用，如果你想要在一个容器中运行 pydoc 的话。

**python -m pydoc -b** 将启动服务器并额外打开一个 Web 浏览器访问模块索引页。 所发布的每个页面顶端都带有导航栏，你可以点击 *Get* 来获取特定条目的帮助信息，点击 *Search* 在所有模块的摘要行中搜索某个关键词，或点击 *Module index*, *Topics* 和 *Keywords* 前往相应的页面。

​	当 **pydoc** 生成文档内容时，它会使用当前环境和路径来定位模块。 因此，发起调用 **pydoc spam** 得到的文档版本会与你启动 Python 解释器并输入 `import spam` 时得到的模块版本完全相同。

​	核心模块的模块文档应当位于 `https://docs.python.org/X.Y/library/` 其中 `X` 和 `Y` 是 Python 解释器的主要和次要版本号。 这可以通过将 `PYTHONDOCS` 环境变量设为不同的 URL 或包含标准库参考指南页面的本地目录来覆盖。

*在 3.2 版本发生变更:* 添加 `-b` 选项。

*在 3.3 版本发生变更:* 命令行选项 `-g` 已经移除。

*在 3.4 版本发生变更:* 现在 `pydoc` 会使用 [`inspect.signature()`](https://docs.python.org/zh-cn/3.13/library/inspect.html#inspect.signature) 而不是 [`inspect.getfullargspec()`](https://docs.python.org/zh-cn/3.13/library/inspect.html#inspect.getfullargspec) 来从可调用对象中提取签名信息。

*在 3.7 版本发生变更:* 添加 `-n` 选项。
