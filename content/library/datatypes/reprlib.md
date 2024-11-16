+++
title = "reprlib --- 替代性 repr() 实现"
date = 2024-11-15T11:18:41+08:00
weight = 120
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/reprlib.html](https://docs.python.org/zh-cn/3.13/library/reprlib.html)
>
> 收录该文档的时间：`2024-11-15T11:18:41+08:00`

# `reprlib` --- 替代性 [`repr()`]({{< ref "/library/functions#repr" >}}) 实现

**源代码:** [Lib/reprlib.py](https://github.com/python/cpython/tree/3.13/Lib/reprlib.py)

------

`reprlib` 模块提供了一种对象表示的产生方式，它会对结果字符串的大小进行限制。 该方式被用于 Python 调试器并可能同样适用于某些其他场景。

​	此模块提供了一个类、一个实例和一个函数：

## *class* reprlib.**Repr**(***, *maxlevel=6*, *maxtuple=6*, *maxlist=6*, *maxarray=5*, *maxdict=4*, *maxset=6*, *maxfrozenset=6*, *maxdeque=6*, *maxstring=30*, *maxlong=40*, *maxother=30*, *fillvalue='...'*, *indent=None*)

​	该类提供了格式化服务 适用于实现与内置 [`repr()`]({{< ref "/library/functions#repr" >}}) 相似的方法；其中附加了针对不同对象类型的大小限制，以避免生成超长的表示。

​	该构造器的关键字参数可被用作设置 [`Repr`]({{< ref "/library/datatypes/reprlib#reprlib.Repr" >}}) 实例属性的快捷方式。 这意味着以下的初始化:

```
aRepr = reprlib.Repr(maxlevel=3)
```

​	等价于：

```
aRepr = reprlib.Repr()
aRepr.maxlevel = 3
```

​	请参阅 [Repr Objects]({{< ref "/library/datatypes/reprlib#id1" >}}) 小节了解有关 [`Repr`]({{< ref "/library/datatypes/reprlib#reprlib.Repr" >}}) 属性的信息。

> 在 3.12 版本发生变更: 允许通过关键字参数来设置属性。

## reprlib.**aRepr**

​	这是 [`Repr`]({{< ref "/library/datatypes/reprlib#reprlib.Repr" >}}) 的一个实例，用于提供如下所述的 [`repr()`]({{< ref "/library/datatypes/reprlib#reprlib.repr" >}}) 函数。 改变此对象的属性将会影响 [`repr()`]({{< ref "/library/datatypes/reprlib#reprlib.repr" >}}) 和 Python 调试器所使用的大小限制。

## reprlib.**repr**(*obj*)

​	这是 `aRepr` 的 [`repr()`]({{< ref "/library/datatypes/reprlib#reprlib.Repr.repr" >}}) 方法。 它会返回与同名内置函数所返回字符串相似的字符串，区别在于附带了对多数类型的大小限制。

​	在大小限制工具以外，此模块还提供了一个装饰器用于检测对 [`__repr__()`]({{< ref "/reference/datamodel#object.__repr__" >}}) 的递归调用并改用一个占位符来替换。

## @reprlib.**recursive_repr**(*fillvalue='...'*)

​	用于为 [`__repr__()`]({{< ref "/reference/datamodel#object.__repr__" >}}) 方法检查同一线程内部递归调用的装饰器。 如果执行了递归调用，则返回 *fillvalue*，在其他情况下，将执行正常的 `__repr__()` 调用。 例如：



``` python
>>> from reprlib import recursive_repr
>>> class MyList(list):
...     @recursive_repr()
...     def __repr__(self):
...         return '<' + '|'.join(map(repr, self)) + '>'
...
>>> m = MyList('abc')
>>> m.append(m)
>>> m.append('x')
>>> print(m)
<'a'|'b'|'c'|...|'x'>
```

> Added in version 3.2.
>



## Repr 对象

[`Repr`]({{< ref "/library/datatypes/reprlib#reprlib.Repr" >}}) 实例对象包含一些属性可以用于为不同对象类型的表示提供大小限制，还包含一些方法可以格式化特定的对象类型。

## Repr.**fillvalue**

​	该字符串将针对递归引用显示。 它默认为 `...`。

> Added in version 3.11.
>

## Repr.**maxlevel**

​	创建递归表示形式的深度限制。 默认为 `6`。

## Repr.**maxdict**

## Repr.**maxlist**

## Repr.**maxtuple**

## Repr.**maxset**

## Repr.**maxfrozenset**

## Repr.**maxdeque**

## Repr.**maxarray**

​	表示命名对象类型的条目数量限制。 对于 [`maxdict`]({{< ref "/library/datatypes/reprlib#reprlib.Repr.maxdict" >}}) 的默认值为 `4`，对于 [`maxarray`]({{< ref "/library/datatypes/reprlib#reprlib.Repr.maxarray" >}}) 为 `5`，对于其他则为 `6`。

## Repr.**maxlong**

​	表示整数的最大字符数量。 数码会从中间被丢弃。 默认值为 `40`。

## Repr.**maxstring**

​	表示字符串的字符数量限制。 请注意字符源会使用字符串的“正常”表示形式：如果表示中需要用到转义序列，在缩短表示时它们可能会被破坏。 默认值为 `30`。

## Repr.**maxother**

​	此限制用于控制在 [`Repr`]({{< ref "/library/datatypes/reprlib#reprlib.Repr" >}}) 对象上没有特定的格式化方法可用的对象类型的大小。 它会以类似 [`maxstring`]({{< ref "/library/datatypes/reprlib#reprlib.Repr.maxstring" >}}) 的方式被应用。 默认值为 `20`。

## Repr.**indent**

​	如果该属性被设为 `None` (默认值)，输出将被格式化为不带换行或缩进，像标准的 [`repr()`]({{< ref "/library/functions#repr" >}}) 一样。 例如:



``` python
>>> example = [
...     1, 'spam', {'a': 2, 'b': 'spam eggs', 'c': {3: 4.5, 6: []}}, 'ham']
>>> import reprlib
>>> aRepr = reprlib.Repr()
>>> print(aRepr.repr(example))
[1, 'spam', {'a': 2, 'b': 'spam eggs', 'c': {3: 4.5, 6: []}}, 'ham']
```

​	如果 [`indent`]({{< ref "/library/datatypes/reprlib#reprlib.Repr.indent" >}}) 被设为一个字符串，每个递归层级将放在单独行中，并用该字符串来缩进:



``` python
>>> aRepr.indent = '-->'
>>> print(aRepr.repr(example))
[
-->1,
-->'spam',
-->{
-->-->'a': 2,
-->-->'b': 'spam eggs',
-->-->'c': {
-->-->-->3: 4.5,
-->-->-->6: [],
-->-->},
-->},
-->'ham',
]
```

​	将 [`indent`]({{< ref "/library/datatypes/reprlib#reprlib.Repr.indent" >}}) 设为一个正整数时其行为与设为相应数量的空格是相同的:



``` python
>>> aRepr.indent = 4
>>> print(aRepr.repr(example))
[
    1,
    'spam',
    {
        'a': 2,
        'b': 'spam eggs',
        'c': {
            3: 4.5,
            6: [],
        },
    },
    'ham',
]
```

> Added in version 3.12.
>

## Repr.**repr**(*obj*)

​	内置 [`repr()`]({{< ref "/library/functions#repr" >}}) 的等价形式，它使用实例专属的格式化。

## Repr.**repr1**(*obj*, *level*)

​	供 [`repr()`]({{< ref "/library/datatypes/reprlib#reprlib.Repr.repr" >}}) 使用的递归实现。 此方法使用 *obj* 的类型来确定要调用哪个格式化方法，并传入 *obj* 和 *level*。 类型专属的方法应当调用 [`repr1()`]({{< ref "/library/datatypes/reprlib#reprlib.Repr.repr1" >}}) 来执行递归格式化，在递归调用中使用 `level - 1` 作为 *level* 的值。

## Repr.**repr_TYPE**(*obj*, *level*)

​	特定类型的格式化方法会被实现为基于类型名称来命名的方法。 在方法名称中，**TYPE** 会被替换为 `'_'.join(type(obj).__name__.split())`。 对这些方法的分派会由 [`repr1()`]({{< ref "/library/datatypes/reprlib#reprlib.Repr.repr1" >}}) 来处理。 需要对值进行递归格式化的类型专属方法应当调用 `self.repr1(subobj, level - 1)`。



## 子类化 Repr 对象

​	通过 [`Repr.repr1()`]({{< ref "/library/datatypes/reprlib#reprlib.Repr.repr1" >}}) 使用动态分派允许 [`Repr`]({{< ref "/library/datatypes/reprlib#reprlib.Repr" >}}) 的子类添加额外内置对象类型的支持，或是修改对已支持类型的处理。 这个例子演示了如何添加对文件对象的特殊支持：

```
import reprlib
import sys

class MyRepr(reprlib.Repr):

    def repr_TextIOWrapper(self, obj, level):
        if obj.name in {'<stdin>', '<stdout>', '<stderr>'}:
            return obj.name
        return repr(obj)

aRepr = MyRepr()
print(aRepr.repr(sys.stdin))         # 打印 '<stdin>'
```

```
<stdin>
```

