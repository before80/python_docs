+++
title = "zlib --- 与 gzip 兼容的压缩"
date = 2024-11-15T12:00:20+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/zlib.html](https://docs.python.org/zh-cn/3.13/library/zlib.html)
>
> 收录该文档的时间：`2024-11-15T12:00:20+08:00`

# `zlib` --- 与 **gzip** 兼容的压缩

------

​	对于需要数据压缩的应用，此模块中的函数允许使用 zlib 库进行压缩和解压缩。 zlib 库的项目主页是 [https://www.zlib.net](https://www.zlib.net/)。 已知此 Python 模块与 1.1.3 之前版本的 zlib 库存在不兼容；1.1.3 版则存在一个 [安全缺陷](https://zlib.net/zlib_faq.html#faq33)，因此我们推荐使用 1.1.4 或更新的版本。

​	zlib 的函数有很多选项，一般需要按特定顺序使用。本文档没有覆盖全部的用法。更多详细信息请于 http://www.zlib.net/manual.html 参阅官方手册。

​	要读写 `.gz` 格式的文件，请参考 [`gzip`]({{< ref "/library/archiving/gzip#module-gzip" >}}) 模块。

​	此模块中可用的异常和函数如下：

## *exception* zlib.**error**

​	在压缩或解压缩过程中发生错误时的异常。

## zlib.**adler32**(*data*[, *value*])

​	计算 *data* 的 Adler-32 校验值。(Adler-32 校验的可靠性与 CRC32 基本相当，但比计算 CRC32 更高效。) 计算的结果是一个 32 位的整数。参数 *value* 是校验时的起始值，其默认值为 1。借助参数 *value* 可为分段的输入计算校验值。此算法没有加密强度，不应用于身份验证和数字签名。此算法的目的仅为验证数据的正确性，不适合作为通用散列算法。

> 在 3.0 版本发生变更: 结果将总是不带符号的。

## zlib.**compress**(*data*, */*, *level=-1*, *wbits=MAX_WBITS*)

​	压缩 *data* 中的字节，返回包含已压缩数据的字节串对象。 *level* 是一个用于控制压缩级别的 `0` 到 `9` 之间的整数或 `-1`；`1` (Z_BEST_SPEED) 表示最快速度和最低压缩率，`9` (Z_BEST_COMPRESSION) 表示最慢速度和最高压缩率。 `0` (Z_NO_COMPRESSION) 表示不压缩。 默认值为 `-1` (Z_DEFAULT_COMPRESSION)。 Z_DEFAULT_COMPRESSION 表示速度和压缩率折中的默认值 (目前相当于级别 6)。

​	参数 *wbits* 指定压缩数据时所使用的历史缓冲区的大小 (窗口大小)，并指定压缩输出是否包含头部或尾部。参数的默认值是 `15` (MAX_WBITS)。参数的值分为几个范围：

- +9 至 +15：窗口大小以二为底的对数。 即这些值对应着 512 至 32768 的窗口大小。 更大的值会提供更好的压缩，同时内存开销也会更大。 压缩输出会包含 zlib 特定格式的头部和尾部。
- −9 至 −15：绝对值为窗口大小以二为底的对数。 压缩输出仅包含压缩数据，没有头部和尾部。
- +25 至 +31 = 16 + (9 至 15)：后 4 个比特位为窗口大小以二为底的对数。 压缩输出包含一个基本的 **gzip** 头部，并以校验和为尾部。

​	如果发生任何错误则将引发 [`error`]({{< ref "/library/archiving/zlib#zlib.error" >}}) 异常。

> 在 3.6 版本发生变更: 现在，*level* 可作为关键字参数。

> 在 3.11 版本发生变更: 现在可以用 *wbits* 形参来设置窗口位和压缩类型。

## zlib.**compressobj**(*level=-1*, *method=DEFLATED*, *wbits=MAX_WBITS*, *memLevel=DEF_MEM_LEVEL*, *strategy=Z_DEFAULT_STRATEGY*[, *zdict*])

​	返回一个 压缩对象，用来压缩内存中难以容下的数据流。

​	参数 *level* 为压缩等级，是整数，可取值为 `0` 到 `9` 或 `-1`。`1` (Z_BEST_SPEED) 表示最快速度和最低压缩率，`9` (Z_BEST_COMPRESSION) 表示最慢速度和最高压缩率。`0` (Z_NO_COMPRESSION) 表示不压缩。参数默认值为 `-1` (Z_DEFAULT_COMPRESSION)。Z_DEFAULT_COMPRESSION 是速度和压缩率之间的平衡 (一般相当于设压缩等级为 6)。

*method* 表示压缩算法。现在只支持 `DEFLATED` 这个算法。

*wbits* 形参控制历史缓冲区的大小（或称“窗口大小”），以及将要使用的头部和尾部格式。 它的含义与 [对 compress() 的描述]({{< ref "/library/archiving/zlib#compress-wbits" >}}) 相同。

​	参数 *memLevel* 指定内部压缩操作时所占用内存大小。参数取 `1` 到 `9`。更大的值占用更多的内存，同时速度也更快输出也更小。

​	参数 *strategy* 用于调节压缩算法。可取值为 `Z_DEFAULT_STRATEGY`、`Z_FILTERED`、`Z_HUFFMAN_ONLY`、`Z_RLE` (zlib 1.2.0.1) 或 `Z_FIXED` (zlib 1.2.2.2)。

​	参数 *zdict* 指定预定义的压缩字典。它是一个字节序列 (如 [`bytes`]({{< ref "/library/stdtypes#bytes" >}}) 对象)，其中包含用户认为要压缩的数据中可能频繁出现的子序列。频率高的子序列应当放在字典的尾部。

> 在 3.3 版本发生变更: 添加关键字参数 *zdict*。

## zlib.**crc32**(*data*[, *value*])

​	计算 *data* 的 CRC (循环冗余校验) 值。计算的结果是一个 32 位的整数。参数 *value* 是校验时的起始值，其默认值为 0。借助参数 *value* 可为分段的输入计算校验值。此算法没有加密强度，不应用于身份验证和数字签名。此算法的目的仅为验证数据的正确性，不适合作为通用散列算法。

> 在 3.0 版本发生变更: 结果将总是不带符号的。

## zlib.**decompress**(*data*, */*, *wbits=MAX_WBITS*, *bufsize=DEF_BUF_SIZE*)

​	解压 *data* 中的字节，返回含有已解压内容的 bytes 对象。参数 *wbits* 取决于 *data* 的格式，具体参见下边的说明。*bufsize* 为输出缓冲区的起始大小。函数发生错误时抛出 [`error`]({{< ref "/library/archiving/zlib#zlib.error" >}}) 异常。

*wbits* 形参控制历史缓冲区的大小（或称“窗口大小”）以及所期望的头部和尾部格式。 它类似于 [`compressobj()`]({{< ref "/library/archiving/zlib#zlib.compressobj" >}}) 的形参，但可接受更大范围的值：

- +8 至 +15：窗口尺寸以二为底的对数。 输入必须包含 zlib 头部和尾部。
- 0：根据 zlib 头部自动确定窗口大小。 只从 zlib 1.2.3.5 版起受支持。
- −8 至 −15：使用 *wbits* 的绝对值作为窗口大小以二为底的对数。 输入必须为原始数据流，没有头部和尾部。
- +24 至 +31 = 16 + (8 至 15)：使用后 4 个比特位作为窗口大小以二为底的对数。 输入必须包括 gzip 头部和尾部。
- +40 至 +47 = 32 + (8 至 15)：使用后 4 个比特位作为窗口大小以二为底的对数，并且自动接受 zlib 或 gzip 格式。

​	当解压缩一个数据流时，窗口大小必须不小于用于压缩数据流的原始窗口大小；使用太小的值可能导致 [`error`]({{< ref "/library/archiving/zlib#zlib.error" >}}) 异常。 默认 *wbits* 值对应于最大的窗口大小并且要求包括 zlib 头部和尾部。

*bufsize* 是用于存放解压数据的缓冲区初始大小。 如果需要更大空间，缓冲区大小将按需增加，因此你不需要让这个值完全精确；对其进行调整仅会节省一点对 `malloc()` 的调用次数。

> 在 3.6 版本发生变更: *wbits* 和 *bufsize* 可用作关键字参数。

## zlib.**decompressobj**(*wbits=MAX_WBITS*[, *zdict*])

​	返回一个解压对象，用来解压无法被一次性放入内存的数据流。

*wbits* 形参控制历史缓冲区的大小（或称“窗口大小”）以及所期望的头部和尾部格式。 它的含义与 [对 decompress() 的描述]({{< ref "/library/archiving/zlib#decompress-wbits" >}}) 相同。

*zdict* 形参指定指定一个预定义的压缩字典。 如果提供了此形参，它必须与产生将解压数据的压缩器所使用的字典相同。

​备注
 

​	如果 *zdict* 是一个可变对象 (例如 [`bytearray`]({{< ref "/library/stdtypes#bytearray" >}}))，则你不可在对 [`decompressobj()`]({{< ref "/library/archiving/zlib#zlib.decompressobj" >}}) 的调用和对解压器的 `decompress()` 方法的调用之间修改其内容。

> 在 3.3 版本发生变更: 增加了 *zdict* 形参。

​	压缩对象支持以下方法：

## Compress.**compress**(*data*)

​	压缩 *data* 并返回 bytes 对象，这个对象含有 *data* 的部分或全部内容的已压缩数据。所得的对象必须拼接在上一次调用 [`compress()`]({{< ref "/library/archiving/zlib#zlib.compress" >}}) 方法所得数据的后面。缓冲区中可能留存部分输入以供下一次调用。

## Compress.**flush**([*mode*])

​	压缩所有缓冲区的数据并返回已压缩的数据。参数 *mode* 可以传入的常量为：`Z_NO_FLUSH`、`Z_PARTIAL_FLUSH`、`Z_SYNC_FLUSH`、`Z_FULL_FLUSH`、`Z_BLOCK` (zlib 1.2.3.4) 或 `Z_FINISH`。默认值为 `Z_FINISH`。`Z_FINISH` 关闭已压缩数据流并不允许再压缩其他数据，`Z_FINISH` 以外的值皆允许这个对象继续压缩数据。调用 [`flush()`]({{< ref "/library/archiving/zlib#zlib.Compress.flush" >}}) 方法并将 *mode* 设为 `Z_FINISH` 后会无法再次调用 [`compress()`]({{< ref "/library/archiving/zlib#zlib.compress" >}})，此时只能删除这个对象。

## Compress.**copy**()

​	返回此压缩对象的一个拷贝。它可以用来高效压缩一系列拥有相同前缀的数据。

> 在 3.8 版本发生变更: 添加了对压缩对象执行 [`copy.copy()`]({{< ref "/library/datatypes/copy#copy.copy" >}}) 和 [`copy.deepcopy()`]({{< ref "/library/datatypes/copy#copy.deepcopy" >}}) 的支持。

​	解压缩对象支持以下方法：

## Decompress.**unused_data**

​	一个 bytes 对象，其中包含压缩数据结束之后的任何字节数据。 也就是说，它将为 `b""` 直到包含压缩数据的末尾字节可用。 如果整个结果字节串都包含压缩数据，它将为一个空的 bytes 对象 `b""`。

## Decompress.**unconsumed_tail**

​	一个 bytes 对象，其中包含未被上一次 [`decompress()`]({{< ref "/library/archiving/zlib#zlib.decompress" >}}) 调用所消耗的任何数据。 此数据不能被 zlib 机制看到，因此你必须将其送回（可能要附带额外的数据拼接）到后续的 [`decompress()`]({{< ref "/library/archiving/zlib#zlib.decompress" >}}) 方法调用以获得正确的输出。

## Decompress.**eof**

​	一个布尔值，指明是否已到达压缩数据流的末尾。

​	这使得区分正确构造的压缩数据流和不完整或被截断的流成为可能。

> Added in version 3.3.
>

## Decompress.**decompress**(*data*, *max_length=0*)

​	解压缩 *data* 并返回 bytes 对象，其中包含对应于 *string* 中至少一部分数据的解压缩数据。 此数据应当被拼接到之前任何对 [`decompress()`]({{< ref "/library/archiving/zlib#zlib.decompress" >}}) 方法的调用所产生的输出。 部分输入数据可能会被保留在内部缓冲区以供后续处理。

​	如果可选的形参 *max_length* 非零则返回值将不会长于 *max_length*。 这可能意味着不是所有已压缩输入都能被处理；并且未被消耗的数据将被保存在 [`unconsumed_tail`]({{< ref "/library/archiving/zlib#zlib.Decompress.unconsumed_tail" >}}) 属性中。 如果要继续解压缩则这个字节串必须被传给对 [`decompress()`]({{< ref "/library/archiving/zlib#zlib.decompress" >}}) 的后续调用。 如果 *max_length* 为零则整个输入都会被解压缩，并且 [`unconsumed_tail`]({{< ref "/library/archiving/zlib#zlib.Decompress.unconsumed_tail" >}}) 将为空。

> 在 3.6 版本发生变更: *max_length* 可用作关键字参数。

## Decompress.**flush**([*length*])

​	所有挂起的输入会被处理，并且返回包含剩余未压缩输出的 bytes 对象。 在调用 [`flush()`]({{< ref "/library/archiving/zlib#zlib.Decompress.flush" >}}) 之后，[`decompress()`]({{< ref "/library/archiving/zlib#zlib.decompress" >}}) 方法将无法被再次调用；唯一可行的操作是删除该对象。

​	可选的形参 *length* 设置输出缓冲区的初始大小。

## Decompress.**copy**()

​	返回解压缩对象的一个拷贝。 它可以用来在数据流的中途保存解压缩器的状态以便加快随机查找数据流后续位置的速度。

> 在 3.8 版本发生变更: 添加了对解压缩对象执行 [`copy.copy()`]({{< ref "/library/datatypes/copy#copy.copy" >}}) 和 [`copy.deepcopy()`]({{< ref "/library/datatypes/copy#copy.deepcopy" >}}) 的支持。

​	通过下列常量可获取模块所使用的 zlib 库的版本信息：

## zlib.**ZLIB_VERSION**

​	构建此模块时所用的 zlib 库的版本字符串。它的值可能与运行时所加载的 zlib 不同。运行时加载的 zlib 库的版本字符串为 [`ZLIB_RUNTIME_VERSION`]({{< ref "/library/archiving/zlib#zlib.ZLIB_RUNTIME_VERSION" >}})。

## zlib.**ZLIB_RUNTIME_VERSION**

​	解释器所加载的 zlib 库的版本字符串。

> Added in version 3.3.
>

​参见
## 模块 [`gzip`]({{< ref "/library/archiving/gzip#module-gzip" >}})

​	读写 **gzip** 格式的文件。

## [http://www.zlib.net](http://www.zlib.net/)

​	zlib 库项目主页。

## http://www.zlib.net/manual.html

​	zlib 库用户手册。提供了库的许多功能的解释和用法。
