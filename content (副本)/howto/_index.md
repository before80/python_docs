+++
title = "Python 指南"
linkTitle="Python 指南"
date = 2024-11-14T22:23:58+08:00
type = "docs"
description = ""
isCJKLanguage = true
draft = false
[menu.main]
    weight = 60

+++

> 原文：[https://docs.python.org/zh-cn/3.13/howto/index.html](https://docs.python.org/zh-cn/3.13/howto/index.html)
>
> 收录该文档的时间：`2024-11-14T22:23:58+08:00`

# Python 指南

​	Python HOWTO 是覆盖特定主题深度文档。 这份合集以Modeled on the Linux 文档项目的 HOWTO 合为样板，它致力于提供比 Python 库参考更详细的文档。

通用：

- [注解最佳实践]({{< ref "/howto/annotations#annotations-howto" >}})
- [argparse 教程]({{< ref "/library/allos/argparse_tutorial#argparse-tutorial" >}})
- [描述器指南]({{< ref "/howto/descriptor#descriptorhowto" >}})
- [Enum 指南]({{< ref "/howto/enum#enum-howto" >}})
- [函数式编程指引]({{< ref "/howto/functional#functional-howto" >}})
- [ipaddress模块介绍]({{< ref "/howto/ipaddress#ipaddress-howto" >}})
- [日志指南]({{< ref "/howto/logging#logging-howto" >}})
- [日志专题手册]({{< ref "/howto/logging-cookbook#logging-cookbook" >}})
- [正则表达式指南]({{< ref "/howto/regex#regex-howto" >}})
- [排序的技术]({{< ref "/howto/sorting#sortinghowto" >}})
- [Unicode 指南]({{< ref "/howto/unicode#unicode-howto" >}})
- [如何利用 urllib 包获取网络资源]({{< ref "/howto/urllib2#urllib-howto" >}})

高级开发：

- [用 Python 进行 Curses 编程]({{< ref "/howto/curses#curses-howto" >}})
- [Python 对自由线程的实验性支持]({{< ref "/howto/free-threading-python#freethreading-python-howto" >}})
- [自由线程的 C API 扩展支持]({{< ref "/howto/free-threading-extensions#freethreading-extensions-howto" >}})
- [隔离扩展模块]({{< ref "/howto/isolating-extensions#isolating-extensions-howto" >}})
- [Python 2.3 方法解析顺序]({{< ref "/howto/mro#python-2-3-mro" >}})
- [套接字编程指南]({{< ref "/howto/sockets#socket-howto" >}})
- [定时器文件描述符指南]({{< ref "/howto/timerfd#timerfd-howto" >}})
- [将扩展模块移植到 Python 3]({{< ref "/howto/cporting#cporting-howto" >}})

调试和性能分析：

- [使用 GDB 调试 C API 扩展和 CPython 内部代码]({{< ref "/howto/gdb_helpers#gdb" >}})
- [使用 DTrace 和 SystemTap 检测CPython]({{< ref "/howto/instrumentation#instrumentation" >}})
- [Python 对 Linux perf 性能分析器的支持]({{< ref "/howto/perf_profiling#perf-profiling" >}})
