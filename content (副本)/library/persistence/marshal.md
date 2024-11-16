+++
title = "marshal --- 内部 Python 对象序列化"
date = 2024-11-15T11:57:40+08:00
weight = 30
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/marshal.html](https://docs.python.org/zh-cn/3.13/library/marshal.html)
>
> 收录该文档的时间：`2024-11-15T11:57:40+08:00`

# `marshal` --- 内部 Python 对象序列化

------

​	此模块包含一些能以二进制格式来读写 Python 值的函数。 这种格式是 Python 专属的，但是独立于特定的机器架构（即你可以在一台 PC 上写入某个 Python 值，将文件传到一台 Mac 上并在那里读取它）。 这种格式的细节有意不带文档说明；可可能在不同 Python 版本之间发生改变（但这种情况极少发生）。 [[1\]]({{< ref "/library/persistence/marshal#id2" >}})

​	这不是一个通用的“持久化”模块。 对于通用的持久化以及通过 RPC 调用传递 Python 对象，请参阅 [`pickle`]({{< ref "/library/persistence/pickle#module-pickle" >}}) 和 [`shelve`]({{< ref "/library/persistence/shelve#module-shelve" >}}) 等模块。 提供 [`marshal`]({{< ref "/library/persistence/marshal#module-marshal" >}}) 模块主要是为了支持读写 `.pyc` 形式“伪编译”代码的 Python 模块。 因此，Python 维护者保留在必要时以不向下兼容的方式修改 marshal 格式的权利。 代码对象的格式在 Python 版本之间不保证兼容，即使格式的版本是相同的。 在不正确的 Python 版本中反序列化代码对象是未定义的行为。 如果你要序列化和反序列化 Python 对象，请改用 [`pickle`]({{< ref "/library/persistence/pickle#module-pickle" >}}) 模块 —— 具有类似的性能，保证版本独立性，并且 pickle 还支持比 marshal 更丰富种类的对象。

​	警告

 

[`marshal`]({{< ref "/library/persistence/marshal#module-marshal" >}}) 模块对于错误或恶意构建的数据来说是不安全的。 永远不要 unmarshal 来自不受信任的或未经验证的来源的数据。

​	并非所有 Python 对象类型都受到支持；通常，此模块只能写入和读取不依赖于特定 Python 调用发起方式的对象值。 下列类型是受支持的：布尔对象、整数、浮点数、复数、字符串、字节串、字节数组、元组、列表、集合、冻结集合、字典以及代码对象（如果 *allow_code* 为真值），需要了解的一点是元组、列表、集合、冻结集合和字典只在其所包含的值也受支持时才是受支持的。 单例对象 [`None`]({{< ref "/library/constants#None" >}}), [`Ellipsis`]({{< ref "/library/constants#Ellipsis" >}}) 和 [`StopIteration`]({{< ref "/library/exceptions#StopIteration" >}}) 也可以执行 marshall 和 unmarshall。 对于 *version* 小于 3 的格式，将无法写入递归的列表、集合以及字典（见下文）。

​	有些函数可以读/写文件，还有些函数可以操作字节类对象。

​	这个模块定义了以下函数：

## marshal.**dump**(*value*, *file*, *version=version*, */*, ***, *allow_code=True*)

​	向打开的文件写入值。 值必须为受支持的类型。 文件必须为可写的 [binary file]({{< ref "/glossary/idx#term-binary-file" >}})。

​	如果值具有（或其包含的对象具有）不受支持的类型，则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 异常 --- 但还是会向文件写入垃圾数据。 对象将不能使用 [`load()`]({{< ref "/library/persistence/marshal#marshal.load" >}}) 正确地重新读取。 [代码对象]({{< ref "/reference/datamodel#code-objects" >}}) 仅在 *allow_code* 为真值时受到支持。

*version* 参数指明 `dump` 应当使用的数据格式（见下文）。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `marshal.dumps` 并附带参数 `value`, `version`。

*在 3.13 版本发生变更:* 增加了 *allow_code* 形参。

## marshal.**load**(*file*, */*, ***, *allow_code=True*)

​	从打开的文件读取一个值并返回它。 如果没有读取到有效的值（例如由于数据具有来自不同 Python 版本的不兼容的 marshal 格式），则会引发 [`EOFError`]({{< ref "/library/exceptions#EOFError" >}}), [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 或 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。 [代码对象]({{< ref "/reference/datamodel#code-objects" >}}) 仅在 *allow_code* 为真值时受到支持。 文件必须为可读的 [binary file]({{< ref "/glossary/idx#term-binary-file" >}})。

​	引发一个不带参数的 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `marshal.load`。

​	备注

 

​	如果通过 [`dump()`]({{< ref "/library/persistence/marshal#marshal.dump" >}}) marshal 了一个包含不受支持类型的对象，[`load()`]({{< ref "/library/persistence/marshal#marshal.load" >}}) 将为不可 marshal 的类型替换 `None`。

*在 3.10 版本发生变更:* 使用此调用为每个代码对象引发一个 `code.__new__` 审计事件。 现在它会为整个载入操作引发单个 `marshal.load` 事件。

*在 3.13 版本发生变更:* 增加了 *allow_code* 形参。

## marshal.**dumps**(*value*, *version=version*, */*, ***, *allow_code=True*)

​	返回将通过 `dump(value, file)` 写入到文件的字节串对象。 值必须是受支持的类型。 如果值具有（或其包含的对象具有）不受支持的类型则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 异常。 [代码对象]({{< ref "/reference/datamodel#code-objects" >}}) 仅在 *allow_code* 为真值时受到支持。

*version* 参数指明 `dumps` 应当使用的数据类型（见下文）。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `marshal.dumps` 并附带参数 `value`, `version`。

*在 3.13 版本发生变更:* 增加了 *allow_code* 形参。

## marshal.**loads**(*bytes*, */*, ***, *allow_code=True*)

​	将 [bytes-like object]({{< ref "/glossary/idx#term-bytes-like-object" >}}) 转换为一个值。 如果找不到有效的值，则会引发 [`EOFError`]({{< ref "/library/exceptions#EOFError" >}}), [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 或 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。 [代码对象]({{< ref "/reference/datamodel#code-objects" >}}) 仅在 *allow_code* 为真值时受支持。 输入的额外字节串会被忽略。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `marshal.loads` 并附带参数 `bytes`。

*在 3.10 版本发生变更:* 使用此调用为每个代码对象引发一个 `code.__new__` 审计事件。 现在它会为整个载入操作引发单个 `marshal.loads` 事件。

*在 3.13 版本发生变更:* 增加了 *allow_code* 形参。

​	此外，还定义了以下常量：

## marshal.**version**

​	指明模块所使用的格式。 第 0 版为历史格式，第 1 版为共享固化的字符串，第 2 版对浮点数使用二进制格式。 第 3 版添加了对于对象实例化和递归的支持。 目前使用的为第 4 版。

​	备注

[[1]({{< ref "/library/persistence/marshal#id1" >}})]

​	此模块的名称来源于 Modula-3 (及其他语言) 的设计者所使用的术语，他们使用术语 "marshal" 来表示以自包含的形式传输数据。 严格地说，将数据从内部形式转换为外部形式 (例如用于 RPC 缓冲区) 称为 "marshal" 而其逆过程则称为 "unmarshal"。
