---
layout: post
title:  "Django中的时间处理总结（1）"
date:   2015-11-04 18:10:00
categories: Django
---

### About Timezone

#### Settings

Enable the time zone support...

in settings.py

```
TIME_ZONE = 'Asia/Shanghai'

USE_TZ = True
```

And installing pytz is highly recommended

```
$ pip install pytz
```

#### The rules which we should pay attention

**Store data in UTC in our database**

### Examples
```python
def convert_timezone(time_in):
    """
    用来将系统自动生成的datetime格式的utc时区时间转化为本地时间
    :param time_in: datetime.datetime格式的utc时间
    :return:输出仍旧是datetime.datetime格式，但已经转换为本地时间
    """
    time_utc = time_in.replace(tzinfo=pytz.timezone('UTC'))
    time_local = time_utc.astimezone(pytz.timezone('Asia/Shanghai'))
    return time_local
```

```python
def convert_local_timezone(time_in):
    """
    用来将输入的datetime格式的本地时间转化为utc时区时间
    :param time_in: datetime.datetime格式的utc时间
    :return:输出仍旧是datetime.datetime格式，但已经转换为utc时间
    """
    # time_local = time_in.replace(tzinfo=pytz.timezone('Asia/Shanghai'))
    # time_utc = time_local.astimezone(pytz.timezone('UTC'))

    local = pytz.timezone('Asia/Shanghai')
    local_dt = local.localize(time_in, is_dst=None)
    time_utc = local_dt.astimezone(pytz.utc)
    return time_utc
```



