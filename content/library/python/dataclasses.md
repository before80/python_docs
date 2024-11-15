+++
title = "dataclasses --- 数据类"
date = 2024-11-15T21:12:39+08:00
weight = 60
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/dataclasses.html](https://docs.python.org/zh-cn/3.13/library/dataclasses.html)
>
> 收录该文档的时间：`2024-11-15T21:12:39+08:00`

# `dataclasses` --- 数据类

**源码：** [Lib/dataclasses.py](https://github.com/python/cpython/tree/3.13/Lib/dataclasses.py)

------

​	这个模块提供了一个装饰器和一些函数，用于自动为用户自定义的类添加生成的 [特殊方法](https://docs.python.org/zh-cn/3.13/glossary.html#term-special-method) 例如 [`__init__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__init__) 和 [`__repr__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__repr__)。 它的初始描述见 [**PEP 557**](https://peps.python.org/pep-0557/)。

​	在这些生成的方法中使用的成员变量是使用 [**PEP 526**](https://peps.python.org/pep-0526/) 类型标注来定义的。例如以下代码：

```
from dataclasses import dataclass

@dataclass
class InventoryItem:
    """Class for keeping track of an item in inventory."""
    name: str
    unit_price: float
    quantity_on_hand: int = 0

    def total_cost(self) -> float:
        return self.unit_price * self.quantity_on_hand
```

​	将添加多项内容，包括如下所示的 `__init__()`:

```
def __init__(self, name: str, unit_price: float, quantity_on_hand: int = 0):
    self.name = name
    self.unit_price = unit_price
    self.quantity_on_hand = quantity_on_hand
```

​	请注意此方法会自动添加到类中：它不是在如上所示的 `InventoryItem` 定义中直接指定的。

*Added in version 3.7.*

## 模块内容

## @dataclasses.**dataclass**(***, *init=True*, *repr=True*, *eq=True*, *order=False*, *unsafe_hash=False*, *frozen=False*, *match_args=True*, *kw_only=False*, *slots=False*, *weakref_slot=False*)

​	此函数是一个 [decorator](https://docs.python.org/zh-cn/3.13/glossary.html#term-decorator)，它被用于将生成的 [特殊方法](https://docs.python.org/zh-cn/3.13/glossary.html#term-special-method) 添加到类中，如下所述。

`@dataclass` 装饰器会检查类以找到其中的 `field`。 `field` 被定义为具有 [类型标注](https://docs.python.org/zh-cn/3.13/glossary.html#term-variable-annotation) 的类变量。 除了下面所述的两个例外，在 `@dataclass` 中没有任何东西会去检查变量标注中指定的类型。

​	这些字段在所有生成的方法中的顺序，都是它们在类定义中出现的顺序。

`@dataclass` 装饰器将把各种“双下线”方法添加到类，具体如下所述。 如果所添加的任何方法在类中已存在，其行为将取决于形参的值，具体如下所述。 该装饰器将返回执行其调用的类而不会创建新类。

​	如果 `@dataclass` 仅被用作不带形参的简单装饰器，其行为相当于使用在此签名中记录的默认值。 也就是说，这三种 `@dataclass` 的用法是等价的:

```
@dataclass
class C:
    ...

@dataclass()
class C:
    ...

@dataclass(init=True, repr=True, eq=True, order=False, unsafe_hash=False, frozen=False,
           match_args=True, kw_only=False, slots=False, weakref_slot=False)
class C:
    ...
```

`@dataclass` 的形参有：

- *init*: 如为真值（默认），将生成 [`__init__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__init__) 方法。

  如果类已经定义了 `__init__()`，此形参将被忽略。

- *repr*: 如为真值（默认），将生成 [`__repr__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__repr__) 方法。 生成的 repr 字符串将带有类名及每个字符的名称和 repr，并按它们在类中定义的顺序排列。 不包括被标记为从 repr 排除的字段。 例如: `InventoryItem(name='widget', unit_price=3.0, quantity_on_hand=10)`。

  如果类已经定义了 `__repr__()`，此形参将被忽略。

- *eq*: 如为真值（默认），将生成 [`__eq__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__eq__) 方法。 此方法将把类当作由其字段组成的元组那样按顺序进行比较。 要比较的两个实例必须是相同的类型。

  如果类已经定义了 `__eq__()`，此形参将被忽略。

- *order*: 如为真值 (默认为 `False`)，将生成 [`__lt__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__lt__), [`__le__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__le__), [`__gt__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__gt__) 和 [`__ge__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__ge__) 方法。 这些方法将把类当作由其字段组成的元组那样按顺序进行比较。 要比较的两个实例必须是相同的类型。 如果 *order* 为真值且 *eq* 为假值，则会引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

  如果类已经定义了 `__lt__()`, `__le__()`, `__gt__()` 或者 `__ge__()` 中的任意一个，将引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。

- *unsafe_hash*: 如为 `False` (默认值)，则会根据 *eq* 和 *frozen* 的设置情况生成 [`__hash__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__hash__) 方法。

  `__hash__()` is used by built-in [`hash()`](https://docs.python.org/zh-cn/3.13/library/functions.html#hash), and when objects are added to hashed collections such as dictionaries and sets. Having a `__hash__()` implies that instances of the class are immutable. Mutability is a complicated property that depends on the programmer's intent, the existence and behavior of `__eq__()`, and the values of the *eq* and *frozen* flags in the `@dataclass` decorator.

  在默认情况下，`@dataclass` 不会隐式地添加 [`__hash__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__hash__) 方法，除非这样做是安全的。 它也没会添加或更改现有的显式定义的 `__hash__()` 方法。 设置类属性 `__hash__ = None` 对 Python 具有特定含义，如 `__hash__()` 文档中所述。

  如果 `__hash__()` 没有被显式定义，或者它被设为 `None`，则 `@dataclass` *可能* 会添加一个隐式 `__hash__()` 方法。 虽然并不推荐，但你可以用 `unsafe_hash=True` 来强制让 `@dataclass` 创建一个 `__hash__()` 方法。 如果你的类在逻辑上不可变但却仍然可被修改那么可能就是这种情况一。 这是一个特殊用例并且应当被小心地处理。

  以下是针对隐式创建 `__hash__()` 方法的规则。 请注意你的数据类中不能既有显式的 `__hash__()` 方法又设置 `unsafe_hash=True`；这将导致 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。

  如果 *eq* 和 *frozen* 均为真值，则默认 `@dataclass` 将为你生成 `__hash__()` 方法。 如果 *eq* 为真值而 *frozen* 为假值，则:meth:!__hash__ 将被设为 `None`，即将其标记为不可哈希（因为它属于可变对象）。 如果 *eq* 为假值，则 `__hash__()` 将保持不变，这意味着将使用超类的 `__hash__()` 方法（如果超类是 [`object`](https://docs.python.org/zh-cn/3.13/library/functions.html#object)，这意味着它将回退为基于 id 的哈希）。

- *frozen*: 如为真值 (默认为 `False`)，则对字段赋值将引发异常。 这模拟了只读的冻结实例。 如果在类中定义了 [`__setattr__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__setattr__) 或 [`__delattr__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__delattr__)，则将引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。 参见下文的讨论。

- *match_args*: 如为真值 (默认为 `True`)，则将根据传给生成的 [`__init__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__init__) 方法的形参列表来创建 [`__match_args__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__match_args__) 元组 (即使没有生成 `__init__()`，见上文)。 如为假值，或者如果 `__match_args__` 已在类中定义，则不会生成 `__match_args__`。

> *Added in version 3.10.*

- *kw_only*: 如为真值 (默认值为 `False`)，则所有字段都将被标记为仅限关键字的。 如果一个字段被标记为仅限关键字的，则唯一的影响是由仅限关键字的字段生成的 [`__init__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__init__) 的对应形参在 `__init__()` 被调用时必须以关键字形式指定。 而数据类的任何其他行为都不会受影响。 详情参见 [parameter](https://docs.python.org/zh-cn/3.13/glossary.html#term-parameter) 术语表条目。 另请参阅 [`KW_ONLY`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.KW_ONLY) 一节。

> *Added in version 3.10.*

- *slots*: 如为真值 (默认为 `False`)，则将生成 [`__slots__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__slots__) 属性并返回一个新类而非原本的类。 如果 `__slots__` 已在类中定义，则会引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。

> ​	警告
>
>  
>
> ​	Calling no-arg [`super()`](https://docs.python.org/zh-cn/3.13/library/functions.html#super) in dataclasses using `slots=True` will result in the following exception being raised: `TypeError: super(type, obj): obj must be an instance or subtype of type`. The two-arg [`super()`](https://docs.python.org/zh-cn/3.13/library/functions.html#super) is a valid workaround. See [gh-90562](https://github.com/python/cpython/issues/90562) for full details.
>
> ​	警告
>
>  
>
> ​	Passing parameters to a base class [`__init_subclass__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__init_subclass__) when using `slots=True` will result in a [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError). Either use `__init_subclass__` with no parameters or use default values as a workaround. See [gh-91126](https://github.com/python/cpython/issues/91126) for full details.
>
> *Added in version 3.10.*
>
> *在 3.11 版本发生变更:* 如果某个字段名称已经包括在基类的 `__slots__` 中，它将不会被包括在生成的 `__slots__` 中以防止 [重写它们](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#datamodel-note-slots)。 因此，请不要使用 `__slots__` 来获取数据类的字段名称。 而应改用 [`fields()`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.fields)。 为了能够确定所继承的槽位，基类 `__slots__` 可以是任意可迭代对象，但是 *不可以* 是迭代器。an iterator.

- *weakref_slot*: If true (the default is `False`), add a slot named "__weakref__", which is required to make an instance [`weakref-able`](https://docs.python.org/zh-cn/3.13/library/weakref.html#weakref.ref). It is an error to specify `weakref_slot=True` without also specifying `slots=True`.

> *Added in version 3.11.*

​	可以用普通的 Python 语法为各个 `field` 指定默认值：

```
@dataclass
class C:
    a: int       # 'a' 没有默认值
    b: int = 0   # 为 'b' 赋默认值
```

​	在这个例子中，`a` 和 `b` 都将被包括在所添加的 [`__init__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__init__) 方法中，该方法将被定义为:

```
def __init__(self, a: int, b: int = 0):
```

​	如果在具有默认值的字段之后存在没有默认值的字段，将会引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。无论此情况是发生在单个类中还是作为类继承的结果，都是如此。

## dataclasses.**field**(***, *default=MISSING*, *default_factory=MISSING*, *init=True*, *repr=True*, *hash=None*, *compare=True*, *metadata=None*, *kw_only=MISSING*)

​	对于常见和简单的用例，不需要其他的功能。 但是，有些数据类的特性需要额外的每字段信息。 为了满足这种对额外信息的需求，你可以通过调用所提供的 `field()` 函数来替换默认的字段值。 例如:

```
@dataclass
class C:
    mylist: list[int] = field(default_factory=list)

c = C()
c.mylist += [1, 2, 3]
```

​	如上所示，[`MISSING`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.MISSING) 值是一个哨兵对象，用于检测一些形参是否由用户提供。使用它是因为 `None` 对于一些形参来说是有效的用户值。任何代码都不应该直接使用 [`MISSING`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.MISSING) 值。

​	传给 `field()` 的形参有：

- *default*: 如果提供，这将为该字段的默认值。 设置此形参是因为 `field()` 调用本身会替换通常的默认值所在位置。

- *default_factory*: 如果提供，它必须是一个零参数的可调用对象，它将在该字段需要一个默认值时被调用。 在其他目的以外，它还可被用于指定具有可变默认值的字段，如下所述。 同时指定 *default* 和 *default_factory* 将会导致错误。

- *init*: 如为真值（默认），则该字段将作为一个形参被包括在生成的 [`__init__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__init__) 方法中。

- *repr*: 如为真值（默认值），则该字段将被包括在生成的 [`__repr__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__repr__) 方法所返回的字符串中。

- *hash*: 这可以是一个布尔值或 `None`。 如为真值，则此字段将被包括在生成的 [`__hash__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__hash__) 方法中。 如果为 `None` (默认)，则将使用 *compare* 的值：这通常是预期的行为。 一个字段如果被用于比较那么就应当在哈希时考虑到它。 不建议将该值设为 `None` 以外的任何其他值。

  设置 `hash=False` 但 `compare=True` 的一个合理情况是，一个计算哈希值的代价很高的字段是检验等价性需要的，且还有其他字段可以用于计算类型的哈希值。可以从哈希值中排除该字段，但仍令它用于比较。

- *compare*: 如为真值（默认），则该字段将被包括在生成的相等和比较方法中 ([`__eq__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__eq__), [`__gt__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__gt__) 等等)。

- *metadata*: 这可以是一个映射或为 `None`。 `None` 将被当作空字典来处理。 这个值将被包装在 [`MappingProxyType()`](https://docs.python.org/zh-cn/3.13/library/types.html#types.MappingProxyType) 以便其为只读，并暴露在 [`Field`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.Field) 对象上。 它完全不被数据类所使用，并且是作为第三方扩展机制提供的。 多个第三方可以各自拥有其本身的键，以用作元数据的命名空间。

- *kw_only*: 如为真值，则该字段将被标记为仅限关键字的。 这将在计算所生成的 [`__init__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__init__) 方法的形参时被使用。

> *Added in version 3.10.*

​	如果通过对 `field()` 的调用来指定字段的默认值，那么该字段对应的类属性将被替换为指定的 *default* 值。 如果没有提供 *default*，那么该类属性将被删除。 其意图是在 [`@dataclass`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.dataclass) 装饰器运行之后，该类属性将包含所有字段的默认值，就像直接指定了默认值本身一样。 例如，在执行以下代码之后:

```
@dataclass
class C:
    x: int
    y: int = field(repr=False)
    z: int = field(repr=False, default=10)
    t: int = 20
```

​	类属性 `C.z` 将为 `10`，类属性 `C.t` 将为 `20`，类属性 `C.x` 和 `C.y` 将不被设置。

## *class* dataclasses.**Field**

`Field` 对象描述每个已定义的字段。 这些对象是在内部创建的，并会由 [`fields()`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.fields) 模块块方法返回（见下文）。 用户绝不应直接实例化 `Field` 对象。 已写入文档的属性如下：

- `name`: 字段的名称。
- `type`: 字段的类型。
- `default`, `default_factory`, `init`, `repr`, `hash`, `compare`, `metadata` 和 `kw_only` 具有与 [`field()`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.field) 函数中对应参数相同的含义和值。

​	可能存在其他属性，但它们是私有的。用户不应检查或依赖于这些属性。

## dataclasses.**fields**(*class_or_instance*)

​	返回一个能描述此数据类所包含的字段的元组，元组的每一项都是 [`Field`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.Field) 对象。接受数据类或数据类的实例。如果没有传递一个数据类或实例将引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。不返回 `ClassVar` 或 `InitVar` 等伪字段。

## dataclasses.**asdict**(*obj*, ***, *dict_factory=dict*)

​	将数据类 *obj* 转换为一个字典 (使用工厂函数 *dict_factory*)。 每个数据类会被转换为以 `name: value` 键值对来存储其字段的字典。 数据类、字典、列表和元组会被递归地处理。 其他对象会通过 [`copy.deepcopy()`](https://docs.python.org/zh-cn/3.13/library/copy.html#copy.deepcopy) 来拷贝。

​	在嵌套的数据类上使用 `asdict()` 的例子:

```
@dataclass
class Point:
     x: int
     y: int

@dataclass
class C:
     mylist: list[Point]

p = Point(10, 20)
assert asdict(p) == {'x': 10, 'y': 20}

c = C([Point(0, 0), Point(10, 4)])
assert asdict(c) == {'mylist': [{'x': 0, 'y': 0}, {'x': 10, 'y': 4}]}
```

​	要创建一个浅拷贝，可以使用以下的变通方法：

```
{field.name: getattr(obj, field.name) for field in fields(obj)}
```

​	如果 *obj* 不是一个数据类实例则 `asdict()` 将引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError) 。

## dataclasses.**astuple**(*obj*, ***, *tuple_factory=tuple*)

​	将数据类 *obj* 转换为元组 (使用工厂函数 *tuple_factory*)。 每个数据类将被转换为由其字段值组成的元组。 数据类、字典、列表和元组会被递归地处理。 其他对象会通过 [`copy.deepcopy()`](https://docs.python.org/zh-cn/3.13/library/copy.html#copy.deepcopy) 来拷贝。

​	继续前一个例子：

```
assert astuple(p) == (10, 20)
assert astuple(c) == ([(0, 0), (10, 4)],)
```

​	要创建一个浅拷贝，可以使用以下的变通方法：

```
tuple(getattr(obj, field.name) for field in dataclasses.fields(obj))
```

​	如果 *obj* 不是一个数据类实例则 `astuple()` 将引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。

## dataclasses.**make_dataclass**(*cls_name*, *fields*, ***, *bases=()*, *namespace=None*, *init=True*, *repr=True*, *eq=True*, *order=False*, *unsafe_hash=False*, *frozen=False*, *match_args=True*, *kw_only=False*, *slots=False*, *weakref_slot=False*, *module=None*)

​	新建一个名为 *cls_name* 的数据类，其字段在 *fields* 中定义，其基类在 *bases* 中给出，并使用在 *namespace* 中给定的命名空间来初始化。 *fields* 是一个可迭代对象，其中每个元素均为 `name`, `(name, type)` 或 `(name, type, Field)` 的形式。 如果只提供了 `name`，则使用 [`typing.Any`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.Any) 作为 `type`。 *init*, *repr*, *eq*, *order*, *unsafe_hash*, *frozen*, *match_args*, *kw_only*, *slots* 和 *weakref_slot* 值的含义与 [`@dataclass`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.dataclass) 中的同名参数一致。

​	如果定义了 *module*，则该数据类的 `__module__` 属性将被设为该值。 在默认情况下，它将被设为调用方的模块名。

​	此函数不是必需的，因为任何用于创建带有 `__annotations__` 的新类的 Python 机制都可以进一步用 [`@dataclass`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.dataclass) 函数将创建的类转换为数据类。 提供此函数是为了方便。 例如:

```
C = make_dataclass('C',
                   [('x', int),
                     'y',
                    ('z', int, field(default=5))],
                   namespace={'add_one': lambda self: self.x + 1})
```

​	等价于：

```
@dataclass
class C:
    x: int
    y: 'typing.Any'
    z: int = 5

    def add_one(self):
        return self.x + 1
```

## dataclasses.**replace**(*obj*, */*, ***changes*)

​	创建一个与 *obj* 类型相同的新对象，将字段替换为 *changes* 的值。 如果 *obj* 不是数据类，则会引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。 如果 *changes* 中的键不是给定数据类的字段名，则会引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。

​	新返回的对象是通过调用数据类的 [`__init__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__init__) 方法来创建的。 这确保了如果存在 [`__post_init__()`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.__post_init__)，则它也会被调用。

​	如果存在任何没有默认值的仅初始化变量，那么必须在调用 `replace()` 时指定它们的值，以便它们可以被传递给 `__init__()` 和 [`__post_init__()`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.__post_init__)。

​	如果 *changes* 包含被任何定义为 defined as having `init=False` 的字段都会导致错误。 在此情况下将引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

​	需要预先注意 `init=False` 字段在对 `replace()` 的调用期间的行为。 如果它们会被初始化，它们就不会从源对象拷贝，而是在 [`__post_init__()`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.__post_init__) 中初始化。 通常预期 `init=False` 字段将很少能被正确地使用。 如果要使用它们，那么更明智的做法是使用另外的类构造器，或者自定义的 `replace()` (或类似名称) 方法来处理实例的拷贝。

​	数据类支持也被泛型函数 [`copy.replace()`](https://docs.python.org/zh-cn/3.13/library/copy.html#copy.replace) 所支持。

## dataclasses.**is_dataclass**(*obj*)

​	如果其形参是一个 dataclass（包括 dataclass 的子类）或其实例则返回 `True`，否则返回 `False`。

​	如果你需要知道一个类是否是一个数据类的实例（而不是一个数据类本身），那么再添加一个 `not isinstance(obj, type)` 检查：

```
def is_dataclass_instance(obj):
    return is_dataclass(obj) and not isinstance(obj, type)
```

## dataclasses.**MISSING**

​	一个指明“没有提供 default 或 default_factory”的监视值。

## dataclasses.**KW_ONLY**

​	一个用途类型标的监视值。 任何在伪字段之后的类型为 `KW_ONLY` 的字段会被标记为仅限关键字的字段。 请注意在其他情况下 `KW_ONLY` 类型的伪字段会被完全忽略。 这包括此类字段的名称。 根据惯例，名称 `_` 会被用作 `KW_ONLY` 字段。 仅限关键字字段指明当类被实例化时 [`__init__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__init__) 形参必须以关键字形式来指定。

​	在这个例子中，字段 `y` 和 `z` 将被标记为仅限关键字字段:

```
@dataclass
class Point:
    x: float
    _: KW_ONLY
    y: float
    z: float

p = Point(0, y=1.5, z=2.0)
```

​	在单个数据类中，指定一个以上 `KW_ONLY` 类型的字段将导致错误。

*Added in version 3.10.*

## *exception* dataclasses.**FrozenInstanceError**

​	在定义时设置了 `frozen=True` 的类上调用隐式定义的 [`__setattr__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__setattr__) 或 [`__delattr__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__delattr__) 时引发。 这是 [`AttributeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#AttributeError) 的一个子类。



## 初始化后处理

## dataclasses.**__post_init__**()

​	当在类上定义时，它将被所生成的 [`__init__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__init__) 调用，通常是以 `self.__post_init__()` 的形式。 但是，如果定义了任何 `InitVar` 字段，它们也将按照它们在类中定义的顺序被传递给 `__post_init__()`。 如果没有生成 `__init__()` 方法，那么 `__post_init__()` 将不会被自动调用。

​	在其他用途中，这允许初始化依赖于一个或多个其他字段的字段值。例如:

```
@dataclass
class C:
    a: float
    b: float
    c: float = field(init=False)

    def __post_init__(self):
        self.c = self.a + self.b
```

​	由 [`@dataclass`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.dataclass) 生成的 [`__init__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__init__) 方法不会调用基类的 `__init__()` 方法。 如果基类有必须被调用的 `__init__()` 方法，通常是在 [`__post_init__()`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.__post_init__) 方法中调用此方法:

```
class Rectangle:
    def __init__(self, height, width):
        self.height = height
        self.width = width

@dataclass
class Square(Rectangle):
    side: float

    def __post_init__(self):
        super().__init__(self.side, self.side)
```

​	但是，请注意一般来说数据类生成的 `__init__()` 方法不需要被调用，因为派生的数据类将负责初始化任何本身为数据类的基类的所有字段。

​	请参阅下面有关仅初始化变量的小节来了解如何将形参传递给 `__post_init__()`。 另请参阅关于 [`replace()`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.replace) 如何处理 `init=False` 字段的警告。



## 类变量

​	少数几个 [`@dataclass`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.dataclass) 会实际检查字段类型的地方之一是确定字段是否为如 [**PEP 526**](https://peps.python.org/pep-0526/) 所定义的类变量。 它通过检查字段的类型是否为 [`typing.ClassVar`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.ClassVar) 来实现这一点。 如果一个字段是 `ClassVar`，它将被排除在考虑范围之外并被数据类机制所忽略。 这样的 `ClassVar` 伪字段将不会被模块层级的 [`fields()`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.fields) 函数返回。



## 仅初始化变量

​	另一个 [`@dataclass`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.dataclass) 会检查类型标注的地方是为了确定一个字段是否为仅限初始化的变量。 这通过检查字段的类型是否为 `dataclasses.InitVar` 来实现这一点。 如果一个字段是 `InitVar`，它会被当作是被称为仅限初始化字段的伪字段。 因为它不是一个真正的字段，所以它不会被模块层级的 [`fields()`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.fields) 函数返回。 仅限初始化字段会作为形参被添加到所生成的 [`__init__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__init__) 方法中，并被传递给可选的 [`__post_init__()`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.__post_init__) 方法。 在其他情况下它们将不会被数据类所使用。

​	例如，假设在创建类时没有为某个字段提供值，初始化时将从数据库中取值:

```
@dataclass
class C:
    i: int
    j: int | None = None
    database: InitVar[DatabaseType | None] = None

    def __post_init__(self, database):
        if self.j is None and database is not None:
            self.j = database.lookup('j')

c = C(10, database=my_database)
```

​	在这种情况下，[`fields()`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.fields) 将返回 [`Field`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.Field) 作为 `i` 和 `j`，但不包括 `database`。



## 冻结的实例

​	创建真正不可变的 Python 对象是不可能的。 但是，你可以通过将 `frozen=True` 传递给 [`@dataclass`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.dataclass) 装饰器来模拟出不可变性。 在这种情况下，数据类将向类添加 [`__setattr__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__setattr__) 和 [`__delattr__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__delattr__) 方法。 当被发起调用时这些方法将会引发 [`FrozenInstanceError`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.FrozenInstanceError)。

​	在使用 `frozen=True` 时会有微小的性能损失: [`__init__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__init__) 不能使用简单赋值来初始化字段，而必须使用 `object.__setattr__()`。



## 继承

​	当数据类由 [`@dataclass`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.dataclass) 装饰器创建时，它会按反向 MRO 顺序（也就是说，从 [`object`](https://docs.python.org/zh-cn/3.13/library/functions.html#object) 开始）查看它的所有基类，并将找到的每个数据类的字段添加到一个有序映射中。 所有生成的方法都将使用这个有序映射。 字段会遵守它们被插入的顺序，因此派生类会重写基类。 一个例子:

```
@dataclass
class Base:
    x: Any = 15.0
    y: int = 0

@dataclass
class C(Base):
    z: int = 10
    x: int = 15
```

​	最终的字段列表依次是 `x`, `y`, `z`。 最终的 `x` 类型是 [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int)，正如类 `C` 中所指定的。

​	为 `C` 生成的 [`__init__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__init__) 方法看起来像是这样:

```
def __init__(self, x: int = 15, y: int = 0, z: int = 10):
```

## `__init__()` 中仅限关键字形参的重新排序

​	在计算出 [`__init__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__init__) 所需要的形参之后，任何仅限关键字形参会被移至所有常规（非仅限关键字）形参的后面。 这是 Python 中实现仅限关键字形参所要求的：它们必须位于非仅限关键字形参之后。

​	在这个例子中，`Base.y`, `Base.w` 和 `D.t` 是仅限关键字字段，而 `Base.x` 和 `D.z` 是常规字段:

```
@dataclass
class Base:
    x: Any = 15.0
    _: KW_ONLY
    y: int = 0
    w: int = 1

@dataclass
class D(Base):
    z: int = 10
    t: int = field(kw_only=True, default=0)
```

​	为 `D` 生成的 `__init__()` 方法看起来像是这样:

```
def __init__(self, x: Any = 15.0, z: int = 10, *, y: int = 0, w: int = 1, t: int = 0):
```

​	请注意形参原来在字段列表中出现的位置已被重新排序：前面是来自常规字段的形参而后面是来自仅限关键字字段的形参。

​	仅限关键字形参的相对顺序会在重新排序的 `__init__()` 列表中保持不变。

## 默认工厂函数

​	如果一个 [`field()`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.field) 指定了 *default_factory*，它将在该字段需要默认值时不带参数地被调用。 例如，要创建一个列表的新实例，则使用:

```
mylist: list = field(default_factory=list)
```

​	如果一个字段被排除在 [`__init__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__init__) 之外 (使用 `init=False`) 并且该字段还指定了 *default_factory*，则默认的工厂函数将总是会从生成的 `__init__()` 函数中被调用。 发生这种情况是因为没有其他方式能为字段提供初始值。

## 可变的默认值

​	Python 在类属性中存储默认成员变量值。思考这个例子，不使用数据类:

```
class C:
    x = []
    def add(self, element):
        self.x.append(element)

o1 = C()
o2 = C()
o1.add(1)
o2.add(2)
assert o1.x == [1, 2]
assert o1.x is o2.x
```

​	请注意类 `C` 的两个实例将共享同一个类变量 `x`，正如预期的那样。

​	使用数据类，*如果* 此代码有效：

```
@dataclass
class D:
    x: list = []      # This code raises ValueError
    def add(self, element):
        self.x.append(element)
```

​	它生成的代码类似于:

```
class D:
    x = []
    def __init__(self, x=x):
        self.x = x
    def add(self, element):
        self.x.append(element)

assert D().x is D().x
```

​	这具有与使用 `C` 类的原始示例相同的问题。 也就是说，当创建类实例时如果 `D` 类的两个实例没有为 `x` 指定值则将共享同一个 `x` 的副本。 因为数据类只是使用普通的 Python 类创建方式所以它们也会共享此行为。 数据类没有任何通用方式来检测这种情况。 相反地，[`@dataclass`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.dataclass) 装饰器在检测到不可哈希的默认形参时将会引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。 这一行为假定如果一个值是不可哈希的，则它就是可变对象。 这是一个部分解决方案，但它确实能防止许多常见错误。

​	使用默认工厂函数是一种创建可变类型新实例的方法，并将其作为字段的默认值:

```
@dataclass
class D:
    x: list = field(default_factory=list)

assert D().x is not D().x
```

*在 3.11 版本发生变更:* 现在不再是寻找并阻止使用类型为 [`list`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#list), [`dict`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#dict) 或 [`set`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#set) 的对象，而是不允许将不可哈希的对象用作默认值。 就是不可哈希性被作为不可变性的近似物了。

## 描述器类型的字段

​	当字段被 [描述器对象](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#descriptors) 赋值为默认值时会遵循以下行为:

- 传递给数据类的 [`__init__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__init__) 方法的字段值会被传递给描述器的 [`__set__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__set__) 方法而不会覆盖描述器对象。
- 类似地，当获取或设置字段值时，将调用描述器的 [`__get__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__get__) 或 `__set__()` 方法而不是返回或重写描述器对象。
- 为了确定一个字段是否包含默认值，[`@dataclass`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.dataclass) 会使用类访问形式调用描述器的 `__get__()` 方法: `descriptor.__get__(obj=None, type=cls)`。 如果在此情况下描述器返回了一个值，它将被用作字段的默认值。 另一方面，如果在此情况下描述器引发了 [`AttributeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#AttributeError)，则不会为字段提供默认值。

```
class IntConversionDescriptor:
    def __init__(self, *, default):
        self._default = default

    def __set_name__(self, owner, name):
        self._name = "_" + name

    def __get__(self, obj, type):
        if obj is None:
            return self._default

        return getattr(obj, self._name, self._default)

    def __set__(self, obj, value):
        setattr(obj, self._name, int(value))

@dataclass
class InventoryItem:
    quantity_on_hand: IntConversionDescriptor = IntConversionDescriptor(default=100)

i = InventoryItem()
print(i.quantity_on_hand)   # 100
i.quantity_on_hand = 2.5    # calls __set__ with 2.5
print(i.quantity_on_hand)   # 2
```

​	若一个字段的类型是描述器，但其默认值并不是描述器对象，那么该字段只会像普通的字段一样工作。
