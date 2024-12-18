+++
title = "unittest --- 单元测试框架"
date = 2024-11-15T21:03:03+08:00
weight = 40
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/unittest.html](https://docs.python.org/zh-cn/3.13/library/unittest.html)
>
> 收录该文档的时间：`2024-11-15T21:03:03+08:00`

# `unittest` --- 单元测试框架

**源代码：** [Lib/unittest/__init__.py](https://github.com/python/cpython/tree/3.13/Lib/unittest/__init__.py)

------

​	（如果你已经对测试的概念比较熟悉了，你可能想直接跳转到这一部分 [断言方法]({{< ref "/library/development/unittest#assert-methods" >}})。）

[`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 单元测试框架是受到 JUnit 的启发，与其他语言中的主流单元测试框架有着相似的风格。其支持测试自动化，配置共享和关机代码测试。支持将测试样例聚合到测试集中，并将测试与报告框架独立。

​	为了实现这些，[`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 通过面向对象的方式支持了一些重要的概念。

## 测试脚手架

*test fixture* 表示为了开展一项或多项测试所需要进行的准备工作，以及所有相关的清理操作。举个例子，这可能包含创建临时或代理的数据库、目录，再或者启动一个服务器进程。

## 测试用例

​	一个测试用例是一个独立的测试单元。它检查输入特定的数据时的响应。 [`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 提供一个基类： [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) ，用于新建测试用例。

## 测试套件

*test suite* 是一系列的测试用例，或测试套件，或两者皆有。它用于归档需要一起执行的测试。

## 测试运行器（test runner）

*test runner* 是一个用于执行和输出测试结果的组件。这个运行器可能使用图形接口、文本接口，或返回一个特定的值表示运行测试的结果。

​参见
## [`doctest`]({{< ref "/library/development/doctest#module-doctest" >}}) --- 文档测试模块

​	另一个风格完全不同的测试模块。

## [简单 Smalltalk 测试：便用模式](https://web.archive.org/web/20150315073817/http://www.xprogramming.com/testfram.htm)

​	Kent Beck 有关使用 [`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 所共享的模式的测试框架的原创论文。

## [pytest](https://docs.pytest.org/)

​	第三方单元测试框架，提供轻量化的语法来编写测试，例如：`assert func(10) == 42`。

## [Python 测试工具分类](https://wiki.python.org/moin/PythonTestingToolsTaxonomy)

​	一个 Python 测试工具的详细列表，包含测试框架和模拟对象库。

## Python 中的测试 邮件列表

​	一个讨论 Python 中的测试和测试工具的特别兴趣小组。

​	Python 源代码分发包中的脚本 `Tools/unittestgui/unittestgui.py` 是一个用于发现和执行测试的 GUI 工具。 这主要是为了方便单元测试的新手使用。 在生产环境中推荐通过持续集成系统如 [Buildbot](https://buildbot.net/), [Jenkins](https://www.jenkins.io/), [GitHub Actions](https://github.com/features/actions) 或 [AppVeyor](https://www.appveyor.com/) 来驱动测试过程。



## 基本实例

[`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 模块提供了一系列创建和运行测试的工具。这一段落演示了这些工具的一小部分，但也足以满足大部分用户的需求。

​	这是一段简短的代码，来测试三种字符串方法:

```
import unittest

class TestStringMethods(unittest.TestCase):

    def test_upper(self):
        self.assertEqual('foo'.upper(), 'FOO')

    def test_isupper(self):
        self.assertTrue('FOO'.isupper())
        self.assertFalse('Foo'.isupper())

    def test_split(self):
        s = 'hello world'
        self.assertEqual(s.split(), ['hello', 'world'])
        # check that s.split fails when the separator is not a string
        with self.assertRaises(TypeError):
            s.split(2)

if __name__ == '__main__':
    unittest.main()
```

​	继承 [`unittest.TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 就创建了一个测试样例。上述三个独立的测试是三个类的方法，这些方法的命名都以 `test` 开头。 这个命名约定告诉测试运行者类的哪些方法表示测试。

​	每个测试的关键是：调用 [`assertEqual()`]({{< ref "/library/development/unittest#unittest.TestCase.assertEqual" >}}) 来检查预期的输出； 调用 [`assertTrue()`]({{< ref "/library/development/unittest#unittest.TestCase.assertTrue" >}}) 或 [`assertFalse()`]({{< ref "/library/development/unittest#unittest.TestCase.assertFalse" >}}) 来验证一个条件；调用 [`assertRaises()`]({{< ref "/library/development/unittest#unittest.TestCase.assertRaises" >}}) 来验证抛出了一个特定的异常。使用这些方法而不是 [`assert`]({{< ref "/reference/simple_stmts#assert" >}}) 语句是为了让测试运行者能聚合所有的测试结果并产生结果报告。

​	通过 [`setUp()`]({{< ref "/library/development/unittest#unittest.TestCase.setUp" >}}) 和 [`tearDown()`]({{< ref "/library/development/unittest#unittest.TestCase.tearDown" >}}) 方法，可以设置测试开始前与完成后需要执行的指令。 在 [组织你的测试代码]({{< ref "/library/development/unittest#organizing-tests" >}}) 中，对此有更为详细的描述。

​	最后的代码块中，演示了运行测试的一个简单的方法。 [`unittest.main()`]({{< ref "/library/development/unittest#unittest.main" >}}) 提供了一个测试脚本的命令行接口。当在命令行运行该测试脚本，上文的脚本生成如以下格式的输出:

```
...
----------------------------------------------------------------------
Ran 3 tests in 0.000s

OK
```

​	在调用测试脚本时添加 `-v` 参数使 [`unittest.main()`]({{< ref "/library/development/unittest#unittest.main" >}}) 显示更为详细的信息，生成如以下形式的输出:

```
test_isupper (__main__.TestStringMethods.test_isupper) ... ok
test_split (__main__.TestStringMethods.test_split) ... ok
test_upper (__main__.TestStringMethods.test_upper) ... ok

----------------------------------------------------------------------
Ran 3 tests in 0.001s

OK
```

​	以上例子演示了 [`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 中最常用的、足够满足许多日常测试需求的特性。文档的剩余部分详述该框架的完整特性。

> 在 3.11 版本发生变更: 从测试方法返回一个值（而非返回默认的 `None` 值）现在已被弃用。



## 命令行接口

​	unittest 模块可以通过命令行运行模块、类和独立测试方法的测试:

```
python -m unittest test_module1 test_module2
python -m unittest test_module.TestClass
python -m unittest test_module.TestClass.test_method
```

​	你可以传入模块名、类或方法名或他们的任意组合。

​	同样的，测试模块可以通过文件路径指定:

```
python -m unittest tests/test_something.py
```

​	这样就可以使用 shell 的文件名补全指定测试模块。所指定的文件仍需要可以被作为模块导入。路径通过去除 '.py' 、把分隔符转换为 '.' 转换为模块名。若你需要执行不能被作为模块导入的测试文件，你需要直接执行该测试文件。

​	在运行测试时，你可以通过添加 -v 参数获取更详细（更多的冗余）的信息。

```
python -m unittest -v test_module
```

​	当运行时不包含参数，开始 [探索性测试]({{< ref "/library/development/unittest#unittest-test-discovery" >}})

```
python -m unittest
```

​	用于获取命令行选项列表：

```
python -m unittest -h
```

> 在 3.2 版本发生变更: 在早期版本中，只支持运行独立的测试方法，而不支持模块和类。

### 命令行选项

**unittest** supports these command-line options:

## **-b**, `--buffer`

​	在测试运行时，标准输出流与标准错误流会被放入缓冲区。成功的测试的运行时输出会被丢弃；测试不通过时，测试运行中的输出会正常显示，错误会被加入到测试失败信息。

## **-c**, `--catch`

​	当测试正在运行时， Control-C 会等待当前测试完成，并在完成后报告已执行的测试的结果。当再次按下 Control-C 时，引发平常的 [`KeyboardInterrupt`]({{< ref "/library/exceptions#KeyboardInterrupt" >}}) 异常。

​	See [Signal Handling]({{< ref "/library/development/unittest#signal-handling" >}}) for the functions that provide this functionality.

## **-f**, `--failfast`

​	当出现第一个错误或者失败时，停止运行测试。

## **-k**

​	只运行匹配模式或子字符串的测试方法和类。 此选项可以被多次使用，在此情况下将会包括匹配任何给定模式的所有测试用例。

​	包含通配符（*）的模式使用 [`fnmatch.fnmatchcase()`]({{< ref "/library/filesys/fnmatch#fnmatch.fnmatchcase" >}}) 对测试名称进行匹配。另外，该匹配是大小写敏感的。

​	模式对测试加载器导入的测试方法全名进行匹配。

​	例如，`-k foo` 可以匹配到 `foo_tests.SomeTest.test_something` 和 `bar_tests.SomeTest.test_foo` ，但是不能匹配到 `bar_tests.FooTest.test_something` 。

## `--locals`

​	在回溯中显示局部变量。

## `--durations` N

​	显示 N 个最慢的测试用例 (N=0 表示全部)。

> Added in version 3.2:
> 添加命令行选项 `-b`, `-c` 和 `-f` 。

> Added in version 3.5:
> 命令行选项 `--locals` 。

> Added in version 3.7:
> 命令行选项 `-k` 。

> Added in version 3.12:
> 命令行选项 `--durations`。

​	命令行亦可用于探索性测试，以运行一个项目的所有测试或其子集。



## 探索性测试

> Added in version 3.2.
>

​	unittest 支持简单的测试发现。 为了与测试发现兼容，所有测试文件都必须是可从项目的最高层级目录导入的 [模块]({{< ref "/tutorial/modules#tut-modules" >}}) 或 [包]({{< ref "/tutorial/modules#tut-packages" >}}) (这意味着它们的文件名必须是有效的 [标识符]({{< ref "/reference/lexical_analysis#identifiers" >}}))。

​	探索性测试在 [`TestLoader.discover()`]({{< ref "/library/development/unittest#unittest.TestLoader.discover" >}}) 中实现，但也可以通过命令行使用。它在命令行中的基本用法如下：

```
cd project_directory
python -m unittest discover
```

​备注
 

​	方便起见， `python -m unittest` 与 `python -m unittest discover` 等价。如果你需要向探索性测试传入参数，必须显式地使用 `discover` 子命令。

`discover` 有以下选项：

## **-v**, `--verbose`

​	更详细地输出结果。

## **-s**, `--start-directory` directory

​	开始进行搜索的目录(默认值为当前目录 `.` )。

## **-p**, `--pattern` pattern

​	用于匹配测试文件的模式（默认为 `test*.py` ）。

## **-t**, `--top-level-directory` directory

​	指定项目的最上层目录（通常为开始时所在目录）。

[`-s`]({{< ref "/library/development/unittest#cmdoption-unittest-discover-s" >}}) ，[`-p`]({{< ref "/library/development/unittest#cmdoption-unittest-discover-p" >}}) 和 [`-t`]({{< ref "/library/development/unittest#cmdoption-unittest-discover-t" >}}) 选项可以按顺序作为位置参数传入。以下两条命令是等价的：

```
python -m unittest discover -s project_directory -p "*_test.py"
python -m unittest discover project_directory "*_test.py"
```

​	正如可以传入路径那样，传入一个包名作为起始目录也是可行的，如 `myproject.subpackage.test` 。你提供的包名会被导入，它在文件系统中的位置会被作为起始目录。

​	小心

 

​	探索性测试通过导入测试对测试进行加载。在找到所有你指定的开始目录下的所有测试文件后，它把路径转换为包名并进行导入。如 `foo/bar/baz.py` 会被导入为 `foo.bar.baz` 。

​	如果你有一个全局安装的包，并尝试对这个包的副本进行探索性测试，可能会从错误的地方开始导入。如果出现这种情况，测试会输出警告并退出。

​	如果你使用包名而不是路径作为开始目录，搜索时会假定它导入的是你想要的目录，所以你不会收到警告。

​	测试模块和包可以通过 [load_tests protocol]({{< ref "/library/development/unittest#id1" >}}) 自定义测试的加载和搜索。

> 在 3.4 版本发生变更: 测试发现支持初始目录下的 [命名空间包]({{< ref "/glossary/idx#term-namespace-package" >}})。注意你也需要指定顶层目录（例如：`python -m unittest discover -s root/namespace -t root`）。

> 在 3.11 版本发生变更: 在 Python 3.11 中 [`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 丢弃了 [命名空间包]({{< ref "/glossary/idx#term-namespace-package" >}}) 支持。 它自 Python 3.7 就已不可用。 包含测试的起始目录和子目录都必须是具有 `__init__.py` 文件的常规包。

​	包含起始目录的目录仍然可以是命名空间包。 在此情况下，你需要以带点号的包名称来显式地指明起始目录和目标目录。 例如:

```
# proj/  <-- current directory
#   namespace/
#     mypkg/
#       __init__.py
#       test_mypkg.py

python -m unittest discover -s namespace.mypkg -t .
```



## 组织你的测试代码

​	单元测试的构建单位是 *test cases* ：独立的、包含执行条件与正确性检查的方案。在 [`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 中，测试用例表示为 [`unittest.TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 的实例。通过编写 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 的子类或使用 [`FunctionTestCase`]({{< ref "/library/development/unittest#unittest.FunctionTestCase" >}}) 编写你自己的测试用例。

​	一个 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 实例的测试代码必须是完全自含的，因此它可以独立运行，或与其它任意组合任意数量的测试用例一起运行。

[`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 的最简单的子类需要实现一个测试方法（例如一个命名以 `test` 开头的方法）以执行特定的测试代码：

```
import unittest

class DefaultWidgetSizeTestCase(unittest.TestCase):
    def test_default_widget_size(self):
        widget = Widget('The widget')
        self.assertEqual(widget.size(), (50, 50))
```

​	请注意为了进行测试，我们使用了 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 基类提供的某个 [assert* 方法]({{< ref "/library/development/unittest#assert-methods" >}})。 如果测试不通过，将会引发一个异常并附带解释性的消息，并且 [`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 会将这个测试用例标记为 *failure*。 任何其它异常都将被视为 *errors*。

​	可能同时存在多个前置操作相同的测试，我们可以把测试的前置操作从测试代码中拆解出来，并实现测试前置方法 [`setUp()`]({{< ref "/library/development/unittest#unittest.TestCase.setUp" >}}) 。在运行测试时，测试框架会自动地为每个单独测试调用前置方法。

```
import unittest

class WidgetTestCase(unittest.TestCase):
    def setUp(self):
        self.widget = Widget('The widget')

    def test_default_widget_size(self):
        self.assertEqual(self.widget.size(), (50,50),
                         'incorrect default size')

    def test_widget_resize(self):
        self.widget.resize(100,150)
        self.assertEqual(self.widget.size(), (100,150),
                         'wrong size after resize')
```

​备注
 

​	多个测试运行的顺序由内置字符串排序方法对测试名进行排序的结果决定。

​	在测试运行时，若 [`setUp()`]({{< ref "/library/development/unittest#unittest.TestCase.setUp" >}}) 方法引发异常，测试框架会认为测试发生了错误，因此测试方法不会被运行。

​	相似的，我们提供了一个 [`tearDown()`]({{< ref "/library/development/unittest#unittest.TestCase.tearDown" >}}) 方法在测试方法运行后进行清理工作。

```
import unittest

class WidgetTestCase(unittest.TestCase):
    def setUp(self):
        self.widget = Widget('The widget')

    def tearDown(self):
        self.widget.dispose()
```

​	若 [`setUp()`]({{< ref "/library/development/unittest#unittest.TestCase.setUp" >}}) 成功运行，无论测试方法是否成功，都会运行 [`tearDown()`]({{< ref "/library/development/unittest#unittest.TestCase.tearDown" >}}) 。

​	这样的一个测试代码运行的环境被称为 *test fixture* 。一个新的 TestCase 实例作为一个测试脚手架，用于运行各个独立的测试方法。在运行每个测试时，[`setUp()`]({{< ref "/library/development/unittest#unittest.TestCase.setUp" >}}) 、[`tearDown()`]({{< ref "/library/development/unittest#unittest.TestCase.tearDown" >}}) 和 `__init__()` 会被调用一次。

​	推荐你根据用例所测试的功能将测试用 TestCase 分组。[`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 为此提供了 *test suite*：[`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 的 [`TestSuite`]({{< ref "/library/development/unittest#unittest.TestSuite" >}}) 类是一个代表。通常情况下，调用 [`unittest.main()`]({{< ref "/library/development/unittest#unittest.main" >}}) 就能正确地找到并执行这个模块下所有用 TestCase 分组的测试。

​	然而，如果你需要自定义你的测试套件的话，你可以参考以下方法组织你的测试：

```
def suite():
    suite = unittest.TestSuite()
    suite.addTest(WidgetTestCase('test_default_widget_size'))
    suite.addTest(WidgetTestCase('test_widget_resize'))
    return suite

if __name__ == '__main__':
    runner = unittest.TextTestRunner()
    runner.run(suite())
```

​	你可以把测试用例和测试套件放在与被测试代码相同的模块中（比如 `widget.py`），但将测试代码放在单独的模块中（比如 `test_widget.py`）有几个优势。

- 测试模块可以从命令行被独立调用。
- 更容易在分发的代码中剥离测试代码。
- 降低没有好理由的情况下修改测试代码以通过测试的诱惑。
- 测试代码应比被测试代码更少地被修改。
- 被测试代码可以更容易地被重构。
- 对用 C 语言写成的模块无论如何都得单独写成一个模块，为什么不保持一致呢？
- 如果测试策略发生了改变，没有必要修改源代码。



## 复用已有的测试代码

​	一些用户希望直接使用 [`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 运行已有的测试代码，而不需要把已有的每个测试函数转化为一个 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 的子类。

​	因此， [`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 提供 [`FunctionTestCase`]({{< ref "/library/development/unittest#unittest.FunctionTestCase" >}}) 类。这个 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 的子类可用于打包已有的测试函数，并支持设置前置与后置函数。

​	假定有一个测试函数：

```
def testSomething():
    something = makeSomething()
    assert something.name is not None
    # ...
```

​	可以创建等价的测试用例如下，其中前置和后置方法是可选的。

```
testcase = unittest.FunctionTestCase(testSomething,
                                     setUp=makeSomethingDB,
                                     tearDown=deleteSomethingDB)
```

​备注
 

​	用 [`FunctionTestCase`]({{< ref "/library/development/unittest#unittest.FunctionTestCase" >}}) 可以快速将现有的测试转换成基于 [`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 的测试，但不推荐你这样做。花点时间继承 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 会让以后重构测试无比轻松。

​	在某些情况下，现有的测试可能是用 [`doctest`]({{< ref "/library/development/doctest#module-doctest" >}}) 模块编写的。 如果是这样， [`doctest`]({{< ref "/library/development/doctest#module-doctest" >}}) 提供了一个 `DocTestSuite` 类，可以从现有基于 [`doctest`]({{< ref "/library/development/doctest#module-doctest" >}}) 的测试中自动构建 [`unittest.TestSuite`]({{< ref "/library/development/unittest#unittest.TestSuite" >}}) 用例。



## 跳过测试与预计的失败

> Added in version 3.1.
>

​	Unittest 支持跳过单个或整组的测试用例。它还支持把测试标注成“预期失败”的测试。这些坏测试会失败，但不会算进 [`TestResult`]({{< ref "/library/development/unittest#unittest.TestResult" >}}) 的失败里。

​	要跳过测试只需使用 [`skip()`]({{< ref "/library/development/unittest#unittest.skip" >}}) [decorator]({{< ref "/glossary/idx#term-decorator" >}}) 或其附带条件的版本，在 [`setUp()`]({{< ref "/library/development/unittest#unittest.TestCase.setUp" >}}) 内部使用 [`TestCase.skipTest()`]({{< ref "/library/development/unittest#unittest.TestCase.skipTest" >}})，或是直接引发 [`SkipTest`]({{< ref "/library/development/unittest#unittest.SkipTest" >}})。

​	跳过测试的基本用法如下:

```
class MyTestCase(unittest.TestCase):

    @unittest.skip("demonstrating skipping")
    def test_nothing(self):
        self.fail("shouldn't happen")

    @unittest.skipIf(mylib.__version__ < (1, 3),
                     "not supported in this library version")
    def test_format(self):
        # Tests that work for only a certain version of the library.
        pass

    @unittest.skipUnless(sys.platform.startswith("win"), "requires Windows")
    def test_windows_support(self):
        # windows specific testing code
        pass

    def test_maybe_skipped(self):
        if not external_resource_available():
            self.skipTest("external resource not available")
        # test code that depends on the external resource
        pass
```

​	在啰嗦模式下运行以上测试例子时，程序输出如下：

```
test_format (__main__.MyTestCase.test_format) ... skipped 'not supported in this library version'
test_nothing (__main__.MyTestCase.test_nothing) ... skipped 'demonstrating skipping'
test_maybe_skipped (__main__.MyTestCase.test_maybe_skipped) ... skipped 'external resource not available'
test_windows_support (__main__.MyTestCase.test_windows_support) ... skipped 'requires Windows'

----------------------------------------------------------------------
Ran 4 tests in 0.005s

OK (skipped=4)
```

​	跳过测试类的写法跟跳过测试方法的写法相似:

```
@unittest.skip("showing class skipping")
class MySkippedTestCase(unittest.TestCase):
    def test_not_run(self):
        pass
```

[`TestCase.setUp()`]({{< ref "/library/development/unittest#unittest.TestCase.setUp" >}}) 也可以跳过测试。可以用于所需资源不可用的情况下跳过接下来的测试。

​	使用 [`expectedFailure()`]({{< ref "/library/development/unittest#unittest.expectedFailure" >}}) 装饰器表明这个测试预计失败。:

```
class ExpectedFailureTestCase(unittest.TestCase):
    @unittest.expectedFailure
    def test_fail(self):
        self.assertEqual(1, 0, "broken")
```

​	你可以很容易地编写在测试时调用 [`skip()`]({{< ref "/library/development/unittest#unittest.skip" >}}) 的装饰器作为自定义的跳过测试装饰器。 下面这个装饰器会跳过测试，除非所传入的对象具有特定的属性:

```
def skipUnlessHasattr(obj, attr):
    if hasattr(obj, attr):
        return lambda func: func
    return unittest.skip("{!r} doesn't have {!r}".format(obj, attr))
```

​	以下的装饰器和异常实现了跳过测试和预期失败两种功能：

## @unittest.**skip**(*reason*)

​	跳过被此装饰器装饰的测试。 *reason* 为测试被跳过的原因。

## @unittest.**skipIf**(*condition*, *reason*)

​	当 *condition* 为真时，跳过被装饰的测试。

## @unittest.**skipUnless**(*condition*, *reason*)

​	跳过被装饰的测试，除非 *condition* 为真。

## @unittest.**expectedFailure**

​	将测试标记为预期的失败或错误。 如果测试失败或在测试函数自身（而非在某个 *test fixture* 方法）中出现错误则将认为是测试成功。 如果测试通过，则将认为是测试失败。

## *exception* unittest.**SkipTest**(*reason*)

​	引发此异常以跳过一个测试。

​	通常来说，你可以使用 [`TestCase.skipTest()`]({{< ref "/library/development/unittest#unittest.TestCase.skipTest" >}}) 或其中一个跳过测试的装饰器实现跳过测试的功能，而不是直接引发此异常。

​	被跳过的测试的 [`setUp()`]({{< ref "/library/development/unittest#unittest.TestCase.setUp" >}}) 和 [`tearDown()`]({{< ref "/library/development/unittest#unittest.TestCase.tearDown" >}}) 不会被运行。被跳过的类的 [`setUpClass()`]({{< ref "/library/development/unittest#unittest.TestCase.setUpClass" >}}) 和 [`tearDownClass()`]({{< ref "/library/development/unittest#unittest.TestCase.tearDownClass" >}}) 不会被运行。被跳过的模组的 `setUpModule()` 和 `tearDownModule()` 不会被运行。



## 使用子测试区分测试迭代

> Added in version 3.4.
>

​	当你的几个测试之间的差异非常小，例如只有某些形参不同时，unittest 允许你使用 [`subTest()`]({{< ref "/library/development/unittest#unittest.TestCase.subTest" >}}) 上下文管理器在一个测试方法体的内部区分它们。

​	例如，以下测试:

```
class NumbersTest(unittest.TestCase):

    def test_even(self):
        """
        Test that numbers between 0 and 5 are all even.
        """
        for i in range(0, 6):
            with self.subTest(i=i):
                self.assertEqual(i % 2, 0)
```

​	可以得到以下输出:

```
======================================================================
FAIL: test_even (__main__.NumbersTest.test_even) (i=1)
Test that numbers between 0 and 5 are all even.
----------------------------------------------------------------------
Traceback (most recent call last):
  File "subtests.py", line 11, in test_even
    self.assertEqual(i % 2, 0)
    ^^^^^^^^^^^^^^^^^^^^^^^^^^
AssertionError: 1 != 0

======================================================================
FAIL: test_even (__main__.NumbersTest.test_even) (i=3)
Test that numbers between 0 and 5 are all even.
----------------------------------------------------------------------
Traceback (most recent call last):
  File "subtests.py", line 11, in test_even
    self.assertEqual(i % 2, 0)
    ^^^^^^^^^^^^^^^^^^^^^^^^^^
AssertionError: 1 != 0

======================================================================
FAIL: test_even (__main__.NumbersTest.test_even) (i=5)
Test that numbers between 0 and 5 are all even.
----------------------------------------------------------------------
Traceback (most recent call last):
  File "subtests.py", line 11, in test_even
    self.assertEqual(i % 2, 0)
    ^^^^^^^^^^^^^^^^^^^^^^^^^^
AssertionError: 1 != 0
```

​	如果不使用子测试，程序遇到第一次错误之后就会停止。而且因为 `i` 的值不显示，错误也更难找。

```
======================================================================
FAIL: test_even (__main__.NumbersTest.test_even)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "subtests.py", line 32, in test_even
    self.assertEqual(i % 2, 0)
AssertionError: 1 != 0
```



## 类与函数

​	本节深入介绍了 [`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 的 API。



### 测试用例

## *class* unittest.**TestCase**(*methodName='runTest'*)

[`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 类的实例代表了 [`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 宇宙中的逻辑测试单元。 该类旨在被当作基类使用，特定的测试将由其实体子类来实现。 该类实现了测试运行器所需的接口以允许它驱动测试，并实现了可被测试代码用来检测和报告各种类型的失败的方法。

​	每个 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 实例将运行一个单位的基础方法：即名为 *methodName* 的方法。 在使用 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 的大多数场景中，你都不需要修改 *methodName* 或重新实现默认的 `runTest()` 方法。

> 在 3.2 版本发生变更: [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 不需要提供 *methodName* 即可成功实例化。 这使得从交互式解释器试验 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 更为容易。

[`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 的实例提供了三组方法：一组用来运行测试，另一组被测试实现用来检查条件和报告失败，还有一些查询方法用来收集有关测试本身的信息。

​	第一组（用于运行测试的）方法是：

## **setUp**()

​	为测试预备而调用的方法。 此方法会在调用测试方法之前被调用；除了 [`AssertionError`]({{< ref "/library/exceptions#AssertionError" >}}) 或 [`SkipTest`]({{< ref "/library/development/unittest#unittest.SkipTest" >}})，此方法所引发的任何异常都将被视为错误而非测试失败。 默认的实现将不做任何事情。

## **tearDown**()

​	在测试方法被调用并记录结果之后立即被调用的方法。 此方法即使在测试方法引发异常时仍会被调用，因此子类中的实现将需要特别注意检查内部状态。 除 [`AssertionError`]({{< ref "/library/exceptions#AssertionError" >}}) 或 [`SkipTest`]({{< ref "/library/development/unittest#unittest.SkipTest" >}}) 外，此方法所引发的任何异常都将被视为额外的错误而非测试失败（因而会增加总计错误报告数）。 此方法将只在 [`setUp()`]({{< ref "/library/development/unittest#unittest.TestCase.setUp" >}}) 成功执行时被调用，无论测试方法的结果如何。 默认的实现将不做任何事情。

## **setUpClass**()

​	在一个单独类中的测试运行之前被调用的类方法。 `setUpClass` 会被作为唯一的参数在类上调用且必须使用 [`classmethod()`]({{< ref "/library/functions#classmethod" >}}) 装饰器:

```
@classmethod
def setUpClass(cls):
    ...
```

​	查看 [Class and Module Fixtures]({{< ref "/library/development/unittest#class-and-module-fixtures" >}}) 获取更详细的说明。

> Added in version 3.2.
>

## **tearDownClass**()

​	在一个单独类的测试完成运行之后被调用的类方法。 `tearDownClass` 会被作为唯一的参数在类上调用且必须使用 [`classmethod()`]({{< ref "/library/functions#classmethod" >}}) 装饰器:

```
@classmethod
def tearDownClass(cls):
    ...
```

​	查看 [Class and Module Fixtures]({{< ref "/library/development/unittest#class-and-module-fixtures" >}}) 获取更详细的说明。

> Added in version 3.2.
>

## **run**(*result=None*)

​	运行测试，将结果收集至作为 *result* 传入的 [`TestResult`]({{< ref "/library/development/unittest#unittest.TestResult" >}})。 如果 *result* 被省略或为 `None`，则会创建一个临时的结果对象（通过调用 [`defaultTestResult()`]({{< ref "/library/development/unittest#unittest.TestCase.defaultTestResult" >}}) 方法）并使用它。 结果对象会被返回给 [`run()`]({{< ref "/library/development/unittest#unittest.TestCase.run" >}}) 的调用方。

​	同样的效果也可通过简单地调用 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 实例来达成。

> 在 3.3 版本发生变更: 之前版本的 `run` 不会返回结果。 也不会对实例执行调用。

## **skipTest**(*reason*)

​	在测试方法或 [`setUp()`]({{< ref "/library/development/unittest#unittest.TestCase.setUp" >}}) 执行期间调用此方法将跳过当前测试。 详情参见 [跳过测试与预计的失败]({{< ref "/library/development/unittest#unittest-skipping" >}})。

> Added in version 3.1.
>

## **subTest**(*msg=None*, ***params*)

​	返回一个上下文管理器以将其中的代码块作为子测试来执行。 可选的 *msg* 和 *params* 是将在子测试失败时显示的任意值，以便让你能清楚地标识它们。

​	一个测试用例可以包含任意数量的子测试声明，并且它们可以任意地嵌套。

​	查看 [使用子测试区分测试迭代]({{< ref "/library/development/unittest#subtests" >}}) 获取更详细的信息。

> Added in version 3.4.
>

## **debug**()

​	运行测试而不收集结果。 这允许测试所引发的异常被传递给调用方，并可被用于支持在调试器中运行测试。

[`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 类提供了一些断言方法用于检查并报告失败。 下表列出了最常用的方法（请查看下文的其他表来了解更多的断言方法）:

| 方法                                                         | 检查对象               | 引入版本 |
| :----------------------------------------------------------- | :--------------------- | :------- |
| [`assertEqual(a, b)`]({{< ref "/library/development/unittest#unittest.TestCase.assertEqual" >}}) | `a == b`               |          |
| [`assertNotEqual(a, b)`]({{< ref "/library/development/unittest#unittest.TestCase.assertNotEqual" >}}) | `a != b`               |          |
| [`assertTrue(x)`]({{< ref "/library/development/unittest#unittest.TestCase.assertTrue" >}}) | `bool(x) is True`      |          |
| [`assertFalse(x)`]({{< ref "/library/development/unittest#unittest.TestCase.assertFalse" >}}) | `bool(x) is False`     |          |
| [`assertIs(a, b)`]({{< ref "/library/development/unittest#unittest.TestCase.assertIs" >}}) | `a is b`               | 3.1      |
| [`assertIsNot(a, b)`]({{< ref "/library/development/unittest#unittest.TestCase.assertIsNot" >}}) | `a is not b`           | 3.1      |
| [`assertIsNone(x)`]({{< ref "/library/development/unittest#unittest.TestCase.assertIsNone" >}}) | `x is None`            | 3.1      |
| [`assertIsNotNone(x)`]({{< ref "/library/development/unittest#unittest.TestCase.assertIsNotNone" >}}) | `x is not None`        | 3.1      |
| [`assertIn(a, b)`]({{< ref "/library/development/unittest#unittest.TestCase.assertIn" >}}) | `a in b`               | 3.1      |
| [`assertNotIn(a, b)`]({{< ref "/library/development/unittest#unittest.TestCase.assertNotIn" >}}) | `a not in b`           | 3.1      |
| [`assertIsInstance(a, b)`]({{< ref "/library/development/unittest#unittest.TestCase.assertIsInstance" >}}) | `isinstance(a, b)`     | 3.2      |
| [`assertNotIsInstance(a, b)`]({{< ref "/library/development/unittest#unittest.TestCase.assertNotIsInstance" >}}) | `not isinstance(a, b)` | 3.2      |

​	这些断言方法都支持 *msg* 参数，如果指定了该参数，它将被用作测试失败时的错误消息 (另请参阅 [`longMessage`]({{< ref "/library/development/unittest#unittest.TestCase.longMessage" >}}))。 请注意将 *msg* 关键字参数传给 [`assertRaises()`]({{< ref "/library/development/unittest#unittest.TestCase.assertRaises" >}}), [`assertRaisesRegex()`]({{< ref "/library/development/unittest#unittest.TestCase.assertRaisesRegex" >}}), [`assertWarns()`]({{< ref "/library/development/unittest#unittest.TestCase.assertWarns" >}}), [`assertWarnsRegex()`]({{< ref "/library/development/unittest#unittest.TestCase.assertWarnsRegex" >}}) 的前提是它们必须被用作上下文管理器。

## **assertEqual**(*first*, *second*, *msg=None*)

​	测试 *first* 和 *second* 是否相等。 如果两个值的比较结果是不相等，则测试将失败。

​	此外，如果 *first* 和 *second* 的类型完全相同且属于 list, tuple, dict, set, frozenset 或 str 或者属于通过 [`addTypeEqualityFunc()`]({{< ref "/library/development/unittest#unittest.TestCase.addTypeEqualityFunc" >}}) 注册子类的类型则将会调用类型专属的相等判断函数以便生成更有用的默认错误消息 (另请参阅 [类型专属方法列表]({{< ref "/library/development/unittest#type-specific-methods" >}}))。

> 在 3.1 版本发生变更: 增加了对类型专属的相等判断函数的自动调用。

> 在 3.2 版本发生变更: 增加了 [`assertMultiLineEqual()`]({{< ref "/library/development/unittest#unittest.TestCase.assertMultiLineEqual" >}}) 作为用于比较字符串的默认类型相等判断函数。

## **assertNotEqual**(*first*, *second*, *msg=None*)

​	测试 *first* 和 *second* 是否不等。 如果两个值的比较结果是相等，则测试将失败。

## **assertTrue**(*expr*, *msg=None*)

## **assertFalse**(*expr*, *msg=None*)

​	测试 *expr* 是否为真值（或假值）。

​	请注意这等价于 `bool(expr) is True` 而不等价于 `expr is True` (后者要使用 `assertIs(expr, True)`)。 当存在更专门的方法时也应避免使用此方法 (例如应使用 `assertEqual(a, b)` 而不是 `assertTrue(a == b)`)，因为它们在测试失败时会提供更有用的错误消息。

## **assertIs**(*first*, *second*, *msg=None*)

## **assertIsNot**(*first*, *second*, *msg=None*)

​	测试 *first* 和 *second* 是 (或不是) 同一个对象。

> Added in version 3.1.
>

## **assertIsNone**(*expr*, *msg=None*)

## **assertIsNotNone**(*expr*, *msg=None*)

​	测试 *expr* 是 (或不是) `None`。

> Added in version 3.1.
>

## **assertIn**(*member*, *container*, *msg=None*)

## **assertNotIn**(*member*, *container*, *msg=None*)

​	测试 *member* 是 (或不是) *container* 的成员。

> Added in version 3.1.
>

## **assertIsInstance**(*obj*, *cls*, *msg=None*)

## **assertNotIsInstance**(*obj*, *cls*, *msg=None*)

​	测试 *obj* 是 (或不是) *cls* (此参数可以为一个类或包含类的元组，即 [`isinstance()`]({{< ref "/library/functions#isinstance" >}}) 所接受的参数) 的实例。 要检测是否为指定类型，请使用 [`assertIs(type(obj), cls)`]({{< ref "/library/development/unittest#unittest.TestCase.assertIs" >}})。

> Added in version 3.2.
>

​	还可以使用下列方法来检查异常、警告和日志消息的产生:

| 方法                                                         | 检查对象                                                     | 引入版本 |
| :----------------------------------------------------------- | :----------------------------------------------------------- | :------- |
| [`assertRaises(exc, fun, *args, **kwds)`]({{< ref "/library/development/unittest#unittest.TestCase.assertRaises" >}}) | `fun(*args, **kwds)` 引发了 *exc*                            |          |
| [`assertRaisesRegex(exc, r, fun, *args, **kwds)`]({{< ref "/library/development/unittest#unittest.TestCase.assertRaisesRegex" >}}) | `fun(*args, **kwds)` 引发了 *exc* 并且消息可与正则表达式 *r* 相匹配 | 3.1      |
| [`assertWarns(warn, fun, *args, **kwds)`]({{< ref "/library/development/unittest#unittest.TestCase.assertWarns" >}}) | `fun(*args, **kwds)` 引发了 *warn*                           | 3.2      |
| [`assertWarnsRegex(warn, r, fun, *args, **kwds)`]({{< ref "/library/development/unittest#unittest.TestCase.assertWarnsRegex" >}}) | `fun(*args, **kwds)` 引发了 *warn* 并且消息可与正则表达式 *r* 相匹配 | 3.2      |
| [`assertLogs(logger, level)`]({{< ref "/library/development/unittest#unittest.TestCase.assertLogs" >}}) | `with` 代码块在 *logger* 上使用了最小的 *level* 级别写入日志 | 3.4      |
| [`assertNoLogs(logger, level)`]({{< ref "/library/development/unittest#unittest.TestCase.assertNoLogs" >}}) | `with` 代码块没有在*logger* 上使用最小的 *level* 级别写入日志 | 3.10     |

## **assertRaises**(*exception*, *callable*, **args*, ***kwds*)

## **assertRaises**(*exception*, ***, *msg=None*)

​	测试当 *callable* 附带任何同时被传给 [`assertRaises()`]({{< ref "/library/development/unittest#unittest.TestCase.assertRaises" >}}) 的位置或关键字参数被调用时是否引发了异常。 如果引发了 *exception* 则测试通过，如果引发了另一个异常则报错，或者如果未引发任何异常则测试失败。 要捕获一组异常中的任何一个，可以将包含多个异常类的元组作为 *exception* 传入。

​	如果只给出了 *exception* 和可能的 *msg* 参数，则返回一个上下文管理器以便被测试的代码可以被写成内联形式而不是被写成函数:

```
with self.assertRaises(SomeException):
    do_something()
```

​	当被作为上下文管理器使用时，[`assertRaises()`]({{< ref "/library/development/unittest#unittest.TestCase.assertRaises" >}}) 接受额外的关键字参数 *msg*。

​	上下文管理器将把捕获的异常对象存入在其 `exception` 属性中。 这适用于需要对所引发异常执行额外检查的场合:

```
with self.assertRaises(SomeException) as cm:
    do_something()

the_exception = cm.exception
self.assertEqual(the_exception.error_code, 3)
```

> 在 3.1 版本发生变更: 添加了将 [`assertRaises()`]({{< ref "/library/development/unittest#unittest.TestCase.assertRaises" >}}) 用作上下文管理器的功能。

> 在 3.2 版本发生变更: 增加了 `exception` 属性。

> 在 3.3 版本发生变更: 增加了 *msg* 关键字参数在作为上下文管理器时使用。

## **assertRaisesRegex**(*exception*, *regex*, *callable*, **args*, ***kwds*)

## **assertRaisesRegex**(*exception*, *regex*, ***, *msg=None*)

​	与 [`assertRaises()`]({{< ref "/library/development/unittest#unittest.TestCase.assertRaises" >}}) 类似但还会测试 *regex* 是否匹配被引发异常的字符串表示形式。 *regex* 可以是一个正则表达式对象或包含正则表达式的字符串以提供给 [`re.search()`]({{< ref "/library/text/re#re.search" >}}) 使用。 例如:

```
self.assertRaisesRegex(ValueError, "invalid literal for.*XYZ'$",
                       int, 'XYZ')
```

​	或者：

```
with self.assertRaisesRegex(ValueError, 'literal'):
   int('XYZ')
```

> Added in version 3.1:
> 以方法名 `assertRaisesRegexp` 添加。

> 在 3.2 版本发生变更: 重命名为 [`assertRaisesRegex()`]({{< ref "/library/development/unittest#unittest.TestCase.assertRaisesRegex" >}})。

> 在 3.3 版本发生变更: 增加了 *msg* 关键字参数在作为上下文管理器时使用。

## **assertWarns**(*warning*, *callable*, **args*, ***kwds*)

## **assertWarns**(*warning*, ***, *msg=None*)

​	测试当 *callable* 附带任何同时被传给 [`assertWarns()`]({{< ref "/library/development/unittest#unittest.TestCase.assertWarns" >}}) 的位置或关键字参数被调用时是否触发了警告。 如果触发了 *warning* 则测试通过，否则测试失败。 引发任何异常则报错。 要捕获一组警告中的任何一个，可将包含多个警告类的元组作为 *warnings* 传入。

​	如果只给出了 *warning* 和可能的 *msg* 参数，则返回一个上下文管理器以便被测试的代码可以被写成内联形式而不是被写成函数:

```
with self.assertWarns(SomeWarning):
    do_something()
```

​	当被作为上下文管理器使用时，[`assertWarns()`]({{< ref "/library/development/unittest#unittest.TestCase.assertWarns" >}}) 接受额外的关键字参数 *msg*。

​	上下文管理器将把捕获的警告对象保存在其 `warning` 属性中，并把触发警告的源代码行保存在 `filename` 和 `lineno` 属性中。 这适用于需要对捕获的警告执行额外检查的场合:

```
with self.assertWarns(SomeWarning) as cm:
    do_something()

self.assertIn('myfile.py', cm.filename)
self.assertEqual(320, cm.lineno)
```

​	无论被调用时警告过滤器是否就位此方法均可工作。

> Added in version 3.2.
>

> 在 3.3 版本发生变更: 增加了 *msg* 关键字参数在作为上下文管理器时使用。

## **assertWarnsRegex**(*warning*, *regex*, *callable*, **args*, ***kwds*)

## **assertWarnsRegex**(*warning*, *regex*, ***, *msg=None*)

​	与 [`assertWarns()`]({{< ref "/library/development/unittest#unittest.TestCase.assertWarns" >}}) 类似但还会测试 *regex* 是否匹配被触发警告的消息文本。 *regex* 可以是一个正则表达式对象或包含正则表达式的字符串以提供给 [`re.search()`]({{< ref "/library/text/re#re.search" >}}) 使用。 例如:

```
self.assertWarnsRegex(DeprecationWarning,
                      r'legacy_function\(\) is deprecated',
                      legacy_function, 'XYZ')
```

​	或者：

```
with self.assertWarnsRegex(RuntimeWarning, 'unsafe frobnicating'):
    frobnicate('/etc/passwd')
```

> Added in version 3.2.
>

> 在 3.3 版本发生变更: 增加了 *msg* 关键字参数在作为上下文管理器时使用。

## **assertLogs**(*logger=None*, *level=None*)

​	一个上下文管理器，它测试在 *logger* 或其子对象上是否至少记录了一条至少为指定 *level* 以上级别的消息。

​	如果给出了 *logger* 则它应为一个 [`logging.Logger`]({{< ref "/library/allos/logging#logging.Logger" >}}) 对象或为一个指定日志记录器名称的 [`str`]({{< ref "/library/stdtypes#str" >}})。 默认为根日志记录器，它将捕获未被非传播型后继日志记录器所拦阻的所有消息。

​	如果给出了 *level*，它应为一个用数字表示的日志记录级别或其字符串等价形式 (例如为 `"ERROR"` 或 [`logging.ERROR`]({{< ref "/library/allos/logging#logging.ERROR" >}}))。 默认为 [`logging.INFO`]({{< ref "/library/allos/logging#logging.INFO" >}})。

​	如果在 `with` 代码块内部发出了至少一条与 *logger* 和 *level* 条件相匹配的消息则测试通过，否则测试失败。

​	上下文管理器返回的对象是一个记录辅助器，它会记录所匹配的日志消息。 它有两个属性:

## **records**

​	所匹配的日志消息 [`logging.LogRecord`]({{< ref "/library/allos/logging#logging.LogRecord" >}}) 对象组成的列表。

## **output**

​	由 [`str`]({{< ref "/library/stdtypes#str" >}}) 对象组成的列表，内容为所匹配消息经格式化后的输出。

​	示例:

```
with self.assertLogs('foo', level='INFO') as cm:
    logging.getLogger('foo').info('first message')
    logging.getLogger('foo.bar').error('second message')
self.assertEqual(cm.output, ['INFO:foo:first message',
                             'ERROR:foo.bar:second message'])
```

> Added in version 3.4.
>

## **assertNoLogs**(*logger=None*, *level=None*)

​	一个上下文管理器，它测试在 *logger* 或其子对象上是否未记录任何至少为指定 *level* 以上级别的消息。

​	如果给出了 *logger* 则它应为一个 [`logging.Logger`]({{< ref "/library/allos/logging#logging.Logger" >}}) 对象或为一个指定日志记录器名称的 [`str`]({{< ref "/library/stdtypes#str" >}})。 默认为根日志记录器，它将捕获所有消息。

​	如果给出了 *level*，它应为一个用数字表示的日志记录级别或其字符串等价形式 (例如为 `"ERROR"` 或 [`logging.ERROR`]({{< ref "/library/allos/logging#logging.ERROR" >}}))。 默认为 [`logging.INFO`]({{< ref "/library/allos/logging#logging.INFO" >}})。

​	与 [`assertLogs()`]({{< ref "/library/development/unittest#unittest.TestCase.assertLogs" >}}) 不同，上下文管理器将不返回任何对象。

> Added in version 3.10.
>

​	还有其他一些方法可用于执行更专门的检查，例如:

| 方法                                                         | 检查对象                                            | 引入版本 |
| :----------------------------------------------------------- | :-------------------------------------------------- | :------- |
| [`assertAlmostEqual(a, b)`]({{< ref "/library/development/unittest#unittest.TestCase.assertAlmostEqual" >}}) | `round(a-b, 7) == 0`                                |          |
| [`assertNotAlmostEqual(a, b)`]({{< ref "/library/development/unittest#unittest.TestCase.assertNotAlmostEqual" >}}) | `round(a-b, 7) != 0`                                |          |
| [`assertGreater(a, b)`]({{< ref "/library/development/unittest#unittest.TestCase.assertGreater" >}}) | `a > b`                                             | 3.1      |
| [`assertGreaterEqual(a, b)`]({{< ref "/library/development/unittest#unittest.TestCase.assertGreaterEqual" >}}) | `a >= b`                                            | 3.1      |
| [`assertLess(a, b)`]({{< ref "/library/development/unittest#unittest.TestCase.assertLess" >}}) | `a < b`                                             | 3.1      |
| [`assertLessEqual(a, b)`]({{< ref "/library/development/unittest#unittest.TestCase.assertLessEqual" >}}) | `a <= b`                                            | 3.1      |
| [`assertRegex(s, r)`]({{< ref "/library/development/unittest#unittest.TestCase.assertRegex" >}}) | `r.search(s)`                                       | 3.1      |
| [`assertNotRegex(s, r)`]({{< ref "/library/development/unittest#unittest.TestCase.assertNotRegex" >}}) | `not r.search(s)`                                   | 3.2      |
| [`assertCountEqual(a, b)`]({{< ref "/library/development/unittest#unittest.TestCase.assertCountEqual" >}}) | *a* 和 *b* 具有同样数量的相同元素，无论其顺序如何。 | 3.2      |

## **assertAlmostEqual**(*first*, *second*, *places=7*, *msg=None*, *delta=None*)

## **assertNotAlmostEqual**(*first*, *second*, *places=7*, *msg=None*, *delta=None*)

​	测试 *first* 与 *second* 是否几乎相等，比较的标准是计算差值并舍入到 *places* 所指定的十进制位数 (默认为 7 位)，再与零相比较。 请注意此方法是将结果值舍入到指定的 *十进制位数* (即相当于 [`round()`]({{< ref "/library/functions#round" >}}) 函数) 而非 *有效位数*。

​	如果提供了 *delta* 而非 *places* 则 *first* 和 *second* 之间的差值必须小于等于 (或大于) *delta*。

​	同时提供 *delta* 和 *places* 将引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。

> 在 3.2 版本发生变更: [`assertAlmostEqual()`]({{< ref "/library/development/unittest#unittest.TestCase.assertAlmostEqual" >}}) 会自动将几乎相等的对象视为相等。 而如果对象相等则 [`assertNotAlmostEqual()`]({{< ref "/library/development/unittest#unittest.TestCase.assertNotAlmostEqual" >}}) 会自动测试失败。 增加了 *delta* 关键字参数。

## **assertGreater**(*first*, *second*, *msg=None*)

## **assertGreaterEqual**(*first*, *second*, *msg=None*)

## **assertLess**(*first*, *second*, *msg=None*)

## **assertLessEqual**(*first*, *second*, *msg=None*)

​	根据方法名分别测试 *first* 是否 >, >=, < 或 <= *second*。 如果不是，则测试失败:



``` python
>>> self.assertGreaterEqual(3, 4)
AssertionError: "3" unexpectedly not greater than or equal to "4"
```

> Added in version 3.1.
>

## **assertRegex**(*text*, *regex*, *msg=None*)

## **assertNotRegex**(*text*, *regex*, *msg=None*)

​	测试一个 *regex* 搜索匹配（或不匹配） *文本*。如果不匹配，错误信息中将包含匹配模式和 *文本\*（或部分匹配失败的 \*文本*）。*regex* 可以是正则表达式对象或能够用于 [`re.search()`]({{< ref "/library/text/re#re.search" >}}) 的包含正则表达式的字符串。

> Added in version 3.1:
> 以方法名 `assertRegexpMatches` 添加。

> 在 3.2 版本发生变更: 方法 `assertRegexpMatches()` 已被改名为 [`assertRegex()`]({{< ref "/library/development/unittest#unittest.TestCase.assertRegex" >}})。

> Added in version 3.2:
> [`assertNotRegex()`]({{< ref "/library/development/unittest#unittest.TestCase.assertNotRegex" >}})

## **assertCountEqual**(*first*, *second*, *msg=None*)

​	测试序列 *first* 与 *second* 是否包含同样的元素，无论其顺序如何。 当存在差异时，将生成一条错误消息来列出两个序列之间的差异。

​	重复的元素 *不会* 在 *first* 和 *second* 的比较中被忽略。 它会检查每个元素在两个序列中的出现次数是否相同。 等价于: `assertEqual(Counter(list(first)), Counter(list(second)))` 但还适用于包含不可哈希对象的序列。

> Added in version 3.2.
>

[`assertEqual()`]({{< ref "/library/development/unittest#unittest.TestCase.assertEqual" >}}) 方法会将相同类型对象的相等性检查分派给不同的类型专属方法。 这些方法已被大多数内置类型所实现，但也可以使用 [`addTypeEqualityFunc()`]({{< ref "/library/development/unittest#unittest.TestCase.addTypeEqualityFunc" >}}) 来注册新的方法:

## **addTypeEqualityFunc**(*typeobj*, *function*)

​	注册一个由 [`assertEqual()`]({{< ref "/library/development/unittest#unittest.TestCase.assertEqual" >}}) 调用的特定类型专属方法来检查恰好为相同 *typeobj* (而非子类) 的两个对象是否相等。 *function* 必须接受两个位置参数和第三个 msg=None 关键字参数，就像 [`assertEqual()`]({{< ref "/library/development/unittest#unittest.TestCase.assertEqual" >}}) 那样。 当检测到前两个形参之间不相等时它必须引发 [`self.failureException(msg)`]({{< ref "/library/development/unittest#unittest.TestCase.failureException" >}}) -- 可能还会提供有用的信息并在错误消息中详细解释不相等的原因。

> Added in version 3.1.
>

​	以下是 [`assertEqual()`]({{< ref "/library/development/unittest#unittest.TestCase.assertEqual" >}}) 自动选用的不同类型的比较方法。一般情况下不需要直接在测试中调用这些方法。

| 方法                                                         | 用作比较 | 引入版本 |
| :----------------------------------------------------------- | :------- | :------- |
| [`assertMultiLineEqual(a, b)`]({{< ref "/library/development/unittest#unittest.TestCase.assertMultiLineEqual" >}}) | 字符串   | 3.1      |
| [`assertSequenceEqual(a, b)`]({{< ref "/library/development/unittest#unittest.TestCase.assertSequenceEqual" >}}) | 序列     | 3.1      |
| [`assertListEqual(a, b)`]({{< ref "/library/development/unittest#unittest.TestCase.assertListEqual" >}}) | 列表     | 3.1      |
| [`assertTupleEqual(a, b)`]({{< ref "/library/development/unittest#unittest.TestCase.assertTupleEqual" >}}) | 元组     | 3.1      |
| [`assertSetEqual(a, b)`]({{< ref "/library/development/unittest#unittest.TestCase.assertSetEqual" >}}) | 集合     | 3.1      |
| [`assertDictEqual(a, b)`]({{< ref "/library/development/unittest#unittest.TestCase.assertDictEqual" >}}) | 字典     | 3.1      |

## **assertMultiLineEqual**(*first*, *second*, *msg=None*)

​	测试多行字符串 *first* 是否与字符串 *second* 相等。 当不相等时将在错误消息中包括两个字符串之间差异的高亮显示。 此方法会在通过 [`assertEqual()`]({{< ref "/library/development/unittest#unittest.TestCase.assertEqual" >}}) 进行字符串比较时默认被使用。

> Added in version 3.1.
>

## **assertSequenceEqual**(*first*, *second*, *msg=None*, *seq_type=None*)

​	测试两个序列是否相等。 如果提供了 *seq_type*，则 *first* 和 *second* 都必须为 *seq_type* 的实例否则将引发失败。 如果两个序列不相等则会构造一个错误消息来显示两者之间的差异。

​	此方法不会被 [`assertEqual()`]({{< ref "/library/development/unittest#unittest.TestCase.assertEqual" >}}) 直接调用，但它会被用于实现 [`assertListEqual()`]({{< ref "/library/development/unittest#unittest.TestCase.assertListEqual" >}}) 和 [`assertTupleEqual()`]({{< ref "/library/development/unittest#unittest.TestCase.assertTupleEqual" >}})。

> Added in version 3.1.
>

## **assertListEqual**(*first*, *second*, *msg=None*)

## **assertTupleEqual**(*first*, *second*, *msg=None*)

​	测试两个列表或元组是否相等。 如果不相等，则会构造一个错误消息来显示两者之间的差异。 如果某个形参的类型不正确也会引发错误。 这些方法会在通过 [`assertEqual()`]({{< ref "/library/development/unittest#unittest.TestCase.assertEqual" >}}) 进行列表或元组比较时默认被使用。

> Added in version 3.1.
>

## **assertSetEqual**(*first*, *second*, *msg=None*)

​	测试两个集合是否相等。 如果不相等，则会构造一个错误消息来列出两者之间的差异。 此方法会在通过 [`assertEqual()`]({{< ref "/library/development/unittest#unittest.TestCase.assertEqual" >}}) 进行集合或冻结集合比较时默认被使用。

​	如果 *first* 或 *second* 没有 `set.difference()` 方法则测试失败。

> Added in version 3.1.
>

## **assertDictEqual**(*first*, *second*, *msg=None*)

​	测试两个字典是否相等。 如果不相等，则会构造一个错误消息来显示两个字典的差异。 此方法会在对 [`assertEqual()`]({{< ref "/library/development/unittest#unittest.TestCase.assertEqual" >}}) 的调用中默认被用来进行字典的比较。

> Added in version 3.1.
>

​	最后 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 还提供了以下的方法和属性:

## **fail**(*msg=None*)

​	无条件地发出测试失败消息，附带错误消息 *msg* 或 `None`。

## **failureException**

​	这个类属性给出测试方法所引发的异常。 如果某个测试框架需要使用专门的异常，并可能附带额外的信息，则必须子类化该类以便与框架“正常互动”。 这个属性的初始值为 [`AssertionError`]({{< ref "/library/exceptions#AssertionError" >}})。

## **longMessage**

​	这个类属性决定当将一个自定义失败消息作为 msg 参数传给一个失败的 assertXYY 调用时会发生什么。默认值为 `True`。 在此情况下，自定义消息会被添加到标准失败消息的末尾。 当设为 `False` 时，自定义消息会替换标准消息。

​	类设置可以通过在调用断言方法之前将一个实例属性 self.longMessage 赋值为 `True` 或 `False` 在单个测试方法中进行重载。

​	类设置会在每个测试调用之前被重置。

> Added in version 3.1.
>

## **maxDiff**

​	这个属性控制来自在测试失败时报告 diffs 的断言方法的 diffs 输出的最大长度。 它默认为 80*8 个字符。 这个属性所影响的断言方法有 [`assertSequenceEqual()`]({{< ref "/library/development/unittest#unittest.TestCase.assertSequenceEqual" >}}) (包括所有委托给它的序列比较方法), [`assertDictEqual()`]({{< ref "/library/development/unittest#unittest.TestCase.assertDictEqual" >}}) 以及 [`assertMultiLineEqual()`]({{< ref "/library/development/unittest#unittest.TestCase.assertMultiLineEqual" >}})。

​	将 `maxDiff` 设为 `None` 表示不限制 diffs 的最大长度。

> Added in version 3.2.
>

​	测试框架可使用下列方法来收集测试的有关信息:

## **countTestCases**()

​	返回此测试对象所提供的测试数量。 对于 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 实例，该数量将总是为 `1`。

## **defaultTestResult**()

​	返回此测试类所要使用的测试结果类的实例（如果未向 [`run()`]({{< ref "/library/development/unittest#unittest.TestCase.run" >}}) 方法提供其他结果实例）。

​	对于 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 实例，该返回值将总是为 [`TestResult`]({{< ref "/library/development/unittest#unittest.TestResult" >}}) 的实例；[`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 的子类应当在有必要时重写此方法。

## **id**()

​	返回一个标识指定测试用例的字符串。 该返回值通常为测试方法的完整名称，包括模块名和类名。

## **shortDescription**()

​	返回测试的描述，如果未提供描述则返回 `None`。 此方法的默认实现将在可用的情况下返回测试方法的文档字符串的第一行，或者返回 `None`。

> 在 3.1 版本发生变更: 在 3.1 中已修改此方法将测试名称添加到简短描述中，即使存在文档字符串。 这导致了与单元测试扩展的兼容性问题因而在 Python 3.2 中将添加测试名称操作改到 [`TextTestResult`]({{< ref "/library/development/unittest#unittest.TextTestResult" >}}) 中。

## **addCleanup**(*function*, */*, **args*, ***kwargs*)

​	在 [`tearDown()`]({{< ref "/library/development/unittest#unittest.TestCase.tearDown" >}}) 之后添加了一个要调用的函数来清理测试期间所使用的资源。 函数将按它们被添加的相反顺序被调用 (LIFO)。 它们在调用时将附带它们被添加时传给 [`addCleanup()`]({{< ref "/library/development/unittest#unittest.TestCase.addCleanup" >}}) 的任何参数和关键字参数。

​	如果 [`setUp()`]({{< ref "/library/development/unittest#unittest.TestCase.setUp" >}}) 失败，即意味着 [`tearDown()`]({{< ref "/library/development/unittest#unittest.TestCase.tearDown" >}}) 未被调用，则已添加的任何清理函数仍将被调用。

> Added in version 3.1.
>

## **enterContext**(*cm*)

​	进入所提供的 [context manager]({{< ref "/glossary/idx#term-context-manager" >}})。 如果成功，还会将其 [`__exit__()`]({{< ref "/reference/datamodel#object.__exit__" >}}) 方法作为使用 [`addCleanup()`]({{< ref "/library/development/unittest#unittest.TestCase.addCleanup" >}}) 的清理函数并返回 [`__enter__()`]({{< ref "/reference/datamodel#object.__enter__" >}}) 方法的结果。

> Added in version 3.11.
>

## **doCleanups**()

​	此方法会在 [`tearDown()`]({{< ref "/library/development/unittest#unittest.TestCase.tearDown" >}}) 之后，或者如果 [`setUp()`]({{< ref "/library/development/unittest#unittest.TestCase.setUp" >}}) 引发了异常则会在 [`setUp()`]({{< ref "/library/development/unittest#unittest.TestCase.setUp" >}}) 之后被调用。

​	它将负责调用由 [`addCleanup()`]({{< ref "/library/development/unittest#unittest.TestCase.addCleanup" >}}) 添加的所有清理函数。 如果你需要在 [`tearDown()`]({{< ref "/library/development/unittest#unittest.TestCase.tearDown" >}}) *之前* 调用清理函数则可以自行调用 [`doCleanups()`]({{< ref "/library/development/unittest#unittest.TestCase.doCleanups" >}})。

[`doCleanups()`]({{< ref "/library/development/unittest#unittest.TestCase.doCleanups" >}}) 每次会弹出清理函数栈中的一个方法，因此它可以在任何时候被调用。

> Added in version 3.1.
>

## *classmethod* **addClassCleanup**(*function*, */*, **args*, ***kwargs*)

​	在Add a function to be called after [`tearDownClass()`]({{< ref "/library/development/unittest#unittest.TestCase.tearDownClass" >}}) 之后添加了一个要调用的函数来清理测试类运行期间所使用的资源。 函数将按它们被添加的相反顺序被调用 (LIFO)。 它们在调用时将附带它们被添加时传给 [`addClassCleanup()`]({{< ref "/library/development/unittest#unittest.TestCase.addClassCleanup" >}}) 的任何参数和关键字参数。

​	如果 [`setUpClass()`]({{< ref "/library/development/unittest#unittest.TestCase.setUpClass" >}}) 失败，即意味着 [`tearDownClass()`]({{< ref "/library/development/unittest#unittest.TestCase.tearDownClass" >}}) 未被调用，则已添加的任何清理函数仍将被调用。

> Added in version 3.8.
>

## *classmethod* **enterClassContext**(*cm*)

​	进入所提供的 [context manager]({{< ref "/glossary/idx#term-context-manager" >}})。 如果成功，还会将其 [`__exit__()`]({{< ref "/reference/datamodel#object.__exit__" >}}) 方法作为使用 [`addClassCleanup()`]({{< ref "/library/development/unittest#unittest.TestCase.addClassCleanup" >}}) 的清理函数并返回 [`__enter__()`]({{< ref "/reference/datamodel#object.__enter__" >}}) 方法的结果。

> Added in version 3.11.
>

## *classmethod* **doClassCleanups**()

​	此方法会在 [`tearDownClass()`]({{< ref "/library/development/unittest#unittest.TestCase.tearDownClass" >}}) 之后无条件地被调用，或者如果 [`setUpClass()`]({{< ref "/library/development/unittest#unittest.TestCase.setUpClass" >}}) 引发了异常则会在 [`setUpClass()`]({{< ref "/library/development/unittest#unittest.TestCase.setUpClass" >}}) 之后被调用。

​	它将负责访问由 [`addClassCleanup()`]({{< ref "/library/development/unittest#unittest.TestCase.addClassCleanup" >}}) 添加的所有清理函数。 如果你需要在 [`tearDownClass()`]({{< ref "/library/development/unittest#unittest.TestCase.tearDownClass" >}}) *之前* 调用清理函数则可以自行调用 [`doClassCleanups()`]({{< ref "/library/development/unittest#unittest.TestCase.doClassCleanups" >}})。

[`doClassCleanups()`]({{< ref "/library/development/unittest#unittest.TestCase.doClassCleanups" >}}) 每次会弹出清理函数栈中的一个方法，因此它在任何时候被调用。

> Added in version 3.8.
>

## *class* unittest.**IsolatedAsyncioTestCase**(*methodName='runTest'*)

​	这个类提供了与 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 类似的 API 并也接受协程作为测试函数。

> Added in version 3.8.
>

## **loop_factory**

​	传给 [`asyncio.Runner`]({{< ref "/library/ipc/asyncio/asyncio-runner#asyncio.Runner" >}}) 的 *loop_factory*。 通过 [`asyncio.EventLoop`]({{< ref "/library/ipc/asyncio/asyncio-eventloop#asyncio.EventLoop" >}}) 重写子类以避免使用 asyncio 策略系统。

> Added in version 3.13.
>

## *coroutine* **asyncSetUp**()

​	为测试预备而调用的方法。 此方法会在 `setUp()` 之后被调用。 此方法将在调用测试方法之前立即被调用；除了 [`AssertionError`]({{< ref "/library/exceptions#AssertionError" >}}) 或 [`SkipTest`]({{< ref "/library/development/unittest#unittest.SkipTest" >}})，此方法所引发的任何异常都将被视为错误而非测试失败。 默认的实现将不做任何事情。

## *coroutine* **asyncTearDown**()

​	在测试方法被调用并记录结果之后立即被调用的方法。 此方法会在 `tearDown()` 之前被调用。 此方法即使在测试方法引发异常时仍会被调用，因此子类中的实现将需要特别注意检查内部状态。 除 [`AssertionError`]({{< ref "/library/exceptions#AssertionError" >}}) 或 [`SkipTest`]({{< ref "/library/development/unittest#unittest.SkipTest" >}}) 外，此方法所引发的任何异常都将被视为额外的错误而非测试失败（因而会增加总计错误报告数）。 此方法将只在 [`asyncSetUp()`]({{< ref "/library/development/unittest#unittest.IsolatedAsyncioTestCase.asyncSetUp" >}}) 成功执行时被调用，无论测试方法的结果如何。 默认的实现将不做任何事情。

## **addAsyncCleanup**(*function*, */*, **args*, ***kwargs*)

​	此方法接受一个可被用作清理函数的协程。

## *coroutine* **enterAsyncContext**(*cm*)

​	进入所提供的 [asynchronous context manager]({{< ref "/glossary/idx#term-asynchronous-context-manager" >}})。 如果成功，还会将其 [`__aexit__()`]({{< ref "/reference/datamodel#object.__aexit__" >}}) 方法作为使用 [`addAsyncCleanup()`]({{< ref "/library/development/unittest#unittest.IsolatedAsyncioTestCase.addAsyncCleanup" >}}) 的清理函数并返回 [`__aenter__()`]({{< ref "/reference/datamodel#object.__aenter__" >}}) 方法的结果。

> Added in version 3.11.
>

## **run**(*result=None*)

​	设置一个新的事件循环来运行测试，将结果收集至作为 *result* 传入的 [`TestResult`]({{< ref "/library/development/unittest#unittest.TestResult" >}})。 如果 *result* 被省略或为 `None`，则会创建一个临时的结果对象（通过调用 `defaultTestResult()` 方法）并使用它。 结果对象会被返回给 [`run()`]({{< ref "/library/development/unittest#unittest.IsolatedAsyncioTestCase.run" >}}) 的调用方。 在测试结束时事件循环中的所有任务都将被取消。

​	一个显示先后顺序的例子:

```
from unittest import IsolatedAsyncioTestCase

events = []


class Test(IsolatedAsyncioTestCase):


    def setUp(self):
        events.append("setUp")

    async def asyncSetUp(self):
        self._async_connection = await AsyncConnection()
        events.append("asyncSetUp")

    async def test_response(self):
        events.append("test_response")
        response = await self._async_connection.get("https://example.com")
        self.assertEqual(response.status_code, 200)
        self.addAsyncCleanup(self.on_cleanup)

    def tearDown(self):
        events.append("tearDown")

    async def asyncTearDown(self):
        await self._async_connection.close()
        events.append("asyncTearDown")

    async def on_cleanup(self):
        events.append("cleanup")

if __name__ == "__main__":
    unittest.main()
```

​	在运行测试之后，`events` 将会包含 `["setUp", "asyncSetUp", "test_response", "asyncTearDown", "tearDown", "cleanup"]`。

## *class* unittest.**FunctionTestCase**(*testFunc*, *setUp=None*, *tearDown=None*, *description=None*)

​	这个类实现了 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 的部分接口，允许测试运行方驱动测试，但不提供可被测试代码用来检查和报告错误的方法。 这个类被用于创建使用传统测试代码的测试用例，允许它被集成到基于 [`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 的测试框架中。



### 分组测试

## *class* unittest.**TestSuite**(*tests=()*)

​	这个类代表对单独测试用例和测试套件的聚合。 这个类提供给测试运行方所需的接口以允许其像任何其他测试用例一样运行。 运行一个 [`TestSuite`]({{< ref "/library/development/unittest#unittest.TestSuite" >}}) 实例与对套件执行迭代来逐一运行每个测试的效果相同。

​	如果给出了 *tests*，则它必须是一个包含单独测试用例的可迭代对象或是将被用于初始构建测试套件的其他测试套件。 还有一些附加的方法会被提供用来在随后向测试集添加测试用例和测试套件。

[`TestSuite`]({{< ref "/library/development/unittest#unittest.TestSuite" >}}) 对象的行为与 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 对象很相似，区别在于它们并不会真正实现一个测试。 它们会被用来将测试聚合为多个要同时运行的测试分组。 还有一些附加的方法会被用来向 [`TestSuite`]({{< ref "/library/development/unittest#unittest.TestSuite" >}}) 实例添加测试:

## **addTest**(*test*)

​	向测试套件添加 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 或 [`TestSuite`]({{< ref "/library/development/unittest#unittest.TestSuite" >}})。

## **addTests**(*tests*)

​	将来自包含 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 和 [`TestSuite`]({{< ref "/library/development/unittest#unittest.TestSuite" >}}) 实例的可迭代对象的所有测试添加到这个测试套件。

​	这等价于对 *tests* 进行迭代，并为其中的每个元素调用 [`addTest()`]({{< ref "/library/development/unittest#unittest.TestSuite.addTest" >}})。

[`TestSuite`]({{< ref "/library/development/unittest#unittest.TestSuite" >}}) 与 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 共享下列方法:

## **run**(*result*)

​	运行与这个套件相关联的测试，将结果收集到作为 *result* 传入的测试结果对象中。 请注意与 [`TestCase.run()`]({{< ref "/library/development/unittest#unittest.TestCase.run" >}}) 的区别，[`TestSuite.run()`]({{< ref "/library/development/unittest#unittest.TestSuite.run" >}}) 必须传入结果对象。

## **debug**()

​	运行与这个套件相关联的测试而不收集结果。 这允许测试所引发的异常被传递给调用方并可被用于支持在调试器中运行测试。

## **countTestCases**()

​	返回此测试对象所提供的测试数量，包括单独的测试和子套件。

## `__iter__`()

​	由 [`TestSuite`]({{< ref "/library/development/unittest#unittest.TestSuite" >}}) 分组的测试总是可以通过迭代来访问。 其子类可以通过重载 `__iter__()` 来惰性地提供测试。 请注意此方法可在单个套件上多次被调用（例如在计数或相等性比较时），为此在 [`TestSuite.run()`]({{< ref "/library/development/unittest#unittest.TestSuite.run" >}}) 之前重复迭代所返回的测试对于每次调用迭代都必须相同。 在 [`TestSuite.run()`]({{< ref "/library/development/unittest#unittest.TestSuite.run" >}}) 之后，调用方不应继续访问此方法所返回的测试，除非调用方使用重载了 `TestSuite._removeTestAtIndex()` 的子类来保留对测试的引用。

> 在 3.2 版本发生变更: 在较早的版本中 [`TestSuite`]({{< ref "/library/development/unittest#unittest.TestSuite" >}}) 会直接访问测试而不是通过迭代，因此只重载 `__iter__()` 并不足以提供所有测试。

> 在 3.4 版本发生变更: 在较早的版本中 [`TestSuite`]({{< ref "/library/development/unittest#unittest.TestSuite" >}}) 会在 [`TestSuite.run()`]({{< ref "/library/development/unittest#unittest.TestSuite.run" >}}) 之后保留对每个 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 的引用。 其子类可以通过重载 `TestSuite._removeTestAtIndex()` 来恢复此行为。

​	在 [`TestSuite`]({{< ref "/library/development/unittest#unittest.TestSuite" >}}) 对象的典型应用中，[`run()`]({{< ref "/library/development/unittest#unittest.TestSuite.run" >}}) 方法是由 `TestRunner` 发起调用而不是由最终用户测试来控制。

### 加载和运行测试

## *class* unittest.**TestLoader**

[`TestLoader`]({{< ref "/library/development/unittest#unittest.TestLoader" >}}) 类可被用来基于类和模块创建测试套件。 通常，没有必要创建该类的实例；[`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 模块提供了一个可作为 [`unittest.defaultTestLoader`]({{< ref "/library/development/unittest#unittest.defaultTestLoader" >}}) 共享的实例。 但是，使用子类或实例允许对某些配置属性进行定制。

[`TestLoader`]({{< ref "/library/development/unittest#unittest.TestLoader" >}}) 对象具有下列属性:

## **errors**

​	由在加载测试期间遇到的非致命错误组成的列表。 在任何时候都不会被加载方重围。 致命错误是通过相关方法引发一个异常来向调用方发出信号的。 非致命错误也是由一个将在运行时引发原始错误的合成测试来提示的。

> Added in version 3.5.
>

[`TestLoader`]({{< ref "/library/development/unittest#unittest.TestLoader" >}}) 对象具有下列方法:

## **loadTestsFromTestCase**(*testCaseClass*)

​	返回一个包含在 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 所派生的 `testCaseClass` 中的所有测试用例的测试套件。

​	会为每个由 [`getTestCaseNames()`]({{< ref "/library/development/unittest#unittest.TestLoader.getTestCaseNames" >}}) 指明的方法创建一个测试用例实例。 在默认情况下这些都是以 `test` 开头的方法名称。 如果 [`getTestCaseNames()`]({{< ref "/library/development/unittest#unittest.TestLoader.getTestCaseNames" >}}) 不返回任何方法，但 `runTest()` 方法已被实现，则会为该方法创建一个单独的测试用例。

## **loadTestsFromModule**(*module*, ***, *pattern=None*)

​	返回包含在给定模块中的所有测试用例的测试套件。 此方法会在 *module* 中搜索从派生自 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 的类并为该类定义的每个测试方法创建一个类实例。

​备注
 

​	虽然使用 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 所派生的类的层级结构可以方便地共享配置和辅助函数，但在不打算直接实例化的基类上定义测试方法并不能很好地配合此方法使用。 不过，当配置有差异并且定义在子类当中时这样做还是有用处的。

​	如果一个模块提供了 `load_tests` 函数则它将被调用以加载测试。 这允许模块自行定制测试加载过程。 这就称为 [load_tests protocol]({{< ref "/library/development/unittest#id1" >}})。 *pattern* 参数会被作为传给 `load_tests` 的第三个参数。

> 在 3.2 版本发生变更: 添加了对 `load_tests` 的支持。

> 在 3.5 版本发生变更: 增加了对仅限关键字参数 *pattern* 的支持。

> 在 3.12 版本发生变更: 未写入文档的非正式 *use_load_tests* 形参已被移除。

## **loadTestsFromName**(*name*, *module=None*)

​	返回由给出了字符串形式规格描述的所有测试用例组成的测试套件。

​	描述名称 *name* 是一个“带点号的名称”，它可以被解析为一个模块、一个测试用例类、一个测试用例类内部的测试方法、一个 [`TestSuite`]({{< ref "/library/development/unittest#unittest.TestSuite" >}}) 实例，或者一个返回 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 或 [`TestSuite`]({{< ref "/library/development/unittest#unittest.TestSuite" >}}) 实例的可调用对象。 这些检查将按在此列出的顺序执行；也就是说，一个可能的测试用例类上的方法将作为“一个测试用例内部的测试方法”而非作为“一个可调用对象”被选定。

​	举例来说，如果你有一个模块 `SampleTests`，其中包含一个派生自 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 的类 `SampleTestCase`，其中包含三个测试方法 (`test_one()`, `test_two()` 和 `test_three()`)。 则描述名称 `'SampleTests.SampleTestCase'` 将使此方法返回一个测试套件，它将运行全部三个测试方法。 使用描述名称 `'SampleTests.SampleTestCase.test_two'` 将使它返回一个测试套件，它将仅运行 `test_two()` 测试方法。 描述名称可以指向尚未被导入的模块和包；它们将作为附带影响被导入。

​	本模块可以选择相对于给定的 *module* 来解析 *name*。

> 在 3.5 版本发生变更: 如果在遍历 *name* 时发生了 [`ImportError`]({{< ref "/library/exceptions#ImportError" >}}) 或 [`AttributeError`]({{< ref "/library/exceptions#AttributeError" >}}) 则在运行时引发该错误的合成测试将被返回。 这些错误被包括在由 self.errors 所积累的错误中。

## **loadTestsFromNames**(*names*, *module=None*)

​	类似于 [`loadTestsFromName()`]({{< ref "/library/development/unittest#unittest.TestLoader.loadTestsFromName" >}})，但是接受一个名称序列而不是单个名称。 返回值是一个测试套件，它支持为每个名称所定义的所有测试。

## **getTestCaseNames**(*testCaseClass*)

​	返回由 *testCaseClass* 中找到的方法名称组成的已排序的序列；这应当是 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 的一个子类。

## **discover**(*start_dir*, *pattern='test\*.py'*, *top_level_dir=None*)

​	通过从指定的开始目录向其子目录递归来找出所有测试模块，并返回一个包含该结果的 TestSuite 对象。 只有与 *pattern* 匹配的测试文件才会被加载。 （使用 shell 风格的模式匹配。） 只有可导入的模块名称（即有效的 Python 标识符）将会被加载。

​	所有测试模块都必须可以从项目的最高层级上导入。 如果起始目录不是最高层级则必须单独指明 *top_level_dir*。

​	如果导入某个模块失败，比如因为存在语法错误，则会将其记录为单独的错误并将继续查找模块。 如果导入失败是因为引发了 [`SkipTest`]({{< ref "/library/development/unittest#unittest.SkipTest" >}})，则会将其记录为跳过而不是错误。

​	如果找到了一个包（即包含名为 `__init__.py` 的文件的目录），则将在包中查找 `load_tests` 函数。 如果存在此函数则将对其执行调用 `package.load_tests(loader, tests, pattern)`。 测试发现操作会确保在执行期间仅检查测试一次，即使 load_tests 函数本身调用了 `loader.discover` 也是如此。.

​	如果 `load_tests` 存在则发现操作 *不会* 对包执行递归处理，`load_tests` 将负责加载包中的所有测试。is responsible for loading all tests in the package.

​	该模式故意不被保存为加载器属性以使得包可以继续发自其自身。

*top_level_dir* 是在内部保存的，并被用作任何对 `discover()` 的嵌套调用的默认值。 也就是说，如果一个包的 `load_tests` 调用了 `loader.discover()`，则无需传递此参数。

*start_dir* 可以是一个带点号的名称或是一个目录。

> Added in version 3.2.
>

> 在 3.4 版本发生变更: 在导入时引发 [`SkipTest`]({{< ref "/library/development/unittest#unittest.SkipTest" >}}) 的模块会被记录为跳过，而不是错误。

> 在 3.4 版本发生变更: *start_dir* 可以是一个 [命名空间包]({{< ref "/glossary/idx#term-namespace-package" >}})。

> 在 3.4 版本发生变更: 路径在被导入之前会先被排序以使得执行顺序保持一致，即使下层文件系统的顺序不是取决于文件名的。

> 在 3.5 版本发生变更: 现在 `load_tests` 会检查已找到的包，无论它们的路径是否与 *pattern* 匹配，因为包名称是无法与默认的模式匹配的。

> 在 3.11 版本发生变更: *start_dir* 不可以为 [命名空间包]({{< ref "/glossary/idx#term-namespace-package" >}})。 它自 Python 3.7 开始已不可用而 Python 3.11 正式将其移除。

> 在 3.13 版本发生变更: *top_level_dir* 仅会在 *discover* 调用期间被保存。

[`TestLoader`]({{< ref "/library/development/unittest#unittest.TestLoader" >}}) 的下列属性可通过子类化或在实例上赋值来配置:

## **testMethodPrefix**

​	给出将被解读为测试方法的方法名称的前缀的字符串。 默认值为 `'test'`。

​	这会影响 [`getTestCaseNames()`]({{< ref "/library/development/unittest#unittest.TestLoader.getTestCaseNames" >}}) 以及所有 `loadTestsFrom*` 方法。

## **sortTestMethodsUsing**

​	将被用来在 [`getTestCaseNames()`]({{< ref "/library/development/unittest#unittest.TestLoader.getTestCaseNames" >}}) 以及所有 `loadTestsFrom*` 方法中比较方法名称以便对它们进行排序。

## **suiteClass**

​	根据一个测试列表来构造测试套件的可调用对象。 不需要结果对象上的任何方法。 默认值为 [`TestSuite`]({{< ref "/library/development/unittest#unittest.TestSuite" >}}) 类。

​	这会影响所有 `loadTestsFrom*` 方法。

## **testNamePatterns**

​	由 Unix shell 风格通配符的测试名称模式组成的列表，供测试方法进行匹配以包括在测试套件中 （参见 `-k` 选项）。

​	如果该属性不为 `None` (默认值)，则将要包括在测试套件中的所有测试方法都必须匹配该列表中的某个模式。 请注意匹配总是使用 [`fnmatch.fnmatchcase()`]({{< ref "/library/filesys/fnmatch#fnmatch.fnmatchcase" >}})，因此不同于传给 `-k` 选项的模式，简单的子字符串模式将必须使用 `*` 通配符来进行转换。

​	这会影响所有 `loadTestsFrom*` 方法。

> Added in version 3.7.
>

## *class* unittest.**TestResult**

​	这个类被用于编译有关哪些测试执行成功而哪些失败的信息。

​	存放一组测试的结果的 [`TestResult`]({{< ref "/library/development/unittest#unittest.TestResult" >}}) 对象。 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 和 [`TestSuite`]({{< ref "/library/development/unittest#unittest.TestSuite" >}}) 类将确保结果被正确地记录；测试创建者无须担心如何记录测试的结果。

​	建立在 [`unittest`]({{< ref "/library/development/unittest#module-unittest" >}}) 之上的测试框架可能会想要访问通过运行一组测试所产生的 [`TestResult`]({{< ref "/library/development/unittest#unittest.TestResult" >}}) 对象用来报告信息；`TestRunner.run()` 方法是出于这个目的而返回 [`TestResult`]({{< ref "/library/development/unittest#unittest.TestResult" >}}) 实例的。

[`TestResult`]({{< ref "/library/development/unittest#unittest.TestResult" >}}) 实例具有下列属性，在检查运行一组测试的结果的时候很有用处。

## **errors**

​	一个包含 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 实例和保存了格式化回溯信息的字符串 2 元组的列表。 每个元组代表一个引发了非预期的异常的测试。

## **failures**

​	一个包含 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 实例和保存了格式化回溯信息的字符串的 2 元组的列表。 每个元素代表一个使用 [assert* 方法]({{< ref "/library/development/unittest#assert-methods" >}}) 显式地发出失败信号的测试。

## **skipped**

​	一个包含 2-tuples of [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 实例和保存了跳过测试原因的字符串 2 元组的列表。

> Added in version 3.1.
>

## **expectedFailures**

​	一个包含 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 实例和保存了格式化回溯信息的 2 元组的列表。 每个元组代表测试用例的一个已预期的失败或错误。

## **unexpectedSuccesses**

​	一个包含被标记为已预期失败，但却测试成功的 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 实例的列表。

## **collectedDurations**

​	一个包含测试用例名称和代表所运行的每个测试所用时间的浮点数 2 元组的列表。

> Added in version 3.12.
>

## **shouldStop**

​	当测试的执行应当被 [`stop()`]({{< ref "/library/development/unittest#unittest.TestResult.stop" >}}) 停止时则设为 `True`。

## **testsRun**

​	目前已运行的测试的总数量。

## **buffer**

​	如果设为真值，`sys.stdout` 和 `sys.stderr` 将在 [`startTest()`]({{< ref "/library/development/unittest#unittest.TestResult.startTest" >}}) 和 [`stopTest()`]({{< ref "/library/development/unittest#unittest.TestResult.stopTest" >}}) 被调用之间被缓冲。 被收集的输出将仅在测试失败或发生错误时才会被回显到真正的 `sys.stdout` 和 `sys.stderr`。 任何输出还会被附加到失败/错误消息中。

> Added in version 3.2.
>

## **failfast**

​	如果设为真值则 [`stop()`]({{< ref "/library/development/unittest#unittest.TestResult.stop" >}}) 将在首次失败或错误时被调用，停止测试运行。

> Added in version 3.2.
>

## **tb_locals**

​	如果设为真值则局部变量将被显示在回溯信息中。

> Added in version 3.5.
>

## **wasSuccessful**()

​	如果当前所有测试都已通过则返回 `True`，否则返回 `False`。

> 在 3.4 版本发生变更: 如果有任何来自测试的 [`unexpectedSuccesses`]({{< ref "/library/development/unittest#unittest.TestResult.unexpectedSuccesses" >}}) 被 [`expectedFailure()`]({{< ref "/library/development/unittest#unittest.expectedFailure" >}}) 装饰器所标记则返回 `False`。

## **stop**()

​	此方法可被调用以提示正在运行的测试集要将 [`shouldStop`]({{< ref "/library/development/unittest#unittest.TestResult.shouldStop" >}}) 属性设为 `True` 来表示其应当被中止。 `TestRunner` 对象应当认同此旗标并返回而不再运行任何额外的测试。

​	例如，该特性会被 [`TextTestRunner`]({{< ref "/library/development/unittest#unittest.TextTestRunner" >}}) 类用来在当用户从键盘发出一个中断信号时停止测试框架。 提供了 `TestRunner` 实现的交互式工具也可通过类似方式来使用该特性。

[`TestResult`]({{< ref "/library/development/unittest#unittest.TestResult" >}}) 类的下列方法被用于维护内部数据结构，并可在子类中被扩展以支持额外的报告需求。 这特别适用于构建支持在运行测试时提供交互式报告的工具。

## **startTest**(*test*)

​	当测试用例 *test* 即将运行时被调用。

## **stopTest**(*test*)

​	在测试用例 *test* 已经执行后被调用，无论其结果如何。

## **startTestRun**()

​	在任何测试被执行之前被调用一次。

> Added in version 3.1.
>

## **stopTestRun**()

​	在所有测试被执行之后被调用一次。

> Added in version 3.1.
>

## **addError**(*test*, *err*)

​	当测试用例 *test* 引发了非预期的异常时将被调用。 *err* 是一个元组，其形式与 [`sys.exc_info()`]({{< ref "/library/python/sys#sys.exc_info" >}}) 的返回值相同: `(type, value, traceback)`。

​	默认实现会将一个元组 `(test, formatted_err)` 添加到实例的 [`errors`]({{< ref "/library/development/unittest#unittest.TestResult.errors" >}}) 属性，其中 *formatted_err* 是派生自 *err* 的已格式化回溯信息。

## **addFailure**(*test*, *err*)

​	当测试用例 *test* 发出了失败信号时将被调用。 *err* 是一个元组，其形式与 [`sys.exc_info()`]({{< ref "/library/python/sys#sys.exc_info" >}}) 的返回值相同: `(type, value, traceback)`。

​	默认实现会将一个元组 `(test, formatted_err)` 添加到实例的 [`failures`]({{< ref "/library/development/unittest#unittest.TestResult.failures" >}}) 属性，其中 *formatted_err* 是派生自 *err* 的已格式化回溯信息。

## **addSuccess**(*test*)

​	当测试用例 *test* 成功时被调用。

​	默认实现将不做任何操作。

## **addSkip**(*test*, *reason*)

​	当测试用例 *test* 被跳过时将被调用。 *reason* 是给出的跳过测试的理由。

​	默认实现会将一个元组 `(test, reason)` 添加到实例的 [`skipped`]({{< ref "/library/development/unittest#unittest.TestResult.skipped" >}}) 属性。

## **addExpectedFailure**(*test*, *err*)

​	当测试用例 *test* 失败或发生错误，但是使用了 [`expectedFailure()`]({{< ref "/library/development/unittest#unittest.expectedFailure" >}}) 装饰器来标记时将被调用。

​	默认实现会将一个元组 `(test, formatted_err)` 添加到实例的 [`expectedFailures`]({{< ref "/library/development/unittest#unittest.TestResult.expectedFailures" >}}) 属性，其中 *formatted_err* 是派生自 *err* 的已格式化回溯信息。

## **addUnexpectedSuccess**(*test*)

​	当测试用例 *test* 使用了was marked with the [`expectedFailure()`]({{< ref "/library/development/unittest#unittest.expectedFailure" >}}) 装饰器来标记，但是却执行成功时将被调用。

​	默认实现会将该测试添加到实例的 [`unexpectedSuccesses`]({{< ref "/library/development/unittest#unittest.TestResult.unexpectedSuccesses" >}}) 属性。

## **addSubTest**(*test*, *subtest*, *outcome*)

​	当一个子测试结束时将被调用。 *test* 是对应于该测试方法的测试用例。 *subtest* 是一个描述该子测试的 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 实例。

​	如果 *outcome* 为 [`None`]({{< ref "/library/constants#None" >}})，则该子测试执行成功。 否则，它将失败并引发一个异常，*outcome* 是一个元组，其形式与 [`sys.exc_info()`]({{< ref "/library/python/sys#sys.exc_info" >}}) 的返回值相同: `(type, value, traceback)`。

​	默认实现在测试结果为成功时将不做任何事，并会将子测试的失败记录为普通的失败。

> Added in version 3.4.
>

## **addDuration**(*test*, *elapsed*)

​	在测试用例结束时被调用。 *elapsed* 是以秒数表示的时间，并且它包括执行清理函数的时间。

> Added in version 3.12.
>

## *class* unittest.**TextTestResult**(*stream*, *descriptions*, *verbosity*, ***, *durations=None*)

​	供 [`TextTestRunner`]({{< ref "/library/development/unittest#unittest.TextTestRunner" >}}) 使用的 [`TestResult`]({{< ref "/library/development/unittest#unittest.TestResult" >}}) 的具体实现。 子类应当接受 `**kwargs` 以确保在接口改变时的兼容性。

> Added in version 3.2.
>

> 在 3.12 版本发生变更: 增加了 *durations* 关键字形参。

## unittest.**defaultTestLoader**

​	用于分享的 [`TestLoader`]({{< ref "/library/development/unittest#unittest.TestLoader" >}}) 类实例。 如果不需要自制 [`TestLoader`]({{< ref "/library/development/unittest#unittest.TestLoader" >}})，则可以使用该实例而不必重复创建新的实例。

## *class* unittest.**TextTestRunner**(*stream=None*, *descriptions=True*, *verbosity=1*, *failfast=False*, *buffer=False*, *resultclass=None*, *warnings=None*, ***, *tb_locals=False*, *durations=None*)

​	一个将结果输出到流的基本测试运行器。 如果 *stream* 为默认的 `None`，则会使用 [`sys.stderr`]({{< ref "/library/python/sys#sys.stderr" >}}) 作为输出流。 这个类具有一些配置形参，但实际上都非常简单。 运行测试套件的图形化应用程序应当提供替代实现。 这样的实现应当在添加新特性到 unittest 时接受 `**kwargs` 作为修改构造运行器的接口。

​	在默认情况下该运行器将显示 [`DeprecationWarning`]({{< ref "/library/exceptions#DeprecationWarning" >}}), [`PendingDeprecationWarning`]({{< ref "/library/exceptions#PendingDeprecationWarning" >}}), [`ResourceWarning`]({{< ref "/library/exceptions#ResourceWarning" >}}) 和 [`ImportWarning`]({{< ref "/library/exceptions#ImportWarning" >}}) 即使它们 [默认会被忽略]({{< ref "/library/python/warnings#warning-ignored" >}})。 此行为可使用 Python 的 `-Wd` 或 `-Wa` 选项 并将 *warnings* 保持为 `None` 来覆盖 (参见 [警告控制]({{< ref "/using/cmdline#using-on-warnings" >}}))。

> 在 3.2 版本发生变更: 增加了 *warnings* 形参。

> 在 3.2 版本发生变更: 默认流会在实例化而不是在导入时被设为 [`sys.stderr`]({{< ref "/library/python/sys#sys.stderr" >}})。

> 在 3.5 版本发生变更: 增加了 *tb_locals* 形参。

> 在 3.12 版本发生变更: 增加了 *durations* 形参。

## **_makeResult**()

​	此方法将返回由 [`run()`]({{< ref "/library/development/unittest#unittest.TextTestRunner.run" >}}) 使用的 `TestResult` 实例。 它不应当被直接调用，但可在子类中被重载以提供自定义的 `TestResult`。

`_makeResult()` 会实例化传给 `TextTestRunner` 构造器的 `resultclass` 参数所指定的类或可迭代对象。 如果没有提供 `resultclass` 则默认为 [`TextTestResult`]({{< ref "/library/development/unittest#unittest.TextTestResult" >}})。 结果类会使用以下参数来实例化:

```
stream, descriptions, verbosity
```

## **run**(*test*)

​	此方法是 `TextTestRunner` 的主要公共接口。 此方法接受一个 [`TestSuite`]({{< ref "/library/development/unittest#unittest.TestSuite" >}}) 或 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 实例。 通过调用 [`_makeResult()`]({{< ref "/library/development/unittest#unittest.TextTestRunner._makeResult" >}}) 创建 [`TestResult`]({{< ref "/library/development/unittest#unittest.TestResult" >}}) 来运行测试并将结果打印到标准输出。

## unittest.**main**(*module='__main__'*, *defaultTest=None*, *argv=None*, *testRunner=None*, *testLoader=unittest.defaultTestLoader*, *exit=True*, *verbosity=1*, *failfast=None*, *catchbreak=None*, *buffer=None*, *warnings=None*)

​	从 *module* 加载一组测试并运行它们的命令行程序；这主要是为了让测试模块能方便地执行。 此函数的最简单用法是在测试脚本末尾包括下列行:

```
if __name__ == '__main__':
    unittest.main()
```

​	你可以通过传入冗余参数运行测试以获得更详细的信息:

```
if __name__ == '__main__':
    unittest.main(verbosity=2)
```

*defaultTest* 参数是要运行的单个测试名称，或者如果未通过 *argv* 指定任何测试名称则是包含多个测试名称的可迭代对象。 如果未指定或为 `None` 且未通过 *argv* 指定任何测试名称，则会运行在 *module* 中找到的所有测试。

*argv* 参数可以是传给程序的选项列表，其中第一个元素是程序名。 如未指定或为 `None`，则会使用 [`sys.argv`]({{< ref "/library/python/sys#sys.argv" >}}) 的值。

*testRunner* 参数可以是一个测试运行器类或是其已创建的实例。 在默认情况下 `main` 会调用 [`sys.exit()`]({{< ref "/library/python/sys#sys.exit" >}}) 并附带一个退出码来指明测试运行是成功 (0) 还是失败 (1)。 退出码为 5 表示没有运行或跳过任何测试。

*testLoader* 参数必须是一个 [`TestLoader`]({{< ref "/library/development/unittest#unittest.TestLoader" >}}) 实例，其默认值为 [`defaultTestLoader`]({{< ref "/library/development/unittest#unittest.defaultTestLoader" >}})。

`main` 支持通过传入 `exit=False` 参数以便在交互式解释器中使用。 这将在标准输出中显示结果而不调用 [`sys.exit()`]({{< ref "/library/python/sys#sys.exit" >}}):



``` python
>>> from unittest import main
>>> main(module='test_module', exit=False)
```

*failfast*, *catchbreak* 和 *buffer* 形参的效果与同名的 [command-line options]({{< ref "/library/development/unittest#command-line-options" >}}) 一致。

*warnings* 参数指定在运行测试时所应使用的 [警告过滤器]({{< ref "/library/python/warnings#warning-filter" >}})。 如果未指定，则默认的 `None` 会在将 `-W` 选项传给 **python** 命令时被保留 (参见 [警告控制]({{< ref "/using/cmdline#using-on-warnings" >}}))，而在其他情况下将被设为 `'default'`。

​	调用 `main` 将返回一个带有 `result` 属性的对象，该属性包含 [`unittest.TestResult`]({{< ref "/library/development/unittest#unittest.TestResult" >}}) 形式的测试运行结果。

> 在 3.1 版本发生变更: 增加了 *exit* 形参。

> 在 3.2 版本发生变更: 增加了 *verbosity*, *failfast*, *catchbreak*, *buffer* 和 *warnings* 形参。

> 在 3.4 版本发生变更: *defaultTest* 形参被修改为也接受一个由测试名称组成的迭代器。



#### load_tests 协议

> Added in version 3.2.
>

​	模块或包可以通过实现一个名为 `load_tests` 的函数来定制在正常测试运行或测试发现期间要如何从中加载测试。

​	如果一个测试模块定义了 `load_tests` 则它将被 [`TestLoader.loadTestsFromModule()`]({{< ref "/library/development/unittest#unittest.TestLoader.loadTestsFromModule" >}}) 调用并传入下列参数:

```
load_tests(loader, standard_tests, pattern)
```

​	其中 *pattern* 会通过 `loadTestsFromModule` 传入。 它的默认值为 `None`。

​	它应当返回一个 [`TestSuite`]({{< ref "/library/development/unittest#unittest.TestSuite" >}})。

*loader* 是执行载入操作的 [`TestLoader`]({{< ref "/library/development/unittest#unittest.TestLoader" >}}) 实例。 *standard_tests* 是默认要从该模块载入的测试。 测试模块通常只需从标准测试集中添加或移除测试。 第三个参数是在作为测试发现的一部分载入包时使用的。

​	一个从指定 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 类集合中载入测试的 `load_tests` 函数看起来可能是这样的:

```
test_cases = (TestCase1, TestCase2, TestCase3)

def load_tests(loader, tests, pattern):
    suite = TestSuite()
    for test_class in test_cases:
        tests = loader.loadTestsFromTestCase(test_class)
        suite.addTests(tests)
    return suite
```

​	如果发现操作是在一个包含包的目录中开始的，不论是通过命令行还是通过调用 [`TestLoader.discover()`]({{< ref "/library/development/unittest#unittest.TestLoader.discover" >}})，则将在包 `__init__.py` 中检查 `load_tests`。 如果不存在此函数，则发现将在包内部执行递归，就像它是另一个目录一样。 在其他情况下，包中测试的发现操作将留给 `load_tests` 执行，它将附带下列参数被调用:

```
load_tests(loader, standard_tests, pattern)
```

​	这应当返回代表包中所有测试的 [`TestSuite`]({{< ref "/library/development/unittest#unittest.TestSuite" >}})。 (`standard_tests` 将只包含从 `__init__.py` 获取的测试。)

​	因为模式已被传入 `load_tests` 所以包可以自由地继续（还可能修改）测试发现操作。 针对一个测试包的 '无操作' `load_tests` 函数看起来是这样的:

```
def load_tests(loader, standard_tests, pattern):
    # top level directory cached on loader instance
    this_dir = os.path.dirname(__file__)
    package_tests = loader.discover(start_dir=this_dir, pattern=pattern)
    standard_tests.addTests(package_tests)
    return standard_tests
```

> 在 3.5 版本发生变更: 发现操作不会再检查包名称是否匹配 *pattern*，因为包名称不可能匹配默认的模式。

## 类与模块设定

​	类与模块设定是在 [`TestSuite`]({{< ref "/library/development/unittest#unittest.TestSuite" >}}) 中实现的。 当测试套件遇到来自新类的测试时则来自之前的类（如果存在）的 `tearDownClass()` 会被调用，然后再调用来自新类的 `setUpClass()`。

​	类似地如果测试是来自之前的测试的另一个模块则来自之前模块的 `tearDownModule` 将被运行，然后再运行来自新模块的 `setUpModule`。

​	在所有测试运行完毕后最终的 `tearDownClass` 和 `tearDownModule` 将被运行。

​	请注意共享设定不适用于一些 [潜在的] 特性例如测试并行化并且它们会破坏测试隔离。 它们应当被谨慎地使用。

​	由 unittest 测试加载器创建的测试的默认顺序是将所有来自相同模块和类的测试归入相同分组。 这将导致 `setUpClass` / `setUpModule` (等) 对于每个类和模块都恰好被调用一次。 如果你将顺序随机化，以便使得来自不同模块和类的测试彼此相邻，那么这些共享的设定函数就可能会在一次测试运行中被多次调用。

​	共享的设定不适用与非标准顺序的套件。 对于不想支持共享设定的框架来说 `BaseTestSuite` 仍然可用。

​	如果在共享的设定函数中引发了任何异常则测试将被报告错误。 因为没有对应的测试实例，所以会创建一个 `_ErrorHolder` 对象（它具有与 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 相同的接口）来代表该错误。 如果你只是使用标准 unittest 测试运行器那么这个细节并不重要，但是如果你是一个框架开发者那么这可能会有关系。

### setUpClass 和 tearDownClass

​	这些必须被实现为类方法:

```
import unittest

class Test(unittest.TestCase):
    @classmethod
    def setUpClass(cls):
        cls._connection = createExpensiveConnectionObject()

    @classmethod
    def tearDownClass(cls):
        cls._connection.destroy()
```

​	如果你希望在基类上的 `setUpClass` 和 `tearDownClass` 被调用则你必须自己去调用它们。 在 [`TestCase`]({{< ref "/library/development/unittest#unittest.TestCase" >}}) 中的实现是空的。

​	如果在 `setUpClass` 中引发了异常则类中的测试将不会被运行并且 `tearDownClass` 也不会被运行。 跳过的类中的 `setUpClass` 或 `tearDownClass` 将不会被运行。 如果引发的异常是 [`SkipTest`]({{< ref "/library/development/unittest#unittest.SkipTest" >}}) 异常则类将被报告为已跳过而非发生错误。

### setUpModule 和 tearDownModule

​	这些应当被实现为函数:

```
def setUpModule():
    createConnection()

def tearDownModule():
    closeConnection()
```

​	如果在 `setUpModule` 中引发了异常则模块中的任何测试都将不会被运行并且 `tearDownModule` 也不会被运行。 如果引发的异常是 [`SkipTest`]({{< ref "/library/development/unittest#unittest.SkipTest" >}}) 异常则模块将被报告为已跳过而非发生错误。

​	要添加即使在发生异常时也必须运行的清理代码，请使用 `addModuleCleanup`:

## unittest.**addModuleCleanup**(*function*, */*, **args*, ***kwargs*)

​	在 `tearDownModule()` 之后添加一个要调用的函数来清理测试类运行期间所使用的资源。 函数将按它们被添加的相反顺序被调用 (LIFO)。 它们在调用时将附带它们被添加时传给 [`addModuleCleanup()`]({{< ref "/library/development/unittest#unittest.addModuleCleanup" >}}) 的任何参数和关键字参数。

​	如果 `setUpModule()` 失败，即意味着 `tearDownModule()` 未被调用，则已添加的任何清理函数仍将被调用。

> Added in version 3.8.
>

## *classmethod* unittest.**enterModuleContext**(*cm*)

​	进入所提供的 [context manager]({{< ref "/glossary/idx#term-context-manager" >}})。 如果成功，还会将其 [`__exit__()`]({{< ref "/reference/datamodel#object.__exit__" >}}) 方法作为使用 [`addModuleCleanup()`]({{< ref "/library/development/unittest#unittest.addModuleCleanup" >}}) 的清理函数并返回 [`__enter__()`]({{< ref "/reference/datamodel#object.__enter__" >}}) 方法的结果。

> Added in version 3.11.
>

## unittest.**doModuleCleanups**()

​	此函数会在 `tearDownModule()` 之后无条件地被调用，或者如果 `setUpModule()` 引发了异常则会在 `setUpModule()` 之后被调用。

​	它将负责调用由It is responsible for calling all the cleanup functions added by [`addModuleCleanup()`]({{< ref "/library/development/unittest#unittest.addModuleCleanup" >}}) 添加的所有清理函数。 如果你需要在 `tearDownModule()` *之前* 调用清理函数则可以自行调用 [`doModuleCleanups()`]({{< ref "/library/development/unittest#unittest.doModuleCleanups" >}})。

[`doModuleCleanups()`]({{< ref "/library/development/unittest#unittest.doModuleCleanups" >}}) 每次会弹出清理函数栈中的一个方法，因此它可以在任何时候被调用。

> Added in version 3.8.
>

## 信号处理

> Added in version 3.2.
>

​	The [`-c/--catch`]({{< ref "/library/development/unittest#cmdoption-unittest-c" >}}) command-line option to unittest, along with the `catchbreak` parameter to [`unittest.main()`]({{< ref "/library/development/unittest#unittest.main" >}}), provide more friendly handling of control-C during a test run. With catch break behavior enabled control-C will allow the currently running test to complete, and the test run will then end and report all the results so far. A second control-c will raise a [`KeyboardInterrupt`]({{< ref "/library/exceptions#KeyboardInterrupt" >}}) in the usual way.

​	处理 control-C 信号的处理器会尝试与安装了自定义 [`signal.SIGINT`]({{< ref "/library/ipc/signal#signal.SIGINT" >}}) 处理器的测试代码保持兼容。 如果是 `unittest` 处理器而 *不是* 已安装的 [`signal.SIGINT`]({{< ref "/library/ipc/signal#signal.SIGINT" >}}) 处理器被调用，即它被系统在测试的下层替换并委托处理，则它会调用默认的处理器。 这通常会是替换了已安装处理器并委托处理的代码所预期的行为。 对于需要禁用 `unittest` control-C 处理的单个测试则可以使用 [`removeHandler()`]({{< ref "/library/development/unittest#unittest.removeHandler" >}}) 装饰器。

​	还有一些工具函数让框架开发者可以在测试框架内部启用 control-C 处理功能。

## unittest.**installHandler**()

​	安装 control-C 处理器。 当接收到 [`signal.SIGINT`]({{< ref "/library/ipc/signal#signal.SIGINT" >}}) 时（通常是响应用户按下 control-C）所有已注册的结果都会执行 [`stop()`]({{< ref "/library/development/unittest#unittest.TestResult.stop" >}}) 调用。

## unittest.**registerResult**(*result*)

​	注册一个 [`TestResult`]({{< ref "/library/development/unittest#unittest.TestResult" >}}) 对象用于 control-C 的处理。 注册一个结果将保存指向它的弱引用，因此这并不能防止结果被作为垃圾回收。

​	如果 control-C 未被启用则注册 [`TestResult`]({{< ref "/library/development/unittest#unittest.TestResult" >}}) 对象将没有任何附带影响，因此不论是否启用了该项处理测试框架都可以无条件地注册他们独立创建的所有结果。

## unittest.**removeResult**(*result*)

​	移除一个已注册的结果。 一旦结果被移除则 [`stop()`]({{< ref "/library/development/unittest#unittest.TestResult.stop" >}}) 将不再会作为针对 control-C 的响应在结果对象上被调用。

## unittest.**removeHandler**(*function=None*)

​	当不附带任何参数被调用时此函数将移除已被安装的 control-C 处理器。 此函数还可被用作测试装饰器以在测试被执行时临时性地移除处理器:

```
@unittest.removeHandler
def test_signal_handling(self):
    ...
```
