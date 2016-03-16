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

```Shell
$ sudo apt-get update
$ sudo apt-get upgrade
```

#### 安装用来编译OpenCV的工具包

```Shell
$ sudo apt-get install build-essential cmake git pkg-config
```

*注意此处cmake的安装其实不必要，因为直接安装的库里面的cmake版本总是很老，所以之后我们需要手动安装，后面会有安装指导。*

#### 安装其他相关必要或者有帮助的软件包以及lib

```Shell
$ sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
$ sudo apt-get install libgtk2.0-dev
$ sudo apt-get install libatlas-base-dev gfortran
```

### 2.搭建虚拟环境和安装Python3.4

#### 没搭建过虚拟环境的话需要预先安装

pip

```Shell
$ wget https://bootstrap.pypa.io/get-pip.py
$ sudo python3 get-pip.py
```

virtualenv和virtualenvwrapper

```Shell
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

```Shell
$ mkvirtualenv .cv
```

命令行进入虚拟环境 .cv

```Shell
$ source .cv/bin/activate
```

安装Python&NumPy

```Shell
$ sudo apt-get install python3.4-dev
$ pip install numpy
```

### 3.CMake安装

在按照那个保姆级别指南的步骤进行第三步前，我们还是重装一下最新版本的CMake吧
此步骤非常非常重要。具体安装参考此[博文](http://yjyj.net/learn/centos/4606.html)

卸载老版本

```Shell
$ sudo apt-get autoremove cmake
```

下载新版本，现阶段最高版本3.5

```Shell
$ cd /usr/local
$ wget http://www.cmake.org/files/v3.5/cmake-3.5.0-Linux-i386.tar.gz
```

解压

```Shell
$ tar zxvf cmake-3.5.0-Linux-i386.tar.gz
```

创建链接

```Shell
$ ln -s /usr/local/cmake-2.8.9-Linux-i386/bin/* /usr/bin/
```

此时安装完成可以查看版本

```Shell
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

```Shell
$ wget http://www.hdfgroup.org/ftp/HDF5/current/src/hdf5-1.8.16.tar
```

之后解压编译安装

```Shell
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

```Shell
$ cd ~
$ git clone https://github.com/Itseez/opencv.git
$ cd opencv
$ git checkout 3.1.0
```

opencv_contrib

```Shell
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

```Shell
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







