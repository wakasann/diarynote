处理 vagrant Homestead 响应慢

## 环境
Homestead box:
Homestead: v8.2.0
vagrant:  2.2.4

1. 在`Homestead`中 安装`nfs-kernel-server`

```
sudo apt-get install nfs-kernel-server
rpcinfo -p
```

2. 编辑 `Homestead.yaml` 文件，给`folders:` 节点下的 `to:`下面新增`type: nfs`

如:

```
ip: "192.168.10.10"
memory: 4098
cpus: 1
provider: virtualbox
box: laravel/homestead
authorize: ~/.ssh/id_rsa.pub

keys:
    - ~/.ssh/id_rsa

folders:
    - map: D:/workspace/php/laravel
      to: /home/vagrant/code/laravel
      type: nfs
```

3. 安装 vagrant `vagrant-winnfsd` 插件

```
vagrant plugin install vagrant-winnfsd
```

4. 重启`vagrant`

```
vagrant provision
vagrant reload
```

在运行了`vagrant reload`之后，`mount.nfs` 连接超时

```
[NFS] Status: halted
[NFS] Start: started
==> homestead-7: Mounting NFS shared folders...

The following SSH command responded with a non-zero exit status.
Vagrant assumes that this means the command failed!

mount -o vers=3,udp,actimeo=1,nolock,noatime 192.168.10.1:/D/workspace/php/laravel1 /home/vagrant/code/laravel1

Stdout from the command:



Stderr from the command:

mount.nfs: Connection timed out
```

如果需要挂载的站点访问快，在 `Homestead.yaml`的`folders` 下添加`type: nfs`，但不可以给太多站点加`type: nfs`，可能会出现`mount.nfs: Connection timed out` 的提示错误，导致挂载文件夹失败


##### References

1. [解决vagrant上使用Homestead很慢(响应速度10s+)](https://www.cnblogs.com/Skrillex/p/8087141.html)
2. [I get this during the mount](https://github.com/winnfsd/vagrant-winnfsd/issues/107#issue-219942549) 发现 Homestead.yaml 文件 中可以添加`mongodb: false
mariadb: false
postgresql: false`
3. [mount.nfs: Connection timed out](https://ubuntuforums.org/showthread.php?t=1906584) 安装了``
4. [VirtualBox 虚拟机里网络很慢的解决方法](https://www.pylist.com/t/1555072754) 了解到 `time`可以获取请求时间
5. 