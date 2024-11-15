+++
title = "5. 在 macOS 上使用 Python"
date = 2024-11-14T22:13:29+08:00
weight = 40
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/using/mac.html](https://docs.python.org/zh-cn/3.13/using/mac.html)
>
> 收录该文档的时间：`2024-11-14T22:13:29+08:00`

# 5. 在 macOS 上使用 Python

​	This document aims to give an overview of macOS-specific behavior you should know about to get started with Python on Mac computers. Python on a Mac running macOS is very similar to Python on other Unix-derived platforms, but there are some differences in installation and some features.

​	There are various ways to obtain and install Python for macOS. Pre-built versions of the most recent versions of Python are available from a number of distributors. Much of this document describes use of the Pythons provided by the CPython release team for download from the [python.org website](https://www.python.org/downloads/). See [Alternative Distributions](https://docs.python.org/zh-cn/3.13/using/mac.html#alternative-bundles) for some other options.



## 5.1. 使用来自 `python.org` 的 macOS 版 Python

### 5.1.1. 安装步骤

​	For [current Python versions](https://www.python.org/downloads/) (other than those in `security` status), the release team produces a **Python for macOS** installer package for each new release. A list of available installers is available [here](https://www.python.org/downloads/macos/). We recommend using the most recent supported Python version where possible. Current installers provide a [universal2 binary](https://en.wikipedia.org/wiki/Universal_binary) build of Python which runs natively on all Macs (Apple Silicon and Intel) that are supported by a wide range of macOS versions, currently typically from at least **macOS 10.13 High Sierra** on.

​	The downloaded file is a standard macOS installer package file (`.pkg`). File integrity information (checksum, size, sigstore signature, etc) for each file is included on the release download page. Installer packages and their contents are signed and notarized with `Python Software Foundation` Apple Developer ID certificates to meet [macOS Gatekeeper requirements](https://support.apple.com/en-us/102445).

​	For a default installation, double-click on the downloaded installer package file. This should launch the standard macOS Installer app and display the first of several installer windows steps.

![../_images/mac_installer_01_introduction.png](https://docs.python.org/zh-cn/3.13/_images/mac_installer_01_introduction.png)

​	Clicking on the **Continue** button brings up the **Read Me** for this installer. Besides other important information, the **Read Me** documents which Python version is going to be installed and on what versions of macOS it is supported. You may need to scroll through to read the whole file. By default, this **Read Me** will also be installed in `/Applications/Python 3.13/` and available to read anytime.

![../_images/mac_installer_02_readme.png](https://docs.python.org/zh-cn/3.13/_images/mac_installer_02_readme.png)

​	Clicking on **Continue** proceeds to display the license for Python and for other included software. You will then need to **Agree** to the license terms before proceeding to the next step. This license file will also be installed and available to be read later.

![../_images/mac_installer_03_license.png](https://docs.python.org/zh-cn/3.13/_images/mac_installer_03_license.png)

​	After the license terms are accepted, the next step is the **Installation Type** display. For most uses, the standard set of installation operations is appropriate.

![../_images/mac_installer_04_installation_type.png](https://docs.python.org/zh-cn/3.13/_images/mac_installer_04_installation_type.png)

​	By pressing the **Customize** button, you can choose to omit or select certain package components of the installer. Click on each package name to see a description of what it installs. To also install support for the optional experimental free-threaded feature, see [安装自由线程二进制文件](https://docs.python.org/zh-cn/3.13/using/mac.html#install-freethreaded-macos).

![../_images/mac_installer_05_custom_install.png](https://docs.python.org/zh-cn/3.13/_images/mac_installer_05_custom_install.png)

​	In either case, clicking **Install** will begin the install process by asking permission to install new software. A macOS user name with `Administrator` privilege is needed as the installed Python will be available to all users of the Mac.

​	When the installation is complete, the **Summary** window will appear.

![../_images/mac_installer_06_summary.png](https://docs.python.org/zh-cn/3.13/_images/mac_installer_06_summary.png)

​	Double-click on the **Install Certificates.command** icon or file in the `/Applications/Python 3.13/` window to complete the installation.

![../_images/mac_installer_07_applications.png](https://docs.python.org/zh-cn/3.13/_images/mac_installer_07_applications.png)

​	This will open a temporary **Terminal** shell window that will use the new Python to download and install SSL root certificates for its use.

![../_images/mac_installer_08_install_certificates.png](https://docs.python.org/zh-cn/3.13/_images/mac_installer_08_install_certificates.png)

​	If `Successfully installed certifi` and `update complete` appears in the terminal window, the installation is complete. Close this terminal window and the installer window.

​	A default install will include:

- A `Python 3.13` folder in your `Applications` folder. In here you find **IDLE**, the development environment that is a standard part of official Python distributions; and **Python Launcher**, which handles double-clicking Python scripts from the macOS [Finder](https://support.apple.com/en-us/HT201732).
- A framework `/Library/Frameworks/Python.framework`, which includes the Python executable and libraries. The installer adds this location to your shell path. To uninstall Python, you can remove these three things. Symlinks to the Python executable are placed in `/usr/local/bin/`.

​	备注

 

​	Recent versions of macOS include a **python3** command in `/usr/bin/python3` that links to a usually older and incomplete version of Python provided by and for use by the Apple development tools, **Xcode** or the **Command Line Tools for Xcode**. You should never modify or attempt to delete this installation, as it is Apple-controlled and is used by Apple-provided or third-party software. If you choose to install a newer Python version from `python.org`, you will have two different but functional Python installations on your computer that can co-exist. The default installer options should ensure that its **python3** will be used instead of the system **python3**.

### 5.1.2. 如何运行 Python 脚本

​	There are two ways to invoke the Python interpreter. If you are familiar with using a Unix shell in a terminal window, you can invoke `python3.13` or `python3` optionally followed by one or more command line options (described in [命令行与环境](https://docs.python.org/zh-cn/3.13/using/cmdline.html#using-on-general)). The Python tutorial also has a useful section on [using Python interactively from a shell](https://docs.python.org/zh-cn/3.13/tutorial/appendix.html#tut-interac).

​	You can also invoke the interpreter through an integrated development environment. [IDLE](https://docs.python.org/zh-cn/3.13/library/idle.html#idle) is a basic editor and interpreter environment which is included with the standard distribution of Python. **IDLE** includes a Help menu that allows you to access Python documentation. If you are completely new to Python, you can read the tutorial introduction in that document.

​	There are many other editors and IDEs available, see [编辑器和集成开发环境](https://docs.python.org/zh-cn/3.13/using/editors.html#editors) for more information.

​	To run a Python script file from the terminal window, you can invoke the interpreter with the name of the script file:

> ```
> python3.13` `myscript.py
> ```

​	要从 Finder 运行你的脚本，你有两种选择：

- 将其拖拽到 **Python Launcher**。
- Select **Python Launcher** as the default application to open your script (or any `.py` script) through the Finder Info window and double-click it. **Python Launcher** has various preferences to control how your script is launched. Option-dragging allows you to change these for one invocation, or use its `Preferences` menu to change things globally.

​	Be aware that running the script directly from the macOS Finder might produce different results than when running from a terminal window as the script will not be run in the usual shell environment including any setting of environment variables in shell profiles. And, as with any other script or program, be certain of what you are about to run.



## 5.2. Alternative Distributions

​	Besides the standard `python.org` for macOS installer, there are third-party distributions for macOS that may include additional functionality. Some popular distributions and their key features:

## [ActivePython](https://www.activestate.com/products/python/)

​	具有多平台兼容性的安装器，文档

## [Anaconda](https://www.anaconda.com/download/)

​	Popular scientific modules (such as numpy, scipy, and pandas) and the `conda` package manager.

## [Homebrew](https://brew.sh/)

​	Package manager for macOS including multiple versions of Python and many third-party Python-based packages (including numpy, scipy, and pandas).

## [MacPorts](https://www.macports.org/)

​	Another package manager for macOS including multiple versions of Python and many third-party Python-based packages. May include pre-built versions of Python and many packages for older versions of macOS.

​	Note that distributions might not include the latest versions of Python or other libraries, and are not maintained or supported by the core Python team.



## 5.3. 安装额外的 Python 包

​	Refer to the [Python Packaging User Guide](https://packaging.python.org/en/latest/tutorials/installing-packages/) for more information.



## 5.4. GUI 编程

​	使用 Python 在 Mac 上构建 GUI 应用程序有多种选择。

​	The standard Python GUI toolkit is [`tkinter`](https://docs.python.org/zh-cn/3.13/library/tkinter.html#module-tkinter), based on the cross-platform Tk toolkit ([https://www.tcl.tk](https://www.tcl.tk/)). A macOS-native version of Tk is included with the installer.

*PyObjC* is a Python binding to Apple's Objective-C/Cocoa framework. Information on PyObjC is available from [pyobjc](https://pypi.org/project/pyobjc/).

​	A number of alternative macOS GUI toolkits are available including:

- [PySide](https://www.qt.io/qt-for-python): Official Python bindings to the [Qt GUI toolkit](https://wiki.qt.io/Qt_for_Python).
- [PyQt](https://riverbankcomputing.com/software/pyqt/): Alternative Python bindings to Qt.
- [Kivy](https://kivy.org/): A cross-platform GUI toolkit that supports desktop and mobile platforms.
- [Toga](https://toga.readthedocs.io/): Part of the [BeeWare Project](https://beeware.org/); supports desktop, mobile, web and console apps.
- [wxPython](https://wxpython.org/): A cross-platform toolkit that supports desktop operating systems.

## 5.5. 进阶



### 5.5.1. 安装自由线程二进制文件

*Added in version 3.13:* （试验性功能）

​	备注

 

​	本节中描述的所有内容都是试验性的，它们预计会在未来的发布版中发生改变。

​	The `python.org` [Python for macOS](https://docs.python.org/zh-cn/3.13/using/mac.html#getting-and-installing-macpython) installer package can optionally install an additional build of Python 3.13 that supports [**PEP 703**](https://peps.python.org/pep-0703/), the experimental free-threading feature (running with the [global interpreter lock](https://docs.python.org/zh-cn/3.13/glossary.html#term-global-interpreter-lock) disabled). Check the release page on `python.org` for possible updated information.

​	Because this feature is still considered experimental, the support for it is not installed by default. It is packaged as a separate install option, available by clicking the **Customize** button on the **Installation Type** step of the installer as described above.

![../_images/mac_installer_09_custom_install_free_threaded.png](https://docs.python.org/zh-cn/3.13/_images/mac_installer_09_custom_install_free_threaded.png)

​	If the box next to the **Free-threaded Python** package name is checked, a separate `PythonT.framework` will also be installed alongside the normal `Python.framework` in `/Library/Frameworks`. This configuration allows a free-threaded Python 3.13 build to co-exist on your system with a traditional (GIL only) Python 3.13 build with minimal risk while installing or testing. This installation layout is itself experimental and is subject to change in future releases.

​	Known cautions and limitations:

- The **UNIX command-line tools** package, which is selected by default, will install links in `/usr/local/bin` for `python3.13t`, the free-threaded interpreter, and `python3.13t-config`, a configuration utility which may be useful for package builders. Since `/usr/local/bin` is typically included in your shell `PATH`, in most cases no changes to your `PATH` environment variables should be needed to use `python3.13t`.

- For this release, the **Shell profile updater** package and the `Update Shell Profile.command` in `/Applications/Python 3.13/` do not support the free-threaded package.

- The free-threaded build and the traditional build have separate search paths and separate `site-packages` directories so, by default, if you need a package available in both builds, it may need to be installed in both. The free-threaded package will install a separate instance of **pip** for use with `python3.13t`.

  - To install a package using **pip** without a **venv**:

    > `python3.13t` `-m pip install <package_name>`

- When working with multiple Python environments, it is usually safest and easiest to [create and use virtual environments](https://docs.python.org/zh-cn/3.13/tutorial/venv.html#tut-venv). This can avoid possible command name conflicts and confusion about which Python is in use:

  > `python3.13t` `-m venv <venv_name>`

  然后执行 **activate**。

- 要运行 IDLE 的自由线程版本：

  > `python3.13t` `-m idlelib`

- The interpreters in both builds respond to the same [PYTHON environment variables](https://docs.python.org/zh-cn/3.13/using/cmdline.html#using-on-envvars) which may have unexpected results, for example, if you have `PYTHONPATH` set in a shell profile. If necessary, there are [command line options](https://docs.python.org/zh-cn/3.13/using/cmdline.html#using-on-interface-options) like `-E` to ignore these environment variables.

- The free-threaded build links to the third-party shared libraries, such as `OpenSSL` and `Tk`, installed in the traditional framework. This means that both builds also share one set of trust certificates as installed by the **Install Certificates.command** script, thus it only needs to be run once.

- If you cannot depend on the link in `/usr/local/bin` pointing to the `python.org` free-threaded `python3.13t` (for example, if you want to install your own version there or some other distribution does), you can explicitly set your shell `PATH` environment variable to include the `PythonT` framework `bin` directory:

  ```
  export PATH="/Library/Frameworks/PythonT.framework/Versions/3.13/bin":"$PATH"
  ```

  The traditional framework installation by default does something similar, except for `Python.framework`. Be aware that having both framework `bin` directories in `PATH` can lead to confusion if there are duplicate names like `python3.13` in both; which one is actually used depends on the order they appear in `PATH`. The `which python3.x` or `which python3.xt` commands can show which path is being used. Using virtual environments can help avoid such ambiguities. Another option might be to create a shell **alias** to the desired interpreter, like:

  ```
  alias py3.13="/Library/Frameworks/Python.framework/Versions/3.13/bin/python3.13"
  alias py3.13t="/Library/Frameworks/PythonT.framework/Versions/3.13/bin/python3.13t"
  ```

### 5.5.2. Installing using the command line

​	If you want to use automation to install the `python.org` installer package (rather than by using the familiar macOS **Installer** GUI app), the macOS command line **installer** utility lets you select non-default options, too. If you are not familiar with **installer**, it can be somewhat cryptic (see **man installer** for more information). As an example, the following shell snippet shows one way to do it, using the `3.13.0b2` release and selecting the free-threaded interpreter option:

```
RELEASE="python-3.13.0b2-macos11.pkg"

# 下载安装器 pkg
curl -O https://www.python.org/ftp/python/3.13.0/${RELEASE}

# 创建安装器 choicechanges 文件来定制安装：
#    启用 PythonTFramework-3.13 包
#    并接受其他默认选项（安装所有其他包）
cat > ./choicechanges.plist <<EOF
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<array>
        <dict>
                <key>attributeSetting</key>
                <integer>1</integer>
                <key>choiceAttribute</key>
                <string>selected</string>
                <key>choiceIdentifier</key>
                <string>org.python.Python.PythonTFramework-3.13</string>
        </dict>
</array>
</plist>
EOF

sudo installer -pkg ./${RELEASE} -applyChoiceChangesXML ./choicechanges.plist -target /
```

​	接下来你可以这样测试两个安装器构建版现在是否可用：

```
$ # 当 Unix Command Tools 包被启用时测试自由线程解释器是否已安装
$ /usr/local/bin/python3.13t -VV
Python 3.13.0b2 experimental free-threading build (v3.13.0b2:3a83b172af, Jun  5 2024, 12:57:31) [Clang 15.0.0 (clang-1500.3.9.4)]
$ # 并测试传统解释器
$ /usr/local/bin/python3.13 -VV
Python 3.13.0b2 (v3.13.0b2:3a83b172af, Jun  5 2024, 12:50:24) [Clang 15.0.0 (clang-1500.3.9.4)]
$ # 当 /usr/local/bin 在 $PATH 中时测试它们在不带前缀的情况下是否可用
$ python3.13t -VV
Python 3.13.0b2 experimental free-threading build (v3.13.0b2:3a83b172af, Jun  5 2024, 12:57:31) [Clang 15.0.0 (clang-1500.3.9.4)]
$ python3.13 -VV
Python 3.13.0b2 (v3.13.0b2:3a83b172af, Jun  5 2024, 12:50:24) [Clang 15.0.0 (clang-1500.3.9.4)]
```

​	备注

 

​	当前的 `python.org` 安装器只会安装到固定位置如 `/Library/Frameworks/`, `/Applications` 和 `/usr/local/bin`。 你不能使用 **installer** `-domain` 选项来安装到其他位置。



### 5.5.3. 分发 Python 应用程序

​	有一系列工具可将你的 Python 代码转换为独立发布的应用程序：

- [py2app](https://pypi.org/project/py2app/): 支持基于 Python 项目创建 macOS `.app` 软件包。
- [Briefcase](https://briefcase.readthedocs.io/): [BeeWare 项目](https://beeware.org/) 的一部分；一款支持在 macOS 上创建 `.app` 捆绑包，并能管理签名和公证的跨平台打包工具。
- [PyInstaller](https://pyinstaller.org/): 一款可创建单独文件或文件夹作为可分发包的跨平台打包工具。

### 5.5.4. App Store 合规性

​	在 macOS App Store 中提交发布的 app 必须通过 Apple 的 app 审核进程。 此进程包括一组在所提交的应用程序包中自动检查有问题代码的验证规则。

​	Python 标准库包含了一些已知会违反这些自动规则的代码。 虽然这些违规情况看来是属于误报，但 Apple 的审核规则是不可挑战的。 因此，有必要修改 Python 标准库以便 app 能够通过 App Store 的审核。

​	Python 源代码树包含 [一个补丁文件](https://github.com/python/cpython/tree/3.13/Mac/Resources/app-store-compliance.patch) 可移除所有已知的会导致 App Store 审核过程出现问题的代码。 这个补丁会在 CPython 配置了 [`--with-app-store-compliance`](https://docs.python.org/zh-cn/3.13/using/configure.html#cmdoption-with-app-store-compliance) 选项时自动应用。

​	这个补丁对于在 Mac 上使用 CPython 并不是必需的；如果你是在 macOS App Store *以外* 的地方发布 app 它也不是必需的。 它 *只有* 在你使用 macOS App Store 作为发布渠道时才是必需的。

## 5.6. 其他资源

​	The [python.org Help page](https://www.python.org/about/help/) has links to many useful resources. The [Pythonmac-SIG mailing list](https://www.python.org/community/sigs/current/pythonmac-sig/) is another support resource specifically for Python users and developers on the Mac.
