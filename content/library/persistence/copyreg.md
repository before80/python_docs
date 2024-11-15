+++
title = "copyreg --- 注册 pickle 支持函数"
date = 2024-11-15T11:57:40+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/copyreg.html](https://docs.python.org/zh-cn/3.13/library/copyreg.html)
>
> 收录该文档的时间：`2024-11-15T11:57:40+08:00`

# `copyreg` --- 注册 `pickle` 支持函数

**源代码:** [Lib/copyreg.py](https://github.com/python/cpython/tree/3.13/Lib/copyreg.py)

------

[`copyreg`](https://docs.python.org/zh-cn/3.13/library/copyreg.html#module-copyreg) 模块提供了可在封存特定对象时使用的一种定义函数方式。 [`pickle`](https://docs.python.org/zh-cn/3.13/library/pickle.html#module-pickle) 和 [`copy`](https://docs.python.org/zh-cn/3.13/library/copy.html#module-copy) 模块会在封存/拷贝特定对象时使用这些函数。 此模块提供了非类对象构造器的相关配置信息。 这样的构造器可以是工厂函数或类实例。

## copyreg.**constructor**(*object*)

​	将 *object* 声明为一个有效的构造器。 如果 *object* 是不可调用的（因而不是一个有效的构造器）则会引发 [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError)。

## copyreg.**pickle**(*type*, *function*, *constructor_ob=None*)

​	声明 *function* 应当被用作 *type* 类型的对象的“归约”函数。 *function* 必须返回一个字符串或包含二至六个元素的元组。 请参阅 [`dispatch_table`](https://docs.python.org/zh-cn/3.13/library/pickle.html#pickle.Pickler.dispatch_table) 了解有关 *function* 的接口的更多细节。

*constructor_ob* 形参是一个旧式特性并且现在会被忽略，但如果传入则它必须为一个可调用对象。

​	请注意一个 pickler 或 [`pickle.Pickler`](https://docs.python.org/zh-cn/3.13/library/pickle.html#pickle.Pickler) 的子类的 [`dispatch_table`](https://docs.python.org/zh-cn/3.13/library/pickle.html#pickle.Pickler.dispatch_table) 属性也可以被用来声明约归函数。

## 示例

​	以下示例将会显示如何注册一个封存函数，以及如何来使用它：



```
>>> import copyreg, copy, pickle
>>> class C:
...     def __init__(self, a):
...         self.a = a
...
>>> def pickle_c(c):
...     print("pickling a C instance...")
...     return C, (c.a,)
...
>>> copyreg.pickle(C, pickle_c)
>>> c = C(1)
>>> d = copy.copy(c)  
pickling a C instance...
>>> p = pickle.dumps(c)  
pickling a C instance...
```
