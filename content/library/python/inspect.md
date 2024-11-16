+++
title = "inspect --- 检查当前对象"
date = 2024-11-15T21:12:39+08:00
weight = 130
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/inspect.html](https://docs.python.org/zh-cn/3.13/library/inspect.html)
>
> 收录该文档的时间：`2024-11-15T21:12:39+08:00`

# `inspect` --- 检查当前对象

**源代码:** [Lib/inspect.py](https://github.com/python/cpython/tree/3.13/Lib/inspect.py)

------

[`inspect`]({{< ref "/library/python/inspect#module-inspect" >}}) 模块提供了一些有用的函数帮助获取对象的信息，例如模块、类、方法、函数、回溯、帧对象以及代码对象。例如它可以帮助你检查类的内容，获取某个方法的源代码，取得并格式化某个函数的参数列表，或者获取你需要显示的回溯的详细信息。

​	该模块提供了4种主要的功能：类型检查、获取源代码、检查类与函数、检查解释器的调用堆栈。



## 类型和成员

[`getmembers()`]({{< ref "/library/python/inspect#inspect.getmembers" >}}) 函数获取对象如类或模块的成员。 名称以“is”打头的函数主要是作为传给 [`getmembers()`]({{< ref "/library/python/inspect#inspect.getmembers" >}}) 的第二个参数的便捷选项提供的。 它们还可帮助你确定你是否能找到下列特殊属性（请参阅 [模块对象上与导入相关的属性]({{< ref "/reference/datamodel#import-mod-attrs" >}}) 了解有关模块属性的详情）:

| 类型                | 属性               | 描述                                                         |
| :------------------ | :----------------- | :----------------------------------------------------------- |
| class -- 类         | __doc__            | 文档字符串                                                   |
|                     | __name__           | 类定义时所使用的名称                                         |
|                     | __qualname__       | qualified name -- 限定名称                                   |
|                     | __module__         | 该类型被定义时所在的模块的名称                               |
|                     | __type_params__    | 一个包含泛型类的 [类型形参]({{< ref "/reference/compound_stmts#type-params" >}}) 的元组 |
| method -- 方法      | __doc__            | 文档字符串                                                   |
|                     | __name__           | 该方法定义时所使用的名称                                     |
|                     | __qualname__       | qualified name -- 限定名称                                   |
|                     | __func__           | 实现该方法的函数对象                                         |
|                     | __self__           | 该方法被绑定的实例，若没有绑定则为 `None`                    |
|                     | __module__         | 定义此方法的模块的名称                                       |
| function -- 函数    | __doc__            | 文档字符串                                                   |
|                     | __name__           | 用于定义此函数的名称                                         |
|                     | __qualname__       | qualified name -- 限定名称                                   |
|                     | __code__           | 包含已编译函数的代码对象 [bytecode]({{< ref "/glossary/idx#term-bytecode" >}}) |
|                     | __defaults__       | 所有位置或关键字参数的默认值的元组                           |
|                     | __kwdefaults__     | 保存仅关键字参数的所有默认值的映射                           |
|                     | __globals__        | 此函数定义所在的全局命名空间                                 |
|                     | __builtins__       | builtins 命名空间                                            |
|                     | __annotations__    | 参数名称到注解的映射；保留键 `"return"` 用于返回值注解。     |
|                     | __type_params__    | 一个包含泛型函数的 [类型形参]({{< ref "/reference/compound_stmts#type-params" >}}) 的元组 |
|                     | __module__         | 此函数定义所在的模块名称                                     |
| traceback -- 回溯   | tb_frame           | 此层的帧对象                                                 |
|                     | tb_lasti           | 在字节码中最后尝试的指令的索引                               |
|                     | tb_lineno          | 当前行在 Python 源代码中的行号                               |
|                     | tb_next            | 下一个内部回溯对象（由本层调用）                             |
| frame -- 帧         | f_back             | 下一个外部帧对象（此帧的调用者）                             |
|                     | f_builtins         | 此帧执行时所在的 builtins 命名空间                           |
|                     | f_code             | 在此帧中执行的代码对象                                       |
|                     | f_globals          | 此帧执行时所在的全局命名空间                                 |
|                     | f_lasti            | 在字节码中最后尝试的指令的索引                               |
|                     | f_lineno           | 当前行在 Python 源代码中的行号                               |
|                     | f_locals           | 此帧所看到的局部命名空间                                     |
|                     | f_trace            | 此帧的追踪函数，或 `None`                                    |
| code -- 代码        | co_argcount        | 参数数量（不包括仅关键字参数、* 或 ** 参数）                 |
|                     | co_code            | 字符串形式的原始字节码                                       |
|                     | co_cellvars        | 单元变量名称的元组(通过包含作用域引用)                       |
|                     | co_consts          | 字节码中使用的常量元组                                       |
|                     | co_filename        | 创建此代码对象的文件的名称                                   |
|                     | co_firstlineno     | 第一行在Python源代码中的行号                                 |
|                     | co_flags           | `CO_*` 标志的位图，详见 [此处]({{< ref "/library/python/inspect#inspect-module-co-flags" >}}) |
|                     | co_lnotab          | 编码的行号到字节码索引的映射                                 |
|                     | co_freevars        | 自由变量的名字组成的元组（通过函数闭包引用）                 |
|                     | co_posonlyargcount | 仅限位置参数的数量                                           |
|                     | co_kwonlyargcount  | 仅限关键字参数的数量（不包括 ** 参数）                       |
|                     | co_name            | 定义此代码对象的名称                                         |
|                     | co_qualname        | 定义此代码对象的完整限定名称                                 |
|                     | co_names           | 除参数和函数局部变量之外的名称元组                           |
|                     | co_nlocals         | 局部变量的数量                                               |
|                     | co_stacksize       | 需要虚拟机堆栈空间                                           |
|                     | co_varnames        | 参数名和局部变量的元组                                       |
| generator -- 生成器 | __name__           | 名称                                                         |
|                     | __qualname__       | qualified name -- 限定名称                                   |
|                     | gi_frame           | frame -- 帧                                                  |
|                     | gi_running         | 生成器在运行吗？                                             |
|                     | gi_code            | code -- 代码                                                 |
|                     | gi_yieldfrom       | 通过 `yield from``迭代的对象，或``None`                      |
| 异步生成器          | __name__           | 名称                                                         |
|                     | __qualname__       | qualified name -- 限定名称                                   |
|                     | ag_await           | 正在等待的对象，或 `None`                                    |
|                     | ag_frame           | frame -- 帧                                                  |
|                     | ag_running         | 生成器在运行吗？                                             |
|                     | ag_code            | code -- 代码                                                 |
| coroutine -- 协程   | __name__           | 名称                                                         |
|                     | __qualname__       | qualified name -- 限定名称                                   |
|                     | cr_await           | 正在等待的对象，或 `None`                                    |
|                     | cr_frame           | frame -- 帧                                                  |
|                     | cr_running         | 这个协程正在运行吗？                                         |
|                     | cr_code            | code -- 代码                                                 |
|                     | cr_origin          | 协程被创建的位置，或 `None`。参见 [`sys.set_coroutine_origin_tracking_depth()`]({{< ref "/library/python/sys#sys.set_coroutine_origin_tracking_depth" >}}) |
| builtin             | __doc__            | 文档字符串                                                   |
|                     | __name__           | 此函数或方法的原始名称                                       |
|                     | __qualname__       | qualified name -- 限定名称                                   |
|                     | __self__           | 方法绑定到的实例，或 `None`                                  |

> 在 3.5 版本发生变更: 为生成器添加 `__qualname__` 和 `gi_yieldfrom` 属性。

​	生成器的 `__name__` 属性现在由函数名称设置，而不是代码对象名称，并且现在可以被修改。

> 在 3.7 版本发生变更: 为协程添加 `cr_origin` 属性。

> 在 3.10 版本发生变更: 为函数添加 `__builtins__` 属性。

## inspect.**getmembers**(*object*[, *predicate*])

​	返回一个对象上的所有成员，组成以 `(名称, 值)` 对为元素的列表，按名称排序。如果提供了可选的 *predicate* 参数（会对每个成员的 `值` 对象进行一次调用），则仅包含该断言为真的成员。

​备注
 

​	当参数是一个类且这些属性在元类的自定义方法 [`__dir__()`]({{< ref "/reference/datamodel#object.__dir__" >}}) 中列出时 [`getmembers()`]({{< ref "/library/python/inspect#inspect.getmembers" >}}) 将只返回在元类中定义的类属性。

## inspect.**getmembers_static**(*object*[, *predicate*])

​	将一个对象的所有成员作为由 `(name, value)` 对组成并按名称排序的列表返回而不触发通过描述器协议 __getattr__ 或 __getattribute__ 执行的动态查找。 或是作为可选项，只返回满足给定预期的成员。

​备注
 

[`getmembers_static()`]({{< ref "/library/python/inspect#inspect.getmembers_static" >}}) 可能无法获得 getmembers 所能获取的所有成员（如动态创建的属性）并且可能会找到一些 getmembers 所不能找到的成员（如会引发 AttributeError 的描述器）。 在某些情况下它还能返回描述器对象而不是实例成员。

> Added in version 3.11.
>

## inspect.**getmodulename**(*path*)

​	返回由文件名 *path* 表示的模块名字，但不包括外层的包名。文件扩展名会检查是否在 [`importlib.machinery.all_suffixes()`]({{< ref "/library/modules/importlib#importlib.machinery.all_suffixes" >}}) 列出的条目中。如果符合，则文件路径的最后一个组成部分会去掉后缀名后被返回；否则返回 `None`。

​	值得注意的是，这个函数 *仅* 返回可以作为 Python 模块的名字，而有可能指向一个 Python 包的路径仍然会返回 `None`。

> 在 3.3 版本发生变更: 该函数直接基于 [`importlib`]({{< ref "/library/modules/importlib#module-importlib" >}})。

## inspect.**ismodule**(*object*)

​	当该对象是一个模块时返回 `True`。

## inspect.**isclass**(*object*)

​	当该对象是一个类时返回 `True`，无论是内置类或者 Python 代码中定义的类。

## inspect.**ismethod**(*object*)

​	当该对象是一个 Python 写成的绑定方法时返回 `True`。

## inspect.**isfunction**(*object*)

​	当该对象是一个 Python 函数时（包括使用 [lambda]({{< ref "/glossary/idx#term-lambda" >}}) 表达式创造的函数），返回 `True`。

## inspect.**isgeneratorfunction**(*object*)

​	当该对象是一个 Python 生成器函数时返回 `True`。

> 在 3.8 版本发生变更: 对于使用 [`functools.partial()`]({{< ref "/library/functional/functools#functools.partial" >}}) 封装的函数，如果被封装的函数是一个 Python 生成器函数，现在也会返回 `True`。

> 在 3.13 版本发生变更: 现在对于使用 [`functools.partialmethod()`]({{< ref "/library/functional/functools#functools.partialmethod" >}}) 包装的函数，如果被包装的函数是一个 Python 生成器函数则返回 `True`。

## inspect.**isgenerator**(*object*)

​	当该对象是一个生成器时返回 `True`。

## inspect.**iscoroutinefunction**(*object*)

​	如果该对象为 [coroutine function]({{< ref "/glossary/idx#term-coroutine-function" >}}) (使用 [`async def`]({{< ref "/reference/compound_stmts#async-def" >}}) 语法定义的函数), 包装了 [coroutine function]({{< ref "/glossary/idx#term-coroutine-function" >}}) 的 [`functools.partial()`]({{< ref "/library/functional/functools#functools.partial" >}}) 或使用 [`markcoroutinefunction()`]({{< ref "/library/python/inspect#inspect.markcoroutinefunction" >}}) 标记的同步函数则返回 `True`。

> Added in version 3.5.
>

> 在 3.8 版本发生变更: 对于使用 [`functools.partial()`]({{< ref "/library/functional/functools#functools.partial" >}}) 封装的函数，如果被封装的函数是一个 [协程函数]({{< ref "/glossary/idx#term-coroutine-function" >}}) ，现在也会返回 `True`。

> 在 3.12 版本发生变更: 使用 [`markcoroutinefunction()`]({{< ref "/library/python/inspect#inspect.markcoroutinefunction" >}}) 标记的同步函数现在将返回 `True`。

> 在 3.13 版本发生变更: 现在对于使用 [`functools.partialmethod()`]({{< ref "/library/functional/functools#functools.partialmethod" >}}) 包装的函数，如果被包装的函数是一个 [coroutine function]({{< ref "/glossary/idx#term-coroutine-function" >}}) 则返回 `True`。

## inspect.**markcoroutinefunction**(*func*)

​	将一个可调用对象标记为 [coroutine function]({{< ref "/glossary/idx#term-coroutine-function" >}}) 的装饰器，如果它不会被 [`iscoroutinefunction()`]({{< ref "/library/python/inspect#inspect.iscoroutinefunction" >}}) 检测到的话。

​	这可被用于返回 [coroutine]({{< ref "/glossary/idx#term-coroutine" >}}) 的同步函数，如果该函数被传给需要 [`iscoroutinefunction()`]({{< ref "/library/python/inspect#inspect.iscoroutinefunction" >}}) 的 API 的话。

​	在可能的情况下，更推荐使用 [`async def`]({{< ref "/reference/compound_stmts#async-def" >}}) 函数。 调用该函数并使用 [`iscoroutine()`]({{< ref "/library/python/inspect#inspect.iscoroutine" >}}) 来检测其返回值也是可接受的。

> Added in version 3.12.
>

## inspect.**iscoroutine**(*object*)

​	当该对象是一个由 [`async def`]({{< ref "/reference/compound_stmts#async-def" >}}) 函数创建的 [协程]({{< ref "/glossary/idx#term-coroutine" >}}) 时返回 `True`。

> Added in version 3.5.
>

## inspect.**isawaitable**(*object*)

​	如果该对象可以在 [`await`]({{< ref "/reference/expressions#await" >}}) 表达式中使用时返回 `True`。

​	也可被用于区分基于生成器的协程和常规的生成器：

```
import types

def gen():
    yield
@types.coroutine
def gen_coro():
    yield

assert not isawaitable(gen())
assert isawaitable(gen_coro())
```

> Added in version 3.5.
>

## inspect.**isasyncgenfunction**(*object*)

​	如果对象是一个 [asynchronous generator]({{< ref "/glossary/idx#term-asynchronous-generator" >}}) 函数则返回 `True`，例如：



``` python
>>> async def agen():
...     yield 1
...
>>> inspect.isasyncgenfunction(agen)
True
```

> Added in version 3.6.
>

> 在 3.8 版本发生变更: 对于使用 [`functools.partial()`]({{< ref "/library/functional/functools#functools.partial" >}}) 封装的函数，如果被封装的函数是一个 [异步生成器]({{< ref "/glossary/idx#term-asynchronous-generator" >}}) 函数现在也会返回 `True`。

> 在 3.13 版本发生变更: 现在对于使用 [`functools.partialmethod()`]({{< ref "/library/functional/functools#functools.partialmethod" >}}) 包装的函数，如果被包装的函数是一个 [coroutine function]({{< ref "/glossary/idx#term-coroutine-function" >}}) 则返回 `True`。

## inspect.**isasyncgen**(*object*)

​	如果该对象是一个由 [异步生成器]({{< ref "/glossary/idx#term-asynchronous-generator" >}}) 函数创建的 [异步生成器迭代器]({{< ref "/glossary/idx#term-asynchronous-generator-iterator" >}})，则返回 `True`。

> Added in version 3.6.
>

## inspect.**istraceback**(*object*)

​	如果该对象是一个回溯则返回 `True`。

## inspect.**isframe**(*object*)

​	如果该对象是一个帧对象则返回 `True`。

## inspect.**iscode**(*object*)

​	如果该对象是一个代码对象则返回 `True`。

## inspect.**isbuiltin**(*object*)

​	如果该对象是一个内置函数或一个绑定的内置方法，则返回 `True`。

## inspect.**ismethodwrapper**(*object*)

​	如果对象类型为 [`MethodWrapperType`]({{< ref "/library/datatypes/types#types.MethodWrapperType" >}}) 则返回 `True`。

​	这些是 [`MethodWrapperType`]({{< ref "/library/datatypes/types#types.MethodWrapperType" >}}) 的实例，如 [`__str__()`]({{< ref "/reference/datamodel#object.__str__" >}}), [`__eq__()`]({{< ref "/reference/datamodel#object.__eq__" >}}) 和 [`__repr__()`]({{< ref "/reference/datamodel#object.__repr__" >}})。

> Added in version 3.11.
>

## inspect.**isroutine**(*object*)

​	如果该对象是一个用户定义的或内置的函数或者方法，则返回 `True`。

## inspect.**isabstract**(*object*)

​	如果该对象是一个抽象基类则返回 `True`。

## inspect.**ismethoddescriptor**(*object*)

​	如果该对象是一个方法描述器，但 [`ismethod()`]({{< ref "/library/python/inspect#inspect.ismethod" >}}) 、 [`isclass()`]({{< ref "/library/python/inspect#inspect.isclass" >}}) 、 [`isfunction()`]({{< ref "/library/python/inspect#inspect.isfunction" >}}) 及 [`isbuiltin()`]({{< ref "/library/python/inspect#inspect.isbuiltin" >}}) 均不为真，则返回 `True`。

​	This, for example, is true of `int.__add__`. An object passing this test has a [`__get__()`]({{< ref "/reference/datamodel#object.__get__" >}}) method, but not a [`__set__()`]({{< ref "/reference/datamodel#object.__set__" >}}) method or a [`__delete__()`]({{< ref "/reference/datamodel#object.__delete__" >}}) method. Beyond that, the set of attributes varies. A [`__name__`]({{< ref "/library/stdtypes#definition.__name__" >}}) attribute is usually sensible, and [`__doc__`]({{< ref "/library/stdtypes#definition.__doc__" >}}) often is.

​	以描述器实现的能够通过其他某个测试的函数对于 [`ismethoddescriptor()`]({{< ref "/library/python/inspect#inspect.ismethoddescriptor" >}}) 测试也会返回 `False`，这只是因为其他测试提供了更多保证 —— 比如，当一个对象通过 [`ismethod()`]({{< ref "/library/python/inspect#inspect.ismethod" >}}) 时你将能够使用 [`__func__`]({{< ref "/reference/datamodel#method.__func__" >}}) 等属性。

> 在 3.13 版本发生变更: 此函数将不再错误地将具有 [`__get__()`]({{< ref "/reference/datamodel#object.__get__" >}}) 和 [`__delete__()`]({{< ref "/reference/datamodel#object.__delete__" >}})，但没有 [`__set__()`]({{< ref "/reference/datamodel#object.__set__" >}}) 的对象报告为方法描述器（这样的对象是数据描述器，不是方法描述器）。

## inspect.**isdatadescriptor**(*object*)

​	如果该对象是一个数据描述器则返回 `True`。

​	数据描述器具有 [`__set__`]({{< ref "/reference/datamodel#object.__set__" >}}) 或 [`__delete__`]({{< ref "/reference/datamodel#object.__delete__" >}}) 方法。 例如特征属性（在 Python 中定义）、getset 和成员等。 后两者是在 C 中定义并且有针对这些类型的更具体的测试，它们在不同 Python 实现中均能保持健壮性。 通常，数据描述器还具有 [`__name__`]({{< ref "/library/stdtypes#definition.__name__" >}}) 和 `__doc__` 属性（特征属性、getset 和成员都同时具有这些属性），但并不保证这一点。

## inspect.**isgetsetdescriptor**(*object*)

​	如果该对象是一个 getset 描述器则返回 `True`。

**CPython 实现细节：** getset 是在扩展模块中通过 [`PyGetSetDef`](https://docs.python.org/zh-cn/3.13/c-api/structures.html#c.PyGetSetDef) 结构体定义的属性。对于不包含这种类型的 Python 实现，这个方法将永远返回 `False`。

## inspect.**ismemberdescriptor**(*object*)

​	如果该对象是一个成员描述器则返回 `True`。

**CPython 实现细节：** 成员描述器是在扩展模块中通过 [`PyMemberDef`](https://docs.python.org/zh-cn/3.13/c-api/structures.html#c.PyMemberDef) 结构体定义的属性。对于不包含这种类型的 Python 实现，这个方法将永远返回 `False`。



## 获取源代码

## inspect.**getdoc**(*object*)

​	获取对象的文档字符串并通过 [`cleandoc()`]({{< ref "/library/python/inspect#inspect.cleandoc" >}}) 进行清理。如果对象本身并未提供文档字符串并且这个对象是一个类、一个方法、一个特性或者一个描述器，将通过继承层次结构获取文档字符串。如果文档字符串无效或缺失，则返回 `None`。

> 在 3.5 版本发生变更: 文档字符串没有被重写的话现在会被继承。

## inspect.**getcomments**(*object*)

​	任意多行注释作为单一一个字符串返回。对于类、函数和方法，选取紧贴在该对象的源代码之前的注释；对于模块，选取 Python 源文件顶部的注释。如果对象的源代码不可获得，返回 `None`。这可能是因为对象是 C 语言中或者是在交互式命令行中定义的。

## inspect.**getfile**(*object*)

​	返回定义了这个对象的文件名（包括文本文件或二进制文件）。如果该对象是一个内置模块、类或函数则会失败并引发一个 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。

## inspect.**getmodule**(*object*)

​	尝试猜测一个对象是在哪个模块中定义的。 如果无法确定模块则返回 `None`。

## inspect.**getsourcefile**(*object*)

​	返回一个对象定义所在 Python 源文件的名称，如果无法获取源文件则返回 `None`。 如果对象是一个内置模块、类或函数则将失败并引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。

## inspect.**getsourcelines**(*object*)

​	返回一个源代码行的列表和对象的起始行号。 参数可以是一个模块、类、方法、函数、回溯或者代码对象。 源代码将以与该对象所对应的行的列表的形式返回并且行号指明其中第一行代码出现在初始源文件的那个位置。 如果源代码无法被获取则会引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}})。 如果对象是一个内置模块、类或函数则会引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。

> 在 3.3 版本发生变更: 现在会引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 而不是 [`IOError`]({{< ref "/library/exceptions#IOError" >}})，后者现在是前者的一个别名。

## inspect.**getsource**(*object*)

​	返回对象的源代码文本。 参数可以是一个模块、类、方法、函数、回溯帧或代码对象。 源代码将以单个字符串的形式被返回。 如果源代码无法被获取则会引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}})。 如果对象是一个内置模块、类或函数则会引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。

