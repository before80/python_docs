+++
title = "venv --- 虚拟环境的创建"
date = 2024-11-15T21:09:42+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/venv.html](https://docs.python.org/zh-cn/3.13/library/venv.html)
>
> 收录该文档的时间：`2024-11-15T21:09:42+08:00`

# `venv` --- 虚拟环境的创建

> Added in version 3.3.
>

**源码：** [Lib/venv/](https://github.com/python/cpython/tree/3.13/Lib/venv/)

------

`venv` 模块支持创建轻量的“虚拟环境”，每个虚拟环境将拥有它们自己独立的安装在其 [`site`](https://docs.python.org/zh-cn/3.13/library/site.html#module-site) 目录中的 Python 软件包集合。 虚拟环境是在现有的 Python 安装版基础之上创建的，这被称为虚拟环境的“基础”Python，并且还可选择与基础环境中的软件包隔离开来，这样只有在虚拟环境中显式安装的软件包才是可用的。

​	当在虚拟环境中使用时，常见安装工具如 [pip](https://pypi.org/project/pip/) 将把 Python 软件包安装到虚拟环境而无需显式地指明这一点。

​	虚拟环境是（主要的特性）：

- 用来包含支持一个项目（库或应用程序）所需的特定 Python 解释器、软件库和二进制文件。 它们在默认情况下与其他虚拟环境中的软件以及操作系统中安装的 Python 解释器和库保持隔离。
- 包含在一个目录中，根据惯例被命名为项目目录下的 `.venv` 或 `venv`，或是有许多虚拟环境的容器目录下，如 `~/.virtualenvs`。
- 不可签入 Git 等源代码控制系统。
- 被认为是可丢弃的 -- 它应当能被简单地删除并从头开始重建。 你不应在此环境中放置任何项目代码。
- 不被视为是可移动或可复制的 —— 你只能在目标位置重建相同的环境。

​	请参阅 [**PEP 405**](https://peps.python.org/pep-0405/) 了解有关 Python 虚拟环境的更多背景信息。

​	参见

 

[Python Packaging User Guide: Creating and using virtual environments](https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/#create-and-use-virtual-environments)

[Availability](https://docs.python.org/zh-cn/3.13/library/intro.html#availability): not Android, not iOS, not WASI.

​	此模块在 [移动平台](https://docs.python.org/zh-cn/3.13/library/intro.html#mobile-availability) 或 [WebAssembly 平台](https://docs.python.org/zh-cn/3.13/library/intro.html#wasm-availability) 上不受支持。

## 创建虚拟环境

[虚拟环境](https://docs.python.org/zh-cn/3.13/library/venv.html#venv-def) 是通过执行 `venv` 模块来创建的：

```
python -m venv /path/to/new/virtual/environment
```

​	This creates the target directory (including parent directories as needed) and places a `pyvenv.cfg` file in it with a `home` key pointing to the Python installation from which the command was run. It also creates a `bin` (or `Scripts` on Windows) subdirectory containing a copy or symlink of the Python executable (as appropriate for the platform or arguments used at environment creation time). It also creates a `lib/pythonX.Y/site-packages` subdirectory (on Windows, this is `Libsite-packages`). If an existing directory is specified, it will be re-used.

*在 3.5 版本发生变更:* 现在推荐使用 `venv` 来创建虚拟环境。

*Deprecated since version 3.6, removed in version 3.8:* **pyvenv** 是针对 Python 3.3 和 3.4 创建虚拟环境的推荐工具，并在 3.5 中被直接执行 `venv` 的方式所取代。

​	On Windows, invoke the `venv` command as follows:

```
PS> python -m venv C:\path\to\new\virtual\environment
```

​	本命令如果以 `-h` 参数运行，将显示可用的选项:

```
usage: venv [-h] [--system-site-packages] [--symlinks | --copies] [--clear]
            [--upgrade] [--without-pip] [--prompt PROMPT] [--upgrade-deps]
            [--without-scm-ignore-files]
            ENV_DIR [ENV_DIR ...]

Creates virtual Python environments in one or more target directories.

positional arguments:
  ENV_DIR               A directory to create the environment in.

options:
  -h, --help            show this help message and exit
  --system-site-packages
                        Give the virtual environment access to the system
                        site-packages dir.
  --symlinks            Try to use symlinks rather than copies, when
                        symlinks are not the default for the platform.
  --copies              Try to use copies rather than symlinks, even when
                        symlinks are the default for the platform.
  --clear               Delete the contents of the environment directory
                        if it already exists, before environment creation.
  --upgrade             Upgrade the environment directory to use this
                        version of Python, assuming Python has been
                        upgraded in-place.
  --without-pip         Skips installing or upgrading pip in the virtual
                        environment (pip is bootstrapped by default)
  --prompt PROMPT       Provides an alternative prompt prefix for this
                        environment.
  --upgrade-deps        Upgrade core dependencies (pip) to the latest
                        version in PyPI
  --without-scm-ignore-files
                        Skips adding SCM ignore files to the environment
                        directory (Git is supported by default).

Once an environment has been created, you may wish to activate it, e.g. by
sourcing an activate script in its bin directory.
```

*在 3.4 版本发生变更:* Installs pip by default, added the `--without-pip` and `--copies` options.

*在 3.4 版本发生变更:* 在早期版本中，如果目标目录已存在，将引发错误，除非使用了 `--clear` 或 `--upgrade` 选项。

*在 3.9 版本发生变更:* Add `--upgrade-deps` option to upgrade pip + setuptools to the latest on PyPI.

*在 3.12 版本发生变更:* `setuptools` 不再是核心的 venv 依赖项。

*在 3.13 版本发生变更:* Added the `--without-scm-ignore-files` option.

*在 3.13 版本发生变更:* `venv` now creates a `.gitignore` file for Git by default.

​	备注

 

​	虽然 Windows 支持符号链接，但不推荐使用它们。特别注意，在文件资源管理器中双击 `python.exe` 将立即解析符号链接，并忽略虚拟环境。

​	备注

 

​	在 Microsoft Windows 上，为了启用 `Activate.ps1` 脚本，可能需要修改用户的执行策略。可以运行以下 PowerShell 命令来执行此操作：

```
PS C:\> Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

​	参阅 [About Execution Policies](https://go.microsoft.com/fwlink/?LinkID=135170) 以获取更多信息。

​	The created `pyvenv.cfg` file also includes the `include-system-site-packages` key, set to `true` if `venv` is run with the `--system-site-packages` option, `false` otherwise.

​	除非采用 `--without-pip` 选项，否则将会调用 [`ensurepip`](https://docs.python.org/zh-cn/3.13/library/ensurepip.html#module-ensurepip) 将 `pip` 引导到虚拟环境中。

​	可以向 `venv` 传入多个路径，此时将根据给定的选项，在所给的每个路径上创建相同的虚拟环境。



## 虚拟环境是如何实现的

​	当运行虚拟环境中的 Python 解释器时，[`sys.prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.prefix) 和 [`sys.exec_prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.exec_prefix) 将指向该虚拟环境的相应目录，而 [`sys.base_prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.base_prefix) 和 [`sys.base_exec_prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.base_exec_prefix) 将指向用于创建该虚拟环境的基础 Python 的相应目录。 只需检测 `sys.prefix != sys.base_prefix` 就足以确定当前解释器是否运行于虚拟环境中。

​	一个虚拟环境可以通过位于其二进制文件目录目录 (在 POSIX 上为 `bin`；在 Windows 上为 `Scripts` ) 中的脚本来“激活”。 这会将该目录添加到你的 `PATH`，这样运行 **python** 时就会发起调用虚拟环境的 Python 解释器，从而可以运行该目录中安装的脚本而不必使用其完整路径。 激活脚本的发起调用方式是平台专属的 (`*<venv>*` 必须用包含虚拟环境目录的路径来替换):

| 平台       | Shell                                   | 用于激活虚拟环境的命令               |
| :--------- | :-------------------------------------- | :----------------------------------- |
| POSIX      | bash/zsh                                | `$ source *<venv>*/bin/activate`     |
| fish       | `$ source *<venv>*/bin/activate.fish`   |                                      |
| csh/tcsh   | `$ source *<venv>*/bin/activate.csh`    |                                      |
| pwsh       | `$ *<venv>*/bin/Activate.ps1`           |                                      |
| Windows    | cmd.exe                                 | `C:\> *<venv>*\Scripts\activate.bat` |
| PowerShell | `PS C:\> *<venv>*\Scripts\Activate.ps1` |                                      |

> Added in version 3.4:* **fish** 和 **csh*
> 激活脚本。

> Added in version 3.8:
> 在 POSIX 上安装 PowerShell 激活脚本，以支持 PowerShell Core。

​	激活一个虚拟环境的操作 *不是必需的*，因为你完全可以在发起调用 Python 时指明特定虚拟环境的 Python 解释器的完整路径。 更进一步地说，安装在虚拟环境中的所有脚本也都可以在不激活该虚拟环境的情况下运行。

​	In order to achieve this, scripts installed into virtual environments have a "shebang" line which points to the environment's Python interpreter, `#!/*<path-to-venv>*/bin/python`. This means that the script will run with that interpreter regardless of the value of `PATH`. On Windows, "shebang" line processing is supported if you have the [适用于Windows的Python启动器](https://docs.python.org/zh-cn/3.13/using/windows.html#launcher) installed. Thus, double-clicking an installed script in a Windows Explorer window should run it with the correct interpreter without the environment needing to be activated or on the `PATH`.

​	当一个虚拟环境已被激活时，`VIRTUAL_ENV` 环境变量会被设为该虚拟环境的路径。 由于使用虚拟环境并不需要显式地激活它，因此 `VIRTUAL_ENV` 并不能被用来可靠地确定是否正在使用虚拟环境。

​	警告

 

​	因为安装在虚拟环境中的脚本不应要求必须激活该虚拟环境，所以它们的“井号叹号”行会包含虚拟环境的绝对路径。 因为这一点，所以虚拟环境在通常情况下都是不可移植的。 你应当保证提供重建一个虚拟环境的简便方式（举例来说，如果你准备了需求文件 `requirements.txt`，则可以使用虚拟环境的 `pip` 执行 `pip install -r requirements.txt` 来安装虚拟环境所需的所有软件包）。 如果出于某种原因你需要将虚拟环境移动到一个新的位置，则你应当在目标位置上重建它并删除旧位置上的虚拟环境。 如果出于某种原因你移动了一个虚拟环境的上级目录，你也应当在新位置上重建该虚拟环境。 否则，安装到该虚拟环境的软件包可能无法正常工作。

​	你可以通过在 shell 中输入 `deactivate` 来取消激活一个虚拟环境。 取消激活的实现机制依赖于具体平台并且属于内部实现细节（通常，将会使用一个脚本或者 shell 函数）。



## API

​	上述的高级方法使用了一个简单的 API，该 API 提供了一种机制，第三方虚拟环境创建者可以根据其需求自定义环境创建过程，该 API 为 [`EnvBuilder`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder) 类。

## *class* venv.**EnvBuilder**(*system_site_packages=False*, *clear=False*, *symlinks=False*, *upgrade=False*, *with_pip=False*, *prompt=None*, *upgrade_deps=False*, ***, *scm_ignore_files=frozenset()*)

[`EnvBuilder`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder) 类在实例化时接受以下关键字参数：

- *system_site_packages* -- a boolean value indicating that the system Python site-packages should be available to the environment (defaults to `False`).
- *clear* -- a boolean value which, if true, will delete the contents of any existing target directory, before creating the environment.
- *symlinks* -- a boolean value indicating whether to attempt to symlink the Python binary rather than copying.
- *upgrade* -- a boolean value which, if true, will upgrade an existing environment with the running Python - for use when that Python has been upgraded in-place (defaults to `False`).
- *with_pip* -- a boolean value which, if true, ensures pip is installed in the virtual environment. This uses [`ensurepip`](https://docs.python.org/zh-cn/3.13/library/ensurepip.html#module-ensurepip) with the `--default-pip` option.
- *prompt* -- a string to be used after virtual environment is activated (defaults to `None` which means directory name of the environment would be used). If the special string `"."` is provided, the basename of the current directory is used as the prompt.
- *upgrade_deps* -- Update the base venv modules to the latest on PyPI
- *scm_ignore_files* -- Create ignore files based for the specified source control managers (SCM) in the iterable. Support is defined by having a method named `create_{scm}_ignore_file`. The only value supported by default is `"git"` via [`create_git_ignore_file()`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder.create_git_ignore_file).

*在 3.4 版本发生变更:* 添加 `with_pip` 参数

*在 3.6 版本发生变更:* 添加 `prompt` 参数

*在 3.9 版本发生变更:* 添加 `upgrade_deps` 参数

*在 3.13 版本发生变更:* 增加了 `scm_ignore_files` 形参

[`EnvBuilder`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder) may be used as a base class.

## **create**(*env_dir*)

​	指定要建立虚拟环境的目标目录（绝对路径或相对于当前路径）来创建虚拟环境。`create` 方法将在指定目录中创建环境，或者引发对应的异常。

[`EnvBuilder`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder) 类的 `create` 方法定义了可用于定制子类的钩子:

```
def create(self, env_dir):
    """
    在一个目录中创建虚拟的 Python 环境。
    env_dir 是用于创建环境的目标目录。
    """
    env_dir = os.path.abspath(env_dir)
    context = self.ensure_directories(env_dir)
    self.create_configuration(context)
    self.setup_python(context)
    self.setup_scripts(context)
    self.post_setup(context)
```

​	每个方法 [`ensure_directories()`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder.ensure_directories), [`create_configuration()`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder.create_configuration), [`setup_python()`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder.setup_python), [`setup_scripts()`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder.setup_scripts) 和 [`post_setup()`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder.post_setup) 都可以被重写。

## **ensure_directories**(*env_dir*)

​	创建虚拟环境目录及尚不存在的所有必要的子目录，并返回一个上下文对象。 这个上下文对象被用于存放供其他方法使用的属性（如路径等）。 如果 [`EnvBuilder`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder) 是附带参数 `clear=True` 创建的，则虚拟环境的内容将被清除并将重新创建所有必要的子目录。

​	返回的上下文对象是一个具有以下属性的 [`types.SimpleNamespace`](https://docs.python.org/zh-cn/3.13/library/types.html#types.SimpleNamespace):

- `env_dir` - 虚拟环境的位置。 将被用作激活脚本中的 `__VENV_DIR__` (参见 [`install_scripts()`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder.install_scripts))。
- `env_name` - 虚拟环境的名称。 将被用作激活脚本中的 `__VENV_NAME__` (参见 [`install_scripts()`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder.install_scripts))。
- `prompt` - 激活脚本要使用的提示符。 将被用作激活脚本中的 `__VENV_PROMPT__` (参见 [`install_scripts()`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder.install_scripts))。
- `executable` - 虚拟环境所使用的下层 Python 可执行文件。 这会将基于另一个虚拟环境创建虚拟环境的情况也纳入考虑。
- `inc_path` - 虚拟环境的 include 路径。
- `lib_path` - 虚拟环境的 purelib 路径。
- `bin_path` - 虚拟环境的 script 路径。
- `bin_name` - 相对于虚拟环境位置的 script 路径名称。 用于激活脚本中的 `__VENV_BIN_NAME__` (参见 [`install_scripts()`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder.install_scripts))。
- `env_exe` - 虚拟环境中 Python 解释器的名称。 用于激活脚本中的 `__VENV_PYTHON__` (参见 [`install_scripts()`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder.install_scripts))。
- `env_exec_cmd` - Python 解释器的名称，会将文件系统重定向也纳入考虑。 这可被用于在虚拟环境中运行 Python。

*在 3.11 版本发生变更:* *venv* [sysconfig installation scheme](https://docs.python.org/zh-cn/3.13/library/sysconfig.html#installation-paths) 被用于构造所创建目录的路径。

*在 3.12 版本发生变更:* 将属性 `lib_path` 添加到上下文中，并将上下文对象写入文档。

## **create_configuration**(*context*)

​	在环境中创建 `pyvenv.cfg` 配置文件。

## **setup_python**(*context*)

​	在环境中创建 Python 可执行文件的拷贝或符号链接。在 POSIX 系统上，如果给定了可执行文件 `python3.x`，将创建指向该可执行文件的 `python` 和 `python3` 符号链接，除非相同名称的文件已经存在。

## **setup_scripts**(*context*)

​	将适用于平台的激活脚本安装到虚拟环境中。

## **upgrade_dependencies**(*context*)

​	Upgrades the core venv dependency packages (currently [pip](https://pypi.org/project/pip/)) in the environment. This is done by shelling out to the `pip` executable in the environment.

> Added in version 3.9.
>

*在 3.12 版本发生变更:* [setuptools](https://pypi.org/project/setuptools/) is no longer a core venv dependency.

## **post_setup**(*context*)

​	占位方法，可以在第三方实现中重写，用于在虚拟环境中预安装软件包，或是其他创建后要执行的步骤。

## **install_scripts**(*context*, *path*)

​	This method can be called from [`setup_scripts()`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder.setup_scripts) or [`post_setup()`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder.post_setup) in subclasses to assist in installing custom scripts into the virtual environment.

*path* is the path to a directory that should contain subdirectories `common`, `posix`, `nt`; each containing scripts destined for the `bin` directory in the environment. The contents of `common` and the directory corresponding to [`os.name`](https://docs.python.org/zh-cn/3.13/library/os.html#os.name) are copied after some text replacement of placeholders:

- `__VENV_DIR__` 会被替换为环境目录的绝对路径。
- `__VENV_NAME__` 会被替换为环境名称（环境目录的最后一个字段）。
- `__VENV_PROMPT__` 会被替换为提示符（用括号括起来的环境名称紧跟着一个空格）。
- `__VENV_BIN_NAME__` 会被替换为 bin 目录的名称（ `bin` 或 `Scripts` ）。
- `__VENV_PYTHON__` 会被替换为环境可执行文件的绝对路径。

​	允许目录已存在（用于升级现有环境时）。

## **create_git_ignore_file**(*context*)

​	Creates a `.gitignore` file within the virtual environment that causes the entire directory to be ignored by the Git source control manager.

> Added in version 3.13.
>

*在 3.7.2 版本发生变更:* Windows 现在为 `python[w].exe` 使用重定向脚本，而不是复制实际的二进制文件。仅在 3.7.2 中，除非运行的是源码树中的构建，否则 [`setup_python()`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder.setup_python) 不会执行任何操作。

*在 3.7.3 版本发生变更:* Windows 将重定向脚本复制为 [`setup_python()`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder.setup_python) 的一部分而非 [`setup_scripts()`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder.setup_scripts)。在 3.7.2 中不是这种情况。使用符号链接时，将链接至原始可执行文件。

​	有一个方便实用的模块级别的函数:

## venv.**create**(*env_dir*, *system_site_packages=False*, *clear=False*, *symlinks=False*, *with_pip=False*, *prompt=None*, *upgrade_deps=False*, ***, *scm_ignore_files=frozenset()*)

​	通过关键词参数来创建一个 [`EnvBuilder`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder)，并且使用 *env_dir* 参数来调用它的 [`create()`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder.create) 方法。

> Added in version 3.3.
>

*在 3.4 版本发生变更:* Added the *with_pip* parameter

*在 3.6 版本发生变更:* Added the *prompt* parameter

*在 3.9 版本发生变更:* Added the *upgrade_deps* parameter

*在 3.13 版本发生变更:* Added the *scm_ignore_files* parameter

## 一个扩展 `EnvBuilder` 的例子

​	下面的脚本展示了如何通过实现一个子类来扩展 [`EnvBuilder`](https://docs.python.org/zh-cn/3.13/library/venv.html#venv.EnvBuilder)。这个子类会安装 setuptools 和 pip 到被创建的虚拟环境中。

```
import os
import os.path
from subprocess import Popen, PIPE
import sys
from threading import Thread
from urllib.parse import urlparse
from urllib.request import urlretrieve
import venv

class ExtendedEnvBuilder(venv.EnvBuilder):
    """
    This builder installs setuptools and pip so that you can pip or
    easy_install other packages into the created virtual environment.

    :param nodist: If true, setuptools and pip are not installed into the
                   created virtual environment.
    :param nopip: If true, pip is not installed into the created
                  virtual environment.
    :param progress: If setuptools or pip are installed, the progress of the
                     installation can be monitored by passing a progress
                     callable. If specified, it is called with two
                     arguments: a string indicating some progress, and a
                     context indicating where the string is coming from.
                     The context argument can have one of three values:
                     'main', indicating that it is called from virtualize()
                     itself, and 'stdout' and 'stderr', which are obtained
                     by reading lines from the output streams of a subprocess
                     which is used to install the app.

                     If a callable is not specified, default progress
                     information is output to sys.stderr.
    """

    def __init__(self, *args, **kwargs):
        self.nodist = kwargs.pop('nodist', False)
        self.nopip = kwargs.pop('nopip', False)
        self.progress = kwargs.pop('progress', None)
        self.verbose = kwargs.pop('verbose', False)
        super().__init__(*args, **kwargs)

    def post_setup(self, context):
        """
        Set up any packages which need to be pre-installed into the
        virtual environment being created.

        :param context: The information for the virtual environment
                        creation request being processed.
        """
        os.environ['VIRTUAL_ENV'] = context.env_dir
        if not self.nodist:
            self.install_setuptools(context)
        # Can't install pip without setuptools
        if not self.nopip and not self.nodist:
            self.install_pip(context)

    def reader(self, stream, context):
        """
        Read lines from a subprocess' output stream and either pass to a progress
        callable (if specified) or write progress information to sys.stderr.
        """
        progress = self.progress
        while True:
            s = stream.readline()
            if not s:
                break
            if progress is not None:
                progress(s, context)
            else:
                if not self.verbose:
                    sys.stderr.write('.')
                else:
                    sys.stderr.write(s.decode('utf-8'))
                sys.stderr.flush()
        stream.close()

    def install_script(self, context, name, url):
        _, _, path, _, _, _ = urlparse(url)
        fn = os.path.split(path)[-1]
        binpath = context.bin_path
        distpath = os.path.join(binpath, fn)
        # Download script into the virtual environment's binaries folder
        urlretrieve(url, distpath)
        progress = self.progress
        if self.verbose:
            term = '\n'
        else:
            term = ''
        if progress is not None:
            progress('Installing %s ...%s' % (name, term), 'main')
        else:
            sys.stderr.write('Installing %s ...%s' % (name, term))
            sys.stderr.flush()
        # Install in the virtual environment
        args = [context.env_exe, fn]
        p = Popen(args, stdout=PIPE, stderr=PIPE, cwd=binpath)
        t1 = Thread(target=self.reader, args=(p.stdout, 'stdout'))
        t1.start()
        t2 = Thread(target=self.reader, args=(p.stderr, 'stderr'))
        t2.start()
        p.wait()
        t1.join()
        t2.join()
        if progress is not None:
            progress('done.', 'main')
        else:
            sys.stderr.write('done.\n')
        # Clean up - no longer needed
        os.unlink(distpath)

    def install_setuptools(self, context):
        """
        Install setuptools in the virtual environment.

        :param context: The information for the virtual environment
                        creation request being processed.
        """
        url = "https://bootstrap.pypa.io/ez_setup.py"
        self.install_script(context, 'setuptools', url)
        # clear up the setuptools archive which gets downloaded
        pred = lambda o: o.startswith('setuptools-') and o.endswith('.tar.gz')
        files = filter(pred, os.listdir(context.bin_path))
        for f in files:
            f = os.path.join(context.bin_path, f)
            os.unlink(f)

    def install_pip(self, context):
        """
        Install pip in the virtual environment.

        :param context: The information for the virtual environment
                        creation request being processed.
        """
        url = 'https://bootstrap.pypa.io/get-pip.py'
        self.install_script(context, 'pip', url)


def main(args=None):
    import argparse

    parser = argparse.ArgumentParser(prog=__name__,
                                     description='Creates virtual Python '
                                                 'environments in one or '
                                                 'more target '
                                                 'directories.')
    parser.add_argument('dirs', metavar='ENV_DIR', nargs='+',
                        help='A directory in which to create the '
                             'virtual environment.')
    parser.add_argument('--no-setuptools', default=False,
                        action='store_true', dest='nodist',
                        help="Don't install setuptools or pip in the "
                             "virtual environment.")
    parser.add_argument('--no-pip', default=False,
                        action='store_true', dest='nopip',
                        help="Don't install pip in the virtual "
                             "environment.")
    parser.add_argument('--system-site-packages', default=False,
                        action='store_true', dest='system_site',
                        help='Give the virtual environment access to the '
                             'system site-packages dir.')
    if os.name == 'nt':
        use_symlinks = False
    else:
        use_symlinks = True
    parser.add_argument('--symlinks', default=use_symlinks,
                        action='store_true', dest='symlinks',
                        help='Try to use symlinks rather than copies, '
                             'when symlinks are not the default for '
                             'the platform.')
    parser.add_argument('--clear', default=False, action='store_true',
                        dest='clear', help='Delete the contents of the '
                                           'virtual environment '
                                           'directory if it already '
                                           'exists, before virtual '
                                           'environment creation.')
    parser.add_argument('--upgrade', default=False, action='store_true',
                        dest='upgrade', help='Upgrade the virtual '
                                             'environment directory to '
                                             'use this version of '
                                             'Python, assuming Python '
                                             'has been upgraded '
                                             'in-place.')
    parser.add_argument('--verbose', default=False, action='store_true',
                        dest='verbose', help='Display the output '
                                             'from the scripts which '
                                             'install setuptools and pip.')
    options = parser.parse_args(args)
    if options.upgrade and options.clear:
        raise ValueError('you cannot supply --upgrade and --clear together.')
    builder = ExtendedEnvBuilder(system_site_packages=options.system_site,
                                   clear=options.clear,
                                   symlinks=options.symlinks,
                                   upgrade=options.upgrade,
                                   nodist=options.nodist,
                                   nopip=options.nopip,
                                   verbose=options.verbose)
    for d in options.dirs:
        builder.create(d)

if __name__ == '__main__':
    rc = 1
    try:
        main()
        rc = 0
    except Exception as e:
        print('Error: %s' % e, file=sys.stderr)
    sys.exit(rc)
```

​	这个脚本同样可以 [在线下载](https://gist.github.com/vsajip/4673395)。
