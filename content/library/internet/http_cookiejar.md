+++
title = "http.cookiejar --- HTTP 客户端的 Cookie 处理"
date = 2024-11-15T20:28:46+08:00
weight = 180
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/http.cookiejar.html](https://docs.python.org/zh-cn/3.13/library/http.cookiejar.html)
>
> 收录该文档的时间：`2024-11-15T20:28:46+08:00`

# `http.cookiejar` --- HTTP 客户端的 Cookie 处理

**源代码：** [Lib/http/cookiejar.py](https://github.com/python/cpython/tree/3.13/Lib/http/cookiejar.py)

------

[`http.cookiejar`]({{< ref "/library/internet/http_cookiejar#module-http.cookiejar" >}}) 模块定义了用于自动处理 HTTP cookie 的类。这对访问需要小段数据 —— *cookies* 的网站很有用，这些数据由 Web 服务器的 HTTP 响应在客户端计算机上设置，然后在以后的 HTTP 请求中返回给服务器。

​	常规的 Netscape cookie 协议和由 [**RFC 2965**](https://datatracker.ietf.org/doc/html/rfc2965.html) 定义的协议都可以被处理。 RFC 2965 的处理默认是关闭的。 [**RFC 2109**](https://datatracker.ietf.org/doc/html/rfc2109.html) cookie 被解析为 Netscape cookie ，随后根据当前使用的 "策略"，被视为 Netscape 或 RFC 2965 cookie。[`http.cookiejar`]({{< ref "/library/internet/http_cookiejar#module-http.cookiejar" >}}) 试图遵循事实上的 Netscape cookie 协议（它与原始Netscape规范中的协议有很大不同），包括注意到 RFC 2965 中引入的 `max-age` 和 `port` cookie 属性。

​备注
 

​	在 *Set-Cookie* 和 *Set-Cookie2* 头中找到的各种命名参数通常指 *attributes*。为了不与 Python 属性相混淆，模块文档使用 *cookie-attribute* 代替。

​	此模块定义了以下异常：

## *exception* http.cookiejar.**LoadError**

[`FileCookieJar`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.FileCookieJar" >}}) 实例在从文件加载 cookies 出错时抛出这个异常。 [`LoadError`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.LoadError" >}}) 是 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 的一个子类。

> 在 3.3 版本发生变更: [`LoadError`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.LoadError" >}}) 曾经是 [`IOError`]({{< ref "/library/exceptions#IOError" >}}) 的子类型，现在它是 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 的一个别名。

​	提供了以下类：

## *class* http.cookiejar.**CookieJar**(*policy=None*)

*policy* 是实现了 [`CookiePolicy`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy" >}}) 接口的一个对象。

[`CookieJar`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookieJar" >}}) 类储存 HTTP cookies。它从 HTTP 请求提取 cookies，并在 HTTP 响应中返回它们。 [`CookieJar`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookieJar" >}}) 实例在必要时自动处理包含 cookie 的到期情况。子类还负责储存和从文件或数据库中查找 cookies。

## *class* http.cookiejar.**FileCookieJar**(*filename=None*, *delayload=None*, *policy=None*)

*policy* 是实现了 [`CookiePolicy`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy" >}}) 接口的一个对象。对于其他参数，参考相应属性的文档。

​	一个可以从硬盘中文件加载或保存 cookie 的 [`CookieJar`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookieJar" >}})。 Cookies **不** 会在 [`load()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.FileCookieJar.load" >}}) 或 [`revert()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.FileCookieJar.revert" >}}) 方法调用前从命名的文件中加载。子类的文档位于段落 [FileCookieJar 的子类及其与 Web 浏览器的协同]({{< ref "/library/internet/http_cookiejar#file-cookie-jar-classes" >}})。

​	此类不应被直接初始化 —— 请改用它的下列子类。

> 在 3.8 版本发生变更: 文件名形参支持 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## *class* http.cookiejar.**CookiePolicy**

​	此类负责确定是否应从服务器接受每个 cookie 或将其返回给服务器。

## *class* http.cookiejar.**DefaultCookiePolicy**(*blocked_domains=None*, *allowed_domains=None*, *netscape=True*, *rfc2965=False*, *rfc2109_as_netscape=None*, *hide_cookie2=False*, *strict_domain=False*, *strict_rfc2965_unverifiable=True*, *strict_ns_unverifiable=False*, *strict_ns_domain=DefaultCookiePolicy.DomainLiberal*, *strict_ns_set_initial_dollar=False*, *strict_ns_set_path=False*, *secure_protocols=('https', 'wss')*)

​	构造参数只能以关键字参数传递，*blocked_domains* 是一个我们既不会接受也不会返回 cookie 的域名序列。*allowed_domains* 如果不是 [`None`]({{< ref "/library/constants#None" >}})，则是仅有的我们会接受或返回的域名序列。*secure_protocols* 是可以添加安全 cookies 的协议序列。默认将 *https* 和 *wss* （安全 WebSocket）考虑为安全协议。对于其他参数，参考 [`CookiePolicy`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy" >}}) 和 [`DefaultCookiePolicy`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.DefaultCookiePolicy" >}}) 对象的文档。

[`DefaultCookiePolicy`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.DefaultCookiePolicy" >}}) 实现了 Netscape 和 [**RFC 2965**](https://datatracker.ietf.org/doc/html/rfc2965.html) cookies 的标准接受 / 拒绝规则。 默认情况下，[**RFC 2109**](https://datatracker.ietf.org/doc/html/rfc2109.html) cookies（即在 *Set-Cookie* 头中收到的 cookie-attribute 版本为 1 的 cookies ）将按照 RFC 2965 规则处理。 然而，如果 RFC 2965 的处理被关闭，或者 [`rfc2109_as_netscape`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.DefaultCookiePolicy.rfc2109_as_netscape" >}}) 为 `True`，[`Cookie`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.Cookie" >}}) 实例的 `version` 属性设置将被为 0， RFC 2109 cookies [`CookieJar`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookieJar" >}}) 实例将 "降级" 为 Netscape cookies。 [`DefaultCookiePolicy`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.DefaultCookiePolicy" >}}) 也提供一些参数以允许一些策略微调。

## *class* http.cookiejar.**Cookie**

​	这个类代表 Netscape、[**RFC 2109**](https://datatracker.ietf.org/doc/html/rfc2109.html) 和 [**RFC 2965**](https://datatracker.ietf.org/doc/html/rfc2965.html) 的 cookie 。 我们不希望 [`http.cookiejar`]({{< ref "/library/internet/http_cookiejar#module-http.cookiejar" >}}) 的用户构建他们自己的 [`Cookie`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.Cookie" >}}) 实例。 如果有必要，请在一个 [`CookieJar`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookieJar" >}}) 实例上调用 `make_cookies()` 。

​参见
## 模块 [`urllib.request`]({{< ref "/library/internet/urllib/urllib_request#module-urllib.request" >}})

​	URL 打开带有自动的 cookie 处理。

## 模块 [`http.cookies`]({{< ref "/library/internet/http_cookies#module-http.cookies" >}})

​	HTTP cookie类，主要是对服务端代码有用。 [`http.cookiejar`]({{< ref "/library/internet/http_cookiejar#module-http.cookiejar" >}}) 和 [`http.cookies`]({{< ref "/library/internet/http_cookies#module-http.cookies" >}}) 模块不相互依赖。

## https://curl.se/rfc/cookie_spec.html

​	原始 Netscape cookie 协议的规范。 虽然这仍然是主流协议，但所有主要浏览器（以及 [`http.cookiejar`]({{< ref "/library/internet/http_cookiejar#module-http.cookiejar" >}}) ）实现的 "Netscape cookie协议" 与 `cookie_spec.html` 中描述的协议仅有几分相似之处。

## [**RFC 2109**](https://datatracker.ietf.org/doc/html/rfc2109.html) - HTTP状态管理机制

​	被 [**RFC 2965**](https://datatracker.ietf.org/doc/html/rfc2965.html) 所取代。使用 *Set-Cookie* version=1 。

## [**RFC 2965**](https://datatracker.ietf.org/doc/html/rfc2965.html) - HTTP状态管理机制

​	修正了错误的 Netscape 协议。 使用 *Set-Cookie2* 来代替 *Set-Cookie* 。 没有广泛被使用。

## https://kristol.org/cookie/errata.html

​	未完成的 [**RFC 2965**](https://datatracker.ietf.org/doc/html/rfc2965.html) 勘误表。

[**RFC 2964**](https://datatracker.ietf.org/doc/html/rfc2964.html) - HTTP状态管理使用方法



## CookieJar 和 FileCookieJar 对象

[`CookieJar`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookieJar" >}}) 对象支持 [iterator]({{< ref "/glossary/idx#term-iterator" >}}) 协议，用于迭代包含的 [`Cookie`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.Cookie" >}}) 对象。

[`CookieJar`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookieJar" >}}) 有以下方法：

## CookieJar.**add_cookie_header**(*request*)

​	在 *request* 中添加正确的 *Cookie* 头。

​	如果策略允许（即 `rfc2965` 和 `hide_cookie2` 属性在 [`CookieJar`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookieJar" >}}) 的 [`CookiePolicy`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy" >}}) 实例中分别为 True 和 False ）， *Cookie2* 标头也会在适当时候添加。

​	如 [`urllib.request`]({{< ref "/library/internet/urllib/urllib_request#module-urllib.request" >}}) 文档所言 *request* 对象（通常是 [`urllib.request.Request`]({{< ref "/library/internet/urllib/urllib_request#urllib.request.Request" >}}) 的实例）必须支持 `get_full_url()`, `has_header()`, `get_header()`, `header_items()`, `add_unredirected_header()` 等方法以及 `host`, `type`, `unverifiable` 和 `origin_req_host` 等属性。

> 在 3.3 版本发生变更: *request* 对象需要 `origin_req_host` 属性。对已废弃的方法 `get_origin_req_host()` 的依赖已被移除。

## CookieJar.**extract_cookies**(*response*, *request*)

​	从HTTP *response* 中提取 cookie，并在政策允许的情况下，将它们存储在 [`CookieJar`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookieJar" >}}) 中。

[`CookieJar`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookieJar" >}}) 将 在*response* 参数中寻找允许的 *Set-Cookie* 和 *Set-Cookie2* 头信息，并适当地存储cookies（须经 [`CookiePolicy.set_ok()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy.set_ok" >}}) 方法批准）。

*response* 对象（通常是调用 [`urllib.request.urlopen()`]({{< ref "/library/internet/urllib/urllib_request#urllib.request.urlopen" >}}) 或类似方法的结果）应该支持 `info()` 方法，它返回 [`email.message.Message`]({{< ref "/library/netdata/email/email_compat32-message#email.message.Message" >}}) 实例。

​	如 [`urllib.request`]({{< ref "/library/internet/urllib/urllib_request#module-urllib.request" >}}) 文档所言 *request* 对象（通常是 [`urllib.request.Request`]({{< ref "/library/internet/urllib/urllib_request#urllib.request.Request" >}}) 的实例）必须支持 `get_full_url()` 方法以及 `host`, `unverifiable` 和 `origin_req_host` 等属性。 该请求被用于设置 cookie-attributes 的默认值并检查 cookie 是否允许被设置。

> 在 3.3 版本发生变更: *request* 对象需要 `origin_req_host` 属性。对已废弃的方法 `get_origin_req_host()` 的依赖已被移除。

## CookieJar.**set_policy**(*policy*)

​	设置要使用的 [`CookiePolicy`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy" >}}) 实例。

## CookieJar.**make_cookies**(*response*, *request*)

​	返回从 *response* 对象中提取的 [`Cookie`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.Cookie" >}}) 对象的序列。

​	关于 *response* 和 *request* 参数所需的接口，请参见 [`extract_cookies()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookieJar.extract_cookies" >}}) 的文档。

## CookieJar.**set_cookie_if_ok**(*cookie*, *request*)

​	如果策略规定可以这样做，就设置一个 [`Cookie`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.Cookie" >}}) 。

## CookieJar.**set_cookie**(*cookie*)

​	设置一个 [`Cookie`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.Cookie" >}})，不需要检查策略是否应该被设置。

## CookieJar.**clear**([*domain*[, *path*[, *name*]]])

​	清除一些cookie。

​	如果调用时没有参数，则清除所有的cookie。 如果给定一个参数，只有属于该 *domain* 的cookies将被删除。如果给定两个参数，那么属于指定的 *domain* 和 URL *path* 的cookie将被删除。 如果给定三个参数，那么属于指定的 *domain* 、*path* 和 *name* 的cookie将被删除

​	如果不存在匹配的 cookie，则会引发 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}})。

## CookieJar.**clear_session_cookies**()

​	丢弃所有的会话 cookie。

​	丢弃所有 `discard` 属性为真值的已包含 cookie（通常是因为它们没有 `max-age` 或 `expires` cookie 属性，或者显式的 `discard` cookie 属性）。 对于交互式浏览器，会话的结束通常对应于关闭浏览器窗口。

​	请注意 `save()` 方法并不会保存会话的 cookie，除非你通过传入一个真值给 *ignore_discard* 参数来提出明确的要求。

[`FileCookieJar`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.FileCookieJar" >}}) 实现了下列附加方法:

## FileCookieJar.**save**(*filename=None*, *ignore_discard=False*, *ignore_expires=False*)

​	将 cookie 保存到文件。

​	基类会引发 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}})。 子类可以继续不实现该方法。

*filename* 为要用来保存 cookie 的文件名称。 如果未指定 *filename*，则会使用 `self.filename` (该属性默认为传给构造器的值，如果有传入的话)；如果 `self.filename` 为 [`None`]({{< ref "/library/constants#None" >}})，则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

*ignore_discard*: 即使设定了丢弃 cookie 仍然保存它们。 *ignore_expires*: 即使 cookie 已超期仍然保存它们

​	文件如果已存在则会被覆盖，这将清除其所包含的全部 cookie。 已保存的 cookie 可以使用 [`load()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.FileCookieJar.load" >}}) 或 [`revert()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.FileCookieJar.revert" >}}) 方法来恢复。

## FileCookieJar.**load**(*filename=None*, *ignore_discard=False*, *ignore_expires=False*)

​	从文件加载 cookie。

​	旧的 cookie 将被保留，除非是被新加载的 cookie 所覆盖。

​	其参数与 [`save()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.FileCookieJar.save" >}}) 的相同。

​	指定的文件必须为该类所能理解的格式，否则将引发 [`LoadError`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.LoadError" >}})。 也可能会引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}})，例如当文件不存在的时候。

> 在 3.3 版本发生变更: 过去触发的 [`IOError`]({{< ref "/library/exceptions#IOError" >}})，现在是 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 的别名。

## FileCookieJar.**revert**(*filename=None*, *ignore_discard=False*, *ignore_expires=False*)

​	清除所有 cookie 并从保存的文件重新加载 cookie。

[`revert()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.FileCookieJar.revert" >}}) 可以引发与 [`load()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.FileCookieJar.load" >}}) 相同的异常。 如果执行失败，对象的状态将不会被改变。