> 在 3.3 版本发生变更: 现在会引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 而不是 [`IOError`]({{< ref "/library/exceptions#IOError" >}})，后者现在是前者的一个别名。

## inspect.**cleandoc**(*doc*)

​	清理文档字符串中为对齐当前代码块进行的缩进

​	第一行的所有前缀空白符会被移除。从第二行开始所有可以被统一去除的空白符也会被去除。之后，首尾的空白行也会被移除。同时，所有制表符会被展开到空格。



## 使用 Signature 对象对可调用对象进行内省

> Added in version 3.3.
>

[`Signature`]({{< ref "/library/python/inspect#inspect.Signature" >}}) 对象代表一个可调用对象的调用签名及其返回值标。 要获取一个 `Signature` 对象，可使用 `signature()` 函数。

## inspect.**signature**(*callable*, ***, *follow_wrapped=True*, *globals=None*, *locals=None*, *eval_str=False*)

​	返回一个给定 *callable* 的 [`Signature`]({{< ref "/library/python/inspect#inspect.Signature" >}}) 对象：



``` python
>>> from inspect import signature
>>> def foo(a, *, b:int, **kwargs):
...     pass

>>> sig = signature(foo)

>>> str(sig)
'(a, *, b: int, **kwargs)'

>>> str(sig.parameters['b'])
'b: int'

>>> sig.parameters['b'].annotation
<class 'int'>
```

