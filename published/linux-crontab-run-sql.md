---
title: linux 定时执行sql
date: 2019-08-01 20:06:28
tags:
  -  Mysql
  -  crontab
categories:
  - 工具
description:  linux 定时执行sql
---

说明: 放执行脚本的路径是: `/home/vagrant/ssh`

文件夹结构:

```
/home
|_ vagrant
|__ ssh
|___ move_order_old_data.sh
|___ move_order_template.sql
```



## 创建执行sql的shell脚本
我的目的是将一些旧数据搬到历史表中，是直接在1个数据库上执行一些sql语句

| 名称 | 属性 |  说明 |
| ------ | ------ | ------ |
| test_db |  database |  数据库名称 |
| orders | table | 订单表 |
| orders_history | table | 订单历史表(存放冷数据的表) |

创建表sql

```
use test_db;
CREATE TABLE `orders` ( `id` INT NOT NULL AUTO_INCREMENT , `order_no` VARCHAR(50) NOT NULL , `order_date` DATETIME NOT NULL , PRIMARY KEY (`id`)) ENGINE = InnoDB;
CREATE TABLE `orders_history` ( `id` INT NOT NULL AUTO_INCREMENT , `order_no` VARCHAR(50) NOT NULL , `order_date` DATETIME NOT NULL , PRIMARY KEY (`id`)) ENGINE = InnoDB;
```

比如创建了一个`move_order_old_data.sh`文件，该文件的内容如下:

```
#!/bin/bash
# 获取当前日期
CURRENTDATE= `date +%Y%m%d`
# 获取当前日期2个月前的第1天日期
DATE=`date -d "${CURRENTDATE} 2 month ago 1 day ago"  +%Y-%m-%d`
#复制sql模板为一个执行的sql文件,目的是为了可替换的字符串固定是replace_date
cp move_order_template.sql move_order_run.sql
#替换里面的replace_date为1个日期
sed -i "s|replace_date|$DATE|" move_order_run.sql
#在数据库test_db上执行的sql语句
mysql -uusername -p123456 test_db < move_order_run.sql
```

`move_order_template.sql` 文件的内容是

```
INSERT  INTO orders_history 
(`id`, `order_no`, `order_date`) 
SELECT `id`, `order_no`, `order_date` FROM orders 
WHERE  order_date >= '2008-01-01 00:00:00' and order_date <= 'replace_date 23:59:59' ON DUPLICATE KEY UPDATE  `order_no`=VALUES(`order_no`), `order_date`=VALUES( `order_date`);
DELETE FROM `orders` WHERE order_date >= '2008-01-01 00:00:00' and order_date <= 'replace_date 23:59:59';
OPTIMIZE TABLE  `orders`;
OPTIMIZE TABLE  `orders_history`;
```

上面的sql解释:
1. 将 `orders`的记录移入到`order_history`
2. 删除已从`orders`移走的数据
3. 优化 `orders` 表
4. 优化 `orders_history` 表

## 定时

Linxu Crontab定时任务

执行命令

```
crontab -e
```

打开的文件上面有案例

```
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
#
# For more information see the manual pages of crontab(5) and cron(8)
#
# m h  dom mon dow   command
```

| 参数|参数说明 |
| ------ | ------ |
| m | minute(0-57) |
| h  | hour(0-23) |
| dom | day of month(1-31) |
| mon | month (1-12)|
| dow | day of week(0-6)|
| command | user-name command to be executed |

如每1分钟执行1次

```
* * * * * vagrant echo date > /tmp/testdata.log
```

如每2分钟执行1次

```
*/2 * * * * vagrant echo date > /tmp/testdata.log
```


我的是定时每天0点 5分执行定时脚本，所有文件放入到`/home/vagrant/ssh`文件夹中
```
5 0 * * * vagrant sh /home/vagrant/ssh/move_order_old_data.sh
```

保存crontab文件的编辑

重启 crontab 服务使其生效

```
# 重启 cron
sudo service cron restart
# 查看 cron 状态
sudo service cron status
# 列出用户的 crontab
crontab -l
# 给个无效的参数-h，会输出如何使用的提示
crontab -h
```


------
## shell date 命令尝试:

```
TZ='Asia/Shanghai' date -d "`date +%Y%m01` 2 month ago" +%Y%m%d
TZ='Asia/Shanghai' date -d "`date +%Y%m%d` 2 month ago 1 day ago" +%Y%m%d
TZ='Asia/Shanghai' date -d "2019-08-01 2month ago 1 day ago" +%Y%m%d
```


##### References
1. [shell 下执行mysql 命令](https://www.cnblogs.com/wangkangluo1/archive/2012/04/27/2472898.html)
2. [MYSQL：INSERT INTO .. ON DUPLICATE KEY更新多行记录，防止主键重复](https://blog.csdn.net/qq_39806256/article/details/80238065)
3. [Linux crontab命令详解](https://www.cnblogs.com/ftl1012/p/crontab.html)