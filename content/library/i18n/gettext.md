+++
title = "gettext --- 多语种国际化服务"
date = 2024-11-15T20:47:51+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/gettext.html](https://docs.python.org/zh-cn/3.13/library/gettext.html)
>
> 收录该文档的时间：`2024-11-15T20:47:51+08:00`

# `gettext` --- 多语种国际化服务

**源代码：** [Lib/gettext.py](https://github.com/python/cpython/tree/3.13/Lib/gettext.py)

------

[`gettext`](https://docs.python.org/zh-cn/3.13/library/gettext.html#module-gettext) 模块为 Python 模块和应用程序提供国际化 (Internationalization, I18N) 和本地化 (Localization, L10N) 服务。它同时支持 GNU **gettext** 消息编目 API 和更高级的、基于类的 API，后者可能更适合于 Python 文件。下方描述的接口允许用户使用一种自然语言编写模块和应用程序消息，并提供翻译后的消息编目，以便在不同的自然语言下运行。

​	同时还给出一些本地化 Python 模块及应用程序的小技巧。

## GNU **gettext** API

​	模块 [`gettext`](https://docs.python.org/zh-cn/3.13/library/gettext.html#module-gettext) 定义了下列 API，这与 **gettext** API 类似。如果你使用该 API，将会对整个应用程序产生全局的影响。如果你的应用程序支持多语种，而语言选择取决于用户的语言环境设置，这通常正是你所想要的。而如果你正在本地化某个 Python 模块，或者你的应用程序需要在运行时切换语言，相反你或许想用基于类的API。

## gettext.**bindtextdomain**(*domain*, *localedir=None*)

​	将 *domain* 绑定到本地目录 *localedir*。 更具体地来说，模块 [`gettext`](https://docs.python.org/zh-cn/3.13/library/gettext.html#module-gettext) 将使用路径 (在 Unix 系统中): `*localedir*/*language*/LC_MESSAGES/*domain*.mo` 查找二进制 `.mo` 文件，此处对应地查找 *language* 的位置是环境变量 `LANGUAGE`, `LC_ALL`, `LC_MESSAGES` 和 `LANG` 中。

​	如果遗漏了 *localedir* 或者设置为 `None`，那么将返回当前 *domain* 所绑定的值 [[1\]](https://docs.python.org/zh-cn/3.13/library/gettext.html#id3)

## gettext.**textdomain**(*domain=None*)

​	修改或查询当前的全局域。如果 *domain* 为 `None`，则返回当前的全局域，不为 `None` 则将全局域设置为 *domain*，并返回它。

## gettext.**gettext**(*message*)

​	返回 *message* 的本地化翻译，依据当前的全局域、语言和语言区域目录。 本函数在局部命名空间中通常包含别名 `_()` (参见下面的示例)。

## gettext.**dgettext**(*domain*, *message*)

​	与 [`gettext()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.gettext) 类似，但在指定的 *domain* 中查找 message。

## gettext.**ngettext**(*singular*, *plural*, *n*)

​	与 [`gettext()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.gettext) 类似，但考虑了复数形式。如果找到了翻译，则将 *n* 代入复数公式，然后返回得出的消息（某些语言具有两种以上的复数形式）。如果未找到翻译，则 *n* 为 1 时返回 *singular*，为其他数时返回 *plural*。

​	复数公式取自编目头文件。它是 C 或 Python 表达式，有一个自变量 *n*，该表达式计算的是所需复数形式在编目中的索引号。关于在 `.po` 文件中使用的确切语法和各种语言的公式，请参阅 [GNU gettext 文档](https://www.gnu.org/software/gettext/manual/gettext.html) 。

## gettext.**dngettext**(*domain*, *singular*, *plural*, *n*)

​	与 [`ngettext()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.ngettext) 类似，但在指定的 *domain* 中查找 message。

## gettext.**pgettext**(*context*, *message*)

## gettext.**dpgettext**(*domain*, *context*, *message*)

## gettext.**npgettext**(*context*, *singular*, *plural*, *n*)

## gettext.**dnpgettext**(*domain*, *context*, *singular*, *plural*, *n*)

​	与前缀中没有 `p` 的相应函数类似（即 [`gettext()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#module-gettext), [`dgettext()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.dgettext), [`ngettext()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.ngettext), [`dngettext()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.dngettext) ），但是仅翻译给定的 message *context*。

> Added in version 3.8.
>

​	请注意 GNU **gettext** 还定义了一个 `dcgettext()` 方法，但它被认为并不实用因此目前尚未实现它。

​	这是该 API 的典型用法示例:

```
import gettext
gettext.bindtextdomain('myapplication', '/path/to/my/language/directory')
gettext.textdomain('myapplication')
_ = gettext.gettext
# ...
print(_('This is a translatable string.'))
```

## 基于类的 API

​	与 GNU **gettext** API 相比，[`gettext`](https://docs.python.org/zh-cn/3.13/library/gettext.html#module-gettext) 模块的基于类的API 提供了更多的灵活性和便利性。 这是本地化 Python 应用程序和模块的推荐方式。 `gettext` 定义了一个 [`GNUTranslations`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.GNUTranslations) 类，它实现了对 GNU `.mo` 格式文件的解析，并且具有用于返回字符串的方法。 本类的实例也可以将自身作为函数 `_()` 安装到内置命名空间中。

## gettext.**find**(*domain*, *localedir=None*, *languages=None*, *all=False*)

​	本函数实现了标准的 `.mo` 文件搜索算法。它接受一个 *domain*，它与 [`textdomain()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.textdomain) 接受的域相同。可选参数 *localedir* 与 [`bindtextdomain()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.bindtextdomain) 中的相同。可选参数 *languages* 是多条字符串的列表，其中每条字符串都是一种语言代码。

​	如果没有传入 *localedir*，则使用默认的系统语言环境目录。 [[2\]](https://docs.python.org/zh-cn/3.13/library/gettext.html#id4) 如果没有传入 *languages*，则搜索以下环境变量：`LANGUAGE`、`LC_ALL`、`LC_MESSAGES` 和 `LANG`。从这些变量返回的第一个非空值将用作 *languages* 变量。环境变量应包含一个语言列表，由冒号分隔，该列表会被按冒号拆分，以产生所需的语言代码字符串列表。

[`find()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.find) 将扩展并规范化 language，然后遍历它们，搜索由这些组件构建的现有文件:

```
*localedir*/*language*/LC_MESSAGES/*domain*.mo
```

[`find()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.find) 返回找到类似的第一个文件名。如果找不到这样的文件，则返回 `None`。如果传入了 *all*，它将返回一个列表，包含所有文件名，并按它们在语言列表或环境变量中出现的顺序排列。

## gettext.**translation**(*domain*, *localedir=None*, *languages=None*, *class_=None*, *fallback=False*)

​	根据 *domain*, *localedir* 和 *languages* 返回一个 `*Translations` 实例，它们将首先被传给 [`find()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.find) 以获取由所关联的 `.mo` 文件路径组成的列表。 具有相同 `.mo` 文件名的实例会被缓存。 如果提供了 *class_* 则它将是被实例化的类，否则将是 [`GNUTranslations`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.GNUTranslations)。 该类的构造器必须接受一个 [file object](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-object) 参数。

​	如果找到多个文件，后找到的文件将用作先前文件的替补。为了设置替补，将使用 [`copy.copy()`](https://docs.python.org/zh-cn/3.13/library/copy.html#copy.copy) 从缓存中克隆每个 translation 对象。实际的实例数据仍在缓存中共享。

​	如果 `.mo` 文件未找到，且 *fallback* 为 false（默认值），则本函数引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 异常，如果 *fallback* 为 true，则返回一个 [`NullTranslations`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.NullTranslations) 实例。

*在 3.3 版本发生变更:* 过去触发的 [`IOError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#IOError)，现在是 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 的别名。

*在 3.11 版本发生变更:* *codeset* 形参已被移除。

## gettext.**install**(*domain*, *localedir=None*, ***, *names=None*)

​	这将在 Python 的内置命名空间中安装 `_()` 函数，基于传给 [`translation()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.translation) 函数的 *domain* 和 *localedir*。

*names* 参数的信息请参阅 translation 对象的 [`install()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.NullTranslations.install) 方法的描述。

​	如下所示，通常是将字符串包裹在对 `_()` 函数的调用中，以标记应用程序中待翻译的字符串，就像这样:

```
print(_('This string will be translated.'))
```

​	为了方便，可将 `_()` 函数安装在 Python 的内置命名空间中，这样就可以在应用程序的所有模块中轻松地访问它。

*在 3.11 版本发生变更:* *names* 现在是仅限关键字形参。

### [`NullTranslations`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.NullTranslations) 类

​	translation 类实际实现的是，将原始源文件消息字符串转换为已翻译的消息字符串。所有 translation 类使用的基类为 [`NullTranslations`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.NullTranslations)，它提供了基本的接口，可用于编写自己定制的 translation 类。以下是 `NullTranslations` 的方法：

## *class* gettext.**NullTranslations**(*fp=None*)

​	接受一个可选参数 [文件对象](https://docs.python.org/zh-cn/3.13/glossary.html#term-file-object) *fp*，该参数会被基类忽略。初始化由派生类设置的 "protected" （受保护的）实例变量 *_info* 和 *_charset*，与 *_fallback* 类似，但它是通过 [`add_fallback()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.NullTranslations.add_fallback) 来设置的。如果 *fp* 不为 `None`，就会调用 `self._parse(fp)`。

## **_parse**(*fp*)

​	在基类中没有操作，本方法接受文件对象 *fp*，从该文件读取数据，用来初始化消息编目。如果你手头的消息编目文件的格式不受支持，则应重写本方法来解析你的格式。

## **add_fallback**(*fallback*)

​	添加 *fallback* 为当前 translation 对象的替补对象。如果 translation 对象无法为指定消息提供翻译，则应向替补查询。

## **gettext**(*message*)

​	如果设置了替补，则转发 `gettext()` 给替补。否则返回 *message*。在派生类中被重写。

## **ngettext**(*singular*, *plural*, *n*)

​	如果设置了替补，则转发 `ngettext()` 给替补。否则，*n* 为 1 时返回 *singular*，为其他时返回 *plural*。在派生类中被重写。

## **pgettext**(*context*, *message*)

​	如果设置了替补，则转发 [`pgettext()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.pgettext) 给替补。否则返回已翻译的消息。在派生类中被重写。

> Added in version 3.8.
>

## **npgettext**(*context*, *singular*, *plural*, *n*)

​	如果设置了替补，则转发 [`npgettext()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.npgettext) 给替补。否则返回已翻译的消息。在派生类中被重写。

> Added in version 3.8.
>

## **info**()

​	返回一个包含在消息编目文件中找到的元数据的字典。

## **charset**()

​	返回消息编目文件的编码。

## **install**(*names=None*)

​	本方法将 [`gettext()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.NullTranslations.gettext) 安装至内建命名空间，并绑定为 `_`。

​	如果给出了 *names* 形参，则它必须是一个包含除 `_()` 外需要在内置命名空间中安装的函数的名称的序列。 受支持的名称有 `'gettext'`, `'ngettext'`, `'pgettext'` 和 `'npgettext'`。

​	请注意这只是将 `_()` 函数提供给应用程序的一种方式，尽管也是最方便的方式。 由于它会全局性地影响整个应用程序，特别是内置命名空间，因此本地化的模块绝不应安装 `_()`。 作为替代，它们应使用以下代码使 `_()` 可用于它们的模块:

```
import gettext
t = gettext.translation('mymodule', ...)
_ = t.gettext
```

​	这样只把 `_()` 放在模块的全局命名空间中所以只会影响该模块内的调用。

*在 3.8 版本发生变更:* 添加了 `'pgettext'` 和 `'npgettext'`。

### [`GNUTranslations`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.GNUTranslations) 类

`gettext` 模块提供了一个派生自 [`NullTranslations`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.NullTranslations) 的附加类: [`GNUTranslations`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.GNUTranslations)。 该类重写了 `_parse()` 以同时支持以大端序和小端序格式读取 GNU **gettext** 格式的 `.mo` 文件。

[`GNUTranslations`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.GNUTranslations) 会从翻译编目中解析可选的元数据。 根据惯例 GNU **gettext** 会以空字符串翻译的形式包括元数据。 该元数据使用 [**RFC 822**](https://datatracker.ietf.org/doc/html/rfc822.html) 风格的 `key: value` 对，并且应当包含 `Project-Id-Version` 键。 如果找到了 `Content-Type` 键，则将使用 `charset` 属性来初始化 "protected" `_charset` 实例变量，如未找到则默认为 `None`。 如果指定了 charset 编码格式，则从编目中读取的所有消息 ID 和消息字符串都将使用该编码格式转换为 Unicode，否则会设定使用 ASCII。

​	由于消息 ID 也是以 Unicode 字符串的形式读取的，因此所有 `*gettext()` 方法都会假定消息 ID 为 Unicode字符串，而不是字节串。

​	整个键/值对集合将被放入一个字典并设置为 "protected" `_info` 实例变量。

​	如果 `.mo` 文件的魔法值 (magic number) 无效，或遇到意外的主版本号，或在读取文件时发生其他问题，则实例化 [`GNUTranslations`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.GNUTranslations) 类会引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError)。

## *class* gettext.**GNUTranslations**

​	下列方法是根据基类实现重写的：

## **gettext**(*message*)

​	在编目中查找 *message* ID，并以 Unicode 字符串形式返回相应的消息字符串。如果在编目中没有 *message* ID 条目，且配置了替补，则查找请求将被转发到替补的 [`gettext()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.NullTranslations.gettext) 方法。否则，返回 *message* ID。

## **ngettext**(*singular*, *plural*, *n*)

​	查找消息 ID 的复数形式。*singular* 用作消息 ID，用于在编目中查找，同时 *n* 用于确定使用哪种复数形式。返回的消息字符串是 Unicode 字符串。

​	如果在编目中没有找到消息 ID，且配置了替补，则查找请求将被转发到替补的 [`ngettext()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.NullTranslations.ngettext) 方法。否则，当 *n* 为 1 时返回 *singular*，其他情况返回 *plural*。

​	例如：

```
n = len(os.listdir('.'))
cat = GNUTranslations(somefile)
message = cat.ngettext(
    'There is %(num)d file in this directory',
    'There are %(num)d files in this directory',
    n) % {'num': n}
```

## **pgettext**(*context*, *message*)

​	在编目中查找 *context* 和 *message* ID，并以 Unicode 字符串形式返回相应的消息字符串。如果在编目中没有 *message* ID 和 *context* 条目，且配置了替补，则查找请求将被转发到替补的 [`pgettext()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.pgettext) 方法。否则，返回 *message* ID。

> Added in version 3.8.
>

## **npgettext**(*context*, *singular*, *plural*, *n*)

​	查找消息 ID 的复数形式。*singular* 用作消息 ID，用于在编目中查找，同时 *n* 用于确定使用哪种复数形式。

​	如果在编目中没有找到 *context* 对应的消息 ID，且配置了替补，则查找请求将被转发到替补的 [`npgettext()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.npgettext) 方法。否则，当 *n* 为 1 时返回 *singular*，其他情况返回 *plural*。

> Added in version 3.8.
>

### Solaris 消息编目支持

​	Solaris 操作系统定义了自己的二进制 `.mo` 文件格式，但由于找不到该格式的文档，因此目前不支持该格式。

### 编目构造器

​	GNOME 用的 [`gettext`](https://docs.python.org/zh-cn/3.13/library/gettext.html#module-gettext) 模块是 James Henstridge 写的版本，但该版本的 API 略有不同。它文档中的用法是:

```
import gettext
cat = gettext.Catalog(domain, localedir)
_ = cat.gettext
print(_('hello world'))
```

​	为了与此模块的旧版本兼容，函数 `Catalog()` 是上述 [`translation()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.translation) 函数的别名。

​	本模块与 Henstridge 的模块有一个区别：他的编目对象支持通过映射 API 进行访问，但是该特性似乎从未使用过，因此目前不支持该特性。



## 国际化 (I18N) 你的程序和模块

​	国际化 (I18N) 是指使程序可切换多种语言的操作。本地化 (L10N) 是指程序的适配能力，一旦程序被国际化，就能适配当地的语言和文化习惯。为了向 Python 程序提供不同语言的消息，需要执行以下步骤：

1. 准备程序或模块，将可翻译的字符串特别标记起来
2. 在已标记的文件上运行一套工具，用来生成原始消息编目
3. 创建消息编目的不同语言的翻译
4. 使用 [`gettext`](https://docs.python.org/zh-cn/3.13/library/gettext.html#module-gettext) 模块，以便正确翻译消息字符串

​	为了准备代码以实现 I18N，你需要查看文件中的所有字符串。 任何需要翻译的字符串都应在 `_('...')` 中包含它来进行标记 --- 即调用函数 [`_`](https://docs.python.org/zh-cn/3.13/library/gettext.html#module-gettext)。 例如:

```
filename = 'mylog.txt'
message = _('writing a log message')
with open(filename, 'w') as fp:
    fp.write(message)
```

​	在这个例子中，字符串 `'writing a log message'` 被标记为待翻译，而字符串 `'mylog.txt'` 和 `'w'` 没有被标记。

​	有一些工具可以将待翻译的字符串提取出来。 原版的 GNU **gettext** 仅支持 C 或 C++ 源代码，但其扩展版 **xgettext** 可以扫描多种语言的代码，包括 Python 在内，来找出标记为可翻译的字符串。 [Babel](https://babel.pocoo.org/) 是一个包括了可用于提取并编译消息编目的 `pybabel` 脚本的 Python 国际化库。 François Pinard 的 **xpot** 程序也能完成类似的工作并可在他的 [po-utils 包](https://github.com/pinard/po-utils) 中获取。

​	（Python 还包括了这些程序的纯 Python 版本，称为 **pygettext.py** 和 **msgfmt.py**，某些 Python 发行版已经安装了它们。**pygettext.py** 类似于 **xgettext**，但只能理解 Python 源代码，无法处理诸如 C 或 C++ 的其他编程语言。**pygettext.py** 支持的命令行界面类似于 **xgettext**，查看其详细用法请运行 `pygettext.py --help`。**msgfmt.py** 与 GNU **msgfmt** 是二进制兼容的。有了这两个程序，可以不需要 GNU **gettext** 包来国际化 Python 应用程序。）

**xgettext**、**pygettext** 或类似工具生成的 `.po` 文件就是消息编目。它们是结构化的人类可读文件，包含源代码中所有被标记的字符串，以及这些字符串的翻译的占位符。

​	然后把这些 `.po` 文件的副本交给各个人工译者，他们为所支持的每种自然语言编写翻译。译者以 `<语言名称>.po` 文件的形式发送回翻译完的某个语言的版本，将该文件用 **msgfmt** 程序编译为机器可读的 `.mo` 二进制编目文件。[`gettext`](https://docs.python.org/zh-cn/3.13/library/gettext.html#module-gettext) 模块使用 `.mo` 文件在运行时进行实际的翻译处理。

​	如何在代码中使用 [`gettext`](https://docs.python.org/zh-cn/3.13/library/gettext.html#module-gettext) 模块取决于国际化单个模块还是整个应用程序。接下来的两节将讨论每种情况。

### 本地化你的模块

​	如果要本地化模块，则切忌进行全局性的更改，如更改内建命名空间。不应使用 GNU **gettext** API，而应使用基于类的 API。

​	假设你的模块叫做 "spam"，并且该模块的各种自然语言翻译 `.mo` 文件存放于 `/usr/share/locale`，为 GNU **gettext** 格式。以下内容应放在模块顶部:

```
import gettext
t = gettext.translation('spam', '/usr/share/locale')
_ = t.gettext
```

### 本地化你的应用程序

​	如果你正在本地化你的应用程序，你可以将 `_()` 函数全局安装到内置命名空间中，通常位于应用程序的主驱动文件内。 这样将让你的应用程序专属的所有文件都可以使用 `_('...')` 而无需在每个文件中显示安装它。

​	最简单的情况，就只需将以下代码添加到应用程序的主程序文件中:

```
import gettext
gettext.install('myapplication')
```

​	如果需要设置语言环境目录，可以将其传递给 [`install()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.install) 函数:

```
import gettext
gettext.install('myapplication', '/usr/share/locale')
```

### 即时更改语言

​	如果程序需要同时支持多种语言，则可能需要创建多个翻译实例，然后在它们之间进行显式切换，如下所示:

```
import gettext

lang1 = gettext.translation('myapplication', languages=['en'])
lang2 = gettext.translation('myapplication', languages=['fr'])
lang3 = gettext.translation('myapplication', languages=['de'])

# 从使用语言 1 开始
lang1.install()

# ... 过一段时间后，用户选择了语言 2
lang2.install()

# ... 再过一段时间后，用户选择了语言 3
lang3.install()
```

### 延迟翻译

​	在大多数代码中，字符串会在编写位置进行翻译。但偶尔需要将字符串标记为待翻译，实际翻译却推迟到后面。一个典型的例子是:

```
animals = ['mollusk',
           'albatross',
           'rat',
           'penguin',
           'python', ]
# ...
for a in animals:
    print(a)
```

​	此处希望将 `animals` 列表中的字符串标记为可翻译，但不希望在打印之前对它们进行翻译。

​	这是处理该情况的一种方式:

```
def _(message): return message

animals = [_('mollusk'),
           _('albatross'),
           _('rat'),
           _('penguin'),
           _('python'), ]

del _

# ...
for a in animals:
    print(_(a))
```

​	这样做是因为 `_()` 的虚定义只是简单地原样返回字符串。 并且这个虚定义将临时覆盖内置命名空间中任何的 `_()` 定义（直到 [`del`](https://docs.python.org/zh-cn/3.13/reference/simple_stmts.html#del) 命令）。 但是如果之前你在局部命名空间中已有 `_()` 的定义，则需要特别注意。

​	请注意在第二次使用 `_()` 时将不会认为“a”可以由 **gettext** 程序去翻译，因为该形参不是字符串字面值。

​	解决该问题的另一种方法是下面这个例子:

```
def N_(message): return message

animals = [N_('mollusk'),
           N_('albatross'),
           N_('rat'),
           N_('penguin'),
           N_('python'), ]

# ...
for a in animals:
    print(_(a))
```

​	在这种情况下，你用函数 `N_()` 来标记可翻译的字符串，它与 `_()` 的任何定义都不会冲突。 不过，你需要让你的消息提取程序寻找用 `N_()` 标记的可翻译字符串。 **xgettext**, **pygettext**, `pybabel extract` 和 **xpot** 都通过使用 `-k` 命令行开关来支持此功能。 这里选择用 `N_()` 完全是任意的；它也可以简单地改为 `MarkThisStringForTranslation()`。

## 致谢

​	以下人员为创建此模块贡献了代码、反馈、设计建议、早期实现和宝贵的经验：

- Peter Funk
- James Henstridge
- Juan David Ibáñez Palomar
- Marc-André Lemburg
- Martin von Löwis
- François Pinard
- Barry Warsaw
- Gustavo Niemeyer

​	备注

[[1](https://docs.python.org/zh-cn/3.13/library/gettext.html#id1)]

​	默认的语言区域目录取决于具体系统；例如，在 Red Hat Linux 上为 `/usr/share/locale`，但在 Solaris 上则为 `/usr/lib/locale`。 `gettext` 模块没有试图支持这些依赖于系统的默认值；而是默认设为 `*sys.base_prefix*/share/locale` (参见 [`sys.base_prefix`](https://docs.python.org/zh-cn/3.13/library/sys.html#sys.base_prefix))。 基于上述原因，最好每次都在程序启动时调用 [`bindtextdomain()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.bindtextdomain) 并附带一个显式的绝对路径。

[[2](https://docs.python.org/zh-cn/3.13/library/gettext.html#id2)]

​	参阅上方 [`bindtextdomain()`](https://docs.python.org/zh-cn/3.13/library/gettext.html#gettext.bindtextdomain) 的脚注。
