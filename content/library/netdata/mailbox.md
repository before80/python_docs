+++
title = "mailbox --- 操纵多种格式的邮箱"
date = 2024-11-15T14:53:40+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/mailbox.html](https://docs.python.org/zh-cn/3.13/library/mailbox.html)
>
> 收录该文档的时间：`2024-11-15T14:53:40+08:00`

# `mailbox` --- 操纵多种格式的邮箱

**源代码：** [Lib/mailbox.py](https://github.com/python/cpython/tree/3.13/Lib/mailbox.py)

------

​	本模块定义了两个类，[`Mailbox`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox" >}}) 和 [`Message`]({{< ref "/library/netdata/mailbox#mailbox.Message" >}})，用于访问和操作磁盘中的邮箱及其所包含的电子邮件。 `Mailbox` 提供了类似字典的从键到消息的映射。 `Message` 为 [`email.message`]({{< ref "/library/netdata/email/email_message#module-email.message" >}}) 模块的 [`Message`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message" >}}) 类扩展了特定格式专属的状态和行为。 支持的邮箱格式有 Maildir, mbox, MH, Babyl 和 MMDF。

​参见
## 模块 [`email`]({{< ref "/library/netdata/email#module-email" >}})

​	表示和操作邮件消息。



## `Mailbox` 对象

## *class* mailbox.**Mailbox**

​	一个邮箱，它可以被检视和修改。

`Mailbox` 类定义了一个接口并且它不应被实例化。 而是应该让格式专属的子类继承 `Mailbox` 并且你的代码应当实例化一个特定的子类。

`Mailbox` 接口类似于字典，其中每个小键都有对应的消息。 键是由 `Mailbox` 实例发出的，它们将由实例来使用并且只对该 `Mailbox` 实例有意义。 键会持续标识一条消息，即使对应的消息已被修改，例如被另一条消息所替代。

​	可以使用类似于集合的方法 [`add()`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox.add" >}}) 将消息添加到 `Mailbox` 实例并使用 `del` 语句或类似于集合的方法 [`remove()`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox.remove" >}}) 和 [`discard()`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox.discard" >}}) 将其移除。

`Mailbox` 接口语义在某些值得注意的方面与字典语义有所不同。 每次请求消息时，都会基于邮箱的当前状态生成一个新的表示形式（通常为 [`Message`]({{< ref "/library/netdata/mailbox#mailbox.Message" >}}) 实例）。 类似地，当向 `Mailbox` 实例添加消息时，所提供的消息表示形式的内容将被复制。 无论在哪种情况下 `Mailbox` 实例都不会保留对消息表示形式的引用。

​	默认的 `Mailbox` [iterator]({{< ref "/glossary/idx#term-iterator" >}}) 会迭代消息表示形式，而不像默认的 [`字典`]({{< ref "/library/stdtypes#dict" >}}) 迭代器那样迭代键。 此外，在迭代期间修改邮箱是安全且有明确定义的。 在创建迭代器之后被添加到邮箱的消息将对该迭代不可见。 在迭代器产出消息之前从邮箱移除的消息将被静默地跳过，但是使用来自迭代器的键也有可能导致 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}}) 异常，如果对应的消息后来被移除的话。

​	警告

 

​	在修改可能同时被其他某个进程修改的邮箱时要非常小心。 用于此种任务的最安全邮箱格式是 [`Maildir`]({{< ref "/library/netdata/mailbox#mailbox.Maildir" >}})；请尽量避免使用 [`mbox`]({{< ref "/library/netdata/mailbox#mailbox.mbox" >}}) 之类的单文件格式进行并发写入。 如果你正在修改一个邮箱，你 *必须* 在读取文件中的任何消息或者执行添加或删除消息等修改操作 *之前* 通过调用 [`lock()`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox.lock" >}}) 和 [`unlock()`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox.unlock" >}}) 方法来锁定它。 如果未锁定邮箱则将导致丢失消息或损坏整个邮箱的风险。

`Mailbox` 实例具有下列方法：

## **add**(*message*)

​	将 *message* 添加到邮箱并返回分配给它的键。

​	形参 *message* 可以是 [`Message`]({{< ref "/library/netdata/mailbox#mailbox.Message" >}}) 实例、[`email.message.Message`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message" >}}) 实例、字符串、字节串或文件型对象（应当以二进制模式打开）。 如果 *message* 是适当的格式专属 [`Message`]({{< ref "/library/netdata/mailbox#mailbox.Message" >}}) 子类的实例（举例来说，如果它是一个 [`mboxMessage`]({{< ref "/library/netdata/mailbox#mailbox.mboxMessage" >}}) 实例而这是一个 [`mbox`]({{< ref "/library/netdata/mailbox#mailbox.mbox" >}}) 实例），将使用其格式专属的信息。 在其他情况下，则会使用合理的默认值作为格式专属的信息。

> 在 3.2 版本发生变更: 增加了对二进制输入的支持。

## **remove**(*key*)

## `__delitem__`(*key*)

## **discard**(*key*)

​	从邮箱中删除对应于 *key* 的消息。

​	当消息不存在时，如果此方法是作为 [`remove()`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox.remove" >}}) 或 [`__delitem__()`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox.__delitem__" >}}) 调用则会引发 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}}) 异常，而如果此方法是作为 [`discard()`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox.discard" >}}) 调用则不会引发异常。 如果下层邮箱格式支持来自其他进程的并发修改则 [`discard()`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox.discard" >}}) 的行为可能是更为适合的。

## `__setitem__`(*key*, *message*)

​	将 *key* 所对应的消息替换为 *message*。 如果没有与 *key* 所对应的消息则会引发 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}}) 异常。

​	与 [`add()`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox.add" >}}) 一样，形参 *message* 可以是 [`Message`]({{< ref "/library/netdata/mailbox#mailbox.Message" >}}) 实例、[`email.message.Message`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message" >}}) 实例、字符串、字节串或文件型对象（应当以二进制模式打开）。 如果 *message* 是适当的格式专属 [`Message`]({{< ref "/library/netdata/mailbox#mailbox.Message" >}}) 子类的实例（举例来说，如果它是一个 [`mboxMessage`]({{< ref "/library/netdata/mailbox#mailbox.mboxMessage" >}}) 实例而这是一个 [`mbox`]({{< ref "/library/netdata/mailbox#mailbox.mbox" >}}) 实例），将使用其格式专属的信息。 在其他情况下，当前与 *key* 所对应的消息的格式专属信息则会保持不变。

## **iterkeys**()

​	返回一个迭代所有键的 [iterator]({{< ref "/glossary/idx#term-iterator" >}})

## **keys**()

​	与 [`iterkeys()`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox.iterkeys" >}}) 类似，不同之处是返回 [`list`]({{< ref "/library/stdtypes#list" >}}) 而不是 [iterator]({{< ref "/glossary/idx#term-iterator" >}})

## **itervalues**()

## `__iter__`()

​	返回一个迭代所有消息的表示形式的 [iterator]({{< ref "/glossary/idx#term-iterator" >}})。 消息会被表示为适当的格式专属 [`Message`]({{< ref "/library/netdata/mailbox#mailbox.Message" >}}) 子类的实例，除非当 `Mailbox` 实例被初始化时指定了自定义的消息工厂函数。

​备注
 

[`__iter__()`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox.__iter__" >}}) 的行为与字典不同，后者是对键进行迭代。

## **values**()

​	与 [`itervalues()`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox.itervalues" >}}) 类似，不同之处是返回 [`list`]({{< ref "/library/stdtypes#list" >}}) 而不是 [iterator]({{< ref "/glossary/idx#term-iterator" >}})

## **iteritems**()

​	返回一个包含 (*key*, *message*) 对的 [iterator]({{< ref "/glossary/idx#term-iterator" >}})，其中 *key* 为键而 *message* 为消息表示形式。 消息会被表示为适当的格式专属 [`Message`]({{< ref "/library/netdata/mailbox#mailbox.Message" >}}) 子类的实例，除非当 `Mailbox` 实例被初始化时指定了自定义的消息工厂函数。

## **items**()

​	与 [`iteritems()`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox.iteritems" >}}) 类似，不同之处是返回包含键值对的 [`list`]({{< ref "/library/stdtypes#list" >}}) 而不是键值对的 [iterator]({{< ref "/glossary/idx#term-iterator" >}})。

## **get**(*key*, *default=None*)

## `__getitem__`(*key*)

​	返回对应于 *key* 的消息的表示形式。 当对应的消息不存在时，如果该方法是通过 [`get()`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox.get" >}}) 调用则返回 *default*，而如果该方法是通过 `__getitem__()` 调用则会引发 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}})。 消息会被表示为适当的格式专属 [`Message`]({{< ref "/library/netdata/mailbox#mailbox.Message" >}}) 子类的实例，除非当 `Mailbox` 被初始化时指定了自定义的消息工厂函数。

## **get_message**(*key*)

​	将对应于 *key* 的消息的表示形式作为适当的格式专属 [`Message`]({{< ref "/library/netdata/mailbox#mailbox.Message" >}}) 子类的实例返回，或者如果对应的消息不存在则会引发 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}}) 异常。

## **get_bytes**(*key*)

​	返回对应于 *key* 的消息的字节表示形式，或者如果对应的消息不存在则会引发 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}}) 异常。

