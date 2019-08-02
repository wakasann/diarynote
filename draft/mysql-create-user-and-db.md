---
title: Mysql添加用户和数据库
date: 2019-07-30 20:06:28
tags:
  - mysql
categories:
  - Linux
description: Mysql添加用户和数据库
---


1. 先创建数据库
2. 创建用户并赋予一个数据库的所有权限

```
use mysql;
create database databaseName character set utf8;
grant all privileges on databaseName.* to username@'ip' identified by 'password';
```

举例:

```
use mysql;
create database test_db character set utf8;
grant all privileges on test_db.* to testuser@'localhost' identified by '123456';
```

