+++
title = "enum --- 对枚举的支持"
date = 2024-11-15T11:18:41+08:00
weight = 130
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/enum.html](https://docs.python.org/zh-cn/3.13/library/enum.html)
>
> 收录该文档的时间：`2024-11-15T11:18:41+08:00`

# `enum` --- 对枚举的支持

> Added in version 3.4.
>

**源代码：** [Lib/enum.py](https://github.com/python/cpython/tree/3.13/Lib/enum.py)

​	Important

​	此页面仅包含 API 参考信息。教程信息和更多高级用法的讨论，请参阅

- [基础教程]({{< ref "/howto/enum#enum-basic-tutorial" >}})
- [进阶教程]({{< ref "/howto/enum#enum-advanced-tutorial" >}})
- [枚举指南]({{< ref "/howto/enum#enum-cookbook" >}})

------

​	一个枚举:

- 是绑定到唯一值的符号名称（成员）集合
- 可以被执行迭代以按定义顺序返回其规范的（即非别名的）成员
- 使用 *调用* 语法按值返回成员
- 使用 *索引* 语法按名称返回成员

​	枚举是通过使用 [`class`]({{< ref "/reference/compound_stmts#class" >}}) 语法或是通过使用函数调用语法来创建的:



``` python
>>> from enum import Enum

>>> # class syntax
>>> class Color(Enum):
...     RED = 1
...     GREEN = 2
...     BLUE = 3

>>> # functional syntax
>>> Color = Enum('Color', [('RED', 1), ('GREEN', 2), ('BLUE', 3)])
```

​	虽然我们可以使用 [`class`]({{< ref "/reference/compound_stmts#class" >}}) 语法来创建枚举，但枚举并不是常规的 Python 类。 请参阅 [枚举有什么不同？]({{< ref "/howto/enum#enum-class-differences" >}}) 了解更多细节。

​备注
 

​	命名法

- 类 `Color` 是一个 *枚举* （或 *enum* ）
- 属性 `Color.RED` 、 `Color.GREEN` 等是 *枚举成员* （或 *members* ）并且在功能上是常量。
- 枚举成员有 *names* 和 *values* (`Color.RED` 的名称是 `RED`，`Color.BLUE` 的值是 `3`，等等)

------

## 模块内容

> [`EnumType`]({{< ref "/library/datatypes/enum#enum.EnumType" >}})
>
> > ​	The `type` for Enum and its subclasses.
>
> [`Enum`]({{< ref "/library/datatypes/enum#enum.Enum" >}})
>
> > ​	用于创建枚举常量的基类。
>
> [`IntEnum`]({{< ref "/library/datatypes/enum#enum.IntEnum" >}})
>
> > ​	用于创建枚举常量的基类，这些常量也是 [`int`]({{< ref "/library/functions#int" >}}) 的子类。 ([Notes]({{< ref "/library/datatypes/enum#notes" >}}))
>
> [`StrEnum`]({{< ref "/library/datatypes/enum#enum.StrEnum" >}})
>
> > ​	用于创建枚举常量的基类，这些常量也是 [`str`]({{< ref "/library/stdtypes#str" >}}) 的子类。 ([Notes]({{< ref "/library/datatypes/enum#notes" >}}))
>
> [`Flag`]({{< ref "/library/datatypes/enum#enum.Flag" >}})
>
> > ​	创建可与位运算符搭配使用，又不会失去 [`Flag`]({{< ref "/library/datatypes/enum#enum.Flag" >}}) 成员资格的枚举常量的基类。
>
> [`IntFlag`]({{< ref "/library/datatypes/enum#enum.IntFlag" >}})
>
> > ​	创建可与位运算符搭配使用，又不失去 [`IntFlag`]({{< ref "/library/datatypes/enum#enum.IntFlag" >}}) 成员资格的枚举常量的基类。[`IntFlag`]({{< ref "/library/datatypes/enum#enum.IntFlag" >}}) 成员也是 [`int`]({{< ref "/library/functions#int" >}}) 的子类。 ([Notes]({{< ref "/library/datatypes/enum#notes" >}}))
>
> [`ReprEnum`]({{< ref "/library/datatypes/enum#enum.ReprEnum" >}})
>
> > ​	由 [`IntEnum`]({{< ref "/library/datatypes/enum#enum.IntEnum" >}}) 、[`StrEnum`]({{< ref "/library/datatypes/enum#enum.StrEnum" >}}) 和 [`IntFlag`]({{< ref "/library/datatypes/enum#enum.IntFlag" >}}) 用来保持混合类型的 [`str()`]({{< ref "/library/stdtypes#str" >}}) 。
>
> [`EnumCheck`]({{< ref "/library/datatypes/enum#enum.EnumCheck" >}})
>
> > ​	具有值 `CONTINUOUS`、`NAMED_FLAGS` 和 `UNIQUE` 的枚举，用于 [`verify()`]({{< ref "/library/datatypes/enum#enum.verify" >}}) 以确保给定枚举满足各种约束。
>
> [`FlagBoundary`]({{< ref "/library/datatypes/enum#enum.FlagBoundary" >}})
>
> > ​	具有值 `STRICT` 、 `CONFORM` 、 `EJECT` 和 `KEEP` 的枚举，允许对枚举中无效值的处理方式进行更细粒度的控制。
>
> [`auto`]({{< ref "/library/datatypes/enum#enum.auto" >}})
>
> > ​	实例被替换为枚举成员的适当值。 [`StrEnum`]({{< ref "/library/datatypes/enum#enum.StrEnum" >}}) 默认为成员名称的小写版本，而其他枚举默认为 1 并由此递增。
>
> [`property()`]({{< ref "/library/datatypes/enum#enum.property" >}})
>
> > ​	允许 [`Enum`]({{< ref "/library/datatypes/enum#enum.Enum" >}}) 成员拥有属性而不会与成员名称相冲突。 `value` 和 `name` 属性都是以这样的方式实现的。
>
> [`unique()`]({{< ref "/library/datatypes/enum#enum.unique" >}})
>
> > ​	确保一个名称只绑定一个值的 Enum 类装饰器。
>
> [`verify()`]({{< ref "/library/datatypes/enum#enum.verify" >}})
>
> > ​	检查枚举的用户可选择约束的枚举类装饰器。
>
> [`member()`]({{< ref "/library/datatypes/enum#enum.member" >}})
>
> > ​	使 `obj` 成为成员。可以用作装饰器。
>
> [`nonmember()`]({{< ref "/library/datatypes/enum#enum.nonmember" >}})
>
> > ​	使 `obj` 不为成员。可以用作装饰器。
>
> [`global_enum()`]({{< ref "/library/datatypes/enum#enum.global_enum" >}})
>
> > ​	修改枚举的 [`str()`]({{< ref "/library/stdtypes#str" >}}) 和 [`repr()`]({{< ref "/library/functions#repr" >}}) 以将其成员显示为属于模块而不是其类，并将枚举成员导出到全局命名空间。
>
> [`show_flag_values()`]({{< ref "/library/datatypes/enum#enum.show_flag_values" >}})
>
> > ​	返回标志中包含的所有二次幂整数的列表。

> Added in version 3.6:
> `Flag`, `IntFlag`, `auto`

> Added in version 3.11:
> `StrEnum`, `EnumCheck`, `ReprEnum`, `FlagBoundary`, `property`, `member`, `nonmember`, `global_enum`, `show_flag_values`

------

## 数据类型

## *class* enum.**EnumType**

*EnumType* 是 *enum* 枚举的 [metaclass]({{< ref "/glossary/idx#term-metaclass" >}}) 。可以对 *EnumType* 进行子类化——有关详细信息，请参阅 [Subclassing EnumType]({{< ref "/howto/enum#enumtype-examples" >}})。

`EnumType` 负责在最终的 *enum* 上设置正确的 `__repr__()`, `__str__()`, `__format__()`, and `__reduce__()` 方法，以及创建枚举成员，正确处理重复项，提供对枚举类的迭代等。

## `__call__`(*cls*, *value*, *names=None*, ***, *module=None*, *qualname=None*, *type=None*, *start=1*, *boundary=None*)

​	此方法以两种不同的方式调用：

- 查找现有成员：

  > ## cls:
  >
  > ​	被调用的枚举类。
  >
  > ## value:
  >
  > ​	要查找的值。

- 使用 `cls` 枚举创建新枚举（仅当现有枚举没有任何成员时）：

  > ## cls:
  >
  > ​	被调用的枚举类。
  >
  > ## value:
  >
  > ​	要创建的新枚举的名称。
  >
  > ## names:
  >
  > ​	新枚举成员的名称/值。
  >
  > ## module -- 模块:
  >
  > ​	在其中创建新枚举的模块的名称。
  >
  > ## qualname:
  >
  > ​	可以找到此枚举的模块中的实际位置。
  >
  > ## type -- 类型:
  >
  > ​	新枚举的混合类型。
  >
  > ## start:
  >
  > ​	枚举的第一个整数值（由 [`auto`]({{< ref "/library/datatypes/enum#enum.auto" >}}) 使用）。
  >
  > ## 边界:
  >
  > ​	如何处理来自位操作的超出范围的值（仅限 [`Flag`]({{< ref "/library/datatypes/enum#enum.Flag" >}}) ）。

## `__contains__`(*cls*, *member*)

​	如果成员属于``cls`` 则返回``True``



``` python
>>> some_var = Color.RED
>>> some_var in Color
True
>>> Color.RED.value in Color
True
```

> 在 3.12 版本发生变更: 在 Python 3.12 之前，如果在包含检测中使用了非枚举成员则会引发 `TypeError`。

## `__dir__`(*cls*)

​	返回 `['__class__', '__doc__', '__members__', '__module__']` 和 *cls* 中的成员名称



``` python
>>> dir(Color)
['BLUE', 'GREEN', 'RED', '__class__', '__contains__', '__doc__', '__getitem__', '__init_subclass__', '__iter__', '__len__', '__members__', '__module__', '__name__', '__qualname__']
```

## `__getitem__`(*cls*, *name*)

​	返回 *cls* 中匹配 *name* 的 Enum 成员，或者引发 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}}):



