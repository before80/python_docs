+++
title = "`email.generator`: 生成 MIME 文档"
date = 2024-11-15T12:09:25+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/email.generator.html](https://docs.python.org/zh-cn/3.13/library/email.generator.html)
>
> 收录该文档的时间：`2024-11-15T12:09:25+08:00`

# `email.generator`: 生成 MIME 文档

**源代码:** [Lib/email/generator.py](https://github.com/python/cpython/tree/3.13/Lib/email/generator.py)

------

​	最常见的任务之一是生成由消息对象结构体表示的电子消息消息的展平（序列化）版本。 如果你想通过 [`smtplib.SMTP.sendmail()`]({{< ref "/library/internet/smtplib#smtplib.SMTP.sendmail" >}}) 来发送你的消息或是将消息打印到控制台就会需要这样做。 接受一个消息对象结构体并生成其序列化表示就是这些生成器类的工作。

​	与 [`email.parser`]({{< ref "/library/netdata/email/email_parser#module-email.parser" >}}) 模块一样，你并不会受限于已捆绑生成器的功能；你可以自己从头写一个。 不过，已捆绑生成器知道如何以符合标准的方式来生成大多数电子邮件，应该能够很好地处理 MIME 和非 MIME 电子邮件消息，并且被设计为面向字节的解析和生成操作是互逆的，它假定两者都使用同样的非转换型 [`policy`]({{< ref "/library/netdata/email/email_policy#module-email.policy" >}})。 也就是说，通过 [`BytesParser`]({{< ref "/library/netdata/email/email_parser#email.parser.BytesParser" >}}) 类来解析序列化字节流然后再使用 [`BytesGenerator`]({{< ref "/library/netdata/email/email_generator#email.generator.BytesGenerator" >}}) 来重新生成序列化字节流应当得到与输入相同的结果 [[1\]]({{< ref "/library/netdata/email/email_generator#id3" >}})。 （而另一方面，在由程序所构造的 [`EmailMessage`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage" >}}) 上使用生成器可能导致对默认填入的 [`EmailMessage`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage" >}}) 对象的改变。。）

​	可以使用 [`Generator`]({{< ref "/library/netdata/email/email_generator#email.generator.Generator" >}}) 类将消息扁平化为文本（而非二进制数据）的序列化表示形式，但是由于 Unicode 无法直接表示二进制数据，因此消息有必要被转换为仅包含 ASCII 字符的数据，这将使用标准电子邮件 RFC 内容传输编码格式技术来编码电子邮件消息以便通过非 “8 比特位兼容”的信道来传输。

​	为了适应 SMIME 签名消息的可重现处理过程，[`Generator`]({{< ref "/library/netdata/email/email_generator#email.generator.Generator" >}}) 禁用了针对 `multipart/signed` 类型的消息部分及所有子部分的标头折叠。

## *class* email.generator.**BytesGenerator**(*outfp*, *mangle_from_=None*, *maxheaderlen=None*, ***, *policy=None*)

​	返回一个 [`BytesGenerator`]({{< ref "/library/netdata/email/email_generator#email.generator.BytesGenerator" >}}) 对象，该对象将把提供给 [`flatten()`]({{< ref "/library/netdata/email/email_generator#email.generator.BytesGenerator.flatten" >}}) 方法的任何消息或者提供给 [`write()`]({{< ref "/library/netdata/email/email_generator#email.generator.BytesGenerator.write" >}}) 方法的任何经过代理转义编码的文本写入到 [file-like object]({{< ref "/glossary/idx#term-file-like-object" >}}) *outfp*。 *outfp* 必须支持接受二进制数据的 `write` 方法。

