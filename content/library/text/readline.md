+++
title = "readline --- GNU readline 接口"
date = 2024-11-15T11:04:21+08:00
weight = 60
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/readline.html](https://docs.python.org/zh-cn/3.13/library/readline.html)
>
> 收录该文档的时间：`2024-11-15T11:04:21+08:00`

# `readline` --- GNU readline 接口

------

[`readline`]({{< ref "/library/text/readline#module-readline" >}}) 模块定义了许多方便从 Python 解释器完成和读取/写入历史文件的函数。 此模块可以直接使用，或通过支持在交互提示符下完成 Python 标识符的 [`rlcompleter`]({{< ref "/library/text/rlcompleter#module-rlcompleter" >}}) 模块使用。 使用此模块进行的设置会同时影响解释器的交互提示符以及内置 [`input()`]({{< ref "/library/functions#input" >}}) 函数提供的提示符。

​	Readline 的按键绑定可以通过一个初始化文件来配置，通常是你的用户目录中的 `.inputrc`。 请参阅 GNU Readline 手册中的 [Readline 初始化文件](https://tiswww.cwru.edu/php/chet/readline/rluserman.html#Readline-Init-File) 来了解有关该文件的格式和允许的结构，以及 Readline 库的一般功能。

[Availability]({{< ref "/library/intro#availability" >}}): not Android, not iOS, not WASI.

​	此模块在 [移动平台]({{< ref "/library/intro#mobile-availability" >}}) 或 [WebAssembly 平台]({{< ref "/library/intro#wasm-availability" >}}) 上不受支持。

​备注
 

​	下层的 Readline 库 API 可能使用 `editline` (`libedit`) 库而不是 GNU readline 来实现。 在 macOS 上 [`readline`]({{< ref "/library/text/readline#module-readline" >}}) 模块会在运行时检测所使用的是哪个库。

​	用于 `editline` 的配置文件与 GNU readline 的不同。 如果你要在程序中载入配置字符串你可以使用 [`backend`]({{< ref "/library/text/readline#readline.backend" >}}) 来确定正在使用的是哪个库。

​	如果你是在 macOS 上使用 `editline`/`libedit` readline 模拟，则位于你的主目录中的初始化文件的名称为 `.editrc`。 例如，`~/.editrc` 中的以下内容将开启 *vi* 按键绑定和 TAB 补全:

```
python:bind -v
python:bind ^I rl_complete
```

​	另外请注意不同的库可能使用不同的历史文件格式。 当切换下层的库时，现有的历史文件可能会无法使用。

## readline.**backend**

​	被使用的下层 Readline 库的名称，可以是 `"readline"` 或 `"editline"`。

> Added in version 3.13.
>

## 初始化文件

​	下列函数与初始化文件和用户配置有关：

## readline.**parse_and_bind**(*string*)

​	执行在 *string* 参数中提供的初始化行。 此函数会调用底层库中的 `rl_parse_and_bind()`。

## readline.**read_init_file**([*filename*])

​	执行一个 readline 初始化文件。 默认文件名为最近所使用的文件名。 此函数会调用底层库中的 `rl_read_init_file()`。

## 行缓冲区

​	下列函数会在行缓冲区上操作。

## readline.**get_line_buffer**()

​	返回行缓冲区的当前内容 (底层库中的 `rl_line_buffer`)。

## readline.**insert_text**(*string*)

​	将文本插入行缓冲区的当前游标位置。 该函数会调用底层库中的 `rl_insert_text()`，但会忽略其返回值。

## readline.**redisplay**()

​	改变屏幕的显示以反映行缓冲区的当前内容。 该函数会调用底层库中的 `rl_redisplay()`。

## 历史文件

​	下列函数会在历史文件上操作：

## readline.**read_history_file**([*filename*])

​	载入一个 readline 历史文件，并将其添加到历史列表。 默认文件名为 `~/.history`。 此函数会调用底层库中的 `read_history()`。

## readline.**write_history_file**([*filename*])

​	将历史列表保存为 readline 历史文件，覆盖任何现有文件。 默认文件名为 `~/.history`。 此函数会调用底层库中的 `write_history()`。

## readline.**append_history_file**(*nelements*[, *filename*])

​	将历史列表的最后 *nelements* 项添加到历史文件。 默认文件名为 `~/.history`。 文件必须已存在。 此函数会调用底层库中的 `append_history()`。 此函数仅当 Python 编译包带有支持此功能的库版本时才会存在。

> Added in version 3.5.
>

## readline.**get_history_length**()

## readline.**set_history_length**(*length*)

​	设置或返回需要保存到历史文件的行数。 [`write_history_file()`]({{< ref "/library/text/readline#readline.write_history_file" >}}) 函数会通过调用底层库中的 `history_truncate_file()` 以使用该值来截取历史文件。 负值意味着不限制历史文件的大小。

## 历史列表

​	以下函数会在全局历史列表上操作：

## readline.**clear_history**()

​	清除当前历史。 此函数会调用底层库的 `clear_history()`。 此 Python 函数仅当 Python 编译包带有支持此功能的库版本时才会存在。

## readline.**get_current_history_length**()

​	返回历史列表的当前项数。 （此函数不同于 [`get_history_length()`]({{< ref "/library/text/readline#readline.get_history_length" >}})，后者是返回将被写入历史文件的最大行数。）

## readline.**get_history_item**(*index*)

​	返回序号为 *index* 的历史条目的当前内容。 条目序号从一开始。 此函数会调用底层库中的 `history_get()`。

## readline.**remove_history_item**(*pos*)

​	从历史列表中移除指定位置上的历史条目。 条目位置从零开始。 此函数会调用底层库中的 `remove_history()`。

## readline.**replace_history_item**(*pos*, *line*)

​	将指定位置上的历史条目替换为 *line*。 条目位置从零开始。 此函数会调用底层库中的 `replace_history_entry()`。

## readline.**add_history**(*line*)

​	将 *line* 添加到历史缓冲区，相当于是最近输入的一行。 此函数会调用底层库中的 `add_history()`。

## readline.**set_auto_history**(*enabled*)

​	启用或禁用当通过 readline 读取输入时自动调用 `add_history()`。 *enabled* 参数应为一个布尔值，当其为真值时启用自动历史，当其为假值时禁用自动历史。

> Added in version 3.6.
>

**CPython 实现细节：** 自动历史将默认启用，对此设置的改变不会在多个会话中保持。

## 启动钩子

## readline.**set_startup_hook**([*function*])

​	设置或移除底层库的 `rl_startup_hook` 回调所发起调用的函数。 如果指定了 *function*，它将被用作新的钩子函数；如果省略或为 `None`，任何已安装的函数将被移除。 钩子函数将在 readline 打印第一个提示信息之前不带参数地被调用。

## readline.**set_pre_input_hook**([*function*])

​	设置或移除底层库的 `rl_pre_input_hook` 回调所发起调用的函数。 如果指定了 *function*，它将被用作新的钩子函数；如果省略或为 `None`，任何已安装的函数将被移除。 钩子函数将在打印第一个提示信息之后、readline 开始读取输入字符之前不带参数地被调用。 此函数仅当 Python 编译包带有支持此功能的库版本时才会存在。



## Completion

​	以下函数与自定义单词补全函数的实现有关。 这通常使用 Tab 键进行操作，能够提示并自动补全正在输入的单词。 默认情况下，Readline 设置为由 [`rlcompleter`]({{< ref "/library/text/rlcompleter#module-rlcompleter" >}}) 来补全交互模式解释器的 Python 标识符。 如果 [`readline`]({{< ref "/library/text/readline#module-readline" >}}) 模块要配合自定义的补全函数来使用，则需要设置不同的单词分隔符。

## readline.**set_completer**([*function*])

​	设置或移除补全函数。 如果指定了 *function*，它将被用作新的补全函数；如果省略或为 `None`，任何已安装的补全函数将被移除。 补全函数的调用形式为 `function(text, state)`，其中 *state* 为 `0`, `1`, `2`, ..., 直至其返回一个非字符串值。 它应当返回下一个以 *text* 开头的候选补全内容。

​	已安装的补全函数将由传递给底层库中 `rl_completion_matches()` 的 *entry_func* 回调函数来发起调用。 *text* 字符串来自于底层库中 `rl_attempted_completion_function` 回调函数的第一个形参。

## readline.**get_completer**()

​	获取补全函数，如果没有设置补全函数则返回 `None`。

## readline.**get_completion_type**()

​	获取正在尝试的补全类型。 此函数会将底层库中的 `rl_completion_type` 变量作为一个整数返回。

## readline.**get_begidx**()

## readline.**get_endidx**()

​	获取完全范围的开始和结束索引号。 这些索引号就是传递给下层库的 `rl_attempted_completion_function` 回调的 *start* 和 *end* 参数。 具体值在同一个输入编辑场景中可能不同，具体取决于下层的 C readline 实现。 例如：已知 libedit 的行为就不同于 libreadline。

## readline.**set_completer_delims**(*string*)

## readline.**get_completer_delims**()

​	设置或获取补全的单词分隔符。 此分隔符确定了要考虑补全的单词的开始和结束位置（补全域）。 这些函数会访问底层库的 `rl_completer_word_break_characters` 变量。

## readline.**set_completion_display_matches_hook**([*function*])

​	设置或移除补全显示函数。 如果指定了 *function*，它将被用作新的补全显示函数；如果省略或为 `None`，任何已安装的补全显示函数将被移除。 此函数会设置或清除底层库的 `rl_completion_display_matches_hook` 回调函数。 补全显示函数会在每次需要显示匹配项时以 `function(substitution, [matches], longest_match_length)` 的形式被调用。



## 示例

​	以下示例演示了如何使用 [`readline`]({{< ref "/library/text/readline#module-readline" >}}) 模块的历史读取或写入函数来自动加载和保存用户主目录下名为 `.python_history` 的历史文件。 以下代码通常应当在交互会话期间从用户的 [`PYTHONSTARTUP`]({{< ref "/using/cmdline#envvar-PYTHONSTARTUP" >}}) 文件自动执行。

```
import atexit
import os
import readline

histfile = os.path.join(os.path.expanduser("~"), ".python_history")
try:
    readline.read_history_file(histfile)
    # 默认的历史长度为 -1 (无限)，这可能导致增长失控
    readline.set_history_length(1000)
except FileNotFoundError:
    pass

atexit.register(readline.write_history_file, histfile)
```

​	此代码实际上会在 Python 运行于 [交互模式]({{< ref "/tutorial/interpreter#tut-interactive" >}}) 时自动运行 (参见 [Readline 配置]({{< ref "/library/python/site#rlcompleter-config" >}}))。

​	以下示例实现了同样的目标，但是通过只添加新历史的方式来支持并发的交互会话。

```
import atexit
import os
import readline
histfile = os.path.join(os.path.expanduser("~"), ".python_history")

try:
    readline.read_history_file(histfile)
    h_len = readline.get_current_history_length()
except FileNotFoundError:
    open(histfile, 'wb').close()
    h_len = 0

def save(prev_h_len, histfile):
    new_h_len = readline.get_current_history_length()
    readline.set_history_length(1000)
    readline.append_history_file(new_h_len - prev_h_len, histfile)
atexit.register(save, h_len, histfile)
```

​	以下示例扩展了 [`code.InteractiveConsole`]({{< ref "/library/custominterp/code#code.InteractiveConsole" >}}) 类以支持历史保存/恢复。

```
import atexit
import code
import os
import readline

class HistoryConsole(code.InteractiveConsole):
    def __init__(self, locals=None, filename="<console>",
                 histfile=os.path.expanduser("~/.console-history")):
        code.InteractiveConsole.__init__(self, locals, filename)
        self.init_history(histfile)

    def init_history(self, histfile):
        readline.parse_and_bind("tab: complete")
        if hasattr(readline, "read_history_file"):
            try:
                readline.read_history_file(histfile)
            except FileNotFoundError:
                pass
            atexit.register(self.save_history, histfile)

    def save_history(self, histfile):
        readline.set_history_length(1000)
        readline.write_history_file(histfile)
```
