+++
title = "importlib.resources -- 包资源的读取、打开和访问"
date = 2024-11-15T21:19:20+08:00
weight = 50
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/importlib.resources.html](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html)
>
> 收录该文档的时间：`2024-11-15T21:19:20+08:00`

# `importlib.resources` -- 包资源的读取、打开和访问

**源代码:** [Lib/importlib/resources/__init__.py](https://github.com/python/cpython/tree/3.13/Lib/importlib/resources/__init__.py)

------

> Added in version 3.7.
>

​	此模块调整了 Python 的导入系统以便提供对 *包* 内部的 *资源* 的访问。

​	“资源”是指 Python 中与模块或包相关联的文件类资源。 资源可以直接包含在某个包中，包含在某个包的子目录中，或是与某个包外部的模块相邻。 资源可以是文本或二进制数据。 因此，从技术上说 Python 包的模块源代码文件 (.py) 和编译结果文件 (pycache) 就是包实际所包含的资源。 但是，在实践中，资源主要是指包作者专门公开的非 Python 文件。

​	资源可以使用二进制或文本模式打开。

​	资源大致相当于目录内的文件，不过需要记住这只是一个比喻。 资源和包 **不是** 必须如文件系统上的物理文件和目录那样存在的：例如，一个包及其资源可使用 [`zipimport`](https://docs.python.org/zh-cn/3.13/library/zipimport.html#module-zipimport) 从一个 ZIP 文件导入。

​	备注

 

​	本模块提供了类似于 [pkg_resources](https://setuptools.readthedocs.io/en/latest/pkg_resources.html) [Basic Resource Access](https://setuptools.readthedocs.io/en/latest/pkg_resources.html#basic-resource-access) 的功能而没有那样高的性能开销。 这使得读取包中的资源更为容易，并具有更为稳定和一致的语义。

​	此模块的独立向下移植版本在 [using importlib.resources](https://importlib-resources.readthedocs.io/en/latest/using.html) 和 [migrating from pkg_resources to importlib.resources](https://importlib-resources.readthedocs.io/en/latest/migration.html) 中提供了更多信息。

​	想要支持资源读取的 [`加载器`](https://docs.python.org/zh-cn/3.13/library/importlib.html#importlib.abc.Loader) 应当实现 [`importlib.resources.abc.ResourceReader`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.abc.html#importlib.resources.abc.ResourceReader) 中规定的 `get_resource_reader(fullname)` 方法。

## *class* importlib.resources.**Anchor**

​	代表资源的锚点，可以是一个 [`模块对象`](https://docs.python.org/zh-cn/3.13/library/types.html#types.ModuleType) 或字符串形式的模块名称不。 定义为 `Union[str, ModuleType]`。

## importlib.resources.**files**(*anchor: [Anchor](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html#importlib.resources.Anchor) | [None](https://docs.python.org/zh-cn/3.13/library/constants.html#None) = None*)

​	返回一个代表资源容器（相当于目录）及其资源（相当于文件）的 [`Traversable`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.abc.html#importlib.resources.abc.Traversable) 对象。 Traversable 可以包含其他容器（相当于子目录）。

*anchor* 是一个可选的 [`Anchor`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html#importlib.resources.Anchor)。 如果 anchor 是一个包，则会从这个包获取资源。 如果是一个模块，则会从这个模块的相邻位置获取资源（在同一个包或包的根目录中）。 如果省略了 anchor,则会使用调用方的模块。

> Added in version 3.9.
>

*在 3.12 版本发生变更:* *package* 形参被重命名为 *anchor*。 *anchor* 现在可以是一个不为包的模块，如果被省略则默认为调用方的模块。 为保持兼容性 *package* 仍然被接受但会引发 [`DeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#DeprecationWarning)。 请考虑以位置参数方式传入或使用 `importlib_resources >= 5.10` 作为针对旧版 Python 的兼容接口。

## importlib.resources.**as_file**(*traversable*)

​	给定一个代表文件或目录的 [`Traversable`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.abc.html#importlib.resources.abc.Traversable) 对象，通常是来自 [`importlib.resources.files()`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html#importlib.resources.files)，返回一个上下文管理器以供 [`with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#with) 语句使用。 该上下文管理器提供一个 [`pathlib.Path`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.Path) 对象。

​	退出上下文管理器后会清除从 zip 文件等提取资源时创建的任何临时文件或目录。

​	当 Traversable 的方法（如 `read_text` 等）不足以满足需要而需要文件系统中的真实文件或目录时请使用 `as_file`。

> Added in version 3.9.
>

*在 3.12 版本发生变更:* 增加了对代表目录的 *traversable* 的支持。



## 函数式 API

​	提供了一套简化的、向下兼容的辅助工具。 这些工具使得常用操作只需一次函数调用即可完成。

​	对于以下所有函数：

- *anchor* 是一个 [`Anchor`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html#importlib.resources.Anchor)，就像在 [`files()`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html#importlib.resources.files) 中的一样。 与在 `files` 中不同，它不可被省略。

- *path_names* 是相对于 anchor 的资源的路径名的各个组成部分。 例如，要获取名为 `info.txt` 的资源的文本，则使用:

  ```
  importlib.resources.read_text(my_module, "info.txt")
  ```

  类似于 [`Traversable.joinpath`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.abc.html#importlib.resources.abc.Traversable)，单独的组成部分应当使用正斜杠 (`/`) 作为路径分隔符。 例如，下面这些条目是等价的:

  ```
  importlib.resources.read_binary(my_module, "pics/painting.png")
  importlib.resources.read_binary(my_module, "pics", "painting.png")
  ```

  出于保持向下兼容性的理由，如果给出了多个 *path_names* 则读取文本的函数需要一个显式的 *encoding* 参数。 例如，要获取 `info/chapter1.txt` 的文本，则使用:

  ```
  importlib.resources.read_text(my_module, "info", "chapter1.txt",
                                encoding='utf-8')
  ```

## importlib.resources.**open_binary**(*anchor*, **path_names*)

​	打开指定的资源用于二进制读取。

​	请参阅 [说明文档](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html#importlib-resources-functional) 了解 *anchor* 和 *path_names* 的详情。

​	此函数返回一个 [`BinaryIO`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.BinaryIO) 对象，即一个打开供读取的二进制流。

​	此函数大致等价于:

```
files(anchor).joinpath(*path_names).open('rb')
```

*在 3.13 版本发生变更:* 可接受多个 *path_names*。

## importlib.resources.**open_text**(*anchor*, **path_names*, *encoding='utf-8'*, *errors='strict'*)

​	打开指定的资源用于文本读取。 在默认情况下，将使用严格 UTF-8 编码读取内容。

​	请参阅 [说明文档](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html#importlib-resources-functional) 了解 *anchor* 和 *path_names* 的详情。 *encoding* 和 *errors* 的含义与在内置 [`open()`](https://docs.python.org/zh-cn/3.13/library/functions.html#open) 中的相同。

​	出于保持向下兼容性的理由，如果有多个 *path_names* 则需要显式地给出 *encoding* 参数。 此限制计划在 Python 3.15 中去除。

​	此函数返回一个 [`TextIO`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.TextIO) 对象，即一个打开供读取的文本流。

​	此函数大致等价于:

```
files(anchor).joinpath(*path_names).open('r', encoding=encoding)
```

*在 3.13 版本发生变更:* 可接受多个 *path_names*。 必须以关键字参数形式给出 *encoding* 和 *errors*。

## importlib.resources.**read_binary**(*anchor*, **path_names*)

​	以 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 形式读取并返回指定资源的内容。

​	请参阅 [说明文档](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html#importlib-resources-functional) 了解 *anchor* 和 *path_names* 的详情。

​	此函数大致等价于:

```
files(anchor).joinpath(*path_names).read_bytes()
```

*在 3.13 版本发生变更:* 可接受多个 *path_names*。

## importlib.resources.**read_text**(*anchor*, **path_names*, *encoding='utf-8'*, *errors='strict'*)

​	以 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 形式读取并返回指定资源的内容。 在默认情况下，将使用严格 UTF-8 编码读取内容。

​	请参阅 [说明文档](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html#importlib-resources-functional) 了解 *anchor* 和 *path_names* 的详情。 *encoding* 和 *errors* 的含义与在内置 [`open()`](https://docs.python.org/zh-cn/3.13/library/functions.html#open) 中的相同。

​	出于保持向下兼容性的理由，如果有多个 *path_names* 则需要显式地给出 *encoding* 参数。 此限制计划在 Python 3.15 中去除。

​	此函数大致等价于:

```
files(anchor).joinpath(*path_names).read_text(encoding=encoding)
```

*在 3.13 版本发生变更:* 可接受多个 *path_names*。 必须以关键字参数形式给出 *encoding* 和 *errors*。

## importlib.resources.**path**(*anchor*, **path_names*)

​	以实际文件系统路径的形式提供 *resource* 的路径。 此函数返回一个可在 [`with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#with) 语句中使用的上下文管理器。 该上下文管理器将提供一个 [`pathlib.Path`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.Path) 对象。

​	退出上下文管理器时将清理已创建的任何临时文件，例如在需要从 zip 文件提取资源时就将创建临时文件。

​	例如，[`stat()`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.Path.stat) 方法需要一个实际的文件系统路径；可以这样使用它:

```
with importlib.resources.path(anchor, "resource.txt") as fspath:
    result = fspath.stat()
```

​	请参阅 [说明文档](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html#importlib-resources-functional) 了解 *anchor* 和 *path_names* 的详情。

​	此函数大致等价于:

```
as_file(files(anchor).joinpath(*path_names))
```

*在 3.13 版本发生变更:* 可接受多个 *path_names*。 必须以关键字参数形式给出 *encoding* 和 *errors*。

## importlib.resources.**is_resource**(*anchor*, **path_names*)

​	如果指定的资源存在则返回 `True`，否则返回 `False`。 此函数不会考虑目录作为资源的情况。

​	请参阅 [说明文档](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html#importlib-resources-functional) 了解 *anchor* 和 *path_names* 的详情。

​	此函数大致等价于:

```
files(anchor).joinpath(*path_names).is_file()
```

*在 3.13 版本发生变更:* 可接受多个 *path_names*。

## importlib.resources.**contents**(*anchor*, **path_names*)

​	返回一个用于遍历指定的包或路径内条目的可迭代对象。 该可迭代对象将以 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 的形式返回资源（例如文件）和非资源（例如目录）。 该迭代器不会递归进入子目录。

​	请参阅 [说明文档](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html#importlib-resources-functional) 了解 *anchor* 和 *path_names* 的详情。

​	此函数大致等价于:

```
for resource in files(anchor).joinpath(*path_names).iterdir():
    yield resource.name
```

*自 3.11 版本弃用:* 首选上面的 `iterdir()`，它提供了对结果的更多控制和更丰富的功能。
