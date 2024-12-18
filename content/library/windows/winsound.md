+++
title = "winsound --- 针对 Windows 的声音播放接口"
date = 2024-11-15T21:28:55+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/winsound.html](https://docs.python.org/zh-cn/3.13/library/winsound.html)
>
> 收录该文档的时间：`2024-11-15T21:28:55+08:00`

# `winsound` --- 针对 Windows 的声音播放接口

------

​	通过 [`winsound`]({{< ref "/library/windows/winsound#module-winsound" >}}) 模块可访问 Windows 平台的基础音频播放机制。包括一些函数和几个常量。

## winsound.**Beep**(*frequency*, *duration*)

​	让 PC 的扬声器发出提示音。*frequency* 参数可指定声音的频率，单位是赫兹，必须位于 37 到 32,767 之间。*duration* 参数则指定了声音应持续的毫秒数。若系统无法让扬声器发声，则会触发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。

## winsound.**PlaySound**(*sound*, *flags*)

​	由平台 API 调用底层的 `PlaySound()` 函数。 *sound* 形参可以是一个文件名、系统声音别名、[bytes-like object]({{< ref "/glossary/idx#term-bytes-like-object" >}}) 形式的音频数据或者 `None`。 对它的解读取决于 *flags* 的值，它可以为下述常量通过按位或运算得到的组合。 如果 *sound* 形参为 `None`，则将停止当前播放的任何波形声音。 如果系统提示错误，则会引发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。

## winsound.**MessageBeep**(*type=MB_OK*)

​	由平台 API 调用底层的 `MessageBeep()` 函数。 这将播放在注册表中指定的声音。 *type* 参数指定要播放的声音；可能的值为 `-1`, `MB_ICONASTERISK`, `MB_ICONEXCLAMATION`, `MB_ICONHAND`, `MB_ICONQUESTION` 和 `MB_OK`，如下文所述。 值为 `-1` 将产生一个简单的“哔”；这是在无法播放其他声音时的最终回退项。 如果系统提示错误，则会引发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。

## winsound.**SND_FILENAME**

​	参数 *sound* 指明 WAV 文件名。不要与 [`SND_ALIAS`]({{< ref "/library/windows/winsound#winsound.SND_ALIAS" >}}) 一起使用。

## winsound.**SND_ALIAS**

​	参数 *sound* 是注册表内关联的音频名称。 如果注册表中无此名称，则播放系统默认的声音，除非同时设定了 [`SND_NODEFAULT`]({{< ref "/library/windows/winsound#winsound.SND_NODEFAULT" >}}) 。 如果没有注册默认声音，则会触发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。 请勿与 [`SND_FILENAME`]({{< ref "/library/windows/winsound#winsound.SND_FILENAME" >}}) 一起使用。

​	所有的 Win32 系统至少支持以下音频名称；大多数系统支持的音频都多于这些：

| [`PlaySound()`]({{< ref "/library/windows/winsound#winsound.PlaySound" >}}) *name* 参数 | 对应的控制面板音频名 |
| :----------------------------------------------------------- | :------------------- |
| `'SystemAsterisk'`                                           | 星号                 |
| `'SystemExclamation'`                                        | 感叹号               |
| `'SystemExit'`                                               | 退出 Windows         |
| `'SystemHand'`                                               | 关键性停止           |
| `'SystemQuestion'`                                           | 问题                 |

​	例如：

```
import winsound
# 播放 Windows 退出音效。
winsound.PlaySound("SystemExit", winsound.SND_ALIAS)

# 可能会播放 Windows 默认音效，如果有注册的话
# （因为 "*" 可能不是任何音效的注册名称）。
winsound.PlaySound("*", winsound.SND_ALIAS)
```

## winsound.**SND_LOOP**

​	循环播放音频。为避免阻塞，必须同时使用 [`SND_ASYNC`]({{< ref "/library/windows/winsound#winsound.SND_ASYNC" >}}) 标志。不能与 [`SND_MEMORY`]({{< ref "/library/windows/winsound#winsound.SND_MEMORY" >}}) 一起使用。

## winsound.**SND_MEMORY**

[`PlaySound()`]({{< ref "/library/windows/winsound#winsound.PlaySound" >}}) 的 *sound* 形参是一个 WAV 文件的内存镜像，作为一个 [bytes-like object]({{< ref "/glossary/idx#term-bytes-like-object" >}})。

​备注
 

​	本模块不支持异步播放音频的内存镜像，所以该标志和 [`SND_ASYNC`]({{< ref "/library/windows/winsound#winsound.SND_ASYNC" >}}) 的组合将触发 [`RuntimeError`]({{< ref "/library/exceptions#RuntimeError" >}})。

## winsound.**SND_PURGE**

​	停止播放指定音频的所有实例。

​备注
 

​	新版 Windows 平台不支持本标志。

## winsound.**SND_ASYNC**

​	立即返回，允许异步播放音频。

## winsound.**SND_NODEFAULT**

​	即便找不到指定的音频，也不播放系统默认音频。

## winsound.**SND_NOSTOP**

​	不打断正在播放的音频。

## winsound.**SND_NOWAIT**

​	如果音频驱动程序忙，则立即返回。

​备注
 

​	新版 Windows 平台不支持本标志。

## winsound.**MB_ICONASTERISK**

​	播放 `SystemDefault` 音频。

## winsound.**MB_ICONEXCLAMATION**

​	播放 `SystemExclamation` 音频。

## winsound.**MB_ICONHAND**

​	播放 `SystemHand` 音频。

## winsound.**MB_ICONQUESTION**

​	播放 `SystemQuestion` 音频。

## winsound.**MB_OK**

​	播放 `SystemDefault` 音频。
