+++
title = "`email.parser`: 解析电子邮件消息"
date = 2024-11-15T12:09:25+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/email.parser.html](https://docs.python.org/zh-cn/3.13/library/email.parser.html)
>
> 收录该文档的时间：`2024-11-15T12:09:25+08:00`

# `email.parser`: 解析电子邮件消息

**源代码**: [Lib/email/parser.py](https://github.com/python/cpython/tree/3.13/Lib/email/parser.py)

------

​	使用以下两种方法的其中一种以创建消息对象结构：直接创建一个 [`EmailMessage`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage" >}}) 对象，使用字典接口添加消息头，并且使用 [`set_content()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.set_content" >}}) 和其他相关方法添加消息负载；或者通过解析一个电子邮件消息的序列化表达来创建消息对象结构。

[`email`]({{< ref "/library/netdata/email#module-email" >}}) 包提供了一个可以理解包含 MIME 文档在内的绝大多数电子邮件文档结构的标准语法分析程序。 你可以传递给语法分析程序一个字节串、字符串或者文件对象，语法分析程序会返回给你对应于该对象结构的根 [`EmailMessage`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage" >}}) 实例。 对于简单的、非 MIME 的消息，这个根对象的负载很可能就是一个包含了该消息文字内容的字符串。 对于 MIME 消息，调用根对象的 [`is_multipart()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.is_multipart" >}}) 方法会返回 `True`，其子项可以通过负载操纵方法来进行访问，例如 [`get_body()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.get_body" >}})、[`iter_parts()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.iter_parts" >}}) 还有 [`walk()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.walk" >}})。

​	事实上你可以使用的语法分析程序接口有两种: [`Parser`]({{< ref "/library/netdata/email/email_parser#email.parser.Parser" >}}) API 和增量式的 [`FeedParser`]({{< ref "/library/netdata/email/email_parser#email.parser.FeedParser" >}}) API。当你的全部消息内容都在内存当中，或者整个消息都保存在文件系统内的一个文件当中的时候，[`Parser`]({{< ref "/library/netdata/email/email_parser#email.parser.Parser" >}}) API非常有用。当你从可能会为了等待更多输入而阻塞的数据流当中读取消息（比如从套接字当中读取电子邮件消息）的时候，[`FeedParser`]({{< ref "/library/netdata/email/email_parser#email.parser.FeedParser" >}}) 会更合适。[`FeedParser`]({{< ref "/library/netdata/email/email_parser#email.parser.FeedParser" >}}) 会增量读取并解析消息，并且只有在你关闭语法分析程序的时候才会返回根对象。

