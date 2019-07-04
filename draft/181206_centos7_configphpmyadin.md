# 配置 centos7 静态地址 #

```
ip a show
```
显示目前所有的ip信息，我的是 `eth1`

进入`/etc/sysconfig/network-scripts/` 目录 你应该可以找到 `ifcfg-INTERFACENAME`的文件

打开需要编辑的文件，如我的: `sudo vim ifcfg-eth1`

将`BOOTPROTO=dhcp` 改为`BOOTPROTO=static`

设定ip信息，最终配置文件如:

```
BOOTPROTO=satic
ONBOOT=yes
PREFIX=24
DEVICE=eth1
IPADDR=192.168.1.184
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
```

修改完之后，重启网络 `systemctl restart network`，最后通过`ip a show` 查看ip是否修改到,
#### 参考 ####
1. [How to configure a static IP address on CentOS 7 / RHEL 7](https://www.cyberciti.biz/faq/howto-setting-rhel7-centos-7-static-ip-configuration/)
2. [How to configure a static IP address in CentOS 7](https://www.techrepublic.com/article/how-to-configure-a-static-ip-address-in-centos-7/)
3. [Configure Static IP Address in CentOS](http://www.mustbegeek.com/configure-static-ip-address-in-centos/)
4. [[Centos7]Vagrantでpublic networkを使用して固定IPでアクセス可能にする](https://qiita.com/daikiojm/items/05c7b3ae7635ebec5d9c)


# phpmyadmin config #

安装命令

```
cd /var/www/html/
wget https://files.phpmyadmin.net/phpMyAdmin/4.8.3/phpMyAdmin-4.8.3-all-languages.zip
unzip phpMyAdmin-4.8.3-all-languages.zip 
mv phpMyAdmin-4.8.3-all-languages phpMyAdmin
cd phpMyAdmin/
cp config.simple.inc.php config.inc.php
vim config.inc.php
```

config.inc.php配置案例

```
<?php
/* vim: set expandtab sw=4 ts=4 sts=4: */
/**
 * phpMyAdmin sample configuration, you can use it as base for
 * manual configuration. For easier setup you can use setup/
 *
 * All directives are explained in documentation in the doc/ folder
 * or at <https://docs.phpmyadmin.net/>.
 *
 * @package PhpMyAdmin
 */
//https://lastpass.com/generatepassword.php
$cfg['blowfish_secret'] = '6&9NlqJNCvdG'; /* YOU MUST FILL IN THIS FOR COOKIE AUTH! */
$cfg['CheckConfigurationPermissions'] = false;
$i = 0;

$i++;
/* Authentication type */
$cfg['Servers'][$i]['auth_type'] = 'cookie';
$cfg['Servers'][$i]['host'] = '127.0.0.1';
$cfg['Servers'][$i]['compress'] = false;
$cfg['Servers'][$i]['AllowNoPassword'] = false;
$cfg['Servers'][$i]['user'] = 'root';
$cfg['Servers'][$i]['password'] = 'root';

$cfg['DefaultLang'] = 'zh_CN';
$cfg['ServerDefault'] = 1;

/**
 * Directories for saving/loading files from server
 */
$cfg['UploadDir'] = '';
$cfg['SaveDir'] = '';


```


1. [解决phpmyadmin配置文件的权限问题](https://blog.csdn.net/Enarm/article/details/57083515)

# centos7 禁用 selinux #

```
sudo vim /etc/selinux/config
```

将 SELINUX=enforcing改为SELINUX=disabled 
设置后需要重启才能生效

参考: [CentOS 7.X 关闭SELinux](https://www.cnblogs.com/activiti/p/7552677.html)

# mysql 5.7 重置 root密码 #

```
mysql -u root -p
```

```
UPDATE mysql.user SET authentication_string=PASSWORD('YOURPASSWOED') WHERE user='root' AND host='localhost';
flush privileges;
exit;
```

重启mysql

```
sudo systemctl restart mysqld
```

#### 参考 ####
1. [MySQL Server 5.7 修改 root 密码](https://www.jianshu.com/p/d9996e22603c)
2. [Reset MySQL 5.7 root password](https://www.techandme.se/reset-mysql-5-7-root-password/)

# centos apache You don't have permission to access / on this server #

1. 关闭 selinux
2. 配置 `/etc/httpd/conf/httpd.conf` 中的两个目录添加 `Allow from all`

最后修改完配置，需要重启 apache

```
sudo systemctl restart httpd
```

参考: [You don't have permission to access / on this server](https://stackoverflow.com/a/17543828)
