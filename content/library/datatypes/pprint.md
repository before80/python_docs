+++
title = "pprint --- 数据美化输出"
date = 2024-11-15T11:18:41+08:00
weight = 110
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/pprint.html](https://docs.python.org/zh-cn/3.13/library/pprint.html)
>
> 收录该文档的时间：`2024-11-15T11:18:41+08:00`

# `pprint` --- 数据美化输出

**源代码：** [Lib/pprint.py](https://github.com/python/cpython/tree/3.13/Lib/pprint.py)

------

[`pprint`](https://docs.python.org/zh-cn/3.13/library/pprint.html#module-pprint) 模块提供了“美化打印”任意 Python 数据结构的功能，这种美化形式可用作对解释器的输入。 如果经格式化的结构包含非基本 Python 类型的对象，则其美化形式可能无法被加载。 包含文件、套接字或类对象，以及许多其他不能用 Python 字面值来表示的对象都有可能导致这样的结果。

​	已格式化的表示形式会在可能的情况下将对象放在单行中，而当它们不能在允许宽度中被容纳时将其分为多行，允许宽度可由默认为 80 个字符的 *width* 形参加以调整。

​	字典在计算其显示形式前会先根据键来排序。

*在 3.9 版本发生变更:* 添加了对美化打印 [`types.SimpleNamespace`](https://docs.python.org/zh-cn/3.13/library/types.html#types.SimpleNamespace) 的支持。

*在 3.10 版本发生变更:* 添加了对美化打印 [`dataclasses.dataclass`](https://docs.python.org/zh-cn/3.13/library/dataclasses.html#dataclasses.dataclass) 的支持。



## 函数

## pprint.**pp**(*object*, *stream=None*, *indent=1*, *width=80*, *depth=None*, ***, *compact=False*, *sort_dicts=False*, *underscore_numbers=False*)

​	打印 *object* 的格式化表示形式，末尾加一个换行符。 此函数可以在交互式解释器中代替 [`print()`](https://docs.python.org/zh-cn/3.13/library/functions.html#print) 函数用于检查对象值。 提示：你可以执行重赋值 `print = pprint.pp` 以在指定作用域内使用。

## 参数:

- **object** -- 要打印的对象。
- **stream** ([file-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-like-object) | None) -- 一个文件型对象，可通过调用其 `write()` 方法将输出写入该对象。 如为 `None` (默认值 )，则使用 [`sys.stdout`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stdout)。
- **indent** ([*int*](https://docs.python.org/zh-cn/3.13/library/functions.html#int)) -- 要为每个嵌套层级添加的缩进量。
- **width** ([*int*](https://docs.python.org/zh-cn/3.13/library/functions.html#int)) -- 输出中每行所允许的最大字符数。 如果一个结构无法在宽度限制内被格式化，则将尽可能的接近。
- **depth** ([*int*](https://docs.python.org/zh-cn/3.13/library/functions.html#int) *|* *None*) -- 可被找钱的嵌套层级数量。 如果要打印的数据结构具有过深的层级，则其包含的下一层级将用 `...` 替换。 如为 `None` (默认值)，则不会限制被格式化对象的层级深度。
- **compact** ([*bool*](https://docs.python.org/zh-cn/3.13/library/functions.html#bool)) -- 控制长 [序列](https://docs.python.org/zh-cn/3.13/glossary.html#term-sequence) 的格式化方式。 如为 `False` (默认值)，则序列的每一项将被格式化为单独的行，否则在格式化每个输出行时将根据 *width* 限制容纳尽可能多的条目。
- **sort_dicts** ([*bool*](https://docs.python.org/zh-cn/3.13/library/functions.html#bool)) -- 如为 `True`，则在格式化字典时将基于键进行排序，否则将按插入顺序显示它们（默认）。
- **underscore_numbers** ([*bool*](https://docs.python.org/zh-cn/3.13/library/functions.html#bool)) -- 如为 `True`，则在格式化整数时将使用 `_` 字符作为千位分隔符，否则将不显示下划线（默认）。



``` python
>>> import pprint
>>> stuff = ['spam', 'eggs', 'lumberjack', 'knights', 'ni']
>>> stuff.insert(0, stuff)
>>> pprint.pp(stuff)
[<Recursion on list with id=...>,
 'spam',
 'eggs',
 'lumberjack',
 'knights',
 'ni']
```

> Added in version 3.8.
>

## pprint.**pprint**(*object*, *stream=None*, *indent=1*, *width=80*, *depth=None*, ***, *compact=False*, *sort_dicts=True*, *underscore_numbers=False*)

​	默认将 *sort_dicts* 设为 `True` 的 [`pp()`](https://docs.python.org/zh-cn/3.13/library/pprint.html#pprint.pp) 的别名，它将自动按字典的键进行排序，你也可以选择使用该参数默认为 `False` 的 [`pp()`](https://docs.python.org/zh-cn/3.13/library/pprint.html#pprint.pp)。

## pprint.**pformat**(*object*, *indent=1*, *width=80*, *depth=None*, ***, *compact=False*, *sort_dicts=True*, *underscore_numbers=False*)

​	将 *object* 的格式化表示形式作为字符串返回。 *indent*, *width*, *depth*, *compact*, *sort_dicts* 和 *underscore_numbers* 将作为格式化形参传递给 [`PrettyPrinter`](https://docs.python.org/zh-cn/3.13/library/pprint.html#pprint.PrettyPrinter) 构造器，它们的含义请参阅前面文档中的说明。

## pprint.**isreadable**(*object*)

​	确定 *object* 的格式化表示是否“可读”，或是否可被用来通过 [`eval()`](https://docs.python.org/zh-cn/3.13/library/functions.html#eval) 重新构建对象的值。 此函数对于递归对象总是返回 `False`。



``` python
>>> pprint.isreadable(stuff)
False
```

## pprint.**isrecursive**(*object*)

​	确定 *object* 是否需要递归的表示。 此函数会受到下面 [`saferepr()`](https://docs.python.org/zh-cn/3.13/library/pprint.html#pprint.saferepr) 所提及的同样限制的影响并可能在无法检测到可递归对象时引发 [`RecursionError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#RecursionError)。

## pprint.**saferepr**(*object*)

​	返回 *object* 的字符串表示，并为某些通用数据结构提供防递归保护，包括 [`dict`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#dict), [`list`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#list) 和 [`tuple`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#tuple) 或其未重载 `__repr__` 的子类的实例。 如果该对象表示形式公开了一个递归条目，该递归引用会被表示为 `<Recursion on typename with id=number>`。 否则该表示形式将不会被格式化。



``` python
>>> pprint.saferepr(stuff)
"[<Recursion on list with id=...>, 'spam', 'eggs', 'lumberjack', 'knights', 'ni']"
```



## PrettyPrinter 对象

## *class* pprint.**PrettyPrinter**(*indent=1*, *width=80*, *depth=None*, *stream=None*, ***, *compact=False*, *sort_dicts=True*, *underscore_numbers=False*)

​	构造一个 [`PrettyPrinter`](https://docs.python.org/zh-cn/3.13/library/pprint.html#pprint.PrettyPrinter) 实例。

​	参数的含义与 [`pp()`](https://docs.python.org/zh-cn/3.13/library/pprint.html#pprint.pp) 的相同。 注意它们的顺序有所不同，并且 *sort_dicts* 默认为 `True`。



``` python
>>> import pprint
>>> stuff = ['spam', 'eggs', 'lumberjack', 'knights', 'ni']
>>> stuff.insert(0, stuff[:])
>>> pp = pprint.PrettyPrinter(indent=4)
>>> pp.pprint(stuff)
[   ['spam', 'eggs', 'lumberjack', 'knights', 'ni'],
    'spam',
    'eggs',
    'lumberjack',
    'knights',
    'ni']
>>> pp = pprint.PrettyPrinter(width=41, compact=True)
>>> pp.pprint(stuff)
[['spam', 'eggs', 'lumberjack',
  'knights', 'ni'],
 'spam', 'eggs', 'lumberjack', 'knights',
 'ni']
>>> tup = ('spam', ('eggs', ('lumberjack', ('knights', ('ni', ('dead',
... ('parrot', ('fresh fruit',))))))))
>>> pp = pprint.PrettyPrinter(depth=6)
>>> pp.pprint(tup)
('spam', ('eggs', ('lumberjack', ('knights', ('ni', ('dead', (...)))))))
```

*在 3.4 版本发生变更:* 增加了 *compact* 形参。

*在 3.8 版本发生变更:* 增加了 *sort_dicts* 形参。

*在 3.10 版本发生变更:* 添加了 *underscore_numbers* 形参。

*在 3.11 版本发生变更:* 如果 `sys.stdout` 为 `None` 则将不会尝试向其中写入。

[`PrettyPrinter`](https://docs.python.org/zh-cn/3.13/library/pprint.html#pprint.PrettyPrinter) 的实例具有下列方法：

## PrettyPrinter.**pformat**(*object*)

​	返回 *object* 格式化表示。 这会将传给 [`PrettyPrinter`](https://docs.python.org/zh-cn/3.13/library/pprint.html#pprint.PrettyPrinter) 构造器的选项纳入考虑。

## PrettyPrinter.**pprint**(*object*)

​	在所配置的流上打印 *object* 的格式化表示，并附加一个换行符。

​	下列方法提供了与同名函数相对应的实现。 在实例上使用这些方法效率会更高一些，因为不需要创建新的 [`PrettyPrinter`](https://docs.python.org/zh-cn/3.13/library/pprint.html#pprint.PrettyPrinter) 对象。

## PrettyPrinter.**isreadable**(*object*)

​	确定对象的格式化表示是否“可读”，或者是否可使用 [`eval()`](https://docs.python.org/zh-cn/3.13/library/functions.html#eval) 重建对象值。 请注意此方法对于递归对象将返回 `False`。 如果设置了 [`PrettyPrinter`](https://docs.python.org/zh-cn/3.13/library/pprint.html#pprint.PrettyPrinter) 的 *depth* 形参并且对象深度超出允许范围，此方法将返回 `False`。

## PrettyPrinter.**isrecursive**(*object*)

​	确定对象是否需要递归表示。

​	此方法作为一个钩子提供，允许子类修改将对象转换为字符串的方式。 默认实现使用 [`saferepr()`](https://docs.python.org/zh-cn/3.13/library/pprint.html#pprint.saferepr) 实现的内部方式。

## PrettyPrinter.**format**(*object*, *context*, *maxlevels*, *level*)

​	返回三个值：字符串形式的 *object* 已格式化版本，指明结果是否可读的旗标，以及指明是否检测到递归的旗标。 第一个参数是要表示的对象。 第二个是以对象 [`id()`](https://docs.python.org/zh-cn/3.13/library/functions.html#id) 为键的字典，这些对象是当前表示上下文的一部分（影响 *object* 表示的直接和间接容器）；如果需要呈现一个已经在 *context* 中表示的对象，则第三个返回值应当为 `True`。 对 [`format()`](https://docs.python.org/zh-cn/3.13/library/pprint.html#pprint.PrettyPrinter.format) 方法的递归调用应当将容器的附加条目添加到此字典中。 第三个参数 *maxlevels* 给出了对递归的请求限制；如果没有请求限制则其值将为 `0`。 此参数应当不加修改地传给递归调用。 第四个参数 *level* 给出于当前层级；传给递归调用的参数值应当小于当前调用的值。



## 示例

​	为了演示To demonstrate several uses of the [`pp()`](https://docs.python.org/zh-cn/3.13/library/pprint.html#pprint.pp) 函数及其形参的几种用法，让我们从 [PyPI](https://pypi.org/) 获取关于某个项目的信息:



``` python
>>> import json
>>> import pprint
>>> from urllib.request import urlopen
>>> with urlopen('https://pypi.org/pypi/sampleproject/1.2.0/json') as resp:
...     project_info = json.load(resp)['info']
```

​	在其基本形式中，[`pp()`](https://docs.python.org/zh-cn/3.13/library/pprint.html#pprint.pp) 会显示整个对象:



``` python
>>> pprint.pp(project_info)
{'author': 'The Python Packaging Authority',
 'author_email': 'pypa-dev@googlegroups.com',
 'bugtrack_url': None,
 'classifiers': ['Development Status :: 3 - Alpha',
                 'Intended Audience :: Developers',
                 'License :: OSI Approved :: MIT License',
                 'Programming Language :: Python :: 2',
                 'Programming Language :: Python :: 2.6',
                 'Programming Language :: Python :: 2.7',
                 'Programming Language :: Python :: 3',
                 'Programming Language :: Python :: 3.2',
                 'Programming Language :: Python :: 3.3',
                 'Programming Language :: Python :: 3.4',
                 'Topic :: Software Development :: Build Tools'],
 'description': 'A sample Python project\n'
                '=======================\n'
                '\n'
                'This is the description file for the project.\n'
                '\n'
                'The file should use UTF-8 encoding and be written using '
                'ReStructured Text. It\n'
                'will be used to generate the project webpage on PyPI, and '
                'should be written for\n'
                'that purpose.\n'
                '\n'
                'Typical contents for this file would include an overview of '
                'the project, basic\n'
                'usage examples, etc. Generally, including the project '
                'changelog in here is not\n'
                'a good idea, although a simple "What\'s New" section for the '
                'most recent version\n'
                'may be appropriate.',
 'description_content_type': None,
 'docs_url': None,
 'download_url': 'UNKNOWN',
 'downloads': {'last_day': -1, 'last_month': -1, 'last_week': -1},
 'home_page': 'https://github.com/pypa/sampleproject',
 'keywords': 'sample setuptools development',
 'license': 'MIT',
 'maintainer': None,
 'maintainer_email': None,
 'name': 'sampleproject',
 'package_url': 'https://pypi.org/project/sampleproject/',
 'platform': 'UNKNOWN',
 'project_url': 'https://pypi.org/project/sampleproject/',
 'project_urls': {'Download': 'UNKNOWN',
                  'Homepage': 'https://github.com/pypa/sampleproject'},
 'release_url': 'https://pypi.org/project/sampleproject/1.2.0/',
 'requires_dist': None,
 'requires_python': None,
 'summary': 'A sample Python project',
 'version': '1.2.0'}
```

​	结果可以被限制到特定的 *depth* (更深层的内容将使用省略号):



``` python
>>> pprint.pp(project_info, depth=1)
{'author': 'The Python Packaging Authority',
 'author_email': 'pypa-dev@googlegroups.com',
 'bugtrack_url': None,
 'classifiers': [...],
 'description': 'A sample Python project\n'
                '=======================\n'
                '\n'
                'This is the description file for the project.\n'
                '\n'
                'The file should use UTF-8 encoding and be written using '
                'ReStructured Text. It\n'
                'will be used to generate the project webpage on PyPI, and '
                'should be written for\n'
                'that purpose.\n'
                '\n'
                'Typical contents for this file would include an overview of '
                'the project, basic\n'
                'usage examples, etc. Generally, including the project '
                'changelog in here is not\n'
                'a good idea, although a simple "What\'s New" section for the '
                'most recent version\n'
                'may be appropriate.',
 'description_content_type': None,
 'docs_url': None,
 'download_url': 'UNKNOWN',
 'downloads': {...},
 'home_page': 'https://github.com/pypa/sampleproject',
 'keywords': 'sample setuptools development',
 'license': 'MIT',
 'maintainer': None,
 'maintainer_email': None,
 'name': 'sampleproject',
 'package_url': 'https://pypi.org/project/sampleproject/',
 'platform': 'UNKNOWN',
 'project_url': 'https://pypi.org/project/sampleproject/',
 'project_urls': {...},
 'release_url': 'https://pypi.org/project/sampleproject/1.2.0/',
 'requires_dist': None,
 'requires_python': None,
 'summary': 'A sample Python project',
 'version': '1.2.0'}
```

​	此外，还可以设置建议的最大字符 *width*。 如果一个对象无法被拆分，则将超出指定宽度:



``` python
>>> pprint.pp(project_info, depth=1, width=60)
{'author': 'The Python Packaging Authority',
 'author_email': 'pypa-dev@googlegroups.com',
 'bugtrack_url': None,
 'classifiers': [...],
 'description': 'A sample Python project\n'
                '=======================\n'
                '\n'
                'This is the description file for the '
                'project.\n'
                '\n'
                'The file should use UTF-8 encoding and be '
                'written using ReStructured Text. It\n'
                'will be used to generate the project '
                'webpage on PyPI, and should be written '
                'for\n'
                'that purpose.\n'
                '\n'
                'Typical contents for this file would '
                'include an overview of the project, '
                'basic\n'
                'usage examples, etc. Generally, including '
                'the project changelog in here is not\n'
                'a good idea, although a simple "What\'s '
                'New" section for the most recent version\n'
                'may be appropriate.',
 'description_content_type': None,
 'docs_url': None,
 'download_url': 'UNKNOWN',
 'downloads': {...},
 'home_page': 'https://github.com/pypa/sampleproject',
 'keywords': 'sample setuptools development',
 'license': 'MIT',
 'maintainer': None,
 'maintainer_email': None,
 'name': 'sampleproject',
 'package_url': 'https://pypi.org/project/sampleproject/',
 'platform': 'UNKNOWN',
 'project_url': 'https://pypi.org/project/sampleproject/',
 'project_urls': {...},
 'release_url': 'https://pypi.org/project/sampleproject/1.2.0/',
 'requires_dist': None,
 'requires_python': None,
 'summary': 'A sample Python project',
 'version': '1.2.0'}
```
