php curl Problem with the SSL CA cert (path access rights) 



## 前言

运行环境: centos6 + Apache 2 + mysql 5.6 + php 5.6

出错地方:  使用php curl post 请求 onesignal的创建通知 api,返回 `Problem with the SSL CA cert (path? access rights?) `,导致推送消息失败

## 内容

php curl请求 https的url, 通过 curl_error 方法，捕获到 `Problem with the SSL CA cert (path? access rights?) `的错误,通过网上搜索资料并自己尝试之后，

我的解决方法是 将 curl 方式，改为使用 `file_get_content` 方法进行请求

```
$rest_key = '';
$fields = array('param' => 1);
$fields = json_encode($fields);
 // print("\nJSON sent:\n");
 // print($fields);

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, "https://onesignal.com/api/v1/notifications");
curl_setopt($ch, CURLOPT_HTTPHEADER, array(
'Content-Type: application/json',
'Authorization: Basic ' . $rest_key
));
curl_setopt($ch, CURLOPT_RETURNTRANSFER, TRUE);
curl_setopt($ch, CURLOPT_HEADER, FALSE);
curl_setopt($ch, CURLOPT_POST, TRUE);
curl_setopt($ch, CURLOPT_POSTFIELDS, $fields);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, FALSE);
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false);

$response = curl_exec($ch);

if($response === false){
echo 'Curl Error:'.curl_error($ch);
}

curl_close($ch);

```

输出的结果是: `Curl Error:Problem with the SSL CA cert (path access rights) `

将curl post的代码改为

```
$rest_key = '';
$fields = array('param' => 1);
$fields = json_encode($fields);
 // print("\nJSON sent:\n");
 // print($fields);

$response = file_get_contents('https://onesignal.com/api/v1/notifications', null, stream_context_create(
            array(
                'http' => array(
                    'method' => 'POST',
                    'header' => 'Content-Type: application/json' . "\r\n"
                    . 'Content-Length: ' . strlen($fields) . "\r\n"
                    . 'Authorization: Basic ' . $rest_key . "\r\n",
                    'content' => $fields,
                ),
            )));
```

------

以下是在服务器尝试的笔记: 

参考网上的资料:

1. 重新安装 openssl,`ca-certificates` 软件包:  

```
cp /etc/pki/tls/certs/ca-bundle.crt /etc/pki/tls/certs/ca-bundle.crt.bak # 备份证书文件
yum reinstall openssl ca-certificates -y
# rm -f /etc/ssl/certs/ca-bundle.crt && yum reinstall -y ca-certificates
service httpd restart # 重启apache web服务器

```

通过 `ls -l /etc/pki/tls/certs/ca-bundle.crt` 命令列出该文件信息，文件的修改日期好像没有变化。php curl  post https url 还是返回一样的错误。

2. 重启服务器方法

尝试过`reboot`命令重启 服务器，curl 再请求也是一样的错误。php curl 一样的错误

3. 使用curl的证书进行替换

```
cp /etc/pki/tls/certs/ca-bundle.crt /etc/pki/tls/certs/ca-bundle.crt.bak2 # 备份证书文件
curl http://curl.haxx.se/ca/cacert.pem -o /etc/pki/tls/certs/ca-bundle.crt 
curl --remote-name --time-cond cacert.pem https://curl.haxx.se/ca/cacert.pem -O /root/ca-certificates.crt
cp cacert.pem /etc/ssl/certs/ca-certificates.crt
```

```
curl -vvv https://onesignal.com/
```

curl 命令可能是正常运行，可是通过 php的curl 方式请求 https，返回的错误信息还是一样的。

4. 移动nssdb文件夹

```
mv /etc/pki/nssdb /etc/pki/nssdb.old
```

3. 更新 directadmin ssl

 https://help.directadmin.com/item.php?id=15 

因为服务器使用的是 `DirectAdmin` 控制面板，想是否是directAdmin的 执行了以下的命令，没有任何效果

