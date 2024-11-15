+++
title = "collections.abc --- 容器的抽象基类"
date = 2024-11-15T11:32:48+08:00
weight = 40
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/collections.abc.html](https://docs.python.org/zh-cn/3.13/library/collections.abc.html)
>
> 收录该文档的时间：`2024-11-15T11:32:48+08:00`

# `collections.abc` --- 容器的抽象基类

*Added in version 3.3:* 该模块曾是 [`collections`](https://docs.python.org/zh-cn/3.13/library/collections.html#module-collections) 模块的组成部分。

**源代码：** [Lib/_collections_abc.py](https://github.com/python/cpython/tree/3.13/Lib/_collections_abc.py)

------

​	本模块提供了一些 [抽象基类](https://docs.python.org/zh-cn/3.13/glossary.html#term-abstract-base-class)，它们可被用于测试一个类是否提供某个特定的接口；例如，它是否为 [hashable](https://docs.python.org/zh-cn/3.13/glossary.html#term-hashable) 或是否为 [mapping](https://docs.python.org/zh-cn/3.13/glossary.html#term-mapping) 等。

​	一个接口的 [`issubclass()`](https://docs.python.org/zh-cn/3.13/library/functions.html#issubclass) 或 [`isinstance()`](https://docs.python.org/zh-cn/3.13/library/functions.html#isinstance) 测试采用以下三种方式之一。

​	1) A newly written class can inherit directly from one of the abstract base classes. The class must supply the required abstract methods. The remaining mixin methods come from inheritance and can be overridden if desired. Other methods may be added as needed:

```
class C(Sequence):                      # 直接继承
    def __init__(self): ...             # ABC 所不需要的额外方法
    def __getitem__(self, index):  ...  # 需要的抽象方法
    def __len__(self):  ...             # 需要的抽象方法
    def count(self, value): ...         # 可选覆盖一个混入方法
```



```
>>> issubclass(C, Sequence)
True
>>> isinstance(C(), Sequence)
True
```

​	2) Existing classes and built-in classes can be registered as "virtual subclasses" of the ABCs. Those classes should define the full API including all of the abstract methods and all of the mixin methods. This lets users rely on [`issubclass()`](https://docs.python.org/zh-cn/3.13/library/functions.html#issubclass) or [`isinstance()`](https://docs.python.org/zh-cn/3.13/library/functions.html#isinstance) tests to determine whether the full interface is supported. The exception to this rule is for methods that are automatically inferred from the rest of the API:

```
class D:                                 # 无继承
    def __init__(self): ...              # ABC 所不需要的额外方法
    def __getitem__(self, index):  ...   # 抽象方法
    def __len__(self):  ...              # 抽象方法
    def count(self, value): ...          # 混入方法
    def index(self, value): ...          # 混入方法

Sequence.register(D)                     # 注册而非继承
```



```
>>> issubclass(D, Sequence)
True
>>> isinstance(D(), Sequence)
True
```

​	在这个例子中，`D` 类不需要定义 `__contains__`, `__iter__` 和 `__reversed__`，因为 [in 运算符](https://docs.python.org/zh-cn/3.13/reference/expressions.html#comparisons), [迭代](https://docs.python.org/zh-cn/3.13/glossary.html#term-iterable) 逻辑和 [`reversed()`](https://docs.python.org/zh-cn/3.13/library/functions.html#reversed) 函数会自动回退为使用 `__getitem__` 和 `__len__`。

​	3) Some simple interfaces are directly recognizable by the presence of the required methods (unless those methods have been set to [`None`](https://docs.python.org/zh-cn/3.13/library/constants.html#None)):

```
class E:
    def __iter__(self): ...
    def __next__(self): ...
```



```
>>> issubclass(E, Iterable)
True
>>> isinstance(E(), Iterable)
True
```

​	复杂的接口不支持最后这种技术手段因为接口并不只是作为方法名称存在。 接口指明了方法之间的语义和关系，这些是无法根据特定方法名称的存在推断出来的。 例如，知道一个类提供了 `__getitem__`, `__len__` 和 `__iter__` 并不足以区分 [`Sequence`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Sequence) 和 [`Mapping`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Mapping)。

*Added in version 3.9:* 这些抽象类现在都支持 `[]`。 参见 [GenericAlias 类型](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#types-genericalias) 和 [**PEP 585**](https://peps.python.org/pep-0585/)。



## 容器抽象基类

​	这个容器模块提供了以下 [ABCs](https://docs.python.org/zh-cn/3.13/glossary.html#term-abstract-base-class):

| 抽象基类                                                     | 继承自                                                       | 抽象方法                                                     | Mixin 方法                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`Container`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Container) [[1\]](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id18) |                                                              | `__contains__`                                               |                                                              |
| [`Hashable`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Hashable) [[1\]](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id18) |                                                              | `__hash__`                                                   |                                                              |
| [`Iterable`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Iterable) [[1\]](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id18) [[2\]](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id19) |                                                              | `__iter__`                                                   |                                                              |
| [`Iterator`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Iterator) [[1\]](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id18) | [`Iterable`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Iterable) | `__next__`                                                   | `__iter__`                                                   |
| [`Reversible`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Reversible) [[1\]](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id18) | [`Iterable`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Iterable) | `__reversed__`                                               |                                                              |
| [`Generator`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Generator) [[1\]](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id18) | [`Iterator`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Iterator) | `send`, `throw`                                              | `close`, `__iter__`, `__next__`                              |
| [`Sized`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Sized) [[1\]](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id18) |                                                              | `__len__`                                                    |                                                              |
| [`Callable`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Callable) [[1\]](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id18) |                                                              | `__call__`                                                   |                                                              |
| [`Collection`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Collection) [[1\]](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id18) | [`Sized`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Sized), [`Iterable`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Iterable), [`Container`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Container) | `__contains__`, `__iter__`, `__len__`                        |                                                              |
| [`Sequence`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Sequence) | [`Reversible`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Reversible), [`Collection`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Collection) | `__getitem__`, `__len__`                                     | `__contains__`, `__iter__`, `__reversed__`, `index`, and `count` |
| [`MutableSequence`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.MutableSequence) | [`Sequence`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Sequence) | `__getitem__`, `__setitem__`, `__delitem__`, `__len__`, `insert` | 继承了 [`Sequence`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Sequence) 的方法以及 `append`, `clear`, `reverse`, `extend`, `pop`, `remove` 和 `__iadd__` |
| [`ByteString`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.ByteString) | [`Sequence`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Sequence) | `__getitem__`, `__len__`                                     | 继承自 [`Sequence`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Sequence) 的方法 |
| [`Set`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Set) | [`Collection`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Collection) | `__contains__`, `__iter__`, `__len__`                        | `__le__`, `__lt__`, `__eq__`, `__ne__`, `__gt__`, `__ge__`, `__and__`, `__or__`, `__sub__`, `__xor__`, and `isdisjoint` |
| [`MutableSet`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.MutableSet) | [`Set`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Set) | `__contains__`, `__iter__`, `__len__`, `add`, `discard`      | 继承自 [`Set`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Set) 的方法以及 `clear`, `pop`, `remove`, `__ior__`, `__iand__`, `__ixor__`，和 `__isub__` |
| [`Mapping`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Mapping) | [`Collection`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Collection) | `__getitem__`, `__iter__`, `__len__`                         | `__contains__`, `keys`, `items`, `values`, `get`, `__eq__`, and `__ne__` |
| [`MutableMapping`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.MutableMapping) | [`Mapping`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Mapping) | `__getitem__`, `__setitem__`, `__delitem__`, `__iter__`, `__len__` | 继承自 [`Mapping`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Mapping) 的方法以及 `pop`, `popitem`, `clear`, `update`，和 `setdefault` |
| [`MappingView`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.MappingView) | [`Sized`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Sized) |                                                              | `__len__`                                                    |
| [`ItemsView`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.ItemsView) | [`MappingView`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.MappingView), [`Set`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Set) |                                                              | `__contains__`, `__iter__`                                   |
| [`KeysView`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.KeysView) | [`MappingView`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.MappingView), [`Set`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Set) |                                                              | `__contains__`, `__iter__`                                   |
| [`ValuesView`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.ValuesView) | [`MappingView`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.MappingView), [`Collection`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Collection) |                                                              | `__contains__`, `__iter__`                                   |
| [`Awaitable`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Awaitable) [[1\]](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id18) |                                                              | `__await__`                                                  |                                                              |
| [`Coroutine`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Coroutine) [[1\]](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id18) | [`Awaitable`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Awaitable) | `send`, `throw`                                              | `close`                                                      |
| [`AsyncIterable`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.AsyncIterable) [[1\]](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id18) |                                                              | `__aiter__`                                                  |                                                              |
| [`AsyncIterator`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.AsyncIterator) [[1\]](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id18) | [`AsyncIterable`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.AsyncIterable) | `__anext__`                                                  | `__aiter__`                                                  |
| [`AsyncGenerator`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.AsyncGenerator) [[1\]](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id18) | [`AsyncIterator`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.AsyncIterator) | `asend`, `athrow`                                            | `aclose`, `__aiter__`, `__anext__`                           |
| [`Buffer`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Buffer) [[1\]](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id18) |                                                              | `__buffer__`                                                 |                                                              |

​	附注

[1]([1](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id2),[2](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id3),[3](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id4),[4](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id6),[5](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id7),[6](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id8),[7](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id9),[8](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id10),[9](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id11),[10](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id12),[11](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id13),[12](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id14),[13](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id15),[14](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id16),[15](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id17))

​	这些 ABC 重写了 [`__subclasshook__()`](https://docs.python.org/zh-cn/3.13/library/abc.html#abc.ABCMeta.__subclasshook__) 以便支持通过验证所需的方法是否存在并且没有被设为 [`None`](https://docs.python.org/zh-cn/3.13/library/constants.html#None) 来测试一个接口。 这只适用于简单的接口。 更复杂的接口需要注册或者直接子类化。

[[2](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#id5)]

​	检查 `isinstance(obj, Iterable)` 是否侦测到被注册为 [`Iterable`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Iterable) 或者具有 [`__iter__()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#container.__iter__) 方法的类，但它不能侦测到使用 [`__getitem__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__getitem__) 方法进行迭代的类。 确定一个对象是否为 [iterable](https://docs.python.org/zh-cn/3.13/glossary.html#term-iterable) 的唯一可靠方式是调用 `iter(obj)`。

## 多项集抽象基类 -- 详细描述

## *class* collections.abc.**Container**

​	提供了 [`__contains__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__contains__) 方法的抽象基类。

## *class* collections.abc.**Hashable**

​	提供了 [`__hash__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__hash__) 方法的抽象基类。

## *class* collections.abc.**Sized**

​	用于提供 [`__len__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__len__) 方法的类的 ABC

## *class* collections.abc.**Callable**

​	用于提供 [`__call__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__call__) 方法的类的 ABC

​	有关如何在类型标注中使用 `Callable` 的详细信息请参阅 [标注可调用对象](https://docs.python.org/zh-cn/3.13/library/typing.html#annotating-callables)。

## *class* collections.abc.**Iterable**

​	用于提供 [`__iter__()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#container.__iter__) 方法的类的 ABC

​	检查 `isinstance(obj, Iterable)` 是否侦测到被注册为 [`Iterable`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Iterable) 或者具有 [`__iter__()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#container.__iter__) 方法的类，但它不能侦测到使用 [`__getitem__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__getitem__) 方法进行迭代的类。 确定一个对象是否为 [iterable](https://docs.python.org/zh-cn/3.13/glossary.html#term-iterable) 的唯一可靠方式是调用 `iter(obj)`。

## *class* collections.abc.**Collection**

​	集合了 Sized 和 Iterable 类的抽象基类。

*Added in version 3.6.*

## *class* collections.abc.**Iterator**

​	提供了 [`__iter__()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#iterator.__iter__) 和 [`__next__()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#iterator.__next__) 方法的抽象基类。参见 [iterator](https://docs.python.org/zh-cn/3.13/glossary.html#term-iterator) 的定义。

## *class* collections.abc.**Reversible**

​	用于同时提供了 [`__reversed__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__reversed__) 方法的可迭代类的 ABC

*Added in version 3.6.*

## *class* collections.abc.**Generator**

​	用于实现了 [**PEP 342**](https://peps.python.org/pep-0342/) 中定义的协议的 [generator](https://docs.python.org/zh-cn/3.13/glossary.html#term-generator) 类的 ABC，它通过 [`send()`](https://docs.python.org/zh-cn/3.13/reference/expressions.html#generator.send), [`throw()`](https://docs.python.org/zh-cn/3.13/reference/expressions.html#generator.throw) 和 [`close()`](https://docs.python.org/zh-cn/3.13/reference/expressions.html#generator.close) 方法对 [迭代器](https://docs.python.org/zh-cn/3.13/glossary.html#term-iterator) 进行了扩展。

​	有关在类型标注中使用 `Generator` 的详细信息请参阅 [标注生成器和协程](https://docs.python.org/zh-cn/3.13/library/typing.html#annotating-generators-and-coroutines)。

*Added in version 3.5.*

## *class* collections.abc.**Sequence**

## *class* collections.abc.**MutableSequence**

## *class* collections.abc.**ByteString**

​	只读的与可变的 [序列](https://docs.python.org/zh-cn/3.13/glossary.html#term-sequence) 的抽象基类。

​	实现注意事项：某些混入方法，如 [`__iter__()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#container.__iter__), [`__reversed__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__reversed__) 和 `index()`，会重复调用下层的 [`__getitem__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__getitem__) 方法。 因此，如果 [`__getitem__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__getitem__) 被实现为常数级访问速度，则混入方法的性能将为线性级；但是，如果下层的方法是线性的（例如链表就是如此），则混入方法的性能将为平方级并可能需要被重写。

*在 3.5 版本发生变更:* index() 方法支持 *stop* 和 *start* 参数。

*Deprecated since version 3.12, will be removed in version 3.14:* [`ByteString`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.ByteString) ABC 已被弃用。 当用于类型标注时，建议改为并集形式，如 `bytes | bytearray`，或 [`collections.abc.Buffer`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Buffer)。 当用作 ABC 时，建议改为 [`Sequence`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Sequence) 或 [`collections.abc.Buffer`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Buffer)。

## *class* collections.abc.**Set**

## *class* collections.abc.**MutableSet**

​	用于只读和可变 [集合](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#types-set) 的 ABC。

## *class* collections.abc.**Mapping**

## *class* collections.abc.**MutableMapping**

​	只读的与可变的 [映射](https://docs.python.org/zh-cn/3.13/glossary.html#term-mapping) 的抽象基类。

## *class* collections.abc.**MappingView**

## *class* collections.abc.**ItemsView**

## *class* collections.abc.**KeysView**

## *class* collections.abc.**ValuesView**

​	映射及其键和值的 [视图](https://docs.python.org/zh-cn/3.13/glossary.html#term-dictionary-view) 的抽象基类。

## *class* collections.abc.**Awaitable**

​	针对 [awaitable](https://docs.python.org/zh-cn/3.13/glossary.html#term-awaitable) 对象的 ABC，它可被用于 [`await`](https://docs.python.org/zh-cn/3.13/reference/expressions.html#await) 表达式。 根据惯例所有实现都必须提供 [`__await__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__await__) 方法。

[协程](https://docs.python.org/zh-cn/3.13/glossary.html#term-coroutine) 对象和 [`Coroutine`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Coroutine) ABC 的实例都是这个 ABC 的实例。

​	备注

 

​	在 CPython 中，基于生成器的协程 (使用 [`@types.coroutine`](https://docs.python.org/zh-cn/3.13/library/types.html#types.coroutine) 装饰的 [生成器](https://docs.python.org/zh-cn/3.13/glossary.html#term-generator)) 都是 *可等待对象*，即使它们没有 [`__await__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__await__) 方法。 对它们使用 `isinstance(gencoro, Awaitable)` 将返回 `False`。 请使用 [`inspect.isawaitable()`](https://docs.python.org/zh-cn/3.13/library/inspect.html#inspect.isawaitable) 来检测它们。

*Added in version 3.5.*

## *class* collections.abc.**Coroutine**

​	用于 [coroutine](https://docs.python.org/zh-cn/3.13/glossary.html#term-coroutine) 兼容类的 ABC。 实现了如下定义在 [协程对象](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#coroutine-objects) 里的方法: [`send()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#coroutine.send), [`throw()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#coroutine.throw) 和 [`close()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#coroutine.close)。 根据惯例所有实现都还需要实现 [`__await__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__await__)。 所有的 [`Coroutine`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Coroutine) 实例同时也是 [`Awaitable`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Awaitable) 的实例。

​	备注

 

​	在 CPython 中，基于生成器的协程 (使用 [`@types.coroutine`](https://docs.python.org/zh-cn/3.13/library/types.html#types.coroutine) 装饰的 [生成器](https://docs.python.org/zh-cn/3.13/glossary.html#term-generator)) 都是 *可等待对象*，即使它们没有 [`__await__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__await__) 方法。 对它们使用 `isinstance(gencoro, Coroutine)` 将返回 `False`。 请使用 [`inspect.isawaitable()`](https://docs.python.org/zh-cn/3.13/library/inspect.html#inspect.isawaitable) 来检测它们。

​	有关在类型标注中使用 `Coroutine` 的详细信息请参阅 [标注生成器和协程](https://docs.python.org/zh-cn/3.13/library/typing.html#annotating-generators-and-coroutines)。 类型形参的变化和顺序与 [`Generator`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Generator) 的相对应。

*Added in version 3.5.*

## *class* collections.abc.**AsyncIterable**

​	针对提供了 `__aiter__` 方法的类的 ABC。 另请参阅 [asynchronous iterable](https://docs.python.org/zh-cn/3.13/glossary.html#term-asynchronous-iterable) 的定义。

*Added in version 3.5.*

## *class* collections.abc.**AsyncIterator**

​	提供了 `__aiter__` 和 `__anext__` 方法的抽象基类。参见 [asynchronous iterator](https://docs.python.org/zh-cn/3.13/glossary.html#term-asynchronous-iterator) 的定义。

*Added in version 3.5.*

## *class* collections.abc.**AsyncGenerator**

​	针对实现了在 [**PEP 525**](https://peps.python.org/pep-0525/) 和 [**PEP 492**](https://peps.python.org/pep-0492/) 中定义的协议的 [asynchronous generator](https://docs.python.org/zh-cn/3.13/glossary.html#term-asynchronous-generator) 类的 ABC。

​	有关在类型标注中使用 `AsyncGenerator` 的详细信息请参阅 [标注生成器和协程](https://docs.python.org/zh-cn/3.13/library/typing.html#annotating-generators-and-coroutines)。

*Added in version 3.6.*

## *class* collections.abc.**Buffer**

​	针对提供 [`__buffer__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__buffer__) 方法的类的 ABC，实现了 [缓冲区协议](https://docs.python.org/zh-cn/3.13/c-api/buffer.html#bufferobjects)。 参见 [**PEP 688**](https://peps.python.org/pep-0688/)。

*Added in version 3.12.*

## 例子和配方

​	ABC 允许我们询问类或实例是否提供特定的功能，例如:

```
size = None
if isinstance(myvar, collections.abc.Sized):
    size = len(myvar)
```

​	有些 ABC 还适用于作为混入类，这可以更容易地开发支持容器 API 的类。 例如，要写一个支持完整 [`Set`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Set) API 的类，只需要提供三个下层抽象方法: [`__contains__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__contains__), [`__iter__()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#container.__iter__) 和 [`__len__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__len__)。 ABC 会提供其余的方法如 `__and__()` 和 [`isdisjoint()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#frozenset.isdisjoint):

```
class ListBasedSet(collections.abc.Set):
    ''' 空间重于速度并且不要求集合元素可哈希的
        替代性集合实现。 '''
    def __init__(self, iterable):
        self.elements = lst = []
        for value in iterable:
            if value not in lst:
                lst.append(value)

    def __iter__(self):
        return iter(self.elements)

    def __contains__(self, value):
        return value in self.elements

    def __len__(self):
        return len(self.elements)

s1 = ListBasedSet('abcdef')
s2 = ListBasedSet('defghi')
overlap = s1 & s2            # 自动支持 __and__() 方法
```

​	当把 [`Set`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Set) 和 [`MutableSet`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.MutableSet) 用作混入类时需注意：

1. 由于某些集合操作会创建新的集合，默认的混入方法需要一种根据 [iterable](https://docs.python.org/zh-cn/3.13/glossary.html#term-iterable) 创建新实例的方式。 类构造器应当具有 `ClassName(iterable)` 形式的签名。 这样它将被重构为一个执行 `_from_iterable()` 的内部 [`classmethod`](https://docs.python.org/zh-cn/3.13/library/functions.html#classmethod)，该方法会调用 `cls(iterable)` 来产生一个新的集合。 如果 [`Set`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Set) 混入类在具有不同构造器签名的类中被使用，你将需要通过一个能根据可迭代对象参数构造新实例的类方法或常规方法来重写 `_from_iterable()`。
2. 要重写比较运算（应该是为了提高速度，因为其语义是固定的），请重新定义 [`__le__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__le__) 和 [`__ge__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__ge__)，然后其他运算将自动跟进。
3. [`Set`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Set) 混入类提供了一个 `_hash()` 方法为集合计算哈希值；但是，[`__hash__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__hash__) 没有被定义因为并非所有集合都是 [hashable](https://docs.python.org/zh-cn/3.13/glossary.html#term-hashable) 或不可变对象。 要使用混入类为集合添加可哈希性，请同时继承 [`Set()`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Set) 和 [`Hashable()`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Hashable)，然后定义 `__hash__ = Set._hash`。

​	参见

- [OrderedSet recipe](https://code.activestate.com/recipes/576694/) 是基于 [`MutableSet`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.MutableSet) 构建的一个示例。
- 对于抽象基类，参见 [`abc`](https://docs.python.org/zh-cn/3.13/library/abc.html#module-abc) 模块和 [**PEP 3119**](https://peps.python.org/pep-3119/)。
