+++
title = "configparser --- 配置文件解析器"
date = 2024-11-15T12:03:23+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/configparser.html](https://docs.python.org/zh-cn/3.13/library/configparser.html)
>
> 收录该文档的时间：`2024-11-15T12:03:23+08:00`

# `configparser` --- 配置文件解析器

**源代码:** [Lib/configparser.py](https://github.com/python/cpython/tree/3.13/Lib/configparser.py)

------

​	此模块提供了它实现一种基本配置语言 [`ConfigParser`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser" >}}) 类，这种语言所提供的结构与 Microsoft Windows INI 文件的类似。 你可以使用这种语言来编写能够由最终用户来自定义的 Python 程序。

​备注
 

​	这个库 *并不* 能够解析或写入在 Windows Registry 扩展版本 INI 语法中所使用的值-类型前缀。

​参见
## 模块 [`tomllib`]({{< ref "/library/fileformats/tomllib#module-tomllib" >}})

​	TOML 是一种具有良好规范的针对应用程序配置文件的格式。 它被专门设计作为 INI 改进版本。

## 模块 [`shlex`]({{< ref "/library/frameworks/shlex#module-shlex" >}})

​	支持创建类似 Unix shell 的同样可被用于应用程序配置文件的迷你语言。

## 模块 [`json`]({{< ref "/library/netdata/json#module-json" >}})

`json` 模块实现了 JavaScript 语法的一个子集，它有时被用于配置，但是不支持注释。

## 快速起步

​	让我们准备一个非常基本的配置文件，它看起来是这样的:

```
[DEFAULT]
ServerAliveInterval = 45
Compression = yes
CompressionLevel = 9
ForwardX11 = yes

[forge.example]
User = hg

[topsecret.server.example]
Port = 50022
ForwardX11 = no
```

​	INI 文件的结构描述见 [以下章节]({{< ref "/library/fileformats/configparser#supported-ini-file-structure" >}})。 总的来说，这种文件由多个节组成，每个节包含多个带有值的键。 [`configparser`]({{< ref "/library/fileformats/configparser#module-configparser" >}}) 类可以读取和写入这种文件。 让我们先通过程序方式来创建上述的配置文件。



``` python
>>> import configparser
>>> config = configparser.ConfigParser()
>>> config['DEFAULT'] = {'ServerAliveInterval': '45',
...                      'Compression': 'yes',
...                      'CompressionLevel': '9'}
>>> config['forge.example'] = {}
>>> config['forge.example']['User'] = 'hg'
>>> config['topsecret.server.example'] = {}
>>> topsecret = config['topsecret.server.example']
>>> topsecret['Port'] = '50022'     # 更改解析器
>>> topsecret['ForwardX11'] = 'no'  # same here
>>> config['DEFAULT']['ForwardX11'] = 'yes'
>>> with open('example.ini', 'w') as configfile:
...   config.write(configfile)
...
```

​	如你所见，我们可以把配置解析器当作一个字典来处理。 两者确实存在差异，[将在后文说明]({{< ref "/library/fileformats/configparser#mapping-protocol-access" >}})，但是其行为非常接近于字典所具有一般行为。

​	现在我们已经创建并保存了一个配置文件，让我们再将它读取出来并探究其中包含的数据。



``` python
>>> config = configparser.ConfigParser()
>>> config.sections()
[]
>>> config.read('example.ini')
['example.ini']
>>> config.sections()
['forge.example', 'topsecret.server.example']
>>> 'forge.example' in config
True
>>> 'python.org' in config
False
>>> config['forge.example']['User']
'hg'
>>> config['DEFAULT']['Compression']
'yes'
>>> topsecret = config['topsecret.server.example']
>>> topsecret['ForwardX11']
'no'
>>> topsecret['Port']
'50022'
>>> for key in config['forge.example']:  
...     print(key)
user
compressionlevel
serveraliveinterval
compression
forwardx11
>>> config['forge.example']['ForwardX11']
'yes'
```

​	正如我们在上面所看到的，相关的 API 相当直观。 唯一有些神奇的地方是 `DEFAULT` 小节，它为所有其他小节提供了默认值 [[1\]]({{< ref "/library/fileformats/configparser#id16" >}})。 还要注意小节中的键大小写不敏感并且会存储为小写形式 [[1\]]({{< ref "/library/fileformats/configparser#id16" >}})。

​	将多个配置读入单个 [`ConfigParser`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser" >}}) 是可能的，其中最近添加的配置具有最高优先级。 任何冲突的键都会从更近的配置获取并且先前存在的键会被保留。 下面的例子读入一个 `override.ini` 文件，它将覆盖任何来自 `example.ini` 文件的冲突的键。

```
[DEFAULT]
ServerAliveInterval = -1
```



``` python
>>> config_override = configparser.ConfigParser()
>>> config_override['DEFAULT'] = {'ServerAliveInterval': '-1'}
>>> with open('override.ini', 'w') as configfile:
...     config_override.write(configfile)
...
>>> config_override = configparser.ConfigParser()
>>> config_override.read(['example.ini', 'override.ini'])
['example.ini', 'override.ini']
>>> print(config_override.get('DEFAULT', 'ServerAliveInterval'))
-1
```

​	此行为等价于一次 [`ConfigParser.read()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.read" >}}) 调用并向 *filenames* 形参传入多个文件。

## 支持的数据类型

​	配置解析器并不会猜测配置文件中值的类型，而总是将它们在内部存储为字符串。 这意味着如果你需要其他数据类型，你应当自己来转换:



``` python
>>> int(topsecret['Port'])
50022
>>> float(topsecret['CompressionLevel'])
9.0
```

​	由于这种任务十分常用，配置解析器提供了一系列便捷的获取方法来处理整数、浮点数和布尔值。 最后一个类型的处理最为有趣，因为简单地将值传给 `bool()` 是没有用的，`bool('False')` 仍然会是 `True`。 为解决这个问题配置解析器还提供了 [`getboolean()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.getboolean" >}})。 这个方法对大小写不敏感并可识别 `'yes'`/`'no'`, `'on'`/`'off'`, `'true'`/`'false'` 和 `'1'`/`'0'` [[1\]]({{< ref "/library/fileformats/configparser#id16" >}}) 等布尔值。 例如:



``` python
>>> topsecret.getboolean('ForwardX11')
False
>>> config['forge.example'].getboolean('ForwardX11')
True
>>> config.getboolean('forge.example', 'Compression')
True
```

​	除了 [`getboolean()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.getboolean" >}})，配置解析器还提供了同类的 [`getint()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.getint" >}}) 和 [`getfloat()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.getfloat" >}}) 方法。 你可以注册你自己的转换器并或是定制已提供的转换器。 [[1\]]({{< ref "/library/fileformats/configparser#id16" >}})

## 回退值

​	与字典类似，你可以使用某一节的 [`get()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.get" >}}) 方法来提供回退值：



``` python
>>> topsecret.get('Port')
'50022'
>>> topsecret.get('CompressionLevel')
'9'
>>> topsecret.get('Cipher')
>>> topsecret.get('Cipher', '3des-cbc')
'3des-cbc'
```

​	请注意默认值会优先于回退值。 例如，在我们的示例中 `'CompressionLevel'` 键仅在 `'DEFAULT'` 小节中被指定。 如果我们尝试从 `'topsecret.server.example'` 小节获取它，我们将总是会得到默认值，即使我们指定了一个回退值:



``` python
>>> topsecret.get('CompressionLevel', '3')
'9'
```

​	还需要注意的一点是解析器层级的 [`get()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.get" >}}) 方法提供了自定义的更复杂接口，它被继续维护用于向下兼容。 当使用此方法时，可以通过 `fallback` 仅限关键字参数提供一个回退值：



``` python
>>> config.get('forge.example', 'monster',
...            fallback='No such things as monsters')
'No such things as monsters'
```

​	同样的 `fallback` 参数也可在 [`getint()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.getint" >}}), [`getfloat()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.getfloat" >}}) 和 [`getboolean()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.getboolean" >}}) 方法中使用，例如:



``` python
>>> 'BatchMode' in topsecret
False
>>> topsecret.getboolean('BatchMode', fallback=True)
True
>>> config['DEFAULT']['BatchMode'] = 'no'
>>> topsecret.getboolean('BatchMode', fallback=True)
False
```

## 受支持的 INI 文件结构

​	配置文件是由小节组成的，每个小节都有一个 `[section]` 标头，加上多个由特定字符串 (默认为 `=` 或 `:` [[1\]]({{< ref "/library/fileformats/configparser#id16" >}})) 分隔的键/值条目。 在默认情况下，小节名对大小写敏感而键对大小写不敏感 [[1\]]({{< ref "/library/fileformats/configparser#id16" >}})。 键和值开头和末尾的空格会被移除。 在解释器配置允许时值可以被省略 [[1\]]({{< ref "/library/fileformats/configparser#id16" >}})，在此情况下键/值分隔符也可以被省略。 值还可以跨越多行，只要值的其他行带有比第一行更深的缩进。 依据解析器的具体模式，空白行可能会被视为多行值的组成部分或是被忽略。

​	在默认情况下，有效的节名称可以是不包含 '\n' 的任意字符串。 要改变此设定，请参阅 [`ConfigParser.SECTCRE`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.SECTCRE" >}})。

​	如果解析器通过 `allow_unnamed_section=True` 被配置为允许未命名的最高层级小节则第一个小节的名称可以省略。 在这种情况下，键/值可以通过 [`UNNAMED_SECTION`]({{< ref "/library/fileformats/configparser#configparser.UNNAMED_SECTION" >}}) 来获取例如 `config[UNNAMED_SECTION]`。

​	配置文件可以包含注释，要带有指定字符前缀 (默认为 `#` 和 `;` [[1\]]({{< ref "/library/fileformats/configparser#id16" >}}))。 注释可以单独出现于原本的空白行，并可使用缩进。 [[1\]]({{< ref "/library/fileformats/configparser#id16" >}})

​	例如:

```
[Simple Values]
key=value
spaces in keys=allowed
spaces in values=allowed as well
spaces around the delimiter = obviously
you can also use : to delimit keys from values

[All Values Are Strings]
values like this: 1000000
or this: 3.14159265359
are they treated as numbers? : no
integers, floats and booleans are held as: strings
can use the API to get converted values directly: true

[Multiline Values]
chorus: I'm a lumberjack, and I'm okay
    I sleep all night and I work all day

[No Values]
key_without_value
empty string value here =

[You can use comments]
# like this
; or this

# By default only in an empty line.
# Inline comments can be harmful because they prevent users
# from using the delimiting characters as parts of values.
# That being said, this can be customized.

    [Sections Can Be Indented]
        can_values_be_as_well = True
        does_that_mean_anything_special = False
        purpose = formatting for readability
        multiline_values = are
            handled just fine as
            long as they are indented
            deeper than the first line
            of a value
        # Did I mention we can indent comments, too?
```



## 未命名小节

​	第一（或唯一）小节的名称可以省略并且其值可通过 [`UNNAMED_SECTION`]({{< ref "/library/fileformats/configparser#configparser.UNNAMED_SECTION" >}}) 属性来获取。



``` python
>>> config = """
... option = value
...
... [  Section 2  ]
... another = val
... """
>>> unnamed = configparser.ConfigParser(allow_unnamed_section=True)
>>> unnamed.read_string(config)
>>> unnamed.get(configparser.UNNAMED_SECTION, 'option')
'value'
```

## 值的插值

​	在核心功能之上，[`ConfigParser`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser" >}}) 还支持插值。 这意味着值可以在被 `get()` 调用返回之前进行预处理。

## *class* configparser.**BasicInterpolation**

​	默认实现由 [`ConfigParser`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser" >}}) 来使用。 它允许值包含引用了相同小节中其他值或者特殊的默认小节中的值的格式字符串 [[1\]]({{< ref "/library/fileformats/configparser#id16" >}})。 额外的默认值可以在初始化时提供。

​	例如:

```
[Paths]
home_dir: /Users
my_dir: %(home_dir)s/lumberjack
my_pictures: %(my_dir)s/Pictures

[Escape]
# use a %% to escape the % sign (% is the only character that needs to be escaped):
gain: 80%%
```

​	在上面的例子里，[`ConfigParser`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser" >}}) 的 *interpolation* 设为 `BasicInterpolation()`，这会将 `%(home_dir)s` 求解为 `home_dir` 的值 (在这里是 `/Users`)。 `%(my_dir)s` 的将被实际求解为 `/Users/lumberjack`。 所有插值都是按需进行的，这样引用链中使用的键不必以任何特定顺序在配置文件中指明。

​	当 `interpolation` 设为 `None` 时，解析器会简单地返回 `%(my_dir)s/Pictures` 作为 `my_pictures` 的值，并返回 `%(home_dir)s/lumberjack` 作为 `my_dir` 的值。

## *class* configparser.**ExtendedInterpolation**

​	一个用于插值的替代处理程序实现了更高级的语法，它被用于 `zc.buildout` 中的实例。 扩展插值使用 `${section:option}` 来表示来自外部小节的值。 插值可以跨越多个层级。 为了方便使用，`section:` 部分可被省略，插值会默认作用于当前小节（可能会从特殊小节获取默认值）。

​	例如，上面使用基本插值描述的配置，使用扩展插值将是这个样子:

```
[Paths]
home_dir: /Users
my_dir: ${home_dir}/lumberjack
my_pictures: ${my_dir}/Pictures

[Escape]
# use a $$ to escape the $ sign ($ is the only character that needs to be escaped):
cost: $$80
```

​	来自其他小节的值也可以被获取:

```
[Common]
home_dir: /Users
library_dir: /Library
system_dir: /System
macports_dir: /opt/local

[Frameworks]
Python: 3.2
path: ${Common:system_dir}/Library/Frameworks/

[Arthur]
nickname: Two Sheds
last_name: Jackson
my_dir: ${Common:home_dir}/twosheds
my_pictures: ${my_dir}/Pictures
python_dir: ${Frameworks:path}/Python/Versions/${Frameworks:Python}
```

## 映射协议访问

> Added in version 3.2.
>

​	映射协议访问这个通用名称是指允许以字典的方式来使用自定义对象的功能。 在 [`configparser`]({{< ref "/library/fileformats/configparser#module-configparser" >}}) 中，映射接口的实现使用了 `parser['section']['option']` 标记法。

`parser['section']` 专门为解析器中的小节数据返回一个代理。 这意味着其中的值不会被拷贝，而是在需要时从原始解析器中获取。 更为重要的是，当值在小节代理上被修改时，它们其实是在原始解析器中发生了改变。

[`configparser`]({{< ref "/library/fileformats/configparser#module-configparser" >}}) 对象的行为会尽可能地接近真正的字典。 映射接口是完整而且遵循 [`MutableMapping`]({{< ref "/library/datatypes/collections_abc#collections.abc.MutableMapping" >}}) ABC 规范的。 但是，还是有一些差异应当被纳入考虑:

- 默认情况下，小节中的所有键是以大小写不敏感的方式来访问的 [[1\]]({{< ref "/library/fileformats/configparser#id16" >}})。 例如 `for option in parser["section"]` 只会产生 `optionxform` 形式的选项键名称。 也就是说默认使用小写字母键名。 与此同时，对于一个包含键 `'a'` 的小节，以下两个表达式均将返回 `True`:

  ```
  "a" in parser["section"]
  "A" in parser["section"]
  ```

- 所有小节也包括 `DEFAULTSECT`，这意味着对一个小节执行 `.clear()` 可能无法使得该小节显示为空。 这是因为默认值是无法从小节中被删除的（因为从技术上说它们并不在那里）。 如果它们在小节中被覆盖，删除将导致默认值重新变为可见。 尝试删除默认值将会引发 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}})。

- `DEFAULTSECT` 无法从解析器中被移除:

  - 尝试删除将引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})，
  - `parser.clear()` 会保留其原状，
  - `parser.popitem()` 绝不会将其返回。

- `parser.get(section, option, **kwargs)` - 第二个参数 **并非** 回退值。 但是请注意小节层级的 `get()` 方法可同时兼容映射协议和经典配置解析器 API。

- `parser.items()` 兼容映射协议（返回 *section_name*, *section_proxy* 对的列表，包括 DEFAULTSECT）。 但是，此方法也可以带参数发起调用: `parser.items(section, raw, vars)`。 这种调用形式返回指定 `section` 的 *option*, *value* 对的列表，将展开所有插值（除非提供了 `raw=True` 选项）。

​	映射协议是在现有的传统 API 之上实现的，以便重写原始接口的子类仍然具有符合预期的有效映射。

## 定制解析器行为

​	INI 格式的变种数量几乎和使用此格式的应用一样多。 [`configparser`]({{< ref "/library/fileformats/configparser#module-configparser" >}}) 花费了很大力气来为尽量大范围的可用 INI 样式提供支持。 默认的可用功能主要由历史状况来确定，你很可能会想要定制某些特性。

​	改变特定配置解析器行为的最常见方式是使用 `__init__()` 选项：

- *defaults*，默认值: `None`

  此选项接受一个键值对的字典，它将被首先放入 `DEFAULT` 小节。 这实现了一种优雅的方式来支持简洁的配置文件，它不必指定与已记录的默认值相同的值。

  提示：如果你想要为特定的节指定默认值，请在读取实际文件之前 使用 [`read_dict()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.read_dict" >}})。

- *dict_type*，默认值: [`dict`]({{< ref "/library/stdtypes#dict" >}})

  此选项主要影响映射协议的行为和写入配置文件的外观。 使用标准字典时，每个小节是按照它们被加入解析器的顺序保存的。 在小节内的选项也是如此。

  还有其他替换的字典类型可以使用，例如在写回数据时对小节和选项进行排序。

  请注意：存在其他方式只用一次操作来添加键值对的集合。 当你在这些操作中使用一个常规字典时，键将按顺序进行排列。 例如:

  

  ```
  >>> parser = configparser.ConfigParser()
  >>> parser.read_dict({'section1': {'key1': 'value1',
  ...                                'key2': 'value2',
  ...                                'key3': 'value3'},
  ...                   'section2': {'keyA': 'valueA',
  ...                                'keyB': 'valueB',
  ...                                'keyC': 'valueC'},
  ...                   'section3': {'foo': 'x',
  ...                                'bar': 'y',
  ...                                'baz': 'z'}
  ... })
  >>> parser.sections()
  ['section1', 'section2', 'section3']
  >>> [option for option in parser['section3']]
  ['foo', 'bar', 'baz']
  ```

- *allow_no_value*，默认值: `False`

  已知某些配置文件会包括不带值的设置，但其在其他方面均符合 [`configparser`]({{< ref "/library/fileformats/configparser#module-configparser" >}}) 所支持的语法。 构造器的 *allow_no_value* 形参可用于指明应当接受这样的值:

  

  ```
  >>> import configparser
  
  >>> sample_config = """
  ... [mysqld]
  ...   user = mysql
  ...   pid-file = /var/run/mysqld/mysqld.pid
  ...   skip-external-locking
  ...   old_passwords = 1
  ...   skip-bdb
  ...   # we don't need ACID today
  ...   skip-innodb
  ... """
  >>> config = configparser.ConfigParser(allow_no_value=True)
  >>> config.read_string(sample_config)
  
  >>> # Settings with values are treated as before:
  >>> config["mysqld"]["user"]
  'mysql'
  
  >>> # Settings without values provide None:
  >>> config["mysqld"]["skip-bdb"]
  
  >>> # Settings which aren't specified still raise an error:
  >>> config["mysqld"]["does-not-exist"]
  Traceback (most recent call last):
    ...
  KeyError: 'does-not-exist'
  ```

- *delimiters*，默认值: `('=', ':')`

  分隔符是用于在小节内分隔键和值的子字符串。 在一行中首次出现的分隔子字符串会被视为一个分隔符。 这意味着值可以包含分隔符（但键不可以）。

  另请参见 [`ConfigParser.write()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.write" >}}) 的 *space_around_delimiters* 参数。

- *comment_prefixes*，默认值: `('#', ';')`

- *inline_comment_prefixes*，默认值: `None`

  注释前缀是配置文件中用于标示一条有效注释的开头的字符串。 *comment_prefixes* 仅用在被视为空白的行（可以缩进）之前而 *inline_comment_prefixes* 可用在每个有效值之后（例如小节名称、选项以及空白的行）。 默认情况下禁用行内注释，并且 `'#'` 和 `';'` 都被用作完整行注释的前缀。

  > 在 3.2 版本发生变更: 在之前的 [`configparser`]({{< ref "/library/fileformats/configparser#module-configparser" >}}) 版本中行为匹配 `comment_prefixes=('#',';')` 和 `inline_comment_prefixes=(';',)`。

  请注意配置解析器不支持对注释前缀的转义，因此使用 *inline_comment_prefixes* 可能妨碍用户将被用作注释前缀的字符指定为可选值。 当有疑问时，请避免设置 *inline_comment_prefixes*。 在许多情况下，在多行值的一行开头存储注释前缀字符的唯一方式是进行前缀插值，例如:

  

  ```
  >>> from configparser import ConfigParser, ExtendedInterpolation
  >>> parser = ConfigParser(interpolation=ExtendedInterpolation())
  >>> # the default BasicInterpolation could be used as well
  >>> parser.read_string("""
  ... [DEFAULT]
  ... hash = #
  ...
  ... [hashes]
  ... shebang =
  ...   ${hash}!/usr/bin/env python
  ...   ${hash} -*- coding: utf-8 -*-
  ...
  ... extensions =
  ...   enabled_extension
  ...   another_extension
  ...   #disabled_by_comment
  ...   yet_another_extension
  ...
  ... interpolation not necessary = if # is not at line start
  ... even in multiline values = line #1
  ...   line #2
  ...   line #3
  ... """)
  >>> print(parser['hashes']['shebang'])
  
  #!/usr/bin/env python
  # -*- coding: utf-8 -*-
  >>> print(parser['hashes']['extensions'])
  
  enabled_extension
  another_extension
  yet_another_extension
  >>> print(parser['hashes']['interpolation not necessary'])
  if # is not at line start
  >>> print(parser['hashes']['even in multiline values'])
  line #1
  line #2
  line #3
  ```

- *strict*，默认值: `True`

  当设为 `True` 时，解析器在从单一源读取 (使用 [`read_file()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.read_file" >}}), [`read_string()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.read_string" >}}) 或 [`read_dict()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.read_dict" >}})) 期间将不允许任何节或选项出现重复。 推荐在新的应用中使用严格解析器。

  > 在 3.2 版本发生变更: 在之前的 [`configparser`]({{< ref "/library/fileformats/configparser#module-configparser" >}}) 版本中行为匹配 `strict=False`。