``` python
>>> Color['BLUE']
<Color.BLUE: 3>
```

## `__iter__`(*cls*)

​	按定义顺序返回 *cls* 中的每个成员:



``` python
>>> list(Color)
[<Color.RED: 1>, <Color.GREEN: 2>, <Color.BLUE: 3>]
```

## `__len__`(*cls*)

​	返回 *cls* 中成员的数量:



``` python
>>> len(Color)
3
```

## `__members__`

​	返回一个从每个枚举名称到其成员的映射，包括别名

## `__reversed__`(*cls*)

​	按定义的逆序返回 *cls* 中的每个成员:



``` python
>>> list(reversed(Color))
[<Color.BLUE: 3>, <Color.GREEN: 2>, <Color.RED: 1>]
```

## **_add_alias_**()

​	增加一个新名称作为现有成员的别名。 如果该名称已被分配给不同的成员则会引发 [`NameError`]({{< ref "/library/exceptions#NameError" >}})。

## **_add_value_alias_**()

​	增加一个新值作为现有成员的别名。 如果该值已经链接到不同的成员则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

> Added in version 3.11:
> 在 3.11 之前 `EnumType` 被称为 `EnumMeta`，该名称作为别名仍然可用。

## *class* enum.**Enum**

*Enum* 是所有 *enum* 枚举的基类。

