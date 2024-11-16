+++
title = "grp --- 组数据库"
date = 2024-11-15T21:30:54+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/grp.html](https://docs.python.org/zh-cn/3.13/library/grp.html)
>
> 收录该文档的时间：`2024-11-15T21:30:54+08:00`

# `grp` --- 组数据库

------

​	该模块提供对Unix组数据库的访问。 它在所有Unix版本上都可用。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not Android, not iOS.

​	组数据库条目被报告为类似元组的对象，其属性对应于 `group` 结构的成员 (下面的属性字段，请参见 `<grp.h>`):

| 索引 | 属性      | 含意                        |
| :--- | :-------- | :-------------------------- |
| 0    | gr_name   | 组名                        |
| 1    | gr_passwd | （加密的）组密码； 通常为空 |
| 2    | gr_gid    | 数字组ID                    |
| 3    | gr_mem    | 组内所有成员的用户名        |

​	gid 是整数，名称和密码是字符串，成员列表是字符串列表。 （注意，大多数用户未根据密码数据库显式列为所属组的成员。请检查两个数据库以获取完整的成员资格信息。还要注意，以 `+` 或 `-` 开头的 `gr_name` 可能是 YP/NIS 引用，可能无法通过 [`getgrnam()`]({{< ref "/library/unix/grp#grp.getgrnam" >}}) 或 [`getgrgid()`]({{< ref "/library/unix/grp#grp.getgrgid" >}}) 访问。）

​	本模块定义如下内容：

## grp.**getgrgid**(*id*)

​	返回给定数字组 ID 的组数据库条目。 如果请求的条目无法找到则会引发 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}})。

> 在 3.10 版本发生变更: 对于非整数参数如浮点数或字符串将引发 [`TypeError`]({{< ref "/library/exceptions#TypeError" >}})。

## grp.**getgrnam**(*name*)

​	返回给定组名的组数据库条目。 如果找不到要求的条目，则会引发 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}}) 错误。

## grp.**getgrall**()

​	以任意顺序返回所有可用组条目的列表。

> 参见
>
> 模块 [`pwd`]({{< ref "/library/unix/pwd#module-pwd" >}})
>
> ​	用户数据库的接口，与此类似。
