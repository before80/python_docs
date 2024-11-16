+++
title = "symtable --- 访问编译器的符号表"
date = 2024-11-15T21:23:55+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/symtable.html](https://docs.python.org/zh-cn/3.13/library/symtable.html)
>
> 收录该文档的时间：`2024-11-15T21:23:55+08:00`

# `symtable` --- 访问编译器的符号表

**Source code：** [Lib/symtable.py](https://github.com/python/cpython/tree/3.13/Lib/symtable.py)

------

​	符号表由编译器在生成字节码之前根据 AST 生成。符号表负责计算代码中每个标识符的作用域。 [`symtable`]({{< ref "/library/language/symtable#module-symtable" >}}) 提供了一个查看这些表的接口。

## 符号表的生成

## symtable.**symtable**(*code*, *filename*, *compile_type*)

​	返回 Python 源 *代码* 顶层的 [`SymbolTable`]({{< ref "/library/language/symtable#symtable.SymbolTable" >}})。*filename* 是代码文件名。 *compile_type* 的含义类似 [`compile()`]({{< ref "/library/functions#compile" >}}) 的 *mode* 参数。

## 符号表的查看

## *class* symtable.**SymbolTableType**

​	一个指明 [`SymbolTable`]({{< ref "/library/language/symtable#symtable.SymbolTable" >}}) 对象的类型的枚举。

## **MODULE** *= "module"*

​	用于模块的符号表。

## **FUNCTION** *= "function"*

​	用于函数的符号表。

## **CLASS** *= "class"*

​	用于类的符号表。

​	以下成员指向不同风格的 [标注作用域]({{< ref "/reference/executionmodel#annotation-scopes" >}})。

## **ANNOTATION** *= "annotation"*

​	当 `from __future__ import annotations` 被激活时用于标注。

## **TYPE_ALIAS** *= "type alias"*

​	用于 [`type`]({{< ref "/reference/simple_stmts#type" >}}) 构造的符号表。

## **TYPE_PARAMETERS** *= "type parameters"*

​	用于 [泛型函数]({{< ref "/reference/compound_stmts#generic-functions" >}}) 或 [泛型类]({{< ref "/reference/compound_stmts#generic-classes" >}}) 的符号表。

## **TYPE_VARIABLE** *= "type variable"*

​	用于正式意义下的绑定、约束元组或单个类型变量的默认值的符号变量的符号表，即 TypeVar, TypeVarTuple 或 ParamSpec 对象（后两者不支持绑定或约束元组）。

> Added in version 3.13.
>

## *class* symtable.**SymbolTable**

​	某个代码块的命名空间表。构造函数不公开。

## **get_type**()

​	返回符号表的类型。 可能的值为 [`SymbolTableType`]({{< ref "/library/language/symtable#symtable.SymbolTableType" >}}) 枚举的成员。

> 在 3.12 版本发生变更: 增加 `'annotation'`, `'TypeVar bound'`, `'type alias'` 和 `'type parameter'` 作为可能的返回值。

> 在 3.13 版本发生变更: 返回值为 [`SymbolTableType`]({{< ref "/library/language/symtable#symtable.SymbolTableType" >}}) 枚举的成员。

​	返回字符串的实际值可能在未来发生变化，因此，建议使用 [`SymbolTableType`]({{< ref "/library/language/symtable#symtable.SymbolTableType" >}}) 成员而不是硬编码的字符串。

## **get_id**()

​	返回符号表的标识符

## **get_name**()

​	返回表名称。 如果表是针对类的则为类名；如果是针对函数的则为函数名；或者如果表是全局的 ([`get_type()`]({{< ref "/library/language/symtable#symtable.SymbolTable.get_type" >}}) 返回 `'module'`) 则为 `'top'`。 对于类型形参作用域 (用于泛型类、函数和类型别名)，它将为底层类、函数或类型别名的名称。 对于类型别名作用域，它将为类型别名的名称。 对于 [`TypeVar`]({{< ref "/library/development/typing#typing.TypeVar" >}}) 绑定作用域，它将为 `TypeVar` 的名称。

## **get_lineno**()

​	返回符号表所代表代码块的第一行编号。

## **is_optimized**()

​	如果符号表中的局部变量可能被优化过，则返回 `True`。

## **is_nested**()

​	如果代码块是嵌套类或函数，则返回 `True`。

## **has_children**()

​	如果代码块中有嵌套的命名空间，则返回 `True`。可通过 [`get_children()`]({{< ref "/library/language/symtable#symtable.SymbolTable.get_children" >}}) 读取。

## **get_identifiers**()

​	返回一个包含表中符号名称的视图对象。 参见 [视图对象文档]({{< ref "/library/stdtypes#dict-views" >}})。

## **lookup**(*name*)

​	在符号表中查找 *name* 并返回一个 [`Symbol`]({{< ref "/library/language/symtable#symtable.Symbol" >}}) 实例。

## **get_symbols**()

​	返回符号表中所有符号的 [`Symbol`]({{< ref "/library/language/symtable#symtable.Symbol" >}}) 实例的列表。

## **get_children**()

​	返回嵌套符号表的列表。

## *class* symtable.**Function**

​	函数或方法的命名空间。 该类继承自 [`SymbolTable`]({{< ref "/library/language/symtable#symtable.SymbolTable" >}})。

## **get_parameters**()

​	返回由函数的参数名组成的元组。

## **get_locals**()

​	返回函数中局部变量名组成的元组。

## **get_globals**()

​	返回函数中全局变量名组成的元组。

## **get_nonlocals**()

​	返回一个包含在此函数中显式声明的非局部变量名称的元组。

## **get_frees**()

​	返回一个包含在此函数中的 [自由（闭包）变量]({{< ref "/glossary/idx#term-closure-variable" >}}) 名称的元组。

## *class* symtable.**Class**

​	类的命名空间。 该类继承自 [`SymbolTable`]({{< ref "/library/language/symtable#symtable.SymbolTable" >}})。

## **get_methods**()

​	返回一个包含类中声明的方法型函数的名称的元组。

​	在这里，术语 '方法' 是指 *任何* 在 class 语句体中通过 [`def`]({{< ref "/reference/compound_stmts#def" >}}) 或 [`async def`]({{< ref "/reference/compound_stmts#async-def" >}}) 定义的函数。

​	在更深的作用域（例如内部类）中定义的函数不会被 [`get_methods()`]({{< ref "/library/language/symtable#symtable.Class.get_methods" >}}) 所获取。

​	例如:



``` python
>>> import symtable
>>> st = symtable.symtable('''
... def outer(): pass
...
... class A:
...    def f():
...        def w(): pass
...
...    def g(self): pass
...
...    @classmethod
...    async def h(cls): pass
...
...    global outer
...    def outer(self): pass
... ''', 'test', 'exec')
>>> class_A = st.get_children()[1]
>>> class_A.get_methods()
('f', 'g', 'h')
```

​	虽然 `A().f()` 在运行时会引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})，但 `A.f` 仍然被视为是方法型函数。

