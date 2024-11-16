+++
title = "abc --- 抽象基类"
date = 2024-11-15T21:12:39+08:00
weight = 80
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/abc.html](https://docs.python.org/zh-cn/3.13/library/abc.html)
>
> 收录该文档的时间：`2024-11-15T21:12:39+08:00`

# `abc` --- 抽象基类

**源代码：** [Lib/abc.py](https://github.com/python/cpython/tree/3.13/Lib/abc.py)

------

​	该模块提供了在 Python 中定义 [抽象基类]({{< ref "/glossary/idx#term-abstract-base-class" >}}) (ABC) 的组件，在 [**PEP 3119**](https://peps.python.org/pep-3119/) 中已有概述。查看 PEP 文档了解为什么需要在 Python 中增加这个模块。（也可查看 [**PEP 3141**](https://peps.python.org/pep-3141/) 以及 [`numbers`]({{< ref "/library/numeric/numbers#module-numbers" >}}) 模块了解基于 ABC 的数字类型继承关系。）

[`collections`](https://docs.python.org/zh-cn/3.13/library/collections.html#module-collections) 模块中有一些派生自 ABC 的实体类；当然，这些类还可以进一步被派生。 此外，[`collections.abc`]({{< ref "/library/datatypes/collections_abc#module-collections.abc" >}}) 子模块中有一些可被用于测试一个类或实例是否提供了特定接口的 ABC，例如，它是否为 [hashable]({{< ref "/glossary/idx#term-hashable" >}}) 或者是否为 [mapping]({{< ref "/glossary/idx#term-mapping" >}}) 等。

​	该模块提供了一个元类 [`ABCMeta`]({{< ref "/library/python/abc#abc.ABCMeta" >}})，可以用来定义抽象类，另外还提供一个工具类 [`ABC`]({{< ref "/library/python/abc#abc.ABC" >}})，可以用它以继承的方式定义抽象基类。

## *class* abc.**ABC**

​	一个使用 [`ABCMeta`]({{< ref "/library/python/abc#abc.ABCMeta" >}}) 作为元类的辅助类。 使用这个类，可以通过简单地从 `ABC` 派生的方式创建抽象基类，这将避免时常令人混淆的元类用法，例如:

```
from abc import ABC

class MyABC(ABC):
    pass
```

​	请注意 `ABC` 的类型仍然是 [`ABCMeta`]({{< ref "/library/python/abc#abc.ABCMeta" >}})，因此继承 `ABC` 仍然需要考虑使用元类的注意事项，比如可能会导致元类冲突的多重继承。 你也可以通过传入 metaclass 关键字并直接使用 `ABCMeta` 来定义抽象基类，例如:

```
from abc import ABCMeta

class MyABC(metaclass=ABCMeta):
    pass
```

> Added in version 3.4.
>

## *class* abc.**ABCMeta**

​	用于定义抽象基类（ABC）的元类。

​	使用该元类以创建抽象基类。抽象基类可以像 mix-in 类一样直接被子类继承。你也可以将不相关的具体类（包括内建类）和抽象基类注册为“抽象子类” —— 这些类以及它们的子类会被内建函数 [`issubclass()`]({{< ref "/library/functions#issubclass" >}}) 识别为对应的抽象基类的子类，但是该抽象基类不会出现在其 MRO（Method Resolution Order，方法解析顺序）中，抽象基类中实现的方法也不可调用（即使通过 [`super()`]({{< ref "/library/functions#super" >}}) 调用也不行）。[[1\]]({{< ref "/library/python/abc#id2" >}})

​	使用 `ABCMeta` 元类创建的类具有以下方法：

## **register**(*subclass*)

​	将 *subclass* 注册为该 ABC 的“虚拟子类”。 例如:

```
from abc import ABC

class MyABC(ABC):
    pass

MyABC.register(tuple)

assert issubclass(tuple, MyABC)
assert isinstance((), MyABC)
```

*在 3.3 版本发生变更:* 返回注册的子类，使其能够作为类装饰器。

*在 3.4 版本发生变更:* 要检测对 `register()` 的调用，你可以使用 [`get_cache_token()`]({{< ref "/library/python/abc#abc.get_cache_token" >}}) 函数。

​	你也可以在虚基类中重写这个方法。

## **__subclasshook__**(*subclass*)

​	（必须定义为类方法。）

​	检查 *subclass* 是否为该 ABC 的子类。 这意味着你可以进一步定制 [`issubclass()`]({{< ref "/library/functions#issubclass" >}}) 的行为而无需在每个你希望作为该 ABC 的子类的类上调用 [`register()`]({{< ref "/library/python/abc#abc.ABCMeta.register" >}})。 （这个类方法是在该 ABC 的 [`__subclasscheck__()`]({{< ref "/reference/datamodel#type.__subclasscheck__" >}}) 方法上被调用的。）

​	该方法应为返回 `True` , `False` 或 [`NotImplemented`]({{< ref "/library/constants#NotImplemented" >}}) 。如果返回 `True` ，则 *子类* 被视为该抽象基类的子类 。如果返回 `False` ，则 *子类* 不被视为此抽象基类的子类 ，即使它通常是。 如果返回 `NotImplemented` ，则继续按照常规机制检查子类 。

​	为了对这些概念做一演示，请看以下定义 ABC 的示例：

```
class Foo:
    def __getitem__(self, index):
        ...
    def __len__(self):
        ...
    def get_iterator(self):
        return iter(self)

class MyIterable(ABC):

    @abstractmethod
    def __iter__(self):
        while False:
            yield None

    def get_iterator(self):
        return self.__iter__()

    @classmethod
    def __subclasshook__(cls, C):
        if cls is MyIterable:
            if any("__iter__" in B.__dict__ for B in C.__mro__):
                return True
        return NotImplemented

MyIterable.register(Foo)
```

​	ABC `MyIterable` 将标准的迭代方法 [`__iter__()`]({{< ref "/library/stdtypes#iterator.__iter__" >}}) 定义为一个抽象方法。 这里给出的实现仍可在子类中被调用。 `get_iterator()` 方法也是 `MyIterable` 抽象基类的一部分，但它并非必须被非抽象的派生类重写。

​	这里定义的 [`__subclasshook__()`]({{< ref "/library/python/abc#abc.ABCMeta.__subclasshook__" >}}) 类方法指明任何在其 [`__dict__`]({{< ref "/reference/datamodel#object.__dict__" >}}) (或在其通过 [`__mro__`]({{< ref "/reference/datamodel#type.__mro__" >}}) 列表访问的基类) 中具有 [`__iter__()`]({{< ref "/library/stdtypes#iterator.__iter__" >}}) 方法的类也都会被视为 `MyIterable`。

​	最后，末尾的行使得 `Foo` 成为 `MyIterable` 的一个虚子类，即使它没有定义 [`__iter__()`]({{< ref "/library/stdtypes#iterator.__iter__" >}}) 方法（它使用了以 [`__len__()`]({{< ref "/reference/datamodel#object.__len__" >}}) 和 [`__getitem__()`]({{< ref "/reference/datamodel#object.__getitem__" >}}) 术语定义的旧式可迭代协议）。 注意这将不会使 `get_iterator` 成为对 `Foo` 可用的方法，所以它将被单独地提供。

`abc` 模块还提供了下列装饰器：

## @abc.**abstractmethod**

​	用于声明抽象方法的装饰器。

​	使用此装饰器要求类的元类是 [`ABCMeta`]({{< ref "/library/python/abc#abc.ABCMeta" >}}) 或是其派生类。 一个具有派生自 `ABCMeta` 的元类的类无法被实例化，除非它全部的抽象方法和特征属性均已被重载。 抽象方法可通过任何普通的 'super' 调用机制来调用。 `abstractmethod()` 可被用于声明特征属性和描述器的抽象方法。

​	动态地添加抽象方法到一个类，或尝试在方法或类被创建后修改其抽象状态等操作仅在使用 [`update_abstractmethods()`]({{< ref "/library/python/abc#abc.update_abstractmethods" >}}) 函数时受到支持。 `abstractmethod()` 只会影响使用常规继承所派生的子类；通过 ABC 的 [`register()`]({{< ref "/library/python/abc#abc.ABCMeta.register" >}}) 方法注册的“虚子类”不会受到影响。

​	当 `abstractmethod()` 与其他方法描述器配合应用时，它应当被应用为最内层的装饰器，如以下用法示例所示:

```
class C(ABC):
    @abstractmethod
    def my_abstract_method(self, arg1):
        ...
    @classmethod
    @abstractmethod
    def my_abstract_classmethod(cls, arg2):
        ...
    @staticmethod
    @abstractmethod
    def my_abstract_staticmethod(arg3):
        ...

    @property
    @abstractmethod
    def my_abstract_property(self):
        ...
    @my_abstract_property.setter
    @abstractmethod
    def my_abstract_property(self, val):
        ...

    @abstractmethod
    def _get_x(self):
        ...
    @abstractmethod
    def _set_x(self, val):
        ...
    x = property(_get_x, _set_x)
```

​	为了正确地与抽象基类机制互操作，描述器必须使用 `__isabstractmethod__` 将自身标识为抽象的。 通常，如果组成描述器的任一方法是抽象的，那么此属性就应为 `True`。 例如，Python 的内置 [`property`]({{< ref "/library/functions#property" >}}) 所做的就等价于:

```
class Descriptor:
    ...
    @property
    def __isabstractmethod__(self):
        return any(getattr(f, '__isabstractmethod__', False) for
                   f in (self._fget, self._fset, self._fdel))
```

​	备注

 

​	不同于 Java 抽象方法，这些抽象方法可能具有一个实现。 这个实现可在重写它的类上通过 [`super()`]({{< ref "/library/functions#super" >}}) 机制来调用。 这在使用协作多重继承的框架中可以被用作 super 调用的一个终点。

`abc` 模块还支持下列旧式装饰器：

## @abc.**abstractclassmethod**

> Added in version 3.2.
>

*自 3.3 版本弃用:* 现在可以让 [`classmethod`]({{< ref "/library/functions#classmethod" >}}) 配合 [`abstractmethod()`]({{< ref "/library/python/abc#abc.abstractmethod" >}}) 使用，使得此装饰器变得冗余。

​	内置 [`classmethod()`]({{< ref "/library/functions#classmethod" >}}) 的子类，指明一个抽象类方法。 在其他方面它都类似于 [`abstractmethod()`]({{< ref "/library/python/abc#abc.abstractmethod" >}})。

​	这个特例已被弃用，因为现在当 [`classmethod()`]({{< ref "/library/functions#classmethod" >}}) 装饰器应用于抽象方法时它会被正确地标识为抽象的:

```
class C(ABC):
    @classmethod
    @abstractmethod
    def my_abstract_classmethod(cls, arg):
        ...
```

## @abc.**abstractstaticmethod**

> Added in version 3.2.
>

*自 3.3 版本弃用:* 现在可以让 [`staticmethod`]({{< ref "/library/functions#staticmethod" >}}) 配合 [`abstractmethod()`]({{< ref "/library/python/abc#abc.abstractmethod" >}}) 使用，使得此装饰器变得冗余。

​	内置 [`staticmethod()`]({{< ref "/library/functions#staticmethod" >}}) 的子类，指明一个抽象静态方法。 在其他方面它都类似于 [`abstractmethod()`]({{< ref "/library/python/abc#abc.abstractmethod" >}})。

​	这个特例已被弃用，因为现在当 [`staticmethod()`]({{< ref "/library/functions#staticmethod" >}}) 装饰器应用于抽象方法时它会被正确地标识为抽象的:

```
class C(ABC):
    @staticmethod
    @abstractmethod
    def my_abstract_staticmethod(arg):
        ...
```

## @abc.**abstractproperty**

*自 3.3 版本弃用:* 现在可以让 [`property`]({{< ref "/library/functions#property" >}}), [`property.getter()`]({{< ref "/library/functions#property.getter" >}}), [`property.setter()`]({{< ref "/library/functions#property.setter" >}}) 和 [`property.deleter()`]({{< ref "/library/functions#property.deleter" >}}) 配合 [`abstractmethod()`]({{< ref "/library/python/abc#abc.abstractmethod" >}}) 使用，使得此装饰器变得冗余。

​	内置 [`property()`]({{< ref "/library/functions#property" >}}) 的子类，指明一个抽象特性属性。

​	这个特例已被弃用，因为现在当 [`property()`]({{< ref "/library/functions#property" >}}) 装饰器应用于抽象方法时它会被正确地标识为抽象的:

```
class C(ABC):
    @property
    @abstractmethod
    def my_abstract_property(self):
        ...
```

​	上面的例子定义了一个只读特征属性；你也可以通过适当地将一个或多个下层方法标记为抽象的来定义可读写的抽象特征属性:

```
class C(ABC):
    @property
    def x(self):
        ...

    @x.setter
    @abstractmethod
    def x(self, val):
        ...
```

​	如果只有某些组件是抽象的，则只需更新那些组件即可在子类中创建具体的特征属性:

```
class D(C):
    @C.x.setter
    def x(self, val):
        ...
```

`abc` 模块还提供了下列函数：

## abc.**get_cache_token**()

​	返回当前抽象基类的缓存令牌

​	此令牌是一个不透明对象（支持相等性测试），用于为虚子类标识抽象基类缓存的当前版本。 此令牌会在任何 ABC 上每次调用 [`ABCMeta.register()`]({{< ref "/library/python/abc#abc.ABCMeta.register" >}}) 时发生更改。

> Added in version 3.4.
>

## abc.**update_abstractmethods**(*cls*)

​	重新计算一个抽象类的抽象状态的函数。 如果一个类的抽象方法在类被创建后被实现或被修改则应当调用此函数。 通常，此函数应当在一个类装饰器内部被调用。

​	返回 *cls*，使其能够用作类装饰器。

​	如果 *cls* 不是 [`ABCMeta`]({{< ref "/library/python/abc#abc.ABCMeta" >}}) 的子类，则不做任何操作。

​	备注

 

​	此函数会假定 *cls* 的上级类已经被更新。 它不会更新任何子类。

> Added in version 3.10.
>

​	备注

[[1]({{< ref "/library/python/abc#id1" >}})]

​	C++ 程序员需要注意：Python 中虚基类的概念和 C++ 中的并不相同。
