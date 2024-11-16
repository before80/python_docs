+++
title = "importlib --- import 的实现"
date = 2024-11-15T21:19:20+08:00
weight = 40
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/importlib.html](https://docs.python.org/zh-cn/3.13/library/importlib.html)
>
> 收录该文档的时间：`2024-11-15T21:19:20+08:00`

# `importlib` --- `import` 的实现

> Added in version 3.1.
>

**源代码** [Lib/importlib/__init__.py](https://github.com/python/cpython/tree/3.13/Lib/importlib/__init__.py)

------

## 概述

[`importlib`]({{< ref "/library/modules/importlib#module-importlib" >}}) 包具有三重目标。

​	一是在 Python 源代码中提供 [`import`]({{< ref "/reference/simple_stmts#import" >}}) 语句的实现（并且因此而扩展 [`__import__()`]({{< ref "/library/functions#import__" >}}) 函数）。 这提供了一个可移植到任何 Python 解释器的 `import` 实现。 与使用 Python 以外的编程语言实现的方式相比这一实现也更易于理解。

​	第二个目的是实现 [`import`]({{< ref "/reference/simple_stmts#import" >}}) 的部分被公开在这个包中，使得用户更容易创建他们自己的自定义对象 (通常被称为 [importer]({{< ref "/glossary/idx#term-importer" >}})) 来参与到导入过程中。

​	三，这个包也包含了对外公开用于管理 Python 包的各个方面的附加功能的模块:

- [`importlib.metadata`]({{< ref "/library/modules/importlib_metadata#module-importlib.metadata" >}}) 代表对来自第三方发行版的元数据的访问。
- [`importlib.resources`]({{< ref "/library/modules/importlib_resources#module-importlib.resources" >}}) 提供了用于对来自 Python 包的非代码“资源”的访问的例程。

> 参见
>
> 
>
> - [import 语句]({{< ref "/reference/simple_stmts#import" >}})
>
>   [`import`]({{< ref "/reference/simple_stmts#import" >}}) 语句的语言参考
>
> - [包规格说明](https://www.python.org/doc/essays/packages/)
>
>   包的初始规范。自从编写这个文档开始，一些语义已经发生改变了（比如基于 [`sys.modules`]({{< ref "/library/python/sys#sys.modules" >}}) 中 `None` 的重定向）。
>
> - [`__import__()`]({{< ref "/library/modules/importlib#importlib.__import__" >}}) 函数
>
>   [`import`]({{< ref "/reference/simple_stmts#import" >}}) 语句是这个函数的语法糖。
>
> - [sys.path 模块搜索路径的初始化]({{< ref "/library/modules/sys_path_init#sys-path-init" >}})
>
>   [`sys.path`]({{< ref "/library/python/sys#sys.path" >}}) 的初始化。
>
> - [**PEP 235**](https://peps.python.org/pep-0235/)
>
>   在忽略大小写的平台上进行导入
>
> - [**PEP 263**](https://peps.python.org/pep-0263/)
>
>   定义 Python 源代码编码
>
> - [**PEP 302**](https://peps.python.org/pep-0302/)
>
>   新导入钩子
>
> - [**PEP 328**](https://peps.python.org/pep-0328/)
>
>   导入：多行和绝对/相对
>
> - [**PEP 366**](https://peps.python.org/pep-0366/)
>
>   主模块显式相对导入
>
> - [**PEP 420**](https://peps.python.org/pep-0420/)
>
>   隐式命名空间包
>
> - [**PEP 451**](https://peps.python.org/pep-0451/)
>
>   导入系统的一个模块规范类型
>
> - [**PEP 488**](https://peps.python.org/pep-0488/)
>
>   消除PYO文件
>
> - [**PEP 489**](https://peps.python.org/pep-0489/)
>
>   多阶段扩展模块初始化
>
> - [**PEP 552**](https://peps.python.org/pep-0552/)
>
>   确定性的 pyc 文件
>
> - [**PEP 3120**](https://peps.python.org/pep-3120/)
>
>   使用 UTF-8 作为默认的源编码
>
> - [**PEP 3147**](https://peps.python.org/pep-3147/)
>
>   PYC 仓库目录

## 函数

## importlib.`__import__`(*name*, *globals=None*, *locals=None*, *fromlist=()*, *level=0*)

​	内置 [`__import__()`]({{< ref "/library/functions#import__" >}}) 函数的实现。

​备注
 

​	程序式地导入模块应该使用 [`import_module()`]({{< ref "/library/modules/importlib#importlib.import_module" >}}) 而不是这个函数。

## importlib.**import_module**(*name*, *package=None*)

​	导入一个模块。 参数 *name* 指定了以绝对或相对导入方式导入什么模块 (比如要么像这样 `pkg.mod` 或者这样 `..mod`)。 如果参数 name 使用相对导入的方式来指定，那么 *package* 参数必须设置为那个包名，这个包名作为解析这个包名的锚点 (比如 `import_module('..mod', 'pkg.subpkg')` 将会导入 `pkg.mod`)。

[`import_module()`]({{< ref "/library/modules/importlib#importlib.import_module" >}}) 函数是一个对 [`importlib.__import__()`]({{< ref "/library/modules/importlib#importlib.__import__" >}}) 进行简化的包装器。 这意味着该函数的所有语义都来自于 [`importlib.__import__()`]({{< ref "/library/modules/importlib#importlib.__import__" >}})。 这两个函数之间最重要的不同点在于 [`import_module()`]({{< ref "/library/modules/importlib#importlib.import_module" >}}) 返回指定的包或模块 (例如 `pkg.mod`)，而 [`__import__()`]({{< ref "/library/functions#import__" >}}) 返回最高层级的包或模块 (例如 `pkg`)。

​	如果动态导入一个自解释器开始执行以来被创建的模块（即创建了一个 Python 源代码文件），为了让导入系统知道这个新模块，可能需要调用 [`invalidate_caches()`]({{< ref "/library/modules/importlib#importlib.invalidate_caches" >}})。

> 在 3.3 版本发生变更: 父包会被自动导入。

## importlib.**invalidate_caches**()

​	使查找器存储在 [`sys.meta_path`]({{< ref "/library/python/sys#sys.meta_path" >}}) 中的内部缓存无效。如果一个查找器实现了 `invalidate_caches()`，那么它会被调用来执行那个无效过程。 如果创建/安装任何模块，同时正在运行的程序是为了保证所有的查找器知道新模块的存在，那么应该调用这个函数。

> Added in version 3.3.
>

> 在 3.10 版本发生变更: 当注意到相同命名空间已被导入之后在不同 [`sys.path`]({{< ref "/library/python/sys#sys.path" >}}) 位置中创建/安装的命名空间包。

## importlib.**reload**(*module*)

​	重新加载之前导入的 *module*。 那个参数必须是一个模块对象，所以它之前必须已经成功导入了。 这在你已经使用外部编辑器编辑过了那个模块的源代码文件并且想在退出 Python 解释器之前试验这个新版本的模块的时候将很适用。 函数的返回值是那个模块对象（如果重新导入导致一个不同的对象放置在 [`sys.modules`]({{< ref "/library/python/sys#sys.modules" >}}) 中，那么那个模块对象是有可能会不同）。

​	当执行 [`reload()`]({{< ref "/library/modules/importlib#importlib.reload" >}}) 的时候：

- Python 模块的代码会被重新编译并且那个模块级的代码被重新执行，通过重新使用一开始加载那个模块的 [loader]({{< ref "/glossary/idx#term-loader" >}})，定义一个新的绑定在那个模块字典中的名称的对象集合。扩展模块的 `init` 函数不会被调用第二次。
- 与Python中的所有的其它对象一样，旧的对象只有在它们的引用计数为0之后才会被回收。
- 模块命名空间中的名称重新指向任何新的或更改后的对象。
- 其他旧对象的引用（例如那个模块的外部名称）不会被重新绑定到引用的新对象的，并且如果有需要，必须在出现的每个命名空间中进行更新。

​	有一些其他注意事项：

​	当一个模块被重新加载的时候，它的字典（包含了那个模块的全区变量）会被保留。名称的重新定义会覆盖旧的定义，所以通常来说这不是问题。如果一个新模块没有定义在旧版本模块中定义的名称，则将保留旧版本中的定义。这一特性可用于作为那个模块的优点，如果它维护一个全局表或者对象的缓存 —— 使用 [`try`]({{< ref "/reference/compound_stmts#try" >}}) 语句，就可以测试表的存在并且跳过它的初始化，如果有需要的话:

```
try:
    cache
except NameError:
    cache = {}
```

​	重新加载内置的或者动态加载模块，通常来说不是很有用处。不推荐重新加载"[`sys`]({{< ref "/library/python/sys#module-sys" >}})，[`__main__`]({{< ref "/library/python/__main__#module-__main__" >}})，[`builtins`]({{< ref "/library/python/builtins#module-builtins" >}}) 和其它关键模块。在很多例子中，扩展模块并不是设计为不止一次的初始化，并且当重新加载时，可能会以任意方式失败。

​	如果一个模块使用 [`from`]({{< ref "/reference/simple_stmts#from" >}}) ... [`import`]({{< ref "/reference/simple_stmts#import" >}}) ... 导入的对象来自另外一个模块，给其它模块调用 [`reload()`]({{< ref "/library/modules/importlib#importlib.reload" >}}) 不会重新定义来自这个模块的对象 —— 解决这个问题的一种方式是重新执行 `from` 语句，另一种方式是使用 `import` 和限定名称(*module.name*)来代替。

​	如果一个模块创建一个类的实例，重新加载定义那个类的模块不影响那些实例的方法定义———它们继续使用旧类中的定义。对于子类来说同样是正确的。

> Added in version 3.4.
>

> 在 3.7 版本发生变更: 如果重新加载的模块缺少 [`ModuleSpec`]({{< ref "/library/modules/importlib#importlib.machinery.ModuleSpec" >}}) ，则会触发 [`ModuleNotFoundError`]({{< ref "/library/exceptions#ModuleNotFoundError" >}}) 。



## [`importlib.abc`]({{< ref "/library/modules/importlib#module-importlib.abc" >}}) —— 关于导入的抽象基类

**源代码：** [Lib/importlib/abc.py](https://github.com/python/cpython/tree/3.13/Lib/importlib/abc.py)

------

[`importlib.abc`]({{< ref "/library/modules/importlib#module-importlib.abc" >}}) 模块包含了 [`import`]({{< ref "/reference/simple_stmts#import" >}}) 使用到的所有核心抽象基类。在实现核心的 ABCs 中，核心抽象基类的一些子类也提供了帮助。

​	ABC 类的层次结构：

```
object
 +-- MetaPathFinder
 +-- PathEntryFinder
 +-- Loader
      +-- ResourceLoader --------+
      +-- InspectLoader          |
           +-- ExecutionLoader --+
                                 +-- FileLoader
                                 +-- SourceLoader
```

## *class* importlib.abc.**MetaPathFinder**

​	一个代表 [meta path finder]({{< ref "/glossary/idx#term-meta-path-finder" >}}) 的抽象基类。

> Added in version 3.3.
>

> 在 3.10 版本发生变更: 不再是 `Finder` 的子类。

## **find_spec**(*fullname*, *path*, *target=None*)

​	An abstract method for finding a [spec]({{< ref "/glossary/idx#term-module-spec" >}}) for the specified module. If this is a top-level import, *path* will be `None`. Otherwise, this is a search for a subpackage or module and *path* will be the value of [`__path__`]({{< ref "/reference/datamodel#module.__path__" >}}) from the parent package. If a spec cannot be found, `None` is returned. When passed in, `target` is a module object that the finder may use to make a more educated guess about what spec to return. [`importlib.util.spec_from_loader()`]({{< ref "/library/modules/importlib#importlib.util.spec_from_loader" >}}) may be useful for implementing concrete `MetaPathFinders`.

> Added in version 3.4.
>

## **invalidate_caches**()

​	当被调用的时候，一个可选的方法应该将查找器使用的任何内部缓存进行无效。将在 [`sys.meta_path`]({{< ref "/library/python/sys#sys.meta_path" >}}) 上的所有查找器的缓存进行无效的时候，这个函数被 [`importlib.invalidate_caches()`]({{< ref "/library/modules/importlib#importlib.invalidate_caches" >}}) 所使用。

> 在 3.4 版本发生变更: 当被调用时将返回 `None` 而不是 [`NotImplemented`]({{< ref "/library/constants#NotImplemented" >}})。

## *class* importlib.abc.**PathEntryFinder**

​	一个抽象基类，代表 [path entry finder]({{< ref "/glossary/idx#term-path-entry-finder" >}})。虽然与 [`MetaPathFinder`]({{< ref "/library/modules/importlib#importlib.abc.MetaPathFinder" >}}) 有些相似之处，但 PathEntryFinder 仅用于 [`importlib.machinery.PathFinder`]({{< ref "/library/modules/importlib#importlib.machinery.PathFinder" >}}) 提供的基于路径的导入子系统中。

> Added in version 3.3.
>

> 在 3.10 版本发生变更: 不再是 `Finder` 的子类。

## **find_spec**(*fullname*, *target=None*)

​	一个抽象方法，用于查找指定模块的 [spec]({{< ref "/glossary/idx#term-module-spec" >}})。搜索器将只在指定的 [path entry]({{< ref "/glossary/idx#term-path-entry" >}}) 内搜索该模块。找不到则会返回 `None`。在实现具体的 `PathEntryFinders` 代码时，可能会用到 [`importlib.util.spec_from_loader()`]({{< ref "/library/modules/importlib#importlib.util.spec_from_loader" >}}) 。

> Added in version 3.4.
>

## **invalidate_caches**()

​	可选方法，调用后应让查找器用到的所有内部缓存失效。要让所有缓存的查找器的缓存无效时，可供 [`importlib.machinery.PathFinder.invalidate_caches()`]({{< ref "/library/modules/importlib#importlib.machinery.PathFinder.invalidate_caches" >}}) 调用。

## *class* importlib.abc.**Loader**

[loader]({{< ref "/glossary/idx#term-loader" >}}) 的抽象基类。 关于一个加载器的实际定义请查看 [**PEP 302**](https://peps.python.org/pep-0302/)。

​	想要支持资源读取的加载器应当实现 [`importlib.resources.abc.ResourceReader`]({{< ref "/library/modules/importlib_resources_abc#importlib.resources.abc.ResourceReader" >}}) 所规定的 `get_resource_reader()` 方法。

> 在 3.7 版本发生变更: 引入了可选的 `get_resource_reader()` 方法。

## **create_module**(*spec*)

​	当导入一个模块的时候，一个返回将要使用的那个模块对象的方法。这个方法可能返回 `None` ，这暗示着应该发生默认的模块创建语义。"

> Added in version 3.4.
>

> 在 3.6 版本发生变更: 当 [`exec_module()`]({{< ref "/library/modules/importlib#importlib.abc.Loader.exec_module" >}}) 已定义时此方法将不再是可选项。

## **exec_module**(*module*)

​	当一个模块被导入或重新加载时在自己的命名空间中执行该模块的的抽象方法。 该模块在 [`exec_module()`]({{< ref "/library/modules/importlib#importlib.abc.Loader.exec_module" >}}) 被调用时应该已经被初始化了。 当此方法存在时，必须要定义 [`create_module()`]({{< ref "/library/modules/importlib#importlib.abc.Loader.create_module" >}})。

> Added in version 3.4.
>

> 在 3.6 版本发生变更: [`create_module()`]({{< ref "/library/modules/importlib#importlib.abc.Loader.create_module" >}}) 也必须要定义。

## **load_module**(*fullname*)

​	用于加载模块的传统方法。 如果模块无法被导入，则会引发 [`ImportError`]({{< ref "/library/exceptions#ImportError" >}})，在其他情况下将返回被加载的模块。

​	如果请求的模块已存在于 [`sys.modules`]({{< ref "/library/python/sys#sys.modules" >}}) 中，则该模块应当被使用并重新加载。 在其他情况下加载器应当创建一个新模块并在任何加载操作开始之前将其插入到 [`sys.modules`]({{< ref "/library/python/sys#sys.modules" >}}) 中，以防止来自导入的无限递归。 如果加载器插入了一个模块并且加载失败，则必须用加载器将其从 [`sys.modules`]({{< ref "/library/python/sys#sys.modules" >}}) 中移除；在加载器开始执行之前已经存在于 [`sys.modules`]({{< ref "/library/python/sys#sys.modules" >}}) 中的模块应当保持原样。

​	加载器应当在模块上设置几个属性（请注意在模块被重新加载时这些属性有几个可能发生改变）:

- [`module.__name__`]({{< ref "/reference/datamodel#module.__name__" >}})
- [`module.__file__`]({{< ref "/reference/datamodel#module.__file__" >}})
- [`module.__cached__`]({{< ref "/reference/datamodel#module.__cached__" >}}) *(已弃用)*
- [`module.__path__`]({{< ref "/reference/datamodel#module.__path__" >}})
- [`module.__package__`]({{< ref "/reference/datamodel#module.__package__" >}}) *(已弃用)*
- [`module.__loader__`]({{< ref "/reference/datamodel#module.__loader__" >}}) *(已弃用)*

​	当 [`exec_module()`]({{< ref "/library/modules/importlib#importlib.abc.Loader.exec_module" >}}) 可用的时候，那么则提供了向后兼容的功能。

> 在 3.4 版本发生变更: 当被调用时将引发 [`ImportError`]({{< ref "/library/exceptions#ImportError" >}}) 而不是 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}})。 在 [`exec_module()`]({{< ref "/library/modules/importlib#importlib.abc.Loader.exec_module" >}}) 可用时提供的功能。

> 自 3.4 版本弃用: 用于加载模块的推荐 API 是 [`exec_module()`]({{< ref "/library/modules/importlib#importlib.abc.Loader.exec_module" >}}) (和 [`create_module()`]({{< ref "/library/modules/importlib#importlib.abc.Loader.create_module" >}}))。 加载器应该实现它而不是 [`load_module()`]({{< ref "/library/modules/importlib#importlib.abc.Loader.load_module" >}})。 当实现了 [`exec_module()`]({{< ref "/library/modules/importlib#importlib.abc.Loader.exec_module" >}}) 时导入机制将会承担 [`load_module()`]({{< ref "/library/modules/importlib#importlib.abc.Loader.load_module" >}}) 的所有其他责任。

## *class* importlib.abc.**ResourceLoader**

​	一个 [loader]({{< ref "/glossary/idx#term-loader" >}}) 的抽象基类，它实现了可选的 [**PEP 302**](https://peps.python.org/pep-0302/) 协议用于从存储后端加载任意资源。

> 自 3.7 版本弃用: 这个 ABC 已被弃用并转为通过 [`importlib.resources.abc.ResourceReader`]({{< ref "/library/modules/importlib_resources_abc#importlib.resources.abc.ResourceReader" >}}) 来支持资源加载。

## *abstractmethod* **get_data**(*path*)

​	An abstract method to return the bytes for the data located at *path*. Loaders that have a file-like storage back-end that allows storing arbitrary data can implement this abstract method to give direct access to the data stored. [`OSError`]({{< ref "/library/exceptions#OSError" >}}) is to be raised if the *path* cannot be found. The *path* is expected to be constructed using a module's [`__file__`]({{< ref "/reference/datamodel#module.__file__" >}}) attribute or an item from a package's [`__path__`]({{< ref "/reference/datamodel#module.__path__" >}}).

> 在 3.4 版本发生变更: 引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 异常而不是 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}}) 异常。

## *class* importlib.abc.**InspectLoader**

​	一个实现加载器检查模块可选的 [**PEP 302**](https://peps.python.org/pep-0302/) 协议的 [loader]({{< ref "/glossary/idx#term-loader" >}}) 的抽象基类。

## **get_code**(*fullname*)

​	返回一个模块的代码对象，或如果模块没有一个代码对象（例如，对于内置的模块来说，这会是这种情况），则为 `None`。 如果加载器不能找到请求的模块，则引发 [`ImportError`]({{< ref "/library/exceptions#ImportError" >}}) 异常。

​备注
 

​	当这个方法有一个默认的实现的时候，出于性能方面的考虑，如果有可能的话，建议覆盖它。

> 在 3.4 版本发生变更: 不再抽象并且提供一个具体的实现。

## *abstractmethod* **get_source**(*fullname*)

​	一个返回模块源的抽象方法。使用 [universal newlines]({{< ref "/glossary/idx#term-universal-newlines" >}}) 作为文本字符串被返回，将所有可识别行分割符翻译成 `'\n'` 字符。 如果没有可用的源（例如，一个内置模块），则返回 `None`。 如果加载器不能找到指定的模块，则引发 [`ImportError`]({{< ref "/library/exceptions#ImportError" >}}) 异常。

> 在 3.4 版本发生变更: 引发 [`ImportError`]({{< ref "/library/exceptions#ImportError" >}}) 而不是 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}})。

## **is_package**(*fullname*)

​	可选方法，如果模块为包，则返回 True，否则返回 False。 如果 [loader]({{< ref "/glossary/idx#term-loader" >}}) 找不到模块，则会触发 [`ImportError`]({{< ref "/library/exceptions#ImportError" >}})。

> 在 3.4 版本发生变更: 引发 [`ImportError`]({{< ref "/library/exceptions#ImportError" >}}) 而不是 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}})。

## *static* **source_to_code**(*data*, *path='<string>'*)

​	创建一个来自Python源码的代码对象。

​	参数 *data* 可以是任意 [`compile()`]({{< ref "/library/functions#compile" >}}) 函数支持的类型（例如字符串或字节串）。 参数 *path* 应该是源代码来源的路径，这可能是一个抽象概念（例如位于一个 zip 文件中）。

​	在有后续代码对象的情况下，可以在一个模块中通过运行 `exec(code, module.__dict__)` 来执行它。

> Added in version 3.4.
>

> 在 3.5 版本发生变更: 使得这个方法变成静态的。

## **exec_module**(*module*)

[`Loader.exec_module()`]({{< ref "/library/modules/importlib#importlib.abc.Loader.exec_module" >}}) 的实现。

> Added in version 3.4.
>

## **load_module**(*fullname*)

[`Loader.load_module()`]({{< ref "/library/modules/importlib#importlib.abc.Loader.load_module" >}}) 的实现。

> 自 3.4 版本弃用: 使用 [`exec_module()`]({{< ref "/library/modules/importlib#importlib.abc.InspectLoader.exec_module" >}}) 来代替。

## *class* importlib.abc.**ExecutionLoader**

​	一个继承自 [`InspectLoader`]({{< ref "/library/modules/importlib#importlib.abc.InspectLoader" >}}) 的抽象基类，当被实现时，帮助一个模块作为脚本来执行。 这个抽象基类表示可选的 [**PEP 302**](https://peps.python.org/pep-0302/) 协议。

## *abstractmethod* **get_filename**(*fullname*)

​	An abstract method that is to return the value of [`__file__`]({{< ref "/reference/datamodel#module.__file__" >}}) for the specified module. If no path is available, [`ImportError`]({{< ref "/library/exceptions#ImportError" >}}) is raised.

​	如果源代码可用，那么这个方法返回源文件的路径，不管是否是用来加载模块的字节码。

> 在 3.4 版本发生变更: 引发 [`ImportError`]({{< ref "/library/exceptions#ImportError" >}}) 而不是 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}})。

## *class* importlib.abc.**FileLoader**(*fullname*, *path*)

​	一个继承自 [`ResourceLoader`]({{< ref "/library/modules/importlib#importlib.abc.ResourceLoader" >}}) 和 [`ExecutionLoader`]({{< ref "/library/modules/importlib#importlib.abc.ExecutionLoader" >}})，提供 [`ResourceLoader.get_data()`]({{< ref "/library/modules/importlib#importlib.abc.ResourceLoader.get_data" >}}) 和 [`ExecutionLoader.get_filename()`]({{< ref "/library/modules/importlib#importlib.abc.ExecutionLoader.get_filename" >}}) 具体实现的抽象基类。

​	参数 *fullname* 是加载器要处理的模块的完全解析的名字。参数 *path* 是模块文件的路径。

> Added in version 3.3.
>

## **name**

​	加载器可以处理的模块的名字。

## **path**

​	模块的文件路径

## **load_module**(*fullname*)

​	调用super的 `load_module()`。

> 自 3.4 版本弃用: 使用 [`Loader.exec_module()`]({{< ref "/library/modules/importlib#importlib.abc.Loader.exec_module" >}}) 来代替。

## *abstractmethod* **get_filename**(*fullname*)

​	返回 [`path`]({{< ref "/library/modules/importlib#importlib.abc.FileLoader.path" >}})。

## *abstractmethod* **get_data**(*path*)

​	读取 *path* 作为二进制文件并且返回来自它的字节数据。

## *class* importlib.abc.**SourceLoader**

​	一个用于实现源文件（和可选地字节码）加载的抽象基类。这个类继承自 [`ResourceLoader`]({{< ref "/library/modules/importlib#importlib.abc.ResourceLoader" >}}) 和 [`ExecutionLoader`]({{< ref "/library/modules/importlib#importlib.abc.ExecutionLoader" >}})，需要实现：

- [`ResourceLoader.get_data()`]({{< ref "/library/modules/importlib#importlib.abc.ResourceLoader.get_data" >}})

- ## [`ExecutionLoader.get_filename()`]({{< ref "/library/modules/importlib#importlib.abc.ExecutionLoader.get_filename" >}})

  ​	应该是只返回源文件的路径；不支持无源加载。

​	由这个类定义的抽象方法用来添加可选的字节码文件支持。不实现这些可选的方法（或导致它们引发 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}}) 异常）导致这个加载器只能与源代码一起工作。 实现这些方法允许加载器能与源 *和* 字节码文件一起工作。不允许只提供字节码的 *无源式* 加载。字节码文件是通过移除 Python 编译器的解析步骤来加速加载的优化，并且因此没有开放出字节码专用的 API。

## **path_stats**(*path*)

​	返回一个包含关于指定路径的元数据的 [`dict`]({{< ref "/library/stdtypes#dict" >}}) 的可选的抽象方法。 支持的字典键有：

- `'mtime'` (必选项): 一个表示源码修改时间的整数或浮点数；
- `'size'` (可选项)：源码的字节大小。

​	字典中任何其他键会被忽略，以允许将来的扩展。 如果不能处理该路径，则会引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}})。

