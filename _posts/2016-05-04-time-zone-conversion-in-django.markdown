---
layout: post
section-type: post
title: Django中的时区转换&Python的时间处理
category: tech
tags: [ 'django','python' ]
toc: show
---


### Django中时区问题

#### 几点建议

1.设置`USE_TZ = True`并且设置`TIME_ZONE = 'Asia/Shanghai'`

注意要求安装python的包`pytz`

2.以第一条为前提，需要遵守的原则是——__所有存入model的时间一律先转换为utc时间再存储__，而通过`auto_now`以及`auto_now_add`自动生成的时间一定是utc时间，而且存入model的时间再取出之后的时区并不是naive的而是已经设定时区为utc的，例如从数据库中取出时间显示如下

```python
>>> a.time
datetime.datetime(2016, 5, 11, 6, 49, 18, tzinfo=<UTC>)
```

3.推荐使用django自带模块[django.utils.timezone](https://docs.djangoproject.com/es/1.9/ref/utils/#module-django.utils.timezone)进行时间处理

比较好的应用实例，在建表时遇到取当前时间默认值时，之前有如下写法——

```django
from django.utils.datetime_safe import datetime
class X(models.Model):
	models.DateTimeField(null=False, default=datetime.utcnow)
```

而现在可以用

```django
from django.utils import timezone
class X(models.Model):
	models.DateTimeField(null=False, default=timezone.now)
```

特别注意设置`USE_TZ = True`之后，`timezone.now()`得到的datetime格式时间是aware的并且是UTC时间。以上两种方式都可以使用。

同样获得日期的话也可以用同样的方法。另外先举个例子说明为什么`default=datetime.utcnow`这里后面不加括号

反例：

```django
today = models.DateField(null=False, default=date.today())
```

在django1.8中这条在migrations里面生成的信息变成了

```django
('today', models.DateField(default=datetime.date(2016, 5, 31)))
```

也就是哪天建表，这个默认日期就是哪天了，而不是写表的日期。

关于这点的解释有人在stack-overflow上面[说过](http://stackoverflow.com/a/2771701/5256607)——

> By passing datetime.now without the parentheses, you are passing the actual function, which will be called each time a record is added. If you pass it datetime.now(), then you are just evaluating the function and passing it the return value.

同样可以用timezone里面的方法

```django
today = models.DateField(null=False, default=timezone.now)
```

这里有点疑问，如果在django里面定义一个方法如下

```django
def time_now():
    a = datetime.datetime.now()
    print(a)
    b = datetime.datetime.today()
    print(b)
    c = timezone.now()
    print(c)
```

其执行结果是
```python
2016-05-31 17:30:37.130926
2016-05-31 17:30:37.131008
2016-05-31 09:30:37.131042+00:00
```

然而调用datetime.datetime.now存入数据库的却是utc时间

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














