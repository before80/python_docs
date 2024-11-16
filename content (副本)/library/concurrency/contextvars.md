+++
title = "contextvars --- 上下文变量"
date = 2024-11-15T12:21:05+08:00
weight = 80
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/contextvars.html](https://docs.python.org/zh-cn/3.13/library/contextvars.html)
>
> 收录该文档的时间：`2024-11-15T12:21:05+08:00`

# `contextvars` --- 上下文变量

------

​	本模块提供了相关API用于管理、存储和访问上下文相关的状态。 [`ContextVar`]({{< ref "/library/concurrency/contextvars#contextvars.ContextVar" >}}) 类用于声明 *上下文变量* 并与其一起使用。函数 [`copy_context()`]({{< ref "/library/concurrency/contextvars#contextvars.copy_context" >}}) 和类 [`Context`]({{< ref "/library/concurrency/contextvars#contextvars.Context" >}}) 用于管理当前上下文和异步框架中。

​	当在并发代码中使用时，有状态的上下文管理器应当使用上下文变量而不是 [`threading.local()`]({{< ref "/library/concurrency/threading#threading.local" >}}) 以防止它们的状态意外地泄露到其他代码中。

​	更多信息参见 [**PEP 567**](https://peps.python.org/pep-0567/) 。

> Added in version 3.7.
>

## 上下文变量

## *class* contextvars.**ContextVar**(*name*[, ***, *default*])

​	此类用于声明一个新的上下文变量，如:

```
var: ContextVar[int] = ContextVar('var', default=42)
```

*name* 参数用于内省和调试，必需。

​	调用 [`ContextVar.get()`]({{< ref "/library/concurrency/contextvars#contextvars.ContextVar.get" >}}) 时，如果上下文中没有找到此变量的值，则返回可选的仅命名参数 *default* 。

**重要：** 上下文变量应该在顶级模块中创建，且永远不要在闭包中创建。 [`Context`]({{< ref "/library/concurrency/contextvars#contextvars.Context" >}}) 对象拥有对上下文变量的强引用，这可以让上下文变量被垃圾收集器正确回收。

## **name**

​	上下文变量的名称，只读属性。

> Added in version 3.7.1.
>

## **get**([*default*])

​	返回当前上下文中此上下文变量的值。

​	如果当前上下文中此变量没有值，则此方法会:

- 如果提供了 *default*，返回其值；或者
- 返回上下文变量本身的默认值， 如果创建此上下文变量时提供了默认值；或者
- 抛出 [`LookupError`]({{< ref "/library/exceptions#LookupError" >}}) 异常。

## **set**(*value*)

​	调用此方法设置上下文变量在当前上下文中的值。

​	必选参数 *value* 是上下文变量的新值。

​	返回一个 [`Token`]({{< ref "/library/concurrency/contextvars#contextvars.Token" >}}) 对象，可通过 [`ContextVar.reset()`]({{< ref "/library/concurrency/contextvars#contextvars.ContextVar.reset" >}}) 方法将上下文变量还原为之前某个状态。

## **reset**(*token*)

​	将上下文变量重置为调用 [`ContextVar.set()`]({{< ref "/library/concurrency/contextvars#contextvars.ContextVar.set" >}}) 之前、创建 *token* 时候的状态。

​	例如：

```
var = ContextVar('var')

token = var.set('new value')
# 使用 'var' 的代码；var.get() 将返回 'new value'。
var.reset(token)

# 在重置调用之后 var 将不再有值，
# 因此 var.get() 将引发一个 LookupError。
```

## *class* contextvars.**Token**

[`ContextVar.set()`]({{< ref "/library/concurrency/contextvars#contextvars.ContextVar.set" >}}) 方法返回 *Token* 对象。此对象可以传递给 [`ContextVar.reset()`]({{< ref "/library/concurrency/contextvars#contextvars.ContextVar.reset" >}}) 方法用于将上下文变量还原为调用 *set* 前的状态。

## **var**

​	只读属性。指向创建此 token 的 [`ContextVar`]({{< ref "/library/concurrency/contextvars#contextvars.ContextVar" >}}) 对象。

## **old_value**

​	一个只读属性。 会被设为在创建此令牌的 [`ContextVar.set()`]({{< ref "/library/concurrency/contextvars#contextvars.ContextVar.set" >}}) 方法调用之前该变量所具有的值。 如果调用之前变量没有设置值则它会指令 [`Token.MISSING`]({{< ref "/library/concurrency/contextvars#contextvars.Token.MISSING" >}})。

## **MISSING**

[`Token.old_value`]({{< ref "/library/concurrency/contextvars#contextvars.Token.old_value" >}}) 会用到的一个标记对象。

## 手动上下文管理

## contextvars.**copy_context**()

​	返回当前上下文中 [`Context`]({{< ref "/library/concurrency/contextvars#contextvars.Context" >}}) 对象的拷贝。

​	以下代码片段会获取当前上下文的拷贝并打印设置到其中的所有变量及其值:

```
ctx: Context = copy_context()
print(list(ctx.items()))
```

​	此函数具有 *O*(1) 复杂度，也就是说对于只包含几个上下文变量和很多上下文变量的情况运行速度是相同的。

## *class* contextvars.**Context**

[`ContextVars`]({{< ref "/library/concurrency/contextvars#contextvars.ContextVar" >}}) 与其值的映射。

`Context()` 创建一个不包含任何值的空上下文。如果要获取当前上下文的拷贝，使用 [`copy_context()`]({{< ref "/library/concurrency/contextvars#contextvars.copy_context" >}}) 函数。

​	Each thread has its own effective stack of `Context` objects. The [current context]({{< ref "/glossary/idx#term-current-context" >}}) is the `Context` object at the top of the current thread's stack. All `Context` objects in the stacks are considered to be *entered*.

*Entering* a context, which can be done by calling its [`run()`]({{< ref "/library/concurrency/contextvars#contextvars.Context.run" >}}) method, makes the context the current context by pushing it onto the top of the current thread's context stack.

*Exiting* from the current context, which can be done by returning from the callback passed to the [`run()`]({{< ref "/library/concurrency/contextvars#contextvars.Context.run" >}}) method, restores the current context to what it was before the context was entered by popping the context off the top of the context stack.

​	Since each thread has its own context stack, [`ContextVar`]({{< ref "/library/concurrency/contextvars#contextvars.ContextVar" >}}) objects behave in a similar fashion to [`threading.local()`]({{< ref "/library/concurrency/threading#threading.local" >}}) when values are assigned in different threads.

​	Attempting to enter an already entered context, including contexts entered in other threads, raises a [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}}).

