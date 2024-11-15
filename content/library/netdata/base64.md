+++
title = "base64 --- Base16, Base32, Base64, Base85 数据编码"
date = 2024-11-15T14:53:40+08:00
weight = 40
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/base64.html](https://docs.python.org/zh-cn/3.13/library/base64.html)
>
> 收录该文档的时间：`2024-11-15T14:53:40+08:00`

# `base64` --- Base16, Base32, Base64, Base85 数据编码

**源代码：** [Lib/base64.py](https://github.com/python/cpython/tree/3.13/Lib/base64.py)

------

​	此模块提供了将二进制数据编码为可打印的 ASCII 字符以及将这种编码格式解码回二进制数据的函数。 它为 [**RFC 4648**](https://datatracker.ietf.org/doc/html/rfc4648.html) 所定义的 Base16, Base32 和 Base64 算法及已成为事实标准的 Ascii85 和 Base85 编码格式提供了编码和解码函数。

[**RFC 4648**](https://datatracker.ietf.org/doc/html/rfc4648.html) 中的编码格式适用于编码二进制数据使得它能安全地通过电子邮件发送、用作 URL 的一部分，或者包括在 HTTP POST 请求之中。 此编码格式算法与 **uuencode** 程序并不相同。

​	此模块提供了两个接口。 较新的接口支持将 [字节类对象](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) 编码为 ASCII [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)，以及将 [字节类对象](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) 或包含 ASCII 的字符串解码为 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)。 在 [**RFC 4648**](https://datatracker.ietf.org/doc/html/rfc4648.html) 中定义的几种 base-64 字母表（普通的以及 URL 和文件系统安全的）都受到支持。

​	旧的接口不提供从字符串的解码操作，但提供了操作 [文件对象](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-object) 的编码和解码函数。旧接口只支持标准的 Base64 字母表，并且按照 [**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.html) 的规范每 76 个字符增加一个换行符。注意：如果你需要支持 [**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.html)，那么使用 [`email`](https://docs.python.org/zh-cn/3.13/library/email.html#module-email) 模块可能更加合适。

*在 3.3 版本发生变更:* 新的接口提供的解码函数现在已经支持只包含 ASCII 的 Unicode 字符串。

*在 3.4 版本发生变更:* 所有 [类字节对象](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) 现在已经被所有编码和解码函数接受。添加了对 Ascii85/Base85 的支持。

​	新的接口提供：

## base64.**b64encode**(*s*, *altchars=None*)

​	对 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) *s* 进行 Base64 编码，并返回编码后的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)。

​	可选项 *altchars* 必须是一个长度为 2 的 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object)，它指定了用于替换 `+` 和 `/` 的字符表。 这允许应用程序生成对 URL 或文件系统安全的 Base64 字符串。 默认值为 `None`，即使用标准 Base64 字符表。

​	如果 *altchars* 的长度不为 2 则可以断言或引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。 如果 *altchars* 不是 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) 则会引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。

## base64.**b64decode**(*s*, *altchars=None*, *validate=False*)

​	解码 Base64 编码过的 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) 或 ASCII 字符串 *s* 并返回解码过的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)。

​	可选项 *altchars* 必须是一个长度为 2 的 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) 或 ASCII 字符串，它指定了用于替换 `+` 和 `/` 的字符表。

​	如果 *s* 被不正确地填写，一个 [`binascii.Error`](https://docs.python.org/zh-cn/3.13/library/binascii.html#binascii.Error) 错误将被抛出。

​	如果 *validate* 值为 `False` （默认情况），则在填充检查前，将丢弃既不在标准 base-64 字母表之中也不在备用字母表中的字符。如果 *validate* 为 `True`，这些非 base64 字符将导致 [`binascii.Error`](https://docs.python.org/zh-cn/3.13/library/binascii.html#binascii.Error)。

​	有关严格 base64 检查的详情，请参阅 [`binascii.a2b_base64()`](https://docs.python.org/zh-cn/3.13/library/binascii.html#binascii.a2b_base64)

​	如果 *altchars* 不为 2 则可以断言设定或引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

## base64.**standard_b64encode**(*s*)

​	编码 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) *s*，使用标准 Base64 字母表并返回编码过的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)。

## base64.**standard_b64decode**(*s*)

​	解码 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) 或 ASCII 字符串 *s*，使用标准 Base64 字母表并返回编码过的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)。

