+++
title = "zoneinfo --- IANA 时区支持"
date = 2024-11-15T11:18:41+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/zoneinfo.html](https://docs.python.org/zh-cn/3.13/library/zoneinfo.html)
>
> 收录该文档的时间：`2024-11-15T11:18:41+08:00`

# `zoneinfo` --- IANA 时区支持

> Added in version 3.9.
>

**源代码:** [Lib/zoneinfo](https://github.com/python/cpython/tree/3.13/Lib/zoneinfo)

------

[`zoneinfo`]({{< ref "/library/datatypes/zoneinfo#module-zoneinfo" >}}) 模块根据 [**PEP 615**](https://peps.python.org/pep-0615/) 中的原始规范说明提供了一个具体的时区实现来支持 IANA 时区数据库。 在默认情况下，[`zoneinfo`]({{< ref "/library/datatypes/zoneinfo#module-zoneinfo" >}}) 会在可能的情况下使用系统的时区数据；如果系统时区数据不可用，该库将回退为使用 PyPI 上提供的 [tzdata](https://pypi.org/project/tzdata/) 第一方包。

​参见
## 模块: [`datetime`]({{< ref "/library/datatypes/datetime#module-datetime" >}})

​	提供 [`time`]({{< ref "/library/datatypes/datetime#datetime.time" >}}) 和 [`datetime`]({{< ref "/library/datatypes/datetime#datetime.datetime" >}}) 类型，[`ZoneInfo`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.ZoneInfo" >}}) 类被设计为可配合这两个类型使用。

## 包 [tzdata](https://pypi.org/project/tzdata/)

​	由 CPython 核心开发者维护以通过 PyPI 提供时区数据的第一方包。

[Availability]({{< ref "/library/intro#availability" >}}): not WASI.

​	此模块在 WebAssembly 平台上无效或不可用。 请参阅 [WebAssembly 平台]({{< ref "/library/intro#wasm-availability" >}}) 了解详情。

## 使用 `ZoneInfo`

[`ZoneInfo`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.ZoneInfo" >}}) 是 [`datetime.tzinfo`]({{< ref "/library/datatypes/datetime#datetime.tzinfo" >}}) 抽象基类的具体实现，其目标是通过构造器、 [`datetime.replace`]({{< ref "/library/datatypes/datetime#datetime.datetime.replace" >}}) 方法或 [`datetime.astimezone`]({{< ref "/library/datatypes/datetime#datetime.datetime.astimezone" >}}) 来与 `tzinfo` 建立关联:



``` python
>>> from zoneinfo import ZoneInfo
>>> from datetime import datetime, timedelta

>>> dt = datetime(2020, 10, 31, 12, tzinfo=ZoneInfo("America/Los_Angeles"))
>>> print(dt)
2020-10-31 12:00:00-07:00

>>> dt.tzname()
'PDT'
```

​	以此方式构造的日期时间对象可兼容日期时间运算并可在无需进一步干预的情况下处理夏令时转换:



``` python
>>> dt_add = dt + timedelta(days=1)

>>> print(dt_add)
2020-11-01 12:00:00-08:00

>>> dt_add.tzname()
'PST'
```

​	这些时区还支持在 [**PEP 495**](https://peps.python.org/pep-0495/) 中引入的 [`fold`]({{< ref "/library/datatypes/datetime#datetime.datetime.fold" >}})。 在可能导致时间歧义的时差转换中（例如夏令时到标准时的转换），当 `fold=0` 时会使用转换 *之前* 的时差，而当 `fold=1` 时则使用转换 *之后* 的时差，例如:



``` python
>>> dt = datetime(2020, 11, 1, 1, tzinfo=ZoneInfo("America/Los_Angeles"))
>>> print(dt)
2020-11-01 01:00:00-07:00

>>> print(dt.replace(fold=1))
2020-11-01 01:00:00-08:00
```

​	当执行来自另一时区的转换时，fold 将被设置为正确的值:



``` python
>>> from datetime import timezone
>>> LOS_ANGELES = ZoneInfo("America/Los_Angeles")
>>> dt_utc = datetime(2020, 11, 1, 8, tzinfo=timezone.utc)

>>> # 在 PDT -> PST 转换之前
>>> print(dt_utc.astimezone(LOS_ANGELES))
2020-11-01 01:00:00-07:00

>>> # 在 PDT -> PST 转换之后
>>> print((dt_utc + timedelta(hours=1)).astimezone(LOS_ANGELES))
2020-11-01 01:00:00-08:00
```

## 数据源

`zoneinfo` 模块不直接提供时区数据，而是在可能的情况下从系统时区数据库或使用 PyPI 上的第一方包 [tzdata](https://pypi.org/project/tzdata/) 来获取时区信息。 某些系统，特别是 Windows 系统也包括在内，并没有可用的 IANA 数据库，因此对于要保证获取时区信息的跨平台兼容性的项目，推荐针对 tzdata 声明依赖。 如果系统数据和 tzdata 均不可用，则所有对 [`ZoneInfo`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.ZoneInfo" >}}) 的调用都将引发 [`ZoneInfoNotFoundError`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.ZoneInfoNotFoundError" >}})。



### 配置数据源

​	当 `ZoneInfo(key)` 被调用时，此构造器首先会在 [`TZPATH`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.TZPATH" >}}) 所指定的目录下搜索匹配 `key` 的文件，失败时则会在 tzdata 包中查找匹配。 此行为可通过三种方式来配置:

1. 默认的 [`TZPATH`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.TZPATH" >}}) 未通过其他方式指定时可在 [编译时]({{< ref "/library/datatypes/zoneinfo#zoneinfo-data-compile-time-config" >}}) 进行配置。
2. [`TZPATH`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.TZPATH" >}}) 可使用 [环境变量]({{< ref "/library/datatypes/zoneinfo#zoneinfo-data-environment-var" >}}) 进行配置。
3. 在 [运行时]({{< ref "/library/datatypes/zoneinfo#zoneinfo-data-runtime-config" >}})，搜索路径可使用 [`reset_tzpath()`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.reset_tzpath" >}}) 函数来修改。



#### 编译时配置

​	默认的 [`TZPATH`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.TZPATH" >}}) 包括一些时区数据库的通用部署位置（Windows 除外，该系统没有时区数据的“通用”位置）。 在 POSIX 系统中，下游分发者和从源码编译 Python 的开发者知道系统时区数据部署位置，它们可以通过指定编译时选项 `TZPATH` (或者更常见的是通过 [`配置旗标 --with-tzpath`]({{< ref "/using/configure#cmdoption-with-tzpath" >}})) 来改变默认的时区路径，该选项应当是一个由 [`os.pathsep`]({{< ref "/library/allos/os#os.pathsep" >}}) 分隔的字符串。

​	在所有平台上，配置值会在 [`sysconfig.get_config_var()`]({{< ref "/library/python/sysconfig#sysconfig.get_config_var" >}}) 中以 `TZPATH` 键的形式提供。



#### 环境配置

​	当初始化 [`TZPATH`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.TZPATH" >}}) 时（在导入时或不带参数调用 [`reset_tzpath()`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.reset_tzpath" >}}) 时），`zoneinfo` 模块将使用环境变量 `PYTHONTZPATH`，如果变量存在则会设置搜索路径。

## **PYTHONTZPATH**

​	这是一个以 [`os.pathsep`]({{< ref "/library/allos/os#os.pathsep" >}}) 分隔的字符串，其中包含要使用的时区搜索路径。 它必须仅由绝对路径而非相对路径组成。 在 `PYTHONTZPATH` 中指定的相对路径部分将不会被使用，但在其他情况下当指定相对路径时的行为该实现是有定义的；CPython 将引发 [`InvalidTZPathWarning`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.InvalidTZPathWarning" >}})，而其他实现可自由地忽略错误部分或是引发异常。

​	要设置让系统忽略系统数据并改用 tzdata 包，请设置 `PYTHONTZPATH=""`。



#### 运行时配置

​	TZ 搜索路径也可在运行时使用 [`reset_tzpath()`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.reset_tzpath" >}}) 函数来配置。 通常并不建议如此操作，不过在需要使用指定时区路径（或者需要禁止访问系统时区）的测试函数中使用它则是合理的。

## `ZoneInfo` 类

## *class* zoneinfo.**ZoneInfo**(*key*)

​	一个具体的 [`datetime.tzinfo`]({{< ref "/library/datatypes/datetime#datetime.tzinfo" >}}) 子类，它代表一个由字符串 `key` 所指定的 IANA 时区。 对主构造器的调用将总是返回可进行标识比较的对象；但是另一种方式，对所有的 `key` 值通过 [`ZoneInfo.clear_cache()`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.ZoneInfo.clear_cache" >}}) 禁止缓存失效，对以下断言将总是为真值:

```
a = ZoneInfo(key)
b = ZoneInfo(key)
assert a is b
```

`key` 必须采用相对的标准化 POSIX 路径的形式，其中没有对上一层级的引用。 如果传入了不合要求的键则构造器将引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

​	如果没有找到匹配 `key` 的文件，构造器将引发 [`ZoneInfoNotFoundError`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.ZoneInfoNotFoundError" >}})。

`ZoneInfo` 类具有两个替代构造器:

## *classmethod* ZoneInfo.**from_file**(*fobj*, */*, *key=None*)

​	基于一个返回字节串的文件型对象（例如一个以二进制模式打开的文件或是一个 [`io.BytesIO`]({{< ref "/library/allos/io#io.BytesIO" >}}) 对象）构造 `ZoneInfo` 对象。 不同于主构造器，此构造器总是会构造一个新对象。

`key` 形参设置时区名称以供 [`__str__()`]({{< ref "/reference/datamodel#object.__str__" >}}) 和 [`__repr__()`]({{< ref "/reference/datamodel#object.__repr__" >}}) 使用。

​	由此构造器创建的对象不可被封存 (参见 [pickling]({{< ref "/library/datatypes/zoneinfo#pickling" >}}))。

## *classmethod* ZoneInfo.**no_cache**(*key*)

​	一个绕过构造器缓存的替代构造器。 它与主构造器很相似，但每次调用都会返回一个新对象。 此构造器在进行测试或演示时最为适用，但它也可以被用来创建具有不同缓存失效策略的系统。

​	由此构造器创建的对象在被解封时也会绕过反序列化进程的缓存。

​	小心

 

​	使用此构造器可以会以令人惊讶的方式改变日期时间对象的语义，只有在你确定你的需求时才使用它。

​	也可以使用以下的类方法:

## *classmethod* ZoneInfo.**clear_cache**(***, *only_keys=None*)

​	一个可在 `ZoneInfo` 类上禁用缓存的方法。 如果不传入参数，则会禁用所有缓存并且下次对每个键调用主构造器将返回一个新实例。

​	如果将一个键名称的可迭代对象传给 `only_keys` 形参，则将只有指定的键会被从缓存中移除。 传给 `only_keys` 但在缓存中找不到的键会被忽略。

​	警告

 

​	发起调用此函数可能会以令人惊讶的方式改变使用 `ZoneInfo` 的日期时间对象的语义；这会修改模块的状态并因此可能产生大范围的影响。 你只有在确定有必要时才可以使用它。

​	该类具有一个属性:

## ZoneInfo.**key**

​	这是一个只读的 [attribute]({{< ref "/glossary/idx#term-attribute" >}})，它返回传给构造器的 `key` 的值，该值应为一个 IANA 时区数据库的查找键 (例如 `America/New_York`, `Europe/Paris` 或 `Asia/Tokyo`)。

​	对于不指定 `key` 形参而是基于文件构造时区，该属性将设为 `None`。

​备注
 

​	尽管将这些信息暴露给最终用户是一种比较普通的做法，但是这些值被设计作为代表相关时区的主键而不一定是面向用户的元素。 CLDR (Unicode 通用区域数据存储库) 之类的项目可被用来根据这些键获取更为用户友好的字符串。

### 字符串表示

​	当在 [`ZoneInfo`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.ZoneInfo" >}}) 对象上调用 [`str`]({{< ref "/library/stdtypes#str" >}}) 时返回的字符串表示默认会使用 [`ZoneInfo.key`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.ZoneInfo.key" >}}) 属性（参见该属性文档中的用法注释）:



``` python
>>> zone = ZoneInfo("Pacific/Kwajalein")
>>> str(zone)
'Pacific/Kwajalein'

>>> dt = datetime(2020, 4, 1, 3, 15, tzinfo=zone)
>>> f"{dt.isoformat()} [{dt.tzinfo}]"
'2020-04-01T03:15:00+12:00 [Pacific/Kwajalein]'
```

​	对于基于文件而非指定 `key` 形参所构建的对象，`str` 会回退为调用 [`repr()`]({{< ref "/library/functions#repr" >}})。 `ZoneInfo` 的 `repr` 是由具体实现定义的并且不一定会在不同版本间保持稳定，但它保证不会是一个有效的 `ZoneInfo` 键。



### 封存序列化

`ZoneInfo` 对象的序列化是基于键的，而不是序列化所有过渡数据，并且基于文件构造的 `ZoneInfo` 对象（即使是指定了 `key` 值的对象）不能被封存。

`ZoneInfo` 文件的行为取决于它的构造方式:

1. `ZoneInfo(key)`: 当使用主构造器构造时，会基于键序列化一个 `ZoneInfo` 对象，而当反序列化时，反序列化过程会使用主构造器，因此预期它们与其他对同一时区的引用会是同一对象。 例如，如果 `europe_berlin_pkl` 是一个包含基于 `ZoneInfo("Europe/Berlin")` 构建的封存数据的字符串，你可以预期出现以下的行为:

   

   ```
   >>> a = ZoneInfo("Europe/Berlin")
   >>> b = pickle.loads(europe_berlin_pkl)
   >>> a is b
   True
   ```

2. `ZoneInfo.no_cache(key)`: 当通过绕过缓存的构造器构造时，`ZoneInfo` 对象也会基于键序列化，但当反序列化时，反序列化过程会使用绕过缓存的构造器。 如果 `europe_berlin_pkl_nc` 是一个包含基于 `ZoneInfo.no_cache("Europe/Berlin")` 构造的封存数据的字符串，你可以预期出现以下的行为:

   

   ```
   >>> a = ZoneInfo("Europe/Berlin")
   >>> b = pickle.loads(europe_berlin_pkl_nc)
   >>> a is b
   False
   ```

3. `ZoneInfo.from_file(fobj, /, key=None)`: 当通过文件构造时，`ZoneInfo` 对象会在封存时引发异常。 如果最终用户想要封存通过文件构造的 `ZoneInfo`，则推荐他们使用包装类型或自定义序列化函数：或者基于键序列化，或者存储文件对象的内容并将其序列化。

​	该序列化方法要求所需键的时区数据在序列化和反序列化中均可用，类似于在序列化和反序列化环境中都预期存在对类和函数的引用的方式。 这还意味着在具有不同时区数据版本的环境中当解封被封存的 `ZoneInfo` 时并不会保证结果的一致性。

## 函数

## zoneinfo.**available_timezones**()

​	获取一个包含可用 IANA 时区的在时区路径的任何位置均可用的全部有效键的集合。 每次调用该函数时都会重新计算。

​	此函数仅包括规范时区名称而不包括“特殊”时区如位于 `posix/` 和 `right/` 目录下的时区或 `posixrules` 时区。

​	小心

 

​	此函数可能会打开大量的文件，因为确定时区路径上某个文件是否为有效时区的最佳方式是读取开头位置的“魔术字符串”。

​备注
 

​	这些值并不被设计用来对外公开给最终用户；对于面向用户的元素，应用程序应当使用 CLDR (Unicode 通用区域数据存储库) 之类来获取更为用户友好的字符串。 另请参阅 [`ZoneInfo.key`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.ZoneInfo.key" >}}) 中的提示性说明。

## zoneinfo.**reset_tzpath**(*to=None*)

​	设置或重置模块的时区搜索路径 ([`TZPATH`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.TZPATH" >}}))。 当不带参数调用时，[`TZPATH`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.TZPATH" >}}) 会被设为默认值。

​	调用 `reset_tzpath` 将不会使 [`ZoneInfo`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.ZoneInfo" >}}) 缓存失效，因而在缓存未命中的情况下对主 `ZoneInfo` 构造器的调用将只使用新的 `TZPATH`。

`to` 形参必须是由字符串或 [`os.PathLike`]({{< ref "/library/allos/os#os.PathLike" >}}) 组成的 [sequence]({{< ref "/glossary/idx#term-sequence" >}}) 或而不是字符串，它们必须都是绝对路径。 如果所传入的不是绝对路径则将引发 [`ValueError`]({{< ref "/library/exceptions#ValueError" >}})。

## 全局变量

## zoneinfo.**TZPATH**

​	一个表示时区搜索路径的只读序列 -- 当通过键构造 `ZoneInfo` 时，键会与 `TZPATH` 中的每个条目进行合并，并使用所找到的第一个文件。

`TZPATH` 可以只包含绝对路径，绝不包含相对路径，无论它是如何配置的。

`zoneinfo.TZPATH` 所指向的对象可能随着对 [`reset_tzpath()`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.reset_tzpath" >}}) 的调用而改变，因此推荐使用 `zoneinfo.TZPATH` 而不是从 `zoneinfo` 导入 `TZPATH` 或是将 `zoneinfo.TZPATH` 赋值给一个长期变量。

​	有关配置时区搜索路径的更多信息，请参阅 [配置数据源]({{< ref "/library/datatypes/zoneinfo#zoneinfo-data-configuration" >}})。

## 异常与警告

## *exception* zoneinfo.**ZoneInfoNotFoundError**

​	当一个 [`ZoneInfo`]({{< ref "/library/datatypes/zoneinfo#zoneinfo.ZoneInfo" >}}) 对象的构造由于在系统中找不到指定的键而失败时引发。 这是 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}}) 的一个子类。

## *exception* zoneinfo.**InvalidTZPathWarning**

​	当 [`PYTHONTZPATH`]({{< ref "/library/datatypes/zoneinfo#envvar-PYTHONTZPATH" >}}) 包含将被过滤掉的无效组件，例如一个相对路径时引发。