> Added in version 3.2.
>

## **get_string**(*key*)

​	返回对应于 *key* 的消息的字符串表示形式，或者如果对应的消息不存在则会引发 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}}) 异常。 消息是通过 [`email.message.Message`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message" >}}) 处理来将其转换为纯 7bit 表示形式的。

## **get_file**(*key*)

​	返回对应于 *key* 的消息的 [文件类]({{< ref "/glossary/idx#term-file-like-object" >}}) 表示形式，或者如果对应的消息不存在则会引发 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}}) 异常。 文件型对象的行为相当于以二进制模式打开。 当不再需要此文件时应当将其关闭。

> 在 3.2 版本发生变更: 此文件对象实际上是一个 [binary file]({{< ref "/glossary/idx#term-binary-file" >}})；之前它被不正确地以文本模式返回。 并且，此 [file-like object]({{< ref "/glossary/idx#term-file-like-object" >}}) 现在还支持 [context manager]({{< ref "/glossary/idx#term-context-manager" >}}) 协议：你可以使用 [`with`]({{< ref "/reference/compound_stmts#with" >}}) 语句来自动关闭它。

​备注
 

​	不同于其他消息表示形式，[文件类]({{< ref "/glossary/idx#term-file-like-object" >}}) 表示形式并不一定独立于创建它们的 `Mailbox` 实例或下层的邮箱。 每个子类都会提供更具体的文档。

## `__contains__`(*key*)

​	如果 *key* 有对应的消息则返回 `True`，否则返回 `False`。

## `__len__`()

​	返回邮箱中消息的数量。

## **clear**()

​	从邮箱中删除所有消息。

## **pop**(*key*, *default=None*)

​	返回对应于 *key* 的消息的表示形式并删除该消息。 如果对应的消息不存在则返回 *default*。 消息会被表示为适当的格式专属 [`Message`]({{< ref "/library/netdata/mailbox#mailbox.Message" >}}) 子类的实例，除非当 `Mailbox` 实例被初始化时指定的自定义的消息工厂函数。

## **popitem**()

​	返回一个任意的 (*key*, *message*) 对，其中 *key* 为键而 *message* 为消息的表示形式，并删除对应的消息。 如果邮箱为空，则会引发 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}}) 异常。 消息会被表示为适当的格式专属 [`Message`]({{< ref "/library/netdata/mailbox#mailbox.Message" >}}) 子类的实例，除非当 `Mailbox` 实例被初始化时指定了自定义的消息工厂函数。

## **update**(*arg*)

​	形参 *arg* 应当是 *key* 到 *message* 的映射或 (*key*, *message*) 对的可迭代对象。 用来更新邮箱以使得对于每个给定的 *key* 和 *message*，与 *key* 相对应的消息会被设为 *message*，就像通过使用 [`__setitem__()`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox.__setitem__" >}}) 一样。 类似于 [`__setitem__()`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox.__setitem__" >}})，每个 *key* 都必须在邮箱中有一个对应的消息否则将会引发 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}}) 异常。 因此在通常情况下将 *arg* 设为 `Mailbox` 实例是不正确的。

​备注
 

​	与字典不同，关键字参数是不受支持的。

## **flush**()

​	将所有待定的更改写入到文件系统。 对于某些 [`Mailbox`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox" >}}) 子类来说，更改总是被立即写入因而 `flush()` 将不做任何事，但你仍然应当养成调用此方法的习惯。

## **lock**()

​	在邮箱上获取一个独占式咨询锁以使其他进程知道不能修改它。 如果锁无法被获取则会引发 [`ExternalClashError`]({{< ref "/library/netdata/mailbox#mailbox.ExternalClashError" >}})。 所使用的具体锁机制取决于邮箱的格式。 在对邮箱内容进行任何修改之前你应当 *总是* 锁定它。

## **unlock**()

​	释放邮箱上的锁，如果存在的话。

## **close**()

​	刷新邮箱，如有必要则将其解锁，并关闭所有打开的文件。 对于某些 `Mailbox` 子类来说，此方法不会做任何事。



### `Maildir` 对象

## *class* mailbox.**Maildir**(*dirname*, *factory=None*, *create=True*)

[`Mailbox`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox" >}}) 的一个子类，用于 Maildir 格式的邮箱。 形参 *factory* 是一个可调用对象，它接受一个文件类消息表示形式（其行为相当于以二进制模式打开）并返回一个自定义的表示形式。 如果 *factory* 为 `None`，则会使用 [`MaildirMessage`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage" >}}) 作为默认的消息表示形式。 如果 *create* 为 `True`，则当邮箱不存在时会创建它。

​	如果 *create* 为 `True` 且 *dirname* 路径存在，它将被视为已有的 maildir 而无需尝试验证其目录布局。

​	使用 *dirname* 这个名称而不使用 *path* 是出于历史原因。

​	Maildir 是一种基于目录的邮箱格式，它是针对 qmail 邮件传输代理而发明的，现在也得到了其他程序的广泛支持。 Maildir 邮箱中的消息存储在一个公共目录结构中的单独文件内。 这样的设计允许 Maildir 邮箱被多个彼此无关的程序访问和修改而不会导致数据损坏，因此文件锁定操作是不必要的。

​	Maildir 邮箱包含三个子目录，分别是: `tmp`, `new` 和 `cur`。 消息会不时地在 `tmp` 子目录中创建然后移至 `new` 子目录来结束投递。 后续电子邮件客户端可能将消息移至 `cur` 子目录并将有关消息状态的信息存储在附带到其文件名的特殊 "info" 小节中。

​	Courier 邮件传输代理所引入的文件夹风格也是受支持的。 主邮箱中任何子目录只要其名称的第一个字符是 `'.'` 就会被视为文件夹。 文件夹名称会被 `Maildir` 表示为不带前缀 `'.'` 的形式。 每个文件夹自身都是一个 Maildir 邮箱但不应包含其他文件夹。 逻辑嵌套关系是使用 `'.'` 来划定层级，例如 "Archived.2005.07"。

## **colon**

​	Maildir 规范要求使用在特定消息文件名中使用冒号 (`':'`)。 但是，某些操作系统不允许将此字符用于文件名，如果你希望在这些操作系统上使用类似 Maildir 的格式，你应当指定改用另一个字符。 叹号 (`'!'`) 是一个受欢迎的选择。 例如:

```
import mailbox
mailbox.Maildir.colon = '!'
```

