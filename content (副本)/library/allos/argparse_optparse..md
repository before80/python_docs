+++
title = "argparse --- 用于命令行选项、参数和子命令的解析器"
date = 2024-11-15T12:09:25+08:00
weight = 30
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/howto/argparse.html](https://docs.python.org/zh-cn/3.13/howto/argparse.html)
>
> 收录该文档的时间：`2024-11-15T12:09:25+08:00`

# Upgrading optparse code

Originally, the [`argparse`]({{< ref "/library/allos/argparse#module-argparse" >}}) module had attempted to maintain compatibility with [`optparse`]({{< ref "/library/superseded/optparse#module-optparse" >}}). However, [`optparse`]({{< ref "/library/superseded/optparse#module-optparse" >}}) was difficult to extend transparently, particularly with the changes required to support `nargs=` specifiers and better usage messages. When most everything in [`optparse`]({{< ref "/library/superseded/optparse#module-optparse" >}}) had either been copy-pasted over or monkey-patched, it no longer seemed practical to try to maintain the backwards compatibility.

The [`argparse`]({{< ref "/library/allos/argparse#module-argparse" >}}) module improves on the [`optparse`]({{< ref "/library/superseded/optparse#module-optparse" >}}) module in a number of ways including:

- Handling positional arguments.
- Supporting subcommands.
- Allowing alternative option prefixes like `+` and `/`.
- Handling zero-or-more and one-or-more style arguments.
- Producing more informative usage messages.
- Providing a much simpler interface for custom `type` and `action`.

A partial upgrade path from [`optparse`]({{< ref "/library/superseded/optparse#module-optparse" >}}) to [`argparse`]({{< ref "/library/allos/argparse#module-argparse" >}}):

- Replace all [`optparse.OptionParser.add_option()`]({{< ref "/library/superseded/optparse#optparse.OptionParser.add_option" >}}) calls with [`ArgumentParser.add_argument()`]({{< ref "/library/allos/argparse#argparse.ArgumentParser.add_argument" >}}) calls.
- Replace `(options, args) = parser.parse_args()` with `args = parser.parse_args()` and add additional [`ArgumentParser.add_argument()`]({{< ref "/library/allos/argparse#argparse.ArgumentParser.add_argument" >}}) calls for the positional arguments. Keep in mind that what was previously called `options`, now in the [`argparse`]({{< ref "/library/allos/argparse#module-argparse" >}}) context is called `args`.
- Replace [`optparse.OptionParser.disable_interspersed_args()`]({{< ref "/library/superseded/optparse#optparse.OptionParser.disable_interspersed_args" >}}) by using [`parse_intermixed_args()`]({{< ref "/library/allos/argparse#argparse.ArgumentParser.parse_intermixed_args" >}}) instead of [`parse_args()`]({{< ref "/library/allos/argparse#argparse.ArgumentParser.parse_args" >}}).
- Replace callback actions and the `callback_*` keyword arguments with `type` or `action` arguments.
- Replace string names for `type` keyword arguments with the corresponding type objects (e.g. int, float, complex, etc).
- Replace [`optparse.Values`]({{< ref "/library/superseded/optparse#optparse.Values" >}}) with [`Namespace`]({{< ref "/library/allos/argparse#argparse.Namespace" >}}) and [`optparse.OptionError`]({{< ref "/library/superseded/optparse#optparse.OptionError" >}}) and [`optparse.OptionValueError`]({{< ref "/library/superseded/optparse#optparse.OptionValueError" >}}) with [`ArgumentError`]({{< ref "/library/allos/argparse#argparse.ArgumentError" >}}).
- Replace strings with implicit arguments such as `%default` or `%prog` with the standard Python syntax to use dictionaries to format strings, that is, `%(default)s` and `%(prog)s`.
- Replace the OptionParser constructor `version` argument with a call to `parser.add_argument('--version', action='version', version='<the version>')`.