> Added in version 3.3.
>

> 在 3.4 版本发生变更: 引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 而不是 [`NotImplemented`]({{< ref "/library/constants#NotImplemented" >}})。

## **path_mtime**(*path*)

​	返回指定文件路径修改时间的可选的抽象方法。

> 自 3.3 版本弃用: 在有了 [`path_stats()`]({{< ref "/library/modules/importlib#importlib.abc.SourceLoader.path_stats" >}}) 的情况下，这个方法被弃用了。 没必要去实现它了，但是为了兼容性，它依然处于可用状态。 如果文件路径不能被处理，则引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 异常。

> 在 3.4 版本发生变更: 引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 而不是 [`NotImplemented`]({{< ref "/library/constants#NotImplemented" >}})。

## **set_data**(*path*, *data*)

​	往一个文件路径写入指定字节的的可选的抽象方法。任何中间不存在的目录不会被自动创建。

​	当对路径的写入因路径为只读而失败时 ([`errno.EACCES`]({{< ref "/library/allos/errno#errno.EACCES" >}})/[`PermissionError`]({{< ref "/library/exceptions#PermissionError" >}}))，不会传播异常。

> 在 3.4 版本发生变更: 当被调用时，不再引起 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}}) 异常。

## **get_code**(*fullname*)

[`InspectLoader.get_code()`]({{< ref "/library/modules/importlib#importlib.abc.InspectLoader.get_code" >}}) 的具体实现。

