+++
title = "msvcrt --- 来自 MS VC++ 运行时的有用例程"
date = 2024-11-15T21:28:55+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/msvcrt.html](https://docs.python.org/zh-cn/3.13/library/msvcrt.html)
>
> 收录该文档的时间：`2024-11-15T21:28:55+08:00`

# `msvcrt` --- 来自 MS VC++ 运行时的有用例程

------

​	这些函数提供了对 Windows 平台上一些有用功能的访问。 一些更高层级的模块使用这些函数来构建其服务的 Windows 实现。 例如，[`getpass`](https://docs.python.org/zh-cn/3.13/library/getpass.html#module-getpass) 模块在 [`getpass()`](https://docs.python.org/zh-cn/3.13/library/getpass.html#module-getpass) 函数的实现中就用到了它。

​	关于这些函数的更多信息可以在平台 API 文档中找到。

​	该模块实现了控制台 I/O API 的普通和宽字符变体。普通的 API 只处理ASCII字符，国际化应用受限。应该尽可能地使用宽字符 API 。

*在 3.3 版本发生变更:* 此模块中过去会引发 [`IOError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#IOError) 的操作现在将引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError)。



## 文件操作

## msvcrt.**locking**(*fd*, *mode*, *nbytes*)

​	基于文件描述符 *fd* 从 C 运行时锁定文件的某个部分。 失败时将引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError)。 锁定的文件区域从当前文件位置扩展 *nbytes* 个字节，并可能持续到超出文件末尾。 *mode* 必须为下面列出的 `LK_*` 常量之一。 一个文件中的多个区域可以被同时锁定，但是不能重叠。 相邻的区域不会被合并；它们必须被单独解锁。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `msvcrt.locking` 并附带参数 `fd`, `mode`, `nbytes`。

## msvcrt.**LK_LOCK**

## msvcrt.**LK_RLCK**

​	锁定指定的字节数据。 如果字节数据无法被锁定，程序会在 1 秒后立即重试。 如果在 10 次尝试后字节数据仍无法被锁定，则会引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError)。

## msvcrt.**LK_NBLCK**

## msvcrt.**LK_NBRLCK**

​	锁定指定的字节数据。 如果字节数据无法被锁定，则会引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError)。

## msvcrt.**LK_UNLCK**

​	解锁指定的字节数据，该对象必须在之前被锁定。

## msvcrt.**setmode**(*fd*, *flags*)

​	设置文件描述符 *fd* 的行结束符转写模式。 要将其设为文本模式，则 *flags* 应当为 [`os.O_TEXT`](https://docs.python.org/zh-cn/3.13/library/os.html#os.O_TEXT)；设为二进制模式，则应当为 [`os.O_BINARY`](https://docs.python.org/zh-cn/3.13/library/os.html#os.O_BINARY)。

## msvcrt.**open_osfhandle**(*handle*, *flags*)

​	基于文件句柄 *handle* 创建一个 C 运行时文件描述符。 *flags* 形参应当是 [`os.O_APPEND`](https://docs.python.org/zh-cn/3.13/library/os.html#os.O_APPEND), [`os.O_RDONLY`](https://docs.python.org/zh-cn/3.13/library/os.html#os.O_RDONLY), [`os.O_TEXT`](https://docs.python.org/zh-cn/3.13/library/os.html#os.O_TEXT) 和 [`os.O_NOINHERIT`](https://docs.python.org/zh-cn/3.13/library/os.html#os.O_NOINHERIT) 按位 OR 的结果。 返回的文件描述符可以被用作传给 [`os.fdopen()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.fdopen) 的形参以创建一个文件对象。

​	该文件描述符默认是不可继承的。 传入 [`os.O_NOINHERIT`](https://docs.python.org/zh-cn/3.13/library/os.html#os.O_NOINHERIT) 旗标可使它成为非不可继承的。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `msvcrt.open_osfhandle` 并附带参数 `handle`, `flags`。

## msvcrt.**get_osfhandle**(*fd*)

​	返回文件描述符 *fd* 的文件句柄。 如果 *fd* 不能被识别则会引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError)。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `msvcrt.get_osfhandle` 并附带参数 `fd`。



## 控制台 I/O

## msvcrt.**kbhit**()

​	如果正在等待读取一个按键则返回一个非零值。 在其他情况下，将返回 0。

## msvcrt.**getch**()

​	读取一个按键并将结果字符以字节串形式返回。 不会回显到控制台。 如果没有任何按键可用则此调用将会阻塞，但它不会等待 Enter 被按下。 如果按下的键是一个特殊功能键，此函数将返回 `'\000'` 或 `'\xe0'`；下一次调用将返回键代码。 Control-C 按键无法使用此函数来读取。

## msvcrt.**getwch**()

[`getch()`](https://docs.python.org/zh-cn/3.13/library/msvcrt.html#msvcrt.getch) 的宽字符版本，返回一个 Unicode 值。

## msvcrt.**getche**()

​	类似于 [`getch()`](https://docs.python.org/zh-cn/3.13/library/msvcrt.html#msvcrt.getch)，但如果按键是代表一个可打印字符则它将被回显。

## msvcrt.**getwche**()

[`getche()`](https://docs.python.org/zh-cn/3.13/library/msvcrt.html#msvcrt.getche) 的宽字符版本，返回一个 Unicode 值。

## msvcrt.**putch**(*char*)

​	将字符串 *char* 打印到终端，不使用缓冲区。

## msvcrt.**putwch**(*unicode_char*)

[`putch()`](https://docs.python.org/zh-cn/3.13/library/msvcrt.html#msvcrt.putch) 的宽字符版本，接受一个 Unicode 值。

## msvcrt.**ungetch**(*char*)

​	使得字节串 *char* 被“推回”终端缓冲区；它将是被 [`getch()`](https://docs.python.org/zh-cn/3.13/library/msvcrt.html#msvcrt.getch) 或 [`getche()`](https://docs.python.org/zh-cn/3.13/library/msvcrt.html#msvcrt.getche) 读取的下一个字符。

## msvcrt.**ungetwch**(*unicode_char*)

[`ungetch()`](https://docs.python.org/zh-cn/3.13/library/msvcrt.html#msvcrt.ungetch) 的宽字符版本，接受一个 Unicode 值。



## 其他函数

## msvcrt.**heapmin**()

​	强制 `malloc()` 堆清空自身并将未使用的块返回给操作系统。 失败时，这将引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError)。

## msvcrt.**set_error_mode**(*mode*)

​	更改 C 运行时为可能终止程序的错误写入错误消息的位置。 *mode* 必须是下面列出的 `OUT_*` 常量或 [`REPORT_ERRMODE`](https://docs.python.org/zh-cn/3.13/library/msvcrt.html#msvcrt.REPORT_ERRMODE) 中的一个。 返回旧设置或者在有错误发生时返回 -1。 仅在 [Python 调试构建版](https://docs.python.org/zh-cn/3.13/using/configure.html#debug-build) 中可用。

## msvcrt.**OUT_TO_DEFAULT**

​	错误 sink 是由应用程序的类型决定的。 仅在 [Python 调试构建版](https://docs.python.org/zh-cn/3.13/using/configure.html#debug-build) 中可用。

## msvcrt.**OUT_TO_STDERR**

​	错误 sink 是某个标准错误。 仅在 [Python 调试构建版](https://docs.python.org/zh-cn/3.13/using/configure.html#debug-build) 中可用。

## msvcrt.**OUT_TO_MSGBOX**

​	错误 sink 是某个消息框。 仅在 [Python 调试构建版](https://docs.python.org/zh-cn/3.13/using/configure.html#debug-build) 中可用。

## msvcrt.**REPORT_ERRMODE**

​	报告当前错误模式值。 仅在 [Python 调试构建版](https://docs.python.org/zh-cn/3.13/using/configure.html#debug-build) 中可用。

## msvcrt.**CrtSetReportMode**(*type*, *mode*)

​	为 `_CrtDbgReport()` 在 MS VC++ 运行时中生成的特定报告类型指定一个目标或多个目标。 *type* 必须是下面列出的 `CRT_*` 常量之一。 *mode* 必须是下面列出的 `CRTDBG_*` 常量之一。 仅在 [Python 调试构建版](https://docs.python.org/zh-cn/3.13/using/configure.html#debug-build) 中可用。

## msvcrt.**CrtSetReportFile**(*type*, *file*)

​	在你使用 [`CrtSetReportMode()`](https://docs.python.org/zh-cn/3.13/library/msvcrt.html#msvcrt.CrtSetReportMode) 来指定 [`CRTDBG_MODE_FILE`](https://docs.python.org/zh-cn/3.13/library/msvcrt.html#msvcrt.CRTDBG_MODE_FILE) 之后，你可以指定接收消息文本的文件句柄。 *type* 必须是下面列出的 `CRT_*` 常量之一。 *file* 应当是你希望指定的文件句柄。 仅在 [Python 调试构建版](https://docs.python.org/zh-cn/3.13/using/configure.html#debug-build) 中可用。

## msvcrt.**CRT_WARN**

​	不需要立即关注的警告、消息和信息。

## msvcrt.**CRT_ERROR**

​	需要立即关注的错误、不可恢复的问题和议题。

## msvcrt.**CRT_ASSERT**

​	断言失败

## msvcrt.**CRTDBG_MODE_DEBUG**

​	将消息写至调试器的输出窗口。

## msvcrt.**CRTDBG_MODE_FILE**

​	将消息写入用户提供的文件句柄。 应当调用 [`CrtSetReportFile()`](https://docs.python.org/zh-cn/3.13/library/msvcrt.html#msvcrt.CrtSetReportFile) 来定义要用作写入目标的特定文件或流。

## msvcrt.**CRTDBG_MODE_WNDW**

​	创建一个消息框来显示消息并提供 `Abort`, `Retry` 和 `Ignore` 等按钮。

## msvcrt.**CRTDBG_REPORT_MODE**

​	返回指定 *type* 当前的 *mode*。

## msvcrt.**CRT_ASSEMBLY_VERSION**

​	CRT 汇编版，来自 `crtassem.h` 头文件。

## msvcrt.**VC_ASSEMBLY_PUBLICKEYTOKEN**

​	VC 汇编公钥凭据，来自 `crtassem.h` 头文件。

## msvcrt.**LIBRARIES_ASSEMBLY_NAME_PREFIX**

​	库汇编名称前缀，来自 `crtassem.h` 头文件。
