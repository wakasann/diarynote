Xshell出现要继续使用此程序必须应用到最新的更新或使用新版本

我使用的是 [免费 SSH 客户端 - Xshell 5 Build 1339 中文绿色便携版](https://www.portablesoft.org/xshell/) 的绿色版，今天启动使用的时候，提示`要继续使用此程序必须应用到最新的更新或使用新版本`

写这篇blog的时候，再去看下载链接页面，发现更新日志里，2019-01-06 已经通过修改 nslicense.dll 文件，修复 Xshell 出现错误提示`要继续使用此程序必须应用到最新的更新或使用新版本`

------

通过下面的参考链接文章，我学习到以下2个方案

1. 修改电脑的 host

在 host文件添加以下规则，将Xshell的升级请求的几个地址都屏蔽了

```
127.0.0.1 transact.netsarang.com
127.0.0.1 update.netsarang.com
127.0.0.1 www.netsarang.com
127.0.0.1 www.netsarang.co.kr
127.0.0.1 sales.netsarang.com
```
2. 下载 参考文章 1 的`nslicense.dll`

参考文章1的百度网盘分享链接：[https://pan.baidu.com/s/1Bfxe5_j9yfNbDJmjiRWDgA ](https://pan.baidu.com/s/1Bfxe5_j9yfNbDJmjiRWDgA )
提取码：tv5u

我转存参考文章1的百度网盘分享链接: [https://pan.baidu.com/s/1734vTfIEkKIlHkAU1LFAMQ](https://pan.baidu.com/s/1734vTfIEkKIlHkAU1LFAMQ) 
提取码: amv2 


将 `nslicense.dll` 文件下载下来，覆盖 xshell 安装目录的 `App\Xshell`下的`nslicense.dll`文件

覆盖前，可先将xshell文件下的nslicense.dll` 重命令为`nslicense.dll.bak`，然后再进行覆盖



##### References
1. [Xshell出现要继续使用此程序必须应用到最新的更新或使用新版本](https://www.cnblogs.com/chenxiaomeng/p/9435860.html)
2. [Xshell无法启动：要继续使用此程序，您必须应用最新的更新或使用新版本](https://51.ruyo.net/11404.html)
