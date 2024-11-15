+++
title = "ipaddress --- IPv4/IPv6 操作库"
date = 2024-11-15T20:28:46+08:00
weight = 220
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/ipaddress.html](https://docs.python.org/zh-cn/3.13/library/ipaddress.html)
>
> 收录该文档的时间：`2024-11-15T20:28:46+08:00`

# `ipaddress` --- IPv4/IPv6 操作库

**源代码：** [Lib/ipaddress.py](https://github.com/python/cpython/tree/3.13/Lib/ipaddress.py)

------

[`ipaddress`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#module-ipaddress) 提供了创建、处理和操作 IPv4 和 IPv6 地址和网络的功能。

​	该模块中的函数和类可以直接处理与IP地址相关的各种任务，包括检查两个主机是否在同一个子网中，遍历某个子网中的所有主机，检查一个字符串是否是一个有效的IP地址或网络定义等等。

​	这是完整的模块 API 参考—若要查看概述，请见 [ipaddress模块介绍](https://docs.python.org/zh-cn/3.13/howto/ipaddress.html#ipaddress-howto).

*Added in version 3.3.*

## 方便的工厂函数

[`ipaddress`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#module-ipaddress) 模块提供来工厂函数来方便地创建 IP 地址，网络和接口：

## ipaddress.**ip_address**(*address*)

​	返回一个 [`IPv4Address`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Address) 或 [`IPv6Address`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv6Address) 对象，取决于作为参数传递的 IP 地址。可以提供IPv4或IPv6地址，小于 `2**32` 的整数默认被认为是 IPv4。如果 *address* 不是有效的 IPv4 或 IPv6 地址，则会抛出 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

\>>>

```
>>> ipaddress.ip_address('192.168.0.1')
IPv4Address('192.168.0.1')
>>> ipaddress.ip_address('2001:db8::')
IPv6Address('2001:db8::')
```

## ipaddress.**ip_network**(*address*, *strict=True*)

​	返回一个 [`IPv4Network`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Network) 或 [`IPv6Network`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv6Network) 对象，具体取决于作为参数传入的 IP 地址。 *address* 是表示 IP 网址的字符串或整数。 可以提供 IPv4 或 IPv6 网址；小于 `2**32` 的整数默认被视为 IPv4。 *strict* 会被传给 [`IPv4Network`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Network) 或 [`IPv6Network`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv6Network) 构造器。 如果 *address* 不表示有效的 IPv4 或 IPv6 网址，或者网络设置了 host 比特位，则会引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

\>>>

```
>>> ipaddress.ip_network('192.168.0.0/28')
IPv4Network('192.168.0.0/28')
```

## ipaddress.**ip_interface**(*address*)

​	返回一个 [`IPv4Interface`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Interface) 或 [`IPv6Interface`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv6Interface) 对象，取决于作为参数传递的 IP 地址。 *address* 是代表 IP 地址的字符串或整数。 可以提供 IPv4 或 IPv6 地址，小于 `2**32` 的整数默认认为是 IPv4。 如果 *address* 不是有效的IPv4 或 IPv6 地址，则会抛出一个 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

​	这些方便的函数的一个缺点是需要同时处理IPv4和IPv6格式，这意味着提供的错误信息并不精准，因为函数不知道是打算采用IPv4还是IPv6格式。更详细的错误报告可以通过直接调用相应版本的类构造函数来获得。

## IP 地址

### 地址对象

[`IPv4Address`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Address) 和 [`IPv6Address`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv6Address) 对象有很多共同的属性。一些只对IPv6 地址有意义的属性也在 [`IPv4Address`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Address) 对象实现，以便更容易编写正确处理两种 IP 版本的代码。地址对象是可哈希的 [hashable](https://docs.python.org/zh-cn/3.13/glossary.html#term-hashable)，所以它们可以作为字典中的键来使用。

## *class* ipaddress.**IPv4Address**(*address*)

​	构造一个 IPv4 地址。 如果 *address* 不是一个有效的 IPv4 地址，会抛出 [`AddressValueError`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.AddressValueError) 。

​	以下是有效的 IPv4 地址：

1. 以十进制小数点表示的字符串，由四个十进制整数组成，范围为0--255，用点隔开(例如 `192.168.0.1` )。每个整数代表地址中的八位（一个字节）。不允许使用前导零，以免与八进制表示产生歧义。
2. 一个32位可容纳的整数。
3. 一个长度为 4 的封装在 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 对象中的整数(高位优先)。

\>>>

```
>>> ipaddress.IPv4Address('192.168.0.1')
IPv4Address('192.168.0.1')
>>> ipaddress.IPv4Address(3232235521)
IPv4Address('192.168.0.1')
>>> ipaddress.IPv4Address(b'\xC0\xA8\x00\x01')
IPv4Address('192.168.0.1')
```

*在 3.8 版本发生变更:* 前导零可被接受，即使是在可能与八进制表示混淆的情况下也会被接受。

*在 3.9.5 版本发生变更:* 前导零不再被接受，并且会被视作错误。IPv4地址字符串现在严格按照glibc的 [`inet_pton()`](https://docs.python.org/zh-cn/3.13/library/socket.html#socket.inet_pton) 函数进行解析。

## **version**

​	合适的版本号：IPv4为 `4` ，IPv6为 `6` 。

## **max_prefixlen**

​	在该版本的地址表示中，比特数的总数：IPv4为 `32` ；IPv6为 `128` 。

​	前缀定义了地址中的前导位数量，通过比较来确定一个地址是否是网络的一部分。

## **compressed**

## **exploded**

​	以点符号分割十进制表示的字符串。表示中不包括前导零。

​	由于IPv4没有为八位数设为零的地址定义速记符号，这两个属性始终与IPv4地址的 `str(addr)` 相同。暴露这些属性使得编写能够处理IPv4和IPv6地址的显示代码变得更加容易。

## **packed**

​	这个地址的二进制表示——一个适当长度的 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 对象（最高的八位在最前）。 对于 IPv4 来说是 4 字节，对于 IPv6 来说是 16 字节。

## **reverse_pointer**

​	IP地址的反向DNS PTR记录的名称，例如:

\>>>

```
>>> ipaddress.ip_address("127.0.0.1").reverse_pointer
'1.0.0.127.in-addr.arpa'
>>> ipaddress.ip_address("2001:db8::1").reverse_pointer
'1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.8.b.d.0.1.0.0.2.ip6.arpa'
```

​	这是可用于执行PTR查询的名称，而不是已解析的主机名本身。

*Added in version 3.5.*

## **is_multicast**

​	如果该地址被保留用作多播用途，返回 `True` 。关于多播地址，请参见 [**RFC 3171**](https://datatracker.ietf.org/doc/html/rfc3171.html) （IPv4）和 [**RFC 2373**](https://datatracker.ietf.org/doc/html/rfc2373.html) （IPv6）。

## **is_private**

​	如果地址被 [iana-ipv4-special-registry](https://www.iana.org/assignments/iana-ipv4-special-registry/iana-ipv4-special-registry.xhtml) (针对 IPv4) 或 [iana-ipv6-special-registry](https://www.iana.org/assignments/iana-ipv6-special-registry/iana-ipv6-special-registry.xhtml) (针对 IPv6) 定义为非全局可用则为 `True`，例外情况如下：

- 对于共享地址空间 (`100.64.0.0/10`) `is_private` 为 `False`

- 对于映射 IPv4 的 IPv6 地址来说 `is_private` 值由下层 IPv4 地址的语义决定并且以下条件将保持成立 (见 [`IPv6Address.ipv4_mapped`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv6Address.ipv4_mapped)):

  ```
  address.is_private == address.ipv4_mapped.is_private
  ```

`is_private` 具有与 [`is_global`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Address.is_global) 相反的值，除了对于共享的地址空间（即 `100.64.0.0/10` 范围）来说它们均为 `False`。

*在 3.13 版本发生变更:* 修复了一些错误的正值和错误的负值。

- `192.0.0.0/24` 被视为私有但 `192.0.0.9/32` 和 `192.0.0.10/32` 除外（之前：只有 `192.0.0.0/29` 子范围被视为私有）。
- `64:ff9b:1::/48` 被视为私有。
- `2002::/16` 被视为私有。
- 在 `2001::/23` 中存在例外（其余范围被视为私有）: `2001:1::1/128`, `2001:1::2/128`, `2001:3::/32`, `2001:4:112::/48`, `2001:20::/28`, `2001:30::/28`。 这些例外不被视为私有。

## **is_global**

​	如果地址被 [iana-ipv4-special-registry](https://www.iana.org/assignments/iana-ipv4-special-registry/iana-ipv4-special-registry.xhtml) (针对 IPv4) 或 [iana-ipv6-special-registry](https://www.iana.org/assignments/iana-ipv6-special-registry/iana-ipv6-special-registry.xhtml) (针对 IPv6) 定义为全局可用则为 `True`，例外情况如下：

​	对于映射 IPv4 的 IPv6 地址来说 `is_private` 值由下层 IPv4 地址的语义决定并且以下条件将保持成立 (见 [`IPv6Address.ipv4_mapped`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv6Address.ipv4_mapped)):

```
address.is_global == address.ipv4_mapped.is_global
```

`is_global` 具有与 [`is_private`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Address.is_private) 相反的值，除了对于共享的地址空间（即 `100.64.0.0/10` 范围）来说它们均为 `False`。

*Added in version 3.4.*

*在 3.13 版本发生变更:* 修复了一些错误的正值和错误的负值，请参阅 [`is_private`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Address.is_private) 了解详情。

## **is_unspecified**

​	当地址未指定时为 `True` 。 参见 [**RFC 5735**](https://datatracker.ietf.org/doc/html/rfc5735.html) (IPv4) 或 [**RFC 2373**](https://datatracker.ietf.org/doc/html/rfc2373.html) (IPv6).

## **is_reserved**

​	如果该地址属于互联网工程任务组（IETF）所规定的其他保留地址，返回 `True` 。

## **is_loopback**

​	如果该地址为一个回环地址，返回 `True` 。关于回环地址，请见 [**RFC 3330**](https://datatracker.ietf.org/doc/html/rfc3330.html) （IPv4）和 [**RFC 2373**](https://datatracker.ietf.org/doc/html/rfc2373.html) （IPv6）。

## **is_link_local**

​	如果该地址被保留用于本地链接则为 `True`。 参见 [**RFC 3927**](https://datatracker.ietf.org/doc/html/rfc3927.html)。

## **ipv6_mapped**

[`IPv4Address`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Address) 对象代表已映射 IPv4 的 IPv6 地址。 参见 [**RFC 4291**](https://datatracker.ietf.org/doc/html/rfc4291.html)。

*Added in version 3.13.*

## IPv4Address.**__format__**(*fmt*)

​	返回一个IP地址的字符串表示，由一个明确的格式字符串控制。*fmt* 可以是以下之一: `'s'`，默认选项，相当于 [`str()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)，`'b'` 用于零填充的二进制字符串，`'X'` 或者 `'x'` 用于大写或小写的十六进制表示，或者 `'n'` 相当于 `'b'` 用于 IPv4 地址和 `'x'` 用于 IPv6 地址。 对于二进制和十六进制表示法，可以使用形式指定器 `'#'` 和分组选项 `'_'`。 `__format__` 被 `format`、 `str.format` 和 f 字符串使用。

\>>>

```
>>> format(ipaddress.IPv4Address('192.168.0.1'))
'192.168.0.1'
>>> '{:#b}'.format(ipaddress.IPv4Address('192.168.0.1'))
'0b11000000101010000000000000000001'
>>> f'{ipaddress.IPv6Address("2001:db8::1000"):s}'
'2001:db8::1000'
>>> format(ipaddress.IPv6Address('2001:db8::1000'), '_X')
'2001_0DB8_0000_0000_0000_0000_0000_1000'
>>> '{:#_n}'.format(ipaddress.IPv6Address('2001:db8::1000'))
'0x2001_0db8_0000_0000_0000_0000_0000_1000'
```

*Added in version 3.9.*

## *class* ipaddress.**IPv6Address**(*address*)

​	构造一个 IPv6 地址。 如果 *address* 不是一个有效的 IPv6 地址，会抛出 [`AddressValueError`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.AddressValueError) 。

​	以下是有效的 IPv6 地址：

1. 一个由八组四个16进制数字组成的字符串, 每组展示为16位. 以冒号分隔. 这可以描述为 *分解* (普通书写). 此字符可以被 *压缩* (速记书写) . 详见 [**RFC 4291**](https://datatracker.ietf.org/doc/html/rfc4291.html) . 例如, `"0000:0000:0000:0000:0000:0abc:0007:0def"` 可以被精简为 `"::abc:7:def"`.

   可选择的是，该字符串也可以有一个作用域ID，用后缀 `%scope_id` 表示。如果存在，作用域ID必须是非空的，并且不能包含 `%`。详见 [**RFC 4007**](https://datatracker.ietf.org/doc/html/rfc4007.html)。例如，`fe80::1234%1` 可以识别节点第一条链路上的地址 `fe80::1234`

2. 适合 128 位的整数.

3. 一个打包在长度为 16 字节的大端序 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 对象中的整数。

\>>>

```
>>> ipaddress.IPv6Address('2001:db8::1000')
IPv6Address('2001:db8::1000')
>>> ipaddress.IPv6Address('ff02::5678%1')
IPv6Address('ff02::5678%1')
```

## **compressed**

​	地址表示的简短形式，省略了组中的前导零，完全由零组成的最长的组序列被折叠成一个空组。

​	这也是 `str(addr)` 对IPv6地址返回的值。

## **exploded**

​	地址的长形式表示，包括所有前导零和完全由零组成的组。

​	关于以下属性和方法，请参见 [`IPv4Address`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Address) 类的相应文档。

## **packed**

## **reverse_pointer**

## **version**

## **max_prefixlen**

## **is_multicast**

## **is_private**

## **is_global**

*Added in version 3.4.*

## **is_unspecified**

## **is_reserved**

## **is_loopback**

## **is_link_local**

## **is_site_local**

​	如果地址被保留用于本地站点则为 `True`。 请注意本地站点地址空间已被 [**RFC 3879**](https://datatracker.ietf.org/doc/html/rfc3879.html) 弃用。 请使用 [`is_private`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Address.is_private) 来检测此地址是否位于 [**RFC 4193**](https://datatracker.ietf.org/doc/html/rfc4193.html) 所定义的本地唯一地址空间中。

## **ipv4_mapped**

​	地址为映射的IPv4地址 (起始为 `::FFFF/96`), 此属性记录为嵌入IPv4地址. 其他的任何地址, 此属性为 `None`.

## **scope_id**

​	对于 [**RFC 4007**](https://datatracker.ietf.org/doc/html/rfc4007.html) 定义的作用域地址，此属性以字符串的形式确定地址所属的作用域的特定区域。当没有指定作用域时，该属性将是 `None`。

## **sixtofour**

​	对于看起来是6to4的地址（以 `2002::/16``开头），如 :RFC:`3056` 所定义的，此属性将返回嵌入的IPv4地址。 对于任何其他地址，该属性将是``None`。

## **teredo**

​	对于看起来是 [**RFC 4380**](https://datatracker.ietf.org/doc/html/rfc4380.html) 定义的Teredo地址（以 `2001::/32` 开头）的地址，此属性将返回嵌入式IP地址对 `(server, client)`。 对于任何其他地址，该属性将是 `None`。

## IPv6Address.**__format__**(*fmt*)

​	请参考 [`IPv4Address`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Address) 中对应的方法文档。

*Added in version 3.9.*

### 转换字符串和整数

​	与网络模块互操作像套接字模块, 地址必须转换为字符串或整数. 这是使用 [`str()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 和 [`int()`](https://docs.python.org/zh-cn/3.13/library/functions.html#int) 内置函数:

\>>>

```
>>> str(ipaddress.IPv4Address('192.168.0.1'))
'192.168.0.1'
>>> int(ipaddress.IPv4Address('192.168.0.1'))
3232235521
>>> str(ipaddress.IPv6Address('::1'))
'::1'
>>> int(ipaddress.IPv6Address('::1'))
1
```

​	请注意，IPv6范围内的地址被转换为没有范围区域ID的整数。

### 运算符

​	地址对象支持一些运算符。 除非另有说明，运算符只能在兼容对象之间应用（即IPv4与IPv4，IPv6与IPv6）。

#### 比较运算符

​	地址对象可以用通常的一组比较运算符进行比较。具有不同范围区域ID的相同IPv6地址是不平等的。一些例子:

\>>>

```
>>> IPv4Address('127.0.0.2') > IPv4Address('127.0.0.1')
True
>>> IPv4Address('127.0.0.2') == IPv4Address('127.0.0.1')
False
>>> IPv4Address('127.0.0.2') != IPv4Address('127.0.0.1')
True
>>> IPv6Address('fe80::1234') == IPv6Address('fe80::1234%1')
False
>>> IPv6Address('fe80::1234%1') != IPv6Address('fe80::1234%2')
True
```

#### 算术运算符

​	整数可以被添加到地址对象或从地址对象中减去。 一些例子:

\>>>

```
>>> IPv4Address('127.0.0.2') + 3
IPv4Address('127.0.0.5')
>>> IPv4Address('127.0.0.2') - 3
IPv4Address('126.255.255.255')
>>> IPv4Address('255.255.255.255') + 1
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ipaddress.AddressValueError: 4294967296 (>= 2**32) is not permitted as an IPv4 address
```

## IP网络的定义

[`IPv4Network`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Network) 和 [`IPv6Network`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv6Network) 对象提供了一个定义和检查IP网络定义的机制。一个网络定义由一个 *掩码* 和一个 *网络地址* 组成，因此定义了一个IP地址的范围，当用掩码屏蔽（二进制AND）时，等于网络地址。 例如，一个带有掩码 `255.255.255.0` 和网络地址 `192.168.1.0` 的网络定义由包括 `192.168.1.0` 到 `192.168.1.255` 的IP地址组成。

### 前缀、网络掩码和主机掩码

​	有几种相等的方法来指定IP网络掩码。 *前缀* `/<nbits>` 是一个符号，表示在网络掩码中设置了多少个高阶位。 一个 *网络掩码* 是一个设置了一定数量高阶位的IP地址。 因此，前缀 `/24` 等同于IPv4中的网络掩码 `255.255.255.0` 或IPv6中的网络掩码 `ffff:ff00::` 。 此外，*主机掩码* 是 *网络掩码* 的逻辑取反，有时被用来表示网络掩码（例如在Cisco访问控制列表中）。 在IPv4中，相当于主机掩码 `0.0.0.255` 的是 `/24` 。

### 网络对象

​	所有由地址对象实现的属性也由网络对象实现。 此外，网络对象还实现了额外的属性。所有这些在 [`IPv4Network`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Network) 和 [`IPv6Network`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv6Network) 之间是共同的，所以为了避免重复，它们只在 [`IPv4Network`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Network) 中记录。网络对象是 [hashable](https://docs.python.org/zh-cn/3.13/glossary.html#term-hashable)，所以它们可以作为字典中的键使用。

## *class* ipaddress.**IPv4Network**(*address*, *strict=True*)

​	构建一个 IPv4 网络定义。 *address* 可以是以下之一：

1. 一个由 IP 地址和可选掩码组成的字符串，用斜线 (`/`) 分开。 IP 地址是网络地址，掩码可以是一个单一的数字，这意味着它是一个 *前缀*，或者是一个 IPv4 地址的字符串表示。 如果是后者，如果掩码以非零字段开始，则被解释为 *网络掩码*，如果以零字段开始，则被解释为 *主机掩码*，唯一的例外是全零的掩码被视为 *网络掩码*。 如果没有提供掩码，它就被认为是 `/32`。

   例如，以下的*地址*描述是等同的：`192.168.1.0/24`，`192.168.1.0/255.255.255.0``和``192.168.1.0/0.0.0.255`

2. 一个可转化为32位的整数。 这相当于一个单地址的网络，网络地址是*address*，掩码是 `/32`。

3. 一个整数被打包成一个长度为 4 的大端序 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 对象。 其解释类似于一个整数 *address*。

4. 一个地址描述和一个网络掩码的双元组，其中地址描述是一个字符串，一个 32 位的整数，一个 4 字节的打包整数，或一个现有的 IPv4Address 对象；而网络掩码是一个代表前缀长度的整数 (例如 `24`) 或一个代表前缀掩码的字符串 (例如 `255.255.255.0`)。

​	如果 *address* 不是一个有效的 IPv4 地址则会引发 [`AddressValueError`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.AddressValueError)。 如果掩码不是有效的 IPv4 地址则会引发 [`NetmaskValueError`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.NetmaskValueError)。

​	如果 *strict* 是 `True`，并且在提供的地址中设置了主机位，那么 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError) 将被触发。 否则，主机位将被屏蔽掉，以确定适当的网络地址。

​	除非另有说明，如果参数的 IP 版本与 `self` 不兼容，所有接受其他网络/地址对象的网络方法都将引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。

*在 3.5 版本发生变更:* 为*address*构造函数参数添加了双元组形式。

## **version**

## **max_prefixlen**

​	请参考 [`IPv4Address`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Address) 中的相应属性文档。

## **is_multicast**

## **is_private**

## **is_unspecified**

## **is_reserved**

## **is_loopback**

## **is_link_local**

​	如果这些属性对网络地址和广播地址都是True，那么它们对整个网络来说就是True。

## **network_address**

​	网络的网络地址。网络地址和前缀长度一起唯一地定义了一个网络。

## **broadcast_address**

​	网络的广播地址。发送到广播地址的数据包应该被网络上的每台主机所接收。

## **hostmask**

​	主机掩码，作为一个 [`IPv4Address`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Address) 对象。

## **netmask**

​	网络掩码，作为一个 [`IPv4Address`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Address) 对象。

## **with_prefixlen**

## **compressed**

## **exploded**

​	网络的字符串表示，其中掩码为前缀符号。

`with_prefixlen` 和 `compressed` 总是与 `str(network)` 相同，`exploded` 使用分解形式的网络地址。

## **with_netmask**

​	网络的字符串表示，掩码用网络掩码符号表示。

## **with_hostmask**

​	网络的字符串表示，其中的掩码为主机掩码符号。

## **num_addresses**

​	网络中的地址总数。

## **prefixlen**

​	网络前缀的长度，以比特为单位。

## **hosts**()

​	返回一个网络中可用主机的迭代器。 可用的主机是属于该网络的所有IP地址，除了网络地址本身和网络广播地址。 对于掩码长度为31的网络，网络地址和网络广播地址也包括在结果中。掩码为32的网络将返回一个包含单一主机地址的列表。

\>>>

```
>>> list(ip_network('192.0.2.0/29').hosts())  
[IPv4Address('192.0.2.1'), IPv4Address('192.0.2.2'),
 IPv4Address('192.0.2.3'), IPv4Address('192.0.2.4'),
 IPv4Address('192.0.2.5'), IPv4Address('192.0.2.6')]
>>> list(ip_network('192.0.2.0/31').hosts())
[IPv4Address('192.0.2.0'), IPv4Address('192.0.2.1')]
>>> list(ip_network('192.0.2.1/32').hosts())
[IPv4Address('192.0.2.1')]
```

## **overlaps**(*other*)

​	如果这个网络部分或全部包含在*other*中，或者*other*全部包含在这个网络中，则为 `True`。

## **address_exclude**(*network*)

​	计算从这个网络中移除给定的 *network* 后产生的网络定义。 返回一个网络对象的迭代器。 如果 *network* 不完全包含在这个网络中则会引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

\>>>

```
>>> n1 = ip_network('192.0.2.0/28')
>>> n2 = ip_network('192.0.2.1/32')
>>> list(n1.address_exclude(n2))  
[IPv4Network('192.0.2.8/29'), IPv4Network('192.0.2.4/30'),
 IPv4Network('192.0.2.2/31'), IPv4Network('192.0.2.0/32')]
```

## **subnets**(*prefixlen_diff=1*, *new_prefix=None*)

​	根据参数值，加入的子网构成当前的网络定义。 *prefixlen_diff* 是我们的前缀长度应该增加的数量。 *new_prefix* 是所需的子网的新前缀；它必须大于我们的前缀。 必须设置 *prefixlen_diff* 和 *new_prefix* 中的一个，且只有一个。 返回一个网络对象的迭代器。

\>>>

```
>>> list(ip_network('192.0.2.0/24').subnets())
[IPv4Network('192.0.2.0/25'), IPv4Network('192.0.2.128/25')]
>>> list(ip_network('192.0.2.0/24').subnets(prefixlen_diff=2))  
[IPv4Network('192.0.2.0/26'), IPv4Network('192.0.2.64/26'),
 IPv4Network('192.0.2.128/26'), IPv4Network('192.0.2.192/26')]
>>> list(ip_network('192.0.2.0/24').subnets(new_prefix=26))  
[IPv4Network('192.0.2.0/26'), IPv4Network('192.0.2.64/26'),
 IPv4Network('192.0.2.128/26'), IPv4Network('192.0.2.192/26')]
>>> list(ip_network('192.0.2.0/24').subnets(new_prefix=23))
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
    raise ValueError('new prefix must be longer')
ValueError: new prefix must be longer
>>> list(ip_network('192.0.2.0/24').subnets(new_prefix=25))
[IPv4Network('192.0.2.0/25'), IPv4Network('192.0.2.128/25')]
```

## **supernet**(*prefixlen_diff=1*, *new_prefix=None*)

​	包含这个网络定义的超级网，取决于参数值。 *prefixlen_diff* 是我们的前缀长度应该减少的数量。 *new_prefix* 是超级网的新前缀；它必须比我们的前缀小。 必须设置 *prefixlen_diff* 和 *new_prefix* 中的一个，而且只有一个。 返回一个单一的网络对象。

\>>>

```
>>> ip_network('192.0.2.0/24').supernet()
IPv4Network('192.0.2.0/23')
>>> ip_network('192.0.2.0/24').supernet(prefixlen_diff=2)
IPv4Network('192.0.0.0/22')
>>> ip_network('192.0.2.0/24').supernet(new_prefix=20)
IPv4Network('192.0.0.0/20')
```

## **subnet_of**(*other*)

​	如果这个网络是*other*的子网，则返回 `True`。

\>>>

```
>>> a = ip_network('192.168.1.0/24')
>>> b = ip_network('192.168.1.128/30')
>>> b.subnet_of(a)
True
```

*Added in version 3.7.*

## **supernet_of**(*other*)

​	如果这个网络是*other*的超网，则返回 `True`。

\>>>

```
>>> a = ip_network('192.168.1.0/24')
>>> b = ip_network('192.168.1.128/30')
>>> a.supernet_of(b)
True
```

*Added in version 3.7.*

## **compare_networks**(*other*)

​	将这个网络与*ohter*网络进行比较。 在这个比较中，只考虑网络地址；不考虑主机位。 返回是 `-1` 、 `0``或``1`。

\>>>

```
>>> ip_network('192.0.2.1/32').compare_networks(ip_network('192.0.2.2/32'))
-1
>>> ip_network('192.0.2.1/32').compare_networks(ip_network('192.0.2.0/32'))
1
>>> ip_network('192.0.2.1/32').compare_networks(ip_network('192.0.2.1/32'))
0
```

*自 3.7 版本弃用:* 它使用与"<"、"=="和">"相同的排序和比较算法。

## *class* ipaddress.**IPv6Network**(*address*, *strict=True*)

​	构建一个 IPv6 网络定义。 *address* 可以是以下之一：

1. 一个由IP地址和可选前缀长度组成的字符串，用斜线（ `/`）分开。 IP地址是网络地址，前缀长度必须是一个数字，即*prefix*。 如果没有提供前缀长度，就认为是 `/128`。

   请注意，目前不支持扩展的网络掩码。 这意味着 `2001:db00::0/24` 是一个有效的参数，而 `2001:db00::0/ffff:ff00::` 不是。

2. 一个适合128位的整数。 这相当于一个单地址网络，网络地址是*address*，掩码是 `/128`。

3. 一个整数被打包成一个长度为 16 的大端序 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 对象。 其解释类似于一个整数的 *address*。

4. 一个地址描述和一个网络掩码的双元组，其中地址描述是一个字符串，一个 128 位的整数，一个 16 字节的打包整数，或者一个现有的 IPv6Address 对象；而网络掩码是一个代表前缀长度的整数。

​	如果 *address* 不是一个有效的 IPv6 地址则会引发 [`AddressValueError`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.AddressValueError)。 如果掩码对 IPv6 地址无效则会引发 [`NetmaskValueError`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.NetmaskValueError)。

​	如果 *strict* 是 `True`，并且在提供的地址中设置了主机位，那么 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError) 将被触发。 否则，主机位将被屏蔽掉，以确定适当的网络地址。

*在 3.5 版本发生变更:* 为*address*构造函数参数添加了双元组形式。

## **version**

## **max_prefixlen**

## **is_multicast**

## **is_private**

## **is_unspecified**

## **is_reserved**

## **is_loopback**

## **is_link_local**

## **network_address**

## **broadcast_address**

## **hostmask**

## **netmask**

## **with_prefixlen**

## **compressed**

## **exploded**

## **with_netmask**

## **with_hostmask**

## **num_addresses**

## **prefixlen**

## **hosts**()

​	返回一个网络中可用主机的迭代器。 可用的主机是属于该网络的所有IP地址，除了Subnet-Router任播的地址。 对于掩码长度为127的网络，子网-路由器任播地址也包括在结果中。掩码为128的网络将返回一个包含单一主机地址的列表。

## **overlaps**(*other*)

## **address_exclude**(*network*)

## **subnets**(*prefixlen_diff=1*, *new_prefix=None*)

## **supernet**(*prefixlen_diff=1*, *new_prefix=None*)

## **subnet_of**(*other*)

## **supernet_of**(*other*)

## **compare_networks**(*other*)

​	请参考 [`IPv4Network`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Network) 中的相应属性文档。

## **is_site_local**

​	如果这些属性对网络地址和广播地址都是True，那么对整个网络来说就是True。

### 运算符

​	网络对象支持一些运算符。 除非另有说明，运算符只能在兼容的对象之间应用（例如，IPv4与IPv4，IPv6与IPv6）。

#### 逻辑运算符

​	网络对象可以用常规的逻辑运算符集进行比较。网络对象首先按网络地址排序，然后按网络掩码排序。

#### 迭代

​	网络对象可以被迭代，以列出属于该网络的所有地址。 对于迭代，*所有* 主机都会被返回，包括不可用的主机（对于可用的主机，使用 [`hosts()`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Network.hosts) 方法）。 一个例子:

\>>>

```
>>> for addr in IPv4Network('192.0.2.0/28'):
...     addr
...
IPv4Address('192.0.2.0')
IPv4Address('192.0.2.1')
IPv4Address('192.0.2.2')
IPv4Address('192.0.2.3')
IPv4Address('192.0.2.4')
IPv4Address('192.0.2.5')
IPv4Address('192.0.2.6')
IPv4Address('192.0.2.7')
IPv4Address('192.0.2.8')
IPv4Address('192.0.2.9')
IPv4Address('192.0.2.10')
IPv4Address('192.0.2.11')
IPv4Address('192.0.2.12')
IPv4Address('192.0.2.13')
IPv4Address('192.0.2.14')
IPv4Address('192.0.2.15')
```

#### 作为地址容器的网络

​	网络对象可以作为地址的容器。 一些例子:

\>>>

```
>>> IPv4Network('192.0.2.0/28')[0]
IPv4Address('192.0.2.0')
>>> IPv4Network('192.0.2.0/28')[15]
IPv4Address('192.0.2.15')
>>> IPv4Address('192.0.2.6') in IPv4Network('192.0.2.0/28')
True
>>> IPv4Address('192.0.3.6') in IPv4Network('192.0.2.0/28')
False
```

## 接口对象

​	接口对象是 [hashable](https://docs.python.org/zh-cn/3.13/glossary.html#term-hashable) 的，所以它们可以作为字典中的键使用。

## *class* ipaddress.**IPv4Interface**(*address*)

​	构建一个 IPv4 接口。 *address* 的含义与 [`IPv4Network`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Network) 构造器中的一样，不同之处在于任意主机地址总是会被接受。

[`IPv4Interface`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Interface) 是 [`IPv4Address`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Address) 的一个子类，所以它继承了该类的所有属性。 此外，还有以下属性可用：

## **ip**

​	地址（[`IPv4Address`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Address)）没有网络信息。

\>>>

```
>>> interface = IPv4Interface('192.0.2.5/24')
>>> interface.ip
IPv4Address('192.0.2.5')
```

## **network**

​	该接口所属的网络（[`IPv4Network`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Network)）。

\>>>

```
>>> interface = IPv4Interface('192.0.2.5/24')
>>> interface.network
IPv4Network('192.0.2.0/24')
```

## **with_prefixlen**

​	用前缀符号表示的接口与掩码的字符串。

\>>>

```
>>> interface = IPv4Interface('192.0.2.5/24')
>>> interface.with_prefixlen
'192.0.2.5/24'
```

## **with_netmask**

​	带有网络的接口的网络掩码字符串表示。

\>>>

```
>>> interface = IPv4Interface('192.0.2.5/24')
>>> interface.with_netmask
'192.0.2.5/255.255.255.0'
```

## **with_hostmask**

​	带有网络的接口的主机掩码字符串表示。

\>>>

```
>>> interface = IPv4Interface('192.0.2.5/24')
>>> interface.with_hostmask
'192.0.2.5/0.0.0.255'
```

## *class* ipaddress.**IPv6Interface**(*address*)

​	构建一个 IPv6 接口。 *address* 的含义与 [`IPv6Network`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv6Network) 构造器中的一样，不同之处在于任意主机地址总是会被接受。

[`IPv6Interface`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv6Interface) 是 [`IPv6Address`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv6Address) 的一个子类，所以它继承了该类的所有属性。 此外，还有以下属性可用：

## **ip**

## **network**

## **with_prefixlen**

## **with_netmask**

## **with_hostmask**

​	请参考 [`IPv4Interface`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Interface) 中的相应属性文档。

### 运算符

​	接口对象支持一些运算符。 除非另有说明，运算符只能在兼容的对象之间应用（即IPv4与IPv4，IPv6与IPv6）。

#### 逻辑运算符

​	接口对象可以用通常的逻辑运算符集进行比较。

​	对于相等比较（`==` 和 `!=`），IP地址和网络都必须是相同的对象才会相等。 一个接口不会与任何地址或网络对象相等。

​	对于排序 (`<`、`>` 等)，规则是不同的。 具有相同 IP 版本的接口和地址对象可以被比较，而地址对象总是在接口对象之前排序。 两个接口对象首先通过它们的网络进行比较，如果它们是相同的，则通过它们的 IP 地址进行比较。

## 其他模块级别函数

​	该模块还提供以下模块级函数：

## ipaddress.**v4_int_to_packed**(*address*)

​	以网络（大端序）顺序将一个地址表示为 4 个打包的字节。*address* 是一个 IPv4 IP 地址的整数表示。 如果整数是负数或太大而不满足 IPv4 IP 地址要求，会触发一个 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

\>>>

```
>>> ipaddress.ip_address(3221225985)
IPv4Address('192.0.2.1')
>>> ipaddress.v4_int_to_packed(3221225985)
b'\xc0\x00\x02\x01'
```

## ipaddress.**v6_int_to_packed**(*address*)

​	以网络（大端序）顺序将一个地址表示为 4 个打包的字节。*address* 是一个IPv6 IP地址的整数表示。 如果整数是负数或太大而不满足 IPv6 IP 地址要求，会触发一个 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

## ipaddress.**summarize_address_range**(*first*, *last*)

​	给出第一个和最后一个 IP 地址，返回总结的网络范围的迭代器。 *first* 是范围内的第一个 [`IPv4Address`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Address) 或 [`IPv6Address`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv6Address)，*last* 是范围内的最后一个 [`IPv4Address`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Address) 或 [`IPv6Address`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv6Address)。 如果 *first* 或 *last* 不是IP地址或不是同一版本则会引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。 如果 *last* 不大于 *first*，或者 *first* 的地址版本不是 4 或 6 则会引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

\>>>

```
>>> [ipaddr for ipaddr in ipaddress.summarize_address_range(
...    ipaddress.IPv4Address('192.0.2.0'),
...    ipaddress.IPv4Address('192.0.2.130'))]
[IPv4Network('192.0.2.0/25'), IPv4Network('192.0.2.128/31'), IPv4Network('192.0.2.130/32')]
```

## ipaddress.**collapse_addresses**(*addresses*)

​	返回一个已展开的 [`IPv4Network`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Network) 或 [`IPv6Network`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv6Network) 对象的迭代器。 *addresses* 是一个 [`IPv4Network`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv4Network) 或 [`IPv6Network`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#ipaddress.IPv6Network) 对象的 [iterable](https://docs.python.org/zh-cn/3.13/glossary.html#term-iterable)。 如果 *addresses* 包含混合版本的对象则会引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。

\>>>

```
>>> [ipaddr for ipaddr in
... ipaddress.collapse_addresses([ipaddress.IPv4Network('192.0.2.0/25'),
... ipaddress.IPv4Network('192.0.2.128/25')])]
[IPv4Network('192.0.2.0/24')]
```

## ipaddress.**get_mixed_type_key**(*obj*)

​	返回一个适合在网络和地址之间进行排序的键。 地址和网络对象在默认情况下是不可排序的；它们在本质上是不同的，所以表达式:

```
IPv4Address('192.0.2.0') <= IPv4Network('192.0.2.0/24')
```

​	是没有意义的。 不过在某些时候，你可能还是希望让 [`ipaddress`](https://docs.python.org/zh-cn/3.13/library/ipaddress.html#module-ipaddress) 对这些进行排序。 如果你需要这样做，你可以使用这个函数作为 [`sorted()`](https://docs.python.org/zh-cn/3.13/library/functions.html#sorted) 的 *key* 参数。

*obj* 是一个网络或地址对象。

## 自定义异常

​	为了支持来自类构造函数的更具体的错误报告，模块定义了以下异常：

## *exception* ipaddress.**AddressValueError**(*ValueError*)

​	与地址有关的任何数值错误。

## *exception* ipaddress.**NetmaskValueError**(*ValueError*)

​	与网络掩码有关的任何数值错误。
