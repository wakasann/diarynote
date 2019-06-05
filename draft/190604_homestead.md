# Homestead 安装其它的PHP版本

运行环境:

系统: win10

软件: 

1. virtualbox 6.2
2. vagrant 2.2.4 
3. homestead 7.1.0 

```
sudo apt-get update
sudo apt-get -y install php5.6-mysql php5.6-fpm php5.6-mbstring php5.6-xml php5.6-curl
```

安装中出现
```
Fetched 3,466 kB in 6min 24s (9,022 B/s)                                                                                       
E: Failed to fetch http://ppa.launchpad.net/ondrej/php/ubuntu/pool/main/p/php5.6/php5.6-common_5.6.40-8+ubuntu18.04.1+deb.sury.org+1_amd64.deb  Connection timed out [IP: 91.189.95.83 80]
E: Unable to fetch some archives, maybe run apt-get update or try with --fix-missing?
```

这是因为下载超时导致的，继续执行

```
sudo apt-get -y install php5.6-mysql php5.6-fpm php5.6-mbstring php5.6-xml php5.6-curl
```

进行重试进行安装 php 5.6

最后执行命令，进行 重启 nginx和php5.6-fpm

```
/etc/init.d/nginx restart
/etc/init.d/php5.6-fpm restart
```

##### Reference

1. [How to update PHP version in homestead](https://stackoverflow.com/a/40103584)
2. [Cannot add PPA: 'ppa:ondrej/php5-5.6'](https://stackoverflow.com/a/40677790)



# Ubuntu 16.04 PHP5.6



安装 Apache + PHP 5.6 + mysql 5.7

系统: Ubuntu 16.04

1. 替换源

```
sudo cp /etc/apt/sources.list /etc/apt/source.list_bak
sudo vim /etc/apt/sources.list
```

阿里源:

```
# deb cdrom:[Ubuntu 16.04 LTS _Xenial Xerus_ - Release amd64 (20160420.1)]/ xenial main restricted
deb-src http://archive.ubuntu.com/ubuntu xenial main restricted #Added by software-properties
deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted
deb-src http://mirrors.aliyun.com/ubuntu/ xenial main restricted multiverse universe #Added by software-properties
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted multiverse universe #Added by software-properties
deb http://mirrors.aliyun.com/ubuntu/ xenial universe
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates universe
deb http://mirrors.aliyun.com/ubuntu/ xenial multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse #Added by software-properties
deb http://archive.canonical.com/ubuntu xenial partner
deb-src http://archive.canonical.com/ubuntu xenial partner
deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted multiverse universe #Added by software-properties
deb http://mirrors.aliyun.com/ubuntu/ xenial-security universe
deb http://mirrors.aliyun.com/ubuntu/ xenial-security multiverse
```

2. 安装 apache2

```
sudo apt-get install apache2
```

3. 安装 php 5.6

```
sudo apt-get install python-software-properties
sudo apt-get install software-properties-common
sudo apt-get update

sudo add-apt-repository ppa:ondrej/php
sudo apt-get update
sudo apt-get -y install libapache2-mod-php5.6 php5.6 php5.6-mcrypt php5.6-mbstring php5.6-curl php5.6-cli php5.6-mysql php5.6-gd php5.6-intl php5.6-xsl php5.6-zip
```

国内安装时可能会比较慢，需要耐心的等待


4. 安装mysql-client,mysql-server

```
sudo apt-get install mysql-server mysql-client
```

安装过程中，会跳出 输入mysql `root` 用户的密码的界面

5. 设置 mysql的 sql_model

编辑 `/etc/mysql.cnf`,在文件内容的末尾添加

```
[mysqld]
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
max_allowed_packet=100M
```



注: 如果需要 mysql中的 date 和datetime可以为 `0000-00-00 00:00:00`的格式的话，需要将上面的 `NO_ZERO_IN_DATE,NO_ZERO_DATE` 移除掉



避免出现 `#3065 - Expression #1 of ORDER BY clause is not in SELECT list` 这样的错误提示


##### Reference

1. [Ubuntu 16.04 几个国内更新源](https://www.cnblogs.com/bovenson/p/5752213.html)
2. [ubuntu16.04安装php5](https://www.cnblogs.com/yxhblogs/p/5842568.html)
3. [Cannot add PPA: 'ppa:ondrej/php5-5.6'](https://stackoverflow.com/a/40677790)
4. [MySQL 5.7 以上版本默认禁止 0000-00-00 的日期](https://www.cnblogs.com/dannyyao/p/8126193.html)