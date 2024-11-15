+++
title = "numbers --- 数字抽象基类"
date = 2024-11-15T11:42:26+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/numbers.html](https://docs.python.org/zh-cn/3.13/library/numbers.html)
>
> 收录该文档的时间：`2024-11-15T11:42:26+08:00`

# `numbers` --- 数字抽象基类

**源代码：** [Lib/numbers.py](https://github.com/python/cpython/tree/3.13/Lib/numbers.py)

------

`numbers` 模块 ([**PEP 3141**](https://peps.python.org/pep-3141/)) 定义了数字 [抽象基类](https://docs.python.org/zh-cn/3.13/glossary.html#term-abstract-base-class) 的层级结构，其中逐级定义了更多的操作。 此模块中定义的类型都不可被实例化。

## *class* numbers.**Number**

​	数字的层次结构的基础。 如果你只想确认参数 *x* 是不是数字而不关心其类型，则使用 `isinstance(x, Number)`。

## 数字的层次

## *class* numbers.**Complex**

​	这个类型的子类描述了复数并包括了适用于内置 [`complex`](https://docs.python.org/zh-cn/3.13/library/functions.html#complex) 类型的操作。 这些操作有: 转换为 [`complex`](https://docs.python.org/zh-cn/3.13/library/functions.html#complex) 和 [`bool`](https://docs.python.org/zh-cn/3.13/library/functions.html#bool), [`real`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Complex.real), [`imag`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Complex.imag), `+`, `-`, `*`, `/`, `**`, [`abs()`](https://docs.python.org/zh-cn/3.13/library/functions.html#abs), [`conjugate()`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Complex.conjugate), `==` 以及 `!=`。 除 `-` 和 `!=` 之外所有操作都是抽象的。

## **real**

​	抽象的。得到该数字的实数部分。

## **imag**

​	抽象的。得到该数字的虚数部分。

## *abstractmethod* **conjugate**()

​	抽象的。返回共轭复数。例如 `(1+3j).conjugate() == (1-3j)`。

## *class* numbers.**Real**

​	相对于 [`Complex`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Complex)，`Real` 加入了只适用于实数的操作。

​	简单的说，它们是：转化至 [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float)，[`math.trunc()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.trunc)、 [`round()`](https://docs.python.org/zh-cn/3.13/library/functions.html#round)、 [`math.floor()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.floor)、 [`math.ceil()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.ceil)、 [`divmod()`](https://docs.python.org/zh-cn/3.13/library/functions.html#divmod)、 `//`、 `%`、 `<`、 `<=`、 `>`、 和 `>=`。

​	实数同样默认支持 [`complex()`](https://docs.python.org/zh-cn/3.13/library/functions.html#complex)、 [`real`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Complex.real)、 [`imag`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Complex.imag) 和 [`conjugate()`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Complex.conjugate)。

## *class* numbers.**Rational**

​	子类型 [`Real`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Real) 并加入 [`numerator`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Rational.numerator) 和 [`denominator`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Rational.denominator) 两种特征属性。 它还为 [`float()`](https://docs.python.org/zh-cn/3.13/library/functions.html#float) 提供了默认值。

[`numerator`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Rational.numerator) 和 [`denominator`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Rational.denominator) 值应为 [`Integral`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Integral) 的实例并且应当是具有 [`denominator`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Rational.denominator) 正值的最低项。

## **numerator**

​	抽象的。

## **denominator**

​	抽象的。

## *class* numbers.**Integral**

​	子类型 [`Rational`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Rational) 还增加了到 [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int) 的转换操作。 为 [`float()`](https://docs.python.org/zh-cn/3.13/library/functions.html#float), [`numerator`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Rational.numerator) 和 [`denominator`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Rational.denominator) 提供了默认支持。 为 [`pow()`](https://docs.python.org/zh-cn/3.13/library/functions.html#pow) 方法增加了求余和按位字符串运算的抽象方法: `<<`, `>>`, `&`, `^`, `|`, `~`。

## 有关类型实现的注释

​	实现方应当注意让相等的数值保证相等并使它们哈希运算的结果值相同。 当相互比较的实数属于两个不同的扩展模块时可能会有微妙的差异。 举例来说，[`fractions.Fraction`](https://docs.python.org/zh-cn/3.13/library/fractions.html#fractions.Fraction) 是这样实现 [`hash()`](https://docs.python.org/zh-cn/3.13/library/functions.html#hash) 的:

```
def __hash__(self):
    if self.denominator == 1:
        # 正确获取整数。
        return hash(self.numerator)
    # 高开销的检查，但肯定正确。
    if self == float(self):
        return hash(float(self))
    else:
        # 使用元组的哈希值来避免简单分数的
        # 高碰撞率。
        return hash((self.numerator, self.denominator))
```

### 加入更多数字的ABC

​	当然，这里有更多支持数字的ABC，如果不加入这些，就将缺少层次感。你可以用如下方法在 [`Complex`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Complex) 和 [`Real`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Real) 中加入 `MyFoo`:

```
class MyFoo(Complex): ...
MyFoo.register(Real)
```



### 实现算术运算

​	我们想要实现算术运算，因此混合模式运算要么调用一个开发者知道两个参数类型的实现，要么将两个参数转换为最接近的内置类型再执行运算。 对于 [`Integral`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Integral) 的子类，这意味着 [`__add__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__add__) 和 [`__radd__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__radd__) 应当被定义为:

```
class MyIntegral(Integral):

    def __add__(self, other):
        if isinstance(other, MyIntegral):
            return do_my_adding_stuff(self, other)
        elif isinstance(other, OtherTypeIKnowAbout):
            return do_my_other_adding_stuff(self, other)
        else:
            return NotImplemented

    def __radd__(self, other):
        if isinstance(other, MyIntegral):
            return do_my_adding_stuff(other, self)
        elif isinstance(other, OtherTypeIKnowAbout):
            return do_my_other_adding_stuff(other, self)
        elif isinstance(other, Integral):
            return int(other) + int(self)
        elif isinstance(other, Real):
            return float(other) + float(self)
        elif isinstance(other, Complex):
            return complex(other) + complex(self)
        else:
            return NotImplemented
```

[`Complex`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Complex) 有 5 种不同的混合类型的操作。 我将上面提到的所有代码作为“模板”称作 `MyIntegral` 和 `OtherTypeIKnowAbout`。 `a` 是 [`Complex`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Complex) 的子类型 `A` 的实例 (`a : A <: Complex`)，同时 `b : B <: Complex`。 我将要计算 `a + b`:

1. 如果 `A` 定义了接受 `b` 的 [`__add__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__add__)，一切都没有问题。
2. 如果 `A` 回退至基础代码，它将返回一个来自 [`__add__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__add__) 的值，我们就没有机会为 `B` 定义更加智能的 [`__radd__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__radd__)，因此基础代码应当从 `__add__()` 返回 [`NotImplemented`](https://docs.python.org/zh-cn/3.13/library/constants.html#NotImplemented)。 （或者 `A` 可能完全不实现 `__add__()`。）
3. 那么 `B` 的 [`__radd__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__radd__) 将有机会发挥作用。 如果它接受 `a`，一切都没有问题。
4. 如果没有成功回退到模板，就没有更多的方法可以去尝试，因此这里将使用默认的实现。
5. 如果 `B <: A` ， Python 在 `A.__add__` 之前尝试 `B.__radd__` 。 这是可行的，是通过对 `A` 的认识实现的，因此这可以在交给 [`Complex`](https://docs.python.org/zh-cn/3.13/library/numbers.html#numbers.Complex) 处理之前处理这些实例。

​	如果 `A <: Complex` 和 `B <: Real` 没有共享任何其他信息，那么内置 [`complex`](https://docs.python.org/zh-cn/3.13/library/functions.html#complex) 的共享操作就是最适当的，两个 [`__radd__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__radd__) 都将应用该操作，因此 `a+b == b+a`。

​	由于对任何一直类型的大部分操作是十分相似的，可以定义一个帮助函数，即一个生成后续或相反的实例的生成器。例如，使用 [`fractions.Fraction`](https://docs.python.org/zh-cn/3.13/library/fractions.html#fractions.Fraction) 如下：

```
def _operator_fallbacks(monomorphic_operator, fallback_operator):
    def forward(a, b):
        if isinstance(b, (int, Fraction)):
            return monomorphic_operator(a, b)
        elif isinstance(b, float):
            return fallback_operator(float(a), b)
        elif isinstance(b, complex):
            return fallback_operator(complex(a), b)
        else:
            return NotImplemented
    forward.__name__ = '__' + fallback_operator.__name__ + '__'
    forward.__doc__ = monomorphic_operator.__doc__

    def reverse(b, a):
        if isinstance(a, Rational):
            # Includes ints.
            return monomorphic_operator(a, b)
        elif isinstance(a, Real):
            return fallback_operator(float(a), float(b))
        elif isinstance(a, Complex):
            return fallback_operator(complex(a), complex(b))
        else:
            return NotImplemented
    reverse.__name__ = '__r' + fallback_operator.__name__ + '__'
    reverse.__doc__ = monomorphic_operator.__doc__

    return forward, reverse

def _add(a, b):
    """a + b"""
    return Fraction(a.numerator * b.denominator +
                    b.numerator * a.denominator,
                    a.denominator * b.denominator)

__add__, __radd__ = _operator_fallbacks(_add, operator.add)

# ...
```
