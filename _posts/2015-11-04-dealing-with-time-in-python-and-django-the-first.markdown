---
layout: post
title:  "Django中的时间处理总结（1）"
date:   2015-11-04 18:10:00
categories: Django
---

## About Timezone
### Settings
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
### The rules which we should pay attention
**1.Store data in UTC in our database**
