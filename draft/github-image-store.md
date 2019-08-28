使用Github 当作自己个人博客的图床

## 前提

本文前提:

1. 我个人博客的草稿是存放在 github上的一个仓库 [diarynote](https://github.com/wakasann/diarynote)
2. 截图存放的图片或者需要放在文章中图片，会固定存放在对应的文件夹中，我个人是使用日期文件夹，如: `uploads/190828/test.jpg`
3. 本文中提到的`这个仓库`，都是指我自己的 [diarynote](https://github.com/wakasann/diarynote) 仓库



## 使用GitHub作为图床



从上面的描述可以知道，我自己的一个项目仓库的一个文件夹在 GitHub的 的路径固定是 [https://github.com/wakasann/diarynote/tree/master/draft/](https://github.com/wakasann/diarynote/tree/master/draft/)

当我使用`Typora`工具在本地编写当前仓库的Markdown 图片路径一般都会写成

`![](uploads/190828/test.jpg)`

编辑的时候，可以边预览边编辑，因为编辑时，查看的是本地的图片，访问速度快，也好替换。



一般编辑完之后，我自己会这个仓库的改动推送到 GitHub上。



当自己准备发布当前编辑的Markdown 文件中,并且该文件中图片时，可以通过喜欢的文本编辑工具，如:sublime text 

查找

```
uploads
```



替换为

```
https://raw.githubusercontent.com/wakasann/diarynote/master/draft/uploads
```

------

## 废话1

自从有了博客的草稿仓库之后，我遇到发布的烦恼是:

1. 将草稿发布到我自己 Hexo 源码项目中时，需要将图片复制一遍到 `source/uploads`，然后在草稿的图片链接面前加一个`/`就可以了，这个工作量对我来说还行
2. 将草稿发布到 博客园时，那文章中的使用到的图片就需要通过博客园的图片上传进行上传，这个发操作对比上一点，上传的工作量就有点大了



慢慢的，自己通过逛`v2ex` 社区，发现了一个 [sm.ms](https://sm.ms/)的网站，，然后自己也注册了，体验过，速度还是可以的，对写 Markdown文档也有很大的帮助，后来了解到`图床`是指 专门存放图片的服务器。

我主要是从维护自己个人博客图片路径的麻烦程度来考虑使用哪种图床来进行选择。

1. 使用非github图床时,如原博文中， markdown 图片路径是: `![](uploads/20190814/20190812173836.png)` ，在 `Typora` 和 在我自己基于Hexo的博客中，都可以直接显示图片。

通过`sm.ms` 上传的图片，得到 `![20190812173836.png](https://i.loli.net/2019/08/14/sLlFKmqwAXvaGfO.png)`，替换之后，在 `Typora` 和 在我自己基于Hexo的博客中，也都可以直接显示图片。

自己担心以后免费的图床以后不存在了，那博客中的所有图片也就看不到了，要替换自己草稿仓库中已存放图片路径，工作量会比较大，故 最后选择 自己存放博客草稿的仓库服务器 Github 作为自己的图床。



2. 使用github图床时,如: 如原博文中， markdown 图片路径是: `![](uploads/20190814/20190812173836.png)` ，在 `Typora` 和 在我自己基于Hexo的博客中，都可以直接显示图片。

通过查看这个仓库的图片路径，发现在`dfaft`文件夹的的所有图片路径相同开头的路径是[https://raw.githubusercontent.com/wakasann/diarynote/master/draft/](https://raw.githubusercontent.com/wakasann/diarynote/master/draft/)，我只需要在 已有的Markdown 文件中 图片链接 前面加 `https://raw.githubusercontent.com/wakasann/diarynote/master/draft/` 即可，以后github的图床不能使用时，我可以通过我自己喜欢的  sublime text 文本编辑工具，将这个仓库的所有Markdown 源文件的 `https://raw.githubusercontent.com/wakasann/diarynote/master/draft/` 替换为空，然后就可以重新发布到基于Hexo的个人博客网站上了，但发布到非基于hexo 的其它平台时，自己还是无法避免要和自己上面提到的烦恼2 一样，一张一张的上传了。

------




## 废话2



我的个人博客目前是使用 hexo 进行搭建,存放的主机的1个月流量少，如下图:

![](https://raw.githubusercontent.com/wakasann/diarynote/master/draft/uploads/190828/20190828165503.jpg)

发布在 博客园自己的博文的流量也是少的，所以没有 达到滥用的程度。

得到以上的结论之后，我自己的心里也好过一点了，嘿嘿嘿 :smile:



从下面参考的文章，自己明白

使用GitHub作为图床的缺点

1. 当提交图片上传的GitHub的仓库时，会给自己的Github帐号 添加无效的提交绿点

而使用GitHub作为图床，对我来说的优点是：

1. 仓库中的图片路径 维护起来比较方便, 将同一个随笔发布到其它平台时，发布的速度会比较快



通过帖子 [你们都用什么图床啊？](https://www.v2ex.com/t/489390)，看回复，自己了解到的是:

1. 使用 阿里云OSS 或者腾讯云OSS  加开源的 [PicGo](https://github.com/Molunerfinn/PicGo) 来自己搭建图床
2. 在自己服务器上使用开源的 [imgurl](https://github.com/helloxz/imgurl) 来搭建图床
3. 使用Gitee 做图床
4. 使用网上已有的免费图床 [sm.ms](https://sm.ms/) ,[imgchr](https://imgchr.com/)




###### References

1. [厘清把 github 当图床的思路](https://www.cnblogs.com/qwangxiao/p/9191508.html)
2. [Github做图床](https://www.jianshu.com/p/d51258ef5484)
3. [github做Markdown图床](https://www.jianshu.com/p/33eeacac3344)
4. [图床工具的使用---PicGo](https://www.jianshu.com/p/9d91355e8418)