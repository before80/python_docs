+++
title = "流"
date = 2024-11-15T12:30:27+08:00
weight = 30
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html)
>
> 收录该文档的时间：`2024-11-15T12:30:27+08:00`

# 流

**源码:** [Lib/asyncio/streams.py](https://github.com/python/cpython/tree/3.13/Lib/asyncio/streams.py)

------

​	流是用于处理网络连接的支持 async/await 的高层级原语。 流允许发送和接收数据，而不需要使用回调或低级协议和传输。

​	下面是一个使用 asyncio streams 编写的 TCP echo 客户端示例:

```
import asyncio

async def tcp_echo_client(message):
    reader, writer = await asyncio.open_connection(
        '127.0.0.1', 8888)

    print(f'Send: {message!r}')
    writer.write(message.encode())
    await writer.drain()

    data = await reader.read(100)
    print(f'Received: {data.decode()!r}')

    print('Close the connection')
    writer.close()
    await writer.wait_closed()

asyncio.run(tcp_echo_client('Hello World!'))
```

​	参见下面的 [Examples](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#examples) 部分。

​	Stream 函数

​	下面的高级 asyncio 函数可以用来创建和处理流:

## *coroutine* asyncio.**open_connection**(*host=None*, *port=None*, ***, *limit=None*, *ssl=None*, *family=0*, *proto=0*, *flags=0*, *sock=None*, *local_addr=None*, *server_hostname=None*, *ssl_handshake_timeout=None*, *ssl_shutdown_timeout=None*, *happy_eyeballs_delay=None*, *interleave=None*)

​	建立网络连接并返回一对 `(reader, writer)` 对象。

​	返回的 *reader* 和 *writer* 对象是 [`StreamReader`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.StreamReader) 和 [`StreamWriter`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.StreamWriter) 类的实例。

*limit* 确定返回的 [`StreamReader`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.StreamReader) 实例使用的缓冲区大小限制。默认情况下，*limit* 设置为 64 KiB 。

​	其余的参数直接传递到 [`loop.create_connection()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.loop.create_connection) 。

​	备注

 

*sock* 参数可将套接字的所有权转给所创建的 [`StreamWriter`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.StreamWriter)。 要关闭该套接字，请调用其 [`close()`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.StreamWriter.close) 方法。

*在 3.7 版本发生变更:* 添加了 *ssl_handshake_timeout* 形参。

*在 3.8 版本发生变更:* 增加了 *happy_eyeballs_delay* 和 *interleave* 形参。

*在 3.10 版本发生变更:* 移除了 *loop* 形参。

*在 3.11 版本发生变更:* 添加了 *ssl_shutdown_timeout* 形参。

## *coroutine* asyncio.**start_server**(*client_connected_cb*, *host=None*, *port=None*, ***, *limit=None*, *family=socket.AF_UNSPEC*, *flags=socket.AI_PASSIVE*, *sock=None*, *backlog=100*, *ssl=None*, *reuse_address=None*, *reuse_port=None*, *ssl_handshake_timeout=None*, *ssl_shutdown_timeout=None*, *start_serving=True*)

​	启动套接字服务。

​	当一个新的客户端连接被建立时，回调函数 *client_connected_cb* 会被调用。该函数会接收到一对参数 `(reader, writer)` ，reader是类 [`StreamReader`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.StreamReader) 的实例，而writer是类 [`StreamWriter`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.StreamWriter) 的实例。

*client_connected_cb* 即可以是普通的可调用对象也可以是一个 [协程函数](https://docs.python.org/zh-cn/3.13/library/asyncio-task.html#coroutine); 如果它是一个协程函数，它将自动作为 [`Task`](https://docs.python.org/zh-cn/3.13/library/asyncio-task.html#asyncio.Task) 被调度。

*limit* 确定返回的 [`StreamReader`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.StreamReader) 实例使用的缓冲区大小限制。默认情况下，*limit* 设置为 64 KiB 。

​	余下的参数将会直接传递给 [`loop.create_server()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.loop.create_server).

​	备注

 

*sock* 参数可将套接字的所有权转给所创建的服务器。 要关闭该套接字，请调用服务器的 [`close()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.Server.close) 方法。

*在 3.7 版本发生变更:* 增加了 *ssl_handshake_timeout* 和 *start_serving* 形参。

*在 3.10 版本发生变更:* 移除了 *loop* 形参。

*在 3.11 版本发生变更:* 添加了 *ssl_shutdown_timeout* 形参。

​	Unix 套接字

## *coroutine* asyncio.**open_unix_connection**(*path=None*, ***, *limit=None*, *ssl=None*, *sock=None*, *server_hostname=None*, *ssl_handshake_timeout=None*, *ssl_shutdown_timeout=None*)

​	建立一个 Unix 套接字连接并返回 `(reader, writer)` 这对返回值。

​	与 [`open_connection()`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.open_connection) 相似，但是是在 Unix 套接字上的操作。

​	请看文档 [`loop.create_unix_connection()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.loop.create_unix_connection).

​	备注

 

*sock* 参数可将套接字的所有权转给所创建的 [`StreamWriter`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.StreamWriter)。 要关闭该套接字，请调用其 [`close()`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.StreamWriter.close) 方法。

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): Unix.

*在 3.7 版本发生变更:* 增加了 *ssl_handshake_timeout* 形参。 现在 *path* 形参可以是一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)

