在虚拟机中使用maven编译signal server项目记录

## 前言

1. 安装好 mvn
2. Ubuntu 16.04



## JDK

我是从 [oracle jdk 11 lts download](https://blog.forsre.com/)  网站，复制`jdk-11.0.4_linux-x64_bin.tar.gz`的链接


```
wget https://blog.forsre.com/java/jdk-11.0.4_linux-x64_bin.tar.gz
sudo mkdir /usr/lib/jvm
sudo tar zxvf ./jdk-11.0.4_linux-x64_bin.tar.gz  -C /usr/lib/jvm
```

配置环境变量

```
vim ~/.bashrc
```

添加

```
export JAVA_HOME=/usr/lib/jvm/java-11-oracle
export PATH=${JAVA_HOME}/bin:$PATH
```



```
source ~/.bashrc
java -version
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
ALTER USER "Signal" WITH PASSWORD 'thepassword';
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
sudo invoke-rc.d postgresql restart
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
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export MAVEN_HOME=/home/wa/env/maven
export M2_HOME=/home/wa/env/maven
export PATH=${JAVA_HOME}/bin:${M2_HOME}/bin:${PATH}
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

添加多一个文件`apache-maven.sh`，用于编译时使用

```
vim /etc/profile.d/apache-maven.sh
```

内容为:

```
export JAVA_HOME=/usr/lib/jvm/java-11-oracle
export MAVEN_HOME=/home/waka/env/maven
export M2_HOME=/home/waka/env/maven
export PATH=${JAVA_HOME}/bin:${M2_HOME}/bin:${PATH}
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
# mvn install -DskipTests
source /etc/profile.d/apache-maven.sh && mvn install -DskipTests
```

运行最后会出现:

```
[INFO] Replacing original artifact with shaded artifact.
[INFO] Replacing /home/waka/Signal-Server/service/target/TextSecureServer-2.55.jar with /home/waka/Signal-Server/service/target/service-2.55-shaded.jar
[INFO] 
[INFO] --- maven-assembly-plugin:2.4:single (make-assembly) @ service ---
[INFO] Reading assembly descriptor: assembly.xml
[INFO] Building tar: /home/waka/Signal-Server/service/target/TextSecureServer-2.55-bin.tar.gz
[INFO] 
[INFO] --- maven-install-plugin:2.4:install (default-install) @ service ---
[INFO] Installing /home/waka/Signal-Server/service/target/TextSecureServer-2.55.jar to /home/waka/.m2/repository/org/whispersystems/textsecure/service/2.55/service-2.55.jar
[INFO] Installing /home/waka/Signal-Server/service/dependency-reduced-pom.xml to /home/waka/.m2/repository/org/whispersystems/textsecure/service/2.55/service-2.55.pom
[INFO] Installing /home/waka/Signal-Server/service/target/TextSecureServer-2.55-bin.tar.gz to /home/waka/.m2/repository/org/whispersystems/textsecure/service/2.55/service-2.55-bin.tar.gz
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary:
[INFO] 
[INFO] TextSecureServer 1.0 ............................... SUCCESS [  0.598 s]
[INFO] redis-dispatch 2.55 ................................ SUCCESS [  3.041 s]
[INFO] websocket-resources 2.55 ........................... SUCCESS [  0.370 s]
[INFO] service 2.55 ....................................... SUCCESS [ 56.760 s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  01:01 min
[INFO] Finished at: 2019-10-14T16:23:22+08:00
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

迁移数据:


```
java -jar service/target/TextSecureServer-2.55.jar  messagedb migrate service/config/Signal.yml
java -jar service/target/TextSecureServer-2.55.jar  accountdb migrate service/config/Signal.yml

java -jar websocket-resources/target/websocket-resources-2.55.jar
```



一直出现以下问题:

```
java.io.IOException: Bad Base64 input character '-' in array position 0
	at org.whispersystems.textsecuregcm.util.Base64.decode(Base64.java:1221)
	at org.whispersystems.textsecuregcm.util.Base64.decode(Base64.java:1287)
	at org.whispersystems.textsecuregcm.util.Base64.decode(Base64.java:1244)
	at org.whispersystems.textsecuregcm.util.Base64.decodeWithoutPadding(Base64.java:1255)
	at org.whispersystems.textsecuregcm.util.ByteArrayAdapter$Deserializing.deserialize(ByteArrayAdapter.java:46)
	at org.whispersystems.textsecuregcm.util.ByteArrayAdapter$Deserializing.deserialize(ByteArrayAdapter.java:41)
	at com.fasterxml.jackson.databind.deser.impl.FieldProperty.deserializeAndSet(FieldProperty.java:136)
	at com.fasterxml.jackson.databind.deser.BeanDeserializer.deserialize(BeanDeserializer.java:252)
	at com.fasterxml.jackson.module.afterburner.deser.SuperSonicBeanDeserializer.deserialize(SuperSonicBeanDeserializer.java:155)
	at com.fasterxml.jackson.databind.deser.impl.FieldProperty.deserializeAndSet(FieldProperty.java:136)
	at com.fasterxml.jackson.databind.deser.BeanDeserializer.deserialize(BeanDeserializer.java:252)
	at com.fasterxml.jackson.module.afterburner.deser.SuperSonicBeanDeserializer.deserialize(SuperSonicBeanDeserializer.java:155)
	at com.fasterxml.jackson.databind.ObjectMapper._readValue(ObjectMapper.java:3984)
	at com.fasterxml.jackson.databind.ObjectMapper.readValue(ObjectMapper.java:2276)
	at io.dropwizard.configuration.BaseConfigurationFactory.build(BaseConfigurationFactory.java:127)
	at io.dropwizard.configuration.BaseConfigurationFactory.build(BaseConfigurationFactory.java:89)
	at io.dropwizard.cli.ConfiguredCommand.parseConfiguration(ConfiguredCommand.java:126)
	at io.dropwizard.cli.ConfiguredCommand.run(ConfiguredCommand.java:74)
	at io.dropwizard.cli.Cli.run(Cli.java:78)
	at io.dropwizard.Application.run(Application.java:93)
	at org.whispersystems.textsecuregcm.WhisperServerService.main(WhisperServerService.java:296)

```





启动服务:

```
java -jar service/target/TextSecureServer-2.55.jar  server service/config/Signal.yml
```





生成证书

---

```
openssl genrsa -aes128 -out fd.key 2048
openssl rsa -in fd.key -pubout -out fd-public.key
```



我的`Signal.yml` 文件内容:

```
twilio: # Twilio gateway configuration
  accountId: ddd #fake
  accountToken: aaa #fake
  numbers:  # Numbers allocated in Twilio
    - "+121020222" #fake
  messagingServicesId: 
  localDomain:  # Domain Twilio can connect back to for calls. Should be domain of your service.

push:
  queueSize: 200 # Size of push pending queue

#redphone:
#  authKey: 12312321 # Deprecated

turn: # TURN server configuration
  secret: 121654fjdfgdyesdfgfhgh # TURN server secret
  uris:
    - stun:yourdomain:80
    - stun:yourdomain.com:443
    - turn:yourdomain:443?transport=udp
    - turn:etc.com:80?transport=udp

cache: # Redis server configuration for cache cluster
  url: "redis://127.0.0.1:6379/1"
  replicaUrls:
    - "redis://redit:6379/2"

directory:
  redis: # Redis server configuration for directory cluster
    url: "redis://redit:6379/3"
    replicaUrls:
      - "redis://redit:6379/4"
  client: # Configuration for interfacing with Contact Discovery Service cluster
    userAuthenticationTokenSharedSecret: test # hex-encoded secret shared with CDS used to generate auth tokens for Signal users
    userAuthenticationTokenUserIdSecret: test# hex-encoded secret shared among Signal-Servers to obscure user phone numbers from CDS
  sqs:
    accessKey: AKIAIOSFODNN7EXAMPLE    # AWS SQS accessKey
    accessSecret: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY  # AWS SQS accessSecret
    queueUrl:  sqs://127.0.0.1    # AWS SQS queue url
  server:
    replicationUrl: http://127.0.0.2               # CDS replication endpoint base url
    replicationPassword:  12321312         # CDS replication endpoint password
    replicationCaCertificate: 5656     # CDS replication endpoint TLS certificate trust root
    
messageCache: # Redis server configuration for message store cache
  redis:
    url: "redis://127.0.0.1:6379/5"
    replicaUrls:
      - "redis://127.0.0.1:6379/6"

messageStore: # Postgresql database configuration for message store
  driverClass: org.postgresql.Driver
  user: Signal
  password: Signal!!
  url: "jdbc:postgresql://127.0.0.1:5432/messagedb"

attachments: # AWS S3 configuration
  accessKey: AKIAIOSFODNN7EXAMPLE
  accessSecret: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
  bucket: signal
  region: us-east-1

profiles: # AWS S3 configuration
  accessKey: AKIAIOSFODNN7EXAMPLE
  accessSecret: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
  bucket: profile
  region: us-east-1

database: # Postgresql database configuration
  driverClass: org.postgresql.Driver
  user: Signal
  password: Signal!!
  url: "jdbc:postgresql://127.0.0.1:5432/accountdb"
  properties:
    charSet: UTF-8

apn: # Apple Push Notifications configuration
  bundleId: 1312312
  pushCertificate: 31231
  pushKey: 12321321

gcm: # GCM Configuration
  senderId: 412918270132 #fake
  apiKey: AIzaSyC8gPzceq2SPebZZWaD3I9OeqePyD9CUqk #fake

accountDatabaseCrawler:
  chunkSize: 100
  chunkIntervalMs: 8000

recaptcha:
  secret: 666666

pushScheduler:
  url: "redis://localhost:6379/7"
  replicaUrls: 
    - "redis://localhost:6379/8"
```





------

后续:

1. 了解到 twilio 好像不支持 给国内的手机发送SMS
2. apn,gcm 的配置弄不到

最后放弃尝试了，好像配置文件`Signal.yml`改动了，都需要运行一次`source /etc/profile.d/apache-maven.sh && mvn install -DskipTests`



---


redis

````
mvn clean install -DskipTests
````

websocket

```
mvn clean install -DskipTests
mvn install:install-file -Dfile=./target/websocket-resources-2.55.jar -DgroupId=org.whispersystems -DartifactId=websocket-resources -Dversion=1.0 -Dpackaging=jar
```


## 命令草稿

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
sudo update-alternatives --config java
```



在另外一台虚拟机Ubuntu 16.04,JDK 我切换到 oracle jdk11

```
# 卸载之前安装的jdk
sudo apt-get autoremove default-jdk
sudo apt-get autoremove default-jre
sudo apt-get autoremove openjdk-8-jdk

# 安装 jdk11
sudo add-apt-repository ppa:linuxuprising/java
sudo apt-get update
sudo apt-get install oracle-java11-installer-local #如果这一步遇到类似以下的问题，可以去其它网站下载响应的文件
sudo add-apt-repository  -r ppa:linuxuprising/java
```

我是从 [oracle jdk 11 lts download](https://blog.forsre.com/)  网站，复制`jdk-11.0.4_linux-x64_bin.tar.gz`的链接

通过 [ppa 网站](https://launchpad.net/~linuxuprising/+archive/ubuntu/java/+packages?field.name_filter=&field.status_filter=published&field.series_filter=xenial ) 发现安装 java11，我在 10月25日尝试，发现版本由 `java_11.0.4` 改为 `java_11.0.5`，故安装失败了，我是直接解压 jdk的下载包，然后将 JAVA,Maven 配置到环境变量镜像进行使用的



~~命令~~:

````
waka@ubuntus1:~/Signal-Server$ sudo apt-get remove oracle-java11-set-default-local
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Package 'oracle-java11-set-default-local' is not installed, so not removed
0 upgraded, 0 newly installed, 0 to remove and 133 not upgraded.
1 not fully installed or removed.
After this operation, 0 B of additional disk space will be used.
Setting up oracle-java11-installer-local (11.0.4-1~linuxuprising1) ...
Before installing this package,
please download the Oracle JDK 11 .tar.gz file
with the same version as this package (version 11.0.4),
and place it in /var/cache/oracle-jdk11-installer-local,

E.g.:
sudo mkdir -p /var/cache/oracle-jdk11-installer-local
sudo cp jdk-11.0.4_linux-x64_bin.tar.gz /var/cache/oracle-jdk11-installer-local/
sha256sum mismatch jdk-11.0.4_linux-x64_bin.tar.gz
Oracle JDK 11 is NOT installed.
dpkg: error processing package oracle-java11-installer-local (--configure):
 subprocess installed post-installation script returned error exit status 1
Errors were encountered while processing:
 oracle-java11-installer-local
E: Sub-process /usr/bin/dpkg returned an error code (1)
````

如何 `apt-get install` 一直提示上面的错误信息，可以通过下面的命令进行卸载安装的`oracle-java11-installer-local`

```
sudo apt-get auto-remove oracle-java11-installer-local
```







~~安装jdk 11 命令~~:

```
cd /tmp
wget https://blog.forsre.com/java/jdk-11.0.4_linux-x64_bin.tar.gz
sudo mkdir -p /var/cache/oracle-jdk11-installer-local
sudo cp jdk-11.0.4_linux-x64_bin.tar.gz /var/cache/oracle-jdk11-installer-local/
sudo apt-get install oracle-java11-installer-local #进行安装
```


## References

1. [Signal Server Installation Guide](https://gist.github.com/aqnouch/9a371af0614f4fe706a951c2b97651e7)

2. [Oracle-Java8-Installer：没有安装候选者](http://www.kbase101.com/question/2587.html)

3. [Ubuntu16.04 使用PPA安装JDK8](https://www.cnblogs.com/iban/p/5540854.html)

4. [Oracle-Java8-Installer: No installation candidate](https://askubuntu.com/questions/790671/oracle-java8-installer-no-installation-candidate#comment1955081_790674)

5. [How to Install JAVA 8 on Ubuntu 18.04/16.04, Linux Mint 19/18](https://tecadmin.net/install-oracle-java-8-ubuntu-via-ppa/comment-page-15/#comment-41215)

6. [apt-get指令的autoclean,clean,autoremove的区别](https://blog.csdn.net/flydream0/article/details/8620396)

7. [maven更改镜像路径为阿里镜像，以便下载速度快](https://www.cnblogs.com/panxuejun/p/6140768.html)

8. [SeksunNiampan/docker-Signal-Server](https://github.com/SeksunNiampan/docker-Signal-Server)  通过修改endpoint，可以使用MINIO这个服务

9. [MinIO Docker Quickstart Guide](https://docs.min.io/docs/minio-docker-quickstart-guide.html)

10. [oracle jdk 11 lts download](https://blog.forsre.com/)

11. [How to Install Oracle Java 11 in Ubuntu 18.04/18.10](http://ubuntuhandbook.org/index.php/2018/11/how-to-install-oracle-java-11-in-ubuntu-18-04-18-10/)

12. [https学习笔记三----OpenSSL生成root CA及签发证书](https://www.cnblogs.com/loleina/p/8418111.html)

    