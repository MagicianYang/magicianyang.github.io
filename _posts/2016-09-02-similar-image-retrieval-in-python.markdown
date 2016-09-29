---
layout: post
section-type: post
title: 相似图片查找调研
category: tech
tags: [ 'python','opencv' ]
toc: show
---
_此博客基于之前博文“[OPENCV的安装与实践](https://magicianyang.github.io/tech/2016/03/16/install-opencv.html)”继续调研_

## 相似图查找方法相关调研

### 基于图片指纹（Fingerprinting Images）

具体方式见博文[利用图片指纹检测高相似度图片](http://python.jobbole.com/81277/)

缺陷：只能查找那种缩略图和原图之类的极高相似度的图片，实用价值一般

不过里面有句话可以参考借鉴，之后图片特征值比较的时候是因为这篇博客尝试使用了K-d Tree进行搜索

> As the size of our database grows, so will the time it takes to compare all hashes. Eventually, our database of hashes will reach such a size where this linear comparison is not practical.

> The solution, while outside the scope of this post, is to utilize K-d trees and VP trees to reduce the complexity of the search problem from linear to sub-linear.

### 基于颜色直方图构建特征值

这就是上次的博文实践的那个方法了,[用Python和OpenCV创建一个图片搜索引擎的完整指南](http://python.jobbole.com/80860/)，原文戳[这里](http://www.pyimagesearch.com/2014/12/01/complete-guide-building-image-search-engine-python-opencv/)

使用颜色直方图方法比较古老但是比较简便，文章中特征值计算的方法基本可以直接用，根据项目本身所做调整有两点：

1.色相h，饱和度s，明度v的取值调整

2.使用opencv划分区域的调整

考虑到项目本身是黑白表情居多，和文章中需求的风景照不同，色相取值调小，明度取值调高。暂时取（4，6，12）

因为表情图片大部分是四周留白的特点，所以分割区域抛弃原文章的椭圆中心四角分割划分四块的方法，直接取中间椭圆，并将椭圆十字分割四块计算特征值。

于是特征向量长度为4*6*12*4，特征向量太短会导致查询结果不准，太长的话索引建立会复杂搜索会非常慢，所以暂时取了这个值

### 索引建立以及查找优化

原文章中作者使用卡方检定来挨个对比搜索图和数据库里面的图的特征向量，但是一旦数据库过大，就不能使用这样的线性方式解决查找问题。所以必须提前建立好索引，然后每次查找的时候去搜索这个索引。

一开始没想去建立索引，本想尝试hadoop，然后并行处理。但是发现hadoop是一个大坑不好搞，更重点的是搞hadoop不如先优化搜索算法。之前线性对比实在太糟糕。于是先尝试的之前那个图片指纹里面提及的KD Tree的次线性搜索。

#### KD Tree

关于KD树的讲解可以看[这篇博客](http://blog.csdn.net/v_july_v/article/details/8203674)

KD Tree的python代码实现可以直接套用scipy包里面scipy.spatial.KDTree

戳[文档](http://docs.scipy.org/doc/scipy-0.18.0/reference/generated/scipy.spatial.KDTree.html#scipy-spatial-kdtree)，目前最新版为0.18.0，戳[源代码](https://github.com/scipy/scipy/blob/v0.18.0/scipy/spatial/kdtree.py#L176)

文档和代码还是看最新的好，之前看0.14版本里面还没有cKDTree的实现（所以没测试过，不知道是不是更快），而且0.14版本的代码里面L437示例代码有点小问题。

不过这个KD Tree实现之后想要存储进某个地方是有问题的，直接Pickle不可以。出现了报错如下

```python
_pickle.PicklingError: Can't pickle <class XXX>
```

关于这个问题的通用解答戳[这个问题](http://stackoverflow.com/questions/4677012/python-cant-pickle-type-x-attribute-lookup-failed)，针对KD Tree存储的解决则戳[这个问题](http://stackoverflow.com/questions/5773216/saving-kdtree-object-in-python)


同时可以看看scipy-cookbook的[KDtree例子](http://scipy-cookbook.readthedocs.io/items/KDTree_example.html)


然后在那个机器学习的包sklearn里面也有KD Tree的实现，是用cpython写的

戳[文档](http://scikit-learn.org/stable/modules/generated/sklearn.neighbors.KDTree.html#sklearn.neighbors.KDTree)和[文档](http://scikit-learn.org/stable/modules/neighbors.html#k-d-tree)

不过我使用sklearn的代码构建之后，总是报错`segmentation fault`，出不来结果，没有明确原因，不知道是因为树太大内存不够还是跟cpython运行有关

#### ANN

但是KD Tree实际上对于海量高维度搜索就很无力，首先是文档指出高于20维就速度变慢，还有一个严重问题就是内存消耗巨大。

实践的时候大概1000张图建Tree就耗费600MB内存，到2500张时要2600MB，到4000张就变成6000MB了完全吃不住。然后程序就会被系统残忍Killed。

于是调研了别的方法。发现了一个词汇是LSH（locality sensitive hashing）局部敏感哈希。

（在看Machine Learning in Action里面hadoop实践那章节的时候发现的）

然后在[这篇博客](http://www.pyimagesearch.com/2014/09/15/python-compare-two-images/)里面发现也提及这个名词，感觉算是走对路子。

在查LSH的时候查到了下列名词

[RPForest](http://developers.lyst.com/2015/07/10/ann/)

[ANN](http://scikit-learn.org/stable/modules/neighbors.html#approximate-nearest-neighbors)

[LSHForest](http://scikit-learn.org/stable/modules/generated/sklearn.neighbors.LSHForest.html#sklearn.neighbors.LSHForest)

[Annoy](https://github.com/spotify/annoy) (Approximate Nearest Neighbors Oh Yeah)

最后选择了annoy来建立索引树。

当时代码虽然没有最终确定，但是大致做了个内存消耗对比（使用和KD Tree内存计算同样的方法，进行粗略统计），8000张图片消耗502MB内存，100000图片消耗4991MB内存，优化效果明显。

不过annoy个人感觉有个坑，在项目里面使用的时候，测试建立索引树，那个`a.add_item(i, v)`里面参数i直接用range()按序生成的，然后实际应用的时候为了方便，就直接用项目数据库里面图片ID了，然后出现了100000图片的树无法建立的情况（测试机器配置CPU：4核，内存：8192MB）

后来才发现文档里面说的很清楚

> a.add_item(i, v) adds item i (any nonnegative integer) with vector v. Note that it will allocate memory for max(i)+1 items.

所以最好还是别用自己的id，而是单独建立个关系表，然后把所有参与建树的图片都从1到n依次紧密编号，这样能够最大效果利用内存。

还有个问题在展示搜索结果的时候才发现，搜索结果不准确……`a.build(n_trees)`的参数一开始一直使用的是示例里面的10，于是尝试了修改到100棵树……改进效果极其明显……






