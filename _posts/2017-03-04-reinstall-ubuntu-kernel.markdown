---
layout: post
section-type: post
title: 误删Ubuntu内核后处理恢复方法
category: tech
tags: [ 'ubuntu' ]
toc: show
---

这周升级Ubuntu16.04LTS版本的时候，遇到了一个无法安装`initramfs-tools`的报错。经查询之后发现是/boot这个区块满了

当时查看我本机状况的时候/boot部分是100%，以下是现在的状况

```
$ df -h
文件系统        容量  已用  可用 已用% 挂载点
udev            3.9G     0  3.9G    0% /dev
tmpfs           792M  9.5M  782M    2% /run
/dev/sdb7        19G  9.3G  8.5G   53% /
tmpfs           3.9G   89M  3.8G    3% /dev/shm
tmpfs           5.0M  4.0K  5.0M    1% /run/lock
tmpfs           3.9G     0  3.9G    0% /sys/fs/cgroup
/dev/sdb5       232M   58M  158M   27% /boot
/dev/sdb8        79G  5.9G   69G    8% /home
cgmfs           100K     0  100K    0% /run/cgmanager/fs
tmpfs           792M   52K  792M    1% /run/user/1000

```

于是继续找解决方法，删除多余内核，但是这里按照网上操作的就有问题了……

查看当前内核

```
$ dpkg --get-selections|grep linux-image
```

网上说你直接`sudo apt-get remove linux-image-xxx`把所有这些都删了就行了，然后……然后就悲剧了，我开心的删了所有内核，包括当前系统使用的，等我再开机的时候，发现进不了Ubuntu系统了……

关于怎么正确删除旧版本内核，以后再研究，目前先说如何解决了问题。

本人电脑装的是Windows7+Ubuntu双系统，于是进了win7做了一个Ubuntu16.04LTS的USB安装盘。

这里有个坑，因为太着急第一次制作的时候忘记格式化为FAT32类型的了，而是直接在NTFS的格式上面装的，导致重启电脑后，u盘进入时直接黑着屏幕然后有报错，无法抵达Ubuntu的LiveCD界面，记得格式化正确！

之后的教程全程参考这个答案[How to restore a system after accidentally removing all kernels](http://askubuntu.com/a/166010)

感谢大佬的保姆级教程全程托管，所有坑的地方大佬都已经指出了，本人顺利安装成功了。

需要指出的一点就是，大佬教程里面第7步，那句

```
sudo mount /dev/sda1 /mnt
```

里面替代`sda1`的是`sdb7`

然后第8步里面

```
sudo mount BOOT-PARTITION /mnt/boot
```

替代`BOOT-PARTITION`的是`/dev/sdb5`，也就是我电脑划定的那个/boot小分区，辨别这些挂载分区的方法大佬在第6步的时候描述的很清楚

总之走完大佬的教学步骤之后，重启进入win7，重新用EasyBCD设置了一下开机启动向导，这里还要注意一点，添加Ubuntu的启动项的时候，务必选Grub2那条，然后引导分区就会显示默认查找，这样就行了。之后再重启电脑，就能成功进入Ubuntu啦！
