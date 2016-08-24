---
layout: post
section-type: post
title: Useful Packages for Python
category: tech
tags: [ 'python' ]
---

### 1.[SciPy](http://www.scipy.org)
#### [Install](http://www.scipy.org/install.html)

Q1:About the missing libraries -- BLAS and LAPACK,
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

Q2:With the error below

```
error: library dfftpack has Fortran sources but no Fortran compiler found
    ----------------------------------------
Command "/.env345/bin/python3.4 -c "import setuptools, tokenize;__file__='/tmp/pip-build-fxjr8z4j/scipy/setup.py';exec(compile(getattr(tokenize, 'open', open)(__file__).read().replace('\r\n', '\n'), __file__, 'exec'))" install --record /tmp/pip-0cl56t99-record/install-record.txt --single-version-externally-managed --compile --install-headers /.env345/include/site/python3.4/scipy" failed with error code 1 in /tmp/pip-build-fxjr8z4j/scipy
You are using pip version 7.1.2, however version 8.1.2 is available.
You should consider upgrading via the 'pip install --upgrade pip' command.
```

just update the pip and it will be fixed.

see [this](https://github.com/scipy/scipy/issues/4102)