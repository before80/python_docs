+++
title = "__future__ --- Future 语句定义"
date = 2024-11-15T21:12:39+08:00
weight = 110
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/__future__.html](https://docs.python.org/zh-cn/3.13/library/__future__.html)
>
> 收录该文档的时间：`2024-11-15T21:12:39+08:00`

# `__future__` --- Future 语句定义

**源代码：** [Lib/__future__.py](https://github.com/python/cpython/tree/3.13/Lib/__future__.py)

------

`from __future__ import feature` 形式的导入被称为 [future 语句](https://docs.python.org/zh-cn/3.13/reference/simple_stmts.html#future)。 它们会被 Python 编译器当作特例，通过包含 future 语句来允许新的 Python 特性在该特性成为语言标准之前发布的模块中使用。

​	虽然这些future 语句被 Python 编译器赋予了额外的特殊含义，但它们仍然像会其他导入语句一样被执行，而 [`__future__`](https://docs.python.org/zh-cn/3.13/library/__future__.html#module-__future__) 的存在和被导入系统处理的方式与其他任何 Python 模块的相同。 这种设计有三个目的：

- 避免混淆已有的分析 import 语句并查找 import 的模块的工具。
- 当引入不兼容的修改时，可以记录其引入的时间以及强制使用的时间。这是一种可执行的文档，并且可以通过 import [`__future__`](https://docs.python.org/zh-cn/3.13/library/__future__.html#module-__future__) 来做程序性的检查。
- 确保 [future 语句](https://docs.python.org/zh-cn/3.13/reference/simple_stmts.html#future) 在 Python 2.1 之前的发布版上运行时至少能抛出运行时异常（对 [`__future__`](https://docs.python.org/zh-cn/3.13/library/__future__.html#module-__future__) 的导入将失败，因为will fail, because there was no module of that name prior to 2.1 之前没有这个模块名称）。

## 模块内容

[`__future__`](https://docs.python.org/zh-cn/3.13/library/__future__.html#module-__future__) 中不会删除特性的描述。从 Python 2.1 中首次加入以来，通过这种方式引入了以下特性：

| 特性             | 可选版本 | 强制加入版本                                                 | 效果                                                         |
| :--------------- | :------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| nested_scopes    | 2.1.0b1  | 2.2                                                          | [**PEP 227**](https://peps.python.org/pep-0227/): *静态嵌套作用域* |
| generators       | 2.2.0a1  | 2.3                                                          | [**PEP 255**](https://peps.python.org/pep-0255/): *简单生成器* |
| division         | 2.2.0a2  | 3.0                                                          | [**PEP 238**](https://peps.python.org/pep-0238/): *修改除法运算符* |
| absolute_import  | 2.5.0a1  | 3.0                                                          | [**PEP 328**](https://peps.python.org/pep-0328/): *导入：多行与绝对/相对* |
| with_statement   | 2.5.0a1  | 2.6                                                          | [**PEP 343**](https://peps.python.org/pep-0343/): * "with" 语句* |
| print_function   | 2.6.0a2  | 3.0                                                          | [**PEP 3105**](https://peps.python.org/pep-3105/): *print 改为函数* |
| unicode_literals | 2.6.0a2  | 3.0                                                          | [**PEP 3112**](https://peps.python.org/pep-3112/): *Python 3000 中的字节字面值* |
| generator_stop   | 3.5.0b1  | 3.7                                                          | [**PEP 479**](https://peps.python.org/pep-0479/): *在生成器中处理 StopIteration* |
| annotations      | 3.7.0b1  | TBD [[1\]](https://docs.python.org/zh-cn/3.13/library/__future__.html#id2) | [**PEP 563**](https://peps.python.org/pep-0563/): *Postponed evaluation of annotations* |

## *class* __future__.**_Feature**

`__future__.py` 中的每一条语句都是以下格式的：

```
FeatureName = _Feature(OptionalRelease, MandatoryRelease,
                       CompilerFlag)
```

​	通常 *OptionalRelease* 要比 *MandatoryRelease* 小，并且都是和 [`sys.version_info`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.version_info) 格式一致的 5 元素元组。

```
(PY_MAJOR_VERSION, # 2.1.0a3 中的 2; 一个整数
 PY_MINOR_VERSION, # 1; 一个整数
 PY_MICRO_VERSION, # 0; 一个整数
 PY_RELEASE_LEVEL, # "alpha", "beta", "candidate" 或 "final"; 字符串
 PY_RELEASE_SERIAL # 3; 一个整数
)
```

## _Feature.**getOptionalRelease**()

*OptionalRelease* 记录了一个特性首次发布时的 Python 版本。

## _Feature.**getMandatoryRelease**()

​	在 *MandatoryRelases* 还没有发布时，*MandatoryRelease* 表示该特性会变成语言的一部分的预测时间。

​	其他情况下，*MandatoryRelease* 用来记录这个特性是何时成为语言的一部分的。从该版本往后，使用该特性将不需要 future 语句，不过很多人还是会加上对应的 import。

*MandatoryRelease* 也可能为 `None`，表示计划中的特性被撤销或尚未确定。

## _Feature.**compiler_flag**

*CompilerFlag* 是一个（位）旗标，对于动态编译的代码应当将其作为第四个参数传给内置函数 [`compile()`](https://docs.python.org/zh-cn/3.13/library/functions.html#compile) 以启用相应的特性。 该旗标存储在 [`_Feature`](https://docs.python.org/zh-cn/3.13/library/__future__.html#future__._Feature) 实例的 [`_Feature.compiler_flag`](https://docs.python.org/zh-cn/3.13/library/__future__.html#future__._Feature.compiler_flag) 属性中。

[[1](https://docs.python.org/zh-cn/3.13/library/__future__.html#id1)]

​	先前计划在 Python 3.10 中强制使用 `from __future__ import annotations` ，但Python指导委员会两次决定推迟这一改变（ [Python 3.10 的公告](https://mail.python.org/archives/list/python-dev@python.org/message/CLVXXPQ2T2LQ5MP2Y53VVQFCXYWQJHKZ/) ； [Python 3.11 的公告](https://mail.python.org/archives/list/python-dev@python.org/message/VIZEBX5EYMSYIJNDBF6DMUMZOCWHARSO/) ）。目前还没有做出最终决定。参见 [**PEP 563**](https://peps.python.org/pep-0563/) 和 [**PEP 649**](https://peps.python.org/pep-0649/) 。

> 参见
>
> 
>
> - [future 语句](https://docs.python.org/zh-cn/3.13/reference/simple_stmts.html#future)
>
>   编译器怎样处理 future import。
>
> - [**PEP 236**](https://peps.python.org/pep-0236/) - 回到 __future__
>
>   有关 __future__ 机制的最初提议。