​	请注意解析器可以进行有限的扩展，当然你也可以完全从零开始实现你自己的解析器。 将 [`email`]({{< ref "/library/netdata/email#module-email" >}}) 包的内置解析器和 [`EmailMessage`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage" >}}) 类连接起来的所有逻辑都保存在 [`Policy`]({{< ref "/library/netdata/email/email_policy#email.policy.Policy" >}}) 类中。 因此自定义解析器可以根据其需要通过实现合适的 `Policy` 方法的自定义版本以任意方式创建消息对象树。

## FeedParser API

​	从 `email.feedparser` 模块导入的 [`BytesFeedParser`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesFeedParser" >}}) 类提供了一个适合于增量解析电子邮件消息的API，比如说在从一个可能会阻塞（例如套接字）的源当中读取消息文字的场合中它就会变得很有用。当然， [`BytesFeedParser`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesFeedParser" >}}) 也可以用来解析一个已经完整包含在一个 [bytes-like object]({{< ref "/glossary/idx#term-bytes-like-object" >}}) 、字符串或文件内的电子邮件消息，但是在这些场合下使用 [`BytesParser`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesParser" >}}) API可能会更加方便。这两个语法分析程序API的语义和最终结果是一致的。

[`BytesFeedParser`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesFeedParser" >}}) 的API十分简洁易懂：你创建一个语法分析程序的实例，向它不断输入大量的字节直到尽头，然后关闭这个语法分析程序就可以拿到根消息对象了。 在处理符合标准的消息的时候 [`BytesFeedParser`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesFeedParser" >}}) 非常准确；在处理不符合标准的消息的时候它做的也不差，但这视消息损坏的程度而定。它会向消息对象的 [`defects`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.defects" >}}) 属性中写入它从消息中找到的问题列表。关于它能找到的所有问题类型的列表，详见 [`email.errors`]({{< ref "/library/netdata/email/email_errors#module-email.errors" >}}) 模块。

​	这里是 [`BytesFeedParser`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesFeedParser" >}}) 的 API：

## *class* email.parser.**BytesFeedParser**(*_factory=None*, ***, *policy=policy.compat32*)

​	创建一个 [`BytesFeedParser`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesFeedParser" >}}) 实例。可选的 *_factory* 参数是一个不带参数的可调用对象；如果没有被指定，就会使用 *policy* 参数的 [`message_factory`]({{< ref "/library/netdata/email/email_policy#email.policy.Policy.message_factory" >}}) 属性。 每当需要一个新的消息对象的时候，*_factory* 都会被调用。

​	如果指定了 *policy* 参数，它就会使用这个参数所指定的规则来更新消息的表达方式。 如果没有设定 *policy* 参数，它就会使用 [`compat32`]({{< ref "/library/netdata/email/email_policy#email.policy.Compat32" >}}) 策略。 这个策略维持了对 Python 3.2 版本的 email 包的后向兼容性，并且使用 [`Message`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message" >}}) 作为默认的工厂。 其他策略使用 [`EmailMessage`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage" >}}) 作为默认的 *_factory*。 关于 *policy* 还会控制什么，参见 [`policy`]({{< ref "/library/netdata/email/email_policy#module-email.policy" >}}) 的文档。

​	注: **一定要指定policy关键字**。 在未来版本的 Python 当中，它的默认值会变成 [`email.policy.default`]({{< ref "/library/netdata/email/email_policy#email.policy.default" >}})。

> Added in version 3.2.
>

> 在 3.3 版本发生变更: 添加了 *policy* 关键字。

> 在 3.6 版本发生变更: *_factory* 默认为策略 `message_factory`。

## **feed**(*data*)

​	向语法分析程序输入更多数据。*data* 应当是一个包含一行或多行内容的 [bytes-like object]({{< ref "/glossary/idx#term-bytes-like-object" >}})。 行内容可以是不完整的，语法分析程序会妥善的将这些不完整的行缝合在一起。每一行可以使用以下三种常见的终止符号的其中一种：回车符、换行符或回车符加换行符（三者甚至可以混合使用）。

## **close**()

​	完成之前输入的所有数据的解析并返回根消息对象。 如果在这个方法被调用之后仍然调用 [`feed()`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesFeedParser.feed" >}}) 方法，结果是未定义的。

## *class* email.parser.**FeedParser**(*_factory=None*, ***, *policy=policy.compat32*)

​	行为跟 [`BytesFeedParser`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesFeedParser" >}}) 类一致，只不过向 [`feed()`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesFeedParser.feed" >}}) 方法输入的内容必须是字符串。它的实用性有限，因为这种消息只有在其只含有ASCII文字，或者 `utf8` 被设置为 `True` 且没有二进制格式的附件的时候，才会有效。

> 在 3.3 版本发生变更: 添加了 *policy* 关键字。

## Parser API

[`BytesParser`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesParser" >}}) 类从 [`email.parser`]({{< ref "/library/netdata/email/email_parser#module-email.parser" >}}) 模块导入，当消息的完整内容包含在一个 [bytes-like object]({{< ref "/glossary/idx#term-bytes-like-object" >}}) 或文件中时它提供了可用于解析消息的 API。 [`email.parser`]({{< ref "/library/netdata/email/email_parser#module-email.parser" >}}) 模块还提供了 [`Parser`]({{< ref "/library/netdata/email/email_parser#email.parser.Parser" >}}) 用来解析字符串，以及只用来解析消息头的 [`BytesHeaderParser`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesHeaderParser" >}}) 和 [`HeaderParser`]({{< ref "/library/netdata/email/email_parser#email.parser.HeaderParser" >}})，如果你只对消息头感兴趣就可以使用后两者。 在这种场合下 [`BytesHeaderParser`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesHeaderParser" >}}) 和 [`HeaderParser`]({{< ref "/library/netdata/email/email_parser#email.parser.HeaderParser" >}}) 速度非常快，因为它们并不会尝试解析消息体，而是将载荷设为原始数据。

## *class* email.parser.**BytesParser**(*_class=None*, ***, *policy=policy.compat32*)

​	创建一个 [`BytesParser`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesParser" >}}) 实例。 *_class* 和 *policy* 参数在含义和语义上与 [`BytesFeedParser`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesFeedParser" >}}) 的 *_factory* 和 *policy* 参数一致。