*在 3.10 版本发生变更:* 移除了 *loop* 形参。

*在 3.11 版本发生变更:* 添加了 *ssl_shutdown_timeout* 形参。

## *coroutine* asyncio.**start_unix_server**(*client_connected_cb*, *path=None*, ***, *limit=None*, *sock=None*, *backlog=100*, *ssl=None*, *ssl_handshake_timeout=None*, *ssl_shutdown_timeout=None*, *start_serving=True*)

​	启动一个 Unix 套接字服务。

​	与 [`start_server()`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.start_server) 相似，但是是在 Unix 套接字上的操作。

​	请看文档 [`loop.create_unix_server()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.loop.create_unix_server).

​	备注

 

*sock* 参数可将套接字的所有权转给所创建的服务器。 要关闭该套接字，请调用服务器的 [`close()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.Server.close) 方法。

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): Unix.

*在 3.7 版本发生变更:* 增加了 *ssl_handshake_timeout* 和 *start_serving* 形参。 现在 *path* 形参可以是一个 [path-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-path-like-object)。

*在 3.10 版本发生变更:* 移除了 *loop* 形参。

*在 3.11 版本发生变更:* 添加了 *ssl_shutdown_timeout* 形参。

## StreamReader

## *class* asyncio.**StreamReader**

​	代表一个提供从 IO 流读取数据的 API 的读取器。 作为一个 [asynchronous iterable](https://docs.python.org/zh-cn/3.13/glossary.html#term-asynchronous-iterable)，该对象支持 [`async for`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#async-for) 语句。

​	不推荐直接实例化 *StreamReader* 对象，建议使用 [`open_connection()`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.open_connection) 和 [`start_server()`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.start_server) 来获取 *StreamReader* 实例。

## **feed_eof**()

​	识别 EOF。

## *coroutine* **read**(*n=-1*)

​	从流读取至多 *n* 个字节。

​	如果未提供 *n* 或是设为 `-1`，则一直读取到 EOF，然后返回所读取的全部 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes)。 如果收到 EOF 并且内部缓冲区为空，则返回一个空 `bytes` 对象。object.

​	如果 *n* 为 `0`，则立即返回一个空 `bytes` 对象。

​	如果 *n* 为正值，则一旦内部缓冲区有至少 1 个字节可用就返回至多 *n* 个可用的 `bytes`。 如果在读取任何字节之前收到 EOF，则返回一个空 `bytes` 对象。

## *coroutine* **readline**()

​	读取一行，其中“行”指的是以 `\n` 结尾的字节序列。

​	如果读到EOF而没有找到 `\n` ，该方法返回部分读取的数据。

​	如果读到EOF，且内部缓冲区为空，则返回一个空的 `bytes` 对象。

## *coroutine* **readexactly**(*n*)

​	精确读取 *n* 个 bytes，不会超过也不能少于。

