```csharp
$ docker run -d  -p 443:443 -p 80:80 -p 222:22 --name gitlab --restart always -v /home/gitlab/config:/etc/gitlab -v /home/gitlab/logs:/var/log/gitlab -v /home/gitlab/data:/var/opt/gitlab gitlab/gitlab-ce
# -d：后台运行
# -p：将容器内部端口向外映射
# --name：命名容器名称
# -v：将容器内数据文件夹或者日志、配置等文件夹挂载到宿主机指定目录
    
    
sudo docker run  \
    --publish 8780:80 --publish 8722:22 \
    --name gitlab \
    --restart always \
    --volume ${PWD}/gitlab/config:/etc/gitlab \
    --volume ${PWD}/gitlab/logs:/var/log/gitlab \
    --volume ${PWD}/gitlab/data:/var/opt/gitlab \
    gitlab/gitlab-ce:11.1.4-ce.0
        
        mkdir -p gitlab/{config,log,data}
```





