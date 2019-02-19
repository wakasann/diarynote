## apache httpd.conf alias ##

参考 [Apache alias目录配置](http://www.cnblogs.com/bourneli/archive/2012/11/13/2767522.html)

我的环境是 Ubuntu apache2,配置文件目录在 `/etc/apache2/sites-available/000-default.conf`

在这个配置文件中，添加

```
<Directory /var/www/html>
		Options Indexes FollowSymLinks MultiViews
		AllowOverride All
		Order allow,deny
		allow from all
</Directory>

Alias /testweb /var/www/html/testweb/public

<Directory /var/www/html/testweb/public>
   Options Indexes FollowSymLinks MultiViews
   AllowOverride All
   Require all granted
   Order allow,deny
   allow from all
</Directory>
```

如果启动了 ssl,在 ssl中的配置也是一样的


1. [Apache alias目录配置](http://www.cnblogs.com/bourneli/archive/2012/11/13/2767522.html)
2. [/etc/apache2/sites-enabled/000-default.conf](https://gist.github.com/jnuc093/6507a95d1580eb992d8f)
3. [Alias not working on Apache2.4](https://askubuntu.com/a/757269)


[localhost 500 internal server](https://askubuntu.com/questions/391909/localhost-500-internal-server)

如果本地有 .htaccess，可能需要修改 `Rewritebase /文件夹名称`


