+++
title = "dbm --- Unix `数据库` 接口"
date = 2024-11-15T11:57:40+08:00
weight = 40
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/dbm.html](https://docs.python.org/zh-cn/3.13/library/dbm.html)
>
> 收录该文档的时间：`2024-11-15T11:57:40+08:00`

# `dbm` --- Unix "数据库" 接口

**源代码:** [Lib/dbm/__init__.py](https://github.com/python/cpython/tree/3.13/Lib/dbm/__init__.py)

------

[`dbm`]({{< ref "/library/persistence/dbm#module-dbm" >}}) 是一个针对多种 DBM 数据库的泛用接口：

- [`dbm.sqlite3`]({{< ref "/library/persistence/dbm#module-dbm.sqlite3" >}})
- [`dbm.gnu`]({{< ref "/library/persistence/dbm#module-dbm.gnu" >}})
- [`dbm.ndbm`]({{< ref "/library/persistence/dbm#module-dbm.ndbm" >}})

​	如果未安装这些模块中的任何一种，则将使用 [`dbm.dumb`]({{< ref "/library/persistence/dbm#module-dbm.dumb" >}}) 模块中慢速但简单的实现。 还有一个适用于 Oracle Berkeley DB 的 [第三方接口](https://www.jcea.es/programacion/pybsddb.htm)。

## *exception* dbm.**error**

​	一个元组，其中包含每个受支持的模块可引发的异常，另外还有一个名为 [`dbm.error`]({{< ref "/library/persistence/dbm#dbm.error" >}}) 的特殊异常作为第一项 --- 后者最在引发 [`dbm.error`]({{< ref "/library/persistence/dbm#dbm.error" >}}) 时被使用。

## dbm.**whichdb**(*filename*)

​	此函数会尝试猜测几种简单数据库模块中哪一个是可用的 --- [`dbm.sqlite3`]({{< ref "/library/persistence/dbm#module-dbm.sqlite3" >}}), [`dbm.gnu`]({{< ref "/library/persistence/dbm#module-dbm.gnu" >}}), [`dbm.ndbm`]({{< ref "/library/persistence/dbm#module-dbm.ndbm" >}}) 或 [`dbm.dumb`]({{< ref "/library/persistence/dbm#module-dbm.dumb" >}}) --- 并应当被用来打开给定的文件。

​	返回下列值中的一个：

- 如果文件因其不可读或不存在而无法打开则返回 `None`
- 如果文件格式无法猜测则返回空字符串 (`''`)
- 包含所需模块名称的字符串，如 `'dbm.ndbm'` 或 `'dbm.gnu'`

> 在 3.11 版本发生变更: *filename* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## dbm.**open**(*file*, *flag='r'*, *mode=0o666*)

​	打开一个数据库并返回相应的数据库对象。

## 参数:

- **file** ([path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})) -- 要打开的数据库文件。 如果数据库文件已存在，则使用 [`whichdb()`]({{< ref "/library/persistence/dbm#dbm.whichdb" >}}) 来确定其类型和要使用的适当模块；如果文件不存在，则会使用上述可导入子模块中的第一个。

- **flag** ([*str*]({{< ref "/library/stdtypes#str" >}})) --

  - `'r'` (default): Open existing database for reading only.
  - `'w'`: Open existing database for reading and writing.
  - `'c'`: Open database for reading and writing, creating it if it doesn't exist.
  - `'n'`: Always create a new, empty database, open for reading and writing.

  

- **mode** ([*int*]({{< ref "/library/functions#int" >}})) -- The Unix file access mode of the file (default: octal `0o666`), used only when the database has to be created.

> 在 3.11 版本发生变更: *file* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

[`open()`]({{< ref "/library/persistence/dbm#dbm.open" >}}) 所返回的对象支持与 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 相同的基本功能；可以存储、获取和删除键及其对应的值，并可使用 [`in`]({{< ref "/reference/expressions#in" >}}) 运算符和 `keys()` 方法，以及 `get()` 和 `setdefault()` 方法。

​	键和值总是被存储为 [`bytes`]({{< ref "/library/stdtypes#bytes" >}})。 这意味着当使用字符串时它们会在被存储之前隐式地转换至默认编码格式。

​	这些对象也支持在 [`with`]({{< ref "/reference/compound_stmts#with" >}}) 语句中使用，当语句结束时将自动关闭它们。

> 在 3.2 版本发生变更: 现在 `get()` 和 `setdefault()` 方法对所有 [`dbm`]({{< ref "/library/persistence/dbm#module-dbm" >}}) 后端均可用。

> 在 3.4 版本发生变更: 向 [`open()`]({{< ref "/library/persistence/dbm#dbm.open" >}}) 所返回的对象添加了对上下文管理协议的原生支持。

> 在 3.8 版本发生变更: 从只读数据库中删除键将引发数据库模块专属的异常而不是 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}})。