## base64.**urlsafe_b64encode**(*s*)

​	编码 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) *s*，使用 URL 与文件系统安全的字母表，使用 `-` 以及 `_` 代替标准 Base64 字母表中的 `+` 和 `/`。返回编码过的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)。结果中可能包含 `=`。

## base64.**urlsafe_b64decode**(*s*)

​	解码 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) 或 ASCII 字符串 *s*，使用 URL 与文件系统安全的字母表，使用 `-` 以及 `_` 代替标准 Base64 字母表中的 `+` 和 `/`。返回解码过的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)

## base64.**b32encode**(*s*)

​	用 Base32 编码 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) *s* 并返回编码过的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)

## base64.**b32decode**(*s*, *casefold=False*, *map01=None*)

​	解码 Base32 编码过的 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) 或 ASCII 字符串 *s* 并返回解码过的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)。

​	可选的 *casefold* 是一个指定小写字幕是否可接受为输入的标志。为了安全考虑，默认值为 `False`。

[**RFC 4648**](https://datatracker.ietf.org/doc/html/rfc4648.html) 允许可以选择将数码 0 (zero) 映射为字母 O (oh)，并可以选择将数码 1 (one) 映射为字母 I (eye) 或字母 L (el)。 可选参数 *map01* 在不为 `None` 时，指定数码 1 应当映射为哪个字母 (当 *map01* 不为 `None` 时，数码 0 总是被映射为字母 O)。 出于安全考虑其默认值为 `None`，因而在输入中不允许 0 和 1。

​	如果 *s* 被错误地填写或输入中存在字母表之外的字符，将抛出 [`binascii.Error`](https://docs.python.org/zh-cn/3.13/library/binascii.html#binascii.Error)。

## base64.**b32hexencode**(*s*)

​	类似于 [`b32encode()`](https://docs.python.org/zh-cn/3.13/library/base64.html#base64.b32encode) 但是使用 Extended Hex Alphabet，如 [**RFC 4648**](https://datatracker.ietf.org/doc/html/rfc4648.html) 所定义。

*Added in version 3.10.*

## base64.**b32hexdecode**(*s*, *casefold=False*)

​	类似于 [`b32decode()`](https://docs.python.org/zh-cn/3.13/library/base64.html#base64.b32decode) 但是使用 Extended Hex Alphabet，如 [**RFC 4648**](https://datatracker.ietf.org/doc/html/rfc4648.html) 所定义。

​	这个版本不允许数字 0（零）与字母 O（oh）和数字 1（一）与字母 I（eye）或字母 L （el）的映射，所有这些字符都包含在扩展的十六进制字母表中，不能互换。

*Added in version 3.10.*

## base64.**b16encode**(*s*)

​	用 Base16 编码 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) *s* 并返回编码过的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)

## base64.**b16decode**(*s*, *casefold=False*)

​	解码 Base16 编码过的 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) 或 ASCII 字符串 *s* 并返回解码过的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)。

​	可选的 *casefold* 是一个指定小写字幕是否可接受为输入的标志。为了安全考虑，默认值为 `False`。

​	如果 *s* 被错误地填写或输入中存在字母表之外的字符，将抛出 [`binascii.Error`](https://docs.python.org/zh-cn/3.13/library/binascii.html#binascii.Error)。

## base64.**a85encode**(*b*, ***, *foldspaces=False*, *wrapcol=0*, *pad=False*, *adobe=False*)

​	用 Ascii85 编码 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) *s* 并返回编码过的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)

*foldspaces* 是一个可选的标志，使用特殊的短序列 'y' 代替 'btoa' 提供的 4 个连续空格 (ASCII 0x20)。这个特性不被 "标准" Ascii85 编码支持。

*wrapcol* 控制输出是否应当添加换行符 (`b'\n'`)。 如其为非零值，则每个输出行将只有该值所限定长度的字符数量，不包括末尾换行符。

*pad* 控制在编码之前输入是否填充为4的倍数。请注意，`btoa` 实现总是填充。

*adobe* 控制编码后的字节序列是否要加上 `<~` 和 `~>`，这是 Adobe 实现所使用的。

*Added in version 3.4.*

## base64.**a85decode**(*b*, ***, *foldspaces=False*, *adobe=False*, *ignorechars=b' \t\n\r\x0b'*)

