+++
title = "importlib.resources.abc -- 资源的抽象基类"
date = 2024-11-15T21:19:20+08:00
weight = 60
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/importlib.resources.abc.html](https://docs.python.org/zh-cn/3.13/library/importlib.resources.abc.html)
>
> 收录该文档的时间：`2024-11-15T21:19:20+08:00`

# `importlib.resources.abc` -- 资源的抽象基类

**源代码:** [Lib/importlib/resources/abc.py](https://github.com/python/cpython/tree/3.13/Lib/importlib/resources/abc.py)

------

> Added in version 3.11.
>

## *class* importlib.resources.abc.**ResourceReader**

*被 TraversableResources* 取代

​	提供读取 *resources* 能力的一个 [abstract base class](https://docs.python.org/zh-cn/3.13/glossary.html#term-abstract-base-class) 。

​	从这个 ABC 的视角出发，*resource* 指一个包附带的二进制文件。常见的如在包的 `__init__.py` 文件旁的数据文件。这个类存在的目的是为了将对数据文件的访问进行抽象，这样包就和其数据文件的存储方式无关了。不论这些文件是存放在一个 zip 文件里还是直接在文件系统内。

​	对于该类中的任一方法，*resource* 参数的值都需要是一个在概念上表示文件名称的 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。 这意味着任何子目录的路径都不该出现在 *resouce* 参数值内。 因为对于阅读器而言，包的位置就代表着「目录」。 因此目录和文件名就分别对应于包和资源。 这也是该类的实例都需要和一个包直接关联（而不是潜在指代很多包或者一整个模块）的原因。

​	想支持资源读取的加载器需要提供一个返回实现了此 ABC 的接口的 `get_resource_reader(fullname)` 方法。如果通过全名指定的模块不是一个包，这个方法应该返回 [`None`](https://docs.python.org/zh-cn/3.13/library/constants.html#None)。 当指定的模块是一个包时，应该只返回一个与这个抽象类ABC兼容的对象。

*Deprecated since version 3.12, will be removed in version 3.14:* 使用 [`importlib.resources.abc.TraversableResources`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.abc.html#importlib.resources.abc.TraversableResources) 代替。

## *abstractmethod* **open_resource**(*resource*)

​	返回一个打开的 [file-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-like-object) 用于 *resource* 的二进制读取。

​	如果无法找到资源，将会引发 [`FileNotFoundError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#FileNotFoundError)。

## *abstractmethod* **resource_path**(*resource*)

​	返回 *resource* 的文件系统路径。

​	如果资源并不实际存在于文件系统中，将会引发 [`FileNotFoundError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#FileNotFoundError)。

## *abstractmethod* **is_resource**(*name*)

​	如果 *name* 被视作资源，则返回True。如果 *name* 不存在，则引发 [`FileNotFoundError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#FileNotFoundError) 异常。

## *abstractmethod* **contents**()

​	反回由字符串组成的 [iterable](https://docs.python.org/zh-cn/3.13/glossary.html#term-iterable)，表示这个包的所有内容。 请注意并不要求迭代器返回的所有名称都是实际的资源，例如返回 [`is_resource()`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.abc.html#importlib.resources.abc.ResourceReader.is_resource) 为假值的名称也是可接受的。

​	允许非资源名字被返回是为了允许存储的一个包和它的资源的方式是已知先验的并且非资源名字会有用的情况。比如，允许返回子目录名字，目的是当得知包和资源存储在文件系统上面的时候，能够直接使用子目录的名字。

​	这个抽象方法返回了一个不包含任何内容的可迭代对象。

## *class* importlib.resources.abc.**Traversable**

​	一个具有 [`pathlib.Path`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.Path) 中方法的子集并适用于遍历目录和打开文件的对象。

​	对于该对象在文件系统中的表示形式，请使用 [`importlib.resources.as_file()`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html#importlib.resources.as_file)。

## **name**

​	抽象属性。 此对象的不带任何父引用的基本名称。

## *abstractmethod* **iterdir**()

​	产出自身内部的可遍历对象。

## *abstractmethod* **is_dir**()

​	如果 self 是一个目录则返回 `True`。

## *abstractmethod* **is_file**()

​	如果 self 是一个文件则返回 `True`。

## *abstractmethod* **joinpath**(**pathsegments*)

​	按照 *pathsegments* 遍历目录并以 `Traversable` 形式返回结果。

​	每个 *pathsegments* 参数可能包含以正斜杠 (`/`, `posixpath.sep` ) 分隔的多个名称。 例如，以下值是等价的:

```
files.joinpath('subdir', 'subsuddir', 'file.txt')
files.joinpath('subdir/subsuddir/file.txt')
```

​	请注意某些 `Traversable` 实现可能没有升级到最新版本的协议。 要与这样的实现保持兼容，可以向每个对 `joinpath` 的调用提供提供单个不带路径分隔符的参数。 例如:

```
files.joinpath('subdir').joinpath('subsubdir').joinpath('file.txt')
```

*在 3.11 版本发生变更:* `joinpath` 接受多个 *pathsegments*，这些部分可以包含正斜杠作为路径分隔符。 在之前版本中，只接受单个 *child* 参数。

## *abstractmethod* **__truediv__**(*child*)

​	返回可遍历的子对象自身。 等价于 `joinpath(child)`。

## *abstractmethod* **open**(*mode='r'*, **args*, ***kwargs*)

*mode* 可以为 'r' 或 'rb' 即以文本或二进制模式打开。 返回一个适用于读取的句柄（与 [`pathlib.Path.open`](https://docs.python.org/zh-cn/3.13/library/pathlib.html#pathlib.Path.open) 样同）。

​	当以文件模式打开时，接受与 [`io.TextIOWrapper`](https://docs.python.org/zh-cn/3.13/library/io.html#io.TextIOWrapper) 所接受的相同编码格式形参。

## **read_bytes**()

​	以字节串形式读取自身的内容。

## **read_text**(*encoding=None*)

​	以文本形式读取自身的内容。

## *class* importlib.resources.abc.**TraversableResources**

​	针对能够为 [`importlib.resources.files()`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.html#importlib.resources.files) 接口提供服务的资源读取器的抽象基类。 子类化 [`ResourceReader`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.abc.html#importlib.resources.abc.ResourceReader) 并为 `ResourceReader` 的抽象方法提供具体实现。 因此 ，任何提供了 `TraversableResources` 的加载器也会提供 `ResourceReader`。

​	需要支持资源读取的加载器应实现此接口。

## *abstractmethod* **files**()

​	为载入的包返回一个 [`importlib.resources.abc.Traversable`](https://docs.python.org/zh-cn/3.13/library/importlib.resources.abc.html#importlib.resources.abc.Traversable) 对象。
