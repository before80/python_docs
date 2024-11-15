+++
title = "itertools --- 为高效循环创建迭代器的函数"
date = 2024-11-15T11:50:11+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/itertools.html](https://docs.python.org/zh-cn/3.13/library/itertools.html)
>
> 收录该文档的时间：`2024-11-15T11:50:11+08:00`

# `itertools` --- 为高效循环创建迭代器的函数

------

​	本模块实现一系列 [iterator](https://docs.python.org/zh-cn/3.13/glossary.html#term-iterator) ，这些迭代器受到APL，Haskell和SML的启发。为了适用于Python，它们都被重新写过。

​	本模块标准化了一个快速、高效利用内存的核心工具集，这些工具本身或组合都很有用。它们一起形成了“迭代器代数”，这使得在纯Python中有可能创建简洁又高效的专用工具。

​	例如，SML有一个制表工具： `tabulate(f)`，它可产生一个序列 `f(0), f(1), ...`。在Python中可以组合 [`map()`](https://docs.python.org/zh-cn/3.13/library/functions.html#map) 和 [`count()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.count) 实现： `map(f, count())`。

​	这些工具及其内置对应物也能很好地配合 [`operator`](https://docs.python.org/zh-cn/3.13/library/operator.html#module-operator) 模块中的快速函数来使用。 例如，乘法运算符可以被映射到两个向量之间执行高效的点积: `sum(starmap(operator.mul, zip(vec1, vec2, strict=True)))`。

**无穷迭代器：**

| 迭代器                                                       | 实参            | 结果                                  | 示例                                  |
| :----------------------------------------------------------- | :-------------- | :------------------------------------ | :------------------------------------ |
| [`count()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.count) | [start[, step]] | start, start+step, start+2*step, ...  | `count(10) → 10 11 12 13 14 ...`      |
| [`cycle()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.cycle) | p               | p0, p1, ... plast, p0, p1, ...        | `cycle('ABCD') → A B C D A B C D ...` |
| [`repeat()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.repeat) | elem [,n]       | elem, elem, elem, ... 重复无限次或n次 | `repeat(10, 3) → 10 10 10`            |

**根据最短输入序列长度停止的迭代器：**

| 迭代器                                                       | 实参                        | 结果                                          | 示例                                                     |
| :----------------------------------------------------------- | :-------------------------- | :-------------------------------------------- | :------------------------------------------------------- |
| [`accumulate()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.accumulate) | p [,func]                   | p0, p0+p1, p0+p1+p2, ...                      | `accumulate([1,2,3,4,5]) → 1 3 6 10 15`                  |
| [`batched()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.batched) | p, n                        | (p0, p1, ..., p_n-1), ...                     | `batched('ABCDEFG', n=3) → ABC DEF G`                    |
| [`chain()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.chain) | p, q, ...                   | p0, p1, ... plast, q0, q1, ...                | `chain('ABC', 'DEF') → A B C D E F`                      |
| [`chain.from_iterable()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.chain.from_iterable) | iterable -- 可迭代对象      | p0, p1, ... plast, q0, q1, ...                | `chain.from_iterable(['ABC', 'DEF']) → A B C D E F`      |
| [`compress()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.compress) | data, selectors             | (d[0] if s[0]), (d[1] if s[1]), ...           | `compress('ABCDEF', [1,0,1,0,1,1]) → A C E F`            |
| [`dropwhile()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.dropwhile) | predicate, seq              | seq[n], seq[n+1], 从 predicate 未通过时开始   | `dropwhile(lambda x: x<5, [1,4,6,3,8]) → 6 3 8`          |
| [`filterfalse()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.filterfalse) | predicate, seq              | predicate(elem) 未通过的 seq 元素             | `filterfalse(lambda x: x<5, [1,4,6,3,8]) → 6 8`          |
| [`groupby()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.groupby) | iterable[, key]             | 根据key(v)值分组的迭代器                      | `groupby(['A','B','DEF'], len) → (1, A B) (3, DEF)`      |
| [`islice()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.islice) | seq, [start,] stop [, step] | seq[start:stop:step]中的元素                  | `islice('ABCDEFG', 2, None) → C D E F G`                 |
| [`pairwise()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.pairwise) | iterable -- 可迭代对象      | (p[0], p[1]), (p[1], p[2])                    | `pairwise('ABCDEFG') → AB BC CD DE EF FG`                |
| [`starmap()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.starmap) | func, seq                   | func(*seq[0]), func(*seq[1]), ...             | `starmap(pow, [(2,5), (3,2), (10,3)]) → 32 9 1000`       |
| [`takewhile()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.takewhile) | predicate, seq              | seq[0], seq[1], 直到 predicate 未通过         | `takewhile(lambda x: x<5, [1,4,6,3,8]) → 1 4`            |
| [`tee()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.tee) | it, n                       | it1, it2, ... itn 将一个迭代器拆分为n个迭代器 |                                                          |
| [`zip_longest()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.zip_longest) | p, q, ...                   | (p[0], q[0]), (p[1], q[1]), ...               | `zip_longest('ABCD', 'xy', fillvalue='-') → Ax By C- D-` |

**排列组合迭代器：**

| 迭代器                                                       | 实参                 | 结果                                  |
| :----------------------------------------------------------- | :------------------- | :------------------------------------ |
| [`product()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.product) | p, q, ... [repeat=1] | 笛卡尔积，相当于嵌套的for循环         |
| [`permutations()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.permutations) | p[, r]               | 长度r元组，所有可能的排列，无重复元素 |
| [`combinations()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.combinations) | p, r                 | 长度r元组，有序，无重复元素           |
| [`combinations_with_replacement()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.combinations_with_replacement) | p, r                 | 长度r元组，有序，元素可重复           |

| 例子                                       | 结果                                              |
| :----------------------------------------- | :------------------------------------------------ |
| `product('ABCD', repeat=2)`                | `AA AB AC AD BA BB BC BD CA CB CC CD DA DB DC DD` |
| `permutations('ABCD', 2)`                  | `AB AC AD BA BC BD CA CB CD DA DB DC`             |
| `combinations('ABCD', 2)`                  | `AB AC AD BC BD CD`                               |
| `combinations_with_replacement('ABCD', 2)` | `AA AB AC AD BB BC BD CC CD DD`                   |



## Itertool 函数

​	The following functions all construct and return iterators. Some provide streams of infinite length, so they should only be accessed by functions or loops that truncate the stream.

## itertools.**accumulate**(*iterable*[, *function*, ***, *initial=None*])

​	创建一个返回累积汇总值或来自其他双目运算函数的累积结果的迭代器。

*function* 默认为加法运算。 *function* 应当接受两个参数，即一个累积汇总值和一个来自 *iterable* 的值。

​	如果提供了 *initial* 值，将从该值开始累积并且输出将比输入可迭代对象多一个元素。

​	大致相当于：

```
def accumulate(iterable, function=operator.add, *, initial=None):
    'Return running totals'
    # accumulate([1,2,3,4,5]) → 1 3 6 10 15
    # accumulate([1,2,3,4,5], initial=100) → 100 101 103 106 110 115
    # accumulate([1,2,3,4,5], operator.mul) → 1 2 6 24 120

    iterator = iter(iterable)
    total = initial
    if initial is None:
        try:
            total = next(iterator)
        except StopIteration:
            return

    yield total
    for element in iterator:
        total = function(total, element)
        yield total
```

​	To compute a running minimum, set *function* to [`min()`](https://docs.python.org/zh-cn/3.13/library/functions.html#min). For a running maximum, set *function* to [`max()`](https://docs.python.org/zh-cn/3.13/library/functions.html#max). Or for a running product, set *function* to [`operator.mul()`](https://docs.python.org/zh-cn/3.13/library/operator.html#operator.mul). To build an [amortization table](https://www.ramseysolutions.com/real-estate/amortization-schedule), accumulate the interest and apply payments:

\>>>

```
>>> data = [3, 4, 6, 2, 1, 9, 0, 7, 5, 8]
>>> list(accumulate(data, max))              # running maximum
[3, 4, 6, 6, 6, 9, 9, 9, 9, 9]
>>> list(accumulate(data, operator.mul))     # running product
[3, 12, 72, 144, 144, 1296, 0, 0, 0, 0]

# Amortize a 5% loan of 1000 with 10 annual payments of 90
>>> update = lambda balance, payment: round(balance * 1.05) - payment
>>> list(accumulate(repeat(90, 10), update, initial=1_000))
[1000, 960, 918, 874, 828, 779, 728, 674, 618, 559, 497]
```

​	参考一个类似函数 [`functools.reduce()`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.reduce) ，它只返回一个最终累积值。

*Added in version 3.2.*

*在 3.3 版本发生变更:* 添加了可选的 *function* 形参。

*在 3.8 版本发生变更:* 添加了可选的 *initial* 形参。

## itertools.**batched**(*iterable*, *n*, ***, *strict=False*)

​	来自 *iterable* 的长度为 *n* 元组形式的批次数据。 最后一个批次可能短于 *n*。

​	如果 *strict* 为真值，将在最终的批次短于 *n* 时引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

​	循环处理输入可迭代对象并将数据积累为长度至多为 *n* 的元组。 输入将被惰性地消耗，能填满一个批次即可。 结果将在批次填满或输入可迭代对象被耗尽时产生:

\>>>

```
>>> flattened_data = ['roses', 'red', 'violets', 'blue', 'sugar', 'sweet']
>>> unflattened = list(batched(flattened_data, 2))
>>> unflattened
[('roses', 'red'), ('violets', 'blue'), ('sugar', 'sweet')]
```

​	大致相当于：

```
def batched(iterable, n, *, strict=False):
    # batched('ABCDEFG', 3) → ABC DEF G
    if n < 1:
        raise ValueError('n must be at least one')
    iterator = iter(iterable)
    while batch := tuple(islice(iterator, n)):
        if strict and len(batch) != n:
            raise ValueError('batched(): incomplete batch')
        yield batch
```

*Added in version 3.12.*

*在 3.13 版本发生变更:* 增加了 *strict* 选项。

## itertools.**chain**(**iterables*)

​	Make an iterator that returns elements from the first iterable until it is exhausted, then proceeds to the next iterable, until all of the iterables are exhausted. This combines multiple data sources into a single iterator. Roughly equivalent to:

```
def chain(*iterables):
    # chain('ABC', 'DEF') → A B C D E F
    for iterable in iterables:
        yield from iterable
```

## *classmethod* chain.**from_iterable**(*iterable*)

​	构建类似 [`chain()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.chain) 迭代器的另一个选择。从一个单独的可迭代参数中得到链式输入，该参数是延迟计算的。大致相当于：

```
def from_iterable(iterables):
    # chain.from_iterable(['ABC', 'DEF']) → A B C D E F
    for iterable in iterables:
        yield from iterable
```

## itertools.**combinations**(*iterable*, *r*)

​	返回由输入 *iterable* 中元素组成长度为 *r* 的子序列。

​	输出结果是 [`product()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.product) 的子序列其中只保留属于 *iterable* 的子序列的条目。 输出的长度由 [`math.comb()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.comb) 给出，该函数在 `0 ≤ r ≤ n` 时为计算 `n! / r! / (n - r)!` 而在 `r > n` 时为 0。

​	组合元组是根据输入的 *iterable* 的顺序以词典排序方式发出的。 如果输入的 *iterable* 是已排序的，则输出的元组将按排序后的顺序产生。

​	元素是的唯一性是基于它们的位置，而不是它们的值。 如果输入的元素都是唯一的，则将每个组合中将不会有重复的值。

​	大致相当于：

```
def combinations(iterable, r):
    # combinations('ABCD', 2) → AB AC AD BC BD CD
    # combinations(range(4), 3) → 012 013 023 123

    pool = tuple(iterable)
    n = len(pool)
    if r > n:
        return
    indices = list(range(r))

    yield tuple(pool[i] for i in indices)
    while True:
        for i in reversed(range(r)):
            if indices[i] != i + n - r:
                break
        else:
            return
        indices[i] += 1
        for j in range(i+1, r):
            indices[j] = indices[j-1] + 1
        yield tuple(pool[i] for i in indices)
```

## itertools.**combinations_with_replacement**(*iterable*, *r*)

​	返回由输入 *iterable* 中元素组成的长度为 *r* 的子序列，允许每个元素可重复出现。

​	输出是 [`product()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.product) 的子序列，其中仅保留也属于 *iterable* 的子序列的条目（可能有重复的元素）。 当 `n > 0` 时返回的子序列数量为 `(n + r - 1)! / r! / (n - 1)!`。

​	组合元组是根据输入的 *iterable* 的顺序以词典排序方式发出的。 如果输入的 *iterable* 是已排序的，则输出的元组将按已排序的顺序产生。

​	元素的唯一性是基于它们的位置，而不是它们的值。 如果输入的元素都是唯一的，则生成的组合也将是唯一的。

​	大致相当于：

```
def combinations_with_replacement(iterable, r):
    # combinations_with_replacement('ABC', 2) → AA AB AC BB BC CC

    pool = tuple(iterable)
    n = len(pool)
    if not n and r:
        return
    indices = [0] * r

    yield tuple(pool[i] for i in indices)
    while True:
        for i in reversed(range(r)):
            if indices[i] != n - 1:
                break
        else:
            return
        indices[i:] = [indices[i] + 1] * (r - i)
        yield tuple(pool[i] for i in indices)
```

*Added in version 3.1.*

## itertools.**compress**(*data*, *selectors*)

​	创建一个迭代器，它返回来自 *data* 在 *selectors* 中对应元素为真值的元素。 当 *data* 或 *selectors* 可迭代对象被耗尽时将停止。 大致相当于:

```
def compress(data, selectors):
    # compress('ABCDEF', [1,0,1,0,1,1]) → A C E F
    return (datum for datum, selector in zip(data, selectors) if selector)
```

*Added in version 3.1.*

## itertools.**count**(*start=0*, *step=1*)

​	创建一个迭代器，它返回从 *start* 开始的均匀间隔的值。 可与 [`map()`](https://docs.python.org/zh-cn/3.13/library/functions.html#map) 配合使用以生成连续的数据点或与 [`zip()`](https://docs.python.org/zh-cn/3.13/library/functions.html#zip) 配合使用以添加序列数字。 大致相当于:

```
def count(start=0, step=1):
    # count(10) → 10 11 12 13 14 ...
    # count(2.5, 0.5) → 2.5 3.0 3.5 ...
    n = start
    while True:
        yield n
        n += step
```

​	当对浮点数计数时，替换为乘法代码有时会有更高的精度，例如: `(start + step * i for i in count())`。

*在 3.1 版本发生变更:* 增加参数 *step* ，允许非整型。

## itertools.**cycle**(*iterable*)

​	创建一个迭代器，它返回来自 *iterable* 中的元素并保存每个元素的拷贝。 当 iterable 耗尽时，返回来自已保存拷贝中的元素。 将无限重复进行。 大致相当于:

```
def cycle(iterable):
    # cycle('ABCD') → A B C D A B C D A B C D ...

    saved = []
    for element in iterable:
        yield element
        saved.append(element)

    while saved:
        for element in saved:
            yield element
```

​	这个迭代工具可能需要很大的辅助存储（取决于 iterable 的长度）。

## itertools.**dropwhile**(*predicate*, *iterable*)

​	创建一个迭代器，它将丢弃来自 *iterable* 中 *predicate* 为真值的元素然后返回每个元素。 大致相当于:

```
def dropwhile(predicate, iterable):
    # dropwhile(lambda x: x<5, [1,4,6,3,8]) → 6 3 8

    iterator = iter(iterable)
    for x in iterator:
        if not predicate(x):
            yield x
            break

    for x in iterator:
        yield x
```

​	请注意它将不产生 *任何* 输出直到 predicate 首次变为假值，所以此迭代工具可能具有很长的启动时间。

## itertools.**filterfalse**(*predicate*, *iterable*)

​	创建一个迭代器，它过滤来自 *iterable* 的元素从而只返回其中 *predicate* 返回假值的元素。 如果 *predicate* 为 `None`，则返回本身为假值的条目。 大致相当于:

```
def filterfalse(predicate, iterable):
    # filterfalse(lambda x: x<5, [1,4,6,3,8]) → 6 8

    if predicate is None:
        predicate = bool

    for x in iterable:
        if not predicate(x):
            yield x
```

## itertools.**groupby**(*iterable*, *key=None*)

​	创建一个迭代器，返回 *iterable* 中连续的键和组。*key* 是一个计算元素键值函数。如果未指定或为 `None`，*key* 缺省为恒等函数（identity function），返回元素不变。一般来说，*iterable* 需用同一个键值函数预先排序。

[`groupby()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.groupby) 操作类似于Unix中的 `uniq`。当每次 *key* 函数产生的键值改变时，迭代器会分组或生成一个新组（这就是为什么通常需要使用同一个键值函数先对数据进行排序）。这种行为与SQL的GROUP BY操作不同，SQL的操作会忽略输入的顺序将相同键值的元素分在同组中。

​	返回的组本身也是一个迭代器，它与 [`groupby()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.groupby) 共享底层的可迭代对象。因为源是共享的，当 [`groupby()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.groupby) 对象向后迭代时，前一个组将消失。因此如果稍后还需要返回结果，可保存为列表：

```
groups = []
uniquekeys = []
data = sorted(data, key=keyfunc)
for k, g in groupby(data, keyfunc):
    groups.append(list(g))      # 将 group 迭代器以列表形式保存
    uniquekeys.append(k)
```

[`groupby()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.groupby) 大致相当于：

```
def groupby(iterable, key=None):
    # [k for k, g in groupby('AAAABBBCCDAABBB')] → A B C D A B
    # [list(g) for k, g in groupby('AAAABBBCCD')] → AAAA BBB CC D

    keyfunc = (lambda x: x) if key is None else key
    iterator = iter(iterable)
    exhausted = False

    def _grouper(target_key):
        nonlocal curr_value, curr_key, exhausted
        yield curr_value
        for curr_value in iterator:
            curr_key = keyfunc(curr_value)
            if curr_key != target_key:
                return
            yield curr_value
        exhausted = True

    try:
        curr_value = next(iterator)
    except StopIteration:
        return
    curr_key = keyfunc(curr_value)

    while not exhausted:
        target_key = curr_key
        curr_group = _grouper(target_key)
        yield curr_key, curr_group
        if curr_key == target_key:
            for _ in curr_group:
                pass
```

## itertools.**islice**(*iterable*, *stop*)

## itertools.**islice**(*iterable*, *start*, *stop*[, *step*])

​	创建一个迭代器，它返回 iterable 的选定元素。 效果与序列切片类似但不支持负的 *start*, *stop* 或 *step* 值。

​	如果 *start* 为零或为 `None`，迭代将从零开始。 在其他情况下，iterable 中的元素将被跳过直至到达 *start*。

​	If *stop* is `None`, iteration continues until the input is exhausted, if at all. Otherwise, it stops at the specified position.

​	如果 *step* 为 `None`，则步长默认为一。 元素将被逐一返回除非 *step* 被设为大于一的数，此情况将导致部分条目被跳过。

​	大致相当于：

```
def islice(iterable, *args):
    # islice('ABCDEFG', 2) → A B
    # islice('ABCDEFG', 2, 4) → C D
    # islice('ABCDEFG', 2, None) → C D E F G
    # islice('ABCDEFG', 0, None, 2) → A C E G

    s = slice(*args)
    start = 0 if s.start is None else s.start
    stop = s.stop
    step = 1 if s.step is None else s.step
    if start < 0 or (stop is not None and stop < 0) or step <= 0:
        raise ValueError

    indices = count() if stop is None else range(max(start, stop))
    next_i = start
    for i, element in zip(indices, iterable):
        if i == next_i:
            yield element
            next_i += step
```

​	If the input is an iterator, then fully consuming the *islice* advances the input iterator by `max(start, stop)` steps regardless of the *step* value.

## itertools.**pairwise**(*iterable*)

​	返回从输入 *iterable* 中获取的连续重叠对。

​	输出迭代器中 2 元组的数量将比输入的数量少一个。 如果输入可迭代对象中少于两个值则它将为空。

​	大致相当于：

```
def pairwise(iterable):
    # pairwise('ABCDEFG') → AB BC CD DE EF FG

    iterator = iter(iterable)
    a = next(iterator, None)

    for b in iterator:
        yield a, b
        a = b
```

*Added in version 3.10.*

## itertools.**permutations**(*iterable*, *r=None*)

​	根据 *iterable* 返回连续的 *r* 长度 [元素的排列](https://www.britannica.com/science/permutation)。

​	如果 *r* 未指定或为 `None` ，*r* 默认设置为 *iterable* 的长度，这种情况下，生成所有全长排列。

​	输出结果是 [`product()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.product) 的子序列并已过滤掉其中的重复元素。 输出的长度由 [`math.perm()`](https://docs.python.org/zh-cn/3.13/library/math.html#math.perm) 给出，它在 `0 ≤ r ≤ n` 时为计算 `n! / (n - r)!` 而在 `r > n` 时则为零。

​	排列元组是根据输入的 *iterable* 的顺序以词典排序的形式发出的。 如果输入的 *iterable* 是已排序的，则输出的元组将按已排序的顺序产生。

​	元素的唯一性是基于它们的位置，而不是它们的值。 如果输入的元素都是唯一的，则在排列中就不会有重复的元素。

​	大致相当于：

```
def permutations(iterable, r=None):
    # permutations('ABCD', 2) → AB AC AD BA BC BD CA CB CD DA DB DC
    # permutations(range(3)) → 012 021 102 120 201 210

    pool = tuple(iterable)
    n = len(pool)
    r = n if r is None else r
    if r > n:
        return

    indices = list(range(n))
    cycles = list(range(n, n-r, -1))
    yield tuple(pool[i] for i in indices[:r])

    while n:
        for i in reversed(range(r)):
            cycles[i] -= 1
            if cycles[i] == 0:
                indices[i:] = indices[i+1:] + indices[i:i+1]
                cycles[i] = n - i
            else:
                j = cycles[i]
                indices[i], indices[-j] = indices[-j], indices[i]
                yield tuple(pool[i] for i in indices[:r])
                break
        else:
            return
```

## itertools.**product**(**iterables*, *repeat=1*)

[Cartesian product](https://en.wikipedia.org/wiki/Cartesian_product) of the input iterables.

​	大致相当于生成器表达式中的嵌套循环。例如， `product(A, B)` 和 `((x,y) for x in A for y in B)` 返回结果一样。

​	嵌套循环像里程表那样循环变动，每次迭代时将最右侧的元素向后迭代。这种模式形成了一种字典序，因此如果输入的可迭代对象是已排序的，笛卡尔积元组依次序发出。

​	要计算可迭代对象自身的笛卡尔积，将可选参数 *repeat* 设定为要重复的次数。例如，`product(A, repeat=4)` 和 `product(A, A, A, A)` 是一样的。

​	该函数大致相当于下面的代码，只不过实际实现方案不会在内存中创建中间结果。

```
def product(*iterables, repeat=1):
    # product('ABCD', 'xy') → Ax Ay Bx By Cx Cy Dx Dy
    # product(range(2), repeat=3) → 000 001 010 011 100 101 110 111

    if repeat < 0:
        raise ValueError('repeat argument cannot be negative')
    pools = [tuple(pool) for pool in iterables] * repeat

    result = [[]]
    for pool in pools:
        result = [x+[y] for x in result for y in pool]

    for prod in result:
        yield tuple(prod)
```

​	在 [`product()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.product) 运行之前，它会完全耗尽输入的可迭代对象，在内存中保留值的临时池以生成结果积。 相应地，它只适用于有限的输入。

## itertools.**repeat**(*object*[, *times*])

​	创建一个持续地返回 *object* 的迭代器。 将会无限期地运行除非指定了 *times* 参数。

​	大致相当于：

```
def repeat(object, times=None):
    # repeat(10, 3) → 10 10 10
    if times is None:
        while True:
            yield object
    else:
        for i in range(times):
            yield object
```

*repeat* 的一个常见用途是向 *map* 或 *zip* 提供一个常量值的流:

\>>>

```
>>> list(map(pow, range(10), repeat(2)))
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```

## itertools.**starmap**(*function*, *iterable*)

​	创建一个迭代器，它使用从 *iterable* 获取的参数来计算 *function*。 当参数形参已被“预先 zip”为元组时可代替 [`map()`](https://docs.python.org/zh-cn/3.13/library/functions.html#map) 来使用。

[`map()`](https://docs.python.org/zh-cn/3.13/library/functions.html#map) 和 [`starmap()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.starmap) 之间的区别类似于 `function(a,b)` 和 `function(*c)` 之间的差异。 大致相当于:

```
def starmap(function, iterable):
    # starmap(pow, [(2,5), (3,2), (10,3)]) → 32 9 1000
    for args in iterable:
        yield function(*args)
```

## itertools.**takewhile**(*predicate*, *iterable*)

​	创建一个迭代器，它返回来自 *iterable* 的 *predicate* 为真值的元素。 大致相当于:

```
def takewhile(predicate, iterable):
    # takewhile(lambda x: x<5, [1,4,6,3,8]) → 1 4
    for x in iterable:
        if not predicate(x):
            break
        yield x
```

​	请注意，第一个未能满足 predicate 条件的元素将从输入迭代器中消耗掉并且没有办法访问它。 当应用程序想在 *takewhile* 运行到耗尽后进一步消耗输入迭代器时这可能会造成问题。 要绕过这个问题，可以考虑改用 [more-iterools before_and_after()](https://more-itertools.readthedocs.io/en/stable/api.html#more_itertools.before_and_after)。

## itertools.**tee**(*iterable*, *n=2*)

​	从一个可迭代对象中返回 *n* 个独立的迭代器。

​	大致相当于：

```
def tee(iterable, n=2):
    if n < 0:
        raise ValueError
    if n == 0:
        return ()
    iterator = _tee(iterable)
    result = [iterator]
    for _ in range(n - 1):
        result.append(_tee(iterator))
    return tuple(result)

class _tee:

    def __init__(self, iterable):
        it = iter(iterable)
        if isinstance(it, _tee):
            self.iterator = it.iterator
            self.link = it.link
        else:
            self.iterator = it
            self.link = [None, None]

    def __iter__(self):
        return self

    def __next__(self):
        link = self.link
        if link[1] is None:
            link[0] = next(self.iterator)
            link[1] = [None, None]
        value, self.link = link
        return value
```

​	When the input *iterable* is already a tee iterator object, all members of the return tuple are constructed as if they had been produced by the upstream [`tee()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.tee) call. This "flattening step" allows nested [`tee()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.tee) calls to share the same underlying data chain and to have a single update step rather than a chain of calls.

​	The flattening property makes tee iterators efficiently peekable:

```
def lookahead(tee_iterator):
     "Return the next value without moving the input forward"
     [forked_iterator] = tee(tee_iterator, 1)
     return next(forked_iterator)
```

\>>>

```
>>> iterator = iter('abcdef')
>>> [iterator] = tee(iterator, 1)   # Make the input peekable
>>> next(iterator)                  # Move the iterator forward
'a'
>>> lookahead(iterator)             # Check next value
'b'
>>> next(iterator)                  # Continue moving forward
'b'
```

`tee` 迭代器不是线程安全的。 当同时使用由同一个 [`tee()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.tee) 调用所返回的迭代器时可能引发 [`RuntimeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#RuntimeError)，即使原本的 *iterable* 是线程安全的。is threadsafe.

​	该迭代工具可能需要相当大的辅助存储空间（这取决于要保存多少临时数据）。通常，如果一个迭代器在另一个迭代器开始之前就要使用大部份或全部数据，使用 [`list()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#list) 会比 [`tee()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.tee) 更快。

## itertools.**zip_longest**(**iterables*, *fillvalue=None*)

​	创建一个迭代器，它聚合了来自 *iterables* 中每一项的对应元素。

​	如果 iterables 中每一项的长度不同，则缺失的值将以 *fillvalue* 填充。 如果未指定，则 *fillvalue* 默认为 `None`。

​	迭代将持续进行直至其中最长的可迭代对象被耗尽。

​	大致相当于：

```
def zip_longest(*iterables, fillvalue=None):
    # zip_longest('ABCD', 'xy', fillvalue='-') → Ax By C- D-

    iterators = list(map(iter, iterables))
    num_active = len(iterators)
    if not num_active:
        return

    while True:
        values = []
        for i, iterator in enumerate(iterators):
            try:
                value = next(iterator)
            except StopIteration:
                num_active -= 1
                if not num_active:
                    return
                iterators[i] = repeat(fillvalue)
                value = fillvalue
            values.append(value)
        yield tuple(values)
```

​	如果 iterables 中的每一项可能有无限长度，则 [`zip_longest()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.zip_longest) 函数应当用限制调用次数的代码进行包装（例如 [`islice()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.islice) 或 [`takewhile()`](https://docs.python.org/zh-cn/3.13/library/itertools.html#itertools.takewhile) 等）。



## itertools 配方

​	本节将展示如何使用现有的 itertools 作为基础构件来创建扩展的工具集。

​	这些 itertools 专题的主要目的是教学。 各个专题显示了对单个工具的各种思维方式 — 例如，`chain.from_iterable` 被关联到展平的概念。 这些专题还给出了有关这些工具的组合方式的想法 — 例如，`starmap()` 和 `repeat()` 应当如何一起工作。 这些专题还显示了 itertools 与 [`operator`](https://docs.python.org/zh-cn/3.13/library/operator.html#module-operator) 和 [`collections`](https://docs.python.org/zh-cn/3.13/library/collections.html#module-collections) 模块以及内置迭代工具如 `map()`, `filter()`, `reversed()` 和 `enumerate()` 相互配合的使用模式。

​	这些例程的次要目的是作为一个孵化器使用。 `accumulate()`, `compress()` 和 `pairwise()` 等迭代工具最初就是作为例程引入的。 目前，`sliding_window()`, `iter_index()` 和 `sieve()` 例程正在被测试以确定它们是否堪当大任。

​	基本上所有这些配方和许许多多其他配方都可以通过 Python Package Index 上的 [more-itertools](https://pypi.org/project/more-itertools/) 项目来安装:

```
python -m pip install more-itertools
```

​	许多例程提供了与底层工具集相当的高性能。 更好的内存效率是通过每次只处理一个元素而不是将整个可迭代对象放入内存来保证的。 代码量的精简是通过以 [函数式风格](https://www.cs.kent.ac.uk/people/staff/dat/miranda/whyfp90.pdf) 来链接工具来实现的。 运行的早速度是通过选择使用“矢量化”构件来取代会导致较大解释器开销的 for 循环和 [生成器](https://docs.python.org/zh-cn/3.13/glossary.html#term-generator) 来达成的。

```
import collections
import contextlib
import functools
import math
import operator
import random

def take(n, iterable):
    "Return first n items of the iterable as a list."
    return list(islice(iterable, n))

def prepend(value, iterable):
    "Prepend a single value in front of an iterable."
    # prepend(1, [2, 3, 4]) → 1 2 3 4
    return chain([value], iterable)

def tabulate(function, start=0):
    "Return function(0), function(1), ..."
    return map(function, count(start))

def repeatfunc(func, times=None, *args):
    "Repeat calls to func with specified arguments."
    if times is None:
        return starmap(func, repeat(args))
    return starmap(func, repeat(args, times))

def flatten(list_of_lists):
    "Flatten one level of nesting."
    return chain.from_iterable(list_of_lists)

def ncycles(iterable, n):
    "Returns the sequence elements n times."
    return chain.from_iterable(repeat(tuple(iterable), n))

def tail(n, iterable):
    "Return an iterator over the last n items."
    # tail(3, 'ABCDEFG') → E F G
    return iter(collections.deque(iterable, maxlen=n))

def consume(iterator, n=None):
    "Advance the iterator n-steps ahead. If n is None, consume entirely."
    # Use functions that consume iterators at C speed.
    if n is None:
        collections.deque(iterator, maxlen=0)
    else:
        next(islice(iterator, n, n), None)

def nth(iterable, n, default=None):
    "Returns the nth item or a default value."
    return next(islice(iterable, n, None), default)

def quantify(iterable, predicate=bool):
    "Given a predicate that returns True or False, count the True results."
    return sum(map(predicate, iterable))

def first_true(iterable, default=False, predicate=None):
    "Returns the first true value or the *default* if there is no true value."
    # first_true([a,b,c], x) → a or b or c or x
    # first_true([a,b], x, f) → a if f(a) else b if f(b) else x
    return next(filter(predicate, iterable), default)

def all_equal(iterable, key=None):
    "Returns True if all the elements are equal to each other."
    # all_equal('4٤௪౪໔', key=int) → True
    return len(take(2, groupby(iterable, key))) <= 1

def unique_justseen(iterable, key=None):
    "Yield unique elements, preserving order. Remember only the element just seen."
    # unique_justseen('AAAABBBCCDAABBB') → A B C D A B
    # unique_justseen('ABBcCAD', str.casefold) → A B c A D
    if key is None:
        return map(operator.itemgetter(0), groupby(iterable))
    return map(next, map(operator.itemgetter(1), groupby(iterable, key)))

def unique_everseen(iterable, key=None):
    "Yield unique elements, preserving order. Remember all elements ever seen."
    # unique_everseen('AAAABBBCCDAABBB') → A B C D
    # unique_everseen('ABBcCAD', str.casefold) → A B c D
    seen = set()
    if key is None:
        for element in filterfalse(seen.__contains__, iterable):
            seen.add(element)
            yield element
    else:
        for element in iterable:
            k = key(element)
            if k not in seen:
                seen.add(k)
                yield element

def unique(iterable, key=None, reverse=False):
   "Yield unique elements in sorted order. Supports unhashable inputs."
   # unique([[1, 2], [3, 4], [1, 2]]) → [1, 2] [3, 4]
   return unique_justseen(sorted(iterable, key=key, reverse=reverse), key=key)

def sliding_window(iterable, n):
    "Collect data into overlapping fixed-length chunks or blocks."
    # sliding_window('ABCDEFG', 4) → ABCD BCDE CDEF DEFG
    iterator = iter(iterable)
    window = collections.deque(islice(iterator, n - 1), maxlen=n)
    for x in iterator:
        window.append(x)
        yield tuple(window)

def grouper(iterable, n, *, incomplete='fill', fillvalue=None):
    "Collect data into non-overlapping fixed-length chunks or blocks."
    # grouper('ABCDEFG', 3, fillvalue='x') → ABC DEF Gxx
    # grouper('ABCDEFG', 3, incomplete='strict') → ABC DEF ValueError
    # grouper('ABCDEFG', 3, incomplete='ignore') → ABC DEF
    iterators = [iter(iterable)] * n
    match incomplete:
        case 'fill':
            return zip_longest(*iterators, fillvalue=fillvalue)
        case 'strict':
            return zip(*iterators, strict=True)
        case 'ignore':
            return zip(*iterators)
        case _:
            raise ValueError('Expected fill, strict, or ignore')

def roundrobin(*iterables):
    "Visit input iterables in a cycle until each is exhausted."
    # roundrobin('ABC', 'D', 'EF') → A D E B F C
    # Algorithm credited to George Sakkis
    iterators = map(iter, iterables)
    for num_active in range(len(iterables), 0, -1):
        iterators = cycle(islice(iterators, num_active))
        yield from map(next, iterators)

def subslices(seq):
    "Return all contiguous non-empty subslices of a sequence."
    # subslices('ABCD') → A AB ABC ABCD B BC BCD C CD D
    slices = starmap(slice, combinations(range(len(seq) + 1), 2))
    return map(operator.getitem, repeat(seq), slices)

def iter_index(iterable, value, start=0, stop=None):
    "Return indices where a value occurs in a sequence or iterable."
    # iter_index('AABCADEAF', 'A') → 0 1 4 7
    seq_index = getattr(iterable, 'index', None)
    if seq_index is None:
        iterator = islice(iterable, start, stop)
        for i, element in enumerate(iterator, start):
            if element is value or element == value:
                yield i
    else:
        stop = len(iterable) if stop is None else stop
        i = start
        with contextlib.suppress(ValueError):
            while True:
                yield (i := seq_index(value, i, stop))
                i += 1

def iter_except(func, exception, first=None):
    "Convert a call-until-exception interface to an iterator interface."
    # iter_except(d.popitem, KeyError) → non-blocking dictionary iterator
    with contextlib.suppress(exception):
        if first is not None:
            yield first()
        while True:
            yield func()
```

​	下面的例程具有更数学化的风格:

```
def powerset(iterable):
    "powerset([1,2,3]) → () (1,) (2,) (3,) (1,2) (1,3) (2,3) (1,2,3)"
    s = list(iterable)
    return chain.from_iterable(combinations(s, r) for r in range(len(s)+1))

def sum_of_squares(iterable):
    "Add up the squares of the input values."
    # sum_of_squares([10, 20, 30]) → 1400
    return math.sumprod(*tee(iterable))

def reshape(matrix, cols):
    "Reshape a 2-D matrix to have a given number of columns."
    # reshape([(0, 1), (2, 3), (4, 5)], 3) →  (0, 1, 2), (3, 4, 5)
    return batched(chain.from_iterable(matrix), cols, strict=True)

def transpose(matrix):
    "Swap the rows and columns of a 2-D matrix."
    # transpose([(1, 2, 3), (11, 22, 33)]) → (1, 11) (2, 22) (3, 33)
    return zip(*matrix, strict=True)

def matmul(m1, m2):
    "Multiply two matrices."
    # matmul([(7, 5), (3, 5)], [(2, 5), (7, 9)]) → (49, 80), (41, 60)
    n = len(m2[0])
    return batched(starmap(math.sumprod, product(m1, transpose(m2))), n)

def convolve(signal, kernel):
    """Discrete linear convolution of two iterables.
    Equivalent to polynomial multiplication.

    Convolutions are mathematically commutative; however, the inputs are
    evaluated differently.  The signal is consumed lazily and can be
    infinite. The kernel is fully consumed before the calculations begin.

    Article:  https://betterexplained.com/articles/intuitive-convolution/
    Video:    https://www.youtube.com/watch?v=KuXjwB4LzSA
    """
    # convolve([1, -1, -20], [1, -3]) → 1 -4 -17 60
    # convolve(data, [0.25, 0.25, 0.25, 0.25]) → Moving average (blur)
    # convolve(data, [1/2, 0, -1/2]) → 1st derivative estimate
    # convolve(data, [1, -2, 1]) → 2nd derivative estimate
    kernel = tuple(kernel)[::-1]
    n = len(kernel)
    padded_signal = chain(repeat(0, n-1), signal, repeat(0, n-1))
    windowed_signal = sliding_window(padded_signal, n)
    return map(math.sumprod, repeat(kernel), windowed_signal)

def polynomial_from_roots(roots):
    """Compute a polynomial's coefficients from its roots.

       (x - 5) (x + 4) (x - 3)  expands to:   x³ -4x² -17x + 60
    """
    # polynomial_from_roots([5, -4, 3]) → [1, -4, -17, 60]
    factors = zip(repeat(1), map(operator.neg, roots))
    return list(functools.reduce(convolve, factors, [1]))

def polynomial_eval(coefficients, x):
    """Evaluate a polynomial at a specific value.

    Computes with better numeric stability than Horner's method.
    """
    # Evaluate x³ -4x² -17x + 60 at x = 5
    # polynomial_eval([1, -4, -17, 60], x=5) → 0
    n = len(coefficients)
    if not n:
        return type(x)(0)
    powers = map(pow, repeat(x), reversed(range(n)))
    return math.sumprod(coefficients, powers)

def polynomial_derivative(coefficients):
    """Compute the first derivative of a polynomial.

       f(x)  =  x³ -4x² -17x + 60
       f'(x) = 3x² -8x  -17
    """
    # polynomial_derivative([1, -4, -17, 60]) → [3, -8, -17]
    n = len(coefficients)
    powers = reversed(range(1, n))
    return list(map(operator.mul, coefficients, powers))

def sieve(n):
    "Primes less than n."
    # sieve(30) → 2 3 5 7 11 13 17 19 23 29
    if n > 2:
        yield 2
    data = bytearray((0, 1)) * (n // 2)
    for p in iter_index(data, 1, start=3, stop=math.isqrt(n) + 1):
        data[p*p : n : p+p] = bytes(len(range(p*p, n, p+p)))
    yield from iter_index(data, 1, start=3)

def factor(n):
    "Prime factors of n."
    # factor(99) → 3 3 11
    # factor(1_000_000_000_000_007) → 47 59 360620266859
    # factor(1_000_000_000_000_403) → 1000000000000403
    for prime in sieve(math.isqrt(n) + 1):
        while not n % prime:
            yield prime
            n //= prime
            if n == 1:
                return
    if n > 1:
        yield n

def totient(n):
    "Count of natural numbers up to n that are coprime to n."
    # https://mathworld.wolfram.com/TotientFunction.html
    # totient(12) → 4 because len([1, 5, 7, 11]) == 4
    for prime in set(factor(n)):
        n -= n // prime
    return n
```
