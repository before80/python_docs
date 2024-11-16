+++
title = "hashlib --- 安全哈希与消息摘要"
date = 2024-11-15T12:06:46+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/hashlib.html](https://docs.python.org/zh-cn/3.13/library/hashlib.html)
>
> 收录该文档的时间：`2024-11-15T12:06:46+08:00`

# `hashlib` --- 安全哈希与消息摘要

**源码：** [Lib/hashlib.py](https://github.com/python/cpython/tree/3.13/Lib/hashlib.py)



------

​	本模块针对许多不同的安全哈希和消息摘要算法实现了一个通用接口。 包括了 FIPS 安全哈希算法 SHA1, SHA224, SHA256, SHA384, SHA512, (定义见 [the FIPS 180-4 standard](https://csrc.nist.gov/pubs/fips/180-4/upd1/final)), SHA-3 系列 (定义见 [the FIPS 202 standard](https://csrc.nist.gov/pubs/fips/202/final)) 以及 RSA 的 MD5 算法 (定义见互联网 [**RFC 1321**](https://datatracker.ietf.org/doc/html/rfc1321.html))。 术语 "安全哈希" 和 "消息摘要" 是同义的。 较旧的算法被称为消息摘要。 现代的术语则是安全哈希。

​	备注

 

​	如果你想找到 adler32 或 crc32 哈希函数，它们在 [`zlib`]({{< ref "/library/archiving/zlib#module-zlib" >}}) 模块中。



## 哈希算法

​	每种类型的 *hash* 都有一个构造器方法。 它们都返回一个具有相同简单接口的哈希对象。 例如，使用 [`sha256()`]({{< ref "/library/crypto/hashlib#hashlib.sha256" >}}) 创建一个 SHA-256 哈希对象。 你可以使用 [`update`]({{< ref "/library/crypto/hashlib#hashlib.hash.update" >}}) 方法向这个对象输入 [字节类对象]({{< ref "/glossary/idx#term-bytes-like-object" >}}) (通常是 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}))。 在任何时候你都可以使用 [`digest()`]({{< ref "/library/crypto/hashlib#hashlib.hash.digest" >}}) 或 [`hexdigest()`]({{< ref "/library/crypto/hashlib#hashlib.hash.hexdigest" >}}) 方法获得到目前为止输入这个对象的拼接数据的 *digest*。

​	为了允许多线程，当在其构造器或 [`.update`]({{< ref "/library/crypto/hashlib#hashlib.hash.update" >}}) 方法中计算一次性提供超过 2047 字节数据的哈希时将会释放 Python [GIL]({{< ref "/glossary/idx#term-GIL" >}})。

​	本模块中总是存在的哈希算法构造器有 [`sha1()`]({{< ref "/library/crypto/hashlib#hashlib.sha1" >}}), [`sha224()`]({{< ref "/library/crypto/hashlib#hashlib.sha224" >}}), [`sha256()`]({{< ref "/library/crypto/hashlib#hashlib.sha256" >}}), [`sha384()`]({{< ref "/library/crypto/hashlib#hashlib.sha384" >}}), [`sha512()`]({{< ref "/library/crypto/hashlib#hashlib.sha512" >}}), [`sha3_224()`]({{< ref "/library/crypto/hashlib#hashlib.sha3_224" >}}), [`sha3_256()`]({{< ref "/library/crypto/hashlib#hashlib.sha3_256" >}}), [`sha3_384()`]({{< ref "/library/crypto/hashlib#hashlib.sha3_384" >}}), [`sha3_512()`]({{< ref "/library/crypto/hashlib#hashlib.sha3_512" >}}), [`shake_128()`]({{< ref "/library/crypto/hashlib#hashlib.shake_128" >}}), [`shake_256()`]({{< ref "/library/crypto/hashlib#hashlib.shake_256" >}}), [`blake2b()`]({{< ref "/library/crypto/hashlib#hashlib.blake2b" >}}) 和 [`blake2s()`]({{< ref "/library/crypto/hashlib#hashlib.blake2s" >}})。 [`md5()`]({{< ref "/library/crypto/hashlib#hashlib.md5" >}}) 通常也是可用的，但在你使用稀有的 "FIPS 兼容" Python 编译版时它可能会缺失或被屏蔽。 这些构造器对应于 [`algorithms_guaranteed`]({{< ref "/library/crypto/hashlib#hashlib.algorithms_guaranteed" >}})。

​	如果你的 Python 分发版的 [`hashlib`]({{< ref "/library/crypto/hashlib#module-hashlib" >}}) 是基于提供了其他算法的 OpenSSL 编译版上链接的那么还可能存在一些附加的算法。 其他算法在所有安装版上 *不保证全都可用* 并且仅可通过 [`new()`]({{< ref "/library/crypto/hashlib#hashlib.new" >}}) 使用名称来访问。 参见 [`algorithms_available`]({{< ref "/library/crypto/hashlib#hashlib.algorithms_available" >}})。

​	警告

 

​	一些算法具有已知的碰撞弱点（包括 MD5 和 SHA1）。 请参阅本文档末尾的 [Attacks on cryptographic hash algorithms](https://en.wikipedia.org/wiki/Cryptographic_hash_function#Attacks_on_cryptographic_hash_algorithms) 和 [hashlib-seealso]({{< ref "/library/crypto/hashlib#hashlib-seealso" >}}) 小节。

> Added in version 3.6:
> 增加了 SHA3 (Keccak) 和 SHAKE 构造器 [`sha3_224()`]({{< ref "/library/crypto/hashlib#hashlib.sha3_224" >}}), [`sha3_256()`]({{< ref "/library/crypto/hashlib#hashlib.sha3_256" >}}), [`sha3_384()`]({{< ref "/library/crypto/hashlib#hashlib.sha3_384" >}}), [`sha3_512()`]({{< ref "/library/crypto/hashlib#hashlib.sha3_512" >}}), [`shake_128()`]({{< ref "/library/crypto/hashlib#hashlib.shake_128" >}}), [`shake_256()`]({{< ref "/library/crypto/hashlib#hashlib.shake_256" >}})。 并增加了 [`blake2b()`]({{< ref "/library/crypto/hashlib#hashlib.blake2b" >}}) 和 [`blake2s()`]({{< ref "/library/crypto/hashlib#hashlib.blake2s" >}})。

*在 3.9 版本发生变更:* 所有 hashlib 的构造器都接受仅限关键字参数 *usedforsecurity* 且其默认值为 `True`。 设为假值即允许在受限的环境中使用不安全且阻塞的哈希算法。 `False` 表示此哈希算法不可用于安全场景，例如用作非加密的单向压缩函数。

*在 3.9 版本发生变更:* 现在 hashlib 会在 OpenSSL 有提供的情况下使用 SHA3 和 SHAKE。

*在 3.12 版本发生变更:* 在所链接的 OpenSSL 未提供 MD5, SHA1, SHA2 或 SHA3 算法的情况下我们将回退至来自 [HACL* project](https://github.com/hacl-star/hacl-star) 的已验证的实现。

## 用法

​	要获取字节串 `b"Nobody inspects the spammish repetition"` 的摘要:



``` python
>>> import hashlib
>>> m = hashlib.sha256()
>>> m.update(b"Nobody inspects")
>>> m.update(b" the spammish repetition")
>>> m.digest()
b'\x03\x1e\xdd}Ae\x15\x93\xc5\xfe\\\x00o\xa5u+7\xfd\xdf\xf7\xbcN\x84:\xa6\xaf\x0c\x95\x0fK\x94\x06'
>>> m.hexdigest()
'031edd7d41651593c5fe5c006fa5752b37fddff7bc4e843aa6af0c950f4b9406'
```

​	更简要的写法：



``` python
>>> hashlib.sha256(b"Nobody inspects the spammish repetition").hexdigest()
'031edd7d41651593c5fe5c006fa5752b37fddff7bc4e843aa6af0c950f4b9406'
```

## 构造器

## hashlib.**new**(*name*, [*data*, ]***, *usedforsecurity=True*)

​	接受想要的算法对应的字符串 *name* 作为其第一个形参的泛型构造器。 它还允许访问上面列出的哈希算法以及你的 OpenSSL 库可能提供的任何其他算法。

​	使用 [`new()`]({{< ref "/library/crypto/hashlib#hashlib.new" >}}) 并附带一个算法名称:



``` python
>>> h = hashlib.new('sha256')
>>> h.update(b"Nobody inspects the spammish repetition")
>>> h.hexdigest()
'031edd7d41651593c5fe5c006fa5752b37fddff7bc4e843aa6af0c950f4b9406'
```

## hashlib.**md5**([*data*, ]***, *usedforsecurity=True*)

## hashlib.**sha1**([*data*, ]***, *usedforsecurity=True*)

## hashlib.**sha224**([*data*, ]***, *usedforsecurity=True*)

## hashlib.**sha256**([*data*, ]***, *usedforsecurity=True*)

## hashlib.**sha384**([*data*, ]***, *usedforsecurity=True*)

## hashlib.**sha512**([*data*, ]***, *usedforsecurity=True*)

## hashlib.**sha3_224**([*data*, ]***, *usedforsecurity=True*)

## hashlib.**sha3_256**([*data*, ]***, *usedforsecurity=True*)

## hashlib.**sha3_384**([*data*, ]***, *usedforsecurity=True*)

## hashlib.**sha3_512**([*data*, ]***, *usedforsecurity=True*)

​	这些带命名的构造器速度相比向 [`new()`]({{< ref "/library/crypto/hashlib#hashlib.new" >}}) 传入算法名称更快。

## 属性

​	在 hashlib 中提供了下列常量模块属性:

## hashlib.**algorithms_guaranteed**

​	一个集合，其中包含此模块在所有平台上都保证支持的哈希算法的名称。 请注意 'md5' 也在此清单中，虽然某些上游厂商提供了一个怪异的排除了此算法的 "FIPS 兼容" Python 编译版本。

> Added in version 3.2.
>

## hashlib.**algorithms_available**

​	一个集合，其中包含在所运行的 Python 解释器上可用的哈希算法的名称。 将这些名称传给 [`new()`]({{< ref "/library/crypto/hashlib#hashlib.new" >}}) 时将可被识别。 [`algorithms_guaranteed`]({{< ref "/library/crypto/hashlib#hashlib.algorithms_guaranteed" >}}) 将总是它的一个子集。 同样的算法在此集合中可能以不同的名称出现多次（这是 OpenSSL 的原因）。

> Added in version 3.2.
>

## 哈希对象

​	下列值会以构造器所返回的哈希对象的常量属性的形式被提供:

## hash.**digest_size**

​	以字节表示的结果哈希对象的大小。

## hash.**block_size**

​	以字节表示的哈希算法的内部块大小。

​	hash 对象具有以下属性：

## hash.**name**

​	此哈希对象的规范名称，总是为小写形式并且总是可以作为 [`new()`]({{< ref "/library/crypto/hashlib#hashlib.new" >}}) 的形参用来创建另一个此类型的哈希对象。

*在 3.4 版本发生变更:* 该属性名称自被引入起即存在于 CPython 中，但在 Python 3.4 之前并未正式指明，因此可能不存在于某些平台上。

​	哈希对象具有下列方法:

## hash.**update**(*data*)

​	用 [bytes-like object]({{< ref "/glossary/idx#term-bytes-like-object" >}}) 来更新哈希对象。 重复调用相当于单次调用并传入所有参数的拼接结果: `m.update(a); m.update(b)` 等价于 `m.update(a+b)`。

## hash.**digest**()

​	返回当前已传给 [`update()`]({{< ref "/library/crypto/hashlib#hashlib.hash.update" >}}) 方法的数据摘要。 这是一个大小为 [`digest_size`]({{< ref "/library/crypto/hashlib#hashlib.hash.digest_size" >}}) 的字节串对象，字节串中可包含 0 至 255 的完整取值范围。

## hash.**hexdigest**()

​	类似于 [`digest()`]({{< ref "/library/crypto/hashlib#hashlib.hash.digest" >}}) 但摘要会以两倍长度字符串对象的形式返回，其中仅包含十六进制数码。 这可以被用于在电子邮件或其他非二进制环境中安全地交换数据值。

## hash.**copy**()

​	返回哈希对象的副本（“克隆”）。 这可被用来高效地计算共享相同初始子串的数据的摘要。

## SHAKE 可变长度摘要

## hashlib.**shake_128**([*data*, ]***, *usedforsecurity=True*)

## hashlib.**shake_256**([*data*, ]***, *usedforsecurity=True*)

[`shake_128()`]({{< ref "/library/crypto/hashlib#hashlib.shake_128" >}}) 和 [`shake_256()`]({{< ref "/library/crypto/hashlib#hashlib.shake_256" >}}) 算法提供安全的 length_in_bits//2 至 128 或 256 位可变长度摘要。 为此，它们的摘要需指定一个长度。 SHAKE 算法不限制最大长度。

## shake.**digest**(*length*)

​	返回当前已传给 [`update()`]({{< ref "/library/crypto/hashlib#hashlib.hash.update" >}}) 方法的数据摘要。 这是一个大小为 *length* 的字节串对象，其中可包含 0 至 255 完整范围内的字节值。

## shake.**hexdigest**(*length*)

​	类似于 [`digest()`]({{< ref "/library/crypto/hashlib#hashlib.shake.digest" >}}) 但摘要会以两倍长度字符串对象的形式返回，其中仅包含十六进制数码。 这可以被用于在电子邮件或其他非二进制环境中安全地交换数据值。

​	用法示例:



``` python
>>> h = hashlib.shake_256(b'Nobody inspects the spammish repetition')
>>> h.hexdigest(20)
'44709d6fcb83d92a76dcb0b668c98e1b1d3dafe7'
```

## 文件哈希

​	hashlib 模块提供了一个辅助函数用于文件或文件型对象的高效哈希操作。

## hashlib.**file_digest**(*fileobj*, *digest*, */*)

​	返回一个根据文件对象进行更新的摘要对象。

*fileobj* 必须是一个以二进制模式打开用于读取的文件型对象。 它接受来自内置 [`open()`]({{< ref "/library/functions#open" >}}), [`BytesIO`]({{< ref "/library/allos/io#io.BytesIO" >}}) 实例, [`socket.socket.makefile()`]({{< ref "/library/ipc/socket#socket.socket.makefile" >}}) 创建的 SocketIO 及其他类似的文件对象。 此函数也可能绕过 Python 的并直接使用来自 [`fileno()`]({{< ref "/library/allos/io#io.IOBase.fileno" >}}) 的文件描述符。 在此函数返回或引发异常之后必须假定 *fileobj* 已处于未知状态。 应当由调用方来负责关闭 *fileobj*。

*digest* 必须是一个 *str* 形式的哈希算法名称、哈希构造器或返回哈希对象的可调用对象。

​	示例:



``` python
>>> import io, hashlib, hmac
>>> with open(hashlib.__file__, "rb") as f:
...     digest = hashlib.file_digest(f, "sha256")
...
>>> digest.hexdigest()  
'...'
```



``` python
>>> buf = io.BytesIO(b"somedata")
>>> mac1 = hmac.HMAC(b"key", digestmod=hashlib.sha512)
>>> digest = hashlib.file_digest(buf, lambda: mac1)
```



``` python
>>> digest is mac1
True
>>> mac2 = hmac.HMAC(b"key", b"somedata", digestmod=hashlib.sha512)
>>> mac1.digest() == mac2.digest()
True
```

> Added in version 3.11.
>

## 密钥派生

​	密钥派生和密钥延展算法被设计用于安全密码哈希。 `sha1(password)` 这样的简单算法无法防御暴力攻击。 好的密码哈希函数必须可以微调、放慢步调，并且包含 [加盐](https://en.wikipedia.org/wiki/Salt_(cryptography))。

## hashlib.**pbkdf2_hmac**(*hash_name*, *password*, *salt*, *iterations*, *dklen=None*)

​	此函数提供 PKCS#5 基于密码的密钥派生函数 2。 它使用 HMAC 作为伪随机函数。

​	字符串 *hash_name* 是要求用于 HMAC 的哈希摘要算法的名称，例如 'sha1' 或 'sha256'。 *password* 和 *salt* 会以字节串缓冲区的形式被解析。 应用和库应当将 *password* 限制在合理长度 (例如 1024)。 *salt* 应当为适当来源例如 [`os.urandom()`]({{< ref "/library/allos/os#os.urandom" >}}) 的大约 16 个或更多的字节串数据。

*iterations* 的数值应当基于哈希算法和机器算力来选择。 在 2022 年，建议选择进行数万次的 SHA-256 迭代。 对于为何以及如何选择最适合你的应用程序的迭代次数的理由，请参阅 [NIST-SP-800-132](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-132.pdf) 的 *Appendix A.2.2*。 其中 [stackexchange pbkdf2 迭代问题](https://security.stackexchange.com/questions/3959/recommended-of-iterations-when-using-pbkdf2-sha256/) 的解答提供的详细的说明。

*dklen* 是以字节数表示的派生密钥长度。 如果 *dklen* 为 `None` 则会使用哈希算法 *hash_name* 的摘要长度，例如对 SHA-512 来说是 64。



``` python
>>> from hashlib import pbkdf2_hmac
>>> our_app_iters = 500_000  # Application specific, read above.
>>> dk = pbkdf2_hmac('sha256', b'password', b'bad salt' * 2, our_app_iters)
>>> dk.hex()
'15530bba69924174860db778f2c6f8104d3aaf9d26241840c8c4a641c8d000a9'
```

​	此函数只有在 Python 附带 OpenSSL 编译时才可用。

> Added in version 3.4.
>

*在 3.12 版本发生变更:* 现在此函数只有在 Python 附带 OpenSSL 构建时才可用。 慢速的纯 Python 实现已被移除。

## hashlib.**scrypt**(*password*, ***, *salt*, *n*, *r*, *p*, *maxmem=0*, *dklen=64*)

​	此函数提供基于密码加密的密钥派生函数，其定义参见 [**RFC 7914**](https://datatracker.ietf.org/doc/html/rfc7914.html)。

*password* 和 *salt* 必须为 [字节类对象]({{< ref "/glossary/idx#term-bytes-like-object" >}})。 应用和库应当将 *password* 限制在合理长度 (例如 1024)。 *salt* 应当为适当来源例如 [`os.urandom()`]({{< ref "/library/allos/os#os.urandom" >}}) 的大约 16 个或更多的字节串数据。

*n* 是 CPU/内存开销因子，*r* 是块大小，*p* 是并行化因子而 *maxmem* 是内存上限（OpenSSL 1.1.0 默认为 32 MiB）。 *dklen* 是以字节数表示的派生密钥长度。

> Added in version 3.6.
>



## BLAKE2

[BLAKE2](https://www.blake2.net/) 是在 [**RFC 7693**](https://datatracker.ietf.org/doc/html/rfc7693.html) 中定义的加密哈希函数，它有两种形式:

- **BLAKE2b**，针对 64 位平台进行优化，并会生成长度介于 1 和 64 字节之间任意大小的摘要。
- **BLAKE2s**，针对 8 至 32 位平台进行优化，并会生成长度介于 1 和 32 字节之间任意大小的摘要。

​	BLAKE2 支持 **keyed mode** ([HMAC](https://en.wikipedia.org/wiki/Hash-based_message_authentication_code) 的更快速更简单的替代), **salted hashing**, **personalization** 和 **tree hashing**.

​	此模块的哈希对象遵循标准库 [`hashlib`]({{< ref "/library/crypto/hashlib#module-hashlib" >}}) 对象的 API。

### 创建哈希对象

​	新哈希对象可通过调用构造器函数来创建:

## hashlib.**blake2b**(*data=b''*, ***, *digest_size=64*, *key=b''*, *salt=b''*, *person=b''*, *fanout=1*, *depth=1*, *leaf_size=0*, *node_offset=0*, *node_depth=0*, *inner_size=0*, *last_node=False*, *usedforsecurity=True*)

## hashlib.**blake2s**(*data=b''*, ***, *digest_size=32*, *key=b''*, *salt=b''*, *person=b''*, *fanout=1*, *depth=1*, *leaf_size=0*, *node_offset=0*, *node_depth=0*, *inner_size=0*, *last_node=False*, *usedforsecurity=True*)

​	这些函数返回用于计算 BLAKE2b 或 BLAKE2s 的相应的哈希对象。 它们接受下列可选通用形参:

- *data*: 要哈希的初始数据块，它必须为 [bytes-like object]({{< ref "/glossary/idx#term-bytes-like-object" >}})。 它只能作为位置参数传入。
- *digest_size*: 以字节数表示的输出摘要大小。
- *key*: 用于密钥哈希的密钥（对于 BLAKE2b 最长 64 字节，对于 BLAKE2s 最长 32 字节）。
- *salt*: 用于随机哈希的盐值（对于 BLAKE2b 最长 16 字节，对于 BLAKE2s 最长 8 字节）。
- *person*: 个性化字符串（对于 BLAKE2b 最长 16 字节，对于 BLAKE2s 最长 8 字节）。

​	下表显示了常规参数的限制（以字节为单位）：

| Hash    | 目标长度 | 长度（键） | 长度（盐） | 长度（个人） |
| :------ | :------- | :--------- | :--------- | :----------- |
| BLAKE2b | 64       | 64         | 16         | 16           |
| BLAKE2s | 32       | 32         | 8          | 8            |

​	备注

 

​	BLAKE2 规格描述为盐值和个性化形参定义了固定的长度，但是为了方便起见，此实现接受指定在长度以内的任意大小的字节串。 如果形参长度小于指定值，它将以零值进行填充，因此举例来说，`b'salt'` 和 `b'salt\x00'` 为相同的值 (*key* 的情况则并非如此。)

​	如下面的模块 [constants]({{< ref "/library/crypto/hashlib#constants" >}}) 所描述，这些是可用的大小取值。

​	构造器函数还接受下列树形哈希形参:

- *fanout*: 扇出值 (0 至 255，如无限制即为 0，连续模式下为 1)。
- *depth*: 树的最大深度 (1 至 255，如无限制则为 255，连续模式下为 1)。
- *leaf_size*: 叶子的最大字节长度 (0 至 `2**32-1`，如无限制或在连续模式下则为 0)。
- *node_offset*: 节点的偏移量 (对于 BLAKE2b 为 0 至 `2**64-1`，对于 BLAKE2s 为 0 至 `2**48-1`，对于最多边的第一个叶子或在连续模式下则为 0)。
- *node_depth*: 节点深度 (0 至 255，对于叶子或在连续模式下则为 0)。
- *inner_size*: 内部摘要大小 (对于 BLAKE2b 为 0 至 64，对于 BLAKE2s 为 0 至 32，连续模式下则为 0)。
- *last_node*: 一个指明所处理的节点是否为最后一个 (在连续模式下为 `False`) 的布尔值。

![树模式形参的说明。](hashlib_img/hashlib-blake2-tree.png)

​	请参阅 [BLAKE2 规格描述](https://www.blake2.net/blake2_20130129.pdf) 第 2.10 节获取有关树形哈希的完整介绍。

### 常量

## blake2b.**SALT_SIZE**

## blake2s.**SALT_SIZE**

​	盐值长度（构造器所接受的最大长度）。

## blake2b.**PERSON_SIZE**

## blake2s.**PERSON_SIZE**

​	个性化字符串长度（构造器所接受的最大长度）。

## blake2b.**MAX_KEY_SIZE**

## blake2s.**MAX_KEY_SIZE**

​	最大密钥长度。

## blake2b.**MAX_DIGEST_SIZE**

## blake2s.**MAX_DIGEST_SIZE**

​	哈希函数可输出的最大摘要长度。

### 例子

#### 简单哈希

​	要计算某个数据的哈希值，你应该首先通过调用适当的构造器函数 ([`blake2b()`]({{< ref "/library/crypto/hashlib#hashlib.blake2b" >}}) 或 [`blake2s()`]({{< ref "/library/crypto/hashlib#hashlib.blake2s" >}})) 来构造一个哈希对象，然后通过在该对象上调用 [`update()`]({{< ref "/library/crypto/hashlib#hashlib.hash.update" >}}) 来更新目标数据，最后再通过调用 [`digest()`]({{< ref "/library/crypto/hashlib#hashlib.hash.digest" >}}) (或针对十六进制编码字符串的 [`hexdigest()`]({{< ref "/library/crypto/hashlib#hashlib.hash.hexdigest" >}})) 来获取该对象的摘要。



``` python
>>> from hashlib import blake2b
>>> h = blake2b()
>>> h.update(b'Hello world')
>>> h.hexdigest()
'6ff843ba685842aa82031d3f53c48b66326df7639a63d128974c5c14f31a0f33343a8c65551134ed1ae0f2b0dd2bb495dc81039e3eeb0aa1bb0388bbeac29183'
```

​	作为快捷方式，你可以直接以位置参数的形式向构造器传入第一个数据块来直接更新:



``` python
>>> from hashlib import blake2b
>>> blake2b(b'Hello world').hexdigest()
'6ff843ba685842aa82031d3f53c48b66326df7639a63d128974c5c14f31a0f33343a8c65551134ed1ae0f2b0dd2bb495dc81039e3eeb0aa1bb0388bbeac29183'
```

​	你可以多次调用 [`hash.update()`]({{< ref "/library/crypto/hashlib#hashlib.hash.update" >}}) 至你所想要的任意次数以迭代地更新哈希值:



``` python
>>> from hashlib import blake2b
>>> items = [b'Hello', b' ', b'world']
>>> h = blake2b()
>>> for item in items:
...     h.update(item)
...
>>> h.hexdigest()
'6ff843ba685842aa82031d3f53c48b66326df7639a63d128974c5c14f31a0f33343a8c65551134ed1ae0f2b0dd2bb495dc81039e3eeb0aa1bb0388bbeac29183'
```

#### 使用不同的摘要大小

​	BLAKE2 具有可配置的摘要大小，对于 BLAKE2b 最多 64 字节，对于 BLAKE2s 最多 32 字节。 例如，要使用 BLAKE2b 来替代 SHA-1 而不改变输出大小，我们可以让 BLAKE2b 产生 20 个字节的摘要:



``` python
>>> from hashlib import blake2b
>>> h = blake2b(digest_size=20)
>>> h.update(b'Replacing SHA1 with the more secure function')
>>> h.hexdigest()
'd24f26cf8de66472d58d4e1b1774b4c9158b1f4c'
>>> h.digest_size
20
>>> len(h.digest())
20
```

​	不同摘要大小的哈希对象具有完全不同的输出（较短哈希值 *并非* 较长哈希值的前缀）；即使输出长度相同，BLAKE2b 和 BLAKE2s 也会产生不同的输出:



``` python
>>> from hashlib import blake2b, blake2s
>>> blake2b(digest_size=10).hexdigest()
'6fa1d8fcfd719046d762'
>>> blake2b(digest_size=11).hexdigest()
'eb6ec15daf9546254f0809'
>>> blake2s(digest_size=10).hexdigest()
'1bf21a98c78a1c376ae9'
>>> blake2s(digest_size=11).hexdigest()
'567004bf96e4a25773ebf4'
```

#### 密钥哈希

​	带密钥的哈希运算可被用于身份验证，作为 [基于哈希的消息验证代码](https://en.wikipedia.org/wiki/HMAC) (HMAC) 的一种更快速更简单的替代。 BLAKE2 可被安全地用于前缀 MAC 模式，这是由于它从 BLAKE 继承而来的不可区分特性。

​	这个例子演示了如何使用密钥 `b'pseudorandom key'` 来为 `b'message data'` 获取一个（十六进制编码的）128 位验证代码:



``` python
>>> from hashlib import blake2b
>>> h = blake2b(key=b'pseudorandom key', digest_size=16)
>>> h.update(b'message data')
>>> h.hexdigest()
'3d363ff7401e02026f4a4687d4863ced'
```

​	作为实际的例子，一个 Web 应用可为发送给用户的 cookies 进行对称签名，并在之后对其进行验证以确保它们没有被篡改:



``` python
>>> from hashlib import blake2b
>>> from hmac import compare_digest
>>>
>>> SECRET_KEY = b'pseudorandomly generated server secret key'
>>> AUTH_SIZE = 16
>>>
>>> def sign(cookie):
...     h = blake2b(digest_size=AUTH_SIZE, key=SECRET_KEY)
...     h.update(cookie)
...     return h.hexdigest().encode('utf-8')
>>>
>>> def verify(cookie, sig):
...     good_sig = sign(cookie)
...     return compare_digest(good_sig, sig)
>>>
>>> cookie = b'user-alice'
>>> sig = sign(cookie)
>>> print("{0},{1}".format(cookie.decode('utf-8'), sig))
user-alice,b'43b3c982cf697e0c5ab22172d1ca7421'
>>> verify(cookie, sig)
True
>>> verify(b'user-bob', sig)
False
>>> verify(cookie, b'0102030405060708090a0b0c0d0e0f00')
False
```

​	即使存在原生的密钥哈希模式，BLAKE2 也同样可在 [`hmac`]({{< ref "/library/crypto/hmac#module-hmac" >}}) 模块的 HMAC 构造过程中使用:



``` python
>>> import hmac, hashlib
>>> m = hmac.new(b'secret key', digestmod=hashlib.blake2s)
>>> m.update(b'message')
>>> m.hexdigest()
'e3c8102868d28b5ff85fc35dda07329970d1a01e273c37481326fe0c861c8142'
```

#### 随机哈希

​	用户可通过设置 *salt* 形参来为哈希函数引入随机化。 随机哈希适用于防止对数字签名中使用的哈希函数进行碰撞攻击。

> ​	随机哈希被设计用来处理当一方（消息准备者）要生成由另一方（消息签名者）进行签名的全部或部分消息的情况。 如果消息准备者能够找到加密哈希函数的碰撞现象（即两条消息产生相同的哈希值），则他们就可以准备将产生相同哈希值和数字签名但却具有不同结果的有意义的消息版本（例如向某个账户转入 $1,000,000 而不是 $10）。 加密哈希函数的设计都是以防碰撞性能为其主要目标之一的，但是当前针对加密哈希函数的集中攻击可能导致特定加密哈希函数所提供的防碰撞性能低于预期。 随机哈希为签名者提供了额外的保护，可以降低准备者在数字签名生成过程中使得两条或更多条消息最终产生相同哈希值的可能性 --- 即使为特定哈希函数找到碰撞现象是可行的。 但是，当消息的所有部分均由签名者准备时，使用随机哈希可能降低数字签名所提供的安全性。
>
> ​	([NIST SP-800-106 "数字签名的随机哈希"](https://csrc.nist.gov/pubs/sp/800/106/final))

​	在 BLAKE2 中，盐值会在初始化期间作为对哈希函数的一次性输入而不是对每个压缩函数的输入来处理。

​	警告

 

​	使用 BLAKE2 或任何其他通用加密哈希函数，例如 SHA-256 进行 *加盐哈希* (或纯哈希) 并不适用于对密码的哈希。 请参阅 [BLAKE2 FAQ](https://www.blake2.net/#qa) 了解更多信息。



``` python
>>> import os
>>> from hashlib import blake2b
>>> msg = b'some message'
>>> # Calculate the first hash with a random salt.
>>> salt1 = os.urandom(blake2b.SALT_SIZE)
>>> h1 = blake2b(salt=salt1)
>>> h1.update(msg)
>>> # Calculate the second hash with a different random salt.
>>> salt2 = os.urandom(blake2b.SALT_SIZE)
>>> h2 = blake2b(salt=salt2)
>>> h2.update(msg)
>>> # The digests are different.
>>> h1.digest() != h2.digest()
True
```

#### 个性化

​	出于不同的目的强制让哈希函数为相同的输入生成不同的摘要有时也是有用的。 正如 Skein 哈希函数的作者所言:

> ​	我们建议所有应用设计者慎重考虑这种做法；我们已看到有许多协议在协议的某一部分中计算出来的哈希值在另一个完全不同的部分中也可以被使用，因为两次哈希计算是针对类似或相关的数据进行的，这样攻击者可以强制应用为相同的输入生成哈希值。 个性化协议中所使用的每个哈希函数将有效地阻止这种类型的攻击。
>
> ​	([Skein 哈希函数族](https://www.schneier.com/wp-content/uploads/2016/02/skein.pdf), p. 21)

​	BLAKE2 可通过向 *person* 参数传入字节串来进行个性化:



``` python
>>> from hashlib import blake2b
>>> FILES_HASH_PERSON = b'MyApp Files Hash'
>>> BLOCK_HASH_PERSON = b'MyApp Block Hash'
>>> h = blake2b(digest_size=32, person=FILES_HASH_PERSON)
>>> h.update(b'the same content')
>>> h.hexdigest()
'20d9cd024d4fb086aae819a1432dd2466de12947831b75c5a30cf2676095d3b4'
>>> h = blake2b(digest_size=32, person=BLOCK_HASH_PERSON)
>>> h.update(b'the same content')
>>> h.hexdigest()
'cf68fb5761b9c44e7878bfb2c4c9aea52264a80b75005e65619778de59f383a3'
```

​	个性化配合密钥模式也可被用来从单个密钥派生出多个不同密钥。



``` python
>>> from hashlib import blake2s
>>> from base64 import b64decode, b64encode
>>> orig_key = b64decode(b'Rm5EPJai72qcK3RGBpW3vPNfZy5OZothY+kHY6h21KM=')
>>> enc_key = blake2s(key=orig_key, person=b'kEncrypt').digest()
>>> mac_key = blake2s(key=orig_key, person=b'kMAC').digest()
>>> print(b64encode(enc_key).decode('utf-8'))
rbPb15S/Z9t+agffno5wuhB77VbRi6F9Iv2qIxU7WHw=
>>> print(b64encode(mac_key).decode('utf-8'))
G9GtHFE1YluXY1zWPlYk1e/nWfu0WSEb0KRcjhDeP/o=
```

#### 树形模式

​	以下是对包含两个叶子节点的最小树进行哈希的例子:

```
  10
 /  \
00  01
```

​	这个例子使用 64 字节内部摘要，返回 32 字节最终摘要:



``` python
>>> from hashlib import blake2b
>>>
>>> FANOUT = 2
>>> DEPTH = 2
>>> LEAF_SIZE = 4096
>>> INNER_SIZE = 64
>>>
>>> buf = bytearray(6000)
>>>
>>> # Left leaf
... h00 = blake2b(buf[0:LEAF_SIZE], fanout=FANOUT, depth=DEPTH,
...               leaf_size=LEAF_SIZE, inner_size=INNER_SIZE,
...               node_offset=0, node_depth=0, last_node=False)
>>> # Right leaf
... h01 = blake2b(buf[LEAF_SIZE:], fanout=FANOUT, depth=DEPTH,
...               leaf_size=LEAF_SIZE, inner_size=INNER_SIZE,
...               node_offset=1, node_depth=0, last_node=True)
>>> # Root node
... h10 = blake2b(digest_size=32, fanout=FANOUT, depth=DEPTH,
...               leaf_size=LEAF_SIZE, inner_size=INNER_SIZE,
...               node_offset=0, node_depth=1, last_node=True)
>>> h10.update(h00.digest())
>>> h10.update(h01.digest())
>>> h10.hexdigest()
'3ad2a9b37c6070e374c7a8c508fe20ca86b6ed54e286e93a0318e95e881db5aa'
```

### 开发人员

[BLAKE2](https://www.blake2.net/) 是由 *Jean-Philippe Aumasson*, *Samuel Neves*, *Zooko Wilcox-O'Hearn* 和 *Christian Winnerlein* 基于 *Jean-Philippe Aumasson*, *Luca Henzen*, *Willi Meier* 和 *Raphael C.-W. Phan* 所创造的 [SHA-3](https://en.wikipedia.org/wiki/Secure_Hash_Algorithms) 入围方案 [BLAKE](https://web.archive.org/web/20200918190133/https://131002.net/blake/) 进行设计的。

​	它使用的核心算法来自由 *Daniel J. Bernstein* 所设计的 [ChaCha](https://cr.yp.to/chacha.html) 加密。

​	stdlib 实现是基于 [pyblake2](https://pythonhosted.org/pyblake2/) 模块的。 它由 *Dmitry Chestnykh* 在 *Samuel Neves* 所编写的 C 实现的基础上编写。 此文档拷贝自 [pyblake2](https://pythonhosted.org/pyblake2/) 并由 *Dmitry Chestnykh* 撰写。

​	C 代码由 *Christian Heimes* 针对 Python 进行了部分的重写。

​	以下公共领域贡献同时适用于 C 哈希函数实现、扩展代码和本文档:

> ​	在法律许可的范围内，作者已将此软件的全部版权以及关联和邻接权利贡献到全球公共领域。 此软件的发布不附带任何担保。
>
> ​	你应该已收到此软件附带的 CC0 公共领域专属证书的副本。 如果没有，请参阅 https://creativecommons.org/publicdomain/zero/1.0/。

​	根据创意分享公共领域贡献 1.0 通用规范，下列人士为此项目的开发提供了帮助或对公共领域的修改作出了贡献:

- *Alexandr Sokolovskiy*

> 参见
>
> 
>
> - 模块 [`hmac`]({{< ref "/library/crypto/hmac#module-hmac" >}})
>
>   使用哈希运算来生成消息验证代码的模块。
>
> - 模块 [`base64`]({{< ref "/library/netdata/base64#module-base64" >}})
>
>   针对非二进制环境对二进制哈希值进行编辑的另一种方式。
>
> - https://nvlpubs.nist.gov/nistpubs/fips/nist.fips.180-4.pdf
>
>   有关安全哈希算法的 FIPS 180-4 发布版。
>
> - https://csrc.nist.gov/pubs/fips/202/final
>
>   关于 SHA-3 标准的 FIPS 202 公告。
>
> - https://www.blake2.net/
>
>   BLAKE2 官方网站
>
> - https://en.wikipedia.org/wiki/Cryptographic_hash_function
>
>   包含关于哪些算法存在已知问题以及对其使用所造成的影响的信息的 Wikipedia 文章。
>
> - https://www.ietf.org/rfc/rfc8018.txt
>
>   PKCS #5: 基于密码的加密规范描述 2.1 版
>
> - https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-132.pdf
>
>   NIST 对基于密码的密钥派生的建议。
