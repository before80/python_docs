+++
title = "`email.mime`: 从头创建电子邮件和 MIME 对象"
date = 2024-11-15T12:09:25+08:00
weight = 90
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/email.mime.html](https://docs.python.org/zh-cn/3.13/library/email.mime.html)
>
> 收录该文档的时间：`2024-11-15T12:09:25+08:00`

# `email.mime`: 从头创建电子邮件和 MIME 对象

**源代码:** [Lib/email/mime/](https://github.com/python/cpython/tree/3.13/Lib/email/mime/)

------

​	此模块是旧版 (`Compat32`) 电子邮件 API 的组成部分。 它的功能在新版 API 中被 [`contentmanager`](https://docs.python.org/zh-cn/3.13/library/email.contentmanager.html#module-email.contentmanager) 部分替代，但在某些应用中这些类仍可能有用，即使是在非旧版代码中。

​	通常，你是通过传递一个文件或一些文本到解析器来获得消息对象结构体的，解析器会解析文本并返回根消息对象。 不过你也可以从头开始构建一个完整的消息结构体，甚至是手动构建单独的 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 对象。 实际上，你也可以接受一个现有的结构体并添加新的 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 对象并移动它们。 这为切片和分割 MIME 消息提供了非常方便的接口。

​	你可以通过创建 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 实例并手动添加附件和所有适当的标头来创建一个新的对象结构体。 不过对于 MIME 消息来说，[`email`](https://docs.python.org/zh-cn/3.13/library/email.html#module-email) 包提供了一些便捷子类来让事情变得更容易。

​	这些类列示如下:

## *class* email.mime.base.**MIMEBase**(*_maintype*, *_subtype*, ***, *policy=compat32*, ***_params*)

​	模块: [`email.mime.base`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#module-email.mime.base)

​	这是 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 的所有 MIME 专属子类。 通常你不会创建专门的 [`MIMEBase`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.base.MIMEBase) 实例，尽管你可以这样做。 [`MIMEBase`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.base.MIMEBase) 主要被提供用来作为更具体的 MIME 感知子类的便捷基类。

*_maintype* 是 *Content-Type* 的主类型 (例如 *text* 或 *image*)，而 *_subtype* 是 *Content-Type* 的次类型 (例如 *plain* 或 *gif*)。 *_params* 是一个形参键/值字典并会被直接传递给 [`Message.add_header`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.add_header)。

​	如果指定了 *policy* (默认为 [`compat32`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Compat32) 策略)，它将被传递给 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message)。

[`MIMEBase`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.base.MIMEBase) 类总是会添加一个 *Content-Type* 标头 (基于 *_maintype*, *_subtype* 和 *_params*)，以及一个 *MIME-Version* 标头 (总是设为 `1.0`)。

*在 3.6 版本发生变更:* 添加了 *policy* 仅限关键字形参。

## *class* email.mime.nonmultipart.**MIMENonMultipart**

​	模块: [`email.mime.nonmultipart`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#module-email.mime.nonmultipart)

[`MIMEBase`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.base.MIMEBase) 的子类，这是用于非 *multipart* MIME 消息的中间基类。 这个类的主要目标是避免使用 [`attach()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.attach) 方法，该方法仅对 *multipart* 消息有意义。 如果 [`attach()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.attach) 被调用，则会引发 [`MultipartConversionError`](https://docs.python.org/zh-cn/3.13/library/email.errors.html#email.errors.MultipartConversionError) 异常。

## *class* email.mime.multipart.**MIMEMultipart**(*_subtype='mixed'*, *boundary=None*, *_subparts=None*, ***, *policy=compat32*, ***_params*)

​	模块: [`email.mime.multipart`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#module-email.mime.multipart)

[`MIMEBase`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.base.MIMEBase) 的子类，这是用于 *multipart* MIME 消息的中间基类。 可选的 *_subtype* 默认为 *mixed*，但可被用来指定消息的子类型。 将会在消息对象中添加一个 *multipart/_subtype* 的 *Content-Type* 标头。 并还将添加一个 *MIME-Version* 标头。

​	可选的 *boundary* 是多部分边界字符串。 当为 `None` (默认值) 时，则会在必要时（例如当消息被序列化时）计算边界。

*_subparts* 是载荷初始子部分的序列。 此序列必须可以被转换为列表。 你总是可以使用 [`Message.attach`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.attach) 方法将新的子部分附加到消息中。

​	可选的 *policy* 参数默认为 [`compat32`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Compat32)。

​	用于 *Content-Type* 标头的附加形参会从关键字参数中获取，或者传入到 *_params* 参数，该参数是一个关键字的字典。

*在 3.6 版本发生变更:* 添加了 *policy* 仅限关键字形参。

## *class* email.mime.application.**MIMEApplication**(*_data*, *_subtype='octet-stream'*, *_encoder=email.encoders.encode_base64*, ***, *policy=compat32*, ***_params*)

​	模块: [`email.mime.application`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#module-email.mime.application)

[`MIMENonMultipart`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.nonmultipart.MIMENonMultipart) 的子类，[`MIMEApplication`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.application.MIMEApplication) 类被用来表示主类型为 *application* 的 MIME 消息。 *_data* 为包含原始应用程序数据的字节串。 可选的 *_subtype* 指定 MIME 子类型并默认为 *octet-stream*。

​	可选的 *_encoder* 是一个可调用对象（即函数），它将执行实际的数据编码以便传输。 这个可调用对象接受一个参数，该参数是 [`MIMEApplication`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.application.MIMEApplication) 的实例。 它应当使用 [`get_payload()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_payload) 和 [`set_payload()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.set_payload) 来将载荷改为已编码形式。 它还应根据需要将任何 *Content-Transfer-Encoding* 或其他标头添加到消息对象中。 默认编码格式为 base64。 请参阅 [`email.encoders`](https://docs.python.org/zh-cn/3.13/library/email.encoders.html#module-email.encoders) 模块来查看内置编码器列表。

​	可选的 *policy* 参数默认为 [`compat32`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Compat32)。

*_params* 会被直接传递给基类的构造器。

*在 3.6 版本发生变更:* 添加了 *policy* 仅限关键字形参。

## *class* email.mime.audio.**MIMEAudio**(*_audiodata*, *_subtype=None*, *_encoder=email.encoders.encode_base64*, ***, *policy=compat32*, ***_params*)

​	模块: [`email.mime.audio`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#module-email.mime.audio)

[`MIMENonMultipart`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.nonmultipart.MIMENonMultipart) 的子类，[`MIMEAudio`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.audio.MIMEAudio) 类被用来创建主类型为 *audio* 的 MIME 消息。 *_audiodata* 是包含原始音频数据的字节串。 如果此数据可作为 au, wav, aiff 或 aifc 来解码，则其子类型将被自动包括在 *Content-Type* 标头中。 在其他情况下你可以通过 *_subtype* 参数显式地指定音频子类型。 如果无法猜测出次类型并且未给出 *_subtype*，则会引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。

​	可选的 *_encoder* 是一个可调用对象（即函数），它将执行实际的音频数据编码以便传输。 这个可调用对象接受一个参数，该参数是 [`MIMEAudio`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.audio.MIMEAudio) 的实例。 它应当使用 [`get_payload()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_payload) 和 [`set_payload()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.set_payload) 来将载荷改为已编码形式。 它还应根据需要将任何 *Content-Transfer-Encoding* 或其他标头添加到消息对象中。 默认编码格式为 base64。 请参阅 [`email.encoders`](https://docs.python.org/zh-cn/3.13/library/email.encoders.html#module-email.encoders) 模块来查看内置编码器列表。

​	可选的 *policy* 参数默认为 [`compat32`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Compat32)。

*_params* 会被直接传递给基类的构造器。

*在 3.6 版本发生变更:* 添加了 *policy* 仅限关键字形参。

## *class* email.mime.image.**MIMEImage**(*_imagedata*, *_subtype=None*, *_encoder=email.encoders.encode_base64*, ***, *policy=compat32*, ***_params*)

​	模块: [`email.mime.image`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#module-email.mime.image)

[`MIMENonMultipart`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.nonmultipart.MIMENonMultipart) 的子类，[`MIMEImage`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.image.MIMEImage) 类被用来创建主类型为 *image* 的 MIME 消息对象。 *_imagedata* 是包含原始图像数据的字节串。 如果此数据类型可以被检测（将尝试 jpeg, png, gif, tiff, rgb, pbm, pgm, ppm, rast, xbm, bmp, webp 和 exr 类型），则其子类型将被自动包括在 *Content-Type* 标头中。 在其他情况下你可以通过 *_subtype* 参数显式地指定图像子类型。 如果无法猜测出次类型并且未给出 *_subtype*，则会引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。

​	可选的 *_encoder* 是一个可调用对象（即函数），它将执行实际的图像数据编码以便传输。 这个可调用对象接受一个参数，该参数是 [`MIMEImage`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.image.MIMEImage) 的实例。 它应当使用 [`get_payload()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.get_payload) 和 [`set_payload()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.set_payload) 来将载荷改为已编码形式。 它还应根据需要将任何 *Content-Transfer-Encoding* 或其他标头添加到消息对象中。 默认编码格式为 base64。 请参阅 [`email.encoders`](https://docs.python.org/zh-cn/3.13/library/email.encoders.html#module-email.encoders) 模块来查看内置编码器列表。

​	可选的 *policy* 参数默认为 [`compat32`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Compat32)。

*_params* 会被直接传递给 [`MIMEBase`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.base.MIMEBase) 构造器。

*在 3.6 版本发生变更:* 添加了 *policy* 仅限关键字形参。

## *class* email.mime.message.**MIMEMessage**(*_msg*, *_subtype='rfc822'*, ***, *policy=compat32*)

​	模块: [`email.mime.message`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#module-email.mime.message)

[`MIMENonMultipart`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.nonmultipart.MIMENonMultipart) 的子类，[`MIMEMessage`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.message.MIMEMessage) 类被用来创建主类型为 *message* 的 MIME 对象。 *_msg* 将被用作载荷，并且必须为 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 类（或其子类）的实例，否则会引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。

​	可选的 *_subtype* 设置消息的子类型；它的默认值为 *rfc822*。

​	可选的 *policy* 参数默认为 [`compat32`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Compat32)。

*在 3.6 版本发生变更:* 添加了 *policy* 仅限关键字形参。

## *class* email.mime.text.**MIMEText**(*_text*, *_subtype='plain'*, *_charset=None*, ***, *policy=compat32*)

​	模块: [`email.mime.text`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#module-email.mime.text)

[`MIMENonMultipart`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.nonmultipart.MIMENonMultipart) 的子类，[`MIMEText`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.text.MIMEText) 类被用来创建主类型为 *text* 的 MIME 对象。 *_text* 是用作载荷的字符串。 *_subtype* 指定子类型并且默认为 *plain*。 *_charset* 是文本的字符集并会作为参数传递给 [`MIMENonMultipart`](https://docs.python.org/zh-cn/3.13/library/email.mime.html#email.mime.nonmultipart.MIMENonMultipart) 构造器；如果该字符串仅包含 `ascii` 码位则其默认值为 `us-ascii`，否则为 `utf-8`。 *_charset* 形参接受一个字符串或是一个 [`Charset`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.Charset) 实例。

​	除非 *_charset* 参数被显式地设为 `None`，否则所创建的 MIMEText 对象将同时具有附带 `charset` 形参的 *Content-Type* 标头，以及 *Content-Transfer-Encoding* 标头。 这意味着后续的 `set_payload` 调用将不再产生已编码的载荷，即使它在 `set_payload` 命令中被传入。 你可以通过删除 `Content-Transfer-Encoding` 标头来“重置”此行为，在此之后的 `set_payload` 调用将自动编码新的载荷（并添加新的 *Content-Transfer-Encoding* 标头）。

​	可选的 *policy* 参数默认为 [`compat32`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Compat32)。

*在 3.5 版本发生变更:* *_charset* 也可接受 [`Charset`](https://docs.python.org/zh-cn/3.13/library/email.charset.html#email.charset.Charset) 实例。

*在 3.6 版本发生变更:* 添加了 *policy* 仅限关键字形参。
