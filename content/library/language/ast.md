+++
title = "ast --- 抽象语法树"
date = 2024-11-15T21:23:55+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/ast.html](https://docs.python.org/zh-cn/3.13/library/ast.html)
>
> 收录该文档的时间：`2024-11-15T21:23:55+08:00`

# `ast` --- 抽象语法树

**源代码：** [Lib/ast.py](https://github.com/python/cpython/tree/3.13/Lib/ast.py)

------

[`ast`](https://docs.python.org/zh-cn/3.13/library/ast.html#module-ast) 模块帮助 Python 程序处理 Python 语法的抽象语法树。抽象语法或许会随着 Python 的更新版发行而改变；该模块能够帮助理解当前语法在编程层面的样貌。

​	抽象语法树可通过将 [`ast.PyCF_ONLY_AST`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.PyCF_ONLY_AST) 作为旗标传递给 [`compile()`](https://docs.python.org/zh-cn/3.13/library/functions.html#compile) 内置函数来生成，或是使用此模块中提供的 [`parse()`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.parse) 辅助函数。返回结果将是一个由许多对象构成的树，这些对象所属的类都继承自 [`ast.AST`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST)。抽象语法树可被内置的 [`compile()`](https://docs.python.org/zh-cn/3.13/library/functions.html#compile) 函数编译为一个 Python 代码对象。



## 抽象文法

​	抽象文法目前定义如下

```
-- ASDL's 4 builtin types are:
-- identifier, int, string, constant

module Python
{
    mod = Module(stmt* body, type_ignore* type_ignores)
        | Interactive(stmt* body)
        | Expression(expr body)
        | FunctionType(expr* argtypes, expr returns)

    stmt = FunctionDef(identifier name, arguments args,
                       stmt* body, expr* decorator_list, expr? returns,
                       string? type_comment, type_param* type_params)
          | AsyncFunctionDef(identifier name, arguments args,
                             stmt* body, expr* decorator_list, expr? returns,
                             string? type_comment, type_param* type_params)

          | ClassDef(identifier name,
             expr* bases,
             keyword* keywords,
             stmt* body,
             expr* decorator_list,
             type_param* type_params)
          | Return(expr? value)

          | Delete(expr* targets)
          | Assign(expr* targets, expr value, string? type_comment)
          | TypeAlias(expr name, type_param* type_params, expr value)
          | AugAssign(expr target, operator op, expr value)
          -- 'simple' indicates that we annotate simple name without parens
          | AnnAssign(expr target, expr annotation, expr? value, int simple)

          -- use 'orelse' because else is a keyword in target languages
          | For(expr target, expr iter, stmt* body, stmt* orelse, string? type_comment)
          | AsyncFor(expr target, expr iter, stmt* body, stmt* orelse, string? type_comment)
          | While(expr test, stmt* body, stmt* orelse)
          | If(expr test, stmt* body, stmt* orelse)
          | With(withitem* items, stmt* body, string? type_comment)
          | AsyncWith(withitem* items, stmt* body, string? type_comment)

          | Match(expr subject, match_case* cases)

          | Raise(expr? exc, expr? cause)
          | Try(stmt* body, excepthandler* handlers, stmt* orelse, stmt* finalbody)
          | TryStar(stmt* body, excepthandler* handlers, stmt* orelse, stmt* finalbody)
          | Assert(expr test, expr? msg)

          | Import(alias* names)
          | ImportFrom(identifier? module, alias* names, int? level)

          | Global(identifier* names)
          | Nonlocal(identifier* names)
          | Expr(expr value)
          | Pass | Break | Continue

          -- col_offset is the byte offset in the utf8 string the parser uses
          attributes (int lineno, int col_offset, int? end_lineno, int? end_col_offset)

          -- BoolOp() can use left & right?
    expr = BoolOp(boolop op, expr* values)
         | NamedExpr(expr target, expr value)
         | BinOp(expr left, operator op, expr right)
         | UnaryOp(unaryop op, expr operand)
         | Lambda(arguments args, expr body)
         | IfExp(expr test, expr body, expr orelse)
         | Dict(expr* keys, expr* values)
         | Set(expr* elts)
         | ListComp(expr elt, comprehension* generators)
         | SetComp(expr elt, comprehension* generators)
         | DictComp(expr key, expr value, comprehension* generators)
         | GeneratorExp(expr elt, comprehension* generators)
         -- the grammar constrains where yield expressions can occur
         | Await(expr value)
         | Yield(expr? value)
         | YieldFrom(expr value)
         -- need sequences for compare to distinguish between
         -- x < 4 < 3 and (x < 4) < 3
         | Compare(expr left, cmpop* ops, expr* comparators)
         | Call(expr func, expr* args, keyword* keywords)
         | FormattedValue(expr value, int conversion, expr? format_spec)
         | JoinedStr(expr* values)
         | Constant(constant value, string? kind)

         -- the following expression can appear in assignment context
         | Attribute(expr value, identifier attr, expr_context ctx)
         | Subscript(expr value, expr slice, expr_context ctx)
         | Starred(expr value, expr_context ctx)
         | Name(identifier id, expr_context ctx)
         | List(expr* elts, expr_context ctx)
         | Tuple(expr* elts, expr_context ctx)

         -- can appear only in Subscript
         | Slice(expr? lower, expr? upper, expr? step)

          -- col_offset is the byte offset in the utf8 string the parser uses
          attributes (int lineno, int col_offset, int? end_lineno, int? end_col_offset)

    expr_context = Load | Store | Del

    boolop = And | Or

    operator = Add | Sub | Mult | MatMult | Div | Mod | Pow | LShift
                 | RShift | BitOr | BitXor | BitAnd | FloorDiv

    unaryop = Invert | Not | UAdd | USub

    cmpop = Eq | NotEq | Lt | LtE | Gt | GtE | Is | IsNot | In | NotIn

    comprehension = (expr target, expr iter, expr* ifs, int is_async)

    excepthandler = ExceptHandler(expr? type, identifier? name, stmt* body)
                    attributes (int lineno, int col_offset, int? end_lineno, int? end_col_offset)

    arguments = (arg* posonlyargs, arg* args, arg? vararg, arg* kwonlyargs,
                 expr* kw_defaults, arg? kwarg, expr* defaults)

    arg = (identifier arg, expr? annotation, string? type_comment)
           attributes (int lineno, int col_offset, int? end_lineno, int? end_col_offset)

    -- keyword arguments supplied to call (NULL identifier for **kwargs)
    keyword = (identifier? arg, expr value)
               attributes (int lineno, int col_offset, int? end_lineno, int? end_col_offset)

    -- import name with optional 'as' alias.
    alias = (identifier name, identifier? asname)
             attributes (int lineno, int col_offset, int? end_lineno, int? end_col_offset)

    withitem = (expr context_expr, expr? optional_vars)

    match_case = (pattern pattern, expr? guard, stmt* body)

    pattern = MatchValue(expr value)
            | MatchSingleton(constant value)
            | MatchSequence(pattern* patterns)
            | MatchMapping(expr* keys, pattern* patterns, identifier? rest)
            | MatchClass(expr cls, pattern* patterns, identifier* kwd_attrs, pattern* kwd_patterns)

            | MatchStar(identifier? name)
            -- The optional "rest" MatchMapping parameter handles capturing extra mapping keys

            | MatchAs(pattern? pattern, identifier? name)
            | MatchOr(pattern* patterns)

             attributes (int lineno, int col_offset, int end_lineno, int end_col_offset)

    type_ignore = TypeIgnore(int lineno, string tag)

    type_param = TypeVar(identifier name, expr? bound, expr? default_value)
               | ParamSpec(identifier name, expr? default_value)
               | TypeVarTuple(identifier name, expr? default_value)
               attributes (int lineno, int col_offset, int end_lineno, int end_col_offset)
}
```

## 节点类

## *class* ast.**AST**

​	这是所有 AST 节点类的基类。 实际的节点类都派生自 `Parser/Python.asdl` 文件，其完整内容 [如上所示](https://docs.python.org/zh-cn/3.13/library/ast.html#abstract-grammar)。 它们 `_ast` C 模块中定义并在 [`ast`](https://docs.python.org/zh-cn/3.13/library/ast.html#module-ast) 中重新导出。

​	抽象文法中的每个等号左边的符号（比方说， `ast.stmt` 或者 `ast.expr`）定义了一个类。另外，在等号右边，对每一个构造器也定义了一个类；这些类继承自等号左边的类。比如，[`ast.BinOp`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.BinOp) 继承自 `ast.expr`。对于多分支产生式（也就是含有“ | ”的产生式），左边的类是抽象的；只有具体构造器类的实例能够被 compile 函数构造。



## **_fields**

​	每个实体类都具有属性 `_fields`，它给出了所有子节点的名字。

​	每个具体类的实例为自己的每个子节点都准备了一个属性来引用该子节点，属性的类型就是文法中所定义的。比如，[`ast.BinOp`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.BinOp) 的实例有个属性 `left`，类型是 `ast.expr`。

​	如果这些属性在文法中标记为可选（用问号标记），对应值可能会是 `None`。如果这些属性可有零或多个值（用星号标记），对应值会用Python的列表来表示。在用 [`compile()`](https://docs.python.org/zh-cn/3.13/library/functions.html#compile) 将AST编译为可执行代码时，所有的属性必须已经被赋值为有效的值。

## **_field_types**

​	每次个实体类上的 `_field_types` 属性都是一个将字段名（与在 [`_fields`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST._fields) 中列出的相同）) 映射到其类型的字典。



``` python
>>> ast.TypeVar._field_types
{'name': <class 'str'>, 'bound': ast.expr | None, 'default_value': ast.expr | None}
```

> Added in version 3.13.
>

## **lineno**

## **col_offset**

## **end_lineno**

## **end_col_offset**

`ast.expr` 和 `ast.stmt` 的子类的实例的属性包括 [`lineno`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST.lineno)、[`col_offset`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST.col_offset)、[`end_lineno`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST.end_lineno) 和 [`end_col_offset`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST.end_col_offset)。[`lineno`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST.lineno) 和 [`end_lineno`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST.end_lineno) 是源码中属于该节点的部分从哪一行开始，到哪一行结束（数字 1 指第一行，以此类推）；[`col_offset`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST.col_offset) 和 [`end_col_offset`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST.end_col_offset) 是第一个和最后一个属于该节点的 token 的 UTF-8 字节偏移量。记录 UTF-8 偏移量的原因是解析器内部使用 UTF-8。

​	注意编译器不需要结束位置，所以结束位置是可选的。结束偏移在最后一个符号 *之后* ，例如你可以通过 `source_line[node.col_offset : node.end_col_offset]` 获得一个单行表达式节点的源码片段。

`ast.T` 类的构造器像下面这样解析它的参数：

- 如果只用位置参数，参数的数量必须和 `T._fields` 中的项一样多；它们会按顺序赋值到这些属性上。
- 如果有关键字参数，它们会为与其关键字同名的属性赋值。

​	比方说，要创建和填充节点 [`ast.UnaryOp`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.UnaryOp)，你得用

```
node = ast.UnaryOp(ast.USub(), ast.Constant(5, lineno=0, col_offset=0),
                   lineno=0, col_offset=0)
```

​	如果从构造器中省略一个在语法中可选的字段，则其默认值为 `None`。 如果省略一个列表字段，则其默认值为空列表。 如果省略一个 `ast.expr_context` 类型的字段，则其默认值 为 [`Load()`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Load)。 如果省略任何其他字段，则会引发 [`DeprecationWarning`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#DeprecationWarning) 并且 AST 节点将不包含此字段。 在 Python 3.15，这种情况将引发一个错误。

*在 3.8 版本发生变更:* [`ast.Constant`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Constant) 类现在用于所有常量。

*在 3.9 版本发生变更:* 简单索引由它们的值表示，扩展切片表示为元组。

*自 3.8 版本弃用:* 原有的类 `ast.Num`, `ast.Str`, `ast.Bytes`, `ast.NameConstant` 和 `ast.Ellipsis` 仍然可用，但它们将在未来的 Python 发布版中被移除。 同时，实例化它们将返回其他某个类的实例。

*自 3.9 版本弃用:* 原有的类 `ast.Index` and `ast.ExtSlice` 仍然可用，但它们将在未来的 Python 发布版中被移除。 同时，实例化它们将返回其他某个类的实例。

*Deprecated since version 3.13, will be removed in version 3.15:* 之前版本的 Python 允许创建缺少必需字段的 AST 节点。 类似地，AST 节点构造器也允许任意用于设置 AST 节点属性的关键字参数，即使它们不能匹配任何 AST 节点的字段。 此行为已被弃用并将在 Python 3.15 中移除。

​	备注

 

​	在此显示的特定节点类的描述最初是改编自杰出的 [Green Tree Snakes](https://greentreesnakes.readthedocs.io/en/latest/) 项目及其所有贡献者。



### 根节点

## *class* ast.**Module**(*body*, *type_ignores*)

​	一个 Python 模块，用于 [文件输入](https://docs.python.org/zh-cn/3.13/reference/toplevel_components.html#file-input)。 由 [`ast.parse()`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.parse) 以默认 `"exec"` *mode* 生成的节点类型。

`body` 是由该模块的 [语句](https://docs.python.org/zh-cn/3.13/library/ast.html#ast-statements) 组成的 [`list`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#list)。

`type_ignores` 是由该模块的类型忽略注释组成的 [`list`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#list)；请参阅 [`ast.parse()`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.parse) 了解详情。



``` python
>>> print(ast.dump(ast.parse('x = 1'), indent=4))
Module(
    body=[
        Assign(
            targets=[
                Name(id='x', ctx=Store())],
            value=Constant(value=1))])
```

## *class* ast.**Expression**(*body*)

​	单个 Python [表达式输入](https://docs.python.org/zh-cn/3.13/reference/toplevel_components.html#expression-input)。 当 *mode* 为 `"eval"` 时由 [`ast.parse()`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.parse) 所生成的节点类型。

`body` 为单独节点，是某一个 [表达式类型](https://docs.python.org/zh-cn/3.13/library/ast.html#ast-expressions)。



``` python
>>> print(ast.dump(ast.parse('123', mode='eval'), indent=4))
Expression(
    body=Constant(value=123))
```

## *class* ast.**Interactive**(*body*)

​	单个 [交互式输入](https://docs.python.org/zh-cn/3.13/reference/toplevel_components.html#interactive)，就像在 [交互模式](https://docs.python.org/zh-cn/3.13/tutorial/appendix.html#tut-interac) 中一样。 当 *mode* 为 `"single"` 时由 [`ast.parse()`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.parse) 所生成的节点类型。

`body` 是由 [语句节点](https://docs.python.org/zh-cn/3.13/library/ast.html#ast-statements) 组成的 [`list`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#list)。



``` python
>>> print(ast.dump(ast.parse('x = 1; y = 2', mode='single'), indent=4))
Interactive(
    body=[
        Assign(
            targets=[
                Name(id='x', ctx=Store())],
            value=Constant(value=1)),
        Assign(
            targets=[
                Name(id='y', ctx=Store())],
            value=Constant(value=2))])
```

## *class* ast.**FunctionType**(*argtypes*, *returns*)

​	函数的旧风格类型注释表示形式，因为 Python 3.5 之前的版本不支持 [**PEP 484**](https://peps.python.org/pep-0484/) 标注。 当 *mode* 为 `"func_type"` 时由 [`ast.parse()`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.parse) 所生成的节点类型。

​	此种类型注释的形式是这样的:

```
def sum_two_number(a, b):
    # type: (int, int) -> int
    return a + b
```

`argtypes` 是由 [表达式节点](https://docs.python.org/zh-cn/3.13/library/ast.html#ast-expressions) 组成的 [`list`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#list)。

`returns` 是单独的 [表达式节点](https://docs.python.org/zh-cn/3.13/library/ast.html#ast-expressions)。



``` python
>>> print(ast.dump(ast.parse('(int, str) -> List[int]', mode='func_type'), indent=4))
FunctionType(
    argtypes=[
        Name(id='int', ctx=Load()),
        Name(id='str', ctx=Load())],
    returns=Subscript(
        value=Name(id='List', ctx=Load()),
        slice=Name(id='int', ctx=Load()),
        ctx=Load()))
```

> Added in version 3.8.
>

### 字面值

## *class* ast.**Constant**(*value*)

​	一个常量。 `Constant` 字面值的 `value` 属性即为其代表的 Python 对象。它可以代表简单的数字，字符串或者 `None` 对象，但是也可以代表所有元素都是常量的不可变容器（例如元组或冻结集合）。



``` python
>>> print(ast.dump(ast.parse('123', mode='eval'), indent=4))
Expression(
    body=Constant(value=123))
```

## *class* ast.**FormattedValue**(*value*, *conversion*, *format_spec*)

​	节点是以一个 f-字符串形式的格式化字段来代表的。 如果该字符串只包含单个格式化字段而没有任何其他内容则节点可以被隔离，否则它将在 [`JoinedStr`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.JoinedStr) 中出现。

- `value` 为任意的表达式节点（如一个字面值、变量或函数调用）。
- `conversion` 是一个整数:
  - -1: 无格式化
  - 115: `!s` 字符串格式化
  - 114: `!r` repr 格式化
  - 97: `!a` ascii 格式化
- `format_spec` 是一个代表值的格式化的 [`JoinedStr`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.JoinedStr) 节点，或者如果未指定格式则为 `None`。 `conversion` 和 `format_spec` 可以被同时设置。

## *class* ast.**JoinedStr**(*values*)

​	一个 f-字符串，由一系列 [`FormattedValue`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.FormattedValue) 和 [`Constant`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Constant) 节点组成。



``` python
>>> print(ast.dump(ast.parse('f"sin({a}) is {sin(a):.3}"', mode='eval'), indent=4))
Expression(
    body=JoinedStr(
        values=[
            Constant(value='sin('),
            FormattedValue(
                value=Name(id='a', ctx=Load()),
                conversion=-1),
            Constant(value=') is '),
            FormattedValue(
                value=Call(
                    func=Name(id='sin', ctx=Load()),
                    args=[
                        Name(id='a', ctx=Load())]),
                conversion=-1,
                format_spec=JoinedStr(
                    values=[
                        Constant(value='.3')]))]))
```

## *class* ast.**List**(*elts*, *ctx*)

## *class* ast.**Tuple**(*elts*, *ctx*)

​	一个列表或元组。 `elts` 保存一个代表元素的节点的列表。 `ctx` 在容器为赋值的目标时 (如 `(x,y)=something`) 是 [`Store`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Store)，否则是 [`Load`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Load)。



``` python
>>> print(ast.dump(ast.parse('[1, 2, 3]', mode='eval'), indent=4))
Expression(
    body=List(
        elts=[
            Constant(value=1),
            Constant(value=2),
            Constant(value=3)],
        ctx=Load()))
>>> print(ast.dump(ast.parse('(1, 2, 3)', mode='eval'), indent=4))
Expression(
    body=Tuple(
        elts=[
            Constant(value=1),
            Constant(value=2),
            Constant(value=3)],
        ctx=Load()))
```

## *class* ast.**Set**(*elts*)

​	一个集合。 `elts` 保存一个代表集合的元组的节点的列表。



``` python
>>> print(ast.dump(ast.parse('{1, 2, 3}', mode='eval'), indent=4))
Expression(
    body=Set(
        elts=[
            Constant(value=1),
            Constant(value=2),
            Constant(value=3)]))
```

## *class* ast.**Dict**(*keys*, *values*)

​	一个字典。 `keys` 和 `values` 保存分别代表键和值的节点的列表，按照匹配的顺序（即当调用 `dictionary.keys()` 和 `dictionary.values()` 时将返回的结果）。

​	当使用字典字面值进行字典解包操作时要扩展的表达式放入 `values` 列表，并将 `None` 放入 `keys` 的对应位置。



``` python
>>> print(ast.dump(ast.parse('{"a":1, **d}', mode='eval'), indent=4))
Expression(
    body=Dict(
        keys=[
            Constant(value='a'),
            None],
        values=[
            Constant(value=1),
            Name(id='d', ctx=Load())]))
```

### 变量

## *class* ast.**Name**(*id*, *ctx*)

​	一个变量名。 `id` 将名称保存为字符串，而 `ctx` 为下列类型之一。

## *class* ast.**Load**

## *class* ast.**Store**

## *class* ast.**Del**

​	变量引用可被用来载入一个变量的值，为其赋一个新值，或是将其删除。 变量引用会给出一个上下文来区分这几种情况。



``` python
>>> print(ast.dump(ast.parse('a'), indent=4))
Module(
    body=[
        Expr(
            value=Name(id='a', ctx=Load()))])

>>> print(ast.dump(ast.parse('a = 1'), indent=4))
Module(
    body=[
        Assign(
            targets=[
                Name(id='a', ctx=Store())],
            value=Constant(value=1))])

>>> print(ast.dump(ast.parse('del a'), indent=4))
Module(
    body=[
        Delete(
            targets=[
                Name(id='a', ctx=Del())])])
```

## *class* ast.**Starred**(*value*, *ctx*)

​	一个 `*var` 变量引用。 `value` 保存变量，通常为一个 [`Name`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Name) 节点。 此类型必须在构建 [`Call`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Call) 节点并传入 `*args` 时被使用。



``` python
>>> print(ast.dump(ast.parse('a, *b = it'), indent=4))
Module(
    body=[
        Assign(
            targets=[
                Tuple(
                    elts=[
                        Name(id='a', ctx=Store()),
                        Starred(
                            value=Name(id='b', ctx=Store()),
                            ctx=Store())],
                    ctx=Store())],
            value=Name(id='it', ctx=Load()))])
```



### 表达式

## *class* ast.**Expr**(*value*)

​	当一个表达式，例如函数调用，本身作为一个语句出现并且其返回值未被使用或存储时，它会被包装在此容器中。 `value` 保存本节中的其他节点之一，一个 [`Constant`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Constant), [`Name`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Name), [`Lambda`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Lambda), [`Yield`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Yield) 或者 [`YieldFrom`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.YieldFrom) 节点。



``` python
>>> print(ast.dump(ast.parse('-a'), indent=4))
Module(
    body=[
        Expr(
            value=UnaryOp(
                op=USub(),
                operand=Name(id='a', ctx=Load())))])
```

## *class* ast.**UnaryOp**(*op*, *operand*)

​	一个单目运算。 `op` 是运算符，而 `operand` 是任意表达式节点。

## *class* ast.**UAdd**

## *class* ast.**USub**

## *class* ast.**Not**

## *class* ast.**Invert**

​	单目运算符对应的形符。 [`Not`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Not) 是 `not` 关键字，[`Invert`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Invert) 是 `~` 运算符。



``` python
>>> print(ast.dump(ast.parse('not x', mode='eval'), indent=4))
Expression(
    body=UnaryOp(
        op=Not(),
        operand=Name(id='x', ctx=Load())))
```

## *class* ast.**BinOp**(*left*, *op*, *right*)

​	一个双目运算（如相加或相减）。 `op` 是运算符，而 `left` 和 `right` 是任意表达式节点。



``` python
>>> print(ast.dump(ast.parse('x + y', mode='eval'), indent=4))
Expression(
    body=BinOp(
        left=Name(id='x', ctx=Load()),
        op=Add(),
        right=Name(id='y', ctx=Load())))
```

## *class* ast.**Add**

## *class* ast.**Sub**

## *class* ast.**Mult**

## *class* ast.**Div**

## *class* ast.**FloorDiv**

## *class* ast.**Mod**

## *class* ast.**Pow**

## *class* ast.**LShift**

## *class* ast.**RShift**

## *class* ast.**BitOr**

## *class* ast.**BitXor**

## *class* ast.**BitAnd**

## *class* ast.**MatMult**

​	双目运算符对应的形符。

## *class* ast.**BoolOp**(*op*, *values*)

​	一个布尔运算，'or' 或者 'and'。 `op` 是 [`Or`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Or) 或者 [`And`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.And)。 `values` 是参与运算的值。 具有相同运算符的连续运算，如 `a or b or c`，会被折叠为具有多个值的单个节点。

​	这不包括 `not`，它属于 [`UnaryOp`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.UnaryOp)。



``` python
>>> print(ast.dump(ast.parse('x or y', mode='eval'), indent=4))
Expression(
    body=BoolOp(
        op=Or(),
        values=[
            Name(id='x', ctx=Load()),
            Name(id='y', ctx=Load())]))
```

## *class* ast.**And**

## *class* ast.**Or**

​	布尔运算符对应的形符。

## *class* ast.**Compare**(*left*, *ops*, *comparators*)

​	两个或更多值之间的比较运算。 `left` 是参加比较的第一个值，`ops` 是由运算符组成的列表，而 `comparators` 是由参加比较的第一个元素之后的值组成的列表。



``` python
>>> print(ast.dump(ast.parse('1 <= a < 10', mode='eval'), indent=4))
Expression(
    body=Compare(
        left=Constant(value=1),
        ops=[
            LtE(),
            Lt()],
        comparators=[
            Name(id='a', ctx=Load()),
            Constant(value=10)]))
```

## *class* ast.**Eq**

## *class* ast.**NotEq**

## *class* ast.**Lt**

## *class* ast.**LtE**

## *class* ast.**Gt**

## *class* ast.**GtE**

## *class* ast.**Is**

## *class* ast.**IsNot**

## *class* ast.**In**

## *class* ast.**NotIn**

​	比较运算符对应的形符。

## *class* ast.**Call**(*func*, *args*, *keywords*)

​	一个函数调用。 `func` 是函数，它通常是一个 [`Name`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Name) 或 [`Attribute`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Attribute) 对象。 对于其参数:

- `args` 保存由按位置传入的参数组成的列表。
- `keywords` 保存了一个代表以关键字传入的参数的 [`keyword`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.keyword) 对象的列表。

`args` 和 `keywords` 参数是可选的并且默认为空列表。



``` python
>>> print(ast.dump(ast.parse('func(a, b=c, *d, **e)', mode='eval'), indent=4))
Expression(
    body=Call(
        func=Name(id='func', ctx=Load()),
        args=[
            Name(id='a', ctx=Load()),
            Starred(
                value=Name(id='d', ctx=Load()),
                ctx=Load())],
        keywords=[
            keyword(
                arg='b',
                value=Name(id='c', ctx=Load())),
            keyword(
                value=Name(id='e', ctx=Load()))]))
```

## *class* ast.**keyword**(*arg*, *value*)

​	传给函数调用或类定义的关键字参数。 `arg` 是形参名称对应的原始字符串，`value` 是要传入的节点。

## *class* ast.**IfExp**(*test*, *body*, *orelse*)

​	一个表达式例如 `a if b else c`。 每个字段保存一个单独节点，因而在下面的示例中，三个节点均为 [`Name`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Name) 节点。



``` python
>>> print(ast.dump(ast.parse('a if b else c', mode='eval'), indent=4))
Expression(
    body=IfExp(
        test=Name(id='b', ctx=Load()),
        body=Name(id='a', ctx=Load()),
        orelse=Name(id='c', ctx=Load())))
```

## *class* ast.**Attribute**(*value*, *attr*, *ctx*)

​	属性访问，例如 `d.keys`。 `value` 是一个节点，通常为 [`Name`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Name)。 `attr` 是一个给出属性名称的纯字符串，而 `ctx` 根据属性操作的方式可以为 [`Load`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Load), [`Store`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Store) 或 [`Del`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Del)。



``` python
>>> print(ast.dump(ast.parse('snake.colour', mode='eval'), indent=4))
Expression(
    body=Attribute(
        value=Name(id='snake', ctx=Load()),
        attr='colour',
        ctx=Load()))
```

## *class* ast.**NamedExpr**(*target*, *value*)

​	一个带名称的表达式。 此 AST 节点是由赋值表达式运算符（或称海象运算符）产生的。 与第一个参数可以有多个节点的 [`Assign`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Assign) 节点不同，在此情况下 `target` 和 `value` 都必须为单独节点。



``` python
>>> print(ast.dump(ast.parse('(x := 4)', mode='eval'), indent=4))
Expression(
    body=NamedExpr(
        target=Name(id='x', ctx=Store()),
        value=Constant(value=4)))
```

> Added in version 3.8.
>

#### 抽取

## *class* ast.**Subscript**(*value*, *slice*, *ctx*)

​	抽取操作，如 `l[1]`。 `value` 是被抽取的对象（通常为序列或映射）。 `slice` 是索引号、切片或键。 它可以是一个包含 [`Slice`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Slice) 的 [`Tuple`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Tuple)。 `ctx` 根据抽取所执行的操作可以为 [`Load`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Load), [`Store`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Store) 或 [`Del`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Del)。



``` python
>>> print(ast.dump(ast.parse('l[1:2, 3]', mode='eval'), indent=4))
Expression(
    body=Subscript(
        value=Name(id='l', ctx=Load()),
        slice=Tuple(
            elts=[
                Slice(
                    lower=Constant(value=1),
                    upper=Constant(value=2)),
                Constant(value=3)],
            ctx=Load()),
        ctx=Load()))
```

## *class* ast.**Slice**(*lower*, *upper*, *step*)

​	常规切片 (形式如 `lower:upper` 或 `lower:upper:step`)。 只能在 [`Subscript`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Subscript) 的 *slice* 字段内部出现，可以是直接切片对象或是作为 [`Tuple`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Tuple) 的元素。



``` python
>>> print(ast.dump(ast.parse('l[1:2]', mode='eval'), indent=4))
Expression(
    body=Subscript(
        value=Name(id='l', ctx=Load()),
        slice=Slice(
            lower=Constant(value=1),
            upper=Constant(value=2)),
        ctx=Load()))
```

#### 推导式

## *class* ast.**ListComp**(*elt*, *generators*)

## *class* ast.**SetComp**(*elt*, *generators*)

## *class* ast.**GeneratorExp**(*elt*, *generators*)

## *class* ast.**DictComp**(*key*, *value*, *generators*)

​	列表和集合推导式、生成器表达式以及字典推导式。 `elt` (或 `key` 和 `value`) 是一个代表将针对每个条目被求值的部分的单独节点。

`generators` 是一个由 [`comprehension`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.comprehension) 节点组成的列表。



``` python
>>> print(ast.dump(
...     ast.parse('[x for x in numbers]', mode='eval'),
...     indent=4,
... ))
Expression(
    body=ListComp(
        elt=Name(id='x', ctx=Load()),
        generators=[
            comprehension(
                target=Name(id='x', ctx=Store()),
                iter=Name(id='numbers', ctx=Load()),
                is_async=0)]))
>>> print(ast.dump(
...     ast.parse('{x: x**2 for x in numbers}', mode='eval'),
...     indent=4,
... ))
Expression(
    body=DictComp(
        key=Name(id='x', ctx=Load()),
        value=BinOp(
            left=Name(id='x', ctx=Load()),
            op=Pow(),
            right=Constant(value=2)),
        generators=[
            comprehension(
                target=Name(id='x', ctx=Store()),
                iter=Name(id='numbers', ctx=Load()),
                is_async=0)]))
>>> print(ast.dump(
...     ast.parse('{x for x in numbers}', mode='eval'),
...     indent=4,
... ))
Expression(
    body=SetComp(
        elt=Name(id='x', ctx=Load()),
        generators=[
            comprehension(
                target=Name(id='x', ctx=Store()),
                iter=Name(id='numbers', ctx=Load()),
                is_async=0)]))
```

## *class* ast.**comprehension**(*target*, *iter*, *ifs*, *is_async*)

​	推导式中的一个 `for` 子句。 `target` 是针对每个元素使用的引用 —— 通常为一个 [`Name`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Name) 或 [`Tuple`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Tuple) 节点。 `iter` 是要执行迭代的对象。 `ifs` 是一个由测试表达式组成的列表：每个 `for` 子句都可以拥有多个 `ifs`。

`is_async` 表明推导式是异步的 (使用 `async for` 而不是 `for`)。 它的值是一个整数 (0 或 1)。



``` python
>>> print(ast.dump(ast.parse('[ord(c) for line in file for c in line]', mode='eval'),
...                indent=4)) # Multiple comprehensions in one.
Expression(
    body=ListComp(
        elt=Call(
            func=Name(id='ord', ctx=Load()),
            args=[
                Name(id='c', ctx=Load())]),
        generators=[
            comprehension(
                target=Name(id='line', ctx=Store()),
                iter=Name(id='file', ctx=Load()),
                is_async=0),
            comprehension(
                target=Name(id='c', ctx=Store()),
                iter=Name(id='line', ctx=Load()),
                is_async=0)]))

>>> print(ast.dump(ast.parse('(n**2 for n in it if n>5 if n<10)', mode='eval'),
...                indent=4)) # generator comprehension
Expression(
    body=GeneratorExp(
        elt=BinOp(
            left=Name(id='n', ctx=Load()),
            op=Pow(),
            right=Constant(value=2)),
        generators=[
            comprehension(
                target=Name(id='n', ctx=Store()),
                iter=Name(id='it', ctx=Load()),
                ifs=[
                    Compare(
                        left=Name(id='n', ctx=Load()),
                        ops=[
                            Gt()],
                        comparators=[
                            Constant(value=5)]),
                    Compare(
                        left=Name(id='n', ctx=Load()),
                        ops=[
                            Lt()],
                        comparators=[
                            Constant(value=10)])],
                is_async=0)]))

>>> print(ast.dump(ast.parse('[i async for i in soc]', mode='eval'),
...                indent=4)) # Async comprehension
Expression(
    body=ListComp(
        elt=Name(id='i', ctx=Load()),
        generators=[
            comprehension(
                target=Name(id='i', ctx=Store()),
                iter=Name(id='soc', ctx=Load()),
                is_async=1)]))
```



### 语句

## *class* ast.**Assign**(*targets*, *value*, *type_comment*)

​	一次赋值。 `targets` 是一个由节点组成的列表，而 `value` 是一个单独节点。

`targets` 中有多个节点表示将同一个值赋给多个目标。 解包操作是通过在 `targets` 中放入一个 [`Tuple`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Tuple) 或 [`List`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.List) 来表示的。

## **type_comment**

`type_comment` 是带有以注释表示的类型标注的可选的字符串。



``` python
>>> print(ast.dump(ast.parse('a = b = 1'), indent=4)) # Multiple assignment
Module(
    body=[
        Assign(
            targets=[
                Name(id='a', ctx=Store()),
                Name(id='b', ctx=Store())],
            value=Constant(value=1))])

>>> print(ast.dump(ast.parse('a,b = c'), indent=4)) # Unpacking
Module(
    body=[
        Assign(
            targets=[
                Tuple(
                    elts=[
                        Name(id='a', ctx=Store()),
                        Name(id='b', ctx=Store())],
                    ctx=Store())],
            value=Name(id='c', ctx=Load()))])
```

## *class* ast.**AnnAssign**(*target*, *annotation*, *value*, *simple*)

​	带有类型标注的赋值。 `target` 是单独的节点并可以是一个 [`Name`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Name), [`Attribute`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Attribute) 或 [`Subscript`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Subscript)。 `annotation` 是标注，例如一个 [`Constant`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Constant) 或 [`Name`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Name) 节点。 `value` 是单独的可选节点。

`simple` is always either 0 (indicating a "complex" target) or 1 (indicating a "simple" target). A "simple" target consists solely of a [`Name`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Name) node that does not appear between parentheses; all other targets are considered complex. Only simple targets appear in the `__annotations__` dictionary of modules and classes.



``` python
>>> print(ast.dump(ast.parse('c: int'), indent=4))
Module(
    body=[
        AnnAssign(
            target=Name(id='c', ctx=Store()),
            annotation=Name(id='int', ctx=Load()),
            simple=1)])

>>> print(ast.dump(ast.parse('(a): int = 1'), indent=4)) # Annotation with parenthesis
Module(
    body=[
        AnnAssign(
            target=Name(id='a', ctx=Store()),
            annotation=Name(id='int', ctx=Load()),
            value=Constant(value=1),
            simple=0)])

>>> print(ast.dump(ast.parse('a.b: int'), indent=4)) # Attribute annotation
Module(
    body=[
        AnnAssign(
            target=Attribute(
                value=Name(id='a', ctx=Load()),
                attr='b',
                ctx=Store()),
            annotation=Name(id='int', ctx=Load()),
            simple=0)])

>>> print(ast.dump(ast.parse('a[1]: int'), indent=4)) # Subscript annotation
Module(
    body=[
        AnnAssign(
            target=Subscript(
                value=Name(id='a', ctx=Load()),
                slice=Constant(value=1),
                ctx=Store()),
            annotation=Name(id='int', ctx=Load()),
            simple=0)])
```

## *class* ast.**AugAssign**(*target*, *op*, *value*)

​	增强赋值，如 `a += 1`。 在下面的例子中，`target` 是一个针对 `x` (带有 [`Store`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Store) 上下文) 的 [`Name`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Name) 节点，`op` 为 [`Add`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Add)，而 `value` 是一个值为 1 的 [`Constant`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Constant)。

`target` 属性不可以是 [`Tuple`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Tuple) 或 [`List`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.List) 类，这与 [`Assign`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Assign) 的目标不同。



``` python
>>> print(ast.dump(ast.parse('x += 2'), indent=4))
Module(
    body=[
        AugAssign(
            target=Name(id='x', ctx=Store()),
            op=Add(),
            value=Constant(value=2))])
```

## *class* ast.**Raise**(*exc*, *cause*)

​	一条 `raise` 语句。 `exc` 是要引发的异常，对于一个单独的 `raise` 通常为 [`Call`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Call) 或 [`Name`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Name)，或者为 `None`。 `cause` 是针对 `raise x from y` 中 `y` 的可选部分。



``` python
>>> print(ast.dump(ast.parse('raise x from y'), indent=4))
Module(
    body=[
        Raise(
            exc=Name(id='x', ctx=Load()),
            cause=Name(id='y', ctx=Load()))])
```

## *class* ast.**Assert**(*test*, *msg*)

​	一条断言。 `test` 保存条件，例如为一个 [`Compare`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Compare) 节点。 `msg` 保存失败消息。



``` python
>>> print(ast.dump(ast.parse('assert x,y'), indent=4))
Module(
    body=[
        Assert(
            test=Name(id='x', ctx=Load()),
            msg=Name(id='y', ctx=Load()))])
```

## *class* ast.**Delete**(*targets*)

​	代表一条 `del` 语句。 `targets` 是一个由节点组成的列表，例如 [`Name`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Name), [`Attribute`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Attribute) 或 [`Subscript`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Subscript) 节点。



``` python
>>> print(ast.dump(ast.parse('del x,y,z'), indent=4))
Module(
    body=[
        Delete(
            targets=[
                Name(id='x', ctx=Del()),
                Name(id='y', ctx=Del()),
                Name(id='z', ctx=Del())])])
```

## *class* ast.**Pass**

​	一条 `pass` 语句。



``` python
>>> print(ast.dump(ast.parse('pass'), indent=4))
Module(
    body=[
        Pass()])
```

## *class* ast.**TypeAlias**(*name*, *type_params*, *value*)

​	通过 [`type`](https://docs.python.org/zh-cn/3.13/reference/simple_stmts.html#type) 语句创建的 [类型别名](https://docs.python.org/zh-cn/3.13/library/typing.html#type-aliases)。 `name` 是别名的名称，`type_params` 是 [类型形参](https://docs.python.org/zh-cn/3.13/library/ast.html#ast-type-params) 的列表，而 `value` 是类型别名的值。



``` python
>>> print(ast.dump(ast.parse('type Alias = int'), indent=4))
Module(
    body=[
        TypeAlias(
            name=Name(id='Alias', ctx=Store()),
            value=Name(id='int', ctx=Load()))])
```

> Added in version 3.12.
>

​	其他仅在函数或循环内部可用的语句将在其他小节中描述。

#### 导入

## *class* ast.**Import**(*names*)

​	一条导入语句。 `names` 是一个由 [`alias`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.alias) 节点组成的列表。



``` python
>>> print(ast.dump(ast.parse('import x,y,z'), indent=4))
Module(
    body=[
        Import(
            names=[
                alias(name='x'),
                alias(name='y'),
                alias(name='z')])])
```

## *class* ast.**ImportFrom**(*module*, *names*, *level*)

​	代表 `from x import y`。 `module` 是一个 'from' 名称的原始字符串，不带任何前导点号，或者为 `None` 表示 `from . import foo` 这样的语句。 `level` 是一个保存相对导入层级的整数（0 表示绝对导入）。



``` python
>>> print(ast.dump(ast.parse('from y import x,y,z'), indent=4))
Module(
    body=[
        ImportFrom(
            module='y',
            names=[
                alias(name='x'),
                alias(name='y'),
                alias(name='z')],
            level=0)])
```

## *class* ast.**alias**(*name*, *asname*)

​	两个形参均为名称的原始字符串。 如果要使用常规名称则 `asname` 可以为 `None`。



``` python
>>> print(ast.dump(ast.parse('from ..foo.bar import a as b, c'), indent=4))
Module(
    body=[
        ImportFrom(
            module='foo.bar',
            names=[
                alias(name='a', asname='b'),
                alias(name='c')],
            level=2)])
```

### 控制流

​	备注

 

​	可选的子句如 `else` 如果不存在则会被存储为一个空列表。

## *class* ast.**If**(*test*, *body*, *orelse*)

​	一条 `if` 语句。 `test` 保存一个单独节点，如一个 [`Compare`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Compare) 节点。 `body` 和 `orelse` 各自保存一个节点列表。

`elif` 子句在 AST 中没有特别的表示形式，而是作为上文介绍的 `orelse` 部分之内的一个额外 [`If`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.If) 节点出现。



``` python
>>> print(ast.dump(ast.parse("""
... if x:
...    ...
... elif y:
...    ...
... else:
...    ...
... """), indent=4))
Module(
    body=[
        If(
            test=Name(id='x', ctx=Load()),
            body=[
                Expr(
                    value=Constant(value=Ellipsis))],
            orelse=[
                If(
                    test=Name(id='y', ctx=Load()),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))],
                    orelse=[
                        Expr(
                            value=Constant(value=Ellipsis))])])])
```

## *class* ast.**For**(*target*, *iter*, *body*, *orelse*, *type_comment*)

​	一个 `for` 循环。 `target` 保存循环赋值的变量，是一个单独的 [`Name`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Name), [`Tuple`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Tuple), [`List`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.List), [`Attribute`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Attribute) 或 [`Subscript`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Subscript) 节点。 `iter` 保存要被循环的条目，同样也是一个单独节点。 `body` 和 `orelse` 包含要执行的节点列表。 `orelse` 中的语句会在循环正常结束时被执行，而不是通过 `break` 语句执行。

## **type_comment**

`type_comment` 是带有以注释表示的类型标注的可选的字符串。



``` python
>>> print(ast.dump(ast.parse("""
... for x in y:
...     ...
... else:
...     ...
... """), indent=4))
Module(
    body=[
        For(
            target=Name(id='x', ctx=Store()),
            iter=Name(id='y', ctx=Load()),
            body=[
                Expr(
                    value=Constant(value=Ellipsis))],
            orelse=[
                Expr(
                    value=Constant(value=Ellipsis))])])
```

## *class* ast.**While**(*test*, *body*, *orelse*)

​	一个 `while` 循环。 `test` 保存条件，如一个 [`Compare`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Compare) 节点。

```
>> print(ast.dump(ast.parse("""
... while x:
...    ...
... else:
...    ...
... """), indent=4))
Module(
    body=[
        While(
            test=Name(id='x', ctx=Load()),
            body=[
                Expr(
                    value=Constant(value=Ellipsis))],
            orelse=[
                Expr(
                    value=Constant(value=Ellipsis))])])
```

## *class* ast.**Break**

## *class* ast.**Continue**

`break` 和 `continue` 语句。



``` python
>>> print(ast.dump(ast.parse("""\
... for a in b:
...     if a > 5:
...         break
...     else:
...         continue
...
... """), indent=4))
Module(
    body=[
        For(
            target=Name(id='a', ctx=Store()),
            iter=Name(id='b', ctx=Load()),
            body=[
                If(
                    test=Compare(
                        left=Name(id='a', ctx=Load()),
                        ops=[
                            Gt()],
                        comparators=[
                            Constant(value=5)]),
                    body=[
                        Break()],
                    orelse=[
                        Continue()])])])
```

## *class* ast.**Try**(*body*, *handlers*, *orelse*, *finalbody*)

`try` 代码块。 所有属性都是要执行的节点列表，除了 `handlers`，它是一个 [`ExceptHandler`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.ExceptHandler) 节点列表。



``` python
>>> print(ast.dump(ast.parse("""
... try:
...    ...
... except Exception:
...    ...
... except OtherException as e:
...    ...
... else:
...    ...
... finally:
...    ...
... """), indent=4))
Module(
    body=[
        Try(
            body=[
                Expr(
                    value=Constant(value=Ellipsis))],
            handlers=[
                ExceptHandler(
                    type=Name(id='Exception', ctx=Load()),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))]),
                ExceptHandler(
                    type=Name(id='OtherException', ctx=Load()),
                    name='e',
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))])],
            orelse=[
                Expr(
                    value=Constant(value=Ellipsis))],
            finalbody=[
                Expr(
                    value=Constant(value=Ellipsis))])])
```

## *class* ast.**TryStar**(*body*, *handlers*, *orelse*, *finalbody*)

`try` 代码块后带有 `except*` 子句。 包含的属性与 [`Try`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Try) 的相同但 [`ExceptHandler`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.ExceptHandler) 节点在 `handlers` 中会被解读为 `except*` 而不是 `except` 代码块。



``` python
>>> print(ast.dump(ast.parse("""
... try:
...    ...
... except* Exception:
...    ...
... """), indent=4))
Module(
    body=[
        TryStar(
            body=[
                Expr(
                    value=Constant(value=Ellipsis))],
            handlers=[
                ExceptHandler(
                    type=Name(id='Exception', ctx=Load()),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))])])])
```

> Added in version 3.11.
>

## *class* ast.**ExceptHandler**(*type*, *name*, *body*)

​	一个单独的 `except` 子句。 `type` 是它将匹配的异常，通常为一个 [`Name`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Name) 节点（或 `None` 表示捕获全部的 `except:` 子句）。 `name` 是一个用于存放异常的别名的原始字符串，或者如果子句没有 `as foo` 则为 `None`。 `body` 为一个节点列表。



``` python
>>> print(ast.dump(ast.parse("""\
... try:
...     a + 1
... except TypeError:
...     pass
... """), indent=4))
Module(
    body=[
        Try(
            body=[
                Expr(
                    value=BinOp(
                        left=Name(id='a', ctx=Load()),
                        op=Add(),
                        right=Constant(value=1)))],
            handlers=[
                ExceptHandler(
                    type=Name(id='TypeError', ctx=Load()),
                    body=[
                        Pass()])])])
```

## *class* ast.**With**(*items*, *body*, *type_comment*)

​	一个 `with` 代码块。 `items` 是一个代表上下文管理器的 [`withitem`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.withitem) 节点列表，而 `body` 是该上下文中的缩进代码块。

## **type_comment**

`type_comment` 是带有以注释表示的类型标注的可选的字符串。

## *class* ast.**withitem**(*context_expr*, *optional_vars*)

​	一个 `with` 代码块中单独的上下文管理器。 `context_expr` 为上下文管理器，通常为一个 [`Call`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Call) 节点。 `optional_vars` 为一个针对 `as foo` 部分的 [`Name`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Name), [`Tuple`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Tuple) 或 [`List`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.List)，或者如果未使用别名则为 `None`。



``` python
>>> print(ast.dump(ast.parse("""\
... with a as b, c as d:
...    something(b, d)
... """), indent=4))
Module(
    body=[
        With(
            items=[
                withitem(
                    context_expr=Name(id='a', ctx=Load()),
                    optional_vars=Name(id='b', ctx=Store())),
                withitem(
                    context_expr=Name(id='c', ctx=Load()),
                    optional_vars=Name(id='d', ctx=Store()))],
            body=[
                Expr(
                    value=Call(
                        func=Name(id='something', ctx=Load()),
                        args=[
                            Name(id='b', ctx=Load()),
                            Name(id='d', ctx=Load())]))])])
```

### 模式匹配

## *class* ast.**Match**(*subject*, *cases*)

​	一条 `match` 语句。 `subject` 保存匹配的目标（与 cases 相匹配的对象）而 `cases` 包含一个由不同分支的 [`match_case`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.match_case) 节点组成的可迭代对象。

> Added in version 3.10.
>

## *class* ast.**match_case**(*pattern*, *guard*, *body*)

​	一个 `match` 语句中单独的 case 模式。 `pattern` 包含目标将要去匹配的匹配模式。 请注意针对模式所产生的 [`AST`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST) 节点不同于针对表达式所产生的节点，即使它们共享相同的语法。

`guard` 属性包含一个当模式与目标相匹配时将被求值的表达式。

`body` 包含一个当模式匹配并且对 guard 表达式求值的结果为真时要执行的节点列表。



``` python
>>> print(ast.dump(ast.parse("""
... match x:
...     case [x] if x>0:
...         ...
...     case tuple():
...         ...
... """), indent=4))
Module(
    body=[
        Match(
            subject=Name(id='x', ctx=Load()),
            cases=[
                match_case(
                    pattern=MatchSequence(
                        patterns=[
                            MatchAs(name='x')]),
                    guard=Compare(
                        left=Name(id='x', ctx=Load()),
                        ops=[
                            Gt()],
                        comparators=[
                            Constant(value=0)]),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))]),
                match_case(
                    pattern=MatchClass(
                        cls=Name(id='tuple', ctx=Load())),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))])])])
```

> Added in version 3.10.
>

## *class* ast.**MatchValue**(*value*)

​	一个按相等性进行比较的匹配字面值或值模式。 `value` 为一个表达式节点。 允许的值节点被限制为 match 语句文档中所描述的节点。 如果匹配目标等于 value 的求值结果则模式匹配成功。



``` python
>>> print(ast.dump(ast.parse("""
... match x:
...     case "Relevant":
...         ...
... """), indent=4))
Module(
    body=[
        Match(
            subject=Name(id='x', ctx=Load()),
            cases=[
                match_case(
                    pattern=MatchValue(
                        value=Constant(value='Relevant')),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))])])])
```

> Added in version 3.10.
>

## *class* ast.**MatchSingleton**(*value*)

​	一个按标识号进行比较的匹配字面值模式。 `value` 为用于比较的单例对象: `None`, `True` 或 `False`。 如果匹配目标为给定的常量则该模式匹配成功。



``` python
>>> print(ast.dump(ast.parse("""
... match x:
...     case None:
...         ...
... """), indent=4))
Module(
    body=[
        Match(
            subject=Name(id='x', ctx=Load()),
            cases=[
                match_case(
                    pattern=MatchSingleton(value=None),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))])])])
```

> Added in version 3.10.
>

## *class* ast.**MatchSequence**(*patterns*)

​	一个匹配序列模式。 `patterns` 包含当目标为一个序列时要与目标元素进行匹配的模式。 如果某一子模式为 `MatchStar` 节点则将匹配一个变长度序列，否则将匹配一个固定长度序列。



``` python
>>> print(ast.dump(ast.parse("""
... match x:
...     case [1, 2]:
...         ...
... """), indent=4))
Module(
    body=[
        Match(
            subject=Name(id='x', ctx=Load()),
            cases=[
                match_case(
                    pattern=MatchSequence(
                        patterns=[
                            MatchValue(
                                value=Constant(value=1)),
                            MatchValue(
                                value=Constant(value=2))]),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))])])])
```

> Added in version 3.10.
>

## *class* ast.**MatchStar**(*name*)

​	匹配一个可变长度匹配序列模式中的剩余部分序列。 如果 `name` 不为 `None`，则当整个序列模式匹配成功时将把一个包含剩余序列元素的列表绑定到该名称。



``` python
>>> print(ast.dump(ast.parse("""
... match x:
...     case [1, 2, *rest]:
...         ...
...     case [*_]:
...         ...
... """), indent=4))
Module(
    body=[
        Match(
            subject=Name(id='x', ctx=Load()),
            cases=[
                match_case(
                    pattern=MatchSequence(
                        patterns=[
                            MatchValue(
                                value=Constant(value=1)),
                            MatchValue(
                                value=Constant(value=2)),
                            MatchStar(name='rest')]),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))]),
                match_case(
                    pattern=MatchSequence(
                        patterns=[
                            MatchStar()]),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))])])])
```

> Added in version 3.10.
>

## *class* ast.**MatchMapping**(*keys*, *patterns*, *rest*)

​	一个匹配的映射模式。 `keys` 为一个由表达式节点组成的序列。 `patterns` 为一个由对应的模式节点组成的序列。 `rest` 是一个可被指定用来捕获剩余映射元素的可选名称。 允许的关键字表达式被限制为与 match 语句文档中所描述的一致。

​	如果目标为一个映射、所有被求值的表达式都存在于该映射中，并且对应于每个键的值都与对应的子模式相匹配则此模式匹配成功。 如果 `rest` 不为 `None`，则当整个映射模式匹配成功时会将一个包含剩余映射元素的字典绑定到该名称。



``` python
>>> print(ast.dump(ast.parse("""
... match x:
...     case {1: _, 2: _}:
...         ...
...     case {**rest}:
...         ...
... """), indent=4))
Module(
    body=[
        Match(
            subject=Name(id='x', ctx=Load()),
            cases=[
                match_case(
                    pattern=MatchMapping(
                        keys=[
                            Constant(value=1),
                            Constant(value=2)],
                        patterns=[
                            MatchAs(),
                            MatchAs()]),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))]),
                match_case(
                    pattern=MatchMapping(rest='rest'),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))])])])
```

> Added in version 3.10.
>

## *class* ast.**MatchClass**(*cls*, *patterns*, *kwd_attrs*, *kwd_patterns*)

​	一个 match 类模式。 `cls` 为一个给出要匹配的名义类的表达式。 `patterns` 为一个由要与该类所定义的模式匹配属性相匹配的模式节点组成的序列。 `kwd_attrs` 为一个由要匹配的附加属性（指定为该类模式中的关键字参数）组成的序列，`kwd_patterns` 为对应的模式（指定为该类模式中的关键字值）。

​	如果目标为被指名类的一个实例、所有的位置模式都与对应的类定义属性相匹配，并且任何被指定的关键字属性都与其对应的模式相匹配则此模式匹配成功。

​	注意：类可能会定义一个返回自身的特征属性以便能将一个模式节点与被匹配的实例相匹配。 某些内置类型也是以这种方式来匹配的，与 match 语句文档中所描述的一致。



``` python
>>> print(ast.dump(ast.parse("""
... match x:
...     case Point2D(0, 0):
...         ...
...     case Point3D(x=0, y=0, z=0):
...         ...
... """), indent=4))
Module(
    body=[
        Match(
            subject=Name(id='x', ctx=Load()),
            cases=[
                match_case(
                    pattern=MatchClass(
                        cls=Name(id='Point2D', ctx=Load()),
                        patterns=[
                            MatchValue(
                                value=Constant(value=0)),
                            MatchValue(
                                value=Constant(value=0))]),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))]),
                match_case(
                    pattern=MatchClass(
                        cls=Name(id='Point3D', ctx=Load()),
                        kwd_attrs=[
                            'x',
                            'y',
                            'z'],
                        kwd_patterns=[
                            MatchValue(
                                value=Constant(value=0)),
                            MatchValue(
                                value=Constant(value=0)),
                            MatchValue(
                                value=Constant(value=0))]),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))])])])
```

> Added in version 3.10.
>

## *class* ast.**MatchAs**(*pattern*, *name*)

​	一个匹配 "as-模式"、捕获模式或通配符模式。 `pattern` 包含将要与目标相匹配的匹配模式。 如果模式为 `None`，则该节点代表一个捕获模式（即一个简单的名称）并将总是会成功。

`name` 属性包含当模式匹配成功时将要绑定的名称。 如果 `name` 为 `None`，则 `pattern` 也必须为 `None` 并且该节点代表的是通配符模式。



``` python
>>> print(ast.dump(ast.parse("""
... match x:
...     case [x] as y:
...         ...
...     case _:
...         ...
... """), indent=4))
Module(
    body=[
        Match(
            subject=Name(id='x', ctx=Load()),
            cases=[
                match_case(
                    pattern=MatchAs(
                        pattern=MatchSequence(
                            patterns=[
                                MatchAs(name='x')]),
                        name='y'),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))]),
                match_case(
                    pattern=MatchAs(),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))])])])
```

> Added in version 3.10.
>

## *class* ast.**MatchOr**(*patterns*)

​	一个匹配 "or-模式"。 or-模式会依次将其每个子模式与目标相匹配，直到有一个匹配成功。 此时该 or-模式将被视为匹配成功。 如果没有一个子模式匹配成功则该 or-模式匹配失败。 `patterns` 属性包含一个由将与目标相匹配的匹配模式节点组成的列表。



``` python
>>> print(ast.dump(ast.parse("""
... match x:
...     case [x] | (y):
...         ...
... """), indent=4))
Module(
    body=[
        Match(
            subject=Name(id='x', ctx=Load()),
            cases=[
                match_case(
                    pattern=MatchOr(
                        patterns=[
                            MatchSequence(
                                patterns=[
                                    MatchAs(name='x')]),
                            MatchAs(name='y')]),
                    body=[
                        Expr(
                            value=Constant(value=Ellipsis))])])])
```

> Added in version 3.10.
>



### 类型形参

[类型形参](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#type-params) 可以存在于类、函数和类型别名中。

## *class* ast.**TypeVar**(*name*, *bound*, *default_value*)

​	A [`typing.TypeVar`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.TypeVar). `name` is the name of the type variable. `bound` is the bound or constraints, if any. If `bound` is a [`Tuple`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Tuple), it represents constraints; otherwise it represents the bound. `default_value` is the default value; if the `TypeVar` has no default, this attribute will be set to `None`.



``` python
>>> print(ast.dump(ast.parse("type Alias[T: int = bool] = list[T]"), indent=4))
Module(
    body=[
        TypeAlias(
            name=Name(id='Alias', ctx=Store()),
            type_params=[
                TypeVar(
                    name='T',
                    bound=Name(id='int', ctx=Load()),
                    default_value=Name(id='bool', ctx=Load()))],
            value=Subscript(
                value=Name(id='list', ctx=Load()),
                slice=Name(id='T', ctx=Load()),
                ctx=Load()))])
```

> Added in version 3.12.
>

*在 3.13 版本发生变更:* 增加了 *default_value* 形参。

## *class* ast.**ParamSpec**(*name*, *default_value*)

​	A [`typing.ParamSpec`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.ParamSpec). `name` is the name of the parameter specification. `default_value` is the default value; if the `ParamSpec` has no default, this attribute will be set to `None`.



``` python
>>> print(ast.dump(ast.parse("type Alias[**P = (int, str)] = Callable[P, int]"), indent=4))
Module(
    body=[
        TypeAlias(
            name=Name(id='Alias', ctx=Store()),
            type_params=[
                ParamSpec(
                    name='P',
                    default_value=Tuple(
                        elts=[
                            Name(id='int', ctx=Load()),
                            Name(id='str', ctx=Load())],
                        ctx=Load()))],
            value=Subscript(
                value=Name(id='Callable', ctx=Load()),
                slice=Tuple(
                    elts=[
                        Name(id='P', ctx=Load()),
                        Name(id='int', ctx=Load())],
                    ctx=Load()),
                ctx=Load()))])
```

> Added in version 3.12.
>

*在 3.13 版本发生变更:* 增加了 *default_value* 形参。

## *class* ast.**TypeVarTuple**(*name*, *default_value*)

​	A [`typing.TypeVarTuple`](https://docs.python.org/zh-cn/3.13/library/typing.html#typing.TypeVarTuple). `name` is the name of the type variable tuple. `default_value` is the default value; if the `TypeVarTuple` has no default, this attribute will be set to `None`.



``` python
>>> print(ast.dump(ast.parse("type Alias[*Ts = ()] = tuple[*Ts]"), indent=4))
Module(
    body=[
        TypeAlias(
            name=Name(id='Alias', ctx=Store()),
            type_params=[
                TypeVarTuple(
                    name='Ts',
                    default_value=Tuple(ctx=Load()))],
            value=Subscript(
                value=Name(id='tuple', ctx=Load()),
                slice=Tuple(
                    elts=[
                        Starred(
                            value=Name(id='Ts', ctx=Load()),
                            ctx=Load())],
                    ctx=Load()),
                ctx=Load()))])
```

> Added in version 3.12.
>

*在 3.13 版本发生变更:* 增加了 *default_value* 形参。

### 函数与类定义

## *class* ast.**FunctionDef**(*name*, *args*, *body*, *decorator_list*, *returns*, *type_comment*, *type_params*)

​	一个函数定义。

- `name` 是函数名称的原始字符串。
- `args` 是一个 [`arguments`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.arguments) 节点。
- `body` 是函数内部的节点列表。
- `decorator_list` 是要应用的装饰器列表，最外层的最先保存（即列表中的第一项将最后被应用）。
- `returns` 是返回标注。
- `type_params` 是一个 [类型形参](https://docs.python.org/zh-cn/3.13/library/ast.html#ast-type-params) 的列表。

## **type_comment**

`type_comment` 是带有以注释表示的类型标注的可选的字符串。

*在 3.12 版本发生变更:* 增加了 `type_params`。

## *class* ast.**Lambda**(*args*, *body*)

`lambda` 是可在表达式内部使用的最小化函数定义。 不同于 [`FunctionDef`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.FunctionDef)，`body` 是保存一个单独节点。



``` python
>>> print(ast.dump(ast.parse('lambda x,y: ...'), indent=4))
Module(
    body=[
        Expr(
            value=Lambda(
                args=arguments(
                    args=[
                        arg(arg='x'),
                        arg(arg='y')]),
                body=Constant(value=Ellipsis)))])
```

## *class* ast.**arguments**(*posonlyargs*, *args*, *vararg*, *kwonlyargs*, *kw_defaults*, *kwarg*, *defaults*)

​	函数的参数。

- `posonlyargs`, `args` 和 `kwonlyargs` 均为 [`arg`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.arg) 节点的列表。
- `vararg` 和 `kwarg` 均为单独的 [`arg`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.arg) 节点，指向 `*args, **kwargs` 形参。
- `kw_defaults` 是一个由仅限关键字参数默认值组成的列表。 如果有一个为 `None`，则对应的参数为必须的参数。
- `defaults` 是一个由可按位置传入的参数的默认值组成的列表。 如果默认值个数少于参数个数，则它们将对应最后 n 个参数。

## *class* ast.**arg**(*arg*, *annotation*, *type_comment*)

​	列表中的一个单独参数。 `arg` 为参数名称原始字符串；`annotation` 为其标，如一个 [`Name`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Name) 节点。

## **type_comment**

`type_comment` 是一个可选的将注释用作类型标注的字符串。



``` python
>>> print(ast.dump(ast.parse("""\
... @decorator1
... @decorator2
... def f(a: 'annotation', b=1, c=2, *d, e, f=3, **g) -> 'return annotation':
...     pass
... """), indent=4))
Module(
    body=[
        FunctionDef(
            name='f',
            args=arguments(
                args=[
                    arg(
                        arg='a',
                        annotation=Constant(value='annotation')),
                    arg(arg='b'),
                    arg(arg='c')],
                vararg=arg(arg='d'),
                kwonlyargs=[
                    arg(arg='e'),
                    arg(arg='f')],
                kw_defaults=[
                    None,
                    Constant(value=3)],
                kwarg=arg(arg='g'),
                defaults=[
                    Constant(value=1),
                    Constant(value=2)]),
            body=[
                Pass()],
            decorator_list=[
                Name(id='decorator1', ctx=Load()),
                Name(id='decorator2', ctx=Load())],
            returns=Constant(value='return annotation'))])
```

## *class* ast.**Return**(*value*)

​	一条 `return` 语句。



``` python
>>> print(ast.dump(ast.parse('return 4'), indent=4))
Module(
    body=[
        Return(
            value=Constant(value=4))])
```

## *class* ast.**Yield**(*value*)

## *class* ast.**YieldFrom**(*value*)

​	一个 `yield` 或 `yield from` 表达式。 因为这些属性表达式，所以如果发回的值未被使用则必须将它们包装在 [`Expr`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Expr) 节点中。



``` python
>>> print(ast.dump(ast.parse('yield x'), indent=4))
Module(
    body=[
        Expr(
            value=Yield(
                value=Name(id='x', ctx=Load())))])

>>> print(ast.dump(ast.parse('yield from x'), indent=4))
Module(
    body=[
        Expr(
            value=YieldFrom(
                value=Name(id='x', ctx=Load())))])
```

## *class* ast.**Global**(*names*)

## *class* ast.**Nonlocal**(*names*)

`global` 和 `nonlocal` 语句。 `names` 为一个由原始字符串组成的列表。



``` python
>>> print(ast.dump(ast.parse('global x,y,z'), indent=4))
Module(
    body=[
        Global(
            names=[
                'x',
                'y',
                'z'])])

>>> print(ast.dump(ast.parse('nonlocal x,y,z'), indent=4))
Module(
    body=[
        Nonlocal(
            names=[
                'x',
                'y',
                'z'])])
```

## *class* ast.**ClassDef**(*name*, *bases*, *keywords*, *body*, *decorator_list*, *type_params*)

​	一个类定义。

- `name` 为类名称的原始字符串。
- `bases` 为一个由显式指明的基类节点组成的列表。
- `keywords` is a list of [`keyword`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.keyword) nodes, principally for 'metaclass'. Other keywords will be passed to the metaclass, as per [**PEP 3115**](https://peps.python.org/pep-3115/).
- `body` 是一个由代表类定义内部代码的节点组成的列表。
- `decorator_list` 是一个节点的列表，与 [`FunctionDef`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.FunctionDef) 中的一致。
- `type_params` 是一个 [类型形参](https://docs.python.org/zh-cn/3.13/library/ast.html#ast-type-params) 的列表。



``` python
>>> print(ast.dump(ast.parse("""\
... @decorator1
... @decorator2
... class Foo(base1, base2, metaclass=meta):
...     pass
... """), indent=4))
Module(
    body=[
        ClassDef(
            name='Foo',
            bases=[
                Name(id='base1', ctx=Load()),
                Name(id='base2', ctx=Load())],
            keywords=[
                keyword(
                    arg='metaclass',
                    value=Name(id='meta', ctx=Load()))],
            body=[
                Pass()],
            decorator_list=[
                Name(id='decorator1', ctx=Load()),
                Name(id='decorator2', ctx=Load())])])
```

*在 3.12 版本发生变更:* 增加了 `type_params`。

### async 与 await

## *class* ast.**AsyncFunctionDef**(*name*, *args*, *body*, *decorator_list*, *returns*, *type_comment*, *type_params*)

​	一个 `async def` 函数定义。 具有与 [`FunctionDef`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.FunctionDef) 相同的字段。

*在 3.12 版本发生变更:* 增加了 `type_params`。

## *class* ast.**Await**(*value*)

​	一个 `await` 表达式。 `value` 是它所等待的值。 仅在 [`AsyncFunctionDef`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AsyncFunctionDef) 的函数体内可用。



``` python
>>> print(ast.dump(ast.parse("""\
... async def f():
...     await other_func()
... """), indent=4))
Module(
    body=[
        AsyncFunctionDef(
            name='f',
            args=arguments(),
            body=[
                Expr(
                    value=Await(
                        value=Call(
                            func=Name(id='other_func', ctx=Load()))))])])
```

## *class* ast.**AsyncFor**(*target*, *iter*, *body*, *orelse*, *type_comment*)

## *class* ast.**AsyncWith**(*items*, *body*, *type_comment*)

`async for` 循环和 `async with` 上下文管理器。 它们分别具有与 [`For`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.For) 和 [`With`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.With) 相同的字段。 仅在 [`AsyncFunctionDef`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AsyncFunctionDef) 的函数体内可用。

​	备注

 

​	当一个字符串由 [`ast.parse()`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.parse) 来解析时，所返回的树中的运算符节点 (为 `ast.operator`, `ast.unaryop`, `ast.cmpop`, `ast.boolop` 和 `ast.expr_context` 的子类) 将均为单例对象。 对其中某一个 (例如 [`ast.Add`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Add)) 的修改将反映到同一个值所出现的其他位置上。

## [`ast`](https://docs.python.org/zh-cn/3.13/library/ast.html#module-ast) 中的辅助函数

​	除了节点类， [`ast`](https://docs.python.org/zh-cn/3.13/library/ast.html#module-ast) 模块里为遍历抽象语法树定义了这些工具函数和类:

## ast.**parse**(*source*, *filename='<unknown>'*, *mode='exec'*, ***, *type_comments=False*, *feature_version=None*, *optimize=-1*)

​	将 source 解析为一个 AST 节点。 等价于 `compile(source, filename, mode, flags=FLAGS_VALUE, optimize=optimize)`，其中当 `optimize <= 0` 时 `FLAGS_VALUE` 为 `ast.PyCF_ONLY_AST` 否则为 `ast.PyCF_OPTIMIZED_AST`。

​	If `type_comments=True` is given, the parser is modified to check and return type comments as specified by [**PEP 484**](https://peps.python.org/pep-0484/) and [**PEP 526**](https://peps.python.org/pep-0526/). This is equivalent to adding [`ast.PyCF_TYPE_COMMENTS`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.PyCF_TYPE_COMMENTS) to the flags passed to [`compile()`](https://docs.python.org/zh-cn/3.13/library/functions.html#compile). This will report syntax errors for misplaced type comments. Without this flag, type comments will be ignored, and the `type_comment` field on selected AST nodes will always be `None`. In addition, the locations of `# type: ignore` comments will be returned as the `type_ignores` attribute of [`Module`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Module) (otherwise it is always an empty list).

​	并且，如果 `mode` 为 `'func_type'`，则输入语法会进行与 [**PEP 484**](https://peps.python.org/pep-0484/) "签名类型注释" 对应的修改，例如 `(str, int) -> List[str]`。

​	将 `feature_version` 设为元组 `(major, minor)` 将导致使用相应 Python 版本的语法来“尽力”尝试解析。 例如，设置 `feature_version=(3, 9)` 将尝试禁止解析 [`match`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#match) 语句。 目前 `major` 必须等于 `3`。 最低的受支持版本为 `(3, 7)` (并且这可能在未来的 Python 版本中增高)；最高版本为 `sys.version_info[0:2]`。 “尽力”尝试意味着不能保证解析（或解析成功）与在对应于 `feature_version` 的 Python 版本上运行时相同。

​	如果源包含一个空字符 (`\0`)，则会引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

​	警告

 

​	请注意成功将源代码解析为 AST 对象并不能保证该源代码提供了可被执行的有效 Python 代码，因为编译步骤还可能引发其他的 [`SyntaxError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#SyntaxError) 异常。 例如，对于源代码 `return 42` 可以生成一条有效的 return 语句 AST 节点，但它不能被单独编译（它必须位于一个函数节点之内）。

​	特别地，[`ast.parse()`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.parse) 不会执行任何作用域检查，这是由编译步骤来做的。

​	警告

 

​	足够复杂或是巨大的字符串可能导致Python解释器的崩溃，因为Python的AST编译器是有栈深限制的。

*在 3.8 版本发生变更:* 增加了 `type_comments`, `mode='func_type'` 和 `feature_version`。

*在 3.13 版本发生变更:* 现在最低的支持 `feature_version` 的版本为 `(3, 7)`。 增加了 `optimize` 参数。

## ast.**unparse**(*ast_obj*)

​	反向解析一个 [`ast.AST`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST) 对象并生成一个包含当再次使用 [`ast.parse()`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.parse) 解析时将产生同样的 [`ast.AST`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST) 对象的代码的字符串。

​	警告

 

​	所产生的代码字符串将不一定与生成 [`ast.AST`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST) 对象的原始代码完全一致（不带任何编译器优化，例如常量元组/冻结集合等）。

​	警告

 

​	尝试反向解析一个高度复杂的表达式可能会导致 [`RecursionError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#RecursionError)。

> Added in version 3.9.
>

## ast.**literal_eval**(*node_or_string*)

​	对表达式节点或仅包含 Python 字面值或容器表示形式的字符串进行求值。 所提供的字符串或节点可能只包含下列 Python 字面值结构：字符串、字节串、数字、元组、字典、集合、布尔值、`None` 和 `Ellipsis`。

​	这可被用于对包含 Python 值的字符串进行求值而不必解析这些值本身。 它不能对任意的复杂表达式进行求值，例如涉及运算符或索引操作的表达式。

​	此函数过去曾被描述为“安全”但并未定义其含义。 这是存在误导性的。 此函数被专门设计为不执行 Python 代码，这与更通用的 [`eval()`](https://docs.python.org/zh-cn/3.13/library/functions.html#eval) 不同。 它没有命名空间、没有名称查找或执行外部调用的能力。 但是它并不能完全避免攻击：一个相对较小的输入有可能导致内存耗尽或 C 栈耗尽，使得进程崩溃。 还可能在某些输入上出现过度消耗 CPU 的拒绝服务问题。 因此不建议在未被信任的数据上调用它。

​	警告

 

​	有可能由于 Python 的 AST 编译器中的栈深度限制而导致 Python 解释器的崩溃。

​	根据输入错误的具体形式它可能引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError), [`TypeError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#TypeError), [`SyntaxError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#SyntaxError), [`MemoryError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#MemoryError) 以及 [`RecursionError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#RecursionError)。

*在 3.2 版本发生变更:* 目前支持字节和集合。

*在 3.9 版本发生变更:* 现在支持通过 `'set()'` 创建空集合。

*在 3.10 版本发生变更:* 对于字符串输入，打头的空格和制表符现在会被去除。

## ast.**get_docstring**(*node*, *clean=True*)

​	返回给定 *node* (必须为 [`FunctionDef`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.FunctionDef), [`AsyncFunctionDef`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AsyncFunctionDef), [`ClassDef`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.ClassDef) 或 [`Module`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.Module) 节点) 的文档字符串，或者如果没有文档字符串则返回 `None`。 如果 *clean* 为真值，则通过 [`inspect.cleandoc()`](https://docs.python.org/zh-cn/3.13/library/inspect.html#inspect.cleandoc) 清除文档字符串的缩进。

*在 3.5 版本发生变更:* 目前支持 [`AsyncFunctionDef`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AsyncFunctionDef)

## ast.**get_source_segment**(*source*, *node*, ***, *padded=False*)

​	获取生成 *node* 的 *source* 的源代码段。 如果丢失了某些位置信息 ([`lineno`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST.lineno), [`end_lineno`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST.end_lineno), [`col_offset`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST.col_offset) 或 [`end_col_offset`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST.end_col_offset))，则返回 `None`。

​	如果 *padded* 为 `True`，则多行语句的第一行将以与其初始位置相匹配的空格填充。

> Added in version 3.8.
>

## ast.**fix_missing_locations**(*node*)

​	当你使用 [`compile()`](https://docs.python.org/zh-cn/3.13/library/functions.html#compile) 来编译节点树时，编译器会接受每个支持 [`lineno`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST.lineno) 和 [`col_offset`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST.col_offset) 属性的节点的相应信息。 对于已生成的节点来说这是相当繁琐的，因此这个辅助工具会递归地为尚未设置这些属性的节点添加它们，具体做法是将其设为父节点的对应值。 它将从 *node* 开始递归地执行。

## ast.**increment_lineno**(*node*, *n=1*)

​	从 *node* 开始按 *n* 递增节点树中每个节点的行号和结束行号。 这在“移动代码”到文件中的不同位置时很有用处。

## ast.**copy_location**(*new_node*, *old_node*)

​	在可能的情况下将源位置 ([`lineno`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST.lineno), [`col_offset`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST.col_offset), [`end_lineno`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST.end_lineno) 和 [`end_col_offset`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST.end_col_offset)) 从 *old_node* 拷贝到 *new_node*，并返回 *new_node*。

## ast.**iter_fields**(*node*)

​	针对于 *node* 上在 `node._fields` 中出现的每个字段产生一个 `(fieldname, value)` 元组。

## ast.**iter_child_nodes**(*node*)

​	产生 *node* 所有的直接子节点，也就是说，所有为节点的字段所有为节点列表的字段条目。

## ast.**walk**(*node*)

​	递归地产生节点树中从 *node* 开始（包括 *node* 本身）的所有下级节点，没有确定的排序方式。 这在你仅想要原地修改节点而不关心具体上下文时很有用处。

## *class* ast.**NodeVisitor**

​	一个遍历抽象语法树并针对所找到的每个节点调用访问器函数的节点访问器基类。 该函数可能会返回一个由 [`visit()`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.NodeVisitor.visit) 方法所提供的值。

​	这个类应当被子类化，并由子类来添加访问器方法。

## **visit**(*node*)

​	访问一个节点。 默认实现会调用名为 `self.visit_*classname*` 的方法其中 *classname* 为节点类的名称，或者如果该方法不存在则为 [`generic_visit()`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.NodeVisitor.generic_visit)。

## **generic_visit**(*node*)

​	该访问器会在节点的所有子节点上调用 [`visit()`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.NodeVisitor.visit)。

​	请注意所有包含自定义访问器方法的节点的子节点将不会被访问除非访问器调用了 [`generic_visit()`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.NodeVisitor.generic_visit) 或是自行访问它们。

## **visit_Constant**(*node*)

​	处理所有常量节点。

​	如果你想在遍历期间应用对节点的修改则请不要使用 [`NodeVisitor`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.NodeVisitor)。 对此目的可使用一个允许修改的特殊访问器 ([`NodeTransformer`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.NodeTransformer))。

*自 3.8 版本弃用:* `visit_Num()`, `visit_Str()`, `visit_Bytes()`, `visit_NameConstant()` 和 `visit_Ellipsis()` 等方法现在已被弃用并且在未来的 Python 版本中将不再被调用。 请添加 [`visit_Constant()`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.NodeVisitor.visit_Constant) 方法来处理所有常量节点。

## *class* ast.**NodeTransformer**

​	子类 [`NodeVisitor`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.NodeVisitor) 用于遍历抽象语法树，并允许修改节点。

[`NodeTransformer`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.NodeTransformer) 将遍历抽象语法树并使用visitor方法的返回值去替换或移除旧节点。如果visitor方法的返回值为 `None` , 则该节点将从其位置移除，否则将替换为返回值。当返回值是原始节点时，无需替换。

​	如下是一个转换器示例，它将所有出现的名称 (`foo`) 重写为 `data['foo']`:

```
class RewriteName(NodeTransformer):

    def visit_Name(self, node):
        return Subscript(
            value=Name(id='data', ctx=Load()),
            slice=Constant(value=node.id),
            ctx=node.ctx
        )
```

​	请记住如果你正在操作的节点具有子节点，你必须先自行转换这些子节点或是为该节点调用 [`generic_visit()`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.NodeVisitor.generic_visit) 方法。

​	对于属于语句集合（适用于所有语句节点）的节点，访问者还可以返回节点列表而不仅仅是单个节点。

​	如果 [`NodeTransformer`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.NodeTransformer) 引入了新的（不属于原节点树的一部分的）节点而没有给出它们的位置信息（如 [`lineno`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.AST.lineno) 等），则应当调用 [`fix_missing_locations()`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.fix_missing_locations) 并传入新的子节点树来重新计算位置信息:

```
tree = ast.parse('foo', mode='eval')
new_tree = fix_missing_locations(RewriteName().visit(tree))
```

​	通常你可以像这样使用转换器:

```
node = YourTransformer().visit(node)
```

## ast.**dump**(*node*, *annotate_fields=True*, *include_attributes=False*, *, *indent=None*, *show_empty=False*)

​	返回 *node* 中树结构的格式化转储。 这主要适用于调试目的。 如果 *annotate_fields* 为真值（默认），返回的字符串将显示字段的名称和值。 如果 *annotate_fields* 为假值，结果字符串将通过省略无歧义的字段名称变得更为紧凑。 默认情况下不会转储行号和列偏移等属性。 如果需要，可将 *include_attributes* 设为真值。

​	如果 *indent* 是一个非负整数或者字符串，那么节点树将被美化输出为指定的缩进级别。 如果缩进级别为 0、负数或者 `""` 则将只插入换行符。 `None` (默认) 将选择最紧凑的表示形式。 使用一个正整数将让每个级别缩进相应数量的空格。 如果 *indent* 是一个字符串 (如 `"\t"`)，该字符串会被用于缩进每个级别。

​	如果 *show_empty* 为 `False` (默认值)，则空列表和值为 `None` 的字段将在输出中省略。

*在 3.9 版本发生变更:* 添加了 *indent* 选项。

*在 3.13 版本发生变更:* 增加了 *show_empty* 选项。



``` python
>>> print(ast.dump(ast.parse("""\
... async def f():
...     await other_func()
... """), indent=4, show_empty=True))
Module(
    body=[
        AsyncFunctionDef(
            name='f',
            args=arguments(
                posonlyargs=[],
                args=[],
                kwonlyargs=[],
                kw_defaults=[],
                defaults=[]),
            body=[
                Expr(
                    value=Await(
                        value=Call(
                            func=Name(id='other_func', ctx=Load()),
                            args=[],
                            keywords=[])))],
            decorator_list=[],
            type_params=[])],
    type_ignores=[])
```



## 编译器旗标

​	下列旗标可被传给 [`compile()`](https://docs.python.org/zh-cn/3.13/library/functions.html#compile) 用来改变程序编译的效果:

## ast.**PyCF_ALLOW_TOP_LEVEL_AWAIT**

​	启用对最高层级 `await`, `async for`, `async with` 以及异步推导式的支持。

> Added in version 3.8.
>

## ast.**PyCF_ONLY_AST**

​	生成并返回一个抽象语法树而不是返回一个已编译的代码对象。

## ast.**PyCF_OPTIMIZED_AST**

​	返回的 AST 已根据 [`compile()`](https://docs.python.org/zh-cn/3.13/library/functions.html#compile) 或 [`ast.parse()`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.parse) 中的 *optimize* 参数进行了优化。

> Added in version 3.13.
>

## ast.**PyCF_TYPE_COMMENTS**

​	启用对 [**PEP 484**](https://peps.python.org/pep-0484/) 和 [**PEP 526**](https://peps.python.org/pep-0526/) 风格的类型注释的支持 (`# type: <type>`, `# type: ignore <stuff>`)。

> Added in version 3.8.
>



## 命令行用法

> Added in version 3.9.
>

[`ast`](https://docs.python.org/zh-cn/3.13/library/ast.html#module-ast) 模块可以在命令行下作为脚本来执行。 具体做法非常简单:

```
python -m ast [-m <mode>] [-a] [infile]
```

​	可以接受以下选项：

## **-h**, **--help**

​	显示帮助信息并退出。

## **-m** <mode>

## **--mode** <mode>

​	指明哪种代码必须被编译，相当于 [`parse()`](https://docs.python.org/zh-cn/3.13/library/ast.html#ast.parse) 中的 *mode* 参数。

## **--no-type-comments**

​	不要解析类型注释。

## **-a**, **--include-attributes**

​	包括属性如行号和列偏移。

## **-i** <indent>

## **--indent** <indent>

​	AST 中节点的缩进（空格数）。

​	如果指定了 `infile` 则其内容将被解析为 AST 并转储至 stdout。 在其他情况下，将从 stdin 读取内容。

​	参见

 

[Green Tree Snakes](https://greentreesnakes.readthedocs.io/)，一个外部文档资源，包含处理 Python AST 的完整细节。

[ASTTokens](https://asttokens.readthedocs.io/en/latest/user-guide.html) 会为 Python AST 标注生成它们的源代码中的形符和文本的位置。 这对执行源代码转换的工具很有帮助。

[leoAst.py](https://leo-editor.github.io/leo-editor/appendices.html#leoast-py) 通过在形符和 ast 节点之间插入双向链接统一了 python 程序基于形符的和基于解析树的视图。

[LibCST](https://libcst.readthedocs.io/) 将代码解析为一个实体语法树（Concrete Syntax Tree），它看起来像是 ast 树而又保留了所有格式化细节。 它对构建自动化重构（codemod）应用和代码质量检查工具很有用处。

[Parso](https://parso.readthedocs.io/) 是一个支持错误恢复和不同 Python 版本的（在多个 Python 版本中）往返解析的 Python 解析器。 Parso 还能列出你的 Python 文件中的许多语法错误。