​	以下示例记录了一些主机名和对应的标题，随后将数据库的内容打印出来。:

```
import dbm

# 打开数据库，如有必要则创建它。
with dbm.open('cache', 'c') as db:

    # 记录一些值
    db[b'hello'] = b'there'
    db['www.python.org'] = 'Python Website'
    db['www.cnn.com'] = 'Cable News Network'

    # 请注意现在键被作为字节串。
    assert db[b'www.python.org'] == b'Python Website'
    # 可以看到值现在被作为字节串。
    assert db['www.cnn.com'] == b'Cable News Network'

    # 常用的字典接口方法同样可用。
    print(db.get('python.org', b'not present'))

    # 存储非字符串的键或值将引发异常
    # (通常为 TypeError)。
    db['www.yahoo.com'] = 4

# 当离开 with 语句时 db 将被自动关闭。
```

​参见
## 模块 [`shelve`]({{< ref "/library/persistence/shelve#module-shelve" >}})

​	存储非字符串数据的持久化模块。

​	以下部分描述了各个单独的子模块。



## [`dbm.sqlite3`]({{< ref "/library/persistence/dbm#module-dbm.sqlite3" >}}) --- 针对 dbm 的 SQLite 后端

> Added in version 3.13.
>

**源代码:** [Lib/dbm/sqlite3.py](https://github.com/python/cpython/tree/3.13/Lib/dbm/sqlite3.py)

------

​	此模块使用标准库 [`sqlite3`]({{< ref "/library/persistence/sqlite3#module-sqlite3" >}}) 模块来提供针对 [`dbm`]({{< ref "/library/persistence/dbm#module-dbm" >}}) 模块的 SQLite 后端。 这样由 [`dbm.sqlite3`]({{< ref "/library/persistence/dbm#module-dbm.sqlite3" >}}) 创建的文件可通过 [`sqlite3`]({{< ref "/library/persistence/sqlite3#module-sqlite3" >}})，或任何其他 SQLite 浏览器，包括 SQLite CLI 打开。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

​	此模块在 WebAssembly 平台上无效或不可用。 请参阅 [WebAssembly 平台]({{< ref "/library/intro#wasm-availability" >}}) 了解详情。

## dbm.sqlite3.**open**(*filename*, */*, *flag='r'*, *mode=0o666*)

​	打开一个 SQLite 数据库。 返回的对象行为类似于 [mapping]({{< ref "/glossary/idx#term-mapping" >}})，实现了 `close()` 方法，并通过 [`with`]({{< ref "/reference/compound_stmts#with" >}}) 关键字支持“关闭”上下文管理器。

## 参数:

- **filename** ([path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})) -- 要打开的数据库的路径。

- **flag** ([*str*]({{< ref "/library/stdtypes#str" >}})) --

  - `'r'` (default): Open existing database for reading only.
  - `'w'`: Open existing database for reading and writing.
  - `'c'`: Open database for reading and writing, creating it if it doesn't exist.
  - `'n'`: Always create a new, empty database, open for reading and writing.

  

