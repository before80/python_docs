+++
title = "locale --- 国际化服务"
date = 2024-11-15T20:47:51+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/locale.html](https://docs.python.org/zh-cn/3.13/library/locale.html)
>
> 收录该文档的时间：`2024-11-15T20:47:51+08:00`

# `locale` --- 国际化服务

**源代码：** [Lib/locale.py](https://github.com/python/cpython/tree/3.13/Lib/locale.py)

------

[`locale`]({{< ref "/library/i18n/locale#module-locale" >}}) 模块开通了 POSIX 本地化数据库和功能的访问。POSIX 本地化机制让程序员能够为应用程序处理某些本地化的问题，而不需要去了解运行软件的每个国家的全部语言习惯。

[`locale`]({{< ref "/library/i18n/locale#module-locale" >}}) 模块是在 `_locale` 模块之上实现的，后者又会在可能的情况下使用 ANSI C 语言区域实现。

[`locale`]({{< ref "/library/i18n/locale#module-locale" >}}) 模块定义了以下异常和函数：

## *exception* locale.**Error**

​	当传给 [`setlocale()`]({{< ref "/library/i18n/locale#locale.setlocale" >}}) 的 locale 无法识别时，会触发异常。

## locale.**setlocale**(*category*, *locale=None*)

​	如果给定了 *locale* 而不是 `None`，[`setlocale()`]({{< ref "/library/i18n/locale#locale.setlocale" >}}) 会修改 *category* 的 locale 设置。可用的类别会在下面的数据描述中列出。*locale* 可以是一个字符串，也可以是两个字符串（语言代码和编码）组成的可迭代对象。若为可迭代对象，则会用地区别名引擎转换为一个地区名称。若为空字符串则指明采用用户的默认设置。如果 locale 设置修改失败，会触发 [`Error`]({{< ref "/library/i18n/locale#locale.Error" >}}) 异常。如果成功则返回新的 locale 设置。

​	如果省略 *locale* 或为 `None`，将返回 *category* 但当前设置。

[`setlocale()`]({{< ref "/library/i18n/locale#locale.setlocale" >}}) 在大多数系统上都不是线程安全的。应用程序通常会如下调用：

```
import locale
locale.setlocale(locale.LC_ALL, '')
```

​	这会把所有类别的 locale 都设为用户的默认设置（通常在 `LANG` 环境变量中指定）。如果后续 locale 没有改动，则使用多线程应该不会产生问题。

## locale.**localeconv**()

​	以字典的形式返回本地约定的数据库。此字典具有以下字符串作为键：

| 类别                                                         | 键                                | 含意                                                         |
| :----------------------------------------------------------- | :-------------------------------- | :----------------------------------------------------------- |
| [`LC_NUMERIC`]({{< ref "/library/i18n/locale#locale.LC_NUMERIC" >}}) | `'decimal_point'`                 | 小数点字符。                                                 |
|                                                              | `'grouping'`                      | 数字列表，指定 `'thousands_sep'` 应该出现的位置。 如果列表以 [`CHAR_MAX`]({{< ref "/library/i18n/locale#locale.CHAR_MAX" >}}) 结束，则不会作分组。如果列表以 `0` 结束，则重复使用最后的分组大小。 |
|                                                              | `'thousands_sep'`                 | 组之间使用的字符。                                           |
| [`LC_MONETARY`]({{< ref "/library/i18n/locale#locale.LC_MONETARY" >}}) | `'int_curr_symbol'`               | 国际货币符号。                                               |
|                                                              | `'currency_symbol'`               | 当地货币符号。                                               |
|                                                              | `'p_cs_precedes/n_cs_precedes'`   | 货币符号是否在值之前（对于正值或负值）。                     |
|                                                              | `'p_sep_by_space/n_sep_by_space'` | 货币符号是否通过空格与值分隔（对于正值或负值）。             |
|                                                              | `'mon_decimal_point'`             | 用于货币金额的小数点。                                       |
|                                                              | `'frac_digits'`                   | 货币值的本地格式中使用的小数位数。                           |
|                                                              | `'int_frac_digits'`               | 货币价值的国际格式中使用的小数位数。                         |
|                                                              | `'mon_thousands_sep'`             | 用于货币值的组分隔符。                                       |
|                                                              | `'mon_grouping'`                  | 相当于 `'grouping'` ，用于货币价值。                         |
|                                                              | `'positive_sign'`                 | 用于标注正货币价值的符号。                                   |
|                                                              | `'negative_sign'`                 | 用于注释负货币价值的符号。                                   |
|                                                              | `'p_sign_posn/n_sign_posn'`       | 符号的位置（对于正值或负值），见下文。                       |

​	可以将所有数值设置为 [`CHAR_MAX`]({{< ref "/library/i18n/locale#locale.CHAR_MAX" >}}) ，以指示本 locale 中未指定任何值。

​	下面给出了 `'p_sign_posn'` 和 `'n_sign_posn'` 的可能值。

| 值         | 说明                           |
| :--------- | :----------------------------- |
| `0`        | 被括号括起来的货币和金额。     |
| `1`        | 该标志应位于值和货币符号之前。 |
| `2`        | 该标志应位于值和货币符号之后。 |
| `3`        | 标志应该紧跟在值之前。         |
| `4`        | 标志应该紧跟在值之后。         |
| `CHAR_MAX` | 该地区未指定内容。             |

​	此函数可临时性地将 `LC_CTYPE` 语言区域设为 `LC_NUMERIC` 语言区域或者如果语言区域不同且数字或货币字符串是非 ASCII 的则设为 `LC_MONETARY` 语言区域。 这个临时性地改变会影响到其他线程。

> 在 3.7 版本发生变更: 现在此函数在某些情况下会临时性地将 `LC_CTYPE` 语言区域设为 `LC_NUMERIC` 语言区域。

## locale.**nl_langinfo**(*option*)

​	Return some locale-specific information as a string. This function is not available on all systems, and the set of possible options might also vary across platforms. The possible argument values are numbers, for which symbolic constants are available in the locale module.

[`nl_langinfo()`]({{< ref "/library/i18n/locale#locale.nl_langinfo" >}}) 函数可接受以下值。大部分含义都取自 GNU C 库。

## locale.**CODESET**

​	获取一个字符串，代表所选地区采用的字符编码名称。

## locale.**D_T_FMT**

​	获取一个字符串，可用作 [`time.strftime()`]({{< ref "/library/allos/time#time.strftime" >}}) 的格式串，以便以地区特定格式表示日期和时间。

## locale.**D_FMT**

​	获取一个字符串，可用作 [`time.strftime()`]({{< ref "/library/allos/time#time.strftime" >}}) 的格式串，以便以地区特定格式表示日期。

## locale.**T_FMT**

​	获取一个字符串，可用作 [`time.strftime()`]({{< ref "/library/allos/time#time.strftime" >}}) 的格式串，以便以地区特定格式表示时间。

## locale.**T_FMT_AMPM**

​	获取一个字符串，可用作 [`time.strftime()`]({{< ref "/library/allos/time#time.strftime" >}}) 的格式串，以便以 am/pm 的格式表示时间。

## locale.**DAY_1**

## locale.**DAY_2**

## locale.**DAY_3**

## locale.**DAY_4**

## locale.**DAY_5**

## locale.**DAY_6**

## locale.**DAY_7**

​	获取一周中第 n 天的名称。

​备注
 

​	这里遵循美国惯例，即 [`DAY_1`]({{< ref "/library/i18n/locale#locale.DAY_1" >}}) 是星期天，而不是国际惯例（ISO 8601），即星期一是一周的第一天。

## locale.**ABDAY_1**

## locale.**ABDAY_2**

## locale.**ABDAY_3**

## locale.**ABDAY_4**

## locale.**ABDAY_5**

## locale.**ABDAY_6**

## locale.**ABDAY_7**

​	获取一周中第 n 天的缩写名称。

## locale.**MON_1**

## locale.**MON_2**

## locale.**MON_3**

## locale.**MON_4**

## locale.**MON_5**

## locale.**MON_6**

## locale.**MON_7**

## locale.**MON_8**

## locale.**MON_9**

## locale.**MON_10**

## locale.**MON_11**

## locale.**MON_12**

​	获取第 n 个月的名称。

## locale.**ABMON_1**

## locale.**ABMON_2**

## locale.**ABMON_3**

## locale.**ABMON_4**

## locale.**ABMON_5**

## locale.**ABMON_6**

## locale.**ABMON_7**

## locale.**ABMON_8**

## locale.**ABMON_9**

## locale.**ABMON_10**

## locale.**ABMON_11**

## locale.**ABMON_12**

​	获取第 n 个月的缩写名称。

## locale.**RADIXCHAR**

​	获取小数点字符（小数点、小数逗号等）。

## locale.**THOUSEP**

​	获取千位数（三位数一组）的分隔符。

## locale.**YESEXPR**

​	获取一个可供 regex 函数使用的正则表达式，用于识别需要回答是或否的问题的肯定回答。

## locale.**NOEXPR**

​	获取一个可供 `regex(3)` 函数使用的正则表达式以识别对于是/否型问题的否定回答。

​备注
 

​	针对 [`YESEXPR`]({{< ref "/library/i18n/locale#locale.YESEXPR" >}}) 和 [`NOEXPR`]({{< ref "/library/i18n/locale#locale.NOEXPR" >}}) 的正则表达式使用了适合来自 C 库的 `regex` 函数的语法，它与 [`re`]({{< ref "/library/text/re#module-re" >}}) 中使用的语法会有所不同。

## locale.**CRNCYSTR**

​	获取货币符号，如果符号应位于数字之前，则在其前面加上“-”；如果符号应位于数字之后，则前面加“+”；如果符号应取代小数点字符，则前面加“.”。

## locale.**ERA**

​	获取一个字符串，代表当前地区使用的纪元。

​	大多数地区都没有定义该值。定义了该值的一个案例日本。日本传统的日期表示方法中，包含了当时天皇统治朝代的名称。

​	通常没有必要直接使用该值。在格式串中指定 `E` 符号，会让 [`time.strftime()`]({{< ref "/library/allos/time#time.strftime" >}}) 函数启用此信息。返回字符串的格式并没有定义，因此不得假定各个系统都能理解。

## locale.**ERA_D_T_FMT**

​	获取一个字符串，可用作 [`time.strftime()`]({{< ref "/library/allos/time#time.strftime" >}}) 的格式串，以便以地区特定格式表示带纪元的日期和时间。

## locale.**ERA_D_FMT**

​	获取一个字符串，可用作 [`time.strftime()`]({{< ref "/library/allos/time#time.strftime" >}}) 的格式串，以便以地区特定格式表示带纪元的日期。

## locale.**ERA_T_FMT**

​	获取一个字符串，可用作 [`time.strftime()`]({{< ref "/library/allos/time#time.strftime" >}}) 的格式串，以便以地区特定格式表示带纪元的时间。

## locale.**ALT_DIGITS**

​	Get a string consisting of up to 100 semicolon-separated symbols used to represent the values 0 to 99 in a locale-specific way. In most locales this is an empty string.

## locale.**getdefaultlocale**([*envvars*])

​	尝试确定默认的地区设置，并以 `(language code, encoding)` 元组的形式返回。

​	根据 POSIX 的规范，未调用 `setlocale(LC_ALL, '')` 的程序采用可移植的 `'C'` 区域设置运行。 调用 `setlocale(LC_ALL, '')` 则可采用 `LANG` 变量定义的默认区域。 由于不想干扰当前的区域设置，因此就以上述方式进行了模拟。

​	为了维持与其他平台的兼容性，不仅需要检测 `LANG` 变量，还需要检测 envvars 参数给出的变量列表。首先发现的定义将被采用。 *envvars* 默认为 GNU gettext 采用的搜索路径；必须包含 `'LANG'` 变量。 GNU gettext 的搜索路径依次包含了 `'LC_ALL'`、`'LC_CTYPE'`、`'LANG'` 和 `'LANGUAGE'`。

​	除了 `'C'` 之外，语言代码对应 [**RFC 1766**](https://datatracker.ietf.org/doc/html/rfc1766.html) 标准。若 *语言代码* 和 *编码* 无法确定，则可为 `None`。

*Deprecated since version 3.11, will be removed in version 3.15.*

## locale.**getlocale**(*category=LC_CTYPE*)

​	以包含 *语言代码*, *编码格式* 的序列形式返回指定语言区域类别的当前设置。 *category* 可以是某个 `LC_*` 值但不能是 [`LC_ALL`]({{< ref "/library/i18n/locale#locale.LC_ALL" >}})。 默认值为 [`LC_CTYPE`]({{< ref "/library/i18n/locale#locale.LC_CTYPE" >}})。

​	除了 `'C'` 之外，语言代码对应 [**RFC 1766**](https://datatracker.ietf.org/doc/html/rfc1766.html) 标准。若 *语言代码* 和 *编码* 无法确定，则可为 `None`。

## locale.**getpreferredencoding**(*do_setlocale=True*)

​	根据用户的偏好，返回用于文本数据的 [locale encoding]({{< ref "/glossary/idx#term-locale-encoding" >}})。用户偏好在不同的系统上有不同的表达方式，而且在某些系统上可能无法以编程方式获取到，所以本函数只是返回猜测结果。

​	某些系统必须调用 [`setlocale()`]({{< ref "/library/i18n/locale#locale.setlocale" >}}) 才能获取用户偏好，所以本函数不是线程安全的。如果不需要或不希望调用 setlocale，*do_setlocale* 应设为 `False`。

​	在 Android 上或者如果启用了 [Python UTF-8 模式]({{< ref "/library/allos/os#utf8-mode" >}})，则将始终返回 `'utf-8'`，[locale encoding]({{< ref "/glossary/idx#term-locale-encoding" >}}) 和 *do_setlocale* 参数将被忽略。

[Python preinitialization]({{< ref "/c_api/init_config#c-preinit" >}}) 用于配置 LC_CTYPE 区域。还请参阅 [filesystem encoding and error handler]({{< ref "/glossary/idx#term-filesystem-encoding-and-error-handler" >}})。

> 在 3.7 版本发生变更: 目前在 Android 上或者如果启用了 [Python UTF-8 模式]({{< ref "/library/allos/os#utf8-mode" >}}) 此函数将总是返回 `"utf-8"`。

## locale.**getencoding**()

​	获取当前的 [locale encoding]({{< ref "/glossary/idx#term-locale-encoding" >}}):

- 在 Android 和 VxWorks 上，将返回 `"utf-8"`。
- 在 Unix 上，将返回当前, return the encoding of the current [`LC_CTYPE`]({{< ref "/library/i18n/locale#locale.LC_CTYPE" >}}) 语言区域的编码格式。 如果 `nl_langinfo(CODESET)` 返回空字符串则将返回 `"utf-8"`: 举例来说，如果当前 LC_CTYPE 语言区域不受支持的时候。
- 在 Windows 上，返回 ANSI 代码页。

[Python preinitialization]({{< ref "/c_api/init_config#c-preinit" >}}) 用于配置 LC_CTYPE 区域。还请参阅 [filesystem encoding and error handler]({{< ref "/glossary/idx#term-filesystem-encoding-and-error-handler" >}})。

​	此函数类似于 [`getpreferredencoding(False)`]({{< ref "/library/i18n/locale#locale.getpreferredencoding" >}})，区别是此函数会忽略 [Python UTF-8 模式]({{< ref "/library/allos/os#utf8-mode" >}})。

> Added in version 3.11.
>

## locale.**normalize**(*localename*)

​	为给定的区域名称返回标准代码。返回的区域代码已经格式化，可供 [`setlocale()`]({{< ref "/library/i18n/locale#locale.setlocale" >}}) 使用。 如果标准化操作失败，则返回原名称。

​	如果给出的编码无法识别，则本函数默认采用区域代码的默认编码，这正类似于 [`setlocale()`]({{< ref "/library/i18n/locale#locale.setlocale" >}})。

## locale.**strcoll**(*string1*, *string2*)

​	根据当前的 [`LC_COLLATE`]({{< ref "/library/i18n/locale#locale.LC_COLLATE" >}}) 设置，对两个字符串进行比较。与其他比较函数一样，根据 *string1* 位于 *string2* 之前、之后或是相同，返回负值、、正值或者 `0`。

## locale.**strxfrm**(*string*)

​	将字符串转换为可用于本地化比较的字符串。例如 `strxfrm(s1) < strxfrm(s2)` 相当于 `strcoll(s1, s2) < 0`。在重复比较同一个字符串时，可能会用到本函数，比如整理字符串列表时。

## locale.**format_string**(*format*, *val*, *grouping=False*, *monetary=False*)

​	根据当前的 [`LC_NUMERIC`]({{< ref "/library/i18n/locale#locale.LC_NUMERIC" >}}) 设置对数字 *val* 进行格式化。 此格式将遵循 `%` 运算符的约定。 对于浮点数值，会根据具体情况修改小数点。 如果 *grouping* 为 `True`，还会将分组纳入考虑。

​	若 *monetary* 为 True，则会用到货币千位分隔符和分组字符串。

​	格式化符的处理类似 `format % val` ，但会考虑到当前的区域设置。

> 在 3.7 版本发生变更: 增加了关键字参数 *monetary* 。

## locale.**currency**(*val*, *symbol=True*, *grouping=False*, *international=False*)

​	根据当前的 [`LC_MONETARY`]({{< ref "/library/i18n/locale#locale.LC_MONETARY" >}}) 设置，对数字 *val* 进行格式化。

​	如果 *symbol* 为真值则返回的字符串将包括货币符号，该参数默认为真值。 如果 *grouping* 为 `True` (非默认值)，则会对值进行分组。 如果 *international* 为 `True` (非默认值)，则会使用国际货币符号。

​备注
 

​	此函数将不适用于 'C' 语言区域，所以你必须先通过 [`setlocale()`]({{< ref "/library/i18n/locale#locale.setlocale" >}}) 设置一个语言区域。

## locale.**str**(*float*)

​	对浮点数进行格式化，格式要求与内置函数 `str(float)` 相同，但会考虑小数点。

## locale.**delocalize**(*string*)

​	根据 [`LC_NUMERIC`]({{< ref "/library/i18n/locale#locale.LC_NUMERIC" >}}) 的设置，将字符串转换为标准化的数字字符串。

> Added in version 3.5.
>

## locale.**localize**(*string*, *grouping=False*, *monetary=False*)

​	根据 [`LC_NUMERIC`]({{< ref "/library/i18n/locale#locale.LC_NUMERIC" >}}) 的设置，将标准化的数字字符串转换为格式化的字符串。

> Added in version 3.10.
>

## locale.**atof**(*string*, *func=float*)

​	将一个字符串转换为数字，遵循 [`LC_NUMERIC`]({{< ref "/library/i18n/locale#locale.LC_NUMERIC" >}}) 设置，通过在 *string* 上调用 [`delocalize()`]({{< ref "/library/i18n/locale#locale.delocalize" >}}) 的结果上调用 *func* 来实现。

## locale.**atoi**(*string*)

​	按照 [`LC_NUMERIC`]({{< ref "/library/i18n/locale#locale.LC_NUMERIC" >}}) 的约定，将字符串转换为整数。

## locale.**LC_CTYPE**

​	字符类型函数的语言区域类别。 最重要的是，该类别定义了文件编码格式，即如何将字节解读为 Unicode 码位点。 请参阅 [**PEP 538**](https://peps.python.org/pep-0538/) 和 [**PEP 540**](https://peps.python.org/pep-0540/) 了解如何将该变量自动强制转换为 `C.UTF-8` 以避免容器中的无效设置或通过远程 SSH 连接传递的不兼容设置所造成的问题。

​	Python 在内部并不使用来自 `ctype.h` 的依赖于语言区域的字符转换函数。 相反，内部 `pyctype.h` 提供了不依赖于语言区域的等价物如 `Py_TOLOWER`。

## locale.**LC_COLLATE**

​	字符串排序会用到的区域类别。 将会影响 [`locale`]({{< ref "/library/i18n/locale#module-locale" >}}) 模块的 [`strcoll()`]({{< ref "/library/i18n/locale#locale.strcoll" >}}) 和 [`strxfrm()`]({{< ref "/library/i18n/locale#locale.strxfrm" >}}) 函数。

## locale.**LC_TIME**

​	格式化时间时会用到的区域类别。 [`time.strftime()`]({{< ref "/library/allos/time#time.strftime" >}}) 函数会参考这些约定。

## locale.**LC_MONETARY**

​	格式化货币值时会用到的区域类别。可用值可由 [`localeconv()`]({{< ref "/library/i18n/locale#locale.localeconv" >}}) 函数获取。

## locale.**LC_MESSAGES**

​	显示消息时用到的区域类别。目前 Python 不支持应用定制的本地化消息。 由操作系统显示的消息，比如由 [`os.strerror()`]({{< ref "/library/allos/os#os.strerror" >}}) 返回的消息可能会受到该类别的影响。

​	这个值在不符合 POSIX 标准的操作系统上可能不可用，最主要是指 Windows。

## locale.**LC_NUMERIC**

​	用于格式化数字的语言区域类别。 [`locale`]({{< ref "/library/i18n/locale#module-locale" >}}) 模块的 [`format_string()`]({{< ref "/library/i18n/locale#locale.format_string" >}}), [`atoi()`]({{< ref "/library/i18n/locale#locale.atoi" >}}), [`atof()`]({{< ref "/library/i18n/locale#locale.atof" >}}) 和 [`str()`]({{< ref "/library/i18n/locale#locale.str" >}}) 等函数会受到该类别的影响。 其他所有数字格式化操作将不受影响。

## locale.**LC_ALL**

​	混合所有的区域设置。如果在区域改动时使用该标志，将尝试设置所有类别的区域参数。只要有任何一个类别设置失败，就不会修改任何类别。在使用此标志获取区域设置时，会返回一个代表所有类别设置的字符串。之后可用此字符串恢复设置。

## locale.**CHAR_MAX**

​	一个符号常量， [`localeconv()`]({{< ref "/library/i18n/locale#locale.localeconv" >}}) 返回多个值时将会用到。

​	示例：



``` python
>>> import locale
>>> loc = locale.getlocale()  # 获取当前语言区域
# 使用德语的语言区域；在具体平台上名称可能不同
>>> locale.setlocale(locale.LC_ALL, 'de_DE')
>>> locale.strcoll('f\xe4n', 'foo')  # 比较一个包含变音的字符串
>>> locale.setlocale(locale.LC_ALL, '')   # 使用用户首选的语言区域
>>> locale.setlocale(locale.LC_ALL, 'C')  # 使用默认的语言区域 (C)
>>> locale.setlocale(locale.LC_ALL, loc)  # 恢复已保存的语言区域
```

## 背景、细节、提示、技巧和注意事项

​	C 语言标准将区域定义为程序级别的属性，修改的代价可能相对较高。此外，有某些实现代码写得不好，频繁改变区域可能会导致内核崩溃。于是要想正确使用区域就变得有些痛苦。

​	当程序第一次启动时，无论用户偏好定义成什么，区域值都是 `C`。不过有一个例外，就是在启动时修改 [`LC_CTYPE`]({{< ref "/library/i18n/locale#locale.LC_CTYPE" >}}) 类别，设置当前区域编码为用户偏好编码。程序必须调用 `setlocale(LC_ALL, '')` 明确表示用户偏好区域将设为其他类别。

​	若要从库程序中调用 [`setlocale()`]({{< ref "/library/i18n/locale#locale.setlocale" >}}) ，通常这不是个好主意，因为副作用是会影响整个程序。保存和恢复区域设置也几乎一样糟糕：不仅代价高昂，而且会影响到恢复之前运行的其他线程。

​	如果是要编写通用模块，需要有一种不受区域设置影响的操作方式（比如某些用到 [`time.strftime()`]({{< ref "/library/allos/time#time.strftime" >}}) 的格式），将不得不寻找一种不用标准库的方案。更好的办法是说服自己，可以采纳区域设置。只有在万不得已的情况下，才能用文档标注出模块与非 `C` 区域设置不兼容。

​	根据语言区域执行数字运算的唯一方式就是使用本模块所定义的特殊函数: [`atof()`]({{< ref "/library/i18n/locale#locale.atof" >}}), [`atoi()`]({{< ref "/library/i18n/locale#locale.atoi" >}}), [`format_string()`]({{< ref "/library/i18n/locale#locale.format_string" >}}), [`str()`]({{< ref "/library/i18n/locale#locale.str" >}})。

​	无法根据区域设置进行大小写转换和字符分类。对于（Unicode）文本字符串来说，这些操作都是根据字符值进行的；而对于字节符串来说，转换和分类则是根据字节的 ASCII 值进行的，高位被置位的字节（即非 ASCII 字节）永远不会被转换或被视作字母或空白符之类。



## 针对扩展程序编写人员和嵌入Python 运行的程序

​	除了要查询当前区域，扩展模块不应去调用 [`setlocale()`]({{< ref "/library/i18n/locale#locale.setlocale" >}})。但由于返回值只能用于恢复设置，所以也没什么用（也许只能用于确认是否为 `C`）。

​	当 Python 代码使用 [`locale`]({{< ref "/library/i18n/locale#module-locale" >}}) 模块来修改语言区域时，这也会影响到嵌入的应用程序。 如果嵌入的应用程序不希望发生这种情况，它应当从 `config.c` 文件的内置模块表中移除 `_locale` 扩展模块（所有工作都是由它完成的），以确保 `_locale` 模块不能作为共享库来访问。



## 访问消息目录

## locale.**gettext**(*msg*)

## locale.**dgettext**(*domain*, *msg*)

## locale.**dcgettext**(*domain*, *msg*, *category*)

## locale.**textdomain**(*domain*)

## locale.**bindtextdomain**(*domain*, *dir*)

## locale.**bind_textdomain_codeset**(*domain*, *codeset*)

​	locale 模块在提供了 C 库的 gettext 接口的系统上对外公开该接口。 它由 [`gettext()`]({{< ref "/library/i18n/gettext#module-gettext" >}}), [`dgettext()`]({{< ref "/library/i18n/locale#locale.dgettext" >}}), [`dcgettext()`]({{< ref "/library/i18n/locale#locale.dcgettext" >}}), [`textdomain()`]({{< ref "/library/i18n/locale#locale.textdomain" >}}), [`bindtextdomain()`]({{< ref "/library/i18n/locale#locale.bindtextdomain" >}}) 和 [`bind_textdomain_codeset()`]({{< ref "/library/i18n/locale#locale.bind_textdomain_codeset" >}}) 等函数组成。 它们与 [`gettext`]({{< ref "/library/i18n/gettext#module-gettext" >}}) 模块中的同名函数类似，但使用了 C 库的二进制格式来表示消息目录，并使用 C 库的搜索算法来查找消息目录。

​	Python 应用程序通常不需要发起调用这些函数，而应当改用 [`gettext`]({{< ref "/library/i18n/gettext#module-gettext" >}})。 这条规则的一个已知例外是与附加 C 库相链接的应用程序，它们会在内部发起调用 C 函数 `gettext` 或 `dcgettext`。 对于这些应用程序，可能有必要绑定文本域，以便库能够正确地找到它们的消息目录。
