docker ce

參考: [Docker Community Edition 镜像使用帮助](https://mirror.tuna.tsinghua.edu.cn/help/docker-ce/)

ubuntu
```
sudo bash
```
切換到 `root`,如果需要退出，輸入`exit`可以退出


```
waka@ubuntu:~$ systemctl status docker.service
● docker.service - Docker Application Container Engine
   Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
   Active: failed (Result: start-limit-hit) since Sun 2020-05-24 09:42:51 CST; 23s ago
     Docs: https://docs.docker.com
  Process: 5731 ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock (c
 Main PID: 5731 (code=exited, status=1/FAILURE)

May 24 09:42:51 ubuntu systemd[1]: docker.service: Service hold-off time over, scheduling restart.
May 24 09:42:51 ubuntu systemd[1]: Stopped Docker Application Container Engine.
May 24 09:42:51 ubuntu systemd[1]: docker.service: Start request repeated too quickly.
May 24 09:42:51 ubuntu systemd[1]: Failed to start Docker Application Container Engine.
May 24 09:42:51 ubuntu systemd[1]: docker.service: Unit entered failed state.
May 24 09:42:51 ubuntu systemd[1]: docker.service: Failed with result 'start-limit-hit'.
May 24 09:43:00 ubuntu systemd[1]: Stopped Docker Application Container Engine.
May 24 09:43:02 ubuntu systemd[1]: docker.service: Start request repeated too quickly.
May 24 09:43:02 ubuntu systemd[1]: Failed to start Docker Application Container Engine.
May 24 09:43:02 ubuntu systemd[1]: docker.service: Failed with result 'start-limit-hit'.
```

有可能是`/etc/docker/daemon.json` 文件配置錯誤導致的



```
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
```





拉取 gitlab-ce時，出現

```
waka@ubuntu:~$ sudo docker pull gitlab/gitlab-ce:11.1.4
Error response from daemon: manifest for gitlab/gitlab-ce:11.1.4 not found: manifest unknown: manifest unknown
```
通過查看 docker hub上的[gitlab ce tag](https://hub.docker.com/r/gitlab/gitlab-ce/tags?page=10)才發現，版本號是需要帶`-ce.0`或其它字符的

如: 11.1.4

```
waka@ubuntu:~$ sudo docker pull gitlab/gitlab-ce:11.1.4-ce.0
```

重置gitlab ce root 用戶的密碼

進入 docker 容器命令行內，執行

```
# Form Docker container /bin/bash
# 執行下面的命令啓動一個 Ruby on Rails console,等待 console加載完成
root@44b33d22e70f:/# gitlab-rails console production
-------------------------------------------------------------------------------------
 GitLab:       11.1.4 (63daf37)
 GitLab Shell: 7.1.4
 postgresql:   9.6.8
-------------------------------------------------------------------------------------
Loading production environment (Rails 4.2.10)
irb(main):001:0> user = User.where(id: 1).first #這裏通過用戶id查詢用戶，也可以通過email或用戶名查詢
=> #<User id:1 @root>
irb(main):002:0> user.password="12345678" #設置密碼
=> "12345678"
irb(main):003:0> user.password_confirmation="12345678" #確認密碼
=> "12345678"
irb(main):004:0> user.save! # 保存修改
Enqueued ActionMailer::DeliveryJob (Job ID: 38850b0d-7690-47b7-b5c9-9cf975bae8fd) to Sidekiq(mailers) with arguments: "DeviseMailer", "password_change", "deliver_now", gid://gitlab/User/1
=> true
irb(main):005:0> quit #退出 Ruby on Rails console
```


## Remark
1. 阿里雲源: [mirror](https://developer.aliyun.com/mirror/)
2. [USTC Mirror Help](http://mirrors.ustc.edu.cn/help/index.html)
## references
1. [Docker 无法启动](https://q.cnblogs.com/q/109675/)
2. [Docker镜像加速，设置国内源](https://www.cnblogs.com/happy4java/p/11206839.html)