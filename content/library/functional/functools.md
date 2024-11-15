+++
title = "functools —— 高阶函数，以及可调用对象上的操作"
date = 2024-11-15T11:50:11+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/functools.html](https://docs.python.org/zh-cn/3.13/library/functools.html)
>
> 收录该文档的时间：`2024-11-15T11:50:11+08:00`

# `functools` —— 高阶函数，以及可调用对象上的操作

**源代码:** [Lib/functools.py](https://github.com/python/cpython/tree/3.13/Lib/functools.py)

------

[`functools`](https://docs.python.org/zh-cn/3.13/library/functools.html#module-functools) 模块应用于高阶函数，即参数或（和）返回值为其他函数的函数。 通常来说，此模块的功能适用于所有可调用对象。

[`functools`](https://docs.python.org/zh-cn/3.13/library/functools.html#module-functools) 模块定义了以下函数:

## @functools.**cache**(*user_function*)

​	简单轻量级未绑定函数缓存。 有时称为 ["memoize"](https://en.wikipedia.org/wiki/Memoization)。

​	返回值与 `lru_cache(maxsize=None)` 相同，创建一个查找函数参数的字典的简单包装器。 因为它不需要清除旧值，所以比带有大小限制的 [`lru_cache()`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.lru_cache) 更小更快。

​	例如：

```
@cache
def factorial(n):
    return n * factorial(n-1) if n else 1

>>> factorial(10)      # 不预先缓存结果，执行 11 次递归调用
3628800
>>> factorial(5)       # 只查找缓存结果值
120
>>> factorial(12)      # 执行两次新的递归调用，另外 10 次已缓存
479001600
```

​	该缓存是线程安全的因此被包装的函数可在多线程中使用。 这意味着下层的数据结构将在并发更新期间保持一致性。

​	如果另一个线程在初始调用完成并被缓存之前执行了额外的调用则被包装的函数可能会被多次调用。

*Added in version 3.9.*

## @functools.**cached_property**(*func*)

​	将一个类方法转换为特征属性，一次性计算该特征属性的值，然后将其缓存为实例生命周期内的普通属性。 类似于 [`property()`](https://docs.python.org/zh-cn/3.13/library/functions.html#property) 但增加了缓存功能。 对于在其他情况下实际不可变的高计算资源消耗的实例特征属性来说该函数非常有用。

​	示例:

```
class DataSet:

    def __init__(self, sequence_of_numbers):
        self._data = tuple(sequence_of_numbers)

    @cached_property
    def stdev(self):
        return statistics.stdev(self._data)
```

[`cached_property()`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.cached_property) 的设定与 [`property()`](https://docs.python.org/zh-cn/3.13/library/functions.html#property) 有所不同。 常规的 property 会阻止属性写入，除非定义了 setter。 与之相反，*cached_property* 则允许写入。

*cached_property* 装饰器仅在执行查找且不存在同名属性时才会运行。 当运行时，*cached_property* 会写入同名的属性。 后续的属性读取和写入操作会优先于 *cached_property* 方法，其行为就像普通的属性一样。

​	缓存的值可通过删除该属性来清空。 这允许 *cached_property* 方法再次运行。

*cached_property* 不能防止在多线程使用中可能出现的竞争条件。 getter 函数可以在同一实例上多次运行，最后一次运行将设置缓存值。 如果缓存的特征属性是幂等的或者对于在同一实例上多次运行是无害的，那就没有问题。 如果需要进行同步，请在被装饰的 getter 函数内部或在缓存的特征属性访问外部实现必要的锁定操作。

​	注意，这个装饰器会影响 [**PEP 412**](https://peps.python.org/pep-0412/) 键共享字典的操作。 这意味着相应的字典实例可能占用比通常时更多的空间。

​	而且，这个装饰器要求每个实例上的 `__dict__` 是可变的映射。 这意味着它将不适用于某些类型，例如元类（因为类型实例上的 `__dict__` 属性是类命名空间的只读代理），以及那些指定了 `__slots__` 但未包括 `__dict__` 作为所定义的空位之一的类（因为这样的类根本没有提供 `__dict__` 属性）。

​	如果可变的映射不可用或者如果想要节省空间的键共享，可以通过在 [`lru_cache()`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.lru_cache) 上堆叠 [`property()`](https://docs.python.org/zh-cn/3.13/library/functions.html#property) 来实现类似 [`cached_property()`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.cached_property) 的效果。 请参阅 [我该如何缓存方法调用？](https://docs.python.org/zh-cn/3.13/faq/programming.html#faq-cache-method-calls) 了解这与 [`cached_property()`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.cached_property) 之间区别的详情。

*Added in version 3.8.*

*在 3.12 版本发生变更:* 在 Python 3.12 之前，`cached_property` 包括了一个未写入文档的锁用来确保在多线程使用中 getter 函数对于每个实例保证只运行一次。 但是，这个锁是针对特征属性的，不是针对实例的，这可能导致不可接受的高强度锁争用。 在 Python 3.12+ 中这个锁已被移除。

## functools.**cmp_to_key**(*func*)

​	将(旧式的)比较函数转换为新式的 [key function](https://docs.python.org/zh-cn/3.13/glossary.html#term-key-function) . 在类似于 [`sorted()`](https://docs.python.org/zh-cn/3.13/library/functions.html#sorted) ， [`min()`](https://docs.python.org/zh-cn/3.13/library/functions.html#min) ， [`max()`](https://docs.python.org/zh-cn/3.13/library/functions.html#max) ， [`heapq.nlargest()`](https://docs.python.org/zh-cn/3.13/library/heapq.html#heapq.nlargest) ， [`heapq.nsmallest()`](https://docs.python.org/zh-cn/3.13/library/heapq.html#heapq.nsmallest) ， [`itertools.groupby()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.groupby) 等函数的 key 参数中使用。此函数主要用作将 Python 2 程序转换至新版的转换工具，以保持对比较函数的兼容。

​	比较函数是任何接受两个参数，比较它们，并在结果为小于时返回负数，等于时返回零，大于时返回正数的可调用对象。键函数是接受一个参数并返回另一值的可调用对象，返回值在排序时被用作键。

​	示例:

```
sorted(iterable, key=cmp_to_key(locale.strcoll))  # 感知语言区域的排序设置
```

​	有关排序示例和简要排序教程，请参阅 [排序的技术](https://docs.python.org/zh-cn/3.13/howto/sorting.html#sortinghowto)。

*Added in version 3.2.*

## @functools.**lru_cache**(*user_function*)

## @functools.**lru_cache**(*maxsize=128*, *typed=False*)

​	一个为函数提供缓存功能的装饰器，缓存 *maxsize* 组传入参数，在下次以相同参数调用时直接返回上一次的结果。用以节约高开销或I/O函数的调用时间。

​	该缓存是线程安全的因此被包装的函数可在多线程中使用。 这意味着下层的数据结构将在并发更新期间保持一致性。

​	如果另一个线程在初始调用完成并被缓存之前执行了额外的调用则被包装的函数可能会被多次调用。

​	由于使用字典来缓存结果，因此传给该函数的位置和关键字参数必须为 [hashable](https://docs.python.org/zh-cn/3.13/glossary.html#term-hashable)。

​	不同的参数模式可能会被视为具有单独缓存项的不同调用。 例如，`f(a=1, b=2)` 和 `f(b=2, a=1)` 因其关键字参数顺序不同而可能会具有两个单独的缓存项。

​	如果指定了 *user_function*，它必须是一个可调用对象。 这允许 *lru_cache* 装饰器被直接应用于一个用户自定义函数，让 *maxsize* 保持其默认值 128:

```
@lru_cache
def count_vowels(sentence):
    return sum(sentence.count(vowel) for vowel in 'AEIOUaeiou')
```

​	如果 *maxsize* 设为 `None`，LRU 特性将被禁用且缓存可无限增长。

​	如果 *typed* 被设置为 true ，不同类型的函数参数将被分别缓存。 如果 *typed* 为 false ，实现通常会将它们视为等价的调用，只缓存一个结果。(有些类型，如 *str* 和 *int* ，即使 *typed* 为 false ，也可能被分开缓存）。

​	注意，类型的特殊性只适用于函数的直接参数而不是它们的内容。 标量参数 `Decimal(42)` 和 `Fraction(42)` 被视为具有不同结果的不同调用。相比之下，元组参数 `('answer', Decimal(42))` 和 `('answer', Fraction(42))` 被视为等同的。

​	被包装的函数配有一个 `cache_parameters()` 函数，它返回一个新的 [`dict`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#dict) 用来显示 *maxsize* 和 *typed* 的值。 这只是出于显示信息的目的。 改变这些值没有有任何效果。

​	为了帮助衡量缓存的有效性以及调整 *maxsize* 形参，被包装的函数会带有一个 `cache_info()` 函数，它返回一个 [named tuple](https://docs.python.org/zh-cn/3.13/glossary.html#term-named-tuple) 以显示 *hits*, *misses*, *maxsize* 和 *currsize*。

​	该装饰器也提供了一个用于清理/使缓存失效的函数 `cache_clear()` 。

​	原始的未经装饰的函数可以通过 `__wrapped__` 属性访问。它可以用于检查、绕过缓存，或使用不同的缓存再次装饰原始函数。

​	缓存会保持对参数的引用并返回值，直到它们结束生命期退出缓存或者直到缓存被清空。

​	如果一个方法被缓存，则 `self` 实例参数会被包括在缓存中。 请参阅 [我该如何缓存方法调用？](https://docs.python.org/zh-cn/3.13/faq/programming.html#faq-cache-method-calls)

[LRU (least recently used) 缓存](https://en.wikipedia.org/wiki/Cache_replacement_policies#Least_Recently_Used_(LRU)) 在最近的调用是即将到来的调用的最佳预测值时性能最好 (例如，新闻服务器上的最热门文章倾向于每天发生变化)。 缓存的大小限制可确保缓存不会在长期运行的进程如 web 服务器上无限制地增长。

​	一般来说，LRU 缓存只应在你需要重复使用先前计算的值时使用。 因此，缓存有附带影响的函数、每次调用都需要创建不同的可变对象的函数（如生成器和异步函数）或不纯的函数如 time() 或 random() 等是没有意义的。

​	静态 Web 内容的 LRU 缓存示例:

```
@lru_cache(maxsize=32)
def get_pep(num):
    'Retrieve text of a Python Enhancement Proposal'
    resource = f'https://peps.python.org/pep-{num:04d}'
    try:
        with urllib.request.urlopen(resource) as s:
            return s.read()
    except urllib.error.HTTPError:
        return 'Not Found'

>>> for n in 8, 290, 308, 320, 8, 218, 320, 279, 289, 320, 9991:
...     pep = get_pep(n)
...     print(n, len(pep))

>>> get_pep.cache_info()
CacheInfo(hits=3, misses=8, maxsize=32, currsize=8)
```

​	以下是使用缓存通过 [动态规划](https://zh.wikipedia.org/wiki/动态规划) 计算 [斐波那契数列](https://zh.wikipedia.org/wiki/斐波那契数列) 的例子。

```
@lru_cache(maxsize=None)
def fib(n):
    if n < 2:
        return n
    return fib(n-1) + fib(n-2)

>>> [fib(n) for n in range(16)]
[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377, 610]

>>> fib.cache_info()
CacheInfo(hits=28, misses=16, maxsize=None, currsize=16)
```

*Added in version 3.2.*

*在 3.3 版本发生变更:* 添加 *typed* 选项。

*在 3.8 版本发生变更:* 添加了 *user_function* 选项。

*在 3.9 版本发生变更:* 增加了 `cache_parameters()` 函数

## @functools.**total_ordering**

​	给定一个声明一个或多个全比较排序方法的类，这个类装饰器实现剩余的方法。这减轻了指定所有可能的全比较操作的工作。

​	此类必须包含以下方法之一：`__lt__()` 、`__le__()`、`__gt__()` 或 `__ge__()`。另外，此类必须支持 `__eq__()` 方法。

​	例如：

```
@total_ordering
class Student:
    def _is_valid_operand(self, other):
        return (hasattr(other, "lastname") and
                hasattr(other, "firstname"))
    def __eq__(self, other):
        if not self._is_valid_operand(other):
            return NotImplemented
        return ((self.lastname.lower(), self.firstname.lower()) ==
                (other.lastname.lower(), other.firstname.lower()))
    def __lt__(self, other):
        if not self._is_valid_operand(other):
            return NotImplemented
        return ((self.lastname.lower(), self.firstname.lower()) <
                (other.lastname.lower(), other.firstname.lower()))
```

​	备注

 

​	虽然此装饰器使得创建具有良好行为的完全有序类型变得非常容易，但它 *确实* 是以执行速度更缓慢和派生比较方法的堆栈回溯更复杂为代价的。 如果性能基准测试表明这是特定应用的瓶颈所在，则改为实现全部六个富比较方法应该会轻松提升速度。

​	备注

 

​	这个装饰器不会尝试重写类 *或其上级类* 中已经被声明的方法。 这意味着如果某个上级类定义了比较运算符，则 *total_ordering* 将不会再次实现它，即使原方法是抽象方法。

*Added in version 3.2.*

*在 3.4 版本发生变更:* 现在已支持从未识别的类型的下层比较函数返回 `NotImplemented` 异常。

## functools.**partial**(*func*, */*, **args*, ***keywords*)

​	返回一个新的 [部分对象](https://docs.python.org/zh-cn/3.13/library/functools.html#partial-objects)，当被调用时其行为类似于 *func* 附带位置参数 *args* 和关键字参数 *keywords* 被调用。 如果为调用提供了更多的参数，它们会被附加到 *args*。 如果提供了额外的关键字参数，它们会扩展并重写 *keywords*。 大致等价于:

```
def partial(func, /, *args, **keywords):
    def newfunc(*fargs, **fkeywords):
        newkeywords = {**keywords, **fkeywords}
        return func(*args, *fargs, **newkeywords)
    newfunc.func = func
    newfunc.args = args
    newfunc.keywords = keywords
    return newfunc
```

[`partial()`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.partial) 会被“冻结了”一部分函数参数和/或关键字的部分函数应用所使用，从而得到一个具有简化签名的新对象。 例如，[`partial()`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.partial) 可用来创建一个行为类似于 [`int()`](https://docs.python.org/zh-cn/3.13/library/functions.html#int) 函数的可调用对象，其中 *base* 参数默认为二：



```
>>> from functools import partial
>>> basetwo = partial(int, base=2)
>>> basetwo.__doc__ = 'Convert base 2 string to an int.'
>>> basetwo('10010')
18
```

## *class* functools.**partialmethod**(*func*, */*, **args*, ***keywords*)

​	返回一个新的 [`partialmethod`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.partialmethod) 描述器，其行为类似 [`partial`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.partial) 但它被设计用作方法定义而非直接用作可调用对象。

*func* 必须是一个 [descriptor](https://docs.python.org/zh-cn/3.13/glossary.html#term-descriptor) 或可调用对象（同属两者的对象例如普通函数会被当作描述器来处理）。

​	当 *func* 是一个描述器（例如普通 Python 函数, [`classmethod()`](https://docs.python.org/zh-cn/3.13/library/functions.html#classmethod), [`staticmethod()`](https://docs.python.org/zh-cn/3.13/library/functions.html#staticmethod), `abstractmethod()` 或其他 [`partialmethod`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.partialmethod) 的实例）时, 对 `__get__` 的调用会被委托给底层的描述器，并会返回一个适当的 [部分对象](https://docs.python.org/zh-cn/3.13/library/functools.html#partial-objects) 作为结果。

​	当 *func* 是一个非描述器类可调用对象时，则会动态创建一个适当的绑定方法。 当用作方法时其行为类似普通 Python 函数：将会插入 *self* 参数作为第一个位置参数，其位置甚至会处于提供给 [`partialmethod`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.partialmethod) 构造器的 *args* 和 *keywords* 之前。

​	示例:



```
>>> class Cell:
...     def __init__(self):
...         self._alive = False
...     @property
...     def alive(self):
...         return self._alive
...     def set_state(self, state):
...         self._alive = bool(state)
...     set_alive = partialmethod(set_state, True)
...     set_dead = partialmethod(set_state, False)
...
>>> c = Cell()
>>> c.alive
False
>>> c.set_alive()
>>> c.alive
True
```

*Added in version 3.4.*

## functools.**reduce**(*function*, *iterable*, [*initial*, ]*/*)

​	将两个参数的 *function* 从左至右累积地应用到 *iterable* 的条目，以便将该可迭代对象缩减为单个值。 例如，`reduce(lambda x, y: x+y, [1, 2, 3, 4, 5])` 就是计算 `((((1+2)+3)+4)+5)`。 左边的参数 *x* 是累积的值而右边的参数 *y* 则是来自 *iterable* 的更新值。 如果存在可选项 *initial*，它会被放在参与计算的可迭代对象的条目之前，并在可迭代对象为空时作为默认值。 如果未给出 *initial* 并且 *iterable* 仅包含一个条目，则将返回第一项。

​	大致相当于：

```
initial_missing = object()

def reduce(function, iterable, initial=initial_missing, /):
    it = iter(iterable)
    if initial is initial_missing:
        value = next(it)
    else:
        value = initial
    for element in it:
        value = function(value, element)
    return value
```

​	请参阅 [`itertools.accumulate()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.accumulate) 了解有关可产生所有中间值的迭代器。

## @functools.**singledispatch**

​	将一个函数转换为 [单分派](https://docs.python.org/zh-cn/3.13/glossary.html#term-single-dispatch) [generic function](https://docs.python.org/zh-cn/3.13/glossary.html#term-generic-function)。

​	要定义一个泛型函数，用装饰器 `@singledispatch` 来装饰它。当使用 `@singledispatch` 定义一个函数时，请注意调度发生在第一个参数的类型上:



```
>>> from functools import singledispatch
>>> @singledispatch
... def fun(arg, verbose=False):
...     if verbose:
...         print("Let me just say,", end=" ")
...     print(arg)
```

​	要将重载的实现添加到函数中，请使用泛型函数的 `register()` 属性，它可以被用作装饰器。 对于带有类型标注的函数，该装饰器将自动推断第一个参数的类型:



```
>>> @fun.register
... def _(arg: int, verbose=False):
...     if verbose:
...         print("Strength in numbers, eh?", end=" ")
...     print(arg)
...
>>> @fun.register
... def _(arg: list, verbose=False):
...     if verbose:
...         print("Enumerate this:")
...     for i, elem in enumerate(arg):
...         print(i, elem)
```

​	还可以使用 [`types.UnionType`](https://docs.python.org/zh-cn/3.13/library/types.html#types.UnionType) 和 [`typing.Union`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.Union):



```
>>> @fun.register
... def _(arg: int | float, verbose=False):
...     if verbose:
...         print("Strength in numbers, eh?", end=" ")
...     print(arg)
...
>>> from typing import Union
>>> @fun.register
... def _(arg: Union[list, set], verbose=False):
...     if verbose:
...         print("Enumerate this:")
...     for i, elem in enumerate(arg):
...         print(i, elem)
...
```

​	对于不使用类型标注的代码，可以将适当的类型参数显式地传给装饰器本身:



```
>>> @fun.register(complex)
... def _(arg, verbose=False):
...     if verbose:
...         print("Better than complicated.", end=" ")
...     print(arg.real, arg.imag)
...
```

​	For code that dispatches on a collections type (e.g., `list`), but wants to typehint the items of the collection (e.g., `list[int]`), the dispatch type should be passed explicitly to the decorator itself with the typehint going into the function definition:



```
>>> @fun.register(list)
... def _(arg: list[int], verbose=False):
...     if verbose:
...         print("Enumerate this:")
...     for i, elem in enumerate(arg):
...         print(i, elem)
```

​	备注

 

​	At runtime the function will dispatch on an instance of a list regardless of the type contained within the list i.e. `[1,2,3]` will be dispatched the same as `["foo", "bar", "baz"]`. The annotation provided in this example is for static type checkers only and has no runtime impact.

​	要启用注册 [lambda](https://docs.python.org/zh-cn/3.13/glossary.html#term-lambda) 和现有的函数，也可以使用 `register()` 属性的函数形式:



```
>>> def nothing(arg, verbose=False):
...     print("Nothing.")
...
>>> fun.register(type(None), nothing)
```

`register()` 属性会返回未被装饰的函数。 这将启用装饰器栈、[`封存`](https://docs.python.org/zh-cn/3.13/library/pickle.html#module-pickle)，并为每个变量单独创建单元测试:



```
>>> @fun.register(float)
... @fun.register(Decimal)
... def fun_num(arg, verbose=False):
...     if verbose:
...         print("Half of your number:", end=" ")
...     print(arg / 2)
...
>>> fun_num is fun
False
```

​	在调用时，泛型函数会根据第一个参数的类型进行分派:



```
>>> fun("Hello, world.")
Hello, world.
>>> fun("test.", verbose=True)
Let me just say, test.
>>> fun(42, verbose=True)
Strength in numbers, eh? 42
>>> fun(['spam', 'spam', 'eggs', 'spam'], verbose=True)
Enumerate this:
0 spam
1 spam
2 eggs
3 spam
>>> fun(None)
Nothing.
>>> fun(1.23)
0.615
```

​	在没有针对特定类型的已注册实现的情况下，会使用其方法解析顺序来查找更通用的实现。 使用 `@singledispatch` 装饰的原始函数将为基本的 [`object`](https://docs.python.org/zh-cn/3.13/library/functions.html#object) 类型进行注册，这意味着它将在找不到更好的实现时被使用。

​	如果一个实现被注册到 [abstract base class](https://docs.python.org/zh-cn/3.13/glossary.html#term-abstract-base-class)，则基类的虚拟子类将被发送到该实现:



```
>>> from collections.abc import Mapping
>>> @fun.register
... def _(arg: Mapping, verbose=False):
...     if verbose:
...         print("Keys & Values")
...     for key, value in arg.items():
...         print(key, "=>", value)
...
>>> fun({"a": "b"})
a => b
```

​	要检查泛型函数将为给定的类型选择哪个实现，请使用 `dispatch()` 属性:



```
>>> fun.dispatch(float)
<function fun_num at 0x1035a2840>
>>> fun.dispatch(dict)    # note: default implementation
<function fun at 0x103fe0000>
```

​	要访问所有已注册实现，请使用只读的 `registry` 属性:



```
>>> fun.registry.keys()
dict_keys([<class 'NoneType'>, <class 'int'>, <class 'object'>,
          <class 'decimal.Decimal'>, <class 'list'>,
          <class 'float'>])
>>> fun.registry[float]
<function fun_num at 0x1035a2840>
>>> fun.registry[object]
<function fun at 0x103fe0000>
```

*Added in version 3.4.*

*在 3.7 版本发生变更:* `register()` 属性现在支持使用类型标注。

*在 3.11 版本发生变更:* `register()` 属性现在支持将 [`types.UnionType`](https://docs.python.org/zh-cn/3.13/library/types.html#types.UnionType) 和 [`typing.Union`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.Union) 作为类型标注。

## *class* functools.**singledispatchmethod**(*func*)

​	将一个方法转换为 [单分派](https://docs.python.org/zh-cn/3.13/glossary.html#term-single-dispatch) [generic function](https://docs.python.org/zh-cn/3.13/glossary.html#term-generic-function)。

​	要定义一个泛型方法，请用 `@singledispatchmethod` 装饰器来装饰它。 当使用 `@singledispatchmethod` 定义一个函数时，请注意发送操作将针对第一个非 *self* 或非 *cls* 参数的类型上:

```
class Negator:
    @singledispatchmethod
    def neg(self, arg):
        raise NotImplementedError("Cannot negate a")

    @neg.register
    def _(self, arg: int):
        return -arg

    @neg.register
    def _(self, arg: bool):
        return not arg
```

`@singledispatchmethod` 支持与其他装饰器如 [`@classmethod`](https://docs.python.org/zh-cn/3.13/library/functions.html#classmethod) 相嵌套。 请注意为了允许 `dispatcher.register`，`singledispatchmethod` 必须是 *最外层的* 装饰器。 下面是一个 `Negator` 类包含绑定到类的 `neg` 方法，而不是一个类实例:

```
class Negator:
    @singledispatchmethod
    @classmethod
    def neg(cls, arg):
        raise NotImplementedError("Cannot negate a")

    @neg.register
    @classmethod
    def _(cls, arg: int):
        return -arg

    @neg.register
    @classmethod
    def _(cls, arg: bool):
        return not arg
```

​	同样的模式也可被用于其他类似的装饰器: [`@staticmethod`](https://docs.python.org/zh-cn/3.13/library/functions.html#staticmethod), [`@abstractmethod`](https://docs.python.org/zh-cn/3.13/library/abc.html#abc.abstractmethod) 等等。

*Added in version 3.8.*

## functools.**update_wrapper**(*wrapper*, *wrapped*, *assigned=WRAPPER_ASSIGNMENTS*, *updated=WRAPPER_UPDATES*)

​	Update a *wrapper* function to look like the *wrapped* function. The optional arguments are tuples to specify which attributes of the original function are assigned directly to the matching attributes on the wrapper function and which attributes of the wrapper function are updated with the corresponding attributes from the original function. The default values for these arguments are the module level constants `WRAPPER_ASSIGNMENTS` (which assigns to the wrapper function's [`__module__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#function.__module__), [`__name__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#function.__name__), [`__qualname__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#function.__qualname__), [`__annotations__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#function.__annotations__), [`__type_params__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#function.__type_params__), and [`__doc__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#function.__doc__), the documentation string) and `WRAPPER_UPDATES` (which updates the wrapper function's [`__dict__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#function.__dict__), i.e. the instance dictionary).

​	为了允许出于内省和其他目的访问原始函数（例如绕过 [`lru_cache()`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.lru_cache) 之类的缓存装饰器），此函数会自动为 wrapper 添加一个指向被包装函数的 `__wrapped__` 属性。

​	此函数的主要目的是在 [decorator](https://docs.python.org/zh-cn/3.13/glossary.html#term-decorator) 函数中用来包装被装饰的函数并返回包装器。 如果包装器函数未被更新，则被返回函数的元数据将反映包装器定义而不是原始函数定义，这通常没有什么用处。

[`update_wrapper()`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.update_wrapper) 可以与函数之外的可调用对象一同使用。 在 *assigned* 或 *updated* 中命名的任何属性如果不存在于被包装对象则会被忽略（即该函数将不会尝试在包装器函数上设置它们）。 如果包装器函数自身缺少在 *updated* 中命名的任何属性则仍将引发 [`AttributeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#AttributeError)。

*在 3.2 版本发生变更:* The `__wrapped__` attribute is now automatically added. The [`__annotations__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#function.__annotations__) attribute is now copied by default. Missing attributes no longer trigger an [`AttributeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#AttributeError).

*在 3.4 版本发生变更:* `__wrapped__` 属性现在总是指向被包装的函数，即使该函数定义了 `__wrapped__` 属性。 (参见 [bpo-17482](https://bugs.python.org/issue?@action=redirect&bpo=17482))

*在 3.12 版本发生变更:* The [`__type_params__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#function.__type_params__) attribute is now copied by default.

## @functools.**wraps**(*wrapped*, *assigned=WRAPPER_ASSIGNMENTS*, *updated=WRAPPER_UPDATES*)

​	这是一个便捷函数，用于在定义包装器函数时发起调用 [`update_wrapper()`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.update_wrapper) 作为函数装饰器。 它等价于 `partial(update_wrapper, wrapped=wrapped, assigned=assigned, updated=updated)`。 例如:



```
>>> from functools import wraps
>>> def my_decorator(f):
...     @wraps(f)
...     def wrapper(*args, **kwds):
...         print('Calling decorated function')
...         return f(*args, **kwds)
...     return wrapper
...
>>> @my_decorator
... def example():
...     """Docstring"""
...     print('Called example function')
...
>>> example()
Calling decorated function
Called example function
>>> example.__name__
'example'
>>> example.__doc__
'Docstring'
```

​	如果不使用这个装饰器工厂函数，则 example 函数的名称将变为 `'wrapper'`，并且 `example()` 原本的文档字符串将会丢失。



## [`partial`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.partial) 对象

[`partial`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.partial) 对象是由 [`partial()`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.partial) 创建的可调用对象。 它们具有三个只读属性：

## partial.**func**

​	一个可调用对象或函数。 对 [`partial`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.partial) 对象的调用将被转发给 [`func`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.partial.func) 并附带新的参数和关键字。

## partial.**args**

​	最左边的位置参数将放置在提供给 [`partial`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.partial) 对象调用的位置参数之前。

## partial.**keywords**

​	当调用 [`partial`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.partial) 对象时将要提供的关键字参数。

[`partial`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.partial) objects are like [function objects](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#user-defined-funcs) in that they are callable, weak referenceable, and can have attributes. There are some important differences. For instance, the [`__name__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#function.__name__) and [`function.__doc__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#function.__doc__) attributes are not created automatically. Also, [`partial`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.partial) objects defined in classes behave like static methods and do not transform into bound methods during instance attribute look-up.