## **name**

​	用于定义 `Enum` 成员的名称:



``` python
>>> Color.BLUE.name
'BLUE'
```

## **value**

​	赋给 `Enum` 成员的值:



``` python
>>> Color.RED.value
1
```

​	成员的值，可在 [`__new__()`]({{< ref "/library/datatypes/enum#enum.Enum.__new__" >}}) 中设置。

​备注
 

​	Enum 成员值

​	成员值可以为任意类型: [`int`]({{< ref "/library/functions#int" >}}), [`str`]({{< ref "/library/stdtypes#str" >}}) 等等。 如果具体的值不重要则你可以使用 [`auto`]({{< ref "/library/datatypes/enum#enum.auto" >}}) 实例这将为你选择一个适当的值。 详情参见 [`auto`]({{< ref "/library/datatypes/enum#enum.auto" >}})。

​	虽然可以使用可变/不可哈希的值，比如 [`dict`]({{< ref "/library/stdtypes#dict" >}}), [`list`]({{< ref "/library/stdtypes#list" >}}) 或是可变的 [`dataclass`]({{< ref "/library/python/dataclasses#dataclasses.dataclass" >}})，但它们在创建期间会产生基于枚举中可变/不可哈希的值数量的指数级性能影响。

## **_name_**

​	成员的名称。

## **_value_**

​	成员的值，可在 [`__new__()`]({{< ref "/library/datatypes/enum#enum.Enum.__new__" >}}) 中设置。

## **_order_**

​	已不再使用，保留以便向下兼容。 （类属性，在类创建期间移除）。

## **_ignore_**

`_ignore_` 仅在创建期间使用并会在创建完成后立即从枚举中移除。

`_ignore_` 是由不会被作为成员的名称组成的列包，并且这些名称还将从完成的枚举中移除。 请参阅 [TimePeriod]({{< ref "/howto/enum#enum-time-period" >}}) 获取示例。

## `__dir__`(*self*)

​	返回 `['__class__', '__doc__', '__module__', 'name', 'value']` 以及在 *self.__class__* 上定义的任何公有方法:



``` python
>>> from datetime import date
>>> class Weekday(Enum):
...     MONDAY = 1
...     TUESDAY = 2
...     WEDNESDAY = 3
...     THURSDAY = 4
...     FRIDAY = 5
...     SATURDAY = 6
...     SUNDAY = 7
...     @classmethod
...     def today(cls):
...         print('today is %s' % cls(date.today().isoweekday()).name)
...
>>> dir(Weekday.SATURDAY)
['__class__', '__doc__', '__eq__', '__hash__', '__module__', 'name', 'today', 'value']
```

## **_generate_next_value_**(*name*, *start*, *count*, *last_values*)

> ## name:
>
> ​	定义的成员名称（例如 'RED'）。
>
> ## start:
>
> ​	Enum 的起始值；默认为 1。
>
> ## count:
>
> ​	当前定义的成员数量，不包括这一个。
>
> ## last_values:
>
> ​	由前面的值组成的列表。

​	一个用来确定由 [`auto`]({{< ref "/library/datatypes/enum#enum.auto" >}}) 所返回的下一个值的 *静态方法*:



``` python
>>> from enum import auto
>>> class PowersOfThree(Enum):
...     @staticmethod
...     def _generate_next_value_(name, start, count, last_values):
...         return 3 ** (count + 1)
...     FIRST = auto()
...     SECOND = auto()
...
>>> PowersOfThree.SECOND.value
9
```

## `__init__`(*self*, **args*, ***kwds*)

​	在默认情况下，将不做任何事。 如果在成员赋值时给出了多个值，这些值将成为传给 `__init__` 的单独参数；例如



``` python
>>> from enum import Enum
>>> class Weekday(Enum):
...     MONDAY = 1, 'Mon'
```

`Weekday.__init__()` 将以 `Weekday.__init__(self, 1, 'Mon')` 的形式被调用

## **__init_subclass__**(*cls*, ***kwds*)

