+++
title = "imaplib --- IMAP4 协议客户端"
date = 2024-11-15T20:28:46+08:00
weight = 120
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/imaplib.html](https://docs.python.org/zh-cn/3.13/library/imaplib.html)
>
> 收录该文档的时间：`2024-11-15T20:28:46+08:00`

# `imaplib` --- IMAP4 协议客户端

**源代码：** [Lib/imaplib.py](https://github.com/python/cpython/tree/3.13/Lib/imaplib.py)

------

​	本模块定义了三个类： [`IMAP4`]({{< ref "/library/internet/imaplib#imaplib.IMAP4" >}}) 、 [`IMAP4_SSL`]({{< ref "/library/internet/imaplib#imaplib.IMAP4_SSL" >}}) 和 [`IMAP4_stream`]({{< ref "/library/internet/imaplib#imaplib.IMAP4_stream" >}}) 。这三个类封装了与IMAP4服务器的连接并实现了 [**RFC 2060**](https://datatracker.ietf.org/doc/html/rfc2060.html) 当中定义的大多数IMAP4rev1客户端协议。其与IMAP4（ [**RFC 1730**](https://datatracker.ietf.org/doc/html/rfc1730.html) ）服务器后向兼容，但是 `STATUS` 指令在IMAP4中不支持。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

​	此模块在 WebAssembly 平台上无效或不可用。 请参阅 [WebAssembly 平台]({{< ref "/library/intro#wasm-availability" >}}) 了解详情。

[`imaplib`]({{< ref "/library/internet/imaplib#module-imaplib" >}}) 模块提供了三个类，其中 [`IMAP4`]({{< ref "/library/internet/imaplib#imaplib.IMAP4" >}}) 是基类：

## *class* imaplib.**IMAP4**(*host=''*, *port=IMAP4_PORT*, *timeout=None*)

​	这个类实现了实际的 IMAP4 协议。 当其实例被初始化时会创建连接并确定协议版本 (IMAP4 或 IMAP4rev1)。 如果未指明 *host*，则会使用 `''` (本地主机)。 如果省略 *port*，则会使用标准的 IMAP4 端口 (143)。 可选的 *timeout* 形参指定连接尝试的超时秒数。 如果未指定超时或为 `None`，则会使用全局默认的套接字超时。

[`IMAP4`]({{< ref "/library/internet/imaplib#imaplib.IMAP4" >}}) 类支持 [`with`]({{< ref "/reference/compound_stmts#with" >}}) 语句。 当这样使用时，IMAP4 `LOGOUT` 命令会在 `with` 语句退出时自动发出。 例如:



``` python
>>> from imaplib import IMAP4
>>> with IMAP4("domain.org") as M:
...     M.noop()
...
('OK', [b'Nothing Accomplished. d25if65hy903weo.87'])
```

> 在 3.5 版本发生变更: 添加了对 [`with`]({{< ref "/reference/compound_stmts#with" >}}) 语句的支持。

> 在 3.9 版本发生变更: 添加了可选的 *timeout* 形参。

​	有三个异常被定义为 [`IMAP4`]({{< ref "/library/internet/imaplib#imaplib.IMAP4" >}}) 类的属性:

## *exception* IMAP4.**error**

​	任何错误都将引发该异常。 异常的原因会以字符串的形式传递给构造器。

## *exception* IMAP4.**abort**

​	IMAP4 服务器错误会导致引发该异常。 这是 [`IMAP4.error`]({{< ref "/library/internet/imaplib#imaplib.IMAP4.error" >}}) 的子类。 请注意关闭此实例并实例化一个新实例通常将会允许从该异常中恢复。

## *exception* IMAP4.**readonly**

​	当一个可写邮箱的状态被服务器修改时会引发此异常。 此异常是 [`IMAP4.error`]({{< ref "/library/internet/imaplib#imaplib.IMAP4.error" >}}) 的子类。 某个其他客户端现在会具有写入权限，将需要重新打开该邮箱以重新获得写入权限。

​	另外还有一个针对安全连接的子类:

## *class* imaplib.**IMAP4_SSL**(*host=''*, *port=IMAP4_SSL_PORT*, ***, *ssl_context=None*, *timeout=None*)

​	这是一个派生自 [`IMAP4`]({{< ref "/library/internet/imaplib#imaplib.IMAP4" >}}) 的子类，它使用经 SSL 加密的套接字进行连接 (为了使用这个类你需要编译时附带 SSL 支持的 socket 模块)。 如果未指定 *host*，则会使用 `''` (本地主机)。 如果省略了 *port*，则会使用标准的 IMAP4-over-SSL 端口 (993)。 *ssl_context* 是一个 [`ssl.SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 对象，它允许将 SSL 配置选项、证书和私钥打包放入一个单独的 (可以长久存在的) 结构体中。 请阅读 [安全考量]({{< ref "/library/ipc/ssl#ssl-security" >}}) 以获取最佳实践。

​	可选的 *timeout* 形参指明连接尝试的超时秒数。 如果超时值未给出或为 `None`，则会使用全局默认的套接字超时设置。

> 在 3.3 版本发生变更: 增加了 *ssl_context* 形参。

> 在 3.4 版本发生变更: 该类现在支持使用 [`ssl.SSLContext.check_hostname`]({{< ref "/library/ipc/ssl#ssl.SSLContext.check_hostname" >}}) 和 *服务器名称提示* (参见 [`ssl.HAS_SNI`]({{< ref "/library/ipc/ssl#ssl.HAS_SNI" >}})) 进行主机名检测。

> 在 3.9 版本发生变更: 添加了可选的 *timeout* 形参。

> 在 3.12 版本发生变更: 已弃用的 *keyfile* 和 *certfile* 形参已被移除。

​	第二个子类允许由子进程所创建的连接:

## *class* imaplib.**IMAP4_stream**(*command*)

​	这是一个派生自 [`IMAP4`]({{< ref "/library/internet/imaplib#imaplib.IMAP4" >}}) 的子类，它可以连接 `stdin/stdout` 文件描述符，此种文件是通过向 `subprocess.Popen()` 传入 *command* 来创建的。

​	定义了下列工具函数:

## imaplib.**Internaldate2tuple**(*datestr*)

​	解析一个 IMAP4 `INTERNALDATE` 字符串并返回对应的本地时间。 返回值是一个 [`time.struct_time`]({{< ref "/library/allos/time#time.struct_time" >}}) 元组或者如果字符串格式错误则为 `None`。

## imaplib.**Int2AP**(*num*)

​	将一个整数转换为使用字符集 [`A` .. `P`] 的字节串表示形式。

## imaplib.**ParseFlags**(*flagstr*)

​	将一个 IMAP4 `FLAGS` 响应转换为包含单独旗标的元组。

## imaplib.**Time2Internaldate**(*date_time*)

​	将 *date_time* 转换为 IMAP4 `INTERNALDATE` 表示形式。 返回值是以下形式的字符串: `"DD-Mmm-YYYY HH:MM:SS +HHMM"` (包括双引号)。 *date_time* 参数可以是一个代表距离纪元起始的秒数 (如 [`time.time()`]({{< ref "/library/allos/time#time.time" >}}) 的返回值) 的数字 (整数或浮点数)，一个代表本地时间的 9 元组，一个 [`time.struct_time`]({{< ref "/library/allos/time#time.struct_time" >}}) 实例 (如 [`time.localtime()`]({{< ref "/library/allos/time#time.localtime" >}}) 的返回值)，一个感知型的 [`datetime.datetime`]({{< ref "/library/datatypes/datetime#datetime.datetime" >}}) 实例，或一个双引号字符串。 在最后一种情况下，它会被假定已经具有正确的格式。

​	请注意 IMAP4 消息编号会随邮箱的改变而改变；特别是在使用 `EXPUNGE` 命令执行删除后剩余的消息会被重新编号。 因此高度建议通过 UID 命令来改用 UID。

​	模块的最后有一段测试，其中包含的用法示例更加广泛。

​参见
 

​	描述该协议的文档，实现该协议的服务器源代码，由华盛顿大学 IMAP 信息中心提供 (**源代码**) https://github.com/uw-imap/imap (**不再维护**)。



## IMAP4 对象

​	所有 IMAP4rev1 命令都是以相同名称的方法来表示的，可以为大写或小写形式。

​	命令的所有参数都会被转换为字符串，只有 `AUTHENTICATE` 例外，而 `APPEND` 的最后一个参数会被作为 IMAP4 字面值传入。 如有必要 (字符串包含 IMAP4 协议中的敏感字符并且未加圆括号或双引号) 每个字符串都会被转码。 但是，`LOGIN` 命令的 *password* 参数总是会被转码。 如果你想让某个参数字符串免于被转码 (例如: `STORE` 的 *flags* 参数) 则要为该字符串加上圆括号 (例如: `r'(\Deleted)'`)。

​	每条命令均返回一个元组: `(type, [data, ...])` 其中 *type* 通常为 `'OK'` 或 `'NO'`，而 *data* 为来自命令响应的文本，或为来自命令的规定结果。 每个 *data* 均为 `bytes` 或者元组。 如果为元组，则其第一部分是响应的标头，而第二部分将包含数据 (例如: 'literal' 值)。

​	以下命令的 *message_set* 选项为指定要操作的一条或多条消息的字符串。 它可以是一个简单的消息编号 (`'1'`)，一段消息编号区间 (`'2:4'`)，或者一组以逗号分隔的非连续区间 (`'1:3,6:9'`)。 区间可以包含一个星号来表示无限的上界 (`'3:*'`)。

[`IMAP4`]({{< ref "/library/internet/imaplib#imaplib.IMAP4" >}}) 实例具有下列方法:

## IMAP4.**append**(*mailbox*, *flags*, *date_time*, *message*)

​	将 *message* 添加到指定的邮箱。

## IMAP4.**authenticate**(*mechanism*, *authobject*)

​	认证命令 --- 要求对响应进行处理。

*mechanism* 指明要使用哪种认证机制 —— 它应当在实例变量 `capabilities` 中以 `AUTH=mechanism` 的形式出现。

*authobject* 必须是一个可调用对象:

```
data = authobject(response)
```

​	它将被调用以便处理服务器连续响应；传给它的 *response* 参数将为 `bytes` 类型。 它应当返回 base64 编码的 `bytes` *数据* 并发送给服务器。 或者在客户端中止响应时返回 `None` 并应改为发送 `*`。

> 在 3.5 版本发生变更: 字符串形式的用户名和密码现在会被执行 `utf-8` 编码而不限于 ASCII 字符。

## IMAP4.**check**()

​	为服务器上的邮箱设置检查点。

## IMAP4.**close**()

​	关闭当前选定的邮箱。 已删除的消息会从可写邮箱中被移除。 在 `LOGOUT` 之前建议执行此命令。

## IMAP4.**copy**(*message_set*, *new_mailbox*)

​	将 *message_set* 消息拷贝到 *new_mailbox* 的末尾。

## IMAP4.**create**(*mailbox*)

​	新建名为 *mailbox* 新邮箱。

## IMAP4.**delete**(*mailbox*)

​	删除名为 *mailbox* 的旧邮箱。

## IMAP4.**deleteacl**(*mailbox*, *who*)

​	删除邮箱上某人的 ACL (移除任何权限)。

## IMAP4.**enable**(*capability*)

​	启用 *capability* (参见 [**RFC 5161**](https://datatracker.ietf.org/doc/html/rfc5161.html))。 大多数功能都不需要被启用。 目前只有 `UTF8=ACCEPT` 功能受到支持 (参见 [**RFC 6855**](https://datatracker.ietf.org/doc/html/rfc6855.html))。

> Added in version 3.5:* [`enable()`]({{< ref "/library/internet/imaplib#imaplib.IMAP4.enable" >}}) 方法本身，以及 [**RFC 6855*](https://datatracker.ietf.org/doc/html/rfc6855.html) 支持。

## IMAP4.**expunge**()

​	从选定的邮箱中永久移除被删除的条目。 为每条被删除的消息各生成一个 `EXPUNGE` 响应。 返回包含按接收时间排序的 `EXPUNGE` 消息编号的列表。

## IMAP4.**fetch**(*message_set*, *message_parts*)

​	获取消息（的各个部分）。 *message_parts* 应为加圆标号的消息部分名称字符串，例如: `"(UID BODY[TEXT])"`。 返回的数据是由消息部分封包和数据组成的元组。

## IMAP4.**getacl**(*mailbox*)

​	获取 *mailbox* 的 `ACL`。 此方法是非标准的，但是被 `Cyrus` 服务器所支持。

## IMAP4.**getannotation**(*mailbox*, *entry*, *attribute*)

​	提取 *mailbox* 的特定 `ANNOTATION`。 此方法是非标准的，但是被 `Cyrus` 服务器所支持。

## IMAP4.**getquota**(*root*)

​	获取 `quota` *root* 的资源使用和限制。 此方法是 rfc2087 定义的 IMAP4 QUOTA 扩展的组成部分。

## IMAP4.**getquotaroot**(*mailbox*)

​	获取指定 *mailbox* 的 `quota` `roots` 列表。 此方法是 rfc2087 定义的 IMAP4 QUOTA 扩展的组成部分。

## IMAP4.**list**([*directory*[, *pattern*]])

​	列出 *directory* 中与 *pattern* 相匹配的邮箱名称。 *directory* 默认为最高层级的电邮文件夹，而 *pattern* 默认为匹配任何文本。 返回的数据包含 `LIST` 响应列表。

## IMAP4.**login**(*user*, *password*)

​	使用纯文本密码标识客户。 *password* 将被转码。

## IMAP4.**login_cram_md5**(*user*, *password*)

​	在标识用户以保护密码时强制使用 `CRAM-MD5` 认证。 将只在服务器 `CAPABILITY` 响应包含 `AUTH=CRAM-MD5` 阶段时才有效。

## IMAP4.**logout**()

​	关闭对服务器的连接。 返回服务器 `BYE` 响应。

> 在 3.8 版本发生变更: 此方法不会再忽略静默的任意异常。

## IMAP4.**lsub**(*directory='""'*, *pattern='\*'*)

​	列出 directory 中抽取的与 pattern 相匹配的邮箱。 *directory* 默认为最高层级目录而 *pattern* 默认为匹配任何邮箱。 返回的数据为消息部分封包和数据的元组。

## IMAP4.**myrights**(*mailbox*)

​	显示某个邮箱的本人 ACL (即本人在邮箱中的权限)。

## IMAP4.**namespace**()

​	返回 [**RFC 2342**](https://datatracker.ietf.org/doc/html/rfc2342.html) 中定义的 IMAP 命名空间。

## IMAP4.**noop**()

​	将 `NOOP` 发送给服务器。

## IMAP4.**open**(*host*, *port*, *timeout=None*)

​	打开连接到 *host* 上 *port* 的套接字。 可选的 *timeout* 形参指定连接尝试的超时秒数。 如果超时值未给出或为 `None`，则会使用全局默认的套接字超时。 另外请注意如果 *timeout* 形参被设为零，它将引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 以拒绝创建非阻塞套接字。 此方法会由 [`IMAP4`]({{< ref "/library/internet/imaplib#imaplib.IMAP4" >}}) 构造器隐式地调用。 此方法所建立的连接对象将在 [`IMAP4.read()`]({{< ref "/library/internet/imaplib#imaplib.IMAP4.read" >}}), [`IMAP4.readline()`]({{< ref "/library/internet/imaplib#imaplib.IMAP4.readline" >}}), [`IMAP4.send()`]({{< ref "/library/internet/imaplib#imaplib.IMAP4.send" >}}) 和 [`IMAP4.shutdown()`]({{< ref "/library/internet/imaplib#imaplib.IMAP4.shutdown" >}}) 等方法中被使用。 你可以重写此方法。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `imaplib.open` 并附带参数 `self`, `host`, `port`。

> 在 3.9 版本发生变更: 加入 *timeout* 参数。

## IMAP4.**partial**(*message_num*, *message_part*, *start*, *length*)

​	获取消息被截断的部分。 返回的数据是由消息部分封包和数据组成的元组。

## IMAP4.**proxyauth**(*user*)

​	作为 *user* 进行认证。 允许经权限的管理员通过代理进入任意用户的邮箱。

## IMAP4.**read**(*size*)

​	从远程服务器读取 *size* 字节。 你可以重写此方法。

## IMAP4.**readline**()

​	从远程服务器读取一行。 你可以重写此方法。

## IMAP4.**recent**()

​	提示服务器进行更新。 如果没有新消息则返回的数据为 `None`，否则为 `RECENT` 响应的值。

## IMAP4.**rename**(*oldmailbox*, *newmailbox*)

​	将名为 *oldmailbox* 的邮箱重命名为 *newmailbox*。

## IMAP4.**response**(*code*)

​	如果收到响应 *code* 则返回其数据，否则返回 `None`。 返回给定的代码，而不是普通的类型。

## IMAP4.**search**(*charset*, *criterion*[, *...*])

​	在邮箱中搜索匹配的消息。 *charset* 可以为 `None`，在这种情况下在发给服务器的请求中将不指定 `CHARSET`。 IMAP 协议要求至少指定一个标准；当服务器返回错误时将会引发异常。 *charset* 为 `None` 对应使用 [`enable()`]({{< ref "/library/internet/imaplib#imaplib.IMAP4.enable" >}}) 命令启用了 `UTF8=ACCEPT` 功能的情况。

​	示例:

```
# M 是一个已连接的 IMAP4 实例...
typ, msgnums = M.search(None, 'FROM', '"LDJ"')

# 或者：
typ, msgnums = M.search(None, '(FROM "LDJ")')
```

## IMAP4.**select**(*mailbox='INBOX'*, *readonly=False*)

​	选择一个邮箱。 返回的数据是 *mailbox* 中消息的数量 (`EXISTS` 响应)。 默认的 *mailbox* 为 `'INBOX'`。 如果设置了 *readonly* 旗标，则不允许修改该邮箱。

## IMAP4.**send**(*data*)

​	将 `data` 发送给远程服务器。 你可以重写此方法。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `imaplib.send` 并附带参数 `self`, `data`。

## IMAP4.**setacl**(*mailbox*, *who*, *what*)

​	发送 *mailbox* 的 `ACL`。 此方法是非标准的，但是被 `Cyrus` 服务器所支持。

## IMAP4.**setannotation**(*mailbox*, *entry*, *attribute*[, *...*])

​	设置 *mailbox* 的 `ANNOTATION`。 此方法是非标准的，但是被 `Cyrus` 服务器所支持。

## IMAP4.**setquota**(*root*, *limits*)

​	设置 `quota` *root* 的资源限制为 *limits*。 此方法是 rfc2087 定义的 IMAP4 QUOTA 扩展的组成部分。

## IMAP4.**shutdown**()

​	关闭在 `open` 中建立的连接。 此方法会由 [`IMAP4.logout()`]({{< ref "/library/internet/imaplib#imaplib.IMAP4.logout" >}}) 隐式地调用。 你可以重写此方法。

## IMAP4.**socket**()

​	返回用于连接服务器的套接字实例。

## IMAP4.**sort**(*sort_criteria*, *charset*, *search_criterion*[, *...*])

`sort` 命令是 `search` 的变化形式，带有结果排序语句。 返回的数据包含以空格分隔的匹配消息编号列表。

​	sort 命令在 *search_criterion* 参数之前还有两个参数；一个带圆括号的 *sort_criteria* 列表，和搜索的 *charset*。 请注意不同于 `search`，搜索的 *charset* 参数是强制性的。 还有一个 `uid sort` 命令与 `sort` 对应，如同 `uid search` 与 `search` 对应一样。 `sort` 命令首先在邮箱中搜索匹配给定搜索条件的消息，使用 charset 参数来解读搜索条件中的字符串。 然后它将返回所匹配消息的编号。

​	这是一个 `IMAP4rev1` 扩展命令。

## IMAP4.**starttls**(*ssl_context=None*)

​	发送一个 `STARTTLS` 命令。 *ssl_context* 参数是可选的并且应为一个 [`ssl.SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 对象。 这将在 IMAP 连接上启用加密。 请阅读 [安全考量]({{< ref "/library/ipc/ssl#ssl-security" >}}) 来了解最佳实践。

> Added in version 3.2.
>

> 在 3.4 版本发生变更: 该方法现在支持使用 [`ssl.SSLContext.check_hostname`]({{< ref "/library/ipc/ssl#ssl.SSLContext.check_hostname" >}}) 和 *服务器名称提示* (参见 [`ssl.HAS_SNI`]({{< ref "/library/ipc/ssl#ssl.HAS_SNI" >}})) 进行主机名称检测。

## IMAP4.**status**(*mailbox*, *names*)

​	针对 *mailbox* 请求指定的状态条件。

## IMAP4.**store**(*message_set*, *command*, *flag_list*)

​	改变邮箱中消息的旗标处理。 *command* 由 [**RFC 2060**](https://datatracker.ietf.org/doc/html/rfc2060.html) 的 6.4.6 小节指明，应为 "FLAGS", "+FLAGS" 或 "-FLAGS" 之一，并可选择附带 ".SILENT" 后缀。

​	例如，要在所有消息上设置删除旗标:

```
typ, data = M.search(None, 'ALL')
for num in data[0].split():
   M.store(num, '+FLAGS', '\\Deleted')
M.expunge()
```

​备注
 

​	创建包含 ']' (例如: "[test]") 的旗标并违反 [**RFC 3501**](https://datatracker.ietf.org/doc/html/rfc3501.html) (即 IMAP 协议)。 不过，imaplib 在历史上曾经允许创建这样的标签，并且流行的 IMAP 服务器，如 Gmail，都接受并会产生这样的旗标。 有些非 Python 程序也会创建这样的旗标。 虽然它违反 RFC 并且 IMAP 客户端和服务器应当严格规范，但是 imaplib 出于向下兼容的考虑仍然继承允许创建这样的标签，并且像在 Python 3.6 中一样，会在当其被服务器所发送时处理它们，因为这能提升在真实世界中的兼容性。

## IMAP4.**subscribe**(*mailbox*)

​	订阅新邮箱。

## IMAP4.**thread**(*threading_algorithm*, *charset*, *search_criterion*[, *...*])

`thread` 命令是 `search` 的变化形式，带有针对结果的消息串句法。 返回的数据包含以空格分隔的消息串成员列表。

​	消息串成员由零个或多个消息编号组成，以空格分隔，标示了连续的上下级关系。

​	Thread 命令在 *search_criterion* 参数之前还有两个参数；一个 *threading_algorithm*，以及搜索使用的 *charset*。 请注意不同于 `search`，搜索使用的 *charset* 参数是强制性的。 还有一个 `uid thread` 命令与 `thread` 对应，如同 `uid search` 与 `search` 对应一样。 `thread` 命令首先在邮箱中搜索匹配给定搜索条件的消息，使用 *charset* 参数来解读搜索条件中的字符串。 然后它将按照指定的消息串算法返回所匹配的消息串。

​	这是一个 `IMAP4rev1` 扩展命令。

## IMAP4.**uid**(*command*, *arg*[, *...*])

​	执行 command arg 并附带用 UID 所标识的消息，而不是用消息编号。 返回与命令对应的响应。 必须至少提供一个参数；如果不提供任何参数，服务器将返回错误并引发异常。

## IMAP4.**unsubscribe**(*mailbox*)

​	取消订阅原有邮箱。

## IMAP4.**unselect**()

[`imaplib.IMAP4.unselect()`]({{< ref "/library/internet/imaplib#imaplib.IMAP4.unselect" >}}) 会释放关联到选定邮箱的服务器资源并将服务器返回到已认证状态。 此命令会执行与 [`imaplib.IMAP4.close()`]({{< ref "/library/internet/imaplib#imaplib.IMAP4.close" >}}) 相同的动作，区别在于它不会从当前选定邮箱中永久性地移除消息。

> Added in version 3.9.
>

## IMAP4.**xatom**(*name*[, *...*])

​	允许服务器在 `CAPABILITY` 响应中通知简单的扩展命令。

​	在 [`IMAP4`]({{< ref "/library/internet/imaplib#imaplib.IMAP4" >}}) 的实例上定义了下列属性:

## IMAP4.**PROTOCOL_VERSION**

​	在服务器的 `CAPABILITY` 响应中最新的受支持协议。

## IMAP4.**debug**

​	控制调试输出的整数值。 初始值会从模块变量 `Debug` 中获取。 大于三的值表示将追踪每一条命令。

## IMAP4.**utf8_enabled**

​	通常为 `False` 的布尔值，但也可以被设为 `True`，如果成功地为 `UTF8=ACCEPT` 功能发送了 [`enable()`]({{< ref "/library/internet/imaplib#imaplib.IMAP4.enable" >}}) 命令的话。

> Added in version 3.5.
>



## IMAP4 示例

​	以下是一个最短示例（不带错误检查），该示例将打开邮箱，检索并打印所有消息:

```
import getpass, imaplib

M = imaplib.IMAP4(host='example.org')
M.login(getpass.getuser(), getpass.getpass())
M.select()
typ, data = M.search(None, 'ALL')
for num in data[0].split():
    typ, data = M.fetch(num, '(RFC822)')
    print('Message %s\n%s\n' % (num, data[0][1]))
M.close()
M.logout()
```
