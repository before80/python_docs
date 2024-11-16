+++
title = "fnmatch --- Unix 文件名模式匹配"
date = 2024-11-15T11:53:09+08:00
weight = 70
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/fnmatch.html](https://docs.python.org/zh-cn/3.13/library/fnmatch.html)
>
> 收录该文档的时间：`2024-11-15T11:53:09+08:00`

# `fnmatch` --- Unix 文件名模式匹配

**源代码:** [Lib/fnmatch.py](https://github.com/python/cpython/tree/3.13/Lib/fnmatch.py)



------

​	此模块提供了 Unix shell 风格的通配符，它们 *并不* 等同于正则表达式（关于后者的文档参见 [`re`](https://docs.python.org/zh-cn/3.13/library/re.html#module-re) 模块）。 shell 风格通配符所使用的特殊字符如下：

| 模式     | 含意                        |
| :------- | :-------------------------- |
| `*`      | 匹配所有                    |
| `?`      | 匹配任何单个字符            |
| `[seq]`  | 匹配 *seq* 中的任何字符     |
| `[!seq]` | 匹配任何不在 *seq* 中的字符 |

​	对于字面值匹配，请将原字符用方括号括起来。 例如，`'[?]'` 将匹配字符 `'?'`。

​	注意文件名分隔符 (Unix 上为 `'/'`) *不会* 被此模块特别对待。 请参见 [`glob`](https://docs.python.org/zh-cn/3.13/library/glob.html#module-glob) 模块了解文件名扩展 ([`glob`](https://docs.python.org/zh-cn/3.13/library/glob.html#module-glob) 使用 [`filter()`](https://docs.python.org/zh-cn/3.13/library/fnmatch.html#fnmatch.filter) 来匹配文件名的各个部分)。 类似地，以一个句点打头的文件名也不会被此模块特别对待，可以通过 `*` 和 `?` 模式来匹配。

​	还要注意是使用将 *maxsize* 设为 32768 的 [`functools.lru_cache()`](https://docs.python.org/zh-cn/3.13/library/functools.html#functools.lru_cache) 来缓存下列函数中的已编译正则表达式: [`fnmatch()`](https://docs.python.org/zh-cn/3.13/library/fnmatch.html#module-fnmatch), [`fnmatchcase()`](https://docs.python.org/zh-cn/3.13/library/fnmatch.html#fnmatch.fnmatchcase), [`filter()`](https://docs.python.org/zh-cn/3.13/library/fnmatch.html#fnmatch.filter)。

## fnmatch.**fnmatch**(*name*, *pat*)

​	检测文件名字符串 *name* 是否匹配模式字符串 *pat*，返回 `True` 或 `False`。 两个形参都会使用 [`os.path.normcase()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.normcase) 进行大小写正规化。 [`fnmatchcase()`](https://docs.python.org/zh-cn/3.13/library/fnmatch.html#fnmatch.fnmatchcase) 可被用于执行大小写敏感的比较，无论这是否为所在操作系统的标准。can be used to perform a case-sensitive comparison, regardless of whether that's standard for the operating system.

​	这个例子将打印当前目录下带有扩展名 `.txt` 的所有文件名:

```
import fnmatch
import os

for file in os.listdir('.'):
    if fnmatch.fnmatch(file, '*.txt'):
        print(file)
```

## fnmatch.**fnmatchcase**(*name*, *pat*)

​	检测文件名字符串 *name* 是否匹配模式字符串 *pat*，返回 `True` 或 `False`；此比较是大小写敏感的并且不会应用 [`os.path.normcase()`](https://docs.python.org/zh-cn/3.13/library/os.path.html#os.path.normcase)。

## fnmatch.**filter**(*names*, *pat*)

​	基于 [iterable](https://docs.python.org/zh-cn/3.13/glossary.html#term-iterable) *names* 中匹配模式 *pat* 的元素构造一个列表。 它等价于 `[n for n in names if fnmatch(n, pat)]`，但实现得更为高效。

## fnmatch.**translate**(*pat*)

​	返回由 shell 风格的模式 *pat* 转换成的正则表达式以便用于 [`re.match()`](https://docs.python.org/zh-cn/3.13/library/re.html#re.match)。

​	示例:



``` python
>>> import fnmatch, re
>>>
>>> regex = fnmatch.translate('*.txt')
>>> regex
'(?s:.*\\.txt)\\Z'
>>> reobj = re.compile(regex)
>>> reobj.match('foobar.txt')
<re.Match object; span=(0, 10), match='foobar.txt'>
```

​	参见

## 模块 [`glob`](https://docs.python.org/zh-cn/3.13/library/glob.html#module-glob)

​	Unix shell 风格路径扩展。
