+++
title = "wave --- 读写 WAV 文件"
date = 2024-11-15T20:46:14+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/wave.html](https://docs.python.org/zh-cn/3.13/library/wave.html)
>
> 收录该文档的时间：`2024-11-15T20:46:14+08:00`

# `wave` --- 读写 WAV 文件

**源代码:** [Lib/wave.py](https://github.com/python/cpython/tree/3.13/Lib/wave.py)

------

[`wave`](https://docs.python.org/zh-cn/3.13/library/wave.html#module-wave) 模块提供了一个处理 Waveform Audio "WAVE" (或称 "WAV") 文件格式的便利接口。 仅支持未压缩的 PCM 编码波形文件。

*在 3.12 版本发生变更:* 增加了对 `WAVE_FORMAT_EXTENSIBLE` 标头的支持，要求扩展格式为 `KSDATAFORMAT_SUBTYPE_PCM`。

[`wave`](https://docs.python.org/zh-cn/3.13/library/wave.html#module-wave) 模块定义了以下函数和异常:

## wave.**open**(*file*, *mode=None*)

​	如果 *file* 是一个字符串，打开对应文件名的文件。否则就把它作为文件型对象来处理。*mode* 可以为以下值：

## `'rb'`

​	只读模式。

## `'wb'`

​	只写模式。

​	注意不支持同时读写WAV文件。

*mode* 设为 `'rb'` 时返回一个 [`Wave_read`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_read) 对象，而 *mode* 设为 `'wb'` 时返回一个 [`Wave_write`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_write) 对象。如果省略 *mode* 并指定 *file* 来传入一个文件型对象，则 `file.mode` 会被用作 *mode* 的默认值。

​	如果你传入一个文件型对象，当调用 wave 对象的 `close()` 方法时并不会真正关闭它；调用者需要负责关闭文件对象。

[`open()`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.open) 函数可以在 [`with`](https://docs.python.org/zh-cn/3.13/reference/compound_stmts.html#with) 语句中使用。 当 `with` 代码块结束时，[`Wave_read.close()`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_read.close) 或 [`Wave_write.close()`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_write.close) 方法会被调用。

*在 3.4 版本发生变更:* 添加了对不可搜索文件的支持。

## *exception* wave.**Error**

​	当不符合WAV格式或无法操作时引发的错误。



## Wave_read对象

## *class* wave.**Wave_read**

​	读取一个 WAV 文件。

​	由 [`open()`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.open) 返回的 Wave_read 对象，有以下几种方法:

## **close**()

​	关闭 [`wave`](https://docs.python.org/zh-cn/3.13/library/wave.html#module-wave) 打开的数据流并使对象不可用。当对象销毁时会自动调用。

## **getnchannels**()

​	返回声道数量（`1` 为单声道，`2` 为立体声）

## **getsampwidth**()

​	返回采样字节长度。

## **getframerate**()

​	返回采样频率。

## **getnframes**()

​	返回音频总帧数。

## **getcomptype**()

​	返回压缩类型（只支持 `'NONE'` 类型）

## **getcompname**()

[`getcomptype()`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_read.getcomptype) 的通俗版本。使用 `'not compressed'` 代替 `'NONE'`。

## **getparams**()

​	返回一个 [`namedtuple()`](https://docs.python.org/zh-cn/3.13/library/collections.html#collections.namedtuple) `(nchannels, sampwidth, framerate, nframes, comptype, compname)`，等价于 `get*()` 方法的输出。

## **readframes**(*n*)

​	读取并返回以 [`bytes`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#bytes) 对象表示的最多 *n* 帧音频。

## **rewind**()

​	重置文件指针至音频开头.

​	以下两个方法是为了和旧的 `aifc` 模块保持兼容而定义的，实际上不会做任何事情。

## **getmarkers**()

​	返回 `None`。

*Deprecated since version 3.13, will be removed in version 3.15:* 此方法只是为了和已在 Python 3.13 中被移除的 `aifc` 模块保持兼容而存在的。

## **getmark**(*id*)

​	引发错误异常。

*Deprecated since version 3.13, will be removed in version 3.15:* 此方法只是为了和已在 Python 3.13 中被移除的 `aifc` 模块保持兼容而存在的。

​	以下两个方法都使用指针，具体实现由其底层决定。

## **setpos**(*pos*)

​	设置文件指针到指定位置。

## **tell**()

​	返回当前文件指针位置。



## Wave_write 对象

## *class* wave.**Wave_write**

​	写入一个 WAV 文件。

​	Wave_write 对象，由 [`open()`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.open) 返回。

​	对于可查找的输出流，`wave` 头将自动更新以反映实际写入的帧数。 对于不可查找的流，当写入第一帧时 *nframes* 值必须是准确的。 要获取准确的 *nframes* 值可以通过调用 [`setnframes()`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_write.setnframes) 或 [`setparams()`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_write.setparams) 并附带 [`close()`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_write.close) 被调用之前将要写入的帧数然后使用 [`writeframesraw()`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_write.writeframesraw) 来写入帧数据，或者通过调用 [`writeframes()`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_write.writeframes) 并附带所有要写入的帧。 在后一种情况下 [`writeframes()`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_write.writeframes) 将计算数据中的帧数并在写入帧数据之前相应地设置 *nframes*。

*在 3.4 版本发生变更:* 添加了对不可搜索文件的支持。

​	Wave_write 对象具有以下方法:

## **close**()

​	确保 *nframes* 是正确的，并在文件被 [`wave`](https://docs.python.org/zh-cn/3.13/library/wave.html#module-wave) 打开时关闭它。 此方法会在对象收集时被调用。 如果输出流不可查找且 *nframes* 与实际写入的帧数不匹配时引发异常。

## **setnchannels**(*n*)

​	设置声道数。

## **setsampwidth**(*n*)

​	设置采样字节长度为 *n*。

## **setframerate**(*n*)

​	设置采样频率为 *n*。

*在 3.2 版本发生变更:* 对此方法的非整数输入会被舍入到最接近的整数。

## **setnframes**(*n*)

​	设置总帧数为 *n*。 如果与之后实际写入的帧数不一致此值将会被更改（ 如果输出流不可查找则此更改尝试将引发错误）。

## **setcomptype**(*type*, *name*)

​	设置压缩格式。目前只支持 `NONE` 即无压缩格式。

## **setparams**(*tuple*)

*tuple* 应该是 `(nchannels, sampwidth, framerate, nframes, comptype, compname)`，每项的值可用于 `set*()` 方法。 设置所有形参。

## **tell**()

​	返回当前文件指针，其指针含义和 [`Wave_read.tell()`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_read.tell) 以及 [`Wave_read.setpos()`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_read.setpos) 是一致的。

## **writeframesraw**(*data*)

​	写入音频数据但不更新 *nframes*。

*在 3.4 版本发生变更:* 现在可接受任意 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object)。

## **writeframes**(*data*)

​	写入音频帧并确保 *nframes* 是正确的。 如果输出流不可查找且在 *data* 被写入之后写入的总帧数与之前设定的 *nframes* 值不匹配将会引发错误。

*在 3.4 版本发生变更:* 现在可接受任意 [bytes-like object](https://docs.python.org/zh-cn/3.13/glossary.html#term-bytes-like-object)。

​	注意在调用 [`writeframes()`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_write.writeframes) 或 [`writeframesraw()`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Wave_write.writeframesraw) 之后再设置任何格式参数是无效的，而且任何这样的尝试将引发 [`wave.Error`](https://docs.python.org/zh-cn/3.13/library/wave.html#wave.Error)。
