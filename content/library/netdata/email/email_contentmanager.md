+++
title = "`email.contentmanager`: 管理 MIME 内容"
date = 2024-11-15T12:09:25+08:00
weight = 60
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/email.contentmanager.html](https://docs.python.org/zh-cn/3.13/library/email.contentmanager.html)
>
> 收录该文档的时间：`2024-11-15T12:09:25+08:00`

# `email.contentmanager`: 管理 MIME 内容

**源代码:** [Lib/email/contentmanager.py](https://github.com/python/cpython/tree/3.13/Lib/email/contentmanager.py)

------

> Added in version 3.6:
> [[1\]]({{< ref "/library/netdata/email/email_contentmanager#id2" >}})

## *class* email.contentmanager.**ContentManager**

​	内容管理器的基类。 提供注册 MIME 内容和其他表示形式间转换器的标准注册机制，以及 `get_content` 和 `set_content` 发送方法。

## **get_content**(*msg*, **args*, ***kw*)

​	基于 *msg* 的 `mimetype` 查找处理函数（参见下一段），调用该函数，传递所有参数，并返回调用的结果。 预期的效果是处理程序将从 *msg* 中提取有效载荷并返回编码了有关被提取数据信息的对象。

​	要找到处理程序，将在注册表中查找以下键，找到第一个键即停止:

- 表示完整 MIME 类型的字符串 (`maintype/subtype`)
- 表示 `maintype` 的字符串
- 空字符串

​	如果这些键都没有产生处理程序，则为完整 MIME 类型引发一个 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}})。

## **set_content**(*msg*, *obj*, **args*, ***kw*)

