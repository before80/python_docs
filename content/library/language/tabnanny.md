+++
title = "tabnanny --- 检测有歧义的缩进"
date = 2024-11-15T21:23:55+08:00
weight = 50
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/tabnanny.html](https://docs.python.org/zh-cn/3.13/library/tabnanny.html)
>
> 收录该文档的时间：`2024-11-15T21:23:55+08:00`

# `tabnanny` --- 检测有歧义的缩进

**源代码:** [Lib/tabnanny.py](https://github.com/python/cpython/tree/3.13/Lib/tabnanny.py)

------

​	目前，该模块旨在作为脚本调用。但是可以使用下面描述的 [`check()`](https://docs.python.org/zh-cn/3.13/library/tabnanny.html#tabnanny.check) 函数将其导入IDE。

​	备注

 

​	此模块提供的API可能会在将来的版本中更改；此类更改可能无法向后兼容。

## tabnanny.**check**(*file_or_dir*)

​	如果 *file_or_dir* 是目录而非符号链接，则递归地在名为 *file_or_dir* 的目录树中下行，沿途检查所有 `.py` 文件。 如果 *file_or_dir* 是一个普通 Python 源文件，将检查其中的空格相关问题。 诊断消息将使用 [`print()`](https://docs.python.org/zh-cn/3.13/library/functions.html#print) 函数写入到标准输出。

## tabnanny.**verbose**

​	此旗标指明是否打印详细消息。 如果作为脚本调用则是通过 `-v` 选项来增加。

## tabnanny.**filename_only**

​	此旗标指明是否只打印包含空格相关问题文件的文件名。 如果作为脚本调用则是通过 `-q` 选项来设为真值。

## *exception* tabnanny.**NannyNag**

​	如果检测到模糊缩进则由 [`process_tokens()`](https://docs.python.org/zh-cn/3.13/library/tabnanny.html#tabnanny.process_tokens) 引发。 在 [`check()`](https://docs.python.org/zh-cn/3.13/library/tabnanny.html#tabnanny.check) 中捕获并处理。

## tabnanny.**process_tokens**(*tokens*)

​	此函数由 [`check()`](https://docs.python.org/zh-cn/3.13/library/tabnanny.html#tabnanny.check) 用来处理由 [`tokenize`](https://docs.python.org/zh-cn/3.13/library/tokenize.html#module-tokenize) 模块所生成的标记。

​	参见

## 模块 [`tokenize`](https://docs.python.org/zh-cn/3.13/library/tokenize.html#module-tokenize)

​	用于Python源代码的词法扫描程序。