## *class* symtable.**Symbol**

[`SymbolTable`]({{< ref "/library/language/symtable#symtable.SymbolTable" >}}) 中的数据项，对应于源码中的某个标识符。构造函数不公开。

## **get_name**()

​	返回符号名

## **is_referenced**()

​	如果符号在代码块中被引用了，则返回 `True`。

## **is_imported**()

​	如果符号是由导入语句创建的，则返回 `True`。

## **is_parameter**()

​	如果符号是参数，返回 `True`。

## **is_global**()

​	如果符号是全局变量，则返回 `True`。

## **is_nonlocal**()

​	如果符号为非局部变量，则返回 `True`。

## **is_declared_global**()

​	如果符号用 global 声明为全局变量，则返回 `True`。

## **is_local**()

​	如果符号是代码块内的局部变量，则返回 `True`。

## **is_annotated**()

​	如果符号带有注解，则返回 `True`。

> Added in version 3.6.
>

## **is_free**()

​	如果符号在代码块中被引用，但未赋值，则返回 `True`。

## **is_assigned**()

​	如果符号在代码块中赋值，则返回 `True`。

## **is_namespace**()

​	如果符号名绑定引入了新的命名空间，则返回 `True`。

​	如果符号名用于函数或类定义语句，则为 True。

​	例如：



``` python
>>> table = symtable.symtable("def some_func(): pass", "string", "exec")
>>> table.lookup("some_func").is_namespace()
True
```

​	注意，一个符号名可以与多个对象绑定。如果结果为 `True`，则该符号名还可以绑定到其他对象上，比如 int 或 list ，且不会引入新的命名空间。

## **get_namespaces**()

​	返回与符号名绑定的命名空间的列表。

## **get_namespace**()

​	返回绑定到这个名称的命名空间。 如果有多个命名空间或没有命名空间被绑定到这个名称，则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。



## 命令行用法

> Added in version 3.13.
>

[`symtable`]({{< ref "/library/language/symtable#module-symtable" >}}) 模块可以在命令行下作为脚本来执行。

```
python -m symtable [infile...]
```

​	符号表将针对指定的 Python 文件生成并转储至 stdout。 如果未指定输入文件，将从 stdin 读取内容。
