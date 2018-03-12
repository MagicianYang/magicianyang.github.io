---
layout: post
section-type: post
title: Ubuntu Environment Settings
category: tech
tags: [ 'linux' ]
---

## 装机必备常用环境配置，不断更新中

### 1.[pip](https://pip.pypa.io/en/stable/)
#### Install
On Debian and Ubuntu:

```
$ sudo apt-get install python-pip
```

#### Upgrade
On Linux or OS X:

```
pip install -U pip
```

### 2.[virtualenv](https://virtualenv.readthedocs.org/en/latest/index.html)
#### Install
To install globally with pip (if you have pip 1.3 or greater installed globally):

```
$ [sudo] pip install virtualenv
```

#### Usage
```
$ virtualenv --python=python3 .env
```

python version here is necessary, after you activate the env,try `which python`

```
$ source .env/bin/activate
$ deactivate
```