- *empty_lines_in_values*，默认值: `True`

  在配置解析器中，值可以包含多行，只要它们的缩进级别低于它们所对应的键。 默认情况下解析器还会将空行视为值的一部分。 于此同时，键本身也可以任意缩进以提升可读性。 因此，当配置文件变得非常庞大而复杂时，用户很容易失去对文件结构的掌控。 例如:

  ```
  [Section]
  key = multiline
    value with a gotcha
  
   this = is still a part of the multiline value of 'key'
  ```

  在用户查看时这可能会特别有问题，如果她是使用比例字体来编辑文件的话。 这就是为什么当你的应用不需要带有空行的值时，你应该考虑禁用它们。 这将使得空行每次都会作为键之间的分隔。 在上面的示例中，空行产生了两个键，`key` 和 `this`。

- *default_section*，默认值: `configparser.DEFAULTSECT` (即: `"DEFAULT"`)

  允许设置一个保存默认值的特殊节在其他节或插值等目的中使用的惯例是这个库所拥有的一个强大概念，使得用户能够创建复杂的声明性配置。 这种特殊节通常称为 `"DEFAULT"` 但也可以被定制为指向任何其他有效的节名称。 一些典型的值包括: `"general"` 或 `"common"`。 所提供的名称在从任意节读取的时候被用于识别默认的节，而且也会在将配置写回文件时被使用。 它的当前值可以使用 `parser_instance.default_section` 属性来获取，并且可以在运行时被修改（即将文件从一种格式转换为另一种格式）。