​	如果可选的 *mangle_from_* 为 `True`，则会将一个 `>` 字符放到消息体中恰好以字符串 `"From "` 打头，即开头文本为 `From` 加一个空格的任何行的前面。 *mangle_from_* 默认为 *policy* 的 [`mangle_from_`]({{< ref "/library/netdata/email/email_policy#email.policy.Policy.mangle_from_" >}}) 设置值 (对于 [`compat32`]({{< ref "/library/netdata/email/email_policy#email.policy.compat32" >}}) 策略为 `True` 而对于所有其他策略则为 `False`)。 *mangle_from_* 被设计为在当消息以 Unix mbox 格式存储时使用 (参见 [`mailbox`]({{< ref "/library/netdata/mailbox#module-mailbox" >}}) 和 [WHY THE CONTENT-LENGTH FORMAT IS BAD](https://www.jwz.org/doc/content-length.html))。

​	如果 *maxheaderlen* 不为 `None`，则重新折叠任何长于 *maxheaderlen* 的标头行，或者如果为 `0`，则不重新包装任何标头。 如果 *manheaderlen* 为 `None` (默认值)，则根据 *policy* 设置包装标头和其他消息行。

​	如果指定了 *policy*，则使用该策略来控制消息的生成。 如果 *policy* 为 `None` (默认值)，则使用与传递给 `flatten` 的 [`Message`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message" >}}) 或 [`EmailMessage`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage" >}}) 对象相关联的策略来控制消息的生成。 请参阅 [`email.policy`]({{< ref "/library/netdata/email/email_policy#module-email.policy" >}}) 了解有关 *policy* 所控制内容的详情。

> Added in version 3.2.
>

> 在 3.3 版本发生变更: 添加了 *policy* 关键字。

> 在 3.6 版本发生变更: *mangle_from_* 和 *maxheaderlen* 形参的默认行为是遵循策略。

## **flatten**(*msg*, *unixfrom=False*, *linesep=None*)

​	将将以 *msg* 为根的消息对象结构体的文本表示形式打印到创建 [`BytesGenerator`]({{< ref "/library/netdata/email/email_generator#email.generator.BytesGenerator" >}}) 实例时指定的输出文件。

​	如果 [`policy`]({{< ref "/library/netdata/email/email_policy#module-email.policy" >}}) 选项 [`cte_type`]({{< ref "/library/netdata/email/email_policy#email.policy.Policy.cte_type" >}}) 为 `8bit` (默认值)，则会将未被修改的原始已解析消息中的任何标头拷贝到输出，其中会重新生成与原始数据相同的高比特位组字节数据，并保留具有它们的任何消息体部分的非 ASCII *Content-Transfer-Encoding*。 如果 `cte_type` 为 `7bit`，则会根据需要使用兼容 ASCII 的 *Content-Transfer-Encoding* 来转换高比特位组字节数据。 也就是说，将具有非 ASCII *Content-Transfer-Encoding* (*Content-Transfer-Encoding: 8bit*) 的部分转换为兼容 ASCII 的 *Content-Transfer-Encoding*，并使用 MIME `unknown-8bit` 字符集来编码标头中不符合 RFC 的非 ASCII 字节数据，以使其符合 RFC。

​	如果 *unixfrom* 为 `True`，则会在根消息对象的第一个 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.html) 标头之前打印 Unix mailbox 格式 (参见 [`mailbox`]({{< ref "/library/netdata/mailbox#module-mailbox" >}})) 所使用的封包标头分隔符。 如果根对象没有封包标头，则会创建一个标准标头。 默认值为 `False`。 请注意对于子部分来说，不会打印任何封包标头。

​	如果 *linesep* 不为 `None`，则会将其用作扁平化消息的所有行之间的分隔符。 如果 *linesep* 为 `None` (默认值)，则使用在 *policy* 中指定的值。

## **clone**(*fp*)

​	返回此 [`BytesGenerator`]({{< ref "/library/netdata/email/email_generator#email.generator.BytesGenerator" >}}) 实例的独立克隆，具有完全相同的选项设置，而 *fp* 为新的 *outfp*。

## **write**(*s*)

​	使用 `ASCII` 编解码器和 `surrogateescape` 错误处理程序编码 *s*，并将其传递给传入到 [`BytesGenerator`]({{< ref "/library/netdata/email/email_generator#email.generator.BytesGenerator" >}}) 的构造器的 *outfp* 的 *write* 方法 。

