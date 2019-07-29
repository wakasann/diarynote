# Centos 添加 sudo 用户

说明以下的 `<username>`字样 是 用户名

## 新增用户

```
# 终端命令
useradd <username>
```

## 给新增的用户设置密码

```
# 终端命令
passwd <username>
```

## 加入sudo

`visudo`打开的是`/etc/sudoers`文件

```
# 终端命令
# 打开 sudo 配置文件
visudo
```

在打开的`sudo配置文件`中找到 `root ALL=(ALL) ALL`这一行，并在这行下面新增以下格式的内容

```
<username> ALL=(ALL) ALL
```

注: 上面的命令是使用`root`用户执行，如果不是`root`用户，请在执行的命令前加`sudo`

## 切换用户:

```
su <username>
```
切换成功之后，可以尝试运行需要 root权限的命令，如:`sudo yum -h`

#### References
1. [CentOS添加用户并加入sudo权限](https://www.cnblogs.com/stream886/p/11123837.html)