- *interpolation*，默认值: `configparser.BasicInterpolation`

  插值行为可以用通过提供 *interpolation* 参数提供自定义处理程序的方式来定制。 `None` 可用来完全禁用插值，`ExtendedInterpolation()` 提供了一种更高级的变体形式，它的设计受到了 `zc.buildout` 的启发。 有关该主题的更多信息请参见 [专门的文档章节]({{< ref "/library/fileformats/configparser#interpolation-of-values" >}})。 [`RawConfigParser`]({{< ref "/library/fileformats/configparser#configparser.RawConfigParser" >}}) 具有默认的值 `None`。

- *converters*，默认值: 不设置

  配置解析器提供了可选的值获取方法用来执行类型转换。 默认情况下实现了 [`getint()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.getint" >}}), [`getfloat()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.getfloat" >}}) 和 [`getboolean()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.getboolean" >}})。 如果还需要其他获取方法，用户可以在子类中定义它们，或者传入一个字典，其中每个键都是一个转换器的名称而每个值都是一个实现了特定转换的可调用对象。 例如，传入 `{'decimal': decimal.Decimal}` 将对解释器对象和所有节代理添加 `getdecimal()`。 换句话说，可以同时编写 `parser_instance.getdecimal('section', 'key', fallback=0)` 和 `parser_instance['section'].getdecimal('key', 0)`。

  如果转换器需要访问解析器的状态，可以在配置解析器子类上作为一个方法来实现。 如果该方法的名称是以 `get` 打头的，它将在所有节代理上以兼容字典的形式提供（参见上面的 `getdecimal()` 示例）。

