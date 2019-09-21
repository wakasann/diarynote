signal server

## 前言

1. 安装好 mvn
2. Ubuntu 16.04

```
sudo apt-get update
# install java
sudo apt-get install default-jdk
#删除为了满足其他软件包的依赖而安装的，但现在不再需要的软件包。
sudo apt autoremove
sudo apt-get install default-jre
java –version
sudo apt install openjdk-8-jdk
#如果需要使用其它jdk版本，可用这个进行切换
sudo update-alternatives –-config java 
```

## install redis-server

```
sudo apt-get install -y redis-server
```

启动redis

```
sudo systemctl start redis-server
```

## install postgresql database

```
sudo apt-get install postgresql postgresql-contrib -y
```

Create postgres root user:

```
sudo -i -u postgres
createdb accountdb
createdb messagedb 

createuser --interactive
# Enter name of role to add: Signal
psql
ALTER USER "Signal" WITH PASSWORD 'Signal!!';
#quit
\q
```

让远程可以访问到 postgresql database的5432端口，

编辑`/etc/postgresql/10*/main/postgresql.conf` 文件

修改

```
listen_addresses='localhost'
```
改为
```
listen_addresses='*'
```

编辑`/etc/postgresql/10*/main/pg_hba.conf`

在文件末尾添加

```
host all all * md5
```

最后重启电脑或者重启你的DBMS

```
invoke-rc.d postgresql restart
```

## maven 安装

