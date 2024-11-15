+++
title = "`email.policy`: 策略对象"
date = 2024-11-15T12:09:25+08:00
weight = 30
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/email.policy.html](https://docs.python.org/zh-cn/3.13/library/email.policy.html)
>
> 收录该文档的时间：`2024-11-15T12:09:25+08:00`

# `email.policy`: 策略对象

*Added in version 3.3.*

**源代码:** [Lib/email/policy.py](https://github.com/python/cpython/tree/3.13/Lib/email/policy.py)

------

[`email`](https://docs.python.org/zh-cn/3.13/library/email.html#module-email) 的主要焦点是按照各种电子邮件和 MIME RFC 的描述来处理电子邮件消息。 但是电子邮件消息的基本格式（一个由名称加冒号加值的标头字段构成的区块，后面再加一个空白行和任意的‘消息体’）是在电子邮件领域以外也获得应用的格式。 这些应用的规则有些与主要电子邮件 RFC 十分接近，有些则很不相同。 即使是操作电子邮件，有时也可能需要打破严格的 RFC 规则，例如生成可与某些并不遵循标准的电子邮件服务器互联的电子邮件，或者是实现希望应用某些破坏标准的操作方式的扩展。

​	Policy 对象给予 email 包处理这些不同用例的灵活性。

[`Policy`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy) 对象封装了一组属性和方法用来在使用期间控制 email 包中各个组件的行为。 [`Policy`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy) 实例可以被传给 email 包中的多个类和方法以更改它们的默认行为。 可设置的值及其默认值如下所述。

​	在 email 包中的所有类会使用一个默认的策略。 对于所有 [`parser`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#module-email.parser) 类及相关的便捷函数，还有对于 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 类来说，它是 [`Compat32`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Compat32) 策略，通过其对应的预定义实例 [`compat32`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.compat32) 来使用。 这个策略提供了与 Python3.3 版之前的 email 包的完全向下兼容性（在某些情况下，也包括对缺陷的兼容性）。

​	传给 [`EmailMessage`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage) 的 *policy* 关键字的默认值是 [`EmailPolicy`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.EmailPolicy) 策略，表示为其预定义的实例 [`default`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.default)。

​	在创建 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 或 [`EmailMessage`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage) 对象时，它需要一个策略。 如果消息是由 [`parser`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#module-email.parser) 创建的，则传给该解析器的策略将是它所创建的消息所使用的策略。 如果消息是由程序创建的，则该策略可以在创建它的时候指定。 当消息被传递给 [`generator`](https://docs.python.org/zh-cn/3.13/library/email.generator.html#module-email.generator) 时，生成器默认会使用来自该消息的策略，但你也可以将指定的策略传递给生成器，这将覆盖存储在消息对象上的策略。

[`email.parser`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#module-email.parser) 类和解析器便捷函数的 *policy* 关键字的默认值在未来的 Python 版本中 **将会改变**。 因此在调用任何 [`parser`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#module-email.parser) 模块所描述的类和函数时你应当 **总是显式地指定你想要使用的策略**。

​	本文档的第一部分介绍了 [`Policy`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy) 的特性，它是一个 [abstract base class](https://docs.python.org/zh-cn/3.13/glossary.html#term-abstract-base-class)，定义了所有策略对象包括 [`compat32`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.compat32) 的共有特性。 这些特性包括一些由 email 包内部调用的特定钩子方法，自定义策略可以重写这些方法以获得不同行为。 第二部分描述了实体类 [`EmailPolicy`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.EmailPolicy) 和 [`Compat32`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Compat32)，它们分别实现了提供标准行为和向下兼容行为与特性的钩子。

[`Policy`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy) 实例是不可变的，但它们可以被克隆，接受与类构造器一致的关键字参数并返回一个新的 [`Policy`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy) 实例，新实例是原实例的副本，但具有被改变的指定属性。

​	例如，以下代码可以被用来从一个 Unix 系统的磁盘文件中读取电子邮件消息并将其传递给系统的 `sendmail` 程序:

\>>>

```
>>> from email import message_from_binary_file
>>> from email.generator import BytesGenerator
>>> from email import policy
>>> from subprocess import Popen, PIPE
>>> with open('mymsg.txt', 'rb') as f:
...     msg = message_from_binary_file(f, policy=policy.default)
...
>>> p = Popen(['sendmail', msg['To'].addresses[0]], stdin=PIPE)
>>> g = BytesGenerator(p.stdin, policy=msg.policy.clone(linesep='\r\n'))
>>> g.flatten(msg)
>>> p.stdin.close()
>>> rc = p.wait()
```

​	这里我们让 [`BytesGenerator`](https://docs.python.org/zh-cn/3.13/library/email.generator.html#email.generator.BytesGenerator) 在创建要送入 `sendmail's` `stdin` 的二进制字串时使用符合 RFC 的行分隔字符，默认的策略将会使用 `\n` 行分隔符。

​	某些 email 包的方法接受一个 *policy* 关键字参数，允许为该方法覆盖原有策略。 例如，以下代码使用了来自之前示例的 *msg* 对象的 [`as_bytes()`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message.as_bytes) 方法并使用其运行所在平台的本机行分隔符将消息写入一个文件:

\>>>

```
>>> import os
>>> with open('converted.txt', 'wb') as f:
...     f.write(msg.as_bytes(policy=msg.policy.clone(linesep=os.linesep)))
17
```

​	Policy 对象也可使用加法运算符进行组合来产生一个新策略对象，其设置是被加总对象的非默认值的组合:

\>>>

```
>>> compat_SMTP = policy.compat32.clone(linesep='\r\n')
>>> compat_strict = policy.compat32.clone(raise_on_defect=True)
>>> compat_strict_SMTP = compat_SMTP + compat_strict
```

​	此运算不满足交换律；也就是说对象的添加顺序很重要。 见以下演示:

\>>>

```
>>> policy100 = policy.compat32.clone(max_line_length=100)
>>> policy80 = policy.compat32.clone(max_line_length=80)
>>> apolicy = policy100 + policy80
>>> apolicy.max_line_length
80
>>> apolicy = policy80 + policy100
>>> apolicy.max_line_length
100
```

## *class* email.policy.**Policy**(***kw*)

​	这是所有策略类的 [abstract base class](https://docs.python.org/zh-cn/3.13/glossary.html#term-abstract-base-class)。 它提供了一些简单方法的默认实现，以及不可变特征属性，[`clone()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.clone) 方法以及构造器语义的实现。

​	可以向策略类的构造器传入各种关键字参数。 可以指定的参数是该类的任何非方法特征属性，以及实体类的任何额外非方法特征属性。 在构造器中指定的值将覆盖相应属性的默认值。

​	这个类定义了下列特征属性，因此下列值可以被传给任何策略类的构造器:

## **max_line_length**

​	序列化输出中任何行的最大长度，不计入行字符的末尾。 默认值为 78，基于 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.html)。 值为 `0` 或 [`None`](https://docs.python.org/zh-cn/3.13/library/constants.html#None) 表示完全没有行包装。

## **linesep**

​	用来在序列化输出中确定行的字符串。 默认值为 `\n` 因为这是 Python 所使用的内部行结束符规范，但 RFC 的要求是 `\r\n`。

## **cte_type**

​	控制可能要求使用的内容传输编码格式类型。 可能的值包括:

| `7bit` | 所有数据必须为 "纯 7 比特位" (仅 ASCII)。 这意味着在必要情况下数据将使用可打印引用形式或 base64 编码格式进行编码。 |
| ------ | ------------------------------------------------------------ |
| `8bit` | 数据不会被限制为纯 7 比特位。 标头中的数据仍要求仅 ASCII 因此将被编码（参阅下文的 [`fold_binary()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.fold_binary) 和 [`utf8`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.EmailPolicy.utf8) 了解例外情况），但消息体部分可能使用 `8bit` CTE。 |

`cte_type` 值为 `8bit` 仅适用于 `BytesGenerator` 而非 `Generator`，因为字符串不能包含二进制数据。 如果 `Generator` 运行于指定了 `cte_type=8bit` 的策略，它的行为将与 `cte_type` 为 `7bit` 相同。

## **raise_on_defect**

​	如为 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)，则遇到的任何缺陷都将引发错误。 如为 [`False`](https://docs.python.org/zh-cn/3.13/library/constants.html#False) (默认值)，则缺陷将被传递给 [`register_defect()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.register_defect) 方法。

## **mangle_from_**

​	如为 [`True`](https://docs.python.org/zh-cn/3.13/library/constants.html#True)，则消息体中以 *"From "* 开头的行会通过在其前面放一个 `>` 来进行转义。 当消息被生成器执行序列化时会使用此形参。 默认值t: [`False`](https://docs.python.org/zh-cn/3.13/library/constants.html#False)。

*Added in version 3.5.*

## **message_factory**

​	用来构造新的空消息对象的工厂函数。 在构建消息时由解析器使用。 默认为 `None`，在此情况下会使用 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message)。

*Added in version 3.6.*

## **verify_generated_headers**

​	如为 `True` (默认值)，则生成器会引发 raise [`HeaderWriteError`](https://docs.python.org/zh-cn/3.13/library/email.errors.html#email.errors.HeaderWriteError) 而不是写入一个不正确地折叠或设限的标头，以便它可以被解析为多重标头或与相邻数据合并。 这样的标头可通过自定义标头类或 `email` 模块中的程序错误来生成。

​	由于它是一个安全特性，即使是在 [`Compat32`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Compat32) 策略中该值也默认为 `True`。 为了保持向下兼容但是不安全的行为，它必须显式地被设为 `False`。

*Added in version 3.13.*

​	下列 [`Policy`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy) 方法是由使用 email 库的代码来调用以创建具有自室外设置的策略实例:

## **clone**(***kw*)

​	返回一个新的 [`Policy`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy) 实例，其属性与当前实例具有相同的值，除非是那些由关键字参数给出了新值的属性。

​	其余的 [`Policy`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy) 方法是由 email 包代码来调用的，而不应当被使用 email 包的应用程序所调用。 自定义的策略必须实现所有这些方法。

## **handle_defect**(*obj*, *defect*)

​	处理在 *obj* 上发现的 *defect*。 当 email 包调用此方法时，*defect* 将总是 `Defect` 的一个子类。

​	默认实现会检查 [`raise_on_defect`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.raise_on_defect) 旗标。 如果其为 `True`，则 *defect* 会被作为异常来引发。 如果其为 `False` (默认值)，则 *obj* 和 *defect* 会被传递给 [`register_defect()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.register_defect)。

## **register_defect**(*obj*, *defect*)

​	在 *obj* 上注册一个 *defect*。 在 email 包中，*defect* 将总是 `Defect` 的一个子类。

​	默认实现会调用 *obj* 的 `defects` 属性的 `append` 方法。 当 email 包调用 [`handle_defect`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.handle_defect) 时，*obj* 通常将具有一个带 `append` 方法的 `defects` 属性。 配合 email 包使用的自定义对象类型（例如自定义的 `Message` 对象）也应当提供这样的属性，否则在被解析消息中的缺陷将引发非预期的错误。

## **header_max_count**(*name*)

​	返回名为 *name* 的标头的最大允许数量。

​	当添加一个标头到 [`EmailMessage`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage) 或 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 对象时被调用。 如果返回值不为 `0` 或 `None`，并且已有的名称为 *name* 的标头数量大于等于所返回的值，则会引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

​	由于 `Message.__setitem__` 的默认行为是将值添加到标头列表，因此很容易不知情地创建重复的标头。 此方法允许在程序中限制可以被添加到 `Message` 中的特定标头的实例数量。 （解析器不会考虑此限制，它将忠实地产生被解析消息中存在的任意数量的标头。）

​	默认实现对于所有标头名称都返回 `None`。

## **header_source_parse**(*sourcelines*)

​	email 包调用此方法时将传入一个字符串列表，其中每个字符串以在被解析源中找到的行分隔符结束。 第一行包括字段标头名称和分隔符。 源中的所有空白符都会被保留。 此方法应当返回 `(name, value)` 元组以保存至 `Message` 中来代表被解析的标头。

​	如果一个实现希望保持与现有 email 包策略的兼容性，则 *name* 应当为保留大小写形式的名称（所有字符直至 '`:`' 分隔符），而 *value* 应当为展开后的值（移除所有行分隔符，但空白符保持不变），并移除开头的空白符。

*sourcelines* 可以包含经替代转义的二进制数据。

​	此方法没有默认实现

## **header_store_parse**(*name*, *value*)

​	当一个应用通过程序代码修改 `Message` (而不是由解析器创建 `Message`) 时，email 包会调用此方法并附带应用程序所提供的名称和值。 此方法应当返回 `(name, value)` 元组以保存至 `Message` 中用来表示标头。

​	如果一个实现希望保持与现有 email 包策略的兼容性，则 *name* 和 *value* 应当为字符串或字符串的子类，它们不会修改在参数中传入的内容。

​	此方法没有默认实现

## **header_fetch_parse**(*name*, *value*)

​	当标头被应用程序所请求时，email 包会调用此方法并附带当前保存在 `Message` 中的 *name* 和 *value*，并且无论此方法返回什么它都会被回传给应用程序作为被提取标头的值。 请注意可能会有多个相同名称的标头被保存在 `Message` 中；此方法会将指定标头的名称和值返回给应用程序。

*value* 可能包含经替代转义的二进制数据。 此方法所返回的值应当没有经替代转义的二进制数据。

​	此方法没有默认实现

## **fold**(*name*, *value*)

​	email 包调用此方法时会附带当前保存在 `Message` 中的给定标头的 *name* 和 *value*。 此方法应当返回一个代表该标头的（根据策略设置）通过处理 *name* 和 *value* 并在适当位置插入 [`linesep`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.linesep) 字符来正确地“折叠”的字符串。 请参阅 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.html) 了解有关折叠电子邮件标头的规则的讨论。

*value* 可能包含经替代转义的二进制数据。 此方法所返回的字符串应当没有经替代转义的二进制数据。

## **fold_binary**(*name*, *value*)

​	与 [`fold()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.fold) 类似，不同之处在于返回的值应当为字节串对象而非字符串。

*value* 可能包含经替代转义的二进制数据。 这些数据可以在被返回的字节串对象中被转换回二进制数据。

## *class* email.policy.**EmailPolicy**(***kw*)

​	这个实体 [`Policy`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy) 提供了完全遵循当前电子邮件 RFC 的行为。 这包括 (但不限于) [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.html), [**RFC 2047**](https://datatracker.ietf.org/doc/html/rfc2047.html) 以及当前的各种 MIME RFC。

​	此策略添加了新的标头解析和折叠算法。 标头不是简单的字符串，而是带有依赖于字段类型的属性的 `str` 的子类。 这个解析和折叠算法完整实现了 [**RFC 2047**](https://datatracker.ietf.org/doc/html/rfc2047.html) 和 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.html)。

[`message_factory`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.message_factory) 属性的默认值为 [`EmailMessage`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage)。

​	除了上面列出的适用于所有策略的可设置属性，此策略还添加了下列额外属性:

*Added in version 3.6:* [[1\]](https://docs.python.org/zh-cn/3.13/library/email.policy.html#id2)

## **utf8**

​	如为 `False`，则遵循 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.html)，通过编码为“已编码字”来支持标头中的非 ASCII 字符。 如为 `True`，则遵循 [**RFC 6532**](https://datatracker.ietf.org/doc/html/rfc6532.html) 并对标头使用 `utf-8` 编码格式。 以此方式格式化的消息可被传递给支持 `SMTPUTF8` 扩展 ([**RFC 6531**](https://datatracker.ietf.org/doc/html/rfc6531.html)) 的 SMTP 服务器。

## **refold_source**

​	如果 `Message` 对象中标头的值源自 [`parser`](https://docs.python.org/zh-cn/3.13/library/email.parser.html#module-email.parser) (而非由程序设置)，此属性会表明当将消息转换回序列化形式时是否应当由生成器来重新折叠该值。 可能的值如下:

| `none` | 所有源值使用原始折叠                                  |
| ------ | ----------------------------------------------------- |
| `long` | 具有任何长度超过 `max_line_length` 的行的源值将被折叠 |
| `all`  | 所有值会被重新折叠。                                  |

​	默认值为 `long`。

## **header_factory**

​	该可调用对象接受两个参数，`name` 和 `value`，其中 `name` 为标头字段名而 `value` 为展开后的标头字段值，并返回一个表示该标头的字符串子类。 已提供的默认 `header_factory` (参见 [`headerregistry`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#module-email.headerregistry)) 支持对各种地址和日期 [**RFC 5322**](https://datatracker.ietf.org/doc/html/rfc5322.html) 标头字段类型及主要 MIME 标头字段类型的自定义解析。 未来还将添加对其他自定义解析的支持。

## **content_manager**

​	此对象至少有两个方法: get_content 和 set_content。 当一个 [`EmailMessage`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage) 对象的 [`get_content()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.get_content) 或 [`set_content()`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage.set_content) 方法被调用时，它会调用此对象的相应方法，将消息对象作为其第一个参数，并将传给它的任何参数或关键字作为附加参数传入。 默认情况下 `content_manager` 会被设为 [`raw_data_manager`](https://docs.python.org/zh-cn/3.13/library/email.contentmanager.html#email.contentmanager.raw_data_manager)。

*Added in version 3.4.*

​	这个类提供了下列对 [`Policy`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy) 的抽象方法的具体实现:

## **header_max_count**(*name*)

​	返回用来表示具有给定名称的标头的专用类的 [`max_count`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#email.headerregistry.BaseHeader.max_count) 属性的值。

## **header_source_parse**(*sourcelines*)

​	此名称会被作为到 '`:`' 止的所有内容来解析。 该值是通过从第一行的剩余部分去除前导空格，再将所有后续行连接到一起，并去除所有末尾回车符或换行符来确定的。

## **header_store_parse**(*name*, *value*)

​	name 将会被原样返回。 如果输入值具有 `name` 属性并可在忽略大小写的情况下匹配 *name*，则 value 也会被原样返回。 在其他情况下 *name* 和 *value* 会被传递给 `header_factory`，并将结果标头对象作为值返回。 在此情况下如果输入值包含 CR 或 LF 字符则会引发 `ValueError`。

## **header_fetch_parse**(*name*, *value*)

​	如果值具有 `name` 属性，它会被原样返回。 在其他情况下 *name* 和移除了所有 CR 和 LF 字符的 *value* 会被传递给 `header_factory`，并返回结果标头对象。 任何经替代转义的字节串会被转换为 unicode 未知字符字形。

## **fold**(*name*, *value*)

​	标头折叠是由 [`refold_source`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.EmailPolicy.refold_source) 策略设置来控制的。 当且仅当一个值没有 `name` 属性（具有 `name` 属性就意味着它是某种标头对象）它才会被当作是“源值”。 如果一个原值需要按照策略来重新折叠，则会通过将 *name* 和去除了所有 CR 和 LF 字符的 *value* 传递给 `header_factory` 来将其转换为标头对象。 标头对象的折叠是通过调用其 `fold` 方法并附带当前策略来完成的。

​	源值会使用 [`splitlines()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str.splitlines) 来拆分成多行。 如果该值不被重新折叠，则会使用策略中的 `linesep` 重新合并这些行并将其返回。 例外的是包含非 ascii 二进制数据的行。 在此情况下无论 `refold_source` 如何设置该值都会被重新折叠，这会导致二进制数据使用 `unknown-8bit` 字符集进行 CTE 编码。

## **fold_binary**(*name*, *value*)

​	如果 [`cte_type`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.cte_type) 为 `7bit` 则与 [`fold()`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.EmailPolicy.fold) 类似，不同之处在于返回的值是字节串。

​	如果 [`cte_type`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy.cte_type) 为 `8bit`，则将非 ASCII 二进制数据转换回字节串。 带有二进制数据的标头不会被重新折叠，无论 `refold_header` 设置如何，因为无法知晓该二进制数据是由单字节字符还是多字节字符组成的。

​	以下 [`EmailPolicy`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.EmailPolicy) 的实例提供了适用于特定应用领域的默认值。 请注意在未来这些实例（特别是 `HTTP` 实例）的行为可能会被调整以便更严格地遵循与其领域相关的 RFC。

## email.policy.**default**

​	一个未改变任何默认值的 `EmailPolicy` 实例。 此策略使用标准的 Python `\n` 行结束符而非遵循 RFC 的 `\r\n`。

## email.policy.**SMTP**

​	适用于按照符合电子邮件 RFC 的方式来序列化消息。 与 `default` 类似，但 `linesep` 被设为遵循 RFC 的 `\r\n`。

## email.policy.**SMTPUTF8**

​	与 `SMTP` 类似但是 [`utf8`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.EmailPolicy.utf8) 为 `True`。 适用于在不使用标头内已编码字的情况下对消息进行序列化。 如果发送方或接收方地址具有非 ASCII 字符则应当只被用于 SMTP 传输 ([`smtplib.SMTP.send_message()`](https://docs.python.org/zh-cn/3.13/library/smtplib.html#smtplib.SMTP.send_message) 方法会自动如此处理)。

## email.policy.**HTTP**

​	适用于序列化标头以在 HTTP 通信中使用。 与 `SMTP` 类似但是 `max_line_length` 被设为 `None` (无限制)。

## email.policy.**strict**

​	便捷实例。 与 `default` 类似但是 `raise_on_defect` 被设为 `True`。 这样可以允许通过以下写法来严格地设置任何策略:

```
somepolicy + policy.strict
```

​	因为所有这些 [`EmailPolicies`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.EmailPolicy)，email 包的高效 API 相比 Python 3.2 API 发生了以下几方面变化:

- 在 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 中设置标头将使得该标头被解析并创建一个标头对象。
- 从 [`Message`](https://docs.python.org/zh-cn/3.13/library/email.compat32-message.html#email.message.Message) 提取标头将使得该标头被解析并创建和返回一个标头对象。
- 任何标头对象或任何由于策略设置而被重新折叠的标头都会使用一种完全实现了 RFC 折叠算法的算法来进行折叠，包括知道在休息需要并允许已编码字。

​	从应用程序的视角来看，这意味着任何通过 [`EmailMessage`](https://docs.python.org/zh-cn/3.13/library/email.message.html#email.message.EmailMessage) 获得的标头都是具有附加属性的标头对象，其字符串值都是该标头的完全解码后的 unicode 值。 类似地，可以使用 unicode 对象为一个标头赋予新的值，或创建一个新的标头对象，并且该策略将负责把该 unicode 字符串转换为正确的 RFC 已编码形式。

​	标头对象及其属性的描述见 [`headerregistry`](https://docs.python.org/zh-cn/3.13/library/email.headerregistry.html#module-email.headerregistry)。

## *class* email.policy.**Compat32**(***kw*)

​	这个实体 [`Policy`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy) 为向下兼容策略。 它复制了 Python 3.2 中 email 包的行为。 [`policy`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#module-email.policy) 模块还定义了该类的一个实例 [`compat32`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.compat32)，用来作为默认策略。 因此 email 包的默认行为会保持与 Python 3.2 的兼容性。

​	下列属性具有与 [`Policy`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy) 默认值不同的值:

## **mangle_from_**

​	默认值为 `True`。

​	这个类提供了下列对 [`Policy`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Policy) 的抽象方法的具体实现:

## **header_source_parse**(*sourcelines*)

​	此名称会被作为到 '`:`' 止的所有内容来解析。 该值是通过从第一行的剩余部分去除前导空格，再将所有后续行连接到一起，并去除所有末尾回车符或换行符来确定的。

## **header_store_parse**(*name*, *value*)

​	name 和 value 会被原样返回。

## **header_fetch_parse**(*name*, *value*)

​	如果 value 包含二进制数据，则会使用 `unknown-8bit` 字符集来将其转换为 [`Header`](https://docs.python.org/zh-cn/3.13/library/email.header.html#email.header.Header) 对象。 在其他情况下它会被原样返回。

## **fold**(*name*, *value*)

​	标头会使用 [`Header`](https://docs.python.org/zh-cn/3.13/library/email.header.html#email.header.Header) 折叠算法进行折叠，该算法保留 value 中现有的换行，并将每个结果行的长度折叠至 `max_line_length`。 非 ASCII 二进制数据会使用 `unknown-8bit` 字符串进行 CTE 编码。

## **fold_binary**(*name*, *value*)

​	标头会使用 [`Header`](https://docs.python.org/zh-cn/3.13/library/email.header.html#email.header.Header) 折叠算法进行折叠，该算法保留 value 中现有的换行，并将每个结果行的长度折叠至 `max_line_length`。 如果 `cte_type` 为 `7bit`，则非 ascii 二进制数据会使用 `unknown-8bit` 字符集进行 CTE 编码。 在其他情况下则会使用原始的源标头，这将保留其现有的换行和所包含的任何（不符合 RFC 的）二进制数据。

## email.policy.**compat32**

[`Compat32`](https://docs.python.org/zh-cn/3.13/library/email.policy.html#email.policy.Compat32) 的实例，提供与 Python 3.2 中的 email 包行为的向下兼容性。

​	备注

[[1](https://docs.python.org/zh-cn/3.13/library/email.policy.html#id1)]

​	最初在 3.3 中作为 [暂定特性](https://docs.python.org/zh-cn/3.13/glossary.html#term-provisional-package) 添加。