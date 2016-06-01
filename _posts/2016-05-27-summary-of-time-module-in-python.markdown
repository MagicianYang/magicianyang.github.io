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

[返回当前毫秒级别时间戳](http://stackoverflow.com/questions/5998245/get-current-time-in-milliseconds-in-python)

```python
>>> import time
>>> current_milli_time = lambda: int(round(time.time()*1000))
>>> current_milli_time()
1464338590155
```


2.struct

3.datetime形式

4.字符串形式

### 四种形式的相互转换

```
start_datetime = datetime.datetime.strptime(start_str,'%Y-%m-%d %H:%M:%S')
```

#### 时间戳转换成字符串

```python
>>> import datetime
>>> print(datetime.datetime.fromtimestamp(1464338590.155).strftime('%Y-%m-%d %H:%M:%S'))
2016-05-27 16:43:10
```

值得注意的是，`.fromtimestamp()`这个方法在本地执行的时候，会把填入的时间戳转换为本地时间，如果需要确定的获得utc的时间，则直接使用`.utcfromtimestamp()`

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

### 其他Python时间处理问题实例