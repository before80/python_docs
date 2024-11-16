+++
title = "site --- 站点专属的配置钩子"
date = 2024-11-15T21:12:39+08:00
weight = 140
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/site.html](https://docs.python.org/zh-cn/3.13/library/site.html)
>
> 收录该文档的时间：`2024-11-15T21:12:39+08:00`

# `site` --- 站点专属的配置钩子

**源代码:** [Lib/site.py](https://github.com/python/cpython/tree/3.13/Lib/site.py)

------

**这个模块将在初始化时被自动导入。** 此自动导入可以通过使用解释器的 [`-S`]({{< ref "/using/cmdline#cmdoption-S" >}}) 选项来屏蔽。

​	正常导入此模块将会把站点专属的路径添加到模块搜索路径并将一些 [可调用对象]({{< ref "/library/constants#site-consts" >}})，包括 [`help()`]({{< ref "/library/functions#help" >}}) 添加到内置命名空间。 不过，Python 启动选项 [`-S`]({{< ref "/using/cmdline#cmdoption-S" >}}) 会阻止此行为且此模块可被安全地导入而不会自动修改模块搜索路径或添加内置对象。 要显式地触发通常的站点专属附加项，请调用 [`main()`]({{< ref "/library/python/site#site.main" >}}) 函数。

> 在 3.3 版本发生变更: 在之前即便使用了 [`-S`]({{< ref "/using/cmdline#cmdoption-S" >}})，导入此模块仍然会触发路径操纵。

​	它会以一个开部和一个尾部来构造至多四个目录作为起点。 对于头部，它将使用 `sys.prefix` 和 `sys.exec_prefix`；空的头部会被跳过。 对于尾部，它将使用空字符串然后是 `lib/site-packages` (在 Windows 上) 或 `lib/python*X.Y[t]*/site-packages` (在 Unix 和 macOS 上)。 (可选后缀 "t" 表示 [free threading]({{< ref "/glossary/idx#term-free-threading" >}}) 构建版，并会在 `"t"` 存在于 [`sys.abiflags`]({{< ref "/library/python/sys#sys.abiflags" >}}) 常量中时被添加。) 对于每个不同的头尾组合，它会查看其是否指向现有的目录，如果确实如此，则将其添加到 `sys.path` 并且还会检查新添加目录中的配置文件。

> 在 3.5 版本发生变更: 对 "site-python" 目录的支持已被移除。

> 在 3.13 版本发生变更: 在 Unix 上，[自由线程]({{< ref "/glossary/idx#term-free-threading" >}}) Python 安装版是在版本专属的目录名称中以 "t" 后缀来标识的，例如 `lib/python3.13t/`。

​	如果名为 "pyvenv.cfg" 的文件存在于 sys.executable 之上的一个目录中，则 sys.prefix 和 sys.exec_prefix 将被设置为该目录，并且还会检查 site-packages （ sys.base_prefix 和 sys.base_exec_prefix 始终是 Python 安装的 "真实" 前缀）。 如果 "pyvenv.cfg" （引导程序配置文件）包含设置为非 "true"（不区分大小写）的 "include-system-site-packages" 键，则不会在系统级前缀中搜索 site-packages；反之则会。

​	一个路径配置文件是具有 `*name*.pth` 命名格式的文件，并且存在上面提到的四个目录之一中；它的内容是要添加到 `sys.path` 中的额外项目（每行一个）。不存在的项目不会添加到 `sys.path`，并且不会检查项目指向的是目录还是文件。项目不会被添加到 `sys.path` 超过一次。空行和由 `#` 起始的行会被跳过。以 `import` 开始的行（跟着空格或 TAB）会被执行。

​备注
 

​	每次启动 Python，在 `.pth` 文件中的可执行行都将会被运行，而不管特定的模块实际上是否需要被使用。 因此，其影响应降至最低。可执行行的主要预期目的是使相关模块可导入（加载第三方导入钩子，调整 `PATH` 等）。如果它发生了，任何其他的初始化都应当在模块实际导入之前完成。将代码块限制为一行是一种有意采取的措施，不鼓励在此处放置更复杂的内容。

> 在 3.13 版本发生变更: 现在 `.pth` 文件会首先使用 UTF-8 来解码，如果失败会再改用 [locale encoding]({{< ref "/glossary/idx#term-locale-encoding" >}}) 来解码。

​	例如，假设 `sys.prefix` 和 `sys.exec_prefix` 已经被设置为 `/usr/local`。 Python X.Y 的库之后被安装为 `/usr/local/lib/python*X.Y*`。假设有一个拥有三个孙目录 `foo`, `bar` 和 `spam` 的子目录 `/usr/local/lib/python*X.Y*/site-packages`，并且有两个路径配置文件 `foo.pth` 和 `bar.pth`。假定 `foo.pth` 内容如下:

```
# foo 包配置

foo
bar
bletch
```

​	并且 `bar.pth` 包含:

```
# bar 包配置

bar
```

​	则下面特定版目录将以如下顺序被添加到 `sys.path`。

```
/usr/local/lib/pythonX.Y/site-packages/bar
/usr/local/lib/pythonX.Y/site-packages/foo
```

​	请注意 `bletch` 已被省略因为它并不存在；`bar` 目前在 `foo` 目录之前因为 `bar.pth` 按字母顺序排在 `foo.pth` 之前；而 `spam` 已被省略因为它在两个路径配置文件中都未被提及。



## [`sitecustomize`]({{< ref "/library/python/site#module-sitecustomize" >}})

​	在这些路径操作之后，会尝试导入 一个名为 [`sitecustomize`]({{< ref "/library/python/site#module-sitecustomize" >}}) 的模块，它可以执行任意站点专属的定制。 它通常是由系统管理员在 site-packages 目录下创建的。 如果此导入失败并引发 [`ImportError`]({{< ref "/library/exceptions#ImportError" >}}) 或其子类的异常，并且异常的 [`name`]({{< ref "/library/exceptions#ImportError.name" >}}) 属性等于 `'sitecustomize'`，则它会被静默地忽略。 如果 Python 是在没有可用输出流的情况下启动的，例如在 Windows 上使用 `pythonw.exe` (它被默认被用于 IDLE)，则来自 [`sitecustomize`]({{< ref "/library/python/site#module-sitecustomize" >}}) 的输出尝试会被忽略。 任何其他异常都会导致静默且可能令人困惑的进程失败。



## [`usercustomize`]({{< ref "/library/python/site#module-usercustomize" >}})

​	在此之后，会尝试导入一个名为 [`usercustomize`]({{< ref "/library/python/site#module-usercustomize" >}}) 的模块，如果 [`ENABLE_USER_SITE`]({{< ref "/library/python/site#site.ENABLE_USER_SITE" >}}) 为真值，则它可以执行任意的用户专属定制。 这个文件应在用户的 site-packages 目录中创建（见下文），除非被 [`-s`]({{< ref "/using/cmdline#cmdoption-s" >}}) 所禁用，在其他情况下该目录都是 `sys.path` 的组成部分。 如果此导入失败并引发 [`ImportError`]({{< ref "/library/exceptions#ImportError" >}}) 或其子类异常，并且异常的 [`name`]({{< ref "/library/exceptions#ImportError.name" >}}) 属性等于 `'usercustomize'`，它会被静默地忽略。

​	请注意对于某些非 Unix 系统来说，`sys.prefix` 和 `sys.exec_prefix` 均为空值，并且路径操作会被跳过；但是仍然会尝试导入 [`sitecustomize`]({{< ref "/library/python/site#module-sitecustomize" >}}) 和 [`usercustomize`]({{< ref "/library/python/site#module-usercustomize" >}})。



## Readline 配置

​	在支持 [`readline`]({{< ref "/library/text/readline#module-readline" >}}) 的系统上，这个模块也将导入并配置 [`rlcompleter`]({{< ref "/library/text/rlcompleter#module-rlcompleter" >}}) 模块，如果 Python 是以 [交互模式]({{< ref "/tutorial/interpreter#tut-interactive" >}}) 启动并且不带 [`-S`]({{< ref "/using/cmdline#cmdoption-S" >}}) 选项的话。 默认的行为是启用 tab 键补全并使用 `~/.python_history` 作为历史存档文件。 要禁用它，请删除（或重载）你的 [`sitecustomize`]({{< ref "/library/python/site#module-sitecustomize" >}}) 或 [`usercustomize`]({{< ref "/library/python/site#module-usercustomize" >}}) 模块或 [`PYTHONSTARTUP`]({{< ref "/using/cmdline#envvar-PYTHONSTARTUP" >}}) 文件中的 [`sys.__interactivehook__`]({{< ref "/library/python/sys#sys.__interactivehook__" >}}) 属性。

> 在 3.4 版本发生变更: rlcompleter 和 history 会被自动激活。

## 模块内容

## site.**PREFIXES**

​	site-packages 目录的前缀列表。

## site.**ENABLE_USER_SITE**

​	显示用户 site-packages 目录状态的旗标。 `True` 意味着它被启用并被添加到 `sys.path`。 `False` 意味着它按照用户请求被禁用 (通过 [`-s`]({{< ref "/using/cmdline#cmdoption-s" >}}) 或 [`PYTHONNOUSERSITE`]({{< ref "/using/cmdline#envvar-PYTHONNOUSERSITE" >}}))。 `None` 意味着它因安全理由（user 或 group id 和 effective id 之间不匹配）或是被管理员所禁用。

## site.**USER_SITE**

​	运行中的 Python 的用户级 site-packages 的路径。 它可以为 `None`，如果 [`getusersitepackages()`]({{< ref "/library/python/site#site.getusersitepackages" >}}) 尚未被调用的话。 默认值在 UNIX 和 macOS 非框架构建版上为 `~/.local/lib/python*X.Y*[t]/site-packages`，在 macOS 框架构建版上为 `~/Library/Python/*X.Y*/lib/python/site-packages`，而在 Windows 上为 `*%APPDATA%*\Python\Python*XY*\site-packages`。 可选的 "t" 表示自由线程构建版。 此目录属于 site 目录，这意味着其中的 `.pth` 文件将会被处理。files in it will be processed.

## site.**USER_BASE**

​	用户级 site-packages 目录的路径。 如果尚未调用 [`getuserbase()`]({{< ref "/library/python/site#site.getuserbase" >}}) 则它可以为 `None`。默认值在 Unix 和 macOS 非框架编译版上为 `~/.local`，在 macOS框架编译版上为 `~/Library/Python/*X.Y*`，而在 Windows 上则为 `*%APPDATA%*\Python`。 这个值会被用于计算针对 [用户安装方案]({{< ref "/library/python/sysconfig#sysconfig-user-scheme" >}}) 的脚本、数据文件、Python 模块等的安装目录。 另请参阅 [`PYTHONUSERBASE`]({{< ref "/using/cmdline#envvar-PYTHONUSERBASE" >}})。

## site.**main**()

​	将所有的标准站点专属目录添加到模块搜索路径。 这个函数会在导入此模块时被自动调用，除非 Python 解释器启动时附带了 [`-S`]({{< ref "/using/cmdline#cmdoption-S" >}}) 旗标。

> 在 3.3 版本发生变更: 这个函数使用无条件调用。

## site.**addsitedir**(*sitedir*, *known_paths=None*)

​	将一个目录添加到 sys.path 并处理其 `.pth` 文件。 通常被用于 [`sitecustomize`]({{< ref "/library/python/site#module-sitecustomize" >}}) 或 [`usercustomize`]({{< ref "/library/python/site#module-usercustomize" >}}) (见下文)。

## site.**getsitepackages**()

​	返回包含所有全局 site-packages 目录的列表。

> Added in version 3.2.
>

## site.**getuserbase**()

​	返回用户基准目录的路径 [`USER_BASE`]({{< ref "/library/python/site#site.USER_BASE" >}})。 如果它尚未被初始化，则此函数还将参照 [`PYTHONUSERBASE`]({{< ref "/using/cmdline#envvar-PYTHONUSERBASE" >}}) 来设置它。

> Added in version 3.2.
>

## site.**getusersitepackages**()

​	返回用户专属 site-packages 目录的路径 [`USER_SITE`]({{< ref "/library/python/site#site.USER_SITE" >}})。 如果它尚未被初始化，则此函数还将参照 [`USER_BASE`]({{< ref "/library/python/site#site.USER_BASE" >}}) 来设置它。 要确定用户专属 site-packages 是否已被添加到 `sys.path` 则应当使用 [`ENABLE_USER_SITE`]({{< ref "/library/python/site#site.ENABLE_USER_SITE" >}})。

> Added in version 3.2.
>



## 命令行界面

[`site`]({{< ref "/library/python/site#module-site" >}}) 模块还提供了一个从命令行获取用户目录的方式:

```
$ python -m site --user-site
/home/user/.local/lib/python3.11/site-packages
```

​	如果它被不带参数地调用，它将在标准输出打印 [`sys.path`]({{< ref "/library/python/sys#sys.path" >}}) 的内容，再打印 [`USER_BASE`]({{< ref "/library/python/site#site.USER_BASE" >}}) 的值以及该目录是否存在，然后打印 [`USER_SITE`]({{< ref "/library/python/site#site.USER_SITE" >}}) 的相应信息，最后打印 [`ENABLE_USER_SITE`]({{< ref "/library/python/site#site.ENABLE_USER_SITE" >}}) 的值。

## `--user-base`

​	输出用户基本的路径。

## `--user-site`

​	输出用户site-packages目录的路径。

​	如果同时给出了两个选项，则将打印用户基准目录和用户站点信息（总是按此顺序），并以 [`os.pathsep`]({{< ref "/library/allos/os#os.pathsep" >}}) 分隔。

​	如果给出了其中一个选项，脚本将退出并返回以下值中的一个：如果用户级 site-packages 目录被启用则为 `0`，如果它被用户禁用则为 `1`，如果它因安全理由或被管理员禁用则为 `2`，如果发生错误则为大于 2 的值。

​参见
- [**PEP 370**](https://peps.python.org/pep-0370/) -- 分用户的 site-packages 目录
- [sys.path 模块搜索路径的初始化]({{< ref "/library/modules/sys_path_init#sys-path-init" >}}) -- [`sys.path`]({{< ref "/library/python/sys#sys.path" >}}) 的初始化。
