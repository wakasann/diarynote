使用 fiddler 和PHP程序将快手App中喜欢的短视频保存在电脑上

## 前言

运行平台: windows 10家庭版

php代码地址:
* github: [https://github.com/wakasann/gksvideo_in_pc](https://github.com/wakasann/gksvideo_in_pc) 
* gitee: [https://gitee.com/mrshenyuan/gksvideo_in_pc](https://gitee.com/mrshenyuan/gksvideo_in_pc)

b站视频: [av88853974](https://www.bilibili.com/video/av88853974)

项目开始原因:  因为烦使用快手App 自带的下载按钮下载到的视频，在视频的末尾会有快手官方的一小段视频(台词是:快手看见每一种生活)


必须:

1. 电脑: 已安装 
    *  Fiddler 软件
    * WAMP(WIndows下安装Apache,PHP,Mysql软件,我为了省事，使用的是最新版的 phpstudy)
    * [git for windows](https://git-scm.com/downloads)
2. 安卓手机连接的WLAN 的代理已配置好 Fiddler的连接信息

如未配置好Fiddler和安卓手机的代理，可参考我之前的博文 [使用fiddler 软件监听手机app的http请求](https://www.cnblogs.com/fsong/p/12297387.html)，这里跳过 介绍phpstudy的安装、Fiddler 配置、安卓手机设置WLAN代理。

构思:

![uploads/200212/think001.png](https://s2.ax1x.com/2020/02/14/1OHPpT.png)


Fiddler和 php 项目之间的配合

通过Fiddler 查看一些http请求返回结果，在快手 App 首页的几个标签的内容或者个人主页，会请求一些 api，会返回短视频列表显示需要的封面图和作者，视频地址(在App 内看是无水印的视频)

1. 快手App 刷新列表(快手App首页3个tab，个人资料) 在进行刷新时，Fiddler script 匹配到对应的 http 请求的返回结果， 通过http 方式访问 php 的`welcome/myfollows` 链接
2. 快手 App 在分享 单个视频或者个人资料的时，请求的 url的参数有 视频id或者用户id,Fiddler script 匹配到对应的http url 部分后，提前请求的参数  通过http 方式访问  php的`welcome/sshare`或者`welcome/profileshare` 连接

## 步骤

项目中的文件特意说明:
1. `gifshow.sql` 使用到的数据库文件
2. `CustomRules.js` 我使用的Fiddler 自定义规则的文件

1. 进入到 web的根目录下，克隆项目

```
# cd D:
# cd phpstudy_pro/WWW
git clone https://github.com/wakasann/gksvideo_in_pc
```

2. 通过 phpmyadmin 工具将已克隆下的项目根目录下的`gifshow.sql` 导入到数据库中 并 修改好 `application/config/database.php` 中 数据库连接信息
3. 打开Fiddler 软件，将`CustomRules.js` 中的`OnBeforeRequest`和`OnBeforeResponse` 两个方法中，从`//关于快手部分的代码部分开始`开始到`//关于快手部分的代码部分结束`结束部分的代码复制到 Fiddler script 对应的方法内



## 快手App 内的操作

WLAN设置完代理之后，预先刷新 首页或者个人资料页
1. 下载单个视频/图片:  进入单个视频/图片详细页面，然后点击 `分享` -> `复制连接`，Fiddler Script 就会调用 php 项目进行匹配到视频id进行下载文件到电脑本机中
2. 下载一个用户的所有作品: 进入被下载用户的个人资料页面，手动刷新到底部,目的是用户的所有作品的信息可以通过php 程序记录作品信息在 mysql中，刷新完成之后，点击 `分享` ->`复制链接`,Fiddler Script 就会手动调用 php项目进行下载这个用户id 记录在数据库的所有作品了在电脑本地中


## 其它废话

因之前看了一些博文，了解 快手apk里面有一个`sig`参数，通过`jadx` 查看 apk 研究一段时间之后，一直没有结果，并放弃研究 apk代码了。

之前通过 fiddler 监听 快手app的请求，发现一些请求是有返回 视频的无水印的URL的，并且fiddler script 支持编写对一些匹配的uri地址进行一些获取响应结果和请求参数的api，所以弄了个曲线的方式获取 无水印视频。