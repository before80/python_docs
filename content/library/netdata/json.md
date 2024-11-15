+++
title = "json --- JSON 编码器和解码器"
date = 2024-11-15T14:53:40+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/json.html](https://docs.python.org/zh-cn/3.13/library/json.html)
>
> 收录该文档的时间：`2024-11-15T14:53:40+08:00`

# `json` --- JSON 编码器和解码器

**源代码:** [Lib/json/__init__.py](https://github.com/python/cpython/tree/3.13/Lib/json/__init__.py)

------

[JSON (JavaScript Object Notation)](https://json.org/)，由 [**RFC 7159**](https://datatracker.ietf.org/doc/html/rfc7159.html) (它取代了 [**RFC 4627**](https://datatracker.ietf.org/doc/html/rfc4627.html)) 和 [ECMA-404](https://ecma-international.org/publications-and-standards/standards/ecma-404/) 进行规范，是一个受到 [JavaScript](https://en.wikipedia.org/wiki/JavaScript) 对象字面值语法启发的轻量级数据交换格式 (虽然它并不是严格意义上的 JavaScript 子集 [[1\]](https://docs.python.org/zh-cn/3.13/library/json.html#rfc-errata) )。

​	警告

 

​	在解析来自不受信任恶劣的 JSON 数据时要小心谨慎。 恶意的 JSON 字符串可能导致解码器消耗大量 CPU 和内存资源。 建议对要解析的数据大小进行限制。

[`json`](https://docs.python.org/zh-cn/3.13/library/json.html#module-json) 提供了与标准库 [`marshal`](https://docs.python.org/zh-cn/3.13/library/marshal.html#module-marshal) 和 [`pickle`](https://docs.python.org/zh-cn/3.13/library/pickle.html#module-pickle) 相似的API接口。

​	对基本的 Python 对象层次结构进行编码：



```
>>> import json
>>> json.dumps(['foo', {'bar': ('baz', None, 1.0, 2)}])
'["foo", {"bar": ["baz", null, 1.0, 2]}]'
>>> print(json.dumps("\"foo\bar"))
"\"foo\bar"
>>> print(json.dumps('\u1234'))
"\u1234"
>>> print(json.dumps('\\'))
"\\"
>>> print(json.dumps({"c": 0, "b": 0, "a": 0}, sort_keys=True))
{"a": 0, "b": 0, "c": 0}
>>> from io import StringIO
>>> io = StringIO()
>>> json.dump(['streaming API'], io)
>>> io.getvalue()
'["streaming API"]'
```

​	紧凑编码：



```
>>> import json
>>> json.dumps([1, 2, 3, {'4': 5, '6': 7}], separators=(',', ':'))
'[1,2,3,{"4":5,"6":7}]'
```

​	美化输出：



```
>>> import json
>>> print(json.dumps({'6': 7, '4': 5}, sort_keys=True, indent=4))
{
    "4": 5,
    "6": 7
}
```

​	对 JSON 对象解码的特殊化:



```
>>> import json
>>> def custom_json(obj):
...     if isinstance(obj, complex):
...         return {'__complex__': True, 'real': obj.real, 'imag': obj.imag}
...     raise TypeError(f'Cannot serialize object of {type(obj)}')
...
>>> json.dumps(1 + 2j, default=custom_json)
'{"__complex__": true, "real": 1.0, "imag": 2.0}'
```

​	JSON解码：



```
>>> import json
>>> json.loads('["foo", {"bar":["baz", null, 1.0, 2]}]')
['foo', {'bar': ['baz', None, 1.0, 2]}]
>>> json.loads('"\\"foo\\bar"')
'"foo\x08ar'
>>> from io import StringIO
>>> io = StringIO('["streaming API"]')
>>> json.load(io)
['streaming API']
```

​	特殊 JSON 对象解码：



```
>>> import json
>>> def as_complex(dct):
...     if '__complex__' in dct:
...         return complex(dct['real'], dct['imag'])
...     return dct
...
>>> json.loads('{"__complex__": true, "real": 1, "imag": 2}',
...     object_hook=as_complex)
(1+2j)
>>> import decimal
>>> json.loads('1.1', parse_float=decimal.Decimal)
Decimal('1.1')
```

​	扩展 [`JSONEncoder`](https://docs.python.org/zh-cn/3.13/library/json.html#json.JSONEncoder)：



```
>>> import json
>>> class ComplexEncoder(json.JSONEncoder):
...     def default(self, obj):
...         if isinstance(obj, complex):
...             return [obj.real, obj.imag]
...         # Let the base class default method raise the TypeError
...         return super().default(obj)
...
>>> json.dumps(2 + 1j, cls=ComplexEncoder)
'[2.0, 1.0]'
>>> ComplexEncoder().encode(2 + 1j)
'[2.0, 1.0]'
>>> list(ComplexEncoder().iterencode(2 + 1j))
['[2.0', ', 1.0', ']']
```

​	从命令行使用 [`json.tool`](https://docs.python.org/zh-cn/3.13/library/json.html#module-json.tool) 来验证并美化输出：

```
$ echo '{"json":"obj"}' | python -m json.tool
{
    "json": "obj"
}
$ echo '{1.2:3.4}' | python -m json.tool
Expecting property name enclosed in double quotes: line 1 column 2 (char 1)
```

​	详细文档请参见 [CLI](https://docs.python.org/zh-cn/3.13/library/json.html#json-commandline)。

​	备注

 

​	JSON 是 [YAML](https://yaml.org/) 1.2 的一个子集。 由该模块的默认设置所产生的 JSON（尤其是默认的 *separators* 值）也是 YAML 1.0 和 1.1 的一个子集。 因此该模块也能被用作 YAML 序列化器。

​	备注

 

​	这个模块的编码器和解码器默认保护输入和输出的顺序。仅当底层的容器未排序时才会失去顺序。

## 基本使用

## json.**dump**(*obj*, *fp*, ***, *skipkeys=False*, *ensure_ascii=True*, *check_circular=True*, *allow_nan=True*, *cls=None*, *indent=None*, *separators=None*, *default=None*, *sort_keys=False*, ***kw*)

​	使用这个 [转换表](https://docs.python.org/zh-cn/3.13/library/json.html#py-to-json-table) 将 *obj* 序列化为 JSON 格式化流形式的 *fp* (支持 `.write()` 的 [file-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-like-object))。

​	如果 *skipkeys* 是 true （默认为 `False`），那么那些不是基本对象（包括 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str), [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int)、[`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float)、[`bool`](https://docs.python.org/zh-cn/3.13/library/functions.html#bool)、`None`）的字典的键会被跳过；否则引发一个 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。

[`json`](https://docs.python.org/zh-cn/3.13/library/json.html#module-json) 模块始终产生 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 对象而非 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 对象。因此，`fp.write()` 必须支持 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 输入。

​	如果 *ensure_ascii* 是 true （即默认值），输出保证将所有输入的非 ASCII 字符转义。如果 *ensure_ascii* 是 false，这些字符会原样输出。

​	如果 *check_circular* 为假值 (默认值: `True`)，那么容器类型的循环引用检查会被跳过并且循环引用会引发 [`RecursionError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#RecursionError) (或者更糟的情况)。

​	如果 *allow_nan* 是 false（默认为 `True`），那么在对严格 JSON 规格范围外的 [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float) 类型值（`nan`、`inf` 和 `-inf`）进行序列化时会引发一个 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。如果 *allow_nan* 是 true，则使用它们的 JavaScript 等价形式（`NaN`、`Infinity` 和 `-Infinity`）。

​	如果 *indent* 是一个非负整数或者字符串，那么 JSON 数组元素和对象成员会被美化输出为该值指定的缩进等级。 如果缩进等级为零、负数或者 `""`，则只会添加换行符。 `None` (默认值) 选择最紧凑的表达。 使用一个正整数会让每一层缩进同样数量的空格。 如果 *indent* 是一个字符串 (比如 `"\t"`)，那个字符串会被用于缩进每一层。

*在 3.2 版本发生变更:* 现允许使用字符串作为 *indent* 而不再仅仅是整数。

​	当被指定时，*separators* 应当是一个 `(item_separator, key_separator)` 元组。当 *indent* 为 `None` 时，默认值取 `(', ', ': ')`，否则取 `(',', ': ')`。为了得到最紧凑的 JSON 表达式，你应该指定其为 `(',', ':')` 以消除空白字符。

*在 3.4 版本发生变更:* 现当 *indent* 不是 `None` 时，采用 `(',', ': ')` 作为默认值。

​	当 *default* 被指定时，其应该是一个函数，每当某个对象无法被序列化时它会被调用。它应该返回该对象的一个可以被 JSON 编码的版本或者引发一个 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。如果没有被指定，则会直接引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。

​	如果 *sort_keys* 是 true（默认为 `False`），那么字典的输出会以键的顺序排序。

​	要使用自定义的 [`JSONEncoder`](https://docs.python.org/zh-cn/3.13/library/json.html#json.JSONEncoder) 子类（例如重写了 [`default()`](https://docs.python.org/zh-cn/3.13/library/json.html#json.JSONEncoder.default) 方法来序列化额外类型），请使用 *cls* 关键字参数来指定它；否则将使用 [`JSONEncoder`](https://docs.python.org/zh-cn/3.13/library/json.html#json.JSONEncoder)。

*在 3.6 版本发生变更:* 所有可选形参现在都是 [仅限关键字参数](https://docs.python.org/zh-cn/3.13/glossary.html#keyword-only-parameter)。

​	备注

 

​	与 [`pickle`](https://docs.python.org/zh-cn/3.13/library/pickle.html#module-pickle) 和 [`marshal`](https://docs.python.org/zh-cn/3.13/library/marshal.html#module-marshal) 不同，JSON 不是一个具有框架的协议，所以尝试多次使用同一个 *fp* 调用 [`dump()`](https://docs.python.org/zh-cn/3.13/library/json.html#json.dump) 来序列化多个对象会产生一个不合规的 JSON 文件。

## json.**dumps**(*obj*, ***, *skipkeys=False*, *ensure_ascii=True*, *check_circular=True*, *allow_nan=True*, *cls=None*, *indent=None*, *separators=None*, *default=None*, *sort_keys=False*, ***kw*)

​	使用这个 [转换表](https://docs.python.org/zh-cn/3.13/library/json.html#py-to-json-table) 将 *obj* 序列化为 JSON 格式的 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)。 其参数的含义与 [`dump()`](https://docs.python.org/zh-cn/3.13/library/json.html#json.dump) 中的相同。

​	备注

 

​	JSON 中的键-值对中的键永远是 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 类型的。当一个对象被转化为 JSON 时，字典中所有的键都会被强制转换为字符串。这所造成的结果是字典被转换为 JSON 然后转换回字典时可能和原来的不相等。换句话说，如果 x 具有非字符串的键，则有 `loads(dumps(x)) != x`。

## json.**load**(*fp*, ***, *cls=None*, *object_hook=None*, *parse_float=None*, *parse_int=None*, *parse_constant=None*, *object_pairs_hook=None*, ***kw*)

​	使用这个 [转换表](https://docs.python.org/zh-cn/3.13/library/json.html#json-to-py-table) 将 *fp* (一个支持 `.read()` 并包含一个 JSON 文档的 [text file](https://docs.python.org/zh-cn/3.13/glossary.html#term-text-file) 或者 [binary file](https://docs.python.org/zh-cn/3.13/glossary.html#term-binary-file)) 反序列化为一个 Python 对象。

*object_hook* 是一个将附带任意已解码的对象字面值 (即 [`dict`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#dict)) 来调用的可选函数。 *object_hook* 的返回值会代替 [`dict`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#dict) 使用。 此特性可被用于实现自定义解码器 (例如 [JSON-RPC](https://www.jsonrpc.org/) 的类型提示)。

*object_pairs_hook* 是一个可选的函数，它被调用时将传入任意对象字面值以一个对照值的有序列表进行解码的结果作为参数。 *object_pairs_hook* 的返回值将代替原本的 [`dict`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#dict)。 此特性可被用于实现自定义解码器。 如果还定义了 *object_hook*，则 *object_pairs_hook* 的优先级更高。

*在 3.1 版本发生变更:* 添加了对 *object_pairs_hook* 的支持。

*parse_float* 是一个可选的函数，它被调用时将传入每个要解码的 JSON 浮点数。 默认情况下，这相当于 `float(num_str)`。 它可被用于 JSON 浮点数使用其他类型或解析器的情况 (比如 [`decimal.Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal))。

*parse_int* 是一个可选的函数，它被调用时将传入每个要解码的 JSON 整数。 默认情况下，这相当于 `int(num_str)`。 它可被用于 JSON 整数使用其他数据类型或解析器的情况 (比如 [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float))。

*在 3.11 版本发生变更:* 现在 [`int()`](https://docs.python.org/zh-cn/3.13/library/functions.html#int) 默认的 *parse_int* 会通过解释器的 [整数字符串长度上限](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#int-max-str-digits) 来限制整数字符串的最大长度以帮助避免拒绝服务攻击。

*parse_constant* 是一个可选的函数，它被调用时将传入以下字符串之一: `'-Infinity'`, `'Infinity'`, `'NaN'`。 它可被用于当遇到无效的 JSON 数字时引发一个异常。

*在 3.1 版本发生变更:* *parse_constant* 不再调用 'null' ， 'true' ， 'false' 。

​	要使用自定义的 [`JSONDecoder`](https://docs.python.org/zh-cn/3.13/library/json.html#json.JSONDecoder) 子类，用 `cls` 指定他；否则使用 [`JSONDecoder`](https://docs.python.org/zh-cn/3.13/library/json.html#json.JSONDecoder) 。额外的关键词参数会通过类的构造函数传递。

​	如果反序列化的数据不是有效 JSON 文档，引发 [`JSONDecodeError`](https://docs.python.org/zh-cn/3.13/library/json.html#json.JSONDecodeError) 错误。

*在 3.6 版本发生变更:* 所有可选形参现在都是 [仅限关键字参数](https://docs.python.org/zh-cn/3.13/glossary.html#keyword-only-parameter)。

*在 3.6 版本发生变更:* *fp* 现在可以是 [binary file](https://docs.python.org/zh-cn/3.13/glossary.html#term-binary-file) 。输入编码应当是 UTF-8 ， UTF-16 或者 UTF-32 。

## json.**loads**(*s*, ***, *cls=None*, *object_hook=None*, *parse_float=None*, *parse_int=None*, *parse_constant=None*, *object_pairs_hook=None*, ***kw*)

​	使用这个 [转换表](https://docs.python.org/zh-cn/3.13/library/json.html#json-to-py-table) 将 *s* (一个包含 JSON 文档的 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str), [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 或 [`bytearray`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytearray) 实例) 反序列化为 Python 对象。

​	其他参数的含义与 [`load()`](https://docs.python.org/zh-cn/3.13/library/json.html#json.load) 中的相同。

​	如果反序列化的数据不是有效 JSON 文档，引发 [`JSONDecodeError`](https://docs.python.org/zh-cn/3.13/library/json.html#json.JSONDecodeError) 错误。

*在 3.6 版本发生变更:* *s* 现在可以为 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 或 [`bytearray`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytearray) 类型。 输入编码应为 UTF-8, UTF-16 或 UTF-32。

*在 3.9 版本发生变更:* 关键字参数 *encoding* 已被移除。

## 编码器和解码器

## *class* json.**JSONDecoder**(***, *object_hook=None*, *parse_float=None*, *parse_int=None*, *parse_constant=None*, *strict=True*, *object_pairs_hook=None*)

​	简单的JSON解码器。

​	默认情况下，解码执行以下翻译:

| JSON           | Python       |
| :------------- | :----------- |
| object -- 对象 | dict         |
| array          | list -- 列表 |
| string         | str          |
| number (int)   | int          |
| number (real)  | float        |
| true           | True         |
| false          | False        |
| null           | None         |

​	它还将“NaN”、“Infinity”和“-Infinity”理解为它们对应的“float”值，这超出了JSON规范。

*object_hook* 是一个可选的函数，它被调用时将传入每个要解码的 JSON 对象而其返回值将被用来替代给定的 [`dict`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#dict)。 它可被用于提供自定义的反序列化操作 (比如支持 [JSON-RPC](https://www.jsonrpc.org/) 类提示)。

*object_pairs_hook* 是一个可选的函数，它被调用时将传入每个以一个对照值的有序列表进行解码的 JSON 对象的结果。 *object_pairs_hook* 的返回值将被用于代替原本的 [`dict`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#dict)。 此特性可被用于实现自定义的解码器。 如果还定义了 *object_hook*，则 *object_pairs_hook* 的优先级更高。

*在 3.1 版本发生变更:* 添加了对 *object_pairs_hook* 的支持。

*parse_float* 是一个可选的函数，它被调用时将传入每个要解码的 JSON 浮点数。 默认情况下，这相当于 `float(num_str)`。 它可被用于 JSON 浮点数使用其他类型或解析器的情况 (比如 [`decimal.Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal))。

*parse_int* 是一个可选的函数，它被调用时将传入每个要解码的 JSON 整数。 默认情况下，这相当于 `int(num_str)`。 它可被用于 JSON 整数使用其他数据类型或解析器的情况 (比如 [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float))。

*parse_constant* 是一个可选的函数，它被调用时将传入以下字符串之一: `'-Infinity'`, `'Infinity'`, `'NaN'`。 它可被用于当遇到无效的 JSON 数字时引发一个异常。

​	如果 *strict* 为 false （默认为 `True` ），那么控制字符将被允许在字符串内。在此上下文中的控制字符编码在范围 0--31 内的字符，包括 `'\t'` (制表符）， `'\n'` ， `'\r'` 和 `'\0'` 。

​	如果反序列化的数据不是有效 JSON 文档，引发 [`JSONDecodeError`](https://docs.python.org/zh-cn/3.13/library/json.html#json.JSONDecodeError) 错误。

*在 3.6 版本发生变更:* 所有形参现在都是 [仅限关键字参数](https://docs.python.org/zh-cn/3.13/glossary.html#keyword-only-parameter)。

## **decode**(*s*)

​	返回 *s* 的 Python 表示形式（包含一个 JSON 文档的 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 实例）。

​	如果给定的 JSON 文档无效则将引发 [`JSONDecodeError`](https://docs.python.org/zh-cn/3.13/library/json.html#json.JSONDecodeError)。

## **raw_decode**(*s*)

​	从 *s* 中解码出 JSON 文档（以 JSON 文档开头的一个 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 对象）并返回一个 Python 表示形式为 2 元组以及指明该文档在 *s* 中结束位置的序号。

​	这可以用于从一个字符串解码JSON文档，该字符串的末尾可能有无关的数据。

## *class* json.**JSONEncoder**(***, *skipkeys=False*, *ensure_ascii=True*, *check_circular=True*, *allow_nan=True*, *sort_keys=False*, *indent=None*, *separators=None*, *default=None*)

​	用于Python数据结构的可扩展JSON编码器。

​	默认支持以下对象和类型：

| Python                              | JSON           |
| :---------------------------------- | :------------- |
| dict                                | object -- 对象 |
| list, tuple                         | array          |
| str                                 | string         |
| int, float, int 和 float 派生的枚举 | number         |
| True                                | true           |
| False                               | false          |
| None                                | null           |

*在 3.4 版本发生变更:* 添加了对 int 和 float 派生的枚举类的支持

​	要将其扩展至识别其他对象，需要子类化并实现 [`default()`](https://docs.python.org/zh-cn/3.13/library/json.html#json.JSONEncoder.default)，如果可能还要实现另一个返回 `o` 的可序列化对象的方法，否则它应当调用超类实现 (来引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError))。

​	如果 *skipkeys* 为假值（默认），则当尝试对非 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str), [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int), [`float`](https://docs.python.org/zh-cn/3.13/library/functions.html#float) 或 `None` 的键进行编码时将会引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。 如果 *skipkeys* 为真值，这些条目将被直接跳过。

​	如果 *ensure_ascii* 是 true （即默认值），输出保证将所有输入的非 ASCII 字符转义。如果 *ensure_ascii* 是 false，这些字符会原样输出。

​	如果 *check_circular* 为真值（默认），那么列表、字典和自定义的已编码对象将在编码期间进行循环引用检查以防止无限递归 (无限递归会导致 [`RecursionError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#RecursionError))。 在其他情况下，将不会进行这种检查。

​	如果 *allow_nan* 为 true （默认），那么 `NaN` ， `Infinity` ，和 `-Infinity` 进行编码。此行为不符合 JSON 规范，但与大多数的基于 Javascript 的编码器和解码器一致。否则，它将是一个 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError) 来编码这些浮点数。

​	如果 *sort_keys* 为 true （默认为： `False` ），那么字典的输出是按照键排序；这对回归测试很有用，以确保可以每天比较 JSON 序列化。

​	如果 *indent* 是一个非负整数或者字符串，那么 JSON 数组元素和对象成员会被美化输出为该值指定的缩进等级。 如果缩进等级为零、负数或者 `""`，则只会添加换行符。 `None` (默认值) 选择最紧凑的表达。 使用一个正整数会让每一层缩进同样数量的空格。 如果 *indent* 是一个字符串 (比如 `"\t"`)，那个字符串会被用于缩进每一层。

*在 3.2 版本发生变更:* 现允许使用字符串作为 *indent* 而不再仅仅是整数。

​	当被指定时，*separators* 应当是一个 `(item_separator, key_separator)` 元组。当 *indent* 为 `None` 时，默认值取 `(', ', ': ')`，否则取 `(',', ': ')`。为了得到最紧凑的 JSON 表达式，你应该指定其为 `(',', ':')` 以消除空白字符。

*在 3.4 版本发生变更:* 现当 *indent* 不是 `None` 时，采用 `(',', ': ')` 作为默认值。

​	当 *default* 被指定时，其应该是一个函数，每当某个对象无法被序列化时它会被调用。它应该返回该对象的一个可以被 JSON 编码的版本或者引发一个 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。如果没有被指定，则会直接引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。

*在 3.6 版本发生变更:* 所有形参现在都是 [仅限关键字参数](https://docs.python.org/zh-cn/3.13/glossary.html#keyword-only-parameter)。

## **default**(*o*)

​	在子类中实现这种方法使其返回 *o* 的可序列化对象，或者调用基础实现（引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError) ）。

​	例如，要支持任意的迭代器，你可以这样实现 [`default()`](https://docs.python.org/zh-cn/3.13/library/json.html#json.JSONEncoder.default):

```
def default(self, o):
   try:
       iterable = iter(o)
   except TypeError:
       pass
   else:
       return list(iterable)
   # 让基类的 default 方法引发 TypeError
   return super().default(o)
```

## **encode**(*o*)

​	返回 Python *o* 数据结构的 JSON 字符串表达方式。例如:



```
>>> json.JSONEncoder().encode({"foo": ["bar", "baz"]})
'{"foo": ["bar", "baz"]}'
```

## **iterencode**(*o*)

​	编码给定对象 *o* ，并且让每个可用的字符串表达方式。例如:

```
for chunk in json.JSONEncoder().iterencode(bigobject):
    mysocket.write(chunk)
```

## 异常

## *exception* json.**JSONDecodeError**(*msg*, *doc*, *pos*)

​	拥有以下附加属性的 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError) 的子类：

## **msg**

​	未格式化的错误消息。

## **doc**

​	正在解析的 JSON 文档。

## **pos**

​	The start index of *doc* where parsing failed.

## **lineno**

​	The line corresponding to *pos*.

## **colno**

​	The column corresponding to *pos*.

*Added in version 3.5.*

## 标准符合性和互操作性

​	JSON 格式由 [**RFC 7159**](https://datatracker.ietf.org/doc/html/rfc7159.html) 和 [ECMA-404](https://ecma-international.org/publications-and-standards/standards/ecma-404/) 进行规范说明。 这一节详细介绍了本模块与 RFC 的相符程度级别。 简单起见，[`JSONEncoder`](https://docs.python.org/zh-cn/3.13/library/json.html#json.JSONEncoder) 和 [`JSONDecoder`](https://docs.python.org/zh-cn/3.13/library/json.html#json.JSONDecoder) 子类，以及明确提到的形参以外的形参，都未被纳入考量。

​	此模块不严格遵循于 RFC ，它实现了一些扩展是有效的 Javascript 但不是有效的 JSON。尤其是：

- 无限和 NaN 数值是被接受并输出；
- 对象内的重复名称是接受的，并且仅使用最后一对属性-值对的值。

​	自从 RFC 允许符合 RFC 的语法分析程序接收 不符合 RFC 的输入文本以来，这个模块的解串器在默认状态下默认符合 RFC 。

### 字符编码

​	RFC 要求使用 UTF-8 ， UTF-16 ，或 UTF-32 之一来表示 JSON ，为了最大互通性推荐使用 UTF-8 。

​	RFC允许，尽管不是必须的，这个模块的序列化默认设置为 *ensure_ascii=True* ，这样消除输出以便结果字符串至容纳 ASCII 字符。

*ensure_ascii* 参数以外，此模块是严格的按照在 Python 对象和 [`Unicode strings`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 间的转换定义的，并且因此不能直接解决字符编码的问题。

​	RFC 禁止添加字符顺序标记（ BOM ）在 JSON 文本的开头，这个模块的序列化器不添加 BOM 标记在它的输出上。 RFC，准许 JSON 反序列化器忽略它们输入中的初始 BOM 标记，但不要求。此模块的反序列化器引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError) 当存在初始 BOM 标记。

​	RFC 不会明确禁止包含字节序列的 JSON 字符串这不对应有效的 Unicode 字符（比如 不成对的 UTF-16 的替代物），但是它确实指出它们可能会导致互操作性问题。默认下，模块对这样的序列接受和输出（当在原始 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 存在时）代码点。

### Infinite 和 NaN 数值

​	RFC 不允许 infinite 或者 NaN 数值的表达方式。尽管这样，默认情况下，此模块接受并且输出 `Infinity` ， `-Infinity`，和 `NaN` 好像它们是有效的JSON数字字面值



```
>>> # 这些调用均不会引发异常，但结果都不是合法的 JSON
>>> json.dumps(float('-inf'))
'-Infinity'
>>> json.dumps(float('nan'))
'NaN'
>>> # 当反序列化时也一样
>>> json.loads('-Infinity')
-inf
>>> json.loads('NaN')
nan
```

​	序列化器中， *allow_nan* 参数可用于替代这个行为。反序列化器中， *parse_constant* 参数，可用于替代这个行为。

### 对象中的重复名称

​	RFC 具体说明了 在 JSON对象里的名字应该是唯一的，但没有规定如何处理JSON对象中的重复名称。默认下，此模块不引发异常；作为替代，对于给定名它将忽略除姓-值对之外的所有对:



```
>>> weird_json = '{"x": 1, "x": 2, "x": 3}'
>>> json.loads(weird_json)
{'x': 3}
```

​	The *object_pairs_hook* parameter can be used to alter this behavior.

### 顶级非对象，非数组值

​	过时的 [**RFC 4627**](https://datatracker.ietf.org/doc/html/rfc4627.html) 指定的旧版本 JSON 要求 JSON 文本顶级值必须是 JSON 对象或数组（ Python [`dict`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#dict) 或 [`list`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#list) ），并且不能是 JSON null 值，布尔值，数值或者字符串值。 [**RFC 7159**](https://datatracker.ietf.org/doc/html/rfc7159.html) 移除这个限制，此模块没有并且从未在序列化器和反序列化器中实现这个限制。

​	无论如何，为了最大化地获取互操作性，你可能希望自己遵守该原则。

### 实现限制

​	一些 JSON 反序列化器的实现应该在以下方面做出限制：

- 可接受的 JSON 文本大小
- 嵌套 JSON 对象和数组的最高水平
- JSON 数字的范围和精度
- JSON 字符串的内容和最大长度

​	此模块不强制执行任何上述限制，除了相关的 Python 数据类型本身或者 Python 解释器本身的限制以外。

​	当序列化为 JSON ，在应用中当心此类限制这可能破坏你的 JSON 。特别是，通常将 JSON 数字反序列化为 IEEE 754 双精度数字，从而受到该表示方式的范围和精度限制。这是特别相关的，当序列化非常大的 Python [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int) 值时，或者当序列化 "exotic" 数值类型的实例时比如 [`decimal.Decimal`](https://docs.python.org/zh-cn/3.13/library/decimal.html#decimal.Decimal) 。



## CLI

**源代码：** [Lib/json/tool.py](https://github.com/python/cpython/tree/3.13/Lib/json/tool.py)

------

​	The [`json.tool`](https://docs.python.org/zh-cn/3.13/library/json.html#module-json.tool) module provides a simple command line interface to validate and pretty-print JSON objects.

​	如果未指定可选的 `infile` 和 `outfile` 参数，则将分别使用 [`sys.stdin`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stdin) 和 [`sys.stdout`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stdout):

```
$ echo '{"json": "obj"}' | python -m json.tool
{
    "json": "obj"
}
$ echo '{1.2:3.4}' | python -m json.tool
Expecting property name enclosed in double quotes: line 1 column 2 (char 1)
```

*在 3.5 版本发生变更:* 输出现在将与输入顺序保持一致。 请使用 [`--sort-keys`](https://docs.python.org/zh-cn/3.13/library/json.html#cmdoption-json.tool-sort-keys) 选项来将输出按照键的字母顺序排序。

### 命令行选项

## **infile**

​	要被验证或美化打印的 JSON 文件：

```
$ python -m json.tool mp_films.json
[
    {
        "title": "And Now for Something Completely Different",
        "year": 1971
    },
    {
        "title": "Monty Python and the Holy Grail",
        "year": 1975
    }
]
```

​	如果未指定 *infile*，则从 [`sys.stdin`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stdin) 读取。

## **outfile**

​	将 *infile* 输出写入到给定的 *outfile*。 在其他情况下，将写入到 [`sys.stdout`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stdout)。

## **--sort-keys**

​	将字典输出按照键的字母顺序排序。

*Added in version 3.5.*

## **--no-ensure-ascii**

​	禁用非 ASCII 字符的转义，详情参见 [`json.dumps()`](https://docs.python.org/zh-cn/3.13/library/json.html#json.dumps)。

*Added in version 3.9.*

## **--json-lines**

​	将每个输入行解析为单独的 JSON 对象。

*Added in version 3.8.*

## **--indent**, **--tab**, **--no-indent**, **--compact**

​	用于空白符控制的互斥选项。

*Added in version 3.9.*

## **-h**, **--help**

​	显示帮助消息。

​	备注

[[1](https://docs.python.org/zh-cn/3.13/library/json.html#id1)]

​	正如 [RFC 7159 的勘误表](https://www.rfc-editor.org/errata_search.php?rfc=7159) 所说明的，JSON 允许以字符串表示字面值字符 U+2028 (LINE SEPARATOR) 和 U+2029 (PARAGRAPH SEPARATOR)，而 JavaScript (在 ECMAScript 5.1 版中) 不允许。
