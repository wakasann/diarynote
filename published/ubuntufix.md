使用ubuntu的一些操作笔记20191203

## 前言

环境: virtualbox + Ubuntu 16.04

情况: 
* 可以进入虚拟机中Ubuntu系统的桌面，但是外部可以访问到 ssh，输入正确的用户名和密码无法登录
* 无法正常启动 Apache

最后解决方法是 增加一个新用户并分配为`sudoers`,然后外部可以通过ssh 方式可以登录进服务器中

------

## Apache  无法启动的修复笔记

```
apachectl configtest
```

输出:

```
AH00526: Syntax error on line 109 of /etc/apache2/apache2.conf:
Error:\tApache has not been designed to serve pages while\n\trunning as root.  There are known race conditions that\n\twill allow any local user to read any file on the system.\n\tIf you still desire to serve pages as root then\n\tadd -DBIG_SECURITY_HOLE to the CFLAGS env variable\n\tand then rebuild the server.\n\tIt is strongly suggested that you instead modify the User\n\tdirective in your httpd.conf file to list a non-root\n\tuser.\n
Action 'configtest' failed.
The Apache error log may have more information.
```

在 `/etc/apache2/apache2.conf` 109行左右是

```
# These need to be set in /etc/apache2/envvars
User ${APACHE_RUN_USER}
Group ${APACHE_RUN_GROUP}
```

apache的环境变量文件`/etc/apache2/envvars`

修改里面的用户名为稍后需要新增的新用户名`www-data2`

```
# /etc/init.d/apache2, /etc/logrotate.d/apache2, etc.
#export APACHE_RUN_USER=www-data
#export APACHE_RUN_GROUP=www-data
export APACHE_RUN_USER=www-data2
export APACHE_RUN_GROUP=www-data
```

然后新增用户:

```
# add a user name is www-data2
sudo useradd www-data2
# set www-data2 user nologin
sudo usermod -s /sbin/nologin www-data2
# set www-data2 user to www-data usergroup
sudo usermod -a -G www-data
#set www-data2 user home directory is /var/www
sudo usermod -d /var/www www-data2
```

新增完用户之后，然后重启 apache `sudo service apache2 restart`

------

apt install 过程中输出

```
WARN: /etc is world writable!
WARN: /etc is group writable!
WARN: /etc/default/ufw is world writable!
WARN: /etc/default/ufw is group writable!
WARN: /etc/default is world writable!
WARN: /etc/default is group writable!
WARN: /lib/ufw/ufw-init is world writable!
WARN: /lib/ufw/ufw-init is group writable!
WARN: /lib/ufw is world writable!
WARN: /lib/ufw is group writable!
WARN: /lib is world writable!
WARN: /lib is group writable!
```

重新给上面提示的文件夹恢复文件夹权限

```
sudo chmod 755 /etc/ufw
sudo chmod 644 /etc/ufw/* -R
sudo chmod 755 /lib/ufw
sudo chmod 644 /lib/ufw/* -R
```

## References

* [Linux useradd 与 adduser的区别， /sbin/nologin 与 /bin/bash](https://blog.csdn.net/danson_yang/article/details/65629948)
* [Ubuntu Linux Add a User To Group www-data ( Apache Group )](https://www.cyberciti.biz/faq/ubuntu-add-user-to-group-www-data/)
* [bash-cd-ssh-permission-denied](https://unix.stackexchange.com/a/146195)
* [Loggin in ssh server: Permission denied, please try again](https://serverfault.com/a/288237) `su - username`

