+++
title = "cmd --- 对面向行的命令解释器的支持"
date = 2024-11-15T20:49:53+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/cmd.html](https://docs.python.org/zh-cn/3.13/library/cmd.html)
>
> 收录该文档的时间：`2024-11-15T20:49:53+08:00`

# `cmd` --- 对面向行的命令解释器的支持

**源代码:** [Lib/cmd.py](https://github.com/python/cpython/tree/3.13/Lib/cmd.py)

------

[`Cmd`]({{< ref "/library/frameworks/cmd#cmd.Cmd" >}}) 类提供简单框架用于编写面向行的命令解释器。 这些通常对测试工具，管理工具和原型有用，这些工具随后将被包含在更复杂的接口中。

## *class* cmd.**Cmd**(*completekey='tab'*, *stdin=None*, *stdout=None*)

​	一个 [`Cmd`]({{< ref "/library/frameworks/cmd#cmd.Cmd" >}}) 实例或子类实例是面向行的解释器框架结构。 实例化 [`Cmd`]({{< ref "/library/frameworks/cmd#cmd.Cmd" >}}) 本身是没有充分理由的， 它作为自定义解释器类的超类是非常有用的为了继承 [`Cmd`]({{< ref "/library/frameworks/cmd#cmd.Cmd" >}}) 的方法并且封装动作方法。

​	可选参数 *completekey* 是完成键的 [`readline`]({{< ref "/library/text/readline#module-readline" >}}) 名称；默认是 Tab 。如果 *completekey* 不是 [`None`]({{< ref "/library/constants#None" >}}) 并且 [`readline`]({{< ref "/library/text/readline#module-readline" >}}) 是可用的， 命令完成会自动完成。

​	默认值 `'tab'` 会被特殊对待，以使其在任何 [`readline.backend`]({{< ref "/library/text/readline#readline.backend" >}}) 上都指向 Tab 键。 根据特殊规则，如果 [`readline.backend`]({{< ref "/library/text/readline#readline.backend" >}}) 是 `editline`，`Cmd` 将使用 `'^I'` 而不是 `'tab'`。 请注意其它的值不会以这种方式来处理，并可能仅适用于特定的后端。

​	可选参数 *stdin* 和 *stdout* 指定了Cmd实例或子类实例将用于输入和输出的输入和输出文件对象。如果没有指定，他们将默认为 [`sys.stdin`]({{< ref "/library/python/sys#sys.stdin" >}}) 和 [`sys.stdout`]({{< ref "/library/python/sys#sys.stdout" >}}) 。

​	如果你想要使用一个给定的 *stdin* ，确保将实例的 [`use_rawinput`]({{< ref "/library/frameworks/cmd#cmd.Cmd.use_rawinput" >}}) 属性设置为 `False` ，否则 *stdin* 将被忽略。

*在 3.13 版本发生变更:* 对于 `editline` 来说 `completekey='tab'` 将被替换为 `'^I'`。



## Cmd 对象

[`Cmd`]({{< ref "/library/frameworks/cmd#cmd.Cmd" >}}) 实例有下列方法：

## Cmd.**cmdloop**(*intro=None*)

​	反复发出提示，接受输入，从收到的输入中解析出一个初始前缀，并分派给操作方法，将其余的行作为参数传递给它们。

​	可选参数是在第一个提示之前发布的横幅或介绍字符串（这将覆盖 [`intro`]({{< ref "/library/frameworks/cmd#cmd.Cmd.intro" >}}) 类属性）。

​	如果 [`readline`]({{< ref "/library/text/readline#module-readline" >}}) 继承模块被加载，输入将自动继承类似 **bash**的历史列表编辑（例如， Control-P 滚动回到最后一个命令， Control-N 转到下一个命令，以 Control-F 非破坏性的方式向右 Control-B 移动光标，破坏性地等）。

​	输入的文件结束符被作为字符串传回 `'EOF'` 。

​	当且仅当命令名称 `foo` 具有 `do_foo()` 方法时解释器实例才会识别它。 存在一种特殊情况，以字符 `'?'` 开头的行将被分派给 [`do_help()`]({{< ref "/library/frameworks/cmd#cmd.Cmd.do_help" >}}) 方法。 还存在另一种特殊情况，以字符 `'!'` 开头的行将被分派给 `do_shell()` 方法（如果定义了该方法的话）。

​	当 [`postcmd()`]({{< ref "/library/frameworks/cmd#cmd.Cmd.postcmd" >}}) 方法返回真值时此方法将返回。 [`postcmd()`]({{< ref "/library/frameworks/cmd#cmd.Cmd.postcmd" >}}) 的 *stop* 参数是命令对应的 `do_*()` 方法的返回值。

​	如果启用了补全，则会自动完成命令的补全，命令参数的补全则是通过调用 `complete_foo()` 并附带参数 *text*, *line*, *begidx* 和 *endidx* 来完成的。 *text* 是我们要尝试匹配的字符串前缀：所有被返回的匹配必须以它为开头。 *line* 是去除了开头空白符的当前输入行，*begidx* 和 *endidx* 是前缀文本的开始和结束索引号，它们可被用来根据参数所在的位置提供不同的补全。

## Cmd.**do_help**(*arg*)

​	所有 [`Cmd`]({{< ref "/library/frameworks/cmd#cmd.Cmd" >}}) 的子类都继承了一个预定义的 `do_help()`。 调用该方法时传入一个参数 `'bar'`，将发起调用对应的方法 `help_bar()`，如果该方法不存在，则将打印 `do_bar()` 的文档字符串，如果有文档字符串的话。 在没有参数的情况下，`do_help()` 将列出所有可用的帮助主题（即任何具有对应的 `help_*()` 方法的命令或具有文档字符串的命令），还会列出任何未写入文档的命令。

## Cmd.**onecmd**(*str*)

​	解释该参数就好像它是为响应提示而键入的一样。 此方法可以被重写，但通常不需要这样做；请参阅针对有用的执行钩子的 [`precmd()`]({{< ref "/library/frameworks/cmd#cmd.Cmd.precmd" >}}) 和 [`postcmd()`]({{< ref "/library/frameworks/cmd#cmd.Cmd.postcmd" >}}) 方法。 返回值是一个指明解释器对命令的解释是否应停止的旗标。 如果对于命令 *str* 存在 `do_*()` 方法，则将返回该方法的返回值，否则将返回 [`default()`]({{< ref "/library/frameworks/cmd#cmd.Cmd.default" >}}) 方法的返回值。

## Cmd.**emptyline**()

​	在响应提示输入空行时调用的方法。如果此方法未被覆盖，则重复输入的最后一个非空命令。

## Cmd.**default**(*line*)

​	当命令前缀不能被识别的时候在输入行调用的方法。如果此方法未被覆盖，它将输出一个错误信息并返回。

## Cmd.**completedefault**(*text*, *line*, *begidx*, *endidx*)

​	当没有命令专属的 `complete_*()` 方法可供使用时将被调用以完成输入行的方法。 在默认情况下，它将返回一个空列表。

## Cmd.**columnize**(*list*, *displaywidth=80*)

​	调用以将一个字符串列表显示为紧凑的列集的方法。 每列的宽度仅够显示其内容。 各列之间以两个空格分隔以保证可读性。

## Cmd.**precmd**(*line*)

​	钩方法在命令行 *line* 被解释之前执行，但是在输入提示被生成和发出后。这个方法是一个在 [`Cmd`]({{< ref "/library/frameworks/cmd#cmd.Cmd" >}}) 中的存根；它的存在是为了被子类覆盖。返回值被用作 [`onecmd()`]({{< ref "/library/frameworks/cmd#cmd.Cmd.onecmd" >}}) 方法执行的命令； [`precmd()`]({{< ref "/library/frameworks/cmd#cmd.Cmd.precmd" >}}) 的实现或许会重写命令或者简单的返回 *line* 不变。

## Cmd.**postcmd**(*stop*, *line*)

​	钩方法只在命令调度完成后执行。这个方法是一个在 [`Cmd`]({{< ref "/library/frameworks/cmd#cmd.Cmd" >}}) 中的存根；它的存在是为了子类被覆盖。 *line* 是被执行的命令行， *stop* 是一个表示在调用 [`postcmd()`]({{< ref "/library/frameworks/cmd#cmd.Cmd.postcmd" >}}) 之后是否终止执行的标志；这将作为 [`onecmd()`]({{< ref "/library/frameworks/cmd#cmd.Cmd.onecmd" >}}) 方法的返回值。这个方法的返回值被用作与 *stop* 相关联的内部标志的新值；返回 false 将导致解释继续。

## Cmd.**preloop**()

​	钩方法当 [`cmdloop()`]({{< ref "/library/frameworks/cmd#cmd.Cmd.cmdloop" >}}) 被调用时执行一次。方法是一个在 [`Cmd`]({{< ref "/library/frameworks/cmd#cmd.Cmd" >}}) 中的存根；它的存在是为了被子类覆盖。

## Cmd.**postloop**()

​	钩方法在 [`cmdloop()`]({{< ref "/library/frameworks/cmd#cmd.Cmd.cmdloop" >}}) 即将返回时执行一次。这个方法是一个在 [`Cmd`]({{< ref "/library/frameworks/cmd#cmd.Cmd" >}}) 中的存根；它的存在是为了被子类覆盖。

​	Instances of [`Cmd`]({{< ref "/library/frameworks/cmd#cmd.Cmd" >}}) subclasses have some public instance variables:

## Cmd.**prompt**

​	发出提示以请求输入。

## Cmd.**identchars**

​	接受命令前缀的字符串。

## Cmd.**lastcmd**

​	看到最后一个非空命令前缀。

## Cmd.**cmdqueue**

​	排队的输入行列表。当需要新的输入时，在 [`cmdloop()`]({{< ref "/library/frameworks/cmd#cmd.Cmd.cmdloop" >}}) 中检查 cmdqueue 列表；如果它不是空的，它的元素将被按顺序处理，就像在提示符处输入一样。

## Cmd.**intro**

​	要作为简介或横幅发出的字符串。 可以通过给 [`cmdloop()`]({{< ref "/library/frameworks/cmd#cmd.Cmd.cmdloop" >}}) 方法一个参数来覆盖它。

## Cmd.**doc_header**

​	如果帮助输出具有记录命令的段落，则发出头文件。

## Cmd.**misc_header**

​	如果帮助输出包含一个杂项帮助主题小节时（也就是说，存在没有对应 `do_*()` 方法的 `help_*()` 方法）要发出的标题。

## Cmd.**undoc_header**

​	如果帮助输出包含一个未写入文档的命令小节时（也就是说，存在没有对应 `help_*()` 方法的 `do_*()` 方法）要发出的标题。

## Cmd.**ruler**

​	用于在帮助信息标题的下方绘制分隔符的字符，如果为空，则不绘制标尺线。这个字符默认是 `'='` 。

## Cmd.**use_rawinput**

​	一个旗标，默认为真值。 如为真值，[`cmdloop()`]({{< ref "/library/frameworks/cmd#cmd.Cmd.cmdloop" >}}) 将使用 [`input()`]({{< ref "/library/functions#input" >}}) 显示一条提示并读取下一个命令；如为假值，则将使用 [`sys.stdout.write()`]({{< ref "/library/python/sys#sys.stdout" >}}) 和 [`sys.stdin.readline()`]({{< ref "/library/python/sys#sys.stdin" >}})。 （这意味着通过在受支持的系统上导入 [`readline`]({{< ref "/library/text/readline#module-readline" >}})，解释器将自动支持类似 **Emacs** 的行编辑和命令历史按键操作。）



## Cmd 例子

​	The [`cmd`]({{< ref "/library/frameworks/cmd#module-cmd" >}}) module is mainly useful for building custom shells that let a user work with a program interactively.

​	这部分提供了一个简单的例子来介绍如何使用一部分在 [`turtle`]({{< ref "/library/frameworks/turtle#module-turtle" >}}) 模块中的命令构建一个 shell 。

​	基本 turtle 命令比如 [`forward()`]({{< ref "/library/frameworks/turtle#turtle.forward" >}}) 将被添加到一个具有名为 `do_forward()` 的方法的 [`Cmd`]({{< ref "/library/frameworks/cmd#cmd.Cmd" >}}) 子类。 参数将被转换为数字并发送给 turtle 模块。 文档字符串将被用于 shell 所提供的帮助工具。

​	该示例还包括一个通过 [`precmd()`]({{< ref "/library/frameworks/cmd#cmd.Cmd.precmd" >}}) 方法实现的基本录制和回放工具，这个方法负责将输入转换为小写形式并将命令写入到文件。 `do_playback()` 方法将读取文件并将被录制的命令添加到 [`cmdqueue`]({{< ref "/library/frameworks/cmd#cmd.Cmd.cmdqueue" >}}) 用于立即回放:

```
import cmd, sys
from turtle import *

class TurtleShell(cmd.Cmd):
    intro = 'Welcome to the turtle shell.   Type help or ? to list commands.\n'
    prompt = '(turtle) '
    file = None

    # ----- 基本 turtle 命令 -----
    def do_forward(self, arg):
        'Move the turtle forward by the specified distance:  FORWARD 10'
        forward(*parse(arg))
    def do_right(self, arg):
        'Turn turtle right by given number of degrees:  RIGHT 20'
        right(*parse(arg))
    def do_left(self, arg):
        'Turn turtle left by given number of degrees:  LEFT 90'
        left(*parse(arg))
    def do_goto(self, arg):
        'Move turtle to an absolute position with changing orientation.  GOTO 100 200'
        goto(*parse(arg))
    def do_home(self, arg):
        'Return turtle to the home position:  HOME'
        home()
    def do_circle(self, arg):
        'Draw circle with given radius an options extent and steps:  CIRCLE 50'
        circle(*parse(arg))
    def do_position(self, arg):
        'Print the current turtle position:  POSITION'
        print('Current position is %d %d\n' % position())
    def do_heading(self, arg):
        'Print the current turtle heading in degrees:  HEADING'
        print('Current heading is %d\n' % (heading(),))
    def do_color(self, arg):
        'Set the color:  COLOR BLUE'
        color(arg.lower())
    def do_undo(self, arg):
        'Undo (repeatedly) the last turtle action(s):  UNDO'
    def do_reset(self, arg):
        'Clear the screen and return turtle to center:  RESET'
        reset()
    def do_bye(self, arg):
        'Stop recording, close the turtle window, and exit:  BYE'
        print('Thank you for using Turtle')
        self.close()
        bye()
        return True

    # ----- 记录和回放 -----
    def do_record(self, arg):
        'Save future commands to filename:  RECORD rose.cmd'
        self.file = open(arg, 'w')
    def do_playback(self, arg):
        'Playback commands from a file:  PLAYBACK rose.cmd'
        self.close()
        with open(arg) as f:
            self.cmdqueue.extend(f.read().splitlines())
    def precmd(self, line):
        line = line.lower()
        if self.file and 'playback' not in line:
            print(line, file=self.file)
        return line
    def close(self):
        if self.file:
            self.file.close()
            self.file = None

def parse(arg):
    'Convert a series of zero or more numbers to an argument tuple'
    return tuple(map(int, arg.split()))

if __name__ == '__main__':
    TurtleShell().cmdloop()
```

​	这是一个示例会话，其中 turtle shell 显示帮助功能，使用空行重复命令，以及简单的记录和回放功能：

```
Welcome to the turtle shell.   Type help or ? to list commands.

(turtle) ?

Documented commands (type help <topic>):
========================================
bye     color    goto     home  playback  record  right
circle  forward  heading  left  position  reset   undo

(turtle) help forward
Move the turtle forward by the specified distance:  FORWARD 10
(turtle) record spiral.cmd
(turtle) position
Current position is 0 0

(turtle) heading
Current heading is 0

(turtle) reset
(turtle) circle 20
(turtle) right 30
(turtle) circle 40
(turtle) right 30
(turtle) circle 60
(turtle) right 30
(turtle) circle 80
(turtle) right 30
(turtle) circle 100
(turtle) right 30
(turtle) circle 120
(turtle) right 30
(turtle) circle 120
(turtle) heading
Current heading is 180

(turtle) forward 100
(turtle)
(turtle) right 90
(turtle) forward 100
(turtle)
(turtle) right 90
(turtle) forward 400
(turtle) right 90
(turtle) forward 500
(turtle) right 90
(turtle) forward 400
(turtle) right 90
(turtle) forward 300
(turtle) playback spiral.cmd
Current position is 0 0

Current heading is 0

Current heading is 180

(turtle) bye
Thank you for using Turtle
```
