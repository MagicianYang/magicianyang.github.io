---
layout: post
section-type: post
title: Python中与图片相关的一些基础操作
category: tech
tags: [ 'python' ]
toc: show
---

#### 获取图片类型

使用[imghdr](https://docs.python.org/2/library/imghdr.html#imghdr.what)

```python
import imghdr

# 本地图片类型获取方法一
f_type = imghdr.what('/path/of/your/image/image_name')
# 本地图片类型获取方法二，注意第二个参数是图片的byte stream，第一个参数会被自动忽略
with open('/path/of/your/image/image_name','rb') as f:
    f_type=imghdr.what('fake_path',f.read())
```

以上两种方式对本地图片操作或许没什么意义（主要指方法二），不过对云端取出的图片来说一般都使用第二种，因为图片的比特流可能直接传入等等。

对于python 3.4，`imghdr`不能识别出google的新型图片格式`webp`，解决方式参考[这里](http://stackoverflow.com/questions/28085271/how-to-identify-webp-image-type-with-python)

```python
try:
    imghdr.test_webp
except AttributeError:
    # add in webp test, see http://bugs.python.org/issue20197
    def test_webp(h, f):
        if h.startswith(b'RIFF') and h[8:12] == b'WEBP':
            return 'webp'

    imghdr.tests.append(test_webp)
```

加上此段代码之后就可以正常识别.webp文件格式

_environment:Python3.4, Pillow_

#### 获取图片长宽

本地文件

```python
import os
def upload_pic_size(file):
    file_size = os.stat(file).st_size
    print(file_size)
    with Image.open(file) as im:
        width, height = im.size
        print(width)
        print(height)
    return width, height, file_size
```

云端文件

```python
import io
from PIL import Image
def get_image_size(self):
	if self._file_path:
    	file_size = self._image_file_object.size
        try:
        	im_file = io.BytesIO(self._image_file_object.read())
            with Image.open(im_file) as im:
                width, height = im.size
            return width, height, file_size
        except (OSError, IOError) as e:
            return 0, 0, file_size
```


