+++
title = "xml.sax.xmlreader --- 用于 XML 解析器的接口"
date = 2024-11-15T15:24:42+08:00
weight = 110
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/xml.sax.reader.html](https://docs.python.org/zh-cn/3.13/library/xml.sax.reader.html)
>
> 收录该文档的时间：`2024-11-15T15:24:42+08:00`

# `xml.sax.xmlreader` --- 用于 XML 解析器的接口

**源代码:** [Lib/xml/sax/xmlreader.py](https://github.com/python/cpython/tree/3.13/Lib/xml/sax/xmlreader.py)

------

​	SAX 解析器实现了 [`XMLReader`]({{< ref "/library/markup/xml_sax_reader#xml.sax.xmlreader.XMLReader" >}}) 接口。 它们是在一个 Python 模块中实现的，该模块必须提供一个 `create_parser()` 函数。 该函数由 [`xml.sax.make_parser()`]({{< ref "/library/markup/xml_sax#xml.sax.make_parser" >}}) 不带参数地发起调用来创建新的解析器对象。

## *class* xml.sax.xmlreader.**XMLReader**

​	可由 SAX 解析器继承的基类。

## *class* xml.sax.xmlreader.**IncrementalParser**

​	在某些情况下，最好不要一次性地解析输入源，而是在可用的时候分块送入。 请注意读取器通常不会读取整个文件，它同样也是分块读取的； 并且 `parse()` 在处理完整个文档之前不会返回。 所以如果不希望 `parse()` 出现阻塞行为则应当使用这些接口。

​	当解析器被实例化时它已准备好立即开始接受来自 feed 方法的数据。 在通过调用 close 方法结束解析时 reset 方法也必须被调用以使解析器准备好接受新的数据，无论它是来自于 feed 还是使用 parse 方法。

​	请注意这些方法 *不可* 在解析期间被调用，即在 parse 被调用之后及其返回之前。

​	默认情况下，该类还使用 IncrementalParser 接口的 feed, close 和 reset 方法来实现 XMLReader 接口的 parse 方法以方便 SAX 2.0 驱动的编写者。

## *class* xml.sax.xmlreader.**Locator**

​	用于关联一个 SAX 事件与一个文档位置的接口。 定位器对象只有在调用 DocumentHandler 的方法期间才会返回有效的结果；在其他任何时候，结果都是不可预测的。 如果信息不可用，这些方法可能返回 `None`。

## *class* xml.sax.xmlreader.**InputSource**(*system_id=None*)

[`XMLReader`]({{< ref "/library/markup/xml_sax_reader#xml.sax.xmlreader.XMLReader" >}}) 读取实体所需信息的封装。

​	这个类可能包括了关于公有标识符、系统标识符、字节流（可能带有字符编码格式信息）和/或一个实体的字符流的信息。

​	应用程序将创建这个类的对象以便在 [`XMLReader.parse()`]({{< ref "/library/markup/xml_sax_reader#xml.sax.xmlreader.XMLReader.parse" >}}) 方法中使用或是用于从 EntityResolver.resolveEntity 返回值。

[`InputSource`]({{< ref "/library/markup/xml_sax_reader#xml.sax.xmlreader.InputSource" >}}) 属于应用程序，[`XMLReader`]({{< ref "/library/markup/xml_sax_reader#xml.sax.xmlreader.XMLReader" >}}) 不能修改从应用程序传递给它的 [`InputSource`]({{< ref "/library/markup/xml_sax_reader#xml.sax.xmlreader.InputSource" >}}) 对象，但它可以创建副本并进行修改。

## *class* xml.sax.xmlreader.**AttributesImpl**(*attrs*)

​	这是 `Attributes` 接口（参见 [Attributes 接口]({{< ref "/library/markup/xml_sax_reader#attributes-objects" >}}) 一节）的具体实现。 这是一个 `startElement()` 调用中的元素属性的字典类对象。 除了最有用处的字典操作，它还支持接口所描述的一些其他方法。 该类的对象应当由读取器来实例化；*attrs* 必须为包含从属性名到属性值的映射的字典类对象。

## *class* xml.sax.xmlreader.**AttributesNSImpl**(*attrs*, *qnames*)

​	可感知命名空间的 [`AttributesImpl`]({{< ref "/library/markup/xml_sax_reader#xml.sax.xmlreader.AttributesImpl" >}}) 变体形式，它将被传递给 `startElementNS()`。 它派生自 [`AttributesImpl`]({{< ref "/library/markup/xml_sax_reader#xml.sax.xmlreader.AttributesImpl" >}})，但会将属性名称解读为 *namespaceURI* 和 *localname* 二元组。 此外，它还提供了一些期望接收在原始文档中出现的限定名称的方法。 这个类实现了 `AttributesNS` 接口（参见 [AttributesNS 接口]({{< ref "/library/markup/xml_sax_reader#attributes-ns-objects" >}}) 一节）。



## XMLReader 对象

[`XMLReader`]({{< ref "/library/markup/xml_sax_reader#xml.sax.xmlreader.XMLReader" >}}) 接口支持下列方法:

## XMLReader.**parse**(*source*)

​	处理输入源，产生 SAX 事件。 *source* 对象可以是一个系统标识符（标识输入源的字符串 -- 通常为文件名或 URL）, [`pathlib.Path`]({{< ref "/library/filesys/pathlib#pathlib.Path" >}}) 或 [路径类]({{< ref "/glossary/idx#term-path-like-object" >}}) 对象，或者是 [`InputSource`]({{< ref "/library/markup/xml_sax_reader#xml.sax.xmlreader.InputSource" >}}) 对象。 当 [`parse()`]({{< ref "/library/markup/xml_sax_reader#xml.sax.xmlreader.XMLReader.parse" >}}) 返回时，输入会被全部处理完成，解析器对象可以被丢弃或重置。

> 在 3.5 版本发生变更: 添加了对字符流的支持。

> 在 3.8 版本发生变更: 增加了对路径类对象的支持。

## XMLReader.**getContentHandler**()

​	返回当前的 [`ContentHandler`]({{< ref "/library/markup/xml_sax_handler#xml.sax.handler.ContentHandler" >}})。

## XMLReader.**setContentHandler**(*handler*)

​	设置当前的 [`ContentHandler`]({{< ref "/library/markup/xml_sax_handler#xml.sax.handler.ContentHandler" >}})。 如果没有设置 [`ContentHandler`]({{< ref "/library/markup/xml_sax_handler#xml.sax.handler.ContentHandler" >}})，内容事件将被丢弃。

## XMLReader.**getDTDHandler**()

​	返回当前的 [`DTDHandler`]({{< ref "/library/markup/xml_sax_handler#xml.sax.handler.DTDHandler" >}})。

## XMLReader.**setDTDHandler**(*handler*)

​	设置当前的 [`DTDHandler`]({{< ref "/library/markup/xml_sax_handler#xml.sax.handler.DTDHandler" >}})。 如果没有设置 [`DTDHandler`]({{< ref "/library/markup/xml_sax_handler#xml.sax.handler.DTDHandler" >}})，DTD 事件将被丢弃。

## XMLReader.**getEntityResolver**()

​	返回当前的 [`EntityResolver`]({{< ref "/library/markup/xml_sax_handler#xml.sax.handler.EntityResolver" >}})。

## XMLReader.**setEntityResolver**(*handler*)

​	设置当前的 [`EntityResolver`]({{< ref "/library/markup/xml_sax_handler#xml.sax.handler.EntityResolver" >}})。 如果没有设置 [`EntityResolver`]({{< ref "/library/markup/xml_sax_handler#xml.sax.handler.EntityResolver" >}})，尝试解析一个外部实体将导致打开该实体的系统标识符，并且如果它不可用则操作将失败。

## XMLReader.**getErrorHandler**()

​	返回当前的 [`ErrorHandler`]({{< ref "/library/markup/xml_sax_handler#xml.sax.handler.ErrorHandler" >}})。

## XMLReader.**setErrorHandler**(*handler*)

​	设置当前的错误处理器。 如果没有设置 [`ErrorHandler`]({{< ref "/library/markup/xml_sax_handler#xml.sax.handler.ErrorHandler" >}})，错误将作为异常被引发，并将打印警告信息。

## XMLReader.**setLocale**(*locale*)

​	允许应用程序为错误和警告设置语言区域。

​	SAX 解析器不要求为错误和警告提供本地化信息；但是如果它们无法支持所请求的语言区域，则必须引发一个 SAX 异常。 应用程序可以在解析的中途请求更改语言区域。

## XMLReader.**getFeature**(*featurename*)

​	返回 *featurename* 特性的当前设置。 如果特性无法被识别，则会引发 `SAXNotRecognizedException`。 在 [`xml.sax.handler`]({{< ref "/library/markup/xml_sax_handler#module-xml.sax.handler" >}}) 模块中列出了常见的特性名称。

## XMLReader.**setFeature**(*featurename*, *value*)

​	将 *featurename* 设为 *value*。 如果特性无法被识别，则会引发 `SAXNotRecognizedException`。 如果特性或其设置不被解析器所支持，则会引发 *SAXNotSupportedException*。

## XMLReader.**getProperty**(*propertyname*)

​	返回 *propertyname* 属性的当前设置。 如果属性无法被识别，则会引发 `SAXNotRecognizedException`。 在 [`xml.sax.handler`]({{< ref "/library/markup/xml_sax_handler#module-xml.sax.handler" >}}) 模块中列出了常见的属性名称。

## XMLReader.**setProperty**(*propertyname*, *value*)

​	将 *propertyname* 设为 *value*。 如果属性无法被识别，则会引发 `SAXNotRecognizedException`。 如果属性或其设置不被解析器所支持，则会引发 *SAXNotSupportedException*。



## IncrementalParser 对象

[`IncrementalParser`]({{< ref "/library/markup/xml_sax_reader#xml.sax.xmlreader.IncrementalParser" >}}) 的实例额外提供了下列方法:

## IncrementalParser.**feed**(*data*)

​	处理 *data* 的一个分块。

## IncrementalParser.**close**()

​	确定文档的结尾。 这将检查只能在结尾处检查的格式是否良好的条件，发起调用处理程序，并可能会清理在解析期间分配的资源。

## IncrementalParser.**reset**()

​	此方法会在调用 close 来重置解析器以便其准备好解析新的文档之后被调用。 在 close 之后未调用 reset 即调用 parse 或 feed 的结果是未定义的。



## Locator 对象

[`Locator`]({{< ref "/library/markup/xml_sax_reader#xml.sax.xmlreader.Locator" >}}) 的实例提供了下列方法:

## Locator.**getColumnNumber**()

​	返回当前事件开始位置的列号。

## Locator.**getLineNumber**()

​	返回当前事件开始位置的行号。

## Locator.**getPublicId**()

​	返回当前事件的公有标识符。

## Locator.**getSystemId**()

​	返回当前事件的系统标识符。



## InputSource 对象

## InputSource.**setPublicId**(*id*)

​	设置该 [`InputSource`]({{< ref "/library/markup/xml_sax_reader#xml.sax.xmlreader.InputSource" >}}) 的公有标识符。

## InputSource.**getPublicId**()

​	返回此 [`InputSource`]({{< ref "/library/markup/xml_sax_reader#xml.sax.xmlreader.InputSource" >}}) 的公有标识符。

## InputSource.**setSystemId**(*id*)

​	设置此 [`InputSource`]({{< ref "/library/markup/xml_sax_reader#xml.sax.xmlreader.InputSource" >}}) 的系统标识符。

## InputSource.**getSystemId**()

​	返回此 [`InputSource`]({{< ref "/library/markup/xml_sax_reader#xml.sax.xmlreader.InputSource" >}}) 的系统标识符。

## InputSource.**setEncoding**(*encoding*)

​	设置此 [`InputSource`]({{< ref "/library/markup/xml_sax_reader#xml.sax.xmlreader.InputSource" >}}) 的字符编码格式。

​	编码格式必须是 XML 编码声明可接受的字符串（参见 XML 建议规范第 4.3.3 节）。

​	如果 [`InputSource`]({{< ref "/library/markup/xml_sax_reader#xml.sax.xmlreader.InputSource" >}}) 还包含一个字符流则 [`InputSource`]({{< ref "/library/markup/xml_sax_reader#xml.sax.xmlreader.InputSource" >}}) 的 encoding 属性会被忽略。

## InputSource.**getEncoding**()

​	获取此 InputSource 的字符编码格式。

## InputSource.**setByteStream**(*bytefile*)

​	设置此输入源的字节流（为 [binary file]({{< ref "/glossary/idx#term-binary-file" >}}) 对象）。

​	如果还指定了一个字符流被则 SAX 解析器会忽略此设置，但它将优先使用字节流而不是自己打开一个 URI 连接。

​	如果应用程序知道字节流的字符编码格式，它应当使用 setEncoding 方法来设置它。

## InputSource.**getByteStream**()

​	获取此输入源的字节流。

​	getEncoding 方法将返回该字节流的字符编码格式，如果未知则返回 `None`。

## InputSource.**setCharacterStream**(*charfile*)

​	设置此输入源的字符流 (为 [text file]({{< ref "/glossary/idx#term-text-file" >}}) 对象)。

​	如果指定了一个字符流，SAX 解析器将忽略任何字节流并且不会尝试打开一个指向系统标识符的 URI 连接。

## InputSource.**getCharacterStream**()

​	获取此输入源的字符流。



## `Attributes` 接口

`Attributes` 对象实现了一部分 [映射协议]({{< ref "/glossary/idx#term-mapping" >}})，包括 `copy()`, `get()`, [`__contains__()`]({{< ref "/reference/datamodel#object.__contains__" >}}), `items()`, `keys()` 和 `values()` 等方法。 还提供了下列方法:

## Attributes.**getLength**()

​	返回属性的数量。

## Attributes.**getNames**()

​	返回属性的名称。

## Attributes.**getType**(*name*)

​	返回属性 *name* 的类型，通常为 `'CDATA'`。

## Attributes.**getValue**(*name*)

​	返回属性 *name* 的值。



## `AttributesNS` 接口

​	此接口是 `Attributes` 接口（参见 [Attributes 接口]({{< ref "/library/markup/xml_sax_reader#attributes-objects" >}}) 章节）的一个子类型。 那个接口所支持的所有方法在 `AttributesNS` 对象上也都可用。

​	下列方法也是可用的:

## AttributesNS.**getValueByQName**(*name*)

​	返回一个限定名称的值。

## AttributesNS.**getNameByQName**(*name*)

​	返回限定名称 *name* 的 `(namespace, localname)` 对。

## AttributesNS.**getQNameByName**(*name*)

​	返回 `(namespace, localname)` 对的限定名称。

## AttributesNS.**getQNames**()

​	返回所有属性的限定名称。
