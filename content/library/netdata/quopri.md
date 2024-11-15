+++
title = "quopri --- 编码与解码 MIME 转码的可打印数据"
date = 2024-11-15T14:53:40+08:00
weight = 60
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/quopri.html](https://docs.python.org/zh-cn/3.13/library/quopri.html)
>
> 收录该文档的时间：`2024-11-15T14:53:40+08:00`

# `quopri` --- 编码与解码 MIME 转码的可打印数据

**源代码:** [Lib/quopri.py](https://github.com/python/cpython/tree/3.13/Lib/quopri.py)

------

​	此模块会执行转换后可打印的传输编码与解码，具体定义见 [**RFC 1521**](https://datatracker.ietf.org/doc/html/rfc1521.html): "MIME (Multipurpose Internet Mail Extensions) Part One: Mechanisms for Specifying and Describing the Format of Internet Message Bodies"。 转换后可打印的编码格式被设计用于只包含相对较少的不可打印字符的数据；如果存在大量这样的字符，通过 [`base64`](https://docs.python.org/zh-cn/3.13/library/base64.html#module-base64) 模块所提供的 base64 编码方案会更为紧凑，例如当发送图片文件时。

## quopri.**decode**(*input*, *output*, *header=False*)

​	解码 *input* 文件的内容并将已解码二进制数据结果写入 *output* 文件。 *input* 和 *output* 必须为 [二进制文件对象](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-object)。 如果提供了可选参数 *header* 且为真值，下划线将被解码为空格。 此函数可用于解码“Q”编码的头数据，具体描述见 [**RFC 1522**](https://datatracker.ietf.org/doc/html/rfc1522.html): "MIME (Multipurpose Internet Mail Extensions) Part Two: Message Header Extensions for Non-ASCII Text"。

## quopri.**encode**(*input*, *output*, *quotetabs*, *header=False*)

​	编码 *input* 文件的内容并将转换后可打印的数据结果写入 *output* 文件。 *input* 和 *output* 必须为 [二进制文件对象](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-object). *quotetabs* 是一个非可选的旗标，它控制是否要编码内嵌的空格与制表符；当为真值时将编码此类内嵌空白符，当为假值时则保持原样不进行编码。 请注意出现在行尾的空格与制表符总是会被编码，具体描述见 [**RFC 1521**](https://datatracker.ietf.org/doc/html/rfc1521.html)。 *header* 旗标控制空格符是否要编码为下划线，具体描述见 [**RFC 1522**](https://datatracker.ietf.org/doc/html/rfc1522.html)。

## quopri.**decodestring**(*s*, *header=False*)

​	类似 [`decode()`](https://docs.python.org/zh-cn/3.13/library/quopri.html#quopri.decode)，区别在于它接受一个源 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 并返回对应的已解码 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)。

## quopri.**encodestring**(*s*, *quotetabs=False*, *header=False*)

​	类型 [`encode()`](https://docs.python.org/zh-cn/3.13/library/quopri.html#quopri.encode)，区别在于它接受一个源 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 并返回对应的已编码 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)。 在默认情况下，它会发送 `False` 值给 [`encode()`](https://docs.python.org/zh-cn/3.13/library/quopri.html#quopri.encode) 函数的 *quotetabs* 形参。

> 参见
>
> 
>
> - 模块 [`base64`](https://docs.python.org/zh-cn/3.13/library/base64.html#module-base64)
>
>   编码与解码 MIME base64 数据