​	解码 Ascii85 编码过的 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) 或 ASCII 字符串 *s* 并返回解码过的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)。

*foldspaces* 旗标指明是否应接受 'y' 短序列作为 4 个连续空格 (ASCII 0x20) 的快捷方式。 此特性不被 "标准" Ascii85 编码格式所支持。

*adobe* 控制输入序列是否为 Adobe Ascii85 格式 (即附加 <~ 和 ~>)。

*ignorechars* 应当是一个 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) 或 ASCII 字符串，其中包含要从输入中忽略的字符。 这应当只包含空白字符，并且默认包含 ASCII 中所有的空白字符。

*Added in version 3.4.*

## base64.**b85encode**(*b*, *pad=False*)

​	用 base85（如 git 风格的二进制 diff 数据所用格式）编码 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) *b* 并返回编码后的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)。

​	如果 *pad* 为真值，输入将以 `b'\0'` 填充以使其编码前长度为 4 字节的倍数。

*Added in version 3.4.*

## base64.**b85decode**(*b*)

​	解码 base85 编码过的 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) 或 ASCII 字符串 *b* 并返回解码过的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)。 如有必要，填充会被隐式地移除。

*Added in version 3.4.*

## base64.**z85encode**(*s*)

​	使用 Z85 (如在 ZeroMQ 中所使用的) 来编码 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) *s* 并返回已编码的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)。 请参阅 [Z85 规范说明](https://rfc.zeromq.org/spec/32/) 了解详情。

*Added in version 3.13.*

## base64.**z85decode**(*s*)

​	解码 Z85 编码的 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) 或 ASCII 字符串 *s* 并返回已解码的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)。 请参阅 [Z85 规范说明](https://rfc.zeromq.org/spec/32/) 了解详情。

*Added in version 3.13.*

​	旧式接口:

## base64.**decode**(*input*, *output*)

​	解码二进制 *input* 文件的内容并将结果二进制数据写入 *output* 文件。 *input* 和 *output* 必须为 [文件对象](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-object). *input* 将被读取直至 `input.readline()` 返回空字节串对象。

## base64.**decodebytes**(*s*)

​	解码 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) *s*，该对象必须包含一行或多行 base64 编码的数据，并返回已解码的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)。

*Added in version 3.1.*

## base64.**encode**(*input*, *output*)

​	编码二进制 *input* 文件的内容并将经 base64 编码的数据写入 *output* 文件。 *input* 和 *output* 必须为 [文件对象](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-object)。 *input* 将被读取直到 `input.read()` 返回空字节串对象。 [`encode()`](https://docs.python.org/zh-cn/3.13/library/base64.html#base64.encode) 会在每输出 76 个字节之后插入一个换行符 (`b'\n'`)，并会确保输出总是以换行符来结束，如 [**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.html) (MIME) 所规定的那样。

## base64.**encodebytes**(*s*)

​	编码 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) *s*，其中可以包含任意二进制数据，并返回包含经 base64 编码数据的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)，每输出 76 个字节之后将带一个换行符 (`b'\n'`)，并会确保在末尾也有一个换行符，如 [**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.html) (MIME) 所规定的那样。

*Added in version 3.1.*

​	此模块的一个使用示例:



```
>>> import base64
>>> encoded = base64.b64encode(b'data to be encoded')
>>> encoded
b'ZGF0YSB0byBiZSBlbmNvZGVk'
>>> data = base64.b64decode(encoded)
>>> data
b'data to be encoded'
```



## 安全考量

​	在 [**RFC 4648**](https://datatracker.ietf.org/doc/html/rfc4648.html) 中新增了安全事项部分（第 12 节）；对于要部署到生产环境的任何代码都建议充分考虑此安全事项部分。

> 参见
>
> 
>
> - 模块 [`binascii`](https://docs.python.org/zh-cn/3.13/library/binascii.html#module-binascii)
>
>   支持模块，包含ASCII到二进制和二进制到ASCII转换。
>
> - [**RFC 1521**](https://datatracker.ietf.org/doc/html/rfc1521.html) - MIME (Multipurpose Internet Mail Extensions) 第一部分：规定并描述因特网消息体的格式的机制。
>
>   第 5.2 节，“Base64 内容转换编码格式” 提供了 base64 编码格式的定义。