`colon` 属性也可以在每个实例上分别设置。

> 在 3.13 版本发生变更: 现在 [`Maildir`]({{< ref "/library/netdata/mailbox#mailbox.Maildir" >}}) 会忽略以点号打头的文件。

`Maildir` 实例具有 [`Mailbox`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox" >}}) 的所有方法及下列附加方法：

## **list_folders**()

​	返回所有文件夹名称的列表。

## **get_folder**(*folder*)

​	返回表示名称为 *folder* 的文件夹的 `Maildir` 实例。 如果文件夹不存在则会引发 [`NoSuchMailboxError`]({{< ref "/library/netdata/mailbox#mailbox.NoSuchMailboxError" >}}) 异常。

## **add_folder**(*folder*)

​	创建一个名称为 *folder* 的文件夹并返回代表它的 `Maildir` 实例。

## **remove_folder**(*folder*)

​	删除名称为 *folder* 的文件夹。 如果文件夹包含任何消息，则将引发 [`NotEmptyError`]({{< ref "/library/netdata/mailbox#mailbox.NotEmptyError" >}}) 异常且该文件夹将不会被删除。

## **clean**()

​	从邮箱中删除最近 36 小时内未被访问过的临时文件。 Maildir 规范要求邮件阅读程序应当时常进行此操作。

## **get_flags**(*key*)

​	以字符串形式返回在对应于 *key* 的消息上设置的旗标。 这等同于 `get_message(key).get_flags()` 但更为快速，因为它不会打开消息文件。 在迭代键时可使用此方法来确定哪些消息是值得获取的。

​	如果你确实有一个 [`MaildirMessage`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage" >}}) 对象，请改用其 [`get_flags()`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage.get_flags" >}}) 方法，因为由消息的 [`set_flags()`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage.set_flags" >}}), [`add_flag()`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage.add_flag" >}}) 和 [`remove_flag()`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage.remove_flag" >}}) 方法所做的修改在邮箱的 [`__setitem__()`]({{< ref "/library/netdata/mailbox#mailbox.Maildir.__setitem__" >}}) 方法被调用之前都不会在这里反映出来。

> Added in version 3.13.
>

## **set_flags**(*key*, *flags*)

​	在对应于 *key* 的消息上，设置由 *flags* 指定的旗标并取消设置所有其他旗标。 调用 `some_mailbox.set_flags(key, flags)` 就类似于

```
one_message = some_mailbox.get_message(key)
one_message.set_flags(flags)
some_mailbox[key] = one_message
```

​	但更为快速，因为它不会打开消息文件。

​	如果你确实有一个 [`MaildirMessage`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage" >}}) 对象，请改用其 [`set_flags()`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage.set_flags" >}}) 方法，因为用此邮箱方法所做的修改对消息对象的方法 [`get_flags()`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage.get_flags" >}}) 来说将不可见。

> Added in version 3.13.
>

## **add_flag**(*key*, *flag*)

​	在对应于 *key* 的消息上，设置由 *flag* 指定的旗标而不改变其他旗标。 要一次性添加多个旗标，*flag* 可以是包含多个字符的字符串。

​	对于是使用此方法还是使用消息对象的 [`add_flag()`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage.add_flag" >}}) 方法的考量与 [`set_flags()`]({{< ref "/library/netdata/mailbox#mailbox.Maildir.set_flags" >}}) 类似；参见那里的讨论。

> Added in version 3.13.
>

## **remove_flag**(*key*, *flag*)

​	在对应于 *key* 的消息上，取消设置由 *flag* 指定的旗标而不改变其他旗标。 要一次性移除多个旗标，*flag* 可以是包含多个字符的字符串。

​	对于是使用此方法还是使用消息对象的 [`remove_flag()`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage.remove_flag" >}}) 方法的考量与 [`set_flags()`]({{< ref "/library/netdata/mailbox#mailbox.Maildir.set_flags" >}}) 类似；参见那里的讨论。

> Added in version 3.13.
>

## **get_info**(*key*)

​	以字符串形式返回包含对应于 *key* 的消息的信息的字符串。 这等同于 `get_message(key).get_info()` 但更为快速，因为它不会打开消息文件。 在迭代键时可使用此方法来确定哪些消息是值得获取的。

​	如果你确实有一个 [`MaildirMessage`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage" >}}) 对象，请改用其 [`get_info()`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage.get_info" >}}) 方法，因为由消息的 [`set_info()`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage.set_info" >}}) 方法所做的修改在邮箱的 [`__setitem__()`]({{< ref "/library/netdata/mailbox#mailbox.Maildir.__setitem__" >}}) 方法被调用之前都不会在这里反映出来。

> Added in version 3.13.
>

## **set_info**(*key*, *info*)

​	将对应于 *key* 的消息的信息设为 *info*。 调用 `some_mailbox.set_info(key, flags)` 就类似于

```
one_message = some_mailbox.get_message(key)
one_message.set_info(info)
some_mailbox[key] = one_message
```

​	但更为快速，因为它不会打开消息文件。

​	如果你确实有一个 [`MaildirMessage`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage" >}}) 对象，请改用其 [`set_info()`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage.set_info" >}}) 方法，因为用此邮箱方法所做的修改对消息对象的方法 [`get_info()`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage.get_info" >}}) 来说将不可见。

> Added in version 3.13.
>

`Maildir` 所实现的某些 [`Mailbox`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox" >}}) 方法值得进行特别说明：

## **add**(*message*)

## `__setitem__`(*key*, *message*)

## **update**(*arg*)

​	警告

 

​	这些方法会基于当前进程 ID 来生成唯一文件名。 当使用多线程时，可能发生未被检测到的名称冲突并导致邮箱损坏，除非是对线程进行协调以避免使用这些方法同时操作同一个邮箱。

## **flush**()

​	对 Maildir 邮箱的所有更改都会立即被应用，所以此方法并不会做任何事情。

## **lock**()

## **unlock**()

​	Maildir 邮箱不支持（或要求）锁定操作，所以此方法并不会做任何事情。

## **close**()

`Maildir` 实例不保留任何打开的文件并且下层的邮箱不支持锁定操作。 所以此方法不会做任何事情。

## **get_file**(*key*)

​	根据主机平台的不同，当返回的文件保持打开状态时可能无法修改或移除下层的消息。

​参见
## [maildir man page from Courier](https://www.courier-mta.org/maildir.html)

​	该格式的规格说明。 描述了用于支持文件夹的通用扩展。

## [使用 maildir 格式](https://cr.yp.to/proto/maildir.html)

​	Maildir 发明者对它的说明。 包括已更新的名称创建方案和 "info" 语义的相关细节。



### `mbox` 对象

## *class* mailbox.**mbox**(*path*, *factory=None*, *create=True*)

[`Mailbox`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox" >}}) 的子类，用于 mbox 格式的邮箱。 形参 *factory* 是一个可调用对象，它接受一个文件类消息表示形式（其行为相当于以二进制模式打开）并返回一个自定义的表示形式。 如果 *factory* 为 `None`，则会使用 [`mboxMessage`]({{< ref "/library/netdata/mailbox#mailbox.mboxMessage" >}}) 作为默认的消息表示形式。 如果 *create* 为 `True`，则当邮箱不存在时会创建它。

​	mbox 格式是在 Unix 系统上存储电子邮件的经典格式。 mbox 邮箱中的所有消息都存储在一个单独文件中，每条消息的开头由前五个字符为 "From " 的行来指明。

​	还有一些 mbox 格式的变种对原始格式中发现的缺点做了改进。 为了保证兼容性，`mbox` 只实现了原始格式，或称 *mboxo* 格式。 这意味着当存储消息时，如果存在 *Content-Length* 标头，它将被忽略并且消息体中出现于行开头的任何 "From " 都会被转换为 ">From "，但是当读取消息时 ">From " 则不会被转换为 "From "。

`mbox` 所实现的某些 [`Mailbox`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox" >}}) 方法值得进行特别的说明：

## **get_file**(*key*)

​	在 `mbox` 实例上调用 [`flush()`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox.flush" >}}) 或 [`close()`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox.close" >}}) 之后再使用文件可能产生无法预料的结果或者引发异常。

## **lock**()

## **unlock**()

​	使用三种锁机制 --- dot 锁，以及在受支持的情况下可用的 `flock()` 和 `lockf()` 系统调用。

​参见
## [tin 上的 mbox 指南页面](http://www.tin.org/bin/man.cgi?section=5&topic=mbox)

​	该格式的规格说明，包括有关锁的详情。

## [在 Unix 上配置 Netscape Mail: 为何 Content-Length 格式是不好的](https://www.jwz.org/doc/content-length.html)

​	使用原始 mbox 格式而非其变种的一些理由。

## ["mbox" 是由多个彼此不兼容的邮箱格式构成的家族](https://www.loc.gov/preservation/digital/formats/fdd/fdd000383.shtml)

​	有关 mbox 变种的历史。



### `MH` 对象

## *class* mailbox.**MH**(*path*, *factory=None*, *create=True*)

[`Mailbox`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox" >}}) 的子类，用于 MH 格式的邮箱。 形参 *factory* 是一个可调用对象，它接受一个文件类消息表示形式（其行为相当于以二进制模式打开）并返回一个自定义的表示形式。 如果 *factory* 为 `None`，则会使用 [`MHMessage`]({{< ref "/library/netdata/mailbox#mailbox.MHMessage" >}}) 作为默认的消息表示形式。 如果 *create* 为 `True`，则当邮箱不存在时会创建它。

​	MH 是一种基于目录的邮箱格式，它是针对 MH Message Handling System 电子邮件用户代理而发明的。 在 MH 邮箱的每条消息都放在单独文件中。 MH 邮箱中除了邮件消息还可以包含其他 MH 邮箱 (称为 *文件夹*)。 文件夹可以无限嵌套。 MH 邮箱还支持 *序列*，这是一种命名列表，用来对消息进行逻辑分组而不必将其移入子文件夹。 序列是在每个文件夹中名为 `.mh_sequences` 的文件内定义的。

`MH` 类可以操作 MH 邮箱，但它并不试图模拟 **mh** 的所有行为。 特别地，它并不会修改 `context` 或 `.mh_profile` 文件也不会受其影响，这两个文件是 **mh** 用来存储状态和配置数据的。

`MH` 实例具有 [`Mailbox`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox" >}}) 的所有方法及下列附加方法：

> 在 3.13 版本发生变更: 不包含 `.mh_sequences` 文件的受支持文件夹。

## **list_folders**()

​	返回所有文件夹名称的列表。

## **get_folder**(*folder*)

​	返回表示名称为 *folder* 的文件夹的 `MH` 实例。 如果文件夹不存在则会引发 [`NoSuchMailboxError`]({{< ref "/library/netdata/mailbox#mailbox.NoSuchMailboxError" >}}) 异常。

## **add_folder**(*folder*)

​	创建名称为 *folder* 的文件夹并返回表示它的 `MH` 实例。

## **remove_folder**(*folder*)

​	删除名称为 *folder* 的文件夹。 如果文件夹包含任何消息，则将引发 [`NotEmptyError`]({{< ref "/library/netdata/mailbox#mailbox.NotEmptyError" >}}) 异常且该文件夹将不会被删除。

## **get_sequences**()

​	返回映射到键列表的序列名称字典。 如果不存在任何序列，则返回空字典。

## **set_sequences**(*sequences*)

​	根据由映射到键列表的名称组成的字典 *sequences* 来重新定义邮箱中的序列，该字典与 [`get_sequences()`]({{< ref "/library/netdata/mailbox#mailbox.MH.get_sequences" >}}) 返回值的形式一样。

## **pack**()

​	根据需要重命名邮箱中的消息以消除序号中的空缺。 序列列表中的条目会做相应的修改。

​备注
 

​	已发送的键会因此操作而失效并且不应当被继续使用。

`MH` 所实现的某些 [`Mailbox`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox" >}}) 方法值得进行特别的说明：

## **remove**(*key*)

## `__delitem__`(*key*)

## **discard**(*key*)

​	这些方法会立即删除消息。 通过在名称前加缀一个冒号作为消息删除标记的 MH 惯例不会被使用。

## **lock**()

## **unlock**()

​	使用三种锁机制 --- dot 锁，以及在受支持的情况下可用的 `flock()` 和 `lockf()` 系统调用。 对于 MH 邮箱来说，锁定邮箱意味着锁定 `.mh_sequences` 文件，并且仅在执行任何对它们有影响的操作期间锁定单独消息文件。

## **get_file**(*key*)

​	根据主机平台的不同，当返回的文件保持打开状态时可能无法移除下层的消息。

## **flush**()

​	对 MH 邮箱的所有更改都会立即被应用，所以此方法并不会做任何事情。

## **close**()

`MH` 实例不会保留任何打开的文件，所以此方法等价于 [`unlock()`]({{< ref "/library/netdata/mailbox#mailbox.MH.unlock" >}})。

​参见
## [nmh - Message Handling System](https://www.nongnu.org/nmh/)

**nmh** 的主页，这是原始 **mh** 的更新版本。

## [MH & nmh: Email for Users & Programmers](https://rand-mh.sourceforge.io/book/)

​	使用 GPL 许可证的介绍 **mh** 与 **nmh** 的图书，包含有关该邮箱格式的各种信息。



### `Babyl` 对象

## *class* mailbox.**Babyl**(*path*, *factory=None*, *create=True*)

[`Mailbox`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox" >}}) 的子类，用于 Babyl 格式的邮箱。 形参 *factory* 是一个可调用对象，它接受一个文件类表示形式（其行为相当于以二进制模式打开）并返回一个自定义的表示形式。 如果 *factory* 为 `None`，则会使用 [`BabylMessage`]({{< ref "/library/netdata/mailbox#mailbox.BabylMessage" >}}) 作为默认的消息表示形式。 如果 *create* 为 `True`，则当邮箱不存在时会创建它。

​	Babyl 是 Rmail 电子邮箱用户代理所使用单文件邮箱格式，包括在 Emacs 中。 每条消息的开头由一个包含 Control-Underscore (`'\037'`) 和 Control-L (`'\014'`) 这两个字符的行来指明。 消息的结束由下一条消息的开头来指明，或者当为最后一条消息时则由一个包含 Control-Underscore (`'\037'`) 字符的行来指明。

​	Babyl 邮箱中的消息带有两组标头：原始标头和所谓的可见标头。 可见标头通常为原始标头经过重格式化和删减以更易读的子集。 Babyl 邮箱中的每条消息都附带了一个 *标签* 列表，即记录消息相关额外信息的短字符串，邮箱中所有的用户定义标签列表会存储于 Babyl 的选项部分。

`Babyl` 实例具有 [`Mailbox`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox" >}}) 的所有方法及下列附加方法：

## **get_labels**()

​	返回邮箱中使用的所有用户定义标签名称的列表。

​备注
 

​	邮箱中存在哪些标签会通过检查实际的消息而非查询 Babyl 选项部分的标签列表，但 Babyl 选项部分会在邮箱被修改时更新。

`Babyl` 所实现的某些 [`Mailbox`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox" >}}) 方法值得进行特别的说明：

## **get_file**(*key*)

​	在 Babyl 邮箱中，消息的标头并不是与消息体存储在一起的。 要生成文件类表示形式，标头和消息体会被一起拷贝到一个 [`io.BytesIO`]({{< ref "/library/allos/io#io.BytesIO" >}}) 实例中，它具有与文件相似的 API。 因此，文件型对象实际上独立于下层邮箱，但与字符串表达形式相比并不会更节省内存。

## **lock**()

## **unlock**()

​	使用三种锁机制 --- dot 锁，以及在受支持的情况下可用的 `flock()` 和 `lockf()` 系统调用。

​参见
## [Format of Version 5 Babyl Files](https://quimby.gnus.org/notes/BABYL)

​	Babyl 格式的规格说明。

## [Reading Mail with Rmail](https://www.gnu.org/software/emacs/manual/html_node/emacs/Rmail.html)

​	Rmail 的帮助手册，包含了有关 Babyl 语义的一些信息。



### `MMDF` 对象

## *class* mailbox.**MMDF**(*path*, *factory=None*, *create=True*)

[`Mailbox`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox" >}}) 的子类，用于 MMDF 格式的邮箱。 形参 *factory* 是一个可调用对象，它接受一个文件类消息表示形式（其行为相当于以二进制模式打开）并返回一个自定义的表示形式。 如果 *factory* 为 `None`，则会使用 [`MMDFMessage`]({{< ref "/library/netdata/mailbox#mailbox.MMDFMessage" >}}) 作为默认的消息表示形式。 如果 *create* 为 `True`，则当邮箱不存在时会创建它。

​	MMDF 是一种专用于电子邮件传输代理 Multichannel Memorandum Distribution Facility 的单文件邮箱格式。 每条消息使用与 mbox 消息相同的形式，但其前后各有包含四个 Control-A (`'\001'`) 字符的行。 与 mbox 格式一样，每条消息的开头由一个前五个字符为 "From " 的行来指明，但当存储消息时额外出现的 "From " 不会被转换为 ">From " 因为附加的消息分隔符可防止将这些内容误认为是后续消息的开头。

`MMDF` 所实现的某些 [`Mailbox`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox" >}}) 方法值得进行特别的说明：

## **get_file**(*key*)

​	在 `MMDF` 实例上调用 [`flush()`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox.flush" >}}) 或 [`close()`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox.close" >}}) 之后再使用文件可能产生无法预料的结果或者引发异常。

## **lock**()

## **unlock**()

​	使用三种锁机制 --- dot 锁，以及在受支持的情况下可用的 `flock()` 和 `lockf()` 系统调用。

​参见
## [tin 上的 mmdf 指南页面](http://www.tin.org/bin/man.cgi?section=5&topic=mmdf)

​	MMDF 格式的规格说明，来自新闻阅读器 tin 的文档。

## [MMDF](https://en.wikipedia.org/wiki/MMDF)

​	一篇描述 Multichannel Memorandum Distribution Facility 的维基百科文章。



## `Message` 对象

## *class* mailbox.**Message**(*message=None*)

[`email.message`]({{< ref "/library/netdata/email/email_message#module-email.message" >}}) 模块的 [`Message`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message" >}}) 的子类。 `mailbox.Message` 的子类添加了特定邮箱格式专属的状态和行为。

​	如果省略了 *message*，则新实例会以默认的空状态被创建。 如果 *message* 是一个 [`email.message.Message`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message" >}}) 实例，其内容会被拷贝；此外，如果 *message* 是一个 `Message` 实例，则任何格式专属信息会尽可能地被拷贝。 如果 *message* 是一个字符串、字节串或文件，则它应当包含兼容 [**RFC 2822**](https://datatracker.ietf.org/doc/html/rfc2822.html) 的消息，该消息会被读取和解析。 文件应当以二进制模式打开，但文本模式的文件也会被接受以向下兼容。

​	各个子类所提供的格式专属状态和行为各有不同，但总的来说只有那些不仅限于特定邮箱的特性才会被支持（虽然这些特性可能专属于特定邮箱格式）。 例如，例如，单文件邮箱格式的文件偏移量和基于目录的邮箱格式的文件名都不会被保留，因为它们都仅适用于对应的原始邮箱。 但消息是否已被用户读取或标记为重要等状态则会被保留，因为它们适用于消息本身。

​	不要求使用 `Message` 实例来表示使用 [`Mailbox`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox" >}}) 实例所提取到的消息。 在某些情况下，生成 `Message` 表示形式所需的时间和内存空间可能是不可接受的。 对于此类情况，`Mailbox` 实例还提供了字符串和文件类表示形式，并可在初始化 `Mailbox` 实例时设置自定义的消息工厂函数。



### `MaildirMessage` 对象

## *class* mailbox.**MaildirMessage**(*message=None*)

​	具有 Maildir 专属行为的消息。 形参 *message* 的含义与 [`Message`]({{< ref "/library/netdata/mailbox#mailbox.Message" >}}) 构造器一致。

​	通常，邮件用户代理应用程序会在用户第一次打开并关闭邮箱之后将 `new` 子目录中的所有消息移至 `cur` 子目录，将这些消息记录为旧消息，无论它们是否真的已被阅读。 `cur` 下的每条消息都有一个 "info" 部分被添加到其文件名中以存储有关其状态的信息。 （某些邮件阅读器还会把 "info" 部分也添加到 `new` 下的消息中。） "info" 部分可以采用两种形式之一：它可能包含 "2," 后面跟一个经标准化的旗标列表（例如 "2,FR"）或者它可能包含 "1," 后面跟所谓的实验性信息。 Maildir 消息的标准旗标如下:

| 旗标 | 含意   | 说明                 |
| :--- | :----- | :------------------- |
| D    | 草稿   | 正在撰写中           |
| F    | 已标记 | 已被标记为重要       |
| P    | 已检视 | 转发，重新发送或退回 |
| R    | 已回复 | 回复给               |
| S    | 已查看 | 已阅读               |
| T    | 已删除 | 标记为可被删除       |

`MaildirMessage` 实例提供了下列方法：

## **get_subdir**()

​	返回 "new" (如果消息应当被存储在 `new` 子目录下) 或者 "cur" (如果消息应当被存储在 `cur` 子目录下)。

​备注
 

​	一条消息通常会在其邮箱被访问后从 `new` 移至 `cur`，无论该消息是否已被阅读。 如果 msg.get_flags()`` 中的 `"S" 为 ``True` 则说明消息 `msg` 已被阅读。

## **set_subdir**(*subdir*)

​	设置消息应当被存储到的子目录。 形参 *subdir* 必须为 "new" 或 "cur"。

## **get_flags**()

​	返回一个指明当前所设旗标的字符串。 如果消息符合标准的 Maildir 格式，则结果为零或按字母顺序各自出现一次的 `'D'`, `'F'`, `'P'`, `'R'`, `'S'` 和 `'T'` 的拼接。 如果未设任何旗标或者如果 "info" 包含实验性语义则返回空字符串。

## **set_flags**(*flags*)

​	设置由 *flags* 所指定的旗标并重置所有其它旗标。

## **add_flag**(*flag*)

​	设置由 *flag* 所指明的旗标而不改变其他旗标。 要一次性添加一个以上的旗标，*flag* 可以为包含一个以上字符的字符串。 当前 "info" 会被覆盖，无论它是否只包含实验性信息而非旗标。

## **remove_flag**(*flag*)

​	重置由 *flag* 所指明的旗标而不改变其他旗标。 要一次性移除一个以上的旗标，*flag* 可以为包含一个以上字符的字符串。 如果 "info" 包含实验性信息而非旗标，则当前的 "info" 不会被修改。

## **get_date**()

​	以表示 Unix 纪元秒数的浮点数形式返回消息的发送日期。

## **set_date**(*date*)

​	将消息的发送日期设为 *date*，一个表示 Unix 纪元秒数的浮点数。

## **get_info**()

​	返回一个包含消息的 "info" 的字符串。 这适用于访问和修改实验性的 "info" (即不是由旗标组成的列表)。

## **set_info**(*info*)

​	将 "info" 设为 *info*，这应当是一个字符串。

​	当一个 `MaildirMessage` 实例基于 [`mboxMessage`]({{< ref "/library/netdata/mailbox#mailbox.mboxMessage" >}}) 或 [`MMDFMessage`]({{< ref "/library/netdata/mailbox#mailbox.MMDFMessage" >}}) 实例被创建时，将会忽略 *Status* 和 *X-Status* 标头并进行下列转换：

| 结果状态     | [`mboxMessage`]({{< ref "/library/netdata/mailbox#mailbox.mboxMessage" >}}) 或 [`MMDFMessage`]({{< ref "/library/netdata/mailbox#mailbox.MMDFMessage" >}}) 状态 |
| :----------- | :----------------------------------------------------------- |
| "cur" 子目录 | O 旗标                                                       |
| F 旗标       | F 旗标                                                       |
| R 旗标       | A 旗标                                                       |
| S 旗标       | R 旗标                                                       |
| T 旗标       | D 旗标                                                       |

​	当一个 `MaildirMessage` 实例基于 [`MHMessage`]({{< ref "/library/netdata/mailbox#mailbox.MHMessage" >}}) 实例被创建时，将进行下列转换：

| 结果状态              | [`MHMessage`]({{< ref "/library/netdata/mailbox#mailbox.MHMessage" >}}) 状态 |
| :-------------------- | :----------------------------------------------------------- |
| "cur" 子目录          | "unseen" 序列                                                |
| "cur" 子目录和 S 旗标 | 非 "unseen" 序列                                             |
| F 旗标                | "flagged" 序列                                               |
| R 旗标                | "replied" 序列                                               |

​	当一个 `MaildirMessage` 实例基于 [`BabylMessage`]({{< ref "/library/netdata/mailbox#mailbox.BabylMessage" >}}) 实例被创建时，将进行下列转换：

| 结果状态              | [`BabylMessage`]({{< ref "/library/netdata/mailbox#mailbox.BabylMessage" >}}) 状态 |
| :-------------------- | :----------------------------------------------------------- |
| "cur" 子目录          | "unseen" 标签                                                |
| "cur" 子目录和 S 旗标 | 非 "unseen" 标签                                             |
| P 旗标                | "forwarded" 或 "resent" 标签                                 |
| R 旗标                | "answered" 标签                                              |
| T 旗标                | "deleted" 标签                                               |



### `mboxMessage` 对象

## *class* mailbox.**mboxMessage**(*message=None*)

​	具有 mbox 专属行为的消息。 形参 *message* 的含义与 [`Message`]({{< ref "/library/netdata/mailbox#mailbox.Message" >}}) 构造器一致。

​	mbox 邮箱中的消息会一起存储在单个文件中。 发件人的信封地址和发送时间通常存储在指明每条消息的起始的以 "From " 打头的行中，不过在 mbox 的各种实现之间此数据的确切格式具有相当大的差异。 指明消息状态的各种旗标，例如是否已读或标记为重要等等通常存储在 *Status* 和 *X-Status* 标头中。

​	传统的 mbox 消息旗标如下:

| 旗标 | 含意   | 说明                |
| :--- | :----- | :------------------ |
| R    | 已阅读 | 已阅读              |
| O    | 旧消息 | 之前已经过 MUA 检测 |
| D    | 已删除 | 标记为可被删除      |
| F    | 已标记 | 已被标记为重要      |
| A    | 已回复 | 回复给              |

​	"R" 和 "O" 旗标存储在 *Status* 标头中，而 "D", "F" 和 "A" 旗标存储在 *X-Status* 标头中。 旗标和标头通常会按上述顺序显示。

`mboxMessage` 实例提供了下列方法：

## **get_from**()

​	返回一个表示在 mbox 邮箱中标记消息起始的 "From " 行的字符串。 开头的 "From " 和末尾的换行符会被去除。

## **set_from**(*from_*, *time_=None*)

​	将 "From " 行设为 *from_*，这应当被指定为不带开头的 "From " 或末尾的换行符。 为方便起见，可以指定 *time_* 并将经过适当的格式化再添加到 *from_*。 如果指定了 *time_*，它应当是一个 [`time.struct_time`]({{< ref "/library/allos/time#time.struct_time" >}}) 实例、适合传给 [`time.strftime()`]({{< ref "/library/allos/time#time.strftime" >}}) 的元组或者 `True` (以使用 [`time.gmtime()`]({{< ref "/library/allos/time#time.gmtime" >}}))。

## **get_flags**()

​	返回一个指明当前所设旗标的字符串。 如果消息符合规范格式，则结果为零或各自出现一次的 `'R'`, `'O'`, `'D'`, `'F'` 和 `'A'` 按上述顺序的拼接。

## **set_flags**(*flags*)

​	设置由 *flags* 所指明的旗标并重启所有其他旗标。 形参 *flags* 应当为零或各自出现多次的 `'R'`, `'O'`, `'D'`, `'F'` 和 `'A'` 按任意顺序的拼接。

## **add_flag**(*flag*)

​	设置由 *flag* 所指明的旗标而不改变其他旗标。 要一次性添加一个以上的旗标，*flag* 可以为包含一个以上字符的字符串。

## **remove_flag**(*flag*)

​	重置由 *flag* 所指明的旗标而不改变其他旗标。 要一次性移除一个以上的旗标，*flag* 可以为包含一个以上字符的字符串。

​	当一个 `mboxMessage` 实例基于 [`MaildirMessage`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage" >}}) 实例被创建时，将根据 [`MaildirMessage`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage" >}}) 实例的发送日期生成 "From " 行，并进行下列转换：

| 结果状态 | [`MaildirMessage`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage" >}}) 状态 |
| :------- | :----------------------------------------------------------- |
| R 旗标   | S 旗标                                                       |
| O 旗标   | "cur" 子目录                                                 |
| D 旗标   | T 旗标                                                       |
| F 旗标   | F 旗标                                                       |
| A 旗标   | R 旗标                                                       |

​	当一个 `mboxMessage` 实例基于 [`MHMessage`]({{< ref "/library/netdata/mailbox#mailbox.MHMessage" >}}) 实例被创建时，将进行下列转换：

| 结果状态         | [`MHMessage`]({{< ref "/library/netdata/mailbox#mailbox.MHMessage" >}}) 状态 |
| :--------------- | :----------------------------------------------------------- |
| R 旗标 和 O 旗标 | 非 "unseen" 序列                                             |
| O 旗标           | "unseen" 序列                                                |
| F 旗标           | "flagged" 序列                                               |
| A 旗标           | "replied" 序列                                               |

​	当一个 `mboxMessage` 实例基于 [`BabylMessage`]({{< ref "/library/netdata/mailbox#mailbox.BabylMessage" >}}) 实例被创建时，将进行下列转换：

| 结果状态         | [`BabylMessage`]({{< ref "/library/netdata/mailbox#mailbox.BabylMessage" >}}) 状态 |
| :--------------- | :----------------------------------------------------------- |
| R 旗标 和 O 旗标 | 非 "unseen" 标签                                             |
| O 旗标           | "unseen" 标签                                                |
| D 旗标           | "deleted" 标签                                               |
| A 旗标           | "answered" 标签                                              |

​	当一个 `mboxMessage` 实例基于 [`MMDFMessage`]({{< ref "/library/netdata/mailbox#mailbox.MMDFMessage" >}}) 实例被创建时，"From " 行会被拷贝并直接对应所有旗标：

| 结果状态 | [`MMDFMessage`]({{< ref "/library/netdata/mailbox#mailbox.MMDFMessage" >}}) 状态 |
| :------- | :----------------------------------------------------------- |
| R 旗标   | R 旗标                                                       |
| O 旗标   | O 旗标                                                       |
| D 旗标   | D 旗标                                                       |
| F 旗标   | F 旗标                                                       |
| A 旗标   | A 旗标                                                       |



### `MHMessage` 对象

## *class* mailbox.**MHMessage**(*message=None*)

​	具有 MH 专属行为的消息。 形参 *message* 的含义与 [`Message`]({{< ref "/library/netdata/mailbox#mailbox.Message" >}}) 构造器一致。

​	MH 消息不支持传统意义上的标记或旗标，但它们支持序列，即对任意消息的逻辑分组。 某些邮件阅读程序 (但不包括标准 **mh** 和 **nmh**) 以与其他格式使用旗标类似的方式来使用序列，如下所示:

| 序列   | 说明                          |
| :----- | :---------------------------- |
| unseen | 未阅读，但之前已经过 MUA 检测 |
| 已回复 | 回复给                        |
| 已标记 | 已被标记为重要                |

`MHMessage` 实例提供了下列方法：

## **get_sequences**()

​	返回一个包含此消息的序列的名称的列表。

## **set_sequences**(*sequences*)

​	设置包含此消息的序列的列表。

## **add_sequence**(*sequence*)

​	将 *sequence* 添加到包含此消息的序列的列表。

## **remove_sequence**(*sequence*)

​	将 *sequence* 从包含此消息的序列的列表中移除。

​	当一个 `MHMessage` 实例基于 [`MaildirMessage`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage" >}}) 实例被创建时，将进行下列转换：

| 结果状态       | [`MaildirMessage`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage" >}}) 状态 |
| :------------- | :----------------------------------------------------------- |
| "unseen" 序列  | 非 S 旗标                                                    |
| "replied" 序列 | R 旗标                                                       |
| "flagged" 序列 | F 旗标                                                       |

​	当一个 `MHMessage` 实例基于 [`mboxMessage`]({{< ref "/library/netdata/mailbox#mailbox.mboxMessage" >}}) 或 [`MMDFMessage`]({{< ref "/library/netdata/mailbox#mailbox.MMDFMessage" >}}) 实例被创建时，将会忽略 *Status* 和 *X-Status* 标头并进行下列转换：

| 结果状态       | [`mboxMessage`]({{< ref "/library/netdata/mailbox#mailbox.mboxMessage" >}}) 或 [`MMDFMessage`]({{< ref "/library/netdata/mailbox#mailbox.MMDFMessage" >}}) 状态 |
| :------------- | :----------------------------------------------------------- |
| "unseen" 序列  | 非 R 旗标                                                    |
| "replied" 序列 | A 旗标                                                       |
| "flagged" 序列 | F 旗标                                                       |

​	当一个 `MHMessage` 实例基于 [`BabylMessage`]({{< ref "/library/netdata/mailbox#mailbox.BabylMessage" >}}) 实例被创建时，将进行下列转换：

| 结果状态       | [`BabylMessage`]({{< ref "/library/netdata/mailbox#mailbox.BabylMessage" >}}) 状态 |
| :------------- | :----------------------------------------------------------- |
| "unseen" 序列  | "unseen" 标签                                                |
| "replied" 序列 | "answered" 标签                                              |



### `BabylMessage` 对象

## *class* mailbox.**BabylMessage**(*message=None*)

​	具有 Babyl 专属行为的消息。 形参 *message* 的含义与 [`Message`]({{< ref "/library/netdata/mailbox#mailbox.Message" >}}) 构造器一致。

​	某些消息标签被称为 *属性*，根据惯例被定义为具有特殊的含义。 这些属性如下所示:

| 标签      | 说明                          |
| :-------- | :---------------------------- |
| unseen    | 未阅读，但之前已经过 MUA 检测 |
| deleted   | 标记为可被删除                |
| filed     | 复制到另一个文件或邮箱        |
| answered  | 回复给                        |
| forwarded | 已转发                        |
| edited    | 已被用户修改                  |
| resent    | 已重发                        |

​	默认情况下，Rmail 只显示可见标头。 不过，`BabylMessage` 类会使用原始标头因为它们更为完整。 如果需要可以显式地访问可见标头。

`BabylMessage` 实例提供了下列方法：

## **get_labels**()

​	返回邮件上的标签列表。

## **set_labels**(*labels*)

​	将消息上的标签列表设置为 *labels* 。

## **add_label**(*label*)

​	将 *label* 添加到消息上的标签列表中。

## **remove_label**(*label*)

​	从消息上的标签列表中删除 *label* 。

## **get_visible**()

​	返回一个 [`Message`]({{< ref "/library/netdata/mailbox#mailbox.Message" >}}) 实例，其标头为消息的可见标头而其消息体为空。

## **set_visible**(*visible*)

​	将消息的可见标头设为与 *message* 中的标头一致。 形参 *visible* 应当是一个 [`Message`]({{< ref "/library/netdata/mailbox#mailbox.Message" >}}) 实例，[`email.message.Message`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message" >}}) 实例，字符串或文件型对象（且应当以文本模式打开）。

## **update_visible**()

​	当一个 `BabylMessage` 实例的原始标头被修改时，可见标头不会自动进行对应修改。 此方法将按以下方式更新可见标头：每个具有对应原始标头的可见标头会被设为原始标头的值，每个没有对应原始标头的可见标头会被移除，而任何存在于原始标头但不存在于可见标头中的 *Date*, *From*, *Reply-To*, *To*, *CC* 和 *Subject* 会被添加至可见标头。

​	当一个 `BabylMessage` 实例基于 [`MaildirMessage`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage" >}}) 实例被创建时，将进行下列转换：

| 结果状态         | [`MaildirMessage`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage" >}}) 状态 |
| :--------------- | :----------------------------------------------------------- |
| "unseen" 标签    | 非 S 旗标                                                    |
| "deleted" 标签   | T 旗标                                                       |
| "answered" 标签  | R 旗标                                                       |
| "forwarded" 标签 | P 旗标                                                       |

​	当一个 `BabylMessage` 实例基于 [`mboxMessage`]({{< ref "/library/netdata/mailbox#mailbox.mboxMessage" >}}) 或 [`MMDFMessage`]({{< ref "/library/netdata/mailbox#mailbox.MMDFMessage" >}}) 实例被创建时，将会忽略 *Status* 和 *X-Status* 标头并进行下列转换：

| 结果状态        | [`mboxMessage`]({{< ref "/library/netdata/mailbox#mailbox.mboxMessage" >}}) 或 [`MMDFMessage`]({{< ref "/library/netdata/mailbox#mailbox.MMDFMessage" >}}) 状态 |
| :-------------- | :----------------------------------------------------------- |
| "unseen" 标签   | 非 R 旗标                                                    |
| "deleted" 标签  | D 旗标                                                       |
| "answered" 标签 | A 旗标                                                       |

​	当一个 `BabylMessage` 实例基于 [`MHMessage`]({{< ref "/library/netdata/mailbox#mailbox.MHMessage" >}}) 实例被创建时，将进行下列转换：

| 结果状态        | [`MHMessage`]({{< ref "/library/netdata/mailbox#mailbox.MHMessage" >}}) 状态 |
| :-------------- | :----------------------------------------------------------- |
| "unseen" 标签   | "unseen" 序列                                                |
| "answered" 标签 | "replied" 序列                                               |



### `MMDFMessage` 对象

## *class* mailbox.**MMDFMessage**(*message=None*)

​	具有 MMDF 专属行为的消息。 形参 *message* 的含义与 [`Message`]({{< ref "/library/netdata/mailbox#mailbox.Message" >}}) 构造器一致。

​	与 mbox 邮箱中的消息类似，MMDF 消息会与将发件人的地址和发送日期作为以 "From " 打头的初始行一起存储。 同样地，指明消息状态的旗标通常存储在 *Status* 和 *X-Status* 标头中。

​	传统的 MMDF 消息旗标与 mbox 消息的类似，如下所示:

| 旗标 | 含意   | 说明                |
| :--- | :----- | :------------------ |
| R    | 已阅读 | 已阅读              |
| O    | 旧消息 | 之前已经过 MUA 检测 |
| D    | 已删除 | 标记为可被删除      |
| F    | 已标记 | 已被标记为重要      |
| A    | 已回复 | 回复给              |

​	"R" 和 "O" 旗标存储在 *Status* 标头中，而 "D", "F" 和 "A" 旗标存储在 *X-Status* 标头中。 旗标和标头通常会按上述顺序显示。

`MMDFMessage` 实例提供了下列方法，与 [`mboxMessage`]({{< ref "/library/netdata/mailbox#mailbox.mboxMessage" >}}) 所提供的类似：

## **get_from**()

​	返回一个表示在 mbox 邮箱中标记消息起始的 "From " 行的字符串。 开头的 "From " 和末尾的换行符会被去除。

## **set_from**(*from_*, *time_=None*)

​	将 "From " 行设为 *from_*，这应当被指定为不带开头的 "From " 或末尾的换行符。 为方便起见，可以指定 *time_* 并将经过适当的格式化再添加到 *from_*。 如果指定了 *time_*，它应当是一个 [`time.struct_time`]({{< ref "/library/allos/time#time.struct_time" >}}) 实例、适合传给 [`time.strftime()`]({{< ref "/library/allos/time#time.strftime" >}}) 的元组或者 `True` (以使用 [`time.gmtime()`]({{< ref "/library/allos/time#time.gmtime" >}}))。

## **get_flags**()

​	返回一个指明当前所设旗标的字符串。 如果消息符合规范格式，则结果为零或各自出现一次的 `'R'`, `'O'`, `'D'`, `'F'` 和 `'A'` 按上述顺序的拼接。

## **set_flags**(*flags*)

​	设置由 *flags* 所指明的旗标并重启所有其他旗标。 形参 *flags* 应当为零或各自出现多次的 `'R'`, `'O'`, `'D'`, `'F'` 和 `'A'` 按任意顺序的拼接。

## **add_flag**(*flag*)

​	设置由 *flag* 所指明的旗标而不改变其他旗标。 要一次性添加一个以上的旗标，*flag* 可以为包含一个以上字符的字符串。

## **remove_flag**(*flag*)

​	重置由 *flag* 所指明的旗标而不改变其他旗标。 要一次性移除一个以上的旗标，*flag* 可以为包含一个以上字符的字符串。

​	当一个 `MMDFMessage` 实例基于 [`MaildirMessage`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage" >}}) 实例被创建时，"From " 行会基于 [`MaildirMessage`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage" >}}) 实例的发送日期被生成，并进行下列转换：

| 结果状态 | [`MaildirMessage`]({{< ref "/library/netdata/mailbox#mailbox.MaildirMessage" >}}) 状态 |
| :------- | :----------------------------------------------------------- |
| R 旗标   | S 旗标                                                       |
| O 旗标   | "cur" 子目录                                                 |
| D 旗标   | T 旗标                                                       |
| F 旗标   | F 旗标                                                       |
| A 旗标   | R 旗标                                                       |

​	当一个 `MMDFMessage` 实例基于 [`MHMessage`]({{< ref "/library/netdata/mailbox#mailbox.MHMessage" >}}) 实例被创建时，将进行下列转换：

| 结果状态         | [`MHMessage`]({{< ref "/library/netdata/mailbox#mailbox.MHMessage" >}}) 状态 |
| :--------------- | :----------------------------------------------------------- |
| R 旗标 和 O 旗标 | 非 "unseen" 序列                                             |
| O 旗标           | "unseen" 序列                                                |
| F 旗标           | "flagged" 序列                                               |
| A 旗标           | "replied" 序列                                               |

​	当一个 `MMDFMessage` 实例基于 [`BabylMessage`]({{< ref "/library/netdata/mailbox#mailbox.BabylMessage" >}}) 实例被创建时，将进行下列转换：

| 结果状态         | [`BabylMessage`]({{< ref "/library/netdata/mailbox#mailbox.BabylMessage" >}}) 状态 |
| :--------------- | :----------------------------------------------------------- |
| R 旗标 和 O 旗标 | 非 "unseen" 标签                                             |
| O 旗标           | "unseen" 标签                                                |
| D 旗标           | "deleted" 标签                                               |
| A 旗标           | "answered" 标签                                              |

​	当一个 `MMDFMessage` 实例基于 [`mboxMessage`]({{< ref "/library/netdata/mailbox#mailbox.mboxMessage" >}}) 实例被创建时，"From " 行会被拷贝并直接对应所有旗标：

| 结果状态 | [`mboxMessage`]({{< ref "/library/netdata/mailbox#mailbox.mboxMessage" >}}) 状态 |
| :------- | :----------------------------------------------------------- |
| R 旗标   | R 旗标                                                       |
| O 旗标   | O 旗标                                                       |
| D 旗标   | D 旗标                                                       |
| F 旗标   | F 旗标                                                       |
| A 旗标   | A 旗标                                                       |

## 异常

`mailbox` 模块中定义了下列异常类：

## *exception* mailbox.**Error**

​	所有其他模块专属异常的基类。

## *exception* mailbox.**NoSuchMailboxError**

​	在期望获得一个邮箱但未找到时被引发，例如当使用不存在的路径来实例化一个 [`Mailbox`]({{< ref "/library/netdata/mailbox#mailbox.Mailbox" >}}) 子类时 (且将 *create* 形参设为 `False`)，或是当打开一个不存在的路径时。

## *exception* mailbox.**NotEmptyError**

​	在期望一个邮箱为空但不为空时被引发，例如当删除一个包含消息的文件夹时。

## *exception* mailbox.**ExternalClashError**

​	在某些邮箱相关条件超出了程序控制范围导致其无法继续运行时被引发，例如当要获取的锁已被另一个程序获取时，或是当要生成的唯一性文件名已存在时等等。

## *exception* mailbox.**FormatError**

​	在某个文件中的数据无法被解析时被引发，例如当一个 [`MH`]({{< ref "/library/netdata/mailbox#mailbox.MH" >}}) 实例尝试读取已损坏的 `.mh_sequences` 文件时。



## 例子

​	一个打印指定邮箱中所有消息的主题的简单示例:

```
import mailbox
for message in mailbox.mbox('~/mbox'):
    subject = message['subject']       # 可能为 None。
    if subject and 'python' in subject.lower():
        print(subject)
```

​	要将所有邮件从 Babyl 邮箱拷贝到 MH 邮箱，请转换所有可转换的格式专属信息:

```
import mailbox
destination = mailbox.MH('~/Mail')
destination.lock()
for message in mailbox.Babyl('~/RMAIL'):
    destination.add(mailbox.MHMessage(message))
destination.flush()
destination.unlock()
```

​	这个示例将来自多个邮件列表的邮件分类放入不同的邮箱，小心避免由于其他程序的并发修改导致的邮件损坏，由于程序中断导致的邮件丢失，或是由于邮箱中消息格式错误导致的意外终止:

```
import mailbox
import email.errors

list_names = ('python-list', 'python-dev', 'python-bugs')

boxes = {name: mailbox.mbox('~/email/%s' % name) for name in list_names}
inbox = mailbox.Maildir('~/Maildir', factory=None)

for key in inbox.iterkeys():
    try:
        message = inbox[key]
    except email.errors.MessageParseError:
        continue                # 消息格式错误。 不做处理。

    for name in list_names:
        list_id = message['list-id']
        if list_id and name in list_id:
            # 获取要使用的邮箱
            box = boxes[name]

            # 在移除原始消息之前将副本写入磁盘。
            # 如果发生崩溃，你可能重复写入消息，
            # 但那也比完全丢失这条消息要好。
            box.lock()
            box.add(message)
            box.flush()
            box.unlock()

            # 移除原始消息
            inbox.lock()
            inbox.discard(key)
            inbox.flush()
            inbox.unlock()
            break               # 已发现目标，停止查找。

for box in boxes.itervalues():
    box.close()
```