[`FileCookieJar`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.FileCookieJar" >}}) 实例具有下列公有属性:

## FileCookieJar.**filename**

​	默认的保存 cookie 的文件的文件名。 该属性可以被赋值。

## FileCookieJar.**delayload**

​	如为真值，则惰性地从磁盘加载 cookie。 该属性不应当被赋值。 这只是一个提示，因为它只会影响性能，而不会影响行为（除非磁盘中的 cookie 被改变了）。 [`CookieJar`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookieJar" >}}) 对象可能会忽略它。 任何包括在标准库中的 [`FileCookieJar`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.FileCookieJar" >}}) 类都不会惰性地加载 cookie。



## FileCookieJar 的子类及其与 Web 浏览器的协同

​	提供了以下 [`CookieJar`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookieJar" >}}) 子类用于读取和写入。

## *class* http.cookiejar.**MozillaCookieJar**(*filename=None*, *delayload=None*, *policy=None*)

​	一个能够以 Mozilla `cookies.txt` 文件格式（该格式也被 curl 和 Lynx 以及 Netscape 浏览器所使用）从硬盘加载和存储 cookie 的 [`FileCookieJar`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.FileCookieJar" >}})。

​备注
 

​	这会丢失有关 [**RFC 2965**](https://datatracker.ietf.org/doc/html/rfc2965.html) cookie 的信息，以及有关较新或非标准的 cookie 属性例如 `port`。

​	警告

 

​	在存储之前备份你的 cookie，如果你的 cookie 丢失/损坏会造成麻烦的话（有一些微妙的因素可能导致文件在加载/保存的往返过程中发生细微的变化）。

​	还要注意在 Mozilla 运行期间保存的 cookie 将可能被 Mozilla 清除。

## *class* http.cookiejar.**LWPCookieJar**(*filename=None*, *delayload=None*, *policy=None*)

​	一个能够以 libwww-perl 库的 `Set-Cookie3` 文件格式从磁盘加载和存储 cookie 的 [`FileCookieJar`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.FileCookieJar" >}})。 这适用于当你想以人类可读的文件来保存 cookie 的情况。

> 在 3.8 版本发生变更: 文件名形参支持 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。



## CookiePolicy 对象

​	实现了 [`CookiePolicy`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy" >}}) 接口的对象具有下列方法:

## CookiePolicy.**set_ok**(*cookie*, *request*)

​	返回指明是否应当从服务器接受 cookie 的布尔值。

*cookie* 是一个 [`Cookie`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.Cookie" >}}) 实例。 *request* 是一个实现了由 [`CookieJar.extract_cookies()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookieJar.extract_cookies" >}}) 的文档所定义的接口的对象。

## CookiePolicy.**return_ok**(*cookie*, *request*)

​	返回指明是否应当将 cookie 返回给服务器的布尔值。

*cookie* 是一个 [`Cookie`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.Cookie" >}}) 实例。 *request* 是一个实现了 [`CookieJar.add_cookie_header()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookieJar.add_cookie_header" >}}) 的文档所定义的接口的对象。

## CookiePolicy.**domain_return_ok**(*domain*, *request*)

​	对于给定的 cookie 域如果不应当返回 cookie 则返回 `False`。

​	此方法是一种优化操作。 它消除了检查每个具有特定域的 cookie 的必要性（这可能会涉及读取许多文件）。 从 [`domain_return_ok()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy.domain_return_ok" >}}) 和 [`path_return_ok()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy.path_return_ok" >}}) 返回真值并将所有工作留给 [`return_ok()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy.return_ok" >}})。

