+++
title = "`email.charset`: 表示字符集"
date = 2024-11-15T12:09:25+08:00
weight = 110
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/email.charset.html](https://docs.python.org/zh-cn/3.13/library/email.charset.html)
>
> 收录该文档的时间：`2024-11-15T12:09:25+08:00`

# `email.charset`: 表示字符集

**源代码:** [Lib/email/charset.py](https://github.com/python/cpython/tree/3.13/Lib/email/charset.py)

------

​	此模块是旧版 (`Compat32`) email API 的组成部分。 在新版 API 中只会使用其中的别名表。

​	本节中的其余文本是此模块的原始文档。

​	此模块提供了一个 [`Charset`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset" >}}) 类用来表示电子邮件消息中的字符集和字符集转换操作，以及一个字符集注册表和几个用于操作此注册表的便捷方法。 [`Charset`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset" >}}) 的实例在 [`email`]({{< ref "/library/netdata/email#module-email" >}}) 包的其他几个模块中也有使用。

​	请从 [`email.charset`]({{< ref "/library/netdata/email/email_charset#module-email.charset" >}}) 模块导入这个类。

## *class* email.charset.**Charset**(*input_charset=DEFAULT_CHARSET*)

​	将字符集映射到其 email 特征属性。

​	这个类提供了特定字符集对于电子邮件的要求的相关信息。 考虑到适用编解码器的可用性，它还为字符集之间的转换提供了一些便捷例程。 在给定字符集的情况下，它将尽可能地以符合 RFC 的方式在电子邮件消息中提供有关如何使用该字符集的信息。

​	特定字符集当在电子邮件标头或消息体中使用时必须以 quoted-printable 或 base64 来编码。 某些字符集则必须被立即转换，不允许在电子邮件中使用。

​	可选的 *input_charset* 说明如下；它总是会被强制转为小写。 在进行别名正规化后它还会被用来查询字符集注册表以找出用于该字符集的标头编码格式、消息体编码格式和输出转换编解码器。 举例来说，如果 *input_charset* 为 `iso-8859-1`，则标头和消息体将会使用 quoted-printable 来编码并且不需要输出转换编解码器。 如果 *input_charset* 为 `euc-jp`，则标头将使用 base64 来编码，消息体将不会被编码，但输出文本将从 `euc-jp` 字符集转换为 `iso-2022-jp` 字符集。

