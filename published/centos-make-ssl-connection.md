Centos 14: problem making ssl connection



在执行 yum 命令时，会提示



```
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
Could not get metalink https://mirrors.fedoraproject.org/metalink?repo=epel-6&arch=x86_64 error was
14: problem making ssl connection
```



解决方法是将: `/etc/yum.repos.d/epel.repo` 文件中的`[epel]` 节点的`mirrorlist`注释掉，`baseurl`去除注释,`enable`的值为`1`



命令:

使用vim 编辑器打开`/etc/yum.repos.d/epel.repo`文件

```
vim /etc/yum.repos.d/epel.repo
```

将

```
[epel]
name=Extra Packages for Enterprise Linux 6 - $basearch
# baseurl 前去除#号
baseurl=http://download.fedoraproject.org/pub/epel/6/$basearch
# mirrorlist 前加#好
#mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=epel-6&arch=$basearch
failovermethod=priority
# enable值改为1
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6
```

修改完`epel.repo` 文件之后，然后再试 `yum` 命令
如:

```
yum list php
```


##### References

1. [linux下yum错误：Errno 14 problem making ssl connection Trying other mirror.](https://www.cnblogs.com/ssyfj/p/9178745.html)