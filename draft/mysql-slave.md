学习配置和使用 mysql 主从数据库

### 实验环境: 

virtualbox + ubuntu 16.04.3 + mysql 5.7

```
sudo apt-get install mysql-server mysql-client
```

然后导出虚拟机，再导入虚拟机，至此就有2台虚拟服务器了。

`mysql -V` 查看 mysql版本，最好采用相同版本的mysql。

master 服务器ip: 192.168.1.168
slave 服务器ip: 192.168.1.113
同步的数据库: zhucong_test
同步的用户名和密码都是: master_test

### 主(master)服务器配置:

1. 配置文件修改

主mysql,`/etc/mysql/my.cnf`

```
[mysqld]
#sql_mode=STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
#max_allowed_packet=100M
log-bin = mysql-bin
server-id=1

#不同步的数据库
binlog-ignore-db = mysql
binlog-ignore-db = test
binlog-ignore-db = information_schema
binlog-ignore-db = sys
#同步的数据库
binlog-do-db = zhucong_test
```

重启主服务器的mysql.

```
show master status;
```

修改 `bind-address`,`/etc/mysql/mysql.conf.d/mysqld.cnf`,找到 `bind-address           = 127.0.0.1`并在该行的前面加`#`注释掉，变成`#bind-address           = 127.0.0.1`，重启主服务器的mysql。这一步是为了让从服务器可以连接到主服务器的mysql。

2. 主服务器添加一个用于主从的mysql用户，并赋予 `REPLICATION SLAVE`权限。

```
mysql > create user 'master_test'@'%' identified by 'master_test';
mysql > GRANT REPLICATION SLAVE ON *.* TO 'master_test'@'%';
mysql > flush privileges; #刷新权限
```



### 配置从(slave)服务器

创建要同步数据库

```
create database zhucong_test charset utf8 collate utf8_general_ci;
```
**把master数据库的数据dump一份导入到slave(非常重要)**

```
mysqldump -h 192.168.1.168 -uroot -p zhucong_test > source.sql #导出主(master)服务器的zhucong_test db的数据到从(slave)服务器上
mysql  -uroot -p  zhucong_test < source.sql #导入数据到从(slave)服务器的zhucong_test数据库中
```

修改从(slave)服务器mysql的配置文件,`/etc/mysql/my.cnf`

```
[mysqld]
#sql_mode=STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
#max_allowed_packet=100M
log-bin = mysql-bin
server-id=2

expire_logs_days=10
#忽略同步的数据库
binlog-ignore-db = mysql
binlog-ignore-db = test
binlog-ignore-db = information_schema
binlog-ignore-db = sys
#同步的数据库
replicate-do-db = zhucong_test
log-slave-updates
slave-skip-errors=all
slave-net-timeout=60
```

>  重启从服务器的mysql,使用`CHANGE MASTER TO`语句指定主服务器信息，不要在配置文件中配置。该语言可以替代在配置文件中提供主服务器的信息，另外，不需要停止服务器，便可以为从服务器指定不同的主服务器。

```
CHANGE MASTER TO MASTER_HOST='192.168.1.168',MASTER_USER='master_test',MASTER_PASSWORD='master_test',MASTER_LOG_FILE='mysql-bin.000001',MASTER_LOG_POS=0;
```

> 此处指定 MASTER_LOG_POS的值为0，因为要从日志的开始位置开始读。



```
start slave; # 开始复制
show slave status\G; # 查看从服务器的设置是否正确
```

当从服务器的mysql显示的状态:

```
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
```
说明配置成功了


1. `Last_IO_Error: Fatal error: The slave I/O thread stops because master and slave have equal MySQL server UUIDs; these UUIDs must be different for replication to work.`



解决方法: 停止从服务器的mysql，并将`auto.cnf` 文件删除。如:

```
sudo service mysql stop
sudo mv /var/lib/mysql/auto.cnf /var/lib/mysql/auto.cnf.bak
```



参考书籍:《PHP核心技术与最佳实践》


## Refences

1. [MHA集群报错：The slave I/O thread stops because master and slave have equal MySQL server UUIDs](https://blog.csdn.net/sunbocong/article/details/81634296) 了解到mysql 5.7配置文件的地址
2. ["Last_IO_Error: Fatal error: The slave I/O thread stops because master and slave have equal MySQL server UUIDs](https://www.cnblogs.com/datalife/p/8026928.html) 从服务器中 相同 server uuids的解决方法
3. [mysql 主从配置，slave master 设置](https://blog.csdn.net/qq_27517377/article/details/98610374)
4. [mysql主从简单配置](https://www.cnblogs.com/lelehellow/p/9633315.html)
5. [MySQL.ERROR 1133 (42000): Can't find any matching row in the user table](https://www.cnblogs.com/Charles-Yuan/p/9618606.html)
6. [MySQL创建用户和授权](https://www.cnblogs.com/wangyueping/p/11258028.html)