---
layout: post
section-type: post
title: Django中添加验证码
category: tech
tags: [ 'Python','OpenCV' ]
---
### 安装Pillow

环境：Ubuntu14.04LTS,Python3.4,Django1.8

#### The _imagingft C module is not installed报错解决

[尝试1](https://pillow.readthedocs.org/en/latest/installation.html)：官方文档

Prerequisites:

Debian or Ubuntu:

```
$ sudo apt-get install python-dev python-setuptools
```

注意python3需要（重点）

```
sudo apt-get install python3-dev python3-setuptools
```

Ubuntu 14.04 LTS(重点)

```
$ sudo apt-get install libtiff5-dev libjpeg8-dev zlib1g-dev \
    libfreetype6-dev liblcms2-dev libwebp-dev tcl8.6-dev tk8.6-dev python-tk
```

[尝试2](http://stackoverflow.com/questions/4011705/python-the-imagingft-c-module-is-not-installed)[尝试3](http://stackoverflow.com/questions/32942861/error-installing-pillow-on-ubuntu-14-04)：stackoverflow等问题回答


Ubuntu 14.04

```
sudo apt-get install libfreetype6-dev
pip uninstall pillow
pip install --no-cache-dir pillow
```

还有就是

```
sudo ln -s /usr/lib/x86_64-linux-gnu/libz.so /usr/lib/libz.so
sudo ln -s /usr/lib/x86_64-linux-gnu/libjpeg.so /usr/lib/libjpeg.so
sudo ln -s /usr/lib/x86_64-linux-gnu/libfreetype.so /usr/lib/libfreetype.so
```

[验证](http://effbot.org/zone/pil-imaging-not-installed.htm):

下面这种情况应该就没问题了

```
Python 3.4.3 (default, Oct 14 2015, 20:28:29) 
[GCC 4.8.4] on linux
Type "help", "copyright", "credits" or "license" for more information.
(InteractiveConsole)
>>> from PIL import Image
>>> import _imaging
Traceback (most recent call last):
  File "<console>", line 1, in <module>
ImportError: No module named '_imaging'
>>> from PIL import _imaging
>>> Image.init()
1
>>> Image.SAVE.keys()
dict_keys(['HDF5', 'BUFR', 'PNG', 'MPO', 'GRIB', 'PDF', 'MSP', 'XBM', 'TIFF', 'PPM', 'PALM', 'ICO', 'FITS', 'IM', 'PCX', 'JPEG2000', 'EPS', 'GIF', 'SPIDER', 'TGA', 'WEBP', 'JPEG', 'WMF', 'BMP'])
```

而且也可以在路径`/.env34/lib/python3.4/site-packages/PIL`里面找到文件`_imagingft.cpython-34m.so`，这时候应该就正常了

