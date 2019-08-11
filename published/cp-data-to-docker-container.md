传输文件到docker容器

首先需要知道docker容器的container_id,可以使用`docker ps`命令来查看你要操作的docker容器的container_id



## Docker容器向宿主机传送文件

格式:

```
docker cp container_id:<docker容器内的路径> <本地保存文件的路径>
```
比如:

```
docker cp 10704c9eb7bb:/root/test.text /home/vagrant/test.txt
```


## 宿主机向Docker容器传送文件

格式:

```
docker cp 本地文件的路径 container_id:<docker容器内的路径>
```
比如:

```
docker cp  /home/vagrant/test.txt 10704c9eb7bb:/root/test.text
```

## 其它

在宿主机(本机)中通过`docker cp --help` 查看 `docker cp`的用法

```
vagrant@centos:~$ docker cp --help

Usage:	docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-
	docker cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH

Copy files/folders between a container and the local filesystem

Options:
  -a, --archive       Archive mode (copy all uid/gid information)
  -L, --follow-link   Always follow symbol link in SRC_PATH
```

##### References

1. [docker容器和本机互传文件](https://www.cnblogs.com/phonecom/p/10267510.html)