使用浏览器进入[http://maven.apache.org/download.cgi](http://maven.apache.org/download.cgi) 页面，
1. 在文件(Files)的列表中Link列，找到 类似`apache-maven-3.6.2-bin.zip`的链接
2. 右键，选择"复制链接地址",切换到Linux 终端 运行`wget http://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/3.6.2/binaries/apache-maven-3.6.2-bin.zip` 命令下载
3. 解压 `unzip apache-maven-3.6.2-bin.zip`
4. 移动文件

```
mkdir ~/env
cp -Rf apache-maven-3.6.2 ~/env/maven
cd ~/env/maven
# 输出当前完整的路径
wa@wa-VirtualBox:~/env/maven$ pwd
/home/wa/env/maven
```
5. 设置环境变量 ,编辑 `/etc/profile`

```
sudo vim /etc/profile
```
在文件末尾添加

```
export MAVEN_HOME=/home/wa/env/maven
export PATH=${MAVEN_HOME}/bin:$PATH
```

重启电脑或者重新加载 `/etc/profile`的配置

```
source /etc/profile
```

```
wa@wa-VirtualBox:~/env/maven$ source --help
source: source 文件名 [参数]
    在当前 shell 中执行一个文件中的命令。
    
    在当前 shell 中读取并执行 FILENAME 文件中的命令。$PATH 变量中的
    条目被用于寻找包含 FILENAME 文件的目录。如果提供了任何的 ARGUMENTS
    参数，则它们将成为 FILENAME 文件执行时的位置参数。
    
    退出状态：
    返回 FILENAME 文件中最后一个命令的状态；如果 FILENAME 文件不可读则失败。
```

验证 maven 配置成功

```
wa@wa-VirtualBox:~/env/maven$ mvn -v
Apache Maven 3.6.2 (40f52333136460af0dc0d7232c0dc0bcf0d9e117; 2019-08-27T23:06:16+08:00)
Maven home: /home/wa/env/maven
Java version: 11.0.4, vendor: Ubuntu, runtime: /usr/lib/jvm/java-11-openjdk-amd64
Default locale: zh_CN, platform encoding: UTF-8
OS name: "linux", version: "4.15.0-64-generic", arch: "amd64", family: "unix"

```

设置 maven 阿里云镜像

编辑 maven 安装目录下的`conf/settings.xml`（或者当前用户目录 的 .m2 目录下的 setting.xml 文件）

在 `mirrors`节点中 添加

```
<mirror>
    <id>alimaven</id>
    <name>aliyun maven</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
    <mirrorOf>central</mirrorOf>
</mirror>
```

-----

上面的准备的运行条件已准备好

这个主服务器的源码可以在[https://github.com/signalapp/Signal-Server](https://github.com/signalapp/Signal-Server) 找到

开始之前，先将源码拷贝到本地

```
git clone https://github.com/signalapp/Signal-Server
```

进入项目文件夹:

```
cd Signal-server
```

编译主要的服务器jar

```
mvn install -DskipTests	
```

运行最后会出现:

```
[INFO] Reading assembly descriptor: assembly.xml
[INFO] Building tar: /home/wa/workspace/java/Signal-Server/service/target/TextSecureServer-2.55-bin.tar.gz
[INFO] 
[INFO] --- maven-install-plugin:2.4:install (default-install) @ service ---
[INFO] Installing /home/wa/workspace/java/Signal-Server/service/target/TextSecureServer-2.55.jar to /home/wa/.m2/repository/org/whispersystems/textsecure/service/2.55/service-2.55.jar
[INFO] Installing /home/wa/workspace/java/Signal-Server/service/dependency-reduced-pom.xml to /home/wa/.m2/repository/org/whispersystems/textsecure/service/2.55/service-2.55.pom
[INFO] Installing /home/wa/workspace/java/Signal-Server/service/target/TextSecureServer-2.55-bin.tar.gz to /home/wa/.m2/repository/org/whispersystems/textsecure/service/2.55/service-2.55-bin.tar.gz
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary:
[INFO] 
[INFO] TextSecureServer 1.0 ............................... SUCCESS [ 17.345 s]
[INFO] redis-dispatch 2.55 ................................ SUCCESS [03:25 min]
[INFO] websocket-resources 2.55 ........................... SUCCESS [  9.466 s]
[INFO] service 2.55 ....................................... SUCCESS [02:16 min]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  06:09 min
[INFO] Finished at: 2019-09-21T12:10:24+08:00
[INFO] ------------------------------------------------------------------------

```

---

minio

```
sudo docker pull minio/minio

sudo docker run -p 9000:9000 --name minio1 \
  -e "MINIO_ACCESS_KEY=AKIAIOSFODNN7EXAMPLE" \
  -e "MINIO_SECRET_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY" \
  -v /mnt/data:/data \
  minio/minio server /data/minio
```

```
java -jar ../jars/Signal-2.1.jar  messagedb migrate  ../config/Signal.yml
java -jar ../jars/Signal-2.1.jar  accountdb migrate  ../config/Signal.yml
```


###### References

1. [Signal Server Installation Guide](https://gist.github.com/aqnouch/9a371af0614f4fe706a951c2b97651e7)
2. [Oracle-Java8-Installer：没有安装候选者](http://www.kbase101.com/question/2587.html)
3. [Ubuntu16.04 使用PPA安装JDK8](https://www.cnblogs.com/iban/p/5540854.html)
4. [Oracle-Java8-Installer: No installation candidate](https://askubuntu.com/questions/790671/oracle-java8-installer-no-installation-candidate#comment1955081_790674)
5. [How to Install JAVA 8 on Ubuntu 18.04/16.04, Linux Mint 19/18](https://tecadmin.net/install-oracle-java-8-ubuntu-via-ppa/comment-page-15/#comment-41215)
6. [apt-get指令的autoclean,clean,autoremove的区别](https://blog.csdn.net/flydream0/article/details/8620396)
7. [maven更改镜像路径为阿里镜像，以便下载速度快](https://www.cnblogs.com/panxuejun/p/6140768.html)
8. [SeksunNiampan/docker-Signal-Server](https://github.com/SeksunNiampan/docker-Signal-Server) 发现MINIO这个服务
9. [MinIO Docker Quickstart Guide](https://docs.min.io/docs/minio-docker-quickstart-guide.html)