​	一个用来进一步配置后续子类的 *类方法*。 在默认情况下，将不做任何事。

## **_missing_**(*cls*, *value*)

​	一个用来查找不存在于 *cls* 中的值的 *类方法*。 在默认情况下它将不做任何事，但可以被重写以实现自定义的搜索行为:



``` python
>>> from enum import StrEnum
>>> class Build(StrEnum):
...     DEBUG = auto()
...     OPTIMIZED = auto()
...     @classmethod
...     def _missing_(cls, value):
...         value = value.lower()
...         for member in cls:
...             if member.value == value:
...                 return member
...         return None
...
>>> Build.DEBUG.value
'debug'
>>> Build('deBUG')
<Build.DEBUG: 'debug'>
```

## `__new__`(*cls*, **args*, ***kwds*)

​	在默认情况下，将不会存在。 如果指定，则或是在枚举类定义中或是在混入类定义中 (比如 `int`)，在成员赋值时给出的所有值都将被传递；例如



``` python
>>> from enum import Enum
>>> class MyIntEnum(int, Enum):
...     TWENTYSIX = '1a', 16
```

`int('1a', 16)` 调用的结果和成员的值 `26`。

​备注
 

​	当编写自定义的 `__new__` 时，不要使用 `super().__new__` -- 而要调用适当的 `__new__`。

## `__repr__`(*self*)

​	返回用于 *repr()* 调用的字符串。 在默认情况下，将返回 *Enum* 名称、成员名称和值，但也可以被重写:



``` python
>>> class OtherStyle(Enum):
...     ALTERNATE = auto()
...     OTHER = auto()
...     SOMETHING_ELSE = auto()
...     def __repr__(self):
...         cls_name = self.__class__.__name__
...         return f'{cls_name}.{self.name}'
...
>>> OtherStyle.ALTERNATE, str(OtherStyle.ALTERNATE), f"{OtherStyle.ALTERNATE}"
(OtherStyle.ALTERNATE, 'OtherStyle.ALTERNATE', 'OtherStyle.ALTERNATE')
```

## `__str__`(*self*)

​	返回用于 *str()* 调用的字符串。 在默认情况下，返回 *Enum* 名称和成员名称，但也可以被重写:



``` python
>>> class OtherStyle(Enum):
...     ALTERNATE = auto()
...     OTHER = auto()
...     SOMETHING_ELSE = auto()
...     def __str__(self):
...         return f'{self.name}'
...
>>> OtherStyle.ALTERNATE, str(OtherStyle.ALTERNATE), f"{OtherStyle.ALTERNATE}"
(<OtherStyle.ALTERNATE: 1>, 'ALTERNATE', 'ALTERNATE')
```

## `__format__`(*self*)

​	返回用于 *format()* 和 *f-string* 调用的字符串。 在默认情况下，将返回 [`__str__()`]({{< ref "/library/datatypes/enum#enum.Enum.__str__" >}}) 的返回值，但也可以被重写:



``` python
>>> class OtherStyle(Enum):
...     ALTERNATE = auto()
...     OTHER = auto()
...     SOMETHING_ELSE = auto()
...     def __format__(self, spec):
...         return f'{self.name}'
...
>>> OtherStyle.ALTERNATE, str(OtherStyle.ALTERNATE), f"{OtherStyle.ALTERNATE}"
(<OtherStyle.ALTERNATE: 1>, 'OtherStyle.ALTERNATE', 'ALTERNATE')
```

​备注
 

​	将 [`auto`]({{< ref "/library/datatypes/enum#enum.auto" >}}) 用于 [`Enum`]({{< ref "/library/datatypes/enum#enum.Enum" >}}) 将得到递增的整数值，从 `1` 开始。

> 在 3.12 版本发生变更: 增加了 [数据类支持]({{< ref "/howto/enum#enum-dataclass-support" >}})

## *class* enum.**IntEnum**

*IntEnum* 和 [`Enum`]({{< ref "/library/datatypes/enum#enum.Enum" >}}) 是一样的，但其成员还属于整数并可被用在任何可以使用整数的地方。 如果对一个 *IntEnum* 成员执行整数运算，结果值将失去其枚举状态。



``` python
>>> from enum import IntEnum
>>> class Number(IntEnum):
...     ONE = 1
...     TWO = 2
...     THREE = 3
...
>>> Number.THREE
<Number.THREE: 3>
>>> Number.ONE + Number.TWO
3
>>> Number.THREE + 5
8
>>> Number.THREE == 3
True
```

​备注
 

​	将 [`auto`]({{< ref "/library/datatypes/enum#enum.auto" >}}) 用于 [`IntEnum`]({{< ref "/library/datatypes/enum#enum.IntEnum" >}}) 将得到递增的整数值，从 `1` 开始。

> 在 3.11 版本发生变更: [`__str__()`]({{< ref "/reference/datamodel#object.__str__" >}}) 现在是 `int.__str__()` 以更好地支持 *现有常量的替换* 应用场景。 出于同样的原因 [`__format__()`]({{< ref "/reference/datamodel#object.__format__" >}}) 也已经是 `int.__format__()`。

## *class* enum.**StrEnum**

