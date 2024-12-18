+++
title = "低层级 API 索引"
date = 2024-11-15T12:30:27+08:00
weight = 150
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/asyncio-llapi-index.html](https://docs.python.org/zh-cn/3.13/library/asyncio-llapi-index.html)
>
> 收录该文档的时间：`2024-11-15T12:30:27+08:00`

# 低层级 API 索引

​	本页列出所有低层级的 asyncio API。

## 获取事件循环

| [`asyncio.get_running_loop()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.get_running_loop" >}}) | 获取当前运行的事件循环 **首选** 函数。                       |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [`asyncio.get_event_loop()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.get_event_loop" >}}) | 获取一个事件循环实例（正在运行的事件循环或通过当前策略确定的当前事件循环）。 |
| [`asyncio.set_event_loop()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.set_event_loop" >}}) | 通过当前策略将事件循环设置当前事件循环。                     |
| [`asyncio.new_event_loop()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.new_event_loop" >}}) | 创建一个新的事件循环。                                       |

​	例子

- [使用asyncio.get_running_loop()]({{< ref "/library/ipc/asyncio/asyncio-future#asyncio-example-future" >}})。

## 事件循环方法集

​	另请参阅有关 [事件循环方法集]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio-event-loop-methods" >}}) 的主文档章节。

​	生命周期

| [`loop.run_until_complete()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.run_until_complete" >}}) | 运行一个期程/任务/可等待对象直到完成。  |
| ------------------------------------------------------------ | --------------------------------------- |
| [`loop.run_forever()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.run_forever" >}}) | 一直运行事件循环。                      |
| [`loop.stop()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.stop" >}}) | 停止事件循环。                          |
| [`loop.close()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.close" >}}) | 关闭事件循环。                          |
| [`loop.is_running()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.is_running" >}}) | 返回 `True` ， 如果事件循环正在运行。   |
| [`loop.is_closed()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.is_closed" >}}) | 返回 `True` ，如果事件循环已经被关闭 。 |
| `await` [`loop.shutdown_asyncgens()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.shutdown_asyncgens" >}}) | 关闭异步生成器。                        |

​	调试

| [`loop.set_debug()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.set_debug" >}}) | 开启或禁用调试模式。 |
| ------------------------------------------------------------ | -------------------- |
| [`loop.get_debug()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.get_debug" >}}) | 获取当前测试模式。   |

​	调度回调函数

| [`loop.call_soon()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.call_soon" >}}) | 尽快调用回调。                                               |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [`loop.call_soon_threadsafe()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.call_soon_threadsafe" >}}) | [`loop.call_soon()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.call_soon" >}}) 方法线程安全的变体。 |
| [`loop.call_later()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.call_later" >}}) | 在给定时间 *之后* 调用回调函数。                             |
| [`loop.call_at()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.call_at" >}}) | 在 *指定* 时间调用回调函数。                                 |

​	线程/进程池

| `await` [`loop.run_in_executor()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.run_in_executor" >}}) | 在 [`concurrent.futures`]({{< ref "/library/concurrency/future/concurrent_futures#module-concurrent.futures" >}}) 执行器中运行一个独占CPU或其它阻塞函数。 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [`loop.set_default_executor()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.set_default_executor" >}}) | 设置 [`loop.run_in_executor()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.run_in_executor" >}}) 默认执行器。 |

​	任务与期程

| [`loop.create_future()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_future" >}}) | 创建一个 [`Future`]({{< ref "/library/ipc/asyncio/asyncio-future#asyncio.Future" >}}) 对象。 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [`loop.create_task()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_task" >}}) | 将协程当作 [`Task`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}}) 一样调度。 |
| [`loop.set_task_factory()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.set_task_factory" >}}) | 设置 [`loop.create_task()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_task" >}}) 使用的工厂，它将用来创建 [`Tasks`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}}) 。 |
| [`loop.get_task_factory()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.get_task_factory" >}}) | 获取 [`loop.create_task()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_task" >}}) 使用的工厂，它用来创建 [`Tasks`]({{< ref "/library/ipc/asyncio/asyncio-task#asyncio.Task" >}}) 。 |

​	DNS

| `await` [`loop.getaddrinfo()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.getaddrinfo" >}}) | 异步版的 [`socket.getaddrinfo()`]({{< ref "/library/ipc/socket#socket.getaddrinfo" >}}) 。 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `await` [`loop.getnameinfo()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.getnameinfo" >}}) | 异步版的 [`socket.getnameinfo()`]({{< ref "/library/ipc/socket#socket.getnameinfo" >}}) 。 |

​	网络和IPC

| `await` [`loop.create_connection()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_connection" >}}) | 打开一个TCP链接。                                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `await` [`loop.create_server()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_server" >}}) | 创建一个TCP服务。                                            |
| `await` [`loop.create_unix_connection()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_unix_connection" >}}) | 打开一个Unix socket连接。                                    |
| `await` [`loop.create_unix_server()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_unix_server" >}}) | 创建一个Unix socket服务。                                    |
| `await` [`loop.connect_accepted_socket()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.connect_accepted_socket" >}}) | 将 [`socket`]({{< ref "/library/ipc/socket#socket.socket" >}}) 包装成 `(transport, protocol)` 对。 |
| `await` [`loop.create_datagram_endpoint()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_datagram_endpoint" >}}) | 打开一个数据报(UDP)连接。                                    |
| `await` [`loop.sendfile()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.sendfile" >}}) | 通过传输通道发送一个文件。                                   |
| `await` [`loop.start_tls()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.start_tls" >}}) | 将一个已建立的链接升级到TLS。                                |
| `await` [`loop.connect_read_pipe()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.connect_read_pipe" >}}) | 将管道读取端包装成 `(transport, protocol)` 对。              |
| `await` [`loop.connect_write_pipe()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.connect_write_pipe" >}}) | 将管道写入端包装成 `(transport, protocol)` 对。              |

​	套接字

| `await` [`loop.sock_recv()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.sock_recv" >}}) | 从 [`socket`]({{< ref "/library/ipc/socket#socket.socket" >}}) 接收数据。 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `await` [`loop.sock_recv_into()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.sock_recv_into" >}}) | 从 [`socket`]({{< ref "/library/ipc/socket#socket.socket" >}}) 接收数据到一个缓冲区中。 |
| `await` [`loop.sock_recvfrom()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.sock_recvfrom" >}}) | 从 [`socket`]({{< ref "/library/ipc/socket#socket.socket" >}}) 接收数据报。 |
| `await` [`loop.sock_recvfrom_into()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.sock_recvfrom_into" >}}) | 从 [`socket`]({{< ref "/library/ipc/socket#socket.socket" >}}) 接收数据报并放入缓冲区。 |
| `await` [`loop.sock_sendall()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.sock_sendall" >}}) | 发送数据到 [`socket`]({{< ref "/library/ipc/socket#socket.socket" >}}) 。 |
| `await` [`loop.sock_sendto()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.sock_sendto" >}}) | 通过 [`socket`]({{< ref "/library/ipc/socket#socket.socket" >}}) 向给定的地址发送数据报。 |
| `await` [`loop.sock_connect()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.sock_connect" >}}) | 链接 `await` [`loop.sock_connect()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.sock_connect" >}}) 。 |
| `await` [`loop.sock_accept()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.sock_accept" >}}) | 接受一个 [`socket`]({{< ref "/library/ipc/socket#socket.socket" >}}) 链接。 |
| `await` [`loop.sock_sendfile()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.sock_sendfile" >}}) | 利用 [`socket`]({{< ref "/library/ipc/socket#socket.socket" >}}) 发送一个文件。 |
| [`loop.add_reader()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.add_reader" >}}) | 开始对一个文件描述符的可读性的监视。                         |
| [`loop.remove_reader()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.remove_reader" >}}) | 停止对一个文件描述符的可读性的监视。                         |
| [`loop.add_writer()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.add_writer" >}}) | 开始对一个文件描述符的可写性的监视。                         |
| [`loop.remove_writer()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.remove_writer" >}}) | 停止对一个文件描述符的可写性的监视。                         |

​	Unix信号

| [`loop.add_signal_handler()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.add_signal_handler" >}}) | 给 [`signal`]({{< ref "/library/ipc/signal#module-signal" >}}) 添加一个处理回调函数。 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [`loop.remove_signal_handler()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.remove_signal_handler" >}}) | 删除 [`signal`]({{< ref "/library/ipc/signal#module-signal" >}}) 的处理回调函数。 |

​	子进程集

| [`loop.subprocess_exec()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.subprocess_exec" >}}) | 衍生一个子进程             |
| ------------------------------------------------------------ | -------------------------- |
| [`loop.subprocess_shell()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.subprocess_shell" >}}) | 从终端命令衍生一个子进程。 |

​	错误处理

| [`loop.call_exception_handler()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.call_exception_handler" >}}) | 调用异常处理器。         |
| ------------------------------------------------------------ | ------------------------ |
| [`loop.set_exception_handler()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.set_exception_handler" >}}) | 设置一个新的异常处理器。 |
| [`loop.get_exception_handler()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.get_exception_handler" >}}) | 获取当前异常处理器。     |
| [`loop.default_exception_handler()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.default_exception_handler" >}}) | 默认异常处理器实现。     |

​	例子

- [使用 asyncio.new_event_loop() 和 loop.run_forever()]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio-example-lowlevel-helloworld" >}}).
- [使用 loop.call_later()]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio-example-call-later" >}}).
- 使用 `loop.create_connection()` 实现 [echo客户端]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio-example-tcp-echo-client-protocol" >}}).
- 使用 `loop.create_connection()` 去 [链接socket]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio-example-create-connection" >}}).
- [使用add_reader()监听FD(文件描述符)的读取事件]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio-example-watch-fd" >}}).
- [使用loop.add_signal_handler()]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio-example-unix-signals" >}}).
- [使用loop.add_signal_handler()]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio-example-subprocess-proto" >}})。

## 传输

​	所有传输都实现以下方法:

| [`transport.close()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.BaseTransport.close" >}}) | 关闭传输。                                 |
| ------------------------------------------------------------ | ------------------------------------------ |
| [`transport.is_closing()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.BaseTransport.is_closing" >}}) | 返回 `True` ，如果传输正在关闭或已经关闭。 |
| [`transport.get_extra_info()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.BaseTransport.get_extra_info" >}}) | 请求传输的相关信息。                       |
| [`transport.set_protocol()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.BaseTransport.set_protocol" >}}) | 设置一个新协议。                           |
| [`transport.get_protocol()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.BaseTransport.get_protocol" >}}) | 返回当前协议。                             |

​	传输可以接收数据(TCP和Unix链接，管道等)。它通过 [`loop.create_connection()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_connection" >}}), [`loop.create_unix_connection()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_unix_connection" >}}), [`loop.connect_read_pipe()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.connect_read_pipe" >}}) 等方法返回。

​	读取传输

| [`transport.is_reading()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.ReadTransport.is_reading" >}}) | 返回 `True` ，如果传输正在接收。 |
| ------------------------------------------------------------ | -------------------------------- |
| [`transport.pause_reading()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.ReadTransport.pause_reading" >}}) | 暂停接收。                       |
| [`transport.resume_reading()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.ReadTransport.resume_reading" >}}) | 继续接收。                       |

​	传输可以发送数据(TCP和Unix链接，管道等)。它通过 [`loop.create_connection()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_connection" >}}), [`loop.create_unix_connection()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_unix_connection" >}}), [`loop.connect_write_pipe()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.connect_write_pipe" >}}) 等方法返回。

​	写入传输

| [`transport.write()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.WriteTransport.write" >}}) | 向传输写入数据。                                             |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [`transport.write()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.WriteTransport.write" >}}) | 向传输写入缓冲。                                             |
| [`transport.can_write_eof()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.WriteTransport.can_write_eof" >}}) | 返回 [`True`]({{< ref "/library/constants#True" >}}) ，如果传输支持发送 EOF。 |
| [`transport.write_eof()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.WriteTransport.write_eof" >}}) | 在冲洗已缓冲的数据后关闭传输和发送EOF。                      |
| [`transport.abort()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.WriteTransport.abort" >}}) | 立即关闭传输。                                               |
| [`transport.get_write_buffer_size()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.WriteTransport.get_write_buffer_size" >}}) | 返回当前输出缓冲区的大小。                                   |
| [`transport.get_write_buffer_limits()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.WriteTransport.get_write_buffer_limits" >}}) | 返回写入流控制的高位标记位和低位标记位。                     |
| [`transport.set_write_buffer_limits()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.WriteTransport.set_write_buffer_limits" >}}) | 设置新的写入流控制的高位标记位和低位标记位。                 |

​	由 [`loop.create_datagram_endpoint()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.create_datagram_endpoint" >}}) 返回的传输:

​	数据报传输

| [`transport.sendto()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.DatagramTransport.sendto" >}}) | 发送数据到远程链接端。 |
| ------------------------------------------------------------ | ---------------------- |
| [`transport.abort()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.DatagramTransport.abort" >}}) | 立即关闭传输。         |

​	基于子进程的底层抽象传输，它由 [`loop.subprocess_exec()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.subprocess_exec" >}}) 和 [`loop.subprocess_shell()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.loop.subprocess_shell" >}}) 返回:

​	子进程传输

| [`transport.get_pid()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.SubprocessTransport.get_pid" >}}) | 返回子进程的进程ID。                                      |
| ------------------------------------------------------------ | --------------------------------------------------------- |
| [`transport.get_pipe_transport()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.SubprocessTransport.get_pipe_transport" >}}) | 返回请求通信管道 (*stdin*, *stdout*, 或 *stderr*)的传输。 |
| [`transport.get_returncode()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.SubprocessTransport.get_returncode" >}}) | 返回子进程的返回代号。                                    |
| [`transport.kill()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.SubprocessTransport.kill" >}}) | 杀死子进程。                                              |
| [`transport.send_signal()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.SubprocessTransport.send_signal" >}}) | 发送一个信号到子进程。                                    |
| [`transport.terminate()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.SubprocessTransport.terminate" >}}) | 停止子进程。                                              |
| [`transport.close()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.SubprocessTransport.close" >}}) | 杀死子进程并关闭所有管道。                                |

## 协议

​	协议类可以由下面 **回调方法** 实现：

| `callback` [`connection_made()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.BaseProtocol.connection_made" >}}) | 连接建立时被调用。                     |
| ------------------------------------------------------------ | -------------------------------------- |
| `callback` [`connection_lost()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.BaseProtocol.connection_lost" >}}) | 连接丢失或关闭时将被调用。             |
| `callback` [`pause_writing()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.BaseProtocol.pause_writing" >}}) | 传输的缓冲区超过高位标记位时被调用。   |
| `callback` [`resume_writing()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.BaseProtocol.resume_writing" >}}) | 传输的缓冲区传送到低位标记位时被调用。 |

​	流协议 (TCP, Unix 套接字, 管道)

| `callback` [`data_received()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.Protocol.data_received" >}}) | 接收到数据时被调用。 |
| ------------------------------------------------------------ | -------------------- |
| `callback` [`eof_received()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.Protocol.eof_received" >}}) | 接收到EOF时被调用。  |

​	缓冲流协议

| `callback` [`get_buffer()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.BufferedProtocol.get_buffer" >}}) | 调用后会分配新的接收缓冲区。     |
| ------------------------------------------------------------ | -------------------------------- |
| `callback` [`buffer_updated()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.BufferedProtocol.buffer_updated" >}}) | 用接收的数据更新缓冲区时被调用。 |
| `callback` [`eof_received()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.BufferedProtocol.eof_received" >}}) | 接收到EOF时被调用。              |

​	数据报协议

| `callback` [`datagram_received()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.DatagramProtocol.datagram_received" >}}) | 接收到数据报时被调用。                                       |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `callback` [`error_received()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.DatagramProtocol.error_received" >}}) | 前一个发送或接收操作引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 时被调用。 |

​	子进程协议

| `callback` [`pipe_data_received()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.SubprocessProtocol.pipe_data_received" >}}) | 子进程向 *stdout* 或 *stderr* 管道写入数据时被调用。         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `callback` [`pipe_connection_lost()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.SubprocessProtocol.pipe_connection_lost" >}}) | 与子进程通信的其中一个管道关闭时被调用。                     |
| `callback` [`process_exited()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.SubprocessProtocol.process_exited" >}}) | 当子进程退出时被调用。 可在 [`pipe_data_received()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.SubprocessProtocol.pipe_data_received" >}}) 和 [`pipe_connection_lost()`]({{< ref "/library/ipc/asyncio/asyncio-protocol#asyncio.SubprocessProtocol.pipe_connection_lost" >}}) 方法之前被调用。 |

## 事件循环策略

​	策略是改变 [`asyncio.get_event_loop()`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.get_event_loop" >}}) 这类函数行为的一个底层机制。更多细节可以查阅 [策略部分]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio-policies" >}})。

​	访问策略

| [`asyncio.get_event_loop_policy()`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.get_event_loop_policy" >}}) | 返回当前进程域的策略。   |
| ------------------------------------------------------------ | ------------------------ |
| [`asyncio.set_event_loop_policy()`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.set_event_loop_policy" >}}) | 设置一个新的进程域策略。 |
| [`AbstractEventLoopPolicy`]({{< ref "/library/ipc/asyncio/asyncio-policy#asyncio.AbstractEventLoopPolicy" >}}) | 策略对象的基类。         |
