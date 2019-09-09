处理Codeigniter CLI模式无法读取通过Apache写入的文件缓存

运行环境: Ubuntu 16.04 + PHP 5.6.40 + Apache/2.4.18
Codeigniter: 3.1.10

------

Codeigniter 3.1.10，缓存驱动方式是文件方式

遇到的问题是:通过浏览器访问控制器的方法可以写入缓存文件，当尝试通过CLI模式初始化文件缓存时，无法读取缓存，最终想到的是只能 通过 `curl` 请求，并判断是内网访问，才能执行这个方法


```
A PHP Error was encountered

Severity:    Warning
Message:     file_get_contents(/var/www/html/codeigniter3/application/cache/cache_test): failed to open stream: Permission denied
Filename:    /var/www/html/codeigniter3/system/libraries/Cache/drivers/Cache_file.php
Line Number: 275
```

尝试过给 `cache` 文件夹的拥有者设置为 Apache的`www-data`并给了`755`权限，在 CLI模式下执行，得到的依然是上面的错误

解决方法:

在 `helpers` 文件夹中放的 帮助文件，并在`application/autoload.php`中自动加载这个帮助文件
```
if(!function_exists('get_real_ip')){
	function get_real_ip(){  
	    $ip=false;  
	    if(!empty($_SERVER["HTTP_CLIENT_IP"])){  
	        $ip = $_SERVER["HTTP_CLIENT_IP"];  
	    }  
	    if (!empty($_SERVER['HTTP_X_FORWARDED_FOR'])) {  
	        $ips = explode (", ", $_SERVER['HTTP_X_FORWARDED_FOR']);  
	    if ($ip) { array_unshift($ips, $ip); $ip = FALSE; }  
	        for ($i = 0; $i < count($ips); $i++) {  
	        if (!preg_match ("^(10|172\.16|192\.168)\.", $ips[$i])) {
	            $ip = $ips[$i];  
	         break;  
	        }  
	    }  
	    }  
	    return ($ip ? $ip : $_SERVER['REMOTE_ADDR']);  
	}	
}
```
控制器

```
<?php

/**
* 每天凌晨需要初始化文件緩存
*/
class Init_daily_cache extends CI_Controller {

	public function index(){
	    if(is_cli()){
	        exit('can not Run at cli');
        }
        $ip =  get_real_ip();
        $intranet_ip = array('127.0.0.1','::1');
        //可以先调试在当前服务器的ip,然后再修改$intranet_ip 变量的值
        //echo $ip;exit();
        if(!in_array($ip, $intranet_ip)){
        	exit('不是内网访问，无须的请求');
        }
        //code
        $this->cache->file->save('cache_test', 'test',3600);
	}
}
```



尝试执行:



```
vagrant@vagrant:/var/www/html/codeigniter3$ curl http://localhost/codeigniter3/index.php/init_daily_cache
::1
```
尝试成功

下一步通过Linux Crontab 定时 每2个小时执行1次这个请求，自动更新缓存

```
crontab -e
```

在打开的 contable编辑窗口添加

```
* */2 * * * curl http://localhost/codeigniter3/index.php/init_daily_cache >> /dev/null 2>&1
```

最后重启cron服务

```
sudo service cron reload
sudo service cron restart
```


