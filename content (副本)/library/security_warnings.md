+++
title = "安全考量"
date = 2024-11-14T22:00:25+08:00
weight = 360
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/security_warnings.html](https://docs.python.org/zh-cn/3.13/library/security_warnings.html)
>
> 收录该文档的时间：`2024-11-14T22:00:25+08:00`

# 安全考量

下列模块具有专门的安全事项:

- [`base64`]({{< ref "/library/netdata/base64#module-base64" >}}): [base64 安全事项]({{< ref "/library/netdata/base64#base64-security" >}})，参见 [**RFC 4648**](https://datatracker.ietf.org/doc/html/rfc4648.html)
- [`hashlib`]({{< ref "/library/crypto/hashlib#module-hashlib" >}}): [所有构造器都接受一个 "usedforsecurity" 仅限关键字参数以停用已知的不安全和已封禁的算法]({{< ref "/library/crypto/hashlib#hashlib-usedforsecurity" >}})
- [`http.server`]({{< ref "/library/internet/http_server#module-http.server" >}}) 不适合生产用途，只实现了基本的安全检查。 请参阅 [安全性考量]({{< ref "/library/internet/http_server#http-server-security" >}})。
- [`logging`]({{< ref "/library/allos/logging#module-logging" >}}): [日志记录配置使用了 eval()]({{< ref "/library/allos/logging_config#logging-eval-security" >}})
- [`multiprocessing`]({{< ref "/library/concurrency/multiprocessing#module-multiprocessing" >}}): [Connection.recv() 使用了 pickle]({{< ref "/library/concurrency/multiprocessing#multiprocessing-recv-pickle-security" >}})
- [`pickle`]({{< ref "/library/persistence/pickle#module-pickle" >}}): [在 pickle 中限制全局变量]({{< ref "/library/persistence/pickle#pickle-restrict" >}})
- [`random`]({{< ref "/library/numeric/random#module-random" >}}) 不应当被用于安全目的，而应改用 [`secrets`]({{< ref "/library/crypto/secrets#module-secrets" >}})
- [`shelve`]({{< ref "/library/persistence/shelve#module-shelve" >}}): [shelve 是基于 pickle 的因此不适用于处理不受信任的源]({{< ref "/library/persistence/shelve#shelve-security" >}})
- [`ssl`]({{< ref "/library/ipc/ssl#module-ssl" >}}): [SSL/TLS 安全事项]({{< ref "/library/ipc/ssl#ssl-security" >}})
- [`subprocess`]({{< ref "/library/concurrency/subprocess#module-subprocess" >}}): [子进程安全事项]({{< ref "/library/concurrency/subprocess#subprocess-security" >}})
- [`tempfile`]({{< ref "/library/filesys/tempfile#module-tempfile" >}}): [mktemp 由于存在竞争条件缺陷已被弃用]({{< ref "/library/filesys/tempfile#tempfile-mktemp-deprecated" >}})
- [`xml`]({{< ref "/library/markup/xml#module-xml" >}}): [XML 安全缺陷]({{< ref "/library/markup/xml#xml-vulnerabilities" >}})
- [`zipfile`]({{< ref "/library/archiving/zipfile#module-zipfile" >}}): [恶意处理的 .zip 文件可能导致硬盘空间耗尽]({{< ref "/library/archiving/zipfile#zipfile-resources-limitations" >}})

[`-I`]({{< ref "/using/cmdline#cmdoption-I" >}}) 命令行选项可被用来在隔离模式下运行 Python。 当它无法使用时，可以使用 [`-P`]({{< ref "/using/cmdline#cmdoption-P" >}}) 选项或 [`PYTHONSAFEPATH`]({{< ref "/using/cmdline#envvar-PYTHONSAFEPATH" >}}) 环境变量以避免在 [`sys.path`]({{< ref "/library/python/sys#sys.path" >}}) 中预置一个潜在的不安全路径，如当前目录、脚本的目录或一个空字符串。
