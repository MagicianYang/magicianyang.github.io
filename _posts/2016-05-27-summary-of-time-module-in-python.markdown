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