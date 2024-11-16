+++
title = "pwd --- 密码数据库"
date = 2024-11-15T21:30:54+08:00
weight = 10
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/pwd.html](https://docs.python.org/zh-cn/3.13/library/pwd.html)
>
> 收录该文档的时间：`2024-11-15T21:30:54+08:00`

# `pwd` --- 密码数据库

------

​	此模块可以访问 Unix 用户账户名及密码数据库，在所有 Unix 版本上均可使用。

[Availability]({{< ref "/library/intro#availability" >}}): Unix, not WASI, not iOS.

​	密码数据库中的条目以元组对象返回，属性对应 `passwd` 中的结构（属性如下所示，可参考 `<pwd.h>`）：

| 索引 | 属性        | 含意               |
| :--- | :---------- | :----------------- |
| 0    | `pw_name`   | 登录名             |
| 1    | `pw_passwd` | 密码，可能已经加密 |
| 2    | `pw_uid`    | 用户 ID 数值       |
| 3    | `pw_gid`    | 组 ID 数值         |
| 4    | `pw_gecos`  | 用户名或备注       |
| 5    | `pw_dir`    | 用户主目录         |
| 6    | `pw_shell`  | 用户的命令解释器   |

​	其中 uid 和 gid 是整数，其他是字符串，如果找不到对应的项目，抛出 [`KeyError`]({{< ref "/library/exceptions#KeyError" >}}) 异常。

​	备注

 

​	在传统的 Unix 中字段 `pw_passwd` 通常包含一个使用 DES 派生算法加密的口令。 不过大多数现代 Unix 使用一种所谓的 *影子口令* 系统。 在这些 Unix 上 *pw_passwd* 字段只包含一个星号 (`'*'`) 或字母 `'x'` 而加密的口令存储在非全局可读的文件 `/etc/shadow` 中。 而 *pw_passwd* 字段是否包含任何有用内容则取决于具体的系统。

​	本模块定义如下内容：

## pwd.**getpwuid**(*uid*)

​	给定用户的数值 ID，返回密码数据库的对应项目。

## pwd.**getpwnam**(*name*)

​	给定用户名，返回密码数据库的对应项目。

## pwd.**getpwall**()

​	返回密码数据库中所有项目的列表，顺序不是固定的。

> 参见
>
> 模块 [`grp`]({{< ref "/library/unix/grp#module-grp" >}})
>
> ​	针对用户组数据库的接口，与本模块类似。