​	如果在读取完 *n* 个byte之前读取到EOF，则会引发 [`IncompleteReadError`](https://docs.python.org/zh-cn/3.13/library/asyncio-exceptions.html#asyncio.IncompleteReadError) 异常。使用 [`IncompleteReadError.partial`](https://docs.python.org/zh-cn/3.13/library/asyncio-exceptions.html#asyncio.IncompleteReadError.partial) 属性来获取到达流结束之前读取的 bytes 字符串。

## *coroutine* **readuntil**(*separator=b'\n'*)

​	从流中读取数据直至遇到 *separator*

​	成功后，数据和指定的separator将从内部缓冲区中删除(或者说被消费掉)。返回的数据将包括在末尾的指定separator。

​	如果读取的数据量超过了配置的流限制，将引发 [`LimitOverrunError`](https://docs.python.org/zh-cn/3.13/library/asyncio-exceptions.html#asyncio.LimitOverrunError) 异常，数据将留在内部缓冲区中并可以再次读取。

​	如果在找到完整的separator之前到达EOF，则会引发 [`IncompleteReadError`](https://docs.python.org/zh-cn/3.13/library/asyncio-exceptions.html#asyncio.IncompleteReadError) 异常，并重置内部缓冲区。 [`IncompleteReadError.partial`](https://docs.python.org/zh-cn/3.13/library/asyncio-exceptions.html#asyncio.IncompleteReadError.partial) 属性可能包含指定separator的一部分。

*separator* 也可以是由分隔符组成的元组。 在这种情况下返回值将为以任意分隔符为前缀的最短可能值。 对于 [`LimitOverrunError`](https://docs.python.org/zh-cn/3.13/library/asyncio-exceptions.html#asyncio.LimitOverrunError) 来说，分隔符的最短可能值将被认为是匹配的值。

> Added in version 3.5.2.
>

*在 3.13 版本发生变更:* 现在 *separator* 形参可以是由分隔符组成的 [`tuple`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#tuple)。

## **at_eof**()

​	如果缓冲区为空并且 [`feed_eof()`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.StreamReader.feed_eof) 被调用，则返回 `True` 。

## StreamWriter

## *class* asyncio.**StreamWriter**

​	这个类表示一个写入器对象，该对象提供api以便于写数据至IO流中。

​	不建议直接实例化 *StreamWriter*；而应改用 [`open_connection()`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.open_connection) 和 [`start_server()`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.start_server)。

## **write**(*data*)

​	此方法会尝试立即将 *data* 写入到下层的套接字。 如果写入失败，数据会被排入内部写缓冲队列直到可以被发送。

​	此方法应当与 `drain()` 方法一起使用:

```
stream.write(data)
await stream.drain()
```

## **writelines**(*data*)

​	此方法会立即尝试将一个字节串列表（或任何可迭代对象）写入到下层的套接字。 如果写入失败，数据会被排入内部写缓冲队列直到可以被发送。

​	此方法应当与 `drain()` 方法一起使用:

```
stream.writelines(lines)
await stream.drain()
```

## **close**()

​	此方法会关闭流以及下层的套接字。

​	此方法应当于 `wait_closed()` 方法一起使用，但这并非强制要求:

```
stream.close()
await stream.wait_closed()
```

## **can_write_eof**()

​	如果下层的传输支持 [`write_eof()`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.StreamWriter.write_eof) 方法则返回 `True`，否则返回 `False`。

## **write_eof**()

​	在已缓冲的写入数据被刷新后关闭流的写入端。

## **transport**

​	返回下层的 asyncio 传输。

## **get_extra_info**(*name*, *default=None*)

​	访问可选的传输信息；详情参见 [`BaseTransport.get_extra_info()`](https://docs.python.org/zh-cn/3.13/library/asyncio-protocol.html#asyncio.BaseTransport.get_extra_info)。

## *coroutine* **drain**()

​	等待直到可以适当地恢复写入到流。 示例:

```
writer.write(data)
await writer.drain()
```

​	这是一个与下层的 IO 写缓冲区进行交互的流程控制方法。 当缓冲区大小达到最高水位（最大上限）时，*drain()* 会阻塞直到缓冲区大小减少至最低水位以便恢复写入。 当没有要等待的数据时，[`drain()`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.StreamWriter.drain) 会立即返回。

## *coroutine* **start_tls**(*sslcontext*, ***, *server_hostname=None*, *ssl_handshake_timeout=None*, *ssl_shutdown_timeout=None*)

​	将现有基于流的连接升级到 TLS。

​	参数：

- *sslcontext* ：一个已经配置好的 [`SSLContext`](https://docs.python.org/zh-cn/3.13/library/ssl.html#ssl.SSLContext) 实例。
- *server_hostname* ：设置或者覆盖目标服务器证书中相对应的主机名。
- *ssl_handshake_timeout* 是在放弃连接之前要等待 TLS 握手完成的秒数。 如为 `None` (默认值) 则使用 `60.0`。
- *ssl_shutdown_timeout* 是在放弃连接之前要等待 SSL 关闭完成的秒数。 如为 `None` (默认值) 则使用 `30.0`。

> Added in version 3.11.
>

*在 3.12 版本发生变更:* 添加了 *ssl_shutdown_timeout* 形参。

## **is_closing**()

​	如果流已被关闭或正在被关闭则返回 `True`。

> Added in version 3.7.
>

## *coroutine* **wait_closed**()

​	等待直到流被关闭。

​	应当在 [`close()`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.StreamWriter.close) 之后调用以等待直到下层连接被关闭，确保所有数据在退出程序之前已刷新。

> Added in version 3.7.
>

## 例子



### 使用流的 TCP 回显客户端

​	使用 [`asyncio.open_connection()`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.open_connection) 函数的 TCP 回显客户端:

```
import asyncio

async def tcp_echo_client(message):
    reader, writer = await asyncio.open_connection(
        '127.0.0.1', 8888)

    print(f'Send: {message!r}')
    writer.write(message.encode())
    await writer.drain()

    data = await reader.read(100)
    print(f'Received: {data.decode()!r}')

    print('Close the connection')
    writer.close()
    await writer.wait_closed()

asyncio.run(tcp_echo_client('Hello World!'))
```

​	参见

 

​	使用低层级 [`loop.create_connection()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.loop.create_connection) 方法的 [TCP 回显客户端协议](https://docs.python.org/zh-cn/3.13/library/asyncio-protocol.html#asyncio-example-tcp-echo-client-protocol) 示例。



### 使用流的 TCP 回显服务器

​	TCP 回显服务器使用 [`asyncio.start_server()`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.start_server) 函数:

```
import asyncio

async def handle_echo(reader, writer):
    data = await reader.read(100)
    message = data.decode()
    addr = writer.get_extra_info('peername')

    print(f"Received {message!r} from {addr!r}")

    print(f"Send: {message!r}")
    writer.write(data)
    await writer.drain()

    print("Close the connection")
    writer.close()
    await writer.wait_closed()

async def main():
    server = await asyncio.start_server(
        handle_echo, '127.0.0.1', 8888)

    addrs = ', '.join(str(sock.getsockname()) for sock in server.sockets)
    print(f'Serving on {addrs}')

    async with server:
        await server.serve_forever()

asyncio.run(main())
```

​	参见

 

​	使用 [`loop.create_server()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.loop.create_server) 方法的 [TCP 回显服务器协议](https://docs.python.org/zh-cn/3.13/library/asyncio-protocol.html#asyncio-example-tcp-echo-server-protocol) 示例。

### 获取 HTTP 标头

​	查询命令行传入 URL 的 HTTP 标头的简单示例:

```
import asyncio
import urllib.parse
import sys

async def print_http_headers(url):
    url = urllib.parse.urlsplit(url)
    if url.scheme == 'https':
        reader, writer = await asyncio.open_connection(
            url.hostname, 443, ssl=True)
    else:
        reader, writer = await asyncio.open_connection(
            url.hostname, 80)

    query = (
        f"HEAD {url.path or '/'} HTTP/1.0\r\n"
        f"Host: {url.hostname}\r\n"
        f"\r\n"
    )

    writer.write(query.encode('latin-1'))
    while True:
        line = await reader.readline()
        if not line:
            break

        line = line.decode('latin1').rstrip()
        if line:
            print(f'HTTP header> {line}')

    # Ignore the body, close the socket
    writer.close()
    await writer.wait_closed()

url = sys.argv[1]
asyncio.run(print_http_headers(url))
```

​	用法：

```
python example.py http://example.com/path/page.html
```

​	或使用 HTTPS:

```
python example.py https://example.com/path/page.html
```



### 注册一个打开的套接字以等待使用流的数据

​	使用 [`open_connection()`](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio.open_connection) 函数实现等待直到套接字接收到数据的协程:

```
import asyncio
import socket

async def wait_for_data():
    # 获取一个指向当前事件循环的引用
    # 因为我们想要访问低层级的 API。
    loop = asyncio.get_running_loop()

    # 创建一对已接连的套接字。
    rsock, wsock = socket.socketpair()

    # 注册打开的套接字以等待数据。
    reader, writer = await asyncio.open_connection(sock=rsock)

    # 模拟从网络接收数据
    loop.call_soon(wsock.send, 'abc'.encode())

    # 等待数据
    data = await reader.read(100)

    # 获得数据，我们已完成：关闭套接字
    print("Received:", data.decode())
    writer.close()
    await writer.wait_closed()

    # 关闭第二个套接字
    wsock.close()

asyncio.run(wait_for_data())
```

​	参见

 

​	使用低层级协议以及 [`loop.create_connection()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.loop.create_connection) 方法的 [注册一个打开的套接字以等待使用协议的数据](https://docs.python.org/zh-cn/3.13/library/asyncio-protocol.html#asyncio-example-create-connection) 示例。

​	使用低层级的 [`loop.add_reader()`](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio.loop.add_reader) 方法来监视文件描述符的 [监视文件描述符以读取事件](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio-example-watch-fd) 示例。
