+++
title = "socket --- 低层级的网络接口"
date = 2024-11-15T12:30:27+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/socket.html](https://docs.python.org/zh-cn/3.13/library/socket.html)
>
> 收录该文档的时间：`2024-11-15T12:30:27+08:00`

# `socket` --- 低层级的网络接口

**源代码:** [Lib/socket.py](https://github.com/python/cpython/tree/3.13/Lib/socket.py)

------

​	这个模块提供了访问 BSD *套接字* 的接口。在所有现代 Unix 系统、Windows、macOS 和其他一些平台上可用。

​备注
 

​	一些行为可能因平台不同而异，因为调用的是操作系统的套接字API。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

​	此模块在 WebAssembly 平台上无效或不可用。 请参阅 [WebAssembly 平台]({{< ref "/library/intro#wasm-availability" >}}) 了解详情。

​	这个 Python 接口是将 Unix 系统调用和套接字库接口直接转写为 Python 的面向对象风格：函数 [`socket()`]({{< ref "/library/ipc/socket#socket.socket" >}}) 返回一个 *套接字对象*，其方法是对各种套接字系统调用的实现。 形参类型相比 C 接口更高级一些：如同在 Python 文件上的 `read()` 和 `write()` 操作那样，接受操作的缓冲区分配是自动进行的，发送操作的缓冲区长度则是隐式的。

​参见
模块 [`socketserver`]({{< ref "/library/internet/socketserver#module-socketserver" >}})

​	用于简化网络服务端编写的类。

模块 [`ssl`]({{< ref "/library/ipc/ssl#module-ssl" >}})

​	套接字对象的TLS/SSL封装。

## 套接字协议族

​	根据系统以及构建选项，此模块提供了各种套接字协议簇。

​	特定的套接字对象需要的地址格式将根据此套接字对象被创建时指定的地址族被自动选择。套接字地址表示如下：

- 一个绑定在文件系统节点上的 [`AF_UNIX`]({{< ref "/library/ipc/socket#socket.AF_UNIX" >}}) 套接字的地址表示为一个字符串，使用文件系统字符编码和 `'surrogateescape'` 错误回调方法（see [**PEP 383**](https://peps.python.org/pep-0383/)）。一个地址在 Linux 的抽象命名空间被返回为带有初始的 null 字节的 [字节类对象]({{< ref "/glossary/idx#term-bytes-like-object" >}}) ；注意在这个命名空间种的套接字可能与普通文件系统套接字通信，所以打算运行在 Linux 上的程序可能需要解决两种地址类型。当传递为参数时，一个字符串或字节类对象可以用于任一类型的地址。

  > 在 3.3 版本发生变更: 之前，[`AF_UNIX`]({{< ref "/library/ipc/socket#socket.AF_UNIX" >}}) 套接字路径被假设使用 UTF-8 编码。

  > 在 3.5 版本发生变更: 现在接受可写的 [字节类对象]({{< ref "/glossary/idx#term-bytes-like-object" >}})。

- 一对 `(host, port)` 被用作 [`AF_INET`]({{< ref "/library/ipc/socket#socket.AF_INET" >}}) 地址族，其中 *host* 是一个表示互联网域名标记形式的主机名例如 `'daring.cwi.nl'` 或者 IPv4 地址例如 `'100.50.200.5'` 的字符串，而 *port* 是一个整数值。

  - 对于 IPv4 地址，有两种可接受的特殊形式被用来代替一个主机地址： `''` 代表 `INADDR_ANY`，用来绑定到所有接口；字符串 `'<broadcast>'` 代表 `INADDR_BROADCAST`。此行为不兼容 IPv6，因此，如果你的 Python 程序打算支持 IPv6，则可能需要避开这些。

- 对于 [`AF_INET6`]({{< ref "/library/ipc/socket#socket.AF_INET6" >}}) 地址族，使用一个四元组 `(host, port, flowinfo, scope_id)`，其中 *flowinfo* 和 *scope_id* 代表了 C 库 `struct sockaddr_in6` 中的 `sin6_flowinfo` 和 `sin6_scope_id` 成员。对于 [`socket`]({{< ref "/library/ipc/socket#module-socket" >}}) 模块中的方法， *flowinfo* 和 *scope_id* 可以被省略，只为了向后兼容。注意，省略 *scope_id* 可能会导致操作带有领域 (Scope) 的 IPv6 地址时出错。

  > 在 3.7 版本发生变更: 对于多播地址（其 *scope_id* 起作用），*地址* 中可以不包含 `%scope_id` （或 `zone id` ）部分，这部分是多余的，可以放心省略（推荐）。

- `AF_NETLINK` 套接字由一对 `(pid, groups)` 表示。

- 指定 `AF_TIPC` 地址族可以使用仅 Linux 支持的 TIPC 协议。TIPC 是一种开放的、非基于 IP 的网络协议，旨在用于集群计算环境。其地址用元组表示，其中的字段取决于地址类型。一般元组形式为 `(addr_type, v1, v2, v3 [, scope])`，其中：

  - *addr_type* 取 `TIPC_ADDR_NAMESEQ`、`TIPC_ADDR_NAME` 或 `TIPC_ADDR_ID` 中的一个。

  - *scope* 取 `TIPC_ZONE_SCOPE`、`TIPC_CLUSTER_SCOPE` 和 `TIPC_NODE_SCOPE` 中的一个。

  - 如果 *addr_type* 为 `TIPC_ADDR_NAME`，那么 *v1* 是服务器类型，*v2* 是端口标识符，*v3* 应为 0。

    如果 *addr_type* 为 `TIPC_ADDR_NAMESEQ`，那么 *v1* 是服务器类型，*v2* 是端口号下限，而 *v3* 是端口号上限。

    如果 *addr_type* 为 `TIPC_ADDR_ID`，那么 *v1* 是节点 (node)，*v2* 是 ref，*v3* 应为 0。

- [`AF_CAN`]({{< ref "/library/ipc/socket#socket.AF_CAN" >}}) 地址族使用元组 `(interface, )`，其中 *interface* 是表示网络接口名称的字符串，如 `'can0'`。网络接口名 `''` 可以用于接收本族所有网络接口的数据包。

  - [`CAN_ISOTP`]({{< ref "/library/ipc/socket#socket.CAN_ISOTP" >}}) 协议接受一个元组 `(interface, rx_addr, tx_addr)`，其中两个额外参数都是无符号长整数，都表示 CAN 标识符（标准或扩展标识符）。
  - [`CAN_J1939`]({{< ref "/library/ipc/socket#socket.CAN_J1939" >}}) 协议接受一个元组 `(interface, name, pgn, addr)`，其中额外参数有：表示 ECU 名称的 64 位无符号整数，表示参数组号 (Parameter Group Number, PGN) 的 32 位无符号整数，以及表示地址的 8 位整数。

- `PF_SYSTEM` 协议族的 `SYSPROTO_CONTROL` 协议使用一个字符串或元组 `(id, unit)`。 这个字符串是使用动态分配 ID 的内核控件名称。 如果 ID 和内核控件的单元编号都已知或者使用了已注册的 ID 则可以使用元组。

  *Added in version 3.3.*

- `AF_BLUETOOTH` 支持以下协议和地址格式：

  - `BTPROTO_L2CAP` 接受 `(bdaddr, psm)`，其中 `bdaddr` 为字符串格式的蓝牙地址，`psm` 是一个整数。

  - `BTPROTO_RFCOMM` 接受 `(bdaddr, channel)`，其中 `bdaddr` 为字符串格式的蓝牙地址，`channel` 是一个整数。

  - `BTPROTO_HCI` 接受 `(device_id,)`，其中 `device_id` 为整数或字符串，它表示接口对应的蓝牙地址（具体取决于你的系统，NetBSD 和 DragonFlyBSD 需要蓝牙地址字符串，其他系统需要整数）。

    > 在 3.2 版本发生变更: 添加了对 NetBSD 和 DragonFlyBSD 的支持。

  - `BTPROTO_SCO` 接受 `bdaddr`，其中 `bdaddr` 是 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 对象，其中含有字符串格式的蓝牙地址（如 `b'12:23:34:45:56:67'` ），FreeBSD 不支持此协议。

- [`AF_ALG`]({{< ref "/library/ipc/socket#socket.AF_ALG" >}}) 是一个仅 Linux 可用的、基于套接字的接口，用于连接内核加密算法。算法套接字可用包括 2 至 4 个元素的元组来配置 `(type, name [, feat [, mask]])`，其中：

  - *type* 是表示算法类型的字符串，如 `aead`、`hash`、`skcipher` 或 `rng`。
  - *name* 是表示算法类型和操作模式的字符串，如 `sha256`、`hmac(sha256)`、`cbc(aes)` 或 `drbg_nopr_ctr_aes256`。
  - *feat* 和 *mask* 是无符号 32 位整数。

  [Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.38.

  ​	某些算法类型需要更新的内核。

  *Added in version 3.6.*

- [`AF_VSOCK`]({{< ref "/library/ipc/socket#socket.AF_VSOCK" >}}) 用于支持虚拟机与宿主机之间的通讯。该套接字用 `(CID, port)` 元组表示，其中 Context ID (CID) 和 port 都是整数。

  [Availability]({{< ref "/library/intro#availability" >}}): Linux >= 3.9

  ​	参见 *[vsock(7)](https://manpages.debian.org/vsock(7))*

  *Added in version 3.7.*

- [`AF_PACKET`]({{< ref "/library/ipc/socket#socket.AF_PACKET" >}}) 是一个直接连接网络设备的低层级接口。 地址以元组 `(ifname, proto[, pkttype[, hatype[, addr]]])` 表示，其中：

  - *ifname* - 指定设备名称的字符串。
  - *proto* - 以太网协议号。 可以为 [`ETH_P_ALL`]({{< ref "/library/ipc/socket#socket.ETH_P_ALL" >}}) 表示捕获所有协议，某个 [ETHERTYPE_* 常量]({{< ref "/library/ipc/socket#socket-ethernet-types" >}}) 或者任何其他以太网协议号。
  - *pkttype* - 指定数据包类型的整数（可选）：
    - `PACKET_HOST` （默认） - 寻址到本地主机的数据包。
    - `PACKET_BROADCAST` - 物理层广播的数据包。
    - `PACKET_MULTICAST` - 发送到物理层多播地址的数据包。
    - `PACKET_OTHERHOST` - 被（处于混杂模式的）网卡驱动捕获的、发送到其他主机的数据包。
    - `PACKET_OUTGOING` - 来自本地主机的、回环到一个套接字的数据包。
  - *hatype* - 可选整数，指定 ARP 硬件地址类型。
  - *addr* - 可选的类字节串对象，用于指定硬件物理地址，其解释取决于各设备。

  [Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.2.

- [`AF_QIPCRTR`]({{< ref "/library/ipc/socket#socket.AF_QIPCRTR" >}}) 是一个仅 Linux 可用的、基于套接字的接口，用于与高通平台中协处理器上运行的服务进行通信。该地址簇用一个 `(node, port)` 元组表示，其中 *node* 和 *port* 为非负整数。

  [Availability]({{< ref "/library/intro#availability" >}}): Linux >= 4.7.

  *Added in version 3.8.*

- `IPPROTO_UDPLITE` 是一种 UDP 的变体，允许指定数据包的哪一部分计算入校验码内。它添加了两个可以修改的套接字选项。`self.setsockopt(IPPROTO_UDPLITE, UDPLITE_SEND_CSCOV, length)` 修改传出数据包的哪一部分计算入校验码内，而 `self.setsockopt(IPPROTO_UDPLITE, UDPLITE_RECV_CSCOV, length)` 将过滤掉计算入校验码的数据太少的数据包。在这两种情况下，`length` 都应在 `range(8, 2**16, 8)` 范围内。

  对于 IPv4，应使用 `socket(AF_INET, SOCK_DGRAM, IPPROTO_UDPLITE)` 来构造这样的套接字；对于 IPv6，应使用 `socket(AF_INET6, SOCK_DGRAM, IPPROTO_UDPLITE)` 来构造这样的套接字。

  [Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.20, FreeBSD >= 10.1

  *Added in version 3.9.*

- [`AF_HYPERV`]({{< ref "/library/ipc/socket#socket.AF_HYPERV" >}}) 是 Windows 专属的用于同 Hyper-V 主机和客户机通信的基于套接字的接口。 其地址族以一个 `(vm_id, service_id)` 元组表示，其中 `vm_id` 和 `service_id` 均为 UUID 字符串。

  `vm_id` 为虚拟机标识号或者如果目标不是一台特定的虚拟机则为已知 VMID 值的集合。 在 `socket` 上定义的已知 VMID 常量有:

  - `HV_GUID_ZERO`
  - `HV_GUID_BROADCAST`
  - `HV_GUID_WILDCARD` - 用于绑定自身并接受来自所有分区的连接。
  - `HV_GUID_CHILDREN` - 用于绑定自身并接受来自子分区的连接。
  - `HV_GUID_LOOPBACK` - 用作指向自身的目标。
  - `HV_GUID_PARENT` - 当用作绑定时接受来自父分区的连接。 当用作地址目标时它将连接到父分区。will connect to the parent partition.

  `service_id` 是已注册服务的服务标识号。

  *Added in version 3.12.*

​	如果你在 IPv4/v6 套接字地址的 *host* 部分中使用了一个主机名，此程序可能会表现不确定行为，因为 Python 使用 DNS 解析返回的第一个地址。套接字地址在实际的 IPv4/v6 中以不同方式解析，根据 DNS 解析和/或 host 配置。为了确定行为，在 *host* 部分中使用数字的地址。

​	所有错误都会引发异常。 普通异常将针对无效的参数类型和内存不足等情况被引发。 与套接字或地址语义有关的错误则会引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 或它的某个子类。

​	可以用 [`setblocking()`]({{< ref "/library/ipc/socket#socket.socket.setblocking" >}}) 设置非阻塞模式。一个基于超时的 generalization 通过 [`settimeout()`]({{< ref "/library/ipc/socket#socket.socket.settimeout" >}}) 支持。

## 模块内容

[`socket`]({{< ref "/library/ipc/socket#module-socket" >}}) 模块包含下列元素。

### 异常

## *exception* socket.**error**

​	一个被弃用的 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 的别名。

> 在 3.3 版本发生变更: 根据 [**PEP 3151**](https://peps.python.org/pep-3151/)，这个类是 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 的别名。

## *exception* socket.**herror**

[`OSError`]({{< ref "/library/exceptions#OSError" >}}) 的子类，本异常通常表示与地址相关的错误，比如那些在 POSIX C API 中使用了 *h_errno* 的函数，包括 [`gethostbyname_ex()`]({{< ref "/library/ipc/socket#socket.gethostbyname_ex" >}}) 和 [`gethostbyaddr()`]({{< ref "/library/ipc/socket#socket.gethostbyaddr" >}})。附带的值是一对 `(h_errno, string)`，代表库调用返回的错误。*h_errno* 是一个数字，而 *string* 表示 *h_errno* 的描述，它们由 C 函数 `hstrerror()` 返回。

> 在 3.3 版本发生变更: 此类是 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 的子类。

## *exception* socket.**gaierror**

[`OSError`]({{< ref "/library/exceptions#OSError" >}}) 的子类，该异常由 [`getaddrinfo()`]({{< ref "/library/ipc/socket#socket.getaddrinfo" >}}) 和 [`getnameinfo()`]({{< ref "/library/ipc/socket#socket.getnameinfo" >}}) 引发以表示与地址相关的错误。 附带的值是一个 `(error, string)` 对，代表库调用所返回的错误。 *string* 代表 *error* 的描述，如 `gai_strerror()` C 函数所返回的值。 数字值 *error* 将与本模块中定义的某个 `EAI_*` 常量相匹配。

> 在 3.3 版本发生变更: 此类是 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 的子类。

## *exception* socket.**timeout**

[`TimeoutError`]({{< ref "/library/exceptions#TimeoutError" >}}) 的已被弃用的别名。

[`OSError`]({{< ref "/library/exceptions#OSError" >}}) 的子类，当套接字发生超时，且事先已调用过 [`settimeout()`]({{< ref "/library/ipc/socket#socket.socket.settimeout" >}}) （或隐式地通过 [`setdefaulttimeout()`]({{< ref "/library/ipc/socket#socket.setdefaulttimeout" >}}) ）启用了超时，则会抛出此异常。附带的值是一个字符串，其值总是 "timed out"。

> 在 3.3 版本发生变更: 此类是 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 的子类。

> 在 3.10 版本发生变更: 这个类是 [`TimeoutError`]({{< ref "/library/exceptions#TimeoutError" >}}) 的别名。

### 常量

> ​	AF_* 和 SOCK_* 常量现在都在 `AddressFamily` 和 `SocketKind` 这两个 [`IntEnum`]({{< ref "/library/datatypes/enum#enum.IntEnum" >}}) 集合内。
>
> *Added in version 3.4.*

## socket.**AF_UNIX**

## socket.**AF_INET**

## socket.**AF_INET6**

​	这些常量表示地址（和协议）族，被用作传给 [`socket()`]({{< ref "/library/ipc/socket#socket.socket" >}}) 的第一个参数。 如果 [`AF_UNIX`]({{< ref "/library/ipc/socket#socket.AF_UNIX" >}}) 常量未定义则该协议将不受支持。 根据具体系统可能会有更多的常量可用。

## socket.**AF_UNSPEC**

[`AF_UNSPEC`]({{< ref "/library/ipc/socket#socket.AF_UNSPEC" >}}) 表示 [`getaddrinfo()`]({{< ref "/library/ipc/socket#socket.getaddrinfo" >}}) 应当为任何可被使用的地址族返回套接字地址（无论是 IPv4, IPv6 还是其他）。

## socket.**SOCK_STREAM**

## socket.**SOCK_DGRAM**

## socket.**SOCK_RAW**

## socket.**SOCK_RDM**

## socket.**SOCK_SEQPACKET**

​	这些常量表示套接字类型，被用作传给 [`socket()`]({{< ref "/library/ipc/socket#socket.socket" >}}) 的第二个参数。 根据具体系统可能会有更多的常量可用。 （只有 [`SOCK_STREAM`]({{< ref "/library/ipc/socket#socket.SOCK_STREAM" >}}) 和 [`SOCK_DGRAM`]({{< ref "/library/ipc/socket#socket.SOCK_DGRAM" >}}) 是普遍适用的。）

## socket.**SOCK_CLOEXEC**

## socket.**SOCK_NONBLOCK**

​	这两个常量（如果已定义）可以与上述套接字类型结合使用，允许你设置这些原子性相关的 flags （从而避免可能的竞争条件和单独调用的需要）。

​参见
 

[安全文件描述符处理](https://udrepper.livejournal.com/20407.html) 提供了更详尽的解释。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.27.

> Added in version 3.2.
>

## **SO_\***

## socket.**SOMAXCONN**

## **MSG_\***

## **SOL_\***

## **SCM_\***

## **IPPROTO_\***

## **IPPORT_\***

## **INADDR_\***

## **IP_\***

## **IPV6_\***

## **EAI_\***

## **AI_\***

## **NI_\***

## **TCP_\***

​	许多这样的常量，记录在 Unix 有关套接字和/或 IP 协议的文档中，也在 socket 模块中有定义。 它们通常被用于传给套接字对象的 [`setsockopt()`]({{< ref "/library/ipc/socket#socket.socket.setsockopt" >}}) 和 [`getsockopt()`]({{< ref "/library/ipc/socket#socket.socket.getsockopt" >}}) 等方法的参数中。 在大多数情况下，只有那些在 Unix 头文件中有定义的符号会在本模块中定义；对于部分符号，还提供了默认值。

> 在 3.6 版本发生变更: 添加了 `SO_DOMAIN`, `SO_PROTOCOL`, `SO_PEERSEC`, `SO_PASSSEC`, `TCP_USER_TIMEOUT`, `TCP_CONGESTION`。

*在 3.6.5 版本发生变更:* 在 Windows 上，如果 Windows 运行时支持，则 `TCP_FASTOPEN`、`TCP_KEEPCNT` 可用。

> 在 3.7 版本发生变更: 添加了 `TCP_NOTSENT_LOWAT`。

​	在 Windows 上，如果 Windows 运行时支持，则 `TCP_KEEPIDLE`、`TCP_KEEPINTVL` 可用。

> 在 3.10 版本发生变更: 添加了 `IP_RECVTOS`。 还添加了 `TCP_KEEPALIVE`。 这个常量在 MacOS 上可以与在 Linux 上使用 `TCP_KEEPIDLE` 的相同方式被使用。

> 在 3.11 版本发生变更: 添加了 `TCP_CONNECTION_INFO`。 在 MacOS 上此常量可以与在 Linux 和 BSD 上使用 `TCP_INFO` 的相同方式来使用。

> 在 3.12 版本发生变更: 增加了 `SO_RTABLE` 和 `SO_USER_COOKIE`。 这些常量分别在 OpenBSD 和 FreeBSD 可按与 `SO_MARK` 在 Linux 上相同的方式被使用。 还增加了来自 Linux 的缺失的 TCP 套接字选项: `TCP_MD5SIG`, `TCP_THIN_LINEAR_TIMEOUTS`, `TCP_THIN_DUPACK`, `TCP_REPAIR`, `TCP_REPAIR_QUEUE`, `TCP_QUEUE_SEQ`, `TCP_REPAIR_OPTIONS`, `TCP_TIMESTAMP`, `TCP_CC_INFO`, `TCP_SAVE_SYN`, `TCP_SAVED_SYN`, `TCP_REPAIR_WINDOW`, `TCP_FASTOPEN_CONNECT`, `TCP_ULP`, `TCP_MD5SIG_EXT`, `TCP_FASTOPEN_KEY`, `TCP_FASTOPEN_NO_COOKIE`, `TCP_ZEROCOPY_RECEIVE`, `TCP_INQ`, `TCP_TX_DELAY`。 增加了 `IP_PKTINFO`, `IP_UNBLOCK_SOURCE`, `IP_BLOCK_SOURCE`, `IP_ADD_SOURCE_MEMBERSHIP`, `IP_DROP_SOURCE_MEMBERSHIP`。

> 在 3.13 版本发生变更: 增加了 `SO_BINDTOIFINDEX`。 在 Linux 上此常量可按照与 `SO_BINDTODEVICE` 相同的用法来使用，但是要通过网络接口的索引号而不是其名称。

## socket.**AF_CAN**

## socket.**PF_CAN**

## **SOL_CAN_\***

## **CAN_\***

​	此列表内的许多常量，记载在 Linux 文档中，同时也定义在本 socket 模块中。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.25, NetBSD >= 8.

> Added in version 3.3.
>

> 在 3.11 版本发生变更: 添加了 NetBSD 支持。

## socket.**CAN_BCM**

## **CAN_BCM_\***

​	CAN 协议簇内的 CAN_BCM 是广播管理器（Bbroadcast Manager -- BCM）协议，广播管理器常量在 Linux 文档中有所记载，在本 socket 模块中也有定义。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.25.

​备注
 

`CAN_BCM_CAN_FD_FRAME` 旗标仅在 Linux >= 4.8 时可用。

> Added in version 3.4.
>

## socket.**CAN_RAW_FD_FRAMES**

​	在 CAN_RAW 套接字中启用 CAN FD 支持，默认是禁用的。它使应用程序可以发送 CAN 和 CAN FD 帧。但是，从套接字读取时，也必须同时接受 CAN 和 CAN FD 帧。

​	此常量在 Linux 文档中有所记载。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 3.6.

> Added in version 3.5.
>

## socket.**CAN_RAW_JOIN_FILTERS**

​	加入已应用的 CAN 过滤器，这样只有与所有 CAN 过滤器匹配的 CAN 帧才能传递到用户空间。

​	此常量在 Linux 文档中有所记载。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 4.1.

> Added in version 3.9.
>

## socket.**CAN_ISOTP**

​	CAN 协议簇中的 CAN_ISOTP 就是 ISO-TP (ISO 15765-2) 协议。ISO-TP 常量在 Linux 文档中有所记载。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.25.

> Added in version 3.7.
>

## socket.**CAN_J1939**

​	CAN 协议族中的 CAN_J1939 即 SAE J1939 协议。 J1939 常量记录在 Linux 文档中。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 5.4.

> Added in version 3.9.
>

## socket.**AF_DIVERT**

## socket.**PF_DIVERT**

​	这两个常量，记录在 FreeBSD divert(4) 手册页中，同样已在 socket 模块中定义。

[Availability]({{< ref "/library/intro#availability" >}}): FreeBSD >= 14.0.

> Added in version 3.12.
>

## socket.**AF_PACKET**

## socket.**PF_PACKET**

## **PACKET_\***

​	此列表内的许多常量，记载在 Linux 文档中，同时也定义在本 socket 模块中。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.2.

## socket.**ETH_P_ALL**

`ETH_P_ALL` 可在 [`socket`]({{< ref "/library/ipc/socket#socket.socket" >}}) 构造器中用作 [`AF_PACKET`]({{< ref "/library/ipc/socket#socket.AF_PACKET" >}}) 族的 *proto* 以便捕获每个包，无论是使用什么协议。

​	要了解详情，请参阅 *[packet(7)](https://manpages.debian.org/packet(7))* 手册页。

[Availability]({{< ref "/library/intro#availability" >}}): Linux.

> Added in version 3.12.
>

## socket.**AF_RDS**

## socket.**PF_RDS**

## socket.**SOL_RDS**

## **RDS_\***

​	此列表内的许多常量，记载在 Linux 文档中，同时也定义在本 socket 模块中。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.30.

> Added in version 3.3.
>

## socket.**SIO_RCVALL**

## socket.**SIO_KEEPALIVE_VALS**

## socket.**SIO_LOOPBACK_FAST_PATH**

## **RCVALL_\***

​	Windows 的 WSAIoctl() 的常量。这些常量用于套接字对象的 [`ioctl()`]({{< ref "/library/ipc/socket#socket.socket.ioctl" >}}) 方法的参数。

> 在 3.6 版本发生变更: 添加了 `SIO_LOOPBACK_FAST_PATH`。

## **TIPC_\***

​	TIPC 相关常量，与 C socket API 导出的常量一致。更多信息请参阅 TIPC 文档。

## socket.**AF_ALG**

## socket.**SOL_ALG**

## **ALG_\***

​	用于 Linux 内核加密算法的常量。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.38.

> Added in version 3.6.
>

## socket.**AF_VSOCK**

## socket.**IOCTL_VM_SOCKETS_GET_LOCAL_CID**

## **VMADDR\***

## **SO_VM\***

​	用于 Linux 宿主机/虚拟机通讯的常量。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 4.8.

> Added in version 3.7.
>

## socket.**AF_LINK**

[Availability]({{< ref "/library/intro#availability" >}}): BSD, macOS.

> Added in version 3.4.
>

## socket.**has_ipv6**

​	本常量为一个布尔值，该值指示当前平台是否支持 IPv6。

## socket.**BDADDR_ANY**

## socket.**BDADDR_LOCAL**

​	这些是字符串常量，包含蓝牙地址，这些地址具有特殊含义。例如，当用 `BTPROTO_RFCOMM` 指定绑定套接字时， [`BDADDR_ANY`]({{< ref "/library/ipc/socket#socket.BDADDR_ANY" >}}) 表示“任何地址”。

## socket.**HCI_FILTER**

## socket.**HCI_TIME_STAMP**

## socket.**HCI_DATA_DIR**

​	与 `BTPROTO_HCI` 一起使用。 [`HCI_FILTER`]({{< ref "/library/ipc/socket#socket.HCI_FILTER" >}}) 在 NetBSD 或 DragonFlyBSD 上不可用。 [`HCI_TIME_STAMP`]({{< ref "/library/ipc/socket#socket.HCI_TIME_STAMP" >}}) 和 [`HCI_DATA_DIR`]({{< ref "/library/ipc/socket#socket.HCI_DATA_DIR" >}}) 在 FreeBSD、NetBSD 或 DragonFlyBSD 上不可用。

## socket.**AF_QIPCRTR**

​	高通 IPC 路由协议的常数，用于与提供远程处理器的服务进行通信。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 4.7.

## socket.**SCM_CREDS2**

## socket.**LOCAL_CREDS**

## socket.**LOCAL_CREDS_PERSISTENT**

​	LOCAL_CREDS 和 LOCAL_CREDS_PERSISTENT 可与 SOCK_DGRAM, SOCK_STREAM 套接字一起使用，等价于 Linux/DragonFlyBSD SO_PASSCRED，其中 LOCAL_CREDS 会在首次读取时发送凭证，LOCAL_CREDS_PERSISTENT 会在每次读取时发送，随后必须为后者使用 SCM_CREDS2 作为消息类型。

> Added in version 3.11.
>

[Availability]({{< ref "/library/intro#availability" >}}): FreeBSD.

## socket.**SO_INCOMING_CPU**

> ​	用于优化 CPU 定位的常量，应与 `SO_REUSEPORT` 配合使用。

> Added in version 3.11.
>

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 3.9

## socket.**AF_HYPERV**

## socket.**HV_PROTOCOL_RAW**

## socket.**HVSOCKET_CONNECT_TIMEOUT**

## socket.**HVSOCKET_CONNECT_TIMEOUT_MAX**

## socket.**HVSOCKET_CONNECTED_SUSPEND**

## socket.**HVSOCKET_ADDRESS_FLAG_PASSTHRU**

## socket.**HV_GUID_ZERO**

## socket.**HV_GUID_WILDCARD**

## socket.**HV_GUID_BROADCAST**

## socket.**HV_GUID_CHILDREN**

## socket.**HV_GUID_LOOPBACK**

## socket.**HV_GUID_PARENT**

​	用于 Windows Hyper-V 宿主机/客户机通信的套接字的常量。

[Availability]({{< ref "/library/intro#availability" >}}): Windows.

> Added in version 3.12.
>

## socket.**ETHERTYPE_ARP**

## socket.**ETHERTYPE_IP**

## socket.**ETHERTYPE_IPV6**

## socket.**ETHERTYPE_VLAN**

[IEEE 802.3 协议号](https://www.iana.org/assignments/ieee-802-numbers/ieee-802-numbers.txt) 常量。

[Availability]({{< ref "/library/intro#availability" >}}): Linux, FreeBSD, macOS.

> Added in version 3.12.
>

## socket.**SHUT_RD**

## socket.**SHUT_WR**

## socket.**SHUT_RDWR**

​	这些常量将由套接字对象的 [`shutdown()`]({{< ref "/library/ipc/socket#socket.socket.shutdown" >}}) 方法使用。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

### 函数

#### 创建套接字

​	下列函数都能创建 [套接字对象]({{< ref "/library/ipc/socket#socket-objects" >}}).

## *class* socket.**socket**(*family=AF_INET*, *type=SOCK_STREAM*, *proto=0*, *fileno=None*)

​	使用给定的地址族、套接字类型和协议号创建一个新的套接字。 地址族应为 [`AF_INET`]({{< ref "/library/ipc/socket#socket.AF_INET" >}}) (默认值), [`AF_INET6`]({{< ref "/library/ipc/socket#socket.AF_INET6" >}}), [`AF_UNIX`]({{< ref "/library/ipc/socket#socket.AF_UNIX" >}}), [`AF_CAN`]({{< ref "/library/ipc/socket#socket.AF_CAN" >}}), [`AF_PACKET`]({{< ref "/library/ipc/socket#socket.AF_PACKET" >}}) 或 [`AF_RDS`]({{< ref "/library/ipc/socket#socket.AF_RDS" >}}) 之一。 套接字类型应为 [`SOCK_STREAM`]({{< ref "/library/ipc/socket#socket.SOCK_STREAM" >}}) (默认值), [`SOCK_DGRAM`]({{< ref "/library/ipc/socket#socket.SOCK_DGRAM" >}}), [`SOCK_RAW`]({{< ref "/library/ipc/socket#socket.SOCK_RAW" >}}) 或其他可能的 `SOCK_` 常量之一。 协议号通常为零并且可以省略，或在协议族为 [`AF_CAN`]({{< ref "/library/ipc/socket#socket.AF_CAN" >}}) 的情况下，协议应为 `CAN_RAW`, [`CAN_BCM`]({{< ref "/library/ipc/socket#socket.CAN_BCM" >}}), [`CAN_ISOTP`]({{< ref "/library/ipc/socket#socket.CAN_ISOTP" >}}) 或 [`CAN_J1939`]({{< ref "/library/ipc/socket#socket.CAN_J1939" >}}) 之一。

​	如果指定了 *fileno*，那么将从指定的文件描述符中自动检测 *family*, *type* 和 *proto* 的值。 自动检测可被调用此函数时显式传入的 *family*, *type* 或 *proto* 参数所覆盖。 这只会影响 Python 表示诸如 [`socket.getpeername()`]({{< ref "/library/ipc/socket#socket.socket.getpeername" >}}) 函数的返回值的方式而不会影响实际的 OS 资源。 与 [`socket.fromfd()`]({{< ref "/library/ipc/socket#socket.fromfd" >}}) 不同，*fileno* 将返回同样的套接字而不是其副本。 这将有助于使用 [`socket.close()`]({{< ref "/library/ipc/socket#socket.close" >}}) 来关闭已分离的套接字。

​	新创建的套接字是 [不可继承的]({{< ref "/library/allos/os#fd-inheritance" >}})。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `socket.__new__` 并附带参数 `self`, `family`, `type`, `protocol`。

> 在 3.3 版本发生变更: 添加了 AF_CAN 簇。添加了 AF_RDS 簇。

> 在 3.4 版本发生变更: 添加了 CAN_BCM 协议。

> 在 3.4 版本发生变更: 返回的套接字现在是不可继承的。

> 在 3.7 版本发生变更: 添加了 CAN_ISOTP 协议。

> 在 3.7 版本发生变更: 当将 [`SOCK_NONBLOCK`]({{< ref "/library/ipc/socket#socket.SOCK_NONBLOCK" >}}) 或 [`SOCK_CLOEXEC`]({{< ref "/library/ipc/socket#socket.SOCK_CLOEXEC" >}}) 旗标位应用于 *type* 时它们将被清除，且 [`socket.type`]({{< ref "/library/ipc/socket#socket.socket.type" >}}) 将不会反映它们。 它们仍然会被传递给底层的系统 `socket()` 调用。 因而，

```
sock = socket.socket(
    socket.AF_INET,
    socket.SOCK_STREAM | socket.SOCK_NONBLOCK)
```

​	仍将在支持 `SOCK_NONBLOCK` 的系统上创建一个非阻塞的套接字，但是 `sock.type` 会被置为 `socket.SOCK_STREAM`。

> 在 3.9 版本发生变更: 添加了 CAN_J1939 协议。

> 在 3.10 版本发生变更: 添加了 IPPROTO_MPTCP 协议。

## socket.**socketpair**([*family*[, *type*[, *proto*]]])

​	使用给定的地址族、套接字类型和协议号构建一对已连接的套接字对象。 地址族、套接字类型和协议号与上述 [`socket()`]({{< ref "/library/ipc/socket#socket.socket" >}}) 函数中的相同。 默认地址族为定义于平台中的 [`AF_UNIX`]({{< ref "/library/ipc/socket#socket.AF_UNIX" >}})；如未定义，则默认为 [`AF_INET`]({{< ref "/library/ipc/socket#socket.AF_INET" >}})。

​	新创建的套接字都是 [不可继承的]({{< ref "/library/allos/os#fd-inheritance" >}})。

> 在 3.2 版本发生变更: 现在，返回的套接字对象支持全部套接字 API，而不是全部 API 的一个子集。

> 在 3.4 版本发生变更: 返回的套接字现在都是不可继承的。

> 在 3.5 版本发生变更: 添加了 Windows 支持。

## socket.**create_connection**(*address*, *timeout=GLOBAL_DEFAULT*, *source_address=None*, ***, *all_errors=False*)

​	连接到一个在互联网 *address* (以 `(host, port)` 2 元组表示) 上侦听的 TCP 服务，并返回套接字对象。 这是一个相比 [`socket.connect()`]({{< ref "/library/ipc/socket#socket.socket.connect" >}}) 层级更高的函数：如果 *host* 是非数字的主机名，它将尝试将其解析为 [`AF_INET`]({{< ref "/library/ipc/socket#socket.AF_INET" >}}) 和 [`AF_INET6`]({{< ref "/library/ipc/socket#socket.AF_INET6" >}})，然后依次尝试连接到所有可能的地址直到连接成功。 这使编写兼容 IPv4 和 IPv6 的客户端变得很容易。

​	传入可选参数 *timeout* 可以在套接字实例上设置超时（在尝试连接前）。如果未提供 *timeout*，则使用由 [`getdefaulttimeout()`]({{< ref "/library/ipc/socket#socket.getdefaulttimeout" >}}) 返回的全局默认超时设置。

​	如果提供了 *source_address*，它必须为二元组 `(host, port)`，以便套接字在连接之前绑定为其源地址。如果 host 或 port 分别为 '' 或 0，则使用操作系统默认行为。

​	当无法创建连接时，将会引发一个异常。 在默认情况下，它将是来自列表中最后一个地址的异常。 如果 *all_errors* 为 `True`，它将是一个包含所有尝试错误的 [`ExceptionGroup`]({{< ref "/library/exceptions#ExceptionGroup" >}})。

> 在 3.2 版本发生变更: 添加了*source_address* 参数

> 在 3.11 版本发生变更: 添加了 *all_errors*。

## socket.**create_server**(*address*, ***, *family=AF_INET*, *backlog=None*, *reuse_port=False*, *dualstack_ipv6=False*)

​	创建绑定到 *address* 的 TCP 套接字（一个 `(host, port)` 2 元组）并返回该套接字对象的便捷函数。

*family* 应当为 [`AF_INET`]({{< ref "/library/ipc/socket#socket.AF_INET" >}}) 或 [`AF_INET6`]({{< ref "/library/ipc/socket#socket.AF_INET6" >}})。 *backlog* 是传递给 [`socket.listen()`]({{< ref "/library/ipc/socket#socket.socket.listen" >}}) 的队列大小；当未指定时，将选择一个合理的默认值。 *reuse_port* 指定是否要设置 `SO_REUSEPORT` 套接字选项。

​	如果 *dualstack_ipv6* 为 true 且平台支持，则套接字能接受 IPv4 和 IPv6 连接，否则将抛出 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 异常。大多数 POSIX 平台和 Windows 应该支持此功能。启用此功能后，[`socket.getpeername()`]({{< ref "/library/ipc/socket#socket.socket.getpeername" >}}) 在进行 IPv4 连接时返回的地址将是一个（映射到 IPv4 的）IPv6 地址。在默认启用该功能的平台上（如 Linux），如果 *dualstack_ipv6* 为 false，即显式禁用此功能。该参数可以与 [`has_dualstack_ipv6()`]({{< ref "/library/ipc/socket#socket.has_dualstack_ipv6" >}}) 结合使用：

```
import socket

addr = ("", 8080)  # 所有接口，端口 8080
if socket.has_dualstack_ipv6():
    s = socket.create_server(addr, family=socket.AF_INET6, dualstack_ipv6=True)
else:
    s = socket.create_server(addr)
```

​备注
 

​	在 POSIX 平台上，设置 `SO_REUSEADDR` 套接字选项是为了立即重用以前绑定在同一 *address* 上并保持 TIME_WAIT 状态的套接字。

> Added in version 3.8.
>

## socket.**has_dualstack_ipv6**()

​	如果平台支持创建 IPv4 和 IPv6 连接都可以处理的 TCP 套接字，则返回 `True`。

> Added in version 3.8.
>

## socket.**fromfd**(*fd*, *family*, *type*, *proto=0*)

​	复制文件描述符 *fd* (由文件对象的 [`fileno()`]({{< ref "/library/allos/io#io.IOBase.fileno" >}}) 方法返回的整数) 并根据结果构建一个套接字对象。 地址族、套接字类型和协议号与上述 [`socket()`]({{< ref "/library/ipc/socket#socket.socket" >}}) 函数中的相同。 文件描述符应指向一个套接字，但不会检查这一点 --- 如果文件描述符是无效的则对该对象的后续操作可能会失败。 本函数很少被用到，但在将套接字作为标准输入或输出传给给程序 (如 Unix inet 进程启动的服务器) 时可以用来获取或设置套接字选项。 套接字将被假定为阻塞模式。

​	新创建的套接字是 [不可继承的]({{< ref "/library/allos/os#fd-inheritance" >}})。

> 在 3.4 版本发生变更: 返回的套接字现在是不可继承的。

## socket.**fromshare**(*data*)

​	根据 [`socket.share()`]({{< ref "/library/ipc/socket#socket.socket.share" >}}) 方法获得的数据实例化套接字。套接字将处于阻塞模式。

[Availability]({{< ref "/library/intro#availability" >}}): Windows.

> Added in version 3.3.
>

## socket.**SocketType**

​	这是一个 Python 类型对象，表示套接字对象的类型。它等同于 `type(socket(...))`。

#### 其他功能

[`socket`]({{< ref "/library/ipc/socket#module-socket" >}}) 模块还提供多种网络相关服务：

## socket.**close**(*fd*)

​	关闭一个套接字文件描述符。它类似于 [`os.close()`]({{< ref "/library/allos/os#os.close" >}})，但专用于套接字。在某些平台上（特别是在 Windows 上），[`os.close()`]({{< ref "/library/allos/os#os.close" >}}) 对套接字文件描述符无效。

> Added in version 3.7.
>

## socket.**getaddrinfo**(*host*, *port*, *family=0*, *type=0*, *proto=0*, *flags=0*)

​	将 *host*/*port* 参数转换为 5 元组的序列，其中包含创建（连接到某服务的）套接字所需的所有参数。*host* 是域名，是字符串格式的 IPv4/v6 地址或 `None`。*port* 是字符串格式的服务名称，如 `'http'` 、端口号（数字）或 `None`。传入 `None` 作为 *host* 和 *port* 的值，相当于将 `NULL` 传递给底层 C API。

​	可以指定 *family*、*type* 和 *proto* 参数，以缩小返回的地址列表。向这些参数分别传入 0 表示保留全部结果范围。*flags* 参数可以是 `AI_*` 常量中的一个或多个，它会影响结果的计算和返回。例如，`AI_NUMERICHOST` 会禁用域名解析，此时如果 *host* 是域名，则会抛出错误。

​	本函数返回一个列表，其中的 5 元组具有以下结构：

```
(family, type, proto, canonname, sockaddr)
```

​	在这些元组中，*family*, *type*, *proto* 都是整数且其作用是被传给 [`socket()`]({{< ref "/library/ipc/socket#socket.socket" >}}) 函数。 如果 `AI_CANONNAME` 是 *flags* 参数的一部分则 *canonname* 将为表示 *host* 的规范名称的字符串；否则 *canonname* 将为空。 *sockaddr* 是一个描述套接字地址的元组，其具体格式取决于返回的 *family* (对于 [`AF_INET`]({{< ref "/library/ipc/socket#socket.AF_INET" >}}) 将为 `(address, port)` 2 元组，对于 [`AF_INET6`]({{< ref "/library/ipc/socket#socket.AF_INET6" >}}) 将为 `(address, port, flowinfo, scope_id)` 4 元组)，其作用是被传给 [`socket.connect()`]({{< ref "/library/ipc/socket#socket.socket.connect" >}}) 方法。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `socket.getaddrinfo` 并附带参数 `host`, `port`, `family`, `type`, `protocol`。

​	下面的示例获取了 TCP 连接地址信息，假设该连接通过 80 端口连接至 `example.org` （如果系统未启用 IPv6，则结果可能会不同）:



``` python
>>> socket.getaddrinfo("example.org", 80, proto=socket.IPPROTO_TCP)
[(socket.AF_INET6, socket.SOCK_STREAM,
 6, '', ('2606:2800:220:1:248:1893:25c8:1946', 80, 0, 0)),
 (socket.AF_INET, socket.SOCK_STREAM,
 6, '', ('93.184.216.34', 80))]
```

> 在 3.2 版本发生变更: 现在可以使用关键字参数的形式来传递参数。

> 在 3.7 版本发生变更: 对于 IPv6 多播地址，表示地址的字符串将不包含 `%scope_id` 部分。

## socket.**getfqdn**([*name*])

​	返回 *name* 的完整限定域名。 如果 *name* 被省略或为空，则将其解读为本地主机。 要查找完整限定名称，将先检查 [`gethostbyaddr()`]({{< ref "/library/ipc/socket#socket.gethostbyaddr" >}}) 所返回的主机名，然后是主机的别名（如果存在）。 包括句点的第一个名称将会被选择。 对于没有完整限定域名而提供了 *name* 的情况，则会将其原样返回。 如果 *name* 为空或等于 `'0.0.0.0'`，则返回来自 [`gethostname()`]({{< ref "/library/ipc/socket#socket.gethostname" >}}) 的主机名。

## socket.**gethostbyname**(*hostname*)

​	将主机名转换为 IPv4 地址格式。IPv4 地址以字符串格式返回，如 `'100.50.200.5'`。如果主机名本身是 IPv4 地址，则原样返回。更完整的接口请参考 [`gethostbyname_ex()`]({{< ref "/library/ipc/socket#socket.gethostbyname_ex" >}})。 [`gethostbyname()`]({{< ref "/library/ipc/socket#socket.gethostbyname" >}}) 不支持 IPv6 名称解析，应使用 [`getaddrinfo()`]({{< ref "/library/ipc/socket#socket.getaddrinfo" >}}) 来支持 IPv4/v6 双协议栈。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `socket.gethostbyname` 并附带参数 `hostname`。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

## socket.**gethostbyname_ex**(*hostname*)

​	将一个主机名转换为 IPv4 地址格式的扩展接口。 返回一个 3 元组 `(hostname, aliaslist, ipaddrlist)` 其中 *hostname* 是主机的首选主机名，*aliaslist* 是同一地址的备选主机名列表（可能为空），而 *ipaddrlist* 是同一主机上同一接口的 IPv4 地址列表（通常为单个地址但并不总是如此）。 [`gethostbyname_ex()`]({{< ref "/library/ipc/socket#socket.gethostbyname_ex" >}}) 不支持 IPv6 名称解析，应当改用 [`getaddrinfo()`]({{< ref "/library/ipc/socket#socket.getaddrinfo" >}}) 来提供 IPv4/v6 双栈支持。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `socket.gethostbyname` 并附带参数 `hostname`。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

## socket.**gethostname**()

​	返回一个字符串，包含当前正在运行 Python 解释器的机器的主机名。

​	引发一个不带参数的 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `socket.gethostname`。

​	注意： [`gethostname()`]({{< ref "/library/ipc/socket#socket.gethostname" >}}) 并不总是返回全限定域名，必要的话请使用 [`getfqdn()`]({{< ref "/library/ipc/socket#socket.getfqdn" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

## socket.**gethostbyaddr**(*ip_address*)

​	返回一个 3 元组 `(hostname, aliaslist, ipaddrlist)` 其中 *hostname* 是响应给定 *ip_address* 的首选主机名，*aliaslist* 是同一地址的备选主机名列表（可能为空），而 *ipaddrlist* 是同一主机上同一接口的 IPv4/v6 地址列表（很可能仅包含一个地址）。 要查询完整限定域名，请使用函数 [`getfqdn()`]({{< ref "/library/ipc/socket#socket.getfqdn" >}})。 [`gethostbyaddr()`]({{< ref "/library/ipc/socket#socket.gethostbyaddr" >}}) 同时支持 IPv4 和 IPv6。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `socket.gethostbyaddr` 并附带参数 `ip_address`。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

## socket.**getnameinfo**(*sockaddr*, *flags*)

​	将套接字地址 *sockaddr* 转换为一个 2 元组 `(host, port)`。 根据 *flags* 的设置，结果可能包含 *host* 中的完整限定域名或数字形式的地址。 类似地，*port* 可以包含字符串形式的端口名或数字形式的端口号。

​	对于 IPv6 地址，如果 *sockaddr* 包含有意义的 *scope_id*，则 `%scope_id` 会被附加到主机部分。 这种情况通常发生在多播地址上。

​	关于 *flags* 的更多信息可参阅 *[getnameinfo(3)](https://manpages.debian.org/getnameinfo(3))*。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `socket.getnameinfo` 并附带参数 `sockaddr`。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

## socket.**getprotobyname**(*protocolname*)

​	将一个互联网协议名称 (如 `'icmp'`) 转写为能被作为 (可选的) 第三个参数传给 [`socket()`]({{< ref "/library/ipc/socket#socket.socket" >}}) 函数的常量。 这通常仅对以 "raw" 模式 ([`SOCK_RAW`]({{< ref "/library/ipc/socket#socket.SOCK_RAW" >}})) 打开的套接字来说是必要的；对于正常的套接字模式，当协议名称被省略或为零时会自动选择正确的协议。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

## socket.**getservbyname**(*servicename*[, *protocolname*])

​	将一个互联网服务名称和协议名称转换为该服务的端口号。 如果给出了可选的协议名称，它应为 `'tcp'` 或 `'udp'`，否则将匹配任意的协议。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `socket.getservbyname` 并附带参数 `servicename`, `protocolname`。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

## socket.**getservbyport**(*port*[, *protocolname*])

​	将一个互联网端口号和协议名称转换为该服务的服务名称。 如果给出了可选的协议名称，它应为 `'tcp'` 或 `'udp'`，否则将匹配任意的协议。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `socket.getservbyport` 并附带参数 `port`, `protocolname`。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

## socket.**ntohl**(*x*)

​	将 32 位正整数从网络字节序转换为主机字节序。在主机字节序与网络字节序相同的计算机上，这是一个空操作。字节序不同将执行 4 字节交换操作。

## socket.**ntohs**(*x*)

​	将 16 位正整数从网络字节序转换为主机字节序。在主机字节序与网络字节序相同的计算机上，这是一个空操作。字节序不同将执行 2 字节交换操作。

> 在 3.10 版本发生变更: 如果 *x* 不能转为 16 位无符号整数则会引发 [`OverflowError`]({{< ref "/library/exceptions#OverflowError" >}})。

## socket.**htonl**(*x*)

​	将 32 位正整数从主机字节序转换为网络字节序。在主机字节序与网络字节序相同的计算机上，这是一个空操作。字节序不同将执行 4 字节交换操作。

## socket.**htons**(*x*)

​	将 16 位正整数从主机字节序转换为网络字节序。在主机字节序与网络字节序相同的计算机上，这是一个空操作。字节序不同将执行 2 字节交换操作。

> 在 3.10 版本发生变更: 如果 *x* 不能转为 16 位无符号整数则会引发 [`OverflowError`]({{< ref "/library/exceptions#OverflowError" >}})。

## socket.**inet_aton**(*ip_string*)

​	将一个 IPv4 地址从以点号分为四段的字符串格式（例如 '123.45.67.89'）转换为 32 位的紧凑二进制格式，长度为四个字符的字节串对象。 这在与使用标准 C 库并且需要 `in_addr` 类型对象的程序通信时很有用处，该类型就是此函数所返回的 32 位的紧凑二进制格式 C 类型。

[`inet_aton()`]({{< ref "/library/ipc/socket#socket.inet_aton" >}}) 也接受句点数少于三的字符串，详情请参阅 Unix 手册 *[inet(3)](https://manpages.debian.org/inet(3))*。

​	如果传入本函数的 IPv4 地址字符串无效，则抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}})。注意，具体什么样的地址有效取决于 `inet_aton()` 的底层 C 实现。

[`inet_aton()`]({{< ref "/library/ipc/socket#socket.inet_aton" >}}) 不支持 IPv6，在 IPv4/v6 双协议栈下应使用 [`inet_pton()`]({{< ref "/library/ipc/socket#socket.inet_pton" >}}) 来代替。

## socket.**inet_ntoa**(*packed_ip*)

​	将一个 32 位紧凑 IPv4 地址 (长度为四个字节的 [bytes-like object]({{< ref "/glossary/idx#term-bytes-like-object" >}})) 转换为标准的以点号四分段字符串表示形式 (例如 '123.45.67.89')。 这在与使用标准 C 库并且需要 `in_addr` 类型对象的程序通信时很有用处，该类型就是此函数接受作为参数的 32 位的紧凑二进制格式 C 类型。

​	如果传入本函数的字节序列长度不是 4 个字节，则抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}})。[`inet_ntoa()`]({{< ref "/library/ipc/socket#socket.inet_ntoa" >}}) 不支持 IPv6，在 IPv4/v6 双协议栈下应使用 [`inet_ntop()`]({{< ref "/library/ipc/socket#socket.inet_ntop" >}}) 来代替。

> 在 3.5 版本发生变更: 现在接受可写的 [字节类对象]({{< ref "/glossary/idx#term-bytes-like-object" >}})。

## socket.**inet_pton**(*address_family*, *ip_string*)

​	将基于特定地址族字符串格式的 IP 地址转换为紧凑的二进制格式。 [`inet_pton()`]({{< ref "/library/ipc/socket#socket.inet_pton" >}}) 在一个库或网络协议需要 `in_addr` (类似于 [`inet_aton()`]({{< ref "/library/ipc/socket#socket.inet_aton" >}})) 或 `in6_addr` 类型的对象时很有用处。

​	目前 *address_family* 支持 [`AF_INET`]({{< ref "/library/ipc/socket#socket.AF_INET" >}}) 和 [`AF_INET6`]({{< ref "/library/ipc/socket#socket.AF_INET6" >}})。如果 IP 地址字符串 *ip_string* 无效，则抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}})。注意，具体什么地址有效取决于 *address_family* 的值和 `inet_pton()` 的底层实现。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows.

> 在 3.4 版本发生变更: 添加了 Windows 支持

## socket.**inet_ntop**(*address_family*, *packed_ip*)

​	将一个紧凑的 IP 地址 (长度为多个字节的 [bytes-like object]({{< ref "/glossary/idx#term-bytes-like-object" >}})) 转换为标准的基于特定地址族的字符串表示形式 (例如 `'7.10.0.5'` 或 `'5aef:2b::8'`)。 [`inet_ntop()`]({{< ref "/library/ipc/socket#socket.inet_ntop" >}}) 在一个库或网络协议返回 `in_addr` (类似于 [`inet_ntoa()`]({{< ref "/library/ipc/socket#socket.inet_ntoa" >}})) 或 `in6_addr` 类型的对象时很有用处。

​	目前 *address_family* 支持 [`AF_INET`]({{< ref "/library/ipc/socket#socket.AF_INET" >}}) 和 [`AF_INET6`]({{< ref "/library/ipc/socket#socket.AF_INET6" >}})。如果字节对象 *packed_ip* 与指定的地址簇长度不符，则抛出 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。针对 [`inet_ntop()`]({{< ref "/library/ipc/socket#socket.inet_ntop" >}}) 调用的错误则抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows.

> 在 3.4 版本发生变更: 添加了 Windows 支持

> 在 3.5 版本发生变更: 现在接受可写的 [字节类对象]({{< ref "/glossary/idx#term-bytes-like-object" >}})。

## socket.**CMSG_LEN**(*length*)

​	返回给定 *length* 所关联数据的辅助数据项的总长度（不带尾部填充）。此值通常用作 [`recvmsg()`]({{< ref "/library/ipc/socket#socket.socket.recvmsg" >}}) 接收一个辅助数据项的缓冲区大小，但是 [**RFC 3542**](https://datatracker.ietf.org/doc/html/rfc3542.html) 要求可移植应用程序使用 [`CMSG_SPACE()`]({{< ref "/library/ipc/socket#socket.CMSG_SPACE" >}})，以此将尾部填充的空间计入，即使该项在缓冲区的最后。如果 *length* 超出允许范围，则抛出 [`OverflowError`]({{< ref "/library/exceptions#OverflowError" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

​	大多数 Unix 平台。

> Added in version 3.3.
>

## socket.**CMSG_SPACE**(*length*)

​	返回 [`recvmsg()`]({{< ref "/library/ipc/socket#socket.socket.recvmsg" >}}) 所需的缓冲区大小，以接收给定 *length* 所关联数据的辅助数据项，带有尾部填充。接收多个项目所需的缓冲区空间是关联数据长度的 [`CMSG_SPACE()`]({{< ref "/library/ipc/socket#socket.CMSG_SPACE" >}}) 值的总和。如果 *length* 超出允许范围，则抛出 [`OverflowError`]({{< ref "/library/exceptions#OverflowError" >}})。

​	请注意，某些系统可能支持辅助数据，但不提供本函数。还需注意，如果使用本函数的结果来设置缓冲区大小，可能无法精确限制可接收的辅助数据量，因为可能会有其他数据写入尾部填充区域。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

​	大多数 Unix 平台。

> Added in version 3.3.
>

## socket.**getdefaulttimeout**()

​	返回用于新套接字对象的默认超时（以秒为单位的浮点数）。值 `None` 表示新套接字对象没有超时。首次导入 socket 模块时，默认值为 `None`。

## socket.**setdefaulttimeout**(*timeout*)

​	设置用于新套接字对象的默认超时（以秒为单位的浮点数）。首次导入 socket 模块时，默认值为 `None`。可能的取值及其各自的含义请参阅 [`settimeout()`]({{< ref "/library/ipc/socket#socket.socket.settimeout" >}})。

## socket.**sethostname**(*name*)

​	将计算机的主机名设置为 *name*。如果权限不足将抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}})。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `socket.sethostname` 并附带参数 `name`。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not Android.

> Added in version 3.3.
>

## socket.**if_nameindex**()

​	返回一个列表，包含网络接口（网卡）信息二元组（整数索引，名称字符串）。系统调用失败则抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows, not WASI.

> Added in version 3.3.
>

> 在 3.8 版本发生变更: 添加了 Windows 支持。

​备注
 

​	在 Windows 中网络接口在不同上下文中具有不同的名称（所有名称见对应示例）:

- UUID: `{FB605B73-AAC2-49A6-9A2F-25416AEA0573}`
- 名称: `ethernet_32770`
- 友好名称: `vEthernet (nat)`
- 描述: `Hyper-V Virtual Ethernet Adapter`

​	此函数返回列表中第二种形式的名称，在此示例中为 `ethernet_32770`。

## socket.**if_nametoindex**(*if_name*)

​	返回网络接口名称相对应的索引号。如果没有所给名称的接口，则抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows, not WASI.

> Added in version 3.3.
>

> 在 3.8 版本发生变更: 添加了 Windows 支持。

​参见
 

​	"Interface name" 为 [`if_nameindex()`]({{< ref "/library/ipc/socket#socket.if_nameindex" >}}) 中所描述的名称。

## socket.**if_indextoname**(*if_index*)

​	返回网络接口索引号相对应的接口名称。如果没有所给索引号的接口，则抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows, not WASI.

> Added in version 3.3.
>

> 在 3.8 版本发生变更: 添加了 Windows 支持。

​参见
 

​	"Interface name" 为 [`if_nameindex()`]({{< ref "/library/ipc/socket#socket.if_nameindex" >}}) 中所描述的名称。

## socket.**send_fds**(*sock*, *buffers*, *fds*[, *flags*[, *address*]])

​	将文件描述符列表 *fds* 通过一个 [`AF_UNIX`]({{< ref "/library/ipc/socket#socket.AF_UNIX" >}}) 套接字 *sock* 进行发送。 *fds* 形参是由文件描述符组成的序列。 请查看 [`sendmsg()`]({{< ref "/library/ipc/socket#socket.socket.sendmsg" >}}) 获取这些形参的文档说明。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows, not WASI.

​	支持 [`sendmsg()`]({{< ref "/library/ipc/socket#socket.socket.sendmsg" >}}) 和 `SCM_RIGHTS` 机制的 Unix 平台。

> Added in version 3.9.
>

## socket.**recv_fds**(*sock*, *bufsize*, *maxfds*[, *flags*])

​	接收至多 *maxfds* 个来自 [`AF_UNIX`]({{< ref "/library/ipc/socket#socket.AF_UNIX" >}}) 套接字 *sock* 的文件描述符。 返回 `(msg, list(fds), flags, addr)`。 请查看 [`recvmsg()`]({{< ref "/library/ipc/socket#socket.socket.recvmsg" >}}) 获取这些形参的文档说明。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, Windows, not WASI.

​	支持 [`sendmsg()`]({{< ref "/library/ipc/socket#socket.socket.sendmsg" >}}) 和 `SCM_RIGHTS` 机制的 Unix 平台。

> Added in version 3.9.
>

​备注
 

​	位于文件描述符列表末尾的任何被截断整数。



## 套接字对象

​	套接字对象具有以下方法。除了 [`makefile()`]({{< ref "/library/ipc/socket#socket.socket.makefile" >}})，其他都与套接字专用的 Unix 系统调用相对应。

> 在 3.2 版本发生变更: 添加了对 [上下文管理器]({{< ref "/glossary/idx#term-context-manager" >}}) 协议的支持。退出上下文管理器与调用 [`close()`]({{< ref "/library/ipc/socket#socket.close" >}}) 等效。

## socket.**accept**()

​	接受一个连接。此 socket 必须绑定到一个地址上并且监听连接。返回值是一个 `(conn, address)` 对，其中 *conn* 是一个 *新* 的套接字对象，用于在此连接上收发数据，*address* 是连接另一端的套接字所绑定的地址。

​	新创建的套接字是 [不可继承的]({{< ref "/library/allos/os#fd-inheritance" >}})。

> 在 3.4 版本发生变更: 该套接字现在是不可继承的。

> 在 3.5 版本发生变更: 如果系统调用被中断，但信号处理程序没有触发异常，此方法现在会重试系统调用，而不是触发 [`InterruptedError`]({{< ref "/library/exceptions#InterruptedError" >}}) 异常 (原因详见 [**PEP 475**](https://peps.python.org/pep-0475/))。

## socket.**bind**(*address*)

​	将套接字绑定到 *address*。套接字必须尚未绑定。（ *address* 的格式取决于地址簇 —— 参见上文）

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `socket.bind` 并附带参数 `self`、`address`。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

## socket.**close**()

​	将套接字标记为已关闭。 底层的系统资源（例如文件描述符）也将在 [`makefile()`]({{< ref "/library/ipc/socket#socket.socket.makefile" >}}) 创建的所有文件对象关闭时被关闭。 一旦上述情况发生，将来对该套接字对象的所有操作都将失败。 远端将不会接收到新的数据（在队列中的数据被清空之后）。

​	垃圾回收时，套接字会自动关闭，但建议显式 [`close()`]({{< ref "/library/ipc/socket#socket.close" >}}) 它们，或在它们周围使用 [`with`]({{< ref "/reference/compound_stmts#with" >}}) 语句。

> 在 3.6 版本发生变更: 现在，如果底层的 `close()` 调用出错，会抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}})。

​备注
 

[`close()`]({{< ref "/library/ipc/socket#socket.close" >}}) 会释放与连接相关联的资源但不一定立即关闭连接。 如果你想要及时关闭连接，请在 [`close()`]({{< ref "/library/ipc/socket#socket.close" >}}) 之前调用 [`shutdown()`]({{< ref "/library/ipc/socket#socket.socket.shutdown" >}})。

## socket.**connect**(*address*)

​	连接到 *address* 处的远程套接字。（ *address* 的格式取决于地址簇 —— 参见上文）

​	如果连接被信号中断，则本方法将等待直至连接完成，或者如果信号处理器未引发异常并且套接字被阻塞或已超时则会在超时后引发 [`TimeoutError`]({{< ref "/library/exceptions#TimeoutError" >}})。 对于非阻塞型套接字，如果连接被信号中断则本方法将引发 [`InterruptedError`]({{< ref "/library/exceptions#InterruptedError" >}}) 异常（或信号处理器所引发的异常）。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `socket.connect` 并附带参数 `self`、`address`。

> 在 3.5 版本发生变更: 本方法现在将等待，直到连接完成，而不是在以下情况抛出 [`InterruptedError`]({{< ref "/library/exceptions#InterruptedError" >}}) 异常。该情况为，连接被信号中断，信号处理程序未抛出异常，且套接字阻塞中或已超时（具体解释请参阅 [**PEP 475**](https://peps.python.org/pep-0475/) ）。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

## socket.**connect_ex**(*address*)

​	类似于 `connect(address)`，但是对于 C 级别的 `connect()` 调用返回的错误，本函数将返回错误指示器，而不是抛出异常（对于其他问题，如“找不到主机”，仍然可以抛出异常）。如果操作成功，则错误指示器为 `0`，否则为 `errno` 变量的值。这对支持如异步连接很有用。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `socket.connect` 并附带参数 `self`、`address`。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

## socket.**detach**()

​	将套接字对象置于关闭状态，而底层的文件描述符实际并不关闭。返回该文件描述符，使其可以重新用于其他目的。

> Added in version 3.2.
>

## socket.**dup**()

​	创建套接字的副本。

​	新创建的套接字是 [不可继承的]({{< ref "/library/allos/os#fd-inheritance" >}})。

> 在 3.4 版本发生变更: 该套接字现在是不可继承的。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

## socket.**fileno**()

​	返回套接字的文件描述符（一个小整数），失败返回 -1。配合 [`select.select()`]({{< ref "/library/ipc/select#select.select" >}}) 使用很有用。

​	在 Windows 下，此方法返回的小整数在允许使用文件描述符的地方无法使用（如 [`os.fdopen()`]({{< ref "/library/allos/os#os.fdopen" >}}) ）。Unix 无此限制。

## socket.**get_inheritable**()

​	获取套接字文件描述符或套接字句柄的 [可继承标志]({{< ref "/library/allos/os#fd-inheritance" >}}) ：如果子进程可以继承套接字则为 `True`，否则为 `False`。

> Added in version 3.4.
>

## socket.**getpeername**()

​	返回套接字连接到的远程地址。举例而言，这可以用于查找远程 IPv4/v6 套接字的端口号。（返回的地址格式取决于地址簇 —— 参见上文。）部分系统不支持此函数。

## socket.**getsockname**()

​	返回套接字本身的地址。举例而言，这可以用于查找 IPv4/v6 套接字的端口号。（返回的地址格式取决于地址簇 —— 参见上文。）

## socket.**getsockopt**(*level*, *optname*[, *buflen*])

​	返回给定套接字选项的值 (参见 Unix 手册页 *[getsockopt(2)](https://manpages.debian.org/getsockopt(2))*)。 所需的符号常量 ([SO_* 等]({{< ref "/library/ipc/socket#socket-unix-constants" >}})) 在本模块中定义。 如果未指定 *buflen*，则会假定该选项为整数值并且将由此函数返回其整数值。 如果指定了 *buflen*，则它定义了用于存放选项值的缓冲区的最大长度，且该缓冲区将作为字节对象返回。 调用方需要执行对缓冲区内容的解码（请参阅可选的内置模块 [`struct`]({{< ref "/library/binary/struct#module-struct" >}}) 了解如何对编码为字节串的 C 结构体进行解码）。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

## socket.**getblocking**()

​	如果套接字处于阻塞模式，返回 `True`，非阻塞模式返回 `False`。

​	这等价于检测 `socket.gettimeout() != 0`。

> Added in version 3.7.
>

## socket.**gettimeout**()

​	返回套接字操作相关的超时秒数（浮点数），未设置超时则返回 `None`。它反映最后一次调用 [`setblocking()`]({{< ref "/library/ipc/socket#socket.socket.setblocking" >}}) 或 [`settimeout()`]({{< ref "/library/ipc/socket#socket.socket.settimeout" >}}) 后的设置。

## socket.**ioctl**(*control*, *option*)

## 平台:

​	Windows

[`ioctl()`]({{< ref "/library/ipc/socket#socket.socket.ioctl" >}}) 方法是 WSAIoctl 系统接口的有限接口。请参考 [Win32 文档](https://msdn.microsoft.com/en-us/library/ms741621(VS.85).aspx) 以获取更多信息。

​	在其他平台上，可以使用通用的 [`fcntl.fcntl()`]({{< ref "/library/unix/fcntl#fcntl.fcntl" >}}) 和 [`fcntl.ioctl()`]({{< ref "/library/unix/fcntl#fcntl.ioctl" >}}) 函数，它们接受套接字对象作为第一个参数。

​	当前仅支持以下控制码： `SIO_RCVALL`、`SIO_KEEPALIVE_VALS` 和 `SIO_LOOPBACK_FAST_PATH`。

> 在 3.6 版本发生变更: 添加了 `SIO_LOOPBACK_FAST_PATH`。

## socket.**listen**([*backlog*])

​	启动一个服务器用于接受连接。如果指定 *backlog*，则它最低为 0（小于 0 会被置为 0），它指定系统允许暂未 accept 的连接数，超过后将拒绝新连接。未指定则自动设为合理的默认值。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

> 在 3.5 版本发生变更: *backlog* 参数现在是可选的。

## socket.**makefile**(*mode='r'*, *buffering=None*, ***, *encoding=None*, *errors=None*, *newline=None*)

​	返回一个与套接字相关联的 [file object]({{< ref "/glossary/idx#term-file-object" >}})。 返回对象的具体类型取决于传给 [`makefile()`]({{< ref "/library/ipc/socket#socket.socket.makefile" >}}) 的参数。 这些参数的解读方式与内置的 [`open()`]({{< ref "/library/functions#open" >}}) 函数相同，区别在于 *mode* 值仅支持 `'r'` (默认), `'w'`, `'b'` 或它们的组合。

​	套接字必须处于阻塞模式，它可以有超时，但是如果发生超时，文件对象的内部缓冲区可能会以不一致的状态结尾。

​	关闭 [`makefile()`]({{< ref "/library/ipc/socket#socket.socket.makefile" >}}) 返回的文件对象不会关闭原始套接字，除非所有其他文件对象都已关闭且在套接字对象上调用了 [`socket.close()`]({{< ref "/library/ipc/socket#socket.close" >}})。

​备注
 

​	在 Windows 上，由 [`makefile()`]({{< ref "/library/ipc/socket#socket.socket.makefile" >}}) 创建的文件型对象无法作为带文件描述符的文件对象使用，如无法作为 [`subprocess.Popen()`]({{< ref "/library/concurrency/subprocess#subprocess.Popen" >}}) 的流参数。

## socket.**recv**(*bufsize*[, *flags*])

​	从套接字接收数据。 返回值是一个代表所接收数据的字节串对象。 可一次性接收的最大数据量由 *bufsize* 指定。 返回空字节串对象表示客户端已断开连接。 请参阅 Unix 手册页 *[recv(2)](https://manpages.debian.org/recv(2))* 了解可选参数 *flags* 的含义；它默认为零。

​备注
 

​	为了最佳匹配硬件和网络的实际情况，*bufsize* 的值应为 2 的相对较小的幂，如 4096。

> 在 3.5 版本发生变更: 如果系统调用被中断，但信号处理程序没有触发异常，此方法现在会重试系统调用，而不是触发 [`InterruptedError`]({{< ref "/library/exceptions#InterruptedError" >}}) 异常 (原因详见 [**PEP 475**](https://peps.python.org/pep-0475/))。

## socket.**recvfrom**(*bufsize*[, *flags*])

​	从套接字接收数据。返回值是一对 `(bytes, address)`，其中 *bytes* 是字节对象，表示接收到的数据，*address* 是发送端套接字的地址。可选参数 *flags* 的含义请参阅 Unix 手册页 *[recv(2)](https://manpages.debian.org/recv(2))*，它默认为零。（ *address* 的格式取决于地址簇 —— 参见上文）

> 在 3.5 版本发生变更: 如果系统调用被中断，但信号处理程序没有触发异常，此方法现在会重试系统调用，而不是触发 [`InterruptedError`]({{< ref "/library/exceptions#InterruptedError" >}}) 异常 (原因详见 [**PEP 475**](https://peps.python.org/pep-0475/))。

> 在 3.7 版本发生变更: 对于多播 IPv6 地址，*address* 的第一项不会再包含 `%scope_id` 部分。 要获得完整的 IPv6 地址请使用 [`getnameinfo()`]({{< ref "/library/ipc/socket#socket.getnameinfo" >}})。

## socket.**recvmsg**(*bufsize*[, *ancbufsize*[, *flags*]])

​	从套接字接收普通数据（至多 *bufsize* 字节）和辅助数据。*ancbufsize* 参数设置用于接收辅助数据的内部缓冲区的大小（以字节为单位），默认为 0，表示不接收辅助数据。可以使用 [`CMSG_SPACE()`]({{< ref "/library/ipc/socket#socket.CMSG_SPACE" >}}) 或 [`CMSG_LEN()`]({{< ref "/library/ipc/socket#socket.CMSG_LEN" >}}) 计算辅助数据缓冲区的合适大小，无法放入缓冲区的项目可能会被截断或丢弃。*flags* 参数默认为 0，其含义与 [`recv()`]({{< ref "/library/ipc/socket#socket.socket.recv" >}}) 中的相同。

​	返回值是一个四元组： `(data, ancdata, msg_flags, address)`。*data* 项是一个 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 对象，用于保存接收到的非辅助数据。*ancdata* 项是零个或多个元组 `(cmsg_level, cmsg_type, cmsg_data)` 组成的列表，表示接收到的辅助数据（控制消息）：*cmsg_level* 和 *cmsg_type* 是分别表示协议级别和协议类型的整数，而 *cmsg_data* 是保存相关数据的 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 对象。*msg_flags* 项由各种标志按位或组成，表示接收消息的情况，详细信息请参阅系统文档。如果接收端套接字断开连接，则 *address* 是发送端套接字的地址（如果有），否则该值无指定。

​	在某些系统上，可以使用 [`sendmsg()`]({{< ref "/library/ipc/socket#socket.socket.sendmsg" >}}) 和 [`recvmsg()`]({{< ref "/library/ipc/socket#socket.socket.recvmsg" >}}) 通过 [`AF_UNIX`]({{< ref "/library/ipc/socket#socket.AF_UNIX" >}}) 套接字在进程之间传递文件描述符。 当使用此功能时 (通常仅限于 [`SOCK_STREAM`]({{< ref "/library/ipc/socket#socket.SOCK_STREAM" >}}) 套接字), [`recvmsg()`]({{< ref "/library/ipc/socket#socket.socket.recvmsg" >}}) 将在其附带数据中返回 `(socket.SOL_SOCKET, socket.SCM_RIGHTS, fds)` 形式的项，其中 *fds* 是一个代表新文件描述符的原生as a binary array of the native C int 类型的二进制数组形式的 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 对象。 如果 [`recvmsg()`]({{< ref "/library/ipc/socket#socket.socket.recvmsg" >}}) 在系统调用返回后引发了异常，它将首先尝试关闭通过此机制接收到的任何文件描述符。

​	对于仅接收到一部分的辅助数据项，一些系统没有指示其截断长度。如果某个项目可能超出了缓冲区的末尾，[`recvmsg()`]({{< ref "/library/ipc/socket#socket.socket.recvmsg" >}}) 将发出 [`RuntimeWarning`]({{< ref "/library/exceptions#RuntimeWarning" >}})，并返回其在缓冲区内的部分，前提是该对象被截断于关联数据开始后。

​	在支持 `SCM_RIGHTS` 机制的系统上，下方的函数将最多接收 *maxfds* 个文件描述符，返回消息数据和包含描述符的列表（同时忽略意外情况，如接收到无关的控制消息）。另请参阅 [`sendmsg()`]({{< ref "/library/ipc/socket#socket.socket.sendmsg" >}})。

```
import socket, array

def recv_fds(sock, msglen, maxfds):
    fds = array.array("i")   # 整数数组
    msg, ancdata, flags, addr = sock.recvmsg(msglen, socket.CMSG_LEN(maxfds * fds.itemsize))
    for cmsg_level, cmsg_type, cmsg_data in ancdata:
        if cmsg_level == socket.SOL_SOCKET and cmsg_type == socket.SCM_RIGHTS:
            # 添加数据，忽略任何在末尾被截断的整数。
            fds.frombytes(cmsg_data[:len(cmsg_data) - (len(cmsg_data) % fds.itemsize)])
    return msg, list(fds)
```

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

​	大多数 Unix 平台。

> Added in version 3.3.
>

> 在 3.5 版本发生变更: 如果系统调用被中断，但信号处理程序没有触发异常，此方法现在会重试系统调用，而不是触发 [`InterruptedError`]({{< ref "/library/exceptions#InterruptedError" >}}) 异常 (原因详见 [**PEP 475**](https://peps.python.org/pep-0475/))。

## socket.**recvmsg_into**(*buffers*[, *ancbufsize*[, *flags*]])

​	从套接字接收普通数据和辅助数据，其行为与 [`recvmsg()`]({{< ref "/library/ipc/socket#socket.socket.recvmsg" >}}) 相同，但将非辅助数据分散到一系列缓冲区中，而不是返回新的字节对象。*buffers* 参数必须是可迭代对象，它迭代出可供写入的缓冲区（如 [`bytearray`]({{< ref "/library/stdtypes#bytearray" >}}) 对象），这些缓冲区将被连续的非辅助数据块填充，直到数据全部写完或缓冲区用完为止。在允许使用的缓冲区数量上，操作系统可能会有限制（ [`sysconf()`]({{< ref "/library/allos/os#os.sysconf" >}}) 的 `SC_IOV_MAX` 值）。*ancbufsize* 和 *flags* 参数的含义与 [`recvmsg()`]({{< ref "/library/ipc/socket#socket.socket.recvmsg" >}}) 中的相同。

​	返回值为四元组： `(nbytes, ancdata, msg_flags, address)`，其中 *nbytes* 是写入缓冲区的非辅助数据的字节总数，而 *ancdata*、*msg_flags* 和 *address* 与 [`recvmsg()`]({{< ref "/library/ipc/socket#socket.socket.recvmsg" >}}) 中的相同。

​	示例:



``` python
>>> import socket
>>> s1, s2 = socket.socketpair()
>>> b1 = bytearray(b'----')
>>> b2 = bytearray(b'0123456789')
>>> b3 = bytearray(b'--------------')
>>> s1.send(b'Mary had a little lamb')
22
>>> s2.recvmsg_into([b1, memoryview(b2)[2:9], b3])
(22, [], 0, None)
>>> [b1, b2, b3]
[bytearray(b'Mary'), bytearray(b'01 had a 9'), bytearray(b'little lamb---')]
```

[Availability]({{< ref "/library/intro#availability" >}}): Unix.

​	大多数 Unix 平台。

> Added in version 3.3.
>

## socket.**recvfrom_into**(*buffer*[, *nbytes*[, *flags*]])

​	从套接字接收数据，将其写入 *buffer* 而不是创建新的字节串。返回值是一对 `(nbytes, address)`，其中 *nbytes* 是收到的字节数，*address* 是发送端套接字的地址。可选参数 *flags* 的含义请参阅 Unix 手册页 *[recv(2)](https://manpages.debian.org/recv(2))*，它默认为零。（ *address* 的格式取决于地址簇 —— 参见上文）

## socket.**recv_into**(*buffer*[, *nbytes*[, *flags*]])

​	从套接字接收至多 *nbytes* 个字节，将其写入缓冲区而不是创建新的字节串。如果 *nbytes* 未指定（或指定为 0），则接收至所给缓冲区的最大可用大小。返回接收到的字节数。可选参数 *flags* 的含义请参阅 Unix 手册页 *[recv(2)](https://manpages.debian.org/recv(2))*，它默认为零。

## socket.**send**(*bytes*[, *flags*])

​	发送数据给套接字。本套接字必须已连接到远程套接字。可选参数 *flags* 的含义与上述 [`recv()`]({{< ref "/library/ipc/socket#socket.socket.recv" >}}) 中的相同。本方法返回已发送的字节数。应用程序要负责检查所有数据是否已发送，如果仅传输了部分数据，程序需要自行尝试传输其余数据。有关该主题的更多信息，请参考 [套接字编程指南]({{< ref "/howto/sockets#socket-howto" >}})。

> 在 3.5 版本发生变更: 如果系统调用被中断，但信号处理程序没有触发异常，此方法现在会重试系统调用，而不是触发 [`InterruptedError`]({{< ref "/library/exceptions#InterruptedError" >}}) 异常 (原因详见 [**PEP 475**](https://peps.python.org/pep-0475/))。

## socket.**sendall**(*bytes*[, *flags*])

​	发送数据给套接字。本套接字必须已连接到远程套接字。可选参数 *flags* 的含义与上述 [`recv()`]({{< ref "/library/ipc/socket#socket.socket.recv" >}}) 中的相同。与 [`send()`]({{< ref "/library/ipc/socket#socket.socket.send" >}}) 不同，本方法持续从 *bytes* 发送数据，直到所有数据都已发送或发生错误为止。成功后会返回 `None`。出错后会抛出一个异常，此时并没有办法确定成功发送了多少数据。

> 在 3.5 版本发生变更: 每次成员发送数据后，套接字超时将不再重置。 目前的套接字超时是发送所有数据的最大总持续时间。

> 在 3.5 版本发生变更: 如果系统调用被中断，但信号处理程序没有触发异常，此方法现在会重试系统调用，而不是触发 [`InterruptedError`]({{< ref "/library/exceptions#InterruptedError" >}}) 异常 (原因详见 [**PEP 475**](https://peps.python.org/pep-0475/))。

## socket.**sendto**(*bytes*, *address*)

## socket.**sendto**(*bytes*, *flags*, *address*)

​	发送数据给套接字。本套接字不应连接到远程套接字，而应由 *address* 指定目标套接字。可选参数 *flags* 的含义与上述 [`recv()`]({{< ref "/library/ipc/socket#socket.socket.recv" >}}) 中的相同。本方法返回已发送的字节数。（ *address* 的格式取决于地址簇 —— 参见上文。）

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `socket.sendto` 并附带参数 `self`, `address`。

> 在 3.5 版本发生变更: 如果系统调用被中断，但信号处理程序没有触发异常，此方法现在会重试系统调用，而不是触发 [`InterruptedError`]({{< ref "/library/exceptions#InterruptedError" >}}) 异常 (原因详见 [**PEP 475**](https://peps.python.org/pep-0475/))。

## socket.**sendmsg**(*buffers*[, *ancdata*[, *flags*[, *address*]]])

​	将普通数据和辅助数据发送给套接字，将从一系列缓冲区中收集非辅助数据，并将其拼接为一条消息。*buffers* 参数指定的非辅助数据应为可迭代的 [字节类对象]({{< ref "/glossary/idx#term-bytes-like-object" >}}) （如 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 对象），在允许使用的缓冲区数量上，操作系统可能会有限制（ [`sysconf()`]({{< ref "/library/allos/os#os.sysconf" >}}) 的 `SC_IOV_MAX` 值）。*ancdata* 参数指定的辅助数据（控制消息）应为可迭代对象，迭代出零个或多个 `(cmsg_level, cmsg_type, cmsg_data)` 元组，其中 *cmsg_level* 和 *cmsg_type* 是分别指定协议级别和协议类型的整数，而 *cmsg_data* 是保存相关数据的字节类对象。请注意，某些系统（特别是没有 [`CMSG_SPACE()`]({{< ref "/library/ipc/socket#socket.CMSG_SPACE" >}}) 的系统）可能每次调用仅支持发送一条控制消息。*flags* 参数默认为 0，与 [`send()`]({{< ref "/library/ipc/socket#socket.socket.send" >}}) 中的含义相同。如果 *address* 指定为除 `None` 以外的值，它将作为消息的目标地址。返回值是已发送的非辅助数据的字节数。

​	在支持 `SCM_RIGHTS` 机制的系统上，下方的函数通过一个 [`AF_UNIX`]({{< ref "/library/ipc/socket#socket.AF_UNIX" >}}) 套接字来发送文件描述符列表 *fds*。另请参阅 [`recvmsg()`]({{< ref "/library/ipc/socket#socket.socket.recvmsg" >}})。

```
import socket, array

def send_fds(sock, msg, fds):
    return sock.sendmsg([msg], [(socket.SOL_SOCKET, socket.SCM_RIGHTS, array.array("i", fds))])
```

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI.

​	大多数 Unix 平台。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `socket.sendmsg` 并附带参数 `self`, `address`。

> Added in version 3.3.
>

> 在 3.5 版本发生变更: 如果系统调用被中断，但信号处理程序没有触发异常，此方法现在会重试系统调用，而不是触发 [`InterruptedError`]({{< ref "/library/exceptions#InterruptedError" >}}) 异常 (原因详见 [**PEP 475**](https://peps.python.org/pep-0475/))。

## socket.**sendmsg_afalg**([*msg*, ]***, *op*[, *iv*[, *assoclen*[, *flags*]]])

​	为 [`AF_ALG`]({{< ref "/library/ipc/socket#socket.AF_ALG" >}}) 套接字定制的 [`sendmsg()`]({{< ref "/library/ipc/socket#socket.socket.sendmsg" >}}) 版本。可为 [`AF_ALG`]({{< ref "/library/ipc/socket#socket.AF_ALG" >}}) 套接字设置模式、IV、AEAD 关联数据的长度和标志位。

[Availability]({{< ref "/library/intro#availability" >}}): Linux >= 2.6.38.

> Added in version 3.6.
>

## socket.**sendfile**(*file*, *offset=0*, *count=None*)

​	使用高性能的 [`os.sendfile`]({{< ref "/library/allos/os#os.sendfile" >}}) 发送文件，直到达到文件的 EOF 为止，返回已发送的字节总数。*file* 必须是一个以二进制模式打开的常规文件对象。如果 [`os.sendfile`]({{< ref "/library/allos/os#os.sendfile" >}}) 不可用（如 Windows）或 *file* 不是常规文件，将使用 [`send()`]({{< ref "/library/ipc/socket#socket.socket.send" >}}) 代替。*offset* 指示从哪里开始读取文件。如果指定了 *count*，它确定了要发送的字节总数，而不会持续发送直到达到文件的 EOF。返回时或发生错误时，文件位置将更新，在这种情况下，[`file.tell()`]({{< ref "/library/allos/io#io.IOBase.tell" >}}) 可用于确定已发送的字节数。套接字必须为 [`SOCK_STREAM`]({{< ref "/library/ipc/socket#socket.SOCK_STREAM" >}}) 类型。不支持非阻塞的套接字。

> Added in version 3.5.
>

## socket.**set_inheritable**(*inheritable*)

​	设置套接字文件描述符或套接字句柄的 [可继承标志]({{< ref "/library/allos/os#fd-inheritance" >}})。

> Added in version 3.4.
>

## socket.**setblocking**(*flag*)

​	设置套接字为阻塞或非阻塞模式：如果 *flag* 为 false，则将套接字设置为非阻塞，否则设置为阻塞。

​	本方法是某些 [`settimeout()`]({{< ref "/library/ipc/socket#socket.socket.settimeout" >}}) 调用的简写：

- `sock.setblocking(True)` 相当于 `sock.settimeout(None)`
- `sock.setblocking(False)` 相当于 `sock.settimeout(0.0)`

> 在 3.7 版本发生变更: 本方法不再对 [`socket.type`]({{< ref "/library/ipc/socket#socket.socket.type" >}}) 属性设置 [`SOCK_NONBLOCK`]({{< ref "/library/ipc/socket#socket.SOCK_NONBLOCK" >}}) 标志。

## socket.**settimeout**(*value*)

​	为阻塞套接字的操作设置超时。 *value* 参数可以是非负浮点数，表示秒，也可以是 `None`。 如果赋为一个非零值，那么如果在操作完成前超过了超时时间 *value*，后续的套接字操作将抛出 [`timeout`]({{< ref "/library/ipc/socket#socket.timeout" >}}) 异常。 如果赋为 0，则套接字将处于非阻塞模式。 如果指定为 `None`，则套接字将处于阻塞模式。

​	更多信息请查阅 [关于套接字超时的说明]({{< ref "/library/ipc/socket#socket-timeouts" >}})。

> 在 3.7 版本发生变更: 本方法不再修改 [`socket.type`]({{< ref "/library/ipc/socket#socket.socket.type" >}}) 属性的 [`SOCK_NONBLOCK`]({{< ref "/library/ipc/socket#socket.SOCK_NONBLOCK" >}}) 标志。

## socket.**setsockopt**(*level*, *optname*, *value: [int]({{< ref "/library/functions#int" >}})*)

## socket.**setsockopt**(*level*, *optname*, *value: buffer*)

## socket.**setsockopt**(*level*, *optname*, *None*, *optlen: int*)

​	设置给定套接字选项的值 (参见 Unix 手册页 *[setsockopt(2)](https://manpages.debian.org/setsockopt(2))*)。 所需的符号常量已定义在本模块中 (SO_* 等 <socket-unix-constants>)。 该值可以是整数、`None` 或表示缓冲区的 [bytes-like object]({{< ref "/glossary/idx#term-bytes-like-object" >}})。 在后一种情况下将由调用者确保字节串中包含正确的数据位 (请参阅可选的内置模块 [`struct`]({{< ref "/library/binary/struct#module-struct" >}}) 了解如何将 C 结构体编码为字节串)。 当 *value* 设为 `None` 时，*optlen* 参数是必须的。 这等价于调用 `setsockopt()` C 函数并设置 `optval=NULL` 和 `optlen=optlen`。

> 在 3.5 版本发生变更: 现在接受可写的 [字节类对象]({{< ref "/glossary/idx#term-bytes-like-object" >}})。

> 在 3.6 版本发生变更: 添加了 setsockopt(level, optname, None, optlen: int) 调用形式。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

## socket.**shutdown**(*how*)

​	关闭一半或全部的连接。如果 *how* 为 [`SHUT_RD`]({{< ref "/library/ipc/socket#socket.SHUT_RD" >}})，则后续不再允许接收。如果 *how* 为 [`SHUT_WR`]({{< ref "/library/ipc/socket#socket.SHUT_WR" >}})，则后续不再允许发送。如果 *how* 为 [`SHUT_RDWR`]({{< ref "/library/ipc/socket#socket.SHUT_RDWR" >}})，则后续的发送和接收都不允许。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

## socket.**share**(*process_id*)

​	复制套接字，并准备将其与目标进程共享。目标进程必须以 *process_id* 形式提供。然后可以利用某种形式的进程间通信，将返回的字节对象传递给目标进程，还可以使用 [`fromshare()`]({{< ref "/library/ipc/socket#socket.fromshare" >}}) 在新进程中重新创建套接字。一旦本方法调用完毕，就可以安全地将套接字关闭，因为操作系统已经为目标进程复制了该套接字。

[Availability]({{< ref "/library/intro#availability" >}}): Windows.

> Added in version 3.3.
>

​	注意此处没有 `read()` 或 `write()` 方法，请使用不带 *flags* 参数的 [`recv()`]({{< ref "/library/ipc/socket#socket.socket.recv" >}}) 和 [`send()`]({{< ref "/library/ipc/socket#socket.socket.send" >}}) 来替代。

​	套接字对象还具有以下（只读）属性，这些属性与传入 [`socket`]({{< ref "/library/ipc/socket#socket.socket" >}}) 构造函数的值相对应。

## socket.**family**

​	套接字的协议簇。

## socket.**type**

​	套接字的类型。

## socket.**proto**

​	套接字的协议。



## 关于套接字超时的说明

​	一个套接字对象可以处于以下三种模式之一：阻塞、非阻塞或超时。套接字默认以阻塞模式创建，但是可以调用 [`setdefaulttimeout()`]({{< ref "/library/ipc/socket#socket.setdefaulttimeout" >}}) 来更改。

- 在 *blocking mode* （阻塞模式）中，操作将阻塞，直到操作完成或系统返回错误（如连接超时）。
- 在 *非阻塞模式* 中，如果操作无法立即完成则该操作将失败（不幸的是它所附带的错误将依赖于具体系统）: 来自 [`select`]({{< ref "/library/ipc/select#module-select" >}}) 模块的函数可被用来获知一个套接字是否可以读取或写入。
- 在 *timeout mode* （超时模式）下，如果无法在指定的超时内完成操作（抛出 [`timeout`]({{< ref "/library/ipc/socket#socket.timeout" >}}) 异常），或如果系统返回错误，则操作将失败。

​备注
 

​	在操作系统层级，*超时模式* 下的套接字在内部都被设为非阻塞模式。 同时，阻塞和超时模式会在指向同一个网络端点的文件描述符和套接字对象之间共享。 这一实现细节可能导致明显的后果，例如当你决定使用套接字的 [`fileno()`]({{< ref "/library/ipc/socket#socket.socket.fileno" >}}) 的时候。

### 超时与 `connect` 方法

[`connect()`]({{< ref "/library/ipc/socket#socket.socket.connect" >}}) 操作也受超时设置的约束，通常建议在调用 [`connect()`]({{< ref "/library/ipc/socket#socket.socket.connect" >}}) 之前调用 [`settimeout()`]({{< ref "/library/ipc/socket#socket.socket.settimeout" >}})，或将超时参数直接传递给 [`create_connection()`]({{< ref "/library/ipc/socket#socket.create_connection" >}})。但是，无论 Python 套接字超时设置如何，系统网络栈都有可能返回自带的连接超时错误。

### 超时与 `accept` 方法

​	如果 [`getdefaulttimeout()`]({{< ref "/library/ipc/socket#socket.getdefaulttimeout" >}}) 的值不是 [`None`]({{< ref "/library/constants#None" >}})，则 [`accept()`]({{< ref "/library/ipc/socket#socket.socket.accept" >}}) 方法返回的套接字将继承该超时值。若是 None，返回的套接字行为取决于侦听套接字的设置：

- 如果侦听套接字处于 *阻塞模式* 或 *超时模式*，则 [`accept()`]({{< ref "/library/ipc/socket#socket.socket.accept" >}}) 返回的套接字处于 *阻塞模式*；
- 如果侦听套接字处于 *非阻塞模式*，那么 [`accept()`]({{< ref "/library/ipc/socket#socket.socket.accept" >}}) 返回的套接字是阻塞还是非阻塞取决于操作系统。如果要确保跨平台时的正确行为，建议手动覆盖此设置。



## 示例

​	以下是四个使用 TCP/IP 协议的最小示例程序：一个将收到的所有数据原样回馈的服务器（仅服务一个客户端），和一个使用该服务器的客户端。 请注意服务器必须按 [`socket()`]({{< ref "/library/ipc/socket#socket.socket" >}}), [`bind()`]({{< ref "/library/ipc/socket#socket.socket.bind" >}}), [`listen()`]({{< ref "/library/ipc/socket#socket.socket.listen" >}}), [`accept()`]({{< ref "/library/ipc/socket#socket.socket.accept" >}}) 的顺序执行（可能需要重复执行 [`accept()`]({{< ref "/library/ipc/socket#socket.socket.accept" >}}) 以便 服务多个客户端），而客户端仅需要按 [`socket()`]({{< ref "/library/ipc/socket#socket.socket" >}}), [`connect()`]({{< ref "/library/ipc/socket#socket.socket.connect" >}}) 的顺序执行。 还要注意服务器不是在侦听的套接字上发送 [`sendall()`]({{< ref "/library/ipc/socket#socket.socket.sendall" >}})/[`recv()`]({{< ref "/library/ipc/socket#socket.socket.recv" >}}) 而是在由 [`accept()`]({{< ref "/library/ipc/socket#socket.socket.accept" >}}) 返回的新套接字上发送。

​	前两个示例仅支持 IPv4。

```
# Echo server program
import socket

HOST = ''                 # 该符号名表示所有可用接口
PORT = 50007              # 任意非特权端口
with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.bind((HOST, PORT))
    s.listen(1)
    conn, addr = s.accept()
    with conn:
        print('Connected by', addr)
        while True:
            data = conn.recv(1024)
            if not data: break
            conn.sendall(data)
# 回显客户端程序
import socket

HOST = 'daring.cwi.nl'    # 远端主机
PORT = 50007              # 与服务器所用端口相同
with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.connect((HOST, PORT))
    s.sendall(b'Hello, world')
    data = s.recv(1024)
print('Received', repr(data))
```

​	接下来的两个例子与上面两个很想像，但同时支持 IPv4 和 IPv6。 服务端将监听第一个可用的地址族（它本应同时监听两个地址族）。 在大多数支持 IPv6 的系统中，IPv6 将有优先权并且服务端可能不会接受 IPv4 流量。 客户端将尝试连接到作为名称解析结果被返回的所有地址，并将流量发送给第一个成功连接的地址。

```
# 回显服务端程序
import socket
import sys

HOST = None               # 该符号名表示所有可用接口
PORT = 50007              # 任意非特权端口
s = None
for res in socket.getaddrinfo(HOST, PORT, socket.AF_UNSPEC,
                              socket.SOCK_STREAM, 0, socket.AI_PASSIVE):
    af, socktype, proto, canonname, sa = res
    try:
        s = socket.socket(af, socktype, proto)
    except OSError as msg:
        s = None
        continue
    try:
        s.bind(sa)
        s.listen(1)
    except OSError as msg:
        s.close()
        s = None
        continue
    break
if s is None:
    print('could not open socket')
    sys.exit(1)
conn, addr = s.accept()
with conn:
    print('Connected by', addr)
    while True:
        data = conn.recv(1024)
        if not data: break
        conn.send(data)
# 回显客户端程序
import socket
import sys

HOST = 'daring.cwi.nl'    # 远端主机
PORT = 50007              # 与服务器所用端口相同
s = None
for res in socket.getaddrinfo(HOST, PORT, socket.AF_UNSPEC, socket.SOCK_STREAM):
    af, socktype, proto, canonname, sa = res
    try:
        s = socket.socket(af, socktype, proto)
    except OSError as msg:
        s = None
        continue
    try:
        s.connect(sa)
    except OSError as msg:
        s.close()
        s = None
        continue
    break
if s is None:
    print('could not open socket')
    sys.exit(1)
with s:
    s.sendall(b'Hello, world')
    data = s.recv(1024)
print('Received', repr(data))
```

​	下面的例子演示了如何在 Windows 上使用原始套接字编写一个非常简单的网络嗅探器。 这个例子需要管理员权限来修改接口:

```
import socket

# 公共网络接口
HOST = socket.gethostbyname(socket.gethostname())

# 创建一个原始套接字并将其绑定到公共接口
s = socket.socket(socket.AF_INET, socket.SOCK_RAW, socket.IPPROTO_IP)
s.bind((HOST, 0))

# 包括 IP 标头
s.setsockopt(socket.IPPROTO_IP, socket.IP_HDRINCL, 1)

# 接收所有数据包
s.ioctl(socket.SIO_RCVALL, socket.RCVALL_ON)

# 接收一个数据包
print(s.recvfrom(65565))

# 禁用混杂模式
s.ioctl(socket.SIO_RCVALL, socket.RCVALL_OFF)
```

​	下面的例子演示了如何使用 socket 接口与采用原始套接字协议的 CAN 网络进行通信。 要改为通过广播管理器协议来使用 CAN，则要用以下方式打开一个 socket:

```
socket.socket(socket.AF_CAN, socket.SOCK_DGRAM, socket.CAN_BCM)
```

​	在绑定 (`CAN_RAW`) 或连接 ([`CAN_BCM`]({{< ref "/library/ipc/socket#socket.CAN_BCM" >}})) 套接字之后，你将可以在套接字对象上正常地使用 [`socket.send()`]({{< ref "/library/ipc/socket#socket.socket.send" >}}) 和 [`socket.recv()`]({{< ref "/library/ipc/socket#socket.socket.recv" >}}) 操作（及其同类操作）。

​	最后一个例子可能需要特别的权限:

```
import socket
import struct


# CAN 帧打包/解包 (参见 <linux/can.h> 中的 'struct can_frame')

can_frame_fmt = "=IB3x8s"
can_frame_size = struct.calcsize(can_frame_fmt)

def build_can_frame(can_id, data):
    can_dlc = len(data)
    data = data.ljust(8, b'\x00')
    return struct.pack(can_frame_fmt, can_id, can_dlc, data)

def dissect_can_frame(frame):
    can_id, can_dlc, data = struct.unpack(can_frame_fmt, frame)
    return (can_id, can_dlc, data[:can_dlc])


# 创建一个原始套接字并将其绑定到 'vcan0' 接口
s = socket.socket(socket.AF_CAN, socket.SOCK_RAW, socket.CAN_RAW)
s.bind(('vcan0',))

while True:
    cf, addr = s.recvfrom(can_frame_size)

    print('Received: can_id=%x, can_dlc=%x, data=%s' % dissect_can_frame(cf))

    try:
        s.send(cf)
    except OSError:
        print('Error sending CAN frame')

    try:
        s.send(build_can_frame(0x01, b'\x01\x02\x03'))
    except OSError:
        print('Error sending CAN frame')
```

​	多次运行一个示例，且每次执行之间等待时间过短，可能导致这个错误:

```
OSError: [Errno 98] Address already in use
```

​	这是因为前一次运行使套接字处于 `TIME_WAIT` 状态，无法立即重用。

​	要防止这种情况，需要设置一个 [`socket`]({{< ref "/library/ipc/socket#module-socket" >}}) 旗标 `socket.SO_REUSEADDR`:

```
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
s.bind((HOST, PORT))
```

`SO_REUSEADDR` 标志告诉内核将处于 `TIME_WAIT` 状态的本地套接字重新使用，而不必等到固有的超时到期。

​参见
 

​	关于套接字编程（C 语言）的介绍，请参阅以下文章：

- *An Introductory 4.3BSD Interprocess Communication Tutorial*，作者 Stuart Sechrest
- *An Advanced 4.3BSD Interprocess Communication Tutorial*，作者 Samuel J. Leffler et al,

​	两篇文章都在 UNIX 开发者手册，补充文档 1（第 PS1:7 和 PS1:8 节）中。那些特定于平台的参考资料，它们包含与套接字有关的各种系统调用，也是套接字语义细节的宝贵信息来源。对于 Unix，请参考手册页。对于 Windows，请参阅 WinSock（或 Winsock 2）规范。如果需要支持 IPv6 的 API，读者可能希望参考 [**RFC 3493**](https://datatracker.ietf.org/doc/html/rfc3493.html)，标题为 Basic Socket Interface Extensions for IPv6。