`StrEnum` 和 [`Enum`]({{< ref "/library/datatypes/enum#enum.Enum" >}}) 是一样的，但其成员还属于字符串并可被用在任何可以使用字符串的地方。 如果对一个 *StrEnum* 成员执行字符串操作其结果值将不再是该枚举的一部分。

​备注
 

​	在标准库中有些地方会检查是否是真正的 [`str`]({{< ref "/library/stdtypes#str" >}}) 而不是 [`str`]({{< ref "/library/stdtypes#str" >}}) 的子类 (例如使用 `type(unknown) == str` 而不是 `isinstance(unknown, str)`)，在这些地方你将需要使用 `str(StrEnum.member)`。

​备注
 

​	将 [`auto`]({{< ref "/library/datatypes/enum#enum.auto" >}}) 用于 [`StrEnum`]({{< ref "/library/datatypes/enum#enum.StrEnum" >}}) 将得到小写形式的成员名称字符串值。

​备注
 

[`__str__()`]({{< ref "/reference/datamodel#object.__str__" >}}) 是 `str.__str__()` 以更好地支持 *现有常量的替换* 应用场景。 出于同样的原因 [`__format__()`]({{< ref "/reference/datamodel#object.__format__" >}}) 也是 `str.__format__()`。

> Added in version 3.11.
>

## *class* enum.**Flag**

`Flag` 与 [`Enum`]({{< ref "/library/datatypes/enum#enum.Enum" >}}) 的相同，但其成员支持按位运算符 `&` (*AND*), `|` (*OR*), `^` (*XOR*) 和 `~` (*INVERT*)；这些运算的结果都是枚举成员（的别名）。

## `__contains__`(*self*, *value*)

​	如果 value 是 self 之中则返回 *True*:



``` python
>>> from enum import Flag, auto
>>> class Color(Flag):
...     RED = auto()
...     GREEN = auto()
...     BLUE = auto()
...
>>> purple = Color.RED | Color.BLUE
>>> white = Color.RED | Color.GREEN | Color.BLUE
>>> Color.GREEN in purple
False
>>> Color.GREEN in white
True
>>> purple in white
True
>>> white in purple
False
```

## **__iter__(self):**

​	返回所有包含的非别名成员:



``` python
>>> list(Color.RED)
[<Color.RED: 1>]
>>> list(purple)
[<Color.RED: 1>, <Color.BLUE: 4>]
```

> Added in version 3.11.
>

## **__len__(self):**

​	返回旗标中成员的数量:



``` python
>>> len(Color.GREEN)
1
>>> len(white)
3
```

> Added in version 3.11.
>

## **__bool__(self):**

​	如果旗标中有成员则返回 *True*，否则返回 *False*:



``` python
>>> bool(Color.GREEN)
True
>>> bool(white)
True
>>> black = Color(0)
>>> bool(black)
False
```

## `__or__`(*self*, *other*)

​	返回当前旗标与另一个旗标执行二进制或运算的结果:



``` python
>>> Color.RED | Color.GREEN
<Color.RED|GREEN: 3>
```

## `__and__`(*self*, *other*)

​	返回当前旗标与另一个旗标执行二进制与运算的结果:



``` python
>>> purple & white
<Color.RED|BLUE: 5>
>>> purple & Color.GREEN
<Color: 0>
```

## `__xor__`(*self*, *other*)

​	返回当前旗标与另一个旗标执行二进制异或运算的结果:



``` python
>>> purple ^ white
<Color.GREEN: 2>
>>> purple ^ Color.GREEN
<Color.RED|GREEN|BLUE: 7>
```

## **__invert__(self):**

​	返回 *type(self)* 中所有不在 *self* 中的旗标:



``` python
>>> ~white
<Color: 0>
>>> ~purple
<Color.GREEN: 2>
>>> ~Color.RED
<Color.GREEN|BLUE: 6>
```

## **_numeric_repr_**()

​	用于格式化任何其他未命名数字值的函数。 默认为数字值的 repr；常见的选择有 [`hex()`]({{< ref "/library/functions#hex" >}}) 和 [`oct()`]({{< ref "/library/functions#oct" >}})。

​备注
 

​	将 [`auto`]({{< ref "/library/datatypes/enum#enum.auto" >}}) 用于 [`Flag`]({{< ref "/library/datatypes/enum#enum.Flag" >}}) 将得到二的整数次方，从 `1` 开始。

> 在 3.11 版本发生变更: 零值旗标的 *repr()* 已被修改。 现在将是:



``` python
>>> Color(0) 
<Color: 0>
```

## *class* enum.**IntFlag**

`IntFlag` 与 [`Flag`]({{< ref "/library/datatypes/enum#enum.Flag" >}}) 相同，但其成员还属于整数类型并能被用于任何可以使用整数的地方。



``` python
>>> from enum import IntFlag, auto
>>> class Color(IntFlag):
...     RED = auto()
...     GREEN = auto()
...     BLUE = auto()
...
>>> Color.RED & 2
<Color: 0>
>>> Color.RED | 2
<Color.RED|GREEN: 3>
```

​	如果对一个 *IntFlag* 成员执行任何整数运算，结果将不再是一个 *IntFlag*:



``` python
>>> Color.RED + 2
3
```

