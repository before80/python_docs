+++
title = "`email.message.Message`: 使用 `compat32` API 来表示电子邮件消息"
date = 2024-11-15T12:09:25+08:00
weight = 80
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html)
>
> 收录该文档的时间：`2024-11-15T12:09:25+08:00`

# `email.message.Message`: 使用`compat32` API 来表示电子邮件消息

[`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 类与 [`EmailMessage`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage) 类非常相似，但没有该类所添加的方法，并且某些方法的默认行为也略有不同。 我们还在这里记录了一些虽然被 [`EmailMessage`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage) 类所支持但并不推荐的方法，除非你是在处理旧有代码。

​	在其他情况下这两个类的理念和结构都是相同的。

​	本文档描述了默认 (对于 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message)) 策略 [`Compat32`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Compat32) 之下的行为。 如果你要使用其他策略，你应当改用 [`EmailMessage`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage) 类。

​	电子邮件消息由多个 *标头* 和一个 *载荷* 组成。 标头必须为 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.html) 风格的名称和值，其中字典名和值由冒号分隔。 冒号不是字段名或字段值的组成部分。 载荷可以是简单的文本消息，或是二进制对象，或是多个子消息的结构化序列，每个子消息都有自己的标头集合和自己的载荷。 后一种类型的载荷是由具有 `multipart/*` 或 `message/rfc822` 等 MIME 类型的消息来指明的。

[`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 对象所提供了概念化模型是由标头组成的有序字典，加上用于访问标头中的特殊信息以及访问载荷的额外方法，以便能生成消息的序列化版本，并递归地遍历对象树。 请注意重复的标头是受支持的，但必须使用特殊的方法来访问它们。

[`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 伪字典以标头名作为索引，标头名必须为 ASCII 值。 字典的值为应当只包含 ASCII 字符的字符串；对于非 ASCII 输入有一些特殊处理，但这并不总能产生正确的结果。 标头以保留原大小写的形式存储和返回，但字段名称匹配对大小写不敏感。 还可能会有一个单独的封包标头，也称 *Unix-From* 标头或 `From_` 标头。 *载荷* 对于简单消息对象的情况是一个字符串或字节串，对于 MIME 容器文档的情况 (例如 `multipart/*` 和 `message/rfc822`) 则是一个 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 对象。

​	以下是 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 类的方法:

## *class* email.message.**Message**(*policy=compat32*)

​	如果指定了 *policy* (它必须为 [`policy`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#module-email.policy) 类的实例) 则使用它所设置的规则来更新和序列化消息的表示形式。 如果未设置 *policy*，则使用 [`compat32`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Compat32) 策略，该策略会保持对 Python 3.2 版 email 包的向下兼容性。 更多信息请参阅 [`policy`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#module-email.policy) 文档。

*在 3.3 版本发生变更:* 增加了 *policy* 关键字参数。

## **as_string**(*unixfrom=False*, *maxheaderlen=0*, *policy=None*)

​	以展平的字符串形式返回整个消息对象。 或可选的 *unixfrom* 为真值，返回的字符串会包括封包标头。 *unixfrom* 的默认值是 `False`。 出于保持向下兼容性的原因，*maxheaderlen* 的默认值是 `0`，因此如果你想要不同的值你必须显式地重写它（在策略中为 *max_line_length* 指定的值将被此方法忽略）。 *policy* 参数可被用于覆盖从消息实例获取的默认策略。 这可以用来对该方法所输出的格式进行一些控制，因为指定的 *policy* 将被传递给 `Generator`。

​	如果需要填充默认值以完成对字符串的转换则展平消息可能触发对 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 的修改（例如，MIME 边界可能会被生成或被修改）。

​	请注意此方法是出于便捷原因提供的，并可能无法总是以你想要的方式来格式化消息。 例如，在默认情况下它不会按 Unix mbox 格式的要求对以 `From` 打头的行执行调整。 为了获得更高灵活性，请实例化一个 [`Generator`](https://docs.python.org/zh-cn/3.13/library/email.generator.html#email.generator.Generator) 实例并直接使用其 [`flatten()`](https://docs.python.org/zh-cn/3.13/library/email.generator.html#email.generator.Generator.flatten) 方法。 例如:

```
from io import StringIO
from email.generator import Generator
fp = StringIO()
g = Generator(fp, mangle_from_=True, maxheaderlen=60)
g.flatten(msg)
text = fp.getvalue()
```

​	如果消息对象包含未按照 RFC 标准进行编码的二进制数据，则这些不合规数据将被 unicode "unknown character" 码位值所替代。 （另请参阅 [`as_bytes()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.as_bytes) 和 [`BytesGenerator`](https://docs.python.org/zh-cn/3.13/library/email.generator.html#email.generator.BytesGenerator)。）

*在 3.4 版本发生变更:* 增加了 *policy* 关键字参数。

## **__str__**()

​	等价于 [`as_string()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.as_string)。 让 `str(msg)` 产生一个包含已格式化消息的字符串。

## **as_bytes**(*unixfrom=False*, *policy=None*)

​	以字节串对象的形式返回整个扁平化后的消息。 当可选的 *unixfrom* 为真值时，返回的字符串会包括封包标头。 *unixfrom* 的默认值为 `False`。 *policy* 参数可被用于覆盖从消息实例获取的默认策略。 这可被用来控制该方法所产生的部分格式化效果，因为指定的 *policy* 将被传递给 `BytesGenerator`。

​	如果需要填充默认值以完成对字符串的转换则展平消息可能触发对 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 的修改（例如，MIME 边界可能会被生成或被修改）。

​	请注意此方法是出于便捷原因提供的，并可能无法总是以你想要的方式来格式化消息。 例如，在默认情况下它不会按 Unix mbox 格式的要求对以 `From` 打头的行执行调整。 为了获得更高灵活性，请实例化一个 [`BytesGenerator`](https://docs.python.org/zh-cn/3.13/library/email.generator.html#email.generator.BytesGenerator) 实例并直接使用其 [`flatten()`](https://docs.python.org/zh-cn/3.13/library/email.generator.html#email.generator.BytesGenerator.flatten) 方法。 例如:

```
from io import BytesIO
from email.generator import BytesGenerator
fp = BytesIO()
g = BytesGenerator(fp, mangle_from_=True, maxheaderlen=60)
g.flatten(msg)
text = fp.getvalue()
```

*Added in version 3.4.*

## **__bytes__**()

​	等价于 [`as_bytes()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.as_bytes)。 让 `bytes(msg)` 产生一个包含已格式化消息的字节串对象。

*Added in version 3.4.*

## **is_multipart**()

​	如果该消息的载荷是一个子 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 对象列表则返回 `True`，否则返回 `False`。 当 [`is_multipart()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.is_multipart) 返回 `False` 时，载荷应当是一个字符串对象（有可能是一个 CTE 编码的二进制载荷）。 （请注意 [`is_multipart()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.is_multipart) 返回 `True` 并不意味着 "msg.get_content_maintype() == 'multipart'" 将返回 `True`。 例如，`is_multipart` 在 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 类型为 `message/rfc822` 时也将返回 `True`。）

## **set_unixfrom**(*unixfrom*)

​	将消息的封包标头设为 *unixfrom*，这应当是一个字符串。

## **get_unixfrom**()

​	返回消息的信封头。如果信封头从未被设置过，默认返回 `None` 。

## **attach**(*payload*)

​	将给定的 *payload* 添加到当前载荷中，当前载荷在该调用之前必须为 `None` 或是一个 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 对象列表。 在调用之后，此载荷将总是一个 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 对象列表。 如果你想将此载荷设为一个标量对象（如字符串），请改用 [`set_payload()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.set_payload)。

​	这是一个过时的方法。 在 `EmailMessage` 类上它的功能已被 [`set_content()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.set_content) 及相应的 `make` 和 `add` 方法所替代。

## **get_payload**(*i=None*, *decode=False*)

​	返回当前的载荷，它在 [`is_multipart()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.is_multipart) 为 `True` 时将是一个 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 对象列表，在 [`is_multipart()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.is_multipart) 为 `False` 时则是一个字符串。 如果该载荷是一个列表且你修改了这个列表对象，那么你就是原地修改了消息的载荷。

​	传入可选参数 *i* 时，如果 [`is_multipart()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.is_multipart) 为 `True`，[`get_payload()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_payload) 将返回载荷从零开始计数的第 *i* 个元素。 如果 *i* 小于 0 或大于等于载荷中的条目数则将引发 [`IndexError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#IndexError)。 如果载荷是一个字符串 (即 [`is_multipart()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.is_multipart) 为 `False`) 且给出了 *i*，则会引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。

​	可选的 *decode* 是一个指明载荷是否应根据 *Content-Transfer-Encoding* 标头被解码的旗标。 当其值为 `True` 且消息没有多个部分时，如果此标头值为 `quoted-printable` 或 `base64` 则载荷将被解码。 如果使用了其他编码格式，或者找不到 *Content-Transfer-Encoding* 标头时，载荷将被原样返回（不编码）。 在所有情况下返回值都是二进制数据。 如果消息有多个部分且 *decode* 旗标为 `True`，则将返回 `None`。 如果载荷为 base64 但内容不完全正确（如缺少填充符、存在 base64 字母表以外的字符等），则将在消息的缺陷属性中添加适当的缺陷值 (分别为 `InvalidBase64PaddingDefect` 或 `InvalidBase64CharactersDefect`)。

​	当 *decode* 为 `False` (默认值) 时消息体会作为字符串返回而不解码 *Content-Transfer-Encoding*。 但是，对于 *Content-Transfer-Encoding* 为 8bit 的情况，会尝试使用 *Content-Type* 标头指定的 `charset` 来解码原始字节串，并使用 `replace` 错误处理程序。 如果未指定 `charset`，或者如果指定的 `charset` 未被 email 包所识别，则会使用默认的 ASCII 字符集来解码消息体。

​	这是一个过时的方法。 在 `EmailMessage` 类上它的功能已被 [`get_content()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.get_content) 和 [`iter_parts()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.iter_parts) 方法所替代。

## **set_payload**(*payload*, *charset=None*)

​	将整个消息对象的载荷设为 *payload*。 客户端要负责确保载荷的不变性。 可选的 *charset* 用于设置消息的默认字符集；详情请参阅 [`set_charset()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.set_charset)。

​	这是一个过时的方法。 在 `EmailMessage` 类上它的功能已被 [`set_content()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.set_content) 方法所替代。

## **set_charset**(*charset*)

​	将载荷的字符集设为 *charset*，它可以是 [`Charset`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.Charset) 实例 (参见 [`email.charset`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#module-email.charset))、字符集名称字符串或 `None`。 如果是字符串，它将被转换为一个 [`Charset`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.Charset) 实例。 如果 *charset* 是 `None`，`charset` 形参将从 *Content-Type* 标头中被删除（消息将不会进行其他修改）。 任何其他值都将导致 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。

​	如果 *MIME-Version* 标头不存在则将被添加。 如果 *Content-Type* 标头不存在，则将添加一个值为 *text/plain* 的该标头。 无论 *Content-Type* 标头是否已存在，其 `charset` 形参都将被设为 *charset.output_charset*。 如果 *charset.input_charset* 和 *charset.output_charset* 不同，则载荷将被重编码为 *output_charset*。 如果 *Content-Transfer-Encoding* 标头不存在，则载荷将在必要时使用指定的 [`Charset`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.Charset) 来转换编码，并将添加一个具有相应值的标头。 如果 *Content-Transfer-Encoding* 标头已存在，则会假定载荷已使用该 *Content-Transfer-Encoding* 进行正确编码并不会再被修改。

​	这是一个过时的方法。 在 `EmailMessage` 类上它的功能已被 `email.emailmessage.EmailMessage.set_content()` 方法的 *charset* 形参所替代。

## **get_charset**()

​	返回与消息的载荷相关联的 [`Charset`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.Charset) 实例。

​	这是一个过时的方法。 在 `EmailMessage` 类上它将总是返回 `None`。

​	以下方法实现了用于访问消息的 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.html) 标头的类映射接口。 请注意这些方法和普通映射（例如字典）接口之间存在一些语义上的不同。 举例来说，在一个字典中不能有重复的键，但消息标头则可能有重复。 并且，在字典中由 [`keys()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.keys) 返回的键的顺序是没有保证的，但在 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 对象中，标头总是会按它们在原始消息中的出现或后继加入顺序返回。 任何已删除再重新加入的标头总是会添加到标头列表的末尾。

​	这些语义上的差异是有意为之且其目的是为了提供最大的便利性。

​	请注意在任何情况下，消息当中的任何封包标头都不会包含在映射接口当中。

​	在由字符串生成的模型中，任何包含非 ASCII 字节数据（违反 RFC）的标头值在通过此接口来获取时，将被表示为使用 `unknown-8bit` 字符集的 [`Header`](https://docs.python.org/zh-cn/3.13/library/email.header.html#email.header.Header) 对象。

## **__len__**()

​	返回标头的总数，包括重复项。

## **__contains__**(*name*)

​	如果消息对象中有一个名为 *name* 的字段则返回 `True`。 匹配操作对大小写不敏感并且 *name* 不应包括末尾的冒号。 用于 `in` 运算符，例如:

```
if 'message-id' in myMessage:
   print('Message-ID:', myMessage['message-id'])
```

## **__getitem__**(*name*)

​	返回指定名称标头字段的值。 *name* 不应包括作为字段分隔符的冒号。 如果标头未找到，则返回 `None`；[`KeyError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#KeyError) 永远不会被引发。

​	请注意如果指定名称的字段在消息标头中多次出现，具体将返回哪个字段值是未定义的。 请使用 [`get_all()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_all) 方法来获取所有指定名称标头的值。

## **__setitem__**(*name*, *val*)

​	将具有字段名 *name* 和值 *val* 的标头添加到消息中。 字段会被添加到消息的现有字段的末尾。

​	请注意，这个方法 *既不会* 覆盖 *也不会* 删除任何字段名重名的已有字段。如果你确实想保证新字段是整个信息头当中唯一拥有 *name* 字段名的字段，你需要先把旧字段删除。例如：

```
del msg['subject']
msg['subject'] = 'Python roolz!'
```

## **__delitem__**(*name*)

​	删除信息头当中字段名匹配 *name* 的所有字段。如果匹配指定名称的字段没有找到，也不会抛出任何异常。

## **keys**()

​	以列表形式返回消息头中所有的字段名。

## **values**()

​	以列表形式返回消息头中所有的字段值。

## **items**()

​	以二元元组的列表形式返回消息头中所有的字段名和字段值。

## **get**(*name*, *failobj=None*)

​	返回对应标头字段名的值。 这个方法与 [`__getitem__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__getitem__) 是一样的，只是如果对应标头不存在则返回可选的 *failobj* (默认为 `None`)。

​	以下是一些有用的附加方法:

## **get_all**(*name*, *failobj=None*)

​	返回字段名为 *name* 的所有字段值的列表。如果信息内不存在匹配的字段，返回 *failobj* （其默认值为 `None` ）。

## **add_header**(*_name*, *_value*, ***_params*)

​	高级头字段设定。这个方法与 [`__setitem__()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.__setitem__) 类似，不过你可以使用关键字参数为字段提供附加参数。 *_name* 是字段名， *_value* 是字段 *主* 值。

​	对于关键字参数字典 *_params* 中的每一项，其键会被当作形参名，并执行下划线和连字符间的转换（因为连字符不是合法的 Python 标识符）。 通常，形参将以 `key="value"` 的形式添加，除非值为 `None`，在这种情况下将只添加键。 如果值包含非 ASCII 字符，可将其指定为格式为 `(CHARSET, LANGUAGE, VALUE)` 的三元组，其中 `CHARSET` 为要用来编码值的字符集名称字符串，`LANGUAGE` 通常可设为 `None` 或空字符串（请参阅 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.html) 了解其他可能的取值），而 `VALUE` 为包含非 ASCII 码位的字符串值。 如果不是传入一个三元组且值包含非 ASCII 字符，则会自动以 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.html) 格式使用 `CHARSET` 为 `utf-8` 和 `LANGUAGE` 为 `None` 对其进行编码。

​	以下是为示例代码:

```
msg.add_header('Content-Disposition', 'attachment', filename='bud.gif')
```

​	会添加一个形如下文的头字段：

```
Content-Disposition: attachment; filename="bud.gif"
```

​	使用非 ASCII 字符的示例代码:

```
msg.add_header('Content-Disposition', 'attachment',
               filename=('iso-8859-1', '', 'Fußballer.ppt'))
```

​	它的输出结果为

```
Content-Disposition: attachment; filename*="iso-8859-1''Fu%DFballer.ppt"
```

## **replace_header**(*_name*, *_value*)

​	替换一个标头。 将替换在匹配 *_name* 的消息中找到的第一个标头，标头顺序和字段名大小写保持不变。 如果未找到匹配的标头，则会引发 [`KeyError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#KeyError)。

## **get_content_type**()

​	返回消息的内容类型。 返回的字符串会强制转换为 *maintype/subtype* 的全小写形式。 如果消息中没有 *Content-Type* 标头则将返回由 [`get_default_type()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_default_type) 给出的默认类型。 因为根据 [**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.html)，消息总是要有一个默认类型，所以 [`get_content_type()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_content_type) 将总是返回一个值。

[**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.html) 将消息的默认类型定义为 *text/plain*，除非它是出现在 *multipart/digest* 容器内，在这种情况下其类型应为 *message/rfc822*。 如果 *Content-Type* 标头指定了无效的类型，[**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.html) 规定其默认类型应为 *text/plain*。

## **get_content_maintype**()

​	返回信息的主要内容类型。准确来说，此方法返回的是 [`get_content_type()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_content_type) 方法所返回的形如 *maintype/subtype* 的字符串当中的 *maintype* 部分。

## **get_content_subtype**()

​	返回信息的子内容类型。准确来说，此方法返回的是 [`get_content_type()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_content_type) 方法所返回的形如 *maintype/subtype* 的字符串当中的 *subtype* 部分。

## **get_default_type**()

​	返回默认的内容类型。绝大多数的信息，其默认内容类型都是 *text/plain* 。作为 *multipart/digest* 容器内子部分的信息除外，它们的默认内容类型是 *message/rfc822* 。

## **set_default_type**(*ctype*)

​	设置默认的内容类型。 *ctype* 应当为 *text/plain* 或者 *message/rfc822*，尽管这并非强制。 默认的内容类型不会存储在 *Content-Type* 标头中。

## **get_params**(*failobj=None*, *header='content-type'*, *unquote=True*)

​	将消息的 *Content-Type* 形参作为列表返回。 所返回列表的元素为以 `'='` 号拆分出的键/值对 2 元组。 `'='` 左侧的为键，右侧的为值。 如果形参值中没有 `'='` 号，否则该将值如 [`get_param()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_param) 描述并且在可选 *unquote* 为 `True` (默认值) 时会被取消转义。

​	可选的 *failobj* 是在没有 *Content-Type* 标头时要返回的对象。 可选的 *header* 是要替代 *Content-Type* 被搜索的标头。

​	这是一个过时的方法。 在 `EmailMessage` 类上它的功能已被标头访问方法所返回的单独标头对象的 *params* 特征属性所替代。

## **get_param**(*param*, *failobj=None*, *header='content-type'*, *unquote=True*)

​	将 *Content-Type* 标头的形参 *param* 作为字符串返回。 如果消息没有 *Content-Type* 标头或者没有这样的形参，则返回 *failobj* (默认为 `None`)。

​	如果给出可选的 *header*，它会指定要替代 *Content-Type* 来使用的消息标头。

​	形参的键总是以大小写不敏感的方式来比较的。 返回值可以是一个字符串，或者如果形参以 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.html) 编码则是一个 3 元组。 当为 3 元组时，值中的元素采用 `(CHARSET, LANGUAGE, VALUE)` 的形式。 请注意 `CHARSET` 和 `LANGUAGE` 都可以为 `None`，在此情况下你应当将 `VALUE` 当作以 `us-ascii` 字符集来编码。 你可以总是忽略 `LANGUAGE`。

​	如果你的应用不关心形参是否以 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.html) 来编码，你可以通过调用 [`email.utils.collapse_rfc2231_value()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.collapse_rfc2231_value) 来展平形参值，传入来自 [`get_param()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_param) 的返回值。 当值为元组时这将返回一个经适当编码的 Unicode 字符串，否则返回未经转换的原字符串。 例如:

```
rawparam = msg.get_param('foo')
param = email.utils.collapse_rfc2231_value(rawparam)
```

​	无论在哪种情况下，形参值（或为返回的字符串，或为 3 元组形式的 `VALUE` 条目）总是未经转换的，除非 *unquote* 被设为 `False`。

​	这是一个过时的方法。 在 `EmailMessage` 类上它的功能已被标头访问方法所返回的单独标头对象的 *params* 特征属性所替代。

## **set_param**(*param*, *value*, *header='Content-Type'*, *requote=True*, *charset=None*, *language=''*, *replace=False*)

​	在 *Content-Type* 标头中设置一个形参。 如果该形参已存在于标头中，它的值将被替换为 *value*。 如果此消息还未定义 *Content-Type* 标头，它将被设为 *text/plain* 且新的形参值将按 [**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.html) 的要求添加。

​	可选的 *header* 指定一个 *Content-Type* 的替代标头，并且所有形参将根据需要被转换，除非可选的 *requote* 为 `False` (默认为 `True`)。

​	如果指定了可选的 *charset*，形参将按照 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.html) 来编码。 可选的 *language* 指定了 RFC 2231 的语言，默认为空字符串。 *charset* 和 *language* 都应为字符串。

​	如果 *replace* 为 `False` （默认值），该头字段会被移动到所有头字段列表的末尾。如果 *replace* 为 `True` ，字段会被原地更新。

*在 3.4 版本发生变更:* 添加了 `replace` 关键字。

## **del_param**(*param*, *header='content-type'*, *requote=True*)

​	从 *Content-Type* 标头中完全移除给定的形参。 标头将被原地重写并不带该形参或它的值。 所有的值将根据需要被转换，除非 *requote* 为 `False` (默认为 `True`)。 可选的 *header* 指定 *Content-Type* 的一个替代项。

## **set_type**(*type*, *header='Content-Type'*, *requote=True*)

​	设置 *Content-Type* 标头的主类型和子类型。 *type* 必须为 *maintype/subtype* 形式的字符串，否则会引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

​	此方法可替换 *Content-Type* 标头，并保持所有形参不变。 如果 *requote* 为 `False`，这会保持原有标头引用转换不变，否则形参将被引用转换（默认行为）。

​	可以在 *header* 参数中指定一个替代标头。 当 *Content-Type* 标头被设置时也会添加一个 *MIME-Version* 标头。

​	这是一个过时的方法。 在 `EmailMessage` 类上它的功能已被 `make_` 和 `add_` 方法所替代。

## **get_filename**(*failobj=None*)

​	返回信息头当中 *Content-Disposition* 字段当中名为 `filename` 的参数值。如果该字段当中没有此参数，该方法会退而寻找 *Content-Type* 字段当中的 `name` 参数值。如果这个也没有找到，或者这些个字段压根就不存在，返回 *failobj* 。返回的字符串永远按照 [`email.utils.unquote()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.unquote) 方法去除引号。

## **get_boundary**(*failobj=None*)

​	返回信息头当中 *Content-Type* 字段当中名为 `boundary` 的参数值。如果字段当中没有此参数，或者这些个字段压根就不存在，返回 *failobj* 。返回的字符串永远按照 [`email.utils.unquote()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.unquote) 方法去除引号。

## **set_boundary**(*boundary*)

​	将 *Content-Type* 头字段的 `boundary` 参数设置为 *boundary* 。 [`set_boundary()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.set_boundary) 方法永远都会在必要的时候为 *boundary* 添加引号。如果信息对象中没有 *Content-Type* 头字段，抛出 [`HeaderParseError`](https://docs.python.org/zh-cn/3.13/library/email.errors.html#email.errors.HeaderParseError) 异常。

​	请注意使用这个方法与删除旧的 *Content-Type* 标头并通过 [`add_header()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.add_header) 添加一个带有新边界的新标头有细微的差异，因为 [`set_boundary()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.set_boundary) 会保留 *Content-Type* 标头在原标头列表中的顺序。 但是，它 *不会* 保留原 *Content-Type* 标头中可能存在的任何连续的行。

## **get_content_charset**(*failobj=None*)

​	返回 *Content-Type* 头字段中的 `charset` 参数，强制小写。如果字段当中没有此参数，或者这个字段压根不存在，返回 *failobj* 。

​	请注意此方法不同于 [`get_charset()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_charset)，后者会返回 [`Charset`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.Charset) 实例作为消息体的默认编码格式。

## **get_charsets**(*failobj=None*)

​	返回一个包含了信息内所有字符集名字的列表。 如果信息是 *multipart* 类型的，那么列表当中的每一项都对应其载荷的子部分的字符集名字。 否则，该列表是一个长度为 1 的列表。

​	列表中的每一项都是字符串，它们是其所表示的子部分的 *Content-Type* 标头中 `charset` 形参的值。 但是，如果该子部分没有 *Content-Type* 标头，或没有 `charset` 形参，或者主 MIME 类型不是 *text*，则所返回列表中的对应项将为 *failobj*。

## **get_content_disposition**()

​	如果信息的 *Content-Disposition* 头字段存在，返回其字段值；否则返回 `None` 。返回的值均为小写，不包含参数。如果信息遵循 [**RFC 2183**](https://datatracker.ietf.org/doc/html/rfc2183.html) 标准，则此方法的返回值只可能在 *inline* 、 *attachment* 和 `None` 之间选择。

*Added in version 3.5.*

## **walk**()

[`walk()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.walk) 方法是一个多功能生成器。它可以被用来以深度优先顺序遍历信息对象树的所有部分和子部分。一般而言， [`walk()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.walk) 会被用作 `for` 循环的迭代器，每一次迭代都返回其下一个子部分。

​	以下例子会打印出一封具有多部分结构之信息的每个部分的 MIME 类型。



```
>>> for part in msg.walk():
...     print(part.get_content_type())
multipart/report
text/plain
message/delivery-status
text/plain
text/plain
message/rfc822
text/plain
```

`walk` 会遍历所有 [`is_multipart()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.is_multipart) 方法返回 `True` 的部分之子部分，哪怕 `msg.get_content_maintype() == 'multipart'` 返回的是 `False` 。使用 `_structure` 除错帮助函数可以帮助我们在下面这个例子当中看清楚这一点：



```
>>> for part in msg.walk():
...     print(part.get_content_maintype() == 'multipart',
...           part.is_multipart())
True True
False False
False True
False False
False False
False True
False False
>>> _structure(msg)
multipart/report
    text/plain
    message/delivery-status
        text/plain
        text/plain
    message/rfc822
        text/plain
```

​	在这里， `message` 的部分并非 `multiparts` ，但是它们真的包含子部分！ `is_multipart()` 返回 `True` ， `walk` 也深入进这些子部分中。

[`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 对象也可以包含两个可选的实例属性，它们可被用于生成纯文本的 MIME 消息。

## **preamble**

​	MIME 文档格式在标头之后的空白行以及第一个多部分的分界字符串之间允许添加一些文本， 通常，此文本在支持 MIME 的邮件阅读器中永远不可见，因为它处在标准 MIME 保护范围之外。 但是，当查看消息的原始文本，或当在不支持 MIME 的阅读器中查看消息时，此文本会变得可见。

*preamble* 属性包含 MIME 文档开头部分的这些处于保护范围之外的文本。 当 [`Parser`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#email.parser.Parser) 在标头之后及第一个分界字符串之前发现一些文本时，它会将这些文本赋值给消息的 *preamble* 属性。 当 [`Generator`](https://docs.python.org/zh-cn/3.13/library/email.generator.html#email.generator.Generator) 写出 MIME 消息的纯文本表示形式时，如果它发现消息具有 *preamble* 属性，它将在标头及第一个分界之间区域写出这些文本。 请参阅 [`email.parser`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#module-email.parser) 和 [`email.generator`](https://docs.python.org/zh-cn/3.13/library/email.generator.html#module-email.generator) 了解更多细节。

​	请注意如果消息对象没有前导文本，则 *preamble* 属性将为 `None`。

## **epilogue**

*epilogue* 属性的作用方式与 *preamble* 属性相同，区别在于它包含出现于最后一个分界与消息结尾之间的文本。

​	你不需要将 epilogue 设为空字符串以便让 [`Generator`](https://docs.python.org/zh-cn/3.13/library/email.generator.html#email.generator.Generator) 在文件末尾打印一个换行符。

## **defects**

*defects* 属性包含在解析消息时发现的所有问题的列表。 请参阅 [`email.errors`](https://docs.python.org/zh-cn/3.13/library/email.errors.html#module-email.errors) 了解可能的解析缺陷的详细描述。