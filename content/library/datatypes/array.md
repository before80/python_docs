+++
title = "array --- 高效的数字值数组"
date = 2024-11-15T11:18:41+08:00
weight = 70
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/array.html](https://docs.python.org/zh-cn/3.13/library/array.html)
>
> 收录该文档的时间：`2024-11-15T11:18:41+08:00`

# `array` --- 高效的数字值数组

------

​	此模块定义了一种对象类型，可以紧凑地表示由基本值（字符、整数、浮点数）组成的数组。数组是序列类型，其行为与列表非常相似，不同之处在于其中存储的对象类型是受限的，在数组对象创建时用单个字符的 *类型码* 来指定。已定义的类型码如下：

| 类型码 | C 类型             | Python 类型  | 最小字节数 | 备注 |
| :----- | :----------------- | :----------- | :--------- | :--- |
| `'b'`  | signed char        | int          | 1          |      |
| `'B'`  | unsigned char      | int          | 1          |      |
| `'u'`  | wchar_t            | Unicode 字符 | 2          | (1)  |
| `'w'`  | Py_UCS4            | Unicode 字符 | 4          |      |
| `'h'`  | signed short       | int          | 2          |      |
| `'H'`  | unsigned short     | int          | 2          |      |
| `'i'`  | signed int         | int          | 2          |      |
| `'I'`  | unsigned int       | int          | 2          |      |
| `'l'`  | signed long        | int          | 4          |      |
| `'L'`  | unsigned long      | int          | 4          |      |
| `'q'`  | signed long long   | int          | 8          |      |
| `'Q'`  | unsigned long long | int          | 8          |      |
| `'f'`  | float              | float        | 4          |      |
| `'d'`  | double             | float        | 8          |      |

​	备注：

1. 可能为 16 位或 32 位，取决于具体的平台。

   > 在 3.9 版本发生变更: `array('u')` 现在使用 `wchar_t` 作为 C 类型而不是已不建议使用的 `Py_UNICODE`。这个改变不会影响其行为，因为 `Py_UNICODE` 自 Python 3.3 起就是 `wchar_t` 的别名。

   *Deprecated since version 3.3, will be removed in version 3.16:* 请迁移到 `'w'` 类型码。

​	值的实际表示是由机器架构（严格说是由 C 实现）决定的。实际大小可以通过 [`array.itemsize`]({{< ref "/library/datatypes/array#array.array.itemsize" >}}) 属性来访问。

​	此模块定义了以下项目：

## array.**typecodes**

​	一个由所有可用的类型码组成的字符串。

​	此模块定义了以下类型：

## *class* array.**array**(*typecode*[, *initializer*])

​	一个由 *typecode* 限定其条目的新数组，并能根据可选的 *initializer* 值来初始化。*initializer* 必须是 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 或 [`bytearray`]({{< ref "/library/stdtypes#bytearray" >}}) 对象、Unicode 字符串或元素类型合适的可迭代对象。

​	如果给定了一个 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 或 [`bytearray`]({{< ref "/library/stdtypes#bytearray" >}}) 对象，则将 *initializer* 传给新数组的 [`frombytes()`]({{< ref "/library/datatypes/array#array.array.frombytes" >}}) 方法；如果给定了一个 Unicode 字符串，则将 *initializer* 传给 [`fromunicode()`]({{< ref "/library/datatypes/array#array.array.fromunicode" >}}) 方法；在其他情况下，则将 *initializer* 的迭代器传给 [`extend()`]({{< ref "/library/datatypes/array#array.array.extend" >}}) 方法以向数组添加初始条目。

​	数组对象支持普通的序列操作如索引、切片、拼接和重复等。当使用切片赋值时，所赋的值必须为具有相同类型码的数组对象；所有其他情况都将引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。数组对象也实现了缓冲区接口，可以用于所有支持 [类字节对象]({{< ref "/glossary/idx#term-bytes-like-object" >}}) 的场合。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `array.__new__` 并附带参数 `typecode`, `initializer`。

## **typecode**

​	在创建数组时使用的类型码字符。

## **itemsize**

​	内部表示中，单个数组项的长度。单位为字节。

## **append**(*x*)

​	添加一个值为 *x* 的新项到数组末尾。

## **buffer_info**()

​	返回一个元组 `(address, length)` 给出存放数组内容的内存缓冲区的当前地址和长度（以元素个数为单位）。以字节为单位的的内存缓冲区大小可通过 `array.buffer_info()[1] * array.itemsize` 来计算。工作在需要内存地址的底层（因此天然地不够安全）的 I/O 接口上时，这有时会有用，例如某些 `ioctl()` 操作。只要数组还存在，并且没有对其应用过改变长度的操作，则返回的数值就是有效的。

​备注
 

​	只有在使用以 C 或 C++ 编写的代码中的数组对象时，才能有效利用该信息，但此时，更合理的是，使用数组对象支持的缓冲区接口。因此，该方法的存在仅仅是为了向后兼容性，应避免在新代码中使用。缓冲区接口的文档参见 [缓冲协议](https://docs.python.org/zh-cn/3.13/c-api/buffer.html#bufferobjects)。

## **byteswap**()

​	“字节对调”所有数组项。此方法只支持大小为 1, 2, 4 或 8 字节的值；对于其它类型的值将引发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。当要从另一种字节顺序的机器生成的文件中读取数据时，它很有用。

## **count**(*x*)

​	返回 *x* 在数组中的出现次数。

## **extend**(*iterable*)

​	将来自 *iterable* 的项添加到数组末尾。如果 *iterable* 是另一个数组，它必须具有 *完全* 相同的类型码；否则将引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。如果 *iterable* 不是一个数组，则它必须为可迭代对象且其元素的类型须为可添加到数组的适当类型。

## **frombytes**(*buffer*)

​	添加来自 [bytes-like object]({{< ref "/glossary/idx#term-bytes-like-object" >}}) 的条目，将其内容解读为由机器值组成的数组（就像是使用 [`fromfile()`]({{< ref "/library/datatypes/array#array.array.fromfile" >}}) 方法从文件中读取内容一样）。

> Added in version 3.2:
> `fromstring()` 被重命名为含义更准确的 [`frombytes()`]({{< ref "/library/datatypes/array#array.array.frombytes" >}})。

## **fromfile**(*f*, *n*)

​	从 [file object]({{< ref "/glossary/idx#term-file-object" >}}) *f* 中读取 *n* 项（视为机器值）并将它们添加到数组末尾。如果可用的项少于 *n* 项，则会引发 [`EOFError`]({{< ref "/library/exceptions#EOFError" >}})，但可用的项仍然会被加进数组。

## **fromlist**(*list*)

​	将来自列表的项添加到数组末尾。等价于 `for x in list: a.append(x)`，而不同之处在于，若发生类型错误，数组则不会被改变。

## **fromunicode**(*s*)

​	使用来自给定的 Unicode 字符串的数据扩展该数组。 该数组的类型码必须为 `'u'` 或 `'w'`；否则将引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。 请使用 `array.frombytes(unicodestring.encode(enc))` 将 Unicode 数据添加到其他类型的数组。

## **index**(*x*[, *start*[, *stop*]])

​	返回以这样的方式找到的最小的 *i*：*i* 为数组中第一个 *x* 的下标。可选参数 *start* 和 *stop* 用于在数组的一个指定的子段中搜索 *x*。如果未找到 *x* 则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

> 在 3.10 版本发生变更: 添加了可选的 *start* 和 *stop* 形参。

## **insert**(*i*, *x*)

​	在数组的位置 *i* 之前插入一个值为 *x* 的新项。负值被视为相对于数组末尾的位置。

## **pop**([*i*])

​	从数组中移除下标为 *i* 的项并将其返回。参数默认值为 `-1`，因此默认移除并返回末项。

## **remove**(*x*)

​	从数组中移除第一个出现的 *x*。

## **clear**()

​	从数组中移除所有元素。

> Added in version 3.13.
>

## **reverse**()

​	反转数组中各项的顺序。

## **tobytes**()

​	将数组转换为一个由机器值组成的数组并返回其字节表示（和用 [`tofile()`]({{< ref "/library/datatypes/array#array.array.tofile" >}}) 方法写入文件的字节序列相同）。

> Added in version 3.2:
> `tostring()` 被重命名为含义更准确的 [`tobytes()`]({{< ref "/library/datatypes/array#array.array.tobytes" >}})。

## **tofile**(*f*)

​	将所有项（作为机器值）写入 [file object]({{< ref "/glossary/idx#term-file-object" >}}) *f*。

## **tolist**()

​	将数组转换为由相同的项组成的普通列表。

## **tounicode**()

​	将数组转换为一个 Unicode 字符串。 数组的类型必须为 `'u'` 或 `'w'`；否则将引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。 请使用 `array.tobytes().decode(enc)` 来从其他类型的数组获取 Unicode 字符串。

​	数组对象的字符串表示形式是 `array(typecode, initializer)`。 如果数组为空则 *initializer* 将被省略，否则如果 *typecode* 为 `'u'` 或 `'w'` 则为 Unicode 字符串，否则为由数字组成的列表。 只要 [`array`]({{< ref "/library/datatypes/array#array.array" >}}) 类是使用 `from array import array` 导入的，该字符串表示形式就保证能使用 [`eval()`]({{< ref "/library/functions#eval" >}}) 转换回具有相同类型和值的数组。 如果它包含相应的浮点数值则还必须定义变量 `inf` 和 `nan`。 例如:

```
array('l')
array('w', 'hello \u2641')
array('l', [1, 2, 3, 4, 5])
array('d', [1.0, 2.0, 3.14, -inf, nan])
```

​参见
## [`struct`]({{< ref "/library/binary/struct#module-struct" >}}) 模块

​	打包和解包异构二进制数据。

## [NumPy](https://numpy.org/)

​	NumPy 包定义了另一数组类型。