​	接受各类的 Python 可调用对象，包括单纯的函数、类，到 [`functools.partial()`]({{< ref "/library/functional/functools#functools.partial" >}}) 对象。

​	对于使用字符化标注的模块中定义的对象 (`from __future__ import annotations`)，[`signature()`]({{< ref "/library/python/inspect#inspect.signature" >}}) 会尝试使用 [`get_annotations()`]({{< ref "/library/python/inspect#inspect.get_annotations" >}}) 自动地反字符串化这些标注。 *globals*, *locals* 和 *eval_str* 等形参会在解析标注时被传入 [`get_annotations()`]({{< ref "/library/python/inspect#inspect.get_annotations" >}})；请参阅 [`get_annotations()`]({{< ref "/library/python/inspect#inspect.get_annotations" >}}) 的文档获取如何使用这些形参的说明。

​	如果没有可提供的签名则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})，而如果对象类型不受支持则会引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。 同时，如果标注被字符串化，并且 *eval_str* 不为假值，则在 [`get_annotations()`]({{< ref "/library/python/inspect#inspect.get_annotations" >}}) 中调用 `eval()` 来反字符串化标注可能会引发任何种类的异常。

​	函数签名中的斜杠（/）表示在它之前的参数是仅限位置的。详见 [编程常见问题中关于仅限位置参数的条目]({{< ref "/faq/programming#faq-positional-only-arguments" >}})