​	更多高级定制选项可通过重写这些解析器属性的默认值来达成。 默认值是在类中定义的，因此它们可以通过子类或属性赋值来重写。

## ConfigParser.**BOOLEAN_STATES**

​	默认情况下当使用 [`getboolean()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.getboolean" >}}) 时，配置解析器会将下列值视为 `True`: `'1'`, `'yes'`, `'true'`, `'on'` 而将下列值视为 `False`: `'0'`, `'no'`, `'false'`, `'off'`。 你可以通过指定一个自定义的字符串键及其对应的布尔值字典来覆盖此行为。 例如:



``` python
>>> custom = configparser.ConfigParser()
>>> custom['section1'] = {'funky': 'nope'}
>>> custom['section1'].getboolean('funky')
Traceback (most recent call last):
...
ValueError: Not a boolean: nope
>>> custom.BOOLEAN_STATES = {'sure': True, 'nope': False}
>>> custom['section1'].getboolean('funky')
False
```

​	其他典型的布尔值对包括 `accept`/`reject` 或 `enabled`/`disabled`。

## ConfigParser.**optionxform**(*option*)

​	这个方法会转换每次 read, get, 或 set 操作的选项名称。 默认会将名称转换为小写形式。 这也意味着当一个配置文件被写入时，所有键都将为小写形式。 如果此行为不合适则要重写此方法。 例如:



``` python
>>> config = """
... [Section1]
... Key = Value
...
... [Section2]
... AnotherKey = Value
... """
>>> typical = configparser.ConfigParser()
>>> typical.read_string(config)
>>> list(typical['Section1'].keys())
['key']
>>> list(typical['Section2'].keys())
['anotherkey']
>>> custom = configparser.RawConfigParser()
>>> custom.optionxform = lambda option: option
>>> custom.read_string(config)
>>> list(custom['Section1'].keys())
['Key']
>>> list(custom['Section2'].keys())
['AnotherKey']
```

​备注
 

​	optionxform 函数会将选项名称转换为规范形式。 这应该是一个幂等函数：如果名称已经为规范形式，则应不加修改地将其返回。

## ConfigParser.**SECTCRE**

​	一个已编译正则表达式会被用来解析节标头。 默认将 `[section]` 匹配到名称 `"section"`。 空格会被视为节名称的一部分，因此 `[ larch ]` 将被读取为一个名称为 `" larch "` 的节。 如果此行为不合适则要覆盖此属性。 例如:



``` python
>>> import re
>>> config = """
... [Section 1]
... option = value
...
... [  Section 2  ]
... another = val
... """
>>> typical = configparser.ConfigParser()
>>> typical.read_string(config)
>>> typical.sections()
['Section 1', '  Section 2  ']
>>> custom = configparser.ConfigParser()
>>> custom.SECTCRE = re.compile(r"\[ *(?P<header>[^]]+?) *\]")
>>> custom.read_string(config)
>>> custom.sections()
['Section 1', 'Section 2']
```

​备注
 

​	虽然 ConfigParser 对象也使用 `OPTCRE` 属性来识别选项行，但并不推荐重写它，因为这会与构造器选项 *allow_no_value* 和 *delimiters* 产生冲突。

## 旧式 API 示例

​	主要出于向下兼容性的考虑，[`configparser`]({{< ref "/library/fileformats/configparser#module-configparser" >}}) 还提供了一种采用显式 `get`/`set` 方法的旧式 API。 虽然以下介绍的方法存在有效的用例，但对于新项目仍建议采用映射协议访问。 旧式 API 在多数时候都更复杂、更底层并且完全违反直觉。

​	一个写入配置文件的示例:

```
import configparser

config = configparser.RawConfigParser()

# Please note that using RawConfigParser's set functions, you can assign
# non-string values to keys internally, but will receive an error when
# attempting to write to a file or when you get it in non-raw mode. Setting
# values using the mapping protocol or ConfigParser's set() does not allow
# such assignments to take place.
config.add_section('Section1')
config.set('Section1', 'an_int', '15')
config.set('Section1', 'a_bool', 'true')
config.set('Section1', 'a_float', '3.1415')
config.set('Section1', 'baz', 'fun')
config.set('Section1', 'bar', 'Python')
config.set('Section1', 'foo', '%(bar)s is %(baz)s!')

# Writing our configuration file to 'example.cfg'
with open('example.cfg', 'w') as configfile:
    config.write(configfile)
```

​	一个再次读取配置文件的示例:

```
import configparser

config = configparser.RawConfigParser()
config.read('example.cfg')

# getfloat() raises an exception if the value is not a float
# getint() and getboolean() also do this for their respective types
a_float = config.getfloat('Section1', 'a_float')
an_int = config.getint('Section1', 'an_int')
print(a_float + an_int)

# Notice that the next output does not interpolate '%(bar)s' or '%(baz)s'.
# This is because we are using a RawConfigParser().
if config.getboolean('Section1', 'a_bool'):
    print(config.get('Section1', 'foo'))
```

​	要获取插值，请使用 [`ConfigParser`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser" >}}):

```
import configparser

cfg = configparser.ConfigParser()
cfg.read('example.cfg')

# Set the optional *raw* argument of get() to True if you wish to disable
# interpolation in a single get operation.
print(cfg.get('Section1', 'foo', raw=False))  # -> "Python is fun!"
print(cfg.get('Section1', 'foo', raw=True))   # -> "%(bar)s is %(baz)s!"

# The optional *vars* argument is a dict with members that will take
# precedence in interpolation.
print(cfg.get('Section1', 'foo', vars={'bar': 'Documentation',
                                       'baz': 'evil'}))

# The optional *fallback* argument can be used to provide a fallback value
print(cfg.get('Section1', 'foo'))
      # -> "Python is fun!"

print(cfg.get('Section1', 'foo', fallback='Monty is not.'))
      # -> "Python is fun!"

print(cfg.get('Section1', 'monster', fallback='No such things as monsters.'))
      # -> "No such things as monsters."

# A bare print(cfg.get('Section1', 'monster')) would raise NoOptionError
# but we can also use:

print(cfg.get('Section1', 'monster', fallback=None))
      # -> None
```

​	默认值在两种类型的 ConfigParser 中均可用。 它们将在当某个选项未在别处定义时被用于插值。

```
import configparser

# 'bar' 和 'baz' 分别默认为 'Life' 和 'hard' 的新实例
config = configparser.ConfigParser({'bar': 'Life', 'baz': 'hard'})
config.read('example.cfg')

print(config.get('Section1', 'foo'))     # -> "Python is fun!"
config.remove_option('Section1', 'bar')
config.remove_option('Section1', 'baz')
print(config.get('Section1', 'foo'))     # -> "Life is hard!"
```



## ConfigParser 对象

## *class* configparser.**ConfigParser**(*defaults=None*, *dict_type=dict*, *allow_no_value=False*, ***, *delimiters=('=', ':')*, *comment_prefixes=('#', ';')*, *inline_comment_prefixes=None*, *strict=True*, *empty_lines_in_values=True*, *default_section=configparser.DEFAULTSECT*, *interpolation=BasicInterpolation()*, *converters={}*, *allow_unnamed_section=False*)

​	主配置解析器。 当给定 *defaults* 时，它会被初始化为包含固有默认值的字典。 当给定 *dict_type* 时，它将被用来创建包含节、节中的选项以及默认值的字典。

​	当给定 *delimiters* 时，它会被用作分隔键与值的子字符串的集合。 当给定 *comment_prefixes* 时，它将被用作在否则为空行的注释的前缀子字符串的集合。 注释可以被缩进。 当给定 *inline_comment_prefixes* 时，它将被用作非空行的注释的前缀子字符串的集合。

​	当 *strict* 为 `True` (默认值) 时，解析器在从单个源（文件、字符串或字典）读取时将不允许任何节或选项出现重复，否则会引发 [`DuplicateSectionError`]({{< ref "/library/fileformats/configparser#configparser.DuplicateSectionError" >}}) 或 [`DuplicateOptionError`]({{< ref "/library/fileformats/configparser#configparser.DuplicateOptionError" >}})。 当 *empty_lines_in_values* 为 `False` (默认值: `True`) 时，每个空行均表示一个选项的结束。 在其他情况下，一个多行选项内部的空行会被保留为值的一部分。 当 *allow_no_value* 为 `True` (默认值: `False`) 时，将接受没有值的选项；此种选项的值将为 `None` 并且它们会以不带末尾分隔符的形式被序列化。

​	当给出 *default_section* 时，它指定了为其他部分和插值目的而保存默认值的特殊部分的名称 (通常命名为 `"DEFAULT"`)。 该值可通过使用 `default_section` 实例属性在运行时被读取或修改值。 这不会对已解析的配置文件进行重新求值，但会在将解析的设置写入新的配置文件时使用。

​	插值行为可通过给出 *interpolation* 参数提供自定义处理程序的方式来定制。 `None` 可用来完全禁用插值，`ExtendedInterpolation()` 提供了一种更高级的变体形式，它的设计受到了 `zc.buildout` 的启发。 有关该主题的更多信息请参见 [专门的文档章节]({{< ref "/library/fileformats/configparser#interpolation-of-values" >}})。

​	插值中使用的所有选项名称将像任何其他选项名称引用一样通过 [`optionxform()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.optionxform" >}}) 方法来传递。 例如，使用 [`optionxform()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.optionxform" >}}) 的默认实现（它会将选项名称转换为小写形式）时，值 `foo %(bar)s` 和 `foo %(BAR)s` 是等价的。

