我的浏览器标签同步方案：坚果云+Floccus



## 前言

floccus github地址: [https://github.com/marcelklehr/floccus](https://github.com/marcelklehr/floccus) 

>  Floccus插件是一款浏览器书签收藏同步插件，支持Chrome和Firefox浏览器，用户可以通过Nextcloud，WebDAV或本地文件快速同步书签，便捷浏览。 



XBEL 地址: [http://xbel.sourceforge.net/ ](http://xbel.sourceforge.net/ )

> The XML Bookmark Exchange Language, or XBEL, is an Internet "bookmarks" interchange format. It was originally designed by the [Python XML Special Interest Group](http://www.python.org/sigs/xml-sig/) on the group's mailing list. 

XBEL 文件可以通过文本编辑器进行编辑



我选择的是使用 坚果云 的 WebDAV  +  Floccus 方案;
Nextcloud 可自建私有云，因我自己没有服务器，故放弃。



## 浏览器安装相关扩展介绍



[Chrome扩展安装说明](https://github.com/marcelklehr/floccus#chrome)

~~离线安装的步骤~~:

Chrome 扩展我是用 floccus的[releases](https://github.com/marcelklehr/floccus/releases/) 下载 后缀未`crx`的 文件，然后在本地 通过 7-zip 直接解压

Chrome 扩展安装: 
1. 在地址栏输入 `chrome://extensions/` 或者 通过点击 Chrome 浏览器 右上角的 `|`(快捷键: Alt + F) 打开菜单, `更多工具` -> `扩展程序` ,进入 `扩展程序`页面
2. 确认右上角的`开发者模式` 是否开启，如果已开启，可看到 `加载已解压的扩展程序`的按钮
3. 点击`加载已解压的扩展程序`按钮，然后选择 刚才解压的 `crx`的文件夹，至此，扩展程序就会安装成功了

我遇到在chrome 离线安装的floccus的扩展，在输入webdav的信息出现 输入的值总是被还原，最后还是去 chrome商店安装了这个扩展。

firefox的扩展程序，可通过 [floccus firefox安装说明](https://github.com/marcelklehr/floccus#firefox)进行安装


## 坚果云的准备

1. 登录到 坚果云，在`我的文件` -> `同步文件夹`(是登录成功之后的首页)下新建了一个类型是`个人同步文件夹`，名字为`floccus`的文件夹，用于 专门存放  floccus 同步的书签
2. 通过`账户信息` -> `安全选项` ->`第三方应用管理`，创建一个WebDAV应用，获取密码



**bookmarks.xbel** 文件的内容

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE xbel PUBLIC "+//IDN python.org//DTD XML Bookmark Exchange Language 1.0//EN//XML" "http://pyxml.sourceforge.net/topics/dtds/xbel.dtd">
<xbel version="1.0">
</xbel>
```



然后上传一个bookmarks.xbel文件到刚才新建的文件夹里面，没有的点这里[下载](uploads/191028/bookmarks.xbel)。


## 设置Chrome 已安装的floccus扩展

点击刚才安装 chrome 的`floccus` 扩展的图标，选择 `WebDAV共享中的XBEL文件`

WebDAV的服务器信息就跟着坚果云给你的信息填写上去就可以了。

Bookmarks file path(书签地址)的值填写你刚才创建的`文件夹/bookmarks.xbel`，如:`floccus/bookmarks.xbel`



书签的目录直接选择**根目录**就可以了，如果新建的话他是在你现在浏览器书签基础上新建一个同步的书签文件夹。

![](D:\workspace\note\diarynote\draft\uploads\191028\fs2019102800003V.jpg)

![](D:\workspace\note\diarynote\draft\uploads\191028\fs2019102800004h.jpg)



配置好了点击一次同步，就会先把你浏览器上所有书签同步到坚果云。然后你去另外的浏览器上同步就会把坚果云的和你本地的合并，设置里可以设置重新导入，不过建议是新浏览器里直接清空书签了再进行同步。

------

因为遇到浏览器标签因为没有及时备份就直接还原了系统，虽然很久以前同步过 chrome的标签，但是后来新电脑虽然还是用chrome,标签还是会加，但是标签未同步到google上，导致自己个人电脑上的chrome标签丢失，心里感到有点遗憾。通过搜索引擎找 `浏览器书签同步` ，有专门介绍了`EverSync`方案，发现参考文章有提到 `flocus`，并支持webadv。

在 google chrome下，我个人是使用 Floccus 和 google 同步2种方法进行使用的。

我将 google chrome 同步回之前同步过的书签之后，为了避免书签混乱，我将 放在 坚果云的 `bookmarks.xbel` 文件重命令为`bookmarks2.xbel`,下一步在 坚果云的 同一个文件夹下上传一个`xbel`节点为空的`bookmarks.xbel`文件，最后进行 chrome 的 floccus 扩展将书签同步到坚果云。这样 google 同步和坚果云里面的`bookmarks.xbel` 的书签列表是一样的了。



## References

1. [不同浏览器书签同步方案：坚果云+Floccus](https://blog.naibabiji.com/archives/601)
2. [The XML Bookmark Exchange Language (XBEL) Resource Page](http://xbel.sourceforge.net/)