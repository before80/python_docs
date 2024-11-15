+++
title = "math --- 数学函数"
date = 2024-11-15T11:42:26+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/math.html](https://docs.python.org/zh-cn/3.13/library/math.html)
>
> 收录该文档的时间：`2024-11-15T11:42:26+08:00`

# `math` --- 数学函数

------

​	该模块提供了对C标准定义的数学函数的访问。

​	这些函数不适用于复数；如果你需要计算复数，请使用 [`cmath`](https://docs.python.org/zh-cn/3.13/library/cmath.html#module-cmath) 模块中的同名函数。将支持计算复数的函数区分开的目的，来自于大多数开发者并不愿意像数学家一样需要学习复数的概念。得到一个异常而不是一个复数结果使得开发者能够更早地监测到传递给这些函数的参数中包含复数，进而调查其产生的原因。

​	该模块提供了以下函数。除非另有明确说明，否则所有返回值均为浮点数。

| **Number-theoretic and representation functions**            |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [`ceil(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.ceil) | Ceiling of *x*, the smallest integer greater than or equal to *x* |
| [`comb(n, k)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.comb) | Number of ways to choose *k* items from *n* items without repetition and without order |
| [`copysign(x, y)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.copysign) | Magnitude (absolute value) of *x* with the sign of *y*       |
| [`fabs(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.fabs) | Absolute value of *x*                                        |
| [`factorial(n)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.factorial) | *n* factorial                                                |
| [`floor (x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.floor) | Floor of *x*, the largest integer less than or equal to *x*  |
| [`fma(x, y, z)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.fma) | Fused multiply-add operation: `(x * y) + z`                  |
| [`fmod(x, y)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.fmod) | Remainder of division `x / y`                                |
| [`frexp(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.frexp) | Mantissa and exponent of *x*                                 |
| [`fsum(iterable)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.fsum) | Sum of values in the input *iterable*                        |
| [`gcd(*integers)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.gcd) | Greatest common divisor of the integer arguments             |
| [`isclose(a, b, rel_tol, abs_tol)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.isclose) | Check if the values *a* and *b* are close to each other      |
| [`isfinite(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.isfinite) | Check if *x* is neither an infinity nor a NaN                |
| [`isinf(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.isinf) | Check if *x* is a positive or negative infinity              |
| [`isnan(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.isnan) | Check if *x* is a NaN (not a number)                         |
| [`isqrt(n)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.isqrt) | Integer square root of a nonnegative integer *n*             |
| [`lcm(*integers)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.lcm) | Least common multiple of the integer arguments               |
| [`ldexp(x, i)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.ldexp) | `x * (2**i)`, inverse of function [`frexp()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.frexp) |
| [`modf(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.modf) | Fractional and integer parts of *x*                          |
| [`nextafter(x, y, steps)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.nextafter) | Floating-point value *steps* steps after *x* towards *y*     |
| [`perm(n, k)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.perm) | Number of ways to choose *k* items from *n* items without repetition and with order |
| [`prod(iterable, start)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.prod) | Product of elements in the input *iterable* with a *start* value |
| [`remainder(x, y)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.remainder) | Remainder of *x* with respect to *y*                         |
| [`sumprod(p, q)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.sumprod) | Sum of products from two iterables *p* and *q*               |
| [`trunc(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.trunc) | Integer part of *x*                                          |
| [`ulp(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.ulp) | Value of the least significant bit of *x*                    |
| **Power and logarithmic functions**                          |                                                              |
| [`cbrt(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.cbrt) | Cube root of *x*                                             |
| [`exp(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.exp) | *e* raised to the power *x*                                  |
| [`exp2(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.exp2) | *2* raised to the power *x*                                  |
| [`expm1(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.expm1) | *e* raised to the power *x*, minus 1                         |
| [`log(x, base)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.log) | Logarithm of *x* to the given base (*e* by default)          |
| [`log1p(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.log1p) | Natural logarithm of *1+x* (base *e*)                        |
| [`log2(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.log2) | Base-2 logarithm of *x*                                      |
| [`log10(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.log10) | Base-10 logarithm of *x*                                     |
| [`pow(x, y)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.pow) | *x* raised to the power *y*                                  |
| [`sqrt(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.sqrt) | Square root of *x*                                           |
| **Trigonometric functions**                                  |                                                              |
| [`acos(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.acos) | Arc cosine of *x*                                            |
| [`asin(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.asin) | Arc sine of *x*                                              |
| [`atan(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.atan) | Arc tangent of *x*                                           |
| [`atan2(y, x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.atan2) | `atan(y / x)`                                                |
| [`cos(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.cos) | Cosine of *x*                                                |
| [`dist(p, q)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.dist) | Euclidean distance between two points *p* and *q* given as an iterable of coordinates |
| [`hypot(*coordinates)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.hypot) | Euclidean norm of an iterable of coordinates                 |
| [`sin(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.sin) | Sine of *x*                                                  |
| [`tan(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.tan) | Tangent of *x*                                               |
| **Angular conversion**                                       |                                                              |
| [`degrees(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.degrees) | Convert angle *x* from radians to degrees                    |
| [`radians(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.radians) | Convert angle *x* from degrees to radians                    |
| **Hyperbolic functions**                                     |                                                              |
| [`acosh(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.acosh) | Inverse hyperbolic cosine of *x*                             |
| [`asinh(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.asinh) | Inverse hyperbolic sine of *x*                               |
| [`atanh(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.atanh) | Inverse hyperbolic tangent of *x*                            |
| [`cosh(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.cosh) | Hyperbolic cosine of *x*                                     |
| [`sinh(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.sinh) | Hyperbolic sine of *x*                                       |
| [`tanh(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.tanh) | Hyperbolic tangent of *x*                                    |
| **Special functions**                                        |                                                              |
| [`erf(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.erf) | [Error function](https://en.wikipedia.org/wiki/Error_function) at *x* |
| [`erfc(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.erfc) | [Complementary error function](https://en.wikipedia.org/wiki/Error_function) at *x* |
| [`gamma(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.gamma) | [Gamma function](https://en.wikipedia.org/wiki/Gamma_function) at *x* |
| [`lgamma(x)`](https://docs.python.org/zh-cn/3.13/library/math.html#math.lgamma) | Natural logarithm of the absolute value of the [Gamma function](https://en.wikipedia.org/wiki/Gamma_function) at *x* |
| **Constants**                                                |                                                              |
| [`pi`](https://docs.python.org/zh-cn/3.13/library/math.html#math.pi) | *π* = 3.141592...                                            |
| [`e`](https://docs.python.org/zh-cn/3.13/library/math.html#math.e) | *e* = 2.718281...                                            |
| [`tau`](https://docs.python.org/zh-cn/3.13/library/math.html#math.tau) | *τ* = 2*π* = 6.283185...                                     |
| [`inf`](https://docs.python.org/zh-cn/3.13/library/math.html#math.inf) | Positive infinity                                            |
| [`nan`](https://docs.python.org/zh-cn/3.13/library/math.html#math.nan) | "Not a number" (NaN)                                         |

## 数论与表示函数

## math.**ceil**(*x*)

​	返回 *x* 的向上取整，即大于或等于 *x* 的最小的整数。如果 *x* 不是浮点数，委托给 [`x.__ceil__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__ceil__) ，它应该返回一个 [`Integral`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Integral) 的值。

## math.**comb**(*n*, *k*)

​	返回不重复且无顺序地从 *n* 项中选择 *k* 项的方式总数。

​	当 `k <= n` 时取值为 `n! / (k! * (n - k)!)`；当 `k > n` 时取值为零。

​	也称为二项式系数，因为它等价于 `(1 + x)ⁿ` 的多项式展开中第 k 项的系数。

​	如果任一参数不为整数则会引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。 如果任一参数为负数则会引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

*Added in version 3.8.*

## math.**copysign**(*x*, *y*)

​	返回一个基于 *x* 的绝对值和 *y* 的符号的浮点数。在支持带符号零的平台上，`copysign(1.0, -0.0)` 返回 *-1.0*.

## math.**fabs**(*x*)

​	返回 *x* 的绝对值。

## math.**factorial**(*n*)

​	将 *n* 的阶乘作为整数返回。 如果 *n* 不是正数或为负值则会引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

*在 3.10 版本发生变更:* 具有整数值的浮点数 (如 `5.0`) 将不再被接受。

## math.**floor**(*x*)

​	返回 *x* 的向下取整，小于或等于 *x* 的最大整数。如果 *x* 不是浮点数，则委托给 [`x.__floor__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__floor__) ，它应返回一个 [`Integral`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Integral) 值。

## math.**fma**(*x*, *y*, *z*)

​	融合的乘法-加法运算。 返回 `(x * y) + z`，类似于使用无限精度和取值范围进行计算然后执行一次舍入到 `float` 格式。 此运算往往可提供比直接使用表达式 `(x * y) + z` 更高的精确度。

​	此函数遵循在 IEEE 754 标准中描述的 fusedMultiplyAdd 运算规范。 该标准定义了相同场景的统一实现，即 `fma(0, inf, nan)` 和 `fma(inf, 0, nan)`。 在这些场景中，`math.fma` 将返回 NaN，且不会引发任何异常。

*Added in version 3.13.*

## math.**fmod**(*x*, *y*)

​	返回 `fmod(x, y)` ，由平台C库定义。请注意，Python表达式 `x % y` 可能不会返回相同的结果。C标准的目的是 `fmod(x, y)` 完全（数学上；到无限精度）等于 `x - n*y` 对于某个整数 *n* ，使得结果具有 与 *x* 相同的符号和小于 `abs(y)` 的幅度。Python的 `x % y` 返回带有 *y* 符号的结果，并且可能不能完全计算浮点参数。 例如， `fmod(-1e-100, 1e100)` 是 `-1e-100` ，但Python的 `-1e-100 % 1e100` 的结果是 `1e100-1e-100` ，它不能完全表示为浮点数，并且取整为令人惊讶的 `1e100` 。 出于这个原因，函数 [`fmod()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.fmod) 在使用浮点数时通常是首选，而Python的 `x % y` 在使用整数时是首选。

## math.**frexp**(*x*)

​	以 `(m, e)` 对的形式返回 *x* 的尾数和指数。 *m* 是一个浮点数， *e* 是一个整数，正好是 `x == m * 2**e` 。 如果 *x* 为零，则返回 `(0.0, 0)` ，否则返回 `0.5 <= abs(m) < 1` 。这用于以可移植方式“分离”浮点数的内部表示。

## math.**fsum**(*iterable*)

​	返回可迭代对象中的值的精确浮点总计值。 通过跟踪多个中间部分和来避免精度损失。

​	该算法的准确性取决于IEEE-754算术保证和舍入模式为半偶的典型情况。在某些非Windows版本中，底层C库使用扩展精度添加，并且有时可能会使中间和加倍，导致它在最低有效位中关闭。

​	有关进一步的讨论和两种替代方式，请参阅 [ASPN cookbook recipes for accurate floating-point summation](https://code.activestate.com/recipes/393090-binary-floating-point-summation-accurate-to-full-p/)。

## math.**gcd**(**integers*)

​	返回给定的整数参数的最大公约数。 如果有一个参数非零，则返回值将是能同时整除所有参数的最大正整数。 如果所有参数为零，则返回值为 `0`。 不带参数的 `gcd()` 返回 `0`。

*Added in version 3.5.*

*在 3.9 版本发生变更:* 添加了对任意数量的参数的支持。 之前的版本只支持两个参数。

## math.**isclose**(*a*, *b*, ***, *rel_tol=1e-09*, *abs_tol=0.0*)

​	若 *a* 和 *b* 的值比较接近则返回 `True`，否则返回 `False`。

​	Whether or not two values are considered close is determined according to given absolute and relative tolerances. If no errors occur, the result will be: `abs(a-b) <= max(rel_tol * max(abs(a), abs(b)), abs_tol)`.

*rel_tol* is the relative tolerance -- it is the maximum allowed difference between *a* and *b*, relative to the larger absolute value of *a* or *b*. For example, to set a tolerance of 5%, pass `rel_tol=0.05`. The default tolerance is `1e-09`, which assures that the two values are the same within about 9 decimal digits. *rel_tol* must be nonnegative and less than `1.0`.

*abs_tol* is the absolute tolerance; it defaults to `0.0` and it must be nonnegative. When comparing `x` to `0.0`, `isclose(x, 0)` is computed as `abs(x) <= rel_tol * abs(x)`, which is `False` for any `x` and rel_tol less than `1.0`. So add an appropriate positive abs_tol argument to the call.

​	IEEE 754特殊值 `NaN` ， `inf` 和 `-inf` 将根据IEEE规则处理。具体来说， `NaN` 不被认为接近任何其他值，包括 `NaN` 。 `inf` 和 `-inf` 只被认为接近自己。

*Added in version 3.5.*

​	参见

 

[**PEP 485**](https://peps.python.org/pep-0485/) —— 用于测试近似相等的函数

## math.**isfinite**(*x*)

​	如果 *x* 既不是无穷大也不是NaN，则返回 `True` ，否则返回 `False` 。 （注意 `0.0` 被认为 *是* 有限的。）

*Added in version 3.2.*

## math.**isinf**(*x*)

​	如果 *x* 是正或负无穷大，则返回 `True` ，否则返回 `False` 。

## math.**isnan**(*x*)

​	如果 *x* 是 NaN（不是数字），则返回 `True` ，否则返回 `False` 。

## math.**isqrt**(*n*)

​	返回非负整数 *n* 的整数平方根。 这就是对 *n* 的实际平方根向下取整，或者相当于使得 *a*² ≤ *n* 的最大整数 *a*。

​	对于某些应用来说，可以更适合取值为使得 *n* ≤ *a*² 的最小整数 *a* ，或者换句话说就是 *n* 的实际平方根向上取整。 对于正数 *n*，这可以使用 `a = 1 + isqrt(n - 1)` 来计算。

*Added in version 3.8.*

## math.**lcm**(**integers*)

​	返回给定的整数参数的最小公倍数。 如果所有参数均非零，则返回值将是为所有参数的整数倍的最小正整数。 如果参数之一为零，则返回值为 `0`。 不带参数的 `lcm()` 返回 `1`。

*Added in version 3.9.*

## math.**ldexp**(*x*, *i*)

​	返回 `x * (2**i)` 。 这基本上是函数 [`frexp()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.frexp) 的反函数。

## math.**modf**(*x*)

​	返回 *x* 的小数和整数部分。两个结果都带有 *x* 的符号并且是浮点数。

## math.**nextafter**(*x*, *y*, *steps=1*)

​	返回从 *x* 到 *y* 的步数的浮点值 *steps*。

​	如果 *x* 等于 *y*，则返回 *y*，除非 *steps* 值为零。

​	示例：

- `math.nextafter(x, math.inf)` 的方向朝上：趋向于正无穷。
- `math.nextafter(x, -math.inf)` 的方向朝下：趋向于负无穷。
- `math.nextafter(x, 0.0)` 趋向于零。
- `math.nextafter(x, math.copysign(math.inf, x))` 趋向于零的反方向。

​	另请参阅 [`math.ulp()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.ulp)。

*Added in version 3.9.*

*在 3.12 版本发生变更:* 增加了 *steps* 参数。

## math.**perm**(*n*, *k=None*)

​	返回不重复且有顺序地从 *n* 项中选择 *k* 项的方式总数。

​	当 `k <= n` 时取值为 `n! / (n - k)!`；当 `k > n` 时取值为零。

​	如果 *k* 未指定或为 `None`，则 *k* 默认值为 *n* 并且函数将返回 `n!`。

​	如果任一参数不为整数则会引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。 如果任一参数为负数则会引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

*Added in version 3.8.*

## math.**prod**(*iterable*, ***, *start=1*)

​	计算输入的 *iterable* 中所有元素的积。 积的默认 *start* 值为 `1`。

​	当可迭代对象为空时，返回起始值。 此函数特别针对数字值使用，并会拒绝非数字类型。

*Added in version 3.8.*

## math.**remainder**(*x*, *y*)

​	返回 IEEE 754 风格的 *x* 相对于 *y* 的余数。对于有限 *x* 和有限非零 *y* ，这是差异 `x - n*y` ，其中 `n` 是与商 `x / y` 的精确值最接近的整数。如果 `x / y` 恰好位于两个连续整数之间，则将最接近的 *偶数* 用作 `n` 。 余数 `r = remainder(x, y)` 因此总是满足 `abs(r) <= 0.5 * abs(y)`。

​	特殊情况遵循IEEE 754：特别是 `remainder(x, math.inf)` 对于任何有限 *x* 都是 *x* ，而 `remainder(x, 0)` 和 `remainder(math.inf, x)` 引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError) 适用于任何非NaN的 *x* 。如果余数运算的结果为零，则该零将具有与 *x* 相同的符号。

​	在使用IEEE 754二进制浮点的平台上，此操作的结果始终可以完全表示：不会引入舍入错误。

*Added in version 3.7.*

## math.**sumprod**(*p*, *q*)

​	两个可迭代对象 *p* 和 *q* 中的值的乘积的总计值。

​	如果输入值的长度不相等则会引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

​	大致相当于：

```
sum(itertools.starmap(operator.mul, zip(p, q, strict=True)))
```

​	对于浮点数或混合整数/浮点数的输入，中间的乘积和总计值将使用扩展精度来计算。

*Added in version 3.12.*

## math.**trunc**(*x*)

​	返回去除小数部分的 *x* ，只留下整数部分。 这样就可以四舍五入到0了： `trunc()` 对于正的 *x* 相当于 [`floor()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.floor) ，对于负的 *x* 相当于 [`ceil()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.ceil) 。如果 *x* 不是浮点数，委托给 [`x.__trunc__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__trunc__) ，它应该返回一个 [`Integral`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Integral) 值。

## math.**ulp**(*x*)

​	返回浮点数 *x* 的最小有效比特位的值:

- 如果 *x* 是 NaN (非数字)，则返回 *x*。
- 如果 *x* 为负数，则返回 `ulp(-x)`。
- 如果 *x* 为正数，则返回 *x*。
- 如果 *x* 等于零，则返回 *去正规化的* 可表示最小正浮点数 (小于 *正规化的* 最小正浮点数 [`sys.float_info.min`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.float_info))。
- 如果 *x* 等于可表示最大正浮点数，则返回 *x* 的最低有效比特位的值，使得小于 *x* 的第一个浮点数为 `x - ulp(x)`。
- 在其他情况下 (*x* 是一个有限的正数)，则返回 *x* 的最低有效比特位的值，使得大于 *x* 的第一个浮点数为 `x + ulp(x)`。

​	ULP 即 "Unit in the Last Place" 的缩写。

​	另请参阅 [`math.nextafter()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.nextafter) 和 [`sys.float_info.epsilon`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.float_info)。

*Added in version 3.9.*

​	注意 [`frexp()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.frexp) 和 [`modf()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.modf) 具有与它们的C等价函数不同的调用/返回模式：它们采用单个参数并返回一对值，而不是通过 '输出形参' 返回它们的第二个返回参数（Python中没有这样的东西）。

​	对于 [`ceil()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.ceil) ， [`floor()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.floor) 和 [`modf()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.modf) 函数，请注意 *所有* 足够大的浮点数都是精确整数。Python浮点数通常不超过53位的精度（与平台C double类型相同），在这种情况下，任何浮点 *x* 与 `abs(x) >= 2**52` 必然没有小数位。

## 幂函数与对数函数

## math.**cbrt**(*x*)

​	返回 *x* 的立方根。

*Added in version 3.11.*

## math.**exp**(*x*)

​	返回 *e* 的 *x* 次幂，其中 *e* = 2.718281... 是自然对数的基数。这通常比 `math.e ** x` 或 `pow(math.e, x)` 更精确。

## math.**exp2**(*x*)

​	返回 *2* 的 *x* 次幂。

*Added in version 3.11.*

## math.**expm1**(*x*)

​	返回 *e* 的 *x*，减去 1。 这里 *e* 是以自然对数作为基数。 对于小浮点数 *x*，在 `exp(x) - 1` 中的减法运算可能导致 [明显的精度损失](https://en.wikipedia.org/wiki/Loss_of_significance); [`expm1()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.expm1) 函数提供了一种以完整精度计算此数量的办法:



```
>>> from math import exp, expm1
>>> exp(1e-5) - 1  # gives result accurate to 11 places
1.0000050000069649e-05
>>> expm1(1e-5)    # result accurate to full precision
1.0000050000166668e-05
```

*Added in version 3.2.*

## math.**log**(*x*[, *base*])

​	使用一个参数，返回 *x* 的自然对数（底为 *e* ）。

​	使用两个参数，返回给定的 *base* 的对数 *x* ，计算为 `log(x)/log(base)` 。

## math.**log1p**(*x*)

​	返回 *1+x* 的自然对数（以 *e* 为底）。 以对于接近零的 *x* 精确的方式计算结果。

## math.**log2**(*x*)

​	返回 *x* 以2为底的对数。这通常比 `log(x, 2)` 更准确。

*Added in version 3.3.*

​	参见

 

[`int.bit_length()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#int.bit_length) 返回表示二进制整数所需的位数，不包括符号和前导零。

## math.**log10**(*x*)

​	返回 *x* 底为10的对数。这通常比 `log(x, 10)` 更准确。

## math.**pow**(*x*, *y*)

​	Return *x* raised to the power *y*. Exceptional cases follow the IEEE 754 standard as far as possible. In particular, `pow(1.0, x)` and `pow(x, 0.0)` always return `1.0`, even when *x* is a zero or a NaN. If both *x* and *y* are finite, *x* is negative, and *y* is not an integer then `pow(x, y)` is undefined, and raises [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError).

​	与内置的 `**` 运算符不同， [`math.pow()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.pow) 将其参数转换为 [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float) 类型。使用 `**` 或内置的 [`pow()`](https://docs.python.org/zh-cn/3.13/library/functions.html#pow) 函数来计算精确的整数幂。

*在 3.11 版本发生变更:* 特殊情况 `pow(0.0, -inf)` 和 `pow(-0.0, -inf)` 已改为返回 `inf` 而不是引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)，以便同 IEEE 754 保持一致。

## math.**sqrt**(*x*)

​	返回 *x* 的平方根。

## 三角函数

## math.**acos**(*x*)

​	返回以弧度为单位的 *x* 的反余弦值。 结果范围在 `0` 到 `pi` 之间。

## math.**asin**(*x*)

​	返回以弧度为单位的 *x* 的反正弦值。 结果范围在 `-pi/2` 到 `pi/2` 之间。

## math.**atan**(*x*)

​	返回以弧度为单位的 *x* 的反正切值。 结果范围在 `-pi/2` 到 `pi/2` 之间。.

## math.**atan2**(*y*, *x*)

​	以弧度为单位返回 `atan(y / x)` 。结果是在 `-pi` 和 `pi` 之间。从原点到点 `(x, y)` 的平面矢量使该角度与正X轴成正比。 [`atan2()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.atan2) 的点的两个输入的符号都是已知的，因此它可以计算角度的正确象限。 例如， `atan(1)` 和 `atan2(1, 1)` 都是 `pi/4` ，但 `atan2(-1, -1)` 是 `-3*pi/4` 。

## math.**cos**(*x*)

​	返回 *x* 弧度的余弦值。

## math.**dist**(*p*, *q*)

​	返回 *p* 与 *q* 两点之间的欧几里得距离，以一个坐标序列（或可迭代对象）的形式给出。 两个点必须具有相同的维度。

​	大致相当于：

```
sqrt(sum((px - qx) ** 2.0 for px, qx in zip(p, q)))
```

*Added in version 3.8.*

## math.**hypot**(**coordinates*)

​	返回欧几里得范数，`sqrt(sum(x**2 for x in coordinates))`。 这是从原点到坐标给定点的向量长度。

​	对于一个二维点 `(x, y)`，这等价于使用毕达哥拉斯定义 `sqrt(x*x + y*y)` 计算一个直角三角形的斜边。

*在 3.8 版本发生变更:* 添加了对 n 维点的支持。 之前的版本只支持二维点。

*在 3.10 版本发生变更:* 改进了算法的精确性，使得最大误差在 1 ulp (最后一位的单位数值) 以下。 更为常见的情况是，结果几乎总是能正确地舍入到 1/2 ulp 范围之内。

## math.**sin**(*x*)

​	返回 *x* 弧度的正弦值。

## math.**tan**(*x*)

​	返回 *x* 弧度的正切值。

## 角度转换

## math.**degrees**(*x*)

​	将角度 *x* 从弧度转换为度数。

## math.**radians**(*x*)

​	将角度 *x* 从度数转换为弧度。

## 双曲函数

[双曲函数](https://en.wikipedia.org/wiki/Hyperbolic_functions) 是基于双曲线而非圆来对三解函数进行的模拟。

## math.**acosh**(*x*)

​	返回 *x* 的反双曲余弦值。

## math.**asinh**(*x*)

​	返回 *x* 的反双曲正弦值。

## math.**atanh**(*x*)

​	返回 *x* 的反双曲正切值。

## math.**cosh**(*x*)

​	返回 *x* 的双曲余弦值。

## math.**sinh**(*x*)

​	返回 *x* 的双曲正弦值。

## math.**tanh**(*x*)

​	返回 *x* 的双曲正切值。

## 特殊函数

## math.**erf**(*x*)

​	返回 *x* 处的 [误差函数](https://en.wikipedia.org/wiki/Error_function) 。

​	可以使用 [`erf()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.erf) 函数来计算传统的统计函数如 [累积标准正态分布](https://en.wikipedia.org/wiki/Cumulative_distribution_function):

```
def phi(x):
    '标准正态分布的累积分布函数'
    return (1.0 + erf(x / sqrt(2.0))) / 2.0
```

*Added in version 3.2.*

## math.**erfc**(*x*)

​	返回 *x* 处的互补误差函数。 [互补错误函数](https://en.wikipedia.org/wiki/Error_function) 定义为 `1.0 - erf(x)`。 它用于 *x* 的大值，从其中减去一个会导致 [有效位数损失](https://en.wikipedia.org/wiki/Loss_of_significance)。

*Added in version 3.2.*

## math.**gamma**(*x*)

​	返回 *x* 处的 [伽马函数](https://en.wikipedia.org/wiki/Gamma_function) 值。

*Added in version 3.2.*

## math.**lgamma**(*x*)

​	返回Gamma函数在 *x* 绝对值的自然对数。

*Added in version 3.2.*

## 常量

## math.**pi**

​	数学常数 *π* = 3.141592...，精确到可用精度。

## math.**e**

​	数学常数 *e* = 2.718281...，精确到可用精度。

## math.**tau**

​	数学常数 *τ* = 6.283185...，精确到可用精度。Tau 是一个圆周常数，等于 2*π*，圆的周长与半径之比。更多关于 Tau 的信息可参考 Vi Hart 的视频 [Pi is (still) Wrong](https://www.youtube.com/watch?v=jG7vhMMXagQ)。吃两倍多的派来庆祝 [Tau 日](https://tauday.com/) 吧！

*Added in version 3.6.*

## math.**inf**

​	浮点正无穷大。 （对于负无穷大，使用 `-math.inf` 。）相当于 `float('inf')` 的输出。

*Added in version 3.5.*

## math.**nan**

​	一个浮点数值 "Not a Number" (NaN)。 相当于 `float('nan')` 的输出。 根据 [IEEE-754 标准](https://en.wikipedia.org/wiki/IEEE_754) 要求，`math.nan` 和 `float('nan')` 不会被视为等于任何其他数值，包括其本身。 要检查一个数字是否为 NaN，请使用 [`isnan()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.isnan) 函数来测试 NaN 而不能使用 `is` 或 `==`。 例如:



```
>>> import math
>>> math.nan == math.nan
False
>>> float('nan') == float('nan')
False
>>> math.isnan(math.nan)
True
>>> math.isnan(float('nan'))
True
```

*Added in version 3.5.*

*在 3.11 版本发生变更:* 该常量现在总是可用。

**CPython 实现细节：** [`math`](https://docs.python.org/zh-cn/3.13/library/math.html#module-math) 模块主要包含围绕平台C数学库函数的简单包装器。特殊情况下的行为在适当情况下遵循C99标准的附录F。当前的实现将引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError) 用于无效操作，如 `sqrt(-1.0)` 或 `log(0.0)` （其中C99附件F建议发出无效操作信号或被零除）， 和 [`OverflowError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OverflowError) 用于溢出的结果（例如， `exp(1000.0)` ）。除非一个或多个输入参数是NaN，否则不会从上述任何函数返回NaN；在这种情况下，大多数函数将返回一个NaN，但是（再次遵循C99附件F）这个规则有一些例外，例如 `pow(float('nan'), 0.0)` 或 `hypot(float('nan'), float('inf'))` 。

​	请注意，Python不会将显式NaN与静默NaN区分开来，并且显式NaN的行为仍未明确。典型的行为是将所有NaN视为静默的。

​	参见

## [`cmath`](https://docs.python.org/zh-cn/3.13/library/cmath.html#module-cmath) 模块

​	这里很多函数的复数版本。
