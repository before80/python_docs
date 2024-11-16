+++
title = "cmath --- 针对复数的数学函数"
date = 2024-11-15T11:42:26+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/cmath.html](https://docs.python.org/zh-cn/3.13/library/cmath.html)
>
> 收录该文档的时间：`2024-11-15T11:42:26+08:00`

# `cmath` --- 针对复数的数学函数

------

​	本模块提供了一些适用于复数的数学函数。 本模块中的函数接受整数、浮点数或复数作为参数。 它们也接受任意具有 [`__complex__()`]({{< ref "/reference/datamodel#object.__complex__" >}}) 或 [`__float__()`]({{< ref "/reference/datamodel#object.__float__" >}}) 方法的 Python 对象：这些方法分别用于将对象转换为复数或浮点数，然后再将函数应用于转换后的结果。

​	备注

 

​	对于涉及分支切割的函数，我们会有确定如何在切割本身上定义这些函数的问题。 根据 Kahan 的论文 "Branch cuts for complex elementary functions"，以及 C99 的附录 G 和之后的 C 标准，我们使用零符号来区别分支切割的一侧和另一侧：对于沿实轴（一部分）的分支切割我们要看虚部的符号，而对于沿虚轴的分支切割我们则要看实部的符号。

​	例如，[`cmath.sqrt()`]({{< ref "/library/numeric/cmath#cmath.sqrt" >}}) 函数有一个沿着负实轴的分支切割。 参数 `complex(-2.0, -0.0)` 会被当作位于切支切割的 *下方* 来处理，因而将给出一个负虚轴上的结果。



``` python
>>> cmath.sqrt(complex(-2.0, -0.0))
-1.4142135623730951j
```

​	但是参数 `complex(-2.0, 0.0)` 则会被当作是位于支割线的上方来处理:



``` python
>>> cmath.sqrt(complex(-2.0, 0.0))
1.4142135623730951j
```

## 到极坐标和从极坐标的转换

​	Python 复数 `z` 是使用 *直角* 或 *笛卡尔* 坐标在内部存储的。 这完全取决于其 *实部* `z.real` 及其 *虚部* `z.imag` 的值。

*极坐标* 提供了另一种复数的表示方法。在极坐标中，一个复数 *z* 由模量 *r* 和相位角 *phi* 来定义。模量 *r* 是从 *z* 到坐标原点的距离，而相位角 *phi* 是以弧度为单位的，逆时针的，从正X轴到连接原点和 *z* 的线段间夹角的角度。

​	下面的函数可用于原生直角坐标与极坐标的相互转换。

## cmath.**phase**(*x*)

​	将 *x* 的相位 (或称 *x* 的 *参数*) 作为一个浮点数返回。 `phase(x)` 等价于 `math.atan2(x.imag, x.real)`。 结果将位于 [-*π*, *π*] 范围内，且此操作的支割线将位于负实轴上。 结果的符号将与 `x.imag` 的符号相同，即使 `x.imag` 的值为零:



``` python
>>> phase(complex(-1.0, 0.0))
3.141592653589793
>>> phase(complex(-1.0, -0.0))
-3.141592653589793
```

​	备注

 

​	一个复数 *x* 的模数（绝对值）可以通过内置函数 [`abs()`]({{< ref "/library/functions#abs" >}}) 计算。没有单独的 [`cmath`]({{< ref "/library/numeric/cmath#module-cmath" >}}) 模块函数用于这个操作。

## cmath.**polar**(*x*)

​	在极坐标中返回 *x* 的表达方式。返回一个数对 `(r, phi)`，*r* 是 *x* 的模数，*phi* 是 *x* 的相位角。 `polar(x)` 相当于 `(abs(x), phase(x))`。

## cmath.**rect**(*r*, *phi*)

​	使用极坐标形式 *r* 和 *phi* 返回复数 *x* 的值。 相当于 `complex(r * math.cos(phi), r * math.sin(phi))`。

## 幂函数与对数函数

## cmath.**exp**(*x*)

​	返回 *e* 的 *x* 次方，*e* 是自然对数的底数。

## cmath.**log**(*x*[, *base*])

​	返回 *x* 的以 *base* 为底的对数。 如果没有指定 *base*，则返回 *x* 的自然对数。 存在一条支割线，即沿着负实轴从 0 到 -∞。

## cmath.**log10**(*x*)

​	返回底数为 10 的 *x* 的对数。它具有与 [`log()`]({{< ref "/library/numeric/cmath#cmath.log" >}}) 相同的支割线。

## cmath.**sqrt**(*x*)

​	返回 *x* 的平方根。 它具有与 [`log()`]({{< ref "/library/numeric/cmath#cmath.log" >}}) 相同的支割线。

## 三角函数

## cmath.**acos**(*x*)

​	返回 *x* 的反余弦。 存在两条支割线：一条沿着实轴从 1 到 ∞。 另一条沿着实轴从 -1 向左延伸到 -∞。

## cmath.**asin**(*x*)

​	返回 *x* 的反正弦。它与 [`acos()`]({{< ref "/library/numeric/cmath#cmath.acos" >}}) 有相同的支割线。

## cmath.**atan**(*x*)

​	返回 *x* 的反正切。 存在两条支割线：一条沿着虚轴从 `1j` 延伸到 `∞j`。 另一条沿着虚轴从 `-1j` 延伸到 `-∞j`。

## cmath.**cos**(*x*)

​	返回 *x* 的余弦。

## cmath.**sin**(*x*)

​	返回 *x* 的正弦。

## cmath.**tan**(*x*)

​	返回 *x* 的正切。

## 双曲函数

## cmath.**acosh**(*x*)

​	返回 *x* 的反双曲余弦。 存在一条支割线，沿着实轴从 1 向左延伸到 -∞。

## cmath.**asinh**(*x*)

​	返回 *x* 的反双曲正弦。 存在两条支割线：一条沿着虚轴从 `1j` 延伸到 `∞j`。 另一条沿着虚轴从 `-1j` 延伸到 `-∞j`。

## cmath.**atanh**(*x*)

​	返回 *x* 反双曲正切。 存在两条支割线：一条沿着实轴从 `1` 延伸到 `∞`。 另一条沿着实轴从 `-1` 延伸到 `-∞`。

## cmath.**cosh**(*x*)

​	返回 *x* 的双曲余弦值。

## cmath.**sinh**(*x*)

​	返回 *x* 的双曲正弦值。

## cmath.**tanh**(*x*)

​	返回 *x* 的双曲正切值。

## 分类函数

## cmath.**isfinite**(*x*)

​	如果 *x* 的实部和虚部都是有限的，则返回 `True`，否则返回 `False`。

> Added in version 3.2.
>

## cmath.**isinf**(*x*)

​	如果 *x* 的实部或者虚部是无穷大的，则返回 `True`，否则返回 `False`。

## cmath.**isnan**(*x*)

​	如果 *x* 的实部或者虚部是 NaN，则返回 `True` ，否则返回 `False`。

## cmath.**isclose**(*a*, *b*, ***, *rel_tol=1e-09*, *abs_tol=0.0*)

​	若 *a* 和 *b* 的值比较接近则返回 `True`，否则返回 `False`。

​	Whether or not two values are considered close is determined according to given absolute and relative tolerances. If no errors occur, the result will be: `abs(a-b) <= max(rel_tol * max(abs(a), abs(b)), abs_tol)`.

*rel_tol* is the relative tolerance -- it is the maximum allowed difference between *a* and *b*, relative to the larger absolute value of *a* or *b*. For example, to set a tolerance of 5%, pass `rel_tol=0.05`. The default tolerance is `1e-09`, which assures that the two values are the same within about 9 decimal digits. *rel_tol* must be nonnegative and less than `1.0`.

*abs_tol* is the absolute tolerance; it defaults to `0.0` and it must be nonnegative. When comparing `x` to `0.0`, `isclose(x, 0)` is computed as `abs(x) <= rel_tol * abs(x)`, which is `False` for any `x` and rel_tol less than `1.0`. So add an appropriate positive abs_tol argument to the call.

​	IEEE 754特殊值 `NaN` ， `inf` 和 `-inf` 将根据IEEE规则处理。具体来说， `NaN` 不被认为接近任何其他值，包括 `NaN` 。 `inf` 和 `-inf` 只被认为接近自己。

> Added in version 3.5.
>

​	参见

 

[**PEP 485**](https://peps.python.org/pep-0485/) —— 用于测试近似相等的函数

## 常量

## cmath.**pi**

​	数学常数 *π* ，作为一个浮点数。

## cmath.**e**

​	数学常数 *e* ，作为一个浮点数。

## cmath.**tau**

​	数学常数 *τ* ，作为一个浮点数。

> Added in version 3.6.
>

## cmath.**inf**

​	浮点正无穷大。相当于 `float('inf')`。

> Added in version 3.6.
>

## cmath.**infj**

​	具有零实部和正无穷虚部的复数。相当于 `complex(0.0, float('inf'))`。

> Added in version 3.6.
>

## cmath.**nan**

​	浮点“非数字”（NaN）值。相当于 `float('nan')`。

> Added in version 3.6.
>

## cmath.**nanj**

​	具有零实部和 NaN 虚部的复数。相当于 `complex(0.0, float('nan'))`。

> Added in version 3.6.
>

​	请注意，函数的选择与模块 [`math`]({{< ref "/library/numeric/math#module-math" >}}) 中的函数选择相似，但不完全相同。 拥有两个模块的原因是因为有些用户对复数不感兴趣，甚至根本不知道它们是什么。它们宁愿 `math.sqrt(-1)` 引发异常，也不想返回一个复数。 另请注意，被 [`cmath`]({{< ref "/library/numeric/cmath#module-cmath" >}}) 定义的函数始终会返回一个复数，尽管答案可以表示为一个实数（在这种情况下，复数的虚数部分为零）。

​	关于支割线的注释：它们是沿着给定函数无法连续的曲线。它们是许多复变函数的必要特征。 假设您需要使用复变函数进行计算，您将会了解支割线的概念。 请参阅几乎所有关于复变函数的（不太基本）的书来获得启发。 对于如何正确地基于数值目的来选择支割线的相关信息，一个良好的参考如下：

​	参见

 

​	Kahan, W: Branch cuts for complex elementary functions; or, Much ado about nothing's sign bit. In Iserles, A., and Powell, M. (eds.), The state of the art in numerical analysis. Clarendon Press (1987) pp165--211.