​	作为一个便捷工具，[`EmailMessage`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage" >}}) 提供了 [`as_bytes()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.as_bytes" >}}) 和 `bytes(aMessage)` (即 [`__bytes__()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.__bytes__" >}})) 等方法，它们简单地生成一个消息对象的序列化二进制表示形式。 更多细节请参阅 [`email.message`]({{< ref "/library/netdata/email/email_message#module-email.message" >}})。

​	因为字符串无法表示二进制数据，[`Generator`]({{< ref "/library/netdata/email/email_generator#email.generator.Generator" >}}) 类必须将任何消息中扁平化的任何二进制数据转换为兼容 ASCII 的格式，具体将其转换为兼容 ASCII 的 *Content-Transfer_Encoding*。 使用电子邮件 RFC 的术语，你可以将其视作 [`Generator`]({{< ref "/library/netdata/email/email_generator#email.generator.Generator" >}}) 序列化为不 "支持 8 比特" 的 I/O 流。 换句话说，大部分应用程序将需要使用 [`BytesGenerator`]({{< ref "/library/netdata/email/email_generator#email.generator.BytesGenerator" >}})，而非 [`Generator`]({{< ref "/library/netdata/email/email_generator#email.generator.Generator" >}})。

## *class* email.generator.**Generator**(*outfp*, *mangle_from_=None*, *maxheaderlen=None*, ***, *policy=None*)

​	返回一个 [`Generator`]({{< ref "/library/netdata/email/email_generator#email.generator.Generator" >}})，它将把提供给 [`flatten()`]({{< ref "/library/netdata/email/email_generator#email.generator.Generator.flatten" >}}) 方法的任何消息，或者提供给 [`write()`]({{< ref "/library/netdata/email/email_generator#email.generator.Generator.write" >}}) 方法的任何文本写入到 [file-like object]({{< ref "/glossary/idx#term-file-like-object" >}}) *outfp*。 *outfp* 必须支持接受字符串数据的 `write` 方法。

​	如果可选的 *mangle_from_* 为 `True`，则会将一个 `>` 字符放到消息体中恰好以字符串 `"From "` 打头，即开头文本为 `From` 加一个空格的任何行的前面。 *mangle_from_* 默认为 *policy* 的 [`mangle_from_`]({{< ref "/library/netdata/email/email_policy#email.policy.Policy.mangle_from_" >}}) 设置值 (对于 [`compat32`]({{< ref "/library/netdata/email/email_policy#email.policy.compat32" >}}) 策略为 `True` 而对于所有其他策略则为 `False`)。 *mangle_from_* 被设计为在当消息以 Unix mbox 格式存储时使用 (参见 [`mailbox`]({{< ref "/library/netdata/mailbox#module-mailbox" >}}) 和 [WHY THE CONTENT-LENGTH FORMAT IS BAD](https://www.jwz.org/doc/content-length.html))。

​	如果 *maxheaderlen* 不为 `None`，则重新折叠任何长于 *maxheaderlen* 的标头行，或者如果为 `0`，则不重新包装任何标头。 如果 *manheaderlen* 为 `None` (默认值)，则根据 *policy* 设置包装标头和其他消息行。

​	如果指定了 *policy*，则使用该策略来控制消息的生成。 如果 *policy* 为 `None` (默认值)，则使用与传递给 `flatten` 的 [`Message`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message" >}}) 或 [`EmailMessage`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage" >}}) 对象相关联的策略来控制消息的生成。 请参阅 [`email.policy`]({{< ref "/library/netdata/email/email_policy#module-email.policy" >}}) 了解有关 *policy* 所控制内容的详情。

> 在 3.3 版本发生变更: 添加了 *policy* 关键字。

> 在 3.6 版本发生变更: *mangle_from_* 和 *maxheaderlen* 形参的默认行为是遵循策略。

## **flatten**(*msg*, *unixfrom=False*, *linesep=None*)

​	将以 *msg* 为根的消息对象结构体的文本表示形式打印到当 [`Generator`]({{< ref "/library/netdata/email/email_generator#email.generator.Generator" >}}) 实例被创建时所指定的输出文件。

​	如果 [`policy`]({{< ref "/library/netdata/email/email_policy#module-email.policy" >}}) 选项 [`cte_type`]({{< ref "/library/netdata/email/email_policy#email.policy.Policy.cte_type" >}}) 为 `8bit`，则视同选项被设为 `7bit` 来生成消息。 （这是必需的，因为字符串无法表示非 ASCII 字节数据。） 将使用兼容 ASCII 的 *Content-Transfer-Encoding* 按需转换任何具有高比特位组的字节数据。 也就是说，将具有非 ASCII *Content-Transfer-Encoding* (*Content-Transfer-Encoding: 8bit*) 的部分转换为兼容 ASCII 的 *Content-Transfer-Encoding*，并使用 MIME `unknown-8bit` 字符集来编码标头中不符合 RFC 的非 ASCII 字节数据，以使其符合 RFC。

​	如果 *unixfrom* 为 `True`，则会在根消息对象的第一个 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.html) 标头之前打印 Unix mailbox 格式 (参见 [`mailbox`]({{< ref "/library/netdata/mailbox#module-mailbox" >}})) 所使用的封包标头分隔符。 如果根对象没有封包标头，则会创建一个标准标头。 默认值为 `False`。 请注意对于子部分来说，不会打印任何封包标头。

​	如果 *linesep* 不为 `None`，则会将其用作扁平化消息的所有行之间的分隔符。 如果 *linesep* 为 `None` (默认值)，则使用在 *policy* 中指定的值。

> 在 3.2 版本发生变更: 添加了对重编码 `8bit` 消息体的支持，以及 *linesep* 参数。

## **clone**(*fp*)

​	返回此 [`Generator`]({{< ref "/library/netdata/email/email_generator#email.generator.Generator" >}}) 实例的独立克隆，具有完全相同的选项设置，而 *fp* 为新的 *outfp*。

## **write**(*s*)

​	将 *s* 写入到传给 [`Generator`]({{< ref "/library/netdata/email/email_generator#email.generator.Generator" >}}) 的构造器的 *outfp* 的 *write* 方法。 这足够为 [`Generator`]({{< ref "/library/netdata/email/email_generator#email.generator.Generator" >}}) 实际提供可用于 [`print()`]({{< ref "/library/functions#print" >}}) 函数的文件类 API。

​	作为一个便捷工具，[`EmailMessage`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage" >}}) 提供了 [`as_string()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.as_string" >}}) 和 `str(aMessage)` (即 [`__str__()`]({{< ref "/library/netdata/email/email_message#email.message.EmailMessage.__str__" >}})) 等方法，它们简单地生成一个消息对象的已格式化字符串表示形式。 更多细节请参阅 [`email.message`]({{< ref "/library/netdata/email/email_message#module-email.message" >}})。

[`email.generator`]({{< ref "/library/netdata/email/email_generator#module-email.generator" >}}) 模块还提供了一个派生类 [`DecodedGenerator`]({{< ref "/library/netdata/email/email_generator#email.generator.DecodedGenerator" >}})，它类似于 [`Generator`]({{< ref "/library/netdata/email/email_generator#email.generator.Generator" >}}) 基类，不同之处在于非 *text* 部分不会被序列化，而是被表示为 基于模板并填写了有关该部分的信息的字符串输出流的形式。

## *class* email.generator.**DecodedGenerator**(*outfp*, *mangle_from_=None*, *maxheaderlen=None*, *fmt=None*, ***, *policy=None*)

​	行为类似于 [`Generator`]({{< ref "/library/netdata/email/email_generator#email.generator.Generator" >}})，不同之处在于对传给 [`Generator.flatten()`]({{< ref "/library/netdata/email/email_generator#email.generator.Generator.flatten" >}}) 的消息的任何子部分，如果该子部分的主类型为 *text*，则打印该子部分的已解码载荷，而如果其主类型不为 *text*，则不直接打印它而是使用来自该部分的信息填入字符串 *fmt* 并将填写完成的字符串打印出来。

​	要填入 *fmt*，则执行 `fmt % part_info`，其中 `part_info` 是由下列键和值组成的字典:

- `type` -- 非 *text* 部分的完整 MIME 类型
- `maintype` -- 非 *text* 部分的主 MIME 类型
- `subtype` -- 非 *text* 部分的子 MIME 类型
- `filename` -- 非 *text* 部分的文件名
- `description` -- 与非 *text* 部分相关联的描述
- `encoding` -- 非 *text* 部分的内容转换编码格式

​	如果 *fmt* 为 `None`，则使用下列默认 *fmt*:

> ​	"[忽略消息的非文本 (%(type)s) 部分，文件名 %(filename)s]"

​	可选的 *_mangle_from_* 和 *maxheaderlen* 与 [`Generator`]({{< ref "/library/netdata/email/email_generator#email.generator.Generator" >}}) 基类的相同。

​备注
[[1]({{< ref "/library/netdata/email/email_generator#id1" >}})]

​	此语句假定你使用了正确的 `unixfrom` 设置，并且没有针对自动调整的 [`email.policy`]({{< ref "/library/netdata/email/email_policy#module-email.policy" >}}) 设置调用（例如，[`refold_source`]({{< ref "/library/netdata/email/email_policy#email.policy.EmailPolicy.refold_source" >}}) 必须为 `none`，这 *不是* 默认值）。 这也不是 100% 为真的，因为如果消息不遵循 RFC 标准则有时实际原始文本的信息会在解析错误恢复时丢失。 它的目标是在可能的情况下修复这些后续的边缘情况。
