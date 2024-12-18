+++
title = "4. 执行模型"
date = 2024-11-14T22:01:37+08:00
weight = 30
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/reference/executionmodel.html](https://docs.python.org/zh-cn/3.13/reference/executionmodel.html)
>
> 收录该文档的时间：`2024-11-14T22:01:37+08:00`

# 4. 执行模型



## 4.1. 程序的结构

​	Python 程序是由代码块构成的。 *代码块* 是被作为一个单元来执行的一段 Python 程序文本。 以下几个都属于代码块：模块、函数体和类定义。 交互式输入的每条命令都是代码块。 一个脚本文件（作为标准输入发送给解释器或是作为命令行参数发送给解释器的文件）也是代码块。 一条脚本命令（通过 [`-c`]({{< ref "/using/cmdline#cmdoption-c" >}}) 选项在解释器命令行中指定的命令）也是代码块。 通过在命令行中使用 [`-m`]({{< ref "/using/cmdline#cmdoption-m" >}}) 参数作为最高层级脚本（即 `__main__` 模块）运行的模块也是代码块。 传递给内置函数 [`eval()`]({{< ref "/library/functions#eval" >}}) 和 [`exec()`]({{< ref "/library/functions#exec" >}}) 的字符串参数也是代码块。

​	代码块在 *执行帧* 中被执行。 一个帧会包含某些管理信息（用于调试）并决定代码块执行完成后应前往何处以及如何继续执行。



## 4.2. 命名与绑定



### 4.2.1. 名称的绑定

​	*名称* 用于指代对象。 名称是通过名称绑定操作来引入的。

​	下面的结构将名字绑定:

- 函数的正式参数，
- 类定义，
- 函数定义，
- 赋值表达式,
- 如果在一个赋值中出现，则为标识符的 [目标]({{< ref "/reference/simple_stmts#assignment" >}}) :
  - [`for`]({{< ref "/reference/compound_stmts#for" >}}) 循环头,
  - 在 [`with`]({{< ref "/reference/compound_stmts#with" >}}) 语句, [`except`]({{< ref "/reference/compound_stmts#except" >}}) 子句, [`except*`]({{< ref "/reference/compound_stmts#except-star" >}}) 子句，或格式化模式匹配的 as 模式的 `as` 之后，
  - 在结构模式匹配中的捕获模式
- [`import`]({{< ref "/reference/simple_stmts#import" >}}) 语句。
- [`type`]({{< ref "/reference/simple_stmts#type" >}}) 语句。
- [类型形参列表]({{< ref "/reference/compound_stmts#type-params" >}})。

​	形式为 `from ... import *` 的 `import` 语句绑定所有在导入的模块中定义的名字，除了那些以下划线开头的名字。这种形式只能在模块级别上使用。

[`del`]({{< ref "/reference/simple_stmts#del" >}}) 语句的目标也被视作一种绑定（虽然其实际语义为解除名称绑定）。

​	每条赋值或导入语句均发生于类或函数内部定义的代码块中，或是发生于模块层级（即最高层级的代码块）。

​	如果某个名称绑定在一个代码块中，则它就是该代码块的局部变量，除非声明为 [`nonlocal`]({{< ref "/reference/simple_stmts#nonlocal" >}}) 或 [`global`]({{< ref "/reference/simple_stmts#global" >}})。 如果某个名称绑定在模块层级，则它就是全局变量。 （模块代码块的变量既是局部变量又是全局变量。） 如果某个变量在一个代码块中被使用但不是在其中定义的，则它是 [free variable]({{< ref "/glossary/idx#term-free-variable" >}})。

​	每个在程序文本中出现的名称是指由以下名称解析规则所建立的对该名称的 *绑定*。



### 4.2.2. 名称的解析

​	*作用域* 定义了一个代码块中名称的可见性。 如果代码块中定义了一个局部变量，则其作用域包含该代码块。 如果定义发生于函数代码块中，则其作用域会扩展到该函数所包含的任何代码块，除非有某个被包含代码块引入了对该名称的不同绑定。

​	当一个名称在代码块中被使用时，会由包含它的最近作用域来解析。 对一个代码块可见的所有这种作用域的集合称为该代码块的 *环境*。

​	当一个名称完全找不到时，将会引发 [`NameError`]({{< ref "/library/exceptions#NameError" >}}) 异常。 如果当前作用域为函数作用域，且该名称指向一个局部变量，而此变量在该名称被使用的时候尚未绑定到特定值，将会引发 [`UnboundLocalError`]({{< ref "/library/exceptions#UnboundLocalError" >}}) 异常。 [`UnboundLocalError`]({{< ref "/library/exceptions#UnboundLocalError" >}}) 为 [`NameError`]({{< ref "/library/exceptions#NameError" >}}) 的一个子类。

​	如果一个代码块内的任何位置发生名称绑定操作，则代码块内所有对该名称的使用都会被视为对当前代码块的引用。 当一个名称在其被绑定前就在代码块内被使用时将会导致错误。 这个规则是很微妙的。 Python 缺少声明语法并且允许名称绑定操作发生于代码块内的任何位置。 一个代码块的局部变量可通过在整个代码块文本中扫描名称绑定操作来确定。 请参阅 [UnboundLocalError 的 FAQ 条目]({{< ref "/faq/programming#faq-unboundlocalerror" >}}) 来获取示例。

​	如果 [`global`]({{< ref "/reference/simple_stmts#global" >}}) 语句出现在一个代码块中，则所有对该语句所指定名称的使用都是在最高层级命名空间内对该名称绑定的引用。 名称在最高层级命名空间内的解析是通过搜索全局命名空间，也就是包含该代码块的模块的命名空间，以及内置命名空间即 [`builtins`]({{< ref "/library/python/builtins#module-builtins" >}}) 模块的命名空间。 全局命名空间会先被搜索。 如果未在其中找到相应名称，将再搜索内置命名空间。 如果未在内置命名空间中找到相应名称，将在全局命名空间中创建新变量。 global 语句必须位于所有对其所列名称的使用之前。

​	[`global`]({{< ref "/reference/simple_stmts#global" >}}) 语句与同一代码块中名称绑定具有相同的作用域。 如果一个自由变量的最近包含作用域中有一条 global 语句，则该自由变量也会被当作是全局变量。

​	[`nonlocal`]({{< ref "/reference/simple_stmts#nonlocal" >}}) 语句会使得相应的名称指向之前在最近包含函数作用域中绑定的变量。 如果指定的名称不存在于任何包含函数作用域中则将在编译时引发 [`SyntaxError`]({{< ref "/library/exceptions#SyntaxError" >}})。 [类型形参]({{< ref "/reference/compound_stmts#type-params" >}}) 不能使用 `nonlocal` 语句来重新绑定。

​	模块的作用域会在模块第一次被导入时自动创建。 一个脚本的主模块总是被命名为 [`__main__`]({{< ref "/library/python/__main__#module-__main__" >}})。

​	类定义代码块以及传给 [`exec()`]({{< ref "/library/functions#exec" >}}) 和 [`eval()`]({{< ref "/library/functions#eval" >}}) 的参数是名称解析的上下文中的特殊情况。 类定义是可能使用并定义名称的可执行语句。 这些引用遵循正常的名称解析规则，例外之处在于未绑定的局部变量会在全局命名空间中查找。 类定义的命名空间会成为该类的属性字典。 在类代码块中定义的名称的作用域会被限制在类代码块中；它不会扩展到方法的代码块中。 这包括推导式和生成器表达式，但不包括 [标注作用域]({{< ref "/reference/executionmodel#annotation-scopes" >}})，因为它可以访问所包含的类作用域。 这意味着以下代码将会失败:

```
class A:
    a = 42
    b = list(a + i for i in range(10))
```

​	但是，下面的代码将会成功:

```
class A:
    type Alias = Nested
    class Nested: pass

print(A.Alias.__value__)  # <type 'A.Nested'>
```



### 4.2.3. 标注作用域

​	[类型形参列表]({{< ref "/reference/compound_stmts#type-params" >}}) 和 [`type`]({{< ref "/reference/simple_stmts#type" >}}) 语句引入了 *标注作用域*，其行为很像函数作用域，但具有下述的几处例外。 [标注]({{< ref "/glossary/idx#term-annotation" >}}) 目前没有使用标注作用域，但它们预期会在实现了 [**PEP 649**](https://peps.python.org/pep-0649/) 的 Python 3.13 中使用标注作用域。

​	标注作用域将在下列情况中使用:

- 针对 [泛型类型别名]({{< ref "/reference/compound_stmts#generic-type-aliases" >}}) 的类型形参列表。
- 针对 [泛型函数]({{< ref "/reference/compound_stmts#generic-functions" >}}) 的类型形参列表。 泛型函数的标注会在标注作用域内执行，但其默认值和装饰器则不会。
- 针对 [泛型类]({{< ref "/reference/compound_stmts#generic-classes" >}}) 的类型形参列表。 泛型类的基类和关键字参数会在标注作用域内执行，但其装饰器则不会。
- 针对类型形参的绑定、约束和默认值 ([惰性求值]({{< ref "/reference/executionmodel#lazy-evaluation" >}}))。
- 类型别名的值 ([惰性求值]({{< ref "/reference/executionmodel#lazy-evaluation" >}}))。

​	标注作用域在以下几个方面不同于函数作用域:

- 标注作用域能够访问其所包含的类命名空间。 如果某个标注作用域紧接在一个类作用域之内，或是位于紧接一个类作用域的另一个标注作用域之内，则该标注作用域中的代码将能使用在该类作用域中定义的名称，就像它是在该类内部直接执行一样。 这不同于在类中定义的常规函数，后者无法访问在类作用域中定义的名称。
- 标注作用域中的表达式不能包含 [`yield`]({{< ref "/reference/simple_stmts#yield" >}}), `yield from`, [`await`]({{< ref "/reference/expressions#await" >}}) 或 [`:=`]({{< ref "/reference/expressions#grammar-token-python-grammar-assignment_expression" >}}) 表达式。 （这些表达式在包含于标注作用域之内的其他作用域中则是允许的。）
- 在标注作用域中定义的名称不能在内部作用域中通过 [`nonlocal`]({{< ref "/reference/simple_stmts#nonlocal" >}}) 语句来重新绑定。 这只包括类型形参，因为没有其他可以在标注作用域内部出现的语法元素能够引入新的名称。
- 虽然标注作用域具有一个内部名称，但该名称不会反映在作用域内定义的对象的 [qualified name]({{< ref "/glossary/idx#term-qualified-name" >}}) 中。 相反，这些对象的 [`__qualname__`]({{< ref "/library/stdtypes#definition.__qualname__" >}}) 就像它们是定义在包含作用域中的对象一样。

> Added in version 3.12:* 标注作用域是在 Python 3.12 中作为 [**PEP 695*](https://peps.python.org/pep-0695/) 的一部分引入的。

> 在 3.13 版本发生变更: 标注作用域也被用于类型形参默认值，这是由 [**PEP 696**](https://peps.python.org/pep-0696/) 引入的。



### 4.2.4. 惰性求值

​	通过 [`type`]({{< ref "/reference/simple_stmts#type" >}}) 语句创建的类型别名的值将被 *惰性求值*。 此特性也适用于通过 [类型形参语法]({{< ref "/reference/compound_stmts#type-params" >}}) 创建的类型变量的绑定、约束和默认值。 这意味着它们在创建类型别名或类型变量时不会被求值。 相反，它们只有在需要处理属性访问时才会被求值。

​	示例:



``` python
>>> type Alias = 1/0
>>> Alias.__value__
Traceback (most recent call last):
  ...
ZeroDivisionError: division by zero
>>> def func[T: 1/0](): pass
>>> T = func.__type_params__[0]
>>> T.__bound__
Traceback (most recent call last):
  ...
ZeroDivisionError: division by zero
```

​	此处的异常只有在类型别名的 `__value__` 属性或类型变量的 `__bound__` 属性被访问时才会被引发。

​	此行为主要适用于当创建类型别名或类型变量时对尚未被定义的类型进行引用。 例如，惰性求值将允许创建相互递归的类型别名:

```
from typing import Literal

type SimpleExpr = int | Parenthesized
type Parenthesized = tuple[Literal["("], Expr, Literal[")"]]
type Expr = SimpleExpr | tuple[SimpleExpr, Literal["+", "-"], Expr]
```

​	被惰性求值的值是在 [标记作用域]({{< ref "/reference/executionmodel#annotation-scopes" >}}) 内进行求值的，这意味着出现在被惰性求值的值内部的名称的查找范围就相当于它们是在紧邻的作用域中被使用。

> Added in version 3.12.
>



### 4.2.5. 内置命名空间和受限的执行

**CPython 实现细节：** 用户不应该接触 `__builtins__`，严格说来它属于实现细节。 用户如果要重载内置命名空间中的值则应该 [`import`]({{< ref "/reference/simple_stmts#import" >}}) [`builtins`]({{< ref "/library/python/builtins#module-builtins" >}}) 并相应地修改该模块中的属性。

​	与一个代码块的执行相关联的内置命名空间实际上是通过在其全局命名空间中搜索名称 `__builtins__` 来找到的；这应该是一个字典或一个模块（在后一种情况下会使用该模块的字典）。 默认情况下，当在 [`__main__`]({{< ref "/library/python/__main__#module-__main__" >}}) 模块中时，`__builtins__` 就是内置模块 [`builtins`]({{< ref "/library/python/builtins#module-builtins" >}})；当在任何其他模块中时，`__builtins__` 则是 [`builtins`]({{< ref "/library/python/builtins#module-builtins" >}}) 模块自身的字典的一个别名。



### 4.2.6. 与动态特性的交互

​	自由变量的名称解析发生于运行时而不是编译时。 这意味着以下代码将打印出 42:

```
i = 10
def f():
    print(i)
i = 42
f()
```

​	[`eval()`]({{< ref "/library/functions#eval" >}}) 和 [`exec()`]({{< ref "/library/functions#exec" >}}) 函数没有对完整环境的访问权限来解析名称。 名称可以在调用者的局部和全局命名空间中被解析。 自由变量的解析不是在最近包含命名空间中，而是在全局命名空间中。 [[1\]]({{< ref "/reference/executionmodel#id5" >}}) [`exec()`]({{< ref "/library/functions#exec" >}}) 和 [`eval()`]({{< ref "/library/functions#eval" >}}) 函数有可选参数用来重载全局和局部命名空间。 如果只指定一个命名空间，则它会同时作用于两者。



## 4.3. 异常

​	异常是中断代码块的正常控制流程以便处理错误或其他异常条件的一种方式。 异常会在错误被检测到的位置 *引发*，它可以被当前包围代码块或是任何直接或间接发起调用发生错误的代码块的其他代码块所 *处理*。

​	Python 解析器会在检测到运行时错误（例如零作为被除数）的时候引发异常。 Python 程序也可以通过 [`raise`]({{< ref "/reference/simple_stmts#raise" >}}) 语句显式地引发异常。 异常处理是通过 [`try`]({{< ref "/reference/compound_stmts#try" >}}) ... [`except`]({{< ref "/reference/compound_stmts#except" >}}) 语句来指定的。 该语句的 [`finally`]({{< ref "/reference/compound_stmts#finally" >}}) 子句可被用来指定清理代码，它并不处理异常，而是无论之前的代码是否发生异常都会被执行。

​	Python 的错误处理采用的是“终止”模型：异常处理器可以找出发生了什么问题，并在外层继续执行，但它不能修复错误的根源并重试失败的操作（除非通过从顶层重新进入出错的代码片段）。

​	当一个异常完全未被处理时，解释器会终止程序的执行，或者返回交互模式的主循环。 无论是哪种情况，它都会打印栈回溯信息，除非是当异常为 [`SystemExit`]({{< ref "/library/exceptions#SystemExit" >}}) 的时候。

​	异常是通过类实例来标识的。 [`except`]({{< ref "/reference/compound_stmts#except" >}}) 子句会依据实例的类来选择：它必须引用实例的类或是其所属的 [非虚基类]({{< ref "/glossary/idx#term-abstract-base-class" >}}) 。 实例可通过处理器被接收，并可携带有关异常条件的附加信息。

备注


​	异常消息不是 Python API 的组成部分。 其内容可能在 Python 升级到新版本时不经警告地发生改变，不应该被需要在多版本解释器中运行的代码所依赖。

​	另请参看 [try 语句]({{< ref "/reference/compound_stmts#try" >}}) 小节中对 [`try`]({{< ref "/reference/compound_stmts#try" >}}) 语句的描述以及 [raise 语句]({{< ref "/reference/simple_stmts#raise" >}}) 小节中对 [`raise`]({{< ref "/reference/simple_stmts#raise" >}}) 语句的描述。

备注
[[1]({{< ref "/reference/executionmodel#id3" >}})]

​	出现这样的限制是由于通过这些操作执行的代码在模块被编译的时候并不可用。
