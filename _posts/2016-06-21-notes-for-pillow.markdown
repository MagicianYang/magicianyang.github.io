---
layout: post
section-type: post
title: Pillow相关图片处理
category: tech
tags: [ 'python','pillow' ]
---

### 问题解决

#### 使用Image.open()报错

1.首先引入时必须写为

```python
from PIL import Image
```

2.出现类似以下报错

```
OSError: cannot identify image file <_io.BytesIO object at 0x7fb113ed0ac8>
```

针对webp格式文件出现这个报错，参考[这里](https://github.com/python-pillow/Pillow/issues/1502#issuecomment-150771825)

虽然目前版本的Pillow如3.0已经支持webp格式，但是需要检查`libwebp-dev`版本

建议是——

> Try installing from source, you're going to want the 0.4.3 version. You may need to reinstall pillow afterwards, making sure that you're not installing from the pip cache. See the install docs for the details. http://pillow.readthedocs.org/en/latest/installation.html

跟这个issue题主遇到的问题相同的是同样是Ubuntu 12.04的测试机系统太老，导致安装的`libwebp-dev`版本太低。因此代码在本机系统能测试成功但是到了测试机就不行，会出现上面的报错。

官方Pillow文档中有这么一段——

> libwebp provides the WebP format.

> Pillow has been tested with version 0.1.3, which does not read transparent WebP files. Versions 0.3.0 and above support transparency.

安装流程参考以前的[博客文章](https://magicianyang.github.io/tech/2016/03/22/django-simple-captcha.html)应该没有问题

并可以参考[webp官方文档](https://developers.google.com/speed/webp/docs/compiling#building)