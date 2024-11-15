+++
title = "email --- 电子邮件与 MIME 处理包"
date = 2024-11-15T14:53:40+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/email.html](https://docs.python.org/zh-cn/3.13/library/email.html)
>
> 收录该文档的时间：`2024-11-15T14:53:40+08:00`

# `email` --- 电子邮件与 MIME 处理包

**源代码:** [Lib/email/__init__.py](https://github.com/python/cpython/tree/3.13/Lib/email/__init__.py)

------

[`email`](https://docs.python.org/zh-cn/3.13/library/email.html#module-email) 包是一个用于管理电子邮件消息的库。 它 *并非* 专门被设计用来执行向 SMTP ([**RFC 2821**](https://datatracker.ietf.org/doc/html/rfc2821.html)), NNTP 或其他服务器发送电子邮件消息；这些是 [`smtplib`](https://docs.python.org/zh-cn/3.13/library/smtplib.html#module-smtplib) 等模块的功能。 [`email`](https://docs.python.org/zh-cn/3.13/library/email.html#module-email) 包试图尽可能地遵循 RFC，支持 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.html) 和 [**RFC 6532**](https://datatracker.ietf.org/doc/html/rfc6532.html)，以及与 MIME 相关的各个 RFC 例如 [**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.html), [**RFC 2046**](https://datatracker.ietf.org/doc/html/rfc2046.html), [**RFC 2047**](https://datatracker.ietf.org/doc/html/rfc2047.html), [**RFC 2183**](https://datatracker.ietf.org/doc/html/rfc2183.html) 和 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.html)。

​	email 包的总体结构可以分为三个主要组件，另外还有第四个组件用于控制其他组件的行为。

​	这个包的中心组件是代表电子邮件消息的“对象模型”。 应用程序主要通过在 [`message`](https://docs.python.org/zh-cn/3.13/library/email.message.html#module-email.message) 子模块中定义的对象模型接口与这个包进行交互。 应用程序可以使用此 API 来询问有关现有电子邮件的问题、构造新的电子邮件，或者添加或移除自身也使用相同对象模型接口的电子邮件子组件。 也就是说，遵循电子邮件消息及其 MIME 子组件的性质，电子邮件对象模型是所有提供 [`EmailMessage`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage) API 的对象所构成的树状结构。

​	这个包的另外两个主要组件是 [`parser`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#module-email.parser) 和 [`generator`](https://docs.python.org/zh-cn/3.13/library/email.generator.html#module-email.generator)。 parser 接受电子邮件消息的序列化版本（字节流）并将其转换为 [`EmailMessage`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage) 对象树。 generator 接受 [`EmailMessage`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage) 并将其转回序列化的字节流。 (parser 和 generator 还能处理文本字符流，但不建议这种用法，因为这很容易导致某种形式的无效消息。

​	控制组件是 [`policy`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#module-email.policy) 模块。 每一个 [`EmailMessage`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage)、每一个 [`generator`](https://docs.python.org/zh-cn/3.13/library/email.generator.html#module-email.generator) 和每一个 [`parser`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#module-email.parser) 都有一个相关联的 [`policy`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#module-email.policy) 对象来控制其行为。 通常应用程序只有在 [`EmailMessage`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage) 被创建时才需要指明控制策略，或者通过直接实例代 [`EmailMessage`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage) 来新建电子邮件，或者通过使用 [`parser`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#module-email.parser) 来解析输入流。 但是策略也可以在使用 [`generator`](https://docs.python.org/zh-cn/3.13/library/email.generator.html#module-email.generator) 序列化消息时被更改。 例如，这允许从磁盘解析通用电子邮件消息，而在将消息发送到电子邮件服务器时使用标准 SMTP 设置对其进行序列化。

​	email 包会尽量地对应用程序隐藏各种控制类 RFC 的细节。 从概念上讲应用程序应当能够将电子邮件消息视为 Unicode 文本和二进制附件的结构化树，而不必担心在序列化时要如何表示它们。 但在实际中，经常有必要至少了解一部分控制类 MIME 消息及其结构的规划，特别是 MIME "内容类型" 的名称和性质以及它们是如何标识多部分文档的。 在大多数情况下这些知识应当仅对于更复杂的应用程序来说才是必需的，并且即便在那时它也应当仅是特定的高层级结构，而不是如何表示这些结构的细节信息。 由于 MIME 内容类型被广泛应用于现代因特网软件（而非只是电子邮件），因此这对许多程序员来说将是很熟悉的概念。

​	以下小节描述了 [`email`](https://docs.python.org/zh-cn/3.13/library/email.html#module-email) 包的具体功能。 我们会从 [`message`](https://docs.python.org/zh-cn/3.13/library/email.message.html#module-email.message) 对象模型开始，它是应用程序将要使用的主要接口，之后是 [`parser`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#module-email.parser) 和 [`generator`](https://docs.python.org/zh-cn/3.13/library/email.generator.html#module-email.generator) 组件。 然后我们会介绍 [`policy`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#module-email.policy) 控制组件，它将完成对这个库的主要组件的处理。

​	接下来的三个小节会介绍这个包可能引发的异常以及 [`parser`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#module-email.parser) 可能检测到的缺陷（即与 RFC 不相符）。 然后我们会介绍 [`headerregistry`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#module-email.headerregistry) 和 [`contentmanager`](https://docs.python.org/zh-cn/3.13/library/email.contentmanager.html#module-email.contentmanager) 子组件，它们分别提供了用于更精细地操纵标题和载荷的工具。 这两个组件除了包含使用与生成非简单消息的相关特性，还记录了它们的可扩展性 API，这将是高级应用程序所感兴趣的内容。

​	在此之后是一组使用之前小节所介绍的 API 的基本部分的示例。

​	前面的内容是 email 包的现代（对 Unicode 支持良好）API。 从 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 类开始的其余小节则介绍了旧式 [`compat32`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.compat32) API，它会更直接地处理如何表示电子邮件消息的细节。 [`compat32`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.compat32) API *不会* 向应用程序隐藏 RFC 的相关细节，但对于需要进行此种层级操作的应用程序来说将是很有用的工具。 此文档对于因向下兼容理由而仍然使用 [`compat32`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.compat32) API 的应用程序也是很适合的。

*在 3.6 版本发生变更:* 文档经过重新组织和撰写以鼓励使用新的 [`EmailMessage`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage)/[`EmailPolicy`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.EmailPolicy) API。

[`email`](https://docs.python.org/zh-cn/3.13/library/email.html#module-email) 包文档的内容:

- `email.message`: 表示电子邮件消息
  - `EmailMessage`
    - [`as_string()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.as_string)
    - [`__str__()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.__str__)
    - [`as_bytes()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.as_bytes)
    - [`__bytes__()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.__bytes__)
    - [`is_multipart()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.is_multipart)
    - [`set_unixfrom()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.set_unixfrom)
    - [`get_unixfrom()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.get_unixfrom)
    - [`__len__()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.__len__)
    - [`__contains__()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.__contains__)
    - [`__getitem__()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.__getitem__)
    - [`__setitem__()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.__setitem__)
    - [`__delitem__()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.__delitem__)
    - [`keys()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.keys)
    - [`values()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.values)
    - [`items()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.items)
    - [`get()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.get)
    - [`get_all()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.get_all)
    - [`add_header()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.add_header)
    - [`replace_header()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.replace_header)
    - [`get_content_type()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.get_content_type)
    - [`get_content_maintype()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.get_content_maintype)
    - [`get_content_subtype()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.get_content_subtype)
    - [`get_default_type()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.get_default_type)
    - [`set_default_type()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.set_default_type)
    - [`set_param()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.set_param)
    - [`del_param()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.del_param)
    - [`get_filename()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.get_filename)
    - [`get_boundary()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.get_boundary)
    - [`set_boundary()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.set_boundary)
    - [`get_content_charset()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.get_content_charset)
    - [`get_charsets()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.get_charsets)
    - [`is_attachment()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.is_attachment)
    - [`get_content_disposition()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.get_content_disposition)
    - [`walk()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.walk)
    - [`get_body()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.get_body)
    - [`iter_attachments()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.iter_attachments)
    - [`iter_parts()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.iter_parts)
    - [`get_content()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.get_content)
    - [`set_content()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.set_content)
    - [`make_related()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.make_related)
    - [`make_alternative()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.make_alternative)
    - [`make_mixed()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.make_mixed)
    - [`add_related()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.add_related)
    - [`add_alternative()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.add_alternative)
    - [`add_attachment()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.add_attachment)
    - [`clear()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.clear)
    - [`clear_content()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.clear_content)
    - [`preamble`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.preamble)
    - [`epilogue`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.epilogue)
    - [`defects`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.defects)
  - [`MIMEPart`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.MIMEPart)
- `email.parser`: 解析电子邮件消息
  - FeedParser API
    - `BytesFeedParser`
      - [`feed()`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#email.parser.BytesFeedParser.feed)
      - [`close()`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#email.parser.BytesFeedParser.close)
    - [`FeedParser`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#email.parser.FeedParser)
  - Parser API
    - `BytesParser`
      - [`parse()`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#email.parser.BytesParser.parse)
      - [`parsebytes()`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#email.parser.BytesParser.parsebytes)
    - [`BytesHeaderParser`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#email.parser.BytesHeaderParser)
    - `Parser`
      - [`parse()`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#email.parser.Parser.parse)
      - [`parsestr()`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#email.parser.Parser.parsestr)
    - [`HeaderParser`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#email.parser.HeaderParser)
    - [`message_from_bytes()`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#email.message_from_bytes)
    - [`message_from_binary_file()`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#email.message_from_binary_file)
    - [`message_from_string()`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#email.message_from_string)
    - [`message_from_file()`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#email.message_from_file)
  - [附加说明](https://docs.python.org/zh-cn/3.13/library/email.parser.html#additional-notes)
- `email.generator`: 生成 MIME 文档
  - `BytesGenerator`
    - [`flatten()`](https://docs.python.org/zh-cn/3.13/library/email.generator.html#email.generator.BytesGenerator.flatten)
    - [`clone()`](https://docs.python.org/zh-cn/3.13/library/email.generator.html#email.generator.BytesGenerator.clone)
    - [`write()`](https://docs.python.org/zh-cn/3.13/library/email.generator.html#email.generator.BytesGenerator.write)
  - `Generator`
    - [`flatten()`](https://docs.python.org/zh-cn/3.13/library/email.generator.html#email.generator.Generator.flatten)
    - [`clone()`](https://docs.python.org/zh-cn/3.13/library/email.generator.html#email.generator.Generator.clone)
    - [`write()`](https://docs.python.org/zh-cn/3.13/library/email.generator.html#email.generator.Generator.write)
  - [`DecodedGenerator`](https://docs.python.org/zh-cn/3.13/library/email.generator.html#email.generator.DecodedGenerator)
- `email.policy`: 策略对象
  - `Policy`
    - [`max_line_length`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.max_line_length)
    - [`linesep`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.linesep)
    - [`cte_type`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.cte_type)
    - [`raise_on_defect`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.raise_on_defect)
    - [`mangle_from_`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.mangle_from_)
    - [`message_factory`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.message_factory)
    - [`verify_generated_headers`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.verify_generated_headers)
    - [`clone()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.clone)
    - [`handle_defect()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.handle_defect)
    - [`register_defect()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.register_defect)
    - [`header_max_count()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.header_max_count)
    - [`header_source_parse()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.header_source_parse)
    - [`header_store_parse()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.header_store_parse)
    - [`header_fetch_parse()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.header_fetch_parse)
    - [`fold()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.fold)
    - [`fold_binary()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.fold_binary)
  - `EmailPolicy`
    - [`utf8`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.EmailPolicy.utf8)
    - [`refold_source`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.EmailPolicy.refold_source)
    - [`header_factory`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.EmailPolicy.header_factory)
    - [`content_manager`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.EmailPolicy.content_manager)
    - [`header_max_count()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.EmailPolicy.header_max_count)
    - [`header_source_parse()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.EmailPolicy.header_source_parse)
    - [`header_store_parse()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.EmailPolicy.header_store_parse)
    - [`header_fetch_parse()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.EmailPolicy.header_fetch_parse)
    - [`fold()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.EmailPolicy.fold)
    - [`fold_binary()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.EmailPolicy.fold_binary)
  - [`default`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.default)
  - [`SMTP`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.SMTP)
  - [`SMTPUTF8`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.SMTPUTF8)
  - [`HTTP`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.HTTP)
  - [`strict`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.strict)
  - `Compat32`
    - [`mangle_from_`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Compat32.mangle_from_)
    - [`header_source_parse()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Compat32.header_source_parse)
    - [`header_store_parse()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Compat32.header_store_parse)
    - [`header_fetch_parse()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Compat32.header_fetch_parse)
    - [`fold()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Compat32.fold)
    - [`fold_binary()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Compat32.fold_binary)
  - [`compat32`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.compat32)
- `email.errors`: 异常和缺陷类
  - [`MessageError`](https://docs.python.org/zh-cn/3.13/library/email.errors.html#email.errors.MessageError)
  - [`MessageParseError`](https://docs.python.org/zh-cn/3.13/library/email.errors.html#email.errors.MessageParseError)
  - [`HeaderParseError`](https://docs.python.org/zh-cn/3.13/library/email.errors.html#email.errors.HeaderParseError)
  - [`BoundaryError`](https://docs.python.org/zh-cn/3.13/library/email.errors.html#email.errors.BoundaryError)
  - [`MultipartConversionError`](https://docs.python.org/zh-cn/3.13/library/email.errors.html#email.errors.MultipartConversionError)
  - [`HeaderWriteError`](https://docs.python.org/zh-cn/3.13/library/email.errors.html#email.errors.HeaderWriteError)
  - [`MessageDefect`](https://docs.python.org/zh-cn/3.13/library/email.errors.html#email.errors.MessageDefect)
  - [`HeaderDefect`](https://docs.python.org/zh-cn/3.13/library/email.errors.html#email.errors.HeaderDefect)
- `email.headerregistry`: 自定义标头对象
  - `BaseHeader`
    - [`name`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.BaseHeader.name)
    - [`defects`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.BaseHeader.defects)
    - [`max_count`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.BaseHeader.max_count)
    - [`fold()`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.BaseHeader.fold)
  - [`UnstructuredHeader`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.UnstructuredHeader)
  - `DateHeader`
    - [`datetime`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.DateHeader.datetime)
  - `AddressHeader`
    - [`groups`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.AddressHeader.groups)
    - [`addresses`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.AddressHeader.addresses)
  - `SingleAddressHeader`
    - [`address`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.SingleAddressHeader.address)
  - `MIMEVersionHeader`
    - [`version`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.MIMEVersionHeader.version)
    - [`major`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.MIMEVersionHeader.major)
    - [`minor`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.MIMEVersionHeader.minor)
  - `ParameterizedMIMEHeader`
    - [`params`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.ParameterizedMIMEHeader.params)
  - `ContentTypeHeader`
    - [`content_type`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.ContentTypeHeader.content_type)
    - [`maintype`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.ContentTypeHeader.maintype)
    - [`subtype`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.ContentTypeHeader.subtype)
  - `ContentDispositionHeader`
    - [`content_disposition`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.ContentDispositionHeader.content_disposition)
  - `ContentTransferEncoding`
    - [`cte`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.ContentTransferEncoding.cte)
  - `HeaderRegistry`
    - [`map_to_type()`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.HeaderRegistry.map_to_type)
    - [`__getitem__()`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.HeaderRegistry.__getitem__)
    - [`__call__()`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.HeaderRegistry.__call__)
  - `Address`
    - [`display_name`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.Address.display_name)
    - [`username`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.Address.username)
    - [`domain`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.Address.domain)
    - [`addr_spec`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.Address.addr_spec)
    - [`__str__()`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.Address.__str__)
  - `Group`
    - [`display_name`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.Group.display_name)
    - [`addresses`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.Group.addresses)
    - [`__str__()`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.Group.__str__)
- `email.contentmanager`: 管理 MIME 内容
  - `ContentManager`
    - [`get_content()`](https://docs.python.org/zh-cn/3.13/library/email.contentmanager.html#email.contentmanager.ContentManager.get_content)
    - [`set_content()`](https://docs.python.org/zh-cn/3.13/library/email.contentmanager.html#email.contentmanager.ContentManager.set_content)
    - [`add_get_handler()`](https://docs.python.org/zh-cn/3.13/library/email.contentmanager.html#email.contentmanager.ContentManager.add_get_handler)
    - [`add_set_handler()`](https://docs.python.org/zh-cn/3.13/library/email.contentmanager.html#email.contentmanager.ContentManager.add_set_handler)
  - 内容管理器实例
    - `raw_data_manager`
      - [`get_content()`](https://docs.python.org/zh-cn/3.13/library/email.contentmanager.html#email.contentmanager.get_content)
      - [`set_content()`](https://docs.python.org/zh-cn/3.13/library/email.contentmanager.html#email.contentmanager.set_content)
- [`email`: 示例](https://docs.python.org/zh-cn/3.13/library/email.examples.html)

​	旧式 API:

- `email.message.Message`: 使用 `compat32` API 来表示电子邮件消息
  - `Message`
    - [`as_string()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.as_string)
    - [`__str__()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.__str__)
    - [`as_bytes()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.as_bytes)
    - [`__bytes__()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.__bytes__)
    - [`is_multipart()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.is_multipart)
    - [`set_unixfrom()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.set_unixfrom)
    - [`get_unixfrom()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_unixfrom)
    - [`attach()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.attach)
    - [`get_payload()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_payload)
    - [`set_payload()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.set_payload)
    - [`set_charset()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.set_charset)
    - [`get_charset()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_charset)
    - [`__len__()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.__len__)
    - [`__contains__()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.__contains__)
    - [`__getitem__()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.__getitem__)
    - [`__setitem__()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.__setitem__)
    - [`__delitem__()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.__delitem__)
    - [`keys()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.keys)
    - [`values()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.values)
    - [`items()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.items)
    - [`get()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get)
    - [`get_all()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_all)
    - [`add_header()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.add_header)
    - [`replace_header()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.replace_header)
    - [`get_content_type()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_content_type)
    - [`get_content_maintype()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_content_maintype)
    - [`get_content_subtype()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_content_subtype)
    - [`get_default_type()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_default_type)
    - [`set_default_type()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.set_default_type)
    - [`get_params()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_params)
    - [`get_param()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_param)
    - [`set_param()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.set_param)
    - [`del_param()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.del_param)
    - [`set_type()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.set_type)
    - [`get_filename()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_filename)
    - [`get_boundary()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_boundary)
    - [`set_boundary()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.set_boundary)
    - [`get_content_charset()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_content_charset)
    - [`get_charsets()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_charsets)
    - [`get_content_disposition()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_content_disposition)
    - [`walk()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.walk)
    - [`preamble`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.preamble)
    - [`epilogue`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.epilogue)
    - [`defects`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.defects)
- `email.mime`: 从头创建电子邮件和 MIME 对象
  - [`MIMEBase`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.base.MIMEBase)
  - [`MIMENonMultipart`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.nonmultipart.MIMENonMultipart)
  - [`MIMEMultipart`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.multipart.MIMEMultipart)
  - [`MIMEApplication`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.application.MIMEApplication)
  - [`MIMEAudio`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.audio.MIMEAudio)
  - [`MIMEImage`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.image.MIMEImage)
  - [`MIMEMessage`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.message.MIMEMessage)
  - [`MIMEText`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.text.MIMEText)
- `email.header`: 国际化标头
  - `Header`
    - [`append()`](https://docs.python.org/zh-cn/3.13/library/email.header.html#email.header.Header.append)
    - [`encode()`](https://docs.python.org/zh-cn/3.13/library/email.header.html#email.header.Header.encode)
    - [`__str__()`](https://docs.python.org/zh-cn/3.13/library/email.header.html#email.header.Header.__str__)
    - [`__eq__()`](https://docs.python.org/zh-cn/3.13/library/email.header.html#email.header.Header.__eq__)
    - [`__ne__()`](https://docs.python.org/zh-cn/3.13/library/email.header.html#email.header.Header.__ne__)
  - [`decode_header()`](https://docs.python.org/zh-cn/3.13/library/email.header.html#email.header.decode_header)
  - [`make_header()`](https://docs.python.org/zh-cn/3.13/library/email.header.html#email.header.make_header)
- `email.charset`: 表示字符集
  - `Charset`
    - [`input_charset`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.Charset.input_charset)
    - [`header_encoding`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.Charset.header_encoding)
    - [`body_encoding`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.Charset.body_encoding)
    - [`output_charset`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.Charset.output_charset)
    - [`input_codec`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.Charset.input_codec)
    - [`output_codec`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.Charset.output_codec)
    - [`get_body_encoding()`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.Charset.get_body_encoding)
    - [`get_output_charset()`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.Charset.get_output_charset)
    - [`header_encode()`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.Charset.header_encode)
    - [`header_encode_lines()`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.Charset.header_encode_lines)
    - [`body_encode()`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.Charset.body_encode)
    - [`__str__()`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.Charset.__str__)
    - [`__eq__()`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.Charset.__eq__)
    - [`__ne__()`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.Charset.__ne__)
  - [`add_charset()`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.add_charset)
  - [`add_alias()`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.add_alias)
  - [`add_codec()`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.add_codec)
- `email.encoders`: 编码器
  - [`encode_quopri()`](https://docs.python.org/zh-cn/3.13/library/email.encoders.html#email.encoders.encode_quopri)
  - [`encode_base64()`](https://docs.python.org/zh-cn/3.13/library/email.encoders.html#email.encoders.encode_base64)
  - [`encode_7or8bit()`](https://docs.python.org/zh-cn/3.13/library/email.encoders.html#email.encoders.encode_7or8bit)
  - [`encode_noop()`](https://docs.python.org/zh-cn/3.13/library/email.encoders.html#email.encoders.encode_noop)
- `email.utils`: 杂项工具
  - [`localtime()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.localtime)
  - [`make_msgid()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.make_msgid)
  - [`quote()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.quote)
  - [`unquote()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.unquote)
  - [`parseaddr()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.parseaddr)
  - [`formataddr()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.formataddr)
  - [`getaddresses()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.getaddresses)
  - [`parsedate()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.parsedate)
  - [`parsedate_tz()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.parsedate_tz)
  - [`parsedate_to_datetime()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.parsedate_to_datetime)
  - [`mktime_tz()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.mktime_tz)
  - [`formatdate()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.formatdate)
  - [`format_datetime()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.format_datetime)
  - [`decode_rfc2231()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.decode_rfc2231)
  - [`encode_rfc2231()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.encode_rfc2231)
  - [`collapse_rfc2231_value()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.collapse_rfc2231_value)
  - [`decode_params()`](https://docs.python.org/zh-cn/3.13/library/email.utils.html#email.utils.decode_params)
- `email.iterators`: 迭代器
  - [`body_line_iterator()`](https://docs.python.org/zh-cn/3.13/library/email.iterators.html#email.iterators.body_line_iterator)
  - [`typed_subpart_iterator()`](https://docs.python.org/zh-cn/3.13/library/email.iterators.html#email.iterators.typed_subpart_iterator)
  - [`_structure()`](https://docs.python.org/zh-cn/3.13/library/email.iterators.html#email.iterators._structure)

> 参见
>
> 
>
> - [`smtplib`](https://docs.python.org/zh-cn/3.13/library/smtplib.html#module-smtplib) 模块
>
>   SMTP (简单邮件传输协议) 客户端
>
> - [`poplib`](https://docs.python.org/zh-cn/3.13/library/poplib.html#module-poplib) 模块
>
>   POP (邮局协议) 客户端
>
> - [`imaplib`](https://docs.python.org/zh-cn/3.13/library/imaplib.html#module-imaplib) 模块
>
>   IMAP (互联网消息访问协议) 客户端
>
> - [`mailbox`](https://docs.python.org/zh-cn/3.13/library/mailbox.html#module-mailbox) 模块
>
>   创建、读取和管理使用保存在磁盘中的多种标准格式的消息集的工具。