## **exec_module**(*module*)

[`Loader.exec_module()`]({{< ref "/library/modules/importlib#importlib.abc.Loader.exec_module" >}}) 的具体实现。

> Added in version 3.4.
>

## **load_module**(*fullname*)

​	Concrete implementation of [`Loader.load_module()`]({{< ref "/library/modules/importlib#importlib.abc.Loader.load_module" >}}).

> 自 3.4 版本弃用: 使用 [`exec_module()`]({{< ref "/library/modules/importlib#importlib.abc.SourceLoader.exec_module" >}}) 来代替。

## **get_source**(*fullname*)

[`InspectLoader.get_source()`]({{< ref "/library/modules/importlib#importlib.abc.InspectLoader.get_source" >}}) 的具体实现。

## **is_package**(*fullname*)

[`InspectLoader.is_package()`]({{< ref "/library/modules/importlib#importlib.abc.InspectLoader.is_package" >}}) 的具体实现。一个模块被确定为一个包的条件是：它的文件路径（由 [`ExecutionLoader.get_filename()`]({{< ref "/library/modules/importlib#importlib.abc.ExecutionLoader.get_filename" >}}) 提供）当文件扩展名被移除时是一个命名为 `__init__` 的文件，**并且** 这个模块名字本身不是以 `__init__` 结束。

