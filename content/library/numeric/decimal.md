+++
title = "decimal --- 十进制定点和浮点算术"
date = 2024-11-15T11:42:26+08:00
weight = 30
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/decimal.html](https://docs.python.org/zh-cn/3.13/library/decimal.html)
>
> 收录该文档的时间：`2024-11-15T11:42:26+08:00`

# `decimal` --- 十进制定点和浮点算术

**源码：** [Lib/decimal.py](https://github.com/python/cpython/tree/3.13/Lib/decimal.py)

------

[`decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#module-decimal) 模块提供了对快速且正确舍入的十进制浮点运算的支持。 与 [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float) 数据类型相比它具有以下优势:

- Decimal 类型的“设计是基于考虑人类习惯的浮点数模型，并且因此具有以下最高指导原则 —— 计算机必须提供与人们在学校所学习的算术相一致的算术。” —— 摘自 decimal 算术规范描述。

- Decimal 数字可以完全精确地表示。 相比之下，`1.1` 和 `2.2` 这样的数字在二进制浮点形式下没有精确的表示。 最终用户通常不希望 `1.1 + 2.2` 像在二进制浮点形式下那样被显示为 `3.3000000000000003`。

- 这样的精确性会延续到算术运算中。 对于 decimal 浮点数，`0.1 + 0.1 + 0.1 - 0.3` 会精确地等于零。 而对于二进制浮点数，结果则为 `5.5511151231257827e-017`。 虽然接近于零，但其中的误差将妨碍到可靠的相等性检测并且这样的误差还会不断累积。 因此，decimal 更适合具有严格相等不变性要求的会计类应用。

- decimal 模块包含有效位的概念因而使得 `1.30 + 1.20` 等于 `2.50`。 末尾的零会被保留以表明有效位。 这是货币相关应用的惯例表示方式。 对于乘法，则按“教科书”方式来使用被乘数中的所有数位。 例如，`1.3 * 1.2` 结果为 `1.56` 而 `1.30 * 1.20` 结果为 `1.5600`。

- 与基于硬件的二进制浮点不同，十进制模块具有用户可更改的精度（默认为28位），可以与给定问题所需的一样大：

  

  ```
  >>> from decimal import *
  >>> getcontext().prec = 6
  >>> Decimal(1) / Decimal(7)
  Decimal('0.142857')
  >>> getcontext().prec = 28
  >>> Decimal(1) / Decimal(7)
  Decimal('0.1428571428571428571428571429')
  ```

- 二进制和 decimal 浮点数都是根据已发布的标准实现的。 虽然内置浮点类型只公开其功能的一小部分，但 decimal 模块公开了标准的所有必需部分。 在需要时，程序员可以完全控制舍入和信号处理。 这包括通过使用异常来阻止任何不精确操作来强制执行精确算术的选项。

- decimal 模块旨在支持“无偏差，精确无舍入的十进制算术（有时称为定点数算术）和有舍入的浮点数算术”。 —— 摘自 decimal 算术规范说明。

​	该模块的设计以三个概念为中心：decimal 数值，算术上下文和信号。

​	decimal 数值属于不可变对象。 它由一个符号、一个系数值及一个指数值组成。 为了保留有效位，系数值不会截去末尾的零。 decimal 数值还包括特殊值如 `Infinity`, `-Infinity` 和 `NaN`。 该标准还会区分 `-0` 和 `+0`。

​	算术的上下文是指定精度、舍入规则、指数限制、指示操作结果的标志以及确定符号是否被视为异常的陷阱启用器的环境。 舍入选项包括 [`ROUND_CEILING`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.ROUND_CEILING) 、 [`ROUND_DOWN`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.ROUND_DOWN) 、 [`ROUND_FLOOR`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.ROUND_FLOOR) 、 [`ROUND_HALF_DOWN`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.ROUND_HALF_DOWN), [`ROUND_HALF_EVEN`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.ROUND_HALF_EVEN) 、 [`ROUND_HALF_UP`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.ROUND_HALF_UP) 、 [`ROUND_UP`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.ROUND_UP) 以及 [`ROUND_05UP`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.ROUND_05UP).

​	信号是在计算过程中出现的异常条件组。 根据应用程序的需要，信号可能会被忽略，被视为信息，或被视为异常。 十进制模块中的信号有：[`Clamped`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Clamped) 、 [`InvalidOperation`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.InvalidOperation) 、 [`DivisionByZero`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.DivisionByZero) 、 [`Inexact`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Inexact) 、 [`Rounded`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Rounded) 、 [`Subnormal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Subnormal) 、 [`Overflow`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Overflow) 、 [`Underflow`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Underflow) 以及 [`FloatOperation`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.FloatOperation) 。

​	对于每个信号，都有一个标志和一个陷阱启动器。 遇到信号时，其标志设置为 1 ，然后，如果陷阱启用器设置为 1 ，则引发异常。 标志是粘性的，因此用户需要在监控计算之前重置它们。

​	参见

- IBM 的通用十进制算术规范描述，[The General Decimal Arithmetic Specification](https://speleotrove.com/decimal/decarith.html)。



## 快速入门教程

​	通常使用 decimal 的方式是先导入该模块，通过 [`getcontext()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.getcontext) 查看当前上下文，并在必要时为精度、舍入或启用的陷阱设置新值:



``` python
>>> from decimal import *
>>> getcontext()
Context(prec=28, rounding=ROUND_HALF_EVEN, Emin=-999999, Emax=999999,
        capitals=1, clamp=0, flags=[], traps=[Overflow, DivisionByZero,
        InvalidOperation])

>>> getcontext().prec = 7       # 设置新的精度
```

​	Decimal 实例可以基于整数、字符串、浮点数或元组来构建。 基于整数或浮点数进行构建将执行该整数或浮点数值的精确转换。 Decimal 数字包括特殊值如代表“非数字”的 `NaN`，正的和负的 `Infinity` 以及 `-0`:



``` python
>>> getcontext().prec = 28
>>> Decimal(10)
Decimal('10')
>>> Decimal('3.14')
Decimal('3.14')
>>> Decimal(3.14)
Decimal('3.140000000000000124344978758017532527446746826171875')
>>> Decimal((0, (3, 1, 4), -2))
Decimal('3.14')
>>> Decimal(str(2.0 ** 0.5))
Decimal('1.4142135623730951')
>>> Decimal(2) ** Decimal('0.5')
Decimal('1.414213562373095048801688724')
>>> Decimal('NaN')
Decimal('NaN')
>>> Decimal('-Infinity')
Decimal('-Infinity')
```

​	如果 [`FloatOperation`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.FloatOperation) 信号被捕获，构造函数中的小数和浮点数的意外混合或排序比较会引发异常



``` python
>>> c = getcontext()
>>> c.traps[FloatOperation] = True
>>> Decimal(3.14)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
decimal.FloatOperation: [<class 'decimal.FloatOperation'>]
>>> Decimal('3.5') < 3.7
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
decimal.FloatOperation: [<class 'decimal.FloatOperation'>]
>>> Decimal('3.5') == 3.5
True
```

> Added in version 3.3.
>

​	新 Decimal 的重要性仅由输入的位数决定。 上下文精度和舍入仅在算术运算期间发挥作用。



``` python
>>> getcontext().prec = 6
>>> Decimal('3.0')
Decimal('3.0')
>>> Decimal('3.1415926535')
Decimal('3.1415926535')
>>> Decimal('3.1415926535') + Decimal('2.7182818285')
Decimal('5.85987')
>>> getcontext().rounding = ROUND_UP
>>> Decimal('3.1415926535') + Decimal('2.7182818285')
Decimal('5.85988')
```

​	如果超出了 C 版本的内部限制，则构造一个 decimal 将引发 [`InvalidOperation`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.InvalidOperation)



``` python
>>> Decimal("1e9999999999999999999")
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
decimal.InvalidOperation: [<class 'decimal.InvalidOperation'>]
```

*在 3.3 版本发生变更.*

​	Decimal 能很好地与 Python 的其余部分交互。 以下是一个小小的 decimal 浮点数飞行马戏团：



``` python
>>> data = list(map(Decimal, '1.34 1.87 3.45 2.35 1.00 0.03 9.25'.split()))
>>> max(data)
Decimal('9.25')
>>> min(data)
Decimal('0.03')
>>> sorted(data)
[Decimal('0.03'), Decimal('1.00'), Decimal('1.34'), Decimal('1.87'),
 Decimal('2.35'), Decimal('3.45'), Decimal('9.25')]
>>> sum(data)
Decimal('19.29')
>>> a,b,c = data[:3]
>>> str(a)
'1.34'
>>> float(a)
1.34
>>> round(a, 1)
Decimal('1.3')
>>> int(a)
1
>>> a * 5
Decimal('6.70')
>>> a * b
Decimal('2.5058')
>>> c % a
Decimal('0.77')
```

​	Decimal 也可以使用一些数学函数：



``` python
>>> getcontext().prec = 28
>>> Decimal(2).sqrt()
Decimal('1.414213562373095048801688724')
>>> Decimal(1).exp()
Decimal('2.718281828459045235360287471')
>>> Decimal('10').ln()
Decimal('2.302585092994045684017991455')
>>> Decimal('10').log10()
Decimal('1')
```

[`quantize()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.quantize) 方法将舍入为固定的指数。 此方法对于将结果舍入到固定位置的货币应用程序来说很有用处:



``` python
>>> Decimal('7.325').quantize(Decimal('.01'), rounding=ROUND_DOWN)
Decimal('7.32')
>>> Decimal('7.325').quantize(Decimal('1.'), rounding=ROUND_UP)
Decimal('8')
```

​	如上所示，[`getcontext()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.getcontext) 函数访问当前上下文并允许更改设置。 这种方法满足大多数应用程序的需求。

​	对于更高级的工作，使用 Context() 构造函数创建备用上下文可能很有用。 要使用备用活动，请使用 [`setcontext()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.setcontext) 函数。

​	根据标准，[`decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#module-decimal) 模块提供了两个现成的标准上下文 [`BasicContext`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.BasicContext) 和 [`ExtendedContext`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.ExtendedContext) 。 前者对调试特别有用，因为许多陷阱都已启用：



``` python
>>> myothercontext = Context(prec=60, rounding=ROUND_HALF_DOWN)
>>> setcontext(myothercontext)
>>> Decimal(1) / Decimal(7)
Decimal('0.142857142857142857142857142857142857142857142857142857142857')

>>> ExtendedContext
Context(prec=9, rounding=ROUND_HALF_EVEN, Emin=-999999, Emax=999999,
        capitals=1, clamp=0, flags=[], traps=[])
>>> setcontext(ExtendedContext)
>>> Decimal(1) / Decimal(7)
Decimal('0.142857143')
>>> Decimal(42) / Decimal(0)
Decimal('Infinity')

>>> setcontext(BasicContext)
>>> Decimal(42) / Decimal(0)
Traceback (most recent call last):
  File "<pyshell#143>", line 1, in -toplevel-
    Decimal(42) / Decimal(0)
DivisionByZero: x / 0
```

​	上下文还具有用于监视计算期间遇到的异常情况的信号旗标。 这些旗标将保持设置直到被显式地清除，因此最好是通过使用 [`clear_flags()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Context.clear_flags) 方法来清除每组受监控的计算之前的旗标。



``` python
>>> setcontext(ExtendedContext)
>>> getcontext().clear_flags()
>>> Decimal(355) / Decimal(113)
Decimal('3.14159292')
>>> getcontext()
Context(prec=9, rounding=ROUND_HALF_EVEN, Emin=-999999, Emax=999999,
        capitals=1, clamp=0, flags=[Inexact, Rounded], traps=[])
```

*flags* 条目显示对 pi 的有理逼近被舍入（超出上下文精度的数字会被丢弃）并且结果是不精确的（某些被丢弃的数字为非零值）。

​	单个陷阱是使用上下文的 `traps` 属性中的字典来设置的:



``` python
>>> setcontext(ExtendedContext)
>>> Decimal(1) / Decimal(0)
Decimal('Infinity')
>>> getcontext().traps[DivisionByZero] = 1
>>> Decimal(1) / Decimal(0)
Traceback (most recent call last):
  File "<pyshell#112>", line 1, in -toplevel-
    Decimal(1) / Decimal(0)
DivisionByZero: x / 0
```

​	大多数程序仅在程序开始时调整当前上下文一次。 并且，在许多应用程序中，数据在循环内单个强制转换为 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 。 通过创建上下文集和小数，程序的大部分操作数据与其他 Python 数字类型没有区别。



## Decimal 对象

## *class* decimal.**Decimal**(*value='0'*, *context=None*)

​	根据 *value* 构造一个新的 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 对象。

*value* 可以是整数，字符串，元组，[`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float) ，或另一个 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 对象。 如果没有给出 *value*，则返回 `Decimal('0')`。 如果 *value* 是一个字符串，它应该在前导和尾随空格字符以及下划线被删除之后符合十进制数字字符串语法:

```
sign           ::=  '+' | '-'
digit          ::=  '0' | '1' | '2' | '3' | '4' | '5' | '6' | '7' | '8' | '9'
indicator      ::=  'e' | 'E'
digits         ::=  digit [digit]...
decimal-part   ::=  digits '.' [digits] | ['.'] digits
exponent-part  ::=  indicator [sign] digits
infinity       ::=  'Infinity' | 'Inf'
nan            ::=  'NaN' [digits] | 'sNaN' [digits]
numeric-value  ::=  decimal-part [exponent-part] | infinity
numeric-string ::=  [sign] numeric-value | [sign] nan
```

​	当上面出现 `digit` 时也允许其他十进制数码。 其中包括来自各种其他语言系统的十进制数码（例如阿拉伯-印地语和天城文的数码）以及全宽数码 `'\uff10'` 到 `'\uff19'`。

​	如果 *value* 是一个 [`tuple`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#tuple)，它应当有三个组成部分，一个符号 (`0` 表示正数 `1` 表示负数)，一个由数字组成的 [`tuple`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#tuple)，以及一个整数指数值。 例如，`Decimal((0, (1, 4, 1, 4), -3))` 将返回 `Decimal('1.414')`。

​	如果 *value* 是 [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float)，则二进制浮点值将无损地转换为其精确的十进制等效值。 此转换通常需要53位或更多位数的精度。 例如，`Decimal(float('1.1'))` 转换为 `Decimal('1.100000000000000088817841970012523233890533447265625')`。

*context* 精度不会影响存储的位数。 这完全由 *value* 中的位数决定。 例如，`Decimal('3.00000')` 记录所有五个零，即使上下文精度只有三。

*context* 参数的目的是确定当 *value* 为错误格式的字符串时要怎么做。 如果上下文捕获了 [`InvalidOperation`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.InvalidOperation)，将会引发异常；在其他情况下，构造器将返回一个值为 `NaN` 的新 Decimal。

​	构造完成后， [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 对象是不可变的。

*在 3.2 版本发生变更:* 现在允许构造函数的参数为 [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float) 实例。

*在 3.3 版本发生变更:* [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float) 参数在设置 [`FloatOperation`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.FloatOperation) 陷阱时引发异常。 默认情况下，陷阱已关闭。

*在 3.6 版本发生变更:* 允许下划线进行分组，就像代码中的整数和浮点文字一样。

​	十进制浮点对象与其他内置数值类型共享许多属性，例如 [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float) 和 [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int) 。 所有常用的数学运算和特殊方法都适用。 同样，十进制对象可以复制、pickle、打印、用作字典键、用作集合元素、比较、排序和强制转换为另一种类型（例如 [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float) 或 [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int) 等）。

​	算术对十进制对象和算术对整数和浮点数有一些小的差别。 当余数运算符 `%` 应用于Decimal对象时，结果的符号是 *被除数* 的符号，而不是除数的符号:



``` python
>>> (-7) % 4
1
>>> Decimal(-7) % Decimal(4)
Decimal('-3')
```

​	整数除法运算符 `//` 的行为类似，返回真商的整数部分（截断为零）而不是它的向下取整，以便保留通常的标识 `x == (x // y) * y + x % y`:



``` python
>>> -7 // 4
-2
>>> Decimal(-7) // Decimal(4)
Decimal('-1')
```

`%` 和 `//` 运算符实现了 `remainder` 和 `divide-integer` 操作（分别），如规范中所述。

​	十进制对象通常不能与浮点数或 [`fractions.Fraction`](https://docs.python.org/zh-cn/3.13/library/fractions.html#fractions.Fraction) 实例在算术运算中结合使用：例如,尝试将 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 加到 [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float) ，将引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。 但是，可以使用 Python 的比较运算符来比较 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 实例 `x` 和另一个数字 `y` 。 这样可以避免在对不同类型的数字进行相等比较时混淆结果。

*在 3.2 版本发生变更:* 现在完全支持 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 实例和其他数字类型之间的混合类型比较。

​	除了标准的数字属性，十进制浮点对象还有许多专门的方法：

## **adjusted**()

​	在移出系数最右边的数字之后返回调整后的指数，直到只剩下前导数字：`Decimal('321e+5').adjusted()` 返回 7 。 用于确定最高有效位相对于小数点的位置。

## **as_integer_ratio**()

​	返回一对 `(n, d)` 整数，表示给定的 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 实例作为分数、最简形式项并带有正分母:



``` python
>>> Decimal('-3.14').as_integer_ratio()
(-157, 50)
```

​	转换是精确的。 在 Infinity 上引发 OverflowError ，在 NaN 上引起 ValueError 。

> Added in version 3.6.
>

## **as_tuple**()

​	返回一个 [named tuple](https://docs.python.org/zh-cn/3.13/glossary.html#term-named-tuple) 表示的数字： `DecimalTuple(sign, digits, exponent)`。

## **canonical**()

​	返回参数的规范编码。 目前，一个 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 实例的编码始终是规范的，因此该操作返回其参数不变。

## **compare**(*other*, *context=None*)

​	比较两个 Decimal 实例的值。 [`compare()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.compare) 返回一个 Decimal 实例，如果任一操作数是 NaN ，那么结果是 NaN

```
a or b is a NaN  ==> Decimal('NaN')
a < b            ==> Decimal('-1')
a == b           ==> Decimal('0')
a > b            ==> Decimal('1')
```

## **compare_signal**(*other*, *context=None*)

​	除了所有 NaN 信号之外，此操作与 [`compare()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.compare) 方法相同。 也就是说，如果两个操作数都不是信令NaN，那么任何静默的 NaN 操作数都被视为信令NaN。

## **compare_total**(*other*, *context=None*)

​	使用它们的抽象表示而不是它们的数值来比较两个操作数。 类似于 [`compare()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.compare) 方法，但结果给出了一个总排序 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 实例。 两个 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 实例具有相同的数值但不同的表示形式在此排序中比较不相等：



``` python
>>> Decimal('12.0').compare_total(Decimal('12'))
Decimal('-1')
```

​	静默和发出信号的 NaN 也包括在总排序中。 这个函数的结果是 `Decimal('0')` 如果两个操作数具有相同的表示，或是 `Decimal('-1')` 如果第一个操作数的总顺序低于第二个操作数，或是 `Decimal('1')` 如果第一个操作数在总顺序中高于第二个操作数。 有关总排序的详细信息，请参阅规范。

​	此操作不受上下文影响且静默：不更改任何标志且不执行舍入。 作为例外，如果无法准确转换第二个操作数，则C版本可能会引发InvalidOperation。

## **compare_total_mag**(*other*, *context=None*)

​	比较两个操作数使用它们的抽象表示而不是它们的值，如 [`compare_total()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.compare_total)，但忽略每个操作数的符号。 `x.compare_total_mag(y)` 相当于 `x.copy_abs().compare_total(y.copy_abs())`。

​	此操作不受上下文影响且静默：不更改任何标志且不执行舍入。 作为例外，如果无法准确转换第二个操作数，则C版本可能会引发InvalidOperation。

## **conjugate**()

​	只返回self，这种方法只符合 Decimal 规范。

## **copy_abs**()

​	返回参数的绝对值。 此操作不受上下文影响并且是静默的：没有更改标志且不执行舍入。

## **copy_negate**()

​	回到参数的否定。 此操作不受上下文影响并且是静默的：没有标志更改且不执行舍入。

## **copy_sign**(*other*, *context=None*)

​	返回第一个操作数的副本，其符号设置为与第二个操作数的符号相同。 例如：



``` python
>>> Decimal('2.3').copy_sign(Decimal('-1.5'))
Decimal('-2.3')
```

​	此操作不受上下文影响且静默：不更改任何标志且不执行舍入。 作为例外，如果无法准确转换第二个操作数，则C版本可能会引发InvalidOperation。

## **exp**(*context=None*)

​	返回给定数字的（自然）指数函数 `e**x` 的值。结果使用 [`ROUND_HALF_EVEN`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.ROUND_HALF_EVEN) 舍入模式正确舍入。



``` python
>>> Decimal(1).exp()
Decimal('2.718281828459045235360287471')
>>> Decimal(321).exp()
Decimal('2.561702493119680037517373933E+139')
```

## *classmethod* **from_float**(*f*)

​	另一个构造函数，只接受 [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float) 或 [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int) 的实例。

​	请注意 `Decimal.from_float(0.1)` 与 `Decimal('0.1')` 是不同的。 由于 0.1 不能以二进制浮点数精确表示，该值将被存储为最接受的可表示值 `0x1.999999999999ap-4`。 与其等价的十进制值为 `0.1000000000000000055511151231257827021181583404541015625`。

​	备注

 

​	从 Python 3.2 开始，[`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 实例也可以直接从 [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float) 构造。



``` python
>>> Decimal.from_float(0.1)
Decimal('0.1000000000000000055511151231257827021181583404541015625')
>>> Decimal.from_float(float('nan'))
Decimal('NaN')
>>> Decimal.from_float(float('inf'))
Decimal('Infinity')
>>> Decimal.from_float(float('-inf'))
Decimal('-Infinity')
```

> Added in version 3.1.
>

## **fma**(*other*, *third*, *context=None*)

​	混合乘法加法。 返回 self*other+third ，中间乘积 self*other 没有舍入。



``` python
>>> Decimal(2).fma(3, 5)
Decimal('11')
```

## **is_canonical**()

​	如果参数是规范的，则为返回 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)，否则为 [`False`](https://docs.python.org/zh-cn/3.13/library/constants.html#False) 。 目前，[`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 实例总是规范的，所以这个操作总是返回 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True) 。

## **is_finite**()

​	如果参数是一个有限的数，则返回为 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True) ；如果参数为无穷大或 NaN ，则返回为 [`False`](https://docs.python.org/zh-cn/3.13/library/constants.html#False)。

## **is_infinite**()

​	如果参数为正负无穷大，则返回为 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True) ，否则为 [`False`](https://docs.python.org/zh-cn/3.13/library/constants.html#False) 。

## **is_nan**()

​	如果参数为 NaN （无论是否静默），则返回为 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True) ，否则为 [`False`](https://docs.python.org/zh-cn/3.13/library/constants.html#False) 。

## **is_normal**(*context=None*)

​	如果参数是一个 *标准的* 有限数则返回 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)。 如果参数为零、次标准数、无穷大或 NaN 则返回 [`False`](https://docs.python.org/zh-cn/3.13/library/constants.html#False)。

## **is_qnan**()

​	如果参数为静默 NaN，返回 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)，否则返回 [`False`](https://docs.python.org/zh-cn/3.13/library/constants.html#False)。

## **is_signed**()

​	如果参数带有负号，则返回为 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)，否则返回 [`False`](https://docs.python.org/zh-cn/3.13/library/constants.html#False)。注意，0 和 NaN 都可带有符号。

## **is_snan**()

​	如果参数为显式 NaN，则返回 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)，否则返回 [`False`](https://docs.python.org/zh-cn/3.13/library/constants.html#False)。

## **is_subnormal**(*context=None*)

​	如果参数为次标准数，则返回 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)，否则返回 [`False`](https://docs.python.org/zh-cn/3.13/library/constants.html#False)。

## **is_zero**()

​	如果参数是0（正负皆可），则返回 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)，否则返回 [`False`](https://docs.python.org/zh-cn/3.13/library/constants.html#False)。

## **ln**(*context=None*)

​	返回操作数的自然对数（以 e 为底）。结果是使用 [`ROUND_HALF_EVEN`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.ROUND_HALF_EVEN) 舍入模式正确舍入的。

## **log10**(*context=None*)

​	返回操作数的以十为底的对数。结果是使用 [`ROUND_HALF_EVEN`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.ROUND_HALF_EVEN) 舍入模式正确舍入的。

## **logb**(*context=None*)

​	对于一个非零数，返回其运算数的调整后指数作为一个 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 实例。 如果运算数为零将返回 `Decimal('-Infinity')` 并且产生 the [`DivisionByZero`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.DivisionByZero) 标志。如果运算数是无限大则返回 `Decimal('Infinity')` 。

## **logical_and**(*other*, *context=None*)

[`logical_and()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.logical_and) 是需要两个 *逻辑运算数* 的逻辑运算（参考 [逻辑操作数](https://docs.python.org/zh-cn/3.13/library/decimal.html#logical-operands-label) ）。按位输出两运算数的 `and` 运算的结果。

## **logical_invert**(*context=None*)

[`logical_invert()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.logical_invert) 是一个逻辑运算。结果是操作数的按位求反。

## **logical_or**(*other*, *context=None*)

[`logical_or()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.logical_or) 是需要两个 *logical operands* 的逻辑运算（请参阅 [逻辑操作数](https://docs.python.org/zh-cn/3.13/library/decimal.html#logical-operands-label) ）。结果是两个运算数的按位的 `or` 运算。

## **logical_xor**(*other*, *context=None*)

[`logical_xor()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.logical_xor) 是需要两个 *逻辑运算数* 的逻辑运算（参考 [逻辑操作数](https://docs.python.org/zh-cn/3.13/library/decimal.html#logical-operands-label) ）。结果是按位输出的两运算数的异或运算。

## **max**(*other*, *context=None*)

​	类似于 `max(self, other)` 只是上下文舍入规则是在返回之前被应用并且对于 `NaN` 值会发出信号或忽略（依赖于上下文以及它们是否要发送信号或保持静默）。

## **max_mag**(*other*, *context=None*)

​	与 [`max()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.max) 方法相似，但是操作数使用绝对值完成比较。

## **min**(*other*, *context=None*)

​	类似于 `min(self, other)` 只是上下文舍入规则是在返回之前被应用并且对于 `NaN` 值会发出信号或忽略（依赖于上下文以及它们是发出了信号还是保持静默）。

## **min_mag**(*other*, *context=None*)

​	与 [`min()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.min) 方法相似，但是操作数使用绝对值完成比较。

## **next_minus**(*context=None*)

​	返回小于给定操作数的上下文中可表示的最大数字（或者当前线程的上下文中的可表示的最大数字如果没有给定上下文）。

## **next_plus**(*context=None*)

​	返回大于给定操作数的上下文中可表示的最小数字（或者当前线程的上下文中的可表示的最小数字如果没有给定上下文）。

## **next_toward**(*other*, *context=None*)

​	如果两运算数不相等，返回在第二个操作数的方向上最接近第一个操作数的数。如果两操作数数值上相等，返回将符号设置为与第二个运算数相同的第一个运算数的拷贝。

## **normalize**(*context=None*)

​	用于在当前上下文或指定上下文中产生等价的类的规范值。

​	该操作具有与单目取正值运算相同的语义，区别在于如果最终结果为有限值则将缩减到最简形式，即移除所有末尾的零并保留正负号。 也就是说，当系数为非零值且为十的倍数时则将该系数除以十并将指数加 1。 否则（当系数为零）则将指数设为 0。 在任何情况下正负号都将保持不变。

​	例如，`Decimal('32.100')` 和 `Decimal('0.321000e+2')` 均将标准化为等价的值 `Decimal('32.1')`。

​	请注意舍入的应用将在缩减到最简形式 *之前* 执行。

​	在此规范的最新版本中，该操作也被称为 `reduce`。

## **number_class**(*context=None*)

​	返回一个字符串描述运算数的 *class* 。返回值是以下十个字符串中的一个。

- `"-Infinity"` ，指示运算数为负无穷大。
- `"-Normal"` ，指示该运算数是负正常数字。
- `"-Subnormal"` ，指示该运算数是负的次标准数。
- `"-Zero"` ，指示该运算数是负零。
- `"-Zero"` ，指示该运算数是正零。
- `"+Subnormal"` ，指示该运算数是正的次标准数。
- `"+Normal"` ，指示该运算数是正的标准数。
- `"+Infinity"` ，指示该运算数是正无穷。
- `"NaN"` ，指示该运算数是肃静 NaN （非数字）。
- `"sNaN"` ，指示该运算数是信号 NaN 。

## **quantize**(*exp*, *rounding=None*, *context=None*)

​	返回的值等于舍入后的第一个运算数并且具有第二个操作数的指数。



``` python
>>> Decimal('1.41421356').quantize(Decimal('1.000'))
Decimal('1.414')
```

​	与其他运算不同，如果量化运算后的系数长度大于精度，那么会发出一个 [`InvalidOperation`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.InvalidOperation) 信号。这保证了除非有一个错误情况，量化指数恒等于右手运算数的指数。

​	与其他运算不同，量化永不信号下溢，即使结果不正常且不精确。

​	如果第二个运算数的指数大于第一个运算数的指数那或许需要舍入。在这种情况下，舍入模式由给定 `rounding` 参数决定，其余的由给定 `context` 参数决定；如果参数都未给定，使用当前线程上下文的舍入模式。

​	每当结果的指数大于 `Emax` 或小于 [`Etiny()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Context.Etiny) 就将返回一个错误。

## **radix**()

​	返回 `Decimal(10)`，即 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 类进行所有算术运算所用的数制（基数）。 这是为保持与规范描述的兼容性而加入的。

## **remainder_near**(*other*, *context=None*)

​	返回 *self* 除以 *other* 的余数。 这与 `self % other` 的区别在于所选择的余数要使其绝对值最小化。 更准确地说，返回值为 `self - n * other` 其中 `n` 是最接近 `self / other` 的实际值的整数，并且如果两个整数与实际值的差相等则会选择其中的偶数。

​	如果结果为零则其符号将为 *self* 的符号。



``` python
>>> Decimal(18).remainder_near(Decimal(10))
Decimal('-2')
>>> Decimal(25).remainder_near(Decimal(10))
Decimal('5')
>>> Decimal(35).remainder_near(Decimal(10))
Decimal('-5')
```

## **rotate**(*other*, *context=None*)

​	返回对第一个操作数的数码按第二个操作数所指定的数量进行轮转的结果。 第二个操作数必须为 -precision 至 precision 精度范围内的整数。 第二个操作数的绝对值给出要轮转的位数。 如果第二个操作数为正值则向左轮转；否则向右轮转。 如有必要第一个操作数的系数会在左侧填充零以达到 precision 所指定的长度。 第一个操作数的符号和指数保持不变。

## **same_quantum**(*other*, *context=None*)

​	检测自身与 other 是否具有相同的指数或是否均为 `NaN`。

​	此操作不受上下文影响且静默：不更改任何标志且不执行舍入。 作为例外，如果无法准确转换第二个操作数，则C版本可能会引发InvalidOperation。

## **scaleb**(*other*, *context=None*)

​	返回第一个操作数使用第二个操作数对指数进行调整的结果。 等价于返回第一个操作数乘以 `10**other` 的结果。 第二个操作数必须为整数。

## **shift**(*other*, *context=None*)

​	返回第一个操作数的数码按第二个操作数所指定的数量进行移位的结果。 第二个操作数必须为 -precision 至 precision 范围内的整数。 第二个操作数的绝对值给出要移动的位数。 如果第二个操作数为正值则向左移位；否则向右移位。 移入系数的数码为零。 第一个操作数的符号和指数保持不变。

## **sqrt**(*context=None*)

​	返回参数的平方根精确到完整精度。

## **to_eng_string**(*context=None*)

​	转换为字符串，如果需要指数则会使用工程标注法。

​	工程标注法的指数是 3 的倍数。 这会在十进制位的左边保留至多 3 个数码，并可能要求添加一至两个末尾零。

​	例如，此方法会将 `Decimal('123E+1')` 转换为 `Decimal('1.23E+3')`。

## **to_integral**(*rounding=None*, *context=None*)

​	与 [`to_integral_value()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.to_integral_value) 方法相同。 保留 `to_integral` 名称是为了与旧版本兼容。

## **to_integral_exact**(*rounding=None*, *context=None*)

​	舍入到最接近的整数，发出信号 [`Inexact`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Inexact) 或者如果发生舍入则相应地发出信号 [`Rounded`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Rounded)。 如果给出 `rounding` 形参则由其确定舍入模式，否则由给定的 `context` 来确定。 如果没有给定任何形参则会使用当前上下文的舍入模式。

## **to_integral_value**(*rounding=None*, *context=None*)

​	舍入到最接近的整数而不发出 [`Inexact`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Inexact) 或 [`Rounded`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Rounded) 信号。 如果给出 *rounding* 则会应用其所指定的舍入模式；否则使用所提供的 *context* 或当前上下文的舍入方法。

​	可以使用 [`round()`](https://docs.python.org/zh-cn/3.13/library/functions.html#round) 函数对 Decimal 数字执行舍入：

## **round(number)**

## **round(number, ndigits)**

​	如果 *ndigits* 未给出或为 `None`，则返回最接近 *number* 的 [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int)，同样接近时向偶数舍入，并忽略 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 上下文的舍入模式。 如果 *number* 为无穷大则引发 [`OverflowError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OverflowError) 或者如果为（静默或有信号）NaN 则引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

​	如果 *ndigits* 是一个 [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int)，则将遵循上下文的舍入模式并返回代表 *number* 的舍入到最接近 `Decimal('1E-ndigits')` 的倍数的 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal)；在此情况下，`round(number, ndigits)` 等价于 `self.quantize(Decimal('1E-ndigits'))`。 如果 *number* 是一个静默 NaN 则返回 `Decimal('NaN')`。 如果 *number* 为无穷大、有信号 NaN，或者如果量化操作后的系数长度大于当前上下文的精度则会引发 [`InvalidOperation`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.InvalidOperation)。 换句话说，对于非边际情况：

- 如果 *ndigits* 为正值，则返回 *number* 舍入到 *ndigits* 个十进制数位的结果；
- 如果 *ndigits* 为零，则返回 *number* 舍入到最接近整数的结果；
- 如果 *ndigits* 为负值，则返回 *number* 舍入到最接近 `10**abs(ndigits)` 的倍数的结果。

​	例如:



``` python
>>> from decimal import Decimal, getcontext, ROUND_DOWN
>>> getcontext().rounding = ROUND_DOWN
>>> round(Decimal('3.75'))     # 上下文舍入设置将被忽略
4
>>> round(Decimal('3.5'))      # 两边相等则舍入到偶数
4
>>> round(Decimal('3.75'), 0)  # 使用上下文舍入设置
Decimal('3')
>>> round(Decimal('3.75'), 1)
Decimal('3.7')
>>> round(Decimal('3.75'), -1)
Decimal('0E+1')
```



### 逻辑操作数

[`logical_and()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.logical_and), [`logical_invert()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.logical_invert), [`logical_or()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.logical_or) 和 [`logical_xor()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.logical_xor) 方法均期望其参数为 *逻辑操作数*。 *逻辑操作数* 即指数位和符号位均为零，且其数字位均为 `0` 或 `1` 的 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 实例。



## 上下文对象

​	上下文是算术运算所在的环境。 它们管理精度、设置舍入规则、确定将哪些信号视为异常，并限制指数的范围。

​	每个线程都有自己的当前上下文，可使用 [`getcontext()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.getcontext) 和 [`setcontext()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.setcontext) 函数来读取或修改：

## decimal.**getcontext**()

​	返回活动线程的当前上下文。

## decimal.**setcontext**(*c*)

​	将活动线程的当前上下文设为 *c*。

​	你也可以使用 [`with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#with) 语句和 [`localcontext()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.localcontext) 函数来临时改变活动上下文。

## decimal.**localcontext**(*ctx=None*, ***kwargs*)

​	返回一个将在进入 with 语句时将活动线程的上下文设为 *ctx* 的一个副本并在退出该 with 语句时恢复之前上下文的上下文管理器。 如果未指定上下文，则会使用当前上下文的一个副本。 *kwargs* 参数将被用来设置新上下文的属性。

​	例如，以下代码会将当前 decimal 精度设为 42 位，执行一个运算，然后自动恢复之前的上下文:

```
from decimal import localcontext

with localcontext() as ctx:
    ctx.prec = 42   # 执行高精度的运算
    s = calculate_something()
s = +s  # 将最终结果舍入到默认精度
```

​	使用关键字参数，代码将如下所示:

```
from decimal import localcontext

with localcontext(prec=42) as ctx:
    s = calculate_something()
s = +s
```

​	如果 *kwargs* 提供了 [`Context`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Context) 所不支持的属性则会引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。 如果 *kwargs* 提供了无效的属性值则会引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError) 或 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

*在 3.11 版本发生变更:* [`localcontext()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.localcontext) 现在支持通过使用关键字参数来设置上下文属性。

​	新的上下文也可使用下述的 [`Context`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Context) 构造器来创建。 此外，模块还提供了三种预设的上下文:

## *class* decimal.**BasicContext**

​	这是由通用十进制算术规范描述所定义的标准上下文。 精度设为九。 舍入设为 [`ROUND_HALF_UP`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.ROUND_HALF_UP)。 清除所有旗标。 启用所有陷阱（视为异常），但 [`Inexact`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Inexact), [`Rounded`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Rounded) 和 [`Subnormal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Subnormal) 除外。

​	由于启用了许多陷阱，此上下文适用于进行调试。

## *class* decimal.**ExtendedContext**

​	这是由通用十进制算术规范描述所定义的标准上下文。 精度设为九。 舍入设为 [`ROUND_HALF_EVEN`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.ROUND_HALF_EVEN)。 清除所有旗标。 不启用任何陷阱（因此在计算期间不会引发异常）。

​	由于禁用了陷阱，此上下文适用于希望结果值为 `NaN` 或 `Infinity` 而不是引发异常的应用程序。 这允许应用程序在出现当其他情况下会中止程序的条件时仍能完成运行。

## *class* decimal.**DefaultContext**

​	此上下文被 [`Context`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Context) 构造器用作新上下文的原型。 改变一个字段（例如精度）的效果将是改变 [`Context`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Context) 构造器所创建的新上下文的默认值。

​	此上下文最适用于多线程环境。 在线程开始前改变一个字段具有设置全系统默认值的效果。 不推荐在线程开始后改变字段，因为这会要求线程同步避免竞争条件。

​	在单线程环境中，最好完全不使用此上下文。 而是简单地电显式创建上下文，具体如下所述。

​	默认值为 `Context.prec`=`28`, `Context.rounding`=[`ROUND_HALF_EVEN`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.ROUND_HALF_EVEN)，并为 [`Overflow`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Overflow), [`InvalidOperation`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.InvalidOperation) 和 [`DivisionByZero`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.DivisionByZero) 启用陷阱。

​	在已提供的三种上下文之外，还可以使用 [`Context`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Context) 构造器创建新的上下文。

## *class* decimal.**Context**(*prec=None*, *rounding=None*, *Emin=None*, *Emax=None*, *capitals=None*, *clamp=None*, *flags=None*, *traps=None*)

​	创建一个新上下文。 如果某个字段未指定或为 [`None`](https://docs.python.org/zh-cn/3.13/library/constants.html#None)，则从 [`DefaultContext`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.DefaultContext) 拷贝默认值。 如果 *flags* 字段未指定或为 [`None`](https://docs.python.org/zh-cn/3.13/library/constants.html#None)，则清空所有旗标。

*prec* 是一个用于设置该上下文中算术运算的精度的 [`1`, [`MAX_PREC`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.MAX_PREC)] 范围内的整数。

*rounding* 选项应为 [Rounding Modes](https://docs.python.org/zh-cn/3.13/library/decimal.html#rounding-modes) 小节中列出的常量之一。

*traps* 和 *flags* 字段列出要设置的任何信号。 通常，新上下文应当只设置 traps 而让 flags 为空。

*Emin* 和 *Emax* 字段是指定指数所允许的外部上限的整数。 *Emin* 必须在 [[`MIN_EMIN`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.MIN_EMIN), `0`] 范围内，*Emax* 必须在 [`0`, [`MAX_EMAX`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.MAX_EMAX)] 范围内。

*capitals* 字段为 `0` 或 `1` (默认值)。 如果设为 `1`，指数将附带大写的 `E` 打印出来；在其他情况下将使用小写的 `e`: `Decimal('6.02e+23')`。

*clamp* 字段为 `0` (默认值) 或 `1`。 如果设为 `1`，则 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 实例的指数 `e` 的表示范围在此上下文中将严格限制在 `Emin - prec + 1 <= e <= Emax - prec + 1` 范围内。 如果 *clamp* 为 `0` 则将适用较弱的条件：调整后的 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 实例指数最大值为 `Emax`。 当 *clamp* 为 `1` 时，一个较大的普通数值将在可能的情况下减小其指数并为其系数添加相应数量的零，以便符合指数值限制；这可以保留数字值但会丢失有效末尾零的信息。 例如:



``` python
>>> Context(prec=6, Emax=999, clamp=1).create_decimal('1.23e999')
Decimal('1.23000E+999')
```

​	将 *clamp* 值设为 `1` 即允许与 IEEE 754 所描述的固定宽度十进制交换格式保持兼容性。

[`Context`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Context) 类定义了几种通用方法以及大量直接在给定上下文中进行算术运算的方法。 此外，对于上述的每种 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 方法（除了 [`adjusted()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.adjusted) 和 [`as_tuple()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.as_tuple) 方法）都有一个对应的 [`Context`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Context) 方法。 例如，对于一个 [`Context`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Context) 的实例 `C` 和 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 的实例 `x`，`C.exp(x)` 就等价于 `x.exp(context=C)`。 每个 [`Context`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Context) 方法都接受一个 Python 整数（即 [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int) 的实例）在任何接受 Decimal 实例的地方使用。

## **clear_flags**()

​	将所有旗标重置为 `0`。

## **clear_traps**()

​	将所有陷阱重置为 `0`。

> Added in version 3.3.
>

## **copy**()

​	返回上下文的一个副本。

## **copy_decimal**(*num*)

​	返回 Decimal 实例 num 的一个副本。

## **create_decimal**(*num*)

​	基于 *num* 创建一个新 Decimal 实例但使用 *self* 作为上下文。 与 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 构造器不同，该上下文的精度、舍入方法、旗标和陷阱会被应用于转换过程。

​	此方法很有用处，因为常量往往被给予高于应用所需的精度。 另一个好处在于立即执行舍入可以消除超出当前精度的数位所导致的意外效果。 在下面的示例中，使用未舍入的输入意味着在总和中添加零会改变结果：



``` python
>>> getcontext().prec = 3
>>> Decimal('3.4445') + Decimal('1.0023')
Decimal('4.45')
>>> Decimal('3.4445') + Decimal(0) + Decimal('1.0023')
Decimal('4.44')
```

​	此方法实现了 IBM 规格描述中的转换为数字操作。 如果参数为字符串，则不允许有开头或末尾的空格或下划线。

## **create_decimal_from_float**(*f*)

​	基于浮点数 *f* 创建一个新的 Decimal 实例，但会使用 *self* 作为上下文来执行舍入。 与 [`Decimal.from_float()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.from_float) 类方法不同，上下文的精度、舍入方法、旗标和陷阱会应用到转换中。



``` python
>>> context = Context(prec=5, rounding=ROUND_DOWN)
>>> context.create_decimal_from_float(math.pi)
Decimal('3.1415')
>>> context = Context(prec=5, traps=[Inexact])
>>> context.create_decimal_from_float(math.pi)
Traceback (most recent call last):
    ...
decimal.Inexact: None
```

> Added in version 3.1.
>

## **Etiny**()

​	返回一个等于 `Emin - prec + 1` 的值即次标准化结果中的最小指数值。 当发生向下溢出时，指数会设为 [`Etiny`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Context.Etiny)。

## **Etop**()

​	返回一个等于 `Emax - prec + 1` 的值。

​	使用 decimal 的通常方式是创建 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 实例然后对其应用算术运算,这些运算发生在活动线程的当前上下文中。 一种替代方式则是使用上下文的方法在特定上下文中进行计算。 这些方法类似于 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 类的方法，在此仅简单地重新列出。

## **abs**(*x*)

​	返回 *x* 的绝对值。

## **add**(*x*, *y*)

​	返回 *x* 与 *y* 的和。

## **canonical**(*x*)

​	返回相同的 Decimal 对象 *x*。

## **compare**(*x*, *y*)

​	对 *x* 与 *y* 进行数值比较。

## **compare_signal**(*x*, *y*)

​	对两个操作数进行数值比较。

## **compare_total**(*x*, *y*)

​	对两个操作数使用其抽象表示进行比较。

## **compare_total_mag**(*x*, *y*)

​	对两个操作数使用其抽象表示进行比较，忽略符号。

## **copy_abs**(*x*)

​	返回 *x* 的副本，符号设为 0。

## **copy_negate**(*x*)

​	返回 *x* 的副本，符号取反。

## **copy_sign**(*x*, *y*)

​	从 *y* 拷贝符号至 *x*。

## **divide**(*x*, *y*)

​	返回 *x* 除以 *y* 的结果。

## **divide_int**(*x*, *y*)

​	返回 *x* 除以 *y* 的结果，截短为整数。

## **divmod**(*x*, *y*)

​	两个数字相除并返回结果的整数部分。

## **exp**(*x*)

​	返回 `e ** x`。

## **fma**(*x*, *y*, *z*)

​	返回 *x* 乘以 *y* 再加 *z* 的结果。

## **is_canonical**(*x*)

​	如果 *x* 是规范的则返回 `True`；否则返回 `False`。

## **is_finite**(*x*)

​	如果 *x* 为有限的则返回 `True`；否则返回 `False`。

## **is_infinite**(*x*)

​	如果 *x* 是无限的则返回 `True`；否则返回 `False`。

## **is_nan**(*x*)

​	如果 *x* 是 qNaN 或 sNaN 则返回 `True`；否则返回 `False`。

## **is_normal**(*x*)

​	如果 *x* 是标准数则返回 `True`；否则返回 `False`。

## **is_qnan**(*x*)

​	如果 *x* 是静默 NaN 则返回 `True`；否则返回 `False`。

## **is_signed**(*x*)

*x* 是负数则返回 `True`；否则返回 `False`。

## **is_snan**(*x*)

​	如果 *x* 是显式 NaN 则返回 `True`；否则返回 `False`。

## **is_subnormal**(*x*)

​	如果 *x* 是次标准数则返回 `True`；否则返回 `False`。

## **is_zero**(*x*)

​	如果 *x* 为零则返回 `True`；否则返回 `False`。

## **ln**(*x*)

​	返回 *x* 的自然对数（以 e 为底）。

## **log10**(*x*)

​	返回 *x* 的以 10 为底的对数。

## **logb**(*x*)

​	返回操作数的 MSD 等级的指数。

## **logical_and**(*x*, *y*)

​	在操作数的每个数位间应用逻辑运算 *and*。

## **logical_invert**(*x*)

​	反转 *x* 中的所有数位。

## **logical_or**(*x*, *y*)

​	在操作数的每个数位间应用逻辑运算 *or*。

## **logical_xor**(*x*, *y*)

​	在操作数的每个数位间应用逻辑运算 *xor*。

## **max**(*x*, *y*)

​	对两个值执行数字比较并返回其中的最大值。

## **max_mag**(*x*, *y*)

​	对两个值执行忽略正负号的数字比较。

## **min**(*x*, *y*)

​	对两个值执行数字比较并返回其中的最小值。

## **min_mag**(*x*, *y*)

​	对两个值执行忽略正负号的数字比较。

## **minus**(*x*)

​	对应于 Python 中的单目前缀取负运算符执行取负操作。

## **multiply**(*x*, *y*)

​	返回 *x* 和 *y* 的积。

## **next_minus**(*x*)

​	返回小于 *x* 的最大数字表示形式。

## **next_plus**(*x*)

​	返回大于 *x* 的最小数字表示形式。

## **next_toward**(*x*, *y*)

​	返回 *x* 趋向于 *y* 的最接近的数字。

## **normalize**(*x*)

​	将 *x* 改写为最简形式。

## **number_class**(*x*)

​	返回 *x* 的类的表示。

## **plus**(*x*)

​	对应于 Python 中的单目前缀取正运算符执行取正操作。 此操作将应用上下文精度和舍入，因此它 *不是* 标识运算。

## **power**(*x*, *y*, *modulo=None*)

​	返回 `x` 的 `y` 次方，如果给出了模数 `modulo` 则取其余数。

​	传入两个参数时，计算 `x**y`。 如果 `x` 为负值则 `y` 必须为整数。 除非 `y` 为整数且结果为有限值并可在 'precision' 位内精确表示否则结果将是不精确的。 所在上下文的舍入模式将被使用。 结果在 Python 版中总是会被正确地舍入。

`Decimal(0) ** Decimal(0)` 结果为 `InvalidOperation`，而如果 `InvalidOperation` 未被捕获，则结果为 `Decimal('NaN')`。

*在 3.3 版本发生变更:* C 模块计算 [`power()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Context.power) 时会使用已正确舍入的 [`exp()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Context.exp) 和 [`ln()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Context.ln) 函数。 结果是有良好定义的但仅限于“几乎总是正确舍入”。

​	带有三个参数时，计算 `(x**y) % modulo`。 对于三个参数的形式，参数将会应用以下限制：

- 三个参数必须都是整数
- `y` 必须是非负数
- `x` 或 `y` 至少有一个不为零
- `modulo` 必须不为零且至多有 'precision' 位

​	来自 `Context.power(x, y, modulo)` 的结果值等于使用无限精度计算 `(x**y) % modulo` 所得到的值，但其计算过程更高效。 结果的指数为零，无论 `x`, `y` 和 `modulo` 的指数是多少。 结果值总是完全精确的。

## **quantize**(*x*, *y*)

​	返回的值等于 *x* (舍入后)，并且指数为 *y*。

## **radix**()

​	恰好返回 10，因为这是 Decimal 对象 :)

## **remainder**(*x*, *y*)

​	返回整除所得到的余数。

​	结果的符号，如果不为零，则与原始除数的符号相同。

## **remainder_near**(*x*, *y*)

​	返回 `x - y * n`，其中 *n* 为最接近 `x / y` 实际值的整数（如结果为 0 则其符号将与 *x* 的符号相同）。

## **rotate**(*x*, *y*)

​	返回 *x* 翻转 *y* 次的副本。

## **same_quantum**(*x*, *y*)

​	如果两个操作数具有相同的指数则返回 `True`。

## **scaleb**(*x*, *y*)

​	返回第一个操作数添加第二个值的指数后的结果。

## **shift**(*x*, *y*)

​	返回 *x* 变换 *y* 次的副本。

## **sqrt**(*x*)

​	非负数基于上下文精度的平方根。

## **subtract**(*x*, *y*)

​	返回 *x* 和 *y* 的差。

## **to_eng_string**(*x*)

​	转换为字符串，如果需要指数则会使用工程标注法。

​	工程标注法的指数是 3 的倍数。 这会在十进制位的左边保留至多 3 个数码，并可能要求添加一至两个末尾零。

## **to_integral_exact**(*x*)

​	舍入到一个整数。

## **to_sci_string**(*x*)

​	使用科学计数法将一个数字转换为字符串。



## 常量

​	本节中的常量仅与 C 模块相关。 它们也被包含在纯 Python 版本以保持兼容性。

|                       | 32位         | 64位                   |
| :-------------------- | :----------- | :--------------------- |
| decimal.**MAX_PREC**  | `425000000`  | `999999999999999999`   |
| decimal.**MAX_EMAX**  | `425000000`  | `999999999999999999`   |
| decimal.**MIN_EMIN**  | `-425000000` | `-999999999999999999`  |
| decimal.**MIN_ETINY** | `-849999999` | `-1999999999999999997` |

## decimal.**HAVE_THREADS**

​	该值为 `True`。 已弃用，因为 Python 现在总是启用线程。

*自 3.9 版本弃用.*

## decimal.**HAVE_CONTEXTVAR**

​	默认值为 `True`。 如果 Python 编译版本 [`使用了 --without-decimal-contextvar 选项来配置`](https://docs.python.org/zh-cn/3.13/using/configure.html#cmdoption-without-decimal-contextvar)，则 C 版本会使用线程局部而非协程局部上下文并且该值为 `False`。 这在某些嵌套上下文场景中将会稍快一些。

> Added in version 3.8.3.
>

## 舍入模式

## decimal.**ROUND_CEILING**

​	舍入方向为 `Infinity`。

## decimal.**ROUND_DOWN**

​	舍入方向为零。

## decimal.**ROUND_FLOOR**

​	舍入方向为 `-Infinity`。

## decimal.**ROUND_HALF_DOWN**

​	舍入到最接近的数，同样接近则舍入方向为零。

## decimal.**ROUND_HALF_EVEN**

​	舍入到最接近的数，同样接近则舍入到最接近的偶数。

## decimal.**ROUND_HALF_UP**

​	舍入到最接近的数，同样接近则舍入到零的反方向。

## decimal.**ROUND_UP**

​	舍入到零的反方向。

## decimal.**ROUND_05UP**

​	如果最后一位朝零的方向舍入后为 0 或 5 则舍入到零的反方向；否则舍入方向为零。



## 信号

​	信号代表在计算期间引发的条件。 每个信号对应于一个上下文旗标和一个上下文陷阱启用器。

​	上下文旗标将在遇到特定条件时被设定。 在完成计算之后，将为了获得信息而检测旗标（例如确定计算是否精确）。 在检测旗标后，请确保在开始下一次计算之前清除所有旗标。

​	如果为信号设定了上下文的陷阱启用器，则条件会导致特定的 Python 异常被引发。 举例来说，如果设定了 [`DivisionByZero`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.DivisionByZero) 陷阱，则当遇到此条件时就将引发 [`DivisionByZero`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.DivisionByZero) 异常。

## *class* decimal.**Clamped**

​	修改一个指数以符合表示限制。

​	通常，限位将在一个指数值超出上下文的 `Emin` 和 `Emax` 限制时发生。 在可能的情况下，会通过向系数添加零来将指数缩减至符合限制。

## *class* decimal.**DecimalException**

​	其他信号的基类，并且也是 [`ArithmeticError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ArithmeticError) 的一个子类。

## *class* decimal.**DivisionByZero**

​	非无限数被零除的信号。

​	可在除法、取余除法或对一个数执行负数次幂运算时发生。 如果此信号未被陷阱捕获，则返回 `Infinity` 或 `-Infinity` 并由对计算的输入来确定正负符号。

## *class* decimal.**Inexact**

​	表明发生了舍入且结果是不精确的。

​	有非零数位在舍入期间被丢弃的信号。 舍入结果将被返回。 此信号旗标或陷阱被用于检测结果不精确的情况。

## *class* decimal.**InvalidOperation**

​	执行了一个无效的操作。

​	表明请求了一个无意义的运算。 如果未被捕获，则返回 `NaN`。 可能的原因包括:

```
Infinity - Infinity
0 * Infinity
Infinity / Infinity
x % 0
Infinity % x
sqrt(-x) and x > 0
0 ** 0
x ** (non-integer)
x ** Infinity
```

## *class* decimal.**Overflow**

​	数值的溢出。

​	表明在发生舍入之后指数值大于 `Context.Emax`。 如果未被捕获，则结果将取决于舍入模式，或是向下舍入为最大的可表示有限数值，或是向上舍入为 `Infinity`。 无论是哪种情况，都将发出 [`Inexact`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Inexact) 和 [`Rounded`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Rounded) 信号。

## *class* decimal.**Rounded**

​	发生了舍入，但或许并没有信息丢失。

​	一旦舍入操作丢弃了数位就会发出此信号；即使被丢弃的数位是零（如将 `5.00` 舍入到 `5.0` 的情况）。 如果未被捕获，则不加修改地返回结果。 此信号用于检测有效位数的丢弃。

## *class* decimal.**Subnormal**

​	在舍入之前指数值低于 `Emin`。

​	当操作结果是次标准数（即指数过小）时就会发出此信号。 如果未被陷阱捕获，则不经修改过返回结果。

## *class* decimal.**Underflow**

​	数字向下溢出导致结果舍入到零。

​	当一个次标准数结果通过舍入转为零时就会发出此信号。 同时还将引发 [`Inexact`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Inexact) 和 [`Subnormal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Subnormal) 信号。

## *class* decimal.**FloatOperation**

​	为 float 和 Decimal 的混合启用更严格的语义。

​	如果信号未被捕获（默认），则在 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 构造器、[`create_decimal()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Context.create_decimal) 和所有比较运算中允许 float 和 Decimal 的混合。 转换和比较都是完全精确的。 发生的任何混合运算都将通过在上下文旗标中设置 [`FloatOperation`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.FloatOperation) 来静默地记录。 通过 [`from_float()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.from_float) 或 [`create_decimal_from_float()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Context.create_decimal_from_float) 进行显式转换则不会设置旗标。

​	在其他情况下（即信号被捕获），则只静默执行相等性比较和显式转换。 所有其他混合运算都将引发 [`FloatOperation`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.FloatOperation)。

​	以下表格总结了信号的层级结构:

```
exceptions.ArithmeticError(exceptions.Exception)
    DecimalException
        Clamped
        DivisionByZero(DecimalException, exceptions.ZeroDivisionError)
        Inexact
            Overflow(Inexact, Rounded)
            Underflow(Inexact, Rounded, Subnormal)
        InvalidOperation
        Rounded
        Subnormal
        FloatOperation(DecimalException, exceptions.TypeError)
```



## 浮点数说明

### 通过提升精度来解决舍入错误

​	使用 decimal 浮点数可以消除十进制表示错误（即能够精确地表示 `0.1` 这样的数）；然而，某些运算在非零数位超出了给定的精度时仍然可能导至舍入错误。

​	舍入错误的影响可能因接近相互抵销的加减运算被放大从而导致丢失有效位。 Knuth 提供了两个指导性示例，其中出现了精度不足的浮点算术舍入，导致加法的交换律和分配律被打破：



```
# 来自 Seminumerical Algorithms, Section 4.2.2 的示例。
>>> from decimal import Decimal, getcontext
>>> getcontext().prec = 8

>>> u, v, w = Decimal(11111113), Decimal(-11111111), Decimal('7.51111111')
>>> (u + v) + w
Decimal('9.5111111')
>>> u + (v + w)
Decimal('10')

>>> u, v, w = Decimal(20000), Decimal(-6), Decimal('6.0000003')
>>> (u*v) + (u*w)
Decimal('0.01')
>>> u * (v+w)
Decimal('0.0060000')
```

[`decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#module-decimal) 模块则可以通过充分地扩展精度来避免有效位的丢失：



``` python
>>> getcontext().prec = 20
>>> u, v, w = Decimal(11111113), Decimal(-11111111), Decimal('7.51111111')
>>> (u + v) + w
Decimal('9.51111111')
>>> u + (v + w)
Decimal('9.51111111')
>>>
>>> u, v, w = Decimal(20000), Decimal(-6), Decimal('6.0000003')
>>> (u*v) + (u*w)
Decimal('0.0060000')
>>> u * (v+w)
Decimal('0.0060000')
```

### 特殊的值

[`decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#module-decimal) 模块的数字系统提供了一些特殊的值包括 `NaN`, `sNaN`, `-Infinity`, `Infinity`, 和两种零值，`+0` 和 `-0`。

​	无穷大可以使用 `Decimal('Infinity')` 来构建。 它们也可以在不捕获 [`DivisionByZero`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.DivisionByZero) 信号捕获时通过除以零来产生。 类似地，当不捕获 [`Overflow`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Overflow) 信号时，也可以通过舍入到超出最大可表示数字限制的方式产生无穷大的结果。

​	无穷大是有符号的（仿射）并可用于算术运算，它们会被当作极其巨大的不确定数字来处理。 例如，无穷大加一个常量结果也将为无穷大。

​	某些运算没有确定的结果并将返回 `NaN`，或者如果捕获了 [`InvalidOperation`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.InvalidOperation) 信号，则会引发一个异常。 例如，`0/0` 将返回 `NaN` 表示 "not a number"。 这样的 `NaN` 将静默产生，并且一旦产生就将在参与其他运算时始终得到 `NaN` 的结果。 这种行为对于偶尔缺少输入的各类计算都很有用处 --- 它允许在将特定结果标记为无效的同时让计算继续进行。

​	一种变体形式是 `sNaN`，它在每次运算后会发出信号而不是保持静默。 当对于无效结果需要中断计算进行特别处理时这是一个很有用的返回值。

​	Python 中比较运算符的行为在涉及 `NaN` 时可能会令人有点惊讶。 相等性检测在操作数中有静默型或信号型 `NaN` 时总是会返回 [`False`](https://docs.python.org/zh-cn/3.13/library/constants.html#False) (即使是执行 `Decimal('NaN')==Decimal('NaN')`)，而不等性检测总是会返回 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)。 当尝试使用 `<`, `<=`, `>` 或 `>=` 运算符中的任何一个来比较两个 Decimal 值时，如果运算数中有 `NaN` 则将引发 [`InvalidOperation`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.InvalidOperation) 信号，如果此信号未被捕获则将返回 [`False`](https://docs.python.org/zh-cn/3.13/library/constants.html#False)。 请注意通用十进制算术规范并未规定直接比较行为；这些涉及 `NaN` 的比较规则来自于 IEEE 854 标准 (见第 5.7 节表 3)。 要确保严格符合标准，请改用 [`compare()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.compare) 和 [`compare_signal()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.compare_signal) 方法。

​	有符号零值可以由向下溢出的运算产生。 它们保留符号是为了让运算结果能以更高的精度传递。 由于它们的大小为零，正零和负零会被视为相等，且它们的符号具有信息。

​	在这两个不相同但却相等的有符号零之外，还存在几种零的不同表示形式，它们的精度不同但值也都相等。 这需要一些时间来逐渐适应。 对于习惯了标准浮点表示形式的眼睛来说，以下运算返回等于零的值并不是显而易见的：



``` python
>>> 1 / Decimal('Infinity')
Decimal('0E-1000026')
```



## 使用线程

[`getcontext()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.getcontext) 函数会为每个线程访问不同的 [`Context`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Context) 对象。 具有单独线程上下文意味着线程可以修改上下文 (例如 `getcontext().prec=10`) 而不影响其他线程。

​	类似的 [`setcontext()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.setcontext) 会为当前上下文的目标自动赋值。

​	如果在调用 [`setcontext()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.setcontext) 之前调用了 [`getcontext()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.getcontext)，则 [`getcontext()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.getcontext) 将自动创建一个新的上下文在当前线程中使用。

​	新的上下文拷贝自一个名为 *DefaultContext* 的原型上下文。 要控制默认值以便每个线程在应用运行期间都使用相同的值，可以直接修改 *DefaultContext* 对象。 这应当在任何线程启动 *之前* 完成以使得调用 [`getcontext()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.getcontext) 的线程之间不会产生竞争条件。 例如:

```
# 为所有将要启动的线程设置应用程序级默认值
DefaultContext.prec = 12
DefaultContext.rounding = ROUND_DOWN
DefaultContext.traps = ExtendedContext.traps.copy()
DefaultContext.traps[InvalidOperation] = 1
setcontext(DefaultContext)

# 在此之后，即可启动线程
t1.start()
t2.start()
t3.start()
 . . .
```



## 例程

​	以下是一些用作工具函数的例程，它们演示了使用 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 类的各种方式:

```
def moneyfmt(value, places=2, curr='', sep=',', dp='.',
             pos='', neg='-', trailneg=''):
    """将 Decimal 值转换为以货币形式格式化的字符串。

    places:  小数点之后要保留的位数
    curr:    正负号之前可选的货币符号 (可以为空)
    sep:     可选的千位分隔符 (逗点, 句点, 空格或为空)
    dp:      小数点符号 (逗点或句点)
             仅在保留零位小数时设为空
    pos:     可选的正值符号: '+', 空格或为空
    neg:     可选的负值符号: '-', '(', 空格或为空
    trailneg:可选的末尾负值符号:  '-', ')', 空格或为空

    >>> d = Decimal('-1234567.8901')
    >>> moneyfmt(d, curr='$')
    '-$1,234,567.89'
    >>> moneyfmt(d, places=0, sep='.', dp='', neg='', trailneg='-')
    '1.234.568-'
    >>> moneyfmt(d, curr='$', neg='(', trailneg=')')
    '($1,234,567.89)'
    >>> moneyfmt(Decimal(123456789), sep=' ')
    '123 456 789.00'
    >>> moneyfmt(Decimal('-0.02'), neg='<', trailneg='>')
    '<0.02>'

    """
    q = Decimal(10) ** -places      # 2 个小数位 --> '0.01'
    sign, digits, exp = value.quantize(q).as_tuple()
    result = []
    digits = list(map(str, digits))
    build, next = result.append, digits.pop
    if sign:
        build(trailneg)
    for i in range(places):
        build(next() if digits else '0')
    if places:
        build(dp)
    if not digits:
        build('0')
    i = 0
    while digits:
        build(next())
        i += 1
        if i == 3 and digits:
            i = 0
            build(sep)
    build(curr)
    build(neg if sign else pos)
    return ''.join(reversed(result))

def pi():
    """计算 Pi 到当前精度。

    >>> print(pi())
    3.141592653589793238462643383

    """
    getcontext().prec += 2  # 用于中间步骤的额外位数
    three = Decimal(3)      # 替换常规浮点数的 "three=3.0"
    lasts, t, s, n, na, d, da = 0, three, 3, 1, 0, 0, 24
    while s != lasts:
        lasts = s
        n, na = n+na, na+8
        d, da = d+da, da+32
        t = (t * n) / d
        s += t
    getcontext().prec -= 2
    return +s               # 单目取正值运算将应用新的精度

def exp(x):
    """返回 e 的 x 次方。 结果类型将与输入类型相匹配。

    >>> print(exp(Decimal(1)))
    2.718281828459045235360287471
    >>> print(exp(Decimal(2)))
    7.389056098930650227230427461
    >>> print(exp(2.0))
    7.38905609893
    >>> print(exp(2+0j))
    (7.38905609893+0j)

    """
    getcontext().prec += 2
    i, lasts, s, fact, num = 0, 0, 1, 1, 1
    while s != lasts:
        lasts = s
        i += 1
        fact *= i
        num *= x
        s += num / fact
    getcontext().prec -= 2
    return +s

def cos(x):
    """返回以弧度衡量的 x 的余弦。

    泰勒级数近似算法对较小的 x 值效果最好。
    对于较大的值，则先计算 x = x % (2 * pi)。

    >>> print(cos(Decimal('0.5')))
    0.8775825618903727161162815826
    >>> print(cos(0.5))
    0.87758256189
    >>> print(cos(0.5+0j))
    (0.87758256189+0j)

    """
    getcontext().prec += 2
    i, lasts, s, fact, num, sign = 0, 0, 1, 1, 1, 1
    while s != lasts:
        lasts = s
        i += 2
        fact *= i * (i-1)
        num *= x * x
        sign *= -1
        s += num / fact * sign
    getcontext().prec -= 2
    return +s

def sin(x):
    """返回以弧度衡量的 x 的正弦。

    泰勒级数近似算法对较小的 x 值效果最好。
    对于较大的值，则先计算 x = x % (2 * pi)。

    >>> print(sin(Decimal('0.5')))
    0.4794255386042030002732879352
    >>> print(sin(0.5))
    0.479425538604
    >>> print(sin(0.5+0j))
    (0.479425538604+0j)

    """
    getcontext().prec += 2
    i, lasts, s, fact, num, sign = 1, 0, x, 1, x, 1
    while s != lasts:
        lasts = s
        i += 2
        fact *= i * (i-1)
        num *= x * x
        sign *= -1
        s += num / fact * sign
    getcontext().prec -= 2
    return +s
```



## Decimal 常见问题

​	Q. 总是输入 `decimal.Decimal('1234.5')` 是否过于笨拙。 在使用交互解释器时有没有最小化输入量的方式？

​	A. 有些用户会将构造器简写为一个字母：



``` python
>>> D = decimal.Decimal
>>> D('1.23') + D('3.45')
Decimal('4.68')
```

​	Q. 在带有两个十进制位的定点数应用中，有些输入值具有许多位，需要被舍入。 另一些数则不应具有多余位，需要验证有效性。 这种情况应该用什么方法？

​	A. 用 [`quantize()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.quantize) 方法舍入到固定数目的十进制位。 如果设置了 [`Inexact`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Inexact) 陷阱，它也适用于验证有效性:



``` python
>>> TWOPLACES = Decimal(10) ** -2       # same as Decimal('0.01')
```



``` python
>>> # Round to two places
>>> Decimal('3.214').quantize(TWOPLACES)
Decimal('3.21')
```



``` python
>>> # Validate that a number does not exceed two places
>>> Decimal('3.21').quantize(TWOPLACES, context=Context(traps=[Inexact]))
Decimal('3.21')
```



``` python
>>> Decimal('3.214').quantize(TWOPLACES, context=Context(traps=[Inexact]))
Traceback (most recent call last):
   ...
Inexact: None
```

​	Q. 当我使用两个有效位的输入时，我要如何在一个应用中保持有效位不变？

​	A. 某些运算如与整数相加、相减和相乘将会自动保留固定的小数位数。 其他运算，如相除和非整数相乘则会改变小数位数，需要再加上 [`quantize()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.quantize) 处理步骤:



``` python
>>> a = Decimal('102.72')           # Initial fixed-point values
>>> b = Decimal('3.17')
>>> a + b                           # Addition preserves fixed-point
Decimal('105.89')
>>> a - b
Decimal('99.55')
>>> a * 42                          # So does integer multiplication
Decimal('4314.24')
>>> (a * b).quantize(TWOPLACES)     # Must quantize non-integer multiplication
Decimal('325.62')
>>> (b / a).quantize(TWOPLACES)     # And quantize division
Decimal('0.03')
```

​	在开发定点数应用时，更方便的做法是定义处理 [`quantize()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.quantize) 步骤的函数:



``` python
>>> def mul(x, y, fp=TWOPLACES):
...     return (x * y).quantize(fp)
...
>>> def div(x, y, fp=TWOPLACES):
...     return (x / y).quantize(fp)
```



``` python
>>> mul(a, b)                       # Automatically preserve fixed-point
Decimal('325.62')
>>> div(b, a)
Decimal('0.03')
```

​	Q. 表示同一个值有许多方式。 数字 `200`, `200.000`, `2E2` 和 `.02E+4` 都具有相同的值但其精度不同。 是否有办法将它们转换为一个可识别的规范值？

​	A. [`normalize()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal.normalize) 方法可将所有相等的值映射为单一表示形式:



``` python
>>> values = map(Decimal, '200 200.000 2E2 .02E+4'.split())
>>> [v.normalize() for v in values]
[Decimal('2E+2'), Decimal('2E+2'), Decimal('2E+2'), Decimal('2E+2')]
```

​	Q. 计算中的舍入是在什么时候发生的？

​	A. 是在计算 *之后* 发生的。 decimal 设计规范认为数字应当被视为是精确的并且是不依赖于当前上下文而创建的。 它们甚至可以具有比当前上下文更高的精确度。 计算过程将使用精确的输入然后再对计算的 *结果* 应用舍入（或其他的上下文操作）:



``` python
>>> getcontext().prec = 5
>>> pi = Decimal('3.1415926535')   # 超过 5 个数位
>>> pi                             # 所有数位都将保留
Decimal('3.1415926535')
>>> pi + 0                         # 加法运算后将执行舍入
Decimal('3.1416')
>>> pi - Decimal('0.00005')        # 减去未舍入的数值，然后执行舍入
Decimal('3.1415')
>>> pi + 0 - Decimal('0.00005').   # 中间值将执行舍入
Decimal('3.1416')
```

​	Q. 有些十进制值总是被打印为指数表示形式。 是否有办法得到一个非指数表示形式？

​	A. 对于某些值来说，指数表示法是表示系数中有效位的唯一方式。 例如，将 `5.0E+3` 表示为 `5000` 可以让值保持恒定但是无法显示原本的两位有效位。

​	如果一个应用不必关心追踪有效位，则可以很容易地移除指数和末尾的零，丢弃有效位但让值保持不变：



``` python
>>> def remove_exponent(d):
...     return d.quantize(Decimal(1)) if d == d.to_integral() else d.normalize()
```



``` python
>>> remove_exponent(Decimal('5E+3'))
Decimal('5000')
```

​	Q. 是否有办法将一个普通浮点数转换为 [`Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal)？

​	A. 是的，任何二进制浮点数都可以精确地表示为 Decimal 值，但完全精确的转换可能需要比平常感觉更高的精度：



``` python
>>> Decimal(math.pi)
Decimal('3.141592653589793115997963468544185161590576171875')
```

​	Q. 在一个复杂的计算中，我怎样才能保证不会得到由精度不足和舍入异常所导致的虚假结果。

​	A. 使用 decimal 模块可以很容易地检测结果。 最好的做法是使用更高的精度和不同的舍入模式重新进行计算。 明显不同的结果表明存在精度不足、舍入模式问题、不符合条件的输入或是结果不稳定的算法。

​	Q. 我发现上下文精度的应用只针对运算结果而不针对输入。在混合使用不同精度的值时有什么需要注意的吗？

​	A. 是的。 原则上所有值都会被视为精确值，在这些值上进行的算术运算也是如此。 只有结果会被舍入。 对于输入来说其好处是“所输入即所得”。 而其缺点则是如果你忘记了输入没有被舍入，结果看起来可能会很奇怪：



``` python
>>> getcontext().prec = 3
>>> Decimal('3.104') + Decimal('2.104')
Decimal('5.21')
>>> Decimal('3.104') + Decimal('0.000') + Decimal('2.104')
Decimal('5.20')
```

​	解决办法是提高精度或使用单目加法运算对输入执行强制舍入：



``` python
>>> getcontext().prec = 3
>>> +Decimal('1.23456789')      # 单目取正运算符将触发舍入
Decimal('1.23')
```

​	此外，还可以使用 [`Context.create_decimal()`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Context.create_decimal) 方法在创建输入时执行舍入：



``` python
>>> Context(prec=5, rounding=ROUND_DOWN).create_decimal('1.2345678')
Decimal('1.2345')
```

​	Q. CPython 实现对于巨大数字是否足够快速？

​	A. 是的。 在 CPython 和 PyPy3 实现中，decimal 模块的 C/CFFI 版本集成了高速 [libmpdec](https://www.bytereef.org/mpdecimal/doc/libmpdec/index.html) 库用于实现任意精度正确舍入的十进制浮点算术 [[1\]](https://docs.python.org/zh-cn/3.13/library/decimal.html#id4)。 `libmpdec` 会对中等大小的数字使用 [Karatsuba 乘法](https://en.wikipedia.org/wiki/Karatsuba_algorithm) 而对非常巨大的数字使用 [数字原理变换](https://en.wikipedia.org/wiki/Discrete_Fourier_transform_(general)#Number-theoretic_transform)。

​	上下文必须针对任意精度算术进行适配。 `Emin` 和 `Emax` 应当总是被设为最大值，`clamp` 应当总是为 0 (默认值)。 设置 `prec` 需要十分谨慎。requires some care.

​	进行大数字算术的最便捷方式同样是使用 `prec` 的最大值 [[2\]](https://docs.python.org/zh-cn/3.13/library/decimal.html#id5):



``` python
>>> setcontext(Context(prec=MAX_PREC, Emax=MAX_EMAX, Emin=MIN_EMIN))
>>> x = Decimal(2) ** 256
>>> x / 128
Decimal('904625697166532776746648320380374280103671755200316906558262375061821325312')
```

​	对于不精确的结果，在 64 位平台上 [`MAX_PREC`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.MAX_PREC) 的值太大了，可用的内存将会不足:



``` python
>>> Decimal(1) / 3
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
MemoryError
```

​	在具有超量分配的系统上 (如 Linux)，一种更复杂的方式是根据可用的 RAM 大小来调整 `prec`。 假设你有 8GB 的 RAM 并期望同时有 10 个操作数，每个最多使用 500MB:



``` python
>>> import sys
>>>
>>> # 字长 8 字节使用 500MB 的单个操作数的最大位数
>>> # 每字 19 个数位（32 位构建版则为字长 4 字节每字 9 个数位）：
>>> maxdigits = 19 * ((500 * 1024**2) // 8)
>>>
>>> # 检测此代码有效。
>>> c = Context(prec=maxdigits, Emax=MAX_EMAX, Emin=MIN_EMIN)
>>> c.traps[Inexact] = True
>>> setcontext(c)
>>>
>>> # 以数字九填满可用的精度：
>>> x = Decimal(0).logical_invert() * 9
>>> sys.getsizeof(x)
524288112
>>> x + 2
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  decimal.Inexact: [<class 'decimal.Inexact'>]
```

​	总体而言（特别是在没有超量分配的系统上），如果期望所有计算都是精确的则推荐预估更严格的边界并设置 [`Inexact`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Inexact) 陷阱。

[[1](https://docs.python.org/zh-cn/3.13/library/decimal.html#id2)]

> Added in version 3.3.
>

[[2](https://docs.python.org/zh-cn/3.13/library/decimal.html#id3)]

*在 3.9 版本发生变更:* 此方式现在适用于除了非整数乘方以外的所有精确结果。
