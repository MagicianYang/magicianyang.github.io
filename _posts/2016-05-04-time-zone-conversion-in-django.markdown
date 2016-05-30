---
layout: post
section-type: post
title: Django中的时区转换&Python的时间处理
category: tech
tags: [ 'django','python' ]
toc: show
---


### Django中时区问题

#### recommendation

1.设置`USE_TZ = True`并且设置`TIME_ZONE = 'Asia/Shanghai'`

2.以第一条为前提，需要遵守的原则是——__所有存入model的时间一律先转换为utc时间再存储__，而通过`auto_now`以及`auto_now_add`自动生成的时间一定是utc时间，而且存入model的时间再取出之后的时区并不是naive的而是已经设定时区为utc的，例如从数据库中取出时间显示如下

```python
>>> a.time
datetime.datetime(2016, 5, 11, 6, 49, 18, tzinfo=<UTC>)
```

3.



### 实例

#### 本地时间和UTC之前的时区转换问题

```python
import pytz
import datetime

def convert_timezone(time_in):
    """
    用来将系统自动生成的datetime格式的utc时区时间转化为本地时间
    :param time_in: datetime.datetime格式的utc时间
    :return:输出仍旧是datetime.datetime格式，但已经转换为本地时间
    """
    time_utc = time_in.replace(tzinfo=pytz.timezone('UTC'))
    time_local = time_utc.astimezone(pytz.timezone('Asia/Shanghai'))
    return time_local


def convert_local_timezone(time_in):
    """
    用来将输入的datetime格式的本地时间转化为utc时区时间
    :param time_in: datetime.datetime格式的本地时间
    :return:输出仍旧是datetime.datetime格式，但已经转换为utc时间
    """
    local = pytz.timezone('Asia/Shanghai')
    local_dt = local.localize(time_in, is_dst=None)
    time_utc = local_dt.astimezone(pytz.utc)
    return time_utc
```

注意：传入参数因为[上海1927年的调表事件](http://stackoverflow.com/questions/6841333/why-is-subtracting-these-two-times-in-1927-giving-a-strange-result)，由本地时间转到UTC时间时，附上时区信息用replace的话会导致之后时间转换会多六分钟。终端测试情况如下

```python
In [1]: import pytz

In [2]: import datetime

In [3]: a=datetime.datetime.now()

In [4]: a
Out[4]: datetime.datetime(2016, 5, 11, 17, 13, 14, 791456)

In [5]: a_l = a.replace(tzinfo=pytz.timezone('Asia/Shanghai'))

In [6]: a_l
Out[6]: datetime.datetime(2016, 5, 11, 17, 13, 14, 791456, tzinfo=<DstTzInfo 'Asia/Shanghai' LMT+8:06:00 STD>)

In [7]: local = pytz.timezone('Asia/Shanghai')

In [8]: a_local = local.localize(a, is_dst=None)

In [9]: a_local
Out[9]: datetime.datetime(2016, 5, 11, 17, 13, 14, 791456, tzinfo=<DstTzInfo 'Asia/Shanghai' CST+8:00:00 STD>)
```

#### 获取某时刻的准确时间戳（考虑时区）

```python
import pytz
import datetime
import time
date_str = '2016-05-27'
start_str = '{} 00:00:00'.format(date_str)
start_datetime = datetime.datetime.strptime(start_str,'%Y-%m-%d %H:%M:%S')
tz=pytz.timezone('Asia/Shanghai')
start_local = tz.localize(start_datetime,is_dst=None)
start_structtime = start_local.timetuple()
start_timestamp = int(time.mktime(start_structtime)*1000)

In [11]: start_timestamp
Out[11]: 1464278400000
```

---

趣味小知识：
UTC是什么的缩写？戳[维基UTC](https://zh.wikipedia.org/wiki/%E5%8D%8F%E8%B0%83%E4%B8%96%E7%95%8C%E6%97%B6)结果发现因为英文表示（Coordinated Universal Time）和法文表示（Temps Universel Coordonné）冲突导致缩写进行了妥协使用……

---



### 其他Python时间处理问题实例

#### 返回当前毫秒级别时间戳

```python
>>> import time
>>> current_milli_time = lambda: int(round(time.time()*1000))
>>> current_milli_time()
1464338590155
```

#### 时间戳转换成字符串

```python
>>> import datetime
>>> print(datetime.datetime.fromtimestamp(1464338590.155).strftime('%Y-%m-%d %H:%M:%S'))
2016-05-27 16:43:10
```

值得注意的是，`.fromtimestamp()`这个方法在本地执行的时候，会把填入的时间戳转换为本地时间，如果需要确定的获得utc的时间，则直接使用`.utcfromtimestamp()`













