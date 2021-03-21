```
server {
		listen       8092;
        server_name  192.168.1.108:8092;
		autoindex on;
		root   L:/;
		charset utf-8,gbk;
		
		location /software {
			root   L:/;
			#Nginx日志目录
     		 autoindex on;
         #打开目录浏览功能
         autoindex_exact_size off;
         #默认为on，显示出文件的确切大小，单位是bytes
         #显示出文件的大概大小，单位是kB或者MB或者GB
         autoindex_localtime on;
         #默认为off，显示的文件时间为GMT时间。
         #改为on后，显示的文件时间为文件的服务器时间
         add_header Cache-Control no-store;
         #让浏览器不保存临时文件
       }
	}
```

