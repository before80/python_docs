+++
title = "textwrap --- 文本自动换行与填充"
date = 2024-11-15T11:04:21+08:00
weight = 30
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/textwrap.html](https://docs.python.org/zh-cn/3.13/library/textwrap.html)
>
> 收录该文档的时间：`2024-11-15T11:04:21+08:00`

# `textwrap` --- 文本自动换行与填充

**源代码:** [Lib/textwrap.py](https://github.com/python/cpython/tree/3.13/Lib/textwrap.py)

------

[`textwrap`]({{< ref "/library/text/textwrap#module-textwrap" >}}) 模块提供了一些快捷函数，以及可以完成所有工作的类 [`TextWrapper`]({{< ref "/library/text/textwrap#textwrap.TextWrapper" >}})。 如果你只是要对一两个文本字符串进行自动换行或填充，快捷函数应该就够用了；否则的话，你应该使用 [`TextWrapper`]({{< ref "/library/text/textwrap#textwrap.TextWrapper" >}}) 的实例来提高效率。

## textwrap.**wrap**(*text*, *width=70*, *, *initial_indent=''*, *subsequent_indent=''*, *expand_tabs=True*, *replace_whitespace=True*, *fix_sentence_endings=False*, *break_long_words=True*, *drop_whitespace=True*, *break_on_hyphens=True*, *tabsize=8*, *max_lines=None*, *placeholder=' [...]'*)

​	对 *text* (字符串) 中的单独段落自动换行以使每行长度最多为 *width* 个字符。 返回由输出行组成的列表，行尾不带换行符。

​	与 [`TextWrapper`]({{< ref "/library/text/textwrap#textwrap.TextWrapper" >}}) 的实例属性对应的可选的关键字参数，具体文档见下。

​	请参阅 [`TextWrapper.wrap()`]({{< ref "/library/text/textwrap#textwrap.TextWrapper.wrap" >}}) 方法了解有关 [`wrap()`]({{< ref "/library/text/textwrap#textwrap.wrap" >}}) 行为的详细信息。

## textwrap.**fill**(*text*, *width=70*, *, *initial_indent=''*, *subsequent_indent=''*, *expand_tabs=True*, *replace_whitespace=True*, *fix_sentence_endings=False*, *break_long_words=True*, *drop_whitespace=True*, *break_on_hyphens=True*, *tabsize=8*, *max_lines=None*, *placeholder=' [...]'*)

​	对 *text* 中的单独段落自动换行，并返回一个包含被自动换行段落的单独字符串。 [`fill()`]({{< ref "/library/text/textwrap#textwrap.fill" >}}) 是以下语句的快捷方式

```
"\n".join(wrap(text, ...))
```

​	特别要说明的是，[`fill()`]({{< ref "/library/text/textwrap#textwrap.fill" >}}) 接受与 [`wrap()`]({{< ref "/library/text/textwrap#textwrap.wrap" >}}) 完全相同的关键字参数。

## textwrap.**shorten**(*text*, *width*, *, *fix_sentence_endings=False*, *break_long_words=True*, *break_on_hyphens=True*, *placeholder=' [...]'*)

​	折叠并截短给定的 *text* 以符合给定的 *width*。

​	首先 *text* 中的空格会被折叠（所有连续会替换为单个空格）。 如果结果能适合 *width*，它将被返回。 在其他情况下，将在末尾丢弃足够数量的单词以使剩余的单词加 *placeholder* 能适合 *width*:



``` python
>>> textwrap.shorten("Hello  world!", width=12)
'Hello world!'
>>> textwrap.shorten("Hello  world!", width=11)
'Hello [...]'
>>> textwrap.shorten("Hello world", width=10, placeholder="...")
'Hello...'
```

​	可选的关键字参数对应于 [`TextWrapper`]({{< ref "/library/text/textwrap#textwrap.TextWrapper" >}}) 的实际属性，具体见下文。 请注意文本在被传入 [`TextWrapper`]({{< ref "/library/text/textwrap#textwrap.TextWrapper" >}}) 的 [`fill()`]({{< ref "/library/text/textwrap#textwrap.fill" >}}) 函数之前会被折叠，因此改变 [`tabsize`]({{< ref "/library/text/textwrap#textwrap.TextWrapper.tabsize" >}}), [`expand_tabs`]({{< ref "/library/text/textwrap#textwrap.TextWrapper.expand_tabs" >}}), [`drop_whitespace`]({{< ref "/library/text/textwrap#textwrap.TextWrapper.drop_whitespace" >}}) 和 [`replace_whitespace`]({{< ref "/library/text/textwrap#textwrap.TextWrapper.replace_whitespace" >}}) 的值将没有任何效果。

> Added in version 3.4.
>

## textwrap.**dedent**(*text*)

​	移除 *text* 中每一行的任何相同前缀空白符。

​	这可以用来清除三重引号字符串行左侧空格，而仍然在源码中显示为缩进格式。

​	请注意制表符和空格符都被视为是空白符，但它们并不相等：以下两行 `" hello"` 和 `"\thello"` 不会被视为具有相同的前缀空白符。

​	只包含空白符的行会在输入时被忽略并在输出时被标准化为单个换行符。

​	例如:

```
def test():
    # 第一行以 \ 结束以避免出现空行！
    s = '''\
    hello
      world
    '''
    print(repr(s))          # prints '    hello\n      world\n    '
    print(repr(dedent(s)))  # prints 'hello\n  world\n'
```

## textwrap.**indent**(*text*, *prefix*, *predicate=None*)

​	将 *prefix* 添加到 *text* 中选定行的开头。

​	通过调用 `text.splitlines(True)` 来对行进行拆分。

​	默认情况下，*prefix* 会被添加到所有不是只由空白符（包括任何行结束符）组成的行。

​	例如:



``` python
>>> s = 'hello\n\n \nworld'
>>> indent(s, '  ')
'  hello\n\n \n  world'
```

​	可选的 *predicate* 参数可用来控制哪些行要缩进。 例如，可以很容易地为空行或只有空白符的行添加 *prefix*:



``` python
>>> print(indent(s, '+ ', lambda line: True))
+ hello
+
+
+ world
```

> Added in version 3.3.
>

[`wrap()`]({{< ref "/library/text/textwrap#textwrap.wrap" >}}), [`fill()`]({{< ref "/library/text/textwrap#textwrap.fill" >}}) 和 [`shorten()`]({{< ref "/library/text/textwrap#textwrap.shorten" >}}) 的作用方式为创建一个 [`TextWrapper`]({{< ref "/library/text/textwrap#textwrap.TextWrapper" >}}) 实例并在其上调用单个方法。 该实例不会被重用，因此对于要使用 [`wrap()`]({{< ref "/library/text/textwrap#textwrap.wrap" >}}) 和/或 [`fill()`]({{< ref "/library/text/textwrap#textwrap.fill" >}}) 来处理许多文本字符串的应用来说，创建你自己的 [`TextWrapper`]({{< ref "/library/text/textwrap#textwrap.TextWrapper" >}}) 对象可能会更有效率。

​	文本最好在空白符位置自动换行，包括带连字符单词的连字符之后；长单词仅在必要时会被拆分，除非 [`TextWrapper.break_long_words`]({{< ref "/library/text/textwrap#textwrap.TextWrapper.break_long_words" >}}) 被设为假值。

## *class* textwrap.**TextWrapper**(***kwargs*)

[`TextWrapper`]({{< ref "/library/text/textwrap#textwrap.TextWrapper" >}}) 构造器接受多个可选的关键字参数。 每个关键字参数对应一个实例属性，比如说

```
wrapper = TextWrapper(initial_indent="* ")
```

​	相当于：

```
wrapper = TextWrapper()
wrapper.initial_indent = "* "
```

​	你可以多次重用相同的 [`TextWrapper`]({{< ref "/library/text/textwrap#textwrap.TextWrapper" >}}) 对象，并且你也可以在使用期间通过直接向实例属性赋值来修改它的任何选项。

[`TextWrapper`]({{< ref "/library/text/textwrap#textwrap.TextWrapper" >}}) 的实例属性（以及构造器的关键字参数）如下所示：

## **width**

​	(默认: `70`) 自动换行的最大行长度。 只要输入文本中没有长于 [`width`]({{< ref "/library/text/textwrap#textwrap.TextWrapper.width" >}}) 的单个单词，[`TextWrapper`]({{< ref "/library/text/textwrap#textwrap.TextWrapper" >}}) 就能保证没有长于 [`width`]({{< ref "/library/text/textwrap#textwrap.TextWrapper.width" >}}) 个字符的输出行。

## **expand_tabs**

​	(默认值: `True`) 如果为真值，则 *text* 中的所有制表符将使用 *text* 的 [`expandtabs()`]({{< ref "/library/stdtypes#str.expandtabs" >}}) 方法扩展为空格符。

## **tabsize**

​	(默认: `8`) 如果 [`expand_tabs`]({{< ref "/library/text/textwrap#textwrap.TextWrapper.expand_tabs" >}}) 为真值，则 *text* 中所有的制表符将扩展为零个或多个空格，具体取决于当前列位置和给定的制表宽度。

> Added in version 3.3.
>

## **replace_whitespace**

​	(default: `True`) 如果为真值，在制表符扩展之后、自动换行之前，[`wrap()`]({{< ref "/library/text/textwrap#textwrap.wrap" >}}) 方法将把每个空白字符都替换为单个空格。 会被替换的空白字符如下：制表，换行，垂直制表，进纸和回车 (`'\t\n\v\f\r'`)。

​备注
 

​	如果 [`expand_tabs`]({{< ref "/library/text/textwrap#textwrap.TextWrapper.expand_tabs" >}}) 为假值且 [`replace_whitespace`]({{< ref "/library/text/textwrap#textwrap.TextWrapper.replace_whitespace" >}}) 为真值，每个制表符将被替换为单个空格，这与制表符扩展是 *不* 一样的。

​备注
 

​	如果 [`replace_whitespace`]({{< ref "/library/text/textwrap#textwrap.TextWrapper.replace_whitespace" >}}) 为假值，在一行的中间有可能出现换行符并导致怪异的输出。 因此，文本应当（使用 [`str.splitlines()`]({{< ref "/library/stdtypes#str.splitlines" >}}) 或类似方法）拆分为段落并分别进行自动换行。

## **drop_whitespace**

​	(默认: `True`) 如果为真值，每一行开头和末尾的空白字符（在包装之后、缩进之前）会被丢弃。 但是段落开头的空白字符如果后面不带任何非空白字符则不会被丢弃。 如果被丢弃的空白字符占据了一个整行，则该整行将被丢弃。

## **initial_indent**

​	(默认: `''`) 将被添加到被自动换行输出内容的第一行的字符串。 其长度会被计入第一行的长度。 空字符串不会被缩进。

## **subsequent_indent**

​	(default: `''`) 将被添加到被自动换行输出内容除第一行外的所有行的字符串。 其长度会被计入除行一行外的所有行的长度。

## **fix_sentence_endings**

​	(默认: `False`) 如果为真值，[`TextWrapper`]({{< ref "/library/text/textwrap#textwrap.TextWrapper" >}}) 将尝试检测句子结尾并确保句子间总是以恰好两个空格符分隔。 对于使用等宽字体的文本来说通常都需要这样。 但是句子检测算法并不完美：它假定句子结尾是一个小写字母加字符 `'.'`, `'!'` 或 `'?'` 之一，并可能跟一个 `'"'` 或 `"'"`，再跟一个空格。 此算法的一个问题是它无法区分以下文本中的 "Dr."

```
[...] Dr. Frankenstein's monster [...]
```

​	和以下文本中的 "Spot."

```
[...] See Spot. See Spot run [...]
```

[`fix_sentence_endings`]({{< ref "/library/text/textwrap#textwrap.TextWrapper.fix_sentence_endings" >}}) 默认为假值。

​	由于句子检测算法依赖于 `string.lowercase` 来确定“小写字母”，以及约定在句点后使用两个空格来分隔处于同一行的句子，因此只适用于英语文本。

## **break_long_words**

​	(默认: `True`) 如果为真值，则长度超过 [`width`]({{< ref "/library/text/textwrap#textwrap.TextWrapper.width" >}}) 的单词将被分开以保证行的长度不会超过 [`width`]({{< ref "/library/text/textwrap#textwrap.TextWrapper.width" >}})。 如果为假值，超长单词不会被分开，因而某些行的长度可能会超过 [`width`]({{< ref "/library/text/textwrap#textwrap.TextWrapper.width" >}})。 （超长单词将被单独作为一行，以尽量减少超出 [`width`]({{< ref "/library/text/textwrap#textwrap.TextWrapper.width" >}}) 的情况。）

## **break_on_hyphens**

​	(默认: `True`) 如果为真值，将根据英语的惯例首选在空白符和复合词的连字符之后自动换行。 如果为假值，则只有空白符会被视为合适的潜在断行位置，但如果你确实不希望出现分开的单词则你必须将 [`break_long_words`]({{< ref "/library/text/textwrap#textwrap.TextWrapper.break_long_words" >}}) 设为假值。 之前版本的默认行为总是允许分开带有连字符的单词。

## **max_lines**

​	(默认: `None`) 如果不为 `None`，则输出内容将最多包含 *max_lines* 行，并使 *placeholder* 出现在输出内容的末尾。

> Added in version 3.4.
>

## **placeholder**

​	(默认: `' [...]'`) 该文本将在输出文本被截短时出现在文本末尾。

> Added in version 3.4.
>

[`TextWrapper`]({{< ref "/library/text/textwrap#textwrap.TextWrapper" >}}) 还提供了一些公有方法，类似于模块层级的便捷函数：

## **wrap**(*text*)

​	对 *text* (字符串) 中的单独段落自动换行以使每行长度最多为 [`width`]({{< ref "/library/text/textwrap#textwrap.TextWrapper.width" >}}) 个字符。 所有自动换行选项均获取自 [`TextWrapper`]({{< ref "/library/text/textwrap#textwrap.TextWrapper" >}}) 实例的实例属性。 返回由输出行组成的列表，行尾不带换行符。 如果自动换行输出结果没有任何内容，则返回空列表。

## **fill**(*text*)

​	对 *text* 中的单独段落自动换行并返回包含被自动换行段落的单独字符串。
