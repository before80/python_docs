+++
title = "网络和进程间通信"
date = 2024-11-14T22:00:25+08:00
weight = 170
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/ipc.html](https://docs.python.org/zh-cn/3.13/library/ipc.html)
>
> 收录该文档的时间：`2024-11-14T22:00:25+08:00`

# 网络和进程间通信

​	本章介绍的模块提供了网络和进程间通信的机制。

​	某些模块仅适用于同一台机器上的两个进程，例如 [`signal`]({{< ref "/library/ipc/signal#module-signal" >}}) 和 [`mmap`]({{< ref "/library/ipc/mmap#module-mmap" >}}) 。 其他模块支持两个或多个进程可用于跨机器通信的网络协议。

​	本章中描述的模块列表是：