​	当给出 *converters* 时，它应当是一个字典，其中每个键代表一个类型转换器的名称而每个值则为实现从字符串到目标数据类型的转换的可调用对象。 每个转换器会获得其在解析器对象和节代理上对应的 `get*()` 方法。

​	When *allow_unnamed_section* is `True` (default: `False`), the first section name can be omitted. See the ["Unnamed Sections" section]({{< ref "/library/fileformats/configparser#unnamed-sections" >}}).

​	将多个配置读入单个 [`ConfigParser`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser" >}}) 是可能的，其中最近添加的配置具有最高优先级。 任何冲突的键都会从更近的配置获取并且先前存在的键会被保留。 下面的例子读入一个 `override.ini` 文件，它将覆盖任何来自 `example.ini` 文件的冲突的键。

```
[DEFAULT]
ServerAliveInterval = -1
```



``` python
>>> config_override = configparser.ConfigParser()
>>> config_override['DEFAULT'] = {'ServerAliveInterval': '-1'}
>>> with open('override.ini', 'w') as configfile:
...     config_override.write(configfile)
...
>>> config_override = configparser.ConfigParser()
>>> config_override.read(['example.ini', 'override.ini'])
['example.ini', 'override.ini']
>>> print(config_override.get('DEFAULT', 'ServerAliveInterval'))
-1
```

> 在 3.1 版本发生变更: 默认的 *dict_type* 为 [`collections.OrderedDict`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.OrderedDict)。

> 在 3.2 版本发生变更: 添加了 *allow_no_value*, *delimiters*, *comment_prefixes*, *strict*, *empty_lines_in_values*, *default_section* 以及 *interpolation*。

> 在 3.5 版本发生变更: 添加了 *converters* 参数。

> 在 3.7 版本发生变更: The *defaults* argument is read with [`read_dict()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.read_dict" >}}), providing consistent behavior across the parser: non-string keys and values are implicitly converted to strings.

> 在 3.8 版本发生变更: 默认的 *dict_type* 为 [`dict`]({{< ref "/library/stdtypes#dict" >}})，因为它现在会保留插入顺序。

> 在 3.13 版本发生变更: 当 *allow_no_value* 为 `True` 且没有值的键带有一个缩进的行时将会引发 [`MultilineContinuationError`]({{< ref "/library/fileformats/configparser#configparser.MultilineContinuationError" >}})。

> 在 3.13 版本发生变更: The *allow_unnamed_section* argument was added.

## **defaults**()

​	返回包含实例范围内默认值的字典。

## **sections**()

​	返回可用节的列表；*default section* 不包括在该列表中。

## **add_section**(*section*)

​	向实例添加一个名为 *section* 的节。 如果给定名称的节已存在，将会引发 [`DuplicateSectionError`]({{< ref "/library/fileformats/configparser#configparser.DuplicateSectionError" >}})。 如果传入了 *default section* 名称，则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。 节名称必须为字符串；如果不是则会引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。

> 在 3.2 版本发生变更: 非字符串的节名称将引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。

## **has_section**(*section*)

​	指明相应名称的 *section* 是否存在于配置中。 *default section* 不包含在内。

## **options**(*section*)

​	返回指定 *section* 中可用选项的列表。

## **has_option**(*section*, *option*)

​	如果给定的 *section* 存在并且包含给定的 *option* 则返回 [`True`]({{< ref "/library/constants#True" >}})；否则返回 [`False`]({{< ref "/library/constants#False" >}})。 如果指定的 *section* 为 [`None`]({{< ref "/library/constants#None" >}}) 或空字符串，则会使用 DEFAULT。

## **read**(*filenames*, *encoding=None*)

​	尝试读取并解析一个包含文件名的可迭代对象，返回一个被成功解析的文件名列表。

​	如果 *filenames* 为字符串、[`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 对象或 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})，它会被当作单个文件来处理。 如果 *filenames* 中名称对应的某个文件无法被打开，该文件将被忽略。 这样的设计使得你可以指定包含多个潜在配置文件位置的可迭代对象（例如当前目录、用户家目录以及某个系统级目录），存在于该可迭代对象中的所有配置文件都将被读取。

