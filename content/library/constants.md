+++
title = "内置常量"
date = 2024-11-14T22:00:25+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/constants.html](https://docs.python.org/zh-cn/3.13/library/constants.html)
>
> 收录该文档的时间：`2024-11-14T22:00:25+08:00`

# 内置常量

​	有少数的常量存在于内置命名空间中。 它们是：

## **False**

[`bool`]({{< ref "/library/functions#bool" >}}) 类型的假值。 给 `False` 赋值是非法的并会引发 [`SyntaxError`]({{< ref "/library/exceptions#SyntaxError" >}})。

## **True**

[`bool`]({{< ref "/library/functions#bool" >}}) 类型的真值。 给 `True` 赋值是非法的并会引发 [`SyntaxError`]({{< ref "/library/exceptions#SyntaxError" >}})。

## **None**

​	通常被用来代表空值的对象，例如未向某个函数传入默认参数时。 向 `None` 赋值是非法的并会引发 [`SyntaxError`]({{< ref "/library/exceptions#SyntaxError" >}})。 `None` 是 [`NoneType`]({{< ref "/library/datatypes/types#types.NoneType" >}}) 类型的唯一实例。

## **NotImplemented**

​	一个应当由双目运算特殊方法（如 [`__eq__()`]({{< ref "/reference/datamodel#object.__eq__" >}}), [`__lt__()`]({{< ref "/reference/datamodel#object.__lt__" >}}), [`__add__()`]({{< ref "/reference/datamodel#object.__add__" >}}), [`__rsub__()`]({{< ref "/reference/datamodel#object.__rsub__" >}}) 等）返回的特殊值，用来表明该运算没有针对其他类型的实现；也可由原地双目运算特殊方法（如 [`__imul__()`]({{< ref "/reference/datamodel#object.__imul__" >}}), [`__iand__()`]({{< ref "/reference/datamodel#object.__iand__" >}}) 等）出于同样的目的而返回。 它不应在布尔上下文中被求值。 `NotImplemented` 是 [`types.NotImplementedType`]({{< ref "/library/datatypes/types#types.NotImplementedType" >}}) 类型的唯一实例。

​备注
 

​	当一个双目（或原地）方法返回 `NotImplemented` 时解释器将尝试对另一种类型（或其他回退操作，具体取决于所用的运算符）的反射操作。 如果所有尝试都返回 `NotImplemented`，解释器将引发适当的异常。 错误地返回 `NotImplemented` 将导致误导性的错误消息或 `NotImplemented` 值被返回给 Python 代码。

​	参见 [实现算术运算]({{< ref "/library/numeric/numbers#implementing-the-arithmetic-operations" >}}) 为例。

​备注
 

`NotImplementedError` 和 `NotImplemented` 不可相互替代，即使它们有相似的名称和用途。 请参阅 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}}) 了解其使用细节。

> 在 3.9 版本发生变更: 在布尔上下文件中对 `NotImplemented` 求值的操作已被弃用。 虽然它目前会被求解为真值，但将同时发出 [`DeprecationWarning`]({{< ref "/library/exceptions#DeprecationWarning" >}})。 它将在未来的 Python 版本中引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。

## **Ellipsis**

​	与省略号字面值 "`...`" 相同。 该特殊值主要是与用户定义的容器数据类型的扩展切片语法结合使用。 `Ellipsis` 是 [`types.EllipsisType`]({{< ref "/library/datatypes/types#types.EllipsisType" >}}) 类型的唯一实例。

## `__debug__`

​	如果 Python 没有以 [`-O`]({{< ref "/using/cmdline#cmdoption-O" >}}) 选项启动，则此常量为真值。 另请参见 [`assert`]({{< ref "/reference/simple_stmts#assert" >}}) 语句。

​备注
 

​	变量名 [`None`]({{< ref "/library/constants#None" >}})，[`False`]({{< ref "/library/constants#False" >}})，[`True`]({{< ref "/library/constants#True" >}}) 和 `__ debug__` 无法重新赋值（赋值给它们，即使是属性名，将引发 [`SyntaxError`]({{< ref "/library/exceptions#SyntaxError" >}}) ），所以它们可以被认为是“真正的”常数。



## 由 [`site`]({{< ref "/library/python/site#module-site" >}}) 模块添加的常量

[`site`]({{< ref "/library/python/site#module-site" >}}) 模块（在启动期间自动导入，除非给出 [`-S`]({{< ref "/using/cmdline#cmdoption-S" >}}) 命令行选项）将几个常量添加到内置命名空间。 它们对交互式解释器 shell 很有用，并且不应在程序中使用。

### **quit**(*code=None*)

### **exit**(*code=None*)

​	当打印此对象时，会打印出一条消息，例如“Use quit() or Ctrl-D (i.e. EOF) to exit”，当调用此对象时，将使用指定的退出代码来引发 [`SystemExit`]({{< ref "/library/exceptions#SystemExit" >}})。

### **help**

​	该对象被打印时，将打印消息 "Type help() for interactive help, or help(object) for help about object."，并且当被调用时，其行为将如 [`别处`]({{< ref "/library/functions#help" >}}) 所描述的一样。

### **copyright**

## **credits**

​	打印或调用的对象分别打印版权或作者的文本。

### **license**

​	当打印此对象时，会打印出一条消息“Type license() to see the full license text”，当调用此对象时，将以分页形式显示完整的许可证文本（每次显示一屏）。
