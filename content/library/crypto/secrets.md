+++
title = "secrets --- 生成管理密码的安全随机数"
date = 2024-11-15T12:06:46+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/secrets.html](https://docs.python.org/zh-cn/3.13/library/secrets.html)
>
> 收录该文档的时间：`2024-11-15T12:06:46+08:00`

# `secrets` --- 生成管理密码的安全随机数

> Added in version 3.6.
>

**源代码:** [Lib/secrets.py](https://github.com/python/cpython/tree/3.13/Lib/secrets.py)

------

[`secrets`](https://docs.python.org/zh-cn/3.13/library/secrets.html#module-secrets) 模块用于生成高度加密的随机数，适于管理密码、账户验证、安全凭据及机密数据。

​	最好用 [`secrets`](https://docs.python.org/zh-cn/3.13/library/secrets.html#module-secrets) 替代 [`random`](https://docs.python.org/zh-cn/3.13/library/random.html#module-random) 模块的默认伪随机数生成器，该生成器适用于建模和模拟，不宜用于安全与加密。

​	参见

 

[**PEP 506**](https://peps.python.org/pep-0506/)

## 随机数

[`secrets`](https://docs.python.org/zh-cn/3.13/library/secrets.html#module-secrets) 模块是操作系统提供的最安全地随机性来源。

## *class* secrets.**SystemRandom**

​	用操作系统提供的最高质量源生成随机数的类。详见 [`random.SystemRandom`](https://docs.python.org/zh-cn/3.13/library/random.html#random.SystemRandom)。

## secrets.**choice**(*seq*)

​	返回一个从非空序列中随机选取的元素。

## secrets.**randbelow**(*exclusive_upper_bound*)

​	返回 [0, *exclusive_upper_bound*) 范围内的随机整数。

## secrets.**randbits**(*k*)

​	返回有 *k* 个随机比特位的非负数。

## 生成 Token

[`secrets`](https://docs.python.org/zh-cn/3.13/library/secrets.html#module-secrets) 模块提供了生成安全 Token 的函数，适用于密码重置、密保 URL 等应用场景。

## secrets.**token_bytes**([*nbytes=None*])

​	返回含 *nbytes* 个字节的随机字节字符串。如果未提供 *nbytes*，或*nbytes* 为 `None`，则使用合理的默认值。



``` python
>>> token_bytes(16)  
b'\xebr\x17D*t\xae\xd4\xe3S\xb6\xe2\xebP1\x8b'
```

## secrets.**token_hex**([*nbytes=None*])

​	返回十六进制随机文本字符串。字符串有 *nbytes* 个随机字节，每个字节转换为两个十六进制数码。未提供 *nbytes* 或为 `None` 时，则使用合理的默认值。



``` python
>>> token_hex(16)  
'f9bf78b9a18ce6d46a0cd2b0b86df9da'
```

## secrets.**token_urlsafe**([*nbytes=None*])

​	返回安全的 URL 随机文本字符串，包含 *nbytes* 个随机字节。文本用 Base64 编码，平均来说，每个字节对应 1.3 个结果字符。未提供 *nbytes* 或为 `None` 时，则使用合理的默认值。



``` python
>>> token_urlsafe(16)  
'Drmhze6EPcv0fN_81Bj-nA'
```

### Token 应当使用多少个字节？

​	为了在面对 [暴力攻击](https://en.wikipedia.org/wiki/Brute-force_attack) 时保证安全，Token 的随机性必须足够高。随着计算机推衍能力的不断提升，随机性的安全标准也要不断提高。比如 2015 年，32 字节（256 位）的随机性对于 [`secrets`](https://docs.python.org/zh-cn/3.13/library/secrets.html#module-secrets) 模块的典型用例就已经足够了。

​	要自行管理 Token 长度的用户，可以通过为 `token_*` 函数指定 [`int`](https://docs.python.org/zh-cn/3.13/library/functions.html#int) 参数显式指定 Token 要使用多大的随机性。该参数以字节数表示随机性大小。

​	反之，如果未提供参数，或参数为 `None`，则 `token_*` 函数将使用合理的默认值。

​	备注

 

​	该默认值随时可能会改变，比如，版本更新的时候。

## 其他功能

## secrets.**compare_digest**(*a*, *b*)

​	如果字符串或 [字节型对象](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object) *a* 与 *b* 相等则返回 `True`，否则返回 `False`，使用了“常数时间比较”来降低 [定时攻击](https://codahale.com/a-lesson-in-timing-attacks/) 的风险。请参阅 [`hmac.compare_digest()`](https://docs.python.org/zh-cn/3.13/library/hmac.html#hmac.compare_digest) 了解更多细节。

## 应用技巧与最佳实践

​	本节展示了一些使用 [`secrets`](https://docs.python.org/zh-cn/3.13/library/secrets.html#module-secrets) 管理基本安全级别的应用技巧和最佳实践。

​	生成长度为八个字符的字母数字密码：

```
import string
import secrets
alphabet = string.ascii_letters + string.digits
password = ''.join(secrets.choice(alphabet) for i in range(8))
```

​	备注

 

​	应用程序不应该 [**以可恢复的格式存储密码**](https://cwe.mitre.org/data/definitions/257.html)，无论是纯文本的还是加密的。 它们应当使用高加密强度的单向（不可逆）哈希函数加盐并执行哈希运算。

​	生成长度为十个字符的字母数字密码，包含至少一个小写字母，至少一个大写字母以及至少三个数字：

```
import string
import secrets
alphabet = string.ascii_letters + string.digits
while True:
    password = ''.join(secrets.choice(alphabet) for i in range(10))
    if (any(c.islower() for c in password)
            and any(c.isupper() for c in password)
            and sum(c.isdigit() for c in password) >= 3):
        break
```

​	生成 [XKCD 风格的密码串](https://xkcd.com/936/)：

```
import secrets
# 在标准 Linux 系统中，使用方便的字典文件。
# 其他系统平台可能需要提供它们专用的词列表。
with open('/usr/share/dict/words') as f:
    words = [word.strip() for word in f]
    password = ' '.join(secrets.choice(words) for i in range(4))
```

​	生成临时密保 URL，包含密码恢复应用的安全 Token：

```
import secrets
url = 'https://example.com/reset=' + secrets.token_urlsafe()
```
