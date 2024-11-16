+++
title = "tkinter.ttk --- Tk 带主题的控件"
date = 2024-11-15T20:56:44+08:00
weight = 70
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html)
>
> 收录该文档的时间：`2024-11-15T20:56:44+08:00`

# `tkinter.ttk` --- Tk 带主题的控件

**源代码:** [Lib/tkinter/ttk.py](https://github.com/python/cpython/tree/3.13/Lib/tkinter/ttk.py)

------

[`tkinter.ttk`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#module-tkinter.ttk) 模块自 Tk 8.5 开始引入，它提供了对 Tk 风格的部件集的访问。 它还带来了一些额外好处包括在 X11 下的反锯齿字体渲染和透明化窗口（需要有 X11 上的混合窗口管理器）。

[`tkinter.ttk`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#module-tkinter.ttk) 的基本设计思路，就是尽可能地把控件的行为代码与实现其外观的代码分离开来。

​	参见

[Tk 控件风格](https://core.tcl.tk/tips/doc/trunk/tip/48.md)

​	介绍 Tk 风格的文档

## ttk 的用法

​	使用 ttk 之前，首先要导入模块：

```
from tkinter import ttk
```

​	为了覆盖基础的 Tk 控件，应该在 Tk 之后进行导入：

```
from tkinter import *
from tkinter.ttk import *
```

​	这段代码会让以下几个 `tkinter. ttk` 控件（`Button`, `Checkbutton`, `Entry`, `Frame`, `Label`, `LabelFrame`, `Menubutton`, `PanedWindow`, `Radiobutton`, `Scale` 和 `Scrollbar`）自动替换掉 Tk 的对应控件。

​	使用新控件的直接好处，是拥有更好的跨平台的外观，但新旧控件并不完全兼容。主要区别在于，Ttk 组件不再包含“fg”、“bg”等与样式相关的属性 。而是用 `ttk.Style` 类来定义更美观的样式效果。

​	参见

[将现有应用程序转换为使用 Tile 部件](https://tktable.sourceforge.net/tile/doc/converting.txt)

​	此文介绍迁移为新控件时的常见差别（使用 Tcl ）。

## ttk 控件

​	ttk 中有 18 种部件 ，其中 12 种在 tkinter 中已包含了： `Button` 、 `Checkbutton` 、`Entry` 、 `Frame` 、 `Label`, `LabelFrame` 、 `Menubutton` 、`PanedWindow` 、`Radiobutton` 、 `Scale` 、 `Scrollbar` 和 [`Spinbox`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Spinbox)。另有 6 种是新增的： [`Combobox`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Combobox) 、 [`Notebook`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Notebook) 、 [`Progressbar`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Progressbar) 、 `Separator` 、 `Sizegrip` 和 [`Treeview`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Treeview)。这些控件全都是 [`Widget`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Widget) 的子类。

​	ttk 控件可以改善应用程序的外观。如上所述，修改样式的代码与 tk 控件存在差异。

​	Tk 代码:

```
l1 = tkinter.Label(text="Test", fg="black", bg="white")
l2 = tkinter.Label(text="Test", fg="black", bg="white")
```

​	Ttk 代码：

```
style = ttk.Style()
style.configure("BW.TLabel", foreground="black", background="white")

l1 = ttk.Label(text="Test", style="BW.TLabel")
l2 = ttk.Label(text="Test", style="BW.TLabel")
```

​	有关 [TtkStyling](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#ttkstyling) 的更多信息，请参阅 [`Style`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Style) 类文档。

## 控件

`ttk.Widget` 定义了 Tk 风格控件支持的标准属性和方法，不应直接对其进行实例化。

### 标准属性

​	所有 `ttk` 控件均接受以下选项:

| 属性      | 描述                                                         |
| :-------- | :----------------------------------------------------------- |
| class     | 指定窗口类。若要从参数库中查找窗口的其他属性，或确认窗口的默认绑定标签，或选择控件的默认布局和样式，会用到 class 属性。该属性只读，且只能在创建窗口时指定。 |
| cursor    | 指定控件使用的鼠标光标。若设为空字符串（默认值），则会继承父控件的光标。 |
| takefocus | 决定了窗口是否可用键盘获得焦点。返回 0 、1 或空字符串。若返回 0，则表示在用键盘遍历时应该跳过该窗口。如果为 1，则表示只要窗口可见即应接收输入焦点。而空字符串则表示由遍历代码决定窗口是否接收焦点。 |
| style     | 可用于指定自定义控件样式。                                   |

### 可滚动控件的属性

​	带滚动条的控件支持以下属性：

| 属性           | 描述                                                         |
| :------------- | :----------------------------------------------------------- |
| xscrollcommand | 用于与水平滚动条通讯.当窗口中的可见内容发生变化时，控件将根据 scrollcommand 生成 Tcl 命令。通常该属性由一些滚动条的 `Scrollbar.set()` 方法组成。当窗口中的可见内容发生变化时，将会刷新滚动条的状态。 |
| yscrollcommand | 用于与垂直滚动条通讯，更多信息请参考上一条。                 |

### 标签控件的属性

​	标签、按钮和类似按钮的控件支持以下属性。

| 属性         | 描述                                                         |
| :----------- | :----------------------------------------------------------- |
| text         | 指定显示在控件内的文本。                                     |
| textvariable | 指定一个变量名，其值将用于设置 text 属性。                   |
| underline    | 设置文本字符串中带下划线字符的索引（基于0）。下划线字符用于激活快捷键。 |
| image        | 指定一个用于显示的图片。这是一个由1个或多个元素组成的列表。第一个元素是默认的图片名称。列表的其余部分是由 [`Style.map()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Style.map) 定义的“状态/值对”的序列，指定控件在某状态或状态组合时要采用的图片。列表中的所有图片应具备相同的尺寸。 |
| compound     | 指定同时存在 text 和 image 属性时，应如何显示文本和对应的图片。合法的值包括：text:：只显示文本image：只显示图片top、bottom、left、right：分别在文本的上、下、左、右显示图片。none：默认值。 如果给出了图片则显示，否则显示文本。 |
| width        | 如果值大于零，指定文本标签留下多少空间，单位是字符数；如果值小于零，则指定最小宽度。如果等于零或未指定，则使用文本标签本身的宽度。 |

### 兼容性属性

| 属性  | 描述                                                         |
| :---- | :----------------------------------------------------------- |
| state | 可以设为“normal”或“disabled”，以便控制“禁用”状态标志位。本属性只允许写入：用以改变控件的状态，但 [`Widget.state()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Widget.state) 方法不影响本属性。 |

### 控件状态

​	控件状态是多个相互独立的状态标志位的组合。

| 标志位     | 描述                                                         |
| :--------- | :----------------------------------------------------------- |
| active     | 鼠标光标经过控件并按下鼠标按钮，将引发动作。                 |
| disabled   | 控件处于禁用状态，而由程序控制。                             |
| focus      | 控件接受键盘焦点。                                           |
| pressed    | 控件已被按下。                                               |
| selected   | 勾选或单选框之类的控件，表示启用、选中状态。                 |
| background | Windows 和 Mac 系统的窗口具有“激活”或后台的概念。后台窗口的控件会设置 *foreground* 参数，而前台窗口的控件则会清除此状态。 |
| readonly   | 控件不允许用户修改。                                         |
| alternate  | 控件的备选显式格式。                                         |
| invalid    | 控件的值是无效的                                             |

​	所谓的控件状态，就是一串状态名称的组合，可在某个名称前加上感叹号，表示该状态位是关闭的。

### ttk.Widget

​	除了以下方法之外，`ttk.Widget` 还支持 `tkinter.Widget.cget()` 和 `tkinter.Widget.configure()` 方法。

## *class* tkinter.ttk.**Widget**

## **identify**(*x*, *y*)

​	返回位于 *x* *y* 的控件名称，如果该坐标点不属于任何控件，则返回空字符串。

*x* 和 *y* 是控件内的相对坐标，单位是像素。

## **instate**(*statespec*, *callback=None*, **args*, ***kw*)

​	检测控件的状态。如果没有设置回调函数，那么当控件状态符合 *statespec* 时返回 `True`，否则返回 `False`。如果指定了回调函数，那么当控件状态匹配 *statespec* 时将会调用回调函数，且会带上后面的参数。

## **state**(*statespec=None*)

​	修改或查询部件状态。 如果指定了 *statespec*，则会用它来设置部件状态并返回一个新的 *statespec* 来指明哪些旗标做过改动。 如果未指定 *statespec*，则返回当前启用的状态旗标。

*statespec* 通常是个列表或元组。

## Combobox

`ttk.Combobox` 控件是文本框和下拉列表的组合体。该控件是 `Entry` 的子类。

​	除了从 [`Widget`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Widget) 继承的 `Widget.cget()` 、 `Widget.configure()` 、[`Widget.identify()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Widget.identify) 、[`Widget.instate()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Widget.instate) 和 [`Widget.state()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Widget.state) 方法，以及从 `Entry` 继承的 `Entry.bbox()` 、 `Entry.delete()` 、 `Entry.icursor()` 、`Entry.index()` 、 `Entry.insert()` 、 `Entry.selection()` 、 `Entry.xview()` 方法，控件还自带了其他几个方法，在 `ttk.Combobox` 中都有介绍。

### 属性

​	控件可设置以下属性：

| 属性            | 描述                                                         |
| :-------------- | :----------------------------------------------------------- |
| exportselection | 布尔值，如果设为 True，则控件的选中文字将关联为窗口管理器的选中文字（可由 Misc.selection_get 返回）。 |
| justify         | 指定文本在控件中的对齐方式。可为 left、center、right 之一。  |
| height          | 设置下拉列表框的高度。                                       |
| postcommand     | 在显示之前将被调用的代码（可用 Misc.register 进行注册）。可用于选择要显示的值。 |
| state           | normal 、readonly 或 disabled。在 readonly 状态下，数据不能直接编辑，用户只能从下拉列表中选取。在 normal 状态下，可直接编辑文本框。在 disabled 状态下，无法做任何交互。 |
| textvariable    | 设置一个变量名，其值与控件的值关联。每当该变量对应的值发生变动时，控件值就会更新，反之亦然。参见 `tkinter.StringVar` 。 |
| values          | 设置显示于下拉列表中的值。                                   |
| width           | 设置为整数值，表示输入窗口的应有宽度，单位是字符单位（控件字体的平均字符宽度）。 |

### 虚拟事件

​	当用户从下拉列表中选择某个元素时，控件会生成一条 **<<ComboboxSelected>>** 虚拟事件。

### ttk.Combobox

## *class* tkinter.ttk.**Combobox**

## **current**(*newindex=None*)

​	如果给出了 *newindex*，则把控件值设为 *newindex* 位置的元素值。否则，返回当前值的索引，当前值未在列表中则返回 -1。

## **get**()

​	返回控件的当前值。

## **set**(*value*)

​	设置控件的值为 *value* 。

## Spinbox

`ttk.Spinbox` 控件是 `ttk.Entry` 的扩展，带有递增和递减箭头。可用于数字或字符串列表。这是 `Entry` 的子类。

​	除了从 [`Widget`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Widget) 继承的 `Widget.cget()` 、 `Widget.configure()` 、`Widget.identified()` 、 [`Widget.instate()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Widget.instate) 和 [`Widget.state()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Widget.state) 方法，以及从 `Entry` 继承的 `Entry. bbox()` 、 `Entry.delete()` 、 `Entry.icursor()` 、`Entry.index()` 、 `Entry.insert()` 、 `Entry.xview()` 方法，控件还自带了其他一些方法，在 `ttk.Spinbox` 中都有介绍。

### 属性

​	控件可设置以下属性：

| 属性      | 描述                                                         |
| :-------- | :----------------------------------------------------------- |
| from      | 浮点值。如若给出，则为递减按钮能够到达的最小值。作为参数使用时必须写成 `from_`，因为 `from` 是 Python 关键字。 |
| to        | 浮点值。如若给出，则为递增按钮能够到达的最大值。             |
| increment | 浮点值。指定递增/递减按钮每次的修改量。默认值为 1.0。        |
| values    | 字符串或浮点值构成的序列。如若给出，则递增/递减会在此序列元素间循环，而不是增减数值。 |
| wrap      | 布尔值。若为 `True` ，则递增和递减按钮会由 `to` 值循环至 `from` 值，或由 `from` 值循环至 `to` 值。 |
| format    | 字符串。指定递增/递减按钮的数字格式。必须以“%W.Pf”的格式给出，W 是填充的宽度，P 是小数精度，% 和 f 就是本身的含义。 |
| command   | Python 回调函数。只要递增或递减按钮按下之后，就会进行不带参数的调用。 |

### 虚拟事件

​	用户若按下 <Up> ，则控件会生成 **<<Increment>>** 虚拟事件，若按下 <Down> 则会生成 **<<Decrement>>** 事件。

### ttk.Spinbox

## *class* tkinter.ttk.**Spinbox**

## **get**()

​	返回控件的当前值。

## **set**(*value*)

​	设置控件值为 *value*。

## Notebook

​	Ttk Notebook 部件可管理由窗口组成的多项集并每次显示其中的某一个。 每个子窗口都与一个选项卡相关联，用户可以选择该选项卡来改变当前所显示的窗口。

### 属性

​	控件可设置以下属性：

| 属性    | 描述                                                         |
| :------ | :----------------------------------------------------------- |
| height  | 如若给出且大于 0，则指定面板的应有高度（不含内部 padding 或 tab）。否则会采用所有子窗口面板的最大高度。 |
| padding | 指定在控件外部添加的留白。padding 是最多包含四个值的列表，指定左顶右底的空间。如果给出的元素少于四个，底部值默认为顶部值，右侧值默认为左侧值，顶部值默认为左侧值。 |
| width   | 若给出且大于 0，则设置面板的应有宽度（不含内部 padding）。否则将采用所有子窗口面板的最大宽度。 |

### Tab 属性

​	Tab 特有属性如下：

| 属性      | 描述                                                         |
| :-------- | :----------------------------------------------------------- |
| state     | 可为 normal、disabled 或 disabled 之一。若为 disabled 则不能选中。若为 hidden 则不会显示。 |
| sticky    | 指定子窗口在面板内的定位方式。应为包含零个或多个 n、s、e 、w 字符的字符串。每个字母表示子窗口应紧靠的方向（北、南、东或西），正如 `grid()` 位置管理器所述。 |
| padding   | 指定控件和面板之间的留白空间。格式与本控件的 padding 属性相同。 |
| text      | 指定显示在 tab 上的文本。                                    |
| image     | 指定显示在 tab 上的图片。参见 [`Widget`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Widget) 的 image 属性。 |
| compound  | 当文本和图片同时存在时，指定图片相对于文本的显示位置。合法的属性值参见 [Label Options](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#label-options) 。 |
| underline | 指定下划线在文本字符串中的索引（基于0）。如果调用过了 [`Notebook.enable_traversal()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Notebook.enable_traversal)，带下划线的字符将用于激活快捷键。 |

### Tab ID

​	The tab_id present in several methods of `ttk.Notebook` may take any of the following forms:

- 介于 0 和 tab 总数之间的整数值。
- 子窗口的名称。
- 以“@x,y”形式给出的位置，唯一标识了 tab 页。
- 字符串字面值 "current"，它标识当前被选中的选项卡
- 字符串字面值 "end"，它返回标签页的数量 (仅适用于 [`Notebook.index()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Notebook.index))

### 虚拟事件

​	当选中一个新 tab 页之后，控件会生成一条 **<<NotebookTabChanged>>** 虚拟事件。

### ttk.Notebook

## *class* tkinter.ttk.**Notebook**

## **add**(*child*, ***kw*)

​	添加一个新 tab 页。

​	如果窗口是由 Notebook 管理但处于隐藏状态，则会恢复到之前的位置。

​	可用属性请参见 [Tab Options](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tab-options) 。

## **forget**(*tab_id*)

​	删除 *tab_id* 指定的 tab 页，对其关联的窗口不再作映射和管理。

## **hide**(*tab_id*)

​	隐藏 *tab_id* 指定的 tab 页。

​	tab 页不会显示出来，但关联的窗口仍接受 Notebook 的管理，其配置属性会继续保留。隐藏的 tab 页可由 [`add()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Notebook.add) 恢复。

## **identify**(*x*, *y*)

​	返回 tab 页内位置为 *x*、*y* 的控件名称，若不存在则返回空字符串。

## **index**(*tab_id*)

​	返回 *tab_id* 指定 tab 页的索引值，如果 *tab_id* 为 end 则返回 tab 页的总数。

## **insert**(*pos*, *child*, ***kw*)

​	在指定位置插入一个 tab。

*pos* 可为字符串“end” 、整数索引值或子窗口名称。如果 *child* 已由 Notebook 管理，则将其移至指定位置。

​	可用属性请参见 [Tab Options](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tab-options) 。

## **select**(*tab_id=None*)

​	选中 *tab_id* 指定 tab。

​	关联的子窗口将被显示，而之前所选择的窗口（如果不同）将被取消映射关系。 如果省略 *tab_id*，则返回当前被选中的面板的部件名称。

## **tab**(*tab_id*, *option=None*, ***kw*)

​	查询或修改 *tab_id* 指定 tab 的属性。

​	如果未给出 *kw* ，则返回由 tab 属性组成的字典。如果指定了 *option*，则返回其值。否则，设置属性值。

## **tabs**()

​	返回 Notebook 管理的窗口列表。

## **enable_traversal**()

​	为包含 Notebook 的顶层窗口启用键盘遍历。

​	这将为包含 Notebook 的顶层窗口增加如下键盘绑定关系：

- Control-Tab ：选中当前 tab 之后的页。
- Shift-Control-Tab ：选中当前 tab 之前的页。
- Alt-K ：这里 *K* 是任意 tab 页的快捷键（带下划线）字符，将会直接选中该 tab。

​	一个顶层窗口中可为多个 Notebook 启用键盘遍历，包括嵌套的 Notebook 。但仅当所有面板都将所在 Notebook 作为父控件时，键盘遍历才会生效。

## Progressbar

`ttk.Progressbar` 控件可为长时间操作显示状态。可工作于两种模式：1）determinate 模式，显示相对完成进度；2） indeterminate 模式，显示动画让用户知道工作正在进行中。

### 属性

​	控件可设置以下属性：

| 属性     | 描述                                                         |
| :------- | :----------------------------------------------------------- |
| orient   | horizontal 或 vertical。指定进度条的显示方向。               |
| length   | 指定进度条长轴的长度（横向为宽度，纵向则为高度）。           |
| mode     | determinate 或 indeterminate。                               |
| maximum  | 设定最大值。默认为 100。                                     |
| value    | 进度条的当前值。在 determinate 模式下代表已完成的工作量。在 indeterminate 模式下，解释为 *maximum* 的模；也就是说，当本值增至 *maximum* 时，进度条完成了一个“周期”。 |
| variable | 与属性值关联的变量名。若给出，则当变量值变化时会自动设为进度条的值。 |
| phase    | 只读属性。只要值大于 0 且在 determinate 模式下小于最大值，控件就会定期增大该属性值。当前主题可利用本属性提供额外的动画效果。 |

### ttk.Progressbar

## *class* tkinter.ttk.**Progressbar**

## **start**(*interval=None*)

​	开启自增模式：安排一个循环的定时器事件，每隔 *interval* 毫秒调用一次 [`Progressbar.step()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Progressbar.step)。*interval* 可省略，默认为 50毫秒。

## **step**(*amount=None*)

​	将进度条的值增加 *amount*。

*amount* 可省略，默认为 1.0。

## **stop**()

​	停止自增模式：取消所有由 [`Progressbar.start()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Progressbar.start) 启动的循环定时器事件。

## Separator

`ttk.Separator` 控件用于显示横向或纵向的分隔条。

​	除由 `ttk.Widget` 继承而来的方法外，没有定义其他方法。

### 属性

​	属性如下：

| 属性   | 描述                                       |
| :----- | :----------------------------------------- |
| orient | horizontal 或 vertical。指定分隔条的方向。 |

## Sizegrip

`ttk.Sizegrip` 控件允许用户通过按下并拖动控制柄来调整内部顶层窗口的大小。

​	除由 `ttk.Widget` 继承的之外，没有其他属性和方法。

### 与平台相关的注意事项

- 在 macOS 上，顶层窗口默认自动包括了一个内置的大小控制柄。 再加一个 `Sizegrip` 也没什么坏处，因为内置的控制柄会盖住该控件。

### Bug

- 假如内部的顶层窗口位置是相对于屏幕的右侧或底部进行设置的，那么 `Sizegrip` 控件将不会改变窗口的大小。
- Sizegrip 仅支持往“东南”方向的缩放。

## Treeview

`ttk.Treeview` 控件可将多项内容分层级显示。每个数据项都带有一个文本标签、一个可选的图片和一个可选的数据值列表。 这些数据值将在树标签后面分列显示。

​	数据值的显示顺序可用属性 `displaycolumns` 进行控制。树控件还可以显示列标题。数据列可通过数字或名称进行访问，各列的名称在属性 columns 中列出。参阅 [Column Identifiers](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#column-identifiers)。

​	每个数据项都由唯一名称进行标识。如果调用者未提供数据项的 ID，树控件会自动生成。根有且只有一个，名为 `{}`。根本身不会显示出来；其子项将显示在顶层。

​	每个数据项均带有一个 tag 列表，可用于绑定事件及控制外观。

​	Treeview 组件支持水平和垂直滚动，滚动时会依据 [Scrollable Widget Options](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#scrollable-widget-options) 描述的属性和 [`Treeview.xview()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Treeview.xview) 和 [`Treeview.yview()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Treeview.yview) 方法。

### 属性

​	控件可设置以下属性：

| 属性           | 描述                                                         |
| :------------- | :----------------------------------------------------------- |
| columns        | 列标识的列表，定义了列的数量和名称。                         |
| displaycolumns | 列标识的列表（索引可为符号或整数），指定要显示的数据列及显示顺序，或为字符串 “#all”。 |
| height         | 指定可见的行数。注意：所需宽度由各列宽度之和决定。           |
| padding        | 指定控件内部的留白。为不超过四个元素的长度列表。             |
| selectmode     | 控制内部类如何进行选中项的管理。可为 extended、browse 或 none。若设为 extended（默认），则可选中多个项。若为 browse ，则每次只能选中一项。若为 none，则无法修改选中项。请注意，代码和 tag 绑定可自由进行选中操作，不受本属性的限制。 |
| show           | 由0个或下列值组成的列表，指定要显示树的哪些元素。tree ：在 #0 列显示树的文本标签。headings ：显示标题行。默认为“tree headings”，显示所有元素。** 注意** ：第 #0 列一定是指 tree 列，即便未设置 show="tree" 也一样。 |

### 数据项的属性

​	可在插入和数据项操作时设置以下属性。

| 属性   | 描述                                                         |
| :----- | :----------------------------------------------------------- |
| text   | 用于显示的文本标签。                                         |
| image  | Tk 图片对象，显示在文本标签左侧。                            |
| values | 关联的数据值列表。每个数据项关联的数据数量应与 columns 属性相同。如果比 columns 属性的少，剩下的值将视为空。如果多于 columns 属性的，多余数据将被忽略。 |
| open   | `True` 或 `False`，表明是否显示数据项的子树。                |
| tags   | 与该数据项关联的 tag 列表。                                  |

### tag 属性

​	tag 可定义以下属性：

| 属性       | 描述                                        |
| :--------- | :------------------------------------------ |
| foreground | 定义文本前景色。                            |
| background | 定义单元格或数据项的背景色。                |
| font       | 定义文本的字体。                            |
| image      | 定义数据项的图片，当 image 属性为空时使用。 |

### 列标识

​	列标识可用以下格式给出：

- 由 columns 属性给出的符号名。
- 整数值 n，指定第 n 列。
- \#n 的字符串格式，n 是整数，指定第 n 个显示列。

​	注意：

- 数据项属性的显示顺序可能与存储顺序不一样。
- \#0 列一定是指 tree 列，即便未指定 show="tree" 也是一样。

​	数据列号是指属性值列表中的索引值，显示列号是指显示在树控件中的列号。树的文本标签将显示在 #0 列。如果未设置 displaycolumns 属性，则数据列 n 将显示在第 #n+1 列。再次强调一下，**#0 列一定是指 tree 列** 。

### 虚拟事件

​	Treeview 控件会生成以下虚拟事件。

| 事件               | 描述                                     |
| :----------------- | :--------------------------------------- |
| <<TreeviewSelect>> | 当选中项发生变化时生成。                 |
| <<TreeviewOpen>>   | 当焦点所在项的 open= True 之前立即生成。 |
| <<TreeviewClose>>  | 当焦点所在项的 open= True 之后立即生成。 |

[`Treeview.focus()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Treeview.focus) 和 [`Treeview.selection()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Treeview.selection) 方法可用于确认涉及的数据项。

### ttk.Treeview

## *class* tkinter.ttk.**Treeview**

## **bbox**(*item*, *column=None*)

​	返回某 *数据项* 的边界（相对于控件窗口的坐标），形式为 (x, y, width, height) 。

​	若给出了 *column*，则返回该单元格的边界。若该 *数据项* 不可见（即从属于已关闭项或滚动至屏幕外），则返回空字符串。

## **get_children**(*item=None*)

​	返回 *item* 的下属数据项列表。

​	若未给出 *item* ，则返回根的下属数据。

## **set_children**(*item*, **newchildren*)

​	用 *newchildren* 替换 *item* 的下属数据。

​	对于 *item* 中存在而 *newchildren* 中不存在的数据项，会从树中移除。*newchildren* 中的数据不能是 *item* 的上级。注意，未给出 *newchildren* 会导致 *item* 的子项被解除关联。

## **column**(*column*, *option=None*, ***kw*)

​	查询或修改列 *column* 的属性。

​	如果未给出 *kw*，则返回属性值的字典。若指定了 *option*，则会返回该属性值。否则将设置属性值。

​	合法的 属性/值 可为：

## *id*

​	返回列名。这是只读属性。

## *anchor*: 某个标准 Tk 锚点值。

​	指定该列的文本在单元格内的对齐方式。

## *minwidth*: 宽度

​	列的最小宽度，单位是像素。在缩放控件或用户拖动某一列时，Treeview 会保证列宽不小于此值。

## *stretch*: `True`/`False`

​	指明缩放控件时是否调整列宽。

## *width*: 宽度

​	列宽，单位为像素数。

​	若要设置 tree 列，请带上参数 column = "#0" 进行调用。

## **delete**(**items*)

​	删除所有 *items* 及其下属。

​	根不能删除。

## **detach**(**items*)

​	将所有 *items* 与树解除关联。

​	数据项及其下属依然存在，后续可以重新插入，目前只是不显示出来。

​	根不能解除关联。

## **exists**(*item*)

​	如果给出的 *item* 位于树中，则返回 `True`。

## **focus**(*item=None*)

​	如果给出 *item* 则设为当前焦点。否则返回当前焦点所在数据项，若无则返回 ''。

## **heading**(*column*, *option=None*, ***kw*)

​	查询或修改某 *column* 的标题。

​	若未给出 *kw*，则返回列标题组成的列表。若给出了 *option* 则返回对应属性值。否则，设置属性值。

​	合法的 属性/值 可为：

## *text*: 文本

​	显示为列标题的文本。

## *image*: 图片名称

​	指定显示在列标题右侧的图片。

## *anchor*: 锚点

​	指定列标题文本的对齐方式。应为标准的 Tk 锚点值。

## *command*: 回调

​	点击列标题时执行的回调函数。

​	若要对 tree 列进行设置，请带上 column = "#0" 进行调用。

## **identify**(*component*, *x*, *y*)

​	返回 *x*、*y* 位置上 *component* 数据项的描述信息，如果此处没有该数据项，则返回空字符串。

## **identify_row**(*y*)

​	返回 *y* 位置上的数据项 ID。

## **identify_column**(*x*)

​	返回 *x* 位置上的单元格所在的数据列 ID。

​	tree 列的 ID 为 #0 。

## **identify_region**(*x*, *y*)

​	返回以下值之一：

| 区域      | 含义                       |
| :-------- | :------------------------- |
| heading   | 树的标题栏区域。           |
| separator | 两个列标题之间的间隔区域。 |
| tree      | 树区域。                   |
| cell      | 数据单元格。               |

​	可用性：Tk 8.6。

## **identify_element**(*x*, *y*)

​	返回位于 *x* 、*y* 的数据项。

​	可用性：Tk 8.6。

## **index**(*item*)

​	返回 *item* 在父项的子项列表中的整数索引。

## **insert**(*parent*, *index*, *iid=None*, ***kw*)

​	新建一个数据项并返回其 ID。

*parent* 是父项的 ID，若要新建顶级项则为空字符串。 *index* 是整数或“end”，指明在父项的子项列表中的插入位置。如果 *index* 小于等于0，则在开头插入新节点；如果 *index* 大于或等于当前子节点数，则将其插入末尾。如果给出了 *iid*，则将其用作数据项 ID； *iid* 不得存在于树中。否则会新生成一个唯一 ID。

​	可用的选项列表请参阅 [Item Options](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#item-options)。

## **item**(*item*, *option=None*, ***kw*)

​	查询或修改某 *item* 的属性。

​	如果未给出 option，则返回属性/值构成的字典。如果给出了 *option*，则返回该属性的值。否则，将属性设为 *kw* 给出的值。

## **move**(*item*, *parent*, *index*)

​	将 *item* 移至指定位置，父项为 *parent* ，子项列表索引为 *index* 。

​	将数据项移入其子项之下是非法的。如果 *index* 小于等于0，*item* 将被移到开头；如果大于等于子项的总数，则被移至最后。如果 *item* 已解除关联，则会被重新关联。

## **next**(*item*)

​	返回 *item* 的下一个相邻项，如果 *item* 是父项的最后一个子项，则返回 ''。

## **parent**(*item*)

​	返回 *item* 的父项 ID，如果 *item* 为顶级节点，则返回 ''。

## **prev**(*item*)

​	返回 *item* 的前一个相邻项，若 *item* 为父项的第一个子项，则返回 ''。

## **reattach**(*item*, *parent*, *index*)

[`Treeview.move()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Treeview.move) 的别名。

## **see**(*item*)

​	确保 *item* 可见。

​	将 *item* 所有上级的 open 属性设为 `True`，必要时会滚动控件，让 *item* 处于树的可见部分。

## **selection**()

​	返回由选中项构成的元组。

*在 3.8 版本发生变更:* `selection()` 不再接受参数了。若要改变选中的状态，请使用下面介绍的方法。

## **selection_set**(**items*)

​	让 *items* 成为新的选中项。

*在 3.6 版本发生变更:* *items* 可作为多个单独的参数传递，而不只是作为一个元组。

## **selection_add**(**items*)

​	将 *items* 加入选中项。

*在 3.6 版本发生变更:* *items* 可作为多个单独的参数传递，而不只是作为一个元组。

## **selection_remove**(**items*)

​	从选中项中移除 *items* 。

*在 3.6 版本发生变更:* *items* 可作为多个单独的参数传递，而不只是作为一个元组。

## **selection_toggle**(**items*)

​	切换 *items* 中各项的选中状态。

*在 3.6 版本发生变更:* *items* 可作为多个单独的参数传递，而不只是作为一个元组。

## **set**(*item*, *column=None*, *value=None*)

​	若带一个参数，则返回 *item* 的列/值字典。若带两个参数，则返回 *column* 的当前值。若带三个参数，则将 *item* 的 *column* 设为 *value*。

## **tag_bind**(*tagname*, *sequence=None*, *callback=None*)

​	为 tag 为 *tagname* 的数据项绑定事件 *sequence* 的回调函数。当事件分发给该数据项时，tag 参数为 *tagname* 的全部数据项的回调都会被调用到。

## **tag_configure**(*tagname*, *option=None*, ***kw*)

​	查询或修改 *tagname* 指定项的属性。

​	如果给出了 *kw*，则返回 *tagname* 项的属性字典。如果给出了 *option*，则返回 *tagname* 项的 *option* 属性值。否则，设置 *tagname* 项的属性值。

## **tag_has**(*tagname*, *item=None*)

​	如果给出了 *item* ，则依据 *item* 是否具备 *tagname* 而返回 1 或 0。否则，返回 tag 为 *tagname* 的所有数据项构成的列表。

​	可用性：Tk 8.6。

## **xview**(**args*)

​	查询或修改 Treeview 的横向位置。

## **yview**(**args*)

​	查询或修改 Treeview 的纵向位置。



## Ttk 样式

`ttk` 的每种控件都赋有一个样式，指定了控件内的元素及其排列方式，以及元素属性的动态和默认设置。默认情况下，样式名与控件的类名相同，但可能会被控件的 style 属性覆盖。如果不知道控件的类名，可用 `Misc.winfo_class()` 方法获取（somewidget.winfo_class()）。

​	参见

[Tcl'2004 会议报告](https://tktable.sourceforge.net/tile/tile-tcl2004.pdf)

​	文章解释了主题引擎的工作原理。

## *class* tkinter.ttk.**Style**

​	用于操控样式数据库的类。

## **configure**(*style*, *query_opt=None*, ***kw*)

​	查询或设置 *style* 的默认属性值。

​	Each key in *kw* is an option and each value is a string identifying the value for that option.

​	例如，要将默认按钮改为扁平样式，并带有留白和各种背景色：

```
from tkinter import ttk
import tkinter

root = tkinter.Tk()

ttk.Style().configure("TButton", padding=6, relief="flat",
   background="#ccc")

btn = ttk.Button(text="Sample")
btn.pack()

root.mainloop()
```

## **map**(*style*, *query_opt=None*, ***kw*)

​	查询或设置 *style* 的指定属性的动态值。

*kw* 的每个键都是一个属性，每个值通常应为列表或元组，其中包含以元组、列表或其他形式组合而成的状态标识（statespec）。状态标识是由一个或多个状态组合，加上一个值组成。

​	举个例子能更清晰些：

```
import tkinter
from tkinter import ttk

root = tkinter.Tk()

style = ttk.Style()
style.map("C.TButton",
    foreground=[('pressed', 'red'), ('active', 'blue')],
    background=[('pressed', '!disabled', 'black'), ('active', 'white')]
    )

colored_btn = ttk.Button(text="Test", style="C.TButton").pack()

root.mainloop()
```

​	请注意，要点是属性的（状态，值）序列的顺序，如果前景色属性的顺序改为 `[('active', 'blue'), ('pressed', 'red')]` ，则控件处于激活或按下状态时的前景色将为蓝色。

## **lookup**(*style*, *option*, *state=None*, *default=None*)

​	返回 *style* 中的 *option* 属性值。

​	如果给出了 *state* ，则应是一个或多个状态组成的序列。如果设置了 *default* 参数，则在属性值缺失时会用作后备值。

​	若要检测按钮的默认字体，可以：

```
from tkinter import ttk

print(ttk.Style().lookup("TButton", "font"))
```

## **layout**(*style*, *layoutspec=None*)

​	按照 *style* 定义控件布局。如果省略了 *layoutspec*，则返回该样式的布局属性。

​	若给出了 *layoutspec*，则应为一个列表或其他的序列类型（不包括字符串），其中的数据项应为元组类型，第一项是布局名称，第二项的格式应符合 [Layouts](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#layouts) 的描述。

​	以下示例有助于理解这种格式（这里并没有实际意义）：

```
from tkinter import ttk
import tkinter

root = tkinter.Tk()

style = ttk.Style()
style.layout("TMenubutton", [
   ("Menubutton.background", None),
   ("Menubutton.button", {"children":
       [("Menubutton.focus", {"children":
           [("Menubutton.padding", {"children":
               [("Menubutton.label", {"side": "left", "expand": 1})]
           })]
       })]
   }),
])

mbtn = ttk.Menubutton(text='Text')
mbtn.pack()
root.mainloop()
```

## **element_create**(*elementname*, *etype*, **args*, ***kw*)

​	在当前主题中创建一个新元素，为给定的 *etype*，它应当是 "image", "from" 或 "vsapi"。 后者权在 Windows 版 Tk 8.6 中可用。

​	如果用了 image，则 *args* 应包含默认的图片名，后面跟着 状态标识/值（这里是 imagespec），*kw* 可带有以下属性：

## border=padding

​	padding 是由不超过四个整数构成的列表，分别定义了左、顶、右、底的边界。

## height=height

​	定义了元素的最小高度。如果小于零，则默认采用图片本身的高度。

## padding=padding

​	定义了元素的内部留白。若未指定则默认采用 border 值。

## sticky=spec

​	定义了图片的对齐方式。spec 包含零个或多个 n、s、w、e 字符。

## width=width

​	定义了元素的最小宽度。如果小于零，则默认采用图片本身的宽度。

​	示例:

```
img1 = tkinter.PhotoImage(master=root, file='button.png')
img1 = tkinter.PhotoImage(master=root, file='button-pressed.png')
img1 = tkinter.PhotoImage(master=root, file='button-active.png')
style = ttk.Style(root)
style.element_create('Button.button', 'image',
                     img1, ('pressed', img2), ('active', img3),
                     border=(2, 4), sticky='we')
```

​	如果 *etype* 的值用了 from，则 [`element_create()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Style.element_create) 将复制一个现有的元素。 *args* 应包含主题名和可选的要复制的元素。若未给出要克隆的元素，则采用空元素。 *kw* 参数将被丢弃。

​	示例:

```
style = ttk.Style(root)
style.element_create('plain.background', 'from', 'default')
```

​	如果使用 "vsapi" 作为 *etype* 的值，[`element_create()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Style.element_create) 将在当前主题中创建一个新元素，其视觉外观将使用负责处理 Windows XP 和 Vista 上带主题风格的 Microsoft Visual Styles API 来绘制。 *args* 应当包含 Microsoft 文档中给出的 Visual Styles 类和部件并带有由 ttk 状态及对应 Visual Styles API 状态值组成的元组的可选序列。 *kw* 可能具有下列选项：

## padding=padding

​	指定元素的内边距。 *padding* 是由至多四个分别指定左、上、右、下边距值的整数组成的列表。 如果指定的元素少于四个，则下边距默认等于上边距，右边距默认等于左边距。 换句话说，由三个数字组成的列表将指定左边距、垂直边距和右边距；由两个数字组成的列表将指定水平边距和垂直边距；一个单独数字将为该部件的所有边指定相同的边距。 此选项不可与其他任何选项混用。

## margins=padding

​	指定元素的外边距。 *padding* 是由至多四个分别指定左、上、右、下边距值的整数组成的列表。 此选项不可与其他任何选项混用。

## width=width

​	指定元素的宽度。 如果设置了此选项则不会查询 Visual Styles API 来获取推荐的大小或部件。 如果设置了此选项则还应当设置 *height*。 *width* 和 *height* 选项不可与 *padding* 或 *margins* 选项混用。

## height=height

​	指定元素的高度。 参见 *width* 的注释。

​	示例:

```
style = ttk.Style(root)
style.element_create('pin', 'vsapi', 'EXPLORERBAR', 3, [
                     ('pressed', '!selected', 3),
                     ('active', '!selected', 2),
                     ('pressed', 'selected', 6),
                     ('active', 'selected', 5),
                     ('selected', 4),
                     ('', 1)])
style.layout('Explorer.Pin',
             [('Explorer.Pin.pin', {'sticky': 'news'})])
pin = ttk.Checkbutton(style='Explorer.Pin')
pin.pack(expand=True, fill='both')
```

*在 3.13 版本发生变更:* 增加了对 "vsapi" 元素工厂的支持。

## **element_names**()

​	返回当前主题已定义的元素列表 。

## **element_options**(*elementname*)

​	返回 *elementname* 元素的属性列表。

## **theme_create**(*themename*, *parent=None*, *settings=None*)

​	新建一个主题。

​	如果 *themename* 已经存在，则会报错。如果给出了 *parent*，则新主题将从父主题继承样式、元素和布局。若给出了 *settings* ，则语法应与 [`theme_settings()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Style.theme_settings) 的相同。

## **theme_settings**(*themename*, *settings*)

​	将当前主题临时设为 *themename*，并应用 *settings*，然后恢复之前的主题。

*settings* 中的每个键都是一种样式而每个值可能包含 'configure', 'map', 'layout' 和 'element create' 等键并且它们被预期具有与分别由 [`Style.configure()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Style.configure), [`Style.map()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Style.map), [`Style.layout()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Style.layout) 和 [`Style.element_create()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Style.element_create) 方法所指定的相符的格式。

​	以下例子会对 Combobox 的默认主题稍作修改：

```
from tkinter import ttk
import tkinter

root = tkinter.Tk()

style = ttk.Style()
style.theme_settings("default", {
   "TCombobox": {
       "configure": {"padding": 5},
       "map": {
           "background": [("active", "green2"),
                          ("!disabled", "green4")],
           "fieldbackground": [("!disabled", "green3")],
           "foreground": [("focus", "OliveDrab1"),
                          ("!disabled", "OliveDrab2")]
       }
   }
})

combo = ttk.Combobox().pack()

root.mainloop()
```

## **theme_names**()

​	返回所有已知主题的列表。

## **theme_use**(*themename=None*)

​	若未给出 *themename*，则返回正在使用的主题。否则，将当前主题设为 *themename*，刷新所有控件并引发 <<ThemeChanged>> 事件。

### 布局

​	布局可以为 `None`，如果未传入任何选项，或传入一个指明元素排列方式的字典的话。 布局机制使用简化版本的打包位置管理器：给定一个初始容器，并为每个元素分配一个区块。

​	合法的 属性/值 可为：

## *side*: 边缘

​	指定元素置于容器的哪一侧； 顶、右、底或左。如果省略，则该元素将占据整个容器。

## *sticky*: 方向

​	指定元素在已分配包装盒内的放置位置。

## *unit*: 0 或 1

​	如果设为 1，则将元素及其所有后代均视作单个元素以供 [`Widget.identify()`](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#tkinter.ttk.Widget.identify) 等使用。 它被用于滚动条之类带有控制柄的东西。

## *children*: [子布局... ]

​	指定要放置于元素内的元素列表。每个元素都是一个元组（或其他序列类型），其中第一项是布局名称，另一项是个 [Layout](https://docs.python.org/zh-cn/3.13/library/tkinter.ttk.html#layouts) 。
