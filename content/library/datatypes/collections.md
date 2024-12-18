+++
title = "collections.abc --- 容器的抽象基类"
date = 2024-11-15T11:18:41+08:00
weight = 40
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/collections.abc.html](https://docs.python.org/zh-cn/3.13/library/collections.abc.html)
>
> 收录该文档的时间：`2024-11-15T11:18:41+08:00`

# `collections` --- 容器数据类型

**源代码：** [Lib/collections/__init__.py](https://github.com/python/cpython/tree/3.13/Lib/collections/__init__.py)

------

​	这个模块实现了一些专门化的容器，提供了对 Python 的通用内建容器 [`dict`]({{< ref "/library/stdtypes#dict" >}})、[`list`]({{< ref "/library/stdtypes#list" >}})、[`set`]({{< ref "/library/stdtypes#set" >}}) 和 [`tuple`]({{< ref "/library/stdtypes#tuple" >}}) 的补充。

| [`namedtuple()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.namedtuple) | 一个工厂函数，用来创建元组的子类，子类的字段是有名称的。     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [`deque`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.deque) | 类似列表的容器，但 append 和 pop 在其两端的速度都很快。      |
| [`ChainMap`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.ChainMap) | 类似字典的类，用于创建包含多个映射的单个视图。               |
| [`Counter`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.Counter) | 用于计数 [hashable]({{< ref "/glossary/idx#term-hashable" >}}) 对象的字典子类 |
| [`OrderedDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.OrderedDict) | 字典的子类，能记住条目被添加进去的顺序。                     |
| [`defaultdict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.defaultdict) | 字典的子类，通过调用用户指定的工厂函数，为键提供默认值。     |
| [`UserDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.UserDict) | 封装了字典对象，简化了字典子类化                             |
| [`UserList`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.UserList) | 封装了列表对象，简化了列表子类化                             |
| [`UserString`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.UserString) | 封装了字符串对象，简化了字符串子类化                         |

## [`ChainMap`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.ChainMap) 对象

> Added in version 3.3.
>

[`ChainMap`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.ChainMap) 类将多个映射迅速地链到一起，这样它们就可以作为一个单元处理。这通常比创建一个新字典再重复地使用 [`update()`]({{< ref "/library/stdtypes#dict.update" >}}) 要快得多。

​	这个类可以用于模拟嵌套作用域，并且对模版化有用。

## *class* collections.**ChainMap**(**maps*)

​	一个 [`ChainMap`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.ChainMap) 将多个字典或者其他映射组合在一起，创建一个单独的可更新的视图。 如果没有指定任何 *maps*，一个空字典会被作为 *maps*。这样，每个新链至少包含一个映射。

​	底层映射被存储在一个列表中。这个列表是公开的，可以通过 *maps* 属性存取和更新。没有其他的状态。

​	搜索查询底层映射，直到一个键被找到。不同的是，写，更新和删除只操作第一个映射。

​	一个 [`ChainMap`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.ChainMap) 通过引用合并底层映射。 所以，如果一个底层映射更新了，这些更改会反映到 [`ChainMap`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.ChainMap) 。

​	支持所有常用字典方法。另外还有一个 *maps* 属性(attribute)，一个创建子上下文的方法(method)， 一个存取它们首个映射的属性(property):

## **maps**

​	一个可以更新的映射列表。这个列表是按照第一次搜索到最后一次搜索的顺序组织的。它是仅有的存储状态，可以被修改。列表最少包含一个映射。

## **new_child**(*m=None*, ***kwargs*)

​	返回一个新的 [`ChainMap`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.ChainMap)，其中包含一个新的映射，后面跟随当前实例中的所有映射。 如果指定了 `m`，它会成为新的映射加在映射列表的前面；如果未指定，则会使用一个空字典，因此调用 `d.new_child()` 就等价于 `ChainMap({}, *d.maps)`。 如果指定了任何关键字参数，它们会更新所传入的映射或新的空字典。 此方法被用于创建子上下文，它可在不改变任何上级映射的情况下被更新。

> 在 3.4 版本发生变更: 添加了可选的 `m` 形参。

> 在 3.10 版本发生变更: 增加了对关键字参数的支持。

## **parents**

​	属性返回一个新的 [`ChainMap`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.ChainMap) 包含所有的当前实例的映射，除了第一个。这样可以在搜索的时候跳过第一个映射。 使用的场景类似在 [nested scopes]({{< ref "/glossary/idx#term-nested-scope" >}}) 嵌套作用域中使用 [`nonlocal`]({{< ref "/reference/simple_stmts#nonlocal" >}}) 关键词。用例也可以类比内建函数 [`super()`]({{< ref "/library/functions#super" >}}) 。一个 `d.parents` 的引用等价于 `ChainMap(*d.maps[1:])` 。

​	注意，[`ChainMap`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.ChainMap) 的迭代顺序是通过从后往前扫描所有映射来确定的:



``` python
>>> baseline = {'music': 'bach', 'art': 'rembrandt'}
>>> adjustments = {'art': 'van gogh', 'opera': 'carmen'}
>>> list(ChainMap(adjustments, baseline))
['music', 'art', 'opera']
```

​	使得顺序与从最后一个映射开始调用一系列 [`dict.update()`]({{< ref "/library/stdtypes#dict.update" >}}) 得到的字典的迭代顺序相同：



``` python
>>> combined = baseline.copy()
>>> combined.update(adjustments)
>>> list(combined)
['music', 'art', 'opera']
```

> 在 3.9 版本发生变更: 增加了对 `|` 和 `|=` 运算符的支持，相关说明见 [**PEP 584**](https://peps.python.org/pep-0584/)。

​参见
- [MultiContext class](https://github.com/enthought/codetools/blob/4.0.0/codetools/contexts/multi_context.py) 在 Enthought [CodeTools package](https://github.com/enthought/codetools) 有支持写映射的选项。
- Django 中用于模板的 [Context class](https://github.com/django/django/blob/main/django/template/context.py) 是只读的映射链。 它还具有上下文推送和弹出特性，类似于 [`new_child()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.ChainMap.new_child) 方法和 [`parents`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.ChainMap.parents) 特征属性。
- [Nested Contexts recipe](https://code.activestate.com/recipes/577434-nested-contexts-a-chain-of-mapping-objects/) 提供了对于写入和其他修改是只应用于链路中第一个映射还是所有映射的选项。
- 一个 [极简的只读版 Chainmap](https://code.activestate.com/recipes/305268/).

### [`ChainMap`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.ChainMap) 例子和方法

​	这一节提供了多个使用链映射的案例。

​	模拟Python内部lookup链的例子

```
import builtins
pylookup = ChainMap(locals(), globals(), vars(builtins))
```

​	让用户指定的命令行参数优先于环境变量，优先于默认值的例子

```
import os, argparse

defaults = {'color': 'red', 'user': 'guest'}

parser = argparse.ArgumentParser()
parser.add_argument('-u', '--user')
parser.add_argument('-c', '--color')
namespace = parser.parse_args()
command_line_args = {k: v for k, v in vars(namespace).items() if v is not None}

combined = ChainMap(command_line_args, os.environ, defaults)
print(combined['color'])
print(combined['user'])
```

​	用 [`ChainMap`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.ChainMap) 类模拟嵌套上下文的例子

```
c = ChainMap()        # Create root context
d = c.new_child()     # Create nested child context
e = c.new_child()     # Child of c, independent from d
e.maps[0]             # Current context dictionary -- like Python's locals()
e.maps[-1]            # Root context -- like Python's globals()
e.parents             # Enclosing context chain -- like Python's nonlocals

d['x'] = 1            # Set value in current context
d['x']                # Get first key in the chain of contexts
del d['x']            # Delete from current context
list(d)               # All nested values
k in d                # Check all nested values
len(d)                # Number of nested values
d.items()             # All nested items
dict(d)               # Flatten into a regular dictionary
```

[`ChainMap`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.ChainMap) 类只更新链中的第一个映射，但lookup会搜索整个链。 然而，如果需要深度写和删除，也可以很容易的通过定义一个子类来实现它

```
class DeepChainMap(ChainMap):
    'Variant of ChainMap that allows direct updates to inner scopes'

    def __setitem__(self, key, value):
        for mapping in self.maps:
            if key in mapping:
                mapping[key] = value
                return
        self.maps[0][key] = value

    def __delitem__(self, key):
        for mapping in self.maps:
            if key in mapping:
                del mapping[key]
                return
        raise KeyError(key)

>>> d = DeepChainMap({'zebra': 'black'}, {'elephant': 'blue'}, {'lion': 'yellow'})
>>> d['lion'] = 'orange'         # update an existing key two levels down
>>> d['snake'] = 'red'           # new keys get added to the topmost dict
>>> del d['elephant']            # remove an existing key one level down
>>> d                            # display result
DeepChainMap({'zebra': 'black', 'snake': 'red'}, {}, {'lion': 'orange'})
```

## [`Counter`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.Counter) 对象

​	一个计数器工具，为的是可以方便快速地计账。例如：



``` python
>>> # Tally occurrences of words in a list
>>> cnt = Counter()
>>> for word in ['red', 'blue', 'red', 'green', 'blue', 'blue']:
...     cnt[word] += 1
...
>>> cnt
Counter({'blue': 3, 'red': 2, 'green': 1})

>>> # Find the ten most common words in Hamlet
>>> import re
>>> words = re.findall(r'\w+', open('hamlet.txt').read().lower())
>>> Counter(words).most_common(10)
[('the', 1143), ('and', 966), ('to', 762), ('of', 669), ('i', 631),
 ('you', 554),  ('a', 546), ('my', 514), ('hamlet', 471), ('in', 451)]
```

## *class* collections.**Counter**([*iterable-or-mapping*])

[`Counter`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.Counter) 是 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 的子类，用于计数 [hashable]({{< ref "/glossary/idx#term-hashable" >}}) 对象。它是一个多项集，元素存储为字典的键而它们的计数存储为字典的值。计数可以是任何整数，包括零或负的计数值。[`Counter`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.Counter) 类与其他语言中的 bag 或 multiset 很相似。

​	它可以通过计数一个 *iterable* 中的元素来初始化，或用其它 *mapping* (包括 counter) 初始化：



``` python
>>> c = Counter()                           # a new, empty counter
>>> c = Counter('gallahad')                 # a new counter from an iterable
>>> c = Counter({'red': 4, 'blue': 2})      # a new counter from a mapping
>>> c = Counter(cats=4, dogs=8)             # a new counter from keyword args
```

​	Counter 对象的接口类似于字典，不同的是，如果查询的键不在 Counter 中，它会返回一个 0 而不是引发一个 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}})：



``` python
>>> c = Counter(['eggs', 'ham'])
>>> c['bacon']                              # count of a missing element is zero
0
```

​	设置一个计数为0不会从计数器中移去一个元素。使用 `del` 来删除它:



``` python
>>> c['sausage'] = 0                        # counter entry with a zero count
>>> del c['sausage']                        # del actually removes the entry
```

> Added in version 3.1.
>

> 在 3.7 版本发生变更: 作为 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 的子类，[`Counter`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.Counter) 继承了记住插入顺序的功能。*Counter* 对象间的数学运算也是保序的。结果首先把左操作数中存在的元素按照它们在左操作数中的顺序排序，后面跟着其它元素，按它们在右操作数中的顺序排序。

​	Counter 对象在对所有字典可用的方法以外还支持一些附加方法:

## **elements**()

​	返回一个迭代器，其中每个元素将重复出现计数值所指定次。 元素会按首次出现的顺序返回。 如果一个元素的计数值小于一，[`elements()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.Counter.elements) 将会忽略它。



``` python
>>> c = Counter(a=4, b=2, c=0, d=-2)
>>> sorted(c.elements())
['a', 'a', 'a', 'a', 'b', 'b']
```

## **most_common**([*n*])

​	返回一个列表，其中包含 *n* 个最常见的元素及出现次数，按常见程度由高到低排序。 如果 *n* 被省略或为 `None`，[`most_common()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.Counter.most_common) 将返回计数器中的 *所有* 元素。 计数值相等的元素按首次出现的顺序排序：



``` python
>>> Counter('abracadabra').most_common(3)
[('a', 5), ('b', 2), ('r', 2)]
```

## **subtract**([*iterable-or-mapping*])

​	减去一个 *可迭代对象* 或 *映射对象* (或 counter) 中的元素。类似于 [`dict.update()`]({{< ref "/library/stdtypes#dict.update" >}}) 但是是减去而非替换。输入和输出都可以是 0 或负数。



``` python
>>> c = Counter(a=4, b=2, c=0, d=-2)
>>> d = Counter(a=1, b=2, c=3, d=4)
>>> c.subtract(d)
>>> c
Counter({'a': 3, 'b': 0, 'c': -3, 'd': -6})
```

> Added in version 3.2.
>

## **total**()

​	计算总计数值。



``` python
>>> c = Counter(a=10, b=5, c=0)
>>> c.total()
15
```

> Added in version 3.10.
>

​	通常字典方法都可用于 [`Counter`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.Counter) 对象，除了有两个方法工作方式与字典并不相同。

## **fromkeys**(*iterable*)

​	这个类方法没有在 [`Counter`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.Counter) 中实现。

## **update**([*iterable-or-mapping*])

​	加上一个 *可迭代对象* 或 *映射对象* (或 counter) 中的元素。类似于 [`dict.update()`]({{< ref "/library/stdtypes#dict.update" >}}) 但是是加上而非替换。另外，*可迭代对象* 应当是一个元素序列，而不是一个 `(key, value)` 对的序列。

​	计数对象支持相等性、子集和超集关系等富比较运算符: `==`, `!=`, `<`, `<=`, `>`, `>=`。 所有这些检测会将不存在的元素当作计数值为零，因此 `Counter(a=1) == Counter(a=1, b=0)` 将返回真值。

> 在 3.10 版本发生变更: 增加了富比较运算。

> 在 3.10 版本发生变更: 在相等性检测中，不存在的元素会被当作计数值为零。 在此之前，`Counter(a=3)` 和 `Counter(a=3, b=0)` 会被视为不同。

[`Counter`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.Counter) 对象的常用案例

```
c.total()                       # total of all counts
c.clear()                       # reset all counts
list(c)                         # list unique elements
set(c)                          # convert to a set
dict(c)                         # convert to a regular dictionary
c.items()                       # access the (elem, cnt) pairs
Counter(dict(list_of_pairs))    # convert from a list of (elem, cnt) pairs
c.most_common()[:-n-1:-1]       # n least common elements
+c                              # remove zero and negative counts
```

​	提供了几种数学运算用来合并 [`Counter`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.Counter) 对象，产生多集（所有计数值均大于零的 counter）。加减运算通过增加或减少两者间对应元素的计数来合并 counter。交并运算返回对应计数的最小值和最大值。相等和包含运算比较对应的计数。每个运算的参数都可以含有有符号的计数，但输出将排除计数小于等于零的元素。



``` python
>>> c = Counter(a=3, b=1)
>>> d = Counter(a=1, b=2)
>>> c + d                       # add two counters together:  c[x] + d[x]
Counter({'a': 4, 'b': 3})
>>> c - d                       # subtract (keeping only positive counts)
Counter({'a': 2})
>>> c & d                       # intersection:  min(c[x], d[x])
Counter({'a': 1, 'b': 1})
>>> c | d                       # union:  max(c[x], d[x])
Counter({'a': 3, 'b': 2})
>>> c == d                      # equality:  c[x] == d[x]
False
>>> c <= d                      # inclusion:  c[x] <= d[x]
False
```

​	单目加和减（一元操作符）意思是从空计数器加或者减去。



``` python
>>> c = Counter(a=2, b=-4)
>>> +c
Counter({'a': 2})
>>> -c
Counter({'b': 4})
```

> Added in version 3.3:
> 添加了对一元加，一元减和位置集合操作的支持。

​备注
 

​	计数器主要是为了表达运行的正的计数而设计；但是，小心不要预先排除负数或者其他类型。为了帮助这些用例，这一节记录了最小范围和类型限制。

- [`Counter`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.Counter) 类是一个字典的子类，不限制键和值。值用于表示计数，但你实际上 *可以* 存储任何其他值。
- [`most_common()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.Counter.most_common) 方法在值需要排序的时候用。
- 参与原地操作如 `c[key] += 1` 的值的类型只需要支持加和减，所以分数、小数和 decimals 都可以用，也支持负数。[`update()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.Counter.update) 和 [`subtract()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.Counter.subtract) 当然也一样，输入和输出都支持 0 和 负数。
- 多集方法是专为只会遇到正值的使用情况设计的。输入可以是 0 或负数，但只输出计数为正的值。没有类型限制，但值的类型需支持加、减和比较操作。
- [`elements()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.Counter.elements) 方法要求正整数计数。忽略0和负数计数。

​参见
- [Bag class](https://www.gnu.org/software/smalltalk/manual-base/html_node/Bag.html) 在 Smalltalk。

- Wikipedia 链接 [Multisets](https://en.wikipedia.org/wiki/Multiset).

- [C++ multisets](http://www.java2s.com/Tutorial/Cpp/0380__set-multiset/Catalog0380__set-multiset.htm) 教程和例子。

- 数学操作和多集合用例，参考 *Knuth, Donald. The Art of Computer Programming Volume II, Section 4.6.3, Exercise 19* 。

- 在给定数量和集合元素枚举所有不同的多集合，参考 [`itertools.combinations_with_replacement()`]({{< ref "/library/functional/itertools#itertools.combinations_with_replacement" >}})

  ```
  map(Counter, combinations_with_replacement('ABC', 2)) # --> AA AB AC BB BC CC
  ```

## [`deque`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.deque) 对象

## *class* collections.**deque**([*iterable*[, *maxlen*]])

​	返回一个新的双向队列对象，从左到右初始化(用方法 [`append()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.deque.append)) ，从 *iterable* （迭代对象) 数据创建。如果 *iterable* 没有指定，新队列为空。

​	Deque 队列是对栈或 queue 队列的泛化（该名称的发音为 "deck"，是 "double-ended queue" 的简写形式)。 Deque 支持线程安全，高度节省内存地从 deque 的任一端添加和弹出条目，在两个方向上的大致性能均为 *O*(1)。

​	虽然 [`list`]({{< ref "/library/stdtypes#list" >}}) 对象也支持类似的操作，但它们是针对快速的固定长度的操作进行优化而 `pop(0)` 和 `insert(0, v)` 操作对下层数据表示的大小和位置改变都将产生 *O*(*n*) 的内存移动开销。

​	如果 *maxlen* 没有指定或者是 `None` ，deques 可以增长到任意长度。否则，deque就限定到指定最大长度。一旦限定长度的deque满了，当新项加入时，同样数量的项就从另一端弹出。限定长度deque提供类似Unix filter `tail` 的功能。它们同样可以用与追踪最近的交换和其他数据池活动。

​	双向队列(deque)对象支持以下方法：

## **append**(*x*)

​	添加 *x* 到右端。

## **appendleft**(*x*)

​	添加 *x* 到左端。

## **clear**()

​	移除所有元素，使其长度为0.

## **copy**()

​	创建一份浅拷贝。

> Added in version 3.5.
>

## **count**(*x*)

​	计算 deque 中元素等于 *x* 的个数。

> Added in version 3.2.
>

## **extend**(*iterable*)

​	扩展deque的右侧，通过添加iterable参数中的元素。

## **extendleft**(*iterable*)

​	扩展deque的左侧，通过添加iterable参数中的元素。注意，左添加时，在结果中iterable参数中的顺序将被反过来添加。

## **index**(*x*[, *start*[, *stop*]])

​	返回 *x* 在 deque 中的位置（在索引 *start* 之后，索引 *stop* 之前）。 返回第一个匹配项，如果未找到则引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

> Added in version 3.5.
>

## **insert**(*i*, *x*)

​	在位置 *i* 插入 *x* 。

​	如果插入会导致一个限长 deque 超出长度 *maxlen* 的话，就引发一个 [`IndexError`]({{< ref "/library/exceptions#IndexError" >}})。

> Added in version 3.5.
>

## **pop**()

​	移去并且返回一个元素，deque 最右侧的那一个。 如果没有元素的话，就引发一个 [`IndexError`]({{< ref "/library/exceptions#IndexError" >}})。

## **popleft**()

​	移去并且返回一个元素，deque 最左侧的那一个。 如果没有元素的话，就引发 [`IndexError`]({{< ref "/library/exceptions#IndexError" >}})。

## **remove**(*value*)

​	移除找到的第一个 *value*。 如果没有的话就引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

## **reverse**()

​	将deque逆序排列。返回 `None` 。

> Added in version 3.2.
>

## **rotate**(*n=1*)

​	向右循环移动 *n* 步。 如果 *n* 是负数，就向左循环。

​	如果deque不是空的，向右循环移动一步就等价于 `d.appendleft(d.pop())` ， 向左循环一步就等价于 `d.append(d.popleft())` 。

​	Deque对象同样提供了一个只读属性:

## **maxlen**

​	Deque的最大尺寸，如果没有限定的话就是 `None` 。

> Added in version 3.1.
>

​	在上述操作以外，deque 还支持迭代, 封存, `len(d)`, `reversed(d)`, `copy.copy(d)`, `copy.deepcopy(d)`, 使用 [`in`]({{< ref "/reference/expressions#in" >}}) 运算符的成员检测以及下标引用例如通过 `d[0]` 访问首个元素等。 索引访问在两端的时间复杂度均为 *O*(1) 但在中间则会低至 *O*(*n*)。 对于快速随机访问，请改用列表。

​	Deque从版本3.5开始支持 `__add__()`, `__mul__()`, 和 `__imul__()` 。

​	示例:



``` python
>>> from collections import deque
>>> d = deque('ghi')                 # make a new deque with three items
>>> for elem in d:                   # iterate over the deque's elements
...     print(elem.upper())
G
H
I

>>> d.append('j')                    # add a new entry to the right side
>>> d.appendleft('f')                # add a new entry to the left side
>>> d                                # show the representation of the deque
deque(['f', 'g', 'h', 'i', 'j'])

>>> d.pop()                          # return and remove the rightmost item
'j'
>>> d.popleft()                      # return and remove the leftmost item
'f'
>>> list(d)                          # list the contents of the deque
['g', 'h', 'i']
>>> d[0]                             # peek at leftmost item
'g'
>>> d[-1]                            # peek at rightmost item
'i'

>>> list(reversed(d))                # list the contents of a deque in reverse
['i', 'h', 'g']
>>> 'h' in d                         # search the deque
True
>>> d.extend('jkl')                  # add multiple elements at once
>>> d
deque(['g', 'h', 'i', 'j', 'k', 'l'])
>>> d.rotate(1)                      # right rotation
>>> d
deque(['l', 'g', 'h', 'i', 'j', 'k'])
>>> d.rotate(-1)                     # left rotation
>>> d
deque(['g', 'h', 'i', 'j', 'k', 'l'])

>>> deque(reversed(d))               # make a new deque in reverse order
deque(['l', 'k', 'j', 'i', 'h', 'g'])
>>> d.clear()                        # empty the deque
>>> d.pop()                          # cannot pop from an empty deque
Traceback (most recent call last):
    File "<pyshell#6>", line 1, in -toplevel-
        d.pop()
IndexError: pop from an empty deque

>>> d.extendleft('abc')              # extendleft() reverses the input order
>>> d
deque(['c', 'b', 'a'])
```

### [`deque`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.deque) 用法

​	这一节展示了deque的多种用法。

​	限长deque提供了类似Unix `tail` 过滤功能

```
def tail(filename, n=10):
    '返回文件的最后 n 行'
    with open(filename) as f:
        return deque(f, n)
```

​	另一个用法是维护一个近期添加元素的序列，通过从右边添加和从左边弹出

```
def moving_average(iterable, n=3):
    # moving_average([40, 30, 50, 46, 39, 44]) --> 40.0 42.0 45.0 43.0
    # https://en.wikipedia.org/wiki/Moving_average
    it = iter(iterable)
    d = deque(itertools.islice(it, n-1))
    d.appendleft(0)
    s = sum(d)
    for elem in it:
        s += elem - d.popleft()
        d.append(elem)
        yield s / n
```

​	一个 [轮询调度器](https://en.wikipedia.org/wiki/Round-robin_scheduling) 可以通过在 [`deque`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.deque) 中放入迭代器来实现。值从当前迭代器的位置0被取出并暂存(yield)。 如果这个迭代器消耗完毕，就用 [`popleft()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.deque.popleft) 将其从对列中移去；否则，就通过 [`rotate()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.deque.rotate) 将它移到队列的末尾

```
def roundrobin(*iterables):
    "roundrobin('ABC', 'D', 'EF') --> A D E B F C"
    iterators = deque(map(iter, iterables))
    while iterators:
        try:
            while True:
                yield next(iterators[0])
                iterators.rotate(-1)
        except StopIteration:
            # 移除已耗尽的迭代器。
            iterators.popleft()
```

[`rotate()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.deque.rotate) 方法提供了一种方式来实现 [`deque`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.deque) 切片和删除。 例如， 一个纯的Python `del d[n]` 实现依赖于 `rotate()` 来定位要弹出的元素

```
def delete_nth(d, n):
    d.rotate(-n)
    d.popleft()
    d.rotate(n)
```

​	要实现 [`deque`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.deque) 切片， 使用一个类似的方法，应用 [`rotate()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.deque.rotate) 将目标元素放到左边。通过 [`popleft()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.deque.popleft) 移去老的条目（entries），通过 [`extend()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.deque.extend) 添加新的条目， 然后反向 rotate。这个方法可以最小代价实现命令式的栈操作，诸如 `dup`, `drop`, `swap`, `over`, `pick`, `rot`, 和 `roll` 。

## [`defaultdict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.defaultdict) 对象

## *class* collections.**defaultdict**(*default_factory=None*, */*[, *...*])

​	返回一个新的类似字典的对象。 [`defaultdict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.defaultdict) 是内置 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 类的子类。 它重写了一个方法并添加了一个可写的实例变量。 其余的功能与 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 类相同因而不在此文档中写明。

​	本对象包含一个名为 [`default_factory`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.defaultdict.default_factory) 的属性，构造时，第一个参数用于为该属性提供初始值，默认为 `None`。所有其他参数（包括关键字参数）都相当于传递给 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 的构造函数。

[`defaultdict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.defaultdict) 对象除了支持标准 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 的操作，还支持以下方法作为扩展：

## `__missing__`(*key*)

​	如果 [`default_factory`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.defaultdict.default_factory) 属性为 `None`，则调用本方法会抛出 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}}) 异常，附带参数 *key*。

​	如果 [`default_factory`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.defaultdict.default_factory) 不为 `None`，则它会被（不带参数地）调用来为 *key* 提供一个默认值，这个值和 *key* 作为一对键值对被插入到字典中，并作为本方法的返回值返回。

​	如果调用 [`default_factory`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.defaultdict.default_factory) 时抛出了异常，这个异常会原封不动地向外层传递。

​	当请求的键未找到时本方法会被 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 类的 [`__getitem__()`]({{< ref "/reference/datamodel#object.__getitem__" >}}) 方法调用；它返回或引发的任何对象都会被 [`__getitem__()`]({{< ref "/reference/datamodel#object.__getitem__" >}}) 返回或引发。

​	请注意除了 [`__getitem__()`]({{< ref "/reference/datamodel#object.__getitem__" >}}) 之外 [`__missing__()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.defaultdict.__missing__) *不会* 被调用进行任何操作。 这意味着 `get()` 会像普通字典一样返回 `None` 作为默认值而不是使用 [`default_factory`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.defaultdict.default_factory)。

[`defaultdict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.defaultdict) 对象支持以下实例变量：

## **default_factory**

​	本属性由 [`__missing__()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.defaultdict.__missing__) 方法来调用。如果构造对象时提供了第一个参数，则本属性会被初始化成那个参数，如果未提供第一个参数，则本属性为 `None`。

> 在 3.9 版本发生变更: 增加了合并 (`|`) 与更新 (`|=`) 运算符，相关说明见 [**PEP 584**](https://peps.python.org/pep-0584/)。

### [`defaultdict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.defaultdict) 例子

​	使用 [`list`]({{< ref "/library/stdtypes#list" >}}) 作为 [`default_factory`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.defaultdict.default_factory)，很轻松地将（键-值对组成的）序列转换为（键-列表组成的）字典：



``` python
>>> s = [('yellow', 1), ('blue', 2), ('yellow', 3), ('blue', 4), ('red', 1)]
>>> d = defaultdict(list)
>>> for k, v in s:
...     d[k].append(v)
...
>>> sorted(d.items())
[('blue', [2, 4]), ('red', [1]), ('yellow', [1, 3])]
```

​	当每个键第一次遇见时，它还没有在字典里面，所以自动创建该条目，即调用 [`default_factory`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.defaultdict.default_factory) 方法，返回一个空的 [`list`]({{< ref "/library/stdtypes#list" >}})。 `list.append()` 操作添加值到这个新的列表里。当再次存取该键时，就正常操作，`list.append()` 添加另一个值到列表中。这个计数比它的等价方法 [`dict.setdefault()`]({{< ref "/library/stdtypes#dict.setdefault" >}}) 要快速和简单：



``` python
>>> d = {}
>>> for k, v in s:
...     d.setdefault(k, []).append(v)
...
>>> sorted(d.items())
[('blue', [2, 4]), ('red', [1]), ('yellow', [1, 3])]
```

​	设置 [`default_factory`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.defaultdict.default_factory) 为 [`int`]({{< ref "/library/functions#int" >}})，使 [`defaultdict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.defaultdict) 用于计数（类似其他语言中的 bag 或 multiset）：



``` python
>>> s = 'mississippi'
>>> d = defaultdict(int)
>>> for k in s:
...     d[k] += 1
...
>>> sorted(d.items())
[('i', 4), ('m', 1), ('p', 2), ('s', 4)]
```

​	当一个字母首次遇到时，它会查询失败，则 [`default_factory`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.defaultdict.default_factory) 会调用 [`int()`]({{< ref "/library/functions#int" >}}) 来提供一个整数 0 作为默认值。后续的自增操作建立起对每个字母的计数。

​	函数 [`int()`]({{< ref "/library/functions#int" >}}) 总是返回 0，这是常数函数的特殊情况。一个更快和灵活的方法是使用 lambda 函数，可以提供任何常量值（不只是0）：



``` python
>>> def constant_factory(value):
...     return lambda: value
...
>>> d = defaultdict(constant_factory('<missing>'))
>>> d.update(name='John', action='ran')
>>> '%(name)s %(action)s to %(object)s' % d
'John ran to <missing>'
```

​	设置 [`default_factory`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.defaultdict.default_factory) 为 [`set`]({{< ref "/library/stdtypes#set" >}}) 使 [`defaultdict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.defaultdict) 用于构建 set 集合：



``` python
>>> s = [('red', 1), ('blue', 2), ('red', 3), ('blue', 4), ('red', 1), ('blue', 4)]
>>> d = defaultdict(set)
>>> for k, v in s:
...     d[k].add(v)
...
>>> sorted(d.items())
[('blue', {2, 4}), ('red', {1, 3})]
```

## [`namedtuple()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.namedtuple) 命名元组的工厂函数

​	命名元组赋予每个位置一个含义，提供可读性和自文档性。它们可以用于任何普通元组，并添加了通过名字获取值的能力，通过索引值也是可以的。

## collections.**namedtuple**(*typename*, *field_names*, ***, *rename=False*, *defaults=None*, *module=None*)

​	返回一个新的元组子类，名为 *typename* 。这个新的子类用于创建类元组的对象，可以通过字段名来获取属性值，同样也可以通过索引和迭代获取值。子类实例同样有文档字符串（类名和字段名）另外一个有用的 `__repr__()` 方法，以 `name=value` 格式列明了元组内容。

*field_names* 是一个像 `[‘x’, ‘y’]` 一样的字符串序列。另外 *field_names* 可以是一个纯字符串，用空白或逗号分隔开元素名，比如 `'x y'` 或者 `'x, y'` 。

​	任何有效的Python 标识符都可以作为字段名，除了下划线开头的那些。有效标识符由字母，数字，下划线组成，但首字母不能是数字或下划线，另外不能是关键词 [`keyword`]({{< ref "/library/language/keyword#module-keyword" >}}) 比如 *class*, *for*, *return*, *global*, *pass*, 或 *raise* 。

​	如果 *rename* 为真， 无效字段名会自动转换成位置名。比如 `['abc', 'def', 'ghi', 'abc']` 转换成 `['abc', '_1', 'ghi', '_3']` ， 消除关键词 `def` 和重复字段名 `abc` 。

*defaults* 可以为 `None` 或者是一个默认值的 [iterable]({{< ref "/glossary/idx#term-iterable" >}}) 。如果一个默认值域必须跟其他没有默认值的域在一起出现，*defaults* 就应用到最右边的参数。比如如果域名 `['x', 'y', 'z']` 和默认值 `(1, 2)` ，那么 `x` 就必须指定一个参数值 ，`y` 默认值 `1` ， `z` 默认值 `2` 。

​	If *module* is defined, the [`__module__`]({{< ref "/reference/datamodel#type.__module__" >}}) attribute of the named tuple is set to that value.

​	具名元组实例毋需字典来保存每个实例的不同属性，所以它们轻量，占用的内存和普通元组一样。

​	要支持封存操作，应当将命名元组类赋值给一个匹配 *typename* 的变量。

> 在 3.1 版本发生变更: 添加了对 *rename* 的支持。

> 在 3.6 版本发生变更: *verbose* 和 *rename* 参数成为 [仅限关键字参数]({{< ref "/glossary/idx#keyword-only-parameter" >}}).

> 在 3.6 版本发生变更: 添加了 *module* 参数。

> 在 3.7 版本发生变更: 移除了 *verbose* 形参和 `_source` 属性。

> 在 3.7 版本发生变更: 添加了 *defaults* 参数和 `_field_defaults` 属性。



``` python
>>> # Basic example
>>> Point = namedtuple('Point', ['x', 'y'])
>>> p = Point(11, y=22)     # instantiate with positional or keyword arguments
>>> p[0] + p[1]             # indexable like the plain tuple (11, 22)
33
>>> x, y = p                # unpack like a regular tuple
>>> x, y
(11, 22)
>>> p.x + p.y               # fields also accessible by name
33
>>> p                       # readable __repr__ with a name=value style
Point(x=11, y=22)
```

​	命名元组尤其有用于赋值 [`csv`]({{< ref "/library/fileformats/csv#module-csv" >}}) [`sqlite3`]({{< ref "/library/persistence/sqlite3#module-sqlite3" >}}) 模块返回的元组

```
EmployeeRecord = namedtuple('EmployeeRecord', 'name, age, title, department, paygrade')

import csv
for emp in map(EmployeeRecord._make, csv.reader(open("employees.csv", "rb"))):
    print(emp.name, emp.title)

import sqlite3
conn = sqlite3.connect('/companydata')
cursor = conn.cursor()
cursor.execute('SELECT name, age, title, department, paygrade FROM employees')
for emp in map(EmployeeRecord._make, cursor.fetchall()):
    print(emp.name, emp.title)
```

​	除了继承元组的方法，命名元组还支持三个额外的方法和两个属性。为了防止字段名冲突，方法和属性以下划线开始。

## *classmethod* somenamedtuple.**_make**(*iterable*)

​	类方法从存在的序列或迭代实例创建一个新实例。



``` python
>>> t = [11, 22]
>>> Point._make(t)
Point(x=11, y=22)
```

## somenamedtuple.**_asdict**()

​	返回一个新的 [`dict`]({{< ref "/library/stdtypes#dict" >}}) ，它将字段名称映射到它们对应的值：



``` python
>>> p = Point(x=11, y=22)
>>> p._asdict()
{'x': 11, 'y': 22}
```

> 在 3.1 版本发生变更: 返回一个 [`OrderedDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.OrderedDict) 而不是 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 。

> 在 3.8 版本发生变更: 返回一个常规 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 而不是 [`OrderedDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.OrderedDict)。 因为自 Python 3.7 起，常规字典已经保证有序。 如果需要 [`OrderedDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.OrderedDict) 的额外特性，推荐的解决方案是将结果转换为需要的类型: `OrderedDict(nt._asdict())`。

## somenamedtuple.**_replace**(***kwargs*)

​	返回一个新的命名元组实例，并将指定域替换为新的值



``` python
>>> p = Point(x=11, y=22)
>>> p._replace(x=33)
Point(x=33, y=22)

>>> for partnum, record in inventory.items():
...     inventory[partnum] = record._replace(price=newprices[partnum], timestamp=time.now())
```

​	泛型函数 [`copy.replace()`]({{< ref "/library/datatypes/copy#copy.replace" >}}) 也支持具名元组。

> 在 3.13 版本发生变更: 对于无效的关键字参数将引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}}) 而不是 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

## somenamedtuple.**_fields**

​	字符串元组列出了字段名。用于提醒和从现有元组创建一个新的命名元组类型。



``` python
>>> p._fields            # view the field names
('x', 'y')

>>> Color = namedtuple('Color', 'red green blue')
>>> Pixel = namedtuple('Pixel', Point._fields + Color._fields)
>>> Pixel(11, 22, 128, 255, 0)
Pixel(x=11, y=22, red=128, green=255, blue=0)
```

## somenamedtuple.**_field_defaults**

​	字典将字段名称映射到默认值。



``` python
>>> Account = namedtuple('Account', ['type', 'balance'], defaults=[0])
>>> Account._field_defaults
{'balance': 0}
>>> Account('premium')
Account(type='premium', balance=0)
```

​	要获取这个名字域的值，使用 [`getattr()`]({{< ref "/library/functions#getattr" >}}) 函数 :



``` python
>>> getattr(p, 'x')
11
```

​	转换一个字典到命名元组，使用 ** 两星操作符 (所述如 [解包实参列表]({{< ref "/tutorial/controlflow#tut-unpacking-arguments" >}})):



``` python
>>> d = {'x': 11, 'y': 22}
>>> Point(**d)
Point(x=11, y=22)
```

​	因为一个命名元组是一个正常的Python类，它可以很容易的通过子类更改功能。这里是如何添加一个计算域和定宽输出打印格式:



``` python
>>> class Point(namedtuple('Point', ['x', 'y'])):
...     __slots__ = ()
...     @property
...     def hypot(self):
...         return (self.x ** 2 + self.y ** 2) ** 0.5
...     def __str__(self):
...         return 'Point: x=%6.3f  y=%6.3f  hypot=%6.3f' % (self.x, self.y, self.hypot)

>>> for p in Point(3, 4), Point(14, 5/7):
...     print(p)
Point: x= 3.000  y= 4.000  hypot= 5.000
Point: x=14.000  y= 0.714  hypot=14.018
```

​	上面的子类设置 `__slots__` 为一个空元组。通过阻止创建实例字典保持了较低的内存开销。

​	子类化对于添加和存储新的名字域是无效的。应当通过 [`_fields`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.somenamedtuple._fields) 创建一个新的命名元组来实现它:



``` python
>>> Point3D = namedtuple('Point3D', Point._fields + ('z',))
```

​	文档字符串可以自定义，通过直接赋值给 `__doc__` 属性:



``` python
>>> Book = namedtuple('Book', ['id', 'title', 'authors'])
>>> Book.__doc__ += ': Hardcover book in active collection'
>>> Book.id.__doc__ = '13-digit ISBN'
>>> Book.title.__doc__ = 'Title of first printing'
>>> Book.authors.__doc__ = 'List of authors sorted by last name'
```

> 在 3.5 版本发生变更: 文档字符串属性变成可写。

​参见
- 请参阅 [`typing.NamedTuple`]({{< ref "/library/development/typing#typing.NamedTuple" >}}) ，以获取为命名元组添加类型提示的方法。 它还使用 [`class`]({{< ref "/reference/compound_stmts#class" >}}) 关键字提供了一种优雅的符号:

  ```
  class Component(NamedTuple):
      part_number: int
      weight: float
      description: Optional[str] = None
  ```

- 对于以字典为底层的可变域名， 参考 [`types.SimpleNamespace()`]({{< ref "/library/datatypes/types#types.SimpleNamespace" >}}) 。

- [`dataclasses`]({{< ref "/library/python/dataclasses#module-dataclasses" >}}) 模块提供了一个装饰器和一些函数，用于自动将生成的特殊方法添加到用户定义的类中。

## [`OrderedDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.OrderedDict) 对象

​	有序词典就像常规词典一样，但有一些与排序操作相关的额外功能。由于内置的 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 类获得了记住插入顺序的能力（在 Python 3.7 中保证了这种新行为），它们变得不那么重要了。

​	一些与 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 的不同仍然存在：

- 常规的 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 被设计为非常擅长映射操作。 跟踪插入顺序是次要的。

- [`OrderedDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.OrderedDict) 旨在擅长重新排序操作。 空间效率、迭代速度和更新操作的性能是次要的。

- [`OrderedDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.OrderedDict) 算法能比 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 更好地处理频繁的重排序操作。 如下面的例程所示，这使得它更适用于实现各种 LRU 缓存。

- 对于 [`OrderedDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.OrderedDict) ，相等操作检查匹配顺序。

  常规的 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 可以使用 `p == q and all(k1 == k2 for k1, k2 in zip(p, q))` 进行模拟顺序相等性测试。

- [`OrderedDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.OrderedDict) 类的 `popitem()` 方法有不同的签名。它接受一个可选参数来指定弹出哪个元素。

  常规的 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 可以使用 `d.popitem()` 模拟 OrderedDict 的 `od.popitem(last=True)`，其保证会返回最右边（最后）的项。

  常规的 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 可以通过 `(k := next(iter(d)), d.pop(k))` 来模拟 OrderedDict 的 `od.popitem(last=False)`，它将返回并移除最左边（开头）的条目，如果条目存在的话。

- [`OrderedDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.OrderedDict) 类有一个 `move_to_end()` 方法，可以有效地将元素移动到任一端。

  常规的 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 可以通过 `d[k] = d.pop(k)` 来模拟 OrderedDict 的 `od.move_to_end(k, last=True)`，它将把键及其所关联的值移到最右边（末尾）的位置。

  常规的 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 没有 OrderedDict 的 `od.move_to_end(k, last=False)` 的高效等价物，它会把键及其所关联的值移到最左边（开头）的位置。

- Python 3.8之前， [`dict`]({{< ref "/library/stdtypes#dict" >}}) 缺少 `__reversed__()` 方法。

## *class* collections.**OrderedDict**([*items*])

​	返回一个 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 子类的实例，它具有专门用于重新排列字典顺序的方法。

> Added in version 3.1.
>

## **popitem**(*last=True*)

​	有序字典的 [`popitem()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.OrderedDict.popitem) 方法移除并返回一个 (key, value) 键值对。 如果 *last* 值为真，则按 LIFO 后进先出的顺序返回键值对，否则就按 FIFO 先进先出的顺序返回键值对。

## **move_to_end**(*key*, *last=True*)

​	将一个现有的 *key* 移到序字典的任一端。 如果 *last* 为真值（默认）则将条目移到右端，或者如果 *last* 为假值则将条目移到开头。 如果 *key* 不存在则会引发 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}}):



``` python
>>> d = OrderedDict.fromkeys('abcde')
>>> d.move_to_end('b')
>>> ''.join(d)
'acdeb'
>>> d.move_to_end('b', last=False)
>>> ''.join(d)
'bacde'
```

> Added in version 3.2.
>

​	相对于通常的映射方法，有序字典还另外提供了逆序迭代的支持，通过 [`reversed()`]({{< ref "/library/functions#reversed" >}}) 。

​	Equality tests between [`OrderedDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.OrderedDict) objects are order-sensitive and are roughly equivalent to `list(od1.items())==list(od2.items())`.

​	Equality tests between [`OrderedDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.OrderedDict) objects and other [`Mapping`]({{< ref "/library/datatypes/collections_abc#collections.abc.Mapping" >}}) objects are order-insensitive like regular dictionaries. This allows [`OrderedDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.OrderedDict) objects to be substituted anywhere a regular dictionary is used.

> 在 3.5 版本发生变更: [`OrderedDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.OrderedDict) 的项(item)，键(key)和值(value) [视图]({{< ref "/glossary/idx#term-dictionary-view" >}}) 现在支持逆序迭代，通过 [`reversed()`]({{< ref "/library/functions#reversed" >}}) 。

> 在 3.6 版本发生变更: [**PEP 468**](https://peps.python.org/pep-0468/) 赞成将关键词参数的顺序保留， 通过传递给 [`OrderedDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.OrderedDict) 构造器和它的 `update()` 方法。

> 在 3.9 版本发生变更: 增加了合并 (`|`) 与更新 (`|=`) 运算符，相关说明见 [**PEP 584**](https://peps.python.org/pep-0584/)。

### [`OrderedDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.OrderedDict) 例子和用法

​	创建记住键值 *最后* 插入顺序的有序字典变体很简单。 如果新条目覆盖现有条目，则原始插入位置将更改并移至末尾:

```
class LastUpdatedOrderedDict(OrderedDict):
    'Store items in the order the keys were last added'

    def __setitem__(self, key, value):
        super().__setitem__(key, value)
        self.move_to_end(key)
```

​	一个 [`OrderedDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.OrderedDict) 对于实现 [`functools.lru_cache()`]({{< ref "/library/functional/functools#functools.lru_cache" >}}) 的变体也很有用:

```
from collections import OrderedDict
from time import time

class TimeBoundedLRU:
    "LRU Cache that invalidates and refreshes old entries."

    def __init__(self, func, maxsize=128, maxage=30):
        self.cache = OrderedDict()      # { args : (timestamp, result)}
        self.func = func
        self.maxsize = maxsize
        self.maxage = maxage

    def __call__(self, *args):
        if args in self.cache:
            self.cache.move_to_end(args)
            timestamp, result = self.cache[args]
            if time() - timestamp <= self.maxage:
                return result
        result = self.func(*args)
        self.cache[args] = time(), result
        if len(self.cache) > self.maxsize:
            self.cache.popitem(last=False)
        return result
class MultiHitLRUCache:
    """ LRU cache that defers caching a result until
        it has been requested multiple times.

        To avoid flushing the LRU cache with one-time requests,
        we don't cache until a request has been made more than once.

    """

    def __init__(self, func, maxsize=128, maxrequests=4096, cache_after=1):
        self.requests = OrderedDict()   # { uncached_key : request_count }
        self.cache = OrderedDict()      # { cached_key : function_result }
        self.func = func
        self.maxrequests = maxrequests  # max number of uncached requests
        self.maxsize = maxsize          # max number of stored return values
        self.cache_after = cache_after

    def __call__(self, *args):
        if args in self.cache:
            self.cache.move_to_end(args)
            return self.cache[args]
        result = self.func(*args)
        self.requests[args] = self.requests.get(args, 0) + 1
        if self.requests[args] <= self.cache_after:
            self.requests.move_to_end(args)
            if len(self.requests) > self.maxrequests:
                self.requests.popitem(last=False)
        else:
            self.requests.pop(args, None)
            self.cache[args] = result
            if len(self.cache) > self.maxsize:
                self.cache.popitem(last=False)
        return result
```

## [`UserDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.UserDict) 对象

[`UserDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.UserDict) 类是用作字典对象的外包装。对这个类的需求已部分由直接创建 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 的子类的功能所替代；不过，这个类处理起来更容易，因为底层的字典可以作为属性来访问。

## *class* collections.**UserDict**([*initialdata*])

​	模拟字典的类。 这个实例的内容保存在一个常规字典中，它可以通过 [`UserDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.UserDict) 实例的 [`data`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.UserDict.data) 属性来访问。 如果提供了 *initialdata*，则 [`data`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.UserDict.data) 会用其内容来初始化；请注意对 *initialdata* 的引用将不会被保留，以允许它被用于其他目的。

[`UserDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.UserDict) 实例提供了以下属性作为扩展方法和操作的支持:

## **data**

​	一个真实的字典，用于保存 [`UserDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.UserDict) 类的内容。

## [`UserList`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.UserList) 对象

​	这个类封装了列表对象。它是一个有用的基础类，对于你想自定义的类似列表的类，可以继承和覆盖现有的方法，也可以添加新的方法。这样我们可以对列表添加新的行为。

​	对这个类的需求已部分由直接创建 [`list`]({{< ref "/library/stdtypes#list" >}}) 的子类的功能所替代；不过，这个类处理起来更容易，因为底层的列表可以作为属性来访问。

## *class* collections.**UserList**([*list*])

​	模拟一个列表。这个实例的内容被保存为一个正常列表，通过 [`UserList`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.UserList) 的 [`data`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.UserList.data) 属性存取。实例内容被初始化为一个 *list* 的copy，默认为 `[]` 空列表。 *list* 可以是迭代对象，比如一个Python列表，或者一个 [`UserList`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.UserList) 对象。

[`UserList`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.UserList) 提供了以下属性作为可变序列的方法和操作的扩展:

## **data**

​	一个 [`list`]({{< ref "/library/stdtypes#list" >}}) 对象用于存储 [`UserList`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.UserList) 的内容。

**子类化的要求:** [`UserList`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.UserList) 的子类需要提供一个构造器，可以无参数调用，或者一个参数调用。返回一个新序列的列表操作需要创建一个实现类的实例。它假定了构造器可以以一个参数进行调用，这个参数是一个序列对象，作为数据源。

​	如果一个分离的类不希望依照这个需求，所有的特殊方法就必须重写；请参照源代码进行修改。

## [`UserString`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.UserString) 对象

[`UserString`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.UserString) 类是用作字符串对象的外包装。对这个类的需求已部分由直接创建 [`str`]({{< ref "/library/stdtypes#str" >}}) 的子类的功能所替代；不过，这个类处理起来更容易，因为底层的字符串可以作为属性来访问。

## *class* collections.**UserString**(*seq*)

​	模拟一个字符串对象。这个实例对象的内容保存为一个正常字符串，通过 [`UserString`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.UserString) 的 [`data`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.UserString.data) 属性存取。实例内容初始化设置为 *seq* 的copy。*seq* 参数可以是任何可通过内建 [`str()`]({{< ref "/library/stdtypes#str" >}}) 函数转换为字符串的对象。

[`UserString`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.UserString) 提供了以下属性作为字符串方法和操作的额外支持：

## **data**

​	一个真正的 [`str`]({{< ref "/library/stdtypes#str" >}}) 对象用来存放 [`UserString`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.UserString) 类的内容。

> 在 3.5 版本发生变更: 新方法 `__getnewargs__`, `__rmod__`, `casefold`, `format_map`, `isprintable`, 和 `maketrans`。
