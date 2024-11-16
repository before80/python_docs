+++
title = "curses --- 字符单元显示的终端处理"
date = 2024-11-15T12:09:25+08:00
weight = 80
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/curses.html](https://docs.python.org/zh-cn/3.13/library/curses.html)
>
> 收录该文档的时间：`2024-11-15T12:09:25+08:00`

# `curses` --- 字符单元显示的终端处理

**源代码:** [Lib/curses](https://github.com/python/cpython/tree/3.13/Lib/curses)

------

[`curses`]({{< ref "/library/allos/curses#module-curses" >}}) 模块提供了 curses 库的接口，这是可移植高级终端处理的事实标准。

​	虽然 curses 在 Unix 环境中使用最为广泛，但也有适用于 Windows，DOS 以及其他可能的系统的版本。此扩展模块旨在匹配 ncurses 的 API，这是一个部署在 Linux 和 Unix 的 BSD 变体上的开源 curses 库。

[Availability]({{< ref "/library/intro#availability" >}}): not Android, not iOS, not WASI.

​	此模块在 [移动平台]({{< ref "/library/intro#mobile-availability" >}}) 或 [WebAssembly 平台]({{< ref "/library/intro#wasm-availability" >}}) 上不受支持。

​备注
 

​	每当文档提到 **字符** 时，它可以被指定为一个整数，一个单字符 Unicode 字符串或者一个单字节的字节字符串。

​	每当此文档提到 **字符串** 时，它可以被指定为一个 Unicode 字符串或者一个字节字符串。

​参见
模块 [`curses.ascii`]({{< ref "/library/allos/curses_ascii#module-curses.ascii" >}})

​	在 ASCII 字符上工作的工具，无论你的区域设置是什么。

模块 [`curses.panel`]({{< ref "/library/allos/curses_panel#module-curses.panel" >}})

​	为 curses 窗口添加深度的面板栈扩展。

模块 [`curses.textpad`]({{< ref "/library/allos/curses#module-curses.textpad" >}})

​	用于使 curses 支持 **Emacs** 式绑定的可编辑文本部件。

[用 Python 进行 Curses 编程]({{< ref "/howto/curses#curses-howto" >}})

​	关于配合 Python 使用 curses 的教学材料，由 Andrew Kuchling 和 Eric Raymond 撰写。



## 函数

[`curses`]({{< ref "/library/allos/curses#module-curses" >}}) 模块定义了以下异常：

## *exception* curses.**error**

​	当 curses 库中函数返回一个错误时引发的异常。

​备注
 

​	只要一个函数或方法的 *x* 或 *y* 参数是可选项，它们会默认为当前光标位置。 而当 *attr* 是可选项时，它会默认为 [`A_NORMAL`]({{< ref "/library/allos/curses#curses.A_NORMAL" >}})。

[`curses`]({{< ref "/library/allos/curses#module-curses" >}}) 模块定义了以下函数：

## curses.**baudrate**()

​	以每秒比特数为单位返回终端输出速度。 在软件终端模拟器上它将具有一个固定的最高值。 此函数出于历史原因被包括；在以前，它被用于写输出循环以提供时间延迟，并偶尔根据线路速度来改变接口。

## curses.**beep**()

​	发出短促的提醒声音。

## curses.**can_change_color**()

​	根据程序员能否改变终端显示的颜色返回 `True` 或 `False`。

## curses.**cbreak**()

​	进入 cbreak 模式。 在 cbreak 模式（有时也称为“稀有”模式）通常的 tty 行缓冲会被关闭并且字符可以被一个一个地读取。 但是，与原始模式不同，特殊字符（中断、退出、挂起和流程控制）会在 tty 驱动和调用程序上保留其效果。 首先调用 [`raw()`]({{< ref "/library/allos/curses#curses.raw" >}}) 然后调用 [`cbreak()`]({{< ref "/library/allos/curses#curses.cbreak" >}}) 会将终端置于 cbreak 模式。

## curses.**color_content**(*color_number*)

​	返回颜色值 *color_number* 中红、绿和蓝（RGB）分量的强度，此强度值必须介于 `0` 和 `COLORS - 1` 之间。 返回一个 3 元组，其中包含给定颜色的 R,G,B 值，它们必须介于 `0` (无分量) 和 `1000` (最大分量) 之间。

## curses.**color_pair**(*pair_number*)

​	返回用于以指定颜色对显示文本的属性值。 仅支持前 256 个颜色对。 该属性值可与 [`A_STANDOUT`]({{< ref "/library/allos/curses#curses.A_STANDOUT" >}}), [`A_REVERSE`]({{< ref "/library/allos/curses#curses.A_REVERSE" >}}) 以及其他 `A_*` 属性组合使用。 [`pair_number()`]({{< ref "/library/allos/curses#curses.pair_number" >}}) 是此函数的对应操作。

## curses.**curs_set**(*visibility*)

​	设置光标状态。 *visibility* 可设为 `0`, `1` 或 `2` 表示不可见、正常与高度可见。 如果终端支持所请求的可见性，则返回之前的光标状态；否则会引发异常。 在许多终端上，“正常可见”模式为下划线光标而“高度可见”模式为方块形光标。

## curses.**def_prog_mode**()

​	将当前终端模式保存为 "program" 模式，即正在运行的程序使用 curses 的模式。 （与其相对的是 "shell" 模式，即程序不使用 curses。） 对 [`reset_prog_mode()`]({{< ref "/library/allos/curses#curses.reset_prog_mode" >}}) 的后续调用将恢复此模式。

## curses.**def_shell_mode**()

​	将当前终端模式保存为 "shell" 模式，即正在运行的程序不使用 curses 的模式。 （与其相对的是 "program" 模式，即程序使用 功能。） 对 [`reset_shell_mode()`]({{< ref "/library/allos/curses#curses.reset_shell_mode" >}}) 的后续调用将恢复此模式。

## curses.**delay_output**(*ms*)

​	在输出中插入 *ms* 毫秒的暂停。

## curses.**doupdate**()

​	更新物理屏幕。 curses 库会保留两个数据结构，一个代表当前物理屏幕的内容以及一个虚拟屏幕代表需要的后续状态。 [`doupdate()`]({{< ref "/library/allos/curses#curses.doupdate" >}}) 整体更新物理屏幕以匹配虚拟屏幕。

​	虚拟屏幕可以通过在写入操作例如在一个窗口上执行 [`addstr()`]({{< ref "/library/allos/curses#curses.window.addstr" >}}) 之后调用 [`noutrefresh()`]({{< ref "/library/allos/curses#curses.window.noutrefresh" >}}) 来刷新。 普通的 [`refresh()`]({{< ref "/library/allos/curses#curses.window.refresh" >}}) 调用只是简单的 `noutrefresh()` 加 `doupdate()`；如果你需要更新多个窗口，你可以通过在所有窗口上发出 `noutrefresh()` 调用再加单次 `doupdate()` 来提升性能并可减少屏幕闪烁。

## curses.**echo**()

​	进入 echo 模式。 在 echo 模式下，输入的每个字符都会在输入后回显到屏幕上。

## curses.**endwin**()

​	撤销库的初始化，使终端返回正常状态。

## curses.**erasechar**()

​	将用户的当前擦除字符以单字节字节串对象的形式返回。 在 Unix 操作系统下这是 curses 程序用来控制 tty 的属性，而不是由 curses 库本身来设置的。

## curses.**filter**()

​	如果要使用 [`filter()`]({{< ref "/library/allos/curses#curses.filter" >}}) 例程，它必须在调用 [`initscr()`]({{< ref "/library/allos/curses#curses.initscr" >}}) 之前被调用。 其效果是在这些调用期间，`LINES` 会被设为 `1`；`clear`, `cup`, `cud`, `cud1`, `cuu1`, `cuu`, `vpa` 等功能会被禁用；而 `home` 字符串会被设为 `cr` 的值。 其影响是光标会被限制在当前行内，屏幕刷新也是如此。 这可被用于启用单字符模式的行编辑而不触及屏幕的其余部分。

## curses.**flash**()

​	闪烁屏幕。 也就是将其改为反显并在很短的时间内将其改回原状。 有些人更喜欢这样的‘视觉响铃’而非 [`beep()`]({{< ref "/library/allos/curses#curses.beep" >}}) 所产生的听觉提醒信号。

## curses.**flushinp**()

​	刷新所有输入缓冲区。 这会丢弃任何已被用户输入但尚未被程序处理的预输入内容。

## curses.**getmouse**()

​	在 [`getch()`]({{< ref "/library/allos/curses#curses.window.getch" >}}) 返回 [`KEY_MOUSE`]({{< ref "/library/allos/curses#curses.KEY_MOUSE" >}}) 以发出鼠标事件信号之后，应当调用此方法来获取加入队列的鼠标事件，事件以一个 5 元组 `(id, x, y, z, bstate)` 来表示。 其中 *id* 为用于区分多个设备的 ID 值，而 *x*, *y*, *z* 为事件的坐标。 (*z* 目前未被使用。) *bstate* 为一个整数值，其各个比特位将被设置用来表示事件的类型，并将为下列常量中的一个或多个按位 OR 的结果，其中 *n* 是以 1 到 5 表示的键号: [`BUTTONn_PRESSED`]({{< ref "/library/allos/curses#curses.BUTTONn_PRESSED" >}}), [`BUTTONn_RELEASED`]({{< ref "/library/allos/curses#curses.BUTTONn_RELEASED" >}}), [`BUTTONn_CLICKED`]({{< ref "/library/allos/curses#curses.BUTTONn_CLICKED" >}}), [`BUTTONn_DOUBLE_CLICKED`]({{< ref "/library/allos/curses#curses.BUTTONn_DOUBLE_CLICKED" >}}), [`BUTTONn_TRIPLE_CLICKED`]({{< ref "/library/allos/curses#curses.BUTTONn_TRIPLE_CLICKED" >}}), [`BUTTON_SHIFT`]({{< ref "/library/allos/curses#curses.BUTTON_SHIFT" >}}), [`BUTTON_CTRL`]({{< ref "/library/allos/curses#curses.BUTTON_CTRL" >}}), [`BUTTON_ALT`]({{< ref "/library/allos/curses#curses.BUTTON_ALT" >}})。

> 在 3.10 版本发生变更: 现在 `BUTTON5_*` 常量如果是由下层 curses 库提供的则会对外公开。

## curses.**getsyx**()

​	将当前虚拟屏幕光标的坐标作为元组 `(y, x)` 返回。 如果 [`leaveok`]({{< ref "/library/allos/curses#curses.window.leaveok" >}}) 当前为 `True`，则返回 `(-1, -1)`。

## curses.**getwin**(*file*)

​	读取由之前的 [`window.putwin()`]({{< ref "/library/allos/curses#curses.window.putwin" >}}) 调用存储在文件中的窗口相关数据。 该例程随后将使用该数据创建并初始化一个新窗口，并返回这个新窗口对象。

## curses.**has_colors**()

​	如果终端能显示彩色则返回 `True`；否则返回 `False`。

## curses.**has_extended_color_support**()

​	如果此模块支持扩展颜色则返回 `True`；否则返回 `False`。 扩展颜色支持允许支持超过 16 种颜色的终端（例如 xterm-256color）支持超过 256 种颜色对。

​	扩展颜色支持要求 ncurses 版本为 6.1 或更新。

> Added in version 3.10.
>

## curses.**has_ic**()

​	如果终端具有插入和删除字符的功能则返回 `True`。 此函数仅是出于历史原因而被包括的，因为所有现代软件终端模拟器都具有这些功能。

## curses.**has_il**()

​	如果终端具有插入和删除字符功能，或者能够使用滚动区域来模拟这些功能则返回 `True`。 此函数仅是出于历史原因而被包括的，因为所有现代软件终端模拟器都具有这些功能。

## curses.**has_key**(*ch*)

​	接受一个键值 *ch*，并在当前终端类型能识别出具有该值的键时返回 `True`。

## curses.**halfdelay**(*tenths*)

​	用于半延迟模式，与 cbreak 模式的类似之处是用户所键入的字符会立即对程序可用。 但是，在阻塞 *tenths* 个十分之一秒之后，如果还未输入任何内容则将引发异常。 *tenths* 值必须为 `1` 和 `255` 之间的数字。 使用 [`nocbreak()`]({{< ref "/library/allos/curses#curses.nocbreak" >}}) 可退出半延迟模式。

## curses.**init_color**(*color_number*, *r*, *g*, *b*)

​	更改某个颜色的定义，接受要更改的颜色编号以及三个 RGB 值（表示红绿蓝三个分量的强度）。 *color_number* 的值必须为 `0` 和 `COLORS - 1` 之间的数字。 每个 *r*, *g*, *b* 值必须为 `0` 和 `1000` 之间的数字。 当使用 [`init_color()`]({{< ref "/library/allos/curses#curses.init_color" >}}) 时，出现在屏幕上的对应颜色会立即按照定定义来更改。 此函数在大多数终端上都是无操作的；它仅会在 [`can_change_color()`]({{< ref "/library/allos/curses#curses.can_change_color" >}}) 返回 `True` 时生效。

## curses.**init_pair**(*pair_number*, *fg*, *bg*)

​	更改某个颜色对的定义。 它接受三个参数：要更改的颜色对编号，前景色编号和背景色编号。 *pair_number* 值必须为 `1` 和 `COLOR_PAIRS - 1` 之间的数字（并且 `0` 号颜色对固定为黑底白字而无法更改）。 *fg* 和 *bg* 参数必须为 `0` 和 `COLORS - 1` 之间的数字，或者在调用 [`use_default_colors()`]({{< ref "/library/allos/curses#curses.use_default_colors" >}}) 之后则为 `-1`。 如果颜色对之前已被初始化，则屏幕会被刷新使得出现在屏幕上的该颜色会立即按照新定义来更改。

## curses.**initscr**()

​	初始化库。 返回代表整个屏幕的 [窗口]({{< ref "/library/allos/curses#curses-window-objects" >}}) 对象。

​备注
 

​	如果打开终端时发生错误，则下层的 curses 库可能会导致解释器退出。

## curses.**is_term_resized**(*nlines*, *ncols*)

​	如果 [`resize_term()`]({{< ref "/library/allos/curses#curses.resize_term" >}}) 会修改窗口结构则返回 `True`，否则返回 `False`。

## curses.**isendwin**()

​	如果 [`endwin()`]({{< ref "/library/allos/curses#curses.endwin" >}}) 已经被调用（即 curses 库已经被撤销初始化则返回 `True`。

## curses.**keyname**(*k*)

​	将编号为 *k* 的键名称作为字节串对象返回。 生成可打印 ASCII 字符的键名称就是键所对应的字符。 Ctrl-键组合的键名称则是一个两字节的字节串对象，它由插入符 (`b'^'`) 加对应的可打印 ASCII 字符组成。 Alt-键组合 (128--255) 的键名称则是由前缀 `b'M-'` 加对应的可打印 ASCII 字符组成的字节串对象。

## curses.**killchar**()

​	将用户的当前行删除字符以单字节字节串对象的形式返回。 在 Unix 操作系统下这是 curses 程序用来控制 tty 的属性，而不是由 curses 库本身来设置的。

## curses.**longname**()

​	返回一个字节串对象，其中包含描述当前终端的 terminfo 长名称字段。 详细描述的最大长度为 128 个字符。 它仅在调用 [`initscr()`]({{< ref "/library/allos/curses#curses.initscr" >}}) 之后才会被定义。

## curses.**meta**(*flag*)

​	如果 *flag* 为 `True`，则允许输入 8 比特位的字符。 如果 *flag* 为 `False`，则只允许 7 比特位的字符。

## curses.**mouseinterval**(*interval*)

​	以毫秒为单位设置能够被识别为点击的按下和事件事件之间可间隔的最长时间，并返回之前的间隔值。 默认值为 200 毫秒，即五分之一秒。

## curses.**mousemask**(*mousemask*)

​	设置要报告的鼠标事件，并返回一个元组 `(availmask, oldmask)`。 *availmask* 表明指定的鼠标事件中哪些可以被报告；当完全失败时将返回 `0`。 *oldmask* 是给定窗口的鼠标事件之前的掩码值。 如果从未调用此函数，则不会报告任何鼠标事件。

## curses.**napms**(*ms*)

​	休眠 *ms* 毫秒。

## curses.**newpad**(*nlines*, *ncols*)

​	创建并返回一个指向具有给定行数和列数新的面板数据结构的指针。 将面板作为窗口对象返回。

​	面板类似于窗口，区别在于它不受屏幕大小的限制，并且不必与屏幕的特定部分相关联。 面板可以在需要使用大窗口时使用，并且每次只需将窗口的一部分放在屏幕上。 面板不会发生自动刷新（例如由于滚动或输入回显）。 面板的 [`refresh()`]({{< ref "/library/allos/curses#curses.window.refresh" >}}) 和 [`noutrefresh()`]({{< ref "/library/allos/curses#curses.window.noutrefresh" >}}) 方法需要 6 个参数来指定面板要显示的部分以及要用于显示的屏幕位置。 这些参数是 *pminrow*, *pmincol*, *sminrow*, *smincol*, *smaxrow*, *smaxcol*；*p* 参数表示要显示的面板区域的左上角而 *s* 参数定义了要显示的面板区域在屏幕上的剪切框。

## curses.**newwin**(*nlines*, *ncols*)

## curses.**newwin**(*nlines*, *ncols*, *begin_y*, *begin_x*)

​	返回一个新的 [窗口]({{< ref "/library/allos/curses#curses-window-objects" >}})，其左上角位于 `(begin_y, begin_x)`，并且其高度/宽度为 *nlines*/*ncols*。

​	默认情况下，窗口将从指定位置扩展到屏幕的右下角。

## curses.**nl**()

​	进入 newline 模式。 此模式会在输入时将回车转换为换行符，并在输出时将换行符转换为回车加换行。 newline 模式会在初始时启用。

## curses.**nocbreak**()

​	退出 cbreak 模式。 返回具有行缓冲的正常 "cooked" 模式。

## curses.**noecho**()

​	退出 echo 模式。 关闭输入字符的回显。

## curses.**nonl**()

​	退出 newline 模式。 停止在输入时将回车转换为换行，并停止在输出时从换行到换行/回车的底层转换（但这不会改变 `addch('\n')` 的行为，此行为总是在虚拟屏幕上执行相当于回车加换行的操作）。 当停止转换时，curses 有时能使纵向移动加快一些；并且，它将能够在输入时检测回车键。

## curses.**noqiflush**()

​	当使用 `noqiflush()` 例程时，与 `INTR`, `QUIT` 和 `SUSP` 字符相关联的输入和输出队列的正常刷新将不会被执行。 如果你希望在处理程序退出后还能继续输出，就像没有发生过中断一样，你可能会想要在信号处理程序中调用 `noqiflush()`。

## curses.**noraw**()

​	退出 raw 模式。 返回具有行缓冲的正常 "cooked" 模式。

## curses.**pair_content**(*pair_number*)

​	返回包含对应于所请求颜色对的元组 `(fg, bg)`。 *pair_number* 的值必须在 `0` 和 `COLOR_PAIRS - 1` 之间。

## curses.**pair_number**(*attr*)

​	返回通过属性值 *attr* 所设置的颜色对的编号。 [`color_pair()`]({{< ref "/library/allos/curses#curses.color_pair" >}}) 是此函数的对应操作。

## curses.**putp**(*str*)

​	等价于 `tputs(str, 1, putchar)`；为当前终端发出指定 terminfo 功能的值。 请注意 [`putp()`]({{< ref "/library/allos/curses#curses.putp" >}}) 的输出总是前往标准输出。

## curses.**qiflush**([*flag*])

​	如果 *flag* 为 `False`，则效果与调用 [`noqiflush()`]({{< ref "/library/allos/curses#curses.noqiflush" >}}) 相同。 如果 *flag* 为 `True` 或未提供参数，则在读取这些控制字符时队列将被刷新。

## curses.**raw**()

​	进入 raw 模式。 在 raw 模式下，正常的行缓冲和对中断、退出、挂起和流程控制键的处理会被关闭；字符会被逐个地提交给 curses 输入函数。

## curses.**reset_prog_mode**()

​	将终端恢复到 "program" 模式，如之前由 [`def_prog_mode()`]({{< ref "/library/allos/curses#curses.def_prog_mode" >}}) 所保存的一样。

## curses.**reset_shell_mode**()

​	将终端恢复到 "shell" 模式，如之前由 [`def_shell_mode()`]({{< ref "/library/allos/curses#curses.def_shell_mode" >}}) 所保存的一样。

## curses.**resetty**()

​	将终端模式恢复到最后一次调用 [`savetty()`]({{< ref "/library/allos/curses#curses.savetty" >}}) 时的状态。

## curses.**resize_term**(*nlines*, *ncols*)

​	由 [`resizeterm()`]({{< ref "/library/allos/curses#curses.resizeterm" >}}) 用来执行大部分工作的后端函数；当调整窗口大小时，[`resize_term()`]({{< ref "/library/allos/curses#curses.resize_term" >}}) 会以空白填充扩展区域。 调用方应用程序应当以适当的数据填充这些区域。 `resize_term()` 函数会尝试调整所有窗口的大小。 但是，由于面板的调用约定，在不与应用程序进行额外交互的情况下是无法调整其大小的。

## curses.**resizeterm**(*nlines*, *ncols*)

​	将标准窗口和当前窗口的大小调整为指定的尺寸，并调整由 curses 库所使用的记录窗口尺寸的其他记录数据（特别是 SIGWINCH 处理程序）。

## curses.**savetty**()

​	将终端模式的当前状态保存在缓冲区中，可供 [`resetty()`]({{< ref "/library/allos/curses#curses.resetty" >}}) 使用。

## curses.**get_escdelay**()

​	提取通过 [`set_escdelay()`]({{< ref "/library/allos/curses#curses.set_escdelay" >}}) 设置的值。

> Added in version 3.9.
>

## curses.**set_escdelay**(*ms*)

​	设置读取一个转义字符后要等待的毫秒数，以区分在键盘上输入的单个转义字符与通过光标和功能键发送的转义序列。

> Added in version 3.9.
>

## curses.**get_tabsize**()

​	提取通过 [`set_tabsize()`]({{< ref "/library/allos/curses#curses.set_tabsize" >}}) 设置的值。

> Added in version 3.9.
>

## curses.**set_tabsize**(*size*)

​	设置 curses 库在将制表符添加到窗口时将制表符转换为空格所使用的列数。

> Added in version 3.9.
>

## curses.**setsyx**(*y*, *x*)

​	将虚拟屏幕光标设置到 *y*, *x*。 如果 *y* 和 *x* 均为 `-1`，则 [`leaveok`]({{< ref "/library/allos/curses#curses.window.leaveok" >}}) 将设为 `True`。

## curses.**setupterm**(*term=None*, *fd=-1*)

​	初始化终端。 *term* 为给出终端名称的字符串或为 `None`；如果省略或为 `None`，则将使用 `TERM` 环境变量的值。 *fd* 是任何初始化序列将被发送到的文件描述符；如未指定或为 `-1`，则将使用 `sys.stdout` 的文件描述符。

## curses.**start_color**()

​	如果程序员想要使用颜色，则必须在任何其他颜色操作例程被调用之前调用它。 在 [`initscr()`]({{< ref "/library/allos/curses#curses.initscr" >}}) 之后立即调用此例程是一个很好的做法。

[`start_color()`]({{< ref "/library/allos/curses#curses.start_color" >}}) 会初始化八种基本颜色（黑、红、绿、黄、蓝、品、青和白）以及 [`curses`]({{< ref "/library/allos/curses#module-curses" >}}) 模块中的两个全局变量 [`COLORS`]({{< ref "/library/allos/curses#curses.COLORS" >}}) 和 [`COLOR_PAIRS`]({{< ref "/library/allos/curses#curses.COLOR_PAIRS" >}})，其中包含终端可支持的颜色和颜色对的最大数量。 它还会将终端中的颜色恢复为终端刚启动时的值。

## curses.**termattrs**()

​	返回终端所支持的所有视频属性逻辑 OR 的值。 此信息适用于当 curses 程序需要对屏幕外观进行完全控制的情况。

## curses.**termname**()

​	将环境变量 `TERM` 的值截短至 14 个字节，作为字节串对象返回。

## curses.**tigetflag**(*capname*)

​	将与 terminfo 功能名称 *capname* 相对应的布尔功能值以整数形式返回。 如果 *capname* 不是一个布尔功能则返回 `-1`，如果其被取消或不存在于终端描述中则返回 `0`。

## curses.**tigetnum**(*capname*)

​	将与 terminfo 功能名称 *capname* 相对应的数字功能值以整数形式返回。 如果 *capname* 不是一个数字功能则返回 `-2`，如果其被取消或不存在于终端描述中则返回 `-1`。

## curses.**tigetstr**(*capname*)

​	将与 terminfo 功能名称 *capname* 相对应的字符串功能值以字节串对象形式返回。 如果 *capname* 不是一个 terminfo "字符串功能" 或者如果其被取消或不存在于终端描述中则返回 `None`。

## curses.**tparm**(*str*[, *...*])

​	使用提供的形参初始化字节串对象 *str*，其中 *str* 应当是从 terminfo 数据库获取的参数化字符串。 例如 `tparm(tigetstr("cup"), 5, 3)` 的结果可能为 `b'\033[6;4H'`，实际结果将取决于终端类型。

## curses.**typeahead**(*fd*)

​	指定将被用于预输入检查的文件描述符 *fd*。 如果 *fd* 为 `-1`，则不执行预输入检查。

​	curses 库会在更新屏幕时通过定期查找预输入来执行 "断行优化"。 如果找到了输入，并且输入是来自于 tty，则会将当前更新推迟至 refresh 或 doupdate 再次被调用的时候，以便允许更快地响应预先输入的命令。 此函数允许为预输入检查指定其他的文件描述符。

## curses.**unctrl**(*ch*)

​	返回一个字节串对象作为字符 *ch* 的可打印表示形式。 控制字符会表示为一个变换符加相应的字符，例如 `b'^C'`。 可打印字符则会保持原样。

## curses.**ungetch**(*ch*)

​	推送 *ch* 以便让下一个 [`getch()`]({{< ref "/library/allos/curses#curses.window.getch" >}}) 返回该字符。

​备注
 

​	在 `getch()` 被调用之前只能推送一个 *ch*。

## curses.**update_lines_cols**()

​	更新 [`LINES`]({{< ref "/library/allos/curses#curses.LINES" >}}) 和 [`COLS`]({{< ref "/library/allos/curses#curses.COLS" >}}) 模块变量。 适用于检测手动调整屏幕大小。

> Added in version 3.5.
>

## curses.**unget_wch**(*ch*)

​	推送 *ch* 以便让下一个 [`get_wch()`]({{< ref "/library/allos/curses#curses.window.get_wch" >}}) 返回该字符。

​备注
 

​	在 `get_wch()` 被调用之前只能推送一个 *ch*。

> Added in version 3.3.
>

## curses.**ungetmouse**(*id*, *x*, *y*, *z*, *bstate*)

​	将 [`KEY_MOUSE`]({{< ref "/library/allos/curses#curses.KEY_MOUSE" >}}) 事件推送到输入队列，将其与给定的状态数据进行关联。

## curses.**use_env**(*flag*)

​	如果使用此函数，则应当在调用 [`initscr()`]({{< ref "/library/allos/curses#curses.initscr" >}}) 或 newterm 之前调用它。 当 *flag* 为 `False` 时，将会使用在 terminfo 数据库中指定的行和列的值，即使设置了环境变量 `LINES` 和 `COLUMNS` (默认使用)，或者如果 curses 是在窗口中运行（在此情况下如果未设置 `LINES` 和 `COLUMNS` 则默认行为将是使用窗口大小）。

## curses.**use_default_colors**()

​	允许在支持此特性的终端上使用默认的颜色值。 使用此函数可在你的应用程序中支持透明效果。 默认颜色会被赋给颜色编号 `-1`。 举例来说，在调用此函数后，`init_pair(x, curses.COLOR_RED, -1)` 会将颜色对 *x* 初始化为红色前景和默认颜色背景。

## curses.**wrapper**(*func*, */*, **args*, ***kwargs*)

​	初始化 curses 并调用另一个可调用对象 *func*，该对象应当为你的使用 curses 的应用程序的其余部分。 如果应用程序引发了异常，此函数将在重新引发异常并生成回溯信息之前将终端恢复到正常状态。 随后可调用对象 *func* 会被传入主窗口 'stdscr' 作为其第一个参数，再带上其他所有传给 `wrapper()` 的参数。 在调用 *func* 之前，`wrapper()` 会启用 cbreak 模式，关闭回显，启用终端键盘，并在终端具有颜色支持的情况下初始化颜色。 在退出时（无论是正常退出还是异常退出）它会恢复 cooked 模式，打开回显，并禁用终端键盘。



## Window 对象

​	Window 对象会由上面的 [`initscr()`]({{< ref "/library/allos/curses#curses.initscr" >}}) 和 [`newwin()`]({{< ref "/library/allos/curses#curses.newwin" >}}) 返回，它具有以下方法和属性:

## window.**addch**(*ch*[, *attr*])

## window.**addch**(*y*, *x*, *ch*[, *attr*])

​	将带有属性 *attr* 的字符 *ch* 绘制到 `(y, x)`，覆盖之前在该位置上绘制的任何字符。 默认情况下，字符的位置和属性均为窗口对象的当前设置。

​备注
 

​	在窗口、子窗口或面板之外写入会引发 [`curses.error`]({{< ref "/library/allos/curses#curses.error" >}})。 尝试在窗口、子窗口或面板的右下角写入将在字符被打印之后导致异常被引发。

## window.**addnstr**(*str*, *n*[, *attr*])

## window.**addnstr**(*y*, *x*, *str*, *n*[, *attr*])

​	将带有属性 *attr* 的字符串 *str* 中的至多 *n* 个字符绘制到 `(y, x)`，覆盖之前在屏幕上的任何内容。

## window.**addstr**(*str*[, *attr*])

## window.**addstr**(*y*, *x*, *str*[, *attr*])

​	将带有属性 *attr* 的字符串 *str* 绘制到 `(y, x)`，覆盖之前在屏幕上的任何内容。

​备注
- 在窗口、子窗口或面板之外写入会引发 [`curses.error`]({{< ref "/library/allos/curses#curses.error" >}})。 尝试在窗口、子窗口或面板的右下角写入将在字符串被打印之后导致异常被引发。
- 此 Python 模块的后端 [ncurses 中的一个缺陷](https://bugs.python.org/issue35924) 会在调整窗口大小时导致段错误。 此缺陷已在 ncurses-6.1-20190511 中被修复。 如果你必须使用较早版本的 ncurses，则你只要在调用 [`addstr()`]({{< ref "/library/allos/curses#curses.window.addstr" >}}) 时不传入嵌入了换行符的 *str* 即可避免触发此错误。 请为每一行分别调用 [`addstr()`]({{< ref "/library/allos/curses#curses.window.addstr" >}})。

## window.**attroff**(*attr*)

​	从应用于写入到当前窗口的 "background" 集中移除属性 *attr*。

## window.**attron**(*attr*)

​	向应用于写入到当前窗口的 "background" 集中添加属性 *attr*。

## window.**attrset**(*attr*)

​	将 "background" 属性集设为 *attr*。 该集合初始时为 `0` (无属性)。

## window.**bkgd**(*ch*[, *attr*])

​	将窗口 background 特征属性设为带有属性 *attr* 的字符 *ch*。 随后此修改将应用于放置到该窗口中的每个字符。

- 窗口中每个字符的属性会被修改为新的 background 属性。
- 不论之前的 background 字符出现在哪里，它都会被修改为新的 background 字符。

## window.**bkgdset**(*ch*[, *attr*])

​	设置窗口的背景。 窗口的背景由字符和属性的任意组合构成。 背景的属性部分会与写入窗口的所有非空白字符合并（即 OR 运算）。 背景和字符和属性部分均会与空白字符合并。 背景将成为字符的特征属性并在任何滚动与插入/删除行/字符操作中与字符一起移动。

## window.**border**([*ls*[, *rs*[, *ts*[, *bs*[, *tl*[, *tr*[, *bl*[, *br*]]]]]]]])

​	在窗口边缘绘制边框。每个参数指定用于边界特定部分的字符;请参阅下表了解更多详情。

​备注
 

​	任何形参的值为 `0` 都将导致该形参使用默认字符。 关键字形参 *不可* 被使用。 默认字符在下表中列出:

| 参数 | 描述   | 默认值                                                       |
| :--- | :----- | :----------------------------------------------------------- |
| *ls* | 左侧   | [`ACS_VLINE`]({{< ref "/library/allos/curses#curses.ACS_VLINE" >}}) |
| *rs* | 右侧   | [`ACS_VLINE`]({{< ref "/library/allos/curses#curses.ACS_VLINE" >}}) |
| *ts* | 顶部   | [`ACS_HLINE`]({{< ref "/library/allos/curses#curses.ACS_HLINE" >}}) |
| *bs* | 底部   | [`ACS_HLINE`]({{< ref "/library/allos/curses#curses.ACS_HLINE" >}}) |
| *tl* | 左上角 | [`ACS_ULCORNER`]({{< ref "/library/allos/curses#curses.ACS_ULCORNER" >}}) |
| *tr* | 右上角 | [`ACS_URCORNER`]({{< ref "/library/allos/curses#curses.ACS_URCORNER" >}}) |
| *bl* | 左下角 | [`ACS_LLCORNER`]({{< ref "/library/allos/curses#curses.ACS_LLCORNER" >}}) |
| *br* | 右下角 | [`ACS_LRCORNER`]({{< ref "/library/allos/curses#curses.ACS_LRCORNER" >}}) |

## window.**box**([*vertch*, *horch*])

​	类似于 [`border()`]({{< ref "/library/allos/curses#curses.window.border" >}})，但 *ls* 和 *rs* 均为 *vertch* 而 *ts* 和 *bs* 均为 *horch*。 此函数总是会使用默认的转角字符。

## window.**chgat**(*attr*)

## window.**chgat**(*num*, *attr*)

## window.**chgat**(*y*, *x*, *attr*)

## window.**chgat**(*y*, *x*, *num*, *attr*)

​	在当前光标位置或是在所提供的位置 `(y, x)` 设置 *num* 个字符的属性。 如果 *num* 未给出或为 `-1`，则将属性设置到所有字符上直至行尾。 如果提供了位置 `(y, x)` 则此函数会将光标移至该位置。 修改过的行将使用 [`touchline()`]({{< ref "/library/allos/curses#curses.window.touchline" >}}) 方法处理以便下次窗口刷新时内容会重新显示。

## window.**clear**()

​	类似于 [`erase()`]({{< ref "/library/allos/curses#curses.window.erase" >}})，但还会导致在下次调用 [`refresh()`]({{< ref "/library/allos/curses#curses.window.refresh" >}}) 时整个窗口被重新绘制。

## window.**clearok**(*flag*)

​	如果 *flag* 为 `True`，则在下次调用 [`refresh()`]({{< ref "/library/allos/curses#curses.window.refresh" >}}) 时将完全清除窗口。

## window.**clrtobot**()

​	从光标位置开始擦除直至窗口末端：光标以下的所有行都会被删除，然后会执行 [`clrtoeol()`]({{< ref "/library/allos/curses#curses.window.clrtoeol" >}}) 的等效操作。

## window.**clrtoeol**()

​	从光标位置开始擦除直至行尾。

## window.**cursyncup**()

​	更新窗口所有上级窗口的当前光标位置以反映窗口的当前光标位置。

## window.**delch**([*y*, *x*])

​	删除位于 `(y, x)` 的任何字符。

## window.**deleteln**()

​	删除在光标之下的行。 所有后续的行都会上移一行。

## window.**derwin**(*begin_y*, *begin_x*)

## window.**derwin**(*nlines*, *ncols*, *begin_y*, *begin_x*)

​	"derive window" 的缩写，[`derwin()`]({{< ref "/library/allos/curses#curses.window.derwin" >}}) 与调用 [`subwin()`]({{< ref "/library/allos/curses#curses.window.subwin" >}}) 等效，不同之处在于 *begin_y* 和 *begin_x* 是想对于窗口的初始位置，而不是相对于整个屏幕。 返回代表所派生窗口的窗口对象。

## window.**echochar**(*ch*[, *attr*])

​	使用属性 *attr* 添加字符 *ch*，并立即在窗口上调用 [`refresh()`]({{< ref "/library/allos/curses#curses.window.refresh" >}})。

## window.**enclose**(*y*, *x*)

​	检测给定的相对屏幕的字符-单元格坐标是否被给定的窗口所包围，返回 `True` 或 `False`。 它适用于确定是哪个屏幕窗口子集包围着某个鼠标事件的位置。

> 在 3.10 版本发生变更: 在之前版本中它会返回 `1` 或 `0` 而不是 `True` 或 `False`。

## window.**encoding**

​	用于编码方法参数（Unicode 字符串和字符）的编码格式。 encoding 属性是在创建子窗口时从父窗口继承的，例如通过 [`window.subwin()`]({{< ref "/library/allos/curses#curses.window.subwin" >}})。 在默认情况下，会使用当前语言区域的编码格式（参见 [`locale.getencoding()`]({{< ref "/library/i18n/locale#locale.getencoding" >}}) 文档）。

> Added in version 3.3.
>

## window.**erase**()

​	清空窗口。

## window.**getbegyx**()

​	返回以左上角为原点的坐标元组 `(y, x)`。

## window.**getbkgd**()

​	返回给定窗口的当前背景字符/属性对。

## window.**getch**([*y*, *x*])

​	获取一个字符。 请注意所返回的整数 *不一定* 要在 ASCII 范围以内：功能键、小键盘键等等是由大于 255 的数字表示的。 在无延迟模式下，如果没有输入则返回 `-1`，在其他情况下都会等待直至有键被按下。

## window.**get_wch**([*y*, *x*])

​	获取一个宽字符。 对于大多数键都是返回一个字符，对于功能键、小键盘键和其他特殊键则是返回一个整数。 在无延迟模式下，如果没有输入则引发一个异常。

> Added in version 3.3.
>

## window.**getkey**([*y*, *x*])

​	获取一个字符，返回一个字符串而不是像 [`getch()`]({{< ref "/library/allos/curses#curses.window.getch" >}}) 那样返回一个整数。 功能键、小键盘键和其他特殊键则是返回一个包含键名的多字节字符串。 在无延迟模式下，如果没有输入则引发一个异常。

## window.**getmaxyx**()

​	返回窗口高度和宽度的元组 `(y, x)`。

## window.**getparyx**()

​	将此窗口相对于父窗口的起始坐标作为元组 `(y, x)` 返回。 如果此窗口没有父窗口则返回 `(-1, -1)`。

## window.**getstr**()

## window.**getstr**(*n*)

## window.**getstr**(*y*, *x*)

## window.**getstr**(*y*, *x*, *n*)

​	从用户读取一个字节串对象，附带基本的行编辑功能。

## window.**getyx**()

​	返回当前光标相对于窗口左上角的位置的元组 `(y, x)`。

## window.**hline**(*ch*, *n*)

## window.**hline**(*y*, *x*, *ch*, *n*)

​	显示一条起始于 `(y, x)` 长度为 *n* 个字符 *ch* 的水平线。

## window.**idcok**(*flag*)

​	如果 *flag* 为 `False`，curses 将不再考虑使用终端的硬件插入/删除字符功能；如果 *flag* 为 `True`，则会启用字符插入和删除。 当 curses 首次初始化时，默认会启用字符插入/删除。

## window.**idlok**(*flag*)

​	如果 *flag* 为 `True`，[`curses`]({{< ref "/library/allos/curses#module-curses" >}}) 将尝试使用硬件行编辑功能。 否则，行插入/删除会被禁用。

## window.**immedok**(*flag*)

​	如果 *flag* 为 `True`，窗口图像中的任何改变都会自动导致窗口被刷新；你不必再自己调用 [`refresh()`]({{< ref "/library/allos/curses#curses.window.refresh" >}})。 但是，这可能会由于重复调用 wrefresh 而显著降低性能。 此选项默认被禁用。

## window.**inch**([*y*, *x*])

​	返回窗口中给定位置上的字符。 下面的 8 个比特位是字符本身，上面的比特位则为属性。

## window.**insch**(*ch*[, *attr*])

## window.**insch**(*y*, *x*, *ch*[, *attr*])

​	将带有属性 *attr* 的字符 *ch* 绘制到 `(y, x)`，将该行从位置 *x* 开始右移一个字符。

## window.**insdelln**(*nlines*)

​	在指定窗口的当前行上方插入 *nlines* 行。 下面的 *nlines* 行将丢失。 对于 *nlines* 为负值的情况，则从光标下方的行开始删除 *nlines* 行，并将其余的行向上移动。 下面的 *nlines* 行会被清空。 当前光标位置将保持不变。

## window.**insertln**()

​	在光标下方插入一个空行。 所有后续的行都会下移一行。

## window.**insnstr**(*str*, *n*[, *attr*])

## window.**insnstr**(*y*, *x*, *str*, *n*[, *attr*])

​	在光标下方的字符之前插入一个至多为 *n* 个字符的字符串（字符数量将与该行相匹配）。 如果 *n* 为零或负数，则插入整个字符串。 光标右边的所有字符将被右移，该行右端的字符将丢失。 光标位置将保持不变（在移到可能指定的 *y*, *x* 之后）。

## window.**insstr**(*str*[, *attr*])

## window.**insstr**(*y*, *x*, *str*[, *attr*])

​	在光标下方的字符之前插入一个字符串（字符数量将与该行相匹配）。 光标右边的所有字符将被右移，该行右端的字符将丢失。 光标位置将保持不变（在移到可能指定的 *y*, *x* 之后）。

## window.**instr**([*n*])

## window.**instr**(*y*, *x*[, *n*])

​	返回从窗口的当前光标位置，或者指定的 *y*, *x* 开始提取的字符所对应的字节串对象。 属性会从字符中去除。 如果指定了 *n*，[`instr()`]({{< ref "/library/allos/curses#curses.window.instr" >}}) 将返回长度至多为 *n* 个字符的字符串（不包括末尾的 NUL）。

## window.**is_linetouched**(*line*)

​	如果指定的行自上次调用 [`refresh()`]({{< ref "/library/allos/curses#curses.window.refresh" >}}) 后发生了改变则返回 `True`；否则返回 `False`。 如果 *line* 对于给定的窗口不可用则会引发 [`curses.error`]({{< ref "/library/allos/curses#curses.error" >}}) 异常。

## window.**is_wintouched**()

​	如果指定的窗口自上次调用 [`refresh()`]({{< ref "/library/allos/curses#curses.window.refresh" >}}) 后发生了改变则返回 `True`；否则返回 `False`。

## window.**keypad**(*flag*)

​	如果 *flag* 为 `True`，则某些键（小键盘键、功能键等）生成的转义序列将由 [`curses`]({{< ref "/library/allos/curses#module-curses" >}}) 来解析。 如果 *flag* 为 `False`，转义序列将保持在输入流中的原样。

## window.**leaveok**(*flag*)

​	如果 *flag* 为 `True`，则在更新时光标将停留在原地，而不是在“光标位置”。 这将可以减少光标的移动。 在可能的情况下光标将变为不可见。

​	如果 *flag* 为 `False`，光标在更新后将总是位于“光标位置”。

## window.**move**(*new_y*, *new_x*)

​	将光标移至 `(new_y, new_x)`。

## window.**mvderwin**(*y*, *x*)

​	让窗口在其父窗口内移动。 窗口相对于屏幕的参数不会被更改。 此例程用于在屏幕的相同物理位置显示父窗口的不同部分。

## window.**mvwin**(*new_y*, *new_x*)

​	移动窗口以使其左上角位于 `(new_y, new_x)`。

## window.**nodelay**(*flag*)

​	如果 *flag* 为 `True`，则 [`getch()`]({{< ref "/library/allos/curses#curses.window.getch" >}}) 将为非阻塞的。

## window.**notimeout**(*flag*)

​	如果 *flag* 为 `True`，则转义序列将不会发生超时。

​	如果 *flag* 为 `False`，则在几毫秒之后，转义序列将不会被解析，并将保持在输入流中的原样。

## window.**noutrefresh**()

​	标记为刷新但保持等待。 此函数会更新代表预期窗口状态的数据结构，但并不强制更新物理屏幕。 要完成后者，请调用 [`doupdate()`]({{< ref "/library/allos/curses#curses.doupdate" >}})。

## window.**overlay**(*destwin*[, *sminrow*, *smincol*, *dminrow*, *dmincol*, *dmaxrow*, *dmaxcol*])

​	将窗口覆盖在 *destwin* 上方。 窗口的大小不必相同，只有重叠的区域会被复制。 此复制是非破坏性的，这意味着当前背景字符不会覆盖掉 *destwin* 的旧内容。

​	为了获得对被复制区域的细粒度控制，可以使用 [`overlay()`]({{< ref "/library/allos/curses#curses.window.overlay" >}}) 的第二种形式。 *sminrow* 和 *smincol* 是源窗口的左上角坐标，而其他变量则在目标窗口中标记出一个矩形。

## window.**overwrite**(*destwin*[, *sminrow*, *smincol*, *dminrow*, *dmincol*, *dmaxrow*, *dmaxcol*])

​	将窗口覆盖在 *destwin* 上方。 窗口的大小不必相同，此时只有重叠的区域会被复制。 此复制是破坏性的，这意味着当前背景字符会覆盖掉 *destwin* 的旧内容。

​	为了获得对被复制区域的细粒度控制，可以使用 [`overwrite()`]({{< ref "/library/allos/curses#curses.window.overwrite" >}}) 的第二种形式。 *sminrow* 和 *smincol* 是源窗口的左上角坐标，而其他变量则在目标窗口中标记出一个矩形。

## window.**putwin**(*file*)

​	将关联到窗口的所有数据写入到所提供的文件对象。 此信息可在以后使用 [`getwin()`]({{< ref "/library/allos/curses#curses.getwin" >}}) 函数来提取。

## window.**redrawln**(*beg*, *num*)

​	指明从 *beg* 行开始的 *num* 个屏幕行已被破坏并且应当在下次 [`refresh()`]({{< ref "/library/allos/curses#curses.window.refresh" >}}) 调用时完全重绘。

## window.**redrawwin**()

​	触碰整个窗口，以使其在下次 [`refresh()`]({{< ref "/library/allos/curses#curses.window.refresh" >}}) 调用时完全重绘。

## window.**refresh**([*pminrow*, *pmincol*, *sminrow*, *smincol*, *smaxrow*, *smaxcol*])

​	立即更新显示（将实际屏幕与之前的绘制/删除方法进行同步）。

​	6 个可选参数仅在窗口为使用 [`newpad()`]({{< ref "/library/allos/curses#curses.newpad" >}}) 创建的面板时可被指定。 需要额外的形参来指定所涉及到的是面板和屏幕的哪一部分。 *pminrow* 和 *pmincol* 指定要在面板中显示的矩形的左上角。 *sminrow*, *smincol*, *smaxrow* 和 *smaxcol* 指定要在屏幕中显示的矩形的边。 要在面板中显示的矩形的右下角是根据屏幕坐标计算出来的，由于矩形的大小必须相同。 两个矩形都必须完全包含在其各自的结构之内。 负的 *pminrow*, *pmincol*, *sminrow* 或 *smincol* 值会被视为将它们设为零值。

## window.**resize**(*nlines*, *ncols*)

​	为 curses 窗口重新分配存储空间以将其尺寸调整为指定的值。 如果任一维度的尺寸大于当前值，则窗口的数据将以具有合并了当前背景渲染（由 [`bkgdset()`]({{< ref "/library/allos/curses#curses.window.bkgdset" >}}) 设置）的空白来填充。

## window.**scroll**([*lines=1*])

​	将屏幕或滚动区域向上滚动 *lines* 行。

## window.**scrollok**(*flag*)

​	控制当一个窗口的光标移出窗口或滚动区域边缘时会发生什么，这可能是在底端行执行换行操作，或者在最后一行输入最后一个字符导致的结果。 如果 *flag* 为 `False`，光标会留在底端行。 如果 *flag* 为 `True`，窗口会向上滚动一行。 请注意为了在终端上获得实际的滚动效果，还需要调用 [`idlok()`]({{< ref "/library/allos/curses#curses.window.idlok" >}})。

## window.**setscrreg**(*top*, *bottom*)

​	设置从 *top* 行至 *bottom* 行的滚动区域。 所有滚动操作将在此区域中进行。

## window.**standend**()

​	关闭 standout 属性。 在某些终端上此操作会有关闭所有属性的副作用。

## window.**standout**()

​	启用属性 *A_STANDOUT*。

## window.**subpad**(*begin_y*, *begin_x*)

## window.**subpad**(*nlines*, *ncols*, *begin_y*, *begin_x*)

​	返回一个子窗口，其左上角位于 `(begin_y, begin_x)`，并且其宽度/高度为 *ncols*/*nlines*。

## window.**subwin**(*begin_y*, *begin_x*)

## window.**subwin**(*nlines*, *ncols*, *begin_y*, *begin_x*)

​	返回一个子窗口，其左上角位于 `(begin_y, begin_x)`，并且其宽度/高度为 *ncols*/*nlines*。

​	默认情况下，子窗口将从指定位置扩展到窗口的右下角。

## window.**syncdown**()

​	触碰已在上级窗口上被触碰的每个位置。 此例程由 [`refresh()`]({{< ref "/library/allos/curses#curses.window.refresh" >}}) 调用，因此几乎从不需要手动调用。

## window.**syncok**(*flag*)

​	如果 *flag* 为 `True`，则 [`syncup()`]({{< ref "/library/allos/curses#curses.window.syncup" >}}) 会在窗口发生改变的任何时候自动被调用。

## window.**syncup**()

​	触碰已在窗口中被改变的此窗口的各个上级窗口中的所有位置。

## window.**timeout**(*delay*)

​	为窗口设置阻塞或非阻塞读取行为。 如果 *delay* 为负值，则会使用阻塞读取（这将无限期地等待输入）。 如果 *delay* 为零，则会使用非阻塞读取，并且当没有输入在等待时 [`getch()`]({{< ref "/library/allos/curses#curses.window.getch" >}}) 将返回 `-1`。 如果 *delay* 为正值，则 [`getch()`]({{< ref "/library/allos/curses#curses.window.getch" >}}) 将阻塞 *delay* 毫秒，并且当此延时结束时仍无输入将返回 `-1`。

## window.**touchline**(*start*, *count*[, *changed*])

​	假定从行 *start* 开始的 *count* 行已被更改。 如果提供了 *changed*，它将指明是将受影响的行标记为已更改 (*changed*`=True`) 还是未更改 (*changed*`=False`)。

## window.**touchwin**()

​	假定整个窗口已被更改，其目的是用于绘制优化。

## window.**untouchwin**()

​	将自上次调用 [`refresh()`]({{< ref "/library/allos/curses#curses.window.refresh" >}}) 以来窗口中的所有行标记为未改变。

## window.**vline**(*ch*, *n*[, *attr*])

## window.**vline**(*y*, *x*, *ch*, *n*[, *attr*])

​	显示一行起始于 `(y, x)` 长度为 *n* 的由具有属性 *attr* 的字符 *ch* 组成的垂直线。

## 常量

[`curses`]({{< ref "/library/allos/curses#module-curses" >}}) 模块定义了以下数据成员:

## curses.**ERR**

​	一些返回整数的 curses 例程，例如 [`getch()`]({{< ref "/library/allos/curses#curses.window.getch" >}})，在失败时将返回 [`ERR`]({{< ref "/library/allos/curses#curses.ERR" >}})。

## curses.**OK**

​	一些返回整数的 curses 例程，例如 [`napms()`]({{< ref "/library/allos/curses#curses.napms" >}})，在成功时将返回 [`OK`]({{< ref "/library/allos/curses#curses.OK" >}})。

## curses.**version**

## curses.`__version__`

​	一个代表模块当前版本的字节串对象。

## curses.**ncurses_version**

​	一个具名元组，它包含构成 ncurses 库版本号的三个数字: *major*, *minor* 和 *patch*。 三个值均为整数。 三个值也可通过名称来访问，因此 `curses.ncurses_version[0]` 等价于 `curses.ncurses_version.major`，依此类推。

​	可用性：如果使用了 ncurses 库。

> Added in version 3.8.
>

## curses.**COLORS**

​	终端可支持的最大颜色数。 它只有在调用 [`start_color()`]({{< ref "/library/allos/curses#curses.start_color" >}}) 之后才会被定义。

## curses.**COLOR_PAIRS**

​	终端可支持的最大颜色对数。 它只有在调用 [`start_color()`]({{< ref "/library/allos/curses#curses.start_color" >}}) 之后才会被定义。

## curses.**COLS**

​	屏幕的宽度，即列数。 它只有在调用 [`initscr()`]({{< ref "/library/allos/curses#curses.initscr" >}}) 之后才会被定义。 可被 [`update_lines_cols()`]({{< ref "/library/allos/curses#curses.update_lines_cols" >}})、[`resizeterm()`]({{< ref "/library/allos/curses#curses.resizeterm" >}}) 和 [`resize_term()`]({{< ref "/library/allos/curses#curses.resize_term" >}}) 更新。

## curses.**LINES**

​	屏幕的高度，即行数。 它只有在调用 [`initscr()`]({{< ref "/library/allos/curses#curses.initscr" >}}) 之后才会被定义。 可被 [`update_lines_cols()`]({{< ref "/library/allos/curses#curses.update_lines_cols" >}})、[`resizeterm()`]({{< ref "/library/allos/curses#curses.resizeterm" >}}) 和 [`resize_term()`]({{< ref "/library/allos/curses#curses.resize_term" >}}) 更新。

​	有些常量可用于指定字符单元属性。 实际可用的常量取决于具体的系统。

| 属性                    | 含意               |
| :---------------------- | :----------------- |
| curses.**A_ALTCHARSET** | 备用字符集模式     |
| curses.**A_BLINK**      | 闪烁模式           |
| curses.**A_BOLD**       | 粗体模式           |
| curses.**A_DIM**        | 暗淡模式           |
| curses.**A_INVIS**      | 不可见或空白模式   |
| curses.**A_ITALIC**     | 斜体模式           |
| curses.**A_NORMAL**     | 正常属性           |
| curses.**A_PROTECT**    | 保护模式           |
| curses.**A_REVERSE**    | 反转背景色和前景色 |
| curses.**A_STANDOUT**   | 突出模式           |
| curses.**A_UNDERLINE**  | 下划线模式         |
| curses.**A_HORIZONTAL** | 水平突出显示       |
| curses.**A_LEFT**       | 左高亮             |
| curses.**A_LOW**        | 底部高亮           |
| curses.**A_RIGHT**      | 右高亮             |
| curses.**A_TOP**        | 顶部高亮           |
| curses.**A_VERTICAL**   | 垂直突出显示       |

> Added in version 3.7:
> `A_ITALIC` was added.

​	有几个常量可用于提取某些方法返回的相应属性。

| 位掩码                  | 含意                           |
| :---------------------- | :----------------------------- |
| curses.**A_ATTRIBUTES** | 用于提取属性的位掩码           |
| curses.**A_CHARTEXT**   | 用于提取字符的位掩码           |
| curses.**A_COLOR**      | 用于提取颜色对字段信息的位掩码 |

​	键由名称以 `KEY_` 开头的整数常量引用。确切的可用键取决于系统。

| 关键常数                 | 键                              |
| :----------------------- | :------------------------------ |
| curses.**KEY_MIN**       | 最小键值                        |
| curses.**KEY_BREAK**     | 中断键（不可靠）                |
| curses.**KEY_DOWN**      | 向下箭头                        |
| curses.**KEY_UP**        | 向上箭头                        |
| curses.**KEY_LEFT**      | 向左箭头                        |
| curses.**KEY_RIGHT**     | 向右箭头                        |
| curses.**KEY_HOME**      | Home 键 (上+左箭头)             |
| curses.**KEY_BACKSPACE** | 退格（不可靠）                  |
| curses.**KEY_F0**        | 功能键。 支持至多 64 个功能键。 |
| curses.**KEY_Fn**        | 功能键 *n* 的值                 |
| curses.**KEY_DL**        | 删除行                          |
| curses.**KEY_IL**        | 插入行                          |
| curses.**KEY_DC**        | 删除字符                        |
| curses.**KEY_IC**        | 插入字符或进入插入模式          |
| curses.**KEY_EIC**       | 退出插入字符模式                |
| curses.**KEY_CLEAR**     | 清空屏幕                        |
| curses.**KEY_EOS**       | 清空至屏幕底部                  |
| curses.**KEY_EOL**       | 清空至行尾                      |
| curses.**KEY_SF**        | 向前滚动 1 行                   |
| curses.**KEY_SR**        | 向后滚动 1 行 (反转)            |
| curses.**KEY_NPAGE**     | 下一页                          |
| curses.**KEY_PPAGE**     | 上一页                          |
| curses.**KEY_STAB**      | 设置制表符                      |
| curses.**KEY_CTAB**      | 清除制表符                      |
| curses.**KEY_CATAB**     | 清除所有制表符                  |
| curses.**KEY_ENTER**     | 回车或发送 (不可靠)             |
| curses.**KEY_SRESET**    | 软 (部分) 重置 (不可靠)         |
| curses.**KEY_RESET**     | 重置或硬重置 (不可靠)           |
| curses.**KEY_PRINT**     | 打印                            |
| curses.**KEY_LL**        | Home 向下或到底 (左下)          |
| curses.**KEY_A1**        | 键盘的左上角                    |
| curses.**KEY_A3**        | 键盘的右上角                    |
| curses.**KEY_B2**        | 键盘的中心                      |
| curses.**KEY_C1**        | 键盘左下方                      |
| curses.**KEY_C3**        | 键盘右下方                      |
| curses.**KEY_BTAB**      | 回退制表符                      |
| curses.**KEY_BEG**       | Beg (开始)                      |
| curses.**KEY_CANCEL**    | 取消                            |
| curses.**KEY_CLOSE**     | 关闭                            |
| curses.**KEY_COMMAND**   | Cmd (命令行)                    |
| curses.**KEY_COPY**      | 复制                            |
| curses.**KEY_CREATE**    | 创建                            |
| curses.**KEY_END**       | End                             |
| curses.**KEY_EXIT**      | 退出                            |
| curses.**KEY_FIND**      | 查找                            |
| curses.**KEY_HELP**      | 帮助                            |
| curses.**KEY_MARK**      | 标记                            |
| curses.**KEY_MESSAGE**   | 消息                            |
| curses.**KEY_MOVE**      | 移动                            |
| curses.**KEY_NEXT**      | 下一个                          |
| curses.**KEY_OPEN**      | 打开                            |
| curses.**KEY_OPTIONS**   | 选项                            |
| curses.**KEY_PREVIOUS**  | Prev (上一个)                   |
| curses.**KEY_REDO**      | 重做                            |
| curses.**KEY_REFERENCE** | Ref (引用)                      |
| curses.**KEY_REFRESH**   | 刷新                            |
| curses.**KEY_REPLACE**   | 替换                            |
| curses.**KEY_RESTART**   | 重启                            |
| curses.**KEY_RESUME**    | 恢复                            |
| curses.**KEY_SAVE**      | 保存                            |
| curses.**KEY_SBEG**      | Shift + Beg (开始)              |
| curses.**KEY_SCANCEL**   | Shift + Cancel                  |
| curses.**KEY_SCOMMAND**  | Shift + Command                 |
| curses.**KEY_SCOPY**     | Shift + Copy                    |
| curses.**KEY_SCREATE**   | Shift + Create                  |
| curses.**KEY_SDC**       | Shift + 删除字符                |
| curses.**KEY_SDL**       | Shift + 删除行                  |
| curses.**KEY_SELECT**    | 选择                            |
| curses.**KEY_SEND**      | Shift + End                     |
| curses.**KEY_SEOL**      | Shift + 清空行                  |
| curses.**KEY_SEXIT**     | Shift + 退出                    |
| curses.**KEY_SFIND**     | Shift + 查找                    |
| curses.**KEY_SHELP**     | Shift + 帮助                    |
| curses.**KEY_SHOME**     | Shift + Home                    |
| curses.**KEY_SIC**       | Shift + 输入                    |
| curses.**KEY_SLEFT**     | Shift + 向左箭头                |
| curses.**KEY_SMESSAGE**  | Shift + 消息                    |
| curses.**KEY_SMOVE**     | Shift + 移动                    |
| curses.**KEY_SNEXT**     | Shift + 下一个                  |
| curses.**KEY_SOPTIONS**  | Shift + 选项                    |
| curses.**KEY_SPREVIOUS** | Shift + 上一个                  |
| curses.**KEY_SPRINT**    | Shift + 打印                    |
| curses.**KEY_SREDO**     | Shift + 重做                    |
| curses.**KEY_SREPLACE**  | Shift + 替换                    |
| curses.**KEY_SRIGHT**    | Shift + 向右箭头                |
| curses.**KEY_SRSUME**    | Shift + 恢复                    |
| curses.**KEY_SSAVE**     | Shift + 保存                    |
| curses.**KEY_SSUSPEND**  | Shift + 挂起                    |
| curses.**KEY_SUNDO**     | Shift + 撤销                    |
| curses.**KEY_SUSPEND**   | 挂起                            |
| curses.**KEY_UNDO**      | 撤销操作                        |
| curses.**KEY_MOUSE**     | 鼠标事件已发生                  |
| curses.**KEY_RESIZE**    | 终端大小改变事件                |
| curses.**KEY_MAX**       | 最大键值                        |

​	在 VT100s 及其软件模拟器，如 X 终端模拟器上，通常至少有四个功能键 ([`KEY_F1`]({{< ref "/library/allos/curses#curses.KEY_Fn" >}}), [`KEY_F2`]({{< ref "/library/allos/curses#curses.KEY_Fn" >}}), [`KEY_F3`]({{< ref "/library/allos/curses#curses.KEY_Fn" >}}), [`KEY_F4`]({{< ref "/library/allos/curses#curses.KEY_Fn" >}})) 可用，并且方向键将明确地映射到 [`KEY_UP`]({{< ref "/library/allos/curses#curses.KEY_UP" >}}), [`KEY_DOWN`]({{< ref "/library/allos/curses#curses.KEY_DOWN" >}}), [`KEY_LEFT`]({{< ref "/library/allos/curses#curses.KEY_LEFT" >}}) 和 [`KEY_RIGHT`]({{< ref "/library/allos/curses#curses.KEY_RIGHT" >}})。 如果你的机器有一个 PC 键盘，则保证能使用方向键和十二个功能键 (老式的 PC 键盘可能只有十个功能键)；此外，还有以下的标准小键盘映射:

| 键帽      | 常量      |
| :-------- | :-------- |
| Insert    | KEY_IC    |
| Delete    | KEY_DC    |
| Home      | KEY_HOME  |
| End       | KEY_END   |
| Page Up   | KEY_PPAGE |
| Page Down | KEY_NPAGE |

​	下表列出了替代字符集中的字符。 这些字符继承自 VT100 终端，在 X 终端等软件模拟器上通常均为可用。 当没有可用的图形时，curses 会回退为粗糙的可打印 ASCII 近似符号。

​备注
 

​	只有在调用 [`initscr()`]({{< ref "/library/allos/curses#curses.initscr" >}}) 之后才能使用它们

| ACS代码                 | 含意                   |
| :---------------------- | :--------------------- |
| curses.**ACS_BBSS**     | 右上角的别名           |
| curses.**ACS_BLOCK**    | 实心方块               |
| curses.**ACS_BOARD**    | 正方形                 |
| curses.**ACS_BSBS**     | 水平线的别名           |
| curses.**ACS_BSSB**     | 左上角的别名           |
| curses.**ACS_BSSS**     | 顶部 T 型的别名        |
| curses.**ACS_BTEE**     | 底部 T 型              |
| curses.**ACS_BULLET**   | 正方形                 |
| curses.**ACS_CKBOARD**  | 棋盘（点刻）           |
| curses.**ACS_DARROW**   | 向下箭头               |
| curses.**ACS_DEGREE**   | 等级符                 |
| curses.**ACS_DIAMOND**  | 菱形                   |
| curses.**ACS_GEQUAL**   | 大于或等于             |
| curses.**ACS_HLINE**    | 水平线                 |
| curses.**ACS_LANTERN**  | 灯形符号               |
| curses.**ACS_LARROW**   | 向左箭头               |
| curses.**ACS_LEQUAL**   | 小于或等于             |
| curses.**ACS_LLCORNER** | 左下角                 |
| curses.**ACS_LRCORNER** | 右下角                 |
| curses.**ACS_LTEE**     | 左侧 T 型              |
| curses.**ACS_NEQUAL**   | 不等号                 |
| curses.**ACS_PI**       | 字母π                  |
| curses.**ACS_PLMINUS**  | 正负号                 |
| curses.**ACS_PLUS**     | 加号                   |
| curses.**ACS_RARROW**   | 向右箭头               |
| curses.**ACS_RTEE**     | 右侧 T 型              |
| curses.**ACS_S1**       | 扫描线 1               |
| curses.**ACS_S3**       | 扫描线3                |
| curses.**ACS_S7**       | 扫描线7                |
| curses.**ACS_S9**       | 扫描线 9               |
| curses.**ACS_SBBS**     | 右下角的别名           |
| curses.**ACS_SBSB**     | 垂直线的别名           |
| curses.**ACS_SBSS**     | 右侧 T 型的别名        |
| curses.**ACS_SSBB**     | 左下角的别名           |
| curses.**ACS_SSBS**     | 底部 T 型的别名        |
| curses.**ACS_SSSB**     | 左侧 T 型的别名        |
| curses.**ACS_SSSS**     | 交叉或大加号的替代名称 |
| curses.**ACS_STERLING** | 英镑                   |
| curses.**ACS_TTEE**     | 顶部 T 型              |
| curses.**ACS_UARROW**   | 向上箭头               |
| curses.**ACS_ULCORNER** | 左上角                 |
| curses.**ACS_URCORNER** | 右上角                 |
| curses.**ACS_VLINE**    | 垂线                   |

​	下面列出了 [`getmouse()`]({{< ref "/library/allos/curses#curses.getmouse" >}}) 所使用的鼠标按键常量:

| 鼠标按键常量                      | 含意                            |
| :-------------------------------- | :------------------------------ |
| curses.**BUTTONn_PRESSED**        | 鼠标按键 *n* 被按下             |
| curses.**BUTTONn_RELEASED**       | 鼠标按键 *n* 被释放             |
| curses.**BUTTONn_CLICKED**        | 鼠标按键 *n* 被点击             |
| curses.**BUTTONn_DOUBLE_CLICKED** | 鼠标按键 *n* 被双击             |
| curses.**BUTTONn_TRIPLE_CLICKED** | 鼠标按键 *n* 被三击             |
| curses.**BUTTON_SHIFT**           | 当按键状态改变时 Shift 被按下   |
| curses.**BUTTON_CTRL**            | 当按键状态改变时 Control 被按下 |
| curses.**BUTTON_ALT**             | 当按键状态改变时 Control 被按下 |

> 在 3.10 版本发生变更: 现在 `BUTTON5_*` 常量如果是由下层 curses 库提供的则会对外公开。

​	下表列出了预定义的颜色：

| 常量                     | 颜色             |
| :----------------------- | :--------------- |
| curses.**COLOR_BLACK**   | 黑色             |
| curses.**COLOR_BLUE**    | 蓝色             |
| curses.**COLOR_CYAN**    | 青色（浅绿蓝色） |
| curses.**COLOR_GREEN**   | 绿色             |
| curses.**COLOR_MAGENTA** | 洋红色（紫红色） |
| curses.**COLOR_RED**     | 红色             |
| curses.**COLOR_WHITE**   | 白色             |
| curses.**COLOR_YELLOW**  | 黄色             |



# [`curses.textpad`]({{< ref "/library/allos/curses#module-curses.textpad" >}}) --- 用于 curses 程序的文本输入控件

[`curses.textpad`]({{< ref "/library/allos/curses#module-curses.textpad" >}}) 模块提供了一个 [`Textbox`]({{< ref "/library/allos/curses#curses.textpad.Textbox" >}}) 类，该类在 curses 窗口中处理基本的文本编辑，支持一组与 Emacs 类似的键绑定（因此这也适用于 Netscape Navigator, BBedit 6.x, FrameMaker 和许多其他程序）。 该模块还提供了一个绘制矩形的函数，适用于容纳文本框或其他目的。

[`curses.textpad`]({{< ref "/library/allos/curses#module-curses.textpad" >}}) 模块定义了以下函数:

## curses.textpad.**rectangle**(*win*, *uly*, *ulx*, *lry*, *lrx*)

​	绘制一个矩形。 第一个参数必须为窗口对象；其余参数均为相对于该窗口的坐标值。 第二和第三个参数为要绘制的矩形的左上角的 y 和 x 坐标值；第四和第五个参数为其右下角的 y 和 x 坐标值。 将会使用 VT100/IBM PC 形式的字符在可用的终端上（包括 xterm 和大多数其他软件终端模拟器）绘制矩形。 在其他情况下则将使用 ASCII 横杠、竖线和加号绘制。



## 文本框对象

​	你可以通过如下方式实例化一个 [`Textbox`]({{< ref "/library/allos/curses#curses.textpad.Textbox" >}}):

## *class* curses.textpad.**Textbox**(*win*)

​	返回一个文本框控件对象。 *win* 参数必须是一个 curses [窗口]({{< ref "/library/allos/curses#curses-window-objects" >}}) 对象，文本框将被包含在其中。 文本框的编辑光标在初始时位于包含窗口的左上角，坐标值为 `(0, 0)`。 实例的 [`stripspaces`]({{< ref "/library/allos/curses#curses.textpad.Textbox.stripspaces" >}}) 旗标初始时为启用。

[`Textbox`]({{< ref "/library/allos/curses#curses.textpad.Textbox" >}}) 对象具有以下方法:

## **edit**([*validator*])

​	这是你通常将使用的入口点。 它接受编辑按键直到键入了一个终止按键。 如果提供了 *validator*，它必须是一个函数。 它将在每次按键时被调用并传入相应的按键作作为形参；命令发送将在结果上执行。 此方法会以字符串形式返回窗口内容；是否包括窗口中的空白将受到 [`stripspaces`]({{< ref "/library/allos/curses#curses.textpad.Textbox.stripspaces" >}}) 属性的影响。

## **do_command**(*ch*)

​	处理单个按键命令。以下是支持的特殊按键：

| 按键      | 动作                                                         |
| :-------- | :----------------------------------------------------------- |
| Control-A | 转到窗口的左边缘。                                           |
| Control-B | 光标向左，如果可能，包含前一行。                             |
| Control-D | 删除光标下的字符。                                           |
| Control-E | 前往右边缘（stripspaces 关闭时）或者行尾（stripspaces 启用时）。 |
| Control-F | 向右移动光标，适当时换行到下一行。                           |
| Control-G | 终止，返回窗口内容。                                         |
| Control-H | 向后删除字符。                                               |
| Control-J | 如果窗口是1行则终止，否则插入换行符。                        |
| Control-K | 如果行为空，则删除它，否则清除到行尾。                       |
| Control-L | 刷新屏幕。                                                   |
| Control-N | 光标向下;向下移动一行。                                      |
| Control-O | 在光标位置插入一个空行。                                     |
| Control-P | 光标向上;向上移动一行。                                      |

​	如果光标位于无法移动的边缘，则移动操作不执行任何操作。在可能的情况下，支持以下同义词：

| 常量                                                         | 按键      |
| :----------------------------------------------------------- | :-------- |
| [`KEY_LEFT`]({{< ref "/library/allos/curses#curses.KEY_LEFT" >}}) | Control-B |
| [`KEY_RIGHT`]({{< ref "/library/allos/curses#curses.KEY_RIGHT" >}}) | Control-F |
| [`KEY_UP`]({{< ref "/library/allos/curses#curses.KEY_UP" >}}) | Control-P |
| [`KEY_DOWN`]({{< ref "/library/allos/curses#curses.KEY_DOWN" >}}) | Control-N |
| [`KEY_BACKSPACE`]({{< ref "/library/allos/curses#curses.KEY_BACKSPACE" >}}) | Control-h |

​	所有其他按键将被视为插入给定字符并右移的命令（带有自动折行）。

## **gather**()

​	以字符串形式返回窗口内容；是否包括窗口中的空白将受到 [`stripspaces`]({{< ref "/library/allos/curses#curses.textpad.Textbox.stripspaces" >}}) 成员的影响。

## **stripspaces**

​	此属性是控制窗口中空白解读方式的旗标。 当启用时，每一行的末尾空白会被忽略；任何将光标定位至末尾空白的光标动作都将改为前往该行末尾，并且在收集窗口内容时将去除末尾空白。
