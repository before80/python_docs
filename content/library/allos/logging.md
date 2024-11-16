+++
title = "logging --- Python 的日志记录工具"
date = 2024-11-15T12:09:25+08:00
weight = 40
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/logging.html](https://docs.python.org/zh-cn/3.13/library/logging.html)
>
> 收录该文档的时间：`2024-11-15T12:09:25+08:00`

# `logging` --- Python 的日志记录工具

**源代码：** [Lib/logging/__init__.py](https://github.com/python/cpython/tree/3.13/Lib/logging/__init__.py)

​	Important

​	此页面仅包含 API 参考信息。教程信息和更多高级用法的讨论，请参阅

- [基础教程](https://docs.python.org/zh-cn/3.13/howto/logging.html#logging-basic-tutorial)
- [进阶教程](https://docs.python.org/zh-cn/3.13/howto/logging.html#logging-advanced-tutorial)
- [日志记录操作手册](https://docs.python.org/zh-cn/3.13/howto/logging-cookbook.html#logging-cookbook)

------

​	这个模块为应用与库实现了灵活的事件日志系统的函数与类。

​	使用标准库提供的 logging API 最主要的好处是，所有的 Python 模块都可能参与日志输出，包括你自己的日志消息和第三方模块的日志消息。

​	这是一个惯例用法的简单示例:

```
# myapp.py
import logging
import mylib
logger = logging.getLogger(__name__)

def main():
    logging.basicConfig(filename='myapp.log', level=logging.INFO)
    logger.info('Started')
    mylib.do_something()
    logger.info('Finished')

if __name__ == '__main__':
    main()
# mylib.py
import logging
logger = logging.getLogger(__name__)

def do_something():
    logger.info('Doing something')
```

​	如果你运行 *myapp.py* ，你应该在 *myapp.log* 中看到：

```
INFO:__main__:Started
INFO:mylib:Doing something
INFO:__main__:Finished
```

​	这种惯常用法的一个关键特性在于大部分代码都是简单地通过 `getLogger(__name__)` 创建一个模块级别的日志记录器，并使用该日志记录器来完成任何需要的日志记录。 这样既简洁明了，又能根据需要对下游代码进行细粒度的控制。 记录到模块级日志记录器的消息会被转发给更高级别模块的日志记录器的处理器，一直到最高层级的日志记录器既根日志记录器；这种方式被称为分级日志记录。

​	要使日志记录有用，就需要对其进行配置：为每个日志记录器设置级别和目标，还可能改变特定模块的日志记录方式，通常是基于命令行参数或应用配置来实现。 在大多数情况下，如上文所述，只有根日志记录器需要如此配置，因为所有在模块层级上的低级别日志记录器最终都会将消息转发给它的处理器。 [`basicConfig()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.basicConfig) 提供了一种配置根日志记录器的快捷方式，它可以处理多种应用场景。

​	这个模块提供许多强大而灵活的功能。如果对 logging 不太熟悉， 掌握它最好的方式就是查看它对应的教程（**详见右侧的链接**）。

​	该模块定义的基础类，以及它们的属性和方法都在下面的小节中列出。

- 记录器暴露了应用程序代码直接使用的接口。
- 处理器将日志记录（由记录器创建）发送到适当的目标。
- 过滤器提供了更细粒度的功能，用于确定要输出的日志记录。
- 格式器指定最终输出中日志记录的样式。



## 记录器对象

​	记录器有以下的属性和方法。注意 *永远* 不要直接实例化记录器，应当通过模块级别的函数 `logging.getLogger(name)` 。多次使用相同的名字调用 [`getLogger()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.getLogger) 会一直返回相同的 Logger 对象的引用。

`name` 一般是以句点分割的层级值，如 `foo.bar.baz` (尽管也可以是简单形式，例如 `foo`)。 层级结构列表中位于下方的日志记录器是列表中较高位置的日志记录器的子级。 例如，假定有一个名叫 `foo` 的日志记录器，则名字为 `foo.bar`, `foo.bar.baz` 和 `foo.bam` 的日志记录器都是 `foo` 的子级。 而且，所有日志记录器都是根日志记录器的子级。 日志记录器名称的层级结构类似于 Python 包的层级结构，如果你使用建议的构造 `logging.getLogger(__name__)` 以每个模块为基础来组织你的日志记录器则将与后者完全一致。 这是因为在一个模块中，`__name__` 是该模块在 Python 包命名空间中的名字。

## *class* logging.**Logger**

## **name**

​	这是日志记录器的名称，也是传给 [`getLogger()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.getLogger) 用以获取日志记录器的值。

​	备注

 

​	该属性应当被视为是只读的。

## **level**

​	该日志记录器的阈值，由 [`setLevel()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.setLevel) 方法设置。

​	备注

 

​	请不要直接设置该值 —— 应当始终使用 [`setLevel()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.setLevel)，它会检查传入的级别。

## **parent**

​	此日志记录器的父日志记录器。它可能会根据命名空间层次结构中更高日志记录器的实例化而发生变化。

​	备注

 

​	该值应被视为只读 。

## **propagate**

​	如果这个属性为真，记录到这个记录器的事件除了会发送到此记录器的所有处理程序外，还会传递给更高级别（祖先）记录器的处理器，此外任何关联到这个记录器的处理器。消息会直接传递给祖先记录器的处理器 —— 不考虑祖先记录器的级别和过滤器。

​	如果为假，记录消息将不会传递给当前记录器的祖先记录器的处理器。

​	举例说明：如果名为 `A.B.C` 的记录器的传播属性求值为真，则任何通过调用诸如 `logging.getLogger('A.B.C').error(...)` 之类的方法记录到 `A.B.C` 的事件，在第一次被传递到 `A.B.C` 上附加的处理器后，将[取决于传递该记录器的级别和过滤器设置]依次传递给附加到名为 `A.B`，`A` 的记录器和根记录器的所有处理器。如果 `A.B.C`、`A.B`、`A` 组成的链中，任一记录器的 `propagate` 属性设置为假，那么这将是最后一个其处理器会收到事件的记录器，此后传播在该点停止。

​	构造器将这个属性初始化为 `True`。

​	备注

 

​	如果你将一个处理器附加到一个记录器 *和* 其一个或多个祖先记录器，它可能发出多次相同的记录。通常，您不需要将一个处理器附加到一个以上的记录器上 —— 如果您将它附加到记录器层次结构中最高的适当记录器上，则它将看到所有后代记录器记录的所有事件，前提是它们的传播设置保留为 `True`。一种常见的方案是仅将处理器附加到根记录器，通过传播来处理其余部分。

## **handlers**

​	直接连接到此记录器的处理程序列表实例。

​	备注

 

​	该属性应被视为只读 ；通常通过 [`addHandler()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.addHandler) 和 [`removeHandler()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.removeHandler) 方法进行更改，它们使用锁来确保线程安全的操作。

## **disabled**

​	该属性禁用对任何事件的处理。初始化程序将其设置为 `False` ，只有日志配置代码才能更改。

​	备注

 

​	该属性应当被视为是只读的。

## **setLevel**(*level*)

​	给记录器设置阈值为 *level* 。日志等级小于 *level* 会被忽略。严重性为 *level* 或更高的日志消息将由该记录器的任何一个或多个处理器发出，除非将处理器的级别设置为比 *level* 更高的级别。

​	创建记录器时，级别默认设置为 [`NOTSET`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.NOTSET) （当记录器是根记录器时，将处理所有消息；如果记录器不是根记录器，则将委托给父级）。请注意，根记录器的默认级别为 [`WARNING`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.WARNING) 。

​	委派给父级的意思是如果一个记录器的级别设置为 NOTSET，将遍历其祖先记录器，直到找到级别不是 NOTSET 的记录器，或者到根记录器为止。

​	如果发现某个父级的级别不是 NOTSET ，那么该父级的级别将被视为发起搜索的记录器的有效级别，并用于确定如何处理日志事件。

​	如果搜索到达根记录器，并且其级别为 NOTSET，则将处理所有消息。否则，将使用根记录器的级别作为有效级别。

​	参见 [日志级别](https://docs.python.org/zh-cn/3.13/library/logging.html#levels) 级别列表。

*在 3.2 版本发生变更:* 现在 *level* 参数可以接受形如 'INFO' 的级别字符串表示形式，以代替形如 [`INFO`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.INFO) 的整数常量。 但是请注意，级别在内部存储为整数，并且 [`getEffectiveLevel()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.getEffectiveLevel) 和 [`isEnabledFor()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.isEnabledFor) 等方法的传入/返回值也为整数。

## **isEnabledFor**(*level*)

​	指示此记录器是否将处理级别为 *level* 的消息。此方法首先检查由 `logging.disable(level)` 设置的模块级的级别，然后检查由 [`getEffectiveLevel()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.getEffectiveLevel) 确定的记录器的有效级别。

## **getEffectiveLevel**()

​	指示此记录器的有效级别。如果通过 [`setLevel()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.setLevel) 设置了除 [`NOTSET`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.NOTSET) 以外的值，则返回该值。否则，将层次结构遍历到根，直到找到除 [`NOTSET`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.NOTSET) 以外的其他值，然后返回该值。返回的值是一个整数，通常为 [`logging.DEBUG`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.DEBUG)、 [`logging.INFO`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.INFO) 等等。

## **getChild**(*suffix*)

​	返回由后缀确定的该记录器的后代记录器。 因此，`logging.getLogger('abc').getChild('def.ghi')` 与 `logging.getLogger('abc.def.ghi')` 将返回相同的记录器。 这是一个便捷方法，当使用如 `__name__` 而不是字符串字面值命名父记录器时很有用。

> Added in version 3.2.
>

## **getChildren**()

​	返回由该日志记录器的直接下级日志记录器组成的集合。 举例来说 `logging.getLogger().getChildren()` 将返回包含名为 `foo` 和 `bar` 的日志记录器的集合，但名为 `foo.bar` 的日志记录器则不会包括在集合中。 类似地，`logging.getLogger('foo').getChildren()` 将返回包括名为 `foo.bar` 的日志记录器的集合，但不会包括名为 `foo.bar.baz` 的日志记录器。

> Added in version 3.12.
>

## **debug**(*msg*, **args*, ***kwargs*)

​	在此记录器上记录 [`DEBUG`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.DEBUG) 级别的消息。 *msg* 是消息格式字符串，而 *args* 是用于字符串格式化操作合并到 *msg* 的参数。（请注意，这意味着您可以在格式字符串中使用关键字以及单个字典参数。）当未提供 *args* 时，不会对 *msg* 执行 ％ 格式化操作。

​	在 *kwargs* 中会检查四个关键字参数： *exc_info* ，*stack_info* ，*stacklevel* 和 *extra* 。

​	如果 *exc_info* 的求值结果不为 false ，则它将异常信息添加到日志消息中。如果提供了一个异常元组（按照 [`sys.exc_info()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.exc_info) 返回的格式）或一个异常实例，则它将被使用；否则，调用 [`sys.exc_info()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.exc_info) 以获取异常信息。

​	第二个可选关键字参数是 *stack_info*，默认为 `False`。如果为 True，则将堆栈信息添加到日志消息中，包括实际的日志调用。请注意，这与通过指定 *exc_info* 显示的堆栈信息不同：前者是从堆栈底部到当前线程中的日志记录调用的堆栈帧，而后者是在搜索异常处理程序时，跟踪异常而打开的堆栈帧的信息。

​	您可以独立于 *exc_info* 来指定 *stack_info*，例如，即使在未引发任何异常的情况下，也可以显示如何到达代码中的特定点。堆栈帧在标题行之后打印：

```
Stack (most recent call last):
```

​	这模仿了显示异常帧时所使用的 `Traceback (most recent call last):` 。

​	第三个可选关键字参数是 *stacklevel* ，默认为 `1` 。如果大于 1 ，则在为日志记录事件创建的 [`LogRecord`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LogRecord) 中计算行号和函数名时，将跳过相应数量的堆栈帧。可以在记录帮助器时使用它，以便记录的函数名称，文件名和行号不是帮助器的函数/方法的信息，而是其调用方的信息。此参数是 [`warnings`](https://docs.python.org/zh-cn/3.13/library/warnings.html#module-warnings) 模块中的同名等效参数。

​	第四个关键字参数 *extra* 被用于传递一个字典，该字典将被用来将为日志记录事件创建的 [`LogRecord`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LogRecord) 中的 [`__dict__`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__dict__) 填充为用户自定义的属性。 然后你将可以随意使用这些自定义的属性。 举例来说，它们可以被加入到已记录的日志消息中。 例如:

```
FORMAT = '%(asctime)s %(clientip)-15s %(user)-8s %(message)s'
logging.basicConfig(format=FORMAT)
d = {'clientip': '192.168.0.1', 'user': 'fbloggs'}
logger = logging.getLogger('tcpserver')
logger.warning('Protocol problem: %s', 'connection reset', extra=d)
```

​	输出类似于

```
2006-02-08 22:20:02,165 192.168.0.1 fbloggs  Protocol problem: connection reset
```

​	在The keys in the dictionary passed in *extra* 传入的字典的键不应与日志系统所使用的键相冲突。 （请参阅 [LogRecord 属性](https://docs.python.org/zh-cn/3.13/library/logging.html#logrecord-attributes) 一节了解有关日志系统所使用的键的更多信息。）

​	如果在已记录的消息中使用这些属性，则需要格外小心。例如，在上面的示例中，[`Formatter`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Formatter) 已设置了格式字符串，其在 [`LogRecord`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LogRecord) 的属性字典中键值为 “clientip” 和 “user”。如果缺少这些内容，则将不会记录该消息，因为会引发字符串格式化异常。因此，在这种情况下，您始终需要使用 *extra* 字典传递这些键。

​	尽管这可能很烦人，但此功能旨在用于特殊情况，例如在多个上下文中执行相同代码的多线程服务器，并且出现的有趣条件取决于此上下文（例如在上面的示例中就是远程客户端IP地址和已验证用户名）。在这种情况下，很可能将专门的 [`Formatter`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Formatter) 与特定的 [`Handler`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Handler) 一起使用。

​	如果没有处理器附加到这个记录器（或者它的任何父辈记录器，考虑到相关的 [`Logger.propagate`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.propagate) 属性），消息将被发送到设置在 [`lastResort`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.lastResort) 的处理器。

*在 3.2 版本发生变更:* 增加了 *stack_info* 参数。

*在 3.5 版本发生变更:* *exc_info* 参数现在可以接受异常实例。

*在 3.8 版本发生变更:* 增加了 *stacklevel* 参数。

## **info**(*msg*, **args*, ***kwargs*)

​	在此记录器上记录 [`INFO`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.INFO) 级别的消息。参数解释同 [`debug()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.debug)。

## **warning**(*msg*, **args*, ***kwargs*)

​	在此记录器上记录 [`WARNING`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.WARNING) 级别的消息。参数解释同 [`debug()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.debug)。

​	备注

 

​	有一个功能上与 `warning` 一致的方法 `warn`。由于 `warn` 已被弃用，请不要使用它 —— 改为使用 `warning`。

## **error**(*msg*, **args*, ***kwargs*)

​	在此记录器上记录 [`ERROR`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.ERROR) 级别的消息。参数解释同 [`debug()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.debug)。

## **critical**(*msg*, **args*, ***kwargs*)

​	在此记录器上记录 [`CRITICAL`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.CRITICAL) 级别的消息。参数解释同 [`debug()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.debug)。

## **log**(*level*, *msg*, **args*, ***kwargs*)

​	在此记录器上记录 *level* 整数代表的级别的消息。参数解释同 [`debug()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.debug)。

## **exception**(*msg*, **args*, ***kwargs*)

​	在此记录器上记录 [`ERROR`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.ERROR) 级别的消息。参数解释同 [`debug()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.debug)。异常信息将添加到日志消息中。仅应从异常处理程序中调用此方法。

## **addFilter**(*filter*)

​	将指定的过滤器 *filter* 添加到此记录器。

## **removeFilter**(*filter*)

​	从此记录器中删除指定的过滤器 *filter*。

## **filter**(*record*)

​	将此记录器的过滤器应用于记录，如果记录能被处理则返回 `True`。过滤器会被依次使用，直到其中一个返回假值为止。如果它们都不返回假值，则记录将被处理（传递给处理器）。如果返回任一为假值，则不会对该记录做进一步处理。

## **addHandler**(*hdlr*)

​	将指定的处理器 *hdlr* 添加到此记录器。

## **removeHandler**(*hdlr*)

​	从此记录器中删除指定的处理器 *hdlr*。

## **findCaller**(*stack_info=False*, *stacklevel=1*)

​	查找调用源的文件名和行号，以 文件名，行号，函数名称和堆栈信息 4元素元组的形式返回。堆栈信息将返回 `None`，除非 *stack_info* 为 `True`。

*stacklevel* 参数用于调用 [`debug()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.debug) 和其他 API。如果大于 1，则多余部分将用于跳过堆栈帧，然后再确定要返回的值。当从帮助器/包装器代码调用日志记录 API 时，这通常很有用，以便事件日志中的信息不是来自帮助器/包装器代码，而是来自调用它的代码。

## **handle**(*record*)

​	通过将记录传递给与此记录器及其祖先关联的所有处理器来处理（直到某个 *propagate* 值为 false）。此方法用于从套接字接收的未序列化的以及在本地创建的记录。使用 [`filter()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.filter) 进行记录器级别过滤。

## **makeRecord**(*name*, *level*, *fn*, *lno*, *msg*, *args*, *exc_info*, *func=None*, *extra=None*, *sinfo=None*)

​	这是一种工厂方法，可以在子类中对其进行重写以创建专门的 [`LogRecord`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LogRecord) 实例。

## **hasHandlers**()

​	检查此记录器是否配置了任何处理器。通过在此记录器及其记录器层次结构中的父级中查找处理器完成此操作。如果找到处理器则返回 `True`，否则返回 `False`。只要找到 “propagate” 属性设置为假值的记录器，该方法就会停止搜索层次结构 —— 其将是最后一个检查处理器是否存在的记录器。

> Added in version 3.2.
>

*在 3.7 版本发生变更:* 现在可以对处理器进行序列化和反序列化。



## 日志级别

​	日志记录级别的数值在下表中给出。如果你想要定义自己的级别，并且需要它们具有相对于预定义级别的特定值，那么这你可能对以下内容感兴趣。如果你定义具有相同数值的级别，它将覆盖预定义的值；预定义的名称将失效。

| 级别                 | 数值 | 何种含义 / 何时使用                                          |
| :------------------- | :--- | :----------------------------------------------------------- |
| logging.**NOTSET**   | 0    | 当在日志记录器上设置时，表示将查询上级日志记录器以确定生效的级别。 如果仍被解析为 `NOTSET`，则会记录所有事件。 在处理器上设置时，所有事件都将被处理。 |
| logging.**DEBUG**    | 10   | 详细的信息，通常只有试图诊断问题的开发人员才会感兴趣。       |
| logging.**INFO**     | 20   | 确认程序按预期运行。                                         |
| logging.**WARNING**  | 30   | 表明发生了意外情况，或近期有可能发生问题（例如‘磁盘空间不足’）。 软件仍会按预期工作。 |
| logging.**ERROR**    | 40   | 由于严重的问题，程序的某些功能已经不能正常执行               |
| logging.**CRITICAL** | 50   | 严重的错误，表明程序已不能继续执行                           |



## 处理器对象

​	处理器具有以下属性和方法。 请注意 [`Handler`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Handler) 不可直接实例化；该类是被作为更有用的子类的基类。 不过，子类中的 `__init__()` 方法需要调用 [`Handler.__init__()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Handler.__init__)。

## *class* logging.**Handler**

## **__init__**(*level=NOTSET*)

​	初始化 [`Handler`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Handler) 实例时，需要设置它的级别，将过滤列表置为空，并且创建锁（通过 [`createLock()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Handler.createLock) ）来序列化对 I/O 的访问。

## **createLock**()

​	初始化一个线程锁，用来序列化对底层的 I/O 功能的访问，底层的 I/O 功能可能不是线程安全的。

## **acquire**()

​	获取由 [`createLock()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Handler.createLock) 创建的线程锁。

## **release**()

​	释放由 [`acquire()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Handler.acquire) 获取的线程锁。

## **setLevel**(*level*)

​	给处理器设置阈值为 *level* 。日志级别小于 *level* 将被忽略。创建处理器时，日志级别被设置为 [`NOTSET`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.NOTSET) （所有的消息都会被处理）。

​	参见 [日志级别](https://docs.python.org/zh-cn/3.13/library/logging.html#levels) 级别列表。

*在 3.2 版本发生变更:* *level* 形参现在接受像 'INFO' 这样的字符串形式的级别表达方式，也可以使用像 [`INFO`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.INFO) 这样的整数常量。

## **setFormatter**(*fmt*)

​	将此处理器的 [`Formatter`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Formatter) 设置为 *fmt*。

## **addFilter**(*filter*)

​	将指定的过滤器 *filter* 添加到此处理器。

## **removeFilter**(*filter*)

​	从此处理器中删除指定的过滤器 *filter* 。

## **filter**(*record*)

​	将此处理器的过滤器应用于记录，在要处理记录时返回 `True` 。依次查询过滤器，直到其中一个返回假值为止。如果它们都不返回假值，则将发出记录。如果返回一个假值，则处理器将不会发出记录。

## **flush**()

​	确保所有日志记录从缓存输出。此版本不执行任何操作，并且应由子类实现。

## **close**()

​	回收处理器使用的所有资源。此版本不输出，但从内部处理器列表中删除处理器，内部处理器在 [`shutdown()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.shutdown) 被调用时关闭 。子类应确保从重写的 [`close()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Handler.close) 方法中调用此方法。

## **handle**(*record*)

​	经已添加到处理器的过滤器过滤后，有条件地发出指定的日志记录。用获取/释放 I/O 线程锁包装了记录的实际发出行为。

## **handleError**(*record*)

​	此方法应当在 [`emit()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Handler.emit) 调用期间遇到异常时从处理器中调用。 如果模块级属性 [`raiseExceptions`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.raiseExceptions) 为 `False`，则异常将被静默地忽略。 这是大多数情况下日志系统所需要的 —— 大多数用户不会关心日志系统中的错误，他们对应用程序错误更感兴趣。 但是，你可以根据需要将其替换为自定义处理器。 指定的记录是发生异常时正在处理的记录。 ([`raiseExceptions`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.raiseExceptions) 的默认值是 `True`，因为这在开发过程中更有用处）。

## **format**(*record*)

​	如果设置了格式器则用其对记录进行格式化。否则，使用模块的默认格式器。

## **emit**(*record*)

​	执行实际记录给定日志记录所需的操作。这个版本应由子类实现，因此这里直接引发 [`NotImplementedError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#NotImplementedError) 异常。

​	警告

 

​	此方法会在获得一个处理器层级的锁之后被调用，在此方法返回之后锁将被释放。 当你重写此方法时，请注意在调用任何可能执行锁定操作的日志记录 API 的其他部分的方法时务必小心谨慎，因为这可能会导致死锁。 具体来说:

- 日志记录配置 API 会获取模块层级锁，然后还会在配置处理器时获取处理器层级锁。
- 许多日志记录 API 都会锁定模块级锁。 如果这样的 API 在此方法中被调用，则它可能会在另一个线程执行配置调用时导致死锁，因为那个线程将试图在处理器级锁 *之前* 获取模块级锁，而这个线程将试图在处理器级锁 *之后* 获取模块级锁（因为在此方法中，处理器级锁已经被获取了）。

​	有关作为标准随附的处理器列表，请参见 [`logging.handlers`](https://docs.python.org/zh-cn/3.13/library/logging.handlers.html#module-logging.handlers)。



## 格式器对象

## *class* logging.**Formatter**(*fmt=None*, *datefmt=None*, *style='%'*, *validate=True*, ***, *defaults=None*)

​	负责将一个 [`LogRecord`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LogRecord) 转换为可供人类或外部系统解读的输出字符串。

## 参数:

- **fmt** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)) -- 用于日志记录整体输出的给定 *style* 形式的格式字符串。 可用的映射键将从 [`LogRecord`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LogRecord) 对象的 [LogRecord 属性](https://docs.python.org/zh-cn/3.13/library/logging.html#logrecord-attributes) 中提取。 如果未指定，则将使用 `'%(message)s'`，即已记录的日志消息。
- **datefmt** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)) -- 用于日志记录输出的日期/时间部分的给定 *style* 形式的格式字符串。 如果未指定，则将使用 [`formatTime()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Formatter.formatTime) 中描述的默认值。
- **style** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)) -- 可以是 `'%'`, `'{'` 或 `'$'` 之一并决定格式字符串将如何与数据合并: 使用 [printf 风格的字符串格式化](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#old-string-formatting) (`%`), [`str.format()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str.format) (`{`) 或 [`string.Template`](https://docs.python.org/zh-cn/3.13/library/string.html#string.Template) (`$`) 之一。 这将只应用于 *fmt* 和 *datefmt* (例如 `'%(message)s'` 或 `'{message}'`)，而不会应用于传给日志记录方法的实际日志消息。 但是，也存在 [其他方式](https://docs.python.org/zh-cn/3.13/howto/logging-cookbook.html#formatting-styles) 可以为日志消息使用 `{` 和 `$` 格式化。
- **validate** ([*bool*](https://docs.python.org/zh-cn/3.13/library/functions.html#bool)) -- 如果为 `True` (默认值)，则不正确或不匹配的 *fmt* 和 *style* 将引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)；例如 `logging.Formatter('%(asctime)s - %(message)s', style='{')`。
- **defaults** ([*dict*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#dict)*[*[*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)*,* *Any**]*) -- 一个由在自定义字段中使用的默认值组成的字典。 例如 `logging.Formatter('%(ip)s %(message)s', defaults={"ip": None})`

*在 3.2 版本发生变更:* 增加了 *style* 形参。

*在 3.8 版本发生变更:* 增加了 *validate* 形参。

*在 3.10 版本发生变更:* 增加了 *defaults* 形参。

## **format**(*record*)

​	记录的属性字典被用作字符串格式化操作的操作数。 返回结果字符串。 在格式化该字典之前，会执行几个预备步骤。 记录的 *message* 属性是用 *msg* % *args* 来计算的。 如果格式化字符串包含 `'(asctime)'`，则会调用 [`formatTime()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Formatter.formatTime) 来格式化事件时间。 如果有异常信息，则使用 [`formatException()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Formatter.formatException) 将其格式化并添加到消息中。 请注意已格式化的异常信息会缓存在 *exc_text* 属性中。 这很有用因为异常信息可以被 pickle 并通过网络发送，但是如果你有不止一个对异常信息进行定制的 [`Formatter`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Formatter) 子类则应当小心。 在这种情况下，你必须在一个格式化器完成格式化后清空缓存的值 (通过将 *exc_text* 属性设为 `None`)，以便下一个处理事件的格式化器不会使用缓存的值，而是重新计算它。

​	如果栈信息可用，它将被添加在异常信息之后，如有必要请使用 [`formatStack()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Formatter.formatStack) 来转换它。

## **formatTime**(*record*, *datefmt=None*)

​	此方法应由想要使用格式化时间的格式器中的 [`format()`](https://docs.python.org/zh-cn/3.13/library/functions.html#format) 调用。可以在格式器中重写此方法以提供任何特定要求，但是基本行为如下：如果指定了 *datefmt* （字符串），则将其用于 [`time.strftime()`](https://docs.python.org/zh-cn/3.13/library/time.html#time.strftime) 来格式化记录的创建时间。否则，使用格式 '%Y-%m-%d %H:%M:%S,uuu'，其中 uuu 部分是毫秒值，其他字母根据 [`time.strftime()`](https://docs.python.org/zh-cn/3.13/library/time.html#time.strftime) 文档。这种时间格式的示例为 `2003-01-23 00:29:50,411`。返回结果字符串。

​	此函数使用一个用户可配置函数将创建时间转换为元组。 默认情况下，使用 [`time.localtime()`](https://docs.python.org/zh-cn/3.13/library/time.html#time.localtime)；要为特定格式化程序实例更改此项，请将实例的 `converter` 属性设为具有与 [`time.localtime()`](https://docs.python.org/zh-cn/3.13/library/time.html#time.localtime) 或 [`time.gmtime()`](https://docs.python.org/zh-cn/3.13/library/time.html#time.gmtime) 相同签名的函数。 要为所有格式化程序更改此项，例如当你希望所有日志时间都显示为 GMT，请在 `Formatter` 类中设置 `converter` 属性。

*在 3.3 版本发生变更:* 在之前版本中，默认格式是被硬编码的，例如这个例子: `2010-09-06 22:38:15,292` 其中逗号之前的部分由 strptime 格式字符串 (`'%Y-%m-%d %H:%M:%S'`) 处理，而逗号之后的部分为毫秒值。 因为 strptime 没有表示毫秒的占位符，毫秒值使用了另外的格式字符串来添加 `'%s,%03d'` --- 这两个格式字符串代码都是硬编码在该方法中的。 经过修改，这些字符串被定义为类层级的属性，当需要时可以在实例层级上被重写。 属性的名称为 `default_time_format` (用于 strptime 格式字符串) 和 `default_msec_format` (用于添加毫秒值)。

*在 3.9 版本发生变更:* `default_msec_format` 可以为 `None`。

## **formatException**(*exc_info*)

​	将指定的异常信息（由 [`sys.exc_info()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.exc_info) 返回的标准异常元组）格式化为字符串。默认实现只是使用了 [`traceback.print_exception()`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.print_exception)。 结果字符串将被返回。

## **formatStack**(*stack_info*)

​	将指定的堆栈信息（由 [`traceback.print_stack()`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.print_stack) 返回的字符串，但移除末尾的换行符）格式化为字符串。 默认实现只是返回输入值。

## *class* logging.**BufferingFormatter**(*linefmt=None*)

​	适合用来在你想要格式化多条记录时进行子类化的格式化器。 你可以传入一个 [`Formatter`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Formatter) 实例用来格式化每一行（每一行对应一条记录）。 如果未被指定，则会使用默认的格式化器（仅输出事件消息）作为行格式化器。

## **formatHeader**(*records*)

​	为 *records* 列表返回一个标头。 基本实现只是返回空字符串。 如果你想要指明特定行为则需要重写此方法，例如显示记录条数、标题或分隔行等。

## **formatFooter**(*records*)

​	为 *records* 列表返回一个结束标记。 基本实现只是返回空字符串。 如果你想要指明特定行为则需要重写此方法，例如显示记录条数或分隔行等。

## **format**(*records*)

​	为 *records* 列表返回已格式化文本。 基本实现在没有记录时只是返回空字符串；在其他情况下，它将返回标头、使用行格式化器执行格式化的每行记录以及结束标记。



## 过滤器对象

`Filters` 可被 `Handlers` 和 `Loggers` 用来实现比按层级提供更复杂的过滤操作。 基本过滤器类只允许低于日志记录器层级结构中低于特定层级的事件。 例如，一个用 'A.B' 初始化的过滤器将允许 'A.B', 'A.B.C', 'A.B.C.D', 'A.B.D' 等日志记录器所记录的事件。 但 'A.BB', 'B.A.B' 等则不允许。 如果用空字符串初始化，则所有事件都会通过。

## *class* logging.**Filter**(*name=''*)

​	返回一个 [`Filter`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Filter) 类的实例。 如果指定了 *name*，则它将被用来为日志记录器命名，该类及其子类将通过该过滤器允许指定事件通过。 如果 *name* 为空字符串，则允许所有事件通过。

## **filter**(*record*)

​	指定的记录是否会被写入日志？否则返回假值，是则返回真值。 过滤器可以原地修改日志记录或者返回完全不同的记录实例并在该事件未来的任何处理过程中用它来替代原始日志记录。

​	请注意关联到处理器的过滤器会在事件由处理器发出之前被查询，而关联到日志记录器的过滤器则会在有事件被记录的的任何时候（使用 [`debug()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.debug), [`info()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.info) 等等）在将事件发送给处理器之前被查询。 这意味着由后代日志记录器生成的事件将不会被父代日志记录器的过滤器设置所过滤，除非该过滤器也已被应用于后代日志记录器。

​	你实际上不需要子类化 `Filter` ：你可以传入任何一个包含有相同语义的 `filter` 方法的实例。

*在 3.2 版本发生变更:* 你不需要创建专门的 `Filter` 类，或使用具有 `filter` 方法的其他类：你可以使用一个函数（或其他可调用对象）作为过滤器。 过滤逻辑将检查过滤器对象是否具有 `filter` 属性：如果有，就会将它当作是 `Filter` 并调用它的 [`filter()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Filter.filter) 方法。 在其他情况下，则会将它当作是可调用对象并将记录作为唯一的形参进行调用。 返回值应当与 [`filter()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Filter.filter) 的返回值相一致。

*在 3.12 版本发生变更:* 现在你可以从过滤器返回一个 [`LogRecord`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LogRecord) 实例来替代日志记录而不是原地修改它。 这允许附加到特定 [`Handler`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Handler) 的过滤器在日志记录发出之前修改它，而不会对其他处理器产生附带影响。

​	尽管过滤器主要被用来构造比层级更复杂的规则以过滤记录，但它们可以查看由它们关联的处理器或记录器所处理的每条记录：当你想要执行统计特定记录器或处理器共处理了多少条记录，或是在所处理的 [`LogRecord`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LogRecord) 中添加、修改或移除属性这样的任务时该特性将很有用处。 显然改变 LogRecord 时需要相当小心，但将上下文信息注入日志确实是被允许的 (参见 [使用过滤器传递上下文信息](https://docs.python.org/zh-cn/3.13/howto/logging-cookbook.html#filters-contextual))。



## LogRecord 属性

[`LogRecord`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LogRecord) 实例是每当有日志被记录时由 [`Logger`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger) 自动创建的，并且可通过 [`makeLogRecord()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.makeLogRecord) 手动创建（例如根据从网络接收的已封存事件创建）。

## *class* logging.**LogRecord**(*name*, *level*, *pathname*, *lineno*, *msg*, *args*, *exc_info*, *func=None*, *sinfo=None*)

​	包含与被记录的事件相关的所有信息。

​	主要信息是在 *msg* 中 *args* 传递的，它们使用 `msg % args` 组合到一起以创建记录的 `message` 属性。

## 参数:

- **name** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)) -- 用于记录此 `LogRecord` 所表示事件的记录器名称。 请注意 `LogRecord` 中的记录器名称将始终为该值，即使它可能是由附加到不同（上级）日志记录器的处理器所发出的。
- **level** ([*int*](https://docs.python.org/zh-cn/3.13/library/functions.html#int)) -- 日志记录事件的 [数字层级](https://docs.python.org/zh-cn/3.13/library/logging.html#levels) (如 `10` 表示 `DEBUG`, `20` 表示 `INFO` 等等)。 请注意这会转换为 LogRecord 的 *两个* 属性: `levelno` 表示数字值而 `levelname` 表示对应的层级名。
- **pathname** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)) -- 日志记录调用所在源文件的完整路径字符串。
- **lineno** ([*int*](https://docs.python.org/zh-cn/3.13/library/functions.html#int)) -- 记录调用所在源文件中的行号。
- **msg** ([*Any*](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.Any)) -- 事件描述消息，这可以是一个带有 % 形式可变数据占位符的格式字符串，或是任意对象 (参见 [使用任意对象作为消息](https://docs.python.org/zh-cn/3.13/howto/logging.html#arbitrary-object-messages))。
- **args** ([*tuple*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#tuple) *|* [*dict*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#dict)*[*[*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str)*,* [*Any*](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.Any)*]*) -- 要合并到 *msg* 参数以获得事件描述的可变数据。
- **exc_info** ([*tuple*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#tuple)*[*[*type*](https://docs.python.org/zh-cn/3.13/library/functions.html#type)*[*[*BaseException*](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BaseException)*]**,* [*BaseException*](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BaseException)*,* [*types.TracebackType*](https://docs.python.org/zh-cn/3.13/library/types.html#types.TracebackType)*]* *|* *None*) -- 包含当前异常信息的异常元组，就如 [`sys.exc_info()`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.exc_info) 所返回的，或者如果没有可用异常信息则为 `None`。
- **func** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) *|* *None*) -- 发起调用日志记录调用的函数或方法名称。
- **sinfo** ([*str*](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) *|* *None*) -- 一个文本字符串，表示当前线程中从堆栈底部直到日志记录调用的堆栈信息。

## **getMessage**()

​	在将 [`LogRecord`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LogRecord) 实例与任何用户提供的参数合并之后，返回此实例的消息。 如果用户提供给日志记录调用的消息参数不是字符串，则会在其上调用 [`str()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str) 以将它转换为字符串。 此方法允许将用户定义的类用作消息，类的 `__str__` 方法可以返回要使用的实际格式字符串。

*在 3.2 版本发生变更:* 通过提供用于创建记录的工厂方法已使得 [`LogRecord`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LogRecord) 的创建更易于配置。 该工厂方法可使用 [`getLogRecordFactory()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.getLogRecordFactory) 和 [`setLogRecordFactory()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.setLogRecordFactory) （在此可查看工厂方法的签名）来设置。

​	在创建时可使用此功能将你自己的值注入 [`LogRecord`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LogRecord)。 你可以使用以下模式:

```
old_factory = logging.getLogRecordFactory()

def record_factory(*args, **kwargs):
    record = old_factory(*args, **kwargs)
    record.custom_attribute = 0xdecafbad
    return record

logging.setLogRecordFactory(record_factory)
```

​	通过此模式，多个工厂方法可以被链接起来，并且只要它们不重写彼此的属性或是在无意中覆盖了上面列出的标准属性，就不会发生意外。



## LogRecord 属性

​	LogRecord 具有许多属性，它们大多数来自于传递给构造器的形参。 （请注意 LogRecord 构造器形参与 LogRecord 属性的名称并不总是完全彼此对应的。） 这些属性可被用于将来自记录的数据合并到格式字符串中。 下面的表格（按字母顺序）列出了属性名称、它们的含义以及相应的 %-style 格式字符串内占位符。

​	如果是使用 {}-格式化（[`str.format()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str.format)），你可以将 `{attrname}` 用作格式字符串内的占位符。 如果是使用 $-格式化（[`string.Template`](https://docs.python.org/zh-cn/3.13/library/string.html#string.Template)），则会使用 `${attrname}` 的形式。 当然在这两种情况下，都应当将 `attrname` 替换为你想要使用的实际属性名称。

​	在 {}-格式化的情况下，你可以在属性名称之后放置指定的格式化旗标，并用冒号来分隔。 例如：占位符 `{msecs:03.0f}` 会将毫秒值 `4` 格式化为 `004`。 有参看 [`str.format()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str.format) 文档了解你可以使用的选项的详情。

| 属性名称        | 格式                         | 描述                                                         |
| :-------------- | :--------------------------- | :----------------------------------------------------------- |
| args            | 此属性不需要用户进行格式化。 | 合并到 `msg` 以产生 `message` 的包含参数的元组，或是其中的值将被用于合并的字典（当只有一个参数且其类型为字典时）。 |
| asctime         | `%(asctime)s`                | 表示人类易读的 [`LogRecord`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LogRecord) 生成时间。 默认形式为 '2003-07-08 16:49:45,896' （逗号之后的数字为时间的毫秒部分）。 |
| created         | `%(created)f`                | 当 [`LogRecord`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LogRecord) 被创建的时间（即 [`time.time_ns()`](https://docs.python.org/zh-cn/3.13/library/time.html#time.time_ns) / 1e9 所返回的值）。 |
| exc_info        | 此属性不需要用户进行格式化。 | 异常元组（例如 `sys.exc_info`）或者如未发生异常则为 `None`。 |
| filename        | `%(filename)s`               | `pathname` 的文件名部分。                                    |
| funcName        | `%(funcName)s`               | 函数名包括调用日志记录.                                      |
| levelname       | `%(levelname)s`              | 消息文本记录级别（`'DEBUG'`，`'INFO'`，`'WARNING'`，`'ERROR'`，`'CRITICAL'`）。 |
| levelno         | `%(levelno)s`                | 消息数字的记录级别 ([`DEBUG`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.DEBUG), [`INFO`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.INFO), [`WARNING`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.WARNING), [`ERROR`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.ERROR), [`CRITICAL`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.CRITICAL)). |
| lineno          | `%(lineno)d`                 | 发出日志记录调用所在的源行号（如果可用）。                   |
| message         | `%(message)s`                | 记入日志的消息，即 `msg % args` 的结果。 这是在发起调用 [`Formatter.format()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Formatter.format) 时设置的。 |
| module          | `%(module)s`                 | 模块 (`filename` 的名称部分)。                               |
| msecs           | `%(msecs)d`                  | [`LogRecord`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LogRecord) 被创建的时间的毫秒部分。 |
| msg             | 此属性不需要用户进行格式化。 | 在原始日志记录调用中传入的格式字符串。 与 `args` 合并以产生 `message`，或是一个任意对象 (参见 [使用任意对象作为消息](https://docs.python.org/zh-cn/3.13/howto/logging.html#arbitrary-object-messages))。 |
| name            | `%(name)s`                   | 用于记录调用的日志记录器名称。                               |
| pathname        | `%(pathname)s`               | 发出日志记录调用的源文件的完整路径名（如果可用）。           |
| process         | `%(process)d`                | 进程ID（如果可用）                                           |
| processName     | `%(processName)s`            | 进程名（如果可用）                                           |
| relativeCreated | `%(relativeCreated)d`        | 以毫秒数表示的 LogRecord 被创建的时间，即相对于 logging 模块被加载时间的差值。 |
| stack_info      | 此属性不需要用户进行格式化。 | 当前线程中从堆栈底部起向上直到包括日志记录调用并引发创建当前记录堆栈帧创建的堆栈帧信息（如果可用）。 |
| thread          | `%(thread)d`                 | 线程ID（如果可用）                                           |
| threadName      | `%(threadName)s`             | 线程名（如果可用）                                           |
| taskName        | `%(taskName)s`               | [`asyncio.Task`](https://docs.python.org/zh-cn/3.13/library/asyncio-task.html#asyncio.Task) 名称（如果可用）。 |

*在 3.1 版本发生变更:* 添加了 *processName*

*在 3.12 版本发生变更:* 添加了 *taskName*。



## LoggerAdapter 对象

[`LoggerAdapter`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LoggerAdapter) 实例会被用来方便地将上下文信息传入日志记录调用。 要获取用法示例，请参阅 [添加上下文信息到你的日志记录输出](https://docs.python.org/zh-cn/3.13/howto/logging-cookbook.html#context-info) 部分。

## *class* logging.**LoggerAdapter**(*logger*, *extra*, *merge_extra=False*)

​	返回使用一个下层 [`Logger`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger) 实例、一个字典型对象 (*extra*) 和一个指明单独日志调用的 *extra* 参数是否要与 [`LoggerAdapter`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LoggerAdapter) 的 extra 值合并的布尔值 (*merge_extra*) 进行初始化的 [`LoggerAdapter`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LoggerAdapter) 的实例。 其默认行为将忽略单独日志调用的 *extra* 参数并只使用 [`LoggerAdapter`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LoggerAdapter) 实例中的相应参数值。

## **process**(*msg*, *kwargs*)

​	修改传递给日志记录调用的消息和/或关键字参数以便插入上下文信息。 此实现接受以 *extra* 形式传给构造器的对象并使用 'extra' 键名将其加入 *kwargs*。 返回值为一个 (*msg*, *kwargs*) 元组，其包含（可能经过修改的）传入参数。

## **manager**

​	在 *logger* 中委托给下层的 `manager`。

## **_log**

​	在 *logger* 中委托给下层的 `_log()` 方法。

​	在上述方法之外，[`LoggerAdapter`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LoggerAdapter) 还支持 [`Logger`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger) 的下列方法: [`debug()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.debug), [`info()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.info)，[`warning()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.warning)，[`error()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.error), [`exception()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.exception), [`critical()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.critical)，[`log()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.log)，[`isEnabledFor()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.isEnabledFor)，[`getEffectiveLevel()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.getEffectiveLevel)，[`setLevel()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.setLevel) 以及 [`hasHandlers()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.hasHandlers)。 这些方法具有与它们在 [`Logger`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger) 中的对应方法相同的签名，因此你可以互换使用这两种类型的实例。

*在 3.2 版本发生变更:* [`isEnabledFor()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.isEnabledFor), [`getEffectiveLevel()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.getEffectiveLevel), [`setLevel()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.setLevel) 和 [`hasHandlers()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.hasHandlers) 方法已被添加到 [`LoggerAdapter`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LoggerAdapter)。 这些方法会委托给下层的日志记录器。

*在 3.6 版本发生变更:* 增加了 `manager` 属性和 `_log()` 方法，它们会委托给下层的日志记录器并允许适配器嵌套。

*在 3.13 版本发生变更:* 增加了 *merge_extra* 参数。

## 线程安全

​	logging 模块的目标是使客户端不必执行任何特殊操作即可确保线程安全。 它通过使用线程锁来达成这个目标；用一个锁来序列化对模块共享数据的访问，并且每个处理程序也会创建一个锁来序列化对其下层 I/O 的访问。

​	如果你要使用 [`signal`](https://docs.python.org/zh-cn/3.13/library/signal.html#module-signal) 模块来实现异步信号处理程序，则可能无法在这些处理程序中使用 logging。 这是因为 [`threading`](https://docs.python.org/zh-cn/3.13/library/threading.html#module-threading) 模块中的锁实现并非总是可重入的，所以无法从此类信号处理程序发起调用。

## 模块级函数

​	在上述的类之外，还有一些模块级的函数。

## logging.**getLogger**(*name=None*)

​	返回一个由 name 指定名称的日志记录器，或者如果 name 为 `None` 则返回层级结构中的根日志记录器。 如果指定了 name，它通常是以点号分隔的带层级结构的名称如 *'a'*, *'a.b'* 或 *'a.b.c.d'*。 这些名称的选择完全取决于使用 logging 的开发者，不过就如在 [记录器对象](https://docs.python.org/zh-cn/3.13/library/logging.html#logger) 中提到的那样建议使用 `__name__`，除非你有不这样做的特别理由。

​	所有用给定的 name 对该函数的调用都将返回相同的日志记录器实例。 这意味着日志记录器实例不需要在应用的各部分间传递。

## logging.**getLoggerClass**()

​	返回标准的 [`Logger`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger) 类，或是最近传给 [`setLoggerClass()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.setLoggerClass) 的类。 此函数可以从一个新的类定义中调用，以确保安装自定义的 [`Logger`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger) 类不会撤销其他代码已经应用的自定义操作。 例如:

```
class MyLogger(logging.getLoggerClass()):
    # ... 在这里重写行为
```

## logging.**getLogRecordFactory**()

​	返回一个被用来创建 [`LogRecord`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LogRecord) 的可调用对象。

> Added in version 3.2:
> 此函数与 [`setLogRecordFactory()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.setLogRecordFactory) 一起提供，以允许开发者对表示日志记录事件的 [`LogRecord`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LogRecord) 的构造有更好的控制。

​	请参阅 [`setLogRecordFactory()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.setLogRecordFactory) 了解有关如何调用该工厂方法的更多信息。

## logging.**debug**(*msg*, **args*, ***kwargs*)

​	这是在根日志记录器上调用 [`Logger.debug()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.debug) 的便捷函数。 其参数的处理方式与该方法中的描述完全一致。

​	唯一的区别在于如果根日志记录器没有处理器，则在根日志记录器上调用 `debug` 之前会先调用 [`basicConfig()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.basicConfig)。

​	对于非常简短的脚本或 `logging` 功能的快速演示，`debug` 和其他模块级函数可能会很方便。 不过，大多数程序都会想要仔细和显式地控制日志记录配置，所以应当更倾向于创建一个模块级的日志记录器并在其上调用 [`Logger.debug()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Logger.debug) (或其他特定级别的方法)，如本文档的开头所描述的那样。

## logging.**info**(*msg*, **args*, ***kwargs*)

​	在根日志记录器上记录一条 [`INFO`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.INFO) 级别的消息。 其他参数与行为均与 [`debug()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.debug) 的相同。

## logging.**warning**(*msg*, **args*, ***kwargs*)

​	在根日志记录器上记录一条 [`WARNING`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.WARNING) 级别的消息。 其他参数与行为均与 [`debug()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.debug) 的相同。

​	备注

 

​	有一个已过时方法 `warn` 其功能与 `warning` 一致。 由于 `warn` 已被弃用，请不要使用它 —— 而是改用 `warning`。

## logging.**error**(*msg*, **args*, ***kwargs*)

​	在根日志记录器上记录一条 [`ERROR`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.ERROR) 级别的消息。 其他参数与行为均与 [`debug()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.debug) 的相同。

## logging.**critical**(*msg*, **args*, ***kwargs*)

​	在根日志记录器上记录一条 [`CRITICAL`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.CRITICAL) 级别的消息。 其他参数与行为均与 [`debug()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.debug) 的相同。

## logging.**exception**(*msg*, **args*, ***kwargs*)

​	在根日志记录器上记录一条 [`ERROR`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.ERROR) 级别的消息。 其他参数与行为均与 [`debug()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.debug) 的相同。 异常信息会被添加到日志记录消息中。 此函数应当仅从异常处理器中调用。

## logging.**log**(*level*, *msg*, **args*, ***kwargs*)

​	在根日志记录器上记录一条 *level* 级别的消息。 其他参数与行为均与 [`debug()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.debug) 相同。

## logging.**disable**(*level=CRITICAL*)

​	为所有日志记录器提供重写的级别 *level*，其优先级高于日志记录器自己的级别。 当需要临时限制整个应用程序中的日志记录输出时，此功能会很有用。 它的效果是禁用所有重要程度为 *level* 及以下的日志记录调用，因此如果你附带 INFO 值调用它，则所有 INFO 和 DEBUG 事件就会被丢弃，而重要程度为 WARNING 以及上的事件将根据日志记录器的当前有效级别来处理。 如果 `logging.disable(logging.NOTSET)` 被调用，它将移除这个重写的级别，因此日志记录输出会再次取决于单个日志记录器的有效级别。

​	请注意如果你定义了任何高于 `CRITICAL` 的自定义日志级别（并不建议这样做），你就将无法沿用 *level* 形参的默认值，而必须显式地提供适当的值。

*在 3.7 版本发生变更:* *level* 形参默认级别为 `CRITICAL`。 请参阅 [bpo-28524](https://bugs.python.org/issue?@action=redirect&bpo=28524) 了解此项改变的更多细节。

## logging.**addLevelName**(*level*, *levelName*)

​	在一个内部字典中关联级别 *level* 与文本 *levelName*，该字典会被用来将数字级别映射为文本表示形式，例如在 [`Formatter`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Formatter) 格式化消息的时候。 此函数也可被用来定义你自己的级别。 唯一的限制是自定义的所有级别必须使用此函数来注册，级别值必须为正整数并且其应随严重程度而递增。

​	备注

 

​	如果你考虑要定义你自己的级别，请参阅 [自定义级别](https://docs.python.org/zh-cn/3.13/howto/logging.html#custom-levels) 部分。

## logging.**getLevelNamesMapping**()

​	返回一个级别名到其对应日志记录级别的映射。 例如，字符串 "CRITICAL" 将映射到 [`CRITICAL`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.CRITICAL)。 所返回的映射是从每个对此函数的调用的内部映射拷贝的。

> Added in version 3.11.
>

## logging.**getLevelName**(*level*)

​	返回日志记录级别 *level* 的字符串表示。

​	如果 *level* 为预定义的级别 [`CRITICAL`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.CRITICAL), [`ERROR`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.ERROR), [`WARNING`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.WARNING), [`INFO`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.INFO) 或 [`DEBUG`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.DEBUG) 之一则你会得到相应的字符串。 如果你使用 [`addLevelName()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.addLevelName) 将级别关联到名称则返回你为 *level* 所关联的名称。 如果传入了与已定义级别相对应的数字值，则返回对应的字符串表示。

*level* 形参也接受级别的字符串表示例如 'INFO'。 在这种情况下，此函数将返回级别所对应的数字值。

​	如果未传入可匹配的数字或字符串值，则返回字符串 'Level %s' % level。

​	备注

 

​	级别在内部以整数表示（因为它们在日志记录逻辑中需要进行比较）。 此函数被用于在整数级别与通过 `%(levelname)s` 格式描述符方式在格式化日志输出中显示的级别名称之间进行相互的转换 (参见 [LogRecord 属性](https://docs.python.org/zh-cn/3.13/library/logging.html#logrecord-attributes))。

*在 3.4 版本发生变更:* 在早于 3.4 的 Python 版本中，此函数也可传入一个字符串形式的级别名称，并将返回对应的级别数字值。 此未记入文档的行为被视为是一个错误，并在 Python 3.4 中被移除，但又在 3.4.2 中被恢复以保持向下兼容性。

## logging.**getHandlerByName**(*name*)

​	返回具有指定 *name* 的处理器，或者如果指定名称的处理器不存在则返回 `None`。

> Added in version 3.12.
>

## logging.**getHandlerNames**()

​	返回一个由所有已知处理器名称组成的不可变集合。

> Added in version 3.12.
>

## logging.**makeLogRecord**(*attrdict*)

​	创建并返回一个新的 [`LogRecord`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LogRecord) 实例，实例属性由 *attrdict* 定义。 此函数适用于接受一个通过套接字传输的封存好的 [`LogRecord`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LogRecord) 属性字典，并在接收端将其重建为一个 [`LogRecord`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LogRecord) 实例。

## logging.**basicConfig**(***kwargs*)

​	通过使用默认的 [`Formatter`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.Formatter) 创建一个 [`StreamHandler`](https://docs.python.org/zh-cn/3.13/library/logging.handlers.html#logging.StreamHandler) 并将其加入根日志记录器来为日志记录系统执行基本配置。 如果没有为根日志记录器定义处理器则 [`debug()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.debug), [`info()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.info), [`warning()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.warning), [`error()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.error) 和 [`critical()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.critical) 等函数将自动调用 [`basicConfig()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.basicConfig)。

​	如果根日志记录器已配置了处理器则此函数将不执行任何操作，除非关键字参数 *force* 被设为 `True`。

​	备注

 

​	此函数应当在其他线程启动之前从主线程被调用。 在 2.7.1 和 3.2 之前的 Python 版本中，如果此函数从多个线程被调用，一个处理器（在极少的情况下）有可能被多次加入根日志记录器，导致非预期的结果例如日志中的消息出现重复。

​	支持以下关键字参数。

| 格式       | 描述                                                         |
| :--------- | :----------------------------------------------------------- |
| *filename* | 使用指定的文件名创建一个 [`FileHandler`](https://docs.python.org/zh-cn/3.13/library/logging.handlers.html#logging.FileHandler)，而不是 [`StreamHandler`](https://docs.python.org/zh-cn/3.13/library/logging.handlers.html#logging.StreamHandler)。 |
| *filemode* | 如果指定了 *filename*，则用此 [模式](https://docs.python.org/zh-cn/3.13/library/functions.html#filemodes) 打开该文件。 默认模式为 `'a'`。 |
| *format*   | 使用指定的格式字符串作为处理器。 默认为属性以冒号分隔的 `levelname`, `name` 和 `message`。 |
| *datefmt*  | 使用指定的日期/时间格式，与 [`time.strftime()`](https://docs.python.org/zh-cn/3.13/library/time.html#time.strftime) 所接受的格式相同。 |
| *style*    | 如果指定了 *format*，将为格式字符串使用此风格。 `'%'`, `'{'` 或 `'$'` 分别对应于 [printf 风格](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#old-string-formatting), [`str.format()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#str.format) 或 [`string.Template`](https://docs.python.org/zh-cn/3.13/library/string.html#string.Template)。 默认为 `'%'`。 |
| *level*    | 设置根记录器级别为指定的 [level](https://docs.python.org/zh-cn/3.13/library/logging.html#levels). |
| *stream*   | 使用指定的流初始化 [`StreamHandler`](https://docs.python.org/zh-cn/3.13/library/logging.handlers.html#logging.StreamHandler)。 请注意此参数与 *filename* 不兼容 —— 如果两者同时存在，则会引发 `ValueError`。 |
| *handlers* | 如果指定，这应为一个包含要加入根日志记录器的已创建处理器的可迭代对象。 任何尚未设置格式描述符的处理器将被设置为在此函数中创建的默认格式描述符。 请注意此参数与 *filename* 或 *stream* 不兼容 —— 如果两者同时存在，则会引发 `ValueError`。 |
| *force*    | 如果将此关键字参数指定为 true，则在执行其他参数指定的配置之前，将移除并关闭附加到根记录器的所有现有处理器。 |
| *encoding* | 如果此关键字参数与 *filename* 一同被指定，则其值会在创建 [`FileHandler`](https://docs.python.org/zh-cn/3.13/library/logging.handlers.html#logging.FileHandler) 时被使用，因而也会在打开输出文件时被使用。 |
| *errors*   | 如果此关键字参数与 *filename* 一同被指定，则其值会在创建 [`FileHandler`](https://docs.python.org/zh-cn/3.13/library/logging.handlers.html#logging.FileHandler) 时被使用，因而也会在打开输出文件时被使用。 如果未指定，则会使用值 'backslashreplace'。 请注意如果指定为 `None`，它将被原样传给 [`open()`](https://docs.python.org/zh-cn/3.13/library/functions.html#open)，这意味着它将会当作传入 'errors' 一样处理。 |

*在 3.2 版本发生变更:* 增加了 *style* 参数。

*在 3.3 版本发生变更:* 增加了 *handlers* 参数。 增加了额外的检查来捕获指定不兼容参数的情况 (例如同时指定 *handlers* 与 *stream* 或 *filename*，或者同时指定 *stream* 与 *filename*)。

*在 3.8 版本发生变更:* 增加了 *force* 参数。

*在 3.9 版本发生变更:* 增加了 *encoding* 和 *errors* 参数。

## logging.**shutdown**()

​	通过刷新和关闭所有处理程序来通知日志记录系统执行有序停止。 此函数应当在应用退出时被调用并且在此调用之后不应再使用日志记录系统。

​	当 logging 模块被导入时，它会将此函数注册为退出处理程序 (参见 [`atexit`](https://docs.python.org/zh-cn/3.13/library/atexit.html#module-atexit))，因此通常不需要手动执行该操作。

## logging.**setLoggerClass**(*klass*)

​	通知日志记录系统在实例化日志记录器时使用 *klass* 类。 该类应当定义 `__init__()` 使其只需要一个 name 参数，并且 `__init__()` 应当调用 `Logger.__init__()`。 此函数通常会在需要使用自定义日志记录器行为的应用程序实例化任何日志记录器之前被调用。 在此调用之后，在其他任何时候都不要直接使用该子类来实例化日志记录器：请继续使用 [`logging.getLogger()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.getLogger) API 来获取你的日志记录器。

## logging.**setLogRecordFactory**(*factory*)

​	设置一个用来创建 [`LogRecord`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LogRecord) 的可调用对象。

## 参数:

**factory** -- 用来实例化日志记录的工厂可调用对象。

> Added in version 3.2:
> 此函数与 [`getLogRecordFactory()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.getLogRecordFactory) 一起提供，以便允许开发者对如何构造表示日志记录事件的 [`LogRecord`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.LogRecord) 有更好的控制。

​	可调用对象 factory 具有如下签名:

```
factory(name, level, fn, lno, msg, args, exc_info, func=None, sinfo=None, **kwargs)
```

> ## name:
>
> ​	日志记录器名称
>
> ## level:
>
> ​	日志记录级别（数字）。
>
> ## fn:
>
> ​	进行日志记录调用的文件的完整路径名。
>
> ## lno:
>
> ​	记录调用所在文件中的行号。
>
> ## msg:
>
> ​	日志消息。
>
> ## args:
>
> ​	日志记录消息的参数。
>
> ## exc_info:
>
> ​	异常元组，或 `None` 。
>
> ## func:
>
> ​	调用日志记录调用的函数或方法的名称。
>
> ## sinfo:
>
> ​	与 [`traceback.print_stack()`](https://docs.python.org/zh-cn/3.13/library/traceback.html#traceback.print_stack) 所提供的类似的栈回溯信息，显示调用的层级结构。
>
> ## kwargs:
>
> ​	其他关键字参数。

## 模块级属性

## logging.**lastResort**

​	通过此属性提供的“最后处理者”。 这是一个以 `WARNING` 级别写入到 `sys.stderr` 的 [`StreamHandler`](https://docs.python.org/zh-cn/3.13/library/logging.handlers.html#logging.StreamHandler)，用于在没有任何日志记录配置的情况下处理日志记录事件。 最终结果就是将消息打印到 `sys.stderr`，这会替代先前形式为 "no handlers could be found for logger XYZ" 的错误消息。 如果出于某种原因你需要先前的行为，可将 `lastResort` 设为 `None`。

> Added in version 3.2.
>

## logging.**raiseExceptions**

​	用于查看在处理过程中异常是否应当被传播。

​	默认值: `True`。

​	如果 [`raiseExceptions`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.raiseExceptions) 为 `False`，则异常会被静默地忽略。 这大多数情况下是日志系统所需要的 —— 大多数用户不会关心日志系统中的错误，他们对应用程序错误更感兴趣。

## 与警告模块集成

[`captureWarnings()`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.captureWarnings) 函数可用来将 [`logging`](https://docs.python.org/zh-cn/3.13/library/logging.html#module-logging) 和 [`warnings`](https://docs.python.org/zh-cn/3.13/library/warnings.html#module-warnings) 模块集成。

## logging.**captureWarnings**(*capture*)

​	此函数用于打开和关闭日志系统对警告的捕获。

​	如果 *capture* 是 `True`，则 [`warnings`](https://docs.python.org/zh-cn/3.13/library/warnings.html#module-warnings) 模块发出的警告将重定向到日志记录系统。具体来说，将使用 [`warnings.formatwarning()`](https://docs.python.org/zh-cn/3.13/library/warnings.html#warnings.formatwarning) 格式化警告信息，并将结果字符串使用 [`WARNING`](https://docs.python.org/zh-cn/3.13/library/logging.html#logging.WARNING) 等级记录到名为 `'py.warnings'` 的记录器中。

​	如果 *capture* 是 `False`，则将停止将警告重定向到日志记录系统，并且将警告重定向到其原始目标（即在 `captureWarnings(True)` 调用之前的有效目标）。

> 参见
>
> - [`logging.config`](https://docs.python.org/zh-cn/3.13/library/logging.config.html#module-logging.config) 模块
>
>   日志记录模块的配置 API 。
>
> - [`logging.handlers`](https://docs.python.org/zh-cn/3.13/library/logging.handlers.html#module-logging.handlers) 模块
>
>   日志记录模块附带的有用处理器。
>
> - [**PEP 282**](https://peps.python.org/pep-0282/) - Logging 系统
>
>   该提案描述了Python标准库中包含的这个特性。
>
> - [Original Python logging package](https://old.red-dove.com/python_logging.html)
>
>   这是该 [`logging`](https://docs.python.org/zh-cn/3.13/library/logging.html#module-logging) 包的原始来源。该站点提供的软件包版本适用于 Python 1.5.2、2.1.x 和 2.2.x，它们不被 [`logging`](https://docs.python.org/zh-cn/3.13/library/logging.html#module-logging) 包含在标准库中。
