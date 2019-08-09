---
title: Ubuntu vhost不执行php小记
date: 2019-08-09 20:06:28
tags:
  -  linux
  -  lamp
categories:
  - Linux
description:  Ubuntu vhost不执行php小记
---

运行环境:
Ubuntu : 16.04
PHP: 5.6.36
Apache: 2.4.18


出现`/var/www/html` 文件夹下的 php文件能够执行

vhost 配置文件的`DocumentRoot`的路径格式是`/home/USERNAME/public_html`的，访问时，输出 php源码或者下载php文件

**解决方法**: 
1. 不使用`/home/USERNAME/public_html`这样的格式，改为如:`/home/USERNAME/www` 这样格式
2. 修改 `/etc/apache2/mods-available/php5.6.conf`文件，使用`#`号注释掉 `从<IfModule ...> 到 </IfModule>`的行数

最后重启 `apache`

原因是: `/etc/apache2/mods-available/php5.6.conf` 文件有一段内容是:

```
# Running PHP scripts in user directories is disabled by default
# 
# To re-enable PHP in user directories comment the following lines
# (from <IfModule ...> to </IfModule>.) Do NOT set it to On as it
# prevents .htaccess files from disabling it.
<IfModule mod_userdir.c>
    <Directory /home/*/public_html>
        php_admin_flag engine Off
    </Directory>
</IfModule>
```

我自己对上面注释的翻译

```
# 在用户的目录中运行php脚本是默认禁止的
#
# 为了在用户的目录重新启用php 注释以下行
# (从 <IfModule ...> 到 </IfModule>.) 不要将它设置为 On
# 预防 .htaccess 文件来禁止它
<IfModule mod_userdir.c>
    <Directory /home/*/public_html>
        php_admin_flag engine Off
    </Directory>
</IfModule>
```



------


在 `/etc/apache2/sites-available` 文件夹下添加了一个 `www.test.com.conf`的vhost文件

```
<VirtualHost *:80>
    ServerName www.test.com
    ServerAlias www.test.com test.com
    DocumentRoot /home/test/public_html
    <Directory /home/test/public_html>
             Options  FollowSymLinks MultiViews
             AllowOverride All
             Require all granted
             Order allow,deny
             allow from all
    </Directory>


    ErrorLog /var/log/apache2/domain/www.test.com.error.log
    CustomLog /var/log/apache2/domain/www.test.comaccess.log combined
</VirtualHost>
```

在 `/home/test/public_html/`下有一个 `info.php`文件

```
<?php
phpinfo();
```

访问 [http://www.test.com/info.php](http://www.test.com/info.php) 的时候，却显示出 php 源码，没有执行 php

后来将 vhost配置文件中的`/home/test/public_html` 中的`public_html` 改为为`www`，对应的文件夹重命名

```
mv /home/test/public_html /home/test/www
```


修改之后，`/etc/apache2/sites-available/www.test.com.conf`文件的内容是

```
<VirtualHost *:80>
    ServerName www.test.com
    ServerAlias www.test.com test.com
    # only replace public_html to www
    DocumentRoot /home/test/www
    <Directory /home/test/www>
             Options  FollowSymLinks MultiViews
             AllowOverride All
             Require all granted
             Order allow,deny
             allow from all
    </Directory>

    ErrorLog /var/log/apache2/domain/www.test.com.error.log
    CustomLog /var/log/apache2/domain/www.test.comaccess.log combined
</VirtualHost>
```




然后重启 apache

```
sudo service apache2 restart
```

然后再试访问 [http://www.test.com/info.php](http://www.test.com/info.php) 就输出 php信息了


