+++
title = "安全考量"
date = 2024-11-14T22:00:25+08:00
weight = 360
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/security_warnings.html](https://docs.python.org/zh-cn/3.13/library/security_warnings.html)
>
> 收录该文档的时间：`2024-11-14T22:00:25+08:00`

# 安全考量

下列模块具有专门的安全事项:

- [`base64`](https://docs.python.org/zh-cn/3.13/library/base64.html#module-base64): [base64 安全事项](https://docs.python.org/zh-cn/3.13/library/base64.html#base64-security)，参见 [**RFC 4648**](https://datatracker.ietf.org/doc/html/rfc4648.html)
- [`hashlib`](https://docs.python.org/zh-cn/3.13/library/hashlib.html#module-hashlib): [所有构造器都接受一个 "usedforsecurity" 仅限关键字参数以停用已知的不安全和已封禁的算法](https://docs.python.org/zh-cn/3.13/library/hashlib.html#hashlib-usedforsecurity)
- [`http.server`](https://docs.python.org/zh-cn/3.13/library/http.server.html#module-http.server) 不适合生产用途，只实现了基本的安全检查。 请参阅 [安全性考量](https://docs.python.org/zh-cn/3.13/library/http.server.html#http-server-security)。
- [`logging`](https://docs.python.org/zh-cn/3.13/library/logging.html#module-logging): [日志记录配置使用了 eval()](https://docs.python.org/zh-cn/3.13/library/logging.config.html#logging-eval-security)
- [`multiprocessing`](https://docs.python.org/zh-cn/3.13/library/multiprocessing.html#module-multiprocessing): [Connection.recv() 使用了 pickle](https://docs.python.org/zh-cn/3.13/library/multiprocessing.html#multiprocessing-recv-pickle-security)
- [`pickle`](https://docs.python.org/zh-cn/3.13/library/pickle.html#module-pickle): [在 pickle 中限制全局变量](https://docs.python.org/zh-cn/3.13/library/pickle.html#pickle-restrict)
- [`random`](https://docs.python.org/zh-cn/3.13/library/random.html#module-random) 不应当被用于安全目的，而应改用 [`secrets`](https://docs.python.org/zh-cn/3.13/library/secrets.html#module-secrets)
- [`shelve`](https://docs.python.org/zh-cn/3.13/library/shelve.html#module-shelve): [shelve 是基于 pickle 的因此不适用于处理不受信任的源](https://docs.python.org/zh-cn/3.13/library/shelve.html#shelve-security)
- [`ssl`](https://docs.python.org/zh-cn/3.13/library/ssl.html#module-ssl): [SSL/TLS 安全事项](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl-security)
- [`subprocess`](https://docs.python.org/zh-cn/3.13/library/subprocess.html#module-subprocess): [子进程安全事项](https://docs.python.org/zh-cn/3.13/library/subprocess.html#subprocess-security)
- [`tempfile`](https://docs.python.org/zh-cn/3.13/library/tempfile.html#module-tempfile): [mktemp 由于存在竞争条件缺陷已被弃用](https://docs.python.org/zh-cn/3.13/library/tempfile.html#tempfile-mktemp-deprecated)
- [`xml`](https://docs.python.org/zh-cn/3.13/library/xml.html#module-xml): [XML 安全缺陷](https://docs.python.org/zh-cn/3.13/library/xml.html#xml-vulnerabilities)
- [`zipfile`](https://docs.python.org/zh-cn/3.13/library/zipfile.html#module-zipfile): [恶意处理的 .zip 文件可能导致硬盘空间耗尽](https://docs.python.org/zh-cn/3.13/library/zipfile.html#zipfile-resources-limitations)

[`-I`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-I) 命令行选项可被用来在隔离模式下运行 Python。 当它无法使用时，可以使用 [`-P`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#cmdoption-P) 选项或 [`PYTHONSAFEPATH`](https://docs.python.org/zh-cn/3.13/using/cmdline.html#envvar-PYTHONSAFEPATH) 环境变量以避免在 [`sys.path`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.path) 中预置一个潜在的不安全路径，如当前目录、脚本的目录或一个空字符串。
