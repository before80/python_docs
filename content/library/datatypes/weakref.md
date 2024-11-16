+++
title = "weakref --- 弱引用"
date = 2024-11-15T11:18:41+08:00
weight = 80
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/weakref.html](https://docs.python.org/zh-cn/3.13/library/weakref.html)
>
> 收录该文档的时间：`2024-11-15T11:18:41+08:00`

# [`weakref`]({{< ref "/library/datatypes/weakref#module-weakref" >}}) --- 弱引用

**源代码：** [Lib/weakref.py](https://github.com/python/cpython/tree/3.13/Lib/weakref.py)

------

[`weakref`]({{< ref "/library/datatypes/weakref#module-weakref" >}}) 模块允许 Python 程序员创建对象的 *弱引用* 。

​	在下文中，术语 *所指对象* 表示弱引用所指向的对象。

​	对象的弱引用不能保证对象存活：当所指对像的引用只剩弱引用时， [垃圾回收]({{< ref "/glossary/idx#term-garbage-collection" >}}) 可以销毁所指对象，并将其内存重新用于其它用途。但是，在实际销毁对象之前，即使没有强引用，弱引用也能返回该对象。

​	弱引用的一个主要用途是实现一个存储大型对象的缓存或映射，但又不希望该大型对象仅因为它只出现在这个缓存或映射中而保持存活。

​	例如，如果你有许多大型二进制图像对象，你可能希望为每个对象关联一个名称。如果你使用 Python 字典来将名称映射到图像，或将图像映射到名称，那么图像对象将因为它们在字典中作为值或键而保持存活。 [`weakref`]({{< ref "/library/datatypes/weakref#module-weakref" >}}) 模块提供的 [`WeakKeyDictionary`]({{< ref "/library/datatypes/weakref#weakref.WeakKeyDictionary" >}}) 和 [`WeakValueDictionary`]({{< ref "/library/datatypes/weakref#weakref.WeakValueDictionary" >}}) 类可以替代 Python 字典，它们使用弱引用来构造映射，这种映射不会仅因为对象出现在映射中而使对象保持存活。例如，如果一个图像对象是 [`WeakValueDictionary`]({{< ref "/library/datatypes/weakref#weakref.WeakValueDictionary" >}}) 中的值，那么当对该图像对象的剩余引用是弱映射对象所持有的弱引用时，垃圾回收器将回收该对象，并删除弱映射对象中相应的条目。

[`WeakKeyDictionary`]({{< ref "/library/datatypes/weakref#weakref.WeakKeyDictionary" >}}) 和 [`WeakValueDictionary`]({{< ref "/library/datatypes/weakref#weakref.WeakValueDictionary" >}}) 在它们的实现中使用了弱引用，并在弱引用上设置当键或值被垃圾回收器回收时通知弱字典的回调函数。 [`WeakSet`]({{< ref "/library/datatypes/weakref#weakref.WeakSet" >}}) 实现了 [`set`]({{< ref "/library/stdtypes#set" >}}) 接口，但像 [`WeakKeyDictionary`]({{< ref "/library/datatypes/weakref#weakref.WeakKeyDictionary" >}}) 一样，只持有其元素的弱引用。

[`finalize`]({{< ref "/library/datatypes/weakref#weakref.finalize" >}}) 提供了一种直接的方法来注册当对象被垃圾收集时要调用的清理函数。这比在普通的弱引用上设置回调函数的方式更简单，因为模块会自动确保对象被回收前终结器一直保持存活。

​	这些弱容器类型之一或者 [`finalize`]({{< ref "/library/datatypes/weakref#weakref.finalize" >}}) 就是大多数程序所需要的——通常不需要直接创建自己的弱引用。[`weakref`]({{< ref "/library/datatypes/weakref#module-weakref" >}}) 模块暴露了底层机制，以便用于高级用途。

​	并非所有对象都可以被弱引用。支持弱引用的对象包括类实例、用 Python（而非用 C）编写的函数、实例方法、集合、冻结集合、某些 [文件对象]({{< ref "/glossary/idx#term-file-object" >}})、[生成器]({{< ref "/glossary/idx#term-generator" >}})、类型对象、套接字、数组、双端队列、正则表达式模式对象以及代码对象。

> 在 3.2 版本发生变更: 添加了对 thread.lock，threading.Lock 和代码对象的支持。

​	一些内置类型，如 [`list`]({{< ref "/library/stdtypes#list" >}}) 和 [`dict`]({{< ref "/library/stdtypes#dict" >}})，不直接支持弱引用，但可以通过子类化添加支持:

```
class Dict(dict):
    pass

obj = Dict(red=1, green=2, blue=3)   # 此对象是可弱引用的
```

**CPython 实现细节：** 其他内置类型，如 [`tuple`]({{< ref "/library/stdtypes#tuple" >}}) 和 [`int`]({{< ref "/library/functions#int" >}})，不支持弱引用，即使通过子类化也不支持。

​	可以轻松地使扩展类型支持弱引用；参见 [弱引用支持]({{< ref "/extending/newtypes#weakref-support" >}})。

​	当为某个给定类型定义了 `__slots__` 时，弱引用支持会被禁用，除非将 `'__weakref__'` 字符串也加入到 `__slots__` 声明的字符串序列中。 请参阅 [__slots__ 文档]({{< ref "/reference/datamodel#slots" >}}) 了解详情。

## *class* weakref.**ref**(*object*[, *callback*])

​	返回 *object* 的弱引用。如果所指对象存活，则可以通过调用引用对象来获取原始对象；如果所指对象不存在，则调用引用对象将得到 [`None`]({{< ref "/library/constants#None" >}}) 。如果提供了值不是 [`None`]({{< ref "/library/constants#None" >}}) 的 *callback*，并且返回的弱引用对象仍然存活，则在对象即将终结时将调用回调函数；弱引用对象将作为回调函数的唯一参数传递；然后所指对象将不再可用。

​	允许为同一个对象的构造多个弱引用。每个弱引用注册的回调函数将按从最近注册的回调函数，到最早注册的回调函数的顺序调用。

​	由回调函数引发的异常将记录于标准错误输入，但无法传播该异常；这些异常的处理方式与对象 [`__del__()`]({{< ref "/reference/datamodel#object.__del__" >}}) 方法引发异常的处理方式相同。

​	如果 *object* 可哈希，则弱引用也 [可哈希]({{< ref "/glossary/idx#term-hashable" >}})。即使在 *object* 被删除之后，弱引用仍将保持其哈希值。如果在 *object* 被删除之后才首次调用 [`hash()`]({{< ref "/library/functions#hash" >}})，则该调用将引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。

​	弱引用支持相等性测试，但不支持排序。如果所指对象仍然存活，两个引用具有与它们的所指对象具有一致的相等关系（无论 *callback* 是否相同）。如果删除了任一所指对象，则仅在两个引用指向同一对象时，二者才相等。

​	这是一个可子类化的类型，而非一个工厂函数。

## `__callback__`

​	这个只读属性会返回当前关联到弱引用的回调函数。 如果回调函数不存在，或弱引用的所指对象已不存在，则此属性的值为 `None`。

> 在 3.4 版本发生变更: 添加了 [`__callback__`]({{< ref "/library/datatypes/weakref#weakref.ref.__callback__" >}}) 属性。

## weakref.**proxy**(*object*[, *callback*])

​	返回一个使用弱引用的 *object* 代理。此函数支持在大多数上下文中使用代理，而不要求显式地解引用弱引用对象。返回的对象类型将为 `ProxyType` 或 `CallableProxyType`，具体取决于 *object* 是否为可调用对象。无论所指对象是否可哈希，代理对象都不属于 [可哈希]({{< ref "/glossary/idx#term-hashable" >}}) 对象；这避免了与它们的基本可变性质相关的许多问题，且防止代理被用作字典的键。*callback* 形参含义与 [`ref()`]({{< ref "/library/datatypes/weakref#weakref.ref" >}}) 函数的同名形参含义相同。

​	在所指对象被作为垃圾回收后访问代理对象的属性将引发 [`ReferenceError`]({{< ref "/library/exceptions#ReferenceError" >}})。

> 在 3.8 版本发生变更: 扩展代理对象所支持的运算符，包括矩阵乘法运算符 `@` 和 `@=`。

## weakref.**getweakrefcount**(*object*)

​	返回指向 *object* 的弱引用和代理的数量。

## weakref.**getweakrefs**(*object*)

​	返回由指向 *object* 的所有弱引用和代理构成的列表。

## *class* weakref.**WeakKeyDictionary**([*dict*])

​	弱引用键的映射类。当不再存在对键的强引用时，字典中的相关条目将被丢弃。这可用于将额外数据与应用程序中其它部分拥有的对象相关联，而无需向这些对象添加属性。这对于重写了属性访问的对象来说特别有用。

​	请注意，当把一个与现有键具有相同值（但是标识号不相等）的键插入字典时，它会替换该值，但不会替换现有的键。由于这一点，当删除对原来的键的引用时，也将同时删除字典中的对应条目:



``` python
>>> class T(str): pass
...
>>> k1, k2 = T(), T()
>>> d = weakref.WeakKeyDictionary()
>>> d[k1] = 1   # d = {k1: 1}
>>> d[k2] = 2   # d = {k1: 2}
>>> del k1      # d = {}
```

​	一种变通做法是在重新赋值之前先移除键:



``` python
>>> class T(str): pass
...
>>> k1, k2 = T(), T()
>>> d = weakref.WeakKeyDictionary()
>>> d[k1] = 1   # d = {k1: 1}
>>> del d[k1]
>>> d[k2] = 2   # d = {k2: 2}
>>> del k1      # d = {k2: 2}
```

> 在 3.9 版本发生变更: 增加了对 `|` 和 `|=` 运算符的支持，相关说明见 [**PEP 584**](https://peps.python.org/pep-0584/)。

[`WeakKeyDictionary`]({{< ref "/library/datatypes/weakref#weakref.WeakKeyDictionary" >}}) 对象具有一个额外方法，可以直接公开内部引用。这些引用不保证在它们被使用时仍然保持“存活”，因此这些引用的调用结果需要在使用前进行检测。 此方法可用于避免创建会导致垃圾回收器将保留键超出实际需要时长的引用。

## WeakKeyDictionary.**keyrefs**()

​	返回包含对键的弱引用的可迭代对象。

## *class* weakref.**WeakValueDictionary**([*dict*])

​	弱引用值的映射类。当不再存在对该值的强引用时，字典中的条目将被丢弃。

> 在 3.9 版本发生变更: 增加了对 `|` 和 `|=` 运算符的支持，相关说明见 [**PEP 584**](https://peps.python.org/pep-0584/)。

[`WeakValueDictionary`]({{< ref "/library/datatypes/weakref#weakref.WeakValueDictionary" >}}) 对象具有一个额外方法，此方法存在与 [`WeakKeyDictionary.keyrefs()`]({{< ref "/library/datatypes/weakref#weakref.WeakKeyDictionary.keyrefs" >}}) 方法相同的问题。

## WeakValueDictionary.**valuerefs**()

​	返回包含对值的弱引用的可迭代对象。

## *class* weakref.**WeakSet**([*elements*])

​	保持对其元素弱引用的集合类。当某个元素没有强引用时，该元素将被丢弃。

## *class* weakref.**WeakMethod**(*method*[, *callback*])

​	一个模拟对绑定方法（即在类中定义并在实例中查找的方法）进行弱引用的自定义 [`ref`]({{< ref "/library/datatypes/weakref#weakref.ref" >}}) 子类。 由于绑定方法是临时性的，标准弱引用无法保持它。 [`WeakMethod`]({{< ref "/library/datatypes/weakref#weakref.WeakMethod" >}}) 包含特别代码用来重新创建绑定方法，直到对象或初始函数被销毁:



``` python
>>> class C:
...     def method(self):
...         print("method called!")
...
>>> c = C()
>>> r = weakref.ref(c.method)
>>> r()
>>> r = weakref.WeakMethod(c.method)
>>> r()
<bound method C.method of <__main__.C object at 0x7fc859830220>>
>>> r()()
method called!
>>> del c
>>> gc.collect()
0
>>> r()
>>>
```

*callback* 与 [`ref()`]({{< ref "/library/datatypes/weakref#weakref.ref" >}}) 函数的同名形参含义相同。

> Added in version 3.4.
>

## *class* weakref.**finalize**(*obj*, *func*, */*, **args*, ***kwargs*)

​	返回一个可调用的终结器对象，该对象将在 *obj* 作为垃圾回收时被调用。与普通的弱引用不同，终结器将总是存活，直到引用对象被回收，这极大地简化了生命周期管理。

​	终结器总是被视为 *存活* 直到它被调用（显式调用或在垃圾回收时隐式调用），调用之后它将 *死亡*。 调用存活的终结器将返回 `func(*arg, **kwargs)` 的求值结果，而调用死亡的终结器将返回 [`None`]({{< ref "/library/constants#None" >}})。

​	在垃圾收集期间由终结器回调所引发的异常将显示在标准错误输出中，但无法被传播。 它们会按与对象的 [`__del__()`]({{< ref "/reference/datamodel#object.__del__" >}}) 方法或或弱引用的回调所引发的异常相同的方式被处理。

​	当程序退出时，剩余的存活终结器会被调用，除非它们的 [`atexit`]({{< ref "/library/python/atexit#module-atexit" >}}) 属性已被设为假值。 它们会按与创建时相反的顺序被调用。

​	终结器在 [interpreter shutdown]({{< ref "/glossary/idx#term-interpreter-shutdown" >}}) 的后期绝不会发起调用其回调函数，此时模块全局变量很可能已被替换为 [`None`]({{< ref "/library/constants#None" >}})。

## `__call__`()

​	如果 *self* 为存活状态则将其标记为已死亡，并返回调用 `func(*args, **kwargs)` 的结果。 如果 *self* 已死亡则返回 [`None`]({{< ref "/library/constants#None" >}})。

## **detach**()

​	如果 *self* 为存活状态则将其标记为已死亡，并返回元组 `(obj, func, args, kwargs)`。 如果 *self* 已死亡则返 [`None`]({{< ref "/library/constants#None" >}})。

## **peek**()

​	如果 *self* 为存活状态则返回元组 `(obj, func, args, kwargs)`。 如果 *self* 已死亡则返回 [`None`]({{< ref "/library/constants#None" >}})。

## **alive**

​	如果终结器为存活状态则该特征属性为真值，否则为假值。

## **atexit**

​	一个可写的布尔型特征属性，默认为真值。 当程序退出时，它会调用所有 [`atexit`]({{< ref "/library/datatypes/weakref#weakref.finalize.atexit" >}}) 为真值的剩余存活终结器。 它们会按与创建时相反的顺序被调用。

​备注
 

​	很重要的一点是确保 *func*, *args* 和 *kwargs* 不拥有任何对 *obj* 的引用，无论是直接的或是间接的，否则的话 *obj* 将永远不会被作为垃圾回收。 特别地，*func* 不应当是 *obj* 的一个绑定方法。

> Added in version 3.4.
>

## weakref.**ReferenceType**

​	弱引用对象的类型对象。

## weakref.**ProxyType**

​	不可调用对象的代理的类型对象。

## weakref.**CallableProxyType**

​	可调用对象的代理的类型对象。

## weakref.**ProxyTypes**

​	包含所有代理的类型对象的序列。 这可以用于更方便地检测一个对象是否是代理，而不必依赖于两种代理对象的名称。

​参见
[**PEP 205**](https://peps.python.org/pep-0205/) - 弱引用

​	此特性的提议和理由，包括早期实现的链接和其他语言中类似特性的相关信息。



## 弱引用对象

​	弱引用对象没有 [`ref.__callback__`]({{< ref "/library/datatypes/weakref#weakref.ref.__callback__" >}}) 以外的方法和属性。 一个弱引用对象如果存在，就允许通过调用它来获取引用:



``` python
>>> import weakref
>>> class Object:
...     pass
...
>>> o = Object()
>>> r = weakref.ref(o)
>>> o2 = r()
>>> o is o2
True
```

​	如果引用已不存在，则调用引用对象将返回 [`None`]({{< ref "/library/constants#None" >}}):



``` python
>>> del o, o2
>>> print(r())
None
```

​	检测一个弱引用对象是否仍然存在应该使用表达式 `ref() is not None`。 通常，需要使用引用对象的应用代码应当遵循这样的模式:

```
# r 是一个弱引用对象
o = r()
if o is None:
    # 引用已被作为垃圾回收
    print("Object has been deallocated; can't frobnicate.")
else:
    print("Object is still live!")
    o.do_something_useful()
```

​	使用单独的“存活”测试会在多线程应用中制造竞争条件；其他线程可能导致某个弱引用在该弱引用被调用前就失效；上述的写法在多线程应用和单线程应用中都是安全的。

​	特别版本的 [`ref`]({{< ref "/library/datatypes/weakref#weakref.ref" >}}) 对象可以通过子类化来创建。 在 [`WeakValueDictionary`]({{< ref "/library/datatypes/weakref#weakref.WeakValueDictionary" >}}) 的实现中就使用了这种方式来减少映射中每个条目的内存开销。 这对于将附加信息关联到引用的情况最为适用，但也可以被用于在调用中插入额外处理来提取引用。

​	这个例子演示了如何将 [`ref`]({{< ref "/library/datatypes/weakref#weakref.ref" >}}) 的一个子类用于存储有关对象的附加信息并在引用被访问时影响其所返回的值:

```
import weakref

class ExtendedRef(weakref.ref):
    def __init__(self, ob, callback=None, /, **annotations):
        super().__init__(ob, callback)
        self.__counter = 0
        for k, v in annotations.items():
            setattr(self, k, v)

    def __call__(self):
        """返回一个包含引用和该引用被调用次数的
        二元组。
        """
        ob = super().__call__()
        if ob is not None:
            self.__counter += 1
            ob = (ob, self.__counter)
        return ob
```



## 示例

​	这个简单的例子演示了一个应用如何使用对象 ID 来提取之前出现过的对象。 然后对象的 ID 可以在其它数据结构中使用，而无须强制对象保持存活，但处于存活状态的对象也仍然可以通过 ID 来提取。

```
import weakref

_id2obj_dict = weakref.WeakValueDictionary()

def remember(obj):
    oid = id(obj)
    _id2obj_dict[oid] = obj
    return oid

def id2obj(oid):
    return _id2obj_dict[oid]
```



## 终结器对象

​	使用 [`finalize`]({{< ref "/library/datatypes/weakref#weakref.finalize" >}}) 的主要好处在于它能更简便地注册回调函数，而无须保留所返回的终结器对象。 例如



``` python
>>> import weakref
>>> class Object:
...     pass
...
>>> kenny = Object()
>>> weakref.finalize(kenny, print, "You killed Kenny!")  
<finalize object at ...; for 'Object' at ...>
>>> del kenny
You killed Kenny!
```

​	终结器也可以被直接调用。 但是终结器最多只能对回调函数发起一次调用。



``` python
>>> def callback(x, y, z):
...     print("CALLBACK")
...     return x + y + z
...
>>> obj = Object()
>>> f = weakref.finalize(obj, callback, 1, 2, z=3)
>>> assert f.alive
>>> assert f() == 6
CALLBACK
>>> assert not f.alive
>>> f()                     # callback not called because finalizer dead
>>> del obj                 # callback not called because finalizer dead
```

​	你可以使用 [`detach()`]({{< ref "/library/datatypes/weakref#weakref.finalize.detach" >}}) 方法来注销一个终结器。 该方法将销毁终结器并返回其被创建时传给构造器的参数。



``` python
>>> obj = Object()
>>> f = weakref.finalize(obj, callback, 1, 2, z=3)
>>> f.detach()                                           
(<...Object object ...>, <function callback ...>, (1, 2), {'z': 3})
>>> newobj, func, args, kwargs = _
>>> assert not f.alive
>>> assert newobj is obj
>>> assert func(*args, **kwargs) == 6
CALLBACK
```

​	除非你将 [`atexit`]({{< ref "/library/datatypes/weakref#weakref.finalize.atexit" >}}) 属性设为 [`False`]({{< ref "/library/constants#False" >}})，否则终结器在程序退出时如果仍然存活就将被调用。 例如



``` python
>>> obj = Object()
>>> weakref.finalize(obj, print, "obj dead or exiting")
<finalize object at ...; for 'Object' at ...>
>>> exit()
obj dead or exiting
```

## 比较终结器与 [`__del__()`]({{< ref "/reference/datamodel#object.__del__" >}}) 方法

​	假设我们想创建一个类，用它的实例来代表临时目录。 当以下事件中的某一个发生时，这个目录应当与其内容一起被删除：

- 对象被作为垃圾回收，
- 对象的 `remove()` 方法被调用，或
- 程序退出。

​	我们可以像下面这样尝试使用 [`__del__()`]({{< ref "/reference/datamodel#object.__del__" >}}) 方法来实现这个类:

```
class TempDir:
    def __init__(self):
        self.name = tempfile.mkdtemp()

    def remove(self):
        if self.name is not None:
            shutil.rmtree(self.name)
            self.name = None

    @property
    def removed(self):
        return self.name is None

    def __del__(self):
        self.remove()
```

​	从 Python 3.4 开始，[`__del__()`]({{< ref "/reference/datamodel#object.__del__" >}}) 方法会不再阻止循环引用被作为垃圾回收，并且模块全局变量在 [interpreter shutdown]({{< ref "/glossary/idx#term-interpreter-shutdown" >}}) 期间不会再被强制设为 [`None`]({{< ref "/library/constants#None" >}})。 因此这段代码在 CPython 上应该会正常运行而不会出现任何问题。

​	然而，[`__del__()`]({{< ref "/reference/datamodel#object.__del__" >}}) 方法的处理严重受影响于具体实现，因为它依赖于解释器的垃圾回收实现方式的内部细节。

​	更健壮的替代方式可以是定义一个终结器，只引用它所需要的特定函数和对象，而不是获取对整个对象状态的访问权:

```
class TempDir:
    def __init__(self):
        self.name = tempfile.mkdtemp()
        self._finalizer = weakref.finalize(self, shutil.rmtree, self.name)

    def remove(self):
        self._finalizer()

    @property
    def removed(self):
        return not self._finalizer.alive
```

​	像这样定义后，我们的终结器将只接受一个对其完成正确清理目录任务所需细节的引用。 如果对象一直未被作为垃圾回收，终结器仍会在退出时被调用。

​	基于弱引用的终结器还具有另一项优势，就是它们可被用来为定义由第三方控制的类注册终结器，例如当一个模块被卸载时运行特定代码:

```
import weakref, sys
def unloading_module():
    # 从函数体隐式地引用模块的 globals
weakref.finalize(sys.modules[__name__], unloading_module)
```

​备注
 

​	如果当程序退出时你恰好在守护线程中创建终结器对象，则有可能该终结器不会在退出时被调用。 但是，在一个守护线程中 [`atexit.register()`]({{< ref "/library/python/atexit#atexit.register" >}}), `try: ... finally: ...` 和 `with: ...` 同样不能保证执行清理。
