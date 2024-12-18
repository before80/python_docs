+++
title = "errno --- 标准 errno 系统符号"
date = 2024-11-15T12:09:25+08:00
weight = 130
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/errno.html](https://docs.python.org/zh-cn/3.13/library/errno.html)
>
> 收录该文档的时间：`2024-11-15T12:09:25+08:00`

# `errno` --- 标准 errno 系统符号

------

​	该模块提供了标准的 `errno` 系统符号。每个符号的值都是相应的整数值。名称和描述借用自 `linux/include/errno.h` ，它应该是全包含的。

## errno.**errorcode**

​	提供从 errno 值到底层系统中字符串名称的映射的字典。例如， `errno.errorcode[errno.EPERM]` 映射为 `'EPERM'` 。

​	如果要将数字的错误代码转换为错误信息，请使用 [`os.strerror()`]({{< ref "/library/allos/os#os.strerror" >}})。

​	在下面的列表中，当前平台上没有使用的符号没有被本模块定义。 已定义的符号的具体列表可参见 `errno.errorcode.keys()`。 可用的符号包括：

## errno.**EPERM**

​	操作不允许。这个错误被映射到异常 [`PermissionError`]({{< ref "/library/exceptions#PermissionError" >}}) 。

## errno.**ENOENT**

​	没有这样的文件或目录。这个错误被映射到异常 [`FileNotFoundError`]({{< ref "/library/exceptions#FileNotFoundError" >}}) 。

## errno.**ESRCH**

​	没有这样的进程。这个错误被映射到异常 [`ProcessLookupError`]({{< ref "/library/exceptions#ProcessLookupError" >}}) 。

## errno.**EINTR**

​	系统调用中断。这个错误被映射到异常 [`InterruptedError`]({{< ref "/library/exceptions#InterruptedError" >}}) 。

## errno.**EIO**

​	I/O 错误

## errno.**ENXIO**

​	无此设备或地址

## errno.**E2BIG**

​	参数列表过长

## errno.**ENOEXEC**

​	执行格式错误

## errno.**EBADF**

​	错误的文件号

## errno.**ECHILD**

​	没有子进程。这个错误被映射到异常 [`ChildProcessError`]({{< ref "/library/exceptions#ChildProcessError" >}}) 。

## errno.**EAGAIN**

​	再试一次。这个错误被映射到异常 [`BlockingIOError`]({{< ref "/library/exceptions#BlockingIOError" >}}) 。

## errno.**ENOMEM**

​	内存不足

## errno.**EACCES**

​	权限被拒绝。 这个错误被映射到异常 [`PermissionError`]({{< ref "/library/exceptions#PermissionError" >}}) 。

## errno.**EFAULT**

​	错误的地址

## errno.**ENOTBLK**

​	需要块设备

## errno.**EBUSY**

​	设备或资源忙

## errno.**EEXIST**

​	文件存在。这个错误被映射到异常 [`FileExistsError`]({{< ref "/library/exceptions#FileExistsError" >}}) 。

## errno.**EXDEV**

​	跨设备链接

## errno.**ENODEV**

​	无此设备

## errno.**ENOTDIR**

​	不是一个目录。这个错误被映射到异常 [`NotADirectoryError`]({{< ref "/library/exceptions#NotADirectoryError" >}}) 。

## errno.**EISDIR**

​	是一个目录。这个错误被映射到异常 [`IsADirectoryError`]({{< ref "/library/exceptions#IsADirectoryError" >}}) 。

## errno.**EINVAL**

​	无效的参数

## errno.**ENFILE**

​	文件表溢出

## errno.**EMFILE**

​	打开的文件过多

## errno.**ENOTTY**

​	不是打字机

## errno.**ETXTBSY**

​	文本文件忙

## errno.**EFBIG**

​	文件过大

## errno.**ENOSPC**

​	设备已无可用空间

## errno.**ESPIPE**

​	非法查找

## errno.**EROFS**

​	只读文件系统

## errno.**EMLINK**

​	链接过多

## errno.**EPIPE**

​	管道中断。这个错误被映射到异常 [`BrokenPipeError`]({{< ref "/library/exceptions#BrokenPipeError" >}}) 。

## errno.**EDOM**

​	数学参数超出函数范围

## errno.**ERANGE**

​	数学运算结果无法表示

## errno.**EDEADLK**

​	将发生资源死锁

## errno.**ENAMETOOLONG**

​	文件名过长

## errno.**ENOLCK**

​	没有可用的记录锁

## errno.**ENOSYS**

​	功能未实现

## errno.**ENOTEMPTY**

​	目录非空

## errno.**ELOOP**

​	遇到过多的符号链接

## errno.**EWOULDBLOCK**

​	操作会阻塞。这个错误被映射到异常 [`BlockingIOError`]({{< ref "/library/exceptions#BlockingIOError" >}}) 。

## errno.**ENOMSG**

​	没有所需类型的消息

## errno.**EIDRM**

​	标识符被移除

## errno.**ECHRNG**

​	信道编号超出范围

## errno.**EL2NSYNC**

​	级别 2 未同步

## errno.**EL3HLT**

​	级别 3 已停止

## errno.**EL3RST**

​	级别 3 重置

## errno.**ELNRNG**

​	链接编号超出范围

## errno.**EUNATCH**

​	未附加协议驱动

## errno.**ENOCSI**

​	没有可用的 CSI 结构

## errno.**EL2HLT**

​	级别 2 已停止

## errno.**EBADE**

​	无效的交换

## errno.**EBADR**

​	无效的请求描述符

## errno.**EXFULL**

​	交换已满

## errno.**ENOANO**

​	没有阳极

## errno.**EBADRQC**

​	无效的请求码·

## errno.**EBADSLT**

​	无效的槽位

## errno.**EDEADLOCK**

​	文件锁定死锁错误

## errno.**EBFONT**

​	错误的字体文件格式

## errno.**ENOSTR**

​	设备不是流

## errno.**ENODATA**

​	没有可用的数据

## errno.**ETIME**

​	计时器已到期

## errno.**ENOSR**

​	流资源不足

## errno.**ENONET**

​	机器不在网络上

## errno.**ENOPKG**

​	包未安装

## errno.**EREMOTE**

​	对象是远程的

## errno.**ENOLINK**

​	链接已被切断

## errno.**EADV**

​	广告错误

## errno.**ESRMNT**

​	挂载错误

## errno.**ECOMM**

​	发送时通讯错误

## errno.**EPROTO**

​	协议错误

## errno.**EMULTIHOP**

​	已尝试多跳

## errno.**EDOTDOT**

​	RFS 专属错误

## errno.**EBADMSG**

​	非数据消息

## errno.**EOVERFLOW**

​	值相对于已定义数据类型过大

## errno.**ENOTUNIQ**

​	名称在网络上不唯一

## errno.**EBADFD**

​	文件描述符处于错误状态

## errno.**EREMCHG**

​	远端地址已改变

## errno.**ELIBACC**

​	无法访问所需的共享库

## errno.**ELIBBAD**

​	访问已损坏的共享库

## errno.**ELIBSCN**

​	a.out 中的 .lib 部分已损坏

## errno.**ELIBMAX**

​	尝试链接过多的共享库

## errno.**ELIBEXEC**

​	无法直接执行共享库

## errno.**EILSEQ**

​	非法字节序列

## errno.**ERESTART**

​	已中断系统调用需要重启

## errno.**ESTRPIPE**

​	流管道错误

## errno.**EUSERS**

​	用户过多

## errno.**ENOTSOCK**

​	在非套接字上执行套接字操作

## errno.**EDESTADDRREQ**

​	需要目标地址

## errno.**EMSGSIZE**

​	消息过长

## errno.**EPROTOTYPE**

​	套接字的协议类型错误

## errno.**ENOPROTOOPT**

​	协议不可用

## errno.**EPROTONOSUPPORT**

​	协议不受支持

## errno.**ESOCKTNOSUPPORT**

​	套接字类型不受支持

## errno.**EOPNOTSUPP**

​	操作在传输端点上不受支持

## errno.**ENOTSUP**

​	操作不受支持

> Added in version 3.2.
>

## errno.**EPFNOSUPPORT**

​	协议族不受支持

## errno.**EAFNOSUPPORT**

​	地址族不受协议支持

## errno.**EADDRINUSE**

​	地址已被使用

## errno.**EADDRNOTAVAIL**

​	无法分配要求的地址

## errno.**ENETDOWN**

​	网络已断开

## errno.**ENETUNREACH**

​	网络不可达

## errno.**ENETRESET**

​	网络因重置而断开连接

## errno.**ECONNABORTED**

​	软件导致连接中止。这个错误被映射到异常 [`ConnectionAbortedError`]({{< ref "/library/exceptions#ConnectionAbortedError" >}}) 。

## errno.**ECONNRESET**

​	连接被对方重置。这个错误被映射到异常 [`ConnectionResetError`]({{< ref "/library/exceptions#ConnectionResetError" >}}) 。

## errno.**ENOBUFS**

​	没有可用的缓冲区空间

## errno.**EISCONN**

​	传输端点已连接

## errno.**ENOTCONN**

​	传输端点未连接

## errno.**ESHUTDOWN**

​	在传输端点关闭后无法发送。这个错误被映射到异常 [`BrokenPipeError`]({{< ref "/library/exceptions#BrokenPipeError" >}}) 。

## errno.**ETOOMANYREFS**

​	引用过多：无法拼接

## errno.**ETIMEDOUT**

​	连接超时。这个错误被映射到异常 [`TimeoutError`]({{< ref "/library/exceptions#TimeoutError" >}}) 。

## errno.**ECONNREFUSED**

​	连接被拒绝。这个错误被映射到异常 [`ConnectionRefusedError`]({{< ref "/library/exceptions#ConnectionRefusedError" >}}) 。

## errno.**EHOSTDOWN**

​	主机已关闭

## errno.**EHOSTUNREACH**

​	没有到主机的路由

## errno.**EALREADY**

​	操作已经在进行中。这个错误被映射到异常 [`BlockingIOError`]({{< ref "/library/exceptions#BlockingIOError" >}}) 。

## errno.**EINPROGRESS**

​	操作现在正在进行中。这个错误被映射到异常 [`BlockingIOError`]({{< ref "/library/exceptions#BlockingIOError" >}}) 。

## errno.**ESTALE**

​	过期的 NFS 文件句柄

## errno.**EUCLEAN**

​	结构需要清理

## errno.**ENOTNAM**

​	不是 XENIX 命名类型文件

## errno.**ENAVAIL**

​	没有可用的 XENIX 信标

## errno.**EISNAM**

​	是命名类型文件

## errno.**EREMOTEIO**

​	远程 I/O 错误

## errno.**EDQUOT**

​	超出配额

## errno.**EQFULL**

​	接口输出队列已满

> Added in version 3.11.
>

## errno.**ENOTCAPABLE**

​	功能不足。 此错误被映射到异常 [`PermissionError`]({{< ref "/library/exceptions#PermissionError" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): WASI, FreeBSD

> Added in version 3.11.1.
>

## errno.**ECANCELED**

​	操作已被取消

> Added in version 3.2.
>

## errno.**EOWNERDEAD**

​	所有者已不存在

> Added in version 3.2.
>

## errno.**ENOTRECOVERABLE**

​	状态无法恢复

> Added in version 3.2.
>
