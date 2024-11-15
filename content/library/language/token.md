+++
title = "token --- 用于 Python 解析树的常量"
date = 2024-11-15T21:23:55+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/token.html](https://docs.python.org/zh-cn/3.13/library/token.html)
>
> 收录该文档的时间：`2024-11-15T21:23:55+08:00`

# `token` --- 用于 Python 解析树的常量

**源码：** [Lib/token.py](https://github.com/python/cpython/tree/3.13/Lib/token.py)

------

​	该模块提供了一些代表解析树的叶子节点的数字值的常量（终端形符）。 请参阅 Python 发布版中的 `Grammar/Tokens` 文件获取在该语言语法情境下的名称定义。 这些名称所映射的特定数字值有可能在各 Python 版本间发生变化。

​	该模块还提供从数字代码到名称和一些函数的映射。 这些函数镜像了 Python C 头文件中的定义。

## token.**tok_name**

​	将此模块中定义的常量的数值映射回名称字符串的字典，允许生成更加人类可读的解析树表示。

## token.**ISTERMINAL**(*x*)

​	对终端形符值返回 `True`。

## token.**ISNONTERMINAL**(*x*)

​	对非终端形符值返回 `True`。

## token.**ISEOF**(*x*)

​	如果 *x* 是表示输入结束的标记则返回 `True`。

​	形符常量有：

## token.**ENDMARKER**

## token.**NAME**

## token.**NUMBER**

## token.**STRING**

## token.**NEWLINE**

## token.**INDENT**

## token.**DEDENT**

## token.**LPAR**

`"("` 的形符值。

## token.**RPAR**

`")"` 的形符值。

## token.**LSQB**

`"["` 的形符值。

## token.**RSQB**

`"]"` 的形符值。

## token.**COLON**

`":"` 的形符值。

## token.**COMMA**

`","` 的形符值。

## token.**SEMI**

`";"` 的形符值。

## token.**PLUS**

`"+"` 的形符值。

## token.**MINUS**

`"-"` 的形符值。

## token.**STAR**

`"*"` 的形符值。

## token.**SLASH**

`"/"` 的形符值。

## token.**VBAR**

`"|"` 的形符值。

## token.**AMPER**

`"&"` 的形符值。

## token.**LESS**

`"<"` 的形符值。

## token.**GREATER**

`">"` 的形符值。

## token.**EQUAL**

`"="` 的形符值。

## token.**DOT**

`"."` 的形符值。

## token.**PERCENT**

`"%"` 的形符值。

## token.**LBRACE**

​	Token value for `"{"`.

## token.**RBRACE**

`"}"` 的形符值。

## token.**EQEQUAL**

`"=="` 的形符值。

## token.**NOTEQUAL**

`"!="` 的形符值。

## token.**LESSEQUAL**

`"<="` 的形符值。

## token.**GREATEREQUAL**

`">="` 的形符值。

## token.**TILDE**

`"~"` 的形符值。

## token.**CIRCUMFLEX**

`"^"` 的形符值。

## token.**LEFTSHIFT**

`"<<"` 的形符值。

## token.**RIGHTSHIFT**

`">>"` 的形符值。

## token.**DOUBLESTAR**

`"**"` 的形符值。

## token.**PLUSEQUAL**

`"+="` 的形符值。

## token.**MINEQUAL**

`"-="` 的形符值。

## token.**STAREQUAL**

`"*="` 的形符值。

## token.**SLASHEQUAL**

`"/="` 的形符值。

## token.**PERCENTEQUAL**

`"%="` 的形符值。

## token.**AMPEREQUAL**

`"&="` 的形符值。

## token.**VBAREQUAL**

`"|="` 的形符值。

## token.**CIRCUMFLEXEQUAL**

`"^="` 的形符值。

## token.**LEFTSHIFTEQUAL**

`"<<="` 的形符值。

## token.**RIGHTSHIFTEQUAL**

`">>="` 的形符值。

## token.**DOUBLESTAREQUAL**

`"**="` 的形符值。

## token.**DOUBLESLASH**

`"//"` 的形符值。

## token.**DOUBLESLASHEQUAL**

`"//="` 的形符值。

## token.**AT**

`"@"` 的形符值。

## token.**ATEQUAL**

`"@="` 的形符值。

## token.**RARROW**

`"->"` 的形符值。

## token.**ELLIPSIS**

`"..."` 的形符值。

## token.**COLONEQUAL**

`":="` 的形符值。

## token.**EXCLAMATION**

`"!"` 的形符值。

## token.**OP**

## token.**TYPE_IGNORE**

## token.**TYPE_COMMENT**

## token.**SOFT_KEYWORD**

## token.**FSTRING_START**

## token.**FSTRING_MIDDLE**

## token.**FSTRING_END**

## token.**COMMENT**

## token.**NL**

## token.**ERRORTOKEN**

## token.**N_TOKENS**

## token.**NT_OFFSET**

​	C 形符生成器不使用以下形符类型值，但 [`tokenize`](https://docs.python.org/zh-cn/3.13/library/tokenize.html#module-tokenize) 模块需要它们。

## token.**COMMENT**

​	形符值用于表示注释。

## token.**NL**

​	形符值用于表示非终止换行符。 [`NEWLINE`](https://docs.python.org/zh-cn/3.13/library/token.html#token.NEWLINE) 形符表示 Python 代码逻辑行的结束；当在多条物理线路上继续执行逻辑代码行时，会生成 `NL` 形符。

## token.**ENCODING**

​	指示用于将源字节解码为文本的编码的形符值。 [`tokenize.tokenize()`](https://docs.python.org/zh-cn/3.13/library/tokenize.html#tokenize.tokenize) 返回的第一个形符将始终是一个 `ENCODING` 形符。

## token.**TYPE_COMMENT**

​	指明类型注释已被识别的形符值。 此种形符仅在 [`ast.parse()`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.parse) 附带 `type_comments=True` 被发起调用时才会产生。

*在 3.5 版本发生变更:* 增加了 `AWAIT` 和 `ASYNC` 形符。

*在 3.7 版本发生变更:* 形符 [`COMMENT`](https://docs.python.org/zh-cn/3.13/library/token.html#token.COMMENT)、 [`NL`](https://docs.python.org/zh-cn/3.13/library/token.html#token.NL) 和 [`ENCODING`](https://docs.python.org/zh-cn/3.13/library/token.html#token.ENCODING) 形符。

*在 3.7 版本发生变更:* 移除了 `AWAIT` 和 `ASYNC` 形符。 "async" 和 "await" 被形符化为 [`NAME`](https://docs.python.org/zh-cn/3.13/library/token.html#token.NAME) 形符。

*在 3.8 版本发生变更:* 增加了 [`TYPE_COMMENT`](https://docs.python.org/zh-cn/3.13/library/token.html#token.TYPE_COMMENT), [`TYPE_IGNORE`](https://docs.python.org/zh-cn/3.13/library/token.html#token.TYPE_IGNORE), [`COLONEQUAL`](https://docs.python.org/zh-cn/3.13/library/token.html#token.COLONEQUAL)。 重新增加了 `AWAIT` 和 `ASYNC` 形符（需要用它们来支持解析 [`ast.parse()`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.parse) 的 `feature_version` 设为 6 或更低的较旧 Python 版本）。

*在 3.13 版本发生变更:* 重新移除了 `AWAIT` 和 `ASYNC` 形符。
