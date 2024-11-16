+++
title = "`email.header`: 国际化标头"
date = 2024-11-15T12:09:25+08:00
weight = 100
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/email.header.html](https://docs.python.org/zh-cn/3.13/library/email.header.html)
>
> 收录该文档的时间：`2024-11-15T12:09:25+08:00`

# `email.header`: 国际化标头

**源代码:** [Lib/email/header.py](https://github.com/python/cpython/tree/3.13/Lib/email/header.py)

------

​	此模块是旧式 (`Compat32`) email API 的一部分。 在当前的 API 中标头的编码和解码是由 [`EmailMessage`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage" >}}) 类的字典型 API 来透明地处理的。 除了在旧有代码中使用，此模块在需要完全控制当编码标头时所使用的字符集时也很有用处。

​	本节中的其余文本是此模块的原始文档。

[**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.html) 是描述电子邮件消息格式的基础标准。 它派生自更早的 [**RFC 822**](https://datatracker.ietf.org/doc/html/rfc822.html) 标准，该标准在大多数电子邮件仅由 ASCII 字符组成时已被广泛使用。 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.html) 所描述的规范假定电子邮件都只包含 7 位 ASCII 字符。

​	当然，随着电子邮件在全球部署，它已经变得国际化了，例如电子邮件消息中现在可以使用特定语言的专属字符集。 这个基础标准仍然要求电子邮件消息只使用 7 位 ASCII 字符来进行传输，为此编写了大量 RFC 来描述如何将包含非 ASCII 字符的电子邮件编码为符合 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.html) 的格式。 这些 RFC 包括 [**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.html), [**RFC 2046**](https://datatracker.ietf.org/doc/html/rfc2046.html), [**RFC 2047**](https://datatracker.ietf.org/doc/html/rfc2047.html) 和 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.html)。 [`email`]({{< ref "/library/netdata/email#module-email" >}}) 包在其 [`email.header`]({{< ref "/library/netdata/email/email_header#module-email.header" >}}) 和 [`email.charset`]({{< ref "/library/netdata/email/email_charset#module-email.charset" >}}) 模块中支持了这些标准。

​	如果你想在你的电子邮件标头中包括非 ASCII 字符，比如说是在 *Subject* 或 *To* 字段中，你应当使用 [`Header`]({{< ref "/library/netdata/email/email_header#email.header.Header" >}}) 类并将 [`Message`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message" >}}) 对象中的字段赋值为 [`Header`]({{< ref "/library/netdata/email/email_header#email.header.Header" >}}) 的实例而不是使用字符串作为字段值。 请从 [`email.header`]({{< ref "/library/netdata/email/email_header#module-email.header" >}}) 模块导入 [`Header`]({{< ref "/library/netdata/email/email_header#email.header.Header" >}}) 类。 例如:



``` python
>>> from email.message import Message
>>> from email.header import Header
>>> msg = Message()
>>> h = Header('p\xf6stal', 'iso-8859-1')
>>> msg['Subject'] = h
>>> msg.as_string()
'Subject: =?iso-8859-1?q?p=F6stal?=\n\n'
```

​	是否注意到这里我们是如何希望 *Subject* 字段包含非 ASCII 字符的？ 我们通过创建一个 [`Header`]({{< ref "/library/netdata/email/email_header#email.header.Header" >}}) 实例并传入字节串编码所用的字符集来做到这一点。 当后续的 [`Message`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message" >}}) 实例被展平时，*Subject* 字段会正确地按 [**RFC 2047**](https://datatracker.ietf.org/doc/html/rfc2047.html) 来编码。 可感知 MIME 的电子邮件阅读器将会使用嵌入的 ISO-8859-1 字符来显示此标头。

​	以下是 [`Header`]({{< ref "/library/netdata/email/email_header#email.header.Header" >}}) 类描述:

## *class* email.header.**Header**(*s=None*, *charset=None*, *maxlinelen=None*, *header_name=None*, *continuation_ws=' '*, *errors='strict'*)

​	创建符合 MIME 要求的标头，其中可包含不同字符集的字符串。

​	可选的 *s* 是初始标头值。 如果为 `None` (默认值)，则表示初始标头值未设置。 你可以在稍后使用 [`append()`]({{< ref "/library/netdata/email/email_header#email.header.Header.append" >}}) 方法调用向标头添加新值。 *s* 可以是 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 或 [`str`]({{< ref "/library/stdtypes#str" >}}) 的实例，注意参阅 [`append()`]({{< ref "/library/netdata/email/email_header#email.header.Header.append" >}}) 文档了解相关语义。

​	可选的 *charset* 用于两种目的：它的含义与 [`append()`]({{< ref "/library/netdata/email/email_header#email.header.Header.append" >}}) 方法的 *charset* 参数相同。 它还会为所有省略了 *charset* 参数的后续 [`append()`]({{< ref "/library/netdata/email/email_header#email.header.Header.append" >}}) 调用设置默认字符集。 如果 *charset* 在构造器中未提供（默认设置），则会将 `us-ascii` 字符集用作 *s* 的初始字符集以及后续 [`append()`]({{< ref "/library/netdata/email/email_header#email.header.Header.append" >}}) 调用的默认字符集。

​	通过 maximum line length can be specified explicitly via *maxlinelen* 可以显式地指定行长度的最大值。 要将第一行拆分为更短的值（以适应未被包括在 *s* 中的字段标头，例如 *Subject* 等）则将字段名称作为 *header_name* 传入。 默认的 *maxlinelen* 为 78，而 *header_name* 的默认值为 `None`，表示不考虑拆分超长标头的第一行。

​	可选的 *continuation_ws* 必须为符合 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.html) 的折叠用空白符，通常是空格符或硬制表符。 这个字符将被加缀至连续行的开头。 *continuation_ws* 默认为一个空格符。

​	可选的 *errors* 会被直接传递给 [`append()`]({{< ref "/library/netdata/email/email_header#email.header.Header.append" >}}) 方法。

## **append**(*s*, *charset=None*, *errors='strict'*)

​	将字符串 *s* 添加到 MIME 标头。

​	如果给出可选的 *charset*，它应当是一个 [`Charset`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset" >}}) 实例 (参见 [`email.charset`]({{< ref "/library/netdata/email/email_charset#module-email.charset" >}})) 或字符集名称，该参数将被转换为一个 [`Charset`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset" >}}) 实例。 如果为 `None` (默认值) 则表示会使用构造器中给出的 *charset*。

*s* 可以是 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 或 [`str`]({{< ref "/library/stdtypes#str" >}}) 的实例。 如果它是 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 的实例，则 *charset* 为该字节串的编码格式，如果字节串无法用该字符集来解码则将引发 [`UnicodeError`]({{< ref "/library/exceptions#UnicodeError" >}})。

​	如果 *s* 是 [`str`]({{< ref "/library/stdtypes#str" >}}) 的实例，则 *charset* 是用来指定字符串中字符字符集的提示。

​	在这两种情况下，当使用 [**RFC 2047**](https://datatracker.ietf.org/doc/html/rfc2047.html) 规则产生符合 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.html) 的标头时，将使用指定字符集的输出编解码器来编码字符串。 如果字符串无法使用该输出编解码器来编码，则将引发 UnicodeError。

​	可选的 *errors* 会在 *s* 为字节串时被作为 errors 参数传递给 decode 调用。

## **encode**(*splitchars=';, \t'*, *maxlinelen=None*, *linesep='\n'*)

​	将消息标头编码为符合 RFC 的格式，可能会对过长的行采取折行并将非 ASCII 部分以 base64 或 quoted-printable 编码格式进行封装。

​	可选的 *splitchars* 是一个字符串，其中包含应在正常的标头折行处理期间由拆分算法赋予额外权重的字符。 这是对于 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.html) 中 '更高层级语法拆分' 的很粗略的支持：在拆分期间会首选在 splitchar 之前的拆分点，字符的优先级是基于它们在字符串中的出现顺序。 字符串中可包含空格和制表符以指明当其他拆分字符未在被拆分行中出现时是否要将某个字符作为优先于另一个字符的首选拆分点。 拆分字符不会影响以 [**RFC 2047**](https://datatracker.ietf.org/doc/html/rfc2047.html) 编码的行。

​	如果给出 *maxlinelen*，它将覆盖实例的最大行长度值。

*linesep* 指定用来分隔已折叠标头行的字符。 它默认为 Python 应用程序代码中最常用的值 (`\n`)，但也可以指定为 `\r\n` 以便产生带有符合 RFC 的行分隔符的标头。

*在 3.2 版本发生变更:* 增加了 *linesep* 参数。

[`Header`]({{< ref "/library/netdata/email/email_header#email.header.Header" >}}) 类还提供了一些方法以支持标准运算符和内置函数。

## **__str__**()

​	以字符串形式返回 [`Header`]({{< ref "/library/netdata/email/email_header#email.header.Header" >}}) 的近似表示，使用不受限制的行长度。 所有部分都会使用指定编码格式转换为 unicode 并适当地连接起来。 任何带有 `'unknown-8bit'` 字符集的部分都会使用 `'replace'` 错误处理程序解码为 ASCII。

*在 3.2 版本发生变更:* 增加对 `'unknown-8bit'` 字符集的处理。

## **__eq__**(*other*)

​	这个方法允许你对两个 [`Header`]({{< ref "/library/netdata/email/email_header#email.header.Header" >}}) 实例进行相等比较。

## **__ne__**(*other*)

​	这个方法允许你对两个 [`Header`]({{< ref "/library/netdata/email/email_header#email.header.Header" >}}) 实例进行不等比较。

[`email.header`]({{< ref "/library/netdata/email/email_header#module-email.header" >}}) 模块还提供了下列便捷函数。

## email.header.**decode_header**(*header*)

​	在不转换字符集的情况下对消息标头值进行解码。 *header* 为标头值。

​	这个函数返回一个 `(decoded_string, charset)` 对的列表，其中包含标头的每个已解码部分。 对于标头的未编码部分 *charset* 为 `None`，在其他情况下则为一个包含已编码字符串中所指定字符集名称的小写字符串。

​	以下是为示例代码:



``` python
>>> from email.header import decode_header
>>> decode_header('=?iso-8859-1?q?p=F6stal?=')
[(b'p\xf6stal', 'iso-8859-1')]
```

## email.header.**make_header**(*decoded_seq*, *maxlinelen=None*, *header_name=None*, *continuation_ws=' '*)

​	基于 [`decode_header()`]({{< ref "/library/netdata/email/email_header#email.header.decode_header" >}}) 所返回的数据对序列创建一个 [`Header`]({{< ref "/library/netdata/email/email_header#email.header.Header" >}}) 实例。

[`decode_header()`]({{< ref "/library/netdata/email/email_header#email.header.decode_header" >}}) 接受一个标头值字符串并返回格式为 `(decoded_string, charset)` 的数据对序列，其中 *charset* 是字符集名称。

​	这个函数接受这样的数据对序列并返回一个 [`Header`]({{< ref "/library/netdata/email/email_header#email.header.Header" >}}) 实例。 可选的 *maxlinelen*, *header_name* 和 *continuation_ws* 与 [`Header`]({{< ref "/library/netdata/email/email_header#email.header.Header" >}}) 构造器中的含义相同。
