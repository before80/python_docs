+++
title = "`xmlrpc.server` --- 基本 XML-RPC 服务器"
date = 2024-11-15T20:28:46+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/xmlrpc.server.html](https://docs.python.org/zh-cn/3.13/library/xmlrpc.server.html)
>
> 收录该文档的时间：`2024-11-15T20:28:46+08:00`

# `xmlrpc.server` --- 基本 XML-RPC 服务器

**源代码:** [Lib/xmlrpc/server.py](https://github.com/python/cpython/tree/3.13/Lib/xmlrpc/server.py)

------

[`xmlrpc.server`]({{< ref "/library/internet/xmlrpc_server#module-xmlrpc.server" >}}) 模块为以 Python 编写 XML-RPC 服务器提供了一个基本服务器框架。 服务器可以是独立的，使用 [`SimpleXMLRPCServer`]({{< ref "/library/internet/xmlrpc_server#xmlrpc.server.SimpleXMLRPCServer" >}})，或是嵌入某个 CGI 环境中，使用 [`CGIXMLRPCRequestHandler`]({{< ref "/library/internet/xmlrpc_server#xmlrpc.server.CGIXMLRPCRequestHandler" >}})。

​	警告

 

[`xmlrpc.server`]({{< ref "/library/internet/xmlrpc_server#module-xmlrpc.server" >}}) 模块对于恶意构建的数据是不安全的。 如果你需要解析不受信任或未经身份验证的数据，请参阅 [XML 漏洞]({{< ref "/library/markup/xml#xml-vulnerabilities" >}})。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

​	此模块在 WebAssembly 平台上无效或不可用。 请参阅 [WebAssembly 平台]({{< ref "/library/intro#wasm-availability" >}}) 了解详情。

## *class* xmlrpc.server.**SimpleXMLRPCServer**(*addr*, *requestHandler=SimpleXMLRPCRequestHandler*, *logRequests=True*, *allow_none=False*, *encoding=None*, *bind_and_activate=True*, *use_builtin_types=False*)

​	创建一个新的服务器实例。 这个类提供了一些用来注册可以被 XML-RPC 协议所调用的函数的方法。 *requestHandler* 形参应该是一个用于请求处理器实例的工厂函数；它默认为 [`SimpleXMLRPCRequestHandler`]({{< ref "/library/internet/xmlrpc_server#xmlrpc.server.SimpleXMLRPCRequestHandler" >}})。 *addr* 和 *requestHandler* 形参会被传给 [`socketserver.TCPServer`]({{< ref "/library/internet/socketserver#socketserver.TCPServer" >}}) 构造器。 如果 *logRequests* 为真值（默认），请求将被记录到日志；将此形参设为假值将关闭日志记录。 *allow_none* 和 *encoding* 形参会被传给 [`xmlrpc.client`]({{< ref "/library/internet/xmlrpc_client#module-xmlrpc.client" >}}) 并控制将从服务器返回的 XML-RPC 响应。 *bind_and_activate* 形参控制 `server_bind()` 和 `server_activate()` 是否会被构造器立即调用；它默认为真值。 将其设为假值将允许代码在地址被绑定之前操作 *allow_reuse_address* 类变量。 *use_builtin_types* 形参会被传给 [`loads()`]({{< ref "/library/internet/xmlrpc_client#xmlrpc.client.loads" >}}) 函数并控制当收到日期/时间值或二进制数据时将处理哪些类型；它默认为假值。

> 在 3.3 版本发生变更: 增加了 *use_builtin_types* 旗标。

## *class* xmlrpc.server.**CGIXMLRPCRequestHandler**(*allow_none=False*, *encoding=None*, *use_builtin_types=False*)

​	创建一个新的实例来处理 CGI 环境中的 XML-RPC 请求。 *allow_none* 和 *encoding* 形参会被传递给 [`xmlrpc.client`]({{< ref "/library/internet/xmlrpc_client#module-xmlrpc.client" >}}) 并控制将要从服务器返回的 XML-RPC 响应。 *use_builtin_types* 形参会被传递给 [`loads()`]({{< ref "/library/internet/xmlrpc_client#xmlrpc.client.loads" >}}) 函数并控制当接收到日期/时间值或二进制数据时要处理哪种类型；该形参默认为假值。

> 在 3.3 版本发生变更: 增加了 *use_builtin_types* 旗标。

## *class* xmlrpc.server.**SimpleXMLRPCRequestHandler**

​	创建一个新的请求处理器实例。 该请求处理器支持 `POST` 请求并会修改日志记录操作以便使用传递给 [`SimpleXMLRPCServer`]({{< ref "/library/internet/xmlrpc_server#xmlrpc.server.SimpleXMLRPCServer" >}}) 构造器形参的 *logRequests* 形参。



## SimpleXMLRPCServer 对象

[`SimpleXMLRPCServer`]({{< ref "/library/internet/xmlrpc_server#xmlrpc.server.SimpleXMLRPCServer" >}}) 类是基于 [`socketserver.TCPServer`]({{< ref "/library/internet/socketserver#socketserver.TCPServer" >}}) 并提供了一个创建简单、独立的 XML-RPC 服务器的方式。

## SimpleXMLRPCServer.**register_function**(*function=None*, *name=None*)

​	注册一个可以响应 XML-RPC 请求的函数。 如果给出了 *name*，它将成为与 *function* 相关联的方法名，否则将使用 [`function.__name__`]({{< ref "/reference/datamodel#function.__name__" >}})。 *name* 是一个字符串，并可能包含不能用于 Python 标识符的字符，包括句点符等。

​	此方法也可用作装饰器。 当被用作装饰器时，*name* 只能被指定为以 *name* 注册的 *function* 的一个关键字参数。 如果没有指定 *name*，则将使用 [`function.__name__`]({{< ref "/reference/datamodel#function.__name__" >}})。

> 在 3.7 版本发生变更: [`register_function()`]({{< ref "/library/internet/xmlrpc_server#xmlrpc.server.SimpleXMLRPCServer.register_function" >}}) 可被用作装饰器。

## SimpleXMLRPCServer.**register_instance**(*instance*, *allow_dotted_names=False*)

​	注册一个被用来公开未使用 [`register_function()`]({{< ref "/library/internet/xmlrpc_server#xmlrpc.server.SimpleXMLRPCServer.register_function" >}}) 注册的方法名的对象。 如果 *instance* 包含 `_dispatch()` 方法，它将被调用并附带被请求的方法名和来自请求的形参。 它的 API 是 `def _dispatch(self, method, params)` (请注意 *params* 并不表示变量参数列表)。 如果它调用了一个下层函数来执行任务，该函数将以 `func(*params)` 的形式被调用，即扩展了形参列表。 来自 `_dispatch()` 的返回值将作为结果被返回给客户端。 如果 *instance* 不包含 `_dispatch()` 方法，则会在其中搜索与被请求的方法名相匹配的属性。

​	如果可选的 *allow_dotted_names* 参数为真值且该实例没有 `_dispatch()` 方法，则如果被请求的方法名包含句点符，会单独搜索该方法名的每个组成部分，其效果就是执行了简单的分层级搜索。 通过搜索找到的值将随即附带来自请求的形参被调用，并且返回值会被回传给客户端。

​	警告

 

​	启用 *allow_dotted_names* 选项将允许入侵者访问你的模块的全局变量并可能允许入侵者在你的机器上执行任意代码。 仅可在安全、封闭的网络中使用此选项。

## SimpleXMLRPCServer.**register_introspection_functions**()

​	注册 XML-RPC 内省函数 `system.listMethods`, `system.methodHelp` 和 `system.methodSignature`。

## SimpleXMLRPCServer.**register_multicall_functions**()

​	注册 XML-RPC 多调用函数 system.multicall。

## SimpleXMLRPCRequestHandler.**rpc_paths**

​	一个必须为元组类型的属性值，其中列出所接收 XML-RPC 请求的有效路径部分。 发送到其他路径的请求将导致 404 "no such page" HTTP 错误。 如果此元组为空，则所有路径都将被视为有效。 默认值为 `('/', '/RPC2')`。



### SimpleXMLRPCServer 示例

​	服务器端代码:

```
from xmlrpc.server import SimpleXMLRPCServer
from xmlrpc.server import SimpleXMLRPCRequestHandler

# 限制为特定的路径。
class RequestHandler(SimpleXMLRPCRequestHandler):
    rpc_paths = ('/RPC2',)

# 创建服务器
with SimpleXMLRPCServer(('localhost', 8000),
                        requestHandler=RequestHandler) as server:
    server.register_introspection_functions()

    # 注册 pow() 函数；这将使用 pow.__name__ 的值
    # 作为名称，即 'pow'。
    server.register_function(pow)

    # 以不同的名称注册一个函数
    def adder_function(x, y):
        return x + y
    server.register_function(adder_function, 'add')

    # 注册一个实例；该实例的所有方法将发布为
    # XML-RPC 方法 (在本例中，即为 'mul')。
    class MyFuncs:
        def mul(self, x, y):
            return x * y

    server.register_instance(MyFuncs())

    # 运行服务器的主循环
    server.serve_forever()
```

​	以下客户端代码将调用上述服务器所提供的方法:

```
import xmlrpc.client

s = xmlrpc.client.ServerProxy('http://localhost:8000')
print(s.pow(2,3))  # 返回 2**3 = 8
print(s.add(2,3))  # 返回 5
print(s.mul(5,2))  # 返回 5*2 = 10

# 打印可用方法的列表
print(s.system.listMethods())
```

`register_function()` 也可被用作装饰器。 上述服务器端示例可以通过装饰器方式来注册函数:

```
from xmlrpc.server import SimpleXMLRPCServer
from xmlrpc.server import SimpleXMLRPCRequestHandler

class RequestHandler(SimpleXMLRPCRequestHandler):
    rpc_paths = ('/RPC2',)

with SimpleXMLRPCServer(('localhost', 8000),
                        requestHandler=RequestHandler) as server:
    server.register_introspection_functions()

    # 注册 pow() 函数；这将使用 pow.__name__ 的值
    # 作为名称，该值即为 'pow'.
    server.register_function(pow)

    # 以不同的名称注册一个函数，
    # 使用 register_function 作为装饰器。
    # *name* 只能作为关键字参数给出。
    @server.register_function(name='add')
    def adder_function(x, y):
        return x + y

    # 以 function.__name__ 为名称注册一个函数。
    @server.register_function
    def mul(x, y):
        return x * y

    server.serve_forever()
```

​	以下包括在 `Lib/xmlrpc/server.py` 模块中的例子演示了一个允许带点号名称并注册有多调用函数的服务器。

​	警告

 

​	启用 *allow_dotted_names* 选项将允许入侵者访问你的模块的全局变量并可能允许入侵者在你的机器上执行任意代码。 仅可在安全、封闭的网络中使用此示例。

```
import datetime

class ExampleService:
    def getData(self):
        return '42'

    class currentTime:
        @staticmethod
        def getCurrentTime():
            return datetime.datetime.now()

with SimpleXMLRPCServer(("localhost", 8000)) as server:
    server.register_function(pow)
    server.register_function(lambda x,y: x+y, 'add')
    server.register_instance(ExampleService(), allow_dotted_names=True)
    server.register_multicall_functions()
    print('Serving XML-RPC on localhost port 8000')
    try:
        server.serve_forever()
    except KeyboardInterrupt:
        print("\nKeyboard interrupt received, exiting.")
        sys.exit(0)
```

​	这个 ExampleService 演示程序可通过命令行发起调用:

```
python -m xmlrpc.server
```

​	可与上述服务器进行交互的客户端包括在 `Lib/xmlrpc/client.py` 中:

```
server = ServerProxy("http://localhost:8000")

try:
    print(server.currentTime.getCurrentTime())
except Error as v:
    print("ERROR", v)

multi = MultiCall(server)
multi.getData()
multi.pow(2,9)
multi.add(1,2)
try:
    for response in multi():
        print(response)
except Error as v:
    print("ERROR", v)
```

​	这个可与示例 XMLRPC 服务器进行交互的客户端的启动方式如下:

```
python -m xmlrpc.client
```

## CGIXMLRPCRequestHandler

[`CGIXMLRPCRequestHandler`]({{< ref "/library/internet/xmlrpc_server#xmlrpc.server.CGIXMLRPCRequestHandler" >}}) 类可被用来处理发送给 Python CGI 脚本的 XML-RPC 请求。

## CGIXMLRPCRequestHandler.**register_function**(*function=None*, *name=None*)

​	注册一个可以响应 XML-RPC 请求的函数。 如果给出了 *name*，它将成为与 *function* 相关联的方法名，否则将使用 [`function.__name__`]({{< ref "/reference/datamodel#function.__name__" >}})。 *name* 是一个字符串，并可能包含不能用于 Python 标识符的字符，包括句点符等。

​	此方法也可用作装饰器。 当被用作装饰器时，*name* 只能被指定为以 *name* 注册的 *function* 的一个关键字参数。 如果没有指定 *name*，则将使用 [`function.__name__`]({{< ref "/reference/datamodel#function.__name__" >}})。

> 在 3.7 版本发生变更: [`register_function()`]({{< ref "/library/internet/xmlrpc_server#xmlrpc.server.CGIXMLRPCRequestHandler.register_function" >}}) 可被用作装饰器。

## CGIXMLRPCRequestHandler.**register_instance**(*instance*)

​	注册一个对象用来公开未使用 [`register_function()`]({{< ref "/library/internet/xmlrpc_server#xmlrpc.server.CGIXMLRPCRequestHandler.register_function" >}}) 进行注册的方法名。 如果实例包含 `_dispatch()` 方法，它会附带所请求的方法名和来自请求的形参被调用；返回值会作为结果被返回给客户端。 如果实例不包含 `_dispatch()` 方法，则在其中搜索与所请求方法名相匹配的属性；如果所请求方法名包含句点，则会分别搜索方法名的每个部分，其效果就是执行了简单的层级搜索。 搜索找到的值将附带来自请求的形参被调用，其返回值会被返回给客户端。

## CGIXMLRPCRequestHandler.**register_introspection_functions**()

​	注册 XML-RPC 内海函数 `system.listMethods`, `system.methodHelp` 和 `system.methodSignature`。

## CGIXMLRPCRequestHandler.**register_multicall_functions**()

​	注册 XML-RPC 多调用函数 `system.multicall`。

## CGIXMLRPCRequestHandler.**handle_request**(*request_text=None*)

​	处理一个 XML-RPC 请求。 如果给出了 *request_text*，它应当是 HTTP 服务器所提供的 POST 数据，否则将使用 stdin 的内容。

​	示例:

```
class MyFuncs:
    def mul(self, x, y):
        return x * y


handler = CGIXMLRPCRequestHandler()
handler.register_function(pow)
handler.register_function(lambda x,y: x+y, 'add')
handler.register_introspection_functions()
handler.register_instance(MyFuncs())
handler.handle_request()
```

## 文档 XMLRPC 服务器

​	这些类扩展了上面的类以发布响应 HTTP GET 请求的 HTML 文档。 服务器可以是独立的，使用 [`DocXMLRPCServer`]({{< ref "/library/internet/xmlrpc_server#xmlrpc.server.DocXMLRPCServer" >}})，或是嵌入某个 CGI 环境中，使用 [`DocCGIXMLRPCRequestHandler`]({{< ref "/library/internet/xmlrpc_server#xmlrpc.server.DocCGIXMLRPCRequestHandler" >}})。

## *class* xmlrpc.server.**DocXMLRPCServer**(*addr*, *requestHandler=DocXMLRPCRequestHandler*, *logRequests=True*, *allow_none=False*, *encoding=None*, *bind_and_activate=True*, *use_builtin_types=True*)

​	创建一个新的服务器实例。 所有形参的含义与 [`SimpleXMLRPCServer`]({{< ref "/library/internet/xmlrpc_server#xmlrpc.server.SimpleXMLRPCServer" >}}) 的相同；*requestHandler* 默认为 [`DocXMLRPCRequestHandler`]({{< ref "/library/internet/xmlrpc_server#xmlrpc.server.DocXMLRPCRequestHandler" >}})。

> 在 3.3 版本发生变更: 增加了 *use_builtin_types* 旗标。

## *class* xmlrpc.server.**DocCGIXMLRPCRequestHandler**

​	创建一个新的实例来处理 CGI 环境中的 XML-RPC 请求。

## *class* xmlrpc.server.**DocXMLRPCRequestHandler**

​	创建一个新的请求处理器实例。 该请求处理器支持 XML-RPC POST 请求、文档 GET 请求并会修改日志记录操作以便使用传递给 [`DocXMLRPCServer`]({{< ref "/library/internet/xmlrpc_server#xmlrpc.server.DocXMLRPCServer" >}}) 构造器形参的 *logRequests* 形参。



## DocXMLRPCServer 对象

[`DocXMLRPCServer`]({{< ref "/library/internet/xmlrpc_server#xmlrpc.server.DocXMLRPCServer" >}}) 类派生自 [`SimpleXMLRPCServer`]({{< ref "/library/internet/xmlrpc_server#xmlrpc.server.SimpleXMLRPCServer" >}}) 并提供了一种创建自动记录文档的、独立的 XML-RPC 服务器的方式。 HTTP POST 请求将作为 XML-RPC 方法调用来处理。 HTTP GET 请求将通过生成 pydoc 风格的 HTML 文档来处理。 这将允许服务器自己提供基于 Web 的文档。

## DocXMLRPCServer.**set_server_title**(*server_title*)

​	设置所生成 HTML 文档要使用的标题。 此标题将在 HTML "title" 元素中使用。

## DocXMLRPCServer.**set_server_name**(*server_name*)

​	设置所生成 HTML 文档要使用的名称。 此名称将出现在所生成文档顶部的 "h1" 元素中。

## DocXMLRPCServer.**set_server_documentation**(*server_documentation*)

​	设置所生成Set the description used in the generated HTML 文档要使用的描述。 此描述将显示为文档中的一个段落，位于服务器名称之下。

## DocCGIXMLRPCRequestHandler

[`DocCGIXMLRPCRequestHandler`]({{< ref "/library/internet/xmlrpc_server#xmlrpc.server.DocCGIXMLRPCRequestHandler" >}}) 类派生自 [`CGIXMLRPCRequestHandler`]({{< ref "/library/internet/xmlrpc_server#xmlrpc.server.CGIXMLRPCRequestHandler" >}}) 并提供了一种创建自动记录文档的 XML-RPC CGI 脚本的方式。 HTTP POST 请求将作为 XML-RPC 方法调用来处理。 HTTP GET 请求将通过生成 pydoc 风格的 HTML 文档来处理。 这将允许服务器自己提供基于 Web 的文档。

## DocCGIXMLRPCRequestHandler.**set_server_title**(*server_title*)

​	设置所生成 HTML 文档要使用的标题。 此标题将在 HTML "title" 元素中使用。

## DocCGIXMLRPCRequestHandler.**set_server_name**(*server_name*)

​	设置所生成 HTML 文档要使用的名称。 此名称将出现在所生成文档顶部的 "h1" 元素中。

## DocCGIXMLRPCRequestHandler.**set_server_documentation**(*server_documentation*)

​	设置所生成Set the description used in the generated HTML 文档要使用的描述。 此描述将显示为文档中的一个段落，位于服务器名称之下。
