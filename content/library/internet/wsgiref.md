+++
title = "wsgiref --- WSGI 工具和参考实现"
date = 2024-11-15T20:28:46+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/wsgiref.html](https://docs.python.org/zh-cn/3.13/library/wsgiref.html)
>
> 收录该文档的时间：`2024-11-15T20:28:46+08:00`

# `wsgiref` --- WSGI 工具和参考实现

**源代码:** [Lib/wsgiref](https://github.com/python/cpython/tree/3.13/Lib/wsgiref)

------

​	Web 服务器网关接口（WSGI）是 Web 服务器软件和用 Python 编写的 Web 应用程序之间的标准接口。 具有标准接口能够让支持 WSGI 的应用程序与多种不同的 Web 服务器配合使用。

​	只有 Web 服务器和编程框架的开发者才需要了解 WSGI 设计的每个细节和边界情况。 你不需要了解 WSGI 的每个细节而只需要安装一个 WSGI 应用程序或编写使用现有框架的 Web 应用程序。

[`wsgiref`]({{< ref "/library/internet/wsgiref#module-wsgiref" >}}) 是 WSGI 规范的一个参考实现，可被用于将 WSGI 支持添加到 Web 服务器或框架中。 它提供了操作 WSGI 环境变量和响应标头的工具，实现 WSGI 服务器的基类，可部署 WSGI 应用程序的演示性 HTTP 服务器，用于静态类型检查的类型，以及一个用于检查 WSGI 服务器和应用程序是否符合 WSGI 规范的验证工具 ([**PEP 3333**](https://peps.python.org/pep-3333/))。

​	参看 [wsgi.readthedocs.io](https://wsgi.readthedocs.io/) 获取有关 WSGI 的更多信息，以及教程和其他资源的链接。



## [`wsgiref.util`]({{< ref "/library/internet/wsgiref#module-wsgiref.util" >}}) -- WSGI 环境工具

​	本模块提供了多种工具函数配合 WSGI 环境使用。 WSGI 环境就是一个包含 [**PEP 3333**](https://peps.python.org/pep-3333/) 所描述的 HTTP 请求变量的字典。 所有接受 *environ* 形参的函数都会预期得到一个符合 WSGI 规范的字典；请参阅 [**PEP 3333**](https://peps.python.org/pep-3333/) 来了解相关规范的细节并请参阅 [`WSGIEnvironment`]({{< ref "/library/internet/wsgiref#wsgiref.types.WSGIEnvironment" >}}) 来了解可被用于类型标注的类型别名。

## wsgiref.util.**guess_scheme**(*environ*)

​	返回对于 `wsgi.url_scheme` 应为 "http" 还是 "https" 的猜测，具体方式是在 *environ* 中检查 `HTTPS` 环境变量。 返回值是一个字符串。

​	此函数适用于创建一个包装了 CGI 或 CGI 类协议例如 FastCGI 的网关。 通常，提供这种协议的服务器将包括一个 `HTTPS` 变量并会在通过 SSL 接收请求时将其值设为 "1", "yes" 或 "on"。 这样，此函数会在找到上述值时返回 "https"，否则返回 "http"。

## wsgiref.util.**request_uri**(*environ*, *include_query=True*)

​	使用 [**PEP 3333**](https://peps.python.org/pep-3333/) 的 "URL Reconstruction" 一节中的算法返回完整的请求 URL，可能包括查询字符串。 如果 *include_query* 为假值，则结果 URI 中将不包括查询字符串。

## wsgiref.util.**application_uri**(*environ*)

​	类似于 [`request_uri()`]({{< ref "/library/internet/wsgiref#wsgiref.util.request_uri" >}})，区别在于 `PATH_INFO` 和 `QUERY_STRING` 变量会被忽略。 结果为请求所指定的应用程序对象的基准 URI。

## wsgiref.util.**shift_path_info**(*environ*)

​	将单个名称从 `PATH_INFO` 变换为 `SCRIPT_NAME` 并返回该名称。 *environ* 目录将被原地 *修改*；如果你需要保留原始 `PATH_INFO` 或 `SCRIPT_NAME` 不变请使用一个副本。

​	如果 `PATH_INFO` 中没有剩余的路径节，则返回 `None`。

​	通常，此例程被用来处理请求 URI 路径的每个部分，比如说将路径当作是一系列字典键。 此例程会修改传入的环境以使其适合发起调用位于目标 URI 上的其他 WSGI 应用程序，如果有一个 WSGI 应用程序位于 `/foo`，而请求 URI 路径为 `/foo/bar/baz`，且位于 `/foo` 的 WSGI 应用程序调用了 [`shift_path_info()`]({{< ref "/library/internet/wsgiref#wsgiref.util.shift_path_info" >}})，它将获得字符串 "bar"，而环境将被更新以适合传递给位于 `/foo/bar` 的 WSGI 应用程序。 也就是说，`SCRIPT_NAME` 将由 `/foo` 变为 `/foo/bar`，而 `PATH_INFO` 将由 `/bar/baz` 变为 `/baz`。

​	当 `PATH_INFO` 只是 "/" 时，此例程会返回一个空字符串并在 `SCRIPT_NAME` 末尾添加一个斜杠，虽然空的路径节通常会被忽略，并且 `SCRIPT_NAME` 通常也不以斜杠作为结束。 此行为是有意为之的，用来确保应用程序在使用此例程执行对象遍历时能区分以 `/x` 结束的和以 `/x/` 结束的 URI。

## wsgiref.util.**setup_testing_defaults**(*environ*)

​	以简短的默认值更新 *environ* 用于测试目的。

​	此例程会添加 WSGI 所需要的各种参数，包括 `HTTP_HOST`, `SERVER_NAME`, `SERVER_PORT`, `REQUEST_METHOD`, `SCRIPT_NAME`, `PATH_INFO` 以及 [**PEP 3333**](https://peps.python.org/pep-3333/) 中定义的所有 `wsgi.*` 变量。 它只提供默认值，而不会替换这些变量的现有设置。

​	此例程的目的是让 WSGI 服务器的单元测试以及应用程序设置测试环境更为容易。 它不应该被实际的 WSGI 服务器或应用程序所使用，因为它用的是假数据！

​	用法示例:

```
from wsgiref.util import setup_testing_defaults
from wsgiref.simple_server import make_server

# 一个相对简单的 WSGI 应用程序。 它将打印出
# 由 setup_testing_defaults 更新之后的环境字典
def simple_app(environ, start_response):
    setup_testing_defaults(environ)

    status = '200 OK'
    headers = [('Content-type', 'text/plain; charset=utf-8')]

    start_response(status, headers)

    ret = [("%s: %s\n" % (key, value)).encode("utf-8")
           for key, value in environ.items()]
    return ret

with make_server('', 8000, simple_app) as httpd:
    print("Serving on port 8000...")
    httpd.serve_forever()
```

​	除了上述的环境函数，[`wsgiref.util`]({{< ref "/library/internet/wsgiref#module-wsgiref.util" >}}) 模块还提供了以下辅助工具:

## wsgiref.util.**is_hop_by_hop**(*header_name*)

​	如果 'header_name' 是 [**RFC 2616**](https://datatracker.ietf.org/doc/html/rfc2616.html) 所定义的 HTTP/1.1 "Hop-by-Hop" 标头则返回 `True`。

## *class* wsgiref.util.**FileWrapper**(*filelike*, *blksize=8192*)

​	一个 [`wsgiref.types.FileWrapper`]({{< ref "/library/internet/wsgiref#wsgiref.types.FileWrapper" >}}) 协议的具体实现被用来将文件型对象转换为 [iterator]({{< ref "/glossary/idx#term-iterator" >}})。 结果对象将为 [iterable]({{< ref "/glossary/idx#term-iterable" >}})。 当对象被迭代时，可选的 *blksize* 形参将被反复地传给 *filelike* 对象的 `read()` 方法以获取字节器并产生输出。 当 `read()` 返回空字节串时，迭代将结束并且不可再恢复。

​	如果 *filelike* 具有 `close()` 方法，返回的对象也将具有 `close()` 方法，并且它将在被调用时发起调用 *filelike* 对象的 `close()` 方法。

​	用法示例:

```
from io import StringIO
from wsgiref.util import FileWrapper

# 我们使用一个 StringIO 缓冲区作为文件型对象
filelike = StringIO("This is an example file-like object"*10)
wrapper = FileWrapper(filelike, blksize=5)

for chunk in wrapper:
    print(chunk)
```

> 在 3.11 版本发生变更: 对 [`__getitem__()`]({{< ref "/reference/datamodel#object.__getitem__" >}}) 方法的支持已被移除。



## [`wsgiref.headers`]({{< ref "/library/internet/wsgiref#module-wsgiref.headers" >}}) -- WSGI 响应标头工具

​	此模块提供了一个单独的类 [`Headers`]({{< ref "/library/internet/wsgiref#wsgiref.headers.Headers" >}})，可以方便地使用一个映射类接口操作 WSGI 响应标头。

## *class* wsgiref.headers.**Headers**([*headers*])

​	创建一个包装了 *headers* 的映射类对象，它必须为如 [**PEP 3333**](https://peps.python.org/pep-3333/) 所描述的由标头名称/值元组构成的列表。 *headers* 的默认值为一个空列表。

[`Headers`]({{< ref "/library/internet/wsgiref#wsgiref.headers.Headers" >}}) 对象支持典型的映射操作包括 [`__getitem__()`]({{< ref "/reference/datamodel#object.__getitem__" >}}), [`get()`]({{< ref "/library/stdtypes#dict.get" >}}), [`__setitem__()`]({{< ref "/reference/datamodel#object.__setitem__" >}}), [`setdefault()`]({{< ref "/library/stdtypes#dict.setdefault" >}}), [`__delitem__()`]({{< ref "/reference/datamodel#object.__delitem__" >}}) 和 [`__contains__()`]({{< ref "/reference/datamodel#object.__contains__" >}})。 对于以上各个方法，映射的键都是标头名称（大小写不敏感），而值则是关联到该标头名称的第一个值。 设置一个标头会移除该标头的任何现有值，再将一个新值添加到所包装的标头列表末尾。 标头的现有顺序通常会保持不变，新的标头会被添加到所包装的列表末尾。

​	与字典不同，当你试图获取或删除所包装的标头列表中不存在的键时 [`Headers`]({{< ref "/library/internet/wsgiref#wsgiref.headers.Headers" >}}) 对象不会引发错误。 获取一个不存在的标头只是返回 `None`，而删除一个不存在的标头则没有任何影响。

[`Headers`]({{< ref "/library/internet/wsgiref#wsgiref.headers.Headers" >}}) 对象还支持 `keys()`, `values()` 以及 `items()` 方法。 如果存在具有多个值的键则 `keys()` 和 `items()` 所返回的列表可能包括多个相同的键。 对 [`Headers`]({{< ref "/library/internet/wsgiref#wsgiref.headers.Headers" >}}) 对象执行 `len()` 的结果与 `items()` 的长度相同，也与所包装的标头列表的长度相同。 实际上，`items()` 方法只是返回所包装的标头列表的一个副本。

​	在 [`Headers`]({{< ref "/library/internet/wsgiref#wsgiref.headers.Headers" >}}) 对象上调用 `bytes()` 将返回适用于作为 HTTP 响应标头来传输的已格式化字节串。 每个标头附带以逗号加空格分隔的值放置在一行中。 每一行都以回车符加换行符结束，且该字节串会以一个空行作为结束。

​	除了映射接口和格式化特性，[`Headers`]({{< ref "/library/internet/wsgiref#wsgiref.headers.Headers" >}}) 对象还具有下列方法用来查询和添加多值标头，以及添加具有 MIME 参数的标头:

## **get_all**(*name*)

​	返回包含指定标头的所有值的列表。

​	返回的列表项将按它们在原始标头列表中的出现或被添加到实例中的顺序排序，并可能包含重复项。 任何被删除并重新插入的字段总是会被添加到标头列表末尾。 如果给定名称的字段不存在，则返回一个空列表。

## **add_header**(*name*, *value*, ***_params*)

​	添加一个（可能有多个值）标头，带有通过关键字参数指明的可选的 MIME 参数。

*name* 是要添加的标头字段。 可以使用关键字参数来为标头字段设置 MIME 参数。 每个参数必须为字符串或 `None`。 参数名中的下划线会被转换为连字符，因为连字符不可在 Python 标识符中出现，但许多 MIME 参数名都包括连字符。 如果参数值为字符串，它会以 `name="value"` 的形式被添加到标头值参数中。 如果为 `None`，则只会添加参数名。 （这适用于没有值的 MIME 参数。） 示例用法:

```
h.add_header('content-disposition', 'attachment', filename='bud.gif')
```

​	以上代码将添加一个这样的标头:

```
Content-Disposition: attachment; filename="bud.gif"
```

> 在 3.5 版本发生变更: *headers* 形参是可选的。



## [`wsgiref.simple_server`]({{< ref "/library/internet/wsgiref#module-wsgiref.simple_server" >}}) -- 一个简单的 WSGI HTTP 服务器

​	此模块实现了一个简单的 HTTP 服务器 (基于 [`http.server`]({{< ref "/library/internet/http_server#module-http.server" >}})) 来发布 WSGI 应用程序。 每个服务器实例会在给定的主机名和端口号上发布一个 WSGI 应用程序。 如果你想在一个主机名和端口号上发布多个应用程序，你应当创建一个通过解析 `PATH_INFO` 来选择每个请求要发起调用哪个应用程序的 WSGI 应用程序。 (例如，使用 [`wsgiref.util`]({{< ref "/library/internet/wsgiref#module-wsgiref.util" >}}) 中的 `shift_path_info()` 函数。)

## wsgiref.simple_server.**make_server**(*host*, *port*, *app*, *server_class=WSGIServer*, *handler_class=WSGIRequestHandler*)

​	创建一个新的 WSGI 服务器并在 *host* 和 *port* 上进行监听，接受对 *app* 的连接。 返回值是所提供的 *server_class* 的实例，并将使用指定的 *handler_class* 来处理请求。 *app* 必须是一个如 [**PEP 3333**](https://peps.python.org/pep-3333/) 所定义的 WSGI 应用程序对象。

​	用法示例:

```
from wsgiref.simple_server import make_server, demo_app

with make_server('', 8000, demo_app) as httpd:
    print("Serving HTTP on port 8000...")

    # 响应请求直到进程被杀死
    httpd.serve_forever()

    # 或者：服务一次请求，然后退出
    httpd.handle_request()
```

## wsgiref.simple_server.**demo_app**(*environ*, *start_response*)

​	此函数是一个小巧但完整的 WSGI 应用程序，它返回一个包含消息 "Hello world!" 以及 *environ* 形参中提供的键/值对的文本页面。 它适用于验证 WSGI 服务器 (例如 [`wsgiref.simple_server`]({{< ref "/library/internet/wsgiref#module-wsgiref.simple_server" >}})) 是否能够正确地运行一个简单的 WSGI 应用程序。

## *class* wsgiref.simple_server.**WSGIServer**(*server_address*, *RequestHandlerClass*)

​	创建一个 [`WSGIServer`]({{< ref "/library/internet/wsgiref#wsgiref.simple_server.WSGIServer" >}}) 实例。 *server_address* 应当是一个 `(host,port)` 元组，而 *RequestHandlerClass* 应当是 [`http.server.BaseHTTPRequestHandler`]({{< ref "/library/internet/http_server#http.server.BaseHTTPRequestHandler" >}}) 的子类，它将被用来处理请求。

​	你通常不需要调用此构造器，因为 [`make_server()`]({{< ref "/library/internet/wsgiref#wsgiref.simple_server.make_server" >}}) 函数能为你处理所有的细节。

[`WSGIServer`]({{< ref "/library/internet/wsgiref#wsgiref.simple_server.WSGIServer" >}}) 是 [`http.server.HTTPServer`]({{< ref "/library/internet/http_server#http.server.HTTPServer" >}}) 的子类，因此它所有的方法 (例如 `serve_forever()` 和 `handle_request()`) 都是可用的。 [`WSGIServer`]({{< ref "/library/internet/wsgiref#wsgiref.simple_server.WSGIServer" >}}) 还提供了以下 WSGI 专属的方法:

## **set_app**(*application*)

​	将可调用对象 *application* 设为将要接受请求的 WSGI 应用程序。

## **get_app**()

​	返回当前设置的应用程序可调用对象。

​	但是，你通常不需要使用这些附加的方法，因为 [`set_app()`]({{< ref "/library/internet/wsgiref#wsgiref.simple_server.WSGIServer.set_app" >}}) 通常会由 [`make_server()`]({{< ref "/library/internet/wsgiref#wsgiref.simple_server.make_server" >}}) 来调用，而 [`get_app()`]({{< ref "/library/internet/wsgiref#wsgiref.simple_server.WSGIServer.get_app" >}}) 主要是针对请求处理器实例的。

## *class* wsgiref.simple_server.**WSGIRequestHandler**(*request*, *client_address*, *server*)

​	为给定的 *request* 创建一个 HTTP 处理器 (例如套接字)，*client_address* (一个 `(host,port)` 元组)，以及 *server* ([`WSGIServer`]({{< ref "/library/internet/wsgiref#wsgiref.simple_server.WSGIServer" >}}) 实例)。

​	你不需要直接创建该类的实例；它们会根据 [`WSGIServer`]({{< ref "/library/internet/wsgiref#wsgiref.simple_server.WSGIServer" >}}) 对象的需要自动创建。 但是，你可以子类化该类并将其作为 *handler_class* 提供给 [`make_server()`]({{< ref "/library/internet/wsgiref#wsgiref.simple_server.make_server" >}}) 函数。 一些可能在子类中重载的相关方法:

## **get_environ**()

​	返回对应于一个请求的 [`WSGIEnvironment`]({{< ref "/library/internet/wsgiref#wsgiref.types.WSGIEnvironment" >}}) 字典。 默认实现会拷贝 [`WSGIServer`]({{< ref "/library/internet/wsgiref#wsgiref.simple_server.WSGIServer" >}}) 对象的 `base_environ` 字典属性的内容然后添加从 HTTP 请求获取的各种标头。 对此方法的每次调用都应当返回一个新的包含 [**PEP 3333**](https://peps.python.org/pep-3333/) 所规定的所有相关 CGI 环境变量的字典。

## **get_stderr**()

​	返回应被用作 `wsgi.errors` 流的对象。 默认实现将只返回 `sys.stderr`。

## **handle**()

​	处理 HTTP 请求。 默认的实现会使用 [`wsgiref.handlers`]({{< ref "/library/internet/wsgiref#module-wsgiref.handlers" >}}) 类创建一个处理器实例来实现实际的 WSGI 应用程序接口。



## [`wsgiref.validate`]({{< ref "/library/internet/wsgiref#module-wsgiref.validate" >}}) --- WSGI 一致性检查器

​	当创建新的 WSGI 应用程序对象、框架、服务器或中间件时，使用 [`wsgiref.validate`]({{< ref "/library/internet/wsgiref#module-wsgiref.validate" >}}) 来验证新代码的一致性是很有用的。 此模块提供了一个创建 WSGI 应用程序对象的函数来验证 WSGI 服务器或网关与 WSGI 应用程序对象之间的通信，以便检查双方的协议一致性。

​	请注意这个工具并不保证完全符合 [**PEP 3333**](https://peps.python.org/pep-3333/)；此模块没有报告错误并不一定表示不存在错误。 但是，如果此模块确实报告了错误，那么几乎可以肯定服务器或应用程序不是 100% 符合要求的。

​	此模块是基于 Ian Bicking 的 "Python Paste" 库的 `paste.lint` 模块。

## wsgiref.validate.**validator**(*application*)

​	包装 *application* 并返回一个新的 WSGI 应用程序对象。 返回的应用程序将转发所有请求到原始的 *application*，并将检查 *application* 和发起调用它的服务器是否都符合 WSGI 规范和 [**RFC 2616**](https://datatracker.ietf.org/doc/html/rfc2616.html)。

​	任何被检测到的不一致性都会导致引发 [`AssertionError`]({{< ref "/library/exceptions#AssertionError" >}})；但是请注意，如何对待这些错误取决于具体服务器。 例如，[`wsgiref.simple_server`]({{< ref "/library/internet/wsgiref#module-wsgiref.simple_server" >}}) 和其他基于 [`wsgiref.handlers`]({{< ref "/library/internet/wsgiref#module-wsgiref.handlers" >}}) 的服务器（它们没有重载错误处理方法来做其他操作）将简单地输出一条消息报告发生了错误，并将回溯转给 `sys.stderr` 或者其他错误数据流。

​	这个包装器也可能会使用 [`warnings`]({{< ref "/library/python/warnings#module-warnings" >}}) 模块生成输出来指明存在问题但实际上未被 [**PEP 3333**](https://peps.python.org/pep-3333/) 所禁止的行为。 除非它们被 Python 命令行选项或 [`warnings`]({{< ref "/library/python/warnings#module-warnings" >}}) API 所屏蔽，否则任何此类警告都将被写入到 `sys.stderr` (*不是* `wsgi.errors`，除非它们恰好是同一个对象)。

​	用法示例:

```
from wsgiref.validate import validator
from wsgiref.simple_server import make_server

# 我们的可调用对象与标准是故意不兼容的，
# 因此验证器将会出错
def simple_app(environ, start_response):
    status = '200 OK'  # HTTP 状态
    headers = [('Content-type', 'text/plain')]  # HTTP 标头
    start_response(status, headers)

    # 这将会出错因为我们需要返回一个列表，
    # 验证器将会提醒我们
    return b"Hello World"

# 这是包装在验证器中国应用程序
validator_app = validator(simple_app)

with make_server('', 8000, validator_app) as httpd:
    print("Listening on port 8000....")
    httpd.serve_forever()
```



## [`wsgiref.handlers`]({{< ref "/library/internet/wsgiref#module-wsgiref.handlers" >}}) -- 服务器/网关基类

​	此模块提供了用于实现 WSGI 服务器和网关的处理器基类。 这些基类可处理大部分与 WSGI 应用程序通信的工作，只要给予它们一个带有输入、输出和错误流的 CGI 类环境。

## *class* wsgiref.handlers.**CGIHandler**

​	通过 `sys.stdin`, `sys.stdout`, `sys.stderr` 和 `os.environ` 发起基于 CGI 的调用。 这在当你有一个 WSGI 应用程序并想将其作为 CGI 脚本运行时很有用处。 只需发起调用 `CGIHandler().run(app)`，其中 `app` 是你想要发起调用的 WSGI 应用程序。

​	该类是 [`BaseCGIHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseCGIHandler" >}}) 的子类，它将设置 `wsgi.run_once` 为真值，`wsgi.multithread` 为假值，`wsgi.multiprocess` 为真值，并总是使用 [`sys`]({{< ref "/library/python/sys#module-sys" >}}) 和 [`os`]({{< ref "/library/allos/os#module-os" >}}) 来获取所需的 CGI 流和环境。

## *class* wsgiref.handlers.**IISCGIHandler**

[`CGIHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.CGIHandler" >}}) 的一个专门化替代，用于在 Microsoft 的 IIS Web 服务器上部署，无需设置 config allowPathInfo 选项 (IIS>=7) 或 metabase allowPathInfoForScriptMappings (IIS<7)。

​	默认情况下，IIS 给出的 `PATH_INFO` 会与前面的 `SCRIPT_NAME` 重复，导致想要实现路由的 WSGI 应用程序出现问题。 这个处理器会去除任何这样的重复路径。

​	IIS 可被配置为传递正确的 `PATH_INFO`，但这会导致另一个 `PATH_TRANSLATED` 出错的问题。 幸运的是这个变量很少被使用并且不被 WSGI 所保证。 但是在 IIS<7 上，这个设置只能在 vhost 层级上进行，影响到所有其他脚本映射，其中许多在受 `PATH_TRANSLATED` 问题影响时都会中断运行。 因此 IIS<7 的部署几乎从不附带这样的修正（即使 IIS7 也很少使用它，因为它仍然不带 UI）。

​	CGI 代码没有办法确定该选项是否已设置，因此提供了一个单独的处理器类。 它的用法与 [`CGIHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.CGIHandler" >}}) 相同，即通过调用 `IISCGIHandler().run(app)`，其中 `app` 是你想要发起调用的 WSGI 应用程序。

> Added in version 3.2.
>

## *class* wsgiref.handlers.**BaseCGIHandler**(*stdin*, *stdout*, *stderr*, *environ*, *multithread=True*, *multiprocess=False*)

​	类似于 [`CGIHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.CGIHandler" >}})，但是改用 [`sys`]({{< ref "/library/python/sys#module-sys" >}}) 和 [`os`]({{< ref "/library/allos/os#module-os" >}}) 模块，CGI 环境和 I/O 流会被显式地指定。 *multithread* 和 *multiprocess* 值被用来为处理器实例所运行的任何应用程序设置 `wsgi.multithread` 和 `wsgi.multiprocess` 旗标。

​	该类是 [`SimpleHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.SimpleHandler" >}}) 的子类，旨在用于 HTTP "原始服务器" 以外的软件。 如果你在编写一个网关协议实现（例如 CGI, FastCGI, SCGI 等等），它使用 `Status:` 标头来发布 HTTP 状态，你可能会想要子类化该类而不是 [`SimpleHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.SimpleHandler" >}})。

## *class* wsgiref.handlers.**SimpleHandler**(*stdin*, *stdout*, *stderr*, *environ*, *multithread=True*, *multiprocess=False*)

​	类似于 [`BaseCGIHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseCGIHandler" >}})，但被设计用于 HTTP 原始服务器。 如果你在编写一个 HTTP 服务器实现，你可能会想要子类化该类而不是 [`BaseCGIHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseCGIHandler" >}})。

​	该类是 [`BaseHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler" >}}) 的子类。 它重载了 `__init__()`, [`get_stdin()`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler.get_stdin" >}}), [`get_stderr()`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler.get_stderr" >}}), [`add_cgi_vars()`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler.add_cgi_vars" >}}), [`_write()`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler._write" >}}) 和 [`_flush()`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler._flush" >}}) 方法以支持通过构造器显式地设置环境和流。 所提供的环境和流存储在 `stdin`, `stdout`, `stderr` 和 `environ` 属性中。

*stdout* 的 [`write()`]({{< ref "/library/allos/io#io.BufferedIOBase.write" >}}) 方法应该完整写入每个数据块，与 [`io.BufferedIOBase`]({{< ref "/library/allos/io#io.BufferedIOBase" >}}) 一样。

## *class* wsgiref.handlers.**BaseHandler**

​	这是适用于运行 WSGI 应用程序的抽象基类。 每个实例将处理一个单独的 HTTP 请求，不过在原则上你也可以创建一个可针对多个请求重用的子类。

[`BaseHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler" >}}) 实例只有一个方法提供给外部使用:

## **run**(*app*)

​	运行指定的 WSGI 应用程序 *app*。

​	所有的 [`BaseHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler" >}}) 其他方法都是在运行应用程序过程中由该方法发起调用的，因此主要是为了允许定制运行过程。

​	以下方法必须在子类中被重载:

## **_write**(*data*)

​	缓冲字节数据 *data* 以便传输给客户端。 如果此方法真的传输了数据也是可以的；[`BaseHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler" >}}) 只有在底层系统真有这样的区分时才会区分写入和刷新操作以提高效率。

## **_flush**()

​	强制将缓冲的数据传输给客户端。 如果此方法无任何操作也是可以的（例如，[`_write()`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler._write" >}}) 实际上已发送了数据）。

## **get_stdin**()

​	返回一个兼容 [`InputStream`]({{< ref "/library/internet/wsgiref#wsgiref.types.InputStream" >}}) 的适合用作当前所处理请求的 `wsgi.input` 的对象。

## **get_stderr**()

​	返回一个兼容 [`ErrorStream`]({{< ref "/library/internet/wsgiref#wsgiref.types.ErrorStream" >}}) 的适合用作当前所处理请求的 `wsgi.errors` 的对象。

## **add_cgi_vars**()

​	将当前请求的 CGI 变量插入到 `environ` 属性。

​	以下是一些你可能会想要重载的其他方法。 但这只是个简略的列表，它不包括每个可被重载的方法。 你应当在在尝试创建自定义的 [`BaseHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler" >}}) 子类之前参阅文档字符串和源代码来了解更多信息。

​	用于自定义 WSGI 环境的属性和方法:

## **wsgi_multithread**

​	用于 `wsgi.multithread` 环境变量的值。 它在 [`BaseHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler" >}}) 中默认为真值，但在其他子类中可能有不同的默认值（或是由构造器来设置）。

## **wsgi_multiprocess**

​	用于 `wsgi.multiprocess` 环境变量的值。 它在 [`BaseHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler" >}}) 中默认为真值，但在其他子类中可能有不同的默认值（或是由构造器来设置）。

## **wsgi_run_once**

​	用于 `wsgi.run_once` 环境变量的值。 它在 [`BaseHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler" >}}) 中默认为假值，但在 [`CGIHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.CGIHandler" >}}) 中默认为真值。

## **os_environ**

​	要包括在每个请求的 WSGI 环境中的默认环境变量。 在默认情况下，这是当 [`wsgiref.handlers`]({{< ref "/library/internet/wsgiref#module-wsgiref.handlers" >}}) 被导入时的 `os.environ` 的一个副本，但也可以在类或实例层级上创建它们自己的子类。 请注意该字典应当被当作是只读的，因为默认值会在多个类和实际之间共享。

## **server_software**

​	如果设置了 [`origin_server`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler.origin_server" >}}) 属性，该属性的值会被用来设置默认的 `SERVER_SOFTWARE` WSGI 环境变量，并且还会被用来设置 HTTP 响应中默认的 `Server:` 标头。 它会被不是 HTTP 原始服务器的处理器所忽略 (例如 [`BaseCGIHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseCGIHandler" >}}) 和 [`CGIHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.CGIHandler" >}}))。

> 在 3.3 版本发生变更: 名称 "Python" 会被替换为实现专属的名称如 "CPython", "Jython" 等等。

## **get_scheme**()

​	返回当前请求所使用的 URL 方案。 默认的实现会使用来自 [`wsgiref.util`]({{< ref "/library/internet/wsgiref#module-wsgiref.util" >}}) 的 `guess_scheme()` 函数根据当前请求的 `environ` 变量来猜测方案应该是 "http" 还是 "https"。

## **setup_environ**()

​	将 `environ` 属性设为填充了完整内容的 WSGI 环境。 默认的实现会使用上述的所有方法和属性，加上 [`get_stdin()`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler.get_stdin" >}}), [`get_stderr()`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler.get_stderr" >}}) 和 [`add_cgi_vars()`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler.add_cgi_vars" >}}) 方法以及 [`wsgi_file_wrapper`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler.wsgi_file_wrapper" >}}) 属性。 它还会在 `SERVER_SOFTWARE` 不存在时插入该键，只要 [`origin_server`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler.origin_server" >}}) 属性为真值并且设置了 [`server_software`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler.server_software" >}}) 属性。

​	用于自定义异常处理的方法和属性:

## **log_exception**(*exc_info*)

​	将 *exc_info* 元素记录到服务器日志。 *exc_info* 是一个 `(type, value, traceback)` 元组。 默认的实现会简单地将回溯信息写入请求的 `wsgi.errors` 流并刷新它。 子类可以重写此方法来修改格式或重设输出目标，通过邮件将回溯信息发给管理员，或执行任何其他符合要求的动作。

## **traceback_limit**

​	要包括在默认 [`log_exception()`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler.log_exception" >}}) 方法的回溯信息输出中的最大帧数。 如果为 `None`，则会包括所有的帧。

## **error_output**(*environ*, *start_response*)

​	此方法是一个为用户生成错误页面的 WSGI 应用程序。 它仅当将标头发送给客户端之前发生错误时会被发起调用。

​	此访问可以使用 `sys.exception()` 来访问当前错误信息，并应当在调用它时将该信息传给 *start_response* (如as described in the "Error Handling" section of [**PEP 3333**](https://peps.python.org/pep-3333/) 的“错误处理”一节所描述的)。

​	默认的实现只是使用 [`error_status`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler.error_status" >}}), [`error_headers`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler.error_headers" >}}), 和 [`error_body`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler.error_body" >}}) 属性来生成一个输出页面。 子类可以重写此方法来产生更动态化的错误输出。

​	但是请注意，从安全角度看来是不建议将诊断信息暴露给任何用户的；理想的做法是你应当通过特别处理来启用诊断输出，因此默认的实现并不包括这样的内容。

## **error_status**

​	用于错误响应的 HTTP 状态。 这应当是一个在 [**PEP 3333**](https://peps.python.org/pep-3333/) 中定义的字符串；它默认为代码 500 以相应的消息。

## **error_headers**

​	用于错误响应的 HTTP 标头。 这应当是由 WSGI 响应标头 (`(name, value)` 元组) 构成的列表，如 [**PEP 3333**](https://peps.python.org/pep-3333/) 所定义的。 默认的列表只是将内容类型设为 `text/plain`。

## **error_body**

​	错误响应体。 这应当是一个 HTTP 响应体字节串。 它默认为纯文本 "A server error occurred. Please contact the administrator."

​	用于 [**PEP 3333**](https://peps.python.org/pep-3333/) 的 "可选的平台专属文件处理" 特性的方法和属性:

## **wsgi_file_wrapper**

​	一个 `wsgi.file_wrapper` 工厂对象，兼容 [`wsgiref.types.FileWrapper`]({{< ref "/library/internet/wsgiref#wsgiref.types.FileWrapper" >}})，或者为 `None`。 该属性的默认值是 [`wsgiref.util.FileWrapper`]({{< ref "/library/internet/wsgiref#wsgiref.util.FileWrapper" >}}) 类。

## **sendfile**()

​	重载以实现平台专属的文件传输。 此方法仅在应用程序的返回值是由 [`wsgi_file_wrapper`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler.wsgi_file_wrapper" >}}) 属性指定的类的实例时会被调用。 如果它能够成功地传输文件则应当返回真值，以使得默认的传输代码将不会被执行。 此方法的默认实现只会返回假值。

​	杂项方法和属性:

## **origin_server**

​	该属性在处理器的 [`_write()`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler._write" >}}) 和 [`_flush()`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler._flush" >}}) 被用于同客户端直接通信而不是通过需要 HTTP 状态为某种特殊 `Status:` 标头的 CGI 类网关协议时应当被设为真值

​	该属性在 [`BaseHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler" >}}) 中默认为真值，但在 [`BaseCGIHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseCGIHandler" >}}) 和 [`CGIHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.CGIHandler" >}}) 中则为假值。

## **http_version**

​	如果 [`origin_server`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.BaseHandler.origin_server" >}}) 为真值，则该字符串属性会被用来设置给客户端的响应的 HTTP 版本。 它的默认值为 `"1.0"`。

## wsgiref.handlers.**read_environ**()

​	将来自 `os.environ` 的 CGI 变量转码为 [**PEP 3333**](https://peps.python.org/pep-3333/) "bytes in unicode" 字符串，返回一个新的字典。 此函数被 [`CGIHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.CGIHandler" >}}) 和 [`IISCGIHandler`]({{< ref "/library/internet/wsgiref#wsgiref.handlers.IISCGIHandler" >}}) 用来替代直接使用 `os.environ`，后者不一定在所有使用 Python 3 的平台和 Web 服务器上都符合 WSGI 标准 -- 特别是当 OS 的实际环境为 Unicode 时 (例如 Windows)，或者当环境为字节数据，但被 Python 用来解码它的系统编码格式不是 ISO-8859-1 时 (例如使用 UTF-8 的 Unix 系统)。

​	如果你要实现自己的基于 CGI 的处理器，你可能会想要使用此例程而不是简单地从 `os.environ` 直接拷贝值。

> Added in version 3.2.
>



## [`wsgiref.types`]({{< ref "/library/internet/wsgiref#module-wsgiref.types" >}}) -- 用于静态类型检查的 WSGI 类型

​	本模块提供了多种类型用于在 [**PEP 3333**](https://peps.python.org/pep-3333/) 中所描述的静态类型检查。

> Added in version 3.11.
>

## *class* wsgiref.types.**StartResponse**

​	一个描述 [**start_response()**](https://peps.python.org/pep-3333/#the-start-response-callable) 可迭代对象的 [`typing.Protocol`]({{< ref "/library/development/typing#typing.Protocol" >}})。 ([**PEP 3333**](https://peps.python.org/pep-3333/))

## wsgiref.types.**WSGIEnvironment**

​	一个描述 WSGI 环境字典的类型别名。

## wsgiref.types.**WSGIApplication**

​	一个描述 WSGI 应用程序可迭代对象的类型别名。

## *class* wsgiref.types.**InputStream**

​	一个描述 [**WSGI 输入流**](https://peps.python.org/pep-3333/#input-and-error-streams) 的 [`typing.Protocol`]({{< ref "/library/development/typing#typing.Protocol" >}})。

## *class* wsgiref.types.**ErrorStream**

​	一个描述 [**WSGI 错误流**](https://peps.python.org/pep-3333/#input-and-error-streams) 的 [`typing.Protocol`]({{< ref "/library/development/typing#typing.Protocol" >}})。

## *class* wsgiref.types.**FileWrapper**

​	一个描述 [**文件包装器**](https://peps.python.org/pep-3333/#optional-platform-specific-file-handling) 的 [`typing.Protocol`]({{< ref "/library/development/typing#typing.Protocol" >}})。 请参阅 [`wsgiref.util.FileWrapper`]({{< ref "/library/internet/wsgiref#wsgiref.util.FileWrapper" >}}) 查看此协议的一个具体实现。

## 例子

​	这是一个可运行的 "Hello World" WSGI 应用程序:

```
"""
每个 WSGI 应用程序必须有一个应用程序对象 —— 即一个将接受
两个参数的可调用对象。 为达成此目的，我们准备使用一个函数
（请注意并非仅限使用函数，例如你也可以使用一个类）。
传给该函数的第一个参数是一个包含 CGI 风格的环境变量的字典
而第二个变量就是上述的可调用对象。
"""
from wsgiref.simple_server import make_server


def hello_world_app(environ, start_response):
    status = "200 OK"  # HTTP Status
    headers = [("Content-type", "text/plain; charset=utf-8")]  # HTTP 标头
    start_response(status, headers)

    # 返回的对象将被打印出来
    return [b"Hello World"]

with make_server("", 8000, hello_world_app) as httpd:
    print("Serving on port 8000...")

    # 运行服务直到进程被杀掉
    httpd.serve_forever()
```

​	一个发布当前目录的 WSGI 应用程序示例，接受通过命令行指定可选的目录和端口号 (默认值: 8000):

```
"""
基于 wsgiref 的小型 web 服务器。 接受一个服务路径和
一个可选的端口号 (默认为 8000)，然后尝试发布文件。
MIME 类型将根据文件名来猜测，如果文件未找到则会
引发 404 错误。
"""
import mimetypes
import os
import sys
from wsgiref import simple_server, util


def app(environ, respond):
    # 获取文件名和 MIME 类型
    fn = os.path.join(path, environ["PATH_INFO"][1:])
    if "." not in fn.split(os.path.sep)[-1]:
        fn = os.path.join(fn, "index.html")
    mime_type = mimetypes.guess_file_type(fn)[0]

    # 如果文件存在则返回 200 OK，否则返回 404 Not Found
    if os.path.exists(fn):
        respond("200 OK", [("Content-Type", mime_type)])
        return util.FileWrapper(open(fn, "rb"))
    else:
        respond("404 Not Found", [("Content-Type", "text/plain")])
        return [b"not found"]


if __name__ == "__main__":
    # 从命令行参数获取路径和端口
    path = sys.argv[1] if len(sys.argv) > 1 else os.getcwd()
    port = int(sys.argv[2]) if len(sys.argv) > 2 else 8000

    # 创建并启动服务直至被 control-c 中断
    httpd = simple_server.make_server("", port, app)
    print(f"Serving {path} on port {port}, control-C to stop")
    try:
        httpd.serve_forever()
    except KeyboardInterrupt:
        print("Shutting down.")
        httpd.server_close()
```