> 在 3.5 版本发生变更: 添加了 *follow_wrapped* 形参。 传入 `False` 以获得特定 *callable* 的签名 (`callable.__wrapped__` 将不会被用来解包被装饰的可调用对象。)

> 在 3.10 版本发生变更: 添加了 *globals*, *locals* 和 *eval_str* 形参。

​备注
 

​	一些可调用对象可能在特定 Python 实现中无法被内省。例如，在 CPython 中，部分通过 C 语言定义的内置函数不提供关于其参数的元数据。

**CPython 实现细节：** 如果传递的对象有一个 `__signature__` 属性 ，我们可以用它来创建签名。确切的语义是实现的一个细节，可能会有未经宣布的更改。有关当前语义，请查阅源代码。

## *class* inspect.**Signature**(*parameters=None*, ***, *return_annotation=Signature.empty*)

`Signature` 对象表示一个函数的调用签名及其返回值标注。 对于函数所接受的每个形参它会在其 [`parameters`]({{< ref "/library/python/inspect#inspect.Signature.parameters" >}}) 多项集中存储一个 [`Parameter`]({{< ref "/library/python/inspect#inspect.Parameter" >}}) 对象。

​	可选参数 *parameters* 是一个 [`Parameter`]({{< ref "/library/python/inspect#inspect.Parameter" >}}) 对象组成的序列，它会在之后被验证不存在名字重复的参数，并且参数处于正确的顺序，即仅限位置参数最前，之后紧接着可位置可关键字参数，并且有默认值参数在无默认值参数之前。

​	可选的 *return_annotation* 参数可以是任意 Python 对象。 它表示可调用对象的 "return" 标注。

`Signature` 对象是 *不可变* 对象。 使用 [`Signature.replace()`]({{< ref "/library/python/inspect#inspect.Signature.replace" >}}) 或 [`copy.replace()`]({{< ref "/library/datatypes/copy#copy.replace" >}}) 来创建经修改的副本。

> 在 3.5 版本发生变更: `Signature` 对象现在是可 pickle 且 [hashable]({{< ref "/glossary/idx#term-hashable" >}}) 的对象。

## **empty**

​	该类的一个特殊标记来明确指出返回值标注缺失。

## **parameters**

​	一个参数名字到对应 [`Parameter`]({{< ref "/library/python/inspect#inspect.Parameter" >}}) 对象的有序映射。参数以严格的定义顺序出现，包括仅关键字参数。

> 在 3.7 版本发生变更: Python 从 3.7 版起才显式地保证了它保持仅关键字参数的定义顺序，尽管实践上在 Python 3 中一直保持了这个顺序。

## **return_annotation**

​	可调用对象的“返回值”标注。如果可调用对象没有“返回值”标注，这个属性会被设置为 [`Signature.empty`]({{< ref "/library/python/inspect#inspect.Signature.empty" >}})。

## **bind**(**args*, ***kwargs*)

​	构造一个位置和关键字实参到形参的映射。如果 `*args` 和 `**kwargs` 符合签名，则返回一个 [`BoundArguments`]({{< ref "/library/python/inspect#inspect.BoundArguments" >}})；否则引发一个 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}}) 。

## **bind_partial**(**args*, ***kwargs*)

​	与 [`Signature.bind()`]({{< ref "/library/python/inspect#inspect.Signature.bind" >}}) 作用方式相同，但允许省略部分必要的参数（模仿 [`functools.partial()`]({{< ref "/library/functional/functools#functools.partial" >}}) 的行为）。返回 [`BoundArguments`]({{< ref "/library/python/inspect#inspect.BoundArguments" >}})，或者在传入参数不符合签名的情况下，引发一个 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。

## **replace**(**[, parameters][, return_annotation]*)

​	根据发起调用 [`replace()`]({{< ref "/library/python/inspect#inspect.Signature.replace" >}}) 的实例新建一个 [`Signature`]({{< ref "/library/python/inspect#inspect.Signature" >}}) 实例。 可以通过传入不同的 *parameters* 和/或 *return_annotation* 来覆盖基类签名的相应特征属性。 要从拷贝的 `Signature` 中移除 `return_annotation`，可以传入 [`Signature.empty`]({{< ref "/library/python/inspect#inspect.Signature.empty" >}})。



``` python
>>> def test(a, b):
...     pass
...
>>> sig = signature(test)
>>> new_sig = sig.replace(return_annotation="new return anno")
>>> str(new_sig)
"(a, b) -> 'new return anno'"
```

[`Signature`]({{< ref "/library/python/inspect#inspect.Signature" >}}) 对象也被泛型函数 [`copy.replace()`]({{< ref "/library/datatypes/copy#copy.replace" >}}) 所支持。

## **format**(***, *max_width=None*)

​	创建一个 [`Signature`]({{< ref "/library/python/inspect#inspect.Signature" >}}) 对象的字符串表示形式。

​	如果传入了 *max_width*，该方法将尝试将签名调整为每行至多 *max_width* 个字符的多行文本。 如果签名长度超过 *max_width*，所有形参都将位于单独的行。

> Added in version 3.13.
>

## *classmethod* **from_callable**(*obj*, ***, *follow_wrapped=True*, *globals=None*, *locals=None*, *eval_str=False*)

​	返回给定的可调用对象 *obj* 的 [`Signature`]({{< ref "/library/python/inspect#inspect.Signature" >}}) (或其子类)。

​	该方法简化了 [`Signature`]({{< ref "/library/python/inspect#inspect.Signature" >}}) 的子类化操作：

```
class MySignature(Signature):
    pass
sig = MySignature.from_callable(sum)
assert isinstance(sig, MySignature)
```

​	其行为在其他方面都与 [`signature()`]({{< ref "/library/python/inspect#inspect.signature" >}}) 相同。

> Added in version 3.5.
>

> 在 3.10 版本发生变更: 添加了 *globals*, *locals* 和 *eval_str* 形参。

## *class* inspect.**Parameter**(*name*, *kind*, ***, *default=Parameter.empty*, *annotation=Parameter.empty*)

`Parameter` 对象是 *不可变* 对象。 不要直接修改 `Parameter` 对象，你可以使用 [`Parameter.replace()`]({{< ref "/library/python/inspect#inspect.Parameter.replace" >}}) 或 [`copy.replace()`]({{< ref "/library/datatypes/copy#copy.replace" >}}) 来创建一个修改后的副本。

> 在 3.5 版本发生变更: 现在 Parameter 对象可以被 pickle 并且为 [hashable]({{< ref "/glossary/idx#term-hashable" >}})。

## **empty**

​	该类的一个特殊标记来明确指出默认值和标注的缺失。

## **name**

​	参数的名字字符串。这个名字必须是一个合法的 Python 标识符。

**CPython 实现细节：** CPython 会为用于实现推导式和生成器表达式的代码对象构造形如 `.0` 的隐式形参名。

> 在 3.6 版本发生变更: 这些形参名会被此模块公开为 `implicit0` 这样的名字。

## **default**

​	该参数的默认值。如果该参数没有默认值，这个属性会被设置为 [`Parameter.empty`]({{< ref "/library/python/inspect#inspect.Parameter.empty" >}}) 。

## **annotation**

​	该参数的标注。如果该参数没有标注，这个属性会被设置为 [`Parameter.empty`]({{< ref "/library/python/inspect#inspect.Parameter.empty" >}}) 。

## **kind**

​	描述参数值要如何绑定到形参。 可能的取值可通过 [`Parameter`]({{< ref "/library/python/inspect#inspect.Parameter" >}}) 获得 (如 `Parameter.KEYWORD_ONLY`)，并支持比较与排序，基于以下顺序:

