+++
title = "sys.monitoring --- 执行事件监测"
date = 2024-11-15T21:12:39+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/sys.monitoring.html](https://docs.python.org/zh-cn/3.13/library/sys.monitoring.html)
>
> 收录该文档的时间：`2024-11-15T21:12:39+08:00`

# `sys.monitoring` --- 执行事件监测

> Added in version 3.12.
>

------

​备注
 

[`sys.monitoring`]({{< ref "/library/python/sys_monitoring#module-sys.monitoring" >}}) 是 [`sys`]({{< ref "/library/python/sys#module-sys" >}}) 模块内部的一个命名空间，而不是一个独立模块，因此不需要 `import sys.monitoring`，只要简单地 `import sys` 然后使用 `sys.monitoring`。

​	这个命名空间提供了对于激活和控制事件监控所需的函数和常量的访问。

​	在程序执行过程中，会发生对于监控执行的工具来说值得关注的事件。 [`sys.monitoring`]({{< ref "/library/python/sys_monitoring#module-sys.monitoring" >}}) 命名空间提供了在相应事件发生时接收回调的操作方式。

​	monitoring API由三个部分组成：

- [Tool identifiers]({{< ref "/library/python/sys_monitoring#tool-identifiers" >}})
- [Events]({{< ref "/library/python/sys_monitoring#events" >}})
- [回调]({{< ref "/library/python/sys_monitoring#callbacks" >}})

## 工具标识符

​	工具标识符是一个整数及其所关联的名称。 工具标识符被用来防止工具之间的相互干扰并允许同时操作多个工作。 目前工具是完全独立的且不能被用于相互监控。 这一限制在将来可能会被取消。

​	在注册或激活事件之前，工具应选择一个标识符。 标识符是 0 到 5 的开区间内的整数。

### 注册和使用工具

## sys.monitoring.**use_tool_id**(*tool_id: [int]({{< ref "/library/functions#int" >}})*, *name: [str]({{< ref "/library/stdtypes#str" >}})*, */*) → [None]({{< ref "/library/constants#None" >}})

​	必须在 *tool_id* 可被使用之前调用。 *tool_id* 必须在 0 到 5 的开区间内。 如果 *tool_id* 已被使用则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

## sys.monitoring.**free_tool_id**(*tool_id: [int]({{< ref "/library/functions#int" >}})*, */*) → [None]({{< ref "/library/constants#None" >}})

​	应当在一个工具不再需要 *tool_id* 时被调用。

​备注
 

[`free_tool_id()`]({{< ref "/library/python/sys_monitoring#sys.monitoring.free_tool_id" >}}) 将不会禁用关联到 *tool_id* 的全局或局部事件，也不会注销任何回调函数。 此函数仅被设计用来通知虚拟机特定的 *tool_id* 已不再被使用。

## sys.monitoring.**get_tool**(*tool_id: [int]({{< ref "/library/functions#int" >}})*, */*) → [str]({{< ref "/library/stdtypes#str" >}}) | [None]({{< ref "/library/constants#None" >}})

​	如果 *tool_id* 已被使用则返回工具名称，否则返回 `None`。 *tool_id* 取值必须在 0 至 5 的开区间内。

​	虚拟机在处理事件时对所有 ID 都一视同仁，但为便于工具之间的协作而预定义了下列 ID:

```
sys.monitoring.DEBUGGER_ID = 0
sys.monitoring.COVERAGE_ID = 1
sys.monitoring.PROFILER_ID = 2
sys.monitoring.OPTIMIZER_ID = 5
```

## 事件

​	以下事件是受支持的：

## sys.monitoring.events.**BRANCH**

​	条件分支被采用（或不采用）。

## sys.monitoring.events.**CALL**

​	Python 代码中的调用（事件发生在调用之前）。

## sys.monitoring.events.**C_RAISE**

​	从任意可调用对象引发的异常。 Python 函数除外（事件发生在退出之后）。

## sys.monitoring.events.**C_RETURN**

​	从任意可调用对象返回，Python 函数除外（事件在返回之后发生）。

## sys.monitoring.events.**EXCEPTION_HANDLED**

​	一个异常被处理。

## sys.monitoring.events.**INSTRUCTION**

​	一个 VM 指令即将被执行。

## sys.monitoring.events.**JUMP**

​	在控制流图中进行一次无条件的跳转。

## sys.monitoring.events.**LINE**

​	一条与之前指令行号不同的指令即将被执行。

## sys.monitoring.events.**PY_RESUME**

​	恢复执行一个 Python 函数（用于生成器和协程函数），`throw()` 调用除外。

## sys.monitoring.events.**PY_RETURN**

​	从一个 Python 函数返回（在返回之前立即发生，被调用方的帧将在栈中）。

## sys.monitoring.events.**PY_START**

​	开始一个 Python 函数（在调用之后立即发生，被调用方的帧将在栈中）

## sys.monitoring.events.**PY_THROW**

​	一个 Python 函数由 `throw()` 调用恢复执行。

## sys.monitoring.events.**PY_UNWIND**

​	在异常解除期间从一个 Python函数退出。

## sys.monitoring.events.**PY_YIELD**

​	从一个 Python 函数产出数据（在产出之前立即发生，被调用方的帧将在栈中）。

## sys.monitoring.events.**RAISE**

​	一个异常被引发，导致 [`STOP_ITERATION`]({{< ref "/library/python/sys_monitoring#monitoring-event-STOP_ITERATION" >}}) 事件的异常除外。

## sys.monitoring.events.**RERAISE**

​	一个异常被重新引发，例如在 [`finally`]({{< ref "/reference/compound_stmts#finally" >}}) 代码块结束的时候。

## sys.monitoring.events.**STOP_ITERATION**

​	一个 [`StopIteration`]({{< ref "/library/exceptions#StopIteration" >}}) 被人工引发；参见 [the STOP_ITERATION event]({{< ref "/library/python/sys_monitoring#the-stop-iteration-event" >}})。

​	将来可能会添加更多事件。

​	这些事件都是 `sys.monitoring.events` 命名空间的属性。 每个事件用整数常量的 2 次幂来表示。 要定义一组事件，只需对多个单独事件执行按位或运算即可。 例如，要同时指定 [`PY_RETURN`]({{< ref "/library/python/sys_monitoring#monitoring-event-PY_RETURN" >}}) 和 [`PY_START`]({{< ref "/library/python/sys_monitoring#monitoring-event-PY_START" >}}) 事件，则使用表达式 `PY_RETURN | PY_START`。

## sys.monitoring.events.**NO_EVENTS**

​	代表 `0` 的别名以便用户可以这样执行显式比较:

```
if get_events(DEBUGGER_ID) == NO_EVENTS:
    ...
```

​	事件被分为三组：

### 本地事件

​	本地事件与程序的正常执行相关联并且发生在明确定义的位置上。 所有本地事件都可以被禁用。 本地事件包括：

- [`PY_START`]({{< ref "/library/python/sys_monitoring#monitoring-event-PY_START" >}})
- [`PY_RESUME`]({{< ref "/library/python/sys_monitoring#monitoring-event-PY_RESUME" >}})
- [`PY_RETURN`]({{< ref "/library/python/sys_monitoring#monitoring-event-PY_RETURN" >}})
- [`PY_YIELD`]({{< ref "/library/python/sys_monitoring#monitoring-event-PY_YIELD" >}})
- [`CALL`]({{< ref "/library/python/sys_monitoring#monitoring-event-CALL" >}})
- [`LINE`]({{< ref "/library/python/sys_monitoring#monitoring-event-LINE" >}})
- [`INSTRUCTION`]({{< ref "/library/python/sys_monitoring#monitoring-event-INSTRUCTION" >}})
- [`JUMP`]({{< ref "/library/python/sys_monitoring#monitoring-event-JUMP" >}})
- [`BRANCH`]({{< ref "/library/python/sys_monitoring#monitoring-event-BRANCH" >}})
- [`STOP_ITERATION`]({{< ref "/library/python/sys_monitoring#monitoring-event-STOP_ITERATION" >}})

### 辅助事件

​	辅助事件可以像其他事件一样被监视，但是由另一个事件来控制：

- [`C_RAISE`]({{< ref "/library/python/sys_monitoring#monitoring-event-C_RAISE" >}})
- [`C_RETURN`]({{< ref "/library/python/sys_monitoring#monitoring-event-C_RETURN" >}})

[`C_RETURN`]({{< ref "/library/python/sys_monitoring#monitoring-event-C_RETURN" >}}) 和 [`C_RAISE`]({{< ref "/library/python/sys_monitoring#monitoring-event-C_RAISE" >}}) 事件是由 [`CALL`]({{< ref "/library/python/sys_monitoring#monitoring-event-CALL" >}}) 事件控制的。 [`C_RETURN`]({{< ref "/library/python/sys_monitoring#monitoring-event-C_RETURN" >}}) 和 [`C_RAISE`]({{< ref "/library/python/sys_monitoring#monitoring-event-C_RAISE" >}}) 事件只会在相应的 [`CALL`]({{< ref "/library/python/sys_monitoring#monitoring-event-CALL" >}}) 事件被监控时才能被看到。

### 其他事件

​	其他事件不一定与程序中的特定位置相关联并且不能被单独禁用。

​	可以被监视的其他事件包括：

- [`PY_THROW`]({{< ref "/library/python/sys_monitoring#monitoring-event-PY_THROW" >}})
- [`PY_UNWIND`]({{< ref "/library/python/sys_monitoring#monitoring-event-PY_UNWIND" >}})
- [`RAISE`]({{< ref "/library/python/sys_monitoring#monitoring-event-RAISE" >}})
- [`EXCEPTION_HANDLED`]({{< ref "/library/python/sys_monitoring#monitoring-event-EXCEPTION_HANDLED" >}})

### STOP_ITERATION 事件

[**PEP 380**](https://peps.python.org/pep-0380/#use-of-stopiteration-to-return-values) 规定了当从生成器或协程返回值时可引发 [`StopIteration`]({{< ref "/library/exceptions#StopIteration" >}}) 异常。 不过，这是一种非常低效的返回值的方式，因此某些 Python 实现，比如 CPython 3.12+，只有在异常对其他代码可见时才会引发它。

​	为允许工具监视真正的异常而不会拖慢生成器和协程的运行，解释器提供了 [`STOP_ITERATION`]({{< ref "/library/python/sys_monitoring#monitoring-event-STOP_ITERATION" >}}) 事件。 [`STOP_ITERATION`]({{< ref "/library/python/sys_monitoring#monitoring-event-STOP_ITERATION" >}}) 可以被局部禁用，这与 [`RAISE`]({{< ref "/library/python/sys_monitoring#monitoring-event-RAISE" >}}) 不同。

## 开启和关闭事件

​	要监视一个事件，它必须被开启并注册相应的回调函数。 可以通过将事件设置为全局的或针对特定代码对象的来开启或关闭事件。

### 全局设置事件

​	通过修改被监视的事件集可以对事件进行全局控制。

## sys.monitoring.**get_events**(*tool_id: [int]({{< ref "/library/functions#int" >}})*, */*) → [int]({{< ref "/library/functions#int" >}})

​	返回代表所有活动事件的 `int`。

## sys.monitoring.**set_events**(*tool_id: [int]({{< ref "/library/functions#int" >}})*, *event_set: [int]({{< ref "/library/functions#int" >}})*, */*) → [None]({{< ref "/library/constants#None" >}})

​	激活在 *event_set* 中设置的所有事件。 如果 *tool_id* 未被使用则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

​	在默认情况下没有被激活的事件。

### 针对特定代码对象的事件

​	事件也可以基于每个代码对象来控制。 下面定义的接受一个 [`types.CodeType`]({{< ref "/library/datatypes/types#types.CodeType" >}}) 的函数应当准备好接受来自不是在 Python 中定义的类似对象 (参见 [监控 C API]({{< ref "/c_api/monitoring#c-api-monitoring" >}}))。

## sys.monitoring.**get_local_events**(*tool_id: [int]({{< ref "/library/functions#int" >}})*, *code: [CodeType]({{< ref "/library/datatypes/types#types.CodeType" >}})*, */*) → [int]({{< ref "/library/functions#int" >}})

​	返回 *code* 的所有局部事件

## sys.monitoring.**set_local_events**(*tool_id: [int]({{< ref "/library/functions#int" >}})*, *code: [CodeType]({{< ref "/library/datatypes/types#types.CodeType" >}})*, *event_set: [int]({{< ref "/library/functions#int" >}})*, */*) → [None]({{< ref "/library/constants#None" >}})

​	激活在 *event_set* 中设置的针对 *code* 的所有局部事件。 如果 *tool_id* 未被使用则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

​	局部事件将添加到全局事件中，但不会屏蔽全局事件。 换句话说，所有全局事件都会为代码对象触发，无论是否有局部事件。

### 禁用事件

## sys.monitoring.**DISABLE**

​	一个可从回调函数返回以禁用当前代码位置上的事件的特殊值。

​	可从回调函数返回 [`sys.monitoring.DISABLE`]({{< ref "/library/python/sys_monitoring#sys.monitoring.DISABLE" >}}) 以禁用特定代码位置上的局部事件。 这不会改变已设置的事件，也不会改变同一事件的任何其他代码位置。

​	禁用特定位置的事件对高性能的监控非常重要。 例如，如果调试器禁用了除几个断点外的所有监控那么程序在调试器下运行时就不会产生额外的开销。

## sys.monitoring.**restart_events**() → [None]({{< ref "/library/constants#None" >}})

​	启用 [`sys.monitoring.DISABLE`]({{< ref "/library/python/sys_monitoring#sys.monitoring.DISABLE" >}}) 针对所有工具禁用的所有事件。



## 注册回调函数

​	要为事件注册一个可调用对象则要调用

## sys.monitoring.**register_callback**(*tool_id: [int]({{< ref "/library/functions#int" >}})*, *event: [int]({{< ref "/library/functions#int" >}})*, *func: [Callable]({{< ref "/library/datatypes/collections_abc#collections.abc.Callable" >}}) | [None]({{< ref "/library/constants#None" >}})*, */*) → [Callable]({{< ref "/library/datatypes/collections_abc#collections.abc.Callable" >}}) | [None]({{< ref "/library/constants#None" >}})

​	使用给定的 *tool_id* 为 *event* 注册可调用对象 *func*

​	如果已经为给定的 *tool_id* 和 *event* 注册了另一个回调，它将被注销并返回。 在其他情况下 [`register_callback()`]({{< ref "/library/python/sys_monitoring#sys.monitoring.register_callback" >}}) 将返回 `None`。

​	函数可以通过调用 `sys.monitoring.register_callback(tool_id, event, None)` 来注销。

​	回调函数可在任何时候被注册或注销。

​	注册或注销回调函数将生成一个 [`sys.audit()`]({{< ref "/library/python/sys#sys.audit" >}}) 事件。

### 回调函数参数

## sys.monitoring.**MISSING**

​	一个传给回调函数表明该调用不附带任何参数的特殊值。

​	当一个激活的事件发生时，已注册的回调函数将被调用。 不同的事件将为回调函数提供不同的参数，如下所示：

- [`PY_START`]({{< ref "/library/python/sys_monitoring#monitoring-event-PY_START" >}}) 和 [`PY_RESUME`]({{< ref "/library/python/sys_monitoring#monitoring-event-PY_RESUME" >}}):

  ```
  func(code: CodeType, instruction_offset: int) -> DISABLE | Any
  ```

- [`PY_RETURN`]({{< ref "/library/python/sys_monitoring#monitoring-event-PY_RETURN" >}}) 和 [`PY_YIELD`]({{< ref "/library/python/sys_monitoring#monitoring-event-PY_YIELD" >}}):

  ```
  func(code: CodeType, instruction_offset: int, retval: object) -> DISABLE | Any
  ```

- [`CALL`]({{< ref "/library/python/sys_monitoring#monitoring-event-CALL" >}}), [`C_RAISE`]({{< ref "/library/python/sys_monitoring#monitoring-event-C_RAISE" >}}) 和 [`C_RETURN`]({{< ref "/library/python/sys_monitoring#monitoring-event-C_RETURN" >}}):

  ```
  func(code: CodeType, instruction_offset: int, callable: object, arg0: object | MISSING) -> DISABLE | Any
  ```

  如果没有任何参数，则 *arg0* 将被设为 [`sys.monitoring.MISSING`]({{< ref "/library/python/sys_monitoring#sys.monitoring.MISSING" >}})。

- [`RAISE`]({{< ref "/library/python/sys_monitoring#monitoring-event-RAISE" >}}), [`RERAISE`]({{< ref "/library/python/sys_monitoring#monitoring-event-RERAISE" >}}), [`EXCEPTION_HANDLED`]({{< ref "/library/python/sys_monitoring#monitoring-event-EXCEPTION_HANDLED" >}}), [`PY_UNWIND`]({{< ref "/library/python/sys_monitoring#monitoring-event-PY_UNWIND" >}}), [`PY_THROW`]({{< ref "/library/python/sys_monitoring#monitoring-event-PY_THROW" >}}) 和 [`STOP_ITERATION`]({{< ref "/library/python/sys_monitoring#monitoring-event-STOP_ITERATION" >}}):

  ```
  func(code: CodeType, instruction_offset: int, exception: BaseException) -> DISABLE | Any
  ```

- [`LINE`]({{< ref "/library/python/sys_monitoring#monitoring-event-LINE" >}}):

  ```
  func(code: CodeType, line_number: int) -> DISABLE | Any
  ```

- [`BRANCH`]({{< ref "/library/python/sys_monitoring#monitoring-event-BRANCH" >}}) 和 [`JUMP`]({{< ref "/library/python/sys_monitoring#monitoring-event-JUMP" >}}):

  ```
  func(code: CodeType, instruction_offset: int, destination_offset: int) -> DISABLE | Any
  ```

  请注意 *destination_offset* 是代码下一次执行的位置。 对于未进入的分支这将为该分支之后的指令的偏移量。

- [`INSTRUCTION`]({{< ref "/library/python/sys_monitoring#monitoring-event-INSTRUCTION" >}}):

  ```
  func(code: CodeType, instruction_offset: int) -> DISABLE | Any
  ```
