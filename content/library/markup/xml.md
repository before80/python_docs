+++
title = "XML处理模块"
date = 2024-11-15T15:24:42+08:00
weight = 30
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/xml.html](https://docs.python.org/zh-cn/3.13/library/xml.html)
>
> 收录该文档的时间：`2024-11-15T15:24:42+08:00`

# XML处理模块

**源码：** [Lib/xml/](https://github.com/python/cpython/tree/3.13/Lib/xml/)

------

​	用于处理XML的Python接口分组在 `xml` 包中。

​	警告

 

​	XML 模块对于错误或恶意构造的数据是不安全的。 如果你需要解析不受信任或未经身份验证的数据，请参阅 [XML 漏洞]({{< ref "/library/markup/xml#xml-vulnerabilities" >}}) 和 [defusedxml 包]({{< ref "/library/markup/xml#defusedxml-package" >}}) 部分。

​	值得注意的是 [`xml`]({{< ref "/library/markup/xml#module-xml" >}}) 包中的模块要求至少有一个 SAX 兼容的 XML 解析器可用。在 Python 中包含 Expat 解析器，因此 [`xml.parsers.expat`]({{< ref "/library/markup/pyexpat#module-xml.parsers.expat" >}}) 模块将始终可用。

[`xml.dom`]({{< ref "/library/markup/xml_dom#module-xml.dom" >}}) 和 [`xml.sax`]({{< ref "/library/markup/xml_sax#module-xml.sax" >}}) 包的文档是 DOM 和 SAX 接口的 Python 绑定的定义。

​	XML 处理子模块包括:

- [`xml.etree.ElementTree`]({{< ref "/library/markup/xml_etree_elementtree#module-xml.etree.ElementTree" >}})： ElementTree API，一个简单而轻量级的XML处理器

- [`xml.dom`]({{< ref "/library/markup/xml_dom#module-xml.dom" >}})：DOM API 定义
- [`xml.dom.minidom`]({{< ref "/library/markup/xml_dom_minidom#module-xml.dom.minidom" >}})：最小的 DOM 实现
- [`xml.dom.pulldom`]({{< ref "/library/markup/xml_dom_pulldom#module-xml.dom.pulldom" >}})：支持构建部分 DOM 树

- [`xml.sax`]({{< ref "/library/markup/xml_sax#module-xml.sax" >}})：SAX2 基类和便利函数
- [`xml.parsers.expat`]({{< ref "/library/markup/pyexpat#module-xml.parsers.expat" >}})：Expat解析器绑定



## XML 漏洞

​	XML 处理模块对于恶意构造的数据是不安全的。 攻击者可能滥用 XML 功能来执行拒绝服务攻击、访问本地文件、生成与其它计算机的网络连接或绕过防火墙。

​	下表概述了已知的攻击以及各种模块是否容易受到攻击。

| 种类                                                         | sax              | etree            | minidom          | pulldom          | xmlrpc           |
| :----------------------------------------------------------- | :--------------- | :--------------- | :--------------- | :--------------- | :--------------- |
| billion laughs                                               | **易受攻击** (1) | **易受攻击** (1) | **易受攻击** (1) | **易受攻击** (1) | **易受攻击** (1) |
| quadratic blowup                                             | **易受攻击** (1) | **易受攻击** (1) | **易受攻击** (1) | **易受攻击** (1) | **易受攻击** (1) |
| external entity expansion                                    | 安全 (5)         | 安全 (2)         | 安全 (3)         | 安全 (5)         | 安全 (4)         |
| [DTD](https://en.wikipedia.org/wiki/Document_type_definition) retrieval | 安全 (5)         | 安全             | 安全             | 安全 (5)         | 安全             |
| decompression bomb                                           | 安全             | 安全             | 安全             | 安全             | **易受攻击**     |
| 解析大量词元                                                 | **易受攻击** (6) | **易受攻击** (6) | **易受攻击** (6) | **易受攻击** (6) | **易受攻击** (6) |

1. Expat 2.4.1 及更新的版本不易受 "billion laughs" 和 "quadratic blowup" 漏洞的影响。 因为可能要依赖系统提供的库而仍被列为易受攻击的项。 请检查 `pyexpat.EXPAT_VERSION`。
2. [`xml.etree.ElementTree`]({{< ref "/library/markup/xml_etree_elementtree#module-xml.etree.ElementTree" >}}) 不会扩展外部实体并将在遇到实体时引发 [`ParseError`]({{< ref "/library/markup/xml_etree_elementtree#xml.etree.ElementTree.ParseError" >}})。
3. [`xml.dom.minidom`]({{< ref "/library/markup/xml_dom_minidom#module-xml.dom.minidom" >}}) 不会扩展外部实体，只是简单地返回未扩展的实体。
4. [`xmlrpc.client`]({{< ref "/library/internet/xmlrpc_client#module-xmlrpc.client" >}}) 不会扩展外部实体并将忽略它们。
5. 从 Python 3.7.1 开始，默认情况下不再处理外部通用实体。
6. Expat 2.6.0 及更新的版本不易受到因解析大量词元而导致利用指数级运行时间的拒绝服务攻击。 由于对系统所提供的库的潜在依赖仍会有一些项目被列为易受攻击。 请检查 `pyexpat.EXPAT_VERSION`。

## billion laughs / exponential entity expansion （狂笑/递归实体扩展）

[Billion Laughs](https://en.wikipedia.org/wiki/Billion_laughs) 攻击 -- 也称为递归实体扩展 -- 使用多级嵌套实体。 每个实体多次引用另一个实体，最终实体定义包含一个小字符串。 指数级扩展导致几千 GB 的文本，并消耗大量内存和 CPU 时间。

## quadratic blowup entity expansion（二次爆炸实体扩展）

​	二次爆炸攻击类似于 [Billion Laughs](https://en.wikipedia.org/wiki/Billion_laughs) 攻击；它也滥用了实体扩展。 它不是嵌套实体，而是一遍又一遍地重复一个具有几千个字符的大型实体。 这种攻击不如递归情况有效，但它可避免触发禁止深度嵌套实体的解析器对策。

## external entity expansion

​	实体声明可以包含的不仅仅是替换文本。 它们还可以指向外部资源或本地文件。 XML 解析器访问资源并将内容嵌入到 XML 文档中。

## [DTD](https://en.wikipedia.org/wiki/Document_type_definition) retrieval

​	Python 的一些 XML 库 [`xml.dom.pulldom`]({{< ref "/library/markup/xml_dom_pulldom#module-xml.dom.pulldom" >}}) 从远程或本地位置检索文档类型定义。 该功能与外部实体扩展问题具有相似的含义。

## decompression bomb

​	Decompression bombs（解压炸弹，又名 [ZIP bomb](https://en.wikipedia.org/wiki/Zip_bomb)）适用于所有可以解析压缩 XML 流（例如 gzip 压缩的 HTTP 流或 LZMA 压缩的文件）的 XML 库。 对于攻击者来说，它可以将传输的数据量减少三个量级或更多。

## 解析大量词元

​	Expat 需要重新解析未完成的词元；在没有 Expat 2.6.0 所引入的防护措施的情况下，这会导致可被用来在解析 XML 的应用程序中制造拒绝服务攻击的指数级运行时间。 此问题被称为 [**CVE 2023-52425**](https://www.cve.org/CVERecord?id=CVE-2023-52425)。

​	PyPI 上 [defusedxml](https://pypi.org/project/defusedxml/) 的文档包含关于所有已知攻击向量的更多信息并附带示例和参考资料。



## `defusedxml` 包

[defusedxml](https://pypi.org/project/defusedxml/) 是一个纯 Python 软件包，它修改了所有 stdlib XML 解析器的子类，可以防止任何潜在的恶意操作。 对于解析不受信任的 XML 数据的任何服务器代码推荐使用此软件包。 该软件包还附带了关于其他 XML 漏洞（如 XPath 注入）的利用示例和扩展文档。
