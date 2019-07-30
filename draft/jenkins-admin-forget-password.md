Jenkins忘记 admin 密码

进入 jenkins home(我的是`/var/jenkins_home`)路径下的`users` 文件夹

```
cd /var/jenkins_home/users
sudo vim admin/config.xml
```

找到打开文件的`hudson.security.HudsonPrivateSecurityRealm_-Details`

```
<hudson.security.HudsonPrivateSecurityRealm_-Details>
      <passwordHash>#jbcrypt:$2a$10$fFGna8XSkGzxE056Uo8SxuNBiVo6/W6fZB1CzbzZHrYay7hgI5/FW</passwordHash>
    </hudson.security.HudsonPrivateSecurityRealm_-Details>
```

将`passwordHash`里面的值替换为`#jbcrypt:$2a$10$MiIVR0rr/UhQBqT.bBq0QehTiQVqgNpUGyWW2nJObaVAM/2xSQdSq`,保存重启 Jenkins程序。


因为123456 加密之后是: `#jbcrypt:$2a$10$MiIVR0rr/UhQBqT.bBq0QehTiQVqgNpUGyWW2nJObaVAM/2xSQdSq`，所以先将 admin的密码改为`123456`

再以用户名`admin` 密码`123456` 成功登录。

登录成功之后，再在`系统管理`-> `管理用户` -> `用户列表` 最后面一列的 设置图标进行修改用户信息

##### References
1. [Jenkins 忘记admin密码拯救方法](https://www.cnblogs.com/huangenai/p/7416322.html)

