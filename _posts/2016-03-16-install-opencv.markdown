---
layout: post
section-type: post
title: OpenCV的安装与实践
category: tech
tags: [ 'Python','OpenCV' ]
---
## OpenCV的安装

为了实践[用Python和OpenCV创建一个图片搜索引擎的完整指南](http://python.jobbole.com/80860/)，
([原文地址](http://www.pyimagesearch.com/2014/12/01/complete-guide-building-image-search-engine-python-opencv/)戳)
需要安装OpenCV，以下是安装流程以及报错解决。

安装环境是基于Ubuntu 14.04LTS和Python3.4，进行OpenCV 3.1.0的安装

具体安装参考文章[Install OpenCV 3.0 and Python 3.4+ on Ubuntu](http://www.pyimagesearch.com/2015/07/20/install-opencv-3-0-and-python-3-4-on-ubuntu/)来进行比较靠谱，OpenCV官网的安装指南缺失东西太多，比较坑爹，尤其是不要去网上找旧版本的安装指南

另外有一篇同样保姆级别的安装文章是针对Python2.7+的，见[Install OpenCV 3.0 and Python 2.7+ on Ubuntu](http://www.pyimagesearch.com/2015/06/22/install-opencv-3-0-and-python-2-7-on-ubuntu/)

以下将会摘录重要安装步骤

### Table of Contents

**[1.预先准备，安装各种依赖](#1.预先准备，安装各种依赖)**

**[2.搭建虚拟环境和安装Python3.4](#2.搭建虚拟环境和安装Python3.4)**

**[3.CMake安装](#3.CMake安装)**

**[4.HDF5安装](#4.HDF5安装)**

**[5.OpenCV安装](#5.OpenCV安装)**


### 1.预先准备，安装各种依赖

#### 升级系统环境

```
$ sudo apt-get update
$ sudo apt-get upgrade
```

#### 安装用来编译OpenCV的工具包

```
$ sudo apt-get install build-essential cmake git pkg-config
```

*注意此处cmake的安装其实不必要，因为直接安装的库里面的cmake版本总是很老，所以之后我们需要手动安装，后面会有安装指导。*

#### 安装其他相关必要或者有帮助的软件包以及lib

```
$ sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
$ sudo apt-get install libgtk2.0-dev
$ sudo apt-get install libatlas-base-dev gfortran
```

### 2.搭建虚拟环境和安装Python3.4

#### 没搭建过虚拟环境的话需要预先安装

pip

```
$ wget https://bootstrap.pypa.io/get-pip.py
$ sudo python3 get-pip.py
```

virtualenv和virtualenvwrapper

```
$ sudo pip3 install virtualenv virtualenvwrapper
```

注意需要修改文件`~/.bashrc`

```
# virtualenv and virtualenvwrapper
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
export WORKON_HOME=$HOME/.virtualenvs
source /usr/local/bin/virtualenvwrapper.sh
```

#### 虚拟环境搭建

新的环境可以在Pycharm里面Settings的Project Interpreter直接对工程直接创建，记得选Python3.4
或者命令行创建

```
$ mkvirtualenv .cv
```

命令行进入虚拟环境 .cv

```
$ source .cv/bin/activate
```

安装Python&NumPy

```
$ sudo apt-get install python3.4-dev
$ pip install numpy
```

### 3.CMake安装

在按照那个保姆级别指南的步骤进行第三步前，我们还是重装一下最新版本的CMake吧
此步骤非常非常重要。具体安装参考此[博文](http://yjyj.net/learn/centos/4606.html)

卸载老版本

```
$ sudo apt-get autoremove cmake
```

下载新版本，现阶段最高版本3.5

```
$ cd /usr/local
$ wget http://www.cmake.org/files/v3.5/cmake-3.5.0-Linux-i386.tar.gz
```

解压

```
$ tar zxvf cmake-3.5.0-Linux-i386.tar.gz
```

创建链接

```
$ ln -s /usr/local/cmake-2.8.9-Linux-i386/bin/* /usr/bin/
```

此时安装完成可以查看版本

```
$ cmake --version
$ cmake version 3.5.0
```

### 4.HDF5安装

什么？还不能安装OpenCV？！是的，因为如果你现在就去装，编译到百分之九十四的时候你会发现……

```
fatal error: hdf5.h: no such file or directory
```

(╯°Д°)╯︵ ┻━┻等了半天就给我看这个！！！hdf5是个什么鬼玩意儿

当然网上对于这个报错的解决貌似都没有什么用，例如[这个](https://github.com/NVIDIA/DIGITS/issues/156)
又或者是[这个](https://github.com/PacificBiosciences/pbcore/issues/5)

是因为这个东西根本没装吧……所以以下是安装

去[HDF5官网](https://www.hdfgroup.org/HDF5/)下一份或者用命令行随便下个老版本

```
$ wget http://www.hdfgroup.org/ftp/HDF5/current/src/hdf5-1.8.16.tar
```

之后解压编译安装

```
$ tar -xzf hdf5-1.8.16.tar.gz
$ cd hdf5-1.8.16
$ mkdir build
$ cd build
$ cmake ..
$ make
$ sudo make install
```

### 5.OpenCV安装

终于……继续看保姆级教程第三步……

#### 先从github下载最新的文件

目前是3.1.0版本的。**注意一个非常坑爹的问题，就是这个opencv源码和下面那个opencv_contrib源码都要直接在～这个目录下拖，不要随便在别的目录下拖。
否则编译的时候会出现找不到一些文件的状况**，这里绝对不能随意发挥

opencv

```
$ cd ~
$ git clone https://github.com/Itseez/opencv.git
$ cd opencv
$ git checkout 3.1.0
```

opencv_contrib

```
$ cd ~
$ git clone https://github.com/Itseez/opencv_contrib.git
$ cd opencv_contrib
$ git checkout 3.1.0
```

到这里又要停一下，为什么不能快马加鞭去编译呢？因为，呵呵，cmake的结果是，即使局部成功了，最后在python里面输入`import cv2`的时候会报错没有这个模块。

```
>>> import cv2
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ImportError: No module named cv2
```

也就是说这个OpenCV装了跟没装一样……
出现这个问题的原因是因为没有cv2.cpython-34m.so这个文件，有人问过这个问题[Why cv2.so missing after opencv installed?](http://stackoverflow.com/questions/15790501/why-cv2-so-missing-after-opencv-installed)，可惜下面的答案都不能解决实际问题。

我在cmake之后报告上面发现一个问题

![pic-149](/img/blog-pic/2016-03-16/Selection_149.jpg)

这个图和那个保姆级别教程里面的图对比，发现python3部分，只有Interpreter，没有Libraries和numpy

所以应该是因为OpenCV没有正常绑定python的libraries，导致了cv2.cpython-34m.so文件的缺失

于是我找到了这个问题[CMake can not find PythonLibs](http://askubuntu.com/questions/479260/cmake-can-not-find-pythonlibs)，这个[答案](http://askubuntu.com/a/515485)或者[这个](http://stackoverflow.com/a/28414440/5256607)解决了我的困扰。

**最终解决办法如下**

**打开opencv文件夹找到文件`CMakeLists.txt`，在前面加上一行`set(Python_ADDITIONAL_VERSIONS 3.4)`**

这时候我们回到正常步骤

```
$ cd ~/opencv
$ mkdir build
$ cd build
$ cmake -D CMAKE_BUILD_TYPE=RELEASE \
	-D CMAKE_INSTALL_PREFIX=/usr/local \
	-D INSTALL_C_EXAMPLES=OFF \
	-D INSTALL_PYTHON_EXAMPLES=ON \
	-D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib/modules \
	-D BUILD_EXAMPLES=ON ..
```

```
$ make -j4
```

```
$ sudo make install
$ sudo ldconfig
```

最后OpenCV已经被安装到了`/usr/local/lib/python3.4/site-packages/`，我们需要绑定一下路径

```
$ cd ~/code/vacation-image-search-engine/.cv/lib/python3.4/site-packages/
$ ln -s /usr/local/lib/python3.4/site-packages/cv2.cpython-34m.so cv2.so
```

这样就能用cv2来引入模块了，在虚拟环境测试一下

![pic-151](/img/blog-pic/2016-03-16/Selection_151.jpg)

拓展阅读
[在linux环境下编译运行OpenCV程序的两种方法](http://www.cnblogs.com/woshijpf/p/3840530.html)

## OpenCV的实践

### 代码调试

这个文章[用Python和OpenCV创建一个图片搜索引擎的完整指南](http://python.jobbole.com/80860/)中的代码部分调试时有这么几个报错。

#### cv2.ellipse

报错

```
TypeError: ellipse() takes at most 5 arguments (8 given)
```

问题见[<Python, openCV> How I can use cv2.ellipse?](http://stackoverflow.com/questions/18595099/python-opencv-how-i-can-use-cv2-ellipse)

[官方文档](http://docs.opencv.org/2.4/modules/core/doc/drawing_functions.html#ellipse)里面可以看到有两个同名函数,导致了这个问题

```
cv2.ellipse(img, center, axes, angle, startAngle, endAngle, color[, thickness[, lineType[, shift]]]) → None
cv2.ellipse(img, box, color[, thickness[, lineType]]) → None
```

由答案可知，这个实践里面代码运行环境应该是python2.7，所以原来代码中的除法得到的是int类型的数值，传入该函数之后，函数辨认为因此在python3.4下，除号应该改变，代码应该变更为

```Python
(axesX, axesY) = (int(w * 0.75) // 2, int(h * 0.75) // 2)
```


#### cv2.normalize

报错

```
Traceback (most recent call last):
  File "index.py", line 32, in <module>
    features = cd.describe(image)
  File "/home/wulinfang/code/vacation-image-search-engine/pyimagesearch/colordescriptor.py", line 43, in describe
    hist = self.histogram(image, cornerMask)
  File "/home/wulinfang/code/vacation-image-search-engine/pyimagesearch/colordescriptor.py", line 60, in histogram
    hist = cv2.normalize(hist).flatten()
TypeError: Required argument 'dst' (pos 2) not found
```

修改文件`colordesciptor.py`里面

```Python
hist = cv2.normalize(hist).flatten()
```

为

```Python
hist = cv2.normalize(hist, hist).flatten()
```

虽然[OpenCV官方文档2.4](http://docs.opencv.org/2.4/modules/core/doc/operations_on_arrays.html#normalize)上面没有要求第二个参数，但是3.1.0不加第二个参数不能运行。

### 代码应用流程（简要总结）

代码通过四个步骤实现图像的搜索：定义图像描述符，索引化数据集，定义相似矩阵，搜索

#### 1.定义图像描述符

通过`pyimagesearch.colordescriptor.py`里面定义的类`ColorDescriptor`实现。

目前作者将图片划分了五个区域，设定了计算对应区域的像素值占比的方法，主要是针对了风景照片的特点。

*此处可以根据自身需求进行修正。*

#### 2.索引化数据集

在`index.py`里面实现，对已有的相册中图片挨个套用**步骤1**的方法，生成每幅图的特征向量值，将整个相册的信息存入一个`.csv`文件中

通过命令行指令实现——

```
$ python index.py --dataset dataset --index index.csv
```

其中`--dataset`后面跟需要分析的存储图片路径，`--index`后面跟输出的`.csv`文件路径，要求存储的每个图片名必须唯一

最后可以用命令行查看成功索引化的图片数量

```
$ wc -l index.csv
```

*此处有关命令行指令可以根据实际需求应用*

#### 3.定义相似矩阵

在`pyimagesearch.searcher.py`中定义类`Searcher`,输入**步骤2**生成的`.csv`文件，调用里面的`search`方法时还需要再输入测试图片的特征值，之后会将测试图片和`.csv`文件的数据**逐行对比测试图和相册图的图像特征**，计算出的结果（图片相似度）存入字典。

最后对字典的值进行升序排序，相似度数值越高，表示两幅图像差别越大

该方法参数`limit`决定了输出结果图片的数量。

#### 4.搜索

用`search.py`进行整合调用，调用步骤1的图像特征值计算方法，对测试图片生成特征值。然后调用步骤3，输出对比结果，最后根据对比结果挨个进行匹配的图片读取展示。

命令行操作——

```
$ python search.py --index index.csv --query queries/108100.png --result-path dataset
```

其中`--index`后跟已生成的`.csv`文件路径，`--query`后跟测试图片路径，`--result-path`后跟相册图片路径，用来最后展示时使用











