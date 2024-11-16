+++
title = "html.entities --- HTML 一般实体的定义"
date = 2024-11-15T15:24:42+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/html.entities.html](https://docs.python.org/zh-cn/3.13/library/html.entities.html)
>
> 收录该文档的时间：`2024-11-15T15:24:42+08:00`

# `html.entities` --- HTML 一般实体的定义

**源码：** [Lib/html/entities.py](https://github.com/python/cpython/tree/3.13/Lib/html/entities.py)

------

​	该模块定义了四个词典， [`html5`]({{< ref "/library/markup/html_entities#html.entities.html5" >}})、 [`name2codepoint`]({{< ref "/library/markup/html_entities#html.entities.name2codepoint" >}})、 [`codepoint2name`]({{< ref "/library/markup/html_entities#html.entities.codepoint2name" >}})、以及 [`entitydefs`]({{< ref "/library/markup/html_entities#html.entities.entitydefs" >}})。

## html.entities.**html5**

​	将 HTML5 命名字符引用 [[1\]]({{< ref "/library/markup/html_entities#id2" >}}) 映射到等效的 Unicode 字符的字典，例如 `html5['gt;'] == '>'`。 请注意，尾随的分号包含在名称中（例如 `'gt;'` ），但是即使没有分号，一些名称也会被标准接受，在这种情况下，名称出现时带有和不带有 `';'`。另见 [`html.unescape()`]({{< ref "/library/markup/html#html.unescape" >}})。

> Added in version 3.3.
>

## html.entities.**entitydefs**

​	将 XHTML 1.0 实体定义映射到 ISO Latin-1 中的替换文本的字典。

## html.entities.**name2codepoint**

​	一个将 HTML4 实体名称映射到 Unicode 代码点的字典。

## html.entities.**codepoint2name**

​	一个将 Unicode 代码点映射到 HTML4 实体名称的字典。

​	备注

[[1]({{< ref "/library/markup/html_entities#id1" >}})]

​	参见 https://html.spec.whatwg.org/multipage/named-characters.html#named-character-references
