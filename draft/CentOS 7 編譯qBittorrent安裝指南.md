# CentOS 7 編譯qBittorrent安裝指南

## 前言

以下是安装qBittorrent教学。
适用于CentOS7或更新版本
适用于qBittorrent4.1.5或更新版本



## 安裝須知

> qBittorrent基于libtorrent，所以必须先安装libtorrent。但是需要说明的是，我安装时,libtorrent最新的版本为`1.2.1 `,但是此版本qb暂未支持。
> 我们选择安装`1.0.11`或者`1.1.12`，相对而言1.0.11更加稳定，但1.1.12性能更为强劲，因为其支持了异步i/o.本文就以1.1.12为例，演示安装过程。



------

> https://gcc.gnu.org/
> GNU編譯器(GCC)是一套程式語言編譯器，需要用於編譯qBittorrent的源碼。
> qBittorrent4.1.4需要C++14進行編譯
> CentOS7自帶的GCC4.8.5只支援到C++11，所以稍後會透過SCL安裝GCC8.2 (支援C++14)



## 安裝libtorrent

先安裝依賴包：

```
yum groupinstall "Development Tools"
yum install qt-devel boost-devel openssl-devel qt5-qtbase-devel qt5-linguist
```

> https://github.com/arvidn/libtorrent/releases  查看更新的版本来替换安装的版本

这里以安装的 `1.1.12` 为例

```
wget https://github.com/arvidn/libtorrent/releases/download/libtorrent_1_1_12/libtorrent-rasterbar-1.1.12.tar.gz
tar xf libtorrent-rasterbar-1.1.12.tar.gz
cd libtorrent-rasterbar-1.1.12
./configure --disable-debug --prefix=/usr CXXFLAGS=-std=c++11
make -j$(nproc)
make install
ln -s /usr/lib/pkgconfig/libtorrent-rasterbar.pc /usr/lib64/pkgconfig/libtorrent-rasterbar.pc
ln -s /usr/lib/libtorrent-rasterbar.so.9 /usr/lib64/libtorrent-rasterbar.so.9
```

## 安装qBittorrent

> 由于qBittorrent4.1.4+ 需要C++14进行编译，而centos7.x 默认自带的gcc版本为4.8.5只支持c++11，因此我们需要更新gcc版本，为了尽量保护系统环境不被破坏，我们选择适用scl安装高版本gcc。

通过scl构建gcc8.2环境

```
yum install centos-release-scl  
yum install devtoolset-8-gcc*
scl enable devtoolset-8 bash
```

[https://github.com/qbittorrent/qBittorrent/releases](https://github.com/qbittorrent/qBittorrent/releases)  可查看更新的版本来替换安装的版本

这里以 安装 qBittorrent `4.1.5` 为例

```
cd ~ 
wget https://github.com/qbittorrent/qBittorrent/archive/release-4.1.5.tar.gz
tar xf release-4.1.5.tar.gz
cd qBittorrent-release-4.1.5
./configure --prefix=/usr --disable-gui CPPFLAGS=-I/usr/include/qt5
make -j$(nproc)
make install
```

## 退出SCL的GCC環境

```
exit
```

## 设置开机启动

```
vi /etc/systemd/system/qbittorrent.service
```

输入以下内容

```
[Unit]
Description=qBittorrent Daemon Service
After=network.target

[Service]
LimitNOFILE=512000
User=root
ExecStart=/usr/bin/qbittorrent-nox
ExecStop=/usr/bin/killall -w qbittorrent-nox

[Install]
WantedBy=multi-user.target
```

保存后退出编辑

载入配置启用以上設置

```
systemctl enable qbittorrent.service
```

## 初次运行设置

安装完成后请先启动一次qb。

```
qbittorrent-nox
```

初次启动需要同意其适用协议，按照其提示输入`y`回车即可。

使用`ctrl+c`退出

使用systemctl 让其后台运行 `systemctl start qbittorrent.service`

## 开始使用

访问[http://ip:8080](http://ip:8080) 进入webui，如果无法访问可能是防火墙问题，将在下一节说明。

```
默认用户名：admin
默认密码：adminadmin
```

> 修改webui密码：
> 头部菜单:  工具 -->  选项 --> Web 用户界面 --> 验证
> 工具栏:  选项(最后一个转轮图标) --> Web 用户界面 --> 验证

systemctl 相关控制命令:

```
systemctl start qbittorrent.service #开启qb
systemctl stop qbittorrent.service #关闭qb
systemctl restart qbittorrent.service #重启qb
```

## 防火墙设置

centos7 使用firewall防火墙

放行webui端口

```
firewall-cmd --permanent --add-port=8080/tcp
```

放行qb默认监听端口（8999）自行修改后请同步放行防火墙。

```
firewall-cmd --permanent --add-port=8999/tcp
```

启用配置

```
firewall-cmd --reload
```

## 创建下载文件夹和设置权限

```
mkdir -p /home/Downloads
chmod 777 /home/Downloads
```

把下载路径设置到 `/home/Downloads`就OK了！


##### References
1. [CentOS 7 編譯qBittorrent安裝指南](https://npchk.info/centos7-qbittorrent/)
2. [centos7编译安装qBittorrent](https://www.fingertc.com/archives/287/)
3. [Centos7编译安装qbittorrent4.1.2](https://www.haiyun.me/archives/1239.html)