| 名称                    | 含意                                                         |
| :---------------------- | :----------------------------------------------------------- |
| *POSITIONAL_ONLY*       | 值必须以位置参数的方式提供。仅限位置参数是在函数定义中出现在 `/` 之前（如果有）的条目。 |
| *POSITIONAL_OR_KEYWORD* | 值既可以以关键字参数的形式提供，也可以以位置参数的形式提供（这是 Python 写成的函数的标准绑定行为的）。 |
| *VAR_POSITIONAL*        | 没有绑定到其他形参的位置实参组成的元组。这对应于 Python 函数定义中的 `*args` 形参。 |
| *KEYWORD_ONLY*          | 值必须以关键字参数的形式提供。仅限关键字形参是在 Python 函数定义中出现在 `*` 或 `*args` 之后的条目。 |
| *VAR_KEYWORD*           | 一个未绑定到其他形参的关键字参数的字典。这对应于 Python 函数定义中的 `**kwargs` 形参。 |

​	示例：打印全部没有默认值的仅限关键字参数：



``` python
>>> def foo(a, b, *, c, d=10):
...     pass

>>> sig = signature(foo)
>>> for param in sig.parameters.values():
...     if (param.kind == param.KEYWORD_ONLY and
...                        param.default is param.empty):
...         print('Parameter:', param)
Parameter: c
```

## kind.**description**

​	描述 [`Parameter.kind`]({{< ref "/library/python/inspect#inspect.Parameter.kind" >}}) 的枚举值。

> Added in version 3.8.
>

​	示例：打印全部参数的描述：



``` python
>>> def foo(a, b, *, c, d=10):
...     pass

>>> sig = signature(foo)
>>> for param in sig.parameters.values():
...     print(param.kind.description)
positional or keyword
positional or keyword
keyword-only
keyword-only
```

## **replace**(**[, name][, kind][, default][, annotation]*)

​	根据发起调用 replace 的实例新建一个 [`Parameter`]({{< ref "/library/python/inspect#inspect.Parameter" >}}) 实例。 要覆盖一个 `Parameter` 属性，可以传入相应的参数。 要从一个 `Parameter` 中移除默认值或/和标注，可以传入 [`Parameter.empty`]({{< ref "/library/python/inspect#inspect.Parameter.empty" >}})。



``` python
>>> from inspect import Parameter
>>> param = Parameter('foo', Parameter.KEYWORD_ONLY, default=42)
>>> str(param)
'foo=42'

>>> str(param.replace()) # Will create a shallow copy of 'param'
'foo=42'

>>> str(param.replace(default=Parameter.empty, annotation='spam'))
"foo: 'spam'"
```

[`Parameter`]({{< ref "/library/python/inspect#inspect.Parameter" >}}) 对象也被泛型函数 [`copy.replace()`]({{< ref "/library/datatypes/copy#copy.replace" >}}) 所支持。

> 在 3.4 版本发生变更: 在 Python 3.3 中 [`Parameter`]({{< ref "/library/python/inspect#inspect.Parameter" >}}) 对象在其 `kind` 被设为 `POSITIONAL_ONLY` 时允许将 `name` 设为 `None`。 现在已不再允许这样做。

## *class* inspect.**BoundArguments**

​	调用 [`Signature.bind()`]({{< ref "/library/python/inspect#inspect.Signature.bind" >}}) 或 [`Signature.bind_partial()`]({{< ref "/library/python/inspect#inspect.Signature.bind_partial" >}}) 的结果。容纳实参到函数的形参的映射。

## **arguments**

​	一个形参名到实参值的可变映射。仅包含显式绑定的参数。对 [`arguments`]({{< ref "/library/python/inspect#inspect.BoundArguments.arguments" >}}) 的修改会反映到 [`args`]({{< ref "/library/python/inspect#inspect.BoundArguments.args" >}}) 和 [`kwargs`]({{< ref "/library/python/inspect#inspect.BoundArguments.kwargs" >}}) 上。

​	应当在任何参数处理目的中与 [`Signature.parameters`]({{< ref "/library/python/inspect#inspect.Signature.parameters" >}}) 结合使用。

​备注
 

​	 [`Signature.bind()`]({{< ref "/library/python/inspect#inspect.Signature.bind" >}}) 和 [`Signature.bind_partial()`]({{< ref "/library/python/inspect#inspect.Signature.bind_partial" >}}) 中采用默认值的参数被跳过。然而，如果有需要的话，可以使用 [`BoundArguments.apply_defaults()`]({{< ref "/library/python/inspect#inspect.BoundArguments.apply_defaults" >}}) 来添加它们。

