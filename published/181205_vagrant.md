1. [Xshell配置ssh免密码登录-密钥公钥(Public key)与私钥(Private Key)登录](http://www.aiezu.com/system/linux/xshell_ssh_public-key_login.html)
2. [Install VirtualBox guest additions](https://nanxiao.me/en/install-virtualbox-guest-additions/)
[mount gives unknown filesystem type 'vboxsf'](https://superuser.com/a/496550)
在虚拟机设定中，添加光驱
```
Vagrant was unable to mount VirtualBox shared folders. This is usually
because the filesystem "vboxsf" is not available. This filesystem is
made available via the VirtualBox Guest Additions and kernel module.
Please verify that these guest additions are properly installed in the
guest. This is not a bug in Vagrant and is usually caused by a faulty
Vagrant box. For context, the command attempted was:

mount -t vboxsf -o uid=1000,gid=1000 var_www_html /var/www/html

The error output from the command was:

mount: unknown filesystem type 'vboxsf'
```

解决访问参考 [vagrant up で mount: unknown filesystem type 'vboxsf' が出る場合](http://blog.suprsonicjetboy.com/entry/2017/05/16/215353)

通过`vagrant plugin install vagrant-vbguest` 安装了这个插件之后，再执行 `vagrant up` 就运行正常了