​	如果 [`domain_return_ok()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy.domain_return_ok" >}}) 为 cookie 域返回真值，则会为 cookie 路径调用 [`path_return_ok()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy.path_return_ok" >}})。 在其他情况下，则不会为该 cookie 域调用 [`path_return_ok()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy.path_return_ok" >}}) 和 [`return_ok()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy.return_ok" >}})。 如果 [`path_return_ok()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy.path_return_ok" >}}) 返回真值，则会调用 [`return_ok()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy.return_ok" >}}) 并附带 [`Cookie`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.Cookie" >}}) 对象本身以进行全面检查。 在其他情况下，都永远不会为该 cookie 路径调用 [`return_ok()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy.return_ok" >}})。

​	请注意 [`domain_return_ok()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy.domain_return_ok" >}}) 会针对每个 *cookie* 域被调用，而非只针对 *request* 域。 例如，该函数会针对 `".example.com"` 和 `"www.example.com"` 被调用，如果 request 域为 `"www.example.com"` 的话。 对于 [`path_return_ok()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy.path_return_ok" >}}) 也是如此。

*request* 参数与 [`return_ok()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy.return_ok" >}}) 的文档所说明的一致。

## CookiePolicy.**path_return_ok**(*path*, *request*)

​	对于给定的 cookie 路径如果不应当返回 cookie 返回 `False`。

​	请参阅 [`domain_return_ok()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy.domain_return_ok" >}}) 的文档。

​	除了实现上述方法，[`CookiePolicy`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy" >}}) 接口的实现还必须提供下列属性，指明应当使用哪种协议以及如何使用。 所有这些属性都可以被赋值。

## CookiePolicy.**netscape**

​	实现 Netscape 协议。

## CookiePolicy.**rfc2965**

​	实现 [**RFC 2965**](https://datatracker.ietf.org/doc/html/rfc2965.html) 协议。

## CookiePolicy.**hide_cookie2**

​	不要向请求添加 *Cookie2* 标头（此标头是提示服务器请求方能识别 [**RFC 2965**](https://datatracker.ietf.org/doc/html/rfc2965.html) cookie）。

​	定义 [`CookiePolicy`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy" >}}) 类的最适用方式是通过子类化 [`DefaultCookiePolicy`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.DefaultCookiePolicy" >}}) 并重写部分或全部上述的方法。 [`CookiePolicy`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy" >}}) 本身可被用作 '空策略' 以允许设置和接收所有的 cookie（但这没有什么用处）。



## DefaultCookiePolicy 对象

​	实现接收和返回 cookie 的标准规则。

[**RFC 2965**](https://datatracker.ietf.org/doc/html/rfc2965.html) 和 Netscape cookie 均被涵盖。 RFC 2965 处理默认关闭。

​	提供自定义策略的最容易方式是重写此类并在你重写的实现中添加你自己的额外检查之前调用其方法:

```
import http.cookiejar
class MyCookiePolicy(http.cookiejar.DefaultCookiePolicy):
    def set_ok(self, cookie, request):
        if not http.cookiejar.DefaultCookiePolicy.set_ok(self, cookie, request):
            return False
        if i_dont_want_to_store_this_cookie(cookie):
            return False
        return True
```

​	在实现 [`CookiePolicy`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy" >}}) 接口所要求的特性之外，该类还允许你阻止和允许特定的域设置和接收 cookie。 还有一些严格性开关允许你将相当宽松的 Netscape 协议规则收紧一点（代价是可能会阻止某些无害的 cookie）。

​	提供了域阻止名单和允许名单（默认都是关闭的）。 只有不存在于阻止列表且存在于允许列表（如果允许名单被启用）的域才能参与 cookie 的设置与返回。 请使用 *blocked_domains* 构造器参数，以及 `blocked_domains()` 和 `set_blocked_domains()` 方法（以及and the corresponding argument and methods for *allowed_domains* 的相应参数和方法）。 如果你设置了允许名单，你可以通过将其设为 [`None`]({{< ref "/library/constants#None" >}}) 来关闭它。

​	阻止名单或允许名单中不以点号开头的域名必须与要匹配的 cookie 域完全相等。 例如， `"example.com"` 将匹配阻止名单条目 `"example.com"`，但不匹配 `"www.example.com"`。 以点号开头的域名也能与更明确的域相匹配。 例如，`"www.example.com"` 和 `"www.coyote.example.com"` 将匹配 `".example.com"` (但不匹配 `"example.com"` 本身)。 IP 地址不在此例，而是必须完全匹配。 例如，如果 blocked_domains 包含 `"192.168.1.2"` 和 `".168.1.2"`，则会阻止 192.168.1.2，但不会阻止 193.168.1.2。

[`DefaultCookiePolicy`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.DefaultCookiePolicy" >}}) 实现了下列附加方法:

## DefaultCookiePolicy.**blocked_domains**()

​	返回被阻止域的序列（元组类型）。

## DefaultCookiePolicy.**set_blocked_domains**(*blocked_domains*)

​	设置被阻止域的序列。

## DefaultCookiePolicy.**is_blocked**(*domain*)

​	如果 *domain* 在设置或接受 cookie 的阻止列表中则返回 `True`。

## DefaultCookiePolicy.**allowed_domains**()

​	返回 [`None`]({{< ref "/library/constants#None" >}})，或者被允许域的序列（元组类型）。

## DefaultCookiePolicy.**set_allowed_domains**(*allowed_domains*)

​	设置被允许域的序列，或者为 [`None`]({{< ref "/library/constants#None" >}})。

## DefaultCookiePolicy.**is_not_allowed**(*domain*)

​	如果 *domain* 不在设置或接受 cookie 的允许列表中则返回 `True`。

[`DefaultCookiePolicy`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.DefaultCookiePolicy" >}}) 实例具有下列属性，它们都是基于同名的构造器参数来初始化的，并且都可以被赋值。

## DefaultCookiePolicy.**rfc2109_as_netscape**

​	如为真值，则请求 [`CookieJar`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookieJar" >}}) 实例将 [**RFC 2109**](https://datatracker.ietf.org/doc/html/rfc2109.html) cookie (即在带有 version 值为 1 的 cookie 属性的 *Set-Cookie* 标头中接收到的 cookie) 降级为 Netscape cookie: 即将 [`Cookie`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.Cookie" >}}) 实例的 version 属性设为 0。 默认值为 [`None`]({{< ref "/library/constants#None" >}})，在此情况下 RFC 2109 cookie 仅在s are downgraded if and only if [**RFC 2965**](https://datatracker.ietf.org/doc/html/rfc2965.html) 处理被关闭时才会被降级。 因此，RFC 2109 cookie 默认会被降级。

​	通用严格性开关:

## DefaultCookiePolicy.**strict_domain**

​	不允许网站设置带国家码顶级域的包含两部分的域名例如 `.co.uk`, `.gov.uk`, `.co.nz` 等。 此开关尚未十分完善，并不保证有效！

[**RFC 2965**](https://datatracker.ietf.org/doc/html/rfc2965.html) 协议严格性开关:

## DefaultCookiePolicy.**strict_rfc2965_unverifiable**

​	遵循针对不可验证事务的 [**RFC 2965**](https://datatracker.ietf.org/doc/html/rfc2965.html) 规则（不可验证事务通常是由重定向或请求发布在其它网站的图片导致的）。 如果该属性为假值，则 *永远不会* 基于可验证性而阻止 cookie。

​	Netscape 协议严格性开关:

## DefaultCookiePolicy.**strict_ns_unverifiable**

​	即便是对 Netscape cookie 也要应用 [**RFC 2965**](https://datatracker.ietf.org/doc/html/rfc2965.html) 规则。

## DefaultCookiePolicy.**strict_ns_domain**

​	指明针对 Netscape cookie 的域匹配规则的严格程度。 可接受的值见下文。

## DefaultCookiePolicy.**strict_ns_set_initial_dollar**

​	忽略 Set-Cookie 中的 cookie: 即名称前缀为 `'{TX-PL-LABEL}#x27;` 的标头。

## DefaultCookiePolicy.**strict_ns_set_path**

​	不允许设置路径与请求 URL 路径不匹配的 cookie。

`strict_ns_domain` 是一组旗标。 其值是通过或运算来构造的（例如，`DomainStrictNoDots|DomainStrictNonDomain` 表示同时设置两个旗标）。

## DefaultCookiePolicy.**DomainStrictNoDots**

​	当设置 cookie 是，'host prefix' 不可包含点号（例如 `www.foo.bar.com` 不能为 `.bar.com` 设置 cookie，因为 `www.foo` 包含了一个点号）。

## DefaultCookiePolicy.**DomainStrictNonDomain**

​	没有显式指明Cookies that did not explicitly specify a `domain` cookie 属性的 cookie 只能被返回给与设置 cookie 的域相同的域（例如 `spam.example.com` 不会是来自 `example.com` 的返回 cookie，如果该域名没有 `domain` cookie 属性的话）。

## DefaultCookiePolicy.**DomainRFC2965Match**

​	当设置 cookie 时，要求完整的 [**RFC 2965**](https://datatracker.ietf.org/doc/html/rfc2965.html) 域匹配。

​	下列属性是为方便使用而提供的，是上述旗标的几种最常用组合:

## DefaultCookiePolicy.**DomainLiberal**

​	等价于 0 (即所有上述 Netscape 域严格性旗标均停用)。

## DefaultCookiePolicy.**DomainStrict**

​	等价于 `DomainStrictNoDots|DomainStrictNonDomain`。

## Cookie 对象

[`Cookie`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.Cookie" >}}) 实例具有与各种 cookie 标准中定义的标准 cookie 属性大致对应的 Python 属性。 这并非一一对应，因为存在复杂的赋默认值的规则，因为 `max-age` 和 `expires` cookie 属性包含相同信息，也因为 [**RFC 2109**](https://datatracker.ietf.org/doc/html/rfc2109.html) cookie 可以被 [`http.cookiejar`]({{< ref "/library/internet/http_cookiejar#module-http.cookiejar" >}}) 从第 1 版 '降级为' 第 0 版 (Netscape) cookie。

​	对这些属性的赋值在 [`CookiePolicy`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.CookiePolicy" >}}) 方法的极少数情况以外应该都是不必要的。 该类不会强制内部一致性，因此如果这样做则你应当清楚自己在做什么。

## Cookie.**version**

​	整数或 [`None`]({{< ref "/library/constants#None" >}})。 Netscape cookie 的 [`version`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.Cookie.version" >}}) 值为 0。 [**RFC 2965**](https://datatracker.ietf.org/doc/html/rfc2965.html) 和 [**RFC 2109**](https://datatracker.ietf.org/doc/html/rfc2109.html) cookie 的 `version` cookie 属性值为 1。 但是，请注意 [`http.cookiejar`]({{< ref "/library/internet/http_cookiejar#module-http.cookiejar" >}}) 可以将 RFC 2109 cookie '降级' 为 Netscape cookie，在此情况下 [`version`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.Cookie.version" >}}) 值也为 0。

## Cookie.**name**

​	Cookie 名称（一个字符串）。

## Cookie.**value**

​	Cookie 值（一个字符串），或为 [`None`]({{< ref "/library/constants#None" >}})。

## Cookie.**port**

​	代表一个端口或一组端口（例如 '80' 或 '80,8080'）的字符串，或为 [`None`]({{< ref "/library/constants#None" >}})。

## Cookie.**domain**

​	Cookie 域（一个字符串）。

## Cookie.**path**

​	Cookie 路径 (字符串类型，例如 `'/acme/rocket_launchers'`)。

## Cookie.**secure**

​	如果 cookie 应当只能通过安全连接返回则为 `True`。

## Cookie.**expires**

​	整数类型的过期时间，以距离 Unix 纪元的秒数表示，或者为 [`None`]({{< ref "/library/constants#None" >}})。 另请参阅 [`is_expired()`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.Cookie.is_expired" >}}) 方法。

## Cookie.**discard**

​	如果是会话 cookie 则为 `True`。

## Cookie.**comment**

​	来自服务器的解释此 cookie 功能的字符串形式的注释，或者为 [`None`]({{< ref "/library/constants#None" >}})。

## Cookie.**comment_url**

​	链接到来自服务器的解释此 cookie 功能的注释的 URL，或者为 [`None`]({{< ref "/library/constants#None" >}})。

## Cookie.**rfc2109**

​	如果 cookie 是作为 [**RFC 2109**](https://datatracker.ietf.org/doc/html/rfc2109.html) cookie 被接收（即该 cookie 是在 *Set-Cookie* 标头中送达，且该标头的 Version cookie 属性的值为 1）则为 `True`。 之所以要提供该属性是因为 [`http.cookiejar`]({{< ref "/library/internet/http_cookiejar#module-http.cookiejar" >}}) 可能会从 RFC 2109 cookies '降级' 为 Netscape cookie，在此情况下 [`version`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.Cookie.version" >}}) 值为 0。

## Cookie.**port_specified**

​	如果服务器显式地指定了一个端口或一组端口（在 *Set-Cookie* / *Set-Cookie2* 标头中）则为 `True`。

## Cookie.**domain_specified**

​	如果服务器显式地指定了一个域则为 `True`。

## Cookie.**domain_initial_dot**

​	该属性为 `True` 表示服务器显式地指定了以一个点号 (`'.'`) 打头的域。

​	Cookie 可能还有额外的非标准 cookie 属性。 这些属性可以通过下列方法来访问:

## Cookie.**has_nonstandard_attr**(*name*)

​	如果 cookie 具有相应名称的 cookie 属性则返回 `True`。

## Cookie.**get_nonstandard_attr**(*name*, *default=None*)

​	如果 cookie 具有相应名称的 cookie 属性，则返回其值。 否则，返回 *default*。

## Cookie.**set_nonstandard_attr**(*name*, *value*)

​	设置指定名称的 cookie 属性的值。

[`Cookie`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.Cookie" >}}) 类还定义了下列方法:

## Cookie.**is_expired**(*now=None*)

​	如果 cookie 传入了服务器请求其所应过期的时间则为 `True`。 如果给出 *now* 值（距离 Unix 纪元的秒数），则返回在指定的时间 cookie 是否已过期。

## 例子

​	第一个例子显示了 [`http.cookiejar`]({{< ref "/library/internet/http_cookiejar#module-http.cookiejar" >}}) 的最常见用法:

```
import http.cookiejar, urllib.request
cj = http.cookiejar.CookieJar()
opener = urllib.request.build_opener(urllib.request.HTTPCookieProcessor(cj))
r = opener.open("http://example.com/")
```

​	这个例子演示了如何使用你的 Netscape, Mozilla 或 Lynx cookie 打开一个 URL (假定 cookie 文件位置采用 Unix/Netscape 惯例):

```
import os, http.cookiejar, urllib.request
cj = http.cookiejar.MozillaCookieJar()
cj.load(os.path.join(os.path.expanduser("~"), ".netscape", "cookies.txt"))
opener = urllib.request.build_opener(urllib.request.HTTPCookieProcessor(cj))
r = opener.open("http://example.com/")
```

​	下一个例子演示了 [`DefaultCookiePolicy`]({{< ref "/library/internet/http_cookiejar#http.cookiejar.DefaultCookiePolicy" >}}) 的使用。 启用 [**RFC 2965**](https://datatracker.ietf.org/doc/html/rfc2965.html) cookie，在设置和返回 Netscape cookie 时更严格地限制域，以及阻止某些域设置 cookie 或返回它们:

```
import urllib.request
from http.cookiejar import CookieJar, DefaultCookiePolicy
policy = DefaultCookiePolicy(
    rfc2965=True, strict_ns_domain=Policy.DomainStrict,
    blocked_domains=["ads.net", ".ads.net"])
cj = CookieJar(policy)
opener = urllib.request.build_opener(urllib.request.HTTPCookieProcessor(cj))
r = opener.open("http://example.com/")
```