- **mode** -- 文件的 Unix 文件访问模式 (默认值: 八进制数 `0o666`)，仅在需要创建数据为时使用。



## [`dbm.gnu`]({{< ref "/library/persistence/dbm#module-dbm.gnu" >}}) --- GNU 数据库管理器

**源代码:** [Lib/dbm/gnu.py](https://github.com/python/cpython/tree/3.13/Lib/dbm/gnu.py)

------

[`dbm.gnu`]({{< ref "/library/persistence/dbm#module-dbm.gnu" >}}) 模块提供了针对 GDBM 库的接口，类似于 [`dbm.ndbm`]({{< ref "/library/persistence/dbm#module-dbm.ndbm" >}}) 模块，但带有额外的功能如对崩溃的容忍。

​备注
 

​	由 [`dbm.gnu`]({{< ref "/library/persistence/dbm#module-dbm.gnu" >}}) 和 [`dbm.ndbm`]({{< ref "/library/persistence/dbm#module-dbm.ndbm" >}}) 创建的文件格式是不兼容的因而无法互换使用。

[Availability]({{< ref "/library/intro#availability" >}}): not Android, not iOS, not WASI.

​	此模块在 [移动平台]({{< ref "/library/intro#mobile-availability" >}}) 或 [WebAssembly 平台]({{< ref "/library/intro#wasm-availability" >}}) 上不受支持。

## *exception* dbm.gnu.**error**

​	针对 [`dbm.gnu`]({{< ref "/library/persistence/dbm#module-dbm.gnu" >}}) 专属错误例如 I/O 错误引发。 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}}) 的引发则针对一般映射错误例如指定了不正确的键。

## dbm.gnu.**open**(*filename*, *flag='r'*, *mode=0o666*, */*)

​	打开 GDBM 数据库并返回一个 `gdbm` 对象。

## 参数:

- **filename** ([path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})) -- 要打开的数据库文件。

- **flag** ([*str*]({{< ref "/library/stdtypes#str" >}})) --

  - `'r'` (default): Open existing database for reading only.
  - `'w'`: Open existing database for reading and writing.
  - `'c'`: Open database for reading and writing, creating it if it doesn't exist.
  - `'n'`: Always create a new, empty database, open for reading and writing.

  可以添加下列额外字符来控制数据库的打开方式：

  - `'f'`: 以快速模式打开数据库。 对数据库的写入将不是同步的。
  - `'s'`: 同步模式。 对数据库的修改将立即写入到文件。
  - `'u'`: 不锁定数据库。

  并非所有旗标都对所有 GDBM 版本可用。 请参阅 [`open_flags`]({{< ref "/library/persistence/dbm#dbm.gnu.open_flags" >}}) 成员获取受支持旗标字符的列表。

  

- **mode** ([*int*]({{< ref "/library/functions#int" >}})) -- The Unix file access mode of the file (default: octal `0o666`), used only when the database has to be created.

## 抛出:

[**error**]({{< ref "/library/persistence/dbm#dbm.gnu.error" >}}) -- 如果传入了不可用的 *flag* 参数。

> 在 3.11 版本发生变更: *filename* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## dbm.gnu.**open_flags**

​	由 [`open()`]({{< ref "/library/persistence/dbm#dbm.gnu.open" >}}) 的 *flag* 形参所支持的字符组成的字符串。

`gdbm` 对象的行为类似于 [映射]({{< ref "/glossary/idx#term-mapping" >}})，但不支持 `items()` 和 `values()` 方法。 还提供了以下方法：

## gdbm.**firstkey**()

​	可以使用此方法和 [`nextkey()`]({{< ref "/library/persistence/dbm#dbm.gnu.gdbm.nextkey" >}}) 方法循环遍历数据库中的每个键。 遍历的顺序是按照 GDBM 的内部哈希值，而不会根据键的值排序。 此方法将返回起始的键。

