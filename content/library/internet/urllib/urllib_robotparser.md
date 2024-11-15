+++
title = "`urllib.robotparser` --- 用于 robots.txt 的解析器"
date = 2024-11-15T20:28:46+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/urllib.robotparser.html](https://docs.python.org/zh-cn/3.13/library/urllib.robotparser.html)
>
> 收录该文档的时间：`2024-11-15T20:28:46+08:00`

# `urllib.robotparser` --- 用于 robots.txt 的解析器

**源代码：** [Lib/urllib/robotparser.py](https://github.com/python/cpython/tree/3.13/Lib/urllib/robotparser.py)

------

​	此模块提供了一个单独的类 [`RobotFileParser`](https://docs.python.org/zh-cn/3.13/library/urllib.robotparser.html#urllib.robotparser.RobotFileParser)，它可以回答关于某个特定用户代理能否在发布了 `robots.txt` 文件的网站抓取特定 URL 的问题。 有关 `robots.txt` 文件结构的更多细节，请参阅 http://www.robotstxt.org/orig.html。

## *class* urllib.robotparser.**RobotFileParser**(*url=''*)

​	这个类提供了一些可以读取、解析和回答关于 *url* 上的 `robots.txt` 文件的问题的方法。

## **set_url**(*url*)

​	设置指向 `robots.txt` 文件的 URL。

## **read**()

​	读取 `robots.txt` URL 并将其输入解析器。

## **parse**(*lines*)

​	解析行参数。

## **can_fetch**(*useragent*, *url*)

​	如果允许 *useragent* 按照被解析 `robots.txt` 文件中的规则来获取 *url* 则返回 `True`。

## **mtime**()

​	返回最近一次获取 `robots.txt` 文件的时间。 这适用于需要定期检查 `robots.txt` 文件更新情况的长时间运行的网页爬虫。

## **modified**()

​	将最近一次获取 `robots.txt` 文件的时间设置为当前时间。

## **crawl_delay**(*useragent*)

​	为指定的 *useragent* 从 `robots.txt` 返回 `Crawl-delay` 形参。 如果此形参不存在或不适用于指定的 *useragent* 或者此形参的 `robots.txt` 条目存在语法错误，则返回 `None`。

*Added in version 3.6.*

## **request_rate**(*useragent*)

​	以 [named tuple](https://docs.python.org/zh-cn/3.13/glossary.html#term-named-tuple) `RequestRate(requests, seconds)` 的形式从 `robots.txt` 返回 `Request-rate` 形参的内容。 如果此形参不存在或不适用于指定的 *useragent* 或者此形参的 `robots.txt` 条目存在语法错误，则返回 `None`。

*Added in version 3.6.*

## **site_maps**()

​	以 [`list()`](https://docs.python.org/zh-cn/3.13/library/stdtypes.html#list) 的形式从 `robots.txt` 返回 `Sitemap` 形参的内容。 如果此形参不存在或者此形参的 `robots.txt` 条目存在语法错误，则返回 `None`。

*Added in version 3.8.*

​	下面的例子演示了 [`RobotFileParser`](https://docs.python.org/zh-cn/3.13/library/urllib.robotparser.html#urllib.robotparser.RobotFileParser) 类的基本用法:



```
>>> import urllib.robotparser
>>> rp = urllib.robotparser.RobotFileParser()
>>> rp.set_url("http://www.musi-cal.com/robots.txt")
>>> rp.read()
>>> rrate = rp.request_rate("*")
>>> rrate.requests
3
>>> rrate.seconds
20
>>> rp.crawl_delay("*")
6
>>> rp.can_fetch("*", "http://www.musi-cal.com/cgi-bin/search?city=San+Francisco")
False
>>> rp.can_fetch("*", "http://www.musi-cal.com/")
True
```
