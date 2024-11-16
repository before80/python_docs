+++
title = "syslog --- Unix syslog 库例程"
date = 2024-11-15T21:30:54+08:00
weight = 80
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/syslog.html](https://docs.python.org/zh-cn/3.13/library/syslog.html)
>
> 收录该文档的时间：`2024-11-15T21:30:54+08:00`

# `syslog` --- Unix syslog 库例程

------

​	此模块提供一个接口到Unix `syslog` 日常库. 参考 Unix 手册页关于 `syslog` 设施的详细描述.

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not iOS.

​	此模块包装了系统 `syslog` 例程族。 一个能与 syslog 服务器对话的纯 Python 库则在 [`logging.handlers`]({{< ref "/library/allos/logging_handlers#module-logging.handlers" >}}) 模块中以 [`SysLogHandler`]({{< ref "/library/allos/logging_handlers#logging.handlers.SysLogHandler" >}}) 类的形式提供。

​	这个模块定义了以下函数：

## syslog.**syslog**(*message*)

## syslog.**syslog**(*priority*, *message*)

​	将字符串 *message* 发送到系统日志记录器。 如有必要会添加末尾换行符。 每条消息都带有一个由 *facility* 和 *level* 组成的优先级标价签。 可选的 *priority* 参数默认值为 [`LOG_INFO`]({{< ref "/library/unix/syslog#syslog.LOG_INFO" >}})，它确定消息的优先级。 如果未在 *priority* 中使用逻辑或 (`LOG_INFO | LOG_USER`) 对 facility 进行编码，则会使用在 [`openlog()`]({{< ref "/library/unix/syslog#syslog.openlog" >}}) 调用中所给定的值。

​	如果 [`openlog()`]({{< ref "/library/unix/syslog#syslog.openlog" >}}) 未在对 [`syslog()`]({{< ref "/library/unix/syslog#module-syslog" >}}) 的调用之前被调用，则将不带参数地调用 [`openlog()`]({{< ref "/library/unix/syslog#syslog.openlog" >}})。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `syslog.syslog` 并附带参数 `priority`, `message`。

> 在 3.2 版本发生变更: 在之前的版本中，如果 [`openlog()`]({{< ref "/library/unix/syslog#syslog.openlog" >}}) 未在对 [`syslog()`]({{< ref "/library/unix/syslog#module-syslog" >}}) 的调用之前被调用则它将不会被自动调用，而是由 syslog 实现来负责调用 `openlog()`。

> 在 3.12 版本发生变更: 此函数在子解释器中受到限制。 （该限制只影响在多解释器中运行的代码因而与大多数用户无关。） [`openlog()`]({{< ref "/library/unix/syslog#syslog.openlog" >}}) 必须在子解释器使用 [`syslog()`]({{< ref "/library/unix/syslog#module-syslog" >}}) 之前在主解释器中被调用。 否则它将引发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。

## syslog.**openlog**([*ident*[, *logoption*[, *facility*]]])

​	后续 [`syslog()`]({{< ref "/library/unix/syslog#module-syslog" >}}) 调用的日志选项可以通过调用 [`openlog()`]({{< ref "/library/unix/syslog#syslog.openlog" >}}) 来设置。 如果日志当前未打开则 [`syslog()`]({{< ref "/library/unix/syslog#module-syslog" >}}) 将不带参数地调用 [`openlog()`]({{< ref "/library/unix/syslog#syslog.openlog" >}})。

​	可选的 *ident* 关键字参数是在每条消息前添加的字符串，默认为 `sys.argv[0]` 去除打头的路径部分。 可选的 *logoption* 关键字参数（默认为 0）是一个位字段 -- 请参见下文了解可能的组合值。 可选的 *facility* 关键字参数 (默认为 [`LOG_USER`]({{< ref "/library/unix/syslog#syslog.LOG_USER" >}})) 为没有显式编码 facility 的消息设置默认的 facility。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `syslog.openlog` 并附带参数 `ident`, `logoption`, `facility`。

> 在 3.2 版本发生变更: 在之前的版本中，不允许使用关键字参数，并且要求必须有 *ident*。

> 在 3.12 版本发生变更: 此函数在子解释器中受到限制。 （该限制只影响在多解释器中运行的代码因而与大多数用户无关。） 此函数只能在主解释器中被调用。 如果在子解释器中被调用它将引发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。

## syslog.**closelog**()

​	重置日志模块值并且调用系统库 `closelog()`.

​	这使得此模块在初始导入时行为固定。 例如，[`openlog()`]({{< ref "/library/unix/syslog#syslog.openlog" >}}) 将在首次调用 [`syslog()`]({{< ref "/library/unix/syslog#module-syslog" >}}) 时被调用（如果 [`openlog()`]({{< ref "/library/unix/syslog#syslog.openlog" >}}) 还未被调用过），并且 *ident* 和其他 [`openlog()`]({{< ref "/library/unix/syslog#syslog.openlog" >}}) 形参会被重置为默认值。

​	引发一个不带参数的 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `syslog.closelog`。

> 在 3.12 版本发生变更: 此函数在子解释器中受到限制。 （该限制只影响在多解释器中运行的代码因而与大多数用户无关。） 此函数只能在主解释器中被调用。 如果在子解释器中被调用它将引发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。

## syslog.**setlogmask**(*maskpri*)

​	将优先级掩码设为 *maskpri* 并返回之前的掩码值。 调用 [`syslog()`]({{< ref "/library/unix/syslog#module-syslog" >}}) 并附带未在 *maskpri* 中设置的优先级将会被忽略。 默认设置为记录所有优先级。 函数 `LOG_MASK(pri)` 可计算单个优先级 *pri* 的掩码。 函数 `LOG_UPTO(pri)` 可计算包括 *pri* 在内的所有优先级的掩码。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `syslog.setlogmask` 并附带参数 `maskpri`。

​	此模块定义了一下常量:

## syslog.**LOG_EMERG**

## syslog.**LOG_ALERT**

## syslog.**LOG_CRIT**

## syslog.**LOG_ERR**

## syslog.**LOG_WARNING**

## syslog.**LOG_NOTICE**

## syslog.**LOG_INFO**

## syslog.**LOG_DEBUG**

​	优先级别（从高到低）。

## syslog.**LOG_AUTH**

## syslog.**LOG_AUTHPRIV**

## syslog.**LOG_CRON**

## syslog.**LOG_DAEMON**

## syslog.**LOG_FTP**

## syslog.**LOG_INSTALL**

## syslog.**LOG_KERN**

## syslog.**LOG_LAUNCHD**

## syslog.**LOG_LPR**

## syslog.**LOG_MAIL**

## syslog.**LOG_NETINFO**

## syslog.**LOG_NEWS**

## syslog.**LOG_RAS**

## syslog.**LOG_REMOTEAUTH**

## syslog.**LOG_SYSLOG**

## syslog.**LOG_USER**

## syslog.**LOG_UUCP**

## syslog.**LOG_LOCAL0**

## syslog.**LOG_LOCAL1**

## syslog.**LOG_LOCAL2**

## syslog.**LOG_LOCAL3**

## syslog.**LOG_LOCAL4**

## syslog.**LOG_LOCAL5**

## syslog.**LOG_LOCAL6**

## syslog.**LOG_LOCAL7**

​	功能项，根据在 `<syslog.h>` 中 [`LOG_AUTHPRIV`]({{< ref "/library/unix/syslog#syslog.LOG_AUTHPRIV" >}}), [`LOG_FTP`]({{< ref "/library/unix/syslog#syslog.LOG_FTP" >}}), [`LOG_NETINFO`]({{< ref "/library/unix/syslog#syslog.LOG_NETINFO" >}}), [`LOG_REMOTEAUTH`]({{< ref "/library/unix/syslog#syslog.LOG_REMOTEAUTH" >}}), [`LOG_INSTALL`]({{< ref "/library/unix/syslog#syslog.LOG_INSTALL" >}}) 和 [`LOG_RAS`]({{< ref "/library/unix/syslog#syslog.LOG_RAS" >}}) 的可用性确定。

> 在 3.13 版本发生变更: 增加了 [`LOG_FTP`]({{< ref "/library/unix/syslog#syslog.LOG_FTP" >}}), [`LOG_NETINFO`]({{< ref "/library/unix/syslog#syslog.LOG_NETINFO" >}}), [`LOG_REMOTEAUTH`]({{< ref "/library/unix/syslog#syslog.LOG_REMOTEAUTH" >}}), [`LOG_INSTALL`]({{< ref "/library/unix/syslog#syslog.LOG_INSTALL" >}}), [`LOG_RAS`]({{< ref "/library/unix/syslog#syslog.LOG_RAS" >}}) 和 [`LOG_LAUNCHD`]({{< ref "/library/unix/syslog#syslog.LOG_LAUNCHD" >}})。

## syslog.**LOG_PID**

## syslog.**LOG_CONS**

## syslog.**LOG_NDELAY**

## syslog.**LOG_ODELAY**

## syslog.**LOG_NOWAIT**

## syslog.**LOG_PERROR**

​	日志选项，根据在 `<syslog.h>` 中 [`LOG_ODELAY`]({{< ref "/library/unix/syslog#syslog.LOG_ODELAY" >}}), [`LOG_NOWAIT`]({{< ref "/library/unix/syslog#syslog.LOG_NOWAIT" >}}) 和 [`LOG_PERROR`]({{< ref "/library/unix/syslog#syslog.LOG_PERROR" >}}) 的可用性确定。

## 例子

### 简单示例

​	一个简单的示例集:

```
import syslog

syslog.syslog('Processing started')
if error:
    syslog.syslog(syslog.LOG_ERR, 'Processing started')
```

​	一个设置多种日志选项的示例，其中有在日志消息中包含进程 ID，以及将消息写入用于邮件日志记录的目标设施等:

```
syslog.openlog(logoption=syslog.LOG_PID, facility=syslog.LOG_MAIL)
syslog.syslog('E-mail processing initiated...')
```