​	如果名称对应的文件全都不存在，则 [`ConfigParser`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser" >}}) 实例将包含一个空数据集。 一个要求从文件加载初始值的应用应当在调用 [`read()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.read" >}}) 来获取任何可选文件之前使用 [`read_file()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.read_file" >}}) 来加载所要求的一个或多个文件:

```
import configparser, os

config = configparser.ConfigParser()
config.read_file(open('defaults.cfg'))
config.read(['site.cfg', os.path.expanduser('~/.myapp.cfg')],
            encoding='cp1250')
```

> 在 3.2 版本发生变更: 增加了 *encoding* 形参。 在之前版本中，所有文件都将使用 [`open()`]({{< ref "/library/functions#open" >}}) 的默认编码格式来读取。

*在 3.6.1 版本发生变更:* *filenames* 形参接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

> 在 3.7 版本发生变更: *filenames* 形参接受一个 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 对象。

## **read_file**(*f*, *source=None*)

​	从 *f* 读取并解析配置数据，它必须是一个产生 Unicode 字符串的可迭代对象（例如以文本模式打开的文件）。

​	可选参数 *source* 指定要读取的文件名称。 如果未给出并且 *f* 具有 `name` 属性，则该属性会被用作 *source*；默认值为 `'<???>'`。

> Added in version 3.2:
> 替代 `readfp()`。

## **read_string**(*string*, *source='<string>'*)

​	从字符串中解析配置数据。

​	可选参数 *source* 指定一个所传入字符串的上下文专属名称。 如果未给出，则会使用 `'<string>'`。 这通常应为一个文件系统路径或 URL。

> Added in version 3.2.
>

## **read_dict**(*dictionary*, *source='<dict>'*)

​	从任意一个提供了类似于字典的 `items()` 方法的对象加载配置。 键为节名称，值为包含节中所出现的键和值的字典。 如果所用的字典类型会保留顺序，则节和其中的键将按顺序加入。 值会被自动转换为字符串。

​	可选参数 *source* 指定一个所传入字典的上下文专属名称。 如果未给出，则会使用 `<dict>`。

​	此方法可被用于在解析器之间拷贝状态。

> Added in version 3.2.
>

## **get**(*section*, *option*, ***, *raw=False*, *vars=None*[, *fallback*])

​	获取指定名称的 *section* 的一个 *option* 的值。 如果提供了 *vars*，则它必须为一个字典。 *option* 的查找顺序为 *vars\*（如果有提供）、\*section* 以及 *DEFAULTSECT*。 如果未找到该键并且提供了 *fallback*，则它会被用作回退值。 可以提供 `None` 作为 *fallback* 值。

​	所有 `'%'` 插值会在返回值中被展开，除非 *raw* 参数为真值。 插值键所使用的值会按与选项相同的方式来查找。

> 在 3.2 版本发生变更: *raw*, *vars* 和 *fallback* 都是仅限关键字参数，以防止用户试图使用第三个参数作业为 *fallback* 回退值（特别是在使用映射 协议的时候）。

## **getint**(*section*, *option*, ***, *raw=False*, *vars=None*[, *fallback*])

​	将在指定 *section* 中的 *option* 强制转换为整数的便捷方法。 参见 [`get()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.get" >}}) 获取对于 *raw*, *vars* 和 *fallback* 的解释。

## **getfloat**(*section*, *option*, ***, *raw=False*, *vars=None*[, *fallback*])

​	将在指定 *section* 中的 *option* 强制转换为浮点数的便捷方法。 参见 [`get()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.get" >}}) 获取对于 *raw*, *vars* 和 *fallback* 的解释。

## **getboolean**(*section*, *option*, ***, *raw=False*, *vars=None*[, *fallback*])

​	将在指定 *section* 中的 *option* 强制转换为布尔值的便捷方法。 请注意选项所接受的值为 `'1'`, `'yes'`, `'true'` 和 `'on'`，它们会使得此方法返回 `True`，以及 `'0'`, `'no'`, `'false'` 和 `'off'`，它们会使得此方法返回 `False`。 这些字符串值会以对大小写不敏感的方式被检测。 任何其他值都将导致引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。 参见 [`get()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.get" >}}) 获取对于 *raw*, *vars* 和 *fallback* 的解释。

## **items**(*raw=False*, *vars=None*)

## **items**(*section*, *raw=False*, *vars=None*)

​	当未给出 *section* 时，将返回由 *section_name*, *section_proxy* 对组成的列表，包括 DEFAULTSECT。

​	在其他情况下，将返回给定的 *section* 中的 option 的 *name*, *value* 对组成的列表。 可选参数具有与 [`get()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.get" >}}) 方法的参数相同的含义。

> 在 3.8 版本发生变更: *vars* 中的条目将不在结果中出现。 之前的行为混淆了实际的解析器选项和为插值提供的变量。

## **set**(*section*, *option*, *value*)

​	如果给定的节存在，则将所给出的选项设为指定的值；在其他情况下将引发 [`NoSectionError`]({{< ref "/library/fileformats/configparser#configparser.NoSectionError" >}})。 *option* 和 *value* 必须为字符串；如果不是则将引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。

## **write**(*fileobject*, *space_around_delimiters=True*)

​	将配置的表示形式写入指定的 [file object]({{< ref "/glossary/idx#term-file-object" >}})，该对象必须以文本模式打开（接受字符串）。 此表示形式可由将来的 [`read()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.read" >}}) 调用进行解析。 如果 *space_around_delimiters* 为真值，键和值之前的分隔符两边将加上空格。

​备注
 

​	原始配置文件中的注释在写回配置时不会被保留。 具体哪些会被当作注释，取决于为 *comment_prefix* 和 *inline_comment_prefix* 所指定的值。

## **remove_option**(*section*, *option*)

​	将指定的 *option* 从指定的 *section* 中移除。 如果指定的节不存在则会引发 [`NoSectionError`]({{< ref "/library/fileformats/configparser#configparser.NoSectionError" >}})。 如果要移除的选项存在则返回 [`True`]({{< ref "/library/constants#True" >}})；在其他情况下将返回 [`False`]({{< ref "/library/constants#False" >}})。

## **remove_section**(*section*)

​	从配置中移除指定的 *section*。 如果指定的节确实存在则返回 `True`。 在其他情况下将返回 `False`。

## **optionxform**(*option*)

​	将选项名 *option* 转换为输入文件中的形式或客户端代码所传入的应当在内部结构中使用的形式。 默认实现将返回 *option* 的小写形式版本；子类可以重写此行为，或者客户端代码也可以在实例上设置一个具有此名称的属性来影响此行为。

​	你不需要子类化解析器来使用此方法，你也可以在一个实例上设置它，或使用一个接受字符串参数并返回字符串的函数。 例如将它设为 `str` 将使得选项名称变得大小写敏感:

```
cfgparser = ConfigParser()
cfgparser.optionxform = str
```

​	请注意当读取配置文件时，选项名称两边的空格将在调用 [`optionxform()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.optionxform" >}}) 之前被去除。

## configparser.**UNNAMED_SECTION**

​	一个代表用于引用未命名小节的小节名称的特殊对象 (参见 [未命名小节]({{< ref "/library/fileformats/configparser#unnamed-sections" >}}))。

## configparser.**MAX_INTERPOLATION_DEPTH**

​	当 *raw* 形参为假值时 [`get()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.get" >}}) 所采用的递归插值的最大深度。 这只在使用默认的 *interpolation* 时会起作用。



## RawConfigParser 对象

## *class* configparser.**RawConfigParser**(*defaults=None*, *dict_type=dict*, *allow_no_value=False*, ***, *delimiters=('=', ':')*, *comment_prefixes=('#', ';')*, *inline_comment_prefixes=None*, *strict=True*, *empty_lines_in_values=True*, *default_section=configparser.DEFAULTSECT*, *interpolation=BasicInterpolation()*, *converters={}*, *allow_unnamed_section=False*)

​	旧式 [`ConfigParser`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser" >}})。 它默认禁用插值并且允许通过不安全的 `add_section` 和 `set` 方法以及旧式 `defaults=` 关键字参数处理来设置非字符串的节名、选项名和值。

> 在 3.2 版本发生变更: 添加了 *allow_no_value*, *delimiters*, *comment_prefixes*, *strict*, *empty_lines_in_values*, *default_section* 以及 *interpolation*。

> 在 3.5 版本发生变更: 添加了 *converters* 参数。

> 在 3.8 版本发生变更: 默认的 *dict_type* 为 [`dict`]({{< ref "/library/stdtypes#dict" >}})，因为它现在会保留插入顺序。

> 在 3.13 版本发生变更: The *allow_unnamed_section* argument was added.

​备注
 

​	考虑改用 [`ConfigParser`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser" >}})，它会检查内部保存的值的类型。 如果你不想要插值，你可以使用 `ConfigParser(interpolation=None)`。

## **add_section**(*section*)

​	向实例添加一个名为 *section* 的节。 如果给定名称的节已存在，将会引发 [`DuplicateSectionError`]({{< ref "/library/fileformats/configparser#configparser.DuplicateSectionError" >}})。 如果传入了 *default section* 名称，则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

​	不检查 *section* 以允许用户创建以非字符串命名的节。 此行为已不受支持并可能导致内部错误。

## **set**(*section*, *option*, *value*)

​	如果给定的节存在，则将给定的选项设为指定的值；在其他情况下将引发 [`NoSectionError`]({{< ref "/library/fileformats/configparser#configparser.NoSectionError" >}})。 虽然可能使用 [`RawConfigParser`]({{< ref "/library/fileformats/configparser#configparser.RawConfigParser" >}}) (或使用 [`ConfigParser`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser" >}}) 并将 *raw* 形参设为真值) 以便实现非字符串值的 *internal* 存储，但是完整功能（包括插值和输出到文件）只能使用字符串值来实现。

​	此方法允许用户在内部将非字符串值赋给键。 此行为已不受支持并会在尝试写入到文件或在非原始模式下获取数据时导致错误。 **请使用映射协议 API**，它不允许出现这样的赋值。

## 异常

## *exception* configparser.**Error**

​	所有其他 [`configparser`]({{< ref "/library/fileformats/configparser#module-configparser" >}}) 异常的基类。

## *exception* configparser.**NoSectionError**

​	当找不到指定节时引发的异常。

## *exception* configparser.**DuplicateSectionError**

​	当调用 [`add_section()`]({{< ref "/library/fileformats/configparser#configparser.ConfigParser.add_section" >}}) 时传入已存在的节名称，或者在严格解析器中当单个输入文件、字符串或字典内出现重复的节时引发的异常。

> 在 3.2 版本发生变更: 向 `__init__()` 添加了可选的 *source* 和 *lineno* 属性和形参。

## *exception* configparser.**DuplicateOptionError**

​	当单个选项在从单个文件、字符串或字典读取时出现两次时引发的异常。 这会捕获拼写错误和大小写敏感相关的错误，例如一个字典可能包含两个键分别代表同一个大小写不敏感的配置键。

## *exception* configparser.**NoOptionError**

​	当指定的选项未在指定的节中被找到时引发的异常。

## *exception* configparser.**InterpolationError**

​	当执行字符串插值发生问题时所引发的异常的基类。

## *exception* configparser.**InterpolationDepthError**

​	当字符串插值由于迭代次数超出 [`MAX_INTERPOLATION_DEPTH`]({{< ref "/library/fileformats/configparser#configparser.MAX_INTERPOLATION_DEPTH" >}}) 而无法完成所引发的异常。 为 [`InterpolationError`]({{< ref "/library/fileformats/configparser#configparser.InterpolationError" >}}) 的子类。

## *exception* configparser.**InterpolationMissingOptionError**

​	当从某个值引用的选项并不存在时引发的异常。 为 [`InterpolationError`]({{< ref "/library/fileformats/configparser#configparser.InterpolationError" >}}) 的子类。

## *exception* configparser.**InterpolationSyntaxError**

​	当将要执行替换的源文本不符合要求的语法时引发的异常。 为 [`InterpolationError`]({{< ref "/library/fileformats/configparser#configparser.InterpolationError" >}}) 的子类。

## *exception* configparser.**MissingSectionHeaderError**

​	当尝试解析一个不带节标头的文件时引发的异常。

## *exception* configparser.**ParsingError**

​	当尝试解析一个文件而发生错误时引发的异常。

> 在 3.12 版本发生变更: `filename` 属性和 `__init__()` 构造器参数已被移除。 它们自 3.2 起可以使用名称 `source` 来访问。

## *exception* configparser.**MultilineContinuationError**

​	当没有对应值的键带有一个缩进的行时将引发的异常。

> Added in version 3.13.
>

​备注
[1]([1](https://docs.python.org/zh-cn/3.13/library/configparser.html#id1),[2]({{< ref "/library/fileformats/configparser#id2" >}}),[3]({{< ref "/library/fileformats/configparser#id3" >}}),[4]({{< ref "/library/fileformats/configparser#id4" >}}),[5]({{< ref "/library/fileformats/configparser#id5" >}}),[6]({{< ref "/library/fileformats/configparser#id6" >}}),[7]({{< ref "/library/fileformats/configparser#id7" >}}),[8]({{< ref "/library/fileformats/configparser#id8" >}}),[9]({{< ref "/library/fileformats/configparser#id9" >}}),[10]({{< ref "/library/fileformats/configparser#id11" >}}),[11]({{< ref "/library/fileformats/configparser#id12" >}}))

​	配置解析器允许重度定制。 如果你有兴趣改变脚注说明中所介绍的行为，请参阅 [Customizing Parser Behaviour]({{< ref "/library/fileformats/configparser#customizing-parser-behaviour" >}}) 一节。
