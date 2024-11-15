+++
title = "difflib --- 计算差异的辅助工具"
date = 2024-11-15T11:04:21+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/difflib.html](https://docs.python.org/zh-cn/3.13/library/difflib.html)
>
> 收录该文档的时间：`2024-11-15T11:04:21+08:00`

# `difflib` --- 计算差异的辅助工具

**源代码:** [Lib/difflib.py](https://github.com/python/cpython/tree/3.13/Lib/difflib.py)

------

​	此模块提供用于比较序列的类和函数。 例如，它可被用于比较文件，并可产生多种格式的不同文件差异信息，包括 HTML 和上下文以及统一的 diff 数据。 有关比较目录和文件，另请参阅 [`filecmp`](https://docs.python.org/zh-cn/3.13/library/filecmp.html#module-filecmp) 模块。

## *class* difflib.**SequenceMatcher**

​	这是一个灵活的类，可用于比较任何类型的序列对，只要序列元素为 [hashable](https://docs.python.org/zh-cn/3.13/glossary.html#term-hashable) 对象。 其基本算法要早于由 Ratcliff 和 Obershelp 于 1980 年代末期发表并以“格式塔模式匹配”的夸张名称命名的算法，并且更加有趣一些。 其思路是找到不包含“垃圾”元素的最长连续匹配子序列；所谓“垃圾”元素是指其在某种意义上没有价值，例如空白行或空白符。 （处理垃圾元素是对 Ratcliff 和 Obershelp 算法的一个扩展。） 然后同样的思路将递归地应用于匹配序列的左右序列片段。 这并不能产生最小编辑序列，但确实能产生在人们看来“正确”的匹配。

**耗时:** 基本 Ratcliff-Obershelp 算法在最坏情况下为立方时间而在一般情况下为平方时间。 [`SequenceMatcher`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher) 在最坏情况下为平方时间而在一般情况下的行为受到序列中有多少相同元素这一因素的微妙影响；在最佳情况下则为线性时间。

**自动垃圾启发式计算:** [`SequenceMatcher`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher) 支持使用启发式计算来自动将特定序列项视为垃圾。 这种启发式计算会统计每个单独项在序列中出现的次数。 如果某一项（在第一项之后）的重复次数超过序列长度的 1% 并且序列长度至少有 200 项，该项会被标记为“热门”并被视为序列匹配中的垃圾。 这种启发式计算可以通过在创建 [`SequenceMatcher`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher) 时将 `autojunk` 参数设为 `False` 来关闭。

*在 3.2 版本发生变更:* 增加了 *autojunk* 形参。

## *class* difflib.**Differ**

​	这个类的作用是比较由文本行组成的序列，并产生可供人阅读的差异或增量信息。 Differ 统一使用 [`SequenceMatcher`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher) 来完成行序列的比较以及相似（接近匹配）行内部字符序列的比较。

[`Differ`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.Differ) 增量的每一行均以双字母代码打头：

| ​	双字母代码 | ​	含意                   |
| :------------------------- | :------------------------------------- |
| `'- '`                     | ​	行为序列 1 所独有      |
| `'+ '`                     | ​	行为序列 2 所独有      |
| `' '`                      | ​	行在两序列中相同       |
| `'? '`                     | ​	行不存在于任一输入序列 |

​	以 '`?`' 打头的行尝试将视线紖至行以外而不存在于任一输入序列的差异。 如果序列包含空白符，例如空格、制表或换行则这些行可能会令人感到迷惑。

## *class* difflib.**HtmlDiff**

​	这个类可用于创建 HTML 表格（或包含表格的完整 HTML 文件）以并排地逐行显示文本比较，行间与行外的更改将突出显示。 此表格可以基于完全或上下文差异模式来生成。

​	这个类的构造函数：

## **__init__**(*tabsize=8*, *wrapcolumn=None*, *linejunk=None*, *charjunk=IS_CHARACTER_JUNK*)

​	初始化 [`HtmlDiff`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.HtmlDiff) 的实例。

*tabsize* 是一个可选关键字参数，指定制表位的间隔，默认值为 `8`。

*wrapcolumn* 是一个可选关键字参数，指定行文本自动打断并换行的列位置，默认值为 `None` 表示不自动换行。

*linejunk* 和 *charjunk* 均是可选关键字参数，会传入 [`ndiff()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.ndiff) (被 [`HtmlDiff`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.HtmlDiff) 用来生成并排显示的 HTML 差异)。 请参阅 [`ndiff()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.ndiff) 文档了解参数默认值及其说明。

​	下列是公开的方法

## **make_file**(*fromlines*, *tolines*, *fromdesc=''*, *todesc=''*, *context=False*, *numlines=5*, ***, *charset='utf-8'*)

​	比较 *fromlines* 和 *tolines* (字符串列表) 并返回一个字符串，表示一个完整 HTML 文件，其中包含各行差异的表格，行间与行外的更改将突出显示。

*fromdesc* 和 *todesc* 均是可选关键字参数，指定来源/目标文件的列标题字符串（默认均为空白字符串）。

*context* 和 *numlines* 均是可选关键字参数。 当只要显示上下文差异时就将 *context* 设为 `True`，否则默认值 `False` 为显示完整文件。 *numlines* 默认为 `5`。 当 *context* 为 `True` 时 *numlines* 将控制围绕突出显示差异部分的上下文行数。 当 *context* 为 `False` 时 *numlines* 将控制在使用 "next" 超链接时突出显示差异部分之前所显示的行数（设为零则会导致 "next" 超链接将下一个突出显示差异部分放在浏览器顶端，不添加任何前导上下文）。

​	备注

 

*fromdesc* 和 *todesc* 会被当作未转义的 HTML 来解读，当接收不可信来源的输入时应该适当地进行转义。

*在 3.5 版本发生变更:* 增加了 *charset* 关键字参数。 HTML 文档的默认字符集从 `'ISO-8859-1'` 更改为 `'utf-8'`。

## **make_table**(*fromlines*, *tolines*, *fromdesc=''*, *todesc=''*, *context=False*, *numlines=5*)

​	比较 *fromlines* 和 *tolines* (字符串列表) 并返回一个字符串，表示一个包含各行差异的完整 HTML 表格，行间与行外的更改将突出显示。

​	此方法的参数与 [`make_file()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.HtmlDiff.make_file) 方法的相同。

## difflib.**context_diff**(*a*, *b*, *fromfile=''*, *tofile=''*, *fromfiledate=''*, *tofiledate=''*, *n=3*, *lineterm='\n'*)

​	比较 *a* 和 *b* (字符串列表)；返回上下文差异格式的增量信息 (一个产生增量行的 [generator](https://docs.python.org/zh-cn/3.13/glossary.html#term-generator))。

​	所谓上下文差异是一种只显示有更改的行再加几个上下文行的紧凑形式。 更改被显示为之前/之后的样式。 上下文行数由 *n* 设定，默认为三行。

​	默认情况下，差异控制行（以 `***` or `---` 表示）是通过末尾换行符来创建的。 这样做的好处是从 [`io.IOBase.readlines()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.readlines) 创建的输入将得到适用于 [`io.IOBase.writelines()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.writelines) 的差异信息，因为输入和输出都带有末尾换行符。

​	对于没有末尾换行符的输入，应将 *lineterm* 参数设为 `""`，这样输出内容将统一不带换行符。

​	上下文差异格式通常带有一个记录文件名和修改时间的标头。 这些信息的部分或全部可以使用字符串 *fromfile*, *tofile*, *fromfiledate* 和 *tofiledate* 来指定。 修改时间通常以 ISO 8601 格式表示。 如果未指定，这些字符串默认为空。



```
>>> import sys
>>> from difflib import *
>>> s1 = ['bacon\n', 'eggs\n', 'ham\n', 'guido\n']
>>> s2 = ['python\n', 'eggy\n', 'hamster\n', 'guido\n']
>>> sys.stdout.writelines(context_diff(s1, s2, fromfile='before.py',
...                        tofile='after.py'))
*** before.py
--- after.py
***************
*** 1,4 ****
! bacon
! eggs
! ham
  guido
--- 1,4 ----
! python
! eggy
! hamster
  guido
```

​	请参阅 [difflib 的命令行接口](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib-interface) 获取更详细的示例。

## difflib.**get_close_matches**(*word*, *possibilities*, *n=3*, *cutoff=0.6*)

​	返回由最佳“近似”匹配构成的列表。 *word* 为一个指定目标近似匹配的序列（通常为字符串），*possibilities* 为一个由用于匹配 *word* 的序列构成的列表（通常为字符串列表）。

​	可选参数 *n* (默认为 `3`) 指定最多返回多少个近似匹配； *n* 必须大于 `0`.

​	可选参数 *cutoff* (默认为 `0.6`) 是一个 [0, 1] 范围内的浮点数。 与 *word* 相似度得分未达到该值的候选匹配将被忽略。

​	候选匹配中（不超过 *n* 个）的最佳匹配将以列表形式返回，按相似度得分排序，最相似的排在最前面。



```
>>> get_close_matches('appel', ['ape', 'apple', 'peach', 'puppy'])
['apple', 'ape']
>>> import keyword
>>> get_close_matches('wheel', keyword.kwlist)
['while']
>>> get_close_matches('pineapple', keyword.kwlist)
[]
>>> get_close_matches('accept', keyword.kwlist)
['except']
```

## difflib.**ndiff**(*a*, *b*, *linejunk=None*, *charjunk=IS_CHARACTER_JUNK*)

​	比较 *a* 和 *b* (字符串列表)；返回 [`Differ`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.Differ) 形式的增量信息 (一个产生增量行的 [generator](https://docs.python.org/zh-cn/3.13/glossary.html#term-generator))。

​	可选关键字形参 *linejunk* 和 *charjunk* 均为过滤函数 (或为 `None`)：

*linejunk*: 此函数接受单个字符串参数，如果其为垃圾字符串则返回真值，否则返回假值。 默认为 `None`。 此外还有一个模块层级的函数 [`IS_LINE_JUNK()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.IS_LINE_JUNK)，它会过滤掉没有可见字符的行，除非该行添加了至多一个井号符 (`'#'`) -- 但是下层的 [`SequenceMatcher`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher) 类会动态分析哪些行的重复频繁到足以形成噪音，这通常会比使用此函数的效果更好。

*charjunk*: 此函数接受一个字符（长度为 1 的字符串)，如果其为垃圾字符则返回真值，否则返回假值。 默认为模块层级的函数 [`IS_CHARACTER_JUNK()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.IS_CHARACTER_JUNK)，它会过滤掉空白字符（空格符或制表符；但包含换行符可不是个好主意！）。



```
>>> diff = ndiff('one\ntwo\nthree\n'.splitlines(keepends=True),
...              'ore\ntree\nemu\n'.splitlines(keepends=True))
>>> print(''.join(diff), end="")
- one
?  ^
+ ore
?  ^
- two
- three
?  -
+ tree
+ emu
```

## difflib.**restore**(*sequence*, *which*)

​	返回两个序列中产生增量的那一个。

​	给出一个由 [`Differ.compare()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.Differ.compare) 或 [`ndiff()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.ndiff) 产生的 *序列*，提取出来自文件 1 或 2 (*which* 形参) 的行，去除行前缀。

​	示例:



```
>>> diff = ndiff('one\ntwo\nthree\n'.splitlines(keepends=True),
...              'ore\ntree\nemu\n'.splitlines(keepends=True))
>>> diff = list(diff) # materialize the generated delta into a list
>>> print(''.join(restore(diff, 1)), end="")
one
two
three
>>> print(''.join(restore(diff, 2)), end="")
ore
tree
emu
```

## difflib.**unified_diff**(*a*, *b*, *fromfile=''*, *tofile=''*, *fromfiledate=''*, *tofiledate=''*, *n=3*, *lineterm='\n'*)

​	比较 *a* 和 *b* (字符串列表)；返回统一差异格式的增量信息 (一个产生增量行的 [generator](https://docs.python.org/zh-cn/3.13/glossary.html#term-generator))。

​	所以统一差异是一种只显示有更改的行再加几个上下文行的紧凑形式。 更改被显示为内联的样式（而不是分开的之前/之后文本块）。 上下文行数由 *n* 设定，默认为三行。

​	默认情况下，差异控制行 (以 `---`, `+++` 或 `@@` 表示) 是通过末尾换行符来创建的。 这样做的好处是从 [`io.IOBase.readlines()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.readlines) 创建的输入将得到适用于 [`io.IOBase.writelines()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.writelines) 的差异信息，因为输入和输出都带有末尾换行符。

​	对于没有末尾换行符的输入，应将 *lineterm* 参数设为 `""`，这样输出内容将统一不带换行符。

​	统一的差异格式通常带有一个记录文件名和修改时间的标头。 这些信息的部分或全部可以使用字符串 *fromfile*, *tofile*, *fromfiledate* 和 *tofiledate* 来指定。 修改时间通常以 ISO 8601 格式表示。 如果未指定，这些字符串将默认为空。



```
>>> s1 = ['bacon\n', 'eggs\n', 'ham\n', 'guido\n']
>>> s2 = ['python\n', 'eggy\n', 'hamster\n', 'guido\n']
>>> sys.stdout.writelines(unified_diff(s1, s2, fromfile='before.py', tofile='after.py'))
--- before.py
+++ after.py
@@ -1,4 +1,4 @@
-bacon
-eggs
-ham
+python
+eggy
+hamster
 guido
```

​	请参阅 [difflib 的命令行接口](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib-interface) 获取更详细的示例。

## difflib.**diff_bytes**(*dfunc*, *a*, *b*, *fromfile=b''*, *tofile=b''*, *fromfiledate=b''*, *tofiledate=b''*, *n=3*, *lineterm=b'\n'*)

​	使用 *dfunc* 比较 *a* 和 *b* (字节串对象列表)；产生以 *dfunc* 所返回格式表示的差异行列表（也是字节串）。 *dfunc* 必须是可调用对象，通常为 [`unified_diff()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.unified_diff) 或 [`context_diff()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.context_diff)。

​	允许你比较编码未知或不一致的数据。 除 *n* 之外的所有输入都必须为字节串对象而非字符串。 作用方式为无损地将所有输入 (除 *n* 之外) 转换为字符串，并调用 `dfunc(a, b, fromfile, tofile, fromfiledate, tofiledate, n, lineterm)`。 *dfunc* 的输出会被随即转换回字节串，这样你所得到的增量行将具有与 *a* 和 *b* 相同的未知/不一致编码。

*Added in version 3.5.*

## difflib.**IS_LINE_JUNK**(*line*)

​	对于可忽略的行返回 `True`。 如果 *line* 为空行或只包含单个 `'#'` 则 *line* 行就是可忽略的，否则就是不可忽略的。 此函数被用作较旧版本 [`ndiff()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.ndiff) 中 *linejunk* 形参的默认值。

## difflib.**IS_CHARACTER_JUNK**(*ch*)

​	对于可忽略的字符返回 `True`。 字符 *ch* 如果为空格符或制表符则 *ch* 就是可忽略的，否则就是不可忽略的。 此函数被用作 [`ndiff()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.ndiff) 中 *charjunk* 形参的默认值。

​	参见

## [Pattern Matching: The Gestalt Approach](https://www.drdobbs.com/database/pattern-matching-the-gestalt-approach/184407970)

​	John W. Ratcliff 和 D. E. Metzener 对于一种类似算法的讨论。 此文于 1988 年 7 月发表于 [Dr. Dobb's Journal](https://www.drdobbs.com/)。



## SequenceMatcher 对象

[`SequenceMatcher`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher) 类具有这样的构造器：

## *class* difflib.**SequenceMatcher**(*isjunk=None*, *a=''*, *b=''*, *autojunk=True*)

​	可选参数 *isjunk* 必须为 `None` (默认值) 或为接受一个序列元素并当且仅当其为应忽略的“垃圾”元素时返回真值的单参数函数。 传入 `None` 作为 *isjunk* 的值就相当于传入 `lambda x: False`；也就是说不忽略任何值。 例如，传入:

```
lambda x: x in " \t"
```

​	如果你以字符序列的形式对行进行比较，并且不希望区分空格符或硬制表符。

​	可选参数 *a* 和 *b* 为要比较的序列；两者默认为空字符串。 两个序列的元素都必须为 [hashable](https://docs.python.org/zh-cn/3.13/glossary.html#term-hashable)。

​	可选参数 *autojunk* 可用于启用自动垃圾启发式计算。

*在 3.2 版本发生变更:* 增加了 *autojunk* 形参。

​	SequenceMatcher 对象接受三个数据属性: *bjunk* 是 *b* 当中 *isjunk* 为 `True` 的元素集合；*bpopular* 是被启发式计算（如果其未被禁用）视为热门候选的非垃圾元素集合；*b2j* 是将 *b* 当中剩余元素映射到一个它们出现位置列表的字典。 所有三个数据属性将在 *b* 通过 [`set_seqs()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher.set_seqs) 或 [`set_seq2()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher.set_seq2) 重置时被重置。

*Added in version 3.2:* *bjunk* 和 *bpopular* 属性。

[`SequenceMatcher`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher) 对象具有以下方法：

## **set_seqs**(*a*, *b*)

​	设置要比较的两个序列。

[`SequenceMatcher`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher) 计算并缓存有关第二个序列的详细信息，这样如果你想要将一个序列与多个序列进行比较，可使用 [`set_seq2()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher.set_seq2) 一次性地设置该常用序列并重复地对每个其他序列各调用一次 [`set_seq1()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher.set_seq1)。

## **set_seq1**(*a*)

​	设置要比较的第一个序列。 要比较的第二个序列不会改变。

## **set_seq2**(*b*)

​	设置要比较的第二个序列。 要比较的第一个序列不会改变。

## **find_longest_match**(*alo=0*, *ahi=None*, *blo=0*, *bhi=None*)

​	找出 `a[alo:ahi]` 和 `b[blo:bhi]` 中的最长匹配块。

​	如果 *isjunk* 被省略或为 `None`，[`find_longest_match()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher.find_longest_match) 将返回 `(i, j, k)` 使得 `a[i:i+k]` 等于 `b[j:j+k]`，其中 `alo <= i <= i+k <= ahi` 并且 `blo <= j <= j+k <= bhi`。 对于所有满足这些条件的 `(i', j', k')`，如果 `i == i'`, `j <= j'` 也被满足，则附加条件 `k >= k'`, `i <= i'`。 换句话说，对于所有最长匹配块，返回在 *a* 当中最先出现的一个，而对于在 *a* 当中最先出现的所有最长匹配块，则返回在 *b* 当中最先出现的一个。



```
>>> s = SequenceMatcher(None, " abcd", "abcd abcd")
>>> s.find_longest_match(0, 5, 0, 9)
Match(a=0, b=4, size=5)
```

​	如果提供了 *isjunk*，将按上述规则确定第一个最长匹配块，但额外附加不允许块内出现垃圾元素的限制。 然后将通过（仅）匹配两边的垃圾元素来尽可能地扩展该块。 这样结果块绝对不会匹配垃圾元素，除非同样的垃圾元素正好与有意义的匹配相邻。

​	这是与之前相同的例子，但是将空格符视为垃圾。 这将防止 `' abcd'` 直接与第二个序列末尾的 `' abcd'` 相匹配。 而只可以匹配 `'abcd'`，并且是匹配第二个序列最左边的 `'abcd'`：



```
>>> s = SequenceMatcher(lambda x: x==" ", " abcd", "abcd abcd")
>>> s.find_longest_match(0, 5, 0, 9)
Match(a=1, b=0, size=4)
```

​	如果未找到匹配块，此方法将返回 `(alo, blo, 0)`。

​	此方法将返回一个 [named tuple](https://docs.python.org/zh-cn/3.13/glossary.html#term-named-tuple) `Match(a, b, size)`。

*在 3.9 版本发生变更:* 加入默认参数。

## **get_matching_blocks**()

​	返回描述非重叠匹配子序列的三元组列表。 每个三元组的形式为 `(i, j, n)`，其含义为 `a[i:i+n] == b[j:j+n]`。 这些三元组按 *i* 和 *j* 单调递增排列。

​	最后一个三元组用于占位，其值为 `(len(a), len(b), 0)`。 它是唯一 `n == 0` 的三元组。 如果 `(i, j, n)` 和 `(i', j', n')` 是在列表中相邻的三元组，且后者不是列表中的最后一个三元组，则 `i+n < i'` 或 `j+n < j'`；换句话说，相邻的三元组总是描述非相邻的相等块。



```
>>> s = SequenceMatcher(None, "abxcd", "abcd")
>>> s.get_matching_blocks()
[Match(a=0, b=0, size=2), Match(a=3, b=2, size=2), Match(a=5, b=4, size=0)]
```

## **get_opcodes**()

​	返回描述如何将 *a* 变为 *b* 的 5 元组列表，每个元组的形式为 `(tag, i1, i2, j1, j2)`。 在第一个元组中 `i1 == j1 == 0`，而在其余的元组中 *i1* 等于前一个元组的 *i2*，并且 *j1* 也等于前一个元组的 *j2*。

*tag* 值为字符串，其含义如下：

| ​	值 | ​	含意                                         |
| :----------------- | :----------------------------------------------------------- |
| `'replace'`        | `a[i1:i2]` 应由 `b[j1:j2]` 替换。                            |
| `'delete'`         | `a[i1:i2]` 应被删除。 请注意在此情况下 `j1 == j2`。          |
| `'insert'`         | `b[j1:j2]` 应插入到 `a[i1:i1]`。 请注意在此情况下 `i1 == i2`。 |
| `'equal'`          | `a[i1:i2] == b[j1:j2]` (两个子序列相同)。                    |

​	例如：



```
>>> a = "qabxcd"
>>> b = "abycdf"
>>> s = SequenceMatcher(None, a, b)
>>> for tag, i1, i2, j1, j2 in s.get_opcodes():
...     print('{:7}   a[{}:{}] --> b[{}:{}] {!r:>8} --> {!r}'.format(
...         tag, i1, i2, j1, j2, a[i1:i2], b[j1:j2]))
delete    a[0:1] --> b[0:0]      'q' --> ''
equal     a[1:3] --> b[0:2]     'ab' --> 'ab'
replace   a[3:4] --> b[2:3]      'x' --> 'y'
equal     a[4:6] --> b[3:5]     'cd' --> 'cd'
insert    a[6:6] --> b[5:6]       '' --> 'f'
```

## **get_grouped_opcodes**(*n=3*)

​	返回一个带有最多 *n* 行上下文的分组的 [generator](https://docs.python.org/zh-cn/3.13/glossary.html#term-generator)。

​	从 [`get_opcodes()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher.get_opcodes) 所返回的组开始，此方法会拆分出较小的更改簇并消除没有更改的间隔区域。

​	这些分组以与 [`get_opcodes()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher.get_opcodes) 相同的格式返回。

## **ratio**()

​	返回一个取值范围 [0, 1] 的浮点数作为序列相似性度量。

​	其中 T 是两个序列中元素的总数量，M 是匹配的数量，即 2.0*M / T。 请注意如果两个序列完全相同则该值为 `1.0`，如果两者完全不同则为 `0.0`。

​	如果 [`get_matching_blocks()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher.get_matching_blocks) 或 [`get_opcodes()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher.get_opcodes) 尚未被调用则此方法运算消耗较大，在此情况下你可能需要先调用 [`quick_ratio()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher.quick_ratio) 或 [`real_quick_ratio()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher.real_quick_ratio) 来获取一个上界。

​	备注

 

​	注意: [`ratio()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher.ratio) 调用的结果可能会取决于参数的顺序。 例如:



```
>>> SequenceMatcher(None, 'tide', 'diet').ratio()
0.25
>>> SequenceMatcher(None, 'diet', 'tide').ratio()
0.5
```

## **quick_ratio**()

​	相对快速地返回一个 [`ratio()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher.ratio) 的上界。

## **real_quick_ratio**()

​	非常快速地返回一个 [`ratio()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher.ratio) 的上界。

​	这三个返回匹配部分点总字符数之比的三种方法可能由于不同的近似级别而给出不同的结果，但是 [`quick_ratio()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher.quick_ratio) 和 [`real_quick_ratio()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher.real_quick_ratio) 总是会至少与 [`ratio()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher.ratio) 一样大：



```
>>> s = SequenceMatcher(None, "abcd", "bcde")
>>> s.ratio()
0.75
>>> s.quick_ratio()
0.75
>>> s.real_quick_ratio()
1.0
```



## SequenceMatcher 的示例

​	以下示例比较两个字符串，并将空格视为“垃圾”：



```
>>> s = SequenceMatcher(lambda x: x == " ",
...                     "private Thread currentThread;",
...                     "private volatile Thread currentThread;")
```

[`ratio()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher.ratio) 返回一个 [0, 1] 范围内的浮点数，用来衡量序列的相似度。 根据经验，[`ratio()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher.ratio) 值超过 0.6 就意味着两个序列非常接近匹配：



```
>>> print(round(s.ratio(), 3))
0.866
```

​	如果您只对序列的匹配的位置感兴趣，则 [`get_matching_blocks()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher.get_matching_blocks) 就很方便：



```
>>> for block in s.get_matching_blocks():
...     print("a[%d] and b[%d] match for %d elements" % block)
a[0] and b[0] match for 8 elements
a[8] and b[17] match for 21 elements
a[29] and b[38] match for 0 elements
```

​	请注意 [`get_matching_blocks()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher.get_matching_blocks) 返回的最后一个元组 `(len(a), len(b), 0)` 始终只用于占位，这也是元组的末尾元素（匹配的元素个数）为 `0` 的唯一情况。

​	如果你想要知道如何将第一个序列转成第二个序列，可以使用 [`get_opcodes()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher.get_opcodes):



```
>>> for opcode in s.get_opcodes():
...     print("%6s a[%d:%d] b[%d:%d]" % opcode)
 equal a[0:8] b[0:8]
insert a[8:8] b[8:17]
 equal a[8:29] b[17:38]
```

​	参见

- 此模块中的 [`get_close_matches()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.get_close_matches) 函数显示了如何基于 [`SequenceMatcher`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher) 构建简单的代码来执行有用的功能。
- 针对使用 [`SequenceMatcher`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher) 构建的小型应用程序的 [简易版本控制方案](https://code.activestate.com/recipes/576729-simple-version-control/)。



## Differ 对象

​	请注意 [`Differ`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.Differ) 所生成的增量并不保证是 **最小** 差异。 相反，最小差异往往是违反直觉的，因为它们会同步任何可能的地方，有时甚至意外产生相距 100 页的匹配。 将同步点限制为连续匹配保留了一些局部性概念，这偶尔会带来产生更长差异的代价。

[`Differ`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.Differ) 类具有这样的构造器：

## *class* difflib.**Differ**(*linejunk=None*, *charjunk=None*)

​	可选关键字形参 *linejunk* 和 *charjunk* 均为过滤函数 (或为 `None`)：

*linejunk*: 接受单个字符串作为参数的函数，如果其为垃圾字符串则返回真值。 默认值为 `None`，意味着没有任何行会被视为垃圾行。

*charjunk*: 接受单个字符（长度为 1 的字符串）作为参数的函数，如果其为垃圾字符则返回真值。 默认值为 `None`，意味着没有任何字符会被视为垃圾字符。

​	这些垃圾过滤函数可加快查找差异的匹配速度，并且不会导致任何差异行或字符被忽略。 请阅读 [`find_longest_match()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.SequenceMatcher.find_longest_match) 方法的 *isjunk* 形参的描述了解详情。

[`Differ`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.Differ) 对象是通过一个单独方法来使用（生成增量）的：

## **compare**(*a*, *b*)

​	比较两个由行组成的序列，并生成增量（一个由行组成的序列）。

​	每个序列必须包含一个以换行符结尾的单行字符串。 这样的序列可以通过文件型对象的 [`readlines()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.readlines) 方法来获取。 所生成的增量同样由以换行符结尾的字符串构成，可以通过文件型对象的 [`writelines()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.writelines) 方法原样打印出来。



## Differ 示例

​	此示例比较两段文本。 首先我们设置文本为以换行符结尾的单行字符串组成的序列（这样的序列也可以通过文件型对象的 [`readlines()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.readlines) 方法来获取）：



```
>>> text1 = '''  1. Beautiful is better than ugly.
...   2. Explicit is better than implicit.
...   3. Simple is better than complex.
...   4. Complex is better than complicated.
... '''.splitlines(keepends=True)
>>> len(text1)
4
>>> text1[0][-1]
'\n'
>>> text2 = '''  1. Beautiful is better than ugly.
...   3.   Simple is better than complex.
...   4. Complicated is better than complex.
...   5. Flat is better than nested.
... '''.splitlines(keepends=True)
```

​	接下来我们实例化一个 Differ 对象：



```
>>> d = Differ()
```

​	请注意在实例化 [`Differ`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.Differ) 对象时我们可以传入函数来过滤掉“垃圾”行和字符。 详情参见 [`Differ()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.Differ) 构造器说明。

​	最后，我们比较两个序列：



```
>>> result = list(d.compare(text1, text2))
```

`result` 是一个字符串列表，让我们将其美化打印出来：



```
>>> from pprint import pprint
>>> pprint(result)
['    1. Beautiful is better than ugly.\n',
 '-   2. Explicit is better than implicit.\n',
 '-   3. Simple is better than complex.\n',
 '+   3.   Simple is better than complex.\n',
 '?     ++\n',
 '-   4. Complex is better than complicated.\n',
 '?            ^                     ---- ^\n',
 '+   4. Complicated is better than complex.\n',
 '?           ++++ ^                      ^\n',
 '+   5. Flat is better than nested.\n']
```

​	作为单独的多行字符串显示出来则是这样：



```
>>> import sys
>>> sys.stdout.writelines(result)
    1. Beautiful is better than ugly.
-   2. Explicit is better than implicit.
-   3. Simple is better than complex.
+   3.   Simple is better than complex.
?     ++
-   4. Complex is better than complicated.
?            ^                     ---- ^
+   4. Complicated is better than complex.
?           ++++ ^                      ^
+   5. Flat is better than nested.
```



## difflib 的命令行接口

​	这个例子演示了如何使用 difflib 来创建类似 `diff` 的工具。

```
""" difflib.py 的命令行接口，提供四种格式的 diff：

* ndiff:    列出每一行并高亮各行间的变化。
* context:  以前/后格式高亮变化内容集。
* unified:  以内联格式高亮变化内容集。
* html:     生成高亮变化内容的并排比较。

"""

import sys, os, difflib, argparse
from datetime import datetime, timezone

def file_mtime(path):
    t = datetime.fromtimestamp(os.stat(path).st_mtime,
                               timezone.utc)
    return t.astimezone().isoformat()

def main():

    parser = argparse.ArgumentParser()
    parser.add_argument('-c', action='store_true', default=False,
                        help='Produce a context format diff (default)')
    parser.add_argument('-u', action='store_true', default=False,
                        help='Produce a unified format diff')
    parser.add_argument('-m', action='store_true', default=False,
                        help='Produce HTML side by side diff '
                             '(can use -c and -l in conjunction)')
    parser.add_argument('-n', action='store_true', default=False,
                        help='Produce a ndiff format diff')
    parser.add_argument('-l', '--lines', type=int, default=3,
                        help='Set number of context lines (default 3)')
    parser.add_argument('fromfile')
    parser.add_argument('tofile')
    options = parser.parse_args()

    n = options.lines
    fromfile = options.fromfile
    tofile = options.tofile

    fromdate = file_mtime(fromfile)
    todate = file_mtime(tofile)
    with open(fromfile) as ff:
        fromlines = ff.readlines()
    with open(tofile) as tf:
        tolines = tf.readlines()

    if options.u:
        diff = difflib.unified_diff(fromlines, tolines, fromfile, tofile, fromdate, todate, n=n)
    elif options.n:
        diff = difflib.ndiff(fromlines, tolines)
    elif options.m:
        diff = difflib.HtmlDiff().make_file(fromlines,tolines,fromfile,tofile,context=options.c,numlines=n)
    else:
        diff = difflib.context_diff(fromlines, tolines, fromfile, tofile, fromdate, todate, n=n)

    sys.stdout.writelines(diff)

if __name__ == '__main__':
    main()
```

## ndiff 示例

​	这个例子演示了如何使用 [`difflib.ndiff()`](https://docs.python.org/zh-cn/3.13/library/difflib.html#difflib.ndiff)。

```
"""ndiff [-q] file1 file2
    或
ndiff (-r1 | -r2) < ndiff_output > file1_or_file2

打印对人类友好的文件差异报告至标准输出。 将会标明
行间与行内差异。 在第二种形式下，会根据标准输入上的
ndiff 报告在标准输出上重建 file1 (-r1) 或 file2 (-r2)。

在第一种形式下，如果未指明 -q ("quiet")，则输出的
前两行为

-: file1
+: file2

其余的每一行将以两个字符的代码打头：

    "- "    行不同于 file1
    "+ "    行不同于 file2
    "  "    行在两个文件中相同
    "? "    行不存在于某一个输入文件

以 "? " 打头的行会尝试关注行内差异，并且
不存在于某一个输入文件中。 当源文件包含
制表符时这些行可能会令人迷惑。

第一个文件可通过只保留以" " 或 "- " 打头的行，
并删除那些 2 字符前缀来恢复；使用 ndiff 时传入 -r1。

第二个文件可通过类似方式来恢复，即只保留
" " 和 "+ " 打头的行；使用 ndiff 并传入 -r2；或者
在 Unix 上，第二个文件可通过管道操作来恢复

    sed -n '/^[+ ] /s/^..//p'
"""

__version__ = 1, 7, 0

import difflib, sys

def fail(msg):
    out = sys.stderr.write
    out(msg + "\n\n")
    out(__doc__)
    return 0

# 打开一个文件并返回文件对象；如无法打开文件
# 则返回 0
def fopen(fname):
    try:
        return open(fname)
    except IOError as detail:
        return fail("couldn't open " + fname + ": " + str(detail))

# 打开两个文件并报告差异至标准输出；如有问题则返回假值
def fcompare(f1name, f2name):
    f1 = fopen(f1name)
    f2 = fopen(f2name)
    if not f1 or not f2:
        return 0

    a = f1.readlines(); f1.close()
    b = f2.readlines(); f2.close()
    for line in difflib.ndiff(a, b):
        print(line, end=' ')

    return 1

# 解析参数 (sys.argv[1:] 正常) 并进行比较；
# 如有问题则返回假值

def main(args):
    import getopt
    try:
        opts, args = getopt.getopt(args, "qr:")
    except getopt.error as detail:
        return fail(str(detail))
    noisy = 1
    qseen = rseen = 0
    for opt, val in opts:
        if opt == "-q":
            qseen = 1
            noisy = 0
        elif opt == "-r":
            rseen = 1
            whichfile = val
    if qseen and rseen:
        return fail("can't specify both -q and -r")
    if rseen:
        if args:
            return fail("no args allowed with -r option")
        if whichfile in ("1", "2"):
            restore(whichfile)
            return 1
        return fail("-r value must be 1 or 2")
    if len(args) != 2:
        return fail("need 2 filename args")
    f1name, f2name = args
    if noisy:
        print('-:', f1name)
        print('+:', f2name)
    return fcompare(f1name, f2name)

# 从标准输入读取 ndiff 输出，并打印 file1 (which=='1') 或
# file2 (which=='2') 到标准输出

def restore(which):
    restored = difflib.restore(sys.stdin.readlines(), which)
    sys.stdout.writelines(restored)

if __name__ == '__main__':
    main(sys.argv[1:])
```
