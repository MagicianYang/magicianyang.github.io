---
layout: post
section-type: post
title: Django中添加验证码
category: tech
tags: [ 'python','django','pillow' ]
---
## 环境配置

### 安装Pillow

环境：Ubuntu14.04LTS,Python3.4,Django1.8,jinja2

#### The _imagingft C module is not installed报错解决

[尝试1](https://pillow.readthedocs.org/en/latest/installation.html)：官方文档

Prerequisites:

Debian or Ubuntu:

```
$ sudo apt-get install python-dev python-setuptools
```

注意python3需要（重点）

```
sudo apt-get install python3-dev python3-setuptools
```

Ubuntu 14.04 LTS(重点)

```
$ sudo apt-get install libtiff5-dev libjpeg8-dev zlib1g-dev \
    libfreetype6-dev liblcms2-dev libwebp-dev tcl8.6-dev tk8.6-dev python-tk
```

[尝试2](http://stackoverflow.com/questions/4011705/python-the-imagingft-c-module-is-not-installed)[尝试3](http://stackoverflow.com/questions/32942861/error-installing-pillow-on-ubuntu-14-04)：stackoverflow等问题回答


Ubuntu 14.04

```
sudo apt-get install libfreetype6-dev
pip uninstall pillow
pip install --no-cache-dir pillow
```

还有就是

```
sudo ln -s /usr/lib/x86_64-linux-gnu/libz.so /usr/lib/libz.so
sudo ln -s /usr/lib/x86_64-linux-gnu/libjpeg.so /usr/lib/libjpeg.so
sudo ln -s /usr/lib/x86_64-linux-gnu/libfreetype.so /usr/lib/libfreetype.so
```

[验证](http://effbot.org/zone/pil-imaging-not-installed.htm):

下面这种情况应该就没问题了

```
Python 3.4.3 (default, Oct 14 2015, 20:28:29) 
[GCC 4.8.4] on linux
Type "help", "copyright", "credits" or "license" for more information.
(InteractiveConsole)
>>> import PIL
>>> PIL.PILLOW_VERSION
'3.3.0'
>>> from PIL import Image
>>> from PIL import _imaging
>>> Image.init()
1
>>> Image.SAVE.keys()
dict_keys(['HDF5', 'BUFR', 'PNG', 'MPO', 'GRIB', 'PDF', 'MSP', 'XBM', 'TIFF', 'PPM', 'PALM', 'ICO', 'FITS', 'IM', 'PCX', 'JPEG2000', 'EPS', 'GIF', 'SPIDER', 'TGA', 'WEBP', 'JPEG', 'WMF', 'BMP'])
```

而且也可以在路径`/.env34/lib/python3.4/site-packages/PIL`里面找到文件`_imagingft.cpython-34m.so`，这时候应该就正常了

### 安装django-simple-captcha以及基本Django配置

当前安装版本为django-simple-captcha0.5.2

[官方文档](http://django-simple-captcha.readthedocs.org/en/latest/usage.html)&[github代码](https://github.com/mbi/django-simple-captcha)

> 1. Download `django-simple-captcha` using pip by running: `pip install  django-simple-captcha`

> 2. Add `captcha` to the `INSTALLED_APPS` in your `settings.py`

> 3. Run `python manage.py syncdb` (or ``python manage.py migrate`` if you are managing database migrations via South) to create the required database tables

> 4. Add an entry to your `urls.py`

> ```
	urlpatterns += patterns('',
	url(r'^captcha/', include('captcha.urls')),
```

说明：第三步，当前版本的Django直接用`./manage.py migrate`就可以，不要用老指令`./manage.py syncdb`

## 代码部分

[参考1](http://blog.csdn.net/tanzuozhev/article/details/50458688)

[参考2](http://blog.csdn.net/xxm524/article/details/48370337)

[参考3](http://www.it-recipes.com/articles/blog/84)

### MVC的各部分写法（实践）

#### Form

参照[官方文档](http://django-simple-captcha.readthedocs.org/en/latest/usage.html#adding-to-a-form)

不过我们通常不直接使用django自带的Form，所以只要在form.py里面加上以下内容

```python
from django import forms
from captcha.fields import CaptchaField

class CaptchaLoginForm(forms.Form):
    captcha = CaptchaField()
```

#### Models

这个插件生成的hashkey和response存在表`captcha_captchastore`里面，表格存储实例如下

| id  | challenge | response | hashkey                                  | expiration          |
|-----|-----------|----------|------------------------------------------|---------------------|
| 112 | HFSS      | hfss     | 1c23a56b1b09a0f0c5cfb60d0bf25b4c098bae89 | 2016-05-04 06:16:47 |


#### Views & Templates

尝试1：非ajax形式，直接刷新页面的方式

views.py中,首先对`^login$`的get请求时生成验证码图片

```python
def login(request):
    cap_form = CaptchaLoginForm()
    return render_to_response('template.jinja', {'cap_form': cap_form})
```

对`^login$`的get请求时验证图片

```python
def login(request):
    cap_form = CaptchaLoginForm(request.POST)
    if not cap_form.is_valid():
        return HttpResponseRedirect('login?' + urlencode({'error': '验证码错误'}))
```

html里面

```jinja
<form id="login_form" action='login' method='post'>
    {% raw %}{{cap_form}}{% endraw %}
    <input type='submit'/>
</form>
```


尝试2：ajax形式，只刷新验证码部分

views.py中,首先对`^login$`的get请求时生成验证码图片

```python
def login(request):
    cap_form = CaptchaLoginForm()
    return render_to_response('template.jinja', {'cap_form': cap_form})
```

对应的template.jinja中，html部分可以直接调用包里面自带的区块生成验证码，即将{% raw %}{{ cap_form }}{% endraw %}直接放入form区域，之后添加提交按钮

```jinja
<form id="login_form" action='login' method='post'>
	// 此处先加入包里面自带区块cap_form
    {% raw %}{{cap_form}}{% endraw %}
	<span id="captcha_status" style="color:blue;display:none;">*验证码错误</span>
	<button type="button" id='login_submit'>登录</button>
	<input type='submit' hidden/>
	<button type="button" id='refresh_btn'>刷新</button>
</form>
```

为实现ajax验证，对应js代码（包括验证码核对以及刷新操作）

```JavaScript
$(function () {
        var controller = {
            info_verify: function (e) {
                e.preventDefault();
                var hashkey = $('#id_captcha_0').val();
                var key = $('#id_captcha_1').val();
                console.log(key);
                if (key == "") {
                    alert("请填写验证码");
                }
                else {
                    $.ajax('login/verify', {
                        data: {key: key, hashkey: hashkey},
                        type: 'POST',
                        success: function (data) {
                            console.log(data.rt);
                            if (data.rt) {
                                $("#login_form").submit();
                            } else {
                                $("#captcha_status").show();
                                $.getJSON("/captcha/refresh/",
                                        function (result) {
                                            $('.captcha').attr('src', result['image_url']);
                                            $('#id_captcha_0').val(result['key'])
                                        });
                            }
                        },
                        error: function (data) {
                            alert("网络错误T_T");
                        }
                    });
                }
            },
            refresh: function () {
                $.getJSON("/captcha/refresh/",
                        function (result) {
                            $('.captcha').attr('src', result['image_url']);
                            $('#id_captcha_0').val(result['key'])
                        });
            }
        };

        $('#login_submit').click(controller.info_verify);
        $('#refresh_btn').click(controller.refresh);

    });
```

这样验证码会先提交到路径`^login/verify$`执行views.py里面的

```python
def login_verify(request, key, hashkey):
    cs = CaptchaStore.objects.filter(hashkey=hashkey)
    true_key = cs[0].response
    key = key.lower()
    if key == true_key:
        return True, {'message': 1}
    else:
        return False, {'message': 0}
```

这样的话，views.py中,对`^login$`的post请求不用再去处理验证码问题


### 注意事项

1.rendering

模板中使用{% raw %}{{form}}{% endraw %}这种写法时，会调用captcha本身的模板，路径就在`/.env34/lib/python3.4/site-packages/captcha/templates`里面(.env34是虚拟环境)，但是由于Django自带`settings.py`里面对templates路径有设置，通常默认路径是`'DIRS': [os.path.join(PROJECT_PATH, '../templates')]`这种，所以需要手动拷贝captcha的模板到自己工程的模板文件夹下面。如果使用jinja的模板的话，就需要对该模板进行一些删除。

2.个性化设置参见官方文档即可




