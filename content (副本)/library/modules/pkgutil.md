+++
title = "pkgutil --- 包扩展工具"
date = 2024-11-15T21:19:20+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/pkgutil.html](https://docs.python.org/zh-cn/3.13/library/pkgutil.html)
>
> 收录该文档的时间：`2024-11-15T21:19:20+08:00`

# `pkgutil` --- 包扩展工具

**源代码:** [Lib/pkgutil.py](https://github.com/python/cpython/tree/3.13/Lib/pkgutil.py)

------

​	该模块为导入系统提供了工具，尤其是在包支持方面。

## *class* pkgutil.**ModuleInfo**(*module_finder*, *name*, *ispkg*)

​	一个包含模块信息的简短摘要的命名元组。

> Added in version 3.6.
>

## pkgutil.**extend_path**(*path*, *name*)

​	扩展组成包的模块的搜索路径。 预期用途是将以下代码放到包的 `__init__.py` 中:

```
from pkgutil import extend_path
__path__ = extend_path(__path__, __name__)
```

​	对于For each directory on [`sys.path`]({{< ref "/library/python/sys#sys.path" >}}) 中每个具有与该包名称相匹配的子目录的目录，将该子目录添加到包的 [`__path__`]({{< ref "/reference/datamodel#module.__path__" >}})。 这在需要将单个逻辑包的不同部分拆分为多个目录的情况下很有用处。

​	它还会查找开头部分 `*` 匹配 *name* 参数的 `*.pkg` 文件。 此特性与 `*.pth` 文件类似（参阅 [`site`]({{< ref "/library/python/site#module-site" >}}) 模块了解更多信息），区别在于它不会区别对待以 `import` 开头的行。 `*.pkg` 文件将按外在值确定是否被信任：除了跳过空行和忽略注释，在 `*.pkg` 文件中找到的所有条目都会被添加到路径中，无论它们是否存在于文件系统中（这是个有意为之的特性）。

​	如果输入路径不是一个列表（已冻结包就是这种情况）则它将被原样返回。 输入路径不会被修改；将返回一个扩展的副本。 条目将被添加到副本的末尾。

[`sys.path`]({{< ref "/library/python/sys#sys.path" >}}) 会被假定为一个序列。 [`sys.path`]({{< ref "/library/python/sys#sys.path" >}}) 中的条目如果不是指向现有目录的字符串则会被忽略。 [`sys.path`]({{< ref "/library/python/sys#sys.path" >}}) 上当用作文件名时会导致错误的 Unicode 条目可以会使得此函数引发异常（与 [`os.path.isdir()`]({{< ref "/library/filesys/os_path#os.path.isdir" >}}) 的行为一致）。

## pkgutil.**find_loader**(*fullname*)

​	为给定的 *fullname* 获取一个模块 [loader]({{< ref "/glossary/idx#term-loader" >}})。

​	这是针对 [`importlib.util.find_spec()`]({{< ref "/library/modules/importlib#importlib.util.find_spec" >}}) 的向下兼容包装器，它将大多数失败转换为 [`ImportError`]({{< ref "/library/exceptions#ImportError" >}}) 并且只返回加载器而不是完整的 [`importlib.machinery.ModuleSpec`]({{< ref "/library/modules/importlib#importlib.machinery.ModuleSpec" >}})。

*在 3.3 版本发生变更:* 更新为直接基于 [`importlib`]({{< ref "/library/modules/importlib#module-importlib" >}}) 而不是依赖于包内部的 [**PEP 302**](https://peps.python.org/pep-0302/) 导入模拟。

*在 3.4 版本发生变更:* 更新为基于 [**PEP 451**](https://peps.python.org/pep-0451/)

*Deprecated since version 3.12, will be removed in version 3.14:* 使用 [`importlib.util.find_spec()`]({{< ref "/library/modules/importlib#importlib.util.find_spec" >}}) 来代替。

## pkgutil.**get_importer**(*path_item*)

​	为给定的 *path_item* 获取一个 [finder]({{< ref "/glossary/idx#term-finder" >}})。

​	返回的查找器如果是由一个路径钩子新建的则会被缓存至 [`sys.path_importer_cache`]({{< ref "/library/python/sys#sys.path_importer_cache" >}})。

​	如果需要重新扫描 [`sys.path_hooks`]({{< ref "/library/python/sys#sys.path_hooks" >}}) 则缓存（或其一部分）可以被手动清空。

*在 3.3 版本发生变更:* 更新为直接基于 [`importlib`]({{< ref "/library/modules/importlib#module-importlib" >}}) 而不是依赖于包内部的 [**PEP 302**](https://peps.python.org/pep-0302/) 导入模拟。

## pkgutil.**get_loader**(*module_or_name*)

​	为 *module_or_name* 获取一个 [loader]({{< ref "/glossary/idx#term-loader" >}})。

​	如果模块或包可通过正常导入机制来访问，则会返回该机制相关部分的包装器。 如果模块无法找到或导入则返回 `None`。 如果指定的模块尚未被导入，则包含它的包（如果存在）会被导入，以便建立包 `__path__`。

*在 3.3 版本发生变更:* 更新为直接基于 [`importlib`]({{< ref "/library/modules/importlib#module-importlib" >}}) 而不是依赖于包内部的 [**PEP 302**](https://peps.python.org/pep-0302/) 导入模拟。

*在 3.4 版本发生变更:* 更新为基于 [**PEP 451**](https://peps.python.org/pep-0451/)

*Deprecated since version 3.12, will be removed in version 3.14:* 使用 [`importlib.util.find_spec()`]({{< ref "/library/modules/importlib#importlib.util.find_spec" >}}) 来代替。

## pkgutil.**iter_importers**(*fullname=''*)

​	为给定的模块名称产生 [finder]({{< ref "/glossary/idx#term-finder" >}}) 对象。

​	如果完整名称包含一个 `'.'`，查找器将针对包含该完整名称的包，否则它们将被注册为最高层级查找器（即同时用于 [`sys.meta_path`]({{< ref "/library/python/sys#sys.meta_path" >}}) 和 [`sys.path_hooks`]({{< ref "/library/python/sys#sys.path_hooks" >}}))。

​	如果指定的模块位于一个包内，则该包会作为发起调用此函数的附带影响被导入。

​	如果未指定模块名称，则会产生所有的最高层级查找器。

*在 3.3 版本发生变更:* 更新为直接基于 [`importlib`]({{< ref "/library/modules/importlib#module-importlib" >}}) 而不是依赖于包内部的 [**PEP 302**](https://peps.python.org/pep-0302/) 导入模拟。

## pkgutil.**iter_modules**(*path=None*, *prefix=''*)

​	为 *path* 上的所有子模块产生 [`ModuleInfo`]({{< ref "/library/modules/pkgutil#pkgutil.ModuleInfo" >}})，或者如果 *path* 为 `None`，则为 [`sys.path`]({{< ref "/library/python/sys#sys.path" >}}) 上的所有最高层级模块产生。

*path* 应当为 `None` 或一个作为查找模块目标的路径的列表。

*prefix* 是要在输出时输出到每个模块名称之前的字符串。

​	备注

 

​	只适用于定义了 `iter_modules()` 方法的 [finder]({{< ref "/glossary/idx#term-finder" >}})。 该接口是非标准的，因此本模块还提供了针对 [`importlib.machinery.FileFinder`]({{< ref "/library/modules/importlib#importlib.machinery.FileFinder" >}}) 和 [`zipimport.zipimporter`]({{< ref "/library/modules/zipimport#zipimport.zipimporter" >}}) 的实现。

*在 3.3 版本发生变更:* 更新为直接基于 [`importlib`]({{< ref "/library/modules/importlib#module-importlib" >}}) 而不是依赖于包内部的 [**PEP 302**](https://peps.python.org/pep-0302/) 导入模拟。

## pkgutil.**walk_packages**(*path=None*, *prefix=''*, *onerror=None*)

​	在 *path* 上递归地为所有模块产生 [`ModuleInfo`]({{< ref "/library/modules/pkgutil#pkgutil.ModuleInfo" >}})，或者如果 *path* 为 `None`，则为所有可访问的模块产生。

*path* 应当为 `None` 或一个作为查找模块目标的路径的列表。

*prefix* 是要在输出时输出到每个模块名称之前的字符串。

​	请注意此函数必须导入给定 *path* 上所有的 *packages* (*而不是* 所有的模块！)，以便能访问 `__path__` 属性来查找子模块。

*onerror* 是在当试图导入包如果发生任何异常则将附带一个参数（被导入的包的名称）被调用的函数。 如果没有提供 *onerror* 函数，则 [`ImportError`]({{< ref "/library/exceptions#ImportError" >}}) 会被捕获并被忽略，而其他异常则会被传播，导致模块搜索的终结。

​	示例:

```
# 列出 python 可访问的所有模块
walk_packages()

# 列出 ctypes 的所有子模块
walk_packages(ctypes.__path__, ctypes.__name__ + '.')
```

​	备注

 

​	只适用于定义了 `iter_modules()` 方法的 [finder]({{< ref "/glossary/idx#term-finder" >}})。 该接口是非标准的，因此本模块还提供了针对 [`importlib.machinery.FileFinder`]({{< ref "/library/modules/importlib#importlib.machinery.FileFinder" >}}) 和 [`zipimport.zipimporter`]({{< ref "/library/modules/zipimport#zipimport.zipimporter" >}}) 的实现。

*在 3.3 版本发生变更:* 更新为直接基于 [`importlib`]({{< ref "/library/modules/importlib#module-importlib" >}}) 而不是依赖于包内部的 [**PEP 302**](https://peps.python.org/pep-0302/) 导入模拟。

## pkgutil.**get_data**(*package*, *resource*)

​	从包中获取一个资源。

​	这是一个针对 [loader]({{< ref "/glossary/idx#term-loader" >}}) [`get_data`]({{< ref "/library/modules/importlib#importlib.abc.ResourceLoader.get_data" >}}) API 的包装器。 *package* 参数应为一个标准模块格式 (`foo.bar`) 的包名称。 *resource* 参数应为相对路径文件名的形式，使用 `/` 作为路径分隔符。 父目录名 `..`，以及根目录名 (以 `/` 打头) 均不允许使用。

​	返回指定资源内容的二进制串。

​	对于位于文件系统中，已经被导入的包来说，这大致等价于:

```
d = os.path.dirname(sys.modules[package].__file__)
data = open(os.path.join(d, resource), 'rb').read()
```

​	如果指定的包无法被定位或加载，或者如果它使用了不支持 [`get_data`]({{< ref "/library/modules/importlib#importlib.abc.ResourceLoader.get_data" >}}) 的 [loader]({{< ref "/glossary/idx#term-loader" >}})，则将返回 `None`。 特别地，针对 [命名空间包]({{< ref "/glossary/idx#term-namespace-package" >}}) 的 [loader]({{< ref "/glossary/idx#term-loader" >}}) 不支持 [`get_data`]({{< ref "/library/modules/importlib#importlib.abc.ResourceLoader.get_data" >}})。

## pkgutil.**resolve_name**(*name*)

​	将一个名称解析为对象。

​	此功能被用在标准库的许多地方 (参见 [bpo-12915](https://bugs.python.org/issue?@action=redirect&bpo=12915)) —— 并且等价的功能也被广泛用于第三方包例如 setuptools, Django 和 Pyramid。

​	预期 *name* 将为以下格式之一，其中 W 是一个有效的 Python 标识符的缩写而点号表示这些伪正则表达式中的句点字面值:

- `W(.W)*`
- `W(.W)*:(W(.W)*)?`

​	第一种形式只是为了保持向下兼容性。 它假定带点号名称的某一部分是包，而其余部分则是该包内部的一个对象，并可能嵌套在其他对象之内。 因为包和对象层级结构之间的分界点无法通过观察来确定，所以使用这种形式必须重复尝试导入。

​	在第二种形式中，调用方通过提供一个单独冒号来明确分界点：冒号左边的带点号名称是要导入的包，而冒号右边的带点号名称则是对象层级结构。 使用这种形式只需要导入一次。 如果它以冒号结尾，则将返回一个模块对象。

​	此函数将返回一个对象（可能为模块），或是引发下列异常之一:

[`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) -- 如果 *name* 不为可识别的格式。

[`ImportError`]({{< ref "/library/exceptions#ImportError" >}}) -- 如果导入本应成功但却失败。

[`AttributeError`]({{< ref "/library/exceptions#AttributeError" >}}) -- 当在遍历所导入包的对象层级结构以获取想要的对象时遭遇失败。

> Added in version 3.9.
>
