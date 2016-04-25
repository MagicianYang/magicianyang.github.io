---
layout: post
section-type: post
title: Python中与图片相关的一些基础操作
category: tech
tags: [ 'python' ]
toc: show
---

_environment:Python3.4, Pillow_

#### 获取图片长宽

本地文件

```python
def upload_pic_size(file):
    file_size = os.stat(file).st_size
    print(file_size)
    with Image.open(file) as im:
        width, height = im.size
        print(width)
        print(height)
    return width, height, file_size****
```
