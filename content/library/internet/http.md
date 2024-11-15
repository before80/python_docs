+++
title = "http --- HTTP 模块"
date = 2024-11-15T20:28:46+08:00
weight = 80
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/http.html](https://docs.python.org/zh-cn/3.13/library/http.html)
>
> 收录该文档的时间：`2024-11-15T20:28:46+08:00`

# `http` --- HTTP 模块

**源代码:** [Lib/http/__init__.py](https://github.com/python/cpython/tree/3.13/Lib/http/__init__.py)

------

[`http`](https://docs.python.org/zh-cn/3.13/library/http.html#module-http) 是一个包，它收集了多个用于处理超文本传输协议的模块:

- [`http.client`](https://docs.python.org/zh-cn/3.13/library/http.client.html#module-http.client) 是一个底层的 HTTP 协议客户端；对于高层级的 URL 访问请使用 [`urllib.request`](https://docs.python.org/zh-cn/3.13/library/urllib.request.html#module-urllib.request)
- [`http.server`](https://docs.python.org/zh-cn/3.13/library/http.server.html#module-http.server) 包含基于 [`socketserver`](https://docs.python.org/zh-cn/3.13/library/socketserver.html#module-socketserver) 的基本 HTTP 服务类
- [`http.cookies`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#module-http.cookies) 包含一些有用来实现通过 cookies 进行状态管理的工具
- [`http.cookiejar`](https://docs.python.org/zh-cn/3.13/library/http.cookiejar.html#module-http.cookiejar) 提供了 cookies 的持久化

[`http`](https://docs.python.org/zh-cn/3.13/library/http.html#module-http) 模块还定义了下列枚举来帮助你使用 http 相关的代码:

## *class* http.**HTTPStatus**

*Added in version 3.5.*

[`enum.IntEnum`](https://docs.python.org/zh-cn/3.13/library/enum.html#enum.IntEnum) 的子类，它定义了组 HTTP 状态码，原理短语以及用英语书写的长描述文本。

​	用法：

\>>>

```
>>> from http import HTTPStatus
>>> HTTPStatus.OK
HTTPStatus.OK
>>> HTTPStatus.OK == 200
True
>>> HTTPStatus.OK.value
200
>>> HTTPStatus.OK.phrase
'OK'
>>> HTTPStatus.OK.description
'Request fulfilled, document follows'
>>> list(HTTPStatus)
[HTTPStatus.CONTINUE, HTTPStatus.SWITCHING_PROTOCOLS, ...]
```



## HTTP 状态码

​	已支持的，[IANA 注册的状态码](https://www.iana.org/assignments/http-status-codes/http-status-codes.xhtml) 在 [`http.HTTPStatus`](https://docs.python.org/zh-cn/3.13/library/http.html#http.HTTPStatus) 中可用的有:

| 双字母代码 | 映射名                                                | 详情                                                         |
| :--------- | :---------------------------------------------------- | :----------------------------------------------------------- |
| `100`      | `CONTINUE`：继续                                      | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.2.1 |
| `101`      | `SWITCHING_PROTOCOLS`                                 | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.2.2 |
| `102`      | `PROCESSING`                                          | WebDAV RFC 2518, 10.1 节                                     |
| `103`      | `EARLY_HINTS`                                         | 用于指定提示 [**RFC 8297**](https://datatracker.ietf.org/doc/html/rfc8297.html) 的 HTTP 状态码 |
| `200`      | `OK`                                                  | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.3.1 |
| `201`      | `CREATED`                                             | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.3.2 |
| `202`      | `ACCEPTED`                                            | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.3.3 |
| `203`      | `NON_AUTHORITATIVE_INFORMATION`                       | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.3.4 |
| `204`      | `NO_CONTENT`: 没有内容                                | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.3.5 |
| `205`      | `RESET_CONTENT`                                       | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.3.6 |
| `206`      | `PARTIAL_CONTENT`                                     | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.3.7 |
| `207`      | `MULTI_STATUS`                                        | WebDAV RFC 4918, 11.1 节                                     |
| `208`      | `ALREADY_REPORTED`                                    | WebDAV Binding Extensions RFC 5842, 7.1 节（实验性）         |
| `226`      | `IM_USED`                                             | Delta Encoding in HTTP RFC 3229, 10.4.1 节                   |
| `300`      | `MULTIPLE_CHOICES`：有多种资源可选择                  | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.4.1 |
| `301`      | `MOVED_PERMANENTLY`：永久移动                         | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.4.2 |
| `302`      | `FOUND`：临时移动                                     | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.4.3 |
| `303`      | `SEE_OTHER`：已经移动                                 | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.4.4 |
| `304`      | `NOT_MODIFIED`：没有修改                              | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.4.5 |
| `305`      | `USE_PROXY`：使用代理                                 | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.4.6 |
| `307`      | `TEMPORARY_REDIRECT`：临时重定向                      | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.4.8 |
| `308`      | `PERMANENT_REDIRECT`：永久重定向                      | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.4.9 |
| `400`      | `BAD_REQUEST`：错误请求                               | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.5.1 |
| `401`      | `UNAUTHORIZED`：未授权                                | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.5.2 |
| `402`      | `PAYMENT_REQUIRED`：保留，将来使用                    | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.5.3 |
| `403`      | `FORBIDDEN`：禁止                                     | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.5.4 |
| `404`      | `NOT_FOUND`：没有找到                                 | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.5.5 |
| `405`      | `METHOD_NOT_ALLOWED`：该请求方法不允许                | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.5.6 |
| `406`      | `NOT_ACCEPTABLE`：不可接受                            | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.5.7 |
| `407`      | `PROXY_AUTHENTICATION_REQUIRED`：要求使用代理验明正身 | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.5.8 |
| `408`      | `REQUEST_TIMEOUT`：请求超时                           | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.5.9 |
| `409`      | `CONFLICT`：冲突                                      | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.5.10 |
| `410`      | `GONE`：已经不在了                                    | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.5.11 |
| `411`      | `LENGTH_REQUIRED`：长度要求                           | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.5.12 |
| `412`      | `PRECONDITION_FAILED`：前提条件错误                   | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.5.13 |
| `413`      | `CONTENT_TOO_LARGE`                                   | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.5.14 |
| `414`      | `URI_TOO_LONG`                                        | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.5.15 |
| `415`      | `UNSUPPORTED_MEDIA_TYPE`：不支持的媒体格式            | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.5.16 |
| `416`      | `RANGE_NOT_SATISFIABLE`                               | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.5.17 |
| `417`      | `EXPECTATION_FAILED`：期望失败                        | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.5.18 |
| `418`      | `IM_A_TEAPOT`                                         | HTCPCP/1.0 [**RFC 2324**](https://datatracker.ietf.org/doc/html/rfc2324.html), Section 2.3.2 |
| `421`      | `MISDIRECTED_REQUEST`                                 | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.5.20 |
| `422`      | `UNPROCESSABLE_CONTENT`                               | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.5.21 |
| `423`      | `LOCKED`：锁着                                        | WebDAV RFC 4918, 11.3 节                                     |
| `424`      | `FAILED_DEPENDENCY`：失败的依赖                       | WebDAV RFC 4918, 11.4 节                                     |
| `425`      | `TOO_EARLY`                                           | 使用 HTTP [**RFC 8470**](https://datatracker.ietf.org/doc/html/rfc8470.html) 中的早期数据 |
| `426`      | `UPGRADE_REQUIRED`：升级需要                          | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.5.22 |
| `428`      | `PRECONDITION_REQUIRED`：先决条件要求                 | Additional HTTP Status Codes RFC 6585                        |
| `429`      | `TOO_MANY_REQUESTS`：太多的请求                       | Additional HTTP Status Codes RFC 6585                        |
| `431`      | `REQUEST_HEADER_FIELDS_TOO_LARGE`：请求头太大         | Additional HTTP Status Codes RFC 6585                        |
| `451`      | `UNAVAILABLE_FOR_LEGAL_REASONS`                       | HTTP 状态码用于报告法律障碍 [**RFC 7725**](https://datatracker.ietf.org/doc/html/rfc7725.html) |
| `500`      | `INTERNAL_SERVER_ERROR`：内部服务错误                 | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.6.1 |
| `501`      | `NOT_IMPLEMENTED`：不可执行                           | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.6.2 |
| `502`      | `BAD_GATEWAY`：无效网关                               | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.6.3 |
| `503`      | `SERVICE_UNAVAILABLE`：服务不可用                     | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.6.4 |
| `504`      | `GATEWAY_TIMEOUT`：网关超时                           | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.6.5 |
| `505`      | `HTTP_VERSION_NOT_SUPPORTED`：HTTP版本不支持          | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15.6.6 |
| `506`      | `VARIANT_ALSO_NEGOTIATES`：服务器存在内部配置错误     | 透明内容协商在： HTTP [**RFC 2295**](https://datatracker.ietf.org/doc/html/rfc2295.html), 8.1 节（实验性） |
| `507`      | `INSUFFICIENT_STORAGE`：存储不足                      | WebDAV RFC 4918, 11.5 节                                     |
| `508`      | `LOOP_DETECTED`：循环检测                             | WebDAV Binding Extensions RFC 5842, 7.2 节（实验性）         |
| `510`      | `NOT_EXTENDED`：不扩展                                | WebDAV Binding Extensions RFC 5842, 7.2 节（实验性）         |
| `511`      | `NETWORK_AUTHENTICATION_REQUIRED`：要求网络身份验证   | Additional HTTP Status Codes [**RFC 6585**](https://datatracker.ietf.org/doc/html/rfc6585.html), 6 节 |

​	为了保持向后兼容性，枚举值也以常量形式出现在 [`http.client`](https://docs.python.org/zh-cn/3.13/library/http.client.html#module-http.client) 模块中，。 枚举名等于常量名 (例如 `http.HTTPStatus.OK` 也可以是 `http.client.OK`)。

*在 3.7 版本发生变更:* 添加了 `421 MISDIRECTED_REQUEST` 状态码。

*Added in version 3.8:* 添加了 `451 UNAVAILABLE_FOR_LEGAL_REASONS` 状态码。

*Added in version 3.9:* 增加了 `103 EARLY_HINTS`, `418 IM_A_TEAPOT` 和 `425 TOO_EARLY` 状态码

*在 3.13 版本发生变更:* 实现了针对状态常量的 RFC9110 命名。 旧常量名被保留用于向下兼容。

## HTTP 状态类别

*Added in version 3.12.*

​	这些枚举值具有一些用于指明 HTTP 状态类别的特征属性:

| 特征属性           | 表示                   | 详情                                                         |
| :----------------- | :--------------------- | :----------------------------------------------------------- |
| `is_informational` | `100 <= status <= 199` | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15 |
| `is_success`       | `200 <= status <= 299` | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15 |
| `is_redirection`   | `300 <= status <= 399` | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15 |
| `is_client_error`  | `400 <= status <= 499` | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15 |
| `is_server_error`  | `500 <= status <= 599` | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 15 |

> ​	用法：
>
> \>>>
>
> ```
> >>> from http import HTTPStatus
> >>> HTTPStatus.OK.is_success
> True
> >>> HTTPStatus.OK.is_client_error
> False
> ```

## *class* http.**HTTPMethod**

*Added in version 3.11.*

​	一个 [`enum.StrEnum`](https://docs.python.org/zh-cn/3.13/library/enum.html#enum.StrEnum) 的子类，它定义了一组 HTTP 方法以及用英文书写的描述。

​	用法：

\>>>

```
>>> from http import HTTPMethod
>>>
>>> HTTPMethod.GET
<HTTPMethod.GET>
>>> HTTPMethod.GET == 'GET'
True
>>> HTTPMethod.GET.value
'GET'
>>> HTTPMethod.GET.description
'Retrieve the target.'
>>> list(HTTPMethod)
[<HTTPMethod.CONNECT>,
 <HTTPMethod.DELETE>,
 <HTTPMethod.GET>,
 <HTTPMethod.HEAD>,
 <HTTPMethod.OPTIONS>,
 <HTTPMethod.PATCH>,
 <HTTPMethod.POST>,
 <HTTPMethod.PUT>,
 <HTTPMethod.TRACE>]
```



## HTTP 方法

​	已支持的，[IANA 注册的方法](https://www.iana.org/assignments/http-methods/http-methods.xhtml) 在 [`http.HTTPMethod`](https://docs.python.org/zh-cn/3.13/library/http.html#http.HTTPMethod) 中可用的有:

| 方法      | 映射名    | 详情                                                         |
| :-------- | :-------- | :----------------------------------------------------------- |
| `GET`     | `GET`     | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 9.3.1 |
| `HEAD`    | `HEAD`    | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 9.3.2 |
| `POST`    | `POST`    | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 9.3.3 |
| `PUT`     | `PUT`     | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 9.3.4 |
| `DELETE`  | `DELETE`  | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 9.3.5 |
| `CONNECT` | `CONNECT` | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 9.3.6 |
| `OPTIONS` | `OPTIONS` | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 9.3.7 |
| `TRACE`   | `TRACE`   | HTTP Semantics [**RFC 9110**](https://datatracker.ietf.org/doc/html/rfc9110.html), Section 9.3.8 |
| `PATCH`   | `PATCH`   | HTTP/1.1 [**RFC 5789**](https://datatracker.ietf.org/doc/html/rfc5789.html) |
