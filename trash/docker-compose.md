Docker Compose文件下载慢的处理笔记

官网文档 [Install Compose on Linux systems](https://docs.docker.com/compose/install/#install-compose-on-linux-systems)的说明，运行以下命令进行下载当前稳定发布的 Docker Compose:

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.28.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

参考文档，可以是

```
sudo curl -L "https://get.daocloud.io/docker/compose/releases/download/1.28.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

将 `1.28.2` 这个版本替换为需要下载的版本，然后再执行上面的命令，我自己尝试之后，感觉下载速度很快。

------

[DaoMirror](https://www.daocloud.io/mirror) 在官网的底部菜单栏的资源(Resources)有这个链接。

## References

[docker-compose 官网下载特别慢怎么办？](https://www.cnblogs.com/bigband/p/13515219.html)



