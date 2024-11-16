+++
title = "3. 配置 Python"
date = 2024-11-14T22:13:29+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/using/configure.html](https://docs.python.org/zh-cn/3.13/using/configure.html)
>
> 收录该文档的时间：`2024-11-14T22:13:29+08:00`

# 3. 配置 Python

## 3.1. 构建要求

​	编译 CPython 所需要的特性和最低版本:

- [C11](https://en.cppreference.com/w/c/11) 编译器。 [可选的 C11 特性](https://en.wikipedia.org/wiki/C11_(C_standard_revision)#Optional_features) 不是必须的。
- 在 Windows 上，需要 Microsoft Visual Studio 2017 或更新版本。
- 对 [IEEE 754](https://en.wikipedia.org/wiki/IEEE_754) 浮点数和 [浮点 Not-a-Number (NaN)](https://en.wikipedia.org/wiki/NaN#Floating_point) 的支持。
- 对线程的支持。
- 对于 [`ssl`]({{< ref "/library/ipc/ssl#module-ssl" >}}) 和 [`hashlib`]({{< ref "/library/crypto/hashlib#module-hashlib" >}}) 扩展模块 OpenSSL 1.1.1 为最低版本而 OpenSSL 3.0.9 为推荐的最低版本。
- SQLite 3.15.2 用于 [`sqlite3`]({{< ref "/library/persistence/sqlite3#module-sqlite3" >}}) 扩展模块。
- Tcl/Tk 8.5.12 用于 [`tkinter`]({{< ref "/library/tk/tkinter#module-tkinter" >}}) 模块。
- Autoconf 2.71 and aclocal 1.16.5 are required to regenerate the `configure` script.

*在 3.1 版本发生变更:* 现在需要 Tcl/Tk 8.3.1 版本。

*在 3.5 版本发生变更:* 在 Windows 上，现在需要 Visual Studio 2015 或更新的版本。 现在需要 Tcl/Tk 8.4 版本。

*在 3.6 版本发生变更:* 现在要求选定的 C99 特性，如 `<stdint.h>` 和 `static inline` 函数。

*在 3.7 版本发生变更:* 现在要求线程支持和 OpenSSL 1.0.2。

*在 3.10 版本发生变更:* 现在需要 OpenSSL 1.1.1。 需要 SQLite 3.7.15。

*在 3.11 版本发生变更:* C11 编译器，现在需要 IEEE 754 和 NaN 支持。 在 Windows 上，需要 Visual Studio 2017 或更新的版本。 现在需要 Tcl/Tk version 8.5.12 用于 [`tkinter`]({{< ref "/library/tk/tkinter#module-tkinter" >}}) 模块。

*在 3.13 版本发生变更:* Autoconf 2.71, aclocal 1.16.5 and SQLite 3.15.2 are now required.

​	另请参阅 [**PEP 7**](https://peps.python.org/pep-0007/) "Style Guide for C Code" 和 [**PEP 11**](https://peps.python.org/pep-0011/) "CPython platform support"。

## 3.2. 已生成的文件

​	为了减少构建依赖性，Python 源代码包含多个已生成的文件。 重新生成所有已生成文件的命令如下:

```
make regen-all
make regen-stdlib-module-names
make regen-limited-abi
make regen-configure
```

`Makefile.pre.in` 文件记录了已生成的文件、它们的输入以及用于重新生成它们的工具。 搜索 `regen-*` make target。

### 3.2.1. 配置脚本

`make regen-configure` 命令将使用 `Tools/build/regen-configure.sh` shell 脚本生成 `aclocal.m4` 文件和 `configure` 脚本，它通过使用一个 Ubuntu 容器来获取同样的工具版本并具有可复现的输出。

​	容器是可选的，以下命令可以在本地运行:

```
autoreconf -ivf -Werror
```

​	生成的文件可根据实际的 `autoconf-archive`, `aclocal` 和 `pkg-config` 版本进行改变。



## 3.3. 配置选项

​	用以下方式列出所有 `configure` 脚本选项:

```
./configure --help
```

​	参阅 Python 源代码中的 `Misc/SpecialBuilds.txt` 。

### 3.3.1. 通用选项

## **--enable-loadable-sqlite-extensions**

​	在 [`sqlite3`]({{< ref "/library/persistence/sqlite3#module-sqlite3" >}}) 模块的 `_sqlite` 扩展模块中是否支持可加载扩展（默认为否）。

​	参见 [`sqlite3.Connection.enable_load_extension()`]({{< ref "/library/persistence/sqlite3#sqlite3.Connection.enable_load_extension" >}}) 方法的 [`sqlite3`]({{< ref "/library/persistence/sqlite3#module-sqlite3" >}}) 模块。

> Added in version 3.6.
>

## **--disable-ipv6**

​	禁用 IPv6 支持（若开启支持则默认启用），见 [`socket`]({{< ref "/library/ipc/socket#module-socket" >}}) 模块。

## **--enable-big-digits**=[15|30]

​	定义 Python [`int`]({{< ref "/library/functions#int" >}}) 数字的比特大小：15或30比特

​	在默认情况下，数位大小为 30。

​	定义 `PYLONG_BITS_IN_DIGIT` 为 `15` 或 `30`。

​	参见 [`sys.int_info.bits_per_digit`]({{< ref "/library/python/sys#sys.int_info" >}}) 。

## **--with-suffix**=SUFFIX

​	将 Python 的可执行文件后缀设置为 *SUFFIX*。

​	在 Windows 和 macOS 上默认后缀为 `.exe` (可执行文件为 `python.exe`)，在 Emscripten 节点上为 `.js`，在 Emscripten 浏览器上为 `.html`，在 WASI 上为 `.wasm`，而在其他平台上为一个空字符串 (可执行文件为 `python`)。

*在 3.11 版本发生变更:* 在 WASM 平台上默认后缀为 `.js`, `.html` 或 `.wasm` 之一。

## **--with-tzpath**=<list of absolute paths separated by pathsep>

​	Select the default time zone search path for为 [`zoneinfo.TZPATH`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.TZPATH" >}}) 选择默认的时区搜索路径。 参见 [`zoneinfo`]({{< ref "/library/datatypes/zoneinfo#module-zoneinfo" >}}) 模块的 [编译时配置]({{< ref "/library/datatypes/zoneinfo#zoneinfo-data-compile-time-config" >}})。

​	默认：`/usr/share/zoneinfo:/usr/lib/zoneinfo:/usr/share/lib/zoneinfo:/etc/zoneinfo`

​	参阅 [`os.pathsep`]({{< ref "/library/allos/os#os.pathsep" >}}) 路径分隔符。

> Added in version 3.9.
>

## **--without-decimal-contextvar**

​	编译 `_decimal` 扩展模块时使用线程本地上下文，而不是协程本地上下文（默认），参见 [`decimal`]({{< ref "/library/numeric/decimal#module-decimal" >}}) 模块。

​	参见 [`decimal.HAVE_CONTEXTVAR`]({{< ref "/library/numeric/decimal#decimal.HAVE_CONTEXTVAR" >}}) 和 [`contextvars`]({{< ref "/library/concurrency/contextvars#module-contextvars" >}}) 模块。

> Added in version 3.9.
>

## **--with-dbmliborder**=<list of backend names>

​	覆盖 [`dbm`]({{< ref "/library/persistence/dbm#module-dbm" >}}) 模块的 db 后端检查顺序。

​	合法值是用冒号（`:`）分隔的字符串，包含后端名称。

- `ndbm` ；
- `gdbm` ；
- `bdb` 。

## **--without-c-locale-coercion**

​	禁用 C 语言对 UTF-8 的强制要求（默认为启用）。

​	不定义 `PY_COERCE_C_LOCALE` 宏。

​	参阅 [`PYTHONCOERCECLOCALE`]({{< ref "/using/cmdline#envvar-PYTHONCOERCECLOCALE" >}}) 和 [**PEP 538**](https://peps.python.org/pep-0538/)。

## **--without-freelists**

​	禁用除空元组单例以外的所有自由列表。Disable all freelists except the empty tuple singleton.

> Added in version 3.11.
>

## **--with-platlibdir**=DIRNAME

​	Python 库目录名（默认为 `lib`）。

​	Fedora 和 SuSE 在64 位平台用 `lib64` 。

​	参阅 [`sys.platlibdir`]({{< ref "/library/python/sys#sys.platlibdir" >}}) 。

> Added in version 3.9.
>

## **--with-wheel-pkg-dir**=PATH

[`ensurepip`]({{< ref "/library/distribution/ensurepip#module-ensurepip" >}}) 模块用到的 wheel 包目录（默认为无）。

​	某些 Linux 发行版的打包策略建议不要捆绑依赖关系。如 Fedora 在 `/usr/share/python-wheels/` 目录下安装 wheel 包，而不安装 `ensurepip._bundled` 包。

> Added in version 3.10.
>

## **--with-pkg-config**=[check|yes|no]

​	配置是否应当使用 **pkg-config** 来检测构建依赖。

- `check` (默认值): **pkg-config** 为可选项
- `yes`: **pkg-config** 为必选项。
- `no`: 配置不使用 **pkg-config** 即使其存在

> Added in version 3.11.
>

## **--enable-pystats**

​	启用内部 Python 性能统计数据收集。

​	在默认情况下，将关闭统计数据收集。 使用 `python3 -X pystats` 命令或设置 `PYTHONSTATS=1` 环境变量在 Python 启动时启用统计数据收集。

​	在 Python 退出时，如果统计数据收集已启用且未清除则会转储统计数据。

​	效果如下：

- 增加了 [`-X pystats`]({{< ref "/using/cmdline#cmdoption-X" >}}) 命令行选项。
- 增加了 `PYTHONSTATS` 环境变量。
- 定义 `Py_STATS` 宏。
- 为 [`sys`]({{< ref "/library/python/sys#module-sys" >}}) 模块增加函数：
  - `sys._stats_on()`: 启用统计数据收集。
  - `sys._stats_off()`: 关闭统计数据收集。
  - `sys._stats_clear()`: 清除统计数据。
  - `sys._stats_dump()`: 将统计数据转储到文件，并清除统计数据。

​	统计数据将被转储至 `/tmp/py_stats/` (Unix) 或 `C:\temp\py_stats\` (Windows) 中的任意（可能唯一）文件。 如果该目录不存在，结果将被打印到 stderr。

​	使用 `Tools/scripts/summarize_stats.py` 来读取统计数据。

​	统计数据：

- 操作码：
  - 专门类别: success, failure, hit, deferred, miss, deopt, failures；
  - 执行计数；
  - 对应计数。
- 调用：
  - 内联 Python 调用；
  - PyEval 调用；
  - 推入的帧；
  - 创建的帧对象；
  - Eval 调用: vector, generator, legacy, function VECTORCALL, build class, slot, function "ex", API, method。
- 对象：
  - incref 和 decref；
  - 解释器 incref 和 decref；
  - 分配: all, 512 bytes, 4 kiB, big；
  - 空闲；
  - 去向/来自空闲列表；
  - 实体化/非实体化的字典；
  - 类型缓存；
  - 优化尝试；
  - 已创建/已执行的优化跟踪；
  - 已执行的 uop。
- 垃圾回收器：
  - 垃圾回收；
  - 已访问的对象；
  - 已收集的对象。

> Added in version 3.11.
>

## **--disable-gil**

​	启用对不带 [global interpreter lock]({{< ref "/glossary/idx#term-global-interpreter-lock" >}}) (GIL) 运行 Python 的 **实验性** 支持：自由线程构建版。

​	定义 `Py_GIL_DISABLED` 宏并向 [`sys.abiflags`]({{< ref "/library/python/sys#sys.abiflags" >}}) 添加 `"t"`。

​	请参阅 [自由线程的 CPython]({{< ref "/whatsnew/idx#whatsnew313-free-threaded-cpython" >}}) 了解详情。

> Added in version 3.13.
>

## **--enable-experimental-jit**=[no|yes|yes-off|interpreter]

​	Indicate how to integrate the [JIT compiler]({{< ref "/whatsnew/idx#whatsnew313-jit-compiler" >}}).

- `no` - build the interpreter without the JIT.
- `yes` - build the interpreter with the JIT.
- `yes-off` - build the interpreter with the JIT but disable it by default.
- `interpreter` - build the interpreter without the JIT, but with the tier 2 enabled interpreter.

​	By convention, `--enable-experimental-jit` is a shorthand for `--enable-experimental-jit=yes`.

> Added in version 3.13.
>

## **PKG_CONFIG**

​	指向 `pkg-config` 工具的路径。

## **PKG_CONFIG_LIBDIR**

## **PKG_CONFIG_PATH**

`pkg-config` 选项。

### 3.3.2. C 编译器选项

## **CC**

​	C 编译器指令。

## **CFLAGS**

​	C 编译器标志。

## **CPP**

​	C 预处理器命令。

## **CPPFLAGS**

​	C 预处理器旗标，例如 `-I*include_dir*`。

### 3.3.3. 链接器选项

## **LDFLAGS**

​	链接器旗标，例如 `-L*library_directory*`。

## **LIBS**

​	要传给链接器的库，例如 `-l*library*`。

## **MACHDEP**

​	依赖具体机器的库文件名称。

### 3.3.4. 用于第三方依赖的选项

> Added in version 3.11.
>

## **BZIP2_CFLAGS**

## **BZIP2_LIBS**

​	将 Python 链接到 `libbz2` 的 C 编译器和链接器旗标，由 [`bz2`]({{< ref "/library/archiving/bz2#module-bz2" >}}) 模块使用，覆盖 `pkg-config`。

## **CURSES_CFLAGS**

## **CURSES_LIBS**

​	针对 `libncurses` 或 `libncursesw` 的 C 编译器和链接器旗标，由 [`curses`]({{< ref "/library/allos/curses#module-curses" >}}) 模块使用，覆盖 `pkg-config`。

## **GDBM_CFLAGS**

## **GDBM_LIBS**

​	针对 `gdbm` 的 C 编译器和链接器旗标。

## **LIBB2_CFLAGS**

## **LIBB2_LIBS**

​	针对 `libb2` ([BLAKE2]({{< ref "/library/crypto/hashlib#hashlib-blake2" >}})) 的 C 编译器和链接器旗标，由 [`hashlib`]({{< ref "/library/crypto/hashlib#module-hashlib" >}}) 模块使用，覆盖 `pkg-config`。

## **LIBEDIT_CFLAGS**

## **LIBEDIT_LIBS**

​	针对 `libedit` 的 C 编译器和链接器旗标，由 [`readline`]({{< ref "/library/text/readline#module-readline" >}}) 模块使用，覆盖 `pkg-config`。

## **LIBFFI_CFLAGS**

## **LIBFFI_LIBS**

​	针对 `libffi` 的 C 编译器和链接器旗标，由 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 模块使用，覆盖 `pkg-config`。

## **LIBMPDEC_CFLAGS**

## **LIBMPDEC_LIBS**

​	针对 `libmpdec` 的 C 编译器和链接器旗标，由 [`decimal`]({{< ref "/library/numeric/decimal#module-decimal" >}}) 模块使用，覆盖 `pkg-config`。

​	备注

 

​	除非指定了 [`--with-system-libmpdec`]({{< ref "/using/configure#cmdoption-with-system-libmpdec" >}}) 否则这些环境变量将没有效果。

## **LIBLZMA_CFLAGS**

## **LIBLZMA_LIBS**

​	针对 `liblzma` 的 C 编译器和链接器旗标，由 [`lzma`]({{< ref "/library/archiving/lzma#module-lzma" >}}) 模块使用，覆盖 `pkg-config`。

## **LIBREADLINE_CFLAGS**

## **LIBREADLINE_LIBS**

​	针对 `libreadline` 的 C 编译器和链接器旗标，由 [`readline`]({{< ref "/library/text/readline#module-readline" >}}) 模块使用，覆盖 `pkg-config`。

## **LIBSQLITE3_CFLAGS**

## **LIBSQLITE3_LIBS**

​	针对 `libsqlite3` 的 C 编译器和链接器旗标，由 [`sqlite3`]({{< ref "/library/persistence/sqlite3#module-sqlite3" >}}) 模块使用，覆盖 `pkg-config`。

## **LIBUUID_CFLAGS**

## **LIBUUID_LIBS**

​	针对 `libuuid` 的 C 编译器和链接器旗标，由 [`uuid`]({{< ref "/library/internet/uuid#module-uuid" >}}) 模块使用，覆盖 `pkg-config`。

## **PANEL_CFLAGS**

## **PANEL_LIBS**

​	针对 PANEL 的 C 编译器和链接器旗标，覆盖 `pkg-config`。

​	针对 `libpanel` 或 `libpanelw` 的 C 编译器和链接器旗标，由 [`curses.panel`]({{< ref "/library/allos/curses_panel#module-curses.panel" >}}) 模块使用，覆盖 `pkg-config`。

## **TCLTK_CFLAGS**

## **TCLTK_LIBS**

​	针对 TCLTK 的 C 编译器和链接器旗标，覆盖 `pkg-config`。

## **ZLIB_CFLAGS**

## **ZLIB_LIBS**

​	针对 `libzlib` 的 C 编译器和链接器旗标，由 [`gzip`]({{< ref "/library/archiving/gzip#module-gzip" >}}) 模块使用，覆盖 `pkg-config`。

### 3.3.5. WebAssembly 选项。

## **--with-emscripten-target**=[browser|node]

​	为 `wasm32-emscripten` 设置生成风格。

- `browser` (默认值): 预加载最小 stdlib，默认 MEMFS。
- `node`: NODERAWFS 和 pthread 支持。

> Added in version 3.11.
>

## **--enable-wasm-dynamic-linking**

​	为 WASM 启用动态链接支持。

​	动态链接启用 `dlopen`。 可执行文件的大小将由于限制死代码清理和附加特性而增加。

> Added in version 3.11.
>

## **--enable-wasm-pthreads**

​	为 WASM 启用 pthreads 支持。

> Added in version 3.11.
>

### 3.3.6. 安装时的选项

## **--prefix**=PREFIX

​	在 PREFIX 中安装架构无关的文件。 在 Unix 上，它默认为 `/usr/local`。

​	该值可在运行时使用 [`sys.prefix`]({{< ref "/library/python/sys#sys.prefix" >}}) 获取。

​	作为示例，用户可以使用 `--prefix="$HOME/.local/"` 在其家目录中安装 Python。

## **--exec-prefix**=EPREFIX

​	在 EPREFIX 中安装架构无关的文件，默认为 [`--prefix`]({{< ref "/using/configure#cmdoption-prefix" >}})。

​	该值可在运行时使用 [`sys.exec_prefix`]({{< ref "/library/python/sys#sys.exec_prefix" >}}) 获取。

## **--disable-test-modules**

​	不编译和安装 test 模块，如 [`test`]({{< ref "/library/development/test_html#module-test_support#module-test" >}}) 包或 `_testcapi` 扩展模块（默认会编译并安装）。

> Added in version 3.10.
>

## **--with-ensurepip**=[upgrade|install|no]

​	选择 Python 安装时运行的 [`ensurepip`]({{< ref "/library/distribution/ensurepip#module-ensurepip" >}}) 命令。

- `upgrade` （默认）：运行 `python -m ensurepip --altinstall --upgrade` 命令。
- `install` ：运行 `python -m ensurepip --altinstall` 命令。
- `no` ：不运行 ensurepip。

> Added in version 3.6.
>

### 3.3.7. 性能选项

​	为获得最佳性能推荐使用 `--enable-optimizations --with-lto` (PGO + LTO) 来配置 Python。 试验性的 `--enable-bolt` 旗标也可被用来提升性能。

## **--enable-optimizations**

​	用 [`PROFILE_TASK`]({{< ref "/using/configure#envvar-PROFILE_TASK" >}}) 启用以配置文件主导的优化（PGO）（默认为禁用）。

​	C 编译器 Clang 需要用到 `llvm-profdata` 程序进行 PGO。在 macOS 上，GCC 也需要用到它：在 macOS 上 GCC 只是 Clang 的别名而已。

​	如果使用 `--enable-shared` 和 GCC ，还可以禁用 libpython 中的语义插值：在编译器和链接器的标志中加入 `-fno-semantic-interposition` 。

​	备注

 

​	在构建期间，你可能会遇到编译器警告提示某些源文件的配置数据不可用。 这些警告是无害的，因为在获取配置数据时只有一部分代码会被使用。 要在 Clang 上禁用这些警告，可通过在 [`CFLAGS`]({{< ref "/using/configure#envvar-CFLAGS" >}}) 中添加 `-Wno-profile-instr-unprofiled` 来手动抑制它们。

> Added in version 3.6.
>

*在 3.10 版本发生变更:* 在 GCC 上使用 `-fno-semantic-interposition` 。

## **PROFILE_TASK**

​	Makefile 用到的环境变量：PGO 用到的 Python 命令行参数。

​	默认为：`-m test --pgo --timeout=$(TESTTIMEOUT)` 。

> Added in version 3.8.
>

*在 3.13 版本发生变更:* 任务失败将不会再被静默地忽略。

## **--with-lto**=[full|thin|no|yes]

​	在编译过程中启用链接时间优化（LTO）（默认为禁用）。

​	LTO 时 C 编译器 Clang 需要用到 `llvm-ar` 参数（在 macOS 则为 `ar`），以及支持 LTO 的链接器（`ld.gold` 或 `lld`）。

> Added in version 3.6.
>

> Added in version 3.11:
> 要使用 ThinLTO 特性，请在 Clang 上使用 `--with-lto=thin`。

*在 3.12 版本发生变更:* 如果编译器支持将使用 ThinLTO 旗标作为 Clang 上的默认优化策略。

## **--enable-bolt**

​	允许启用 [BOLT 链接后二进制优化器](https://github.com/llvm/llvm-project/tree/main/bolt) (默认为禁用)。

​	BOLT 是 LLVM 项目的一部分但并不总是包括在其二进制分发包中。 该旗标要求 `llvm-bolt` 和 `merge-fdata` 可用。

​	BOLT 仍然是一个相当新的项目因此目前该旗标应当被视为是试验性的。 因为此工具是作用于机器码所以其成功依赖于构建环境 + 其他优化配置参数 + CPU 架构的组合，并且并非所有组合都受到支持。 已知 LLVM 16 之前的 BOLT 版本在某些场景下会使得 BOLT 发生崩溃。 强烈建议使用 LLVM 16 或更新版本进行 BOLT 优化。

`BOLT_INSTRUMENT_FLAGS` 和 `BOLT_APPLY_FLAGS` **configure** 变量可被定义为覆盖 **llvm-bolt** 的默认参数集合来分别指示和将 BOLT 数据应用于二进制代码中。

> Added in version 3.12.
>

## **BOLT_APPLY_FLAGS**

​	当创建 [BOLT 优化的二进制文件](https://github.com/facebookarchive/BOLT) 时传给 `llvm-bolt` 的参数。

> Added in version 3.12.
>

## **BOLT_INSTRUMENT_FLAGS**

​	当构建二进制文件时传给 `llvm-bolt` 的参数。

> Added in version 3.12.
>

## **--with-computed-gotos**

​	在求值环节启用 goto 计数（在支持的编译器上默认启用）。

## **--without-mimalloc**

​	禁用快速的 [mimalloc]({{< ref "/c_api/memory#mimalloc" >}}) 分配器（默认为启用）。

​	参见环境变量 [`PYTHONMALLOC`]({{< ref "/using/cmdline#envvar-PYTHONMALLOC" >}}) 。

## **--without-pymalloc**

​	禁用特定的 Python 内存分配器 [pymalloc]({{< ref "/c_api/memory#pymalloc" >}}) （默认为启用）。

​	参见环境变量 [`PYTHONMALLOC`]({{< ref "/using/cmdline#envvar-PYTHONMALLOC" >}}) 。

## **--without-doc-strings**

​	禁用静态文档字符串以减少内存占用（默认启用）。Python 中定义的文档字符串不受影响。

​	不定义 `PY_COERCE_C_LOCALE` 宏。

​	参阅宏 `PyDoc_STRVAR()` 。

## **--enable-profiling**

​	用 `gprof` 启用 C 语言级的代码评估（默认为禁用）。

## **--with-strict-overflow**

​	将 `-fstrict-overflow` 添加到 C 编译器旗标 (在默认情况下我们将添加 `-fno-strict-overflow` 来代替)。



### 3.3.8. Python 调试级编译

​	调试版本 Python 是指带有 [`--with-pydebug`]({{< ref "/using/configure#cmdoption-with-pydebug" >}}) 参数的编译。

​	调试版本的效果：

- 默认显示所有警告：在 [`warnings`]({{< ref "/library/python/warnings#module-warnings" >}}) 模块中，默认警告过滤器的列表是空的。
- 在 [`sys.abiflags`]({{< ref "/library/python/sys#sys.abiflags" >}}) 中加入 `d` 标记。
- 加入 `sys.gettotalrefcount()` 函数。
- 命令行参数加入 [`-X showrefcount`]({{< ref "/using/cmdline#cmdoption-X" >}}) 。
- 添加 [`-d`]({{< ref "/using/cmdline#cmdoption-d" >}}) 命令行选项和 [`PYTHONDEBUG`]({{< ref "/using/cmdline#envvar-PYTHONDEBUG" >}}) 环境变量用于调试解析器。
- 添加对 `__lltrace__` 变量的支持：如果定义了该变量则会在字节码求值循环中启用低层级追踪。
- 安装 [内存分配调试钩子]({{< ref "/c_api/memory#default-memory-allocators" >}}) ，以便检测缓冲区溢出和其他内存错误。
- 定义宏 `Py_DEBUG` 和 `Py_REF_DEBUG` 。
- 增加运行时检查：针对由 `#ifdef Py_DEBUG` 和 `#endif` 所包裹的代码。 启用 `assert(...)` 和 `_PyObject_ASSERT(...)` 断言：不设置 `NDEBUG` 宏（另请参阅 [`--with-assertions`]({{< ref "/using/configure#cmdoption-with-assertions" >}}) 配置选项）。 主要的运行时检查有:
  - 增加了对函数参数的合理性检查。
  - 创建 Unicode 和 int 对象时，内存按某种模式进行了填充，用于检测是否使用了未初始化的对象。
  - 确保有能力清除或替换当前异常的函数在调用时不会引发异常。
  - 检查内存释放器函数是否不改变当前异常。
  - 垃圾收集器（[`gc.collect()`]({{< ref "/library/python/gc#gc.collect" >}}) 函数）对对象的一致性进行一些基本检查。
  - 从较宽类型转换到较窄类型时，`Py_SAFE_DOWNCAST()` 宏会检查整数下溢和上溢的情况。

​	参见 [Python 开发模式]({{< ref "/library/development/devmode#devmode" >}}) 和配置参数 [`--with-trace-refs`]({{< ref "/using/configure#cmdoption-with-trace-refs" >}}) 。

*在 3.8 版本发生变更:* 发布构建版和调试构建版现在是 ABI 兼容的：定义了 `Py_DEBUG` 宏不再意味着同时定义了 `Py_TRACE_REFS` 宏（参见 [`--with-trace-refs`]({{< ref "/using/configure#cmdoption-with-trace-refs" >}}) 选项）。

### 3.3.9. 调试选项

## **--with-pydebug**

[在调试模式下编译 Python]({{< ref "/using/configure#debug-build" >}}): 定义宏 `Py_DEBUG` (默认为禁用)。

## **--with-trace-refs**

​	为了调试而启用引用的跟踪（默认为禁用）。

​	效果如下：

- 定义 `Py_TRACE_REFS` 宏。
- Add [`sys.getobjects()`]({{< ref "/library/python/sys#sys.getobjects" >}}) function.
- 环境变量加入 [`PYTHONDUMPREFS`]({{< ref "/using/cmdline#envvar-PYTHONDUMPREFS" >}}) 。

[`PYTHONDUMPREFS`]({{< ref "/using/cmdline#envvar-PYTHONDUMPREFS" >}}) 环境变量可被用来转储在 Python 退出时仍然存活的对象和引用计数。

[静态分配的对象](https://docs.python.org/zh-cn/3.13/c-api/typeobj.html#static-types) 将不会被追踪。

> Added in version 3.8.
>

*在 3.13 版本发生变更:* 此构建版现在与发布构建版和 [调试构建版]({{< ref "/using/configure#debug-build" >}}) 是 ABI 兼容的。

## **--with-assertions**

​	编译时启用 C 断言：`assert(...);` 和 `_PyObject_ASSERT(...);` （默认不启用）。

​	如果设置此参数，则在 [`OPT`]({{< ref "/using/configure#envvar-OPT" >}}) 编译器变量中不定义 `NDEBUG` 宏。

​	参阅 [`--with-pydebug`]({{< ref "/using/configure#cmdoption-with-pydebug" >}}) 选项（[调试编译模式]({{< ref "/using/configure#debug-build" >}})），它也可以启用断言。

> Added in version 3.6.
>

## **--with-valgrind**

​	启用 Valgrind （默认禁用）。

## **--with-dtrace**

​	启用 DTrace（默认禁用）。

​	参阅 [用 DTrace 和 SystemTap 测试 CPython]({{< ref "/howto/instrumentation#instrumentation" >}})。

> Added in version 3.6.
>

## **--with-address-sanitizer**

​	启用 AddressSanitizer 内存错误检测 `asan`，（默认为禁用）。

> Added in version 3.6.
>

## **--with-memory-sanitizer**

​	启用 MemorySanitizer 内存错误检测 `msan`，（默认为禁用）。

> Added in version 3.6.
>

## **--with-undefined-behavior-sanitizer**

​	启用 undefinedBehaviorSanitizer 未定义行为检测 `ubsan`，（默认为禁用）。

> Added in version 3.6.
>

## **--with-thread-sanitizer**

​	启用 ThreadSanitizer 数据竞争检测器，`tsan` (默认为否）。

> Added in version 3.13.
>

### 3.3.10. 链接器选项

## **--enable-shared**

​	启用共享 Python 库 `libpython` 的编译（默认为禁用）。

## **--without-static-libpython**

​	不编译 `libpythonMAJOR.MINOR.a`，也不安装 `python.o` (默认会编译并安装)。

> Added in version 3.10.
>

### 3.3.11. 库选项

## **--with-libs**='lib1 ...'

​	链接附加库（默认不会）。

## **--with-system-expat**

​	用已安装的 `expat` 库编译 `pyexpat` 模块（默认为否）。

## **--with-system-libmpdec**

​	使用已安装的 `mpdecimal` 库来构建 `_decimal` 扩展模块，参见 [`decimal`]({{< ref "/library/numeric/decimal#module-decimal" >}}) 模块（默认为是）。

> Added in version 3.3.
>

*在 3.13 版本发生变更:* 默认为使用已安装的 `mpdecimal` 库。

*Deprecated since version 3.13, will be removed in version 3.15:* `mpdecimal` 库源代码的副本将不再随 Python 3.15 一起分发。

​	参见

 

[`LIBMPDEC_CFLAGS`]({{< ref "/using/configure#cmdoption-arg-LIBMPDEC_CFLAGS" >}}) 和 [`LIBMPDEC_LIBS`]({{< ref "/using/configure#cmdoption-arg-LIBMPDEC_LIBS" >}})。

## **--with-readline**=readline|editline

​	为 [`readline`]({{< ref "/library/text/readline#module-readline" >}}) 模块指定一个后端库。

- readline: 使用 readline 作为后端。
- editline: 使用 editline 作为后端。

> Added in version 3.10.
>

## **--without-readline**

​	不编译 [`readline`]({{< ref "/library/text/readline#module-readline" >}}) 模块（默认会）。

​	不定义 `HAVE_LIBREADLINE` 宏。

> Added in version 3.10.
>

## **--with-libm**=STRING

​	将 `libm` 数学库覆盖为 *STRING* (默认情况视系统而定)。

## **--with-libc**=STRING

​	将 `libc` C 库覆盖为 *STRING* (默认情况视系统而定)。

## **--with-openssl**=DIR

​	OpenSSL 的根目录。

> Added in version 3.7.
>

## **--with-openssl-rpath**=[no|auto|DIR]

​	设置 OpenSSL 库的运行时库目录（rpath）。

- `no` (默认): 不设置 rpath。
- `auto`：根据 [`--with-openssl`]({{< ref "/using/configure#cmdoption-with-openssl" >}}) 和 `pkg-config` 自动检测 rpath。
- *DIR* ：直接设置 rpath。

> Added in version 3.10.
>

### 3.3.12. 安全性选项

## **--with-hash-algorithm**=[fnv|siphash13|siphash24]

​	选择 `Python/pyhash.c` 采用的哈希算法。

- `siphash13` (默认值);
- `siphash24`;
- `fnv`.

> Added in version 3.4.
>

> Added in version 3.11:
> 增加了 `siphash13` 并且是新的默认值。

## **--with-builtin-hashlib-hashes**=md5,sha1,sha256,sha512,sha3,blake2

​	内置哈希模块：

- `md5`。
- `sha1`。
- `sha256`。
- `sha512`。
- `sha3` (带 shake)。
- `blake2`。

> Added in version 3.9.
>

## **--with-ssl-default-suites**=[python|openssl|STRING]

​	覆盖 OpenSSL 默认的密码套件字符串。

- `python` (默认值): 采用 Python 推荐选择。
- `openssl`：保留 OpenSSL 默认值不动。
- *STRING* ：采用自定义字符串。

​	参见 [`ssl`]({{< ref "/library/ipc/ssl#module-ssl" >}}) 模块。

> Added in version 3.7.
>

*在 3.10 版本发生变更:* 设置 `python` 和 *STRING* 也会把 TLS 1.2 设为最低版本的协议。

### 3.3.13. macOS 选项

​	参见 [Mac/README.rst](https://github.com/python/cpython/tree/3.13/Mac/README.rst)。

## **--enable-universalsdk**

## **--enable-universalsdk**=SDKDIR

​	创建通用的二进制版本。*SDKDIR* 指定应采用的 macOS SDK （默认为否）。

## **--enable-framework**

## **--enable-framework**=INSTALLDIR

​	创建 Python.framework ，而不是传统的 Unix 安装版。可选参数 *INSTALLDIR* 指定了安装路径（(默认为否）。

## **--with-universal-archs**=ARCH

​	指定应创建何种通用二进制文件。该选项仅当设置了 [`--enable-universalsdk`]({{< ref "/using/configure#cmdoption-enable-universalsdk" >}}) 时才有效。

​	可选项：

- `universal2`。
- `32-bit`。
- `64-bit`。
- `3-way`。
- `intel`。
- `intel-32`。
- `intel-64`。
- `all`。

## **--with-framework-name**=FRAMEWORK

​	为 macOS 中的 python 框架指定名称，仅当设置了 [`--enable-framework`]({{< ref "/using/configure#cmdoption-enable-framework" >}}) 时有效（默认：`Python`）。

## **--with-app-store-compliance**

## **--with-app-store-compliance**=PATCH-FILE

​	Python 标准库包含已知的当提交给 macOS 和 iOS 应用商店进行发布时会触发自动检查工具错误的字符串。 如果启用，该选项将应用已知可纠正应用商店合规性的补丁列表。 也可以指定自定义补丁文件。 在默认情况下将禁用此选项。

> Added in version 3.13.
>

### 3.3.14. iOS 选项

​	参见 [iOS/README.rst](https://github.com/python/cpython/tree/3.13/iOS/README.rst)。

## **--enable-framework**=INSTALLDIR

​	创建一个 Python 框架。 不同于 macOS，指定安装路径的 *INSTALLDIR* 参数是强制性的。

## **--with-framework-name**=FRAMEWORK

​	指定框架的名称 (默认名称: `Python`)。

### 3.3.15. 交叉编译选项

​	交叉编译，或称交叉构建，可被用于为不同的 CPU 架构或平台构建 Python。 交叉编译需要一个针对构建平台的 Python 解释器。 构建的 Python 版本必须与交叉编译的主机 Python 版本相匹配。

## **--build**=BUILD

​	用于在 BUILD 上执行构建的配置，通常由 **config.guess** 通过推测得到。

## **--host**=HOST

​	交叉编译以构建在 HOST (目标平台) 上运行的程序

## **--with-build-python**=path/to/python

​	针对交叉编译构建 `python` 二进制文件的路径

> Added in version 3.11.
>

## **CONFIG_SITE**=file

​	指向一个带有配置重载的的文件的环境变量。

​	示例 *config.site* 文件：

```
# config.site-aarch64
ac_cv_buggy_getaddrinfo=no
ac_cv_file__dev_ptmx=yes
ac_cv_file__dev_ptc=no
```

## **HOSTRUNNER**

​	用于针对交叉编译主机平台的运行 CPython 的程序。

> Added in version 3.11.
>

​	交叉编译示例:

```
CONFIG_SITE=config.site-aarch64 ../configure \
    --build=x86_64-pc-linux-gnu \
    --host=aarch64-unknown-linux-gnu \
    --with-build-python=../x86_64/python
```

## 3.4. Python 构建系统

### 3.4.1. 构建系统的主要文件

- `configure.ac` => `configure`;
- `Makefile.pre.in` => `Makefile` (由 `configure` 创建);
- `pyconfig.h` (由 `configure` 创建);
- `Modules/Setup`: 由Makefile 使用 `Module/makesetup` shell 脚本构建的 C 扩展;

### 3.4.2. 主要构建步骤

- C文件（ `.c` ）是作为对象文件（ `.o` ）构建的。
- 一个静态库 `libpython` （ `.a` ）是由对象文件创建的。
- `python.o` 和静态库 `libpython` 被链接到最终程序 `python` 中。
- C 扩展是由 Makefile 构建的 (参见 `Modules/Setup`)。

### 3.4.3. 主要 Makefile 目标

#### 3.4.3.1. make

​	对于大部分情况来说，当编译某段代码或从上游刷新你的签出内容之后重新构建时，你需要做的就是执行 `make`，它（按照 Make 的语义）将构建默认目标，即在 Makefile 中定义的第一个目标。 在传统上（包括在 CPython 项目中）这通常为 `all` 目标。 `configure` 脚本将扩展一个 `autoconf` 变量 `@DEF_MAKE_ALL_RULE@` 来准确地描述 `make all` 将构建哪个目标。 有如下三个选择：

- `profile-opt` (使用 `--enable-optimizations` 配置)
- `build_wasm` (使用 `--with-emscripten-target` 配置)
- `build_all` (不显式地使用任何其他配置)

​	根据最近的源文件更改，Make 将重新构建任何尚未被更新的目标（对象文件和可执行文件），包括在必要时再次运行 `configure`。 不过源/目标的依赖项数量很多并且是手动维护的，因此 Make 有时并没有所需的全部信息来正确地检测所有需要重新构建的目标。 根据尚未被重新构建的目标的具体情况，你可能会遇到许多问题。 如果你有无法确定原理的构建或测试问题，`make clean && make` 应该能够解决大多数依赖问题，代价则是会耗费更多的时间来构建。

#### 3.4.3.2. make platform

​	构建 `python` 程序，但不构造标准库扩展模块。 这将生成一个名为 `platform` 的文件，其中只包含一行描述构建平台详细信息的文本，例如 `macosx-14.3-arm64-3.12` 或 `linux-x86_64-3.13`。

#### 3.4.3.3. make profile-opt

​	使用 profile-guided optimization (PGO) 构建 Python。 你可以使用 [`--enable-optimizations`]({{< ref "/using/configure#cmdoption-enable-optimizations" >}}) 配置选项来使其成为 `make` 命令的默认目标（即对应 `make all` 或更简洁的 `make` 命令）。

#### 3.4.3.4. make clean

​	移除已构建文件。

#### 3.4.3.5. make distclean

​	在 `make clean` 所做的工作之外，还移除由配置脚本所创建的文件。 再次构建之前将需要运行 `configure`。 [[1\]]({{< ref "/using/configure#id5" >}})

#### 3.4.3.6. make install

​	构建 `all` 目标并安装 Python。

#### 3.4.3.7. make test

​	构建 `all` 目标并附带 `--fast-ci` 选项运行 Python 测试套件。 相关变量：

- `TESTOPTS`: 额外的回归测试命令行选项。
- `TESTPYTHONOPTS`: 额外的 Python 命令行选项。
- `TESTTIMEOUT`: 超时限制（默认值：10 分钟）。

#### 3.4.3.8. make buildbottest

​	这与 `make test` 类似，但会使用默认超时限制为 20 分钟的 `--slow-ci` 选项，而不是 `--fast-ci` 选项。

#### 3.4.3.9. make regen-all

​	重新生成（几乎）所有的已生成文件。 这包括（但不限于）字节码用例，以及解析器生成器文件。 对于其余的 [已生成文件]({{< ref "/using/configure#generated-files" >}}) 必须分别运行 `make regen-stdlib-module-names` 和 `autoconf`。

### 3.4.4. C 扩展

​	有些 C 扩展是作为内置模块构建的，比如 `sys` 模块。 它们在定义了 `Py_BUILD_CORE_BUILTIN` 宏的情况下构建。 内置模块没有 `__file__` 属性：



``` python
>>> import sys
>>> sys
<module 'sys' (built-in)>
>>> sys.__file__
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: module 'sys' has no attribute '__file__'
```

​	其他 C 扩展是作为动态库来构建的，比如 `_asyncio` 模块。 它们在定义了 `Py_BUILD_CORE_MODULE` 宏的情况下构建。 在 Linux x86-64 上的例子：



``` python
>>> import _asyncio
>>> _asyncio
<module '_asyncio' from '/usr/lib64/python3.9/lib-dynload/_asyncio.cpython-39-x86_64-linux-gnu.so'>
>>> _asyncio.__file__
'/usr/lib64/python3.9/lib-dynload/_asyncio.cpython-39-x86_64-linux-gnu.so'
```

`Modules/Setup` 用于生成 Makefile 目标，以构建 C 扩展。在文件的开头， C 被构建为内置模块。在标记 `*shared*` 之后定义的扩展被构建为动态库。

​	宏 `PyAPI_FUNC()`, `PyAPI_DATA()` 和 [`PyMODINIT_FUNC`]({{< ref "/c_api/intro#c.PyMODINIT_FUNC" >}}) 在 `Include/exports.h` 中的定义将因是否定义了 `Py_BUILD_CORE_MODULE` 宏而不同:

- 如果 `Py_BUILD_CORE_MODULE` 定义了，使用 `Py_EXPORTED_SYMBOL` 。
- 否则使用 `Py_IMPORTED_SYMBOL` 。

​	如果宏 `Py_BUILD_CORE_BUILTIN` 被错误地用在作为共享库构建的 C 扩展上，它的 `PyInit_*xxx*()` 函数就不会被导出，导致导入时出现 [`ImportError`]({{< ref "/library/exceptions#ImportError" >}}) 。

## 3.5. 编译器和链接器的标志

​	脚本 `./configure` 和环境变量设置的选项，并被 `Makefile` 使用。

### 3.5.1. 预处理器的标志

## **CONFIGURE_CPPFLAGS**

​	变量 [`CPPFLAGS`]({{< ref "/using/configure#envvar-CPPFLAGS" >}}) 的值被传递给 `./configure` 脚本。

> Added in version 3.6.
>

## **CPPFLAGS**

​	(Objective) C/C++ 预处理器标志，例如，如果头文件位于非标准的目录 *include_dir* 中，请使用 `-I*include_dir*` 。

[`CPPFLAGS`]({{< ref "/using/configure#envvar-CPPFLAGS" >}}) 和 [`LDFLAGS`]({{< ref "/using/configure#envvar-LDFLAGS" >}}) 都需要包含 shell 的值以便能够使用环境变量中指定的目录构建扩展模块。

## **BASECPPFLAGS**

> Added in version 3.4.
>

## **PY_CPPFLAGS**

​	为构建解释器对象文件增加了额外的预处理器标志。

​	默认为： `$(BASECPPFLAGS) -I. -I$(srcdir)/Include $(CONFIGURE_CPPFLAGS) $(CPPFLAGS)` 。

> Added in version 3.2.
>

### 3.5.2. 编译器标志

## **CC**

​	C 编译器指令。

​	例如： `gcc -pthread` 。

## **CXX**

​	C++ 编译器指令。

​	例如： `g++ -pthread` 。

## **CFLAGS**

​	C 编译器标志。

## **CFLAGS_NODIST**

[`CFLAGS_NODIST`]({{< ref "/using/configure#envvar-CFLAGS_NODIST" >}}) 用于构建解释器和 stdlib C 扩展。 一旦装好 Python 则当某个编译器旗标 *不应* 成为 [`CFLAGS`]({{< ref "/using/configure#envvar-CFLAGS" >}}) 的一部分时将可使用它 ([gh-65320](https://github.com/python/cpython/issues/65320))。

​	特别地，[`CFLAGS`]({{< ref "/using/configure#envvar-CFLAGS" >}}) 不应当包含:

- 编译器旗标 `-I` (用于为包括文件设置搜索路径)。 `-I` 旗标将按从左到右的顺序处理，并且 [`CFLAGS`]({{< ref "/using/configure#envvar-CFLAGS" >}}) 中的任何旗标都将优先于 user- 和 package- 层级所提供的 `-I` 旗标。
- 加固旗标如 `-Werror` 因为分发版无法控制由用户安装的包是否符合这样的高标准。

> Added in version 3.5.
>

## **COMPILEALL_OPTS**

​	当在 `make install` 中构建 PYC 文件时传给 [`compileall`]({{< ref "/library/language/compileall#module-compileall" >}}) 命令行的选项。 默认值: `-j0`。

> Added in version 3.12.
>

## **EXTRA_CFLAGS**

​	而外的 C 编译器指令。

## **CONFIGURE_CFLAGS**

​	变量 [`CFLAGS`]({{< ref "/using/configure#envvar-CFLAGS" >}}) 的值传递给 `./configure` 脚本。

> Added in version 3.2.
>

## **CONFIGURE_CFLAGS_NODIST**

​	变量 [`CFLAGS_NODIST`]({{< ref "/using/configure#envvar-CFLAGS_NODIST" >}}) 的值传递给 `./configure` 脚本。

> Added in version 3.5.
>

## **BASECFLAGS**

​	基础编译器标志。

## **OPT**

​	优化标志。

## **CFLAGS_ALIASING**

​	严格或不严格的别名标志，用于编译 `Python/dtoa.c` 、

> Added in version 3.7.
>

## **CCSHARED**

​	用于构建共享库的编译器标志。

​	例如， `-fPIC` 在 Linux 和 BSD 上使用。

## **CFLAGSFORSHARED**

​	为构建解释器对象文件增加了额外的 C 标志。

​	，默认为： `$(CCSHARED)` ，当 [`--enable-shared`]({{< ref "/using/configure#cmdoption-enable-shared" >}}) 被使用时，则为空字符串

## **PY_CFLAGS**

​	默认为： `$(BASECFLAGS) $(OPT) $(CONFIGURE_CFLAGS) $(CFLAGS) $(EXTRA_CFLAGS)` 。

## **PY_CFLAGS_NODIST**

​	默认为： `$(CONFIGURE_CFLAGS_NODIST) $(CFLAGS_NODIST) -I$(srcdir)/Include/internal` 。

> Added in version 3.5.
>

## **PY_STDMODULE_CFLAGS**

​	用于构建解释器对象文件的 C 标志。

​	默认为： `$(PY_CFLAGS) $(PY_CFLAGS_NODIST) $(PY_CPPFLAGS) $(CFLAGSFORSHARED)`。

> Added in version 3.7.
>

## **PY_CORE_CFLAGS**

​	默认为 `$(PY_STDMODULE_CFLAGS) -DPy_BUILD_CORE` 。

> Added in version 3.2.
>

## **PY_BUILTIN_MODULE_CFLAGS**

​	编译器标志，将标准库的扩展模块作为内置模块来构建，如 [`posix`]({{< ref "/library/unix/posix#module-posix" >}}) 模块

​	默认为： `$(PY_STDMODULE_CFLAGS) -DPy_BUILD_CORE_BUILTIN` 。

> Added in version 3.8.
>

## **PURIFY**

​	Purify 命令。 Purify 是一个内存调试程序。

​	默认为：空字符串（不使用）。

### 3.5.3. 链接器标志位

## **LINKCC**

​	用于构建如 `python` 和 `_testembed` 的程序的链接器命令。

​	默认值: `$(PURIFY) $(CC)`。

## **CONFIGURE_LDFLAGS**

​	变量 [`LDFLAGS`]({{< ref "/using/configure#envvar-LDFLAGS" >}}) 的值被传递给 `./configure` 脚本。

​	避免指定 [`CFLAGS`]({{< ref "/using/configure#envvar-CFLAGS" >}}) ， [`LDFLAGS`]({{< ref "/using/configure#envvar-LDFLAGS" >}}) 等，这样用户就可以在命令行上使用它们来追加这些值，而不用触碰到预设的值。

> Added in version 3.2.
>

## **LDFLAGS_NODIST**

[`LDFLAGS_NODIST`]({{< ref "/using/configure#envvar-LDFLAGS_NODIST" >}}) 的使用方式与 [`CFLAGS_NODIST`]({{< ref "/using/configure#envvar-CFLAGS_NODIST" >}}) 相同。 一旦装好 Python 则当某个链接器旗标 *不应* 成为 [`LDFLAGS`]({{< ref "/using/configure#envvar-LDFLAGS" >}}) 的一部分时将可使用它 ([gh-65320](https://github.com/python/cpython/issues/65320))。

​	特别地，[`LDFLAGS`]({{< ref "/using/configure#envvar-LDFLAGS" >}}) 不应当包含:

- 编译器旗标 `-L` (用于为库设置搜索路径)。 `-L` 旗标将按从左到右的顺序处理，并且 [`LDFLAGS`]({{< ref "/using/configure#envvar-LDFLAGS" >}}) 中的任何旗标都将优先于 user- 和 package 层级所提供的 `-L` 旗标。

## **CONFIGURE_LDFLAGS_NODIST**

​	变量 [`LDFLAGS_NODIST`]({{< ref "/using/configure#envvar-LDFLAGS_NODIST" >}}) 的值传递给 `./configure` 脚本。

> Added in version 3.8.
>

## **LDFLAGS**

​	链接器标志，例如，如果库位于非标准的目录 *lib_dir* 中，请使用 `-L*lib_dir*` 。

[`CPPFLAGS`]({{< ref "/using/configure#envvar-CPPFLAGS" >}}) 和 [`LDFLAGS`]({{< ref "/using/configure#envvar-LDFLAGS" >}}) 都需要包含 shell 的值以便能够使用环境变量中指定的目录构建扩展模块。

## **LIBS**

​	链接器标志，在链接 Python 可执行文件时将库传递给链接器。

​	例如： `-lrt` 。

## **LDSHARED**

​	构建一个共享库的命令。

​	默认为： `@LDSHARED@ $(PY_LDFLAGS)` 。

## **BLDSHARED**

​	构建共享库 `libpython` 的命令。

​	默认为： `@BLDSHARED@ $(PY_CORE_LDFLAGS)` 。

## **PY_LDFLAGS**

​	默认为： `$(CONFIGURE_LDFLAGS) $(LDFLAGS)` 。

## **PY_LDFLAGS_NODIST**

​	默认为： `$(CONFIGURE_LDFLAGS_NODIST) $(LDFLAGS_NODIST)` 。

> Added in version 3.8.
>

## **PY_CORE_LDFLAGS**

​	用于构建解释器对象文件的链接器标志。

> Added in version 3.8.
>

​	备注

[[1]({{< ref "/using/configure#id3" >}})]

`git clean -fdx` 是“清理”你的签出内容的更激进方式。 它将移除所有对 Git 来说未知的文件。 当使用 `git bisect` 查找程序错误时，[推荐在多次探查之间](https://github.com/python/cpython/issues/114505#issuecomment-1907021718) 执行此命令来确保完整的全新构建。 **请谨慎使用**，因为它将删除所有未签入 Git 的文件，包括你最新的、尚未提交的工作。