## gdbm.**nextkey**(*key*)

​	在遍历中返回 *key* 之后的的下一个键。 以下代码将打印数据库 `db` 中的每个键，而不会在内存中创建一个包含所有键的列表:

```
k = db.firstkey()
while k is not None:
    print(k)
    k = db.nextkey(k)
```

## gdbm.**reorganize**()

​	如果你进行了大量删除操作并且想要缩减 GDBM 文件所使用的空间，此例程可将可重新组织数据库。 除非使用此重组功能否则 `gdbm` 对象不会缩减数据库文件大小；在其他情况下，被删除的文件空间将会保留并在添加新的 (键, 值) 对时被重用。

## gdbm.**sync**()

​	当以快速模式打开数据库时，此方法会将任何未写入数据强制写入磁盘。

## gdbm.**close**()

​	关闭 GDBM 数据库。

## gdbm.**clear**()

​	从 GDBM 数据库移除所有条目。

> Added in version 3.13.
>



## [`dbm.ndbm`]({{< ref "/library/persistence/dbm#module-dbm.ndbm" >}}) --- 新数据库管理器

**源代码:** [Lib/dbm/ndbm.py](https://github.com/python/cpython/tree/3.13/Lib/dbm/ndbm.py)

------

[`dbm.ndbm`]({{< ref "/library/persistence/dbm#module-dbm.ndbm" >}}) 模块提供了对 NDBM 库的接口。 此模块可与 "经典" NDBM 接口或 GDBM 兼容接口配合使用。

​备注
 

​	由 [`dbm.gnu`]({{< ref "/library/persistence/dbm#module-dbm.gnu" >}}) 和 [`dbm.ndbm`]({{< ref "/library/persistence/dbm#module-dbm.ndbm" >}}) 创建的文件格式是不兼容的因而无法互换使用。

​	警告

 

​	作为 macOS 的组成部分提供的 NDBM 库对值的大小有一个未写入文档的限制，当存储的值大于此限制时可能会导致数据库文件损坏。 读取这种已损坏的文件可能会导致硬崩溃（段错误）。

[Availability]({{< ref "/library/intro#availability" >}}): not Android, not iOS, not WASI.

​	此模块在 [移动平台]({{< ref "/library/intro#mobile-availability" >}}) 或 [WebAssembly 平台]({{< ref "/library/intro#wasm-availability" >}}) 上不受支持。

## *exception* dbm.ndbm.**error**

​	针对 [`dbm.ndbm`]({{< ref "/library/persistence/dbm#module-dbm.ndbm" >}}) 专属错误例如 I/O 错误引发。 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}}) 的引发则针对一般映射错误例如指定了不正确的键。

## dbm.ndbm.**library**

​	所使用的 NDBM 实现库的名称。

## dbm.ndbm.**open**(*filename*, *flag='r'*, *mode=0o666*, */*)

​	打开 NDBM 数据库并返回一个 `ndbm` 对象。

## 参数:

- **filename** ([path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})) -- 数据库文件的基本名（不带 `.dir` 或 `.pag` 扩展名）。

- **flag** ([*str*]({{< ref "/library/stdtypes#str" >}})) --

  - `'r'` (default): Open existing database for reading only.
  - `'w'`: Open existing database for reading and writing.
  - `'c'`: Open database for reading and writing, creating it if it doesn't exist.
  - `'n'`: Always create a new, empty database, open for reading and writing.

  

- **mode** ([*int*]({{< ref "/library/functions#int" >}})) -- The Unix file access mode of the file (default: octal `0o666`), used only when the database has to be created.

`ndbm` 对象的行为类似于 [映射]({{< ref "/glossary/idx#term-mapping" >}})，但不支持 `items()` 和 `values()` 方法。 还提供了以下方法：

> 在 3.11 版本发生变更: 接受 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}}) 作为文件名。

## ndbm.**close**()

