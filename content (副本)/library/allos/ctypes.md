+++
title = "ctypes --- Python 的外部函数库"
date = 2024-11-15T12:09:25+08:00
weight = 140
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/ctypes.html](https://docs.python.org/zh-cn/3.13/library/ctypes.html)
>
> 收录该文档的时间：`2024-11-15T12:09:25+08:00`

# `ctypes` --- Python 的外部函数库

**源代码:** [Lib/ctypes](https://github.com/python/cpython/tree/3.13/Lib/ctypes)

------

[`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 是 Python 的外部函数库。它提供了与 C 兼容的数据类型，并允许调用 DLL 或共享库中的函数。可使用该模块以纯 Python 形式对这些库进行封装。



## ctypes 教程

​	注：本教程中的示例代码使用 [`doctest`]({{< ref "/library/development/doctest#module-doctest" >}}) 来保证它们能正确运行。 由于有些代码示例在 Linux, Windows 或 macOS 上的行为有所不同，它们在注释中包含了一些 doctest 指令。

​	注意：部分示例代码引用了 ctypes [`c_int`]({{< ref "/library/allos/ctypes#ctypes.c_int" >}}) 类型。在 `sizeof(long) == sizeof(int)` 的平台上此类型是 [`c_long`]({{< ref "/library/allos/ctypes#ctypes.c_long" >}}) 的一个别名。所以，在程序输出 [`c_long`]({{< ref "/library/allos/ctypes#ctypes.c_long" >}}) 而不是你期望的 [`c_int`]({{< ref "/library/allos/ctypes#ctypes.c_int" >}}) 时不必感到迷惑 --- 它们实际上是同一种类型。



### 载入动态连接库

[`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 导出了 *cdll* 对象，在 Windows 系统中还导出了 *windll* 和 *oledll* 对象用于载入动态连接库。

​	您可以通过访问这些对象的属性来加载库。 *cdll* 加载使用标准 `cdecl` 调用约定导出函数的库，而 *windll* 库则使用 `stdcall` 调用约定调用函数。 *oledll* 也使用 `stdcall` 调用约定，并假定函数返回 Windows `HRESULT` 错误代码。 当函数调用失败时会使用错误代码自动引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 异常。

*在 3.3 版本发生变更:* 原来在 Windows 下抛出的异常类型 [`WindowsError`]({{< ref "/library/exceptions#WindowsError" >}}) 现在是 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 的一个别名。

​	这是一些 Windows 下的例子。 请注意 `msvcrt` 是包含大部分 C 函数的 MS 标准 C 库，并会使用 `cdecl` 调用惯例:



``` python
>>> from ctypes import *
>>> print(windll.kernel32)  
<WinDLL 'kernel32', handle ... at ...>
>>> print(cdll.msvcrt)      
<CDLL 'msvcrt', handle ... at ...>
>>> libc = cdll.msvcrt      
>>>
```

​	Windows 会自动添加通常的 `.dll` 文件扩展名。

​	备注

 

​	通过 `cdll.msvcrt` 调用的标准 C 函数，可能会导致调用一个过时的，与当前 Python 所不兼容的函数。因此，请尽量使用标准的 Python 函数，而不要使用 `msvcrt` 模块。

​	在 Linux 中，要求指定文件名 *包括* 扩展名来加载库，因此不能使用属性访问的方式来加载库。 你应当使用 dll 加载器的 [`LoadLibrary()`]({{< ref "/library/allos/ctypes#ctypes.LibraryLoader.LoadLibrary" >}}) 方法，或是应当通过调用构造器创建 CDLL 的实例来加载库:



``` python
>>> cdll.LoadLibrary("libc.so.6")  
<CDLL 'libc.so.6', handle ... at ...>
>>> libc = CDLL("libc.so.6")       
>>> libc                           
<CDLL 'libc.so.6', handle ... at ...>
>>>
```



### 操作导入的动态链接库中的函数

​	通过操作dll对象的属性来操作这些函数。



``` python
>>> libc.printf
<_FuncPtr object at 0x...>
>>> print(windll.kernel32.GetModuleHandleA)  
<_FuncPtr object at 0x...>
>>> print(windll.kernel32.MyOwnFunction)     
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "ctypes.py", line 239, in __getattr__
    func = _StdcallFuncPtr(name, self)
AttributeError: function 'MyOwnFunction' not found
>>>
```

​	请注意 win32 系统的动态库如 `kernel32` 和 `user32` 通常会同时导出一个函数的 ANSI 版本和 UNICODE 版本。 UNICODE 版本导出时会在名称后加上 `W`，而 ANSI 版本导出时会在名称后加上 `A`。 win32 `GetModuleHandle` 函数会为给定的模块名称返回一个 *模块句柄*，它具有以下的 C 原型，以及一个被用来根据是否定义了 UNICODE 将其中之一暴露为 `GetModuleHandle` 的宏:

```
/* ANSI version */
HMODULE GetModuleHandleA(LPCSTR lpModuleName);
/* UNICODE version */
HMODULE GetModuleHandleW(LPCWSTR lpModuleName);
```

*windll* 不会通过这样的魔法手段来帮你决定选择哪一种函数，你必须显式的调用 `GetModuleHandleA` 或 `GetModuleHandleW`，并分别使用字节对象或字符串对象作参数。

​	有时候，dlls的导出的函数名不符合 Python 的标识符规范，比如 `"??2@YAPAXI@Z"`。此时，你必须使用 [`getattr()`]({{< ref "/library/functions#getattr" >}}) 方法来获得该函数。



``` python
>>> getattr(cdll.msvcrt, "??2@YAPAXI@Z")  
<_FuncPtr object at 0x...>
>>>
```

​	Windows 下，有些 dll 导出的函数没有函数名，而是通过其顺序号调用。对此类函数，你也可以通过 dll 对象的数值索引来操作这些函数。



``` python
>>> cdll.kernel32[1]  
<_FuncPtr object at 0x...>
>>> cdll.kernel32[0]  
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "ctypes.py", line 310, in __getitem__
    func = _StdcallFuncPtr(name, self)
AttributeError: function ordinal 0 not found
>>>
```



### 调用函数

​	你可以像任何其它 Python 可调用对象一样调用这些函数。 这个例子使用了 `rand()` 函数，它不接收任何参数并返回一个伪随机整数:



``` python
>>> print(libc.rand())  
1804289383
```

​	在 Windows 上，你可以调用 `GetModuleHandleA()` 函数，它返回一个 win32 模块句柄 (将 `None` 作为唯一参数传入以使用 `NULL` 指针来调用它):



``` python
>>> print(hex(windll.kernel32.GetModuleHandleA(None)))  
0x1d000000
>>>
```

​	如果你用 `cdecl` 调用方式调用 `stdcall` 约定的函数，则会甩出一个异常 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。反之亦然。



``` python
>>> cdll.kernel32.GetModuleHandleA(None)  
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: Procedure probably called with not enough arguments (4 bytes missing)
>>>

>>> windll.msvcrt.printf(b"spam")  
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: Procedure probably called with too many arguments (4 bytes in excess)
>>>
```

​	你必须阅读这些库的头文件或说明文档来确定它们的正确的调用协议。

​	在 Windows 中，[`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 使用 win32 结构化异常处理来防止由于在调用函数时使用非法参数导致的程序崩溃。



``` python
>>> windll.kernel32.GetModuleHandleA(32)  
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
OSError: exception: access violation reading 0x00000020
>>>
```

​	然而，总有许多办法，通过调用 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 使得 Python 程序崩溃。因此，你必须小心使用。 [`faulthandler`]({{< ref "/library/debug/faulthandler#module-faulthandler" >}}) 模块可以用于帮助诊断程序崩溃的原因。（比如由于错误的C库函数调用导致的段错误）。

`None`、整数、字节串对象和（Unicode）字符串是仅有的可以直接作为这些函数调用的形参的原生 Python 对象。 `None` 将作为 C `NULL` 指针传入，字节串对象和字符串将作为指向包含其数据 (char* 或 wchar_t*) 的内存块的指针传入。 Python 整数将作为平台默认的 C int 类型传入，它们的值会被截断以适应 C 类型的长度。

​	在我们开始调用函数前，我们必须先了解作为函数参数的 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 数据类型。



### 基础数据类型

[`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 定义了一些和C兼容的基本数据类型：

| ctypes 类型                                                  | C 类型                                                       | Python 类型         |
| :----------------------------------------------------------- | :----------------------------------------------------------- | :------------------ |
| [`c_bool`]({{< ref "/library/allos/ctypes#ctypes.c_bool" >}}) | _Bool                                                        | bool (1)            |
| [`c_char`]({{< ref "/library/allos/ctypes#ctypes.c_char" >}}) | char                                                         | 单字符字节串对象    |
| [`c_wchar`]({{< ref "/library/allos/ctypes#ctypes.c_wchar" >}}) | `wchar_t`                                                    | 单字符字符串        |
| [`c_byte`]({{< ref "/library/allos/ctypes#ctypes.c_byte" >}}) | char                                                         | int                 |
| [`c_ubyte`]({{< ref "/library/allos/ctypes#ctypes.c_ubyte" >}}) | unsigned char                                                | int                 |
| [`c_short`]({{< ref "/library/allos/ctypes#ctypes.c_short" >}}) | short                                                        | int                 |
| [`c_ushort`]({{< ref "/library/allos/ctypes#ctypes.c_ushort" >}}) | unsigned short                                               | int                 |
| [`c_int`]({{< ref "/library/allos/ctypes#ctypes.c_int" >}}) | int                                                          | int                 |
| [`c_uint`]({{< ref "/library/allos/ctypes#ctypes.c_uint" >}}) | unsigned int                                                 | int                 |
| [`c_long`]({{< ref "/library/allos/ctypes#ctypes.c_long" >}}) | long                                                         | int                 |
| [`c_ulong`]({{< ref "/library/allos/ctypes#ctypes.c_ulong" >}}) | unsigned long                                                | int                 |
| [`c_longlong`]({{< ref "/library/allos/ctypes#ctypes.c_longlong" >}}) | __int64 或 long long                                         | int                 |
| [`c_ulonglong`]({{< ref "/library/allos/ctypes#ctypes.c_ulonglong" >}}) | unsigned __int64 或 unsigned long long                       | int                 |
| [`c_size_t`]({{< ref "/library/allos/ctypes#ctypes.c_size_t" >}}) | `size_t`                                                     | int                 |
| [`c_ssize_t`]({{< ref "/library/allos/ctypes#ctypes.c_ssize_t" >}}) | `ssize_t` 或 [Py_ssize_t]({{< ref "/c_api/intro#c.Py_ssize_t" >}}) | int                 |
| [`c_time_t`]({{< ref "/library/allos/ctypes#ctypes.c_time_t" >}}) | `time_t`                                                     | int                 |
| [`c_float`]({{< ref "/library/allos/ctypes#ctypes.c_float" >}}) | float                                                        | float               |
| [`c_double`]({{< ref "/library/allos/ctypes#ctypes.c_double" >}}) | double                                                       | float               |
| [`c_longdouble`]({{< ref "/library/allos/ctypes#ctypes.c_longdouble" >}}) | long double                                                  | float               |
| [`c_char_p`]({{< ref "/library/allos/ctypes#ctypes.c_char_p" >}}) | char* (以 NUL 结尾)                                          | 字节串对象或 `None` |
| [`c_wchar_p`]({{< ref "/library/allos/ctypes#ctypes.c_wchar_p" >}}) | wchar_t* (以 NUL 结尾)                                       | 字符串或 `None`     |
| [`c_void_p`]({{< ref "/library/allos/ctypes#ctypes.c_void_p" >}}) | void*                                                        | int 或 `None`       |

1. 构造函数接受任何具有真值的对象。

​	所有这些类型都可以通过使用正确类型和值的可选初始值调用它们来创建:



``` python
>>> c_int()
c_long(0)
>>> c_wchar_p("Hello, World")
c_wchar_p(140018365411392)
>>> c_ushort(-3)
c_ushort(65533)
>>>
```

​	由于这些类型是可变的，它们的值也可以在以后更改:



``` python
>>> i = c_int(42)
>>> print(i)
c_long(42)
>>> print(i.value)
42
>>> i.value = -99
>>> print(i.value)
-99
>>>
```

​	当给指针类型的对象 [`c_char_p`]({{< ref "/library/allos/ctypes#ctypes.c_char_p" >}}), [`c_wchar_p`]({{< ref "/library/allos/ctypes#ctypes.c_wchar_p" >}}) 和 [`c_void_p`]({{< ref "/library/allos/ctypes#ctypes.c_void_p" >}}) 等赋值时，将改变它们所指向的 *内存地址*，而 *不是* 它们所指向的内存区域的 *内容* (这是理所当然的，因为 Python 的 bytes 对象是不可变的):



``` python
>>> s = "Hello, World"
>>> c_s = c_wchar_p(s)
>>> print(c_s)
c_wchar_p(139966785747344)
>>> print(c_s.value)
Hello World
>>> c_s.value = "Hi, there"
>>> print(c_s)              # 内存分配已改变
c_wchar_p(139966783348904)
>>> print(c_s.value)
Hi, there
>>> print(s)                # 第一个对象未改变
Hello, World
>>>
```

​	但你要注意不能将它们传递给会改变指针所指内存的函数。如果你需要可改变的内存块，ctypes 提供了 [`create_string_buffer()`]({{< ref "/library/allos/ctypes#ctypes.create_string_buffer" >}}) 函数，它提供多种方式创建这种内存块。当前的内存块内容可以通过 `raw` 属性存取，如果你希望将它作为NUL结束的字符串，请使用 `value` 属性:



``` python
>>> from ctypes import *
>>> p = create_string_buffer(3)            # 创建一个 3 字节的缓冲区，初始化为 NUL 字节
>>> print(sizeof(p), repr(p.raw))
3 b'\x00\x00\x00'
>>> p = create_string_buffer(b"Hello")     # 创建一个包含以 NUL 结束的字符串的缓冲区
>>> print(sizeof(p), repr(p.raw))
6 b'Hello\x00'
>>> print(repr(p.value))
b'Hello'
>>> p = create_string_buffer(b"Hello", 10) # 创建一个 10 字节的缓冲区
>>> print(sizeof(p), repr(p.raw))
10 b'Hello\x00\x00\x00\x00\x00'
>>> p.value = b"Hi"
>>> print(sizeof(p), repr(p.raw))
10 b'Hi\x00lo\x00\x00\x00\x00\x00'
>>>
```

[`create_string_buffer()`]({{< ref "/library/allos/ctypes#ctypes.create_string_buffer" >}}) 函数取代了旧了 `c_buffer()` 函数（后者仍可作为别名使用）。 要创建一个包含 C 类型 `wchar_t` 的 unicode 字符的可变内存块，请使用 [`create_unicode_buffer()`]({{< ref "/library/allos/ctypes#ctypes.create_unicode_buffer" >}}) 函数。



### 调用函数，继续

​	注意 printf 将打印到真正标准输出设备，而*不是* [`sys.stdout`]({{< ref "/library/python/sys#sys.stdout" >}})，因此这些实例只能在控制台提示符下工作，而不能在 *IDLE* 或 *PythonWin* 中运行。



``` python
>>> printf = libc.printf
>>> printf(b"Hello, %s\n", b"World!")
Hello, World!
14
>>> printf(b"Hello, %S\n", "World!")
Hello, World!
14
>>> printf(b"%d bottles of beer\n", 42)
42 bottles of beer
19
>>> printf(b"%f bottles of beer\n", 42.5)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ctypes.ArgumentError: argument 2: TypeError: Don't know how to convert parameter 2
>>>
```

​	正如前面所提到过的，除了整数、字符串以及字节串之外，所有的 Python 类型都必须使用它们对应的 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 类型包装，才能够被正确地转换为所需的C语言类型。



``` python
>>> printf(b"An int %d, a double %f\n", 1234, c_double(3.14))
An int 1234, a double 3.140000
31
>>>
```



### 调用可变函数

​	在许多平台上通过 ctypes 调用可变函数与调用带有固定数量形参的函数是完全一样的。 在某些平台，特别是针对 Apple 平台的 ARM64 上，可变函数的调用约定与常规函数则是不同的。

​	On those platforms it is required to specify the [`argtypes`]({{< ref "/library/allos/ctypes#ctypes._CFuncPtr.argtypes" >}}) attribute for the regular, non-variadic, function arguments:

```
libc.printf.argtypes = [ctypes.c_char_p]
```

​	Because specifying the attribute does not inhibit portability it is advised to always specify [`argtypes`]({{< ref "/library/allos/ctypes#ctypes._CFuncPtr.argtypes" >}}) for all variadic functions.



### 使用自定义的数据类型调用函数

​	您也可以通过自定义 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 参数转换方式来允许将你自己的类实例作为函数参数。 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 会寻找 `_as_parameter_` 属性并使用它作为函数参数。 属性必须是整数、字符串、字节串、[`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 实例或者带有 `_as_parameter_` 属性的对象:



``` python
>>> class Bottles:
...     def __init__(self, number):
...         self._as_parameter_ = number
...
>>> bottles = Bottles(42)
>>> printf(b"%d bottles of beer\n", bottles)
42 bottles of beer
19
>>>
```

​	如果你不想将实例数据存储在 `_as_parameter_` 实例变量中，可以定义一个根据请求提供属性的 [`property`]({{< ref "/library/functions#property" >}})。



### 指定必选参数的类型(函数原型)

​	It is possible to specify the required argument types of functions exported from DLLs by setting the [`argtypes`]({{< ref "/library/allos/ctypes#ctypes._CFuncPtr.argtypes" >}}) attribute.

[`argtypes`]({{< ref "/library/allos/ctypes#ctypes._CFuncPtr.argtypes" >}}) must be a sequence of C data types (the `printf()` function is probably not a good example here, because it takes a variable number and different types of parameters depending on the format string, on the other hand this is quite handy to experiment with this feature):



``` python
>>> printf.argtypes = [c_char_p, c_char_p, c_int, c_double]
>>> printf(b"String '%s', Int %d, Double %f\n", b"Hi", 10, 2.2)
String 'Hi', Int 10, Double 2.200000
37
>>>
```

​	指定数据类型可以防止不合理的参数传递（就像 C 函数的原型），并且会自动尝试将参数转换为需要的类型:



``` python
>>> printf(b"%d %d %d", 1, 2, 3)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ctypes.ArgumentError: argument 2: TypeError: 'int' object cannot be interpreted as ctypes.c_char_p
>>> printf(b"%s %d %f\n", b"X", 2, 3)
X 2 3.000000
13
>>>
```

​	If you have defined your own classes which you pass to function calls, you have to implement a [`from_param()`]({{< ref "/library/allos/ctypes#ctypes._CData.from_param" >}}) class method for them to be able to use them in the [`argtypes`]({{< ref "/library/allos/ctypes#ctypes._CFuncPtr.argtypes" >}}) sequence. The [`from_param()`]({{< ref "/library/allos/ctypes#ctypes._CData.from_param" >}}) class method receives the Python object passed to the function call, it should do a typecheck or whatever is needed to make sure this object is acceptable, and then return the object itself, its `_as_parameter_` attribute, or whatever you want to pass as the C function argument in this case. Again, the result should be an integer, string, bytes, a [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) instance, or an object with an `_as_parameter_` attribute.



### 返回类型

​	By default functions are assumed to return the C int type. Other return types can be specified by setting the [`restype`]({{< ref "/library/allos/ctypes#ctypes._CFuncPtr.restype" >}}) attribute of the function object.

`time()` 的 C 原型是 `time_t time(time_t *)`。 由于 `time_t` 的类型可能不同于默认返回类型 int，你应当指定 `restype` 属性:



``` python
>>> libc.time.restype = c_time_t
```

​	The argument types can be specified using [`argtypes`]({{< ref "/library/allos/ctypes#ctypes._CFuncPtr.argtypes" >}}):



``` python
>>> libc.time.argtypes = (POINTER(c_time_t),)
```

​	调用该函数时如果要将 `NULL` 指针作为第一个参数，请使用 `None`:



``` python
>>> print(libc.time(None))  
1150640792
```

​	下面是一个更高级的示例，它使用了 `strchr()` 函数，该函数接收一个字符串指针和一个字符，并返回一个字符串指针:



``` python
>>> strchr = libc.strchr
>>> strchr(b"abcdef", ord("d"))  
8059983
>>> strchr.restype = c_char_p    # c_char_p is a pointer to a string
>>> strchr(b"abcdef", ord("d"))
b'def'
>>> print(strchr(b"abcdef", ord("x")))
None
>>>
```

​	If you want to avoid the [`ord("x")`]({{< ref "/library/functions#ord" >}}) calls above, you can set the [`argtypes`]({{< ref "/library/allos/ctypes#ctypes._CFuncPtr.argtypes" >}}) attribute, and the second argument will be converted from a single character Python bytes object into a C char:



``` python
>>> strchr.restype = c_char_p
>>> strchr.argtypes = [c_char_p, c_char]
>>> strchr(b"abcdef", b"d")
b'def'
>>> strchr(b"abcdef", b"def")
Traceback (most recent call last):
ctypes.ArgumentError: argument 2: TypeError: one character bytes, bytearray or integer expected
>>> print(strchr(b"abcdef", b"x"))
None
>>> strchr(b"abcdef", b"d")
b'def'
>>>
```

​	You can also use a callable Python object (a function or a class for example) as the [`restype`]({{< ref "/library/allos/ctypes#ctypes._CFuncPtr.restype" >}}) attribute, if the foreign function returns an integer. The callable will be called with the *integer* the C function returns, and the result of this call will be used as the result of your function call. This is useful to check for error return values and automatically raise an exception:



``` python
>>> GetModuleHandle = windll.kernel32.GetModuleHandleA  
>>> def ValidHandle(value):
...     if value == 0:
...         raise WinError()
...     return value
...
>>>
>>> GetModuleHandle.restype = ValidHandle  
>>> GetModuleHandle(None)  
486539264
>>> GetModuleHandle("something silly")  
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 3, in ValidHandle
OSError: [Errno 126] The specified module could not be found.
>>>
```

`WinError` 函数可以调用 Windows 的 `FormatMessage()` API 获取错误码的字符串说明，然后 *返回* 一个异常。 `WinError` 接收一个可选的错误码作为参数，如果没有的话，它将调用 [`GetLastError()`]({{< ref "/library/allos/ctypes#ctypes.GetLastError" >}}) 获取错误码。

​	Please note that a much more powerful error checking mechanism is available through the [`errcheck`]({{< ref "/library/allos/ctypes#ctypes._CFuncPtr.errcheck" >}}) attribute; see the reference manual for details.



### 传递指针（或以引用方式传递形参）

​	有时候 C 函数接口可能由于要往某个地址写入值，或者数据太大不适合作为值传递，从而希望接收一个 *指针* 作为数据参数类型。这和 *传递参数引用* 类似。

[`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 暴露了 [`byref()`]({{< ref "/library/allos/ctypes#ctypes.byref" >}}) 函数用于通过引用传递参数，使用 [`pointer()`]({{< ref "/library/allos/ctypes#ctypes.pointer" >}}) 函数也能达到同样的效果，只不过 [`pointer()`]({{< ref "/library/allos/ctypes#ctypes.pointer" >}}) 需要更多步骤，因为它要先构造一个真实指针对象。所以在 Python 代码本身不需要使用这个指针对象的情况下，使用 [`byref()`]({{< ref "/library/allos/ctypes#ctypes.byref" >}}) 效率更高。



``` python
>>> i = c_int()
>>> f = c_float()
>>> s = create_string_buffer(b'\000' * 32)
>>> print(i.value, f.value, repr(s.value))
0 0.0 b''
>>> libc.sscanf(b"1 3.14 Hello", b"%d %f %s",
...             byref(i), byref(f), s)
3
>>> print(i.value, f.value, repr(s.value))
1 3.1400001049 b'Hello'
>>>
```



### 结构体和联合

​	结构体和联合必须派生自 [`Structure`]({{< ref "/library/allos/ctypes#ctypes.Structure" >}}) 和 [`Union`]({{< ref "/library/allos/ctypes#ctypes.Union" >}}) 基类，这两个基类是在 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 模块中定义的。 每个子类都必须定义 [`_fields_`]({{< ref "/library/allos/ctypes#ctypes.Structure._fields_" >}}) 属性。 `_fields_` 必须是一个 *2元组* 的列表，其中包含一个 *字段名称* 和一个 *字段类型*。

​	type 字段必须是一个 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 类型，比如 [`c_int`]({{< ref "/library/allos/ctypes#ctypes.c_int" >}})，或者其他 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 类型: 结构体、联合、数组、指针。

​	这是一个简单的 POINT 结构体，它包含名称为 *x* 和 *y* 的两个变量，还展示了如何通过构造函数初始化结构体。



``` python
>>> from ctypes import *
>>> class POINT(Structure):
...     _fields_ = [("x", c_int),
...                 ("y", c_int)]
...
>>> point = POINT(10, 20)
>>> print(point.x, point.y)
10 20
>>> point = POINT(y=5)
>>> print(point.x, point.y)
0 5
>>> POINT(1, 2, 3)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: too many initializers
>>>
```

​	当然，你可以构造更复杂的结构体。一个结构体可以通过设置 type 字段包含其他结构体或者自身。

​	这是以一个 RECT 结构体，他包含了两个 POINT ，分别叫 *upperleft* 和 *lowerright*:



``` python
>>> class RECT(Structure):
...     _fields_ = [("upperleft", POINT),
...                 ("lowerright", POINT)]
...
>>> rc = RECT(point)
>>> print(rc.upperleft.x, rc.upperleft.y)
0 5
>>> print(rc.lowerright.x, rc.lowerright.y)
0 0
>>>
```

​	嵌套结构体可以通过几种方式构造初始化:



``` python
>>> r = RECT(POINT(1, 2), POINT(3, 4))
>>> r = RECT((1, 2), (3, 4))
```

​	可以通过 *类* 获取字段 [descriptor]({{< ref "/glossary/idx#term-descriptor" >}}) ，它能提供很多有用的调试信息。



``` python
>>> print(POINT.x)
<Field type=c_long, ofs=0, size=4>
>>> print(POINT.y)
<Field type=c_long, ofs=4, size=4>
>>>
```

​	警告

 

[`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 不支持带位域的结构体、联合以值的方式传给函数。这可能在 32 位 x86 平台上可以正常工作，但是对于一般情况，这种行为是未定义的。带位域的结构体、联合应该总是通过指针传递给函数。

### 结构体/联合字段对齐及字节顺序

​	在默认情况下，Structure 和 Union 字段使用与 C 编译器一样的方式进行对齐。 可以通过在子类定义中指定 [`_pack_`]({{< ref "/library/allos/ctypes#ctypes.Structure._pack_" >}}) 类属性来覆盖此行为。 该属性必须设为一个正整数来指明字段对齐的最大值。 这也是 `#pragma pack(n)` 在 MSVC 所做的事情。 还可以使用与 `#pragma align(n)` 在 MSVC 中一样的方式来设置子类本身数据打包对齐的最小值。 这可以通过在子类定义中指定 :[`_align_`]({{< ref "/library/allos/ctypes#ctypes.Structure._align_" >}}) 类属性来实现。

[`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 中的结构体和联合使用的是本地字节序。要使用非本地字节序，可以使用 [`BigEndianStructure`]({{< ref "/library/allos/ctypes#ctypes.BigEndianStructure" >}}), [`LittleEndianStructure`]({{< ref "/library/allos/ctypes#ctypes.LittleEndianStructure" >}}), [`BigEndianUnion`]({{< ref "/library/allos/ctypes#ctypes.BigEndianUnion" >}}), and [`LittleEndianUnion`]({{< ref "/library/allos/ctypes#ctypes.LittleEndianUnion" >}}) 作为基类。这些类不能包含指针字段。



### 结构体和联合中的位域

​	可以创建包含位字段的结构体和联合。 位字段只适用于整数字段，位宽度是由 [`_fields_`]({{< ref "/library/allos/ctypes#ctypes.Structure._fields_" >}}) 元组中的第三项来指定的:



``` python
>>> class Int(Structure):
...     _fields_ = [("first_16", c_int, 16),
...                 ("second_16", c_int, 16)]
...
>>> print(Int.first_16)
<Field type=c_long, ofs=0:0, bits=16>
>>> print(Int.second_16)
<Field type=c_long, ofs=0:16, bits=16>
>>>
```



### 数组

​	数组是一个序列，包含指定个数元素，且必须类型相同。

​	创建数组类型的推荐方式是使用一个类型乘以一个正数:

```
TenPointsArrayType = POINT * 10
```

​	下面是一个构造的数据案例，结构体中包含了4个 POINT 和一些其他东西。



``` python
>>> from ctypes import *
>>> class POINT(Structure):
...     _fields_ = ("x", c_int), ("y", c_int)
...
>>> class MyStruct(Structure):
...     _fields_ = [("a", c_int),
...                 ("b", c_float),
...                 ("point_array", POINT * 4)]
>>>
>>> print(len(MyStruct().point_array))
4
>>>
```

​	和平常一样，通过调用它创建实例:

```
arr = TenPointsArrayType()
for pt in arr:
    print(pt.x, pt.y)
```

​	以上代码会打印几行 `0 0` ，因为数组内容被初始化为 0.

​	也能通过指定正确类型的数据来初始化:



``` python
>>> from ctypes import *
>>> TenIntegers = c_int * 10
>>> ii = TenIntegers(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
>>> print(ii)
<c_long_Array_10 object at 0x...>
>>> for i in ii: print(i, end=" ")
...
1 2 3 4 5 6 7 8 9 10
>>>
```



### 指针

​	可以将 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 类型数据传入 [`pointer()`]({{< ref "/library/allos/ctypes#ctypes.pointer" >}}) 函数创建指针:



``` python
>>> from ctypes import *
>>> i = c_int(42)
>>> pi = pointer(i)
>>>
```

​	指针实例拥有 [`contents`]({{< ref "/library/allos/ctypes#ctypes._Pointer.contents" >}}) 属性，它返回指针指向的真实对象，如上面的 `i` 对象:



``` python
>>> pi.contents
c_long(42)
>>>
```

​	注意 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 并没有 OOR （返回原始对象）, 每次访问这个属性时都会构造返回一个新的相同对象:



``` python
>>> pi.contents is i
False
>>> pi.contents is pi.contents
False
>>>
```

​	将这个指针的 contents 属性赋值为另一个 [`c_int`]({{< ref "/library/allos/ctypes#ctypes.c_int" >}}) 实例将会导致该指针指向该实例的内存地址:



``` python
>>> i = c_int(99)
>>> pi.contents = i
>>> pi.contents
c_long(99)
>>>
```

​	指针对象也可以通过整数下标进行访问:



``` python
>>> pi[0]
99
>>>
```

​	通过整数下标赋值可以改变指针所指向的真实内容:



``` python
>>> print(i)
c_long(99)
>>> pi[0] = 22
>>> print(i)
c_long(22)
>>>
```

​	使用 0 以外的索引也是合法的，但是你必须确保知道自己为什么这么做，就像 C 语言中: 你可以访问或者修改任意内存内容。 通常只会在函数接收指针是才会使用这种特性，而且你 *知道* 这个指针指向的是一个数组而不是单个值。

​	内部细节, [`pointer()`]({{< ref "/library/allos/ctypes#ctypes.pointer" >}}) 函数不只是创建了一个指针实例，它首先创建了一个指针 *类型* 。这是通过调用 [`POINTER()`]({{< ref "/library/allos/ctypes#ctypes.POINTER" >}}) 函数实现的，它接收 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 类型为参数，返回一个新的类型:



``` python
>>> PI = POINTER(c_int)
>>> PI
<class 'ctypes.LP_c_long'>
>>> PI(42)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: expected c_long instead of int
>>> PI(c_int(42))
<ctypes.LP_c_long object at 0x...>
>>>
```

​	无参调用指针类型可以创建一个 `NULL` 指针。 `NULL` 指针的布尔值是 `False`



``` python
>>> null_ptr = POINTER(c_int)()
>>> print(bool(null_ptr))
False
>>>
```

​	解引用指针的时候， [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 会帮你检测是否指针为 `NULL` (但是解引用无效的 非 `NULL` 指针仍会导致 Python 崩溃):



``` python
>>> null_ptr[0]
Traceback (most recent call last):
    ....
ValueError: NULL pointer access
>>>

>>> null_ptr[0] = 1234
Traceback (most recent call last):
    ....
ValueError: NULL pointer access
>>>
```



### 类型转换

​	Usually, ctypes does strict type checking. This means, if you have `POINTER(c_int)` in the [`argtypes`]({{< ref "/library/allos/ctypes#ctypes._CFuncPtr.argtypes" >}}) list of a function or as the type of a member field in a structure definition, only instances of exactly the same type are accepted. There are some exceptions to this rule, where ctypes accepts other objects. For example, you can pass compatible array instances instead of pointer types. So, for `POINTER(c_int)`, ctypes accepts an array of c_int:



``` python
>>> class Bar(Structure):
...     _fields_ = [("count", c_int), ("values", POINTER(c_int))]
...
>>> bar = Bar()
>>> bar.values = (c_int * 3)(1, 2, 3)
>>> bar.count = 3
>>> for i in range(bar.count):
...     print(bar.values[i])
...
1
2
3
>>>
```

​	In addition, if a function argument is explicitly declared to be a pointer type (such as `POINTER(c_int)`) in [`argtypes`]({{< ref "/library/allos/ctypes#ctypes._CFuncPtr.argtypes" >}}), an object of the pointed type (`c_int` in this case) can be passed to the function. ctypes will apply the required [`byref()`]({{< ref "/library/allos/ctypes#ctypes.byref" >}}) conversion in this case automatically.

​	可以给指针内容赋值为 None 将其设置为 `Null`



``` python
>>> bar.values = None
>>>
```

​	有时候你拥有一个不兼容的类型。 在 C 中，你可以将一个类型强制转换为另一个。 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 中的 a [`cast()`]({{< ref "/library/allos/ctypes#ctypes.cast" >}}) 函数提供了相同的功能。 上面的结构体 `Bar` 的 `value` 字段接收 `POINTER(c_int)` 指针或者 [`c_int`]({{< ref "/library/allos/ctypes#ctypes.c_int" >}}) 数组，但是不能接受其他类型的实例:



``` python
>>> bar.values = (c_byte * 4)()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: incompatible types, c_byte_Array_4 instance instead of LP_c_long instance
>>>
```

​	这种情况下, 需要手动使用 [`cast()`]({{< ref "/library/allos/ctypes#ctypes.cast" >}}) 函数。

[`cast()`]({{< ref "/library/allos/ctypes#ctypes.cast" >}}) 函数可以将一个指针实例强制转换为另一种 ctypes 类型。 [`cast()`]({{< ref "/library/allos/ctypes#ctypes.cast" >}}) 接收两个参数，一个 ctypes 指针对象或者可以被转换为指针的其他类型对象，和一个 ctypes 指针类型。 返回第二个类型的一个实例，该返回实例和第一个参数指向同一片内存空间:



``` python
>>> a = (c_byte * 4)()
>>> cast(a, POINTER(c_int))
<ctypes.LP_c_long object at ...>
>>>
```

​	所以 [`cast()`]({{< ref "/library/allos/ctypes#ctypes.cast" >}}) 可以用来给结构体 `Bar` 的 `values` 字段赋值:



``` python
>>> bar = Bar()
>>> bar.values = cast((c_byte * 4)(), POINTER(c_int))
>>> print(bar.values[0])
0
>>>
```



### 不完整类型

*不完整类型* 即还没有定义成员的结构体、联合或者数组。在 C 中，它们通常用于前置声明，然后在后面定义:

```
struct cell; /* 前向声明 */

struct cell {
    char *name;
    struct cell *next;
};
```

​	直接翻译成 ctypes 的代码如下，但是这行不通:



``` python
>>> class cell(Structure):
...     _fields_ = [("name", c_char_p),
...                 ("next", POINTER(cell))]
...
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 2, in cell
NameError: name 'cell' is not defined
>>>
```

​	因为新的 `class cell` 在 class 语句本身中是不可用的。 在 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 中，我们可以定义 `cell` 类再在 class 语句之后设置 [`_fields_`]({{< ref "/library/allos/ctypes#ctypes.Structure._fields_" >}}) 属性:



``` python
>>> from ctypes import *
>>> class cell(Structure):
...     pass
...
>>> cell._fields_ = [("name", c_char_p),
...                  ("next", POINTER(cell))]
>>>
```

​	让我们试试。我们定义两个 `cell` 实例，让它们互相指向对方，然后通过指针链式访问几次:



``` python
>>> c1 = cell()
>>> c1.name = b"foo"
>>> c2 = cell()
>>> c2.name = b"bar"
>>> c1.next = pointer(c2)
>>> c2.next = pointer(c1)
>>> p = c1
>>> for i in range(8):
...     print(p.name, end=" ")
...     p = p.next[0]
...
foo bar foo bar foo bar foo bar
>>>
```



### 回调函数

[`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 允许创建一个指向 Python 可调用对象的 C 函数。它们有时候被称为 *回调函数* 。

​	首先，你必须为回调函数创建一个类，这个类知道调用约定，包括返回值类型以及函数接收的参数类型及个数。

[`CFUNCTYPE()`]({{< ref "/library/allos/ctypes#ctypes.CFUNCTYPE" >}}) 工厂函数使用 `cdecl` 调用约定创建回调函数类型。在 Windows 上， [`WINFUNCTYPE()`]({{< ref "/library/allos/ctypes#ctypes.WINFUNCTYPE" >}}) 工厂函数使用 `stdcall` 调用约定为回调函数创建类型。

​	这些工厂函数的第一个参数是返回值类型，回调函数的参数类型作为剩余参数。

​	这里展示一个使用标准 C 库的 `qsort()` 函数例子，使用它在一个回调函数的协助下对条目进行排序。 `qsort()` 将被用来给一个整数的数组排序:



``` python
>>> IntArray5 = c_int * 5
>>> ia = IntArray5(5, 1, 7, 33, 99)
>>> qsort = libc.qsort
>>> qsort.restype = None
>>>
```

`qsort()` 被调用时必须传入一个指向要排序的数据的指针、数据数组中的条目数、每条目的大小以及一个指向比较函数即回调函数的指针。 回调函数将附带两个指向条目的指针进行调用，如果第一个条目小于第二个条目则它必须返回一个负整数，如果两者相等则返回零，在其他情况下则返回一个正整数。

​	所以，我们的回调函数要接收两个整数指针，返回一个整数。首先我们创建回调函数的 `类型`



``` python
>>> CMPFUNC = CFUNCTYPE(c_int, POINTER(c_int), POINTER(c_int))
>>>
```

​	首先，这是一个简单的回调，它会显示传入的值:



``` python
>>> def py_cmp_func(a, b):
...     print("py_cmp_func", a[0], b[0])
...     return 0
...
>>> cmp_func = CMPFUNC(py_cmp_func)
>>>
```

​	结果:



``` python
>>> qsort(ia, len(ia), sizeof(c_int), cmp_func)  
py_cmp_func 5 1
py_cmp_func 33 99
py_cmp_func 7 33
py_cmp_func 5 7
py_cmp_func 1 7
>>>
```

​	现在我们可以比较两个元素并返回有用的结果了:



``` python
>>> def py_cmp_func(a, b):
...     print("py_cmp_func", a[0], b[0])
...     return a[0] - b[0]
...
>>>
>>> qsort(ia, len(ia), sizeof(c_int), CMPFUNC(py_cmp_func)) 
py_cmp_func 5 1
py_cmp_func 33 99
py_cmp_func 7 33
py_cmp_func 1 7
py_cmp_func 5 7
>>>
```

​	我们可以轻易地验证，现在数组是有序的了:



``` python
>>> for i in ia: print(i, end=" ")
...
1 5 7 33 99
>>>
```

​	这些工厂函数可以当作装饰器工厂，所以可以这样写:



``` python
>>> @CFUNCTYPE(c_int, POINTER(c_int), POINTER(c_int))
... def py_cmp_func(a, b):
...     print("py_cmp_func", a[0], b[0])
...     return a[0] - b[0]
...
>>> qsort(ia, len(ia), sizeof(c_int), py_cmp_func)
py_cmp_func 5 1
py_cmp_func 33 99
py_cmp_func 7 33
py_cmp_func 1 7
py_cmp_func 5 7
>>>
```

​	备注

 

​	请确保你维持的 [`CFUNCTYPE()`]({{< ref "/library/allos/ctypes#ctypes.CFUNCTYPE" >}}) 对象的引用周期与它们在 C 代码中的使用期一样长。 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 不会确保这一点，如果不这样做，它们可能会被垃圾回收，导致程序在执行回调函数时发生崩溃。

​	注意，如果回调函数在Python之外的另外一个线程使用(比如，外部代码调用这个回调函数)， ctypes 会在每一次调用上创建一个虚拟 Python 线程。这个行为在大多数情况下是合理的，但也意味着如果有数据使用 [`threading.local`]({{< ref "/library/concurrency/threading#threading.local" >}}) 方式存储，将无法访问，就算它们是在同一个 C 线程中调用的 。



### 访问 dll 的导出变量

​	某些共享库不仅会导出函数，还会导出变量。 一个例子就是 Python 库本身的 [`Py_Version`]({{< ref "/c_api/apiabiversion#c.Py_Version" >}})，Python 运行时版本号被编码为单个整数常量。

[`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 可以通过类型的 [`in_dll()`]({{< ref "/library/allos/ctypes#ctypes._CData.in_dll" >}}) 类方法访问这样的值。 *pythonapi* 是一个用于访问 Python C api 预定义符号:



``` python
>>> version = ctypes.c_int.in_dll(ctypes.pythonapi, "Py_Version")
>>> print(hex(version.value))
0x30c00a0
```

​	一个扩展例子, 同时也展示了使用指针访问 Python 导出的 [`PyImport_FrozenModules`](https://docs.python.org/zh-cn/3.13/c-api/import.html#c.PyImport_FrozenModules) 指针对象。

​	对文档中这个值的解释说明

> ​	该指针被初始化为指向一个 [`_frozen`](https://docs.python.org/zh-cn/3.13/c-api/import.html#c._frozen) 记录的数组，以一个所有成员均为 `NULL` 或零的记录表示结束。 当一个冻结模块被导入时，它将在此表中被搜索。 第三方代码可以利用此方式来提供动态创建的冻结模块集。

​	这足以证明修改这个指针是很有用的。为了让实例大小不至于太长，这里只展示如何使用 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 读取这个表:



``` python
>>> from ctypes import *
>>>
>>> class struct_frozen(Structure):
...     _fields_ = [("name", c_char_p),
...                 ("code", POINTER(c_ubyte)),
...                 ("size", c_int),
...                 ("get_code", POINTER(c_ubyte)),  # 函数指针
...                ]
...
>>>
```

​	我们定义了 [`_frozen`](https://docs.python.org/zh-cn/3.13/c-api/import.html#c._frozen) 数据类型，所以我们可以获取表的指针:



``` python
>>> FrozenTable = POINTER(struct_frozen)
>>> table = FrozenTable.in_dll(pythonapi, "_PyImport_FrozenBootstrap")
>>>
```

​	由于 `table` 是指向 `struct_frozen` 数组的 `指针` ，我们可以遍历它，只不过需要自己判断循环是否结束，因为指针本身并不包含长度。它早晚会因为访问到野指针或者什么的把自己搞崩溃，所以我们最好在遇到 `NULL` 后就让它退出循环:



``` python
>>> for item in table:
...     if item.name is None:
...         break
...     print(item.name.decode("ascii"), item.size)
...
_frozen_importlib 31764
_frozen_importlib_external 41499
zipimport 12345
>>>
```

​	Python 的冻结模块和冻结包(由负 `size` 成员表示)并不是广为人知的事情，它们仅仅用于实验。例如，可以使用 `import __hello__` 尝试一下这个功能。



### 意外

[`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 也有自己的边界，有时候会发生一些意想不到的事情。

​	比如下面的例子:



``` python
>>> from ctypes import *
>>> class POINT(Structure):
...     _fields_ = ("x", c_int), ("y", c_int)
...
>>> class RECT(Structure):
...     _fields_ = ("a", POINT), ("b", POINT)
...
>>> p1 = POINT(1, 2)
>>> p2 = POINT(3, 4)
>>> rc = RECT(p1, p2)
>>> print(rc.a.x, rc.a.y, rc.b.x, rc.b.y)
1 2 3 4
>>> # 现在交换这两个
>>> rc.a, rc.b = rc.b, rc.a
>>> print(rc.a.x, rc.a.y, rc.b.x, rc.b.y)
3 4 3 4
>>>
```

​	嗯。我们预想应该打印 `3 4 1 2` 。但是为什么呢? 这是 `rc.a, rc.b = rc.b, rc.a` 这行代码展开后的步骤:



``` python
>>> temp0, temp1 = rc.b, rc.a
>>> rc.a = temp0
>>> rc.b = temp1
>>>
```

​	注意 `temp0` 和 `temp1` 对象始终引用了对象 `rc` 的内容。然后执行 `rc.a = temp0` 会把 `temp0` 的内容拷贝到 `rc` 的空间。这也改变了 `temp1` 的内容。最终导致赋值语句 `rc.b = temp1` 没有产生预想的效果。

​	记住，访问被包含在结构体、联合、数组中的对象并不会将其 *复制* 出来，而是得到了一个代理对象，它是对根对象的内部内容的一层包装。

​	下面是另一个可能和预期有偏差的例子:



``` python
>>> s = c_char_p()
>>> s.value = b"abc def ghi"
>>> s.value
b'abc def ghi'
>>> s.value is s.value
False
>>>
```

​	备注

 

​	使用 [`c_char_p`]({{< ref "/library/allos/ctypes#ctypes.c_char_p" >}}) 实例化的对象只能将其值设置为 bytes 或者整数。

​	为什么这里打印了 `False` ？ ctypes 实例是一些内存块加上一些用于访问这些内存块的 [descriptor]({{< ref "/glossary/idx#term-descriptor" >}}) 组成。将 Python 对象存储在内存块并不会存储对象本身，而是存储了对象的 `内容` 。每次访问对象的内容都会构造一个新的 Python 对象。



### 变长数据类型

[`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 对变长数组和结构体提供了一些支持 。

​	The [`resize()`]({{< ref "/library/allos/ctypes#ctypes.resize" >}}) function can be used to resize the memory buffer of an existing ctypes object. The function takes the object as first argument, and the requested size in bytes as the second argument. The memory block cannot be made smaller than the natural memory block specified by the objects type, a [`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) is raised if this is tried:



``` python
>>> short_array = (c_short * 4)()
>>> print(sizeof(short_array))
8
>>> resize(short_array, 4)
Traceback (most recent call last):
    ...
ValueError: minimum size is 8
>>> resize(short_array, 32)
>>> sizeof(short_array)
32
>>> sizeof(type(short_array))
8
>>>
```

​	这非常好，但是要怎么访问数组中额外的元素呢？因为数组类型已经定义包含4个元素，导致我们访问新增元素时会产生以下错误:



``` python
>>> short_array[:]
[0, 0, 0, 0]
>>> short_array[7]
Traceback (most recent call last):
    ...
IndexError: invalid index
>>>
```

​	使用 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 访问变长数据类型的一个可行方法是利用 Python 的动态特性，根据具体情况，在知道这个数据的大小后，(重新)指定这个数据的类型。



## ctypes 参考手册



### 寻找动态链接库

​	在编译型语言中，动态链接库会在编译、链接或者程序运行时访问。

[`find_library()`]({{< ref "/library/allos/ctypes#ctypes.util.find_library" >}}) 函数的目的是以类似于编译器或运行时加载器的方式来定位库（在有多个共享库版本的平台上应当加载最新的版本），而 ctypes 库加载器的行为类似于程序已经运行时直接调用运行时加载器。

`ctypes.util` 模块提供了一个函数，可以帮助确定要加载的库。

## ctypes.util.**find_library**(*name*)

​	尝试寻找一个库然后返回其路径名， *name* 是库名称, 且去除了 *lib* 等前缀和 `.so` 、 `.dylib` 、版本号等后缀(这是 posix 连接器 `-l` 选项使用的格式)。如果没有找到对应的库，则返回 `None` 。

​	确切的功能取决于系统。

​	在 Linux 中，[`find_library()`]({{< ref "/library/allos/ctypes#ctypes.util.find_library" >}}) 会尝试运行外部程序 (`/sbin/ldconfig`, `gcc`, `objdump` 和 `ld`) 来查找库文件。 它会返回库文件的文件名。

*在 3.6 版本发生变更:* 在Linux 上，如果其他方式找不到的话，会使用环境变量 `LD_LIBRARY_PATH` 搜索动态链接库。

​	这是一些例子:



``` python
>>> from ctypes.util import find_library
>>> find_library("m")
'libm.so.6'
>>> find_library("c")
'libc.so.6'
>>> find_library("bz2")
'libbz2.so.1.0'
>>>
```

​	在 macOS 和 Android 上，[`find_library()`]({{< ref "/library/allos/ctypes#ctypes.util.find_library" >}}) 使用系统的标准命名方案和路径来定位库，并在成功时返回完整的路径名:



``` python
>>> from ctypes.util import find_library
>>> find_library("c")
'/usr/lib/libc.dylib'
>>> find_library("m")
'/usr/lib/libm.dylib'
>>> find_library("bz2")
'/usr/lib/libbz2.dylib'
>>> find_library("AGL")
'/System/Library/Frameworks/AGL.framework/AGL'
>>>
```

​	在 Windows 中，[`find_library()`]({{< ref "/library/allos/ctypes#ctypes.util.find_library" >}}) 会沿着系统搜索路径进行搜索，并返回完整的路径名称，但由于没有预定义的命名方案因此像 `find_library("c")` 这样的调用会失败并返回 `None`。

​	如果使用 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 包装一个共享库，则更好的做法 *可能* 是开发时就确定好共享库的名称，并将其硬编码到包装模块中而不是在运行时使用 [`find_library()`]({{< ref "/library/allos/ctypes#ctypes.util.find_library" >}}) 来定位库。



### 加载动态链接库

​	有很多方式可以将动态链接库加载到 Python 进程。其中之一是实例化以下类的其中一个:

## *class* ctypes.**CDLL**(*name*, *mode=DEFAULT_MODE*, *handle=None*, *use_errno=False*, *use_last_error=False*, *winmode=None*)

​	该类的实例代表已加载的共享库。 这些库中的函数使用标准的 C 调用约定，并被预期会返回 int。

​	在 Windows 上创建 [`CDLL`]({{< ref "/library/allos/ctypes#ctypes.CDLL" >}}) 实例可能会失败，即使 DLL 名称确实存在。 当某个被加载 DLL 所依赖的 DLL 未找到时，将引发 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 错误并附带消息 *"[WinError 126] The specified module could not be found".* 此错误消息不包含缺失 DLL 的名称，因为 Windows API 并不会返回此类信息，这使得此错误难以诊断。 要解决此错误并确定是哪一个 DLL 未找到，你需要找出所依赖的 DLL 列表并使用 Windows 调试与跟踪工具确定是哪一个未找到。

*在 3.12 版本发生变更:* 现在 *name* 形参可以是一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

​	参见

 

[Microsoft DUMPBIN 工具](https://docs.microsoft.com/cpp/build/reference/dependents) -- 一个用于查找 DLL 依赖的工具。

## *class* ctypes.**OleDLL**(*name*, *mode=DEFAULT_MODE*, *handle=None*, *use_errno=False*, *use_last_error=False*, *winmode=None*)

​	仅 Windows : 此类的实例即加载好的动态链接库，其中的函数使用 `stdcall` 调用约定，并且假定返回 windows 指定的 [`HRESULT`]({{< ref "/library/allos/ctypes#ctypes.HRESULT" >}}) 返回码。 [`HRESULT`]({{< ref "/library/allos/ctypes#ctypes.HRESULT" >}}) 的值包含的信息说明函数调用成功还是失败，以及额外错误码。 如果返回值表示失败，会自动抛出 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 异常。

*在 3.3 版本发生变更:* 过去会引发 [`WindowsError`]({{< ref "/library/exceptions#WindowsError" >}})，现在它是 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 的别名。

*在 3.12 版本发生变更:* 现在 *name* 形参可以是一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

## *class* ctypes.**WinDLL**(*name*, *mode=DEFAULT_MODE*, *handle=None*, *use_errno=False*, *use_last_error=False*, *winmode=None*)

​	仅限 Windows：该类的实例代表已加载的共享库，这些库中的函数使用 `stdcall` 调用约定，并被预期默认会返回 int。

*在 3.12 版本发生变更:* 现在 *name* 形参可以是一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

​	调用动态库导出的函数之前，Python会释放 [global interpreter lock]({{< ref "/glossary/idx#term-global-interpreter-lock" >}}) ，并在调用后重新获取。

## *class* ctypes.**PyDLL**(*name*, *mode=DEFAULT_MODE*, *handle=None*)

​	这个类实例的行为与 [`CDLL`]({{< ref "/library/allos/ctypes#ctypes.CDLL" >}}) 类似，只不过 *不会* 在调用函数的时候释放 GIL 锁，且调用结束后会检查 Python 错误码。 如果错误码被设置，会抛出一个 Python 异常。

​	所以，它只在直接调用 Python C 接口函数的时候有用。

*在 3.12 版本发生变更:* 现在 *name* 形参可以是一个 [path-like object]({{< ref "/glossary/idx#term-path-like-object" >}})。

​	所有这些类均可通过附带至少一个参数即共享库的路径名来调用它们进行实例化。 如果你有一个对应已加载共享库的现有句柄，可以将其作为 `handle` 具名形参传入，否则会使用下层平台的 `dlopen()` 或 `LoadLibrary()` 函数将库加载到进程中，并获取对应的句柄。

*mode* 可以指定库加载方式。详情请参见 *[dlopen(3)](https://manpages.debian.org/dlopen(3))* 手册页。 在 Windows 上， 会忽略 *mode* ，在 posix 系统上， 总是会加上 RTLD_NOW ，且无法配置。

​	当 *use_errno* 形参被设为真值时，将启用以安全方式访问系统 [`errno`]({{< ref "/library/allos/errno#module-errno" >}}) 错误号的 ctypes 机制。 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 将维护一份系统 [`errno`]({{< ref "/library/allos/errno#module-errno" >}}) 变量的线程局部副本；如果你调用设置了 `use_errno=True` 的外部函数那么 [`errno`]({{< ref "/library/allos/errno#module-errno" >}}) 将在函数调用之前与 ctypes 私有副本互换，同样的情况也会在函数调用之后立即发生。

​	The function [`ctypes.get_errno()`]({{< ref "/library/allos/ctypes#ctypes.get_errno" >}}) returns the value of the ctypes private copy, and the function [`ctypes.set_errno()`]({{< ref "/library/allos/ctypes#ctypes.set_errno" >}}) changes the ctypes private copy to a new value and returns the former value.

​	当 *use_last_error* 形参设为真值时，为 Windows 错误代码也启用与由 [`GetLastError()`]({{< ref "/library/allos/ctypes#ctypes.GetLastError" >}}) 和 `SetLastError()` Windows API 函数管理相同的机制；[`ctypes.get_last_error()`]({{< ref "/library/allos/ctypes#ctypes.get_last_error" >}}) 和 [`ctypes.set_last_error()`]({{< ref "/library/allos/ctypes#ctypes.set_last_error" >}}) 会被用于请求和更改 Windows 错误代码的 ctypes 私有副本。

*winmode* 形参用于在 Windows 上指定库的加载方式（因为 *mode* 会被忽略）。 它接受任何对 Win32 API `LoadLibraryEx` 旗标形参来说合法的值。 当被省略时，默认使用表示最安全的 DLL 加载的旗标，这将避免 DLL 劫持等问题。 传入 DLL 的完整路径是确保正确加载库及其依赖的最安全的方式。

*在 3.8 版本发生变更:* 增加了 *winmode* 参数。

## ctypes.**RTLD_GLOBAL**

​	用于 *mode* 参数的标识值。在此标识不可用的系统上，它被定义为整数0。

## ctypes.**RTLD_LOCAL**

​	Flag to use as *mode* parameter. On platforms where this is not available, it is the same as *RTLD_GLOBAL*.

## ctypes.**DEFAULT_MODE**

​	加载动态链接库的默认模式。在 OSX 10.3 上，它是 *RTLD_GLOBAL* ，其余系统上是 *RTLD_LOCAL* 。

​	这些类的实例没有共用方法。动态链接库的导出函数可以通过属性或者索引的方式访问。注意，通过属性的方式访问会缓存这个函数，因而每次访问它时返回的都是同一个对象。另一方面，通过索引访问，每次都会返回一个新的对象:



``` python
>>> from ctypes import CDLL
>>> libc = CDLL("libc.so.6")  # 在 Linux
>>> libc.time == libc.time
True
>>> libc['time'] == libc['time']
False
```

​	还有下面这些属性可用，他们的名称以下划线开头，以避免和导出函数重名:

## PyDLL.**_handle**

​	用于访问库的系统句柄。

## PyDLL.**_name**

​	传入构造函数的库名称。

​	共享库也可以通过使用一个预制对象来加载，这种对象是 [`LibraryLoader`]({{< ref "/library/allos/ctypes#ctypes.LibraryLoader" >}}) 类的实例，具体做法是调用 [`LoadLibrary()`]({{< ref "/library/allos/ctypes#ctypes.LibraryLoader.LoadLibrary" >}}) 方法，或是将库作为加载器实例的属性来提取。

## *class* ctypes.**LibraryLoader**(*dlltype*)

​	加载共享库的类。 *dlltype* 应当为 [`CDLL`]({{< ref "/library/allos/ctypes#ctypes.CDLL" >}}), [`PyDLL`]({{< ref "/library/allos/ctypes#ctypes.PyDLL" >}}), [`WinDLL`]({{< ref "/library/allos/ctypes#ctypes.WinDLL" >}}) 或 [`OleDLL`]({{< ref "/library/allos/ctypes#ctypes.OleDLL" >}}) 类型之一。

`__getattr__()` 具有特殊的行为：它允许通过一个作为库加载器实例的属性访问共享库来加载它。 访问结果会被缓存，因此每次重复的属性访问都会返回相同的库。

## **LoadLibrary**(*name*)

​	加载一个共享库到进程中并将其返回。 此方法总是返回一个新的库实例。

​	可用的预制库加载器有如下这些:

## ctypes.**cdll**

​	创建 [`CDLL`]({{< ref "/library/allos/ctypes#ctypes.CDLL" >}}) 实例。

## ctypes.**windll**

​	仅限 Windows：创建 [`WinDLL`]({{< ref "/library/allos/ctypes#ctypes.WinDLL" >}}) 实例.

## ctypes.**oledll**

​	仅限 Windows：创建 [`OleDLL`]({{< ref "/library/allos/ctypes#ctypes.OleDLL" >}}) 实例。

## ctypes.**pydll**

​	创建 [`PyDLL`]({{< ref "/library/allos/ctypes#ctypes.PyDLL" >}}) 实例。

​	要直接访问 C Python api，可以使用一个现成的 Python 共享库对象:

## ctypes.**pythonapi**

​	一个将 Python C API 函数作为属性公开出来的 [`PyDLL`]({{< ref "/library/allos/ctypes#ctypes.PyDLL" >}}) 实例。 请注意所有这些函数都应返回 C int，当然也并非总是如此，因此您必须分配正确的 `restype` 属性才能使用这些函数。

​	通过这些对象中的任何一个加载库都将引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `ctypes.dlopen` 并附带字符串参数 `name`，即用于加载库的名称。

​	在加载的库上访问一个函数将引发一个审计事件 `ctypes.dlsym` 并附带参数 `library` (库对象) 和 `name` (以字符串或整数表示的符号名称).

​	在只有库句柄而非对象可用的情况下，访问函数会引发一个审计事件 `ctypes.dlsym/handle` 并附带参数 `handle` (原始库句柄) 和 `name`。



### 外部函数

​	As explained in the previous section, foreign functions can be accessed as attributes of loaded shared libraries. The function objects created in this way by default accept any number of arguments, accept any ctypes data instances as arguments, and return the default result type specified by the library loader.

​	They are instances of a private local class `_FuncPtr` (not exposed in `ctypes`) which inherits from the private [`_CFuncPtr`]({{< ref "/library/allos/ctypes#ctypes._CFuncPtr" >}}) class:



``` python
>>> import ctypes
>>> lib = ctypes.CDLL(None)
>>> issubclass(lib._FuncPtr, ctypes._CFuncPtr)
True
>>> lib._FuncPtr is ctypes._CFuncPtr
False
```

## *class* ctypes.**_CFuncPtr**

​	C 可调用外部函数的基类。

​	外部函数的实例也是兼容 C 的数据类型；它们代表 C 函数指针。

​	此行为可通过对外部函数对象的特殊属性赋值来自定义。

## **restype**

​	分配一个 ctypes 类型来指定外部函数的结果类型。 使用 `None` 来表示 void，即不返回任何结果的函数。

​	赋值为一个非 ctypes 类型的可调用 Python 对象也是可以的，在这种情况下函数应返回 C int，并且该可调用对象将附带此整数被调用，以允许进一步的处理或错误检查。 这种用法已被弃用，为了更灵活地进行后续处理或错误检查请使用 ctypes 数据类型作为 `restype` 并将 [`errcheck`]({{< ref "/library/allos/ctypes#ctypes._CFuncPtr.errcheck" >}}) 属性赋值为一个可调用对象。

## **argtypes**

​	赋值为一个 ctypes 类型的元组来指定函数所接受的参数类型。 使用 `stdcall` 调用规范的函数只能附带与此元组长度相同数量的参数进行调用；使用 C 调用规范的函数还可接受额外的未指明参数。

​	当调用外部函数时，每个实际参数都会被传给 [`argtypes`]({{< ref "/library/allos/ctypes#ctypes._CFuncPtr.argtypes" >}}) 元组中条目的 [`from_param()`]({{< ref "/library/allos/ctypes#ctypes._CData.from_param" >}}) 类方法，该方法允许将实际参数适配为此外部函数所接受的对象。 例如，[`argtypes`]({{< ref "/library/allos/ctypes#ctypes._CFuncPtr.argtypes" >}}) 元组中的 [`c_char_p`]({{< ref "/library/allos/ctypes#ctypes.c_char_p" >}}) 条目将使用 ctypes 转换规则把作为参数传入的字符串转换为字节串对象。

​	新特性：现在可以在 argtypes 中放入非 ctypes 类型的条目，但每个条目必须具有 [`from_param()`]({{< ref "/library/allos/ctypes#ctypes._CData.from_param" >}}) 方法用于返回一个可作为参数的值（整数、字符串、ctypes 实例）。 这样就允许定义可将将自定义对象适配为函数参数的适配器。

## **errcheck**

​	将一个 Python 函数或其他可调用对象赋值给此属性。 该可调用对象将附带三个及以上的参数被调用。

## **callable**(*result*, *func*, *arguments*)

*result* 是外部函数返回的结果，由 `restype` 属性指明。

*func* 是外部函数对象本身，这样就允许重新使用相同的可调用对象来对多个函数进行检查或后续处理。

*arguments* 是一个包含最初传递给函数调用的形参的元组，这样就允许对所用参数的行为进行特别处理。

​	此函数所返回的对象将会由外部函数调用返回，但它还可以在外部函数调用失败时检查结果并引发异常。

## *exception* ctypes.**ArgumentError**

​	此异常会在外部函数无法对某个传入参数执行转换时被引发。

​	在 Windows 上，当外部函数调用引发一个系统异常时（例如由于访问冲突），它将被捕获并被替换为适当的 Python 异常。 此外，还将引发一个审计事件 `ctypes.set_exception` 并附带参数 `code`，以允许审计钩子将原异常替换为它自己的异常。

​	某些发起外部函数调用的方式可能会引发一个审计事件 `ctypes.call_function` 并附带参数 `function pointer` 和 `arguments`。



### 函数原型

​	外部函数也可通过实例化函数原型来创建。 函数原型类似于 C 中的函数原型；它们在不定义具体实现的情况下描述了一个函数（返回类型、参数类型、调用约定）。 工厂函数必须使用函数所需要的结果类型和参数类型来调用，并可被用作装饰器工厂函数，在此情况下可以通过 `@wrapper` 语法应用于函数。 请参阅 [回调函数]({{< ref "/library/allos/ctypes#ctypes-callback-functions" >}}) 了解有关示例。

## ctypes.**CFUNCTYPE**(*restype*, **argtypes*, *use_errno=False*, *use_last_error=False*)

​	返回的函数原型会创建使用标准 C 调用约定的函数。 该函数在调用过程中将释放 GIL。 如果 *use_errno* 设为真值，则在调用之前和之后系统 [`errno`]({{< ref "/library/allos/errno#module-errno" >}}) 变量的 ctypes 私有副本会与真正的 [`errno`]({{< ref "/library/allos/errno#module-errno" >}}) 值进行交换；*use_last_error* 会为 Windows 错误码执行同样的操作。

## ctypes.**WINFUNCTYPE**(*restype*, **argtypes*, *use_errno=False*, *use_last_error=False*)

​	仅限 Windows：返回的函数原型会创建使用 `stdcall` 调用约定的函数。 该函数在调用过程中将会释放 GIL。 *use_errno* 和 *use_last_error* 具有与上文中相同的含义。

## ctypes.**PYFUNCTYPE**(*restype*, **argtypes*)

​	返回的函数原型会创建使用 Python 调用约定的函数。 该函数在调用过程中将 *不会* 释放 GIL。

​	这些工厂函数所创建的函数原型可通过不同的方式来实例化，具体取决于调用中的类型与数量:

## **prototype**(*address*)

​	在指定地址上返回一个外部函数，地址值必须为整数。

## **prototype**(*callable*)

​	基于 Python *callable* 创建一个 C 可调用函数（回调函数）。

## **prototype**(*func_spec*[, *paramflags*])

​	返回由一个共享库导出的外部函数。 *func_spec* 必须为一个 2 元组 `(name_or_ordinal, library)`。 第一项是字符串形式的所导出函数名称，或小整数形式的所导出函数序号。 第二项是该共享库实例。

## **prototype**(*vtbl_index*, *name*[, *paramflags*[, *iid*]])

​	返回将调用一个 COM 方法的外部函数。 *vtbl_index* 虚拟函数表中的索引。 *name* 是 COM 方法的名称。 *iid* 是可选的指向接口标识符的指针，它被用于扩展的错误报告。

​	COM 方法使用特殊的调用约定：除了在 `argtypes` 元组中指定的形参，它们还要求一个指向 COM 接口的指针作为第一个参数。

​	可选的 *paramflags* 形参会创建相比上述特性具有更多功能的外部函数包装器。

*paramflags* must be a tuple of the same length as [`argtypes`]({{< ref "/library/allos/ctypes#ctypes._CFuncPtr.argtypes" >}}).

​	此元组中的每一项都包含有关形参的更多信息，它必须为包含一个、两个或更多条目的元组。

​	第一项是包含形参指令旗标组合的整数。

> ## 1
>
> ​	指定函数的一个输入形参。
>
> ## 2
>
> ​	输出形参。 外部函数会填入一个值。
>
> ## 4
>
> ​	默认为整数零值的输入形参。

​	可选的第二项是字符串形式的形参名称。 如果指定此项，则可以使用该形参名称来调用外部函数。

​	可选的第三项是该形参的默认值。

​	下面的例子演示了如何包装 Windows 的 `MessageBoxW` 函数以使其支持默认形参和命名参数。 相应的 Windows 头文件的 C 声明是这样的:

```
WINUSERAPI int WINAPI
MessageBoxW(
    HWND hWnd,
    LPCWSTR lpText,
    LPCWSTR lpCaption,
    UINT uType);
```

​	这是使用 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 的包装:



``` python
>>> from ctypes import c_int, WINFUNCTYPE, windll
>>> from ctypes.wintypes import HWND, LPCWSTR, UINT
>>> prototype = WINFUNCTYPE(c_int, HWND, LPCWSTR, LPCWSTR, UINT)
>>> paramflags = (1, "hwnd", 0), (1, "text", "Hi"), (1, "caption", "Hello from ctypes"), (1, "flags", 0)
>>> MessageBox = prototype(("MessageBoxW", windll.user32), paramflags)
```

​	现在 `MessageBox` 外部函数可以通过以下方式来调用:



``` python
>>> MessageBox()
>>> MessageBox(text="Spam, spam, spam")
>>> MessageBox(flags=2, text="foo bar")
```

​	第二个例子演示了输出形参。 这个 win32 `GetWindowRect` 函数通过将指定窗口的维度拷贝至调用者必须提供的 `RECT` 结构体来提取这些值。 这是相应的 C 声明:

```
WINUSERAPI BOOL WINAPI
GetWindowRect(
     HWND hWnd,
     LPRECT lpRect);
```

​	这是使用 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 的包装:



``` python
>>> from ctypes import POINTER, WINFUNCTYPE, windll, WinError
>>> from ctypes.wintypes import BOOL, HWND, RECT
>>> prototype = WINFUNCTYPE(BOOL, HWND, POINTER(RECT))
>>> paramflags = (1, "hwnd"), (2, "lprect")
>>> GetWindowRect = prototype(("GetWindowRect", windll.user32), paramflags)
>>>
```

​	带有输出形参的函数如果输出形参存在单一值则会自动返回该值，或是当输出形参存在多个值时返回包含这些值的元组，因此当 GetWindowRect 被调用时现在将返回一个 RECT 实例。

​	Output parameters can be combined with the [`errcheck`]({{< ref "/library/allos/ctypes#ctypes._CFuncPtr.errcheck" >}}) protocol to do further output processing and error checking. The win32 `GetWindowRect` api function returns a `BOOL` to signal success or failure, so this function could do the error checking, and raises an exception when the api call failed:



``` python
>>> def errcheck(result, func, args):
...     if not result:
...         raise WinError()
...     return args
...
>>> GetWindowRect.errcheck = errcheck
>>>
```

​	If the [`errcheck`]({{< ref "/library/allos/ctypes#ctypes._CFuncPtr.errcheck" >}}) function returns the argument tuple it receives unchanged, [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) continues the normal processing it does on the output parameters. If you want to return a tuple of window coordinates instead of a `RECT` instance, you can retrieve the fields in the function and return them instead, the normal processing will no longer take place:



``` python
>>> def errcheck(result, func, args):
...     if not result:
...         raise WinError()
...     rc = args[1]
...     return rc.left, rc.top, rc.bottom, rc.right
...
>>> GetWindowRect.errcheck = errcheck
>>>
```



### 工具函数

## ctypes.**addressof**(*obj*)

​	以整数形式返回内存缓冲区地址。 *obj* 必须为一个 ctypes 类型的实例。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `ctypes.addressof` 并附带参数 `obj`。

## ctypes.**alignment**(*obj_or_type*)

​	返回一个 ctypes 类型的对齐要求。 *obj_or_type* 必须为一个 ctypes 类型或实例。

## ctypes.**byref**(*obj*[, *offset*])

​	返回指向 *obj* 的轻量指针，该对象必须为一个 ctypes 类型的实例。 *offset* 默认值为零，且必须为一个将被添加到内部指针值的整数。

`byref(obj, offset)` 对应于这段 C 代码:

```
(((char *)&obj) + offset)
```

​	返回的对象只能被用作外部函数调用形参。 它的行为类似于 `pointer(obj)`，但构造起来要快很多。

## ctypes.**cast**(*obj*, *type*)

​	此函数类似于 C 的强制转换运算符。 它返回一个 *type* 的新实例，该实例指向与 *obj* 相同的内存块。 *type* 必须为指针类型，而 *obj* 必须为可以被作为指针来解读的对象。

## ctypes.**create_string_buffer**(*init_or_size*, *size=None*)

​	此函数会创建一个可变的字符缓冲区。 返回的对象是一个 [`c_char`]({{< ref "/library/allos/ctypes#ctypes.c_char" >}}) 的 ctypes 数组。

*init_or_size* 必须是一个指明数组大小的整数，或者是一个将被用来初始化数组条目的字节串对象。

​	如果将一个字节串对象指定为第一个参数，则将使缓冲区大小比其长度多一项以便数组的最后一项为一个 NUL 终结符。 可以传入一个整数作为第二个参数以允许在不使用字节串长度的情况下指定数组大小。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `ctypes.create_string_buffer` 并附带参数 `init`, `size`。

## ctypes.**create_unicode_buffer**(*init_or_size*, *size=None*)

​	此函数会创建一个可变的 unicode 字符缓冲区。 返回的对象是一个 [`c_wchar`]({{< ref "/library/allos/ctypes#ctypes.c_wchar" >}}) 的 ctypes 数组。

*init_or_size* 必须是一个指明数组大小的整数，或者是一个将被用来初始化数组条目的字符串。

​	如果将一个字符串指定为第一个参数，则将使缓冲区大小比其长度多一项以便数组的最后一项为一个 NUL 终结符。 可以传入一个整数作为第二个参数以允许在不使用字符串长度的情况下指定数组大小。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `ctypes.create_unicode_buffer` 并附带参数 `init`, `size`。

## ctypes.**DllCanUnloadNow**()

​	仅限 Windows：此函数是一个允许使用 ctypes 实现进程内 COM 服务的钩子。 它将由 _ctypes 扩展 dll 所导出的 DllCanUnloadNow 函数来调用。

## ctypes.**DllGetClassObject**()

​	仅限 Windows：此函数是一个允许使用 ctypes 实现进程内 COM 服务的钩子。 它将由 `_ctypes` 扩展 dll 所导出的 DllGetClassObject 函数来调用。

## ctypes.util.**find_library**(*name*)

​	尝试寻找一个库并返回路径名称。 *name* 是库名称并且不带任何前缀如 `lib` 以及后缀如 `.so`，`.dylib` 或版本号（形式与 posix 链接器选项 `-l` 所用的一致）。 如果找不到库，则返回 `None`。

​	确切的功能取决于系统。

## ctypes.util.**find_msvcrt**()

​	仅限 Windows：返回 Python 以及扩展模块所使用的 VC 运行时库的文件名。 如果无法确定库名称，则返回 `None`。

​	如果你需要通过调用 `free(void *)` 来释放内存，例如某个扩展模块所分配的内存，重要的一点是你应当使用分配内存的库中的函数。

## ctypes.**FormatError**([*code*])

​	仅限 Windows：返回错误码 *code* 的文本描述。 如果未指定错误码，则会通过调用 Windows api 函数 GetLastError 来获得最新的错误码。

## ctypes.**GetLastError**()

​	仅限 Windows：返回 Windows 在调用线程中设置的最新错误码。 此函数会直接调用 Windows `GetLastError()` 函数，它并不返回错误码的 ctypes 私有副本。

## ctypes.**get_errno**()

​	返回调用线程中系统 [`errno`]({{< ref "/library/allos/errno#module-errno" >}}) 变量的 ctypes 私有副本的当前值。

​	引发一个不带参数的 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `ctypes.get_errno`。

## ctypes.**get_last_error**()

​	仅限 Windows：返回调用线程中系统 `LastError` 变量的 ctypes 私有副本的当前值。

​	引发一个不带参数的 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `ctypes.get_last_error`。

## ctypes.**memmove**(*dst*, *src*, *count*)

​	与标准 C memmove 库函数相同：将 *count* 个字节从 *src* 拷贝到 *dst*。 *dst* 和 *src* 必须为整数或可被转换为指针的 ctypes 实例。

## ctypes.**memset**(*dst*, *c*, *count*)

​	与标准 C memset 库函数相同：将位于地址 *dst* 的内存块用 *count* 个字节的 *c* 值填充。 *dst* 必须为指定地址的整数或 ctypes 实例。

## ctypes.**POINTER**(*type*, */*)

​	创建并返回一个新的 ctypes 指针类型。 指针类型会被缓存并在内部重复使用，因此重复调用此函数耗费不大。 *type* 必须为 ctypes 类型。

## ctypes.**pointer**(*obj*, */*)

​	创建一个新的指针实例，指向 *obj*。 返回的对象类型为 `POINTER(type(obj))`。

​	注意：如果你只是想向外部函数调用传递一个对象指针，你应当使用更为快速的 `byref(obj)`。

## ctypes.**resize**(*obj*, *size*)

​	此函数可改变 *obj* 的内部内存缓冲区大小，其参数必须为 ctypes 类型的实例。 没有可能将缓冲区设为小于对象类型的本机大小值，该值由 `sizeof(type(obj))` 给出，但将缓冲区加大则是可能的。

## ctypes.**set_errno**(*value*)

​	设置调用线程中系统 [`errno`]({{< ref "/library/allos/errno#module-errno" >}}) 变量的 ctypes 私有副本的当前值为 *value* 并返回原来的值。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `ctypes.set_errno` 并附带参数 `errno`。

## ctypes.**set_last_error**(*value*)

​	仅限 Windows：设置调用线程中系统 `LastError` 变量的 ctypes 私有副本的当前值为 *value* 并返回原来的值。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `ctypes.set_last_error` 并附带参数 `error`。

## ctypes.**sizeof**(*obj_or_type*)

​	返回 ctypes 类型或实例的内存缓冲区以字节表示的大小。 其功能与 C `sizeof` 运算符相同。

## ctypes.**string_at**(*ptr*, *size=-1*)

​	返回位于 *void \*ptr* 的字节串。 如果指定了 *size*，它将被用作字节串的大小，否则将假定字节串以零值结尾。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `ctypes.string_at` 并附带参数 `ptr`, `size`。

## ctypes.**WinError**(*code=None*, *descr=None*)

​	仅限 Windows：此函数可能是 ctypes 中命名得最糟糕的。 它会创建一个 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 的实例。 如果未指定 *code*，则会调用 `GetLastError` 来确定错误码。 如果未指定 *descr*，则会调用 [`FormatError()`]({{< ref "/library/allos/ctypes#ctypes.FormatError" >}}) 来获取错误的文本描述。

*在 3.3 版本发生变更:* 过去会创建 [`WindowsError`]({{< ref "/library/exceptions#WindowsError" >}}) 的实例，现在它是 [`OSError`]({{< ref "/library/exceptions#OSError" >}}) 的别名。

## ctypes.**wstring_at**(*ptr*, *size=-1*)

​	返回位于 *void \*ptr* 的宽字符串。 如果指定了 *size*，它将被用作字符串的字符数量，否则将假定字符串以零值结尾。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `ctypes.wstring_at` 并附带参数 `ptr`, `size`。



### 数据类型

## *class* ctypes.**_CData**

​	这个非公有类是所有 ctypes 数据类型的共同基类。 另外，所有 ctypes 类型的实例都包含一个存放 C 兼容数据的内存块；该内存块的地址可由 [`addressof()`]({{< ref "/library/allos/ctypes#ctypes.addressof" >}}) 辅助函数返回。 还有一个实例变量被公开为 [`_objects`]({{< ref "/library/allos/ctypes#ctypes._CData._objects" >}})；此变量包含其他在内存块包含指针的情况下需要保持存活的 Python 对象。

​	ctypes 数据类型的通用方法，它们都是类方法（严谨地说，它们是 [metaclass]({{< ref "/glossary/idx#term-metaclass" >}}) 的方法）:

## **from_buffer**(*source*[, *offset*])

​	此方法返回一个共享 *source* 对象缓冲区的 ctypes 实例。 *source* 对象必须支持可写缓冲区接口。 可选的 *offset* 形参指定以字节表示的源缓冲区内偏移量；默认值为零。 如果源缓冲区不够大则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `ctypes.cdata/buffer` 并附带参数 `pointer`, `size`, `offset`。

## **from_buffer_copy**(*source*[, *offset*])

​	此方法创建一个 ctypes 实例，从 *source* 对象缓冲区拷贝缓冲区，该对象必须是可读的。 可选的 *offset* 形参指定以字节表示的源缓冲区内偏移量；默认值为零。 如果源缓冲区不够大则会引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

​	引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `ctypes.cdata/buffer` 并附带参数 `pointer`, `size`, `offset`。

## **from_address**(*address*)

​	此方法会使用 *address* 所指定的内存返回一个 ctypes 类型的实例，该参数必须为一个整数。

​	这个方法以及其他间接调用了它的方法会引发一个 [审计事件]({{< ref "/library/python/sys#auditing" >}}) `ctypes.cdata`，附带参数 `address`。

## **from_param**(*obj*)

​	This method adapts *obj* to a ctypes type. It is called with the actual object used in a foreign function call when the type is present in the foreign function's [`argtypes`]({{< ref "/library/allos/ctypes#ctypes._CFuncPtr.argtypes" >}}) tuple; it must return an object that can be used as a function call parameter.

​	所有 ctypes 数据类型都带有这个类方法的默认实现，它通常会返回 *obj*，如果该对象是此类型的实例的话。 某些类型也能接受其他对象。

## **in_dll**(*library*, *name*)

​	此方法返回一个由共享库导出的 ctypes 类型。 *name* 为导出数据的符号名称，*library* 为所加载的共享库。

​	ctypes 数据类型的通用实例变量:

## **_b_base_**

​	有时 ctypes 数据实例并不拥有它们所包含的内存块，它们只是共享了某个基对象的部分内存块。 [`_b_base_`]({{< ref "/library/allos/ctypes#ctypes._CData._b_base_" >}}) 只读成员是拥有内存块的根 ctypes 对象。

## **_b_needsfree_**

​	这个只读变量在 ctypes 数据实例自身已分配了内存块时为真值，否则为假值。

## **_objects**

​	这个成员或者为 `None`，或者为一个包含需要保持存活以使内存块的内存保持有效的 Python 对象的字典。 这个对象只是出于调试目的而对外公开；绝对不要修改此字典的内容。



### 基础数据类型

## *class* ctypes.**_SimpleCData**

​	这个非公有类是所有基本 ctypes 数据类型的基类。 它在这里被提及是因为它包含基本 ctypes 数据类型共有的属性。 [`_SimpleCData`]({{< ref "/library/allos/ctypes#ctypes._SimpleCData" >}}) 是 [`_CData`]({{< ref "/library/allos/ctypes#ctypes._CData" >}}) 的子类，因此继承了其方法和属性。 非指针及不包含指针的 ctypes 数据类型现在将可以被封存。

​	实例拥有一个属性:

## **value**

​	这个属性包含实例的实际值。 对于整数和指针类型，它是一个整数，对于字符类型，它是一个单字符字符串对象或字符串，对于字符指针类型，它是一个 Python 字节串对象或字符串。

​	当从 ctypes 实例提取 `value` 属性时，通常每次会返回一个新的对象。 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 并 *没有* 实现原始对象返回，它总是会构造一个新的对象。 所有其他 ctypes 对象实例也同样如此。

​	Fundamental data types, when returned as foreign function call results, or, for example, by retrieving structure field members or array items, are transparently converted to native Python types. In other words, if a foreign function has a [`restype`]({{< ref "/library/allos/ctypes#ctypes._CFuncPtr.restype" >}}) of [`c_char_p`]({{< ref "/library/allos/ctypes#ctypes.c_char_p" >}}), you will always receive a Python bytes object, *not* a [`c_char_p`]({{< ref "/library/allos/ctypes#ctypes.c_char_p" >}}) instance.

​	基本数据类型的子类 *不会* 继承这种行为。 因此，如果一个外部函数的 `restype` 是 [`c_void_p`]({{< ref "/library/allos/ctypes#ctypes.c_void_p" >}}) 的子类，则你将从函数调用得到一个该子类的实例。 当然，你可以通过访问 `value` 属性来获取指针的值。

​	这些是基本 ctypes 数据类型:

## *class* ctypes.**c_byte**

​	代表 C signed char 数据类型，并将值解读为一个小整数。 该构造器接受一个可选的整数初始值；不会执行溢出检查。

## *class* ctypes.**c_char**

​	代表 C char 数据类型，并将值解读为单个字符。 该构造器接受一个可选的字符串初始值，字符串的长度必须恰好为一个字符。

## *class* ctypes.**c_char_p**

​	当指向一个以零为结束符的字符串时代表 C char* 数据类型。 对于通用字符指针来说也可能指向二进制数据，必须要使用 `POINTER(c_char)`。 该构造器接受一个整数地址，或者一个字节串对象。

## *class* ctypes.**c_double**

​	代表 C double 数据类型。 该构造器接受一个可选的浮点数初始值。

## *class* ctypes.**c_longdouble**

​	代表 C long double 数据类型。 该构造器接受一个可选的浮点数初始值。 在 `sizeof(long double) == sizeof(double)` 的平台上它是 [`c_double`]({{< ref "/library/allos/ctypes#ctypes.c_double" >}}) 的一个别名。

## *class* ctypes.**c_float**

​	代表 C float 数据类型。 该构造器接受一个可选的浮点数初始值。datatype. The constructor accepts an optional float initializer.

## *class* ctypes.**c_int**

​	代表 C signed int 数据类型。 该构造器接受一个可选的整数初始值；不会执行溢出检查。 在 `sizeof(int) == sizeof(long)` 的平台上它是 [`c_long`]({{< ref "/library/allos/ctypes#ctypes.c_long" >}}) 的一个别名。

## *class* ctypes.**c_int8**

​	代表 C 8 位 signed int 数据类型。 通常是 [`c_byte`]({{< ref "/library/allos/ctypes#ctypes.c_byte" >}}) 的一个别名。

## *class* ctypes.**c_int16**

​	代表 C 16 位 signed int 数据类型。 通常是 [`c_short`]({{< ref "/library/allos/ctypes#ctypes.c_short" >}}) 的一个别名。

## *class* ctypes.**c_int32**

​	代表 C 32 位 signed int 数据类型。 通常是 [`c_int`]({{< ref "/library/allos/ctypes#ctypes.c_int" >}}) 的一个别名。

## *class* ctypes.**c_int64**

​	代表 C 64 位 signed int 数据类型。 通常是 [`c_longlong`]({{< ref "/library/allos/ctypes#ctypes.c_longlong" >}}) 的一个别名。

## *class* ctypes.**c_long**

​	代表 C signed long 数据类型。 该构造器接受一个可选的整数初始值；不会执行溢出检查。

## *class* ctypes.**c_longlong**

​	代表 C signed long long 数据类型。 该构造器接受一个可选的整数初始值；不会执行溢出检查。

## *class* ctypes.**c_short**

​	代表 C signed short 数据类型。 该构造器接受一个可选的整数初始值；不会执行溢出检查。

## *class* ctypes.**c_size_t**

​	代表 C `size_t` 数据类型。

## *class* ctypes.**c_ssize_t**

​	代表 C `ssize_t` 数据类型。

> Added in version 3.2.
>

## *class* ctypes.**c_time_t**

​	代表 C `time_t` 数据类型。

> Added in version 3.12.
>

## *class* ctypes.**c_ubyte**

​	代表 C unsigned char 数据类型，它将值解读为一个小整数。 该构造器接受一个可选的整数初始值；不会执行溢出检查。

## *class* ctypes.**c_uint**

​	代表 C unsigned int 数据类型。 该构造器接受一个可选的整数初始值；不会执行溢出检查。 在 `sizeof(int) == sizeof(long)` 的平台上它是 [`c_ulong`]({{< ref "/library/allos/ctypes#ctypes.c_ulong" >}}) 的一个别名。

## *class* ctypes.**c_uint8**

​	代表 C 8 位 unsigned int 类型。 通常是 [`c_ubyte`]({{< ref "/library/allos/ctypes#ctypes.c_ubyte" >}}) 的一个别名。.

## *class* ctypes.**c_uint16**

​	代表 C 16 位 unsigned int 数据类型。 通常是 [`c_ushort`]({{< ref "/library/allos/ctypes#ctypes.c_ushort" >}}) 的一个别名。.

## *class* ctypes.**c_uint32**

​	代表 C 32 位 unsigned int 数据类型。 通常是 [`c_uint`]({{< ref "/library/allos/ctypes#ctypes.c_uint" >}}) 的一个别名。

## *class* ctypes.**c_uint64**

​	代表 C 64 位 unsigned int 数据类型。 通常是 [`c_ulonglong`]({{< ref "/library/allos/ctypes#ctypes.c_ulonglong" >}}) 的一个别名。

## *class* ctypes.**c_ulong**

​	代表 C unsigned long 数据类型。 该构造器接受一个可选的整数初始值；不会执行溢出检查。

## *class* ctypes.**c_ulonglong**

​	代表 C unsigned long long 数据类型。 该构造器接受一个可选的整数初始值；不会执行溢出检查。

## *class* ctypes.**c_ushort**

​	代表 C unsigned short 数据类型。 该构造器接受一个可选的整数初始值；不会执行溢出检查。

## *class* ctypes.**c_void_p**

​	代表 C void* 类型。 该值被表示为整数形式。 该构造器接受一个可选的整数初始值。

## *class* ctypes.**c_wchar**

​	代表 C `wchar_t` 数据类型，并将值解读为一单个字符的 unicode 字符串。 该构造器接受一个可选的字符串初始化器，字符串的长度必须恰好为一个字符。

## *class* ctypes.**c_wchar_p**

​	代表 C wchar_t* 数据类型，它必须为指向以零为续签符的宽字符串的指针。 该构造器接受一个整数地址，或一个字符串。

## *class* ctypes.**c_bool**

​	代表 C bool 数据类型 (更准确地说，是 C99 _Bool)。 它的值可以为 `True` 或 `False`，并且该构造器接受任何具有逻辑值的对象。

## *class* ctypes.**HRESULT**

​	仅限 Windows：代表一个 `HRESULT` 值，它包含某个函数或方法调用的成功或错误信息。

## *class* ctypes.**py_object**

​	代表 C [PyObject](https://docs.python.org/zh-cn/3.13/c-api/structures.html#c.PyObject)* 数据类型。 不带参数地调用此构造器将创建一个 `NULL` [PyObject](https://docs.python.org/zh-cn/3.13/c-api/structures.html#c.PyObject)* 指针。

`ctypes.wintypes` 模块提供了其他许多 Windows 专属的数据类型，例如 `HWND`, `WPARAM` 或 `DWORD`。 还定义了一些有用的结构体如 `MSG` 或 `RECT`。



### 结构化数据类型

## *class* ctypes.**Union**(**args*, ***kw*)

​	本机字节序的联合所对应的抽象基类。

## *class* ctypes.**BigEndianUnion**(**args*, ***kw*)

*大端* 字节序的联合所对应的抽象基类。

> Added in version 3.11.
>

## *class* ctypes.**LittleEndianUnion**(**args*, ***kw*)

*小端* 字节序的联合所对应的抽象基类。

> Added in version 3.11.
>

## *class* ctypes.**BigEndianStructure**(**args*, ***kw*)

*大端* 字节序的结构体所对应的抽象基类。

## *class* ctypes.**LittleEndianStructure**(**args*, ***kw*)

*小端* 字节序的结构体所对应的抽象基类。

​	非本机字节序的结构体和联合不能包含指针类型字段，或任何其他包含指针类型字段的数据类型。

## *class* ctypes.**Structure**(**args*, ***kw*)

*本机* 字节序的结构体所对应的抽象基类。

​	实际的结构体和联合类型必须通过子类化这些类型之一来创建，并且至少要定义一个 [`_fields_`]({{< ref "/library/allos/ctypes#ctypes.Structure._fields_" >}}) 类变量。 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 将创建 [descriptor]({{< ref "/glossary/idx#term-descriptor" >}})，它允许通过直接属性访问来读取和写入字段。 这些是

## **_fields_**

​	一个定义结构体字段的序列。 其中的条目必须为 2 元组或 3 元组。 元组的第一项是字段名称，第二项指明字段类型；它可以是任何 ctypes 数据类型。

​	对于整数类型字段例如 [`c_int`]({{< ref "/library/allos/ctypes#ctypes.c_int" >}})，可以给定第三个可选项。 它必须是一个定义字段比特位宽度的小正整数。

​	字段名称在一个结构体或联合中必须唯一。 不会检查这个唯一性，但当名称出现重复时将只有一个字段可被访问。

​	可以在定义 Structure 子类的类语句 *之后* 再定义 [`_fields_`]({{< ref "/library/allos/ctypes#ctypes.Structure._fields_" >}}) 类变量，这将允许创建直接或间接引用其自身的数据类型:

```
class List(Structure):
    pass
List._fields_ = [("pnext", POINTER(List)),
                 ...
                ]
```

​	但是，[`_fields_`]({{< ref "/library/allos/ctypes#ctypes.Structure._fields_" >}}) 类变量必须在类型第一次被使用（创建实例，调用 [`sizeof()`]({{< ref "/library/allos/ctypes#ctypes.sizeof" >}}) 等等）之前进行定义。 在此之后对 [`_fields_`]({{< ref "/library/allos/ctypes#ctypes.Structure._fields_" >}}) 类变量赋值将会引发 AttributeError。

​	可以定义结构体类型的子类，它们会继承基类的字段再加上在子类中定义的任何 [`_fields_`]({{< ref "/library/allos/ctypes#ctypes.Structure._fields_" >}})。

## **_pack_**

​	一个可选的小整数，它允许覆盖实例中结构体字段的对齐方式。 当 [`_fields_`]({{< ref "/library/allos/ctypes#ctypes.Structure._fields_" >}}) 被赋值时必须已经定义了 [`_pack_`]({{< ref "/library/allos/ctypes#ctypes.Structure._pack_" >}})，否则它将没有效果。 将该属性设为 0 的效果与不设置它一样。

## **_align_**

​	一个可选的小整数，它允许覆盖结构体在针对内存执行打包或解包时的对齐方式。 将该属性设为 0 与完全不设置它效果相同。

> Added in version 3.13.
>

## **_anonymous_**

​	一个可选的序列，它会列出未命名（匿名）字段的名称。 当 [`_fields_`]({{< ref "/library/allos/ctypes#ctypes.Structure._fields_" >}}) 被赋值时必须已经定义了 [`_anonymous_`]({{< ref "/library/allos/ctypes#ctypes.Structure._anonymous_" >}})，否则它将没有效果。

​	在此变量中列出的字段必须为结构体或联合类型字段。 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 将在结构体类型中创建描述器以允许直接访问嵌套字段，而无需创建对应的结构体或联合字段。

​	以下是一个示例类型（Windows）:

```
class _U(Union):
    _fields_ = [("lptdesc", POINTER(TYPEDESC)),
                ("lpadesc", POINTER(ARRAYDESC)),
                ("hreftype", HREFTYPE)]

class TYPEDESC(Structure):
    _anonymous_ = ("u",)
    _fields_ = [("u", _U),
                ("vt", VARTYPE)]
```

`TYPEDESC` 结构体描述了一个 COM 数据类型，`vt` 字段指明哪个联合字段是有效的。 由于 `u` 字段被定义为匿名字段，现在可以直接从 TYPEDESC 实例访问成员。 `td.lptdesc` 和 `td.u.lptdesc` 是等价的，但前者速度更快，因为它不需要创建临时的联合实例:

```
td = TYPEDESC()
td.vt = VT_PTR
td.lptdesc = POINTER(some_type)
td.u.lptdesc = POINTER(some_type)
```

​	可以定义结构体的子类，它们会继承基类的字段。 如果子类定义具有单独的 [`_fields_`]({{< ref "/library/allos/ctypes#ctypes.Structure._fields_" >}}) 变量，在其中指定的字段会被添加到基类的字段中。

​	结构体和联合的构造器均可接受位置和关键字参数。 位置参数用于按照 [`_fields_`]({{< ref "/library/allos/ctypes#ctypes.Structure._fields_" >}}) 中的出现顺序来初始化成员字段。 构造器中的关键字参数会被解读为属性赋值，因此它们将以相应的名称来初始化 [`_fields_`]({{< ref "/library/allos/ctypes#ctypes.Structure._fields_" >}})，或为不存在于 [`_fields_`]({{< ref "/library/allos/ctypes#ctypes.Structure._fields_" >}}) 中的名称创建新的属性。



### 数组与指针

## *class* ctypes.**Array**(**args*)

​	数组的抽象基类。

​	创建实体数组类型的推荐方式是通过将任意 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 数据类型与一个非负整数相乘。 作为替代方式，你也可以子类化这个类型并定义 [`_length_`]({{< ref "/library/allos/ctypes#ctypes.Array._length_" >}}) 和 [`_type_`]({{< ref "/library/allos/ctypes#ctypes.Array._type_" >}}) 类变量。 数组元素可使用标准的抽取和切片操作来进行读写；对于切片读取，结果对象本身 *不是* 一个 [`Array`]({{< ref "/library/allos/ctypes#ctypes.Array" >}})。

## **_length_**

​	一个指明数组中元素数量的正整数。 超出范围的抽取会导致 [`IndexError`]({{< ref "/library/exceptions#IndexError" >}})。 该值将由 [`len()`]({{< ref "/library/functions#len" >}}) 返回。

## **_type_**

​	指明数组中每个元素的类型。

​	Array 子类构造器可接受位置参数，用来按顺序初始化元素。

## ctypes.**ARRAY**(*type*, *length*)

​	创建一个数组。 等价于 `type * length`，其中 *type* 是一个 [`ctypes`]({{< ref "/library/allos/ctypes#module-ctypes" >}}) 数据类型而 *length* 是一个整数。

​	该函数已被 [soft deprecated]({{< ref "/glossary/idx#term-soft-deprecated" >}}) 而应改用乘法。 尚无移除它的计划。

## *class* ctypes.**_Pointer**

​	私有对象，指针的抽象基类。

​	实际的指针类型是通过调用 [`POINTER()`]({{< ref "/library/allos/ctypes#ctypes.POINTER" >}}) 并附带其将指向的类型来创建的；这会由 [`pointer()`]({{< ref "/library/allos/ctypes#ctypes.pointer" >}}) 自动完成。

​	如果一个指针指向的是数组，则其元素可使用标准的抽取和切片方式来读写。 指针对象没有长度，因此 [`len()`]({{< ref "/library/functions#len" >}}) 将引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。 抽取负值将会从指针 *之前* 的内存中读取（与 C 一样），并且超出范围的抽取将可能因非法访问而导致崩溃（视你的运气而定）。

## **_type_**

​	指明所指向的类型。

## **contents**

​	返回指针所指向的对象。 对此属性赋值会使指针改为指向所赋值的对象。