​	注: **一定要指定policy关键字**。 在未来版本的 Python 当中，它的默认值会变成 [`email.policy.default`]({{< ref "/library/netdata/email/email_policy#email.policy.default" >}})。

> 在 3.3 版本发生变更: 移除了在2.4版本中被弃用的 *strict* 参数。新增了 *policy* 关键字。

> 在 3.6 版本发生变更: *_class* 默认为策略 `message_factory`。

## **parse**(*fp*, *headersonly=False*)

​	从二进制的类文件对象 *fp* 中读取全部数据、解析其字节内容、并返回消息对象。 *fp* 必须同时支持 [`readline()`]({{< ref "/library/allos/io#io.IOBase.readline" >}}) 方法和 `read()` 方法。

*fp* 内包含的字节内容必须是一块遵循 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.html) （如果 `utf8` 为 `True`，则为 [**RFC 6532**](https://datatracker.ietf.org/doc/html/rfc6532.html) ）格式风格的消息头和消息头延续行，并可能紧跟一个信封头。 头块要么以数据结束，要么以一个空行为终止。 跟着头块的是消息体（消息体内可能包含 MIME 编码的子部分，这也包括 *Content-Transfer-Encoding* 字段为 `8bit` 的子部分）。

​	可选的 *headersonly* 指示了是否应当在读取完消息头后就终止。默认值为 `False` ，意味着它会解析整个文件的全部内容。

## **parsebytes**(*bytes*, *headersonly=False*)

​	与 [`parse()`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesParser.parse" >}}) 方法类似，只不过它要求输入为一个 [bytes-like object]({{< ref "/glossary/idx#term-bytes-like-object" >}}) 而不是类文件对象。于一个 [bytes-like object]({{< ref "/glossary/idx#term-bytes-like-object" >}}) 调用此方法相当于先将这些字节包装于一个 [`BytesIO`]({{< ref "/library/allos/io#io.BytesIO" >}}) 实例中，然后调用 [`parse()`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesParser.parse" >}}) 方法。

​	可选的 *headersonly* 与 [`parse()`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesParser.parse" >}}) 方法中的 *headersonly* 是一致的。

> Added in version 3.2.
>

## *class* email.parser.**BytesHeaderParser**(*_class=None*, ***, *policy=policy.compat32*)

​	除了 *headersonly* 默认为 `True`，其他与 [`BytesParser`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesParser" >}}) 类完全一样。

> Added in version 3.3.
>

## *class* email.parser.**Parser**(*_class=None*, ***, *policy=policy.compat32*)

​	这个类与 [`BytesParser`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesParser" >}}) 一样，但是处理字符串输入。

> 在 3.3 版本发生变更: 移除了 *strict* 参数。添加了 *policy* 关键字。

> 在 3.6 版本发生变更: *_class* 默认为策略 `message_factory`。

## **parse**(*fp*, *headersonly=False*)

​	从文本模式的文件型对象 *fp* 读取所有数据，解析所读取的文本，并返回根消息对象。 *fp* 必须同时支持文件型对象上的 [`readline()`]({{< ref "/library/allos/io#io.TextIOBase.readline" >}}) 和 [`read()`]({{< ref "/library/allos/io#io.TextIOBase.read" >}}) 方法。

​	除了文字模式的要求外，这个方法跟 [`BytesParser.parse()`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesParser.parse" >}}) 的运行方式一致。

## **parsestr**(*text*, *headersonly=False*)

​	与 [`parse()`]({{< ref "/library/netdata/email/email_parser#email.parser.Parser.parse" >}}) 方法类似，只不过它要求输入为一个字符串而不是类文件对象。于一个字符串对象调用此方法相当于先将 *text* 包装于一个 [`StringIO`]({{< ref "/library/allos/io#io.StringIO" >}}) 实例中，然后调用 [`parse()`]({{< ref "/library/netdata/email/email_parser#email.parser.Parser.parse" >}}) 方法。

​	可选的 *headersonly* 与 [`parse()`]({{< ref "/library/netdata/email/email_parser#email.parser.Parser.parse" >}}) 方法中的 *headersonly* 是一致的。

## *class* email.parser.**HeaderParser**(*_class=None*, ***, *policy=policy.compat32*)

​	除了 *headersonly* 默认为 `True` ，其他与 [`Parser`]({{< ref "/library/netdata/email/email_parser#email.parser.Parser" >}}) 类完全一样。

​	考虑到从一个字符串或一个文件对象中创建一个消息对象是非常常见的任务，我们提供了四个方便的函数。它们于顶层 [`email`]({{< ref "/library/netdata/email#module-email" >}}) 包命名空间内可用。

## email.**message_from_bytes**(*s*, *_class=None*, ***, *policy=policy.compat32*)

​	从一个 [bytes-like object]({{< ref "/glossary/idx#term-bytes-like-object" >}}) 中返回消息对象。 这与 `BytesParser().parsebytes(s)` 等价。 可选的 *_class* 和 *policy* 参数与 [`BytesParser`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesParser" >}}) 类的构造函数的参数含义一致。

> Added in version 3.2.
>

> 在 3.3 版本发生变更: 移除了 *strict* 参数。添加了 *policy* 关键字。

## email.**message_from_binary_file**(*fp*, *_class=None*, ***, *policy=policy.compat32*)

​	从打开的二进制 [file object]({{< ref "/glossary/idx#term-file-object" >}}) 中返回消息对象。 这与 `BytesParser().parse(fp)` 等价。 *_class* 和 *policy* 参数与 [`BytesParser`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesParser" >}}) 类的构造函数的参数含义一致。

> Added in version 3.2.
>

> 在 3.3 版本发生变更: 移除了 *strict* 参数。添加了 *policy* 关键字。

## email.**message_from_string**(*s*, *_class=None*, ***, *policy=policy.compat32*)

​	从一个字符串中返回消息对象。 这与 `Parser().parsestr(s)` 等价。 *_class* 和 *policy* 参数与 [`Parser`]({{< ref "/library/netdata/email/email_parser#email.parser.Parser" >}}) 类的构造函数的参数含义一致。

> 在 3.3 版本发生变更: 移除了 *strict* 参数。添加了 *policy* 关键字。

## email.**message_from_file**(*fp*, *_class=None*, ***, *policy=policy.compat32*)

​	从一个打开的 [file object]({{< ref "/glossary/idx#term-file-object" >}}) 中返回消息对象。 这与 `Parser().parse(fp)` 等价。 *_class* 和 *policy* 参数与 [`Parser`]({{< ref "/library/netdata/email/email_parser#email.parser.Parser" >}}) 类的构造函数的参数含义一致。

> 在 3.3 版本发生变更: 移除了 *strict* 参数。添加了 *policy* 关键字。

> 在 3.6 版本发生变更: *_class* 默认为策略 `message_factory`。

​	这里是一个展示了你如何在Python交互式命令行中使用 [`message_from_bytes()`]({{< ref "/library/netdata/email/email_parser#email.message_from_bytes" >}}) 的例子：



``` python
>>> import email
>>> msg = email.message_from_bytes(myBytes)  
```

## 附加说明

​	在解析语义的时候请注意：

- 大多数非 *multipart* 类型的消息都会被解析为一个带有字符串负载的消息对象。这些对象在调用 [`is_multipart()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.is_multipart" >}}) 的时候会返回 `False` ，调用 [`iter_parts()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.iter_parts" >}}) 的时候会产生一个空列表。
- 所有 *multipart* 类型的消息都会被解析成一个容器消息对象。该对象的负载是一个子消息对象列表。外层的容器消息在调用 [`is_multipart()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.is_multipart" >}}) 的时候会返回 `True` ，在调用 [`iter_parts()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.iter_parts" >}}) 的时候会产生一个子部分列表。
- 大多数内容类型为 *message/** （例如 *message/delivery-status* 和 *message/rfc822* ）的消息也会被解析为一个负载是长度为1的列表的容器对象。在它们身上调用 [`is_multipart()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.is_multipart" >}}) 方法会返回 `True` ，调用 [`iter_parts()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.iter_parts" >}}) 所产生的单个元素会是一个子消息对象。
- 一些不遵循标准的消息在其内部关于它是否为 *multipart* 类型前后不一。这些消息可能在消息头的 *Content-Type* 字段中写明为 *multipart* ，但它们的 [`is_multipart()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.is_multipart" >}}) 方法的返回值可能是 `False` 。如果这种消息被 [`FeedParser`]({{< ref "/library/netdata/email/email_parser#email.parser.FeedParser" >}}) 类解析，它们的 *defects* 属性列表当中会有一个 `MultipartInvariantViolationDefect` 类的实例。关于更多信息，详见 [`email.errors`]({{< ref "/library/netdata/email/email_errors#module-email.errors" >}}) 。
