# ss搭建

强烈建议使用docker安装ss，https://hub.docker.com/r/mritd/shadowsocks/



```
docker run -dt --name ss -p 6443:6443 mritd/shadowsocks -s "-s 0.0.0.0 -p 6443 -m chacha20-ietf-poly1305 -k test123" --restart=always
```



`-p 6443:6443` 前面的`6443`是转发的端口，外部访问使用的,可根据实际情况进行更换

`-k`参数后面的是密码



会自动重新 –restart=always，如果已经启动的容器，使用 docker update –restart=always [container id]

也可以手动搭建（不建议，个人觉得是有点繁琐），参考 https://www.linuxbabe.com/linux-server/setup-your-own-shadowsocks-server-on-debian-ubuntu-centos

 

##### References

1. 转发自 [ss 搭建]([http://blog.pdwjun.com/2018/07/14/ss-%e6%90%ad%e5%bb%ba/](http://blog.pdwjun.com/2018/07/14/ss-搭建/))  而来