---
layout: post
section-type: post
title: Notes for Naive Bayes
category: tech
tags: [ 'machine learning','classification' ]
---
*"The moral is, always try the simple things first."*
*-- Data Mining-Practical Machine Learning Tools and Techniques*

### Some Tips for Naive Bayes

#### 1.suitable situations
>This method goes by the name of Naïve Bayes because it’s based on Bayes’ rule
and “naïvely” assumes **independence**—it is only valid to multiply probabilities when
the events are independent. The assumption that attributes are independent (given
the class) in real life certainly is a simplistic one.


### Text Classification using Naive Bayes

#### 1.*Multinomial Naive Bayes* for text classification
See [Stanford's example](https://web.stanford.edu/class/cs124/lec/naivebayes.pdf)


#### 2.practice in python
[scikit-learn](https://github.com/scikit-learn/scikit-learn) is an open source machine learning library for the Python programming language.

And we can find the code for *Multinomial Naive Bayes* [here](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/naive_bayes.py). And the documentation [here](http://scikit-learn.org/stable/modules/generated/sklearn.naive_bayes.MultinomialNB.html)

Before using *class MultinomialNB* for text classification, we have to realize that：

+ Machine does not care about the real words in your text, it cares about **the number of occurrences of each word**
+ *fit(X, y, sample_weight=None)* -- The training data contains three parts
  + the number of occurrences of each word in the sample -- is X
  + the class which the sample belongs to -- is y
  + the number of samples for one class / the total number of samples -- is sample_weight
+ *predict(X)* -- The test data contains the number of occurrences of each word in the sample that we want to pridict

fit(X, y, sample_weight=None)需要的X是个稀疏矩阵（二维数组），里面是样本的词频。构造时注意格式，X矩阵每行都是一份训练样本，每份样本对应y中相应的一个值（类），所以y是个一维数组，注意和X中样本的顺序对应，而sample_weight则是样本权重，因为可能不同类输入的训练样本个数不一样（相当于先验概率）。

predict(X)需要的X样式和前面的训练样本一样，但是只有一份（一行），所以是个一维数组，注意构造时需要用和前面fit()里面一样的词汇表进行对应排序。

下面是生成词频矩阵的实例，需要提前对样本进行分词整合，同时对生成需要预测分类的样本的词频数组也有效

```python
from collections import Counter
import numpy as np
from utils import uniqifier
def get_train_array(train_data, vocabulary=None):
    """
    输入多个样本，输出样本的词汇计数的稀疏矩阵
    :param train_data: 输入样本为已经分词的样本列表
    :param vocabulary: 现有词汇表，没有的话就从样本中统计
    :return: 稀疏矩阵
    """
    all_data = []
    train_array = []

    for t in train_data:
        all_data += t

    if not vocabulary:
        # 得到全部样本的词汇表
        seen = set()
    	seen_add = seen.add
        vocabulary = [x for x in all_data if not (x in seen or seen_add(x))]

    # 统计每个样本的词频
    for t in train_data:
        count_words = Counter(t)
        sample_count = []
        for word in vocabulary:
            count_w = count_words.get(word, 0)
            sample_count.append(count_w)
        train_array.append(np.array(sample_count))
    train_array = np.array(train_array, ndmin=2)
    return train_array, vocabulary
```


### References
1.< Data Mining-Practical Machine Learning Tools and Techniques(3rd) >



