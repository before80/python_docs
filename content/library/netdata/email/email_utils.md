+++
title = "`email.utils`: 杂项工具"
date = 2024-11-15T12:09:25+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/email.utils.html](https://docs.python.org/zh-cn/3.13/library/email.utils.html)
>
> 收录该文档的时间：`2024-11-15T12:09:25+08:00`

# `email.utils`: 杂项工具

**源代码:** [Lib/email/utils.py](https://github.com/python/cpython/tree/3.13/Lib/email/utils.py)

------

[`email.utils`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#module-email.utils) 模块提供如下几个工具

## email.utils.**localtime**(*dt=None*)

​	将当地时间作为感知型datetime 对象返回。 如果不带参数地调用，则返回当前时间。 否则 *dt* 参数应为 [`datetime`](https://docs.python.org/zh-cn/3.13/library/datetime.html#datetime.datetime) 的实例，并将根据系统时区数据库将其转换为当地时区。 如果 *dt* 为简单型 (即 `dt.tzinfo` 为 `None`)，它将被假定为当地时间。 *isdst* 形参将被忽略。

*Added in version 3.3.*

*Deprecated since version 3.12, will be removed in version 3.14:* *isdst* 形参。

## email.utils.**make_msgid**(*idstring=None*, *domain=None*)

​	返回一个适合作为兼容 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.html) 的 *Message-ID* 标头的字符串。可选参数 *idstring* 可传入一字符串以增强该消息 ID 的唯一性。可选参数 *domain* 可用于提供消息 ID 中字符 '@' 之后的部分，其默认值是本机的主机名。正常情况下无需覆盖此默认值，但在特定情况下覆盖默认值可能会有用，比如构建一个分布式系统，在多台主机上采用一致的域名。

*在 3.2 版本发生变更:* 增加了关键字 *domain*

​	下列函数是旧（`Compat32`）电子邮件 API 的一部分。新 API 提供的解析和格式化在标头解析机制中已经被自动完成，故在使用新 API 时没有必要直接使用它们函数。

## email.utils.**quote**(*str*)

​	返回一个新的字符串， *str* 中的反斜杠被替换为两个反斜杠，并且双引号被替换为反斜杠加双引号。

## email.utils.**unquote**(*str*)

​	返回 *str* 被去除引用版本的字符串。如果 *str* 开头和结尾均是双引号，则这对双引号被去除。类似地，如果 *str* 开头和结尾都是尖角括号，这对尖角括号会被去除。

## email.utils.**parseaddr**(*address*, ***, *strict=True*)

​	将地址（应为诸如 *To* 或者 *Cc* 之类包含地址的字段值）解析为构成之的 *真实名字* 和 *电子邮件地址* 部分。返回包含这两个信息的一个元组；如若解析失败，则返回一个二元组 `('', '')` 。

​	如果 *strict* 为真值，将使用拒绝错误形式输入的严格解析器。

*在 3.13 版本发生变更:* 增加了 *strict* 可选形参并将默认拒绝错误形式输入。

## email.utils.**formataddr**(*pair*, *charset='utf-8'*)

​	是 [`parseaddr()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.parseaddr) 的逆操作，接受一个 `(真实名字, 电子邮件地址)` 的二元组，并返回适合于 *To* or *Cc* 标头的字符串。如果第一个元素为假性值，则第二个元素将被原样返回。

​	可选地，如果指定 *charset*，则被视为一符合 [**RFC 2047**](https://datatracker.ietf.org/doc/html/rfc2047.html) 的编码字符集，用于编码 `真实名字` 中的非 ASCII 字符。可以是一个 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 类的实例，或者一个 [`Charset`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.Charset) 类。默认为 `utf-8` 。

*在 3.3 版本发生变更:* 添加了 *charset* 选项。

## email.utils.**getaddresses**(*fieldvalues*, ***, *strict=True*)

​	该方法返回一个与 `parseaddr()` 返回值形式相同的 2 元组。 *fieldvalues* 是与 [`Message.get_all`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_all) 返回值形式相同的由标头字段值组成的序列。

​	如果 *strict* 为真值，将使用拒绝错误形式输入的严格解析器。

​	下面简单示例可获取一条消息的所有接收方:

```
from email.utils import getaddresses

tos = msg.get_all('to', [])
ccs = msg.get_all('cc', [])
resent_tos = msg.get_all('resent-to', [])
resent_ccs = msg.get_all('resent-cc', [])
all_recipients = getaddresses(tos + ccs + resent_tos + resent_ccs)
```

*在 3.13 版本发生变更:* 增加了 *strict* 可选形参并将默认拒绝错误形式输入。

## email.utils.**parsedate**(*date*)

​	尝试根据 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.html) 的规则解析一个日期。然而，有些寄信人不严格遵守这一格式，所以这种情况下 [`parsedate()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.parsedate) 会尝试猜测其形式。*date* 是一个字符串包含了一个形如 `"Mon, 20 Nov 1995 19:12:08 -0500"` 的 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.html) 格式日期。如果日期解析成功， [`parsedate()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.parsedate) 将返回一个九元组，可直接传递给 [`time.mktime()`](https://docs.python.org/zh-cn/3.13/library/time.html#time.mktime)；否则返回 `None`。注意返回的元组中下标为 6、7、8 的部分是无用的。

## email.utils.**parsedate_tz**(*date*)

​	执行与 [`parsedate()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.parsedate) 相同的功能，但会返回 `None` 或是一个 10 元组；前 9 个元素构成一个可以直接传给 [`time.mktime()`](https://docs.python.org/zh-cn/3.13/library/time.html#time.mktime) 的元组，而第十个元素则是该日期的时区与 UTC (格林威治平均时 GMT 的正式名称) [[1\]](https://docs.python.org/zh-cn/3.13/library/email.utils.html#id2) 的时差。 如果输入字符串不带时区，则所返回元组的最后一个元素将为 `0`，这表示 UTC。 请注意结果元组的索引号 6, 7 和 8 是不可用的。

## email.utils.**parsedate_to_datetime**(*date*)

[`format_datetime()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.format_datetime) 的逆操作。 执行与 [`parsedate()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.parsedate) 相同的功能，但会在成功时返回一个 [`datetime`](https://docs.python.org/zh-cn/3.13/library/datetime.html#datetime.datetime)；否则如果 *date* 包含无效的值例如小时值大于 23 或时区偏移量不在 -24 和 24 时范围之内则会引发 `ValueError`。 如果输入日期的时区值为 `-0000`，则 `datetime` 将为一个简单形 `datetime`，而如果日期符合 RFC 标准则它将代表一个 UTC 时间，但是并不指明日期所在消息的实际源时区。 如果输入日期具有任何其他有效的时区偏移量，则 `datetime` 将是一个感知型 `datetime` 并与 [`timezone`](https://docs.python.org/zh-cn/3.13/library/datetime.html#datetime.timezone) [`tzinfo`](https://docs.python.org/zh-cn/3.13/library/datetime.html#datetime.tzinfo) 相对应。

*Added in version 3.3.*

## email.utils.**mktime_tz**(*tuple*)

​	将 [`parsedate_tz()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.parsedate_tz) 所返回的 10 元组转换为一个 UTC 时间戳（相距 Epoch 纪元初始的秒数）。 如果元组中的时区项为 `None`，则视为当地时间。

## email.utils.**formatdate**(*timeval=None*, *localtime=False*, *usegmt=False*)

​	返回 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.html) 标准的日期字符串，例如:

```
Fri, 09 Nov 2001 01:08:47 -0000
```

​	可选的 *timeval* 如果给出，则是一个可被 [`time.gmtime()`](https://docs.python.org/zh-cn/3.13/library/time.html#time.gmtime) 和 [`time.localtime()`](https://docs.python.org/zh-cn/3.13/library/time.html#time.localtime) 接受的浮点数时间值，否则会使用当前时间。

​	可选的 *localtime* 是一个旗标，当为 `True` 时，将会解析 *timeval*，并返回一个相对于当地时区而非 UTC 的日期值，并会适当地考虑夏令时。 默认值 `False` 表示使用 UTC。

​	可选的 *usegmt* 是一个旗标，当为 `True` 时，将会输出一个日期字符串，其中时区表示为 ascii 字符串 `GMT` 而非数字形式的 `-0000`。 这对某些协议（例如 HTTP）来说是必要的。 这仅在 *localtime* 为 `False` 时应用。 默认值为 `False`。

## email.utils.**format_datetime**(*dt*, *usegmt=False*)

​	类似于 `formatdate`，但输入的是一个 [`datetime`](https://docs.python.org/zh-cn/3.13/library/datetime.html#module-datetime) 实例。 如果实例是一个简单型 datetime，它会被视为 "不带源时区信息的 UTC"，并且使用传统的 `-0000` 作为时区。 如果实例是一个感知型 `datetime`，则会使用数字形式的时区时差。 如果实例是感知型且时区时差为零，则 *usegmt* 可能会被设为 `True`，在这种情况下将使用字符串 `GMT` 而非数字形式的时区时差。 这提供了一种生成符合标准 HTTP 日期标头的方式。

*Added in version 3.3.*

## email.utils.**decode_rfc2231**(*s*)

​	根据 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.html) 解码字符串 *s*。

## email.utils.**encode_rfc2231**(*s*, *charset=None*, *language=None*)

​	根据 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.html) 对字符串 *s* 进行编码。 可选的 *charset* 和 *language* 如果给出，则为指明要使用的字符集名称和语言名称。 如果两者均未给出，则会原样返回 *s*。 如果给出 *charset* 但未给出 *language*，则会使用空字符串作为 *language* 值来对字符串进行编码。

## email.utils.**collapse_rfc2231_value**(*value*, *errors='replace'*, *fallback_charset='us-ascii'*)

​	当以 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.html) 格式来编码标头形参时，[`Message.get_param`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_param) 可能返回一个包含字符集、语言和值的 3 元组。 [`collapse_rfc2231_value()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.collapse_rfc2231_value) 会将此返回为一个 unicode 字符串。 可选的 *errors* 会被传递给 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 的 [`encode()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str.encode) 方法的 *errors* 参数；它的默认值为 `'replace'`。 可选的 *fallback_charset* 指定当 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.html) 标头中的字符集无法被 Python 识别时要使用的字符集；它的默认值为 `'us-ascii'`。

​	为方便起见，如果传给 [`collapse_rfc2231_value()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.collapse_rfc2231_value) 的 *value* 不是一个元组，则应为一个字符串并会将其原样返回。

## email.utils.**decode_params**(*params*)

​	根据 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.html) 解码参数列表。 *params* 是一个包含 `(content-type, string-value)` 形式的元素的 2 元组的序列。

​	备注

[[1](https://docs.python.org/zh-cn/3.13/library/email.utils.html#id1)]

​	请注意时区时差的符号与同一时区的 `time.timezone` 变量的符号相反；后者遵循 POSIX 标准而此模块遵循 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.html)。