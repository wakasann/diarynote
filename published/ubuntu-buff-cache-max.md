---
title: buff/cache内存占用过多
date: 2019-08-09 20:06:28
tags:
  -  linux
categories:
  - Linux
description:  buff/cache内存占用过多
---

通过`free -m` 查看到 `buff/cache`的值比较大，导致可使用的内存有`120M`左右了

通过下面的命令，清除缓存

```
echo 1 > /proc/sys/vm/drop_caches
echo 2 > /proc/sys/vm/drop_caches
echo 3 > /proc/sys/vm/drop_caches
```



```
whereis a2ensite
a2ensite: /usr/sbin/a2ensite /usr/share/man/man8/a2ensite.8.gz
ls -l /usr/sbin |grep a2
lrwxrwxrwx 1 root root        7 Jul 15  2016 a2disconf -> a2enmod
lrwxrwxrwx 1 root root        7 Jul 15  2016 a2dismod -> a2enmod
lrwxrwxrwx 1 root root        7 Jul 15  2016 a2dissite -> a2enmod
lrwxrwxrwx 1 root root        7 Jul 15  2016 a2enconf -> a2enmod
-rwxr-xr-x 1 root root    15424 Apr  6  2016 a2enmod
lrwxrwxrwx 1 root root        7 Jul 15  2016 a2ensite -> a2enmod
-rwxr-xr-x 1 root root     9870 Jul 15  2016 a2query
```





##### References

1. [linux buff/cache过大，清理脚本](https://blog.csdn.net/seanxwq/article/details/88527973)
2. [buff/cache内存占用过多](https://blog.csdn.net/qq_30754565/article/details/82458252)