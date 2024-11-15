+++
title = "filecmp --- 文件和目录比较"
date = 2024-11-15T11:53:09+08:00
weight = 40
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/filecmp.html](https://docs.python.org/zh-cn/3.13/library/filecmp.html)
>
> 收录该文档的时间：`2024-11-15T11:53:09+08:00`

# `filecmp` --- 文件和目录比较

**源代码:** [Lib/filecmp.py](https://github.com/python/cpython/tree/3.13/Lib/filecmp.py)

------

[`filecmp`](https://docs.python.org/zh-cn/3.13/library/filecmp.html#module-filecmp) 模块定义了用于比较文件及目录的函数，并且可以选取多种关于时间和准确性的折衷方案。对于文件的比较，另见 [`difflib`](https://docs.python.org/zh-cn/3.13/library/difflib.html#module-difflib) 模块。

[`filecmp`](https://docs.python.org/zh-cn/3.13/library/filecmp.html#module-filecmp) 模块定义了如下函数：

## filecmp.**cmp**(*f1*, *f2*, *shallow=True*)

​	比较名为 *f1* 和 *f2* 的文件，如果它们似乎相等则返回 `True` ，否则返回 `False` 。

​	如果 *shallow* 为真值且两个文件的 [`os.stat()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.stat) 签名信息（文件类型、大小和修改时间）一致，则文件会被视为相同。

​	在其他情况下，如果文件大小或内容不同则它们会被视为不同。

​	需要注意，没有外部程序被该函数调用，这赋予了该函数可移植性与效率。

​	该函数会缓存过去的比较及其结果，且在文件的 [`os.stat()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.stat) 信息变化后缓存条目失效。所有的缓存可以通过使用 [`clear_cache()`](https://docs.python.org/zh-cn/3.13/library/filecmp.html#filecmp.clear_cache) 来清除。

## filecmp.**cmpfiles**(*dir1*, *dir2*, *common*, *shallow=True*)

​	比较在两个目录 *dir1* 和 *dir2* 中，由 *common* 所确定名称的文件。

​	返回三组文件名列表： *match*, *mismatch*, *errors* 。 *match* 含有相匹配的文件， *mismatch* 含有那些不匹配的，然后 *errors* 列出那些未被比较文件的名称。如果文件不存在于两目录中的任一个，或者用户缺少读取它们的权限，又或者因为其他的一些原因而无法比较，那么这些文件将会被列在 *errors* 中。

​	参数 *shallow* 具有同 [`filecmp.cmp()`](https://docs.python.org/zh-cn/3.13/library/filecmp.html#filecmp.cmp) 一致的含义与默认值。

​	例如， `cmpfiles('a', 'b', ['c', 'd/e'])` 将会比较 `a/c` 与 `b/c` 以及 `a/d/e` 与 `b/d/e` 。 `'c'` 和 `'d/e'` 将会各自出现在返回的三个列表里的某一个列表中。

## filecmp.**clear_cache**()

​	清除 filecmp 缓存。如果一个文件过快地修改，以至于超过底层文件系统记录修改时间的精度，那么该函数可能有助于比较该类文件。

*Added in version 3.4.*



## [`dircmp`](https://docs.python.org/zh-cn/3.13/library/filecmp.html#filecmp.dircmp) 类

## *class* filecmp.**dircmp**(*a*, *b*, *ignore=None*, *hide=None*, ***, *shallow=True*)

​	构造一个新的目录比较对象，用来比较目录 *a* 和 *b*。 *ignore* 是要忽略的名称列表，且默认为 [`filecmp.DEFAULT_IGNORES`](https://docs.python.org/zh-cn/3.13/library/filecmp.html#filecmp.DEFAULT_IGNORES)。 *hide* 是要隐藏的名称列表，且默认为 `[os.curdir, os.pardir]`。

[`dircmp`](https://docs.python.org/zh-cn/3.13/library/filecmp.html#filecmp.dircmp) 类如 [`filecmp.cmp()`](https://docs.python.org/zh-cn/3.13/library/filecmp.html#filecmp.cmp) 所描述的那样默认使用 *shallow* 形参通过执行 *shallow* 比较来比较文件。

*在 3.13 版本发生变更:* 增加了 *shallow* 形参。

[`dircmp`](https://docs.python.org/zh-cn/3.13/library/filecmp.html#filecmp.dircmp) 类提供以下方法：

## **report**()

​	将 *a* 与 *b* 之间的比较结果打印（到 [`sys.stdout`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stdout) ）。

## **report_partial_closure**()

​	打印 *a* 与 *b* 及共同直接子目录的比较结果。

## **report_full_closure**()

​	打印 *a* 与 *b* 及共同子目录比较结果（递归地）。

[`dircmp`](https://docs.python.org/zh-cn/3.13/library/filecmp.html#filecmp.dircmp) 类提供了一些有趣的属性，用以得到关于参与比较的目录树的各种信息。

​	请注意通过 [`__getattr__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__getattr__) 钩子，所有的属性都将被惰性求值，因此如果只需使用那些计算简便的属性就不会有速度上的损失。

## **left**

​	目录 *a* 。

## **right**

​	目录 *b* 。

## **left_list**

​	经 *hide* 和 *ignore* 过滤，目录 *a* 中的文件与子目录。

## **right_list**

​	经 *hide* 和 *ignore* 过滤，目录 *b* 中的文件与子目录。

## **common**

​	同时存在于目录 *a* 和 *b* 中的文件和子目录。

## **left_only**

​	仅在目录 *a* 中的文件和子目录。

## **right_only**

​	仅在目录 *b* 中的文件和子目录。

## **common_dirs**

​	同时存在于目录 *a* 和 *b* 中的子目录。

## **common_files**

​	同时存在于目录 *a* 和 *b* 中的文件。

## **common_funny**

​	在目录 *a* 和 *b* 中类型不同的名字，或者那些 [`os.stat()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.stat) 报告错误的名字。

## **same_files**

​	在目录 *a* 和 *b* 中，使用类的文件比较操作符判定相等的文件。

## **diff_files**

​	在目录 *a* 和 *b* 中，根据类的文件比较操作符判定内容不等的文件。

## **funny_files**

​	在目录 *a* 和 *b* 中无法比较的文件。

## **subdirs**

​	一个将 [`common_dirs`](https://docs.python.org/zh-cn/3.13/library/filecmp.html#filecmp.dircmp.common_dirs) 中的名称映射到 [`dircmp`](https://docs.python.org/zh-cn/3.13/library/filecmp.html#filecmp.dircmp) 实例（或者 MyDirCmp 实例，如果该实例类型为 [`dircmp`](https://docs.python.org/zh-cn/3.13/library/filecmp.html#filecmp.dircmp) 的子类 MyDirCmp 的话）的字典。

*在 3.10 版本发生变更:* 在之前版本中字典条目总是为 [`dircmp`](https://docs.python.org/zh-cn/3.13/library/filecmp.html#filecmp.dircmp) 实例。 现在条目将与 *self* 的类型相同，如果 *self* 为 [`dircmp`](https://docs.python.org/zh-cn/3.13/library/filecmp.html#filecmp.dircmp) 的子类的话。

## filecmp.**DEFAULT_IGNORES**

*Added in version 3.4.*

​	默认被 [`dircmp`](https://docs.python.org/zh-cn/3.13/library/filecmp.html#filecmp.dircmp) 忽略的目录列表。

​	下面是一个简单的例子，使用 `subdirs` 属性递归搜索两个目录以显示公共差异文件：



```
>>> from filecmp import dircmp
>>> def print_diff_files(dcmp):
...     for name in dcmp.diff_files:
...         print("diff_file %s found in %s and %s" % (name, dcmp.left,
...               dcmp.right))
...     for sub_dcmp in dcmp.subdirs.values():
...         print_diff_files(sub_dcmp)
...
>>> dcmp = dircmp('dir1', 'dir2') 
>>> print_diff_files(dcmp) 
```
