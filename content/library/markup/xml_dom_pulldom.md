+++
title = "xml.dom.pulldom --- 对构建部分 DOM 树的支持"
date = 2024-11-15T15:24:42+08:00
weight = 70
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/xml.dom.pulldom.html](https://docs.python.org/zh-cn/3.13/library/xml.dom.pulldom.html)
>
> 收录该文档的时间：`2024-11-15T15:24:42+08:00`

# `xml.dom.pulldom` --- 对构建部分 DOM 树的支持

**源代码:** [Lib/xml/dom/pulldom.py](https://github.com/python/cpython/tree/3.13/Lib/xml/dom/pulldom.py)

------

[`xml.dom.pulldom`]({{< ref "/library/markup/xml_dom_pulldom#module-xml.dom.pulldom" >}}) 模块提供了一个“拉取解析器”，它能在必要时被用于产生文件的可访问 DOM 的片段。 其基本概念包括从输入的 XML 流拉取“事件”并处理它们。 与同样地同时应用了事件驱动处理模型加回调函数的 SAX 不同，拉取解析器的用户要负责显式地从流拉取事件，并循环遍历这些事件直到处理结束或者发生了错误条件。

​	警告

 

[`xml.dom.pulldom`]({{< ref "/library/markup/xml_dom_pulldom#module-xml.dom.pulldom" >}}) 模块对于恶意构建的数据是不安全的。 如果你需要解析不受信任或未经身份验证的数据，请参阅 [XML 漏洞]({{< ref "/library/markup/xml#xml-vulnerabilities" >}})。

*在 3.7.1 版本发生变更:* SAX 解析器默认不再处理一般外部实体以提升在默认情况下的安全性。 要启用外部实体处理，请传入一个自定义的解析器实例:

```
from xml.dom.pulldom import parse
from xml.sax import make_parser
from xml.sax.handler import feature_external_ges

parser = make_parser()
parser.setFeature(feature_external_ges, True)
parse(filename, parser=parser)
```

​	示例:

```
from xml.dom import pulldom

doc = pulldom.parse('sales_items.xml')
for event, node in doc:
    if event == pulldom.START_ELEMENT and node.tagName == 'item':
        if int(node.getAttribute('price')) > 50:
            doc.expandNode(node)
            print(node.toxml())
```

`event` 是一个常量，可以取下列值之一:

- `START_ELEMENT`
- `END_ELEMENT`
- `COMMENT`
- `START_DOCUMENT`
- `END_DOCUMENT`
- `CHARACTERS`
- `PROCESSING_INSTRUCTION`
- `IGNORABLE_WHITESPACE`

`node` 是一个 `xml.dom.minidom.Document`, `xml.dom.minidom.Element` 或 `xml.dom.minidom.Text` 类型的对象。

​	由于文档是被当作“展平”的事件流来处理的，文档“树”会被隐式地遍历并且无论所需元素在树中的深度如何都会被找到。 换句话说，不需要考虑层级问题，例如文档节点的递归搜索等，但是如果元素的内容很重要，则有必要保留一些上下文相关的状态（例如记住任意给定点在文档中的位置）或者使用 [`DOMEventStream.expandNode()`]({{< ref "/library/markup/xml_dom_pulldom#xml.dom.pulldom.DOMEventStream.expandNode" >}}) 方法并切换到 DOM 相关的处理过程。

## *class* xml.dom.pulldom.**PullDom**(*documentFactory=None*)

[`xml.sax.handler.ContentHandler`]({{< ref "/library/markup/xml_sax_handler#xml.sax.handler.ContentHandler" >}}) 的子类。

## *class* xml.dom.pulldom.**SAX2DOM**(*documentFactory=None*)

[`xml.sax.handler.ContentHandler`]({{< ref "/library/markup/xml_sax_handler#xml.sax.handler.ContentHandler" >}}) 的子类。

## xml.dom.pulldom.**parse**(*stream_or_string*, *parser=None*, *bufsize=None*)

​	基于给定的输入返回一个 [`DOMEventStream`]({{< ref "/library/markup/xml_dom_pulldom#xml.dom.pulldom.DOMEventStream" >}})。 *stream_or_string* 可以是一个文件名，或是一个文件型对象。 *parser* 如果给出，则必须是一个 [`XMLReader`]({{< ref "/library/markup/xml_sax_reader#xml.sax.xmlreader.XMLReader" >}}) 对象。 此函数将改变解析器的文档处理程序并激活命名空间支持；其他解析器配置（例如设置实体解析器）必须在之前已完成。

​	如果你将 XML 存放为字符串形式，则可以改用 [`parseString()`]({{< ref "/library/markup/xml_dom_pulldom#xml.dom.pulldom.parseString" >}}) 函数:

## xml.dom.pulldom.**parseString**(*string*, *parser=None*)

​	返回一个 [`DOMEventStream`]({{< ref "/library/markup/xml_dom_pulldom#xml.dom.pulldom.DOMEventStream" >}}) 来表示 (Unicode) *string*。

## xml.dom.pulldom.**default_bufsize**

​	将 *bufsize* 形参的默认值设为 [`parse()`]({{< ref "/library/markup/xml_dom_pulldom#xml.dom.pulldom.parse" >}})。

​	此变量的值可在调用 [`parse()`]({{< ref "/library/markup/xml_dom_pulldom#xml.dom.pulldom.parse" >}}) 之前修改并使新值生效。



## DOMEventStream 对象

## *class* xml.dom.pulldom.**DOMEventStream**(*stream*, *parser*, *bufsize*)

> 在 3.11 版本发生变更: 对 [`__getitem__()`]({{< ref "/reference/datamodel#object.__getitem__" >}}) 方法的支持已被移除。

## **getEvent**()

​	返回一个元组，其中包含 *event* 和 `xml.dom.minidom.Document` 形式的当前 *node* 如果 event 等于 `START_DOCUMENT`，包含 `xml.dom.minidom.Element` 如果 event 等于 `START_ELEMENT` 或 `END_ELEMENT` 或者 `xml.dom.minidom.Text` 如果 event 等于 `CHARACTERS`。 当前 node 不包含有关其子节点的信息，除非 [`expandNode()`]({{< ref "/library/markup/xml_dom_pulldom#xml.dom.pulldom.DOMEventStream.expandNode" >}}) 被调用。

## **expandNode**(*node*)

​	将 *node* 的所有子节点扩展到 *node* 中。 例如:

```
from xml.dom import pulldom

xml = '<html><title>Foo</title> <p>Some text <div>and more</div></p> </html>'
doc = pulldom.parseString(xml)
for event, node in doc:
    if event == pulldom.START_ELEMENT and node.tagName == 'p':
        # 以下语句只打印 '<p/>'
        print(node.toxml())
        doc.expandNode(node)
        # 以下语句将打印节点所有的子节点 '<p>Some text <div>and more</div></p>'
        print(node.toxml())
```

## **reset**()
