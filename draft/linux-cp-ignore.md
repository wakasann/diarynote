linux cp ignore folder



比如: `/home/vagrant/test`  目录下有 `a`,`b`,`c` 三个文件夹，只复制 `c` 文件夹下面的文件到`/home/vagrant/test2` 下

>  使用cp命令复制的时候，只能排除一个目录不被复制，如果想排除两个或者多个目录的话，就需要使用rsync命令来实现了，看下面的例子 

```
# yum install rsync
# apt install rsync
$ cd /home/vagrant/test
$ pwd
$ /home/vagrant/test
$ rsync -av --exclude a --exclude b . ../test2
```

*  -a, --archive 归档模式，表示以递归方式传输文件，并保持所有文件属性，等于-rlptgoD。 
* -v 显示同步过程的详细(verbose)信息
* 第一个`.` 是把当前目录做为同步源，因为执行`rsync`命令时已经进入到`/home/vagrant/test` 下
* 第二个`../test2` 把相对当前目录上级的`test2` 文件夹作为同步目标目录




> 注意：--exclude后面的路径不能为绝对路径，必须为相对路径才可以，否则出错。
>
> 注解：
>
> rsync命令是一个**远程数据同步工具**，**可通过LAN/WAN快速同步多台主机间的文件**。-a参数表示archive模式，-v表示详细链模式输出，-z表示传输文件时使用压缩传输的模式。



## References

* [(转)linux下cp目录时排除一个或者多个目录的实现方法](https://www.cnblogs.com/hubavyn/p/5452039.html)
* [Rsync远程同步，实现下行 ，上行异地备份。配置rsync+inotify实时备份](https://blog.51cto.com/13728740/2151543)