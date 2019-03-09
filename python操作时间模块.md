# pythono操作时间
    Python中提供了多个用于对日期和时间进行操作的内置模块：time模块、datetime模块和calendar模块。其中time模块是通过调用C库实现的，所以有些方法在某些平台上可能无法调用，但是其提供的大部分接口与C标准库time.h基本一致。time模块相比，datetime模块提供的接口更直观、易用，功能也更加强大。
# 时间相关术语
>- UTC time Coordinated Universal Time
```
世界协调时，又称 格林尼治天文时间、世界标准时间。
与UTC time对应的是各个时区的local time，
东N区的时间比UTC时间早N个小时，因此UTC time + N小时 即为东N区的本地时间；
而西N区时间比UTC时间晚N个小时，即 UTC time - N小时 即为西N区的本地时间； 
中国在东8区，因此比UTC时间早8小时，可以以UTC+8进行表示。
```
>- epoch time
```
表示时间开始的起点；它是一个特定的时间，
不同平台上这个时间点的值不太相同，对于Unix而言，epoch time为 1970-01-01 00:00:00 UTC。
```
>- timestamp（时间戳）
```
也称为Unix时间 或 POSIX时间；它是一种时间表示方式，表示从格林尼治时间1970年1月1日0时0分0秒开始到现在所经过的毫秒数，其值为float类型。 
但是有些编程语言的相关方法返回的是秒数（Python就是这样），这个需要看方法的文档说明。需要说明的是时间戳是个差值，其值与时区无关。
```
# time模块
![](https://images2015.cnblogs.com/blog/1063221/201702/1063221-20170204153018854-1098926175.png)
## time模块的时间表示形式
    time.struct_time
>- tm_year :	年份，如 2017
>- tm_mon  :	月份，取值范围为[1, 12]
>- tm_mday :	一个月中的第几天，取值范围为[1-31]
>- tm_hour :	小时， 取值范围为[0-23]
>- tm_min  :	分钟，取值范围为[0, 59]
>- tm_sec  :    秒，取值范围为[0, 61]
>- tm_wday :	一个星期中的第几天，取值范围为[0-6]，0表示星期一
>- tm_yday :   一年中的第几天，取值范围为[1, 366]
>- tm_isdst:	是否为夏令时，可取值为：0 , 1 或 -1
## time模块API
>- time.altzone
>>- 返回与utc时间的时间差，以秒为单位（西区该值为正，东区该值为负）。其表示的是本地DST 时区的偏移量，只有daylight非0时才使用。

>- time.clock()
>>- 返回当前进程所消耗的处理器运行时间秒数（不包括sleep时间），值为小数；该方法Python3.3改成了time.process_time()

>- time.time()
>>- 返回时间戳（自1970-1-1 0:00:00 至今所经历的秒数）

>- time.localtime([secs])
>>- 返回以指定时间戳对应的本地时间的 struct_time对象（可以通过下标，也可以通过 .属性名 的方式来引用内部属性）格式

>- time.localtime(time.time() + n*3600)
>>- 返回n个小时后本地时间的 struct_time对象格式（可以用来实现类似crontab的功能）

>- time.gmtime([secs])
>>- 返回指定时间戳对应的utc时间的 struct_time对象格式（与当前本地时间差8个小时）

>- time.gmtime(time.time() + n*3600)
>>- 返回n个小时后utc时间的 struct_time对象（可以通过 .属性名 的方式来引用内部属性）格式

>- time.ctime([secs])
>>- 将一个秒数时间戳表示的时间转换为一个表示当前本地时间的字符串。('Wed Jun 20 22:20:20 2018')如果参数secs没有提供或值为None，则取time()方法的返回值作为默认值。

>- time.asctime([t])
>>- 将一个tuple或struct_time形式的时间（可以通过gmtime()和localtime()方法获取）转换为一个24个字符的时间字符串，格式为: "Fri Aug 19 11:14:16 2016"。如果参数t未提供，则取localtime()的返回值作为参数。

>- time.strptime(time_str, time_format_str)
>>- 将时间字符串转换为struct_time时间对象，如：time.strptime('2017-01-13 17:07', '%Y-%m-%d %H:%M')

>- time.mktime(struct_time_instance)
>>- 	将struct_time对象实例转换成时间戳

>- time.strftime(time_format_str, struct_time_instance)
>>- 将struct_time对象实例转换成字符串 time.strftime('%Y-%m-%d %H:%M:%S %z', time.localtime())

# datetime模块
    datetime模块提供了处理日期和时间的类，既有简单的方式，又有复杂的方式。它虽然支持日期和时间算法，但其实现的重点是为输出格式化和操作提供高效的属性提取功能。
![](https://images2015.cnblogs.com/blog/1063221/201702/1063221-20170205083810667-126583354.png)

## datetime.date 类
    表示日期，常用的属性有：year, month和day
### 类的定义
    class datetime.date(year, month, day)
- year, month 和 day都是是必须参数，各参数的取值范围为：

|参数名称 |取值范围|
|---------|--------|
|year	|[MINYEAR, MAXYEAR]|
|month	|[1, 12]|
|day	|[1, 指定年份的月份中的天数]|
### 类方法和属性
|类方法/属性名称|	描述|
|:---|:-------|
|date.max|	date对象所能表示的最大日期：datetime.date(9999, 12, 31)|
|date.min|	date对象所能表示的最小日志：datetime.date(1, 1, 1)|
|date.resoluation|	date对象表示的日期的最小单位：天: datetime.timedelta(1)|
|date.today()|	返回一个表示当前本地日期的date对象(datetime.date(2018, 6, 20))|
|date.fromtimestamp(timestamp)|	根据跟定的时间戳，返回一个date对象|
### 对象方法和属性
|对象方法/属性名称|描述|
| :--- | :--- |
|d.year|	年|
|d.month|	月|
|d.day|	日|
|d.replace(year[, month[, day]])|	生成并返回一个新的日期对象，原日期对象不变|
|d.timetuple()|	返回日期对应的time.struct_time对象|
|d.toordinal()|	返回日期是是自 0001-01-01 开始的第多少天|
|d.weekday()|	返回日期是星期几，[0, 6]，0表示星期一|
|d.isoweekday()|	返回日期是星期几，[1, 7], 1表示星期一|
|d.isocalendar()|	返回一个元组，格式为：(year, weekday, isoweekday)|
|d.isoformat()|	返回‘YYYY-MM-DD’格式的日期字符串|
|d.strftime(format)|	返回指定格式的日期字符串，与time模块的strftime(format, struct_time)功能相同|
## datetime.time
    表示时间，常用属性有：hour, minute, second, microsecond
### 类的定义
    class datetime.time(hour, [minute[, second, [microsecond[, tzinfo]]]])
- hour为必须参数，其他为可选参数。各参数的取值范围为：：

|参数名称|取值范围|
|:----|:---|
|hour         |	[0, 23]|
|minute       |	[0, 59]|
|second       |	[0, 59]|
|microsecond  |	[0, 1000000]|
|tzinfo	tzinfo| 的子类对象，如timezone类的实例|
### 类方法和属性
|类方法/属性名称|描述|
|:---|:---|
|time.max|	time类所能表示的最大时间：time(23, 59, 59, 999999)|
|time.min|	time类所能表示的最小时间：time(0, 0, 0, 0)|
|time.resolution|	时间的最小单位，即两个不同时间的最小差值：1微秒|
###对象方法和属性
|对象方法/属性名称|描述|
|:---|:---|
|t.hour|	时|
|t.minute|	分|
|t.second|	秒|
|t.microsecond|	微秒|
|t.tzinfo	|返回传递给time构造方法的tzinfo对象，如果该参数未给出，则返回None|
|t.replace(hour[, minute[, second[, microsecond[, tzinfo]]]])|	生成并返回一个新的时间对象，原时间对象不变|
|t.isoformat()|	返回一个‘HH:MM:SS.%f’格式的时间字符串|
|t.strftime()|	返回指定格式的时间字符串，与time模块的strftime(format, struct_time)功能相同|
## datetime.datetime
    表示日期时间
### 类的定义
    class datetime.datetime(year, month, day, hour=0, minute=0, second=0, microsecond=0, tzinfo=None)
- year, month 和 day是必须要传递的参数， tzinfo可以是None或tzinfo子类的实例。
- 各参数的取值范围为：

|参数名称|取值范围|
|---|---|
|year|	[MINYEAR, MAXYEAR]|
|month|	[1, 12]|
|day	|[1, 指定年份的月份中的天数]|
|hour	|[0, 23]|
|minute	|[0, 59]|
|second	|[0, 59]|
|microsecond|	[0, 1000000]|
|tzinfo	|tzinfo的子类对象，如timezone类的实例|
### 类方法和属性

|类方法/属性名称|	描述|
|---|---|
|datetime.today()	|返回一个表示当前本期日期时间的datetime对象|
|datetime.now([tz])	|返回指定时区日期时间的datetime对象，如果不指定tz参数则结果同上|
|datetime.utcnow()	|返回当前utc日期时间的datetime对象|
|datetime.fromtimestamp(timestamp[, tz])	|根据指定的时间戳创建一个datetime对象|
|datetime.utcfromtimestamp(timestamp)	|根据指定的时间戳创建一个datetime对象|
|datetime.combine(date, time)	|把指定的date和time对象整合成一个datetime对象|
|datetime.strptime(date_str, format)|	将时间字符串转换为datetime对象|
### 对象方法和属性

|对象方法/属性名称|描述|
|---|---|
|dt.year, dt.month, dt.day	|年、月、日|
|dt.hour, dt.minute, dt.second	|时、分、秒|
|dt.microsecond, dt.tzinfo	|微秒、时区信息|
|dt.date()|	获取datetime对象对应的date对象|
|dt.time()|	获取datetime对象对应的time对象， tzinfo 为None|
|dt.timetz()|	获取datetime对象对应的time对象，tzinfo与datetime对象的tzinfo相同|
|dt.replace([year[, month[, day[, hour[, minute[, second[, microsecond[, tzinfo]]]]]]]])|	生成并返回一个新的datetime对象，如果所有参数都没有指定，则返回一个与原datetime对象相同的对象|
|dt.timetuple()	|返回datetime对象对应的tuple（不包括tzinfo）|
|dt.utctimetuple()|	返回datetime对象对应的utc时间的tuple（不包括tzinfo）|
|dt.toordinal()	|同date对象|
|dt.weekday()	|同date对象|
|dt.isocalendar()	|同date独享|
|dt.isoformat([sep])|	返回一个‘%Y-%m-%d|
|dt.ctime()	|等价于time模块的time.ctime(time.mktime(d.timetuple()))|
|dt.strftime(format)	|返回指定格式的时间字符串|

## datetime.timedelta
    	表示两个date、time、datetime实例之间的时间间隔，分辨率（最小单位）可达到微秒	表示两个date、time、datetime实例之间的时间间隔，分辨率（最小单位）可达到微秒
```
timedelta对象表示连个不同时间之间的差值。如果使用time模块对时间进行算术运行，只能将字符串格式的时间 和 struct_time格式的时间对象 先转换为时间戳格式，然后对该时间戳加上或减去n秒，最后再转换回struct_time格式或字符串格式，这显然很不方便。而datetime模块提供的timedelta类可以让我们很方面的对datetime.date, datetime.time和datetime.datetime对象做算术运算，且两个时间之间的差值单位也更加容易控制。
这个差值的单位可以是：天、秒、微秒、毫秒、分钟、小时、周。
```
### datetime.timedelta类的定义
    class datetime.timedelta(days=0, seconds=0, microseconds=0, milliseconds=0, hours=0, weeks=0)
>- 所有参数都是默认参数，因此都是可选参数。参数的值可以是整数或浮点数，也可以是正数或负数。内部值存储days、seconds 和 microseconds，其他所有参数都将被转换成这3个单位：
- 1毫秒转换为1000微秒
- 1分钟转换为60秒
- 1小时转换为3600秒
- 1周转换为7天
### 类属性

|类属性名称|描述|
|---|---|
|timedelta.min|	timedelta(-999999999)|
|timedelta.max|	timedelta(days=999999999, hours=23, minutes=59, seconds=59, microseconds=999999)|
|timedelta.resolution|	timedelta(microseconds=1)|
### 实例方法和属性

|实例方法/属性名称|描述|
|---|---|
|td.days|	天 [-999999999, 999999999]|
|td.seconds|	秒 [0, 86399]|
|td.microseconds|	微秒 [0, 999999]|
|td.total_seconds()|	时间差中包含的总秒数，等价于: td / timedelta(seconds=1)|
```
>>> import datetime
>>>
>>> datetime.timedelta(365).total_seconds()  # 一年包含的总秒数
31536000.0
>>> dt = datetime.datetime.now()
>>> dt + datetime.timedelta(3)  # 3天后
datetime.datetime(2017, 2, 8, 9, 39, 40, 102821)
>>> dt + datetime.timedelta(-3)  # 3天前
datetime.datetime(2017, 2, 2, 9, 39, 40, 102821)
>>> dt + datetime.timedelta(hours=3)  # 3小时后
datetime.datetime(2017, 2, 5, 12, 39, 40, 102821)
>>> dt + datetime.timedelta(hours=-3)  # 3小时前
datetime.datetime(2017, 2, 5, 6, 39, 40, 102821)
>>> dt + datetime.timedelta(hours=3, seconds=30)  # 3小时30秒后  
datetime.datetime(2017, 2, 5, 12, 40, 10, 102821)
```
### string转datetime并设置时区
```python
import pytz
import datetime
#先把字符串转成不带时区的datetime
dt = datetime.datetime.strptime('2018-02-03 15:37:12' , '%Y-%m-%d %H:%M:%S')
#把不带时区的datetime转成timestamp再转成秒
ts = int(dt.timestamp())
#把秒转成带时区的datetime
t = datetime.datetime.fromtimestamp(ts, pytz.timezone('Asia/Shanghai'))
```
## datetime.tzinfo
    时区相关信息对象的抽象基类。它们由datetime和time类使用，以提供自定义时间的而调整。
## datetime.timezone
    Python 3.2中新增的功能，实现tzinfo抽象基类的类，表示与UTC的固定偏移量
# 时间格式码
    time模块的struct_time以及datetime模块的datetime、date、time类都提供了strftime()方法，该方法可 以输出一个指定格式的时间字符串。具体格式由一系列的格式码（格式字符）组成，Python最终调用的是各个平台C库的strftme()函数，因此各平台对全套格式码的支持会有所不同，具体情况需要参考该平台上的strftime(3)文档。下面列出了C标准（1989版）要求的所有格式码，它们在所有标准C实现的平台上都可以工作：
![](https://images2015.cnblogs.com/blog/1063221/201702/1063221-20170205104434573-1343126060.png)
    
    

```https://blog.csdn.net/p9bl5bxp/article/details/54945920
```

