+++
title = "urllib --- URL 处理模块"
date = 2024-11-15T20:28:46+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/urllib.html](https://docs.python.org/zh-cn/3.13/library/urllib.html)
>
> 收录该文档的时间：`2024-11-15T20:28:46+08:00`

# `urllib` --- URL 处理模块

**源代码:** [Lib/urllib/](https://github.com/python/cpython/tree/3.13/Lib/urllib/)

------

​	`urllib` 是一个收集了多个涉及 URL 的模块的包：

- [`urllib.request`]({{< ref "/library/internet/urllib/urllib_request#module-urllib.request" >}}) 打开和读取 URL
- [`urllib.error`]({{< ref "/library/internet/urllib/urllib_error#module-urllib.error" >}}) 包含 [`urllib.request`]({{< ref "/library/internet/urllib/urllib_request#module-urllib.request" >}}) 抛出的异常
- [`urllib.parse`]({{< ref "/library/internet/urllib/urllib_parse#module-urllib.parse" >}}) 用于解析 URL
- [`urllib.robotparser`]({{< ref "/library/internet/urllib/urllib_robotparser#module-urllib.robotparser" >}}) 用于解析 `robots.txt` 文件
