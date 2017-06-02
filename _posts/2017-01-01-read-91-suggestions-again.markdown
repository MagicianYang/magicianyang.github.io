---
layout: post
section-type: post
title: 重读91条建议
category: tech
tags: [ 'python' ]
toc: show
---

此为重读《编写高质量代码 改善python程序的91个建议》的部分总结和结合自身经验的，持续更新反思中……

### tip1 Pythonic概念

python的特色

```python
# 变量交换
a, b = b, a
# 遍历容器
for i in alist:
	do_sth_with(i)
# 迭代器
with open(path, 'r') as f:
	do_sth_with(f)
# 列表逆序，作者认为第二种方法更pythonic
a='abcdefg'
print a[::-1]
print list(reversed(a))
#理解标准库，使用内置函数，如字符串格式化str.format()
'{greet} from {language}'.format(greet='hello',language='python')
```

注，字符串不用%而多用format，非常频繁的应用

关于with的理解[戳](https://sdqali.in/blog/2012/07/09/understanding-pythons-with-statement/)
或者[戳](https://www.ibm.com/developerworks/cn/opensource/os-cn-pythonwith/)


### tip3 python与C语言不同

1.不用{}而严格缩进

2.单引号和双引号不区分使用

3.使用`X if C else Y`来替代`C?X:Y`
注，此用法非常频繁

4.使用`if...elif...else`来替代`switch...case`

...

### tip6 函数编写

1.短小，嵌套层次不要过深，三层内为宜

2.参数设计简明，个数不宜过多

3.参数向下兼容，新版本增加参数时使用默认参数
注，版本迭代的时候会用到

4.一个函数只做一件事，保证函数语句粒度一致性

### tip7 常量集中到一个文件

注，实际应用中一般有const.py，其中最多的就是[枚举类型](https://docs.python.org/3/library/enum.html)

```python
from enum import Enum
class Color(Enum):
	RED = 1
	GREEN = 2
	BLUE = 3
```
































