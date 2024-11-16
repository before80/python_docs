+++
title = "csv --- CSV 文件读写"
date = 2024-11-15T12:03:23+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/csv.html](https://docs.python.org/zh-cn/3.13/library/csv.html)
>
> 收录该文档的时间：`2024-11-15T12:03:23+08:00`

# `csv` --- CSV 文件读写

**源代码：** [Lib/csv.py](https://github.com/python/cpython/tree/3.13/Lib/csv.py)

------

​	CSV (Comma Separated Values) 格式是电子表格和数据库中最常见的输入、输出文件格式。在 [**RFC 4180**](https://datatracker.ietf.org/doc/html/rfc4180.html) 规范推出的很多年前，CSV 格式就已经被开始使用了，由于当时并没有合理的标准，不同应用程序读写的数据会存在细微的差别。这种差别让处理多个来源的 CSV 文件变得困难。但尽管分隔符会变化，此类文件的大致格式是相似的，所以编写一个单独的模块以高效处理此类数据，将程序员从读写数据的繁琐细节中解放出来是有可能的。

[`csv`]({{< ref "/library/fileformats/csv#module-csv" >}}) 模块实现了 CSV 格式表单数据的读写。其提供了诸如“以兼容 Excel 的方式输出数据文件”或“读取 Excel 程序输出的数据文件”的功能，程序员无需知道 Excel 所采用 CSV 格式的细节。此模块同样可以用于定义其他应用程序可用的 CSV 格式或定义特定需求的 CSV 格式。

[`csv`]({{< ref "/library/fileformats/csv#module-csv" >}}) 模块中的 [`reader`]({{< ref "/library/fileformats/csv#csv.reader" >}}) 类和 [`writer`]({{< ref "/library/fileformats/csv#csv.writer" >}}) 类可用于读写序列化的数据。也可使用 [`DictReader`]({{< ref "/library/fileformats/csv#csv.DictReader" >}}) 类和 [`DictWriter`]({{< ref "/library/fileformats/csv#csv.DictWriter" >}}) 类以字典的形式读写数据。

​参见
## 该实现在“Python 增强提议” - PEP 305 (CSV 文件 API) 中被提出

​	《Python 增强提议》提出了对 Python 的这一补充。



## 模块内容

[`csv`]({{< ref "/library/fileformats/csv#module-csv" >}}) 模块定义了以下函数：

## csv.**reader**(*csvfile*, *dialect='excel'*, ***fmtparams*)

​	返回一个 [reader 对象]({{< ref "/library/fileformats/csv#reader-objects" >}})，该对象将处理给定 *csvfile* 中的行。 csvfile 必须是一个包含字符串的可迭代对象，使用 reader 所定义的 csv 格式。 csvfile 通常是一个文件型对象或列表。 如果 *csvfile* 是一个文件对象，则打开它时应设置 `newline=''`. [[1\]]({{< ref "/library/fileformats/csv#id4" >}}) 给定可选 *dialect* 形参将被用于定义一组专属于特定 CSV 变种的形参。 它可以是 [`Dialect`]({{< ref "/library/fileformats/csv#csv.Dialect" >}}) 类的子类的实例，或是 [`list_dialects()`]({{< ref "/library/fileformats/csv#csv.list_dialects" >}}) 函数所返回的字符串之一。 另一个可选关键字形参 *fmtparams* 可被用来覆盖当前变种中的单个格式形参。 有关变种和格式设置形参的完整细节，请参阅 [变种与格式参数]({{< ref "/library/fileformats/csv#csv-fmt-params" >}}) 一节。

​	csv 文件的每一行都读取为一个由字符串组成的列表。除非指定了 `QUOTE_NONNUMERIC` 格式选项（在这种情况下，未加引号的字段会转换为浮点数），否则不会执行自动数据类型转换。

​	一个简短的用法示例:



``` python
>>> import csv
>>> with open('eggs.csv', newline='') as csvfile:
...     spamreader = csv.reader(csvfile, delimiter=' ', quotechar='|')
...     for row in spamreader:
...         print(', '.join(row))
Spam, Spam, Spam, Spam, Spam, Baked Beans
Spam, Lovely Spam, Wonderful Spam
```

## csv.**writer**(*csvfile*, *dialect='excel'*, ***fmtparams*)

​	返回一个 writer 对象，该对象负责将用户的数据在给定的文件型对象上转换为带分隔符的字符串。 *csvfile* 可以是任何具有 [`write()`]({{< ref "/library/allos/io#io.TextIOBase.write" >}}) 方法的对象。 如果 *csvfile* 是一个文件对象，则打开它时应使用 `newline=''` [[1\]]({{< ref "/library/fileformats/csv#id4" >}})。 可以给出可选的 *dialect* 形参用来定义一组特定 CSV 变种专属的形参。 它可以是 [`Dialect`]({{< ref "/library/fileformats/csv#csv.Dialect" >}}) 类的某个子类的实例或是 [`list_dialects()`]({{< ref "/library/fileformats/csv#csv.list_dialects" >}}) 函数所返回的字符串之一。 还可以给出另一个可选的 *fmtparams* 关键字参数来覆盖当前变种中的单个格式化形参。 有关各个变种和格式化形参的完整细节，请参阅 [变种与格式参数]({{< ref "/library/fileformats/csv#csv-fmt-params" >}}) 部分。 为了尽量简化与实现 DB API 的模块之间的接口，可以将 [`None`]({{< ref "/library/constants#None" >}}) 作为空字符串写入。 虽然这个转换是不可逆的，但它可以简化 SQL NULL 数据值到 CSV 文件的转储而无需预处理从 `cursor.fetch*` 调用返回的数据。 在被写入之前所有其他非字符串数据都会先用 [`str()`]({{< ref "/library/stdtypes#str" >}}) 来转换为字符串。

​	一个简短的用法示例:

```
import csv
with open('eggs.csv', 'w', newline='') as csvfile:
    spamwriter = csv.writer(csvfile, delimiter=' ',
                            quotechar='|', quoting=csv.QUOTE_MINIMAL)
    spamwriter.writerow(['Spam'] * 5 + ['Baked Beans'])
    spamwriter.writerow(['Spam', 'Lovely Spam', 'Wonderful Spam'])
```

## csv.**register_dialect**(*name*[, *dialect*[, ***fmtparams*]])

​	将 *dialect* 与 *name* 关联起来。 *name* 必须是字符串。 变种的指定可以通过传入一个 [`Dialect`]({{< ref "/library/fileformats/csv#csv.Dialect" >}}) 的子类，或通过 *fmtparams* 关键字参数，或是两者同时传入，此时关键字参数会覆盖 dialect 形参。 有关变种和格式化形参的完整细节，请参阅 [变种与格式参数]({{< ref "/library/fileformats/csv#csv-fmt-params" >}}) 部分。

## csv.**unregister_dialect**(*name*)

​	从变种注册表中删除 *name* 对应的变种。如果 *name* 不是已注册的变种名称，则抛出 [`Error`]({{< ref "/library/fileformats/csv#csv.Error" >}}) 异常。

## csv.**get_dialect**(*name*)

​	返回 *name* 对应的变种。如果 *name* 不是已注册的变种名称，则抛出 [`Error`]({{< ref "/library/fileformats/csv#csv.Error" >}}) 异常。该函数返回的是不可变的 [`Dialect`]({{< ref "/library/fileformats/csv#csv.Dialect" >}}) 对象。

## csv.**list_dialects**()

​	返回所有已注册变种的名称。

## csv.**field_size_limit**([*new_limit*])

​	返回解析器当前允许的最大字段大小。如果指定了 *new_limit*，则它将成为新的最大字段大小。

[`csv`]({{< ref "/library/fileformats/csv#module-csv" >}}) 模块定义了以下类：

## *class* csv.**DictReader**(*f*, *fieldnames=None*, *restkey=None*, *restval=None*, *dialect='excel'*, **args*, ***kwds*)

​	创建一个对象，该对象在操作上类似于常规 reader，但是将每行中的信息映射到一个 [`dict`]({{< ref "/library/stdtypes#dict" >}})，该 dict 的键由 *fieldnames* 可选参数给出。

*fieldnames* 形参是一个 [sequence]({{< ref "/glossary/idx#term-sequence" >}})。 如果省略 *fieldnames*，则文件 *f* 第一行中的值将用作字段名并将从结果中去除。 如果提供了 *fieldnames*，它们将被使用而第一行将包括在结果中。 无论字段名是如何确定的，字典都将保留其原始顺序。

​	如果某一行中的字段多于字段名，则剩余数据会被放入一个列表，并与 *restkey* 所指定的字段名 (默认为 `None`) 一起保存。 如果某个非空白行的字段少于字段名，则缺失的值会使用 *restval* 的值来填充 (默认为 `None`)。

​	所有其他可选或关键字参数都传递给底层的 [`reader`]({{< ref "/library/fileformats/csv#csv.reader" >}}) 实例。

​	如果传给 *fieldnames* 的参数是一个迭代器，它将被强制转换为 [`list`]({{< ref "/library/stdtypes#list" >}})。

> 在 3.6 版本发生变更: 返回的行现在的类型是 `OrderedDict`。

> 在 3.8 版本发生变更: 现在，返回的行是 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 类型。

​	一个简短的用法示例:



``` python
>>> import csv
>>> with open('names.csv', newline='') as csvfile:
...     reader = csv.DictReader(csvfile)
...     for row in reader:
...         print(row['first_name'], row['last_name'])
...
Eric Idle
John Cleese

>>> print(row)
{'first_name': 'John', 'last_name': 'Cleese'}
```

## *class* csv.**DictWriter**(*f*, *fieldnames*, *restval=''*, *extrasaction='raise'*, *dialect='excel'*, **args*, ***kwds*)

​	创建一个对象，该对象在操作上类似常规 writer，但会将字典映射到输出行。 *fieldnames* 形参是一个由键组成的 [`序列`]({{< ref "/library/datatypes/collections_abc#module-collections.abc" >}})，它指定字典中要传给 [`writerow()`]({{< ref "/library/fileformats/csv#csv.csvwriter.writerow" >}}) 方法并写入文件 *f* 的值的顺序。 如果字典没有 *fieldnames* 中的键，则可选的 *restval* 形参将指明要写入的值。 如果传递给 [`writerow()`]({{< ref "/library/fileformats/csv#csv.csvwriter.writerow" >}}) 方法包含的键在 *fieldnames* 中找不到，则可选的 *extrasaction* 形参将指明要执行的操作。 如果将其设为默认值 `'raise'`，则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。 如果将其设为 `'ignore'`，则字典中额外的值将被忽略。 任何其他可选或关键字参数都将被传递给下层的 [`writer`]({{< ref "/library/fileformats/csv#csv.writer" >}}) 实例。

​	注意，与 [`DictReader`]({{< ref "/library/fileformats/csv#csv.DictReader" >}}) 类不同，[`DictWriter`]({{< ref "/library/fileformats/csv#csv.DictWriter" >}}) 类的 *fieldnames* 参数不是可选参数。

​	如果传给 *fieldnames* 的参数是一个迭代器，它将被强制转换为 [`list`]({{< ref "/library/stdtypes#list" >}})。

​	一个简短的用法示例:

```
import csv

with open('names.csv', 'w', newline='') as csvfile:
    fieldnames = ['first_name', 'last_name']
    writer = csv.DictWriter(csvfile, fieldnames=fieldnames)

    writer.writeheader()
    writer.writerow({'first_name': 'Baked', 'last_name': 'Beans'})
    writer.writerow({'first_name': 'Lovely', 'last_name': 'Spam'})
    writer.writerow({'first_name': 'Wonderful', 'last_name': 'Spam'})
```

## *class* csv.**Dialect**

[`Dialect`]({{< ref "/library/fileformats/csv#csv.Dialect" >}}) 类是一个容器类，其属性包含有如何处理双引号、空白符、分隔符等的信息。 由于缺少严格的 CSV 规格描述，不同的应用程序会产生略有差别的 CSV 数据。 [`Dialect`]({{< ref "/library/fileformats/csv#csv.Dialect" >}}) 实例定义了 [`reader`]({{< ref "/library/fileformats/csv#csv.reader" >}}) 和 [`writer`]({{< ref "/library/fileformats/csv#csv.writer" >}}) 实例将具有怎样的行为。

​	所有可用的 [`Dialect`]({{< ref "/library/fileformats/csv#csv.Dialect" >}}) 名称会由 [`list_dialects()`]({{< ref "/library/fileformats/csv#csv.list_dialects" >}}) 返回，并且它们可由特定的 [`reader`]({{< ref "/library/fileformats/csv#csv.reader" >}}) 和 [`writer`]({{< ref "/library/fileformats/csv#csv.writer" >}}) 类通过它们的初始化函数 (`__init__`) 来注册，例如:

```
import csv

with open('students.csv', 'w', newline='') as csvfile:
    writer = csv.writer(csvfile, dialect='unix')
```

## *class* csv.**excel**

[`excel`]({{< ref "/library/fileformats/csv#csv.excel" >}}) 类定义了 Excel 生成的 CSV 文件的常规属性。它在变种注册表中的名称是 `'excel'`。

## *class* csv.**excel_tab**

[`excel_tab`]({{< ref "/library/fileformats/csv#csv.excel_tab" >}}) 类定义了 Excel 生成的、制表符分隔的 CSV 文件的常规属性。它在变种注册表中的名称是 `'excel-tab'`。

## *class* csv.**unix_dialect**

[`unix_dialect`]({{< ref "/library/fileformats/csv#csv.unix_dialect" >}}) 类定义了在 UNIX 系统上生成的 CSV 文件的常规属性，即使用 `'\n'` 作为换行符，且所有字段都有引号包围。它在变种注册表中的名称是 `'unix'`。

> Added in version 3.2.
>

## *class* csv.**Sniffer**

[`Sniffer`]({{< ref "/library/fileformats/csv#csv.Sniffer" >}}) 类用于推断 CSV 文件的格式。

[`Sniffer`]({{< ref "/library/fileformats/csv#csv.Sniffer" >}}) 类提供了两个方法：

## **sniff**(*sample*, *delimiters=None*)

​	分析给定的 *sample* 并返回一个 [`Dialect`]({{< ref "/library/fileformats/csv#csv.Dialect" >}}) 子类，该子类中包含了分析出的格式参数。如果给出可选的 *delimiters* 参数，则该参数会被解释为字符串，该字符串包含了可能的有效定界符。

## **has_header**(*sample*)

​	分析 sample 文本（假定为 CSV 格式），如果发现其首行为一组列标题则返回 [`True`]({{< ref "/library/constants#True" >}})。 在检查每一列时，将考虑是否满足两个关键标准之一来估计 sample 是否包含标题:

- 第二至第 n 行包含数字值
- 第二至第 n 行包含字符串值，其中至少有一个值的长度与该列预期标题的长度不同。

​	会对第一行之后的二十行进行采样；如果有超过一半的列 + 行符合标准，则返回 [`True`]({{< ref "/library/constants#True" >}})。

​备注
 

​	此方法是一个粗略的启发式方式，有可能产生错误的真值和假值。

​	使用 [`Sniffer`]({{< ref "/library/fileformats/csv#csv.Sniffer" >}}) 的示例：

```
with open('example.csv', newline='') as csvfile:
    dialect = csv.Sniffer().sniff(csvfile.read(1024))
    csvfile.seek(0)
    reader = csv.reader(csvfile, dialect)
    # ... 在此处理 CSV 文件内容 ...
```

[`csv`]({{< ref "/library/fileformats/csv#module-csv" >}}) 模块定义了以下常量：

## csv.**QUOTE_ALL**

​	指示 [`writer`]({{< ref "/library/fileformats/csv#csv.writer" >}}) 对象给所有字段加上引号。

## csv.**QUOTE_MINIMAL**

​	指示 [`writer`]({{< ref "/library/fileformats/csv#csv.writer" >}}) 对象仅为包含特殊字符（例如 *定界符*、*引号字符* 或 *行结束符* 中的任何字符）的字段加上引号。

## csv.**QUOTE_NONNUMERIC**

​	指示 [`writer`]({{< ref "/library/fileformats/csv#csv.writer" >}}) 对象为所有非数字字段加上引号。

​	指示 [`reader`]({{< ref "/library/fileformats/csv#csv.reader" >}}) 将所有未加引号的字段转换为 *float* 类型。

## csv.**QUOTE_NONE**

​	指示 [`writer`]({{< ref "/library/fileformats/csv#csv.writer" >}}) 对象不使用引号引出字段。当 *定界符* 出现在输出数据中时，其前面应该有 *转义符*。如果未设置 *转义符*，则遇到任何需要转义的字符时，writer 都会抛出 [`Error`]({{< ref "/library/fileformats/csv#csv.Error" >}}) 异常。

​	指示 [`reader`]({{< ref "/library/fileformats/csv#csv.reader" >}}) 对象不对引号字符执行特殊处理。

## csv.**QUOTE_NOTNULL**

​	指示 [`writer`]({{< ref "/library/fileformats/csv#csv.writer" >}}) 对象为所有不为 `None` 的字段加引号。 这类似于 [`QUOTE_ALL`]({{< ref "/library/fileformats/csv#csv.QUOTE_ALL" >}})，区别是如果一个字段值为 `None` 则会写入一个（不带引号的）空字符串。

​	指示 [`reader`]({{< ref "/library/fileformats/csv#csv.reader" >}}) 对象将（不带引号的）空字段解读为 `None` 并在其他情况下采取与 [`QUOTE_ALL`]({{< ref "/library/fileformats/csv#csv.QUOTE_ALL" >}}) 相同的行为。

> Added in version 3.12.
>

## csv.**QUOTE_STRINGS**

​	指示 [`writer`]({{< ref "/library/fileformats/csv#csv.writer" >}}) 对象总是为字符串字段加引号。 这类似于 [`QUOTE_NONNUMERIC`]({{< ref "/library/fileformats/csv#csv.QUOTE_NONNUMERIC" >}})，区别是如果一个字段值为 `None` 则会写入一个（不带引号的）空字符串。

​	指示 [`reader`]({{< ref "/library/fileformats/csv#csv.reader" >}}) 对象将（不带引号的）空字符串解读为 `None` 并在其他情况下采取与 [`QUOTE_NONNUMERIC`]({{< ref "/library/fileformats/csv#csv.QUOTE_NONNUMERIC" >}}) 相同的行为。

> Added in version 3.12.
>

[`csv`]({{< ref "/library/fileformats/csv#module-csv" >}}) 模块定义了以下异常：

## *exception* csv.**Error**

​	该异常可能由任何发生错误的函数抛出。



## 变种与格式参数

​	为了更容易地指定输入和输出记录的格式，特定的多个格式化形参将组合成为不同的 dialect。 特定的 dialect 是 [`Dialect`]({{< ref "/library/fileformats/csv#csv.Dialect" >}}) 类的一个子类，它包含多个用于描述 CSV 文件的格式的属性。 当创建 [`reader`]({{< ref "/library/fileformats/csv#csv.reader" >}}) 或 [`writer`]({{< ref "/library/fileformats/csv#csv.writer" >}}) 对象时，程序员可以指定一个字符串或 [`Dialect`]({{< ref "/library/fileformats/csv#csv.Dialect" >}}) 类的子类作为 dialect 形参。 作为对 *dialect* 形参的补充或替代，程序员还可以指定单独的格式化形参，它们的名称与 [`Dialect`]({{< ref "/library/fileformats/csv#csv.Dialect" >}}) 类所定义的以下属性相同。

​	Dialect 类支持以下属性：

## Dialect.**delimiter**

​	一个用于分隔字段的单字符，默认为 `','`。

## Dialect.**doublequote**

​	控制出现在字段中的 *引号字符* 本身应如何被引出。当该属性为 [`True`]({{< ref "/library/constants#True" >}}) 时，双写引号字符。如果该属性为 [`False`]({{< ref "/library/constants#False" >}})，则在 *引号字符* 的前面放置 *转义符*。默认值为 [`True`]({{< ref "/library/constants#True" >}})。

​	在输出时，如果 *doublequote* 是 [`False`]({{< ref "/library/constants#False" >}})，且 *转义符* 未指定，且在字段中发现 *引号字符* 时，会抛出 [`Error`]({{< ref "/library/fileformats/csv#csv.Error" >}}) 异常。

## Dialect.**escapechar**

​	一个用于 writer 的单字符，用来在 *quoting* 设置为 [`QUOTE_NONE`]({{< ref "/library/fileformats/csv#csv.QUOTE_NONE" >}}) 的情况下转义 *定界符*，在 *doublequote* 设置为 [`False`]({{< ref "/library/constants#False" >}}) 的情况下转义 *引号字符*。在读取时，*escapechar* 去除了其后所跟字符的任何特殊含义。该属性默认为 [`None`]({{< ref "/library/constants#None" >}})，表示禁用转义。

> 在 3.11 版本发生变更: 不允许空的 *escapechar*。

## Dialect.**lineterminator**

​	放在 [`writer`]({{< ref "/library/fileformats/csv#csv.writer" >}}) 产生的行的结尾，默认为 `'\r\n'`。

​备注
 

[`reader`]({{< ref "/library/fileformats/csv#csv.reader" >}}) 经过硬编码，会识别 `'\r'` 或 `'\n'` 作为行尾，并忽略 *lineterminator*。未来可能会更改这一行为。

## Dialect.**quotechar**

​	一个单字符，用于包住含有特殊字符的字段，特殊字符如 *定界符* 或 *引号字符* 或换行符。默认为 `'"'`。

> 在 3.11 版本发生变更: 不允许空的 *quotechar*。

## Dialect.**quoting**

​	控制 writer 何时生成引号以及 reader 何时识别引号。 它可以设为任意 [QUOTE_* 常量]({{< ref "/library/fileformats/csv#csv-constants" >}}) 并且默认为 [`QUOTE_MINIMAL`]({{< ref "/library/fileformats/csv#csv.QUOTE_MINIMAL" >}})。

## Dialect.**skipinitialspace**

​	当为 [`True`]({{< ref "/library/constants#True" >}}) 时，紧接在 *delimiter* 之后空格会被忽略。 默认值为 [`False`]({{< ref "/library/constants#False" >}})。

## Dialect.**strict**

​	如果为 `True`，则在输入错误的 CSV 时抛出 [`Error`]({{< ref "/library/fileformats/csv#csv.Error" >}}) 异常。默认值为 `False`。



## Reader 对象

​	Reader 对象（[`DictReader`]({{< ref "/library/fileformats/csv#csv.DictReader" >}}) 实例和 [`reader()`]({{< ref "/library/fileformats/csv#csv.reader" >}}) 函数返回的对象）具有以下公开方法：

## csvreader.`__next__`()

​	返回 reader 的可迭代对象的下一行，它可以是一个列表（如果对象是由 [`reader()`]({{< ref "/library/fileformats/csv#csv.reader" >}}) 返回）或字典（如果是一个 [`DictReader`]({{< ref "/library/fileformats/csv#csv.DictReader" >}}) 实例），根据当前 [`Dialect`]({{< ref "/library/fileformats/csv#csv.Dialect" >}}) 来解析。 通常你应当以 `next(reader)` 的形式来调用它。

​	Reader 对象具有以下公开属性：

## csvreader.**dialect**

​	变种描述，只读，供解析器使用。

## csvreader.**line_num**

​	源迭代器已经读取了的行数。它与返回的记录数不同，因为记录可能跨越多行。

​	DictReader 对象具有以下公开属性：

## DictReader.**fieldnames**

​	字段名称。如果在创建对象时未传入字段名称，则首次访问时或从文件中读取第一条记录时会初始化此属性。

## Writer 对象

[`writer`]({{< ref "/library/fileformats/csv#csv.writer" >}}) 对象 ([`DictWriter`]({{< ref "/library/fileformats/csv#csv.DictWriter" >}}) 实例和 [`writer()`]({{< ref "/library/fileformats/csv#csv.writer" >}}) 函数所返回的对象 ) 具有以下公共方法。 对于 [`writer`]({{< ref "/library/fileformats/csv#csv.writer" >}}) 对象 *row* 必须是输出字符串或数字的可迭代对象的数字，而对于 [`DictWriter`]({{< ref "/library/fileformats/csv#csv.DictWriter" >}}) 对象则是一个将文件名映射到字符串或数字 (会先将其传给 [`str()`]({{< ref "/library/stdtypes#str" >}})) 的字典。 请注意在写入复数时会用圆括号括起来。 这可能会给其他读取 CSV 文件的程序带来一些问题 (假定它们确实支持复数)。

## csvwriter.**writerow**(*row*)

​	将 *row* 形参写入到 writer 的文件对象，根据当前 [`Dialect`]({{< ref "/library/fileformats/csv#csv.Dialect" >}}) 进行格式化。 返回对下层文件对象的 *write* 方法的调用的返回值。

> 在 3.5 版本发生变更: 开始支持任意类型的迭代器。

## csvwriter.**writerows**(*rows*)

​	将 *rows\*（即能迭代出多个上述 \*row* 对象的迭代器）中的所有元素写入 writer 的文件对象，并根据当前设置的变种进行格式化。

​	Writer 对象具有以下公开属性：

## csvwriter.**dialect**

​	变种描述，只读，供 writer 使用。

​	DictWriter 对象具有以下公开方法：

## DictWriter.**writeheader**()

​	在 writer 的文件对象中，写入一行字段名称（字段名称在构造函数中指定），并根据当前设置的变种进行格式化。本方法的返回值就是内部使用的 [`csvwriter.writerow()`]({{< ref "/library/fileformats/csv#csv.csvwriter.writerow" >}}) 方法的返回值。

> Added in version 3.2.
>

> 在 3.8 版本发生变更: 现在 [`writeheader()`]({{< ref "/library/fileformats/csv#csv.DictWriter.writeheader" >}}) 也返回其内部使用的 [`csvwriter.writerow()`]({{< ref "/library/fileformats/csv#csv.csvwriter.writerow" >}}) 方法的返回值。



## 例子

​	读取 CSV 文件最简单的一个例子:

```
import csv
with open('some.csv', newline='') as f:
    reader = csv.reader(f)
    for row in reader:
        print(row)
```

​	读取其他格式的文件:

```
import csv
with open('passwd', newline='') as f:
    reader = csv.reader(f, delimiter=':', quoting=csv.QUOTE_NONE)
    for row in reader:
        print(row)
```

​	相应最简单的写入示例是:

```
import csv
with open('some.csv', 'w', newline='') as f:
    writer = csv.writer(f)
    writer.writerows(someiterable)
```

​	由于 [`open()`]({{< ref "/library/functions#open" >}}) 被用来打开 CSV 文件供读取，因此在默认情况下将使用系统默认编码格式 (参见 [`locale.getencoding()`]({{< ref "/library/i18n/locale#locale.getencoding" >}})) 把文件解码至 unicode。 要使用其他编码格式来解码文件，请使用 open 的 `encoding` 参数:

```
import csv
with open('some.csv', newline='', encoding='utf-8') as f:
    reader = csv.reader(f)
    for row in reader:
        print(row)
```

​	这同样适用于写入非系统默认编码的内容：打开输出文件时，指定 encoding 参数。

​	注册一个新的变种:

```
import csv
csv.register_dialect('unixpwd', delimiter=':', quoting=csv.QUOTE_NONE)
with open('passwd', newline='') as f:
    reader = csv.reader(f, 'unixpwd')
```

​	Reader 的更高级用法——捕获并报告错误:

```
import csv, sys
filename = 'some.csv'
with open(filename, newline='') as f:
    reader = csv.reader(f)
    try:
        for row in reader:
            print(row)
    except csv.Error as e:
        sys.exit('file {}, line {}: {}'.format(filename, reader.line_num, e))
```

​	尽管该模块不直接支持解析字符串，但仍可如下轻松完成:

```
import csv
for row in csv.reader(['one,two,three']):
    print(row)
```

​备注
[1]([1](https://docs.python.org/zh-cn/3.13/library/csv.html#id1),[2]({{< ref "/library/fileformats/csv#id2" >}}))

​	如果没有指定 `newline=''`，则嵌入引号中的换行符将无法正确解析，并且在写入时，使用 `\r\n` 换行的平台会有多余的 `\r` 写入。由于 csv 模块会执行自己的（[通用]({{< ref "/glossary/idx#term-universal-newlines" >}})）换行符处理，因此指定 `newline=''` 应该总是安全的。
