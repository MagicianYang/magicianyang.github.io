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

不过里面有句话可以参考借鉴

> As the size of our database grows, so will the time it takes to compare all hashes. Eventually, our database of hashes will reach such a size where this linear comparison is not practical.

> The solution, while outside the scope of this post, is to utilize K-d trees and VP trees to reduce the complexity of the search problem from linear to sub-linear.

### 基于颜色直方图构建特征值

这就是上次的博文实践的那个方法了,[用Python和OpenCV创建一个图片搜索引擎的完整指南](http://python.jobbole.com/80860/)，原文戳[这里](http://www.pyimagesearch.com/2014/12/01/complete-guide-building-image-search-engine-python-opencv/)


### 索引建立以及查找优化

#### KD Tree

关于KD树的讲解可以看[这篇博客](http://blog.csdn.net/v_july_v/article/details/8203674)

KD Tree的python代码实现可以直接套用scipy包里面scipy.spatial.KDTree

戳[文档](http://docs.scipy.org/doc/scipy-0.18.0/reference/generated/scipy.spatial.KDTree.html#scipy-spatial-kdtree)，目前最新版为0.18.0，戳[源代码](https://github.com/scipy/scipy/blob/v0.18.0/scipy/spatial/kdtree.py#L176)

文档和代码还是看最新的好，之前看0.14版本里面还没有cKDTree的实现（所以没测试过），而且0.14版本的代码里面L437示例代码有点小问题。

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

实践的时候大概1000张图建Tree就耗费600M内存，到2500张时要2600M，到4000张就变成6000M了完全吃不住。

于是调研了别的方法。发现了一个词汇是LSH（locality sensitive hashing）局部敏感哈希。

（在看Machine Learning in Action里面hadoop实践那章节的时候发现的）

然后在[这篇博客](http://www.pyimagesearch.com/2014/09/15/python-compare-two-images/)里面发现也提及这个名词，感觉算是走对路子。

[RPForest](http://developers.lyst.com/2015/07/10/ann/)

[ANN](http://scikit-learn.org/stable/modules/neighbors.html#approximate-nearest-neighbors)

[LSHForest](http://scikit-learn.org/stable/modules/generated/sklearn.neighbors.LSHForest.html#sklearn.neighbors.LSHForest)

[Annoy](https://github.com/spotify/annoy) (Approximate Nearest Neighbors Oh Yeah)






