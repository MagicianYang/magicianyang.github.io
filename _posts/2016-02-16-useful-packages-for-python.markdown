---
layout: post
section-type: post
title: Useful Packages for Python
category: tech
tags: [ 'python' ]
---

### 1.[SciPy](http://www.scipy.org)
#### [Install](http://www.scipy.org/install.html)
About the missing libraries -- BLAS and LAPACK,
it shows the error:
```
raise NotFoundError('no lapack/blas resources found')
numpy.distutils.system_info.NotFoundError: no lapack/blas resources found
```
So you cannot install SciPy without these libraries.

To solve the problem, go to [this](http://www.scipy.org/scipylib/building/linux.html#debian-ubuntu)
```
sudo apt-get install gcc gfortran python-dev libblas-dev liblapack-dev cython
```


