+++
title = "http.cookies --- HTTP 状态管理"
date = 2024-11-15T20:28:46+08:00
weight = 170
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/http.cookies.html](https://docs.python.org/zh-cn/3.13/library/http.cookies.html)
>
> 收录该文档的时间：`2024-11-15T20:28:46+08:00`

# `http.cookies` --- HTTP 状态管理

**源代码:** [Lib/http/cookies.py](https://github.com/python/cpython/tree/3.13/Lib/http/cookies.py)

------

[`http.cookies`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#module-http.cookies) 模块定义的类将 cookie 的概念抽象了出来，这是一种 HTTP 状态的管理机制。它既支持简单的纯字符串形式的 cookie，也为任何可序列化数据类型的 cookie 提供抽象。

​	之前该模块严格应用了 [**RFC 2109**](https://datatracker.ietf.org/doc/html/rfc2109.html) 和 [**RFC 2068**](https://datatracker.ietf.org/doc/html/rfc2068.html) 规范中描述的解析规则。 后来人们发现 MSIE 3.0x 并未遵循这些规范中描述的字符规则；目前各种浏览器和服务器在处理 cookie 时也放宽了解析规则。 因此，该模块目前使用的解析规则也没有以前那么严格了。

​	字符集 [`string.ascii_letters`](https://docs.python.org/zh-cn/3.13/library/string.html#string.ascii_letters), [`string.digits`](https://docs.python.org/zh-cn/3.13/library/string.html#string.digits) 和 `!#$%&'*+-.^_`|~:` 标明了本模块允许在 cookie 名称中出现的有效字符 (如 [`key`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.Morsel.key))。

*在 3.3 版本发生变更:* 允许 ':' 作为有效的 cookie 名称字符。

​	备注

 

​	当遇到无效 cookie 时会触发 [`CookieError`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.CookieError)，所以若 cookie 数据来自浏览器，一定要做好应对无效数据的准备，并在解析时捕获 [`CookieError`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.CookieError)。

## *exception* http.cookies.**CookieError**

​	出现异常的原因，可能是不符合 [**RFC 2109**](https://datatracker.ietf.org/doc/html/rfc2109.html) ：属性不正确、*Set-Cookie* 头部信息不正确等等。

## *class* http.cookies.**BaseCookie**([*input*])

​	类似字典的对象，字典键为字符串，字典值是 [`Morsel`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.Morsel) 实例。请注意，在将键值关联时，首先会把值转换为包含键和值的 [`Morsel`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.Morsel) 对象。

​	若给出 *input* ，将会传给 [`load()`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.BaseCookie.load) 方法。

## *class* http.cookies.**SimpleCookie**([*input*])

​	该类派生自 [`BaseCookie`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.BaseCookie) 并重写了 [`value_decode()`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.BaseCookie.value_decode) 和 [`value_encode()`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.BaseCookie.value_encode)。 `SimpleCookie` 支持用字符串作为 cookie 值。 在设置值时，`SimpleCookie` 会调用内置 [`str()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 将值转换为字符串。 从 HTTP 接收的值仍然保持为字符串。

​	参见

## [`http.cookiejar`](https://docs.python.org/zh-cn/3.13/library/http.cookiejar.html#module-http.cookiejar) 模块

​	处理网络 *客户端* 的 HTTP cookie。 [`http.cookiejar`](https://docs.python.org/zh-cn/3.13/library/http.cookiejar.html#module-http.cookiejar) 和 [`http.cookies`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#module-http.cookies) 模块相互没有依赖关系。

## [**RFC 2109**](https://datatracker.ietf.org/doc/html/rfc2109.html) - HTTP状态管理机制

​	这是本模块实现的状态管理规范。



## Cookie 对象

## BaseCookie.**value_decode**(*val*)

​	由字符串返回元组 `(real_value, coded_value)`。`real_value` 可为任意类型。[`BaseCookie`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.BaseCookie) 中的此方法未实现任何解码工作——只为能被子类重写。

## BaseCookie.**value_encode**(*val*)

​	返回元组 `(real_value, coded_value)`。*val* 可为任意类型，`coded_value` 则会转换为字符串。 [`BaseCookie`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.BaseCookie) 中的此方法未实现任何编码工作——只为能被子类重写。

​	通常在 *value_decode* 的取值范围内，[`value_encode()`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.BaseCookie.value_encode) 和 [`value_decode()`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.BaseCookie.value_decode) 应为可互逆操作。

## BaseCookie.**output**(*attrs=None*, *header='Set-Cookie:'*, *sep='\r\n'*)

​	返回可作为 HTTP 标头信息发送的字符串表示。 *attrs* 和 *header* 会传给每个 [`Morsel`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.Morsel) 的 [`output()`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.BaseCookie.output) 方法。 *sep* 用来将标头连接在一起，默认为 `'\r\n'` (CRLF) 组合。

## BaseCookie.**js_output**(*attrs=None*)

​	返回一段可供嵌入的 JavaScript 代码，若在支持 JavaScript 的浏览器上运行，其作用如同发送 HTTP 头部信息一样。

*attrs* 的含义与 [`output()`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.BaseCookie.output) 的相同。

## BaseCookie.**load**(*rawdata*)

​	若 *rawdata* 为字符串，则会作为 `HTTP_COOKIE` 进行解析，并将找到的值添加为 [`Morsel`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.Morsel)。 如果是字典值，则等价于:

```
for k, v in rawdata.items():
    cookie[k] = v
```



## Morsel 对象

## *class* http.cookies.**Morsel**

​	对键/值对的抽象，带有 [**RFC 2109**](https://datatracker.ietf.org/doc/html/rfc2109.html) 的部分属性。

​	morsel 对象类似于字典，它的键是一组常量 --- 即有效的 [**RFC 2109**](https://datatracker.ietf.org/doc/html/rfc2109.html) 属性，包括：

> ## **expires**
>
> ## **path**
>
> ## **comment**
>
> ## **domain**
>
> ## **max-age**
>
> ## **secure**
>
> ## **version**
>
> ## **httponly**
>
> ## **samesite**

[`httponly`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.Morsel.httponly) 属性指明了该 cookie 仅在 HTTP 请求中传输，且不能通过 JavaScript 访问。这是为了减轻某些跨站脚本攻击的危害。

[`samesite`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.Morsel.samesite) 属性指明了浏览器不得与跨站请求一起发送该 cookie。这有助于减轻 CSRF 攻击的危害。此属性的有效值为 “Strict”和“Lax”。

​	键不区分大小写，默认值为 `''`。

*在 3.5 版本发生变更:* 现在 `__eq__()` 会同时考虑 [`key`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.Morsel.key) 和 [`value`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.Morsel.value)。

*在 3.7 版本发生变更:* Attributes [`key`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.Morsel.key), [`value`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.Morsel.value) and [`coded_value`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.Morsel.coded_value) are read-only. Use [`set()`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.Morsel.set) for setting them.

*在 3.8 版本发生变更:* 增加对 [`samesite`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.Morsel.samesite) 属性的支持。

## Morsel.**value**

​	Cookie的值。

## Morsel.**coded_value**

​	编码后的 cookie 值——也即要发送的内容。

## Morsel.**key**

​	cookie 名称

## Morsel.**set**(*key*, *value*, *coded_value*)

​	设置 *key*、*value* 和 *coded_value* 属性。

## Morsel.**isReservedKey**(*K*)

​	判断 *K* 是否属于 [`Morsel`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.Morsel) 的键。

## Morsel.**output**(*attrs=None*, *header='Set-Cookie:'*)

​	返回 morsel 的字符串形式，适用于作为 HTTP 头部信息进行发送。默认包含所有属性，除非给出 *attrs* 属性列表。*header* 默认为 `"Set-Cookie:"`。

## Morsel.**js_output**(*attrs=None*)

​	返回一段可供嵌入的 JavaScript 代码，若在支持 JavaScript 的浏览器上运行，其作用如同发送 HTTP 头部信息一样。

*attrs* 的含义与 [`output()`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.Morsel.output) 的相同。

## Morsel.**OutputString**(*attrs=None*)

​	返回 morsel 的字符串形式，不含 HTTP 或 JavaScript 数据。

*attrs* 的含义与 [`output()`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#http.cookies.Morsel.output) 的相同。

## Morsel.**update**(*values*)

​	用字典 *values* 中的值更新 morsel 字典中的值。若有 *values* 字典中的键不是有效的 [**RFC 2109**](https://datatracker.ietf.org/doc/html/rfc2109.html) 属性，则会触发错误。

*在 3.5 版本发生变更:* 无效键会触发错误。

## Morsel.**copy**(*value*)

​	返回 morsel 对象的浅表复制副本。

*在 3.5 版本发生变更:* 返回一个 morsel 对象，而非字典。

## Morsel.**setdefault**(*key*, *value=None*)

​	若 key 不是有效的 [**RFC 2109**](https://datatracker.ietf.org/doc/html/rfc2109.html) 属性则触发错误，否则与 [`dict.setdefault()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#dict.setdefault) 相同。



## 示例

​	以下例子演示了 [`http.cookies`](https://docs.python.org/zh-cn/3.13/library/http.cookies.html#module-http.cookies) 模块的用法。



``` python
>>> from http import cookies
>>> C = cookies.SimpleCookie()
>>> C["fig"] = "newton"
>>> C["sugar"] = "wafer"
>>> print(C) # 生成 HTTP 标头
Set-Cookie: fig=newton
Set-Cookie: sugar=wafer
>>> print(C.output()) # 同样的内容
Set-Cookie: fig=newton
Set-Cookie: sugar=wafer
>>> C = cookies.SimpleCookie()
>>> C["rocky"] = "road"
>>> C["rocky"]["path"] = "/cookie"
>>> print(C.output(header="Cookie:"))
Cookie: rocky=road; Path=/cookie
>>> print(C.output(attrs=[], header="Cookie:"))
Cookie: rocky=road
>>> C = cookies.SimpleCookie()
>>> C.load("chips=ahoy; vienna=finger") # 从字符串加载 (HTTP 标头)
>>> print(C)
Set-Cookie: chips=ahoy
Set-Cookie: vienna=finger
>>> C = cookies.SimpleCookie()
>>> C.load('keebler="E=everybody; L=\\"Loves\\"; fudge=\\012;";')
>>> print(C)
Set-Cookie: keebler="E=everybody; L=\"Loves\"; fudge=\012;"
>>> C = cookies.SimpleCookie()
>>> C["oreo"] = "doublestuff"
>>> C["oreo"]["path"] = "/"
>>> print(C)
Set-Cookie: oreo=doublestuff; Path=/
>>> C = cookies.SimpleCookie()
>>> C["twix"] = "none for you"
>>> C["twix"].value
'none for you'
>>> C = cookies.SimpleCookie()
>>> C["number"] = 7 # 等价于 C["number"] = str(7)
>>> C["string"] = "seven"
>>> C["number"].value
'7'
>>> C["string"].value
'seven'
>>> print(C)
Set-Cookie: number=7
Set-Cookie: string=seven
```
