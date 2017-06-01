---
layout: post
section-type: post
title: 重读91条建议
category: tech
tags: [ 'python' ]
toc: show
---

此为重读《编写高质量代码 改善python程序的91个建议》的部分总结，持续更新反思中……

#### tip1 Pythonic概念

python的特色

```
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

关于with的理解[戳](https://sdqali.in/blog/2012/07/09/understanding-pythons-with-statement/)
或者[戳](https://www.ibm.com/developerworks/cn/opensource/os-cn-pythonwith/)

#### tip3 python与C语言不同

1.不用{}而严格缩进
2.单引号和双引号不区分使用
3.使用`X if C else Y`来代替`C?X:Y`
