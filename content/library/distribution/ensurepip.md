+++
title = "ensurepip --- 初始设置 pip 安装器"
date = 2024-11-15T21:09:42+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/ensurepip.html](https://docs.python.org/zh-cn/3.13/library/ensurepip.html)
>
> 收录该文档的时间：`2024-11-15T21:09:42+08:00`

# `ensurepip` --- 初始设置 `pip` 安装器

*Added in version 3.4.*

**源代码:** [Lib/ensurepip](https://github.com/python/cpython/tree/3.13/Lib/ensurepip)

------

[`ensurepip`](https://docs.python.org/zh-cn/3.13/library/ensurepip.html#module-ensurepip) 包为在已有的Python安装实例或虚拟环境中引导 `pip` 安装器提供了支持。需要使用引导才能使用pip的这一事实也正好反映了 `pip` 是一个独立的项目，有其自己的发布周期，其最新版本随CPython解释器的维护版本和新特性版本一同捆绑。

​	在大多数情况下，Python的终端使用者不需要直接调用这个模块（ `pip` 默认应该已被引导），不过，如果在安装Python（或创建虚拟环境）之时跳过了安装 `pip` 步骤，或者日后特意卸载了 `pip` ，则需要使用这个模块。

​	备注

 

​	这个模块 *无需* 访问互联网。引导启动 `pip` 所需的全部组件均包含在包的内部。

​	参见

## [安装 Python 模块](https://docs.python.org/zh-cn/3.13/installing/index.html#installing-index)

​	安装Python包的终端使用者教程

## [**PEP 453**](https://peps.python.org/pep-0453/): 在Python安装实例中显式引导启动pip

​	这个模块的原始缘由以及规范文档

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): not Android, not iOS, not WASI.

​	此模块在 [移动平台](https://docs.python.org/zh-cn/3.13/library/intro.html#mobile-availability) 或 [WebAssembly 平台](https://docs.python.org/zh-cn/3.13/library/intro.html#wasm-availability) 上不受支持。

## 命令行界面

​	使用解释器的 `-m` 参数调用命令行接口。

​	最简单的调用方式为：

```
python -m ensurepip
```

​	该调用会在当前未安装 `pip` 的情况下安装 `pip` ，如已安装则无事发生。如要确保安装的 `pip` 版本至少为 `ensurepip` 所支援的最新版本，传入 `--upgrade` 参数：

```
python -m ensurepip --upgrade
```

​	在默认情况下，`pip` 会被安装到当前虚拟环境（如果激活了虚拟环境）或系统的包目录（如果未激活虚拟环境）。 安装位置可通过两个额外的命令行选项来控制:

- `--root*dir*`: 相对于给定的根目录而不是当前已激活虚拟环境（如果存在）的根目录或当前 Python 安装版的默认根目录来安装 `pip`。
- `--user`: 将 `pip` 安装到用户包目录而不是全局安装到当前 Python 安装版（此选项不允许在已激活虚拟环境中使用）。

​	在默认情况下，脚本 `pipX` 和 `pipX.Y` 将被安装（其中 X.Y 表示被用来发起调用 `ensurepip` 的 Python 的版本）。 所安装的脚本可通过两个额外的命令行选项来控制:

- `--altinstall`: 如果请求了一个替代安装版，则 `pipX` 脚本将 *不会* 被安装。
- `--default-pip`: 如果请求了一个 "默认的 pip" 安装版，则除了两个常规脚本之外还将安装 `pip` 脚本。

​	同时提供这两个脚本选择选项将会触发异常。

## 模块 API

[`ensurepip`](https://docs.python.org/zh-cn/3.13/library/ensurepip.html#module-ensurepip) 暴露了两个函数用于编程:

## ensurepip.**version**()

​	返回一个指明在初始创建环境时将被安装的可用 pip 版本的字符串。

## ensurepip.**bootstrap**(*root=None*, *upgrade=False*, *user=False*, *altinstall=False*, *default_pip=False*, *verbosity=0*)

​	初始创建 `pip` 到当前的或指定的环境中。

*root* 指明要作为相对安装路径的替代根目录。 如果 *root* 为 `None`，则安装会使用当前环境的默认安装位置。

*upgrade* 指明是否要将一个现有的较早版本的 `pip` 的安装版升级到可用的新版本。

*user* 指明是否使用针对用户的安装方案而不是全局安装。

​	在默认情况下，将会安装 `pipX` 和 `pipX.Y` 脚本（其中 X.Y 表示 Python 的当前版本）。

​	如果设置了 *altinstall*，则 `pipX` 将 *不会* 被安装。

​	如果设置了 *default_pip*，则除了两个常规脚本外还将安装 `pip`。

​	同时设置 *altinstall* 和 *default_pip* 将触发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

*verbosity* 控制初始创建操作对 [`sys.stdout`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.stdout) 的输出信息级别。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `ensurepip.bootstrap` 并附带参数 `root`。

​	备注

 

​	创建创建过程对于 `sys.path` 和 `os.environ` 都会有附带影响。 改为在子进程中发起调用命令行接口可以避免这些附带影响。

​	备注

 

​	初始创建过程可能会安装 `pip` 所需的额外模块，但其他软件不应假定这些依赖将总是会默认存在（因为这些依赖可能会在未来的 `pip` 版本中被移除）。
