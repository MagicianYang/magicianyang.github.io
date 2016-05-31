---
layout: post
section-type: post
title: Python的时间处理总结
category: tech
tags: [ 'django','python' ]
toc: show
---

### 时间的四种形式

1.时间戳

2.struct

3.datetime形式

4.字符串形式

### 四种形式的相互转换

```
start_datetime = datetime.datetime.strptime(start_str,'%Y-%m-%d %H:%M:%S')
```

### 特定时间

1.now

```
>>> import datetime
>>> a=datetime.datetime.today()
>>> a
datetime.datetime(2016, 5, 31, 11, 35, 3, 660797)
>>> print(a)
2016-05-31 11:35:03.660797
>>> b=datetime.datetime.now()
>>> b
datetime.datetime(2016, 5, 31, 15, 43, 21, 269203)
```

```

```