+++
title = "ssl --- 套接字对象的 TLS/SSL 包装器"
date = 2024-11-15T12:30:27+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/ssl.html](https://docs.python.org/zh-cn/3.13/library/ssl.html)
>
> 收录该文档的时间：`2024-11-15T12:30:27+08:00`

# `ssl` --- 套接字对象的 TLS/SSL 包装器

**源代码:** [Lib/ssl.py](https://github.com/python/cpython/tree/3.13/Lib/ssl.py)



------

​	该模块提供了对传输层安全（通常称为 "安全套接字层"）加密和网络套接字的对等认证设施的访问，包括客户端和服务器端。 该模块使用 OpenSSL 库。它可以在所有现代 Unix 系统、 Windows 、 macOS 和可能的其他平台上使用，只要 OpenSSL 安装在该平台上。

​备注
 

​	某些行为可能依赖于具体平台，因为调用了操作系统的套接字 API. 已安装的 OpenSSL 版本也可能会导致不同的行为。 比如，TLSv1.3 是 OpenSSL 1.1.1 版才提供的。

​	警告

 

​	在阅读 [安全考量]({{< ref "/library/ipc/ssl#ssl-security" >}}) 前不要使用此模块。 这样做可能会导致虚假的安全感，因为ssl模块的默认设置不一定适合你的应用程序。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

​	此模块在 WebAssembly 平台上无效或不可用。 请参阅 [WebAssembly 平台]({{< ref "/library/intro#wasm-availability" >}}) 了解详情。

​	文档本文档记录 `ssl` 模块的对象和函数；更多关于TLS,SSL,和证书的信息，请参阅下方的“详情”选项

​	本模块提供了一个类 [`ssl.SSLSocket`]({{< ref "/library/ipc/ssl#ssl.SSLSocket" >}})，它派生自 [`socket.socket`]({{< ref "/library/ipc/socket#socket.socket" >}}) 类型，并提供类似套接字的包装器，也能够使用 SSL 对通过套接字的数据进行加密和解密。 它支持一些额外方法例如 `getpeercert()`，该方法可以从连接的另一端获取证书，还有 `cipher()`，该方法可获取安全连接所使用的密码，以及 `get_verified_chain()`、`get_unverified_chain()`，它们可获取证书链。

​	对于更复杂的应用程序，[`ssl.SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 类有助于管理设置项和证书，进而可以被使用 [`SSLContext.wrap_socket()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.wrap_socket" >}}) 方法创建的 SSL 套接字继承。

*在 3.5.3 版本发生变更:* 更新以支持和 OpenSSL 1.1.0 的链接

> 在 3.6 版本发生变更: OpenSSL 0.9.8、1.0.0 和 1.0.1 已过时，将不再被支持。在 ssl 模块未来的版本中，最低需要 OpenSSL 1.0.2 或 1.1.0。

> 在 3.10 版本发生变更: [**PEP 644**](https://peps.python.org/pep-0644/) 已经实现。ssl 模块需要 OpenSSL 1.1.1 以上版本的支持。

​	使用废弃的常量和函数会导致废弃警告。

## 方法、常量和异常处理

### 套接字创建

[`SSLSocket`]({{< ref "/library/ipc/ssl#ssl.SSLSocket" >}}) 的实例必须使用 [`SSLContext.wrap_socket()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.wrap_socket" >}}) 方法来创建。 辅助函数 [`create_default_context()`]({{< ref "/library/ipc/ssl#ssl.create_default_context" >}}) 将返回一个使用安全的默认设置的新上下文。

​	客户端套接字实例，采用默认上下文和IPv4/IPv6双栈:

```
import socket
import ssl

hostname = 'www.python.org'
context = ssl.create_default_context()

with socket.create_connection((hostname, 443)) as sock:
    with context.wrap_socket(sock, server_hostname=hostname) as ssock:
        print(ssock.version())
```

​	客户端套接字示例，带有自定义上下文和IPv4:

```
hostname = 'www.python.org'
# PROTOCOL_TLS_CLIENT 需要有效的证书链和主机名
context = ssl.SSLContext(ssl.PROTOCOL_TLS_CLIENT)
context.load_verify_locations('path/to/cabundle.pem')

with socket.socket(socket.AF_INET, socket.SOCK_STREAM, 0) as sock:
    with context.wrap_socket(sock, server_hostname=hostname) as ssock:
        print(ssock.version())
```

​	服务器套接字实例，在localhost上监听IPv4:

```
context = ssl.SSLContext(ssl.PROTOCOL_TLS_SERVER)
context.load_cert_chain('/path/to/certchain.pem', '/path/to/private.key')

with socket.socket(socket.AF_INET, socket.SOCK_STREAM, 0) as sock:
    sock.bind(('127.0.0.1', 8443))
    sock.listen(5)
    with context.wrap_socket(sock, server_side=True) as ssock:
        conn, addr = ssock.accept()
        ...
```

### 上下文创建

​	便捷函数，可以帮助创建 [`SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 对象，用于常见的目的。

## ssl.**create_default_context**(*purpose=Purpose.SERVER_AUTH*, *cafile=None*, *capath=None*, *cadata=None*)

​	返回一个新的 [`SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 对象，使用给定 *purpose* 的默认设置。 该设置由 [`ssl`]({{< ref "/library/ipc/ssl#module-ssl" >}}) 模块选择，并且通常是代表一个比直接调用 [`SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 构造器时更高的安全等级。

*cafile*, *capath*, *cadata* 代表用于进行证书核验的可选受信任 CA 证书，与 [`SSLContext.load_verify_locations()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.load_verify_locations" >}}) 的一致。 如果三个参数均为 [`None`]({{< ref "/library/constants#None" >}})，此函数可以转而选择信任系统的默认 CA 证书。

​	设置为: [`PROTOCOL_TLS_CLIENT`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS_CLIENT" >}}) 或 [`PROTOCOL_TLS_SERVER`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS_SERVER" >}}), [`OP_NO_SSLv2`]({{< ref "/library/ipc/ssl#ssl.OP_NO_SSLv2" >}}) 和 [`OP_NO_SSLv3`]({{< ref "/library/ipc/ssl#ssl.OP_NO_SSLv3" >}}) 带有不含 RC4 及未认证的高强度加密密码套件。 传入 [`SERVER_AUTH`]({{< ref "/library/ipc/ssl#ssl.Purpose.SERVER_AUTH" >}}) 作为 *purpose* 将把 [`verify_mode`]({{< ref "/library/ipc/ssl#ssl.SSLContext.verify_mode" >}}) 设为 [`CERT_REQUIRED`]({{< ref "/library/ipc/ssl#ssl.CERT_REQUIRED" >}}) 并加载 CA 证书（若至少给出 *cafile*, *capath* 或 *cadata* 之一）或使用 [`SSLContext.load_default_certs()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.load_default_certs" >}}) 加载默认的 CA 证书。

​	当 [`keylog_filename`]({{< ref "/library/ipc/ssl#ssl.SSLContext.keylog_filename" >}}) 受支持并且设置了环境变量 `SSLKEYLOGFILE` 时，[`create_default_context()`]({{< ref "/library/ipc/ssl#ssl.create_default_context" >}}) 会启用密钥日志记录。

​	此上下文的默认设置包括 [`VERIFY_X509_PARTIAL_CHAIN`]({{< ref "/library/ipc/ssl#ssl.VERIFY_X509_PARTIAL_CHAIN" >}}) 和 [`VERIFY_X509_STRICT`]({{< ref "/library/ipc/ssl#ssl.VERIFY_X509_STRICT" >}})。 这使得下层的 OpenSSL 实现的行为与符合 [**RFC 5280**](https://datatracker.ietf.org/doc/html/rfc5280.html) 的实现更为相似，代价则是与较旧的 X.509 证书存在少量不兼容。

​备注
 

​	协议、选项、密码和其他设置可随时更改为更具约束性的值而无须事先弃用。 这些值代表了兼容性和安全性之间的合理平衡。

​	如果你的应用需要特定的设置，你应当创建一个 [`SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 并自行应用设置。

​备注
 

​	如果你发现当某些较旧的客户端或服务器尝试与用此函数创建的 [`SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 进行连接时收到了报错提示 "Protocol or cipher suite mismatch"，这可能是因为它们只支持 SSL3.0 而它被此函数用 [`OP_NO_SSLv3`]({{< ref "/library/ipc/ssl#ssl.OP_NO_SSLv3" >}}) 排除掉了。 SSL3.0 被广泛认为 [完全不可用](https://en.wikipedia.org/wiki/POODLE)。 如果你仍希望继续使用此函数但仍允许 SSL 3.0 连接，你可以使用以下代码重新启用它们:

```
ctx = ssl.create_default_context(Purpose.CLIENT_AUTH)
ctx.options &= ~ssl.OP_NO_SSLv3
```

​备注
 

​	此上下文默认会启用 [`VERIFY_X509_STRICT`]({{< ref "/library/ipc/ssl#ssl.VERIFY_X509_STRICT" >}})，它可能拒绝下层 OpenSSL 实现在其他情况下应当会接受的 [**RFC 5280**](https://datatracker.ietf.org/doc/html/rfc5280.html) 之前的证书或格式错误的证书。 虽然不建议禁用此功能，但你可以使用以下方式做到这一点:

```
ctx = ssl.create_default_context()
ctx.verify_flags &= ~ssl.VERIFY_X509_STRICT
```

> Added in version 3.4.
>

*在 3.4.4 版本发生变更:* RC4 被从默认密码字符串中丢弃。

> 在 3.6 版本发生变更: ChaCha20/Poly1305 被添加到默认密码字符串中。

​	3DES 被从默认密码字符串中丢弃。

> 在 3.8 版本发生变更: 增加了对密钥日志记录至 `SSLKEYLOGFILE` 的支持。

> 在 3.10 版本发生变更: 当前上下文使用 [`PROTOCOL_TLS_CLIENT`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS_CLIENT" >}}) 或 [`PROTOCOL_TLS_SERVER`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS_SERVER" >}}) 协议而非通用的 [`PROTOCOL_TLS`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS" >}})。

> 在 3.13 版本发生变更: 此上下文现在会在其默认验证旗标中使用 [`VERIFY_X509_PARTIAL_CHAIN`]({{< ref "/library/ipc/ssl#ssl.VERIFY_X509_PARTIAL_CHAIN" >}}) 和 [`VERIFY_X509_STRICT`]({{< ref "/library/ipc/ssl#ssl.VERIFY_X509_STRICT" >}})。

### 异常

## *exception* ssl.**SSLError**

​	引发此异常以提示来自下层 SSL 实现（目前由 OpenSSL 库提供）的错误。 它表示在下层网络连接之上叠加的高层级加密和验证层存在某种问题。 此错误是 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 的一个子类型。 [`SSLError`]({{< ref "/library/ipc/ssl#ssl.SSLError" >}}) 实例的错误和消息是由 OpenSSL 库提供的。

> 在 3.3 版本发生变更: [`SSLError`]({{< ref "/library/ipc/ssl#ssl.SSLError" >}}) 曾经是 [`socket.error`]({{< ref "/library/ipc/socket#socket.error" >}}) 的一个子类型。

## **library**

​	一个字符串形式的助记符，用来指明发生错误的 OpenSSL 子模块，例如 `SSL`, `PEM` 或 `X509`。 可能的取值范围依赖于 OpenSSL 的版本。

> Added in version 3.3.
>

## **reason**

​	一个字符串形式的助记符，用来指明发生错误的原因，例如 `CERTIFICATE_VERIFY_FAILED`。 可能的取值范围依赖于 OpenSSL 的版本。

> Added in version 3.3.
>

## *exception* ssl.**SSLZeroReturnError**

[`SSLError`]({{< ref "/library/ipc/ssl#ssl.SSLError" >}}) 的子类，当尝试读取或写入且 SSL 连接已被完全关闭时会被引发。 请注意这并不意味着下层的传输（读取 TCP）已被关闭。

> Added in version 3.3.
>

## *exception* ssl.**SSLWantReadError**

[`SSLError`]({{< ref "/library/ipc/ssl#ssl.SSLError" >}}) 的子类，当尝试读取或写入数据，但在请求被满足之前还需要在下层的 TCP 传输上接收更多数据时会被 [非阻塞型 SSL 套接字]({{< ref "/library/ipc/ssl#ssl-nonblocking" >}}) 引发。

> Added in version 3.3.
>

## *exception* ssl.**SSLWantWriteError**

[`SSLError`]({{< ref "/library/ipc/ssl#ssl.SSLError" >}}) 的子类，当尝试读取或写入数据，但在请求被满足之前还需要在下层的 TCP 传输上发送更多数据时会被 [非阻塞型 SSL 套接字]({{< ref "/library/ipc/ssl#ssl-nonblocking" >}}) 引发。

> Added in version 3.3.
>

## *exception* ssl.**SSLSyscallError**

[`SSLError`]({{< ref "/library/ipc/ssl#ssl.SSLError" >}}) 的子类，当尝试在 SSL 套接字上执行操作时遇到系统错误时会被引发。 不幸的是，没有简单的方式能检查原始 errno 编号。

> Added in version 3.3.
>

## *exception* ssl.**SSLEOFError**

[`SSLError`]({{< ref "/library/ipc/ssl#ssl.SSLError" >}}) 的子类，当 SSL 连接被突然终止时会被引发。 通常，当遇到此错误时你不应再尝试重用下层的传输。

> Added in version 3.3.
>

## *exception* ssl.**SSLCertVerificationError**

[`SSLError`]({{< ref "/library/ipc/ssl#ssl.SSLError" >}}) 的子类，当证书验证失败时会被引发。

> Added in version 3.7.
>

## **verify_code**

​	一个数字形式的错误编号，用于表示验证错误。

## **verify_message**

​	用于表示验证错误的人类可读的字符串。

## *exception* ssl.**CertificateError**

[`SSLCertVerificationError`]({{< ref "/library/ipc/ssl#ssl.SSLCertVerificationError" >}}) 的别名。

> 在 3.7 版本发生变更: 此异常现在是 [`SSLCertVerificationError`]({{< ref "/library/ipc/ssl#ssl.SSLCertVerificationError" >}}) 的别名。

### 随机生成

## ssl.**RAND_bytes**(*num*)

​	返回 *num* 个高加密强度伪随机字节数据。 如果 PRNG 未使用足够的数据作为随机种子或者如果当前 RAND 方法不支持该操作则会引发 [`SSLError`]({{< ref "/library/ipc/ssl#ssl.SSLError" >}})。 [`RAND_status()`]({{< ref "/library/ipc/ssl#ssl.RAND_status" >}}) 可被用来检查 PRNG 的状态而 [`RAND_add()`]({{< ref "/library/ipc/ssl#ssl.RAND_add" >}}) 可被用来为 PRNG 设置随机种子。

​	对于几乎所有应用程序都更推荐使用 [`os.urandom()`]({{< ref "/library/allos/os#os.urandom" >}})。

​	请阅读维基百科文章 [Cryptographically secure pseudorandom number generator (CSPRNG)](https://en.wikipedia.org/wiki/Cryptographically_secure_pseudorandom_number_generator) 以了解对于高加密强度生成器的具体要求。

> Added in version 3.3.
>

## ssl.**RAND_status**()

​	如果 SSL 伪随机数生成器已使用‘足够的’随机性作为种子则返回 `True`，否则返回 `False`。 你可以使用 `ssl.RAND_egd()` 和 [`ssl.RAND_add()`]({{< ref "/library/ipc/ssl#ssl.RAND_add" >}}) 来增加伪随机数生成器的随机性。

## ssl.**RAND_add**(*bytes*, *entropy*)

​	将给定的 *bytes* 混合到 SSL 伪随机数生成器中。 参数 *entropy* (浮点数) 是字符串中包含的熵值的下限 （因此可以始终使用 `0.0`)。 请参阅 [**RFC 1750**](https://datatracker.ietf.org/doc/html/rfc1750.html) 了解有关熵源的更多信息。

> 在 3.5 版本发生变更: 现在接受可写的 [字节类对象]({{< ref "/glossary/idx#term-bytes-like-object" >}})。

### 证书处理

## ssl.**cert_time_to_seconds**(*cert_time*)

​	返回距离 Unix 纪元零时的秒数，给定的 `cert_time` 字符串代表来自证书的 "notBefore" 或 "notAfter" 日期值，采用 `"%b %d %H:%M:%S %Y %Z"` strptime 格式（C 区域）。

​	以下为示例代码:



``` python
>>> import ssl
>>> timestamp = ssl.cert_time_to_seconds("Jan  5 09:34:43 2018 GMT")
>>> timestamp  
1515144883
>>> from datetime import datetime
>>> print(datetime.utcfromtimestamp(timestamp))  
2018-01-05 09:34:43
```

​	"notBefore" 或 "notAfter" 日期值必须使用 GMT ([**RFC 5280**](https://datatracker.ietf.org/doc/html/rfc5280.html))。

> 在 3.5 版本发生变更: 将输入时间解读为 UTC 时间，基于输入字符串中指明的 'GMT' 时区。 在之前使用的是本地时区。 返回一个整数（不带输入格式中秒的分数部分）

## ssl.**get_server_certificate**(*addr*, *ssl_version=PROTOCOL_TLS_CLIENT*, *ca_certs=None*[, *timeout*])

​	给定使用 SSL 保护的服务器的地址 `addr`，形式为一个 (*hostname*, *port-number*) 对，获取该服务器的证书，并返回为 PEM 编码的字符串。 如果指定了 `ssl_version`，则使用该版本的 SSL 协议尝试连接该服务器。 如果指定了 *ca_certs*，它应当是一个包含根证书列表的文件，与 [`SSLContext.load_verify_locations()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.load_verify_locations" >}}) 中 *cafile* 形参所使用的格式相同。 该调用将尝试根据该根证书集来难服务器的证书，如果验证失败则调用也将失败。 可以通过 `timeout` 形参来指定超时限制。

> 在 3.3 版本发生变更: 此函数现在是 IPv6 兼容的。-compatible.

> 在 3.5 版本发生变更: 默认的 *ssl_version* 从 [`PROTOCOL_SSLv3`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_SSLv3" >}}) 改为 [`PROTOCOL_TLS`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS" >}}) 以保证与现代服务器的最大兼容性。

> 在 3.10 版本发生变更: 加入 *timeout* 参数。

## ssl.**DER_cert_to_PEM_cert**(*DER_cert_bytes*)

​	根据给定的 DER 编码字节块形式的证书，返回同一证书的 PEM 编码字符串版本。

## ssl.**PEM_cert_to_DER_cert**(*PEM_cert_string*)

​	根据给定的 ASCII PEM 字符串形式的证书，返回同一证书的 DER 编码字节序列。

## ssl.**get_default_verify_paths**()

​	返回包含 OpenSSL 的默认 cafile 和 capath 的路径的命名元组。 此路径与 [`SSLContext.set_default_verify_paths()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.set_default_verify_paths" >}}) 所使用的相同。 返回值是一个 [named tuple]({{< ref "/glossary/idx#term-named-tuple" >}}) `DefaultVerifyPaths`:

- `cafile` - 解析出的 cafile 路径或者如果文件不存在则为 `None`,
- `capath` - 解析出的 capath 路径或者如果目录不存在则为 `None`,
- `openssl_cafile_env` - 指向一个 cafile 的 OpenSSL 环境键,
- `openssl_cafile` - 一个 cafile 的硬编码路径,
- `openssl_capath_env` - 指向一个 capath 的 OpenSSL 环境键,
- `openssl_capath` - 一个 capath 目录的硬编码路径

> Added in version 3.4.
>

## ssl.**enum_certificates**(*store_name*)

​	从 Windows 的系统证书库中检索证书。 *store_name* 可以是 `CA`, `ROOT` 或 `MY` 中的一个。 Windows 也可能会提供额外的证书库。

​	此函数返回一个包含 (cert_bytes, encoding_type, trust) 元组的列表。 encoding_type 指明 cert_bytes 的编码格式。 它可以为 `x509_asn` 以表示 X.509 ASN.1 数据或是 `pkcs_7_asn` 以表示 PKCS#7 ASN.1 数据。 trust 以 OIDS 集合的形式指明证书的目的，或者如果证书对于所有目的都可以信任则为 `True`。

​	示例:



``` python
>>> ssl.enum_certificates("CA")
[(b'data...', 'x509_asn', {'1.3.6.1.5.5.7.3.1', '1.3.6.1.5.5.7.3.2'}),
 (b'data...', 'x509_asn', True)]
```

[Availability]({{< ref "/library/intro#availability" >}}): Windows.

> Added in version 3.4.
>

## ssl.**enum_crls**(*store_name*)

​	Windows 的系统证书库中检索 CRL。 *store_name* 可以是 `CA`, `ROOT` 或 `MY` 中的一个。 Windows 也可能会提供额外的证书库。

​	此函数返回一个包含 (cert_bytes, encoding_type, trust) 元组的列表。 encoding_type 指明 cert_bytes 的编码格式。 它可以为 `x509_asn` 以表示 X.509 ASN.1 数据或是 `pkcs_7_asn` 以表示 PKCS#7 ASN.1 数据。

[Availability]({{< ref "/library/intro#availability" >}}): Windows.

> Added in version 3.4.
>

### 常量

> ​	所有常量现在都是 [`enum.IntEnum`]({{< ref "/library/datatypes/enum#enum.IntEnum" >}}) 或 [`enum.IntFlag`]({{< ref "/library/datatypes/enum#enum.IntFlag" >}}) 多项集的成员。
>
> *Added in version 3.6.*

## ssl.**CERT_NONE**

[`SSLContext.verify_mode`]({{< ref "/library/ipc/ssl#ssl.SSLContext.verify_mode" >}}) 可能的取值。 [`PROTOCOL_TLS_CLIENT`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS_CLIENT" >}}) 除外，这是默认的模式。 对于客户端套接字，几乎任何证书都会被接受。 验证错误，如不受信任或过期的证书等，会被忽略并且不会中止 TLS/SSL 握手。

​	在服务器模式下，不会从客户端请求任何证书，因此客户端不会发送任何用于客户端证书身份验证的证书。

​	参见下文对于 [安全考量]({{< ref "/library/ipc/ssl#ssl-security" >}}) 的讨论。

## ssl.**CERT_OPTIONAL**

[`SSLContext.verify_mode`]({{< ref "/library/ipc/ssl#ssl.SSLContext.verify_mode" >}}) 可能的取值。 在客户端模式下，[`CERT_OPTIONAL`]({{< ref "/library/ipc/ssl#ssl.CERT_OPTIONAL" >}}) 具有与 [`CERT_REQUIRED`]({{< ref "/library/ipc/ssl#ssl.CERT_REQUIRED" >}}) 相同的含义。 对于客户端套接字推荐改用 [`CERT_REQUIRED`]({{< ref "/library/ipc/ssl#ssl.CERT_REQUIRED" >}})。

​	在服务器模式下，客户端证书请求会被发送给客户端。 客户端可以忽略请求也可以发送一个证书以执行 TLS 客户端证书身份验证。 如果客户端选择发送证书，则将对其执行验证。 任何验证错误都将立即中止 TLS 握手。

​	使用此设置要求将一组有效的 CA 证书传递给 [`SSLContext.load_verify_locations()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.load_verify_locations" >}})。

## ssl.**CERT_REQUIRED**

[`SSLContext.verify_mode`]({{< ref "/library/ipc/ssl#ssl.SSLContext.verify_mode" >}}) 可能的取值。 在此模式下，需要从套接字连接的另一端获取证书；如果未提供证书，或验证失败则将引发 [`SSLError`]({{< ref "/library/ipc/ssl#ssl.SSLError" >}})。 此模式 **不能** 在客户端模式下对证书进行验证因为它不会匹配主机名。 [`check_hostname`]({{< ref "/library/ipc/ssl#ssl.SSLContext.check_hostname" >}}) 也必须被启用以验证证书的真实性。 [`PROTOCOL_TLS_CLIENT`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS_CLIENT" >}}) 会使用 [`CERT_REQUIRED`]({{< ref "/library/ipc/ssl#ssl.CERT_REQUIRED" >}}) 并默认启用 [`check_hostname`]({{< ref "/library/ipc/ssl#ssl.SSLContext.check_hostname" >}})。

​	对于服务器套接字，此模式会提供强制性的 TLS 客户端证书验证。 客户端证书请求会被发送给客户端并且客户端必须提供有效且受信任的证书。

​	使用此设置要求将一组有效的 CA 证书传递给 [`SSLContext.load_verify_locations()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.load_verify_locations" >}})。

## *class* ssl.**VerifyMode**

​	CERT_* 常量的 [`enum.IntEnum`]({{< ref "/library/datatypes/enum#enum.IntEnum" >}}) 多项集。

> Added in version 3.6.
>

## ssl.**VERIFY_DEFAULT**

[`SSLContext.verify_flags`]({{< ref "/library/ipc/ssl#ssl.SSLContext.verify_flags" >}}) 可能的取值。 在此模式下，证书吊销列表（CRL）并不会被检查。 OpenSSL 默认不要求也不验证 CRL。

> Added in version 3.4.
>

## ssl.**VERIFY_CRL_CHECK_LEAF**

[`SSLContext.verify_flags`]({{< ref "/library/ipc/ssl#ssl.SSLContext.verify_flags" >}}) 可能的取值。 在此模式下， 只会检查对等证书而不检查任何中间 CA 证书。 此模式要求提供由对等证书颁发者（其直接上级 CA）签名的有效 CRL。 如果未使用 [`SSLContext.load_verify_locations`]({{< ref "/library/ipc/ssl#ssl.SSLContext.load_verify_locations" >}}) 加载正确的 CRL，则验证将失败。

> Added in version 3.4.
>

## ssl.**VERIFY_CRL_CHECK_CHAIN**

[`SSLContext.verify_flags`]({{< ref "/library/ipc/ssl#ssl.SSLContext.verify_flags" >}}) 可能的取值。 在此模式下，会检查对等证书链中所有证书的 CRL。

> Added in version 3.4.
>

## ssl.**VERIFY_X509_STRICT**

[`SSLContext.verify_flags`]({{< ref "/library/ipc/ssl#ssl.SSLContext.verify_flags" >}}) 可能的取值，用于禁用已损坏 X.509 证书的绕过操作。

> Added in version 3.4.
>

## ssl.**VERIFY_ALLOW_PROXY_CERTS**

[`SSLContext.verify_flags`]({{< ref "/library/ipc/ssl#ssl.SSLContext.verify_flags" >}}) 的可能取值，启用代理证书验证。

> Added in version 3.10.
>

## ssl.**VERIFY_X509_TRUSTED_FIRST**

[`SSLContext.verify_flags`]({{< ref "/library/ipc/ssl#ssl.SSLContext.verify_flags" >}}) 可能的取值。 它指示 OpenSSL 在构建用于验证某个证书的信任链时首选受信任的证书。 此旗标将默认被启用。

> Added in version 3.4.4.
>

## ssl.**VERIFY_X509_PARTIAL_CHAIN**

[`SSLContext.verify_flags`]({{< ref "/library/ipc/ssl#ssl.SSLContext.verify_flags" >}}) 的可能取值。它指示 OpenSSL 接受信任存储中的中间 CA 作为信任锚，与自我签名的根 CA 证书的方式相同。这样就能信任中间 CA 颁发的证书，而不一定非要去信任其祖先的根 CA。

> Added in version 3.10.
>

## *class* ssl.**VerifyFlags**

​	VERIFY_* 常量的 [`enum.IntFlag`]({{< ref "/library/datatypes/enum#enum.IntFlag" >}}) 多项集。

> Added in version 3.6.
>

## ssl.**PROTOCOL_TLS**

​	选择客户端和服务器均支持的最高协议版本。 此选项名称并不准确，实际上 "SSL" 和 "TLS" 协议均可被选择。

> Added in version 3.6.
>

> 自 3.10 版本弃用: TLS 客户端和服务器需要不同的默认设置来实现安全通信。通用的 TLS 协议常量已废弃，而采用 [`PROTOCOL_TLS_CLIENT`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS_CLIENT" >}}) 和 [`PROTOCOL_TLS_SERVER`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS_SERVER" >}})。

## ssl.**PROTOCOL_TLS_CLIENT**

​	自动协商为客户端和服务器都支持的最高版本协议，并配置当前上下文客户端的连接。该协议默认启用 [`CERT_REQUIRED`]({{< ref "/library/ipc/ssl#ssl.CERT_REQUIRED" >}}) 和 [`check_hostname`]({{< ref "/library/ipc/ssl#ssl.SSLContext.check_hostname" >}})。

> Added in version 3.6.
>

## ssl.**PROTOCOL_TLS_SERVER**

​	自动协商为客户端和服务器都支持的最高版本协议，并配置上下文服务器端的连接。

> Added in version 3.6.
>

## ssl.**PROTOCOL_SSLv23**

[`PROTOCOL_TLS`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS" >}}) 的别名。

> 自 3.6 版本弃用: 请改用 [`PROTOCOL_TLS`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS" >}})。

## ssl.**PROTOCOL_SSLv3**

​	选择 SSL 版本 3 作为通道加密协议。

​	如果OpenSSL是用 `no-ssl3` 选项编译的，则该协议不可用。

​	警告

 

​	SSL 版本 3 并不安全。 极不建议使用它。

> 自 3.6 版本弃用: OpenSSL 已经废弃了所有特定于版本的协议。请换用带有 [`SSLContext.minimum_version`]({{< ref "/library/ipc/ssl#ssl.SSLContext.minimum_version" >}}) 和 [`SSLContext.maximum_version`]({{< ref "/library/ipc/ssl#ssl.SSLContext.maximum_version" >}}) 的默认协议 [`PROTOCOL_TLS_SERVER`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS_SERVER" >}}) 或 [`PROTOCOL_TLS_CLIENT`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS_CLIENT" >}}) 。

## ssl.**PROTOCOL_TLSv1**

​	选择 TLS 版本 1.0 作为通道加密协议。

> 自 3.6 版本弃用: OpenSSL 已经废弃了所有特定于版本的协议。

## ssl.**PROTOCOL_TLSv1_1**

​	选择 TLS 版本 1.1 作为通道加密协议。 仅适用于 openssl 版本 1.0.1+。

> Added in version 3.4.
>

> 自 3.6 版本弃用: OpenSSL 已经废弃了所有特定于版本的协议。

## ssl.**PROTOCOL_TLSv1_2**

​	选用 TLS 1.2 版本作为隧道加密协议。只适用于 openssl 1.0.1 以上版本。

> Added in version 3.4.
>

> 自 3.6 版本弃用: OpenSSL 已经废弃了所有特定于版本的协议。

## ssl.**OP_ALL**

​	对存在于其他 SSL 实现中的各种缺陷启用绕过操作。 默认会设置此选项。 没有必要设置与 OpenSSL 的 `SSL_OP_ALL` 常量同名的旗标。

> Added in version 3.2.
>

## ssl.**OP_NO_SSLv2**

​	阻止 SSLv2 连接。 此选项仅可与 [`PROTOCOL_TLS`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS" >}}) 结合使用。 它会阻止对等方选择 SSLv2 作为协议版本。

> Added in version 3.2.
>

> 自 3.6 版本弃用: SSLv2 已被弃用

## ssl.**OP_NO_SSLv3**

​	阻止 SSLv3 连接。 此选项仅可与 [`PROTOCOL_TLS`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS" >}}) 结合使用。 它会阻止对等方选择 SSLv3 作为协议版本。

> Added in version 3.2.
>

> 自 3.6 版本弃用: SSLv3 已被弃用

## ssl.**OP_NO_TLSv1**

​	阻止 TLSv1 连接。 此选项仅可与 [`PROTOCOL_TLS`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS" >}}) 结合使用。 它会阻止对等方选择 TLSv1 作为协议版本。

> Added in version 3.2.
>

> 自 3.7 版本弃用: 此选项自 OpenSSL 1.1.0 起已被弃用，请改用新的 [`SSLContext.minimum_version`]({{< ref "/library/ipc/ssl#ssl.SSLContext.minimum_version" >}}) 和 [`SSLContext.maximum_version`]({{< ref "/library/ipc/ssl#ssl.SSLContext.maximum_version" >}})。

## ssl.**OP_NO_TLSv1_1**

​	阻止 TLSv1.1 连接。 此选项仅可与 [`PROTOCOL_TLS`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS" >}}) 结合使用。 它会阻止对等方选择 TLSv1.1 作为协议版本。 仅适用于 openssl 版本 1.0.1+。

> Added in version 3.4.
>

> 自 3.7 版本弃用: 此选项自 OpenSSL 1.1.0 起已被弃用。

## ssl.**OP_NO_TLSv1_2**

​	阻止 TLSv1.2 连接。 此选项仅可与 [`PROTOCOL_TLS`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS" >}}) 结合使用。 它会阻止对等方选择 TLSv1.2 作为协议版本。 仅适用于 openssl 版本 1.0.1+。

> Added in version 3.4.
>

> 自 3.7 版本弃用: 此选项自 OpenSSL 1.1.0 起已被弃用。

## ssl.**OP_NO_TLSv1_3**

​	阻止 TLSv1.3 连接。 此选项仅可与 [`PROTOCOL_TLS`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS" >}}) 结合使用。 它会阻止对等方选择 TLSv1.3 作为协议版本。 TLS 1.3 适用于 OpenSSL 1.1.1 或更新的版本。 当 Python 编译是基于较旧版本的 OpenSSL 时，该旗标默认为 *0*。

> Added in version 3.6.3.
>

> 自 3.7 版本弃用: 此选项自 OpenSSL 1.1.0 起已被弃用。 它被添加到 2.7.15 和 3.6.3 是为了向下兼容 OpenSSL 1.0.2。

## ssl.**OP_NO_RENEGOTIATION**

​	禁用所有 TLSv1.2 和更早版本的重协商操作。 不发送 HelloRequest 消息，并忽略通过 ClientHello 发起的重协商请求。

​	此选项仅适用于 OpenSSL 1.1.0h 及更新的版本。

> Added in version 3.7.
>

## ssl.**OP_CIPHER_SERVER_PREFERENCE**

​	使用服务器的密码顺序首选项，而不是客户端的首选项。 此选项在客户端套接字和 SSLv2 服务器套接字上无效。

> Added in version 3.3.
>

## ssl.**OP_SINGLE_DH_USE**

​	防止对于单独 SSL 会话重用相同的 DH 密钥。 这会提升前向保密性但需要更多的计算资源。 此选项仅适用于服务器套接字。

> Added in version 3.3.
>

## ssl.**OP_SINGLE_ECDH_USE**

​	防止对于单独 SSL 会话重用相同的 ECDH 密钥。 这会提升前向保密性但需要更多的计算资源。 此选项仅适用于服务器套接字。

> Added in version 3.3.
>

## ssl.**OP_ENABLE_MIDDLEBOX_COMPAT**

​	在 TLS 1.3 握手中发送虚拟更改密码规格（CCS）消息以使得 TLS 1.3 连接看起来更像是 TLS 1.2 连接。

​	此选项仅适用于 OpenSSL 1.1.1 及更新的版本。

> Added in version 3.8.
>

## ssl.**OP_NO_COMPRESSION**

​	在 SSL 通道上禁用压缩。 这适用于应用协议支持自己的压缩方案的情况。

> Added in version 3.3.
>

## *class* ssl.**Options**

​	OP_* 常量的 [`enum.IntFlag`]({{< ref "/library/datatypes/enum#enum.IntFlag" >}}) 多项集。

## ssl.**OP_NO_TICKET**

​	阻止客户端请求会话凭据。

> Added in version 3.6.
>

## ssl.**OP_IGNORE_UNEXPECTED_EOF**

​	忽略 TLS 连接的意外关闭。

​	此选项仅适用于 OpenSSL 3.0.0 及更新的版本。

> Added in version 3.10.
>

## ssl.**OP_ENABLE_KTLS**

​	启用内核 TLS。 为了利用该特征，OpenSSL 编译时必须附带对它的支持，并且协商的密码套件和扩展必须被它所支持（受支持项的列表可能因平台和内核版本而有所变化）。

​	请注意当启用内核 TLS 时某些加解密操作将由内核直接执行而不是通过任何可用的 OpenSSL 提供程序。 这可能并不是你想要的，例如，当应用程序要求所有加解密操作由 FIPS 提供程序执行时。

​	此选项仅适用于 OpenSSL 3.0.0 及更新的版本。

> Added in version 3.12.
>

## ssl.**OP_LEGACY_SERVER_CONNECT**

​	允许只在 OpenSSL 和未打补丁的服务器之间进行旧式的不安全协商。

> Added in version 3.12.
>

## ssl.**HAS_ALPN**

​	OpenSSL 库是否具有对 [**RFC 7301**](https://datatracker.ietf.org/doc/html/rfc7301.html) 中描述的 *应用层协议协商* TLS 扩展的内置支持。

> Added in version 3.5.
>

## ssl.**HAS_NEVER_CHECK_COMMON_NAME**

​	OpenSSL 库是否具有对不检测目标通用名称的内置支持且 [`SSLContext.hostname_checks_common_name`]({{< ref "/library/ipc/ssl#ssl.SSLContext.hostname_checks_common_name" >}}) 为可写状态。

> Added in version 3.7.
>

## ssl.**HAS_ECDH**

​	OpenSSL 库是否具有对基于椭圆曲线的 Diffie-Hellman 密钥交换的内置支持。 此常量应当为真值，除非发布者明确地禁用了此功能。

> Added in version 3.3.
>

## ssl.**HAS_SNI**

​	OpenSSL 库是否具有对 *服务器名称提示* 扩展（在 [**RFC 6066**](https://datatracker.ietf.org/doc/html/rfc6066.html) 中定义）的内置支持。

> Added in version 3.2.
>

## ssl.**HAS_NPN**

​	OpenSSL 库是否具有对 [应用层协议协商](https://en.wikipedia.org/wiki/Application-Layer_Protocol_Negotiation) 中描述的 *下一协议协商* 的内置支持。 当此常量为真值时，你可以使用 [`SSLContext.set_npn_protocols()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.set_npn_protocols" >}}) 方法来公告你想要支持的协议。

> Added in version 3.3.
>

## ssl.**HAS_SSLv2**

​	OpenSSL 库是否具有对 SSL 2.0 协议的内置支持。

> Added in version 3.7.
>

## ssl.**HAS_SSLv3**

​	OpenSSL 库是否具有对 SSL 3.0 协议的内置支持。

> Added in version 3.7.
>

## ssl.**HAS_TLSv1**

​	OpenSSL 库是否具有对 TLS 1.0 协议的内置支持。

> Added in version 3.7.
>

## ssl.**HAS_TLSv1_1**

​	OpenSSL 库是否具有对 TLS 1.1 协议的内置支持。

> Added in version 3.7.
>

## ssl.**HAS_TLSv1_2**

​	OpenSSL 库是否具有对 TLS 1.2 协议的内置支持。

> Added in version 3.7.
>

## ssl.**HAS_TLSv1_3**

​	OpenSSL 库是否具有对 TLS 1.3 协议的内置支持。

> Added in version 3.7.
>

## ssl.**HAS_PSK**

​	OpenSSL 库是否具有对 TLS-PSK 的内置支持。

> Added in version 3.13.
>

## ssl.**CHANNEL_BINDING_TYPES**

​	受支持的 TLS 通道绑定类型组成的列表。 此列表中的字符串可被用作传给 [`SSLSocket.get_channel_binding()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.get_channel_binding" >}}) 的参数。

> Added in version 3.3.
>

## ssl.**OPENSSL_VERSION**

​	解释器所加载的 OpenSSL 库的版本字符串:



``` python
>>> ssl.OPENSSL_VERSION
'OpenSSL 1.0.2k  26 Jan 2017'
```

> Added in version 3.2.
>

## ssl.**OPENSSL_VERSION_INFO**

​	代表 OpenSSL 库的版本信息的五个整数所组成的元组:



``` python
>>> ssl.OPENSSL_VERSION_INFO
(1, 0, 2, 11, 15)
```

> Added in version 3.2.
>

## ssl.**OPENSSL_VERSION_NUMBER**

​	OpenSSL 库的原始版本号，以单个整数表示:



``` python
>>> ssl.OPENSSL_VERSION_NUMBER
268443839
>>> hex(ssl.OPENSSL_VERSION_NUMBER)
'0x100020bf'
```

> Added in version 3.2.
>

## ssl.**ALERT_DESCRIPTION_HANDSHAKE_FAILURE**

## ssl.**ALERT_DESCRIPTION_INTERNAL_ERROR**

## **ALERT_DESCRIPTION_\***

​	来自 [**RFC 5246**](https://datatracker.ietf.org/doc/html/rfc5246.html) 等文档的警报描述。 [IANA TLS Alert Registry](https://www.iana.org/assignments/tls-parameters/tls-parameters.xml#tls-parameters-6) 中包含了这个列表及对定义其含义的 RFC 引用。

​	被用作 [`SSLContext.set_servername_callback()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.set_servername_callback" >}}) 中的回调函数的返回值。

> Added in version 3.4.
>

## *class* ssl.**AlertDescription**

​	ALERT_DESCRIPTION_* 常量的 [`enum.IntEnum`]({{< ref "/library/datatypes/enum#enum.IntEnum" >}}) 多项集。

> Added in version 3.6.
>

## Purpose.**SERVER_AUTH**

​	用于 [`create_default_context()`]({{< ref "/library/ipc/ssl#ssl.create_default_context" >}}) 和 [`SSLContext.load_default_certs()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.load_default_certs" >}}) 的参数。表示上下文可用于验证网络服务器（因此，它将被用于创建客户端套接字）。

> Added in version 3.4.
>

## Purpose.**CLIENT_AUTH**

​	用于 [`create_default_context()`]({{< ref "/library/ipc/ssl#ssl.create_default_context" >}}) 和 [`SSLContext.load_default_certs()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.load_default_certs" >}}) 的参数。 表示上下文可用于验证网络客户（因此，它将被用于创建服务器端套接字）。

> Added in version 3.4.
>

## *class* ssl.**SSLErrorNumber**

​	SSL_ERROR_* 常量的 [`enum.IntEnum`]({{< ref "/library/datatypes/enum#enum.IntEnum" >}}) 多项集。

> Added in version 3.6.
>

## *class* ssl.**TLSVersion**

[`SSLContext.maximum_version`]({{< ref "/library/ipc/ssl#ssl.SSLContext.maximum_version" >}}) 和 [`SSLContext.minimum_version`]({{< ref "/library/ipc/ssl#ssl.SSLContext.minimum_version" >}}) 中的 SSL 和 TLS 版本的 [`enum.IntEnum`]({{< ref "/library/datatypes/enum#enum.IntEnum" >}}) 多项集。

> Added in version 3.7.
>

## TLSVersion.**MINIMUM_SUPPORTED**

## TLSVersion.**MAXIMUM_SUPPORTED**

​	受支持的最低和最高 SSL 或 TLS 版本。 这些常量被称为魔术常量。 它们的值并不反映可用的最低和最高 TLS/SSL 版本。

## TLSVersion.**SSLv3**

## TLSVersion.**TLSv1**

## TLSVersion.**TLSv1_1**

## TLSVersion.**TLSv1_2**

## TLSVersion.**TLSv1_3**

​	SSL 3.0 至 TLS 1.3。

> 自 3.10 版本弃用: 所有 [`TLSVersion`]({{< ref "/library/ipc/ssl#ssl.TLSVersion" >}}) 成员，除 [`TLSVersion.TLSv1_2`]({{< ref "/library/ipc/ssl#ssl.TLSVersion.TLSv1_2" >}}) 和 [`TLSVersion.TLSv1_3`]({{< ref "/library/ipc/ssl#ssl.TLSVersion.TLSv1_3" >}}) 之外均已废弃。

## SSL 套接字

## *class* ssl.**SSLSocket**(*socket.socket*)

​	SSL 套接字提供了 [套接字对象]({{< ref "/library/ipc/socket#socket-objects" >}}) 的下列方法:

- [`accept()`]({{< ref "/library/ipc/socket#socket.socket.accept" >}})
- [`bind()`]({{< ref "/library/ipc/socket#socket.socket.bind" >}})
- [`close()`]({{< ref "/library/ipc/socket#socket.socket.close" >}})
- [`connect()`]({{< ref "/library/ipc/socket#socket.socket.connect" >}})
- [`detach()`]({{< ref "/library/ipc/socket#socket.socket.detach" >}})
- [`fileno()`]({{< ref "/library/ipc/socket#socket.socket.fileno" >}})
- [`getpeername()`]({{< ref "/library/ipc/socket#socket.socket.getpeername" >}}), [`getsockname()`]({{< ref "/library/ipc/socket#socket.socket.getsockname" >}})
- [`getsockopt()`]({{< ref "/library/ipc/socket#socket.socket.getsockopt" >}}), [`setsockopt()`]({{< ref "/library/ipc/socket#socket.socket.setsockopt" >}})
- [`gettimeout()`]({{< ref "/library/ipc/socket#socket.socket.gettimeout" >}}), [`settimeout()`]({{< ref "/library/ipc/socket#socket.socket.settimeout" >}}), [`setblocking()`]({{< ref "/library/ipc/socket#socket.socket.setblocking" >}})
- [`listen()`]({{< ref "/library/ipc/socket#socket.socket.listen" >}})
- [`makefile()`]({{< ref "/library/ipc/socket#socket.socket.makefile" >}})
- [`recv()`]({{< ref "/library/ipc/socket#socket.socket.recv" >}}), [`recv_into()`]({{< ref "/library/ipc/socket#socket.socket.recv_into" >}}) (但不允许传入非零的 `flags` 参数)
- [`send()`]({{< ref "/library/ipc/socket#socket.socket.send" >}}), [`sendall()`]({{< ref "/library/ipc/socket#socket.socket.sendall" >}}) (具有同样的限制)
- [`sendfile()`]({{< ref "/library/ipc/socket#socket.socket.sendfile" >}}) (但 [`os.sendfile`]({{< ref "/library/allos/os#os.sendfile" >}}) 将仅用于纯文本套接字，在其他情况下将使用 [`send()`]({{< ref "/library/ipc/socket#socket.socket.send" >}}))
- [`shutdown()`]({{< ref "/library/ipc/socket#socket.socket.shutdown" >}})

​	但是，由于 SSL（和 TLS）协议在 TCP 之上具有自己的框架，因此 SSL 套接字抽象在某些方面可能与常规的 OS 层级套接字存在差异。 特别是要查看 [非阻塞型套接字说明]({{< ref "/library/ipc/ssl#ssl-nonblocking" >}})。

[`SSLSocket`]({{< ref "/library/ipc/ssl#ssl.SSLSocket" >}}) 的实例必须使用 [`SSLContext.wrap_socket()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.wrap_socket" >}}) 方法来创建。

> 在 3.5 版本发生变更: 新增了 `sendfile()` 方法。

> 在 3.5 版本发生变更: `shutdown()` 不会在每次接收或发送字节数据后重置套接字超时。 现在套接字超时为关闭的最大总持续时间。

> 自 3.6 版本弃用: 直接创建 [`SSLSocket`]({{< ref "/library/ipc/ssl#ssl.SSLSocket" >}}) 实例的做法已被弃用，请使用 [`SSLContext.wrap_socket()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.wrap_socket" >}}) 来包装套接字。

> 在 3.7 版本发生变更: [`SSLSocket`]({{< ref "/library/ipc/ssl#ssl.SSLSocket" >}}) 的实例必须使用 [`wrap_socket()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.wrap_socket" >}}) 来创建。 在较早的版本中，直接创建实例是可能的。 但这从未被记入文档或是被正式支持。

> 在 3.10 版本发生变更: Python 内部现在使用 `SSL_read_ex` 和 `SSL_write_ex`。这些函数支持读取和写入大于 2GB 的数据。写入零长数据不再出现违反协议的错误。

​	SSL 套接字还具有下列方法和属性:

## SSLSocket.**read**(*len=1024*, *buffer=None*)

​	从 SSL 套接字读取至多 *len* 个字节的数据并将结果作为 `bytes` 实例返回。 如果指定了 *buffer*，则改为读取到缓冲区，并返回所读取的字节数。

​	如果套接字为 [非阻塞型]({{< ref "/library/ipc/ssl#ssl-nonblocking" >}}) 则会引发 [`SSLWantReadError`]({{< ref "/library/ipc/ssl#ssl.SSLWantReadError" >}}) 或 [`SSLWantWriteError`]({{< ref "/library/ipc/ssl#ssl.SSLWantWriteError" >}}) 且读取将阻塞。

​	由于在任何时候重新协商都是可能的，因此调用 [`read()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.read" >}}) 也可能导致写入操作。

> 在 3.5 版本发生变更: 套接字超时在每次接收或发送字节数据后不会再被重置。 现在套接字超时为读取至多 *len* 个字节数据的最大总持续时间。

> 自 3.6 版本弃用: 请使用 `recv()` 来代替 [`read()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.read" >}})。

## SSLSocket.**write**(*buf*)

​	将 *buf* 写入到 SSL 套接字并返回所写入的字节数。 *buf* 参数必须为支持缓冲区接口的对象。

​	如果套接字为 [非阻塞型]({{< ref "/library/ipc/ssl#ssl-nonblocking" >}}) 则会引发 [`SSLWantReadError`]({{< ref "/library/ipc/ssl#ssl.SSLWantReadError" >}}) 或 [`SSLWantWriteError`]({{< ref "/library/ipc/ssl#ssl.SSLWantWriteError" >}}) 且读取将阻塞。

​	由于在任何时候重新协商都是可能的，因此调用 [`write()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.write" >}}) 也可能导致读取操作。

> 在 3.5 版本发生变更: 套接字超时在每次接收或发送字节数据后不会再被重置。 现在套接字超时为写入 *buf* 的最大总持续时间。

> 自 3.6 版本弃用: 请使用 `send()` 来代替 [`write()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.write" >}})。

​备注
 

[`read()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.read" >}}) 和 [`write()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.write" >}}) 方法是读写未加密的应用级数据，并将其解密/加密为带加密的线路级数据的低层级方法。 这些方法需要有激活的 SSL 连接，即握手已完成而 [`SSLSocket.unwrap()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.unwrap" >}}) 尚未被调用。

​	通常你应当使用套接字 API 方法例如 [`recv()`]({{< ref "/library/ipc/socket#socket.socket.recv" >}}) 和 [`send()`]({{< ref "/library/ipc/socket#socket.socket.send" >}}) 来代替这些方法。

## SSLSocket.**do_handshake**()

​	执行 SSL 设置握手。

> 在 3.4 版本发生变更: 当套接字的 [`context`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.context" >}}) 的 [`check_hostname`]({{< ref "/library/ipc/ssl#ssl.SSLContext.check_hostname" >}}) 属性为真值时此握手方法还会执行 `match_hostname()`。

> 在 3.5 版本发生变更: 套接字超时在每次接收或发送字节数据时不会再被重置。 现在套接字超时为握手的最大总持续时间。

> 在 3.7 版本发生变更: 主机名或 IP 地址会在握手期间由 OpenSSL 进行匹配。 函数 `match_hostname()` 不再被使用。 在 OpenSSL 拒绝主机名或 IP 地址的情况下，握手将提前被中止并向对等方发送 TLS 警告消息。

## SSLSocket.**getpeercert**(*binary_form=False*)

​	如果连接另一端的对等方没有证书，则返回 `None`。 如果 SSL 握手还未完成，则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

​	如果 `binary_form` 形参为 [`False`]({{< ref "/library/constants#False" >}})，并且从对等方接收到了证书，此方法将返回一个 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 实例。 如果证书未通过验证，则字典将为空。 如果证书通过验证，它将返回由多个密钥组成的字典，其中包括 `subject` (证书颁发给的主体) 和 `issuer` (颁发证书的主体)。 如果证书包含一个 *Subject Alternative Name* 扩展的实例 (see [**RFC 3280**](https://datatracker.ietf.org/doc/html/rfc3280.html))，则字典中还将有一个 `subjectAltName` 键。

`subject` 和 `issuer` 字段都是包含在证书中相应字段的数据结构中给出的相对专有名称（RDN）序列的元组，每个 RDN 均为 name-value 对的序列。 这里是一个实际的示例:

```
{'issuer': ((('countryName', 'IL'),),
            (('organizationName', 'StartCom Ltd.'),),
            (('organizationalUnitName',
              'Secure Digital Certificate Signing'),),
            (('commonName',
              'StartCom Class 2 Primary Intermediate Server CA'),)),
 'notAfter': 'Nov 22 08:15:19 2013 GMT',
 'notBefore': 'Nov 21 03:09:52 2011 GMT',
 'serialNumber': '95F0',
 'subject': ((('description', '571208-SLe257oHY9fVQ07Z'),),
             (('countryName', 'US'),),
             (('stateOrProvinceName', 'California'),),
             (('localityName', 'San Francisco'),),
             (('organizationName', 'Electronic Frontier Foundation, Inc.'),),
             (('commonName', '*.eff.org'),),
             (('emailAddress', 'hostmaster@eff.org'),)),
 'subjectAltName': (('DNS', '*.eff.org'), ('DNS', 'eff.org')),
 'version': 3}
```

​	如果 `binary_form` 形参为 [`True`]({{< ref "/library/constants#True" >}})，并且提供了证书，此方法会将整个证书的 DER 编码形式作为字节序列返回，或者如果对等方未提供证书则返回 [`None`]({{< ref "/library/constants#None" >}})。 对等方是否提供证书取决于 SSL 套接字的角色:

- 对于客户端 SSL 套接字，服务器将总是提供证书，无论是否需要进行验证;
- 对于服务器 SSL 套接字，客户端将仅在服务器要求时才提供证书；因此如果你使用了 [`CERT_NONE`]({{< ref "/library/ipc/ssl#ssl.CERT_NONE" >}}) (而不是 [`CERT_OPTIONAL`]({{< ref "/library/ipc/ssl#ssl.CERT_OPTIONAL" >}}) 或 [`CERT_REQUIRED`]({{< ref "/library/ipc/ssl#ssl.CERT_REQUIRED" >}})) 则 [`getpeercert()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.getpeercert" >}}) 将返回 [`None`]({{< ref "/library/constants#None" >}})。

​	另请参阅 [`SSLContext.check_hostname`]({{< ref "/library/ipc/ssl#ssl.SSLContext.check_hostname" >}})。

> 在 3.2 版本发生变更: 返回的字典包括额外的条目例如 `issuer` 和 `notBefore`。

> 在 3.4 版本发生变更: 如果握手未完成则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。 返回的字典包括额外的 X509v3 扩展条目例如 `crlDistributionPoints`, `caIssuers` 和 `OCSP` URI。

> 在 3.9 版本发生变更: IPv6 地址字符串不再附带末尾换行符。

## SSLSocket.**get_verified_chain**()

​	返回由 SSL 通道另一端以 DER 编码字节列表形式提供的经过验证的证书链。 如果证书验证被禁用则此方法的行为与 [`get_unverified_chain()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.get_unverified_chain" >}}) 相同。

> Added in version 3.13.
>

## SSLSocket.**get_unverified_chain**()

​	返回由 SSL 通道另一端以 DER 编码字节列表形式提供的原始证书链。

> Added in version 3.13.
>

## SSLSocket.**cipher**()

​	返回由三个值组成的元组，其中包含所使用的密码名称，定义其使用方式的 SSL 协议版本，以及所使用的加密比特位数。 如果尚未建立连接，则返回 `None`。

## SSLSocket.**shared_ciphers**()

​	返回在客户端和服务器均可用的密码列表。 所返回列表的每个条目都是由三个值组成的元组其中包含密码名称、定义其使用方式的 SSL 协议版本，以及密码所使用的加密比特位数量。 如果连接尚未建立或套接字为客户端套接字则 [`shared_ciphers()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.shared_ciphers" >}}) 将返回 `None`。

> Added in version 3.5.
>

## SSLSocket.**compression**()

​	以字符串形式返回所使用的压缩算法，或者如果连接没有使用压缩则返回 `None`。

​	如果高层级的协议支持自己的压缩机制，你可以使用 [`OP_NO_COMPRESSION`]({{< ref "/library/ipc/ssl#ssl.OP_NO_COMPRESSION" >}}) 来禁用 SSL 层级的压缩。

> Added in version 3.3.
>

## SSLSocket.**get_channel_binding**(*cb_type='tls-unique'*)

​	为当前连接获取字节串形式的通道绑定数据。 如果尚未连接或握手尚未完成则返回 `None`。

*cb_type* 形参允许选择需要的通道绑定类型。 有效的通道绑定类型在 [`CHANNEL_BINDING_TYPES`]({{< ref "/library/ipc/ssl#ssl.CHANNEL_BINDING_TYPES" >}}) 列表中列出。 目前只支持由 [**RFC 5929**](https://datatracker.ietf.org/doc/html/rfc5929.html) 所定义的 'tls-unique' 通道绑定。 如果请求了一个不受支持的通道绑定类型则将引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

> Added in version 3.3.
>

## SSLSocket.**selected_alpn_protocol**()

​	返回在 TLS 握手期间所选择的协议。 如果 [`SSLContext.set_alpn_protocols()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.set_alpn_protocols" >}}) 未被调用，如果另一方不支持 ALPN，如果此套接字不支持任何客户端所用的协议，或者如果握手尚未发生，则将返回 `None`。

> Added in version 3.5.
>

## SSLSocket.**selected_npn_protocol**()

​	返回在Return the higher-level protocol that was selected during the TLS/SSL 握手期间所选择的高层级协议。 如果 [`SSLContext.set_npn_protocols()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.set_npn_protocols" >}}) 未被调用，或者如果另一方不支持 NPN，或者如果握手尚未发生，则将返回 `None`。

> Added in version 3.3.
>

> 自 3.10 版本弃用: NPN 已被 ALPN 取代。

## SSLSocket.**unwrap**()

​	执行 SSL 关闭握手，这会从下层的套接字中移除 TLS 层，并返回下层的套接字对象。 这可被用来通过一个连接将加密操作转为非加密。 返回的套接字应当总是被用于同连接另一方的进一步通信，而不是原始的套接字。

## SSLSocket.**verify_client_post_handshake**()

​	向一个 TLS 1.3 客户端请求握手后身份验证（PHA）。 只有在初始 TLS 握手之后且双方都启用了 PHA 的情况下才能为服务器端套接字的 TLS 1.3 连接启用 PHA，参见 [`SSLContext.post_handshake_auth`]({{< ref "/library/ipc/ssl#ssl.SSLContext.post_handshake_auth" >}})。

​	此方法不会立即执行证书交换。 服务器端会在下一次写入事件期间发送 CertificateRequest 并期待客户端在下一次读取事件期间附带证书进行响应。

​	如果有任何前置条件未被满足（例如非 TLS 1.3，PHA 未启用），则会引发 [`SSLError`]({{< ref "/library/ipc/ssl#ssl.SSLError" >}})。

​备注
 

​	仅在 OpenSSL 1.1.1 且 TLS 1.3 被启用时可用。 没有 TLS 1.3 支持，此方法将引发 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}})。

> Added in version 3.8.
>

## SSLSocket.**version**()

​	以字符串形式返回由连接协商确定的实际 SSL 协议版本，或者如果未建立安全连接则返回 `None`。 在撰写本文档时，可能的返回值包括 `"SSLv2"`, `"SSLv3"`, `"TLSv1"`, `"TLSv1.1"` 和 `"TLSv1.2"`。 最新的 OpenSSL 版本可能会定义更多的返回值。

> Added in version 3.5.
>

## SSLSocket.**pending**()

​	返回在连接上等待被读取的已解密字节数。

## SSLSocket.**context**

​	该 SSL 套接字所关联的 [`SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 对象。

> Added in version 3.2.
>

## SSLSocket.**server_side**

​	一个布尔值，对于服务器端套接字为 `True` 而对于客户端套接字则为 `False`。

> Added in version 3.2.
>

## SSLSocket.**server_hostname**

​	服务器的主机名: [`str`]({{< ref "/library/stdtypes#str" >}}) 类型，对于服务器端套接字或者如果构造器中未指定主机名则为 `None`。

> Added in version 3.2.
>

> 在 3.7 版本发生变更: 现在该属性将始终为 ASCII 文本。 当 `server_hostname` 为一个国际化域名（IDN）时，该属性现在会保存为 A 标签形式 (`"xn--pythn-mua.org"`) 而非 U 标签形式 (`"pythön.org"`)。

## SSLSocket.**session**

​	用于 SSL 连接的 [`SSLSession`]({{< ref "/library/ipc/ssl#ssl.SSLSession" >}})。 该会话将在执行 TLS 握手后对客户端和服务器端套接字可用。 对于客户端套接字该会话可以在调用 [`do_handshake()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.do_handshake" >}}) 之前被设置以重用一个会话。

> Added in version 3.6.
>

## SSLSocket.**session_reused**

> Added in version 3.6.
>

## SSL 上下文

> Added in version 3.2.
>

​	SSL 上下文可保存各种比单独 SSL 连接寿命更长的数据，例如 SSL 配置选项，证书和私钥等。 它还可为服务器端套接字管理缓存，以加快来自相同客户端的重复连接。

## *class* ssl.**SSLContext**(*protocol=None*)

​	创建一个新的 SSL 上下文。 你可以传入 *protocol*，它必须为此模块中定义的 `PROTOCOL_*` 常量之一。 该形参指定要使用哪个 SSL 协议版本。 通常，服务器会选择一个特定的协议版本，而客户端必须适应服务器的选择。 大多数版本都不能与其他版本互操作。 如果未指定，则默认值为 [`PROTOCOL_TLS`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS" >}})；它提供了与其他版本的最大兼容性。

​	这个表显示了客户端（横向）的哪个版本能够连接服务器（纵向）的哪个版本。

| *客户端* / **服务器**                                        | **SSLv2**                                                    | **SSLv3**                                                    | **TLS** [[3\]]({{< ref "/library/ipc/ssl#id9" >}}) | **TLSv1** | **TLSv1.1** | **TLSv1.2** |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | --------- | ----------- | ----------- |
| *SSLv2*                                                      | 是                                                           | 否                                                           | 否 [[1\]]({{< ref "/library/ipc/ssl#id7" >}}) | 否        | 否          | 否          |
| *SSLv3*                                                      | 否                                                           | 是                                                           | 否 [[2\]]({{< ref "/library/ipc/ssl#id8" >}}) | 否        | 否          | 否          |
| *TLS* (*SSLv23*) [[3\]]({{< ref "/library/ipc/ssl#id9" >}}) | 否 [[1\]]({{< ref "/library/ipc/ssl#id7" >}}) | 否 [[2\]]({{< ref "/library/ipc/ssl#id8" >}}) | 是                                                           | 是        | 是          | 是          |
| *TLSv1*                                                      | 否                                                           | 否                                                           | 是                                                           | 是        | 否          | 否          |
| *TLSv1.1*                                                    | 否                                                           | 否                                                           | 是                                                           | 否        | 是          | 否          |
| *TLSv1.2*                                                    | 否                                                           | 否                                                           | 是                                                           | 否        | 否          | 是          |

​备注
[1]([1](https://docs.python.org/zh-cn/3.13/library/ssl.html#id2),[2]({{< ref "/library/ipc/ssl#id5" >}}))

[`SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 默认设置 [`OP_NO_SSLv2`]({{< ref "/library/ipc/ssl#ssl.OP_NO_SSLv2" >}}) 以禁用 SSLv2。

[2]([1](https://docs.python.org/zh-cn/3.13/library/ssl.html#id3),[2]({{< ref "/library/ipc/ssl#id6" >}}))

[`SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 默认设置 [`OP_NO_SSLv3`]({{< ref "/library/ipc/ssl#ssl.OP_NO_SSLv3" >}}) 以禁用 SSLv3。

[3]([1](https://docs.python.org/zh-cn/3.13/library/ssl.html#id1),[2]({{< ref "/library/ipc/ssl#id4" >}}))

​	TLS 1.3 协议在 OpenSSL >= 1.1.1 中设置 [`PROTOCOL_TLS`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS" >}}) 时可用。 没有专门针对 TLS 1.3 的 PROTOCOL 常量。

​参见
 

[`create_default_context()`]({{< ref "/library/ipc/ssl#ssl.create_default_context" >}}) 让 [`ssl`]({{< ref "/library/ipc/ssl#module-ssl" >}}) 为特定目标选择安全设置。

> 在 3.6 版本发生变更: 上下文会使用安全的默认值来创建。 默认设置的选项有 [`OP_NO_COMPRESSION`]({{< ref "/library/ipc/ssl#ssl.OP_NO_COMPRESSION" >}}), [`OP_CIPHER_SERVER_PREFERENCE`]({{< ref "/library/ipc/ssl#ssl.OP_CIPHER_SERVER_PREFERENCE" >}}), [`OP_SINGLE_DH_USE`]({{< ref "/library/ipc/ssl#ssl.OP_SINGLE_DH_USE" >}}), [`OP_SINGLE_ECDH_USE`]({{< ref "/library/ipc/ssl#ssl.OP_SINGLE_ECDH_USE" >}}), [`OP_NO_SSLv2`]({{< ref "/library/ipc/ssl#ssl.OP_NO_SSLv2" >}}) 和 [`OP_NO_SSLv3`]({{< ref "/library/ipc/ssl#ssl.OP_NO_SSLv3" >}}) ([`PROTOCOL_SSLv3`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_SSLv3" >}}) 除外)。 初始密码套件列表只包含 `HIGH` 密码，而不包含 `NULL` 密码和 `MD5` 密码。

> 自 3.10 版本弃用: 不带协议参数的 [`SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 已废弃。将来，上下文类会要求使用 [`PROTOCOL_TLS_CLIENT`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS_CLIENT" >}}) 或 [`PROTOCOL_TLS_SERVER`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS_SERVER" >}}) 协议。

> 在 3.10 版本发生变更: 现在默认的密码套件只包含安全的 AES 和 ChaCha20 密码，具有前向保密性和安全级别2。禁止使用少于 2048 位的 RSA 和 DH 密钥以及少于 224 位的ECC密钥。 [`PROTOCOL_TLS`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS" >}}) 、 [`PROTOCOL_TLS_CLIENT`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS_CLIENT" >}}) 和 [`PROTOCOL_TLS_SERVER`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS_SERVER" >}}) 至少使用 TLS 1.2 版本。

​备注
 

[`SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 一旦被某个连接使用它将只支持有限的变异。 在内部信任存储中添加新证书是允许的，但是更改密码、验证设置或 mTLS 证书则可能导致令人吃惊的行为。

​备注
 

[`SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 被设计为可由多个连接共享和使用。 因此，只要在被某个连接使用后不重新配置那么它就是线程安全的。

[`SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 对象具有以下方法和属性:

## SSLContext.**cert_store_stats**()

​	获取以字典表示的有关已加载的 X.509 证书数量，被标记为 CA 证书的 X.509 证书数量以及证书吊销列表的统计信息。

​	具有一个 CA 证书和一个其他证书的上下文示例:



``` python
>>> context.cert_store_stats()
{'crl': 0, 'x509_ca': 1, 'x509': 2}
```

> Added in version 3.4.
>

## SSLContext.**load_cert_chain**(*certfile*, *keyfile=None*, *password=None*)

​	加载一个私钥及对应的证书。 *certfile* 字符串必须为以 PEM 格式表示的单个文件路径，该文件中包含证书以及确立证书真实性所需的任意数量的 CA 证书。 如果存在 *keyfile* 字符串，它必须指向一个包含私钥的文件。 否则私钥也将从 *certfile* 中提取。 请参阅 [证书]({{< ref "/library/ipc/ssl#ssl-certificates" >}}) 中的讨论来了解有关如何将证书存储至 *certfile* 的更多信息。

*password* 参数可以是一个函数，调用时将得到用于解密私钥的密码。 它在私钥被加密且需要密码时才会被调用。 它调用时将不带任何参数，并且应当返回一个字符串、字节串或字节数组。 如果返回值是一个字符串，在用它解密私钥之前它将以 UTF-8 进行编码。 或者也可以直接将字符串、字节串或字节数组值作为 *password* 参数提供。 如果私钥未被加密且不需要密码则它将被忽略。

​	如果未指定 *password* 参数且需要一个密码，将会使用 OpenSSL 内置的密码提示机制来交互式地提示用户输入密码。

​	如果私钥不能匹配证书则会引发 [`SSLError`]({{< ref "/library/ipc/ssl#ssl.SSLError" >}})。

> 在 3.3 版本发生变更: 新增可选参数 *password*。

## SSLContext.**load_default_certs**(*purpose=Purpose.SERVER_AUTH*)

​	从默认位置加载一组默认的 "证书颁发机构" (CA) 证书。 在 Windows 上它将从 `CA` 和 `ROOT` 系统存储中加载 CA 证书。 在所有系统上它会调用 [`SSLContext.set_default_verify_paths()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.set_default_verify_paths" >}}) 。 将来该方法也可能会从其他位置加载 CA 证书。

*purpose* 旗标指明要加载哪种 CA 证书。 默认设置 [`Purpose.SERVER_AUTH`]({{< ref "/library/ipc/ssl#ssl.Purpose.SERVER_AUTH" >}}) 将加载被标记且被信任用于 TLS Web 服务器验证（客户端套接字）的证书。 [`Purpose.CLIENT_AUTH`]({{< ref "/library/ipc/ssl#ssl.Purpose.CLIENT_AUTH" >}}) 则会加载用于在服务器端进行客户端证书验证的 CA 证书。

> Added in version 3.4.
>

## SSLContext.**load_verify_locations**(*cafile=None*, *capath=None*, *cadata=None*)

​	当 [`verify_mode`]({{< ref "/library/ipc/ssl#ssl.SSLContext.verify_mode" >}}) 不为 [`CERT_NONE`]({{< ref "/library/ipc/ssl#ssl.CERT_NONE" >}}) 时加载一组用于验证其他对等方证书的 "证书颁发机构" (CA) 证书。 必须至少指定 *cafile* 或 *capath* 中的一个。

​	此方法还可加载 PEM 或 DER 格式的证书吊销列表 (CRL)，为此必须正确配置 [`SSLContext.verify_flags`]({{< ref "/library/ipc/ssl#ssl.SSLContext.verify_flags" >}})。

​	如果存在 *cafile* 字符串，它应为 PEM 格式的级联 CA 证书文件的路径。 请参阅 [证书]({{< ref "/library/ipc/ssl#ssl-certificates" >}}) 中的讨论来了解有关如何处理此文件中的证书的更多信息。

​	The *capath* string, if present, is the path to a directory containing several CA certificates in PEM format, following an [OpenSSL specific layout](https://docs.openssl.org/master/man3/SSL_CTX_load_verify_locations/).

​	如果存在 *cadata* 对象，它应为一个或多个 PEM 编码的证书的 ASCII 字符串或者 DER 编码的证书的 [bytes-like object]({{< ref "/glossary/idx#term-bytes-like-object" >}})。 与 *capath* 一样 PEM 编码的证书之外的多余行会被忽略，但至少要有一个证书。

> 在 3.4 版本发生变更: 新增可选参数 *cadata*

## SSLContext.**get_ca_certs**(*binary_form=False*)

​	获取已离开法人 "证书颁发机构" (CA) 证书列表。 如果 `binary_form` 形参为 [`False`]({{< ref "/library/constants#False" >}}) 则每个列表条目都是一个类似于 [`SSLSocket.getpeercert()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.getpeercert" >}}) 输出的字典。 在其他情况下此方法将返回一个 DER 编码的证书的列表。 返回的列表不包含来自 *capath* 的证书，除非 SSL 连接请求并加载了一个证书。

​备注
 

​	capath 目录中的证书不会被加载，除非它们已至少被使用过一次。

> Added in version 3.4.
>

## SSLContext.**get_ciphers**()

​	获取已启用密码的列表。 该列表将按密码的优先级排序。 参见 [`SSLContext.set_ciphers()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.set_ciphers" >}})。

​	示例:



``` python
>>> ctx = ssl.SSLContext(ssl.PROTOCOL_SSLv23)
>>> ctx.set_ciphers('ECDHE+AESGCM:!ECDSA')
>>> ctx.get_ciphers()
[{'aead': True,
  'alg_bits': 256,
  'auth': 'auth-rsa',
  'description': 'ECDHE-RSA-AES256-GCM-SHA384 TLSv1.2 Kx=ECDH     Au=RSA  '
                 'Enc=AESGCM(256) Mac=AEAD',
  'digest': None,
  'id': 50380848,
  'kea': 'kx-ecdhe',
  'name': 'ECDHE-RSA-AES256-GCM-SHA384',
  'protocol': 'TLSv1.2',
  'strength_bits': 256,
  'symmetric': 'aes-256-gcm'},
 {'aead': True,
  'alg_bits': 128,
  'auth': 'auth-rsa',
  'description': 'ECDHE-RSA-AES128-GCM-SHA256 TLSv1.2 Kx=ECDH     Au=RSA  '
                 'Enc=AESGCM(128) Mac=AEAD',
  'digest': None,
  'id': 50380847,
  'kea': 'kx-ecdhe',
  'name': 'ECDHE-RSA-AES128-GCM-SHA256',
  'protocol': 'TLSv1.2',
  'strength_bits': 128,
  'symmetric': 'aes-128-gcm'}]
```

> Added in version 3.6.
>

## SSLContext.**set_default_verify_paths**()

​	从构建 OpenSSL 库时定义的文件系统路径中加载一组默认的 "证书颁发机构" (CA) 证书。 不幸的是，没有一种简单的方式能知道此方法是否执行成功：如果未找到任何证书也不会返回错误。 不过，当 OpenSSL 库是作为操作系统的一部分被提供时，它的配置应当是正确的。

## SSLContext.**set_ciphers**(*ciphers*)

​	Set the available ciphers for sockets created with this context. It should be a string in the [OpenSSL cipher list format](https://docs.openssl.org/master/man1/ciphers/). If no cipher can be selected (because compile-time options or other configuration forbids use of all the specified ciphers), an [`SSLError`]({{< ref "/library/ipc/ssl#ssl.SSLError" >}}) will be raised.

​备注
 

​	在连接后，SSL 套接字的 [`SSLSocket.cipher()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.cipher" >}}) 方法将给出当前所选择的密码。

​	TLS 1.3 密码套件不能通过 [`set_ciphers()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.set_ciphers" >}}) 禁用。

## SSLContext.**set_alpn_protocols**(*protocols*)

​	指定在 SSL/TLS 握手期间套接字应当通告的协议。 它应为由 ASCII 字符串组成的列表，例如 `['http/1.1', 'spdy/2']`，按首选顺序排列。 协议的选择将在握手期间发生，并依据 [**RFC 7301**](https://datatracker.ietf.org/doc/html/rfc7301.html) 来执行。 在握手成功后，[`SSLSocket.selected_alpn_protocol()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.selected_alpn_protocol" >}}) 方法将返回已达成一致的协议。

​	如果 [`HAS_ALPN`]({{< ref "/library/ipc/ssl#ssl.HAS_ALPN" >}}) 为 `False` 则此方法将引发 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}})。

> Added in version 3.5.
>

## SSLContext.**set_npn_protocols**(*protocols*)

​	指定在Specify which protocols the socket should advertise during the SSL/TLS 握手期间套接字应当通告的协议。 它应为由字符串组成的列表，例如 `['http/1.1', 'spdy/2']`，按首选顺序排列。 协议的选择将在握手期间发生，并将依据 [应用层协议协商](https://en.wikipedia.org/wiki/Application-Layer_Protocol_Negotiation) 来执行。 在握手成功后，[`SSLSocket.selected_npn_protocol()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.selected_npn_protocol" >}}) 方法将返回已达成一致的协议。

​	如果 [`HAS_NPN`]({{< ref "/library/ipc/ssl#ssl.HAS_NPN" >}}) 为 `False` 则此方法将引发 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}})。

> Added in version 3.3.
>

> 自 3.10 版本弃用: NPN 已被 ALPN 取代。

## SSLContext.**sni_callback**

​	注册一个回调函数，当 TLS 客户端指定了一个服务器名称提示时，该回调函数将在 SSL/TLS 服务器接收到 TLS Client Hello 握手消息后被调用。 服务器名称提示机制的定义见 [**RFC 6066**](https://datatracker.ietf.org/doc/html/rfc6066.html) section 3 - Server Name Indication。

​	每个 `SSLContext` 只能设置一个回调。 如果 *sni_callback* 被设置为 `None` 则会禁用回调。 对该函数的后续调用将禁用之前注册的回调。

​	此回调函数将附带三个参数来调用；第一个参数是 [`ssl.SSLSocket`]({{< ref "/library/ipc/ssl#ssl.SSLSocket" >}})，第二个参数是代表客户端准备与之通信的服务器的字符串 (或者如果 TLS Client Hello 不包含服务器名称则为 [`None`]({{< ref "/library/constants#None" >}})) 而第三个参数是原来的 [`SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}})。 服务器名称参数为文本形式。 对于国际化域名，服务器名称是一个 IDN A 标签 (`"xn--pythn-mua.org"`)。

​	此回调的一个典型用法是将 [`ssl.SSLSocket`]({{< ref "/library/ipc/ssl#ssl.SSLSocket" >}}) 的 [`SSLSocket.context`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.context" >}}) 属性修改为一个 [`SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 类型的新对象，该对象代表与服务器相匹配的证书链。

​	由于 TLS 连接处于早期协商阶段，因此仅能使用有限的方法和属性例如 [`SSLSocket.selected_alpn_protocol()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.selected_alpn_protocol" >}}) 和 [`SSLSocket.context`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.context" >}})。 [`SSLSocket.getpeercert()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.getpeercert" >}}), [`SSLSocket.get_verified_chain()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.get_verified_chain" >}}), [`SSLSocket.get_unverified_chain()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.get_unverified_chain" >}}) [`SSLSocket.cipher()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.cipher" >}}) 和 [`SSLSocket.compression()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.compression" >}}) 方法要求 TLS 连接已过 TLS Client Hello 步骤因而不会返回有意义的值也不能安全地调用它们。

*sni_callback* 函数必须返回 `None` 以允许 TLS 协商继续进行。 如果想要 TLS 失败，则可以返回常量 [`ALERT_DESCRIPTION_*`]({{< ref "/library/ipc/ssl#ssl.ALERT_DESCRIPTION_INTERNAL_ERROR" >}})。 其他返回值将导致 TLS 的致命错误 [`ALERT_DESCRIPTION_INTERNAL_ERROR`]({{< ref "/library/ipc/ssl#ssl.ALERT_DESCRIPTION_INTERNAL_ERROR" >}})。

​	如果从 *sni_callback* 函数引发了异常，则 TLS 连接将终止并发出 TLS 致命警告消息 [`ALERT_DESCRIPTION_HANDSHAKE_FAILURE`]({{< ref "/library/ipc/ssl#ssl.ALERT_DESCRIPTION_HANDSHAKE_FAILURE" >}})。

​	如果 OpenSSL library 库在构建时定义了 OPENSSL_NO_TLSEXT 则此方法将返回 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}})。

> Added in version 3.7.
>

## SSLContext.**set_servername_callback**(*server_name_callback*)

​	这是被保留用于向下兼容的旧式 API。 在可能的情况下，你应当改用 [`sni_callback`]({{< ref "/library/ipc/ssl#ssl.SSLContext.sni_callback" >}})。 给出的 *server_name_callback* 类似于 *sni_callback*，不同之处在于当服务器主机名是 IDN 编码的国际化域名时，*server_name_callback* 会接收到一个已编码的 U 标签 (`"pythön.org"`)。

​	如果发生了服务器名称解码错误。 TLS 连接将终止并向客户端发出 [`ALERT_DESCRIPTION_INTERNAL_ERROR`]({{< ref "/library/ipc/ssl#ssl.ALERT_DESCRIPTION_INTERNAL_ERROR" >}}) 最严重 TLS 警告消息。

> Added in version 3.4.
>

## SSLContext.**load_dh_params**(*dhfile*)

​	加载密钥生成参数用于 Diffie-Hellman (DH) 密钥交换。 使用 DH 密钥交换能以消耗（服务器和客户端的）计算资源为代价提升前向保密性。 *dhfile* 参数应当为指向一个包含 PEM 格式的 DH 形参的文件的路径。

​	此设置不会应用于客户端套接字。 你还可以使用 [`OP_SINGLE_DH_USE`]({{< ref "/library/ipc/ssl#ssl.OP_SINGLE_DH_USE" >}}) 选项来进一步提升安全性。

> Added in version 3.3.
>

## SSLContext.**set_ecdh_curve**(*curve_name*)

​	为基于椭圆曲线的 Elliptic Curve-based Diffie-Hellman (ECDH) 密钥交换设置曲线名称。 ECDH 显著快于常规 DH 同时据信同样安全。 *curve_name* 形参应为描述某个知名椭圆曲线的字符串，例如受到广泛支持的曲线 `prime256v1`。

​	此设置不会应用于客户端套接字。 你还可以使用 [`OP_SINGLE_ECDH_USE`]({{< ref "/library/ipc/ssl#ssl.OP_SINGLE_ECDH_USE" >}}) 选项来进一步提升安全性。

​	如果 [`HAS_ECDH`]({{< ref "/library/ipc/ssl#ssl.HAS_ECDH" >}}) 为 `False` 则此方法将不可用。

> Added in version 3.3.
>

​参见
[SSL/TLS & Perfect Forward Secrecy](https://vincent.bernat.ch/en/blog/2011-ssl-perfect-forward-secrecy)

​	Vincent Bernat。

## SSLContext.**wrap_socket**(*sock*, *server_side=False*, *do_handshake_on_connect=True*, *suppress_ragged_eofs=True*, *server_hostname=None*, *session=None*)

​	包装一个现有的 Python 套接字 *sock* 并返回一个 [`SSLContext.sslsocket_class`]({{< ref "/library/ipc/ssl#ssl.SSLContext.sslsocket_class" >}}) 的实例 (默认为 [`SSLSocket`]({{< ref "/library/ipc/ssl#ssl.SSLSocket" >}}))。 返回的 SSL 套接字会关联到相应上下文、设置及证书。 *sock* 必须是一个 [`SOCK_STREAM`]({{< ref "/library/ipc/socket#socket.SOCK_STREAM" >}}) 套接字；其他套接字类型均不受支持。

​	形参 `server_side` 是一个布尔值，它标明希望从该套接字获得服务器端行为还是客户端行为。

​	对于客户端套接字，上下文的构造会延迟执行；如果下层的套接字尚未连接，上下文的构造将在对套接字调用 `connect()` 之后执行。 对于服务器端套接字，如果套接字没有远端对等方，它会被视为一个监听套接字，并且服务器端 SSL 包装操作会在通过 `accept()` 方法所接受的客户端连接上自动执行。 此方法可能会引发 [`SSLError`]({{< ref "/library/ipc/ssl#ssl.SSLError" >}})。

​	在客户端连接上，可选形参 *server_hostname* 指定所要连接的服务的主机名。 这允许单个服务器托管具有单独证书的多个基于 SSL 的服务，很类似于 HTTP 虚拟主机。 如果 *server_side* 为真值则指定 *server_hostname* 将引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

​	形参 `do_handshake_on_connect` 指明是否要在调用 `socket.connect()` 之后自动执行 SSL 握手，还是要通过发起调用 [`SSLSocket.do_handshake()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.do_handshake" >}}) 方法让应用程序显式地调用它。 显式地调用 [`SSLSocket.do_handshake()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.do_handshake" >}}) 可给予程序对握手中所涉及的套接字 I/O 阻塞行为的控制。

​	形参 `suppress_ragged_eofs` 指明 `SSLSocket.recv()` 方法应当如何从连接的另一端发送非预期的 EOF 信号。 如果指定为 [`True`]({{< ref "/library/constants#True" >}}) (默认值)，它将返回正常的 EOF (空字节串对象) 来响应从下层套接字引发的非预期的 EOF 错误；如果指定为 [`False`]({{< ref "/library/constants#False" >}})，它将向调用方引发异常。

*session*，参见 [`session`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.session" >}})。

​	要将 [`SSLSocket`]({{< ref "/library/ipc/ssl#ssl.SSLSocket" >}}) 包装在另一个 [`SSLSocket`]({{< ref "/library/ipc/ssl#ssl.SSLSocket" >}}) 中，请使用 [`SSLContext.wrap_bio()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.wrap_bio" >}})。

> 在 3.5 版本发生变更: 总是允许传送 server_hostname，即使 OpenSSL 没有 SNI。

> 在 3.6 版本发生变更: 增加了 *session* 参数。

> 在 3.7 版本发生变更: 此方法返回 [`SSLContext.sslsocket_class`]({{< ref "/library/ipc/ssl#ssl.SSLContext.sslsocket_class" >}}) 的实例而不是硬编码的 [`SSLSocket`]({{< ref "/library/ipc/ssl#ssl.SSLSocket" >}})。

## SSLContext.**sslsocket_class**

[`SSLContext.wrap_socket()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.wrap_socket" >}}) 的返回类型，默认为 [`SSLSocket`]({{< ref "/library/ipc/ssl#ssl.SSLSocket" >}})。 该属性可以在类实例上被重载以便返回自定义的 [`SSLSocket`]({{< ref "/library/ipc/ssl#ssl.SSLSocket" >}}) 的子类。

> Added in version 3.7.
>

## SSLContext.**wrap_bio**(*incoming*, *outgoing*, *server_side=False*, *server_hostname=None*, *session=None*)

​	包装 BIO 对象 *incoming* 和 *outgoing* 并返回一个 [`SSLContext.sslobject_class`]({{< ref "/library/ipc/ssl#ssl.SSLContext.sslobject_class" >}}) (默认为 [`SSLObject`]({{< ref "/library/ipc/ssl#ssl.SSLObject" >}})) 的实例。 SSL 例程将从 BIO 中读取输入数据并将数据写入到 outgoing BIO。

*server_side*, *server_hostname* 和 *session* 形参具有与 [`SSLContext.wrap_socket()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.wrap_socket" >}}) 中相同的含义。

> 在 3.6 版本发生变更: 增加了 *session* 参数。

> 在 3.7 版本发生变更: 此方法返回 [`SSLContext.sslobject_class`]({{< ref "/library/ipc/ssl#ssl.SSLContext.sslobject_class" >}}) 的实例而不是硬编码的 [`SSLObject`]({{< ref "/library/ipc/ssl#ssl.SSLObject" >}})。

## SSLContext.**sslobject_class**

[`SSLContext.wrap_bio()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.wrap_bio" >}}) 的返回类型，默认为 [`SSLObject`]({{< ref "/library/ipc/ssl#ssl.SSLObject" >}})。 该属性可以在类实例上被重载以便返回自定义的 [`SSLObject`]({{< ref "/library/ipc/ssl#ssl.SSLObject" >}}) 的子类。

> Added in version 3.7.
>

## SSLContext.**session_stats**()

​	Get statistics about the SSL sessions created or managed by this context. A dictionary is returned which maps the names of each [piece of information](https://docs.openssl.org/1.1.1/man3/SSL_CTX_sess_number/) to their numeric values. For example, here is the total number of hits and misses in the session cache since the context was created:



``` python
>>> stats = context.session_stats()
>>> stats['hits'], stats['misses']
(0, 0)
```

## SSLContext.**check_hostname**

​	是否要将匹配 [`SSLSocket.do_handshake()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.do_handshake" >}}) 中对等方证书的主机名。 该上下文的 [`verify_mode`]({{< ref "/library/ipc/ssl#ssl.SSLContext.verify_mode" >}}) 必须被设为 [`CERT_OPTIONAL`]({{< ref "/library/ipc/ssl#ssl.CERT_OPTIONAL" >}}) 或 [`CERT_REQUIRED`]({{< ref "/library/ipc/ssl#ssl.CERT_REQUIRED" >}})，并且你必须将 *server_hostname* 传给 [`wrap_socket()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.wrap_socket" >}}) 以便匹配主机名。 启用主机名检查会自动将 [`verify_mode`]({{< ref "/library/ipc/ssl#ssl.SSLContext.verify_mode" >}}) 从 [`CERT_NONE`]({{< ref "/library/ipc/ssl#ssl.CERT_NONE" >}}) 设为 [`CERT_REQUIRED`]({{< ref "/library/ipc/ssl#ssl.CERT_REQUIRED" >}})。 只要启用了主机名检查就无法将其设回 [`CERT_NONE`]({{< ref "/library/ipc/ssl#ssl.CERT_NONE" >}})。 [`PROTOCOL_TLS_CLIENT`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS_CLIENT" >}}) 协议默认启用主机名检查。 对于其他协议，则必须显式地启用主机名检查。

​	示例:

```
import socket, ssl

context = ssl.SSLContext(ssl.PROTOCOL_TLSv1_2)
context.verify_mode = ssl.CERT_REQUIRED
context.check_hostname = True
context.load_default_certs()

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
ssl_sock = context.wrap_socket(s, server_hostname='www.verisign.com')
ssl_sock.connect(('www.verisign.com', 443))
```

> Added in version 3.4.
>

> 在 3.7 版本发生变更: 现在当主机名检查被启用且 [`verify_mode`]({{< ref "/library/ipc/ssl#ssl.SSLContext.verify_mode" >}}) 为 [`CERT_NONE`]({{< ref "/library/ipc/ssl#ssl.CERT_NONE" >}}) 时 [`verify_mode`]({{< ref "/library/ipc/ssl#ssl.SSLContext.verify_mode" >}}) 会自动更改为 [`CERT_REQUIRED`]({{< ref "/library/ipc/ssl#ssl.CERT_REQUIRED" >}})。 在之前版本中同样的操作将失败并引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

## SSLContext.**keylog_filename**

​	每当生成或接收到密钥时，将 TLS 密钥写入到一个密钥日志文件。 密钥日志文件的设计仅适用于调试目的。 文件的格式由 NSS 指明并为许多流量分析工具例如 Wireshark 所使用。 日志文件会以追加模式打开。 写入操作会在线程之间同步，但不会在进程之间同步。

> Added in version 3.8.
>

## SSLContext.**maximum_version**

​	一个代表所支持的最高 TLS 版本的 [`TLSVersion`]({{< ref "/library/ipc/ssl#ssl.TLSVersion" >}}) 枚举成员。 该值默认为 [`TLSVersion.MAXIMUM_SUPPORTED`]({{< ref "/library/ipc/ssl#ssl.TLSVersion.MAXIMUM_SUPPORTED" >}})。 这个属性对于 [`PROTOCOL_TLS`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS" >}}), [`PROTOCOL_TLS_CLIENT`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS_CLIENT" >}}) 和 [`PROTOCOL_TLS_SERVER`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS_SERVER" >}}) 以外的其他协议来说都是只读的。

[`maximum_version`]({{< ref "/library/ipc/ssl#ssl.SSLContext.maximum_version" >}}), [`minimum_version`]({{< ref "/library/ipc/ssl#ssl.SSLContext.minimum_version" >}}) 和 [`SSLContext.options`]({{< ref "/library/ipc/ssl#ssl.SSLContext.options" >}}) 等属性都会影响上下文所支持的 SSL 和 TLS 版本。 这个实现不会阻止无效的组合。 例如一个 [`options`]({{< ref "/library/ipc/ssl#ssl.SSLContext.options" >}}) 为 [`OP_NO_TLSv1_2`]({{< ref "/library/ipc/ssl#ssl.OP_NO_TLSv1_2" >}}) 而 [`maximum_version`]({{< ref "/library/ipc/ssl#ssl.SSLContext.maximum_version" >}}) 设为 [`TLSVersion.TLSv1_2`]({{< ref "/library/ipc/ssl#ssl.TLSVersion.TLSv1_2" >}}) 的上下文将无法建立 TLS 1.2 连接。

> Added in version 3.7.
>

## SSLContext.**minimum_version**

​	与 [`SSLContext.maximum_version`]({{< ref "/library/ipc/ssl#ssl.SSLContext.maximum_version" >}}) 类似，区别在于它是所支持的最低版本或为 [`TLSVersion.MINIMUM_SUPPORTED`]({{< ref "/library/ipc/ssl#ssl.TLSVersion.MINIMUM_SUPPORTED" >}})。

> Added in version 3.7.
>

## SSLContext.**num_tickets**

​	控制 `TLS_PROTOCOL_SERVER` 上下文的 TLS 1.3 会话凭据数量。这个设置不会影响 TLS 1.0 - 1.2 的连接。

> Added in version 3.8.
>

## SSLContext.**options**

​	一个代表此上下文中所启用的 SSL 选项集的整数。 默认值为 [`OP_ALL`]({{< ref "/library/ipc/ssl#ssl.OP_ALL" >}})，但你也可以通过在选项间进行 OR 运算来指定其他选项例如 [`OP_NO_SSLv2`]({{< ref "/library/ipc/ssl#ssl.OP_NO_SSLv2" >}})。

> 在 3.6 版本发生变更: [`SSLContext.options`]({{< ref "/library/ipc/ssl#ssl.SSLContext.options" >}}) 返回 [`Options`]({{< ref "/library/ipc/ssl#ssl.Options" >}}) 旗标:



``` python
>>> ssl.create_default_context().options  
<Options.OP_ALL|OP_NO_SSLv3|OP_NO_SSLv2|OP_NO_COMPRESSION: 2197947391>
```

> 自 3.7 版本弃用: 自 OpenSSL 1.1.0 起，所有 `OP_NO_SSL*` 和 `OP_NO_TLS*` 选项已被弃用，请改用新的 [`SSLContext.minimum_version`]({{< ref "/library/ipc/ssl#ssl.SSLContext.minimum_version" >}}) 和 [`SSLContext.maximum_version`]({{< ref "/library/ipc/ssl#ssl.SSLContext.maximum_version" >}})。

## SSLContext.**post_handshake_auth**

​	启用 TLS 1.3 握手后客户端身份验证。 握手后验证默认是被禁用的，服务器只能在初始握手期间请求 TLS 客户端证书。 当启用时，服务器可以在握手之后的任何时候请求 TLS 客户端证书。

​	当在客户端套接字上启用时，客户端会向服务器发信号说明它支持握手后身份验证。

​	当在服务器端套接字上启用时，[`SSLContext.verify_mode`]({{< ref "/library/ipc/ssl#ssl.SSLContext.verify_mode" >}}) 也必须被设为 [`CERT_OPTIONAL`]({{< ref "/library/ipc/ssl#ssl.CERT_OPTIONAL" >}}) 或 [`CERT_REQUIRED`]({{< ref "/library/ipc/ssl#ssl.CERT_REQUIRED" >}})。 实际的客户端证书交换会被延迟直至 [`SSLSocket.verify_client_post_handshake()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.verify_client_post_handshake" >}}) 被调用并执行了一些 I/O 操作后再进行。

> Added in version 3.8.
>

## SSLContext.**protocol**

​	构造上下文时所选择的协议版本。 这个属性是只读的。

## SSLContext.**hostname_checks_common_name**

​	在没有目标替代名称扩展的情况下 [`check_hostname`]({{< ref "/library/ipc/ssl#ssl.SSLContext.check_hostname" >}}) 是否要回退为验证证书的通用名称（默认为真值）。

> Added in version 3.7.
>

> 在 3.10 版本发生变更: 此旗标在 OpenSSL 1.1.1l 之前的版本上不起作用。 Python 3.8.9, 3.9.3 和 3.10 包括了针对之前版本的变通处理。

## SSLContext.**security_level**

​	An integer representing the [security level](https://docs.openssl.org/master/man3/SSL_CTX_get_security_level/) for the context. This attribute is read-only.

> Added in version 3.10.
>

## SSLContext.**verify_flags**

​	证书验证操作的标志位。可以用“或”的方式组合在一起设置 [`VERIFY_CRL_CHECK_LEAF`]({{< ref "/library/ipc/ssl#ssl.VERIFY_CRL_CHECK_LEAF" >}}) 这类标志。默认情况下，OpenSSL 既不需要也不验证证书吊销列表（CRL）。

> Added in version 3.4.
>

> 在 3.6 版本发生变更: [`SSLContext.verify_flags`]({{< ref "/library/ipc/ssl#ssl.SSLContext.verify_flags" >}}) 返回 [`VerifyFlags`]({{< ref "/library/ipc/ssl#ssl.VerifyFlags" >}}) 旗标:



``` python
>>> ssl.create_default_context().verify_flags  
<VerifyFlags.VERIFY_X509_TRUSTED_FIRST: 32768>
```

## SSLContext.**verify_mode**

​	是否要尝试验证其他对等方的证书以及如果验证失败应采取何种行为。 该属性值必须为 [`CERT_NONE`]({{< ref "/library/ipc/ssl#ssl.CERT_NONE" >}}), [`CERT_OPTIONAL`]({{< ref "/library/ipc/ssl#ssl.CERT_OPTIONAL" >}}) 或 [`CERT_REQUIRED`]({{< ref "/library/ipc/ssl#ssl.CERT_REQUIRED" >}}) 之一。

> 在 3.6 版本发生变更: [`SSLContext.verify_mode`]({{< ref "/library/ipc/ssl#ssl.SSLContext.verify_mode" >}}) 返回 [`VerifyMode`]({{< ref "/library/ipc/ssl#ssl.VerifyMode" >}}) 枚举:



``` python
>>> ssl.create_default_context().verify_mode  
<VerifyMode.CERT_REQUIRED: 2>
```

## SSLContext.**set_psk_client_callback**(*callback*)

​	在客户端连接上启用 TLS-PSK（预共享密钥）验证。

​	一般来说，基于证书的身份验证应当优先于此方法。

​	形参 `callback` 是一个签名为 `def callback(hint: str | None) -> tuple[str | None, bytes]` 的可调用对象。`hint` 形参是服务器所发送的可选身份标识。 返回值是一个 (client-identity, psk) 形式的元组。 其中 client-identity 是一个可选的字符串，它可被服务器用来为客户选择相应的 PSK。 该字符串在使用 UTF-8 编码时必须小于等于 `256` 个八位字节。 PSK 是一个代表预共享密钥的 [bytes-like object]({{< ref "/glossary/idx#term-bytes-like-object" >}})。 返回一个零长度的 PSK 以拒绝连接。

​	将 `callback` 设为 [`None`]({{< ref "/library/constants#None" >}}) 将移除任何现有的回调。

​备注
 

​	当使用 TLS 1.3 时：

- `hint` 形参值将始终为 [`None`]({{< ref "/library/constants#None" >}})。
- client-identity 必须是一个非空字符串。

​	用法示例:

```
context = ssl.SSLContext(ssl.PROTOCOL_TLS_CLIENT)
context.check_hostname = False
context.verify_mode = ssl.CERT_NONE
context.maximum_version = ssl.TLSVersion.TLSv1_2
context.set_ciphers('PSK')

# A simple lambda:
psk = bytes.fromhex('c0ffee')
context.set_psk_client_callback(lambda hint: (None, psk))

# A table using the hint from the server:
psk_table = { 'ServerId_1': bytes.fromhex('c0ffee'),
              'ServerId_2': bytes.fromhex('facade')
}
def callback(hint):
    return 'ClientId_1', psk_table.get(hint, b'')
context.set_psk_client_callback(callback)
```

​	如果 [`HAS_PSK`]({{< ref "/library/ipc/ssl#ssl.HAS_PSK" >}}) 为 `False` 则此方法将引发 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}})。

> Added in version 3.13.
>

## SSLContext.**set_psk_server_callback**(*callback*, *identity_hint=None*)

​	在服务器端连接上启用 TLS-PSK（预共享密钥验证）。

​	一般来说，基于证书的身份验证应当优先于此方法。

​	形参 `callback` 是一个签名为 `def callback(identity: str | None) -> bytes` 的可调用对象。 `identity` 形参是客户端所发送的可选身份标识，可用于选择相应的 PSK。 返回值是一个代表预共享密钥的 [bytes-like object]({{< ref "/glossary/idx#term-bytes-like-object" >}})。 返回一个零长度的 PSK 以拒绝连接。

​	将 `callback` 设为 [`None`]({{< ref "/library/constants#None" >}}) 将移除任何现有的回调。

​	形参 `identity_hint` 是发送给客户端的可选身份标识字符串。 该字符串在使用 UTF-8 编码时必须小于等于 `256` 个八位字节。

​备注
 

​	当使用 TLS 1.3 时 `identity_hint` 形参将不会被发送给客户端。

​	用法示例:

```
context = ssl.SSLContext(ssl.PROTOCOL_TLS_SERVER)
context.maximum_version = ssl.TLSVersion.TLSv1_2
context.set_ciphers('PSK')

# A simple lambda:
psk = bytes.fromhex('c0ffee')
context.set_psk_server_callback(lambda identity: psk)

# A table using the identity of the client:
psk_table = { 'ClientId_1': bytes.fromhex('c0ffee'),
              'ClientId_2': bytes.fromhex('facade')
}
def callback(identity):
    return psk_table.get(identity, b'')
context.set_psk_server_callback(callback, 'ServerId_1')
```

​	如果 [`HAS_PSK`]({{< ref "/library/ipc/ssl#ssl.HAS_PSK" >}}) 为 `False` 则此方法将引发 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}})。

> Added in version 3.13.
>



## 证书

​	总的来说证书是公钥/私钥系统的一个组成部分。 在这个系统中，每 个 *主体* (可能是一台机器、一个人或者一个组织) 都会分配到唯一的包含两部分的加密密钥。 一部分密钥是公开的，称为 *公钥*；另一部分密钥是保密的，称为 *私钥*。 这两个部分是互相关联的，就是说如果你用其中一个部分来加密一条消息，你将能用并且 **只能** 用另一个部分来解密它。

​	在一个证书中包含有两个主体的相关信息。 它包含 *目标方* 的名称和目标方的公钥。 它还包含由第二个主体 *颁发方* 所发布的声明：目标方的身份与他们所宣称的一致，包含的公钥也确实是目标方的公钥。 颁发方的声明使用颁发方的私钥进行签名，该私钥的内容只有颁发方自己才知道。 但是，任何人都可以找到颁发方的公钥，用它来解密这个声明，并将其与证书中的其他信息进行比较来验证颁发方声明的真实性。 证书还包含有关其有效期限的信息。 这被表示为两个字段，即 "notBefore" 和 "notAfter"。

​	在 Python 中应用证书时，客户端或服务器可以用证书来证明自己的身份。 还可以要求网络连接的另一方提供证书，提供的证书可以用于验证以满足客户端或服务器的验证要求。 如果验证失败，连接尝试可被设置为引发一个异常。 验证是由下层的 OpenSSL 框架来自动执行的；应用程序本身不必关注其内部的机制。 但是应用程序通常需要提供一组证书以允许此过程的发生。

​	Python 使用文件来包含证书。 它们应当采用 "PEM" 格式 (参见 [**RFC 1422**](https://datatracker.ietf.org/doc/html/rfc1422.html))，这是一种带有头部行和尾部行的 base-64 编码包装形式:

```
-----BEGIN CERTIFICATE-----
... (使用 base64 PEM 编码的证书) ...
-----END CERTIFICATE-----
```

### 证书链

​	包含证书的 Python 文件可以包含一系列的证书，有时被称为 *证书链*。 这个证书链应当以 "作为" 客户端或服务器的主体的专属证书打头，然后是证书颁发方的证书，然后是 *上述* 证书的颁发方的证书，证书链就这样不断上溯直到你得到一个 *自签名* 的证书，即具有相同目标方和颁发方的证书，有时也称为 *根证书*。 在证书文件中这些证书应当被拼接为一体。 例如，假设我们有一个包含三个证书的证书链，以我们的服务器证书打头，然后是为我们的服务器证书签名的证书颁发机构的证书，最后是为证书颁发机构的证书颁发证书的机构的根证书:

```
-----BEGIN CERTIFICATE-----
... (certificate for your server)...
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
... (the certificate for the CA)...
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
... (the root certificate for the CA's issuer)...
-----END CERTIFICATE-----
```

### CA 证书

​	如果你想要求对连接的另一方的证书进行验证，你必须提供一个 "CA 证书" 文件，其中包含了你愿意信任的每个颁发方的证书链。 同样地，这个文件的内容就是这些证书链拼接在一起的结果。 为了进行验证，Python 将使用它在文件中找到的第一个匹配的证书链。 可以通过调用 [`SSLContext.load_default_certs()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.load_default_certs" >}}) 来使用系统平台的证书文件，这可以由 [`create_default_context()`]({{< ref "/library/ipc/ssl#ssl.create_default_context" >}}) 自动完成。

### 合并的密钥和证书

​	私钥往往与证书存储在相同的文件中；在此情况下，只需要将 `certfile` 形参传给 [`SSLContext.load_cert_chain()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.load_cert_chain" >}})。 如果私钥是与证书一起存储的，则它应当放在证书链的第一个证书之前:

```
-----BEGIN RSA PRIVATE KEY-----
... (private key in base64 encoding) ...
-----END RSA PRIVATE KEY-----
-----BEGIN CERTIFICATE-----
... (certificate in base64 PEM encoding) ...
-----END CERTIFICATE-----
```

### 自签名证书

​	如果你准备创建一个提供 SSL 加密连接服务的服务器，你需要为该服务获取一份证书。 有许多方式可以获取合适的证书，例如从证书颁发机构购买。 另一种常见做法是生成自签名证书。 生成自签名证书的最简单方式是使用 OpenSSL 软件包，代码如下所示:

```
% openssl req -new -x509 -days 365 -nodes -out cert.pem -keyout cert.pem
Generating a 1024 bit RSA private key
.......++++++
.............................++++++
writing new private key to 'cert.pem'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:US
State or Province Name (full name) [Some-State]:MyState
Locality Name (eg, city) []:Some City
Organization Name (eg, company) [Internet Widgits Pty Ltd]:My Organization, Inc.
Organizational Unit Name (eg, section) []:My Group
Common Name (eg, YOUR name) []:myserver.mygroup.myorganization.com
Email Address []:ops@myserver.mygroup.myorganization.com
%
```

​	自签名证书的缺点在于它是它自身的根证书，因此不会存在于别人的已知（且信任的）根证书缓存当中。

## 例子

### 检测 SSL 支持

​	要检测一个 Python 安装版中是否带有 SSL 支持，用户代码应当使用以下例程:

```
try:
    import ssl
except ImportError:
    pass
else:
    ...  # 执行需要 SSL 支持的操作
```

### 客户端操作

​	这个例子创建了一个 SSL 上下文并使用客户端套接字的推荐安全设置，包括自动证书验证:



``` python
>>> context = ssl.create_default_context()
```

​	如果你喜欢自行调整安全设置，你可能需要从头创建一个上下文（但是请请注意避免不正确的设置）:



``` python
>>> context = ssl.SSLContext(ssl.PROTOCOL_TLS_CLIENT)
>>> context.load_verify_locations("/etc/ssl/certs/ca-bundle.crt")
```

​	(这段代码假定你的操作系统将所有 CA 证书打包存放于 `/etc/ssl/certs/ca-bundle.crt`；如果不是这样，你将收到报错信息，必须修改此位置)

[`PROTOCOL_TLS_CLIENT`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS_CLIENT" >}}) 协议配置用于证书验证和主机名验证的上下文。 [`verify_mode`]({{< ref "/library/ipc/ssl#ssl.SSLContext.verify_mode" >}}) 设为 [`CERT_REQUIRED`]({{< ref "/library/ipc/ssl#ssl.CERT_REQUIRED" >}}) 而 [`check_hostname`]({{< ref "/library/ipc/ssl#ssl.SSLContext.check_hostname" >}}) 设为 `True`。 所有其他协议都会使用不安全的默认值创建 SSL 上下文。

​	当你使用此上下文去连接服务器时，[`CERT_REQUIRED`]({{< ref "/library/ipc/ssl#ssl.CERT_REQUIRED" >}}) 和 [`check_hostname`]({{< ref "/library/ipc/ssl#ssl.SSLContext.check_hostname" >}}) 会验证服务器证书；它将确认服务器证书使用了某个 CA 证书进行签名，检查签名是否正确，并验证其他属性例如主机名的有效性和身份真实性:



``` python
>>> conn = context.wrap_socket(socket.socket(socket.AF_INET),
...                            server_hostname="www.python.org")
>>> conn.connect(("www.python.org", 443))
```

​	你可以随后获取该证书:



``` python
>>> cert = conn.getpeercert()
```

​	可视化检查显示证书能够证明目标服务 (即 HTTPS 主机 `www.python.org`) 的身份:



``` python
>>> pprint.pprint(cert)
{'OCSP': ('http://ocsp.digicert.com',),
 'caIssuers': ('http://cacerts.digicert.com/DigiCertSHA2ExtendedValidationServerCA.crt',),
 'crlDistributionPoints': ('http://crl3.digicert.com/sha2-ev-server-g1.crl',
                           'http://crl4.digicert.com/sha2-ev-server-g1.crl'),
 'issuer': ((('countryName', 'US'),),
            (('organizationName', 'DigiCert Inc'),),
            (('organizationalUnitName', 'www.digicert.com'),),
            (('commonName', 'DigiCert SHA2 Extended Validation Server CA'),)),
 'notAfter': 'Sep  9 12:00:00 2016 GMT',
 'notBefore': 'Sep  5 00:00:00 2014 GMT',
 'serialNumber': '01BB6F00122B177F36CAB49CEA8B6B26',
 'subject': ((('businessCategory', 'Private Organization'),),
             (('1.3.6.1.4.1.311.60.2.1.3', 'US'),),
             (('1.3.6.1.4.1.311.60.2.1.2', 'Delaware'),),
             (('serialNumber', '3359300'),),
             (('streetAddress', '16 Allen Rd'),),
             (('postalCode', '03894-4801'),),
             (('countryName', 'US'),),
             (('stateOrProvinceName', 'NH'),),
             (('localityName', 'Wolfeboro'),),
             (('organizationName', 'Python Software Foundation'),),
             (('commonName', 'www.python.org'),)),
 'subjectAltName': (('DNS', 'www.python.org'),
                    ('DNS', 'python.org'),
                    ('DNS', 'pypi.org'),
                    ('DNS', 'docs.python.org'),
                    ('DNS', 'testpypi.org'),
                    ('DNS', 'bugs.python.org'),
                    ('DNS', 'wiki.python.org'),
                    ('DNS', 'hg.python.org'),
                    ('DNS', 'mail.python.org'),
                    ('DNS', 'packaging.python.org'),
                    ('DNS', 'pythonhosted.org'),
                    ('DNS', 'www.pythonhosted.org'),
                    ('DNS', 'test.pythonhosted.org'),
                    ('DNS', 'us.pycon.org'),
                    ('DNS', 'id.python.org')),
 'version': 3}
```

​	现在 SSL 通道已建立并已验证了证书，你可以继续与服务器对话了:



``` python
>>> conn.sendall(b"HEAD / HTTP/1.0\r\nHost: linuxfr.org\r\n\r\n")
>>> pprint.pprint(conn.recv(1024).split(b"\r\n"))
[b'HTTP/1.1 200 OK',
 b'Date: Sat, 18 Oct 2014 18:27:20 GMT',
 b'Server: nginx',
 b'Content-Type: text/html; charset=utf-8',
 b'X-Frame-Options: SAMEORIGIN',
 b'Content-Length: 45679',
 b'Accept-Ranges: bytes',
 b'Via: 1.1 varnish',
 b'Age: 2188',
 b'X-Served-By: cache-lcy1134-LCY',
 b'X-Cache: HIT',
 b'X-Cache-Hits: 11',
 b'Vary: Cookie',
 b'Strict-Transport-Security: max-age=63072000; includeSubDomains',
 b'Connection: close',
 b'',
 b'']
```

​	参见下文对于 [安全考量]({{< ref "/library/ipc/ssl#ssl-security" >}}) 的讨论。

### 服务器端操作

​	对于服务器操作，通常你需要在文件中存放服务器证书和私钥各一份。 你将首先创建一个包含密钥和证书的上下文，这样客户端就能检查你的身份真实性。 然后你将打开一个套接字，将其绑定到一个端口，在其上调用 `listen()`，并开始等待客户端连接:

```
import socket, ssl

context = ssl.create_default_context(ssl.Purpose.CLIENT_AUTH)
context.load_cert_chain(certfile="mycertfile", keyfile="mykeyfile")

bindsocket = socket.socket()
bindsocket.bind(('myaddr.example.com', 10023))
bindsocket.listen(5)
```

​	当有客户端连接时，你将在套接字上调用 `accept()` 以从另一端获取新的套接字，并使用上下文的 [`SSLContext.wrap_socket()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.wrap_socket" >}}) 方法来为连接创建一个服务器端 SSL 套接字:

```
while True:
    newsocket, fromaddr = bindsocket.accept()
    connstream = context.wrap_socket(newsocket, server_side=True)
    try:
        deal_with_client(connstream)
    finally:
        connstream.shutdown(socket.SHUT_RDWR)
        connstream.close()
```

​	随后你将从 `connstream` 读取数据并对其进行处理，直至你结束与客户端的会话（或客户端结束与你的会话）:

```
def deal_with_client(connstream):
    data = connstream.recv(1024)
    # empty data means the client is finished with us
    while data:
        if not do_something(connstream, data):
            # we'll assume do_something returns False
            # when we're finished with client
            break
        data = connstream.recv(1024)
    # finished with client
```

​	并返回至监听新的客户端连接（当然，真正的服务器应当会在单独的线程中处理每个客户端连接，或者将套接字设为 [非阻塞模式]({{< ref "/library/ipc/ssl#ssl-nonblocking" >}}) 并使用事件循环）。



## 关于非阻塞套接字的说明

​	在非阻塞模式下 SSL 套接字的行为与常规套接字略有不同。 当使用非阻塞模式时，你需要注意下面这些事情:

- 如果一个 I/O 操作会阻塞，大多数 [`SSLSocket`]({{< ref "/library/ipc/ssl#ssl.SSLSocket" >}}) 方法都将引发 [`SSLWantWriteError`]({{< ref "/library/ipc/ssl#ssl.SSLWantWriteError" >}}) 或 [`SSLWantReadError`]({{< ref "/library/ipc/ssl#ssl.SSLWantReadError" >}}) 而非 [`BlockingIOError`]({{< ref "/library/exceptions#BlockingIOError" >}})。 如果有必要在下层套接字上执行读取操作将引发 [`SSLWantReadError`]({{< ref "/library/ipc/ssl#ssl.SSLWantReadError" >}})，在下层套接字上执行写入操作则将引发 [`SSLWantWriteError`]({{< ref "/library/ipc/ssl#ssl.SSLWantWriteError" >}})。 请注意尝试 *写入* 到 SSL 套接字可能需要先从下层套接字 *读取*，而尝试从 SSL 套接字 *读取* 则可能需要先向下层套接字 *写入*。

  > 在 3.5 版本发生变更: 在较早的 Python 版本中，`SSLSocket.send()` 方法会返回零值而非引发 [`SSLWantWriteError`]({{< ref "/library/ipc/ssl#ssl.SSLWantWriteError" >}}) 或 [`SSLWantReadError`]({{< ref "/library/ipc/ssl#ssl.SSLWantReadError" >}})。

- 调用 [`select()`]({{< ref "/library/ipc/select#select.select" >}}) 将告诉你可以从 OS 层级的套接字读取（或向其写入），但这并不意味着在上面的 SSL 层有足够的数据。 例如，可能只有部分 SSL 帧已经到达。 因此，你必须准备好处理 `SSLSocket.recv()` 和 `SSLSocket.send()` 失败的情况，并在再次调用 [`select()`]({{< ref "/library/ipc/select#select.select" >}}) 之后重新尝试。

- 相反地，由于 SSL 层具有自己的帧机制，一个 SSL 套接字可能仍有可读取的数据而 [`select()`]({{< ref "/library/ipc/select#select.select" >}}) 并不知道这一点。 因此，你应当先调用 `SSLSocket.recv()` 取走所有潜在的可用数据，然后只在必要时对 [`select()`]({{< ref "/library/ipc/select#select.select" >}}) 调用执行阻塞。

  (当然，类似的保留规则在使用其他原语例如 [`poll()`]({{< ref "/library/ipc/select#select.poll" >}})，或 [`selectors`]({{< ref "/library/ipc/selectors#module-selectors" >}}) 模块中的原语时也适用)

- SSL 握手本身将是非阻塞的: [`SSLSocket.do_handshake()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.do_handshake" >}}) 方法必须不断重试直至其成功返回。 下面是一个使用 [`select()`]({{< ref "/library/ipc/select#select.select" >}}) 来等待套接字就绪的简短例子:

  ```
  while True:
      try:
          sock.do_handshake()
          break
      except ssl.SSLWantReadError:
          select.select([sock], [], [])
      except ssl.SSLWantWriteError:
          select.select([], [sock], [])
  ```

​参见
 

[`asyncio`]({{< ref "/library/ipc/asyncio#module-asyncio" >}}) 模块支持 [非阻塞 SSL 套接字]({{< ref "/library/ipc/ssl#ssl-nonblocking" >}}) 并提供了更高层级的 API。 它会使用 [`selectors`]({{< ref "/library/ipc/selectors#module-selectors" >}}) 模块来轮询事件并处理 [`SSLWantWriteError`]({{< ref "/library/ipc/ssl#ssl.SSLWantWriteError" >}}), [`SSLWantReadError`]({{< ref "/library/ipc/ssl#ssl.SSLWantReadError" >}}) 和 [`BlockingIOError`]({{< ref "/library/exceptions#BlockingIOError" >}}) 等异常。 它还会异步地执行 SSL 握手。

## 内存 BIO 支持

> Added in version 3.5.
>

​	自从 SSL 模块在 Python 2.6 起被引入之后，[`SSLSocket`]({{< ref "/library/ipc/ssl#ssl.SSLSocket" >}}) 类提供了两个互相关联但彼此独立的功能分块:

- SSL 协议处理
- 网络 IO

​	网络 IO API 与 [`socket.socket`]({{< ref "/library/ipc/socket#socket.socket" >}}) 所提供的功能一致，[`SSLSocket`]({{< ref "/library/ipc/ssl#ssl.SSLSocket" >}}) 也是从那里继承而来的。 这允许 SSL 套接字被用作常规套接字的替代，使得向现有应用程序添加 SSL 支持变得非常容易。

​	将 SSL 协议处理与网络 IO 结合使用通常都能运行良好，但在某些情况下则不能。 此情况的一个例子是 async IO 框架，该框架要使用不同的 IO 多路复用模型而非 (基于就绪状态的) "在文件描述器上执行选择/轮询" 模型，该模型是 [`socket.socket`]({{< ref "/library/ipc/socket#socket.socket" >}}) 和内部 OpenSSL 套接字 IO 例程正常运行的假设前提。 这种情况在该模型效率不高的 Windows 平台上最为常见。 为此还提供了一个 [`SSLSocket`]({{< ref "/library/ipc/ssl#ssl.SSLSocket" >}}) 的简化形式，称为 [`SSLObject`]({{< ref "/library/ipc/ssl#ssl.SSLObject" >}})。

## *class* ssl.**SSLObject**

[`SSLSocket`]({{< ref "/library/ipc/ssl#ssl.SSLSocket" >}}) 的简化形式，表示一个不包含任何网络 IO 方法的 SSL 协议实例。 这个类通常由想要通过内存缓冲区为 SSL 实现异步 IO 的框架作者来使用。

​	这个类在低层级 SSL 对象上实现了一个接口，与 OpenSSL 所实现的类似。 此对象会捕获 SSL 连接的状态但其本身不提供任何网络 IO。 IO 需要通过单独的 "BIO" 对象来执行，该对象是 OpenSSL 的 IO 抽象层。

​	这个类没有公有构造器。 [`SSLObject`]({{< ref "/library/ipc/ssl#ssl.SSLObject" >}}) 实例必须使用 [`wrap_bio()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.wrap_bio" >}}) 方法来创建。 此方法将创建 [`SSLObject`]({{< ref "/library/ipc/ssl#ssl.SSLObject" >}}) 实例并将其绑定到一个 BIO 对。 其中 *incoming* BIO 用来将数据从 Python 传递到 SSL 协议实例，而 *outgoing* BIO 用来进行数据反向传递。

​	可以使用以下方法：

- [`context`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.context" >}})
- [`server_side`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.server_side" >}})
- [`server_hostname`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.server_hostname" >}})
- [`session`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.session" >}})
- [`session_reused`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.session_reused" >}})
- [`read()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.read" >}})
- [`write()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.write" >}})
- [`getpeercert()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.getpeercert" >}})
- [`get_verified_chain()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.get_verified_chain" >}})
- [`get_unverified_chain()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.get_unverified_chain" >}})
- [`selected_alpn_protocol()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.selected_alpn_protocol" >}})
- [`selected_npn_protocol()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.selected_npn_protocol" >}})
- [`cipher()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.cipher" >}})
- [`shared_ciphers()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.shared_ciphers" >}})
- [`compression()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.compression" >}})
- [`pending()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.pending" >}})
- [`do_handshake()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.do_handshake" >}})
- [`verify_client_post_handshake()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.verify_client_post_handshake" >}})
- [`unwrap()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.unwrap" >}})
- [`get_channel_binding()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.get_channel_binding" >}})
- [`version()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.version" >}})

​	与 [`SSLSocket`]({{< ref "/library/ipc/ssl#ssl.SSLSocket" >}}) 相比，此对象缺少下列特性:

- 任何形式的网络 IO; `recv()` 和 `send()` 仅对下层的 [`MemoryBIO`]({{< ref "/library/ipc/ssl#ssl.MemoryBIO" >}}) 缓冲区执行读取和写入。
- 不存在 *do_handshake_on_connect* 机制。 你必须总是手动调用 [`do_handshake()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.do_handshake" >}}) 来开始握手操作。
- 不存在对 *suppress_ragged_eofs* 的处理。 所有违反协议的文件结束条件将通过 [`SSLEOFError`]({{< ref "/library/ipc/ssl#ssl.SSLEOFError" >}}) 异常来报告。
- 方法 [`unwrap()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.unwrap" >}}) 的调用不返回任何东西，不会如 SSL 套接字那样返回下层的套接字。
- *server_name_callback* 回调被传给 [`SSLContext.set_servername_callback()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.set_servername_callback" >}}) 时将获得一个 [`SSLObject`]({{< ref "/library/ipc/ssl#ssl.SSLObject" >}}) 实例而非 [`SSLSocket`]({{< ref "/library/ipc/ssl#ssl.SSLSocket" >}}) 实例作为其第一个形参。

​	有关 [`SSLObject`]({{< ref "/library/ipc/ssl#ssl.SSLObject" >}}) 用法的一些说明:

- 在 [`SSLObject`]({{< ref "/library/ipc/ssl#ssl.SSLObject" >}}) 上的所有 IO 都是 [非阻塞的]({{< ref "/library/ipc/ssl#ssl-nonblocking" >}})。 这意味着例如 [`read()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.read" >}}) 在其需要比 incoming BIO 可用的更多数据时将会引发 [`SSLWantReadError`]({{< ref "/library/ipc/ssl#ssl.SSLWantReadError" >}})。

> 在 3.7 版本发生变更: [`SSLObject`]({{< ref "/library/ipc/ssl#ssl.SSLObject" >}}) 的实例必须使用 [`wrap_bio()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.wrap_bio" >}}) 来创建。 在较早的版本中，直接创建该实例是可能的。 但这从未被写入文档或是被正式支持。

​	SSLObject 会使用内存缓冲区与外部世界通信。 [`MemoryBIO`]({{< ref "/library/ipc/ssl#ssl.MemoryBIO" >}}) 类提供了可被用于此目的的内存缓冲区。 它包装了一个 OpenSSL 内存 BIO (Basic IO) 对象:

## *class* ssl.**MemoryBIO**

​	一个可被用来在 Python 和 SSL 协议实例之间传递数据的内存缓冲区。

## **pending**

​	返回当前存在于内存缓冲区的字节数。

## **eof**

​	一个表明内存 BIO 目前是否位于文件末尾的布尔值。

## **read**(*n=-1*)

​	从内存缓冲区读取至多 *n* 个字节。 如果 *n* 未指定或为负值，则返回全部字节数据。

## **write**(*buf*)

​	将字节数据从 *buf* 写入到内存 BIO。 *buf* 参数必须为支持缓冲区协议的对象。

​	返回值为写入的字节数，它总是与 *buf* 的长度相等。

## **write_eof**()

​	将一个 EOF 标记写入到内存 BIO。 在此方法被调用以后，再调用 [`write()`]({{< ref "/library/ipc/ssl#ssl.MemoryBIO.write" >}}) 将是非法的。 属性 [`eof`]({{< ref "/library/ipc/ssl#ssl.MemoryBIO.eof" >}}) will 在缓冲区当前的所有数据都被读取之后将变为真值。

## SSL 会话

> Added in version 3.6.
>

## *class* ssl.**SSLSession**

[`session`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.session" >}}) 所使用的会话对象。

## **id**

## **time**

## **timeout**

## **ticket_lifetime_hint**

## **has_ticket**



## 安全考量

### 最佳默认值

​	针对 **客户端使用**，如果你对于安全策略没有任何特殊要求，则强烈推荐你使用 [`create_default_context()`]({{< ref "/library/ipc/ssl#ssl.create_default_context" >}}) 函数来创建你的 SSL 上下文。 它将加载系统的受信任 CA 证书，启用证书验证和主机名检查，并尝试合理地选择安全的协议和密码设置。

​	例如，以下演示了你应当如何使用 [`smtplib.SMTP`]({{< ref "/library/internet/smtplib#smtplib.SMTP" >}}) 类来创建指向一个 SMTP 服务器的受信任且安全的连接:



``` python
>>> import ssl, smtplib
>>> smtp = smtplib.SMTP("mail.python.org", port=587)
>>> context = ssl.create_default_context()
>>> smtp.starttls(context=context)
(220, b'2.0.0 Ready to start TLS')
```

​	如果连接需要客户端证书，可使用 [`SSLContext.load_cert_chain()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.load_cert_chain" >}}) 来添加。

​	作为对比，如果你通过自行调用 [`SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 构造器来创建 SSL 上下文，它默认将不会启用证书验证和主机名检查。 如果你这样做，请阅读下面的段落以达到良好的安全级别。

### 手动设置

#### 验证证书

​	当直接调用 [`SSLContext`]({{< ref "/library/ipc/ssl#ssl.SSLContext" >}}) 构造器时，默认值为 [`CERT_NONE`]({{< ref "/library/ipc/ssl#ssl.CERT_NONE" >}})。 由于它不会验证对端的身份，因而是不安全的，特别是在大部分时间里你都希望能确保与你通信的服务器的可靠性的客户端模式下。 为此，当在客户端模式下，强烈建议使用 [`CERT_REQUIRED`]({{< ref "/library/ipc/ssl#ssl.CERT_REQUIRED" >}})。 但是，仅靠它本身是不够的；你还必须检查服务器证书，它可通过调用 [`SSLSocket.getpeercert()`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.getpeercert" >}}) 来获取，确定它与目标服务相匹配。 对于许多协议和应用程序来说，服务可通过主机名来进行标识。 这种通用检查会在 [`SSLContext.check_hostname`]({{< ref "/library/ipc/ssl#ssl.SSLContext.check_hostname" >}}) 已启用时自动执行。

> 在 3.7 版本发生变更: 主机名匹配现在是由 OpenSSL 来执行的。 Python 不会再使用 `match_hostname()`。

​	在服务器模式下，如果你想要使用 SSL 层来验证客户端（而不是使用更高层级的验证机制），你也必须要指定 [`CERT_REQUIRED`]({{< ref "/library/ipc/ssl#ssl.CERT_REQUIRED" >}}) 并以类似方式检查客户端证书。

#### 协议版本

​	SSL 版本 2 和 3 被认为是不安全的因而使用它们会有风险。 如果你想要客户端和服务器之间有最大的兼容性，推荐使用 [`PROTOCOL_TLS_CLIENT`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS_CLIENT" >}}) 或 [`PROTOCOL_TLS_SERVER`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS_SERVER" >}}) 作为协议版本。 SSLv2 和 SSLv3 默认会被禁用。



``` python
>>> client_context = ssl.SSLContext(ssl.PROTOCOL_TLS_CLIENT)
>>> client_context.minimum_version = ssl.TLSVersion.TLSv1_3
>>> client_context.maximum_version = ssl.TLSVersion.TLSv1_3
```

​	上面创建的 SSL 上下文将只允许与服务器进行 TLSv1.3 及更高版本（如果你的系统支持）的连接。 在默认情况下 [`PROTOCOL_TLS_CLIENT`]({{< ref "/library/ipc/ssl#ssl.PROTOCOL_TLS_CLIENT" >}}) 将使用证书验证和主机名检查。 你必须将证书加载到上下文中。

#### 密码选择

​	If you have advanced security requirements, fine-tuning of the ciphers enabled when negotiating a SSL session is possible through the [`SSLContext.set_ciphers()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.set_ciphers" >}}) method. Starting from Python 3.2.3, the ssl module disables certain weak ciphers by default, but you may want to further restrict the cipher choice. Be sure to read OpenSSL's documentation about the [cipher list format](https://docs.openssl.org/1.1.1/man1/ciphers/#cipher-list-format). If you want to check which ciphers are enabled by a given cipher list, use [`SSLContext.get_ciphers()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.get_ciphers" >}}) or the `openssl ciphers` command on your system.

### 多进程

​	如果使用此模块作为多进程应用的一部分（例如，使用 [`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}) 或 [`concurrent.futures`]({{< ref "/library/concurrency/future/concurrent_futures#module-concurrent.futures" >}}) 模块），请注意 OpenSSL 的内部随机数字生成器并不能正确处理分叉的进程。 应用程序必须修改父进程的 PRNG 状态，如果它们要使用任何包含 [`os.fork()`]({{< ref "/library/allos/os#os.fork" >}}) 的 SSL 特征的话。 任何对 [`RAND_add()`]({{< ref "/library/ipc/ssl#ssl.RAND_add" >}}) 或 [`RAND_bytes()`]({{< ref "/library/ipc/ssl#ssl.RAND_bytes" >}}) 的成功调用都可以做到这一点。



## TLS 1.3

> Added in version 3.7.
>

​	TLS 1.3 协议的行为与低版本的 TLS/SSL 略有不同。某些 TLS 1.3 新特性还不可用。

- TLS 1.3 使用一组不同的加密套件集。 默认情况下所有 AES-GCM 和 ChaCha20 加密套件都会被启用。 [`SSLContext.set_ciphers()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.set_ciphers" >}}) 方法还不能启用或禁用任何 TLS 1.3 加密，但 [`SSLContext.get_ciphers()`]({{< ref "/library/ipc/ssl#ssl.SSLContext.get_ciphers" >}}) 会返回它们。
- 会话凭据不再会作为初始握手的组成部分被发送而是以不同的方式来处理。 [`SSLSocket.session`]({{< ref "/library/ipc/ssl#ssl.SSLSocket.session" >}}) 和 [`SSLSession`]({{< ref "/library/ipc/ssl#ssl.SSLSession" >}}) 与 TLS 1.3 不兼容。
- 客户端证书在初始握手期间也不会再被验证。 服务器可以在任何时候请求证书。 客户端会在它们从服务器发送或接收应用数据时处理证书请求。
- 早期数据、延迟的 TLS 客户端证书请求、签名算法配置和密钥重生成等 TLS 1.3 特性尚未被支持。

> 参见
>
> - Class [`socket.socket`]({{< ref "/library/ipc/socket#socket.socket" >}})
>
>   下层 [`socket`]({{< ref "/library/ipc/socket#module-socket" >}}) 类的文档
>
> - [SSL/TLS 高强度加密：概述](https://httpd.apache.org/docs/trunk/en/ssl/ssl_intro.html)
>
>   Apache HTTP Server文档介绍
>
> - [**RFC 1422: 因特网电子邮件的隐私加强：第二部分：基于证书的密钥管理**](https://datatracker.ietf.org/doc/html/rfc1422.html)
>
>   Steve Kent
>
> - [**RFC 4086: 确保安全的随机性要求**](https://datatracker.ietf.org/doc/html/rfc4086.html)
>
>   Donald E., Jeffrey I. Schiller
>
> - [**RFC 5280: 互联网 X.509 公钥基础架构证书和证书吊销列表 (CRL) 配置文件**](https://datatracker.ietf.org/doc/html/rfc5280.html)
>
>   D. Cooper
>
> - [**RFC 5246: 传输层安全性 (TLS) 协议版本 1.2**](https://datatracker.ietf.org/doc/html/rfc5246.html)
>
>   T. Dierks et. al.
>
> - [**RFC 6066: 传输层安全性 (TLS) 的扩展**](https://datatracker.ietf.org/doc/html/rfc6066.html)
>
>   D. Eastlake
>
> - [IANA TLS: 传输层安全性 (TLS) 的参数](https://www.iana.org/assignments/tls-parameters/tls-parameters.xml)
>
>   IANA
>
> - [**RFC 7525: 传输层安全性 (TLS) 和数据报传输层安全性 (DTLS) 的安全使用建议**](https://datatracker.ietf.org/doc/html/rfc7525.html)
>
>   IETF
>
> - [Mozilla 的服务器端 TLS 建议](https://wiki.mozilla.org/Security/Server_Side_TLS)
>
>   Mozilla
