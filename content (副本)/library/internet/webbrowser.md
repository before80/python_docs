+++
title = "webbrowser --- 方便的 Web 浏览器控制工具"
date = 2024-11-15T20:28:46+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/webbrowser.html](https://docs.python.org/zh-cn/3.13/library/webbrowser.html)
>
> 收录该文档的时间：`2024-11-15T20:28:46+08:00`

# `webbrowser` --- 方便的 Web 浏览器控制工具

**源码：** [Lib/webbrowser.py](https://github.com/python/cpython/tree/3.13/Lib/webbrowser.py)

------

[`webbrowser`]({{< ref "/library/internet/webbrowser#module-webbrowser" >}}) 模块提供了一个高层级接口，允许向用户显示基于 Web 的文档。 在大多数情况下，只需调用此模块的 [`open()`]({{< ref "/library/internet/webbrowser#webbrowser.open" >}}) 函数就可以了。

​	在 Unix 下，图形浏览器在 X11 下是首选，但如果图形浏览器不可用或 X11 显示不可用，则将使用文本模式浏览器。 如果使用文本模式浏览器，则调用进程将阻塞，直到用户退出浏览器。

​	如果存在环境变量 `BROWSER` ，则将其解释为 [`os.pathsep`]({{< ref "/library/allos/os#os.pathsep" >}}) 分隔的浏览器列表，以便在平台默认值之前尝试。 当列表部分的值包含字符串 `％s` 时，它被解释为一个文字浏览器命令行，用于替换 `％s` 的参数 URL ；如果该部分不包含 `％s`，则它只被解释为要启动的浏览器的名称。 [[1\]]({{< ref "/library/internet/webbrowser#id2" >}})

​	对于非 Unix 平台，或者当 Unix 上有远程浏览器时，控制过程不会等待用户完成浏览器，而是允许远程浏览器在显示界面上维护自己的窗口。 如果 Unix 上没有远程浏览器，控制进程将启动一个新的浏览器并等待。

​	在 iOS 上，`BROWSER` 环境变量以及任何控制自动唤起、浏览器首选项和新选项卡/窗口创建的参数都将被忽略。 Web 页面将 *总是* 在用户首选的浏览器中打开一个新选项卡，并且此浏览器将被切换到前台。 在 iOS 上使用 [`webbrowser`]({{< ref "/library/internet/webbrowser#module-webbrowser" >}}) 模块需要有 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 模块。 如果 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 不可用，对 [`open()`]({{< ref "/library/internet/webbrowser#webbrowser.open" >}}) 的调用将会失败。

​	脚本 **webbrowser** 可作为该模块的命令行接口来使用。 它接受一个 URL 作为参数。 它还接受下列可选形参：

- `-n`/`--new-window` 在新的浏览器窗口中打开 URL，如果可能的话。
- `-t`/`--new-tab` 在新的浏览器页面 ("tab") 中打开 URL。

​	很自然地，该选项是互斥的。 用法示例:

```
python -m webbrowser -t "https://www.python.org"
```

[Availability]({{< ref "/library/intro#availability" >}}): not WASI, not Android.

​	定义了以下异常：

## *exception* webbrowser.**Error**

​	发生浏览器控件错误时引发异常。

​	定义了以下函数：

## webbrowser.**open**(*url*, *new=0*, *autoraise=True*)

​	使用默认浏览器显示 *url*。 如果 *new* 为 0，则尽可能在同一浏览器窗口中打开 *url*。 如果 *new* 为 1，则尽可能打开新的浏览器窗口。 如果 *new* 为 2，则尽可能打开新的浏览器页面（“标签”）。 如果 *autoraise* 为 “True”，则会尽可能置前窗口（请注意，在许多窗口管理器下，无论此变量的设置如何，都会置前窗口）。

​	如果浏览器成功启动则返回 `True`，否则返回 `False`。

​	请注意，在某些平台上，尝试使用此函数打开文件名，可能会起作用并启动操作系统的关联程序。 但是，这种方式不被支持也不可移植。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `webbrowser.open` 并附带参数 `url`。

## webbrowser.**open_new**(*url*)

​	如果可能，在默认浏览器的新窗口中打开 *url*，否则，在唯一的浏览器窗口中打开 *url*。

​	如果浏览器成功启动则返回 `True`，否则返回 `False`。

## webbrowser.**open_new_tab**(*url*)

​	如果可能，在默认浏览器的新页面（“标签”）中打开 *url*，否则等效于 [`open_new()`]({{< ref "/library/internet/webbrowser#webbrowser.open_new" >}})。

​	如果浏览器成功启动则返回 `True`，否则返回 `False`。

## webbrowser.**get**(*using=None*)

​	返回浏览器类型为 *using* 指定的控制器对象。 如果 *using* 为 `None`，则返回适用于调用者环境的默认浏览器的控制器。

## webbrowser.**register**(*name*, *constructor*, *instance=None*, ***, *preferred=False*)

​	注册 *name* 浏览器类型。 注册浏览器类型后， [`get()`]({{< ref "/library/internet/webbrowser#webbrowser.get" >}}) 函数可以返回该浏览器类型的控制器。 如果没有提供 *instance*，或者为 `None`，*constructor* 将在没有参数的情况下被调用，以在需要时创建实例。 如果提供了 *instance*，则永远不会调用 *constructor*，并且可能是 `None`。

​	将 *preferred* 设置为 `True` 使得这个浏览器成为 [`get()`]({{< ref "/library/internet/webbrowser#webbrowser.get" >}}) 不带参数调用的首选结果。 否则，只有在您计划设置 `BROWSER` 变量，或使用与您声明的处理程序的名称相匹配的非空参数调用 [`get()`]({{< ref "/library/internet/webbrowser#webbrowser.get" >}}) 时，此入口点才有用。

*在 3.7 版本发生变更:* 添加了仅关键字参数 *preferred*。

​	预定义了许多浏览器类型。 此表给出了可以传递给 [`get()`]({{< ref "/library/internet/webbrowser#webbrowser.get" >}}) 函数的类型名称以及控制器类的相应实例化，这些都在此模块中定义。

| 类型名               | 类名                           | 备注 |
| :------------------- | :----------------------------- | :--- |
| `'mozilla'`          | `Mozilla('mozilla')`           |      |
| `'firefox'`          | `Mozilla('mozilla')`           |      |
| `'epiphany'`         | `Epiphany('epiphany')`         |      |
| `'kfmclient'`        | `Konqueror()`                  | (1)  |
| `'konqueror'`        | `Konqueror()`                  | (1)  |
| `'kfm'`              | `Konqueror()`                  | (1)  |
| `'opera'`            | `Opera()`                      |      |
| `'links'`            | `GenericBrowser('links')`      |      |
| `'elinks'`           | `Elinks('elinks')`             |      |
| `'lynx'`             | `GenericBrowser('lynx')`       |      |
| `'w3m'`              | `GenericBrowser('w3m')`        |      |
| `'windows-default'`  | `WindowsDefault`               | (2)  |
| `'macosx'`           | `MacOSXOSAScript('default')`   | (3)  |
| `'safari'`           | `MacOSXOSAScript('safari')`    | (3)  |
| `'google-chrome'`    | `Chrome('google-chrome')`      |      |
| `'chrome'`           | `Chrome('chrome')`             |      |
| `'chromium'`         | `Chromium('chromium')`         |      |
| `'chromium-browser'` | `Chromium('chromium-browser')` |      |
| `'iosbrowser'`       | `IOSBrowser`                   | (4)  |

​	注释：

1. "Konqueror" 是用于 Unix 的 KDE 桌面环境的文件管理器，只有在运行了 KDE 时才有意义。 某些能可靠地检测 KDE 的方法会很有用处；仅检查 `KDEDIR` 变量是不够的。 还要注意即使在使用 KDE 2 的 **konqueror** 命令时会使用也会使用 "kfm" 这一名称 --- 该实现会为运行 Konqueror 选择最佳的策略。
2. 仅限 Windows 平台。
3. 仅限于 macOS。
4. 仅限于 iOS。

> Added in version 3.2:
> 在 Mac 上会使用新增的 `MacOSXOSAScript` 类而不是之前的 `MacOSX` 类。 它增加了对打开当前未被设为 OS 默认首选项的浏览器的支持。

> Added in version 3.3:
> 添加了对 Chrome/Chromium 的支持。

*在 3.12 版本发生变更:* 对某些过时浏览器的支持已被移除。 被移除的浏览器包括 Grail, Mosaic, Netscape, Galeon, Skipstone, Iceape 和 Firefox 35 及以下的版本。

*在 3.13 版本发生变更:* 添加了对 iOS 的支持。

​	以下是一些简单的例子:

```
url = 'https://docs.python.org/'

# 在新选项卡中打开 URL，如果已打开一个浏览器窗口的话。
webbrowser.open_new_tab(url)

# 在新窗口中打开 URL，如有可能将启动窗口。
webbrowser.open_new(url)
```



## 浏览器控制器对象

​	浏览器控制器提供三个与模块级便捷函数相同的方法：

## controller.**name**

​	浏览器依赖于系统的名称。

## controller.**open**(*url*, *new=0*, *autoraise=True*)

​	使用此控制器处理的浏览器显示 *url*。 如果 *new* 为 1，则尽可能打开新的浏览器窗口。 如果 *new* 为 2，则尽可能打开新的浏览器页面（“标签”）。

## controller.**open_new**(*url*)

​	如果可能，在此控制器处理的浏览器的新窗口中打开 *url* ，否则，在唯一的浏览器窗口中打开 *url* 。 别名 [`open_new()`]({{< ref "/library/internet/webbrowser#webbrowser.open_new" >}})。

## controller.**open_new_tab**(*url*)

​	如果可能，在此控制器处理的浏览器的新页面（“标签”）中打开 *url*，否则等效于 [`open_new()`]({{< ref "/library/internet/webbrowser#webbrowser.open_new" >}})。

​	备注

[[1]({{< ref "/library/internet/webbrowser#id1" >}})]

​	这里命名的不带完整路径的可执行文件将在 `PATH` 环境变量给出的目录中搜索。