​	如果对一个 *IntFlag* 成员执行 [`Flag`]({{< ref "/library/datatypes/enum#enum.Flag" >}}) 操作并且：

- 结果是一个合法的 *IntFlag*: 将返回一个 *IntFlag*
- 其结果将不是合法的 *IntFlag*: 具体结果将取决于 [`FlagBoundary`]({{< ref "/library/datatypes/enum#enum.FlagBoundary" >}}) 设置

​	未命名的零值旗标 [`repr()`]({{< ref "/library/functions#repr" >}}) 已被修改。 现在将是：



``` python
>>> Color(0)
<Color: 0>
```

​备注
 

​	将 [`auto`]({{< ref "/library/datatypes/enum#enum.auto" >}}) 用于 [`IntFlag`]({{< ref "/library/datatypes/enum#enum.IntFlag" >}}) 将得到二的整数次方，从 `1` 开始。

> 在 3.11 版本发生变更: [`__str__()`]({{< ref "/reference/datamodel#object.__str__" >}}) 现在是 `int.__str__()` 以更好地支持 *现有常量的替换* 应用场景。 出于同样的原因 [`__format__()`]({{< ref "/reference/datamodel#object.__format__" >}}) 也已经是 `int.__format__()`。

​	对一个 `IntFlag` 的反转现在将返回一个等于不在给定旗标中的所有旗标的并集的正值，而非一个负值。 这与现有 [`Flag`]({{< ref "/library/datatypes/enum#enum.Flag" >}}) 的行为相匹配。

## *class* enum.**ReprEnum**

`ReprEnum` 将使用 [`Enum`]({{< ref "/library/datatypes/enum#enum.Enum" >}}) 的 [`repr()`]({{< ref "/library/datatypes/enum#enum.Enum.__repr__" >}})，但使用混入数据类型的 [`str()`]({{< ref "/library/stdtypes#str" >}}):

- `int.__str__()` 用于 [`IntEnum`]({{< ref "/library/datatypes/enum#enum.IntEnum" >}}) 和 [`IntFlag`]({{< ref "/library/datatypes/enum#enum.IntFlag" >}})
- `str.__str__()` 用于 [`StrEnum`]({{< ref "/library/datatypes/enum#enum.StrEnum" >}})

​	从 `ReprEnum` 继承以存放混入数据类型的 [`str()`]({{< ref "/library/stdtypes#str" >}}) / [`format()`]({{< ref "/library/functions#format" >}}) 而不是使用 [`Enum`]({{< ref "/library/datatypes/enum#enum.Enum" >}}) 默认的 [`str()`]({{< ref "/library/datatypes/enum#enum.Enum.__str__" >}})。

> Added in version 3.11.
>

## *class* enum.**EnumCheck**

*EnumCheck* 包含由 [`verify()`]({{< ref "/library/datatypes/enum#enum.verify" >}}) 装饰器用来确保各种约束的选项；失败的约束将导致 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

## **UNIQUE**

​	确保每个值只有一个名称:



``` python
>>> from enum import Enum, verify, UNIQUE
>>> @verify(UNIQUE)
... class Color(Enum):
...     RED = 1
...     GREEN = 2
...     BLUE = 3
...     CRIMSON = 1
Traceback (most recent call last):
...
ValueError: aliases found in <enum 'Color'>: CRIMSON -> RED
```

## **CONTINUOUS**

​	确保在最低值成员和最高值成员之间没有缺失的值:



``` python
>>> from enum import Enum, verify, CONTINUOUS
>>> @verify(CONTINUOUS)
... class Color(Enum):
...     RED = 1
...     GREEN = 2
...     BLUE = 5
Traceback (most recent call last):
...
ValueError: invalid enum 'Color': missing values 3, 4
```

## **NAMED_FLAGS**

​	确保任何旗标分组/掩码只包含已命名的旗标 -- 在值是明确指定而不是由 [`auto()`]({{< ref "/library/datatypes/enum#enum.auto" >}}) 生成时将很有用处:



``` python
>>> from enum import Flag, verify, NAMED_FLAGS
>>> @verify(NAMED_FLAGS)
... class Color(Flag):
...     RED = 1
...     GREEN = 2
...     BLUE = 4
...     WHITE = 15
...     NEON = 31
Traceback (most recent call last):
...
ValueError: invalid Flag 'Color': aliases WHITE and NEON are missing combined values of 0x18 [use enum.show_flag_values(value) for details]
```

​备注
 

​	CONTINUOUS 和 NAMED_FLAGS 被设计用于配合整数值成员。

> Added in version 3.11.
>

## *class* enum.**FlagBoundary**

`FlagBoundary` 控制在 [`Flag`]({{< ref "/library/datatypes/enum#enum.Flag" >}}) 及其子类中如何处理超出范围的值。

## **STRICT**

​	超出范围的值将导致引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。 这是 [`Flag`]({{< ref "/library/datatypes/enum#enum.Flag" >}}) 的默认设置:



``` python
>>> from enum import Flag, STRICT, auto
>>> class StrictFlag(Flag, boundary=STRICT):
...     RED = auto()
...     GREEN = auto()
...     BLUE = auto()
...
>>> StrictFlag(2**2 + 2**4)
Traceback (most recent call last):
...
ValueError: <flag 'StrictFlag'> invalid value 20
    given 0b0 10100
  allowed 0b0 00111
```

