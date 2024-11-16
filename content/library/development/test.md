+++
title = "test --- Python 回归测试包"
date = 2024-11-15T21:03:03+08:00
weight = 70
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/test.html](https://docs.python.org/zh-cn/3.13/library/test.html)
>
> 收录该文档的时间：`2024-11-15T21:03:03+08:00`

# `test` --- Python 回归测试包

​备注
 

[`test`]({{< ref "/library/development/test#module-test" >}}) 包只供 Python 内部使用。它的记录是为了让 Python 的核心开发者受益。我们不鼓励在 Python 标准库之外使用这个包，因为这里提到的代码在 Python 的不同版本之间可能会改变或被删除而不另行通知。

------

​	`test` 包包含了 Python 的所有回归测试，以及 [`test.support`]({{< ref "/library/development/test#module-test.support" >}}) 和 [`test.regrtest`]({{< ref "/library/development/test#module-test.regrtest" >}}) 模块。 [`test.support`]({{< ref "/library/development/test#module-test.support" >}}) 用于增强你的测试，而 [`test.regrtest`]({{< ref "/library/development/test#module-test.regrtest" >}}) 驱动测试套件。

`test`包中每个名字以 ``test_`` 开头的模块都是一个特定模块或功能的测试套件。所有新的测试应该使用 [`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 或 [`doctest`]({{< ref "/library/development/doctest#module-doctest" >}}) 模块编写。一些旧的测试是使用“传统”的测试风格编写的，即比较打印出来的输出到 `sys.stdout`；这种测试风格被认为是过时的。

​参见
## 模块 [`unittest`]({{< ref "/library/development/unittest#module-unittest" >}})

​	编写 PyUnit 回归测试.

## [`doctest`]({{< ref "/library/development/doctest#module-doctest" >}}) --- 文档测试模块

​	嵌入到文档字符串的测试。



## 为 [`test`]({{< ref "/library/development/test#module-test" >}}) 包编写单元测试

​	使用 [`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 模块的测试最好是遵循一些准则。 其中一条是测试模块的名称要以 `test_` 打头并以被测试模块的名称结尾。 测试模块中的测试方法应当以 `test_` 打头并以该方法所测试的内容的说明结尾。 这很有必要因为这样测试驱动程序就会将这些方法识别为测试方法。 此外，该方法不应当包括任何文档字符串。 应当使用注释 (例如 `# Tests function returns only True or False`) 来为测试方法提供文档说明。 这样做是因为文档字符串如果存在则会被打印出来因此无法指明正在运行哪个测试。

​	有一个基本模板经常会被使用:

```
import unittest
from test import support

class MyTestCase1(unittest.TestCase):

    # 仅在需要时使用 setUp() 和 tearDown()

    def setUp(self):
        ... 为准备测试而执行的代码 ...

    def tearDown(self):
        ... 为测试后的清理而执行的代码 ...

    def test_feature_one(self):
        # 测试特性一。
        ... 测试代码 ...

    def test_feature_two(self):
        # 测试特性二。
        ... 测试代码 ...

    ... 更多的测试方法 ...

class MyTestCase2(unittest.TestCase):
    ... 与 MyTestCase1 的结构相同 ...

... 更多的测试类 ...

if __name__ == '__main__':
    unittest.main()
```

​	这种代码模式允许测试套件由 [`test.regrtest`]({{< ref "/library/development/test#module-test.regrtest" >}}) 运行，作为支持 [`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) CLI 的脚本单独运行，或者通过 `python -m unittest` CLI 来运行。

​	回归测试的目标是尝试破坏代码。 这引出了一些需要遵循的准则:

- 测试套件应当测试所有的类、函数和常量。 这不仅包括要向外界展示的外部 API 也包括“私有”的代码。

- 白盒测试（在编写测试时检查被测试的代码）是最推荐的。 黑盒测试（只测试已发布的用户接口）因不够完整而不能确保所有边界和边缘情况都被测试到。

- 确保所有可能的值包括无效的值都被测试到。 这能确保不仅全部的有效值都可被接受而且不适当的值也能被正确地处理。

- 消耗尽可能多的代码路径。 测试发生分支的地方从而调整输入以确保通过代码采取尽可能多的不同路径。

- 为受测试的代码所发现的任何代码缺陷添加明确的测试。 这将确保如果代码在将来被改变错误也不会再次出现。

- 确保在你的测试完成后执行清理（例如关闭并删除所有临时文件）。

- 如果某个测试依赖于操作系统上的特定条件那么要在尝试测试之前先验证该条件是否已存在。

- 尽可能少地导入模块并尽可能快地完成操作。 这可以最大限度地减少测试的外部依赖性并且还可以最大限度地减少导入模块带来的附带影响所导致的异常行为。

- 尝试最大限度地重用代码。 在某些情况下，测试结果会因使用不同类型的输入这样的小细节而变化。 可通过一个指定输入的类来子类化一个基本测试类来最大限度地减少重复代码:

  ```
  class TestFuncAcceptsSequencesMixin:
  
      func = mySuperWhammyFunction
  
      def test_func(self):
          self.func(self.arg)
  
  class AcceptLists(TestFuncAcceptsSequencesMixin, unittest.TestCase):
      arg = [1, 2, 3]
  
  class AcceptStrings(TestFuncAcceptsSequencesMixin, unittest.TestCase):
      arg = 'abc'
  
  class AcceptTuples(TestFuncAcceptsSequencesMixin, unittest.TestCase):
      arg = (1, 2, 3)
  ```

  当使用这种模式时，请记住所有继承自 [`unittest.TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 的类都会作为测试来运行。 上面例子中的 `TestFuncAcceptsSequencesMixin` 类没有任何数据所以其本身是无法运行的，因此它不是继承自 [`unittest.TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}})。

​参见
## 测试驱动的开发

​	Kent Beck 所著的阐述在实现代码之前编写驱动的书。



## 使用命令行界面运行测试

​	通过使用 [`-m`]({{< ref "/using/cmdline#cmdoption-m" >}}) 选项 [`test`]({{< ref "/library/development/test#module-test" >}}) 包可以作为脚本运行以驱动 Python 的回归测试套件: **python -m test**。 在内部，它使用 [`test.regrtest`]({{< ref "/library/development/test#module-test.regrtest" >}})；之前 Python 版本所使用的 **python -m test.regrtest** 调用仍然有效。 运行该脚本自身会自动开始运行 [`test`]({{< ref "/library/development/test#module-test" >}}) 包中的所有回归测试。 它通过在包中查找所有名称以 `test_` 打头的模块，导入它们，并在有 `test_main()` 函数时执行它或是在没有 `test_main` 时通过 unittest.TestLoader.loadTestsFromModule 载入测试。 要执行的测试的名称也可以被传递给脚本。 指定一个单独的回归测试 (**python -m test test_spam**) 将使输出最小化并且只打印测试通过或失败的消息。

​	直接运行 [`test`]({{< ref "/library/development/test#module-test" >}}) 将允许设置哪些资源可供测试使用。 你可以通过使用 `-u` 命令行选项来做到这一点。 指定 `all` 作为 `-u` 选项的值将启用所有可能的资源: **python -m test -uall**。 如果只需要一项资源（这是更为常见的情况），可以在 `all` 之后加一个以逗号分隔的列表来指明不需要的资源。 命令 **python -m test -uall,-audio,-largefile** 将运行 [`test`]({{< ref "/library/development/test#module-test" >}}) 并使用除 `audio` 和 `largefile` 资源之外的所有资源。 要查看所有资源的列表和更多的命令行选项，请运行 **python -m test -h**。

​	另外一些执行回归测试的方式依赖于执行测试所在的系统平台。 在 Unix 上，你可以在构建 Python 的最高层级目录中运行 **make test**。 在 Windows 上，在你的 `PCbuild` 目录中执行 **rt.bat** 将运行所有的回归测试。



# `test.support` --- 针对 Python 测试套件的工具

[`test.support`]({{< ref "/library/development/test#module-test.support" >}}) 模块提供了对 Python 的回归测试套件的支持。

​备注
 

[`test.support`]({{< ref "/library/development/test#module-test.support" >}}) 不是一个公用模块。 这篇文档是为了帮助 Python 开发者编写测试。 此模块的 API 可能被改变而不顾及发行版本之间的向下兼容性问题。

​	此模块定义了以下异常:

## *exception* test.support.**TestFailed**

​	当一个测试失败时将被引发的异常。 此异常已被弃用而应改用基于 [`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 的测试以及 [`unittest.TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 的断言方法。

## *exception* test.support.**ResourceDenied**

[`unittest.SkipTest`]({{< ref "/library/development/unittest#unittest.SkipTest" >}}) 的子类。 当一个资源（例如网络连接）不可用时将被引发。 由 [`requires()`]({{< ref "/library/development/test#test.support.requires" >}}) 函数所引发。

[`test.support`]({{< ref "/library/development/test#module-test.support" >}}) 模块定义了以下常量:

## test.support.**verbose**

​	当启用详细输出时为 `True`。 当需要有关运行中的测试的更详细信息时应当被选择。 *verbose* 是由 [`test.regrtest`]({{< ref "/library/development/test#module-test.regrtest" >}}) 来设置的。

## test.support.**is_jython**

​	如果所运行的解释器是 Jython 时为 `True`。

## test.support.**is_android**

​	如果系统是 Android 时为 `True`。

## test.support.**unix_shell**

​	如果系统不是 Windows 时则为 shell 的路径；否则为 `None`。

## test.support.**LOOPBACK_TIMEOUT**

​	使用网络服务器监听网络本地环回接口如 `127.0.0.1` 的测试的以秒为单位的超时值。

​	该超时长到足以防止测试失败：它要考虑客户端和服务器可能会在不同线程甚至不同进程中运行。

​	该超时应当对于 [`socket.socket`]({{< ref "/library/ipc/socket#socket.socket" >}}) 的 [`connect()`]({{< ref "/library/ipc/socket#socket.socket.connect" >}}), [`recv()`]({{< ref "/library/ipc/socket#socket.socket.recv" >}}) 和 [`send()`]({{< ref "/library/ipc/socket#socket.socket.send" >}}) 方法都足够长。

​	其默认值为5秒。

​	参见 [`INTERNET_TIMEOUT`]({{< ref "/library/development/test#test.support.INTERNET_TIMEOUT" >}})。

## test.support.**INTERNET_TIMEOUT**

​	发往互联网的网络请求的以秒为单位的超时值。

​	该超时短到足以避免测试在互联网请求因任何原因被阻止时等待太久。

​	通常使用 [`INTERNET_TIMEOUT`]({{< ref "/library/development/test#test.support.INTERNET_TIMEOUT" >}}) 的超时不应该将测试标记为失败，而是跳过测试：参见 [`transient_internet()`]({{< ref "/library/development/test#test.support.socket_helper.transient_internet" >}})。

​	其默认值是1分钟。

​	参见 [`LOOPBACK_TIMEOUT`]({{< ref "/library/development/test#test.support.LOOPBACK_TIMEOUT" >}})。

## test.support.**SHORT_TIMEOUT**

​	如果测试耗时“太长”而要将测试标记为失败的以秒为单位的超时值。

​	该超时值取决于 regrtest `--timeout` 命令行选项。

​	如果一个使用 [`SHORT_TIMEOUT`]({{< ref "/library/development/test#test.support.SHORT_TIMEOUT" >}}) 的测试在慢速 buildbots 上开始随机失败，请使用 [`LONG_TIMEOUT`]({{< ref "/library/development/test#test.support.LONG_TIMEOUT" >}}) 来代替。

​	其默认值为30秒。

## test.support.**LONG_TIMEOUT**

​	用于检测测试何时挂起的以秒为单位的超时值。

​	它的长度足够在最慢的 Python buildbot 上降低测试失败的风险。 如果测试耗时“过长”也不应当用它将该测试标记为失败。 此超时值依赖于 regrtest `--timeout` 命令行选项。

​	其默认值为5分钟。

​	另请参见 [`LOOPBACK_TIMEOUT`]({{< ref "/library/development/test#test.support.LOOPBACK_TIMEOUT" >}}), [`INTERNET_TIMEOUT`]({{< ref "/library/development/test#test.support.INTERNET_TIMEOUT" >}}) 和 [`SHORT_TIMEOUT`]({{< ref "/library/development/test#test.support.SHORT_TIMEOUT" >}})。

## test.support.**PGO**

​	当测试对 PGO 没有用处时设置是否要跳过测试。

## test.support.**PIPE_MAX_SIZE**

​	一个通常大于下层 OS 管道缓冲区大小的常量，以产生写入阻塞。

## test.support.**Py_DEBUG**

​	如果 Python 编译时定义了 [`Py_DEBUG`]({{< ref "/c_api/intro#c.Py_DEBUG" >}}) 宏则为 `True`，也就是说，当 Python 是 [以调试模式编译]({{< ref "/using/configure#debug-build" >}}) 的时候。

> Added in version 3.12.
>

## test.support.**SOCK_MAX_SIZE**

​	一个通常大于下层 OS 套接字缓冲区大小的常量，以产生写入阻塞。

## test.support.**TEST_SUPPORT_DIR**

​	设为包含 [`test.support`]({{< ref "/library/development/test#module-test.support" >}}) 的最高层级目录。

## test.support.**TEST_HOME_DIR**

​	设为 test 包的最高层级目录。

## test.support.**TEST_DATA_DIR**

​	设为 test 包中的 `data` 目录。

## test.support.**MAX_Py_ssize_t**

​	设为大内存测试的 [`sys.maxsize`]({{< ref "/library/python/sys#sys.maxsize" >}})。

## test.support.**max_memuse**

​	通过 [`set_memlimit()`]({{< ref "/library/development/test#test.support.set_memlimit" >}}) 设为针对大内存测试的内存限制。 受 [`MAX_Py_ssize_t`]({{< ref "/library/development/test#test.support.MAX_Py_ssize_t" >}}) 的限制。

## test.support.**real_max_memuse**

​	通过 [`set_memlimit()`]({{< ref "/library/development/test#test.support.set_memlimit" >}}) 设为针对大内存测试的内存限制。 不受 [`MAX_Py_ssize_t`]({{< ref "/library/development/test#test.support.MAX_Py_ssize_t" >}}) 的限制。

## test.support.**MISSING_C_DOCSTRINGS**

​	如果 Python 编译时不带文档字符串（即未定义 `WITH_DOC_STRINGS` 宏）则设为 `True`。 参见 [`configure --without-doc-strings`]({{< ref "/using/configure#cmdoption-without-doc-strings" >}}) 选项。

​	另请参阅 [`HAVE_DOCSTRINGS`]({{< ref "/library/development/test#test.support.HAVE_DOCSTRINGS" >}}) 变量。

## test.support.**HAVE_DOCSTRINGS**

​	如果函数带有文档字符串则设为 `True`。 参见 [`python -OO`]({{< ref "/using/cmdline#cmdoption-O" >}}) 选项，该选项会去除在 Python 中实现的函数的文档字符串。

​	另请参阅 [`MISSING_C_DOCSTRINGS`]({{< ref "/library/development/test#test.support.MISSING_C_DOCSTRINGS" >}}) 变量。

## test.support.**TEST_HTTP_URL**

​	定义用于网络测试的韧性 HTTP 服务器的 URL。

## test.support.**ALWAYS_EQ**

​	等于任何对象的对象。 用于测试混合类型比较。

## test.support.**NEVER_EQ**

​	不等于任何对象的对象 (即使是 [`ALWAYS_EQ`]({{< ref "/library/development/test#test.support.ALWAYS_EQ" >}}))。 用于测试混合类型比较。

## test.support.**LARGEST**

​	大于任何对象的对象（除了其自身）。 用于测试混合类型比较。

## test.support.**SMALLEST**

​	小于任何对象的对象（除了其自身）。 用于测试混合类型比较。Used to test mixed type comparison.

[`test.support`]({{< ref "/library/development/test#module-test.support" >}}) 模块定义了以下函数:

## test.support.**busy_retry**(*timeout*, *err_msg=None*, */*, ***, *error=True*)

​	运行循环体直到以 `break` 停止循环。

​	在 *timeout* 秒后，如果 *error* 为真值则引发 [`AssertionError`]({{< ref "/library/exceptions#AssertionError" >}})，或者如果 *error* 为假值则只停止循环。

​	示例：

```
for _ in support.busy_retry(support.SHORT_TIMEOUT):
    if check():
        break
```

​	error=False 的用法示例:

```
for _ in support.busy_retry(support.SHORT_TIMEOUT, error=False):
    if check():
        break
else:
    raise RuntimeError('my custom error')
```

## test.support.**sleeping_retry**(*timeout*, *err_msg=None*, */*, ***, *init_delay=0.010*, *max_delay=1.0*, *error=True*)

​	应用指数回退的等待策略。

​	运行循环体直到以 `break` 停止循环。 在每次循环迭代时休眠，但第一次迭代时除外。 每次迭代的休眠延时都将加倍（至多 *max_delay* 秒）。

​	请参阅 [`busy_retry()`]({{< ref "/library/development/test#test.support.busy_retry" >}}) 文档了解相关形参的用法。

​	在 SHORT_TIMEOUT 秒后引发异常的示例:

```
for _ in support.sleeping_retry(support.SHORT_TIMEOUT):
    if check():
        break
```

​	error=False 的用法示例:

```
for _ in support.sleeping_retry(support.SHORT_TIMEOUT, error=False):
    if check():
        break
else:
    raise RuntimeError('my custom error')
```

## test.support.**is_resource_enabled**(*resource*)

​	如果 *resource* 已启用并可用则返回 `True`。 可用资源列表只有当 [`test.regrtest`]({{< ref "/library/development/test#module-test.regrtest" >}}) 正在执行测试时才会被设置。

## test.support.**python_is_optimized**()

​	如果 Python 编译未使用 `-O0` 或 `-Og` 则返回 `True`。

## test.support.**with_pymalloc**()

​	返回 `_testcapi.WITH_PYMALLOC`。

## test.support.**requires**(*resource*, *msg=None*)

​	如果 *resource* 不可用则引发 [`ResourceDenied`]({{< ref "/library/development/test#test.support.ResourceDenied" >}})。 如果该异常被引发则 *msg* 为传给 [`ResourceDenied`]({{< ref "/library/development/test#test.support.ResourceDenied" >}}) 的参数。 如果被 `__name__` 为 `'__main__'` 的函数调用则总是返回 `True`。 在测试由 [`test.regrtest`]({{< ref "/library/development/test#module-test.regrtest" >}}) 执行时使用。

## test.support.**sortdict**(*dict*)

​	返回 *dict* 按键排序的 repr。

## test.support.**findfile**(*filename*, *subdir=None*)

​	返回名为 *filename* 的文件的路径。 如果未找到匹配结果则返回 *filename*。 这并不等于失败因为它也算是该文件的路径。

​	设置 *subdir* 指明要用来查找文件的相对路径而不是直接在路径目录中查找。

## test.support.**get_pagesize**()

​	获取以字节表示的分页大小。

> Added in version 3.12.
>

## test.support.**setswitchinterval**(*interval*)

​	将 [`sys.setswitchinterval()`]({{< ref "/library/python/sys#sys.setswitchinterval" >}}) 设为给定的 *interval*。 请为 Android 系统定义一个最小间隔以防止系统挂起。

## test.support.**check_impl_detail**(***guards*)

​	使用此检测来保护 CPython 实现专属的测试或者仅在有这些参数保护的实现上运行它们。 此函数将根据主机系统平台的不同返回 `True` 或 `False`。 用法示例:

```
check_impl_detail()               # 仅限 CPython (默认)。
check_impl_detail(jython=True)    # 仅限 Jython。
check_impl_detail(cpython=False)  # 除 CPython 以外的任何地方。
```

## test.support.**set_memlimit**(*limit*)

​	针对大内存测试设置 [`max_memuse`]({{< ref "/library/development/test#test.support.max_memuse" >}}) 和 [`real_max_memuse`]({{< ref "/library/development/test#test.support.real_max_memuse" >}}) 的值。

## test.support.**record_original_stdout**(*stdout*)

​	存放来自 *stdout* 的值。 它会在回归测试开始时处理 stdout。

## test.support.**get_original_stdout**()

​	返回 [`record_original_stdout()`]({{< ref "/library/development/test#test.support.record_original_stdout" >}}) 所设置的原始 stdout 或者如果未设置则为 `sys.stdout`。

## test.support.**args_from_interpreter_flags**()

​	返回在 `sys.flags` 和 `sys.warnoptions` 中重新产生当前设置的命令行参数列表。

## test.support.**optim_args_from_interpreter_flags**()

​	返回在 `sys.flags` 中重新产生当前优化设置的命令行参数列表。

## test.support.**captured_stdin**()

## test.support.**captured_stdout**()

## test.support.**captured_stderr**()

​	使用 [`io.StringIO`]({{< ref "/library/allos/io#io.StringIO" >}}) 对象临时替换指定流的上下文管理器。

​	使用输出流的示例:

```
with captured_stdout() as stdout, captured_stderr() as stderr:
    print("hello")
    print("error", file=sys.stderr)
assert stdout.getvalue() == "hello\n"
assert stderr.getvalue() == "error\n"
```

​	使用输入流的示例:

```
with captured_stdin() as stdin:
    stdin.write('hello\n')
    stdin.seek(0)
    # 调用接受 sys.stdin 的代码
    captured = input()
self.assertEqual(captured, "hello")
```

## test.support.**disable_faulthandler**()

​	临时禁用 [`faulthandler`]({{< ref "/library/debug/faulthandler#module-faulthandler" >}}) 的上下文管理器。

## test.support.**gc_collect**()

​	强制收集尽可能多的对象。 这是有必要的因为垃圾回收器并不能保证及时回收资源。 这意味着 `__del__` 方法的调用可能会晚于预期而弱引用的存活长于预期。

## test.support.**disable_gc**()

​	在进入时禁用垃圾回收器的上下文管理器。 在退出时，垃圾回收器将恢复到先前状态。

## test.support.**swap_attr**(*obj*, *attr*, *new_val*)

​	上下文管理器用一个新对象来交换一个属性。

​	用法：

```
with swap_attr(obj, "attr", 5):
    ...
```

​	这将把 `obj.attr` 设为 5 并在 `with` 语句块内保持，在语句块结束时恢复旧值。 如果 `attr` 不存在于 `obj` 中，它将被创建并在语句块结束时被删除。

​	旧值 (或者如果不存在旧值则为 `None`) 将被赋给 "as" 子句的目标，如果存在子句的话。

## test.support.**swap_item**(*obj*, *attr*, *new_val*)

​	上下文件管理器用一个新对象来交换一个条目。

​	用法：

```
with swap_item(obj, "item", 5):
    ...
```

​	这将把 `obj["item"]` 设为 5 并在 `with` 语句块内保持，在语句块结束时恢复旧值。 如果 `item` 不存在于 `obj` 中，它将被创建并在语句块结束时被删除。

​	旧值 (或者如果不存在旧值则为 `None`) 将被赋给 "as" 子句的目标，如果存在子句的话。

## test.support.**flush_std_streams**()

​	在 [`sys.stdout`]({{< ref "/library/python/sys#sys.stdout" >}}) 然后又在 [`sys.stderr`]({{< ref "/library/python/sys#sys.stderr" >}}) 上调用 `flush()` 方法。 它可被用来确保日志顺序在写入到 stderr 之前的一致性。

> Added in version 3.11.
>

## test.support.**print_warning**(*msg*)

​	打印一个警告到 [`sys.__stderr__`]({{< ref "/library/python/sys#sys.__stderr__" >}})。 将消息格式化为: `f"Warning -- {msg}"`。 如果 *msg* 包含多行，则为每行添加 `"Warning -- "` 前缀。

> Added in version 3.9.
>

## test.support.**wait_process**(*pid*, ***, *exitcode*, *timeout=None*)

​	等待直到进程 *pid* 结束并检查进程退出代码是否为 *exitcode*。

​	如果进程退出代码不等于 *exitcode* 则引发 [`AssertionError`]({{< ref "/library/exceptions#AssertionError" >}})。

​	如果进程运行时长超过 *timeout* 秒 (默认为 [`SHORT_TIMEOUT`]({{< ref "/library/development/test#test.support.SHORT_TIMEOUT" >}}))，则杀死进程并引发 [`AssertionError`]({{< ref "/library/exceptions#AssertionError" >}})。 超时特性在 Windows 上不可用。

> Added in version 3.9.
>

## test.support.**calcobjsize**(*fmt*)

​	返回 [`PyObject`](https://docs.python.org/zh-cn/3.13/c-api/structures.html#c.PyObject) 的大小，其结构成员由 *fmt* 定义。 返回的值包括 Python 对象头的大小和对齐方式。

## test.support.**calcvobjsize**(*fmt*)

​	返回 [`PyVarObject`](https://docs.python.org/zh-cn/3.13/c-api/structures.html#c.PyVarObject) 的大小，其结构成员由 *fmt* 定义。 返回的值包括 Python 对象头的大小和对齐方式。

## test.support.**checksizeof**(*test*, *o*, *size*)

​	对于测试用例 *test*，断言 *o* 的 `sys.getsizeof` 加 GC 头的大小等于 *size*。

## @test.support.**anticipate_failure**(*condition*)

​	一个有条件地用 [`unittest.expectedFailure()`]({{< ref "/library/development/unittest#unittest.expectedFailure" >}}) 来标记测试的装饰器。 任何对此装饰器的使用都应当具有标识相应追踪事项的有关联注释。

## test.support.**system_must_validate_cert**(*f*)

​	一个在 TLS 证书验证失败时跳过被装饰测试的装饰器。

## @test.support.**run_with_locale**(*catstr*, **locales*)

​	一个在不同语言区域下运行函数的装饰器，并在其结束后正确地重置语言区域。 *catstr* 是字符串形式的语言区域类别 (例如 `"LC_ALL"`)。 传入的 *locales* 将依次被尝试，并将使用第一个有效的语言区域。

## @test.support.**run_with_tz**(*tz*)

​	一个在指定时区下运行函数的装饰器，并在其结束后正确地重置时区。

## @test.support.**requires_freebsd_version**(**min_version*)

​	当在 FreeBSD 上运行测试时指定最低版本的装饰器。 如果 FreeBSD 版本号低于指定值，测试将被跳过。

## @test.support.**requires_linux_version**(**min_version*)

​	当在 Linux 上运行测试时指定最低版本的装饰器。 如果 Linux 版本号低于指定值，测试将被跳过。

## @test.support.**requires_mac_version**(**min_version*)

​	当在 macOS 上运行测试时指定最低版本的装饰器。 如果 macOS 版本号低于指定值，测试将被跳过。

## @test.support.**requires_gil_enabled**

​	在自由线程编译版上跳过测试的装饰器。 如果禁用了 [GIL]({{< ref "/glossary/idx#term-GIL" >}})，测试将被跳过。

## @test.support.**requires_IEEE_754**

​	用于在非 non-IEEE 754 平台上跳过测试的装饰器。

## @test.support.**requires_zlib**

​	用于当 [`zlib`]({{< ref "/library/archiving/zlib#module-zlib" >}}) 不存在时跳过测试的装饰器。

## @test.support.**requires_gzip**

​	用于当 [`gzip`]({{< ref "/library/archiving/gzip#module-gzip" >}}) 不存在时跳过测试的装饰器。

## @test.support.**requires_bz2**

​	用于当 [`bz2`]({{< ref "/library/archiving/bz2#module-bz2" >}}) 不存在时跳过测试的装饰器。

## @test.support.**requires_lzma**

​	用于当 [`lzma`]({{< ref "/library/archiving/lzma#module-lzma" >}}) 不存在时跳过测试的装饰器。

## @test.support.**requires_resource**(*resource*)

​	用于当 *resource* 不可用时跳过测试的装饰器。

## @test.support.**requires_docstrings**

​	用于仅当 [`HAVE_DOCSTRINGS`]({{< ref "/library/development/test#test.support.HAVE_DOCSTRINGS" >}}) 时才运行测试的装饰器。

## @test.support.**requires_limited_api**

​	设置仅在 [受限 C API]({{< ref "/c_api/stable#limited-c-api" >}}) 可用时运行测试的装饰器。

## @test.support.**cpython_only**

​	表示仅适用于 CPython 的测试的装饰器。

## @test.support.**impl_detail**(*msg=None*, ***guards*)

​	用于在 *guards* 上发起调用 [`check_impl_detail()`]({{< ref "/library/development/test#test.support.check_impl_detail" >}}) 的装饰器。 如果调用返回 `False`，则使用 *msg* 作为跳过测试的原因。

## @test.support.**no_tracing**

​	用于在测试期间临时关闭追踪的装饰器。

## @test.support.**refcount_test**

​	用于涉及引用计数的测试的装饰器。 如果测试不是由 CPython 运行则该装饰器不会运行测试。 在测试期间会取消设置任何追踪函数以由追踪函数导致的意外引用计数。

## @test.support.**bigmemtest**(*size*, *memuse*, *dry_run=True*)

​	用于大内存测试的装饰器。

*size* 是测试所请求的大小（以任意的，由测试解读的单位。） *memuse* 是测试的每单元字节数，或是对它的良好估计。 例如，一个需要两个字节缓冲区，每个缓冲区 4 GiB，则可以用 `@bigmemtest(size=_4G, memuse=2)` 来装饰。

*size* 参数通常作为额外参数传递给被测试的方法。 如果 *dry_run* 为 `True`，则传给测试方法的值可能少于所请求的值。 如果 *dry_run* 为 `False`，则意味着当当未指定 `-M` 时测试将不支持虚拟运行。

## @test.support.**bigaddrspacetest**

​	用于填充地址空间的测试的装饰器。

## test.support.**check_syntax_error**(*testcase*, *statement*, *errtext=''*, ***, *lineno=None*, *offset=None*)

​	用于通过尝试编译 *statement* 来测试 *statement* 中的语法错误。 *testcase* 是测试的 [`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 实例。 *errtext* 是应当匹配所引发的 [`SyntaxError`]({{< ref "/library/exceptions#SyntaxError" >}}) 的字符串表示形式的正则表达式。 如果 *lineno* 不为 `None`，则与异常所在的行进行比较。 如果 *offset* 不为 `None`，则与异常的偏移量进行比较。

## test.support.**open_urlresource**(*url*, **args*, ***kw*)

​	打开 *url*。 如果打开失败，则引发 [`TestFailed`]({{< ref "/library/development/test#test.support.TestFailed" >}})。

## test.support.**reap_children**()

​	只要有子进程启动就在 `test_main` 的末尾使用此函数。 这将有助于确保没有多余的子进程（僵尸）存在占用资源并在查找引用泄漏时造成问题。

## test.support.**get_attribute**(*obj*, *name*)

​	获取一个属性，如果引发了 [`AttributeError`]({{< ref "/library/exceptions#AttributeError" >}}) 则会引发 [`unittest.SkipTest`]({{< ref "/library/development/unittest#unittest.SkipTest" >}})。

## test.support.**catch_unraisable_exception**()

​	使用 [`sys.unraisablehook()`]({{< ref "/library/python/sys#sys.unraisablehook" >}}) 来捕获不可引发的异常的上下文管理器。

​	存储异常值 (`cm.unraisable.exc_value`) 会创建一个引用循环。 引用循环将在上下文管理器退出时被显式地打破。

​	存储对象 (`cm.unraisable.object`) 如果被设置为一个正在最终化的对象则可以恢复它。 退出上下文管理器将清除已存在对象。

​	用法：

```
with support.catch_unraisable_exception() as cm:
    # 创建一个“不可引发的异常”的代码
    ...

    # 检测这个不可引发的异常：使用 cm.unraisable
    ...

# 此时 cm.unraisable 属性已不存在
# （以打破循环引用）
```

> Added in version 3.8.
>

## test.support.**load_package_tests**(*pkg_dir*, *loader*, *standard_tests*, *pattern*)

​	在测试包中使用的 [`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) `load_tests` 协议的通用实现。 *pkg_dir* 是包的根目录；*loader*, *standard_tests* 和 *pattern* 是 `load_tests` 所期望的参数。 在简单的情况下，测试包的 `__init__.py` 可以是下面这样的:

```
import os
from test.support import load_package_tests

def load_tests(*args):
    return load_package_tests(os.path.dirname(__file__), *args)
```

## test.support.**detect_api_mismatch**(*ref_api*, *other_api*, ***, *ignore=()*)

​	返回未在 *other_api* 中找到的 *ref_api* 的属性、函数或方法的集合，除去在 *ignore* 中指明的要在这个检查中忽略的已定义条目列表。

​	在默认情况下这将跳过以 '_' 打头的私有属性但包括所有魔术方法，即以 '__' 打头和结尾的方法。

> Added in version 3.5.
>

## test.support.**patch**(*test_instance*, *object_to_patch*, *attr_name*, *new_value*)

​	用 *new_value* 重载 *object_to_patch.attr_name*。并向 *test_instance* 添加清理过程以便为 *attr_name* 恢复 *object_to_patch*。 *attr_name* 应当是 *object_to_patch* 的一个有效属性。

## test.support.**run_in_subinterp**(*code*)

​	在子解释器中运行 *code*。 如果启用了 [`tracemalloc`]({{< ref "/library/debug/tracemalloc#module-tracemalloc" >}}) 则会引发 [`unittest.SkipTest`]({{< ref "/library/development/unittest#unittest.SkipTest" >}})。

## test.support.**check_free_after_iterating**(*test*, *iter*, *cls*, *args=()*)

​	断言 *cls* 的实例在迭代后被释放。

## test.support.**missing_compiler_executable**(*cmd_names=[]*)

​	检查在 *cmd_names* 中列出名称的或者当 *cmd_names* 为空时所有的编译器可执行文件是否存在并返回第一个丢失的可执行文件或者如果未发现任何丢失则返回 `None`。

## test.support.**check__all__**(*test_case*, *module*, *name_of_module=None*, *extra=()*, *not_exported=()*)

​	断言 *module* 的 `__all__` 变量包含全部公共名称。

​	模块的公共名称（它的 API）是根据它们是否符合公共名称惯例并在 *module* 中被定义来自动检测的。

*name_of_module* 参数可以（用字符串或元组的形式）指定一个 API 可以被定义为什么模块以便被检测为一个公共 API。 一种这样的情况会在 *module* 从其他模块，可能是一个 C 后端 (如 `csv` 和它的 `_csv`) 导入其公共 API 的某一组成部分时发生。

*extra* 参数可以是一个在其他情况下不会被自动检测为 "public" 的名称的集合，例如没有适当的 [`__module__`]({{< ref "/library/stdtypes#definition.__module__" >}}) 属性的对象。 如果提供该参数，它将被添加到被自动检测的对象中。

*not_exported* 参数可以是一个不可被当作公共 API 的一部分的名称集合，即使其名称没有显式指明这一点。

​	用法示例:

```
import bar
import foo
import unittest
from test import support

class MiscTestCase(unittest.TestCase):
    def test__all__(self):
        support.check__all__(self, foo)

class OtherTestCase(unittest.TestCase):
    def test__all__(self):
        extra = {'BAR_CONST', 'FOO_CONST'}
        not_exported = {'baz'}  # 未写入文档的名称。
        # bar 从 _bar 导入其 API 的一部分。
        support.check__all__(self, bar, ('bar', '_bar'),
                             extra=extra, not_exported=not_exported)
```

> Added in version 3.6.
>

## test.support.**skip_if_broken_multiprocessing_synchronize**()

​	如果没有 `multiprocessing.synchronize` 模块，没有可用的 semaphore 实现，或者如果创建一个锁会引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 则跳过测试。

> Added in version 3.10.
>

## test.support.**check_disallow_instantiation**(*test_case*, *tp*, **args*, ***kwds*)

​	断言类型 *tp* 不能使用 *args* 和 *kwds* 来实例化。

> Added in version 3.10.
>

## test.support.**adjust_int_max_str_digits**(*max_digits*)

​	此函数返回一个将在上下文生效期间改变全局 [`sys.set_int_max_str_digits()`]({{< ref "/library/python/sys#sys.set_int_max_str_digits" >}}) 设置的上下文管理器以便允许执行当在整数和字符串之间进行转换时需要对位数有不同限制的测试代码。

> Added in version 3.11.
>

[`test.support`]({{< ref "/library/development/test#module-test.support" >}}) 模块定义了以下的类:

## *class* test.support.**SuppressCrashReport**

​	一个用于在预期会使子进程崩溃的测试时尽量防止弹出崩溃对话框的上下文管理器。

​	在 Windows 上，它会使用 [SetErrorMode](https://msdn.microsoft.com/en-us/library/windows/desktop/ms680621.aspx) 来禁用 Windows 错误报告对话框。

​	在 UNIX 上，会使用 [`resource.setrlimit()`]({{< ref "/library/unix/resource#resource.setrlimit" >}}) 来将 [`resource.RLIMIT_CORE`]({{< ref "/library/unix/resource#resource.RLIMIT_CORE" >}}) 的软限制设为 0 以防止创建核心转储文件。

​	在这两个平台上，旧值都可通过 [`__exit__()`]({{< ref "/reference/datamodel#object.__exit__" >}}) 恢复。

## *class* test.support.**SaveSignals**

​	用于保存和恢复由 Python 句柄的所注册的信号处理器。

## **save**(*self*)

​	将信号处理器保存到一个将信号编号映射到当前信号处理器的字典。

## **restore**(*self*)

​	将来自 [`save()`]({{< ref "/library/development/test#test.support.SaveSignals.save" >}}) 字典的信号编号设置到已保存的处理器上。

## *class* test.support.**Matcher**

## **matches**(*self*, *d*, ***kwargs*)

​	尝试对单个字典与所提供的参数进行匹配。

## **match_value**(*self*, *k*, *dv*, *v*)

​	尝试对单个已存储值 (*dv*) 与所提供的值 (*v*) 进行匹配。



# [`test.support.socket_helper`]({{< ref "/library/development/test#module-test.support.socket_helper" >}}) --- 用于套接字测试的工具

[`test.support.socket_helper`]({{< ref "/library/development/test#module-test.support.socket_helper" >}}) 模块提供了对套接字测试的支持。

> Added in version 3.9.
>

## test.support.socket_helper.**IPV6_ENABLED**

​	设置为 `True` 如果主机打开IPv6, 否则 `False` .

## test.support.socket_helper.**find_unused_port**(*family=socket.AF_INET*, *socktype=socket.SOCK_STREAM*)

​	返回一个应当适合绑定的未使用端口。 这是通过创建一个与 `sock` 形参相同协议族和类型的临时套接字来达成的 (默认为 [`AF_INET`]({{< ref "/library/ipc/socket#socket.AF_INET" >}}), [`SOCK_STREAM`]({{< ref "/library/ipc/socket#socket.SOCK_STREAM" >}}))，并将其绑定到指定的主机地址 (默认为 `0.0.0.0`) 并将端口设为 0，以从 OS 引出一个未使用的瞬时端口。 这个临时套接字随后将被关闭并删除，然后返回该瞬时端口。

​	这个方法或 [`bind_port()`]({{< ref "/library/development/test#test.support.socket_helper.bind_port" >}}) 应当被用于任何在测试期间需要绑定到特定端口的测试。 具体使用哪个取决于调用方代码是否会创建 Python 套接字，或者是否需要在构造器中提供或向外部程序提供未使用的端口（例如传给 openssl 的 s_server 模式的 `-accept` 参数）。 在可能的情况下将总是优先使用 [`bind_port()`]({{< ref "/library/development/test#test.support.socket_helper.bind_port" >}}) 而非 [`find_unused_port()`]({{< ref "/library/development/test#test.support.socket_helper.find_unused_port" >}})。 不建议使用硬编码的端口因为将使测试的多个实例无法同时运行，这对 buildbot 来说是个问题。

## test.support.socket_helper.**bind_port**(*sock*, *host=HOST*)

​	将套接字绑定到一个空闲端口并返回端口号。 这依赖于瞬时端口以确保我们能使用一个未绑定端口。 这很重要因为可能会有许多测试同时运行，特别是在 buildbot 环境中。 如果 `sock.family` 为 [`AF_INET`]({{< ref "/library/ipc/socket#socket.AF_INET" >}}) 而 `sock.type` 为 [`SOCK_STREAM`]({{< ref "/library/ipc/socket#socket.SOCK_STREAM" >}})，并且套接字上设置了 `SO_REUSEADDR` 或 `SO_REUSEPORT` 则此方法将引发异常。 测试绝不应该为 TCP/IP 套接字设置这些套接字选项。 唯一需要设置这些选项的情况是通过多个 UDP 套接字来测试组播。

​	此外，如果 `SO_EXCLUSIVEADDRUSE` 套接字选项是可用的（例如在 Windows 上），它将在套接字上被设置。 这将阻止其他任何人在测试期间绑定到我们的主机/端口。

## test.support.socket_helper.**bind_unix_socket**(*sock*, *addr*)

​	绑定一个 Unix 套接字，如果 [`PermissionError`]({{< ref "/library/exceptions#PermissionError" >}}) 被引发则会引发 [`unittest.SkipTest`]({{< ref "/library/development/unittest#unittest.SkipTest" >}})。

## @test.support.socket_helper.**skip_unless_bind_unix_socket**

​	一个用于运行需要 Unix 套接字 `bind()` 功能的测试的装饰器。

## test.support.socket_helper.**transient_internet**(*resource_name*, ***, *timeout=30.0*, *errnos=()*)

​	一个在互联网连接的各种问题以异常的形式表现出来时会引发 [`ResourceDenied`]({{< ref "/library/development/test#test.support.ResourceDenied" >}}) 的上下文管理器。



# [`test.support.script_helper`]({{< ref "/library/development/test#module-test.support.script_helper" >}}) --- 用于 Python 执行测试工具

[`test.support.script_helper`]({{< ref "/library/development/test#module-test.support.script_helper" >}}) 模块提供了对 Python 的脚本执行测试的支持。

## test.support.script_helper.**interpreter_requires_environment**()

​	如果 `sys.executable interpreter` 需要环境变量才能运行则返回 `True`。

​	这被设计用来配合 `@unittest.skipIf()` 以便标注需要使用to annotate tests that need to use an `assert_python*()` 函数来启动隔离模式 (`-I`) 或无环境模式 (`-E`) 子解释器的测试。

​	正常的编译和测试运行不会进入这种状况但它在尝试从一个使用 Python 的当前家目录查找逻辑找不到明确的家目录的解释器运行标准库测试套件时有可能发生。

​	设置 [`PYTHONHOME`]({{< ref "/using/cmdline#envvar-PYTHONHOME" >}}) 是一种能让大多数测试套件在这种情况下运行的办法。 [`PYTHONPATH`]({{< ref "/using/cmdline#envvar-PYTHONPATH" >}}) 或 `PYTHONUSERSITE` 是另外两个可影响解释器是否能启动的常见环境变量。

## test.support.script_helper.**run_python_until_end**(**args*, ***env_vars*)

​	基于 *env_vars* 设置环境以便在子进程中运行解释器。 它的值可以包括 `__isolated`, `__cleanenv`, `__cwd`, and `TERM`。

> 在 3.9 版本发生变更: 此函数不会再从 *stderr* 去除空格符。

## test.support.script_helper.**assert_python_ok**(**args*, ***env_vars*)

​	断言附带 *args* 和可选的环境变量 *env_vars* 运行解释器会成功 (`rc == 0`) 并返回一个 `(return code, stdout, stderr)` 元组。

​	如果设置了 *__cleanenv* 仅限关键字形参，*env_vars* 会被用作一个全新的环境。

​	Python 是以隔离模式 (命令行选项 `-I`) 启动的，除非 *__isolated* 仅限关键字形参被设为 `False`。

> 在 3.9 版本发生变更: 此函数不会再从 *stderr* 去除空格符。

## test.support.script_helper.**assert_python_failure**(**args*, ***env_vars*)

​	断言附带 *args* 和可选的环境变量 *env_vars* 运行解释器会失败 (`rc != 0`) 并返回一个 `(return code, stdout, stderr)` 元组。

​	更多选项请参阅 [`assert_python_ok()`]({{< ref "/library/development/test#test.support.script_helper.assert_python_ok" >}})。

> 在 3.9 版本发生变更: 此函数不会再从 *stderr* 去除空格符。

## test.support.script_helper.**spawn_python**(**args*, *stdout=subprocess.PIPE*, *stderr=subprocess.STDOUT*, ***kw*)

​	使用给定的参数运行一个 Python 子进程。

*kw* 是要传给 [`subprocess.Popen()`]({{< ref "/library/concurrency/subprocess#subprocess.Popen" >}}) 的额外关键字参数。 返回一个 [`subprocess.Popen`]({{< ref "/library/concurrency/subprocess#subprocess.Popen" >}}) 对象。

## test.support.script_helper.**kill_python**(*p*)

​	运行给定的 [`subprocess.Popen`]({{< ref "/library/concurrency/subprocess#subprocess.Popen" >}}) 进程直至完成并返回 stdout。

## test.support.script_helper.**make_script**(*script_dir*, *script_basename*, *source*, *omit_suffix=False*)

​	在路径 *script_dir* 和 *script_basename* 中创建包含 *source* 的脚本。 如果 *omit_suffix* 为 `False`，则为名称添加 `.py`。 返回完整的脚本路径。

## test.support.script_helper.**make_zip_script**(*zip_dir*, *zip_basename*, *script_name*, *name_in_zip=None*)

​	使用 *zip_dir* 和 *zip_basename* 创建扩展名为 `zip` 的 zip 文件，其中包含 *script_name* 中的文件。 *name_in_zip* 为归档名。 返回一个包含 `(full path, full path of archive name)` 的元组。

## test.support.script_helper.**make_pkg**(*pkg_dir*, *init_source=''*)

​	创建一个名为 *pkg_dir* 的目录，其中包含一个 `__init__` 文件并以 *init_source* 作为其内容。

## test.support.script_helper.**make_zip_pkg**(*zip_dir*, *zip_basename*, *pkg_name*, *script_basename*, *source*, *depth=1*, *compiled=False*)

​	使用 *zip_dir* 和 *zip_basename* 创建一个 zip 包目录，其中包含一个空的 `__init__` 文件和一个包含 *source* 的文件 *script_basename*。 如果 *compiled* 为 `True`，则两个源文件将被编译并添加到 zip 包中。 返回一个以完整 zip 路径和 zip 文件归档名为元素的元组。



# [`test.support.bytecode_helper`]({{< ref "/library/development/test#module-test.support.bytecode_helper" >}}) --- 用于测试正确字节码生成的支持工具

[`test.support.bytecode_helper`]({{< ref "/library/development/test#module-test.support.bytecode_helper" >}}) 模块提供了对测试和检查字节码生成的支持。

> Added in version 3.9.
>

​	The module defines the following class:

## *class* test.support.bytecode_helper.**BytecodeTestCase**(*unittest.TestCase*)

​	这个类具有用于检查字节码的自定义断言。

## BytecodeTestCase.**get_disassembly_as_string**(*co*)

​	以字符串形式返回 *co* 的汇编码。

## BytecodeTestCase.**assertInBytecode**(*x*, *opname*, *argval=_UNSPECIFIED*)

​	如果找到 *opname* 则返回 instr，否则抛出 [`AssertionError`]({{< ref "/library/exceptions#AssertionError" >}})。

## BytecodeTestCase.**assertNotInBytecode**(*x*, *opname*, *argval=_UNSPECIFIED*)

​	如果找到 *opname* 则抛出 [`AssertionError`]({{< ref "/library/exceptions#AssertionError" >}})。



# [`test.support.threading_helper`]({{< ref "/library/development/test#module-test.support.threading_helper" >}}) --- 用于线程测试的工具

[`test.support.threading_helper`]({{< ref "/library/development/test#module-test.support.threading_helper" >}}) 模块提供了对线程测试的支持。

> Added in version 3.10.
>

## test.support.threading_helper.**join_thread**(*thread*, *timeout=None*)

​	在 *timeout* 秒之内合并一个 *thread*。 如果线程在 *timeout* 秒后仍然存活则引发 [`AssertionError`]({{< ref "/library/exceptions#AssertionError" >}})。

## @test.support.threading_helper.**reap_threads**

​	用于确保即使测试失败线程仍然会被清理的装饰器。

## test.support.threading_helper.**start_threads**(*threads*, *unlock=None*)

​	启动 *threads* 的上下文管理器，该参数为一个线程序列。 *unlock* 是一个在所有线程启动之后被调用的函数，即使引发了异常也会执行；一个例子是 [`threading.Event.set()`]({{< ref "/library/concurrency/threading#threading.Event.set" >}})。 `start_threads` 将在退出时尝试合并已启动的线程。

## test.support.threading_helper.**threading_cleanup**(**original_values*)

​	清理未在 *original_values* 中指定的线程。 被设计为如果有一个测试在后台离开正在运行的线程时会发出警告。

## test.support.threading_helper.**threading_setup**()

​	返回当前线程计数和悬空线程的副本。

## test.support.threading_helper.**wait_threads_exit**(*timeout=None*)

​	等待直到 `with` 语句中所有已创建线程退出的上下文管理器。

## test.support.threading_helper.**catch_threading_exception**()

​	使用 [`threading.excepthook()`]({{< ref "/library/concurrency/threading#threading.excepthook" >}}) 来捕获 [`threading.Thread`]({{< ref "/library/concurrency/threading#threading.Thread" >}}) 异常的上下文管理器。

​	当异常被捕获时要设置的属性:

- `exc_type`
- `exc_value`
- `exc_traceback`
- `thread`

​	参见 [`threading.excepthook()`]({{< ref "/library/concurrency/threading#threading.excepthook" >}}) 文档。

​	这些属性在上下文管理器退出时将被删除。

​	用法：

```
with threading_helper.catch_threading_exception() as cm:
    # 生成一个引发异常的线程的代码
    ...

    # 检测这个线程异常，使用 cm 的属性：
    # exc_type, exc_value, exc_traceback, thread
    ...

# 此时 cm 的 exc_type, exc_value, exc_traceback, thread 属性
# 已不存在
# （以避免循环引用）
```

> Added in version 3.8.
>



# [`test.support.os_helper`]({{< ref "/library/development/test#module-test.support.os_helper" >}}) --- 用于操作系统测试的工具

[`test.support.os_helper`]({{< ref "/library/development/test#module-test.support.os_helper" >}}) 模块提供了对操作系统测试的支持。

> Added in version 3.10.
>

## test.support.os_helper.**FS_NONASCII**

​	一个可通过 [`os.fsencode()`]({{< ref "/library/allos/os#os.fsencode" >}}) 编码的非 ASCII 字符。

## test.support.os_helper.**SAVEDCWD**

​	设置为 [`os.getcwd()`]({{< ref "/library/allos/os#os.getcwd" >}})。

## test.support.os_helper.**TESTFN**

​	设置为一个可以安全地用作临时文件名的名称。 任何被创建的临时文件都应当被关闭和撤销链接（移除）。

## test.support.os_helper.**TESTFN_NONASCII**

​	如果存在的话，设置为一个包含 [`FS_NONASCII`]({{< ref "/library/development/test#test.support.os_helper.FS_NONASCII" >}}) 字符的文件名。 这会确保当文件名存在时，它可使用默认文件系统编码格式来编码和解码。 这允许需要非 ASCII 文件名的测试在其不可用的平台上被方便地跳过。

## test.support.os_helper.**TESTFN_UNENCODABLE**

​	设置为一个应当在严格模式下不可使用文件系统编码格式来编码的文件名（str 类型）。 如果无法生成这样的文件名则可以为 `None`。

## test.support.os_helper.**TESTFN_UNDECODABLE**

​	设置为一个应当在严格模式下不可使用文件系统编码格式来编码的文件名（bytes 类型）。 如果无法生成这样的文件名则可以为 `None`。

## test.support.os_helper.**TESTFN_UNICODE**

​	设置为用于临时文件的非 ASCII 名称。

## *class* test.support.os_helper.**EnvironmentVarGuard**

​	用于临时性地设置或取消设置环境变量的类。 其实例可被用作上下文管理器并具有完整的字典接口用来查询/修改下层的 `os.environ`。 在从上下文管理器退出之后所有通过此实例对环境变量进行的修改都将被回滚。

> 在 3.1 版本发生变更: 增加了字典接口。

## *class* test.support.os_helper.**FakePath**(*path*)

​	简单的 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。 它实现了返回 *path* 参数的 [`__fspath__()`]({{< ref "/library/allos/os#os.PathLike.__fspath__" >}}) 方法。 如果 *path* 是一个异常，它将在 `__fspath__()` 中被引发。

## EnvironmentVarGuard.**set**(*envvar*, *value*)

​	临时性地将环境变量 `envvar` 的值设为 `value`。

## EnvironmentVarGuard.**unset**(*envvar*)

​	临时性地取消设置环境变量 `envvar`。

## test.support.os_helper.**can_symlink**()

​	如果操作系统支持符号链接则返回 `True`，否则返回 `False`。

## test.support.os_helper.**can_xattr**()

​	如果操作系统支持 xattr 支返回 `True`，否则返回 `False`。

## test.support.os_helper.**change_cwd**(*path*, *quiet=False*)

​	一个临时性地将当前工作目录改为 *path* 并输出该目录的上下文管理器。

​	如果 *quiet* 为 `False`，此上下文管理器将在发生错误时引发一个异常。 在其他情况下，它将只发出一个警告并将当前工作目录保持原状。

## test.support.os_helper.**create_empty_file**(*filename*)

​	创建一个名为 *filename* 的空文件。 如果文件已存在，则清空其内容。

## test.support.os_helper.**fd_count**()

​	统计打开的文件描述符数量。

## test.support.os_helper.**fs_is_case_insensitive**(*directory*)

​	如果 *directory* 的文件系统对大小写敏感则返回 `True`。

## test.support.os_helper.**make_bad_fd**()

​	通过打开并关闭临时文件来创建一个无效的文件描述符，并返回其描述器。

## test.support.os_helper.**rmdir**(*filename*)

​	在 *filename* 上调用 [`os.rmdir()`]({{< ref "/library/allos/os#os.rmdir" >}})。 在 Windows 平台上，这将使用一个检测文件是否存在的等待循环来包装，需要这样做是因为反病毒程序会保持文件打开并阻止其被删除。

## test.support.os_helper.**rmtree**(*path*)

​	在 *path* 上调用 [`shutil.rmtree()`]({{< ref "/library/filesys/shutil#shutil.rmtree" >}}) 或者调用 [`os.lstat()`]({{< ref "/library/allos/os#os.lstat" >}}) 和 [`os.rmdir()`]({{< ref "/library/allos/os#os.rmdir" >}}) 来移除一个路径及其内容。 与 [`rmdir()`]({{< ref "/library/development/test#test.support.os_helper.rmdir" >}}) 一样，在 Windows 平台上这将使用一个检测文件是否存在的等待循环来包装。

## @test.support.os_helper.**skip_unless_symlink**

​	一个用于运行需要符号链接支持的测试的装饰器。

## @test.support.os_helper.**skip_unless_xattr**

​	一个用于运行需要 xattr 支持的测试的装饰器。

## test.support.os_helper.**temp_cwd**(*name='tempcwd'*, *quiet=False*)

​	一个临时性地创建新目录并改变当前工作目录（CWD）的上下文管理器。

​	临时性地改变当前工作目录之前此上下文管理器会在当前目录下创建一个名为 *name* 的临时目录。 如果 *name* 为 `None`，则会使用 [`tempfile.mkdtemp()`]({{< ref "/library/filesys/tempfile#tempfile.mkdtemp" >}}) 创建临时目录。

​	如果 *quiet* 为 `False` 并且无法创建或修改 CWD，则会引发一个错误。 在其他情况下，只会引发一个警告并使用原始 CWD。

## test.support.os_helper.**temp_dir**(*path=None*, *quiet=False*)

​	一个在 *path* 上创建临时目录并输出该目录的上下文管理器。

​	如果 *path* 为 `None`，则会使用 [`tempfile.mkdtemp()`]({{< ref "/library/filesys/tempfile#tempfile.mkdtemp" >}}) 来创建临时目录。 如果 *quiet* 为 `False`，则该上下文管理器在发生错误时会引发一个异常。 在其他情况下，如果 *path* 已被指定并且无法创建，则只会发出一个警告。

## test.support.os_helper.**temp_umask**(*umask*)

​	一个临时性地设置进程掩码的上下文管理器。

## test.support.os_helper.**unlink**(*filename*)

​	在 *filename* 上调用 [`os.unlink()`]({{< ref "/library/allos/os#os.unlink" >}})。 与 [`rmdir()`]({{< ref "/library/development/test#test.support.os_helper.rmdir" >}}) 一样，在 Windows 平台上这将使用一个检测文本是否存在的等待循环来包装。



# [`test.support.import_helper`]({{< ref "/library/development/test#module-test.support.import_helper" >}}) --- 用于导入测试的工具

[`test.support.import_helper`]({{< ref "/library/development/test#module-test.support.import_helper" >}}) 模块提供了对导入测试的支持。

> Added in version 3.10.
>

## test.support.import_helper.**forget**(*module_name*)

​	从 `sys.modules` 移除名为 *module_name* 的模块并删除该模块的已编译字节码文件。

## test.support.import_helper.**import_fresh_module**(*name*, *fresh=()*, *blocked=()*, *deprecated=False*)

​	此函数会在执行导入之前通过从 `sys.modules` 移除指定模块来导入并返回指定 Python 模块的新副本。 请注意这不同于 `reload()`，原来的模块不会受到此操作的影响。

*fresh* 是包含在执行导入之前还要从 `sys.modules` 缓存中移除的附加模块名称的可迭代对象。

*blocked* 是包含模块名称的可迭代对象，导入期间在模块缓存中它会被替换为 `None` 以确保尝试导入将引发 [`ImportError`]({{< ref "/library/exceptions#ImportError" >}})。

​	指定名称的模块以及任何在 *fresh* 和 *blocked* 形参中指明的模块会在开始导入之前被保存并在全新导入完成时被重新插入到 `sys.modules` 中。

​	如果 *deprecated* 为 `True` 则在此导入操作期间模块和包的弃用消息会被屏蔽。

​	如果指定名称的模块无法被导入则此函数将引发 [`ImportError`]({{< ref "/library/exceptions#ImportError" >}})。

​	用法示例:

```
# 获取 warnings 模块的副本用于测试而会不影响
# 测试套件的其他部分所使用的版本。 一个副本
# 使用 C 实现，另一个被强制使用纯 Python 的
# 回退实现
py_warnings = import_fresh_module('warnings', blocked=['_warnings'])
c_warnings = import_fresh_module('warnings', fresh=['_warnings'])
```

> Added in version 3.1.
>

## test.support.import_helper.**import_module**(*name*, *deprecated=False*, ***, *required_on=()*)

​	此函数会导入并返回指定名称的模块。 不同于正常的导入，如果模块无法被导入则此函数将引发 [`unittest.SkipTest`]({{< ref "/library/development/unittest#unittest.SkipTest" >}})。

​	如果 *deprecated* 为 `True` 则在此导入操作期间模块和包的弃用消息会被屏蔽。 如果某个模块在特定平台上是必需的而在其他平台上是可选的，请为包含平台前缀的可迭代对象设置 *required_on*，此对象将与 [`sys.platform`]({{< ref "/library/python/sys#sys.platform" >}}) 进行比对。

> Added in version 3.1.
>

## test.support.import_helper.**modules_setup**()

​	返回 [`sys.modules`]({{< ref "/library/python/sys#sys.modules" >}}) 的副本。

## test.support.import_helper.**modules_cleanup**(*oldmodules*)

​	移除 *oldmodules* 和 `encodings` 以外的模块以保留内部缓冲区。

## test.support.import_helper.**unload**(*name*)

​	从 `sys.modules` 中删除 *name*。

## test.support.import_helper.**make_legacy_pyc**(*source*)

​	将 [**PEP 3147**](https://peps.python.org/pep-3147/)/[**PEP 488**](https://peps.python.org/pep-0488/) pyc 文件移至旧版 pyc 位置并返回该旧版 pyc 文件的文件系统路径。 *source* 的值是源文件的文件系统路径。 它不必真实存在，但是 PEP 3147/488 pyc 文件必须存在。

## *class* test.support.import_helper.**CleanImport**(**module_names*)

​	强制导入以返回一个新的模块引用的上下文管理器。 这适用于测试模块层级的行为，例如在导入时发出 [`DeprecationWarning`]({{< ref "/library/exceptions#DeprecationWarning" >}})。 示例用法:

```
with CleanImport('foo'):
    importlib.import_module('foo')  # 新引用
```

## *class* test.support.import_helper.**DirsOnSysPath**(**paths*)

​	一个临时性地向 [`sys.path`]({{< ref "/library/python/sys#sys.path" >}}) 添加目录的上下文管理器。

​	这将创建 [`sys.path`]({{< ref "/library/python/sys#sys.path" >}}) 的一个副本，添加作为位置参数传入的任何目录，然后在上下文结束时将 [`sys.path`]({{< ref "/library/python/sys#sys.path" >}}) 还原到副本的设置。

​	请注意该上下文管理器代码块中 *所有* 对 [`sys.path`]({{< ref "/library/python/sys#sys.path" >}}) 的修改，包括对象的替换，都将在代码块结束时被还原。



# [`test.support.warnings_helper`]({{< ref "/library/development/test#module-test.support.warnings_helper" >}}) --- 用于警告测试的工具

[`test.support.warnings_helper`]({{< ref "/library/development/test#module-test.support.warnings_helper" >}}) 模块提供了对警告测试的支持。

> Added in version 3.10.
>

## test.support.warnings_helper.**ignore_warnings**(***, *category*)

​	抑制作为 *category* 实例的警告，它必须为 [`Warning`]({{< ref "/library/exceptions#Warning" >}}) 或其子类。 大致等价于 [`warnings.catch_warnings()`]({{< ref "/library/python/warnings#warnings.catch_warnings" >}}) 设置 [`warnings.simplefilter('ignore', category=category)`]({{< ref "/library/python/warnings#warnings.simplefilter" >}})。 例如:

```
@warning_helper.ignore_warnings(category=DeprecationWarning)
def test_suppress_warning():
    # 做些什么
```

> Added in version 3.8.
>

## test.support.warnings_helper.**check_no_resource_warning**(*testcase*)

​	检测是否没有任何 [`ResourceWarning`]({{< ref "/library/exceptions#ResourceWarning" >}}) 被引发的上下文管理器。 你必须在该上下文管理器结束之前移除可能发出 [`ResourceWarning`]({{< ref "/library/exceptions#ResourceWarning" >}}) 的对象。

## test.support.warnings_helper.**check_syntax_warning**(*testcase*, *statement*, *errtext=''*, ***, *lineno=1*, *offset=None*)

​	用于通过尝试编译 *statement* 来测试 *statement* 中的语法警告。 还会测试 [`SyntaxWarning`]({{< ref "/library/exceptions#SyntaxWarning" >}}) 是否只发出了一次，以及它在转成错误时是否将被转换为 [`SyntaxError`]({{< ref "/library/exceptions#SyntaxError" >}})。 *testcase* 是用于测试的 [`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 实例。 *errtext* 是应当匹配所发出的 [`SyntaxWarning`]({{< ref "/library/exceptions#SyntaxWarning" >}}) 以及所引发的 [`SyntaxError`]({{< ref "/library/exceptions#SyntaxError" >}}) 的字符串表示形式的正则表达式。 如果 *lineno* 不为 `None`，则与警告和异常所在的行进行比较。 如果 *offset* 不为 `None`，则与异常的偏移量进行比较。

> Added in version 3.8.
>

## test.support.warnings_helper.**check_warnings**(**filters*, *quiet=True*)

​	一个用于 [`warnings.catch_warnings()`]({{< ref "/library/python/warnings#warnings.catch_warnings" >}}) 以更容易地测试特定警告是否被正确引发的便捷包装器。 它大致等价于调用 `warnings.catch_warnings(record=True)` 并将 [`warnings.simplefilter()`]({{< ref "/library/python/warnings#warnings.simplefilter" >}}) 设为 `always` 并附带自动验证已记录结果的选项。

`check_warnings` 接受 `("message regexp", WarningCategory)` 形式的 2 元组作为位置参数。 如果提供了一个或多个 *filters*，或者如果可选的关键字参数 *quiet* 为 `False`，则它会检查确认警告是符合预期的：每个已指定的过滤器必须匹配至少一个被包围的代码或测试失败时引发的警告，并且如果有任何未能匹配已指定过滤器的警告被引发则测试将失败。 要禁用这些检查中的第一项，请将 *quiet* 设为 `True`。

​	如果未指定任何参数，则默认为:

```
check_warnings(("", Warning), quiet=True)
```

​	在此情况下所有警告都会被捕获而不会引发任何错误。

​	在进入该上下文管理器时，将返回一个 `WarningRecorder` 实例。 来自 [`catch_warnings()`]({{< ref "/library/python/warnings#warnings.catch_warnings" >}}) 的下层警告列表可通过该记录器对象的 [`warnings`]({{< ref "/library/python/warnings#module-warnings" >}}) 属性来访问。 作为一个便捷方式，该对象中代表最近的警告的属性也可通过该记录器对象来直接访问（参见以下示例）。 如果未引发任何警告，则在其他情况下预期代表一个警告的任何对象属性都将返回 `None`。

​	该记录器对象还有一个 `reset()` 方法，该方法会清空警告列表。

​	该上下文管理器被设计为像这样来使用:

```
with check_warnings(("assertion is always true", SyntaxWarning),
                    ("", UserWarning)):
    exec('assert(False, "Hey!")')
    warnings.warn(UserWarning("Hide me!"))
```

​	在此情况下如果两个警告都未被引发，或是引发了其他的警告，则 [`check_warnings()`]({{< ref "/library/development/test#test.support.warnings_helper.check_warnings" >}}) 将会引发一个错误。

​	当一个测试需要更深入地查看这些警告，而不是仅仅检查它们是否发生时，可以使用这样的代码:

```
with check_warnings(quiet=True) as w:
    warnings.warn("foo")
    assert str(w.args[0]) == "foo"
    warnings.warn("bar")
    assert str(w.args[0]) == "bar"
    assert str(w.warnings[0].args[0]) == "foo"
    assert str(w.warnings[1].args[0]) == "bar"
    w.reset()
    assert len(w.warnings) == 0
```

​	在这里所有的警告都将被捕获，而测试代码会直接测试被捕获的警告。

> 在 3.2 版本发生变更: 新增可选参数 *filters* 和 *quiet*。

## *class* test.support.warnings_helper.**WarningsRecorder**

​	用于为单元测试记录警告的类。 请参阅以上 [`check_warnings()`]({{< ref "/library/development/test#test.support.warnings_helper.check_warnings" >}}) 的文档来了解详情。
