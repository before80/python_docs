+++
title = "email --- 电子邮件与 MIME 处理包"
date = 2024-11-15T14:53:40+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/email.html](https://docs.python.org/zh-cn/3.13/library/email.html)
>
> 收录该文档的时间：`2024-11-15T14:53:40+08:00`

# `email` --- 电子邮件与 MIME 处理包

**源代码:** [Lib/email/__init__.py](https://github.com/python/cpython/tree/3.13/Lib/email/__init__.py)

------

[`email`]({{< ref "/library/netdata/email#module-email" >}}) 包是一个用于管理电子邮件消息的库。 它 *并非* 专门被设计用来执行向 SMTP ([**RFC 2821**](https://datatracker.ietf.org/doc/html/rfc2821.html)), NNTP 或其他服务器发送电子邮件消息；这些是 [`smtplib`]({{< ref "/library/internet/smtplib#module-smtplib" >}}) 等模块的功能。 [`email`]({{< ref "/library/netdata/email#module-email" >}}) 包试图尽可能地遵循 RFC，支持 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.html) 和 [**RFC 6532**](https://datatracker.ietf.org/doc/html/rfc6532.html)，以及与 MIME 相关的各个 RFC 例如 [**RFC 2045**](https://datatracker.ietf.org/doc/html/rfc2045.html), [**RFC 2046**](https://datatracker.ietf.org/doc/html/rfc2046.html), [**RFC 2047**](https://datatracker.ietf.org/doc/html/rfc2047.html), [**RFC 2183**](https://datatracker.ietf.org/doc/html/rfc2183.html) 和 [**RFC 2231**](https://datatracker.ietf.org/doc/html/rfc2231.html)。

​	email 包的总体结构可以分为三个主要组件，另外还有第四个组件用于控制其他组件的行为。

​	这个包的中心组件是代表电子邮件消息的“对象模型”。 应用程序主要通过在 [`message`]({{< ref "/library/netdata/email/email_message#module-email.message" >}}) 子模块中定义的对象模型接口与这个包进行交互。 应用程序可以使用此 API 来询问有关现有电子邮件的问题、构造新的电子邮件，或者添加或移除自身也使用相同对象模型接口的电子邮件子组件。 也就是说，遵循电子邮件消息及其 MIME 子组件的性质，电子邮件对象模型是所有提供 [`EmailMessage`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage" >}}) API 的对象所构成的树状结构。

​	这个包的另外两个主要组件是 [`parser`]({{< ref "/library/netdata/email/email_parser#module-email.parser" >}}) 和 [`generator`]({{< ref "/library/netdata/email/email_generator#module-email.generator" >}})。 parser 接受电子邮件消息的序列化版本（字节流）并将其转换为 [`EmailMessage`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage" >}}) 对象树。 generator 接受 [`EmailMessage`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage" >}}) 并将其转回序列化的字节流。 (parser 和 generator 还能处理文本字符流，但不建议这种用法，因为这很容易导致某种形式的无效消息。

​	控制组件是 [`policy`]({{< ref "/library/netdata/email/email_policy#module-email.policy" >}}) 模块。 每一个 [`EmailMessage`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage" >}})、每一个 [`generator`]({{< ref "/library/netdata/email/email_generator#module-email.generator" >}}) 和每一个 [`parser`]({{< ref "/library/netdata/email/email_parser#module-email.parser" >}}) 都有一个相关联的 [`policy`]({{< ref "/library/netdata/email/email_policy#module-email.policy" >}}) 对象来控制其行为。 通常应用程序只有在 [`EmailMessage`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage" >}}) 被创建时才需要指明控制策略，或者通过直接实例代 [`EmailMessage`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage" >}}) 来新建电子邮件，或者通过使用 [`parser`]({{< ref "/library/netdata/email/email_parser#module-email.parser" >}}) 来解析输入流。 但是策略也可以在使用 [`generator`]({{< ref "/library/netdata/email/email_generator#module-email.generator" >}}) 序列化消息时被更改。 例如，这允许从磁盘解析通用电子邮件消息，而在将消息发送到电子邮件服务器时使用标准 SMTP 设置对其进行序列化。

​	email 包会尽量地对应用程序隐藏各种控制类 RFC 的细节。 从概念上讲应用程序应当能够将电子邮件消息视为 Unicode 文本和二进制附件的结构化树，而不必担心在序列化时要如何表示它们。 但在实际中，经常有必要至少了解一部分控制类 MIME 消息及其结构的规划，特别是 MIME "内容类型" 的名称和性质以及它们是如何标识多部分文档的。 在大多数情况下这些知识应当仅对于更复杂的应用程序来说才是必需的，并且即便在那时它也应当仅是特定的高层级结构，而不是如何表示这些结构的细节信息。 由于 MIME 内容类型被广泛应用于现代因特网软件（而非只是电子邮件），因此这对许多程序员来说将是很熟悉的概念。

​	以下小节描述了 [`email`]({{< ref "/library/netdata/email#module-email" >}}) 包的具体功能。 我们会从 [`message`]({{< ref "/library/netdata/email/email_message#module-email.message" >}}) 对象模型开始，它是应用程序将要使用的主要接口，之后是 [`parser`]({{< ref "/library/netdata/email/email_parser#module-email.parser" >}}) 和 [`generator`]({{< ref "/library/netdata/email/email_generator#module-email.generator" >}}) 组件。 然后我们会介绍 [`policy`]({{< ref "/library/netdata/email/email_policy#module-email.policy" >}}) 控制组件，它将完成对这个库的主要组件的处理。

​	接下来的三个小节会介绍这个包可能引发的异常以及 [`parser`]({{< ref "/library/netdata/email/email_parser#module-email.parser" >}}) 可能检测到的缺陷（即与 RFC 不相符）。 然后我们会介绍 [`headerregistry`]({{< ref "/library/netdata/email/email_headerregistry#module-email.headerregistry" >}}) 和 [`contentmanager`]({{< ref "/library/netdata/email/email_contentmanager#module-email.contentmanager" >}}) 子组件，它们分别提供了用于更精细地操纵标题和载荷的工具。 这两个组件除了包含使用与生成非简单消息的相关特性，还记录了它们的可扩展性 API，这将是高级应用程序所感兴趣的内容。

​	在此之后是一组使用之前小节所介绍的 API 的基本部分的示例。

​	前面的内容是 email 包的现代（对 Unicode 支持良好）API。 从 [`Message`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message" >}}) 类开始的其余小节则介绍了旧式 [`compat32`]({{< ref "/library/netdata/email/email_policy#email.policy.compat32" >}}) API，它会更直接地处理如何表示电子邮件消息的细节。 [`compat32`]({{< ref "/library/netdata/email/email_policy#email.policy.compat32" >}}) API *不会* 向应用程序隐藏 RFC 的相关细节，但对于需要进行此种层级操作的应用程序来说将是很有用的工具。 此文档对于因向下兼容理由而仍然使用 [`compat32`]({{< ref "/library/netdata/email/email_policy#email.policy.compat32" >}}) API 的应用程序也是很适合的。

*在 3.6 版本发生变更:* 文档经过重新组织和撰写以鼓励使用新的 [`EmailMessage`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage" >}})/[`EmailPolicy`]({{< ref "/library/netdata/email/email_policy#email.policy.EmailPolicy" >}}) API。

[`email`]({{< ref "/library/netdata/email#module-email" >}}) 包文档的内容:

- `email.message`: 表示电子邮件消息
  - `EmailMessage`
    - [`as_string()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.as_string" >}})
    - [`__str__()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.__str__" >}})
    - [`as_bytes()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.as_bytes" >}})
    - [`__bytes__()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.__bytes__" >}})
    - [`is_multipart()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.is_multipart" >}})
    - [`set_unixfrom()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.set_unixfrom" >}})
    - [`get_unixfrom()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.get_unixfrom" >}})
    - [`__len__()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.__len__" >}})
    - [`__contains__()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.__contains__" >}})
    - [`__getitem__()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.__getitem__" >}})
    - [`__setitem__()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.__setitem__" >}})
    - [`__delitem__()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.__delitem__" >}})
    - [`keys()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.keys" >}})
    - [`values()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.values" >}})
    - [`items()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.items" >}})
    - [`get()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.get" >}})
    - [`get_all()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.get_all" >}})
    - [`add_header()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.add_header" >}})
    - [`replace_header()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.replace_header" >}})
    - [`get_content_type()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.get_content_type" >}})
    - [`get_content_maintype()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.get_content_maintype" >}})
    - [`get_content_subtype()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.get_content_subtype" >}})
    - [`get_default_type()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.get_default_type" >}})
    - [`set_default_type()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.set_default_type" >}})
    - [`set_param()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.set_param" >}})
    - [`del_param()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.del_param" >}})
    - [`get_filename()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.get_filename" >}})
    - [`get_boundary()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.get_boundary" >}})
    - [`set_boundary()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.set_boundary" >}})
    - [`get_content_charset()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.get_content_charset" >}})
    - [`get_charsets()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.get_charsets" >}})
    - [`is_attachment()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.is_attachment" >}})
    - [`get_content_disposition()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.get_content_disposition" >}})
    - [`walk()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.walk" >}})
    - [`get_body()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.get_body" >}})
    - [`iter_attachments()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.iter_attachments" >}})
    - [`iter_parts()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.iter_parts" >}})
    - [`get_content()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.get_content" >}})
    - [`set_content()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.set_content" >}})
    - [`make_related()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.make_related" >}})
    - [`make_alternative()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.make_alternative" >}})
    - [`make_mixed()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.make_mixed" >}})
    - [`add_related()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.add_related" >}})
    - [`add_alternative()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.add_alternative" >}})
    - [`add_attachment()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.add_attachment" >}})
    - [`clear()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.clear" >}})
    - [`clear_content()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.clear_content" >}})
    - [`preamble`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.preamble" >}})
    - [`epilogue`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.epilogue" >}})
    - [`defects`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.defects" >}})
  - [`MIMEPart`]({{< ref "/library/netdata/email/email_message#email.message.MIMEPart" >}})
- `email.parser`: 解析电子邮件消息
  - FeedParser API
    - `BytesFeedParser`
      - [`feed()`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesFeedParser.feed" >}})
      - [`close()`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesFeedParser.close" >}})
    - [`FeedParser`]({{< ref "/library/netdata/email/email_parser#email.parser.FeedParser" >}})
  - Parser API
    - `BytesParser`
      - [`parse()`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesParser.parse" >}})
      - [`parsebytes()`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesParser.parsebytes" >}})
    - [`BytesHeaderParser`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesHeaderParser" >}})
    - `Parser`
      - [`parse()`]({{< ref "/library/netdata/email/email_parser#email.parser.Parser.parse" >}})
      - [`parsestr()`]({{< ref "/library/netdata/email/email_parser#email.parser.Parser.parsestr" >}})
    - [`HeaderParser`]({{< ref "/library/netdata/email/email_parser#email.parser.HeaderParser" >}})
    - [`message_from_bytes()`]({{< ref "/library/netdata/email/email_parser#email.message_from_bytes" >}})
    - [`message_from_binary_file()`]({{< ref "/library/netdata/email/email_parser#email.message_from_binary_file" >}})
    - [`message_from_string()`]({{< ref "/library/netdata/email/email_parser#email.message_from_string" >}})
    - [`message_from_file()`]({{< ref "/library/netdata/email/email_parser#email.message_from_file" >}})
  - [附加说明]({{< ref "/library/netdata/email/email_parser#additional-notes" >}})
- `email.generator`: 生成 MIME 文档
  - `BytesGenerator`
    - [`flatten()`]({{< ref "/library/netdata/email/email_generator#email.generator.BytesGenerator.flatten" >}})
    - [`clone()`]({{< ref "/library/netdata/email/email_generator#email.generator.BytesGenerator.clone" >}})
    - [`write()`]({{< ref "/library/netdata/email/email_generator#email.generator.BytesGenerator.write" >}})
  - `Generator`
    - [`flatten()`]({{< ref "/library/netdata/email/email_generator#email.generator.Generator.flatten" >}})
    - [`clone()`]({{< ref "/library/netdata/email/email_generator#email.generator.Generator.clone" >}})
    - [`write()`]({{< ref "/library/netdata/email/email_generator#email.generator.Generator.write" >}})
  - [`DecodedGenerator`]({{< ref "/library/netdata/email/email_generator#email.generator.DecodedGenerator" >}})
- `email.policy`: 策略对象
  - `Policy`
    - [`max_line_length`]({{< ref "/library/netdata/email/email_policy#email.policy.Policy.max_line_length" >}})
    - [`linesep`]({{< ref "/library/netdata/email/email_policy#email.policy.Policy.linesep" >}})
    - [`cte_type`]({{< ref "/library/netdata/email/email_policy#email.policy.Policy.cte_type" >}})
    - [`raise_on_defect`]({{< ref "/library/netdata/email/email_policy#email.policy.Policy.raise_on_defect" >}})
    - [`mangle_from_`]({{< ref "/library/netdata/email/email_policy#email.policy.Policy.mangle_from_" >}})
    - [`message_factory`]({{< ref "/library/netdata/email/email_policy#email.policy.Policy.message_factory" >}})
    - [`verify_generated_headers`]({{< ref "/library/netdata/email/email_policy#email.policy.Policy.verify_generated_headers" >}})
    - [`clone()`]({{< ref "/library/netdata/email/email_policy#email.policy.Policy.clone" >}})
    - [`handle_defect()`]({{< ref "/library/netdata/email/email_policy#email.policy.Policy.handle_defect" >}})
    - [`register_defect()`]({{< ref "/library/netdata/email/email_policy#email.policy.Policy.register_defect" >}})
    - [`header_max_count()`]({{< ref "/library/netdata/email/email_policy#email.policy.Policy.header_max_count" >}})
    - [`header_source_parse()`]({{< ref "/library/netdata/email/email_policy#email.policy.Policy.header_source_parse" >}})
    - [`header_store_parse()`]({{< ref "/library/netdata/email/email_policy#email.policy.Policy.header_store_parse" >}})
    - [`header_fetch_parse()`]({{< ref "/library/netdata/email/email_policy#email.policy.Policy.header_fetch_parse" >}})
    - [`fold()`]({{< ref "/library/netdata/email/email_policy#email.policy.Policy.fold" >}})
    - [`fold_binary()`]({{< ref "/library/netdata/email/email_policy#email.policy.Policy.fold_binary" >}})
  - `EmailPolicy`
    - [`utf8`]({{< ref "/library/netdata/email/email_policy#email.policy.EmailPolicy.utf8" >}})
    - [`refold_source`]({{< ref "/library/netdata/email/email_policy#email.policy.EmailPolicy.refold_source" >}})
    - [`header_factory`]({{< ref "/library/netdata/email/email_policy#email.policy.EmailPolicy.header_factory" >}})
    - [`content_manager`]({{< ref "/library/netdata/email/email_policy#email.policy.EmailPolicy.content_manager" >}})
    - [`header_max_count()`]({{< ref "/library/netdata/email/email_policy#email.policy.EmailPolicy.header_max_count" >}})
    - [`header_source_parse()`]({{< ref "/library/netdata/email/email_policy#email.policy.EmailPolicy.header_source_parse" >}})
    - [`header_store_parse()`]({{< ref "/library/netdata/email/email_policy#email.policy.EmailPolicy.header_store_parse" >}})
    - [`header_fetch_parse()`]({{< ref "/library/netdata/email/email_policy#email.policy.EmailPolicy.header_fetch_parse" >}})
    - [`fold()`]({{< ref "/library/netdata/email/email_policy#email.policy.EmailPolicy.fold" >}})
    - [`fold_binary()`]({{< ref "/library/netdata/email/email_policy#email.policy.EmailPolicy.fold_binary" >}})
  - [`default`]({{< ref "/library/netdata/email/email_policy#email.policy.default" >}})
  - [`SMTP`]({{< ref "/library/netdata/email/email_policy#email.policy.SMTP" >}})
  - [`SMTPUTF8`]({{< ref "/library/netdata/email/email_policy#email.policy.SMTPUTF8" >}})
  - [`HTTP`]({{< ref "/library/netdata/email/email_policy#email.policy.HTTP" >}})
  - [`strict`]({{< ref "/library/netdata/email/email_policy#email.policy.strict" >}})
  - `Compat32`
    - [`mangle_from_`]({{< ref "/library/netdata/email/email_policy#email.policy.Compat32.mangle_from_" >}})
    - [`header_source_parse()`]({{< ref "/library/netdata/email/email_policy#email.policy.Compat32.header_source_parse" >}})
    - [`header_store_parse()`]({{< ref "/library/netdata/email/email_policy#email.policy.Compat32.header_store_parse" >}})
    - [`header_fetch_parse()`]({{< ref "/library/netdata/email/email_policy#email.policy.Compat32.header_fetch_parse" >}})
    - [`fold()`]({{< ref "/library/netdata/email/email_policy#email.policy.Compat32.fold" >}})
    - [`fold_binary()`]({{< ref "/library/netdata/email/email_policy#email.policy.Compat32.fold_binary" >}})
  - [`compat32`]({{< ref "/library/netdata/email/email_policy#email.policy.compat32" >}})
- `email.errors`: 异常和缺陷类
  - [`MessageError`]({{< ref "/library/netdata/email/email_errors#email.errors.MessageError" >}})
  - [`MessageParseError`]({{< ref "/library/netdata/email/email_errors#email.errors.MessageParseError" >}})
  - [`HeaderParseError`]({{< ref "/library/netdata/email/email_errors#email.errors.HeaderParseError" >}})
  - [`BoundaryError`]({{< ref "/library/netdata/email/email_errors#email.errors.BoundaryError" >}})
  - [`MultipartConversionError`]({{< ref "/library/netdata/email/email_errors#email.errors.MultipartConversionError" >}})
  - [`HeaderWriteError`]({{< ref "/library/netdata/email/email_errors#email.errors.HeaderWriteError" >}})
  - [`MessageDefect`]({{< ref "/library/netdata/email/email_errors#email.errors.MessageDefect" >}})
  - [`HeaderDefect`]({{< ref "/library/netdata/email/email_errors#email.errors.HeaderDefect" >}})
- `email.headerregistry`: 自定义标头对象
  - `BaseHeader`
    - [`name`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.BaseHeader.name" >}})
    - [`defects`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.BaseHeader.defects" >}})
    - [`max_count`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.BaseHeader.max_count" >}})
    - [`fold()`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.BaseHeader.fold" >}})
  - [`UnstructuredHeader`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.UnstructuredHeader" >}})
  - `DateHeader`
    - [`datetime`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.DateHeader.datetime" >}})
  - `AddressHeader`
    - [`groups`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.AddressHeader.groups" >}})
    - [`addresses`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.AddressHeader.addresses" >}})
  - `SingleAddressHeader`
    - [`address`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.SingleAddressHeader.address" >}})
  - `MIMEVersionHeader`
    - [`version`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.MIMEVersionHeader.version" >}})
    - [`major`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.MIMEVersionHeader.major" >}})
    - [`minor`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.MIMEVersionHeader.minor" >}})
  - `ParameterizedMIMEHeader`
    - [`params`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.ParameterizedMIMEHeader.params" >}})
  - `ContentTypeHeader`
    - [`content_type`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.ContentTypeHeader.content_type" >}})
    - [`maintype`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.ContentTypeHeader.maintype" >}})
    - [`subtype`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.ContentTypeHeader.subtype" >}})
  - `ContentDispositionHeader`
    - [`content_disposition`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.ContentDispositionHeader.content_disposition" >}})
  - `ContentTransferEncoding`
    - [`cte`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.ContentTransferEncoding.cte" >}})
  - `HeaderRegistry`
    - [`map_to_type()`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.HeaderRegistry.map_to_type" >}})
    - [`__getitem__()`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.HeaderRegistry.__getitem__" >}})
    - [`__call__()`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.HeaderRegistry.__call__" >}})
  - `Address`
    - [`display_name`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.Address.display_name" >}})
    - [`username`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.Address.username" >}})
    - [`domain`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.Address.domain" >}})
    - [`addr_spec`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.Address.addr_spec" >}})
    - [`__str__()`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.Address.__str__" >}})
  - `Group`
    - [`display_name`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.Group.display_name" >}})
    - [`addresses`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.Group.addresses" >}})
    - [`__str__()`]({{< ref "/library/netdata/email/email_headerregistry#email.headerregistry.Group.__str__" >}})
- `email.contentmanager`: 管理 MIME 内容
  - `ContentManager`
    - [`get_content()`]({{< ref "/library/netdata/email/email_contentmanager#email.contentmanager.ContentManager.get_content" >}})
    - [`set_content()`]({{< ref "/library/netdata/email/email_contentmanager#email.contentmanager.ContentManager.set_content" >}})
    - [`add_get_handler()`]({{< ref "/library/netdata/email/email_contentmanager#email.contentmanager.ContentManager.add_get_handler" >}})
    - [`add_set_handler()`]({{< ref "/library/netdata/email/email_contentmanager#email.contentmanager.ContentManager.add_set_handler" >}})
  - 内容管理器实例
    - `raw_data_manager`
      - [`get_content()`]({{< ref "/library/netdata/email/email_contentmanager#email.contentmanager.get_content" >}})
      - [`set_content()`]({{< ref "/library/netdata/email/email_contentmanager#email.contentmanager.set_content" >}})
- [`email`: 示例]({{< ref "/library/netdata/email/email_examples" >}})

​	旧式 API:

- `email.message.Message`: 使用 `compat32` API 来表示电子邮件消息
  - `Message`
    - [`as_string()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.as_string" >}})
    - [`__str__()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.__str__" >}})
    - [`as_bytes()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.as_bytes" >}})
    - [`__bytes__()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.__bytes__" >}})
    - [`is_multipart()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.is_multipart" >}})
    - [`set_unixfrom()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.set_unixfrom" >}})
    - [`get_unixfrom()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.get_unixfrom" >}})
    - [`attach()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.attach" >}})
    - [`get_payload()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.get_payload" >}})
    - [`set_payload()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.set_payload" >}})
    - [`set_charset()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.set_charset" >}})
    - [`get_charset()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.get_charset" >}})
    - [`__len__()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.__len__" >}})
    - [`__contains__()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.__contains__" >}})
    - [`__getitem__()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.__getitem__" >}})
    - [`__setitem__()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.__setitem__" >}})
    - [`__delitem__()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.__delitem__" >}})
    - [`keys()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.keys" >}})
    - [`values()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.values" >}})
    - [`items()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.items" >}})
    - [`get()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.get" >}})
    - [`get_all()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.get_all" >}})
    - [`add_header()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.add_header" >}})
    - [`replace_header()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.replace_header" >}})
    - [`get_content_type()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.get_content_type" >}})
    - [`get_content_maintype()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.get_content_maintype" >}})
    - [`get_content_subtype()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.get_content_subtype" >}})
    - [`get_default_type()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.get_default_type" >}})
    - [`set_default_type()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.set_default_type" >}})
    - [`get_params()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.get_params" >}})
    - [`get_param()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.get_param" >}})
    - [`set_param()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.set_param" >}})
    - [`del_param()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.del_param" >}})
    - [`set_type()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.set_type" >}})
    - [`get_filename()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.get_filename" >}})
    - [`get_boundary()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.get_boundary" >}})
    - [`set_boundary()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.set_boundary" >}})
    - [`get_content_charset()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.get_content_charset" >}})
    - [`get_charsets()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.get_charsets" >}})
    - [`get_content_disposition()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.get_content_disposition" >}})
    - [`walk()`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.walk" >}})
    - [`preamble`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.preamble" >}})
    - [`epilogue`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.epilogue" >}})
    - [`defects`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message.defects" >}})
- `email.mime`: 从头创建电子邮件和 MIME 对象
  - [`MIMEBase`]({{< ref "/library/netdata/email/email_mime#email.mime.base.MIMEBase" >}})
  - [`MIMENonMultipart`]({{< ref "/library/netdata/email/email_mime#email.mime.nonmultipart.MIMENonMultipart" >}})
  - [`MIMEMultipart`]({{< ref "/library/netdata/email/email_mime#email.mime.multipart.MIMEMultipart" >}})
  - [`MIMEApplication`]({{< ref "/library/netdata/email/email_mime#email.mime.application.MIMEApplication" >}})
  - [`MIMEAudio`]({{< ref "/library/netdata/email/email_mime#email.mime.audio.MIMEAudio" >}})
  - [`MIMEImage`]({{< ref "/library/netdata/email/email_mime#email.mime.image.MIMEImage" >}})
  - [`MIMEMessage`]({{< ref "/library/netdata/email/email_mime#email.mime.message.MIMEMessage" >}})
  - [`MIMEText`]({{< ref "/library/netdata/email/email_mime#email.mime.text.MIMEText" >}})
- `email.header`: 国际化标头
  - `Header`
    - [`append()`]({{< ref "/library/netdata/email/email_header#email.header.Header.append" >}})
    - [`encode()`]({{< ref "/library/netdata/email/email_header#email.header.Header.encode" >}})
    - [`__str__()`]({{< ref "/library/netdata/email/email_header#email.header.Header.__str__" >}})
    - [`__eq__()`]({{< ref "/library/netdata/email/email_header#email.header.Header.__eq__" >}})
    - [`__ne__()`]({{< ref "/library/netdata/email/email_header#email.header.Header.__ne__" >}})
  - [`decode_header()`]({{< ref "/library/netdata/email/email_header#email.header.decode_header" >}})
  - [`make_header()`]({{< ref "/library/netdata/email/email_header#email.header.make_header" >}})
- `email.charset`: 表示字符集
  - `Charset`
    - [`input_charset`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset.input_charset" >}})
    - [`header_encoding`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset.header_encoding" >}})
    - [`body_encoding`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset.body_encoding" >}})
    - [`output_charset`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset.output_charset" >}})
    - [`input_codec`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset.input_codec" >}})
    - [`output_codec`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset.output_codec" >}})
    - [`get_body_encoding()`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset.get_body_encoding" >}})
    - [`get_output_charset()`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset.get_output_charset" >}})
    - [`header_encode()`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset.header_encode" >}})
    - [`header_encode_lines()`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset.header_encode_lines" >}})
    - [`body_encode()`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset.body_encode" >}})
    - [`__str__()`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset.__str__" >}})
    - [`__eq__()`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset.__eq__" >}})
    - [`__ne__()`]({{< ref "/library/netdata/email/email_charset#email.charset.Charset.__ne__" >}})
  - [`add_charset()`]({{< ref "/library/netdata/email/email_charset#email.charset.add_charset" >}})
  - [`add_alias()`]({{< ref "/library/netdata/email/email_charset#email.charset.add_alias" >}})
  - [`add_codec()`]({{< ref "/library/netdata/email/email_charset#email.charset.add_codec" >}})
- `email.encoders`: 编码器
  - [`encode_quopri()`]({{< ref "/library/netdata/email/email_encoders#email.encoders.encode_quopri" >}})
  - [`encode_base64()`]({{< ref "/library/netdata/email/email_encoders#email.encoders.encode_base64" >}})
  - [`encode_7or8bit()`]({{< ref "/library/netdata/email/email_encoders#email.encoders.encode_7or8bit" >}})
  - [`encode_noop()`]({{< ref "/library/netdata/email/email_encoders#email.encoders.encode_noop" >}})
- `email.utils`: 杂项工具
  - [`localtime()`]({{< ref "/library/netdata/email/email_utils#email.utils.localtime" >}})
  - [`make_msgid()`]({{< ref "/library/netdata/email/email_utils#email.utils.make_msgid" >}})
  - [`quote()`]({{< ref "/library/netdata/email/email_utils#email.utils.quote" >}})
  - [`unquote()`]({{< ref "/library/netdata/email/email_utils#email.utils.unquote" >}})
  - [`parseaddr()`]({{< ref "/library/netdata/email/email_utils#email.utils.parseaddr" >}})
  - [`formataddr()`]({{< ref "/library/netdata/email/email_utils#email.utils.formataddr" >}})
  - [`getaddresses()`]({{< ref "/library/netdata/email/email_utils#email.utils.getaddresses" >}})
  - [`parsedate()`]({{< ref "/library/netdata/email/email_utils#email.utils.parsedate" >}})
  - [`parsedate_tz()`]({{< ref "/library/netdata/email/email_utils#email.utils.parsedate_tz" >}})
  - [`parsedate_to_datetime()`]({{< ref "/library/netdata/email/email_utils#email.utils.parsedate_to_datetime" >}})
  - [`mktime_tz()`]({{< ref "/library/netdata/email/email_utils#email.utils.mktime_tz" >}})
  - [`formatdate()`]({{< ref "/library/netdata/email/email_utils#email.utils.formatdate" >}})
  - [`format_datetime()`]({{< ref "/library/netdata/email/email_utils#email.utils.format_datetime" >}})
  - [`decode_rfc2231()`]({{< ref "/library/netdata/email/email_utils#email.utils.decode_rfc2231" >}})
  - [`encode_rfc2231()`]({{< ref "/library/netdata/email/email_utils#email.utils.encode_rfc2231" >}})
  - [`collapse_rfc2231_value()`]({{< ref "/library/netdata/email/email_utils#email.utils.collapse_rfc2231_value" >}})
  - [`decode_params()`]({{< ref "/library/netdata/email/email_utils#email.utils.decode_params" >}})
- `email.iterators`: 迭代器
  - [`body_line_iterator()`]({{< ref "/library/netdata/email/email_iterators#email.iterators.body_line_iterator" >}})
  - [`typed_subpart_iterator()`]({{< ref "/library/netdata/email/email_iterators#email.iterators.typed_subpart_iterator" >}})
  - [`_structure()`]({{< ref "/library/netdata/email/email_iterators#email.iterators._structure" >}})

> 参见
>
> 
>
> - [`smtplib`]({{< ref "/library/internet/smtplib#module-smtplib" >}}) 模块
>
>   SMTP (简单邮件传输协议) 客户端
>
> - [`poplib`]({{< ref "/library/internet/poplib#module-poplib" >}}) 模块
>
>   POP (邮局协议) 客户端
>
> - [`imaplib`]({{< ref "/library/internet/imaplib#module-imaplib" >}}) 模块
>
>   IMAP (互联网消息访问协议) 客户端
>
> - [`mailbox`]({{< ref "/library/netdata/mailbox#module-mailbox" >}}) 模块
>
>   创建、读取和管理使用保存在磁盘中的多种标准格式的消息集的工具。
