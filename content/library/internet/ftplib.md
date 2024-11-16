+++
title = "ftplib --- FTP 协议客户端"
date = 2024-11-15T20:28:46+08:00
weight = 100
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/ftplib.html](https://docs.python.org/zh-cn/3.13/library/ftplib.html)
>
> 收录该文档的时间：`2024-11-15T20:28:46+08:00`

# `ftplib` --- FTP 协议客户端

**源代码：** [Lib/ftplib.py](https://github.com/python/cpython/tree/3.13/Lib/ftplib.py)

------

​	本模块定义了 [`FTP`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP) 类和一些相关项目。 [`FTP`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP) 类实现了 FTP 协议的客户端。 你可以用这个类来编写执行各种自动化 FTP 任务的 Python 程序，例如镜像其他 FTP 服务器等。 它还被 [`urllib.request`](https://docs.python.org/zh-cn/3.13/library/urllib.request.html#module-urllib.request) 模块用来处理使用 FTP 的 URL。 有关 FTP (文件传输协议) 的更多信息，请参阅 [**RFC 959**](https://datatracker.ietf.org/doc/html/rfc959.html)。

​	默认编码为 UTF-8，遵循 [**RFC 2640**](https://datatracker.ietf.org/doc/html/rfc2640.html)。

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): not WASI.

​	此模块在 WebAssembly 平台上无效或不可用。 请参阅 [WebAssembly 平台](https://docs.python.org/zh-cn/3.13/library/intro.html#wasm-availability) 了解详情。

​	以下是使用 [`ftplib`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#module-ftplib) 模块的会话示例:



``` python
>>> from ftplib import FTP
>>> ftp = FTP('ftp.us.debian.org')  # 连接到主机，默认端口
>>> ftp.login()                     # 用户 anonymous，密码 anonymous@
'230 Login successful.'
>>> ftp.cwd('debian')               # 更改为 "debian" 目录
'250 Directory successfully changed.'
>>> ftp.retrlines('LIST')           # 列出目录内容
-rw-rw-r--    1 1176     1176         1063 Jun 15 10:18 README
...
drwxr-sr-x    5 1176     1176         4096 Dec 19  2000 pool
drwxr-sr-x    4 1176     1176         4096 Nov 17  2008 project
drwxr-xr-x    3 1176     1176         4096 Oct 10  2012 tools
'226 Directory send OK.'
>>> with open('README', 'wb') as fp:
>>>     ftp.retrbinary('RETR README', fp.write)
'226 Transfer complete.'
>>> ftp.quit()
'221 Goodbye.'
```



## 参考



### FTP 对象

## *class* ftplib.**FTP**(*host=''*, *user=''*, *passwd=''*, *acct=''*, *timeout=None*, *source_address=None*, ***, *encoding='utf-8'*)

​	返回一个 [`FTP`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP) 类的新实例。

## 参数:

- **host** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)) -- 要连接的主机名。 如果给出，则将由构造器隐式地调用 `connect(host)`。
- **user** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)) -- 如果给出 The username to log in with (default: `'anonymous'`).，则将由构造器隐式地调用 `login(host, passwd, acct)`。
- **passwd** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)) -- The password to use when logging in. If not given, and if *passwd* is the empty string or `"-"`, a password will be automatically generated.
- **acct** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)) -- Account information to be used for the `ACCT` FTP command. Few systems implement this. See [RFC-959](https://datatracker.ietf.org/doc/html/rfc959.html) for more details.
- **timeout** ([*float*](https://docs.python.org/zh-cn/3.13/library/functions.html#float) *|* *None*) -- 用于阻塞操作如 [`connect()`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP.connect) 的以秒数表示的超时值（默认：全局默认超时设置值）。
- **source_address** ([*tuple*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#tuple) *|* *None*) -- A 2-tuple `(host, port)` for the socket to bind to as its source address before connecting.
- **encoding** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)) -- The encoding for directories and filenames (default: `'utf-8'`).

[`FTP`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP) 类支持 [`with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#with) 语句，例如：



``` python
>>> from ftplib import FTP
>>> with FTP("ftp1.at.proftpd.org") as ftp:
...     ftp.login()
...     ftp.dir()
... 
'230 Anonymous login ok, restrictions apply.'
dr-xr-xr-x   9 ftp      ftp           154 May  6 10:43 .
dr-xr-xr-x   9 ftp      ftp           154 May  6 10:43 ..
dr-xr-xr-x   5 ftp      ftp          4096 May  6 10:43 CentOS
dr-xr-xr-x   3 ftp      ftp            18 Jul 10  2008 Fedora
>>>
```

*在 3.2 版本发生变更:* 添加了对 [`with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#with) 语句的支持。

*在 3.3 版本发生变更:* 添加了 *source_address* 参数。

*在 3.9 版本发生变更:* 如果 *timeout* 参数设置为 0，创建非阻塞套接字时，它将引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError) 来阻止该操作。添加了 *encoding* 参数，且为了遵循 [**RFC 2640**](https://datatracker.ietf.org/doc/html/rfc2640.html)，该参数默认值从 Latin-1 改为了 UTF-8。

​	某些 `FTP` 方法有两种形式：一种用于处理文本文件而另一种用于二进制文件。 这些方法的名称与所用的命令相对应，文本版之后跟 `lines` 而二进制版之后跟 `binary`。

[`FTP`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP) 实例具有下列方法:

## **set_debuglevel**(*level*)

​	将实例的调试级别设为一个 [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int) 值。 这将控制所打印的调试输出数据量。 调试级别包括：

- `0` (默认): 无调试输出。
- `1`: 产生中等的调试输出数据量，通常为每个请求一行。
- `2` 或更高: 产生最大的调试输出数据量，记录在控制连接中发送和接收的每一行。

## **connect**(*host=''*, *port=0*, *timeout=None*, *source_address=None*)

​	连接到给定的主机和端口。 此函数应当只为每个实例调用一次；如果在创建 [`FTP`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP) 实例时给出了 *host* 参数则不应调用此函数。 所有其他 `FTP` 方法只能在连接成功建立之后被调用。

## 参数:

- **host** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)) -- 要连接的主机。
- **port** ([*int*](https://docs.python.org/zh-cn/3.13/library/functions.html#int)) -- 要连接的 TCP 端口 (默认值: `21`，如 FTP 协议规范所指明的)。 很少有必要指定不同的端口号。
- **timeout** ([*float*](https://docs.python.org/zh-cn/3.13/library/functions.html#float) *|* *None*) -- 针对连接尝试的以秒数表示的超时值（默认值：全局默认超时设置值）。
- **source_address** ([*tuple*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#tuple) *|* *None*) -- A 2-tuple `(host, port)` for the socket to bind to as its source address before connecting.

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `ftplib.connect` 并附带参数 `self`, `host`, `port`。

*在 3.3 版本发生变更:* 添加了 *source_address* 参数。

## **getwelcome**()

​	返回服务器发送的欢迎消息，作为连接开始的回复。（该消息有时包含与用户有关的免责声明或帮助信息。）

## **login**(*user='anonymous'*, *passwd=''*, *acct=''*)

​	登录到已连接的 FTP 服务器。 在建立连接后，此函数应当只为每个实例调用一次；如果在创建 [`FTP`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP) 实例时给出了 *host* 和 *user* 参数则不应调用该函数。 大多数 FTP 命令只有在客户端已登录后才允许使用。

## 参数:

- **user** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)) -- The username to log in with (default: `'anonymous'`).
- **passwd** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)) -- The password to use when logging in. If not given, and if *passwd* is the empty string or `"-"`, a password will be automatically generated.
- **acct** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)) -- Account information to be used for the `ACCT` FTP command. Few systems implement this. See [RFC-959](https://datatracker.ietf.org/doc/html/rfc959.html) for more details.

## **abort**()

​	中止正在进行的文件传输。本方法并不总是有效，但值得一试。

## **sendcmd**(*cmd*)

​	将一条简单的命令字符串发送到服务器，返回响应的字符串。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `ftplib.sendcmd` 并附带参数 `self`, `cmd`。

## **voidcmd**(*cmd*)

​	将一条简单的命令字符串发送到服务器并对响应进行处理。 如果响应代码对应执行成功（代码在 200--299 范围内）则返回响应字符串。 在其他情况下则引发 [`error_reply`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.error_reply)。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `ftplib.sendcmd` 并附带参数 `self`, `cmd`。

## **retrbinary**(*cmd*, *callback*, *blocksize=8192*, *rest=None*)

​	以二进制传输模式获取一个文件。

## 参数:

- **cmd** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)) -- 一个正确的 `RETR` 命令: `"RETR *filename*"`。
- **callback** ([callable](https://docs.python.org/zh-cn/3.13/glossary.html#term-callable)) -- 一个针对所接收的每个数据块被调用的单形参可调用对象，其唯一参数即 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 类型的数据。
- **blocksize** ([*int*](https://docs.python.org/zh-cn/3.13/library/functions.html#int)) -- 在为进行实际传输而创建的底层 [`socket`](https://docs.python.org/zh-cn/3.13/library/socket.html#socket.socket) 对象上读取的块尺寸最大值。 这也对应于将要传给 *callback* 的数据尺寸最大值。 默认为 `8192`。
- **rest** ([*int*](https://docs.python.org/zh-cn/3.13/library/functions.html#int)) -- 要发送给服务器的 `REST` 命令。 参见 [`transfercmd()`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP.transfercmd) 方法的 *rest* 形参的文档。

## **retrlines**(*cmd*, *callback=None*)

​	以在初始化时由 *encoding* 形参指定的编码格式获取文件或目录列表。 *cmd* 应为一个适当的 `RETR` 命令 (参见 [`retrbinary()`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP.retrbinary)) 或是像 `LIST` 或 `NLST` 这样的命令 (通常即字符串 `'LIST'`)。 `LIST` 将获取一个包含文件名及文件相关信息的列表。 `NLST` 将获取一个文件名的列表。 *callback* 函数将针对每一行被调用并附带一个包含去除了末尾 CRLF 的行的字符串参数。 默认的 *callback* 会将行内容打印到 [`sys.stdout`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stdout)。

## **set_pasv**(*val*)

​	如果 *val* 为 true，则打开“被动”模式，否则禁用被动模式。默认下被动模式是打开的。

## **storbinary**(*cmd*, *fp*, *blocksize=8192*, *callback=None*, *rest=None*)

​	以二进制传输模式存储一个文件。

## 参数:

- **cmd** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)) -- 一个正确的 `STOR` 命令: `"STOR *filename*"`。
- **fp** ([file object](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-object)) -- 一个被读取直至 EOF 的文件对象（以二进制模式打开），使用其 [`read()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.RawIOBase.read) 方法以大小为 *blocksize* 的块来提供要存储的数据。
- **blocksize** ([*int*](https://docs.python.org/zh-cn/3.13/library/functions.html#int)) -- 读取块的大小。 默认为 `8192`。
- **callback** ([callable](https://docs.python.org/zh-cn/3.13/glossary.html#term-callable)) -- 一个针对所发送的每个数据块被调用的单形参可调用对象，其唯一参数即 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 类型的数据。
- **rest** ([*int*](https://docs.python.org/zh-cn/3.13/library/functions.html#int)) -- 要发送给服务器的 `REST` 命令。 参见 [`transfercmd()`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP.transfercmd) 方法的 *rest* 形参的文档。

*在 3.2 版本发生变更:* 增加了 *rest* 形参。

## **storlines**(*cmd*, *fp*, *callback=None*)

​	以文本行模式存储文件。*cmd* 应为恰当的 `STOR` 命令 (请参阅 [`storbinary()`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP.storbinary))。 *fp* 是一个 [文件对象](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-object) (以二进制模式打开)，将使用它的 [`readline()`](https://docs.python.org/zh-cn/3.13/library/io.html#io.IOBase.readline) 方法读取它的每一行，用于提供要存储的数据，直到遇到 EOF。 可选参数 *callback* 是单参数函数，在每行发送后都会以该行作为参数来调用它。

## **transfercmd**(*cmd*, *rest=None*)

​	在 FTP 数据连接上开始传输数据。如果传输处于活动状态，传输命令由 *cmd* 指定，需发送 `EPRT` 或 `PORT` 命令，然后接受连接 (accept)。如果服务器是被动服务器，需发送 `EPSV` 或 `PASV` 命令，连接到服务器 (connect)，然后启动传输命令。两种方式都将返回用于连接的套接字。

​	如果传入了可选参数 *rest*，则一条 `REST` 命令会被发送到服务器，并以 *rest* 作为参数。*rest* 通常表示请求文件中的字节偏移量，它告诉服务器重新开始发送文件的字节，从请求的偏移量处开始，跳过起始字节。但是请注意，[`transfercmd()`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP.transfercmd) 方法会将 *rest* 转换为字符串，但是不检查字符串的内容，转换用的编码是在初始化时指定的 *encoding* 参数。如果服务器无法识别 `REST` 命令，将引发 [`error_reply`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.error_reply) 异常。如果发生这种情况，只需不带 *rest* 参数调用 [`transfercmd()`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP.transfercmd)。

## **ntransfercmd**(*cmd*, *rest=None*)

​	类似于 [`transfercmd()`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP.transfercmd)，但返回一个元组，包括数据连接和数据的预计大小。如果预计大小无法计算，则返回的预计大小为 `None`。*cmd* 和 *rest* 的含义与 [`transfercmd()`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP.transfercmd) 中的相同。

## **mlsd**(*path=''*, *facts=[]*)

​	使用 `MLSD` 命令以标准格式列出目录内容 ([**RFC 3659**](https://datatracker.ietf.org/doc/html/rfc3659.html))。如果省略 *path* 则使用当前目录。*facts* 是字符串列表，表示所需的信息类型（如 `["type", "size", "perm"]`）。返回一个生成器对象，每个在 path 中找到的文件都将在该对象中生成两个元素的元组。第一个元素是文件名，第二个元素是该文件的 facts 的字典。该字典的内容受 *facts* 参数限制，但不能保证服务器会返回所有请求的 facts。

> Added in version 3.3.
>

## **nlst**(*argument*[, *...*])

​	返回一个文件名列表，文件名由 `NLST` 命令返回。可选参数 *argument* 是待列出的目录（默认为当前服务器目录）。可以使用多个参数，将非标准选项传递给 `NLST` 命令。

​	备注

 

​	如果目标服务器支持相关命令，那么 [`mlsd()`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP.mlsd) 提供的 API 更好。

## **dir**(*argument*[, *...*])

​	生成一个目录列表即 `LIST` 命令所返回的结果，将其打印到标准输出。 可选的 *argument* 是要列出的目录（默认为当前服务器目录）。 可以使用多个参数将非标准选项传给 `LIST` 命令。 如果最后一个参数是个函数，它将被用作 *callback* 函数，与 [`retrlines()`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP.retrlines) 的类似；默认将打印到 [`sys.stdout`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stdout)。 此方法将返回 `None`。

​	备注

 

​	如果目标服务器支持相关命令，那么 [`mlsd()`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP.mlsd) 提供的 API 更好。

## **rename**(*fromname*, *toname*)

​	将服务器上的文件 *fromname* 重命名为 *toname*。

## **delete**(*filename*)

​	将服务器上名为 *filename* 的文件删除。如果删除成功，返回响应文本，如果删除失败，在权限错误时引发 [`error_perm`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.error_perm)，在其他错误时引发 [`error_reply`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.error_reply)。

## **cwd**(*pathname*)

​	设置服务器端的当前目录。

## **mkd**(*pathname*)

​	在服务器上创建一个新目录。

## **pwd**()

​	返回服务器上当前目录的路径。

## **rmd**(*dirname*)

​	将服务器上名为 *dirname* 的目录删除。

## **size**(*filename*)

​	请求服务器上名为 *filename* 的文件大小。成功后以整数返回文件大小，未成功则返回 `None`。注意，`SIZE` 不是标准命令，但通常许多服务器的实现都支持该命令。

## **quit**()

​	向服务器发送 `QUIT` 命令并关闭连接。 这是关闭一个连接的“礼貌”方式，但是如果服务器对 `QUIT` 命令的响应带有错误消息则这会引发一个异常。 这意味着对 [`close()`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP.close) 方法的调用，它将使得 [`FTP`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP) 实例对后继调用无效（见下文）。

## **close**()

​	单方面关闭连接。 这不该被应用于已经关闭的连接，例如成功调用 [`quit()`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP.quit) 之后的连接。 在此调用之后 [`FTP`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP) 实例不应被继续使用（在调用 [`close()`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP.close) 或 [`quit()`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP.quit) 之后你不能通过再次发起调用 [`login()`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP.login) 方法重新打开连接）。

### FTP_TLS 对象

## *class* ftplib.**FTP_TLS**(*host=''*, *user=''*, *passwd=''*, *acct=''*, ***, *context=None*, *timeout=None*, *source_address=None*, *encoding='utf-8'*)

​	一个为 FTP 添加如 [**RFC 4217**](https://datatracker.ietf.org/doc/html/rfc4217.html) 所描述的 TLS 支持的 [`FTP`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP) 的子类。 连接到 21 端口在身份验证之前隐式地确保 FTP 控制连接的安全。

​	备注

 

​	用户必须通过调用 [`prot_p()`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP_TLS.prot_p) 方法显式地确保数据连接的安全。

## 参数:

- **host** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)) -- 要连接的主机名。 如果给出，则将由构造器隐式地调用 `connect(host)`。
- **user** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)) -- 如果给出 The username to log in with (default: `'anonymous'`).，则将由构造器隐式地调用 `login(host, passwd, acct)`。
- **passwd** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)) -- The password to use when logging in. If not given, and if *passwd* is the empty string or `"-"`, a password will be automatically generated.
- **acct** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)) -- Account information to be used for the `ACCT` FTP command. Few systems implement this. See [RFC-959](https://datatracker.ietf.org/doc/html/rfc959.html) for more details.
- **context** ([`ssl.SSLContext`](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl.SSLContext)) -- 一个允许将 SSL 配置选项、证书和私钥打包至一个单独的、可以长久存在的结构体中的 SSL 上下文对象。 请参阅 [安全考量](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl-security) 了解相关的最佳实践。
- **timeout** ([*float*](https://docs.python.org/zh-cn/3.13/library/functions.html#float) *|* *None*) -- 一个用于阻塞操作如 [`connect()`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP.connect) 的以秒数表示的超时值（默认值：全局默认超时设置）。
- **source_address** ([*tuple*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#tuple) *|* *None*) -- A 2-tuple `(host, port)` for the socket to bind to as its source address before connecting.
- **encoding** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)) -- The encoding for directories and filenames (default: `'utf-8'`).

> Added in version 3.2.
>

*在 3.3 版本发生变更:* 增加了 *source_address* 形参。

*在 3.4 版本发生变更:* 该类现在支持使用 [`ssl.SSLContext.check_hostname`](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl.SSLContext.check_hostname) 和 *服务器名称提示* (参见 [`ssl.HAS_SNI`](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl.HAS_SNI)) 进行主机名检测。

*在 3.9 版本发生变更:* 如果 *timeout* 参数设置为 0，创建非阻塞套接字时，它将引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError) 来阻止该操作。添加了 *encoding* 参数，且为了遵循 [**RFC 2640**](https://datatracker.ietf.org/doc/html/rfc2640.html)，该参数默认值从 Latin-1 改为了 UTF-8。

*在 3.12 版本发生变更:* 已弃用的 *keyfile* 和 *certfile* 形参已被移除。

​	以下是使用 [`FTP_TLS`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP_TLS) 类的会话示例:



``` python
>>> ftps = FTP_TLS('ftp.pureftpd.org')
>>> ftps.login()
'230 Anonymous user logged in'
>>> ftps.prot_p()
'200 Data protection level set to "private"'
>>> ftps.nlst()
['6jack', 'OpenBSD', 'antilink', 'blogbench', 'bsdcam', 'clockspeed', 'djbdns-jedi', 'docs', 'eaccelerator-jedi', 'favicon.ico', 'francotone', 'fugu', 'ignore', 'libpuzzle', 'metalog', 'minidentd', 'misc', 'mysql-udf-global-user-variables', 'php-jenkins-hash', 'php-skein-hash', 'php-webdav', 'phpaudit', 'phpbench', 'pincaster', 'ping', 'posto', 'pub', 'public', 'public_keys', 'pure-ftpd', 'qscan', 'qtc', 'sharedance', 'skycache', 'sound', 'tmp', 'ucarp']
```

`FTP_TLS` 类继承自 [`FTP`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP)，它定义了下列额外方法和属性：

## **ssl_version**

​	要使用的 SSL 版本 (默认为 [`ssl.PROTOCOL_SSLv23`](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl.PROTOCOL_SSLv23))。

## **auth**()

​	通过使用 TLS 或 SSL 来设置一个安全控制连接，具体取决于 [`ssl_version`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP_TLS.ssl_version) 属性是如何设置的。

*在 3.4 版本发生变更:* 该方法现在支持使用 [`ssl.SSLContext.check_hostname`](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl.SSLContext.check_hostname) 和 *服务器名称提示* (参见 [`ssl.HAS_SNI`](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl.HAS_SNI)) 进行主机名称检测。

## **ccc**()

​	将控制通道回复为纯文本。 这适用于发挥知道如何使用非安全 FTP 处理 NAT 而无需打开固定端口的防火墙的优势。

> Added in version 3.3.
>

## **prot_p**()

​	设置加密数据连接。

## **prot_c**()

​	设置明文数据连接。

### 模块变量

## *exception* ftplib.**error_reply**

​	从服务器收到意外答复时，将引发本异常。

## *exception* ftplib.**error_temp**

​	收到表示临时错误的错误代码（响应代码在 400--499 范围内）时，将引发本异常。

## *exception* ftplib.**error_perm**

​	收到表示永久性错误的错误代码（响应代码在 500--599 范围内）时，将引发本异常。

## *exception* ftplib.**error_proto**

​	从服务器收到不符合 FTP 响应规范的答复，比如以数字 1--5 开头时，将引发本异常。

## ftplib.**all_errors**

​	所有异常的集合（一个元组），由于 FTP 连接出现问题（并非调用者的编码错误），[`FTP`](https://docs.python.org/zh-cn/3.13/library/ftplib.html#ftplib.FTP) 实例的方法可能会引发这些异常。该集合包括上面列出的四个异常以及 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 和 [`EOFError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#EOFError)。

> 参见
>
> 
>
> - [`netrc`](https://docs.python.org/zh-cn/3.13/library/netrc.html#module-netrc) 模块
>
>   `.netrc` 文件格式解析器。FTP 客户端在响应用户之前，通常使用 `.netrc` 文件来加载用户认证信息。
