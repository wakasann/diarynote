# Vagrant更改默认的SSH端口

 

vagrant 默认是 22转 2222 端口，可通过下面的方法，转发成 其它端口，如: 2223



```
config.vm.network "forwarded_port", guest: 22, host: 2223, id: "ssh"
```

--------

参考文章的做法是：

```
config.vm.network "forwarded_port", guest: 22, host: 2222, id: "ssh", disabled: "true"
config.vm.network "forwarded_port", guest: 22, host: 3333
```



## References

1. [Vagrant更改默认的SSH端口](https://www.cnblogs.com/php-linux/p/10794091.html)
2. [give a chance to disable default ssh port forwarding](https://github.com/hashicorp/vagrant/issues/1922)

