windows 安装 jenkins笔记

Jenkins 所有镜像列表： [http://mirrors.jenkins-ci.org/status.html](http://mirrors.jenkins-ci.org/status.html) 可在镜像网站上下载安装文件，比官方下载快些

jenkins 官网地址:  [https://jenkins.io/](https://jenkins.io/) 

## windows 安装  jenkins

## 安装方式: 

### war方式

>war 方式， 启动Jenkins命令,打开cmd 进入到 Jenkins war 目录下，运行命令

```
java -jar jenkins.war
```

> 如果改变默认端口，则指定端口例如端口号9080：

```
java -jar jenkins.war --httpPort=9080
```
> 此种安装方式的Jenkins可以在Web容器（例如tomcat）中运行，作为持续性集成环境不推荐此种方法。 

###  msi 安装包安装(本次使用此种方式)

安装完成，windows系统服务中会自动注册jenkins服务并启动Jenkins；

![images/191206/fs2019120600003e.jpg](https://s2.ax1x.com/2019/12/13/QgLt2j.jpg)

我安装时，会打开浏览器访问 [http://localhost:8080](http://localhost:8080)

#### 修改端口号

如果出现端口(8080)冲突或者修改默认的端口(如果不需要修改端口，可略过该小点)，可以编辑安装目录下的`jenkins.xml` 文件，找到 `arguments` 这样，如下:

```
  <executable>%BASE%\jre\bin\java</executable>
  <arguments>-Xrs -Xmx256m -Dhudson.lifecycle=hudson.lifecycle.WindowsServiceLifecycle -jar "%BASE%\jenkins.war" --httpPort=8080 --webroot="%BASE%\war"</arguments>
```

修改 `-httpPort=`的http端口号，修改完成后，重启 Jenkins服务即可； 
如将 `--httpPort=8080` 改为 `--httpPort=8083`，重启 Jenkins后，通过 [http://localhost:8081](http://localhost:8083) 进行访问。


可通过命令进行重启:

```
net stop jenkins # 先停止 jenkins
net start jenkins # 在启动 jenkins
```

或者在服务管理器进行重启 jenkins 服务:

![images/191206/fs2019120600005z.jpg](https://s2.ax1x.com/2019/12/13/QgO9Jg.jpg)


#### Jenkins 初始化

以上面修改后的端口进行演示

1. 浏览器访问 [http://localhost:8083](http://localhost:8083) ，会出现如下界面，找到密码文件输入密码点击继续

![images/191206/fs2019120600006A.jpg](https://s2.ax1x.com/2019/12/13/QgOZwV.jpg)

2. 进入新手入门, 推荐 选择 `安装推荐的插件`，说明:`选择插件来安装` 也是默认选中了`安装推荐的插件`，是可以手动选择需要安装的插件。

![images/191206/fs20191206000070.jpg](https://s2.ax1x.com/2019/12/13/QgOuYF.jpg)

![images/191206/fs2019120600008X.jpg](https://s2.ax1x.com/2019/12/13/QgOALq.jpg)

3. 等待插件安装完成，在`创建第一个管理员用户`页面设定好 管理员的用户名和密码之后，会进入到本地 Jenkins软件的首页

![images/191206/fs2019120600010i.jpg](https://s2.ax1x.com/2019/12/13/QgOVe0.jpg)



## Jenkins 镜像

> 可供选择的jenkins2 插件镜像列表：

> Jenkins 所有镜像列表： http://mirrors.jenkins-ci.org/status.html
> 比如日本的镜像： http://mirror.esuni.jp/jenkins/, http://ftp.yz.yamagata-u.ac.jp/pub/misc/jenkins/， http://ftp.tsukuba.wide.ad.jp/software/jenkins/
> 德国的镜像：http://jenkins.mirror.isppower.de/ ， http://mirror.yandex.ru/mirrors/jenkins/
> 官方的镜像：http://mirrors.jenkins-ci.org/， http://archives.jenkins-ci.org/
> 清华大学镜像：https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json


### 更换地址方法

1. 进入jenkins系统管理(Manage Jenkins)
2.进入插件管理

![images/191206/fs2019120600011a.jpg](https://s2.ax1x.com/2019/12/13/QgOeoT.jpg)

![images/191206/fs2019120600012Z.jpg](https://s2.ax1x.com/2019/12/13/QgOkyn.jpg)

地址我输入的是: [https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json](https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json)

![images/191206/fs2019120600013Z.jpg](https://s2.ax1x.com/2019/12/13/QgOnFU.jpg)

## References
* [windows上Jenkins安装及其配置](https://www.cnblogs.com/fuxuepan/articles/9525623.html) windows 安装的参考博文
* [Jenkins系列-Jenkins插件下载镜像加速](https://www.cnblogs.com/zhuochong/p/10082498.html) 了解到 jenkins的镜像站和安装插件的镜像
* [使用Windows命令行启动关闭服务(net,sc用法)](https://www.cnblogs.com/qlqwjy/p/8010598.html) windows `net` 命令许久不用，既然忘了，惭愧 