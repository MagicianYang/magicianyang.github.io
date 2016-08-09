---
layout: post
section-type: post
title: OpenCV的安装与实践
category: tech
tags: [ 'python','opencv' ]
toc: show
---
_(updated at 2016.08.09)_


## OpenCV的安装

为了实践[用Python和OpenCV创建一个图片搜索引擎的完整指南](http://python.jobbole.com/80860/)，
([原文地址](http://www.pyimagesearch.com/2014/12/01/complete-guide-building-image-search-engine-python-opencv/)戳)
需要安装OpenCV，以下是安装流程以及报错解决。

安装环境是基于Ubuntu 14.04LTS和Python3.4，进行OpenCV 3.1.0的安装
(由于第二次安装是在Ubuntu 12.04的测试机上面，遇到更多坑，所以会增补一些可能遇到的问题)

具体安装参考文章[Install OpenCV 3.0 and Python 3.4+ on Ubuntu](http://www.pyimagesearch.com/2015/07/20/install-opencv-3-0-and-python-3-4-on-ubuntu/)来进行比较靠谱，OpenCV官网的安装指南缺失东西太多，比较坑爹，尤其是不要去网上找旧版本的安装指南

另外有一篇同样保姆级别的安装文章是针对Python2.7+的，见[Install OpenCV 3.0 and Python 2.7+ on Ubuntu](http://www.pyimagesearch.com/2015/06/22/install-opencv-3-0-and-python-2-7-on-ubuntu/)

以下将会摘录重要安装步骤


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
$ ln -s /usr/local/cmake-3.5.0-Linux-i386/bin/* /usr/bin/
```

此时安装完成可以查看版本

```
$ cmake --version
$ cmake version 3.5.0
```

这里有一点需要注意，**如果是ubuntu12.04系统**，这里可能还是找不到cmake，虽然明明建立symbolic link了，却还是显示报错

```shell
bash: /usr/bin/cmake: No such file or directory
```

于是最后在网上找到一个类似[提问](http://askubuntu.com/q/193670)，解决方案是需要安装`ia32-libs`，安装见[这里](http://stackoverflow.com/a/23381781/5256607)，不过一般直接在命令行输入

```shell
sudo apt-get install ia32-libs
```

安装之后就能正常使用cmake了

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
$ wget http://www.hdfgroup.org/ftp/HDF5/current/src/hdf5-1.8.17.tar.gz
```

之后解压编译安装

```
$ tar -zxvf hdf5-1.8.17.tar.gz
$ cd hdf5-1.8.17
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
否则编译的时候会出现找不到一些文件的状况**，这里绝对不能随意发挥（其实应该是在cmake的时候可以指定路径，不过为了省去麻烦就……）

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

出现这个问题的原因是因为**没有cv2.cpython-34m.so这个文件**，有人问过这个问题[Why cv2.so missing after opencv installed?](http://stackoverflow.com/questions/15790501/why-cv2-so-missing-after-opencv-installed)，可惜下面的答案都不能解决实际问题。

我在cmake之后报告上面发现一个问题

![pic-149](/img/blog-pic/2016-03-16/Selection_149.jpg)

这个图和那个保姆级别教程里面的图对比，发现python3部分，只有Interpreter，没有Libraries和numpy

所以应该是因为OpenCV没有正常绑定python的libraries，导致了cv2.cpython-34m.so文件的缺失

于是我找到了这个问题[CMake can not find PythonLibs](http://askubuntu.com/questions/479260/cmake-can-not-find-pythonlibs)，这个[答案](http://askubuntu.com/a/515485)或者[这个](http://stackoverflow.com/a/28414440/5256607)解决了我的困扰。

**最终解决办法如下**

**打开opencv文件夹找到文件`CMakeLists.txt`，在前面加上一行`set(Python_ADDITIONAL_VERSIONS 3.4)`**

当时安装14.04系统的时候做到这步之后就能正常出现保姆级教程里面那张图那样，可以找到python3相关的lib和numpy的路径了，但是在装测试机的时候死活找不到

此时在网上搜索发现大家会手动在cmake中加入参数协助查找路径，见这里的[安装问题排查](http://qa.helplib.com/804955)

也就是说正常步骤进行cmake需要加一行，具体如下，可以与下面的指令进行对比，其中python library的路径最好确认一下，不过大致是这个位置

```shell
$ cd ~/opencv
$ mkdir build
$ cd build
$ cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D PYTHON_LIBRARY=/usr/lib/x86_64-linux-gnu/libpython3.4m.so \
    -D PYTHON_INCLUDE_DIR=/usr/include/python3.4m -DPYTHON_INCLUDE_DIR2=/usr/include/x86_64-linux-gnu/python3.4m \
    -D INSTALL_C_EXAMPLES=OFF \
    -D INSTALL_PYTHON_EXAMPLES=ON \
    -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib/modules \
    -D BUILD_EXAMPLES=ON ..
```

然而，标准结局，还是不行……最后仔细研究了一下报错

```python
-- Could NOT find PythonLibs: Found unsuitable version "3.4.5", but required is exact version "3.4.4" (found /usr/lib/x86_64-linux-gnu/libpython3.4m.so)
```

发现症结就在于，测试机的系统环境python3.4是3.4.5的版本号，而由于工程里面的虚拟环境创建稍早，python是3.4.4的，这样都不行！！！网上有个人也出了类似[问题](http://stackoverflow.com/questions/33082371/install-opencv3-0-with-python-3-4-3-under-virtual-environment-pyenv)。可以用下面两种方式检查当前python版本

```
$ python --version
$ /usr/bin/python3.4 -V
```

而第一次安装的机器上面就没有这个问题，两个位置的python都是3.4.3所以一次性通过了。

于是解决方法就只能是重新开个新的虚拟环境，其实在网上有人问过这种[问题](http://stackoverflow.com/questions/10218946/upgrade-python-in-a-virtualenv)但是没觉得回答的人有什么好的解决方法，所以就重装吧……

```shell
$ virtualenv --no-site-packages -p python3.4 /path/to/environment/.env345
$ pip install -r requirements.txt
```

这时候我们回到正常步骤（原14.04安装时的cmake操作），请务必记住此时的编译是要在开着虚拟环境的情况编译的，这样我们才能去绑定虚拟环境里面的python3

```shell
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

此步骤在测试机上面安装的时候遇到一个别的问题（目测是网不太好导致的），就是安装不上一个第三方插件

```
CMake Error at 3rdparty/ippicv/downloader.cmake:77 (message):
  ICV: Failed to download ICV package: ippicv_linux_20151201.tgz.
  Status=28;"Timeout was reached"
```

呵呵，解决方法就是手动下载，参考[这份教程](http://www.linuxfromscratch.org/blfs/view/svn/general/opencv.html)里面有个下载地址

```
$ wget  https://raw.githubusercontent.com/Itseez/opencv_3rdparty/81a676001ca8075ada498583e4166079e5744668/ippicv/ippicv_linux_20151201.tgz
```

但是下完之后又发现报错

```
CMake Warning at 3rdparty/ippicv/downloader.cmake:56 (message):
  ICV: Local copy of ICV package has invalid MD5 hash:
  d9b734c6f53500d00398aadfbf5e04c8 (expected:
  808b791a6eac9ed78d32a7666804320e)
```

诡异的hash校验不正确的问题，后来找了个可靠的下载地址解决了问题，参见[博客](http://www.cnblogs.com/nwpuxuezha/p/5312886.html)，提前下完之后把文件`ippicv_linux_20151201.tgz`丢到这个文件路径里面去`~/opencv/3rdparty/ippicv/downloads/linux-808b791a6eac9ed78d32a7666804320e`

这样cmake中间可能出现的错误大部分都排查了

编译成功之后，我们继续……

```
$ make -j4
```

这里在14.04系统没再遇上错误，但是12.04系统时，一开始是一到这个位置就停

![pic-189](/img/blog-pic/2016-03-16/Selection_189.jpg)

在一个[opencv安装教程](http://embedonix.com/articles/image-processing/installing-opencv-3-1-0-on-ubuntu/)里面找到了Common Problems

里面有个需要注意的地方——

> Old version of GCC! Please make sure you have a recent version of GCC (I recommend 4.8.X or higher)

这个安装教程下面评论里面有人放一些报错跟我遇到的类似，博主的建议就是检查gcc/g++版本

于是查看了一下测试机的版本`gcc -v`，发现版本是4.6，而当初在14.04系统直接装的就是4.8。所以需要手动重装gcc和g++

参见[安装指导](http://askubuntu.com/a/271561)或者[这个代码](https://gist.github.com/omnus/6404505)，**针对Ubuntu 12.04安装**

```shell
$ sudo apt-get install python-software-properties
$ sudo add-apt-repository ppa:ubuntu-toolchain-r/test
$ sudo apt-get update
$ sudo apt-get install gcc-4.8
$ sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.8 50

$ sudo apt-get install g++-4.8
$ sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-4.8 50
```

安装完了之后回头重新去cmake才行，不过默认还是使用gcc而不是gcc-8.4所以需要做个链接

```shell
$ sudo rm /usr/bin/g++
$ sudo ln -s /usr/bin/g++-4.XXX /usr/bin/g++
```

不过也可以选择不覆盖之前的gcc，参见[这里](http://stackoverflow.com/a/17275650/5256607)

```shell
$ export CC=/usr/local/bin/gcc
$ export CXX=/usr/local/bin/g++
```

重新操作又遇到别的报错了，报错大致如下

```
/home/www-data/opencv/modules/imgcodecs/src/grfmt_webp.cpp: In member function ‘virtual bool cv::WebPEncoder::write(const cv::Mat&, const std::vector<int>&)’:
/home/www-data/opencv/modules/imgcodecs/src/grfmt_webp.cpp:258:93: error: ‘WebPEncodeLosslessBGR’ was not declared in this scope
             size = WebPEncodeLosslessBGR(image->ptr(), width, height, (int)image->step, &out);
                                                                                             ^
/home/www-data/opencv/modules/imgcodecs/src/grfmt_webp.cpp:262:94: error: ‘WebPEncodeLosslessBGRA’ was not declared in this scope
             size = WebPEncodeLosslessBGRA(image->ptr(), width, height, (int)image->step, &out);
                                                                                              ^
make[2]: *** [modules/imgcodecs/CMakeFiles/opencv_imgcodecs.dir/src/grfmt_webp.cpp.o] Error 1
make[1]: *** [modules/imgcodecs/CMakeFiles/opencv_imgcodecs.dir/all] Error 2
make[1]: *** Waiting for unfinished jobs....
```

这个问题是libwebp-dev没有安装的原因，请去参考[官方文档](https://developers.google.com/speed/webp/docs/compiling#building)安装一下

之后……


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

这里也有个安装问题集锦[OpenCV Installation Troubleshooting Guide](http://www.ozbotz.org/opencv-install-troubleshooting/)

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











