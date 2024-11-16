+++
title = "`urllib.error` --- 由 urllib.request 引发的异常类"
date = 2024-11-15T20:28:46+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/urllib.error.html](https://docs.python.org/zh-cn/3.13/library/urllib.error.html)
>
> 收录该文档的时间：`2024-11-15T20:28:46+08:00`

# `urllib.error` --- 由 urllib.request 引发的异常类

**源代码：** [Lib/urllib/error.py](https://github.com/python/cpython/tree/3.13/Lib/urllib/error.py)

------

[`urllib.error`](https://docs.python.org/zh-cn/3.13/library/urllib.error.html#module-urllib.error) 模块为 [`urllib.request`](https://docs.python.org/zh-cn/3.13/library/urllib.request.html#module-urllib.request) 所引发的异常定义了异常类。 基础异常类是 [`URLError`](https://docs.python.org/zh-cn/3.13/library/urllib.error.html#urllib.error.URLError)。

​	下列异常会被 [`urllib.error`](https://docs.python.org/zh-cn/3.13/library/urllib.error.html#module-urllib.error) 按需引发：

## *exception* urllib.error.**URLError**

​	处理程序在遇到问题时会引发此异常（或其派生的异常）。 它是 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 的一个子类。

## **reason**

​	此错误的原因。 它可以是一个消息字符串或另一个异常实例。

*在 3.3 版本发生变更:* [`URLError`](https://docs.python.org/zh-cn/3.13/library/urllib.error.html#urllib.error.URLError) 曾经是 [`IOError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#IOError) 的子类型，现在它是 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 的一个别名。

## *exception* urllib.error.**HTTPError**(*url*, *code*, *msg*, *hdrs*, *fp*)

​	虽然是一个异常（[`URLError`](https://docs.python.org/zh-cn/3.13/library/urllib.error.html#urllib.error.URLError) 的一个子类），[`HTTPError`](https://docs.python.org/zh-cn/3.13/library/urllib.error.html#urllib.error.HTTPError) 也可以作为一个非异常的文件类返回值（与 [`urlopen()`](https://docs.python.org/zh-cn/3.13/library/urllib.request.html#urllib.request.urlopen) 返所回的对象相同）。 这适用于处理特殊 HTTP 错误例如作为认证请求的时候。

## **url**

​	包含请求 URL。 是 *filename* 属性的别名。

## **code**

​	一个 HTTP 状态码，具体定义见 [**RFC 2616**](https://datatracker.ietf.org/doc/html/rfc2616.html)。 这个数字的值对应于存放在 [`http.server.BaseHTTPRequestHandler.responses`](https://docs.python.org/zh-cn/3.13/library/http.server.html#http.server.BaseHTTPRequestHandler.responses) 代码字典中的某个值。

## **reason**

​	这通常是一个解释本次错误原因的字符串。 为 *msg* 属性的别名。

## **headers**

​	导致 [`HTTPError`](https://docs.python.org/zh-cn/3.13/library/urllib.error.html#urllib.error.HTTPError) 的特定 HTTP 请求的 HTTP 响应头。 为 *hdrs* 属性的别名。

> Added in version 3.4.
>

## **fp**

​	可供读取 HTTP 错误消息体的文件型对象。

## *exception* urllib.error.**ContentTooShortError**(*msg*, *content*)

​	此异常会在 [`urlretrieve()`](https://docs.python.org/zh-cn/3.13/library/urllib.request.html#urllib.request.urlretrieve) 函数检测到已下载的数据量少于预期量（由 *Content-Length* 标头给出）时被引发。

## **content**

​	已下载（并可能被截断）的数据。