## *class* importlib.abc.**ResourceReader**

*被 TraversableResources* 取代

​	提供读取 *resources* 能力的一个 [abstract base class]({{< ref "/glossary/idx#term-abstract-base-class" >}}) 。

​	从这个 ABC 的视角出发，*resource* 指一个包附带的二进制文件。常见的如在包的 `__init__.py` 文件旁的数据文件。这个类存在的目的是为了将对数据文件的访问进行抽象，这样包就和其数据文件的存储方式无关了。不论这些文件是存放在一个 zip 文件里还是直接在文件系统内。

​	对于该类中的任一方法，*resource* 参数的值都需要是一个在概念上表示文件名称的 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。 这意味着任何子目录的路径都不该出现在 *resouce* 参数值内。 因为对于阅读器而言，包的位置就代表着「目录」。 因此目录和文件名就分别对应于包和资源。 这也是该类的实例都需要和一个包直接关联（而不是潜在指代很多包或者一整个模块）的原因。

​	想支持资源读取的加载器需要提供一个返回实现了此 ABC 的接口的 `get_resource_reader(fullname)` 方法。如果通过全名指定的模块不是一个包，这个方法应该返回 [`None`]({{< ref "/library/constants#None" >}})。 当指定的模块是一个包时，应该只返回一个与这个抽象类ABC兼容的对象。

> Added in version 3.7.
>

*Deprecated since version 3.12, will be removed in version 3.14:* 使用 [`importlib.resources.abc.TraversableResources`]({{< ref "/library/modules/importlib_resources_abc#importlib.resources.abc.TraversableResources" >}}) 代替。

## *abstractmethod* **open_resource**(*resource*)

​	返回一个打开的 [file-like object]({{< ref "/glossary/idx#term-file-like-object" >}}) 用于 *resource* 的二进制读取。

​	如果无法找到资源，将会引发 [`FileNotFoundError`]({{< ref "/library/exceptions#FileNotFoundError" >}})。

## *abstractmethod* **resource_path**(*resource*)

​	返回 *resource* 的文件系统路径。

​	如果资源并不实际存在于文件系统中，将会引发 [`FileNotFoundError`]({{< ref "/library/exceptions#FileNotFoundError" >}})。

## *abstractmethod* **is_resource**(*name*)

​	如果 *name* 被视作资源，则返回True。如果 *name* 不存在，则引发 [`FileNotFoundError`]({{< ref "/library/exceptions#FileNotFoundError" >}}) 异常。

## *abstractmethod* **contents**()

​	反回由字符串组成的 [iterable]({{< ref "/glossary/idx#term-iterable" >}})，表示这个包的所有内容。 请注意并不要求迭代器返回的所有名称都是实际的资源，例如返回 [`is_resource()`]({{< ref "/library/modules/importlib#importlib.abc.ResourceReader.is_resource" >}}) 为假值的名称也是可接受的。

​	允许非资源名字被返回是为了允许存储的一个包和它的资源的方式是已知先验的并且非资源名字会有用的情况。比如，允许返回子目录名字，目的是当得知包和资源存储在文件系统上面的时候，能够直接使用子目录的名字。

​	这个抽象方法返回了一个不包含任何内容的可迭代对象。

## *class* importlib.abc.**Traversable**

​	一个具有 [`pathlib.Path`]({{< ref "/library/filesys/pathlib#pathlib.Path" >}}) 中方法的子集并适用于遍历目录和打开文件的对象。

​	对于该对象在文件系统中的表示形式，请使用 [`importlib.resources.as_file()`]({{< ref "/library/modules/importlib_resources#importlib.resources.as_file" >}})。

> Added in version 3.9.
>

*Deprecated since version 3.12, will be removed in version 3.14:* 使用 [`importlib.resources.abc.Traversable`]({{< ref "/library/modules/importlib_resources_abc#importlib.resources.abc.Traversable" >}}) 代替。

## **name**

​	抽象属性。 此对象的不带任何父引用的基本名称。

## *abstractmethod* **iterdir**()

​	产出 `self` 中的 `Traversable` 对象。

## *abstractmethod* **is_dir**()

​	如果 `self` 是一个目录则返回 `True`。

## *abstractmethod* **is_file**()

​	如果 `self` 是一个文件则返回 `True`。

## *abstractmethod* **joinpath**(*child*)

​	返回 `self` 中的 Traversable 子对象。

## *abstractmethod* `__truediv__`(*child*)

​	返回 `self` 中的 `Traversable` 子对象。

## *abstractmethod* **open**(*mode='r'*, **args*, ***kwargs*)

*mode* 可以为 'r' 或 'rb' 即以文本或二进制模式打开。 返回一个适用于读取的句柄（与 [`pathlib.Path.open`]({{< ref "/library/filesys/pathlib#pathlib.Path.open" >}}) 样同）。

​	当以文本模式打开时，接受与 [`io.TextIOWrapper`]({{< ref "/library/allos/io#io.TextIOWrapper" >}}) 所接受的相同的编码格式形参。

## **read_bytes**()

​	以字节串形式读取 `self` 的内容。

## **read_text**(*encoding=None*)

​	以文本形式读取 `self` 的内容。

## *class* importlib.abc.**TraversableResources**

​	针对能够为 [`importlib.resources.files()`]({{< ref "/library/modules/importlib_resources#importlib.resources.files" >}}) 接口提供服务的资源读取器的抽象基类。 子类化 [`importlib.resources.abc.ResourceReader`]({{< ref "/library/modules/importlib_resources_abc#importlib.resources.abc.ResourceReader" >}}) 并为 [`importlib.resources.abc.ResourceReader`]({{< ref "/library/modules/importlib_resources_abc#importlib.resources.abc.ResourceReader" >}}) 的抽象方法提供具体实现。 因此，任何提供了 [`importlib.abc.TraversableResources`]({{< ref "/library/modules/importlib#importlib.abc.TraversableResources" >}}) 的加载器也会提供 ResourceReader。

​	需要支持资源读取的加载器应实现此接口。

> Added in version 3.9.
>

*Deprecated since version 3.12, will be removed in version 3.14:* 使用 [`importlib.resources.abc.TraversableResources`]({{< ref "/library/modules/importlib_resources_abc#importlib.resources.abc.TraversableResources" >}}) 代替。

## *abstractmethod* **files**()

​	为载入的包返回一个 [`importlib.resources.abc.Traversable`]({{< ref "/library/modules/importlib_resources_abc#importlib.resources.abc.Traversable" >}}) 对象。



## [`importlib.machinery`]({{< ref "/library/modules/importlib#module-importlib.machinery" >}}) —— 导入器和路径钩子函数。

