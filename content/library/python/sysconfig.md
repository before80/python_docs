+++
title = "sysconfig --- 提供对 Python 配置信息的访问"
date = 2024-11-15T21:12:39+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/sysconfig.html](https://docs.python.org/zh-cn/3.13/library/sysconfig.html)
>
> 收录该文档的时间：`2024-11-15T21:12:39+08:00`

# `sysconfig` --- 提供对 Python 配置信息的访问

*Added in version 3.2.*

**源代码:** [Lib/sysconfig](https://github.com/python/cpython/tree/3.13/Lib/sysconfig)

------

[`sysconfig`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#module-sysconfig) 模块提供了对 Python 配置信息的访问支持，比如安装路径列表和有关当前平台的配置变量。

## 配置变量

​	一个包含 `Makefile` 和 `pyconfig.h` 头文件的 Python 分发版，这是构建 Python 二进制文件本身和用 `setuptools` 编译的第三方 C 扩展所必需的。

[`sysconfig`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#module-sysconfig) 将这些文件中的所有变量放在一个字典对象中，可用 [`get_config_vars()`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#sysconfig.get_config_vars) 或 [`get_config_var()`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#sysconfig.get_config_var) 访问。

​	请注意在 Windows 上，这是一个小得多的集合。

## sysconfig.**get_config_vars**(**args*)

​	不带参数时，返回一个与当前平台相关的所有配置变量的字典。

​	带参数时，返回一个由在配置变量字典中查找每个参数的结果的值组成的列表。

​	对于每个参数，如果未找到值，则返回 `None`。

## sysconfig.**get_config_var**(*name*)

​	返回单个变量 *name* 的值。 等价于 `get_config_vars().get(name)`。

​	如果未找到 *name*，则返回 `None`。

​	用法示例:

\>>>

```
>>> import sysconfig
>>> sysconfig.get_config_var('Py_ENABLE_SHARED')
0
>>> sysconfig.get_config_var('LIBDIR')
'/usr/local/lib'
>>> sysconfig.get_config_vars('AR', 'CXX')
['ar', 'g++']
```



## 安装路径

​	Python 会使用根据平台和安装选项区别处理的安装方案。 这些方案被存储在 [`sysconfig`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#module-sysconfig) 中基于 [`os.name`](https://docs.python.org/zh-cn/3.13/library/os.html#os.name) 返回的值来确定的唯一标识符下。 软件包安装程序使用这些方案来确定将文件复制到何处。

​	Python 目前支持九种方案:

- *posix_prefix*: 针对 POSIX 平台如 Linux 或 macOS 的方案。 这是在安装 Python 或者组件时的默认方案。
- *posix_home*: 当使用 *home* 选项时，针对 POSIX 平台的方案。 该方案定义了位于特定 home 前缀下的路径。
- *posix_user*: 当使用 *user* 选项时，针对 POSIX 平台的方案。 该方案定义了位于用户主目录 ([`site.USER_BASE`](https://docs.python.org/zh-cn/3.13/library/site.html#site.USER_BASE)) 下的路径。
- *posix_venv*: 针对 POSIX 平台上 [`Python 虚拟环境`](https://docs.python.org/zh-cn/3.13/library/venv.html#module-venv) 的方案；在默认情况下与 *posix_prefix* 相同。
- *nt*: 针对 Windows 的方案。 这是在安装 Python 或其组件时的默认方案。
- *nt_user*: 针对 Windows,当使用了 *user* 选项时的方案。
- *nt_venv*: 针对 Windows 上 [`Python 虚拟环境`](https://docs.python.org/zh-cn/3.13/library/venv.html#module-venv) 的方案；在默认情况下与 *nt* 相同。
- *venv*: 根据 Python 运行所在平台的不同来设置 *posix_venv* 或 *nt_venv* 的值的方案。
- *osx_framework_user*: 针对 macOS，当使用了 *user* 选项时的方案。

​	每个方案本身由一系列路径组成并且每个路径都有唯一的标识符。 Python 目前使用了八个路径:

- *stdlib*: 包含非平台专属的标准 Python 库文件的目录。
- *platstdlib*: 包含平台专属的标准 Python 库文件的目录。
- *platlib*: 用于站点专属、平台专属的文件的目录。
- *purelib*: 用于站点专属、非平台专属的文件（‘纯’ Python）的目录。
- *include*: 针对用于 Python C-API 的非平台专属头文件的目录。
- *platinclude*: 针对用于 Python C-API 的平台专属头文件的目录。
- *scripts*: 用于脚本文件的目录。
- *data*: 用于数据文件的目录。



## 用户方案

​	此方案被设计为针对没有全局 site-packages 目录写入权限或不想安装到该目录的用户的最便捷解决方案。

​	文件将被安装到 [`site.USER_BASE`](https://docs.python.org/zh-cn/3.13/library/site.html#site.USER_BASE) (以下称为 `*userbase*`) 的子目录中。 此方案将在同一个位置 (或称 [`site.USER_SITE`](https://docs.python.org/zh-cn/3.13/library/site.html#site.USER_SITE)) 中安装纯 Python 模块和扩展模块。

### `posix_user`

| Path         | 安装目录                                   |
| :----------- | :----------------------------------------- |
| *stdlib*     | `*userbase*/lib/python*X.Y*`               |
| *platstdlib* | `*userbase*/lib/python*X.Y*`               |
| *platlib*    | `*userbase*/lib/python*X.Y*/site-packages` |
| *purelib*    | `*userbase*/lib/python*X.Y*/site-packages` |
| *include*    | `*userbase*/include/python*X.Y*`           |
| *scripts*    | `*userbase*/bin`                           |
| *data*       | `*userbase*`                               |

### `nt_user`

| Path         | 安装目录                              |
| :----------- | :------------------------------------ |
| *stdlib*     | `*userbase*\Python*XY*`               |
| *platstdlib* | `*userbase*\Python*XY*`               |
| *platlib*    | `*userbase*\Python*XY*\site-packages` |
| *purelib*    | `*userbase*\Python*XY*\site-packages` |
| *include*    | `*userbase*\Python*XY*\Include`       |
| *scripts*    | `*userbase*\Python*XY*\Scripts`       |
| *data*       | `*userbase*`                          |

### `osx_framework_user`

| Path         | 安装目录                              |
| :----------- | :------------------------------------ |
| *stdlib*     | `*userbase*/lib/python`               |
| *platstdlib* | `*userbase*/lib/python`               |
| *platlib*    | `*userbase*/lib/python/site-packages` |
| *purelib*    | `*userbase*/lib/python/site-packages` |
| *include*    | `*userbase*/include/python*X.Y*`      |
| *scripts*    | `*userbase*/bin`                      |
| *data*       | `*userbase*`                          |



## 主方案

​	“主方案”背后的理念是你可以构建并维护个人的 Python 模块集。 该方案的名称源自 Unix 上“主目录”的概念，因为通常 Unix 用户会将其主目录的布局设置为与 `/usr/` 或 `/usr/local/` 相似。 任何人都可以使用该方案，无论其安装的操作系统是什么。

### `posix_home`

| Path          | 安装目录                |
| :------------ | :---------------------- |
| *stdlib*      | `*home*/lib/python`     |
| *platstdlib*  | `*home*/lib/python`     |
| *platlib*     | `*home*/lib/python`     |
| *purelib*     | `*home*/lib/python`     |
| *include*     | `*home*/include/python` |
| *platinclude* | `*home*/include/python` |
| *scripts*     | `*home*/bin`            |
| *data*        | `*home*`                |



## 前缀方案

​	“前缀方案”适用于当你希望使用一个 Python 安装程序来执行构建/安装（即运行 setup 脚本），但需要将模块安装到另一个 Python 安装版（或看起来类似于另一个 Python 安装版）的第三方模块目录中的情况。 如果这听起来有点不寻常，确实如此 --- 这就是为什么要先介绍用户和主目录方案的原因。 然而，至少有两种已知的情况会用到前缀方案。

​	首先，许多 Linux 发行版都会将 Python 放在 `/usr` 中，而不是传统的 `/usr/local` 中。 这是完全适当的，因为在这些情况下，Python 是“系统”的一部分而不是本地的附加组件。 但是，如果你从源代码安装 Python模块 ，您可能会想要将它们放在 `/usr/local/lib/python2.*X*` 而不是 `/usr/lib/python2.*X*` 中。

​	另一种可能性是在用于写入远程目录的名称与用于读取该目录的名称不同的网络文件系统：例如，作为 `/usr/local/bin/python` 访问的 Python 解释器可能会在 `/usr/local/lib/python2.*X*` 中搜索模块，但这些模块又必须安装到 `/mnt/*@server*/export/lib/python2.*X*` 这样的地方。

### `posix_prefix`

| Path          | 安装目录                                 |
| :------------ | :--------------------------------------- |
| *stdlib*      | `*prefix*/lib/python*X.Y*`               |
| *platstdlib*  | `*prefix*/lib/python*X.Y*`               |
| *platlib*     | `*prefix*/lib/python*X.Y*/site-packages` |
| *purelib*     | `*prefix*/lib/python*X.Y*/site-packages` |
| *include*     | `*prefix*/include/python*X.Y*`           |
| *platinclude* | `*prefix*/include/python*X.Y*`           |
| *scripts*     | `*prefix*/bin`                           |
| *data*        | `*prefix*`                               |

### `nt`

| Path          | 安装目录                     |
| :------------ | :--------------------------- |
| *stdlib*      | `*prefix*\Lib`               |
| *platstdlib*  | `*prefix*\Lib`               |
| *platlib*     | `*prefix*\Lib\site-packages` |
| *purelib*     | `*prefix*\Lib\site-packages` |
| *include*     | `*prefix*\Include`           |
| *platinclude* | `*prefix*\Include`           |
| *scripts*     | `*prefix*\Scripts`           |
| *data*        | `*prefix*`                   |

## 安装路径函数

[`sysconfig`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#module-sysconfig) 提供了一些函数来确定这些安装路径。

## sysconfig.**get_scheme_names**()

​	返回一个包含 [`sysconfig`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#module-sysconfig) 目前支持的所有方案的元组。

## sysconfig.**get_default_scheme**()

​	返回针对当前平台的默认方案的名称。

*Added in version 3.10:* 此函数之前被命名为 `_get_default_scheme()` 并被认为属性实现细节。

*在 3.11 版本发生变更:* 当 Python 运行于虚拟环境时，将返回 *venv* 方案。

## sysconfig.**get_preferred_scheme**(*key*)

​	返回针对由 *key* 所指定的安装布局的推荐方案的名称。

*key* 必须为 `"prefix"`, `"home"` 或 `"user"`。

​	该返回值是 [`get_scheme_names()`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#sysconfig.get_scheme_names) 中列出的一个方案名称。 它可以被传给接受 *scheme* 参数的 [`sysconfig`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#module-sysconfig) 函数，如 [`get_paths()`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#sysconfig.get_paths)。

*Added in version 3.10.*

*在 3.11 版本发生变更:* 当 Python 运行于虚拟环境且 `key="prefix"` 时，将返回 *venv* 方案。

## sysconfig.**_get_preferred_schemes**()

​	返回一个包含当前平台推荐的方案名称的字典。 Python 的实现方和再分发方可以将他们推荐的方案添加到 `_INSTALL_SCHEMES` 模块层级全局值，并修改此函数以返回这些方案名称，例如为各种系统和语言的包管理器提供不同的方案，这样它们各自安装的包就不会彼此混淆。

​	最终用户不应使用此函数，而应改用 [`get_default_scheme()`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#sysconfig.get_default_scheme) 和 [`get_preferred_scheme()`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#sysconfig.get_preferred_scheme)。

*Added in version 3.10.*

## sysconfig.**get_path_names**()

​	返回一个包含在 [`sysconfig`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#module-sysconfig) 中目前支持的所有路径名称的元组。

## sysconfig.**get_path**(*name*[, *scheme*[, *vars*[, *expand*]]])

​	返回一个对应于路径 *name*，来自名为 *scheme* 的安装方案的安装路径。

*name* 必须是一个来自 [`get_path_names()`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#sysconfig.get_path_names) 所返回的列表的值。

[`sysconfig`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#module-sysconfig) 会针对每个平台保存与每个路径名称相对应的安装路径，并带有可扩展的变量。 例如针对 *nt* 方案的 *stdlib* 路径是: `{base}/Lib`。

[`get_path()`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#sysconfig.get_path) 将使用 [`get_config_vars()`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#sysconfig.get_config_vars) 所返回的变量来扩展路径。 所有变量对于每种平台都有相应的默认值因此使用者可以调用此函数来获取默认值。

​	如果提供了 *scheme*，则它必须是一个来自 [`get_scheme_names()`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#sysconfig.get_scheme_names) 所返回的列表的值。 在其他情况下，将会使用针对当前平台的默认方案。

​	如果提供了 *vars*，则它必须是一个将要更新 [`get_config_vars()`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#sysconfig.get_config_vars) 所返回的字典的变量字典。

​	如果 *expand* 被设为 `False`，则将不使用这些变量来扩展路径。

​	如果 *name* 未找到，则会引发 [`KeyError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#KeyError)。

## sysconfig.**get_paths**([*scheme*[, *vars*[, *expand*]]])

​	返回一个包含与特定安装方案对应的安装路径的字典。 请参阅 [`get_path()`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#sysconfig.get_path) 了解详情。

​	如果未提供 *scheme*，则将使用针对当前平台的默认方案。

​	如果提供了 *vars*，则它必须是一个将要更新用于扩展的字典的变量字典。

​	如果 *expand* 被设为假值，则路径将不会被扩展。

​	如果 *scheme* 不是一个现有的方案，则 [`get_paths()`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#sysconfig.get_paths) 将引发 [`KeyError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#KeyError)。

## 其他功能

## sysconfig.**get_python_version**()

​	以字符串形式 `MAJOR.MINOR` 返回 Python 版本号。 类似于 `'%d.%d' % sys.version_info[:2]`。

## sysconfig.**get_platform**()

​	返回一个标识当前平台的字符串。

​	这主要被用来区分平台专属的构建目录和平台专属的构建分发版。 通常包括 OS 名称和版本以及架构（即 [`os.uname()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.uname) 所提供的信息），但是实际包括的信息取决于具体 OS；例如，在 Linux 上，内核版本并不是特别重要。

​	返回值的示例：

- linux-i586
- linux-alpha (?)
- solaris-2.6-sun4u

​	Windows将返回以下之一：

- win-amd64 (在 AMD64, aka x86_64, Intel64, 和 EM64T上的64位 Windows )
- win32（所有其他的 —— 确切地说，返回 sys.platform）

​	macOS 可以返回:

- macosx-10.6-ppc
- macosx-10.4-ppc64
- macosx-10.3-i386
- macosx-10.4-fat

​	对于其他非-POSIX 平台， 目前只是返回 [`sys.platform`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.platform) 。

## sysconfig.**is_python_build**()

​	如果正在运行的 Python 解释器是使用源代码构建的并在其构建位置上运行，而不是在其他位置例如通过运行 `make install` 或通过二进制机器码安装程序安装则返回 `True`。

## sysconfig.**parse_config_h**(*fp*[, *vars*])

​	解析一个 `config.h` 风格的文件。

*fp* 是一个指向 `config.h` 风格的文件的文件型对象。

​	返回一个包含名称/值对的字典。 如果传入一个可选的字典作为第二个参数，则将使用它而不是新的字典，并使用从文件中读取的值更新它。

## sysconfig.**get_config_h_filename**()

​	返回 `pyconfig.h` 的目录

## sysconfig.**get_makefile_filename**()

​	返回 `Makefile` 的目录



## 将 [`sysconfig`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#module-sysconfig) 作为脚本使用

​	You can use [`sysconfig`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#module-sysconfig) as a script with Python's *-m* option:

```
$ python -m sysconfig
Platform: "macosx-10.4-i386"
Python version: "3.2"
Current installation scheme: "posix_prefix"

Paths:
        data = "/usr/local"
        include = "/Users/tarek/Dev/svn.python.org/py3k/Include"
        platinclude = "."
        platlib = "/usr/local/lib/python3.2/site-packages"
        platstdlib = "/usr/local/lib/python3.2"
        purelib = "/usr/local/lib/python3.2/site-packages"
        scripts = "/usr/local/bin"
        stdlib = "/usr/local/lib/python3.2"

Variables:
        AC_APPLE_UNIVERSAL_BUILD = "0"
        AIX_GENUINE_CPLUSPLUS = "0"
        AR = "ar"
        ARFLAGS = "rc"
        ...
```

​	此调用将把 [`get_platform()`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#sysconfig.get_platform), [`get_python_version()`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#sysconfig.get_python_version), [`get_path()`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#sysconfig.get_path) 和 [`get_config_vars()`](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#sysconfig.get_config_vars) 所返回的信息打印至标准输出。