​	关闭 NDBM 数据库。

## ndbm.**clear**()

​	从 NDBM 数据库移除所有条目。

> Added in version 3.13.
>



## [`dbm.dumb`]({{< ref "/library/persistence/dbm#module-dbm.dumb" >}}) --- 便携式 DBM 实现

**源代码:** [Lib/dbm/dumb.py](https://github.com/python/cpython/tree/3.13/Lib/dbm/dumb.py)

​备注
 

[`dbm.dumb`]({{< ref "/library/persistence/dbm#module-dbm.dumb" >}}) 模块的目的是在更健壮的模块不可用时作为 [`dbm`]({{< ref "/library/persistence/dbm#module-dbm" >}}) 模块的最终回退项。 [`dbm.dumb`]({{< ref "/library/persistence/dbm#module-dbm.dumb" >}}) 不是为高速运行而编写的，也不像其他数据库模块一样被经常使用。

------

[`dbm.dumb`]({{< ref "/library/persistence/dbm#module-dbm.dumb" >}}) 模块提供了一个完全以 Python 编写的持久化 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 型接口。 不同于其他 [`dbm`]({{< ref "/library/persistence/dbm#module-dbm" >}}) 后端，例如 [`dbm.gnu`]({{< ref "/library/persistence/dbm#module-dbm.gnu" >}})，它不需要外部库。

`dbm.dumb` 模块定义了以下对象：

## *exception* dbm.dumb.**error**

​	针对 [`dbm.dumb`]({{< ref "/library/persistence/dbm#module-dbm.dumb" >}}) 专属错误例如 I/O 错误引发。 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}}) 的引发则针对一般映射例如指定了不正确的键。

## dbm.dumb.**open**(*filename*, *flag='c'*, *mode=0o666*)

​	打开一个 `dbm.dumb` 数据库。 返回的数据库对象的行为类似于 [mapping]({{< ref "/glossary/idx#term-mapping" >}})，并额外提供 [`sync()`]({{< ref "/library/persistence/dbm#dbm.dumb.dumbdbm.sync" >}}) 和 [`close()`]({{< ref "/library/persistence/dbm#dbm.dumb.dumbdbm.close" >}}) 等方法。

## 参数:

- **filename** -- 数据库文件的基本名（不带扩展名）。 新数据库将会创建以下文件: - `*filename*.dat` - `*filename*.dir`

- **flag** ([*str*]({{< ref "/library/stdtypes#str" >}})) --

  - `'r'`: Open existing database for reading only.
  - `'w'`: Open existing database for reading and writing.
  - `'c'` (default): Open database for reading and writing, creating it if it doesn't exist.
  - `'n'`: Always create a new, empty database, open for reading and writing.

  

- **mode** ([*int*]({{< ref "/library/functions#int" >}})) -- The Unix file access mode of the file (default: octal `0o666`), used only when the database has to be created.

​	警告

 

​	当载入包含足够巨大/复杂条目的数据库时有可能导致 Python 解释器的崩溃，这是由于 Python AST 编译器有栈深度限制。

> 在 3.5 版本发生变更: [`open()`]({{< ref "/library/persistence/dbm#dbm.dumb.open" >}}) 在 *flag* 为 `'n'` 时将总是创建一个新数据库。

> 在 3.8 版本发生变更: 如果 *flag* 为 `'r'` 则打开的数据库将为只读的。 如果 *flag* 为 `'r'` 或 `'w'` 则当数据库不存在时不会自动创建它。

> 在 3.11 版本发生变更: *filename* 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

​	在 [`collections.abc.MutableMapping`]({{< ref "/library/datatypes/collections_abc#collections.abc.MutableMapping" >}}) 类所提供的方法之外，还提供了以下方法：

## dumbdbm.**sync**()

​	同步磁盘上的目录和数据文件。 此方法会由 `Shelve.sync()` 方法来调用。

## dumbdbm.**close**()

​	关闭数据库。