**源代码：** [Lib/importlib/machinery.py](https://github.com/python/cpython/tree/3.13/Lib/importlib/machinery.py)

------

​	本模块包含多个对象，以帮助 [`import`]({{< ref "/reference/simple_stmts#import" >}}) 查找并加载模块。

## importlib.machinery.**SOURCE_SUFFIXES**

​	一个字符串列表，表示源模块的可识别的文件后缀。

> Added in version 3.3.
>

## importlib.machinery.**DEBUG_BYTECODE_SUFFIXES**

​	一个字符串列表，表示未经优化字节码模块的文件后缀。

> Added in version 3.3.
>

> 自 3.5 版本弃用: 改用 [`BYTECODE_SUFFIXES`]({{< ref "/library/modules/importlib#importlib.machinery.BYTECODE_SUFFIXES" >}}) 。

## importlib.machinery.**OPTIMIZED_BYTECODE_SUFFIXES**

​	一个字符串列表，表示已优化字节码模块的文件后缀。

> Added in version 3.3.
>

> 自 3.5 版本弃用: 改用 [`BYTECODE_SUFFIXES`]({{< ref "/library/modules/importlib#importlib.machinery.BYTECODE_SUFFIXES" >}}) 。

## importlib.machinery.**BYTECODE_SUFFIXES**

​	一个字符串列表，表示字节码模块的可识别的文件后缀（包含前导的句点符号）。

> Added in version 3.3.
>

> 在 3.5 版本发生变更: 该值不再依赖于 `__debug__` 。

## importlib.machinery.**EXTENSION_SUFFIXES**

​	一个字符串列表，表示扩展模块的可识别的文件后缀。

> Added in version 3.3.
>

## importlib.machinery.**all_suffixes**()

​	返回字符串的组合列表，代表标准导入机制可识别模块的所有文件后缀。这是个助手函数，只需知道某个文件系统路径是否会指向模块，而不需要任何关于模块种类的细节（例如 [`inspect.getmodulename()`]({{< ref "/library/python/inspect#inspect.getmodulename" >}})）。

> Added in version 3.3.
>

## *class* importlib.machinery.**BuiltinImporter**

​	用于导入内置模块的 [importer]({{< ref "/glossary/idx#term-importer" >}})。 所有已知的内置模块都已列入 [`sys.builtin_module_names`]({{< ref "/library/python/sys#sys.builtin_module_names" >}})。 此类实现了 [`importlib.abc.MetaPathFinder`]({{< ref "/library/modules/importlib#importlib.abc.MetaPathFinder" >}}) 和 [`importlib.abc.InspectLoader`]({{< ref "/library/modules/importlib#importlib.abc.InspectLoader" >}}) 抽象基类。

​	此类只定义类的方法，以减轻实例化的开销。

> 在 3.5 版本发生变更: 作为 [**PEP 489**](https://peps.python.org/pep-0489/) 的一部分，现在内置模块导入器实现了 `Loader.create_module()` 和 `Loader.exec_module()`。

## *class* importlib.machinery.**FrozenImporter**

​	用于已冻结模块的 [importer]({{< ref "/glossary/idx#term-importer" >}})。 此类实现了 [`importlib.abc.MetaPathFinder`]({{< ref "/library/modules/importlib#importlib.abc.MetaPathFinder" >}}) 和 [`importlib.abc.InspectLoader`]({{< ref "/library/modules/importlib#importlib.abc.InspectLoader" >}}) 抽象基类。

​	此类只定义类的方法，以减轻实例化的开销。

> 在 3.4 版本发生变更: 有了 `create_module()` 和 `exec_module()` 方法。

## *class* importlib.machinery.**WindowsRegistryFinder**

[Finder]({{< ref "/glossary/idx#term-finder" >}}) 用于查找在 Windows 注册表中声明的模块。该类实现了基础的 [`importlib.abc.MetaPathFinder`]({{< ref "/library/modules/importlib#importlib.abc.MetaPathFinder" >}}) 。

​	此类只定义类的方法，以减轻实例化的开销。

> Added in version 3.3.
>

> 自 3.6 版本弃用: 改用 [`site`]({{< ref "/library/python/site#module-site" >}}) 配置。未来版本的 Python 可能不会默认启用该查找器。

## *class* importlib.machinery.**PathFinder**

​	用于 [`sys.path`]({{< ref "/library/python/sys#sys.path" >}}) 和包的 `__path__` 属性的 [Finder]({{< ref "/glossary/idx#term-finder" >}}) 。该类实现了基础的 [`importlib.abc.MetaPathFinder`]({{< ref "/library/modules/importlib#importlib.abc.MetaPathFinder" >}})。

​	此类只定义类的方法，以减轻实例化的开销。

## *classmethod* **find_spec**(*fullname*, *path=None*, *target=None*)

​	类方法试图在 [`sys.path`]({{< ref "/library/python/sys#sys.path" >}}) 或 *path* 上为 *fullname* 指定的模块查找 [spec]({{< ref "/glossary/idx#term-module-spec" >}})。对于每个路径条目，都会查看 [`sys.path_importer_cache`]({{< ref "/library/python/sys#sys.path_importer_cache" >}}) 。如果找到非 False 的对象，则将其用作 [path entry finder]({{< ref "/glossary/idx#term-path-entry-finder" >}}) 来查找要搜索的模块。如果在 [`sys.path_importer_cache`]({{< ref "/library/python/sys#sys.path_importer_cache" >}}) 中没有找到条目，那会在 [`sys.path_hooks`]({{< ref "/library/python/sys#sys.path_hooks" >}}) 检索该路径条目的查找器，找到了则和查到的模块信息一起存入 [`sys.path_importer_cache`]({{< ref "/library/python/sys#sys.path_importer_cache" >}}) 。如果查找器没有找到，则缓存中的查找器和模块信息都存为 `None` ，然后返回。

> Added in version 3.4.
>

> 在 3.5 版本发生变更: 如果当前工作目录不再有效（用空字符串表示），则返回 `None`，但在 [`sys.path_importer_cache`]({{< ref "/library/python/sys#sys.path_importer_cache" >}}) 中不会有缓存值。

## *classmethod* **invalidate_caches**()

​	为所有存于 [`sys.path_importer_cache`]({{< ref "/library/python/sys#sys.path_importer_cache" >}}) 中的查找器，调用其 [`importlib.abc.PathEntryFinder.invalidate_caches()`]({{< ref "/library/modules/importlib#importlib.abc.PathEntryFinder.invalidate_caches" >}}) 方法。 [`sys.path_importer_cache`]({{< ref "/library/python/sys#sys.path_importer_cache" >}}) 中为 `None` 的条目将被删除。

> 在 3.7 版本发生变更: [`sys.path_importer_cache`]({{< ref "/library/python/sys#sys.path_importer_cache" >}}) 中为 `None` 的条目将被删除。

> 在 3.4 版本发生变更: 调用 [`sys.path_hooks`]({{< ref "/library/python/sys#sys.path_hooks" >}}) 中的对象，当前工作目录为 `''` (即空字符串)。

## *class* importlib.machinery.**FileFinder**(*path*, **loader_details*)

[`importlib.abc.PathEntryFinder`]({{< ref "/library/modules/importlib#importlib.abc.PathEntryFinder" >}}) 的一个具体实现，它会缓存来自文件系统的结果。

​	参数 *path* 是查找器负责搜索的目录。

*loader_details* 参数是数量不定的二元组，每个元组包含加载器及其可识别的文件后缀列表。加载器应为可调用对象，可接受两个参数，即模块的名称和已找到文件的路径。

​	查找器将按需对目录内容进行缓存，通过对每个模块的检索进行状态统计，验证缓存是否过期。因为缓存的滞后性依赖于操作系统文件系统状态信息的粒度，所以搜索模块、新建文件、然后搜索新文件代表的模块，这会存在竞争状态。如果这些操作的频率太快，甚至小于状态统计的粒度，那么模块搜索将会失败。为了防止这种情况发生，在动态创建模块时，请确保调用 [`importlib.invalidate_caches()`]({{< ref "/library/modules/importlib#importlib.invalidate_caches" >}})。

> Added in version 3.3.
>

## **path**

​	查找器将要搜索的路径。

## **find_spec**(*fullname*, *target=None*)

​	尝试在 [`path`]({{< ref "/library/modules/importlib#importlib.machinery.FileFinder.path" >}}) 中找到处理 *fullname* 的规格。

> Added in version 3.4.
>

## **invalidate_caches**()

​	清理内部缓存。

## *classmethod* **path_hook**(**loader_details*)

​	一个类方法，返回供 [`sys.path_hooks`]({{< ref "/library/python/sys#sys.path_hooks" >}}) 使用的闭包。 使用直接提供给闭包的路径参数和间接提供的 *loader_details* 闭包将返回一个 [`FileFinder`]({{< ref "/library/modules/importlib#importlib.machinery.FileFinder" >}}) 的实例。

​	如果给闭包的参数不是已存在的目录，将会触发 [`ImportError`]({{< ref "/library/exceptions#ImportError" >}})。

## *class* importlib.machinery.**SourceFileLoader**(*fullname*, *path*)

[`importlib.abc.SourceLoader`]({{< ref "/library/modules/importlib#importlib.abc.SourceLoader" >}}) 的一个具体实现，该实现子类化了 [`importlib.abc.FileLoader`]({{< ref "/library/modules/importlib#importlib.abc.FileLoader" >}}) 并提供了其他一些方法的具体实现。

> Added in version 3.3.
>

## **name**

​	该加载器将要处理的模块名称。

## **path**

​	源文件的路径

## **is_package**(*fullname*)

​	如果 [`path`]({{< ref "/library/modules/importlib#importlib.machinery.SourceFileLoader.path" >}}) 看似包的路径，则返回 `True`。

## **path_stats**(*path*)

[`importlib.abc.SourceLoader.path_stats()`]({{< ref "/library/modules/importlib#importlib.abc.SourceLoader.path_stats" >}}) 的具体代码实现。

## **set_data**(*path*, *data*)

[`importlib.abc.SourceLoader.set_data()`]({{< ref "/library/modules/importlib#importlib.abc.SourceLoader.set_data" >}}) 的具体代码实现。

## **load_module**(*name=None*)

[`importlib.abc.Loader.load_module()`]({{< ref "/library/modules/importlib#importlib.abc.Loader.load_module" >}}) 的具体代码实现，这里要加载的模块名是可选的。

> 自 3.6 版本弃用: 改用 [`importlib.abc.Loader.exec_module()`]({{< ref "/library/modules/importlib#importlib.abc.Loader.exec_module" >}}) 。

## *class* importlib.machinery.**SourcelessFileLoader**(*fullname*, *path*)

[`importlib.abc.FileLoader`]({{< ref "/library/modules/importlib#importlib.abc.FileLoader" >}}) 的具体代码实现，可导入字节码文件（也即源代码文件不存在）。

​	请注意，直接用字节码文件（而不是源代码文件），会让模块无法应用于所有的 Python 版本或字节码格式有所改动的新版本 Python。

> Added in version 3.3.
>

## **name**

​	加载器将要处理的模块名。

## **path**

​	二进制码文件的路径。

## **is_package**(*fullname*)

​	根据 [`path`]({{< ref "/library/modules/importlib#importlib.machinery.SourcelessFileLoader.path" >}}) 确定该模块是否为包。

## **get_code**(*fullname*)

​	返回由 [`path`]({{< ref "/library/modules/importlib#importlib.machinery.SourcelessFileLoader.path" >}}) 创建的 [`name`]({{< ref "/library/modules/importlib#importlib.machinery.SourcelessFileLoader.name" >}}) 的代码对象。

## **get_source**(*fullname*)

​	因为用此加载器时字节码文件没有源码文件，所以返回 `None`。

## **load_module**(*name=None*)

[`importlib.abc.Loader.load_module()`]({{< ref "/library/modules/importlib#importlib.abc.Loader.load_module" >}}) 的具体代码实现，这里要加载的模块名是可选的。

> 自 3.6 版本弃用: 改用 [`importlib.abc.Loader.exec_module()`]({{< ref "/library/modules/importlib#importlib.abc.Loader.exec_module" >}}) 。

## *class* importlib.machinery.**ExtensionFileLoader**(*fullname*, *path*)

[`importlib.abc.ExecutionLoader`]({{< ref "/library/modules/importlib#importlib.abc.ExecutionLoader" >}}) 的具体代码实现，用于扩展模块。

​	参数 *fullname* 指定了加载器要支持的模块名。参数 *path* 是指向扩展模块文件的路径。

​	请注意，在默认情况下，在子解释器中导入未实现多阶段初始化的扩展模块 (参见 [**PEP 489**](https://peps.python.org/pep-0489/)) 将会失败，即使在其他情况下能够成功导入。

> Added in version 3.3.
>

> 在 3.12 版本发生变更: 在子解释器中使用时需要多阶段初始化。

## **name**

​	装载器支持的模块名。

## **path**

​	扩展模块的路径。

## **create_module**(*spec*)

​	根据 [**PEP 489**](https://peps.python.org/pep-0489/) ，由给定规范创建模块对象。

> Added in version 3.5.
>

## **exec_module**(*module*)

​	根据 [**PEP 489**](https://peps.python.org/pep-0489/)，初始化给定的模块对象。

> Added in version 3.5.
>

## **is_package**(*fullname*)

​	根据 [`EXTENSION_SUFFIXES`]({{< ref "/library/modules/importlib#importlib.machinery.EXTENSION_SUFFIXES" >}}) ，如果文件路径指向某个包的 `__init__` 模块，则返回 `True`。

## **get_code**(*fullname*)

​	返回 `None`，因为扩展模块缺少代码对象。

## **get_source**(*fullname*)

​	返回 `None`，因为扩展模块没有源代码。

## **get_filename**(*fullname*)

​	返回 [`path`]({{< ref "/library/modules/importlib#importlib.machinery.ExtensionFileLoader.path" >}})。

> Added in version 3.4.
>

## *class* importlib.machinery.**NamespaceLoader**(*name*, *path*, *path_finder*)

​	一个针对命名空间包的 [`importlib.abc.InspectLoader`]({{< ref "/library/modules/importlib#importlib.abc.InspectLoader" >}}) 具体实现。 这是一个私有类的别名，仅为在命名空间包上内省 `__loader__` 属性而被设为公有:



``` python
>>> from importlib.machinery import NamespaceLoader
>>> import my_namespace
>>> isinstance(my_namespace.__loader__, NamespaceLoader)
True
>>> import importlib.abc
>>> isinstance(my_namespace.__loader__, importlib.abc.Loader)
True
```

> Added in version 3.11.
>

## *class* importlib.machinery.**ModuleSpec**(*name*, *loader*, ***, *origin=None*, *loader_state=None*, *is_package=None*)

​	A specification for a module's import-system-related state. This is typically exposed as the module's [`__spec__`]({{< ref "/reference/datamodel#module.__spec__" >}}) attribute. Many of these attributes are also available directly on a module: for example, `module.__spec__.origin == module.__file__`. Note, however, that while the *values* are usually equivalent, they can differ since there is no synchronization between the two objects. For example, it is possible to update the module's [`__file__`]({{< ref "/reference/datamodel#module.__file__" >}}) at runtime and this will not be automatically reflected in the module's [`__spec__.origin`]({{< ref "/library/modules/importlib#importlib.machinery.ModuleSpec.origin" >}}), and vice versa.

> Added in version 3.4.
>

## **name**

​	The module's fully qualified name (see [`module.__name__`]({{< ref "/reference/datamodel#module.__name__" >}})). The [finder]({{< ref "/glossary/idx#term-finder" >}}) should always set this attribute to a non-empty string.

## **loader**

​	The [loader]({{< ref "/glossary/idx#term-loader" >}}) used to load the module (see [`module.__loader__`]({{< ref "/reference/datamodel#module.__loader__" >}})). The [finder]({{< ref "/glossary/idx#term-finder" >}}) should always set this attribute.

## **origin**

​	The location the [loader]({{< ref "/glossary/idx#term-loader" >}}) should use to load the module (see [`module.__file__`]({{< ref "/reference/datamodel#module.__file__" >}})). For example, for modules loaded from a `.py` file this is the filename. The [finder]({{< ref "/glossary/idx#term-finder" >}}) should always set this attribute to a meaningful value for the [loader]({{< ref "/glossary/idx#term-loader" >}}) to use. In the uncommon case that there is not one (like for namespace packages), it should be set to `None`.

## **submodule_search_locations**

​	A (possibly empty) [sequence]({{< ref "/glossary/idx#term-sequence" >}}) of strings enumerating the locations in which a package's submodules will be found (see [`module.__path__`]({{< ref "/reference/datamodel#module.__path__" >}})). Most of the time there will only be a single directory in this list.

​	The [finder]({{< ref "/glossary/idx#term-finder" >}}) should set this attribute to a sequence, even an empty one, to indicate to the import system that the module is a package. It should be set to `None` for non-package modules. It is set automatically later to a special object for namespace packages.

## **loader_state**

[finder]({{< ref "/glossary/idx#term-finder" >}}) 可以将此属性设为一个包含额外的模块专属数据的对象供加载模块时使用。 在其他情况下应将其设为 `None`。

## **cached**

​	The filename of a compiled version of the module's code (see [`module.__cached__`]({{< ref "/reference/datamodel#module.__cached__" >}})). The [finder]({{< ref "/glossary/idx#term-finder" >}}) should always set this attribute but it may be `None` for modules that do not need compiled code stored.

## **parent**

​	（只读）模块所在的包的完整限定名称（或者对于最高层级模块来说则空字符串）。 参见 [`module.__package__`]({{< ref "/reference/datamodel#module.__package__" >}})。 如果模块是一个包则它将与 [`name`]({{< ref "/library/modules/importlib#importlib.machinery.ModuleSpec.name" >}}) 相同。

## **has_location**

​	如果 spec 的 [`origin`]({{< ref "/library/modules/importlib#importlib.machinery.ModuleSpec.origin" >}}) 指向一个可加载的位置则为 `True`，否则为 `False`。 该值将确定如何解读 `origin` 以及如何填充模块的 [`__file__`]({{< ref "/reference/datamodel#module.__file__" >}})。

## *class* importlib.machinery.**AppleFrameworkLoader**(*name*, *path*)

[`importlib.machinery.ExtensionFileLoader`]({{< ref "/library/modules/importlib#importlib.machinery.ExtensionFileLoader" >}}) 的一个专用版本，能以 Framework 格式加载扩展模块。

​	为了保持与 iOS App Store 的兼容性，在 iOS app 中的 *所有* 二进制模块都必须为动态库，包含在具有适当元数据的框架中，保存于被打包 app 的 `Frameworks` 文件夹下。 每个框架只能有一个二进制模块，而在 Frameworks 文件夹之外不能有可执行的二进制模块。

​	为满足此要求，当在 iOS 上运行时，扩展模块二进制代码 *不会* 被打包为 `sys.path` 中的 `.so` 文件，而是作为单个的独立框架。 要发现这些框架，此加载器必须针对 `.fwork` 文件扩展名进行注册，并以一个 `.fwork` 文件作为 `sys.path` 中二进制代码的原始位置上的占位符。 `.fwork` 文件包含 `Frameworks` 文件夹中实际二进制文件相对于 app 包的路径。 为允许将框架打包的二进制代码解析到原始位置上，框架应当包含一个 `.origin` 文件，其中包含 `.fwork` 文件相对于 app 包的位置。

​	例如，考虑导入 `from foo.bar import _whiz` 的情况，其中 `_whiz` 是使用二进制模块 `sources/foo/bar/_whiz.abi3.so` 实现的，这里 `sources` 是在 `sys.path` 中注册的相对于 app 包的位置。 此模块 *必须* 发布为 `Frameworks/foo.bar._whiz.framework/foo.bar._whiz` (根据模块的完整导入路径创建框架名称)，并通过 `.framework` 目录中的 `Info.plist` 文件将二进制文件标识为一个框架。 `foo.bar._whiz` 模块在原始位置中以一个 `sources/foo/bar/_whiz.abi3.fwork` 标记文件来代表，其中包含路径 `Frameworks/foo.bar._whiz/foo.bar._whiz`。 该框架还要包含 `Frameworks/foo.bar._whiz.framework/foo.bar._whiz.origin`，其中包含 `.fwork` 文件的路径。

​	当使用此加载器加载一个模块时，模块的 `__file__` 将被报告为 `.fwork` 文件的位置。 这允许代码使用模块的 `__file__` 作为文件系统遍历的锚点。 不过，原始规范说明将会引用 `.framework` 文件夹下 *实际* 二进制文件的位置。binary in the

​	构建 app 的 Xcode 项目要负责将存在于 `PYTHONPATH` 中的任何 `.so` 文件转换为 `Frameworks` 文件夹下的框架（包括从模块文件获取扩展，框架元数据的添加，以及结果框架的签名），并创建 `.fwork` 和 `.origin` 文件。 这通常会在 Xcode 项目中使用一个构建步骤来完成；请参阅 iOS 文档了解有关如何构造此构建步骤的细节。

> Added in version 3.13.
>

[Availability]({{< ref "/library/intro#availability" >}}): iOS.

## **name**

​	装载器支持的模块名。

## **path**

​	扩展模块 `.fwork` 文件的路径。



## [`importlib.util`]({{< ref "/library/modules/importlib#module-importlib.util" >}}) —— 导入器的工具程序代码

**源代码：** [Lib/importlib/util.py](https://github.com/python/cpython/tree/3.13/Lib/importlib/util.py)

------

​	本模块包含了帮助构建 [importer]({{< ref "/glossary/idx#term-importer" >}}) 的多个对象。

## importlib.util.**MAGIC_NUMBER**

​	代表字节码版本号的字节串。若要有助于加载/写入字节码，可考虑采用 [`importlib.abc.SourceLoader`]({{< ref "/library/modules/importlib#importlib.abc.SourceLoader" >}})。

> Added in version 3.4.
>

## importlib.util.**cache_from_source**(*path*, *debug_override=None*, ***, *optimization=None*)

​	返回 [**PEP 3147**](https://peps.python.org/pep-3147/)/[**PEP 488**](https://peps.python.org/pep-0488/) 定义的，与源 *path* 相关联的已编译字节码文件的路径。 例如，如果 *path* 为 `/foo/bar/baz.py` 则 Python 3.2 中的返回值将是 `/foo/bar/__pycache__/baz.cpython-32.pyc`。 字符串 `cpython-32` 来自于当前的魔法标签 (参见 `get_tag()`; 如果 `sys.implementation.cache_tag` 未定义则将会引发 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}}))。

​	参数 *optimization* 用于指定字节码文件的优化级别。空字符串代表没有优化，所以 *optimization* 为 的 `/foo/bar/baz.py`，将会得到字节码路径为 `/foo/bar/__pycache__/baz.cpython-32.pyc`。`None` 会导致采用解释器的优化。任何其他字符串都会被采用，所以 *optimization* 为 `''` 的 `/foo/bar/baz.py` 会导致字节码路径为 `/foo/bar/__pycache__/baz.cpython-32.opt-2.pyc`。*optimization* 字符串只能是字母数字，否则会触发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

*debug_override* 参数已废弃，可用于覆盖系统的 `__debug__` 值。`True` 值相当于将 *optimization* 设为空字符串。`False` 则相当于*optimization* 设为 `1`。如果 *debug_override* 和 *optimization* 都不为 `None`，则会触发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。

> Added in version 3.4.
>

> 在 3.5 版本发生变更: 增加了 *optimization* 参数，废弃了 *debug_override* 参数。

> 在 3.6 版本发生变更: 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## importlib.util.**source_from_cache**(*path*)

​	根据指向一个 [**PEP 3147**](https://peps.python.org/pep-3147/) 文件名的 *path*，返回相关联的源代码文件路径。 举例来说，如果 *path* 为 `/foo/bar/__pycache__/baz.cpython-32.pyc` 则返回的路径将是 `/foo/bar/baz.py`。 *path* 不需要已存在，但如果它未遵循 [**PEP 3147**](https://peps.python.org/pep-3147/) 或 [**PEP 488**](https://peps.python.org/pep-0488/) 的格式，则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。 如果未定义 `sys.implementation.cache_tag`，则会引发 [`NotImplementedError`]({{< ref "/library/exceptions#NotImplementedError" >}})。

> Added in version 3.4.
>

> 在 3.6 版本发生变更: 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## importlib.util.**decode_source**(*source_bytes*)

​	对代表源代码的字节串进行解码，并将其作为带有通用换行符的字符串返回（符合 [`importlib.abc.InspectLoader.get_source()`]({{< ref "/library/modules/importlib#importlib.abc.InspectLoader.get_source" >}}) 要求）。

> Added in version 3.4.
>

## importlib.util.**resolve_name**(*name*, *package*)

​	将模块的相对名称解析为绝对名称。

​	如果 **name** 前面没有句点，那就简单地返回 **name**。这样就能采用 `importlib.util.resolve_name('sys', __spec__.parent)` 之类的写法，而无需检查是否需要 **package** 参数。

​	如果 **name** 是一个相对模块名称但 **package** 为假值（如为 `None` 或空字符串）则会引发 [`ImportError`]({{< ref "/library/exceptions#ImportError" >}})。 如果相对名称离开了其所在的包（如为从 `spam` 包请求 `..bacon` 的形式）则也会引发 [`ImportError`]({{< ref "/library/exceptions#ImportError" >}})。

> Added in version 3.3.
>

> 在 3.9 版本发生变更: 为了改善与 import 语句的一致性，对于无效的相对导入尝试会引发 [`ImportError`]({{< ref "/library/exceptions#ImportError" >}}) 而不是 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

## importlib.util.**find_spec**(*name*, *package=None*)

​	查找模块的 [spec]({{< ref "/glossary/idx#term-module-spec" >}})，可选择相对于指定的 **package** 名称。 如果该模块位于 [`sys.modules`]({{< ref "/library/python/sys#sys.modules" >}}) 中，则会返回 `sys.modules[name].__spec__` (除非 spec 为 `None` 或未设置，在此情况下则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}))。 在其他情况下将使用 [`sys.meta_path`]({{< ref "/library/python/sys#sys.meta_path" >}}) 进行搜索。 如果找不到任何 spec 则返回 `None`。

​	如果 **name** 为一个子模块（带有一个句点），则会自动导入父级模块。

**name** 和 **package** 的用法与 `import_module()` 相同。

> Added in version 3.4.
>

> 在 3.7 版本发生变更: 如果 **package** 实际上不是一个包（即缺少 [`__path__`]({{< ref "/reference/datamodel#module.__path__" >}}) 属性）则会引发 [`ModuleNotFoundError`]({{< ref "/library/exceptions#ModuleNotFoundError" >}}) 而不是 [`AttributeError`]({{< ref "/library/exceptions#AttributeError" >}})。

## importlib.util.**module_from_spec**(*spec*)

​	基于 **spec** 和 [`spec.loader.create_module`]({{< ref "/library/modules/importlib#importlib.abc.Loader.create_module" >}}) 创建一个新模块。

​	如果 [`spec.loader.create_module`]({{< ref "/library/modules/importlib#importlib.abc.Loader.create_module" >}}) 未返回 `None`，那么先前已存在的属性不会被重置。另外，如果 [`AttributeError`]({{< ref "/library/exceptions#AttributeError" >}}) 是在访问 **spec** 或设置模块属性时触发的，则不会触发 。

​	本函数比 [`types.ModuleType`]({{< ref "/library/datatypes/types#types.ModuleType" >}}) 创建新模块要好，因为用到 **spec** 模块设置了尽可能多的导入控制属性。

> Added in version 3.5.
>

## importlib.util.**spec_from_loader**(*name*, *loader*, ***, *origin=None*, *is_package=None*)

​	一个工厂函数，用于创建基于加载器的 [`ModuleSpec`]({{< ref "/library/modules/importlib#importlib.machinery.ModuleSpec" >}}) 实例。参数的含义与 ModuleSpec 的相同。该函数会利用当前可用的 [loader]({{< ref "/glossary/idx#term-loader" >}}) API，比如 `InspectLoader.is_package()`，以填充所有缺失的规格信息。

> Added in version 3.4.
>

## importlib.util.**spec_from_file_location**(*name*, *location*, ***, *loader=None*, *submodule_search_locations=None*)

​	一个工厂函数，根据文件路径创建 [`ModuleSpec`]({{< ref "/library/modules/importlib#importlib.machinery.ModuleSpec" >}}) 实例。缺失的信息将根据 spec 进行填补，利用加载器 API ，以及模块基于文件的隐含条件。

> Added in version 3.4.
>

> 在 3.6 版本发生变更: 接受一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## importlib.util.**source_hash**(*source_bytes*)

​	以字节串的形式返回 *source_bytes* 的哈希值。基于哈希值的 `.pyc` 文件在头部嵌入了对应源文件内容的 [`source_hash()`]({{< ref "/library/modules/importlib#importlib.util.source_hash" >}})。

> Added in version 3.7.
>

## importlib.util.**_incompatible_extension_module_restrictions**(***, *disable_check*)

​	一个可以暂时跳过扩展模块兼容性检查的上下文管理器。 在默认情况下该检查将被启用并且当在子解释器中导入单阶段初始化模块时该检查会失败。 如果多阶段初始化模块没有显式地支持针对子解释器的 GIL，那么当它在一个有自己的 GIL 的解释器中被导入时，该检查也会失败。

​	请注意该函数是为了适应一种不寻常的情况；这种情况可能最终会消失。 这很有可能不是你需要考虑的事情。

​	你可以通过实现多阶段初始化的基本接口 ([**PEP 489**](https://peps.python.org/pep-0489/)) 并假装支持多解释器 (或解释器级的 GIL) 来获得与该函数相同的效果。

​	警告

 

​	使用该函数来禁用检查可能会导致预期之外的行为甚至崩溃。 它应当仅在扩展模块开发过程中使用。

> Added in version 3.12.
>

## *class* importlib.util.**LazyLoader**(*loader*)

​	此类会延迟执行模块加载器，直至该模块有一个属性被访问到。

​	此类 **仅仅** 适用于定义 [`exec_module()`]({{< ref "/library/modules/importlib#importlib.abc.Loader.exec_module" >}}) 作为需要控制模块使用何种模块类型的加载器。 出于相同理由，加载器的 [`create_module()`]({{< ref "/library/modules/importlib#importlib.abc.Loader.create_module" >}}) 方法必须返回 `None` 或其 `__class__` 属性可被改变并且不使用 [槽位]({{< ref "/glossary/idx#term-__slots__" >}}) 的类型。 最后，用于替换已放入 [`sys.modules`]({{< ref "/library/python/sys#sys.modules" >}}) 的对象的模块将无法工作因为没有办法安全地在整个解释器中正确替换模块引用； 如果检测到这种替换则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

​备注
 

​	如果项目对启动时间要求很高，只要模块未被用过，此类能够最小化加载模块的开销。对于启动时间并不重要的项目来说，由于加载过程中产生的错误信息会被暂时搁置，因此强烈不建议使用此类。

> Added in version 3.5.
>

> 在 3.6 版本发生变更: 开始调用 [`create_module()`]({{< ref "/library/modules/importlib#importlib.abc.Loader.create_module" >}})，移除 [`importlib.machinery.BuiltinImporter`]({{< ref "/library/modules/importlib#importlib.machinery.BuiltinImporter" >}}) 和 [`importlib.machinery.ExtensionFileLoader`]({{< ref "/library/modules/importlib#importlib.machinery.ExtensionFileLoader" >}}) 的兼容性警告。

## *classmethod* **factory**(*loader*)

​	一个返回创建延迟加载器的可调用对象的类方法。 这专门被用于加载器由类而不是实例来传入的场合。

```
suffixes = importlib.machinery.SOURCE_SUFFIXES
loader = importlib.machinery.SourceFileLoader
lazy_loader = importlib.util.LazyLoader.factory(loader)
finder = importlib.machinery.FileFinder(path, (lazy_loader, suffixes))
```



## 例子

### 用编程方式导入

​	要以编程方式导入一个模块，请使用 [`importlib.import_module()`]({{< ref "/library/modules/importlib#importlib.import_module" >}}) ：

```
import importlib

itertools = importlib.import_module('itertools')
```

### 检查某模块可否导入。

​	如果你需要在不实际执行导入的情况下确定某个模块是否可被导入，则你应当使用 [`importlib.util.find_spec()`]({{< ref "/library/modules/importlib#importlib.util.find_spec" >}})。

​	请注意如果 `name` 是一个子模块（即包含一个点号），则 [`importlib.util.find_spec()`]({{< ref "/library/modules/importlib#importlib.util.find_spec" >}}) 将会导入父模块。

```
import importlib.util
import sys

# 出于展示目的。
name = 'itertools'

if name in sys.modules:
    print(f"{name!r} already in sys.modules")
elif (spec := importlib.util.find_spec(name)) is not None:
    # 如果你选择执行实际的导入 ...
    module = importlib.util.module_from_spec(spec)
    sys.modules[name] = module
    spec.loader.exec_module(module)
    print(f"{name!r} has been imported")
else:
    print(f"can't find the {name!r} module")
```

### 直接导入源码文件。

​	这个专用方案应当谨慎使用：它是直接指明文件路径而不搜索 [`sys.path`]({{< ref "/library/python/sys#sys.path" >}}) 的 import 语句的近似物。 应当首先考虑其他替代方案，比如在需要某个特定模块时修改 [`sys.path`]({{< ref "/library/python/sys#sys.path" >}})，或在运行某个 Python 得到正确的全局命名空间时使用 [`runpy.run_path()`]({{< ref "/library/modules/runpy#runpy.run_path" >}})。

​	要直接从某个路径导入 Python 源文件，请使用以下写法:

```
import importlib.util
import sys


def import_from_path(module_name, file_path):
    spec = importlib.util.spec_from_file_location(module_name, file_path)
    module = importlib.util.module_from_spec(spec)
    sys.modules[module_name] = module
    spec.loader.exec_module(module)
    return module


# 仅用于演示目的 (`json` 是随意选择的)。
import json
file_path = json.__file__
module_name = json.__name__

# 结果与 `import json` 类似。
json = import_from_path(module_name, file_path)
```

### 实现延迟导入

​	以下例子展示了如何实现延迟导入：



``` python
>>> import importlib.util
>>> import sys
>>> def lazy_import(name):
...     spec = importlib.util.find_spec(name)
...     loader = importlib.util.LazyLoader(spec.loader)
...     spec.loader = loader
...     module = importlib.util.module_from_spec(spec)
...     sys.modules[name] = module
...     loader.exec_module(module)
...     return module
...
>>> lazy_typing = lazy_import("typing")
>>> #lazy_typing is a real module object,
>>> #but it is not loaded in memory yet.
>>> lazy_typing.TYPE_CHECKING
False
```

### 导入器的配置

​	对于导入的深度定制，通常你需要实现一个 [importer]({{< ref "/glossary/idx#term-importer" >}})。 这意味着同时管理 [finder]({{< ref "/glossary/idx#term-finder" >}}) 和 [loader]({{< ref "/glossary/idx#term-loader" >}}) 两方面。 对于查找器来说根据你的需求有两种类别可供选择: [meta path finder]({{< ref "/glossary/idx#term-meta-path-finder" >}}) 或 [path entry finder]({{< ref "/glossary/idx#term-path-entry-finder" >}})。 前者你应当放到 [`sys.meta_path`]({{< ref "/library/python/sys#sys.meta_path" >}}) 而后者是使用 [path entry hook]({{< ref "/glossary/idx#term-path-entry-hook" >}}) 在 [`sys.path_hooks`]({{< ref "/library/python/sys#sys.path_hooks" >}}) 上创建并与 [`sys.path`]({{< ref "/library/python/sys#sys.path" >}}) 条目一起创建一个潜在的查找器。 下面的例子将向你演示如何注册自己的导入器供导入机制使用 (关于自行创建导入器，请阅读在本包内定义的相应类的文档):

```
import importlib.machinery
import sys

# 仅用于展示目的。
SpamMetaPathFinder = importlib.machinery.PathFinder
SpamPathEntryFinder = importlib.machinery.FileFinder
loader_details = (importlib.machinery.SourceFileLoader,
                  importlib.machinery.SOURCE_SUFFIXES)

# 设置一个元路径查找器。
# 确保根据优先级别将查找器放在列表中正确的位置上。
sys.meta_path.append(SpamMetaPathFinder)

# 设置一个路径条目查找器。
# 确保根据优先级别将路径钩子放在列表中正确的位置上。
sys.path_hooks.append(SpamPathEntryFinder.path_hook(loader_details))
```

### [`importlib.import_module()`]({{< ref "/library/modules/importlib#importlib.import_module" >}}) 的近似实现

​	导入过程本身是用 Python 代码实现的，这样就有可能通过 importlib 来对外公开大部分导入机制。 以下代码通过提供 [`importlib.import_module()`]({{< ref "/library/modules/importlib#importlib.import_module" >}}) 的近似实现来说明 importlib 所公开的几种 API:

```
import importlib.util
import sys

def import_module(name, package=None):
    """导入的近似实现。"""
    absolute_name = importlib.util.resolve_name(name, package)
    try:
        return sys.modules[absolute_name]
    except KeyError:
        pass

    path = None
    if '.' in absolute_name:
        parent_name, _, child_name = absolute_name.rpartition('.')
        parent_module = import_module(parent_name)
        path = parent_module.__spec__.submodule_search_locations
    for finder in sys.meta_path:
        spec = finder.find_spec(absolute_name, path)
        if spec is not None:
            break
    else:
        msg = f'No module named {absolute_name!r}'
        raise ModuleNotFoundError(msg, name=absolute_name)
    module = importlib.util.module_from_spec(spec)
    sys.modules[absolute_name] = module
    spec.loader.exec_module(module)
    if path is not None:
        setattr(parent_module, child_name, module)
    return module
```
