+++
title = "winreg --- Windows 注册表访问"
date = 2024-11-15T21:28:55+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/winreg.html](https://docs.python.org/zh-cn/3.13/library/winreg.html)
>
> 收录该文档的时间：`2024-11-15T21:28:55+08:00`

# `winreg` --- Windows 注册表访问

------

​	这些函数将 Windows 注册表 API 暴露给 Python。 为了确保即便程序员忘记显式关闭时也能够正确关闭，这里没有用整数作为注册表句柄，而是采用了 [句柄对象](https://docs.python.org/zh-cn/3.13/library/winreg.html#handle-object)。

*在 3.3 版本发生变更:* 模块中有几个函数用于触发 [`WindowsError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#WindowsError)，此异常现在是 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 的别名。



## 函数

​	该模块提供了下列函数：

## winreg.**CloseKey**(*hkey*)

​	关闭之前打开的注册表键。参数 *hkey* 指之前打开的键。

​	备注

 

​	如果没有使用该方法关闭 *hkey* (或者通过 [`hkey.Close()`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.PyHKEY.Close))，在对象 *hkey* 被 Python 销毁时会将其关闭。

## winreg.**ConnectRegistry**(*computer_name*, *key*)

​	建立到另一台计算机上的预定义注册表句柄的连接，并返回一个 [句柄对象](https://docs.python.org/zh-cn/3.13/library/winreg.html#handle-object)。

*computer_name* 是远程计算机的名称，以 `r"\\computername"` 的形式。如果是 `None` ，将会使用本地计算机。

*key* 是所连接到的预定义句柄。

​	返回值是所开打键的句柄。如果函数失败，则引发一个 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 异常。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.ConnectRegistry` 并附带参数 `computer_name`, `key`。

*在 3.3 版本发生变更:* 参考 [上文](https://docs.python.org/zh-cn/3.13/library/winreg.html#exception-changed)。

## winreg.**CreateKey**(*key*, *sub_key*)

​	创建或打开特定的键，返回一个 [handle 对象](https://docs.python.org/zh-cn/3.13/library/winreg.html#handle-object)。

*key* 为某个已经打开的键，或者预定义的 [HKEY_* 常量](https://docs.python.org/zh-cn/3.13/library/winreg.html#hkey-constants) 之一。

*sub_key* 是用于命名该方法所打开或创建的键的字符串。

​	如果 *key* 是预定义键之一，*sub_key* 可能会是 `None`。该情况下，返回的句柄就是传入函数的句柄。

​	如果键已经存在，则该函数打开已经存在的该键。

​	返回值是所开打键的句柄。如果函数失败，则引发一个 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 异常。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.CreateKey` 并附带参数 `key`, `sub_key`, `access`。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.OpenKey/result` 并附带参数 `key`。

*在 3.3 版本发生变更:* 参考 [上文](https://docs.python.org/zh-cn/3.13/library/winreg.html#exception-changed)。

## winreg.**CreateKeyEx**(*key*, *sub_key*, *reserved=0*, *access=KEY_WRITE*)

​	创建或打开特定的键，返回一个 [handle 对象](https://docs.python.org/zh-cn/3.13/library/winreg.html#handle-object)。

*key* 为某个已经打开的键，或者预定义的 [HKEY_* 常量](https://docs.python.org/zh-cn/3.13/library/winreg.html#hkey-constants) 之一。

*sub_key* 是用于命名该方法所打开或创建的键的字符串。

*reserved* 是一个保留的整数，必须是零。 默认值为零。

*access* 为一个整数，用于给键的预期安全访问指定访问掩码。默认值为 [`KEY_WRITE`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.KEY_WRITE)。 参阅 [Access Rights](https://docs.python.org/zh-cn/3.13/library/winreg.html#access-rights) 了解其它允许值。

​	如果 *key* 是预定义键之一，*sub_key* 可能会是 `None`。该情况下，返回的句柄就是传入函数的句柄。

​	如果键已经存在，则该函数打开已经存在的该键。

​	返回值是所开打键的句柄。如果函数失败，则引发一个 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 异常。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.CreateKey` 并附带参数 `key`, `sub_key`, `access`。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.OpenKey/result` 并附带参数 `key`。

*Added in version 3.2.*

*在 3.3 版本发生变更:* 参考 [上文](https://docs.python.org/zh-cn/3.13/library/winreg.html#exception-changed)。

## winreg.**DeleteKey**(*key*, *sub_key*)

​	删除指定的键。

*key* 为某个已经打开的键，或者预定义的 [HKEY_* 常量](https://docs.python.org/zh-cn/3.13/library/winreg.html#hkey-constants) 之一。

*sub_key* 这个字符串必须是由 *key* 参数所指定键的一个子项。该值项不可以是 `None`，同时键也不可以有子项。

*该方法不能删除带有子项的键。*

​	如果方法成功，则整个键，包括其所有值项都会被移除。如果方法失败，则引发一个 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 异常。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.DeleteKey` 并附带参数 `key`, `sub_key`, `access`。

*在 3.3 版本发生变更:* 参考 [上文](https://docs.python.org/zh-cn/3.13/library/winreg.html#exception-changed)。

## winreg.**DeleteKeyEx**(*key*, *sub_key*, *access=KEY_WOW64_64KEY*, *reserved=0*)

​	删除指定的键。

*key* 为某个已经打开的键，或者预定义的 [HKEY_* 常量](https://docs.python.org/zh-cn/3.13/library/winreg.html#hkey-constants) 之一。

*sub_key* 这个字符串必须是由 *key* 参数所指定键的一个子项。该值项不可以是 `None`，同时键也不可以有子项。

*reserved* 是一个保留的整数，必须是零。 默认值为零。

*access* 是一个指定描述注册表键所需的安全权限的访问掩码的整数。 默认值为 [`KEY_WOW64_64KEY`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.KEY_WOW64_64KEY)。 在 32-bit Windows 上，WOW64 常量会被忽略。 请参阅 [访问权限](https://docs.python.org/zh-cn/3.13/library/winreg.html#access-rights) 了解其他可用的值。

*该方法不能删除带有子项的键。*

​	如果方法成功，则整个键，包括其所有值项都会被移除。如果方法失败，则引发一个 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 异常。

​	在不支持的 Windows 版本之上，将会引发 [`NotImplementedError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#NotImplementedError) 异常。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.DeleteKey` 并附带参数 `key`, `sub_key`, `access`。

*Added in version 3.2.*

*在 3.3 版本发生变更:* 参考 [上文](https://docs.python.org/zh-cn/3.13/library/winreg.html#exception-changed)。

## winreg.**DeleteValue**(*key*, *value*)

​	从某个注册键中删除一个命名值项。

*key* 为某个已经打开的键，或者预定义的 [HKEY_* 常量](https://docs.python.org/zh-cn/3.13/library/winreg.html#hkey-constants) 之一。

*value* 为标识所要删除值项的字符串。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.DeleteValue` 并附带参数 `key`, `value`。

## winreg.**EnumKey**(*key*, *index*)

​	列举某个已经打开注册表键的子项，并返回一个字符串。

*key* 为某个已经打开的键，或者预定义的 [HKEY_* 常量](https://docs.python.org/zh-cn/3.13/library/winreg.html#hkey-constants) 之一。

*index* 为一个整数，用于标识所获取键的索引。

​	每次调用该函数都会获取一个子项的名字。通常它会被反复调用，直到引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 异常，这说明已经没有更多的可用值了。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.EnumKey` 并附带参数 `key`, `index`。

*在 3.3 版本发生变更:* 参考 [上文](https://docs.python.org/zh-cn/3.13/library/winreg.html#exception-changed)。

## winreg.**EnumValue**(*key*, *index*)

​	列举某个已经打开注册表键的值项，并返回一个元组。

*key* 为某个已经打开的键，或者预定义的 [HKEY_* 常量](https://docs.python.org/zh-cn/3.13/library/winreg.html#hkey-constants) 之一。

*index* 为一个整数，用于标识要获取值项的索引。

​	每次调用该函数都会获取一个子项的名字。通常它会被反复调用，直到引发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 异常，这说明已经没有更多的可用值了。

​	结果为3元素的元组。

| 索引 | 含意                                                         |
| :--- | :----------------------------------------------------------- |
| `0`  | 用于标识值项名称的字符串。                                   |
| `1`  | 保存值项数据的对象，其类型取决于背后的注册表类型。           |
| `2`  | 标识值项数据类型的整数。（请查阅 [`SetValueEx()`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.SetValueEx) 文档中的表格） |

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.EnumValue` 并附带参数 `key`, `index`。

*在 3.3 版本发生变更:* 参考 [上文](https://docs.python.org/zh-cn/3.13/library/winreg.html#exception-changed)。

## winreg.**ExpandEnvironmentStrings**(*str*)

​	Expands environment variable placeholders `%NAME%` in strings like [`REG_EXPAND_SZ`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.REG_EXPAND_SZ):

\>>>

```
>>> ExpandEnvironmentStrings('%windir%')
'C:\\Windows'
```

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.ExpandEnvironmentStrings` 并附带参数 `str`。

## winreg.**FlushKey**(*key*)

​	将某个键的所有属性写入注册表。

*key* 为某个已经打开的键，或者预定义的 [HKEY_* 常量](https://docs.python.org/zh-cn/3.13/library/winreg.html#hkey-constants) 之一。

​	没有必要调用 [`FlushKey()`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.FlushKey) 去改动注册表键。注册表的变动是由其延迟刷新机制更新到磁盘的。在系统关机时，也会将注册表的变动写入磁盘。与 [`CloseKey()`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.CloseKey) 不同， [`FlushKey()`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.FlushKey) 方法只有等到所有数据都写入注册表后才会返回。只有需要绝对确认注册表变动已写入磁盘时，应用程序才应去调用 [`FlushKey()`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.FlushKey)。

​	备注

 

​	如果不知道是否要调用 [`FlushKey()`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.FlushKey) ，可能就是不需要。

## winreg.**LoadKey**(*key*, *sub_key*, *file_name*)

​	在指定键之下创建一个子键，并将指定文件中的注册表信息存入该子键中。

*key* 是由 [`ConnectRegistry()`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.ConnectRegistry) 返回的句柄，或者是常量 [`HKEY_USERS`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.HKEY_USERS) 或 [`HKEY_LOCAL_MACHINE`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.HKEY_LOCAL_MACHINE)。

*sub_key* 是个字符串，用于标识需要载入的子键。

*file_name* 是要加载注册表数据的文件名。该文件必须是用 [`SaveKey()`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.SaveKey) 函数创建的。在文件分配表（FAT）文件系统中，文件名可能不带扩展名。

​	如果调用 [`LoadKey()`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.LoadKey) 的进程没有 `SE_RESTORE_PRIVILEGE` 特权则调用将失败。 请注意特权与权限是不同的 -- 更多细节请参阅 [RegLoadKey 文档](https://msdn.microsoft.com/en-us/library/ms724889(v=VS.85).aspx)。

​	如果 *key* 是由 [`ConnectRegistry()`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.ConnectRegistry) 返回的句柄，那么 *file_name* 指定的路径是相对于远程计算机而言的。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.LoadKey` 并附带参数 `key`, `sub_key`, `file_name`。

## winreg.**OpenKey**(*key*, *sub_key*, *reserved=0*, *access=KEY_READ*)

## winreg.**OpenKeyEx**(*key*, *sub_key*, *reserved=0*, *access=KEY_READ*)

​	打开指定的注册表键，返回 [handle对象](https://docs.python.org/zh-cn/3.13/library/winreg.html#handle-object)。

*key* 为某个已经打开的键，或者预定义的 [HKEY_* 常量](https://docs.python.org/zh-cn/3.13/library/winreg.html#hkey-constants) 之一。

*sub_key* 是个字符串，标识了需要打开的子键。

*reserved* 是个保留整数，必须为零。默认值为零。

*access* 是个指定访问掩码的整数，掩码描述了注册表键所需的安全权限。 默认值为 [`KEY_READ`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.KEY_READ)。 其他合法值参见 [访问权限](https://docs.python.org/zh-cn/3.13/library/winreg.html#access-rights)。

​	返回结果为一个新句柄，指向指定的注册表键。

​	如果调用失败，则会触发 [`OSError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#OSError) 。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.OpenKey` 并附带参数 `key`, `sub_key`, `access`。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.OpenKey/result` 并附带参数 `key`。

*在 3.2 版本发生变更:* 允许使用命名参数。

*在 3.3 版本发生变更:* 参考 [上文](https://docs.python.org/zh-cn/3.13/library/winreg.html#exception-changed)。

## winreg.**QueryInfoKey**(*key*)

​	以元组形式返回某注册表键的信息。

*key* 为某个已经打开的键，或者预定义的 [HKEY_* 常量](https://docs.python.org/zh-cn/3.13/library/winreg.html#hkey-constants) 之一。

​	结果为3元素的元组。

| 索引 | 含意                                                         |
| :--- | :----------------------------------------------------------- |
| `0`  | 整数值，给出了此注册表键的子键数量。                         |
| `1`  | 整数值，给出了此注册表键的值的数量。                         |
| `2`  | 整数值，给出了此注册表键的最后修改时间，单位为自 1601 年 1 月 1 日以来的 100 纳秒。 |

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.QueryInfoKey` 并附带参数 `key`。

## winreg.**QueryValue**(*key*, *sub_key*)

​	读取某键的未命名值，形式为字符串。

*key* 为某个已经打开的键，或者预定义的 [HKEY_* 常量](https://docs.python.org/zh-cn/3.13/library/winreg.html#hkey-constants) 之一。

*sub_key* 是个字符串，用于保存与某个值相关的子键名称。如果本参数为 `None` 或空，函数将读取由 [`SetValue()`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.SetValue) 方法为 *key* 键设置的值。

​	注册表中的值包含名称、类型和数据。本方法将读取注册表键值的第一个名称为 `NULL` 的数据。可是底层的 API 调用不会返回类型，所以只要有可能就一定要使用 [`QueryValueEx()`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.QueryValueEx)。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.QueryValue` 并附带参数 `key`, `sub_key`, `value_name`。

## winreg.**QueryValueEx**(*key*, *value_name*)

​	读取已打开注册表键指定值名称的类型和数据。

*key* 为某个已经打开的键，或者预定义的 [HKEY_* 常量](https://docs.python.org/zh-cn/3.13/library/winreg.html#hkey-constants) 之一。

*value_name* 是字符串，表示要查询的值。

​	结果为二元组：

| 索引 | 含意                                                         |
| :--- | :----------------------------------------------------------- |
| `0`  | 注册表项的值。                                               |
| `1`  | 整数值，给出该值的注册表类型（请查看文档中的表格了解 [`SetValueEx()`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.SetValueEx) ）。 |

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.QueryValue` 并附带参数 `key`, `sub_key`, `value_name`。

## winreg.**SaveKey**(*key*, *file_name*)

​	将指定注册表键及其所有子键存入指定的文件。

*key* 为某个已经打开的键，或者预定义的 [HKEY_* 常量](https://docs.python.org/zh-cn/3.13/library/winreg.html#hkey-constants) 之一。

*file_name* 是要保存注册表数据的文件名。该文件不能已存在。如果文件名包括扩展名，也不能在文件分配表（FAT）文件系统中用于 [`LoadKey()`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.LoadKey) 方法。

​	如果 *key* 是代表远程计算机上的注册表键，那么 *file_name* 所描述的路径就是相对于远程计算机的。 本方法的调用方必须拥有 **SeBackupPrivilege** 安全特权。 请注意特权与权限是不同的 -- 更多细节请参阅 [Conflicts Between User Rights and Permissions 文档](https://msdn.microsoft.com/en-us/library/ms724878(v=VS.85).aspx)。

​	本函数将 `NULL` 传给 API 的 *security_attributes*。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.SaveKey` 并附带参数 `key`, `file_name`。

## winreg.**SetValue**(*key*, *sub_key*, *type*, *value*)

​	将值与指定的注册表键关联。

*key* 为某个已经打开的键，或者预定义的 [HKEY_* 常量](https://docs.python.org/zh-cn/3.13/library/winreg.html#hkey-constants) 之一。

*sub_key* 是个字符串，用于命名与该值相关的子键。

*type* 是个整数，用于指定数据的类型。目前这必须是 [`REG_SZ`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.REG_SZ) ，意味着只支持字符串。请用 [`SetValueEx()`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.SetValueEx) 函数支持其他的数据类型。

*value* 是设置新值的字符串。

​	如果 *sub_key* 参数指定的注册表键不存在，SetValue 函数会创建一个。

​	值的长度受到可用内存的限制。较长的值（超过 2048 字节）应存为文件，并将文件名存入配置注册表。这有助于提高注册表的使用效率。

​	由 *key* 参数标识的注册表键，必须已用 [`KEY_SET_VALUE`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.KEY_SET_VALUE) 方式打开。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.SetValue` 并附带参数 `key`, `sub_key`, `type`, `value`。

## winreg.**SetValueEx**(*key*, *value_name*, *reserved*, *type*, *value*)

​	将数据存入已打开的注册表键的值中。

*key* 为某个已经打开的键，或者预定义的 [HKEY_* 常量](https://docs.python.org/zh-cn/3.13/library/winreg.html#hkey-constants) 之一。

*value_name* 是个字符串，用于命名与值相关的子键。

*reserved* 可以是任意数据 —— 传给 API 的总是 0。

*type* 是个整数，用于指定数据的类型。请参阅 [Value Types](https://docs.python.org/zh-cn/3.13/library/winreg.html#value-types) 了解可用的类型。

*value* 是设置新值的字符串。

​	本方法也可为指定的注册表键设置额外的值和类型信息。注册表键必须已用 [`KEY_SET_VALUE`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.KEY_SET_VALUE) 方式打开。

​	请用 [`CreateKey()`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.CreateKey) 或 [`OpenKey()`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.OpenKey) 方法打开注册表键。

​	值的长度受到可用内存的限制。较长的值（超过 2048 字节）应存为文件，并将文件名存入配置注册表。这有助于提高注册表的使用效率。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.SetValue` 并附带参数 `key`, `sub_key`, `type`, `value`。

## winreg.**DisableReflectionKey**(*key*)

​	禁用运行于 64 位操作系统的 32 位进程的注册表重定向。

*key* 为某个已经打开的键，或者预定义的 [HKEY_* 常量](https://docs.python.org/zh-cn/3.13/library/winreg.html#hkey-constants) 之一。

​	如果在 32 位操作系统上执行，一般会触发 [`NotImplementedError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#NotImplementedError)。

​	如果注册表键不在重定向列表中，函数会调用成功，但没有实际效果。禁用注册表键的重定向不会影响任何子键的重定向。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.DisableReflectionKey` 并附带参数 `key`。

## winreg.**EnableReflectionKey**(*key*)

​	恢复已禁用注册表键的重定向。

*key* 为某个已经打开的键，或者预定义的 [HKEY_* 常量](https://docs.python.org/zh-cn/3.13/library/winreg.html#hkey-constants) 之一。

​	如果在 32 位操作系统上执行，一般会触发 [`NotImplementedError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#NotImplementedError)。

​	恢复注册表键的重定向不会影响任何子键的重定向。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.EnableReflectionKey` 并附带参数 `key`。

## winreg.**QueryReflectionKey**(*key*)

​	确定给定注册表键的重定向状况。

*key* 为某个已经打开的键，或者预定义的 [HKEY_* 常量](https://docs.python.org/zh-cn/3.13/library/winreg.html#hkey-constants) 之一。

​	如果重定向已禁用则返回 `True`。

​	如果在 32 位操作系统上执行，一般会触发 [`NotImplementedError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#NotImplementedError)。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.QueryReflectionKey` 并附带参数 `key`。



## 常量

​	以下常量被定义以供多个 [`winreg`](https://docs.python.org/zh-cn/3.13/library/winreg.html#module-winreg) 函数使用。



### HKEY_* 常量

## winreg.**HKEY_CLASSES_ROOT**

​	本注册表键下的注册表项定义了文件的类型（或类别）及相关属性。Shell 和 COM 应用程序将使用该注册表键下保存的信息。

## winreg.**HKEY_CURRENT_USER**

​	属于该注册表键的表项定义了当前用户的偏好。这些偏好值包括环境变量设置、程序组数据、颜色、打印机、网络连接和应用程序参数。

## winreg.**HKEY_LOCAL_MACHINE**

​	属于该注册表键的表项定义了计算机的物理状态，包括总线类型、系统内存和已安装软硬件等数据。

## winreg.**HKEY_USERS**

​	属于该注册表键的表项定义了当前计算机中新用户的默认配置和当前用户配置。

## winreg.**HKEY_PERFORMANCE_DATA**

​	属于该注册表键的表项可用于读取性能数据。这些数据其实并不存放于注册表中；注册表提供功能让系统收集数据。

## winreg.**HKEY_CURRENT_CONFIG**

​	包含有关本地计算机系统当前硬件配置的信息。

## winreg.**HKEY_DYN_DATA**

​	Windows 98 以上版本不使用该注册表键。



### 访问权限

​	更多信息，请参阅 [注册表密钥安全和访问](https://msdn.microsoft.com/en-us/library/ms724878(v=VS.85).aspx)。

## winreg.**KEY_ALL_ACCESS**

​	组合了 STANDARD_RIGHTS_REQUIRED 、[`KEY_QUERY_VALUE`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.KEY_QUERY_VALUE) 、 [`KEY_SET_VALUE`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.KEY_SET_VALUE) 、 [`KEY_CREATE_SUB_KEY`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.KEY_CREATE_SUB_KEY) 、 [`KEY_ENUMERATE_SUB_KEYS`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.KEY_ENUMERATE_SUB_KEYS) 、 [`KEY_NOTIFY`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.KEY_NOTIFY) 和 [`KEY_CREATE_LINK`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.KEY_CREATE_LINK) 访问权限。

## winreg.**KEY_WRITE**

​	组合了 STANDARD_RIGHTS_WRITE 、 [`KEY_SET_VALUE`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.KEY_SET_VALUE) 和 [`KEY_CREATE_SUB_KEY`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.KEY_CREATE_SUB_KEY) 访问权限。

## winreg.**KEY_READ**

​	组合了 STANDARD_RIGHTS_READ 、 [`KEY_QUERY_VALUE`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.KEY_QUERY_VALUE) 、 [`KEY_ENUMERATE_SUB_KEYS`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.KEY_ENUMERATE_SUB_KEYS) 和 [`KEY_NOTIFY`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.KEY_NOTIFY) 。

## winreg.**KEY_EXECUTE**

​	等价于 [`KEY_READ`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.KEY_READ)。

## winreg.**KEY_QUERY_VALUE**

​	查询注册表键值时需要用到。

## winreg.**KEY_SET_VALUE**

​	创建、删除或设置注册表值时需要用到。

## winreg.**KEY_CREATE_SUB_KEY**

​	创建注册表键的子键时需要用到。

## winreg.**KEY_ENUMERATE_SUB_KEYS**

​	枚举注册表键的子键时需要用到。

## winreg.**KEY_NOTIFY**

​	为注册表键或子键请求修改通知时需要用到。

## winreg.**KEY_CREATE_LINK**

​	保留给系统使用。



#### 64 位系统特有

​	详情请参阅 [Accessing an Alternate Registry View](https://msdn.microsoft.com/en-us/library/aa384129(v=VS.85).aspx)。

## winreg.**KEY_WOW64_64KEY**

​	表示一个应用程序在 64 位 Windows 上应当在 64 位的注册表视图上进行操作。 在 32 位 Windows 上，此常量会被忽略。

## winreg.**KEY_WOW64_32KEY**

​	表示一个应用程序在 64 位 Windows 上应当在 32 位的注册表视图上进行操作。 在 32 位 Windows 上，此常量会被忽略。



### 注册表值的类型

​	详情请参阅 [Registry Value Types](https://msdn.microsoft.com/en-us/library/ms724884(v=VS.85).aspx)。

## winreg.**REG_BINARY**

​	任意格式的二进制数据。

## winreg.**REG_DWORD**

​	32 位数字。

## winreg.**REG_DWORD_LITTLE_ENDIAN**

​	32 位低字节序格式的数字。相当于 [`REG_DWORD`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.REG_DWORD)。

## winreg.**REG_DWORD_BIG_ENDIAN**

​	32 位高字节序格式的数字。

## winreg.**REG_EXPAND_SZ**

​	包含环境变量（`%PATH%`）的字符串，以空字符结尾。

## winreg.**REG_LINK**

​	Unicode 符号链接。

## winreg.**REG_MULTI_SZ**

​	一串以空字符结尾的字符串，最后以两个空字符结尾。Python 会自动处理这种结尾形式。

## winreg.**REG_NONE**

​	未定义的类型。

## winreg.**REG_QWORD**

​	64 位数字。

*Added in version 3.6.*

## winreg.**REG_QWORD_LITTLE_ENDIAN**

​	64 位低字节序格式的数字。相当于 [`REG_QWORD`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.REG_QWORD)。

*Added in version 3.6.*

## winreg.**REG_RESOURCE_LIST**

​	设备驱动程序资源列表。

## winreg.**REG_FULL_RESOURCE_DESCRIPTOR**

​	硬件设置。

## winreg.**REG_RESOURCE_REQUIREMENTS_LIST**

​	硬件资源列表。

## winreg.**REG_SZ**

​	空字符结尾的字符串。



## 注册表句柄对象

​	该对象封装了 Windows HKEY 对象，对象销毁时会自动关闭。为确保资源得以清理，可调用 [`Close()`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.PyHKEY.Close) 方法或 [`CloseKey()`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.CloseKey) 函数。

​	本模块中的所有注册表函数都会返回注册表句柄对象。

​	本模块中所有接受注册表句柄对象的注册表函数，也能接受一个整数，但鼓励大家使用句柄对象。

​	句柄对象为 [`__bool__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__bool__) 提供语义 —— 因此

```
if handle:
    print("Yes")
```

​	将会打印出 `Yes` 。

​	句柄对象还支持比较语义，因此若多个句柄对象都引用了同一底层 Windows 句柄值，那么比较操作结果将为 True。

​	句柄对象可转换为整数（如利用内置函数 [`int()`](https://docs.python.org/zh-cn/3.13/library/functions.html#int)），这时会返回底层的 Windows 句柄值。用 [`Detach()`](https://docs.python.org/zh-cn/3.13/library/winreg.html#winreg.PyHKEY.Detach) 方法也可返回整数句柄，同时会断开与 Windows 句柄的连接。

## PyHKEY.**Close**()

​	关闭底层的 Windows 句柄。

​	如果句柄已关闭，不会引发错误。

## PyHKEY.**Detach**()

​	断开与 Windows 句柄的连接。

​	结果为一个整数，存有被断开连接之前的句柄值。如果该句柄已断开连接或关闭，则返回 0。

​	调用本函数后，注册表句柄将被迅速禁用，但并没有关闭。当需要底层的 Win32 句柄在句柄对象的生命周期之后仍然存在时，可以调用这个函数。

​	引发一个 [审计事件](https://docs.python.org/zh-cn/3.13/library/sys.html#auditing) `winreg.PyHKEY.Detach` 并附带参数 `key`。

## PyHKEY.**__enter__**()

## PyHKEY.**__exit__**(**exc_info*)

​	HKEY 对象实现了 [`__enter__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__enter__) 和 [`__exit__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__exit__) 方法，因此支持 [`with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#with) 语句的上下文协议：

```
with OpenKey(HKEY_LOCAL_MACHINE, "foo") as key:
    ...  # 使用 key 进行操作
```

​	在离开 [`with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#with) 语句块时，*key* 会自动关闭。
