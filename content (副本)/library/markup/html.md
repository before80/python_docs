+++
title = "html --- 超文本标记语言支持"
date = 2024-11-15T15:24:42+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/html.html](https://docs.python.org/zh-cn/3.13/library/html.html)
>
> 收录该文档的时间：`2024-11-15T15:24:42+08:00`

# `html` --- 超文本标记语言支持

**源码：** [Lib/html/__init__.py](https://github.com/python/cpython/tree/3.13/Lib/html/__init__.py)

------

​	该模块定义了操作HTML的工具。

## html.**escape**(*s*, *quote=True*)

​	将字符串 *s* 中的字符 `&` 、 `<` 和 `>` 转换为安全的HTML序列。 如果需要在 HTML 中显示可能包含此类字符的文本，请使用此选项。 如果可选的标志 *quote* 为真值，则字符 (`"`) 和 (`'`) 也被转换；这有助于包含在由引号分隔的 HTML 属性中，如 `<a href="...">`。

> Added in version 3.2.
>

## html.**unescape**(*s*)

​	将字符串 *s* 中的所有命名和数字字符引用 (例如 `>`, `>`, `>`) 转换为相应的Unicode字符。 此函数使用HTML 5标准为有效和无效字符引用定义的规则，以及 [`HTML 5 命名字符引用列表`]({{< ref "/library/markup/html_entities#html.entities.html5" >}})。

> Added in version 3.4.
>

------

`html` 包中的子模块是：

- [`html.parser`]({{< ref "/library/markup/html_parser#module-html.parser" >}}) —— 具有宽松解析模式的HTML / XHTML解析器
- [`html.entities`]({{< ref "/library/markup/html_entities#module-html.entities" >}}) -- HTML 实体定义