[`Charset`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset" >}}) 实例具有下列数据属性:

## **input_charset**

​	指定的初始字符集。 通用别名会被转换为它们的 *官方* 电子邮件名称 (例如 `latin_1` 会被转换为 `iso-8859-1`)。 默认值为 7 位 `us-ascii`。

## **header_encoding**

​	如果字符集在用于电子邮件标头之前必须被编码，此属性将被设为 `charset.QP` (表示 quoted-printable 编码格式), `charset.BASE64` (表示 base64 编码格式) 或 `charset.SHORTEST` 表示 QP 或 BASE64 编码格式中最简短的一个。 在其他情况下，该属性将为 `None`。

## **body_encoding**

​	与 *header_encoding* 一样，但是用来描述电子邮件消息体的编码格式，它实际上可以与标头编码格式不同。 `charset.SHORTEST` 不允许被用作 *body_encoding*。

## **output_charset**

​	某些字符集在用于电子邮件标头或消息体之前必须被转换。 如果 *input_charset* 是这些字符集之一，该属性将包含输出将要转换的字符集名称。 在其他情况下，该属性将为 `None`。

## **input_codec**

​	用于将 *input_charset* 转换为 Unicode 的 Python 编解码器名称。 如果不需要任何转换编解码器，该属性将为 `None`。

## **output_codec**

​	用于将 Unicode 转换为 *output_charset* 的 Python 编解码器名称。 如果不需要任何转换编解码器，该属性将具有与 *input_codec* 相同的值。

[`Charset`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset" >}}) 实例还有下列方法:

## **get_body_encoding**()

​	返回用于消息体编码的内容转换编码格式。

​	根据所使用的编码格式返回 `quoted-printable` 或 `base64`，或是返回一个函数，在这种情况下你应当调用该函数并附带一个参数，即被编码的消息对象。 该函数应当自行将 *Content-Transfer-Encoding* 标头设为适当的值。

​	如果 *body_encoding* 为 `QP` 则返回字符串 `quoted-printable`，如果 *body_encoding* 为 `BASE64` 则返回字符串 `base64`，并在其他情况下返回字符串 `7bit`。

## **get_output_charset**()

​	返回输出字符集。

​	如果 *output_charset* 属性不为 `None` 则返回该属性，否则返回 *input_charset*。

## **header_encode**(*string*)

​	对字符串 *string* 执行标头编码。

​	编码格式的类型 (base64 或 quoted-printable) 将取决于 *header_encoding* 属性。

## **header_encode_lines**(*string*, *maxlengths*)

​	通过先将 *string* 转换为字节串来对其执行标头编码。

​	这类似于 [`header_encode()`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset.header_encode" >}})，区别是字符串会被调整至参数 *maxlengths* 所给出的最大行长度，它应当是一个迭代器：该迭代器返回的每个元素将提供下一个最大行长度。

## **body_encode**(*string*)

​	对字符串 *string* 执行消息体编码。

​	编码格式的类型 (base64 或 quoted-printable) 将取决于 *body_encoding* 属性。

[`Charset`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset" >}}) 类还提供了一些方法以支持标准运算和内置函数。

## `__str__`()

​	将 *input_charset* 以转为小写的字符串形式返回。 `__repr__()` 是 `__str__()` 的别名。

## `__eq__`(*other*)

​	这个方法允许你对两个 [`Charset`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset" >}}) 实例进行相等比较。

## `__ne__`(*other*)

​	这个方法允许你对两个 [`Charset`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset" >}}) 实例进行相等比较。

[`email.charset`]({{< ref "/library/netdata/email/email_charset#module-email.charset" >}}) 模块还提供了下列函数用于向全局字符集、别名以及编解码器注册表添加新条目:

## email.charset.**add_charset**(*charset*, *header_enc=None*, *body_enc=None*, *output_charset=None*)

​	向全局注册表添加字符特征属性。

*charset* 是输入字符集，它必须为某个字符集的正规名称。

​	可选的 *header_enc* 和 *body_enc* 可以是 `charset.QP` 表示 quoted-printable 编码格式, `charset.BASE64` 表示 base64 编码格式, `charset.SHORTEST` 表示 quoted-printable 或 base64 编码格式中较短的一个，或者为 `None` 表示没有编码格式。 `SHORTEST` 仅对 *header_enc* 有效。 默认值为 `None` 表示没有编码格式。

​	可选的 *output_charset* 是输出所应当采用的字符集。 当 `Charset.convert()` 方法被调用时将会执行从输入字符集到输出字符集的转换。 默认情况下输出字符集将与输入字符集相同。

*input_charset* 和 *output_charset* 都必须在模块的字符集-编解码器映射中具有 Unicode 编解码器条目；使用 [`add_codec()`]({{< ref "/library/netdata/email/email_charset#email.charset.add_codec" >}}) 可添加本模块还不知道的编解码器。 请参阅 [`codecs`]({{< ref "/library/binary/codecs#module-codecs" >}}) 模块的文档来了解更多信息。

​	全局字符集注册表保存在模块全局字典 `CHARSETS` 中。

## email.charset.**add_alias**(*alias*, *canonical*)

​	添加一个字符集别名。 *alias* 为特定的别名，例如 `latin-1`。 *canonical* 是字符集的正规名称，例如 `iso-8859-1`。

​	全局字符集注册表保存在模块全局字典 `ALIASES` 中。

## email.charset.**add_codec**(*charset*, *codecname*)

​	添加在给定字符集的字符和 Unicode 之间建立映射的编解码器。

*charset* 是某个字符集的正规名称。 *codecname* 是某个 Python 编解码器的名称，可以被用来作为 [`str`]({{< ref "/library/stdtypes#str" >}}) 的 [`encode()`]({{< ref "/library/stdtypes#str.encode" >}}) 方法的第二个参数。
