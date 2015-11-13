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

```
def convert_timezone(time_in):
    time_utc = time_in.replace(tzinfo=pytz.timezone('UTC'))
    time_local = time_utc.astimezone(pytz.timezone('Asia/Shanghai'))
    return time_local
```

```Python
def convert_local_timezone(time_in):
    local = pytz.timezone('Asia/Shanghai')
    local_dt = local.localize(time_in, is_dst=None)
    time_utc = local_dt.astimezone(pytz.utc)
    return time_utc
```



