Permission denied (publickey,gssapi-keyex,gssapi-with-mic) 解决方法

```
sudo vim sudo vim /etc/ssh/sshd_config
```

新增或者修改为

```
PasswordAuthentication yes
```

重启 ssh 服务

```
sudo systemctl restart sshd
```

[Permission denied (publickey,gssapi-keyex,gssapi-with-mic) 解决方法](https://blog.csdn.net/albertjone/article/details/84946557)