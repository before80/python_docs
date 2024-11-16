+++
title = "importlib.metadata -- 访问软件包元数据"
date = 2024-11-15T21:19:20+08:00
weight = 70
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/importlib.metadata.html](https://docs.python.org/zh-cn/3.13/library/importlib.metadata.html)
>
> 收录该文档的时间：`2024-11-15T21:19:20+08:00`

# `importlib.metadata` -- 访问软件包元数据

> Added in version 3.8.
>

*在 3.10 版本发生变更:* `importlib.metadata` 不再是暂定的。

**源代码:** [Lib/importlib/metadata/__init__.py](https://github.com/python/cpython/tree/3.13/Lib/importlib/metadata/__init__.py)

`importlib.metadata` 是一个提供对已安装的 [分发包](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package) 的元数据的访问的库，如其入口点或其顶层名称 ([导入包](https://packaging.python.org/en/latest/glossary/#term-Import-Package), 模块等，如果存在的话）。 这个库部分构建于 Python 的导入系统之上，其目标是取代 `pkg_resources` 的中的 [entry point API](https://setuptools.readthedocs.io/en/latest/pkg_resources.html#entry-points) 和 [metadata API](https://setuptools.readthedocs.io/en/latest/pkg_resources.html#metadata-api)。 配合 [`importlib.resources`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html#module-importlib.resources) ，这个包使得较老旧且低效的 `pkg_resources` 包不再必要。

`importlib.metadata` 对 [pip](https://pypi.org/project/pip/) 等工具安装到 Python 的 `site-packages` 目录的第三方 *分发包* 进行操作。 具体来说，适用的分发包应带有可发现的 `dist-info` 或 `egg-info` 目录，以及 [核心元数据规范说明](https://packaging.python.org/en/latest/specifications/core-metadata/#core-metadata) 定义的元数据。

​	重要

 

​	它们 *不一定* 等同或 1:1 对应于可在 Python 代码中导入的顶层 *导入包* 名称。一个 *分发包* 可以包含多个 *导入包* (和单个模块)，如果是命名空间包，一个顶层 *导入包* 可以映射到多个 *分发包*。您可以使用 [packages_distributions()](https://docs.python.org/zh-cn/3.13/library/importlib.metadata.html#package-distributions) 来获取它们之间的映射。

​	分发包元数据默认可存在于 [`sys.path`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.path) 下的文件系统或 zip 归档文件中。通过一个扩展机制，元数据可以存在于几乎任何地方。

​	参见

## https://importlib-metadata.readthedocs.io/

`importlib_metadata` 的文档，它向下移植了 `importlib.metadata`。它包含该模块的类和函数的 [API 参考](https://importlib-metadata.readthedocs.io/en/latest/api.html)，以及针对 `pkg_resources` 现有用户的 [迁移指南](https://importlib-metadata.readthedocs.io/en/latest/migration.html)。

## 概述

​	让我们假设你想要获取你使用 `pip` 安装的某个 [分发包](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package) 的版本字符串。我们首先创建一个虚拟环境并在其中安装一些软件包：

```
$ python -m venv example
$ source example/bin/activate
(example) $ python -m pip install wheel
```

​	你可以通过运行以下代码得到 `wheel` 的版本字符串：



```
(example) $ python
>>> from importlib.metadata import version  
>>> version('wheel')  
'0.32.3'
```

​	你还能得到可通过 EntryPoint 的属性 (通常为 'group' 或 'name') 来选择的入口点多项集，比如 `console_scripts`, `distutils.commands`。 每个 group 包含一个由 [EntryPoint](https://docs.python.org/zh-cn/3.13/library/importlib.metadata.html#entry-points) 对象组成的多项集。

​	你可以获得 [分发的元数据](https://docs.python.org/zh-cn/3.13/library/importlib.metadata.html#metadata)：



``` python
>>> list(metadata('wheel'))  
['Metadata-Version', 'Name', 'Version', 'Summary', 'Home-page', 'Author', 'Author-email', 'Maintainer', 'Maintainer-email', 'License', 'Project-URL', 'Project-URL', 'Project-URL', 'Keywords', 'Platform', 'Classifier', 'Classifier', 'Classifier', 'Classifier', 'Classifier', 'Classifier', 'Classifier', 'Classifier', 'Classifier', 'Classifier', 'Classifier', 'Classifier', 'Requires-Python', 'Provides-Extra', 'Requires-Dist', 'Requires-Dist']
```

​	你也可以获得 [分发包的版本号](https://docs.python.org/zh-cn/3.13/library/importlib.metadata.html#version)，列出它的 [构成文件](https://docs.python.org/zh-cn/3.13/library/importlib.metadata.html#files)，并且得到分发包的 [分发包的依赖](https://docs.python.org/zh-cn/3.13/library/importlib.metadata.html#requirements) 列表。

## *exception* importlib.metadata.**PackageNotFoundError**

​	Subclass of [`ModuleNotFoundError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ModuleNotFoundError) raised by several functions in this module when queried for a distribution package which is not installed in the current Python environment.

## 函数式 API

​	这个包的公开 API 提供了以下功能。



### 入口点

## importlib.metadata.**entry_points**(***select_params*)

​	Returns a [`EntryPoints`](https://docs.python.org/zh-cn/3.13/library/importlib.metadata.html#importlib.metadata.EntryPoints) instance describing entry points for the current environment. Any given keyword parameters are passed to the `select()` method for comparison to the attributes of the individual entry point definitions.

​	Note: it is not currently possible to query for entry points based on their `EntryPoint.dist` attribute (as different `Distribution` instances do not currently compare equal, even if they have the same attributes)

## *class* importlib.metadata.**EntryPoints**

​	Details of a collection of installed entry points.

​	Also provides a `.groups` attribute that reports all identifed entry point groups, and a `.names` attribute that reports all identified entry point names.

## *class* importlib.metadata.**EntryPoint**

​	Details of an installed entry point.

​	Each `EntryPoint` instance has `.name`, `.group`, and `.value` attributes and a `.load()` method to resolve the value. There are also `.module`, `.attr`, and `.extras` attributes for getting the components of the `.value` attribute, and `.dist` for obtaining information regarding the distribution package that provides the entry point.

​	查询所有的入口点：



``` python
>>> eps = entry_points()  
```

`entry_points()` 函数返回一个 `EntryPoints` 对象，即由带 `names` 和 `groups` 属性的所有 `EntryPoint` 对象组成的多项集以方便使用:



``` python
>>> sorted(eps.groups)  
['console_scripts', 'distutils.commands', 'distutils.setup_keywords', 'egg_info.writers', 'setuptools.installation']
```

`EntryPoints` 具有 `select()` 方法用于选择匹配指定特征属性的入口点。 如选择 `console_scripts` 组中的入口点:



``` python
>>> scripts = eps.select(group='console_scripts')  
```

​	效果相同，因为 `entry_points()` 会传递关键字参数来选择:



``` python
>>> scripts = entry_points(group='console_scripts')  
```

​	选出命名为 “wheel” 的特定脚本（可以在 wheel 项目中找到）：



``` python
>>> 'wheel' in scripts.names  
True
>>> wheel = scripts['wheel']  
```

​	等价地，在选择过程中查询对应的入口点：



``` python
>>> (wheel,) = entry_points(group='console_scripts', name='wheel')  
>>> (wheel,) = entry_points().select(group='console_scripts', name='wheel')  
```

​	检查解析得到的入口点：



``` python
>>> wheel  
EntryPoint(name='wheel', value='wheel.cli:main', group='console_scripts')
>>> wheel.module  
'wheel.cli'
>>> wheel.attr  
'main'
>>> wheel.extras  
[]
>>> main = wheel.load()  
>>> main  
<function main at 0x103528488>
```

`group` 和 `name` 是由包作者定义的任意值并且通常来说客户端会想要解析特定 group 的所有入口点。 请参阅 [the setuptools docs](https://setuptools.pypa.io/en/latest/userguide/entry_point.html) 了解有关入口点，其定义和用法的更多信息。

*在 3.12 版本发生变更:* "selectable" 入口点是在 `importlib_metadata` 3.6 和 Python 3.10 中引入的。 在这项改变之前，`entry_points` 不接受任何形参并且总是返回一个由入口点组成的字典，字典的键为分组名。 在 `importlib_metadata` 5.0 和 Python 3.12 中，`entry_points` 总是返回一个 `EntryPoints` 对象。 请参阅 [backports.entry_points_selectable](https://pypi.org/project/backports.entry_points_selectable/) 了解相关兼容性选项。

*在 3.13 版本发生变更:* `EntryPoint` 对象不再提供类似于元组的接口（[`__getitem__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__getitem__)）。



### 分发的元数据

## importlib.metadata.**metadata**(*distribution_name*)

​	Return the distribution metadata corresponding to the named distribution package as a [`PackageMetadata`](https://docs.python.org/zh-cn/3.13/library/importlib.metadata.html#importlib.metadata.PackageMetadata) instance.

​	Raises [`PackageNotFoundError`](https://docs.python.org/zh-cn/3.13/library/importlib.metadata.html#importlib.metadata.PackageNotFoundError) if the named distribution package is not installed in the current Python environment.

## *class* importlib.metadata.**PackageMetadata**

​	A concrete implementation of the [PackageMetadata protocol](https://importlib-metadata.readthedocs.io/en/latest/api.html#importlib_metadata.PackageMetadata).

​	In addition to providing the defined protocol methods and attributes, subscripting the instance is equivalent to calling the `get()` method.

​	每个 [分发包](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package) 都包括一些元数据，你可以使用 `metadata()` 函数来获取:



``` python
>>> wheel_metadata = metadata('wheel')  
```

​	The keys of the returned data structure name the metadata keywords, and the values are returned unparsed from the distribution metadata:



``` python
>>> wheel_metadata['Requires-Python']  
'>=2.7, !=3.0.*, !=3.1.*, !=3.2.*, !=3.3.*'
```

[`PackageMetadata`](https://docs.python.org/zh-cn/3.13/library/importlib.metadata.html#importlib.metadata.PackageMetadata) also presents a `json` attribute that returns all the metadata in a JSON-compatible form per [**PEP 566**](https://peps.python.org/pep-0566/):



``` python
>>> wheel_metadata.json['requires_python']
'>=2.7, !=3.0.*, !=3.1.*, !=3.2.*, !=3.3.*'
```

​	The full set of available metadata is not described here. See the PyPA [Core metadata specification](https://packaging.python.org/en/latest/specifications/core-metadata/#core-metadata) for additional details.

*在 3.10 版本发生变更:* 当有效载荷中包含时，`Description` 以去除续行符的形式被包含于元数据中。

​	添加了 `json` 属性。



### 分发包的版本

## importlib.metadata.**version**(*distribution_name*)

​	返回指定分发包的已安装分发包版本。

​	Raises [`PackageNotFoundError`](https://docs.python.org/zh-cn/3.13/library/importlib.metadata.html#importlib.metadata.PackageNotFoundError) if the named distribution package is not installed in the current Python environment.

`version()` 函数是获取字符串形式的 [分发包](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package) 版本号的最快速方式:



``` python
>>> version('wheel')  
'0.32.3'
```



### 分发包的文件

## importlib.metadata.**files**(*distribution_name*)

​	返回包含在指定分发包内的完整文件集合。

​	Raises [`PackageNotFoundError`](https://docs.python.org/zh-cn/3.13/library/importlib.metadata.html#importlib.metadata.PackageNotFoundError) if the named distribution package is not installed in the current Python environment.

​	如果找到了分发包但未找到报告与分发包相关联的文件的安装数据库记录则返回 [`None`](https://docs.python.org/zh-cn/3.13/library/constants.html#None)。

## *class* importlib.metadata.**PackagePath**

​	A [`pathlib.PurePath`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.PurePath) derived object with additional `dist`, `size`, and `hash` properties corresponding to the distribution package's installation metadata for that file.

​	The `files()` function takes a [Distribution Package](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package) name and returns all of the files installed by this distribution. Each file is reported as a [`PackagePath`](https://docs.python.org/zh-cn/3.13/library/importlib.metadata.html#importlib.metadata.PackagePath) instance. For example:



``` python
>>> util = [p for p in files('wheel') if 'util.py' in str(p)][0]  
>>> util  
PackagePath('wheel/util.py')
>>> util.size  
859
>>> util.dist  
<importlib.metadata._hooks.PathDistribution object at 0x101e0cef0>
>>> util.hash  
<FileHash mode: sha256 value: bYkw5oMccfazVCoYQwKkkemoVyMAFoR34mmKBx8R1NI>
```

​	当你获得了文件对象，你可以读取其内容：



``` python
>>> print(util.read_text())  
import base64
import sys
...
def as_bytes(s):
    if isinstance(s, text_type):
        return s.encode('utf-8')
    return s
```

​	You can also use the `locate()` method to get the absolute path to the file:



``` python
>>> util.locate()  
PosixPath('/home/gustav/example/lib/site-packages/wheel/util.py')
```

​	In the case where the metadata file listing files (`RECORD` or `SOURCES.txt`) is missing, `files()` will return [`None`](https://docs.python.org/zh-cn/3.13/library/constants.html#None). The caller may wish to wrap calls to `files()` in [always_iterable](https://more-itertools.readthedocs.io/en/stable/api.html#more_itertools.always_iterable) or otherwise guard against this condition if the target distribution is not known to have the metadata present.



### 分发包的依赖

## importlib.metadata.**requires**(*distribution_name*)

​	Return the declared dependency specifiers for the named distribution package.

​	Raises [`PackageNotFoundError`](https://docs.python.org/zh-cn/3.13/library/importlib.metadata.html#importlib.metadata.PackageNotFoundError) if the named distribution package is not installed in the current Python environment.

​	To get the full set of requirements for a [Distribution Package](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package), use the `requires()` function:



``` python
>>> requires('wheel')  
["pytest (>=3.0.0) ; extra == 'test'", "pytest-cov ; extra == 'test'"]
```



### 将导入映射到分发包

## importlib.metadata.**packages_distributions**()

​	Return a mapping from the top level module and import package names found via [`sys.meta_path`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.meta_path) to the names of the distribution packages (if any) that provide the corresponding files.

​	To allow for namespace packages (which may have members provided by multiple distribution packages), each top level import name maps to a list of distribution names rather than mapping directly to a single name.

​	解析每个提供可导入的最高层级 Python 模块或 [导入包](https://packaging.python.org/en/latest/glossary/#term-Import-Package) 对应的 [分发包](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package) 名称（对于命名空间包可能有多个名称）的快捷方法:



``` python
>>> packages_distributions()
{'importlib_metadata': ['importlib-metadata'], 'yaml': ['PyYAML'], 'jaraco': ['jaraco.classes', 'jaraco.functools'], ...}
```

​	某些可编辑的安装 [没有提供最高层级名称](https://github.com/pypa/packaging-problems/issues/609)，因而此函数不适用于这样的安装。

> Added in version 3.10.
>



## 分发包对象

## importlib.metadata.**distribution**(*distribution_name*)

​	Return a [`Distribution`](https://docs.python.org/zh-cn/3.13/library/importlib.metadata.html#importlib.metadata.Distribution) instance describing the named distribution package.

​	Raises [`PackageNotFoundError`](https://docs.python.org/zh-cn/3.13/library/importlib.metadata.html#importlib.metadata.PackageNotFoundError) if the named distribution package is not installed in the current Python environment.

## *class* importlib.metadata.**Distribution**

​	Details of an installed distribution package.

​	Note: different `Distribution` instances do not currently compare equal, even if they relate to the same installed distribution and accordingly have the same attributes.

​	While the module level API described above is the most common and convenient usage, you can get all of that information from the `Distribution` class. `Distribution` is an abstract object that represents the metadata for a Python [Distribution Package](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package). You can get the concreate `Distribution` subclass instance for an installed distribution package by calling the [`distribution()`](https://docs.python.org/zh-cn/3.13/library/importlib.metadata.html#importlib.metadata.distribution) function:



``` python
>>> from importlib.metadata import distribution  
>>> dist = distribution('wheel')  
>>> type(dist)  
<class 'importlib.metadata.PathDistribution'>
```

​	Thus, an alternative way to get the version number is through the `Distribution` instance:



``` python
>>> dist.version  
'0.32.3'
```

​	There are all kinds of additional metadata available on `Distribution` instances:



``` python
>>> dist.metadata['Requires-Python']  
'>=2.7, !=3.0.*, !=3.1.*, !=3.2.*, !=3.3.*'
>>> dist.metadata['License']  
'MIT'
```

​	对于可编辑包，`origin` 属性可能表示 [**PEP 610**](https://peps.python.org/pep-0610/) 元数据：



``` python
>>> dist.origin.url
'file:///path/to/wheel-0.32.3.editable-py3-none-any.whl'
```

​	The full set of available metadata is not described here. See the PyPA [Core metadata specification](https://packaging.python.org/en/latest/specifications/core-metadata/#core-metadata) for additional details.

> Added in version 3.13:
> 增加了 `.origin` 特征属性。

## 分发包的发现

​	在默认情况下，这个包针对文件系统和 zip 文件 [分发包](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package) 的元数据发现提供了内置支持。 这个元数据查找器的搜索目标默认为 `sys.path`，但它对来自其他导入机制行为方式的解读会略有变化。 特别地:

- `importlib.metadata` 不会识别 `sys.path` 上的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 对象。
- `importlib.metadata` 将顺带识别 `sys.path` 上的 [`pathlib.Path`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.Path) 对象，即使这些值会被导入操作所忽略。

## 扩展搜索算法

​	因为 [分发包](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package) 元数据不能通过 [`sys.path`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.path) 搜索，或是通过包加载器直接获得，一个分发包的元数据是通过导入系统的 [查找器](https://docs.python.org/zh-cn/3.13/reference/import.html#finders-and-loaders) 找到的。 要找到分发包的元数据，`importlib.metadata` 将在 [`sys.meta_path`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.meta_path) 上查询 [元路径查找器](https://docs.python.org/zh-cn/3.13/glossary.html#term-meta-path-finder) 的列表。

​	在默认情况下 `importlib.metadata` 会安装在文件系统中找到的分发包的查找器。 这个查找器无法真正找出任何 *分发包*，但它能找到它们的元数据。

​	抽象基类 [`importlib.abc.MetaPathFinder`](https://docs.python.org/zh-cn/3.13/library/importlib.html#importlib.abc.MetaPathFinder) 定义了 Python 导入系统期望的查找器接口。 `importlib.metadata` 通过寻找 [`sys.meta_path`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.meta_path) 上查找器可选的 `find_distributions` 可调用的属性扩展这个协议，并将这个扩展接口作为 `DistributionFinder` 抽象基类提供，它定义了这个抽象方法：

```
@abc.abstractmethod
def find_distributions(context=DistributionFinder.Context()):
    """返回一个由所有能够为指定 ``context`` 加载包的元数据的
    Distribution 实例组成的可迭代对象。
    """
```

`DistributionFinder.Context` 对象提供了指示搜索路径和匹配名称的属性 `.path` 和 `.name` ，也可能提供其他相关的上下文。

​	这在实践中意味着要支持在文件系统外的其他位置查找分发包的元数据，你需要子类化 `Distribution` 并实现抽象方法，之后从一个自定义查找器的 `find_distributions()` 方法返回这个派生的 `Distribution` 实例。

### 示例

​	例如，考虑一个从数据库中加载 Python 模块的自定义查找器：

```
class DatabaseImporter(importlib.abc.MetaPathFinder):
    def __init__(self, db):
        self.db = db

    def find_spec(self, fullname, target=None) -> ModuleSpec:
        return self.db.spec_from_name(fullname)

sys.meta_path.append(DatabaseImporter(connect_db(...)))
```

​	该导入器现在大概可以从数据库中导入模块，但它不提供元数据或入口点。这个自定义导入器如果要提供元数据，它还需要实现 `DistributionFinder`：

```
from importlib.metadata import DistributionFinder

class DatabaseImporter(DistributionFinder):
    ...

    def find_distributions(self, context=DistributionFinder.Context()):
        query = dict(name=context.name) if context.name else {}
        for dist_record in self.db.query_distributions(query):
            yield DatabaseDistribution(dist_record)
```

​	这样一来，`query_distributions` 就会返回数据库中与查询匹配的每个分发包的记录。例如，如果数据库中有 `requests-1.0`，`find_distributions` 就会为 `Context(name='requests')` 或``Context(name=None)`` 产生 `DatabaseDistribution`。

​	为简单起见，本例忽略了 `context.path`。`path` 属性默认为 `sys.path`，是搜索中考虑的导入路径集。一个 `DatabaseImporter` 可以在不考虑搜索路径的情况下运作。假设导入器不进行分区，那么 "path" 就无关紧要了。为了说明 `path` 的作用，示例需要展示一个更复杂的 `DatabaseImporter`，它的行为随 `sys.path`/`PYTHONPATH` 而变化。在这种情况下，`find_distributions` 应该尊重 `context.path`，并且只产生与该路径相关的 `Distribution`。

​	那么，`DatabaseDistribution` 看起来就像是这样：

```
class DatabaseDistribution(importlib.metadata.Distribution):
    def __init__(self, record):
        self.record = record

    def read_text(self, filename):
        """
        Read a file like "METADATA" for the current distribution.
        """
        if filename == "METADATA":
            return f"""Name: {self.record.name}
Version: {self.record.version}
"""
        if filename == "entry_points.txt":
            return "\n".join(
              f"""[{ep.group}]\n{ep.name}={ep.value}"""
              for ep in self.record.entry_points)

    def locate_file(self, path):
        raise RuntimeError("This distribution has no file system")
```

​	这个基本实现应当为由 `DatabaseImporter` 进行服务的包提供元数据和入口点，假定 `record` 提供了适当的 `.name`, `.version` 和 `.entry_points` 属性。

`DatabaseDistribution` 还可能提供其他元数据文件，比如 `RECORD` (对 `Distribution.files` 来说需要) 或重写 `Distribution.files` 的实现。 请参看源代码深入了解。