> 在 3.9 版本发生变更: [`arguments`]({{< ref "/library/python/inspect#inspect.BoundArguments.arguments" >}}) 现在的类型是 [`dict`]({{< ref "/library/stdtypes#dict" >}})。之前，它的类型是 [`collections.OrderedDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.OrderedDict)。

## **args**

​	位置参数的值的元组。由 [`arguments`]({{< ref "/library/python/inspect#inspect.BoundArguments.arguments" >}}) 属性动态计算。

## **kwargs**

​	A dict of keyword arguments values. Dynamically computed from the [`arguments`]({{< ref "/library/python/inspect#inspect.BoundArguments.arguments" >}}) attribute. Arguments that can be passed positionally are included in [`args`]({{< ref "/library/python/inspect#inspect.BoundArguments.args" >}}) instead.

## **signature**

​	向所属 [`Signature`]({{< ref "/library/python/inspect#inspect.Signature" >}}) 对象的一个引用。

## **apply_defaults**()

​	设置缺失的参数的默认值。

​	对于变长位置参数（`*args`），默认值是一个空元组。

​	对于变长关键字参数（`**kwargs`）默认值是一个空字典。



``` python
>>> def foo(a, b='ham', *args): pass
>>> ba = inspect.signature(foo).bind('spam')
>>> ba.apply_defaults()
>>> ba.arguments
{'a': 'spam', 'b': 'ham', 'args': ()}
```

> Added in version 3.5.
>

[`args`]({{< ref "/library/python/inspect#inspect.BoundArguments.args" >}}) 和 [`kwargs`]({{< ref "/library/python/inspect#inspect.BoundArguments.kwargs" >}}) 特征属性可被用于发起调用函数：

```
def test(a, *, b):
    ...

sig = signature(test)
ba = sig.bind(10, b=20)
test(*ba.args, **ba.kwargs)
```

​参见
[**PEP 362**](https://peps.python.org/pep-0362/) - 函数签名对象。

​	包含具体的规范，实现细节和样例。



## 类与函数

## inspect.**getclasstree**(*classes*, *unique=False*)

​	将给定的类的列表组织成嵌套列表的层级结构。每当一个内层列表出现时，它包含的类均派生自紧接着该列表之前的条目的类。每个条目均是一个二元组，包含一个类和它的基类组成的元组。如果 *unique* 参数为真值，则给定列表中的每个类将恰有一个对应条目。否则，运用了多重继承的类和它们的后代将出现多次。

## inspect.**getfullargspec**(*func*)

​	获取一个 Python 函数的形参的名字和默认值。将返回一个 [具名元组]({{< ref "/glossary/idx#term-named-tuple" >}})：

```
FullArgSpec(args, varargs, varkw, defaults, kwonlyargs, kwonlydefaults, annotations)
```

*args* 是一个位置参数的名字的列表。 *varargs* 是 `*` 形参的名字或 `None` 表示不接受任意长位置参数时。 *varkw* 是 `**` 参数的名字，或 `None` 表示不接受任意关键字参数。 *defaults* 是一个包含了默认参数值的 *n* 元组分别对应最后 *n* 个位置参数，或 `None` 则表示没有默认值。 *kwonlyargs* 是一个仅关键词参数列表，保持定义时的顺序。 *kwonlydefaults* 是一个字典映射自 *kwonlyargs* 中包含的形参名。 *annotations* 是一个字典，包含形参值到标注的映射。其中包含一个特殊的键 `"return"` 代表函数返回值的标注（如果有的话）。

​	注意： [`signature()`]({{< ref "/library/python/inspect#inspect.signature" >}}) 和 [Signature 对象]({{< ref "/library/python/inspect#inspect-signature-object" >}}) 提供可调用对象内省更推荐的 API，并且支持扩展模块 API 中可能出现的额外的行为（比如仅限位置参数）。该函数被保留的主要原因是保持兼容 Python 2 的 `inspect` 模块 API。

> 在 3.4 版本发生变更: 该函数现在基于 [`signature()`]({{< ref "/library/python/inspect#inspect.signature" >}}) 但仍然忽略 `__wrapped__` 属性，并且在签名中包含绑定方法中的第一个绑定参数。

> 在 3.6 版本发生变更: 该方法在 Python 3.5 中曾因 [`signature()`]({{< ref "/library/python/inspect#inspect.signature" >}}) 被文档归为弃用。但该决定已被推翻以恢复一个明确受支持的标准接口，以便运用一份源码通用 Python 2/3 间遗留的 `getargspec()` API 的迁移。

> 在 3.7 版本发生变更: Python 从 3.7 版起才显式地保证了它保持仅关键字参数的定义顺序，尽管实践上在 Python 3 中一直保持了这个顺序。

## inspect.**getargvalues**(*frame*)

​	获取传入特定的帧的实参的信息。将返回一个 [具名元组]({{< ref "/glossary/idx#term-named-tuple" >}}) `ArgInfo(args, varargs, keywords, locals)`。 *args* 是一个参数名字的列表。 *varargs* 和 *keyword* 是 `*` 和 `**` 参数的名字或 `None`。 *locals* 是给定的帧的局部环境字典。

​备注
 

​	该函数因疏忽在 Python 3.5 中被错误地标记为弃用。

## inspect.**formatargvalues**(*args*[, *varargs*, *varkw*, *locals*, *formatarg*, *formatvarargs*, *formatvarkw*, *formatvalue*])

​	将 [`getargvalues()`]({{< ref "/library/python/inspect#inspect.getargvalues" >}}) 返回的四个值格式化为美观的参数规格。 format* 的参数是对应的可选格式化函数以转化名字和值为字符串。

​备注
 

​	该函数因疏忽在 Python 3.5 中被错误地标记为弃用。

## inspect.**getmro**(*cls*)

​	返回由类 cls 的全部基类按方法解析顺序组成的元组，包括 cls 本身。所有类不会在此元组中出现多于一次。注意方法解析顺序取决于 cls 的类型。除非使用一个非常奇怪的用户定义元类型，否则 cls 会是元组的第一个元素。

## inspect.**getcallargs**(*func*, */*, **args*, ***kwds*)

​	将 *args* 和 *kwds* 绑定到 Python 函数或方法 *func* 的参数名称，就像将它们作为调用时传入的参数一样。 对于绑定方法，还会将第一个参数 (通常命名为 `self`) 绑定到关联的实例。 将返回一个字典，该字典会将参数名称（包括 `*` 和 `**` 参数的名称，如果有的话）绑定到 *args* 和 *kwds* 中的值。 对于不正确地发起调用 *func* 的情况，即 `func(*args, **kwds)` 因函数签名不兼容而引发异常的时候，将引发一个相同类型的异常并附带相同或相似的消息。 例如：



``` python
>>> from inspect import getcallargs
>>> def f(a, b=1, *pos, **named):
...     pass
...
>>> getcallargs(f, 1, 2, 3) == {'a': 1, 'named': {}, 'b': 2, 'pos': (3,)}
True
>>> getcallargs(f, a=2, x=4) == {'a': 2, 'named': {'x': 4}, 'b': 1, 'pos': ()}
True
>>> getcallargs(f)
Traceback (most recent call last):
...
TypeError: f() missing 1 required positional argument: 'a'
```

> Added in version 3.2.
>

> 自 3.5 版本弃用: 改使用 [`Signature.bind()`]({{< ref "/library/python/inspect#inspect.Signature.bind" >}}) 和 [`Signature.bind_partial()`]({{< ref "/library/python/inspect#inspect.Signature.bind_partial" >}})。

## inspect.**getclosurevars**(*func*)

​	获取自 Python 函数或方法 *func* 引用的外部名字到它们的值的映射。返回一个 [具名元组]({{< ref "/glossary/idx#term-named-tuple" >}}) `ClosureVars(nonlocals, globals, builtins, unbound)`。 *nonlocals* 映射引用的名字到词法闭包变量，*globals* 映射到函数的模块级全局， *builtins* 映射到函数体内可见的内置变量。 *unbound* 是在函数中引用但不能解析到给定的模块全局和内置变量的名字的集合。

​	如果 *func* 不是 Python 函数或方法，将引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。

> Added in version 3.3.
>

## inspect.**unwrap**(*func*, ***, *stop=None*)

​	获取 *func* 所包装的对象。它追踪 `__wrapped__` 属性链并返回最后一个对象。

*stop* 是一个可选的回调，接受包装链的一个对象作为唯一参数，以允许通过让回调返回真值使解包装更早中止。如果回调不曾返回一个真值，将如常返回链中的最后一个对象。例如， [`signature()`]({{< ref "/library/python/inspect#inspect.signature" >}}) 使用该参数来在遇到具有 `__signature__` 参数的对象时停止解包装。

​	如果遇到循环，则引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

> Added in version 3.4.
>

## inspect.**get_annotations**(*obj*, ***, *globals=None*, *locals=None*, *eval_str=False*)

​	计算一个对象的标注字典。

​	 `obj` 可以是一个可调用对象、类或模块。传入其他类型的对象将引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。

​	返回一个字典。 `get_annotations()` 每次调用均返回一个新的字典；对同一个对象调用两次将获得两个不同但相等的字典。

​	该函数帮助你处理若干细节：

- If `eval_str` is true, values of type `str` will be un-stringized using [`eval()`]({{< ref "/library/functions#eval" >}}). This is intended for use with stringized annotations (`from __future__ import annotations`).
- 如果 `obj` 不包含一个标注字典，返回一个空字典。（函数和方法永远包含一个标注字典；类、模块和其他类型的可调用对象则可能没有。）
- 对于类，忽略继承而来的标注。如果一个类不包含自己的标注字典，返回一个空字典。
- 因安全原因，所有对于对象成员和字典值的访问将通过 `getattr()` 和 `dict.get()` 完成。
- 请确保始终、始终、始终返回一个新创建的字典。

`eval_str` controls whether or not values of type `str` are replaced with the result of calling [`eval()`]({{< ref "/library/functions#eval" >}}) on those values:

- If eval_str is true, [`eval()`]({{< ref "/library/functions#eval" >}}) is called on values of type `str`. (Note that `get_annotations` doesn't catch exceptions; if [`eval()`]({{< ref "/library/functions#eval" >}}) raises an exception, it will unwind the stack past the `get_annotations` call.)
- 如果 eval_str 为假（默认值）， `str` 类型的值将不会被改变。

`globals` and `locals` are passed in to [`eval()`]({{< ref "/library/functions#eval" >}}); see the documentation for [`eval()`]({{< ref "/library/functions#eval" >}}) for more information. If `globals` or `locals` is `None`, this function may replace that value with a context-specific default, contingent on `type(obj)`:

- 如果 `obj` 是一个模块， `globals` 默认为 `obj.__dict__`。
- 如果 `obj` 是一个类， `globals` 默认值为 `sys.modules[obj.__module__].__dict__` 并且 `locals` 默认值为 `obj` 的类命名空间。
- 如果 `obj` 是一个可调用对象，则 `globals` 默认为 [`obj.__globals__`]({{< ref "/reference/datamodel#function.__globals__" >}})，而如果 `obj` 是一个包装函数 (使用了 [`functools.update_wrapper()`]({{< ref "/library/functional/functools#functools.update_wrapper" >}})) 则它会先打开包装。

​	调用 `get_annotations` 是获取任何对象的标注字典的最佳实践。关于标注的最佳实践的更多信息，参见 [注解最佳实践]({{< ref "/howto/annotations#annotations-howto" >}})。

> Added in version 3.10.
>



## 解释器栈

​	下列函数有些将返回 [`FrameInfo`]({{< ref "/library/python/inspect#inspect.FrameInfo" >}}) 对象。 出于向下兼容性考虑这些对象允许在所有属性上执行元组类操作但 `positions` 除外。 此行为已被弃用并可能会在未来被移除。

## *class* inspect.**FrameInfo**

## **frame**

​	记录所对应的 [帧对象]({{< ref "/reference/datamodel#frame-objects" >}})。

## **filename**

​	关联到由此记录所对应的帧对象所执行的代码的文件名称。

## **lineno**

​	关联到由此记录所对应的帧对象所执行的代码的当前行的行号。

## **function**

​	由此记录所对应的帧所执行的函数的名称。

## **code_context**

​	来自由此记录所对应的帧所执行的源代码的上下文行组成的列表。

## **index**

​	在 [`code_context`]({{< ref "/library/python/inspect#inspect.FrameInfo.code_context" >}}) 列表中执行的当前行的索引号。

## **positions**

​	包含关联到由此记录所对应的指令的起始行号，结束行号，起始列偏移量和结束列偏移量的 [`dis.Positions`]({{< ref "/library/language/dis#dis.Positions" >}}) 对象。

> 在 3.5 版本发生变更: 返回一个 [named tuple]({{< ref "/glossary/idx#term-named-tuple" >}}) 而非 [`tuple`]({{< ref "/library/stdtypes#tuple" >}})。

> 在 3.11 版本发生变更: `FrameInfo` 现在是一个类实例（以便与之前的 [named tuple]({{< ref "/glossary/idx#term-named-tuple" >}}) 保持向下兼容）。

## *class* inspect.**Traceback**

## **filename**

​	关联到由此回溯所对应的帧所执行的代码的文件名称。

## **lineno**

​	关联到由此回溯所对应的帧所执行的代码的当前行的行号。

## **function**

​	由此回溯所对应的帧所执行的函数的名称。

## **code_context**

​	来自由此回溯所对应的帧所执行的源代码的上下文行组成的列表。

## **index**

​	在 [`code_context`]({{< ref "/library/python/inspect#inspect.Traceback.code_context" >}}) 列表中执行的当前行的索引号。

## **positions**

​	包含关联到此回溯所对应的帧所执行的指令的起始行号，结束行号，起始列偏移量和结束列偏移量的 [`dis.Positions`]({{< ref "/library/language/dis#dis.Positions" >}}) 对象。

> 在 3.11 版本发生变更: `Traceback` 现在是一个类实例（以便与之前的 [named tuple]({{< ref "/glossary/idx#term-named-tuple" >}}) 保持向下兼容）。

​备注
 

​	保留帧对象的引用（可见于这些函数返回的帧记录的第一个元素）会导致你的程序产生循环引用。每当一个循环引用被创建，所有可从产生循环的对象访问的对象的生命周期将会被大幅度延长，即便 Python 的可选的循环检测器被启用。如果这类循环必须被创建，确保它们会被显式地打破以避免对象销毁被延迟从而导致占用内存增加。

​	尽管循环检测器能够处理这种情况，这些帧（包括其局部变量）的销毁可以通过在 [`finally`]({{< ref "/reference/compound_stmts#finally" >}}) 子句中移除循环来产生确定的行为。对于循环检测器在编译 Python 时被禁用或者使用 [`gc.disable()`]({{< ref "/library/python/gc#gc.disable" >}}) 时，这样处理更加尤为重要。比如：

```
def handle_stackframe_without_leak():
    frame = inspect.currentframe()
    try:
        # do something with the frame
    finally:
        del frame
```

​	如果你希望保持帧更长的时间（比如在之后打印回溯），你也可以通过 [`frame.clear()`]({{< ref "/reference/datamodel#frame.clear" >}}) 方法打破循环引用。

​	大部分这些函数支持的可选的 *context* 参数指定返回时包含的上下文的行数，以当前行为中心。

## inspect.**getframeinfo**(*frame*, *context=1*)

​	获取关于帧或回溯对象的信息。 将返回一个 [`Traceback`]({{< ref "/library/python/inspect#inspect.Traceback" >}}) 对象。

> 在 3.11 版本发生变更: 将返回一个 [`Traceback`]({{< ref "/library/python/inspect#inspect.Traceback" >}}) 对象而非具名元组。

## inspect.**getouterframes**(*frame*, *context=1*)

​	获取某个帧及其所有外部帧的 [`FrameInfo`]({{< ref "/library/python/inspect#inspect.FrameInfo" >}}) 对象的列表。 这些帧代表导致 *frame* 被创建的一系列调用。 返回的列中的第一个条目代表 *frame*；最后一个条目代表在 *frame* 的栈上的最外层调用。

> 在 3.5 版本发生变更: 返回一个 [具名元组]({{< ref "/glossary/idx#term-named-tuple" >}}) `FrameInfo(frame, filename, lineno, function, code_context, index)` 的列表。

> 在 3.11 版本发生变更: 将返回一个 [`FrameInfo`]({{< ref "/library/python/inspect#inspect.FrameInfo" >}}) 对象的列表。

## inspect.**getinnerframes**(*traceback*, *context=1*)

​	获取一个回溯所在的帧及其所有内部帧的 [`FrameInfo`]({{< ref "/library/python/inspect#inspect.FrameInfo" >}}) 对象的列表。 这些帧代表作为 *frame* 的后续所执行的调用。 列表中的第一个条目代表 *traceback*；最后一个条目代表引发异常的位置。

> 在 3.5 版本发生变更: 返回一个 [具名元组]({{< ref "/glossary/idx#term-named-tuple" >}}) `FrameInfo(frame, filename, lineno, function, code_context, index)` 的列表。

> 在 3.11 版本发生变更: 将返回一个 [`FrameInfo`]({{< ref "/library/python/inspect#inspect.FrameInfo" >}}) 对象的列表。

## inspect.**currentframe**()

​	返回调用者的栈帧对应的帧对象。

**CPython 实现细节：** 该函数依赖于 Python 解释器对于栈帧的支持，这并非在 Python 的所有实现中被保证。该函数在不支持Python 栈帧的实现中运行会返回 `None`。

## inspect.**stack**(*context=1*)

​	返回调用者的栈的 [`FrameInfo`]({{< ref "/library/python/inspect#inspect.FrameInfo" >}}) 对象的列表。 返回的列表中的第一个条目代表调用者；最后一个条目代表栈上的最外层调用。

> 在 3.5 版本发生变更: 返回一个 [具名元组]({{< ref "/glossary/idx#term-named-tuple" >}}) `FrameInfo(frame, filename, lineno, function, code_context, index)` 的列表。

> 在 3.11 版本发生变更: 将返回一个 [`FrameInfo`]({{< ref "/library/python/inspect#inspect.FrameInfo" >}}) 对象的列表。

## inspect.**trace**(*context=1*)

​	返回介于当前帧和引发了当前正在处理的异常所在的帧之间的栈的 [`FrameInfo`]({{< ref "/library/python/inspect#inspect.FrameInfo" >}}) 对象的列表。 列表中的第一个条目代表调用者；最后一个条目代表引发异常的位置。

> 在 3.5 版本发生变更: 返回一个 [具名元组]({{< ref "/glossary/idx#term-named-tuple" >}}) `FrameInfo(frame, filename, lineno, function, code_context, index)` 的列表。

> 在 3.11 版本发生变更: 将返回一个 [`FrameInfo`]({{< ref "/library/python/inspect#inspect.FrameInfo" >}}) 对象的列表。

## 静态地获取属性

[`getattr()`]({{< ref "/library/functions#getattr" >}}) 和 [`hasattr()`]({{< ref "/library/functions#hasattr" >}}) 在获取或检查属性是否存在时可以触发代码执行。 描述器，像特征属性一样，可能会被发起调用而 [`__getattr__()`]({{< ref "/reference/datamodel#object.__getattr__" >}}) 和 [`__getattribute__()`]({{< ref "/reference/datamodel#object.__getattribute__" >}}) 也可能会被调用。

​	对于你想要静态地内省的情况，比如文档工具，这会显得不方便。 [`getattr_static()`]({{< ref "/library/python/inspect#inspect.getattr_static" >}}) 拥有与 [`getattr()`]({{< ref "/library/functions#getattr" >}}) 相同的签名，但避免了获取属性时执行代码。

## inspect.**getattr_static**(*obj*, *attr*, *default=None*)

​	获取属性而不触发通过描述器协议、[`__getattr__()`]({{< ref "/reference/datamodel#object.__getattr__" >}}) 或 [`__getattribute__()`]({{< ref "/reference/datamodel#object.__getattribute__" >}}) 的动态查找。

​	注意：该函数可能无法获取 getattr 能获取的全部的属性（比如动态地创建的属性），并且可能发现一些 getattr 无法找到的属性（比如描述器会引发 AttributeError）。它也能够返回描述器对象本身而非实例成员。

​	如果实例的 [`__dict__`]({{< ref "/reference/datamodel#object.__dict__" >}}) 被其他成员遮盖（比如一个特性）则该函数无法找到实例成员。

> Added in version 3.2.
>

​	 [`getattr_static()`]({{< ref "/library/python/inspect#inspect.getattr_static" >}}) 不解析描述器。比如槽描述器或 C 语言中实现的 getset 描述器。该描述器对象会被直接返回，而不处理底层属性。

​	你可以用类似下方的代码的方法处理此事。注意，对于任意 getset 描述符，使用这段代码仍可能触发代码执行。

```
# example code for resolving the builtin descriptor types
class _foo:
    __slots__ = ['foo']

slot_descriptor = type(_foo.foo)
getset_descriptor = type(type(open(__file__)).name)
wrapper_descriptor = type(str.__dict__['__add__'])
descriptor_types = (slot_descriptor, getset_descriptor, wrapper_descriptor)

result = getattr_static(some_object, 'foo')
if type(result) in descriptor_types:
    try:
        result = result.__get__()
    except AttributeError:
        # descriptors can raise AttributeError to
        # indicate there is no underlying value
        # in which case the descriptor itself will
        # have to do
        pass
```

## 生成器、协程和异步生成器的当前状态

​	当实现协程调度器或其他更高级的生成器用途时，判断一个生成器是正在执行、等待启动或继续或执行，又或者已经被终止是非常有用的。 [`getgeneratorstate()`]({{< ref "/library/python/inspect#inspect.getgeneratorstate" >}}) 允许方便地判断一个生成器的当前状态。

## inspect.**getgeneratorstate**(*generator*)

​	获取生成器迭代器的当前状态。

​	可能的状态是：

- GEN_CREATED：等待开始执行。
- GEN_RUNNING：正在被解释器执行。
- GEN_SUSPENDED：当前挂起于一个 yield 表达式。
- GEN_CLOSED：执行已经完成。

> Added in version 3.2.
>

## inspect.**getcoroutinestate**(*coroutine*)

​	获取协程对象的当前状态。该函数设计为用于使用 [`async def`]({{< ref "/reference/compound_stmts#async-def" >}}) 函数创建的协程函数，但也能接受任何包括 `cr_running` 和 `cr_frame` 的类似协程的对象。

​	可能的状态是：

- CORO_CREATED：等待开始执行。
- CORO_RUNNING：当前正在被解释器执行。
- CORO_SUSPENDED：当前挂起于一个 await 表达式。
- CORO_CLOSED：执行已经完成。

> Added in version 3.5.
>

## inspect.**getasyncgenstate**(*agen*)

​	获取一个异步生成器对象的当前状态。 该函数设计为用于由 [`async def`]({{< ref "/reference/compound_stmts#async-def" >}}) 函数创建的使用 [`yield`]({{< ref "/reference/simple_stmts#yield" >}}) 语句的异步迭代器对象，但也能接受任何具有 `ag_running` 和 `ag_frame` 属性的类似异步生成器的对象。

​	可能的状态是：

- AGEN_CREATED: 等待开始执行。
- AGEN_RUNNING: 当前正在被解释器执行。
- AGEN_SUSPENDED: 当前在 yield 表达式上挂起。
- AGEN_CLOSED: 执行已经完成。

> Added in version 3.12.
>

​	生成器当前的内部状态也可以被查询。这通常在测试目的中最为有用，来保证内部状态如预期一样被更新：

## inspect.**getgeneratorlocals**(*generator*)

​	获取 *generator* 里的实时局部变量到当前值的映射。返回一个由名字映射到值的字典。这与在生成器的主体内调用 [`locals()`]({{< ref "/library/functions#locals" >}}) 是等效的，并且相同的警告也适用。

​	如果 *generator* 是一个没有关联帧的 [生成器]({{< ref "/glossary/idx#term-generator" >}})，则返回一个空字典。如果 *generator* 不是一个 Python 生成器对象，则引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。

**CPython 实现细节：** 该函数依赖于生成器为内省暴露一个 Python 栈帧，这并非在 Python 的所有实现中被保证。在这种情况下，该函数将永远返回一个空字典。

> Added in version 3.3.
>

## inspect.**getcoroutinelocals**(*coroutine*)

​	该函数可类比于 [`getgeneratorlocals()`]({{< ref "/library/python/inspect#inspect.getgeneratorlocals" >}})，只是作用于由 [`async def`]({{< ref "/reference/compound_stmts#async-def" >}}) 函数创建的协程。

> Added in version 3.5.
>

## inspect.**getasyncgenlocals**(*agen*)

​	此函数类似于 [`getgeneratorlocals()`]({{< ref "/library/python/inspect#inspect.getgeneratorlocals" >}})，但只适用于由 [`async def`]({{< ref "/reference/compound_stmts#async-def" >}}) 函数创建的使用 [`yield`]({{< ref "/reference/simple_stmts#yield" >}}) 语句的异步生成器对象。

> Added in version 3.12.
>



## 代码对象位标志

​	Python 代码对象有一个 [`co_flags`]({{< ref "/reference/datamodel#codeobject.co_flags" >}}) 属性，它是下列旗标的位映射：

## inspect.**CO_OPTIMIZED**

​	代码对象已经经过优化，会采用快速局部变量。

## inspect.**CO_NEWLOCALS**

​	如果设置，则当代码对象被执行时会新建一个字典作为帧的 [`f_locals`]({{< ref "/reference/datamodel#frame.f_locals" >}})。

## inspect.**CO_VARARGS**

​	代码对象拥有一个变长位置形参（类似 `*args`）。

## inspect.**CO_VARKEYWORDS**

​	代码对象拥有一个可变关键字形参 (类似 `**kwrags`)。

## inspect.**CO_NESTED**

​	该标志当代码对象是一个嵌套函数时被置位。

## inspect.**CO_GENERATOR**

​	当代码对象是一个生成器函数，即调用时会返回一个生成器对象，则该标志被置位。

## inspect.**CO_COROUTINE**

​	当代码对象是一个协程函数时被置位。当代码对象被执行时它返回一个协程。详见 [**PEP 492**](https://peps.python.org/pep-0492/)。

> Added in version 3.5.
>

## inspect.**CO_ITERABLE_COROUTINE**

​	该标志被用于将生成器转变为基于生成器的协程。包含此标志的生成器对象可以被用于 `await` 表达式，并可以 `yield from` 协程对象。详见 [**PEP 492**](https://peps.python.org/pep-0492/)。

> Added in version 3.5.
>

## inspect.**CO_ASYNC_GENERATOR**

​	当代码对象是一个异步生成器函数时该标志被置位。当代码对象被运行时它将返回一个异步生成器对象。详见 [**PEP 525**](https://peps.python.org/pep-0525/)。

> Added in version 3.6.
>

​备注
 

​	这些标志特指于 CPython，并且在其他 Python 实现中可能从未被定义。更进一步地说，这些标志是一种实现细节，并且可能在将来的 Python 发行中被移除或弃用。推荐使用 [`inspect`]({{< ref "/library/python/inspect#module-inspect" >}}) 模块的公共 API 来进行任何内省需求。

## 缓冲区旗标

## *class* inspect.**BufferFlags**

​	这是一个代表可被传给实现了 [缓冲区协议](https://docs.python.org/zh-cn/3.13/c-api/buffer.html#bufferobjects) 的对象的 [`__buffer__()`]({{< ref "/reference/datamodel#object.__buffer__" >}}) 方法的旗标的 [`enum.IntFlag`]({{< ref "/library/datatypes/enum#enum.IntFlag" >}})。

​	这些旗标的含义的说明见 [缓冲区请求的类型](https://docs.python.org/zh-cn/3.13/c-api/buffer.html#buffer-request-types)。

## **SIMPLE**

## **WRITABLE**

## **FORMAT**

## **ND**

## **STRIDES**

## **C_CONTIGUOUS**

## **F_CONTIGUOUS**

## **ANY_CONTIGUOUS**

## **INDIRECT**

## **CONTIG**

## **CONTIG_RO**

## **STRIDED**

## **STRIDED_RO**

## **RECORDS**

## **RECORDS_RO**

## **FULL**

## **FULL_RO**

## **READ**

## **WRITE**

> Added in version 3.12.
>



## 命令行界面

[`inspect`]({{< ref "/library/python/inspect#module-inspect" >}}) 模块也提供一个从命令行使用基本的内省能力。

​	默认地，命令行接受一个模块的名字并打印模块的源代码。也可通过后缀一个冒号和目标对象的限定名称来打印一个类或者一个函数。

## `--details`

​	打印特定对象的信息而非源码。
