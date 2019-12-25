cpanle/Apache 强制http跳转到https

因为租的虚拟主机是使用Cpanel,按照网上找的文章,处理的步骤如下:

1.  打开Cpanel面板—文件管理器—设置(在页面的右上角)—勾选`显示隐藏文件(dotfiles)`—save 。 
2.  找到网站所在的目录，打开`.htaccess`，如没有，新建一个`.htaccess`。修改以下项 

 无论是HTTP还是不带WWW，都将跳转到HTTPS的WWW形式。 

```
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteBase /

RewriteCond %{SERVER_PORT} 80
RewriteRule ^(.*)$ https://www.wakasann.com/$1 [R,L]
RewriteCond %{HTTP_HOST} ^wakasann.com [NC]
RewriteRule ^(.*)$ http://www.wakasann.com/$1 [L,R=301]
</IfModule>
```

3.  保存文件。测试能否强制跳转。 
4.  用SSL工具检测生效   在线检测工具 : [https://www.ssllabs.com/](https://www.ssllabs.com/)

## References

1. [ Cpanel开启强制http跳转https ](https://eggjun.com/cpanel-httptohttps/)
2. [cPanel主机面板安装SSL证书实现HTTPS网址访问](https://www.laozuo.org/7755.html)
3. [cPanel/Apache 强制开启https访问]( [https://www.feaink.cn/2019/09/03/cpanel-apache-%E5%BC%BA%E5%88%B6%E5%BC%80%E5%90%AFhttps%E8%AE%BF%E9%97%AE/](https://www.feaink.cn/2019/09/03/cpanel-apache-强制开启https访问/) )