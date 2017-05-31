---
layout: post
section-type: post
title: ip地址获取或转换
category: tech
tags: [ 'ubuntu' ]
toc: show
---

django从request里面获取ip

```
def get_ip(request):
    try:
        real_ip = request.META['HTTP_X_FORWARDED_FOR']
        reg_ip = real_ip.split(",")[0]
    except KeyError:
        try:
            reg_ip = request.META['REMOTE_ADDR']
        except KeyError:
            return 0
```

str -> int

```
def ip_str2int(ip_address: str) -> int:
    ip_list = ip_address.split('.')
    ans = 0
    for item in ip_list:
        try:
            ans = (ans << 8) + int(item)
        except ValueError:
            continue
    return ans
```


int -> str

```
def ip_int2str(num: int) -> str:
    """
    inverse operation get_ip
    """
    ip4 = num & 255
    num = num >> 8
    ip3 = num & 255
    num = num >> 8
    ip2 = num & 255
    num = num >> 8
    ip1 = num & 255
    return "{}.{}.{}.{}".format(ip1, ip2, ip3, ip4)

def ip_int2str(ip_int):
    result = []
    mask = 0xff
    while ip_int > 0:
        part = ip_int & mask
        result.append(str(part))
        ip_int = ip_int >> 8
    ip_str = '.'.join(reversed(result))
    return ip_str
```