+++
title = "mimetypes --- 将文件名映射到 MIME 类型"
date = 2024-11-15T14:53:40+08:00
weight = 30
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/mimetypes.html](https://docs.python.org/zh-cn/3.13/library/mimetypes.html)
>
> 收录该文档的时间：`2024-11-15T14:53:40+08:00`

# `mimetypes` --- 将文件名映射到 MIME 类型

**源代码:** [Lib/mimetypes.py](https://github.com/python/cpython/tree/3.13/Lib/mimetypes.py)

------

[`mimetypes`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#module-mimetypes) 模块可以在文件名或 URL 和关联到文件扩展名的 MIME 类型之间执行转换。 所提供的转换包括从文件名到 MIME 类型和从 MIME 类型到文件扩展名；后一种转换不支持编码格式。

​	该模块提供了一个类和一些便捷函数。 这些函数是该模块通常的接口，但某些应用程序可能也会希望使用类。

​	下列函数提供了此模块的主要接口。 如果此模块尚未被初始化，它们将会调用 [`init()`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.init)，如果它们依赖于 [`init()`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.init) 所设置的信息的话。

## mimetypes.**guess_type**(*url*, *strict=True*)

​	根据 *url* 给出的文件名、路径或 URL 来猜测文件的类型，URL 可以为字符串或 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

​	返回值是一个元组 `(type, encoding)` 其中 *type* 在无法猜测（后缀不存在或者未知）时为 `None`，或者为 `'type/subtype'` 形式的字符串，可以作为 MIME *content-type* 标头。

*encoding* 在无编码格式时为 `None`，或者为程序所用的编码格式 (例如 **compress** 或 **gzip**)。 它可以作为 *Content-Encoding* 标头，但 **不可** 作为 *Content-Transfer-Encoding* 标头。 映射是表格驱动的。 编码格式前缀对大小写敏感；类型前缀会先以大小写敏感方式检测再以大小写不敏感方式检测。

​	可选的 *strict* 参数是一个旗标，指明要将已知 MIME 类型限制在 [IANA 已注册](https://www.iana.org/assignments/media-types/media-types.xhtml) 的官方类型之内。 当 *strict* 为 `True` 时（默认值），则仅支持 IANA 类型；当 *strict* 为 `False` 时，则还支持某些附加的非标准但常用的 MIME 类型。

*在 3.8 版本发生变更:* 增加了对于 *url* 使用 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object) 的支持。

*自 3.13 版本弃用:* 传入文件路径而非 URL 的做法已经 [soft deprecated](https://docs.python.org/zh-cn/3.13/glossary.html#term-soft-deprecated)。 此种情况改用 [`guess_file_type()`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.guess_file_type)。

## mimetypes.**guess_file_type**(*path*, ***, *strict=True*)

​	根据由 *path* 给出的文件路径猜测其类型。 类似于 [`guess_type()`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.guess_type) 函数，但可接受路径而非 URL。 路径可以是字符串、字节串对象或 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

*Added in version 3.13.*

## mimetypes.**guess_all_extensions**(*type*, *strict=True*)

​	根据由 *type* 给出的文件 MIME 类型猜测其扩展名。 返回值是由所有可能的文件扩展名组成的字符串列表，包括开头的点号 (`'.'`)。 这些扩展名不保证能关联到任何特定的数据流，但是将会由 [`guess_type()`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.guess_type) 和 [`guess_file_type()`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.guess_file_type) 映射到 MIME 类型 *type*。

​	可选的 *strict* 参数具有与 [`guess_type()`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.guess_type) 函数一致的含义。

## mimetypes.**guess_extension**(*type*, *strict=True*)

​	根据由 *type* 给出的文件 MIME 类型猜测其扩展名。 返回值是一个表示文件扩展名的字符串，包括开头的点号 (`'.'`)。 该扩展名不保证能关联到任何特定的数据流，但是将会由 [`guess_type()`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.guess_type) 和 [`guess_file_type()`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.guess_file_type) 映射到 MIME 类型 *type*。 如果不能猜测出 *type* 的扩展名，则返回 `None`。

​	可选的 *strict* 参数具有与 [`guess_type()`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.guess_type) 函数一致的含义。

​	有一些附加函数和数据项可被用于控制此模块的行为。

## mimetypes.**init**(*files=None*)

​	初始化内部数据结构。 *files* 如果给出则必须是一个文件名序列，它应当被用于协助默认的类型映射。 如果省略则要使用的文件名会从 [`knownfiles`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.knownfiles) 中获取； 在 Windows 上，将会载入当前注册表设置。 在 *files* 或 [`knownfiles`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.knownfiles) 中指定的每个文件名的优先级将高于在它之前的文件名。 [`init()`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.init) 允许被重复调用。

​	为 *files* 指定一个空列表将防止应用系统默认选项：将只保留来自内置列表的常用值。

​	如果 *files* 为 `None` 则内部数据结构会完全重建为其初始默认值。 这是一个稳定操作并将在多次调用时产生相同的结果。

*在 3.2 版本发生变更:* 在之前版本中，Windows 注册表设置会被忽略。

## mimetypes.**read_mime_types**(*filename*)

​	载入在文件 *filename* 中给定的类型映射，如果文件存在的话。 返回的类型映射会是一个字典，其中的键值对为文件扩展名包括开头的点号 (`'.'`) 与 `'type/subtype'` 形式的字符串。 如果文件 *filename* 不存在或无法被读取，则返回 `None`。

## mimetypes.**add_type**(*type*, *ext*, *strict=True*)

​	添加一个从 MIME 类型 *type* 到扩展名 *ext* 的映射。 当扩展名已知时，新类型将替代旧类型。 当类型已知时，扩展名将被添加到已知扩展名列表。

​	当 *strict* 为 `True` 时（默认值），映射将被添加到官方 MIME 类型，否则添加到非标准类型。

## mimetypes.**inited**

​	指明全局数据结构是否已被初始化的旗标。 这会由 [`init()`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.init) 设为 `True`。

## mimetypes.**knownfiles**

​	通常安装的类型映射文件名列表。 这些文件一般被命名为 `mime.types` 并会由不同的包安装在不同的位置。

## mimetypes.**suffix_map**

​	将后缀映射到其他后缀的字典。 它被用来允许识别已编码的文件，其编码格式和类型是由相同的扩展名来指明的。 例如，`.tgz` 扩展名被映射到 `.tar.gz` 以允许编码格式和类型被分别识别。

## mimetypes.**encodings_map**

​	映射文件扩展名到编码格式类型的字典。

## mimetypes.**types_map**

​	映射文件扩展名到 MIME 类型的字典。

## mimetypes.**common_types**

​	映射文件扩展名到非标准但常见的 MIME 类型的字典。

​	此模块一个使用示例:



```
>>> import mimetypes
>>> mimetypes.init()
>>> mimetypes.knownfiles
['/etc/mime.types', '/etc/httpd/mime.types', ... ]
>>> mimetypes.suffix_map['.tgz']
'.tar.gz'
>>> mimetypes.encodings_map['.gz']
'gzip'
>>> mimetypes.types_map['.tgz']
'application/x-tar-gz'
```



## MimeTypes 对象

[`MimeTypes`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.MimeTypes) 类可以被用于那些需要多个 MIME 类型数据库的应用程序；它提供了与 [`mimetypes`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#module-mimetypes) 模块所提供的类似接口。

## *class* mimetypes.**MimeTypes**(*filenames=()*, *strict=True*)

​	这个类表示 MIME 类型数据库。 默认情况下，它提供了对与此模块其余部分一致的数据库的访问权限。 这个初始数据库是此模块所提供数据库的一个副本，并可以通过使用 [`read()`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.MimeTypes.read) 或 [`readfp()`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.MimeTypes.readfp) 方法将附加的 `mime.types` 样式文载入到数据库中来进行扩展。 如果不需要默认数据的话这个映射字典也可以在载入附加数据之前先被清空。

​	可选的 *filenames* 形参可被用来让附加文件被载入到默认数据库“之上”。

## **suffix_map**

​	将后缀映射到其他后缀的字典。 它被用来允许识别已编码的文件，其编码格式和类型是由相同的扩展名来指明的。 例如，`.tgz` 扩展名被映射到 `.tar.gz` 以允许编码格式和类型被分别识别。 这是在模块中定义的全局 [`suffix_map`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.suffix_map) 的一个副本。

## **encodings_map**

​	映射文件扩展名到编码格式类型的字典。 这是在模块中定义的全局 [`encodings_map`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.encodings_map) 的一个副本。

## **types_map**

​	包含两个字典的元组，将文件扩展名映射到 MIME 类型：第一个字典针对非标准类型而第二个字典针对标准类型。 它们会由 [`common_types`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.common_types) 和 [`types_map`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.types_map) 来初始化。

## **types_map_inv**

​	包含两个字典的元组，将 MIME 类型映射到文件扩展名列表：第一个字典针对非标准类型而第二个字典针对标准类型。 它们会由 [`common_types`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.common_types) 和 [`types_map`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.types_map) 来初始化。

## **guess_extension**(*type*, *strict=True*)

​	类似于 [`guess_extension()`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.guess_extension) 函数，使用存储的表作为对象的一部分。

## **guess_type**(*url*, *strict=True*)

​	类似于 [`guess_type()`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.guess_type) 函数，使用存储的表作为对象的一部分。

## **guess_file_type**(*path*, ***, *strict=True*)

​	类似于 [`guess_file_type()`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.guess_file_type) 函数，使用存储的表作为对象的一部分。

*Added in version 3.13.*

## **guess_all_extensions**(*type*, *strict=True*)

​	类似于 [`guess_all_extensions()`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.guess_all_extensions) 函数，使用存储的表作为对象的一部分。

## **read**(*filename*, *strict=True*)

​	从名称为 *filename* 的文件载入 MIME 信息。 此方法使用 [`readfp()`](https://docs.python.org/zh-cn/3.13/library/mimetypes.html#mimetypes.MimeTypes.readfp) 来解析文件。

​	如果 *strict* 为 `True`，信息将被添加到标准类型列表，否则添加到非标准类型列表。

## **readfp**(*fp*, *strict=True*)

​	从打开的文件 *fp* 载入 MIME 类型信息。 文件必须具有标准 `mime.types` 文件的格式。

​	如果 *strict* 为 `True`，信息将被添加到标准类型列表，否则添加到非标准类型列表。

## **read_windows_registry**(*strict=True*)

​	从 Windows 注册表载入 MIME 类型信息。

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): Windows.

​	如果 *strict* 为 `True`，信息将被添加到标准类型列表，否则添加到非标准类型列表。

*Added in version 3.2.*

## **add_type**(*type*, *ext*, *strict=True*)

​	添加一个从 MIME 类型 *type* 到扩展名 *ext* 的映射。 当扩展名已知时，新类型将替代旧类型。 当类型已知时，扩展名将被添加到已知扩展名列表。

​	当 *strict* 为 `True` 时（默认值），映射将被添加到官方 MIME 类型，否则添加到非标准类型。
