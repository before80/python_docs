+++
title = "uuid --- 根据 RFC 4122 定义的 UUID 对象"
date = 2024-11-15T20:28:46+08:00
weight = 140
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/uuid.html](https://docs.python.org/zh-cn/3.13/library/uuid.html)
>
> 收录该文档的时间：`2024-11-15T20:28:46+08:00`

# `uuid` --- 根据 [**RFC 4122**](https://datatracker.ietf.org/doc/html/rfc4122.html) 定义的 UUID 对象

**源代码：** [Lib/uuid.py](https://github.com/python/cpython/tree/3.13/Lib/uuid.py)

------

​	这个模块提供了不可变的 [`UUID`]({{< ref "/library/internet/uuid#uuid.UUID" >}}) 对象 ([`UUID`]({{< ref "/library/internet/uuid#uuid.UUID" >}}) 类) 和 [`uuid1()`]({{< ref "/library/internet/uuid#uuid.uuid1" >}}), [`uuid3()`]({{< ref "/library/internet/uuid#uuid.uuid3" >}}), [`uuid4()`]({{< ref "/library/internet/uuid#uuid.uuid4" >}}), [`uuid5()`]({{< ref "/library/internet/uuid#uuid.uuid5" >}}) 等函数用于生成 [**RFC 4122**](https://datatracker.ietf.org/doc/html/rfc4122.html) 所定义的第 1, 3, 4 和 5 版 UUID。

​	如果你想要的只是一个唯一的ID，你可能应该调用 [`uuid1()`]({{< ref "/library/internet/uuid#uuid.uuid1" >}}) 或 [`uuid4()`]({{< ref "/library/internet/uuid#uuid.uuid4" >}})。 注意 [`uuid1()`]({{< ref "/library/internet/uuid#uuid.uuid1" >}}) 可能会损害隐私，因为它创建了一个包含计算机网络地址的UUID。 [`uuid4()`]({{< ref "/library/internet/uuid#uuid.uuid4" >}}) 可以创建一个随机UUID。

​	根据底层平台的支持情况，[`uuid1()`]({{< ref "/library/internet/uuid#uuid.uuid1" >}}) 可能会也可能不会返回“安全“的 UUID。 安全的 UUID 是指使用同步方法生成的 UUID，此方法可确保没有两个进程能获得相同的 UUID。 [`UUID`]({{< ref "/library/internet/uuid#uuid.UUID" >}}) 的所有实例都有一个能够中转关于 UUID 安全性的任何信息的 [`is_safe`]({{< ref "/library/internet/uuid#uuid.UUID.is_safe" >}}) 属性，它可使用下列枚举：

## *class* uuid.**SafeUUID**

> Added in version 3.7.
>

## **safe**

​	该UUID是由平台以多进程安全的方式生成的。

## **unsafe**

​	UUID不是以多进程安全的方式生成的。

## **unknown**

​	该平台不提供UUID是否安全生成的信息。

## *class* uuid.**UUID**(*hex=None*, *bytes=None*, *bytes_le=None*, *fields=None*, *int=None*, *version=None*, ***, *is_safe=SafeUUID.unknown*)

​	用一串32位十六进制数字、一串大端序16个字节作为*bytes*参数、一串16个小端序字节作为*bytes_le*参数、一个由六个整数组成的元组（32位*time_low*，16位*time_mid*，16位*time_hi_version*，8位*clock_seq_hi_variant*，8位*clock_seq_low*，48位*node*）作为*fields*参数，或者一个128位整数作为*int*参数创建一个UUID。当给出一串十六进制数字时，大括号、连字符和URN前缀都是可选的。 例如，这些表达式都产生相同的UUID:

```
UUID('{12345678-1234-5678-1234-567812345678}')
UUID('12345678123456781234567812345678')
UUID('urn:uuid:12345678-1234-5678-1234-567812345678')
UUID(bytes=b'\x12\x34\x56\x78'*4)
UUID(bytes_le=b'\x78\x56\x34\x12\x34\x12\x78\x56' +
              b'\x12\x34\x56\x78\x12\x34\x56\x78')
UUID(fields=(0x12345678, 0x1234, 0x5678, 0x12, 0x34, 0x567812345678))
UUID(int=0x12345678123456781234567812345678)
```

​	必须给出 *hex*、*bytes*、*bytes_le*、*fields* 或 *int* 中的唯一一个。 *version* 参数是可选的；如果给定，产生的UUID将根据 [**RFC 4122**](https://datatracker.ietf.org/doc/html/rfc4122.html) 设置其变体和版本号，覆盖给定的 *hex*、*bytes*、*bytes_le*、*fields* 或 *int* 中的位。

​	UUID 对象的比较是通过比较它们的 [`UUID.int`]({{< ref "/library/internet/uuid#uuid.UUID.int" >}}) 属性进行的。 与非 UUID 对象的比较会引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。

`str(uuid)` 返回一个 `12345678-1234-5678-1234-567812345678` 形式的字符串，其中 32 位十六进制数字代表 UUID。

[`UUID`]({{< ref "/library/internet/uuid#uuid.UUID" >}}) 实例有这些只读的属性:

## UUID.**bytes**

​	UUID是一个16字节的字符串（包含6个大端字节序的整数字段）。

## UUID.**bytes_le**

​	UUID 是一个 16 字节的字符串（其中 *time_low*、*time_mid* 和 *time_hi_version* 为小端字节顺序）。

## UUID.**fields**

​	以元组形式存放的UUID的6个整数域，有六个单独的属性和两个派生属性：

| 域                            | 含意                  |
| ----------------------------- | --------------------- |
| UUID.**time_low**             | UUID 的前 32 位。     |
| UUID.**time_mid**             | UUID 后续的 16 位。   |
| UUID.**time_hi_version**      | UUID 后续的 16 位。   |
| UUID.**clock_seq_hi_variant** | UUID 后续的 8 位。    |
| UUID.**clock_seq_low**        | UUID 后续的 8 位。    |
| UUID.**node**                 | UUID 的最后 48 位。   |
| UUID.**time**                 | 60 比特位的时间戳。   |
| UUID.**clock_seq**            | 14 比特位的序列编号。 |

## UUID.**hex**

​	UUID 是一个 32 字符的小写十六进制数码字符串。

## UUID.**int**

​	UUID是一个128位的整数。

## UUID.**urn**

​	在 [**RFC 4122**](https://datatracker.ietf.org/doc/html/rfc4122.html) 中定义的 URN 形式的 UUID。

## UUID.**variant**

​	UUID 的变体，它决定了 UUID 的内部布局。 这将是 [`RESERVED_NCS`]({{< ref "/library/internet/uuid#uuid.RESERVED_NCS" >}}), [`RFC_4122`]({{< ref "/library/internet/uuid#uuid.RFC_4122" >}}), [`RESERVED_MICROSOFT`]({{< ref "/library/internet/uuid#uuid.RESERVED_MICROSOFT" >}}) 或 [`RESERVED_FUTURE`]({{< ref "/library/internet/uuid#uuid.RESERVED_FUTURE" >}}) 中的一个。

## UUID.**version**

​	UUID 版本号（1 到 5，只有当变体为 [`RFC_4122`]({{< ref "/library/internet/uuid#uuid.RFC_4122" >}}) 时才有意义）。

## UUID.**is_safe**

​	一个 [`SafeUUID`]({{< ref "/library/internet/uuid#uuid.SafeUUID" >}}) 的枚举，表示平台是否以多进程安全的方式生成 UUID。

> Added in version 3.7.
>

[`uuid`]({{< ref "/library/internet/uuid#module-uuid" >}}) 模块定义了以下函数：

## uuid.**getnode**()

​	获取 48 位正整数形式的硬件地址。 第一次运行时，它可能会启动一个单独的程序，这可能会相当慢。 如果所有获取硬件地址的尝试都失败了，我们会按照 [**RFC 4122**](https://datatracker.ietf.org/doc/html/rfc4122.html) 中的建议，选择一个随机的 48 位数字，其多播位 (第一个八进制数的最小有效位) 设置为 1。 “硬件地址”是指一个网络接口的 MAC 地址。 在一台有多个网络接口的机器上，普遍管理的 MAC 地址 (即第一个八位数的第二个最小有效位是 *未设置的*) 将比本地管理的 MAC 地址优先，但没有其他排序保证。

*在 3.7 版本发生变更:* 普遍管理的MAC地址优于本地管理的MAC地址，因为前者保证是全球唯一的，而后者则不是。

## uuid.**uuid1**(*node=None*, *clock_seq=None*)

​	根据主机 ID、序列号和当前时间生成一个 UUID。 如果没有给出 *node*，则使用 [`getnode()`]({{< ref "/library/internet/uuid#uuid.getnode" >}}) 来获取硬件地址。 如果给出了 *clock_seq*，它将被用作序列号；否则将选择一个随机的 14 比特位序列号。

## uuid.**uuid3**(*namespace*, *name*)

​	根据命名空间标识符（一个 UUID）和名称（一个 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 对象或将使用 UTF-8 进行编码的字符串）的 MD5 哈希值来生成一个 UUID。

## uuid.**uuid4**()

​	生成一个随机的UUID。

## uuid.**uuid5**(*namespace*, *name*)

​	根据命名空间标识符（一个 UUID）和名称（一个 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 对象或将使用 UTF-8 进行编码的字符串）的 SHA-1 哈希值来生成一个 UUID。

[`uuid`]({{< ref "/library/internet/uuid#module-uuid" >}}) 模块定义了以下命名空间标识符，供 [`uuid3()`]({{< ref "/library/internet/uuid#uuid.uuid3" >}}) 或 [`uuid5()`]({{< ref "/library/internet/uuid#uuid.uuid5" >}}) 使用。

## uuid.**NAMESPACE_DNS**

​	当指定这个命名空间时，*name* 字符串是一个完全限定的域名。

## uuid.**NAMESPACE_URL**

​	当指定这个命名空间时，*name* 字符串是一个 URL。

## uuid.**NAMESPACE_OID**

​	当指定这个命名空间时，*name* 字符串是一个 ISO OID。

## uuid.**NAMESPACE_X500**

​	当指定这个命名空间时，*name* 字符串是 DER 或文本输出格式的 X.500 DN。

[`uuid`]({{< ref "/library/internet/uuid#module-uuid" >}}) 模块为 [`variant`]({{< ref "/library/internet/uuid#uuid.UUID.variant" >}}) 属性的可能值定义了以下常量：

## uuid.**RESERVED_NCS**

​	为NCS兼容性保留。

## uuid.**RFC_4122**

​	指定 [**RFC 4122**](https://datatracker.ietf.org/doc/html/rfc4122.html) 中给出的 UUID 布局。

## uuid.**RESERVED_MICROSOFT**

​	为微软的兼容性保留。

## uuid.**RESERVED_FUTURE**

​	保留给未来的定义。

​	参见

## [**RFC 4122**](https://datatracker.ietf.org/doc/html/rfc4122.html) - 通用唯一标识符（UUID）URN命名空间

​	本规范定义了UUID的统一资源名称空间，UUID的内部格式，以及生成UUID的方法。



## 命令行用法

> Added in version 3.12.
>

[`uuid`]({{< ref "/library/internet/uuid#module-uuid" >}}) 模块可以在命令行下作为脚本来执行。

```
python -m uuid [-h] [-u {uuid1,uuid3,uuid4,uuid5}] [-n NAMESPACE] [-N NAME]
```

​	可以接受以下选项：

## **-h**, **--help**

​	显示帮助信息并退出。

## **-u** <uuid>

## **--uuid** <uuid>

​	指定要用于生成 uuid 的函数名称。 默认会使用 [`uuid4()`]({{< ref "/library/internet/uuid#uuid.uuid4" >}})。

## **-n** <namespace>

## **--namespace** <namespace>

​	该命名空间是一个 `UUID` 或者 `@ns` 其中 `ns` 是一个以命名空间名称来定位的知名预定义 UUID。 例如 `@dns`, `@url`, `@oid` 和 `@x500`。 仅对于 [`uuid3()`]({{< ref "/library/internet/uuid#uuid.uuid3" >}}) / [`uuid5()`]({{< ref "/library/internet/uuid#uuid.uuid5" >}}) 等函数是必需的。

## **-N** <name>

## **--name** <name>

​	用作生成 uuid 的一部分的名称。 仅对于 [`uuid3()`]({{< ref "/library/internet/uuid#uuid.uuid3" >}}) / [`uuid5()`]({{< ref "/library/internet/uuid#uuid.uuid5" >}}) 等函数是必需的。



## 示例

​	下面是一些 [`uuid`]({{< ref "/library/internet/uuid#module-uuid" >}}) 模块的典型使用例子:



``` python
>>> import uuid

>>> # 基于主机 ID 和当前时间生成一个 UUID
>>> uuid.uuid1()
UUID('a8098c1a-f86e-11da-bd1a-00112444be1e')

>>> # 使用命名空间 UUID 和特定名称的 MD5 哈希值生成一个 UUID
>>> uuid.uuid3(uuid.NAMESPACE_DNS, 'python.org')
UUID('6fa459ea-ee8a-3ca4-894e-db77e160355e')

>>> # 生成一个随机 UUID
>>> uuid.uuid4()
UUID('16fd2706-8baf-433b-82eb-8c7fada847da')

>>> # 使用命名空间 UUID 和特定名称的 SHA-1 哈希值生成一个 UUID
>>> uuid.uuid5(uuid.NAMESPACE_DNS, 'python.org')
UUID('886313e1-3b8a-5372-9b90-0c9aee199e5d')

>>> # 基于十六进制数码字符串（忽略花括号和连字符）生成一个 UUID
>>> x = uuid.UUID('{00010203-0405-0607-0809-0a0b0c0d0e0f}')

>>> # 将一个 UUID 转换为标准格式的十六进制数码字符串
>>> str(x)
'00010203-0405-0607-0809-0a0b0c0d0e0f'

>>> # 获取 UUID 的 16 字节原始字节串
>>> x.bytes
b'\x00\x01\x02\x03\x04\x05\x06\x07\x08\t\n\x0b\x0c\r\x0e\x0f'

>>> # 基于 16 字节形式的字符串获取 UUID
>>> uuid.UUID(bytes=x.bytes)
UUID('00010203-0405-0607-0809-0a0b0c0d0e0f')
```



## 命令行示例

​	下面是一些 [`uuid`]({{< ref "/library/internet/uuid#module-uuid" >}}) 命令行接口的典型用法示例:

```
# 生成一个随机的 uuid - 默认将使用 uuid4()
$ python -m uuid

# 使用 uuid1() 生成一个 uuid
$ python -m uuid -u uuid1

# 使用 uuid5 生成一个 uuid
$ python -m uuid -u uuid5 -n @url -N example.com
```
