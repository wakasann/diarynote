---
title: Centos Apache 80 代理Tomcat 8080端口
date: 2019-08-03 20:06:28
tags:
  - proxy
  - apache
categories:
  - Linux
description:  Centos Apache 80 代理Tomcat 8080端口
---

运行环境:Centos 6.5
Apache: 2.2.5

1. 开启apache proxy的相应模块

编辑 `/etc/httpd/conf/httpd.conf`文件

```
sudo vim /etc/httpd/conf/httpd.conf
```

查看模块中，带有`proxy`字样的开头是否有`#`号，如果没有，代表是已经开启`proxy`模块了

```
LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_balancer_module modules/mod_proxy_balancer.so
LoadModule proxy_ftp_module modules/mod_proxy_ftp.so
LoadModule proxy_http_module modules/mod_proxy_http.so
LoadModule proxy_ajp_module modules/mod_proxy_ajp.so
LoadModule proxy_connect_module modules/mod_proxy_connect.so
```

2. apache vhost 添加 装发功能

```
<VirtualHost *:80>
    ServerName www.tomcat.test
    ServerAlias www.tomcat.test tomcat.test
    ServerAdmin email@example.com
	ProxyPreserveHost On
    ProxyRequests Off
    ProxyPass / ajp://localhost:8009/
</VirtualHost>
```

访问 域名应该就可以了

因为尝试了使用 `http`形式的代理

```
ProxyPass / http://localhost:8080
ProxyPassReverse/  http://localhost:8080
```

但是如果是域名是外网的，访问 Apache 80 端口，Chrome 的Console 会提示 类似 无法加载 `http://localhost:8080/test/js/web.js`的错误。


##### References
1. [apache 80 端口 反向代理 tomcat 8080端口](https://www.cnblogs.com/qianlizeguo/p/6836841.html)
2. [使用Apache 80端口代理tomcat的8080端口](https://my.oschina.net/u/818053/blog/886567) 提到Tomcat 官网文档中，有关于 Proxy的内容
3. [apache二级域名80端口通过代理映射到Tomcat8080端口](https://www.centos.bz/2017/09/apache%E4%BA%8C%E7%BA%A7%E5%9F%9F%E5%90%8D80%E7%AB%AF%E5%8F%A3%E9%80%9A%E8%BF%87%E4%BB%A3%E7%90%86%E6%98%A0%E5%B0%84%E5%88%B0tomcat8080%E7%AB%AF%E5%8F%A3/) 通过它，才尝试了 ajp的连接方式
4. [Apache 整合 Tomcat (首先Apache 发布的是PHP项目，占用端口80，tomcat 发布的是Java 项目，占用端口8080)](https://www.cnblogs.com/woxiangxintj/p/4914661.html) 因为底下的话而特意加入进来的