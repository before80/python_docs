+++
title = "typing —— 对类型提示的支持"
date = 2024-11-15T21:03:03+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/typing.html](https://docs.python.org/zh-cn/3.13/library/typing.html)
>
> 收录该文档的时间：`2024-11-15T21:03:03+08:00`

# [`typing`]({{< ref "/library/development/typing#module-typing" >}}) —— 对类型提示的支持

> Added in version 3.5.
>

**源代码：** [Lib/typing.py](https://github.com/python/cpython/tree/3.13/Lib/typing.py)

​	备注

 

​	Python 运行时不强制要求函数与变量类型标注。 它们可被 [类型检查器]({{< ref "/glossary/idx#term-static-type-checker" >}})、IDE、语法检查器等第三方工具使用。

------

​	本模块提供了对类型提示的运行时支持。

​	考虑下面的函数:

```
def surface_area_of_cube(edge_length: float) -> str:
    return f"The surface area of the cube is {6 * edge_length ** 2}."
```

​	函数 `surface_area_of_cube` 接受一个预期为 [`float`]({{< ref "/library/functions#float" >}}) 实例的参数，如 [type hint]({{< ref "/glossary/idx#term-type-hint" >}}) `edge_length: float` 所指明的。 该函数预期返回一个 [`str`]({{< ref "/library/stdtypes#str" >}}) 实例，如 `-> str` 提示所指明的。

​	类型提示可以是简单的类比如 [`float`]({{< ref "/library/functions#float" >}}) 或 [`str`]({{< ref "/library/stdtypes#str" >}})，它们也可以更为复杂。 [`typing`]({{< ref "/library/development/typing#module-typing" >}}) 模块提供了一套用于更高级类型提示的词汇。

​	新特性被频繁添加到 `typing` 模块中。 [typing_extensions](https://pypi.org/project/typing_extensions/) 包提供了这些新特性针对较旧版本 Python 的向下移植。

​	参见

## ["类型系统备忘单"](https://mypy.readthedocs.io/en/stable/cheat_sheet_py3.html)

​	关于类型提示的概览（发布于 mypy 文档站点）

## [mypy 文档](https://mypy.readthedocs.io/en/stable/index.html) 的 "Type System Reference" 章节

​	Python 类型系统是通过 PEP 来标准化的，因此该参考应当广泛适用于大多数 Python 类型检查器。 （但某些部分仍然是 mypy 专属的。）

## ["Static Typing with Python"](https://typing.readthedocs.io/en/latest/)

​	由社区编写的不限定具体类型检查器的文档，详细讲解了类型系统特性，有用的类型相关工具以及类型的最佳实践。



## 有关 Python 类型系统的规范说明

​	Python 类型系统最新的规范说明可以在 ["Specification for the Python type system"](https://typing.readthedocs.io/en/latest/spec/index.html) 查看。



## 类型别名

​	类型别名是使用 [`type`]({{< ref "/reference/simple_stmts#type" >}}) 语句来定义的，它将创建一个 [`TypeAliasType`]({{< ref "/library/development/typing#typing.TypeAliasType" >}}) 的实例。 在这个示例中，`Vector` 和 `list[float]` 将被静态类型检查器等同处理:

```
type Vector = list[float]

def scale(scalar: float, vector: Vector) -> Vector:
    return [scalar * num for num in vector]

# 通过类型检查；浮点数列表是合格的 Vector。
new_vector = scale(2.0, [1.0, -4.2, 5.4])
```

​	类型别名适用于简化复杂的类型签名。例如：

```
from collections.abc import Sequence

type ConnectionOptions = dict[str, str]
type Address = tuple[str, int]
type Server = tuple[Address, ConnectionOptions]

def broadcast_message(message: str, servers: Sequence[Server]) -> None:
    ...

# 静态类型检查器会认为上面的类型签名
# 完全等价于下面这个写法。
def broadcast_message(
    message: str,
    servers: Sequence[tuple[tuple[str, int], dict[str, str]]]
) -> None:
    ...
```

[`type`]({{< ref "/reference/simple_stmts#type" >}}) 语句是在 Python 3.12 中新增加的。 为了向下兼容，类型别名也可以通过简单的赋值来创建:

```
Vector = list[float]
```

​	或者用 [`TypeAlias`]({{< ref "/library/development/typing#typing.TypeAlias" >}}) 标记来显式说明这是一个类型别名，而非一般的变量赋值：

```
from typing import TypeAlias

Vector: TypeAlias = list[float]
```



## NewType

​	用 [`NewType`]({{< ref "/library/development/typing#typing.NewType" >}}) 助手创建与原类型不同的类型：

```
from typing import NewType

UserId = NewType('UserId', int)
some_id = UserId(524313)
```

​	静态类型检查器把新类型当作原始类型的子类，这种方式适用于捕捉逻辑错误：

```
def get_user_name(user_id: UserId) -> str:
    ...

# 通过类型检查
user_a = get_user_name(UserId(42351))

# 未通过类型检查；整数不能作为 UserId
user_b = get_user_name(-1)
```

`UserId` 类型的变量可执行所有 `int` 操作，但返回结果都是 `int` 类型。这种方式允许在预期 `int` 时传入 `UserId`，还能防止意外创建无效的 `UserId`：

```
# 'output' 的类型为 'int' 而非 'UserId'
output = UserId(23413) + UserId(54341)
```

​	注意，这些检查只由静态类型检查器强制执行。在运行时，语句 `Derived = NewType('Derived', Base)` 将产生一个 `Derived` 可调用对象，该对象立即返回你传递给它的任何参数。 这意味着语句 `Derived(some_value)` 不会创建一个新的类，也不会引入超出常规函数调用的很多开销。

​	更确切地说，在运行时，`some_value is Derived(some_value)` 表达式总为 True。

​	创建 `Derived` 的子类型是无效的:

```
from typing import NewType

UserId = NewType('UserId', int)

# 将在运行时失败且无法通过类型检查
class AdminUserId(UserId): pass
```

​	然而，我们可以在 "派生的" `NewType` 的基础上创建一个 [`NewType`]({{< ref "/library/development/typing#typing.NewType" >}})。

```
from typing import NewType

UserId = NewType('UserId', int)

ProUserId = NewType('ProUserId', UserId)
```

​	同时，`ProUserId` 的类型检查也可以按预期执行。

​	详见 [**PEP 484**](https://peps.python.org/pep-0484/)。

​	备注

 

​	请记住使用类型别名将声明两个类型是相互 *等价* 的。 使用 `type Alias = Original` 将使静态类型检查器在任何情况下都把 `Alias` 视为与 `Original` *完全等价*。 这在你想要简化复杂的类型签名时会很有用处。

​	反之，`NewType` 声明把一种类型当作另一种类型的 *子类型*。`Derived = NewType('Derived', Original)` 时，静态类型检查器把 `Derived` 当作 `Original` 的 *子类* ，即，`Original` 类型的值不能用在预期 `Derived` 类型的位置。这种方式适用于以最小运行时成本防止逻辑错误。

> Added in version 3.5.2.
>

*在 3.10 版本发生变更:* `NewType` 现在是一个类而不是一个函数。 因此，当调用 `NewType` 而非常规函数时会有一些额外的运行时开销。

*在 3.11 版本发生变更:* 调用 `NewType` 的性能已恢复到 Python 3.9 时的水平。



## 标注可调用对象

​	函数 -- 或是其他 [callable]({{< ref "/glossary/idx#term-callable" >}}) 对象 -- 可以使用 [`collections.abc.Callable`]({{< ref "/library/datatypes/collections_abc#collections.abc.Callable" >}}) 或已被弃用的 [`typing.Callable`]({{< ref "/library/development/typing#typing.Callable" >}}) 来标注。 `Callable[[int], str]` 表示一个接受 [`int`]({{< ref "/library/functions#int" >}}) 类型的单个形参并返回一个 [`str`]({{< ref "/library/stdtypes#str" >}}) 的函数。

​	例如:

```
from collections.abc import Callable, Awaitable

def feeder(get_next_item: Callable[[], str]) -> None:
    ...  # 函数体

def async_query(on_success: Callable[[int], None],
                on_error: Callable[[int, Exception], None]) -> None:
    ...  # 函数体

async def on_update(value: str) -> None:
    ...  # 函数体

callback: Callable[[str], Awaitable[None]] = on_update
```

​	下标语法总是要刚好使用两个值：参数列表和返回类型。 参数列表必须是一个由类型组成的列表、[`ParamSpec`]({{< ref "/library/development/typing#typing.ParamSpec" >}})、[`Concatenate`]({{< ref "/library/development/typing#typing.Concatenate" >}}) 或省略号。 返回类型必须是单一类型。

​	如果将一个省略号字面值 `...` 作为参数列表，则表示可以接受包含任意形参列表的可调用对象:

```
def concat(x: str, y: str) -> str:
    return x + y

x: Callable[..., str]
x = str     # 可以
x = concat  # 同样可以
```

`Callable` 无法表达复杂的签名如接受可变数量参数的函数，[重载的函数]({{< ref "/library/development/typing#overload" >}})，或具有仅限关键字形参的函数。 但是，这些签名可通过自定义具有 [`__call__()`]({{< ref "/reference/datamodel#object.__call__" >}}) 方法的 [`Protocol`]({{< ref "/library/development/typing#typing.Protocol" >}}) 类来表达：

```
from collections.abc import Iterable
from typing import Protocol

class Combiner(Protocol):
    def __call__(self, *vals: bytes, maxlen: int | None = None) -> list[bytes]: ...

def batch_proc(data: Iterable[bytes], cb_results: Combiner) -> bytes:
    for item in data:
        ...

def good_cb(*vals: bytes, maxlen: int | None = None) -> list[bytes]:
    ...
def bad_cb(*vals: bytes, maxitems: int | None) -> list[bytes]:
    ...

batch_proc([], good_cb)  # 可以
batch_proc([], bad_cb)   # 错误！参数 2 的类型不兼容
                         # 因为在回调中有不同的名称和类别
```

​	以其他可调用对象为参数的可调用对象可以使用 [`ParamSpec`]({{< ref "/library/development/typing#typing.ParamSpec" >}}) 来表明其参数类型是相互依赖的。 此外，如果该可调用对象增加或删除了其他可调用对象的参数，可以使用 [`Concatenate`]({{< ref "/library/development/typing#typing.Concatenate" >}}) 操作符。 它们分别采取 `Callable[ParamSpecVariable, ReturnType]` 和 `Callable[Concatenate[Arg1Type, Arg2Type, ..., ParamSpecVariable], ReturnType]` 的形式。

*在 3.10 版本发生变更:* `Callable` 现在支持 [`ParamSpec`]({{< ref "/library/development/typing#typing.ParamSpec" >}}) 和 [`Concatenate`]({{< ref "/library/development/typing#typing.Concatenate" >}})。 详情见 [**PEP 612**](https://peps.python.org/pep-0612/)。

​	参见

 

[`ParamSpec`]({{< ref "/library/development/typing#typing.ParamSpec" >}}) 和 [`Concatenate`]({{< ref "/library/development/typing#typing.Concatenate" >}}) 的文档提供了在 `Callable` 中使用的例子。



## 泛型（Generic）

​	由于无法以通用方式静态地推断容器中保存的对象的类型信息，标准库中的许多容器类都支持下标操作来以表示容器元素的预期类型。

```
from collections.abc import Mapping, Sequence

class Employee: ...

# Sequence[Employee] 表明该序列中的所有元素
# 都必须是 "Employee" 的实例。
# Mapping[str, str] 表明该映射中的所有键和所有值
# 都必须是字符串。
def notify_by_email(employees: Sequence[Employee],
                    overrides: Mapping[str, str]) -> None: ...
```

​	泛型函数和类可以通过使用 [类型形参语法]({{< ref "/reference/compound_stmts#type-params" >}}) 来实现参数化:

```
from collections.abc import Sequence

def first[T](l: Sequence[T]) -> T:  # 函数是 TypeVar "T" 泛型
    return l[0]
```

​	或直接使用 [`TypeVar`]({{< ref "/library/development/typing#typing.TypeVar" >}}) 工厂：

```
from collections.abc import Sequence
from typing import TypeVar

U = TypeVar('U')                  # 声明类型变量 "U"

def second(l: Sequence[U]) -> U:  # 函数是 TypeVar "U" 泛型
    return l[1]
```

*在 3.12 版本发生变更:* 对泛型的语法支持是在 Python 3.12 中新增的。



## 标注元组

​	对于 Python 中的大多数容器，类型系统会假定容器中的所有元素都是相同类型的。 例如:

```
from collections.abc import Mapping

# 类型检查器将推断 ``x`` 中的所有元素均为整数
x: list[int] = []

# 类型检查器错误: ``list`` 只接受单个类型参数：
y: list[int, str] = [1, 'foo']

# 类型检查器将推断 ``z`` 中的所有键均为字符串，
# 并且 ``z`` 中的所有值均为字符串或整数
z: Mapping[str, str | int] = {}
```

[`list`]({{< ref "/library/stdtypes#list" >}}) 只接受一个类型参数，因此类型检查器将在上述代码中对 `y` 赋值时报告错误。同样，[`Mapping`]({{< ref "/library/datatypes/collections_abc#collections.abc.Mapping" >}}) 只接受两个类型参数：第一个给出键的类型，第二个则给出值的类型。

​	然而，与大多数其它 Python 容器不同的是，在常见的 Python 代码中，元组中元素的类型并不相同。因此，在 Python 的类型系统中，元组是特殊情况。[`tuple`]({{< ref "/library/stdtypes#tuple" >}}) 可以接受 *任意数量* 的类型参数：

```
# 可以: ``x`` 被赋值为长度为 1 的元组，其中的唯一元素是个整数
x: tuple[int] = (5,)

# 可以: ``y`` 被赋值为长度为 2 的元素；
# 第 1 个元素是个整数，第 2 个元素是个字符串
y: tuple[int, str] = (5, "foo")

# 错误: 类型标注表明是长度为 1 的元组，
# 但 ``z`` 却被赋值为长度为 3 的元组
z: tuple[int] = (1, 2, 3)
```

​	要表示一个可以是 *任意* 长度的元组，并且其中的所有元素都是相同类型的 `T`，请使用 `tuple[T, ...]`。要表示空元组，请使用 `tuple[()]`。只使用 `tuple` 作为注解等效于使用``tuple[Any, ...]``：

```
x: tuple[int, ...] = (1, 2)
# 这些赋值是可以的 OK: ``tuple[int, ...]`` 表明 x 可以为任意长度
x = (1, 2, 3)
x = ()
# 这个赋值是错误的: ``x`` 中的所有元素都必须为整数
x = ("foo", "bar")

# ``y`` 只能被赋值为一个空元组
y: tuple[()] = ()

z: tuple = ("foo", "bar")
# 这些重新赋值是可以的 OK: 简单的 ``tuple`` 等价于 ``tuple[Any, ...]``
z = (1, 2, 3)
z = ()
```



## 类对象的类型

​	带有 `C` 标注的变量可接受 `C` 类型的值。 反之，带有 `type[C]` (或已被弃用的 [`typing.Type[C\]`]({{< ref "/library/development/typing#typing.Type" >}})) 标注的变量则可接受本身是类的值 -- 准确地说，它将接受 `C` 的 *类对象*。 例如:

```
a = 3         # 为 ``int`` 类型
b = int       # 为 ``type[int]`` 类型
c = type(a)   # 同样为 ``type[int]`` 类型
```

​	注意，`type[C]` 是协变的：

```
class User: ...
class ProUser(User): ...
class TeamUser(User): ...

def make_new_user(user_class: type[User]) -> User:
    # ...
    return user_class()

make_new_user(User)      # 可以
make_new_user(ProUser)   # 同样可以: ``type[ProUser]`` 是 ``type[User]`` 的子类型
make_new_user(TeamUser)  # 仍然可以
make_new_user(User())    # 错误: 预期为 ``type[User]`` 但得到 ``User``
make_new_user(int)       # 错误: ``type[int]`` 不是 ``type[User]`` 的子类型
```

[`type`]({{< ref "/library/functions#type" >}}) 的合法形参只有类, [`Any`]({{< ref "/library/development/typing#typing.Any" >}}), [类型变量]({{< ref "/library/development/typing#generics" >}}) 以及前面这些类型的并集。 例如:

```
def new_non_team_user(user_class: type[BasicUser | ProUser]): ...

new_non_team_user(BasicUser)  # 可以
new_non_team_user(ProUser)    # 可以
new_non_team_user(TeamUser)   # 错误: ``type[TeamUser]`` 不是
                              # ``type[BasicUser | ProUser]`` 的子类型
new_non_team_user(User)       # 同样错误
```

`type[Any]` 等价于 [`type`]({{< ref "/library/functions#type" >}})，它是 Python 的 [元类层级结构]({{< ref "/reference/datamodel#metaclasses" >}}) 的根对象。



## 标注生成器和协程

​	生成器可以使用泛型类型 [`Generator[YieldType, SendType, ReturnType\]`]({{< ref "/library/datatypes/collections_abc#collections.abc.Generator" >}}) 来标。 例如:

```
def echo_round() -> Generator[int, float, str]:
    sent = yield 0
    while sent >= 0:
        sent = yield round(sent)
    return 'Done'
```

​	请注意与标准库里的许多其他泛型类不同，[`Generator`]({{< ref "/library/datatypes/collections_abc#collections.abc.Generator" >}}) 的 `SendType` 采用逆变行为，而不是协变或不变行为。

`SendType` 和 `ReturnType` 形参默认为 `None`：

```
def infinite_stream(start: int) -> Generator[int]:
    while True:
        yield start
        start += 1
```

​	也可以显式设置这些类型：

```
def infinite_stream(start: int) -> Generator[int, None, None]:
    while True:
        yield start
        start += 1
```

​	仅产生值的简单生成器可以被标注为具有 [`Iterable[YieldType\]`]({{< ref "/library/datatypes/collections_abc#collections.abc.Iterable" >}}) 或 [`Iterator[YieldType\]`]({{< ref "/library/datatypes/collections_abc#collections.abc.Iterator" >}}) 类型的返回值:

```
def infinite_stream(start: int) -> Iterator[int]:
    while True:
        yield start
        start += 1
```

​	异步生成器的处理方式类似，但不要指望有 `ReturnType` 类型参数 ([`AsyncGenerator[YieldType, SendType\]`]({{< ref "/library/datatypes/collections_abc#collections.abc.AsyncGenerator" >}}))。 `SendType` 参数默认为 `None`，因此以下定义是等价的:

```
async def infinite_stream(start: int) -> AsyncGenerator[int]:
    while True:
        yield start
        start = await increment(start)

async def infinite_stream(start: int) -> AsyncGenerator[int, None]:
    while True:
        yield start
        start = await increment(start)
```

​	与同步情况一样，[`AsyncIterable[YieldType\]`]({{< ref "/library/datatypes/collections_abc#collections.abc.AsyncIterable" >}}) 和 [`AsyncIterator[YieldType\]`]({{< ref "/library/datatypes/collections_abc#collections.abc.AsyncIterator" >}}) 也可用:

```
async def infinite_stream(start: int) -> AsyncIterator[int]:
    while True:
        yield start
        start = await increment(start)
```

​	协程可使用 [`[YieldType, SendType, ReturnType\]`]({{< ref "/library/datatypes/collections_abc#collections.abc.Coroutine" >}}) 进行注释。 泛型参数对应于 [`Generator`]({{< ref "/library/datatypes/collections_abc#collections.abc.Generator" >}}) 的参数，例如:

```
from collections.abc import Coroutine
c: Coroutine[list[str], str, int]  # 在其他地方定义的协程
x = c.send('hi')                   # 推断 'x' 的类型为 list[str]
async def bar() -> None:
    y = await c                    # 推断 'y' 的类型为 int
```



## 用户定义的泛型类型

​	用户定义的类可以定义为泛型类。

```
from logging import Logger

class LoggedVar[T]:
    def __init__(self, value: T, name: str, logger: Logger) -> None:
        self.name = name
        self.logger = logger
        self.value = value

    def set(self, new: T) -> None:
        self.log('Set ' + repr(self.value))
        self.value = new

    def get(self) -> T:
        self.log('Get ' + repr(self.value))
        return self.value

    def log(self, message: str) -> None:
        self.logger.info('%s: %s', self.name, message)
```

​	这种语法表示类 `LoggedVar` 是围绕单个 [类型变量]({{< ref "/library/development/typing#typevar" >}}) `T` 实现参数化的。 这也使得 `T` 成为类体内部有效的类型。

​	泛型类隐式继承自 [`Generic`]({{< ref "/library/development/typing#typing.Generic" >}})。为了与 Python 3.11 及更低版本兼容，也允许显式地从 [`Generic`]({{< ref "/library/development/typing#typing.Generic" >}}) 继承以表示泛型类：

```
from typing import TypeVar, Generic

T = TypeVar('T')

class LoggedVar(Generic[T]):
    ...
```

​	泛型类具有 [`__class_getitem__()`]({{< ref "/reference/datamodel#object.__class_getitem__" >}}) 方法，这意味着泛型类可在运行时进行参数化（例如下面的 `LoggedVar[int]`）：

```
from collections.abc import Iterable

def zero_all_vars(vars: Iterable[LoggedVar[int]]) -> None:
    for var in vars:
        var.set(0)
```

​	一个泛型可以有任何数量的类型变量。所有种类的 [`TypeVar`]({{< ref "/library/development/typing#typing.TypeVar" >}}) 都可以作为泛型的参数:

```
from typing import TypeVar, Generic, Sequence

class WeirdTrio[T, B: Sequence[bytes], S: (int, str)]:
    ...

OldT = TypeVar('OldT', contravariant=True)
OldB = TypeVar('OldB', bound=Sequence[bytes], covariant=True)
OldS = TypeVar('OldS', int, str)

class OldWeirdTrio(Generic[OldT, OldB, OldS]):
    ...
```

[`Generic`]({{< ref "/library/development/typing#typing.Generic" >}}) 类型变量的参数应各不相同。下列代码就是无效的：

```
from typing import TypeVar, Generic
...

class Pair[M, M]:  # SyntaxError
    ...

T = TypeVar('T')

class Pair(Generic[T, T]):   # 无效
    ...
```

​	泛型类也可以从其他类继承：

```
from collections.abc import Sized

class LinkedList[T](Sized):
    ...
```

​	从泛型类继承时，某些类型参数可被固定：

```
from collections.abc import Mapping

class MyDict[T](Mapping[str, T]):
    ...
```

​	在这个例子中，`MyDict` 就只有一个参数 `T`。

​	未指定泛型类的类型参数时，会假定每个位置的类型都为 [`Any`]({{< ref "/library/development/typing#typing.Any" >}})。在下面的例子中，`MyIterable` 不是泛型，但却隐式继承了 `Iterable[Any]`：

```
from collections.abc import Iterable

class MyIterable(Iterable): # 与 Iterable[Any] 相同
    ...
```

​	用户定义的泛型类型别名也同样受到支持。例如：

```
from collections.abc import Iterable

type Response[S] = Iterable[S] | int

# 这里的返回类型与 Iterable[str] | int 相同
def response(query: str) -> Response[str]:
    ...

type Vec[T] = Iterable[tuple[T, T]]

def inproduct[T: (int, float, complex)](v: Vec[T]) -> T: # 与 Iterable[tuple[T, T]] 相同
    return sum(x*y for x, y in v)
```

​	出于向后兼容性的考虑，也允许使用简单的赋值来创建泛型类型别名：

```
from collections.abc import Iterable
from typing import TypeVar

S = TypeVar("S")
Response = Iterable[S] | int
```

*在 3.7 版本发生变更:* [`Generic`]({{< ref "/library/development/typing#typing.Generic" >}}) 不再支持自定义元类。

*在 3.12 版本发生变更:* 3.12 版本新增了对泛型和类型别名的语法支持。在之前的版本中，泛型类必须显式继承自 [`Generic`]({{< ref "/library/development/typing#typing.Generic" >}})，或者在其基类之一中包含有类型变量。

​	用户定义的参数表达式的泛型也受到支持，可以采用 `[**P]` 形式的参数规格变量来表示。该行为与上面描述的类型变量一致，因为参数规格变量被 typing 模块视为专门的类型变量。这方面的一个例外是，类型的列表可用于替代 [`ParamSpec`]({{< ref "/library/development/typing#typing.ParamSpec" >}})：



``` python
>>> class Z[T, **P]: ...  # T 为 TypeVar；P 为 ParamSpec
...
>>> Z[int, [dict, float]]
__main__.Z[int, [dict, float]]
```

​	带有 [`ParamSpec`]({{< ref "/library/development/typing#typing.ParamSpec" >}}) 的泛型类也可以使用从 [`Generic`]({{< ref "/library/development/typing#typing.Generic" >}}) 显式继承的方式来创建。在这种情况下，不需要使用 `**`：

```
from typing import ParamSpec, Generic

P = ParamSpec('P')

class Z(Generic[P]):
    ...
```

[`TypeVar`]({{< ref "/library/development/typing#typing.TypeVar" >}}) 与 [`ParamSpec`]({{< ref "/library/development/typing#typing.ParamSpec" >}}) 的另一个区别在于只有单个参数规格变量的泛型会接受形如 `X[[Type1, Type2, ...]]` 的参数列表，同时为了美观，也接受 `X[Type1, Type2, ...]` 这样的形式。 在内部，后者被转换为前者，所以下面的内容是等价的：



``` python
>>> class X[**P]: ...
...
>>> X[int, str]
__main__.X[[int, str]]
>>> X[[int, str]]
__main__.X[[int, str]]
```

​	请注意：在某些情况下，具有 [`ParamSpec`]({{< ref "/library/development/typing#typing.ParamSpec" >}}) 的泛型在替换后可能不具有正确的 `__parameters__`，因为参数规格主要用于静态类型检查。

*在 3.10 版本发生变更:* [`Generic`]({{< ref "/library/development/typing#typing.Generic" >}}) 现在可以通过参数表达式进行参数化。参见 [`ParamSpec`]({{< ref "/library/development/typing#typing.ParamSpec" >}}) 和 [**PEP 612**](https://peps.python.org/pep-0612/) 以了解更多细节。

​	用户定义的泛型类可以将 ABC 作为基类而不会导致元类冲突。 参数化泛型的输出结果会被缓存，且 typing 模块中的大多数类型都是 [hashable]({{< ref "/glossary/idx#term-hashable" >}}) 并且支持相等性比较。

## [`Any`]({{< ref "/library/development/typing#typing.Any" >}}) 类型

[`Any`]({{< ref "/library/development/typing#typing.Any" >}}) 是一种特殊的类型。静态类型检查器认为所有类型均与 [`Any`]({{< ref "/library/development/typing#typing.Any" >}}) 兼容，同样，[`Any`]({{< ref "/library/development/typing#typing.Any" >}}) 也与所有类型兼容。

​	也就是说，可对 [`Any`]({{< ref "/library/development/typing#typing.Any" >}}) 类型的值执行任何操作或方法调用，并赋值给任意变量：

```
from typing import Any

a: Any = None
a = []          # 可以
a = 2           # 可以

s: str = ''
s = a           # 可以

def foo(item: Any) -> int:
    # 通过类型检查；'item' 可以为任意类型，
    # 并且其类型会具有 'bar' 方法
    item.bar()
    ...
```

​	注意，[`Any`]({{< ref "/library/development/typing#typing.Any" >}}) 类型的值赋给更精确的类型时，不执行类型检查。例如，把 `a` 赋给 `s`，在运行时，即便 `s` 已声明为 [`str`]({{< ref "/library/stdtypes#str" >}}) 类型，但接收 [`int`]({{< ref "/library/functions#int" >}}) 值时，静态类型检查器也不会报错。

​	此外，未指定返回值与参数类型的函数，都隐式地默认使用 [`Any`]({{< ref "/library/development/typing#typing.Any" >}})：

```
def legacy_parser(text):
    ...
    return data

# 静态类型检查器将认为上面的函数
# 具有与下面的函数相同的签名：
def legacy_parser(text: Any) -> Any:
    ...
    return data
```

​	需要混用动态与静态类型代码时，此操作把 [`Any`]({{< ref "/library/development/typing#typing.Any" >}}) 当作 *应急出口*。

[`Any`]({{< ref "/library/development/typing#typing.Any" >}}) 和 [`object`]({{< ref "/library/functions#object" >}}) 的区别。与 [`Any`]({{< ref "/library/development/typing#typing.Any" >}}) 相似，所有类型都是 [`object`]({{< ref "/library/functions#object" >}}) 的子类型。然而，与 [`Any`]({{< ref "/library/development/typing#typing.Any" >}}) 不同，object 不可逆：[`object`]({{< ref "/library/functions#object" >}}) *不是* 其它类型的子类型。

​	就是说，值的类型是 [`object`]({{< ref "/library/functions#object" >}}) 时，类型检查器几乎会拒绝所有对它的操作，并且，把它赋给更精确的类型变量（或返回值）属于类型错误。例如：

```
def hash_a(item: object) -> int:
    # 不能通过类型检查；对象没有 'magic' 方法。
    item.magic()
    ...

def hash_b(item: Any) -> int:
    # 通过类型检查
    item.magic()
    ...

# 通过类型检查，因为整数和字符串都是 object 的子类
hash_a(42)
hash_a("foo")

# 通过类型检查，因为 Any 可以兼容所有类型
hash_b(42)
hash_b("foo")
```

​	使用 [`object`]({{< ref "/library/functions#object" >}})，说明值能以类型安全的方式转为任何类型。使用 [`Any`]({{< ref "/library/development/typing#typing.Any" >}})，说明值是动态类型。

## 名义子类型 vs 结构子类型

​	最初 [**PEP 484**](https://peps.python.org/pep-0484/) 将 Python 静态类型系统定义为使用 *名义子类型*。这意味着当且仅当类 `A` 是 `B` 的子类时，才满足有类 `B` 预期时使用类 `A` 。

​	此项要求以前也适用于抽象基类，例如，[`Iterable`]({{< ref "/library/datatypes/collections_abc#collections.abc.Iterable" >}}) 。这种方式的问题在于，定义类时必须显式说明，既不 Pythonic，也不是动态类型式 Python 代码的惯用写法。例如，下列代码就遵从了 [**PEP 484**](https://peps.python.org/pep-0484/) 的规范：

```
from collections.abc import Sized, Iterable, Iterator

class Bucket(Sized, Iterable[int]):
    ...
    def __len__(self) -> int: ...
    def __iter__(self) -> Iterator[int]: ...
```

[**PEP 544**](https://peps.python.org/pep-0544/) 允许用户在类定义时不显式说明基类，从而解决了这一问题，静态类型检查器隐式认为 `Bucket` 既是 `Sized` 的子类型，又是 `Iterable[int]` 的子类型。这就是 *结构子类型* （又称为静态鸭子类型）：

```
from collections.abc import Iterator, Iterable

class Bucket:  # 注意：没有基类
    ...
    def __len__(self) -> int: ...
    def __iter__(self) -> Iterator[int]: ...

def collect(items: Iterable[int]) -> int: ...
result = collect(Bucket())  # 通过类型检查
```

​	此外，结构子类型的优势在于，通过继承特殊类 [`Protocol`]({{< ref "/library/development/typing#typing.Protocol" >}}) ，用户可以定义新的自定义协议（见下文中的例子）。

## 模块内容

`typing` 模块定义了下列类、函数和装饰器。

### 特殊类型原语

#### 特殊类型

​	这些类型可用于在注解中表示类型，但不支持下标用法（`[]`）。

## typing.**Any**

​	特殊类型，表示没有约束的类型。

- 所有类型都与 [`Any`]({{< ref "/library/development/typing#typing.Any" >}}) 兼容。
- [`Any`]({{< ref "/library/development/typing#typing.Any" >}}) 与所有类型都兼容。

*在 3.11 版本发生变更:* [`Any`]({{< ref "/library/development/typing#typing.Any" >}}) 现在可以用作基类。这有助于避免类型检查器在高度动态或可通过鸭子类型使用的类上报错。

## typing.**AnyStr**

[受约束的类型变量]({{< ref "/library/development/typing#typing-constrained-typevar" >}})。

​	定义：

```
AnyStr = TypeVar('AnyStr', str, bytes)
```

`AnyStr` 用于可接受 [`str`]({{< ref "/library/stdtypes#str" >}}) 或 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 参数但不允许两者混用的函数。

​	例如：

```
def concat(a: AnyStr, b: AnyStr) -> AnyStr:
    return a + b

concat("foo", "bar")    # 可以，输出为 'str' 类型
concat(b"foo", b"bar")  # 可以，输出为 'bytes' 类型
concat("foo", b"bar")   # 错误，不可混用 str 和 bytes
```

​	请注意：尽管名为 `AnyStr`，但它与 [`Any`]({{< ref "/library/development/typing#typing.Any" >}}) 类型毫无关系，也不是指“任何字符串”。而且，`AnyStr` 更是和 `str | bytes` 彼此互不相同，各有各的使用场景：

```
# AnyStr 的无效使用:
# 类型变量在函数签名中仅使用一次，
# 因此无法通过类型检查器“解决”
def greet_bad(cond: bool) -> AnyStr:
    return "hi there!" if cond else b"greetings!"

# 注释此函数的更好方法:
def greet_proper(cond: bool) -> str | bytes:
    return "hi there!" if cond else b"greetings!"
```

*Deprecated since version 3.13, will be removed in version 3.18:* 已被弃用而应改用新的 [类型形参语法]({{< ref "/reference/compound_stmts#type-params" >}})。 使用 `class A[T: (str, bytes)]: ...` 而不是导入 `AnyStr`。 详情参见 [**PEP 695**](https://peps.python.org/pep-0695/)。

​	在 Python 3.16 中，`AnyStr` 将从 `typing.__all__` 中被移除，在运行时当它被访问或从 `typing` 导入时将发出弃用警告。 在 Python 3.18 中 `AnyStr` 将从 `typing` 中被移除。

## typing.**LiteralString**

​	只包括字符串字面值的的特殊类型。

​	任何字符串字面值或其他 `LiteralString` 都与 `LiteralString` 兼容。但 `str` 类型的对象不与其兼容。组合 `LiteralString` 类型的对象产生的字符串也被认为是 `LiteralString`。

​	示例:

```
def run_query(sql: LiteralString) -> None:
    ...

def caller(arbitrary_string: str, literal_string: LiteralString) -> None:
    run_query("SELECT * FROM students")  # 可以
    run_query(literal_string)  # 可以
    run_query("SELECT * FROM " + literal_string)  # 可以
    run_query(arbitrary_string)  # 类型检查器错误
    run_query(  # 类型检查器错误
        f"SELECT * FROM students WHERE name = {arbitrary_string}"
    )
```

`LiteralString` 对于会因用户可输入任意字符串而导致问题的敏感 API 很有用。例如，上述两处导致类型检查器报错的代码可能容易被 SQL 注入攻击。

​	请参阅 [**PEP 675**](https://peps.python.org/pep-0675/) 了解详情。

> Added in version 3.11.
>

## typing.**Never**

## typing.**NoReturn**

`Never` 和 `NoReturn` 代表 [底类型](https://en.wikipedia.org/wiki/Bottom_type)，一种没有成员的类型。

​	它们可被用于指明一个函数绝不会返回，例如 [`sys.exit()`]({{< ref "/library/python/sys#sys.exit" >}}):

```
from typing import Never  # 或 NoReturn

def stop() -> Never:
    raise RuntimeError('no way')
```

​	或者用于定义一个绝不应被调用的函数，因为不存在有效的参数，例如 [`assert_never()`]({{< ref "/library/development/typing#typing.assert_never" >}}):

```
from typing import Never  # 或 NoReturn

def never_call_me(arg: Never) -> None:
    pass

def int_or_str(arg: int | str) -> None:
    never_call_me(arg)  # 类型检查器错误
    match arg:
        case int():
            print("It's an int")
        case str():
            print("It's a str")
        case _:
            never_call_me(arg)  # OK, arg is of type Never (or NoReturn)
```

`Never` 和 `NoReturn` 在类型系统中具有相同的含义并且静态类型检查器会以相同的方式对待这两者。

> Added in version 3.6.2:
> 增加了 [`NoReturn`]({{< ref "/library/development/typing#typing.NoReturn" >}})。

> Added in version 3.11:
> 增加了 [`Never`]({{< ref "/library/development/typing#typing.Never" >}})。

## typing.**Self**

​	特殊类型，表示当前闭包内的类。

​	例如：

```
from typing import Self, reveal_type

class Foo:
    def return_self(self) -> Self:
        ...
        return self

class SubclassOfFoo(Foo): pass

reveal_type(Foo().return_self())  # 揭示的类型为 "Foo"
reveal_type(SubclassOfFoo().return_self())  # 揭示的类型为 "SubclassOfFoo"
```

​	此注解在语法上等价于以下代码，但形式更为简洁：

```
from typing import TypeVar

Self = TypeVar("Self", bound="Foo")

class Foo:
    def return_self(self: Self) -> Self:
        ...
        return self
```

​	通常来说，如果某些内容返回 `self`，如上面的示例所示，您应该使用 `Self` 作为返回值注解。如果 `Foo.return_self` 被注解为返回 `"Foo"`，那么类型检查器将推断从 `SubclassOfFoo.return_self` 返回的对象是 `Foo` 类型，而不是 `SubclassOfFoo`。

​	其它常见用例包括：

- 被用作替代构造器的 [`classmethod`]({{< ref "/library/functions#classmethod" >}})，它将返回 `cls` 形参的实例。
- 标注一个返回自身的 [`__enter__()`]({{< ref "/reference/datamodel#object.__enter__" >}}) 方法。

​	如果不能保证在子类中方法会返回子类的实例（而非父类的实例），则不应使用 `Self` 作为返回值注解：

```
class Eggs:
    # 在这里 self 是一个不正确的返回注释,
    # 因为返回的对象始终是 Eggs 的一个实例,
    # 即使在子类中
    def returns_eggs(self) -> "Eggs":
        return Eggs()
```

​	更多细节请参见 [**PEP 673**](https://peps.python.org/pep-0673/)。

> Added in version 3.11.
>

## typing.**TypeAlias**

​	特殊注解，用于显式声明 [类型别名]({{< ref "/library/development/typing#type-aliases" >}}).

​	例如：

```
from typing import TypeAlias

Factors: TypeAlias = list[int]
```

​	在较早的 Python 版本上，`TypeAlias` 对注解使用前向引用的别名时特别有用，因为类型检查器可能很难将这些别名与正常的变量赋值区分开来：

```
from typing import Generic, TypeAlias, TypeVar

T = TypeVar("T")

# "Box" 还不存在,
# 因此我们必须在 Python <3.12 版本中使用引号进行前向引用。
# 使用 ``TypeAlias`` 告诉类型检查器这是一个类型别名声明，
# 而不是对字符串的变量赋值。
BoxOfStrings: TypeAlias = "Box[str]"

class Box(Generic[T]):
    @classmethod
    def make_box_of_strings(cls) -> BoxOfStrings: ...
```

​	请参阅 [**PEP 613**](https://peps.python.org/pep-0613/) 了解详情。

> Added in version 3.10.
>

*自 3.12 版本弃用:* [`TypeAlias`]({{< ref "/library/development/typing#typing.TypeAlias" >}}) 被弃用，请使用 [`type`]({{< ref "/reference/simple_stmts#type" >}}) 语句，后者创建 [`TypeAliasType`]({{< ref "/library/development/typing#typing.TypeAliasType" >}}) 的实例，并且天然支持正向引用。请注意，虽然 [`TypeAlias`]({{< ref "/library/development/typing#typing.TypeAlias" >}}) 和 [`TypeAliasType`]({{< ref "/library/development/typing#typing.TypeAliasType" >}}) 具有相似的用途和名称，但它们是不同的，后者并不是前者的类型。目前还没有移除 [`TypeAlias`]({{< ref "/library/development/typing#typing.TypeAlias" >}}) 的计划，但鼓励用户迁移到 [`type`]({{< ref "/reference/simple_stmts#type" >}}) 语句。

#### 特殊形式

​	这些内容在注解中可以视为类型，且都支持下标用法（`[]`），但每个都有唯一的语法。

## typing.**Union**

​	联合类型； `Union[X, Y]` 等价于 `X | Y` ，意味着满足 X 或 Y 之一。

​	要定义一个联合类型，可以使用类似 `Union[int, str]` 或简写 `int | str`。建议使用这种简写。细节:

- 参数必须是某种类型，且至少有一个。

- 联合类型之联合类型会被展平，例如：

  ```
  Union[Union[int, str], float] == Union[int, str, float]
  ```

- 单参数之联合类型就是该参数自身，例如：

  ```
  Union[int] == int  # 该构造器确实返回 int
  ```

- 冗余的参数会被跳过，例如：

  ```
  Union[int, str, int] == Union[int, str] == int | str
  ```

- 比较联合类型，不涉及参数顺序，例如：

  ```
  Union[int, str] == Union[str, int]
  ```

- 不可创建 `Union` 的子类或实例。

- 没有 `Union[X][Y]` 这种写法。

*在 3.7 版本发生变更:* 在运行时，不要移除联合类型中的显式子类。

*在 3.10 版本发生变更:* 联合类型现在可以写成 `X | Y`。 参见 [联合类型表达式]({{< ref "/library/stdtypes#types-union" >}})。

## typing.**Optional**

`Optional[X]` 等价于 `X | None` （或 `Union[X, None]` ） 。

​	注意，可选类型与含默认值的可选参数不同。含默认值的可选参数不需要在类型注解上添加 `Optional` 限定符，因为它仅是可选的。例如：

```
def foo(arg: int = 0) -> None:
    ...
```

​	另一方面，显式应用 `None` 值时，不管该参数是否可选， `Optional` 都适用。例如：

```
def foo(arg: Optional[int] = None) -> None:
    ...
```

*在 3.10 版本发生变更:* 可选参数现在可以写成 `X | None`。 参见 [联合类型表达式]({{< ref "/library/stdtypes#types-union" >}})。

## typing.**Concatenate**

​	特殊形式，用于注解高阶函数。

`Concatenate` 可用于与 [Callable]({{< ref "/library/development/typing#annotating-callables" >}}) 和 [`ParamSpec`]({{< ref "/library/development/typing#typing.ParamSpec" >}}) 连用来注解高阶可调用对象，该可象可以添加、移除或转换另一个可调用对象的形参。 使用形式为 `Concatenate[Arg1Type, Arg2Type, ..., ParamSpecVariable]`。 `Concatenate` 目前仅可用作传给 [Callable]({{< ref "/library/development/typing#annotating-callables" >}}) 的第一个参数。传给 `Concatenate` 的最后一个形参必须是 [`ParamSpec`]({{< ref "/library/development/typing#typing.ParamSpec" >}}) 或省略号（ `...` ）。

​	例如，为了注释一个装饰器 `with_lock`，它为被装饰的函数提供了 [`threading.Lock`]({{< ref "/library/concurrency/threading#threading.Lock" >}})，`Concatenate` 可以用来表示 `with_lock` 期望一个可调用对象，该对象接收一个 `Lock` 作为第一个参数，并返回一个具有不同类型签名的可调用对象。 在这种情况下，[`ParamSpec`]({{< ref "/library/development/typing#typing.ParamSpec" >}}) 表示返回的可调用对象的参数类型取决于被传入的可调用程序的参数类型:

```
from collections.abc import Callable
from threading import Lock
from typing import Concatenate

# 使用此锁来确保在任何时候只有一个线程正在执行某个函数。
my_lock = Lock()

def with_lock[**P, R](f: Callable[Concatenate[Lock, P], R]) -> Callable[P, R]:
    '''A type-safe decorator which provides a lock.'''
    def inner(*args: P.args, **kwargs: P.kwargs) -> R:
        # Provide the lock as the first argument.
        return f(my_lock, *args, **kwargs)
    return inner

@with_lock
def sum_threadsafe(lock: Lock, numbers: list[float]) -> float:
    '''Add a list of numbers together in a thread-safe manner.'''
    with lock:
        return sum(numbers)

# 由于装饰器的存在，我们不需要自己传递锁。
sum_threadsafe([1.1, 2.2, 3.3])
```

> Added in version 3.10.
>

​	参见

- [**PEP 612**](https://peps.python.org/pep-0612/) -- 参数规范变量（引入 `ParamSpec` 和 `Concatenate` 的 PEP）
- [`ParamSpec`]({{< ref "/library/development/typing#typing.ParamSpec" >}})
- [标注可调用对象]({{< ref "/library/development/typing#annotating-callables" >}})

## typing.**Literal**

​	特殊类型注解形式，用于定义“字面值类型”。

`Literal` 可以用来向类型检查器说明被注解的对象具有与所提供的字面量之一相同的值。

​	例如：

```
def validate_simple(data: Any) -> Literal[True]:  # 总是返回 True
    ...

type Mode = Literal['r', 'rb', 'w', 'wb']
def open_helper(file: str, mode: Mode) -> str:
    ...

open_helper('/some/path', 'r')      # 通过类型检查
open_helper('/other/path', 'typo')  # 类型检查错误
```

`Literal[...]` 不能创建子类。在运行时，任意值均可作为 `Literal[...]` 的类型参数，但类型检查器可以对此加以限制。字面量类型详见 [**PEP 586**](https://peps.python.org/pep-0586/) 。

> Added in version 3.8.
>

*在 3.9.1 版本发生变更:* `Literal` 现在能去除形参的重复。 `Literal` 对象的相等性比较不再依赖顺序。 现在如果有某个参数不为 [hashable]({{< ref "/glossary/idx#term-hashable" >}})，`Literal` 对象在相等性比较期间将引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。

## typing.**ClassVar**

​	特殊类型注解构造，用于标注类变量。

​	如 [**PEP 526**](https://peps.python.org/pep-0526/) 所述，打包在 ClassVar 内的变量注解是指，给定属性应当用作类变量，而不应设置在类实例上。用法如下：

```
class Starship:
    stats: ClassVar[dict[str, int]] = {} # 类变量
    damage: int = 10                     # 实例变量
```

[`ClassVar`]({{< ref "/library/development/typing#typing.ClassVar" >}}) 仅接受类型，也不能使用下标。

[`ClassVar`]({{< ref "/library/development/typing#typing.ClassVar" >}}) 本身不是类，不应用于 [`isinstance()`]({{< ref "/library/functions#isinstance" >}}) 或 [`issubclass()`]({{< ref "/library/functions#issubclass" >}})。[`ClassVar`]({{< ref "/library/development/typing#typing.ClassVar" >}}) 不改变 Python 运行时行为，但可以用于第三方类型检查器。例如，类型检查器会认为以下代码有错：

```
enterprise_d = Starship(3000)
enterprise_d.stats = {} # 错误，在实例上设置类变量
Starship.stats = {}     # 这是可以的
```

> Added in version 3.5.3.
>

*在 3.13 版本发生变更:* 现在 [`ClassVar`]({{< ref "/library/development/typing#typing.ClassVar" >}}) 可以被嵌套在 [`Final`]({{< ref "/library/development/typing#typing.Final" >}}) 中，反之亦然。

## typing.**Final**

​	特殊类型注解构造，用于向类型检查器表示最终名称。

​	不能在任何作用域中重新分配最终名称。类作用域中声明的最终名称不能在子类中重写。

​	例如：

```
MAX_SIZE: Final = 9000
MAX_SIZE += 1  # 类型检查器将报告错误

class Connection:
    TIMEOUT: Final[int] = 10

class FastConnector(Connection):
    TIMEOUT = 1  # 类型检查器将报告错误
```

​	这些属性没有运行时检查。详见 [**PEP 591**](https://peps.python.org/pep-0591/)。

> Added in version 3.8.
>

*在 3.13 版本发生变更:* 现在 [`Final`]({{< ref "/library/development/typing#typing.Final" >}}) 可以被嵌套在 [`ClassVar`]({{< ref "/library/development/typing#typing.ClassVar" >}}) 中，反之亦然。

## typing.**Required**

​	特殊类型注解构造，用于标记 [`TypedDict`]({{< ref "/library/development/typing#typing.TypedDict" >}}) 键为必填项。

​	这主要用于 `total=False` 的 TypedDict。有关更多详细信息，请参阅 [`TypedDict`]({{< ref "/library/development/typing#typing.TypedDict" >}}) 和 [**PEP 655**](https://peps.python.org/pep-0655/) 。

> Added in version 3.11.
>

## typing.**NotRequired**

​	特殊类型注解构造，用于标记 [`TypedDict`]({{< ref "/library/development/typing#typing.TypedDict" >}}) 键为可能不存在的键。

​	详情参见 [`TypedDict`]({{< ref "/library/development/typing#typing.TypedDict" >}}) 和 [**PEP 655**](https://peps.python.org/pep-0655/)。

> Added in version 3.11.
>

## typing.**ReadOnly**

​	一个特殊的类型标注构造，用于将 [`TypedDict`]({{< ref "/library/development/typing#typing.TypedDict" >}}) 的项标记为只读。

​	例如：

```
class Movie(TypedDict):
   title: ReadOnly[str]
   year: int

def mutate_movie(m: Movie) -> None:
   m["year"] = 1999  # allowed
   m["title"] = "The Matrix"  # 类型检查错误
```

​	这个属性没有运行时检查。

​	详见 [`TypedDict`]({{< ref "/library/development/typing#typing.TypedDict" >}}) 和 [**PEP 705**](https://peps.python.org/pep-0705/)。

> Added in version 3.13.
>

## typing.**Annotated**

​	特殊类型注解形式，用于向注解添加特定于上下文的元数据。

​	使用注解 `Annotated[T, x]` 将元数据 `x` 添加到给定类型 `T` 。使用 `Annotated` 添加的元数据可以被静态分析工具使用，也可以在运行时使用。在运行时使用的情况下，元数据存储在 `__metadata__` 属性中。

​	如果库或工具遇到注解 `Annotated[T, x]` ，并且没有针对这一元数据的特殊处理逻辑，则应该忽略该元数据，简单地将注解视为 `T` 。因此， `Annotated` 对于希望将注解用于 Python 的静态类型注解系统之外的目的的代码很有用。

​	使用 `Annotated[T, x]` 作为注解仍然允许对 `T` 进行静态类型检查，因为类型检查器将简单地忽略元数据 `x` 。因此，`Annotated` 不同于 [`@no_type_check`]({{< ref "/library/development/typing#typing.no_type_check" >}}) 装饰器，后者虽然也可以用于在类型注解系统范围之外添加注解，但是会完全禁用对函数或类的类型检查。

​	具体解释元数据的方式由遇到 `Annotated` 注解的工具或库来负责。遇到 `Annotated` 类型的工具或库可以扫描元数据的各个元素以确定其是否有意处理（比如使用 [`isinstance()`]({{< ref "/library/functions#isinstance" >}}) ）。

## **Annotated[<type>, <metadata>]**

​	以下示例演示在进行区间范围分析时使用 `Annotated` 将元数据添加到类型注解的方法：

```
@dataclass
class ValueRange:
    lo: int
    hi: int

T1 = Annotated[int, ValueRange(-10, 5)]
T2 = Annotated[T1, ValueRange(-20, 3)]
```

​	语法细节：

- `Annotated` 的第一个参数必须是有效的类型。

- 可提供多个元数据的元素（ `Annotated` 支持可变参数）：

  ```
  @dataclass
  class ctype:
      kind: str
  
  Annotated[int, ValueRange(3, 10), ctype("char")]
  ```

  由处理注解的工具决定是否允许向一个注解中添加多个元数据元素，以及如何合并这些注解。

- `Annotated` 至少要有两个参数（ `Annotated[int]` 是无效的）

- 元数据元素的顺序会被保留，且影响等价检查：

  ```
  assert Annotated[int, ValueRange(3, 10), ctype("char")] != Annotated[
      int, ctype("char"), ValueRange(3, 10)
  ]
  ```

- 嵌套的 `Annotated` 类型会被展平。元数据元素从最内层的注解开始依次展开：

  ```
  assert Annotated[Annotated[int, ValueRange(3, 10)], ctype("char")] == Annotated[
      int, ValueRange(3, 10), ctype("char")
  ]
  ```

- 元数据中的重复元素不会被移除：

  ```
  assert Annotated[int, ValueRange(3, 10)] != Annotated[
      int, ValueRange(3, 10), ValueRange(3, 10)
  ]
  ```

- `Annotated` 可以与嵌套别名和泛型别名一起使用：

  ```
  @dataclass
  class MaxLen:
      value: int
  
  type Vec[T] = Annotated[list[tuple[T, T]], MaxLen(10)]
  
  # 当在类型注释中使用时，类型检查器会将“V”视为
  # ``Annotated[list[tuple[int, int]], MaxLen(10)]``:
  type V = Vec[int]
  ```

- `Annotated` 不能与已解包的 [`TypeVarTuple`]({{< ref "/library/development/typing#typing.TypeVarTuple" >}}) 一起使用:

  ```
  type Variadic[*Ts] = Annotated[*Ts, Ann1]  # 不可用
  ```

  这等价于：

  ```
  Annotated[T1, T2, T3, ..., Ann1]
  ```

  其中 `T1` 、 `T2` 等都是 [`TypeVars`]({{< ref "/library/development/typing#typing.TypeVar" >}}) 。这种写法无效：应当只有一个类型被传递给 Annotated。

- 默认情况下， [`get_type_hints()`]({{< ref "/library/development/typing#typing.get_type_hints" >}}) 会去除注解中的元数据。传入 `include_extras=True` 可以保留元数据：

  

  ```
  >>> from typing import Annotated, get_type_hints
  >>> def func(x: Annotated[int, "metadata"]) -> None: pass
  ...
  >>> get_type_hints(func)
  {'x': <class 'int'>, 'return': <class 'NoneType'>}
  >>> get_type_hints(func, include_extras=True)
  {'x': typing.Annotated[int, 'metadata'], 'return': <class 'NoneType'>}
  ```

- 在运行时，与特定 `Annotated` 类型相关联的元数据可通过 `__metadata__` 属性来获取：

  

  ```
  >>> from typing import Annotated
  >>> X = Annotated[int, "very", "important", "metadata"]
  >>> X
  typing.Annotated[int, 'very', 'important', 'metadata']
  >>> X.__metadata__
  ('very', 'important', 'metadata')
  ```

- 在运行时 ，如果要检索由 `Annotated` 封装的原始类型，请使用 `__origin__` 属性：

  

  ```
  >>> from typing import Annotated, get_origin
  >>> Password = Annotated[str, "secret"]
  >>> Password.__origin__
  <class 'str'>
  ```

  请注意使用 [`get_origin()`]({{< ref "/library/development/typing#typing.get_origin" >}}) 将返回 `Annotated` 本身：

  

  ```
  >>> get_origin(Password)
  typing.Annotated
  ```

​	参见

## [**PEP 593**](https://peps.python.org/pep-0593/) - 灵活的函数与变量标注

​	该 PEP 将 `Annotated` 引入到标准库中。

> Added in version 3.9.
>

## typing.**TypeIs**

​	特殊类型注解构造，用于标记用户定义的谓词函数。

`TypeIs` 能用来注解用户定义的谓词函数的返回值类型。`TypeIs` 接受单个类型参数。如此标注的函数在运行时应当有至少一个位置参数，并且返回一个布尔值。

`TypeIs` 旨在方便 *类型收窄* -- 一个被静态类型检查器使用，用来更精准地决定程序代码流中表达式类型的技巧。通常类型收窄通过分析有条件的代码流并对代码块执行类型收窄实现。此处的条件表达式有时也被称为“类型谓词”。

```
def is_str(val: str | float):
    # "isinstance" 类型谓词
    if isinstance(val, str):
        #  ``val`` 的类型缩小为 ``str``
        ...
    else:
        # 否则， ``val`` 的类型缩小为 ``float``。
        ...
```

​	使用一个用户定义的布尔函数作为类型谓词有时很方便。这样的函数应当将 `TypeIs[...]` 或 [`TypeGuard`]({{< ref "/library/development/typing#typing.TypeGuard" >}}) 作为它的返回类型，以向静态类型检查器传达这个意图。`TypeIs` 的行为通常比 `TypeGuard` 更直观，但在函数的输入与输出类型不兼容 (例如从 `list[object]` 到 `list[int]`) 或函数不会对所有收窄后类型的实例返回 `True` 时不能使用。

​	使用 `-> TypeIs[NarrowedType]` 告诉静态类型检查器对于给定的函数：

1. 返回一个布尔值。
2. 如果返回值是 `True`，那么其参数的类型收窄到参数本身类型与 `NarrowedType` 的并。
3. 如果返回值是 `False`，那么其参数的类型收窄到排除 `NarrowedType`。

​	例如：

```
from typing import assert_type, final, TypeIs

class Parent: pass
class Child(Parent): pass
@final
class Unrelated: pass

def is_parent(val: object) -> TypeIs[Parent]:
    return isinstance(val, Parent)

def run(arg: Child | Unrelated):
    if is_parent(arg):
        # ``arg`` 的类型缩小为 ``Parent`` 和 ```Child`` 的交集，
        # 相当于 ``Child``.
        assert_type(arg, Child)
    else:
        # ``arg`` 的类型缩小为 ``Parent`` 除外，所以仅剩 ``Unrelated``。
        assert_type(arg, Unrelated)
```

`TypeIs` 内的类型必须与函数参数类型契合，否则静态类型检查器会引发错误。编写不正确的 `TypeIs` 可能导致类型系统中出现不健全行为，以类型安全的方式编写这些函数是用户的责任。

​	如果 `TypeIs` 函数是一个类或实例方法，那么 `TypeIs` 中的类型将映射到（在 `cls` 或 `self` 之后）第二个形参的类型。

​	简单来说，`def foo(arg: TypeA) -> TypeIs[TypeB]: ...` 意味着如果 `foo(arg)` 返回 `True`，那么 `arg` 就是 `TypeB` 的实例，如果返回 `False`，它就不是 `TypeB`.的实例。

`TypeIs` 同样可作用于类型变量，详见 [**PEP 742**](https://peps.python.org/pep-0742/) (使用 `TypeIs` 收窄类型) 。

> Added in version 3.13.
>

## typing.**TypeGuard**

​	特殊类型注解构造，用于标记用户定义的谓词函数。

​	类型谓词函数是由用户定义的函数，它的返回值指示参数是否为某个特定类型的实例。`TypeGuard` 和 [`TypeIs`]({{< ref "/library/development/typing#typing.TypeIs" >}}) 用法相近，但是对类型检查行为有不同的影响（如下）。

`-> TypeGuard` 告诉静态类型检查器，某函数：

1. 返回一个布尔值。
2. 如果返回值是 `True`，那么其参数的类型是 `TypeGuard` 内的类型。

`TypeGuard` 也适用于类型变量。 详情参见 [**PEP 647**](https://peps.python.org/pep-0647/)。

​	例如：

```
def is_str_list(val: list[object]) -> TypeGuard[list[str]]:
    '''Determines whether all objects in the list are strings'''
    return all(isinstance(x, str) for x in val)

def func1(val: list[object]):
    if is_str_list(val):
        # ``val`` 的类型缩小为 ``list[str]``.
        print(" ".join(val))
    else:
        # ``val`` 的类型仍为 ``list[object]``.
        print("Not a list of strings!")
```

`TypeIs` 和 `TypeGuard` 有以下不同：

- `TypeIs` 要求收窄的类型是输入类型的子类型，但 `TypeGuard` 不要求。这主要是为了允许将 `list[object]` 缩小为 `list[str]`，即使后者不是前者的子类型，因为 `list` 是不变的。
- 当 `TypeGuard` 函数返回 `True` 时，类型检查器会将变量的类型精确地收窄到 `TypeGuard` 类型。当 `TypeIs` 函数返回 `True` 时，类型检查程序可以结合先前已知的变量类型和 `TypeIs` 类型推断出更精确的类型。（从技术上讲，这叫做交类型。）
- 当 `TypeGuard` 函数返回 `False` 时，类型检查器不会收窄变量的类型范围。当 `TypeIs` 函数返回 `False` 时，类型检查器可以收窄变量的类型范围至排除 `TypeIs` 类型。

> Added in version 3.10.
>

## typing.**Unpack**

​	在概念上将对象标记为已解包的类型运算符。

​	例如，在一个 [类型变量元组]({{< ref "/library/development/typing#typevartuple" >}}) 上使用解包运算符 `*` 就等价于使用 `Unpack` 来将该类型变量元组标记为已被解包:

```
Ts = TypeVarTuple('Ts')
tup: tuple[*Ts]
# 实际所做的：
tup: tuple[Unpack[Ts]]
```

​	实际上，`Unpack` 在 [`typing.TypeVarTuple`]({{< ref "/library/development/typing#typing.TypeVarTuple" >}}) 和 [`builtins.tuple`]({{< ref "/library/stdtypes#tuple" >}}) 类型的上下文中可以和 `*` 互换使用。 你可能会看到 `Unpack` 在较旧版本的 Python 中被显式地使用，这时 `*` 在特定场合则是无法使用的:

```
# 在旧版本的 Python 中，TypeVarTuple 和 Unpack 位于
# `typing_extensions` 反向移植包中。
from typing_extensions import TypeVarTuple, Unpack

Ts = TypeVarTuple('Ts')
tup: tuple[*Ts]         # Python <= 3.10 时的语法错误！
tup: tuple[Unpack[Ts]]  # 语义等效且向后兼容
```

`Unpack` 也可以与 [`typing.TypedDict`]({{< ref "/library/development/typing#typing.TypedDict" >}}) 一起使用以便在函数签名中对 `**kwargs` 进行类型标注:

```
from typing import TypedDict, Unpack

class Movie(TypedDict):
    name: str
    year: int

# 此函数需要两个关键字参数 -
# 类型为 `str` 的 `name` 和类型为 `int` 的 `year`。
def foo(**kwargs: Unpack[Movie]): ...
```

​	请参阅 [**PEP 692**](https://peps.python.org/pep-0692/) 了解将 `Unpack` 用于 `**kwargs` 类型标注的更多细节。

> Added in version 3.11.
>

#### 构造泛型类型与类型别名

​	下列类不应被直接用作标注。 它们的设计目标是作为创建泛型类型和类型别名的构件。

​	这些对象可通过特殊语法 ([类型形参列表]({{< ref "/reference/compound_stmts#type-params" >}}) 和 [`type`]({{< ref "/reference/simple_stmts#type" >}}) 语句) 来创建。 为了与 Python 3.11 及更早版本的兼容性，它们也可不用专门的语法来创建，如下文所述。

## *class* typing.**Generic**

​	用于泛型类型的抽象基类。

​	泛型类型通常是通过在类名后添加一个类型形参列表来声明的:

```
class Mapping[KT, VT]:
    def __getitem__(self, key: KT) -> VT:
        ...
        # 其他
```

​	这样的类将隐式地继承自 `Generic`。 对于该语法的运行语义的讨论参见 [语言参考]({{< ref "/reference/compound_stmts#generic-classes" >}})。

​	该类的用法如下：

```
def lookup_name[X, Y](mapping: Mapping[X, Y], key: X, default: Y) -> Y:
    try:
        return mapping[key]
    except KeyError:
        return default
```

​	此处函数名之后的圆括号是表示 [泛型函数]({{< ref "/reference/compound_stmts#generic-functions" >}})。

​	为了保持向下兼容性，泛型类也可通过显式地继承自 `Generic` 来声明。 在此情况下，类型形参必须单独声明:

```
KT = TypeVar('KT')
VT = TypeVar('VT')

class Mapping(Generic[KT, VT]):
    def __getitem__(self, key: KT) -> VT:
        ...
        # 其他
```

## *class* typing.**TypeVar**(*name*, **constraints*, *bound=None*, *covariant=False*, *contravariant=False*, *infer_variance=False*, *default=typing.NoDefault*)

​	类型变量。

​	构造类型变量的推荐方式是使用针对 [泛型函数]({{< ref "/reference/compound_stmts#generic-functions" >}}), [泛型类]({{< ref "/reference/compound_stmts#generic-classes" >}}) 和 [泛型类型别名]({{< ref "/reference/compound_stmts#generic-type-aliases" >}}) 的专门语法:

```
class Sequence[T]:  # T 是一个 TypeVar
    ...
```

​	This syntax can also be used to create bounded and constrained type variables:

```
class StrSequence[S: str]:  # S is a TypeVar with a `str` upper bound;
    ...                     # we can say that S is "bounded by `str`"


class StrOrBytesSequence[A: (str, bytes)]:  # A is a TypeVar constrained to str or bytes
    ...
```

​	不过，如有需要，也可通过手动方式来构造可重用的类型变量，就像这样:

```
T = TypeVar('T')  # 可以是任意类型
S = TypeVar('S', bound=str)  # 可以是任意 str 的子类型
A = TypeVar('A', str, bytes)  # 必须是 str 或 bytes
```

​	类型变量的主要用处是为静态类型检查器提供支持。 它们可作为泛型类型以及泛型函数和类型别名定义的形参。 请参阅 [`Generic`]({{< ref "/library/development/typing#typing.Generic" >}}) 了解有关泛型类型的更多信息。 泛型函数的作用方式如下:

```
def repeat[T](x: T, n: int) -> Sequence[T]:
    """Return a list containing n references to x."""
    return [x]*n


def print_capitalized[S: str](x: S) -> S:
    """Print x capitalized, and return x."""
    print(x.capitalize())
    return x


def concatenate[A: (str, bytes)](x: A, y: A) -> A:
    """Add two strings or bytes objects together."""
    return x + y
```

​	Note that type variables can be *bounded*, *constrained*, or neither, but cannot be both bounded *and* constrained.

​	类型变量的种类是在其通过 [类型形参语法]({{< ref "/reference/compound_stmts#type-params" >}}) 创建时或是在传入 `infer_variance=True` 时由类型检查器推断得到的。 手动创建的类型变量可通过传入 `covariant=True` 或 `contravariant=True` 被显式地标记为协变或逆变。 在默认情况下，手动创建的类型变量为不变。 请参阅 [**PEP 484**](https://peps.python.org/pep-0484/) 和 [**PEP 695**](https://peps.python.org/pep-0695/) 了解更多细节。

​	Bounded type variables and constrained type variables have different semantics in several important ways. Using a *bounded* type variable means that the `TypeVar` will be solved using the most specific type possible:

```
x = print_capitalized('a string')
reveal_type(x)  # 显示的类型是 str

class StringSubclass(str):
    pass

y = print_capitalized(StringSubclass('another string'))
reveal_type(y)  # 显示的类型是 StringSubclass

z = print_capitalized(45)  # 错误：int 不是 str 的子类型
```

​	The upper bound of a type variable can be a concrete type, abstract type (ABC or Protocol), or even a union of types:

```
# 可以是任何具有 __abs__ 方法的内容
def print_abs[T: SupportsAbs](arg: T) -> None:
    print("Absolute value:", abs(arg))

U = TypeVar('U', bound=str|bytes)  # 可以是联合 str|bytes 的任何子类型
V = TypeVar('V', bound=SupportsAbs)  # 可以是任何具有 __abs__ 方法的内容
```

​	但是，如果使用 *约束* 类型变量，则意味着 `TypeVar` 只能被解析为恰好是给定的约束之一:

```
a = concatenate('one', 'two')
reveal_type(a)  # 揭示的类型为 str

b = concatenate(StringSubclass('one'), StringSubclass('two'))
reveal_type(b)  # 揭示的类型为 str，虽然传入的是 StringSubclass

c = concatenate('one', b'two')  # 错误：在一个函数调用中类型变量 'A' 可以为 str 或 bytes，但不可同时使用
```

​	在运行时，`isinstance(x, T)` 将引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。

## **__name__**

​	类型变量的名称。

## **__covariant__**

​	类型变量是否已被显式地标记为 covariant。

## **__contravariant__**

​	类型变量是否已被显式地标记为 contravariant。

## **__infer_variance__**

​	类型变量的种类是否应由类型检查器来推断。

> Added in version 3.12.
>

## **__bound__**

​	The upper bound of the type variable, if any.

*在 3.12 版本发生变更:* 对于通过 [类型形参语法]({{< ref "/reference/compound_stmts#type-params" >}}) 创建的类型变量，只有在属性被访问的时候才会对绑定求值，而不是在类型变量被创建的时候 (参见 [惰性求值]({{< ref "/reference/executionmodel#lazy-evaluation" >}}))。

## **__constraints__**

​	一个包含对类型变量的约束的元组，如果有的话。A tuple containing the constraints of the type variable, if any.

*在 3.12 版本发生变更:* 对于通过 [类型形参语法]({{< ref "/reference/compound_stmts#type-params" >}}) 创建的类型变量，只有在属性被访问的时候才会对约束求值，而不是在类型变量被创建的时候 (参见 [惰性求值]({{< ref "/reference/executionmodel#lazy-evaluation" >}}))。

## **__default__**

​	类型变量的默认值，如果没有默认值，则为 [`typing.NoDefault`]({{< ref "/library/development/typing#typing.NoDefault" >}})。

> Added in version 3.13.
>

## **has_default**()

​	返回类型变量是否有默认值。它等价于检查 [`__default__`]({{< ref "/library/development/typing#typing.TypeVar.__default__" >}}) 是否为 [`typing.NoDefault`]({{< ref "/library/development/typing#typing.NoDefault" >}}) 单例，但它不要求对 [惰性求值]({{< ref "/reference/executionmodel#lazy-evaluation" >}}) 的默认值求值。

> Added in version 3.13.
>

*在 3.12 版本发生变更:* 类型变量现在可以通过使用 [**PEP 695**](https://peps.python.org/pep-0695/) 引入的 [类型形参]({{< ref "/reference/compound_stmts#type-params" >}}) 语法来声明。 增加了 `infer_variance` 形参。

*在 3.13 版本发生变更:* 增加了对默认值的支持。

## *class* typing.**TypeVarTuple**(*name*, ***, *default=typing.NoDefault*)

​	类型变量元组。 一种启用了 *variadic* 泛型的专属 [类型变量]({{< ref "/library/development/typing#typevar" >}}) 形式。

​	类型变量元组可以通过在 [类型形参列表]({{< ref "/reference/compound_stmts#type-params" >}}) 中使用名称前的单个星号 (`*`) 来声明:

```
def move_first_element_to_last[T, *Ts](tup: tuple[T, *Ts]) -> tuple[*Ts, T]:
    return (*tup[1:], tup[0])
```

​	或者通过显式地发起调用 `TypeVarTuple` 构造器:

```
T = TypeVar("T")
Ts = TypeVarTuple("Ts")

def move_first_element_to_last(tup: tuple[T, *Ts]) -> tuple[*Ts, T]:
    return (*tup[1:], tup[0])
```

​	一个普通类型变量将启用单个类型的形参化。 作为对比，一个类型变量元组通过将 *任意* 数量的类型变量封包在一个元组中来允许 *任意* 数量类型的形参化。 例如:

```
# T 绑定到 int，Ts 绑定到 ()
# 返回值为 (1,)，其类型为 tuple[int]
move_first_element_to_last(tup=(1,))

# T 绑定到 int，Ts 绑定到 (str,)
# 返回值为 ('spam', 1)，其类型为 tuple[str, int]
move_first_element_to_last(tup=(1, 'spam'))

# T 绑定到 int，Ts 绑定到 (str, float)
# 返回值为 ('spam', 3.0, 1)，其类型为 tuple[str, float, int]
move_first_element_to_last(tup=(1, 'spam', 3.0))

# 这不能通过类型检查（并会在运行时执行失败）
# 因为 tuple[()] 与 tuple[T, *Ts] 不兼容
# （至少需要有一个元素）
move_first_element_to_last(tup=())
```

​	请注意解包运算符 `*` 在 `tuple[T, *Ts]` 中的使用。 在概念上，你可以将 `Ts` 当作一个由类型变量组成的元组 `(T1, T2, ...)`。 那么 `tuple[T, *Ts]` 就将变为 `tuple[T, *(T1, T2, ...)]`，这等价于 `tuple[T, T1, T2, ...]`。 （请注意在旧版本 Python 中，你可能会看到改用 [`Unpack`]({{< ref "/library/development/typing#typing.Unpack" >}}) 的写法，如 `Unpack[Ts]`。）

​	类型变量元组 *总是* 要被解包。 这有助于区分类型变量元组和普通类型变量:

```
x: Ts          # 不可用
x: tuple[Ts]   # 不可用
x: tuple[*Ts]  # 正确的做法
```

​	类型变量元组可被用在与普通类型变量相同的上下文中。 例如，在类定义、参数和返回类型中:

```
class Array[*Shape]:
    def __getitem__(self, key: tuple[*Shape]) -> float: ...
    def __abs__(self) -> "Array[*Shape]": ...
    def get_shape(self) -> tuple[*Shape]: ...
```

​	类型变量元组可以很好地与普通类型变量结合在一起：

```
class Array[DType, *Shape]:  # 这样可以
    pass

class Array2[*Shape, DType]:  # 这样也可以
    pass

class Height: ...
class Width: ...

float_array_1d: Array[float, Height] = Array()     # 完全可以
int_array_2d: Array[int, Height, Width] = Array()  # 是的，同样可以
```

​	但是，请注意在一个类型参数或类型形参列表中最多只能有一个类型变量元组:

```
x: tuple[*Ts, *Ts]            # 不可用
class Array[*Shape, *Shape]:  # 不可用
    pass
```

​	最后，一个已解包的类型变量元组可以被用作 `*args` 的类型标注:

```
def call_soon[*Ts](
    callback: Callable[[*Ts], None],
    *args: *Ts
) -> None:
    ...
    callback(*args)
```

​	相比非解包的 `*args` 标注 —— 例如 `*args: int`，它将指明 *所有* 参数均为 `int` —— `*args: *Ts` 启用了对 `*args` 中 *单个* 参数的类型的引用。 在此，这允许我们确保传入 `call_soon` 的 `*args` 的类型与 `callback` 的（位置）参数的类型相匹配。

​	关于类型变量元组的更多细节，请参见 [**PEP 646**](https://peps.python.org/pep-0646/)。

## **__name__**

​	类型变量元组的名称。

## **__default__**

​	类型变量元组的默认值，如果没有默认值，则为 [`typing.NoDefault`]({{< ref "/library/development/typing#typing.NoDefault" >}})。

> Added in version 3.13.
>

## **has_default**()

​	返回类型变量元组是否有默认值。它等价于检查 [`__default__`]({{< ref "/library/development/typing#typing.TypeVarTuple.__default__" >}}) 是否为 [`typing.NoDefault`]({{< ref "/library/development/typing#typing.NoDefault" >}}) 单例，但它不要求对 [惰性求值]({{< ref "/reference/executionmodel#lazy-evaluation" >}}) 的默认值求值。

> Added in version 3.13.
>

> Added in version 3.11.
>

*在 3.12 版本发生变更:* 类型变量元组现在可以使用 [**PEP 695**](https://peps.python.org/pep-0695/) 所引入的 [类型形参]({{< ref "/reference/compound_stmts#type-params" >}}) 语法来声明。

*在 3.13 版本发生变更:* 增加了对默认值的支持。

## *class* typing.**ParamSpec**(*name*, ***, *bound=None*, *covariant=False*, *contravariant=False*, *default=typing.NoDefault*)

​	形参专属变量。 [类型变量]({{< ref "/library/development/typing#typevar" >}}) 的一个专用版本。

​	In [类型形参列表]({{< ref "/reference/compound_stmts#type-params" >}})，形参规格可以使用两个星号 (`**`) 来声明:

```
type IntFunc[**P] = Callable[P, int]
```

​	为了保持与 Python 3.11 及更早版本的兼容性，`ParamSpec` 对象也可以这样创建:

```
P = ParamSpec('P')
```

​	参数规范变量的存在主要是为了使静态类型检查器受益。 它们被用来将一个可调用对象的参数类型转发给另一个可调用对象的参数类型——这种模式通常出现在高阶函数和装饰器中。 它们只有在 `Concatenate` 中使用时才有效，或者作为 `Callable` 的第一个参数，或者作为用户定义的泛型的参数。 参见 [`Generic`]({{< ref "/library/development/typing#typing.Generic" >}}) 以了解更多关于泛型的信息。

​	例如，为了给一个函数添加基本的日志记录，我们可以创建一个装饰器 `add_logging` 来记录函数调用。 参数规范变量告诉类型检查器，传入装饰器的可调用对象和由其返回的新可调用对象有相互依赖的类型参数:

```
from collections.abc import Callable
import logging

def add_logging[T, **P](f: Callable[P, T]) -> Callable[P, T]:
    '''A type-safe decorator to add logging to a function.'''
    def inner(*args: P.args, **kwargs: P.kwargs) -> T:
        logging.info(f'{f.__name__} was called')
        return f(*args, **kwargs)
    return inner

@add_logging
def add_two(x: float, y: float) -> float:
    '''Add two numbers together.'''
    return x + y
```

​	Without `ParamSpec`, the simplest way to annotate this previously was to use a [`TypeVar`]({{< ref "/library/development/typing#typing.TypeVar" >}}) with upper bound `Callable[..., Any]`. However this causes two problems:

1. 类型检查器不能对 `inner` 函数进行类型检查，因为 `*args` 和 `**kwargs` 的类型必须是 [`Any`]({{< ref "/library/development/typing#typing.Any" >}})。
2. [`cast()`]({{< ref "/library/development/typing#typing.cast" >}}) 在返回 `inner` 函数时，可能需要在 `add_logging` 装饰器的主体中进行，或者必须告诉静态类型检查器忽略 `return inner`。

## **args**

## **kwargs**

​	由于 `ParamSpec` 同时捕获了位置参数和关键字参数，`P.args` 和 `P.kwargs` 可以用来将 `ParamSpec` 分割成其组成部分。 `P.args` 代表给定调用中的位置参数的元组，只能用于注释 `*args`。 `P.kwargs` 代表给定调用中的关键字参数到其值的映射，只能用于注释 `**kwargs`。在运行时，`P.args` 和 `P.kwargs` 分别是 [`ParamSpecArgs`]({{< ref "/library/development/typing#typing.ParamSpecArgs" >}}) 和 [`ParamSpecKwargs`]({{< ref "/library/development/typing#typing.ParamSpecKwargs" >}}) 的实例。

## **__name__**

​	形参规格的名称。

## **__default__**

​	形参规格的默认值，如果没有默认值，则为 [`typing.NoDefault`]({{< ref "/library/development/typing#typing.NoDefault" >}})。

> Added in version 3.13.
>

## **has_default**()

​	返回形参规格是否有默认值。它等价于检查 [`__default__`]({{< ref "/library/development/typing#typing.ParamSpec.__default__" >}}) 是否为 [`typing.NoDefault`]({{< ref "/library/development/typing#typing.NoDefault" >}}) 单例，但它不要求对 [惰性求值]({{< ref "/reference/executionmodel#lazy-evaluation" >}}) 的默认值求值。

> Added in version 3.13.
>

​	用 `covariant=True` 或 `contravariant=True` 创建的参数规范变量可以用来声明协变或逆变泛型类型。 参数 `bound` 也被接受，类似于 [`TypeVar`]({{< ref "/library/development/typing#typing.TypeVar" >}})。 然而这些关键字的实际语义还有待决定。

> Added in version 3.10.
>

*在 3.12 版本发生变更:* 形参说明现在可以使用 [**PEP 695**](https://peps.python.org/pep-0695/) 所引入的 [类型形参]({{< ref "/reference/compound_stmts#type-params" >}}) 语法来声明。

*在 3.13 版本发生变更:* 增加了对默认值的支持。

​	备注

 

​	只有在全局范围内定义的参数规范变量可以被 pickle。

​	参见

- [**PEP 612**](https://peps.python.org/pep-0612/) -- 参数规范变量（引入 `ParamSpec` 和 `Concatenate` 的 PEP）
- [`Concatenate`]({{< ref "/library/development/typing#typing.Concatenate" >}})
- [标注可调用对象]({{< ref "/library/development/typing#annotating-callables" >}})

## typing.**ParamSpecArgs**

## typing.**ParamSpecKwargs**

`ParamSpec`的参数和关键字参数属性。``ParamSpec`` 的 `P.args` 属性是 `ParamSpecArgs` 的一个实例，`P.kwargs` 是 `ParamSpecKwargs` 的一个实例。 它们的目的是用于运行时内部检查的，对静态类型检查器没有特殊意义。

​	在这些对象中的任何一个上调用 [`get_origin()`]({{< ref "/library/development/typing#typing.get_origin" >}}) 将返回原始的 `ParamSpec`:



``` python
>>> from typing import ParamSpec, get_origin
>>> P = ParamSpec("P")
>>> get_origin(P.args) is P
True
>>> get_origin(P.kwargs) is P
True
```

> Added in version 3.10.
>

## *class* typing.**TypeAliasType**(*name*, *value*, ***, *type_params=()*)

​	通过 [`type`]({{< ref "/reference/simple_stmts#type" >}}) 语句创建的类型别名的类型。

​	示例:



``` python
>>> type Alias = int
>>> type(Alias)
<class 'typing.TypeAliasType'>
```

> Added in version 3.12.
>

## **__name__**

​	类型别名的名称：



``` python
>>> type Alias = int
>>> Alias.__name__
'Alias'
```

## **__module__**

​	类型别名定义所在的模块名称:



``` python
>>> type Alias = int
>>> Alias.__module__
'__main__'
```

## **__type_params__**

​	类型别名的类型形参，或者如果别名不属于泛型则为一个空元组:



``` python
>>> type ListOrSet[T] = list[T] | set[T]
>>> ListOrSet.__type_params__
(T,)
>>> type NotGeneric = int
>>> NotGeneric.__type_params__
()
```

## **__value__**

​	类型别名的值。 它将被 [惰性求值]({{< ref "/reference/executionmodel#lazy-evaluation" >}})，因此别名定义中使用的名称将直到 `__value__` 属性被访问时才会被解析:



``` python
>>> type Mutually = Recursive
>>> type Recursive = Mutually
>>> Mutually
Mutually
>>> Recursive
Recursive
>>> Mutually.__value__
Recursive
>>> Recursive.__value__
Mutually
```

#### 其他特殊指令

​	这些函数和类不应被直接用作标注。 它们的设计目标是作为创建和声明类型的构件。

## *class* typing.**NamedTuple**

[`collections.namedtuple()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.namedtuple) 的类型版本。

​	用法：

```
class Employee(NamedTuple):
    name: str
    id: int
```

​	这相当于：

```
Employee = collections.namedtuple('Employee', ['name', 'id'])
```

​	为字段提供默认值，要在类体内赋值：

```
class Employee(NamedTuple):
    name: str
    id: int = 3

employee = Employee('Guido')
assert employee.id == 3
```

​	带默认值的字段必须在不带默认值的字段后面。

​	由此产生的类有一个额外的属性 `__annotations__` ，给出一个 dict ，将字段名映射到字段类型。（字段名在 `_fields` 属性中，默认值在 `_field_defaults` 属性中，这两者都是 [`namedtuple()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.namedtuple) API 的一部分。）

`NamedTuple` 子类也支持文档字符串与方法：

```
class Employee(NamedTuple):
    """代表一位雇员。"""
    name: str
    id: int = 3

    def __repr__(self) -> str:
        return f'<Employee {self.name}, id={self.id}>'
```

`NamedTuple` 子类也可以为泛型：

```
class Group[T](NamedTuple):
    key: T
    group: list[T]
```

​	反向兼容用法：

```
# 在 Python 3.11 上创建通用 NamedTuple
T = TypeVar("T")

class Group(NamedTuple, Generic[T]):
    key: T
    group: list[T]

# 函数语法也支持
Employee = NamedTuple('Employee', [('name', str), ('id', int)])
```

*在 3.6 版本发生变更:* 添加了对 [**PEP 526**](https://peps.python.org/pep-0526/) 中变量注解句法的支持。

*在 3.6.1 版本发生变更:* 添加了对默认值、方法、文档字符串的支持。

*在 3.8 版本发生变更:* `_field_types` 和 `__annotations__` 属性现已使用常规字典，不再使用 `OrderedDict` 实例。

*在 3.9 版本发生变更:* 移除了 `_field_types` 属性， 改用具有相同信息，但更标准的 `__annotations__` 属性。

*在 3.11 版本发生变更:* 添加对泛型命名元组的支持。

*Deprecated since version 3.13, will be removed in version 3.15:* 创建命名元组 NamedTuple 的关键字参数语法 (`NT = NamedTuple("NT", x=int)`) 未被写入文档且已被弃用，它将在 3.15 中被禁止。使用基于类的语法或函数式语法作为替代。

*Deprecated since version 3.13, will be removed in version 3.15:* 使用函数式语法创建 NamedTuple 类时，不向 'fields' 形参传值(`NT = NamedTuple("NT")`) 或向 'fields' 形参传递 `None` (`NT = NamedTuple("NT", None)`) 的行为已被弃用，且在 Python 3.15 中都将被禁止。要创建一个无字段的 NamedTuple 类，请使用 `class NT(NamedTuple): pass` 或 `NT = NamedTuple("NT", [])`。

## *class* typing.**NewType**(*name*, *tp*)

​	用于创建低开销的 [独有类型]({{< ref "/library/development/typing#distinct" >}}) 的辅助类。

`NewType` 将被类型检查器视为一个独有类型。 但是，在运行时，调用 `NewType` 将原样返回其参数。

​	用法：

```
UserId = NewType('UserId', int)  # 声明 NewType "UserId"
first_user = UserId(1)  # 在运行时 "UserId" 将原样返回参数
```

## **__module__**

​	新类型定义所在的模块。

## **__name__**

​	新类型的名称。

## **__supertype__**

​	新类型所基于的类型。

> Added in version 3.5.2.
>

*在 3.10 版本发生变更:* `NewType` 现在是一个类而不是函数。

## *class* typing.**Protocol**(*Generic*)

​	协议类的基类。

​	协议类是这样定义的:

```
class Proto(Protocol):
    def meth(self) -> int:
        ...
```

​	这些类主要与静态类型检查器搭配使用，用来识别结构子类型（静态鸭子类型），例如：

```
class C:
    def meth(self) -> int:
        return 0

def func(x: Proto) -> int:
    return x.meth()

func(C())  # 通过静态类型检查
```

​	请参阅 [**PEP 544**](https://peps.python.org/pep-0544/) 了解详情。 使用 [`runtime_checkable()`]({{< ref "/library/development/typing#typing.runtime_checkable" >}}) 装饰的协议类（稍后将介绍）可作为只检查给定属性是否存在，而忽略其类型签名的简单的运行时协议。

​	Protocol 类可以是泛型，例如：

```
class GenProto[T](Protocol):
    def meth(self) -> T:
        ...
```

​	在需要兼容 Python 3.11 或更早版本的代码中，可以这样编写泛型协议:

```
T = TypeVar("T")

class GenProto(Protocol[T]):
    def meth(self) -> T:
        ...
```

> Added in version 3.8.
>

## @typing.**runtime_checkable**

​	用于把 Protocol 类标记为运行时协议。

​	该协议可以与 [`isinstance()`]({{< ref "/library/functions#isinstance" >}}) 和 [`issubclass()`]({{< ref "/library/functions#issubclass" >}}) 一起使用。应用于非协议的类时，会触发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。该指令支持简易结构检查，与 [`collections.abc`]({{< ref "/library/datatypes/collections_abc#module-collections.abc" >}}) 的 [`Iterable`]({{< ref "/library/datatypes/collections_abc#collections.abc.Iterable" >}}) 非常类似，只擅长做一件事。 例如：

```
@runtime_checkable
class Closable(Protocol):
    def close(self): ...

assert isinstance(open('/some/file'), Closable)

@runtime_checkable
class Named(Protocol):
    name: str

import threading
assert isinstance(threading.Thread(name='Bob'), Named)
```

​	备注

 

`runtime_checkable()` 将只检查所需方法或属性是否存在，而不检查它们的类型签名或类型。 例如，[`ssl.SSLObject`]({{< ref "/library/ipc/ssl#ssl.SSLObject" >}}) 是一个类，因此它通过了针对 [Callable]({{< ref "/library/development/typing#annotating-callables" >}}) 的 [`issubclass()`]({{< ref "/library/functions#issubclass" >}}) 检查。 但是，`ssl.SSLObject.__init__` 方法的存在只是引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}}) 并附带更具信息量的消息，因此它无法调用 (实例化) [`ssl.SSLObject`]({{< ref "/library/ipc/ssl#ssl.SSLObject" >}})。

​	备注

 

​	针对运行时可检查协议的 [`isinstance()`]({{< ref "/library/functions#isinstance" >}}) 检查相比针对非协议类的 `isinstance()` 检查可能会惊人的缓慢。 请考虑在性能敏感的代码中使用替代性写法如 [`hasattr()`]({{< ref "/library/functions#hasattr" >}}) 调用进行结构检查。

> Added in version 3.8.
>

*在 3.12 版本发生变更:* 现在 [`isinstance()`]({{< ref "/library/functions#isinstance" >}}) 的内部实现对于运行时可检查协议的检查会使用 [`inspect.getattr_static()`]({{< ref "/library/python/inspect#inspect.getattr_static" >}}) 来查找属性 (在之前版本中，会使用 [`hasattr()`]({{< ref "/library/functions#hasattr" >}}))。 因此，在 Python 3.12+ 上一些以前被认为是运行时可检查协议的实例的对象可能不再被认为是该协议的实例，反之亦反。 大多数用户不太可能受到这一变化的影响。

*在 3.12 版本发生变更:* 一旦类被创建则运行时可检查协议的成员就会被视为在运行时“已冻结”。 在运行时可检查协议上打上猴子补丁属性仍然有效，但不会影响将对象与协议进行比较的 [`isinstance()`]({{< ref "/library/functions#isinstance" >}}) 检查。 请参阅 ["Python 3.12 有什么新变化](https://docs.python.org/zh-cn/3.13/whatsnew/3.12.html#whatsnew-typing-py312) 了解更多细节。

## *class* typing.**TypedDict**(*dict*)

​	把类型提示添加至字典的特殊构造器。在运行时，它是纯 [`dict`]({{< ref "/library/stdtypes#dict" >}})。

`TypedDict` 声明一个字典类型，该类型预期所有实例都具有一组键集，其中，每个键都与对应类型的值关联。运行时不检查此预期，而是由类型检查器强制执行。用法如下：

```
class Point2D(TypedDict):
    x: int
    y: int
    label: str

a: Point2D = {'x': 1, 'y': 2, 'label': 'good'}  # 可以
b: Point2D = {'z': 3, 'label': 'bad'}           # 不能通过类型检查

assert Point2D(x=1, y=2, label='first') == dict(x=1, y=2, label='first')
```

​	另一种创建 `TypedDict` 的方法是使用函数调用语法。第二个参数必须是一个 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 字面值：

```
Point2D = TypedDict('Point2D', {'x': int, 'y': int, 'label': str})
```

​	这种函数式语法允许定义因为是关键字或包含连字符而不是有效 [标识符]({{< ref "/reference/lexical_analysis#identifiers" >}}) 的键，例如：

```
# 引发 SyntaxError
class Point2D(TypedDict):
    in: int  # 'in' 作为关键字
    x-y: int  # 名称带有连字符

# 功能语法
Point2D = TypedDict('Point2D', {'in': int, 'x-y': int})
```

​	默认情况下，所有的键都必须出现在一个 `TypedDict` 中。 可以使用 [`NotRequired`]({{< ref "/library/development/typing#typing.NotRequired" >}}) 将单独的键标记为非必要的:

```
class Point2D(TypedDict):
    x: int
    y: int
    label: NotRequired[str]

# 替代语法
Point2D = TypedDict('Point2D', {'x': int, 'y': int, 'label': NotRequired[str]})
```

​	这意味着一个 `Point2D` `TypedDict` 可以省略 `label` 键。

​	也可以通过全部指定 `False` 将所有键都标记为默认非必要的:

```
class Point2D(TypedDict, total=False):
    x: int
    y: int

# 替代语法
Point2D = TypedDict('Point2D', {'x': int, 'y': int}, total=False)
```

​	这意味着一个 `Point2D` `TypedDict` 可以省略任何一个键。 类型检查器只需要支持一个字面的 `False` 或 `True` 作为 `total` 参数的值。 `True` 是默认的，它使类主体中定义的所有项目都是必需的。

​	一个 `total=False` `TypedDict` 中单独的键可以使用 [`Required`]({{< ref "/library/development/typing#typing.Required" >}}) 标记为必要的:

```
class Point2D(TypedDict, total=False):
    x: Required[int]
    y: Required[int]
    label: str

# 替代语法
Point2D = TypedDict('Point2D', {
    'x': Required[int],
    'y': Required[int],
    'label': str
}, total=False)
```

​	一个 `TypedDict` 类型有可能使用基于类的语法从一个或多个其他 `TypedDict` 类型继承。用法:

```
class Point3D(Point2D):
    z: int
```

`Point3D` 有三个项目 : `x` , `y` 和 `z` 。 其等价于定义:

```
class Point3D(TypedDict):
    x: int
    y: int
    z: int
```

`TypedDict` 不能从非 `TypedDict` 类继承，除了 [`Generic`]({{< ref "/library/development/typing#typing.Generic" >}})。 例如:

```
class X(TypedDict):
    x: int

class Y(TypedDict):
    y: int

class Z(object): pass  # 非 TypedDict 类

class XY(X, Y): pass  # 可以

class XZ(X, Z): pass  # 引发 TypeError
```

`TypedDict` 也可以为泛型的：

```
class Group[T](TypedDict):
    key: T
    group: list[T]
```

​	要创建与 Python 3.11 或更低版本兼容的泛型 `TypedDict`，请显式地从 [`Generic`]({{< ref "/library/development/typing#typing.Generic" >}}) 继承：

```
T = TypeVar("T")

class Group(TypedDict, Generic[T]):
    key: T
    group: list[T]
```

`TypedDict` 可以通过注解字典（参见 [注解最佳实践]({{< ref "/howto/annotations#annotations-howto" >}}) 了解更多关于注解的最佳实践）、 [`__total__`]({{< ref "/library/development/typing#typing.TypedDict.__total__" >}}) 、 [`__required_keys__`]({{< ref "/library/development/typing#typing.TypedDict.__required_keys__" >}}) 和 [`__optional_keys__`]({{< ref "/library/development/typing#typing.TypedDict.__optional_keys__" >}}) 进行内省。

## **__total__**

`Point2D.__total__` 给出了 `total` 参数的值。 例如：



``` python
>>> from typing import TypedDict
>>> class Point2D(TypedDict): pass
>>> Point2D.__total__
True
>>> class Point2D(TypedDict, total=False): pass
>>> Point2D.__total__
False
>>> class Point3D(Point2D): pass
>>> Point3D.__total__
True
```

​	该属性 *只* 反映传给当前 `TypedDict` 类的 `total` 参数的值，而不反映这个类在语义上是否完整。 例如，一个 `__total__` 被设为 `True` 的 `TypedDict` 可能有用 [`NotRequired`]({{< ref "/library/development/typing#typing.NotRequired" >}}) 标记的键，或者它可能继承自另一个设置了 `total=False` 的 `TypedDict`。 因此，使用 [`__required_keys__`]({{< ref "/library/development/typing#typing.TypedDict.__required_keys__" >}}) 和 [`__optional_keys__`]({{< ref "/library/development/typing#typing.TypedDict.__optional_keys__" >}}) 进行内省通常会更好。

## **__required_keys__**

> Added in version 3.9.
>

## **__optional_keys__**

`Point2D.__required_keys__` 和 `Point2D.__optional_keys__` 返回分别包含必要的和非必要的键的 [`frozenset`]({{< ref "/library/stdtypes#frozenset" >}}) 对象。

​	标记为 [`Required`]({{< ref "/library/development/typing#typing.Required" >}}) 的键总是会出现在 `__required_keys__` 中而标记为 [`NotRequired`]({{< ref "/library/development/typing#typing.NotRequired" >}}) 的键总是会出现在 `__optional_keys__` 中。

​	为了向下兼容 Python 3.10 及更老的版本，还可以使用继承机制在同一个 `TypedDict` 中同时声明必要和非必要的键。 这是通过声明一个具有 `total` 参数值的 `TypedDict` 然后在另一个 `TypedDict` 中继承它并使用不同的 `total` 值来实现的：



``` python
>>> class Point2D(TypedDict, total=False):
...     x: int
...     y: int
...
>>> class Point3D(Point2D):
...     z: int
...
>>> Point3D.__required_keys__ == frozenset({'z'})
True
>>> Point3D.__optional_keys__ == frozenset({'x', 'y'})
True
```

> Added in version 3.9.
>

​	备注

 

​	如果使用了 `from __future__ import annotations` 或者如果以字符串形式给出标注，那么标注不会在定义 `TypedDict` 时被求值。 因此，`__required_keys__` 和 `__optional_keys__` 所依赖的运行时内省可能无法正常工作，这些属性的值也可能不正确。

​	对 [`ReadOnly`]({{< ref "/library/development/typing#typing.ReadOnly" >}}) 的支持反映在下列属性中：

## **__readonly_keys__**

​	一个包含所有只读键名称的 [`frozenset`]({{< ref "/library/stdtypes#frozenset" >}})。 带有 [`ReadOnly`]({{< ref "/library/development/typing#typing.ReadOnly" >}}) 限定符的键被认为是只读的。

> Added in version 3.13.
>

## **__mutable_keys__**

​	一个包含所有可变键名称的 [`frozenset`]({{< ref "/library/stdtypes#frozenset" >}})。 不带有 [`ReadOnly`]({{< ref "/library/development/typing#typing.ReadOnly" >}}) 限定符的键被认为是可变的。

> Added in version 3.13.
>

​	更多示例与 `TypedDict` 的详细规则，详见 [**PEP 589**](https://peps.python.org/pep-0589/)。

> Added in version 3.8.
>

*在 3.11 版本发生变更:* 增加了对将单独的键标记为 [`Required`]({{< ref "/library/development/typing#typing.Required" >}}) 或 [`NotRequired`]({{< ref "/library/development/typing#typing.NotRequired" >}}) 的支持。 参见 [**PEP 655**](https://peps.python.org/pep-0655/)。

*在 3.11 版本发生变更:* 添加对泛型 `TypedDict` 的支持。

*在 3.13 版本发生变更:* 移除了对使用关键字参数方法创建 `TypedDict` 的支持。

*在 3.13 版本发生变更:* 添加了对 [`ReadOnly`]({{< ref "/library/development/typing#typing.ReadOnly" >}}) 限定符的支持。

*Deprecated since version 3.13, will be removed in version 3.15:* 使用函数式语法创建 TypedDict 类时，不向 'fields' 形参传值(`TD = TypedDict("TD")`) 或向 'fields' 形参传递 `None` (`TD = TypedDict ("TD", None)`) 的行为已被弃用，且在 Python 3.15 中都将被禁止。要创建一个无字段的 TypedDict 类，请使用 `class TD(TypedDict ): pass` 或 `TD = TypedDict ("TD", {})`。

### 协议

​	下列协议由 typing 模块提供并已全被装饰为 [`可在运行时检查的`]({{< ref "/library/development/typing#typing.runtime_checkable" >}})。

## *class* typing.**SupportsAbs**

​	一个抽象基类，含一个抽象方法 `__abs__`，该方法与其返回类型协变。

## *class* typing.**SupportsBytes**

​	一个抽象基类，含一个抽象方法 `__bytes__`。

## *class* typing.**SupportsComplex**

​	一个抽象基类，含一个抽象方法 `__complex__`。

## *class* typing.**SupportsFloat**

​	一个抽象基类，含一个抽象方法 `__float__`。

## *class* typing.**SupportsIndex**

​	一个抽象基类，含一个抽象方法 `__index__`。

> Added in version 3.8.
>

## *class* typing.**SupportsInt**

​	一个抽象基类，含一个抽象方法 `__int__`。

## *class* typing.**SupportsRound**

​	一个抽象基类，含一个抽象方法 `__round__`，该方法与其返回类型协变。

### 与 IO 相关的抽象基类

## *class* typing.**IO**

## *class* typing.**TextIO**

## *class* typing.**BinaryIO**

​	泛型 `IO[AnyStr]` 及其子类 `TextIO(IO[str])`、`BinaryIO(IO[bytes])` 表示 I/O 流——例如 [`open()`]({{< ref "/library/functions#open" >}}) 返回的对象——的类型。

### 函数与装饰器

## typing.**cast**(*typ*, *val*)

​	把一个值转换为指定的类型。

​	这会把值原样返回。对类型检查器而言这代表了返回值具有指定的类型，在运行时我们故意没有设计任何检查（我们希望让这尽量快）。

## typing.**assert_type**(*val*, *typ*, */*)

​	让静态类型检查器确认 *val* 具有推断为 *typ* 的类型。

​	在运行时这将不做任何事：它会原样返回第一个参数而没有任何检查或附带影响，无论参数的实际类型是什么。

​	当静态类型检查器遇到对 `assert_type()` 的调用时，如果该值不是指定的类型则会报错:

```
def greet(name: str) -> None:
    assert_type(name, str)  # OK，推断 `name` 的类型是 `str`
    assert_type(name, int)  # 类型检查器错误
```

​	此函数适用于确保类型检查器对脚本的理解符合开发者的意图:

```
def complex_function(arg: object):
    # 执行某些复杂的类型细化逻辑，
    # 在此之后我们希望推断出的类型为 `int`
    ...
    # 测试类型检查器能否正确理解我们的函数
    assert_type(arg, int)
```

> Added in version 3.11.
>

## typing.**assert_never**(*arg*, */*)

​	让静态类型检查器确认一行代码是不可达的。

​	示例：

```
def int_or_str(arg: int | str) -> None:
    match arg:
        case int():
            print("It's an int")
        case str():
            print("It's a str")
        case _ as unreachable:
            assert_never(unreachable)
```

​	在这里，标注允许类型检查器推断最后一种情况永远不会执行，因为 `arg` 要么是 [`int`]({{< ref "/library/functions#int" >}}) 要么是 [`str`]({{< ref "/library/stdtypes#str" >}})，而这两种选项都已被之前的情况覆盖了。

​	如果类型检查器发现对 `assert_never()` 的调用是可达的，它将报告一个错误。 举例来说，如果 `arg` 的类型标注改为 `int | str | float`，则类型检查器将报告一个错误指出 `unreachable` 为 [`float`]({{< ref "/library/functions#float" >}}) 类型。 对于通过类型检查的 `assert_never` 调用，参数传入的推断类型必须为兜底类型 [`Never`]({{< ref "/library/development/typing#typing.Never" >}})，而不能为任何其他类型。

​	在运行时，如果调用此函数将抛出一个异常。

​	参见

 

[Unreachable Code and Exhaustiveness Checking](https://typing.readthedocs.io/en/latest/guides/unreachable.html) 提供了更多关于静态类型穷举检查的信息。

> Added in version 3.11.
>

## typing.**reveal_type**(*obj*, */*)

​	让静态类型检查器显示推测的表达式类型。

​	当静态类型检查器遇到一个对此函数的调用时，它将发出带有所推测参数类型的诊断信息。 例如:

```
x: int = 1
reveal_type(x)  # 揭示的类型为 "builtins.int"
```

​	这在你想要调试你的类型检查器如何处理一段特定代码时很有用处。

​	在运行时，此函数会将其参数类型打印到 [`sys.stderr`]({{< ref "/library/python/sys#sys.stderr" >}}) 并不加修改地返回该参数 (以允许该调用在表达式中使用):

```
x = reveal_type(1)  # 打印 "Runtime type is int"
print(x)  # 打印 "1"
```

​	请注意在运行时类型可能不同于类型静态检查器所推测的类型（明确程度可能更高也可能更低）。

​	大多数类型检查器都能在任何地方支持 `reveal_type()`，即使并未从 `typing` 导入该名称。 不过，从 `typing` 导入该名称将允许你的代码在运行时不会出现运行时错误并能更清晰地传递意图。

> Added in version 3.11.
>

## @typing.**dataclass_transform**(***, *eq_default=True*, *order_default=False*, *kw_only_default=False*, *frozen_default=False*, *field_specifiers=()*, ***kwargs*)

​	将一个对象标记为提供类似 [`dataclass`]({{< ref "/library/python/dataclasses#dataclasses.dataclass" >}}) 行为的装饰器。

`dataclass_transform` 可被用于装饰类、元类或本身为装饰器的函数。 使用 `@dataclass_transform()` 将让静态类型检查器知道被装饰的对象会执行以类似 [`@dataclasses.dataclass`]({{< ref "/library/python/dataclasses#dataclasses.dataclass" >}}) 的方式来转换类的运行时“魔法”。

​	装饰器函数使用方式的例子：

```
@dataclass_transform()
def create_model[T](cls: type[T]) -> type[T]:
    ...
    return cls

@create_model
class CustomerModel:
    id: int
    name: str
```

​	在基类上:

```
@dataclass_transform()
class ModelBase: ...

class CustomerModel(ModelBase):
    id: int
    name: str
```

​	在元类上:

```
@dataclass_transform()
class ModelMeta(type): ...

class ModelBase(metaclass=ModelMeta): ...

class CustomerModel(ModelBase):
    id: int
    name: str
```

​	上面定义的 `CustomerModel` 类将被类型检查器视为类似于使用 [`@dataclasses.dataclass`]({{< ref "/library/python/dataclasses#dataclasses.dataclass" >}}) 创建的类。 例如，类型检查器将假定这些类具有接受 `id` 和 `name` 的 `__init__` 方法。

​	被装饰的类、元类或函数可以接受以下布尔值参数，类型检查器将假定它们具有与 [`@dataclasses.dataclass`]({{< ref "/library/python/dataclasses#dataclasses.dataclass" >}}) 装饰器相同的效果: `init`, `eq`, `order`, `unsafe_hash`, `frozen`, `match_args`, `kw_only` 和 `slots`。 这些参数的值 (`True` 或 `False`) 必须可以被静态地求值。

​	传给 `dataclass_transform` 装饰器的参数可以被用来定制被装饰的类、元类或函数的默认行为:

## 参数:

- **eq_default** ([*bool*]({{< ref "/library/functions#bool" >}})) -- 指明如果调用方省略了 `eq` 形参则应将其假定为 `True` 还是 `False`。 默认为 `True`。
- **order_default** ([*bool*]({{< ref "/library/functions#bool" >}})) -- 指明如果调用方省略了 `order` 形参则应将其假定为 `True` 还是 `False`。 默认为 `False`。
- **kw_only_default** ([*bool*]({{< ref "/library/functions#bool" >}})) -- 指明如果调用方省略了 `kw_only` 形参则应将其假定为 `True` 还是 `False`。 默认为 `False`。
- **frozen_default** ([*bool*]({{< ref "/library/functions#bool" >}})) -- 指明如果调用方省略了 `frozen` 形参则应将其假定为 `True` 还是 `False`。 默认为 `False`。 .. versionadded:: 3.12
- **field_specifiers** ([*tuple*]({{< ref "/library/stdtypes#tuple" >}})*[*[*Callable*]({{< ref "/library/datatypes/collections_abc#collections.abc.Callable" >}})*[**...**,* *Any**]**,* *...**]*) -- 指定一个受支持的类或描述字段的函数的静态列表，类似于 [`dataclasses.field()`]({{< ref "/library/python/dataclasses#dataclasses.field" >}})。 默认为 `()`。
- ***\*kwargs** (*Any*) -- 接受任何其他关键字以便允许可能的未来扩展。

​	类型检查器能识别下列字段设定器的可选形参:

| 形参名称          | 描述                                                         |
| :---------------- | :----------------------------------------------------------- |
| `init`            | 指明字段是否应当被包括在合成的 `__init__` 方法中。 如果未指明，则 `init` 默认为 `True`。 |
| `default`         | 为字段提供默认值。                                           |
| `default_factory` | 提供一个返回字段默认值的运行时回调。 如果 `default` 或 `default_factory` 均未指定，则会假定字段没有默认值而在类被实例化时必须提供一个值。 |
| `factory`         | 字段说明符上 `default_factory` 形参的别名。                  |
| `kw_only`         | 指明字段是否应被标记为仅限关键字的。 如为 `True`，字段将是仅限关键字的。 如为 `False`，它将不是仅限关键字的。 如未指明，则将使用以 `dataclass_transform` 装饰的对象的 `kw_only` 形参的值，或者如果该值也未指明，则将使用 `dataclass_transform` 上 `kw_only_default` 的值。 |
| `alias`           | 提供字段的替代名称。 该替代名称会被用于合成的 `__init__` 方法。 |

​	在运行时，该装饰器会将其参数记录到被装饰对象的 `__dataclass_transform__` 属性。 它没有其他的运行时影响。

​	更多细节请参见 [**PEP 681**](https://peps.python.org/pep-0681/)。

> Added in version 3.11.
>

## @typing.**overload**

​	用于创建重载函数和方法的装饰器。

`@overload` 装饰器允许描述支持多参数类型不同组合的函数和方法。 一系列以 `@overload` 装饰的定义必须带上恰好一个非 `@overload` 装饰的定义（用于同一个函数/方法）。

​	以 `@overload` 装饰的定义仅对类型检查器有用，因为它们将被非 `@overload` 装饰的定义覆盖。 与此同时，非 `@overload` 装饰的定义将在运行时使用但应被类型检查器忽略。 在运行时，直接调用以 `@overload` 装饰的函数将引发 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}})。

​	一个提供了比使用联合或类型变量更精确的类型的重载的示例：

```
@overload
def process(response: None) -> None:
    ...
@overload
def process(response: int) -> tuple[int, str]:
    ...
@overload
def process(response: bytes) -> str:
    ...
def process(response):
    ...  # 以下为真正的实现
```

​	请参阅 [**PEP 484**](https://peps.python.org/pep-0484/) 了解更多细节以及与其他类型语义的比较。

*在 3.11 版本发生变更:* 现在可以使用 [`get_overloads()`]({{< ref "/library/development/typing#typing.get_overloads" >}}) 在运行时内省有重载的函数。

## typing.**get_overloads**(*func*)

​	为 *func* 返回以 [`@overload`]({{< ref "/library/development/typing#typing.overload" >}}) 装饰的定义的序列。

*func* 是用于实现过载函数的函数对象。 例如，根据文档中为 [`@overload`]({{< ref "/library/development/typing#typing.overload" >}}) 给出的 `process` 定义，`get_overloads(process)` 将为所定义的三个过载函数返回由三个函数对象组成的序列。 如果在不带过载的函数上调用，`get_overloads()` 将返回一个空序列。

`get_overloads()` 可被用来在运行时内省一个过载函数。

> Added in version 3.11.
>

## typing.**clear_overloads**()

​	清空内部注册表中所有已注册的重载。

​	这可用于回收注册表所使用的内存。

> Added in version 3.11.
>

## @typing.**final**

​	表示最终化方法和最终化类的装饰器。

​	以 `@final` 装饰一个方法将向类型检查器指明该方法不可在子类中被重载。 以 `@final` 装饰一个类表示它不可被子类化。

​	例如：

```
class Base:
    @final
    def done(self) -> None:
        ...
class Sub(Base):
    def done(self) -> None:  # 类型检查器报告错误
        ...

@final
class Leaf:
    ...
class Other(Leaf):  # 类型检查器报告错误
    ...
```

​	这些属性没有运行时检查。详见 [**PEP 591**](https://peps.python.org/pep-0591/)。

> Added in version 3.8.
>

*在 3.11 版本发生变更:* 该装饰器现在将尝试在被装饰的对象上设置 `__final__` 属性为 `True`。 这样，可以在运行时使用 `if getattr(obj, "__final__", False)` 这样的检查来确定对象 `obj` 是否已被标记为终结。 如果被装饰的对象不支持设置属性，该装饰器将不加修改地返回对象而不会引发异常。

## @typing.**no_type_check**

​	标明注解不是类型提示的装饰器。

​	此作用方式类似于类或函数的 [decorator]({{< ref "/glossary/idx#term-decorator" >}})。 对于类，它将递归地应用到该类中定义的所有方法和类（但不包括在其超类或子类中定义的方法）。 类型检查器将忽略带有此装饰器的函数或类的所有标注。

`@no_type_check` 将原地改变被装饰的对象。

## @typing.**no_type_check_decorator**

​	让其他装饰器具有 [`no_type_check()`]({{< ref "/library/development/typing#typing.no_type_check" >}}) 效果的装饰器。

​	本装饰器用 [`no_type_check()`]({{< ref "/library/development/typing#typing.no_type_check" >}}) 里的装饰函数打包其他装饰器。

*Deprecated since version 3.13, will be removed in version 3.15:* `@no_type_check_decorator` 没有任何类型检查器支持过，所以它被弃用，并将在 Python 3.15 中被移除。

## @typing.**override**

​	该装饰器指明子类中的某个方法是重载超类中的方法或属性。

​	如果一个以 `@override` 装饰的方法实际未重载任何东西则类型检查器应当报告错误。 这有助于防止当基类发生修改而子类未进行相应修改而导致的问题。

​	例如:

```
class Base:
    def log_status(self) -> None:
        ...

class Sub(Base):
    @override
    def log_status(self) -> None:  # 可以：重写 Base.log_status
        ...

    @override
    def done(self) -> None:  # 类型检查器报告错误
        ...
```

​	没有对此特征属性的运行时检查。

​	该装饰器将尝试在被装饰的对象上设置 `__override__` 属性为 `True`。 这样，可以在运行时使用 `if getattr(obj, "__override__", False)` 这样的检查来确定对象 `obj` 是否已被标记为重载。 如果被装饰的对象不支持设置属性，该装饰器将不加修改地返回对象而不会引发异常。

​	更多细节参见 [**PEP 698**](https://peps.python.org/pep-0698/)。

> Added in version 3.12.
>

## @typing.**type_check_only**

​	将类或函数标记为在运行时不可用的装饰器。

​	在运行时，该装饰器本身不可用。实现返回的是私有类实例时，它主要是用于标记在类型存根文件中定义的类。

```
@type_check_only
class Response:  # 私有或在运行时不可用
    code: int
    def get_header(self, name: str) -> str: ...

def fetch_response() -> Response: ...
```

​	注意，建议不要返回私有类实例，最好将之设为公共类。

### 内省辅助器

## typing.**get_type_hints**(*obj*, *globalns=None*, *localns=None*, *include_extras=False*)

​	返回函数、方法、模块、类对象的类型提示的字典。

​	该函数通常与 `obj.__annotations__` 相同，但会对注解字典进行以下更改：

- 以字符串字面形式或 [`ForwardRef`]({{< ref "/library/development/typing#typing.ForwardRef" >}}) 对象编码的前向引用会在 *globalns*, *localns* 和 (如适用) *obj* 的 [类型形参]({{< ref "/reference/compound_stmts#type-params" >}}) 命名空间中求值。如果没有传入 *globalns* 或 *localns*，则会从 *obj* 中推断出适当的命名空间字典。
- `None` 被替换为 [`types.NoneType`]({{< ref "/library/datatypes/types#types.NoneType" >}})。
- 如果在 *obj* 上应用了 [`@no_type_check`]({{< ref "/library/development/typing#typing.no_type_check" >}})，返回一个空字典。
- 如果 *obj* 是一个类 `C`，则函数返回一个合并了 `C` 的基类与 `C` 上的基类的字典。 这是通过遍历 [`C.__mro__`]({{< ref "/reference/datamodel#type.__mro__" >}}) 并迭代地合并 `__annotations__` 字典来实现的。 在 [method resolution order]({{< ref "/glossary/idx#term-method-resolution-order" >}}) 中较早出现的类上的标注总是会优先于在方法解析顺序中较晚出现的类的标注。
- 除非 *include_extras* 设置为 `True`，否则函数会递归地将所有出现的 `Annotated[T, ...]` 替换为 `T` (详见 [`Annotated`]({{< ref "/library/development/typing#typing.Annotated" >}}))。

​	另请参阅 [`inspect.get_annotations()`]({{< ref "/library/python/inspect#inspect.get_annotations" >}})，这是一个以更直接方式返回注解的低级函数。

​	备注

 

​	如果 *obj* 注解中的任何前向引用不可解析或不是有效的 Python 代码，此函数将引发 [`NameError`]({{< ref "/library/exceptions#NameError" >}}) 等异常。例如导入的 [类型别名]({{< ref "/library/development/typing#type-aliases" >}}) 包含正向引用，或名称在 [`if TYPE_CHECKING`]({{< ref "/library/development/typing#typing.TYPE_CHECKING" >}}) 下导入。

*在 3.9 版本发生变更:* 增加了作为 [**PEP 593**](https://peps.python.org/pep-0593/) 组成部分的 `include_extras` 形参。 请参阅 [`Annotated`]({{< ref "/library/development/typing#typing.Annotated" >}}) 文档了解详情。

*在 3.11 版本发生变更:* 在之前，如果设置了等于 `None` 的默认值则会为函数和方法标注添加 `Optional[t]`。 现在标注将被不加修改地返回。

## typing.**get_origin**(*tp*)

​	获取一个类型的不带下标的版本：对于 `X[Y, Z, ...]` 形式的类型对象将返回 `X`。

​	如果 `X` 是一个内置类型或 [`collections`](https://docs.python.org/zh-cn/3.13/library/collections.html#module-collections) 类在 typing 模块中的别名，它将被正规化为原始的类。 如果 `X` 是 [`ParamSpecArgs`]({{< ref "/library/development/typing#typing.ParamSpecArgs" >}}) 或 [`ParamSpecKwargs`]({{< ref "/library/development/typing#typing.ParamSpecKwargs" >}}) 的实例，则返回下层的 [`ParamSpec`]({{< ref "/library/development/typing#typing.ParamSpec" >}})。 对于不受支持的对象将返回 `None`。

​	示例：

```
assert get_origin(str) is None
assert get_origin(Dict[str, int]) is dict
assert get_origin(Union[int, str]) is Union
assert get_origin(Annotated[str, "metadata"]) is Annotated
P = ParamSpec('P')
assert get_origin(P.args) is P
assert get_origin(P.kwargs) is P
```

> Added in version 3.8.
>

## typing.**get_args**(*tp*)

​	获取已执行所有下标的类型参数：对于 `X[Y, Z, ...]` 形式的类型对象将返回 `(Y, Z, ...)`。

​	如果 `X` 是一个并集或是包含在另一个泛型类型中的 [`Literal`]({{< ref "/library/development/typing#typing.Literal" >}})，则 `(Y, Z, ...)` 的顺序可能因类型缓存而与原始参数 `[Y, Z, ...]` 存在差异。 对于不受支持的对象将返回 `()`。

​	示例：

```
assert get_args(int) == ()
assert get_args(Dict[int, str]) == (int, str)
assert get_args(Union[int, str]) == (int, str)
```

> Added in version 3.8.
>

## typing.**get_protocol_members**(*tp*)

​	返回 [`Protocol`]({{< ref "/library/development/typing#typing.Protocol" >}}) 中定义的成员构成的集合。



``` python
>>> from typing import Protocol, get_protocol_members
>>> class P(Protocol):
...     def a(self) -> str: ...
...     b: int
>>> get_protocol_members(P) == frozenset({'a', 'b'})
True
```

​	如果参数不是协议，引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。

> Added in version 3.13.
>

## typing.**is_protocol**(*tp*)

​	检查一个类型是否为 [`Protocol`]({{< ref "/library/development/typing#typing.Protocol" >}})。

​	例如：

```
class P(Protocol):
    def a(self) -> str: ...
    b: int

is_protocol(P)    # => True
is_protocol(int)  # => False
```

> Added in version 3.13.
>

## typing.**is_typeddict**(*tp*)

​	检查一个类型是否为 [`TypedDict`]({{< ref "/library/development/typing#typing.TypedDict" >}})。

​	例如:

```
class Film(TypedDict):
    title: str
    year: int

assert is_typeddict(Film)
assert not is_typeddict(list | str)

# TypedDict 是创建类型化字典的工厂，
# 不是类型化字典
assert not is_typeddict(TypedDict)
```

> Added in version 3.10.
>

## *class* typing.**ForwardRef**

​	用于字符串前向引用的内部类型表示的类。

​	例如，`List["SomeClass"]` 会被隐式转换为 `List[ForwardRef("SomeClass")]`。 `ForwardRef` 不应由用户来实例化，但可以由内省工具使用。

​	备注

 

[**PEP 585**](https://peps.python.org/pep-0585/) 泛型类型例如 `list["SomeClass"]` 将不会被隐式地转换为 `list[ForwardRef("SomeClass")]` 因而将不会自动解析为 `list[SomeClass]`。

> Added in version 3.7.4.
>

## typing.**NoDefault**

​	一个用于指示类型形参没有默认值的哨兵对象。例如：



``` python
>>> T = TypeVar("T")
>>> T.__default__ is typing.NoDefault
True
>>> S = TypeVar("S", default=None)
>>> S.__default__ is None
True
```

> Added in version 3.13.
>

### 常量

## typing.**TYPE_CHECKING**

​	会被第 3 方静态类型检查器假定为 `True` 的特殊常量。 在运行时将为 `False`。

​	用法：

```
if TYPE_CHECKING:
    import expensive_mod

def fun(arg: 'expensive_mod.SomeType') -> None:
    local_var: expensive_mod.AnotherType = other_fun()
```

​	第一个类型注解必须用引号标注，才能把它当作“前向引用”，从而在解释器运行时中隐藏 `expensive_mod` 引用。局部变量的类型注释不会被评估，因此，第二个注解不需要用引号引起来。

​	备注

 

​	若用了 `from __future__ import annotations`，函数定义时则不求值注解，直接把注解以字符串形式存在 `__annotations__` 里。这时毋需为注解打引号（见 [**PEP 563**](https://peps.python.org/pep-0563/)）。

> Added in version 3.5.2.
>



### 一些已被弃用的别名

​	本模块给标准库中已有的类定义了许多别名，这些别名现已不再建议使用。起初 typing 模块包含这些别名是为了支持用 `[]` 来参数化泛型类。然而，在 Python 3.9 中，对应的已有的类也支持了 `[]` (参见 [**PEP 585**](https://peps.python.org/pep-0585/))，因此这些别名了就成了多余的了。

​	这些多余的类型从 Python 3.9 起被弃用。然而，虽然它们可能会在某一时刻被移除，但目前还没有移除它们的计划。因此，解释器目前不会对这些别名发出弃用警告。

​	一旦确定了何时这些别名将被移除，解释器将比正式移除之时提前至少两个版本发出弃用警告 (deprecation warning)。但保证至少在 Python 3.14 之前，这些别名仍会留在 typing 模块中，并且不会引发弃用警告。

​	如果被类型检查器检查的程序旨在运行于 Python 3.9 或更高版本，则鼓励类型检查器标记出这些不建议使用的类型。



#### 内置类型的别名

## *class* typing.**Dict**(*dict, MutableMapping[KT, VT]*)

[`dict`]({{< ref "/library/stdtypes#dict" >}}) 的已弃用的别名。

​	请注意，注释参数时，最好使用抽象的多项集类型，如 [`Mapping`]({{< ref "/library/datatypes/collections_abc#collections.abc.Mapping" >}})，而不是使用 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 或 `typing.Dict`。

*自 3.9 版本弃用:* [`builtins.dict`]({{< ref "/library/stdtypes#dict" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**List**(*list, MutableSequence[T]*)

[`list`]({{< ref "/library/stdtypes#list" >}}) 的已弃用的别名。

​	请注意，注释参数时，最好使用抽象的多项集类型，如 [`Sequence`]({{< ref "/library/datatypes/collections_abc#collections.abc.Sequence" >}}) 或 [`Iterable`]({{< ref "/library/datatypes/collections_abc#collections.abc.Iterable" >}})，而不是使用 [`list`]({{< ref "/library/stdtypes#list" >}}) 或 `typing.List`。

*自 3.9 版本弃用:* [`builtins.list`]({{< ref "/library/stdtypes#list" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**Set**(*set, MutableSet[T]*)

[`builtins.set`]({{< ref "/library/stdtypes#set" >}}) 的已弃用的别名。

​	请注意，注释参数时，最好使用抽象的多项集类型，如 [`collections.abc.Set`]({{< ref "/library/datatypes/collections_abc#collections.abc.Set" >}})，而不是使用 [`set`]({{< ref "/library/stdtypes#set" >}}) 或 [`typing.Set`]({{< ref "/library/development/typing#typing.Set" >}})。

*自 3.9 版本弃用:* [`builtins.set`]({{< ref "/library/stdtypes#set" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**FrozenSet**(*frozenset, AbstractSet[T_co]*)

[`builtins.frozenset`]({{< ref "/library/stdtypes#frozenset" >}}) 的已弃用的别名。

*自 3.9 版本弃用:* [`builtins.frozenset`]({{< ref "/library/stdtypes#frozenset" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## typing.**Tuple**

[`tuple`]({{< ref "/library/stdtypes#tuple" >}}) 的已弃用的别名。

[`tuple`]({{< ref "/library/stdtypes#tuple" >}}) 和 `Tuple` 是类型系统中的特例；更多详细信息请参见 [标注元组]({{< ref "/library/development/typing#annotating-tuples" >}})。

*自 3.9 版本弃用:* [`builtins.tuple`]({{< ref "/library/stdtypes#tuple" >}}) 现在支持下标操作（`[]`）。参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**Type**(*Generic[CT_co]*)

[`type`]({{< ref "/library/functions#type" >}}) 的已弃用的别名。

​	有关在类型注解中使用 [`type`]({{< ref "/library/functions#type" >}}) 或 `typing.Type` 的详细信息，请参阅 [类对象的类型]({{< ref "/library/development/typing#type-of-class-objects" >}}) 。

> Added in version 3.5.2.
>

*自 3.9 版本弃用:* [`builtins.type`]({{< ref "/library/functions#type" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。



#### [`collections`](https://docs.python.org/zh-cn/3.13/library/collections.html#module-collections) 中的类型的别名。

## *class* typing.**DefaultDict**(*collections.defaultdict, MutableMapping[KT, VT]*)

[`collections.defaultdict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.defaultdict) 的已弃用的别名。

> Added in version 3.5.2.
>

*自 3.9 版本弃用:* [`collections.defaultdict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.defaultdict) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**OrderedDict**(*collections.OrderedDict, MutableMapping[KT, VT]*)

[`collections.OrderedDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.OrderedDict) 的已弃用的别名。

> Added in version 3.7.2.
>

*自 3.9 版本弃用:* [`collections.OrderedDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.OrderedDict) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**ChainMap**(*collections.ChainMap, MutableMapping[KT, VT]*)

[`collections.ChainMap`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.ChainMap) 的已弃用的别名。

> Added in version 3.6.1.
>

*自 3.9 版本弃用:* [`collections.ChainMap`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.ChainMap) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**Counter**(*collections.Counter, Dict[T, int]*)

[`collections.Counter`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.Counter) 的已弃用的别名。

> Added in version 3.6.1.
>

*自 3.9 版本弃用:* [`collections.Counter`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.Counter) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**Deque**(*deque, MutableSequence[T]*)

[`collections.deque`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.deque) 的已弃用的别名。

> Added in version 3.6.1.
>

*自 3.9 版本弃用:* [`collections.deque`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.deque) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。



#### 其他具体类型的别名

## *class* typing.**Pattern**

## *class* typing.**Match**

[`re.compile()`]({{< ref "/library/text/re#re.compile" >}}) 和 [`re.match()`]({{< ref "/library/text/re#re.match" >}}) 的返回类型的已弃用的别名。

​	这些类型（与对应的函数）是 [`AnyStr`]({{< ref "/library/development/typing#typing.AnyStr" >}}) 上的泛型。 `Pattern` 可以被特化为 `Pattern[str]` 或 `Pattern[bytes]`；`Match` 可以被特化为 `Match[str]` 或 `Match[bytes]`。

*自 3.9 版本弃用:* [`re`]({{< ref "/library/text/re#module-re" >}}) 模块中的 `Pattern` 与 `Match` 类现已支持 `[]`。详见 [**PEP 585**](https://peps.python.org/pep-0585/) 与 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**Text**

[`str`]({{< ref "/library/stdtypes#str" >}}) 的已弃用的别名。

`Text` 被用来为 Python 2 代码提供向上兼容的路径：在 Python 2 中，`Text` 是 `unicode` 的别名。

​	使用 `Text` 时，值中必须包含 unicode 字符串，以兼容 Python 2 和 Python 3：

```
def add_unicode_checkmark(text: Text) -> Text:
    return text + u' \u2713'
```

> Added in version 3.5.2.
>

*自 3.11 版本弃用:* Python 2 已不再受支持，并且大部分类型检查器也都不再支持 Python 2 代码的类型检查。 目前还没有计划移除该别名，但建议用户使用 [`str`]({{< ref "/library/stdtypes#str" >}}) 来代替 `Text`。



#### [`collections.abc`]({{< ref "/library/datatypes/collections_abc#module-collections.abc" >}}) 中容器 ABC 的别名

## *class* typing.**AbstractSet**(*Collection[T_co]*)

[`collections.abc.Set`]({{< ref "/library/datatypes/collections_abc#collections.abc.Set" >}}) 的已弃用的别名。

*自 3.9 版本弃用:* [`collections.abc.Set`]({{< ref "/library/datatypes/collections_abc#collections.abc.Set" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**ByteString**(*Sequence[int]*)

​	该类型代表了 [`bytes`]({{< ref "/library/stdtypes#bytes" >}})、[`bytearray`]({{< ref "/library/stdtypes#bytearray" >}})、[`memoryview`]({{< ref "/library/stdtypes#memoryview" >}}) 等字节序列类型。

*Deprecated since version 3.9, will be removed in version 3.14:* 首选 [`collections.abc.Buffer`]({{< ref "/library/datatypes/collections_abc#collections.abc.Buffer" >}})，或是 `bytes | bytearray | memoryview` 这样的并集。

## *class* typing.**Collection**(*Sized, Iterable[T_co], Container[T_co]*)

[`collections.abc.Collection`]({{< ref "/library/datatypes/collections_abc#collections.abc.Collection" >}}) 的已弃用的别名。

> Added in version 3.6.
>

*自 3.9 版本弃用:* [`collections.abc.Collection`]({{< ref "/library/datatypes/collections_abc#collections.abc.Collection" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**Container**(*Generic[T_co]*)

[`collections.abc.Container`]({{< ref "/library/datatypes/collections_abc#collections.abc.Container" >}}) 的已弃用的别名。

*自 3.9 版本弃用:* [`collections.abc.Container`]({{< ref "/library/datatypes/collections_abc#collections.abc.Container" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**ItemsView**(*MappingView, AbstractSet[tuple[KT_co, VT_co]]*)

[`collections.abc.ItemsView`]({{< ref "/library/datatypes/collections_abc#collections.abc.ItemsView" >}}) 的已弃用的别名。

*自 3.9 版本弃用:* [`collections.abc.ItemsView`]({{< ref "/library/datatypes/collections_abc#collections.abc.ItemsView" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**KeysView**(*MappingView, AbstractSet[KT_co]*)

[`collections.abc.KeysView`]({{< ref "/library/datatypes/collections_abc#collections.abc.KeysView" >}}) 的已弃用的别名。

*自 3.9 版本弃用:* [`collections.abc.KeysView`]({{< ref "/library/datatypes/collections_abc#collections.abc.KeysView" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**Mapping**(*Collection[KT], Generic[KT, VT_co]*)

[`collections.abc.Mapping`]({{< ref "/library/datatypes/collections_abc#collections.abc.Mapping" >}}) 的已弃用的别名。

*自 3.9 版本弃用:* [`collections.abc.Mapping`]({{< ref "/library/datatypes/collections_abc#collections.abc.Mapping" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**MappingView**(*Sized*)

[`collections.abc.MappingView`]({{< ref "/library/datatypes/collections_abc#collections.abc.MappingView" >}}) 的已弃用的别名。

*自 3.9 版本弃用:* [`collections.abc.MappingView`]({{< ref "/library/datatypes/collections_abc#collections.abc.MappingView" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**MutableMapping**(*Mapping[KT, VT]*)

[`collections.abc.MutableMapping`]({{< ref "/library/datatypes/collections_abc#collections.abc.MutableMapping" >}}) 的已弃用的别名。

*自 3.9 版本弃用:* [`collections.abc.MutableMapping`]({{< ref "/library/datatypes/collections_abc#collections.abc.MutableMapping" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**MutableSequence**(*Sequence[T]*)

[`collections.abc.MutableSequence`]({{< ref "/library/datatypes/collections_abc#collections.abc.MutableSequence" >}}) 的已弃用的别名。

*自 3.9 版本弃用:* [`collections.abc.MutableSequence`]({{< ref "/library/datatypes/collections_abc#collections.abc.MutableSequence" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**MutableSet**(*AbstractSet[T]*)

[`collections.abc.MutableSet`]({{< ref "/library/datatypes/collections_abc#collections.abc.MutableSet" >}}) 的已弃用的别名。

*自 3.9 版本弃用:* [`collections.abc.MutableSet`]({{< ref "/library/datatypes/collections_abc#collections.abc.MutableSet" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**Sequence**(*Reversible[T_co], Collection[T_co]*)

[`collections.abc.Sequence`]({{< ref "/library/datatypes/collections_abc#collections.abc.Sequence" >}}) 的已弃用的别名。

*自 3.9 版本弃用:* [`collections.abc.Sequence`]({{< ref "/library/datatypes/collections_abc#collections.abc.Sequence" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**ValuesView**(*MappingView, Collection[_VT_co]*)

[`collections.abc.ValuesView`]({{< ref "/library/datatypes/collections_abc#collections.abc.ValuesView" >}}) 的已弃用的别名。

*自 3.9 版本弃用:* [`collections.abc.ValuesView`]({{< ref "/library/datatypes/collections_abc#collections.abc.ValuesView" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。



#### [`collections.abc`]({{< ref "/library/datatypes/collections_abc#module-collections.abc" >}}) 中异步 ABC 的别名

## *class* typing.**Coroutine**(*Awaitable[ReturnType], Generic[YieldType, SendType, ReturnType]*)

[`collections.abc.Coroutine`]({{< ref "/library/datatypes/collections_abc#collections.abc.Coroutine" >}}) 的已弃用的别名。

​	有关在注解类型中使用 [`collections.abc.Coroutine`]({{< ref "/library/datatypes/collections_abc#collections.abc.Coroutine" >}}) 和 `typing.Coroutine` 的详细信息，请参见 [标注生成器和协程]({{< ref "/library/development/typing#annotating-generators-and-coroutines" >}})。

> Added in version 3.5.3.
>

*自 3.9 版本弃用:* [`collections.abc.Coroutine`]({{< ref "/library/datatypes/collections_abc#collections.abc.Coroutine" >}}) 现在支持下标操作（`[]`）。参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**AsyncGenerator**(*AsyncIterator[YieldType], Generic[YieldType, SendType]*)

[`collections.abc.AsyncGenerator`]({{< ref "/library/datatypes/collections_abc#collections.abc.AsyncGenerator" >}}) 的已弃用的别名。

​	有关在注解类型中使用 [`collections.abc.AsyncGenerator`]({{< ref "/library/datatypes/collections_abc#collections.abc.AsyncGenerator" >}}) 和 `typing.AsyncGenerator` 的详细信息，请参见 [标注生成器和协程]({{< ref "/library/development/typing#annotating-generators-and-coroutines" >}})。

> Added in version 3.6.1.
>

*自 3.9 版本弃用:* [`collections.abc.AsyncGenerator`]({{< ref "/library/datatypes/collections_abc#collections.abc.AsyncGenerator" >}}) 现在支持下标操作（`[]`）。参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

*在 3.13 版本发生变更:* `SendType` 形参现在有默认值。

## *class* typing.**AsyncIterable**(*Generic[T_co]*)

[`collections.abc.AsyncIterable`]({{< ref "/library/datatypes/collections_abc#collections.abc.AsyncIterable" >}}) 的已弃用的别名。

> Added in version 3.5.2.
>

*自 3.9 版本弃用:* [`collections.abc.AsyncIterable`]({{< ref "/library/datatypes/collections_abc#collections.abc.AsyncIterable" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**AsyncIterator**(*AsyncIterable[T_co]*)

[`collections.abc.AsyncIterator`]({{< ref "/library/datatypes/collections_abc#collections.abc.AsyncIterator" >}}) 的已弃用的别名。

> Added in version 3.5.2.
>

*自 3.9 版本弃用:* [`collections.abc.AsyncIterator`]({{< ref "/library/datatypes/collections_abc#collections.abc.AsyncIterator" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**Awaitable**(*Generic[T_co]*)

[`collections.abc.Awaitable`]({{< ref "/library/datatypes/collections_abc#collections.abc.Awaitable" >}}) 的已弃用的别名。

> Added in version 3.5.2.
>

*自 3.9 版本弃用:* [`collections.abc.Awaitable`]({{< ref "/library/datatypes/collections_abc#collections.abc.Awaitable" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。



#### [`collections.abc`]({{< ref "/library/datatypes/collections_abc#module-collections.abc" >}}) 中其他 ABC 的别名

## *class* typing.**Iterable**(*Generic[T_co]*)

[`collections.abc.Iterable`]({{< ref "/library/datatypes/collections_abc#collections.abc.Iterable" >}}) 的已弃用的别名

*自 3.9 版本弃用:* [`collections.abc.Iterable`]({{< ref "/library/datatypes/collections_abc#collections.abc.Iterable" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**Iterator**(*Iterable[T_co]*)

[`collections.abc.Iterator`]({{< ref "/library/datatypes/collections_abc#collections.abc.Iterator" >}}) 的已弃用的别名。

*自 3.9 版本弃用:* [`collections.abc.Iterator`]({{< ref "/library/datatypes/collections_abc#collections.abc.Iterator" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## typing.**Callable**

[`collections.abc.Callable`]({{< ref "/library/datatypes/collections_abc#collections.abc.Callable" >}}) 的已弃用的别名。

​	有关如何在类型标注中使用 [`collections.abc.Callable`]({{< ref "/library/datatypes/collections_abc#collections.abc.Callable" >}}) 和 `typing.Callable` 的详细信息请参阅 [标注可调用对象]({{< ref "/library/development/typing#annotating-callables" >}})。

*自 3.9 版本弃用:* [`collections.abc.Callable`]({{< ref "/library/datatypes/collections_abc#collections.abc.Callable" >}}) 现在支持下标操作（`[]`）。参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

*在 3.10 版本发生变更:* `Callable` 现在支持 [`ParamSpec`]({{< ref "/library/development/typing#typing.ParamSpec" >}}) 和 [`Concatenate`]({{< ref "/library/development/typing#typing.Concatenate" >}})。 详情见 [**PEP 612**](https://peps.python.org/pep-0612/)。

## *class* typing.**Generator**(*Iterator[YieldType], Generic[YieldType, SendType, ReturnType]*)

[`collections.abc.Generator`]({{< ref "/library/datatypes/collections_abc#collections.abc.Generator" >}}) 的已弃用的别名。

​	有关在注解类型中使用 [`collections.abc.Generator`]({{< ref "/library/datatypes/collections_abc#collections.abc.Generator" >}}) 和 `typing.Generator` 的详细信息，请参见 [标注生成器和协程]({{< ref "/library/development/typing#annotating-generators-and-coroutines" >}})。

*自 3.9 版本弃用:* [`collections.abc.Generator`]({{< ref "/library/datatypes/collections_abc#collections.abc.Generator" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

*在 3.13 版本发生变更:* 添加了发送和返回类型的默认值。

## *class* typing.**Hashable**

[`collections.abc.Hashable`]({{< ref "/library/datatypes/collections_abc#collections.abc.Hashable" >}}) 的已弃用的别名。

*自 3.12 版本弃用:* 请改为直接使用 [`collections.abc.Hashable`]({{< ref "/library/datatypes/collections_abc#collections.abc.Hashable" >}})。

## *class* typing.**Reversible**(*Iterable[T_co]*)

[`collections.abc.Reversible`]({{< ref "/library/datatypes/collections_abc#collections.abc.Reversible" >}}) 的已弃用的别名。

*自 3.9 版本弃用:* [`collections.abc.Reversible`]({{< ref "/library/datatypes/collections_abc#collections.abc.Reversible" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

## *class* typing.**Sized**

[`collections.abc.Sized`]({{< ref "/library/datatypes/collections_abc#collections.abc.Sized" >}}) 的已弃用的别名。

*自 3.12 版本弃用:* 请改为直接使用 [`collections.abc.Sized`]({{< ref "/library/datatypes/collections_abc#collections.abc.Sized" >}})。



#### [`contextlib`]({{< ref "/library/python/contextlib#module-contextlib" >}}) ABC 的别名

## *class* typing.**ContextManager**(*Generic[T_co, ExitT_co]*)

[`contextlib.AbstractContextManager`]({{< ref "/library/python/contextlib#contextlib.AbstractContextManager" >}}) 的已弃用的别名。

​	第一个类型形参 `T_co` 表示 [`__enter__()`]({{< ref "/reference/datamodel#object.__enter__" >}}) 方法返回值的类型。可选的第二个类型形参 `ExitT_co` 默认为 `bool | None`，它表示 [`__exit__()`]({{< ref "/reference/datamodel#object.__exit__" >}}) 方法返回的类型。

> Added in version 3.5.4.
>

*自 3.9 版本弃用:* [`contextlib.AbstractContextManager`]({{< ref "/library/python/contextlib#contextlib.AbstractContextManager" >}}) 现在支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

*在 3.13 版本发生变更:* 添加了可选的第二个类型形参，`ExitT_co`。

## *class* typing.**AsyncContextManager**(*Generic[T_co, AExitT_co]*)

[`contextlib.AbstractAsyncContextManager`]({{< ref "/library/python/contextlib#contextlib.AbstractAsyncContextManager" >}}) 的已弃用的别名。

​	第一个类型形参 `T_co` 表示 [`__aenter__()`]({{< ref "/reference/datamodel#object.__aenter__" >}}) 方法返回值的类型。可选的第二个类型形参 `AExitT_co` 默认为 `bool | None`，它表示 [`__aexit__()`]({{< ref "/reference/datamodel#object.__aexit__" >}}) 方法返回的类型。

> Added in version 3.6.2.
>

*自 3.9 版本弃用:* [`contextlib.AbstractAsyncContextManager`]({{< ref "/library/python/contextlib#contextlib.AbstractAsyncContextManager" >}}) 现在 支持下标操作 (`[]`)。 参见 [**PEP 585**](https://peps.python.org/pep-0585/) 和 [GenericAlias 类型]({{< ref "/library/stdtypes#types-genericalias" >}})。

*在 3.13 版本发生变更:* 添加了可选的第二个类型形参，`AExitT_co`。

## 主要特性的弃用时间线

`typing` 的某些特性被弃用，并且可能在将来的 Python 版本中被移除。下表总结了主要的弃用特性。该表可能会被更改，而且并没有列出所有的弃用特性。

| 特性                                                         | 弃用于 | 计划移除                                                     | PEP/问题                                                     |
| :----------------------------------------------------------- | :----- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| 标准容器的 `typing` 版本                                     | 3.9    | 未定（请参阅 [一些已被弃用的别名]({{< ref "/library/development/typing#deprecated-aliases" >}}) 了解详情） | [**PEP 585**](https://peps.python.org/pep-0585/)             |
| [`typing.ByteString`]({{< ref "/library/development/typing#typing.ByteString" >}}) | 3.9    | 3.14                                                         | [gh-91896](https://github.com/python/cpython/issues/91896)   |
| [`typing.Text`]({{< ref "/library/development/typing#typing.Text" >}}) | 3.11   | 未确定                                                       | [gh-92332](https://github.com/python/cpython/issues/92332)   |
| [`typing.Hashable`]({{< ref "/library/development/typing#typing.Hashable" >}}) 和 [`typing.Sized`]({{< ref "/library/development/typing#typing.Sized" >}}) | 3.12   | 未确定                                                       | [gh-94309](https://github.com/python/cpython/issues/94309)   |
| [`typing.TypeAlias`]({{< ref "/library/development/typing#typing.TypeAlias" >}}) | 3.12   | 未确定                                                       | [**PEP 695**](https://peps.python.org/pep-0695/)             |
| [`@typing.no_type_check_decorator`]({{< ref "/library/development/typing#typing.no_type_check_decorator" >}}) | 3.13   | 3.15                                                         | [gh-106309](https://github.com/python/cpython/issues/106309) |
| [`typing.AnyStr`]({{< ref "/library/development/typing#typing.AnyStr" >}}) | 3.13   | 3.18                                                         | [gh-105578](https://github.com/python/cpython/issues/105578) |