## **CONFORM**

​	超出范围的值将导致无效的值被移除，保留有效的 [`Flag`]({{< ref "/library/datatypes/enum#enum.Flag" >}}) 值:



``` python
>>> from enum import Flag, CONFORM, auto
>>> class ConformFlag(Flag, boundary=CONFORM):
...     RED = auto()
...     GREEN = auto()
...     BLUE = auto()
...
>>> ConformFlag(2**2 + 2**4)
<ConformFlag.BLUE: 4>
```

## **EJECT**

​	超出范围的值将失去其 [`Flag`]({{< ref "/library/datatypes/enum#enum.Flag" >}}) 成员资格并转换为 [`int`]({{< ref "/library/functions#int" >}})。



``` python
>>> from enum import Flag, EJECT, auto
>>> class EjectFlag(Flag, boundary=EJECT):
...     RED = auto()
...     GREEN = auto()
...     BLUE = auto()
...
>>> EjectFlag(2**2 + 2**4)
20
```

## **KEEP**

​	超出范围的值将被保留，[`Flag`]({{< ref "/library/datatypes/enum#enum.Flag" >}}) 成员资格也将被保留。 这是 [`IntFlag`]({{< ref "/library/datatypes/enum#enum.IntFlag" >}}) 的默认设置:



``` python
>>> from enum import Flag, KEEP, auto
>>> class KeepFlag(Flag, boundary=KEEP):
...     RED = auto()
...     GREEN = auto()
...     BLUE = auto()
...
>>> KeepFlag(2**2 + 2**4)
<KeepFlag.BLUE|16: 20>
```

> Added in version 3.11.
>

------

### 支持的 `__dunder__` 名称

[`__members__`]({{< ref "/library/datatypes/enum#enum.EnumType.__members__" >}}) 是由 `member_name`:`member` 条目组成的只读有序映射。 它只在类上可用。

​	如果指定了 [`__new__()`]({{< ref "/library/datatypes/enum#enum.Enum.__new__" >}})，它必须创建并返回枚举成员；相应地设置成员的 `_value_` 也是一个很好的主意。 一旦所有成员都创建完成它将不再被使用。

### 支持的 `_sunder_` 名称

- [`_add_alias_()`]({{< ref "/library/datatypes/enum#enum.EnumType._add_alias_" >}}) -- 添加一个新名称作为现有成员的别名。

- [`_add_value_alias_()`]({{< ref "/library/datatypes/enum#enum.EnumType._add_value_alias_" >}}) -- 添加一个新值作为现有成员的别名。

- [`_name_`]({{< ref "/library/datatypes/enum#enum.Enum._name_" >}}) -- 成员的名称

- [`_value_`]({{< ref "/library/datatypes/enum#enum.Enum._value_" >}}) -- 成员的值；可在 `__new__` 中设置

- [`_missing_()`]({{< ref "/library/datatypes/enum#enum.Enum._missing_" >}}) -- 当未找到某个值时所使用的查找函数；可被重写

- [`_ignore_`]({{< ref "/library/datatypes/enum#enum.Enum._ignore_" >}}) -- 一个名称列表，可以为 [`list`]({{< ref "/library/stdtypes#list" >}}) 或 [`str`]({{< ref "/library/stdtypes#str" >}})，它不会被转化为成员，并将从最终类中移除

- [`_order_`]({{< ref "/library/datatypes/enum#enum.Enum._order_" >}}) -- 已不再使用，保留以便向下兼容（类属性，在类创建期间移除）

- [`_generate_next_value_()`]({{< ref "/library/datatypes/enum#enum.Enum._generate_next_value_" >}}) -- 用于为枚举成员获取适当的值；可被重写

  ​备注
   

  ​	对于标准的 [`Enum`]({{< ref "/library/datatypes/enum#enum.Enum" >}}) 类来说下一个被选择的值将是已有的最高值加一。

  ​	对于 [`Flag`]({{< ref "/library/datatypes/enum#enum.Flag" >}}) 类来说下一个选择的值将是下一个最高的二的幂数。

