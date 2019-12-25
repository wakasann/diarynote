基于docker-ce创建gitlab-ce容器笔记

## start

vagrant + ubuntu 16.04

## 设置 apt 源

```
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
sudo vim /etc/apt/sources.list
```

[Ubuntu 镜像](https://developer.aliyun.com/mirror/ubuntu)

在vim在非输入模式，输入`ggdG` 可进行清空整个文件的内容，将以下内容复制进去

```
deb http://mirrors.aliyun.com/ubuntu/ xenial main
deb-src http://mirrors.aliyun.com/ubuntu/ xenial main

deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main

deb http://mirrors.aliyun.com/ubuntu/ xenial universe
deb-src http://mirrors.aliyun.com/ubuntu/ xenial universe
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates universe
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates universe

deb http://mirrors.aliyun.com/ubuntu/ xenial-security main
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main
deb http://mirrors.aliyun.com/ubuntu/ xenial-security universe
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security universe
```

按 Esc 键(退出输入模式) + 输入`:wq`，保存当前修改写入到文件并退出

```
sudo apt-get update
```

-------

docker-ce 安装步骤略

docker-ce gitlab 命令笔记

```
$ sudo docker pull gitlab/gitlab-ce:11.1.4-ce.0
$ cd ~
$ mkdir dockers
$ cd dockers
$ mkdir -p gitlab/{config,log,data}
$ sudo docker run  \
    --publish 8080:80 --publish 8022:22 \
    --name gitlab \
    --restart always \
    --volume ${PWD}/gitlab/config:/etc/gitlab \
    --volume ${PWD}/gitlab/logs:/var/log/gitlab \
    --volume ${PWD}/gitlab/data:/var/opt/gitlab \
    gitlab/gitlab-ce:11.1.4-ce.0
```

将gitlab的端口`80`衍射为`8080`,`22`衍射为`8022` 镜像访问

等待 gitlab初始完成之后，可以通过浏览器访问`http://ip:8080` 访问gitlab页面

docker run 参数说明:

```
# -d：后台运行
# -p：将容器内部端口向外映射
# --name：命名容器名称
# -v：将容器内数据文件夹或者日志、配置等文件夹挂载到宿主机指定目录
```

