+++
title = "Tkinter 对话框"
date = 2024-11-15T20:56:44+08:00
weight = 30
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/dialog.html](https://docs.python.org/zh-cn/3.13/library/dialog.html)
>
> 收录该文档的时间：`2024-11-15T20:56:44+08:00`

# Tkinter 对话框



## [`tkinter.simpledialog`]({{< ref "/library/tk/dialog#module-tkinter.simpledialog" >}}) --- 标准 Tkinter 输入对话框

**源码:** [Lib/tkinter/simpledialog.py](https://github.com/python/cpython/tree/3.13/Lib/tkinter/simpledialog.py)

------

[`tkinter.simpledialog`]({{< ref "/library/tk/dialog#module-tkinter.simpledialog" >}}) 模块包含了一些便捷类和函数，用于创建简单的模态对话框，从用户那里读取一个值。

## tkinter.simpledialog.**askfloat**(*title*, *prompt*, ***kw*)

## tkinter.simpledialog.**askinteger**(*title*, *prompt*, ***kw*)

## tkinter.simpledialog.**askstring**(*title*, *prompt*, ***kw*)

​	以上三个函数提供给用户输入期望值的类型的对话框.

## *class* tkinter.simpledialog.**Dialog**(*parent*, *title=None*)

​	自定义对话框的基类.

## **body**(*master*)

​	可以覆盖，用于构建对话框的界面，并返回初始焦点所在的部件。

## **buttonbox**()

​	加入 OK 和 Cancel 按钮的默认行为. 重写自定义按钮布局.



## [`tkinter.filedialog`]({{< ref "/library/tk/dialog#module-tkinter.filedialog" >}}) --- 文件选择对话框.

**源码:** [Lib/tkinter/filedialog.py](https://github.com/python/cpython/tree/3.13/Lib/tkinter/filedialog.py)

------

[`tkinter.filedialog`]({{< ref "/library/tk/dialog#module-tkinter.filedialog" >}}) 模块提供了用于创建文件/目录选择窗口的类和工厂函数。

### 原生的载入/保存对话框.

​	以下类和函数提供了文件对话窗口，这些窗口带有原生外观，具备可定制行为的配置项。这些关键字参数适用于下列类和函数：

> *parent* —— 对话框下方的窗口
>
> *title* —— 窗口的标题
>
> *initialdir* —— 对话框的启动目录
>
> *initialfile* —— 打开对话框时选中的文件
>
> *filetypes* —— （标签，匹配模式）元组构成的列表，允许使用 “*” 通配符
>
> *defaultextension* —— 默认的扩展名，用于加到文件名后面（保存对话框）。
>
> *multiple* —— 为 True 则允许多选

​	** 静态工厂函数 **

​	调用以下函数时，会创建一个模态的、原生外观的对话框，等待用户选取，然后将选中值或 `None` 返回给调用者。

## tkinter.filedialog.**askopenfile**(*mode='r'*, ***options*)

## tkinter.filedialog.**askopenfiles**(*mode='r'*, ***options*)

​	上述两个函数创建了 [`Open`]({{< ref "/library/tk/dialog#tkinter.filedialog.Open" >}}) 对话框，并返回一个只读模式打开的文件对象。

## tkinter.filedialog.**asksaveasfile**(*mode='w'*, ***options*)

​	创建 [`SaveAs`]({{< ref "/library/tk/dialog#tkinter.filedialog.SaveAs" >}}) 对话框并返回一个写入模式打开的文件对象。

## tkinter.filedialog.**askopenfilename**(***options*)

## tkinter.filedialog.**askopenfilenames**(***options*)

​	以上两个函数创建了 [`Open`]({{< ref "/library/tk/dialog#tkinter.filedialog.Open" >}}) 对话框，并返回选中的文件名，对应着已存在的文件。

## tkinter.filedialog.**asksaveasfilename**(***options*)

​	创建 [`SaveAs`]({{< ref "/library/tk/dialog#tkinter.filedialog.SaveAs" >}}) 对话框，并返回选中的文件名。

## tkinter.filedialog.**askdirectory**(***options*)

提示用户选择一个目录.

其他关键字参数：

*mustexist* —— 确定是否必须为已存在的目录。

## *class* tkinter.filedialog.**Open**(*master=None*, ***options*)

## *class* tkinter.filedialog.**SaveAs**(*master=None*, ***options*)

​	上述两个类提供了用于保存和加载文件的原生对话窗口。

​	** 便捷类 **

​	以下类用于从头开始创建文件/目录窗口。不会模仿当前系统的原生外观。

## *class* tkinter.filedialog.**Directory**(*master=None*, ***options*)

​	创建对话框，提示用户选择一个目录。

​	备注

 

​	为了实现自定义的事件处理和行为，应继承 *FileDialog* 类。

## *class* tkinter.filedialog.**FileDialog**(*master*, *title=None*)

​	创建一个简单的文件选择对话框。

## **cancel_command**(*event=None*)

​	触发对话窗口的终止。

## **dirs_double_event**(*event*)

​	目录双击事件的处理程序。

## **dirs_select_event**(*event*)

​	目录单击事件的处理程序。

## **files_double_event**(*event*)

​	文件双击事件的处理程序。

## **files_select_event**(*event*)

​	文件单击事件的处理程序。

## **filter_command**(*event=None*)

​	按目录筛选文件。

## **get_filter**()

​	获取当前使用的文件筛选器。

## **get_selection**()

​	获取当前选中项。

## **go**(*dir_or_file=os.curdir*, *pattern='\*'*, *default=''*, *key=None*)

​	显示对话框并启动事件循环。

## **ok_event**(*event*)

​	退出对话框并返回当前选中项。

## **quit**(*how=None*)

​	退出对话框并返回文件名。

## **set_filter**(*dir*, *pat*)

​	设置文件筛选器。

## **set_selection**(*file*)

​	将当前选中文件更新为 *file*。

## *class* tkinter.filedialog.**LoadFileDialog**(*master*, *title=None*)

​	FileDialog 的一个子类，创建用于选取已有文件的对话窗口。

## **ok_command**()

​	检测有否给出文件，以及选中的文件是否存在。

## *class* tkinter.filedialog.**SaveFileDialog**(*master*, *title=None*)

​	FileDialog 的一个子类，创建用于选择目标文件的对话窗口。

## **ok_command**()

​	检测选中文件是否为目录。如果选中了已存在文件，则需要用户进行确认。



## [`tkinter.commondialog`]({{< ref "/library/tk/dialog#module-tkinter.commondialog" >}}) --- 对话窗口模板

**源码:**：[Lib/tkinter/commondialog.py](https://github.com/python/cpython/tree/3.13/Lib/tkinter/commondialog.py)

------

[`tkinter.commondialog`]({{< ref "/library/tk/dialog#module-tkinter.commondialog" >}}) 模块提供了 [`Dialog`]({{< ref "/library/tk/dialog#tkinter.commondialog.Dialog" >}}) 类，是其他模块定义的对话框的基类。

## *class* tkinter.commondialog.**Dialog**(*master=None*, ***options*)

## **show**(*color=None*, ***options*)

​	显示对话窗口。

​	参见

 

​	模块 [`tkinter.messagebox`]({{< ref "/library/tk/tkinter_messagebox#module-tkinter.messagebox" >}}), [读写文件]({{< ref "/tutorial/inputoutput#tut-files" >}})
