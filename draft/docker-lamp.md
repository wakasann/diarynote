docker 按照的 lamp 笔记



```

```





工作目录: `/home/{username}/dockers/lamp`

将docker容器的apache的80 映射为本地主机的81

```
# sudo docker pull mattrayner/lamp
git clone https://github.com/wakasann/docker-lamp.git
cd docker-lamp

# Build the 18.04 image
docker build -t=wakasann/lamp:latest -f ./1804/Dockerfile .

mkdir -p ~/dockers/lamp/{app,mysql}
cd ~/dockers/lamp

# Launch a 18.04 based image
sudo docker run --name lamptest -p "80:80" -p "3306:3306" -p "443:443" -v ${PWD}/app:/app -v ${PWD}/mysql:/var/lib/mysql  wakasann/lamp:latest
```
php 7.3

运行日志:

```
vagrant@vagrant:~/dockers/lamp$ sudo docker run --name lamptest -p "81:80" -p "3306:3306" -v ${PWD}/app:/app -v ${PWD}/mysql:/var/lib/mysql  mattrayner/lamp:latest-1804
sed: can't read /etc/php/7.2/apache2/php.ini: No such file or directory
rm: cannot remove '/var/run/mysqld/mysqld.sock': No such file or directory
=> An empty or uninitialized MySQL volume is detected in /var/lib/mysql
=> Installing MySQL ...
=> Done!
=> Waiting for confirmation of MySQL service startup
=> Creating MySQL admin user with random password
ERROR 1133 (42000) at line 1: Can't find any matching row in the user table
=> Done!
========================================================================
You can now connect to this MySQL Server with YDvRU5FwZf2n

    mysql -uadmin -pYDvRU5FwZf2n -h<host> -P<port>

Please remember to change the above password as soon as possible!
MySQL user 'root' has no password but only allows local connections

enjoy!
========================================================================
/usr/lib/python2.7/dist-packages/supervisor/options.py:298: UserWarning: Supervisord is running as root and it is searching for its configuration file in default locations (including its current working directory); you probably want to specify a "-c" argument specifying an absolute path to a configuration file for improved security.
  'Supervisord is running as root and it is searching '
2019-12-04 02:42:22,994 CRIT Supervisor running as root (no user in config file)
2019-12-04 02:42:22,995 INFO Included extra file "/etc/supervisor/conf.d/supervisord-apache2.conf" during parsing
2019-12-04 02:42:22,997 INFO Included extra file "/etc/supervisor/conf.d/supervisord-mysqld.conf" during parsing
2019-12-04 02:42:23,049 INFO RPC interface 'supervisor' initialized
2019-12-04 02:42:23,050 CRIT Server 'unix_http_server' running without any HTTP authentication checking
2019-12-04 02:42:23,050 INFO supervisord started with pid 1
2019-12-04 02:42:24,053 INFO spawned: 'mysqld' with pid 500
2019-12-04 02:42:24,059 INFO spawned: 'apache2' with pid 501
2019-12-04 02:42:25,707 INFO success: mysqld entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
2019-12-04 02:42:25,708 INFO success: apache2 entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)

```

上面会输出 mysql admin用户的密码





## fix mcrypt.so at php 7.3

进入docker lamp 容器中

```
sudo docker exec -ti lamptest /bin/bash
```

查看 php版本

```
root@43cdda3f409c:/usr/lib/php/20180731# php -v
PHP Warning:  PHP Startup: Unable to load dynamic library 'mcrypt.so' (tried: /usr/lib/php/20180731/mcrypt.so (/usr/lib/php/20180731/mcrypt.so: cannot open shared object file: No such file or directory), /usr/lib/php/20180731/mcrypt.so.so (/usr/lib/php/20180731/mcrypt.so.so: cannot open shared object file: No such file or directory)) in Unknown on line 0
PHP 7.3.12-1+ubuntu18.04.1+deb.sury.org+1 (cli) (built: Nov 28 2019 07:37:16) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.3.12, Copyright (c) 1998-2018 Zend Technologies
    with Zend OPcache v7.3.12-1+ubuntu18.04.1+deb.sury.org+1, Copyright (c) 1999-2018, by Zend Technologies
    with Xdebug v2.8.0, Copyright (c) 2002-2019, by Derick Rethans
```

修复 `Unable to load dynamic library 'mcrypt.so'` 在 php 7.3

```
# 验证 php和 pecl的版本
php -v
pecl version
```

安装 mcrypt 扩展

 Mcrypt [PECL 扩展](http://pecl.php.net/package-info.php?package=mcrypt) 

```
apt-get -y install gcc make autoconf libc-dev pkg-config
apt-get -y install libmcrypt-dev
apt-get install php7.3-dev # 让phpize命令可执行，如果出现phpize: not found需执行这句
pecl install mcrypt
```

当你看到显示的提示:

```
libmcrypt prefix? [autodetect] : 
```

按 [Enter] 回车键 来自动选择。

当通过 pecl 安装成功之后，你应该将`mcrypt.so` 扩展添加到 `php.ini`

输出的内容像:

```
...
Build process completed successfully
Installing '/usr/lib/php/20180731/mcrypt.so'
install ok: channel://pecl.php.net/mcrypt-1.0.3
configuration option "php_ini" is not set to php.ini location
You should add "extension=mcrypt.so" to php.ini
```



从上面的输出获取到安装的路径，并将其加入到 cli和apache php.ini 配置文件中(因Docker lamp已经执行了这一步，只做备忘)

```
sudo bash -c "echo extension=/usr/lib/php/20180731/mcrypt.so > /etc/php/7.3/cli/conf.d/mcrypt.ini"
sudo bash -c "echo extension=/usr/lib/php/20180731/mcrypt.so > /etc/php/7.3/apache2/conf.d/mcrypt.ini"
```



重启apache

```
service apache2 restart
```

验证 mcrypt 已经按照成功

```
$ php -i | grep "mcrypt"
/etc/php/7.3/cli/conf.d/20-mcrypt.ini,
Registered Stream Filters => zlib.*, string.rot13, string.toupper, string.tolower, string.strip_tags, convert.*, consumed, dechunk, convert.iconv.*, mcrypt.*, mdecrypt.*
mcrypt
mcrypt support => enabled
mcrypt_filter support => enabled
mcrypt.algorithms_dir => no value => no value
mcrypt.modes_dir => no value => no value
```

## rsync note

通过ssh的方式，将远程的文件同步到当前服务器的一个文件夹下

```
rsync -v -r -e ssh vagrant@192.168.1.155:/home/vagrant/sh/* /home/vagrant/syncfrom155
```


## References
* [How to install mcrypt in php7.2 / php7.3](https://lukasmestan.com/install-mcrypt-extension-in-php7-2/)
* [Rsync](https://www.jianshu.com/p/07b3998e1f53 )