​	After exiting a context, it can later be re-entered (from any thread).

​	Any changes to [`ContextVar`]({{< ref "/library/concurrency/contextvars#contextvars.ContextVar" >}}) values via the [`ContextVar.set()`]({{< ref "/library/concurrency/contextvars#contextvars.ContextVar.set" >}}) method are recorded in the current context. The [`ContextVar.get()`]({{< ref "/library/concurrency/contextvars#contextvars.ContextVar.get" >}}) method returns the value associated with the current context. Exiting a context effectively reverts any changes made to context variables while the context was entered (if needed, the values can be restored by re-entering the context).

​	Context 实现了 [`collections.abc.Mapping`]({{< ref "/library/datatypes/collections_abc#collections.abc.Mapping" >}}) 接口。

## **run**(*callable*, **args*, ***kwargs*)

​	Enters the Context, executes `callable(*args, **kwargs)`, then exits the Context. Returns *callable*'s return value, or propagates an exception if one occurred.

​	Example:

```
import contextvars

var = contextvars.ContextVar('var')
var.set('spam')
print(var.get())  # 'spam'

ctx = contextvars.copy_context()

def main():
    # 'var' was set to 'spam' before
    # calling 'copy_context()' and 'ctx.run(main)', so:
    print(var.get())  # 'spam'
    print(ctx[var])  # 'spam'

    var.set('ham')

    # Now, after setting 'var' to 'ham':
    print(var.get())  # 'ham'
    print(ctx[var])  # 'ham'

# Any changes that the 'main' function makes to 'var'
# will be contained in 'ctx'.
ctx.run(main)

# The 'main()' function was run in the 'ctx' context,
# so changes to 'var' are contained in it:
print(ctx[var])  # 'ham'

# However, outside of 'ctx', 'var' is still set to 'spam':
print(var.get())  # 'spam'
```

## **copy**()

​	返回此上下文对象的浅拷贝。

## **var in context**

​	如果 *context* 中含有名称为 *var* 的变量，返回 `True`， 否则返回 `False`。

## **context[var]**

​	返回名称为 *var* 的 [`ContextVar`]({{< ref "/library/concurrency/contextvars#contextvars.ContextVar" >}}) 变量。如果上下文对象中不包含这个变量，则抛出 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}}) 异常。

## **get**(*var*[, *default*])

​	如果 *var* 在上下文对象中具有值则返回 *var* 的值。 在其他情况下返回 *default*。 如果未给出 *default* 则返回 `None`。

## **iter(context)**

​	返回一个存储在上下文对象中的变量的迭代器。

## **len(proxy)**

​	返回上下文对象中所设的变量的数量。

## **keys**()

​	返回上下文对象中的所有变量的列表。

## **values**()

​	返回上下文对象中所有变量值的列表。

## **items**()

​	返回包含上下文对象中所有变量及其值的 2 元组的列表。

## asyncio 支持

​	上下文变量在 [`asyncio`]({{< ref "/library/ipc/asyncio#module-asyncio" >}}) 中有原生的支持并且无需任何额外配置即可被使用。 例如，以下是一个简单的回显服务器，它使用上下文变量来让远程客户端的地址在处理该客户端的 Task 中可用:

```
import asyncio
import contextvars

client_addr_var = contextvars.ContextVar('client_addr')

def render_goodbye():
    # 可直接访问当前处理的客户端地址
    # 而无需显式地将其传给此函数。

    client_addr = client_addr_var.get()
    return f'Good bye, client @ {client_addr}\r\n'.encode()

async def handle_request(reader, writer):
    addr = writer.transport.get_extra_info('socket').getpeername()
    client_addr_var.set(addr)

    # 现在将可以在我们所调用的任何代码中通过
    # 'client_addr_var.get()' 调用来获取客户端地址。

    while True:
        line = await reader.readline()
        print(line)
        if not line.strip():
            break

    writer.write(b'HTTP/1.1 200 OK\r\n')  # 状态行
    writer.write(b'\r\n')  # 标头
    writer.write(render_goodbye())  # 消息体
    writer.close()

async def main():
    srv = await asyncio.start_server(
        handle_request, '127.0.0.1', 8081)

    async with srv:
        await srv.serve_forever()

asyncio.run(main())

# 要进行测试你可以使用 telnet 或 curl：
#     telnet 127.0.0.1 8081
#     curl 127.0.0.1:8081
```
