+++
title = "6. 在Android上使用 Python"
date = 2024-11-14T22:13:29+08:00
weight = 50
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/using/android.html](https://docs.python.org/zh-cn/3.13/using/android.html)
>
> 收录该文档的时间：`2024-11-14T22:13:29+08:00`

# 6. 在Android上使用 Python

​	Python 在 Android 上与桌面平台上不同。 在桌面平台上，通常是作为系统资源安装的，该计算机的任何用户都可以使用。 然后，用户通过运行 **python** 可执行文件并交互提示器中输入命令 ，或运行脚本。

​	在安卓系统中，没有将安装作为系统资源的概念。 软件分发的唯一单位是"应用程序"。 也没有可以运行 **python** 可执行文件或与 Python REPL 交互的控制台。

​	As a result, the only way you can use Python on Android is in embedded mode – that is, by writing a native Android application, embedding a Python interpreter using `libpython`, and invoking Python code using the [Python embedding API]({{< ref "/extending/embedding#embedding" >}}). The full Python interpreter, the standard library, and all your Python code is then packaged into your app for its own private use.

​	Python 标准库在 Android 上有一些明显的遗漏和限制。 详情参见 [API 可用性指南]({{< ref "/library/intro#mobile-availability" >}})。

## 6.1. 添加Python到Android app

​	只有当您打算自己编译 Python for Android 时，才需要使用这些指令。 大多数用户应该 *不需要* 这样做。 相反，使用以下工具之一会带来更轻松的体验：

- 来自 BeeWare 项目的 [Briefcase](https://briefcase.readthedocs.io/)
- 来自 Kivy 项目的 [Buildozer](https://buildozer.readthedocs.io/)
- [Chaquopy](https://chaquo.com/chaquopy)
- [pyqtdeploy](https://www.riverbankcomputing.com/static/Docs/pyqtdeploy/)
- [Termux](https://termux.dev/en/)

​	如果您确定要手动完成所有这些操作，请继续阅读。您可以使用 [testbed app](https://github.com/python/cpython/tree/3.13/Android/testbed) 作为指南；下面的每个步骤都包含相关文件的链接。

- 构建 Python 请按照 [Android/README.md](https://github.com/python/cpython/tree/3.13/Android/README.md) 中的指令进行操作。
- 在您的 [build.gradle](https://github.com/python/cpython/tree/3.13/Android/testbed/app/build.gradle.kts) 文件中添加代码，将以下项目复制到您的项目中。 除您自己的 Python 代码外，其他代码均可从 `cross-build/HOST/prefix/lib` 复制：
  - 在 JNI 库中:
    - `libpython*.*.so`
    - `lib*_python.so` (外部库，如 OpenSSL）
  - 在您的资源文件中：
    - `python*.*` (Python 标准库)
    - `python*.*/site-packages` （您自己的 Python 代码）
- 在应用程序中添加代码以 [提取资源文件到文件系统](https://github.com/python/cpython/tree/3.13/Android/testbed/app/src/main/java/org/python/testbed/MainActivity.kt)。
- 在应用程序中添加代码 [以嵌入模式启动 Python](https://github.com/python/cpython/tree/3.13/Android/testbed/app/src/main/c/main_activity.c)。 这需要通过 JNI 调用 C 代码。
