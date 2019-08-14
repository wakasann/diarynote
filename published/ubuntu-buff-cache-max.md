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


------

自动清除脚本，参考1

1. 编写一个文件名为`freemem.sh`的文件

日志将会放入到 `/var/log/my_free_mem.log`文件中

```
vim /opt/freemem.sh
```

文件的内容是

```
#! /bin/sh
used=`free -m | awk 'NR==2' | awk '{print $3}'`
free=`free -m | awk 'NR==2' | awk '{print $4}'`
echo "===========================" >> /var/log/my_free_mem.log
date >> /var/log/my_free_mem.log
echo "Memory usage before | [Use：${used}MB][Free：${free}MB]" >>/var/log/my_free_mem.log
if [ $free -le 4000 ] ; then
                sync && echo 1 > /proc/sys/vm/drop_caches
                sync && echo 2 > /proc/sys/vm/drop_caches
                sync && echo 3 > /proc/sys/vm/drop_caches
                used_ok=`free -m | awk 'NR==2' | awk '{print $3}'`
                free_ok=`free -m | awk 'NR==2' | awk '{print $4}'`
                echo "Memory usage after | [Use：${used_ok}MB][Free：${free_ok}MB]" >>/var/log/my_free_mem.log
                echo "OK" >>/var/log/my_free_mem.log
else
                echo "Not required" >>/var/log/my_free_mem.log
fi
exit 1
```

下一步给 `/var/log/my_free_mem.log`文件添加执行权限

```
chmod +x /opt/freemem.sh
```

下一个添加定时任务

通过下面的命令，编写当前用户的crontab

```
crontab -e
```

在打开的文件中末尾添加

```
0 2 * * * /opt/freemem.sh
```

上面的定时任务的意思是，每天2点钟的时候执行一次 `/opt/freemem.sh`脚本，可根据实际情况来调整定时的空隙


保存退出 crontab之后，下一步就是重启 crond服务

```
/etc/init.d/cron restart
```

查看crond服务是否重启成功

```
/etc/init.d/cron status 

● cron.service - Regular background program processing daemon
   Loaded: loaded (/lib/systemd/system/cron.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2019-08-11 10:30:49 CST; 1min 7s ago
     Docs: man:cron(8)
```



##### References

1. [linux buff/cache过大，清理脚本](https://blog.csdn.net/seanxwq/article/details/88527973)
2. [buff/cache内存占用过多](https://blog.csdn.net/qq_30754565/article/details/82458252)