+++
title = "hmac --- 用于消息验证的密钥哈希"
date = 2024-11-15T12:06:46+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/hmac.html](https://docs.python.org/zh-cn/3.13/library/hmac.html)
>
> 收录该文档的时间：`2024-11-15T12:06:46+08:00`

# `hmac` --- 用于消息验证的密钥哈希

**源代码:** [Lib/hmac.py](https://github.com/python/cpython/tree/3.13/Lib/hmac.py)

------

​	此模块实现了 HMAC 算法，算法的描述参见 [**RFC 2104**](https://datatracker.ietf.org/doc/html/rfc2104.html)。

## hmac.**new**(*key*, *msg=None*, *digestmod*)

​	返回一个新的 hmac 对象。 *key* 是一个指定密钥的 bytes 或 bytearray 对象。 如果提供了 *msg*，将会调用 `update(msg)` 方法。 *digestmod* 为 HMAC 对象所用的摘要名称、摘要构造器或模块。 它可以是适用于 [`hashlib.new()`](https://docs.python.org/zh-cn/3.13/library/hashlib.html#hashlib.new) 的任何名称。 虽然该参数位置靠后，但它却是必须的。

*在 3.4 版本发生变更:* 形参 *key* 可以为 bytes 或 bytearray 对象。 形参 *msg* 可以为 [`hashlib`](https://docs.python.org/zh-cn/3.13/library/hashlib.html#module-hashlib) 所支持的任意类型。 形参 *digestmod* 可以为某种哈希算法的名称。

*在 3.8 版本发生变更:* *digestmod* 参数现在是必须的。 请将其作为关键字参数传入以避免当你没有初始 *msg* 时将导致的麻烦。

## hmac.**digest**(*key*, *msg*, *digest*)

​	基于给定密钥 *key* 和 *digest* 返回 *msg* 的摘要。 此函数等价于 `HMAC(key, msg, digest).digest()`，但使用了优化的 C 或内联实现，对放入内存的消息能处理得更快。 形参 *key*, *msg* 和 *digest* 具有与 [`new()`](https://docs.python.org/zh-cn/3.13/library/hmac.html#hmac.new) 中相同的含义。

​	作为 CPython 的实现细节，优化的 C 实现仅当 *digest* 为字符串并且是一个 OpenSSL 所支持的摘要算法的名称时才会被使用。

*Added in version 3.7.*

​	HMAC 对象具有下列方法:

## HMAC.**update**(*msg*)

​	用 *msg* 来更新 hmac 对象。 重复调用相当于单次调用并传入所有参数的拼接结果: `m.update(a); m.update(b)` 等价于 `m.update(a + b)`。

*在 3.4 版本发生变更:* 形参 *msg* 可以为 [`hashlib`](https://docs.python.org/zh-cn/3.13/library/hashlib.html#module-hashlib) 所支持的任何类型。

## HMAC.**digest**()

​	返回当前已传给 [`update()`](https://docs.python.org/zh-cn/3.13/library/hmac.html#hmac.HMAC.update) 方法的字节串数据的摘要。 这个字节串数据的长度将与传给构造器的摘要的长度 *digest_size* 相同。 它可以包含非 ASCII 的字节，包括 NUL 字节。

​	警告

 

​	在验证例程运行期间将 [`digest()`](https://docs.python.org/zh-cn/3.13/library/hmac.html#hmac.digest) 的输出与外部提供的摘要进行比较时，建议使用 [`compare_digest()`](https://docs.python.org/zh-cn/3.13/library/hmac.html#hmac.compare_digest) 函数而不是 `==` 运算符以减少面对定时攻击的弱点。

## HMAC.**hexdigest**()

​	类似于 [`digest()`](https://docs.python.org/zh-cn/3.13/library/hmac.html#hmac.digest) 但摘要会以两倍长度字符串的形式返回，其中仅包含十六进制数码。 这可以被用于在电子邮件或其他非二进制环境中安全地交换数据值。

​	警告

 

​	在验证例程运行期间将 [`hexdigest()`](https://docs.python.org/zh-cn/3.13/library/hmac.html#hmac.HMAC.hexdigest) 的输出与外部提供的摘要进行比较时，建议使用 [`compare_digest()`](https://docs.python.org/zh-cn/3.13/library/hmac.html#hmac.compare_digest) 函数而不是 `==` 运算符以减少面对定时攻击的弱点。

## HMAC.**copy**()

​	返回 hmac 对象的副本（“克隆）。 这可被用来高效地计算共享相同初始子串的数据的摘要。

​	hash 对象具有以下属性：

## HMAC.**digest_size**

​	以字节表示的结果 HMAC 摘要的大小。

## HMAC.**block_size**

​	以字节表示的哈希算法的内部块大小。

*Added in version 3.4.*

## HMAC.**name**

​	HMAC 的规范名称，总是为小写形式，例如 `hmac-md5`。

*Added in version 3.4.*

*在 3.10 版本发生变更:* 移除了未写入文档的属性 `HMAC.digest_cons`, `HMAC.inner` 和 `HMAC.outer`。

​	这个模块还提供了下列辅助函数:

## hmac.**compare_digest**(*a*, *b*)

​	返回 `a == b`。 此函数使用一种经专门设计的方式通过避免基于内容的短路行为来防止定时分析，使得它适合处理密码。 *a* 和 *b* 必须为相同的类型：或者是 [`str`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) (仅限 ASCII 字符，如 [`HMAC.hexdigest()`](https://docs.python.org/zh-cn/3.13/library/hmac.html#hmac.HMAC.hexdigest) 的返回值)，或者是 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object)。

​	备注

 

​	如果 *a* 和 *b* 具有不同的长度，或者如果发生了错误，定时攻击在理论上可以获取有关 *a* 和 *b* 的类型和长度信息 — 但不能获取它们的值。

*Added in version 3.3.*

*在 3.10 版本发生变更:* 此函数在可能的情况下会在内部使用 OpenSSL 的 `CRYPTO_memcmp()`。

​	参见

## 模块 [`hashlib`](https://docs.python.org/zh-cn/3.13/library/hashlib.html#module-hashlib)

​	提供安全哈希函数的 Python 模块。