- 虽然 `_sunder_` 名称通常被保留用于 [`Enum`]({{< ref "/library/datatypes/enum#enum.Enum" >}}) 类的后续开发因而不可被使用，但有一些则被显式地允许：

  - `_repr_*` (例如 `_repr_html_`)，用于 [IPython 的丰富显示](https://ipython.readthedocs.io/en/stable/config/integrating.html#rich-display)

> Added in version 3.6:
> `_missing_`, `_order_`, `_generate_next_value_`

> Added in version 3.7:
> `_ignore_`

> Added in version 3.13:* `_add_alias_`, `_add_value_alias_`, `_repr_
>`

------

## 工具与装饰器

## *class* enum.**auto**

*auto* 可被用来替换某个值。 如果使用，*Enum* 机制将调用一个 [`Enum`]({{< ref "/library/datatypes/enum#enum.Enum" >}}) 的 [`_generate_next_value_()`]({{< ref "/library/datatypes/enum#enum.Enum._generate_next_value_" >}}) 来获取适当的值。 对于 [`Enum`]({{< ref "/library/datatypes/enum#enum.Enum" >}}) 和 [`IntEnum`]({{< ref "/library/datatypes/enum#enum.IntEnum" >}}) 这个适当的值将为最后的值加一；对于 [`Flag`]({{< ref "/library/datatypes/enum#enum.Flag" >}}) 和 [`IntFlag`]({{< ref "/library/datatypes/enum#enum.IntFlag" >}}) 它将为首个大于最高值的二的整数次方；对于 [`StrEnum`]({{< ref "/library/datatypes/enum#enum.StrEnum" >}}) 它将为成员名称的小写版本。 如果将 *auto()* 与手动指定的值混用则必须十分小心。

*auto* 实际仅会在赋值操作的最高层级上被解析:

- `FIRST = auto()` 将是可用的 (auto() 会被替换为 `1`);
- `SECOND = auto(), -2` 将是可用的 (auto 会被替换为 `2`，因此将使用 `2, -2` 来创建 `SECOND` 枚举成员；
- `THREE = [auto(), -3]` 将 *不可用* (`<auto instance>, -3` 将被用来创建 `THREE` 枚举成员)

*在 3.11.1 版本发生变更:* 在之前的版本中，`auto()` 必须为赋值行中唯一的内容才是可用的。

`_generate_next_value_` 可以被重写以便自定义 *auto* 所使用的值。

​备注
 

​	在 3.13 中默认的 `_generate_next_value_` 将总是返回最高成员值递增 1 的结果，并且如果有任何成员为不兼容的类型则将失败。, and will fail if any member is an incompatible type.

## @enum.**property**

​	一个类似于内置 *property* 的装饰器，但是专用于枚举。 它允许成员属性具有与成员自身相同的名称。

​备注
 

*property* 和成员必须在单独的类中定义；例如 *value* 和 *name* 属性是在 *Enum* 类中定义，而 *Enum* 的子类可以定义名称为 `value` 和 `name` 的成员。

> Added in version 3.11.
>

## @enum.**unique**

​	一个专用于枚举的 [`class`]({{< ref "/reference/compound_stmts#class" >}}) 装饰器。 它将搜索一个枚举的 [`__members__`]({{< ref "/library/datatypes/enum#enum.EnumType.__members__" >}})，收集它所找到的任何别名；如果找到了任何别名则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 并附带详情:



``` python
>>> from enum import Enum, unique
>>> @unique
... class Mistake(Enum):
...     ONE = 1
...     TWO = 2
...     THREE = 3
...     FOUR = 3
...
Traceback (most recent call last):
...
ValueError: duplicate values found in <enum 'Mistake'>: FOUR -> THREE
```

## @enum.**verify**

​	一个专用于枚举的 [`class`]({{< ref "/reference/compound_stmts#class" >}}) 装饰器。 将使用来自 [`EnumCheck`]({{< ref "/library/datatypes/enum#enum.EnumCheck" >}}) 的成员指明应当在被装饰的枚举上检查哪些约束。

> Added in version 3.11.
>

## @enum.**member**

​	一个在枚举中使用的装饰器：它的目标将成为一个成员。

> Added in version 3.11.
>

## @enum.**nonmember**

​	一个在枚举中使用的装饰器：它的目标将不会成员一个成员。

> Added in version 3.11.
>

## @enum.**global_enum**

​	一个修改枚举的 [`str()`]({{< ref "/library/stdtypes#str" >}}) 和 [`repr()`]({{< ref "/library/functions#repr" >}}) 来将其成员显示为属于模块而不是类的装饰器。 应当仅在枚举成员被导出到模块全局命名空间时（请参看 [`re.RegexFlag`]({{< ref "/library/text/re#re.RegexFlag" >}}) 获取示例）使用。

> Added in version 3.11.
>

## enum.**show_flag_values**(*value*)

​	返回旗标 *value* 中包含的所有二的整数次幂的列表。

> Added in version 3.11.
>

------

## 备注

[`IntEnum`]({{< ref "/library/datatypes/enum#enum.IntEnum" >}}), [`StrEnum`]({{< ref "/library/datatypes/enum#enum.StrEnum" >}}) 和 [`IntFlag`]({{< ref "/library/datatypes/enum#enum.IntFlag" >}})

> ​	这三个枚举类型被设计用来快速替代现有的基于整数和字符串的值；为此，它们都有额外的限制:
>
> - `__str__` 使用枚举成员的值而不是名称
> - `__format__`，因为它使用了 `__str__`，也将使用枚举成员的值而不是其名称
>
> ​	如果你不需要/希望有这些限制，你可以通过自行混入 `int` 或 `str` 类型来创建你自己的基类:
>
> 
>
> ```
> >>> from enum import Enum
> >>> class MyIntEnum(int, Enum):
> ...     pass
> ```
>
> ​	或者你也可以在你的枚举中重新赋值适当的 [`str()`]({{< ref "/library/stdtypes#str" >}}) 等:
>
> 
>
> ```
> >>> from enum import Enum, IntEnum
> >>> class MyIntEnum(IntEnum):
> ...     __str__ = Enum.__str__
> ```
