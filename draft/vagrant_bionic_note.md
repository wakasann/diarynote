windows vagrant ubuntu bionic(18.04) cloud images 笔记



## 前言

这是我使用 vagrant + virtualbox 6.0 基于ubuntu bionic(18.04) cloud image box 搭建 LAMP(Apache + Mysql 5.7 + PHP7.2)的命令流水帐。

因为我个人对Nginx的配置不熟悉，导致我尝试基于Thinkphp6和TP多应用插件的项目，访问多应用的路由总是显示404,后换回熟悉的Apache环境，同样的项目，多应用的路由访问正常了(我猜想是我没有配置好我运行环境中Nginx关于pathinfo部分的配置项)



## 正文



从 [ubuntu/trusty64](https://app.vagrantup.com/ubuntu/boxes/trusty64) 版本中，有显示`virtualbox Externally hosted (cloud-images.ubuntu.com)`的字样，
通过访问[http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/), 然后到[http://cloud-images.ubuntu.com/bionic/current/](http://cloud-images.ubuntu.com/bionic/current/)
发现有 `bionic-server-cloudimg-amd64-vagrant.box`,这是 vagrant的box

然后通过[清华大学开源软件镜像站](https://mirror.tuna.tsinghua.edu.cn/) 列表中的 [ubuntu-cloud-images](https://mirror.tuna.tsinghua.edu.cn/ubuntu-cloud-images/)，我通过[https://mirror.tuna.tsinghua.edu.cn/ubuntu-cloud-images/bionic/current/](https://mirror.tuna.tsinghua.edu.cn/ubuntu-cloud-images/bionic/current/)
快速下载到了`bionic-server-cloudimg-amd64-vagrant.box`。



我以后下载 vagrant ubuntu的vagrant box，以后就可以通过国内的ubuntu镜像站的`ubuntu-cloud-images`节点，下载到对应版本的ubuntu vagrant box啦，解决了我下载ubuntu系统vagrant box慢的问题。


导入下载的ubuntu bionic vagrant box

通过命令窗口，到达存放bionic box的路径下

```
vagrant box add bionic bionic-server-cloudimg-amd64-vagrant.box
```


在命令行，去到vagrant的工作目录

```
# 使用新增名为 bionic的box
vagrant init bionic
```

启动

```
vagrant up
```


通过Xshell 连接ssh时，需要使用密钥进行登录(密钥文件在 当前vagrant 目录的`.vagrant/machines/default/virtualbox`)



![uploads/200531/20200531144332.png](uploads/200531/20200531144332.png)



使用 xshell 登录

![uploads/200531/20200531145128.png](uploads/200531/20200531145128.png)



确定保存之后，打开，输入用户名为`vagrant`

![uploads/200531/20200531145241.png](uploads/200531/20200531145241.png)

![uploads/200531/20200531145409.png](uploads/200531/20200531145409.png)



ps: 上图中的，可以是 `浏览` -> `用户密钥`, 导入的密钥，选择 该虚拟机工作目录下的 `private_key`导入，这样以后就不用老是选择了。

通过以上的步骤，应该能通过xshell 成功连接 vagrant 基于ubuntu bionic的虚拟机的ssh了。

------

## 修改Ubuntu Bionic源

备份source

`sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak`

参考:

* [阿里云官方镜像站](https://developer.aliyun.com/mirror/)
* [清华大学开源软件镜像站 Ubuntu 镜像使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)



修改`sources.list`


```
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
```

## 安装Apache,PHP,Mysql软件

#### 安装

```
sudo apt-get update

sudo apt-get install zip unzip

sudo apt-get install apache2

sudo apt-get install mysql-server mysql-client mysql-utilities


sudo apt-get install php
sudo apt-get install php-xml php-xmlreader php-xmlrpc php-xmlwriter 
sudo apt-get install php-bcmath php-curl php-mbstring php-mysqli
sudo apt-get install php-pdo-sqlite  php-pgsql php-soap
sudo apt-get install php-gd php-dev php-crypto

# 或安装PHP一条命令过
sudo apt-get install php php-gd php-xml php-xmlreader php-xmlrpc php-xmlwriter  php-bcmath php-curl php-mbstring php-mysqli php-pdo-sqlite php-pgsql php-soap php-dev
```
#### apache的设定

```
# 启动apache重写模块
sudo a2enmod rewrite
# 重启apache服务
sudo systemctl restart apache2
# 编辑apache 默认的vhost文件
sudo vim /etc/apache2/sites-enabled/000-default.conf
```

在apache的配置文件中添加

```
<Directory "/var/www/html">
    AllowOverride All
</Directory>
```
加入以上内容，是让`/var/www/html`目录下的项目允许重写

重启 apache 服务

```
sudo systemctl restart apache2
```

给`/var/www/html` 添加 777 权限

```
sudo chmod 777 /var/www/html
sudo chmod 777 /var/www/html/* -R
```
#### mysql 设定
在安装mysql 软件服务过程中，没有输入密码的交互界面，所以不清楚 root 用户的密码，需要手动进行设置一次 mysql root 用户的密码

```
sudo systemctl start mysql # 启动mysql 服务
```

1. 查看`debian-sys-maint` 用户的密码

```
sudo vim /etc/mysql/debian.cnf
```
可以看到类型如下格式的内容:

```
# Automatically generated for Debian scripts. DO NOT TOUCH!
[client]
host     = localhost
user     = debian-sys-maint
password = gvCPbzra1qI6m49X
socket   = /var/run/mysqld/mysqld.sock
[mysql_upgrade]
....
```
上面`[clinet]`中的`user`和`password`是可以登入到mysql的用户名和密码.

2. 通过`mysql` 命令进入 mysql cli 交互界面

```
#FROM Ubuntu
mysql -u debian-sys-maint -p
```

```
#From mysql cli
mysql> use mysql;
mysql> select user,plugin,host from user; #查看用户表的用户信息
mysql> update user set authentication_string=password('新密码'),plugin="mysql_native_password" where user='root' and host="localhost"; #修改root用户的密码
mysql> flush privileges; #刷新权限
mysql> quit; #退出mysql cli交互界面
```

尝试使用修改过密码后的`root`用户登录,验证密码是否修改成功

```
mysql -u root -p
```



设置apache,mysql 开机启动

```
sudo systemctl enable apache2 # 设置apache开启启动
# sudo systemctl disable apache2 #是设置取消apache开机启动
sudo systemctl enable mysql
```

#### PHP Composer

安装composer

```
php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');"
php composer-setup.php
php -r "unlink('composer-setup.php');"
```

全局安装

```
sudo mv composer.phar /usr/local/bin/composer
```

设置 composer 中国源

```
# https://pkg.phpcomposer.com/
composer config -g repo.packagist composer https://packagist.phpcomposer.com
# 或者https://developer.aliyun.com/composer
composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
```

取消composer配置

```
composer config -g --unset repos.packagist
```

### 修改Mysql 5.7 root的密码

`sudo vim /etc/mysql/debian.cnf`,可以看到mysql默认的用户`debian-sys-maint`用户和密码

```
# Automatically generated for Debian scripts. DO NOT TOUCH!
[client]
host     = localhost
user     = debian-sys-maint
password = cRHEWj8bm6QvaS1d
socket   = /var/run/mysqld/mysqld.sock
[mysql_upgrade]
host     = localhost
user     = debian-sys-maint
password = cRHEWj8bm6QvaS1d
socket   = /var/run/mysqld/mysqld.sock
```

```
#From Ubuntu
mysql -u debian-sys-maint -p #登录mysql
```

```
#from mysql-cli
show databases;
use mysql;
update user set authentication_string=PASSWORD("自定义密码") where user='root';
update user set plugin="mysql_native_password";
flush privileges;
quit;
```

```
#From Ubuntu
/etc/init.d/mysql restart
```





我的 `Vagrantfile` 配置

```
# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "bionic"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"
  config.vm.network "private_network", ip: "192.168.33.11"
  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"
  config.vm.synced_folder "D:/workspaces/lamp", "/var/www/html"
  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
     vb.memory = "2049"
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
```



## References
* [Ubuntu-安装MySQL5.7并配置用户名密码](https://www.cnblogs.com/cm920/p/10029035.html)