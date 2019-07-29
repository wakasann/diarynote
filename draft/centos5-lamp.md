Centos 安装 LAMP

系统: Centos 6.5
Apache 2.4 + PHP 7.2 + Mysql 5.7

## 准备工作

1. centos 查看版本

查看 centos版本 [How to Check CentOS Version](https://www.thegeekdiary.com/how-to-check-centos-version/)

```
cat /etc/redhat-release
cat /etc/centos-release
cat /etc/os-release
```

2. 更新yum 源

```
[vagrant@centos tmp]$ sudo yum update
```

## 使用yum安装 Apache

```
[vagrant@centos tmp]$ sudo yum install httpd
```

## 安装好查看版本

```
[vagrant@centos tmp]$ httpd -v
Server version: Apache/2.2.15 (Unix)
Server built:   Jun 19 2018 15:45:13
```

## 开启 apache 自启动

```
[vagrant@centos tmp]$ sudo chkconfig httpd on
```
## Apahce 相关命令

```
sudo service httpd start #启动apache
sudo service httpd stop # 禁用apache
sudo service httpd restart # 重启apache
sudo apachectl configtest #测试配置文件的语法是否正确
```

## 开放 Apache 常用的 80,443端口

开放Apache的 `80`,`443` 端口

执行命令:

```
sudo /sbin/iptables -I INPUT -p tcp --dport 80 -j ACCEPT
sudo /sbin/iptables -I INPUT -p tcp --dport 443 -j ACCEPT
```
保存之前输入已开放端口号语句的命令

```
sudo /etc/rc.d/init.d/iptables save
```

查看打开端口命令

```
sudo service iptables status 
```

```
sudo service iptables restart #重启 iptables命令
```

## 配置 Apache 配置文件

编辑 `/etc/httpd/conf/httpd.conf`，禁止列出文件目录，访问`.env`(Laravel的配置文件)格式的文件

执行命令:

```
sudo vim /etc/httpd/conf/httpd.conf
```


找到 `<Directory "/var/www/html"`,将 `Options Indexes FollowSymLinks`改为 `Options FollowSymLinks`,禁止列出文件目录

在当前组的 `</Directory>` 之前添加

```
<Files .env>
    Order allow,deny
    Deny from all
</Files>
```
禁止访问`.env`文件

最后`httpd.conf`中`<Directory "/var/www/html">`节点的所有内容类似

```
#
# This should be changed to whatever you set DocumentRoot to.
#
<Directory "/var/www/html">
    Options FollowSymLinks
    AllowOverride All
    Order allow,deny
    Allow from all
<Files .env>
    Order allow,deny
    Deny from all
</Files>
</Directory>
```

---

```
[vagrant@centos tmp]$ apachectl configtest
httpd: apr_sockaddr_info_get() failed for centos
httpd: Could not reliably determine the server's fully qualified domain name, using 127.0.0.1 for ServerName
Syntax OK
```
如果出现`apr_sockaddr_info_get()`的错误，

1. 确认`etc/sysconfig/network`设定的值


```
$ cat /etc/sysconfig/network
NETWORKING=yes
HOSTNAME=centos
```

2. `/etc/hosts`追加

```
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 centos
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
```

3. `/etc/httpd/conf/httpd.conf` 修改

```
#ServerName www.example.com:80
ServerName centos:80
```


## 安装 Mysql 5.7

### 更新及安装 mysql 的 yum 源

1. 官网下载源码包

```
[vagrant@centos tmp]$ wget http://dev.mysql.com/get/mysql57-community-release-el6-7.noarch.rpm
```

2. rpm 安装 mysql 的 yum 源

```
[vagrant@centos tmp]$ sudo rpm -Uvh mysql57-community-release-el6-7.noarch.rpm
```

3. 打开 `mysql-community.repo` 文件，查看关于 mysql 的内容，确定 `[mysql57-community]` 的 `enable`的值是`1` 

```
[vagrant@centos tmp]$ sudo vim /etc/yum.repos.d/mysql-community.repo
```

### 安装 Mysql

执行安装 mysql 命令

```
[vagrant@centos tmp]$ sudo yum install mysql-community-server
```
安装时，会出现2次确认，都输入`y`, 按`Enter`键确认即可

完成后启动服务

```
[vagrant@centos tmp]$ sudo service mysqld start
```

启动完成后，查看临时给`root`用户生成的密码

```
$ sudo grep "password" /var/log/mysqld.log
2018-08-08T02:59:58.687152Z 1 [Note] A temporary password is generated for root@localhost: 3fF4?tujHrfl
2018-08-08T03:00:04.204111Z 2 [Note] Access denied for user 'UNKNOWN_MYSQL_USER'@'localhost' (using password: NO)
```

第一行 ` root@localhost: 3fF4?tujHrfl` 这个就是数据库的 root 密码了

### 修改初始化密码

为了Mysql安全，建议修改 root的初始密码

执行命令:

```
[vagrant@centos tmp]$ sudo mysql_secure_installation
```

1. 执行上面的命令之后，需要先输入 `root`临时生成的密码，上一步查询到
2. 输入`root`目前的密码之后，需要输入2次新的root密码
3. `Change the password for root ? ((Press y|Y for Yes, any other key for No) : ` 回复 `y`来确认修改 `root`用户的密码
4. `Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : `  回复`y`来继续下面的步骤
5. `Remove anonymous users? (Press y|Y for Yes, any other key for No):` 回复 `y`来移除匿名用户(在生成环境下，你应该删除它)
6. `Disallow root login remotely? (Press y|Y for Yes, any other key for No) : ` 回复`y`来禁用 `root` 远程登录
7. `Remove test database and access to it? (Press y|Y for Yes, any other key for No) : ` 回复`y`来移除测试数据库
8. `Reload privilege tables now? (Press y|Y for Yes, any other key for No) : ` 回复`y`来重新加载权限


mysql 的配置文件默认在 `/etc/my.cnf`

###  开启Mysql 自启动

```
[vagrant@centos tmp]$ sudo chkconfig mysqld on
```

## 安装 PHP 7.2

如果之前已经安装过 php 的话,需要先卸载

```
[vagrant@centos tmp]$ sudo yum  remove php* php-common
```

### 更新及安装 PHP 的 yum 源


```
[vagrant@centos tmp]$ rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
```

####  修改 yum 源

执行命令:

```
#安装第三方源epel-release
[vagrant@centos tmp]$ sudo yum install epel-release
[vagrant@centos tmp]$ sudo vi /etc/yum.repos.d/remi.repo
```

将 `[remi]` 节点下的 `enabled`的值由`0` 改为 `1`。

列出 `/etc/yum.repos.d`文件夹下的文件列表

```
[vagrant@centos tmp]$ ls -l /etc/yum.repos.d
CentOS-Base.repo       CentOS-fasttrack.repo  CentOS-Vault.repo  epel-testing.repo     mysql-community-source.repo  remi-glpi92.repo  remi-glpi94.repo  remi-php70.repo  remi-php72.repo  remi.repo
CentOS-Debuginfo.repo  CentOS-Media.repo      epel.repo          mysql-community.repo  remi-glpi91.repo             remi-glpi93.repo  remi-php54.repo   remi-php71.repo  remi-php73.repo  remi-safe.repo
```

通过上面的命令结果，可以按照自己需要安装的PHP版本，修改对应版本`remi-php<version>.repo`的文件

因为我需要安装的是 `7.2`版本，所以编辑的是 `remi-php72.repo`

执行命令: 
```
sudo vim /etc/yum.repos.d/remi-php72.repo
```

将 `[remi-php72]` 节点下的 `enabled`的值由`0` 改为 `1`。

```
yum list php
```
列出所有可安裝的软件php清单,如果显示的是 7.x 的话 就没有问题

#### 开始安装 php

```
sudo yum install php php-cli php-curl php-json php-pdo php-mysql php-gd php-bcmath php-xml php-mbstring php-mcrypt php-redis php-crypto
```
安装途中，会出现多次确认，输入`y`并按`Enter`键 确认 安装

安装完成之后，可以通过`php -v`查看 php 版本 或`php -m` 查看安装的扩展


## 配置PHP

配置文件默认是在`/etc/php.ini`


```
date.timezone = Asia/Shanghai
upload_max_filesize = 20M
post_max_size = 20M
#生成环境需关掉显示错误信息
display_errors = Off
# 在HTTP返回头不显示 PHP 信息
expose_php = Off 
```

## 安装Composer

按照 [Command-line installation](https://getcomposer.org/download/) 命令进行安装
```
cd /tmp
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
# 如果安装的版本不一样，sha384会验证失败的情况
php -r "if (hash_file('sha384', 'composer-setup.php') === '48e3236262b34d30969dca3c37281b3b4bbe3221bda826ac6a9a62d6444cdb0dcd0615698a5cbe587c3f0fe57a54d8f5') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"

php composer-setup.php

php -r "unlink('composer-setup.php');"
# 全局安装 composer
sudo mv composer.phar /usr/local/bin/composer
# 查看composer版本
composer -V
```

##### References
1. [CentOS6.5 下升级 PHP7、MySQL5.7](https://laravel-china.org/topics/2289/centos65-upgrade-php7-mysql57) 文章安装部分文字描述主要参考
1. [centos 6.5 防火墙开放指定端口](https://www.cnblogs.com/tiandi/p/10152775.html) 简单快速设置好  iptables
2. [Centos查看端口占用情况和开启端口命令](https://www.centos.bz/2018/03/centos%E6%9F%A5%E7%9C%8B%E7%AB%AF%E5%8F%A3%E5%8D%A0%E7%94%A8%E6%83%85%E5%86%B5%E5%92%8C%E5%BC%80%E5%90%AF%E7%AB%AF%E5%8F%A3%E5%91%BD%E4%BB%A4/) 开放端口的内容规则，但是新增的规则一定要放在`-A FORWARD`开头这1行的前面，不然重启 iptables 不会生效
3. [CentOS / httpd > Starting httpd: httpd: apr_sockaddr_info_get() failed for [hostname].localdomain](https://qiita.com/7of9/items/a8f856653591dbf73f42) centos httpd 重啓顯示 `httpd: apr_sockaddr_info_get() failed for`的解决参考