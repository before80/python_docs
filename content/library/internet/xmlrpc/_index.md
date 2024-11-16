+++
title = "xmlrpc --- XMLRPC 服务端与客户端模块"
date = 2024-11-15T20:28:46+08:00
weight = 190
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/xmlrpc.html](https://docs.python.org/zh-cn/3.13/library/xmlrpc.html)
>
> 收录该文档的时间：`2024-11-15T20:28:46+08:00`

# `xmlrpc` --- XMLRPC 服务端与客户端模块

​	XML-RPC 是一种远程过程调用方法，它使用通过 HTTP 传递的 XML 作为载体。 有了它，客户端可以在远程服务器上调用带参数的方法（服务器以 URI 命名）并获取结构化的数据。

​	`xmlrpc` 是一个集合了 XML-RPC 服务端与客户端实现模块的包。 这些模块是:

- [`xmlrpc.client`]({{< ref "/library/internet/xmlrpc_client#module-xmlrpc.client" >}})
- [`xmlrpc.server`]({{< ref "/library/internet/xmlrpc_server#module-xmlrpc.server" >}})
