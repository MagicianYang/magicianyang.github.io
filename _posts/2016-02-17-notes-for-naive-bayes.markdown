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



### References
1.< Data Mining-Practical Machine Learning Tools and Techniques(3rd) >



