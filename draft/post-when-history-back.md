---
title:  使用history.back()返回此网页需要使用您之前输入的数据才能正常显示
date: 2019-08-22 20:06:28
tags:
  - Codeigniter
categories:
  - Codeigniter
description: 使用history.back()返回此网页需要使用您之前输入的数据才能正常显示
---

在 接收表单 post 提交的方法里面，使用`history.back();`返回, 会出现`此网页需要使用您之前输入的数据才能正常显示。您可以重新发送这些数据，不过，这么做会重复执行此网页之前执行过的所有操作。`

参考 [使用history.back()返回此网页需要使用您之前输入的数据才能正常显示](https://www.cnblogs.com/wangzhaobo/p/11023297.html) 文章

引用:

> 原因：
>　表单提交页面中使用了 session_start 函数。由于我们后退浏览的是缓存页,而该函数会强制当前页面不被缓存。
>　解决：
php:
　　此提示出现在一个POST提交的页面，点到其它页面后，通过JavaScript再返回时，IE就会显示这个提示。以前也有碰到过好多次，不过都没太在意，百度一下原来是session在作怪。
在通过IE的后退按钮或者网页中的history.go(-1)时，我们看到的将是缓存页，但使用了session_start后，这个函数会强制当前页面不被缓存，导致出现“警告: 网页已经过期”。
　　解决方法一：
在session_start之后加一句header("Cache-control: private"); 注意在此之前程序不能有任何输出。
注意在本行之前你的PHP程序不能有任何输出和空白


##### References
1. [使用history.back()出现"警告： 网页已过期的解决办法"](https://www.cnblogs.com/yangjing1314/p/4698838.html)
2. [php 表单提交后 history.back() 内容不消失](https://www.cnblogs.com/linny/articles/2192839.html)