```
/usr/bin/openssl req -x509 -sha256 -newkey rsa:4096 -keyout /usr/local/directadmin/conf/cakey.pem -out /usr/local/directadmin/conf/cacert.pem -days 9000 -nodes
chown diradmin:diradmin /usr/local/directadmin/conf/cakey.pem
chmod 400 /usr/local/directadmin/conf/cakey.pem
```
4. 下载对应域名的的ssl证书作为curl 的证书文件

参考 [SSL Certificates](https://curl.haxx.se/docs/sslcerts.html) 第3点,将 `onesignal.com`的ssl证书作为 curl的证书，但还是失败了

命令:

```
rpm -V ca-certificates
cd /tmp
openssl s_client -showcerts -servername onesignal.com -connect onesignal.com:443 > cacert.pem
ls -l /tmp/cacert.pem
mv cacert.pem cacert_one.pem 
cp cacert.pem /var/www/html/test
```

php 代码:

```
$rest_key = '';
$fields = array('param' => 1);
$fields = json_encode($fields);
 // print("\nJSON sent:\n");
 // print($fields);
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, "https://onesignal.com/api/v1/notifications");
curl_setopt($ch, CURLOPT_HTTPHEADER, array(
            'Content-Type: application/json',
            'Authorization: Basic ' . $rest_key
        ));
curl_setopt($ch, CURLOPT_RETURNTRANSFER, TRUE);
curl_setopt($ch, CURLOPT_HEADER, FALSE);
curl_setopt($ch, CURLOPT_POST, TRUE);
curl_setopt($ch, CURLOPT_POSTFIELDS, $fields);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, true);
curl_setopt($ch,CURLOPT_SSL_VERIFYHOST,1);
curl_setopt($ch, CURLOPT_CAINFO, __DIR__.'/cacert.pem');
$response = curl_exec($ch);

if($response === false){
    echo 'Curl Error:'.curl_error($ch);
}

curl_close($ch);
```

5. 使用`update-ca-trust` 命令



```
update-ca-trust check
update-ca-trust force-enable
update-ca-trust extract
service httpd restart
update-ca-trust force-disable
curl-config --ca
ls -l /etc/pki/tls/certs/ca-bundle.crt
```

感觉使用这个命令，是可以更新 `/etc/pki/tls/certs/ca-bundle.crt` 文件的。

6. 在 php使用非curl 方式请求 https 和 application/json



```
$rest_key = '';
$fields = array('param' => 1);
$fields = json_encode($fields);
$response = file_get_contents('https://onesignal.com/api/v1/notifications', null, stream_context_create(
            array(
                'http' => array(
                    'method' => 'POST',
                    'header' => 'Content-Type: application/json' . "\r\n"
                    . 'Content-Length: ' . strlen($fields),
                    'content' => $fields,
                ),
            )));
```

## 笔记

header `Content-type` 笔记:

1. `application/x-www-form-urlencode` : 作为普通表单的形式进行提交，key/value的形式
2. `application/json` : 通过json字符串作为参数
3. `multipart/form-data` :  窗体数据被编码为一条消息，页上的每个控件对应消息中的一个部分，这个一般文件上传时用



## References

1. [【原创】PHP Curl Problem with the SSL CA cert (path? access rights?) 解决](http://www.blogdaren.com/post-2357.html)
2. [PHP Problem with the SSL CA cert (path? access rights?)](https://www.cnblogs.com/zhanmeiliang/p/6196746.html)
3. [curl_error](https://www.php.net/manual/zh/function.curl-error.php)
4. [POSTing JSON Data With PHP cURL](https://lornajane.net/posts/2011/posting-json-data-with-php-curl#comment-2630)  通过这个评论，清楚 `application/json` 头的请求代码应该怎么去写
5. [PHP: How to send a POST request with parameters](http://fczaja.blogspot.com/2011/07/php-how-to-send-post-request-with.html) 学习到除curl,php的`file_get_contents()`+ `stream_context_create()`结合起来也可以发送请求
6. [ajax中设置contentType: “application/json”的作用](https://www.cnblogs.com/tizi/p/8758565.html) 扩展了 请求头加`application/json`的认知
7. [application/x-www-form-urlencode/multipart/form-data](https://www.cnblogs.com/dd86/p/11192029.html) 请求头`application/x-www-form-urlencode` 的摘抄和学习