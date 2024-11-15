+++
title = "fractions --- 有理数"
date = 2024-11-15T11:42:26+08:00
weight = 40
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/fractions.html](https://docs.python.org/zh-cn/3.13/library/fractions.html)
>
> 收录该文档的时间：`2024-11-15T11:42:26+08:00`

# `fractions` --- 有理数

**源代码** [Lib/fractions.py](https://github.com/python/cpython/tree/3.13/Lib/fractions.py)

------

[`fractions`](https://docs.python.org/zh-cn/3.13/library/fractions.html#module-fractions) 模块支持分数运算。

​	分数实例可以由一对整数，一个分数，或者一个字符串构建而成。

## *class* fractions.**Fraction**(*numerator=0*, *denominator=1*)

## *class* fractions.**Fraction**(*other_fraction*)

## *class* fractions.**Fraction**(*float*)

## *class* fractions.**Fraction**(*decimal*)

## *class* fractions.**Fraction**(*string*)

​	第一个版本要求 *numerator* 和 *denominator* 是 [`numbers.Rational`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Rational) 的实例，并返回一个值为 `numerator/denominator` 的新 [`Fraction`](https://docs.python.org/zh-cn/3.13/library/fractions.html#fractions.Fraction) 实例。 如果 *denominator* 是 `0` 则会引发 [`ZeroDivisionError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ZeroDivisionError)。 第二个版本要求 *other_fraction* 是 [`numbers.Rational`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Rational) 的实例，并返回具有相同值的 [`Fraction`](https://docs.python.org/zh-cn/3.13/library/fractions.html#fractions.Fraction) 实例。 接下来的两个版本接受 [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float) 或 [`decimal.Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 实例，并返回具有完全相同值的 [`Fraction`](https://docs.python.org/zh-cn/3.13/library/fractions.html#fractions.Fraction) 实例。 请注意由于二进制浮点运算通常存在的问题 (参见 [浮点算术：争议和限制](https://docs.python.org/zh-cn/3.13/tutorial/floatingpoint.html#tut-fp-issues))，`Fraction(1.1)` 的参数并不完全等于 11/10，因此 `Fraction(1.1)` 也 *不会* 像人们所期望的那样返回 `Fraction(11, 10)`。 （请参阅下面 [`limit_denominator()`](https://docs.python.org/zh-cn/3.13/library/fractions.html#fractions.Fraction.limit_denominator) 方法的文档。） 最后一个版本的构造器接受一个字符串或 unicode 实例。 该实例的通常形式为:

```
[sign] numerator ['/' denominator]
```

​	其中的可选项 `sign` 可能为 '+' 或 '-' 且 `numerator` 和 `denominator` (如果存在) 是十进制数码的字符串 (可以如代码中的整数字面值一样使用下划线来分隔数码)。 此外，[`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float) 构造器所接受的任何代表一个有限值的字符串也都为 [`Fraction`](https://docs.python.org/zh-cn/3.13/library/fractions.html#fractions.Fraction) 构造器所接受。 不论哪 种形式的输入字符串也都可以带有开头和/或末尾空格符。 这里是一些示例:



```
>>> from fractions import Fraction
>>> Fraction(16, -10)
Fraction(-8, 5)
>>> Fraction(123)
Fraction(123, 1)
>>> Fraction()
Fraction(0, 1)
>>> Fraction('3/7')
Fraction(3, 7)
>>> Fraction(' -3/7 ')
Fraction(-3, 7)
>>> Fraction('1.414213 \t\n')
Fraction(1414213, 1000000)
>>> Fraction('-.125')
Fraction(-1, 8)
>>> Fraction('7e-6')
Fraction(7, 1000000)
>>> Fraction(2.25)
Fraction(9, 4)
>>> Fraction(1.1)
Fraction(2476979795053773, 2251799813685248)
>>> from decimal import Decimal
>>> Fraction(Decimal('1.1'))
Fraction(11, 10)
```

[`Fraction`](https://docs.python.org/zh-cn/3.13/library/fractions.html#fractions.Fraction) 类继承自抽象基类 [`numbers.Rational`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Rational)，并实现了该类的所有方法和操作。 [`Fraction`](https://docs.python.org/zh-cn/3.13/library/fractions.html#fractions.Fraction) 实例是 [hashable](https://docs.python.org/zh-cn/3.13/glossary.html#term-hashable) 对象，并应当被视为不可变对象。 此外，[`Fraction`](https://docs.python.org/zh-cn/3.13/library/fractions.html#fractions.Fraction) 还具有以下特征属性和方法:

*在 3.2 版本发生变更:* [`Fraction`](https://docs.python.org/zh-cn/3.13/library/fractions.html#fractions.Fraction) 构造器现在接受 [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float) 和 [`decimal.Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 实例。

*在 3.9 版本发生变更:* 现在会使用 [`math.gcd()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.gcd) 函数来正规化 *numerator* 和 *denominator*。 [`math.gcd()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.gcd) 总是返回 [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int) 类型。 在之前版本中，GCD 的类型取决于 *numerator* 和 *denominator* 的类型。

*在 3.11 版本发生变更:* 现在当使用字符串创建 [`Fraction`](https://docs.python.org/zh-cn/3.13/library/fractions.html#fractions.Fraction) 实例时已允许使用下划线，遵循 [**PEP 515**](https://peps.python.org/pep-0515/) 规则。

*在 3.11 版本发生变更:* [`Fraction`](https://docs.python.org/zh-cn/3.13/library/fractions.html#fractions.Fraction) 现在实现了 `__int__` 以满足 `typing.SupportsInt` 实例检测。

*在 3.12 版本发生变更:* 允许字符串输入在斜杠两边添加空格: `Fraction('2 / 3')`。

*在 3.12 版本发生变更:* [`Fraction`](https://docs.python.org/zh-cn/3.13/library/fractions.html#fractions.Fraction) 实例现在支持浮点风格的格式化，使用 `"e"`, `"E"`, `"f"`, `"F"`, `"g"`, `"G"` 和 `"%""` 等表示类型。.

*在 3.13 版本发生变更:* 没有表示类型的 [`Fraction`](https://docs.python.org/zh-cn/3.13/library/fractions.html#fractions.Fraction) 实例的格式化现在支持填充、对齐、正负号处理、最小宽度和分组。

## **numerator**

​	最简分数形式的分子。

## **denominator**

​	最简分数形式的分母。

## **as_integer_ratio**()

​	返回由两个整数组成的元组，两数之比等于原 Fraction 的值且其分母为正数。

*Added in version 3.8.*

## **is_integer**()

​	如果 Fraction 为整数则返回 `True`。

*Added in version 3.12.*

## *classmethod* **from_float**(*flt*)

​	只接受 [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float) 或 [`numbers.Integral`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Integral) 实例的替代性构造器。 请注意 `Fraction.from_float(0.3)` 与 `Fraction(3, 10)` 的值是不同的。

​	备注

 

​	从 Python 3.2 开始，在构造 [`Fraction`](https://docs.python.org/zh-cn/3.13/library/fractions.html#fractions.Fraction) 实例时可以直接使用 [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float)。

## *classmethod* **from_decimal**(*dec*)

​	只接受 [`decimal.Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 或 [`numbers.Integral`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Integral) 实例的替代性构造器。

​	备注

 

​	从 Python 3.2 开始，在构造 [`Fraction`](https://docs.python.org/zh-cn/3.13/library/fractions.html#fractions.Fraction) 实例时可以直接使用 [`decimal.Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 实例。

## **limit_denominator**(*max_denominator=1000000*)

​	找到并返回一个 [`Fraction`](https://docs.python.org/zh-cn/3.13/library/fractions.html#fractions.Fraction) 使得其值最接近 `self` 并且分母不大于 max_denominator。 此方法适用于找出给定浮点数的有理数近似值：



```
>>> from fractions import Fraction
>>> Fraction('3.1415926535897932').limit_denominator(1000)
Fraction(355, 113)
```

​	或是用来恢复被表示为一个浮点数的有理数：



```
>>> from math import pi, cos
>>> Fraction(cos(pi/3))
Fraction(4503599627370497, 9007199254740992)
>>> Fraction(cos(pi/3)).limit_denominator()
Fraction(1, 2)
>>> Fraction(1.1).limit_denominator()
Fraction(11, 10)
```

## **__floor__**()

​	返回最大的 [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int) `<= self`。 此方法也可通过 [`math.floor()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.floor) 函数来使用：



```
>>> from math import floor
>>> floor(Fraction(355, 113))
3
```

## **__ceil__**()

​	返回最小的 [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int) `>= self`。 此方法也可通过 [`math.ceil()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.ceil) 函数来使用。

## **__round__**()

## **__round__**(*ndigits*)

​	第一个版本返回一个 [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int) 使得其值最接近 `self`，位值为二分之一时只对偶数舍入。第二个版本会将 `self` 舍入到最接近 `Fraction(1, 10**ndigits)` 的倍数（如果 `ndigits` 为负值则为逻辑运算），位值为二分之一时同样只对偶数舍入。 此方法也可通过 [`round()`](https://docs.python.org/zh-cn/3.13/library/functions.html#round) 函数来使用。

## **__format__**(*format_spec*, */*)

​	通过 [`str.format()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str.format) 方法、[`format()`](https://docs.python.org/zh-cn/3.13/library/functions.html#format) 内置函数或 [格式化字符串字面值](https://docs.python.org/zh-cn/3.13/reference/lexical_analysis.html#f-strings) 提供对 [`Fraction`](https://docs.python.org/zh-cn/3.13/library/fractions.html#fractions.Fraction) 实例格式化的支持。

​	如果 `format_spec` 格式说明字符串末尾不带表示类型 `'e'`, `'E'`, `'f'`, `'F'`, `'g'`, `'G'` 或 `'%'` 之一则格式化操作将遵循在 [格式说明微语言](https://docs.python.org/zh-cn/3.13/library/string.html#formatspec) 中描述的有关填充、对齐、正负号处理、最小宽度和分组的一般规则。 “替代形式”旗标 `'#'` 也是受支持的：如果提供，将强制输出字符串始终包括一个显式的分母，即使被格式化的值恰好为整数也是如此。 表示填充零值的旗标 `'0'` 是不被支持的。

​	如果 `format_spec` 格式说明字符串末尾带有表示类型 `'e'`, `'E'`, `'f'`, `'F'`, `'g'`, `'G'` 或 `'%'` 之一那么格式化操作将遵循在 [格式规格迷你语言](https://docs.python.org/zh-cn/3.13/library/string.html#formatspec) 小节中针对 [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float) 类型所描述的规则。

​	这是一些例子:



```
>>> from fractions import Fraction
>>> format(Fraction(103993, 33102), '_')
'103_993/33_102'
>>> format(Fraction(1, 7), '.^+10')
'...+1/7...'
>>> format(Fraction(3, 1), '')
'3'
>>> format(Fraction(3, 1), '#')
'3/1'
>>> format(Fraction(1, 7), '.40g')
'0.1428571428571428571428571428571428571429'
>>> format(Fraction('1234567.855'), '_.2f')
'1_234_567.86'
>>> f"{Fraction(355, 113):*>20.6e}"
'********3.141593e+00'
>>> old_price, new_price = 499, 672
>>> "{:.2%} price increase".format(Fraction(new_price, old_price) - 1)
'34.67% price increase'
```

​	参见

[`numbers`](https://docs.python.org/zh-cn/3.13/library/numbers.html#module-numbers) 模块

​	构成数字塔的所有抽象基类。
