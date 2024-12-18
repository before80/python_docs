+++
title = "calendar --- 通用日历相关函数"
date = 2024-11-15T11:18:41+08:00
weight = 20
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/calendar.html](https://docs.python.org/zh-cn/3.13/library/calendar.html)
>
> 收录该文档的时间：`2024-11-15T11:18:41+08:00`

# `calendar` --- 通用日历相关函数

**源代码：** [Lib/calendar.py](https://github.com/python/cpython/tree/3.13/Lib/calendar.py)

------

​	这个模块让你可以输出像 Unix **cal** 那样的日历，它还提供了其它与日历相关的实用函数。 默认情况下，这些日历把星期一作为一周的第一天，星期天作为一周的最后一天（这是欧洲惯例）。可以使用 [`setfirstweekday()`]({{< ref "/library/datatypes/calendar#calendar.setfirstweekday" >}}) 方法设置一周的第一天为星期天 (6) 或者其它任意一天。函数全部接收整数类型的参数用来指定日期。其它相关功能参见 [`datetime`]({{< ref "/library/datatypes/datetime#module-datetime" >}}) 和 [`time`]({{< ref "/library/allos/time#module-time" >}}) 模块。

​	在这个模块中定义的函数和类都基于一个理想化的日历——向过去和未来两个方向无限扩展的现行公历。这与 Dershowitz 和 Reingold 的书“历法计算”中所有计算的基本日历 "proleptic Gregorian" 历的定义相符。0 和负数年份按照 ISO 8601 标准解释：0 年指公元前 1 年，-1 年指公元前 2 年，依此类推。

## *class* calendar.**Calendar**(*firstweekday=0*)

​	创建一个 [`Calendar`]({{< ref "/library/datatypes/calendar#calendar.Calendar" >}}) 对象。*firstweekday* 是一个用来指定每星期第一天的整数。[`MONDAY`]({{< ref "/library/datatypes/calendar#calendar.MONDAY" >}}) 是 `0` （默认值），[`SUNDAY`]({{< ref "/library/datatypes/calendar#calendar.SUNDAY" >}}) 是 `6`。

[`Calendar`]({{< ref "/library/datatypes/calendar#calendar.Calendar" >}}) 对象提供了一些可用于对日历数据进行格式化的准备的方法。这个类本身不执行任何格式化操作。 这部分任务应由子类来完成。

[`Calendar`]({{< ref "/library/datatypes/calendar#calendar.Calendar" >}}) 实例有下列方法：

## **iterweekdays**()

​	返回一个迭代器，迭代器的内容为一周里每天的星期值。迭代器的第一个值与 [`firstweekday`]({{< ref "/library/datatypes/calendar#calendar.firstweekday" >}}) 属性的值一致。

## **itermonthdates**(*year*, *month*)

​	为 *year* 年 *month* 月 (1-12) 返回一个迭代器。这个迭代器返回当月的所有日期（使用 [`datetime.date`]({{< ref "/library/datatypes/datetime#datetime.date" >}}) 对象），日期包含了本月头尾用于组成完整一周的日期。

## **itermonthdays**(*year*, *month*)

​	为 *year* 年 *month* 月返回一个与 [`itermonthdates()`]({{< ref "/library/datatypes/calendar#calendar.Calendar.itermonthdates" >}}) 类似的迭代器，但不会受 [`datetime.date`]({{< ref "/library/datatypes/datetime#datetime.date" >}}) 范围的限制。返回的为每一天的日期相对于当月 1 日过去的天数。对于不在当月的日期，返回数字 `0`。

## **itermonthdays2**(*year*, *month*)

​	为 *year* 年 *month* 月返回一个与 [`itermonthdates()`]({{< ref "/library/datatypes/calendar#calendar.Calendar.itermonthdates" >}}) 类似的迭代器，但不会受 [`datetime.date`]({{< ref "/library/datatypes/datetime#datetime.date" >}}) 范围的限制。迭代器中的每一个元素为由日数和代表星期几的数字组成的元组。

## **itermonthdays3**(*year*, *month*)

​	为 *year* 年 *month* 月返回一个与 [`itermonthdates()`]({{< ref "/library/datatypes/calendar#calendar.Calendar.itermonthdates" >}}) 类似的迭代器，但不会受 [`datetime.date`]({{< ref "/library/datatypes/datetime#datetime.date" >}}) 范围的限制。迭代器的元素为一个由年、月、日组成的元组。

> Added in version 3.7.
>

## **itermonthdays4**(*year*, *month*)

​	为 *year* 年 *month* 月返回一个与 [`itermonthdates()`]({{< ref "/library/datatypes/calendar#calendar.Calendar.itermonthdates" >}}) 类似的迭代器，但不会受 [`datetime.date`]({{< ref "/library/datatypes/datetime#datetime.date" >}}) 范围的限制。迭代器的元素为一个由年、月、日和代表星期几的数字组成的元组。

> Added in version 3.7.
>

## **monthdatescalendar**(*year*, *month*)

​	返回 *year* 年 *month* 月的周组成的列表。列表中的每一个周是由七个 [`datetime.date`]({{< ref "/library/datatypes/datetime#datetime.date" >}}) 对象组成的列表。

## **monthdays2calendar**(*year*, *month*)

​	返回 *year* 年 *month* 月的周组成的列表。列表中的每一个周是七个由日数和代表星期几的数字组成的元组的列表。

## **monthdayscalendar**(*year*, *month*)

​	返回 *year* 年 *month* 月的周组成的列表。列表中的每一个周是由七个日数组成的列表。

## **yeardatescalendar**(*year*, *width=3*)

​	返回可以用来格式化的指定年月的数据。返回的值是一个列表，列表是月份组成的行。每一行包含了最多 *width* 个月(默认为3)。每个月包含了4到6周，每周包含1--7天。每一天使用 [`datetime.date`]({{< ref "/library/datatypes/datetime#datetime.date" >}}) 对象。

## **yeardays2calendar**(*year*, *width=3*)

​	返回可以用来模式化的指定年月的数据(与 [`yeardatescalendar()`]({{< ref "/library/datatypes/calendar#calendar.Calendar.yeardatescalendar" >}}) 类似)。周列表的元素是由表示日期的数字和表示星期几的数字组成的元组。不在这个月的日子为0。

## **yeardayscalendar**(*year*, *width=3*)

​	返回可以用来模式化的指定年月的数据(与 [`yeardatescalendar()`]({{< ref "/library/datatypes/calendar#calendar.Calendar.yeardatescalendar" >}}) 类似)。周列表的元素是表示日期的数字。不在这个月的日子为0。

## *class* calendar.**TextCalendar**(*firstweekday=0*)

​	可以使用这个类生成纯文本日历。

[`TextCalendar`]({{< ref "/library/datatypes/calendar#calendar.TextCalendar" >}}) 实例有以下方法：

## **formatmonth**(*theyear*, *themonth*, *w=0*, *l=0*)

​	返回指定月的用多行字符串表示的月历。*w* 为日期列的宽度，日期列居中打印。*l* 指定了周与周之间的行距。返回的日历还依赖于构造器或者 [`setfirstweekday()`]({{< ref "/library/datatypes/calendar#calendar.setfirstweekday" >}}) 方法指定的每周的第一天是哪一天。

## **prmonth**(*theyear*, *themonth*, *w=0*, *l=0*)

​	调用 [`formatmonth()`]({{< ref "/library/datatypes/calendar#calendar.TextCalendar.formatmonth" >}}) 方法并打印返回的月历。

## **formatyear**(*theyear*, *w=2*, *l=1*, *c=6*, *m=3*)

​	返回指定年的用多行字符串表示的 *m* 列年历。可选参数 *w*、*l* 和 *c* 分别表示日期列宽，周的行距，和月与月之间的纵向间隔。同样依赖于构造器或者 [`setfirstweekday()`]({{< ref "/library/datatypes/calendar#calendar.setfirstweekday" >}}) 方法指定的每周的第一天是哪一天。可以生成年历的最早的年是哪一年依赖于使用的平台。

## **pryear**(*theyear*, *w=2*, *l=1*, *c=6*, *m=3*)

​	调用 [`formatyear()`]({{< ref "/library/datatypes/calendar#calendar.TextCalendar.formatyear" >}}) 方法并打印返回的年历。

## *class* calendar.**HTMLCalendar**(*firstweekday=0*)

​	可以使用这个类生成 HTML 日历。

`HTMLCalendar` 实例有以下方法：

## **formatmonth**(*theyear*, *themonth*, *withyear=True*)

​	返回一个 HTML 表格作为指定年月的日历。 *withyear* 为真，则年份将会包含在表头，否则只显示月份。

## **formatyear**(*theyear*, *width=3*)

​	返回一个 HTML 表格作为指定年份的日历。 *width* (默认为3) 用于规定每一行显示月份的数量。

## **formatyearpage**(*theyear*, *width=3*, *css='calendar.css'*, *encoding=None*)

​	返回一个完整的 HTML 页面作为指定年份的日历。 *width\*(默认为3) 用于规定每一行显示的月份数量。 \*css* 为层叠样式表的名字。如果不使用任何层叠样式表，可以使用 [`None`]({{< ref "/library/constants#None" >}}) 。 *encoding* 为输出页面的编码 (默认为系统的默认编码)。

## **formatmonthname**(*theyear*, *themonth*, *withyear=True*)

​	将一个月份名称以 HTML 表格行的形式返回。 如果 *withyear* 为真值则年份将被包括在行中，否则将只使用月份名称。

`HTMLCalendar` 有以下属性，你可以重写它们来自定义应用日历的样式。

## **cssclasses**

​	一个对应星期一到星期天的 CSS class 列表。默认列表为

```
cssclasses = ["mon", "tue", "wed", "thu", "fri", "sat", "sun"]
```

​	可以向每天加入其它样式

```
cssclasses = ["mon text-bold", "tue", "wed", "thu", "fri", "sat", "sun red"]
```

​	需要注意的是，列表的长度必须为7。

## **cssclass_noday**

​	工作日的 CSS 类在上个月或下个月发生。

> Added in version 3.7.
>

## **cssclasses_weekday_head**

​	用于标题行中的工作日名称的 CSS 类 列表。默认值与 [`cssclasses`]({{< ref "/library/datatypes/calendar#calendar.HTMLCalendar.cssclasses" >}}) 相同。

> Added in version 3.7.
>

## **cssclass_month_head**

​	月份的头 CSS 类（由 [`formatmonthname()`]({{< ref "/library/datatypes/calendar#calendar.HTMLCalendar.formatmonthname" >}}) 使用）。默认值为 `"month"` 。

> Added in version 3.7.
>

## **cssclass_month**

​	某个月的月历的 CSS 类（由 [`formatmonth()`]({{< ref "/library/datatypes/calendar#calendar.HTMLCalendar.formatmonth" >}}) 使用）。默认值为 `"month"` 。

> Added in version 3.7.
>

## **cssclass_year**

​	某年的年历的 CSS 类（由 [`formatyear()`]({{< ref "/library/datatypes/calendar#calendar.HTMLCalendar.formatyear" >}}) 使用）。默认值为 `"year"` 。

> Added in version 3.7.
>

## **cssclass_year_head**

​	年历的·表头 CSS 类（由 [`formatyear()`]({{< ref "/library/datatypes/calendar#calendar.HTMLCalendar.formatyear" >}}) 使用）。默认值为 `"year"` 。

> Added in version 3.7.
>

​	需要注意的是，尽管上面命名的样式类都是单独出现的(如： `cssclass_month` `cssclass_noday`), 但我们可以使用空格将样式类列表中的多个元素分隔开，例如：

```
"text-bold text-red"
```

​	下面是一个如何自定义 `HTMLCalendar` 的示例

```
class CustomHTMLCal(calendar.HTMLCalendar):
    cssclasses = [style + " text-nowrap" for style in
                  calendar.HTMLCalendar.cssclasses]
    cssclass_month_head = "text-center month-head"
    cssclass_month = "text-center month"
    cssclass_year = "text-italic lead"
```

## *class* calendar.**LocaleTextCalendar**(*firstweekday=0*, *locale=None*)

​	可以向这个 [`TextCalendar`]({{< ref "/library/datatypes/calendar#calendar.TextCalendar" >}}) 的子类的构造器传入一个语言区域名称并将返回指定语言区域下的月份和星期名称。

## *class* calendar.**LocaleHTMLCalendar**(*firstweekday=0*, *locale=None*)

​	可以向这个 [`HTMLCalendar`]({{< ref "/library/datatypes/calendar#calendar.HTMLCalendar" >}}) 的子类的构造器传入一个语言区域名称并将返回指定语言区域下的月份和星期名称。

​备注
 

​	这两个类的构造器、`formatweekday()` 和 `formatmonthname()` 方法会临时将 `LC_TIME` 语言区域更改为给定的 *locale*。 因为当前语言区域是进程级的设置，所以它们不是线程安全的。

​	这个模块为简单的文本日历提供了下列函数。

## calendar.**setfirstweekday**(*weekday*)

​	设置每一周的开始（`0` 表示星期一，`6` 表示星期天）。提供了 [`MONDAY`]({{< ref "/library/datatypes/calendar#calendar.MONDAY" >}})、[`TUESDAY`]({{< ref "/library/datatypes/calendar#calendar.TUESDAY" >}})、[`WEDNESDAY`]({{< ref "/library/datatypes/calendar#calendar.WEDNESDAY" >}})、[`THURSDAY`]({{< ref "/library/datatypes/calendar#calendar.THURSDAY" >}})、[`FRIDAY`]({{< ref "/library/datatypes/calendar#calendar.FRIDAY" >}})、[`SATURDAY`]({{< ref "/library/datatypes/calendar#calendar.SATURDAY" >}}) 和 [`SUNDAY`]({{< ref "/library/datatypes/calendar#calendar.SUNDAY" >}}) 几个常量值作为方便。例如，设置每周的第一天为星期天：

```
import calendar
calendar.setfirstweekday(calendar.SUNDAY)
```

## calendar.**firstweekday**()

​	返回当前设置的每星期的第一天的数值。

## calendar.**isleap**(*year*)

​	如果 *year* 是闰年则返回 [`True`]({{< ref "/library/constants#True" >}}) ,否则返回 [`False`]({{< ref "/library/constants#False" >}})。

## calendar.**leapdays**(*y1*, *y2*)

​	返回在范围 *y1* 至 *y2* （不包括 y2）之间的闰年的年数，其中 *y1* 和 *y2* 是年份。

​	此函数对于跨越世纪初的范围也适用。

## calendar.**weekday**(*year*, *month*, *day*)

​	返回某年（ `1970` -- ...），某月（ `1` -- `12` ），某日（ `1` -- `31` ）是星期几（ `0` 是星期一）。

## calendar.**weekheader**(*n*)

​	返回一个包含星期几的缩写名的头。 *n* 指定星期几缩写的字符宽度。

## calendar.**monthrange**(*year*, *month*)

​	返回指定 *年份* 的指定 *月份* 的第一天是星期几和这个月的天数。

## calendar.**monthcalendar**(*year*, *month*)

​	返回表示一个月的日历的矩阵。 每一行代表一周；此月份外的日子由零表示。 每周从周一开始，除非使用 [`setfirstweekday()`]({{< ref "/library/datatypes/calendar#calendar.setfirstweekday" >}}) 改变设置。

## calendar.**prmonth**(*theyear*, *themonth*, *w=0*, *l=0*)

​	打印由 [`month()`]({{< ref "/library/datatypes/calendar#calendar.month" >}}) 返回的一个月的日历。

## calendar.**month**(*theyear*, *themonth*, *w=0*, *l=0*)

​	使用 [`TextCalendar`]({{< ref "/library/datatypes/calendar#calendar.TextCalendar" >}}) 类的 [`formatmonth()`]({{< ref "/library/datatypes/calendar#calendar.TextCalendar.formatmonth" >}}) 返回多行字符串形式的月份日历。

## calendar.**prcal**(*year*, *w=0*, *l=0*, *c=6*, *m=3*)

​	打印由 [`calendar()`]({{< ref "/library/datatypes/calendar#module-calendar" >}}) 返回的整年的日历。

## calendar.**calendar**(*year*, *w=2*, *l=1*, *c=6*, *m=3*)

​	使用 [`TextCalendar`]({{< ref "/library/datatypes/calendar#calendar.TextCalendar" >}}) 类的 [`formatyear()`]({{< ref "/library/datatypes/calendar#calendar.TextCalendar.formatyear" >}}) 返回一个整年的 3 列日历。

## calendar.**timegm**(*tuple*)

​	一个不相关但很好用的函数，它接受一个时间元组例如 [`time`]({{< ref "/library/allos/time#module-time" >}}) 模块中的 [`gmtime()`]({{< ref "/library/allos/time#time.gmtime" >}}) 函数的返回并返回相应的 Unix 时间戳值，假定 1970 年开始计数， POSIX 编码。实际上， [`time.gmtime()`]({{< ref "/library/allos/time#time.gmtime" >}}) 和 [`timegm()`]({{< ref "/library/datatypes/calendar#calendar.timegm" >}}) 是彼此相反的。

[`calendar`]({{< ref "/library/datatypes/calendar#module-calendar" >}}) 模块导出以下数据属性：

## calendar.**day_name**

​	在当前语言区域下表示周内日期的序列，其中“星期一”为 0 号日。



``` python
>>> import calendar
>>> list(calendar.day_name)
['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']
```

## calendar.**day_abbr**

​	在当前语言区域下简写表示周内日期的序列，其中“一” 为 0 号日。



``` python
>>> import calendar
>>> list(calendar.day_abbr)
['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
```

## calendar.**MONDAY**

## calendar.**TUESDAY**

## calendar.**WEDNESDAY**

## calendar.**THURSDAY**

## calendar.**FRIDAY**

## calendar.**SATURDAY**

## calendar.**SUNDAY**

​	星期内每日序号的别名，其中 `MONDAY` 是 `0` 而 `SUNDAY` 是 `6`。

> Added in version 3.12.
>

## *class* calendar.**Day**

​	将星期内的每一天定义为整数常量的枚举。 该枚举的成员以 [`MONDAY`]({{< ref "/library/datatypes/calendar#calendar.MONDAY" >}}) 至 [`SUNDAY`]({{< ref "/library/datatypes/calendar#calendar.SUNDAY" >}}) 的形式导出到模块作用域。

> Added in version 3.12.
>

## calendar.**month_name**

​	在当前语言区域下表示一年中月份的序列。 这遵循一月的月份序号为 1 的通常惯例，所以其长度为 13 且 `month_name[0]` 为空字符串。



``` python
>>> import calendar
>>> list(calendar.month_name)
['', 'January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December']
```

## calendar.**month_abbr**

​	在当前语言区域下简写表示一年中月份的序列。 这遵循一月的月份序号为 1 的通常惯例，所以其长度为 13 且 `month_abbr[0]` 为空字符串。



``` python
>>> import calendar
>>> list(calendar.month_abbr)
['', 'Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']
```

## calendar.**JANUARY**

## calendar.**FEBRUARY**

## calendar.**MARCH**

## calendar.**APRIL**

## calendar.**MAY**

## calendar.**JUNE**

## calendar.**JULY**

## calendar.**AUGUST**

## calendar.**SEPTEMBER**

## calendar.**OCTOBER**

## calendar.**NOVEMBER**

## calendar.**DECEMBER**

​	一年中各个月份的别名，其中 `JANUARY` 是 `1` 而 `DECEMBER` 是 `12`。

> Added in version 3.12.
>

## *class* calendar.**Month**

​	将一年中各个月份定义为整数常量的枚举。 该枚举的成员以 [`JANUARY`]({{< ref "/library/datatypes/calendar#calendar.JANUARY" >}}) 至 [`DECEMBER`]({{< ref "/library/datatypes/calendar#calendar.DECEMBER" >}}) 的形式导出到模块作用域。

> Added in version 3.12.
>

[`calendar`]({{< ref "/library/datatypes/calendar#module-calendar" >}}) 模块定义了以下异常:

## *exception* calendar.**IllegalMonthError**(*month*)

[`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 的子类，当给定的月份数字超出 1-12 范围（不包括边界值）时引发。

## **month**

​	无效的月份数字。

## *exception* calendar.**IllegalWeekdayError**(*weekday*)

[`ValueError`]({{< ref "/library/exceptions#ValueError" >}}) 的子类，当给定的星期数字超出 0-6 范围（不包括边界值）时引发。

## **weekday**

​	无效的星期数字。

​参见
## 模块 [`datetime`]({{< ref "/library/datatypes/datetime#module-datetime" >}})

​	为日期和时间提供与 [`time`]({{< ref "/library/allos/time#module-time" >}}) 模块相似功能的面向对象接口。

## 模块 [`time`]({{< ref "/library/allos/time#module-time" >}})

​	底层时间相关函数。



## 命令行用法

> Added in version 2.5.
>

[`calendar`]({{< ref "/library/datatypes/calendar#module-calendar" >}}) 模块可以作为脚本从命令行执行以实现交互式地打印日历。

```
python -m calendar [-h] [-L LOCALE] [-e ENCODING] [-t {text,html}]
                   [-w WIDTH] [-l LINES] [-s SPACING] [-m MONTHS] [-c CSS]
                   [-f FIRST_WEEKDAY] [year] [month]
```

​	例如，打印 2000 年的日历：

```
$ python -m calendar 2000
                                  2000

      January                   February                   March
Mo Tu We Th Fr Sa Su      Mo Tu We Th Fr Sa Su      Mo Tu We Th Fr Sa Su
                1  2          1  2  3  4  5  6             1  2  3  4  5
 3  4  5  6  7  8  9       7  8  9 10 11 12 13       6  7  8  9 10 11 12
10 11 12 13 14 15 16      14 15 16 17 18 19 20      13 14 15 16 17 18 19
17 18 19 20 21 22 23      21 22 23 24 25 26 27      20 21 22 23 24 25 26
24 25 26 27 28 29 30      28 29                     27 28 29 30 31
31

       April                      May                       June
Mo Tu We Th Fr Sa Su      Mo Tu We Th Fr Sa Su      Mo Tu We Th Fr Sa Su
                1  2       1  2  3  4  5  6  7                1  2  3  4
 3  4  5  6  7  8  9       8  9 10 11 12 13 14       5  6  7  8  9 10 11
10 11 12 13 14 15 16      15 16 17 18 19 20 21      12 13 14 15 16 17 18
17 18 19 20 21 22 23      22 23 24 25 26 27 28      19 20 21 22 23 24 25
24 25 26 27 28 29 30      29 30 31                  26 27 28 29 30

        July                     August                  September
Mo Tu We Th Fr Sa Su      Mo Tu We Th Fr Sa Su      Mo Tu We Th Fr Sa Su
                1  2          1  2  3  4  5  6                   1  2  3
 3  4  5  6  7  8  9       7  8  9 10 11 12 13       4  5  6  7  8  9 10
10 11 12 13 14 15 16      14 15 16 17 18 19 20      11 12 13 14 15 16 17
17 18 19 20 21 22 23      21 22 23 24 25 26 27      18 19 20 21 22 23 24
24 25 26 27 28 29 30      28 29 30 31               25 26 27 28 29 30
31

      October                   November                  December
Mo Tu We Th Fr Sa Su      Mo Tu We Th Fr Sa Su      Mo Tu We Th Fr Sa Su
                   1             1  2  3  4  5                   1  2  3
 2  3  4  5  6  7  8       6  7  8  9 10 11 12       4  5  6  7  8  9 10
 9 10 11 12 13 14 15      13 14 15 16 17 18 19      11 12 13 14 15 16 17
16 17 18 19 20 21 22      20 21 22 23 24 25 26      18 19 20 21 22 23 24
23 24 25 26 27 28 29      27 28 29 30               25 26 27 28 29 30 31
30 31
```

​	可以接受以下选项：

## `--help`, **-h**

​	显示帮助信息并退出。

## `--locale` LOCALE, **-L** LOCALE

​	月份和星期名称所使用的语言区域。 默认为英语。

## `--encoding` ENCODING, **-e** ENCODING

​	输出所使用的编码格式。 如果设置了 [`--locale`]({{< ref "/library/datatypes/calendar#cmdoption-calendar-locale" >}}) 则 [`--encoding`]({{< ref "/library/datatypes/calendar#cmdoption-calendar-encoding" >}}) 将是必须的。

## `--type` {text,html}, **-t** {text,html}

​	将日历以文本或 HTML 文档的形式打印到终端。

## `--first-weekday` FIRST_WEEKDAY, **-f** FIRST_WEEKDAY

​	每个星期的开始星期序号。 必须为 0 (星期一) 到 6 (星期日) 之间的数字。 默认为 0。

> Added in version 3.13.
>

## **year**

​	要打印日历的年份。 默认为当前年份。

## **month**

​	指定 [`year`]({{< ref "/library/datatypes/calendar#cmdoption-calendar-arg-year" >}}) 中要打印日历的月份。 必须是 1 到 12 之间的数字，且只能在文本模式下使用。 默认打印全年的日历。

*文本模式选项:*

## `--width` WIDTH, **-w** WIDTH

​	以终端的列数表示的日期列宽度。 日期将打印在列中央。 小于 2 的值将被忽略。 默认为 2。

## `--lines` LINES, **-l** LINES

​	以终端的行数表示的每周的行数。 日期将顶端对齐打印。小于 1 的值将被忽略。 默认为 1。

## `--spacing` SPACING, **-s** SPACING

​	列中的月份之间的空格。 小于 2 的值将被忽略。 默认为 6。

## `--months` MONTHS, **-m** MONTHS

​	每行打印的月份数。 默认为 3。

*HTML 模式选项:*

## `--css` CSS, **-c** CSS

​	日历要使用的 CSS 样式表的路径。 该路径必须是相对于所生成的 HTML，或是一个绝对 HTTP 或 `file:///` URL。
