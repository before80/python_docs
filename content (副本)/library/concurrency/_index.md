+++
title = "并发执行"
date = 2024-11-14T22:00:25+08:00
weight = 160
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/concurrency.html](https://docs.python.org/zh-cn/3.13/library/concurrency.html)
>
> 收录该文档的时间：`2024-11-14T22:00:25+08:00`

# 并发执行

​	本章中描述的模块支持并发执行代码。 适当的工具选择取决于要执行的任务（CPU密集型或IO密集型）和偏好的开发风格（事件驱动的协作式多任务或抢占式多任务处理）。 这是一个概述：
