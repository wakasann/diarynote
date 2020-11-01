树莓派登录ssh 很慢和开机启动tightvncserver

设备:
1. 树莓派3b,系统 raspbian

#### 树莓派登录ssh很慢

在参考了参考列表的第1篇博文

1. 关闭 ssh的gssapi认证

```
sudo vim /etc/ssh/ssh_config
```

在该文件的末尾找到这两行设置并注释掉

```
# GSSAPIAuthentication yes
# GSSAPIDelegateCredentials no

# 关闭ssh的UseDNS
UseDNS no
```

保存退出,重启ssh服务
```
sudo service sshd restart
```

#### tightvncserver开机启动

> 树莓派上安装vnc服务端（debian）：sudo apt install tightvncserver
>修改vnc密码：vncpasswd
> vnc-server 开机启动文件：`sudo vi /etc/init.d/tightvncserver` ,在这个文件里输入如下内容

```
#!/bin/sh
### BEGIN INIT INFO
# Provides: tightvncserver
# Required-Start: $syslog $remote_fs $network
# Required-Stop: $syslog $remote_fs $network
# Default-Start: 2 3 4 5
# Default-Stop: 0 1 6
# Short-Description: Starts VNC Server on system start.
# Description: Starts tight VNC Server. Script written by James Swineson.
### END INIT INFO
# /etc/init.d/tightvncserver
VNCUSER='pi'
case "$1" in
        start)
                su $VNCUSER -c '/usr/bin/tightvncserver :1'
                echo "Starting TightVNC Server for $VNCUSER"
        ;;
        stop)
                pkill Xtightvnc
                echo "TightVNC Server stopped"
        ;;
        *)
                echo "Usage: /etc/init.d/tightvncserver {start|stop}"
                exit 1
        ;;
esac
exit 0
```

> 然后给增加执行权限，并启动服务：

```
sudo chmod +x /etc/init.d/tightvncserver
sudo update-rc.d tightvncserver defaults # 添加开机启动
sudo update-rc.d tightvncserver remove #移除开机启动
```


## References
* [解决树莓派SSH登录缓慢的问题](https://blog.csdn.net/guanmaoning/article/details/80283012)
* [ssh连接慢或者自动卡死问题解决方案](https://blog.csdn.net/weixin_38234765/article/details/80321723)
* [树莓派设置开机启动vnc教程](https://blog.csdn.net/naibozhuan3744/article/details/84966149)
* [树莓派安装vnc server并设置自启动](https://www.cnblogs.com/leviatan/p/9428188.html)