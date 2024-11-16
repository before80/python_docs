+++
title = "types --- 动态类型创建和内置类型名称"
date = 2024-11-15T11:18:41+08:00
weight = 90
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/types.html](https://docs.python.org/zh-cn/3.13/library/types.html)
>
> 收录该文档的时间：`2024-11-15T11:18:41+08:00`

# `types` --- 动态类型创建和内置类型名称

**源代码:** [Lib/types.py](https://github.com/python/cpython/tree/3.13/Lib/types.py)

------

​	此模块定义了一些工具函数，用于协助动态创建新的类型。

​	它还为某些对象类型定义了名称，这些名称由标准 Python 解释器所使用，但并不像内置的 [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int) 或 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 那样对外公开。

​	最后，它还额外提供了一些类型相关但重要程度不足以作为内置对象的工具类和函数。

## 动态类型创建

## types.**new_class**(*name*, *bases=()*, *kwds=None*, *exec_body=None*)

​	使用适当的元类动态地创建一个类对象。

​	前三个参数是组成类定义头的部件：类名称，基类 (有序排列)，关键字参数 (例如 `metaclass`)。

*exec_body* 参数是一个回调函数，用于填充新创建类的命名空间。 它应当接受类命名空间作为其唯一的参数并使用类内容直接更新命名空间。 如果未提供回调函数，则它就等效于传入 `lambda ns: None`。

> Added in version 3.3.
>

## types.**prepare_class**(*name*, *bases=()*, *kwds=None*)

​	计算适当的元类并创建类命名空间。

​	参数是组成类定义头的部件：类名称，基类 (有序排列) 以及关键字参数 (例如 `metaclass`)。

​	返回值是一个 3 元组: `metaclass, namespace, kwds`

*metaclass* 是适当的元类，*namespace* 是预备好的类命名空间而 *kwds* 是所传入 *kwds* 参数移除每个 `'metaclass'` 条目后的已更新副本。 如果未传入 *kwds* 参数，这将为一个空字典。

> Added in version 3.3.
>

*在 3.6 版本发生变更:* 所返回元组中 `namespace` 元素的默认值已被改变。 现在当元类没有 `__prepare__` 方法时将会使用一个保留插入顺序的映射。

​	参见

## [元类](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#metaclasses)

​	这些函数所支持的类创建过程的完整细节

## [**PEP 3115**](https://peps.python.org/pep-3115/) - Python 3000 中的元类

​	引入 `__prepare__` 命名空间钩子

## types.**resolve_bases**(*bases*)

​	动态地解析 MRO 条目，具体描述见 [**PEP 560**](https://peps.python.org/pep-0560/)。

​	此函数会在 *bases* 中查找不是 [`type`](https://docs.python.org/zh-cn/3.13/library/functions.html#type) 的实例的项，并返回一个元组，其中每个具有 [`__mro_entries__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__mro_entries__) 方法的此种对象将被替换为调用该方法解包后的结果。 如果一个 *bases* 项是 [`type`](https://docs.python.org/zh-cn/3.13/library/functions.html#type) 的实例，或它不具有 `__mro_entries__()` 方法 ，则它将不加改变地被包括在返回的元组中。

> Added in version 3.7.
>

## types.**get_original_bases**(*cls*, */*)

​	在 [`__mro_entries__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__mro_entries__) 方法在任何基类上被调用之前返回最初是作为 *cls* 的基类给出的对象元组（根据 [**PEP 560**](https://peps.python.org/pep-0560/) 所描述的机制）。 这在对 [泛型](https://docs.python.org/zh-cn/3.13/library/typing.html#user-defined-generics) 进行内省时很有用处。

​	对于具有 `__orig_bases__` 属性的类，此函数将返回 `cls.__orig_bases__` 的值。 对于没有 `__orig_bases__` 属性的类，则将返回 [`cls.__bases__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#type.__bases__)。

​	示例:

```
from typing import TypeVar, Generic, NamedTuple, TypedDict

T = TypeVar("T")
class Foo(Generic[T]): ...
class Bar(Foo[int], float): ...
class Baz(list[str]): ...
Eggs = NamedTuple("Eggs", [("a", int), ("b", str)])
Spam = TypedDict("Spam", {"a": int, "b": str})

assert Bar.__bases__ == (Foo, float)
assert get_original_bases(Bar) == (Foo[int], float)

assert Baz.__bases__ == (list,)
assert get_original_bases(Baz) == (list[str],)

assert Eggs.__bases__ == (tuple,)
assert get_original_bases(Eggs) == (NamedTuple,)

assert Spam.__bases__ == (dict,)
assert get_original_bases(Spam) == (TypedDict,)

assert int.__bases__ == (object,)
assert get_original_bases(int) == (object,)
```

> Added in version 3.12.
>

​	参见

 

[**PEP 560**](https://peps.python.org/pep-0560/) - 对 typing 模块和泛型类型的核心支持

## 标准解释器类型

​	此模块为许多类型提供了实现 Python 解释器所要求的名称。 它刻意地避免了包含某些仅在处理过程中偶然出现的类型，例如 `listiterator` 类型。

​	此种名称的典型应用如 [`isinstance()`](https://docs.python.org/zh-cn/3.13/library/functions.html#isinstance) 或 [`issubclass()`](https://docs.python.org/zh-cn/3.13/library/functions.html#issubclass) 检测。

​	如果你要实例化这些类型中的任何一种，请注意其签名在不同 Python 版本之间可能出现变化。

​	以下类型有相应的标准名称定义：

## types.**NoneType**

[`None`](https://docs.python.org/zh-cn/3.13/library/constants.html#None) 的类型。

> Added in version 3.10.
>

## types.**FunctionType**

## types.**LambdaType**

​	用户自定义函数以及由 [`lambda`](https://docs.python.org/zh-cn/3.13/reference/expressions.html#lambda) 表达式所创建函数的类型。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `function.__new__` 并附带参数 `code`。

​	此审计事件只会被函数对象的直接实例化引发，而不会被普通编译所引发。

## types.**GeneratorType**

[generator](https://docs.python.org/zh-cn/3.13/glossary.html#term-generator) 迭代器对象的类型，由生成器函数创建。

## types.**CoroutineType**

[coroutine](https://docs.python.org/zh-cn/3.13/glossary.html#term-coroutine) 对象的类型，由 [`async def`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#async-def) 函数创建。

> Added in version 3.5.
>

## types.**AsyncGeneratorType**

[asynchronous generator](https://docs.python.org/zh-cn/3.13/glossary.html#term-asynchronous-generator) 迭代器对象的类型，由异步生成器函数创建。

> Added in version 3.6.
>

## *class* types.**CodeType**(***kwargs*)

[代码对象](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#code-objects) 例如 [`compile()`](https://docs.python.org/zh-cn/3.13/library/functions.html#compile) 返回值的类型。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `code.__new__` 并附带参数 `code`, `filename`, `name`, `argcount`, `posonlyargcount`, `kwonlyargcount`, `nlocals`, `stacksize`, `flags`。

​	请注意被审计的参数可能与初始化代码所要求的名称或位置不相匹配。 审计事件只会被代码对象的直接实例化引发，而不会被普通编译所引发。

## types.**CellType**

​	单元对象的类型：这种对象被用作函数中 [闭包变量](https://docs.python.org/zh-cn/3.13/glossary.html#term-closure-variable) 的容器。

> Added in version 3.8.
>

## types.**MethodType**

​	用户自定义类实例方法的类型。

## types.**BuiltinFunctionType**

## types.**BuiltinMethodType**

​	内置函数例如 [`len()`](https://docs.python.org/zh-cn/3.13/library/functions.html#len) 或 [`sys.exit()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.exit) 以及内置类方法的类型。 （这里所说的“内置”是指“以 C 语言编写”。）

## types.**WrapperDescriptorType**

​	某些内置数据类型和基类的方法的类型，例如 [`object.__init__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__init__) 或 [`object.__lt__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__lt__)。

> Added in version 3.7.
>

## types.**MethodWrapperType**

​	某些内置数据类型和基类的 *绑定* 方法的类型。 例如 `object().__str__` 所属的类型。

> Added in version 3.7.
>

## types.**NotImplementedType**

[`NotImplemented`](https://docs.python.org/zh-cn/3.13/library/constants.html#NotImplemented) 的类型。

> Added in version 3.10.
>

## types.**MethodDescriptorType**

​	某些内置数据类型方法例如 [`str.join()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str.join) 的类型。

> Added in version 3.7.
>

## types.**ClassMethodDescriptorType**

​	某些内置数据类型 *非绑定* 类方法例如 `dict.__dict__['fromkeys']` 的类型。

> Added in version 3.7.
>

## *class* types.**ModuleType**(*name*, *doc=None*)

[模块](https://docs.python.org/zh-cn/3.13/glossary.html#term-module) 的类型。 构造器接受待创建模块的名称并以其 [docstring](https://docs.python.org/zh-cn/3.13/glossary.html#term-docstring) 作为可选参数。

​	参见

## [模块对象的文档](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#module-objects)

​	提供了有关可在 `ModuleType` 的实例上找到的特殊属性的详情。

## [`importlib.util.module_from_spec()`](https://docs.python.org/zh-cn/3.13/library/importlib.html#importlib.util.module_from_spec)

​	使用 `ModuleType` 构造器创建的模块在被创建时将有许多特殊属性被设置或重设其默认值。 `module_from_spec()` 提供了一种创建 `ModuleType` 实例的更健壮方式，可确保各个属性被正确地设置。

## types.**EllipsisType**

[`Ellipsis`](https://docs.python.org/zh-cn/3.13/library/constants.html#Ellipsis) 的类型。

> Added in version 3.10.
>

## *class* types.**GenericAlias**(*t_origin*, *t_args*)

[形参化泛型](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#types-genericalias) 的类型，例如 `list[int]`。

`t_origin` 应当是一个非形参化的泛型类，例如 `list`, `tuple` 或 `dict`。 `t_args` 应当是一个形参化 `t_origin` 的 [`tuple`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#tuple) (长度可以为 1):



``` python
>>> from types import GenericAlias

>>> list[int] == GenericAlias(list, (int,))
True
>>> dict[str, int] == GenericAlias(dict, (str, int))
True
```

> Added in version 3.9.
>

*在 3.9.2 版本发生变更:* 此类型现在可以被子类化。

​	参见

## [泛用别名类型](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#types-genericalias)

​	有关 `types.GenericAlias` 实例的详细文档

## [**PEP 585**](https://peps.python.org/pep-0585/) - 标准多项集中的类型提示泛型

​	引入 `types.GenericAlias` 类

## *class* types.**UnionType**

[合并类型表达式](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#types-union) 的类型。

> Added in version 3.10.
>

## *class* types.**TracebackType**(*tb_next*, *tb_frame*, *tb_lasti*, *tb_lineno*)

​	回溯对象的类型，如在 `sys.exception().__traceback__` 中找到的一样。

​	请查看 [语言参考](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#traceback-objects) 了解可用属性和操作的细节，以及动态地创建回溯对象的指南。

## types.**FrameType**

[帧对象](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame-objects) 的类型，例如当 `tb` 是一个回溯对象时 [`tb.tb_frame`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#traceback.tb_frame) 中的对象。

## types.**GetSetDescriptorType**

​	使用 `PyGetSetDef` 在扩展模块中定义的对象的类型，例如 [`FrameType.f_locals`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#frame.f_locals) 或 `array.array.typecode`。 此类型被用作对象属性的描述器；它的目的与 [`property`](https://docs.python.org/zh-cn/3.13/library/functions.html#property) 类型相同，但专门针对在扩展模块中定义的类。

## types.**MemberDescriptorType**

​	使用 `PyMemberDef` 在扩展模块中定义的对象的类型，例如 `datetime.timedelta.days`。 此类型被用作使用标准转换函数的简单 C 数据成员的描述器；它的目的与 [`property`](https://docs.python.org/zh-cn/3.13/library/functions.html#property) 类型相同，但专门针对在扩展模块中定义的类。

​	此外，当一个类定义了 [`__slots__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__slots__) 属性时，对于每个槽位，都将添加一个 `MemberDescriptorType` 的实例作为该类上的属性。 这将允许槽位显示在类的 [`__dict__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#type.__dict__) 中。

**CPython 实现细节：** 在 Python 的其它实现中，此类型可能与 `GetSetDescriptorType` 完全相同。

## *class* types.**MappingProxyType**(*mapping*)

​	一个映射的只读代理。 它提供了对映射条目的动态视图，这意味着当映射发生改变时，视图会反映这些改变。

> Added in version 3.3.
>

*在 3.9 版本发生变更:* 更新为支持 [**PEP 584**](https://peps.python.org/pep-0584/) 所新增的合并 (`|`) 运算符，它会简单地委托给下层的映射。

## **key in proxy**

​	如果下层的映射中存在键 *key* 则返回 `True`，否则返回 `False`。

## **proxy[key]**

​	返回下层的映射中以 *key* 为键的项。 如果下层的映射中不存在键 *key* 则引发 [`KeyError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#KeyError)。

## **iter(proxy)**

​	返回由下层映射的键为元素的迭代器。 这是 `iter(proxy.keys())` 的快捷方式。

## **len(proxy)**

​	返回下层映射中的项数。

## **copy**()

​	返回下层映射的浅拷贝。

## **get**(*key*[, *default*])

​	如果 *key* 存在于下层映射中则返回 *key* 的值，否则返回 *default*。 如果 *default* 未给出则默认为 `None`，因而此方法绝不会引发 [`KeyError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#KeyError)。

## **items**()

​	返回由下层映射的项 (`(键, 值)` 对) 组成的一个新视图。

## **keys**()

​	返回由下层映射的键组成的一个新视图。

## **values**()

​	返回由下层映射的值组成的一个新视图。

## **reversed(proxy)**

​	返回一个包含下层映射的键的反向迭代器。

> Added in version 3.9.
>

## **hash(proxy)**

​	返回下层映射的哈希值。

> Added in version 3.12.
>

## *class* types.**CapsuleType**

[capsule 对象](https://docs.python.org/zh-cn/3.13/c-api/capsule.html#capsules) 的类型。

> Added in version 3.13.
>

## 附加工具类和函数

## *class* types.**SimpleNamespace**

​	一个简单的 [`object`](https://docs.python.org/zh-cn/3.13/library/functions.html#object) 子类，提供了访问其命名空间的属性，以及一个有意义的 repr。

​	与 [`object`](https://docs.python.org/zh-cn/3.13/library/functions.html#object) 不同的是，对于 `SimpleNamespace` 你可以添加和移除属性。

[`SimpleNamespace`](https://docs.python.org/zh-cn/3.13/library/types.html#types.SimpleNamespace) 对象可以使用与 [`dict`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#dict) 相同的方式来初始化：关键字参数、单个位置参数或者两者兼有。 当使用关键字参数来初始化时，参数值会被直接加入到下层的命名空间。 而当使用一个位置参数来初始化时，下层的命名空间将以来自该参数（为一个映射对象或是产生键值对的 [iterable](https://docs.python.org/zh-cn/3.13/glossary.html#term-iterable) 对象）的键值对来更新。 所有这样的键都必须为字符串。

​	此类型大致等价于以下代码:

```
class SimpleNamespace:
    def __init__(self, mapping_or_iterable=(), /, **kwargs):
        self.__dict__.update(mapping_or_iterable)
        self.__dict__.update(kwargs)

    def __repr__(self):
        items = (f"{k}={v!r}" for k, v in self.__dict__.items())
        return "{}({})".format(type(self).__name__, ", ".join(items))

    def __eq__(self, other):
        if isinstance(self, SimpleNamespace) and isinstance(other, SimpleNamespace):
           return self.__dict__ == other.__dict__
        return NotImplemented
```

`SimpleNamespace` 可被用于替代 `class NS: pass`。 但是，对于结构化记录类型则应改用 [`namedtuple()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.namedtuple)。

`SimpleNamespace` 对象受到 [`copy.replace()`](https://docs.python.org/zh-cn/3.13/library/copy.html#copy.replace) 的支持。

> Added in version 3.3.
>

*在 3.9 版本发生变更:* repr 中的属性顺序由字母顺序改为插入顺序 (类似 `dict`)。

*在 3.13 版本发生变更:* 增加了对可选的位置参数的支持。

## types.**DynamicClassAttribute**(*fget=None*, *fset=None*, *fdel=None*, *doc=None*)

​	在类上访问 __getattr__ 的路由属性。

​	这是一个描述器，用于定义通过实例与通过类访问时具有不同行为的属性。 当实例访问时保持正常行为，但当类访问属性时将被路由至类的 __getattr__ 方法；这是通过引发 AttributeError 来完成的。

​	这允许有在实例上激活的特性属性，同时又有在类上的同名虚拟属性 (一个例子请参见 [`enum.Enum`](https://docs.python.org/zh-cn/3.13/library/enum.html#enum.Enum))。

> Added in version 3.4.
>

## 协程工具函数

## types.**coroutine**(*gen_func*)

​	此函数可将 [generator](https://docs.python.org/zh-cn/3.13/glossary.html#term-generator) 函数转换为一个返回基于生成器的协程的 [coroutine function](https://docs.python.org/zh-cn/3.13/glossary.html#term-coroutine-function)。 基于生成器的协程仍然属于 [generator iterator](https://docs.python.org/zh-cn/3.13/glossary.html#term-generator-iterator)，但同时又可被视为 [coroutine](https://docs.python.org/zh-cn/3.13/glossary.html#term-coroutine) 对象兼 [awaitable](https://docs.python.org/zh-cn/3.13/glossary.html#term-awaitable)。 不过，它没有必要实现 [`__await__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__await__) 方法。

​	如果 *gen_func* 是一个生成器函数，它将被原地修改。

​	如果 *gen_func* 不是一个生成器函数，则它会被包装。 如果它返回一个 [`collections.abc.Generator`](https://docs.python.org/zh-cn/3.13/library/collections.abc.html#collections.abc.Generator) 的实例，该实例将被包装在一个 *awaitable* 代理对象中。 所有其他对象类型将被原样返回。

> Added in version 3.5.
>
