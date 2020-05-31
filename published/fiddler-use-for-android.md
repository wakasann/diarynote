使用fiddler 软件监听手机app的http请求

## 前言
系统: Windows

通过 [fiddler官网](https://www.telerik.com/download/fiddler)或者其它途径获取 fiddler 安装包，然后安装好 fiddler

我使用的是 Fiddler5 绿色版的

## 操作步骤

1. fiddler导出ca证书

操作路径：
`Tools` -> `Fiddler Options` ->`HTTPS`(`工具` -> `选项` ->`HTTPS`),然后点击 `Action`(动作)按钮，选择`Export Fiddler Root Certificate to Desktop`(导出根证书到桌面)

如下图所示：

![uploads/200211/20200211221855.png](https://s2.ax1x.com/2020/02/11/17C4LF.png)

2. 设置 连接
操作路径：

`Tools` -> `Fiddler Options` ->`Connection`

`工具` -> `选项` ->`连接`


如下图所示：

![uploads/200211/20200211223308.png](https://s2.ax1x.com/2020/02/11/17CfMT.png)

设置完成之后，重启 fiddler软件，到此fiddler 的设置已完成。

3. 将导出的证书放入到手机中

如 手机安装一个`FTP 服务器`app,开启ftp服务，然后通过电脑的 ftp 客户端连接ftp 上传 导出的证书。

小米手机安装证书步骤: `设置` -> `WLAN` -> `高级设置` -> `安装证书`，会弹出一个窗口，`证书名称`自定义,`凭据用途` 选择`VPN和应用`，点击`确定`按钮即可 安装证书成功。

4. 设置手机 WLAN的代理

    4.1 小米2s手机
    `设置` -> `WLAN`,点击当前手机已连接的wifi的 右箭头进入到wifi设置页面；
    4.2 SONY XZP手机(Android 9)
    `设置` -> `WLAN`,点击已连接的wifi名称后面的 设定图标进入wifi设置页面，点击 页面右上角的笔图标进行编辑，在弹出的窗口内容是有滚动条的，滚动到下面或收起键盘就可以看到 代理的设置部分
    4.3 荣耀play
    `设置` -> `WLAN`,长按已连接的wifi名称，选择弹出的菜单中的`修改网络`, 勾选`显示高级选项`，即可看到 代理的设置部分

 `代理` 选择`手动`
 `主机名`输入运行fiddler的电脑ip地址(通过cmd命令`ipconfig`进行查看)
 `端口`输入第2步设置的`Fiddler 监听端口`的端口值

 ![uploads/200211/20200211-224840.png](https://s2.ax1x.com/2020/02/11/17ChsU.png)

5. 操作手机一些需要请求网络的app，电脑 fiddler 就会显示手机发送的http请求。



## References

1. [fiddler对安卓APP进行抓包](https://www.cnblogs.com/king8/p/9016007.html)
2. [Fiddler对安卓移动端的抓包测试](https://www.jianshu.com/p/6858a25674b4)

