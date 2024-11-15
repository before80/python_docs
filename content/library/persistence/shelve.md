+++
title = "shelve --- Python 对象持久化"
date = 2024-11-15T11:57:40+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/shelve.html](https://docs.python.org/zh-cn/3.13/library/shelve.html)
>
> 收录该文档的时间：`2024-11-15T11:57:40+08:00`

# `shelve` --- Python 对象持久化

**源代码:** [Lib/shelve.py](https://github.com/python/cpython/tree/3.13/Lib/shelve.py)

------

​	"Shelf" 是一种持久化的类似字典的对象。 与 "dbm" 数据库的区别在于 Shelf 中的值（不是键！）实际上可以为任意 Python 对象 --- 即 [`pickle`](https://docs.python.org/zh-cn/3.13/library/pickle.html#module-pickle) 模块能够处理的任何东西。 这包括大部分类实例、递归数据类型，以及包含大量共享子对象的对象。 键则为普通的字符串。

## shelve.**open**(*filename*, *flag='c'*, *protocol=None*, *writeback=False*)

​	打开一个持久化字典。 filename 指定下层数据库的基准文件名。 作为附带效果，会为 filename 添加一个扩展名并且可能创建更多的文件。 默认情况下，下层数据库会以读写模式打开。 可选的 *flag* 形参具有与 [`dbm.open()`](https://docs.python.org/zh-cn/3.13/library/dbm.html#dbm.open) *flag* 形参相同的含义。

​	在默认情况下，会使用以 [`pickle.DEFAULT_PROTOCOL`](https://docs.python.org/zh-cn/3.13/library/pickle.html#pickle.DEFAULT_PROTOCOL) 创建的 pickle 来序列化值。 pickle 协议的版本可通过 *protocol* 形参来指定。

​	由于 Python 语义的限制，Shelf 对象无法确定一个可变的持久化字典条目在何时被修改。 默认情况下 *只有* 在被修改对象再赋值给 shelf 时才会写入该对象 (参见 [示例](https://docs.python.org/zh-cn/3.13/library/shelve.html#shelve-example))。 如果可选的 *writeback* 形参设为 `True`，则所有被访问的条目都将在内存中被缓存，并会在 [`sync()`](https://docs.python.org/zh-cn/3.13/library/shelve.html#shelve.Shelf.sync) 和 [`close()`](https://docs.python.org/zh-cn/3.13/library/shelve.html#shelve.Shelf.close) 时被写入；这可以使得对持久化字典中可变条目的修改更方便，但是如果访问的条目很多，这会消耗大量内存作为缓存，并会使得关闭操作变得非常缓慢，因为所有被访问的条目都需要写回到字典（无法确定被访问的条目中哪个是可变的，也无法确定哪个被实际修改了）。

*在 3.10 版本发生变更:* [`pickle.DEFAULT_PROTOCOL`](https://docs.python.org/zh-cn/3.13/library/pickle.html#pickle.DEFAULT_PROTOCOL) 现在会被用作默认的 pickle 协议。

*在 3.11 版本发生变更:* 接受 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object) 作为文件名。

​	备注

 

​	请不要依赖于 Shelf 的自动关闭功能；当你不再需要时应当总是显式地调用 [`close()`](https://docs.python.org/zh-cn/3.13/library/shelve.html#shelve.Shelf.close)，或者使用 [`shelve.open()`](https://docs.python.org/zh-cn/3.13/library/shelve.html#shelve.open) 作为上下文管理器:

```
with shelve.open('spam') as db:
    db['eggs'] = 'eggs'
```

​	警告

 

​	由于 [`shelve`](https://docs.python.org/zh-cn/3.13/library/shelve.html#module-shelve) 模块需要 [`pickle`](https://docs.python.org/zh-cn/3.13/library/pickle.html#module-pickle) 的支持，因此从不可靠的来源载入 shelf 是不安全的。 与 pickle 一样，载入 Shelf 时可以执行任意代码。

​	Shelf 对象支持字典所支持的大多数方法和运算（除了拷贝、构造器以及 `|` 和 `|=` 运算符）。 这样就能方便地将基于字典的脚本转换为要求持久化存储的脚本。

​	额外支持的两个方法：

## Shelf.**sync**()

​	如果 Shelf 打开时将 *writeback* 设为 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True) 则写回缓存中的所有条目。 如果可行还会清空缓存并将持久化字典同步到磁盘。 此方法会在使用 [`close()`](https://docs.python.org/zh-cn/3.13/library/shelve.html#shelve.Shelf.close) 关闭 Shelf 时自动被调用。

## Shelf.**close**()

​	同步并关闭持久化 *dict* 对象。 对已关闭 Shelf 的操作将失败并引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

​	参见

 

[持久化字典方案](https://code.activestate.com/recipes/576642-persistent-dict-with-multiple-standard-file-format/) 使用了广泛支持的存储格式并具有原生字典的速度。

## 限制

- 可选择使用哪种数据库包 (例如 [`dbm.ndbm`](https://docs.python.org/zh-cn/3.13/library/dbm.html#module-dbm.ndbm) 或 [`dbm.gnu`](https://docs.python.org/zh-cn/3.13/library/dbm.html#module-dbm.gnu)) 取决于支持哪种接口。 因此使用 [`dbm`](https://docs.python.org/zh-cn/3.13/library/dbm.html#module-dbm) 直接打开数据库是不安全的。 如果使用了 [`dbm`](https://docs.python.org/zh-cn/3.13/library/dbm.html#module-dbm)，数据库同样会（不幸地）受限于它 --- 这意味着存储在数据库中的（封存形式的）对象尺寸应当较小，并且在少数情况下键冲突有可能导致数据库拒绝更新。
- [`shelve`](https://docs.python.org/zh-cn/3.13/library/shelve.html#module-shelve) 模块不支持对 Shelf 对象的 *并发* 读/写访问。 （多个同时读取访问则是安全的。） 当一个程序打开一个 shelve 对象来写入时，不应再有其他程序同时打开它来读取或写入。 Unix 文件锁定可被用来解决此问题，但这在不同 Unix 版本上会存在差异，并且需要有关所用数据库实现的细节知识。
- 在 macOS 上 [`dbm.ndbm`](https://docs.python.org/zh-cn/3.13/library/dbm.html#module-dbm.ndbm) 会在更新时静默地破坏数据库文件，这将导致在尝试读取该数据库时发生硬崩溃。

## *class* shelve.**Shelf**(*dict*, *protocol=None*, *writeback=False*, *keyencoding='utf-8'*)

[`collections.abc.MutableMapping`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.MutableMapping) 的一个子类，它会将封存的值保存在 *dict* 对象中。

​	在默认情况下，会使用以 [`pickle.DEFAULT_PROTOCOL`](https://docs.python.org/zh-cn/3.13/library/pickle.html#pickle.DEFAULT_PROTOCOL) 创建的 pickle 来序列化值。 pickle 协议的版本可通过 *protocol* 形参来指定。 请参阅 [`pickle`](https://docs.python.org/zh-cn/3.13/library/pickle.html#module-pickle) 文档来查看 pickle 协议的相关讨论。

​	如果 *writeback* 形参为 `True`，对象将为所有访问过的条目保留缓存并在同步和关闭时将它们写回到 *dict*。 这允许对可变的条目执行自然操作，但是会消耗更多内存并让同步和关闭花费更长时间。

*keyencoding* 形参是在下层字典被使用之前用于编码键的编码格式。

[`Shelf`](https://docs.python.org/zh-cn/3.13/library/shelve.html#shelve.Shelf) 对象还可以被用作上下文管理器，在这种情况下它将在 [`with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#with) 语句块结束时自动被关闭。

*在 3.2 版本发生变更:* 添加了 *keyencoding* 形参；之前，键总是使用 UTF-8 编码。

*在 3.4 版本发生变更:* 添加了上下文管理器支持。

*在 3.10 版本发生变更:* [`pickle.DEFAULT_PROTOCOL`](https://docs.python.org/zh-cn/3.13/library/pickle.html#pickle.DEFAULT_PROTOCOL) 现在会被用作默认的 pickle 协议。

## *class* shelve.**BsdDbShelf**(*dict*, *protocol=None*, *writeback=False*, *keyencoding='utf-8'*)

[`Shelf`](https://docs.python.org/zh-cn/3.13/library/shelve.html#shelve.Shelf) 的一个子类，它对外公开了 `first()`, `next()`, `previous()`, `last()` 和 `set_location()` 方法。 这在来自 [pybsddb](https://www.jcea.es/programacion/pybsddb.htm) 的第三方模块 `bsddb` 中可用，但在其他数据库模块中不可用。 传给构造器的 *dict* 对象必须支持这些方法。 这一般是通过调用 `bsddb.hashopen()`, `bsddb.btopen()` 或 `bsddb.rnopen()` 中的一个来完成的。 可选的 *protocol*, *writeback* 和 *keyencoding* 形参具有与 [`Shelf`](https://docs.python.org/zh-cn/3.13/library/shelve.html#shelve.Shelf) 类的对应形参相同的含义。

## *class* shelve.**DbfilenameShelf**(*filename*, *flag='c'*, *protocol=None*, *writeback=False*)

[`Shelf`](https://docs.python.org/zh-cn/3.13/library/shelve.html#shelve.Shelf) 的一个子类，它接受一个 *filename* 而非字典类对象。 下层文件将使用 [`dbm.open()`](https://docs.python.org/zh-cn/3.13/library/dbm.html#dbm.open) 来打开。 默认情况下，文件将以读写模式打开。 可选的 *flag* 形参具有与 [`open()`](https://docs.python.org/zh-cn/3.13/library/shelve.html#shelve.open) 函数相同的含义。 可选的 *protocol* 和 *writeback* 形参具有与 [`Shelf`](https://docs.python.org/zh-cn/3.13/library/shelve.html#shelve.Shelf) 类相同的含义。



## 示例

​	对接口的总结如下 (`key` 为字符串，`data` 为任意对象):

```
import shelve

d = shelve.open(filename)  # 打开 -- 文件可能带有低层级库
                           # 所添加的后缀

d[key] = data              # 将 data 存储到 key 位置 (如果使用现有的 key
                           # 则会覆盖旧数据)
data = d[key]              # 获取 key 位置上 data 的拷贝 (如果 key 不存在
                           # 则会引发 KeyError)
del d[key]                 # 删除 key 位置上的 data (如果 key 不存在
                           # 则会引发 KeyError)

flag = key in d            # 如果 key 存在则为真值
klist = list(d.keys())     # 由全部现有 key 组成的列表 (会很慢!)

# 由于 d 打开时未设置 writeback=True，需要注意：
d['xx'] = [0, 1, 2]        # 这将符合预期，但是...
d['xx'].append(3)          # *这将不符合预期！* -- d['xx'] 仍为 [0, 1, 2]！

# 由于 d 打开时未设置 writeback=True，你需要小心地编码：
temp = d['xx']             # 提取副本
temp.append(5)             # 修改副本
d['xx'] = temp             # 将副本存储回去，以使其持久化

# 或者，d=shelve.open(filename,writeback=True) 将允许你
# 编码 d['xx'].append(5) 并使其符合预期，但是它也会
# 消耗更多内存并使 d.close() 操作变慢。

d.close()                  # 关闭它
```

​	参见

## 模块 [`dbm`](https://docs.python.org/zh-cn/3.13/library/dbm.html#module-dbm)

`dbm` 风格数据库的泛型接口。

## 模块 [`pickle`](https://docs.python.org/zh-cn/3.13/library/pickle.html#module-pickle)

[`shelve`](https://docs.python.org/zh-cn/3.13/library/shelve.html#module-shelve) 所使用的对象序列化。