​	如果 `maintype` 为 `multipart`，则引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})；否则基于 *obj* 的类型（参见下一段）查找处理函数，在 *msg* 上调用 [`clear_content()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.clear_content" >}})，并调用处理函数，传递所有参数。 预期的效果是处理程序将转换 *obj* 并存入 *msg*，并可能对 *msg* 进行其他更改，例如添加各种 MIME 标头来编码需要用来解释所存储数据的信息。

​	要找到处理程序，将获取 *obj* 的类型 (`typ = type(obj)`)，并在注册表中查找以下键，找到第一个键即停止:

- 类型本身 (`typ`)
- 类型的完整限定名称 (`typ.__module__ + '.' + typ.__qualname__`)。
- 类型的 [`qualname`]({{< ref "/reference/datamodel#type.__qualname__" >}}) (`typ.__qualname__`)
- 类型的 [`name`]({{< ref "/reference/datamodel#type.__name__" >}}) (`typ.__name__`)。

​	如果未匹配到上述任何一项，则为 [MRO]({{< ref "/glossary/idx#term-MRO" >}}) ([`typ.__mro__`]({{< ref "/reference/datamodel#type.__mro__" >}})) 中的每个类型重复上述所有检测。 最后，如果没有其他键产生处理器，则为 `None` 键检测处理器。 如果没有 `None` 的处理器，则为该类型的完整限定名称引发 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}})。

​	并会添加一个 *MIME-Version* 标头，如果没有的话 (另请参见 [`MIMEPart`]({{< ref "/library/netdata/email/email_message#email.message.MIMEPart" >}}))。

## **add_get_handler**(*key*, *handler*)

​	将 *handler* 函数记录为 *key* 的处理程序。 对于可能的 *key* 键，请参阅 [`get_content()`]({{< ref "/library/netdata/email/email_contentmanager#email.contentmanager.get_content" >}})。

## **add_set_handler**(*typekey*, *handler*)

​	将 *handler* 记录为当一个匹配 *typekey* 的类型对象被传递给 [`set_content()`]({{< ref "/library/netdata/email/email_contentmanager#email.contentmanager.set_content" >}}) 时所要调用的函数。 对于可能的 *typekey* 值，请参阅 [`set_content()`]({{< ref "/library/netdata/email/email_contentmanager#email.contentmanager.set_content" >}})。

## 内容管理器实例

​	目前 email 包只提供了一个实体内容管理器 [`raw_data_manager`]({{< ref "/library/netdata/email/email_contentmanager#email.contentmanager.raw_data_manager" >}})，不过在未来可能会添加更多。 [`raw_data_manager`]({{< ref "/library/netdata/email/email_contentmanager#email.contentmanager.raw_data_manager" >}}) 是由 [`EmailPolicy`]({{< ref "/library/netdata/email/email_policy#email.policy.EmailPolicy" >}}) 及其衍生工具所提供的 [`content_manager`]({{< ref "/library/netdata/email/email_policy#email.policy.EmailPolicy.content_manager" >}})。

## email.contentmanager.**raw_data_manager**

​	这个内容管理器仅提供了超出 [`Message`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message" >}}) 本身提供内容的最小接口：它只处理文本、原始字节串和 [`Message`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message" >}}) 对象。 不过相比基础 API，它具有显著的优势：在文本部分上执行 `get_content` 将返回一个 unicode 字符串而无需由应用程序来手动解码，`set_content` 为控制添加到一个部分的标头和控制内容传输编码格式提供了丰富的选项集合，并且它还启用了多种 `add_` 方法，从而简化了多部分消息的创建过程。

## email.contentmanager.**get_content**(*msg*, *errors='replace'*)

​	将指定部分的有效载荷作为字符串（对于 `text` 部分）, [`EmailMessage`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage" >}}) 对象（对于 `message/rfc822` 部分）或 `bytes` 对象（对于所有其他非多部分类型）返回。 如果是在 `multipart` 上调用则会引发 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}})。 如果指定部分是一个 `text` 部分并且指明了 *errors*，则会在将载荷解码为 unicode 时将其用作错误处理程序。 默认的错误处理程序是 `replace`。

## email.contentmanager.**set_content**(*msg*, *<'str'>*, *subtype="plain"*, *charset='utf-8'*, *cte=None*, *disposition=None*, *filename=None*, *cid=None*, *params=None*, *headers=None*)

## email.contentmanager.**set_content**(*msg*, *<'bytes'>*, *maintype*, *subtype*, *cte="base64"*, *disposition=None*, *filename=None*, *cid=None*, *params=None*, *headers=None*)

## email.contentmanager.**set_content**(*msg*, *<'EmailMessage'>*, *cte=None*, *disposition=None*, *filename=None*, *cid=None*, *params=None*, *headers=None*)

​	向 *msg* 添加标头和有效载荷:

​	添加一个带有 `maintype/subtype` 值的 *Content-Type* 标头。

- 对于 `str`，将 MIME `maintype` 设为 `text`，如果指定了子类型 *subtype* 则设为指定值，否则设为 `plain`。
- 对于 `bytes`，将使用指定的 *maintype* 和 *subtype*，如果未指定则会引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。
- 对于 [`EmailMessage`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage" >}}) 对象，将 maintype 设为 `message`，并将指定的 subtype 设为 *subtype*，如果未指定则设为 `rfc822`。 如果 *subtype* 为 `partial`，则引发一个错误（必须使用 `bytes` 对象来构造 `message/partial` 部分）。

​	如果提供了 *charset* (这只对 `str` 适用)，则使用指定的字符集将字符串编码为字节串。 默认值为 `utf-8`。 如果指定的 *charset* 是某个标准 MIME 字符集名称的已知别名，则会改用该标准字符集。

​	如果设置了 *cte*，则使用指定的内容传输编码格式对有效载荷进行编码，并将 *Content-Transfer-Encoding* 标头设为该值。 可能的 *cte* 值有 `quoted-printable`, `base64`, `7bit`, `8bit` 和 `binary`。 如果输入无法以指定的编码格式被编码 (例如，对于包含非 ASCII 值的输入指定 *cte* 值为 `7bit`)，则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

- 对于 `str` 对象，如果 *cte* 未设置则会使用启发方式来确定最紧凑的编码格式。
- 对于 [`EmailMessage`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage" >}})，按照 [**RFC 2046**](https://datatracker.ietf.org/doc/html/rfc2046.html)，如果为 *subtype* `rfc822` 请求的 *cte* 为 `quoted-printable` 或 `base64` ，而为 `7bit` 以外的任何 *cte* 为 *subtype* `external-body` 则会引发一个错误。 对于 `message/rfc822`，如果 *cte* 未指定则会使用 `8bit`。 对于所有其他 *subtype* 值，都会使用 `7bit`。

​备注
 

*cte* 值为 `binary` 实际上还不能正确工作。 由 `set_content` 所修改的 `EmailMessage` 对象是正确的，但 [`BytesGenerator`]({{< ref "/library/netdata/email/email_generator#email.generator.BytesGenerator" >}}) 不会正确地将其序列化。

​	如果设置了 *disposition*，它会被用作 *Content-Disposition* 标头的值。 如果未设置，并且指定了 *filename*，则添加值为 `attachment` 的标头。 如果未设置 *disposition* 并且也未指定 *filename*，则不添加标头。 *disposition* 的有效值仅有 `attachment` 和 `inline`。

​	如果设置了 *filename*，则将其用作 *Content-Disposition* 标头的 `filename` 参数的值。

​	如果设置了 *cid*，则添加一个 *Content-ID* 标头并将其值设为 *cid*。

​	如果设置了 *params*，则迭代其 `items` 方法并使用输出的 `(key, value)` 结果对在 *Content-Type* 标头上设置附加参数。

​	如果设置了 *headers* 并且为 `headername: headervalue` 形式的字符串的列表或为 `header` 对象的列表（通过一个 `name` 属性与字符串相区分），则将标头添加到 *msg*。

​备注
[[1]({{< ref "/library/netdata/email/email_contentmanager#id1" >}})]

​	最初在 3.4 中作为 [暂定模块]({{< ref "/glossary/idx#term-provisional-package" >}}) 添加
